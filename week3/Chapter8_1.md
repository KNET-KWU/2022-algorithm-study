# 08-1. 동적 계획법

## 다이나믹 프로그래밍(Dynamic Programming)이란 ?

- 메모리를 적절히 사용하여 수행 시간 효율성을 비약적을 향상시키는 방법
- 이미 계산된 결과(작은 문제)는 별도의 메모리 영역에 저장하여 다시 계산하지 않도록 한다.
- 다이나믹 프로그래밍의 구현은 일반적을 두 가지 방식(탑다운과 바텀업)으로 구성되어있다.
- 다이나믹 프로그래밍(DP)은 동적 계획법이라고도 부른다.
<br><br>

## 일반적인 프로그래밍 분야에서 동적(Dynamic)의 의미는?

- 자료구조에서 동적 할당(Dynamic Allocation)은 '프로그램이 실행되는 도중에 실행에 필요한 메모리를 할당하는 기법'을 의미한다.
- 반면에 다이나믹 프로그래밍에서 '다이나믹'은 별다른 의미 없이 사용된 단어이다.
<br><br>

> 동적 계획법의 고안자인 벨만(Richard E. Bellman)은 dynamic이라는 단어가 멋있어서 선택했다고 한다. <br>
    Programming이란 단어는 최적화 연구 분야에서 최적의 프로그램을 찾아낸다는 의미로 사용된다. <br>


---

## 다이나믹 프로그래밍의 조건

DP는 다음의 조건을 만족할 때 사용할 수 있다.
    
  1. 최적 부분 구조(Optimal Substructure)
    - 큰 문제를 작은 문제로 나눌 수 있으며 작은 문제의 답을 모아 큰 문제를 해결할 수 있는 경우
  2. 중복되는 부분 문제(Overlapping Subproblem)
    - 동일한 작은 문제를 반복적으로 해결해야하는 경우
<br><br>

## VS 분할 정복(Divide and Conquer)

- DP와 분할 정복은 모두 '최적 부분 구조'를 가질 때 사용할 수 있다.(공통점)
  - 즉, 큰 문제를 작은 문젤 나눌 수 있으며 작은 문제의 답을 모아 큰 문제를 해결할 수 있는 상황
- DP와 분할 정복의 차이점은 '부분 문제의 중복'이다.
  - DP 문제에서는 각 부분 문제들이 서로 영향을 미치며 부분 문제가 중복된다.
  - 반면에 분할 정복 문제에서는 동일한 부분 문제가 반복적으로 계산되지 않는다.
<br><br>

## 메모이제이션 (Memoization)이란 ?

- 메모이제이션은 DP를 구현하는 방법 중 하나이다.
- '한 번 계산한 결과를 메모리 공간에 메모하는 기법'이다.
  - 중복되는 부분 문제(Overlapping Subproblem)에 의해 같은 문제를 다시 호출하게되면 메모했던 결과를 그대로 가져온다.
  - 이때 이미 계산한 값을 저장해 두는 메모리의 장소를 캐시(cache)라고 부른다.
  - 값을 기록해 놓는다는 점에서 캐싱(Caching)이라고도 한다.
<br><br>

## 메모이제이션 구현 패텬

동적 계획법은 가장 흔한 문제 유형 중 하나이기 때문에 메모이제이션은 굉장히 자주 구현하게 된다. <br>
그런 만큼 한 가지 패턴을 정해두고 항상 같은 형태로 구현하기로 하면 작성하기도, 버그를 찾기도 쉬워진다.

다음과 같은 재귀 함수가 있다고 하자.
<br><br>
```c++
// a오 b는 각각 [0, 2500) 구간 안의 정수
// 반환 값은 항상 int형 안에 들어가는 음이 아닌 정수
int someObscureFunction(int a, int b);
```

### 분석
함수의 반환 값이 항상 0 이상이라는 점을 이용해 cache[]를 몯 -1로 초기화한다.
1. 항상 기저 사례를 제일 먼저 처리한다.
    - 입력이 범위를 벗어난 경우 등을 기저 사례로 처리하면 매우 유용하다.
    - 기저 사례를 먼저 확인하지 않고 cache[]에 접근하면 범위를 벗어나는 등의 오류가 있을 수 있기 때문이다.
2. 함수의 반환 값이 항상 0 이상이라는 점을 이용해 cache[]를 몯 -1로 초기화한다.
    - cache[]의 해당 위치에 적혀 있는 값이 -1이라면 이 값은 계산된 반환 값이 아니라는 것을 알 수 있다.
    - 만약 함수의 반환 값이 음수일 수도 있다면 이 방법은 써먹을 수 없을 것이다.
