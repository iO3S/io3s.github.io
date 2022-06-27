---
layout: post
title: "[Swift] ReactorKit 사용기 (1) - 민경준"
tags: [Swift, ReactorKit, RxSwfit]
comments: true
---

<br/>

### Write By
[민경준 - velog](https://velog.io/@jxxnnee)


# Overview
안녕하세요 이번 글에서는 제가 프로젝트에 직접 사용중인 `ReactorKit`에 관하여 왜 사용하게 되었는지? `ReactorKit`이 무엇인지? 어떻게 사용하는지? 를 담은 글을 공유해보겠습니다.

그리고 해당 글은 작년에 Notion에 적어두었던 글을 Velog로 옮겨두는 글이기 때문에 조금 미숙한 부분이 있을 수 있습니다. 잘못되었다고 생각이 드시는 부분은 지적해주시면 감사하겠습니다 😂


<br/>

# 1. Why ReactorKit?

우선, MVVM 패턴과 RxSwift를 이용한 개발 과정에서 global state를 관리하는것에 한계를 느꼈다.
기본적으로 **ViewModel은 View와 Model 사이에서 binding을 해주는 역할**로 ViewController와 1:1로 강하게 연결이 되어있다보니 **View 사이의 소통을 MVVM 패턴으로 관리하는것이 용이하지 않았다.**

그리하여 우선 찾아본것은 다른 디자인 패턴을 사용하여 리팩토링하는것을 찾아보았다.
VIPER, RIBs 두개 모두 관련된 자료를 찾아 읽어봤지만 이해하기 쉽지 않았고 현재 개발된 코드에 바로 적용시키기에 용이하지 않았다. 

마지막으로 전수열(갓갓..)님이 만드신 ReactorKit을 찾게 되었고 **RxSwift를 응용하여 설계**된 덕분에 사용방법을 익히기가 어렵지 않았고 무엇보다 **global state를 관리할수 있도록 설계**되어있는 점이 무척 맘에 들었다. 또, 시간을 크게 소요하지 않고 현재 개발된 코드에 적용시키기에 좋을거같아 ReactorKit을 선택하게 되었다.

<br/>

# 2. What is ReactorKit?

## 2-1. 기본 컨셉

1. ReactorKit은 단방향 그리고 반응형 Swift 아키텍쳐를 위한 프레임워크이다. 
2. FaceBook에서 개발한 [Flux](https://haruair.github.io/flux/docs/overview.html)와 [Reactive programming](https://en.wikipedia.org/wiki/Reactive_programming)의 조합이다.
3. View는 Action만 보낼 수 있고, Reactor는 State만 방출 할 수있다.

![](https://velog.velcdn.com/images/jxxnnee/post/a941c04d-8b91-4d05-b076-9189f5bce71b/image.png)


## 2-2. 디자인 목표


1. **테스트 가능성:** ReactorKit의 첫번째 목적은 뷰에서 비즈니스 로직을 분리시키는것이다. Reactor는 View에 대한 종속성이 없기에 Reactor와 View Binding만 테스트 할 수 있다. 
2. **부분 사용 가능성**: ReactorKit은 어플리케이션이 하나의 아키텍쳐를 따라가도록 요구하지 않는다. 특정 View에 부분적으로 채택하여 사용이 가능하다.
3. **적은 코드 사용성**: ReactorKit은 단순한 작업을 위해 복잡한 코드를 작성하는것을 지양하는데 중점을 둔다.



<br/>
<br/>

사용방법에 관한글은 다음 글에... 내용이 생각보다 길어져서 나눠서 적도록 하겠습니다 ㅎㅎ...

<br/>

---

### Reference

- Github: [https://github.com/ReactorKit/ReactorKit/](https://github.com/ReactorKit/ReactorKit/)
- ReactorKit 예제 따라하기: [https://eunjin3786.tistory.com/100](https://eunjin3786.tistory.com/100)
- ReactorKit의 Transform 함수에 대해 알아보자: [https://eunjin3786.tistory.com/147](https://eunjin3786.tistory.com/147)
- 리액터 킷을 사용한 리팩토링: [https://iospanda.tistory.com/entry/ReactorKit-리액터킷을-사용한-리팩토링-진행-중](https://iospanda.tistory.com/entry/ReactorKit-%EB%A6%AC%EC%95%A1%ED%84%B0%ED%82%B7%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%9C-%EB%A6%AC%ED%8C%A9%ED%86%A0%EB%A7%81-%EC%A7%84%ED%96%89-%EC%A4%91)
