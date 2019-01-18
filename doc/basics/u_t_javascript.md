## 为 JavaScript 开发人员准备的 Dart 参考教程

Dart 是 Flutter 主要的开发语言，这一篇文章主要为 JavaScript 开发人员准备的 Dart 教程，我会用很详细的对比来参考，力争 JavaScript 开发人员可以快速的熟练使用 Dart，为使用 Flutter 做好准备；

> 使用 es2015 做为参照；

示例可以使用 [https://dartpad.dartlang.org/](https://dartpad.dartlang.org/) 来运行；Dart 和 JavaScript 有非常重要的不同，Dart2 开始它变成了一个强类型的语言，JavaScript 开发人员可以类比你在使用 TypeScript 。

## 常量和变量

变量

*JavaScript*

```javascript
var c = 1;
c = 2;

let a = 1;
a = 2;
```

*Dart*

```dart
int a = 1;
a = 2;

var h = 1;
h = 2;
```

常量

*JavaScript*

```javascript
const b = 1;
```

*Dart*

```dart
final b = 1;
const ggg = 1;
```

`final` 和 `const` 唯一的区别是 final 可以接收一个变量但 const 不行，多数情况下我们会使用 final 来定义只赋值一次的值；

## 函数

定义函数

*JavaScript*

```javascript
function a(){}
const b = function(){}
```

*Dart*

```dart
void funcs(){}
final funcs = (){}
```

多数情况下 Dart 的函数和 JavaScript 的函数都有一样的特性，如：

- 将函数当做参数进行传递
- 将函数直接赋值给变量
- 对函数进行解构，只传递给函数一部分参数来调用它，让它返回一个函数去处理剩下的参数
- 创建一个可以被当作为常量的匿名函数

当你要使用一个非常简单的函数时，比如只返回一个字符串，它的表现形式和 JavaScript 的箭头函数非常的像；

*JavaScript*

```javascript

const d = () => 'dd';

```

*Dart*

```dart
String d() => 'dd';
```

实际上它可以等价为：

*JavaScript*

```javascript

const d = () => {
  return 'dd';
}

```

*Dart*

```dart
String d(){
  return 'dd';
}
```

当我们需要传递一个函数做为参数时：

*javascript*

```javascript

function a(){
  console.log('a');
}

function b(funcs){
  funcs()
}

b(a);

```

*Dart*

```javascript
String dd() => 'dd';

void PrintString(funcs){
  print(funcs());
}

PrintString(dd);

```

## 字符串模板

JavaScript 的模板和 Dart 一样，都是一个表达式；

*javascript*

```javascript

const d = 'icepy';

`hello ${d}`;

function dd(){
  return 'dd';
}

`hello ${dd()}`;

```

*Dart*

```dart
final d = 'icepy';

'hello $d';

String dd() => 'dd';

'hello ${dd()}';
```

## 模块导入和导出

JavaScript 使用 import 和 export 来导入导出模块，Dart 也使用了 import 来导入模块，只不过它和 JavaScript 有一个显著的区别，Dart 并不需要使用 export 来导出模块。

*javascript*

```javascript
import xxx from "xxx";
import * as xx from "xxx";
import { xx } from "xxx";
```

*Dart*

```dart
import 'package:xxx/xxx'; 

import 'package:xxx/xxx' show xxx; //导出其中一个对象

import 'package:xxx/xxx' hide yyy; //导出模块时不导出yyy

import 'package:xxx/xxx' as myxxx; //给导出的模块加上别名
```

## 类

为了更好的用语言来描述你的程序，类就是这样一个很好的媒介，与 JavaScript 非常一致的是 Dart 也使用 class 来定义一个类，使用 extends 来完成继承，与 JavaScript 不同的是 Dart 有更为丰富的功能；

构造函数

*JavaScript*

```javascript
class Icepy {
  constructor(a){
    this.a = a;
  }
}
```

*Dart*

```dart
class Icepy {
  int a;
  Icepy(this.a);
}
```

构造函数参数默认值

*JavaScript*

```javascript
class Icepy {
  constructor(a = 1){
    this.a = a;
  }
}
```

*Dart*

```dart
class Icepy {
  int a;
  Icepy({this.a = 1});
}

void main() {
  final i = new Icepy();
  print(i.a);
}
```

定义实例方法

*JavaScript*

```javascript
class Icepy {
  constructor(a=1){
    this.a = a;
  }

  say(){
    console.log(this.a);
  }
}
```

*Dart*

```dart
class Icepy {
  int a;
  Icepy({this.a = 1});
  
  void say(){
    print(this.a);
  }
}

void main() {
  final i = new Icepy();
  i.say();
}
```

定义静态方法

*JavaScript*

```javascript
class Icepy {
  static staticMethod() {
    return 'static method has been called.';
  }
}
```

*Dart*

```dart
class Icepy {
  int a;
  Icepy({this.a = 1});  
  Icepy.stt(){
    print('2');
  }
}

void main() {
  Icepy.stt();
}
```

getter&setter

*JavaScript*

```javascript
class Square{
  constructor(length) {
    this.name = 'Square';
  }
  get area() {
    return 2*2;
  }
  set area(value) {
    this._area = value;
  }
}
```

*Dart*

```dart
class Rectangle {
  num left, top, width, height;
  Rectangle(this.left, this.top, this.width, this.height);
  num get right => left + width;
  set right(num value) => left = value - width;
  num get bottom => top + height;
  set bottom(num value) => top = value - height;
}

void main() {
  var rect = Rectangle(3, 4, 20, 15);
  print(rect.left == 3);
  rect.right = 12;
  print(rect.left == -8);
}

```

重写

*JavaScript*

```javascript

class B {
 say(){
 	console.log(1)
 }
}


class A extends B {
 say(){
  console.log(2)
 }
}

new A().say();
```

*Dart*

```dart

class ICe {
  void say(){
    print(2);
  }
}

class ICCC extends ICe {
  @override
  say(){
    print(1);
  }
}

void main(){
  new ICCC().say();
}

```

当然 JavaScript 也有很多它比较独特的特性，这就不一一列举了，更多的 Dart 特性需要你在使用的过程中慢慢学习了，比如：

- 抽象方法
- 抽象类
- 隐式接口
- ...

在我们使用 Flutter 的过程中 @override 应该是我们使用的最多的特性之一；

