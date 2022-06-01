---
layout: post
title: "[OOP] Liskov Substitution Principle - 우태헌"
tags: [LSP, SOLID, OOP]
comments: true
---

<br/>

### Write By 우태헌
[Blog](https://oen0609.tistory.com/41)

***

## Liskov Substitution Principle

<br/>

흔히 말하는 SOLID원칙 중 UIKit에서조차 위배된 케이스가 있는 것이 리스코프 치환 법칙입니다.

<br/>

해당 원칙은 상속 등의 관계를 이루어진 두 객체에서 상위 객체를 하위 객체로 치환해도 동작에 문제가 없어야 한다는 원칙입니다.

개발을 하다보면, 상위 객체의 속성이나 행위를 무시해야 하는 경우가 간혹 있게됩니다. 다만, 무시하게 되면 상속의 의미나 목적성이 희미해진다고 생각합니다.

앞으로 개발의 퀄리티를 높이기 위해서, 위배되는 상황을 일부러 만들어 예제로 만들어보았습니다.   



아래의 예제들은 RubberDuck은 Duck을 대체가능해야하나, height의 실제값이 의도한 20아닌 50이 리턴되는 예제입니다.

```swift
#1

class Duck {
    var height: Int
    
    init(height: Int) {
        self.height = height
    }
}

class RubberDuck: Duck {
    override var height: Int {
        get {
            return 50
        }
        
        set {
            //
        }
    }
}

var duck: Duck = RubberDuck(height: 20)

print(duck.height) // -> expected: 20, actual: 50

```

```swift
class Duck {
    private var height: Int?
    
    func setHeight(_ height: Int) {
        self.height = height
    }
    
    func getHeight() -> Int {
        height ?? 0
    }
}

class RubberDuck: Duck {
    override func getHeight() -> Int {
        50
    }
}

var duck: Duck = RubberDuck()
duck.setHeight(20)

print(duck.getHeight()) // -> expected: 20, actual: 50

```
