# [Android] 커스텀 Dialog(팝업) 사용하기 (2)

## 들어가며

이전 포스팅에서는 커스텀 `Dialog` 로그의 `layout` 부분을 다뤄 봤습니다. 이번 포스팅 에선 커스텀 팝업이 어떻게 구현 되고 어떻게 사용 할 수 있는지 에 대해서 살펴 보도록 하겠습니다.

## ModuleDialog 구현 하기

> ModuleDialog.kt

```kotlin
package com.yotdark.example_dialog

import android.app.Dialog
import android.content.Context
import android.graphics.Color
import android.graphics.drawable.ColorDrawable
import android.view.Gravity
import android.view.View
import android.view.ViewGroup
import android.widget.Button
import android.widget.TextView
import androidx.annotation.LayoutRes

class ModuleDialog(private val context: Context, @LayoutRes private val res: Int) {

    private val dialog by lazy {
        Dialog(context)
    }

    private val window by lazy {
        dialog.window
    }

    init {
        dialog.setContentView(res)
        dialog.setCancelable(false)
    }

    fun setDefaultDialog(){
        dialog.findViewById<Button>(R.id.dialog_footer_confirm).setOnClickListener {
            dismiss()
        }
    }

    fun setSlideBottomToTop(res: Int){
        window?.attributes?.windowAnimations = res
    }

    fun setTitle(title: String){
        dialog.findViewById<TextView>(R.id.dialog_title).text = title
    }

    fun setMessage(message: String){
        dialog.findViewById<TextView>(R.id.dialog_description).text = message
    }

    fun setPositive(listener: View.OnClickListener){
        dialog.findViewById<Button>(R.id.dialog_footer_confirm).setOnClickListener(listener)
    }

    fun setNegative(listener: View.OnClickListener){
        dialog.findViewById<Button>(R.id.dialog_footer_cencel).setOnClickListener(listener)
    }

    fun setCancelButton(condition: Boolean){
        if(condition){
            dialog.findViewById<Button>(R.id.dialog_footer_cencel).visibility = View.VISIBLE
            return
        }
        dialog.findViewById<Button>(R.id.dialog_footer_cencel).visibility = View.GONE
    }

    fun dismiss(){
        dialog.dismiss()
    }

    fun show() = dialog.show()


}
```

엄청 나게 길지만 따라 오실 수 있습니다!

사용한 메서드들은 `AlertDialog` 와 최대한 비슷하게 구성 할려고 노력 했습니다

위 클레스내의 맴버 메서드를 간략하게 설명 하겠습니다.

> `init` : 생성자로써 모듈이 인스턴스화 되었을 때 실행 됩니다. `dialog` 의 레이아웃을 설정하고 기타 옵션을 설정 합니다.
>
> `setDefultDialog()`: `dialog` 의 `default`값을 설정합니다 제가 설정한 `default`로는 확인 버튼만 뷰 하고 확인 버튼을 누를 시 `dismiss` 되는 로직입니다`
>
> `setSlideBottomToTop()`: `dialog`를 View하면 기본 값으로 `fade in `, `fade out` 이 이뤄지는데 위 메서드는 최 하단에서 서서히 View 하는 그런 기능을 가졌습니다
>
> `setTitle()`: `dialog`의 제목을 설정 합니다.
>
> `setMessage()`:  `dialog`의 내용을 설정 합니다.
>
> `setPositive()`:  `dialog`의 확인 버튼에 대한 리스터를 설정 합니다.
>
> `setNegative()`: `dialog`의 취소 버튼에 대한 리스터를 설정 합니다.
>
> `setCancelButton()`:  `dialog`에 취소 버튼을 `View` 합니다.
>
> `dismiss()`:  `dialog`의 창을 닫습니다
>
> `show()`:  `dialog`의 창을 `View` 합니다.

`Module` 이라 위 메서드만 있으면 팝업창을 만들 수 있습니다!!



## MainAcitivty 으로 Dialog 구현 하기

> MainActivity.kt

```kotlin
private fun initCustomDialog(){
  ModuleDialog(this@MainActivity, R.layout.dialog_layout).apply {
    setDefaultDialog()
    setCancelButton(true)
    setSlideBottomToTop(R.style.animation_dialog)
    setTitle("경고!")
    setMessage("당신은 탈모 빔을 맞았습니다.\n" +
               "지금 바로 자라나라 머리머리를 말하지 않는다면 탈모가 걸릴 것입니다")
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

실 사용 기 입니다!

단 `dialog` 로 사용할 `resource` 는 4가지의 id를 담고 있어야 합니다 

- `dialog_title`
- `dialog_description`
- `dialog_footer_cencel`
- `dialog_footer_confirm`

다음과 같이 맞춰 주시면 누구나 예쁜 팝업창을 띄울 수 있습니다~~

