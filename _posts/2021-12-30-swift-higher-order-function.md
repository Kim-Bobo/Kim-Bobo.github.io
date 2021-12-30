---
layout: post
title:  "[Swift] 고차함수"
date:   2021-12-30 00:00:00
author: bobo
categories: Swift
tags:   Swift
cover:  "/assets/instacode.png"
---

# [Swift] 고차함수

고차함수란 하나 이상의 함수를 인자로 받아 결과로 함수를 반환하는 함수를 뜻한다.

- ## map

배열 내부의 값을 하나씩 mapping 하는 역할이다. 각 요소에 대한 값을 변경하고자 할 때 사용하고 결과로 배열을 리턴한다.
컨테이너가 담고 있던 각각의 값을 매개변수를 통해 받은 함수에 적용한 후 새로운 컨테이너를 생성하여 반환한다. (기존 컨테이너의 값은 변경되지 않는다.)
for-in과 비슷하나 비교했을 때 코드가 더 간결하고 재사용에 용이하며 컴파일러 차원에서 성능 차이가 있다.

배열의 값을 2배씩 곱해진 배열을 리턴하는 예제를 만들어보자.

```swift
let arr: [Int] = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
let doubleArr = arr.map { (element: Int) -> Int in 
	element * 2
}
print(doubleArr)	// [2, 4, 6, 8, 10, 12, 13, 16, 18, 20]
```

```swift
let doubleArr = arr.map({ $0 * 2 })
```

아래처럼 축약해서 사용하는 것도 가능하다.

<br/>

- ## filter

각 항목들을 비교하여 일치하는 결과물을 가진 새로운 배열을 리턴한다.

짝수값만 가지는 배열을 리턴하는 예제를 만들어보자.

```swift
let arr: [Int] = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
let evenNumArr = arr.filter { (element: Int) -> Bool in
  element % 2 == 0
}
print(evenNumArr)	// [2, 4, 6, 8, 10]
```

```swift
let evenNumArr = arr.filter({ $0 % 2 == 0 })
```

아래처럼 축약해서 사용하는 것도 가능하다.

<br/>

- ## reduce

기존 컨테이너 내부의 요소들을 결합하여 새로운 값을 만든다. reduc에는 초기값을 계속 업데이트 시켜나가는 방식이기 때문에 초기값 지정이 꼭 필요하다.

배열의 합을 리턴하는 예제를 만들어보자.

```swift
let arr: [Int] = [1, 2, 3, 4]
let arrSum = arr.reduce (0, {x, y in                             
	// 0 + 1
	// 1 + 2
	// 3 + 3
	// 6 + 4
  return x + y
})
print(arrSum)	// 10
```

```swift
let arrSum = arr.reduce(0, +)
```

아래처럼 축약해서 사용하는 것도 가능하다.

<br/>

- ## flatMap

배열 내부의 값을 flatten 하게 만든다. nil을 제거하고 optional binding 해주는 역할이다. Swift 4.1부터 compactMap 이라는 이름으로 바뀌었지만 사라진 것은 아니다. 쓰임에 따라 flatMap, compactMap 을 구별해서 써야한다.

nil이 포함된 2차원 배열을 nil이 제거된 1차원 배열로 변환하는 예제를 만들어보자.

```swift
let multiArray = [[2, 4, 7, nil], [5, nil, 9]]
let flatArray = multiArray.flatMap { $0 }.compactMap { $0 }
print(flatArray)	// [2, 4, 7, 5, 9]
```

2차원 배열에서는 배열 내부의 값을 flatten 하게 만들어주기는 하지만 nil을 제거하고 optional binding 해주지 않기 때문에 flatMap을 한 리턴값에 compactMap을 해주어야 한다. 1차원 배열에서 flatMap을 해준다면 nil이 제거되고 optional binding 된 값이 리턴되지만, deprecated 경고 문구와 함께 compactMap을 사용하라 안내한다.

<br/>

- ## compactMap

flatMap과 비슷하게 nil을 제거하고 optional binding 해주는 역할이다.

nil 제거 후 String으로 형변환하는 배열을 리턴하는 예제를 만들어보자.

```swift
let arrWithNil = [1, 2, nil, 4, 5, 6]
let arrWithoutNilToString = arrWithNil.compactMap { $0 }.map { String($0) }
print(arrWithoutNilToString)	// ["1", "2", "4", "5", "6"]
```

<br/>

마무리로 고차함수를 종합적으로 사용할 수 있는 예제를 하나 만들어보자. 

1. nil이 포함된 2차원 배열을 nil을 제거한 1차원 배열로 만든다.
2. 1차원 배열 내부에서 짝수들로만 이루어진 배열을 만들고 그 합을 구한다.

```swift
let input = [[3, 10, nil], [9, -1, nil, 2], [4, -5, nil, 7]]
let flatArr = input.flatMap { $0 }.compactMap { $0 }
let evenNumArr = flatArr.filter { $0 % 2 == 0 }
let evenNumSum = evenNumArr.reduce(0, +)
print(evenNumSum)	// 16
```

<br/>
<br/>
<br/>