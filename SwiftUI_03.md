# SwiftUI Rect
SwiftUI Recture

3강 실전 앱 구현하기
===========
## 1. 기본 뷰 구성하기

<img src = "https://github.com/HwangWoonChun/SwiftUIRecture/blob/master/image/reimage_01.png" width = 128 height = 227>

1. 이미지뷰 복습
    * clipped() : 뷰를 벗어나는 이미지 잘라주기 
        ``` swift
        struct Home: View {
            var body: some View {
                HStack {
                    Image("apple")
                        .resizable()   //SwiftUI의 이미지는 기본적으로 이미지 원본을 유지하려고 하는데 resizable은 이를 커스터마이징 하기 위함이다.
                        .scaledToFill() //원본 비율을 유지하되 최대 크기로 늘어나는데 가로세로 중 큰것을 선택하여 크기 재조정, fit 은 적은것을 선택
                        .frame(width: 140)
                        .clipped()
                }.frame(height: 150)
            }
        }
        ```

2. 셀 구조 잡기 : HStack 안에 여러 스택 중첩

    * EmptyView() : Stack 뷰 안에 하나 이상의 뷰를 넣어야하기 때문에 추가할 기능을 구현하기전에 미리 아무기능도 아무 공간도 차지하지 않는 뷰를 집어 넣는 것
    
        ``` swift
        struct Home: View {
            var body: some View {
                HStack {
                    Image("apple")
                        .resizable()
                        .scaledToFill()
                        .frame(width: 140)
                        .clipped()

                    VStack {
                        EmptyView()
                        HStack {
                            EmptyView()
                        }
                    }

                }.frame(height: 150)
            }
        }
        ```


<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/03Rect_02.png" width = 259 height = 119>

3. 컨텐츠 추가하기

``` swift
struct Home: View {
    var body: some View {
        HStack {
            Image("apple")
                .resizable()
                .scaledToFill()
                .frame(width: 140)
                .clipped()
            
            VStack(alignment: .leading) {
                Text("백설공주 사과").font(.headline).fontWeight(.medium).padding(.bottom, 6)
                Text("달콤한 맛이 좋은 과일").font(.footnote).foregroundColor(.secondary)
                HStack {
                    Text("2100").font(.headline)
                    Text("원").font(.footnote)
                    
                    Spacer()    // Hstack 뷰 우측을 가득 채워 전체적으로 좌측으로 이동
                    
                    Image(systemName: "heart").imageScale(.large).foregroundColor(Color("peach")).frame(width: 32, height: 32)
                    Image(systemName: "cart").imageScale(.large).foregroundColor(Color("peach")).frame(width: 32, height: 32)
                }
            }
            
        }.frame(height: 150)
    }
}
```

<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/03Rect_04.png" width = 772 height = 328>

4. 형태 다듬기

*. padding : 적용될 방향과 폭을 지정하면 그값을 반영하여 여백 추가

``` swift
.padding(.vertical) == .padding([.top, .bottom])
.padding(.horizontal) == .padding([.leading, .trailing])
.padding() == .padding(.all)== .padding([.vertical, .horizontal])
```
*. colorInvert : 색상반전, primary는 검정색

*. shadow : .background(Color.primary.colorInvert()), .cornerRadius(6) 이 줄을 삭제하면 전체가 쉐도우 먹는다. 뷰의 가장자리에만 그림자 효과를 넣으려면 뷰의 배경색을 불투명하게 하고 이미 작성한 수식어들의 효과를 먼저 반영 시킨뒤, 해당뷰의 쉐도우 수식어를 추가해야한다.

