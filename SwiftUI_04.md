# SwiftUI Rect
# 

4강 네비게이션 뷰와 리스트
===========
## 4-1. 기본기 다지기

1. 버튼

    1) 버튼 표현하기

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

    2) 버튼 스타일(IOS 만 보자)

        <img src = "https://d33wubrfki0l68.cloudfront.net/1a47b639da7c62e0b83ddc93ba2291fcde828281/a449a/images/buttonstyle-ios.png">

        <img src = "https://d33wubrfki0l68.cloudfront.net/973c73566a73626e361f0c816017d82a7e84147a/1682d/images/buttonstyle-mac.png">

        * DefaultButtonStyle : 모든 OS 기본 스타일

        * BorderlessButtonStyle : 테두리가 없는 버튼

        * PlainButtonStyle : IDLE(유휴 상태, 아무런 동작이 없는) 상태에서는 어떠한 시각적 요소도 적용하지 않는 버튼


    3) 버튼이 아닌 onTapGesture를 이용하여 동작 구현, 단 기본적인 애니메이션은 사용 할 수 없다.
    
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
2. 네비게이션 뷰 : 네비게이션 스택을 이용해 컨텐츠 뷰들을 관리 하는 컨테이너 뷰, UINavigationController, UISplitViewController 역할 수행

    1) 네비게이션 바 타이틀
        
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

* * *
3. 리스트
* * *
