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
