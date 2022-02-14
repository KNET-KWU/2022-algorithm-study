# 최적화 문제 결정 문제로 바꿔 풀기

> 최적화 문제를 결정문제로 바꾼 뒤, 이것을 이분법을 이용해 해결하는 방법

* 최적화 문제의 반환 값은 대개 실수나 정수이므로 답의 경우의 수가 무한한 데 반해,
* 결정 문제는 두 가지 답만이 있을 수 있다.

다음은 여행하는 외판원 문제(TSP)를 최적화 문제와 결정 문제로 표현한 예시이다.

* optimize(G)=그래프 G의 모든 정점을 한 번씩 방문하고 시작점으로 돌아오는 *최단 경로의 길이*를 반환한다.(최적화 문제)
* decision(G,x)= 그래프 G의 모든 정점을 한 번씩 방문하고 시작점으로 돌아오면서 길이가 *x 이하인 경로의 존재 여부*를 반환한다.(결정 문제)

> optimize()는 가장 짧은 경로의 길이를 실수로 반환, decision()은 x보다 짧은 경로가 있는지 여부를 반환


# 최적화 문제와 결정 문제의 관계

> 같은 문제를 최적화문제 형태와 결정 문제 형태로 만들었을 때 비교

1. 두 문제 형태가 똑같이 어려운 경우
2. 최적화 문제가 더 어려운 경우

> 결정문제 난이도 <= 최적화문제 난이도





# 예제: DARPA Grand Challenge

> 문제 좌표에 카메라를 설치할 건데 n개의 카메라를 m개의 위치에 설치 하려고 한다.
> 이 중 n군데의 카메라를 설치하여 **가장 가까운 두 카메라 사이의 간격을 최대화** 하려고 함.

