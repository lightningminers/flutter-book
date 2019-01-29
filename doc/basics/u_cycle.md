## Flutter 的生命周期

如果你写过 iOS 了解过 ViewController 那么其实能够了解生命周期在 UI 绘制的场景中是多么有用，Flutter 也存在生命周期，只不过它的回调方法都体现在 State 中，源码可参考：[https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/widgets/framework.dart#L930](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/widgets/framework.dart#L930)，对于我们理解生命周期，写出一个合理的 Widget 至关重要。

大体上它的生命周期可以分为三个阶段：

- 初始化
- 状态改变
- 销毁

各方法可参考：[https://docs.flutter.io/flutter/widgets/State-class.html#instance-methods](https://docs.flutter.io/flutter/widgets/State-class.html#instance-methods)，这里我只举例两个最常用的，因为它和我们的场景至关重要。

### didUpdateWidget

当你调用了 setState 将 Widget 的状态被改变时 didUpdateWidget 会被调用，Flutter 会创建一个新的 Widget 来绑定这个 State，并在这个方法中传递旧的 Widget ，因此如果你想比对新旧 Widget 并且对 State 做一些调整，你可以用它，另外如果你的某些 Widget 上涉及到 controller 的变更，要么一定要在这个回调方法中移除旧的 controller 并创建新的 controller 监听。

```dart
@override
void didUpdateWidget(AVCycleLess oldWidget){
  super.didUpdateWidget(oldWidget);
}
```

### dispose

某些情况下你的 Widget 被释放了，一个很经典的例子是 Navigator.pop 被调用，如果被释放的 Widget 中有一些监听或持久化的变量，你需要在 dispose 中进行释放，在我们学习 Bloc 或 Stream 时应该能了解 dispose ，在这个回调方法中去关闭 Stream。

```dart
void dispose(){
  _countController.close();
  _actionController.close();
}
```

### 关于 App 的生命周期

我们知道在 App 中会有比如从前台切入到后台再从后台切入到前台的场景，在 iOS 中这些生命周期都可以在 AppDelegate 中被体现，那么 Flutter 中我们该如何处理这些场景？对于 App 级别的生命周期与上述 Widget 生命周期相比，稍有不同。

源代码可参考：[https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/widgets/binding.dart](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/widgets/binding.dart)

```bash
flutter: AppLifecycleState.inactive
flutter: AppLifecycleState.paused
flutter: AppLifecycleState.inactive
flutter: AppLifecycleState.resumed
```

上述是 App 级别生命周期的执行顺序。

先让我们来改造一下 AVCycleLess 这个 StatefulWidget，如果我们想监听 App 级别的生命周期需要使用 WidgetsBinding 来监听 WidgetsBindingObserver，因此我们需要把 State 稍微改变一下，并且我们也需要在 dispose 回调方法中移除这个监听。

```dart
class AVCycleLessState extends State<AVCycleLess> with WidgetsBindingObserver {}
```

重载 didChangeAppLifecycleState ，Flutter 封装了一个枚举 AppLifecycleState 来描述这个生命周期，完整代码如下：

```dart
class AVCycleLess extends StatefulWidget {

  AVCycleLess({Key key}): super(key: key);

  AVCycleLessState createState() =>  new AVCycleLessState();
}

class AVCycleLessState extends State<AVCycleLess> with WidgetsBindingObserver {


  @override
  void initState(){
    super.initState();
    WidgetsBinding.instance.addObserver(this);
  }

  @override
  Widget build(BuildContext context){
    return new MaterialApp(
      home: new Scaffold(
        appBar: new AppBar(
          title: new Text('AVCycleLess'),
        ),
        body: new Center(
          child: new Text('AVCycleLess---'),
        ),
      ),
    );
  }

  @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    debugPrint('$state');
  }

  @override
  void dispose(){
    WidgetsBinding.instance.removeObserver(this);
    super.dispose();
  }
}

```

