# [Android] ViewPager2 사용해 이미지 슬라이드 구현 (2)

## 들어가며

이전 포스팅에선 `ViewPager2` 를 사용하기 위한 `Class` 로직을 구현해 보는 시간을 가졌다면 오늘을 `MainActivity`에서 사용 해보는 시간을 가지겠습니다.

이렇게 하나의 기능을 모듈화 해 놓으면 재사용성이 좋아져 업무 속도가 향상이 될 수 있습니다!

이러한 슬라이드를 쉽게 구현 하기 위해서 `Jetpack` 인 `ViewPager2`를 사용해 이미지 슬라이드를 구현 해 보겠습니다!

본 포스팅 내용은 `ViewPager2` 에 대한 기본 설명 코드 설명이 주 된 포스팅으로 아래의 깃허브 주소를 통해 코드와 같이 본다면 이해하기 쉬울 거예요
https://github.com/Junnnnnnnnnnn/demo_android_viewPager2

## ✏️ Layout

`ViewPager2` 엘리먼트를 사용할 `activity_main` `layout`과` ViewHolderd`에 담을 layout 각각 두개의 layout을 작성 할꺼예요

> activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <androidx.viewpager2.widget.ViewPager2
        android:id="@+id/viewPager"
        android:layout_width="0dp"
        android:layout_height="0dp"

        app:layout_constraintBottom_toTopOf="@id/main_content"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_weight="1" />

    <androidx.constraintlayout.widget.ConstraintLayout
        android:id="@+id/main_content"
        android:layout_width="0dp"
        android:layout_height="0dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/viewPager"
        app:layout_constraintVertical_weight="2">

        <TextView
            android:id="@+id/content_text"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#333333"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent" />

    </androidx.constraintlayout.widget.ConstraintLayout>

</androidx.constraintlayout.widget.ConstraintLayout>
```

간단하게 슬라이드 공간과 `text` 공간을 분리 시켰습니다.

> viewhloder_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <ImageView
        android:id="@+id/imageView1"
        android:layout_width="0dp"
        android:layout_height="0dp"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        android:scaleType="centerCrop"
        app:layout_constraintBottom_toBottomOf="parent"/>

</androidx.constraintlayout.widget.ConstraintLayout>
```

`ViewHolder`에 엘리먼트가 될 `Layout` 입니다! 데이터와 바인딩 할때 `ImageView`가 사용 되겠죠?

`Layout` 을 설정 했으니 Activity를 구성 해 보겠습니다.

## ✏️ MainActivity

```kotlin
class MainActivity : AppCompatActivity() {

    private val imageList = mutableListOf<Int>().apply {
        add(R.drawable.download_1)
        add(R.drawable.download_2)
        add(R.drawable.download_3)
        add(R.drawable.download_4)
    }

    private val textList = mutableListOf<String>().apply {
        add("대한민국")
        add("미국")
        add("영국")
        add("중국")
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        mainInitViewPager2()

    }

    private fun mainInitViewPager2(){
        content_text.text = "대한민국"
        viewPager.apply {
            clipToPadding= false
            clipChildren= false
            offscreenPageLimit = 1
            adapter = ViewPager2Adepter(this@MainActivity, imageList)
        }
        viewPager.setPageTransformer(MarginPageTransformer(100))
        viewPager.setPadding(200,0,200,0)
        mainViewChangeEvent()
    }

    private fun mainViewChangeEvent(){
        viewPager.registerOnPageChangeCallback(object: ViewPager2.OnPageChangeCallback(){
            override fun onPageSelected(position: Int) {
                content_text.text = textList[position]
            }
        })
    }
}
```

하나식 뜯어서 살펴 보도록 할께요!

### mainInitViewPager2

```kotlin
private fun mainInitViewPager2(){
    content_text.text = "대한민국"
    viewPager.apply {
        clipToPadding= false
        clipChildren= false
        offscreenPageLimit = 1
        adapter = ViewPager2Adepter(this@MainActivity, imageList)
    }
    viewPager.setPageTransformer(MarginPageTransformer(100))
    viewPager.setPadding(200,0,200,0)
    mainViewChangeEvent()
}
```

`ViewPager2`를 초기화 하는 코드 입니다. `clipToPadding` 과 `clipChildren` 을 셋팅하게 되는데요

`ViewPager2`에 사용될 옵션을 살펴 보겠ㅅ브니다

>`cliptoPadding` 이란 ViewHolder에서 슬라이딩이 일어날때 설정해 놓은 패딩 값을 무시하고 뷰잉 되는 것을 뜻합니다 `cpliChildren` 이란 부모에게 할당 받은 View가 자신 이외의 영역에 침범 할 수 있게 만들어 줍니다.
>
>`offscreenPageLimit` 란 슬라이드로 뷰가 로드 될때 앞, 뒤 데이터를 미리 로드하는 것을 뜻합니다.
>
>`setPageTransfomer` 란 ViewHolder 의 View간의 사이의 간격을 설정 하게 됩니다.
>`setPadding` 란 값이 커질 수록 서로 영역에 침범 범위가 더 넓어 집니다.

기본적으로 ViewPager2를 설정하면 미리보기가 활성화 됩니다.

[사진]

마지막으로 슬라이드 이벤트가 발생할 때 listener 설정을 하겠습니담!

### mainViewChaneEvent

```kotlin
private fun mainViewChangeEvent(){
    viewPager.registerOnPageChangeCallback(object: ViewPager2.OnPageChangeCallback(){
        override fun onPageSelected(position: Int) {
            content_text.text = textList[position]
        }
    })
}
```

`ViewPager2.OnPageChangeCallBack()` 에 `onPageSelected` 를 상속 받아 `TextView`에 동적으로 값이 입력 됩니다.



[결과화면]



이렇게 포스팅을 마치며 간단하게 슬라이드를 구현 하고 싶으시면 `ViewPager2` 만한게 없다고 생각이 듭니다!