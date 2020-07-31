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

    * @Binding : 상위 뷰가 가진 상태를 하위뷰에서도 사용하고 수정 할 수 있게 해주는 타입, 파생자료에 해당된다. 연산프로퍼티 형태로 사용된다.

    * @Binding 프로퍼티 래퍼는 전달 받은 데이터를 읽거나 직접 변경 할 수 있도록 만들어진 타입이고 상세 화면 그 자체가 별도의 원천 자료를 갖는 대신 메인 화면을 참조하게 된다. 그래서 두 화면은 같으며, 동기화가 된다.

      ```swift
      //화면 복귀 시 false 로 동기화가 되어 바뀐다.
      struct Home: View {
          @State var isFavorite: Bool = true
          var body: some View {
              NavigationView {
                  NavigationLink(destination: Sub(isFavorite: $isFavorite)) {
                      Text(isFavorite.description)
                  }
                  .navigationBarTitle("Hello World")
              }
          }
      }

      struct Sub: View {
          @Binding var isFavorite: Bool
          var body: some View {
              isFavorite = false
              return Text("\(isFavorite.description)")
          }
      }
      ```
  
**3) 도구**

> 데이터를 다루는데 사용되는 Tool이라는 도구가 사용되는데 Property, @State, @Binding 등이 있다.

* @State와 @Binding

  * @State : 뷰 자체에서 가져야 할 상태 프로퍼티를 말하며, 데이터에 대한 영속적인 상태를 저장하고 관찰하는 역할을 하게 해준다. 뷰 자신에 대한 상태를 저장하는 것이기 때문에 private 접근으로 설정하는 게 좋다.

  * @Binding : 상위뷰가 가진 상태를 하위뷰에서 사용하고 수정 할 수 있도록 해주는 파생자료 이다.

  * 토글 과 State 예제

      ``` swift
      struct Home: View {
          @State private var isFavorite: Bool = true	//1.
          @State private var count: Int = 0			//1.

          var body: some View {
              VStack {
                  Toggle(isOn: $isFavorite) { //2.
                      Text("isFavorite: \(isFavorite.description)")
                  }
                  Stepper("$count : \(count)", value: $count)	//2.
              }
          }
      }
    ```
  
* ObservableObject 프로토콜과 @ObservedObject

  * @ObservedObject 는 ObservableObject 프로토콜을 준수하는 모델(참조타입에 한해)에 해당 뷰가 의존성을 알리기 위해 사용하는 속성이다. 실제로 뷰에 영향을 끼치지 않는다.
  * @State는 자신이 상태값을 가지지만 이 속성은 변화 탐지를 위해 사용된다.
  
* @Publised

  * @ObservedObject 를 통해 감지한 속성을 뷰에 반영하는 속성이다.
  
  * @Published는 프로퍼티 변경 시점에 알려지는 것이도, 사용자가 그 시점을 정하여 알리고 싶을땐 objectWillChange 프로퍼티를 사용한다.
  
    ``` swift
    class User: ObservableObject {
        var name = "user Name"
        @Published var score = 0
        init(name: String, score: Int) {
            self.name = name
            self.score = score
        }
    }

    struct Home: View {
        @ObservedObject var user : User
        var body: some View {
            VStack {
                Text(user.name)
                Button(action: {
                    self.user.score += 1
                }) {
                    Text("\(self.user.score)")
                }
            }
        }
    }
    ```
