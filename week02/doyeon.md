# 2주차 문제 풀이 인증

## 기본 정보

- 이름: 박도연
- 목표 문제 수: 3
- 실제 풀이 문제 수: 3

## 푼 문제 목록

| 번호 | 문제 이름 | 난이도 | 링크 | 풀이 여부 |
| --- | --- | --- | --- | --- |
| 1 | 올바른 괄호 | Lv.2 | https://school.programmers.co.kr/learn/courses/30/lessons/12909 | O |
| 2 | 기능개발 | Lv.2 | https://school.programmers.co.kr/learn/courses/30/lessons/42586 | O |
| 3 | 의상 | Lv.2 | https://school.programmers.co.kr/learn/courses/30/lessons/42578 | O |

---

# 오답노트

## 문제 1

### 문제 정보

- 문제명: 올바른 괄호
- 문제 링크: https://school.programmers.co.kr/learn/courses/30/lessons/12909
- 사용한 알고리즘 / 자료구조: 스택 (Stack), 카운터 변수

### 처음 접근 방식

- 카운터 변수(`pair`)를 두어 문자열을 순회하면서 여는 괄호 `(`를 만나면 `pair += 1`, 닫는 괄호 `)`이면서 `pair > 0`일 때는 `pair -= 1`을 수행하려 했다.
- 순회가 끝났을 때 `pair == 0`이면 올바른 괄호(`True`), 아니면 `False`를 반환하도록 작성했다.

```python
# 1차 시도 코드
def solution(s):
    pair = 0
    for i in s:
        if i == ')' and pair > 0:
            pair -= 1
        else:
            pair += 1
    if pair == 0:
        return True
    else:
        return False
```

### 어려웠던 지점 (오답 이유)

- `if i == ')' and pair > 0:` 조건을 사용하면서, `pair == 0`일 때 등장하는 잘못된 닫는 괄호 `)`가 `else` 블록으로 넘어가 오히려 **`pair`가 1 증가하는 문제**가 발생했다.
- 이로 인해 `s = ")()())"` 처럼 앞부분에 잘못된 닫는 괄호가 포함되어 있어도, 나중에 나온 닫는 괄호와 상쇄되어 최종 `pair == 0`이 되어 **잘못해서 `True`를 반환하는 반례**가 발생했다.

### 해결 방법 및 개선 코드

#### 방법 1: 스택(Stack)을 활용한 정석 풀이
- `(` 문자는 스택(`st`)에 `append()`하고, `)` 문자를 만나면 스택에서 `pop()`한다.
- `)` 문자를 만났을 때 스택이 이미 비어있다면(`if not st:`) 대응하는 여는 괄호가 없는 것이므로 즉시 `False`를 반환한다.
- `try-except IndexError` 예외 처리 구문으로 작성할 수도 있으나, `if not st:` 조건문으로 미리 빈 스택을 확인하는 것이 파이썬 관례(Pythonic Style)상 더 직관적이고 빠르다.

```python
def solution(s):
    st = []
    for c in s:
        if c == '(':
            st.append(c)
        elif c == ')':
            if not st:  # try-except IndexError 대신 직관적인 조건문 사용
                return False
            st.pop()

    return len(st) == 0
```

#### 방법 2: 카운터 변수 하나로 최적화 ($O(1)$ 공간복잡도)
- 괄호의 종류가 `(`와 `)` 한 가지만 존재하므로, 스택 리스트를 생성하지 않고 정수 변수(`count`) 하나로 수식을 검증한다.
- `count`가 음수(`< 0`)가 되는 순간 조기 리턴(Early Return)하여 시간 및 메모리를 절약한다.

```python
def solution(s):
    count = 0
    for c in s:
        if c == '(':
            count += 1
        elif c == ')':
            count -= 1
            if count < 0:  # 닫는 괄호가 더 많아진 순간 즉시 종료
                return False
    return count == 0
```

> **💡 핵심 인사이트**
> 
> - **단일 괄호 문제**: `count` 카운터 변수 하나만 사용하는 것이 $O(1)$ 공간 복잡도로 가장 효율적이다.
> - **다중 괄호 문제 (`()`, `{}`, `[]`)**: 괄호 종류마다 짝을 맞춰야 하고 후입선출(LIFO) 특성을 가져야 하므로 반드시 **스택(Stack)** 자료구조를 사용해야 한다.

### 시간복잡도 / 공간복잡도

- **스택 풀이**: 시간복잡도 $O(N)$, 공간복잡도 $O(N)$
- **카운터 풀이**: 시간복잡도 $O(N)$, 공간복잡도 $O(1)$

