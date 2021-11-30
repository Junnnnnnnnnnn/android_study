# [Android] Dialog(팝업) 사용하기

## **들어가며**

안드로이드를 사용하면서 수 많은 팝업창을 볼 수 있습니다. 경고창을 띄우기 위한 팝업, 사용자의 액션을 받기 위한 팝업등 다양한 팝업이 존재 합니다. 안드로이드에선 팝업이라는 개념보다 `Dialog` 라는 명칭을 사용합니다. 

포스팅은 총 3개로 준비 했습니다 

- 기본 `AlartDialog`
- 커스텀 `Dialog`
- 하단 고정 `Dialog`

기본 `Dialog` 부터 살펴 보겠습니다

## 기본 Dialog

> MainActivity.kt

```kotlin
private fun initDialog(){
    val dialog = AlertDialog.Builder(this@MainActivity)
        dialog.setTitle("알림!")
        dialog.setMessage("알람창 기본입니다~")
        dialog.setPositiveButton("확인") { popup, _ ->
            Toast.makeText(this@MainActivity, "확인되었습니다", Toast.LENGTH_SHORT).show()
            popup.dismiss()
        }
        dialog.setNegativeButton("취소"){ popup, _ ->
            Toast.makeText(this@MainActivity, "취소되었습니다", Toast.LENGTH_SHORT).show()
            popup.dismiss()
        }
        dialog.show()
}
```

기본 `Dialog` 는 `android` 에서 제공하는 `AlertDialog`를 사용합니다.

`AlertDialog` 는 `Builder` 를 통해서 `dialog` 객체를 받고 여러가지 메서드를 사용해서 팝업 창을 구성합니다

> ### 주로 사용하는 메서드
>
> - `setTitle`: 팝업의 제목을 정합니다
> - `setMessage`: 팝업의 메세지를 정합니다.
> - `setPositiveButton`: 무언가를 허용 할때 사용되는 메서드로 버튼 텍스트 값과 클릭 리스너를 메개변수로 받습니다.
> - `setNegativeButton`: 무언가를 허용 하지 않을 때 사용되는 메서드 입니다 매개변수는 위 값과 같습니다.
> - `dismiss`: 팝업 창을 닫습니다
> - `show`: 팝업창을 보여 줍니다.

확인 버튼과 취소 버튼을 누르면 `dismiss` 를 통해 자동으로 팝업 창이 없어지도록 구현 했습니다.

따로 사용되는 레이아웃 없이 간단한 기본 창만 보여준다면 `AlertDialog` 를 사용하는 것도 하나의 방법이라고 생각 합니다.