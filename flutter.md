# flutter

```bash
# create a project
flutter create $project

# clean deps
flutter clean

# get deps
flutter pub get

# run project
flutter run

# upgrade packages / fetch new master code from git source
flutter packages upgrade
```

# cool widgets

https://api.flutter.dev/flutter/material/DefaultTabController-class.html

# snippets

[Cookbook](https://docs.flutter.dev/cookbook)

## snackbar

```dart
// show
ScaffoldMessenger.of(context).showSnackBar(SnackBar(content: const Text('Foo')))

// hide
ScaffoldMessenger.of(context).hideCurrentSnackBar();
```

## unfocus

```dart
FocusScope.of(context).unfocus();
```

## generation

```dart
// generate 100 widgets
List.generate(100, (index) {
  return Text('Item $index');
}
```

## extension

```dart
// extend enum to be able to use foo.totext(), which converts the enum to a String
enum MyEnum {
  val1,
  val2,
  val3,
}

extension MyEnumExt on MyEnum {
  String toText() {
    String lower = describeEnum(this);

    return '${lower[0].toUpperCase()}${lower.substring(1)}';
  }
}
```

## spread operator

```dart
// you can use ... to unpack lists and maps
List<int> a = [1, 2, 3];
List<int> b = [...a, 4, 5, 6];
```

## misc

```dart
// asign value; default for null
bool foo = variable?? false;

// asign value depending on a condition
bool foo = condition ? cond_ok_value : cond_fail_value;
```

