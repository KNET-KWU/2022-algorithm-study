# 탐욕법(greedy method)

재귀 호출과 똑같이 여러개의 조각으로 쪼개고, 각 단계마다 답의 한 부분을 만들어간다는 점에서 완전탐색과 다를 것이 없다고 생각 했지만,<br>

이 둘의 차이점이 인상깊었습니다.

- [ ] *완전탐색* 은 모든 선택지를 고려해보고, 그 중 전체 답이 가장 좋은 것을 찾는 방식 

- [ ] *탐욕법* 은 각 단계마다 지금 가장 좋은 방법만 선택하는 방식

따라서, 탐욕법을 사용해도 항상 최적해를 구할 수 있는 문제라면, 탐욕법을 쓰는 것이 수행시간이 훨씬 빠르다 라는 것을 알게 되었습니다.

## 예제 : 회의실 예약

문제를 이해하는 데 오래걸리고, 너무 헷갈렸다.<br>
일단, p.368에 있는 
1. 목록 S에 남은 회의 중 가장 일찍 끝나는 회의 Smin을 선택한다.
2. Smin과 겹치는 회의를 S에서 모두 지운다.
3. S가 텅 빌 때까지 반복한다.

이 방식을 보고, LIS가 생각 났습니다. 비슷한 방식인 것 같다.<br><br>

![](KakaoTalk_20220209_170349335.png)
책에 나와있는 그림을 보면, 한 줄이 한 팀인 것 같은데, 책의 코드를 보면 책의 그림에서 나와있는 마지막 줄은 3개로 분할 저장하는데, 그럼 띄어져있는 기준이 한 팀인지 헷갈린다.<br>
order.push_back(make_pair(end[i], begin[i])); // 이런 코드 대로라면, 마지막 줄은 3개로 분할 저장이 되는게 아닌가 라는 생각이 듭니다. 만약 그 것이 맞다면, 한 줄전체가 한 팀이 아니라, 3개의 팀으로 분할 되는 건데, 그럼 한 줄로 표현한 이유가 없는데...

```
int n;
int begin[100], end[100];
int schedule(){
    // 일찍 끝나는 순서대로 정렬한다.
    vector<pair<int, int> > order;
    for(int i = 0 ; i < n ; ++i)
        order.push_back(make_pair(end[i], begin[i]));   
    sort(order.begin(), order.end());       // pair의 첫 번째 인자 기준으로 오름차순 정렬, 동일하다면 2번째 인자 기준으로 정렬
    // earliest : 다음 회의가 시작할 수 있는 가장 빠른 시간
    // selected : 지금까지 선택한 회의의 수
    int earliest = 0, selected = 0;
    for(int i = 0 ; i < order.size() ; ++i){
        int meetingBegin = order[i].second, meetingEnd = order[i].first;
        // second가 end부분이므로, 끝난 이후에 제일 빠른 시작점부터 다시 탐색
        if(earliest <= meetingBegin){
            // earliest를 마지막 회의가 끝난 시간 이후로 갱신한다.
            earliest = meetingBegin;
            ++selected;
        }
    }
    return selected;
}
```
<br>

## 예제 : 출전 선수 정하기

처음 정해진 출전 순서는 책 p.371에 나와있음<br><br>
제가 처음에 생각한 방식은<br><br>
우리팀 선수를 레이팅 순으로 오름차순으로 정렬한 뒤, 둘 중 하나라도 끝에 도달 시, 종료
1. 첫번째부터 비교해서 우리가 더 낮으면 우리팀 index만 ++하기(그냥 skip)
2. 같거나, 큰 경우라면, 둘 다 index++ && count++<br>

![kakao](KakaoTalk_20220209_001141025.jpg)<br><br>

