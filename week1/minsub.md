# 1주차 문제 풀이 인증

## 기본 정보

- 이름: 윤민섭
- 목표 문제 수: 4
- 실제 풀이 문제 수: 4

## 푼 문제 목록

| 번호  | 문제 이름                                          |    난이도    | 링크                                                                                                                  | 풀이 여부 |
|:---:|------------------------------------------------|:---------:|---------------------------------------------------------------------------------------------------------------------|:-----:|
|  1  | KoKo Eating Bananas                            |  Medium   | [코코 이팅 버네나~](https://leetcode.com/problems/koko-eating-bananas/description/)                                        |   O   |
|  2  | Longest Substring Without Repeating Characters |  Medium   | [연속 문자열 중 가장 긴 거 근데 이제 중복 제거를 곁들인..](https://leetcode.com/problems/longest-substring-without-repeating-characters/) |   O   |
|  3  | 큰 수 만들기                                        |  Level 2  | [큰 수 만들기](https://school.programmers.co.kr/learn/courses/30/lessons/42883#)                                         |   O   |
|  4  |               Network Delay Time                                 |     Medium      | [Network Delay Time](https://leetcode.com/problems/network-delay-time/description/)                                                                                              |   X   |

---

# 오답노트

## 문제 1

### 문제 정보

- 문제명: KoKo Eating Bananas
- 문제 링크: [코코 이팅 버네나~](https://leetcode.com/problems/koko-eating-bananas/description/)
- 사용한 알고리즘 / 자료구조: 이분(이진) 탐색

### 처음 접근 방식

우선 문제에 주어진 조건을 차근차근 살폈다.
직원이 돌아올 h 시간 내로 특정 속도 k를 통해 piles 내부의 바나나를 먹어 치우는 것이 관건이라고 생각했다.
다만, piles의 범위를 고려했을 때 이분 탐색이 아니면 무조건 터질 거라고 생각해서 이분 탐색을 떠올렸다.

### 풀이 방법
```java
import java.util.*;
class Solution {
    public int minEatingSpeed(int[] piles, int h) {
        int left = 1;
        int right = Arrays.stream(piles).max().getAsInt();

        while(left < right) {
            int mid = left + (right - left) / 2;

            int hours = 0;

            for (int pile : piles) {
                hours += (pile + mid -1) / mid; 

                if (hours > h) {
                    break;
                }
            }

            if (hours <= h) {
                right = mid;
            } else {
                left = mid + 1;
            }

        }

        return left;
    }
}
```
- 우선 left, right를 지정했다.
- Arrays.stream().max().getAsInt(); 이 함수가 자바 이분 탐색 시에 유용하게 쓰이는 것 같다.
- mid를 정해주고, pile들을 돌며, 현재 mid(속도)에서 시간 내로 다 먹는지 본다.
- 못 먹으면 break 해주고, 속도를 키워주고(left = mid + 1)
- 가능하면, 범위를 좁혀본다. (right = mid)

### 어려웠던 지점

이 문제의 핵심은 속도 `speed`가 주어졌을 때, 전체 바나나를 몇 시간 안에 먹을 수 있는가? 였다. 한 더미 `pile`을 `speed`씩 먹을 때 필요한 시간의 공식을 떠올리는 데 간단하지만, 시간이 좀 걸렸다. 

`(pile + speed - 1) / speed` pile을 그대로 speed로 나누었을 때 딱 떨어지지 않는 경우 1시간이 필요하기에 저 공식을 적용하면 예를 들어 7시간이고 속도가 3인 경우에는 3으로, 6시간인 경우에는 2시간으로 반환시킬 수 있다.
### 해결 방법

- 기본 테스트 케이스에서는 문제가 없었지만, 오버플로우를 고려하여 `mid = left + (right - left) / 2`로 수정한 후 제출했다. (그냥 long 써도 됨)

### 시간복잡도 / 공간복잡도

- 시간복잡도: O(n log M)  
> n = piles.length, M = piles 안의 최댓값 > `int right = Arrays.stream(piles).max().getAsInt();`여기서 최댓값을 찾기 위해 배열 한 번 순회해서 O(n), 그다음 이분 탐색 속도 범위 `1 ~ max(piles)`안에서 진행되는데 범위가 반씩 줄어드니까 O(log M), 그리고 매 반복마다 pile을 확인할 수 있으니 O(n) 즉 전체는 O(n) + O(n log M) = O(n log M)

- 공간복잡도: O(1)


### 새롭게 알게 된 점

이분 탐색으로 구하고자 하는 것을 명확히 정의하고, 그 과정의 핵심 로직을 떠올리는 데에 집중하자.

---

## 문제 2

### 문제 정보

- 문제명: Longest Substring Without Repeating Characters
- 문제 링크: [연속 문자열 중 가장 긴 거 근데 이제 중복 제거를 곁들인..](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
- 사용한 알고리즘 / 자료구조: set, 투포인터, 슬라이딩 윈도우

### 처음 접근 방식

문제의 핵심은 이거였다. '중복된 문자 없는 연속 문자열의 최대 길이를 구하라.'

첨엔, 단순히 이거 그냥 중복 제거해서 뭐 substring이나, char 반복문으로 뿌려서 세면 되지 않을까?
라고 생각했지만,, 주어지는 문자열의 length가 5 * 10^4여서 브루트포스로 돌면 굉장히 비효율적일 것이다...

투포인터와 슬라이딩 윈도우를 적용했다.



### 풀이 방법
```java
import java.util.*;

class Solution {
    public int lengthOfLongestSubstring(String s) {
        // 중복된 문자 없는 연속 문자열의 최대 길이 출력
        Set<Character> set = new HashSet<>();

        int maxInt = 0;
        int left = 0;
        
        for (int right = 0; right < s.length(); right++) {
            char c = s.charAt(right);

            while (set.contains(c)) {
                set.remove(s.charAt(left));
                left++;
            }

            set.add(c);
            maxInt = Math.max(maxInt, right - left + 1);
        }

        return maxInt;
    }
}
```
중복이 없게끔 연속되는 문자를 계속 유지하는 것이 관건이고, 그래서 슬라이딩 윈도우 기법을 적용해서
중복이 발견된 경우 해당 하는 문자가 사라질 때까지 left를 삭제하고, 증가시키면서 끝까지 본다.

- left: 현재 윈도우의 시작 위치
- right: 현재 윈도우의 끝 위치
- set: left ~ right 구간 안에 문자들을 저장



### 어려웠던 지점

투포인터/슬라이딩 로직 자체를 떠올리는 게 좀 어려웠다.

### 해결 방법

떠올리고 나서는 간단하게 구현했다.

### 시간복잡도 / 공간복잡도

- 시간복잡도: O(n) 
> 겉으로는 for문 안에 while이 들어가서 n 제곱을 떠올릴 수 있지만, right는 왼-오로 한번만 이동하고, left의 경우에도 while에서 right을 향해서만 이동해서 최대 O(n)이다.
- 공간복잡도: O(n)

### 새롭게 알게 된 점

연속된 구간 안에서, 특정 범위 또는 조건의 값을 보아야 한다면, 슬라이딩 윈도우를 고려해볼 필요가 있을 것 같다.

다만, 헷갈리면 안되는 부분은 지금처럼 substring(연속된) 문자열에서 뭔가를 구하는 게 아니라, Subsequence(순서는 유지하되, 연속될 필요는 없는 부분 문자열) 문제의 경우에는 오히려 슬라이딩 윈도우보다

- DP
- 그리디
- 백트래킹
- LCS (최장 공통 부분 수열)
- 문자열 매칭

이 적합할 수 있다. 문제를 잘 해석하기 !


---

## 문제 3

### 문제 정보

- 문제명: 큰 수 만들기
- 문제 링크: [큰 수 만들기](https://school.programmers.co.kr/learn/courses/30/lessons/42883#)
- 사용한 알고리즘 / 자료구조: 그리디, 스택

### 처음 접근 방식

처음에는 조합을 뽑아보면서, 비교를 해야 되나 싶었는데, 그럼 오히려 반복이 많아지고 구현이 복잡해지는 것 같았다.

결국 문제의 핵심은 k 기회 안에서 앞자리들을 최댓값으로 남기는 거고, 현재 가장 좋은 값을 판단해야 하는 일종의 그리디라고 보았다.

### 풀이 방법
```java
import java.util.*;
class Solution {
    public String solution(String number, int k) {
        
        Stack<Integer> stack = new Stack<>();
        
        for (int i = 0; i < number.length(); i++) {
            int current = number.charAt(i) - '0';
            
            while(!stack.isEmpty() && k > 0 && stack.peek() < current) {
                stack.pop();
                k--;
            }
            
            stack.push(current);
        }
        
        // number가 내림차순으로 주어진 경우 k가 남아있음
        while(k > 0) {
            stack.pop();
            k--;
        }
        
        StringBuilder sb = new StringBuilder();
        
        for (int i = 0; i < stack.size(); i++) {
            sb.append(stack.get(i));
        }
        
        return sb.toString();
    }
}
```

- stack을 통해 값을 넣는다. 
- while 문에서 현재 추가하려는 값이 이전 값보다 큰 경우, 삭제 시키는(주어진 k 횟수 안에서) 조건을 통해 최적의 값을 구하는 그리디를 적용했다.
- 다만, 9876 같이 내림차순으로 주어지는 경우에는 대처가 불가능해서, 스택 자체가 LIFO이니, k 수만큼 pop 시킴으로써 문제의 조건을 만족하도록 했다.


### 어려웠던 지점

이 문제는 단순히 작은 숫자를 k개 제거하는 문제가 아니라, 현재 숫자를 기준으로 이전에 선택한 숫자를 되돌려 제거해야 하는 문제이다. 현재 숫자가 스택의 top보다 크다면, 이전에 선택한 작은 숫자를 제거하고 현재 숫자를 앞쪽에 배치하는 것이 더 큰 수를 만든다. 

따라서 가장 최근에 선택한 숫자부터 비교하고 제거할 수 있는 스택을 사용한다. 이 발상을 떠올리지 못하면 구현 자체는 간단해도 접근이 어려운 문제일 수 있다고 생각한다.

### 해결 방법

현재 값 > stack.peek()일때 pop 시키는 로직 구현 이후에는 어려운 건 없었다.

### 시간복잡도 / 공간복잡도

- 시간복잡도: O(n)
- 공간복잡도: O(n)

### 새롭게 알게 된 점



---
## 문제 4

### 문제 정보

- 문제명: Network Delay Time
- 문제 링크: [Network Delay Time](https://leetcode.com/problems/network-delay-time/description/)
- 사용한 알고리즘 / 자료구조: 다익스트라, 그래프

### 처음 접근 방식

처음에는 `k`번 노드에서 출발해서 모든 노드를 탐색해야 하므로, 단순히 그래프를 순회하면 되는 문제라고 생각했다. 근데 예제를 보면서 이 문제가 내가 직접 모든 노드를 차례대로 방문하는 문제가 아니라 `k`번 노드에서 시작한 신호가 간선을 따라 퍼지는 **전파 문제**라는 점을 이해해야 했다.

즉, 특정 순서로 노드를 방문하는 최소 경로를 구하는 문제 X

`k`번 노드에서 각 노드까지 신호가 도달하는 **가장 빠른 시간**을 구하는 문제였다.

따라서 이 문제는 다음과 같이 볼 수 있었다.
> dist[i] = k번 노드에서 i번 노드까지 신호가 도달하는 최소 시간

그리고 모든 노드가 신호를 받은 시점은 각 노드의 최소 도달 시간 중 가장 큰 값이 된다.

그래서 정답 = `max(dist[1], dist[2], ..., dist[n])`

여기서 max를 쓰는 건, 각 노드가 신호를 받는 가장 빠른 시간을 구한 뒤, 그 중에서 늦게 받은 노드의 시간이 전체 전파 완료 시간이 되기 때문에 `Math.max`가 필요하다고 판단했다.
### 풀이 방법

처음에 통과하지 못했기에 후에 개선하여 통과된 코드는 기재하지 않고, 풀었던 방식만 기록하고자 한다.

- `times` 배열을 이용해 방향 그래프를 만든다.
  - `times[i] = [from, to, cost]`
  - `from`에서 `to`로 가는 데 `cost` 시간이 걸린다.
- `dist` 배열을 만든다.
  - `dist[i]`는 `k`에서 `i`번 노드까지 가는 최소 시간을 의미한다.
  - 처음에는 모든 값을 `INF`로 초기화한다.
  - 시작 노드 `k`는 자기 자신까지 비용이 0이므로 `dist[k] = 0`으로 둔다.
- 우선순위 큐를 사용해 현재까지 비용이 가장 작은 노드부터 탐색한다.
  - 다익스트라는 현재까지 발견된 경로 중 가장 비용이 작은 후보를 먼저 확장해야 함.
  - 그래야 해당 노드까지의 최단 거리를 올바른 순서로 확정할 수 있다.
- 현재 노드에서 갈 수 있는 다음 노드들을 확인한다.
  - 현재 노드까지의 비용 ex) `curCost`
  - 다음 노드로 가는 간선 비용 ex) `nextCost`
  - 새로운 경로 비용은 `curCost + nextCost`
- 새롭게 계산한 비용이 기존 `dist[nextNode]`보다 작으면 갱신한다.

```java
if (newCost < dist[nextNode]) {
    dist[nextNode] = newCost;
    pq.add(new int[]{nextNode, newCost});
}
```

- 다익스트라가 끝난 뒤, 모든 노드의 `dist`값을 확인한다.
  - 하나라도 `INF`라면 도달할 수 없는 노드가 있다는 뜻이므로 `-1`을 반환한다.
  - 모두 도달 가능하다면 `dist` 값 중 최댓값을 반환한다.

### 어려웠던 지점

1. 각 노드의 최솟값들을 넣어서 최종 노드(마지막에 연결된)의 연결된 최소 비용을 어떻게 리턴할 것인지

이 문제에서 dist[i]는 k번 노드에서 i번 노드까지 신호가 도달하는 가장 빠른 시간이다.

예를 들어 다익스타라 결과가 다음과 같다고 하면

```java
dist[1] = 1
dist[2] = 0
dist[3] = 1
dist[4] = 2
```
그러면 1번 노드는 1초에 신호를 받고, 3번 노드도 1초에 신호를 받으며, 4번 노드는 2초에 신호를 받는다.

즉, 전체 전파 완료 시간은 모든 노드가 신호를 받은 시점에 가장 늦게 신호를 받은 노드의 시간이다.

`전체 전파 완료 시간 = max(1, 0, 1, 2) = 2`

따라서 각 노드까지의 최소 도달 시간 중 최댓값을 정답으로 반환해야 했다.

---

2. 왜 우선순위 큐를 비용 기준 오름차순으로 사용할까

항상 최소로 해야 되는 기준이 주어지면, 그냥 별 생각없이 우선순위 큐로 정렬해서 탐색했다.

근데 이번 문제의 경우 초기 해석이 오래 걸렸다보니, 정렬 되었을 때 실행 과정이 한 번에 떠오르지 않았다.

차근차근 떠올려보니, 이런 다익스트라에서 각 노드가 단순한 도착지가 아니라, 다른 노드로 가는 중간 경유지가 될 수 있다.

예를 들어, 이런 그래프가 있다면
```java
K -> A 비용 100
K -> B 비용 1
B -> A 비용 1
A -> C 비용 1
```
`A`로 가는 경로는 두 가지가 있다.
만약 비용 100짜리 A를 먼저 탐색하면 `A->C`까지 이어져 `C`의 비용이 101로 갱신될 수 있는데,
실제로는 `K -> B -> A -> C`경로를 통해 `C`까지 3의 비용으로 갈 수 있는 것이다.

그래서 현재까지 발견된 후보 중 가장 비용이 작은 노드부터 탐색해야 한다.

> 가장 싸게 도달한 노드부터 확장해야, 그 노드를 거쳐 가는 다른 경로들도 최소 비용 기준으로 갱신될 수 있다는 것이다.

즉, 우선순위 큐는 단순히 탐색 비용을 줄이기 위한 용도만이 아니라, 다익스트라에서 최단거리를 확정하는 순서를 보장하기 위해 사용한다는 걸 다시 한 번 체감했다.

---

### 해결 방법

이 문제는 단순 그래프 탐색 문제로 보기보단, `k`번 노드에서 시작한 신호가 각 노드에 도달하는 최소 시간 탐색 문제로 이해했다.

그래서 `dist[i]`를 `k`에서 `i`까지의 최소 도달 시간으로 정의하고, 우선순위 큐를 사용해 현재까지 가장 짧은 비용의 후보부터 탐색했다.

구현 및 실행 흐름
1. dist 배열은 각 노드까지의 최소 도달 시간 저장
2. 우선순위 큐는 현재까지 비용이 가장 작은 후보를 먼저 꺼내도록
3. 더 짧은 경로 발견 시, dist 갱신 후 다시 큐에 넣음
4. 최종 정답은 dist 값 중 최댓값


### 시간복잡도 / 공간복잡도

- 시간복잡도: O((V + E) log V)
  - `V`: 노드 수
  - `E`: 간선 수
  - 우선순위 큐로 간선 탐색 하니까 로그 비용이 발생한다.
- 공간복잡도: O(V + E)
  - 인접 리스트에 간선 정보를 저장함

### 새롭게 알게 된 점

- 아직 다익스트라, 그래프 유형이 많이 부족하구나 체감했드아..


---

# 다음 주 목표

- 목표 문제 수: 5
- 집중할 유형: 정렬, 해시, 일부 고난도 유형