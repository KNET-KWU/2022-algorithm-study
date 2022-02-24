# 13. 수치해석

## 13.1 도입
수치해석(numerical analysis)은 직접 풀기 힘든 수학 문제를 근사적으로 푸는 알고리즘과 이들의 수치적 안정성, 오차의 범위 등을 연구하는 전산학의 한 분야로, 공학, 과학, 금융공학 등 다양한 범위에 널리 사용된다.

<br><br><br><br>

## 13.2 이분법 ( bisection method )

이분법은 주어진 범위 [lo, hi] 내에서 어떤 함수 f(x)의 값이 0이 되는 지점을 수치적으로 찾아내는 기법이다. <br>
꼭 단조 감소가 아니고, 답이 여러 개 있는 함수라도 연속이기만 하다면 이분법을 사용해 근을 찾을 수 있다. <br>

<p align="center">
<img width="257" alt="스크린샷 2022-02-21 오후 2 59 50" src="https://user-images.githubusercontent.com/91893721/154897940-df024c3f-32d7-4bd4-a690-025e5dbc1df4.png">
<br><br>
  
```c++
// 코드 13.1 이분법의 예제 구현

#include <cmath>
#include <algorithm>

double f(double x);

// 이분법의 예제 구현
double bisection(double lo, double hi) {
    // 반복문 불변식을 강제한다.
    if (f(lo) > 0)
        std::swap(lo, hi);
    
    // 반복문 불변식: f(lo) <= 0 < f(hi)
    while (std::fabs(hi - lo) > 2e-7) {
        double mid = (lo + hi) / 2;
        double fmid = f(mid);

        if (fmid <= 0)
            lo = mid;
        else
            hi = mid;
    }

    // 가운데 값을 반환한다.
    return (lo + hi) / 2;
}
```
<br>
[ 코드 특징 ] <br>
  
- f(lo)와 f(hi)의 부혹 다르다는 조건 대신 f(lo)는 항상 0 이하이고 f(hi)는 항상 양수라는 반복문 불변식을 유지한다.
- f(x)가 0을 반환하는 경우를 딸 처리하지 않는다. f(lo)<=0<f(hi)라는 불변식만 유지한다면 항상 답이 수렴하기 때문이다.
- bisection()은 반복문이 모두 종료한 후에는 답의 후보 구간의 중간 값을 반환한다. 반복문 불변식에 의하면 우리가 원하는 답은 [lo,hi]안에 있는데, 이 중 최대 오차를 최소화할 수 있는 답이 중간 값이기 때문이다.
<br><br><br><br>
  
  
## 절대 오차 vs 상대 오차 (절대 오차의 문제점)

- 문제점: 부동 소수점은 가수부(mantissa)라고 부르느 정수 부분과 이 변수에서 소수점의 위치를 나타내는 지수부(exponent)의 조합으로 표현되기 때문에, 표현할 수 있는 수의 집합이 제한되어 있다. <br>
- 결론: 따라서 숫자의 절대 값이 커지면 커질수록 표현할 수 있는 수들이 듬성듬성해지게 된다. <br><br>

```c++
// 코드 13.2 영원히 종료하지 않는 이분법의 예

#include <cmath>
#include <iostream>

// 종료되지 않는다.
void infiniteBisection() {
    double lo = 123456123456.1234588623046875;
    double hi = 123456123456.1234741210937500;

    while (std::fabs(hi - lo) > 2e-7)
        hi = (lo + hi) / 2.0;
    printf("finished!\n");
}
```
[ 위 코드가 문제인 이유 ] <br>
lo와 hi 사이에는 double 변수가 표현할 수 있는 값이 하나도 없다. (사용하는 컴퓨터의 아키텍처나 컴파일러에 따라 달라질 수도 있다.) <br>
따라서 (lo+hi)/2는 결국 hi와 같은 값이 된다. 그런데 hi와 lo 사이의 거리는 10^-7보다 훨씬 크기 때문에, 이 함수(while문)는 절대로 종료되지 않는다. <br><br>

<p align="center">
<img width="1000" alt="스크린샷 2022-02-21 오후 3 29 00" src="https://user-images.githubusercontent.com/91893721/154900912-06565ee2-fbcf-4f66-b18e-c848b5432cde.png">

