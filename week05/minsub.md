## 하나금융티아이 신입 공채 코딩테스트 회고

### 1. 개요

* 시험 구성: 알고리즘 3문제 + SQL 3문제
* 시험 시간: 2시간
* 플랫폼: 프로그래머스
* 작성 목적: 금융권 코딩테스트에서 출제된 SQL 혼합형 문제를 복기하고, 유사 유형을 다음 주차 학습 문제로 연결하기 위함

---

### 2. 코딩테스트 전체 회고

이번 하나금융티아이 코딩테스트는 알고리즘 3문제와 SQL 3문제로 구성되어 있었다.

알고리즘은 타대기업 대비 쉬운 편이었으나,

모든 SQL 문제가 실제 은행 앱이나 내부 업무 시스템에서 발생할 수 있는 상황을 기반으로 출제되었다는 점에서 재밌고, 어려웠다. 

단순히 문법을 묻는 문제가 아니라, 은행 서비스 안에서 발생하는 거래, 처리 상태, 정책 조건, 결측 데이터 복원과 같은 상황을 코드나 SQL로 모델링하는 문제들이었다.

---

### 3. SQL 4번 회고

### 문제 유형

* 예상 유형: JOIN + GROUP BY + 집계
* 체감 난이도: 쉬움
* 풀이 여부: 해결

10분 내로 풀어서, 정확한 문제 내용은 자세히 기억나지 않는다.

---

### 기존 접근 방식

JOIN을 통해 기준 테이블과 기록 테이블을 연결한 뒤, 특정 ID 또는 코드 기준으로 `GROUP BY`를 수행하고 `SUM()`으로 전체 금액이나 수량을 계산하는 방식으로 접근했다.

예상되는 풀이 구조는 다음과 같다.

```sql
SELECT
    p.PRODUCT_CODE,
    p.PRICE * SUM(s.SALES_AMOUNT) AS SALES
FROM PRODUCT p
JOIN OFFLINE_SALE s
    ON p.PRODUCT_ID = s.PRODUCT_ID
GROUP BY
    p.PRODUCT_CODE,
    p.PRICE
ORDER BY
    SALES DESC,
    p.PRODUCT_CODE ASC;
```
---

### 4. SQL 5번 회고

### 문제 유형

* 예상 유형: 상태 로그 기반 완료 여부 판별
* 사용 개념: JOIN, CASE WHEN, GROUP BY, 조건부 집계
* 체감 난이도: 중상(프로그래머스 SQL LV4 ~ LV5) 
* 풀이 여부: 해결

문제는 은행 업무 처리 과정에서 발생하는 상태 로그를 기반으로, 특정 처리가 완료되었는지 판별하는 형태였다.

상태는 대략 다음과 같이 주어졌던 것으로 기억한다.

```text
START
PROGRESS
END
```

조건은 다음과 같았다.

```text
START는 한 번만 발생
END는 한 번만 발생
PROGRESS는 여러 번 발생 가능
START, PROGRESS, END가 모두 발생해야 처리 완료로 간주
```

즉, 단순히 특정 상태 하나가 있는지를 보는 문제가 아니라, 하나의 처리 ID 또는 상품 ID 안에서 여러 상태 로그가 모두 존재하는지를 판단해야 하는 문제였다.

---

#### 기존 접근 방식

처음에는 상품 또는 처리 테이블과 상태 기록 테이블을 JOIN한 뒤, 상태별로 `CASE WHEN`을 사용해 각각의 존재 여부를 나누려고 했다.

복기하면 다음과 같은 방향이었다.

```sql
SELECT
    process_id,
    CASE WHEN status = 'START' THEN 1 ELSE 0 END AS has_start,
    CASE WHEN status = 'PROGRESS' THEN 1 ELSE 0 END AS has_progress,
    CASE WHEN status = 'END' THEN 1 ELSE 0 END AS has_end
FROM process_log;
```

이후 바깥 쿼리에서 `has_start * has_progress * has_end`가 1이면 완료로 처리하는 식으로 접근했다.

다만 이 방식은 행 단위로 상태를 나누는 방식이라, 상태 로그가 여러 행으로 존재할 때 바로 원하는 결과가 나오지 않았다. 각 행에는 보통 하나의 상태만 존재하기 때문에, 한 행 안에서 `START`, `PROGRESS`, `END`가 동시에 1이 되기 어렵다.

그래서 시험 중에는 가중치를 부여하는 방식으로 우회했다.


그리고 합산 결과가 특정 기준 이상이면 완료된 것으로 간주하는 방식으로 제출했다.

