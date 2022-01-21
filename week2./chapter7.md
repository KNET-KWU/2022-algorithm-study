
# 7.1 도입
분할정복(Divide & conquer)은 가장 유명한 알고리즘 디자인 패러다임으로 주어진 문제를 둘 이상의 부분 문제로 나눈 뒤 각 문제에 대한 답을 재귀호출을 이용해 계산하고, 각 부분 문제의 답으로 부터 전체 문제의 답을 계산해 낸다.  <br><br>

<p align="center">
<img src = "https://user-images.githubusercontent.com/76817418/149627131-9c46d8f3-1078-4de3-bb87-fa528f0e53db.png" width=50%>

<br>
일반적 재귀함수와 분할정복의 차이를 보여주는 그림이다. <br><br></p>

    
- 분할 정복을 사용하는 알고리즘의 특성
    - devide : 문제를 더 작은 문제로 분할하는 과정
    - merge : 각 문제에 대해 구한 답을 원래 문제에 대한 답으로 병합하는 과정
    - base case : 더이상 답을 분할하지 않고 곧장 풀 수 있는 매우 작은 문제
<br><br>

> 분할정복은 문제를 나눔으로써 어려운 문제를 더 빠르게 해결할 수 있다는 장점이 있다. <br>
    그렇지만 함수를 재귀적으로 호출한다는 점에서 함수 호출로 인한 오버헤드가 발생하며, 스택에 다양한 데이터를 보관하고 있어야 하므로 스택 오버플로우가 발생하거나 과도한 메모리 사용을 하게 되는 단점이 있다.<br> 또한 다루려는 문제가 어떤 것이냐에 따라 알고리즘의 성능 차이가 많이 나게 된다. 
<br>

---

## 예제 : 수열의 빠른 합과 행렬의 빠른 제곱

다음의 두 코드는 분할정복과 재귀호출을 이용해 1부터 n까지의 합을 구하는 함수이다. 


```c++
//재귀함수
//1 부터 n까지의 합을 구하는 재귀함수는 6.2절에서 구현되어 있다.
int recursiveSum(int n){
    if(n==1)return 1;
    return n+recursiveSum(n-1);
}
```

1 부터 n 까지의 합을 분할정복으로 구현하는 아이디어는 다음과 같다.
- n이 짝수인 경우
  
	1부터 n까지의 합을 n개의 조각으로 나눈 뒤, 이들을 반으로 뚝 잘라 (n/2)개의 조각들로 만들어진 부분 문제 두개를 만듭니다.

<p align="center">
<img src = "https://user-images.githubusercontent.com/76817418/150475500-1588ac58-29b4-429b-8f3d-9aec544c00df.png">
</p>	

첫 번째 부분은 fastSum(n/2)로 나타낼 수 있고, 두 번째 부분 부터는 수식 정리를 통해 <img src="https://latex.codecogs.com/svg.image?\frac{n}{2}&space;*&space;\frac{n}{2}&space;&plus;&space;fastSum(\frac{n}{2})&space;" title="\frac{n}{2} * \frac{n}{2} + fastSum(\frac{n}{2}) " />로 나타낼 수 있습니다. 

<img src="https://latex.codecogs.com/svg.image?fastSum(n)&space;=&space;2*fastSum(\frac{n}{2})&plus;\frac{n^2}{4}" title="fastSum(n) = 2*fastSum(\frac{n}{2})+\frac{n^2}{4}" /> (n이 짝수 일때)

와 같이 나타낼 수 있다.

-  n이 홀수인 경우
    <br> : 홀수인 입력이 주어질 때는 짝수인 (n-1)까지의 합을 재귀 호출로 계산하고 n을 더해 답을 구한다.

```c++
//1 부터 n까지의 합을 구하는 분할 정복 알고리즘
int fastSum(int n){
    if(n==1)return 1;
    if(n%2==1) return fastSum(n-1)+n;
    return 2*fastSum(n/2)+(n/2)*(n/2);
}
```


### ⏰ 시간복잡도

두 함수는 모두 내부에 반복문이 없기 때문에 fastSum()과 recursiveSum()이 종료하는데 걸리는 시간은 함수가 호출되는 횟수에 비례하기 된다.<br>
recursiveSum의 경우 n번의 함수 호출이 필요하다.<br>
fastSum()의 호출 횟수는 호출이 될 때마다 (n/2)로 줄어들기 때문에 호출 횟수가 훨씬 적은것을 예상할 수 있다. <br>
예를 들어 fastSum(11)을 실행 할 때 재귀 호출의 입력이 어떻게 변화하는지를 아래의 그림으로 볼 수 있다. <br>

<p align="center">
<img src = "https://user-images.githubusercontent.com/76817418/149634275-ae7bffa4-3e5a-4b5f-86b2-4777f69ba2ca.png" width=300>
</p>