<br>
<p align="center">
<img width="700" alt="스크린샷 2022-02-21 오후 3 48 13" src="https://user-images.githubusercontent.com/91893721/154903179-b0b1dbaa-95b1-4335-a1d7-0dbb6b14fd80.png">
<p align="center">
<img width="700" alt="스크린샷 2022-02-21 오후 3 48 01" src="https://user-images.githubusercontent.com/91893721/154903171-30a2b4ac-65c6-4f18-9674-badcba24737e.png">

  
<br>
<p align="left">
[ 또 다른 문제점 ] <br>
단 이 조건은 lo와 hi가 전부 양수라고 가정한다. 두 수가 음수인 경우, lo는 음수이고 hi는 양수인 경우 등을 모두 고려하려면 코드느 더더욱 복잡해진다. <br> 따라서 경험 많은 프로그래밍 대회 참가자들은
이와 같은 방법을 쓰지 않는다.
<br><br><br><br>
  
## 정해진 횟수만큼 반복하기
  
단순해 보이지만 가장 유용한 방법은 while문을 적당한 for문으로 대체해서 반복문이 항상 정해진 횟수만큼 실행하도록 하는 것이다. <br> 반복문을 100번 수행하면 우리가 반환하는 답은 절대 오차는 최대 | lo - hi | / (2^101
)이 된다. <br> 2^101은 대략 서른한 자리의 수로, | lo - hi | 가 대략 10^20미만의 수라면 이 오차는 항상 10^-7보다 작다는 것을 알 수 있다. <br>
따라서 큰 숫자를 다루는 경우에도 충분히 답을 구할 수 있다. 또한 이와 같은 방법은 절대로 무한 반복에 빠지지 않으며, 프로그램의 최대 수행 시간을 예상하기도 쉽다는 장점이 있다.
<br><br><br><br>
  
  
## 예제: 다변수 다항 방정식의 수치적 해법 (문제 ID: ROOTS, 난이도: 중)
  
