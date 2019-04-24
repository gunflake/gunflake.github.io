---
layout: post
title: Best Sightseeing Pair
---

# Leetcode Algorithm

## Contest 129 - Best Sightseeing Pair (1014)

### 1. 문제 

int type형 배열에서 2개의 배열 원소를 선택해서 관광스팟의 기대값이 가장 큰 것을 구하는 문제이다.
Sightseeing spot : `A[i] + A[j] + i - j`

### 2. 문제 접근

이 문제를 O(n)의 시간복잡도로 푸는 방식과 O(n^2) 방식이지만 압축해서 푸는 방식이 있다.

필자는 인풋 파라미터 제한을 보고 압축을 통해 O(n^2)방식으로 문제를 해결했다.

### 3. 압축 방법

>>
Note:
2 <= A.length <= 50000
1 <= A[i] <= 1000

문제의 입력값을 보면 다음과 같이 배열의 길이는 50000이지만, 각 배열의 최대값은 1000을 넘지 않는다.
따라서, `i`와 `j`위치가 1000이상일 경우 
A[j] - j 값이 <= 0 이하의 값이 나오므로 두 위치의 거리가 1000이상이 초과하면 더 이상 탐색할 필요가 없게된다.

### 4. 코드

```
public int maxScoreSightseeingPair(int[] A) {
        int [] first = new int[A.length];
        int [] second = new int[A.length];

        for(int i=0;i<A.length;i++){
            first[i] = A[i]+i;
            second[i] = A[i]-i;
        }

        int result = 0;
        int t = 0;
        for(int i=0;i<A.length-1;i++){
            for (int j = i+1; j < A.length && j<i+1000; j++) {
                t = first[i]+second[j];
                if(result<t)
                    result=t;
            }
        }

        return result;
    }
```

