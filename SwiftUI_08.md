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

  <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_08_04.png" width = 310 height = 672> | <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/rect_08_03.png" width = 310 height = 672>
