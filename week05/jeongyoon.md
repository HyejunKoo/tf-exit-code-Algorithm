# 5주차 문제 풀이 인증

## 기본 정보

* 이름: 주정윤
* 목표 문제 수: 3
* 실제 풀이 문제 수: 3

## 푼 문제 목록

| 번호 | 문제 이름  | 난이도  | 링크                                                              |
| -- | ------ | ---- | --------------------------------------------------------------- |
| 1  | 단어 변환  | Lv.3 | https://school.programmers.co.kr/learn/courses/30/lessons/43163 |
| 2  | 아이템 줍기 | Lv.3 | https://school.programmers.co.kr/learn/courses/30/lessons/87694 |
| 3  | 여행경로   | Lv.3 | https://school.programmers.co.kr/learn/courses/30/lessons/43164 |

---

# 오답노트

## 문제 1

### 문제 정보

* 문제명: 단어 변환
* 문제 링크: https://school.programmers.co.kr/learn/courses/30/lessons/43163
* 사용한 알고리즘 / 자료구조: BFS, Queue, HashSet

### 접근 및 시행착오

처음 문제를 봤을 때 한 번에 한 글자만 변경할 수 있고, 변경한 단어도 `words` 안에 있어야 한다는 조건을 보고 BFS로 접근했다.

`begin`에서 시작해서 현재 단어와 한 글자만 다른 단어들을 다음 단계로 넣어주면 자연스럽게 가장 짧은 변환 과정을 찾을 수 있다고 생각했다.

처음에는 모든 단어를 계속 비교하면서 방문 여부를 체크하는 방식으로 생각했는데, 이미 확인한 단어를 다시 큐에 넣으면 같은 단어를 여러 번 확인할 수 있기 때문에 `HashSet`을 이용해서 아직 방문하지 않은 단어만 관리하도록 했다.

또 하나 신경 쓴 부분은 `target`이 아예 `words`에 없는 경우이다. 문제 조건상 `words`에 있는 단어로만 변환할 수 있기 때문에 이 경우는 바로 `0`을 반환하도록 처리했다.

### 최종 풀이

먼저 `words`를 `HashSet`으로 만들어서 변환할 수 있는 단어들을 관리했다.

1. `target`이 `words`에 없다면 변환할 수 없으므로 `0`을 반환한다.
2. `begin`을 큐에 넣고 BFS를 시작한다.
3. 현재 큐에 있는 단어와 한 글자만 다른 단어를 찾아 큐에 넣는다.
4. 큐에 넣은 단어는 `Set`에서 제거해서 다시 방문하지 않도록 한다.
5. 큐의 한 단계가 끝날 때마다 `answer`를 1 증가시킨다.
6. 현재 단어가 `target`과 같아지면 지금까지의 변환 횟수를 반환한다.
7. 두 단어가 한 글자만 다른지는 `canConvert()` 메서드에서 각 문자를 비교해서 확인했다.

BFS에서는 같은 레벨에 있는 단어들을 한 번에 처리해야 변환 횟수를 정확하게 계산할 수 있기 때문에 `for`문을 이용해서 현재 큐의 크기만큼 처리했다.

### 내가 작성한 코드

```java
import java.util.*;

class Solution {
    public int solution(String begin, String target, String[] words) {
        int answer = 0;

        Queue<String> queue = new LinkedList<>();
        Set<String> set = new HashSet<>(Arrays.asList(words));

        if(!set.contains(target)) return 0;

        queue.offer(begin);
        set.remove(begin);

        while(!queue.isEmpty()){
            for(int i=0; i<queue.size(); i++){
                String current = queue.poll();

                if(current.equals(target)) return answer;

                for(String word : set.toArray(new String[set.size()])){
                    if(canConvert(current, word)){
                        queue.offer(word);
                        set.remove(word);
                    }
                }
            }

            answer++;
        }

        return 0;
    }

    public boolean canConvert(String word1, String word2){
        int cnt = 0;

        for(int i=0; i<word1.length(); i++){
            if(word1.charAt(i)!=word2.charAt(i)) cnt++;
        }

        return cnt==1;
    }
}
```

