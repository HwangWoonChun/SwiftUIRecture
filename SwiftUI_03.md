# SwiftUI Rect
SwiftUI Recture

3강 실전 앱 구현하기
===========
## 1. 기본 뷰 구성하기

<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/03Rect_01.png" width = 268 height = 314>

1. 이미지뷰 복습
*. clipped() : 뷰를 벗어나는 이미지 잘라주기 
``` swift
struct Home: View {
    var body: some View {
        HStack {
            Image("apple")
                .resizable()
                .scaledToFill()
                .frame(width: 140)
                .clipped()
        }.frame(height: 150)
    }
}
```

2. 셀 구조 잡기 : HStack 안에 여러 스택 중첩

*. EmptyView() : Stack 뷰 안에 하나 이상의 뷰를 넣어야하기 때문에 추가할 기능을 구현하기전에 미리 아무기능도 아무 공간도 차지하지 않는 뷰를 집어 넣는 것
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


<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/03Rect_02.png" width = 776 height = 358>

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
