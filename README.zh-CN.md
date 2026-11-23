# Go gRPC 练习

[English](README.md)

一个在本机并行运行 Go `net/rpc` over HTTP 和 gRPC 服务的练习。可以对照两种客户端从建立连接到关闭的调用流程，也可以看到 Protocol Buffers 服务定义如何生成带类型的 Go 客户端和服务端代码。

## 阅读示例

| 源码 | 内容 |
| --- | --- |
| [hello.proto](protocol/proto/hello.proto) | 一元 RPC `Greeter.SayHello`，以及重复字段、枚举、map 和嵌套消息。 |
| [article_list.proto](protocol/proto/article_list.proto) | 从 [article.proto](protocol/proto/article.proto) 导入 `Article`。 |
| [pb_practice1.go](pb_practice1.go) | 用 `proto.Marshal` 和 `proto.Unmarshal` 编解码 `UserInfo` 消息。 |
| [rpc_server/start.go](rpc_server/start.go) 和 [rpc_client/start.go](rpc_client/start.go) | 启停两种服务、注册 gRPC 服务，以及设置一秒请求超时。 |

Go gRPC 服务端嵌入 `UnimplementedGreeterServer`，实现并注册 `SayHello`。客户端通过连接创建 `GreeterClient`。请求超时可能返回 `DeadlineExceeded`。

## 运行示例

仓库已包含生成的 Go 代码。使用 Go 1.19 或更新版本，在仓库根目录运行。请留出本地端口 `10000` 和 `10001`，分别供 RPC 和 gRPC 监听。

```sh
go run .
```

也可以用 [start.sh](start.sh) 构建并运行示例。当前版本在两种客户端都收到回复后，关闭监听器时可能记录连接关闭错误，并以状态码 1 退出。

## 重新生成 Go 代码

在 [Protocol Buffers 发布页](https://github.com/protocolbuffers/protobuf/releases)下载 `protoc`，macOS 上也可用 `brew install protobuf` 安装。安装与当前工具链兼容的 Go 生成器，并把安装目录加入 `PATH`。以下版本与仓库中已提交的生成文件一致。

```sh
go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.30.0
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.3.0
protoc --go_out=./protocol --go-grpc_out=./protocol protocol/proto/*.proto
```

`--go_out` 生成消息类型，`--go-grpc_out` 生成服务绑定代码。其他语言可以使用各自的生成器。
