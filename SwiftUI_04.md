# SwiftUI Rect
# 

4강 네비게이션 뷰와 리스트
===========
## 4-1. 기본기 다지기

## 1. 버튼

**1) 버튼 표현하기**

* 단순히 텍스트만 표현

    ``` swift
    struct Home: View {
        var body: some View {
            HStack{
                Button("button") {
                    print("button")
                }
            }
        }
    }
    ```

* 버튼을 RoundedRectangle 로 표현

    ``` swift
    struct Home: View {
        var body: some View {
            HStack{
                Button(action: {
                    print("Button")
                }) {
                    Text("Button").padding().background(RoundedRectangle(cornerRadius: 10).strokeBorder())
                }
            }
        }
    }
    ```

* 버튼을 원으로 표현

    ``` swift
    struct Home: View {
        var body: some View {
            HStack{
                Button(action: {
                    print("Button")
                }) {
                    Circle().stroke(lineWidth: 2).frame(width:80, height: 80).overlay(Text("text"))
                }
            }
        }
    }
    ```

* 버튼을 이미지로 표현, 이때 원본색상을 잃어버리고 accentColor 로 덮어 버린다.

    <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect_04_01_4141.png" width = 41 height = 41>

    ``` swift
    struct Home: View {
        var body: some View {
            Button(action: {
            }) {
                Image("avocado")
                    .resizable()    //SwiftUI의 이미지는 기본적으로 이미지 원본을 유지하려고 하는데 resizable은 이를 커스터마이징 하기 위함이다. 
                    .frame(width:40,height:40)
                    .background(Color.clear)
            }
        }
    }
    ```
 
    * 원본색상 찾기 첫번째 방법 : 렌더링모드 변경

        <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect_04_02_4141.png" width = 41 height = 41>

        ``` swift
        struct Home: View {
            var body: some View {
                Button(action: {
                }) {
                    Image("avocado")
                        .renderingMode(.original) //default가 template 모드 이다. 오리지널로 바꿔주면 제대로 나온다.
                        .resizable()
                        .frame(width:40,height:40)
                        .background(Color.clear)
                }
            }
        }
        ```

    * 원본색상 찾기 두번째 방법 : 버튼 스타일 변경, PlainButtonStyle은 렌더링 모드가 original 이다.

        ``` swift
        struct Home: View {
            var body: some View {
                HStack{
                    Button(action: {

                    }) {
                        Image("avocado")
                            .resizable()
                            .frame(width:40,height:40)
                            .background(Color.clear)
                    }
                    .buttonStyle(PlainButtonStyle())
                }
            }
        }
        ```

**2) 버튼 스타일(IOS 만 보자)**

<img src = "https://d33wubrfki0l68.cloudfront.net/1a47b639da7c62e0b83ddc93ba2291fcde828281/a449a/images/buttonstyle-ios.png">

<img src = "https://d33wubrfki0l68.cloudfront.net/973c73566a73626e361f0c816017d82a7e84147a/1682d/images/buttonstyle-mac.png">

* DefaultButtonStyle : 모든 OS 기본 스타일

* BorderlessButtonStyle : 테두리가 없는 버튼

* PlainButtonStyle : IDLE(유휴 상태, 아무런 동작이 없는) 상태에서는 어떠한 시각적 요소도 적용하지 않는 버튼, 이미지 렌더링이 기본으로 오리지널 타입  



**3) 버튼이 아닌 onTapGesture를 이용하여 동작 구현, 단 기본적인 애니메이션은 사용 할 수 없다.**
    
``` swift
    struct Home: View {
        var body: some View {
            HStack{
                Image(systemName: "person.circle").imageScale(.large)
                    .onTapGesture {
                        print("gesture")
                }
            }
        }
    }
```

* * *

## 2. 네비게이션 뷰

> **네비게이션 스택을 이용해 컨텐츠 뷰들을 관리 하는 컨테이너 뷰, UINavigationController, UISplitViewController 역할 수행**

**1) 네비게이션 바 타이틀**
        
* 네비게이션 타이틀 수식어는 네비게이션 뷰를 수식하는 곳이 아닌 내부에서 사용한다. 네비게이션 뷰를 수식하는 바깥쪽에 위치 하게 되면 모든 자식뷰가 동일한 타이틀을 가지기 때문이다.
        
* 그래서 UIKit에서는 타이틀을 ViewController에서 지정 했었다.

* 네비게이션 뷰에 사용되는 수식어들을 preference 라는 기능을 통해 하위 뷰가 상위 뷰에 데이터를 전달 하는 방식을 이용한다.
        
    ``` swift
    struct Home: View {
        var body: some View {
            NavigationView {
                Image("apple")
                .navigationBarTitle("Helloworld", displayMode: .large)
                //.navigationBarTitle("Helloworld")
                //.navigationBarTitle("Helloworld", displayMode: .large)
                //.navigationBarTitle("Helloworld", displayMode: .inline)
            }
        }
    }          
    ```
            
    * display Mode : large
            
        <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect_04_03_375137.png" width = 375 height = 137>
            
    * display Mode : inline
            
        <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect_04_04_375137.png" width = 375 height = 137>
            
    * display Mode : Automatic 은 기본값  
            
 
**2) 네비게이션 바 아이템**
    
