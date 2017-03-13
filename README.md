# Introduction

[![Circle CI](https://circleci.com/gh/phuongdo/go-redisproto.svg?style=svg&circle-token=f008be1b378dbf91d68b4b43e0cd2cab97698afe)](https://circleci.com/gh/phuongdo/go-redisproto)


There are plenty of good client/server implementations of the redis protocol, but not many of them that are designed for RPC (see [My Blog](https://pdoviet.wordpress.com/2017/03/06/a-simple-api-using-protobuf-and-grpc/) to see how to use RPC in golang ).
`go-redisproto` is a lightweight library for building server software capable of exploit the advantages of the redis protocol and RPC.
The advantages of `go-redisproto` is to use it's own buffered reader to avoid memory copy compared to other exist client libraries.
It parses data really fast and requires a minimum amount of memory. Therefore, it enhances
the performance in both client and server services, especially in some projects required RPC.
This could be an alternate implementation of redis, a custom proxy to redis, and widely be used in high throughput API applications.

# Acknowledgement
Many thanks to secmack for his original version(github.com/secmack/go-redisproto), who gave me the golden opportunity to do this wonderful project.
He worked so hard to provide this source code.

# Installation
Use go tool to install the package in your packages tree:
```bash
go get github.com/secmask/go-redisproto
```

Then you can use it in import section of your Go programs:
```golang
import "github.com/secmask/go-redisproto"
```

# Usage

See full example [example/main.go](example/main.go)
```golang
import "github.com/secmask/go-redisproto"
.....
    parser := redisproto.NewParser(conn)
    writer := redisproto.NewWriter(bufio.NewWriter(conn))

...
    // read a command from a connection
    command, err := parser.ReadCommand()
    if err != nil {
        _, ok := err.(*redisproto.ProtocolError)
        if ok {
            ew = writer.WriteError(err.Error())
        } else {
            log.Println(err, " closed connection to ", conn.RemoteAddr())
            break
        }
    } else {
        cmd := strings.ToUpper(string(command.Get(0)))
        switch cmd {
        case "GET":
            ew = writer.WriteBulkString("dummy")
        case "SET":
            ew = writer.WriteBulkString("OK")
        default:
            ew = writer.WriteError("Command not support")
        }
    }
    if command.IsLast() {
        writer.Flush()
    }
    if ew != nil {
        log.Println("Connection closed", ew)
        break
    }


```



Start a demo server:
```bash
$ go run example/main.go
````

Test the protocol
```bash
telnet localhost 6380
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
hello
-Command not support
SET hello
$2
OK
GET hello
$5
dummy
```

# Benchmark
Start a demo server:
```bash
$ go run example/main.go
```

Run redis-benchmark (required redis client installed in your local computer)
```bash
redis-benchmark -p 6380 -c 32 -n 1000000 lpush k2 aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
====== lpush k2 aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa ======
  1000000 requests completed in 6.51 seconds
  32 parallel clients
  3 bytes payload
  keep alive: 1

99.87% <= 1 milliseconds
99.96% <= 2 milliseconds
99.99% <= 3 milliseconds
100.00% <= 4 milliseconds
100.00% <= 5 milliseconds
100.00% <= 9 milliseconds
100.00% <= 10 milliseconds
153727.91 requests per second

```

# Applications
applications used `go-redisproto`

1. https://github.com/secmask/mqueue    
2. https://github.com/secmask/roller

# License
go-redisproto is available under The MIT License (MIT).
