> [!NOTE]
> 이 파일을 복사해서 `week0N/{영문이름}.md`로 저장한 뒤 작성해주세요.
> AI나 블로그 풀이를 그대로 복사하지 않고 본인의 언어로 정리해주세요.

# 7주차 문제 풀이 인증

## 기본 정보

- 이름: 홍진기
- 목표 문제 수: 3
- 실제 풀이 문제 수: 3

---

# 오답노트

## 문제 1

- **문제명:** 문자열 압축
- **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/60057
- **알고리즘 / 자료구조:** 완전탐색

### 접근 및 시행착오

가장 효과적으로 압축하기 위해서는 압축 단위가 길어도 **문자열의 절반 길이 이하**여야 된다는 생각을 갖고 접근했다.

시행착오를 겪은 부분은 크게 2가지이다.

1. 압축 반복문의 조건 설정

    처음에는 `i + block < s.length()`로 설정했다(`=`을 포함하지 않았다).

    `substring()`을 활용할 경우, `i + block`의 마지막 인덱스의 -1 값까지만 처리한다. `i=0`부터 시작하기 때문에 -1 값까지 처리되어도 로직은 올바르지만, 결국 `i + block < s.length`로 설정할 경우, 처리되지 않은 문자열이 생긴 상태로 반복문이 종료되기 때문에 `i + block == s.length()`인 경우에도 `s.length() -1`까지 반복이 가능하기 때문에 이를 포함해야 했다.

2. 압축 단위보다 작은 문자열에 대한 마지막 처리

    마지막에 남은 문자열이 압축 단위보다 작을 경우, 반복문으로 처리하지 못하기 때문에 반복문 이후 별도로 처리해야 했다.

    따라서 반복문을 끝으로 계산된 `nextIdx` 값을 활용하여 남은 문자열을 덧붙였다.

### 최종 풀이

```java
import java.util.*;

class Solution {
    public int solution(String s) {
        int answer = s.length();
        
        int max = s.length()/2;
        
        for(int i = 1 ; i <= max; i++){
            String zipResult = zip(s, i);
            answer = Math.min(answer, zipResult.length());
        }
        
        return answer;
    }
    
    // 압축 메서드
    String zip(String s, int block){
        
        int count;
        int nextIdx = 0;
        int i;
        
        StringBuilder sb = new StringBuilder();
        
        // i + block -> block 길이 만큼 남았을 때 처리 가능이므로 `<=`
        // i는 현재 압축 묶음의 시작 위치, nextIdx는 아직 처리하지 않은 위치
        for(i = 0; i + block <= s.length(); i = nextIdx){

            String start = s.substring(i, i + block);
            String next;
            count = 1;
            nextIdx = i + block;

            while(nextIdx + block <= s.length() && 
                  start.equals(s.substring(nextIdx, nextIdx + block))){
                count++;
                nextIdx += block;
            }
            
            if(count > 1){
                sb.append(count);
            }
            sb.append(start);
        
        }
        
        //block보다 짧게 남았을 때 (ex. block은 3인데 2만 남았을때) 처리
        if (nextIdx < s.length()){
            sb.append(s.substring(nextIdx, s.length()));
        }
        
        return sb.toString();
        
    }
    
}
```

### 복잡도

- 시간복잡도:
- 공간복잡도:

### 핵심 포인트

다음에 비슷한 문제를 풀 때 기억할 내용을 작성합니다.

---

## 문제 2

- **문제명:** 택배 배달과 수거하기
- **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/150369
- **알고리즘 / 자료구조:** 그리디 알고리즘

### 접근 및 시행착오

처음에는 가장 먼 집부터 시작하여 한 번의 왕복으로 배달, 수거할 수 있는 집의 범위를 각각 탐색하는 방식으로 접근했다.

하지만 한 집의 물량이 한 번의 왕복으로 처리할 수 있는 양보다 많은 경우를 생각하지 못했다.

예를 들어, 한 집에 배달할 상자가 `cap`보다 많다면, 해당 집까지 여러 번 왕복해야 한다. 따라서 처음에 `cap`과 각 집의 배달, 수거할 상자를 파악하여 왕복 횟수를 먼저 결정한 뒤, 결정한 왕복에서 남은 배달, 수거 가능 용량을 누적해서 관리해야 했다.

필요한 왕복 횟수를 구하기 위해서는 `cap` 값을 각 집의 배달, 또는 수거 용량으로 나눠 올림을 해야했다.

`Math.ceil()`을 사용했지만, 인자를 `double`로 바꾸기 전에 정수 나눗셈이 먼저 수행되어 일부 테스트케이스가 틀렸다.

이 문제에서는 이를 간단하게 해결하기 위해 정수 올림 나눗셈을 사용해야 했다.

```java
(amount + cap - 1) / cap // Math.ceil(amount/cap)을 정수 올림 나눗셈으로 변환한 코드이다.
```

### 최종 풀이

가장 먼 집부터 0번 집까지 역순으로 순회한다.

`deliveryRemain`과 `pickupRemain`에는 현재 집부터 더 먼 집까지의 물량과, 이전 왕복에서 남은 용량을 함께 저장한다.