``` swift
struct Home: View {
    var body: some View {
        HStack {
            Image("apple")
                .resizable()
                .scaledToFill()
                .frame(width: 140)
                .clipped()
            
            VStack(alignment: .leading) {
                Text("백설공주 사과").font(.headline).fontWeight(.medium).padding(.bottom, 6)
                Text("달콤한 맛이 좋은 과일").font(.footnote).foregroundColor(.secondary)
                
                Spacer()
                
                HStack {
                    Text("2100").font(.headline)
                    Text("원").font(.footnote)
                    
                    Spacer()    // Hstack 뷰 우측을 가득 채워 전체적으로 좌측으로 이동
                    
                    Image(systemName: "heart").imageScale(.large).foregroundColor(Color("peach")).frame(width: 32, height: 32)
                    Image(systemName: "cart").imageScale(.large).foregroundColor(Color("peach")).frame(width: 32, height: 32)
                }
                
            }
            .padding([.leading, .bottom], 12)
            .padding([.top,.trailing])
            
        }
        .frame(height: 150)
        .background(Color.primary.colorInvert())
        .cornerRadius(6)
        .shadow(color: Color.primary.opacity(0.33), radius: 1, x: 2, y: 2)
        .padding(.vertical, 8)
    }
}
```
5. 뷰 구조 정리해보기
* 최상위 HStackView에 Command + 클릭 > embed in VStack > Vstack으로 묶인 HStack Command + 클릭 > extract subview > 서브뷰 구성

``` swift
import SwiftUI

struct Home: View {
  var body: some View {
    VStack {
        ExtractedView()
    }
  }
}


// MARK: - Previews

struct Home_Previews: PreviewProvider {
  static var previews: some View {
    Home()
  }
}

struct ExtractedView: View {
    var body: some View {
        HStack {
            Image("apple")
                .resizable()
                .scaledToFill()
                .frame(width: 140)
                .clipped()
            
            VStack(alignment: .leading) {
                Text("백설공주 사과")
                    .font(.headline)
                    .fontWeight(.medium)
                    .padding(.bottom, 6)
                
                Text("달콤한 맛이 좋은 과일의 여왕 사과. 독은 없고 꿀만 가득해요!")
                    .font(.footnote)
                    .foregroundColor(.secondary)
                
                Spacer()
                
                HStack(spacing: 0) {
                    Text("₩").font(.footnote)
                        + Text("2100").font(.headline)
                    
                    Spacer()
                    
                    Image(systemName: "heart")
                        .imageScale(.large)
                        .foregroundColor(Color("peach"))
                        .frame(width: 32, height: 32)
                    
                    Image(systemName: "cart")
                        .foregroundColor(Color("peach"))
                        .frame(width: 32, height: 32)
                }
            }
            .padding([.leading, .bottom], 12)
            .padding([.top, .trailing])
        }
        .frame(height: 150)
        .background(Color.primary.colorInvert())
        .cornerRadius(6)
        .shadow(color: Color.primary.opacity(0.33), radius: 1, x: 2, y: 2)
        .padding(.vertical, 8)
    }
}
```

* productRow 파일을 추가하여 구조를 간결하게 표기

``` swift
struct Home: View {
  var body: some View {
    VStack {
        ProductRow()
        ProductRow()
        ProductRow()
    }
  }
}


// MARK: - Previews

struct Home_Previews: PreviewProvider {
  static var previews: some View {
    Home()
  }
}
```

``` swift
struct ProductRow: View {
  var body: some View {
    HStack {
        productImageView
        productDescription
    }
    .frame(height: 150)
    .background(Color.primary.colorInvert())
    .cornerRadius(6)
    .shadow(color: Color.primary.opacity(0.33), radius: 1, x: 2, y: 2)
    .padding(.vertical, 8)
  }
}


extension ProductRow {
    
    var productImageView: some View {
        Image("apple")
        .resizable()
        .scaledToFill()
        .frame(width: 140)
        .clipped()
    }
    
    var productDescription: some View {
        VStack(alignment: .leading) {
            Text("백설공주 사과")
                .font(.headline)
                .fontWeight(.medium)
                .padding(.bottom, 6)
            
            Text("달콤한 맛이 좋은 과일의 여왕 사과. 독은 없고 꿀만 가득해요!")
                .font(.footnote)
                .foregroundColor(.secondary)
            
            Spacer()
            
            footerView
        }
        .padding([.leading, .bottom], 12)
        .padding([.top, .trailing])
    }

    var footerView: some View {
        HStack {
            Text("2100").font(.headline)
            Text("원").font(.footnote)
            Spacer()    // Hstack 뷰 우측을 가득 채워 전체적으로 좌측으로 이동
            Image(systemName: "heart").imageScale(.large).foregroundColor(Color("peach")).frame(width: 32, height: 32)
            Image(systemName: "cart").imageScale(.large).foregroundColor(Color("peach")).frame(width: 32, height: 32)
        }
    }
}
```
## 2. 컬러익스텐션 활용하기

