---
layout: post
title: "[Algorithm] Sorting Algorithm - Bubble, Insertion, Selection"
tags: [algorithm]
comments: true
---


# # Write By jhjo

[Bubble-거품정렬](https://jhjo-tech.tistory.com/4)

[Selection-선택정렬](https://jhjo-tech.tistory.com/5)

[Insertion-삽입정렬](https://jhjo-tech.tistory.com/6)

# Sort!

- Bubble Sort
    - 정렬 과정에서 원소의 이동이 거품이 수면위로 올라가는 모습을 보이기 때문에 지어진 이름입니다.
    - 서로 인접한 두 원소를 비교하여 주어진 조건에 맞으면 자리를 교환하여 정렬하는 알고리즘입니다.
- Selection Sort
    - 정렬할 위치를 먼저 선택한 뒤 그 자리에 오는 값을 찾고 자리에 올 값이 있다면 교환하는 방식의 알고리즘입니다.
- Insertion Sort
    - 2번째 아이템부터 시작하여 앞에서부터 차례대로 정렬된 부분과 비교하여 자신의 위치를 찾아 삽입하여 정렬하는 알고리즘입니다.

# Process

1에서 6까지 랜덤으로 들어 있는 배열이 있다고 가정합니다.

Ascending으로 정렬하려고 합니다.

## Bubble

1. 첫 번째 아이템과 두 번째 아이템을 비교합니다.
2. 두 번재 아이템이 작으면 교환 크면 교환하지 않습니다.
3. 두 번째 아이템과 세 번째 아이템을 비교합니다.
4. 작으면 교환 크면 교환하지 않습니다.
5. 위 과정을 마지막 인덱스까지 반복합니다.

여기까지 1사이클입니다.

1사이클이 종료되면 마지막 인덱스에는 가장 큰 값이 정렬되게 됩니다.

2사이클 부터는 마지막 인덱스는 제외(정렬된 부분은 제외)하고 반복합니다.

## Selection

오름차순이기 때문에 0번인덱스부터 최소값을 넣어야 합니다.

1. 0번 인덱스에 넣을 최소값의 인덱스를 찾습니다.
2. 0번 인덱스의 아이템이 최소 값이 아니라면 0번 인덱스의 원소와 최소값을 교환합니다.

여기까지 1사이클입니다.

이제 0번 인덱스는 정렬이 되었기 때문에 1번 인덱스에 들어갈 다음 최소 값을 찾습니다.

정렬이 완료 될 때까지 위 사이클을 반복합니다.

## Insertion

1. 두 번째 아이템과 첫 번째 아이템을 비교합니다.
2. 첫 번째 아이템이 크면 교환합니다. (작으면 교환하지 않습니다)

1 사이클 종료

1. 세번째 아이템과 두 번째 아이템을 비교합니다.
2. 두 번째 아이템이 크면 교환, 크지 않다면 다음 사이클로 넘어 갑니다.
3. 두 번째 아이템이 교환 되었으면 첫 번째 아이템과 비교합니다. 크면 교환 크지 않으면 다음 사이클로 넘어 갑니다.

2 사이클 종료

다음 사이클 역시 비교 후 비교 대상 값보다 크지 않은 값이 나오면 비교하는 작업을 하지 않고 다음 사이클로 넘어 갑니다.

마지막 아이템까지 작업을 완료 합니다.

# Code

```cpp
void bubbleSort(int arr[], int length)
{
    int temp = 0;
    for (int i=0; i < length; i++) // 정렬이 완료된 원소의 수
    {
        for (int j=1; j < length - i; j++) // 비교 원소
        {
            if (arr[j] < arr[j - 1])
            {
                temp = arr[j];
                arr[j] = arr[j - 1];
                arr[j - 1] = temp;
            }
        }
    }
}

void selectionSort(int arr[], int length)
{
    int minIndex = 0;
    int temp = 0;
    for (int i=0; i < length; i++) { // 정렬하고자 하는 위치
        minIndex = i;

        for (int j=i; j < length; j++) // 정렬 위치 다음값 부터 비교
        {
            if (arr[j] < arr[minIndex]) // 최소 값 인덱스 찾기
            {
                minIndex = j;
            }
        }
        
        if (i != minIndex) // Swap
        {
            temp = arr[minIndex];
            arr[minIndex] = arr[i];
            arr[i] = temp;
        }
    }
}

void insertionSort(int arr[], int length)
{
 
    int target = 0; // 비교 대상 값
    int minIndex = 0; // 비교 대상 값이 삽입 될 위치
    for (int i=1; i < length; i++) // 두 번째 원소부터 비교
    {
        target = arr[i];
        minIndex = i;
        
        for (int j=i-1; j >=0 && arr[j] > target ; j--) // 비교 대상부터 낮은 인덱스로 비교
        {
            arr[j + 1] = arr[j];
            minIndex = j;
        }
        
        arr[minIndex] = target;
    }
}

```

# Time Complexity

Bubble, Insertion, Selection Sort의 시간 복잡도는 중첩된 반복문을 돌기 때문에 O(n^2)입니다.

그러나 평균적인 상황에서는 Insertion Sort는 O(n)이 발생합니다.

다른 Sort는 정렬 여부와 관계없이 중첩된 반복문을 돌면서 확인을 해야 하는 반면에 Insertion sort는 정렬이 되어 있다고 판단 되면 반복문을 더 돌지 않고 다음 사이클로 넘어가기 때문입니다.
