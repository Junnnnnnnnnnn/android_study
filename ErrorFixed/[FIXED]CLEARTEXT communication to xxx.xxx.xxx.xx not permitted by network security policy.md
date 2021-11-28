# CLEARTEXT communication to xxx.xxx.xxx.xx not permitted by network security policy

## 발생

```
java.lang.RuntimeException- java.net.UnknownServiceException- CLEARTEXT communication to xxx.xxx.xxx.xx  not permitted by network security policy
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

`Retrofit2` 라이브러리를 사용할 때 `api` 주소가 ssh 설정이 안되있다면 `android` 에서 따로 설정을 해야 합니다

예를들면 `https://name.com` 이 아닌 `http://name.com` 일 때 주로 발생 하는 오류 입니다.

## 픽스

> AndroidManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.gochigo.asis">
  							.
  							.
  							.
    <uses-permission android:name="android.permission.INTERNET"/>
								.
  							.
  							.

    <application
								 .
                 .
                 .
    </application>

</manifest>
```

먼저 네트워크를 사용해야 함으로 `android.permission.INTERNET` 을 퍼미션 등록을 합니다.

다음으로는 res -> xml 폴더에 파일을 하나 등록 해 줍니다.

> network_security_config.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config xmlns:android="http://schemas.android.com/apk/res/android">
    <domain-config cleartextTrafficPermitted="true">
        <domain includeSubdomains="true">name.com</domain>
    </domain-config>
</network-security-config>
```

위 코드는 http로 접속 가능한 ip를 등록해주는 파일입니다 위코드 처럼 작성해주시고 컴파일 시켜 주면 정상 작동이 됩니다~ 