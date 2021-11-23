# [Android] CameraX 사용하여 사진 촬영 및 저장 (3)

## 들어가며

이전 포스팅에선 cameraX 사진 촬영 및 이미지 저장에 대해 살펴 보았습니다. 

그러나 사용자가 기기를 세로모드로 변경 후 사진을 가로, 세로로 촬영 한다면 cameraX는 가로세로의 메타 데이터가 없기 때문에 이미지를 서버로 보내 저장 할 때 사진이 원하는 방향으로 되어 있지 않을 수 있습니다. 

이를 해결 하기 위해 코드를 몇출 추가 해야 합니다!

본 포스팅 내용은 CameraX에 대한 기본 설명 코드 설명이 주 된 포스팅으로 아래의 깃허브 주소를 통해 코드와 같이 본다면 이해하기 쉬울 거예요
https://github.com/Junnnnnnnnnnn/demo_android_cameraX



## 화면 감지

### OrientationEventListener

```kotlin
private val orientationEventListener by lazy {
    object : OrientationEventListener(this@CameraActivity) {
        override fun onOrientationChanged(orientation: Int) {
            if(orientation == -1){
                return
            }
            val rotation = when (orientation) {
                in 45 until 135 ->  Surface.ROTATION_270
                in 135 until 225 -> Surface.ROTATION_180
                in 250 until 315 -> Surface.ROTATION_90
                else -> Surface.ROTATION_0
            }

            imageAnalysis?.run { targetRotation = rotation }
            imageCapture?.run { targetRotation = rotation }
        }
    }
}

override fun onStart() {
  super.onStart()
  orientationEventListener.enable()
}

override fun onStop() {
  super.onStop()
  orientationEventListener.disable()
}

```

세로 고정 모드는 가로 세로를 기기가 스스로 확인을 할 수 없습니다 그래서 `orientationEventListener`를 통해 회전 감지를 해야 합니다.

등록한 `Listener`는 기기의 `onCreate` 이후 `onStart` 일 때 실행 이 되야 하고 `onDestroy` 전인 `onStop` 에서 멈춰야 합니다.

`Listener`를 통해 받은 `orientation` 값은 `rotation` 변수에 초기화 되어 사진 촬영을 위한 `ImageCapture` 와 분석을 위한 `imageAnalysis`에 설정 됩니다

그럼 메타데이터에 `90`, `180`, `270`, `0`에 대한 값들이 저장됨으로 서버에 정상적인 방향으로 이미지가 저장됩니다.

그러나 다음과 같이 촬영된 사진에 대한 회전 값만 안다고 UI도 같이 회전에 맞춰서 돌아가지는 않습니다. 

[사진]

그래서 화면 방향에 맞는 UI를 구성해야 합니다

## UI 설정

### setRotate()

```kotlin
private fun setRotate(id: Int, type: String, rotate: Float, duration: Long){
    var currentDegree = 0f
    val view = findViewById<Button>(id)
    when(type){
        "Button" -> currentDegree = view.rotation
        "ImageView" -> currentDegree = view.rotation
    }
    ObjectAnimator.ofFloat(view, View.ROTATION, currentDegree, currentDegree + rotate)
        .setDuration(duration)
        .start()
}

```

간단한 메서드 입니다 

인자로 UI의 현재 회전값을 알기 위해 `id` 를 받고, 타입에 맞는 회전을 하기 위해 `type`, 돌릴 회전 각을 설정하기 위해 `rotate`, UI가 돌아갈 애니메이션 시간을 설정하기 위해 `duration` 을 받습니다.

에니메이션 효과는 `ObjectAnimator` 클래스를 사용하여 제자리에서 회전을 할 수 있습니다.

이렇게 선언함 메서드를 처음에 다뤘던 `orientationEventListener` 에 응용 해보겠습니다.

```kotlin
val rotation = when (orientation) {
    in 45 until 135 -> Surface.ROTATION_270
    in 135 until 225 -> Surface.ROTATION_180
    in 250 until 315 -> {
        if(orientation > 310){
            return
        }
        if(animFlag == 0){
            animFlag = 1
            setRotate(cameraButton.id,"Button",90f, 300)
        }
        Surface.ROTATION_90
    }
    else -> {
        if(orientation > 240){
            return
        }
        if(animFlag == 1){
            animFlag = 0
            setRotate(cameraButton.id,"Button",-90f, 300)
        }
        Surface.ROTATION_0
    }
}

```

`orientation` 값이 `250 ~ 315` 일때가 가로 이므로 `setRotate`를 통해 회전으르 시켜 줍니다. 여기서 `animFlag`란 값을 중복으로 받지 않기 위해서 선언된 값으로 실시간으로 `orientation`을 받기 때문에 가로일때 회전이 무한정으로 될 수도 있습니다. 

따라서 `Flag` 값을 두고 한번만 회전할 수 있게 만듬니다.

그럼 사용자가 세로 고정으로 기기를 변경 하더라도 UI는 동적으로 움직이게 됩니다!



이렇게 android Jectpac cameraX 를 이용한 사진 촬영 및 저장을 마치겠습니다~