### 새롭게 알게 된 점

- **스택(Stack) 핵심 메서드 및 개념**:
  - `append()` / `push()`: 데이터를 스택의 맨 위에 임시 보관(Stash)하듯 쌓는 역할. (파이썬 리스트에서는 `append()` 사용)
  - `pop()`: 가장 최근에 넣은(가장 위에 있는) 데이터를 추출하여 제거하는 역할. 빈 스택에서 실행 시 `IndexError`가 발생하므로 사용 전 검증 필수.
- 괄호 검사 문제는 단순히 괄호의 전체 개수뿐만 아니라 **등장 순서**와 **중간 상태(닫는 괄호가 먼저 나오는지 여부)**가 핵심이라는 점을 배웠다.
- 스택의 빈 상태 예외 처리 시 `try ... except IndexError` 구문보다는 `if not stack:` 형태의 명시적 예외 검사가 훨씬 가독성이 좋고 표준적이라는 것을 깨달았다.

---

## 문제 2

### 문제 정보

- 문제명: 기능개발
- 문제 링크: https://school.programmers.co.kr/learn/courses/30/lessons/42586
- 사용한 알고리즘 / 자료구조: 큐 (`collections.deque`), 시뮬레이션

### 처음 접근 방식

- 먼저 완성되어야 하는 작업이 앞쪽에 위치하고, 앞의 작업이 완료(100% 이상)되어야만 뒤의 완성된 작업들도 함께 배포할 수 있는 구조이므로 **FIFO(선입선출) 특성을 갖는 큐(Queue)**를 사용했다.
- `progresses`와 `speeds`를 각각 `deque`에 담아, 매 반복(1일 흐름)마다 남아있는 모든 작업에 속도를 더해주는 일 단위 시뮬레이션 방식을 떠올렸다.
- 맨 앞 작업(`queue_p[0]`)이 100% 이상이 되면 `popleft()`하면서 함께 배포 가능한 연속된 작업 개수(`count`)를 세어 `answer`에 추가했다.

### 작성 코드

```python
from collections import deque

def solution(progresses, speeds):
    answer = []
    queue_p = deque(progresses)
    queue_s = deque(speeds)
    
    while queue_p:
        count = 0
        # 1. 하루치 작업 진도 수행
        for i in range(len(queue_p)):
            queue_p[i] += queue_s[i]

        # 2. 맨 앞 작업이 완료(>= 100)된 동안 연속 popleft()하여 동시 배포 개수 계산
        for i in range(len(queue_p)):
            if queue_p[0] >= 100:
                queue_p.popleft()
                queue_s.popleft()
                count += 1
            else:
                break
                
        if count > 0:
            answer.append(count)
            
    return answer
```

### 어려웠던 지점 및 피드백

1. **시뮬레이션 방식의 시간 복잡도**:
   - 작성한 코드는 정답을 올바르게 구하지만, 작업 완료까지 걸리는 일수만큼 `while` 루프가 반복된다.
   - 매일 남아있는 $N$개 작업 전체에 속도를 더해주므로 시간 복잡도는 $O(\text{max\_days} \times N)$이 된다. (문제 조건에서 $N \le 100$이라 통과 가능하지만, 작업 수가 커지면 비효율적일 수 있음)
2. **`popleft()` 시 인덱스 조심**:
   - `queue_p.popleft()`를 실행하면 큐의 길이가 줄어들고 index가 앞으로 당겨지므로, 조건문에서 `queue_p[i]`가 아닌 항시 맨 앞 원소인 `queue_p[0]`을 검사해야 정상적으로 작동한다.

### 해결 방법 및 개선 코드 ($O(N)$ 최적화 풀이)

매일 1씩 진도를 올려 시뮬레이션하는 대신, **각 기능이 완성되기까지 필요한 남은 일수를 수학적으로 먼저 계산**하면 $O(N)$ 시간 복잡도로 대폭 최적화할 수 있다.

- 필요한 일수 계산 공식: $\lceil (100 - \text{progress}) / \text{speed} \rceil$
- 파이썬 수식: `(100 - p + s - 1) // s` 또는 `math.ceil((100 - p) / s)`

```python
import math

def solution(progresses, speeds):
    answer = []
    # 각 작업별 완성까지 걸리는 남은 일수 계산
    days = [math.ceil((100 - p) / s) for p, s in zip(progresses, speeds)]
    
    current_max_day = days[0]
    count = 0
    
    for day in days:
        if day <= current_max_day:
            count += 1
        else:
            answer.append(count)
            current_max_day = day
            count = 1
            
    answer.append(count)  # 마지막 배포 그룹 추가
    return answer
```

