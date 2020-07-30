# SwiftUI Rect
# 

4강 프리뷰
===========
## 4-1. 기본기 다지기

## 1. 프리뷰
> 프리뷰는 쉽고 빠른 화면결과를 피드백 받을 수 있지만, 다른 기능들도 있다. 동일화면이 다양한 기기에서 어떻게 보이는지, 버튼 선택시 어떻게 되는 지 등등

**1) 프리뷰 동작과정**

* XCode에서의 동작
  1. 현재 Editor에서 PreviewProvider를 준수하는 타입이 있는지 확인
  2. PreviewProvider의 Preview 타입 프로퍼티를 생성
  3. 시뮬레이터에 연결한 기기의 형태로 브리뷰 컨테이너 렌더링(기본은 IPhone8)
  
* 왜 빠른가?
  * PreviewProvider 타입을 작업중인 파일과 더불어 렌더링 시키는데 그외의 파일들은 분리하고 작업중인 파일만 컴파일 한다.
  * DerivedData 폴더를 보면 프리뷰용이 따로 존재 한다. 소스 에디터를 분할 시키면 분할 시킨 만큼 생성된다.
  
**2) 최적화**

* 디버그 시 no optimization[-Onone] 으로 릴리즈 시 Optimize for Speed[-O] 로 최적화 모드가 설정되는데 
  프리뷰는 no optimization[-Onone] 이다. 이 때문에 릴리즈 하는 경우 프리뷰 와 관련된 코드가 모두 무시된다. 
  만약 디버그 모드의 최적화 레벨을 Optimize for Speed[-O] 로 바꾸면 에러가 난다.

**3) 자동 프리뷰 갱신**

* 자동 프리뷰 갱신이 안되는 케이스
  * 프로퍼티 삭제 추가
  * 저장 프로퍼티 값 변경
  * 뷰의 타입 혹은 이름 변경
  * 앱을 수동으로 빌드
  
* 자동 프리뷰 갱신이 되는 경우
  * 파일의 TopLevel
  * 구조체의 구현
  * 함수 범위 안에서 코드를 변경하는 경우
  * 연산프로퍼티(입력값이 없는 함수와 동일 하기 때문이다.)
  
    ``` swift
    //top Level(파일의 탑레벨)
    struct Home: View {
        //struct implementation(구조체 구현)
        var body: some View {
            //computed property(속성)
            Text("Hello, World!")
        }
        func someMethod() {
            //method
        }
        //struct implementation
    }
    //top Level(파일의 탑레벨)
    func someFunction() {
        //method
    }
    ```
  
**4) 프리뷰 수식어**

* 기기 지정
  ``` swift
  struct Home_Previews: PreviewProvider {
      static var previews: some View {
          Home().previewDevice("iPhone 11 Pro")
      }
  }
  ``` 
  
* 여러 기기에서 보기(List 뷰 이용)
  ``` swift
  struct Home_Previews: PreviewProvider {
      static var previews: some View {
          Group {
              Home().previewDevice("iPhone 11 Pro")
              Home().previewDevice("iPhone 11 Pro Max")
          }
      }
  }
  ``` 
  
  ``` swift
  struct Home_Previews: PreviewProvider {
      static var previews: some View {
          ForEach(["iPhone 11 Pro", "iPhone 11 Pro Max", "iPhone SE"], id: \.self) {
              Home().previewDevice(PreviewDevice(rawValue: $0))
          }
      }
  }
  ``` 
  
* 이름 지정 하기
  ``` swift
  struct Home_Previews: PreviewProvider {
      static var previews: some View {
          ForEach(["iPhone 11 Pro", "iPhone 11 Pro Max", "iPhone SE"], id: \.self) {
              Home().previewDevice(PreviewDevice(rawValue: $0))
          }
      }
  }
  ``` 
  
* 레이 아웃 변경하기 : 화면 크기 조절
  * device : 컨테이너가 기기의 본래 크기를 가진다.
  * sizethatFits : 컨테이너가 프리뷰 크기를 맞춘다.
  * fixed(width: height:) : 개발자가 지정한 크기로 컨테이너를 고정한다. 가로 모드일때 사용된다.
    
    ``` swift
    struct Home_Previews: PreviewProvider {
        static var previews: some View {
            ForEach(["iPhone 11 Pro", "iPhone 11 Pro Max", "iPhone SE"], id: \.self) {
                Home().previewDevice(PreviewDevice(rawValue: $0)).previewDisplayName("\($0) haha").previewLayout(.sizeThatFits)
            }
        }
    }
    ``` 