``` swift
extension Color {
    static let peach = Color("peach")
    static let primaryShadow = Color.primary.opacity(0.2)
    static let secondaryText = Color(hex: "#6e6e6e")
    static let background = Color(UIColor.systemGray6)
    
    init(hex: String) {
        let scanner = Scanner(string: hex) //문자열 Parser 클래스
        _ = scanner.scanString("#") //ios13, #문자 제거
        
        var rgb: UInt64 = 0
        scanner.scanHexInt64(&rgb)  //문자열을 > Int64타입으로 변환 하고 rgb라는 변수에 저장
        
        let r = Double((rgb >> 16) & 0xFF) / 255.0  //비트값을 오른쪽으로 16번 이동, 좌측 문자열 2개 추출
        let g = Double((rgb >> 8) & 0xFF) / 255.0 //중간문자열 2개 추출
        let b = Double((rgb >> 0) & 0xFF) / 255.0 //우측 문자역 2개 추출
        
        self.init(red: r, green: g, blue: b)
    }
}
```

## 3. Product 모델 적용하기


``` swift
import Foundation

struct Product {
  let name: String
  let imageName: String
  let price: Int
  let description: String
  var isFavorite: Bool = false
}


let productSamples = [
  Product(name: "나는야 무화과", imageName: "fig", price: 3100, description: "소화가 잘되고 변비에 좋은 달달한 국내산 무화과에요. 고기와 찰떡궁합!"),
  Product(name: "유기농 아보카도", imageName: "avocado", price: 2900, description: "미네랄도 풍부하고, 요리 장식과 소스로도 좋은 아보카도입니다"),
  Product(name: "바나나 안 바나나", imageName: "banana", price: 2400, description: "달콤한 맛의 바나나. 이렇게 맛있으니 내가 바나나 안 바나나?", isFavorite: true),
  Product(name: "아임 파인애플", imageName: "pineapple", price: 3000, description: "소화와 피로회복, 비타민까지! 파인애플로 맛과 영양까지 한번에!"),
  Product(name: "시원한 수박", imageName: "watermelon", price: 3500, description: "아이들이 너무나 좋아하는 시원하고 달콤한 하우스 수박이에요", isFavorite: true),
  Product(name: "베리베리 블루베리", imageName: "blueberry", price: 2300, description: "타임지 선정 10대 파워 푸드. 신이 내린 선물이라 불리는 블루베리에요"),
]
```
``` swift
import SwiftUI

struct ProductRow: View {
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
}


private extension ProductRow {
  // MARK: View
  
  var productImage: some View {
    Image(product.imageName)
      .resizable()
      .scaledToFill()
      .frame(width: 140)
      .clipped()
  }
  
  var productDescription: some View {
    VStack(alignment: .leading) {
      Text(product.name)
        .font(.headline)
        .fontWeight(.medium)
        .padding(.bottom, 6)
      
      Text(product.description)
        .font(.footnote)
        .foregroundColor(.secondaryText)
      
      Spacer()
      
      footerView
    }
    .padding([.leading, .bottom], 12)
    .padding([.top, .trailing])
  }
  
  var footerView: some View {
    HStack(spacing: 0) {
      Text("₩").font(.footnote)
        + Text("\(product.price)").font(.headline)
      
      Spacer()
      
      Image(systemName: "heart")
        .imageScale(.large)
        .foregroundColor(.peach)
        .frame(width: 32, height: 32)
      
      Image(systemName: "cart")
        .foregroundColor(.peach)
        .frame(width: 32, height: 32)
    }
  }
}


// MARK: - Previews

struct ProductRow_Previews: PreviewProvider {
  static var previews: some View {
    ProductRow(product: productSamples[0])
  }
}
```

``` swift
struct Home: View {
  var body: some View {
    VStack {
      ProductRow(product: productSamples[0])
      ProductRow(product: productSamples[1])
      ProductRow(product: productSamples[2])
    }
  }
}


// MARK: - Previews

struct Home_Previews: PreviewProvider {
  static var previews: some View {
    Home()
  }
}
```

## 4. Opaque type

