# SwiftUI Rect
# 

5강 프리뷰 활용하기
===========
## 4-1. 기본기 다지기

## 1. 프리뷰

**1) 프로퍼티 동작과정**

```swift
struct Home_Previews: PreviewProvider {
    static var previews: some View {
        Home()
    }
}
```

* 동작과정
  1. 현재 소스에디터에 PreviewProvider 프로토콜을 준수하는 타입 있는지?
  2. PreviewProvider의 필수 구현사항인 preview 타입 프로퍼티에서 뷰 생성
  3. 시뮬레이터, 연결된 디바이스 형태로 프리뷰 컨테이너 렌더링
  4. 직접 지정시엔 3번 무시하고 직접 지정된 형태로 렌더링

* 속도가 빠른이유
    1. preview 타입의 뷰와 관려된 코드만을 컴파일 한다.
    2. Dervied Data에 프리뷰만을 위한 별도의 디렉토리 생성된다.
