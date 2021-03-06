# SwiftUI Rect
# 

7강 커스터 마이징
===========
## 7-1. 기본기 다지기
> ViewModifier, 커스텀 스타일, UIAppearance

## 1. View Modifier

* 수식어를 커스터마이징 할 수 있는 기능

* 뷰와는 다르게 Body가 함수 이다. Content 매개변수에 수식해야 할 대상이 전달 된다.

  ```swift
  public protocol ViewModifier {
      associatedtype Body : View
      func body(content: Self.Content) -> Self.Body
      typealias Content
  }
  ```

  ```swift
  public protocol View {
      associatedtype Body : View
      var body: Self.Body { get }
  }
  ```

* 예제 구현

  * CustomViewModifier
  
    ```swift
    struct CustomViewModifier: ViewModifier {
        var borderColor: Color = .red

        func body(content: Content) -> some View {
            content
                .font(.title)
                .foregroundColor(Color.white)
                .padding()
                .background(Rectangle().fill(Color.gray))
                .border(borderColor, width: 2)
        }
    }
    ```

  * CustomViewModifier 적용, 방법은 두가지이다.
  
    ```swift
    Text("custom").modifier(CustomViewModifier(borderColor: .black))
    ModifiedContent(content: Text("Custom ViewModifier"), modifier: CustomViewModifier(borderColor: .blue))
    ```
    
* Concat : 서로다른 역할을 하는 ViewModifier를 여러개 만들었다면 이를 결합 하는 기능

  ```swift
  extension ViewModifier {
      @inlinable public func concat<T>(_ modifier: T) -> ModifiedContent<Self, T>
  }
  ```
  
  ```swift
  struct MyModifier1: ViewModifier {
      func body(content: Content) -> some View {
          content.font(.title)
      }
  }

  struct MyModifier2: ViewModifier {
      func body(content: Content) -> some View {
          content.foregroundColor(.blue)
      }
  }
  ```
  
  ```swift
  Text("Custom ViewModifier").modifier(MyModifier1().concat(MyModifier2()))
  ```
  
* Extension 을 이용한 ViewModifier 적용 : 코드의 간결화

  ```swift
  extension View {
      func customModifier(borderColor: Color = .red) -> some View {
          self
              .modifier(CustomViewModifier(borderColor: borderColor))
      }
  }
  ```

  ```swift
  struct CustomViewModifier: ViewModifier {
      var borderColor: Color = .red

      func body(content: Content) -> some View {
          content
              .font(.title)
              .foregroundColor(Color.white)
              .padding()
              .background(Rectangle().fill(Color.gray))
              .border(borderColor, width: 2)
      }
  }

  struct Home: View {
      var body: some View {
          Text("Custom ViewModifier").customModifier(borderColor: .orange)
      }
  }
  ```

## 2. CustomStyle - 버튼

