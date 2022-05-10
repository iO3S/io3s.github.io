```
---
layout: post
title: "Array reserveCapacity - 한상민"
description: "Swift의 reserveCapacity를 통해 성능향상을 노려보자"
---

## At a Glance

array를 mutable하게 사용하고 요소를 추가할 때, array엔 필요한 capacity가 미리 할당하고 추가하는 방식을 사용합니다.
Swift에선 배열의 요소를 추가하기 위한 배열 용량 할당에 exponential growth startegy을 사용하여 배열의 메모리를 할당합니다.

## Exponential growth strategy
Swift에서 사용하는 배열의 추가 할당 전략으로써, 기존 예약된 사이즈를 넘겼을 때 이를 지수곱으로 할당하여 요소 추가시의 작업을 평균화 하고 요소 추가 할당 시간을 일정 시간에 발생할 수 있게 만들어 놓았습니다.
이 전략으로 인하여 array에 요소를 추가할때 일정 하지만 불필요한 시간이 소요됩니다.

## Reserve Capacity
Exponential growth strategy 할당 전략으로 인하여 발생하는 소요시간은 할당 횟수 자체를 축소시키는 방법으로 성능향상을 기대해 볼 수 있습니다.
방식은 다음과 같습니다.
먼저 배열에 할당이 될 minimum capacity를 먼저 예약 하여 배열에 공간을 만듭니다. 새로운 요소를 추가로 할당할 때 이미 만들어진 영역에 요소를 추가 할당합니다. 이미 예약된 공간만큼 추가를 하기 때문에 array는 메모리 할당에 대한 횟수가 예측가능한 수치가 됩니다. 이에 따라 추가할당 횟수를 줄이고 추가 할당 되는 시간을 축소 시켜 성능 향상을 도모하는 방식입니다.

Swift array는 자체적으로 reserveCapacity 메소드를 제공합니다. 파라미터로 넘기는 minimum capacity를 통해 메모리 할당 선작업을 하고 배열에 이 후 할당한 메모리에 새로운 요소를 추가할 수 있게 해줍니다.

## Code it

간단한 코드를 통해서 성능을 확인 해보고 capacity가 reserve된 것과 그렇지 않은 기본 배열 변수를 선언하여 process time을 비교 해보겠습니다.

~~~ swift
var reserveIntArray = Array<Int>()
reserveIntArray.reserveCapacity(500)
reserveIntArray.capacity

var nonReserveIntArray = [Int]()
nonReserveIntArray.capacity

processTime {
    for i in 0...500 {
        reserveIntArray.append(i)
    }
    reserveIntArray.capacity
}
processTime {
    for j in 0...500 {
        nonReserveIntArray.append(j)
    }
    nonReserveIntArray.capacity
}
~~~

결과 값은 아래와 같습니다.
~~~
processTime: 0.620948076248169
processTime: 0.7575379610061646
~~~


기존 할당한 capacity내에서의 배열의 추가 성능은 0.13sec 차이가 나왔습니다. 생각보다 많은 차이가 나서 놀라긴 했는데.. 몇번 더 테스트를 해보면 그 차이는 일정하게 나타나진 않는 것으로 확인됩니다.
하지만 capacity를 reserve한 배열이 그렇지 않은 배열보다 배열의 추가성능이 빠르다는 것은 확실하게 확인되었습니다.


## One More
그럼 예약 된 capacity 보다 초과된 수치의 요소 갯수를 추가했을 경우 어떤 결과가 나올지에 대한 궁금증이 생겨 한번 더 코드를 작성해보았습니다.

~~~ swift
var reserveIntArray = Array<Int>()
reserveIntArray.reserveCapacity(500)
reserveIntArray.capacity

var nonReserveIntArray = [Int]()
nonReserveIntArray.capacity

processTime {
    for i in 0…1000 {
        reserveIntArray.append(i)
    }
    reserveIntArray.capacity
}
processTime {
    for j in 0…1000{
        nonReserveIntArray.append(j)
    }
    nonReserveIntArray.capacity
}
~~~
결과 값은 아래와 같습니다.

~~~
processTime: 1.640960931777954
processTime: 1.3353140354156494
~~~


예약된 배열의 capacity에서 2배 수치의 요소를 삽입하였을 때 기존 capacity를 예약하지 않은 배열보다 요소 추가작업에 있어 월등히 느린것이 확인 되었습니다.

혹시나 하여 capacity를 확인해보니, 2배의 수치가 일어났을 때 지수 증가 법칙으로 인해 500의 2배인 1000이 할당되었고,
용량 예약이 되지 않은 array는 1500이상이 할당 되었습니다.
추가 배열의 공간이 추가되는 시점 이전의 capacity가 각각 508, 768 이였기 때문에 할당 횟수에 대한 차이는 크게 없는 듯 합니다.
원인에 대해선 좀 더 확인해봐야겠지만 capacity를 통해 성능 향상을 기대하기 위해선 예약 수치에 대한 규정을 명확하게 하여 사용해야 할 것으로 보입니다.

## 결론
기존에 페이징이 필요한 리스트 화면을 표시할 경우 리스트를 관리 하는 배열에 요소를 추가하여 데이터를 표시하는 작업을 진행하였습니다.
자연스레 추가 할당에 대한 시간을 단축 시킬 수 있다면 이에 대한 성능 향상을 기대해 볼 수 있을 것으로 보입니다.
리스트에 나타나는 최대 갯수, 페이징의 단위를 명확하게 규정할 수 있는 경우 리스트를 구성함에 있어 어느정도의 퍼포먼스 향상을 기대해 볼 수 있을 것으로 보입니다.
```
