## 关于 Flutter 本地存储的一些事

对于本地存储而言，可能前端的同学就要失望了，本身 Flutter 并未提供像 `localStorage` 这样的 API 来处理数据的本地存储，你需要依赖 iOS 或 Android 本身平台给你提供的本地存储方案，实现一个插件提供给 Flutter 应用来使用。

比如 [https://flutter.io/docs/get-started/flutter-for/ios-devs#databases-and-local-storage](https://flutter.io/docs/get-started/flutter-for/ios-devs#databases-and-local-storage) 这一小节中给大家详细说明了一些状况。如果在 iOS 中你想使用 UserDefaults 来存储一些数据（这是一个键值对的集合），那么你就要使用 [https://pub.dartlang.org/packages/shared_preferences](https://pub.dartlang.org/packages/shared_preferences) 这个插件，它封装了 iOS 中的 UserDefaults 和 Android 的 SharedPreferences。

让我们来看一看 shared_preferences 是如何使用的：

```dart
import 'package:flutter/material.dart';
import 'package:shared_preferences/shared_preferences.dart';

void main() {
  runApp(MaterialApp(
    home: Scaffold(
      body: Center(
      child: RaisedButton(
        onPressed: _incrementCounter,
        child: Text('Increment Counter'),
        ),
      ),
    ),
  ));
}

_incrementCounter() async {
  SharedPreferences prefs = await SharedPreferences.getInstance();
  int counter = (prefs.getInt('counter') ?? 0) + 1;
  print('Pressed $counter times.');
  await prefs.setInt('counter', counter);
}
```

例子可以让我们看到通过 SharedPreferences.getInstance() 来获取存取器，然后调用 setInt 方法来存储键为 counter 的值。

另外对于 iOS 开发者来说如果你想使用 CoreData 来存储结构化的数据，Flutter 给大家推荐的是使用 SQFlite [https://pub.dartlang.org/packages/sqflite](https://pub.dartlang.org/packages/sqflite) ，这也是一个已经封装完成支持 iOS 和 Android 平台的插件，因此 Android 的同学需要存储结构化的数据时应该也要考虑它。

```dart
import 'package:sqflite/sqflite.dart';
```

让我们来看一个例子：

```dart
var databasesPath = await getDatabasesPath();
String path = join(databasesPath, 'demo.db');

// Delete the database
await deleteDatabase(path);

// open the database
Database database = await openDatabase(path, version: 1,
    onCreate: (Database db, int version) async {
  // When creating the db, create the table
  await db.execute(
      'CREATE TABLE Test (id INTEGER PRIMARY KEY, name TEXT, value INTEGER, num REAL)');
});

// Insert some records in a transaction
await database.transaction((txn) async {
  int id1 = await txn.rawInsert(
      'INSERT INTO Test(name, value, num) VALUES("some name", 1234, 456.789)');
  print('inserted1: $id1');
  int id2 = await txn.rawInsert(
      'INSERT INTO Test(name, value, num) VALUES(?, ?, ?)',
      ['another name', 12345678, 3.1416]);
  print('inserted2: $id2');
});

// Update some record
int count = await database.rawUpdate(
    'UPDATE Test SET name = ?, VALUE = ? WHERE name = ?',
    ['updated name', '9876', 'some name']);
print('updated: $count');

// Get the records
List<Map> list = await database.rawQuery('SELECT * FROM Test');
List<Map> expectedList = [
  {'name': 'updated name', 'id': 1, 'value': 9876, 'num': 456.789},
  {'name': 'another name', 'id': 2, 'value': 12345678, 'num': 3.1416}
];
print(list);
print(expectedList);
assert(const DeepCollectionEquality().equals(list, expectedList));

// Count the records
count = Sqflite
    .firstIntValue(await database.rawQuery('SELECT COUNT(*) FROM Test'));
assert(count == 2);

// Delete a record
count = await database
    .rawDelete('DELETE FROM Test WHERE name = ?', ['another name']);
assert(count == 1);

// Close the database
await database.close();
```

其实这就是在写 SQL 语句。