``` swift
struct Home: View {
    var body: some View {
        let leadingItem = Button(action: {}) {
            Image(systemName: "bell").imageScale(.large)
        }
        let trailingItem = Button(action: {}) {
            Image(systemName: "gear").imageScale(.large)
        }

        return NavigationView {
            Image("avocado")
                .navigationBarItems(leading: leadingItem, trailing: trailingItem)
                .navigationBarTitle("Hello World", displayMode: .automatic)
        }
    }
}
```

``` swift
struct Home: View {
    var body: some View {
        let leadingItem = HStack {
            Button(action: {}) {
                Image(systemName: "bell").imageScale(.large)
                Image(systemName: "gear").imageScale(.large)
            }
        }
        let trailingItem = Button(action: {}) {
            Image(systemName: "gear").imageScale(.large)
        }

        return NavigationView {
            Image("avocado")
                .navigationBarItems(leading: leadingItem, trailing: trailingItem)
                .navigationBarTitle("Hello World", displayMode: .automatic)
        }
    }
}
```
* 주의할점 : 13.4 이전은 백버튼이 리딩에 추가한 아이템을 덮어버린다. 이 경우에 제스쳐를 통한 뒤로가기가 먹히질 않는다. 아래는 13.4 버전
        
    <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect_04_05_39258.png" width = 392 height = 58>
        
        
**3) 네비게이션 링크**
    
* 지정한 목적지로 이동 할 수 있는 네비게이션 버튼
        
* 버튼과 동일하게 이미지 렌더링 모드가 template 에 맞춰져 있다.
        
    <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect_04_06_410204.png" width = 410 height = 204>
            
    ``` swift
    struct Home: View {
        var body: some View {
            NavigationView {
                NavigationLink(destination: Text("Destination View")) {
                    Image("apple")
                }
                .navigationBarTitle("Hello World")
            }
        }
    }
    ```

* 렌더링모드를 오리지널 혹은 버튼스타일을 PlainButton Style로 바꾸자.
        
    <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect_04_07_410204.png" width = 410 height = 204>
        
    ``` swift
    struct Home: View {
        var body: some View {
            NavigationView {
                NavigationLink(destination: Text("Destination View")) {
                    Image("apple").renderingMode(.original)
                }
                //.buttonSytle(PlainButtonStyle())
                .navigationBarTitle("Hello World")
            }
        }
    }
    ```
            
**4) 히든 수식어**

``` swift
//네비게이션 타이틀 숨
struct Home: View {
    var body: some View {
        NavigationView {
            NavigationLink(destination: Text("Destination View")) {
                Image("apple")
            }.buttonStyle(PlainButtonStyle())
            .navigationBarTitle("Hello World")
            .navigationBarHidden(true)
        }
    }
}
``` 
``` swift
//백버튼 숨김
struct Home: View {
    var body: some View {

        let destination = Text("Destination View").navigationBarBackButtonHidden(true)

        return NavigationView {
            NavigationLink(destination: destination) {
                Image("lemon").renderingMode(.original)
            }
            .navigationBarTitle("Hello World")
        }
    }
}
```

**5) 네비게이션 스타일**
    
* DefaultNavigationViewStyle : 뷰가 사용된 환경에 따라 적용
        
* StackNavigationViewStyle : 네비게이션 계층 구조를 하나의 Top뷰 만으로 탐색 하는 스타일

* DoubleColumnNavigationViewStyle : Master, Detail로 구분되는 2개의 뷰를 이용해 콘텐츠를 표현하는 스타일(내부적으로 SplitViewController 이용, 이 스타일이 사용되지 않는 기기라면 자동으로 StackNavigationStyle)
        
* 스타일에 따라 네비게이션 뷰를 생성할때 인식되는 뷰의 개수에도 차이가 발생한다.

    ``` swift
    struct Home: View {
        var body: some View {
            NavigationView {
                Text("Master”)    //이 뷰만 인식
                Text("View1")
                Text("View2")
                Text("Detail")
            }.navigationViewStyle(StackNavigationViewStyle())
        }
    }     
    ```

    ``` swift
    struct Home: View {
        var body: some View {
            NavigationView {
                Text("Master")    //인식
                Text("View1")
                Text("View2")
                Text("Detail")    //인식
            }.navigationViewStyle(DoubleColumnNavigationViewStyle())
        }
    }
    ```

**6) DoubleColumnNavigationViewStyle**
    
* UIKit 에선 마스터 디테일로 뷰를 구분해 표현 하였던 SplitViewController가 SwiftUI에선 네비게이션 뷰에 통합되어 제공된다.
        
* 단 사이즈 클래스의 너비가 Regular 인 기기 경우에만 가능하다.
        
    <img src = "https://jcsoohwancho.github.io/img/SizeClass.png">
        
    <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect_04_08_872397.png" width = 872 height = 397>
            
    ``` swift
    struct Home: View {
        var body: some View {
            NavigationView {
                VStack(spacing: 20) {
                    NavigationLink(destination: Text("디테일 뷰 영역")) {
                        Text("마스터 뷰").font(.title)
                    }
                    NavigationLink(destination: Text("디테일 뷰 영역")) {
                        Text("마스터 뷰2").font(.title)
                    }
                }
            }.navigationViewStyle(DoubleColumnNavigationViewStyle())
        }
    }       
    ```