재귀 호출을 할 때 n의 이진수 표현의 마지막 자리가 1이면 0으로 바뀌고(홀수), 0이면 끝자리가 없어진다는 것을 알 수 있다. 따라서 fastSum()의 총 호출 횟수는 n의 이진수 표현의 자릿수 + 첫자리를 제외하고 나타나는 1의 개수이다. 
<br>두 값의 상한은 모두 (lgn) 이므로, 알고리즘의 실행시간은 O(lgn)이다. <br>

---

## 예시 : 행렬의 거듭제곱

n*n크기의 행렬 A가 주어질 때, A의 거듭제곱(power) A^m은 A를 연속해서 m번 곱한것이다. <br>
이를 계산하는 알고리즘 자체는 어려울 것이 없지만, 행렬의 곱셈에는 O(n^3)의 시간이 들기 떄문에 m이 매우 클 때 A^m 을 구하는 것은 오랜 시간이 걸리게 된다. 

##### O(n^3)은 가장 단순한 행렬의 곱셈 알고리즘을 사용한 경우의 시간복잡도이다.
<br>

분할 정복 알고리즘을 활용해 A^m을 구하는데 필요한 m개의 조각을 절반으로 나눠보자

<img src="https://latex.codecogs.com/svg.image?A^m&space;=&space;A^{m/2}&space;*&space;A^{m/2}" title="A^m = A^{m/2} * A^{m/2}" />

```c++
//행렬의 거듭제곱을 구하는 분할 정복 알고리즘

class SquareMatrix;
//n*n 크기의 항등행렬(identity matrix)을 반환하는 함수
SquareMatrix identity(int n);
//A^m을 반환한다. 
SquareMatrix pow(const SquareMatrix& A, int m){
    if(m==0) return identity(A.size());
    //홀수 A^m = A*A^m-1
    if(m%2>0) return pow(A,m-1)*A;
    SquareMatrix half = pow(A,m/2);
    //A^m = (A^(m/2))*(A^(m/2))    
    return half*half;
}
```

### 나누어 떨어지지 않을 때의 분할과 시간 복잡도

대부분 문제의 크기가 매번 절반에 가깝게 줄어들면 전체 분할의 횟수가 줄어들기 때문에 대부분의 분할 정복 알고리즘은 가능한 한 절반에 가깝게 문제를 나누고자 합니다. <br>
그러나 이 문제에서 절반에 가깝게 나누는 방식은  A^m 을 찾기 위해 계산해야할 재귀호출의 수가 늘어나게 된다. <br>

m이 홀수일 때, 예를 들어 A^7 = A* A^6 이나  A^7 = A^3 * A^4 의 방법으로 나눌 수 있다. <br>

두 가지 분할 방식을 이용해 pow(A,31) 을 계산할 때의 의존관계를 다음 그림에서 보여준다. 

<p align = "center">
<img src = "https://user-images.githubusercontent.com/76817418/149676586-abcacd11-91b3-4264-afb2-b5575e1e5d47.png" width=50%></p>

그림의 (a)와 (b)를 보게 되면 각 부분 문제가 계산되는 횟수가 다르다는 차이가 있다. 

(a)의 경우 절반으로 나눠서 계산하는 과정에서 중복으로 계산하는 일이 많기 때문에, m이 증가함에 따라 pow(A,m)을 계산하는데 필요한 pow()의 호출 횟수는 m에 대해 선형적으로 증가하기 때문에 시간복잡도는 O(m)이 된다. 

반면 (b)에서는 pow()가 O(lgm)개의 거듭제곱에 대해 한번씩만 호출된다. 

이것은 같은 문제라도 어떻게 분할하느냐에 따라 시간복잡도 차이가 커진다는 것을 보여주는 좋은 예이다.

절반으로 나누는 알고리즘이 큰 효율 저하를 불러오는 이유는 바로 여러 번 중복되어 계산되면서, 시간을 소모하는 부분 문제들이 있기 때문이다. 

##### -> 이런 속성을 부분문제가 중복된다(overlapping subproblem)고 부르며, 8장의 동적계획법이 고안된 계기가 된다.

---

## 예제 : 병합정렬과 퀵정렬

병합정렬(Merge Sort)과 퀵정렬(Quick Sort) 알고리즘은 모두 분할정복 패러다임을 기반으로 해서 만들어진 것들입니다. 

### 병합정렬 알고리즘(병합 과정에서 정렬)

    분할(devide) : 가운데에서 쪼개 비슷한 크기의 수열 두 개로 만든 뒤
    정복(conquer) : 이들을 재귀 호출을 이용해 각각 정렬한다.
    결합(combine) : 그 후 정렬된 배열을 하나로 합침으로써 정렬된 수열을 얻는다.
