# OkBuck
[ ![Download](https://api.bintray.com/packages/okbuilds/maven/OkBuild/images/download.svg) ](https://bintray.com/okbuilds/maven/OkBuild/_latestVersion)
[![Master branch build status](https://travis-ci.org/OkBuilds/OkBuck.svg?branch=master)](https://travis-ci.org/OkBuilds/OkBuck)
[![Android Arsenal](https://img.shields.io/badge/Android%20Arsenal-OkBuck-green.svg?style=flat)](https://android-arsenal.com/details/1/2593)

OkBuck is a gradle plugin, aiming to help developers utilize the fast build 
system: [Buck](https://buckbuild.com/), based on an existing project built using gradle. This plugin lets you have both build systems work side by side.

[Wiki](https://github.com/OkBuilds/OkBuck/wiki), 
[中文版](https://github.com/OkBuilds/OkBuck/blob/master/README-zh.md)

## Why?
Gradle is typically the default build tool for android development, and 
to migrate to buck, there is a lot of overhead, which can be difficult and 
buggy. OkBuck automates this migration with very few lines of configuration.

## Installation
In root project `build.gradle` file:

```gradle
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.github.okbuilds:okbuild-gradle-plugin:2.0.0-alpha1'
    }
}

apply plugin: 'com.github.okbuilds.okbuck-gradle-plugin'
```

That's all for basic case, no need for any other configuration. After appling the plugin, two gradle tasks are added `okbuck` and `okbuckClean`
  +  `okbuck` will generate BUCK files
  +  `okbuckClean` will **delete all** files/dirs generated by OkBuck and BUCK

You can type `buck targets` to get a list of targets that can be build. The generated `.buckconfig` file will have some aiases setup to build your apps without having to type the rulename. i.e you can do things like `buck build appDebug another-appPaidRelease` etc.

## Configuration
```gradle
okbuck {
    buildToolVersion "23.0.3"
    target "android-23"
    overwrite true
    linearAllocHardLimit = [
            app: 7194304
    ]
    primaryDexPatterns = [
            app: [
                    '^com/github/okbuilds/okbuck/example/AppShell^',
                    '^com/github/okbuilds/okbuck/example/BuildConfig^',
                    '^android/support/multidex/',
                    '^com/facebook/buck/android/support/exopackage/',
                    '^com/github/promeg/xlog_android/lib/XLogConfig^',
                    '^com/squareup/leakcanary/LeakCanary^',
            ]
    ]
    exopackage = [
            app: true
    ]
    appLibDependencies = [
            app: [
                    'buck-android-support',
                    'multidex',
                    'javalibrary',
            ]
    ]
    buckProjects = project.subprojects
}
```

+  `buildToolVersion` specifies the version of the Android SDK Build-tools, defaults to `23.0.3`
+  `target` specifies the Android compile sdk version, default is `android-23`
+  `overwrite` is used to control whether to overwrite existing BUCK files when running the `okbuck` task, default is `false`
+  `linearAllocHardLimit` and `primaryDexPatterns` are maps, configuration used by buck for multidex. For more details about multidex configuration, please read the
[Multidex wiki page](https://github.com/OkBuilds/OkBuck/wiki/Multidex-Configuration-Guide), 
if you don't need multidex, you can ignore these parameters
+  `exopackage`, `appClassSource` and `appLibDependencies` are used for 
configuring buck's exopackage mode. For more details about exopackage configuration, 
please read the [Exopackage wiki page](https://github.com/OkBuilds/OkBuck/wiki/Exopackage-Configuration-Guide), if you don't need exopackage, you can ignore these parameters
+  `buckProjects` is a set of projects to generate buck configs for. Default is all sub projects of the root project.

## Common Issues
+ If you use ndk filters in your build.grade, you must set the `ANDROID_NDK` environment variable pointing to your local android ndk root dir, otherwise BUCK build will fail.
+ If your project uses gradle 2.4, youneed force jdk version to 1.7, [ref1](http://stackoverflow.com/a/21212790/3077508) 
and [ref2](http://stackoverflow.com/a/18144853/3077508)
+ OkBuck aims to provide almost all the features that the android gradle plugin provides,but your project may still be incompatible with buck for various reasons listed on the [Known caveats wiki page](https://github.com/OkBuilds/OkBuck/wiki/Known-caveats). 

## Compatibility
OkBuck is tested under `gradle` 2.2.1 ~ 2.13, and `com.android.tools.build:gradle` 1.5.0 ~ 2.1.0.
If other versions have any compatibility problems, please file an issue.

## [Full Change log](https://github.com/OkBuilds/OkBuck/blob/master/CHANGELOG.md)

## Liscense
```
The MIT License (MIT)

Copyright (c) 2015 Piasy

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