### 어려웠던 지점

가장 신경 썼던 부분은 **변환 횟수를 어떻게 계산할지**였다.

단순하게 큐에서 하나씩 꺼낼 때마다 `answer`를 증가시키면 같은 단계에 있는 단어들도 서로 다른 횟수로 계산될 수 있다.

그래서 현재 큐에 들어있는 단어들을 한 번에 처리하고, 그 단계가 끝났을 때 `answer`를 증가시키는 방식으로 구현했다.

또한 이미 방문한 단어를 다시 확인하지 않도록 `HashSet`에서 제거하는 것도 중요했다. 같은 단어를 계속 큐에 넣게 되면 불필요하게 탐색하는 경우가 생길 수 있기 때문이다.

### 해결 방법

BFS의 레벨 단위 탐색을 이용해서 해결했다.

예를 들어 `hit -> hot -> dot -> dog -> cog`라면

* `hit` : 0단계
* `hot` : 1단계
* `dot` : 2단계
* `dog` : 3단계
* `cog` : 4단계

이런 식으로 한 단계씩 증가하도록 구현했다.

두 단어가 실제로 변환 가능한지는 `canConvert()`에서 서로 다른 문자의 개수를 세어서 정확히 1개인지 확인했다.

### 시간복잡도 / 공간복잡도

* **시간복잡도:** O(N² × L)

  * `N`은 `words`의 개수이고, 각 단어마다 아직 방문하지 않은 단어들과 비교한다.
  * 두 단어가 한 글자만 다른지 확인하는 데 `L`만큼 시간이 걸린다.
* **공간복잡도:** O(N)

  * 큐와 `HashSet`에 단어들을 저장한다.

### 핵심 포인트

BFS 문제에서 **최단 거리나 최소 횟수**를 구해야 한다면 단계별로 탐색하는 것이 중요하다.

특히 이번 문제처럼 현재 단계의 노드들을 모두 처리한 후 다음 단계로 넘어가는 구조에서는 큐의 현재 크기를 기준으로 반복문을 돌리면 단계별 횟수를 쉽게 관리할 수 있다.

또한 이미 방문한 노드는 다시 방문하지 않도록 `Set`에서 제거하는 방법도 기억해두면 좋을 것 같다.

---

## 문제 2

### 문제 정보

* 문제명: 아이템 줍기
* 문제 링크: https://school.programmers.co.kr/learn/courses/30/lessons/87694
* 사용한 알고리즘 / 자료구조: BFS, 2차원 배열

### 접근 및 시행착오

처음에는 직사각형의 테두리를 그대로 2차원 배열에 표시하고 BFS를 하면 될 것 같다고 생각했다.

그런데 직사각형이 여러 개 겹쳐있는 경우 단순히 좌표에 테두리를 표시하면 **겹치는 부분이나 꼭짓점에서 잘못된 경로가 생길 수 있다는 문제**가 있었다.

특히 직사각형의 모서리를 따라 이동할 때 실제로는 한 칸 이동해야 하는데 좌표를 그대로 사용하면 대각선처럼 연결되는 문제가 발생할 수 있다고 생각했다.

그래서 좌표를 전부 2배로 늘린 다음 BFS를 진행하고, 마지막에 결과를 2로 나누는 방법을 사용했다.

### 최종 풀이

먼저 직사각형의 모든 좌표를 2배로 늘려서 맵에 표시했다.

`fill()` 메서드에서 직사각형의 내부는 `2`, 테두리는 `1`로 표시했다.

실제로 이동할 수 있는 곳은 테두리뿐이기 때문에 BFS에서는 `map[nx][ny] == 1`인 경우에만 이동하도록 했다.