<br>

    병합정렬은 안정정렬에 속한다.<br>
    추가적인 메모리가 필요하다. <br>
    병합 정렬에서 실제로 정렬이 이루어지는 시점은 2개의 배열을 합병(merge)하는 단계이다.<br><br>

<p align=center><img src = "https://user-images.githubusercontent.com/76817418/149678208-ad4b70bb-0a8a-46d7-bde9-048ae0b323bd.png" width = 600>

병합 정렬의 수행 시간은 각 단계마다 반으로 나눈 부분 문제를 재귀 호출을 이용해 해결한 뒤, 이 수열을 합치면서 정렬하기 때문에 병합 과정에 의해 지배된다. O(n)
분할 과정에서는 재귀호출을 이용하여 문제의 크기는 항상 거의 절반으로 나누어 지기 때문에 O(lg n)이 된다.

총 시간복잡도는 O(nlgn)이다.

- 장점
    - 안정적인 정렬방법으로 입력 데이터가 무엇이든 간에 정렬되는 시간은 동일하다. O(n log{n})
    - 배열이 아닌 linked list로 구성하면, 링크 인덱스만 변경되므로 데이터의 이동량이 매우 작아진다. 
- 단점
    - 만약 데이터를 배열(Array)로 구성하면, 임시 배열이 필요하다.-> 제자리 정렬(in-place sorting)이 아니다.
    - 데이터의 크기가 큰 경우에는 배열내에서 이동 횟수가 많으므로 매우 큰 시간적 낭비를 초래한다.

<br><br>

### 퀵정렬 알고리즘(병합 전 정렬)
    분할(devide) : 입력 배열을 피벗을 기준으로 비균등하게 2개의 부분 배열(왼쪽: 피벗보다 작은 요소들, 오른쪽: 피벗보다 큰 요소들)로 분할한다.
    정복(conquer) : 부분 배열을 정렬한다. 부분 배열의 크기가 충분히 작아질 때까지 재귀호출을 이용하여 분할 정복 방법을 적용한다.
    결합(combine) : 정렬된 부분 배열들을 하나의 배열에 합병한다.

<br>
    임의의 기준 수(pivot)를 지정한 후 기준보다 작거나 같은 숫자를 왼쪽, 더 큰 숫자를 오른쪽으로 보내는 과정을 partition이라고 부른다.<br>
    퀵정렬은 불안정 정렬에 속하며, 다른 원소와의 비교만으로 정렬을 수행하는 비교정렬에 속한다.<br>
    피벗을 지정하는 방법에 따라 비효율절인 분할을 불러올 여지가 있지만, 각 부분 배열이 이미 정렬한 상태가 되어 별도의 병합 작업이 필요 없다는 장점이 있다.

<p align = center><img src = "https://user-images.githubusercontent.com/76817418/149794251-23b9b005-cf4d-4ae9-a266-73a49d0ef720.png" width=50%></p>

   - 장점  
     - 평균적으로 부분 문제가 절반에 가깝게 나눠질 때의 시간복잡도가 O(nlogn)으로 다른 정렬 알고리즘과 비교했을 때도 가장 빠르다. 
     - 추가 메모리 공간을 필요로 하지 않는다. O(log n) 만큼의 메모리를 필요로 한다.  

   - 단점
      - 정렬된 리스트에 대해서는 퀵 정렬의 불균형 분할에 의해 오히려 수행시간이 더 많이 걸린다. <br>
        worst case: O(n^2)

    퀵정렬의 불균형 분할을 방지하기 위하여 피벗을 선택할 때 리스트를 균등하게 분할 할 수 있는 데이터를 선택한다. 
    (ex)배열 내의 데이터 중에서 크기순으로 중간값을 피벗으로 선택한다.

---

## 예제 : 카라츠바의 빠른 곱셈 알고리즘
카라츠바의 빠른 곱셈 알고리즘은 수백, 수만자리나 되는 큰 두개의 정수를 곱하는 알고리즘이다.

두자릿 수 이상의 두 수를 곱하는 과정은 다음과 같다.

<p align=center><img src = "https://user-images.githubusercontent.com/76817418/149884773-53ed90e5-1ed3-4c67-aecf-82bf244e74e5.png" width=50%></p>

<p align=center><img src = "https://user-images.githubusercontent.com/76817418/149884812-49062feb-9af6-4b57-a9cf-d22d5b39e3f9.png" width=50%>


이를 코드로 구현하면 다음과 같다.