3. ret가 cache[a][b]에 대한 참조형(reference)이라는 데 유의하자.
    - 실수를 할 가능성을 없애주기 때문이다.
4. C++의 경우 memset()을 이용해 cache[]를 초기화하는 부분이다.
    - 메모이제이션용 배열을 초기화하는 것은 매우 자주 있는일이기 때문에 다중 for문보다 쉽게 초기화할 수 있는 방법을 알아 두면 간편하다.
    - 단, memset()을 배열을 초기화하는 방법은 굉장히 제한적인 경우에만 쓸 수 있는 일이니 유의하자.
<br><br>
```c++
// 코드 8.3 메모이제이션의 사용 예

// 전부 -1로 초기화해 둔다.
int cache[2500][2500];
// a와 b는 각각 [0, 2500) 구간 안의 정수
// 반환 값은 항상 int형 안에 들어가는 음이 아닌 정수
int someObscureFuction(int a, int b) {
    // 기저 사례를 처음에 처리한다.
    if (...) return ...;

    // (a, b)에 대한 답을 구한 적이 있으면 곧장 반환
    int& ret = cache[a][b];
    if (ret != -1) return ret;
    
    // 여기에서 답을 계산한다.
    ...
    return ret;
}
int main() {
    // memset()을 이용해 cache 배열을 초기화한다.
    memset(cache, -1, sizeof(cache));
}
```

> memset()은 두 번째 인자로 주어진 값을 주어진 메모리의 모든 바이트에 채운다. <br>
즉,  value가 int형이지만 내부적으로는 unsigned char(1byte)로 변환되어 저장된다. <br>
만약 배열이 32비트나 64비트 정수형이라면, 각 바이트마다 값이 들어가기 때문에 엉뚱한 수로 초기화될 수 있다. <br>
memset()으로 정수 배열에 0 혹은 -1을 채울 수 있는 이유느 각 바이틍 해당 숫자를 집어넣고 부호 있는 정수형을 해석하면 운 좋게도 해당 숫자와 같아지기 때문이다.
<br>


## 메모이제이션의 시간 복잡도 분석

메모이제이션의 시간 복잡도를 분석하는 과정이 헷갈릴 수 있다. <br>
각 입력에 대해 함수를 처음을 호출할 때와 다음으로 호출할 때 걸리는 시간이 다르기 때문이다. <br>
그러나 우리는 다음과 같이 간단하게 주먹구구식으로 계산할 수 있는 방법이 있다.

- 결론 : (존재하는 부분 문제의 수) X (한 부분 문제를 풀 때 필요한 반복문의 수행 횟수) <br>
- 뒤에 예제에서 적용해보겠습니다.
<br><br>


## 탑다운(Top-Down) vs 바텀업(Bottom-Up)

DP 문제를 푸는 방법은 탑다운(Top-Down)과 바텀업(Bottom-Up)이 있다. <br>
두 방법 모두 큰 문제를 여러 개의 부분 문제들로 나누어 풀지만, 차이점이 있다. <br>

우리 책에서는 메모이제이션을 이용하는 탑다운 방식을 많이 사용하였다. <br>
하지만 DP의 전형적인 형태는 바텀업(Bottom-Up) 방식이라고 한다. <br>

- 탑다운(Top-Down)
    - 큰 문제를 해결하기 위해 작은 문제를 호출하는 방식
    - 탑다운(메모이제이션) 방식은 '하향식'이라고도 한다.
    - 점화식을 이용하면 이해하기 쉬게 풀 수 있는 장점이 있다.


    예시) 이항 계수(binomial coefficient)의 계산
<br><br>

<p align="center">
<img width="219" alt="스크린샷 2022-01-24 오후 1 54 08" src="https://user-images.githubusercontent.com/91893721/150724146-7f0a8a7c-9fa3-4cab-b30f-3af9055f4a78.png">
    
<br>
이항 계수 점화식 <br><br></p>


```c++
// 코드 8.2 메모이제이션을 이용한 이항 계수의 계산

// -1로 초기화해 둔다.
int cache[30][30];

int bino2(int n, int r) {
    // 기저 사례
    if (r == 0 || n == r) return 1;
    // -1이 아니라면 한 번 계산했던 값이니 곧장 반환
    if (cache[n][r] != -1)
        return cache[n][r];
    // 직접 계산한 뒤 배열에 저장
    return cache[n][r] = bino2(n-1, r-1) + bino2(n-1, r);
}
```