* * *
## 3. 리스트

> **하나의 열에 여러개의 행으로 표현되는 UI를 하여 다중 데이터를 쉽게 나열 할 수 있도록 구성된 뷰**

``` swift
struct Home: View {
    var body: some View {
        List {
            Text("1")
            Text("2")
            Text("3")
        }
    }
}  
// 10개가 넘어가면 오류발생 Extra argument in call, 추후 다룸
```

1. 동적 컨텐츠
        
    1) Range<Int> : Range<Int> 타입을 넘겨주는 방식
    
        1-1) 이떄 범위는 Half-Open Range Operator 방식만 가능 A..<B
        
        > Closed Range Operator A..B
        
        > One-side Range Operator A...,...B
    
    ``` swift
    struct Home: View {
        var body: some View {
            List(0..<100) {_ in
                Text("1")
            }
        }
    }   
    ```

2. Random Access Collection : Random Access Collection 프로토콜을 준수하는 하는 데이터를 제공하는 방식, 각 요소들은 구분이 가능하고 식별이 가능 해야한다. 어느 위치에 있던, 임의 인덱스 접근의 시간 복잡도는 O(1)

    1) id 식별자 지정 방식 : id로 사용 할 값을 직접 인수로 제공, id 매개변수에는 Hashablee 프로토콜을 준수하는 프로퍼티를 지정 할 수 있다. 타입자체가 Hasable 하다면 self도 무방
        
    ``` swift
    struct Home: View {
        var body: some View {
            List(["A","B"], id: \.hashValue) {
                Text($0)
            }
        }
    }  
    ```

    ``` swift
    struct Home: View {
        var body: some View {
            List(["A","B"], id: \.self) {
                Text($0)
            }
        }
    }  
    ```

    2) identifitable 프로토콜 준수 방식 : 테이터 타입 자체에 Swift 5.1에 추가된 identifitable 프로토콜 채택하여 타입자체에 id 프로퍼티를 만들고 이것을 식별자로 삼게 된다.
    
        ``` swift
        struct Home: View {
            var body: some View {
                List([Animal(name: "1"), Animal(name: "2")]) {
                    Text($0.name)
                }
            }
        }  

        struct Animal: Identifiable {
            let id = UUID()
            var name: String
        }
        ```

3. 정적컨텐츠와 동적 컨텐츠의 조합

    1) For Each : id로 식별할 수 있는 데이터를 받아서 동적으로 뷰를 생성

    ``` swift
    struct Home: View {
        var body: some View {
            List {
                ForEach(0..<50) {
                    Text("\($0)")
                }
            }
        }
    }  
    ```

    2) 조합하기
    
    ``` swift
    struct Home: View {
        var body: some View {
            List {
                Text("Fruits").font(.largeTitle)
                ForEach(fruits, id: \.self) {
                    Text($0)
                }
            }
        }
    }  
    ```

3. 섹션

    ``` swift
    let fruits = ["사과","배","수박"]
    let drink = ["물","커피"]

    let data = [fruits, drink]
    let titles = ["과일","음료"]

    struct Home: View {
        var body: some View {
            List {
                ForEach(data.indices) { index in
                    Section(header: Text(titles[index]), footer: HStack{ Spacer(); Text("\(data[index].count) 건") } ) {
                        ForEach(data[index], id: \.self) {
                            Text($0)
                        }
                    }
                }
            }
        }
    }  
    ```

4. 리스트 스타일

    1) GroupedListStyle
    2) InsetGrouped
    3) iOS 13 부턴 UITableView에선 grouped 와 insetGrouped 이 상황에 따라 결정이된다. compact width 는 grouped regular width는 insetGrouped
        > 애플 휴먼 인터페이스 "Regular width 환경에선 insetGrouped 스타일을 사용하기에 적합하다."

    <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect_04_009.png" width = 841 height = 451>
    
    <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect_04_010.png" width = 841 height = 451>

    ``` swift
    let fruits = ["사과","배","수박"]
    let drink = ["물","커피"]

    let data = [fruits, drink]
    let titles = ["과일","음료"]

    struct Home: View {
        var body: some View {
            List {
                ForEach(data.indices) { index in
                    Section(header: Text(titles[index]), footer: HStack{ Spacer(); Text("\(data[index].count) 건") } ) {
                        ForEach(data[index], id: \.self) {
                            Text($0)
                        }
                    }
                }
            }.listStyle(GroupedListStyle())
        }
    }  
    ```

    4) Compact width 인 디바이스를 강제로 insetGrouped를 적용하기

    ``` swift
    let fruits = ["사과","배","수박"]
    let drink = ["물","커피"]

    let data = [fruits, drink]
    let titles = ["과일","음료"]

    struct Home: View {
        var body: some View {
            List {
                ForEach(data.indices) { index in
                    Section(header: Text(titles[index]), footer: HStack{ Spacer(); Text("\(data[index].count) 건") } ) {
                        ForEach(data[index], id: \.self) {
                            Text($0)
                        }
                    }
                }
            }
            .listStyle(GroupedListStyle())
            .environment(\.horizontalSizeClass, .regular)
        }
    }  
    ```
    
* * *
## 4. 지오메트리 리더

