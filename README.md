# Dependency Version Resolver
resolves dependency versions of multiple project.

## Features
### 1. Using same library versions in multiple project.
Declare full dependency notation once in **root project** <code>build.gradle</code> and just remove versions in subproject.

* file: /build.gradle
```diff
def versions = [
        support:"25.3.1",
        retrofit:"2.1.0",
        okhttp:"3.4.1",
]

ext.libraries = [
        "com.android.support:appcompat-v7:$versions.support",
        "com.android.support:support-v4:$versions.support",

        "com.squareup.retrofit2:retrofit:$versions.retrofit",
        "com.squareup.retrofit2:converter-gson:$versions.retrofit",
        "com.squareup.okhttp3:okhttp:3.4.1",
]
```
  
* file: app/build.gradle, subproject1/build.gradle, subproject2/build.gradle, ...
```diff
...
dependencies {
-    compile 'com.android.support:appcompat-v7:25.3.1'
-    compile 'com.android.support:support-v4:25.3.1'
-    compile 'com.squareup.retrofit2:retrofit:2.1.0'
-    compile 'com.squareup.retrofit2:converter-gson:2.1.0'
-    compile 'com.squareup.okhttp3:okhttp:3.4.1'

+    compile 'com.android.support:appcompat-v7'
+    compile 'com.android.support:support-v4'
+    compile 'com.squareup.retrofit2:retrofit'
+    compile 'com.squareup.retrofit2:converter-gson'
+    compile 'com.squareup.okhttp3:okhttp'
    ...
}
  ```
### 2. Using default dependency group.
Group multiple dependencies to single notation.

* file: /build.gradle
```gradle
ext.defaultGroup = {project->
    compile project.fileTree(include: ['*.jar'], dir: 'libs')
    androidTestCompile('com.android.support.test.espresso:espresso-core', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
    compile 'com.android.support:appcompat-v7'
    testCompile 'junit:junit'
}
```
  

* file: app/build.gradle, subproject1/build.gradle, subproject2/build.gradle, ...
```diff
...
dependencies {
-    compile fileTree(include: ['*.jar'], dir: 'libs')
-    androidTestCompile('com.android.support.test.espresso:espresso-core', {
-        exclude group: 'com.android.support', module: 'support-annotations'
-    })
-    compile 'com.android.support:appcompat-v7'
-    testCompile 'junit:junit'

+    compileDefault project

    ...
}
```


### 3. Using shared SDK version settings.

* file: /build.gradle
  ```gradle
  ext.shared = [
          buildToolVersion : "25.0.2",
          compileSdkVersion: 25,
          minSdkVersion    : 15,
          targetSdkVersion : 25,
  ]
  ```
* file: app/build.gradle, subproject1/build.gradle, subproject2/build.gradle, ...
```diff
...
android {
-    compileSdkVersion 25
-    buildToolsVersion "25.0.2"
+    compileSdkVersion shared.compileSdkVersion
+    buildToolsVersion shared.buildToolVersion

    defaultConfig {
        applicationId "com.github.b1uec0in.dependencyversionresolver"
-        minSdkVersion 15
-        targetSdkVersion 25
+        minSdkVersion shared.minSdkVersion
+        targetSdkVersion shared.targetSdkVersion
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
 ...
```
  
## Usage:  
1. copy <code>dependencies-resolver.gradle</code> file to your project root directory.
1. open <code>build.gradle</code> file in project root directory.
1. copy and edit (see above sample code of each feature)
1. add <code>dependency resolver initializing</code> codes. (see below)
```diff
// sdk versions
ext.shared = [
    buildToolVersion : "25.0.2",
    compileSdkVersion: 25,
    minSdkVersion    : 15,
    targetSdkVersion : 25,
]

// library versions
def versions = [
    support:"25.3.1",
]
ext.libraries = [
    "com.android.support:appcompat-v7:$versions.support",
    "com.android.support:support-v4:$versions.support",
    // TODO: Add dependencies (with version) used by any sub project.
]

// default dependencies
ext.defaultGroup = { project ->
    compile project.fileTree(include: ['*.jar'], dir: 'libs')
    // TODO: Add dependencies (without version) used by all sub project.
}

// dependency resolver initializing
+ apply from: "dependencies-resolver.gradle"
+ subprojects {
+    resolveDependencyVersion project:project, strictMode:false
+ }


buildscript {
    ...
}

allprojects {
    ...
}
...
```

* Set <code>strictMode:true</code> to allow only dependency notation <b>without</b> version.
(Error occurs when using dependency notation with version or not declared in <code>shared-settings.gradle</code>.)
* Set <code>strictMode:false</code>(or without parameter) to disable check.

## Sample
See this project sources. (https://github.com/b1uec0in/DependencyVersionResolver)
