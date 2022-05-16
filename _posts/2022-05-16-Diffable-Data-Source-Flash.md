---
layout: post
title: "[iOS] Diffable Data Source 점멸현상 방지 실험 - 우태헌"
tags: [iOS, UICollectionViewDiffableDataSource, UITableViewDiffableDataSource]
comments: true
---

<br/>

### Write By 우태헌
[Blog](https://oen0609.tistory.com/66)

***

최근 Rx기반의 구조에서 멀어지고 있어서, RxDataSource를 대체하는 UITableViewDiffableDataSource나 UICollectionViewDiffableDataSource를 활용하고 있습니다.

[UITableViewDiffableDataSource](https://developer.apple.com/documentation/uikit/uitableviewdiffabledatasource)
[UICollectionViewDiffableDataSource](https://developer.apple.com/documentation/uikit/uicollectionviewdiffabledatasource)


## 점멸(?) 현상 방지

RxDataSource를 사용할 때도 마찬가지였지만, 별다른 고려없이 UITableViewDiffableDataSource나 UICollectionViewDiffableDataSource를 활용하여 데이터를 reload하면 Cell들이 번쩍번쩍하는 점멸현상이 발생하는데,

개인적인 경험으로 이 현상이 발생하는 경우가 아래의 2가지 경우였습니다.

1. reload animation 처리 미흡
2. 사용되는 ItemIdentifierType의 Hashable 또는 Equatable 프로토콜 관련 처리 미흡


### 1. reload animation 처리

관련하여 아래의 블로그나 사이트를 참고하여 처리 시도를 먼저 했습니다.

- https://brunch.co.kr/@eunjin3786/245

- https://developer.apple.com/videos/play/wwdc2021/10252/

- https://www.jessesquires.com/blog/2021/07/08/diffable-data-source-behavior-changes-and-reconfiguring-cells-in-ios-15/


```swift
extension UICollectionViewDiffableDataSource {
  func applySnapshot(
    _ snapshot: NSDiffableDataSourceSnapshot<SectionIdentifierType, ItemIdentifierType>,
    animatingDifferences: Bool,
    completion: (() -> Void)? = nil
  ) {
    if #available(iOS 15.0, *) {
      self.apply(snapshot, animatingDifferences: animatingDifferences, completion: completion)
      return
    }
    
    if animatingDifferences {
      self.apply(snapshot, animatingDifferences: true, completion: completion)
      return
    }
    
    UIView.performWithoutAnimation {
      self.apply(snapshot, animatingDifferences: true, completion: completion)
    }
  }
}
```

개인 플젝 같은 곳에서는 위와 같이 사용해봤지만, 간혹 위의 extension 추가로도 Cell이 번쩍이는 현상이 지속되는 경우가 있습니다.

(경험적으로 보통 이미지가 있을 때 발생)

위의 예시처럼 분기처리를 하는 이유는 iOS 15 이전에는 animatingDifferences가 false이면 reloadData를 호출하기 때문에, 버전에 따라 분기처리되었습니다.


### 2. Hashable 또는 Equatable 프로토콜 준수
위의 언급된 animation처리에도 불구하고, 번쩍인다면 data source에 사용되고 있는 Item의 Hashable 또는 Equatable 기준을 변경해야할 수 있습니다.

```swift
struct CellData: Hashable {
  let id: Int64
  let title: String
  let imageUrl: String
  let isLike: Bool
}


struct Item: Hashable {
  let data: CellData
  
  static func == (lhs: Item, rhs: Item) -> Bool {
    lhs.data.id == rhs.data.id
  }
}
```

위의 예시 처럼 Item가 의존하고있는 data가 Hashable를 준수하고 있다면, 

굳이 별도로 hash나 ==를 재정의해줘야할 이유가 없지만, isLike만 변해도 Cell이 update되기에 별도의 재정의 진행했습니다.

---

아직 Diffable Data Source의 숙련도나 이해수준이 높지 않아서 잘못된 정보일 수도 있으나, 경험상으로 사용했을 때 겪었던 문제에 대해 어떻게 해결했는지 풀어봤습니다.
