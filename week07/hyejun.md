> [!NOTE]
> 이 파일을 복사해서 `week0N/{영문이름}.md`로 저장한 뒤 작성해주세요.
> AI나 블로그 풀이를 그대로 복사하지 않고 본인의 언어로 정리해주세요.

# 7주차 문제 풀이 인증

## 기본 정보

- 이름: 구혜준
- 목표 문제 수: 3
- 실제 풀이 문제 수: 3

---

# 오답노트

## 문제 1

- **문제명:** 할인 행사
- **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/131127
- **알고리즘 / 자료구조:** 해시, 슬라이딩 윈도우, Counter

### 접근 및 시행착오

회원가입 기간이 10일로 정해져 있으므로 `discount`에서 연속된 10일씩 확인하면 된다고 생각했다. 처음에는 원하는 상품마다 개수를 직접 세려고 했지만, `Counter`를 사용하면 원하는 상품 구성과 10일간의 할인 상품 구성을 바로 비교할 수 있었다.

### 최종 풀이

1. `want`와 `number`를 묶어 원하는 상품별 수량을 딕셔너리로 만든다.
2. `discount`에서 연속된 10개 상품의 개수를 센다.
3. 원하는 상품 구성과 같으면 정답을 1 증가시킨다.
4. 가능한 모든 시작 날짜를 확인한다.

```python
from collections import Counter

def solution(want, number, discount):
    answer = 0
    wanted = dict(zip(want, number))

    for start in range(len(discount) - 9):
        if Counter(discount[start:start + 10]) == wanted:
            answer += 1

    return answer
```

### 복잡도

- 시간복잡도: `O(N)`
- 공간복잡도: `O(K)`

### 핵심 포인트

확인해야 하는 기간이 10일로 고정되어 있으므로 연속된 10개씩 잘라 상품별 개수를 비교하면 된다. 상품의 개수를 비교할 때는 `Counter`를 사용할 수 있다.

---

## 문제 2

- **문제명:** 괄호 회전하기
- **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/76502
- **알고리즘 / 자료구조:** 스택, 문자열

### 접근 및 시행착오

여는 괄호와 닫는 괄호의 개수만 같다고 올바른 괄호 문자열이 되는 것은 아니다. 괄호의 순서와 종류도 맞아야 하므로 각 회전 결과를 스택으로 검사했다.

### 최종 풀이

1. 문자열을 왼쪽으로 한 칸씩 회전한다.
2. 여는 괄호가 나오면 스택에 넣는다.
3. 닫는 괄호가 나오면 스택의 마지막 괄호와 짝이 맞는지 확인한다.
4. 짝이 맞지 않거나 검사가 끝난 뒤 스택에 괄호가 남으면 실패로 처리한다.
5. 올바른 문자열이 된 회전 횟수를 센다.

```python
def solution(s):
    answer = 0
    pairs = {')': '(', ']': '[', '}': '{'}

    for x in range(len(s)):
        rotated = s[x:] + s[:x]
        stack = []
        valid = True

        for bracket in rotated:
            if bracket in '([{':
                stack.append(bracket)
            else:
                if not stack or stack[-1] != pairs[bracket]:
                    valid = False
                    break
                stack.pop()

        if valid and not stack:
            answer += 1

    return answer
```

### 복잡도

- 시간복잡도: `O(N²)`
- 공간복잡도: `O(N)`

### 핵심 포인트

괄호는 최근에 나온 여는 괄호부터 닫혀야 하므로 스택을 사용한다. 스택이 비어 있는데 닫는 괄호가 나오거나 괄호의 종류가 다르면 바로 실패 처리할 수 있다.

---

## 문제 3

- **문제명:** n² 배열 자르기
- **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/87390
- **알고리즘 / 자료구조:** 인덱스 계산, 수학

### 접근 및 시행착오

처음에는 `n × n` 배열을 직접 만들려고 했지만, `n`이 최대 `10⁷`이라 전체 배열을 저장할 수 없다. 따라서 `left`부터 `right`까지 필요한 값만 계산해야 했다.

1차원 인덱스가 `i`일 때 행은 `i // n`, 열은 `i % n`이다. 해당 위치의 값은 행과 열 중 큰 값에 1을 더한 값이라는 규칙을 사용했다.

### 최종 풀이

1. `left`부터 `right`까지의 인덱스만 순회한다.
2. 각 인덱스의 행과 열을 구한다.
3. `max(행, 열) + 1`을 결과 배열에 추가한다.

```python
def solution(n, left, right):
    answer = []

    for index in range(left, right + 1):
        row = index // n
        column = index % n
        answer.append(max(row, column) + 1)

    return answer
```

### 복잡도

- 시간복잡도: `O(right - left + 1)`
- 공간복잡도: `O(right - left + 1)`

### 핵심 포인트

배열의 크기가 매우 크면 전체 배열을 만들지 않고 필요한 구간의 값만 계산해야 한다. 1차원 인덱스에서 행은 나눗셈, 열은 나머지 연산으로 구할 수 있다.

---

# 다음 주 목표

- 목표 문제 수: 3
- 집중할 유형: DP 기초 / 심화