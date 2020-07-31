# SwiftUI Rect
# 

6강 유저 입력다루기
===========
## 4-1. 기본기 다지기

## 1. 데이터의 흐름 이해하기
> Swift UI 에서 데이터를 변경하고 전달하는 방식

**1) 프로퍼티 수정하기**

* 이 코드는 프로퍼티 framework 가 수정이 가능 한가?

> Cannot assign to property: 'self' is immutable, Body가 get 타입이라 Body 내에서 프로퍼티를 수정 할 수 없다

  ```swift
  struct Home: View {
      var frameWork: String = "UIKit"
      var body: some View {
          Button(frameWork) {
              self.frameWork = "SwfitUI"
          }
      }
  }
  ```

* 
