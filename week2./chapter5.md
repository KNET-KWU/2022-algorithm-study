개관
===========

* 알고리즘을 설계하는 작업은 한순간의 영감보다는 여러 전략적인 선택에 따라 좌우된다.


무식하게 풀기
==========

무식하게 푼다(__brute-force__) --> 컴퓨터의 빠른 계산 능력을 활용하여
가능한 경우의 수를 일일이 나열하면서 답을 찾는 방법을 의미한다.

이렇게 가능한 방법을 전부 만들어 보는 알고리즘들을 가리켜 흔히 __완전탐색__ 이라고 부른다.


재귀 호출과 완전탐색
---------

* 1부터 n까지의 합을 반환하는 함수
```cpp
int recursiveSum(int n){
  if(n==0){return 1;}
  return n+recursiveSum(n-1);
  }
 ```
 ***
n개의 숫자의 합을 구하는 작업을 n개의 조각으로 쪼개, 더할 각 숫자가 하나의 조각이 되도록합니다.
재귀 호출을 이용하기 위해서는 이 조각 중 __하나를 떼내어 자신이 해결하고__, __나머지 조각들은 자기 자신을 호출해 해결해야 합니다__

한가지 유의할 점  n개의 조각 중 n이 아니라 1을 빼냈을 경우 이런 방법으로 문제를 해결할 수 없다.

(기저사례란?
'더이상 쪼개지지 않는' 최소한의 작업에 도달했을 때 답을 곧장 반환하는 조건문을 포함해야 한다.)
***

* 예제:조합구현하기
```cpp
#include<iostream>
#include<vector>

using namespace std;
vector<int> v;
void pick(int n,int toPick){
    if(toPick==0){
        for(int i=0;i<v.size();i++){
            cout<<v[i];
        }
        cout<<endl;
        return;}
    int smallest;

    if(v.empty()){
        smallest=0;
    }else{
        smallest=v.back()+1;
    }


    for(int next=smallest;next<n;next++){
        v.push_back(next);
        pick(n,toPick-1);
        v.pop_back();
    }
}
int main(){
    
    pick(5,3);
}
```
***

예제: 보글게임
-------
```cpp
  #include<iostream>
#include<vector>
using namespace std;
int dx[8]={1,1,1,-1,-1,-1,0,0};
int dy[8]={-1,0,1,-1,0,1,-1,1};
char board[5][5];
bool isVisitied[5][5];
bool inRange(int y,int x){
    if(y<0||x<0||x>=5||y>=5){
        return false;
    }else{
        return true;
    }
}
bool hasWord(int x,int y,const string& word){
    //시작범위가 5*5밖이면 종료
    if(!inRange(y,x)){return false;}
    //보드상의 x,y에 위치한 char변수가 word[0]과 다르면 종료
    if(board[y][x]!=word[0]){return false;}
    //단어 길이가 1이면 성공
    if(word.size()==1){
        
        return true;}
    // 이미 방문했던 지점이라면 false반환 후 종료
    if(isVisitied[y][x]==true){return false;}
    for(int direction=0;direction<8;direction++){
        int nextY=y+dy[direction];
        int nextX=x+dx[direction];
       
        if(hasWord(nextX,nextY,word.substr(1))){
            //재귀 함수를 빠져나오면서 지나온 경로 출력
            cout<<nextX<<" "<<nextY<<endl;
            return true;}
        
    }
    return false;

}

int main(){
    for(int i=0;i<5;i++){
        for(int j=0;j<5;j++){
            cin>>board[i][j];
        }
    }
    string a;
    cin>>a;
    
    for(int i=0;i<5;i++){
        for(int j=0;j<5;j++){
            for(int z=0;z<5;z++){
                fill_n(isVisitied[z],5,false);
            }
            hasWord(j,i,a);
            
        }
    }
}


```

기저사례로 1.범위 밖의 좌표면 종료. 2.좌표상의 글자가 동일하지않으면 종료. 3.단어 길이가 1이면 성공
!!!! 순서에 유의한다
* 시간복잡도는 단어의 길이 N 탐색방향 8가지--> 8^N-1=O(8^N)

완전 탐색 레시피
---

