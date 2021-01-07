# SwiftUI Rect
# 

5강 프리뷰 활용하기
===========
## 4-1. 기본기 다지기

## 1. 프리뷰

**1) 프로퍼티 동작과정**

```swift
struct Home_Previews: PreviewProvider {
    static var previews: some View {
        Home()
    }
}
```

* 동작과정
  1. 현재 소스에디터에 PreviewProvider 프로토콜을 준수하는 타입 있는지?
  2. PreviewProvider의 필수 구현사항인 preview 타입 프로퍼티에서 뷰 생성
  3. 시뮬레이터, 연결된 디바이스 형태로 프리뷰 컨테이너 렌더링
  4. 직접 지정시엔 3번 무시하고 직접 지정된 형태로 렌더링

* 속도가 빠른이유
    1. preview 타입의 뷰와 관려된 코드만을 컴파일 한다.
    2. Dervied Data에 프리뷰만을 위한 별도의 디렉토리 생성된다.
    
* Swift 최적화 레벨
    * 기존에는 #if DEBUG 구문을 통해 지정
    * Swfit UI의 프리뷰 에선 No Optimization [-Onone] 일때만 빌드 되며 릴리즈모드 는 Optimize for Speed[-O] 을 채택한다. (반대로 하면 에러 난다.)
    
* 자동 프리뷰 갱신이 안되는 경우
    * 파일의 탑레벨
    * 구조체, 클래스의 구현
    * 키워드, 속성, 프리프로세서 구문 수정
    
* 자동프리뷰 갱신이 되는 경우
    * 함수 범위 안에서 변경(=연산프로퍼티)

* 프리뷰 수식어
    * 기기 설정
        ```swift
        struct Home_Previews: PreviewProvider {
            static var previews: some View {
                Home().previewDevice("iPhone 11 Pro")
            }
        }
        ```

    * 기기 설정(Group)      
        ```swift
        struct Home_Previews: PreviewProvider {
            static var previews: some View {
                Group {
                    Home().previewDevice("iPhone 11 Pro")
                    Home().previewDevice("iPhone 11 Pro Max")
                }
            }
        }
        ```

    * 기기설정(ForEach)
        ```swift
        struct Home_Previews: PreviewProvider {
            static var previews: some View {
                ForEach(["iPhone 11 Pro", "iPhone 11 Pro Max"], id: \.self) {
                    Home().previewDevice(PreviewDevice(rawValue: $0))
                }
            }
        }
        ```swift
        
    * 이름지정
        ```swift
        struct Home_Previews: PreviewProvider {
            static var previews: some View {
                ForEach(["iPhone 11 Pro", "iPhone 11 Pro Max"], id: \.self) {
                    Home()
                        .previewDevice(PreviewDevice(rawValue: $0))
                        .previewDisplayName($0)
                }
            }
        }
        ```

    * 컨테이너 크기
        ```swift
        struct Home_Previews: PreviewProvider {
            static var previews: some View {
                Home().previewLayout(.device) //디바이스에 맞춰
                Home().previewLayout(.sizeThatFits) // 홈화면에 맞춰
                Home().previewLayout(.fixed(width: 100, height: 100)) //크기지정 맞춰
            }
        }
        ```swift
        
**2) EnviromentValues**
> 화면에 필요한 각종 환경설정(color scheme, locale, calendar 등) UIKit에서는 UITraitCollection 에서 다양한 클래스로 나뉘어 제공했으나 Swift UI 는 모두 하나에 담음

```swift
public struct EnvironmentValues : CustomStringConvertible { }
```
* 최상위 뷰가 가지는 환경변수는 모든 자식뷰에 영향을 주고, 자식뷰에서 개별적으로 다른 환경을 구성했다면 그 뷰에 속한 자식 뷰들은 변경된 값을 적용한다.

* 수식어

    * Text에 일반적인 수식어를 적용하는 것 처럼 보이지만, 자식뷰에 영향을 주냐 안주냐에 따라 크게 달라진다.
        ```swift
            var body: some View {
                Text("Hello world").frame(width: 100)
                    .environment(\.font, .title)
                    .environment(\.lineLimit, 1)
                    .environment(\.minimumScaleFactor, 0.3) //다담지 못한다면 0.3배까지 작아저도 된다.
            }
        ```
    *  프리뷰에서 사용한다면 여러개로 나누어 매번 다른 설정을 돌아가면서 확인하지 않아도 된다.
    
        ```swift
        struct Home_Previews: PreviewProvider {
            static var previews: some View {
                Group {
                    Home()
                        .environment(\.locale, .init(identifier: "ko_kr"))
                        .environment(\.colorScheme, .light)

                    Home()
                        .environment(\.locale, .init(identifier: "en_US"))
                        .environment(\.layoutDirection, .rightToLeft)
                        .environment(\.colorScheme, .dark)
                }
            }
        }
        ```