- 결과적으로 두 번 이상 반복 계산되는 부분 문제들의 답을 미리 저장함으로써 속도의 향상 -> 동적 계획법
<br><br>

- 바텀업(Bottom-Up)
    - 가장 작은 문제들부터 답을 구해가며 전체 문제의 답을 찾는 방식
    - 바텀업 방식은 '상향식'이라고도 한다.
    - 재귀 호출을 하지 않기 때문에 시간과 메모리 사용량을 줄일 수 있는 장점이 있다.
    - 결과 저장용 리스트는 'DP 테이블'이라고 부른다.
<br><br>

```c++
#include <iostream>
int main(void) {
    // DP 테이블
    int C[30][30];
    
    for (int n=0;n<30;n++)
        for (int r=0;r<=n;r++) {
            if (r == 0 || r == n)
                C[n][r] = 1;
            else
                C[n][r] = C[n-1][r-1] + C[n-1][r];
        }
                
    std::cout << C[5][2] << std::endl;
    std::cout << C[6][2] << std::endl;
    return 0;
}
```


### 시간 복잡도 분석

시간 복잡도 = (존재하는 부분 문제의 수) X (한 부분 문제를 풀 때 필요한 반복문의 수행 횟수) 
<br>
r의 최대치는 n이고  <img src="https://latex.codecogs.com/svg.image?bino2(n,&space;r)\\" title="bino2(n, r)" /> 을 계산하는데 만날 수 있는 부분 문제의 수는 최대 <img src="https://latex.codecogs.com/svg.image?O(n^2)\\" title="O(n^2)" /> 이다. <br>
각 부분 문제를 계산할 때 걸리는 시간은 반복문이 없으니 <img src="https://latex.codecogs.com/svg.image?O(1)\\" title="O(1)" /> 이고, <br>
그러면 위 식에 따라  <img src="https://latex.codecogs.com/svg.image?bino2(n,&space;r)\\" title="bino2(n, r)" /> 을 계산하는데 걸리는 시간 복잡도는 <img src="https://latex.codecogs.com/svg.image?O(n^2)\\" title="O(n^2)" /> 이 된다.
<br><br>

---

## 예제: 외발 뛰기 (문제 ID: JUMPGAME, 난이도: 하)

### 재귀 호출에 시작하기

- 함수 정의
    - jump(y, x) = (y, x)에서부터 맨 마지막 칸까지 도달할 수 있는지 여부를 반환한다.

- 점화식
    - jump(y, x) = jump(y+jumpSize, x) || jump(y, x+jumpSize)
<br><br>

```c++
// 코드 8.4 외발 뛰기 문제를 해결하는 재귀 호출 알고리즘

int n, board[100][100];
bool jump(int y, int x) {
    // 기저 사례: 게임판 밖을 벗어난 경우
    if (y >= n || x >= n) return false;
    // 기저 사례: 마지막 칸에 도착한 경우
    if (y == n-1 && x == n-1) return true;

    int jumpSize = board[y][x];
    return jump(y + jumpSize, x) || jump(y, x + jumpSize);
}
```

### 메모이제이션 적용하기

- 위 코드의 문제점
    - 완전 탐색이 포기하기 전까지 만들어야 하는 경롱 개수는 n에 대해 지수적으로 늘어나므로, n=100이면 제한 시간을 초과해 버리게 된다.

여기서 주목할 것은 완전 탐색이 만드는 경로의 수느 엄청나게 많지만 jump()에 주어지는 입력의 개수는 100X100개 뿐이다. <br>
이 경우 비둘기집의 원리에 의해 중복으로 해결되는 부분 문제들이 항상 존재함을 알 수 있다.
<br><br>

```c++
// 코드 8.5 외발 뛰기 문제를 해결하는 동적 계획법 알고리즘

int n, board[100][100];
int cache[100][100];

int jump2(int y, int x) {
    // 기저 사례 처리
    if (y >= n || x >= n) return 0;
    if (y == n-1 && x == n-1) return 1;

    // 메모이제이션
    int& ret = cache[y][x];
    if (ret != -1) return ret;
    int jumpSize = board[y][x];
    return ret = (jump2(y + jumpSize, x) || jump2(y, x + jumpSize));
}
```

## 8.2 문제: 와일드카드 (문제 ID: WILDCARD, 난이도: 중)

1. s[pos]와 w[pos]가 대응되지 않는다.
     - 볼 것도 없이 대응 실패라는 것을 알 수 있다.
