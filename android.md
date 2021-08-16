# bytecode

[https://source.android.com/devices/tech/dalvik/dalvik-bytecode#instructions](Link)

## basic steps to modify an app

### requirements

- apktool
- apksigner
- zipalign
- adb
- jadx-gui

### get the apk

You can use `adb` or the `aurora` playstore to downlaod the app to your computer.

```shell
# get the device
adb devices -l

# list packages
adb -s $device shell pm list packages

# list the app path
adb -s $device shell pm path com.example.foo

# download the apk
adb -s $device pull /data/app/com.foo.apk path/to/desired/destination
```

SOMETIMES the application is a so called `bundle`. You should find multiple apks there... (named like
`config.foo.apk`. Download them all and sign the config apks with your key (keep reading).

### decompile the app

```shell
# the -r will prevent the decompilation of the resources; you wont need that
apktool d -r foo.apk
```

### explore the app

You can use `jadx-gui` to explore the application.

### modify the code

Come up with a modification and implement it in the smali code (the stuff you got from apktool).

### java to smali

Use this script to convert java to smali:

```shell
#!/bin/bash

java_file="$1"
java_no_ext="${java_file%.java}"
class_file="${java_no_ext}.class"
dex_file="${java_no_ext}.dex"
smali_file="${java_no_ext}.smali"

javac "$java_file"
dx --dex --output="$dex_file" "$class_file"
baksmali d -o . "$dex_file"
cat "$smali_file"
```

#### examples

```smali
// register counter
.locals 5

// invoke the name function
invoke-virtual {p0}, Ljava/lang/Enum;->name()Ljava/lang/String;

// save the result in v3
move-result-object v3

// add a string
const-string v4, "SpecialCase"

// compare the previous result with the string "SpecialCase"
// if not equal (result is not our specialcase) jump to cond_4
if-ne v3, v4, :cond_4

// if its equal, save 0x1 (true) in v1
const/4 v1, 0x1

:cond_4

// return what ever v1 is
return v1
```

Can you just add `registers` as you wish? No. In the beginning of the method you have so specify the
number of registers you used. But why are there 5 registers in this example? You forgot about
Dre..em `this`, which always be `v0` in non-static-methods.

### compile it again

```shell
# foo is the directory
apktool b foo
```

### zipalign it

```shell
zipalign -v 4 foo/dist/foo.apk foo-modified.apk
```

### apksign it

```shell
# create a key; remember the password
keytool -genkey -v -keystore my-release-key.keystore -alias alias_name -keyalg RSA -keysize 2048 -validity 10000

# sign the app
apksigner sign --ks my-release-key.keystore foo-modified.apk
```

### upload it

```shell
# single apk
adb -s $device install $apk

# in case of bundle apk
adb -s $device multi-install $apk1 $apk2
```