+ 내 코드
```
// 두 배열을 모두 오름차순으로 정렬 후, 첫번째 index부터 비교를 해서 korean이 더 작으면, 그냥 skip하고
// 같거나 클 시에는, count
int chooseorder(vector<int>& russian, vector<int>& korean){
    sort(russian.begin(), russian.end());
    sort(korean.begin(), korean.end());
    int count = 0, rus = 0, kor = 0;
    while(rus != russian.size() && kor != korean.size()){
        if(russian[rus] > korean[kor])
            kor++;
        else{
            rus++;
            kor++;
            count++;
        }
    }
    return count;
}
```

처음에 구상한 아이디어가 책에 나와있는 구상과 유사<br><br>
한가지 놓친 부분은, 상대선수가 한국팀의 모든 선수보다 레이팅이 높다면, <br>
남은 선수 중 가장 레이팅이 낮은 선수와 경기시킨다.<br><br>

+ 책의 코드
```
int order(vector<int>& russian, vector<int>& korean){
    int n = russian.size(), wins = 0;
    // 아직 남아 있는 선수들의 레이팅
    multiset<int> ratings(korean.begin(), korean.end());
    for(int rus = 0; rus < n ; ++rus){
        // 가장 레이팅이 높은 한국 선수가 이길 수 없는 경우
        // 가장 레이팅이 낮은 선수와 경기시킨다.
        if(*ratings.rbegin() < russian[rus])
            ratings.erase(ratings.begin());
        // 이 외의 경우 이길 수 있는 선수 중 가장 레이팅이 낮은 선수와 경기시킨다.
        else{
            ratings.erase(ratings.lower_bound(russian[rus]));
            ++wins;
        }
    }
    return wins;
}
```

책의 코드에서는 multiset을 쓰는 게 인상적이었다.
+ multiset의 특징<br>
-> 대입 시에, 오름차순으로 자동 정렬하고, 중복을 허용한다<br>
이로 인해 찾고, 삭제하는 작업을 모두 O(log n)에 수행 가능<br>
전체 시간 복잡도는 O(n log n)이 된다.

+ multiset의 내장함수<br>
lower_bound의 사용<br>
![lower_bound](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile5.uf.tistory.com%2Fimage%2F237CB933597967992FCCE5)

+ algorithm 라이브러리의 sort 함수 사용법( 기본적으로 오름차순 정렬)<br> 
// vector 사용시 -> sort(v.begin(), v.end());<br>
// 배열 사용시(배열의 이름은 포인터 이므로) -> sort(arr, arr + 10);<br>
// 다른 기준으로 정렬하고 싶을 시 -> sort(v.begin(), v.end(), cmp);<br>
3번째 인자로 정렬 기준을 넣어주고, 그 함수는 반환 타입이 bool형 이어야 한다.<br>
(ex) bool cmp(int a, int b) { return a > b; } -> 내림차순 정렬<br>
그리고, 기준 함수를 정의 할때, 두 매개변수의 타입은 정렬할 데이터 타입과 일치해야 한다.<br>
이런 것은 처음 알았는데, Java의 comparable -> compareTo와 동일한 기능이 있는 것이 신기했다.

## 10.2 문제 : 도시락 데우기

각 사람은 자기 도시락을 다 데우는 대로 다른 사람을 기다리지 않고 먹는다<br>
처음 든 생각 : 다 데우긴 해야하니, 데우는 시간은 상관쓰지말고, 먹는 시간 기준으로 내림차순 정렬

데우는 시간 : 2 1 3<br>
먹는 시간 : 2 1 1<br>
이런 것은 데우는 시간 6초(2+1+3) + 마지막 먹는 시간 1초 -> 7초<br><br>

이런 예제는 불가<br>
데우는 시간 : 2 1 3<br>
먹는 시간 : 8 1 1<br>
데우는 시간 6초(2+1+3) + 마지막 먹는 시간 1초 -> 7초 가 아닌, 10초가 된다.<br><br>
그래서, 책의 점화식을 활용하면<br>
2 1<br>
8 1<br>
총 데우는 시간 + 마지막 데운 음식을 먹는 시간의 max값을 구하면 된다.<br>
첫 번째 음식 : 2+8=10<br>
두 번째 음식 : (2+1)+1=4<br>
max값은 10이므로, 답은 10이 된다.

