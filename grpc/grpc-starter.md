# 复杂一点的例子
## GRPC的四种服务类型
GRPC允许定义四种服务类型，分别是：
1. 简单RPC。
2. 服务器端流式RPC。
3. 客户端流式PRC。
4. 双向流式PRC。

简单PRC。客户端使用存根（stub）发送请求到服务器并等待响应返回，就像平常的函数调用一样。下面的例子中，可以看到proto文件中定义了一个名为GetFeature的函数，接收一个Point类型的入参，返回一个Feature类型的返回值。
```proto
rpc GetFeature(Point) returns (Feature) {}
```

服务端流式PRC。客户端发送一个请求到服务端，但是服务端返回的对象是一个流。客户端拿着这个流，可以读取流中返回的序列，直到流中没有任何消息为止。下面的例子中包含了一个服务端流的RPC函数定义，客户端的入参是一个Rectangle，而返回是一个包含多个Feature对象的stream。
```proto
rpc ListFeatures(Rectangle) returns (stream Feature) {}
```

客户端流式PRC。客户端写入一个消息序列，使用流的形式发送到服务器。一旦客户端完成了写入，就等待服务器返回结果。所以下面RecordRoute的例子中，入参是一个Point流，等所有传输结束了，服务器返回客户端一个RouteSummary对象。
```proto
rpc RecordRoute(stream Point) returns (RouteSummary) {}
```

双向流式RPC。指的是双方都用读写流去发送消息序列。两个流能够独立操作，比如服务器端能选择等待所有客户端的消息到达后再给客户端响应，也可以交替对客户端的请求进行应答。
```proto
rpc RouteChat(stream RouteNote) returns (stream RouteNote) {}
```

现在我们使用分别使用四种服务方式来写Demo。
## 完整的proto文件定义
```proto
syntax = "proto3";

option java_multiple_files = true;
option java_package = "io.grpc.examples.routeguide";
option java_outer_classname = "RouteGuideProto";
option objc_class_prefix = "RTG";

package routeguide;

service RouteGuide {

  rpc GetFeature(Point) returns (Feature) {}

  rpc ListFeatures(Rectangle) returns (stream Feature) {}

  rpc RecordRoute(stream Point) returns (RouteSummary) {}

  rpc RouteChat(stream RouteNote) returns (stream RouteNote) {}
}

message Point {
  int32 latitude = 1;
  int32 longitude = 2;
}

message Rectangle {
  Point lo = 1;
  Point hi = 2;
}

message Feature {
  string name = 1;
  Point location = 2;
}

message RouteNote {
  Point location = 1;
  string message = 2;
}

message RouteSummary {
  int32 point_count = 1;
  int32 feature_count = 2;
  int32 distance = 3;
  int32 elapsed_time = 4;
}
```
如上所示，RouteGuide中定义了四个RPC的函数，分别表示了四种上一节提到的四种服务类型。文件还定义了几个message，表示要使用到的实体类。

## 简单RPC
### 服务器端
根据前文第一个例子可知，此篇也需要构建一个类**RouteGuideService**继承自**RouteGuideGrpc.RouteGuideImplBase**，然后我们可以编写我们简单RPC的处理逻辑。
```java
/**
* 简单RPC
*
* @param request
* @param responseObserver
*/
@Override
public void getFeature(Point request, StreamObserver<Feature> responseObserver) {
   Feature feature = checkFeature(request);
   responseObserver.onNext(feature);
   responseObserver.onCompleted();
}

private Feature checkFeature(Point location) {
   System.out.println("构造了新的Feature元素。");
   return Feature.newBuilder().setName("").setLocation(location).build();
}
```

### 客户端
客户端构造一个channel和一个阻塞调用的stub。
```java
 private final ManagedChannel channel;
 private final RouteGuideGrpc.RouteGuideBlockingStub blockingStub;

 public RouteGuideClient(String host, int port) {
     this.channel = NettyChannelBuilder.forAddress(host, port)
             .negotiationType(NegotiationType.PLAINTEXT)
             .build();
     this.blockingStub = RouteGuideGrpc.newBlockingStub(channel);
 }

```

通讯逻辑如下。
```java
 public void blockSend() {
     Point point = Point.newBuilder().setLatitude(123).setLongitude(234).build();
     Feature response;
     try {
         response = blockingStub.getFeature(point);
     } catch (StatusRuntimeException e) {
         System.out.println("RPC调用失败:" + e.getMessage());
         return;
     }
     System.out.println("服务器返回信息:\n" + response.getName() + response.getLocation());
 }
```

