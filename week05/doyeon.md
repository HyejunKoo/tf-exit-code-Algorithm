# 5주차 문제 풀이 인증

## 기본 정보

- 이름: 박도연
- 목표 문제 수: 3
- 실제 풀이 문제 수: 3

---

# 오답노트

## 문제 1

- **문제명:** 게임 맵 최단거리
- **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/1844
- **알고리즘 / 자료구조:** BFS(너비 우선 탐색), 큐(`deque`)

### 접근 및 시행착오

- 시작점 `(0, 0)`에서 오른쪽이나 아래쪽으로 이동하고, 길이 막히면 왼쪽이나 위쪽으로 돌아가는 방식으로 목적지까지의 거리를 구하려 했다.
- 이동할 때마다 `answer`를 1씩 증가시키는 방식으로 코드를 작성했지만 시간 초과가 발생했고, 목적지에 도달할 수 없을 때 `-1`을 반환하는 조건도 구현하지 못했다.

```python
# 1차 시도 코드 (시간 초과)
def solution(maps):
    x = 0
    y = 0
    answer = 0
    while (x != len(maps) and y != len(maps)):
        while (maps[x][y+1] != 1 and maps[x+1][y] != 1):
            if maps[x][y+1] == 1:
                y = y+1
                answer = answer+1
            elif maps[x+1][y] == 1:
                x = x+1
                answer = answer+1
        if maps[x][y-1] == 1:
            y = y-1
            answer = answer+1
        else:
            x = x-1
            answer = answer+1
    return answer
```

시간 초과가 발생한 가장 직접적인 원인은 안쪽 `while`문과 그 내부의 `if`문 조건이 서로 모순되기 때문이다.

```python
while maps[x][y + 1] != 1 and maps[x + 1][y] != 1:
    if maps[x][y + 1] == 1:
```

- 안쪽 `while`문에 들어왔다는 것은 오른쪽과 아래쪽 칸이 모두 `1`이 아니라는 뜻이다.
- 그런데 내부에서는 다시 오른쪽이나 아래쪽 칸이 `1`인지 확인하므로 두 분기 모두 실행되지 않는다.
- 따라서 `x`와 `y`가 변하지 않고 같은 조건을 계속 확인하는 무한 반복이 발생한다.

그 밖에도 다음과 같은 문제가 있었다.

1. `maps[x][y + 1]`, `maps[x + 1][y]`를 확인하기 전에 인덱스가 맵의 범위 안에 있는지 검사하지 않았다.
2. `y == 0` 또는 `x == 0`일 때 `y - 1`, `x - 1`을 사용하면 파이썬의 음수 인덱스로 인해 의도와 달리 맵의 반대쪽 원소를 확인하게 된다.
3. 방문한 칸을 표시하지 않아 이미 지나간 칸을 반복해서 방문할 수 있다.
4. 오른쪽과 아래쪽을 우선하는 하나의 경로만 따라가면 목적지에 도착하더라도 최단 거리임을 보장할 수 없다.
5. 행과 열의 수가 다를 수 있는데 두 값 모두 `len(maps)`로 처리했다. 행의 수는 `len(maps)`, 열의 수는 `len(maps[0])`이다.
6. 시작 칸도 이동 칸 수에 포함되므로 거리는 `0`이 아니라 `1`부터 세어야 한다.

### 최종 풀이

1. 시작 위치 `(0, 0)`와 거리 `1`을 큐에 넣는다.
2. 큐에서 현재 위치를 꺼낸 뒤 상, 하, 좌, 우의 네 방향을 확인한다.
3. 다음 위치가 맵 내부에 있고 이동 가능한 길(`1`)이면 방문 처리한 뒤, 거리를 1 증가시켜 큐에 넣는다.
4. BFS는 시작점에서 가까운 칸부터 탐색하므로 목적지를 처음 꺼냈을 때의 거리가 최단 거리이다.
5. 목적지에 도착하기 전에 큐가 비면 갈 수 있는 모든 칸을 확인한 것이므로 `-1`을 반환한다.

```python
from collections import deque


def solution(maps):
    n = len(maps)
    m = len(maps[0])

    directions = [(-1, 0), (1, 0), (0, -1), (0, 1)]
    queue = deque([(0, 0, 1)])
    maps[0][0] = 0

    while queue:
        x, y, distance = queue.popleft()

        if x == n - 1 and y == m - 1:
            return distance

        for dx, dy in directions:
            nx = x + dx
            ny = y + dy

            if 0 <= nx < n and 0 <= ny < m and maps[nx][ny] == 1:
                maps[nx][ny] = 0
                queue.append((nx, ny, distance + 1))

    return -1
```

### 복잡도

