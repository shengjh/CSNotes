本文记录如何实现一个 grpc server , 参考 grpc 官方文档 https://grpc.io/docs/languages/cpp/basics/

# CPP
1. 在 .proto 文件中定义一个服务(service), 以及这个服务的方法
```c
// example.proto
// Obtains the feature at a given position.
rpc GetFeature(Point) returns (Feature) {}
```

2. 实现定义的服务
```cpp
class RouteGuideImpl final : public RouteGuide::Service {
    Status GetFeature(ServerContext* context, const Point* point,
                  Feature* feature) override {
    feature->set_name(GetFeatureName(*point, feature_list_));
    feature->mutable_location()->CopyFrom(*point);
    return Status::OK;
}
...
}
// 这里是同步的方法，也可以实现异步的方法
// service method(这里是GetFeature) 会被多个线程调用，所以这个方法代码里面应该是线程安全的
```

3. 启动 server
启动 grpc server 分为以下几个步骤：
```cpp
void RunServer(const std::string& db_path) {
  std::string server_address("0.0.0.0:50051");
  RouteGuideImpl service(db_path); // 1.创建一个已完成的服务实例

  ServerBuilder builder;　// 2.创建一个 ServerBuilder 实例
  builder.AddListeningPort(server_address, grpc::InsecureServerCredentials()); // 3.监听地址和端口
  builder.RegisterService(&service); // 4.将服务实例注册到 builder 中
  std::unique_ptr<Server> server(builder.BuildAndStart());　// 5.启动 server
  std::cout << "Server listening on " << server_address << std::endl;
  server->Wait(); //6. 阻塞知道程序结束或者调用 shutdown()
}
```