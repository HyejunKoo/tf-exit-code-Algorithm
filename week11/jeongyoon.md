# 11주차 문제 풀이 인증

## 기본 정보

* 이름: 주정윤
* 목표 문제 수: 3
* 실제 풀이 문제 수: 3

---

# 문제 요약

| 번호 | 문제 이름     | 난이도  | 링크                                                                       |
| -- | --------- | ---- | ------------------------------------------------------------------------ |
| 1  | [3차] 압축   | Lv.2 | [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/17684) |
| 2  | 실패율       | Lv.1 | [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/42889) |
| 3  | 게임 맵 최단거리 | Lv.2 | [문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/1844)  |

---

# 오답노트

## 문제 1

* **문제명:** [3차] 압축
* **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/17684
* **알고리즘 / 자료구조:** 해시, 문자열, 구현

### 접근 및 시행착오

LZW 압축 방식에 따라 현재 입력에서 사전에 등록되어 있는 가장 긴 문자열을 찾고, 해당 문자열의 색인 번호를 결과에 추가하는 방식으로 접근했다.

현재 문자열 `w`에 다음 문자를 붙인 `confirm`이 사전에 존재하는지 확인했다.

* 존재하면 `w`를 `confirm`으로 변경한다.
* 존재하지 않으면 현재 `w`의 색인을 결과에 추가한다.
* `w + 다음 문자`를 새로운 사전에 등록한다.

처음에는 `A~Z`를 사전에 등록해야 하므로 `HashMap`에 문자와 색인 번호를 저장했다.

### 최종 풀이

1. `HashMap`에 `A~Z`를 각각 `1~26`의 색인으로 등록한다.
2. 현재 위치 `i`에서 첫 번째 문자를 `w`에 저장한다.
3. 다음 문자를 붙인 `confirm`이 사전에 존재하는지 확인한다.
4. 사전에 존재하면 `w`를 `confirm`으로 변경하고 계속 탐색한다.
5. 사전에 없는 문자열을 발견하면 현재 `w`의 색인을 결과에 추가한다.
6. 새로운 문자열 `confirm`을 사전에 등록한다.
7. 마지막까지 처리한 경우 마지막 `w`의 색인도 결과에 추가한다.
8. `ArrayList<Integer>`를 `int[]`로 변환해서 반환한다.

### 내가 푼 코드

```java
import java.util.*;

class Solution {
    public int[] solution(String msg) {
        
        HashMap<String, Integer> map = new HashMap<>();
        ArrayList<Integer> list = new ArrayList<>();
        
        // A~Z 까지 추가
        for(int i=0; i<26; i++){
            map.put(String.valueOf((char)('A' + i)), i+1);
        }
        
        String[] msg_split = msg.split("");
        
        int index = 27;
        int i = 0;
            
        while(i < msg_split.length){
            String w = ""; // 현재 입력
            String confirm = "";
                
            w += msg_split[i]; // 현재입력에 첫 글자 추가
            int j = i+1;
            
            while(j < msg_split.length){
                confirm = w + msg_split[j];
                
                if(map.containsKey(confirm)){
                    w = confirm;
                    j++;
                } else {
                    list.add(map.get(w));
                    map.put(confirm, index);
                    index++;
                    break;
                }
            
            }
            
            i=j;
            
            if(j==msg_split.length){
                list.add(map.get(w));
            }
            
        }
        
        int[] answer = new int[list.size()];
        
        for(int a =0; a<list.size(); a++){
            answer[a] = list.get(a);
        }
        
        return answer;
    }
}
```

### 복잡도

* 시간복잡도: O(N²)
* 공간복잡도: O(N)

※ `N`은 입력 문자열의 길이이다.

### 핵심 포인트

LZW에서는 **현재 문자열 `w`에 다음 문자를 붙였을 때 사전에 존재하는지 확인하면서 가장 긴 문자열을 찾는 것**이 핵심이다.

`HashMap`을 사용하면 특정 문자열이 사전에 존재하는지와 해당 색인 번호를 빠르게 확인할 수 있다.

---

## 문제 2

* **문제명:** 실패율
* **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/42889
* **알고리즘 / 자료구조:** 해시, 정렬, 구현

### 접근 및 시행착오

각 스테이지의 실패율을 구하기 위해 해당 스테이지에서 아직 클리어하지 못한 사람의 수와 해당 스테이지에 도달한 사람의 수를 각각 계산했다.

실패율은 다음과 같이 계산했다.

`실패율 = 해당 스테이지에 머물러 있는 사람 수 / 해당 스테이지에 도달한 사람 수`

각 스테이지의 실패율을 `HashMap<Integer, Double>`에 저장한 후, 스테이지 번호를 리스트로 만들어 실패율이 높은 순서대로 정렬했다.

실패율이 같은 경우에는 스테이지 번호가 작은 순서대로 정렬했다.

### 최종 풀이

1. `1`번부터 `N`번 스테이지까지 반복한다.
2. `stages`를 순회하면서 현재 스테이지에 머물러 있는 사람 수를 센다.
3. 현재 스테이지 이상에 도달한 사람의 수도 센다.
4. 도달한 사람이 없다면 실패율을 `0`으로 설정한다.
5. 그렇지 않다면 `count / all_count`로 실패율을 계산한다.
6. 스테이지 번호와 실패율을 `HashMap`에 저장한다.
7. 스테이지 번호를 리스트로 만들어 실패율이 높은 순서로 정렬한다.
8. 실패율이 같다면 스테이지 번호가 작은 순서대로 정렬한다.
9. 정렬된 결과를 `int[]`로 변환해서 반환한다.