* 버튼은 buttonStyle 수식어에 PlainButtonStyle 을 적용한다.
* list는 listStyle 수식어에 GroupedListStyle 을 적용한다.
* 이처럼 사용되는 스타일 자체를 커스터마이징이 가능하다.(토글과 버튼만 가능)
* 버튼
  * 버튼스타일은 ButtonStyle, PrimitiveStyle 두가지 프로토콜이 제공되는데 makeBody 함수를 구현하고, Configuration 변수를 통해 프레임워크가 제공해주는 정보를 바탕으로 뷰를 재구성 하면 커스터마이징은 끝이다.
  
    ```swift
    public protocol ButtonStyle {
        associatedtype Body : View
        func makeBody(configuration: Self.Configuration) -> Self.Body
        typealias Configuration = ButtonStyleConfiguration
    }
    ```

    ```swift
    public protocol PrimitiveButtonStyle {
        associatedtype Body : View
        func makeBody(configuration: Self.Configuration) -> Self.Body
        typealias Configuration = PrimitiveButtonStyleConfiguration
    }
    ```

  * ButtonStyleConfiguration 버튼이 눌리고 아닐 때를 구분하여 외형을 정의하는 프로토콜
    
    ```swift
    public struct ButtonStyleConfiguration {
        public struct Label : View {
            public typealias Body = Never
        }
        public let label: ButtonStyleConfiguration.Label
        public let isPressed: Bool
    }
    ```
  
    * 예제
      
      ```swift
      struct CustomButtonStyle: ButtonStyle {
          var backgroundColor: Color = .blue
          var cornerRadius: CGFloat = 6

          func makeBody(configuration: Configuration) -> some View {
              configuration.label
                  .foregroundColor(.white)
                  .padding()
                  .background(RoundedRectangle(cornerRadius: cornerRadius).fill(backgroundColor))
                  .scaleEffect(configuration.isPressed ? 0.7 : 1.0) //눌리면 0.7배 축소
          }
      }

      var body: some View {
        Button("커스텀 버튼") {}
        .buttonStyle(CustomButtonStyle(backgroundColor: .green, cornerRadius: 20))
      }
      ```
  * PrimitiveButtonStyleConfiguration 버튼 액선 수행 조건 시정 등의 세세한 컨트롤 요구 시 사용되는 프로토콜

    ```swift
    public struct PrimitiveButtonStyleConfiguration {
        public struct Label : View {
            public typealias Body = Never
        }
        public let label: PrimitiveButtonStyleConfiguration.Label
        public func trigger()	//버튼에 지정된 액션
    }
    ```
    * 예제

      ```swift
      private struct ButtonStyleBody: View {
          let configuration: PrimitiveButtonStyleConfiguration
          let minimumDuration: Double
          @GestureState private var isPressed = false //@State 처럼 상태를 저장하는 역할이지만, 제스쳐가 동작하는 순간에만 값이 변화했다가 제스처 인식이 종료되면 다시 초깃값으로 돌아온다.

          var body: some View {
              //minimumDuration : 롤프레스가 인식하는데 까지 최소 시간
              //
              let longPress =
                  LongPressGesture(minimumDuration: minimumDuration)
                          .updating($isPressed) { (value, state, _) in
                                  state = value   //value는 버튼을 누르는지의 여부 > state 저장 > isPressed에 저장
                          }
                          //지정된 minimumDuration 종료
                          //이벤트 발생
                          //트리거매소드를 호출하면 액션 발생
                          .onEnded { _ in self.configuration.trigger() }

              return configuration.label
                  .foregroundColor(.white)
                  .padding()
                  .background(RoundedRectangle(cornerRadius: 10).fill(Color.green))
                  .scaleEffect(isPressed ? 0.7 : 1.0)
                  .opacity(isPressed ? 0.6 : 1.0)
                  .gesture(longPress)
          }
      }

      struct CustomPrimitiveButtonStyle: PrimitiveButtonStyle {
          var minimumDuration = 0.5
          func makeBody(configuration: Configuration) -> some View {
              ButtonStyleBody(configuration: configuration, minimumDuration: minimumDuration)
          }
      }

      var body: some View {
        Button("커스텀 버튼") {}
          .buttonStyle(CustomPrimitiveButtonStyle(minimumDuration: 1))
      }
      ```
      
## 3. CustomStyle - 토글

* 토글의 ToggleStyle

  ```swift
  public protocol ToggleStyle {
      associatedtype Body : View
      func makeBody(configuration: Self.Configuration) -> Self.Body
      typealias Configuration = ToggleStyleConfiguration
  }
  ```

* ToggleStyle의 Configuration

  ```swift
  public struct ToggleStyleConfiguration {
      public struct Label : View {	//토글의 사용용도를 알려주는 뷰에 불과
         public typealias Body = Never
      }
      public var isOn: Bool { get nonmutating set }
      public var $isOn: Binding<Bool> { get }
  }
  ```
  
* 버튼과 동일하게 ToggleStyle 프로토콜을 채택하고 Configuration 매개변수를 제공하는 makeBody 함수를 구현하면 된다.

