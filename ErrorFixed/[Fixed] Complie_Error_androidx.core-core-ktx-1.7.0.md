# The minCompileSdk (31) specified in adependency's AAR metadata is greater than this module's compileSdkVersion (android-30).Dependency: androidx.core:core-ktx:1.7.0.

## 발생

```
The minCompileSdk (31) specified in a
dependency's AAR metadata (META-INF/com/android/build/gradle/aar-metadata.properties)
is greater than this module's compileSdkVersion (android-30).
Dependency: androidx.core:core-ktx:1.7.0.
```

## 환경

```groovy
defaultConfig {
  applicationId "com.your.application"
  minSdk 24
  targetSdk 30
  versionCode 1
  versionName "1.0"
}

dependencies {
    implementation 'androidx.core:core-ktx:1.7.0'
  	implementation 'androidx.appcompat:appcompat:1.4.0'
}
```

## 원인

프로젝트를 처음 실행을 시키면 기본으로 다음과 같이 설정이 된다. 하지만 자신이 `sdk 30` 버전을 사용한다면 에러가 날 것이다. 

`androidx.core:core-ktx:1.7.0`과 `'androidx.appcompat:appcompat:1.4.0'` 를 사용해야 하는데 해당 버전은 sdk 31버전의 `dependency`이다. 이것 보다 한단계 낮은 버전을 사용하면 버그는 사라진다.

> 즉 현재 sdk가 31 버전 까지 나왔지만 본인은 sdk를 30버전을 사용을 함으로써 발생되는 버그



### 픽스

```groovy
defaultConfig {
  applicationId "com.your.application"
  minSdk 24
  targetSdk 30
  versionCode 1
  versionName "1.0"
}

dependencies {
    //implementation 'androidx.core:core-ktx:1.7.0'
    implementation 'androidx.core:core-ktx:1.6.0'
    //implementation 'androidx.appcompat:appcompat:1.4.0'
    implementation 'androidx.appcompat:appcompat:1.3.0'
}
```