전체적인 과정은 다음과 같다.

1. 좌표를 2배로 만든다.
2. 각 직사각형의 영역을 맵에 표시한다.
3. 직사각형의 가장자리만 이동 가능한 값 `1`로 표시한다.
4. 시작점과 아이템 위치도 2배로 변환한다.
5. BFS를 이용해서 테두리 위를 따라 이동한다.
6. `visited` 배열에 현재까지 이동한 거리를 저장한다.
7. 아이템 위치에 도착하면 해당 거리를 저장한다.
8. 좌표를 2배로 만들었기 때문에 마지막 결과를 `2`로 나눈다.

좌표를 2배로 만드는 것이 이 문제의 핵심이라고 생각했다. 이렇게 하면 원래 좌표에서 애매하게 연결되는 부분을 분리해서 BFS가 잘못된 경로로 이동하는 것을 막을 수 있다.

### 내가 작성한 코드

```java
import java.util.*;

class Solution {

    static int[][] map;
    static int answer;

    static int[] dx = {-1, 0, 0, 1};
    static int[] dy = {0, -1, 1, 0};

    public int solution(int[][] rectangle, int characterX, int characterY, int itemX, int itemY) {
        answer = 0;

        map = new int[101][101];

        for(int i=0; i<rectangle.length; i++){
            fill(
                2 * rectangle[i][0],
                2 * rectangle[i][1],
                2 * rectangle[i][2],
                2 * rectangle[i][3]
            );
        }

        bfs(2 * characterX, 2 * characterY, 2 * itemX, 2 * itemY);

        return answer / 2;
    }

    // 꼭짓점은 1, 면은 2
    public void fill(int x1, int y1, int x2, int y2){
        for(int i=x1; i<=x2; i++){
            for(int j=y1; j<=y2; j++){
                if(map[i][j] == 2) continue;

                map[i][j] = 2;

                if(i==x1 || i==x2 || j==y1 || j==y2){
                    map[i][j] = 1;
                }
            }
        }
    }

    public void bfs(int startX, int startY, int itemX, int itemY){
        boolean[][] visited = new boolean[101][101];

        Queue<Integer> queue = new LinkedList<>();

        queue.add(startX);
        queue.add(startY);

        while(!queue.isEmpty()){
            int x = queue.poll();
            int y = queue.poll();

            for(int i=0; i<4; i++){
                int nx = x + dx[i];
                int ny = y + dy[i];

                if(!check(nx, ny)) continue;

                if(map[nx][ny] != 1 || visited[nx][ny]) continue;

                map[nx][ny] = map[x][y] + 1;

                if(nx == itemX && ny == itemY){
                    answer = (answer == 0)
                            ? map[nx][ny]
                            : Math.min(answer, map[nx][ny]);

                    continue;
                }

                visited[nx][ny] = true;

                queue.add(nx);
                queue.add(ny);
            }
        }
    }

    public boolean check(int x, int y){
        if(x<0 || y<0 || x>100 || y>100) return false;

        return true;
    }
}
```

### 어려웠던 지점

가장 어려웠던 부분은 **직사각형의 테두리를 어떻게 정확하게 표현할지**였다.

처음에는 좌표를 그대로 사용하면 될 것 같았지만 직사각형이 겹치는 경우 테두리가 아닌 부분이 이동 가능한 것처럼 처리될 수 있었다.

특히 꼭짓점이나 직사각형이 겹치는 부분 때문에 BFS가 실제로는 갈 수 없는 방향으로 이동할 가능성이 있었다.

그래서 좌표를 2배로 늘리는 방법을 사용했다.

예를 들어 원래 좌표에서 한 칸 차이가 나는 것을 2칸 차이로 만들어서 중간 좌표를 확보하면, 테두리와 내부 영역을 좀 더 명확하게 구분할 수 있다.

### 해결 방법

