문제 주소: [https://algospot.com/judge/problem/read/ROUTING](https://algospot.com/judge/problem/read/ROUTING)

주어진 그림을 보면 가중치 그래프 라는 것을 알 수 있다.

# 문제요약

처음 노드에서 마지막 노드까지 신호를 전송하는데, 거쳐가는 엣지만큼 노이즈가 증폭되어 전송이 된다. 노이즈 증폭을 최소화하는 것이 목표이다.

# 주어진 조건

* 제한시간: 2000ms
* 메모리: 64MB
* 입력
  * Testcase = 50
  * 노드 수 &lt;= 10000
  * 엣지 수 &lt;= 20000

# 문제 해결

전수탐색을 한다면 50 \* 10000\* 20000 = 1000000000 연산에 가지치기를 한다해도 시간초과될 것으로 보인다.

다익스트라 알고리즘을 적용하기에 딱 맞는 문제이다.

> Dijkstra's algorithm is an algorithm for finding the shortest paths between nodes in a graph
>
> 다익스트라 알고리즘은 노드 간의 최단 경로 찾기 알고리즘! \(방향, 무방향 상관없음\)

다익스트라 알고리즘의 핵심은 우선순위 큐이며 Java에서는 PriorityQueue를 기본 제공한다.

그래프 문제는 해결하는데 두 가지 자료구조를 사용할 수 있다.

1. 인접 행렬
2. 인접 리스트

인접 행렬은 2차원 배열의 크기를 미리 잡아놓고 direct access 하기때문에 메모리는 상대적으로 많이 사용하고 접근이 매우 빠른 반면, 인접 리스트는 ArrayList의 배열로 사용하기 때문에 메모리는 상대적으로 적게 사용하고, 접근이 느리다.

Routing 문제를 접근할 때 이 사실을 간과하고 인접 행렬로 구현했다.

```java
class Node {}
Node[][] map = new Node[10000][10000]
```

이 경우 메모리는 Class reference를 위한 4byte \* 10000 \* 10000 이므로 400MB의 메모리공간을 차지하게 된다.

메모리 효율성을 위해 인접리스트로 바꾸면 아래와 같다

```java
class Edge {}
class Node {}
ArrayList<Edge> edges[] = new ArrayList<Edge>[10000];
```



전체 Java코드는 아래와 같다.

```java
import java.io.BufferedReader;
import java.io.FileInputStream;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.PriorityQueue;
import java.util.StringTokenizer;

class Edge {
    public int to;
    public double noise;
    
    public Edge(int to, double noise) {
        this.to = to;
        this.noise = noise;
    }
}

class Node implements Comparable<Node> {
    public int index;
    public double noise;
    
    public Node(int index, double noise) {
        this.index = index;
        this.noise = noise;
    }
    
    @Override
    public int compareTo(Node o) {
        return Double.compare(this.noise, o.noise);
    }
}

public class Main {
    static ArrayList<Edge> edges[];
    static double result[];
    static PriorityQueue<Node> pq = new PriorityQueue<Node>();

    public static void main(String[] args) throws Exception {
//        long start = System.currentTimeMillis();
        System.setIn(new FileInputStream("sample_input.txt"));
        
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        
        int N = Integer.parseInt(br.readLine());
        
        for (int i = 0; i < N; i++) {
            StringTokenizer st = new StringTokenizer(br.readLine());
            int V = Integer.parseInt(st.nextToken());
            int E = Integer.parseInt(st.nextToken());

            // initialize map
            edges = new ArrayList[V];
            for (int j = 0; j < V; j++) {
                edges[j] = new ArrayList<Edge>();
            }
            
            for (int j = 0; j < E; j++) {
                st = new StringTokenizer(br.readLine());
                int from = Integer.parseInt(st.nextToken());
                int to = Integer.parseInt(st.nextToken());
                double noise = Double.parseDouble(st.nextToken());
                
                edges[from].add(new Edge(to, noise));
                edges[to].add(new Edge(from, noise));
            }
            
            
            // PriorityQueue 초기화
            pq.clear();
            pq.add(new Node(0, 1.0));
            result = new double[V];
            
            while(!pq.isEmpty()) {
                Node current = pq.poll();
                
                for (Edge edge : edges[current.index]) {
                    if (edge.to == 0) {
                        continue;
                    }
                    if (result[edge.to] == 0) {
                        result[edge.to] = current.noise * edge.noise;
                        pq.add(new Node(edge.to, result[edge.to]));
                    } else {
                        double prevVal = result[edge.to];
                        if (prevVal > current.noise * edge.noise) {
                            result[edge.to] = current.noise * edge.noise;
                            pq.add(new Node(edge.to, result[edge.to]));
                        }
                    }
                }
            }
            System.out.printf("%.10f\n", result[V - 1]);
        }
        
        br.close();
        //System.out.println(System.currentTimeMillis() - start + "ms");
    }

}
```