### 참고할 만한 다른 풀이

- **1차원 순회 숏코딩 풀이**:
  - `Q = [[배포기준일, 개수]]` 형태의 2차원 리스트(스택/큐 개념)를 활용하여 단 한 번의 순회로 완료한 풀이.
  - `Q[-1][0] < day`이면 새 배포 그룹(`[day, 1]`)을 `append`하고, 이하이면 기존 그룹의 개수(`Q[-1][1] += 1`)를 증가시킨 후 `[q[1] for q in Q]`로 개수만 추출하는 기발한 풀이법이다.


```python
def solution(progresses, speeds):
    Q = []
    for p, s in zip(progresses, speeds):
        # -((p-100)//s) 로 math.ceil 없이 올림 계산
        if len(Q) == 0 or Q[-1][0] < -((p - 100) // s):
            Q.append([-((p - 100) // s), 1])
        else:
            Q[-1][1] += 1
    return [q[1] for q in Q]
```

### 시간복잡도 / 공간복잡도

- **시뮬레이션 풀이 (작성 코드)**: 시간복잡도 $O(\text{max\_days} \times N)$, 공간복잡도 $O(N)$
- **날짜 미리 계산 풀이 (최적화)**: 시간복잡도 $O(N)$, 공간복잡도 $O(N)$

### 새롭게 알게 된 점

- **큐(`collections.deque`) 핵심 활용법 및 메서드**:
  - `popleft()`: 큐의 맨 앞(head) 원소를 제거하고 추출하는 $O(1)$ 연산. (일반 리스트의 `pop(0)`은 $O(N)$이 소요되므로 큐 사용 시 필수)
  - **인덱스를 통한 직접 조회 및 수정**: `deque` 객체는 일반 리스트처럼 인덱싱과 원소 값 수정이 가능하다. (예: `queue = deque([1, 2, 3])` 일 때 `queue[0] = 10` 으로 직접 수정 가능 `--> deque([10, 2, 3])`)
- **파이썬 음수 몫 연산(`//`)을 이용한 올림 테크닉**:
  - `math.ceil((100 - p) / s)` 대신 `-((p - 100) // s)` 공식을 사용하면 `import math` 없이 정수 연산만으로 올림(Ceil) 효과를 낼 수 있다.
  - 원리: 파이썬의 `//` 연산자는 내림(Floor)을 수행하므로, 음수로 만들어 내림한 뒤 다시 부호를 뒤집으면 $-\lfloor \frac{p-100}{s} \rfloor = \lceil \frac{100-p}{s} \rceil$ 가 된다.
- 시뮬레이션 방식보다 **남은 날짜를 수식으로 계산하여 그룹화**하는 접근법이 $O(N)$으로 훨씬 빠르고 직관적이라는 점을 알게 되었다.

---

## 문제 3

### 문제 정보

- 문제명: 의상
- 문제 링크: https://school.programmers.co.kr/learn/courses/30/lessons/42578
- 사용한 알고리즘 / 자료구조: 해시 (Hash Map / Dictionary), 조합 (Combinatorics)

### 처음 접근 방식

- 입력 데이터 `clothes`를 딕셔너리로 잘못 판단하고 `for value in clothes.values():` 구문을 사용하여 각 의상의 종류별 개수를 세려고 시도했다.
- 의상 종류별 개수를 구한 뒤, 가능한 서로 다른 옷의 조합 수(경우의 수)를 계산하려 했다.

```python
# 1차 시도 코드 (오류 발생)
def solution(clothes):
    kinds_count = {}
    answer = 1

    for value in clothes.values():  # AttributeError 발생 (clothes는 딕셔너리가 아닌 2차원 리스트)
        kinds_count[value] = kinds_count.get(value, 0) + 1
    
    for i in kinds_count.values():
        answer *= (i + 1)
        
    return answer - 1
```

### 어려웠던 지점 (오답 이유)

1. **입력 자료형 오해 (`AttributeError`)**:
   - `clothes`의 타입은 딕셔너리가 아니라 `[["yellow_hat", "headgear"], ["blue_sunglasses", "eyewear"], ...]` 형태의 **2차원 리스트(List of Lists)**였다.
   - 리스트 객체에는 `.values()` 메서드가 존재하지 않아 `AttributeError`가 발생했다.
   - 2차원 리스트의 각 원소 `[의상이름, 의상종류]`를 순회하려면 `for key, value in clothes:` (또는 `for name, kind in clothes:`) 형태로 순회해야 했다.

