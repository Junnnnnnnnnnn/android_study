# [Android] 안드로이드 Spinner(스피너) 사용하기 (1)

## 들어가며

수 많은 웹 페이지들을 보면 select 태그들을 사용해서 list를 보여준 후 사용자가 클릭을 하거나 선택을 하면 닫히는 그런 엘리먼트 들을 많이 접할 수 있습니다. 이 와같은 기능을 android 네이티브를 사용해 구현 할 수 있는데요. 안드로이드 스피너를 사용해서 드롭 리스트를 구현해 보도록 하겠습니다.

## 레이아웃 정의

> activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/textView1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="15dp"
        android:text="성별: "
        android:textSize="20sp"
        app:layout_constraintBottom_toTopOf="@id/textView2"
        app:layout_constraintEnd_toStartOf="@id/spinner1"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <androidx.appcompat.widget.AppCompatSpinner
        android:id="@+id/spinner1"
        style="@style/spinner_style_1"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginStart="10dp"
        android:layout_marginEnd="10dp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toEndOf="@id/textView1"
        app:layout_constraintTop_toTopOf="@id/textView1" />


    <TextView
        android:id="@+id/textView2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="15dp"
        android:text="거주지: "
        android:textSize="20sp"
        app:layout_constraintBottom_toTopOf="@id/spinner3"
        app:layout_constraintEnd_toStartOf="@id/spinner2"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/textView1" />

    <Spinner
        android:id="@+id/spinner2"
        style="@style/spinner_style_1"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginStart="10dp"
        android:layout_marginEnd="10dp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toEndOf="@id/textView2"
        app:layout_constraintTop_toTopOf="@id/textView2" />


    <TextView
        android:id="@+id/textView3"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="15dp"
        android:text="나이: "
        android:textSize="20sp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toStartOf="@id/spinner3"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/textView2" />

    <Spinner
        android:id="@+id/spinner3"
        style="@style/spinner_style_1"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginStart="10dp"
        android:layout_marginEnd="10dp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toEndOf="@id/textView3"
        app:layout_constraintTop_toTopOf="@id/textView3" />

    <Button
        android:id="@+id/submit"
        android:layout_width="0dp"
        android:layout_height="60dp"
        android:text="확인"
        android:textSize="20sp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />


</androidx.constraintlayout.widget.ConstraintLayout>
```

3개의 `TextView` 와 3개의 `Spinner`로 이루어져 있는 `activity_main`입니다. 각각의 `spinner` 값을 받아 정보를 보여주는 `button` view 도 구현 했습니다.

[사진] 

> spinner_item.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:id="@+id/spinner_header"
        android:layout_width="30dp"
        android:layout_height="30dp"
        android:src="@drawable/spinner_item_woman"
        android:visibility="gone"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="@id/spinner_content"
        app:layout_constraintBottom_toBottomOf="@id/spinner_content"
        tools:visibility="visible" />

    <TextView
        android:id="@+id/spinner_content"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="15dp"
        android:text="내용"
        android:textSize="20sp"
        app:layout_constraintStart_toEndOf="@id/spinner_header"
        app:layout_constraintTop_toTopOf="parent" />

    <ImageView
        android:id="@+id/spinner_tail"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="15dp"
        android:src="@drawable/spinner_item_down_24"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toTopOf="@id/spinner_content"
        app:layout_constraintBottom_toBottomOf="@id/spinner_content"
        android:visibility="gone"
        tools:visibility="visible"/>
</androidx.constraintlayout.widget.ConstraintLayout>
```

실질적으로 드롭 리스트에 뷰잉 되는 layout입니다. 지금은 한줄이 나오지만 `class`를 통해 list값을 넣으면 그 값에 맞게 리스트가 뷰잉 됩니다.

이렇게 `layout` 구성이 끝이 났으면 `Class`를 구현 해 보겠습니다

## Class 구현

> SpinnerAdapter.kt