>**자식 뷰에 부모뷰와 기기에 대한 크기 및 좌표계(Geometry Proxy)를 전달하는 기능을 수행 하는 컨테이너 뷰**

```
@inlinable public init(@ViewBuilder content: @escaping (GeometryProxy) -> Content)
```
* init 함수를 보면 content 매개 변수 하나 있다. 보통 컨테이너 타입의 뷰들은 클로져로 받는 매개변수는 없지만 지오메트리 리더는 매개변수를 받는다. 이 GeomtryProxy를 통해 부모뷰와 기기에 대한 정보를 전달한다.

    ``` swift
    //List 의 content
    public init(selection: Binding<Set<SelectionValue>>?, @ViewBuilder content: () -> Content)
    ```
    
1. 특성

* 뷰 빌더 속성이 선언 되어 있는 뷰를 나열 하는 것만으로 사용 가능
* ZStack 처럼 겹겹이 쌓이는 구조
* 자식 뷰가 하나만 있을때는 중앙에 정렬 두개 이상 되면 좌상단을 기준으로 배치

* <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect_04_04_01_375_670.png" width = 375 height = 670>

    ``` swift
    struct Home: View {
        var body: some View {
            GeometryReader { _ in
                Circle().fill(Color.purple)
                    .frame(width: 200, height: 200)
                    .overlay(Text("center"))
            }.background(Color.gray)
        }
    }  
    ```

* <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect_04_04_02_375_670.png" width = 375 height = 670>

    ``` swift
    struct Home: View {
        var body: some View {
            GeometryReader { _ in
                Circle().fill(Color.purple)
                    .frame(width: 200, height: 200)
                    .overlay(Text("center"))
                Circle().fill(Color.orange)
                    .frame(width: 150, height: 150)
                    .overlay(Text("center"))
                Circle().fill(Color.red)
                    .frame(width: 100, height: 100)
                    .overlay(Text("center"))
            }.background(Color.gray)
        }
    }   
    ```

2. Geometry Proxy
        
* 두개의 프로퍼티와 하나의 메소드와 하나의 첨자를 제공하여 Geometry 레이아웃의 정보를 자식 뷰에게 전달 한다.

``` swift
public struct GeometryProxy {

    /// The size of the container view.
    public var size: CGSize { get }

    /// Resolves the value of `anchor` to the container view.
    public subscript<T>(anchor: Anchor<T>) -> T { get }

    /// The safe area inset of the container view.
    public var safeAreaInsets: EdgeInsets { get }

    /// The container view's bounds rectangle converted to a defined
    /// coordinate space.
    public func frame(in coordinateSpace: CoordinateSpace) -> CGRect
}
```

* Size, SafeInsects
> 아래는 SE 기준이다.

<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect_04_04_03_375_670.png" width = 375 height = 670>

``` swift
struct Home: View {
    var body: some View {
        GeometryReader { gemotry in
            
            Text("Geometry Reader").font(.largeTitle).bold()
                .position(x: gemotry.size.width / 2, y: gemotry.safeAreaInsets.top)
            
            VStack {
                Text("size")
                Text("width : \(gemotry.size.width)")
                Text("height : \(gemotry.size.height)")
            }.position(x: gemotry.size.width / 2, y: gemotry.size.height / 2.5)
            
            VStack {
                Text("safeAreaInsets")
                Text("top : \(gemotry.safeAreaInsets.top)")
                Text("bottom : \(gemotry.safeAreaInsets.bottom)")
            }.position(x: gemotry.size.width / 2, y: gemotry.size.height / 1.4)
            
        }
        .font(.title)
        //.frame(height: 500)	//이부분을 풀면 pro 11 에서 잡히던 안전구역 탑 바텀 높이가 0으로 바뀐다.
        .border(Color.green)
    }
}  
```
        
* Frame
> Geometry Proxy는 프레임에 대한 정보도 Coordinate 구조체로 제공한다. 
```
Local  : 현재 지오메트리 기준으로 한 좌표 정보
Global : 현재 윈도우(기기) 기준으로 한 좌표 정보
Named  : 명시적으로 이름을 할당한 공간을 기준으로 한 좌표 정보 
```

<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect_04_04_04_375_670.png" width = 375 height = 670>

``` swift
struct Home: View {
    var body: some View {
        HStack {
            Rectangle().fill(Color.yellow).frame(width: 30)
            
            VStack {
                Rectangle().fill(Color.blue).frame(width: 200)
                
                GeometryReader {
                    self.contents(geomtry: $0)
                }
                .background(Color.green)
                .border(Color.red)
            }
            .coordinateSpace(name: "VStack")
        }
        .coordinateSpace(name: "HStack")
    }
    
    func contents(geomtry g: GeometryProxy) -> some View {
        VStack {
            Text("local")
            Text(stiringFormat(for: g.frame(in: .local).origin)).padding(.bottom)               //현재 Geometry가 로컬(현재 Geometry) 기준으로 기준점이 어떻게 되는가?
            
            Text("global")
            Text(stiringFormat(for: g.frame(in: .global).origin)).padding(.bottom)              //현재 Geometry가 글로벌(기기) 기준으로 기준점이 어떻게 되는가?
            
            Text("VStack")
            Text(stiringFormat(for: g.frame(in: .named("VStack")).origin)).padding(.bottom)     //현재 Geometry가 VStack 기준으로 기준점이 어떻게 되는가?
            
            Text("HStack")
            Text(stiringFormat(for: g.frame(in: .named("HStack")).origin)).padding(.bottom)     //현재 Geometry가 HStack 기준으로 기준점이 어떻게 되는가?
        }
    }
    func stiringFormat(for point: CGPoint) -> String {
        String(format: "x: %.f, y: %.f", arguments: [point.x, point.y])
    }
}  
```
* * *
## 5. 프레임

