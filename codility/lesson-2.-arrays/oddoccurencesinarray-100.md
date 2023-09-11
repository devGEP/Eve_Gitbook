---
description: 홀수 개의 요소에서 발생하는 값을 찾습니다.
---

# OddOccurencesInArray \[100%]

{% hint style="info" %}
N개의 정수로 구성된 비어 있지 않은 배열 A가 제공됩니다. 배열에는 홀수 개의 요소가 포함되어 있으며, 배열의 각 요소는 짝을 이루지 않은 채로 남아 있는 한 요소를 제외하고 동일한 값을 가진 다른 요소와 쌍을 이룰 수 있습니다.

예를 들어 배열 A에서는 다음과 같습니다.

A\[0] = 9 A\[1] = 3 A\[2] = 9 A\[3] = 3 A\[4] = 9 A\[5] = 7 A\[6] = 9

> * 인덱스 0과 2에 있는 요소의 값은 9입니다.
> * 인덱스 1과 3의 요소는 값 3을 가지며,
> * 인덱스 4와 6에 있는 요소의 값은 9입니다.
> * 인덱스 5의 요소는 값 7을 가지며 쌍을 이루지 않습니다.

함수를 작성하세요:

> def solution(A)

위의 조건을 충족하는 N개의 정수로 구성된 배열 A가 주어지면 짝이 없는 요소의 값을 반환합니다.

예를 들어, 다음과 같은 배열 A가 있다고 가정합니다.

A\[0] = 9 A\[1] = 3 A\[2] = 9 A\[3] = 3 A\[4] = 9 A\[5] = 7 A\[6] = 9

위의 예에서 설명한 대로 함수는 7을 반환해야 합니다.

다음 가정에 대한 효율적인 알고리즘을 작성하십시오 .

> * N은 \[1..1,000,000] 범위 내의 홀수 정수입니다.
> * 배열 A의 각 요소는 \[ 1 .. 1,000,000,000 ] 범위 내의 정수입니다 .
> * A의 값 중 하나를 제외한 모든 값이 짝수 번 발생합니다.



Codility Limited의 저작권 2009-2023. 판권 소유. 무단복제, 출판, 공개를 금지합니다.
{% endhint %}



<mark style="background-color:blue;">**접근 방법**</mark>

1. cuple이라는 배열은 짝지어진 수의 index를 추가하기 위한 배열입니다.
2. for문을 돌려서 i가 cuple에 포함된 index이면, 아래 과정을 생략했습니다.
3. j는 i번째+1부터 len(A)까지의 수인데, i와 j를 비교해서 수가 같으면 i번째와 j번째 index를 배열 cuple에 추가했습니다.
4. 그렇게 2, 3번 과정을 반복하고 나서, for문을 만들어서 cuple안에 든 index를 제외하고 나니까 짝을 못 이룬 index를 발견할 수 있었습니다.
5. 그래서 그 index의 숫자를 return했습니다.



<mark style="background-color:green;">**내가 작성한 코드**</mark>

```python
def solution(A):
    cuple = []
    
    for i in range(len(A)-1):
        if i in cuple:
            continue
    
        for j in range(i + 1, len(A)):
            if A[i] == A[j]:
                cuple.append(i)
                cuple.append(j)
                break
    
    for i in range(len(A)):
        if i in cuple:
            continue
        result = i
    
    return A[result]
```

<mark style="background-color:red;">점수: 55%</mark>

* <mark style="background-color:yellow;">시간 복잡도가 이중 반복문을 써서</mark> <mark style="background-color:yellow;"></mark><mark style="background-color:yellow;">**O(N^2)**</mark>이 나왔다.
*   그래서 성능 테스트 부분에서 N이 천의 자리 수, 십만 등 수가 클때, 성능 <mark style="color:red;">**TIMEOUT ERROR**</mark>가 나왔다.

    <figure><img src="../../.gitbook/assets/스크린샷 2023-09-12 오전 1.36.24.png" alt="" width="326"><figcaption></figcaption></figure>



<mark style="background-color:orange;">**해결**</mark>

도저히 시간을 줄일 방안이 안 보여서 찾아보았더니 **XOR 연산**이라는게 존재했습니다. 그래서 이를 적용하여 코드를 작성하였더니 코드가 훨씬 줄고 깔끔해졌습니다. 그리고 <mark style="background-color:yellow;">**시간복잡도**</mark><mark style="background-color:yellow;">는</mark> <mark style="background-color:yellow;"></mark><mark style="background-color:yellow;">**O(N) or O(N\*log(N))**</mark>으로 나왔습니다.

```python
def solution(A):
    result = 0
    for num in A:
        result ^= num
    
    return result
```

<figure><img src="../../.gitbook/assets/스크린샷 2023-09-12 오전 1.59.51.png" alt="" width="326"><figcaption><p>확실히 시간이 줄어든 것을 알 수 있다.</p></figcaption></figure>
