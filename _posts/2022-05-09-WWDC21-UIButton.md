---
layout: post
title: "[iOS] WWDC 21 - Meet the UIKit button system"
tags: [WWDC, UIButton, UIKit]
comments: true
---

<br/>

### Write By
[민경준 - velog](https://velog.io/@jxxnnee)

### Tool Info
Xcode 13.3<br/>
Swift 5.6<br/>
SnapKit 5.0.1<br/>

***

<br/>

## Declaration
최근 `UIButton`의 `contentEdgeInsets` 프로퍼티를 사용하던중 경고창이 하나 떴습니다.

`'contentEdgeInsets' was deprecated in iOS 15.0` iOS15 이후 버전에서는 해당 프로퍼티가 deprecated 되었다는 경고문구 였는데요 확인해보니 지난번 [WWDC21 - Meet the UIKit button system](https://developer.apple.com/videos/play/wwdc2021/10064/) 에서 발표가 되었던 부분이였습니다.

## Deprecated 
이것 말고도 deprecated된 `UIButton`의 property & method 들을 확인해 보자면 아래와 같았습니다.

- [reversesTitleShadowWhenHighlighted](https://developer.apple.com/documentation/uikit/uibutton/1624004-reversestitleshadowwhenhighlight?changes=latest_minor)
- [adjustsImageWhenHighlighted](https://developer.apple.com/documentation/uikit/uibutton/1624031-adjustsimagewhenhighlighted?changes=latest_minor)
- [adjustsImageWhenDisabled](https://developer.apple.com/documentation/uikit/uibutton/1624020-adjustsimagewhendisabled?changes=latest_minor)
- [showsTouchWhenHighlighted](https://developer.apple.com/documentation/uikit/uibutton/1623996-showstouchwhenhighlighted?changes=latest_minor)
- [contentEdgeInsets](https://developer.apple.com/documentation/uikit/uibutton/1624036-contentedgeinsets?changes=latest_minor)
- [titleEdgeInsets](https://developer.apple.com/documentation/uikit/uibutton/1624010-titleedgeinsets?changes=latest_minor)
- [imageEdgeInsets](https://developer.apple.com/documentation/uikit/uibutton/1624034-imageedgeinsets?changes=latest_minor)
- [backgroundRect(forBounds:)](https://developer.apple.com/documentation/uikit/uibutton/1624015-backgroundrect?changes=latest_minor)
- [contentRect(forBounds:)](https://developer.apple.com/documentation/uikit/uibutton/1624027-contentrect?changes=latest_minor)
- [titleRect(forContentRect:)](https://developer.apple.com/documentation/uikit/uibutton/1624001-titlerect?changes=latest_minor)
- [imageRect(forContentRect:)](https://developer.apple.com/documentation/uikit/uibutton/1624007-imagerect?changes=latest_minor)

<br/>

## New Features
그렇다면 iOS15 버전에서 `UIButton`에 어떤것들이 추가 되었는지 확인해 봅시다.

### 1. 새로운 스타일의 버튼들이 추가 됨
`plain, gray, tinted, filled` 스타일의 버튼이 추가 되었습니다.

이 외에도 스토리보드가 아닌 코드로 UIButton을 생성할때에는 `borderless, bordered, borderedTinted, borderedProminent` 스타일을 기본으로 제공합니다.
![](https://velog.velcdn.com/images/jxxnnee/post/a78f516e-866d-4d6a-b98d-e044144c5f3e/image.png)

<br/>

### 2. mutilineText가 지원됩니다.
한 줄로 길게 텍스트를 작성하더라도 버튼의 크기에 맞춰 multiline이 적용되어 나타납니다.
![](https://velog.velcdn.com/images/jxxnnee/post/e3151f9e-e8d9-4aee-8354-a0ad27a3d584/image.png)

<br/>

### 3. Dinamic Type을 지원합니다.
기존에는 버튼의 텍스트 크기를 IB에서 Font 설정을 통해 변경을 했다면 이번 업데이트를 통해
Font 설정이 사라지고 Dynamic Type을 통해 조절을 할 수 있습니다.
![](https://velog.velcdn.com/images/jxxnnee/post/0dff41d1-d80a-4444-b508-c008a4a2ec20/image.png)

<br/>

### 4. SubTitle을 지원합니다.
기존에 버튼에 들어갈 수 있는것은 타이틀과 이미지가 있었지만 이번 업데이트를 통해 SubTitle까지 지원하게 되었습니다.
![](https://velog.velcdn.com/images/jxxnnee/post/d7737902-cf0e-4490-896d-6a79d5328a1a/image.png)

<br/>

### 5. Configuration이 추가되었습니다.
`configuration`으로 많은것들을 설정할 수 있는데, 기존에 button 객체에서 설정하던 `title, image` 와 더불어 `subtitle` 까지 설정 하실 수 있습니다.

이 외에도 `cornerStyle, buttonSize, titleAlingment, titlePadding, imagePadding, title, subtitle, image, etc...` 다양하게 설정 하실 수 있습니다.

![](https://velog.velcdn.com/images/jxxnnee/post/7b6b571a-502d-4195-9a45-9a07a34f7163/image.png)


우선 기본적으로 `UIButton`에 `Configuration`을 적용하는 두 가지 방법을 알아보도록 합시다.

```swift
#1.
let button = UIButton(type: .system)
button.configuration = .filled() 

#2.
var configuration = UIButton.Configuration.filled()
let button = UIButton(configuration: configuration, primaryAction: nil)
```

이렇게 두 가지의 방법이 있지만, 위에 방법은 처음 configuration의 여러 설정들을 세팅할 수 없기 때문에 아래와 같은 방법이 선호되는거 같다(?)

그리고 두번째 방법을 통해 이미지와 타이틀을 세팅하는 방법은 아래와 같습니다.

```swift
var configuration = UIButton.Configuration.filled()
/// 버튼의 타이틀 설정
configuration.title - "삭제"
/// 버튼의 이미지 설정
configuration.image = UIImage(systemName: "trash.fill")
/// 버튼 이미지의 위치 설정
/// top, bottom, leading, tralling
configuration.imagePlacement = .leading
/// 버튼의 이미지 padding 값 설정
configuration.imagePadding = 10
let button = UIButton(configuration: configuration, primaryAction: nil)
```
>![](https://velog.velcdn.com/images/jxxnnee/post/1593eb56-47d6-45c9-9757-239ecfb6fb68/image.png)


그리고 설정된 `Configuration`은 `configurationUpdateHandler`에서 수정이 가능합니다. 해당 핸들러는 `UIButton`의 `State`가 업데이트 될 때 호출이 됩니다.

또는, 수동으로 호출을 하기 위해서는 `UIButton`의 `setNeedsUpdateConfigruation()`을 호출하면 됩니다.

```swift
#1.
button.configurationUpdateHandler = { [unowned self] button in
    guard var config = button.configuration else { return }
    config.subtitle = self.itemDescription
    config.image = button.isHighlighted
        ? UIImage(systemName: "trash.fill")
        : UIImage(systemName: "trash")
        
    button.configuration = config
}

#2.
var itemDescription = String? {
    didSet {
        self.button.setNeedsUpdateConfiguration()
    }
}
```

> ![](https://velog.velcdn.com/images/jxxnnee/post/0a4647f6-db11-4eb3-8ee5-dd79008e42f7/image.mp4)
[이미지 링크](https://velog.velcdn.com/images/jxxnnee/post/0a4647f6-db11-4eb3-8ee5-dd79008e42f7/image.mp4)

<br/>

#### 5-1. Activity Indicator가 추가되었습니다.
이제부터 버튼에서 indicator가 보이도록 할 수 있습니다.
간단하게 `config.showsActivityIndicator` 값을 `true`로 설정해주면 됩니다.
필요한 경우에는 버튼의 이미지를 대체하기도 합니다.
해당 값은 `configurationUpdateHandler`에서 업데이트 해주면 좋을거 같습니다.

![](https://velog.velcdn.com/images/jxxnnee/post/7ffe59bf-97f2-4fc9-bcf7-7239c1416077/image.png)

<br/>

#### 5-2. Semantic Style
이제는`configuration`을 통해 버튼의 스타일을 세팅해두면 그에 맞는 상태에 따른 디자인이 자동으로 생성되어 적용이 됩니다.

![](https://velog.velcdn.com/images/jxxnnee/post/53bc3324-0402-413e-85b2-7a348a93d8c3/image.png)

<br/>

### 6. Pop-up Buttons
팝업 버튼은 기존에 iOS14 에서 업데이트 되었던 Pull-Down Button의 확장판입니다.
기존에는 여러 선택지를 동시에 선택할 수 있었다면 팝업 버튼은 한가지만 선택 가능합니다.
기존의 코드에서 `showsMenuAsPrimaryAction, changesSelectionAsPrimaryAction` 두 가지를 추가로 설정해주면 사용이 가능합니다.

```swift
let updateClosure: UIActionHandler = { [unowned self] action in
    self.updateTitle(action.title)
}

self.button.menu = UIMenu(children: [
    UIAction(title: "삭제", state: .on, handler: updateClosure),
    UIAction(title: "추가", handler: updateClosure)
])

self.button.showsMenuAsPrimaryAction = true
self.button.changesSelectionAsPrimaryAction = true


/// 코드를 통해 직접 현재 선택 된 메뉴를 변경 하는것도 가능하다.
(self.button.menu?.children[selectedColorIndex()] as? UIAction)?.state = .on

```

> ![](https://velog.velcdn.com/images/jxxnnee/post/ebf695da-f0ec-4da0-ad4a-ec67d4148062/image.mp4)
[이미지 링크](https://velog.velcdn.com/images/jxxnnee/post/ebf695da-f0ec-4da0-ad4a-ec67d4148062/image.mp4)

또한 메뉴아래에 메뉴를 추가하는것 역시 가능합니다.

```swift 
// Single selection menu

// The sort menu
let sortMenu = UIMenu(title: "Sort By", options: .singleSelection, children: [
    UIAction(title: "Title", handler: sortClosure),
    UIAction(title: "Date", handler: sortClosure),
    UIAction(title: "Size", handler: sortClosure)
])

// The top menu
let topMenu = UIMenu(children: [
    UIAction(title: "Refresh", handler: sortClosure),
    UIAction(title: "Account", handler: sortClosure),
    sortMenu
])

self.button.menu = topMenu
```


<br/>
<br/>

***

### Reference
https://zeddios.tistory.com/1291
https://hereismyblog.tistory.com/32
https://nyancoder.tistory.com/37

### Review
'contentEdgeInsets' was deprecated in iOS 15.0 라는 경고 문구를 보고 생긴 호기심에서 출발한 공부였지만 최근 업데이트 된 내용을 숙지하지 않고 있엇다는 점에서 반성해야 할거 같다...

업데이트 이후에는 반드시 WWDC를 공부하거나 어느정도 숙지하고 있는것이 앞으로 프로그래머로써 생명을 유지하는데에 큰 도움이 될테니까...
