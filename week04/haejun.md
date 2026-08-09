# 4주차 문제 풀이 인증

## 기본 정보

- 이름: 이해준
- 목표 문제 수: 3
- 실제 풀이 문제 수: 3

---

# 오답노트

## 문제 1

- **문제명:** 선인장 숨기기
- **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/468379?language=python3
- **알고리즘 / 자료구조:** 슬라이딩 윈도우, deque

### 접근 및 시행착오

난이도가 2라서 쉽게 보고 접근했는데 생각보다 어려운 문제가 나와서 당황했습니다.
대학 다닐때 유사한 문제 구조를 슬라이딩 윈도우로 풀어야 함을 배워서 알고 있었습니다.
비가 떨어지지 않는 란에는 등장하지 않는 큰 값을 넣고, 무작정 모든 후보들을 검증하여도 되지만 그때 시간 복잡도는 O(m * n * h * w)이므로 너무 오래 걸립니다.
따라서, 여러 구역에서 반복해서 계산되는 최솟값을 재사용해야 하며, 이를 2차원 슬라이딩 윈도우를 통해 최적화할 수 있습니다.

### 최종 풀이

슬라이딩 윈도우에서 h * w 구역의 최솟값을 한 번에 구하기는 어려우므로
1. 각 행에서 가로 길이 w의 최솟값
2. 세로 길이 h의 최솟값
두 가지를 따로 구한 뒤 구해봅니다.

이후 deque를 사용하여 구간 최솟값을 빠르게 구한 뒤 문제 조건에 맞추어 최솟값 중 행과 열이 작은 좌표를 선택합니다.

전체 코드는 아래와 같습니다.

```python
from collections import deque

def solution(m, n, h, w, drops):
    rain = [[len(drops)+1]*n for _ in range(m)]
    for idx, (x, y) in enumerate(drops):
        rain[x][y] = idx+1
    
    width_count = n-w+1
    horizontal = [0] * (m*width_count)
    
    # 각 행에서 w 길이의 슬라이딩 최솟값
    for r in range(m):
        dq = deque()
        result = r*width_count
        
        for c in range(n):
            cur = rain[r][c]
            while dq and rain[r][dq[-1]] >= cur:
                dq.pop()
            dq.append(c)
            
            if dq[0] <= c-w:
                dq.popleft()
                
            if c >= w -1:
                left = c-w+1
                horizontal[result + left] = rain[r][dq[0]]
    
    # 각 열에서 h 길이의 슬라이딩 윈도 최소
    best = -1
    answer = [0,0]
    
    for c in range(width_count):
        dq = deque()
        
        for r in range(m):
            cur_idx = r * width_count + c
            cur = horizontal[cur_idx]
            
            while(dq and horizontal[dq[-1]*width_count+c] >= cur):
                dq.pop()
            dq.append(r)
            
            if dq[0] <= r - h:
                dq.popleft()
            if r >= h - 1:
                top = r - h + 1
                first = horizontal[dq[0] * width_count + c]
                
                if first > best:
                    best = first
                    answer = [top, c]
                elif first == best:
                    if top < answer[0]:
                        answer = [top, c]
                    elif top == answer[0] and c < answer[1]:
                        answer = [top, c]
    
    return answer
```

### 복잡도

- 시간복잡도: O(m * n)
- 공간복잡도: O(m * n)

### 핵심 포인트

다음에 비슷한 문제를 풀 때 기억할 내용을 작성합니다.

---

## 문제 2

- **문제명:** 택배상자 꺼내기
- **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/389478
- **알고리즘 / 자료구조:** 좌표 계산

### 접근 및 시행착오

택배 상자가 한 층마다 지그재그 방향으로 쌓이므로, 꺼내려는 상자가 있는 행과 열을 구한 뒤 그 위에 존재하는 상자의 개수를 계산하려 했다.
그리고 마지막 행의 진행 방향이 짝수인지 홀수인지에 따라 정답을 계산했다.

### 최종 풀이

1. num - 1을 이용해 목표 상자의 행과 행 내부 순서를 구한다.
2. 행의 진행 방향에 따라 목표 상자의 실제 열을 구한다.
3. n - 1을 이용해 가장 위쪽 행과 마지막 행에 있는 상자의 개수를 구한다.
4. 목표 행부터 가장 위쪽 행까지의 행 개수를 계산한다.
5. 마지막 행의 목표 열에 상자가 없다면 정답에서 1을 뺀다.

```python
def solution(n, w, num):
    target_row, offset = divmod(num - 1, w)
    if target_row % 2 == 0:
        target_col = offset
    else:
        target_col = w - 1 - offset

    top_row, top_offset = divmod(n - 1, w)
    answer = top_row - target_row + 1
    top_count = top_offset + 1

    if top_count < w:
        if top_row % 2 == 0:
            top_box = target_col < top_count
        else:
            top_box = target_col >= w - top_count

        if not top_box:
            answer -= 1

    return answer
```

### 복잡도

- 시간복잡도: O(1)
- 공간복잡도: O(1)

전체 상자를 직접 배치하지 않고, 목표 상자와 마지막 상자의 좌표만 계산한다.

### 핵심 포인트



---

## 문제 3

- **문제명:** 중요한 단어를 스포 방지
- **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/468370
- **알고리즘 / 자료구조:** 문자열 파싱, 집합

### 접근 및 시행착오

중요한 점은 단어의 개수이므로 클릭 과정을 시뮬레이션 할 필요가 없다는 점이다.
어떤 단어가 중요한 단어로 세어지려면 두 조건을 만족해야 한다.
1. 해당 단어의 문자 중 하나 이상이 스포 구간에 포함 되어야 함.
2. 같은 단어가 스포 구간에 전혀 포함되지 않은 일반 단어로 등장하면 안 됨.

따라서 최종 답은 (스포 단어 집합 - 일반 단어 집합) 을 계산하면 된다.
당연하지만 귀찮은 문자열 파서를 구현해야 한다..... ㅠㅠ

### 최종 풀이


```python
def solution(message, spoiler_ranges):
    n = len(message)

    is_spoiler = [False] * n

    for start, end in spoiler_ranges:
        for i in range(start, end + 1):
            is_spoiler[i] = True

    spoiler_words = set()
    normal_words = set()

    i = 0

    while i < n:
        if message[i] == " ":
            i += 1
            continue

        j = i

        while j < n and message[j] != " ":
            j += 1

        word = message[i:j]

        if any(is_spoiler[i:j]):
            spoiler_words.add(word)
        else:
            normal_words.add(word)

        i = j

    return len(spoiler_words - normal_words)
```

### 복잡도

메세지 길이를 n이라고 할때
- 시간복잡도: O(n)
- 공간복잡도: O(n)

### 핵심 포인트



---

# 다음 주 목표

- 목표 문제 수:
- 집중할 유형: