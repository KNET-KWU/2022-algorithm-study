# 조합탐색

## 11.1 도입
### 조합탐색
- 완전탐색의 최적화
- 완전탐색을 포함해 유한한 크기의 탐색 공간을 뒤지면서 답을 찾아 내는 알고리즘
- 탐색 공간(search space) 부분답과 완성된 답의 집합
- 목표 : 기본적으로 모두 최적해가 될 가능성이 없는 답들을 탐색하는 것을 방지하여 만들어 봐야 할 답의 수를 줄이는 것을 목표로 함.
     
### 조합탐색 최적화 기법
- 가지치기(pruning) : 탐색 과정에서 최적해로 연결될 가능성이 없는 부분을 잘라낸다. 
예) 외판원 문제를 추는데, 길이가 10인 경로를 이미 찾아냈다고 하면, 더이상 탐색하지 않고 탐색을 중단하는 것
- 탐색의 순서를 바꾸거나, 탐색 시작 전에 탐욕법을 이용해 적당히 좋은 답을 우선 찾아낸다.
입

## 11.2 조합 탐색 기법들
    여행하는 외판원 문제(TSP)를 푸는 완전탐색 알고리즘에 기법들을 적용하면서 변화하는 수행 속도를 관찰해본다.
입력파일 <br>
소행, 중형, 대형, 초대형 네가지 입력. 
각각 n=12, n=16, n=20, n=24 크기의 입력 20개씩을 가지고 있으며 각 입력은 2차원 평면세 n개의 점을 임의로 배치하고 이들 사이의 거리를 재서 만듬
    
    목표는 이 동적 계획법 알고리즘 보다는 더 빨리 답을 얻는 것


- 입력이 커질수록 수행시간이 급격히 증가하는 것을 볼 수 있다. (메모리는 입력의 크기에 따라 지수적으로 커진다.)
- 만약 컴퓨터의 메모리가 모자랐다면 결국 가상 메모리를 사용 해야 했을 테고, 결과적으로 상상할 수 없을 정도로 느려졌을 것이다.

### 조합 탐색 뼈대의 구현
```c++
//코드 11.1 TSP를 해결하는 완전 탐색의 구현
const double INF = le200;
const int MAX = 30;
int n; // 도시의 수
double dist[MAX][MAX]; // 두 도시간의 거리를 저장하는 배열
// 지금까지 찾은 최적의 해
doubel best;    
void search(Vector<int>& path, vector<bool>& visited, double currentLength){
    int here = path.back();
    // 기저 사례 : 전체 도시를 모두 순회했을 경우 종료
    if(path.size() == n){
        best=min(best, currentLength+dist[here][0]);
        return;
    }
    for(int next = 0; next < n; ++next){
        if(visited[next]==true) continue;
        push.push_back(next);
        visited[next]=true;
        search(path, visited, currentLnegth + dist[here][next]);
        visited[next]=false;
        path.push_back(next);
    }
}
void solve(){
    best = INF;
    vector<int> path = vector(1,0);
    Vector<bool> visited = vector(n, false);
    visited[0]=true;
    search(path, visited,0);
    return best;
}
```

- 이와 같은 단순한 알고리즘의 수행 속도는 어떨까? search가 만드는 경로의 수는 (n-1)!개.
- 11!는 약 4천만 정도되므로 소형 입력은 그럭저럭 풀수 있다.
- 중형 입력 경로는 12*13*14*15 ~ 30,000 배 는다. -> 풀기 불가능

<img width="973" alt="image" src="https://user-images.githubusercontent.com/76817418/154212109-f4d888c7-2639-4c38-8f7c-ec5ff8088463.png">


### 최적해보다 나빠지면 그만두기
```c++
//간단한 가지치기 : 지금까지 찾은 가장 좋은 답 이상일 경우 중단
if(best<=currentLength) return;
```

<img width="973" alt="image" src="https://user-images.githubusercontent.com/76817418/154212183-ea216f22-9e1c-4c9d-b10d-f93118f658bd.png">