`fill()`에서 직사각형 전체를 먼저 `2`로 표시하고, 가장자리인 경우 `1`로 변경했다.

BFS에서는 `1`인 좌표만 이동할 수 있도록 제한했다.

그리고 시작점과 아이템 위치도 똑같이 2배로 변환해서 같은 좌표계에서 탐색했다.

BFS가 끝난 후 실제 문제에서의 거리는 원래 좌표 기준이기 때문에 `answer / 2`를 해서 반환했다.

### 시간복잡도 / 공간복잡도

* **시간복잡도:** O(R × C² + M)

  * `R`은 직사각형의 개수이고, 좌표 범위가 최대 50이므로 각 직사각형을 맵에 표시하는 과정이 필요하다.
  * BFS는 최대 101 × 101 크기의 맵을 탐색한다.
* **공간복잡도:** O(M²)

  * `map`과 `visited` 배열을 사용한다.
  * 좌표 범위가 최대 100이므로 실제로는 제한된 크기의 2차원 배열을 사용한다.

### 핵심 포인트

이 문제는 단순히 BFS를 사용하는 것보다 **BFS를 돌릴 맵을 어떻게 만들 것인지가 더 중요했던 문제**였다.

특히 좌표를 2배로 늘리는 방법을 처음 접했는데, 좌표가 겹치거나 경로가 애매하게 연결되는 문제를 해결할 때 사용할 수 있다는 것을 알게 되었다.

앞으로 좌표를 이용한 BFS 문제에서 경로가 겹치거나 꼭짓점 때문에 잘못 연결될 가능성이 있다면 좌표를 확대해서 생각해보는 것도 기억해둘 것 같다.

---

## 문제 3

### 문제 정보

* 문제명: 여행경로
* 문제 링크: https://school.programmers.co.kr/learn/courses/30/lessons/43164
* 사용한 알고리즘 / 자료구조: DFS, 백트래킹, ArrayList

### 접근 및 시행착오

처음에는 항공권을 모두 사용해야 하고, 같은 공항에서 여러 경로가 나올 수 있기 때문에 DFS로 모든 가능한 경로를 찾아보는 방식으로 접근했다.

각 항공권을 한 번씩만 사용할 수 있기 때문에 `visited` 배열을 만들어서 사용한 항공권인지 체크했다.

문제에서 경로가 여러 개라면 알파벳 순으로 가장 앞서는 경로를 선택해야 하기 때문에 DFS를 진행하면서 모든 가능한 경로를 `ArrayList`에 저장한 다음 마지막에 정렬하는 방법을 사용했다.

처음부터 알파벳 순서대로 탐색해서 첫 번째 경로를 찾는 방법도 생각할 수 있지만, 일단 모든 경로를 구한 뒤 정렬하는 방식이 구현하기에는 더 편하다고 생각했다.

### 최종 풀이

DFS를 `"ICN"`에서 시작한다.

현재 공항과 출발 공항이 같은 항공권 중에서 아직 사용하지 않은 항공권을 찾는다.

찾은 항공권을 사용한 것으로 표시하고 해당 항공권의 도착 공항으로 DFS를 다시 호출한다.

모든 항공권을 사용했다면 지금까지 만든 경로를 `allRoute`에 저장한다.

DFS가 끝나면 `visited`를 다시 `false`로 변경해서 다른 경로에서도 해당 항공권을 사용할 수 있도록 한다.

모든 가능한 경로를 구한 후 `Collections.sort()`로 정렬하고 가장 앞에 있는 경로를 반환했다.

### 내가 작성한 코드

