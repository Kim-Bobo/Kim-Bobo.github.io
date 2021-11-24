---
layout: post
title:  "[Swift] Generic"
date:   2021-11-08 00:00:00
author: bobo
categories: Swift
tags:	Swift
cover:  "/assets/instacode.png"
---

# [Swift] Generic



## 1. Generic?

[애플 공식 문서](https://docs.swift.org/swift-book/LanguageGuide/Generics.html#//apple_ref/doc/uid/TP40014097-CH26-XID_283)부터 살펴보면, `Generic`은 Swift의 가장 강력한 기능 중 하나이며 Swift 표준 라이브러리의 대부분은 Generic 코드로 빌드된다고 설명한다. 일반 함수 대신 **제네릭 함수(Generic Function)**를 사용하면 코드의 중복을 피하고 재사용 가능하게 작성할 수 있다.

여기 두 개의 Int 값을 교환하는 일반 함수가 있다.

```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
  let temporaryA = a
  a = b
  b = temporaryA
}
```

위 일반 함수의 파라미터는 Int 타입으로 선언되어 있기 때문에 Int만 파라미터로 받을 수 있다.<br/>
다른 타입으로도 서로 두 개의 값을 교환하고 싶다면, 각 타입마다 교환하는 함수를 새로 만들어야 한다.

```swift
func swapTwoStrings(_ a: inout String, _ b: inout String) {
  let temporaryA = a
  a = b
  b = temporaryA
}

func swapTwoDoubles(_ a: inout Double, _ b: inout Double) {
  let temporaryA = a
  a = b
  b = temporaryA
}
```

String형 값을 교환하는 함수와 Double형 값을 교환하는 함수도 만들어주었다.<br/>
파라미터로 받는 타입만 다를 뿐, 함수 안에서 실행하는 동작은 모두 일치한다.

이럴 때, 사용하는게 바로 `제네릭 함수`이다.

```swift
func swapTwoValues<T>(_ a: inout T, _ b: inout T) {
  let temporaryA = a
  a = b
  b = temporaryA
}
```

`제네릭 함수`는 일반 함수와 달리 함수 이름 옆에 `Placeholder 타입 이름`이 온다.<br/>
T는 `타입 파라미터`라고 부르며, 이 T는 swapTwoValues 함수가 호출될 때마다 **결정**된다.

완성된 코드를 갖고 값을 넣어 함수를 호출해보면,

```swift
var firstNum = 10
var secondNum = 394
swapTwoValues(&firstNum, &secondNum)
print("firstNum is \(firstNum), secondNum is \(secondNum)")
// firstNum is 394, secondNum is 10

var firstText = "Hello"
var secondText = "bobo"
swapTwoValues(&firstText, &secondText)
print("firstText is \(firstText), secondText is \(secondText)")
// firstText is bobo, secondText is Hello
```

정상적으로 두 값이 교환된 것을 확인할 수 있다.<br/>
타입 파라미터인 T는 이름일 뿐 아무렇게나 써도 문제는 없다.

```swift
func swapTwoValues<Hello>(_ a: inout Hello, _ b: inout Hello) {
  let temporaryA = a
  a = b
  b = temporaryA
}
```

이렇게 쓰는거도 가능하다. 하지만 이름에는 자신의 역할이 담겨있는게 좋다.<br/>
swapTwoValues 함수같은 경우에는 타입 파라미터와 의미있는 관계가 아니기 때문에 T와 같은 단일 문자를 사용하는 것이 일반적이다. ex) T, V, U, ...

<br/>



## 2. 타입 제약(Type Constraint)

제네릭을 활용해서 두 개의 값을 받아서 두 값이 같으면 true, 다르면 false를 반환하는 함수를 만들어보자.

```swift
func findIndex<T>(of valueToFind: T, in array: [T]) -> Int? {
  for (index, value) in array.enumerated() {
    if value == valueToFind {	// Binary operator '==' cannot be applied to two 'T' operands
      return index
    }
  }
  return nil
}
```

작성해보니 컴파일 에러가 발생한다. `==`연산자는 `Equatable`프로토콜을 준수할 때만 사용할 수 있기 때문이다. `Equatable`를 준수하는 모든 타입은 `==`, `!=`연산자를 두 값의 비교를 위해 사용할 수 있다.

```swift
func findIndex<T: Equatable>(of valueToFind: T, in array: [T]) -> Int? {
  for (index, value) in array.enumerated() {
    if value == valueToFind {
      return index
    }
  }
  return nil
}
```

`타입 파라미터`가 `Equatable` 프로토콜을 준수하게 해주니, 에러가 사라진 것을 볼 수 있다.

<br/>



## 3. 제네릭 타입(Generic Type)

제네릭은 함수에만 가능한 것이 아니다.<br/>
구조체, 클래스, 열거형 타입에도 선언할 수 있는데, 이것을 `제네릭 타입(Generic Type)`이라고 한다.

```swift
struct Stack<Element> {
  var items: [Element] = []
  mutating func push(item: Element) {
    items.append(item)
  }
  mutating func pop() -> Element {
    return items.removeLast()
  }
}
```

제네릭 함수와 동일하게 `<>` 안에 타입 파라미터 이름을 선언해주면 된다.<br/>
제네릭 타입의 인스턴스를 만들고 싶을때는, `<>` 안에 Stack에 저장할 유형을 작성하여 인스턴스를 만들 수 있다.

```swift
var stackOfStrings = Stack<String>()
stackOfStrings("one")
stackOfStrings("two")
stackOfStrings("three")
stackOfStrings("four")
```

<br/><br/><br/>

