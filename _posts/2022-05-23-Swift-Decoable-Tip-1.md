---
layout: post
title: "[Swift] Swift Decoable Tip (1) - 우태헌"
tags: [Swift, Decodable, KeyDecodingStrategy]
comments: true
---

<br/>

### Write By 우태헌
[Blog](https://oen0609.tistory.com/67)

***

아마 SwiftyJSON을 사용하는 케이스가 아니라면 자연스럽게 Codable을 많이 이용하실 겁니다.

 

개인적으로 SwiftyJSON은 직관적(?)으로 내부 구조에 접근하는 반면, Codable을 사용할 때에는 다소 귀찮은 점이 많았습니다. 특히 Decode를 할 때가 많다보니, 종종 서버 응답이 복잡한 구조라면 골치가 아픈 적이 종종 있어서, 이번 기회에 개인적으로 사용하고 있는 팁을 두 번의 포스팅을 통해 짧게나마 나누어 적어두고자 합니다.


## KeyDecodingStrategy
대부분의 iOS개발에서 권장되는 변수 네이밍 규칙 중 하나가 camelCase인데, 서버응답은 snake_case로 올 때가 매우 빈번하죠

그러다보니 CodingKeys를 활용하여 프로퍼티 하나 하나 별도로 매칭 시켜주는 경우가 많습니다. 하지만, JSONDecoder.KeyDecodingStrategy을 사용하면, 시간 절약이 많이 되는 경험을 할 수 있습니다.

 

먼저 CodingKeys을 활용한 예시입니다.


```swift
struct User: Decodable {
  let id: UInt
  let nickname: String
  let profileImageUrl: String
  let description: String
  
  enum CodingKeys: String, CodingKey {
    case id, nickname, description
    case profileImageUrl = "profile_image_url"
  }
}
```

위에는 1개만 별도로 매칭시켜주었지만, 계속 늘어갈수록 피곤함이 늘어나는 작업이 될 겁니다.

```swift
do {
  let jsonData = json.data(using: .utf8)!
  let decoder = JSONDecoder()
  decoder.keyDecodingStrategy = .convertFromSnakeCase
  let decodedData = try decoder.decode(User.self, from: jsonData)
} catch {
  // catch error
}
```

하지만, 위 처럼 KeyDecodingStrategy을 활용하면, 일일이 CodingKeys 매치시켜주지 않아도 됩니다.

해당 로직의 활용도가 높다면, Extension이나 JSONDecoder를 커스터마이징한 후, 활용해서 처리해도 되겠죠

```swift
class MyDecoder: JSONDecoder {
  override init() {
    super.init()
    keyDecodingStrategy = .covertFromSnakeCase
  }
}
```