## 服务器端流式RPC
### 服务器端
服务器端定义的函数体和简单RPC没有太大的区别，唯一区别的是服务器端使用StreamObserver可以执行多次onNext方法，发送多个Feature对象。
```java
/**
* 服务端流式RPC
*
* @param request
* @param responseObserver
*/
@Override
public void listFeatures(Rectangle request, StreamObserver<Feature> responseObserver) {
   Point lo = Point.newBuilder(request.getLo()).build();
   Point hi = Point.newBuilder(request.getHi()).build();
   responseObserver.onNext(Feature.newBuilder().setName("lo").setLocation(lo).build());
   responseObserver.onNext(Feature.newBuilder().setName("hi").setLocation(hi).build());
   responseObserver.onCompleted();
}
```

### 客户端
代码和简单RPC调用类似。
```java
public void serverStreamSend() {
  Rectangle request =
          Rectangle.newBuilder()
                  .setLo(Point.newBuilder().setLatitude(11).setLongitude(22).build())
                  .setHi(Point.newBuilder().setLatitude(33).setLongitude(44).build())
                  .build();
  Iterator<Feature> features = blockingStub.listFeatures(request);
  while (features.hasNext()) {
      Feature item = features.next();
      System.out.println(item.getName() + "\n" + item.getLocation());
  }
}
```

## 客户端流式RPC
### 服务器端
服务器端逻辑代码对比前面的稍微有些复杂。这个函数接收一个StreamObserver对象作为入参，也返回一个StreamObserver对象。入参的这个StreamObserver<RouteSummary>对象，作用是给客户端返回消息，而返回值中的StreamObserver<Point>，是用于读取解析客户端发送的数据流流。可以在这个函数体里，返回了一个StreamObserver的匿名类实例。里面需要重写实现3个方法，分别是onNext,onError,onCompleted。

1. onNext函数，在每次客户端数据到来的时候会被调用，入参就是客户端发送的对象。可以在onNext函数中处理客户端发送的数据。
2. onError函数，用于传输处理出错的情况下，对异常的处理。
3. onCompleted函数，在客户端数据通信结束后会被调用。服务器端在客户端完成数据通信后给客户端发送一个应答。

服务器端的代码如下。

```java
/**
* 客户端流式RPC
*
* @param responseObserver
* @return
*/
@Override
public StreamObserver<Point> recordRoute(StreamObserver<RouteSummary> responseObserver) {
   return new StreamObserver<Point>() {
       int pointCount;
       int featureCount;
       int distance;
       Point previous;
       long startTime = System.nanoTime();

       @Override
       public void onNext(Point point) {
           pointCount++;
           if (point.getLatitude() % 2 == 0)
               featureCount++;
           if (Objects.nonNull(previous)) {
               distance = abs((point.getLatitude() - previous.getLatitude()) + (point.getLongitude() - previous.getLongitude()));
           }
           previous = point;
       }

       @Override
       public void onError(Throwable throwable) {
           System.out.println("Error occurs: =============>" + throwable.getMessage());
       }

       @Override
       public void onCompleted() {
           int seconds = (int) ((System.nanoTime() - startTime) / 1000);
           responseObserver
                   .onNext(RouteSummary.newBuilder()
                           .setPointCount(pointCount)
                           .setFeatureCount(featureCount)
                           .setElapsedTime(seconds)
                           .setDistance(distance).build());
           responseObserver.onCompleted();
       }
   };
}
```

### 客户端
客户端需要声明一个channel和一个异步传输的stab。这是因为客户端的数据传输不是同步阻塞的，每次发送数据后无需等待服务器端的响应。
```java
private final ManagedChannel channel;
private final RouteGuideGrpc.RouteGuideStub asyncStub;

public RouteGuideClient(String host, int port) {
  this.channel = NettyChannelBuilder.forAddress(host, port)
          .negotiationType(NegotiationType.PLAINTEXT)
          .build();
  this.asyncStub = RouteGuideGrpc.newStub(channel);
}
```

