문제 주소: [https://algospot.com/judge/problem/read/TRAVERSAL](https://algospot.com/judge/problem/read/TRAVERSAL)

# 문제 요약

트리의 전위 순회와 중위 순회를 주어질 경우 후위 순회를 출력

# 문제 조건

* 테스트 케이스 &lt;= 100
* 노드 수 &lt;= 100
* 각 노드는 1000이하의 자연수, 같은 번호는 없다

# 문제 해결

트리의 전위/중위/후위 순회에 대해 정확하게 알고 있는 것이 문제 풀이에 도움이 된다. 문제에서도 설명을 하고 있다.

```
  A
 / \
B   C
```

위와 같은 간단한 이진 트리가 있을 경우 각 순회 방법은 다음과 같다

* 전위 순회: A B C
* 중위 순회: B A C
* 후위 순회: B C A

전위순회\(=preorder\)와 중위순회\(=inorder\)가 주어졌을 때 다음과 같은 특성을 볼 수 있다.

```
전위순회: 27 16 9 12 54 36 72
중위순회: 9 12 16 27 36 54 72

전위순회의 제일 첫 숫자가 root 이고,
중위 순회에서 root의 왼쪽 숫자들은 트리에서 root 기준 왼쪽 서브 트리, 오른쪽 숫자들은 오른쪽 서브트리가 된다.
```

이 특성을 이용해 재귀함수로 바로 왼쪽 서브 트리, 오른쪽 서브 트리, root 순서로 후위 순회\(=postorder\)를 출력할 수 있다.

다음  해결 코드에서 printPostOrder\(List&lt;Integer&gt; preorder, List&lt;Integer&gt; inorder\) 함수가 후위 순회를 출력하는 함수 이다.

```java
import java.io.BufferedReader;
import java.io.FileInputStream;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;

public class Main {

    public static void main(String[] args) throws Exception {
        System.setIn(new FileInputStream("sample_input.txt"));

        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int TC = Integer.parseInt(br.readLine());

        for (int test = 0; test < TC; test++) {
            int N = Integer.parseInt(br.readLine());

            // initialize front, mid list
            ArrayList<Integer> front = new ArrayList<Integer>();
            ArrayList<Integer> mid = new ArrayList<Integer>();

            StringTokenizer frontST = new StringTokenizer(br.readLine());
            StringTokenizer middleST = new StringTokenizer(br.readLine());

            for (int i = 0; i < N; i++) {
                front.add(Integer.parseInt(frontST.nextToken()));
                mid.add(Integer.parseInt(middleST.nextToken()));
            }

            // 후위탐색 출력
            printPostOrder(front, mid);
            System.out.println();
        }
    }

    public static void printPostOrder(List<Integer> preorder, List<Integer> inorder) {
        int preSize = preorder.size();

        // preorder가 비었다면 종료
        if (preSize == 0) {
            return;
        }

        // 루트는 preorder의 맨 앞 숫자
        int root = preorder.get(0);

        // 중위탐색 결과에서 루트의 왼쪽 서브 트리의 사이즈를 구한다. (왼쪽 서브트리 사이즈 == 루트의 index) 
        int L = find(inorder, root);

        // 오른쪽 서브 트리 크기는 전체 사이즈 - 루트 index - 1 
//        int R = preSize - L - 1;

        // 1. 왼쪽 서브 트리 출력
        printPostOrder(preorder.subList(1, L + 1), inorder.subList(0, L));
        // 2. 오른쪽 서브 트리 출력
        printPostOrder(preorder.subList(L + 1, preSize), inorder.subList(L + 1, preSize));

        // 3. 루트 출력
        System.out.print(root + " ");
    }

    public static int find(List<Integer> list, int num) {
        int size = list.size();

        for (int i = 0; i < size; i++) {
            if (list.get(i) == num) {
                return i;
            }
        }
        return -1;
    }
}
```



