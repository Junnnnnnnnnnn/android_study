# [Android] 안드로이드 그리드 뷰(GridView) 사용하기 (2)

## 들어가며

이전 포스팅에는 `GridView`를 사용하기 위한 `adapter` 를 구성했습니다. 오늘은 만들어 놓은 `adapter` 를 활용해 `MainActivity` 에 적용하고 이벤트 처리를 어떻게 할지 알아 볼께요!

## 클릭 이벤트 구현

> GridAdapter.kt

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

어제 다뤄 봤던 `gridView` `adapter` 중  `setOnClickListener` 를 알아 보겠습니다.

```kotlin
gridViewHolder.like.setOnClickListener{
  Toast.makeText(context,
                 "${infoList[position]["id"]}번 좋아요!",
                 Toast.LENGTH_SHORT).show()
}
```

여기서 중요한 점은 `MainActivity `에서 `setOnClickListener` 정의 하는 것이 아니라 어뎁터와 연결된 객체를 선언 한 곳 즉 `gridView` 클래스 에서 선언을 해야 작동이 됩니다.. 너무 삽질을 해서 이거 하나는 꼭! 알아두시면 좋을 것 같아요!



## MainActivity에 적용 하기

> MainActivity.kt

```kotlin
package com.yotdark.example_gridview

import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.widget.GridView

class MainActivity : AppCompatActivity() {

    val gridView: GridView by lazy {
        findViewById(R.id.bottom)
    }

    val infoList = mutableListOf<HashMap<String, Any>>().apply {
        add(hashMapOf("id" to 1, "goods" to "삼양a", "image" to R.drawable.image1))
        add(hashMapOf("id" to 2, "goods" to "삼양b", "image" to R.drawable.image2))
        add(hashMapOf("id" to 3, "goods" to "삼양c", "image" to R.drawable.image3))
        add(hashMapOf("id" to 4, "goods" to "삼양d", "image" to R.drawable.image4))
        add(hashMapOf("id" to 5, "goods" to "삼양e", "image" to R.drawable.image5))
        add(hashMapOf("id" to 6, "goods" to "삼양f", "image" to R.drawable.image6))
        add(hashMapOf("id" to 7, "goods" to "삼양g", "image" to R.drawable.image1))
        add(hashMapOf("id" to 8, "goods" to "삼양h", "image" to R.drawable.image2))
        add(hashMapOf("id" to 9, "goods" to "삼양i", "image" to R.drawable.image3))
        add(hashMapOf("id" to 10, "goods" to "삼양j", "image" to R.drawable.image4))
        add(hashMapOf("id" to 11, "goods" to "삼양k", "image" to R.drawable.image5))
        add(hashMapOf("id" to 12, "goods" to "삼양l", "image" to R.drawable.image6))
        add(hashMapOf("id" to 13, "goods" to "삼양m", "image" to R.drawable.image1))
        add(hashMapOf("id" to 14, "goods" to "삼양n", "image" to R.drawable.image2))
        add(hashMapOf("id" to 15, "goods" to "삼양o", "image" to R.drawable.image3))
        add(hashMapOf("id" to 16, "goods" to "삼양p", "image" to R.drawable.image4))
        add(hashMapOf("id" to 17, "goods" to "삼양q", "image" to R.drawable.image5))
        add(hashMapOf("id" to 18, "goods" to "삼양r", "image" to R.drawable.image6))
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        mainInitGridView()
    }

    private fun mainInitGridView(){
        gridView.apply {
            adapter = GridAdapter(this@MainActivity, infoList)
        }
    }
}
```

먼저 데이터를 넣을 값을 `list` 로 초기화 합니다. 그리고 `activity_main` 엘리먼트인 `gridView` 에 `adapter`를 설정 하는 것이 끝입니다! 이렇게 하면 화면에 리스트가 보이게 됩니담!



`gridView` 메인 설정과 `gridView` 의 각각의 `item`에 대한 클릭 이벤트에 대해 알아 봤습니다~~~ 
