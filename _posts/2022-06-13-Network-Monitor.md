---
layout: post
title: "[Swift] Network Monitor - 민경준"
tags: [swift, network, monitor]
comments: true
---

<br/>

### Write By
[민경준 - velog](https://velog.io/@jxxnnee)


## Declaration
swift에서 개발을 하다보면 꼭 필요한것이 네트워크 상태 체크입니다. 네트워크 상태 체크를 어떻게 하는것이 좋을지 고민하다 찾아보니 방법은 의외로 간단 했습니다.

우선 swift에는 `Network`라는 기본 라이브러리가 있는데 이것을 이용해주면 됩니다. 이것을 이용하면 현재 네트워크의 상태, 종류 등.. 다양한 정보들을 알 수 있습니다. 기본적으로 어떻게 사용하는지 하나씩 알아보도록 합시다.

<br/>

## How To Use
첫번째로 네트워크 상태를 항상 체크할 수 있는 `monitor` 객체와 이것에 필요한 `DispatchQueue`가 필요합니다.

```swift
import Network

private let queue: DispatchQueue = DispatchQueue.global()
private let monitor: NWPathMonitor = NWPathMonitor()
```

<br/>

다음으로는 네트워크 상태가 업데이트 됐을 때 어떤 동작을 할지에 대한 `updateHandler`를 정의하도록 합시다.
여기서 리턴값으로 `NWPath`라는 객체를 던져주는데 해당 객체에는 네트워크 인터페이스에 대한 값을 가지고 있지 않지만 정보는 갖고 있어서 어떤 타입을 사용하고 있는지 함수로 전달받을 수 있습니다.
(왜 이렇게 해뒀는지는 개인적으로 의문...)
```swift
self.monitor.pathUpdateHandler = { path in
    /// NWPath의 usesInterfaceType 함수를 통해서 어떤 InterfaceType을 사용하는지 유추해야 한다.
    if path.usesInterfaceType(.wifi) {
        print("Network Type: Wifi")
    } else
    if path.usesInterfaceType(.cellular) {
        print("Network Type: Cellular")
    } else {
        print("Network Type: Other Type")
    }
}
```

<br/>

세번째로는 이제 위에서 선언해둔 `DispatchQueue`를 가지고 상태체크를 시작하도록 해주면 완성입니다.
끝낼 때는 `cancel()` 함수를 사용하면 됩니다.
```swift
self.monitor.start(queue: self.queue)

self.monitor.cancel()
```

<br/>


## Applied
그리고 이걸 응용하여 어플리케이션이 시작할때 모니터링을 동시에 같이 시작하고 상태가 바뀔때마다 `Notification`을 통해 상태값을 받을 수 있도록 코드를 짜봤습니다.

우선 `Singleton`을 통해 외부에서 하나의 객체를 가지고 사용하도록 했습니다. 그리고 상태 알림을 받을 `NotificationName`을 선언 해 두었습니다.

```swift
final class NetworkHelper {
    public static let `default`: NetworkHelper = NetworkHelper()
    public static let statusDidChanged = Notification.Name("NetworkStatusDidChanged")
}
```

<br/>

다음으로는 `pathUpdateHandler`가 호출 될 때마다 `Notification`으로 상태를 알려주면 되겠죠?
그리고 다른 클래스에서 상태 값을 받을 때는 `NWPath`객체를 통해 핸들링을 해주면 될거 같습니다.
```swift
self.monitor.pathUpdateHandler = { path in
    NotificationCenter.default.post(name: NetworkHelper.statusDidChanged, object: path)
}
```
```swift
class OtherClass {
    NotificationCenter.default.addObserver(self, selector: #selector(self.networkStatusHandler(_:)), name: NetworkHelper.statusDidChanged, object: nil)

    @objc
    fileprivate func networkStatusHandler(_ notification: Notification) {
        guard let path = notification.object as? NWPath else { return }
        print("NETWORK DID CHANGED")
    }
}

```

<br/>

마지막으로 상태 체크 시작 및 종료에 대한 함수를 선언해서 `SceneDelegate`에서 호출하도록 합니다.
```swift
final class NetworkHelper {
    .
    .
    .
    
    public func start() {
        self.monitor.start(queue: self.queue)
    }

    public func stop() {
        self.monitor.cancel()
    }
}


@available(iOS 13.0, *)
class SceneDelegate: UIResponder, UIWindowSceneDelegate {
    func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
        NetworkHelper.default.start()
    }
}
```

<br/>
<br/>