### 간단한 휴리스틱을 이용한 가지치기
- 휴리스틱(heuristic) : 경험에 의거한 문제 풀이 기법, 사람이 어림짐작으로 푸는 과정을 알고리즘으로 옮긴 것이 휴리스틱이라고 생각하면 얼추 비슷하다.
- 휴리스틱이 실제 필요한 경로보다 더 긴 경로가 필요하다고 잘못 짐작한 경우에는 최적해를 걸러내기 때문에 찾을 수 없게 된다.
- 그렇기 때문에 휴리스틱의 반환값은 항상 남은 최단 경로의 길이보다 작거나 같아야 한다.
  ->과소평가(underestimate)하는 휴리스틱, 난관적인(optimistic) 휴리스틱, 낙관적인 휴리스틱

### 휴리스틱 함수 작성하기
    TSP의 경우 문제의 제약 조건을 일부 없앤 더 단순한 형태로 문제를 변형할 수 있다.
- 남은 도시를 모두 방문할 필요 없이, 가장 멀리 있는 도시 하나만 방문했다가 시작점으로 돌아가도 된다.
- 남은 도시들을 방문하는 방법이 꼭 일렬로 연결된 형태가 아니어도 된다.

### 단순한 휴리스틱 함수의 구현
- 아직 방문하지 않은 도시들에 대해 인접한 간선 중 가장 짧은 간선의 길이를 더한다.
- 가장 짧은 간선의 길이만을 모으면 실제 최단 경로 이하의 값만 된다.
```c++
//코드 11.2 단순한 휴리스틱을 이용한 가지치기의 구현

//각 도시에서 인접한 간선중 가장 짧은 것을 미리 찾아 둔다.
#include <iostream>
#include <vector>
 
using namespace std;
 
const double INF = 1e200;
const int MAX = 30;
int n;
double dist[MAX][MAX];
 
double best;
 
//각도시에인접한간선 중 가장짧은것 전처리
double minEdge[MAX];
 
double simpleHeuristic(vector<bool>& visited)
{
    double ret = minEdge[0];
    for (int i = 0; i < n; ++i)
        if(!visited[i])
            ret += minEdge[i];
    return ret;
}
 
void search(vector<int>& path, vector<bool>& visited, double currentLength)
{
    if(best <= currentLength + simpleHeuristic(visited)) return;
 
    int here = path.back();
 
    if(path.size() == n) 
    {
        best = min(best, currentLength + dist[here][0]);
        return;
    }
 
    for(int next = 0; next < n; ++next)
    {
        if (visited[next]) continue;
        path.push_back(next);
        visited[next] = true;
        search(path, visited, currentLength + dist[here][next]);
        visited[next] = false;
        path.pop_back();
    }
}
 
double solve()
{
    for (int i = 0; i < n; ++i) 
    {
        minEdge[i] = INF;
        for (int j = 0; j < n; ++j)
            if(i != j) minEdge[i] = min(minEdge[i], dist[i][j]);
    }
    best = INF;
    vector<bool> visited(n, false);
    vector<int> path(1, 0);
    visited[0] = true;
    search(path, visited, 0);
    return best;
}

```

<img width="962" alt="image" src="https://user-images.githubusercontent.com/76817418/154200939-9b11475f-bcaa-4d9f-9e0e-ab5a845b1291.png">

### 가까운 도시부터 방문하기(탐욕법)
    TSP를 해결하는 조합 탐색은 각 재귀호출 마다 어느 도시를 방문할지를 결정하는 방식으로 구현된다.
    이때 더 가까운 것부터 방문하면 좋은 답을 더 빨리 찾아낼 수 있는 경우가 있다.
    각 도시마다 다른 모든 도시들을 거리순으로 미리 정렬해서 저장해둔다.

```c++
//코드 11.3 가까운 정점부터 방문하는 최적화의 구현

//각 도시마다 다른 도시들을 가까운 순서대로 정렬해둔다.
vector<int> nearest[MAX];
void search(vector<int>& path, vector<bool>& visited, double currentLength){
    //
    //다음 방문할 도시를 전부 시도해본다.
    for(int i=0;i<nearest[here].size();++i){
        int next = nearest[here][i];
        //
    }
}
double solve(){
    //nearest 초기화
    for(int i=0; i<n; ++i){
        vector<pair<double, int>> order;
        for(int j=0; j<n; ++j)
            if(i!=j)
                order.push_back(make_pair(dist[i][j],j));
        sort(order.begin(), order.end());
        nearest[i].clear();
        for(int j=0; j<n-1; ++j) 
            nearedst[i].push_back(order[j].second);
    }
    //
}
```
<img width="971" alt="image" src="https://user-images.githubusercontent.com/76817418/154201358-a32c32e1-1c43-42d5-b2c9-060b2d5bc74b.png">

