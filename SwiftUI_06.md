# SwiftUI Rect
# 

6강 유저 입력다루기
===========
## 4-1. 기본기 다지기

## 1. 데이터의 흐름 이해하기
> Swift UI 에서 데이터를 변경하고 전달하는 방식

**1) 프로퍼티 수정하기**

* 이 코드는 프로퍼티 framework 가 수정이 가능 한가?

> Cannot assign to property: 'self' is immutable, Body가 get 타입이라 Body 내에서 프로퍼티를 수정 할 수 없다

  ```swift
  struct Home: View {
      var frameWork: String = "UIKit"
      var body: some View {
          Button(frameWork) {
              self.frameWork = "SwfitUI"
          }
      }
  }
  ```

* 프로퍼티를 수정하려면 @State, @Binding 등의 새로운 도구를 사용 해야 한다.

**2) 데이터 흐름의 2원칙**

* 데이터 의존성
  
  * Swift UI에선 뷰가 어떤 데이터에 대해 의존성이 있는지 알려 주면 나머지는 프레임워크에서 알아서 처리 하도록 설계되어있다.

* 단일 원천 자료 : 동일한 데이터 요소가 여러 곳으로 나누어 중복 되지 않고 한 곳에서 다루어지고 수정해야 한다는 의미
  
  * 원천 자료와 파생자료 : SuperView가 가진 데이터를 원천자료 SubView가 SuperView의 데이터를 전달 받아 사용한다면 파생자료가 된다.
  
    * @State : View가 가져야 할 프로퍼티를 직접 바꾸는 대신 저장소에 값을 전달하고 참조하는 타입, 원천 자료에 해당된다.

    * @Binding : 전달받은 데이터를 읽거나 직접 변경 할 수 있는 타입이고 파생자료에 해당된다.

      ```swift
      struct Super: View {
          @State var isFavorite: Bool = true
          var body: some View {
              Sub(isFavorite: self.isFavorite)
          }
      }

      struct Sub: View {
          @State var isFavorite: Bool = false
          var body: some View {
              Text("\(isFavorite.description)")
          }
          init(isFavorite : Bool) {
              self.isFavorite = isFavorite
          }
      }
      ```
  