- 시간복잡도: $O(N \times M)$ — 각 칸을 최대 한 번 방문하고, 방문할 때 네 방향을 확인한다.
- 공간복잡도: $O(N \times M)$ — 최악의 경우 큐에 맵의 칸들이 저장된다.

### 핵심 포인트

- 가중치가 모두 같은 격자에서 최단 거리를 구할 때는 BFS를 사용한다.
- 큐에 다음 위치를 넣는 순간 방문 처리해야 같은 위치가 큐에 중복으로 들어가지 않는다.
- 이 문제에서는 별도의 `visited` 배열 대신 방문한 길을 `0`으로 변경하여 방문 여부를 기록할 수 있다.
- 배열을 탐색할 때는 원소에 접근하기 전에 `0 <= nx < n`, `0 <= ny < m`으로 범위를 먼저 확인한다.
- BFS가 종료될 때까지 목적지를 만나지 못했다면 도달할 수 없는 경우이므로 `-1`을 반환한다.

---

## 문제 2

- **문제명:** 타겟 넘버
- **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/43165
- **알고리즘 / 자료구조:** DFS(깊이 우선 탐색), 재귀

### 접근 및 시행착오

- 각 숫자 앞에 `+` 또는 `-`를 붙이는 두 가지 경우를 재귀 호출로 탐색했다.
- 모든 숫자를 사용했을 때 계산 결과가 `target`과 같으면 `answer`를 1 증가시키도록 작성했다.
- DFS의 탐색 구조와 종료 조건은 올바르지만, 중첩 함수 안에서 바깥 함수의 `answer`를 수정하면서 변수 스코프 오류가 발생했다.

```python
# 1차 시도 코드 (UnboundLocalError 발생)
def solution(numbers, target):
    index = 0
    answer = 0

    def dfs(index, total):
        if index == len(numbers):
            if total == target:
                answer = answer + 1
            return

        dfs(index + 1, total + numbers[index])
        dfs(index + 1, total - numbers[index])

    dfs(index, 0)
    return answer
```

`dfs()` 내부에서 `answer`에 값을 대입하면 파이썬은 `answer`를 `dfs()`의 지역 변수로 판단한다. 따라서 `answer = answer + 1`을 실행할 때 아직 값이 없는 지역 변수 `answer`를 먼저 읽게 되어 `UnboundLocalError`가 발생한다.

현재 코드의 구조를 그대로 유지하려면 `nonlocal answer`를 선언해야 한다.

```python
def solution(numbers, target):
    answer = 0

    def dfs(index, total):
        nonlocal answer

        if index == len(numbers):
            if total == target:
                answer += 1
            return

        dfs(index + 1, total + numbers[index])
        dfs(index + 1, total - numbers[index])

    dfs(0, 0)
    return answer
```

### 최종 풀이

외부 변수의 값을 변경하는 대신, 각 재귀 호출이 타겟 넘버를 만드는 경우의 수를 직접 반환하도록 구성했다.

1. 현재 숫자를 더하는 경우와 빼는 경우를 각각 재귀 호출한다.
2. 모든 숫자를 사용했을 때 `total == target`이면 `1`, 아니면 `0`을 반환한다.
3. 두 재귀 호출이 반환한 경우의 수를 더해 상위 호출로 전달한다.

```python
def solution(numbers, target):
    def dfs(index, total):
        if index == len(numbers):
            return 1 if total == target else 0

        plus = dfs(index + 1, total + numbers[index])
        minus = dfs(index + 1, total - numbers[index])

        return plus + minus

    return dfs(0, 0)
```

### 복잡도

- 시간복잡도: $O(2^N)$ — 각 숫자마다 더하기와 빼기의 두 갈래로 탐색한다.
- 공간복잡도: $O(N)$ — 재귀 호출 스택의 최대 깊이는 숫자의 개수와 같다.

### 핵심 포인트

- 각 단계에서 선택지가 두 개이고 모든 조합을 확인해야 하므로 DFS로 완전 탐색할 수 있다.
- 재귀 종료 조건에서는 모든 숫자를 사용했는지 먼저 확인한 뒤, 누적 결과와 `target`을 비교한다.
- 중첩 함수에서 바깥 함수의 변수를 수정하려면 `nonlocal` 선언이 필요하다.
- 가능하면 외부 상태를 변경하기보다 재귀 함수가 결과를 반환하게 작성하면 변수 스코프 문제를 피하고 흐름을 명확하게 만들 수 있다.
- 바깥쪽의 `index = 0`은 별도로 선언하지 않고 `dfs(0, 0)`으로 바로 시작할 수 있다.

---

## 문제 3

