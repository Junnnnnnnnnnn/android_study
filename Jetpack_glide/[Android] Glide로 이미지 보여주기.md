# [Android] Glide로 이미지 보여주기

## 들어가며

안드로이드를 구성하는 것들 중 이미지로 되있는 것들이 한 60프로는 차지하는 것 같습니다. 

많은 이미지를 부하 없이 로드하는 것도 하나의 좋은 개발 방법이라고 생각합니다 그래서!! Glide 라이브러리를 사용하여 부하없이 이미지를 로드 하고 로드 하는 과정, 에러 등 다양한 표현들을 코드 몇 줄로 추가하는 방법을 알아 보겠습니다.

## Gradle 설정

```groovy
dependencies {
    					.
    					.
    					.
    // Glide
    implementation 'com.github.bumptech.glide:glide:4.11.0'
    // Progress
    implementation 'androidx.swiperefreshlayout:swiperefreshlayout:1.0.0'
    					.
    					.
    					.
}
```

## AndroidManifest 설정

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.yotdark.example_glide">
		          .
  						.
  						.
		//인터넷 관리 퍼미션 허용
    <uses-permission android:name="android.permission.INTERNET" />

              .
  						.
  						.

</manifest>
```

네트워크에 접속하기 위해 `permission` 을 등록 해줍니다.



## View 구성

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="10dp"
    tools:context=".MainActivity">

    <ImageView
        android:id="@+id/image1"
        android:layout_width="0dp"
        android:layout_height="120dp"
        android:background="@drawable/background_imageview_glide"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toStartOf="@+id/image2"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <ImageView
        android:id="@+id/image2"
        android:layout_width="0dp"
        android:layout_height="120dp"
        android:layout_marginLeft="10dp"
        android:background="@drawable/background_imageview_glide"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toStartOf="@id/image3"
        app:layout_constraintStart_toEndOf="@+id/image1"
        app:layout_constraintTop_toTopOf="parent" />

    <ImageView
        android:id="@+id/image3"
        android:layout_width="0dp"
        android:layout_height="120dp"
        android:layout_marginLeft="10dp"
        android:background="@drawable/background_imageview_glide"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toEndOf="@id/image2"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dp"
        android:text="로드 성공"
        android:textColor="#333333"
        app:layout_constraintEnd_toEndOf="@id/image1"
        app:layout_constraintStart_toStartOf="@id/image1"
        app:layout_constraintTop_toBottomOf="@id/image1" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dp"
        android:text="로드 중"
        android:textColor="#333333"
        app:layout_constraintEnd_toEndOf="@id/image2"
        app:layout_constraintStart_toStartOf="@id/image2"
        app:layout_constraintTop_toBottomOf="@id/image2" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dp"
        android:text="로드 실패"
        android:textColor="#333333"
        app:layout_constraintEnd_toEndOf="@id/image3"
        app:layout_constraintStart_toStartOf="@id/image3"
        app:layout_constraintTop_toBottomOf="@id/image3" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

길어보이지만 정말 별게 아닙니담!

이미지를 로드할 `ImageView` 와 `ImageView` 별 설명을 적기 위해 `TextView` 를 작성합니다.

초기 셋팅이 끝났으면 Glide를 사용할 수 있겠금 Class 화 시켜 보도록 하겠습니다



## GlideActivity 클래스 생성

```kotlin
@GlideModule
class GlideActivity(private val context: Context): AppGlideModule() {

    fun glideLoadImage(view: ImageView, url: String, type: String){
        when(type){
            "loading" -> Glide.with(context)
                              .load(url)
                              .placeholder(getProgress())
                              .into(view)
            else -> Glide.with(context)
                         .load(url)
                         .placeholder(getProgress())
                         .centerCrop()
                         .error(R.drawable.warn_icon_24)
                         .into(view)
        }
    }

    private fun getProgress() = CircularProgressDrawable(context).apply {
        strokeWidth = 5f
        centerRadius = 30f
        start()
    }
}
```

간단하죠?? 이걸로 삽질을 좀 많이 했었는데.. 
`placeholder` 나 `error` 등 다양한 옵션을 사용하기 위해선 `@GlideModule`와 `AppGlideModule()` 를 꼭 써줘야 합니다! 아님 기본 기능인 이미지 로드 밖에 할 수 없습니다.



### glideLoadImage

```kotlin
fun glideLoadImage(view: ImageView, url: String, type: String){
    when(type){
        "loading" -> Glide.with(context)
                          .load(url)
                          .placeholder(getProgress())
                          .into(view)
        else -> Glide.with(context)
                     .load(url)
                     .placeholder(getProgress())
                     .centerCrop()
                     .error(R.drawable.warn_icon_24)
                     .into(view)
    }
}
```

`glideLoadImage` 는 이미지를 원하는 `imageView` 에 로드 하는 기능을 가지고 있습니다.

인수로는 이미지를 로드할 `view` , 이미지 주소인 `url`, 마지막으로 로딩 중 테스트를 위한 `type` 총 3 가지를 인수로 받습니다.

Glide는 여러가지 함수를 옵션 값을 가지고 있는데요 여기서 사용한 옵션 값들만 한번 살펴보도록 하겠습니다.

> 1. with
>    1. view할 context를 입력합니다.
> 2. load
>    1. 이미지 주소를 통해 로드합니다.
> 3. placeholder
>    1. 이미지를 불러오기전 프로그래스 애니메이션을 보여줍니다.
> 4. centerCrop
>    1. 사진을 ImageView 가로 세로 빈 틈없이 꽉체워서 보여줍니다.
> 5. error
>    1. 이미지 로드를 실패 했을 때 이미지를 보여줍니다
> 6. into
>    1. 이미지를 로드할 View를 입력합니다



### CurcularProgressDrawable

```kotlin
private fun getProgress() = CircularProgressDrawable(context).apply {
    strokeWidth = 5f
    centerRadius = 30f
    start()
}
```

프로그레스 애니메이션을 구성하고 있는 함수 입니다 

원형 프로그레스 선 두께를 `5`, 반지름을 `30` 으로 설정 하고 `start()`를 하면  애니메이션이 생성되는데 이 값을 `Glide.placeholder()` 인수로 사용하게 되면 로딩 되는 동안 프로그래스 애니메이션을 볼 수 있습니다.



`class` 를 다 구현했다면 `MainActivity`에 적용을 한번 해보겠습니다.

## MainActivity

```kotlin
class MainActivity : AppCompatActivity() {

    private val glide: GlideActivity by lazy {
        GlideActivity(this@MainActivity)
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)


        glide.glideLoadImage(image1,"https://image.edaily.co.kr/images/photo/files/NP/S/2019/05/PS19050800712.jpg", "success")
        glide.glideLoadImage(image2,"https://node.com.haha.jpg","loading")
        glide.glideLoadImage(image3,"https://nothing.nothing","false")
    }
}
```

사용 방법은 `GlideActivity`에 맴버 메서드인 `glideLoadImage`에 해당하는 변수만 인수로 넣으면 이미지 로드가 됩니다!



