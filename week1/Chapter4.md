알고리즘의 시간 복잡도 분석
---

 알고리즘의 시간복잡도는 일반적으로 반복문에 의해서 판단됩니다. <br>
 수행시간에 관해서는 여러 다양한 요인들이 있지만 그것은 사용하는 언어, 개발 환경 ...등 너무나도 변칙적이기 때문에 우리는 반복문이 수행되는 횟수를 통해서 시간예측합니다.<br>
 한가지 예를 보도록 하겠습니다.

 
```
public class FindMajorityNumber {
    private int majority = -1;
    private int majorityCount = 0;

    int majority1(final int[] arr){
        //모든 경우에 대해서 빈도수를 확인하여 반환 시간복잡도 n^2소요
        for (int k : arr) {
            int count = 0;
            for (int i : arr)
                if (i == k) count++;

            if (count > majorityCount) {
                majority = k;
                majorityCount = count;
            }
        }
        return majority;
    }

    //숫자가 0~100 사이의 숫자라는 것을 알고 있을 때
    int majority2(final int[] arr){
        int[] binDo = new int[101];
        Arrays.fill(binDo, 0);
        majority = -1;
        majorityCount = -1;

        for(int num : arr){
            binDo[num]++;
        }

        for(int i = 0; i<binDo.length; i++){
            if(binDo[i] > majorityCount){
                majority = i;
                majorityCount = binDo[i];
            }
        }
        return majority;
    }

    public static void main(String[] args) {
        FindMajorityNumber findMajorityNumber = new FindMajorityNumber();

        int[] arr = new int[]{1, 2, 2, 2, 2, 2, 2, 3, 4, 5, 5, 5, 5};
        System.out.println(findMajorityNumber.majority1(arr));

        System.out.println(findMajorityNumber.majority2(arr));
    }
}
```
위 코드는 배열을 입력으로 들어올때 그 배열에서 빈도수가 가장 많은 숫자를 반환하는 메소드를 가진 class 입니다.<br>
majority1 메소드의 경우에는 이중반복문을 사용함으로 시간복잡도는 N^2입니다. <br>
하지만 majority2 메소드는 시간 복잡도가 N이죠. 같은 기능을 수행하면서도 이렇게 시간복잡도가 차이날 수 있습니다. <br>

 
선형시간 알고리즘
---

   "선형적이다."라는 것은 어떠한 데이터의 나열을 처음부터 끝까지 한번정도 본다라고 이해하면 편합니다.<br> 
   따라서 데이터가 n배로 늘어나면 소요되는 시간도 n배로 증가하게 됩니다. <br>
   선형시간 알고리즘에 관한 예시로 FindMajorityNumber의 majority2 또한 한가지 예시가 될 수 있지만 다른 예로서 이동 평균을 구하는 알고리즘으로 설명을 하도록 하겠습니다.<br>

 

   이동 평균이란 주식의 가격, GDP 등 시간에 따라 변화하는 값들을 관찰할때 유용하게 사용되는 통계적 기준입니다. <br>
   M-이동 평균은 M개의 관찰 값에서 평균을 낸 지표라고 생각하면 됩니다. 구체적인 수치로 생각해보면<br>

1, 2, 3, 4, 5, 6, 7, 8, 9 <br>

라는 관찰 값이 있고 3-이동평균을 구해야한다고 하면 결과값은<br>

(1+2+3)/3, (2+3+4)/3,(3+4+5)/3 ......  (7+8+9)/3 <br>