* 위에서 공부했던 sort를 사용해서 풀어본 코드

```
// 비교함수를 짤 때, 비교하는 대상과 매개변수의 타입이 일치해야 하는 것 주의
bool cmp(pair<int,int> a, pair<int,int> b){
    return a.first > b.first;   // 한 쌍중에 앞의 원소를 기준으로 내림차순 정렬
}

int heat2(){
    vector<pair<int,int> > order;
    for(int i = 0 ; i < n ; ++i)
        order.push_back(make_pair(e[i], m[i]));
    sort(order.begin(), order.end(), cmp);
    int ret = 0, beginEat = 0;
    for(int i = 0 ; i < n ; ++i){
        beginEat += order[i].second;
        ret = max(ret, beginEat + order[i].first);
    }
    return ret;
}
```

+ 책의 코드

```
int heat(){
    // 어느 순서로 데워야 할지를 정한다.
    vector<pair<int,int> > order;
    for(int i = 0 ; i < n ; ++i)
        order.push_back(make_pair(-e[i], i));        // 부호를 뒤집어 넣으므로, 역순 정렬을 간단히 함.
    sort(order.begin(), order.end());
    // 해당 순서대로 데워먹는 과정을 시뮬레이션한다.
    int ret = 0, beginEat = 0;
    for(int i = 0 ; i < n ; ++i){
        int box = order[i].second;
        beginEat += m[box];
        ret = max(ret, beginEat + e[box]);
    }
    return ret;
}
```

전체 시간 복잡도는 O(n log n)

## 10.4 문제 : 문자열 합치기

두 문자열을 합치는 비용 -> 두 문자열의 길이의 합

처음 접근 방식 : 오름차순으로 정렬해놓고, 앞에서 부터 다 더한다고 생각<br>

이렇게 접근하면, 1 1 3 3 4 에서 <br>
1+1=2, 2+3=5, 5+3=8, 8+4=12  의 총합인 27이 나오게 되는데, <br>

그게 아닌, 매 순간 제일 작은 값 2개를 더하는 방식으로 풀어야 한다.<br>
1 1 3 3 4 이면, <br>
2 3 3 4 -> 3 4 5(2+3) -> 5 7(3+4) -> 12 <br>
그런 방식으로 하면, 26으로 나온다.

이 방식을 사용해서 풀 때, 중복을 허용하고, 오름차순으로 정렬을 해야하니 <br>
multiset을 이용하여 거의 다 구현했을 때 쯤, multiset의 첫번째 인자를 받아오는 기능이 없는 것 같아서 불가능 하다 생각했습니다.<br>
내장함수로 begin()함수가 있지만, iterator를 리턴해주어 값을 출력을 할 수 있지만, int형 변수에 저장은 못하기 때문에, multiset을 이용한 방식은 구현하지 못하였다.

```
// 문자열들의 길이가 주어질 때 하나로 합치는 최소 비용을 반환한다.
int concat(const vector<int>& lengths){
    // 최소 큐를 선언한다.
    priority_queue<int, vector<int>, greater<int> > pq;
    for(int i = 0 ; i < lengths.size() ; ++i)
        pq.push(lengths[i]);
    int ret = 0;
    // 원소가 하나 이상 남은 동안 반복한다.
    while(pq.size() > 1){
        // 가장 짧은 문자열 두 개를 찾아서 합치고 큐에 넣는다.
        int min1 = pq.top(); pq.pop();
        int min2 = pq.top(); pq.pop();
        pq.push(min1 + min2);
        ret += min1 + min2;
    }
    return ret;
}
```

우선순위 큐는 집합에서 가장 작은 원소를 찾거나, 새 원소를 추가하는 작업을 모두 O(log n)에 할 수 있도록 해준다.<br>
concat함수 내의 while문은 n-1번 실행되고, 그 안에서 실행되는 함수 호출에는 최대 O(log n)의 시간이 걸리기 때문에<br>
알고리즘의 전체 수행 시간은 O(n log n)이다.
