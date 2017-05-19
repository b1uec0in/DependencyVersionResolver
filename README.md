# Dependency Version Resolver
resolves dependency versions of multiple project with shared setting.

## Features
### 1. Using same library versions in multiple project
Just remove version in dependency notation and declare once in <code>shared-settings.gradle</code>.

* file: app/build.gradle
  ```gradle
  ...
  dependencies {
      compileDefault dependencies

      compile 'com.android.support:design'
      compile 'com.android.support.constraint:constraint-layout'

      compile project(':mycustomwidget')
  }
  ...
  ```