이 방식은 테스트케이스는 통과했지만, `PROGRESS`가 매우 많이 반복되는 경우에는 의도와 다르게 완료로 판정될 가능성이 있다. 그래서 아래와 같이 개선했다.

---

#### 개선 풀이

이 문제는 각 상태가 한 번이라도 존재하는지를 보는 것이 중요하다.

따라서 `MAX(CASE WHEN ...)`를 사용하는 것이 더 안전하다.

```sql
SELECT
    process_id,
    CASE
        WHEN MAX(CASE WHEN status = 'START' THEN 1 ELSE 0 END) = 1
         AND MAX(CASE WHEN status = 'PROGRESS' THEN 1 ELSE 0 END) = 1
         AND MAX(CASE WHEN status = 'END' THEN 1 ELSE 0 END) = 1
        THEN 1
        ELSE 0
    END AS is_completed
FROM process_log
GROUP BY process_id;
```

만약 상품 테이블과 처리 기록 테이블을 조인해야 한다면 다음과 같은 구조가 된다.

```sql
SELECT
    p.product_id,
    CASE
        WHEN MAX(CASE WHEN l.status = 'START' THEN 1 ELSE 0 END) = 1
         AND MAX(CASE WHEN l.status = 'PROGRESS' THEN 1 ELSE 0 END) = 1
         AND MAX(CASE WHEN l.status = 'END' THEN 1 ELSE 0 END) = 1
        THEN 1
        ELSE 0
    END AS is_completed
FROM product p
JOIN process_log l
    ON p.product_id = l.product_id
GROUP BY
    p.product_id;
```

또는 상태 종류가 정확히 세 가지이고, 이 세 가지가 모두 존재하는지만 보면 된다면 `COUNT(DISTINCT ...)` 방식도 가능하다.

```sql
SELECT
    process_id,
    CASE
        WHEN COUNT(DISTINCT CASE
            WHEN status IN ('START', 'PROGRESS', 'END') THEN status
        END) = 3
        THEN 1
        ELSE 0
    END AS is_completed
FROM process_log
GROUP BY process_id;
```

---

#### 정리

상태 로그 문제에서는 다음 기준으로 접근해야 한다는 것을 다시 정리했다.

```text
상태 A, B, C가 모두 있어야 한다
→ 행 단위 조건이 아니라 그룹 단위 존재 여부 문제

특정 상태가 한 번이라도 있었는지 확인한다
→ MAX(CASE WHEN 조건 THEN 1 ELSE 0 END)

상태 종류가 모두 존재하는지 확인한다
→ COUNT(DISTINCT CASE WHEN 상태 IN (...) THEN 상태 END)

반복 가능한 상태가 있다
→ 단순 SUM 기준이나 가중치 합산은 위험할 수 있다
```

---

### 5. SQL 6번 회고 

### 문제 유형

* 예상 유형: 상품별 정책 후보 생성 + 우선순위 기반 정책 채택
* 사용 개념: JOIN, 서브쿼리 또는 CTE, GROUP BY, 조건부 집계, ROW_NUMBER
* 풀이 여부: 미해결
* 체감 난이도: 상 (프로그래머스 SQL 기준 Lv5)

6번은 특정 조건을 만족하는 정책을 찾고, 상품별로 최종 채택 정책을 결정하는 문제였다.

문제에서 중요한 점은 `상품 ID`와 `정책 ID`의 관계였다.

```text
상품 ID + 정책 ID 조합은 중복되지 않는다.
상품 ID는 여러 정책 후보를 가질 수 있다.
정책 ID도 여러 상품에 등장할 수 있다.
```

즉, 최종 결과는 정책 ID별로 하나씩 출력하는 문제가 아니라, **상품 ID별로 선택 가능한 정책 후보 중 하나를 채택하는 문제**에 가까웠다.

정책 후보가 여러 개 존재할 경우 채택 기준은 다음과 같았다.

```text
1순위: 금액이 높은 정책
2순위: 금액이 같다면 정책 ID가 낮은 정책
```

따라서 이 문제는 단순히 `ORDER BY amount DESC, policy_id ASC`로 정렬해 출력하는 문제가 아니라, **상품 ID별로 우선순위가 가장 높은 정책 1개를 선택하는 문제**였다.

---

#### 기존 접근 방식

처음에는 두 테이블을 JOIN한 뒤, 조건을 만족하는 정책 후보를 서브쿼리로 만들고, 그 결과를 `GROUP BY`하거나 정렬하는 방식으로 접근했다.

대략 다음과 같은 구조였다.