<p align="center">
<img width="900" alt="스크린샷 2022-02-21 오후 4 43 39" src="https://user-images.githubusercontent.com/91893721/154910248-dbd719c8-fdc5-4078-b2ac-d1c8993fdcab.png">

  
```c++
// https://algospot.com/judge/problem/read/ROOTS

#include <iostream>
#include <vector>
#include <cmath>
#include <algorithm>

// 단일 변수 다항식 p의 계수가 주어질 때 미분한 결과 p'의 계수를 반환한다.
std::vector<double> differentiate(const std::vector<double>& poly) {
    int n = poly.size() - 1;
    std::vector<double> result;

    // 미분
    for (int i = 0; i < n; i++)
        result.push_back((n - i) * poly[i]);
    return result;
}

// 1차 혹은 2차 방정식을 푼다.
std::vector<double> solveNaive(const std::vector<double>& poly) {
    int n = poly.size() - 1;
    std::vector<double> result;

    switch (n) {
    case 1:
        result.push_back(-poly[1] / poly[0]);
        break;
    case 2:
        // ax^2 + bx + c
        double a = poly[0], b = poly[1], c = poly[2];

        // 근의 공식
        result.push_back((-b + sqrt(pow(b, 2) - 4 * a*c)) / (2 * a));
        result.push_back((-b - sqrt(pow(b, 2) - 4 * a*c)) / (2 * a));
        break;
    }
    
    // ??? 근은 항상 정렬된 형태로
    sort(result.begin(), result.end());
    return result;
}

// 다항식 f(x)의 계수 poly가 주어질 때, f(x0)를 반환한다.
double evaluate(const std::vector<double>& poly, double x0) {
    int n = poly.size() - 1;
    double result = 0;

    // 대입
    for (int i = 0; i <= n; i++)
        result += pow(x0, n - i) * poly[i];
    return result;
}

// 방정식의 해의 절대 값은 L 이하여야 한다.
const double L = 10;

// 이분법의 예제 구현
double bisection(double lo, double hi, const std::vector<double>& poly) {
    // 반복문 불변식을 강제한다.
    if (evaluate(poly, lo) > 0)
        std::swap(lo, hi);
    
    // 반복문 불변식: f(lo) <= 0 < f(hi)
    while (std::fabs(hi - lo) > 1e-9) {
        double mid = (lo + hi) / 2;
        double fmid = evaluate(poly, mid);

        if (fmid <= 0)
            lo = mid;
        else
            hi = mid;
    }

    // 가운데 값을 반환한다.
    return (lo + hi) / 2;
}

// 방정식 sum(poly[i]*x^i)=0의 근을 반환한다.
std::vector<double> solve(const std::vector<double>& poly) {
    int n = poly.size() - 1;

    // 기저 사례: 2차 이하의 방정식들은 풀 수 있다.
    if (n <= 2) return solveNaive(poly);

    // 방정식을 미분해서 n-1차 방정식을 얻는다.
    std::vector<double> derivative = differentiate(poly);
    std::vector<double> sols = solve(derivative);

    
    // 이 극점들 사이를 하나하나 검사하며 근이 있나 확인한다.
    // sols의 맨 앞에 -L-1을 삽입하고 맨 뒤에는 L+1을 삽입한다.
    // 이 극점들 사이를 하나하나 검사하며 근이 있나 확인한다.
    // 초기 극점과 마지막 극점은 비교대상이 없으므로 ±L과 비교
    
    sols.insert(sols.begin(), -L-1);
    sols.insert(sols.end(), L+1);
    
    // 위 두 줄을 더 엄밀하게 작성하면 다음과 같다.
//    if (sols.front() != -L)
//        sols.insert(sols.begin(), -L);
//    if (sols.back() != L)
//        sols.insert(sols.end(), L);

    // 기존의 다항식의 해를 저장하고 반환하기 위해 만든 변수
    std::vector<double> ret;
    for (int i = 0; i+1 < sols.size(); ++i) {
        double x1 = sols[i], x2 = sols[i+1];
        double y1 = evaluate(poly, x1), y2 = evaluate(poly, x2);

        // f(x1)과 f(x2)의 부호가 같은 경우 답은 없다.
        if (y1*y2 > 0) continue;

        // 불변 조건: f(x1) <= 0 < f(x2)
        if (y1 > y2) {
            std::swap(y1, y2);
            std::swap(x1, x2);
        }

        // 이분법을 사용하자.
        ret.push_back(bisection(x1, x2, poly));
    }
    
    // unique 함수는 algorithm 헤더 파일 안에 있다.
    // unique 함수를 이용하기 전에 무조건 sort를 해야된다!
    // unique를 사용하면 중복된 원소를 뒷부분으로 옮긴다.
    // unique가 끝났으면 반환되는 값은 vector의 쓰레기값의 첫번째 위치가 되는데, 이 때문에 바로 unique 후 erase가 가능하게된다.
    sort(ret.begin(), ret.end());
    ret.erase(std::unique(ret.begin(), ret.end()), ret.end());
    return ret;
}

int main(void) {
    int C; std::cin >> C;
    
    for (int i=0;i<C;i++) {
        int n; std::cin >> n;
        
        std::vector<double> poly;
        for (int j=0;j<=n;j++) {
            double k; std::cin >> k;
            poly.push_back(k);
        }
        
        std::vector<double> ans;
        ans = solve(poly);
        for (double x : ans)
            printf("%.12lf ", x);
        std::cout << std::endl;
    }
    
    return 0;
}

//4
//3
//1.00 -6.00 11.00 -6.00
//2
//1.00 -12.50 31.50
//3
//1 0 -121 0
//4
//1 0 -200 0 10000


// 예제 출력
//1.000000000000 2.000000000000 3.000000000000
//3.500000000000 9.000000000000
//0
```
  
<br><br><br><br>
## 예제: 전세금 균등상환 (문제 ID: LOAN, 난이도: 하)

전세금 N원을 연 이율 P%로 대출받고, 이것을 M(1<=M<=120)개월 동안 매달 일정액 C원씩 갚는다고 하자. <br>
  
- 대출의 잔금은 대출 금액 N원에서 시작한다.
- 한 달이 지날 때마다 대출 잔금이 월 이자 P/12% 만큼 불어난다.
- 이자가 추가된 다음 월 상환액 C를 대출 잔금에서 제한다.
<br>
  
<p align="center">
<img width="900" alt="스크린샷 2022-02-21 오후 5 03 22" src="https://user-images.githubusercontent.com/91893721/154912980-c4b06756-1bdf-49c1-92c2-a535bced8193.png">
<br>
  
