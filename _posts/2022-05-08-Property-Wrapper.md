---
layout: post
title: "[Swift] Property Wrapper - "
tags: [Property Wrapper, iOS, Swift]
comments: true
---

<br/>

### Write By 우태헌
[Blog](https://oen0609.tistory.com/65)

***

이름그대로 property를 감싸는 기능이고, property get/set영역이 중복되는 경우가, 여러 파일에 걸쳐 산재되어 있는 경우에 적합한 기능입니다.

개인적으로는 현업 코드에도 종종 활용하는 기능이고, 주로 UserDefault활용할 때 사용하고 있습니다.

https://github.com/apple/swift-evolution/blob/master/proposals/0258-property-wrappers.md

사용방법은 위의 링크에도 나와있지만, 개인적으로 사용하는 방법을 간단하게 소개합니다

```swift
@propertyWrapper
struct MyDefaults<Value> {
    let key: String
    let defaultValue: Value
    var userDefaults: UserDefaults = .standard

    var wrappedValue: Value {
        get {
            return userDefaults.value(forKey: key) as? Value ?? defaultValue
        }
        
        set {
            userDefaults.setValue(newValue, forKey: key)
        }
    }
}
```

위의 코드는 UserDefaults와 함께 주로 활용하는 방법입니다.

wrapper 구조체를 먼저 만들어서, 내부에 UserDefaults를 이용하여 get/set을 하는 부분을 구현화하면 준비 끝!

```swift
enum ABTest {
    @MyDefaults(key: "my_test", defaultValue: false)
    static var isB: Bool
}

func setup() {
    Task {
        ABTest.isB = await API.call()

        if ABTest.isB {
            print("B")
        } else {
            print("A")
        }
    }
}
```

그리고 UserDefaults을 활용해야하는 영역에, 선언한 wrapper로 감싸주면, 

isB를 위한 UserDefaults 코드는 필요가 없고, 

일반 property처럼 get/set을 자유롭게 해주면서 동시에, UserDefaults도 활용하게 되는 것을 볼 수 있습니다.

이외에도 활용가능성은 많고, 개발자 필요에 따라 요긴하게 사용될 수 있기에, 관심있으신 분들은 공부해보시길 추천드려요

