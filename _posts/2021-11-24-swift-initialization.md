---
layout: post
title:  "[Swift] 초기화 (Initialization)"
date:  2021-11-24 00:00:00
author: bobo
categories: Swift
tags:  Swift
cover:  "/assets/instacode.png"
---

# [Swift] 초기화 (Initialization)



## 1. 초기화?

[애플 공식 문서](https://docs.swift.org/swift-book/LanguageGuide/Initialization.html)에서 초기화란 Class, Struct, Enum 인스턴스를 사용하기위해 준비 작업을 하는 단계라고 설명한다. 이 단계에서 각 저장 프로퍼티의 초기 값을 설정한다. 초기화 과정은 `initializer`를 정의 하는 것으로 구현할 수 있다. Swift의 `initializer`는 값을 반환하지 않는다. 초기화와 반대로 여러 값과 자원의 해지를 위해 `deinitializer`도 사용할 수 있다.

<br/>

## 2. 기본 값을 지정하여 초기화

프로퍼티의 선언과 동시에 값을 할당하면 그 값을 초기 값으로 사용할 수 있다. 항상 같은 초기 값을 갖는다면 기본 프로퍼티를 사용하는 것이 좋다. 이 기본 값은 상속 시에도 함께 상속된다.

```swift
struct Person {
  var name: String = "bobo"
}

let person = Person()
print("My name is \(person.name)")
// Prints "My name is bobo"
```

<br/>

## 3. 이니셜라이저를 사용한 초기화

이니셜라이저는 특정 타입의 인스턴스를 생성한다. 이니셜라이저의 가장 간단한 형태는 파라미터가 없고 `init` 키워드를 사용한다. 이니셜라이저는 메소드와 달리 괄호 앞에 식별할 수 있는 이름을 가지지 않기 때문에 `매개 변수 이름 및 타입`이 이니셜라이저를 식별하는데에 중요한 역할을 한다.

```swift
struct Person {
  var name: String
  init() {
    name = "bobo"
  }
}

let person = Person()
print("My name is \(person.name)")
// Prints "My name is bobo"
```

<br/>

## 4. Class에서의 초기화

### Designated init (지정 초기자)

이 init은 클래스의 `모든 프로퍼티`가 초기화되어야 한다. 만약 클래스 프로퍼티 중에 하나라도 초기화가 되지않으면 에러가 발생하게 된다. 클래스 타입은 반드시 한 개 이상의 Designated init이 필요하다.

```swift
class Person {
  var name: String
  init(name: String) {
    self.name = name
  }
}
```



### Convenience init (편의 초기자)

클래스의 원래 이니셜라이저인 Designated init을 도와주는 역할의 `보조 이니셜라이저`다. 그렇기 때문에 Convenience init을 사용하려면 Designated init이 먼저 선언되어야 한다.<br/>

Swift의 이니셜라이저 규칙에는 몇 가지가 있다. 그 중에는

> convenience init은 같은 클래스에서 다른 이니셜라이저를 호출해야 한다. convenience가 아닌 init은 블록 안에 다른 이니셜라이저를 부를 수 없다.

라는 규칙이 있다.

```swift
class Person {
  var name: String
  var age: Int
  init(name: String, age: Int) {
    self.name = name
    self.age = age
  }
  convenience init(age: Int) {
    self.init(name: "bobo", age: age)
  }
}
```



### required init

생성자의 `재정의`를 `필수적`으로 해줘야 한다는 것을 알려주기 위해 작성한다. 

<br/>

## 5. Struct에서의 초기화

구조체는 기본적으로 제공되는 이니셜라이저가 두 가지가 있다.

1. 멤버와이즈 이니셜라이저: 프로퍼티를 매개 변수로 가지는 이니셜라이저
2. 기본 이니셜라이저: 매개 변수가 없는 이니셜라이저

구조체 내에서 프로퍼티가 하나라도 초기화되지 않은 경우, `초기 값이 없는 속성`을 매개 변수로 갖는 이니셜라이저`(=멤버와이즈 이니셜라이저)`가 제공된다.

```swift
struct Person {
  var name: String
  var age: Int = 0
}

let person = Person(name: "bobo")
```

프로퍼티가 모두 초기화되어 있는 경우에는 매개 변수가 없는 이니셜라이저`(=기본 이니셜라이저)`도 제공된다.

```swift
struct Person {
  var name: String = "bobo"
}

let person1 = Person()
let person2 = Person(name: "kiki")
print("person1.name is \(person1.name), person2.name is \(person2.name)")
// Prints "person1.name is bobo, person2.name is kiki"
```

다만 프로퍼티가 모두 let으로 초기화되어 있다면 멤버와이즈 이니셜라이저는 제공되지 않는다.

```swift
struct Person {
  let name: String = "bobo"
}

let person1 = Person()
let person2 = Person(name: "kiki")	// 에러 발생. let으로 선언되었고 값이 이미 초기화되어 있기 때문에 값을 변경할 수 없다.
```

기본 제공하는 두 가지 이니셜라이저를 사용하지 않고 따로 정의해서 사용한다면 더 이상 기본 이니셜라이저 또는 멤버와이즈 이니셜라이저에 접근할 수 없다.

```swift
struct Person {
  var name: String
  var age: Int
  
  init(age: Int) {
    self.name = "bobo"
    self.age = age
  }
}

let person1 = Person(age: 10)
let person2 = Person(name: "bobo", age: 20)	// 구조체 내부에 이니셜라이저를 구현했기 때문에 멤버와이즈 이니셜라이저는 사용할 수 없다.
```

`사용자 지정 이니셜라이저`를 사용하면서 `기본 이니셜라이저` 또는 `멤버와이즈 이니셜라이저`도 사용하고 싶다면, 사용자 지정 이니셜라이저를 `extension`에 구현하면 된다.

```swift
struct Person {
  var name: String
  var age: Int
}

extension Person {
  init(age: Int) {
    self.name = "bobo"
    self.age = age
  }
}

let person1 = Person(age: 10)
let person2 = Person(name: "bobo", age: 20)
```

<br/>

## 6. 상속에서의 초기화

상속에서의 초기화에도 규칙이 있다.

> 상속받은 프로퍼티를 제외한 멤버 먼저 모두 초기화한다.
> super class로부터 상속받은 프로퍼티들은 super class의 designated init을 호출하여 모두 초기화한다.

그렇지 않으면 super class의 designated init으로 인해 값이 다시 덮어씌워질 위험이 있다. 

```swift
class Person {
  var name: String
  
  init(name: String) {
    self.name = name
  }
}

class Bobo: Person {
  var phoneNumber: String
  
  init() {
    self.phoneNumber = "010-0000-0000"
    super.init(name: "bobo")
    self.name = "kiki"
  }
}

let bobo = Bobo()
print("name is \(bobo.name), phoneNumber is \(bobo.phoneNumber)")
// Prints "name is kiki, phoneNumber is 010-0000-0000"
```

<br/>
<br/>
<br/>