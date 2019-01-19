## 谈谈 Flutter 的通信和插件

对于跨平台的方案来说通信是其非常重要的一个基础，官网贴了一张很直观的图，让我们可以了解其架构。

![](../images/flutter-21.png)

整个插件的消息和响应以异步的方式进行传递，以确保用户界面不会卡顿；

从上述的架构图中，其实已经很明确的知道了在 Dart 端使用 MethodChannel API 来发送消息或调用对应的方法，而 Native 平台上 Android 的 MethodChannel 和 iOS 的 FlutterMethodChannel 处理了接收调用和返回结果，这一过程也可以反向调用，即 Native 主动的给 Dart 端发送消息，如果你有兴趣不妨看一个插件的实现 [https://pub.dartlang.org/packages/quick_actions](https://pub.dartlang.org/packages/quick_actions)，它很直白的实现了这样的过程。对于数据转换的过程，如果你了解过 JavaScriptCore 和 Objective-C 的互转就能明白，比如 JavaScript 端的 string 转换成 Objective-C 的NSString，number 转换成 NSNumber。对于 Dart 而言也有这样数据转换的对照表，你可以参考 [https://flutter.io/docs/development/platform-integration/platform-channels](https://flutter.io/docs/development/platform-integration/platform-channels)，不难讲述的特别清楚。

接下来让我以 iOS 视角写一个简单的插件让大家能很直白的了解到 Dart 和 Native 的通信过程，并且这也是写插件非常有用的方式，你可以利用 flutter 提供的命名行来初始化一个模板项目：

```bash
$ flutter create --org com.example.icepy --template=plugin icepyfetch
```

首先我们在 Dart 端导入 import 'package:flutter/services.dart' 模块利用 MethodChannel 来创建连接的通道：

```dart
class IcepyFetch {
  static const MethodChannel _channel = const MethodChannel('icepy.fetch');
}
```

> 连接通道的名需要保持唯一性

接着我们可以利用 Futrue 来写一个简单的方法，这个方法从 Native 端获取 iOS 的版本号：

```dart
static Future<String> get platformVersion async {
    final String version = await _channel.invokeMethod('getPlatformVersion');
    return version;
}
```

接下来我们在 iOS 端实现 FlutterPlugin 协议的 + (void)registerWithRegistrar:(NSObject<FlutterPluginRegistrar>*)registrar ：

```Objective-C
// .h 文件
#import <Flutter/Flutter.h>

@interface IcepyFetchPlugin : NSObject<FlutterPlugin>
@end

// .m 文件

#import "IcepyFetchPlugin.h"

@implementation IcepyFetchPlugin
+ (void)registerWithRegistrar:(NSObject<FlutterPluginRegistrar>*)registrar {
  FlutterMethodChannel* channel = [FlutterMethodChannel
      methodChannelWithName:@"icepy.fetch"
            binaryMessenger:[registrar messenger]];
  IcepyFetchPlugin* instance = [[IcepyFetchPlugin alloc] init];
  [registrar addMethodCallDelegate:instance channel:channel];
}

@end
```

你在 Dart 端创建的 icepy.fetch 通道名，也需要使用 FlutterMethodChannel 来创建 Native 端的通道名。

接下来，我们继续实现另外一个方法 - (void)handleMethodCall:(FlutterMethodCall*)call result:(FlutterResult)result，由于我们在 Dart 端 invokeMethod 的方法名叫 getPlatformVersion ，因此在这个方法中每一次的通信 Flutter 都会传递一个 FlutterMethodCall类型对象给你使用，在method属性中，你可以获取到从 Dart 端发送过来的方法名：

```Objective-C
- (void)handleMethodCall:(FlutterMethodCall*)call result:(FlutterResult)result {
  if ([@"getPlatformVersion" isEqualToString:call.method]) {
    result([@"iOS " stringByAppendingString:[[UIDevice currentDevice] systemVersion]]);
  } else {
    result(FlutterMethodNotImplemented);
  }
}
```

当你测试过没有问题之后就可以利用另外一个命令让这个插件发布到 Package 市场中：

```bash
$ flutter packages pub publish
```

等待发布成功之后你可以将 package 名写入 pubspec.yaml 的 dependencies，一般情况下你使用 AS 或者 VSCode 保存配置文件即可，能自动安装你发布的 package，但是如果你想在开发阶段进行测试，也可以将你的 package 写在 dev_dependencies 中，指定你的 path 路径即可。

让我们最后来看一看，在业务代码里该如何使用 icepy.fetch package 来获取 Native 平台版本号。

```dart
import 'package:icepyfetch/main.dart'

Future<void> fetchVersionState() async {
  String version;
  try {
    version = await IcepyFetch.platformVersion;
  } on PlatformException {
    version = 'Failed to get platform version.';
  }
  if (!mounted) return;
  setState(() {
    _version = version;
  });
}
```