2. **경우의 수 수학 공식 도출**:
   - 처음에는 각 카테고리별 선택지를 조합하는 공식 $(a+1)(b+1)(c+1) - 1$을 생각하지 못했다.
   - **공식의 원리**:
     - 각 의상 종류별로 의상이 $a$개 있다면, 선택 가능한 경우의 수는 **$a$개 중 하나를 선택하는 경우 + 해당 종류를 착용하지 않는 경우 1가지 = $(a+1)$가지**이다.
     - 모든 의상 종류에 대해 독립적이므로 각 종류의 선택 가짓수를 모두 곱해준다: $(a+1) \times (b+1) \times \dots$
     - 하루에 최소 한 개의 의상은 입어야 하므로, **모든 종류를 착용하지 않는 경우(1가지)**를 최종 결과에서 빼주어야 한다: $- 1$

### 최종 풀이 코드

```python
def solution(clothes):
    kinds_count = {}
    answer = 1

    # 1. 2차원 리스트를 순회하며 의상 종류(value)별 개수 카운팅
    for key, value in clothes:
        kinds_count[value] = kinds_count.get(value, 0) + 1
    
    # 2. (각 종류별 의상 수 + 1) 을 곱함 (안 입는 경우의 수 +1)
    for i in kinds_count.values():
        answer *= (i + 1)
        
    # 3. 모두 안 입은 1가지 경우를 제외하고 반환
    return answer - 1
```

### 참고할 만한 다른 풀이

#### 1. 초기값을 2로 설정하는 직관적인 풀이 (안 입는 경우 미리 포함)
- 딕셔너리에 새로운 의상 종류가 등장할 때 1 대신 **2(의상 1개 + 안 입는 경우 1개)**로 초기화하는 아이디어다.
- 나중에 곱셈 연산 시 `num + 1`을 하지 않고 바로 `cnt *= num`을 할 수 있어 매우 직관적이다.

```python
def solution(clothes):
    clothes_type = {}

    for c, t in clothes:
        if t not in clothes_type:
            clothes_type[t] = 2  # 의상 1개 + 안 입는 경우 1개 = 2로 초기화
        else:
            clothes_type[t] += 1

    cnt = 1
    for num in clothes_type.values():
        cnt *= num

    return cnt - 1
```

#### 2. `collections.Counter` & `functools.reduce` 숏코딩 풀이
- `collections.Counter`와 `reduce` 함수를 활용하면 한 줄로도 계산이 가능하다.

```python
from collections import Counter
from functools import reduce

def solution(clothes):
    # Counter로 종류별 개수를 세고, reduce로 (count + 1) 들을 누적 곱한 뒤 - 1
    return reduce(lambda x, y: x * (y + 1), Counter([kind for name, kind in clothes]).values(), 1) - 1
```

### 시간복잡도 / 공간복잡도

- **시간복잡도**: $O(N)$ ($N$은 전체 의상의 개수. 의상 리스트 1회 순회 후 딕셔너리 값 순회)
- **공간복잡도**: $O(K)$ ($K$는 서로 다른 의상 종류의 개수)

### 새롭게 알게 된 점

- **`dict.get(key, default)` 메서드를 활용한 한 줄 카운팅**:
  - 딕셔너리에 특정 키가 없을 때 `KeyError` 없이 기본값(default)을 반환하는 메서드다.
  - `kinds_count[value] = kinds_count.get(value, 0) + 1` 패턴을 사용하면, 키가 처음 등장할 때는 `0 + 1 = 1`로 초기화되고 이미 존재할 때는 기존 값에 `+ 1`이 되어 별도의 `if key not in dict:` 조건문 없이 한 줄로 빈도수를 셀 수 있다.
- **카운팅 초기값 활용 아이디어**: 딕셔너리 카운팅 시 '안 입는 경우 1가지'를 미리 고려하여 초기값을 1이 아닌 **2**로 시작하면, 추후 곱셈 과정에서 `+ 1`을 할 필요 없이 바로 누적 곱을 구할 수 있다.
- **경우의 수 조합 공식**: 여러 종류 중 최소 하나를 조합하여 착용해야 할 때는 `(각 종류별 개수 + 1)들의 곱 - 1` 공식을 활용한다.
- **입력 타입 확인**: 입력값 `clothes`가 딕셔너리가 아닌 `list` 형태임에 유의해야 한다.


