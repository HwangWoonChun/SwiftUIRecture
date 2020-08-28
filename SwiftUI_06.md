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

> 데이터를 다루는데 사용되는 Tool이라는 도구가 사용되는데 @State, @Binding 등이 있다.


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
  * @State는 뷰의 상태를 저장하고 다루기 위함이라면 뷰 외부의 모델이 가진 원천자료를 다루기 위한 도구 이다. 그 중 참조타입이 아닌 경우에 ObservableObject 가 사용된다.
  
  * @Publised

    * @ObservedObject 를 통해 감지한 속성을 뷰에 반영하는 속성이다.
    * 관련없는 데이터 들이 뷰에 영향을 끼치는 것을 불필요하기에 어떤 변경사항을 어느시점에 뷰에 전달 할지 알려준다.
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

* @EnviormentObject

  * 비교 1 : @ObservedObject 는 모델에 대한 직접적인 의존성을 만들지만 @EnviormentObject는 모델에 대한 간접적인 의존성을 만드는데 사용되는 도구이다.
  * 비교 2 : @ObservedObject 는 특정 모델에 대한 참조를 뷰에 직접 전달하여 의존성을 만들어 준다. 자식 뷰에게도 알려주려면 계속 Binding 해줘야 한다.
  * 동작순서 : @EnviormentObject 는 enviormentObject() 를 이용해 특정뷰에 대한 환경요소로 등록한다. 그럼 뷰를 포함한 자식뷰들에게 등록해둔 모델에 대한 의존서을 만들어 준다.
  * 특징 : @EnviormentObject 는 자식 뷰들을 띄엄띄엄 사용 할 수 있다.(어떤 자식은 쓰고 어떤 자식은 안쓰고)
  
  
    ``` swift
    class User: ObservableObject{
        let name: String = ""
    }

    struct Home: View {
        var body: some View {
            SuperView().environmentObject(User())	//1. 자식 뷰에서 공통으로 사용될 인스턴스 생성하여 전달 ObservableObject 프로토콜을 준수 해야한다.
        }
    }

    struct SuperView: View {
        var body: some View {
            SubView()
        }
    }

    struct SubView: View {
        @EnvironmentObject var user: User	//어떠한 값도 전달안했는데 전달됨
        var body: some View {
            Text(user.name.description)
        }
    }
    ```

**4) Alert, ActionSheet**
> 기존에는 UIAlertViewController.style.alert or UIAlertViewController.style.actionSheet 로 통합하여 사용했지만 SwiftUI에선 쪼개졌다. Alert의 최대 버튼 크기는 2개 이다.

* Alert
  ``` swift
  struct Home: View {
      @State private var showIngAlert = false
      var body: some View {
          Button(action: {
              self.showIngAlert = true
          }) {
              Text("Alert")
          }
          .alert(isPresented: $showIngAlert) {
              Alert(title: Text("제목"), message: Text("내용"), primaryButton: .default(Text("확인"), action: { print("확인") }),  secondaryButton: .cancel(Text("취소")))
          }
      }
  }
  ```
  
* ActionSheet
  ``` swift
  struct Home: View {
      @State private var showIngAlert = false
      var body: some View {
          Button(action: {
              self.showIngAlert = true
          }) {
              Text("Alert")
          }
          .actionSheet(isPresented: $showIngAlert) {
              ActionSheet(title: Text("제목"), message: Text("내용"), buttons: [
                  .default(Text("버튼1"),action: { print("버튼1")}),
                  .default(Text("버튼2")),
                  .destructive(Text("버튼3")),
                  .default(Text("버튼4")),
                  .cancel(Text("버튼5"))
              ])
          }
      }
  }
  ```
**5) 프레젠테이션 스타일**
> IOS 12 이전은 moalPresentation Style 이 Full Screen 이 기본 값이지만, 13 부터는 automatic이 기본이며, 대부분 pageSheet으로 동작한다. PageSheet 은 sheet과 popover 스타일로 나뉘어진다.

* Popover는 아이패드 용임으로 다루지 않겠음

* automatic 으로 된 Pagesheet 과 FullScreen

  <table><tr><td>
      <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rec06_1.png" width = 104 height = 210>
      <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rec06_2.png" width = 104 height = 210>
  </table></tr></td>    

* sheet 은 새로운 뷰를 출력한다.

  * presentationMode 를 사용 하여 뷰 출력 닫기

    ``` swift
    struct Home: View {
        @State private var showingSheet = false
        var body: some View {
            Button(action: {
                self.showingSheet.toggle()
            }) {
                Text("Present").font(.title).foregroundColor(.black)
            }
            .sheet(isPresented: $showingSheet,
                   onDismiss: { print("onDisMiss") },
                   content: { PresentedView() })
        }
    }

    struct PresentedView: View {
        @Environment(\.presentationMode) var presentationMode

        var body: some View {
            Button(action: {
                //presentationMode : 뷰가 가지는 환경변수 이다.
                //isPresented 프로퍼티 : 띄어 져있냐?
                //dismiss() : 닫아라
                if self.presentationMode.wrappedValue.isPresented {
                    self.presentationMode.wrappedValue.dismiss()
                }
            }) {
                Text("Tap to Dissmiss").font(.title).foregroundColor(.red)
            }
        }
    }
    ```

  * presentationMode 를 사용 하지 않고 State, Binding 을 이용하여 뷰 출력 닫기
  
    ``` swift
    struct Home: View {
        @State private var isPresented = false
        var body: some View {
            Button(action: {
                self.isPresented.toggle()
            }) {
                Text("Present").font(.title).foregroundColor(.black)
            }
            .sheet(isPresented: $isPresented,
                   onDismiss: { print("onDisMiss") },
                   content: { PresentedView(isPresented: self.$isPresented) })
        }
    }

    struct PresentedView: View {
        @Binding var isPresented: Bool
        var body: some View {
            Button(action: {
                self.isPresented = false
            }) {
                Text("Tap to Dissmiss").font(.title).foregroundColor(.red)
            }
        }
    }
    ```
