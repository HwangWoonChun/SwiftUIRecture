# SwiftUI Rect
AutoLayout Recture

2강 뷰 구성하기
===========
## 1. UIKit VS SwifUI

1. View and Controls

UIKit | SwiftUI |
---- | ---- |
UILabel | Text |
UITextField | TextField |
UITextField(secure) | SecureField |
UIButton | Button |
UImageView | Slider |
UISlider | Text |
UIPickerView | Picker(WheelPickerStyle) |
UISegmentedControl | Picker(SegmentedPickerStyle) |
UITextView | 동일요소없음 |
UIStackView | HStack, VStack |
UIScrollView | ScrollView |
UITableView(Plain, Grouped) | List(none, GroupedListStyle) |
UICollectionView | 동일요소없음 |

* * *

2. ViewControllers

UIKit | SwiftUI |
---- | ---- |
UIViewController | View |
UINavigationController | NavigationView |
UITabBarControlller | TabView |
UISplitViewController | NavigationView |
UITableViewController | List |
UICollectionViewController | 동일요소없음 |
UIAlertController(actionSheet style) | ActionSheet |
UIAlertController(alert style) | Alert |


## 2. Text(텍스트)
1. UILabel과 비슷하지만, Button, Picker, Toggle 등 에서 UI 구성할때 텍스트를 사용하는 경우가 많다.

<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect2_3.png" width = 250 height = 380>   <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect2_4.png" width = 350 height = 380>

``` swift
struct ContentView: View {
    let egde = EdgeInsets(top: 50, leading: 5, bottom: 5, trailing: 5)
    var body: some View {
        VStack(spacing: 30) {
            Text("폰트 설정, 폰트의 굵기")
                .font(.title)
                .fontWeight(.black)
            Text("글자색, 패딩, 백그라운드 색")
                .foregroundColor(.white)
                .padding(egde)
                .background(Color.blue)
            Text("커스텀 폰트")
                .bold()
                .italic()
                .underline()
                .strikethrough()
            Text("라인수 제한asdfasdfasdfasdfasdfasdfasdfasdfasdfasdfasdfaasdfasdf\n하하\nkk")
                .lineLimit(2)
                .multilineTextAlignment(.trailing)
                .background(Color.blue)
                //.fixedSize() //주어진공간의 크기가 작아도 텍스트 생략하지 않고 표현되도록 설정
            Text("자간, 기준선").kerning(19)
                + Text("조정").baselineOffset(8)
                    .font(.system(size:30))
        }
    }
}
```
* 주요 수식어
  - fixedSize : 주어진 공간의 크기가 작아도 텍스트를 생력하지 않고 표현(오른쪽 스크린샷 참조)
  - kernering : 자간(텍스트 좌우 간격)
  - baseLineOffSet : 기준선(텍스트 상하 간격)
  
* * *

2. 수식어 순서 유의사항(1) : 순서가 중요하다!  

``` swift
struct ContentView: View {
    let egde = EdgeInsets(top: 50, leading: 5, bottom: 5, trailing: 5)
    var body: some View {
        VStack(spacing: 30) {
            Text("폰트 설정, 폰트의 굵기")
                .font(.title)   //Text 프로토콜
                .bold()         //Text 프로토콜
                .padding()      //View 프로토콜
        }
    }
}
```
``` swift
struct ContentView: View {
    let egde = EdgeInsets(top: 50, leading: 5, bottom: 5, trailing: 5)
    var body: some View {
        VStack(spacing: 30) {
            Text("폰트 설정, 폰트의 굵기")
                .bold()         //Text 프로토콜
                .padding()      //View 프로토콜
                .font(.title)   //View 프로토콜
        }
    }
}
```
``` swift
struct ContentView: View {
    let egde = EdgeInsets(top: 50, leading: 5, bottom: 5, trailing: 5)
    var body: some View {
        VStack(spacing: 30) {
            Text("SwiftUI")
                .padding()      //View 프로토콜
                .bold()         //컴파일 오류 : View 프로토콜에는 bold 수식어가 없다.
                .font(.title)   //View 프로토콜에는 font 수식어가 있다.
        }
    }
}
```
* 해당 수식어의 반환 타입이 무엇인지 확인이 필요하다. 
* 수식어는 이전 뷰를 감싼 새로운 뷰를 만들어 내고 다시 그 뷰를 감싼다.

* * *

3. 수식어 순서 유의사항(1) : 순서에 따라 화면이 달라진다! 

* 텍스트 생성 > 텍스트 크기만큼 배경 추가 > 여백 추가
<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect2_7.png" width = 50 height = 40>

