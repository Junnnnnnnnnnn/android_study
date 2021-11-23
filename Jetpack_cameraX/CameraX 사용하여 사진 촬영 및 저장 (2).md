# [Android] CameraX 사용하여 사진 촬영 및 저장 (2)

## 들어가며

이전 포스팅은 CameraX 라이브러리를 이용해 카메라 셋팅 하는 것 까지 다뤄 봤습니다 오늘은 파일 저장에 대한 전체적인 코드 설명을 하겠습니다. 

본 포스팅 내용은 CameraX에 대한 기본 설명 코드 설명이 주 된 포스팅으로 아래의 깃허브 주소를 통해 코드와 같이 본다면 이해하기 쉬울 거예요
https://github.com/Junnnnnnnnnnn/demo_android_cameraX

## 사진 촬영 및 저장

사진을 촬영하기 위해선 기본적으로 카메라 퍼미션을 허용 해줘야 합니다.

## Camera permission

### AndroidManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.yotdark.example_camerax">
      											.
  													.
  													.
    <uses-feature android:name="android.hardware.camera.any" />
    <uses-permission android:name="android.permission.CAMERA" />
    												.
  													.
  													.

</manifest>
```

`android.hardware.camera.any`와 `android.permission.CAMERA` 를 `Manifast` 에 등록 합니다.

### CameraActivity

```kotlin
private fun allPermissionsGranted() = REQUIRED_PERMISSIONS.all {
		ContextCompat.checkSelfPermission(baseContext, it) == PackageManager.PERMISSION_GRANTED
}
```

`AndroidManifest`에 등록된 권한이 허용됬는지 확인하는 메서드 입니다. 
권한이 있다면 1 없다면 0을 반환합니다.

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.camera)
    												.
  													.
  													.
  
    if(allPermissionsGranted()){
        startCamera()
    }else{
        ActivityCompat.requestPermissions(
            this@CameraActivity,
            REQUIRED_PERMISSIONS,
            REQUEST_CODE_PERMISSIONS
        )
    }
      											.
  													.
  													.
}
```

권한이 있는지 없는지 확인 후 없다면 `requestPermissionse` 를 통해 권한 허용 팝업창을 띄워 줍니다.

>  위 코드를 통해 권한을 허용 했다면 사진이 저장될 곳을 지정 해야 합니다. 

## getExternalFilesDir

### AndroidManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.yotdark.example_camerax">
          									.
  													.
  													.
        <provider
            android:name="androidx.core.content.FileProvider"
            android:authorities="com.yotdark.example_camerax"
            android:exported="false"
            android:grantUriPermissions="true">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/file_paths" />
        </provider>
    </application>

</manifest>
```

이미지가 저장될 경로를 저장한 `xml`파일을 `FILE_PROVIDER_PATHS` 에 지정 합니다.

### xml/file_paths

```xml
<?xml version="1.0" encoding="utf-8"?>
<paths>
    <external-path name="my_images"
                   path="Android/data/com.yotdark.example_camerax/files/Pictures" />
</paths>
```

사용자 기기내의 외부 저장소의 경로를 설정하는 파일입니다. 경로는 개발자가 설정하기 나름입니다!

[경로 보는 방법 사진]

### CameraActivity

```kotlin
private fun getOutputDirectory(): File {
		return getExternalFilesDir(Environment.DIRECTORY_PICTURES)!!
}
```

경로를 설정했다면 코드를 통해 설정한 경로를 불러 와야 합니다.
`getExternalFilesDir`과 인수인 `Environment.DIRECTORY_PICTURES`를 설정해줍니다.



이제 설정을 다 했습니다! 사진을 촬영한 후 외부 저장소 경로로 이미지를 저장 시키면 됩니다

## TakePhoto

```kotlin
private fun takePhoto(){
    val imageCapture = imageCapture ?: return
    val photoFile = File(
        outputDirectory,
        SimpleDateFormat(FILENAME_FORMAT, Locale.KOREA)
            .format(System.currentTimeMillis()) + ".$extension"
    )

    val outputOptions = ImageCapture.OutputFileOptions.Builder(photoFile).build()

    imageCapture.takePicture(
        outputOptions,
        ContextCompat.getMainExecutor(this@CameraActivity),
        object: ImageCapture.OnImageSavedCallback{
            override fun onImageSaved(outputFileResults: ImageCapture.OutputFileResults) {
                val saveUri = Uri.fromFile(photoFile)
                ExifInterface(photoFile.absolutePath).run{
                  getAttributeInt(
                    ExifInterface.TAG_ORIENTATION,
                    ExifInterface.ORIENTATION_NORMAL).run {
                        Log.d(TAG,"$this")
                    }
                }

                Toast.makeText(this@CameraActivity,"저장되었습니다",Toast.LENGTH_SHORT).show()
                Log.d(TAG,"사진이 정상 촬영 됬습니다. ${saveUri.path}")

                val intent = Intent(this@CameraActivity, MainActivity::class.java)
                intent.putExtra("path", saveUri.path)
                setResult(200,intent)
                finish()
            }
            override fun onError(exception: ImageCaptureException) {
                Log.e(TAG,"촬영에 실패 했습니다",exception)
            }
        }
    )
}

