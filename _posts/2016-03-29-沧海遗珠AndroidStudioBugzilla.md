---
layout: post
title: AndroidStudio Bugzilla
categories: [沧海遗珠]
tags: [android, 记录]
---
当app gradle设置如下时

```
useLibrary 'org.apache.http.legacy'
    compileSdkVersion 23
    buildToolsVersion "23.0.1"
    defaultConfig {
        applicationId "com.dqqdo.home"
        minSdkVersion 14
        targetSdkVersion 22
        versionCode 1
        versionName "1.0"
        multiDexEnabled true
    }
```
且project gradle如下时

```
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.1.0-alpha4'

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

allprojects {
    repositories {
        jcenter()
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}

```
在Android5.0以上运行一切正常，5.0以下报如下错误，启动崩溃。


```
 E/AndroidRuntime: FATAL EXCEPTION: main
                                                 Process: com.dqqdo.home, PID: 3337
                                                 java.lang.RuntimeException: Unable to instantiate application com.android.tools.fd.runtime.BootstrapApplication: java.lang.ClassNotFoundException: Didn't find class "com.android.tools.fd.runtime.BootstrapApplication" on path: DexPathList[[zip file "/data/app/com.dqqdo.home-1.apk"],nativeLibraryDirectories=[/data/app-lib/com.dqqdo.home-1, /vendor/lib, /data/datalib, /system/lib]]
                                                     at android.app.LoadedApk.makeApplication(LoadedApk.java:537)
                                                     at android.app.ActivityThread.handleBindApplication(ActivityThread.java:4562)
                                                     at android.app.ActivityThread.access$1800(ActivityThread.java:141)
                                                     at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1302)
                                                     at android.os.Handler.dispatchMessage(Handler.java:102)
                                                     at android.os.Looper.loop(Looper.java:136)
                                                     at android.app.ActivityThread.main(ActivityThread.java:5290)
                                                     at java.lang.reflect.Method.invokeNative(Native Method)
                                                     at java.lang.reflect.Method.invoke(Method.java:515)
                                                     at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:859)
                                                     at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:675)
                                                     at dalvik.system.NativeStart.main(Native Method)
                                                  Caused by: java.lang.ClassNotFoundException: Didn't find class "com.android.tools.fd.runtime.BootstrapApplication" on path: DexPathList[[zip file "/data/app/com.dqqdo.home-1.apk"],nativeLibraryDirectories=[/data/app-lib/com.dqqdo.home-1, /vendor/lib, /data/datalib, /system/lib]]
                                                     at dalvik.system.BaseDexClassLoader.findClass(BaseDexClassLoader.java:56)
                                                     at java.lang.ClassLoader.loadClass(ClassLoader.java:497)
                                                     at java.lang.ClassLoader.loadClass(ClassLoader.java:457)
                                                     at android.app.Instrumentation.newApplication(Instrumentation.java:976)
                                                     at android.app.LoadedApk.makeApplication(LoadedApk.java:532)
                                                     at android.app.ActivityThread.handleBindApplication(ActivityThread.java:4562) 
                                                     at android.app.ActivityThread.access$1800(ActivityThread.java:141) 
                                                     at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1302) 
                                                     at android.os.Handler.dispatchMessage(Handler.java:102) 
                                                     at android.os.Looper.loop(Looper.java:136) 
                                                     at android.app.ActivityThread.main(ActivityThread.java:5290) 
                                                     at java.lang.reflect.Method.invokeNative(Native Method) 
                                                     at java.lang.reflect.Method.invoke(Method.java:515) 
                                                     at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:859) 
                                                     at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:675) 
                                                     at dalvik.system.NativeStart.main(Native Method) 
```

Google Group关于这个的讨论，[https://code.google.com/p/android/issues/detail?id=199327](https://code.google.com/p/android/issues/detail?id=199327)
报错原因应该是gradle工具的问题，但是我的版本已经不是2.0.0-alphaxx

```
defaultConfig {
        applicationId "com.dqqdo.home"
        minSdkVersion 14
        targetSdkVersion 22
        versionCode 1
        versionName "1.0"
        multiDexEnabled true
    }
```
最后暂时改为targetSdkVersion 22。