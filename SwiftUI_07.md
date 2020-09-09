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
