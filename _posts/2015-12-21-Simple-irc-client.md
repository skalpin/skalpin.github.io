---
layout: post
title:  "Simple IRC Client"
date: 2015-12-21
categories: update
---

Since I've been using IRC more lately, I've been wanting to integrate
it with other services. Also since this is my first blog I wanted to
start with a project that would be easy to implement. In fact the api is so easy,
you could do all of this in a telnet session if you wanted to. So below are
examples of how you can communicate with an IRC server over telnet, and some methods to
get you going in C#. 

## Telnet

To get into the server all you need to do is open a console and type

<pre>
telnet irc.freenode.net 6667
NICK mynickname
USER foo bar bin :bash
JOIN #mytestchannel
PRIVMSG #mytestchannel :This is just a test
</pre>

And that's it!
You've just logged into an irc server, sent it a few commands to tell it what your nickname
should be and what channel you want to be in. Then you sent a message to the channel using PRIVMSG.
If you want to experiment with other commands you can type HELP into the telnet
session to learn more.

## C\#

The code below does the following.

1. Open a tcp client to the host on the selected port.
2. Send the users nickname.
3. Setup the user.
4. Join the specified channel.

Then the ReadFromStream method starts up a task to read bytes from the stream and convert them to a
readable string. It's important that this runs inside of a task so that you can receive messages while
another thread waits for your commands. 
The last while loop simply waits for the users input and sends the input to the specified channel
from before.

{% highlight csharp %}
async Task Connect()
{
	var host = "irc.freenode.net";
	var port = 6667;
	var nick = "n00bwerthanyou";
	var ident = "n00bwerthanyou";
	var realname = "realnamestuff";
	var channel = "#someotherchannel";
	
	var client = new TcpClient(host, port);
	var stream = client.GetStream();

	await WriteAsync(stream, $"NICK {nick}");
	await WriteAsync(stream, $"USER {ident} {host} bla :{realname}");
	await WriteAsync(stream, $"JOIN {channel}");

	var readTask = ReadFromStream(stream);
	
	while (true)
	{
		var line = Console.ReadLine();
		await WriteAsync(stream, $"PRIVMSG {channel} :{line}");
	}
}

public Task ReadFromStream(Stream stream)
{
	return Task.Run(async () =>
	{
		var buffer = new byte[1024];
		while (true)
		{
			var bytesRead = await stream.ReadAsync(buffer, 0, buffer.Length);
			Console.Write(Encoding.UTF8.GetString(buffer, 0, bytesRead));
		}
	});
}

public Task WriteAsync(Stream stream, string data)
{
	var bytes = Encoding.UTF8.GetBytes($"{data}{Environment.NewLine}");
	return stream.WriteAsync(bytes, 0, bytes.Length);
}
{% endhighlight %}
