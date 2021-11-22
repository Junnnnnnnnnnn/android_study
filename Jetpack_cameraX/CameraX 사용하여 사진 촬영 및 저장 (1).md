## [Android] CameraX 사용하여 사진 촬영 및 저장 (1) 

## 들어가며

안드로이드를 사용하면서 개발을 좀 더 효율적으로 하기위해선 다양한 방법이 있습니다 그중 CameraX 를 통한 사진 촬영에 대해 알아 보겠습니다.

본 포스팅 내용은 CameraX에 대한 기본 설명 코드 설명이 주 된 포스팅으로 아래의 깃허브 주소를 통해 코드와 같이 본다면 이해하기 쉬울 거예요

https://github.com/Junnnnnnnnnnn/demo_android_cameraX

## CameraX 란

CameraX는 카메라 앱 개발을 더 쉽게 할 수 있도록 만들어진 Jetpack 지원 라이브러리 입니다.
CameraX는 기존 camera2의 기능을 활용하면서 수명주기를 인식하고 사용 사례에 기반하는 더 간단한 접근 방식을 따릅니다.
CameraX는 기기 호환성 문제를 해결하므로 개발자가 기기별 코드를 포함할 필요가 없습니다.

## **CameraX의 기기범위** 

CameraX는 Android 5.0 이상을 실행하는 기기를 지원하도록 설계되었습니다

## **CameraX의 주 기능**

- 미리보기
  - 실시간으로 화면에서 이미지를 가져옵니다.
- 이미지 분석
  - 알고리즘에 사용할 수 있도록 버퍼에 원활하게 엑세스 할 수 있습니다
- 이미지 / 동영상 캡쳐
  - 이미지 및 동영상을 캡쳐 할 수 있습니다.



이렇게 CameraX의 기본 정의를 알아 보았고 이제 API를 사용하는 방법을 다뤄 보겠습니다.



## Dependencies

```kotlin
dependencies {
    // CameraX core library using the camera2 implementation
    val camerax_version = "1.0.2"
    // The following line is optional, as the core library is included indirectly by camera-camera2
    implementation("androidx.camera:camera-core:${camerax_version}")
    implementation("androidx.camera:camera-camera2:${camerax_version}")
    // If you want to additionally use the CameraX Lifecycle library
    implementation("androidx.camera:camera-lifecycle:${camerax_version}")
    // If you want to additionally use the CameraX View class
    implementation("androidx.camera:camera-view:1.0.0-alpha31")
    // If you want to additionally use the CameraX Extensions library
    implementation("androidx.camera:camera-extensions:1.0.0-alpha31")
}
```



## **API 사용 예**

CameraX는 onResume, onPause에 따로 호출을 하지 않고 `cameraProvider.bindToLifeCycle()`을 통해 카메라와 연결할 수명 주기를 자동으로 지정해 줍니다. 

예를 들면

```kotlin
private fun startCamera(){
    val cameraProviderFuture = ProcessCameraProvider.getInstance(this@CameraActivity)
    cameraProviderFuture.addListener({
        val cameraProvider: ProcessCameraProvider = cameraProviderFuture.get()

        imageAnalysis = ImageAnalysis.Builder()
            .setBackpressureStrategy(ImageAnalysis.STRATEGY_KEEP_ONLY_LATEST)
            .build().apply {
                setAnalyzer(cameraExecutor, { image ->
                    val rotationDegrees = image.imageInfo.rotationDegrees
                    // insert your code here.
                })
            }

        val preview = Preview.Builder()
            .build()
            .also {
                it.setSurfaceProvider(viewFinder.surfaceProvider)
            }
        imageCapture = ImageCapture.Builder().apply {
            setTargetAspectRatio(ratio)
        }.build()

        val cameraSelector = CameraSelector.DEFAULT_BACK_CAMERA

        try {
            cameraProvider.bindToLifecycle(
                this@CameraActivity,
                cameraSelector,
                preview,
                imageCapture,
                imageAnalysis
            )
        } catch (e: Exception) {
            Log.e(TAG,"바인딩 실패",e)
        }
    }, ContextCompat.getMainExecutor(this@CameraActivity))
}

```

`imageAnalysis`, `preview`, `imageCapture`, `cameraSelector` 카메라 실행을 하기 위한 옵션들을 정의하고 `bindToLifecycle`을 통해 사용자 기기와 카메라 프로바이더는 같은 라이프 사이클을 가지게 됩니다.

좀 더 코드를 분석 해 보자면

### CameraProviderFutuer

```kotlin
val cameraProviderFuture = ProcessCameraProvider.getInstance(this@CameraActivity)
```

카메라를 실행 시키기 위해 프로바이더를 등록하는 `ProviderFuture` 를 정의 합니다.

### CameraProvider

```kotlin
cameraProviderFuture.addListener({
        val cameraProvider: ProcessCameraProvider = cameraProviderFuture.get()
)}
```

프로바이더를 등록하기 위해 `addListner` 에 프로바이더를 추가 합니다.

그리고 여러가지 옵션들을 추가 하기 위해 기능들을 정의 합니다.

### ImageAnalysis

```kotlin
imageAnalysis = ImageAnalysis.Builder()
		.setBackpressureStrategy(ImageAnalysis.STRATEGY_KEEP_ONLY_LATEST)
		.build().apply {
				setAnalyzer(cameraExecutor, { image ->
				val rotationDegrees = image.imageInfo.rotationDegrees
				// insert your code here.
		})}
```

이미지 분석을 도와주는 `ImageAnalysis`를 정의 합니다. 단순 사진 촬영을 원하시는 분들은 넘어가도 되는 부분 입니다.

### Preview

```kotlin
val preview = Preview.Builder()
        .build()
        .also {
            it.setSurfaceProvider(viewFinder.surfaceProvider)
        }
```

사진을 촬영하기 촬영 하고 있는 화면을 미리 보여주는 역할을 하고 있습니다. 만약 위 코드가 없을 시 사진을 찍기 전 화면은 보이지 않게 됩니다.

### ImageCapture

```kotlin
imageCapture = ImageCapture.Builder().apply {
  // ratio = AspectRatio.RATIO_4_3, AspectRatio.RATIO_16_9          
  setTargetAspectRatio(ratio) 
}.build()
```

화면을 캡쳐하고 캡쳐한 파일을 컨트롤 할 수 있는 역할을 하고 있습니다. `setTargetAspectRatio(ratio)` 는 촬영 후 이미지의 화면 비를 구성하는 함수 입니다. 기본값은 `4:3` 비율로 현재 `16:9` 까지 비율을 설정 할 수 있습니다.

### CameraSelector

```kotlin
val cameraSelector = CameraSelector.DEFAULT_BACK_CAMERA
```

카메라 전 / 후면을 선택 할 수 있는 변수 입니다.

### BindToLifecycle

```kotlin
try {
   cameraProvider.bindToLifecycle(
        this@CameraActivity,
        cameraSelector,
        preview,
        imageCapture,
        imageAnalysis
    )
} catch (e: Exception) {
    Log.e(TAG,"바인딩 실패",e)
}
```

선언 한 옵션들을 사용자 생명주기와 바인딩 하여 카메라를 효율적으로 컨트롤 할 수 있습니다.



이렇게 카메라 셋팅에 대해서 다뤄 봤습니다 사진 촬영 및 저장은 다음 포스팅에 작성 하겠습니다 감사합니다 🙋









