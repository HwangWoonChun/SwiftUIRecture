# SwiftUI Rect
# 

6강 유저 입력다루기
===========
## 6-1. 기본기 다지기

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
  
* ObservableObject 프로토콜과 @ObservedObject, @Published
  
  * ObservableObject 을 준수하고 @Published를 사용하게 되면 @Published를 사용한 프로퍼티가 변경 시 뷰가 알 수 있게 해준다.

  * @ObservedObject 을 사용하게 되면 ObservableObject 프로토콜을 준수하는 모델에 해당 뷰가 의존성을 가진다고 알려주는 것이다.
  
  * @Publised

    * ObservableObject 를 통해 감지한 속성을 뷰에 반영하는 속성이다.
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
    
* 그럼 언제 사용하는가?

  * @State : 하나의 뷰에 속하는 간단한 프로퍼티 private 이다.

  * @ObservedObject : 여러개의 뷰에 속하는 복잡한 프로퍼티 이것을 사용

  * @EnviormentObject : 앱의 전반에 걸쳐 공유되는 데이터에 사용한다.



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
    
## 6-2. 실전앱 구현하기

## 1. 수량 선택하기 기능

**1) 스태퍼**

* Stepper 기본

  ``` swift
      Stepper(onIncrement: {
          //증가
      }, onDecrement:  {
          //감소
      }) {
          //레이블
      }
  ```
  
* Custom Stepper

  * 생각 해볼 문제 1) ~= 연산자 : 우변이 좌변에 포함이 되는지 안되는지 체크하는 

  * 생각 해볼 문제 2) 타입프로퍼티 : 타입프로퍼티는 해당 클래스 혹은 구조체 그 자체를 표현하기 위한 프로퍼티 이다. 그래서 타입안에 속한 인스턴스 프로퍼티에 접근 이 불가능하다.

  ``` swift

  import SwiftUI

  struct QuantitySelector: View {
      @Binding var quantity: Int
      var range: ClosedRange<Int> = 1...20
      var body: some View {
          HStack {
              Button(action: {
                  self.changeQuantity(-1)
              }) {
                  Image(systemName: "minus.circle.fill")
                      .imageScale(.large)
                      .padding()
                      .foregroundColor(Color.gray.opacity(0.5))
              }

              Text("\(quantity)")
                  .bold()
                  .font(Font.system(.title, design: .monospaced))
                  .frame(minWidth: 40, maxHeight: 60)

              Button(action: {
                  self.changeQuantity(1)
              }) {
                  Image(systemName: "plus.circle.fill")
                      .imageScale(.large)
                      .padding()
                      .foregroundColor(Color.gray.opacity(0.5))
              }
          }
      }
      private func changeQuantity(_ num: Int){
          if range ~= quantity + num {
              quantity += num
          }
      }
  }

  struct QuantitySelector_Previews: PreviewProvider {
      @State private var quantity: Int = 10
      static var previews: some View {
          //QuantitySelector(quantity: $quantity)
          //위 코드는 동작하지 않는다.
          //타입프로퍼티는 해당 클래스 및 구조체의 프로퍼티 접근이 불가능하다.
          //그래서 인스턴스 프로퍼티 타입인 quantity는
          //타입프로퍼티로 선언된(static) preview에서 사용 할 수 없다.
          //타입프로퍼티는 타입 자체를 나타내는 프로퍼티
          //임시로 상수를 사용
          QuantitySelector(quantity: .constant(0))
      }
  }
  ```
  
* Stepper 적용

  ``` swift
  struct ProductDetailView: View {
      let product: Product
      @State private var quantity: Int = 1
      // MARK: Body

      var body: some View {
          VStack(spacing: 0) {
              productImage
              orderView
          }
          .edgesIgnoringSafeArea(.top)
      }
  }
  ```

  ``` swift
      var priceInfo: some View {
          HStack {
              (Text("₩")
                  + Text("\(product.price)").font(.title)
                  ).fontWeight(.medium)
              Spacer()
              QuantitySelector(quantity: $quantity)
          }
          .foregroundColor(.black)
      }
  ```
  
**2) 햅틱적용하기**

* UIImpactFeedbackGenerator

  ```Swift
  private let softFeed = UIImpactFeedbackGenerator(style: .soft)  //부드러운
  private let rigidFeed = UIImpactFeedbackGenerator(style: .rigid)//딱딱한
  ```

  ```Swift
      private func changeQuantity(_ num: Int){
          if range ~= quantity + num {
              quantity += num
              softFeed.prepare()
              softFeed.impactOccurred(intensity: 0.8) //진동세기
          } else {
              rigidFeed.prepare()
              rigidFeed.impactOccurred()
          }
      }
  ```
  
**3) 즐겨찾기 구현하기**
> 홈, 상세 두곳에 하트 모양 이미지 적용

* ObservableObject + @EnviormentObject

  * Store에 ObservableObject 프로토콜 채택
  
  * products 프로퍼티에 @Published 적용
  
    ```Swift
    final class Store: ObservableObject {
      @Published var products: [Product]

      // MARK: Initialization

      init(filename: String = "ProductData.json") {
        self.products = Bundle.main.decode(filename: filename, as: [Product].self)
      }
    }
    ```
  * 환경 객체 주입

    ```Swift
    Delegate: UIResponder, UIWindowSceneDelegate {
      func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
          //let rootView = Home(store: Store()) 기존코드
          let rootView = Home().environmentObject(Store()) // 환경객체 주입
        }
    }
    ```

    ```Swift
    struct Home: View {
        //let store: Store //기존코드
        @EnvironmentObject private var store: Store
    }
    ```
    
