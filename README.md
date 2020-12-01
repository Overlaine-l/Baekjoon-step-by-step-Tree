# 백준 단계별로 풀어보기 - 트리
[백준](https://www.acmicpc.net)의 단계별로 풀어보기 중 [트리](https://www.acmicpc.net/step/23)의 몇몇 문제를 풀어본 곳입니다.  
코드에 등장하는 여러 함수들의 이름은 그때그때 생각나는대로 적었기에, 그 뜻이 덜 와닿을수도 있습니다.  
또한, 홈페이지에서 주어진 input의 형태와 코드에서 가정하는 input의 형태가 다를 수도 있습니다. 형태 변환은 어려운 문제가 아니고, 백준 단계별로 풀어보기에서 가정하는 input의 형태는 항상 동일하므로 이 부분은 엄밀하게 작성되지 않았을 수도 있습니다.  
모든 코드는 배워가는 입장에서 작성한 만큼, 더 좋은 방법이 있을 수 있습니다.

## 0. 들어가기 전에  
트리(tree)는 그래프라는 개념의 일부입니다. 다양한 방법으로 정의할 수 있고 그것들이 모두 동치임을 보일 수 있으므로, 여기서는 트리를 임의의 두 vertices를 잇는 path가 유일한 그래프로 정의하겠습니다.  
이 조건 때문에 topological sorting이 매우 단순해지며, uniqueness에 의해 shortest path를 구하는 것이 의미가 없어집니다. 그래서 트리만 주면 이러한 정보들을 단순하게 구할 수 있게 됩니다.  
그렇기에, 문제가 되는 경우는 그래프 안에 있는 트리입니다. 그 예시는 아래에서 만나보세요.  

## 1. 트리의 부모 찾기
- [문제](https://www.acmicpc.net/problem/11725)  
루트 없는 트리가 주어진다. 이때, 트리의 루트를 1이라고 정했을 때, 각 노드의 부모를 구하는 프로그램을 작성하시오.  
- 코드 
~~~
def parent_of_tree():
    raw = input().split('\n')
    V = int(raw.pop(0))
    
    adj = [[] for i in range(V)]
    
    for edge in raw:
        edge = list(map(int, edge.split(' ')))
        a = edge[0]
        b = edge[1]
        adj[b-1].append(a)
        adj[a-1].append(b)
    
    parent = [0 for i in range(V)]
    
    Q = [1]
    while Q:
        i = Q.pop(0)
        for j in adj[i-1]:
            parent[j-1] = i
            adj[j-1].remove(i)
            Q.append(j)
    
    return '\n'.join(str(i) for i in parent[1:])
~~~

- 설명  
이전과 같이 adj가 등장합니다. Nondirected tree이므로 path의 uniqueness로 인해 이 adj중 하나만이 부모가 될 수 있을 것입니다. adj의 원소에서 부모가 되는 원소를 찾는 과정은 다음과 같이 귀납적으로 진행할 수 있습니다:  
1. Vertex 1의 adj는 전부 1의 부모가 아닙니다. (1이 root이므로)
2. Vertesx 1의 adj의 한 vertex를 a라 합시다. 그러면 adj\[a]에서 1이 a의 부모이므로, 이를 저장합니다.
3. 1 자리에 a를, a 자리에 adj\[a]의 한 원소를 넣고 1과 2를 반복합니다.