- 값이 양수이면 아직 처리하지 못한 물량이다.
- 값이 음수이면 이전에 추가한 왕복에서 남은 처리 가능 용량이다.

현재 위치에서 배달 또는 수거 물량이 남아 있다면, 둘 중 더 많은 왕복이 필요한 횟수만큼 해당 집까지 왕복해야 한다. 한 번의 왕복에서 배달과 수거를 동시에 처리할 수 있으므로 두 왕복 횟수의 최댓값을 사용한다.

왕복 횟수를 거리 계산에 반영한 뒤, `visitCnt * cap`만큼을 각 잔여량에서 차감한다. 한쪽 값이 음수가 되는 것은 다른 작업 때문에 추가한 왕복의 여유 용량을 의미한다.

```java
class Solution {
    public long solution(int cap, int n, int[] deliveries, int[] pickups) {
        long answer = 0;
        long deliveryRemain = 0;
        long pickupRemain = 0;

        for (int i = n - 1; i >= 0; i--) {
            deliveryRemain += deliveries[i];
            pickupRemain += pickups[i];

            long deliveryVisitCnt =
                (Math.max(0L, deliveryRemain) + cap - 1) / cap;
            long pickupVisitCnt =
                (Math.max(0L, pickupRemain) + cap - 1) / cap;

            long visitCnt = Math.max(deliveryVisitCnt, pickupVisitCnt);

            answer += 2L * (i + 1) * visitCnt;

            deliveryRemain -= visitCnt * cap;
            pickupRemain -= visitCnt * cap;
        }

        return answer;
    }
}
```

### 복잡도

- 시간복잡도: `O(n)`
- 공간복잡도: `O(1)`

### 핵심 포인트

- 가장 먼 미처리 집까지 가는 왕복은 반드시 필요하므로, 집을 뒤에서부터 처리하는 그리디 방식을 사용할 수 있다.
- 배달과 수거는 같은 왕복에서 동시에 가능하므로 필요한 왕복 횟수는 둘 중 큰 값이다.
- 음수 잔여량은 오류가 아니라, 기존 왕복에서 남은 용량을 의미한다.
- 양수 `amount`의 정수 올림 나눗셈은 `(amount + cap - 1) / cap`으로 계산할 수 있다.

---

## 문제 3

- **문제명:** 점 찍기
- **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/140107
- **알고리즘 / 자료구조:** 수학, 완전탐색

### 접근 및 시행착오

처음에는 원점 `(0, 0)`에서 시작하여 상하좌우로 `k`만큼 이동하는 BFS로 모든 좌표를 탐색했다.

이후 거리를 구하는 아래의 공식을 활용하여 범위 내(거리가 d 이하)의 모든 좌표를 탐색하면 탐색한 개수를 반환하는 방식으로 문제를 풀었었다.

```text
x² + y² <= d²
```

하지만 `d`는 최대 1,000,000이므로 2차원 방문 배열에는 최대 약 `1000000 x 1000000`개의 값이 필요하다. 결국 입력값에 따라 메모리 초과가 발생하였고, BFS로는 문제를 해결하지 못했다.

그리고 더 적은 메모리와 시간을 사용하는 방식으로 문제를 해결하고자 했지만, `int`를 사용함에 따라 오버플로우 문제가 발생하여 일부 테스트케이스가 틀리기도 하였다.

### 최종 풀이

수학적인 접근으로 문제를 생각보다 쉽게 해결할 수 있었다.

핵심은 **행의 위치에 따라 찍을 수 있는 좌표의 개수가 달라진다는 점**이었다.

예를 들어, `k=1`, `d=5`일 때, 행이 0일 때 찍을 수 있는 좌표의 개수는 6개이지만, 행이 5일 때 찍을 수 있는 좌표의 개수는 1개이다.

`(x, y)`에서 `x`의 값에 따라 찍을 수 있는 `y` 좌표의 개수를 구하고, 행마다 이 `(x, y)` 좌표의 개수를 구하는 방식으로 문제를 해결했다.

따라서 `for`문을 통해 `x` 값의 반복을 통해 `y` 좌표의 개수를 구할 수 있는 공식은 거리 공식을 변환하여 아래와 같이 구할 수 있다.

```java
long distance = (long) Math.sqrt(d * d - i * i);
```
최종 코드는 다음과 같다.

```java
class Solution {
    public long solution(int k, int d) {
        long answer = 0;
        long dSquared = (long) d * d;

        for (long x = 0; x <= d; x += k) {
            long maxY = (long) Math.sqrt(dSquared - x * x);
            answer += maxY / k + 1;
        }

        return answer;
    }
}
```

### 복잡도

- 시간복잡도: `O(d / k)`
- 공간복잡도: `O(1)`

### 핵심 포인트

- 입력값의 범위를 통해 시간 복잡도를 주로 계산하다보니 공간복잡도를 계산하는 것을 놓쳤다. 앞으로는 공간복잡도를 계산해야겠다.
- 무작정 `int`를 쓰는 것보다 입력값의 범위를 고려해서 유동적으로 타입을 선언하는 습관을 가져야겠다.

---

# 다음 주 목표

- 목표 문제 수:
- 집중할 유형:
