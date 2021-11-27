# [Android] 안드로이드 그리드 뷰(GridView) 사용하기 (1)

## **들어가며**

인스타 그램 내 정보에 가면 네모네모 한 사진들이 바둑판 처럼 잘 정렬 되어 있는 것을 자주 확인 하실 수 있습니다 그 뿐만 아니라 쇼핑몰 게임 등 다양한 곳에서 이미지나 레이아웃을 리스트 형식의 UI로 채택을 많이 하곤 합니다. 오늘은 안드로이드 그리드뷰(GridView)를 사용해서 이미지 리스트 를 다뤄 볼려고 합니다!

## **레이아웃 정의**

> grid_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/top"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Yoterest"
        android:textColor="#333"
        android:textSize="50sp"
        android:textStyle="normal"
        app:layout_constraintBottom_toTopOf="@+id/bottom"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_chainStyle="packed" />

    <GridView
        android:id="@+id/bottom"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:horizontalSpacing="2dp"
        android:numColumns="2"
        android:verticalSpacing="2dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@id/top" />


</androidx.constraintlayout.widget.ConstraintLayout>
```

상단은 텍스트 뷰 하단은 그리드 뷰로 구성 해 보았어요 

우리가 다뤄볼 그리드 뷰에 대해 잠깐 살펴보도록 하겠습니다

> ## GridView
>
> `horizontalSpacing` : 좌우 여백
> `verticalSpacing`: 상하 여백
> `numColumns`: 컬럼 수 

이 외에도 여러가지 GridView 속성이 있지만 3개만 다뤄보도록 하겠습니다.

다음으론 GridView에 item이 될 layout을 설정 하겠습니다.

> grid_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:id="@+id/main_view"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:background="#333"
        android:scaleType="centerCrop"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.0"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintDimensionRatio="1:1"
        app:layout_constraintBottom_toBottomOf="parent"/>

    <View
        android:id="@+id/bottom_view"
        android:layout_width="0dp"
        android:layout_height="50dp"
        android:background="#36000000"
        app:layout_constraintBottom_toBottomOf="@id/main_view"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <TextView
        android:id="@+id/like_text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="20dp"
        android:text="좋아요"
        android:textColor="@color/black"
        android:textSize="15sp"
        app:layout_constraintBottom_toBottomOf="@id/bottom_view"
        app:layout_constraintEnd_toStartOf="@id/share_text"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="@id/bottom_view" />

    <TextView
        android:id="@+id/share_text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="20dp"
        android:text="공유"
        android:textColor="@color/black"
        android:textSize="15sp"
        app:layout_constraintBottom_toBottomOf="@id/bottom_view"
        app:layout_constraintEnd_toStartOf="@id/go_text"
        app:layout_constraintStart_toEndOf="@id/like_text"
        app:layout_constraintTop_toTopOf="@id/bottom_view" />

    <TextView
        android:id="@+id/go_text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="20dp"
        android:text="둘러보기"
        android:textColor="@color/black"
        android:textSize="15sp"
        app:layout_constraintBottom_toBottomOf="@id/bottom_view"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toEndOf="@id/share_text"
        app:layout_constraintTop_toTopOf="@id/bottom_view" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

길어보이지만 별거 아닙니다!

GridView Item 상단엔 `이미지` 하단엔  `좋아요`,` 공유`,` 둘러보기` 로 구성되어 있으며 클릭 이벤트 처리는 좋아요만 할 수 있도록 코딩 할 것 입니다.

이제 `GridView Class` 정의 해보도록 하겠습니다

## GridAdapter.kt 구현

```kotlin
package com.yotdark.example_gridview

import android.annotation.SuppressLint
import android.content.Context
import android.view.LayoutInflater
import android.view.View
import android.view.ViewGroup
import android.widget.*
import androidx.constraintlayout.widget.ConstraintLayout
import androidx.core.content.ContextCompat

class GridAdapter(private val context: Context, private val infoList: MutableList<HashMap<String, Any>>): BaseAdapter() {