```c++
// 코드 13.4 전세금 균등상환 문제를 해결하는 이분법의 구현

#include <iostream>

// amount원을 연 이율 rates퍼센트로 duration월 간 한 달에 monthlyPayment로
// 남았을 때 대출 잔금은?
double balance(double amount, int duration, double rates, double monthlyPayment) {
    double balance = amount;

    for (int i = 0; i < duration; ++i) {
        // 이자가 붙는다.
        balance *= (1.0 + (rates / 12.0) / 100.0);
        // 상환액을 잔금에서 제한다.
        balance -= monthlyPayment;
    }

    return balance;
}

// amount원을 연 이율 rates퍼센트로 duration월 간 갚으려면 한 달에 얼마씩 갚아야 하나?
double payment(double amount, int duration, double rates) {
    // 불변 조건:
    // 1. lo원씩 갚으면 duration개월 안에 갚을 수 없다.
    // 2. hi원씩 갚으면 duration개월 안에 갚을 수 있다.
    double lo = 0, hi = amount * (1.0 + (rates / 12.0) / 100.0);

    for (int iter = 0; iter < 100; ++iter) {
        double mid = (lo + hi) / 2.0;

        if (balance(amount, duration, rates, mid) <= 0)
            hi = mid;
        else
            lo = mid;
    }
    return hi;
}

int main(void) {
    int T; std::cin >> T;
    
    for (int i=0;i<T;i++) {
        double N, M, P;
        std::cin >> N >> M >> P;
        printf("%.12lf ", payment(N, M, P));
    }
    
    return 0;
}


//4
//20000000 12 6.8
//35000000 120 1.1
//40000000 36 0.5
//100 120 0.1


// 출력
//1728691.4686372071
//308135.8967737053
//1119696.7387703573
//0.8375416659
```
<br><br><br><br>
  
## 문제: 승률 올리기 (문제 ID: RATIO, 난이도: 하)
  
친구들 사이에서 가장 마이티를 잘하는 도경이는 지금까지 N게임을 해서 M게임을 승리했다. <br>
이제 표시되는 승리 확률이 1%만 더 올리려면 최소 몇 게임을 해야 할까?
  
```c++
// 코드 13.5 승률 올리기 문제를 해결하는 이분법 알고리즘
// 시간 복잡도) O(lgL)

// [ 특징 ]
// 1. 승리 확률을 백분율로 표시하되, 정수부만 표시한다.
// 2. 코드에서 64비트 정수를 중간중간 사용한 것을 확인할 수 있다.
//    우리의 반환 값은 32비트 부호 있는 정수라도, 계산 중간에서 범위를 초과하는 값이 나타날 수 있기 때문이다.
//    승률을 계산하기 위해 100을 곱하는 부분이나, L연승했을 때 승률이 변하는지 확인하는 부분 등이 대표적인 예.

#include <iostream>

// 도경이가 할 생각이 있는 최대 게임 수 => 20억
long long L = 2000000000;

// b게임 중 a게임 승리했을 때의 승률
int ratio(long long b, long long a) {
    return (a * 100) / b;
}

// 최소 몇 연승해야 승률이 올라갈까?
int neededGames(long long games, long long won) {
    // 불가능한 경우를 미리 걸러낸다.
    if (ratio(games, won) == ratio(games + L, won + L))
        return -1;
    
    long long lo = 0, hi = L;
    // 반복문 불변식:
    // 1. lo게임 이기면 승률은 변하지 않는다.
    // 2. hi게임 이기면 승률은 변한다.

    while (lo + 1 < hi) {
        long long mid = (lo + hi) / 2;
        if (ratio(games, won) == ratio(games + mid, won + mid))
            lo = mid;
        else
            hi = mid;
    }

    return hi;
}

int main(void) {
    int T; std::cin >> T;
    for (int i=0;i<T;i++) {
        int N, M; std::cin >> N >> M;
        std::cout << neededGames(N, M) << std::endl;
    }
    return 0;
}

//5
//10 8
//100 80
//47 47
//99000 0
//1000000000 470000000


// 출력
//1
//6
//-1
//1000
//19230770
```
  
<br><br>
  
## 수학적 해법
  
