# 3주차 문제 풀이 인증

## 기본 정보

- 이름: 박도연
- 목표 문제 수: 3
- 실제 풀이 문제 수: 3

---

# 오답노트

## 문제 1

- **문제명:** 구명보트
- **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/42885
- **알고리즘 / 자료구조:** 탐욕법 (Greedy), 투 포인터 (Two Pointers), 정렬

### 접근 및 시행착오

- 가장 무게가 많이 나가는 사람과 가장 적게 나가는 사람을 짝지어 구명보트에 태우는 그리디(Greedy) 및 투 포인터(Two Pointers) 방식을 접근했다.
- **1차 시도에서의 오류**:
  1. `right = len(people)`로 인덱스를 초기화하면서 첫 번째 참조부터 `IndexError: list index out of range`가 발생했다. (파이썬 인덱스는 `0`부터 `len(people) - 1`까지 존재)
  2. `while left <= right` 조건 사용 시 `left == right` (1명 남았을 때) 자기 자신과 무게를 더해 비교하는 경계 조건 처리가 미흡했다.

```python
# 1차 시도 코드 (오류 발생)
def solution(people, limit):
    people.sort(reverse=True)
    answer = 0 
    left = 0
    right = len(people)
    while left <= right:
        if people[left] + people[right] <= limit:
            left += 1
            right -= 1
        else:
            left += 1
        answer += 1
        print(answer)
    print(answer)
    return answer
```

### 최종 풀이

1. `right = len(people) - 1`로 양 끝 인덱스를 바르게 초기화한다.
2. 내림차순 정렬 후, 무거운 사람(`left`)과 가벼운 사람(`right`)의 합이 `limit` 이하이면 2명이 동승한다 (`right -= 1`).
3. 무거운 사람(`left`)은 매번 탑승하므로 `left += 1`과 `answer += 1`을 수행한다.
4. `left == right`로 1명만 남아있는 경우, 혼자 탑승(`answer += 1`) 후 루프를 종료한다.

```python
def solution(people, limit):
    people.sort(reverse=True)
    answer = 0
    left = 0
    right = len(people) - 1

    while left <= right:
        # 1명만 남아있는 경우 혼자 타고 종료
        if left == right:
            answer += 1
            break

        # 가장 무거운 사람 + 가장 가벼운 사람 합이 limit 이하인 경우 (2명 동승)
        if people[left] + people[right] <= limit:
            right -= 1

        # 무거운 사람은 무조건 보트에 탑승
        left += 1
        answer += 1

    return answer
```

> **💡 참고 (2명 짝짓기 수 기반 풀이)**
> `len(people) - answer` 공식을 이용하면 2명씩 탄 횟수만 세어 1명 남은 경계 조건을 따지지 않고 더 간결하게 작성할 수 있다.

```python
def solution(people, limit):
    answer = 0
    people.sort()
    a, b = 0, len(people) - 1
    while a < b:
        if people[b] + people[a] <= limit:
            a += 1
            answer += 1
        b -= 1
    return len(people) - answer
```

### 복잡도

- 시간복잡도: $O(N \log N)$ (정렬에 $O(N \log N)$, 투 포인터 탐색에 $O(N)$)
- 공간복잡도: $O(1)$ (추가 메모리 최소 사용)

### 핵심 포인트

- **투 포인터 양 끝 인덱스 초기화 주의**: 배열의 마지막 원소를 가리킬 때는 항상 `len(array) - 1`로 설정해 `IndexError`를 방지한다.
- **짝짓기 수 기반의 보트 수 계산**: 총 보트 수 $= P + (N - 2P) = N - P$ 이므로, 2명이 짝지어 탄 보트 수 $P$만 세면 $N - P$로 1명 남은 조건 검사 없이 깔끔하게 풀 수 있다.
- **그리디(Greedy)의 최적해 보장**: 가장 무거운 사람부터 처리할 때 가장 가벼운 사람과 짝짓는 시도가 항상 보트 개수를 최소화하는 최적의 선택이 됨을 이해했다.

---

## 문제 2