  inner class GridViewHolder(){
    lateinit var imageView: ImageView
    lateinit var like: TextView
  }

  override fun getCount() = infoList.size

  override fun getItem(position: Int) = infoList[position]

  override fun getItemId(position: Int) = position.toLong()

  @SuppressLint("ViewHolder")
  override fun getView(position: Int, convertView: View?, parent: ViewGroup?): View {

    var view = convertView

    var gridViewHolder = GridViewHolder()

    if(convertView == null){
      view = LayoutInflater
      .from(context)
      .inflate(R.layout.grid_main, parent, false)
      gridViewHolder.imageView = view.findViewById(R.id.main_view)
      gridViewHolder.like = view.findViewById(R.id.like_text)
      view.tag = gridViewHolder
    }else{
      gridViewHolder = view!!.tag as GridViewHolder
    }

    if(position < infoList.size){
      gridViewHolder.imageView.setImageDrawable(ContextCompat.getDrawable(context, infoList[position]["image"].toString().toInt()))
    }

    gridViewHolder.like.setOnClickListener{
      Toast.makeText(context,"${infoList[position]["id"]}번 좋아요!",Toast.LENGTH_SHORT).show()
    }

    return view!!
  }
}
```

하나하나 뜯어 보도록 하겠습니다



```kotlin
class GridAdapter(private val context: Context, private val infoList: MutableList<HashMap<String, Any>>): BaseAdapter()
```

그리드 뷰는 `context` 와 `infoList`를 파라미터로 받고 `BaseAdapter()` 를 상속 받아 어뎁터를 구성하게 됩니다 `BaseAdapter`는 총 4개의 상속 메서드가 있습니다

- override fun getCount()
- override fun getItem(position: Int)
- override fun getItemId(position: Int)
- override fun getView(position: Int, convertView: View?, parent: ViewGroup?): View

여기서 중요하게 봐야 할 곳은 `getView` 메서드로 엘리먼트 바인딩 과 이벤트 처리 모두를 이 메서드에서 한다고 생각 하시면 좋을 것 같네요 ㅎ



```kotlin
inner class GridViewHolder(){
  lateinit var imageView: ImageView
  lateinit var like: TextView
}
```

뷰홀더를 생성하기 위한 class입니다 뷰홀더를 사용하는 이유는 스크롤 할때마다 리소스를 가져오는 작업을 대신해 이미지를 한번 로드하고 가져다 쓰는 방식이기 때문입니다.

저는 이미지를 보여줄 `imageView` 와 클릭 이벤트를 할 `textView` 를 선언 해 두겠습니다.



```kotlin
@SuppressLint("ViewHolder")
override fun getView(position: Int, convertView: View?, parent: ViewGroup?): View {

  	var view = convertView

  	var gridViewHolder = GridViewHolder()

  	if(convertView == null){
	    view = LayoutInflater
	    			 				.from(context)
	    							.inflate(R.layout.grid_main, parent, false)
      
			gridViewHolder.imageView = view.findViewById(R.id.main_view)
	    gridViewHolder.like = view.findViewById(R.id.like_text)
      
	    view.tag = gridViewHolder
	  }else{
	    gridViewHolder = view!!.tag as GridViewHolder
	  }

	  return view!!
}
```

다음은 뷰 홀더에 `GridView` 의 `Item` 이 될 레이아웃을 넣는 작업입니다.

`LayoutInflater`를 통해 `resource` 를 객체화 시키고 객체화 된 엘리먼트 중 `imageView` 와 `like`를 뷰홀더에 넣어 줍니다. 
넣어준 값을 받은 뷰홀더를 `view.tag` 초기화 하고 `convertView` 가 `null `이 아닐 때 즉 item이 이미 바인딩 되었다면 뷰홀더에 있는 이미지를 꺼내 바인딩 시켜주는 역할 을 합니다

이렇게 설정을 해놓으면 기본적인 그리드뷰 형태를 지니게 됩니다.



이어서 다음 포스팅으로 GridView item 클릭 이벤트와 GridView를 어떻게 설정하는지를 다뤄보도록 하겠습니다~~