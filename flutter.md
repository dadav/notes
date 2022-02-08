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

## misc

```dart
// asign value; default for null
bool foo = variable?? false;

// asign value depending on a condition
bool foo = condition ? cond_ok_value : cond_fail_value;
```

