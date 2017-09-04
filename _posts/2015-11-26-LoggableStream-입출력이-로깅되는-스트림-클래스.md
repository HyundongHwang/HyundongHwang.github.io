---
layout: post
title: 151126 LoggableStream ( 입출력이 로깅되는 스트림 클래스 )
comments: true
tags:
- c#
- stream
- study
- 꿀팁
---

<!-- TOC -->


<!-- /TOC -->

<br>
<br>
<br>

네트워크 스트림의 입출력을 전체 로그로 찍고 싶은데, 매 줄마다 `Trace.Information("hello...");` 하면 모양빠지니깐 좀 모양 좋게 코딩해 볼수는 없을까 해서 만들었다.

스트림 입출력 시에 버퍼를 조금씩 인코딩 해서 로그 찍는 기능이 있다. 아이디어는 스택오버플로우 통해서 얻어서 해결한 것.

http://stackoverflow.com/questions/33935411/how-can-i-set-streamwriters-source-as-multiple-in-net/33938877

```
public class LoggableStream : Stream
{
    private Stream _stream;
    private Encoding _textEncoding;



    public LoggableStream(Stream stream, Encoding textEncoding)
    {
        _stream = stream;
        _textEncoding = textEncoding;
    }

    public override bool CanRead
    {
        get
        {
            return _stream.CanRead;
        }
    }

    public override bool CanSeek
    {
        get
        {
            return _stream.CanSeek;
        }
    }

    public override bool CanWrite
    {
        get
        {
            return _stream.CanWrite;
        }
    }

    public override long Length
    {
        get
        {
            return _stream.Length;
        }
    }

    public override long Position
    {
        get
        {
            return _stream.Position;
        }
        set
        {
            _stream.Position = Position;
        }
    }

    public override void Flush()
    {
        _stream.Flush();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var result = _stream.Read(buffer, offset, count);

        try
        {
            var log = this._textEncoding.GetString(buffer, offset, count);
            Trace.TraceInformation("READ : " + log);
        }
        catch (Exception ex)
        {
            Trace.TraceError(ex.ToString());
        }

        return result;
    }

    public override long Seek(long offset, SeekOrigin origin)
    {
        var result = _stream.Seek(offset, origin);
        return result;
    }

    public override void SetLength(long value)
    {
        _stream.SetLength(value);
    }

    public override void Write(byte[] buffer, int offset, int count)
    {
        _stream.Write(buffer, offset, count);

        try
        {
            var log = this._textEncoding.GetString(buffer, offset, count);
            Trace.TraceInformation("WRIT : " + log);
        }
        catch (Exception ex)
        {
            Trace.TraceError(ex.ToString());
        }
    }
}
```

```
using (var netStream = _controlClient.GetStream())
using (var sr = new StreamReader(new LoggableStream(netStream, Encoding.UTF8)))
using (var sw = new StreamWriter(new LoggableStream(netStream, Encoding.UTF8)))
{
    var readLine = sr.ReadLine();
    sw.WriteLine("hello");
}
```