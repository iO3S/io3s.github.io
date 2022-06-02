---
layout: post
title: "[iOS] Xcode 시뮬레이터 Error - 신이섭"
tags: [xcode , simulator]
comments: true
---

<br/>

### Write By
[신이섭 - Git](https://github.com/seob)

### Tool Info
Xcode 13.3<br/> 

***

<br/>

## Declaration
Xcode 에서  simulator 실행 시(또는 Device버전 14)시 Error 로 인해 실행이 안되는 경우를 볼 수 있다.
여러 가지 방법이 있겠지만 , 제가 테스트 해본결과 , 

1. Xcode > Project > Build Settings > User-Defined > VALID_ARCHS 항목을 $(ARCHS_STANDARD)로 변경해 준다 .
그래도 실행이 안될 경우 , 
VALID_ARCHS 항목을 삭제 후 clean 후 다시 빌드해보면 정상 동작하는 걸 확인 할 수있다.
<br/>
  

### Review
이번에는 간단한 거지만 한동인 xcode 업데이트 후 시뮬레이터를 실행하지 못해 애를 먹었다... ㅡㅡ 간단하지만 이렇게 기록를 
통해 다른분들도 시행착오 겪기전에 같이 공유하기 위해 적어봅니다.^^ 


