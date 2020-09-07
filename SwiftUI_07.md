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

## 2. CustomStyle

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