- **문제명:** 연속 부분 수열 합의 개수
- **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/131701
- **알고리즘 / 자료구조:** 해시 세트 (`set`), 슬라이싱 / 원형 수열 (Circular Sequence)

### 접근 및 시행착오

- 원형 수열(Circular Sequence) 특성을 고려하여 배열을 2배로 확장하는 아이디어(`new_elements = elements * 2`)는 잘 떠올렸다.
- 하지만 부분 수열의 길이(1부터 $N$까지)에 따라 구간 합을 구하고 중복을 제거하는 집합(`set`) 활용 및 2중 반복문 구조를 어떻게 작성해야 할지 구체적인 로직을 완성하지 못했다.

```python
# 1차 시도 코드 (미완성)
def solution(elements):
    new_elements = elements * 2
    for i in range(len(elements)):
        for j in range(i + 1):
            pass  # 구간 합 구하기 및 중복 제거 로직 구현 미흡
            
    answer = 0
    return answer
```

### 최종 풀이

1. 원형 수열의 연결 부위를 쉽게 처리하기 위해 배열을 2배로 늘린 `extended = elements * 2`를 생성한다.
2. 구해진 부분 수열의 합들 중 중복을 자동으로 제거하기 위해 `sums = set()`을 선언한다.
3. 부분 수열의 길이 `length`를 `1`부터 `n`까지 변화시키는 이중 루프를 작성한다.
4. 각 길이별로 시작 인덱스 `start` (0부터 $n-1$)에서 `extended[start : start + length]` 슬라이싱의 합을 구해 `sums.add()`로 저장한다.
5. 모든 길이와 위치에서의 합 저장이 끝나면 중복이 제거된 집합의 크기 `len(sums)`를 반환한다.

```python
def solution(elements):
    n = len(elements)
    extended = elements * 2
    sums = set()

    # 부분 수열의 길이 length: 1부터 n까지
    for length in range(1, n + 1):
        # 시작 위치 start: 0부터 n-1까지
        for start in range(n):
            # 슬라이싱을 통한 구간 합 구하기 및 set을 통한 중복 제거
            sums.add(sum(extended[start:start + length]))

    return len(sums)
```

### 참고할 만한 다른 풀이 (누적 합 & 모듈러 연산 $O(N^2)$ 최적화)

- 매번 `sum()`을 호출하지 않고, **시작 원소에서 다음 원소를 계속 더해가는 누적 합 방식**을 사용하면 $O(N^2)$ 만에 훨씬 빠르게 해결할 수 있다.
- 모듈러 연산(`j % ll`)으로 2배 확장 배열을 만들지 않고도 원형 순회를 구현했다.

```python
def solution(elements):
    ll = len(elements)
    res = set()

    for i in range(ll):
        ssum = elements[i]
        res.add(ssum)  # 길이 1짜리 부분 수열 합
        # 시작점 i부터 다음 원소들을 누적해서 더함 (길이 2 ~ ll)
        for j in range(i + 1, i + ll):
            ssum += elements[j % ll]  # O(1) 시간에 다음 길이의 합 계산
            res.add(ssum)
            
    return len(res)
```

### 복잡도

- **슬라이싱 풀이**: 시간복잡도 $O(N^3)$, 공간복잡도 $O(N^2)$
- **누적 합 최적화 풀이**: 시간복잡도 $O(N^2)$, 공간복잡도 $O(N^2)$

### 핵심 포인트

- **누적 합(Cumulative Sum) 최적화**: 매번 슬라이스의 전체 합(`sum(slice)`)을 새로 구하는 것은 $O(N)$이 걸리지만, 이전 합에 다음 원소 하나만 더해주는 방식(`ssum += next_elem`)을 쓰면 단 $O(1)$ 만에 다음 길이의 합을 구할 수 있다.
- **원형 수열 처리 테크닉**:
  1. `elements * 2` 확장 배열 사용 (직관적인 슬라이싱 가능)
  2. `index % len(elements)` 모듈러 연산 활용 (추가 배열 생성 없이 인덱스 순환 가능)
- **중복 제거 (Set 활용)**: 부분 수열의 위치나 길이가 다르더라도 '합의 값'이 같으면 하나로 처리해야 하므로 `set` 자료구조를 활용한다.