이렇게 되는 것 입니다. 구체적인 코드를 보도록 하겠습니다.<br>

 
```
import java.util.ArrayList;
import java.util.List;

public class FindMovingAverage {
    private List<Double> result;
    //시간복잡도 3^N
    public List<Double> movingAverage1(List<Double> arr, int range){
        result = new ArrayList<>();
        double sum = 0;
        for(int i = range-1; i<arr.size(); i++){
            sum = 0;
            for(int j = 0; j<range; j++) {
                sum+=arr.get(i-j);
            }
            sum/=3;
            result.add(sum);
        }
        return result;
    }
    
    //시간복잡도 N
    public List<Double> movingAverage2(List<Double> arr, int range){
        result = new ArrayList<>();
        double sum = 0.0;

        for(int i = 0; i<range-1; i++)
            sum+=arr.get(i);
        
        for(int i = range-1; i<arr.size(); i++){
            sum+=arr.get(i);
            result.add(sum/3);
            sum-=arr.get(i-range+1);
        }
        return result;
    }

    public static void main(String[] args) {
        List<Double> arr = List.of(1.0, 2.0, 3.0, 4.0, 5.0, 6.0, 7.0, 8.0);
        FindMovingAverage findMovingAverage = new FindMovingAverage();
        List<Double> res1 = findMovingAverage.movingAverage1(arr, 3);
        List<Double> res2 = findMovingAverage.movingAverage2(arr, 3);
        System.out.println(res1);
        System.out.println(res2);
    }
}
```
movingAverage1의 경우에는 각 원소마다 range번씩  반복문을 돌기 때문에 시간복잡도는 N^M이 됩니다.<br>
하지만 movingAverage2의 경우에는 선형시간 알고리즘이기 때문에 시간복잡도는 N이 되는 것이죠 따라서 N의 수가 2배가 되면 소요 시간도 2배가 되고 1/2배가 되면 소요시간도 1/2배가 됩니다.<br>
이것이 선형시간 알고리즘 입니다.<br>


선형이하 시간 알고리즘
---
   binarySearch를 예시로 들 수 있습니다. <br>
   선형이하 시간을 사용하려면 주어진 데이터를 모두 확인하는 것이 아닌 일부분을 사용하고서 원하는 결과를 도출할 수 있어야합니다. <br>
   하지만 이는 생각보다 쉽지 않죠. 따라서 우리는 기본적인 사전 조건을 설정하고 선형이하 시간 알고리즘을 구현할 수 있습니다. <br>
   그 사전조건은 바로 정렬이 되어 있다는 것입니다. <br>
   정렬의 기준은 다를 수 있지만 일반적으로 오름차순 또는 시간 순으로 정렬이 되어 있을 것입니다. <br>
   따라서 어느 한 지점을 임의로 잡았을 때(일반적으로 가지고 있는 데이터의 가운데 원소 위치를 잡는다.) 내가 찾는 조건이 맞는 지 검사하고 그에 따라서 앞이나 뒤 영역만 다시 검사하면 됩니다.<br>
   글로만 이해해서는 잘 모르겠으니 코드를 통해서 살펴보도록하죠.<br>

 
```
public class BinarySearch {
	//시간복잡도 log N
    public int binarySearch1(int[] arr, int target){
        int index = -1;

        int first = 0, last = arr.length-1;
        int mid = 0;
        while(first != last){
            mid = (first + last)/2;
            if(arr[mid] < target)
                first = mid + 1;
            else
                last = mid;
        }
        if(arr[first] ==  target)
            index = first;
        return index;
    }
    public int binarySearch2(int[] arr, int target){
    //method1과 같은 기능을 수행
        return Arrays.binarySearch(arr, target);
    }

    public static void main(String[] args) {
        int[] arr = new int[]{1, 2, 3, 4, 5, 6, 7, 10, 20, 25, 60, 70, 100};

        BinarySearch bs = new BinarySearch();
        System.out.println(bs.binarySearch(arr, 1));
    }
}
 ```

   인자로 들어온 target을 찾기 위해서 중간 지점의 원소를 검사하고 이를 통해서 다시 검사할 영역을 지정한다. <br>
   이 과정을 한개의 원소만 남을 때 까지 반복한 후 마지막으로 남은 원소가 target과 일치하면 그 원소의 index를 아니라면 -1을 반환하도록 설계되어 있다. <br>
   여기서 한가지 더 신경써야할 점은 인자로 들어온 배열이 이미 정렬이 되어 있는 상태라는 것도 반드시 기억해야할 사항이다. <br>
   자바에서는 이미 binarySearch를 Arrays객체에서 지원해주기 때문에 일부러 구현할 필요는 없지만 그 원리는 확실히 파악하는 것이 중요하다.

 