<table><tr><td>
  
  <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_07_01.png" width = 180 height = 20>
  <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_07_02.png" width = 180 height = 20>

  ```swift
  struct CustomToggleStyle: ToggleStyle {
      let size: CGFloat = 30

      func makeBody(configuration: Configuration) -> some View {
          let isOn = configuration.isOn
          return HStack {
              configuration.label
              Spacer()
              ZStack(alignment: isOn ? .top : .bottom) {	//좌우 대신 상하로 움직이게 onOff 여부에 따라 top, bottom 으로 정렬
                  Capsule()
                      .fill(isOn ? Color.green : Color.red)
                      .frame(width:size, height: size * 2)
                  Circle()
                      .frame(width:size-2, height: size - 2)
                      .onTapGesture {
                          withAnimation {
                              configuration.isOn.toggle()
                          }
                  }
              }
          }
      }
  }
  
  @State private var isOn = true
  var body: some View {
	  Toggle(isOn: $isOn, label: {
		  Text("커스텀 토글")
	  })
	  .toggleStyle(CustomToggleStyle())
  }
  ```
</table></tr></td>

## 3. UIAppearance
> 내부적으로 많은 부분에서 각 플랫폼별 UI 프레임워크에서 제공하는 뷰와 컨트롤을 사용한다. Swift UI에서 제공하는 API 영역외에 제어가 가능하다.

* UIAppearance
  * 이 프로토콜은 클래스의 외형객체에 수정 메세지를 던져 그와 관련된 모든 클래스의 인스턴스 속성을 일괄적으로 변경 할 수 있도록 한다.
  
```swift
public protocol UIAppearance : NSObjectProtocol {
    static func appearance() -> Self
}
```

* UINavigationBar 타이틀 커스터 마이징

  * 타이틀 생성
  
    ```swift
    struct Home: View {
        var body: some View {
            NavigationView {
                Text("Hello")
                .navigationBarTitle("Text", displayMode: .inline)
            }
        }
    }
    ```

  * 커스터마이징 방법 1) Appdelegate나 SceneDelegate 처럼 단 한번의 설정으로 앱 전체에 지속해서 통일 하고 싶다면 해당 클래스에서 적용

    ```swift
    final class AppDelegate: UIResponder, UIApplicationDelegate {
        override init() {
            UINavigationBar.appearance().largeTitleTextAttributes = [
                .foregroundColor : UIColor.systemRed,
                .kern : 5   //자간
            ]
        }
    }
    ```

  * 커스터마이징 방법 2) onAppear 함수에 적용, 이 수식어는 viewWillAppear와 동일하다, 뷰가 보여질때마다 불린다. 

    ```swift
    struct Home: View {
        var body: some View {
            SomeView().onAppear {
                UINavigationBar.appearance().largeTitleTextAttributes = [
                    .foregroundColor : UIColor.systemRed,
                    .kern : 5   //자간
                ]
            }
        }
    }

    struct SomeView: View {
        var body: some View {
            NavigationView {
                Text("Hello")
                .navigationBarTitle("Text", displayMode: .inline)
            }
        }
    }
    ```
    
  * 참조 largeTitleTextAttributes 는 displayMode .large 인 경우에만 적용되며 .inline 경우 UINavigationBar.appearance().titleTextAttributes 를 사용하면 적용이 된다.
  

* UISwitch Appearance

  * 외형 프록시(appearance()) 를 이용하여 thumbTintColor, onTintColor 의 속성을 변경

    ```swift
    struct Home: View {
        var body: some View {
            SomeView().onAppear {
                UISwitch.appearance().onTintColor = .red

                UISwitch.appearance().thumbTintColor = .green
            }
        }
    }

    struct SomeView: View {
        @State private var isOn = false
        var body: some View {
            Toggle(isOn: $isOn) {
                Text("toggle")
            }
        }
    }
    ```