``` swift
struct ContentView: View {
    var body: some View {
        VStack(spacing: 30) {
            Text("SwiftUI")
                .background(Color.yellow)
                .padding()
        }
    }
}
```

* 텍스트 생성 > 여백 추가 > 배경 추가
<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect2_6.png" width = 50 height = 40>

``` swift
struct ContentView: View {
    var body: some View {
        VStack(spacing: 30) {
            Text("SwiftUI")
                .padding()
                .background(Color.yellow)
        }
    }
}
```

## 3. Image(이미지)

1. Default가 이미지 사이드 대로 노출된다.

<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect2_8.png" width = 150 height = 200>

``` swift
struct ContentView: View {
    var body: some View {
        HStack {
            Image("dummyImgLifestyleHimart010243X43")
            Image("dummyImgLifestyleHimart010243X43").frame(width: 150, height: 150)
        }
    }
}
```

* * *

2. Resizeable : 사이즈를 조절하는 수식어

* 기본은 Stretch 되어 나온다.

<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect2_9.png" width = 150 height = 200>

``` swift
struct ContentView: View {
    var body: some View {
        VStack {
            Image("dummyImgLifestyleHimart010243X43")
            .resizable() //Image
        }
    }
}
```

* 리딩 50 적용

<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect2_10.png" width = 178 height = 166>

``` swift
struct ContentView: View {
    var body: some View {
        HStack {
            Image("dummyImgLifestyleHimart010243X43")
                .resizable(capInsets: .init(top: 0, leading: 50, bottom: 0, trailing: 0)) //Image
            .frame(width: 150, height: 150) //View
        }
    }
}
```
* resizingMode : .title, 기본 사이즈 만큼 쪼개어 표현한다.

<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect2_11.png" width = 178 height = 166>

``` swift
struct ContentView: View {
    var body: some View {
        HStack {
            Image("dummyImgLifestyleHimart010243X43")
                .resizable(resizingMode: .tile) //Image
            .frame(width: 150, height: 150) //View
        }
    }
}
```

* resizingMode : .stretch, 꽉채워 표현한다.

<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect2_12.png" width = 178 height = 166>

``` swift
struct ContentView: View {
    var body: some View {
        HStack {
            Image("dummyImgLifestyleHimart010243X43")
                .resizable(resizingMode: .stretch) //Image
            .frame(width: 150, height: 150) //View
        }
    }
}
```

* * *

3. ContentMode : 이미지 컨텐츠를 보여주는 2가지 방식, Resizeable은 좀더 커스텀이 가능한 수식어라면 ContentMode는 OS가 결정해준다.

* scaledToFit : 이미지 원본의 비율을 그대로 유지 한체 가능한 최대 크기까지 늘어난다. 이떄 최대 크기는 주어진 공간의 너비와 높이 중 작은 것으로 결정

<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect2_13.png" width = 166 height = 166>

``` swift
struct ContentView: View {
    var body: some View {
        HStack {
            Image("dummyImgLifestyleHimart010243X43")
            .scaledToFill()
            .frame(width: 150, height: 150) //View
        }
    }
}
```

* scaledToFill : 이미지 원본의 비율을 그대로 유지 한체 가능한 최대 크기까지 늘어난다. 이때 최대 크기는 주어진 공간의 너비와 높이 중 큰 것으로 결정(이미지의 가로 세로 크기 같아 fit과 동일하게 표현됨)

<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect2_13.png" width = 166 height = 166>

``` swift
struct ContentView: View {
    var body: some View {
        HStack {
            Image("dummyImgLifestyleHimart010243X43")
            .scaledToFit()
            .frame(width: 150, height: 150) //View
        }
    }
}
```

* * *

4. aspectRatio : 이미지의 비율에 세부적인 조정 설정

``` swift
struct ContentView: View {
    var body: some View {
        HStack {
            Image("dummyImgLifestyleHimart010243X43")
            .resizable() //꽉채운뒤, 너비가 높이보다 1.6배의 비율 가지도록
            .aspectRatio(CGSize(width: 1.6, height: 1), contentMode: .fit)
            .frame(width: 150, height: 150) //View
        }
    }
}
```

* * *

5. Clipshape : 이미지를 원하는 모양으로 수정

``` swift
                .clipShape(Circle())    //원모양으로
                .clipShape(Rectangle().inset(by: 10)) //이미지 크기보다 사방으로 10씩 줄인 사각형
                .clipShape(Ellipse().path(in: CGRect(x: 10, y: 10, width: 50, height: 110)))    //크기와 위치를 직접 지정(타원형)
```

* * *

6. SF Symbols : 애플에서 제공하는 이미지 ios 13.0 