```c++
    //두 큰 수를 곱하는 O(n^2) 시간 알고리즘

    //num[]의 자릿수를 올림을 처리한다.
void normalize(vector<int>& num) 
{
	num.push_back(0);

	//자릿수 올림을 처리한다.
	int size = num.size();
	for (int i = 0; i < size - 1; i++)
	{
		if (num[i] < 0)
		{
			int borrow = (abs(num[i]) + 9) / 10;
			num[i + 1] -= borrow;
			num[i] += borrow * 10;
		}
		else
		{
			num[i + 1] += num[i] / 10;
			num[i] %= 10;
		}
	}
	//앞에 남은 0을 제거한다.
	while (num.size() > 1 && num.back() == 0)
		num.pop_back();
}
//초등학교식 정수 곱셈
vector<int> multiply(const vector<int>& a, const vector<int>& b)
{
	vector<int> c(a.size() + b.size() + 1, 0);

	int aSize = a.size();
	int bSize = b.size();

	for (int i = 0; i < aSize; i++)
		for (int j = 0; j < bSize; j++)
			c[i + j] += a[i] * b[j];
	normalize(c);
	return c;
}
```
이 알고리즘의 시간복잡도는 O(n^2)이다.

nomalize()에서는 자릿 수가 음수인 경우에도 처리하고 있는데 카라츠바 알고리즘의 구현을 돕기 위해 필요한 과정이다.

### 카라츠바 알고리즘
카라츠바 알고리즘은 두 수를 각각 절반으로 쪼갠다.

<img src="https://latex.codecogs.com/svg.image?a&space;=&space;a1&space;*&space;10^{128}&space;&plus;&space;a0" title="a = a1 * 10^{128} + a0" />

<img src="https://latex.codecogs.com/svg.image?b&space;=&space;b1&space;*&space;10^{128}&space;&plus;&space;b0" title="b = b1 * 10^{128} + b0" />

256자리의 두 정수를 곱하면 a1 과 b1 은 첫 128자리 a0 과 b0 은 각각 a,b의 뒷자리를 나타낸다.
이제 a*b의 계산과정은 다음과 같이 나눌 수 있다.

<p align=center><img src = "https://user-images.githubusercontent.com/76817418/149886775-c22d52fa-d34e-4fea-b240-b5b2b2060238.png" width=50%> 

이 방법에서 길이 n 인 두 정수를 곱하는데 드는 시간은 덧셈과 시프트 연산에 걸리는 시간 O(n)과 n/2 길이 조각들의 곱셈 네번으로 나눌 수 있다. 
따라서 이 방법의 전체 수행시간이 O(n^2)이 된다는 사실을 증명할 수 있다. 

<img src="https://latex.codecogs.com/svg.image?T(n)&space;=&space;O(n)&plus;4*T(n/2)" title="T(n) = O(n)+4*T(n/2)" />

이 곱셈 횟수를 줄이기 위해 다음 수식을 이용한다.

<p align=center><img src = "https://user-images.githubusercontent.com/76817418/149894015-c5b6cd32-5bb8-4a03-80b3-49db0ae61c1d.png" width=50%>

이 수식을 수정하면 다음의 결과를 얻을 수 있다. 

<img src="https://latex.codecogs.com/svg.image?z_2&space;=&space;a_1&space;*&space;b_1;" title="z_2 = a_1 * b_1;" />

<img src="https://latex.codecogs.com/svg.image?z_0&space;=&space;a_0&space;*&space;b_0;" title="z_0 = a_0 * b_0;" />

<img src="https://latex.codecogs.com/svg.image?z_1&space;=&space;(&space;a_0&space;&plus;&space;a_1&space;)&space;*&space;(&space;b_0&space;&plus;&space;b_1&space;)-&space;z_0&space;-&space;z_2;" title="z_1 = ( a_0 + a_1 ) * ( b_0 + b_1 )- z_0 - z_2;" />

이렇게 수정하고 나면 a*b는 곱셈 3번, 덧셈 2번, 뺄셈 2번으로 수행할 수 있다.
이를 재귀적으로 처리하여 a1*b1, a0*b0에 대해서도 적용하면 곱셈 결과를 얻을 수 있다.

