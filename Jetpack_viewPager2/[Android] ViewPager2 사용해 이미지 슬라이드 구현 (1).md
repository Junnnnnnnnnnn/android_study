# [Android] ViewPager2 사용해 이미지 슬라이드 구현 (1)

## 🙋 **들어가며**

우리가 일상 생활 하면서 슬라이브 뷰를 많이 접하게 됩니다. 광고, 카테고리, 이미지 등 한손으로 쉽게 슥슥 다음 데이터를 불러오는 것이죠!

이러한 슬라이드를 쉽게 구현 하기 위해서 Jetpack 인 ViewPager2를 사용해 이미지 슬라이드를 구현 해 보겠습니다!

## ✏️ **Gradle 설정**

```groovy
dependencies {
					.
					.
					.
    // view Pager2
    implementation "androidx.viewpager2:viewpager2:1.0.0"
    			.
    			.
    			.
}
```



설정은 `dependencies` 에 한줄만 추가하면 되요!

이제 실질 적으로 슬라이드 페이지를 구성하게되는 어뎁터 클래스를 만들어 볼께요



## ✏️ ViewPager2 Adapter 구성하기

> ViewPager2Adapter.kt

```kotlin
/**
 * RecyclerView Adapter 를 상속 받는다
 * 제네릭 타입으론 UI에 보여줄 View 를 담는 ViewHolder 이다.
 */
class ViewPager2Adepter(
  private val context: Context,
  private val imageList: MutableList<Int>
): RecyclerView.Adapter<ViewPager2Adepter.PagerViewHolder>() {

    /**
     * View 를 담을 ViewHolder class 를 정의 한다.
     */
    inner class PagerViewHolder(itemView: View): RecyclerView.ViewHolder(itemView){
        val item: ImageView = itemView.findViewById(R.id.imageView1)
    }

    /**
     * ViewHolder 를 인스턴스화 하고 return
     */
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): PagerViewHolder {
        val view = LayoutInflater.from(context).inflate(
            R.layout.viewholder_main,
            parent,
            false
        )
        return PagerViewHolder(view)
    }

    /**
     * 뷰와 데이터를 바인딩 하는 메서드
     */
    override fun onBindViewHolder(holder: PagerViewHolder, position: Int) {
        holder.item.setImageDrawable(ContextCompat.getDrawable(context, imageList[position]))
    }

    override fun getItemCount() = imageList.size

}
```

하나씩 코드를 뜯어서 살펴 보죠!



### ViewPager2Adepter

```kotlin
class ViewPager2Adepter(
  private val context: Context,
  private val imageList: MutableList<Int>
): RecyclerView.Adapter<ViewPager2Adepter.PagerViewHolder>()
```

일단 `view` 를 하기위한 `context`와 슬라이드 하고자 하는 데이터인 `imageList` 를 인자로 받습니다.

그리고 `RecyclerView.Adapter` 를 상속받아 제네릭 타입으로 `ViewHolder`를 받는 class 입니다.

> ### ViewHolder 란?
>
> `View`를 할 데이터를 담는 객체로 리소스와 메모리 낭비를 막기 위해 사용됩니다.
>
> 보통 데이터를 슬라이드 할때 `findViewById` 값을 통해 그때 그때 마다 데이터를 불러오고 삭제하고를 반복 하게 됩니다.
>
> 그래서 `ViewHolder`를 사용하면 슬라이드 될 이미지를 모두 가져와 필요할때 마다 가져다 쓰는 방식으로 메모리와 자원 낭비를 줄일 수 있습니다.



### PagerViewHolder

```kotlin
inner class PagerViewHolder(itemView: View): RecyclerView.ViewHolder(itemView){
  val item: ImageView = itemView.findViewById(R.id.imageView1)
}
```

`ViewHolder` 를 구성하는 `class` 입니다. 인자로 받는 `View`는 사용자에게 보여줄 슬라이드 각각의 `Layout` 이고 `item` 은 데이터를 설정 할 엘리먼트 입니다.

앞으로 이 데이터를 통해 뷰를 구성하게 됩니다.



### onCreateViewHolder

```kotlin
override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): PagerViewHolder {
  val view = LayoutInflater.from(context).inflate(
    R.layout.viewholder_main,
    parent,
    false
  )
  return PagerViewHolder(view)
}
```

 슬라이드될 View를 `Inflate` 즉 객체화 해서 `PagerViewHolder` 클래스로 보내줍니다.

그렇게 되면 이제 슬라이드 할 때 이미지나 다른 데이터를 바인딩 할 준비가 되었습니다.



### onBindViewHolder

```kotlin
override fun onBindViewHolder(holder: PagerViewHolder, position: Int) {
    holder.item.setImageDrawable(ContextCompat.getDrawable(context, imageList[position]))
}
```

저는 이미지를 슬라이드 할 것이기 때문에 `ViewPager2Adapter` 인자로 받은 `imageList`를 `ViewHolder` 와 바인딩 시킬 것입니다.

`holder` 의 `item` 변수는 ImageView 타입이기 때문에 `setImageDrawable` 을 사용해서 이미지를 등록 할 수 있습니다.



이렇게 ViewPaer2 에 설정할 adapter 객체를 만들어 봤고 실제로 적용 하는 실습은 다음 포스팅에서 만나 보겠습니다!