2. w 끝에 도달한다.
     - 패턴에 *이 하나도 없는 경우이다. 이 경우에 패턴과 문자열의 길이가 정확히 같아야만 패턴과 문자열이 대응된다고 할 수 있다.
3. s 끝에 도달한다.
     - 패턴은 남았지만 문자열이 이미 끝난 경우이다. 당연히 대응 실패라고 생각할 수 있지만, <br>
       남은 패턴이 전부 *로 구성되어 있다면 사실 두 문자열은 대응될 수 있다. 이 경우를 제외하고는 답은 항상 거짓이다.
4. w[pos]가 *인 경우
     - *가 몇 글자에 대응될지 모르기 때문에, 0글자부터 남은 문자열의 길이까지를 순회하며 모든 가능성을 검사한다. <br>
       이때 w의 pos+1 이후를 패턴 w'으로 하고, s의 pos+skip 이후를 문자열 s'로 해서 match(w', s')로 <br>
       재귀 호출했을 때 답이 하나라도 참이면 답은 참이 된다.

### 와일드카드 완전 탐색
```c++
// 코드 8.6 와일드카드 문제를 해결하는 완전 탐색 알고리즘
#include <iostream>
#include <string>

// 와일드카드 패턴 w가 문자열 s에 대응되는지 여부를 반환한다.
bool match(const std::string& w, const std::string& s) {
    // w[pos]와 s[pos]를 맞춰나간다.
    int pos = 0;
    while (pos < s.size() && pos < w.size() && (w[pos] == '?' || w[pos] == s[pos]))
        ++pos;
    
    // 더 이상 대응할 수 없으면 왜 while문이 끝났는지 확인한다.
    // 2. 패턴 끝에 도달해서 끝난 경우 : 문자열도 끝났어야 대응됨
    if (pos == w.size())
        return pos == s.size();
    // 4. *를 만나서 끝난 경우: *에 몇 글자를 대응해야 할지 재귀 호출하면서 확인한다.
    if (w[pos] == '*')
        for (int skip = 0; pos+skip <= s.size(); ++skip)
            if (match(w.substr(pos+1), s.substr(pos+skip)))
                return true;
    // 이 외이 경우에는 모두 대응되지 않는다.
    return false;
}
```

<p align="center">
<img width="1440" alt="스크린샷 2022-01-24 오후 6 02 34" src="https://user-images.githubusercontent.com/91893721/150752622-02f685e8-415c-43c3-b49f-70ffb2ddfe22.png">


### 중복되는 부분 문제
```c++
// 코드 8.7 와일드카드 문제를 해결하는 동적 계획법 알고리즘
// 시간 복잡도) O(n) = n^3

#include <iostream>
#include <string>

// -1은 아직 답이 계산되지 않았음을 의미한다.
// 1은 해당 입력들이 서로 대응됨을 의미한다.
// 0은 해당 입력들이 서로 대응되지 않음을 의미한다.
int cache[101][101];

// 패턴과 문자열
std::string W, S;

// 와일드카드 패턴 W[w..]가 문자열 S[s..]에 대응되는지 여부를 반환한다.
bool matchMemoized(int w, int x) {
    // 메모이제이션
    int& ret = cache[w][s];
    if (ret != -1) return ret;
    // W[w]와 S[s]를 맞춰나간다.
    while (s < S.size() && w < W.size() && (W[w] == '?' || W[w] == S[s])) {
        ++w;
        ++s;
    }

    // 더 이상 대응할 수 없으면 왜 while문이 끝났는지 확인한다.
    // 2. 패턴 끝에 도달해서 끝난 경우 : 문자열도 끝났어야 참
    if (w == W.size()) return ret = (s == S.size());

    // 4. *를 만나서 끝난 경우: *에 몇 글자를 대응해야 할지 재귀 호출하면서 확인한다.
    if (W[w] == '*')
        for (int skip = 0; skip+s <= S.size(); ++skip)
            if (match(W.substr(w+1), s.substr(s+skip)))
                return ret = 1;
                
    // 3. 이 외이 경우에는 모두 대응되지 않는다.
    return ret = 0;
}
```

### 다른 분해 방법 -> 시간 복잡도 O(n^2)
```c++
// 코드 8.7' 와일드카드 문제를 해결하는 동적 계획법 알고리즘
// 시간 복잡도) O(n) = n^2

#include <iostream>
#include <string>
#include <vector>
#include <algorithm>
#include <memory.h>

// -1은 아직 답이 계산되지 않았음을 의미한다.
// 1은 해당 입력들이 서로 대응됨을 의미한다.
// 0은 해당 입력들이 서로 대응되지 않음을 의미한다.
int cache[101][101];

// 패턴과 문자열
std::string W, S;

// 와일드카드 패턴 W[w..]가 문자열 S[s..]에 대응되는지 여부를 반환한다.
bool matchMemoized(int w, int s) {
    // 메모이제이션
    int& ret = cache[w][s];
    if (ret != -1) return ret;
    // 패턴과 문자열의 첫 한 글자씩을 떼고 이들이 서로 대응되는지를 재귀 호출로 확인
    if (s < S.size() && w < W.size() && (W[w] == '?' || W[w] == S[s]))
        return ret = matchMemoized(w+1, s+1);

    // 더 이상 대응할 수 없으면 왜 while문이 끝났는지 확인한다.
    // 2. 패턴 끝에 도달해서 끝난 경우 : 문자열도 끝났어야 참
    if (w == W.size()) return ret = (s == S.size());

    // 4. *를 만나서 끝난 경우: *에 몇 글자를 대응해야 할지 재귀 호출하면서 확인한다.
    if (W[w] == '*')
        if (matchMemoized(w+1, s) || (s < S.size() && matchMemoized(w, s+1)))
            return ret = 1;
            
    // 3. 이 외이 경우에는 모두 대응되지 않는다.
    return ret = 0;
}

int main(void) {
    int C;
    std::cin >> C;
    
    for (int i=0;i<C;i++) {
        std::vector<std::string> p;
        
        std::cout << i+1 << "번 째 문자 : ";
        std::cin >> W;
        std::cout << "횟수 : ";
        int n;
        std::cin >> n;
        for (int j=0;j<n;j++) {
            std::cin >> S;
            
            for (int k=0;k<W.size()+1;k++)
                memset(cache+k, -1, S.size()+1);
            
            if (matchMemoized(0, 0))
                p.push_back(S);
//            else
//                std::cout << "결과 없음" << std::endl;
        }
        
        std::cout << "-----------------" << std::endl;
        // 알파벳 순서대로 출력
        std::sort(p.begin(), p.end());
        for (int j=0;j<p.size();j++) {
            std::cout << p[j] << std::endl;
        }
        
    }
    
    return 0;
}


//100
//he?p
//3
//help
//heap
//helpp
//*p*
//4
//hp
//help
//papa
//hello
//*bb*
//1
//babbbc
//1
//******a
//aaaaaaaaaab
//??
//3
//a
//aa
//aaa
```
### 내 코드
```c++
// 내 생각) 시간 복잡도 O(n) = n^2
// 느낀점) 처음에 if문이 복잡하더라도 기준을 잘 잡고 분류해서
//       정리하는 연습을 하기
// 아쉬운점) 재귀를 호출할 때마다 매번 문자열 객체를 생성해야된다.


#include <iostream>
#include <string>
#include <memory.h>

int cache[101][101];

int match(const std::string& W, const std::string& s) {
    // 메모이제이션 ( memoization )
    int& ret = cache[W.size()][s.size()];
    if (ret != -1) return ret;
    
    // base case
    if ((W.size() != 0) && (s.size() != 0) && ((W[0] == s[0]) || (W[0] == '?')))
        ret = match(W.substr(1), s.substr(1));
    
    else if (W.size() == 0)
        ret = (s.size() == 0);
    
    else if (W[0] == '*') {
        if (s.size() != 0)
            ret = match(W.substr(1), s) || match(W, s.substr(1));
        else
            ret = match(W.substr(1), s);
    }
    
    // 나머지 경우는 어떤 경우일까?
    // (s.size() == 0) && (W[0] != '*')
    // (s.size() != 0) && ((W[0] != s[0]) && (W[0] != '?'))
    else
        ret = 0;
    
    return ret;
}



int main(void) {
    int C;
    std::cin >> C;
    
    for (int i=0;i<C;i++) {
        std::string W;
        
        std::cout << i+1 << "번 째 문자 : ";
        std::cin >> W;
        std::cout << "횟수 : ";
        int n;
        std::cin >> n;
        for (int j=0;j<n;j++) {
            std::string s;
            std::cin >> s;
            
            for (int k=0;k<101;k++)
                memset(cache+k, -1, 101);
            
            if (match(W, s) == 1)
                std::cout << s << std::endl;
            else
                std::cout << "결과 없음" << std::endl;
        }
        
    }
    
    return 0;
}


//100
//he?p
//3
//help
//heap
//helpp
//*p*
//4
//hp
//help
//papa
//hello
//*bb*
//1
//babbbc
//1
//******a
//aaaaaaaaaab
//??
//3
//a
//aa
//aaa

```

## 예제: 삼각형 위의 최대 경로(문제 ID: TRIANGLEPATH, 난이도: 하)

### 무식하게 메모이제이션 적용하기
- 함수 정의
    path1(y, x, sum) = 현재 위치가 (y,x)이고, 지금까지 만난 수의 합이 sum일 때, 이 경로를 맨 아래줄까지 연장해서 얻을 수 있는 최대 합을 반환한다. <br>

- 점화식
    path1(y,x,sum) = max { path1(y+1, x, sum+triangle[y][x]), path1(y+1, x+1, sum+triangle[y][x]) }
<br><br>
```c++
// 코드 8.8 삼각형 위의 최대 경로 문제를 푸는 메모이제이션 코드 (1)

// MAX_NUMBER: 한 칸에 들어갈 숫자의 최대치
int n, triangle[100][100];
int cache[100][100][MAX_NUMBER*100+1];

// (y,x) 위치까지 내려오기 전에 만난 숫자들의 합이 sum일 때
// 맨 아래줄까지 내려가면서 얻을 수 있는 최대 경로를 반환한다.
int path1(int y, int x, int sum) {
    // 기저 사례: 맨 아래 줄까지 도달했을 경우
    if (y == n-1) return sum + triangle[y][x];
    // 메모이제이션
    int& ret = cache[y][x][sum];
    if (ret != -1) return ret;
    sum += triangle[y][x];
    return ret = std::max(path1(y+1, x+1, sum), path1(y+1, x, sum));
}
```

### 입력 걸러내기
- 함수 정의
    path2(y, x) = (y,x)에서 시작해서 맨 아래줄까지 내려가는 부분 경로의 최대 합을 반환한다. <br>

- 점화식
    path2(y, x) = triangle[y][x] + max { path2(y+1, x), path2(y+1, x+1) }

```c++
// 코드 8.9 삼각형 위의 최대 경로 문제를 푸는 동적 계획법 알고리즘 (2)
// 시간 복잡도 O(n) = n^2

int n, triangle[100][100];
int cache2[100][100];

// (y, x) 위치부터 맨 아래줄까지 내려가면서 얻을 수 있는 최대 경로의 합을 반환한다.
int path2(int y, int x) {
    // 기저 사례
    if (y == n-1) return triangle[y][x];

    // 메모이제이션
    int& ret = cache2[y][x];
    if (ret != -1) return ret;
    return ret = std::max(path2(y+1, x), path2(y+1, x+1)) + triangle[y][x];
}
```

## 예제: 최대 증가 부분 수열 (문제 ID: LIS, 난이도: 하)

### 완전 탐색에서 시작하기
```c++
// 코드 8.10 최대 증가 부분 수열 문제를 해결하는 완전 탐색 알고리즘

#include <vector>

int lis(const std::vector<int>& A) {
    // 기저 사례: A가 텅 비어 있을 때
    if (A.empty()) return 0;
    int ret = 0;
    for (int i = 0; i < A.size(); ++i) {
        std::vector<int> B;
        for (int j = i+1; j < A.size(); ++j)
            if (A[i] < A[j])
                B.push_back(A[j]);
        ret = std::max(ret, 1 + lis(B));
    }
    return ret;
}
```

### 입력 손보기
```c++
// 코드 8.11 최대 증가 부분 수열 문제를 해결하는 동적 계획법 알고리즘(1)

int n;
int cache[100], S[100];

// S[start]에서 시작하는 증가 부분 수열 중 최대 길이를 반환한다.
int lis2(int start) {
    int& ret = cache[start];
    if (ret != -1) return ret;
    // 항상 S[start]는 있기 때문에 길이는 최하 1
    ret = 1;
    for (int next = start+1; next < n; ++next)
        if (A[start] < A[next])
            ret = std::max(ret, lis2(next) + 1);
    return ret;
}

// 시작 위치 고정하기
int main(void) {
    
    int maxLen = 0;
    for (int begin = 0; begin < n; ++begin)
        maxLen = std::max(maxLen, lis2(begin));

    return 0;
}
```

- 문제점: lis2()를 호출할 때는 항상 증가 부분 수열의 시작 위치를 지정해 줘야 하므로, <br>
        처음에 호출할 때 각 위치를 순회하면서 최대 값을 찾는 다음과 같은 형태의 코드를 작성해야된다.
