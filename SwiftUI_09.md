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

* 명시적(explicit) 암시적(implicit) 애니메이션 : animation 수식어는 수식어 이전에 적용된 애니메이션 항목만 적용(암식적), withAnimation 수식어는 애니메이션에 동작하는데 영향을 미칠 데이터를 직접 지정(명시적)

* 속도조절 옵션

    * linear : 처음 부터 끝까지 일정 속도
    * easein : 처음에는 느리게 점점 빨라짐
    * easeOut : 처음에는 빠르게 점점 느려짐
    * easeInOut : 처음과 끝은 느리게 동작하고 중간 지점에서 빠르게 진행
    * timeCurve : 개발자가 직접 지정

        ```swift
        .animation(.linear(duration: 1))    //애니메이션 적용
        ```

        ```swift
        .animation(.timingCurve(0.46, 0.18, 0.15, 0.94))    //애니메이션 적용
        ```

* 스프링 애니메이션 : 위 애니메이션 들은 시작과 끝은 고정 타이밍, 스프링 애니메이션 경우 목점지점에서 진동효과를 주어 좀더 동적인 느낌을 준다.

```swift
.animation(.spring(response: 0.55, dampingFraction: 0.825, blendDuration: 0))    //애니메이션 적용
//response: 애니메이션 지속시간에 대한 근사치, dampingFraction: 진동수준 0에 가까울 수록 커진다, blendDuration: 진동보간
```
        
* 애니메이션 제어 : 

        * delay : 시간(초) 만큼 애니메이션 지연
        * speed : 애니메이션을 지정한 배율만틈 곱한 속도로 진행
        * repeatCount, repeatForever: 반복 수준

            ```swift
            struct AnimationView: View {
                @State private var blur: Bool = false
                @State private var reduction: Bool = false

                var body: some View {
                    Image("apple")
                        .blur(radius: blur ? 5 : 0)
                        //blur 2배속 5번 수행
                        .animation(Animation.default.speed(2).repeatCount(5, autoreverses: true))
                        .scaleEffect(reduction ? 0.7 : 1)
                        //1초되 scaleEffect 애니메이션 실행
                        .animation(Animation.default.delay(1))
                        .onTapGesture {
                            self.blur.toggle()
                            self.reduction.toggle()
                        }
                }
            }
            ```