다음은 카라츠바 알고리즘을 구현한 코드다
```c++

//a += b * (10^k)
void addTo(vector<int>& a, const vector<int>& b, int k)
{
	int originalASize = a.size();
	if (a.size() < b.size() + k)
		a.resize(b.size() + k);
	a.push_back(0);

	int aSize = a.size();
	for (int i = originalASize; i < aSize; i++)
		a[i] = 0;

	int bSize = b.size();

	for (int i = 0; i < bSize; i++)
		a[i + k] += b[i];

	normalize(a);
}

// a -= b
// a>= b인 경우에만 사용 가능하다.
void subFrom(vector<int>& a, const vector<int>& b)
{
	int bSize = b.size();

	for (int i = 0; i < bSize; i++)
		a[i] -= b[i];
	normalize(a);
}

//두 긴 정소의 곱을 반환한다.
vector<int> karatsuba(const vector<int>& a, const vector<int>& b)
{
	int an = a.size(), bn = b.size();

	//a가 b보다 짧을 경우 둘을 바꾼다.
	if (an < bn)
		return karatsuba(b, a);
	//기저 사례 : a나 b가 비어있는 경우
	if (an == 0 || bn == 0)
		return vector<int>();
	//기저 사례 : a가 비교적 짧은 경우, O(n^2) 곱셈으로 변경한다.(성능을 위함)
	if (an <= 50)
		return multiply(a, b);
	int half = an / 2;

	vector<int> a0(a.begin(), a.begin() + half);
	vector<int> a1(a.begin() + half, a.end());
	vector<int> b0(b.begin(), b.begin() + min<int>(b.size(), half));
	vector<int> b1(b.begin() + min<int>(b.size(), half), b.end());

	//z2 = a1 * b1
	vector<int> z2 = karatsuba(a1, b1);

	//z0 = a0 * b0
	vector<int> z0 = karatsuba(a0, b0);
	
	//z1 = ((a0 + a1) * (b0 + b1)) - z0 - z2
	addTo(a0, a1, 0);
	addTo(b0, b1, 0);
	vector<int> z1 = karatsuba(a0, b0);
	subFrom(z1, z0);
	subFrom(z1, z2);

	//병합 과정
	//ret = z0+z1*10^half + z2 * 10(half*2)
	vector<int> ret(z2.size() + half * 2, 0);
	addTo(ret, z0, 0);
	addTo(ret, z1, half);
	addTo(ret, z2, half * 2);
	return ret;
}
```

### 시간복잡도 
위의 구현 코드에서는 a의 길이가 50 이하이면 O(n^2) 곱셈 알고리즘을 이용하도록 했지만, 계산 편의를 위해서 시간복잡도 분석에서는 한 자리 숫자에 도달해야 O(n^2) 곱셈 알고리즘을 이용한다고 친다.

a,b의 자릿수 n이 2^k 이라고 할 때 재귀호출의 깊이는 k가 된다.

한번 쪼갤 때 마다 수행해야 할 곱셈이 3배(a_1*b_1, a_0*b_0, (a_0+a_1) * (b_0+b_1))로 늘어나기 때문에 마지막 단계에서는 3^k 개의 부분 문제가 있고, 마지막 단계에서는 두 수 모두 한자리 숫자니까 곱셈 한번이면 충분하다.
따라서 곱셈 횟수는 총 O(3^k)다.

여기서 n=2^k 라고 가정했으니 k=log(n)이고,

O(3^k) = O(3^{logn}) = O(n^{log3})
<img src="https://latex.codecogs.com/svg.image?O(3^k)&space;=&space;O(3^{logn})&space;=&space;O(n^{log3})" title="O(3^k) = O(3^{logn}) = O(n^{log3})" />

이다.

병합과정도 따져보자면, 병합과정은 더하기, 빼기만으로 구현된다. 더하기와 빼기는 O(n) 시간 안에 해결되고, 재귀 호출로 단계가 내려갈 때마다 숫자의 길이는 절반이 되고 문제의 개수는 3배가 되기 때문에, 깊이 i에서 병합에 필요한 연산 횟수는 

<img src="https://latex.codecogs.com/svg.image?(\frac{3}{2}^i)&space;*&space;n" title="(\frac{3}{2})^i * n" />  

이다.


따라서 병합을 위해 필요한 총 연산 수는



<p align=center><img src = "https://user-images.githubusercontent.com/76817418/149904984-8f1f208a-09f7-4bc7-a809-09ab7885bb24.png" width=200> 



이 함수는 n^log(3)과 같은 속도로 증가한다.

따라서, 최종 시간복잡도는 O(n^log3)이 된다.

단, 카라츠바 알고리즘의 구현은 입력의 크기가 작을 경우 O(n^2) 알고리즘보다 느린 경우가 많다는것에 유의한다.

---

# 7.2 문제 : 쿼드 트리 뒤집기(문제 ID : QUADTREE, 난이도 : 하)

<p align=center><img src = "https://user-images.githubusercontent.com/76817418/149977665-518f02f9-6849-4343-a402-6b6f4965f8cd.png" width=70%> 

대량의 좌표 데이터를 메모리 안에 압축해 저장하기 위해 사용하는 여러 기법 중 쿼드 트리(quad tree)란 것이 있습니다. 주어진 공간을 항상 4개로 분할해 재귀적으로 표현하기 때문에 쿼드 트리라는 이름이 붙었는데, 이의 유명한 사용처 중 하나는 검은 색과 흰 색밖에 없는 흑백 그림을 압축해 표현하는 것입니다. 쿼드 트리는 2N × 2N 크기의 흑백 그림을 다음과 같은 과정을 거쳐 문자열로 압축합니다.

