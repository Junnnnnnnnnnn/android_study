# [Android]안드로이드 팝업 리스트 구현

## 들어가며

리스트를 만들고 싶은데 `spinner` 뷰는 사용하기 싫으신 분들은 고민이 많으실 겁니다. 그렇다면!! `ListPopup` 을 사용해서 어떤 view라도 클릭시 팝업 리스트 형식으로 나올 것입니다. 따라오시죠!

## Layout 구성

> Listpopup_layout.xml

```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <TextView
        android:id="@+id/adapter_text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        android:text="내용"
        android:textSize="18sp"
        android:padding="10dp" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

실질적인 리스트 뷰를 하기위한 `layout` 입니다 간단하게 `TextView` 만 띄워놓는 용도로 사용 할 것입니다.



## AdapterClass

```kotlin
package com.yotdark.example_listpopupwindow

import android.annotation.SuppressLint
import android.content.Context
import android.util.Log
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.ArrayAdapter
import android.widget.TextView
import androidx.annotation.LayoutRes

class ModuleArrayAdapter(
  context: Context,
  @LayoutRes private val res: Int,
  private val infoList: MutableList<HashMap<String, Any>>
): ArrayAdapter<HashMap<String, Any>>(context, res, infoList) {

  @SuppressLint("ViewHolder")
  override fun getView(position: Int, convertView: View?, parent: ViewGroup): View {
    val view = LayoutInflater.from(context).inflate(
      res,
      parent,
      false
    )
    view.findViewById<TextView>(R.id.adapter_text).text = infoList[position]["name"].toString()

    return view
  }

  override fun getDropDownView(position: Int, convertView: View?, parent: ViewGroup): View {
    val view = LayoutInflater.from(context).inflate(
      res,
      parent,
      false
    )

    view.findViewById<TextView>(R.id.adapter_text).text = infoList[position]["name"].toString()
    return view
  }
}
```

메서드 구성은 다음과 같습니다

> `getView`:  리스트를 펼치기전 뷰 (ListPopupWindow 에는 설정 필요 없음)
>
> `getDropDownView`: 리스트를 펼쳤을때 보이는 View



이제 `MainActivity` 에서 사용 방법을 알아 보겠습니다.

## ✏️ MainActivity 구성하기

### ✏️ ListPopupList로 팝업 리스트 설정 해보기

> MainActivity.kt

```kotlin
private val popupList: ListPopupWindow by lazy{
  ListPopupWindow(this)
}

private fun initTextMenu(){
  popupList.apply {
    width = 400
    anchorView = textMenu
    setAdapter(ModuleArrayAdapter(this@MainActivity,R.layout.listpopup_layout,infoList))
    show()
  }
}
```

`ListPopupWindow` 는 가로를 설정해 줘야 합니다. 세로는 layout 의 세로 값에 따라 유연하게 바뀝니다.

`anchorView`을 통해서 리스트 팝업을 보여줄 위치를 정합니다.

당연히 `adapter` 를 설정을 해야 합니다

```kotlin
private fun textMenuEvent(){
    popupList.setOnItemClickListener { _, _, position, _ ->
        Toast.makeText(
            this@MainActivity,
            "textView: ${infoList[position]["name"]}",
            Toast.LENGTH_SHORT
        ).show()
    }
}
```

아이템 클릭 리스너 입니다.

여타 리스트 뷰에서 많이 본 리스너 이기 때문에 사용하기도 수월합니다.





`spinner` 와 비교를 하실 분들은 아래의 링크를 통해 확인 해 주세요~~

