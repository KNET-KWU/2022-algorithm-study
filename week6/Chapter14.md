정수론
======

1. 소수
- 소인수 분해(prime factorization)
- 에라토스테네스의 체
- GCD (Euclidean Algorithm)
2. 모듈라 연산
#
#  Prime
- N이 소수인지 판별할 떄 sqrt(N)까지의 숫자만 필요하다 O(sqrt(N))
- 에라토스테네스의 체는 메모리제이션이라 생각하면 편하다
```cpp
// n까지의 숫자가 소수인지 판별
void eratosthenes(){
    // N까지의 숫자 전부 소수로 초기화
    memset(isPrime,1,sizeof(isPrime));
    // 1은 항상 예외처리
    isPrime[0]=isPrime[1]=false;
    // 루트N 까지만 찾아도 N이 소수인지 판별가능
    // 따라서 N이하의 모든 수가 소수인지 아닌지 판별가능
    
    // 여기서의 N은 max_N
    int sqrtn=int (sqrt(N));
   
    for(int i=2;i<=sqrtn;i++){
        if(isPrime[i]){
            //********************************************
            // i*i부터 flase로 바꾸는 이유는 i*2,i*3,'''는 이미 2와 3의 배수를 지우는 과정에서 지워졌기때문
            for(int j=i*i;j<=N;j+=i){
                isPrime[j]=false;
            }
        }
    }
}
int main(){
    eratosthenes();
    for(int i=2;i<N;i++){
        if(isPrime[i]) cout<<i<<" ";
    }
}
```
#
# 소인수 분해 (Prime Factorization)

```cpp
void SieveEratos(){
  int sqrtn = int(sqrt(MAX_NUM));

  for(int i=2; i<=MAX_NUM ; ++i)
    min_factor[i] = i;  //일단 모든 min_factor를 i 값을 해당 값으로 초기화
  
  for(int i=2; i<=sqrtn ; ++i)
    if(min_factor[i] == i)  //i가 소수인 경우만
      for(int j = i*i; j<=MAX_NUM ; j+=i)
        if(min_factor[j] ==j )  //체로 걸러지지 않은 경우 i가 j의 최소 소인수이다.
          min_factor[j] = i;
}

//n의 소인수들을 반환
vector<int> factor(int n){
  vector<int> ret;
  while(n>1){  //n이 1이 될때 까지
    ret.push_back(min_factor[n]);
    n /= min_factor[n];
  }
  return ret;
}
```

## 비밀번호 486 (ID : PASS486)

- 에라토스테네스의 체 응용
```cpp
void getFactorSmart(){
    factors[1] = 1;
    for(int n=2;n<=MAX;++n){
        //소수인 경우 약수가 2개밖에 없다
        if(minFactor[n] == n){
            minFactorPower[n] = 1;
            factors[n] = 2;
        }
        //소수가 아닌 경우
        else{
            //n을 n의 가장 작은 소인수로 나눈다
            int p = minFactor[n];
            int m = n / p;

            //n과 m의 가장 작은 소인수가 서로 다른 경우에는 n의 소인수에 p가 1개만 존재한다
            if(p != minFactor[m])
                minFactorPower[n] = 1;
            //n과 m의 가장 작은 소인수가 서로 같은 경우에는 
            //n의 소인수에 m의 가장 작은 소인수의 개수 + 1개가 존재한다
            else
                minFactorPower[n] = minFactorPower[m] + 1;

            int a = minFactorPower[n];
            factors[n] = (factors[m] / a) * (a + 1);
        }
    }
}
```
- 가능하면 어렵게 생각말고 Bruteforce로 쉽게 구하자
```cpp
void getFactorsBrute(){
    memset(factor,0,sizeof(factor));
    for (int div = 1; div <= TM; div++)
        for(int mul = div ; mul <= TM; mul+=div)
            factor[mul] +=1;
}
```
#
# GCD
- 백준 순환소수
```cpp
int gcd(int a,int b){
    //유클리드 호제법 사용
    if(b==0) return a;
    else return gcd(b,a%b);
}
int gcd(int a, int b){
    return b==0 ? a : gcd(b,a%b);
}
```
![GCD](https://user-images.githubusercontent.com/91093119/155197806-67c5d5b6-0acf-4820-9637-5f6ae7bcb58d.png)

# 모듈라 연산
- 8장의 POLY
