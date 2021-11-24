---
layout: post
title:  "[Swift] inout"
date:   2021-11-03 00:00:00
author: bobo
categories: Swift
tags:	Swift
cover:  "/assets/instacode.png"
---

# [Swift] inout

`inout`은 `copy-in-copy-out`의 줄임말로 안으로 복사되고, 다시 바깥으로 복사된다는 뜻이다.<br/>
무슨 뜻인지 [애플 공식 문서](https://docs.swift.org/swift-book/LanguageGuide/Functions.html)를 참고하여 알아보자.

파라미터로 넘겨받은 값을 1 증가시키는 함수를 만들어보자.

```swift
func increaseNum(_ value: Int) {
  	value += 1		// 컴파일 에러 발생
}
```

작성했더니 컴파일 에러가 발생했다. 그 이유는 무엇일까?<br/>
함수 파라미터는 기본적으로 `상수(Constant)`이다. C, C++, Java, Python 등의 언어에서 함수의 파라미터는 `변수`로 사용되지만 Swift에서는 `상수`라는 것이 차이점이다.<br/>
함수 안에서 매개변수로 넘겨받은 `값(=상수)`를 변경하려고 했기 때문에 컴파일 에러가 발생한 것이다.

함수가 매개변수 값을 수정 가능하게 하고 함수 호출이 끝난 후에도 변경된 값이 유지되게 하려면 <br/>변수의 주소값을 넘겨 직접 접근할 수 있도록 도와주는 `inout` 키워드를 사용해야 한다.<br/>
어떻게 작성해야 하는지 알아보자.

`inout`은 파라미터 타입 앞에 배치하여 작성해야 한다.

```swift
func increaseNum(_ value: inout Int) { }	
```

또한, `inout` 매개변수에 대한 인수로는 변수만 전달할 수 있다. <br/>상수와 리터럴은 수정할 수 없기 때문에 전달할 수 없다.

매개변수를 인수로 전달할 때에는 변수명 앞에 `&`를 붙여서 사용해야 한다. <br/>이 말은 곧, 함수를 호출할 때 매개변수 값으로 넘어가는 변수명 왼쪽에 `&`가 붙어있다면 함수 내부에서 수정될 수 있다는 의미이기도 하다.

완성된 코드는 다음과 같다.

```swift
var num: Int = 10
print("Before value : \(num)")	// Before value : 10

func increaseNum(_ value: Int) {
  value += 1
}

increaseNum(&num)
print("After value : \(num)")	// After value : 11
```

<br/><br/><br/>