이와 같은 방법 외에, 조금 돌아가면 상수 시간에 동작하는 해법도 찾을 수 있다. <br>
현재 N게임 중 M게임을 승리했고, 승률 R이 되고 싶다고 하자. 승률 R이 되는 최소의 연승 수 x에 대해 다음 식이 성립한다. <br><br>
  
<p align="center">
<img width="700" alt="스크린샷 2022-02-21 오후 5 20 10" src="https://user-images.githubusercontent.com/91893721/154915343-9a5e1411-282f-4c45-a4bc-d67dbf459370.png">

  
```c++
// 수학적 해법

#include <iostream>
#include <cmath>

// 도경이가 할 생각이 있는 최대 게임 수 => 20억
long long L = 2000000000;

// b게임 중 a게임 승리했을 때의 승률
int ratio(long long b, long long a) {
    return (a * 100) / b;
}

int math_method(long long games, long long won) {
    // 불가능한 경우를 미리 걸러낸다.
    if (ratio(games, won) == ratio(games + L, won + L))
        return -1;
    
    long long R = ((100 * won) / games + 1);
    if ((R * games - 100 * won) % (100 - R) != 0)
        return (R * games - 100 * won) / (100 - R) + 1;
    else
        return (R * games - 100 * won) / (100 - R);
}

int main(void) {
    int T; std::cin >> T;
    for (int i=0;i<T;i++) {
        int N, M; std::cin >> N >> M;
        std::cout << math_method(N, M) << std::endl;
    }
    return 0;
}

//5
//10 8
//100 80
//47 47
//99000 0
//1000000000 470000000


// 출력
//1
//6
//-1
//1000
//19230770
```
  
<br><br><br><br>
  
## 13.5 삼분 검색
  
[ 유니모달 함수 ( unimodal function ) ] <br>
- 지역 극대점(local maximum)이란 그래프에서 양쪽 점보다 높이 있는 점을 말한다. 이와 같은 함수를 하나의 극대(mode)가 있다는 의미에서 유니모달 함수(unimodal function)라고 부른다.
- 최대점 왼쪽에서는 시간이 증가함에 따라 만족도가 항상 순증가(strictly increasing)한다.
- 최대점 오른쪽에서는 시간이 증가함에 따라 만족도가 항상 순감소(strictly decreasing)한다.
<br><br>

[ 삼분 탐색 ( ternary search ) ] <br>
세 가지의 조건을 만족하는 함수에서 최대점을 찾을 때 유용하게 사용할 수 있는 기법으로 삼분 검색(ternary search)이 있다. <br>
이분법이 매 반복마다 답의 후보 구간을 절반으로 잘라 각 위치에서 함수의 값을 계산하는 것과 비슷하게, 삼분 검색은 답의 후보 구간을 삼등분하는 두 위치에서 함수의 값을 계산한다.
  
<br><br>
그래프의 (lo, hi) 범위를 두 개의 중간 지점이 삼분했을 때 고려해야 할 경우의 수는 다음과 같다. <br>

<p align="center">
<img width="713" alt="스크린샷 2022-02-21 오후 5 39 39" src="https://user-images.githubusercontent.com/91893721/154918587-8358189e-f9b0-4d2e-baf7-41f4593ae56d.png">
<img width="617" alt="스크린샷 2022-02-21 오후 5 39 46" src="https://user-images.githubusercontent.com/91893721/154918646-84cc25e4-bff1-43d7-86cc-fbf4c12fddb9.png">
<img width="641" alt="스크린샷 2022-02-21 오후 5 40 01" src="https://user-images.githubusercontent.com/91893721/154918652-c9e3b85f-9674-422f-b67a-e7cccd5c21b8.png">
<br>
 
삼분 검색에서는 구간의 크기가 매 반복마다 2/3로 줄어들기 때문에 n회 반복하고 나면 구간의 길이는 | hi - lo | (2/3)^n 이 된다. <br>
100번 수행하고 나면 대개의 경우 충분히 정확한 값을 얻을 수 있다. <br>