* 1.완전 탐색은 존재하는 모든 답을 하나씩 검사하므로, 걸리는 시간은 가능한 답의 수에 정확히 비례합니다.최대 크기의 입력을 가정했을 때 답의 개수를 계산하고 이들을 모두
제한 시간 안에 생성할 수 있을지를 가늠하니다.만약 시간 안에 계산할 수 없다면 3부의 다른 장에서 소개하는 설계 패러다임을 적용해야 합니다.
* 2.가능한 모든 답의 후볼르 만드는 과정을 여러 개의 선택으로 나눕니다. 각 선택은 답의 후보를 만드는 과정의 한 조각이 됩니다.
* 3.그중 하나의 조각을 선택해 답의 일부를 만들고, 나머지 답을 재귀 호출을 통해 완성합니다.
* 4.조각이 하나밖에 남지 않은 경우, 혹은 하나도 남지 않은 경우엔느 답을 생성했으므로, 이것을 기저 사례로 선택해 처리합니다.

문제:소풍 제한사항(1초,64MB이하의 메모리) 학생수의 범위:2<=N<=10
---
* 전체 문제를 2/n 개의 조각으로 나눠서 한 조각마다 두학생을 짝지어 주는 것으로 한다.
* 이때 문제의 형태는 '아직 짝을 찾지 못한 학생들의 명단이 주어질 때 친구끼리 둘씩 짝짓는 경우의 수를 계산하라'
```cpp
/* 예제 입력
    3  테스트 케이스의 수
    2 1 학생수 2명, 친구쌍 1
    0 1 0과 1이 친구
    4 6 학생수 4명, 친구쌍 6
    0 1 1 2 2 3 3 0 0 2 1 3
    6 10 학생수 6명 ,친구쌍 10
    0 1 0 2 1 2 1 3 1 4 2 3 2 4 3 4 3 5 4 5
    
    예제 출력
    1
    3
    4
    */
    #include<iostream>
    using namespace std;
    int n,m;
    bool areFreinds[10][10];
    int countParings(bool taken[10]){
        int firstFree=-1;
        for(int i=0;i<n;i++){
            if(!taken[i]){
                firstFree=i;
                break;
            }
        }
        //모든 학생이 짝을 찾았으면 한 가지 방법을 찾았으니 종료한다.
        if(firstFree==-1){return 1;}
        int ret=0;
        for(int pairWith=firstFree+1;pairWith<n;pairWith++){
            if(!taken[pairWith]&&areFreinds[firstFree][pairWith]){
                taken[firstFree]=taken[pairWith]=true;
                ret+=countParings(taken);
                // 재귀함수의 깊이가 한단계 얕아졌을때 taken관계 해제해준다. 
                taken[firstFree]=taken[pairWith]=false;
            }
        }
        return ret;
    }
  int main(){
      bool taken[10];
      fill_n(taken,10,false);

      cin>>n>>m;
      int p1,p2;
      for(int i=0;i<m;i++){
          cin>>p1>>p2;
          areFreinds[p1][p2]=true;
          areFreinds[p2][p1]=true;
      }
      int ans=countParings(taken);

      cout<<ans;


  }



```
 1초당 대충 1억번 수행하므로 
 열명이 모두 서로 친구인경우-->9x7x5x3x1=954
 
문제:게임판 덮기
---