지수시간 알고리즘
---

   binaryTree구조를 가지면 2^N의 시간 복잡도를 가지는데 이를 지수시간 알고리즘이라고 할 수 있다.<br> 
   만약 N명의 사람이 있을 때 M개의 음식을 만들수 있다고 가정해보자. <br>
   각 사람은 편식을 하기 때문에 모든 사람이 적어도 하나의 음식은 먹을 수 있도록하는 음식의 최소 개수를 구한다고 할때 어떤 식으로 구현해야할까?<br> 
   아래의 코드를 보도록 하자
   
```
public class FindFoodMenu {
    private final int INF = 987654321;
    private int M;
    
    boolean canEveryBodyEat(final List<Integer> menu){
        //들어가 있는 음식에 대해서 음식을 먹는지 안먹지는 모두 검사하여 모든 사람이 음식을 먹을수 있으면 true 아니면 false
        return true;
    }
    int selectMenu(List<Integer> menu, int food){
        if(M == food){
            if(canEveryBodyEat(menu)) return menu.size();
            return INF;
        }
        //food를 넣지 않고 음식을 만드는 만드는 경우의 수
        int ret = selectMenu(menu, food + 1);
        menu.add(food);
        //food를 넣고 음식을 만드는 경우의 수와 비교
        ret = Math.min(ret, selectMenu(menu, food + 1));
        menu.remove(menu.size()-1);
        return ret;
    }
}
```
책에서는 위 코드와 같이 적혀있지만 canEveryBodyEat은 구현은 하지 않고 그저 기능명시만 하였고 전형적인 binarySearchTree구조로 재귀적인 함수 구현이였음을 볼 수 있다. <br> 
하지만 개인적인 생각으로는 코드가 직관적이지 않아서 좀 더 깔끔하게 구현할 수 있지 않을까 고민하고 있는 상태이다. 

 

   입력의 개수가 지수시간 알고리즘의 시간을 결정하는 경우도 있지만 입력 값의 크기가 실행시간을 결정하는 경우도 있다. <br> 
   소인수분해를 하는 메소드가 있다고 가정해보자 사용자는 입력으로 하나의 정수를 입력 받지만 우리는 이 숫자를 소인수 분해하기 위해서 생각할 수 있는 가장 간단한 알고리즘은 2~N-1까지 모두 나누어 보는 것이 가장 간단할 것이다. 코드를 바로 보도록하자

```
public class SoInSu {
    private List<Integer> result;
    public List<Integer> factor(int n){
        result = new ArrayList<>();
        if(n == 1){
            result.add(1);
            return result;
        }

        for(int i = 2; n>1; i++){
            while(n%i == 0){
                result.add(i);
                n /= i;
            }
        }
        return result;
    }

    public static void main(String[] args) {
        SoInSu sis = new SoInSu();
        System.out.println(sis.factor(144));
    }
}
```
n이 1일 될때까지 i의 숫자를 모두 나누어 보는 알고리즘을 사용하였다. <br> 
이때 입력에 사용되는 비트수가 하나 증가할 때 표현할 수 있는 수의 범위와 저장 공간이 두배로 들어나기 때문에 지수시간 알고리즘이라고 할 수 있다고 한다.<br> 
이렇게 말해서는 무슨 이야기 인지 처음에 잘 이해가 되지 않았다. 좀더 명확하게 이해해보자<br> 

컴퓨터는 숫자를 비트의 형태로 저장하고 있다.

7은  111, 8은  1000, 9는 1001 ...

integer의 경우에는 일반적으로 4byte를 차지함으로 저장공간에는 

7(10진수) = 00000000 00000000 00000000 00000111(2진수)이렇게 표현할 수 있다.

그렇다면 7이 입력으로 들어오면 사용되는 비트수는 3개, 100 도 3개, 101도 3개 이다.

그러면 3개의 비트로 표현되는 숫자는 0~7까지라고 생각하면 된다. 이때 만약에 사용되는 비트수가 1개 더 증가한다면?

2^3 -> 2^4범위로 증가하게 되고 표현할 수 있는 영역이 2배 그리고 이를 저장해야할 공간도 대략 2배로 증가하게 되니 지수시간 알고리즘이라고 할 수 있다.