## 2. EnviormentValues

> 뷰를 구성하는데 필요한 각종 환결 설정과 정보를 관리하는 타입
> UIKit에서는 UITraitCollection 등과 같은 클래스에 관리하는 속성(colorScheme, local, timezone 등)을 이제 EnviormentValue 구조체에 하나로 담아 관리한다.
> 하위 뷰는 상위 뷰의 환경요소를 그대로 가져오며, 하위뷰의 환경을 변경 시 우선 변경 된다.

``` swift
public struct EnvironmentValues : CustomStringConvertible { }
```

**1) EnviormentValues 수식어**

``` swift
@inlinable public func environment<V>(_ keyPath: WritableKeyPath<EnvironmentValues, V>, _ value: V) -> some View
```

* enviormnet 수식어 : 텍스트랑 수식어가 비슷해보이지만 자식뷰에게 영향을 끼치는게 Enviorment이다.

  <table><tr><td>
  
    <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_05_01_157×362.png" width = 157 height = 362>
  
    ``` swift
    struct Preview_Previews: PreviewProvider {
        static var previews: some View {
            Group {
                Text("Hello, World!").frame(width: 100)
                    .environment(\.font, .title)
                Text("Hello, World!").frame(width: 100)
                    .environment(\.font, .title)
                    .environment(\.lineLimit, 1)
                Text("Hello, World!").frame(width: 100)
                    .environment(\.font, .title)
                    .environment(\.lineLimit, 1)
                    .environment(\.minimumScaleFactor, 0.5)
            }.previewLayout(.sizeThatFits)
        }
    }
    ```
  </table></tr></td>
    
* @enviorment

  * 특정 뷰에서 이 요소를 읽어와 뷰를 구성해야 할때 사용 한다.
  * 이런 녀석을 프로퍼티 래퍼라고 하는데 추후 적용
  
    ``` swift
    struct Home: View {
        @Environment(\.layoutDirection) var layoutDirection

        var body: some View {

            if layoutDirection == .leftToRight {
                return Text("leftToRight")
            } else {
                //아랍어 경우 right to left 이다.
                return Text("rightToLeft")
            }

        }

    }
    ```
    
* Custom Enviorment

  * 시스템에서만 제공하는게 아니라 앱에 필요한 환경 변수를 직접 만들 수 있다.
  * EnviormentKey 프로토콜을 채택한 타입을 만들고 defaultValue 타입 프로퍼티를 정의 해야한다. 해당키는 기본 값의 타입을 정한다. 아래는 String으로 임의 지정
  
      ``` swift
      struct Home: View {
          @Environment(\.layoutDirection) var layoutDirection

          var body: some View {
              MySubView().environment(\.myEnviorment, "hello world")
          }
      }

      struct MySubView : View {
          //정의한 MyEnviormentKey를 EnviormentValues 타입에 있는 첨자를 이용해 다를 수 있다.
          @Environment(\.myEnviorment) var myValue
          var body: some View {
              Text("\(myValue)")
          }
      }

      struct MyEnviormentKey: EnvironmentKey {
          static let defaultValue: String = ""
      }

      extension EnvironmentValues {
          var myEnviorment: String {
              get {
                  self[MyEnviormentKey.self]
              }
              set {
                  self[MyEnviormentKey.self] = newValue
              }
          }
      }
      ```
      
## 4-2. 실전앱 다루기

## 1. 프리뷰 활용하기
> 자주 비교하는 기기들을 지정하고 쉽게 재사용 할수 있도록 프리뷰에서만 하는 용도의 프리뷰 만들기