```c++
// 코드 13.6 삼분 검색의 구현

// 우리가 최대치를 찾고 싶어하는 함수
double f(double x);

// [lo, hi] 구간에서 f(x)가 최대치를 갖는 x를 반환한다.
double ternary(double lo, double hi) {
    for (int iter = 0; iter < 100; iter++) {
        double a = (2*lo + hi) / 3.0;
        double b = (lo + 2*hi) / 3.0;

        if (f(a) > f(b))
            hi = b;
        else
            lo = a;
    }

    return (lo+hi) / 2.0;
}
```
<br><br>
  
## 볼록 함수(concave function)와 오목 함수(convex function)
  
<p align="center">
<img width="447" alt="스크린샷 2022-02-21 오후 5 48 26" src="https://user-images.githubusercontent.com/91893721/154920011-e7cededf-5b49-4300-b26f-ef20ccae8ec6.png">
<br>

이들 함수는 삼분 검색의 요구 조건을 거의 만족하기 때문에, 볼록 함수의 최소치를 구하거나 오목 함수의 최대치를 구하는 작업을 삼분 검색으로 쉽게 풀 수 있다.
<br><br>
  
[ 볼록 함수(concave function)와 오목 함수(convex function) ] <br>
- f(x)가 볼록 함수라면 -f(x)는 오목 함수이다. 물론 f(x)가 오목 함수라면 -f(x)는 볼록 함수이다.
- 볼록 함수와 볼록 함수를 더한 결과도 볼록 함수이다.
- 볼록 함수와에 상수를 곱한 결과도 볼록 함수이다.
  
  
## 예제: 철인 2종 경기


  
  
  
```c++
// https://onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=1326

#include <iostream>
#include <vector>

double t;   // 전체 거리
std::vector<double> runSpeed, cycleSpeed;

// 달리기 구간의 길이가 run일 때, i번 선수가 걸리는 시간
double time(int i, double run) {
    double cycle = t - run;
    return run / runSpeed[i] + cycle / cycleSpeed[i];
}


// 교재)
// cheater(r) = time<n-1>(r)
// others(r) = min [ i = 0 ~ n-2 ] time<i>(r)

// 달리기 구간 길이가 r일 때, others(r)-cheater(r) 를 반환한다.
double diff(double r) {
    int n = runSpeed.size();
    double cheater = time(n-1, r);

    double others = time(0, r);
    for (int i = 1; i < n-1; ++i)
        others = std::min(others, time(i, r));

    return others - cheater;
}

// diff() 함수의 최대치의 위치를 삼분 검색으로 계산한다.
double maxDifference() {
    double lo = 0, hi = t;
    
    for (int it = 0; it < 100; ++it) {
        double aab = (2*lo + hi) / 3.0;
        double abb = (lo + 2*hi) / 3.0;

        if (diff(aab) > diff(abb))
            hi = abb;
        else
            lo = aab;
    }
    return (lo + hi) / 2;
}

int main(void) {
    int T; std::cin >> T;
    for (int i=0;i<T;i++) {
        int n; std::cin >> t >> n;
        for (int j=0;j<n;j++) {
            double k1, k2; std::cin >> k1 >> k2;
            runSpeed.push_back(k1); cycleSpeed.push_back(k2);
        }
        
        double ans = maxDifference();
        double time = diff(ans);
        if (time < 0) {
            std::cout << "cannot win" << std::endl;
            std::cout << "time : " << time << std::endl;
        }
        else {
            std::cout << "==================" << std::endl;
            std::cout << "r : " << ans << std::endl;
            std::cout << "k : " << t-ans << std::endl;
            std::cout << "time : " << time << std::endl;
        }
    }
}

//2
//100
//3
//10.0 40.0
//20.0 30.0
//15.0 35.0
//100
//3
//10.0 40.0
//20.0 30.0
//15.0 25.0


//Sample Output
//The cheater can win by 612 seconds with r = 14.29km and k = 85.71km.
//The cheater cannot win.
```
  
  
## 문제: 꽃가루 화석 (문제 ID: FOSSIL, 난이도: 상)

