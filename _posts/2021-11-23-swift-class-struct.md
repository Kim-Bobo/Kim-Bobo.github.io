---
layout: post
title:  "[Swift] Class와 Struct"
date:   2021-11-23 00:00:00
author: bobo
categories: Swift
tags:	Swift
cover:  "/assets/instacode.png"
---

# [Swift] Class와 Struct

면접 질문에서도 많이 등장하는 Class와 Struct에 대해서 [애플 공식 문서](https://docs.swift.org/swift-book/LanguageGuide/ClassesAndStructures.html#)와 함께 알아보자.



## 1. Class와 Struct의 공통점

- 여러 변수를 담을 수 있는 컨테이너이다.
- 값을 저장할 프로퍼티를 선언할 수 있다.
- 함수적 기능을 하는 메소드를 선언할 수 있다.
- 내부 값에 .을 사용하여 값에 대해 접근할 수 있다.
- 생성자를 사용하여 초기 상태를 설정할 수 있다.
- extension을 사용하여 기능을 확장할 수 있다.
- Protocol을 채택하여 기능을 설정할 수 있다.

<br/>



## 2. Class와 Struct의 차이점

### Class

- `참조 타입`이다.
- `Heap` 메모리 영역에 할당된다.
- `ARC`로 메모리를 관리한다.
- 같은 클래스 인스턴스를 여러 개의 변수에 할당한 뒤 값을 변형시키면 할당한 모든 변수에 영향을 준다. `(메모리만 복사)`
- `상속`이 가능하다.
- 타입 캐스팅을 통해 클래스 인스턴스의 타입을 확인할 수 있다.
- `deinit`을 사용하여 클래스 인스턴스의 메모리 할당을 해제할 수 있다.

### Struct

- `값 타입`이다.
- `Stack` 메모리 영역에 할당된다.
- 구조체 변수를 새로운 변수에 할당할 때마다 새로운 구조체가 할당된다. `(값 자체를 복사)`
- `Codable` 프로토콜을 이용하여 JSON과 Struct 간에 손쉽게 변환이 가능하다.

<br/>

```swift
class PersonClass {
  var age: Int = 20
  
  deinit {
    print("메모리 할당 해제")
  }
}

struct PersonStruct {
  var age: Int = 20
}

var pClass1 = PersonClass()
var pClass2 = pClass1
var pClass3 = pClass1

pClass3.age = 30
print(pClass1.age)	// 30, class는 참조 타입이기 때문에 pClass3을 변경해도 pClass1까지 변경된다.

var pStruct1 = PersonStruct()
var pStruct2 = pStruct1
var pStruct3 = pStruct1

pStruct3.age = 10
print(pStruct3.age)	// 10
print(pStruct1.age)	// 20, 구조체는 값 타입이기 때문에 pStruct3의 값 변경이 pStruct1에 영향을 끼치지 않는다.
```

<br/>

## 3. Class와 ARC

Class는 `ARC`로 메모리를 관리한다고 한다. 예제와 함께 자세히 알아보자.

```swift
var pClass1: PersonClass? = PersonClass()
print(CFGetRetainCount(pClassARC))		// 2, 디폴트 값

var pClass2 = pClass1
print(CFGetRetainCount(pClassARC))		// 3, 참조 카운트가 증가된다.

pClass1 = nil
print(CFGetRetainCount(pClassARC))		// 2, 참조 카운트가 감소된다.
pClass2 = nil		// 더 이상 참조하는 곳이 없기 때문에 deinit이 호출된다.
```

예제처럼 클래스 인스턴스를 여러 곳에서 참조하게 되면, 원래 인스턴스를 해제하더라도 참조 카운트가 남아있어 `deinit`이 실행되지 않는다. `deinit`이 실행되려면 참조되는 모든 값들을 해제해줘야 한다.

<br/>

## 4. 그렇다면 언제 `Class`를 쓰고 언제 `Struct`를 써야할까?

- 단순한 데이터 값을 보유하기 위해서는 `Struct`가 낫다.
- 메모리의 스택은 크기가 크지 않기 때문에 작은 값을 갖는 데이터를 처리할 때 `Struct`를 사용한다.
- 상속이 필요하지 않고 모델의 사이즈가 그리 크지 않다면 `Struct`를 사용한다.
- `Codable`을 사용하고 싶다면 `Struct`를 사용한다.
- 해당 모델을 `serialize` 해서 전송하거나 파일로 저장할 일이 있으면 `Class` 를 사용한다.

<br/><br/><br/>