- 이 그림의 모든 픽셀이 검은 색일 경우 이 그림의 쿼드 트리 압축 결과는 그림의 크기에 관계없이 b가 됩니다.
- 이 그림의 모든 픽셀이 흰 색일 경우 이 그림의 쿼드 트리 압축 결과는 그림의 크기에 관계없이 w가 됩니다.
- 모든 픽셀이 같은 색이 아니라면, 쿼드 트리는 이 그림을 가로 세로로 각각 2등분해 4개의 조각으로 쪼갠 뒤 각각을 쿼드 트리 압축합니다. 이때 전체 그림의 압축 결과는 x(왼쪽 위 부분의 압축 결과)(오른쪽 위 부분의 압축 결과)(왼쪽 아래 부분의 압축 결과)(오른쪽 아래 부분의 압축 결과)가 됩니다. 예를 들어 그림 (a)의 왼쪽 위 4분면은 xwwwb로 압축됩니다.
  
그림 (a)와 그림 (b)는 16×16 크기의 예제 그림을 쿼드 트리가 어떻게 분할해 압축하는지를 보여줍니다. 이때 전체 그림의 압축 결과는 xxwww bxwxw bbbww xxxww bbbww wwbb가 됩니다.

쿼드 트리로 압축된 흑백 그림이 주어졌을 때, 이 그림을 상하로 뒤집은 그림 을 쿼드 트리 압축해서 출력하는 프로그램을 작성하세요.

#### 입력
첫 줄에 테스트 케이스의 개수 C (C≤50)가 주어집니다. 그 후 C줄에 하나씩 쿼드 트리로 압축한 그림이 주어집니다. 모든 문자열의 길이는 1,000 이하이며, 원본 그림의 크기는 220 × 220 을 넘지 않습니다.

#### 출력
각 테스트 케이스당 한 줄에 주어진 그림을 상하로 뒤집은 결과를 쿼드 트리 압축해서 출력합니다.

<p align=center><img src = "https://user-images.githubusercontent.com/76817418/149991713-38555eeb-6773-4a98-9ebe-261d02802b69.png" > 

---

## 7.3 풀이 : 쿼드 트리 뒤집기
흑, 백으로 표시된 쿼드트리를 상하로 반전하여 출력해주는 문제입니다.

쿼드트리는 4분할로 나눠져있는 정사각형의 형태로 분할된 정사각형도 마찬가지로 4분할로 나뉠 수 있습니다.

따라서 분할된 각 부분을 재귀호출하여 계산하고 마지막에 결과를 합칠 수 있습니다.

쿼드트리는 문자열로 표현이 되고 검은 색은 b, 흰 색은 w로 표시합니다.

그리고 큰사각형의 4 픽셀이 모두 같은 색이 아닐 경우에는 x로 시작하고 내부 색깔에 따라 이어집니다.
<p align=center><img src = "https://user-images.githubusercontent.com/76817418/149983929-26228324-e4c3-4a27-a93e-1365a475049e.png" width=30%> 


사각형의 전체 색깔이 같으면 해당 문자를 반환합니다.

그렇지않으면 사각형을 4분할하여 각 분할된 사각형을 다시 재귀호출하여 분할된 사각형의 전체 색깔이 같은지를 다시 확인합니다.

이러한 방식으로 사각형의 전체 색깔이 같을 때 까지 4분할로 재귀호출하고 색깔이 같으면 문자를 반환하여 합쳐야 합니다.
<p align=center><img src = "https://user-images.githubusercontent.com/76817418/149984136-c368ba5e-7c23-4e12-8b27-dd7b310c009d.png"> 

---

### 쿼드트리 압축 후 문자열 분할하기
문자열에서 x를 만나면 decompress()는 문자열의 나머지 부분을 넷으로 쪼개 재귀 호출합니다.

이를 구현하기 위해 decompress()에서 x를 만나게 되면 한 글자를 가리키는 포인트를 전달하여 함수 내에서는 한 글자를 검사할 때마다 이 포인터를 앞으로 한 칸씩 옮겨서 검사한다.

STL의 문자열에서 지원하는 반복자(iterator)를 재귀 호출에 전달하게 되면 반복자가 참조형으로 전달되기 때문에 재귀 호출 내부에서 반복자를 옮기면 밖에도 그 변화가 전달된다. 따라서 decompress()함수가 종료하고 나면 반복자는 항상 다음 부분의 시작위치를 가리키게 된다.

#### 구현 코드

```c++
//쿼드 트리 압축을 해제하는 재귀 호출 알고리즘
char decompressed[MAX_SIZE][MAX_SIZE];
//s를 압축 해제해서 decompressed[][]구간에 쓴다.
void decompress(const string& s, int y, int x, int size){
    //한 글자를 검사할 때마다 반복자를 한 칸 앞으로 옮긴다.
    char head = *(it++);
    //기저 사례 : 첫 글자가 b또는 w인 경우
    if(head == 'b'|| head=='w'){
        for(int dy=0;dy<size;++dy)
            for(int dx=0;dx<size;++dx)
            decompressed[y+dx][x+dx] = head;
    }else{
        //네 부분을 각각 순서대로 압축 해제 한다.
        int half = size/2;
        decompress(it,y,x,half);
        decompress(it,y,x+half,half);
        decompress(it,y+half,x,half);
        decompress(it,y+half,x+half,half);
    }
}
```
---

