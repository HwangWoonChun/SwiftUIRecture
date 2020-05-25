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
===========
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
* Body 타입을 
* * *




2. Why Rx : 코드가 새로운 데이터에 반응하고 순차적으로 분리된 방식으로 처리 하기 때문에 비동기 프로그래밍이 간소화 된다.
* * *
3. 기존 비동기 코드를 지원 하는 Swift
* Notification Center
* Delegate
* GCD : 운영체제가 스레드 풀을 관리 하는 C 기반의 API
  - ConcurrencyQueue(Global, Main) : 스레드 작업을 동시 실행(비동기)
  - SerialQueue : 스레드 작업을 순서대로 실생
* Closure
* NSThread : 스레드 풀을 직접 관리하는 API
* OperationQueue : 스레드를 큐에 넣고 운영체제가 알아서 꺼내도록 하는 API
``` swift
    //동기
    @IBAction func onLoadSync(_ sender: Any) {
        
        guard let url = URL(string: imageUrl) else { return }
        guard let data = try? Data(contentsOf: url) else { return }
        
        let image = loadImage(from: IMAGE_URL)
        imageView.image = image
    }
    
    //비동기
    private func loadImage(from imageUrl: String) {
        DispatchQueue.global().async {
            guard let url = URL(string: imageUrl) else { return }
            guard let data = try? Data(contentsOf: url) else { return }
            
            let image = UIImage(data: data)
        
            DispatchQueue.main.async {
                self.imageView.image = image
            }
        }
    }
```
* * *
4. RxSwift의 기초
  * Observable : Observable<T> 는 T형태의 데이터를 전달 할 수 있는 일련의 이벤트를 비동기적으로 생성
    * Observable 프로토콜 준수한다는 의미는 next, completed, error 3가지 이벤트를 수신 한다는 의미
    * 유한한 이벤트와 무한한 이벤트를 관찰 할 수 있는 Observable이 존재 한다.
  * Operator : 비동기 작업을 추상화 하는 메소드들의 모임
  * Schedulers : DispatchQueue와 동일하며 메인, 백그라운드 스레드 처리
* * *
5. App Architecture : MVVM은 데이터 바인딩(UI와 데이터 연결)을 제공하는 플렛폼에서 이벤트 기반의 소프트웨어용으로 개발되어 Rx와 잘 어울린다.
* * *
6. RxCocoa : UIKit, Cocoa 프레임워크 기반으로 UI/UX 개발 지원
