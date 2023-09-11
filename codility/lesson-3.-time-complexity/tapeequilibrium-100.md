---
description: '| (A[0] + ... + A[P-1]) - (A[P] + ... + A[N-1]) | 값을 최소화합니다.'
---

# TapeEquilibrium \[100%]

{% hint style="info" %}
N개의 정수로 구성된 비어 있지 않은 배열 A가 제공됩니다. 배열 A는 테이프의 숫자를 나타냅니다.

0 < P < N과 같은 정수 P는 이 테이프를 비어 있지 않은 두 부분, 즉 A\[0], A\[1], ..., A\[P − 1] 및 A\[P], A\[로 나눕니다. P + 1], ..., A\[N − 1].

두 부분의 차이 _는_ 다음 값입니다. |(A\[0] + A\[1] + ... + A\[P − 1]) − (A\[P] + A\[P + 1] + .. .+ A\[N − 1])|

즉, 첫 번째 부분의 합과 두 번째 부분의 합 사이의 절대적인 차이입니다.

예를 들어, 다음과 같은 배열 A를 생각해 보세요.

A\[0] = 3 A\[1] = 1 A\[2] = 2 A\[3] = 4 A\[4] = 3

이 테이프를 네 위치로 분할할 수 있습니다.

> * P = 1, 차이 = |3 − 10| = 7\
>
> * P = 2, 차이 = |4 − 9| = 5\
>
> * P = 3, 차이 = |6 − 7| = 1\
>
> * P = 4, 차이 = |10 − 3| = 7\
>

함수를 작성하세요:

> def solution(A)

이는 N개의 정수로 구성된 비어 있지 않은 배열 A가 주어지면 달성할 수 있는 최소 차이를 반환합니다.

예를 들어, 다음과 같습니다.

A\[0] = 3 A\[1] = 1 A\[2] = 2 A\[3] = 4 A\[4] = 3

위에서 설명한 대로 함수는 1을 반환해야 합니다.

다음 가정에 대한 효율적인 알고리즘을 작성하십시오 .

> * N은 \[ 2 .. 100,000 ] 범위 내의 정수입니다 .
> * 배열 A의 각 요소는 \[ −1,000 .. 1,000 ] 범위 내의 정수입니다 .



Codility Limited의 저작권 2009-2023. 판권 소유. 무단복제, 출판, 공개를 금지합니다.
{% endhint %}



<mark style="background-color:blue;">**접근 방법**</mark>

1. P의 최소값을 구해야 되기 때문에, P의 수만큼 for문을 적었습니다.
2. 그리고 P를 기준으로 앞, 뒤에 얼마나 많은 수가 있는지 알 수 없기 때문에, f\_part, b\_part 초기화를 해주었습니다.
3. 그리고 각각 구한다음에, P가 1일때는 비교대상이 없기 때문에 abs()라는 절댓값 함수를 이용하여 값을 구해서 min\_num에 넣어주고, 2부터는 비교해서 최소값을 넣었습니다.



<mark style="background-color:green;">**내가 작성한 코드**</mark>

```python
def solution(A):
    for P in range(1, len(A)):
        f_part = 0
        b_part = 0
        for f in range(0, P):
            f_part += A[f]
        for b in range(P, len(A)):
            b_part += A[b]

        if P == 1:
            min_num = abs(f_part - b_part)
            continue
        min_num = min(min_num, abs(f_part - b_part))

    return min_num
```

<mark style="background-color:red;">점수: 53%</mark>

*   정확성이나 이런 문제는 없지만, **시간복잡도가 O(N^N)**으로 성능 테스트에서 <mark style="color:red;">**TIMEOUT ERROR**</mark>가 났습니다.



    <figure><img src="../../.gitbook/assets/스크린샷 2023-09-12 오전 4.01.35.png" alt="" width="326"><figcaption></figcaption></figure>
* 코드를 어떻게 해야 시간을 줄일 수 있을까 찾아보다가 for문을 굳이 돌릴 필요가 없다는 것을 깨달았습니다.
* i는 어차피 1씩 증가하게 되고, 이를 left\_sum에 더한만큼이 left\_sum이 되고, right\_sum은 total에서 뺀 값이 되기 때문입니다.&#x20;
* 그리고 if문도 간결히 해도 괜찮을 거같다는 생각이 들어서 수정하게  되었습니다.
* 아래 코드의 **시간 복잡도는 O(N)**입니다.



<mark style="background-color:orange;">**수정한 코드**</mark>

```python
def solution(A):
    total_sum = sum(A)
    left_sum = 0
    min_difference = float('inf')
    
    for i in range(len(A) - 1):  # len(A) - 1까지만 진행하는 이유는 마지막 요소는 오른쪽 부분에 포함되므로 제외
        left_sum += A[i]
        right_sum = total_sum - left_sum
        difference = abs(left_sum - right_sum)
        
        if difference < min_difference:
            min_difference = difference
    
    return min_difference
```
