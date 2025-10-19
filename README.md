# Proto RPC Tool

Proto RPC Tool 是一个基于 C++17 的事件驱动网络与 ProtoBuf RPC 示例项目。仓库主要包含三部分：

- `network/`：Reactor 模型的 TCP 网络库，实现事件循环、连接管理、缓冲区与线程池等基础设施。
- `proto_rpc/`：封装 ProtoBuf RPC 的编解码、通道、服务注册与调度逻辑。
- `examples/`：`monitor.proto` 及其客户端、服务端示例，演示如何使用上述组件完成一次完整的 RPC 调用。

## 特性概览

- 非阻塞的 epoll 事件循环与多线程调度。
- `TcpConnection`、`TcpServer`、`TcpClient` 等高层抽象，简化数据收发流程。
- 自定义 ProtoBuf 报文格式，内置请求 ID、校验和与错误处理。
- 监控示例展示服务实现、Stub 调用与定时请求逻辑。
- 提供 Docker 镜像与脚本，便于快速获得完整依赖环境。

## 目录结构

```
proto_rpc_tool/
├── network/          # 网络库源码与头文件
├── proto_rpc/        # RPC 框架实现与 proto 定义
├── examples/         # 示例 proto、服务端与客户端
├── docker/           # Dockerfile 与辅助脚本
└── CMakeLists.txt    # 顶层 CMake 构建入口
```

## 构建依赖

- CMake 3.1+
- 支持 C++17 的编译器（GCC / Clang）
- protobuf、glog、Boost、abseil、zlib（可通过 Docker 镜像获取）

## 本地编译

```bash
mkdir -p build
cd build
cmake ..
cmake --build .
```

构建完成后，可执行文件位于 `build/examples/` 目录，例如 `protobuf_rpc_server` 与 `protobuf_rpc_client`。

## 运行示例

1. 在服务器上启动 `protobuf_rpc_server`，内部注册监控服务并进入事件循环。
2. 在客户端运行 `protobuf_rpc_client <server-ip>`，程序每隔 3 秒调用远程方法并打印响应。

## Docker 使用

```bash
# 在 docker/build/ 中构建镜像
cd docker/build
docker build -t network:rpc -f network_rpc.dockerfile .

# 回到仓库根目录后
./docker/scripts/rpc_docker_run.sh   # 启动并挂载源码
./docker/scripts/rpc_docker_into.sh  # 进入容器进行开发
```

容器内已预装所有依赖，代码位于 `/work`。

## 扩展建议

- 新增服务时，在 `proto_rpc/proto/` 下定义新的 `.proto` 并更新对应 CMake 列表。
- 通过 `RpcServer::registerService` 注册多个服务实例，实现多服务共存。
- 可在 `ProtoRpcCodec` 中扩展压缩、鉴权等协议字段以满足业务需求。

## 许可证

仓库未包含开源许可证，使用前请确认授权。
