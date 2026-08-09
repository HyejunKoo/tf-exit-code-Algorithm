# 4주차 문제 풀이 인증

## 기본 정보

- 이름: 주정윤
- 목표 문제 수: 3
- 실제 풀이 문제 수: 3

---

# 오답노트

## 문제 1

- **문제명:** 타겟 넘버
- **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/43165
- **알고리즘 / 자료구조:** DFS(깊이 우선 탐색), 재귀

### 접근 및 시행착오

처음에는 모든 경우를 직접 계산해야 하나 고민했다. 하지만 각 숫자마다 더하거나 빼는 두 가지 선택이 있다는 점을 보고 DFS를 사용하면 모든 경우를 탐색할 수 있겠다고 생각했다.

처음에는 재귀가 익숙하지 않아 종료 조건을 어디에 둬야 할지 조금 헷갈렸는데, 모든 숫자를 사용한 시점에서 현재 합이 타겟과 같은지만 확인하면 된다는 점을 이해하고 구현하였다.

### 최종 풀이

현재 숫자를 더하는 경우와 빼는 경우를 모두 재귀적으로 탐색하였다.

재귀 함수에는 현재 인덱스와 지금까지 계산한 합을 전달하였다. 모든 숫자를 사용했을 때 현재 합이 타겟과 같다면 정답을 1 증가시켰고, 이렇게 모든 경우를 탐색하면서 타겟을 만드는 경우의 수를 구하였다.

```java
import java.util.*;

class Solution {
    int[] numbers;
    int target;
    int answer;

    void dfs(int index, int sum){
        if(numbers.length == index){
            if(target == sum) answer++;
            return;
        }

        dfs(index + 1, sum + numbers[index]);
        dfs(index + 1, sum - numbers[index]);
    }

    public int solution(int[] numbers, int target) {
        answer = 0;
        this.numbers = numbers;
        this.target = target;

        dfs(0, 0);
        return answer;
    }
}
```

### 복잡도

- **시간복잡도:** O(2ⁿ)
- **공간복잡도:** O(N)

### 핵심 포인트

DFS에서는 종료 조건을 명확하게 정하는 것이 중요하다는 것을 다시 느꼈다. 또한 현재 상태를 매개변수로 넘기면서 탐색하면 모든 경우를 깔끔하게 확인할 수 있다는 점을 배웠다.

---

## 문제 2

- **문제명:** 네트워크
- **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/43162
- **알고리즘 / 자료구조:** DFS, 그래프

### 접근 및 시행착오

처음에는 연결된 컴퓨터의 개수를 세려고 했는데, 문제를 다시 읽어보니 연결된 그룹의 개수를 구하는 문제였다.

그래서 방문하지 않은 컴퓨터를 시작으로 DFS를 수행하면 하나의 네트워크를 모두 탐색할 수 있다고 생각했다.

### 최종 풀이

모든 컴퓨터를 순회하면서 아직 방문하지 않은 컴퓨터가 나오면 DFS를 수행하였다.

DFS에서는 현재 컴퓨터와 연결된 다른 컴퓨터를 계속 방문하도록 구현하였다. DFS가 한 번 끝날 때마다 하나의 네트워크를 모두 탐색한 것이므로 네트워크 개수를 1 증가시켰다.

```java
class Solution {

    static int num;

    public int solution(int n, int[][] computers) {
        int answer = 0;
        boolean[] visited = new boolean[n];
        num = 0;

        for(int i = 0; i < n; i++){
            if(!visited[i]){
                dfs(n, computers, visited, i);
                num++;
            }
        }

        answer = num;

        return answer;
    }

    public void dfs(int n, int[][] computers, boolean[] visited, int i){
        visited[i] = true;

        for(int j = 0; j < n; j++){
            if(computers[i][j] == 1 && !visited[j]){
                dfs(n, computers, visited, j);
            }
        }
    }
}
```

### 복잡도

- **시간복잡도:** O(N²)
- **공간복잡도:** O(N)

### 핵심 포인트

그래프 문제에서는 연결된 모든 노드를 한 번에 탐색하는 DFS나 BFS를 먼저 떠올리는 것이 중요하다는 것을 느꼈다. 또한 방문 체크를 하지 않으면 같은 노드를 계속 탐색하게 되므로 반드시 방문 여부를 함께 관리해야 한다.

---

## 문제 3

- **문제명:** 게임 맵 최단거리
- **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/1844
- **알고리즘 / 자료구조:** BFS, 큐

### 접근 및 시행착오

처음에는 DFS로도 풀 수 있을 것 같았지만, 최단거리를 구하는 문제는 BFS가 더 적합하다는 점을 떠올렸다.

처음에는 이동 횟수를 따로 저장하지 않았는데, 이전 위치의 이동 거리에서 1을 더하는 방식으로 저장하면 자연스럽게 최단거리를 구할 수 있다는 점을 이용하였다.

### 최종 풀이

시작 위치를 큐에 넣고 BFS를 수행하였다.

현재 위치에서 상하좌우를 확인하면서 이동 가능한 위치이고 아직 방문하지 않은 곳이라면 큐에 넣었다. 이때 이전 위치의 이동 횟수에 1을 더해서 저장하였다.

탐색이 끝난 뒤 도착 위치의 값이 0이라면 도착하지 못한 것이므로 -1을 반환하고, 그렇지 않다면 저장된 이동 횟수를 반환하였다.

```java
import java.util.*;

class Solution {

    int[] dx = {1, 0, -1, 0};
    int[] dy = {0, 1, 0, -1};

    public int solution(int[][] maps) {
        int answer = 0;

        int[][] visited = new int[maps.length][maps[0].length];

        bfs(maps, visited);
        answer = visited[maps.length - 1][maps[0].length - 1];

        if(answer == 0) answer = -1;

        return answer;
    }

    public void bfs(int[][] maps, int[][] visited){
        int x = 0;
        int y = 0;

        visited[x][y] = 1;

        Queue<int[]> queue = new LinkedList<>();
        queue.add(new int[]{x, y});

        while(!queue.isEmpty()){
            int[] current = queue.poll();

            int cX = current[0];
            int cY = current[1];

            for(int i = 0; i < 4; i++){
                int nx = cX + dx[i];
                int ny = cY + dy[i];

                if(nx < 0 || nx >= maps.length || ny < 0 || ny >= maps[0].length){
                    continue;
                }

                if(visited[nx][ny] == 0 && maps[nx][ny] == 1){
                    visited[nx][ny] = visited[cX][cY] + 1;
                    queue.add(new int[]{nx, ny});
                }
            }
        }
    }
}
```

### 복잡도

- **시간복잡도:** O(N × M)
- **공간복잡도:** O(N × M)

### 핵심 포인트

최단거리를 구하는 문제에서는 DFS보다 BFS를 먼저 떠올리는 것이 중요하다는 것을 다시 느꼈다. 또한 방문 배열을 단순히 방문 여부만 저장하는 것이 아니라 이동 거리까지 함께 저장하면 별도의 거리 배열 없이도 쉽게 구현할 수 있다는 점을 배웠다.

---

# 다음 주 목표

- 목표 문제 수: 3
- 집중할 유형: DFS / BFS / 완전탐색