* 즐겨찾기 정보 변경
  
  * 즐겨찾기 OnOff
  
    ```Swift
    extension Store {
        func toggleFavorite(of product: Product) {
            guard let index = products.firstIndex(where: {
                $0.id == product.id
            }) else { return }
            products[index].isFavorite.toggle()
        }
    }
    ```

  * Product 가 Equatable 을 채택하게 하여 이미 무엇을 비교해야 할지 판단하게 한다면 더 깔끔하게 사용 가능
  
    ```Swift
    extension Store {
        func toggleFavorite(of product: Product) {
            guard let index = products.firstIndex(of: product) else { return }
            products[index].isFavorite.toggle()
        }
    }
    ```

* Favorite 버튼 구현

  * 버튼 구현
  
    ```Swift
    import SwiftUI

    struct FavoriteButton: View {
        @EnvironmentObject private var store: Store
        let product: Product

        private var imageName: String {
            product.isFavorite ? "heart.fill" : "heart"
        }

        var body: some View {
            Button(action: {
                self.store.toggleFavorite(of: self.product)
            }) {
                Image(systemName: imageName)
                    .imageScale(.large)
                    .foregroundColor(.peach)
                    .frame(width: 32, height: 32)
            }
        }
    }
    ```
    
  * 버튼 적용
  
    * ProdcutRow
  
      ```Swift
              var footerView: some View {
                  HStack(spacing: 0) {
                      Text("₩").font(.footnote)
                          + Text("\(product.price)").font(.headline)

                      Spacer()

                      FavoriteButton(product: product)

                      Image(systemName: "cart")
                          .foregroundColor(.peach)
                          .frame(width: 32, height: 32)
                  }
              }

      ```

    * ProductDetail

      ```Swift
        var productDescription: some View {
          VStack(alignment: .leading, spacing: 16) {
            HStack {
              Text(product.name)
                .font(.largeTitle).fontWeight(.medium)
                .foregroundColor(.black)

              Spacer()

              FavoriteButton(product: product)
            }

            Text(splitText(product.description))
              .foregroundColor(.secondaryText)
              .fixedSize()
          }
        }
      ```
  * ProdcutRow의 즐겨찾기 버튼을 눌렀을 때 화면 이동을 해버린다.
  
    * 버튼 스타일을 Default 로 하면 동작의 우선권을 SuperView가 가지게 된다. 버튼 스타일을 Plain 혹은 Borderless 로 바꾸면 된다.
    
    * 버튼이 아니라 onTapGesture를 이용해도 위 방법과 동일하게 동작한다.
    
* 주문기능 구현

  * 주문 모델
  
    ```Swift
    struct Order: Identifiable {
        //1부터 시작해 주문이 들어올때마다 1씩 증가
        static var orderSequence = sequence(first: 1) { $0 + 1 }
        let id : Int
        let product: Product
        let quantity: Int

        var price: Int {
            product.price * quantity
        }
    }
    ```
    
  * Store에 주문 모델 추가

    ```Swift
    final class Store: ObservableObject {
        @Published var products: [Product]
        @Published var orders: [Order] = []
        // MARK: Initialization

        init(filename: String = "ProductData.json") {
            self.products = Bundle.main.decode(filename: filename, as: [Product].self)
        }

        func placeOrder(product: Product, quantity: Int) {
            let nextID = Order.orderSequence.next()!
            let order = Order(id: nextID, product: product, quantity: quantity)
            orders.append(order)
        }
    }
    ```
  

  * Alert 뷰와 의존성을 가지는 showingAlert, 주문하기 버튼 기능 
  
    ```Swift
    struct ProductDetailView: View {
        let product: Product
        @State private var showingAlert: Bool = false		//Alert state
        @State private var quantity: Int = 1

        // MARK: Body

        var body: some View {
            VStack(spacing: 0) {
                productImage
                orderView
            }
            .edgesIgnoringSafeArea(.top)
        //alert 수식어
            .alert(isPresented: $showingAlert) {
                confirmAlert
            }
        }
        
      var placeOrderButton: some View {
          Button(action: {
            self.showingAlert = true
        }) {
            Capsule()
                .fill(Color.peach)
                .frame(maxWidth: .infinity, minHeight: 30, maxHeight: 55)
                .overlay(Text("주문하기")
                    .font(.system(size: 20)).fontWeight(.medium)
                    .foregroundColor(Color.white))
                .padding(.vertical, 8)
        }
    }

      //알림창에 표현할 내용 정의
        var confirmAlert: Alert {
            Alert(title: Text("주문확인"),
                  message: Text("\(product.name)을 \(quantity) 개 구매 하시겠습니까?"),
                  primaryButton: .default(Text("확인"), action: {
                      self.placeOrder()
                  }),
                  secondaryButton: .cancel(Text("취소")))
        }
    }
    
    func placeOrder() {
        store.placeOrder(product: product, quantity: quantity)
    }

    ```
