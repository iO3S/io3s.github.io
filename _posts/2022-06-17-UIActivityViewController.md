---
layout: post
title: "[Swift] File, Image 공유하기 - 민경준"
tags: [swift, UIActivityViewController, share]
comments: true
---

<br/>

### Write By
[민경준 - velog](https://velog.io/@jxxnnee)

<br/>

# Overview

앱을 제작 하다보면 특정 아이템을 외부로 공유하거나 외부 어플에 Export 하고 싶을 때가 있습니다.
그런 경우에는 `UIActivityViewController`를 통해 활용할 수 있습니다.

해당 클래스는 `iOS 6.0` 이상 부터 사용이 가능합니다.
`iPod Touch` 혹은 `iPhone`에서는 `Modal` 스타일로 띄워야 하고 
`iPad` 에서는 `Pop Over` 스타일로 띄워야 합니다.

<br/>

# How To Use

기본적인 사용법 부터 알아보도록 하겠습니다.
```swift
/// 1. item 및 viewController 생성
let activityItems: [Any] = ["Hello, World"]
let activityViewController: UIActivityViewController = .init(activityItems: activityItems, applicationActivities: nil)

/// 1-1. iPad를 사용한다면 popOverPresentationController 설정을 위해
/// popOverPresentation을 띄울 View를 설정해준다.
activityViewController.popOverPresentationController?.sourceView = self.view

/// 2. 기본적으로 제공되는 UIActivityType 중에 사용하지 않을 Type을 지정해준다.
activityViewController.excludedActivityTypes = [.postToTencentWeibo, .postToTwitter]

/// 2-1. activityViewController가 완료되었을 때 호출되는 handler를 정의해준다.
activityViewController.completionWithItemsHandler = { type, completed, items, error in
    if completed {
        print("COMPLETED: ", items)
    } else {
        print("FAILED: ", error.debugDescription)
    }
}

/// 3. activityViewController를 present 한다.
self.present(activityViewController, animated: true)
```



> ### ActivityType
```swift
/// Safari의 읽기 목록에 추가
static let addToReadingList: UIActivity.ActivityType
/// airDrop으로 공유하기
static let airDrop: UIActivity.ActivityType
/// 연락처에 내보내기
static let assignToContact: UIActivity.ActivityType
/// clipboard에 복사하기
static let copyToPasteboard: UIActivity.ActivityType
/// 이메일 보내기
static let mail: UIActivity.ActivityType
/// PDF파일로 열기 (iOS 11.0)
static let markupAsPDF: UIActivity.ActivityType
/// 메세지 보내기
static let message: UIActivity.ActivityType
/// iBooks로 열기
static let openInIBooks: UIActivity.ActivityType
/// Facebook으로 공유하기
static let postToFacebook: UIActivity.ActivityType
/// Flickr에 이미지 공유하기
static let postToFlickr: UIActivity.ActivityType
/// Tencent Weibo에 공유하기
static let postToTencentWeibo: UIActivity.ActivityType
/// Twitter에 공유하기
static let postToTwitter: UIActivity.ActivityType
/// Vimeo에 비디오 공유하기
static let postToVimeo: UIActivity.ActivityType
/// Weibo에 공유하기
static let postToWeibo: UIActivity.ActivityType
/// 프린트하기
static let print: UIActivity.ActivityType
/// 카메라 롤에 저장하기
static let saveToCameraRoll: UIActivity.ActivityType
/// SharePlay로 공유하기 (iOS 15.4)
static let sharePlay: UIActivity.ActivityType
```

<br/>

## Result


<img src="https://velog.velcdn.com/images/jxxnnee/post/64ecc141-5ffc-41dd-aee9-b153b62d8ea6/image.png" width="50%">


<br/>

# Applied

## 1. UIActivityItemSource
그리고 위의 방법 말고도 `UIActivityViewController`의 `UIActivityItemSource` 프로토콜을 사용하는 방법이 있습니다. 

`UITableView` 또는 `UICollectionView`의 `dataSource`를 이용하는 방법과 비슷하지만 `tableView.dataSource = self` 처럼 `dataSource`를 직접 지정해주는 방식이 없기때문에 사용법을 잘 모를수도 있습니다.

그렇다면 대체 어떻게 사용하는걸까요?

```swift
/// 1. Activity Item을 설정할 때 현재의 ViewController로 설정을 합니다.
let activityItems: [Any] = [self]

/// 2. 해당 ViewController에 UIActivityItemSource를 상속합니다.
/// 필수 구현 함수 두 가지를 정의해줍니다.
extension ViewController: UIActivityItemSource {
    /// 이 부분은 정확히 어떤 용도로 쓰이는지 확인이 안되네요 ㅠ,ㅠ
    func activityViewControllerPlaceholderItem(_ activityViewController: UIActivityViewController) -> Any {
        return "PlaceHolder"
    }
    
    /// Activity Type에 따라서 어떤 Data를 내보낼지 선택할 수 있습니다.
    func activityViewController(_ activityViewController: UIActivityViewController, itemForActivityType activityType: UIActivity.ActivityType?) -> Any? {
        if activityType == .postToTwitter {
            return "Download #MyAwesomeApp via @twostraws."
        } else {
            return "Download MyAwesomeApp from TwoStraws."
        }
    }
}
```

<br/>

### Result

#### Twitter로 열었을 때
<img src="https://velog.velcdn.com/images/jxxnnee/post/40d6663c-e20e-4761-89e0-37e7c0fc1cb8/image.png" width="50%">

<br/>

#### 메모장으로 열었을 때
<img src="https://velog.velcdn.com/images/jxxnnee/post/2213b6d5-d6b4-4130-ba3f-874b707fbd4f/image.png" width="50%">




<br/>


## 2. UIActivity
`UIActivityViewController`를 오픈했을 때 사용 할 `Activity`도 직접 생성할 수 있습니다.

기본적인 사용방법에 대해서 알아보도록 하겠습니다.
`UIActivity`를 서브클래싱 할 때 반드시 `override` 해야 할 변수와 함수가 있습니다.

```swift
/// action 또는 share 
class var activityCategory: UIActivity.Category 
/// Custom ActivityType
var activityType: UIActivity.ActivityType?
/// Activity의 이름
var activityTitle: String? 
/// Activity에 사용 될 이미지
var activityImage: UIImage?
/// 사용자가 공유 할 Item을 처리할 수 있는지 여부를 정하는 함수
func canPerform(withActivityItems: [Any]) -> Bool
/// 사용자가 공유 할 Item을 미리 받아 볼 수 있는 함수
func prepare(withActivityItems: [Any])
```

<br/>


이렇게 총 여섯가지의 변수와 함수에 더해서 `perform()` 함수와 여러 커스텀 변수들을 선언해주는것으로 서브클래싱을 완성할 수 있습니다.

```swift
final class Activity: UIActivity {
    static let type: UIActivity.ActivityType = .init(rawValue: "com.yoursite.yourapp.activity")
    var _activityTitle: String
    var _activityImage: UIImage?
    var activityItems = [Any]()
    var action: ([Any]) -> Void
    
    override var activityTitle: String? {
        get {
            self._activityTitle
        }
    }
    override var activityImage: UIImage? {
        get {
            self._activityImage
        }
    }
    
    /// Activity를 생성할 때 제목과 이미지를 반드시 받고 
    /// 해당 Acitity를 선택했을 때 어떤 동작을 할지에 대한 Handler도 매개 변수로 받는다.
    init(title: String, image: UIImage?, performAction: @escaping ([Any]) -> Void) {
        self._activityImage = image
        self._activityTitle = title
        
        self.action = performAction
    }
    
    /// 직접 Type을 만들어서 지정해준다.
    override var activityType: UIActivity.ActivityType {
        return self.type
    }
    
    /// .action 과 .share 두가지 카테고리가 있다.
    override class var activityCategory: UIActivity.Category {
        return .share
    }
    
    /// 유저가 공유하는 item이 해당 Activity에서 사용가능한지 여부를 묻는 함수 
    override func canPerform(withActivityItems activityItems: [Any]) -> Bool {
        return true
    }
    
    /// 미리 item을 받아볼 수 있는 함수
    override func prepare(withActivityItems activityItems: [Any]) {
        self.activityItems = activityItems
    }
    
    /// 해당 Activity를 눌렀을 때 호출되는 함수
    override func perform() {
        /// Handler를 호출하여 해당 Activity를 눌렀음을 알린다.
        self.action(self.activityItems)
        /// Activity에서 작업이 완료되었음을 시스템에 알려준다.
        /// 성공적으로 완료되었으면 true
        /// 에러가 있었거나 정상적으로 완료되지 않았을 때는 false를 인자로 넘겨준다.
        /// 해당 Notification을 시스템이 받게되면 UIActivityViewController를 해제한다.
        self.activityDidFinish(true)
    }
}
```

<br/>

그리고 해당 `Activity`는 `UIActivityViewController`를 생성할 때 인자로 넘겨주면 됩니다.
```swift
let title: String = "Winter"
let image: UIImage = .init(named: "winter")
let activity: Activity = .init(title: title, image: image) { items in
    guard let item = items.first as? String else { return }
    print("Item Did Shared: ", item)
}
let activityItems: [Any] = [self]
let activityViewController: UIActivityViewController = .init(activityItems: activityItems, applicationActivities: [activity])
self.present(activityViewController, animated: true)
```

<br/>

### Result
<img src="https://velog.velcdn.com/images/jxxnnee/post/2f452ac4-676f-49c0-ad0d-cfc4f8121115/image.png" width="50%">

Winter라는 `Activity`가 추가된것이 보이시나요?
해당 `Activity`의 카테고리가 `share`일 때 위의 아이콘에 나오게 되고
`action` 같은 경우에는 아래에 `Copy`와 `Save to Files` 같은 위치에 나오게 됩니다.

그럼 카테고리만 변경하면 어떻게 나오는지 볼까요?
(추가로 이미지는 시스템 이미지로 변경했습니다.)


<img src="https://velog.velcdn.com/images/jxxnnee/post/7318438b-5102-4311-907d-70dd95833e3b/image.png" width="50%">




<br/>
<br/>

***

### Reference
https://developer.apple.com/documentation/uikit/uiactivityviewcontroller
https://www.zehye.kr/ios/2020/03/12/12iOS_activity_view_controller/
https://www.hackingwithswift.com/articles/118/uiactivityviewcontroller-by-example
https://www.shrikar.com/blog/ios-sharing-with-uiactivityviewcontroller-in-swift
https://royhelen.tistory.com/25
https://eeyatho.tistory.com/117


### Review
간단할 줄 알았는데 생각보다 여러 형태로 커스텀도 가능해서 놀랐다...
Zoom, Facebook, Twitter 처럼 기본적인 타입 말고도 직접 만든 타입을 제공해서
내가 만든 앱에 유저가 아이템을 공유할 수 있도록 할 때 사용하면 좋을거 같다.