### 내가 푼 코드

```java
import java.util.*;

class Solution {
    public int[] solution(int N, int[] stages) {
        
        HashMap<Integer, Double> map = new HashMap<>();
        
        for(int i=1; i<=N; i++){
            int count = 0; //클리어 못한 사람
            int all_count = 0;
            for(int stage : stages){
                if(i == stage) count++;
                if(stage >= i) all_count++;
            }
            
            double num = 0;
            
            if(all_count == 0){
                num = 0;
            } else {
                num = (double)count / all_count;
            }
            
            map.put(i, num);
        }
        
        ArrayList<Integer> list = new ArrayList<>(map.keySet()); 
        list.sort((a, b) ->{
            if(map.get(a) == map.get(b)){
                return a-b;
            }
            
            return Double.compare(map.get(b), map.get(a));
        });
        
        int[] answer = new int[list.size()];
        
        for(int i=0; i<list.size(); i++){
            answer[i] = list.get(i);
        }
        return answer;
    }
}
```

### 복잡도

* 시간복잡도: O(N × M + N log N)
* 공간복잡도: O(N)

※ `N`은 스테이지의 개수, `M`은 `stages`의 길이이다.

### 핵심 포인트

실패율을 구할 때 **현재 스테이지에 있는 사람 수**와 **현재 스테이지에 도달한 사람 수**를 구분해야 한다.

`stage >= i`인 경우 현재 스테이지에 도달한 사람으로 계산하고, `stage == i`인 경우에는 아직 해당 스테이지를 클리어하지 못한 사람으로 계산한다.

---

## 문제 3

* **문제명:** 게임 맵 최단거리
* **문제 링크:** https://school.programmers.co.kr/learn/courses/30/lessons/1844
* **알고리즘 / 자료구조:** BFS, 큐

### 접근 및 시행착오

시작점 `(0, 0)`에서 도착점까지 이동하는 최단거리를 구해야 하기 때문에 BFS를 사용했다.

BFS는 현재 위치에서 이동할 수 있는 위치를 순서대로 탐색하기 때문에, 처음 도착점에 도달했을 때 최단거리를 구할 수 있다.

큐에는 현재 위치의 `x`, `y` 좌표와 지금까지 이동한 거리 `dist`를 함께 저장했다.

또한 이미 방문한 칸을 다시 방문하지 않도록 `boolean[][] visited`를 사용했다.

### 최종 풀이

1. 상하좌우 이동을 위한 `dx`, `dy` 배열을 만든다.
2. 큐에 시작 위치 `(0, 0)`과 거리 `1`을 넣는다.
3. 시작 위치를 방문 처리한다.
4. 큐에서 현재 위치를 꺼낸다.
5. 현재 위치가 목적지라면 이동 거리를 반환한다.
6. 상하좌우 네 방향으로 다음 위치를 계산한다.
7. 맵의 범위를 벗어나거나 벽인 경우 이동하지 않는다.
8. 이미 방문한 위치라면 이동하지 않는다.
9. 방문하지 않은 이동 가능한 위치를 방문 처리하고 큐에 넣는다.
10. BFS가 끝날 때까지 목적지에 도착하지 못했다면 `-1`을 반환한다.

### 내가 푼 코드

```java
import java.util.*;

class Solution {
    public int solution(int[][] maps) {
        int answer = 0;
        
        int[] dx = {0, 0, -1, 1};
        int[] dy = {-1, 1, 0, 0};
        
        Deque<int[]> queue = new ArrayDeque<>();
        boolean[][] visited = new boolean[maps.length][maps[0].length];
        
        queue.offer(new int[]{0, 0, 1});
        visited[0][0] = true;
        
        while(!queue.isEmpty()){
            int[] now = queue.poll();
            
            int x = now[0];
            int y = now[1];
            int dist = now[2];
            
            //도착했으면 리턴
            if(x == maps.length-1 && y == maps[0].length-1){
                return dist;
            }
            
            for(int i=0; i<4; i++){
                int nx = x + dx[i];
                int ny = y + dy[i];
                
                // 범위 밖이면 넘어가기
                if(nx < 0 || ny < 0 || nx >= maps.length || ny >= maps[0].length){
                    continue; 
                }
                
                // 벽 체크
                if(maps[nx][ny]==0){
                    continue;
                }
                
                // 이미 방문했으면 넘어가기
                if(visited[nx][ny]){
                    continue;
                }
                
                //방문처리
                visited[nx][ny] = true;
                queue.offer(new int[]{nx, ny, dist+1});
            }
        
        }
        
        return -1;
    }
}
```

### 복잡도

* 시간복잡도: O(N × M)
* 공간복잡도: O(N × M)

※ `N`은 맵의 행 개수, `M`은 맵의 열 개수이다.

### 핵심 포인트

최단거리를 구하는 문제에서는 **BFS**를 사용할 수 있다.

각 칸을 처음 방문할 때 최단거리가 결정되므로 `visited` 배열을 이용해 중복 방문을 막을 수 있다.

큐에서 꺼낸 위치가 목적지라면 BFS의 특성상 해당 `dist`가 최단거리이므로 바로 반환할 수 있다.

---

# 다음 주 목표

* 목표 문제 수: 3
* 집중할 유형: DFS / BFS
