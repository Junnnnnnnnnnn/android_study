# [Android] 커스텀 Dialog(팝업) 사용하기 (1)

## 들어가며

이전 포스팅에선 `AlertDialog` 를 통해 간단한 팝업창을 띄워보는 시간을 가졌습니다. 그러나 디자인이 생각보다 마음에 들지는 않을 겁니다. 왜냐하면 구글에서 기본으로 제공 하는 것이기 때문에 우리의 입맛에 맞지가 않죠 ㅜㅠ

그래서 오늘은 커스텀 팝업에 대해서 다뤄 보도록 하겠습니다.

## 커스텀 Dialog 레이아웃 구성

> dialog_layout.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <androidx.constraintlayout.widget.ConstraintLayout
        android:id="@+id/dialog_header"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:background="#eb5757"
        app:layout_constraintBottom_toTopOf="@+id/dialog_title"
        app:layout_constraintEnd_toStartOf="@+id/dialog_guide"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_chainStyle="packed">

        <ImageView
            android:id="@+id/dialog_icon"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:padding="10dp"
            android:src="@drawable/dialog_status_error_70"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent" />

        <ImageView
            android:id="@+id/dialog_plus_1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:src="@drawable/dialog_status_plus_15"
            app:layout_constraintBottom_toBottomOf="@+id/dialog_icon"
            app:layout_constraintEnd_toStartOf="@+id/dialog_icon"
            app:layout_constraintHorizontal_bias="0.9"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="@+id/dialog_icon"
            app:layout_constraintVertical_bias="0.2" />

        <ImageView
            android:id="@+id/dialog_plus_2"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:src="@drawable/dialog_status_plus_15"
            app:layout_constraintBottom_toBottomOf="@+id/dialog_icon"
            app:layout_constraintEnd_toStartOf="@+id/dialog_icon"
            app:layout_constraintHorizontal_bias="1"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="@+id/dialog_icon"
            app:layout_constraintVertical_bias="0.8" />

        <ImageView
            android:id="@+id/dialog_plus_3"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:src="@drawable/dialog_status_plus_15"
            app:layout_constraintBottom_toBottomOf="@+id/dialog_icon"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintHorizontal_bias="0"
            app:layout_constraintStart_toEndOf="@+id/dialog_icon"
            app:layout_constraintTop_toTopOf="@+id/dialog_icon"
            app:layout_constraintVertical_bias="0.3" />
    </androidx.constraintlayout.widget.ConstraintLayout>

    <TextView
        android:id="@+id/dialog_title"
        style="@style/text_title_dialog"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="제목"
        app:layout_constraintBottom_toTopOf="@+id/dialog_description"
        app:layout_constraintTop_toBottomOf="@+id/dialog_header"
        app:layout_constraintStart_toStartOf="parent"/>

    <TextView
        android:id="@+id/dialog_description"
        style="@style/text_description_dialog"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="설명"
        app:layout_constraintBottom_toTopOf="@+id/dialog_footer_confirm"
        app:layout_constraintEnd_toEndOf="@+id/dialog_guide"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/dialog_title" />

    <androidx.appcompat.widget.AppCompatButton
        android:id="@+id/dialog_footer_cencel"
        style="@style/cancel_button_dialog"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="취소"
        android:visibility="gone"
        app:layout_constraintEnd_toStartOf="@+id/dialog_footer_confirm"
        app:layout_constraintHorizontal_weight="1"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/dialog_description"
        tools:visibility="visible" />

    <androidx.appcompat.widget.AppCompatButton
        android:id="@+id/dialog_footer_confirm"
        style="@style/submit_button_dialog"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="확인"
        app:layout_constraintEnd_toEndOf="@+id/dialog_guide"
        app:layout_constraintHorizontal_weight="2"
        app:layout_constraintStart_toEndOf="@+id/dialog_footer_cencel"
        app:layout_constraintTop_toBottomOf="@+id/dialog_description" />

    <androidx.constraintlayout.widget.Guideline
        android:id="@+id/dialog_guide"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        app:layout_constraintGuide_begin="300dp" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

[사진]

길어 보이지만 간단합니다.