<br><br>
    
    <p align="center">
        <img width="1440" alt="스크린샷 2022-01-24 오후 6 27 10" src="https://user-images.githubusercontent.com/91893721/150756405-111ed6bc-2e77-4550-94f9-602700a1c7cc.png">


### 시작 위치 고정하기
```c++
// 코드 8.12 최대 증가 부분 수열 문제를 해결하는 동적 계획법 알고리즘 (2)

int n;
int cache[101], S[100];

// S[start]에서 시작하는 증가 부분 수열 중 최대 길이를 반환한다.
int lis3(int start) {
    int& ret = cache[start+1];
    if (ret != -1) return ret;
    // 항상 S[start]는 있기 때문에 길이는 최하 1
    ret = 1;
    for (int next = start+1; next < n; ++next)
        if (start == -1 || S[start] < S[next])
            ret = std::max(ret, lis3(next) + 1);
    return ret;
}
```

## 내 코드

```c++
#include <iostream>
#include <vector>
#include <memory.h>


// 내 생각) 시간 복잡도 O(n) = n^2
// help 의미 : 바로 앞의 index (만약, 앞이 없으면 -1)
//
// help == -1 : x번째부터 재귀 시작
// help != -1 : 1. ls[help] >= ls[x]
//                  -> 다음 index로 재귀 호출
//              2. ls[help] < ls[x]
//                  1) x번째를 포함하지 않는 경우
//                  2) x번째를 포함시키는 경우

std::vector<int> ls;
int cache[100];
int M = 0;

int recursive(int x, int help) {
    // base case
    if (x == ls.size()) return 0;
    
    // memoization
    int& ret = cache[x];
    
    if (help == -1) {
        if (ret != -1)
            return ret;
        return ret = recursive(x+1, x) + 1;
    }
    
    if (ls[help] >= ls[x])
        return recursive(x+1, help);
    
    else if (ret != -1)
        return ret;
    
    else {
        int a = recursive(x+1, help);
        ret = recursive(x+1, x) + 1;
        return std::max<int>(a, ret);
    }
}

int main(void) {
    M = 0; ls.clear();
    std::memset(cache, -1, 100);
    
    ls.push_back(2);
    ls.push_back(1);
    ls.push_back(5);
    ls.push_back(2);
    ls.push_back(4);
    ls.push_back(7);
    recursive(0, -1);
    for (int i=0;i<100;i++) {
        M = (cache[i] > M) ? cache[i] : M;
    }
    std::cout << "M : " << M << std::endl;
    
    M = 0; ls.clear();
    std::memset(cache, -1, 100);
    
    ls.push_back(1);
    ls.push_back(3);
    ls.push_back(4);
    ls.push_back(2);
    ls.push_back(4);
    recursive(0, -1);
    for (int i=0;i<100;i++) {
        M = (cache[i] > M) ? cache[i] : M;
    }
    std::cout << "M : " << M << std::endl;
    
    M = 0; ls.clear();
    std::memset(cache, -1, 100);
    
    ls.push_back(2);
    ls.push_back(1);
    ls.push_back(5);
    ls.push_back(2);
    ls.push_back(4);
    ls.push_back(7);
    recursive(0, -1);
    for (int i=0;i<100;i++) {
        M = (cache[i] > M) ? cache[i] : M;
    }
    std::cout << "M : " << M << std::endl;
    
    M = 0; ls.clear();
    std::memset(cache, -1, 100);
    ls.push_back(1);
    ls.push_back(1);
    ls.push_back(1);
    ls.push_back(1);
    ls.push_back(1);
    ls.push_back(1);
    recursive(0, -1);
    for (int i=0;i<100;i++) {
        M = (cache[i] > M) ? cache[i] : M;
    }
    std::cout << "M : " << M << std::endl;
    return 0;
}
```

### 더 빠른 해법

O(nlgn)
<br><br>

## 8.5 문제: 합친 LIS (문제 ID: JLIS, 난이도: 하)
    
<p align="center">
<img width="1440" alt="스크린샷 2022-01-24 오후 6 15 44" src="https://user-images.githubusercontent.com/91893721/150754591-5edd3220-9bca-43a0-ae95-1338784b85ba.png">



