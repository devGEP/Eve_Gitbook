---
description: 주어진 순열에서 누락된 요소를 찾습니다.
---

# PermMissingElem \[100%]

{% hint style="info" %}
N개의 서로 다른 정수로 구성된 배열 A가 제공됩니다. 배열에는 \[1..(N + 1)] 범위의 정수가 포함되어 있습니다. 이는 정확히 하나의 요소가 누락되었음을 의미합니다.

당신의 목표는 누락된 요소를 찾는 것입니다.

함수를 작성하세요:

> def solution(A)

배열 A가 주어지면 누락된 요소의 값을 반환합니다.

예를 들어, 다음과 같은 배열 A가 있다고 가정합니다.

A\[0] = 2 A\[1] = 3 A\[2] = 1 A\[3] = 5

함수는 누락된 요소이므로 4를 반환해야 합니다.

다음 가정에 대한 효율적인 알고리즘을 작성하십시오 .

> * N은 \[ 0 .. 100,000 ] 범위 내의 정수입니다 .
> * A의 요소는 모두 서로 다릅니다.
> * 배열 A의 각 요소는 \[1..(N + 1)] 범위 내의 정수입니다.



Codility Limited의 저작권 2009-2023. 판권 소유. 무단복제, 출판, 공개를 금지합니다.
{% endhint %}



<mark style="background-color:blue;">**접근 방법**</mark>

1. A 배열을 정렬하고, 사이 간격이 1이 아니면 결과값에 사이값을 넣었습니다.



<mark style="background-color:green;">**내가 작성한 코드**</mark>

```python
def solution(A):
    result = 0
    index = 1
    A.sort()

    while True:
        if A[index] - A[index-1] != 1:
            result = A[index-1] + 1
            break

        index += 1

    return result
```

<mark style="background-color:red;">점수: 50%</mark>

* 결과에는 사이값이 아닌 끝이 없을 수 있습니다. 또한, 배열이 비었거나 한두개일 때는 또 저 while문을 들어가도 out of index가 나오기 때문에 잘못된 코드임을 깨달았습니다.
* 그래서 코드를 새로 짰습니다. 아래 코드의 **시간복잡도는 O(N) or O(N \* log(N))**입니다.
  * A의 길이가 N일 때, 배열에는 1부터 N+1까지의 값이 있어야 하기 때문에 처음에 전체 수를 가지고 있는 배열을 선언해주었습니다.
  * 그리고 결과값은 전체 수를 가진 배열에서 A 배열을 빼서 **차집합**을 구하였습니다.
  * result는 set type이기 때문에 pop을 해서 빼주었습니다.



<mark style="background-color:orange;">**수정한 코드**</mark>

```python
def solution(A):
    full_list = list(range(1, len(A)+2))
    result = 0
    
    result = set(full_list) - set(A)

    return result.pop()
```
