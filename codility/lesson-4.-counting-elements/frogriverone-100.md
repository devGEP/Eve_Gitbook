---
description: 개구리가 강 건너편으로 점프할 수 있는 가장 빠른 시간을 찾아보세요.
---

# FrogRiverOne \[100%]

{% hint style="info" %}
작은 개구리가 강 건너편으로 가고 싶어 합니다. 개구리는 처음에 강의 한쪽 둑(위치 0)에 있었고 반대쪽 둑(위치 X+1)으로 가고 싶어합니다. 나뭇잎이 나무에서 강 표면으로 떨어집니다.

떨어지는 나뭇잎을 나타내는 N개의 정수로 구성된 배열 A가 제공됩니다. A\[K]는 K 시간에 나뭇잎 하나가 떨어지는 위치를 초 단위로 나타냅니다.

목표는 개구리가 강 반대편으로 점프할 수 있는 가장 빠른 시간을 찾는 것입니다. 개구리는 강 건너편 1에서 X까지 모든 위치에 나뭇잎이 나타날 때만 건널 수 있습니다. 즉, 우리는 1에서 X까지의 모든 위치가 나뭇잎으로 덮이는 가장 빠른 순간을 찾고 싶습니다. 강의 흐름 속도는 무시할 수 있을 정도로 작다고 가정할 수 있습니다. 즉, 나뭇잎이 강에 떨어지면 위치가 바뀌지 않습니다.

예를 들어, 정수 X = 5와 배열 A가 주어지면 다음과 같습니다.

A\[0] = 1 A\[1] = 3 A\[2] = 1 A\[3] = 4 A\[4] = 2 A\[5] = 3 A\[6] = 5 A\[7] = 4

두 번째 6에서는 나뭇잎이 위치 5로 떨어집니다. 이것은 강 건너편의 모든 위치에 나뭇잎이 나타나는 가장 빠른 시간입니다.

함수를 작성하세요:

> def solution(X, A)

N개의 정수와 정수 X로 구성된 비어 있지 않은 배열 A가 주어지면 개구리가 강 반대편으로 점프할 수 있는 가장 빠른 시간을 반환합니다.

개구리가 강 반대편으로 점프할 수 없는 경우 함수는 -1을 반환해야 합니다.

예를 들어, X = 5이고 배열 A가 다음과 같이 주어진 경우:

A\[0] = 1 A\[1] = 3 A\[2] = 1 A\[3] = 4 A\[4] = 2 A\[5] = 3 A\[6] = 5 A\[7] = 4

위에서 설명한 대로 함수는 6을 반환해야 합니다.

다음 가정에 대한 효율적인 알고리즘을 작성하십시오 .

> * N과 X는 \[ 1 .. 100,000 ] 범위 내의 정수입니다 .
> * 배열 A의 각 요소는 \[ 1 .. X ] 범위 내의 정수입니다 .



Codility Limited의 저작권 2009-2023. 판권 소유. 무단복제, 출판, 공개를 금지합니다.
{% endhint %}



<mark style="background-color:blue;">**접근 방법**</mark>

1. 처음에는 X값과 A 배열의 어떠한 숫자와 일치했을 때 도착하는 것으로 지문을 이해했다.
2. 그래서 A 배열을 for문으로 돌리고, if문으로 X와 일치할 경우 result에 넣어서 return했다.



<mark style="background-color:green;">**내가 작성한 코드**</mark>

<pre class="language-python"><code class="lang-python"><strong>def solution(X, A):
</strong>    result = 0
    for i in A:
        if X == i:
            result = A.index(i)

    return result
</code></pre>

<mark style="background-color:red;">점수: 0%</mark>

* 이 경우, X가 어떠한 숫자와도 일치하지 않기 때문에 -1을 return해야 한다는 예외 케이스가 없어서 아래 코드로 수정했다.



<mark style="background-color:orange;">**수정한 코드**</mark>

```python
def solution(X, A):
    result = 0
    for i in A:
        if X == i:
            result = A.index(i)
            break
        result = -1

    return result
```

<mark style="background-color:red;">점수: 18%</mark>

* 이 때는 X와 일치하는 값이 2개 이상일 때는 -1을 return해야 한다는 예외 케이스가 없어서 아래 코드로 수정했다.



<mark style="background-color:orange;">**수정한 코드**</mark>

```python
def solution(X, A):
    result = 0
    for i in A:
        if X == i:
            if X == A[result]:
                result = -1
                continue
            result = A.index(i)
        else:
            if X == A[result]:
                continue
            result = -1

    return result
```

<mark style="background-color:red;">점수: 9%</mark>

* X가 A\[result]랑 같은 경우 return 값을 -1으로 했는데, result를 0으로 초기화했기 때문에 A\[0]이 X랑 같은 경우에도 -1을 리턴하는 오류가 발생했다.
* 그래서 아래와 같이 for문을 A가 아닌 len(A)로 돌렸고, i가 0이 아닌 경우에만 -1을 리턴했다.



<mark style="background-color:orange;">**수정한 코드**</mark>

```python
def solution(X, A):
    result = 0
    for i in range(len(A)):
        if X == A[i]:
            if i != 0 and X == A[result]:
                result = -1
                continue
            result = i
        else:
            if A[i] != 0 and X == A[result]:
                continue
            result = -1

    return result
```

<mark style="background-color:red;">점수: 18%</mark>

* 이 때, 분석의 예시에서 (5, \[1, 2, 3, 5, 3, 1]) 일 때, return이 3이 아니고, -1이 나와야 한다는 것입니다.
* 이 예시가 이해가 되지 않아서 지문을 다시 분석했다. 그랬더니 **'우리는 1에서 X까지의 모든 위치가 나뭇잎으로 덮이는 가장 빠른 순간'** 이 지문이 눈에 들어왔습니다.&#x20;
* 그래서 코드를 전면 수정해서 position을 set()으로 초기화하고, for문을 돌려서 position에 넣어가면서 **position의 길이와 X가 같아질때** i를 리턴하고, 나머지 예외는 -1을 리턴하게 했다.
* 생각한 예외는 너무 많았고, 계속해서 예상치 못한 에러가 나왔습니다. 이 모든 예외를 처리하는 것은 복잡했기 때문에 아래와 같은 방법을 선택했다.
* 아래 코드의 **시간 복잡도는 O(N)**입니다.



<mark style="background-color:orange;">**수정한 코드**</mark>

```python
def solution(X, A):
    position = set()

    for i in range(len(A)):
        position.add(A[i])

        if len(position) == X:
            return i

    return -1
```

**점수: 100%**