客户端的通信逻辑中，需要声明两个StreamObserver，一个observer用于监听，获取和处理来自服务器端的响应，而另一个则用于数据的发送。客户端的通信代码如下。
```java
public void clientStreamSend() throws Exception {
  final SettableFuture<Void> finishFuture = SettableFuture.create();
  StreamObserver<RouteSummary> responseObserver = new StreamObserver<RouteSummary>() {
      @Override
      public void onNext(RouteSummary summary) {
          String format = String.format("Finished trip with %d points. Passed %d features. "
                          + "Travelled %d meters. It took %d seconds.", summary.getPointCount(),
                  summary.getFeatureCount(), summary.getDistance(), summary.getElapsedTime());
          System.out.println(format);
      }

      @Override
      public void onError(Throwable throwable) {
          finishFuture.setException(throwable);
      }

      @Override
      public void onCompleted() {
          finishFuture.set(null);
      }
  };

  StreamObserver<Point> requestObserver = asyncStub.recordRoute(responseObserver);

  try {
      Random random = new Random(1000L);
      Point p1 = Point.newBuilder().setLatitude(random.nextInt()).setLongitude(random.nextInt()).build();
      Point p2 = Point.newBuilder().setLatitude(random.nextInt()).setLongitude(random.nextInt()).build();
      requestObserver.onNext(p1);
      if (finishFuture.isDone()) {
          return;
      }
      Thread.sleep(2000L);
      requestObserver.onNext(p2);
      requestObserver.onCompleted();
      finishFuture.get();
  } catch (Exception e) {
      requestObserver.onError(e);
      throw e;
  }
}
```

在上面这个例子中，服务器端的最终响应是RouteSummary对象，因此需要编写一个StreamObserver<RouteSummary>观察者，来处理服务端返回数据后的代码处理逻辑。下面代码中的responseObserver对象里，重写了onNext逻辑。同上文一样，onNext在服务器返回数据后会被调用。

而requestObserver则是需要负责将服务器端发送数据。逻辑比较简单，只要多次调用requestObserver.onNext函数即可，调用完成以后，需要执行以下onCompleted函数告诉服务器数据已经发送完了。

值得注意的是，前面的代码中新建了一个SettableFuture<Void>对象，这是由于客户端发送数据是异步的，函数在finishFuture.get()阻塞等待服务器端的应答返回。其用法类似Java中Future对象。

## 双向流式PRC
### 服务器端
逻辑和客户端流式RPC非常类似，区别在于由于服务端也是流式，因此可以多次给客户端发送消息。因此下面的例子中，每次收到客户端的RouteNote都直接在onNext中给出响应。最后在onCompleted中只是执行了完成对话的操作。
```java
@Override
public StreamObserver<RouteNote> routeChat(StreamObserver<RouteNote> responseObserver) {
   return new StreamObserver<RouteNote>() {
       @Override
       public void onNext(RouteNote routeNote) {
           System.out.println("收到来自客户端的消息：" + routeNote);
           responseObserver.onNext(routeNote);
       }

       @Override
       public void onError(Throwable throwable) {
           System.out.println("Error occurs: =============>" + throwable.getMessage());
           responseObserver.onError(throwable);
       }

       @Override
       public void onCompleted() {
           responseObserver.onCompleted();
       }
   };
}
```

### 客户端
客户端代码和上例的客户端代码基本类似，代码如下。
```java
public void dualStreamSend() throws Exception {
  final SettableFuture<Void> finishFuture = SettableFuture.create();
  StreamObserver<RouteNote> responseObserver = new StreamObserver<RouteNote>() {
      @Override
      public void onNext(RouteNote routeNote) {
          System.out.println("Receive:" + routeNote.getMessage() +"\n" + routeNote.getLocation());
      }

      @Override
      public void onError(Throwable throwable) {
          finishFuture.setException(throwable);
      }

      @Override
      public void onCompleted() {
          finishFuture.set(null);
      }
  };

  StreamObserver<RouteNote> requestObserver = asyncStub.routeChat(responseObserver);

  try {
      Random random = new Random(1000L);
      for (int i = 0; i < 10; i++) {
          Point location = Point.newBuilder().setLongitude(random.nextInt()).setLatitude(random.nextInt()).build();
          RouteNote routeNote = RouteNote.newBuilder().setLocation(location).setMessage(String.valueOf(i)).build();
          requestObserver.onNext(routeNote);
          if (finishFuture.isDone()) {
              break;
          }
      }
      requestObserver.onCompleted();
      finishFuture.get();
      System.out.println("finish route chat!");
  } catch (Exception e) {
      requestObserver.onError(e);
      throw e;
  }
}
```