1. some : Opaque type 의 키워드로 프로퍼티, 첨자, 함수 등의 반환 타입에 한정적으로 사용, 기존(5.1 이전) 버전에서는 프로토콜을 따르는 어떤 타입에 대해선 리턴이 불가능 했었다. some 키워드를 이용해 가능해 졌다.

``` swift
var body: some View { }
```

*. body는 반드시 반환타입이 some View일 필요는 없지만 some을 빼버리면 타입을 유추 할 수 없어 오류가 발생한다. 타입을 유추 할 수 있도록 body에 타입을 명시해주면 사용이 가능하다.

``` swift
struct Home: View {
  var body: Text {
    Text("hello")
  }
}
```

*. 하지만 Text가 가지고있는 수식어들을 사용하려면 반환타입을 추가해줘야 한다.

``` swift
struct Home: View {
    var body: ModifiedContent<Text, _BackgroundModifier<Color>> {
        Text("hello")
        .foregroundColor(.white)
        .background(.Color.black)
    }
}
```

2. Opaque type이 필요한 이유 : 

    1) 타입 정보를 숨기고 프로토콜에 대한 정보만 남긴채(정보은닉) API를 사용 할 수 있도록 도와준다.
    2) SwiftUI는 구조체와 제너릭을 활용하고 있어, 뷰를 추가 할때마다 새로운 반환타입이 만들어진다.
    3) 프레임워크나 라이브러리 제공자 입장에서 구현에 사용되는 타입들을 명시하는 대신 API를 추상화하고 모듈간 결합성을 낮출 수 있다.

*. 항상 텍스트만 반환 할 것인가? 뷰 프로토콜을 준수하는 뷰를 반환할 것 인가?

``` swift
func someView() -> Text {
    Text("")
}

func someValue() -> some View {
    Image("Hello")
}
```

3. 타입 추상화 :

    1) 불투명 타입의 다른 명칭은 "리버스 제너릭" 제너릭은 함수 Caller에서 Callle의 타입을 결정 하고, 불투명 타입은 Callee에서 Caller의 타입을 반환 한다.

*. 제너릭 예제

``` swift
func genericFunction<T: Animal> (_ animal: T) {}
genericFunction(Dog())
```

4. 정적 타입 시스템 : 

    1) 제너릭이나 불투명 타입은 런타임시 타입이 결정된다.
    
``` swift
protocol Animal {}

struct Dog: Animal { var color = "brown" }

let dog: some Animal = Dog()

dog.color //컴파일 단계에서 error
(dog as! Dog).color //ok
```    

5. 타입 정체성(불투명 타입의 단점) :

    1) 함수를 호출 할때마다 서로 다른 타입이 반환 될 수 있어 타입에 대한 정보를 잃을 수 있다.
    
``` swift
func protocolReturn() -> Animal {
    .random ? Dog() : Cat()
}
let animal: Animal = protocolReturn() //CAT? DOG?
```    

*. foo는 실제타입인 someType 에 대한 정보를 잃어버리고, p 프로토콜 타입 정보만 가지게 되어 그 자신의 프로토콜을 준수하지 않는 것으로 여겨 컴파일 오류

*. self, associatedtype(=typealias)가 선언된 프로토콜은 그 타입에 대한 명시나 추론이 가능해야 한다.

``` swift
protocol P {}
struct SomeType: P {}
func nested<T: P>(_ param: T) -> P {
    param
}

let foo: P = nested(SomeType())
let bar = nested(foo) //error
```

6. 주의사항

``` swift
func returnConcreteType() -> some Animal {
    let result: Animal = Dog()
    return result //error
}
```

``` swift
struct Home: View {
    var body: some View{
        .random() ? Rectangle() : Circle()  //error
    }
}

//AnyView로 
struct Home: View {
    var body: some View{
        .random() ? AnyView(Rectangle()) : AnyView(Circle())
    }
}
```

7. 불투명 타입내용 정리
    
    1) 불투명 타입은 타입은 숨기고, 구현에 대한 정보를 유저에게 숨기고, 특정 프로토콜을 따르는 API 라는 정보만 전달
    2) 프로토콜 타입을 반환하면서도 타입에 대한 정체성을 보장
    3) some 키워드 이용 : 프로퍼티, 첨자(script), 함수에 대한 반환 타입에만 가능 타입은 프로토콜 / 클래스 / Any, AnyObject로 한정