### 압축 다 풀지않고 뒤집기

압축을 푸는 함수 decompress()를 작성했지만 문제에서 다루는 그림들의 크기가 너무 커지게 되면, 압축을 해제하고 뒤집는데 필요한 메모리가 감당할 수 없을 것이다. 

이런경우에 쓸 수 있는 방법으로 압축을 해제한 결과를 메모리에 다 저장하는 대신 결과 이미지를 뒤집은 결과를 곧장 생성하는 코드를 작성한다.

문자열을 읽어 전체가 흰색이나 검은색은 뒤집어도 같다. 그래서 'b'나 'w'를 만나면 그대로 해당 색을 반환해주면 된다. 

'x'를 만나는 경우 재귀를 이용하여 상하반전을 실시한다.

<p align=center><img src = "https://user-images.githubusercontent.com/76817418/149990733-307f8e2d-3e8d-436f-bf51-0080255fba8c.png" width = 50%> 


```c++
//쿼드 트리 뒤집기 문제를 해결하는 분할 정복 알고리즘

//문자열의 반복자를 인자로 사용
string reverse(string::iterator& it){
    char head = *it;
    ++it;
    //문자열의 첫 문자가 b나 w인 경우(사각형의 색이 같은 경우) 문자 그대로 반환
    if(head == 'b' || head == 'w')
        return string(1, head);
    //인자로 받은 문자열의 첫 번째 문자를 제외하고 나머지 문자열을 통해 재귀 호출
    string upperLeft = reverse(it);
    string upperRight = reverse(it);
    string lowerLeft = reverse(it);
    string lowerRight = reverse(it);

    //위, 아래 조각들을 반대로 교체한다.
    return string("x") + lowerLeft + lowerRight + upperLeft + upperRight;
}
```
reverse() 함수는 한 번 호출될 때마다 주어진 문자열의 한 글자씩을 사용하기 때문에 문자열의 길이와 비례하므로 시간복잡도는 O(n)이 된다.

---

## 7.4 문제 : 울타리 잘라내기(문제 ID : FENCE, 난이도 : 중)

문제
너비가 같은 N개의 나무 판자를 붙여 세운 울타리가 있습니다. 시간이 지남에 따라 판자들이 부러지거나 망가져 높이가 다 달라진 관계로 울타리를 통째로 교체하기로 했습니다. 이 때 버리는 울타리의 일부를 직사각형으로 잘라내 재활용하고 싶습니다. 그림 (b)는 (a)의 울타리에서 잘라낼 수 있는 많은 직사각형 중 가장 넓은 직사각형을 보여줍니다. 울타리를 구성하는 각 판자의 높이가 주어질 때, 잘라낼 수 있는 직사각형의 최대 크기를 계산하는 프로그램을 작성하세요. 단 (c)처럼 직사각형을 비스듬히 잘라낼 수는 없습니다.

판자의 너비는 모두 1이라고 가정합니다.

<p align=center><img src = "https://user-images.githubusercontent.com/76817418/149991285-ebd6865b-a625-4ad1-a9ee-45d16f604d14.png" width = 60%> 

#### 입력
첫 줄에 테스트 케이스의 개수 C (C≤50)가 주어집니다. 각 테스트 케이스의 첫 줄에는 판자의 수 N (1≤N≤20000)이 주어집니다. 그 다음 줄에는 N개의 정수로 왼쪽부터 각 판자의 높이가 순서대로 주어집니다. 높이는 모두 10,000 이하의 음이 아닌 정수입니다.

#### 출력
각 테스트 케이스당 정수 하나를 한 줄에 출력합니다. 이 정수는 주어진 울타리에서 잘라낼 수 있는 최대 직사각형의 크기를 나타내야 합니다.

<p align=center><img src = "https://user-images.githubusercontent.com/76817418/149991537-e61c44e0-b9fe-4293-82bf-1b122a657b93.png" > 

---

### 무식하게 풀 수 있을까?

```c++
//울타리 잘라내기 문제를 해결하는 O(n^2)알고리즘

//판자의 높이를 담은 배열 h[]가 주어질 때 사각형의 최대 너비를 반환한다.
int bruteForce(const vector<int> h){
    int ret = 0;
    int N = h.size();
    //가능한 모든 left,right 조합을 순회한다.
    for(int left=0;left<N;++left){
        int minHeight = h[left];
        for(int right=left;right<N;++right){
            minHeight = min(minHeight,h[right]);
            ret = max(ret,(right-left+1)*minHeight);
        }
    }
    return ret;
}
```

---

### 분할정복 알고리즘의 설계