* @Enviorment

    * @Enviorment는 프로퍼티 래퍼로, 읽기 전용으로 특정 뷰에서 EnviormentValues의 특정 요소를 읽어와 뷰 구성에 반영 할때 사용된다.
        > 프로퍼티 래퍼는 추후다룸

        ```swift
        struct Home: View {

            @Environment(\.layoutDirection) var layoutDirection

            var body: some View {
                if layoutDirection == .leftToRight {
                    return Text("leftToRight")
                } else {
                    return Text("rightToLeft")
                }
            }
        }
        ```
        
    * Custom Enviorment

        ```swift
        struct MyEnviormentKey: EnvironmentKey {
            static let defaultValue: Int = 0
        }

        extension EnvironmentValues {
            var myEnviorment: Int {
                get { self[MyEnviormentKey.self] }
                set { self[MyEnviormentKey.self] = newValue }
            }
        }

        struct MySubView: View {
             @Environment(\.myEnviorment) var myEnviorment

            var body: some View {
                Text("\(myEnviorment)")
            }
        }

        struct Home: View {
            var body: some View {
                MySubView().environment(\.myEnviorment, 10)
            }
        }
        ```

## 4-2. 실전 앱 구현하기

## 1. 프리뷰 활용하기

**1) Preview 구현**

* 프리뷰 헬퍼 파일 만들어 구현

    ```swift

    import SwiftUI

    struct Preview<V: View> : View {
        //표현될 기기 목록
        enum Device: String, CaseIterable {
            case iPhone8 = "iPhone 8"
            case iPhone11 = "iPhone 11"
        }
        //표현될 뷰
        let source: V
        //렌더링될 기기 목록 기본값
        let devices: [Device] = [.iPhone8, .iPhone11]
        //다크모드 출력여부
        var displayDarkMode = true

        func previewSource(device: Device) -> some View {
            source
                .previewDevice(PreviewDevice(rawValue: device.rawValue))
                .previewDisplayName(device.rawValue)
        }

        var body: some View {
            Group {
                ForEach(devices, id: \.self) {
                    self.previewSource(device: $0)
                }
                if !devices.isEmpty && displayDarkMode {
                    self.previewSource(device: devices[0]).preferredColorScheme(.dark)  //preferredColorScheme: 컬러모드 
                }
            }
        }
    }

    struct Preview_Previews: PreviewProvider {
        static var previews: some View {
            Preview(source: Text("Hello"))
        }
    }
    ```
    
* 프리뷰 수정하기

    * Home
    
        ```swift    
        struct Home_Previews: PreviewProvider {
            static var previews: some View {
                //Home(store: Store())
                Preview(source: Home(store: Store()))
            }
        }    
        ```
        
    * Product Row : 기기별 의미는 없고 상품 리스트 확인
  
        ```swift
        struct ProductRow_Previews: PreviewProvider {
            static var previews: some View {
                ForEach(productSamples) {
                    ProductRow(product: $0).previewLayout(.sizeThatFits).preferredColorScheme(.dark)
                }.padding()
            }
        }    
        ```
        
    * ProductDetailView : 기기별 의미는 없고 상품 리스트 확인
    
        ```swift    
        struct ProductDetailView_Previews: PreviewProvider {
            static var previews: some View {
                let source1 = ProductDetailView(product: productSamples[0])
                let source2 = ProductDetailView(product: productSamples[1])

                return Group {
                    Preview(source: source1)    //Preview에 지정된 2개 디바이스 출력
                    Preview(source: source2, devices: [.iPhone8], displayDarkMode: false) //iPhone 8 만 
                }
            }
        }
        ```

## 4-3. Identifiable Protocol

* 값 타입 에서의 비교

    * 두개의 다른 값타입은 값이 같으면 같은것으로 취급된다.

        ```swift  
        struct Animal: Equatable {
            var age = 10
            init(age: Int){
                self.age = age
            }
        }

        let dog = Animal(age: 10)
        let cat = Animal(age: 10)

        if dog == cat {
            print("같다")
        }
        ```
        
* 참조 타입 에서의 비교
    
    * 힙 메모리에 저장된 고유 주소값을 통해 정체성을 확보한다.
    

* Identifitable Protocol

    * Diff 알고리즘(비교) 에 따라 아이디를 사용하여 비교한다. 값이 같더라도 id 다르면 달라진다.
    
    * Identifiable 프로토콜은 AnyObject며 참조타입에 한해 id프로퍼티에 ObjectIdentifier 라는 구조체를 사용한다.
    
        ```swift  
        extension Identifiable where Self : AnyObject {
            public var id: ObjectIdentifier { get }
        }
        ```
        
    * SwiftUI
    
        * List, ForEach 같은 뷰 는 Identfitable 프로토콜을 요구한다. 데이터가 Identifitable Protocol 을 채택한다면 ID 값은 생략 할 수 있다.

            ```swift
            struct Home: View {
                let store: Store

                // MARK: Body

                var body: some View {

                    let dog = Animal(id: 1, age: 10)
                    let cat = Animal(id: 2, age: 10)

                    return ForEach([dog,cat]) { animal in
                        Text(String(describing: animal))
                    }
                }
            }
            //Identifiable 프로토콜은 id 변수를 채택해야한다.
            struct Animal: Equatable, Identifiable {
                var id = 0
                var age = 10

                init(id: Int, age: Int){
                    self.id = id
                    self.age = age
                }
            }
            ```