**1) 커스텀 프리뷰 코드 작성**

  ```swift
  struct Preview<V: View>: View {

      private func previewSource(device: Device) -> some View {
          source
              .previewDevice(PreviewDevice(rawValue: device.rawValue))
              .previewDisplayName(device.rawValue)
      }

      enum Device: String, CaseIterable {
          case iPhone8 = "iPhone 8"
          case iPhone11 = "iPhone 11"
          case iPhone11Pro = "iPhone 11 Pro"
          case iPhone11ProMax = "iPhone 11 Pro Max"
      }

      let source: V

      var device: [Device] = [.iPhone11Pro, .iPhone11ProMax, .iPhone8]
      var displayDarkMode = true

      var body: some View {
          Group {
              ForEach(device, id:\.self) {
                  self.previewSource(device: $0)
              }
              if !device.isEmpty && displayDarkMode {
                  self.previewSource(device: device[0])
                      .preferredColorScheme(.dark)
              }
          }
      }
  }

  struct Preview_preivews: PreviewProvider {
      static var previews: some View {
          Preview(source: Text("Hello world"))
      }
  }
  ```

**2) 홈(Home)**

  ```swift
  struct Home_Previews: PreviewProvider {
      static var previews: some View {
          Preview(source: Home(store: Store()))
      }
  }
  ```

**3) productRow**

  ```swift
  struct ProductRow_Previews: PreviewProvider {
      static var previews: some View {
          Group {
              ForEach(productSamples) {
                  ProductRow(product: $0)
              }
              ProductRow(product: productSamples[0])
                  .preferredColorScheme(.dark)
          }
          .padding()
          .previewLayout(.sizeThatFits)
      }
  }
  ```

**4) productDetail : 기본적으로 3개인 디바이스를 두개 추가 해봄 **

  ```swift
  struct ProductDetailView_Previews: PreviewProvider {
      static var previews: some View {
          let source1 = ProductDetailView(product: productSamples[0])
          let source2 = ProductDetailView(product: productSamples[1])
          return Group {
              Preview(source: source1)
              Preview(source: source2, device: [.iPhone11Pro], displayDarkMode: false)
          }
      }
  }
  ```
  
## 4-3. Identifiable Protocol

## 1. 값 타입과 참조타입의 개체 동등성

```swift
struct Animal: Equatable {
    let name : String
}

let animal1 = Animal(name: "호랑이")
let animal2 = Animal(name: "호랑이")
    
if animal1 == animal2 {
  print("같데")	//여기 걸린다.
}
```

* 값 타입은 상태 값을 통해 동등성을 비교한다.

* 참조 타입은 고유 주소값을 통해 비교한다. 하지만 여러 프로세스에 분산되어 처리되거나 저장했던 값을 불러왔을때 메모리 주소만으로 그 대상의 정체성을 완전히 식별 할 수 없는 경우들도 발생 한다.

## 2. Identifiable Protocol

* 이 프로토콜을 체택한 타입은 고유 개체를 구분하기위해 비교 알고리즘(Diff 알고리즘)에 id를 사용한다.

  ```swift
  public protocol Identifiable {

      /// A type representing the stable identity of the entity associated with `self`.
      associatedtype ID : Hashable

      /// The stable identity of the entity associated with `self`.
      var id: Self.ID { get }
  }
  ```

* Identifiable 프로토콜은 참조타입(AnyObject 타입)에 한해 id 프로퍼티에 object Identifier 라는 구조체를 사용하는데 기본값으로는 id 에 메모리 주소를 반환한다.

  ```swift
  extension Identifiable where Self : AnyObject {

      /// The stable identity of the entity associated with `self`.
      public var id: ObjectIdentifier { get }
  }
  ```

  ```swift
  //참조타입에서의 Identifiable

  let animal1 = Animal()
  let animal2 = Animal()

  if animal1.id == animal2.id {
      print("같데")
  }

  class Animal: Equatable, Identifiable {
      //Equatable 프로토콜 필수 구현
      static func == (lhs: Animal, rhs: Animal) -> Bool {
          return lhs.id == rhs.id
      }
      
      public var id: ObjectIdentifier {
          //기본적으로 메모리 반환
          return ObjectIdentifier(self)
      }
      let name : String = ""
  }
  ```

* 참조타입에서의 Identifier

  * 저장 프로퍼티로 값으로 저장
  
    ```swift
    struct Animal: Identifiable {
        let id: UUID = UUID()
    }
    ```
  
  * 연산프로퍼티로 다른프로퍼티의 값 지정

    ```swift
    struct Animal: Identifiable {
        var id: UUID {
            uuid
        }
        var uuid = UUID()
    }
    ```

  * 이미 Identifier가 있다면 프로토콜만 채택
  
    ```swift
    struct Animal {
        var id: UUID
    }

    extension Animal: Identifiable {
    }
    ```