- 항상 방문하지 않은 도시 중 가장 가까운 쪽으로 가는 것은 탐욕적 알고리즘으로 초기해를 구하는것과 같은 효과

### 지나온 경로를 이용한 가지치기

- 지금까지 만든 부분답을 검사해서 가지치기할 수도 있다.

<p align=center>
<img width =500, src ="https://user-images.githubusercontent.com/76817418/154202421-f48a9bee-c492-41d9-afc4-cb2ce17720b7.png"></p>


지금까지 만든 경로가 시작 상태에서 현재 상태까지 도달하는 최적해가 아니라고 하면, 
최적해는 앞으로도 못찾을 것이고, 탐색을 중단해야 한다.

검사해서 최적해인지를 알기가 쉽지 않다.

따라서 대개 지나간 길을 돌아보는 가지치기는 탐색의 각 단계에서 현재 까지 만든 부분해에

간단한 조작을 가해보고, 결과적으로 답이 더 좋아진다면 탐색을 중단하는 식으로 구현된다.


Tsp에서는 두개의 인접한 도시를 골라서 이 둘의 순서를 바꿔 본 뒤, 경로가 더 짧아지면 탐색을 

중단하는 가지치기를 구현할 수 있다.

(.....,.., p, a, b, q, .... here)

 

위 경로에서 a, b의 순서를 바꿔 p - q 구간의 거리가 더 짧아지면 최적해를 찾을 가능성이 없으니

탐색을 중단하는 함수를 만들면 된다.

이 함수는 항상 현재 도시 이전의 두 도시만을 뒤집는다.

```c++
//path의 마지막 네 개의 도시 중 가운데 있는 두 도시의 순서를 바꿨을때 
//경로가 더 짧아지는지 여부를 반환한다.
//항상 현재 도시 이전의 두 도시만을 뒤집어 보는 함수
bool pathSwapPruning(const vector<int>& path)
{
    if (path.size() < 4) return false;
    int p = path[path.size() - 4];
    int a = path[path.size() - 3];
    int b = path[path.size() - 2];
    int q = path[path.size() - 1];
 
    return dist[p][a] + dist[b][q] > dist[p][b] + dist[a][q];
}

```
pathSwapPruning() 을 좀 더 일반화 하면 더 나은 성능을 얻을 수 있다.
두 도시 순서를 바꾸는 대신, 전체 경로의 일부분을 뒤집는 것이다.

(p - a - b - c - d - e - q - - - here)

(p - e - d - c - b - a - q - - - here)

이 경로가 원래 경로보다 더 짧아진다면 가지치기를 할 수 있다. 
이때 경로 자체의 길이는 똑같기 때문에 달라지는 것은 p와 q에 인접하는 도시 뿐이다. 

이런 탐색은 자기 자신을 교차하는 경로를 만드는지를 검사하는 것이다.

한마디로 꼬인 경로를 푸는것과 마찬가자이다.


```c++
// 시작 도시와 현재 도시를 제외한 path의 부분 경로를 뒤집어보고 더 짧아지는지 확인한다.
bool pathReversePruning(const vector<int>& path)
{
    if(path.size() < 4) return false;
    
    int b = path[path.size() -2];
    int q = path[path.size() -1];
    for(int i = 0 ; i + 3 < path.size() ; i++)
    {
        int p = path[i];
        int a = path[i+1];
        
        if(dist[p][a] + dist[b][q] > dist[p][b] + dist[a][q])
            return true;     
    }
    return false;
} 

```





<img width="977" alt="image" src="https://user-images.githubusercontent.com/76817418/154202274-c5035595-ed71-4dd4-8f18-c93c4a42b7c5.png">

