---
description: 정수의 이진 표현에서 가장 긴 0 시퀀스를 찾습니다.
---

# BinaryGap \[100%]

{% hint style="info" %}
_양의 정수 N 내의 이진 간격은_ N 의 이진 표현에서 양쪽 끝이 1로 둘러싸인 연속 0의 최대 시퀀스입니다.

예를 들어, 숫자 9는 이진 표현 1001 을 가지며 길이 2의 이진 간격을 포함합니다. 숫자 529는 이진 표현 1000010001 을 가지며 두 개의 이진 간격(길이 4와 길이 3 중 하나)을 포함합니다. 숫자 20은 이진 표현 10100을 가지며 다음을 포함합니다. 길이가 1인 하나의 이진 갭. 숫자 15는 이진 표현 1111을 가지며 이진 갭이 없습니다. 숫자 32는 이진 표현 100000을 가지며 이진 간격이 없습니다.

함수를 작성하세요:

> def solution(N)

이는 양의 정수 N이 주어지면 가장 긴 이진 간격의 길이를 반환합니다. N에 이진 간격이 포함되어 있지 않으면 함수는 0을 반환해야 합니다.

예를 들어, N = 1041인 경우 N에는 이진 표현 10000010001 이 있고 가장 긴 이진 간격의 길이는 5이기 때문에 함수는 5를 반환해야 합니다. N = 32인 경우 N에는 이진 표현 '100000'이 있으므로 함수는 0을 반환해야 합니다. 바이너리 갭이 없습니다.

다음 가정에 대한 효율적인 알고리즘을 작성하십시오 .

> * N은 \[ 1 .. 2,147,483,647 ] 범위 내의 정수입니다 .



Codility Limited의 저작권 2009-2023. 판권 소유. 무단복제, 출판, 공개를 금지합니다.
{% endhint %}



<mark style="background-color:blue;">**접근 방법**</mark>

1. 먼저 이진 형태이기 때문에 2의 제곱 형태를 이용했습니다.
2. 주어진 N과 2의 제곱 형태를 비교하여 사이에 들어갈 때는 해당하는 2의 제곱으로 나누었습니다.
3. 그리고 n을 다시 0으로 만들어서 while문을 반복했습니다.
4. 이때 만약 사이에 들어가지 않을 때는, n을 1씩 증가시켜서 해당될 때까지 반복했습니다.



<mark style="background-color:green;">**내가 작성한 코드**</mark>

```python
def solution(N):
    while True:
        if 2**n <= N and N < 2**(n+1):
            N %= 2**n
            index.append(n)
            n = 0
            continue
        elif N == 0:
            break
    
        n += 1
    
    for index_num in range(1, len(index)):
        if (len(index) == 1):
            result = index[index_num]
            break
        
        if (index_num == 1):
            result = index[index_num - 1] - index[index_num] - 1
            continue
    
        result = max(result, index[index_num - 1] - index[index_num] - 1)
    
    return result
```