``` swift
Image(systemName: "star.circle")
```

* * *

7. Font, ImageScale 를 통한 사이즈 조절

<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect2_15.png" width = 372 height = 150>

``` swift
struct ContentView: View {
    var body: some View {
        HStack {
            Image(systemName: "star.circle")
                .font(.body)
            Image(systemName: "star.circle")
                .font(.title)
            Image(systemName: "star.circle")
                .font(.system(size: 50))
            Image(systemName: "star.circle")
                .imageScale(.large)
                .font(.system(size: 50))
        }
    }
}
```

<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect2_16.png" width = 242 height = 82>

``` swift
struct ContentView: View {
    var body: some View {
        HStack {
            Image(systemName: "star.circle")
                .font(Font.title.weight(.black))
            Image(systemName: "star.circle")
                .font(Font.title.weight(.semibold))
            Image(systemName: "star.circle")
                .font(Font.title.weight(.light))
            Image(systemName: "star.circle")
                .font(Font.title.weight(.ultraLight))
        }
    }
}
```
## 4. 뷰 레이아웃

1. 스택 : 컨텐츠로 전달된 자식 뷰들을 어떤 형태로 배치 할 것인지 결정하는 뷰

* 필수 적으로 사용
* 가로, 세로, 뷰를 겹겹이 쌓아 올리는 형태로 3가지

* * *

2. 스택뷰는 뷰 프로토콜을 준수하는 Content를 제너릭 매개변수로 받아 자식 뷰로 표현하는 제너릭 구조체

``` swift
public struct HStack<Content> : View where Content : View
```

* * *

3. 생성자 
``` swift
@inlinable public init(alignment: VerticalAlignment = .center, spacing: CGFloat? = nil, @ViewBuilder content: () -> Content)
```

* alignment

<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect02_2_1.png" width = 158 height = 558> <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect02_2_2.png" width = 158 height = 558>
``` swift
struct ContentView: View {
    var body: some View {
        HStack(alignment: .center, spacing: 50) {    //center, top 바꿔보면 확연히 결과물을 볼 수 있다.
            Rectangle()
                .fill(Color.yellow)
                .frame(width: 50, height: 50)
            
            Rectangle()
                .fill(Color.yellow)
                .frame(width: 50, height: 550)
        }
    }
}
```
* * *

4. 스택 그 자체에도 뷰 프로토콜이 가진 수식어 적용이 가능하다, 당연하지만
``` swift
struct ContentView: View {
    var body: some View {
        HStack(alignment: .center, spacing: 50) {
            Text("hi").font(.title).foregroundColor(Color.blue)
        }
        .padding()
        .border(Color.red)
    }
}
```

* 컨텐츠에 타이틀이 적용되어 있지 않다면 일괄 적용이 가능

``` swift
struct ContentView: View {
    var body: some View {
        HStack(alignment: .center, spacing: 50) {
            Text("hi")
        }
        .padding()
        .border(Color.red)
        .font(.largeTitle)
    }
}
```
* * *

5. Spacer : 뷰사이의 간격 설정을 하거나 뷰의 크기를 확정할 용도로 사용되는 레이아웃

* 파란색 화면으로 채워짐
``` swift
struct ContentView: View {
    var body: some View {
        Spacer().background(Color.blue)
    }
}
```

* 스택에서의 Spacer는 화면에 나오지 않고 단순히 자리를 차지하게 된다.
``` swift
struct ContentView: View {
    var body: some View {
        VStack {
            Spacer().background(Color.blue)
        }
    }
}
```

* Spacer를 제거하고 화면 비교

<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect_02_2_7.png" width = 432 height = 105> <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect_02_2_8.png" width = 432 height = 105>

``` swift
struct ContentView: View {
    var body: some View {
        HStack {
            Spacer().background(Color.blue) //이부분을 주석처리
            Text("spacer").font(.title).foregroundColor(Color.black)
        }
    }
}
```

* Spacer 크기 설정(직접 프레임 설정)

``` swift
struct ContentView: View {
    var body: some View {
        HStack {
            Spacer().background(Color.blue).frame(width: 100, height: 100)
            Text("spacer").font(.title).foregroundColor(Color.yellow)
        }.background(Color.blue)
    }
}
``` 

* Spacer 크기 설정(최소 크기 설정)

<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect_02_2_6.png" width = 373 height = 101>

``` swift
struct ContentView: View {
    var body: some View {
        HStack {
            Spacer(minLength: 10)
            Text("spacasdfasdfasdfasdfasfasdfasdfasasdfasdfaser").font(.title).foregroundColor(Color.yellow)
        }.background(Color.blue)
    }
}
``` 
