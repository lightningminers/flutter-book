## Transform

> A widget that applies a transformation before painting its child.

Transform 作为一个很常用的处理 Widget 形变的 Widget 通常会使用在动画中，通过这个 Widget 的组合，就像 CSS 中的 transform 属性一样，我们可以很方便的做出来动效，先让我们来看一个小例子：

![](../images/flutter-65.png)

```dart
import 'package:flutter/material.dart';

class AVTransform extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      home: new Scaffold(
        appBar: new AppBar(
          title: new Text('AVTransform'),
        ),
        body: new Center(
          child: new Transform(
            transform: Matrix4.rotationZ(0.8),
            child: new Container(
              width: 50.0,
              height: 50.0,
              color: Colors.blue,
            ),
          ),
        ),
      ),
    );
  }
}
```

Transform Widget 继承 SingleChildRenderObjectWidget 通过阅读它的构造方法，我们可以看到该怎么使用它的参数：

```dart
Transform({
  Key key,
  @required this.transform,
  this.origin,
  this.alignment,
  this.transformHitTests = true,
  Widget child,
})
```

transform 参数是一个 Matrix4 类型，它就是我们来控制图形变换的一个很重要的属性，alignment 是它的对齐方式，origin 参数是一个 Offset 类型，如果你不想使用最基础的 Transform Widget ，也可以使用它已经提供好的一些便捷构造方法，如：

- Transform.rotate
- Transform.translate
- Transform.scale

再来让我们看一个缩放的例子：

![](../images/flutter-66.png)

```dart
import 'package:flutter/material.dart';

class AVTransform extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      home: new Scaffold(
        appBar: new AppBar(
          title: new Text('AVTransform'),
        ),
        body: new Center(
          child: new Transform(
            transform: Matrix4.diagonal3Values(0.5, 0.5, 1),
            child: new Container(
              width: 50.0,
              height: 50.0,
              color: Colors.blue,
            ),
          ),
        ),
      ),
    );
  }
}
```

运行流畅细腻入微的动画效果对于一个应用程序来讲非常的重要，Transform Widget 可以完成你想要的，让 Widget 动起来。