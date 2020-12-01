# 백준 단계별로 풀어보기 - 트리
[백준](https://www.acmicpc.net)의 단계별로 풀어보기 중 [트리](https://www.acmicpc.net/step/23)의 몇몇 문제를 풀어본 곳입니다.  
코드에 등장하는 여러 함수들의 이름은 그때그때 생각나는대로 적었기에, 그 뜻이 덜 와닿을수도 있습니다.  
또한, 홈페이지에서 주어진 input의 형태와 코드에서 가정하는 input의 형태가 다를 수도 있습니다. 형태 변환은 어려운 문제가 아니고, 백준 단계별로 풀어보기에서 가정하는 input의 형태는 항상 동일하므로 이 부분은 엄밀하게 작성되지 않았을 수도 있습니다.  
모든 코드는 배워가는 입장에서 작성한 만큼, 더 좋은 방법이 있을 수 있습니다.

## 0. 들어가기 전에  
트리(tree)는 그래프라는 개념의 일부입니다. 다양한 방법으로 정의할 수 있고 그것들이 모두 동치임을 보일 수 있으므로, 여기서는 트리를 임의의 두 vertices를 잇는 path가 유일한 그래프로 정의하겠습니다.  
이 조건 때문에 topological sorting이 매우 단순해지며, uniqueness에 의해 shortest path를 구하는 것이 의미가 없어집니다. 그래서 트리만 주면 이러한 정보들을 단순하게 구할 수 있게 됩니다.  
그렇기에, 문제가 되는 경우는 그래프 안에 있는 트리입니다. 이에 대한 예시로는 Krull의 greedy algorithm이 있습니다. 이는 대부분의 학부 이산수학 수업에서 빠지지 않고 등장하는 매우 유명한 내용입니다.  
트리가, edge와 vertex가 추가적인 의미를 가지는 경우도 있습니다. 이에 대한 예시로는, 여기서 다루지 않았던 heap과 binary search tree가 있습니다. 두 주제 모두 백준에서 관련 문제를 찾아볼 수 있으며, heap을 구현하는 것은 문제풀이의 의도에 맞지 않아 제 깃헙에 올리지는 않았지만 스스로 해보시면 좋은 공부가 됩니다.  

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
이전과 같이 adj가 등장합니다. Nondirected tree이므로 path의 uniqueness로 인해 이 adj중 하나만이 부모가 될 수 있을 것입니다. adj의 원소에서 부모가 되는 원소를 찾는 과정은 다음과 같이 귀납적으로 진행할 수 있습니다.  
1. Vertex 1의 adj는 전부 1의 부모가 아닙니다. (1이 root이므로)
2. Vertesx 1의 adj의 한 vertex를 a라 합시다. 그러면 adj\[a]에서 1이 a의 부모이므로, 이를 저장합니다.
3. 1 자리에 a를, a 자리에 adj\[a]의 한 원소를 넣고 1과 2를 반복합니다.

## 2. 트리의 지름
- [문제](https://www.acmicpc.net/problem/1167)  
트리의 지름이란, 트리에서 임의의 두 점 사이의 거리 중 가장 긴 것을 말한다. 트리의 지름을 구하는 프로그램을 작성하시오.

- 코드 
~~~
def diameter_of_tree():
    raw = input().split('\n')
    V = int(raw.pop(0))
    
    weight = [['Nil' for i in range(V)] for j in range(V)]
    adj = [[] for i in range(V)]
    for edge in raw:
        edge = list(map(int, edge.split(' ')))
        v = edge[0]
        i = 1
        while edge[i] != -1 and i < len(edge):
            a = edge[i]
            weight[v-1][a-1] = edge[i+1]
            adj[v-1].append(a)
            i += 2
        del v
    
    leafs = []
    for v in range(1,V+1):
        if len(adj[v-1]) == 1:
            leafs.append(v)
     
    diameter = 0
    while leafs:
        u = leafs.pop()
        near = adj.copy()
        Q = [u]
        dist = [0 for i in range(V)]
        while Q:
            v = Q.pop(0)
            for w in near[v-1]:
                dist[w-1] = dist[v-1] + weight[v-1][w-1]
                near[w-1].remove(v)
                Q.append(w)
                
        diameter = max(diameter, max(dist))
        
    return diameter
~~~

- 설명  
임의의 두 점 사이의 거리를 전부 구해서 최댓값을 내도 되지만, 직관적으로 최댓값은 두 점이 모두 leaf인 경우에 일어난다는 것을 바로 알 수 있을 겁니다.  
그러므로 leaf들을 모은 후, 각 leaf에서 다른 leaf까지의 거리를 모두 구한 뒤 그 중에서 최댓값을 구하면 계산량을 줄일 수 있습니다.  
지금까지는 DFS를 이용해 shortest path만을 구했기에 longest path를 구하는 이번 문제가 낯설 수 있습니다. 하지만 path가 unique하므로, 두 점 사이의 shortest path가 곧 longest path가 됩니다.  
-참고
이와 같이, 한 점에서 동시에 여러 점까지의 (shortest) path의 **길이**을 구할 때는, BFS가 좋은 선택입니다. 왜냐하면 BFS는 그 특성상 root로부터 시작되는 shortest path 길이가 n인 점들을 모두 탐색한 후 n+1인 점들을 모두 탐색하기 때문입니다.  

## 3. 이진 검색 트리
- [문제](https://www.acmicpc.net/problem/5639)  
이진 검색 트리를 전위 순회한 결과가 주어졌을 때, 이 트리를 후위 순회한 결과를 구하는 프로그램을 작성하시오.  
- 코드 
~~~
def bst_postorder():
    pre = list(map(int, input().split('\n')))
    V = len(pre)
    
    # structuring binary search tree from its postorder arrangement
    plr = [['Nil','Nil','Nil'] for i in range(V)]   # parent, left, right
    plr[0][0] = -1  # this simplifies below structuring code
    for i in range(V-1):
        j = i
        k = i
        while pre[j] < pre[i+1] and j != -1:
            k = j
            j = plr[j][0]
        if j == k:
            plr[k][1] = i+1
            plr[i+1][0] = k
        else:
            plr[k][2] = i+1
            plr[i+1][0] = k
    
    # constructuring postorder of bst using recursive code
    def postorder_print(elements, structure, i):
        if structure[i][1] == structure[i][2] == 'Nil':
            return [elements[i]]
        if structure[i][2] == 'Nil':
            return postorder_print(elements, structure, structure[i][1]) + [elements[i]]
        if structure[i][1] == 'Nil':
            return postorder_print(elements, structure, structure[i][2]) + [elements[i]]
        return postorder_print(elements, structure, structure[i][1]) + postorder_print(elements, structure, structure[i][2]) + [elements[i]]


    return postorder_print(pre, plr, 0)
~~~

- 설명  
이진 검색 트리를 나타내는 여러 가지 방법을 오가는 문제를 쉽게 푸는 방법은, 각 방법으로부터 (직관적인 방법으로 구성된) 원래의 트리 구조를 복원하는 일이라고 생각합니다. 반대 과정은 더 쉽기 때문입니다.  
여기서, 직관적으로 구성된 원래의 트리 구조를 저는 parent, left/right child를 이용해 표현했습니다. 이는 이진 검색 트리를 처음 배울 때 트리의 구조를 설명하면서 가장 먼저 나오는 개념들이고, 그만큼 직관적이기 때문입니다.
