# SwiftUI Rect
# 

8강 스크롤뷰, 탭뷰
===========
## 7-1. 기본기 다지기
> 스크롤뷰, 탭뷰 

## 1. ScrollView

* 생성자에 스크롤 방향과 인디케이터 여부, 뷰빌더 속성으로 선언된 content 매개변수가 있다. 여기에 컨텐츠를 정의한다.

  ```swift
  public struct ScrollView<Content> : View where Content : View {
      public var content: Content
      public var axes: Axis.Set
      public var showsIndicators: Bool
      public init(_ axes: Axis.Set = .vertical, showsIndicators: Bool = true, @ViewBuilder content: () -> Content)
      public var body: some View { get }
      public typealias Body = some View
  }
  ```

* 기본 Default는 세로 축이며, 가로축을 사용하려면 .horizontal
  ```swift
  //기본 세로축
  ScrollView {
      Text("hi")
  }

  //가로축
  ScrollView(.horizontal) {
      Text("hi")
  }
  ```

* 가로축인 경우 스크롤뷰는 부모 뷰가 제공 하는 너비의 최대한을 차지하려는 확장성을 가진다.

  <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_08_01.png" width = 310 height = 672>

* 세로축인 경우 스크롤뷰는 자식 뷰가 가진 너비만큼 차지한다.

  <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_08_02.png" width = 310 height = 672>


  ```swift
  ScrollView(.horizontal) {
      Text("hi")
          .font(.largeTitle)
          .bold()
  }
  .background(Color.green)
  ```

* 각각 다른 스크롤 방향을 가진 스크롤 뷰, 주의 점은 가로축 스크롤뷰 를 사용할 때 자식 뷰는 HStack 을 채용해야 한다. 빌드 시 엄청 느려지는데 이유는 추후 알아 보자

  ```swift
  var body: some View {
      GeometryReader { geomtry in
          VStack {
              Text("Vertical").font(.largeTitle)

              ScrollView {
                  ForEach(0..<10) { i in
                      Circle()
                          .fill(Color(white: 0.2 + (0.8 * Double(i) / 10)))
                          .frame(width: 80, height: 80)
                  }
                  .frame(width:geomtry.size.width / 2)
              }
              .frame(height: geomtry.size.height / 2)
              .padding(.bottom)

              Text("Horizontal").font(.largeTitle)

              ScrollView(.horizontal) {
                  HStack {
                      ForEach(0..<10) { i in
                          Circle()
                              .fill(Color(white: 0.2 + (0.8 * Double(i) / 10)))
                              .frame(width: 80, height: 80)
                      }
                  }
              }
              .frame(height: geomtry.size.height / 5)
              .padding(.horizontal)
          }
      }
  }
  ```

* VStack 과 세로축 스크롤뷰 비교(왼 VStack, 우 스크롤뷰)

  ```swift
  var body: some View {
      VStack {
          Color.red
          Color.green
          Color.blue
      }
  }
  ```

  ```swift
  var body: some View {
      ScrollView {
          Color.red
          Color.green
          Color.blue
      }
  }
  ```

  <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_08_04.png" width = 310 height = 672> <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_08_03.png" width = 310 height = 672>

    * 스크롤뷰 경우 화면이 꽉 차 있지 않는데 스크롤뷰의 컨텐츠는 무한대 일 수 있기 때문에 color, rectangle과 같이 크기를 지정하지 않는 뷰 일때 스크롤뷰 입장은 어디까지 표현되는지 알 수 가 없다.
    
    * 스크롤뷰는 내부적으로 스크롤방향에 따라 fixedSize 수식어를 적용하고 있다.
    
    * VStack을 스크롤 뷰 처럼 보이려면
    
      ```swift
      var body: some View {
          VStack {
              Color.red
              Color.green
              Color.blue
          }
          .fixedSize(horizontal: false, vertical: true)
          .frame(maxHeight: .infinity, alignment: .top)
      }
      ```

  * 스크롤뷰를 VStack 처럼 보이려면
  
    ```swift
    var body: some View {
        ScrollView {
            Color.red
            Color.green.frame(idealHeight: 50)
            Color.blue.frame(idealHeight: 100)
        }
    }
    ```
    
  * 위 코드 스크롤뷰에 idealHeight 를 주면 통일된다. 스크롤뷰는 방향에 따라 ideal 높이 너비를 주면 개별 크기가 아닌 (50 + 100 / 2) 이 된다. 전체 크기는 (50 + 100 / 2) + (8 * 1) //간격
  
  * 개별 크기를 주고 싶다면 height를 주면 된다.
  
    ```swift
    var body: some View {
        ScrollView {
            Color.red
            Color.green.frame(height: 100)
            Color.blue.frame(height: 200)
        }
    }
    ```
