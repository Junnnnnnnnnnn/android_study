# [Android] 안드로이드 Spinner(스피너) 사용하기 (2)

## 들어가며

이전 포스팅에선 `spinner` 를 모듈화 시켜 원할 때 사용하기 용이 하도록 구현 해 보았습니다. 이제 모듈화 해 놓은 스피너를 통해 `MainActivity`에 적용 시키는 시간을 가져 보도록 하겠습니다.

## Class 구성

> MainActivity.kt

```kotlin
package com.yotdark.demo_spinner

import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.widget.Button
import android.widget.Spinner
import android.widget.Toast

class MainActivity : AppCompatActivity() {

    private val spinner1: Spinner by lazy {
        findViewById(R.id.spinner1)
    }
    private val spinner2: Spinner by lazy {
        findViewById(R.id.spinner2)
    }
    private val spinner3: Spinner by lazy {
        findViewById(R.id.spinner3)
    }
    private val button: Button by lazy {
        findViewById(R.id.submit)
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        initSpinner()
        submitClick()
    }


    private fun initSpinner(){

        val peopleList = mutableListOf<HashMap<String, Any>>().apply {
            add(hashMapOf("name" to "남자", "tag" to "man", "image" to R.drawable.spinner_item_man))
            add(hashMapOf("name" to "여자", "tag" to "woman", "image" to R.drawable.spinner_item_woman))
        }
        val regionList = mutableListOf<HashMap<String, Any>>().apply {
            add(hashMapOf("name" to "서울시", "tag" to "REGION_01"))
            add(hashMapOf("name" to "부산광역시", "tag" to "REGION_02"))
            add(hashMapOf("name" to "인천광역시", "tag" to "REGION_03"))
            add(hashMapOf("name" to "제주특별자치도", "tag" to "REGION_04"))
        }
        val ageList = mutableListOf<HashMap<String, Any>>().apply {
            add(hashMapOf("name" to "20살", "tag" to "20"))
            add(hashMapOf("name" to "21살", "tag" to "21"))
            add(hashMapOf("name" to "22살", "tag" to "22"))
            add(hashMapOf("name" to "23살", "tag" to "23"))
        }

        spinner1.adapter = SpinnerAdapter(
            this@MainActivity,
            R.layout.spinner_item,
            peopleList,
            MyConstant.SPINNER_OPTION1)
        
        spinner2.adapter = SpinnerAdapter(
            this@MainActivity,
            R.layout.spinner_item,
            regionList,
            MyConstant.SPINNER_OPTION2)
        
        spinner3.adapter = SpinnerAdapter(
            this@MainActivity,
            R.layout.spinner_item,
            ageList,
            MyConstant.SPINNER_OPTION2)
    }

    private fun submitClick(){
        button.setOnClickListener {
            Toast.makeText(this@MainActivity,
                "성별: ${(spinner1.selectedItem as HashMap<*,*>)["name"]}\n" +
                "거주지: ${(spinner2.selectedItem as HashMap<*,*>)["name"]}\n" +
                "나이: ${(spinner3.selectedItem as HashMap<*,*>)["name"]}\n", 																	Toast.LENGTH_LONG).show()
        }
    }
}
```

앞서 설정한 `activity_main`에서 3개의 스피너를 `view`로 사용 하였기 때문에 `spinner` 3개를 전역 변수로 만들고 사용하는 `class`입니다. 

하나씩 살펴 보도록 하겠습니다.

### InitSpinner

```kotlin
private fun initSpinner(){

  val peopleList = mutableListOf<HashMap<String, Any>>().apply {
    add(hashMapOf("name" to "남자", "tag" to "man", "image" to R.drawable.spinner_item_man))
    add(hashMapOf("name" to "여자", "tag" to "woman", "image" to R.drawable.spinner_item_woman))
  }
  val regionList = mutableListOf<HashMap<String, Any>>().apply {
    add(hashMapOf("name" to "서울시", "tag" to "REGION_01"))
    add(hashMapOf("name" to "부산광역시", "tag" to "REGION_02"))
    add(hashMapOf("name" to "인천광역시", "tag" to "REGION_03"))
    add(hashMapOf("name" to "제주특별자치도", "tag" to "REGION_04"))
  }
  val ageList = mutableListOf<HashMap<String, Any>>().apply {
    add(hashMapOf("name" to "20살", "tag" to "20"))
    add(hashMapOf("name" to "21살", "tag" to "21"))
    add(hashMapOf("name" to "22살", "tag" to "22"))
    add(hashMapOf("name" to "23살", "tag" to "23"))
  }

  spinner1.adapter = SpinnerAdapter(
    this@MainActivity,
    R.layout.spinner_item,
    peopleList,
    MyConstant.SPINNER_OPTION1)

  spinner2.adapter = SpinnerAdapter(
    this@MainActivity,
    R.layout.spinner_item,
    regionList,
    MyConstant.SPINNER_OPTION2)

  spinner3.adapter = SpinnerAdapter(
    this@MainActivity,
    R.layout.spinner_item,
    ageList,
    MyConstant.SPINNER_OPTION2)
}
```

총 3개의 스피너이기 때문에 `adapter` 선언도 3개의 `SpinnerAdapter` 객체가 되어야 합니다. 

`SpinnerAdapter` 생성자 매게 변수로 `context` 와 `res`, 보여줄 `infoList`, 리스트 UI를 커스텀할 `option` 이 4가지를 전달하게 되면 스피너가 생성되고 작동를 하게 됩니다.



### submitClick

```kotlin
private fun submitClick(){
  button.setOnClickListener {
    Toast.makeText(this@MainActivity,
                   "성별: ${(spinner1.selectedItem as HashMap<*,*>)["name"]}\n" +
                   "거주지: ${(spinner2.selectedItem as HashMap<*,*>)["name"]}\n" +
                   "나이: ${(spinner3.selectedItem as HashMap<*,*>)["name"]}\n", 																	 Toast.LENGTH_LONG).show()
  }
}
```

간단하게 선택한 아이템의 값을 가져와 `Toast` 메세지에 뿌리는 메서드입니다. 

선택한 값은 앞서 파라미터로 보내준 `infoList` 이고 타입이 `HashMap<String, Any>` 이기 때문에 `value`로 값을 찾는 모습을 확일 할 수 있습니다.

`Spinner`의 옵션값은 다양합니다. 그중 선택한 item 값을 가져오는 메서드로 `spinner.selectedItem` 을 사용 했습니다. 



위와 같은 코드를 작성하고 컴파일을 시키면 다음 과 같이 기능을 사용 할 수 있습니다.