가지치기를 구현한 위의 두 함수는 동적 계획법과 비슷한 수준의 속도로 수행된다.

### MST 휴리스틱을 이용한 가지치기의 구현

단순한 휴리스틱이아니라 좀더 현실에 가까운 답을 계산하는 휴리스틱 알고리즘이 있다면

bfs 조합탐색을 더욱 효과적으로 변형할 수 있다.
<p align =center>
<img width= 500, src="https://user-images.githubusercontent.com/76817418/154209973-f196adbf-fd7e-4840-a5dd-763cf084cd71.png"></p>



단순한 휴리스틱에서 더 제약이 있는 문제를 해결하는 알고리즘을 구현해보자
1. 한 간선은 최대 한번만 선택 가능
2. 선택하지 않은 간선을 모두 지웠을 때 그래프가 둘 이상으로 쪼개지면 안됨
   
즉, 선택한 간선들만 남겼을 때 아직 방문하지 않은 정점들과 현재 정점이 연결되어 있게끔 해야한다.

    그래프 알고리즘중 하나인 MST(최소 스패닝 트리)

<p align=center>
<img width="500" alt="image" src="https://user-images.githubusercontent.com/76817418/154216092-2866975d-8e58-433e-91ad-d3f3b12da751.png"></p>

   
    현재 위치에서 시작해서 아직 방문하지 않은 정점들을 모두 방문하고, 시작점으로 돌아오는 최단 경로또한 시작부분으로 돌아오는 선하나만 제외하면 정점들을 모두 연결하는 스패닝트리이다.



따라서 최소 스패닝 트리의 가중치의 합은 항상 최단 경로보다 작음을 알 수 있다.

크루스칼 스패닝 트리 알고리즘을 이용해 MST를 구하고, 간선 가중치의 합으로부터 탐색의 하한 값을 예상하는 구현은 다음과 같다.

```c++
//코드 11.5 MST 휴리스틱의 구현
//상호 배타적 자료구조를 구현한다.
struct DisjointSet {
    DisjointSet(int n); // n개의 원소로 구성된 집합자료구조 생성
    int find(int here); // here가 포함된 집합 대표를 반환한다.
    bool merge(int a, int b); //a가 포함된 집합과 b가 포함된 집합을 합친다.
};
//모든 도시 간의 도로를 길이 순으로 정렬해 저장해둔다.
vector<pair<double, pair<int, int> > > edges;
//here와 시작점, 아직 방문하지 않은 도시들을 모두 연결하는 MST를 찾는다.
double mstHeuristic(int here, const vector<bool>& visited)
{
    //"Kruskal's MST
    DisjointSet sets(n);
    double taken = 0;
    for(int i = 0; i < edges.size(); ++i)
   { // a -> b
        int a = edges[i].second.first, b = edges[i].second.second;
        if(a != 0 && a != here && visited[a]) continue;
        if(b != 0 && b != here && visited[b]) continue;
        if(sets.merge(a, b))
            taken += edges[i].first;
    }
    return taken;
}
 
double solve()
{ 
    //edge초기화
    edges.clear();
    for (int i = 0; i < n; ++i)
        for(int j = 0; j < i; j++)
            edges.push_back(make_pair(dist[i][j], make_pair(i, j)));
    sort(edges.begin(), edges.end());
    // 생략
}
```
<img width="968" alt="image" src="https://user-images.githubusercontent.com/76817418/154210417-9935b39e-bb6f-4e31-87cd-4b6693e584c3.png">

이 알고리즘은 동적 계획법보다 빠르게 동작하는 구간이 있는 알고리즘이다.

mstHeuristic()은 simpleHeurisric()보다 수행하는데 많은 시간이 걸리지만 그만큼 훨씬 정확한 값을 찾아준다

### 마지막 단계 메모이제이션 하기

조합 탐색 과정에서 같은 상태를 두 번 이상 맞닥뜨리는 것은 흔한일이다. 

이런 비효율을 메모이제이션으로 제거하는 것이 좋겠다는 생각을 할 수 있지만 메모이 제이션을 하는데 제약이 있다. 
1. 메모리가 부족<br>
    : 남은 조각의 수가 미리 정해둔 수 k이하 일때만 메모이제이션을 해야한다.