```c++

vector<int> h;

int solve(int left, int right)
{
	//기저사례 : 판자가 하나밖에 없는 경우
	if (left == right)
		return h[left];
    //[left,mid][mid+1,right]의 두 구간으로 문제를 분할한다.
	int mid = (left + right) / 2;
	int ret = max(solve(left, mid), solve(mid + 1, right));
    //left와 right에 모두 걸치는 사각형 중 가장 큰 것을 찾는다.
	int lo = mid, hi = mid + 1;
	int height = min(h[lo], h[hi]);
    //[mid,mid+1]만 포함하는 너비 2인 사각형을 고려한다.
	ret = max(ret, height * 2);

    //사각형이 입력 전체를 덮을 때까지 확장해 나간다.
	while (left < lo || hi < right)
	{   
        //항상 높이가 더 높은 쪽으로 확장한다.
		if (hi < right && (lo <= left || h[lo - 1] < h[hi + 1]))
		{
			hi++;
			height = min(height, h[hi]);
		}
		else
		{
			lo--;
			height = min(height, h[lo]);
		}
        //확장한 후 사각형의 넓이
		ret = max(ret, height * (hi - lo + 1));
	}
	return ret이
}
```

#### 시간복잡도
문제를 항상 절반으로 나누어서 재귀 호출하고, O(n)시간의 후처리를 하는 알고리즘인 병합정렬과 같은 O(nlgn)이다.

---

## 7.6 문제 : 팬미팅(문제 ID : FANMEETING, 난이도 : 상)

가장 멤버가 많은 아이돌 그룹으로 기네스 북에 올라 있는 혼성 팝 그룹 하이퍼시니어가 데뷔 10주년 기념 팬 미팅을 개최했습니다. 팬 미팅의 한 순서로, 멤버들과 참가한 팬들이 포옹을 하는 행사를 갖기로 했습니다. 하이퍼시니어의 멤버들은 우선 무대에 일렬로 섭니다. 팬 미팅에 참가한 M명의 팬들은 줄을 서서 맨 오른쪽 멤버에서부터 시작해 한 명씩 왼쪽으로 움직이며 멤버들과 하나씩 포옹을 합니다. 모든 팬들은 동시에 한 명씩 움직입니다. 아래 그림은 행사 과정의 일부를 보여줍니다. a~d는 네 명의 하이퍼시니어 멤버들이고, 0~5는 여섯 명의 팬들입니다.

<p align=center><img src = "https://user-images.githubusercontent.com/76817418/150071778-230cf882-457b-4de6-be5d-f4c6446c6179.png" width = 60%> 


하지만 하이퍼시니어의 남성 멤버들이 남성 팬과 포옹하기가 민망하다고 여겨서, 남성 팬과는 포옹 대신 악수를 하기로 했습니다. 줄을 선 멤버들과 팬들의 성별이 각각 주어질 때 팬 미팅이 진행되는 과정에서 하이퍼시니어의 모든 멤버가 동시에 포옹을 하는 일이 몇 번이나 있는지 계산하는 프로그램을 작성하세요.

입력
첫 줄에 테스트 케이스의 개수 C (C≤20)가 주어집니다. 각 테스트 케이스는 멤버들의 성별과 팬들의 성별을 각각 나타내는 두 줄의 문자열로 구성되어 있습니다. 각 문자열은 왼쪽부터 오른쪽 순서대로 각 사람들의 성별을 나타냅니다.

M은 해당하는 사람이 남자, F는 해당하는 사람이 여자임을 나타냅니다. 멤버의 수와 팬의 수는 모두 1 이상 200,000 이하의 정수이며, 멤버의 수는 항상 팬의 수 이하입니다.

출력
각 테스트 케이스마다 한 줄에 모든 멤버들이 포옹을 하는 일이 몇 번이나 있는지 출력합니다.

<p align=center><img src = "https://user-images.githubusercontent.com/76817418/150071872-58649047-d689-429e-b7d1-85eb8546968f.png" > 

---

### 문제 해결

```c++
//카라츠바의 빠른 곱셈을 이용해 팬미팅 문제를 해결하는 함수
int hugs(const string& memvers, const string& fans){
    int N = members.size(), M = fans.size();
    vector<int> A(N), B(M);
    for(int i=0;i<N;++i) A[i] = (members[i]=='M');
    for(int i=0;i<M;++i) B[M-i-1] = (fans[i]=='M');
    //karatsuba 알고리즘에서 자리올림은 생략한다.
    vector<int> C = karatsuba(A,B);
    int allHugs=0;
    for(int i=N-1;i<M;i++)
        if(C[i]==0) ++allHugs;

    return allHugs;
}
```
카라츠바 알고리즘을 이용하여 이 문제를 해결하면 쉽다.
이 알고리즘의 수행 시간은 두 수의 곱셈에 좌우 되므로 O(n^(lg3))이 된다.