```cpp
#include<iostream>
#include<vector>
using namespace std;

const int coverType[4][3][2]={
    {{0,0},{1,0},{0,1}},//블록 1
    {{0,0},{0,1},{1,1,}},//블록 2
    {{0,0},{1,0},{1,1}},//블록 3
    {{0,0},{1,0},{1,-1}}//블록 4
};
// delta의 역할
bool set(vector<vector<int>>& board,int y,int x,int type,int delta){
    bool ok=true;
    for(int i=0;i<3;i++){
        const int ny=y+coverType[type][i][0];
        const int nx=x+coverType[type][i][1];
        if(ny<0||ny>=board.size()||nx<0||nx>=board[0].size()){
            ok=false;
            // 여기서 borad에 delta를 더해준다.
            //delta가 -1일때 
        }else if((board[ny][nx]+=delta)>1){
            ok=false;
        }
    }
    //위의 if문 다 통과하면 true
    return ok;
}
int cover(vector<vector<int>>& board){
    int y=-1,x=-1;
    for(int i=0;i<board.size();i++){
        // 좌표상 위부터 탐색
        for(int j=0;j<board[i].size();j++){
            if(board[i][j]==0){
                y=i;
                x=j;
                break;
            }
            
        }
        if(y!=-1){break;}
        
    }
    if(y==-1){return 1;}
    int ret=0;
    for(int type=0;type<4;type++){
        if(set(board,y,x,type,1)){
            ret+=cover(board);
        }
        //덮었던 블록 복구
        set(board,y,x,type,-1);
    }
    return ret;
}
    

vector<vector<int>> v;
int main(){
   
    int y,x;
    cin>>y>>x;
    int t;
    int count=0;
    vector<int> v2;
    for(int i=0;i<y;i++){
        for(int j=0;j<x;j++){
            cin>>t;
            if(t==0){count++;}
            v2.push_back(t);
        }
        v.push_back(v2);
        v2.clear();
    }
    
    int ans=cover(v);
    cout<<ans;
}
```



최적화 문제
---
문제의 답이 하나가 아니라 여러개이고 그 중 어떤 기준에 따라 가장 좋은 답을 찾아내는 문제


예제: 여행하는 외판원 문제

```cpp
#include<iostream>
#include<vector>
#include<algorithm>
using namespace std;

int n;
double dist[10][10];
vector<bool> visited;
vector<int> path;
double shortestPath(vector<int>&path,vector<bool>&visited,double currentLength){
    if((path.size()==n)){
        return currentLength+dist[path[0]][path.back()];
    }
    double ret=1e9;
    
    for(int next=0;next<n;next++){
        if(visited[next]){continue;}
        int here=path.back();
        path.push_back(next);
        visited[next]=true;
        double cand=shortestPath(path,visited,currentLength+dist[here][next]);
        //재귀호출이 끝난 후 처리
        ret=min(ret,cand);
        visited[next]=false;
        path.pop_back();
    }
    return ret;         
                    
}
int main(){
    cin>>n;
    for(int i=0;i<n;i++){
        visited.push_back(false);
    }
    
    for(int i=0;i<n;i++){
        for(int j=0;j<n;j++){
            dist[j][i]=1;
            dist[i][j]=1;
        }
    }
    path.push_back(0);
    
    double a=shortestPath(path,visited,0);
    cout<<a;
}
```


문제:시계맞추기

```cpp
#include<iostream>
#include<vector>
#include<algorithm>
using namespace std;

const int INF=9999,SWITCHES=10,CLOCKS=16;

const char linked[SWITCHES][CLOCKS+1]={
    "xxx.............",
    "...x...x.x.x....",
    "x...xxxx........",
    "......xxx.x.x...",
    "x.x...........xx",
    "...x..........xx",
    "....xx.x......xx",
    ".xxxxx..........",
    "...xxx...x...x.."
};

bool areAligned(const vector<int>&clocks){
    for(int i=0;i<CLOCKS;i++){
        if(clocks[i]!=12){return false;}
    }
    return true;
}
void push(vector<int>&clocks, int swtch){
    for(int clock=0;clock<CLOCKS;clock++){
        if(linked[swtch][clock]=='x'){
            clocks[clock]+=3;
            if(clocks[clock]==15){clocks[clock]=3;}
        }
    }
}
int solve(vector<int>& clocks,int swtch){
    if(swtch==SWITCHES){return areAligned(clocks)?0:INF;}
    int ret=INF;
    for(int cnt=0;cnt<4;cnt++){
        ret=min(ret,cnt+solve(clocks,swtch+1));
        push(clocks,swtch);
    }
    return ret;
}
vector<int> clocks;
int main(){
    int a;
    for(int i=0;i<CLOCKS;i++){
        cin>>a;
        clocks.push_back(a);
    }
    a=solve(clocks,0);
    cout<<a;
}
```

next_permutation() 사용법
----
https://twpower.github.io/82-next_permutation-and-prev_permutation
