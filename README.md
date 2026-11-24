# gRPC Practice in Go

[简体中文](README.zh-CN.md)

A side-by-side Go example of `net/rpc` over HTTP and gRPC. Compare the client lifecycle for both services and see how Protocol Buffers generates typed gRPC client and server code.

## Read the examples

| Source | What it shows |
| --- | --- |
| [hello.proto](protocol/proto/hello.proto) | The unary `Greeter.SayHello` RPC, plus repeated fields, enums, maps and nested messages. |
| [article_list.proto](protocol/proto/article_list.proto) | Importing `Article` from [article.proto](protocol/proto/article.proto). |
| [pb_practice1.go](pb_practice1.go) | Encoding and decoding a `UserInfo` message with `proto.Marshal` and `proto.Unmarshal`. |
| [rpc_server/start.go](rpc_server/start.go) and [rpc_client/start.go](rpc_client/start.go) | Starting and stopping both servers, registering the gRPC service, and calling it with a one-second deadline. |

A Go gRPC server embeds `UnimplementedGreeterServer`, implements `SayHello`, and registers the implementation. The client creates a `GreeterClient` from its connection. A request that exceeds its deadline can return `DeadlineExceeded`.

## Run the demo

The repository includes the generated Go files. With Go 1.19 or later, run the demo from the repository root. Keep local ports `10000` and `10001` free for the RPC and gRPC listeners.

```sh
go run .
```

[start.sh](start.sh) also builds and runs the demo. The current demo can log a closed-listener error and exit with code 1 during shutdown after both clients have received their replies.

## Regenerate the Go files

Install `protoc` from the [Protocol Buffers releases](https://github.com/protocolbuffers/protobuf/releases) or with `brew install protobuf` on macOS. Install Go generators compatible with your toolchain and put their binaries on `PATH`. The versions below match the generated files tracked in this repository.

```sh
go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.30.0
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.3.0
protoc --go_out=./protocol --go-grpc_out=./protocol protocol/proto/*.proto
```

`--go_out` generates message types. `--go-grpc_out` generates the service bindings. Other languages use their corresponding generator.