```sql
SELECT
    product_id,
    policy_id,
    amount
FROM (
    SELECT
        p.product_id,
        p.policy_id,
        p.amount
    FROM policy p
    JOIN record r
        ON ...
    WHERE
        조건1
        AND 조건2
        AND 조건3
) t
GROUP BY
    product_id,
    policy_id
ORDER BY
    amount DESC,
    policy_id ASC;
```

하지만 이 방식만으로는 상품별 최종 채택 정책을 정확히 고를 수 없다.

복기해보면, 이 문제의 핵심은 다음 두 단계를 분리하는 것이었다.

```text
1단계: 상품 ID + 정책 ID 조합별로 선택 가능한 후보를 만든다.
2단계: 상품 ID별로 후보 정책 중 우선순위가 가장 높은 정책을 1개 채택한다.
```

`GROUP BY`는 상품별 최종 선택을 위해 바로 쓰는 것이 아니라, 먼저 **상품-정책 조합 단위의 후보를 정리하기 위해 필요했을 가능성**이 높다.

---

#### 개선했으면 좋았을 풀이

가장 안정적인 구조는 `상품-정책 조합별 후보 생성` 후 `ROW_NUMBER()`로 상품별 1개 정책을 채택하는 방식이다.

```sql
WITH candidates AS (
    SELECT
        p.product_id,
        p.policy_id,
        p.amount
    FROM policy p
    JOIN record r
        ON ...
    WHERE
        조건1
        AND 조건2
        AND 조건3
    GROUP BY
        p.product_id,
        p.policy_id,
        p.amount
),
ranked AS (
    SELECT
        product_id,
        policy_id,
        amount,
        ROW_NUMBER() OVER (
            PARTITION BY product_id
            ORDER BY amount DESC, policy_id ASC
        ) AS rn
    FROM candidates
)
SELECT
    product_id,
    policy_id
FROM ranked
WHERE rn = 1
ORDER BY product_id;
```

여기서 핵심은 다음 부분이다.

```sql
ROW_NUMBER() OVER (
    PARTITION BY product_id
    ORDER BY amount DESC, policy_id ASC
)
```

상품 ID 별로 정책 후보를 나눠서 -> 각 상품 안의 금액이 높은 정책을 먼저 두고 -> 금액이 같으면 정책 ID가 낮은 정책을 찾고 -> 그 중 rn이 1인 것만 채택

---

#### 조건이 그룹 단위로 판단되어야 하는 경우

만약 문제의 조건들이 한 행에서 동시에 만족되는 조건이 아니라, JOIN 결과를 상품-정책 조합 단위로 모았을 때 만족 여부를 판단해야 하는 조건이었다면 조건부 집계가 필요하다.

이 경우에는 다음과 같은 구조가 더 적절하다.

```sql
WITH candidates AS (
    SELECT
        p.product_id,
        p.policy_id,
        p.amount,

        MAX(CASE WHEN 조건1 THEN 1 ELSE 0 END) AS cond1_ok,
        MAX(CASE WHEN 조건2 THEN 1 ELSE 0 END) AS cond2_ok,
        MAX(CASE WHEN 조건3 THEN 1 ELSE 0 END) AS cond3_ok

    FROM policy p
    JOIN record r
        ON ...
    GROUP BY
        p.product_id,
        p.policy_id,
        p.amount
),
filtered AS (
    SELECT
        product_id,
        policy_id,
        amount
    FROM candidates
    WHERE cond1_ok = 1
      AND cond2_ok = 1
      AND cond3_ok = 1
),
ranked AS (
    SELECT
        product_id,
        policy_id,
        amount,
        ROW_NUMBER() OVER (
            PARTITION BY product_id
            ORDER BY amount DESC, policy_id ASC
        ) AS rn
    FROM filtered
)
SELECT
    product_id,
    policy_id
FROM ranked
WHERE rn = 1
ORDER BY product_id;
```

이 구조에서는 역할이 명확히 나뉜다.

```text
candidates:
상품-정책 조합별로 조건 만족 여부를 만든다.

filtered:
조건을 모두 만족하는 정책 후보만 남긴다.

ranked:
상품별로 정책 후보에 우선순위를 매긴다.

최종 SELECT:
상품별 1순위 정책만 채택한다.
```

---

#### 왜 GROUP BY 기준이 중요했는가

이 문제에서 `GROUP BY product_id`만 하면 안 되는 이유는, 상품 하나에 여러 정책 후보가 있을 수 있기 때문이다.

```text
product_id = A
policy_id = P1
policy_id = P2
policy_id = P3
```