![KakaoTalk_20220214_161217039](https://user-images.githubusercontent.com/85841791/153819930-9b603632-02a7-41b4-acf6-f9eebf1e2d00.jpg)


optimize(locations,cameras)= 카메라를 설치할 수 있는 위치 locations과 카메라의 수 cameras가 주어질 때, 카메라 간 최소 간격의 최대치를 반환.

결정문제로 바꾸면

decision(locations, cameras, gap)=카메라를 설치할 수 있는 위치 locations와 카메라의 수 cameras가 주어질 때, 이들을 적절히 배치해 모든 카메라의 간격이 **gap 이상**이 되도록 하는 방법
이 있는지 판단
```cpp
bool decision(const vector<double>& location, int cameras, double gap) {
	double limit = -1;
	int installed = 0;
	for (int i = 0; i < location.size(); i++) {
		if (limit <= location[i])
			++installed;
		limit = location[i] + gap;
	}
	return installed >= cameras;
}

double optimize(const vector<double>& location, int cameras) {
	double lo = 0, hi = 241;
	for (int it = 0; it < 100; it++) {
		double mid = (lo + hi) / 2.0;
		if (decision(location, cameras, mid))
			lo = mid;
		else
			hi = mid;
	}
	return lo;
}
```


> ** location이 정렬되어 있어야만 448p 의 그림 12.1 (a)와 같은 형태가 성립한다**


> 카메라 설치할지 말지 선택하는 decision은 탐욕법이다( 예를 들어 0km 위치에 카메라를 설치하지 않는 답이 존재한다고 가정하면
> 그 답에서 가장 왼쪽에 있는 카메라를 0km 지점으로 옮겨도 똑같이 답이 되니 0km를 항상 선택해도 된다.

* 가장 왼쪽에 있는 지점부터 gap이상을 유지하며 카메라를 채워 나가도 된다.



# 이분법의 함정
이 문제의 답은 항상 입력으로 주어진 두 장소 간의 거리이기 때문에, 존재할 수 있는 답의 후보들은 최대 (n-1)n/2 가지 이다.

만약  (n-1)n/2 가지를 답의 후보를 두고 이 목록에서 이분탐색을 시도하면 오차가 커질 수 있다.

탐색 범위를 좀더 넓게? 유연하게 두고 해야된다.

이 문제에서는 0~240km를 답의 후보로 두고 탐색을 해야 한다는 뜻

# 최적화 문제 결정 문제로 바꾸기 레시피

1. "가장 좋은 답은 무엇인가?"라는 최적화 문제를 "x 혹은 그보다 좋은 답이 있는가?"라는 결정 문제 형태로 바꾼다.
2. 결정 문제를 쉽게 풀 수 있는 방법이 있는지 찾아본다.
3. 결정 문제를 내부적으로 이용하는 이분법 알고리즘을 작성한다.

# 12.2 문제:남극 기지

> 기지들의 좌표가 주어진다.
> 무전기의 통신 반경은 d, 두 탐사 기지는 사이의 거리가 d 이하여야만 서로 연락을 할 수 있다.
> 각 기지 간에는 다른 기지를 통해 간접적으로 연락할 수도 있다. 
> 항상 모든 기지 간에 서로 연락을 할 수 있게끔 하는 최소의 무전기 통신반경 d를 구하여라


*  optimize(P)=P에 주어진 기지들을 모두 연결하는 연락망을 구축할 때 가능한 최소 무전기 반경은 얼마인가?(최적화 문제)
*  결정 문제로 바꾸면
*  decision(P,d)= 모든 기지를 하나로 연결하되, 가장 먼 두 기지 간의 거리가 d이하인 연락망이 있는가?


풀이: 서로간의 거리가 d 이하인 기지들을  서로를 연결하는 간선이 있다고 가정하고 그래프를 만든 뒤
탐색 알고리즘을 이용해 모든 기지들을 방문하는지 체크한다 (여기까지 decision) 

전부 방문 되면 true 를 반환(optimize에서 d의 값을 줄여준다.) 
전부 방문을 못했으면 false를 반환(optimize에서 d의 값을 늘려준다.)

> 너비 우선 탐색을 이용한다. if문 내의 dist[here][there]<=d 가 성립할 때 탐색을 이어나감에 유의한다

```cpp
int n;
double dist[101][101];
bool decision(double d) {
	vector<bool> visited(n, false);
	visited[0] = true;
	queue<int> q;
	q.push(0);
	int seen = 0;
	while (!q.empty()) {
		int here = q.front();
		q.pop();
		++seen;
		for(int there =0; there <n ;++there)
			if (!visited[there] && dist[here][there] <= d) {
				visited[there] = true;
				q.push(there);
		}
	}
	return seen == n;
}

double optimize() {
	double lo = -0, hi = 1416.00;
	for (int it = 0; it < 100; it++) {
		double mid = (lo + hi) / 2;
		if (decision(mid))
			hi = mid;
		else
			lo = mid;
	}
	return hi;
}
```





# 12.4 문제 : 캐나다 여행

최적화 문제는 아니지만 문제를 푸는 방식은 원래 문제를 결정 문제로 바꾸는 데 있다.

> K번째 표지판의 위치는 어디인가라는 문제를 다음과 같이 바꿔봅니다.

* decision(x)=시작점부터 x미터 지점까지 가면서 K개 이상의 표지판을 만날 수 있는가?
* decision(D-1)=false, decision(D)=true 여야 한다는 뜻이다
* n은 표지판의 개수, k는 k번째 표지판, l은 시작점으로부터 각 도시까지의 거리
* m은 m미터 지점부터 표지판이 등장함, g는 도시에 도착할 떄 까지 g 미터 간격으로 표지판이 설치되어 있다는 뜻


x미터 까지 만난 표지판의 개수를 구하는게 관건이다.
일단 각 도시별로 표지판의 개수를 구한다. i번 도시까지의 표지판은 L[i]-M[i]~M[i] 사이에 있다.
0~x 까지의 구간과 겹치는 구간의 길이는
min(x,L[i])-(L[i]-M[i]) 일 것이다. 이 길이에  G[i]를 나누고 1을 더하면 
i번 도시의 x미터 까지 중표지판의 개수일 것이다.

```cpp
int n, k;
int l[5000], m[5000], g[5000];
bool decision(int dist) {
	int ret = 0;
	// 도시의 개수 만큼 for문으로 dist까지 몇개의 표지판을 만나는지 
	for (int i = 0; i < n; i++)
  // 표지판을 볼 수 있는 dist 라면
		if (dist >= l[i] - m[i])
		// 몇개의 표지판을 볼 수 있는지 계산한다.
			ret += (min(dist, l[i]) - (l[i] - m[i])) / g[i] + 1;
      //t or f 리턴
	return ret >= k;
}

int optimize1() {
	int lo = -1, hi = 8030001;
	while (lo + 1 < hi) {
		int mid = (lo + hi) / 2;
		if (decision(mid))
			hi = mid;
		else
			lo = mid;
	}
	return hi;
}
```
* optimize에서 if(decision(mid))가 참이라면 상한을 줄여주고
* false 라면 하한을 늘려준다


# 문제 12.6 : 수강 철회

이번 학기에 욕심을 부려 학점 초과신청을 한 백준이는 중간고사 성적을 보고 한숨을 토할 수밖에 없었습니다. 다음 학기 장학금을 받을 만큼 성적이 잘 나오지 않았기 때문입니다. 이제 백준이에게 남은 희망은 다음 주의 수강 철회 기간 뿐입니다.

백준이네 학교에서는 장학금을 학생의 중간고사 등수와 기말고사 등수에 따라 배정합니다. 어떤 학생이 듣는 **i번째 과목의 수강생 수가 ci**라고 합시다. 그리고 이 학생의 **i번째 과목 중간 고사 등수가 ri**라고 하면, 이 학생의 중간 고사 누적 등수 **cumulativeRank** 는 다음과 같이 정의됩니다.

cumulativeRank = sum(ri) / sum(ci)

예를 들어 백준이가 수강생이 각각 150, 200, 15명인 3개의 과목을 듣는데, 각각 100, 10, 5등을 했다면 백준이의 누적 등수를 다음과 같이 계산할 수 있지요.

(100 + 10 + 5) / (150 + 200 + 15) = 115 / 365 = 0.315..

수강 철회를 하면 철회한 과목은 중간 고사의 누적 등수 계산에 들어가지 않게 됩니다. 다행히 백준이네 학교에서는 수강 철회를 해도 남은 과목이 **k 개 이상**이라면 장학금을 받을 수 있습니다. 백준이가 적절히 과목을 철회했을 때 얻을 수 있는 **최소 누적 등수**를 계산하는 프로그램을 작성하세요.

> decision(x) = 적절히 과목들을 철회해 누적 등수 x 이하가 될 수 있을까?
> ***sum(ri)/sum(ci)<=x***

식을 조작해보면 0<=x*sum(ci)-sum(ri) 
x*sum(ci)-sum(ri)  이부분을 배열 v라고 하자
그러면 **decision(x)는 배열 v가 주어질 때, 이중 k개 이상을 선택해서 그 합을 0 이상으로 만들 수 있는가 하는 문제**로 변환된다.

v를 오름차순으로 정렬한 후 뒤에서 k개의 원소를 더해준다.
그 값이 0 보다 크거나 같다면 true 반환 --> 누적등수를 더 작게 해준다.
그 값이 0 보다 작다면 false 반환 --> 누적등수를 더 크게 .

```cpp
bool decision(double x)
{
	vector<double> v;
	for (int i = 0; i < N; i++)
		v.push_back(x * C[i] - R[i]);
	// 오름차순 정렬 후에
	sort(v.begin(), v.end());
	double sum = 0;
	// 뒤에서 K개 만큼의 숫자를 더해준다.
	for (int i = N - K; i < N; i++)
		sum += v[i];
	
	return sum >= 0;
}

double optimize()
{
	double lo = -1e-9, hi = 1;
	for (int i = 0; i < 100; i++) {
		double mid = (lo + hi) / 2;
		
		if (decision(mid)) 
			hi = mid;
		else
			lo = mid;
	}
	return hi;
}
```
