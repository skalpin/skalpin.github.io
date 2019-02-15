---
layout: post
title:  "Import From Service to SQL"
date: 2019-2-15
categories: update
---

## Problem

why do we need to be talking about this?
1. SQL Server is much faster at loading records in batches.
2. What happens if we need to pull the data from another source? I don't want to block until the enumerable completes.
3. Migrating data from blob storage

I was recently tasked with moving a large data set of items from Azure blob storage into SQL Server. When I am faced with a migration problem I always look for a producer/consumer solution to keep the memory usage low. Often the dataset is too large to load into memory, and then dump to the destination all at once. I also heavily use IEnumerables because I can return the items that have been retrieved, while the producer goes back to retrieve more.

Here is example code of how to load items from blob storage. There is a query api, and it will return a set number of records, along with a continuation token if there are more records to retrieve.

``` cs
private IEnumerable<T> RetrieveEvents()
{
    foreach(var url in GetBlobUrls($"{containerRootName}/{blobFolderName}")
    {
        foreach (var blob in DownloadBlobs(url).Result)
        {
            yield return blob;
        }
    });
}

private IEnumerable<Uri> GetBlobUrls(string directory)
{
    // List the blobs in the container.
    BlobContinuationToken blobContinuationToken = null;
    do
    {
        var blobListingDetails = new BlobListingDetails();
        var results = GetBlobResultSegmentsAsync(directory, blobContinuationToken, blobListingDetails).Result;
        // Get the value of the continuation token returned by the listing call.
        blobContinuationToken = results.ContinuationToken;
        foreach (IListBlobItem item in results.Results)
        {
            yield return item.Uri;
        }
    } while (blobContinuationToken != null); // Loop while the continuation token is not null.
}

private async Task<string> DownloadBlobs(Uri blobUri, string containerName)
{
    var appinsightsContainer = await blobContainerFactory.GetBlobContainer(containerName);
    var cloudBlockBlob = new CloudBlockBlob(blobUri, appinsightsContainer.ServiceClient);
    using (var sr = new StreamReader(await cloudBlockBlob.OpenReadAsync()))
    {
        return sr.ReadToEnd();
    }
}

public class BlobContainerFactory
{
    private string blobConnectionString;

    public BlobContainerFactory(string blobConnectionString)
    {
        this.blobConnectionString = blobConnectionString;
    }

    public async Task<CloudBlobContainer> GetBlobContainer(string containerName)
    {
        CloudStorageAccount.TryParse(blobConnectionString, out var storageAccount);

        var cloudBlobClient = storageAccount.CreateCloudBlobClient();
        var appinsightsContainer = cloudBlobClient.GetContainerReference(containerName);
        if (!(await appinsightsContainer.ExistsAsync()))
        {
            await appinsightsContainer.CreateAsync();
        }

        return appinsightsContainer;
    }
}
```

Assuming you have a large number of JSON files, calling RetrieveEvents will iterate through the blob uri's in the storage account, download the data, and return it as an `IEnumerable<string>`. So far so good, now let's try and write those items to SQL.

SqlBulkCopy is easily the fastest way to move a large number of items to a SQL instance. You can pass it a large number of items, set the batch size, and let SqlBulkCopy batch and upload items for you.

```cs

var blobContainerFactory = new BlobContainerFactory("<yourconnectionstring>");

WriteAllToSql(RetrieveEvents());

public void WriteAllToSql(IEnumerable<T> allItems)
{
    using (SqlBulkCopy copy = new SqlBulkCopy(connectionString))
    {
        copy.DestinationTableName = tableName;
        copy.BatchSize = 1000;
        copy.EnableStreaming = true;
        foreach (var columnMapping in columnMappings)
        {
            copy.ColumnMappings.Add(columnMapping);
        }
        copy.WriteToServer(allItems.GetDataTable(tableName));
    }
}

public static class DataTableAdapter
{
    public static DataTable GetDataTable<T>(this IEnumerable<T> enumerable, string tableName)
    {
        var dataTable = new DataTable(tableName);
        var properties = typeof(T).GetProperties();
        foreach (var propertyInfo in properties)
        {
            dataTable.Columns.Add(propertyInfo.Name, Nullable.GetUnderlyingType(propertyInfo.PropertyType) ?? propertyInfo.PropertyType);
        }

        foreach (var item in enumerable)
        {
            var dataRow = dataTable.NewRow();
            foreach (var propertyInfo in properties)
            {
                dataRow[propertyInfo.Name] = propertyInfo.GetValue(item) ?? DBNull.Value;
            }

            dataTable.Rows.Add(dataRow);
        }

        return dataTable;
    }
}

```

In order to create a `DataTable` object however, each item in the original enumerable needs to be interated through for a row to be added. The issue is, all of the events need to get loaded into memory before _anything_ gets written to SQL.

## Solution

Instead of commiting the entire collection at once, let's add a non-blocking function that creates _chunks_ of data to be written to SQL. That way the blob storage reader can be waiting on a web request and the SQLBulkCopy can write what it currently has. This extension method will take single items, and create an IEnumerable<IEnumerable<T>> of those items. If there are items in the original collection -> gather them up, otherwise commit what has been gatherd and block until there are more items. This function can optionally take a maximum size if your destination cannot handle a large dataset.

```cs
public static class EnumerableExtensions
{
    public static IEnumerable<IEnumerable<T>> Batch<T>(this IEnumerable<T> enumerable, int? maximumSize = null)
    {
        var queue = new BlockingCollection<T>(new ConcurrentQueue<T>());

        var producingTask = Task.Run(() =>
        {
            foreach (var element in enumerable)
            {
                queue.Add(element);
            }

            queue.CompleteAdding();
        });

        while (!queue.IsCompleted)
        {
            var itemList = new List<T>();
            foreach (var item in queue.GetConsumingEnumerable())
            {
                itemList.Add(item);
                if ((!queue.Any() || (maximumSize.HasValue && itemList.Count >= maximumSize)) && itemList.Any())
                {
                    yield return itemList;
                    itemList = new List<T>();
                }
            }
        }
        
        if(producingTask.IsFaulted)
        {
            throw producingTask.Exception;
        }
    }
}
```

Using a blocking collection and a Task allows us to iterate through the original list, while still allowing items to be collected and returned. `GetConsumingEnumerable()` will block until more items have been added to the queue.

```cs
foreach(var items in RetrieveEvents().Batch())
{
    WriteAllToSql(items);
}
```

Now, if the calls to blob storage are being throttled, the items will be collected and written to SQL. Also while the first call to SQL is being made, items are still being collected and the initial batch size will increase. It should be noted that SQLBulkCopy will still batch writes to SQL using the batchSize set, so if the items size does grow to 1,000,000 records, SQLBulkCopy will take care of breaking it into chunks itself.