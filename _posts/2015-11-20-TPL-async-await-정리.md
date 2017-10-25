---
layout: post
title: 151120 TPL/async/await 정리
comments: true
tags:
- c#
- .net
- uwp
- study
- async/await
- TPL
---


<!-- TOC -->

- [비동기 메소드의 흐름](#비동기-메소드의-흐름)
- [비동기 작업의 취소](#비동기-작업의-취소)
- [다수의 비동기 작업 모두 완료되면 다수의 값을 배열로 리턴](#다수의-비동기-작업-모두-완료되면-다수의-값을-배열로-리턴)
- [다수의 비동기 작업중 한개가 완료되면 모두 취소하는 기능](#다수의-비동기-작업중-한개가-완료되면-모두-취소하는-기능)
- [다수의 비동기 작업중 한개가 완료될때마다 처리](#다수의-비동기-작업중-한개가-완료될때마다-처리)
- [async/await best practice](#asyncawait-best-practice)
- [C# http web server with TPL/async/await](#c-http-web-server-with-tplasyncawait)

<!-- /TOC -->

<br>
<br>
<br>

---

![Alt text](https://s26.postimg.org/gsdqqth7d/screenshot_76.png)

---

## 비동기 메소드의 흐름
https://msdn.microsoft.com/ko-kr/library/hh873191.aspx

```c#
public partial class MainWindow : Window
{
    // . . .
    private async void startButton_Click(object sender, RoutedEventArgs e)
    {
        // ONE
        Task<int> getLengthTask = AccessTheWebAsync();

        // FOUR
        int contentLength = await getLengthTask;

        // SIX
        resultsTextBox.Text +=
            String.Format("\r\nLength of the downloaded string: {0}.\r\n", contentLength);
    }


    async Task<int> AccessTheWebAsync()
    {
        // TWO
        HttpClient client = new HttpClient();
        Task<string> getStringTask =
            client.GetStringAsync("http://msdn.microsoft.com");

        // THREE                 
        string urlContents = await getStringTask;

        // FIVE
        return urlContents.Length;
    }
}
```

---

## 비동기 작업의 취소
https://msdn.microsoft.com/ko-kr/library/jj155759.aspx
`CancellationTokenSource`
`cts.Token`
`cts.Cancel();`

```c#
cts = new CancellationTokenSource();

HttpResponseMessage response = await client.GetAsync("http://msdn.microsoft.com/en-us/library/dd470362.aspx", cts.Token);

cts.Cancel();
```

---

## 다수의 비동기 작업 모두 완료되면 다수의 값을 배열로 리턴
https://msdn.microsoft.com/ko-kr/library/hh556530.aspx
`Task.WhenAll`

```c#
// Create a query.
IEnumerable<Task<int>> downloadTasksQuery = from url in urlList select ProcessURL(url, client);
Task<int>[] downloadTasks = downloadTasksQuery.ToArray();

// You can do other work here before awaiting.

// Await the completion of all the running tasks.
int[] lengths = await Task.WhenAll(downloadTasks);
```


---

## 다수의 비동기 작업중 한개가 완료되면 모두 취소하는 기능
https://msdn.microsoft.com/ko-kr/library/jj155758.aspx
`CancellationToken 와 함께 Task.WhenAny 메서드를 사용`

```c#
Task<int>[] downloadTasks = downloadTasksQuery.ToArray();
Task<int> firstFinishedTask = await Task.WhenAny(downloadTasks);
```

---

## 다수의 비동기 작업중 한개가 완료될때마다 처리
https://msdn.microsoft.com/ko-kr/library/jj155756.aspx
`Task.WhenAny`

```c#
// ***Create a query that, when executed, returns a collection of tasks.
IEnumerable<Task<int>> downloadTasksQuery =
    from url in urlList select ProcessURL(url, client, ct);

// ***Use ToList to execute the query and start the tasks. 
List<Task<int>> downloadTasks = downloadTasksQuery.ToList();

// ***Add a loop to process the tasks one at a time until none remain.
while (downloadTasks.Count > 0)
{
	// Identify the first task that completes.
	Task<int> firstFinishedTask = await Task.WhenAny(downloadTasks);
	
	// ***Remove the selected task from the list so that you don't
	// process it more than once.
	downloadTasks.Remove(firstFinishedTask);	
}
```

---

## async/await best practice
https://msdn.microsoft.com/en-us/magazine/jj991977.aspx


---

## C# http web server with TPL/async/await 
http://blog.tedd.no/2012/07/28/asyncawait-http-server-in-c/


> 서버메인클래스
> 클라이언트 접속처리

```c#
class Listener
{
    public readonly int Port;
    private readonly TcpListener _tcpListener;
    private readonly Task _listenTask;

    public Listener(int port)
    {
        Port = port;

        // Start listening
        _tcpListener = new TcpListener(IPAddress.Any, Port);
        _tcpListener.Start();

        // Start a background thread to listen for incoming
        _listenTask = Task.Factory.StartNew(() => ListenLoop());

    }

    private async void ListenLoop()
    {
        for (; ; )
        {
            // Wait for connection
            var socket = await _tcpListener.AcceptSocketAsync();
            if (socket == null)
                break;

            // Got new connection, create a client handler for it
            var client = new Client(socket);
            // Create a task to handle new connection
            Task.Factory.StartNew(client.Do);
        }
    }
}
```

> 서버에 접속한 클라이언트 처리

```c#
class Client
{
	private readonly Socket _socket;
	private readonly NetworkStream _networkStream;
	private readonly MemoryStream _memoryStream = new MemoryStream();
	private readonly StreamReader _streamReader;
	private readonly string _serverName = "Tedd.Demo.HttpServer";
		
	public Client(Socket socket)
	{
		_socket = socket;
		_networkStream = new NetworkStream(socket, true);
		_streamReader = new StreamReader(_memoryStream);
	}
		
	public async void Do()
	{
		// We are executed on a separate thread from listener, but will release this back to the threadpool as often as we can.
		byte[] buffer = new byte[4096];
		for (; ; )
		{
		    // Read a chunk of data
		    int bytesRead = await _networkStream.ReadAsync(buffer, 0, buffer.Length);
		
		    // If Read returns with no data then the connection is closed.
		    if (bytesRead == 0)
		        return;
		
		    // Write to buffer and process
		    _memoryStream.Seek(0, SeekOrigin.End);
		    _memoryStream.Write(buffer, 0, bytesRead);
		    bool done = ProcessHeader();
		    if (done)
		        break;
		}
		
	}
		
	private bool ProcessHeader()
	{
		// Our task is to find when full HTTP header has been received, then send reply.
		for (; ; )
		{
		    _memoryStream.Seek(0, SeekOrigin.Begin);
		    var line = _streamReader.ReadLine();
		    if (line == null)
		        break;
		
		    if (line.ToUpperInvariant().StartsWith("GET "))
		    {
		        // We got a request: GET /file HTTP/1.1
		        var file = line.Split(' ')[1].TrimStart('/');
		        // Default document is index.html
		        if (string.IsNullOrWhiteSpace(file))
		            file = "index.html";
		        // Send header+file
		        SendFile(file);
		        return true;
		    }
		
		}
		return false;
	}
		
	private async void SendFile(string file)
	{
		// Get info and assemble header
		byte[] data;
		string responseCode = "";
		string contentType = "";
		try
		{
		    if (File.Exists(file))
		    {
		        // Read file
		        data = File.ReadAllBytes(file);
		        contentType = GetContentType(Path.GetExtension(file).TrimStart(".".ToCharArray()));
		        responseCode = "200 OK";
		    }
		    else
		    {
		        data = System.Text.Encoding.ASCII.GetBytes("<html><body><h1>404 File Not Found</h1></body></html>");
		        contentType = GetContentType("html");
		        responseCode = "404 Not found";
		    }
		}
		catch (Exception exception)
		{
		    // In case of error dump exception to client.
		    data = System.Text.Encoding.ASCII.GetBytes("<html><body><h1>500 Internal server error</h1><pre>" + exception.ToString() + "</pre></body></html>");
		    responseCode = "500 Internal server error";
		}
		
		string header = string.Format("HTTP/1.1 {0}\r\n"
		                              + "Server: {1}\r\n"
		                              + "Content-Length: {2}\r\n"
		                              + "Content-Type: {3}\r\n"
		                              + "Keep-Alive: Close\r\n"
		                              + "\r\n",
		                              responseCode, _serverName, data.Length, contentType);
		// Send header & data
		var headerBytes = System.Text.Encoding.ASCII.GetBytes(header)
		await _networkStream.WriteAsync(headerBytes, 0, headerBytes.Length);
		await _networkStream.WriteAsync(data, 0, data.Length);
		await _networkStream.FlushAsync();
		// Close connection (we don't support keep-alive)
		_networkStream.Dispose();
	}

	/// <summary>
	/// Get mime type from a file extension
	/// </summary>
	/// <param name="extension">File extension without starting dot (html, not .html)</param>
	/// <returns>Mime type or default mime type "application/octet-stream" if not found.</returns>
	private string GetContentType(string extension)
	{
		// We are accessing the registry with data received from third party, so we need to have a strict security test. We only allow letters and numbers.
		if (Regex.IsMatch(extension, "^[a-z0-9]+$", RegexOptions.IgnoreCase | RegexOptions.Compiled))
		    return (Registry.GetValue(@"HKEY_CLASSES_ROOT\." + extension, "Content Type", null) as string) ?? "application/octet-stream";
		return "application/octet-stream";
    }
}
```

> 서버실행

```c#
class Program
{
    private static Listener listener;
    static void Main(string[] args)
    {
        listener = new Listener(8080);
        Console.WriteLine("Listening on port 8080 (http://localhost:8080/).");
        Console.WriteLine("Remember to put the files you want to serve in the output folder of the project.");
        Console.WriteLine("Press any key to exit.");
        Console.ReadKey();
    }
}
```