>**Swift UI에서의 Frame 수식어는 UIKit과는 조금 다르다. UIkit은 크기 및 위치에 대한 제약을 걸지만 SwitUI는 컨텐츠를 담는 액자를 만들어 새로운 뷰를 반환한다.**

```
Text("frame")                   //Text 타입
Text("frame").frame(width: 200) //ModifiedContent<Text, FrameLayout> 타입
```

**1) 프레임의 역할**

* 자식 뷰가 사용 가능한 크기를 제안, 실제로 제안된 내에서 자식 뷰가 어떻게 보일지는 자식뷰가 직접 결정한다.
* 아래 두 뷰의 크기는 같지만, Text 경우 그 안에서 문자열이 표현하는 크기만 차지 한다.**
    
    <table><tr><td>
    <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_04_05_01.png" width = 187 height = 335>     
    
    ``` swift
    struct Home: View {
        var body: some View {
            Text("frame").background(Color.yellow).frame(width: 200, height: 100)
        }
    }  
    ```
    </table></tr></td>

    <table><tr><td>
    <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_04_05_02.png" width = 187 height = 335>


    ``` swift
    struct Home: View {
        var body: some View {
            Rectangle().fill(Color.yellow).frame(width: 200, height: 100)
        }
    }  
    ```
    </table></tr></td>

* 뷰 레이아웃 과정
    1. 부모 뷰가 크기를 제안
    2. 자식 뷰는 자신의 크기를 결정
    3. 부모 뷰는 자신의 좌표공간에서 자식 뷰를 적절하게 배치
    
**2) 고정 크기 VS 크기 제약 조건**

* 고정 크기 : 프레임의 크기와 관련된 매개변수 값을 다 넣어준다.
    
    ``` swift
    @inlinable public func frame(width: CGFloat? = nil, height: CGFloat? = nil, alignment: Alignment = .center) -> some View
    ```
    
* 크기 제약 조건 : 최소, 최대, 이상적인 값에 대해 제약조건을 입력 한다. 당연한 말이지만 크기는 최소 <= 이상 <= 최대

    ``` swift
    @inlinable public func frame(minWidth: CGFloat? = nil, idealWidth: CGFloat? = nil, maxWidth: CGFloat? = nil, minHeight: CGFloat? = nil, idealHeight: CGFloat? = nil, maxHeight: CGFloat? = nil, alignment: Alignment = .center) -> some View
    ```
    <table><tr><td>
    <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_04_05_03.png" width = 187 height = 335>
    
    ``` swift
    struct Home: View {
        var body: some View {
            HStack {
                Rectangle().fill(Color.red).frame(width: 100)
                Rectangle().fill(Color.orange).frame(maxWidth: 15)  //1. minWidth, minHeight : 최소 너비, 높이 결정
                Rectangle().fill(Color.yellow).frame(height: 150)   //2. height : 고정된 값을 가진다.
                Rectangle().fill(Color.green).frame(maxHeight: 50)
                Rectangle().fill(Color.blue).frame(maxWidth: .infinity, maxHeight: .infinity)   //3. maxHeight 경우 .infinity로 설정하면 항상 가능한 최대 높이로 설정된다.
                Rectangle().fill(Color.purple) //4. Rectangle, Color 와 같이 프레임을 따로 지정 해주지 않아도 내부적으로 maxWidth, maxHeight가 .infinity 로 설정 된다.
            }
            .frame(width: 300, height: 150)
            .background(Color.black)
        }
    }
    ```
    </table></tr></td>
    
**3) ideal 사이즈와 fixedSize()**

> UIKit 에서 intrinsicContentSize(본질적인 크기) 와 idealSize 와 개념이 비슷하다.

    ``` swift
    @inlinable public func frame(minWidth: CGFloat? = nil, idealWidth: CGFloat? = nil, maxWidth: CGFloat? = nil, minHeight: CGFloat? = nil, idealHeight: CGFloat? = nil, maxHeight: CGFloat? = nil, alignment: Alignment = .center) -> some View
    ```

* idealSize는 부모 뷰의 공간과 관계없이 자신에게 가장 이상적인 idealSize를 가지고 있다. 그리고 fixedSize()를 이용하면 이 크기 정보를 활용 할 수 있다.

    <table><tr><td>
    <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_04_05_04.png" width = 187 height = 335>

    ``` swift
    struct Home: View {
        var body: some View {
            VStack {
                Text("frame modifier").frame(width: 80, height: 30)

                Rectangle()

                Color.red

                Image("box").resizable()
            }
        }
    }  
    ```
    </table></tr></td>

