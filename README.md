# Dependency Version Resolver
resolves dependency versions of multiple project with shared setting.

## Features
### 1. Using same library versions in multiple project.
Declare full dependency notatation once in <code>shared-settings.gradle</code> and just remove version.

* file: /shared-settings.gradle
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

* file: /shared-settings.gradle
```gradle
ext.defaultGroup = {
    compile fileTree(include: ['*.jar'], dir: 'libs')
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

+    compileDefault dependencies

    ...
}
```


### 3. Using shared SDK version settings.

* file: /shared-settings.gradle
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
1. copy <code>dependencies-resolver.gradle</code>, <code>shared-settings.gradle</code> files to project root directory.
2. modify <code>build.gradle</code> file in project root directory.

```diff
buildscript {
+    apply from: "dependencies-resolver.gradle"
+    apply from: "shared-settings.gradle"
+    resolveDependencyVersion buildscript:buildscript, strictMode:true

    repositories {
        jcenter()
    }

    dependencies {
-        classpath 'com.android.tools.build:gradle:2.3.2'
+        classpath 'com.android.tools.build:gradle'

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

allprojects {
+    resolveDependencyVersion project:project, strictMode:true

    repositories {
        jcenter()
    }
}
```

Set <code>strictMode:true</code> if you want to find dependency notation not declared in <code>shared-settings.gradle</code>. 