---

## 문제 3

- **문제명:** 연속된 부분 수열의 합
- **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/178870
- **알고리즘 / 자료구조:** 투 포인터 (Two Pointers), 슬라이딩 윈도우 (Sliding Window)

### 접근 및 시행착오

- 부분 수열의 길이를 1부터 늘려가며 모든 구간의 합을 직접 구하는 3중 반복문 구조를 작성했다.
- 하지만 입력 배열의 길이 $N$이 최대 $1,000,000$까지 주어지므로 $O(N^3)$의 시간 복잡도를 가져 테스트 케이스에서 **시간 초과(Time Limit Exceeded)**가 발생했다.

```python
# 1차 시도 코드 (시간 초과 발생)
def solution(sequence, k):
    for i in range(len(sequence)):
        left = 0
        right = i
        for a in range(len(sequence) - i):
            sum_val = 0
            for j in range(left, right + 1):
                sum_val += sequence[j]
            if sum_val == k:
                break
            left += 1 
            right += 1 
        if sum_val == k:
            break
    return [left, right]
```

### 최종 풀이

1. `for right in range(len(sequence))` 루프를 돌며 오른쪽 포인터 `right`를 한 칸씩 확장한다 (`current_sum += sequence[right]`).
2. 구간 합 `current_sum`이 `k`보다 큰 경우, `k` 이하가 될 때까지 `while current_sum > k:` 루프에서 `left` 포인터를 줄인다 (`current_sum -= sequence[left]`, `left += 1`).
3. `current_sum == k`에 도달하면, 현재 구간 길이(`right - left`)가 이전 최소 길이(`answer[1] - answer[0]`)보다 **엄격히 작을 때만** `answer = [left, right]`로 갱신한다. (엄격한 소등호 `<`를 사용하므로 길이가 같은 구간이 나중에 나오더라도 항상 **시작 인덱스가 더 작은 앞선 구간**이 유지된다)

```python
def solution(sequence, k):
    left = 0
    current_sum = 0
    answer = [0, len(sequence) - 1]

    for right in range(len(sequence)):
        current_sum += sequence[right]

        # current_sum이 k보다 커지면 left를 오른쪽으로 밀어 구간을 줄임
        while current_sum > k:
            current_sum -= sequence[left]
            left += 1

        # current_sum이 k와 같고, 기존 최소 길이보다 짧은 경우에만 갱신
        if current_sum == k:
            if right - left < answer[1] - answer[0]:
                answer = [left, right]

    return answer
```

### 복잡도

- **1차 시도 풀이**: 시간복잡도 $O(N^3)$, 공간복잡도 $O(1)$ $\rightarrow$ **시간 초과**
- **투 포인터 / 슬라이딩 윈도우 풀이**: 시간복잡도 $O(N)$, 공간복잡도 $O(1)$ (각 원소는 `right`에 의해 1번 더해지고 `left`에 의해 최대 1번 빼지므로 전체 $2N$ 번 연산)

### 핵심 포인트

- **시간 초과 원인 파악 ($N \le 1,000,000$)**: $N$이 최대 100만인 문제에서는 $O(N^2)$ 이상의 다중 루프 알고리즘은 무조건 시간 초과가 발생하므로 선형 시간 $O(N)$ 알고리즘으로 설계해야 한다.
- **`for right` 기반 슬라이딩 윈도우 패턴**:
  - `right`를 `for` 루프로 늘리고, 조건 초과 시 `while`로 `left`를 당기는 구조는 인덱스 범위 초과 예외를 방지해 주어 매우 안전하고 직관적이다.
- **동일 길이 시 앞선 인덱스 유지 테크닉**:
  - `right - left < answer[1] - answer[0]` 조건문에서 미만(`<`)을 사용하면, 같은 길이의 구간이 나중에 다시 발견되더라도 갱신되지 않고 **시작 인덱스가 가장 작은 구간**이 자동으로 보존된다.


---

# 다음 주 목표

- 목표 문제 수: 3
- 집중할 유형: 탐욕법 (Greedy) / 투 포인터 / DFS / BFS