팝업의 해더 부분을 `ConstraintLayout` 로 설정하여 다양한 이미지를 조합해 디자인을 구성 했습니다
그 아래로는 제목, 설명란을 구성 하였고 확인과 버튼 부분은 `Button`을 사용해 구성 했습니다.

각각의 `View`에는 `style`값이 들어 있는데요 한번 같이 보겠습니다.

> dialog_style.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="layout_dialog">
        <item name="android:background">@drawable/layout_dialog</item>
        <item name="android:elevation">20dp</item>
    </style>

    <style name="submit_button_dialog">
        <item name="android:background">@drawable/submit_button_dialog</item>
        <item name="android:textColor">#000000</item>
        <item name="android:textSize">14sp</item>
    </style>
    <style name="cancel_button_dialog">
        <item name="android:background">@drawable/submit_button_dialog</item>
        <item name="android:textColor">#000000</item>
        <item name="android:textSize">14sp</item>
    </style>

    <style name="text_title_dialog">
        <item name="android:textSize">20sp</item>
        <item name="android:textColor">@color/black</item>
        <item name="android:paddingTop">15dp</item>
        <item name="android:paddingRight">20dp</item>
        <item name="android:paddingBottom">15dp</item>
        <item name="android:paddingLeft">20dp</item>
    </style>

    <style name="text_description_dialog">
        <item name="android:textSize">14sp</item>
        <item name="android:paddingRight">15dp</item>
        <item name="android:paddingBottom">20dp</item>
        <item name="android:paddingLeft">20dp</item>
    </style>

    <style name="animation_dialog">
        <item name="android:windowEnterAnimation">@anim/slide_bottom_to_top</item>
        <item name="android:windowExitAnimation">@anim/slide_top_to_bottom</item>
    </style>

</resources>
```

앞서 설정한 `style` 속성에 대한 값입니다. 각각의 `style `마다 다양한 `item` 들이 있는데 이것들은 `Layout` 에서 쓰일 속성 값과 같습니다.  

이제 `stlye` 에 사용한 `Drawable` 파일들을 살펴 보겠습니다.

> res/drawable/layout_dialog.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <solid android:color="@color/white" />
    <corners android:radius="20dp" />
</shape>
```

`shape` 를 통해 모양을 잡고  `solid` 는 배경색깔, `corners` 는 모서리를 둥글게 해줍니다.

> res/drawable/submit_button_dialog.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<ripple android:color="#333333" xmlns:android="http://schemas.android.com/apk/res/android">
    <item>
        <shape android:shape="rectangle">
            <solid android:color="@color/white"/>
        </shape>
    </item>
</ripple>
```

위와 별반 다를 건 없지만 `ripple` 사용해서  버튼을 누를때 애니메이션 추가하여 좀 더 예쁜 디자인을 추가 할 수 있습니다.

> res/anim/slide_bottom_to_top.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <alpha
        android:duration="200"
        android:fromAlpha="0.0"
        android:toAlpha="1.0" />

    <translate
        android:duration="200"
        android:fromYDelta="100%"
        android:toYDelta="0%"/>
</set>
```

`anim` 폴더를 추가해 하단에서 상단으로 올라오는 애니메이션  `resource` 파일을 구성 했습니다

`alpha` 태그는 `View` 의 투명도를 정할 수 있는데요 다음 속성을 확인해 보도록 하죠

> `android:duration`: 변화하는 속도
> `android:fromAlpha`: 도착점
> `android:toAlpha`: 시작정

즉 0.2초 동안 1 이라는 불투명도에서 0까지의 애니메이션 효과를 나타나겠금 하는 것입니다.

`translate` 태그는 이동 애니메이션 설정입니다 

> `android:duration`: 변화 속도
> `android:fromYDelta`: 도착점
> `android:toYDelta`: 출발점

즉 0.2초 동안 최 밑단에서 본래 자리로 돌아오는 애니메이션 입니다.

다음 포스팅에서 다룰 `ModuleDialog` 와 `MainActivity` 설정을 한다면 다음과 같이 애니매이션 효과를 넣을 수 있습니다.