* fixedSize() : 원래 뷰가 가지고자 하는 크기로 설정
    
    <table><tr><td>
    <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_04_05_05.png" width = 187 height = 335>

    ``` swift
    struct Home: View {
        var body: some View {
            VStack {
                Text("frame modifier").fixedSize().frame(width: 80, height: 30)

                Rectangle().fixedSize()

                Color.red.fixedSize()

                Image("box").resizable().fixedSize()
            }
        }
    }
    ```
    </table></tr></td>

* fixed 수식어를 사용하기 이전에 idealWidth, idealHeight를 지정해 주면 원하는 크기로 설정 가능

    <table><tr><td>
    <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_04_05_06.png" width = 187 height = 335>

    ``` swift
    struct Home: View {
        var body: some View {
            VStack {
                Text("frame modifier").frame(idealWidth: 10).fixedSize()

                Rectangle().frame(idealHeight: 100).fixedSize()

                Color.red.fixedSize()

                Image("box").resizable().fixedSize()
            }
        }
    }
    ```
    </table></tr></td>
    
* horizontal, vertical : fixedSize()를 상하, 좌우 로 적용
    
    <table><tr><td>
    <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_04_05_07.png" width = 187 height = 335>
    
    ``` swift
    struct Home: View {
        var body: some View {
            VStack(spacing: 100) {
                Group {
                    Text("fixedsize를 적용하면 글자가 생략 되지 않습니다.")

                    Text("fixedsize를 적용하면 글자가 생략 되지 않습니다.").fixedSize(horizontal: true, vertical: false)

                    Text("fixedsize를 적용하면 글자가 생략 되지 않습니다.").fixedSize(horizontal: false, vertical: true)

                }
                .font(.title)
                .frame(width: 150, height: 40)
            }
        }
    }
    ```
    </table></tr></td>

**4) Layout Priority**

* 레이아웃 우선순위가 높은 경우 부모 레이아웃은 그 자식 뷰에 공간 할당에 우선권을 준다. 공간이 늘어날때 먼저 늘어나고 줄어들땐 늦게 줄어 든다.
    
    <table><tr><td>
    <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_04_05_08.png" width = 187 height = 335>

    ``` swift
    struct Home: View {
        var body: some View {
            VStack(spacing: 20) {
                HStack {
                    Color.red
                    Color.green
                    Color.blue
                }
                .frame(height: 40)
            }
        }
    }
    ```
    </table></tr></td>
    
    <table><tr><td>
    <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_04_05_09.png" width = 187 height = 335>

    ``` swift
    struct Home: View {
        var body: some View {
            VStack(spacing: 20) {
                HStack {
                    Color.red.layoutPriority(1)
                    Color.green
                    Color.blue.layoutPriority(1)
                }
                .frame(height: 40)
            }
        }
    }  
    ```    
    </table></tr></td>
    
    <table><tr><td>
    <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_04_05_10.png" width = 187 height = 335>
    
    ``` swift
    struct Home: View {
        var body: some View {
            VStack(spacing: 20) {
                HStack {
                    Color.red.layoutPriority(1)
                    Color.green.frame(minWidth: 30)
                    Color.blue.frame(maxWidth: 50).layoutPriority(1)
                }
                .frame(height: 40)
            }
        }
    }  
    ```    
    </table></tr></td>
    
    <table><tr><td>
    <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_04_05_11.png" width = 187 height = 335>
    
    ``` swift
    struct Home: View {
        var body: some View {
            VStack(spacing: 20) {
                HStack {
                    Color.red.layoutPriority(1)
                    Color.green.frame(width: 30)    //고정크기를 설정하면 우선순위와 상관 없이 크기를 가지게 된다.
                    Color.blue.frame(maxWidth: 50).layoutPriority(1)
                }
                .frame(height: 40)
            }
        }
    }
    ```    
    </table></tr></td>

## 4-2. 실전 앱 구현 하기

## 1. 리스트를 이용한 상품 목록 표시하기

> [이전 강의에서 연장](https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/SwiftUI_03.md#3-product-%EB%AA%A8%EB%8D%B8-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0)

**1) 데이터 변환하기**

* json 읽기

``` swift
extension Bundle {
    func decode<T: Decodable> (fileName: String, as type: T.Type) -> T {
        guard let url = self.url(forResource: fileName, withExtension: nil) else {
            fatalError("번들에 파일이 없습니다.")
        }
        guard let data = try? Data(contentsOf: url) else {
            fatalError("url 로 부터 파일을 읽을 수 없습니다.")
        }
        guard let decodedData = try? JSONDecoder().decode(T.self, from: data) else {
            fatalError("데이터 복호화 실패")
        }
        return decodedData
    }
}
```
* 프로덕트 구조체 Decodable 따를 수 있도록 수정
``` swift
struct Product: Decodable {
  let name: String
  let imageName: String
  let price: Int
  let description: String
  var isFavorite: Bool = false
}
```

**2) Store 모델**

* Store Class 파일 생성
``` swift
final class Store {
    var products: [Product]
    
    init(fileName: String = "ProductData.json") {
        self.products = Bundle.main.decode(fileName: fileName, as: [Product].self)
    }
}
```

* 리스트 활용하여 표기

``` swift
struct Home: View {
    let store: Store    
    var body: some View {
        List(store.products, id: \.name) { product in
            ProductRow(product: product)
        }
    }
}
```

* Store 초기 코드 추가