```c++
// 코드 8.13 합친 LIS 문제를 해결하는 동적 계획법 알고리즘

// 입력이 32비트 부호 있는 정수의 모든 값을 가질 수 있으므로
// 인위적인 최소치는 64비트여야 한다.
const long long NEGINF = numeric_limits<long long>::min();
int n, m, A[100], B[100];
int cache[101][101];

// min(A[indexA], B[indexB]), max(A[indexA], B[indexB])로 시작하는
// 합친 증가 부분 수열의 최대 길이를 반환한다.
// 단 indexA == indexB == -1 혹은 A[indexA] != B[indexB]라고 가정한다.
int jlis(int indexA, int indexB) {
    // 메모이제이션
    int& ret = cache[indexA+1][indexB+1];
    if (ret != -1) return ret;

    // A[indexA], B[indexB]가 이미 존재하므로 2개는 항상 있다.
    ret = 2;
    long long a = (indexA == -1 ? NEGINF : A[indexA]);
    long long b = (indexB == -1 ? NEGINF : B[indexB]);
    long long maxElement = std::max(a, b);
    // 다음 원소를 찾는다.
    for (int nextA = indexA + 1; nextA < n; ++nextA)
        if (maxElement < A[nextA])
            ret = std::max(ret, jlis(nextA, indexB) + 1);
    for (int nextB = nextB + 1; nextB < m; ++nextB)
        if (maxElement < B[nextB])
            ret = std::max(ret, jlis(indexA, nextB) + 1);
    return ret;
}
```
<br><br>
## 8.7 문제: 원주율 외우기(문제 ID: PI, 난이도: 하)
    
        경우                              예               난이도
    모든 숫자가 같을 때                     333, 5555           1
    숫자가 1씩 단조 증가하건 단조 감소할 때         23456, 3210         2
    두 개의 숫자가 번갈아가며 나타날 때            323, 54545          4
    숫자가 등차 수열을 이룰 때                     147, 8642           5
    이 외의 모든 경우                          17912, 331          10


### 점화식
<img width="400" alt="스크린샷 2022-01-26 오후 5 28 17" src="https://user-images.githubusercontent.com/91893721/151128791-d222f8c1-4a80-4ad8-9a3e-ce1181440007.png">
    
    - 길이가 3인 조각의 난이도 + 3글자 빼고 나머지 수열에 대한 최적해
    - 길이가 4인 조각의 난이도 + 4글자 빼고 나머지 수열에 대한 최적해
    - 길이가 5인 조각의 난이도 + 5글자 빼고 나머지 수열에 대한 최적해


    
    
```c++
// 코드 8.14 원주율 외우기 문제를 해결하는 동적 계획법 알고리즘

#include <string>

const int INF = 987654321;
std::string N;

// N[a..b] 구간의 난이도를 반환한다.
int classify(int a, int b) {
    // 숫자 조각을 가져온다.
    std::string M = N.substr(a, b-a+1);
    // 첫 글자만으로 이루어진 문자열과 같으면 난이도는 1
    if (M == std::string(M.size(), M[0])) return 1;
    // 등차수열인지 검사한다.
    bool progressive = true;
    for (int i = 0; i < M.size()-1; ++i)
        if (M[i+1] - M[i] != M[1] - M[0])
            progressive = false;
    // 등차수열이고 공차가 1 혹은 -1이면 난이도는 2
    if (progressive && abs(M[1] - M[0]) == 1)
        return 2;
    // 두 수가 번갈아 등장하는지 확인한다.
    bool alternating = true;
    for (int i = 0; i < M.size(); ++i)
        if (M[i] != M[i%2])
            alternating = false;
    // 두 수가 번갈아 등장하면 난이도는 4
    if (alternating) return 4;
    // 공차가 1 아닌 등차수열의 난이도는 5
    if (progressive) return 5;
    // 이 외는 모두 난이도 10
    return 10;
}

int cache[10002];
// 수열 N[begin..]를 외우는 방법 중 난이도의 최소 합을 출력한다.
int memorize(int begin) {
    // 기저 사례: 수열의 끝에 도달했을 경우
    if (begin == N.size()) return 0;
    // 메모이제이션
    int& ret = cache[begin];
    if (ret != -1) return ret;
    ret = INF;
    for (int L = 3; L <= 5; ++L)
        if (begin + L <= N.size())
            ret = std::min(ret, memorize(begin + L) + classify(begin, begin + L - 1));
    return ret;
}
```
    
<img width="600" alt="스크린샷 2022-01-26 오후 5 28 03" src="https://user-images.githubusercontent.com/91893721/151128899-0b1e42e7-e240-4fb0-a84a-373b34a16887.png">

## 
##
