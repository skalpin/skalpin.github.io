---
layout: post
title:  ".NET IRC Client"
categories: update
---

#irc Client
Since I've been using IRC more lately, I've been wanting to integrate
it with other services. Also since this is my first blog I wanted to
start with a project that would be easy to implement.

{% highlight cs %}
async Task Connect()
{
	var HOST = "irc.freenode.net";
	var PORT = 6667;
	var NICK = "n00bwerthanyou";
	var IDENT = "n00bwerthanyou";
	var REALNAME = "realnamestuff";
	
	var client = new TcpClient(HOST, PORT);
	var stream = client.GetStream();

	await WriteAsync(stream, $"NICK {NICK}\r\n");
	await WriteAsync(stream, $"USER {IDENT} {HOST} bla :{REALNAME}\r\n");

	while (true)
	{
		var buffer = new byte[1024];
		await stream.ReadAsync(buffer, 0, buffer.Length);
		var value = Encoding.UTF8.GetString(buffer);
		Console.Write(value);
	}
}

public Task WriteAsync(Stream stream, string data)
{
	var bytes = Encoding.UTF8.GetBytes(data);
	return stream.WriteAsync(bytes, 0, bytes.Length);
}
{% endhighlight %}
