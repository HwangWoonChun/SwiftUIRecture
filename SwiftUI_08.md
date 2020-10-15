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
    
  * 위 코드 스크롤뷰에 idealHeight 를 주면 통일된다. 스크롤뷰는 방향에 따라 ideal 높이 너비를 주면 개별 크기가 아닌 (50 + 100 / 2) 이 된다. 전체 크기는 (50 + 100 / 2) + (8 * 2) //간격
  
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
    
* ContentOffset

  * SwiftUI는 직접적으로 제공하진 않고, 부모뷰에 데이터를 전달 할 수 있는 preferenceKey 를 사용하거나, 지오메트리 리더의 글로벌 좌표를 이용한다.
  
  * Geometry Leader 를 이용하여 스크롤 할때 마다 색상 변경
  
  * .global : 지오메트리 프록시에서 글로벌 기준으로 프레임을 얻으면 윈도우의 원점을 기준으로 한 상대 좌표를 반환 한다.
  
    ```swift
    struct Home: View {
        var body: some View {
            ScrollView {
                ForEach(0..<10) { _ in
                    GeometryReader {
                        Rectangle().fill(self.color(from: $0))
                    } .frame(width: 150, height: 150)
                }
            }
        }
        func color(from proxy: GeometryProxy) -> Color {
            let yOffset = proxy.frame(in: .global).minY - 44  
            //safeAreaInset.top 만큼의 크기
            //높이 150 간격 8
            //첫번째 셀의 Y는 44, 두번째는 202
            let color = min(1,0.2 + Double(yOffset / 1000))
            return Color(hue: color, saturation: color, brightness: color)
        }
    }
    ```
* 페이징 처리

  * SwiftUI 는 이 기능을 직접 제공하지 않는다.
  
  * 외형 프록시를 이용하여 UIScrollView의 기본 설정을 변경하는 방식으로 구현 할 수 있다.

    ```swift
        var body: some View {
            ScrollView {
                ForEach(0..<10) { _ in
                    GeometryReader {
                        Rectangle().fill(self.color(from: $0))
                    } .frame(width: UIScreen.main.bounds.size.width, height: UIScreen.main.bounds.size.height)
                }
            }
            .onAppear { UIScrollView.appearance().isPagingEnabled = true }
        }
    ```
    
## 2. 탭뷰

* UITabBarController 와 컨샙, 기본구조, 개념은 같다. 하지만 사용방법이 훨씬 간단해졌다.

* 제약 사항
    * 탭 아이템은 이미지 + 텍스트만 가능하다.
    * 텍스트만 넣든 이미지만 넣든 영역은 고정이다.
    * 두개이상 넣을때 하나만(첫번째) 채택된다.
    * 아이템을 하나도 넣지 않아도 자리는 차지 한다.

* 간단 예제
    <tr>
      <td> <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/tab_01.png" width = 310 height = 870> </td>
      <td> <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/tab_02.png" width = 310 height = 870> </td>
      <td> <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/image/tab_03.png" width = 310 height = 870> </td>
    </tr>
    
    ```swift
    struct Home: View {
        var body: some View {
            TabView {
                VStack {
                    Text("1 탭").font(.largeTitle)
                    Image("SwiftUI")
                }.tabItem {
                    Image(systemName: "house")
                    Text("아이템1")
                }
                Text("2 탭").font(.title)
                    .padding()
                    .background(Color.yellow)
                    .tabItem {
                        Image(systemName: "cube")
                        Text("아이템2")
                    }
                SomeView().tabItem {
                    Image(systemName: "person")
                    Text("아이템3")
                }
            }
        }

        struct SomeView: View{
            var body: some View {
                VStack {
                    Text("실제로 이렇게 개발")
                    Text("별도 뷰를 빼서")
                }.font(.title)
            }
        }
    }
    ```