```java
import java.util.*;

class Solution {
    boolean[] visited;
    ArrayList<String> allRoute;

    public String[] solution(String[][] tickets) {
        String[] answer = {};

        visited = new boolean[tickets.length];
        allRoute = new ArrayList<>();

        int cnt = 0;

        dfs("ICN", "ICN", tickets, cnt);

        Collections.sort(allRoute);

        answer = allRoute.get(0).split(" ");

        return answer;
    }

    public void dfs(String start, String route, String[][] tickets, int cnt){
        if(cnt == tickets.length){
            allRoute.add(route);
            return;
        }

        for(int i=0; i<tickets.length; i++){
            if(start.equals(tickets[i][0]) && !visited[i]){
                visited[i] = true;

                dfs(
                    tickets[i][1],
                    route + " " + tickets[i][1],
                    tickets,
                    cnt + 1
                );

                visited[i] = false;
            }
        }
    }
}
```

### 어려웠던 지점

가장 어려웠던 부분은 **항공권을 모두 사용하면서 알파벳 순으로 가장 앞서는 경로를 찾는 것**이었다.

단순히 현재 공항에서 갈 수 있는 공항 중 알파벳이 빠른 곳으로 바로 이동하면 되는 것은 아니었다.

예를 들어 알파벳 순으로 먼저 선택한 경로가 나중에 항공권을 전부 사용하지 못하는 경우가 있을 수 있기 때문에, 한 경로를 선택한 뒤 끝까지 진행해보고 불가능하면 다시 돌아와 다른 항공권을 선택해야 했다.

그래서 백트래킹 방식으로 `visited`를 다시 `false`로 되돌리는 과정을 넣었다.

### 해결 방법

DFS를 이용해서 가능한 모든 여행 경로를 탐색했다.

1. `"ICN"`에서 시작한다.
2. 현재 공항에서 출발하는 항공권을 찾는다.
3. 아직 사용하지 않은 항공권이면 `visited`를 `true`로 변경한다.
4. 도착 공항을 기준으로 다시 DFS를 호출한다.
5. 모든 항공권을 사용했다면 완성된 경로를 저장한다.
6. DFS가 끝나면 `visited`를 다시 `false`로 변경한다.
7. 모든 경로를 구한 뒤 정렬한다.
8. 정렬된 경로 중 가장 앞의 경로를 반환한다.

이렇게 하면 항공권을 전부 사용하는 경로만 결과에 들어가고, 마지막에 정렬했기 때문에 알파벳 순으로 가장 빠른 경로를 선택할 수 있다.

### 시간복잡도 / 공간복잡도

* **시간복잡도:** 최악의 경우 O(N!)

  * 항공권을 사용하는 순서를 여러 가지로 탐색하기 때문에 가능한 경로의 수가 많아질 수 있다.
  * 모든 경로를 `allRoute`에 저장하고 마지막에 정렬하기 때문에 실제 수행 시간은 가능한 경로 수에 따라 더 증가할 수 있다.
* **공간복잡도:** O(N + K × N)

  * DFS의 재귀 깊이와 `visited` 배열에 O(N)이 필요하다.
  * 모든 가능한 경로를 `allRoute`에 저장하기 때문에 `K`개의 경로가 만들어진다면 경로 저장에 O(K × N)이 필요하다.

### 핵심 포인트

이번 문제에서는 단순 DFS보다 **백트래킹을 이용해서 잘못된 경로를 다시 되돌리는 것**이 중요했다.

특히 항공권 자체를 방문 처리하는 것이 중요했다. 같은 공항을 여러 번 방문할 수 있기 때문에 공항을 `visited`로 처리하면 안 되고, **항공권의 인덱스를 기준으로 사용 여부를 체크해야 한다.**

또한 여러 답이 존재할 때 알파벳 순으로 가장 앞선 경로를 반환해야 하기 때문에 모든 경로를 구해서 정렬하는 방법으로 해결했다.

다음에 비슷하게 모든 경우를 확인하면서 특정 조건을 만족하는 하나의 결과를 찾아야 하는 문제가 나오면 DFS + 백트래킹을 먼저 생각해볼 것 같다.

---

# 다음 주 목표

* 목표 문제 수: 3
* 집중할 유형: DFS / BFS / 백트래킹