2. 가지치기를 사용하는 함수는 쉽게 메모이제이션을 할 수 없다. <br>
    : 가지치기로 인해 현재 상태까지 오기 위한 경로에 따라 반환 값이 달라질 수 있다.

<br>

    그러므로, 가지치기를 사용하지 않는 동적 계획법 함수를 별도로 작성한뒤, 
    마지막 k 단계에 도달하면 이 함수를 사용하도록 구현해야한다.

```c++
// 코드 11.6 부분적으로 메모이제이션을 사용하는 최적화의 구현
// 남은 도시의 수가 CACHED_DEPTH 이하면 동적 계획법으로 바꾼다. 상수 5는 실험으로 정한 값이다.
const int CACHED_DEPTH = 5; 
// dp(here, visited)=cache[here][남은 도시의 수][visited]
map<int,double> cache[MAX][CACHED_DEPTH+1]
// here : 현재 위치
// visited : 각 도시의 방문 여부 
// 일 때, 나머지 도시를 모두 방문하고 시작점으로 돌아가는 최단 경로의 길이를 반환한다.
double dp(int here, int visited){
    // 기저사례 : 더 방문할 도시가 없으면 시작점으로 돌아간다.
    if(visited == (1<<n)-1) return dist[here][0];

    // 메모이제이션
    //__builtin_popcount(visited)는 지금까지 방문한 도시의 수
    int remaining = n - __builtin_popcount(visited); 
    double& ret = cache[here][remaining][visited];
    if(ret > 0) return ret;
    ret = INF;

    // 다음 도시를 하나씩 시도한다.
    for(int next = 0; next < n; ++next){
        if(visited & (1<<next)) continue;
        ret = min(ret, dp(next, visited + (1<<next)) + dist[here][next]);
    }
    return ret;
}

void search(vector<int>& path, int visited, double currentLength) {
// 생략
// 기저 사례 : 남은 도시 수가 CACHED_DEPTH 이하면 동적 계획법으로 바꾼다.
    if(path.size() + CACHED_DEPTH >= n){
        best = min(best, currentLength + dp(path.back(), visited));
        return;
    }
    // 생략
}
double solve(){
    // 생략
    // cache 초기화
    for(int i = 0; i < MAX; ++i)
        for(int j = 0; j <= CACHED_DEPTH; ++j)
            cache[i][j].clear();
    // 생략
}
```

<img width="971" alt="image" src="https://user-images.githubusercontent.com/76817418/154211729-5c4f4e9f-5658-4e3d-9867-438bc1c13dbd.png">
메모이제이션은 입력의 크기가 크면 클 수록 큰 효과를 보여준다. 

최적화를 적용했을 때 조합탐색의 효율이 얼마나 좋아질지는 미리 알 수 없기 때문에 이런 기법들을 모두 적용할 수는 없다는것을 염두해 두자

## 11.3 문제 : 게임판 덮기2(문제 ID : BOARDCOVER2)
문제

H×W 크기의 게임판과 한 가지 모양의 블록이 여러 개 있습니다. 게임판에 가능한 많은 블록을 올려놓고 싶은데, 게임판은 검은 칸과 흰 칸으로 구성된 격자 모양을 하고 있으며 이 중에서 흰 칸에만 블록을 올려놓을 수 있습니다. 이때 블록들은 자유롭게 회전해서 놓을 수 있지만, 서로 겹치거나, 격자에 어긋나게 덮거나, 검은 칸을 덮거나, 게임판 밖으로 나가서는 안 됩니다.

<p align=center>
<img width="400" alt="image" src="https://user-images.githubusercontent.com/76817418/154216719-e2346945-acfc-4383-8747-502a35461c40.png"></p>

위 그림은 예제 게임판과 L 자 모양의 블록으로 이 게임판을 덮는 방법을 보여줍니다. 게임판에는 15개의 흰 칸이 있고, 한 블록은 네 칸을 차지하기 때문에 그림과 같이 최대 세 개의 블록을 올려놓을 수 있지요. 게임판과 블록의 모양이 주어질 때 최대 몇 개의 블록을 올려놓을 수 있는지 판단하는 프로그램을 작성하세요.
<p align=center>
<img width="600" alt="image" src="https://user-images.githubusercontent.com/76817418/154216845-d371a34d-4df8-4690-8d91-62c21e87c828.png"></p>