- **문제명:** 귤 고르기
- **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/138476
- **알고리즘 / 자료구조:** 그리디(Greedy), 해시 맵, 정렬

### 접근 및 시행착오

- 딕셔너리를 사용해 크기별 귤의 개수를 셌다.
- 선택하는 귤의 종류를 최소화하려면 개수가 많은 종류부터 담아야 한다고 판단하여, 크기별 개수를 내림차순으로 정렬했다.
- 정렬한 개수를 차례대로 더하다가 누적 개수가 `k` 이상이 되면 선택한 종류 수를 반환했다.

```python
def solution(k, tangerine):
    count = {}
    answer = 0
    sum = 0

    for t in tangerine:
        count[t] = count.get(t, 0) + 1

    counts = sorted(count.values(), reverse=True)

    for a in range(len(counts)):
        sum = sum + counts[a]
        answer = answer + 1

        if sum >= k:
            break

    return answer
```

알고리즘과 결과는 올바르지만 다음 부분은 개선할 수 있다.

1. `sum`은 파이썬 내장 함수의 이름이므로 변수로 사용하면 이후 `sum()` 함수를 호출할 수 없다. `total`과 같은 이름을 사용하는 것이 좋다.
2. `range(len(counts))`로 인덱스를 만든 뒤 `counts[a]`로 접근하기보다, 리스트의 원소를 직접 순회하면 코드가 더 명확하다.
3. 선택한 종류의 수는 `enumerate(..., start=1)`로 얻을 수 있다.

### 최종 풀이

1. 딕셔너리에 크기별 귤의 개수를 저장한다.
2. 개수만 내림차순으로 정렬한다.
3. 개수가 많은 종류부터 선택하여 `total`에 누적한다.
4. 누적 개수가 `k` 이상이 되는 순간의 선택 종류 수를 반환한다.

```python
def solution(k, tangerine):
    count = {}

    for size in tangerine:
        count[size] = count.get(size, 0) + 1

    counts = sorted(count.values(), reverse=True)
    total = 0

    for answer, frequency in enumerate(counts, start=1):
        total += frequency

        if total >= k:
            return answer
```

개수가 많은 종류부터 선택하는 것이 최적인 이유는 한 종류를 추가할 때 최대한 많은 귤을 확보해야 `k`개를 채우는 데 필요한 종류의 수가 최소가 되기 때문이다.

### 다른 사람 풀이

`collections.Counter`를 사용하면 크기별 개수를 직접 세는 코드를 간결하게 작성할 수 있다. 또한 선택한 귤 수를 누적하는 대신, 필요한 귤 수 `k`에서 선택한 개수를 차감한다.

```python
import collections


def solution(k, tangerine):
    answer = 0
    cnt = collections.Counter(tangerine)

    for v in sorted(cnt.values(), reverse=True):
        k -= v
        answer += 1

        if k <= 0:
            break

    return answer
```

두 방식은 수학적으로 동일하다.

- 직접 작성한 풀이: 선택한 귤의 수를 더해 `total >= k`인지 확인한다.
- 다른 사람 풀이: 남은 귤의 수에서 선택한 개수를 빼 `k <= 0`인지 확인한다.

함수 내부에서 `k`를 변경해도 함수 밖의 값에는 영향을 주지 않으므로 해당 코드는 올바르다. 입력값의 의미를 유지하고 싶다면 `remaining = k`와 같이 별도의 변수를 사용할 수도 있다.

### 복잡도

전체 귤의 수를 $N$, 서로 다른 귤 크기의 수를 $U$라고 할 때 다음과 같다.

- 시간복잡도: $O(N + U \log U)$ — 개수를 세는 데 $O(N)$, 종류별 개수를 정렬하는 데 $O(U \log U)$가 필요하다.
- 공간복잡도: $O(U)$ — 서로 다른 크기별 개수를 저장한다.

### 핵심 포인트

- 종류의 수를 최소화하려면 빈도가 높은 종류부터 선택하는 그리디 전략을 사용할 수 있다.
- 딕셔너리의 `get(key, 0)`을 이용하면 존재 여부를 따로 검사하지 않고 빈도를 셀 수 있다.
- `collections.Counter`를 이용하면 빈도 계산을 더 간결하게 구현할 수 있다.
- 파이썬 내장 함수인 `sum`을 변수 이름으로 사용하지 않도록 주의한다.
- 목표 수량을 채웠는지는 선택한 수량을 누적하거나 남은 수량을 차감하는 두 방식으로 표현할 수 있다.

---

# 다음 주 목표

- 목표 문제 수: 3
- 집중할 유형: 이번 주에 어려움을 겪었던 DFS와 BFS 문제를 더 많이 풀어보며 그래프 탐색에 익숙해지기
