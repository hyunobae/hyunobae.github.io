---
title: 소수만들기 (Python)
author: hyunobae
categories: [algorithm]
tags: [algorithm, 프로그래머스, combinations]
published: true
---


### 문제 설명
주어진 숫자 중 3개의 수를 더했을 때 소수가 되는 경우의 개수를 구하려고 합니다. 숫자들이 들어있는 배열 nums가 매개변수로 주어질 때, nums에 있는 숫자들 중 서로 다른 3개를 골라 더했을 때 소수가 되는 경우의 개수를 return 하도록 solution 함수를 완성해주세요.
### 제한 사항
- nums에 들어있는 숫자의 개수는 3개 이상 50개 이하입니다.
- nums의 각 원소는 1 이상 1,000 이하의 자연수이며, 중복된 숫자가 들어있지 않습니다.

### 풀이 
itertools의 combinations를 알고 있는가가 중요한 문제인 것 같다. combinations로 가능한 조합을 만들고 더하고 소수인지 확인하는 loop면 간단하게 해결 가능하다.

### 코드
```python
from itertools import combinations

def solution(nums):
    comb = list(combinations(nums, 3))
    
    answer = 0
    
    for i in range(len(comb)):
        sum = 0
        cnt=0
        sum = comb[i][0]+comb[i][1]+comb[i][2]
        
        for j in range(2, sum):
            if sum % j == 0:
                cnt += 1
                break
        if cnt==0:
            answer+= 1

    return answer
```