## 7-2. 커스텀 뷰 만들기

## 1. Symbol 구현

* SF Symbol 을 사용하는 Image(systemName:) 를 대채하는 커스텀 뷰 만들기

  ```swift
  struct Symbol: View {
      let systemName: String
      let imageScale: Image.Scale
      let color: Color?

      init(_ systemName: String, scale imageScale: Image.Scale = .medium, color: Color? = nil) {
          self.systemName = systemName
          self.imageScale = imageScale
          self.color = color
      }

      var body: some View {
          Image(systemName: systemName)
              .imageScale(imageScale)
              .foregroundColor(color)
      }
  }
  ```

  ```swift
  //기존 코드
  Image(systemName: "heart.fill")
      .imageScale(.large)
      .foregroundColor(nil)

  //커스터마이징
  Symbol("heart.fill", scale: .large)
  ```

  * 주의점 : 이미 foregroundColor, scale 수식어가 적용된 상태이기 때문에 아래 추가 수식어는 무시된다.

    ```swift
    Symbol("heart.fill").imageScale(.large)
    ```

* ResizedImage 구현, 이미지를 크기 그대로 사용하는 경우 보다 사용환경에 맞게 조절해야 하는 경우가 빈번하다.

  ```swift
  struct ResizedImage: View {
      let imageName: String
      let contentMode: ContentMode
      let renderingMode: Image.TemplateRenderingMode?

      init(_ imageName: String, contentMode: ContentMode = .fill, renderingMode: Image.TemplateRenderingMode? = nil) {
          self.imageName = imageName
          self.contentMode = contentMode
          self.renderingMode = renderingMode
      }

      var body: some View {
          Image(imageName)
              .renderingMode(renderingMode)
              .resizable()
              .aspectRatio(contentMode: contentMode)
      }
  }
  ```
  
  * 기존 코드

    ```swift
    Image(self.product.imageName)
      .resizable()
      .scaledToFill()
    ```
    
  * 신규 코드
  
    ```swift
    ResizedImage(self.product.imageName)
    ```

## 2. ButtonStyle 구현 - ButtonStyle
> 주문하기 버튼에 눌러질때 크기가 작아지는 버튼

* ShirnkButtonStyle

	```swift
	struct ShrinkButtonStyle: ButtonStyle {
	    var minScale: CGFloat = 0.9
	    var minOpacity: Double = 0.6

	    func makeBody(configuration: Configuration) -> some View {
		configuration.label
		    .scaleEffect(configuration.isPressed ? minScale : 1)
		    .opacity(configuration.isPressed ? minOpacity : 1)
	    }
	}
	```

* 수식어 적용

	```swift
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
	    .buttonStyle(ShrinkButtonStyle())	//적용코드
	}
	```

## 3. 팝업구현하기 - ViewModifier