이 상황에서 `GROUP BY product_id`만 하면 SQL은 여러 정책 중 어떤 정책 ID를 가져와야 하는지 명확히 알 수 없다.

반대로 `GROUP BY policy_id`만 해도 안 된다.
정책 ID는 여러 상품에 등장할 수 있기 때문에 서로 다른 상품의 정책 후보가 섞일 수 있다.

따라서 후보를 만들 때는 다음 조합이 중요하다.

```sql
GROUP BY product_id, policy_id, amount
```

그리고 최종 채택은 상품별로 해야 하므로 윈도우 함수의 기준은 다음이 된다.

```sql
PARTITION BY product_id
```

---

#### 새롭게 정리한 점

이번 문제는 단순 JOIN이나 단순 GROUP BY 문제가 아니라, **상품별 정책 후보 중 우선순위가 가장 높은 정책을 채택하는 문제**였다.

앞으로 비슷한 문제를 만나면 다음 순서로 접근해야 한다.

```text
1. 최종 출력 단위가 무엇인지 확인한다.
   예: 상품별, 회원별, 계좌별, 신청 건별

2. 후보의 고유 단위가 무엇인지 확인한다.
   예: 상품 ID + 정책 ID 조합

3. JOIN 후 행이 늘어나는지 확인한다.

4. 조건이 행 단위인지, 그룹 단위인지 구분한다.

5. 후보 단위로 GROUP BY 하여 조건 만족 여부를 만든다.

6. 조건을 만족하는 후보만 남긴다.

7. 최종 출력 단위별로 ROW_NUMBER()를 사용해 1개를 채택한다.

8. PARTITION BY 최종 출력 단위
   ORDER BY 금액 DESC, 정책 ID ASC
```

---

### 6. 다음주 학습 계획

이번 코딩테스트를 바탕으로 다음 주에는 알고리즘과 더불어 SQL 복합 유형을 중심으로 몇 문제 풀어봐야겠다.

#### 문제 후보

| 번호 | 플랫폼         | 문제명                               | 링크                                                                           | 목표 유형                  |
| -: | ----------- | --------------------------------- | ---------------------------------------------------------------------------- | ---------------------- |
|  1 | Programmers | 재구매가 일어난 상품과 회원 리스트 구하기           | https://school.programmers.co.kr/learn/courses/30/lessons/131536             | GROUP BY + HAVING      |
|  2 | Programmers | 우유와 요거트가 담긴 장바구니                  | https://school.programmers.co.kr/learn/courses/30/lessons/62284              | 조건 전체 만족               |
|  3 | Programmers | 자동차 대여 기록에서 대여중 / 대여 가능 여부 구분하기   | https://school.programmers.co.kr/learn/courses/30/lessons/157340             | CASE WHEN + GROUP BY   |
|  4 | Programmers | 주문량이 많은 아이스크림들 조회하기               | https://school.programmers.co.kr/learn/courses/30/lessons/133027             | UNION ALL + GROUP BY   |
|  5 | Programmers | 즐겨찾기가 가장 많은 식당 정보 출력하기            | https://school.programmers.co.kr/learn/courses/30/lessons/131123             | 그룹별 최댓값                |
|  6 | Programmers | 식품분류별 가장 비싼 식품의 정보 조회하기           | https://school.programmers.co.kr/learn/courses/30/lessons/131116             | 그룹별 최댓값 행 출력           |
|  7 | Programmers | 그룹별 조건에 맞는 식당 목록 출력하기             | https://school.programmers.co.kr/learn/courses/30/lessons/131124             | 조건 그룹 + JOIN           |
|  8 | LeetCode    | Department Top Three Salaries     | https://leetcode.com/problems/department-top-three-salaries/description/     | DENSE_RANK + 그룹별 Top N |
|  9 | LeetCode    | Human Traffic of Stadium          | https://leetcode.com/problems/human-traffic-of-stadium/description/          | ROW_NUMBER + 연속 구간     |
| 10 | LeetCode    | Customers Who Bought All Products | https://leetcode.com/problems/customers-who-bought-all-products/description/ | 전체 조건 만족 여부            |

---

### 7. 정리

```text
최종 출력 단위는 무엇인가?
JOIN 후 행이 늘어나는가?
조건은 행 단위인가, 그룹 단위인가?
```

이 세 가지를 먼저 정리하면, `WHERE`, `GROUP BY`, `HAVING`, 서브쿼리, 윈도우 함수 중 어떤 도구를 써야 하는지 더 빠르게 결정할 수 있을 것 같다.
