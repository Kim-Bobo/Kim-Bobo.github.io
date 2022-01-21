---
layout: post
title:  "[Swift] Closures"
date:   2022-01-13 00:00:00
author: bobo
categories: Swift
tags:   Swift
cover:  "/assets/instacode.png"
---

# [Swift] Closures



## 1. Closures?

`closure`는 어떤 기능을 하는 코드를 하나의 블럭으로 모아놓은 것을 말한다. 클로저는 코드를 간결하고, 직관적으로 작성하는 데 많은 도움을 준다. 

클로저는 다음과 같은 특징이 있다.

- 1급 객체 함수이다.
- 다른 함수의 인자로 넘길 수 있다.
- 함수를 상수나 변수에 할당할 수 있다.

클로저는 다음 세가지 형태 중 하나를 갖는다.

- 전역 함수: 이름이 있고 어떤 값도 캡쳐하지 않는 클로저
- 중첩 함수: 이름이 있고 관련한 함수로부터 값을 캡쳐할 수 있는 클로저
- 클로저 표현식: 주변 맥락(context)으로부터 값을 캡쳐할 수 있고 간단한 구문으로 작성된 이름이 없는 클로저

<br/>

## 2. 클로저 표현식(Closure Expressions)

[Swift 공식 가이드 문서](https://docs.swift.org/swift-book/LanguageGuide/Closures.html)에서는 `sorted(by:)`로 클로저의 설명을 돕고 있다. `by`에 어떤 방법으로 정렬을 수행할 것인지에 대해 기술한 클로저를 넣으면 그 방법대로 정렬된 배열을 얻을 수 있다.

`sorted(by:)`는 배열의 요소와 동일한 유형의 두 인자를 갖는 클로저를 사용해야 한다. 첫 번째 값이 두 번째 값보다 먼저 나타나야 하는 경우에는 `true`를 리턴해야 하고 그 반대의 경우에는 `false`를 리턴해야 한다.

예제와 함께 살펴보자.

```swift
let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
```

`names`는 String 배열이기 때문에 (String, String) -> Bool 타입의 클로저를 사용해야 한다.

```swift
func backward(_ s1: String, _ s2: String) -> Bool {
	return s1 > s2
}
var reversedNames = names.sorted(by: backward)
print(reversedNames)	// Prints ["Ewa", "Daniella", "Chris", "Barry", "Alex"]
```

`backward` 함수를 만들고 그것을 `sorted(by:)`에 인자로 전달하는 방법은 일반적이지만 다소 긴 방법이다. 이럴 때는 클로저 표현식 구문을 사용하여 작성하는 것이 좋다. 이제 그 사용 방법에 대해 알아보자.

<br/>



- ### 클로저 표현식 구문(Closure Expressions Syntax)

클로저의 기본 형태는 일반적으로 아래와 같다.

```swift
{ (parameters) -> return type in
 	statements
}
```

위 함수를 클로저 표현식 버전으로 변경한 코드는 다음과 같다.

```swift
reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in
  return s1 > s2
})
```

이렇게 함수로 따로 정의된 형태가 아닌 인자로 들어가 있는 형태의 클로저를 `인라인 클로저`라고부른다. `in` 키워드는 인자와 리턴 타입이 정의가 완료되었으며 키워드 이후에 클로저의 `body`가 시작됨을 나타낸다.

<br/>

- ### 타입 추론

어떤 타입의 인자가 들어와야 하는지 안다면 클로저에서는 이 타입들을 생략할 수 있다.

```swift
reversedNames = names.sorted(by: { s1, s2 in return s1 > s2 })
```

<br/>

- ### 리턴 키워드 생략

단일 표현 클로저에서는 리턴 키워드를 생략할 수 있다.

```swift
reversedNames = names.sorted(by: { s1, s2 in s1 > s2 })
```

<br/>

- ### 인자 이름 축약

Swift는 `인라인 클로저`에 자동으로 축약 인자 이름을 제공한다. 인자 값을 순서대로 `$0, $1, $2` 등으로 사용할 수 있다.

```swift
reversedNames = names.sorted(by: { $0 > $1 })
```

<br/>

- ### 연산자 메소드

Swift의 String 타입은 `>` 연산자로 String끼리 비교할 수 있다. 

```swift
reversedNames = names.sorted(by: >)
```

<br/>

## 3. 후행 클로저

클로저가 함수의 마지막 인자로 전달되고 클로저가 길어서 한 줄에 인라인으로 작성할 수 없을 때 가장 유용하다.

```swift
reversedNames = names.sorted() { $0 > $1 }
```

소괄호를 생략하고 클로저의 형태로도 작성이 가능하다.

```swift
reversedNames = names.sorted { $0 > $1 }
```

<br/>

## 4. 값 캡쳐

클로저는 특정 문맥의 상수나 변수의 값을 `캡쳐`할 수 있다. 상수와 변수를 정의한 원래 범위가 더 이상 존재하지 않는 경우에도 클로저의 `body` 안에서 해당 상수 및 변수의 값을 참조하고 수정할 수 있다는 얘기이다. Swift에서 값을 캡쳐할 수 있는 가장 간단한 형태는 다른 함수의 `body` 안에서 다른 함수를 다시 호출하는 형태인 `중첩 함수`이다.

```swift
func makeIncrementer(forIncrement amount: Int) -> (() -> Int) {
  var runningTotal = 0
  func incrementer() -> Int {
    runningTotal += amount
    return runningTotal
  }
  return incrementer
}

let incrementByTen = makeIncrementer(forIncrement: 10)
incrementByTen()	// 10
incrementByTen()	// 20
incrementByTen()	// 30
```

incrementer()의 body를 살펴보면 runningTotal과 amount는 없지만 함수는 정상적으로 결과를 리턴했다. 그 이유는 runningTotal과 amount가 캡쳐되어 그 변수를 `공유`하기 때문이다. (계산이 `누적된` 결과를 갖는다.)

```swift
let incrementBySeven = makeIncrementer(forIncrement: 7)
incrementBySeven()	// 7
```

위와 같은 결과는 새로운 클로저를 생성하면 기존의 클로저와 다른 클로저이기 때문이다. (`고유의 저장소`에 캡쳐해서 사용하기 때문에 값도 다르다.)

<br/>

## 5. 클로저는 참조 타입

위 예제에서 `incrementByTen`, `incrementBySeven`을 상수에 할당했지만 runningTotal 변수는 계속 증가시킬 수 있었다. 바로 함수와 클로저가 `참조 타입`이기 때문이다. 그래서 한 클로저를 두 상수나 변수에 할당하면 그 둘은 같은 클로저를 참조하고 있는 것이다.

<br/>

## 6. Escaping Closures

클로저를 함수의 인자로 넣을 수 있는데, `비동기`로 실행되거나 `completionHandler`로 사용되는 클로저처럼 함수가 끝나고 함수의 밖에서 실행되는 클로저는 인자 타입 앞에 `@escaping` 키워드를 붙여야한다. `@escaping`을 선언하면 해당 클로저 탈출을 허용한다고 명시해주는 것이라고 할 수 있다.

```swift
var completionHandlers: [() -> Void] = []
func someFunctionWithEscapingClosure(completionHandler: @escaping () -> Void) {
  completionHandlers.append(completionHandler)
}
```

위 함수에서 인자로 전달된 `completionHandler`는 위 함수가 끝나고 그 다음에 처리된다. `@escaping`을 사용하면 클로저에는 `self`를 명시적으로 언급해야 한다.

<br/><br/><br/>