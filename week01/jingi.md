# 1주차 문제 풀이 인증

## 기본 정보

- 이름: 홍진기
- 목표 문제 수: 3
- 실제 풀이 문제 수:

## 푼 문제 목록

| 번호 | 문제 이름 | 난이도 | 링크 | 풀이 여부 | 소요 시간 |
| --- | --- | --- | --- | --- | --- |
| 1 | 이모티콘 할인 행사 | level 2, 정답률 44% | [프로그래머스](https://school.programmers.co.kr/learn/courses/30/lessons/150368?language=java) | O | 58분 20초 | 
| 2 | 올바른 괄호 | level 2, 정답률 49% | [프로그래머스](https://school.programmers.co.kr/learn/courses/30/lessons/60058) | O | 1시간 11분 |
| 3 | 바이러스 파이프 | level 2, 정답률 36% | [프로그래머스](https://school.programmers.co.kr/learn/courses/30/lessons/468373) | X | - |

---

# 오답노트

> 오답노트는 AI 답변이나 블로그 풀이를 그대로 복사하지 않고, 본인의 언어로 직접 작성해주세요.
> 문제별로 아래 양식을 복사해서 필요한 만큼 반복 작성하시면 됩니다.

## 문제 1

### 문제 정보

- 문제명: 이모티콘 할인 행사
- 문제 링크: https://school.programmers.co.kr/learn/courses/30/lessons/150368?language=java
- 사용한 알고리즘 / 자료구조: `완전 탐색`, `구현`

### 풀이 시간

58분 20초

### 처음 접근 방식

문제를 이해한 후, 어떤 알고리즘을 사용해야 하는지 빠르게 판단할 수 있었다.

결국 **문제의 조건을 충족하기 위해 각 이모티콘의 최적의 할인 비율 선정**

이모티콘 가격도 입력값에 따라 다 달라지기 때문에 모든 경우의 수를 다 따져봐야 된다고 생각했다. → **백트래킹 또는 완전탐색**

모든 이모티콘의 할인 비율을 선택한 뒤에 결과값 비교가 가능하기 때문에 → **완전탐색**

완전탐색을 생각하고 시간 복잡도를 고려했다.

시간 복잡도는 이모티콘의 개수가 최대 7개이고, 적용 가능한 할인 비율은 4개이므로, 이모티콘의 입력값이 최대일 경우 탐색해야 할 경우의 수는 $4^{7}$ 약 16000가지다.

여기에 각 경우의 수마다 사용자 수만큼 반복하고, 사용자 한 명 당 다시 이모티콘 수만큼 순회하므로 실제 총 연산 횟수는 대략 $4^{7} \times 100 \times 7$, 천만 번이다.

통상적으로 1초에 약 1억번 내외의 연산을 처리할 수 있다고 가정하면 충분하다고 판단했다.

### 풀이 방법

주석으로 어떻게 구현할지 생각하고, 이를 코드로 옮겼다.

```java
class Solution {
    
    public int[] solution(int[][] users, int[] emoticons) {
        
        int[] answer = {};
     
        // 이모티콘들의 할인 비율 -> 결과 결정 : 완전탐색 (할인비율 4, 이모티콘 7)

        return answer;
    }
    
    static void dfs(){ 
        
        // 탐색 끝일 경우 : 할인 가격 모두 적용 시(depth=7) result 검증

            // 사용자 당 구매액 계산
            
            // 기존 결과와 새 결과 비교 후 결과 반환
        
        // 완전탐색 (할인 비율 개수만큼 반복)

    }
}
```

최종 코드는 다음과 같다. 

```java
class Solution {
    
    static double[] rates = {0.1, 0.2, 0.3, 0.4};
    static int[] answer = new int[2];
    
    public int[] solution(int[][] users, int[] emoticons) {
        
        // 각 이모티콘들의 할인 비율
        double[] selectedRates = new double[emoticons.length];
        
        // 이모티콘들의 할인 비율 -> 결과 결정 : 완전탐색 (할인비율 4, 이모티콘 7)
        dfs(0, users, emoticons, selectedRates);

        return answer;
    }
    
    static void dfs(int depth, 
             int[][] users,
             int[] emoticons, 
             double[] selectedRates){ 
        
        // 탐색 끝일 경우 : 할인 가격 모두 적용 시 result 검증
        if (depth == emoticons.length){
            answer = selectResult(answer, users, emoticons, selectedRates);
        }
        else{
            // 완전탐색 (할인 비율 개수만큼 반복)
            for(int i = 0; i < 4; i++){
                selectedRates[depth] = rates[i];
                dfs(depth + 1, users, emoticons, selectedRates);
            }
        }
    }
    
    static int[] selectResult(int[] answer, 
                       int[][] users, 
                       int[] emoticons, 
                       double[] selectedRates){
        
        int[] result = new int[2];
        
        // 사용자 당 구매액 계산
        for (int i = 0 ; i < users.length; i++){

            int minimumRate = users[i][0];
            int priceLimit = users[i][1];
            
            int purchaseTotal = 0;
            
            for(int j = 0; j < emoticons.length; j++){
                // 이모티콘의 할인 비율이 "사용자의 비율" 이상일 경우 > 구매
                if(minimumRate <= (int)(selectedRates[j]*100)){
                    purchaseTotal += (double)emoticons[j] * (1-selectedRates[j]);
                }
            }
            // 이모티콘 플러스 가입 여부 결정
            if (purchaseTotal >= priceLimit){
                result[0]++;
            }
            else{
                result[1] += purchaseTotal;
            }
            
        }
        // 기존 결과와 새 결과 비교 후 결과 반환
        if (result[0] > answer[0]){
            return result;
        } else if (result[0] == answer[0]){
            return result[1] > answer[1] ? result : answer;
        } else {
            return answer;
        }
        
    }
    
}
```

결과값을 비교하는 로직이 생각보다 복잡할 것 같아서 처음 주석으로 계획했을 때와는 다르게 결과 비교 로직을 별도의 메소드로 분리했다.

따라서 **완전탐색 `dfs` → 이모티콘들의 할인 비율 결정 시 → 결과 비교 `selectResult` → 반복**

- **`dfs()`**
    
    완전탐색을 진행하는 메소드이다.
    
    각 이모티콘들(`emoticons[]`)에 대한 할인 경우의 수를 모두 탐색 한 뒤, 결과를 `selectResult()`로 결과를 비교한다.
    
- **`selectResult()`**
    
    기존 결과와 새로 탐색한 경우의 수에 대한 `[이모티콘 플러스 가입자 수, 사용자의 총 구매액]` 결과값을 비교한다.
    

### 어려웠던 지점

다행히 구현 후, 간단한 예외를 해결한 뒤에는 문제가 바로 해결됐다.

1. **이모티콘의 할인 비율은 `10%` , `20%` , `30%` , `40%` 가 있는데, 이를 `int[]` 타입으로 선언한 것**
    
    이로 인해 비율이 적용된 이모티콘의 구매 가격을 정하는 것에 시간이 좀 걸렸다.
    
2. **완전탐색의 조건을 `if (depth == 7)` 로 설정한 것**
    
    어려웠다기보다는 실수였다. 
    
    처음에 코드 실행할 때 `ArrayIndexOutofBounds` 예외가 떴지만 어렵지 않게 발견할 수 있었다.
    
    문제에서 최대 입력값이 7인 거고, 실제로는 이모티콘의 개수 (`emoticons.length`)로 해야 했다.
    

### 해결 방법

간단하게 해결할 수 있었다.

1. **할인 비율 `10%`, `20%`, `30%`, `40%` 를 `double[]` 타입으로 선언**
    
    → `double[] rates = {0.1, 0.2, 0.3, 0.4}` 
    
    그리고 이모티콘 가격 계산 시, 타입 캐스팅으로 할인 금액을 계산하는 로직으로 수정함으로써 해결했다.
    
2. **`if (depth == emotiocons.length)` 로 해결함으로써 간단하게 해결**

### 시간복잡도 / 공간복잡도

- 시간복잡도: $O(4^{m} \times n \times m)$ (1 ≤ m ≤ 7, 1 ≤ n ≤ 100)
    
- 공간복잡도: $O(m)$ (1 ≤ m ≤ 7) (`selectedRates` 배열 + 재귀 스택 깊이)

### 새롭게 알게 된 점

이번 문제는 코드를 다 작성한 뒤, 테스트케이스와 실행 결과가 달라서 코드를 수정하는 작업이 없었다.

→ 원래 위 과정에서 시간을 굉장이 많이 뺏겼는데, 그래서 목표 시간 내에 문제를 풀 수 있었던 것 같다.

주석으로 어떻게 문제를 풀어나갈지 전체적으로 설계한 뒤, 코드로 작성해나가는 방식 덕분인 것 같다.

그리고 다음부터는 비율을 계산하는 경우에는 `double` , 혹은 `float` 타입을 사용하는 것을 잊지 말아야겠다.

---

## 문제 2

### 문제 정보

- 문제명: 괄호 변환
- 문제 링크: https://school.programmers.co.kr/learn/courses/30/lessons/60058
- 사용한 알고리즘 / 자료구조: 스택, 구현

### 처음 접근 방식

기본적으로 괄호를 다루는 문자열이기 때문에 스택 자료구조를 떠올렸다. 그리고 문제에서 "균형잡힌 괄호 문자열"을 "올바른 괄호 문자열"로 변환하는 과정을 제공해주었기 때문에, 이를 그대로 구현하는 것이 문제의 핵심이라고 생각했다.

### 풀이 방법

소스코드는 다음과 같다.

문제의 설명을 그대로 구현하는 것이기 때문에 별도의 주석은 작성하지 않았고, 문제의 설명을 코드로 옮기는 것이기 때문에 별도의 어려운 알고리즘을 구현하지 않아도 됐었다.

```java
class Solution {
    public String solution(String p) {
        return process(p);
    }
    
    public String process(String p){
        
        if (p.equals("")) return "";
        
        int div = separate(p);
        String u = p.substring(0, div);
        String v = p.substring(div, p.length());
        System.out.println("u:" + u);
        System.out.println("v:" + v);
        if (isCorrect(u)){
            return u + process(v);
        } else {
            String tmp = "(" + process(v) + ")";         
            u = reverse(u.substring(1, u.length()-1));
            return tmp + u;
        }
        
    }
     
    public int separate(String p){
        // 분리될 인덱스 (해당 인덱스부터가 v)
        int sepPoint = 2;
        while(!isBalanced(p.substring(0, sepPoint))){
            sepPoint += 2;
        }
        return sepPoint;
    }
    
    public String reverse(String s){
        return s.replace("(", "-")
                .replace(")", "(")
                .replace("-", ")");
    }
    
    public boolean isBalanced(String u){
        int oCount = 0;
        int cCount = 0;
        for(int i = 0; i < u.length(); i++){
            if (u.charAt(i) == '(') oCount++;
            else cCount++;
        }
        return oCount == cCount;
    }
    
    public boolean isCorrect(String u){
        
        char[] p = u.toCharArray();
        char[] stack = new char[1000];
        int top = -1;
        
        for (int i = 0 ; i < p.length; i++){
            if (p[i] == '('){
                stack[++top] = p[i];
            }
            else{
                if (top == -1 || 
                    stack[top--] != '(') return false;
            }   
        }
        if (top != -1) return false;
        else return true;
    }
}
```

- `process()` : 문제에서 설명하는 과정을 실행하는 메소드이다.
- `separate()` : 문자열 w를 두 균형잡힌 문자열로 분리하는 메소드이다. 분리되는 곳의 인덱스(v의 첫 번째 인덱스)를 반환한다.
- `reverse()` : 괄호르 뒤집는 메소드이다.
- `isBalanced()` : 열린 괄호와 닫힌 괄호의 개수를 비교함으로써 "균형잡힌 괄호 문자열"인지를 판단하는 메소드이다.
- `isCorrect()` : 괄호의 짝이 맞는지 확인하는 메소드이다. 여기서 큐를 사용한다.


### 어려웠던 지점

구현하는 과정에서 여러 오류를 접했다.

1. **결과값이 무조건 `(`으로 나오는 문제**

    코드를 실행해보니, 괄호 문자열이 모두 `(`로 구성되었다.

    알고보니, `reverse()` 메소드를 다음과 같이 구현한 것이 문제였다.

    ```java
    public String reverse(String s){
        return s.replace("(", ")")
                .replace(")", "(");
    }
    ```

    내가 구현을 잘못했다. 위 메소드는 결과적으로 모든 문자열이 `)`가 되고, 이를 `(`로 대체하기 때문에 모두 `(`로 출력되는 것이었다.
    
    swap 함수를 구현할 때의 개념을 사용하여 tmp용으로 다른 문자로 대체하고, 이를 다시 `(` 문자로 변환하여 reverse를 구현해야 했다.

2. **재귀함수 설계 오류**

    StringBuilder를 재귀 호출마다 매개변수로 넘김으로써 다음 함수에서도 이전 함수에서 구했던 결과가 중복해서 StringBuilder에 추가되는 문제가 발생한다.

    ```java
    public String process(String p, StringBuilder sb){
        
        if (p.equals("")) return "";
        
        int div = separate(p);
        String u = p.substring(0, div);
        String v = p.substring(div, p.length());
        if (isCorrect(u)){
            sb.append(u);
            return sb.append(process(v, sb)).toString();
        } else {
            StringBuilder sb2 = new StringBuilder();
            sb2.append("(");
            sb2.append(process(v, sb2));
            sb2.append(")");
            sb.append(u.substring(1, u.length()-1));
            String tmp = reverse(sb.toString());
            sb2.append(tmp);
            return reverse(sb2.toString());
        }
        
    }
    ```

    ```java
    // process(v, sb) 내부에서 sb에 append됨
    sb2.append(process(v, sb));   
    // 여기서 또 sb에 append
    sb.append(u.substring(1, u.length()-1));
    // sb 전체를 사용 → 이전 재귀 내용까지 포함됨  
    String tmp = reverse(sb.toString());     
    ```

    process(v, sb) 를 재귀 호출하면 그 안에서 sb에 뭔가 append된 상태로 돌아오는데, 돌아온 후에 sb.append(...) 로 또 추가하고, sb.toString() 으로 sb 전체(이전 재귀 내용 + 지금 내용)를 reverse에 넘기는 게 문제이다.

### 해결 방법

1. **`reverse()` 함수 수정**

    ```java
        public String reverse(String s){
        return s.replace("(", "-")
                .replace(")", "(")
                .replace("-", ")");
    }
    ```

2. **`StringBuilder` 대신 `String` 사용

    가장 중요한 것은 재귀 함수의 기본 개념이다. 재귀는 **호출된 메소드는 반환 결과만 반환하고, 반환 결과를 모두 합치는 것은 처음 호출된 메소드가 한다**는 것이다.

    따라서 StringBuilder를 메소드에서 제거하고, 문자열의 추가는 간단한 문자열 연산으로 처리한다.

    ```java
        public String process(String p){
        
        if (p.equals("")) return "";
        
        int div = separate(p);
        String u = p.substring(0, div);
        String v = p.substring(div, p.length());
        System.out.println("u:" + u);
        System.out.println("v:" + v);
        if (isCorrect(u)){
            return u + process(v);
        } else {
            String tmp = "(" + process(v) + ")";         
            u = reverse(u.substring(1, u.length()-1));
            return tmp + u;
        }
        
    }
    ```

### 시간복잡도 / 공간복잡도

- 시간복잡도: $O(n^{2})$ (n = 괄호 문자열의 길이)
- 공간복잡도: $O(n)$

### 새롭게 알게 된 점

재귀함수에 대한 이해가 부족했던 것을 보여준 문제였다. 막연하게 **재귀함수는 모든 함수가 실행부터 종료될 때까지 똑같이 실행될 때 문제가 발생하지 않도록 작성해야 한다.**라고 이해하고 있었다.

이러한 생각을 갖고 재귀함수의 실행 흐름을 시나리오로 그려보면서 구현을 잘 했던 문제도 있었지만, 가끔은 구현을 잘 못하여 이렇게 매개변수가 중복되는 등의 문제가 발생하는 것 같다.

앞으로 재귀함수를 구현할 때, 함수가 재귀적으로 호출되고, 다시 반환되는 시나리오를 더 집중해서 생각하면서 더 복잡한 문제를 재귀함수로 풀어봐야겠다.

---

## 문제 3

### 문제 정보

- 문제명: 바이러스 파이프
- 문제 링크: https://school.programmers.co.kr/learn/courses/30/lessons/468373
- 사용한 알고리즘 / 자료구조: BFS, DFS, 백트래킹

### 처음 접근 방식

파이프의 종류가 여러 개였기 때문에 어려웠다. 감염된 노드부터 탐색을 시작해서 그 다음에는 어떤 파이프를 열어야 하는지 결정하는 로직을 구현하는 것이 어려웠다.

매 탐색마다 각 파이프를 열었을 때 감염되는 노드의 개수를 비교하는 접근 방식도 생각했지만, 이 접근 방식 역시 구현하는 것이 어려웠다.

결국 시간 내에 문제를 풀지 못했다.

### 풀이 방법

문제의 조건을 본다면 충분히 BFS/DFS와 백트래킹으로 풀이가 가능하다.

열고 닫을 파이프를 결정하는 부분에서, **현재의 최적의 선택이 k번 열고 닫은 뒤의 최적값으로 이어진다는 보장이 없기 때문**이다.

예를 들어, 다음 감염될 수 있는 노드들을 탐색해서 감염될 수 있는 노드의 수가 가장 많은 파이프를 선택한다고 하자. 하지만 그 이후의 노드를 탐색할 때는 연결된 파이프가 없을 수도 있다.

반면, 당장 가장 많이 감염될 수 있는 노드를 선택하지 않더라도, 그 다음 탐색에서 감염될 수 있는 노드가 가장 많을 수도 있다. 

결국, **"현재 최선"이 "전체 최선"을 보장하지 않는** 구조이기 때문에 탐색할 수 있는 파이프라인의 모든 순서 조합을 따져봐야 한다.

파이프의 종류는 3가지이고, n ≤ 100, k ≤ 10이다.

최대 경우의 수는 n*$3^{10}$ 이므로 백트래킹을 사용할 가치가 있다.

따라서 최종 코드는 다음과 같다.

```java
import java.util.*;

class Solution {

    static int[][] graph;
    static int max = 0;
    static int[] pipe = {1, 2, 3};

    public int solution(int n, int infection, int[][] edges, int k) {
        int answer = 0;

        boolean[] infectedNode = new boolean[n+1];

        // graph 생성
        graph = new int[n+1][n+1];

        for (int i = 0 ; i < edges.length ; i++){
            graph[edges[i][0]][edges[i][1]] = edges[i][2];
            graph[edges[i][1]][edges[i][0]] = edges[i][2];
        }
        // infection 노드부터 탐색 시작
        infectedNode[infection] = true;
        dfsPipe(k, 0, infection, infectedNode);
        
        // depth는 깊이, start는 시작 파이프
        return max;
    }

    // pipe 조합 탐색
    static void dfsPipe(int k, int depth, int start, boolean[] infectedNode){

        if (depth == k){
            int result = 0;
            for(int i = 1 ; i < infectedNode.length; i++){
                if (infectedNode[i]){
                    result++;
                }
            }
            max = Math.max(result, max);
        }

        else{
            for(int i = 0; i < 3; i++){  
                dfsPipe(k, depth+1, pipe[i], bfsNode(pipe[i], infectedNode));
            }
        }

    }

    // 감염 노드 탐색 (bfs)
    static boolean[] bfsNode(int pipe, boolean[] infectedNode){

        boolean[] newInfectedNode = infectedNode.clone();
        Queue<Integer> queue = new LinkedList<>();
        
        for (int i = 1; i < newInfectedNode.length; i++){
            if (newInfectedNode[i]){
                queue.add(i);
            }
        }
        
        while(!queue.isEmpty()){
            int current = queue.poll();
            for(int i = 0 ; i < graph[current].length; i++){
                if (graph[current][i] == pipe){
                    if (!newInfectedNode[i]){
                        newInfectedNode[i] = true;
                        queue.add(i);
                    }
                }
            }
        }
        return newInfectedNode;
    }
}
```

dfs로 파이프를 여는 경우를 모두 시도해보고, bfs로 인접한 노드 중 감염되는 노드를 탐색하여 결과값을 구해나간다.

감염된 노드를 탐색할 때 bfs를 사용한 이유는, 노드의 탐색 시작점이 여러 개이기 때문에 이를 dfs로 구현하는 것보다는 bfs로 구현하는 것이 더 편리하다고 생각했다. (bfs로 첫 시작부터 여러 노드를 큐에 넣으면 되기 때문)


### 어려웠던 지점

처음에 문제 해결을 위한 알고리즘을 생각해내는 것이 가장 어려웠던 것 같다. 

### 해결 방법

AI를 활용해서 어떤 방식으로 접근해야 하는지 확인했다. AI를 통해 문제의 접근 방식을 알게 된 후, 코드로 구현하는 것은 어렵지 않았다.

AI가 제공한 접근 방식은 다음과 같다. 핵심은 **먼저 해결해야 할 문제를 각각 나누고**, **각 문제를 해결할 수 있는 적절한 알고리즘을 선택**하는 것이다.

```
트리 구조에서 바이러스 시작 노드로부터 파이프를 열고 닫는 행동을 최대 k번 반복해 최대한 많은 노드를 감염시켜야 합니다. 파이프는 A/B/C 3종류이며, 같은 종류는 한꺼번에 열립니다.

- 파이프를 여닫는 순서 조합을 결정해야 합니다. k ≤ 10이고 타입이 3가지이므로, 각 행동마다 "어떤 타입을 열까"를 선택하는 경우의 수는 최대 3^10 = ~59,000가지입니다.
- 특정 타입의 파이프만 열렸을 때, 현재 감염된 노드들로부터 도달 가능한 모든 노드를 구합니다.
```

### 시간복잡도 / 공간복잡도

- 시간복잡도: $O(3^{k} \times n^{2})$ (1 ≤ n ≤ 100, 1 ≤ k ≤ 10)
- 공간복잡도: $O(n^{2})$

### 새롭게 알게 된 점

생각해보면 지금까지 단조로운(?) dfs, bfs, 백트래킹 문제만 풀다보니, 이렇게 여러 알고리즘을 동시에 요구하는 문제를 접할 때 어려워했던 것 같다.

처음 문제를 봤을 때, 'bfs로 어떻게 해볼 수 있을 것 같은데..'라는 생각을 바탕으로 여러 접근을 시도해보면서 시간을 많이 소비한 것 같다.

AI가 알려준 접근 방식처럼, 입력값의 범위와 같이 문제에서 제공하는 다양한 조건을 고려하여 문제를 이해하고, 여러 알고리즘을 사용해야 하는 것도 염두에 두어야겠다.

---

# 다음 주 목표

- 목표 문제 수: 3
- 집중할 유형: 구현