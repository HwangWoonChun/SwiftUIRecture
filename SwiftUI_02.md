# SwiftUI Rect
AutoLayout Recture

2강 뷰 구성하기
===========
1. UIKit VS SwiftUI

* View and Controls

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

* ViewControllers

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

* * *
2. 텍스트(Text)
* UILabel과 비슷하지만, Button, Picker, Toggle 등 에서 UI 구성할때 텍스트를 사용하는 경우가 많다.

<img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect2_3.png" width = 250 height = 450> <img src = "https://github.com/HwangWoonChun/SWIFTUIRecture/blob/master/rect2_4.png" width = 350 height = 450>

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
  - fixedSize : 주어진 공간의 크기가 작아도 텍스트를 생력하지 않고 표현(오른쪽 스크린샷 참조)
  - kernering : 자간(텍스트 좌우 간격)
  - baseLineOffSet : 기준선(텍스트 상하 간격)
  
* 수식어 순서 유의사항(1) : 순서가 중요하다!  
 