```kotlin
package com.yotdark.demo_spinner

import android.annotation.SuppressLint
import android.content.Context
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.ArrayAdapter
import android.widget.ImageView
import android.widget.TextView
import androidx.annotation.LayoutRes
import androidx.core.content.ContextCompat

class SpinnerAdapter(
    context: Context,
    @LayoutRes private val res: Int,
    private val infoList: MutableList<HashMap<String ,Any>>,
    private val option: String
): ArrayAdapter<HashMap<String, Any>>(context,res,infoList) {

    override fun getItem(position: Int) = infoList[position]
    override fun getCount() = infoList.size

    @SuppressLint("ViewHolder")
    override fun getView(position: Int, convertView: View?, parent: ViewGroup): View {

        val view = LayoutInflater
            .from(context)
            .inflate(res,parent,false)

        val spinnerHeader: ImageView = view.findViewById(R.id.spinner_header)
        val spinnerContent: TextView = view.findViewById(R.id.spinner_content)
        val spinnerTail: ImageView = view.findViewById(R.id.spinner_tail)

        spinnerContent.text = infoList[position]["name"].toString()
        spinnerTail.visibility = View.VISIBLE

        return view
    }

    override fun getDropDownView(position: Int, convertView: View?, parent: ViewGroup): View {
        val view = LayoutInflater
            .from(context)
            .inflate(res, parent, false)

        val spinnerHeader: ImageView = view.findViewById(R.id.spinner_header)
        val spinnerContent: TextView = view.findViewById(R.id.spinner_content)
        val spinnerTail: ImageView = view.findViewById(R.id.spinner_tail)

        when(option){
            MyConstant.SPINNER_OPTION1 -> {
                spinnerHeader.visibility = View.VISIBLE
                spinnerHeader.setImageDrawable(ContextCompat.getDrawable(context, infoList[position]["image"].toString().toInt()))
            }
        }

        spinnerContent.text = infoList[position]["name"].toString()

        return view
    }

}
```

길어보이지만 상속을 해주는 부모 class에서 요구하는 사항만 맞춘다는 생각으로 코딩하시면 편할 것 같습니다. 하나씩 뜯어 보죠

###  Class parameter

```kotlin
class SpinnerAdapter(
    context: Context,
    @LayoutRes private val res: Int,
    private val infoList: MutableList<HashMap<String ,Any>>,
    private val option: String
): ArrayAdapter<HashMap<String, Any>>(context,res,infoList)
```

>`context`: `spinner`를 띄워줄 곳
>`res`: `spinner` layout을 구성하기 위한 값
>`infoList` `spinner`를 이용해서 보여줄 값
>`option`: `spinner item` UI를 커스텀 할 값

생성자로 값을 받고 이중 `context`, `res`, `infoList ` 는 부모에게도 전달 해 줘야 합니다. 그래야 `spinner`를 컨트롤 할 수 있으니까요!!!

### getView

```kotlin
override fun getView(position: Int, convertView: View?, parent: ViewGroup): View {

  val view = LayoutInflater
  .from(context)
  .inflate(res,parent,false)

  val spinnerHeader: ImageView = view.findViewById(R.id.spinner_header)
  val spinnerContent: TextView = view.findViewById(R.id.spinner_content)
  val spinnerTail: ImageView = view.findViewById(R.id.spinner_tail)

  spinnerContent.text = infoList[position]["name"].toString()
  spinnerTail.visibility = View.VISIBLE

  return view
}
```

`spinner`를 누르기 전 ui를 다루는 곳입니다.

`Layoutinflater`를 통해 리소스 파일을 객체화 시키고 `layout` 의 view 값들을 컨트롤 할 수 있습니다.

해당 코드에선 `spinner_tail` 즉 리스트를 펼져 주는 아이콘을 `View` 처리 해주고 선택한 item의 값을 보여주는 형태를 취합니다.

### getDropDownView

```kotlin
override fun getDropDownView(position: Int, convertView: View?, parent: ViewGroup): View {
  val view = LayoutInflater
  .from(context)
  .inflate(res, parent, false)

  val spinnerHeader: ImageView = view.findViewById(R.id.spinner_header)
  val spinnerContent: TextView = view.findViewById(R.id.spinner_content)
  val spinnerTail: ImageView = view.findViewById(R.id.spinner_tail)

  when(option){
    MyConstant.SPINNER_OPTION1 -> {
      spinnerHeader.visibility = View.VISIBLE
      spinnerHeader.setImageDrawable(ContextCompat.getDrawable(context, infoList[position]["image"].toString().toInt()))
    }
  }

  spinnerContent.text = infoList[position]["name"].toString()

  return view
}
```

`spinner`를 클리하면 펼쳐지는 뷰를 구성하는 메서드입니다.

리소스 객체화 하는 방법은 `getView` 와 동일합니다

이 메소드는 옵션 값에 따라 보여지는 리스트 형식이 달라집니다

- MyConstant.SPINNER_OPTION1 = 왼쪽 이미지가 보이게 View
- Else = 왼쪽 이미지가 안보이게

이렇게 사용자 옵션값을 통해서 어떤것을 뷰 할 것인지 안할 것인지를 구분 할 수 있습니다.



다음 포스팅으로는 `spinner` 를 실제로 어떻게 사용하는지에 대해 살펴 보겠습니다.