## 11.4 게임판 덮기
이 문제는 6장의 연습문제였던 게임판 문제의 확장판이다.

가능한 한 많은 흰 칸을 덮는것이 목적이기 때문에 최적화 문제로 해결이 가능하다.

### 전처리
블록의 네 가지 회전 형태를 미리 계산하고, 이들을 첫번째 칸에 대한 상대 좌표의 목록으로 미리 저장해둔다.
```c++
//블록의 각 회전된 형태를 상대 좌표의 목록으로 저장해둔다.
vector<vector<pair<int,int>>> rotations; 
//블록의 크기
int blockSize;

//2차원 배열 arr을 시계방향으로 90도 돌린 결과를 반환한다.
vector<string> rotate(const vector<string>& arr){
    vector<string> ret(arr[0].size(), string(arr.size(),' '));
    for(int i=0; i<arr.size(); ++i)
        for(int j=0; j<arr[0].size(); ++j)
            ret[j][arr.size()-i-1]=arr[i][j];
    return ret;
}
//block의 네가지 회전형태를 만들고 이들을 상대 좌표의 목록으로 반환한다.
void generateRotations(vector<string> block){
    rotations.clear();
    rotations.resize(4);
    for(int rot=0; rot<4;++rot){
        int originY=-1, originX=-1;
        for(int i=0;i<block.size();++i)
            if(block[i][j]=='#'){
                if(originY==-1){
                    //가장 윗줄 맨 왼쪽에 있는 칸이 '원점'이 된다.
                    originY=i;
                    originX=j;
                }
                //각 칸의 위치를 원점으로부터 상대 좌표로 표현한다.
                rotations[rot].push_back(make_pair(i-originY,j-originX));
            }
        //블록을 시계방향으로 90도 회전한다.
        block=rotate(block);
    }

    //네 가지 회전 형태 중 중복이 있을 경우 이를 제거한다.
    sort(rotations.begin(), rotations.end());
    rotations.erase(unique(rotations.begin(), rotations.end()), rotations.end()); 
    //블록이 몇칸 짜리인지 저장해둔다.
    blocksize = rotations[0].size();
}
```

    
unique를 이용한 벡터의 중복 원소 제거
```c++
rotations.erase(unique(rotations.begin(), rotations.end()), rotations.end()); 
```
- 벡터의 unique 함수를 적용하면 중복된 원소를 제거하며 앞에서부터 원소들을 채워나간다.
- 문제를 풀거나 보통의 상황에선 몇 개가 중복되고 몇 개의 원소가 줄어든지를 알 수 없기때문에 erase 함수를 사용하여 뒷 부분에 필요없는 값들을 삭제시켜 줄 수 있다. 
- _v.erase(v.begin()+s, v.begin()+e)_ 명령어를 입력하면 [s,e) 원소가 삭제된다.
- n개의 원소에 대한 unique 함수의 시간복잡도는 O(n)이다.

