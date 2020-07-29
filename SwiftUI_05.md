# SwiftUI Rect
# 

4강 프리뷰
===========
## 4-1. 기본기 다지기

## 1. 프리뷰
> 프리뷰는 쉽고 빠른 화면결과를 피드백 받을 수 있지만, 다른 기능들도 있다. 동일화면이 다양한 기기에서 어떻게 보이는지, 버튼 선택시 어떻게 되는 지 등등

**1) 프리뷰 동작과정**

* XCode에서의 동작
  1. 현재 Editor에서 PreviewProvider를 준수하는 타입이 있는지 확인
  2. PreviewProvider의 Preview 타입 프로퍼티를 생성
  3. 시뮬레이터에 연결한 기기의 형태로 브리뷰 컨테이너 렌더링(기본은 IPhone8)
  
* 왜 빠른가?
  * PreviewProvider 타입을 작업중인 파일과 더불어 렌더링 시키는데 그외의 파일들은 분리하고 작업중인 파일만 컴파일 한다.
  * DerivedData 폴더를 보면 프리뷰용이 따로 존재 한다. 소스 에디터를 분할 시키면 분할 시킨 만큼 생성된다.
  
* 최적화
  * 디버그 시 no optimization[-Onone] 으로 릴리즈 시 Optimize for Speed[-O] 로 최적화 모드가 설정되는데 
    프리뷰는 no optimization[-Onone] 이다. 이 때문에 릴리즈 하는 경우 프리뷰 와 관련된 코드가 모두 무시된다. 
    만약 디버그 모드의 최적화 레벨을 Optimize for Speed[-O] 로 바꾸면 에러가 난다.

* 자동 프리뷰 갱신이 안되는 케이스
  * 프로퍼티 삭제 추가
  * 저장 프로퍼티 값 변경
  * 뷰의 타입 혹은 이름 변경
  * 앱을 수동으로 빌드
  
* 자동 프리뷰 갱신이 되는 경우
  * 파일의 TopLevel
  * 구조체의 구현
  * 함수 범위 안에서 코드를 변경하는 경우
  * 연산프로퍼티(입력값이 없는 함수와 동일 하기 때문이다.)
  
  ``` swift
  //top Level(파일의 탑레벨)
  struct Home: View {
      //struct implementation(구조체 구현)
      var body: some View {
          //computed property(속성)
          Text("Hello, World!")
      }
      func someMethod() {
          //method
      }
      //struct implementation
  }
  //top Level(파일의 탑레벨)
  func someFunction() {
      //method
  }
  ```
* 프리뷰 수식어 보기

  * 기기 지정
  ``` swift
  struct Home_Previews: PreviewProvider {
    static var previews: some View {
        Home().previewDevice("iPhone 11 Pro")
    }
  }
  ``` 
  
  * 여러 기기에서 보기(List 뷰 이용)
  ``` swift
  struct Home_Previews: PreviewProvider {
    static var previews: some View {
        Group {
            Home().previewDevice("iPhone 11 Pro")
            Home().previewDevice("iPhone 11 Pro Max")
        }
    }
  }
  ``` 
  
  ``` swift
  struct Home_Previews: PreviewProvider {
    static var previews: some View {
        ForEach(["iPhone 11 Pro", "iPhone 11 Pro Max", "iPhone SE"], id: \.self) {
            Home().previewDevice(PreviewDevice(rawValue: $0))
        }
    }
  }
  ``` 
  
  * 이름 지정 하기
  ``` swift
  struct Home_Previews: PreviewProvider {
    static var previews: some View {
        ForEach(["iPhone 11 Pro", "iPhone 11 Pro Max", "iPhone SE"], id: \.self) {
            Home().previewDevice(PreviewDevice(rawValue: $0)).previewDisplayName("\($0) haha")
        }
    }
  }
  ``` 
  
  * 레이 아웃 변경하기 : 화면 크기 조절
    * device : 컨테이너가 기기의 본래 크기를 가진다.
    * sizethatFits : 컨테이너가 프리뷰 크기를 맞춘다.
    * fixed(width: height:) : 개발자가 지정한 크기로 컨테이너를 고정한다. 가로 모드일때 사용된다.
    
    ``` swift
    struct Home_Previews: PreviewProvider {
        static var previews: some View {
            ForEach(["iPhone 11 Pro", "iPhone 11 Pro Max", "iPhone SE"], id: \.self) {
                Home().previewDevice(PreviewDevice(rawValue: $0)).previewDisplayName("\($0) haha").previewLayout(.sizeThatFits)
            }
        }
    }
    ``` 