``` swift
struct Home_Previews: PreviewProvider {
  static var previews: some View {
    Home(store: Store())	
  }
}
```

* List는 identifiable 프로토콜을 따르기 때문에 그것에 대해 대체할 ID 값을 지정 해줘야 한다.

``` swift
struct Product: Decodable, Identifiable {
    var id:UUID = UUID()	//UUID 가 아닌 다른 값도 무방
    let name: String
    let imageName: String
    let price: Int
    let description: String
    var isFavorite: Bool = false
}
```

## 2. 네비게이션 링크를 통한 화면 전환

``` swift
struct Home: View {
    let store: Store
 
    var body: some View {
        NavigationView {
            List(store.products, id: \.name) { product in
                NavigationLink(destination: Text("상세정보")) {
                    ProductRow(product: product)
                }
            }
            .navigationBarTitle("과일마트")
        }
    }
}
```

## 3. 상품상세 구현하기

<table><tr><td>
    <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_04_05_12.png" width = 187 height = 335>
    <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_04_05_13.png" width = 187 height = 335>
</table></tr></td>    
 
1) edgesIgnoringSafeArea : 지정한 방향의 안전 영역을 무시

**2) GeomtryReader를 벗겨 내면 이미지뷰와 주문뷰가 상품이미지 크기가 제각각이기 때문에 1:1로 균등하게 나오질 않는다. GeometryReader로 감싸면 productImage와 orderView 최상위 뷰가 모두 지오메트리 리더가 되고 이 둘은 높이를 별도로 지정 해주지 않아도 1:1 비율로 할당 받는다.**

``` swift
struct ProductDetailView: View {
  let product: Product
  
  // MARK: Body
  
  var body: some View {
    VStack(spacing: 0) {
      productImage
      orderView
    }
    .edgesIgnoringSafeArea(.top)    //1)
  }
}


private extension ProductDetailView {
  // MARK: View
  
  var productImage: some View {
    GeometryReader { _ in           //2)
      Image(self.product.imageName)
        .resizable()
        .scaledToFill()
    }
  }
  
  var orderView: some View {
    GeometryReader {
      VStack(alignment: .leading) {
        self.productDescription
        Spacer()
        self.priceInfo
        self.placeOrderButton
      }
      .frame(height: $0.size.height + 10)
      .padding(32)
      .background(Color.white)
      .cornerRadius(16)
      .shadow(color: Color.black.opacity(0.2), radius: 10, x: 0, y: -5)
    }
  }

  var productDescription: some View {
    VStack(alignment: .leading, spacing: 16) {
      HStack {
        Text(product.name)
          .font(.largeTitle).fontWeight(.medium)
          .foregroundColor(.black)
        
        Spacer()
        
        Image(systemName: "heart")
          .imageScale(.large)
          .foregroundColor(Color.peach)
          .frame(width: 32, height: 32)
      }
      
      Text(splitText(product.description))
        .foregroundColor(.secondaryText)
        .fixedSize()
    }
  }
  
  var priceInfo: some View {
    HStack {
      (Text("₩")
        + Text("\(product.price)").font(.title)
        ).fontWeight(.medium)
      Spacer()
    }
    .foregroundColor(.black)
  }
  
  var placeOrderButton: some View {
    Button(action: { }) {
      Capsule()
        .fill(Color.peach)
        .frame(maxWidth: .infinity, minHeight: 30, maxHeight: 55)
        .overlay(Text("주문하기")
          .font(.system(size: 20)).fontWeight(.medium)
          .foregroundColor(Color.white))
        .padding(.vertical, 8)
    }
  }
  
  
  // MARK: Computed Values
  
  func splitText(_ text: String) -> String {
    guard !text.isEmpty else { return text }
    let centerIdx = text.index(text.startIndex, offsetBy: text.count / 2)
    let centerSpaceIdx = text[..<centerIdx].lastIndex(of: " ")
      ?? text[centerIdx...].firstIndex(of: " ")
      ?? text.index(before: text.endIndex)
    let afterSpaceIdx = text.index(after: centerSpaceIdx)
    let lhsString = text[..<afterSpaceIdx].trimmingCharacters(in: .whitespaces)
    let rhsString = text[afterSpaceIdx...].trimmingCharacters(in: .whitespaces)
    return String(lhsString + "\n" + rhsString)
  }
}


// MARK: - Previews

struct ProductDetailView_Previews: PreviewProvider {
  static var previews: some View {
    let source1 = ProductDetailView(product: productSamples[0])
    let source2 = ProductDetailView(product: productSamples[1])
    return Group {
      Preview(source: source1)
      Preview(source: source2, devices: [.iPhone11Pro], displayDarkMode: false)
    }
  }
}
```

## 4. Function Builder
> Function Builder 는 Swift 내장 도메인 특화언어(DSL) 를 정의 하도록 추가 된 문법이다.
> DLS : 특정 종류의 문제를 더 쉽고 나은 방법으로 해결 할 수 있도록 해주는 특수한 형태의 코드 패턴
> 예를 들어 return 생략 같은 문법, VStack 의 @ViewBuilder

1) ViewBuilder : 함수로 정의된 매개변수에 뷰를 전달받아 하나 이상의 자식 뷰를 만들어 내는 기능 수행

