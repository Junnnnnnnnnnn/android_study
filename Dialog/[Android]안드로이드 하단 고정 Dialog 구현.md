# [Android]안드로이드 하단 고정 Dialog 구현

## 들어가며

앞서 기본 팝업, 커스텀 팝업을 다뤄 봤습니다. 요즘 동글동글 하게 하단 고정 `Dialog` 가 대세인 것 같습니다 토스, 카카오톡, 카뱅 등 다양 한 곳에서 하단 `Dialog` 를 사용 하는 것 같아요! 이제 그것을 이전 포스팅에서 구현해 놓았던 `ModuleDialog.kt`를 통해 하단 고정  팝업을 구현해 보겠습니다!

`ModuleDialog.kt` 를 참고 하실 분들은 아래의 링크를 참고 해주시거나 깃허브를 방문 해 주세요~

## 레이아웃 구현

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@drawable/dialog_slide_background">

    <TextView
        android:id="@+id/dialog_title"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_margin="30dp"
        android:text="제목"
        android:textSize="30sp"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:id="@id/dialog_description"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:paddingTop="10dp"
        android:paddingEnd="60dp"
        android:paddingBottom="30dp"
        android:text="상세내용"
        app:layout_constraintStart_toStartOf="@id/dialog_title"
        app:layout_constraintTop_toBottomOf="@id/dialog_title"
        tools:ignore="RtlSymmetry" />

    <androidx.appcompat.widget.AppCompatButton
        android:id="@+id/dialog_footer_cencel"
        style="@style/cancel_button_dialog"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="취소"
        app:layout_constraintBottom_toBottomOf="@id/guideline_horizontal"
        app:layout_constraintStart_toStartOf="parent" />

    <androidx.appcompat.widget.AppCompatButton
        android:id="@+id/dialog_footer_confirm"
        style="@style/submit_button_dialog"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="확인"
        app:layout_constraintBottom_toBottomOf="@id/guideline_horizontal"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toEndOf="@id/dialog_footer_cencel" />

    <androidx.constraintlayout.widget.Guideline
        android:id="@+id/guideline_horizontal"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        app:layout_constraintGuide_begin="300dp" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

사진만 보면 되게 별 것 없어 보일 것입니다 그러나 이 `Dialog` 가 애니메이션과 기본 `dialog` 폼을 가지고 View를 한다면 예쁜 다이어로그가 생성 될 것입니다!

일단 최상단 부모 `Layout` 에 백그라운드로 동글동글한 사각형 `shape` 를 적용 했습니다. 왜냐면.. 요즘 다 그렇게 하더라구요? ㅋㅋ

나머지는 기존에 설정되어 있는 `style` 값들을 사용 했습니다.



## ModuleDialog.kt 에 하단 고정 기능 추가 하기

> ModuleDialog.kt

```kotlin
fun setBottomDialog(){
  window?.apply {
    setLayout(
      ViewGroup.LayoutParams.MATCH_PARENT,
      ViewGroup.LayoutParams.WRAP_CONTENT
    )
    setGravity(Gravity.BOTTOM)
    setBackgroundDrawable(ColorDrawable(Color.TRANSPARENT))
  }
}
```

클래스에 해당 메서드를 추가하고 사용하는 곳에서 메서드를 호출만 하면 됩니다.

`window` 객체를 통해 `Layout`을 재정의 합니다. 좌우는 `MATCH_PARENT` 로 상하는 `WRAP_CONTENT` 로 설정 했습니다. 

`Layout`의 위치를 하단으로 고정 합니다 그럼 밑으로 딱 붙겠죠?!

그리고 중요한게 `setBackgroundDrawable(ColorDrawable(Color.TRANSPARENT))` 이 부분인데 해당 코드를 사용하지 않으면 좌우 여백이 남아 하단 고정으로 보이질 않습니다.



## MainActivity 로 하단 고정 Dialog 구현하기

> MainActivity.kt

```kotlin
private fun initBottomDialog(){
  ModuleDialog(this@MainActivity, R.layout.dialog_bottom).apply {
    setBottomDialog()
    setSlideBottomToTop(R.style.animation_dialog)
    setTitle("경고!")
    setMessage("당신은 탈모 빔을 맞았습니다.\n지금 바로 자라나라 머리머리를 말하지 않는다면 탈모가 걸릴 것입니다")
    setPositive {
      Toast.makeText(this@MainActivity, "자라나라 머리머리!!!!", Toast.LENGTH_SHORT).show()
      dismiss()
    }
    setNegative {
      Toast.makeText(this@MainActivity, "그냥 탈모로 살래...", Toast.LENGTH_SHORT).show()
      dismiss()
    }
    show()
  }
}
```

앞서 포스팅한 `Dialog` 설정 하는 방법 과 같습니다 거기에 `setBottomDialog()` 추가 한 것 뿐!!

설정을 다 하셨다면 다음과 같이 하단 고정 팝업을 사용 할 수 있습니다.