```

코드가 되게 길죠..ㅎㅎ 하나식 분석하면 별 것 아니예요!!

### imageCapture

```kotlin
val imageCapture = imageCapture ?: return
```

전역으로 선언한 `ImageCapture` 변수가 `null` 이라면 함수는 종료 됩니다. 예외처리라고 할 수 있죠

### PhotoFile

```kotlin
val photoFile = File(
    outputDirectory,
    SimpleDateFormat(FILENAME_FORMAT, Locale.KOREA)
       .format(System.currentTimeMillis()) + ".$extension"
)
```

imageCapture 변수를 통해 사진을 등록하려면 photoFile 이 필요합니다.
File의 첫번째 인수 `outputDirectory`는 위 함수에서 가져온 외부 저장소 경로를 입력합니다.
두번재 인수로는 파일 이름을 지정 해 줍니다. 파일의 중복을 처리하기 위해 `System.currentTimeMillis()` 를 사용했습니다.
`extension`으로는 `jpg`, `png` 등이 작성됩니다.

### OutputCapture

```kotlin
val outputOptions = ImageCapture.OutputFileOptions.Builder(photoFile).build()
```

 이미지 경로를 지정했으면 `ImageCapture`에 설정을 합니다.

### ImageCapture.takePicture

```kotlin
imageCapture.takePicture(
    outputOptions,
    ContextCompat.getMainExecutor(this@CameraActivity),
    object: ImageCapture.OnImageSavedCallback{
        override fun onImageSaved(outputFileResults: ImageCapture.OutputFileResults) {
            val saveUri = Uri.fromFile(photoFile)
            ExifInterface(photoFile.absolutePath).run {
                getAttributeInt(
                    ExifInterface.TAG_ORIENTATION,
                    ExifInterface.ORIENTATION_NORMAL
                ).run {
                    Log.d(TAG, "$this")
                }
            }

            Toast.makeText(this@CameraActivity, "저장되었습니다", Toast.LENGTH_SHORT).show()
            Log.d(TAG, "사진이 정상 촬영 됬습니다. ${saveUri.path}")

            val intent = Intent(this@CameraActivity, MainActivity::class.java)
            intent.putExtra("path", saveUri.path)
            setResult(200, intent)
            finish()

        }
        override fun onError(exception: ImageCaptureException) {
            Log.e(TAG,"촬영에 실패 했습니다",exception)
        }
    }
)

```

일단 첫 번째 인수로 `outputOptions`를 입력하고 두번째 인수는 `ContextCompat.getMainExecutor(this@CameraActivity)`  로 스레드를 등록합니다. 

세번째 인수로는 `ImageCapture.OnImageSavedCallback`이라는 콜백 메서드를 등록합니다

> ### 콜백 메서드란
>
> 개발자가 따로 함수를 등록하지 않았지만 함수 스스로 어떤 특정한 이벤트가 발생 했을 때 자신을 호출한 함수 또는 사용자가 지정한 곳으로 돌아가는 현상을 말합니다.

그럼 `onImageSaved` 함수를 상속 받게 되는데 이 함수를 호출하면 앞서 설정한 `outputOptions`를 통해 이미지가 저장이 됩니다.

```kotlin
ExifInterface(photoFile.absolutePath).run {
                getAttributeInt(
                    ExifInterface.TAG_ORIENTATION,
                    ExifInterface.ORIENTATION_NORMAL
                ).run {
                    Log.d(TAG, "$this")
                }
            }
```

저장된 사진의 가로 세로 등 메타값을 알기 위해 `ExifInterface`를 사용했습니다.
만약 사용자가 화면을 `portrait` 로 설정을 했다면 가로 세로를 자동으로 구분 할 수 가 없어 따로 `Surface` 를 등록해야 합니다.
(이건 따로 포스팅 하겠습니다.)

```kotlin
val intent = Intent(this@CameraActivity, MainActivity::class.java)
intent.putExtra("path", saveUri.path)
setResult(200, intent)
finish()
```

마지막으로 인텐트를 통해 값을 해당 엑티비티를 호출한 곳으로 전달하는 과정입니다.





이렇게 전반적인 사진 저장에 대해 알아 보았습니다!
다음 포스팅에는 사용자가 세로 고정으로 사진을 촬영했을 때 가로 세로를 확인 하는 방법을 마지막으로 살펴보도록 하겠습니다.