```swift
@inlinable public init(alignment: HorizontalAlignment = .center, spacing: CGFloat? = nil, @ViewBuilder content: () -> Content)
```

* 커스텀 뷰 만들어 보기 : VStack을 특성을 가지며, 뷰정렬을 기본값으로 가지는 뷰를 만들기

    ```swift
    struct Home: View {

      // MARK: Body

      var body: some View {
        MyStackView {
            Text("hi")
            Text("SwiftUI")
        }
      }
    }

    struct MyStackView<Content: View>: View {
        let content: Content
        init(@ViewBuilder content: () -> Content) {
            self.content = content()
        }
        var body: some View {
            VStack (alignment: .leading, spacing: 10) {
                content
            }
        }
    }
    ```

* 뷰 최대 개수는 10개 : 뷰빌더는 buildBlock 이라는 타입 메소드에 값을 전달 하고 10개까지 담을 수 있는 튜플 타입을 반환한다. 추가 하고 싶은 경우에 VStack 을 추가 해야한다. 

    ```swift
    public static func buildBlock<C0, C1, C2, C3, C4, C5, C6, C7, C8, C9>(_ c0: C0, _ c1: C1, _ c2: C2, _ c3: C3, _ c4: C4, _ c5: C5, _ c6: C6, _ c7: C7, _ c8: C8, _ c9: C9) -> TupleView<(C0, C1, C2, C3, C4, C5, C6, C7, C8, C9)> where C0 : View, C1 : View, C2 : View, C3 : View, C4 : View, C5 : View, C6 : View, C7 : View, C8 : View, C9 : View
    ```
    
* 뷰 빌더 자동 합성 코드 : 뷰 빌더가 어떻게 동작 하는지 확인, 아래의 함수들은 Function Builder를 만들때 필요(뷰 빌더에 한정되지 않는다.)

    * buildBlock : 매개 변수를 받도록 한다.
    * buildEither : 선택적으로 사용 할 수 있도록 한다.
    * buildIf : 조건문을 통해 사용 할 수 있도록 한다.

    ```swift
    VStack {
        Text("Function Builder")
        Bool.random() ? Spacer() : Divider()
        if Bool.random() {
            Text("Optional")
        }
    }
    //스택뷰를 생성하면 하기와 같이 내부적으로 동작한다.
    VStack {
        ViewBuilder.buildBlock {
            Text("Function Builder")
        }
        Bool.random() ? ViewBuilder.buildEither(first: Spacer()) :
                        ViewBuilder.buildEither(second: Divider())
        ViewBuilder.buildIf(Bool.random() ? Text("Optional") : nil)
    }
    ```
    
2) Custom Function Builder : 연산프로퍼티, 함수, 함수의 매개변수 를 통해 만들기, 숫자를 입력하면 짝수를 반환하는 함수빌더

```swift
struct Home: View {

    @_functionBuilder
    struct EvenNumbers {
        static func buildBlock(_ numbers: [Int]) -> [Int] {
            numbers.filter{ $0.isMultiple(of: 2) }
        }
    }
}
```

* 연산프로퍼티 : 연산프로퍼티에 함수 빌더 적용

```swift
struct Home: View {

    @_functionBuilder
    struct EvenNumbers {
        static func buildBlock(_ numbers: [Int]) -> [Int] {
            numbers.filter{ $0.isMultiple(of: 2) }
        }
    }
    
    // MARK: Body
    
    var body: some View {
        @EvenNumbers var computedProperty: [Int] {
            [1,2]
        }
        print(computedProperty)     //[2]
        return Text("")
    }
}
```

* 함수 : Function Builder 를 함수 자체에 추가 해주는 방법

```swift
struct Home: View {

    @_functionBuilder
    struct EvenNumbers {
        static func buildBlock(_ numbers: [Int]) -> [Int] {
            numbers.filter{ $0.isMultiple(of: 2) }
        }
    }
    
    // MARK: Body
    
    var body: some View {

        @EvenNumbers
        func annotedFunction(_ numbers: [Int]) -> [Int] {
            numbers.filter { $0 > 2 }
        }

        print(annotedFunction([1,2,3,4]))
        return Text("")
    }
}
```

* 함수의 매개변수 : 매개 변수에 함수 빌더 추가, 함수로 전달하는 값이 buildBlock의 입력 값이 되고 buildBlock 함수가 호출 되는 타이밍은 넘겨 받은 함수가 실행 되었을 때가 된다.

```swift
@_functionBuilder
struct EvenNumbers {
    static func buildBlock(_ numbers: [Int]) -> [Int] {
        numbers.filter{ $0.isMultiple(of: 2) }
    }
    static func buildBlock(_ numbers: Int...) -> [Int] {
        numbers.filter{ $0.isMultiple(of: 2) }
    }
}

struct MyNumbers {
    let numbers: [Int]
    @inlinable init(@EvenNumbers content: () -> [Int]) {
        self.numbers = content()
    }
}

struct Home: View {

    // MARK: Body
    
    var body: some View {
        let number = MyNumbers {
            [1,2,3]
        }
        let number2 = MyNumbers {
            1
            2
            3
            4
        }
        print(number.numbers)   //[1,2,3] >> 애플 버그로 보임 찾아봐야
        print(number2.numbers)  //[2,4]
        return Text("")
    }
}
```