### 완전탐색
```c++
//코드 11.8 게임판 덮기2 문제를 해결하는 완전탐색 알고리즘

//게임판의 정보
int boardH, boardW;
vector<string> board;
//게임판의 각 칸이 덮였는지를 나타낸다. 1이면 검은칸이거나 이미 덮은칸, 0이면 빈칸
int covered[10][10];
//지금까지 찾은 최적해
int best;

bool set(int y,int x, const vector<pair<int,int>& block>);

//placed: 지금까지 놓은 블록의 수
void search(int placed){
    //아직 채우지 못한 빈칸 중 가장 윗줄 왼쪽에 있는 칸을 찾는다.
    int y=-1,x=-1;
    for(int i=0;i<boardH;++i){
        for(int j=0;j<boardW;++j)
            if(covered[i][j]==0){
                y=i;
                x=j;
                break;
            }
        if(y!=-1)break;
    }
    //기저 사례 : 게임판의 모든 칸을 처리한 경우
    if(y==-1){
        best=max(best,placed);
        return;
    }
    //이 칸을 덮는다.
    for(int i=0;i<rotations.size();++i){
        if(set(y,x,rotations[i],1))
            search(placed+1);
        set(y,x,rotations[i],-1);
    }
    //만약에 빈칸에 놓을 수 있는 블록의 모양이 없다면 해당 빈칸을 막아놓고 재귀호출한다. 그렇지 않으면 재귀호출을 했을때, 해당 빈칸에서 또 블록을 놓을 수 있는지 없는지 다시 계산을 반복하는식으로 무한루프에 빠진다.
    covered[y][x] = 1;
    search(placed);
    covered[y][x] = 0;
}
int solve(){
    best=0;
    //covered 배열을 초기화한다.
    for(int i=0;i<boardH;i++){
        for(int j=0;j<boardW;j++){
            covered[i][j] = (board[i][j]=='#'?1:0);
        }
    }
    search(0);
    return best;
}
```
이 문제에서는 위와같은 완전탐색은 시간내에 수행할 수 없다. 
이를 해결하기 위해 간단한 가지치기를 이용해 수행 시간을 줄일 수 있다.

### 가지치기
최대 값을 구하는것이 목적이므로, 문제를 과대평가하여 풀어야한다. 
즉, 실제로 놓을 수 있는 블록 수 이상을 항상 반환해야한다는 것이다. 

휴리스틱 함수를 만드는 쉬운 방법은 블록을 통째로 내려놓아야 한다는 제약을 없앤다. 
1. 블록을 분리 =>1개의 칸으로 이루어진 블록들
2. 남은 칸을 이것으로 채움
   
(남은 칸의 개수/블록 크기)가 앞으로 채울 수 있는 블록의 수를 과대평가한 답이기 때문에 

이 값은 항상 우리가 실제 놓을 수 있는 블록의 수 이상이어서 우리가 얻을 수 있는 답의 상한이 된다.

## 11.5 문제 : 알러지가 심한 친구들(문제 ID: ALLERGY)

집들이에 n 명의 친구를 초대하려고 합니다. 할 줄 아는 m 가지의 음식 중 무엇을 대접해야 할까를 고민하는데, 친구들은 각각 알러지 때문에 못 먹는 음식들이 있어서 아무 음식이나 해서는 안 됩니다. 만들 줄 아는 음식의 목록과, 해당 음식을 못 먹는 친구들의 목록이 다음과 같은 형태로 주어진다고 합시다.
<p align=center>
<img width="500" alt="image" src="https://user-images.githubusercontent.com/76817418/154227246-527b58d8-ff8d-4241-8e66-b0428ffcd29d.png"></p>

각 친구가 먹을 수 있는 음식이 최소한 하나씩은 있도록 하려면 최소 몇 가지의 음식을 해야 할까요? 

위 경우라면 다 같이 먹을 수 있는 음식이 없기 때문에 결국 두 가지 이상 음식을 해야 합니다. 피자와 탕수육, 혹은 잡채와 닭강정처럼 두 개 이상의 음식을 선택해야만 모두가 음식을 먹을 수 있지요. 친구들의 정보가 주어질 때 최소한 만들어야 하는 요리의 가지수를 계산하는 프로그램을 작성하세요.
<p align=center>
<img width="600" alt="image" src="https://user-images.githubusercontent.com/76817418/154227454-2328db8e-dba6-4551-b3ef-bb3c37b13fb3.png"></p>

## 풀이 : 알러지가 심한 친구들
이 문제는 집합덮개(set cover)라고 부르는 문제로, 계산 이론쪽에서는 가장 유명한 NP완비(NP complete)문제중 하나이다. 
직관적으로 해결할 수 있는 방법으로는 음식을 선택하는 모든 경우의 수를 하나하나 만들어 보는 것이다. 
아래의 코드는 문제를 m개의 조각으로 쪼갠 뒤 각 조각에서 해당 음식을 만들지, 만들지 않을지를 결정한다. 
```c++
//알러지 문제를 해결하는 첫번째 조합 탐색 알고리즘

int n,m;
//eaters[food] : food를 먹을 수 있는 친구들의 번호
vector<int> eaters[50];
int best;


```