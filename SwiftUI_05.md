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
    
* Swift 최적화 레벨
    * 기존네는 #if DEBUG 구문을 통해 지정
    * Swfit UI의 프리뷰 에선 No Optimization [-Onone] 일때만 빌드 되고 릴리즈모드 는 Optimize for Speed[-O] 을 채택한다. (반대로 하면 에러 난다.)
    
* 자동 프리뷰 갱신이 안되는 경우
    * 퍼알의 탑레벨
    * 구조체, 클래스의 구현
    * 키워드, 속성, 프리프로세서 구문 수정
    
* 자동프리뷰 갱신이 되는 경우
    * 함수 범위 안에서 변경(=연산프로퍼티)
    * 
