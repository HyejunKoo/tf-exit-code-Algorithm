# 9주차 문제 풀이 인증

## 기본 정보

* 이름: 주정윤
* 목표 문제 수: 3
* 실제 풀이 문제 수: 3

---

# 문제 요약

| 번호 | 문제 이름      | 난이도  | 링크                                                                        |
| -- | ---------- | ---- | ------------------------------------------------------------------------- |
| 1  | 이진 변환 반복하기 | Lv.2 | [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/70129)  |
| 2  | 귤 고르기      | Lv.2 | [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/138476) |
| 3  | 영어 끝말잇기    | Lv.2 | [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/12981)  |

---

# 오답노트

## 문제 1

* **문제명:** 이진 변환 반복하기
* **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/70129
* **알고리즘 / 자료구조:** 구현, 문자열

### 접근 및 시행착오

문자열에서 0을 제거한 뒤 남은 1의 개수를 다시 이진수로 변환하는 과정을 반복하면 된다.

0을 직접 제거하는 것보다 문자열을 순회하면서 0과 1의 개수만 세고, 1의 개수를 다시 이진수로 변환하는 방식으로 해결했다.

### 최종 풀이

1. 문자열을 순회하면서 0의 개수와 1의 개수를 센다.
2. 발견한 0의 개수를 전체 제거 횟수에 더한다.
3. 남아있는 1의 개수를 이진수 문자열로 변환한다.
4. 문자열의 길이가 1이 될 때까지 반복한다.
5. 최종적으로 이진 변환 횟수와 제거된 0의 개수를 반환한다.

### 내가 푼 코드

```java
import java.util.*;

class Solution {
    public int[] solution(String s) {
        int[] answer = new int[2];

        while(s.length() > 1){
            int cnt = 0;
            for(int i=0; i<s.length(); i++){
                if(s.charAt(i) == '0') answer[1]++;
                else cnt++;
            }

            s = Integer.toBinaryString(cnt);
            answer[0]++;
        }

        return answer;
    }
}
```

### 복잡도

* 시간복잡도: O(N)
* 공간복잡도: O(N)

### 핵심 포인트

문자열에서 0을 실제로 삭제하지 않고, 1의 개수만 구해서 바로 이진수로 변환하면 반복 과정을 간단하게 구현할 수 있다.

---

## 문제 2

* **문제명:** 귤 고르기
* **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/138476
* **알고리즘 / 자료구조:** 해시, 정렬, 그리디

### 접근 및 시행착오

서로 다른 종류의 귤을 최소한으로 선택해야 하기 때문에, 같은 크기의 귤이 많이 있는 종류부터 선택하면 된다고 생각했다.

먼저 `HashMap`을 이용해서 귤의 크기별 개수를 저장하고, 개수가 많은 순서대로 정렬했다.

### 최종 풀이

1. `HashMap`을 이용해서 귤의 크기별 개수를 센다.
2. `HashMap`의 key를 리스트로 만든다.
3. 귤의 개수가 많은 순서대로 정렬한다.
4. 개수가 많은 종류부터 `k`에서 빼면서 선택한다.
5. `k`가 0 이하가 되면 필요한 종류의 개수를 반환한다.

### 내가 푼 코드

```java
import java.util.*;

class Solution {
    public int solution(int k, int[] tangerine) {
        int answer = 0;

        Map<Integer, Integer> map = new HashMap<>();

        for(int s : tangerine){
            map.put(s, map.getOrDefault(s, 0)+1);
        }

        List<Integer> key = new ArrayList<>(map.keySet());
        key.sort((o1, o2) -> map.get(o2).compareTo(map.get(o1)));

        System.out.println(key);
        for(int i:key){
            k -= map.get(i);
            answer++;
            if(k<=0)
                return answer;
        }

        return answer;
    }
}
```

### 복잡도

* 시간복잡도: O(N + M log M)
* 공간복잡도: O(M)

※ N은 전체 귤의 개수, M은 귤의 종류 수이다.

### 핵심 포인트

종류의 개수를 최소화해야 하는 문제이기 때문에, **개수가 많은 종류부터 선택하는 그리디 방식**으로 해결할 수 있다.

---

## 문제 3

* **문제명:** 영어 끝말잇기
* **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/12981
* **알고리즘 / 자료구조:** 해시, 구현

### 접근 및 시행착오

끝말잇기에서 탈락하는 경우를 두 가지로 나눠서 생각했다.

1. 이전에 사용했던 단어를 다시 사용하는 경우
2. 앞에서 말한 단어의 마지막 문자와 현재 단어의 첫 문자가 다른 경우

이미 사용한 단어를 확인하기 위해 `HashSet`을 사용했다.

### 최종 풀이

첫 번째 단어를 `HashSet`에 저장하고 두 번째 단어부터 하나씩 확인한다.

현재 단어가 이미 사용된 단어인지 확인하고, 동시에 이전 단어의 마지막 문자와 현재 단어의 첫 문자가 같은지 확인한다.

조건을 만족하지 못하면 반복을 종료하고 탈락자를 반환한다.

모든 단어를 정상적으로 사용했다면 `[0, 0]`을 반환한다.

### 내가 푼 코드

```java
import java.util.*;

class Solution {
    public int[] solution(int n, String[] words) {
        int[] answer = {};
        int cnt = 1;
        Set<String> set = new HashSet<>();
        set.add(words[0]);

        for(int i=1; i<words.length; i++){
            if(set.contains(words[i])) break;

            if(words[i].charAt(0) == words[i-1].charAt(words[i-1].length()-1)){
                set.add(words[i]);
                cnt++;
            }
            else break;
        }

        if(cnt==words.length) answer = new int[] {0, 0};

        return answer;
    }
}
```

### 복잡도

* 시간복잡도: O(N)
* 공간복잡도: O(N)

※ N은 `words`의 길이이다.

### 핵심 포인트

`HashSet`을 이용하면 이전에 사용한 단어인지 빠르게 확인할 수 있다.

끝말잇기의 규칙은 현재 단어와 이전 단어를 비교하면 되므로, `words[i]`와 `words[i-1]`을 비교하면서 순서대로 확인하면 된다.

---