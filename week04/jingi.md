> [!NOTE]
> 다음은 오답노트 예시 템플릿입니다. 자유롭게 수정해서 사용해주세요.
> 이 파일을 복사해서 `week0N/{영문이름}.md`로 저장한 뒤 작성하시면 됩니다.

# 4주차 문제 풀이 인증

## 기본 정보

- 이름: 홍진기
- 목표 문제 수: 3
- 실제 풀이 문제 수: 3

## 푼 문제 목록

| 번호 | 문제 이름 | 난이도 | 링크 | 풀이 여부 | 소요 시간 |
| --- | --- | --- | --- | --- | --- |
| 1 | 도넛과 막대 그래프 | level 2 | [프로그래머스](https://school.programmers.co.kr/learn/courses/30/lessons/258711) | X | 1시간 |
| 2 | 후보키 | level 2 | [프로그래머스](https://school.programmers.co.kr/learn/courses/30/lessons/42890) | X | 1시간 |
| 3 | 방금그곡 | level 2 | [프로그래머스](https://school.programmers.co.kr/learn/courses/30/lessons/17683) | O | 1시간 15분 |

---

# 오답노트

> 오답노트는 AI 답변이나 블로그 풀이를 그대로 복사하지 않고, 본인의 언어로 직접 작성해주세요.
> 문제별로 아래 양식을 복사해서 필요한 만큼 반복 작성하시면 됩니다.

## 문제 1

### 문제 정보

- 문제명: 도넛과 막대그래프
- 문제 링크: https://school.programmers.co.kr/learn/courses/30/lessons/258711
- 사용한 알고리즘 / 자료구조: 그래프, 구현

### 처음 접근 방식

우선 문제는 주어진 시간 내에 완벽하게 풀지 못했다. (그 이유는 "어려웠던 지점"에서 작성할 예정)

그래프 문제였기 때문에 주어진 입력값으로 그래프를 구현하면서 로직을 작성해봐야겠다는 생각이 들었는데, 주어진 입력값으로 그래프를 구현하는 것이 어려워 시간이 지연되고 문제를 풀지 못했다.

### 풀이 방법

핵심은 문제에서 요구하는 **생성된 노드, 막대 그래프, 8자 그래프, 도넛 그래프**의 특징을 이해하는 것에 있었다.

그리고 또다른 핵심은 **그래프를 모두 다 구현하지 않고 푸는 것**, **특정 정점을 통해 그 그래프의 특성을 파악하는 것**이다.

위 그래프를 판별할 줄 알아야 했는데, 그것은 각 정점마다 들어오는 간선과 나가는 간선으로 그래프를 판별할 수 있었다.

- 새로 생성되는 정점의 경우, 문제의 조건에 나가는 간선의 개수가 최소 2개이다.
- 막대 그래프의 경우, 마지막 노드가 나가는 간선이 존재하지 않는다는 고유한 특징이 존재한다.
- 8자 그래프의 경우, 중심이 되는 한 노드가 들어오는 간선과 나가는 간선을 2개씩 갖는다.

따라서 최종 코드는 다음과 같다. 입력값에 따라 각 정점마다 들어오는 간선의 개수와 나가는 간선의 개수를 저장하고, 반복문을 통해 모든 정점을 순회하며, 정점이 그래프의 조건에 맞는 간선을 보유하고 있을 경우 해당 그래프의 개수를 갱신해나가는 구조이다. 그리고 도넛 그래프의 경우 별다른 특징이 없으므로 전체 그래프의 개수에서 다른 종류의 그래프 개수를 모두 빼는 방식으로 그래프 수를 계산한다.

최종 코드는 아래와 같다.

```java
class Solution {
    
    static int[] in = new int[1000001];
    static int[] out = new int[1000001];
    
    public int[] solution(int[][] edges) {
        int[] answer;
        int newNode = 0;
        int eightGraph = 0;
        int stickGraph = 0;
        int doughnutGraph = 0;
        
        for(int i = 0; i < edges.length; i++){
            in[edges[i][1]]++;
            out[edges[i][0]]++;
        }
        
        for(int i = 1; i <= 1000000; i++){
            if(isNewNode(in[i], out[i])){
                newNode = i;
            } else if (isEight(in[i], out[i])){
                eightGraph++;
            } else if (isStick(in[i], out[i])){
                stickGraph++;
            } 
        }
        
        // 도넛 노드는 [전체 그래프의 개수 - 8자 그래프 - 막대 그래프]
        doughnutGraph = out[newNode] - eightGraph - stickGraph;
        
        answer = new int[] {newNode, doughnutGraph, stickGraph, eightGraph};
        
        return answer;
    }
    
    // 도넛 그래프 판별
    
    // 8자 그래프 판별
        // 가운데 노드는 in 2, out 2
    public boolean isEight(int in, int out){
        return in >= 2 && out == 2;
    }
    
    // 막대 그래프 판별
        // 끝 노드는 out 0
    public boolean isStick(int in, int out){
        return in >= 1 && out == 0;
    }
    
    // 생성된 정점 판별
        // in 0, out >= 2
    public boolean isNewNode(int in, int out){
        return in == 0 && out >= 2;
    }
}
```

### 어려웠던 지점

1. 그래프를 바라보는 접근 방식

    문제에서 요구하는 각 종류의 그래프를 구분할 수 있어야 했는데, 나는 이를 **싸이클**을 기준으로 판단하고자 했다.

    하지만 싸이클로 각 종류를 판단하는 것에도 어려움이 있었다. 새로 추가된 노드와 막대 그래프는 싸이클이 없고, 8자 그래프와 도넛 그래프는 싸이클이 존재하지만 더 세부적으로 8자 그래프와 도넛 그래프를 구분하는 방법을 생각하는 데에 어려움이 있었고, 결과적으로는 이 싸이클 또한 그래프를 구현해서 풀어야 했다.

3. 도넛 그래프

    각 정점에 들어오는 간선과 나가는 간선을 기준으로 그래프를 판단해야 한다는 것은 알았지만, 도넛 그래프는 이 두 간선을 통해 다른 그래프와 비교하여 판단하는 로직을 떠올리기 힘들었다.

2. 예외 케이스

    문제를 해결한 이후에는 딱 한 가지 테스트 케이스만 통과가 되지 않았다.

    많은 시간 여러 케이스를 예상해보면서 고민을 해봤지만, 정답을 알아내지 못해서 질문 게시판을 봤는데, 정답은 "노드의 번호가 1부터 순차적으로 주어진다는 보장이 없다."라는 것이었다.

### 해결 방법

1. 오랜 시간을 생각하며 별도의 그래프 구현 없이 문제에서 제공하는 정보로만 그래프를 구분할 수 있다는 것을 떠올렸다.
2. 구현하면 어떻게든 도넛 그래프를 판별하는 규칙을 정의해보고자 했지만, 결국 오랜 시간 해결하지 못해서 AI를 통해 힌트를 "구할 필요가 없다"라는 정답을 찾아냈다.
3. 이 부분도 시간 내에 생각하지 못했다. 결국 질문 게시판을 통해 정답을 알아냈다.

### 시간복잡도 / 공간복잡도

- 시간복잡도:
- 공간복잡도:

### 새롭게 알게 된 점

사실 그래프 문제는 그동안 BFS, DFS, 다익스트라 문제를 정말 많이 풀어서 무조건 그래프 문제는 그래프를 그려봐야겠다는 편견이 있어서 더욱 어려웠던 것 같다. 
- 그래프 문제지만 그래프를 구현하지 않아도 된다는 것
- 도넛 그래프를 분류해야 하지만 굳이 분류할 필요가 없다는 것
- 노드의 번호가 순차적으로 제공된다는 보장이 없다는 것
모두 내가 가진 편견이었다. 이러한 편견을 갖고 문제에 접근하면 안된다는 것을 체감하게 된 문제였다.

또한 이번 문제는 결국 정해진 알고리즘이 없는 문제였다. 그렇기 때문에 정답을 찾아내는 과정에서 확신을 가지지 못하고, '이 접근 방식이 맞나?'라는 의문을 많이 가졌다. 편견 없이, 확실한 근거를 기반으로 풀이 방식을 생각해나가야겠다는 것을 다시한번 체감하게 된 문제였다.

---

## 문제 2

### 문제 정보

- 문제명: 후보키
- 문제 링크: https://school.programmers.co.kr/learn/courses/30/lessons/42890
- 사용한 알고리즘 / 자료구조: 백트래킹, dfs

### 처음 접근 방식

문제의 입력값과 요구 사항을 고려하여 백트래킹을 통해 문제를 해결할 수 있다고 판단했다.

### 풀이 방법

문제를 보고 간단하게 생각할 수 있다.

칼럼의 개수를 N이라고 했을 때, 1개부터 N개의 칼럼 조합을 모두 탐색하며 최소성과 유일성을 만족하는 조합을 찾아서 저장해나간다.

따라서 1 ~ N까지 반복문을 통해 각 개수의 조합을 백트래킹을 통해 구하면 된다.

이때, 최소성을 판별하는 방법은 **새로운 칼럼 조합이 기존의 후보키 + 다른 컬럼을 포함하는지**를 검사한다.

이 작업을 반복하면서 최종적으로 후보키의 개수를 출력함으로써 문제를 해결할 수 있다.

최종 코드는 다음과 같다.

```java
import java.util.*;

class Solution {
    
    static int answer = 0;
    static boolean[] visited;
    static List<Set<Integer>> candidateKeys = new ArrayList<>();
    
    public int solution(String[][] relation) {
        
        // 칼럼의 길이
        int colLength = relation[0].length;
        visited = new boolean[colLength];
    
        // 칼럼의 길이만큼 백트래킹 반복
        for (int i = 1; i <= colLength; i++){
            dfs(0, i, new int[i], relation, 0);
        }    
        // 결과 산출
        return candidateKeys.size();
    }
    
    // 백트래킹 (idx : 백트래킹 진행한 깊이, depth : 목표 깊이)
    void dfs(int idx, 
             int depth, 
             int[] selectedCol, 
             String[][] relation,
             int start
            ){
        
        // 경우를 한번 모두 탐색할 경우
        if (idx == depth){
            
            // 최소성과 유일성을 확인
            if (isUnique(selectedCol, relation) && isMinimal(selectedCol)){
                // 만족할 경우 다른 후보키에 추가 (추후 다른 값의 최소성을 판별하는 데 활용)
                Set<Integer> set = new HashSet<>();

                for(int cols : selectedCol){
                    set.add(cols);
                }

                candidateKeys.add(set);
            }
        } else {
            for(int i = start ; i < relation[0].length; i++){
                if (!visited[i]){
                    visited[i] = true;
                    selectedCol[idx] = i;
                    dfs(idx + 1, depth, selectedCol, relation, i+1);
                    visited[i] = false;
                }
            }
        }
    }
    
    boolean isUnique(int[] selectedCol, String[][] relation){
                
        // 칼럼 개수 확인
        int colCount = selectedCol.length;
        
        // Set 활용
        Set<String> set = new HashSet<>();
        
        // 칼럼 조합의 row 반복 후 map 활용하여 중복 검사
        for(int i = 0 ; i < relation.length; i++){
            
            StringBuilder sb = new StringBuilder();

            // 한 행에 선택된 열의 데이터를 조합
            for(int j = 0 ; j < colCount; j++){
                sb.append(relation[i][selectedCol[j]]).append("|");
            }
            
            String val = sb.toString();
            
            if(set.contains(val)){
                return false;
            } else {
                set.add(val);
            }
        }
        
        return true;
    }
    
    boolean isMinimal(int[] selectedCol){
        Set<Integer> set = new HashSet<>();
        
        for(int cols : selectedCol){
            set.add(cols);
        }
        
        for(Set<Integer> candidateKey : candidateKeys){
            if(set.containsAll(candidateKey)){
                return false;
            }             
        }
        return true;
    }
    
}
```

### 어려웠던 지점

1. 서로 다른 칼럼의 데이터가 중복되는 경우는 생각하지 못했다. 

예를 들어, 두 행에 대해 A 칼럼의 데이터가 각각 "a", "bc" 이고, B 칼럼의 데이터가 각각 "ab", "c"라고 한다면, 내 코드대로 이 데이터를 조합했을 때 데이터가 중복된다고 판별될 수 있다.

따라서 아래와 같이 칼럼의 데이터들을 합칠 때, `.append("|")`를 추가하여 구분하는 것으로 수정했다.

2. 최소성을 판별하는 로직을 구현하지 못했다.

최소성은 유일성을 가진 "유일성을 가진 키를 구성하는 속성(Attribute) 중 하나라도 제외하는 경우 유일성이 깨지는 것을 의미한다."라고 되어있다.

이 문장을 그대로 구현하려다가 최소성을 판별하는 로직을 구현하는 데에 어려움을 느꼈던 것 같다.

### 해결 방법

결국 나름대로 최소성을 판별하는 로직을 구현해봤지만, 테스트 케이스에서 계속 틀려 정해진 시간 내에 해결하지 못하고 AI를 통해 힌트를 참고하여 해결했다.

### 시간복잡도 / 공간복잡도

- 시간복잡도:
- 공간복잡도:

### 새롭게 알게 된 점

개수를 파악하는 데만 급급하여 처음에는 기존에 구했던 후보키를 최소성 판별을 위해 저장할 생각을 하지 못했다.

최소성을 판별하는 로직을 작성하는 것은 구현의 영역인 것 같다. 아직은 문제의 요구 사항을 파악하고 이를 코드로 옮기는 역량이 부족한 것 같다.

---

## 문제 3

- **문제명:**방금그곡
- **문제 링크:**https://school.programmers.co.kr/learn/courses/30/lessons/17683
- **알고리즘 / 자료구조:** 구현

### 접근 및 시행착오

우선 입력값을 전처리 하는 로직을 먼저 작성하였고, 명확한 알고리즘이 떠오르지 않아 문제를 읽으면서 구현해나갔다.

구현하는 과정에서는 꽤 긴 시간 고민했던 부분이 있었다.

가장 많이 고민했던 것은 `#`에 대한 처리였다.

처음에는 안일하게 악보의 음들을 한 문자씩으로만 처리하다가, `#` 문자일 경우 인덱스에 포함하지 않는 방식으로 처리했다.

하지만 이렇게 될 경우, 음을 비교하는 과정에서 `C`와 `C#`을 비교하는 것이 어려웠다. 따라서 `C#`, `D#`, `F#`, `G#`, `A#`을 각각 `c`, `d`, `f`, `g`, `a`로 치환함으로써 한 문자로 만들어 해결했다.

### 최종 풀이

소스코드는 다음과 같다.

```java
import java.util.*;

class Solution {
    
    static class Song {
        String title;
        String m;
        int time;
        
        public Song(String title, String m, int time){
            this.title = title;
            this.m = m;
            this.time = time;
        }
    }
    
    public String solution(String m, String[] musicinfos) {
        String answer = "";
        StringTokenizer st;
        
        Song[] list = new Song[musicinfos.length];
        
        for(int i = 0; i < musicinfos.length; i++){
            st = new StringTokenizer(musicinfos[i], ",");
            
            String[] start = st.nextToken().split(":"); 
            String[] end = st.nextToken().split(":");
            
            int hh = Integer.parseInt(end[0]) - Integer.parseInt(start[0]);
            int mm = Integer.parseInt(end[1]) - Integer.parseInt(start[1]);
            
            // 시간을 분 단위로 계산
            int time = hh * 60 + mm;
            String title = st.nextToken();
            String info = change(st.nextToken());
           
            // 제목, 시간만큼 실행된 악보 저장            
            list[i] = new Song(title, parseM(time, info), time);
        }
        
        // m 정규화
        m = change(m);
        
        // 길이 긴 순으로 정렬
        Arrays.sort(list, new Comparator<Song>(){
            @Override
            public int compare(Song s1, Song s2){
                return s2.time - s1.time;
            }
        });
        
        for(int i = 0 ; i < musicinfos.length; i++){
            System.out.println(list[i].title + ":" + list[i].m);
        }
        
        // 구간 탐색 (substring)
        for(int i = 0 ; i < musicinfos.length; i++){
            
            Song current = list[i];            

            if (current.m.contains(m)){
                // 반환
                return current.title;
            }
        }    
        return "(None)";
    }
    
    static String parseM(int length, String m){
        
        StringBuilder sb = new StringBuilder();
        int slen = m.length();
        
        for (int i = 0, j = 0 ; i < length; i++, j = (j+1) % slen){
            
            if(m.charAt(j) == '#'){
                i--;
            }
            sb.append(m.charAt(j));
        }
        
        return sb.toString();
    }
    
    static String change(String s){
        
        return s.replace("C#", "c")
                .replace("D#", "d")
                .replace("F#", "f")
                .replace("G#", "g")
                .replace("A#", "a");

    }
    
}
```

### 복잡도

- 시간복잡도: O(NlogN+T) (N은 음악 개수, T는 악보 길이)
- 공간복잡도: O(N+T+M) (N은 음악 개수, T는 악보 길이, M은 치환된 악보 길이)

### 핵심 포인트

이 문제의 핵심 포인트는 문제의 요구 사항을 구현하는 것도 있지만, 더 구체적으로 **문제에서 주어지는 예외적인 문자를 치환하여 해결할 수 있어야 하는 것**이라고 생각한다.

---

# 다음 주 목표

- 목표 문제 수:
- 집중할 유형: