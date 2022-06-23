---
layout: post
title: "Share sheet 쓰지않고 Album에 UIImage 저장하는 방법 - 신이섭"
tags: [UIIage , UIImageWriteToSavedPhotosAlbum ]
comments: true
---


<br>
<br/>

[jhjo-tech : Share sheet 쓰지않고 Album에 UIImage 저장하는 방법
<br>
<br>
<br/>
이번 개발중에 이미지를 다운받아 앨범에 저장하는 기능이 추가 되었다. 여러방법들이 있겠지만 , 고객이 요청한 내용은 
다운로드 클릭 시 선택한 여러 이미지를 앨범에 저장해달라는 요청사항이였다.
그래서 찾아보니 

공식 문서 [링크] 

[링크]: https://developer.apple.com/documentation/uikit/1619125-uiimagewritetosavedphotosalbum

내가 사용한 소스는 아래와 같다.
<br>
<br/>

 
``` 
for (i,_) in imageArr.enumerated() {
    if let url = URL(string: "\(imageArr[i])"),
       let data = try? Data(contentsOf: url),
       let image = UIImage(data: data) {
        UIImageWriteToSavedPhotosAlbum(image, self, #selector(imageDownload(_:didFinishSavingWithError:contextInfo:)), nil)
    }
}
```

```
func UIImageWriteToSavedPhotosAlbum(_ image: UIImage, _ completionTarget: Any?, _ completionSelector: Selector?, _ contextInfo: UnsafeMutableRawPointer?)

```
Parameters

1.image	 - The image to write to the Camera Roll album.  
2.completionTarget	- Optionally, the object whose selector should be called after the image has been written to the Camera Roll album.  
3.completionSelector	- The method selector of the completionTarget object to call. This optional method should conform to the following signature  
4.contextInfo	- An optional pointer to any context-specific data that you want passed to the completion selector.  
 
<br>
<br/>
