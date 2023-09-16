---
description: 배열 A가 순열인지 확인합니다
---

# PermCheck \[100%]

{% hint style="info" %}
N개의 정수로 구성된 비어 있지 않은 배열 A가 제공됩니다.

순열 _은_ 1부터 N까지의 각 요소를 한 번만 포함하는 시퀀스입니다.

예를 들어 배열 A는 다음과 같습니다.

A\[0] = 4 A\[1] = 1 A\[2] = 3 A\[3] = 2

은 순열이지만 배열 A는 다음과 같습니다.

A\[0] = 4 A\[1] = 1 A\[2] = 3

값 2가 누락되었으므로 순열이 아닙니다.

목표는 배열 A가 순열인지 확인하는 것입니다.

함수를 작성하세요:

> def solution(A)

배열 A가 주어지면 배열 A가 순열이면 1을 반환하고 그렇지 않으면 0을 반환합니다.

예를 들어, 다음과 같은 배열 A가 있다고 가정합니다.

A\[0] = 4 A\[1] = 1 A\[2] = 3 A\[3] = 2

함수는 1을 반환해야 합니다.

주어진 배열 A는 다음과 같습니다:

A\[0] = 4 A\[1] = 1 A\[2] = 3

함수는 0을 반환해야 합니다.

다음 가정에 대한 효율적인 알고리즘을 작성하십시오 .

> * N은 \[ 1 .. 100,000 ] 범위 내의 정수입니다 .
> * 배열 A의 각 요소는 \[ 1 .. 1,000,000,000 ] 범위 내의 정수입니다 .



Codility Limited의 저작권 2009-2023. 판권 소유. 무단복제, 출판, 공개를 금지합니다.
{% endhint %}



<mark style="background-color:blue;">**접근 방법**</mark>

1. 처음에는 배열 A를 거꾸로 정렬해서 N이라는 숫자를 result\_num에 대입했습니다.
2. 그리고 A\_num을 position이라는 집합에 넣어서 중복되는 숫자를 제외하고, 배열의 길이가 result\_num과 같을때 1을 return 시켰습니다.



<mark style="background-color:green;">**내가 작성한 코드**</mark>

```python
def solution(A):
    position = set()

    A.sort(reverse = True)
    result_num = A[0]

    for A_num in A:
        position.add(A_num)

        if len(position) == result_num:
            return 1

    return 0
```

<mark style="background-color:red;">점수: 58%</mark>

* 배열이 \[1, 1]으로 주어졌을 때, 가장 큰 값이 1이고 중복된 값을 제거했을 때 길이가 1이기 때문에 return 값이 1이 나오게 되는 결과가 주어진다.
* 그래서 배열 A가 1이 아닐 경우에는 앞뒤로 차를 구했을 때, 1이면 return 값을 1로 주게 했습니다.
* 아래 코드의 **시간복잡도는 O(N) or O(N \* log(N))** 입니다.



<mark style="background-color:orange;">**수정한 코드**</mark>

```python
def solution(A):
    A.sort(reverse = True)
    result_num = A[0]
    
    if len(A) != 1:
        for i in range(1, result_num):
            if A[i-1] - A[i] == 1:
                if A[i] == 1:
                    return 1
                continue
            else:
                return 0
    else:
        if A[0] == 1:
            return 1
        return 0

    return 0
```

<mark style="background-color:red;">점수: 83%</mark>

* 배열에는 1부터 N까지의 요소가 있어야 하는데, 애초에 배열이 \[3, 2]로 주어지게 된다면 1이 없기 때문에 예외가 발생하게 된다.
* 그래서 아래 코드를 새롭게 짰습니다. A를 정렬하고 어차피 정렬했을 때 1부터 N까지 나열되어 있기 때문에 A\[i] 번째가 i+1 값과 동일해야 된다는 것을 깨달았습니다.
* 그래서 아래와 같이 코드를 짰고, **시간복잡도는 O(N) or O(N \* log(N))** 입니다.



<mark style="background-color:orange;">**수정한 코드**</mark>

```python
def solution(A):
    A.sort()

    for i in range(len(A)):
        if A[i] != i+1:
            return 0

    return 1
```