* 팝업 뷰

	```swift
	enum PopupStyle {
	    case none
	    case blur
	    case dimmed
	}

	// MARK: - Popup

	fileprivate struct Popup<Message: View>: ViewModifier {
	    let size: CGSize?
	    let style: PopupStyle
	    let message: Message

	    init(
		size: CGSize? = nil,
		style: PopupStyle = .none,
		message: Message
	    ) {
		self.size = size
		self.style = style
		self.message = message
	    }

	    func body(content: Content) -> some View {
		content
		    .blur(radius: style == .blur ? 2 : 0)
		    .overlay(Rectangle()
			.fill(Color.black.opacity(style == .dimmed ? 0.4 : 0)))
		    .overlay(popupContent)
	    }

	    private var popupContent: some View {
		GeometryReader {
		    VStack { self.message }
			.frame(width: self.size?.width ?? $0.size.width * 0.6,
			       height: self.size?.height ?? $0.size.height * 0.25)
			.background(Color.primary.colorInvert())
			.cornerRadius(12)
			.shadow(color: .primaryShadow, radius: 15, x: 5, y: 5)
			.overlay(self.checkCircleMark, alignment: .top)
		}
	    }

	    private var checkCircleMark: some View {
		Symbol("checkmark.circle.fill", color: .peach)
		    .font(Font.system(size: 60).weight(.semibold))
		    .background(Color.white.scaleEffect(0.8))
		    .offset(x: 0, y: -20)
	    }
	}

	//Boolean 으로 눌렸다 안눌렸다 구분 방법
	fileprivate struct PopupToggle: ViewModifier {
	    @Binding var isPresented: Bool
	    func body(content: Content) -> some View {
		content
		    .disabled(isPresented) 
		    .onTapGesture { self.isPresented.toggle() } //누르면 사라지도록
	    }
	}

	//Identifier를 이용해 눌렀다 안눌렀다 구분 방법
	fileprivate struct PopupItem<Item: Identifiable>: ViewModifier {
	    @Binding var item: Item?
	    func body(content: Content) -> some View {
		content
		    .disabled(item != nil)
		    .onTapGesture { self.item = nil }
	    }
	}

	//주문완료시 메세지
	struct OrderCompletedMessage: View {
	  var body: some View {
	    Text("주문 완료!")
	      .font(.system(size: 24))
	      .bold()
	      .kerning(2)
	  }
	}
	```
* Extension 으로 묶어 사용하기 편하도록 수정

	```swift
	extension View {

	    //팝업 조건지정하기 - Binding<Bool> 이용
	    func popup<Content: View>(
		isPresented: Binding<Bool>, //state는 이 뷰를 사용할 곳, binding은 이 뷰에서 처리
		size: CGSize? = nil,
		style: PopupStyle = .none,
		@ViewBuilder content: () -> Content
	    ) -> some View {
		if isPresented.wrappedValue {
		    let popup = Popup(size: size, style: style, message: content())
		    let popupToggle = PopupToggle(isPresented: isPresented)
		    let modifiedContent = self.modifier(popup).modifier(popupToggle) //두개의 수식어를 중첩해서 사용 할때 그 순서에 따라 차례대로 적용
		    return AnyView(modifiedContent)
		} else {
		    return AnyView(self)
		}
	    }

	    //팝업 조건지정하기 - Identifiable 이용
	    func popup<Content: View, Item: Identifiable>(
		item: Binding<Item?>,
		size: CGSize? = nil,
		style: PopupStyle = .none,
		@ViewBuilder content: (Item) -> Content
	    ) -> some View {
		if let selectedItem = item.wrappedValue {
		    let content = content(selectedItem)
		    let popup = Popup(size: size, style: style, message: content)
		    let popupItem = PopupItem(item: item)
		    let modifiedContent = self.modifier(popup).modifier(popupItem)
		    return AnyView(modifiedContent)
		} else {
		    return AnyView(self)
		}
	    }
	}
	```

* 수식어 반영

	```swift
	struct ProductDetailView: View {

	    @State private var showingPopup: Bool = false

	    // MARK: Body

	    var body: some View {
		VStack(spacing: 0) {
		    productImage
		    orderView
		}
		.popup(isPresented: $showingPopup) { OrderCompletedMessage() }
		.edgesIgnoringSafeArea(.top)
		.alert(isPresented: $showingAlert) { confirmAlert }
	    }
	}
	```
	
## 4. 빠른 주문 기능 구현하기
> 상품상세 뿐만 아니라 리스트에서도 주문하여 팝업 띄우기

