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
                    .resizable()
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

* PlainButtonStyle : IDLE(유휴 상태, 아무런 동작이 없는) 상태에서는 어떠한 시각적 요소도 적용하지 않는 버튼  



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
struct Home: View {
    var body: some View {
        NavigationView {
            NavigationLink(destination: Text("Destination View")) {
                Image("lemon")
            }.buttonStyle(PlainButtonStyle())
            .navigationBarTitle("Hello World")
            .navigationBarHidden(true)
        }
    }
}
``` 
``` swift
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
> **하나의 열에 여러개의 행으로 표현되는 UI를 굿겅하여 다중 데이터를 쉽게 나열 할 수 있도록 구성된 뷰**

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
**1) 동적 컨텐츠 **
        
1. Range<Int> : Range<Int> 타입을 넘겨주는 방식
    
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

2. Random Access Collection : Random Access Collection 프로토콜을 준수하는 하는 데이터를 제공하는 방식, 각 요소들은 구분이 가능하고 식별이 가능 해야한다.

    2-1) id 식별자 지정 방식 : id로 사용 할 값을 직접 인수로 제공, id 매개변수에는 Hashablee 프로토콜을 준수하는 프로퍼티를 지정 할 수 있다. 타입자체가 Hasable 하다면 self도 무방
        
    > Hashable : 정수 Hash 값을 제공하는 타입
    > Hash 함수 : 임의의 길이를 갖는 데이터에 대해 고정된 길이의 데이터로 매핑하는 함수
        
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

    2-2) identifitable 프로토콜 준수 방식 : 테이터 타입 자체에 Swift 5.1에 추가된 identifitable 프로토콜 채택하여 타입자체에 id 프로퍼티를 만들고 이것을 식별자로 삼게 된다.
    
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

**2) 정적컨텐츠와 동적 컨텐츠의 조합 **

1. For Each : id로 식별할 수 있는 데이터를 받아서 동적으로 뷰를 생성

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

    1-1) 조합하기
    
        ``` swift
        let fruits = ["사과","배"]

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

**3) 섹션**

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

**4) 리스트 스타일**

* GroupedListStyle
* InsetGrouped
* iOS 13 부턴 UITableView에선 grouped 와 insetGrouped 이 상황에 따라 결정이된다. compact width 는 grouped regular width는 insetGrouped
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

* Compact width 인 디바이스를 강제로 insetGrouped를 적용하기

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
