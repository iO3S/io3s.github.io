---
layout: post
title: "[Swift] Atomic Variables - 민경준"
tags: [swift, atomic, syncronized]
comments: true
---

<br/>

### Write By
[민경준 - velog](https://velog.io/@jxxnnee)


## Declaration
현재 개발중인 프로젝트에서 사용하고 있는 라이브러리의 코드를 보다보니 `Atomic<A>`라는 구조체가 있엇고
참고할 수 있는 링크가 있기에 호기심에 들어가 봤다가 흥미로운 내용인거 같아 번역을 해봅니다.

## What is Atomic Variables
`Atomic Variables`란 무엇일까? 

바로 원자성을 보장하는 변수를 뜻합니다. 멀티 쓰레드 환경에서 동시에 해당 변수에 접근하더라도 `race condition` 문제를 해결 하기 위해서 고안된 방법입니다.

`Swift` 에서는 이를 해결하기 위해 `CGD Queue`를 사용하여 변수에 대한 동기화된 액세스를 제공하는 `Atomic Variables`를 사용합니다.

> **Race Condition 이란?**
두 개 이상의 프로세스가 병행적으로(concurrently) 읽거나 쓰는 동작을 할 때, 공용 데이터에 대한 접근이 어떤 순서에 따라 이루어졌는지에 따라 그 실행 결과가 같지 않고 달라지는 상황을 말한다.
Race의 뜻 그대로, 간단히 말하면 경쟁하는 상태, 즉 두 개의 스레드가 하나의 자원을 놓고 서로 사용하려고 경쟁하는 상황을 말한다.
race condition 에는 Mutual exclusion, deadlock, starvation 이렇게 세 가지의 종류가 있다.

<br/>

## How To Use
기본적인 사용방법에 대해서 알아봅시다.

```swift
final class Atomic<A> {
    private let queue = DispatchQueue(label: "Atomic serial queue")
    private var _value: A
    
    init(_ value: A) {
        self._value = value
    }

    var value: A {
        return queue.sync { self._value }
    }
}
```

<br/>

물론, 단순히 읽기만 하는것은 흥미롭지 못합니다. 우리는 그것을 수정할 방법도 필요합니다.
`value`를 다음과 같이 `get` `set`을 활용하여 변경하는것은 매우 매력적인 방법입니다.

```swift
var value: A {
    get {
        return queue.sync { self._value }
    }
    set { // BAD IDEA
        queue.sync {
            self._value = newValue
        }
    }
}
```

<br/>

하지만 위의 방법은 굉장히 나쁜 방법입니다. 왜 그런지 예제를 통해 확인해봅시다.
```swift
var x = Atomic<Int>(5)
x.value += 1
```

<br/>

충분히 원자성을 확보한것 처럼 보이지만 전혀 그렇지 않습니다. 먼저 값을 읽고 (원자적으로) 값을 증가 시켜서 다시 씁니다(원자적으로). 단순히 하나의 행동으로는 앱을 손상시킬 수 없지만, 다른 스레드가 읽기 및 쓰기를 하는 사이에 값을 수정하게 되면 잘못된 값이 표시될 수 있습니다.

`Swift5` 에서는 `_modify`를 사용하여 해결할 수 있습니다. 하지만, 사용할 수 없을 때에 우리는 다른 해결책이 필요합니다.
대신, 우리는 기본 변수를 원자적으로 변이시키는 돌연변이 방법을 추가하도록 합시다.
```swift
final class Atomic<A> {
    private let queue = DispatchQueue(label: "Atomic serial queue")
    private var _value: A
    init(_ value: A) {
        self._value = value
    }

    var value: A {
        get {
            return queue.sync { self._value }
        }
    }

    func mutate(_ transform: (inout A) -> ()) {
        queue.sync {
            transform(&self._value)
        }
    }
}
```

<br/>

이것은 `call site`를 좀 더 장황하게 만들지만, `thread-safety`를 위한 비용으로는 저렴한 가격입니다. 
```swift
let x = Atomic<Int>(5)
x.mutate { $0 += 1 }
```

> **call site란?**
프로그래밍에서 함수의 `call site`는 함수가 불리는 위치를 뜻 한다.

<br/>
<br/>

***

### Reference
https://www.objc.io/blog/2018/12/18/atomic-variables/
https://iredays.tistory.com/125
https://stormpy.tistory.com/155
https://en.wikipedia.org/wiki/Call_site
