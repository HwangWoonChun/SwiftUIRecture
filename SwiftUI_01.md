# SwiftUI
SwiftUI Recture

1-1 SwiftUI 소개
===========
1. SwiftUI 등장 : WWDC 2019
* 기존 UI 프레임워크의 한계 : AppKit(OSX) + UIKit(IOS)
  - 오래된 언어 > 기능 부족, 문법 역시 생산적이지 못함
  - cf. 스위프트와 호환시 @objc 키워드
* The shortest Path to a Great App, Less Code, Better Code, Everywhere.
  - Appkit과 UIKit 이 나눠져 있지 않다.
* * *
2. SwiftUI 특성
* 코드의 길이가 적다.
* 명령형이 아닌 선언형
* 디자인 도구(시뮬레이터 없이 캔버스의 프리뷰 사용시 결과물 확인 가능)
* 모든 애플 플랫폼 지원 가능
* * *
3. 4가지 원칙
* 선언형
``` swift
let button = UIButton(type: .system)
button.setTitle("SwiftUI", for : .normal)
view.addSubview(button)
button.addTarget(self, action: #selector(buttonTap(_:)), for: .touchUpInside)
@objc func buttonTap(_ sender: UIButton) {
}
```
``` swift
Button(action: {
    print("")
}) {
    Text("") //뷰
        .font(.title) //수식어
        .foregroundColor(.black) //수식어
}
```
* 조합형 : API 에서 뷰의 조합과 분리를 간단하게 제공, 프로토콜을 통해 모든 객체에 공통적인 UI 적용 가능
* 자동화 : 많은 기능들을 API로 제공하여 자동으로 수행될 수 있게끔 제공
* 일관성 : 데이터와 UI 동기화하여 일관성 유지
* * *

1-2. Hello Swfit UI
===========
1. 프로젝트 생성 후 코드
``` swift
struct ContentView: View {	// 구조체는 상속을 받을 수 없는데 : View는 무엇을 의미하는 걸까? > 뷰 프로토콜이다. 
    var body: some View {
        Text("Hello, World!")
    }
}

struct ContentView_Previews: PreviewProvider {	//프리뷰를 위한 용도
    static var previews: some View {
        ContentView()
    }
}
```
* ContentView.swift
  - ContentView는 UIViewController 를 대채하는 역할을 한다.
  - UIViewController와 UIView를 상속받은 뷰를 사용하지 않고, 뷰 프로토콜을 준수하는 구조체를 사용한다.
  - body 프로퍼티에 UI를 구성하는 코드를 구현한다.

* 캔버스 : 스토리보드를 사용할 필요 없다, 훨씬 빠르다.
* * *
2. 뷰 프로토콜
``` swift
@available(iOS 13.0, OSX 10.15, tvOS 13.0, watchOS 6.0, *)
public protocol View {

    /// The type of view representing the body of this view.
    ///
    /// When you create a custom view, Swift infers this type from your
    /// implementation of the required `body` property.
    associatedtype Body : View

    /// Declares the content and behavior of this view.
    var body: Self.Body { get }
}
```
* SwiftUI의 컨텐트뷰는 뷰 프로토콜을 준수해야한다.
* UIKit와 비교
  - UIKit 은 UIView 클래스가 상당히 많은 수의 프로퍼티를 가지며 상속을 받으면 그대로 편리하게 사용하지만, 사용하지 않는 프로퍼티들도 가지고 있어야 한다는 단점을 지닌다.
  - SwiftUI 는 각각의 뷰 객체가 각자 자신에게 필요한 속성만 가지고 뷰를 생성하도록 구현한다. 
* Body 타입을 또 다시 준수해야 하는 재귀구조
  - body에서 반환한 타입은 내부에서 또 다시 프로토콜을 준수하는 body 프로퍼티를 구현해 View 타입을 반환해야 하는 무한 재귀호출 구조
  - Text, Stack, Group, GeometryReader 같은 컨테이너 뷰에는 더는 재귀호출이 일어 나지 않도록 Never 타입이 사용된다.
``` swift
extension Text : View {

    /// The type of view representing the body of this view.
    ///
    /// When you create a custom view, Swift infers this type from your
    /// implementation of the required `body` property.
    public typealias Body = Never
}
```
* 참조 associatedtype : Generic 에서 나온 개념, placeHolder 역할을 하며 타입의 견본을 만든다. 프로토콜이 적용 될때까지 타입이 지정되지 않는다.
``` swift
protocol ExProtocol{
    var name: String { get }
}
//스트링, Int 등의 다양한 타입으로 받을수있다면
protocol ExProtocol {
    associatedtype MyType
    var name: MyType { get }
}
```
* * *
3. UIHostingController
``` swift
    func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
        // Use this method to optionally configure and attach the UIWindow `window` to the provided UIWindowScene `scene`.
        // If using a storyboard, the `window` property will automatically be initialized and attached to the scene.
        // This delegate does not imply the connecting scene or session are new (see `application:configurationForConnectingSceneSession` instead).

        // Create the SwiftUI view that provides the window contents.
        let contentView = ContentView()

        // Use a UIHostingController as window root view controller.
        if let windowScene = scene as? UIWindowScene {
            let window = UIWindow(windowScene: windowScene)
            window.rootViewController = UIHostingController(rootView: contentView) // window.rootViewController와 동일
            self.window = window
            window.makeKeyAndVisible()
        }
    }
```

``` swift
class UIHostingController<Content> : UIViewController where Content : View
//UIViewController를 상속받고, 뷰 프로토콜을 준수하는 제네릭 매개변수 Content를 전달하는 제네릭 클래스
``` 

1-3. MVC?
===========
1. 컨트롤러없ㄷ이 뷰와 모델만 이용하여 뷰 개발이 가능하기 때문에 아직 애플 공식 패턴이 없기 때문에 여러 논의가 필요