```c++
// https://algospot.com/judge/problem/read/FOSSIL
// code_13.8 과 code_13.9 융합

#include <vector>
#include <utility>
#include <iostream>

struct point {
    double y, x;
};

// 입력에 주어진 볼록 다각형
std::vector<point> hull1, hull2;

// 위 껍질에 속하는 선분들과 아래 껍질에 속하는 선분들
std::vector<std::pair<point, point> > upper, lower;

// hull이 반시계방향으로 주어지므로, 인접한 두 점에 대해 x가
// 증가하는 방향이면 아래쪽 껍질, x가 감소하는 방향이면 위쪽 껍질이다.
void decompose(const std::vector<point>& hull) {
    int n = hull.size();
    
    // 문제 조건 : 주어진 두 다각형의 모든 내각은 180도 미만입니다.
    for (int i = 0; i < n; i++) {
        if (hull[i].x < hull[(i+1)%n].x)
            lower.push_back(std::make_pair(hull[i], hull[(i+1)%n]));
        else if (hull[i].x > hull[(i+1)%n].x)
            upper.push_back(std::make_pair(hull[i], hull[(i+1)%n]));
    }
}

// [a.x, b.x] 구간 안에 x가 포함되나 확인한다.
bool between(const point& a, const point& b, double x) {
    return (a.x <= x && x <= b.x) || (b.x <= x && x <= a.x);
}

// (a, b) 선분과 주어진 위치의 수직선이 교차하는 위치를 반환한다.
double at(const point& a, const point& b, double x) {
    double dy = b.y - a.y, dx = b.x - a.x;
    return a.y + dy * (x - a.x) / dx;
}

// 두 다각형의 교집합을 수직선으로 잘랐을 때 단면의 길이를 반환한다.
double vertical(double x) {
    double minUp = 1e20, maxLow = -1e20;

    // 위 껍질의 선분을 순회하며 최소 y 좌표를 찾는다.
    for (int i = 0; i < upper.size(); ++i)
        if (between(upper[i].first, upper[i].second, x))
            minUp = std::min(minUp, at(upper[i].first, upper[i].second, x));

    // 아래 껍질의 선분을 순회하며 최대 x 좌표를 찾는다.
    for (int i = 0; i < lower.size(); ++i)
        if (between(lower[i].first, lower[i].second, x))
            maxLow = std::max(maxLow, at(lower[i].first, lower[i].second, x));

    return minUp - maxLow;
}

// 다각형에서 가장 작은 x값 반환
double minX(const std::vector<point>& a) {
    double ans = 1e20;
    for (int i = 0; i < a.size(); ++i)
        ans = std::min(ans, a[i].x);
    return ans;
}

// 다각형에서 가장 큰 x값 반환
double maxX(const std::vector<point>& a) {
    double ans = -1e20;
    for (int i = 0; i < a.size(); ++i)
        ans = std::max(ans, a[i].x);
    return ans;
}

double solve() {
    // 수직선이 두 다각형을 모두 만나는 x 좌표의 범위를 구한다.
    double lo = std::max(minX(hull1), minX(hull2));
    double hi = std::min(maxX(hull1), maxX(hull2));

    // 예외 처리: 두 다각형이 아예 겹치지 않는 경우
    if (hi < lo) return 0;

    // 삼분 검색
    for (int iter = 0; iter < 100; ++iter) {
        double aab = (lo*2 + hi) / 3.0;
        double abb = (lo + hi*2) / 3.0;
        if (vertical(aab) < vertical(abb))
            lo = aab;
        else
            hi = abb;
    }

    return std::max(0.0, vertical(hi));
}

int main(void) {
    int c; std::cin >> c;
    for (int i=0;i<c;i++) {
        int n, m; std::cin >> n >> m;
        
        hull1.clear(); hull2.clear();
        hull1.resize(n); hull2.resize(m);
        
        for (int j=0;j<n;j++)
            std::cin >> hull1[j].x >> hull1[j].y;
        for (int j=0;j<m;j++)
            std::cin >> hull2[j].x >> hull2[j].y;
        
        upper.clear(); lower.clear();
        decompose(hull1); decompose(hull2);
        
        printf("%.8lf \n", solve());
    }
    
    return 0;
}

//2
//5 5
//35.74 35.85 69.64 50.00 73.52 82.55 43.50 92.22 17.67 76.18
//16.47 8.02 60.98 14.62 66.80 37.74 45.89 67.22 13.04 55.19
//4 3
//73.84 11.41 71.61 32.72 39.87 38.84 22.41 17.87
//75.13 51.64 47.72 87.34 15.97 64.56


// 출력
//27.6529680365
//0.000000

```
  
  
  
  
  
  
  
  
##
  
  
  
