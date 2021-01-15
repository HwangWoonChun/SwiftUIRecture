# SwiftUI Rect
# 

9강 애니메이션, 트렌지션
===========
## 8-1. 기본기 다지기
> 애니메이션, 트렌지션

## 1. 애니메이션

* 애니메이션의 상태는 3개로 볼 수 있다. trigger > animating > end animated

* blur, scaleEffect에 애니메이션 적용

    ```swift
    struct ContentView: View {
        var body: some View {
            AnimationView()
        }
    }

    struct AnimationView: View {
        @State private var blur: Bool = false
        @State private var reduction: Bool = false

        var body: some View {
            Image("apple")
                .blur(radius: blur ? 5 : 0)
                .scaleEffect(reduction ? 0.7 : 1)
                .animation(.default)    //애니메이션 적용
                .onTapGesture {
                    self.blur.toggle()
                    self.reduction.toggle()
                }
        }
    }
    ```

* 특정 값에만 적용

    ```swift
    .animation(.default, value: reduction)    //특정 값이 변할 떄만 애니메이션 적용
    ```

* 애니메이션 수식어 위치에 따라 애니메이션 적용 범위는 달라 진다.

    ```swift
    .blur(radius: blur ? 5 : 0)
    .animation(.default)    //blur 에만 애니메이션 적용
    .scaleEffect(reduction ? 0.7 : 1)
    ```

* withAnimation : animation 수식어는 특정 뷰에 한정되어 동작한다. withAnimation 같은 경우 여러뷰에 적용이 가능하다.

    ```swift
    struct AnimationView: View {
        @State private var blur: Bool = false
        @State private var reduction: Bool = false

        var body: some View {
            Image("apple")
                .blur(radius: blur ? 5 : 0)
                .scaleEffect(reduction ? 0.7 : 1)
                .onTapGesture {
                    withAnimation {
                        self.blur.toggle()
                        self.reduction.toggle()
                    }
                }
        }
    }
    ```

* 명시적(explicit) 암시적(implicit) 애니메이션 : animation 수식어는 수식어 이전에 적용된 애니메이션 항목만 적용(암식적), withAnimation 수식어는 애니메잏션에 동작하는데 영향을 미칠 데이터를 직접 지정(명시적)

* 속도조절 옵셕

    * linear : 

    ```swift
    .animation(.linear(duration: 1))    //애니메이션 적용
    ```
```swift

```