* ProductRow

	```swift
	struct ProductRow: View {
	    @Environment var store: Store       //주문내역 저장하는데 사용
	    @Binding var quickOrder: Product?   //원천자료는 Home, ProductRow는 에선 동기화만
	    let product: Product

	    // MARK: Body

	    var body: some View {
		HStack {
		    productImage
		    productDescription
		}
		.frame(height: 150)
		.background(Color.primary.colorInvert())
		.cornerRadius(6)
		.shadow(color: .primaryShadow, radius: 1, x: 2, y: 2)
		.padding(.vertical, 8)
	    }

	    var footerView: some View {
		HStack(spacing: 0) {
		    Text("₩").font(.footnote)
			+ Text("\(product.price)").font(.headline)

		    Spacer()

		    FavoriteButton(product: product)

		    Symbol("cart", color: .peach)
			.frame(width: 32, height: 32)
			.onTapGesture {					//주문 로직 추가
			    self.orderProduct()
		    }
		}
	    }

	    func orderProduct() { //주문로직
		quickOrder = product
		store.placeOrder(product: product, quantity: 1)
	    }
	}
	```

* Home

	```swift
	struct Home: View {
	    @EnvironmentObject private var store: Store
	    @State private var quickOrder: Product?		//빠른주문 정보 추가
	    // MARK: Body

	    var body: some View {
		NavigationView {
		    List(store.products) { product in
			NavigationLink(destination: ProductDetailView(product: product)) {
			    ProductRow(product: product, quickOrder: self.$quickOrder)
			}
		    }
		    .navigationBarTitle("과일마트")
		}
		.popup(item: $quickOrder, content: popupMessage(product:)) 
	    }

	    func popupMessage(product: Product) -> some View {			//팝업 메세지 그리는 함수 추가
		let name = product.name.split(separator: " ").last!
		return VStack {
		    Text(name)
			.font(.title).bold().kerning(3)
			.foregroundColor(.peach)
			.padding()

		    OrderCompletedMessage()
		}
	    }
	}
	```

* 위 코드의 문제 뷰와 팝업창이 추가된 뷰가 나뉘어 반환되는 방식이기 때문에 아래의 문제 발생한다. 

	* 팝업창을 닫거나 띄우는 것만으로 네비게이션이 갱신된다.
	* 화면위치를 유지 하지 않고 최 상단으로 돌아간다.
	* 뷰 자체에 disabled 수식어 때문에 아무런 스타일을 적용하지 않았는데도 리스트에서 출력되는 상품목록들이 희미하게 보인다.
	* 팝업창의 크기 조정을 위해 edgeIgnoringSafeArea 수식어를 쓰고 싶어도 실제 컨텐츠까지 영향이 갈 수 있기 때문에 쓰기 힘들다.
	* 해결방안 : ZStack을 이용하여 새로운 뷰를 올리는 방식 채택
	

		```swift
		extension View {
		    func popupOverContext<Item: Identifiable, Content: View>(
			item: Binding<Item?>,
			size: CGSize? = nil,
			style: PopupStyle = .none,
			ignoringEdges edges: Edge.Set = .all,
			@ViewBuilder content: (Item) -> Content
		    ) -> some View  {
			let isNonNil = item.wrappedValue != nil
			return ZStack {
			    self
				.blur(radius: isNonNil && style == .blur ? 2 : 0)

			    if isNonNil {
				Color.black
				    .luminanceToAlpha()
				    .popup(item: item, size: size, style: style, content: content)
				    .edgesIgnoringSafeArea(edges)
			    }
			}
		    }
		}
		```

## 5. 기본 UI 색상 변경하기

* 네비게이션 바 타이틀 글자색 변경

```swift
class SceneDelegate: UIResponder, UIWindowSceneDelegate {
    
    var window: UIWindow?

    func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
        
        let rootView = Home()
            .accentColor(Color.primary) //네비게이션 아이템 색상 변경 primary 이니 다크 모드때 흰색, 라이트 모드에서 검정색 (백버튼)
            .environmentObject(Store())

        self.configurationAppearance()
    }
    
    func configurationAppearance() {
        //large 디스플레이
        UINavigationBar.appearance().largeTitleTextAttributes = [
            .foregroundColor: UIColor(named: "peach")!
        ]
        //inline 디스플레이
        UINavigationBar.appearance().titleTextAttributes = [
            .foregroundColor: UIColor(named: "peach")!
        ]
    }
}
```
