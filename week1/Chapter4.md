 알고리즘의 시간복잡도는 일반적으로 반복문에 의해서 판단됩니다. 수행시간에 관해서는 여러 다양한 요인들이 있지만 그것은 사용하는 언어, 개발 환경 ...등 너무나도 변칙적이기 때문에 우리는 반복문이 수행되는 횟수를 통해서 시간예측합니다. 한가지 예를 보도록 하겠습니다.

 

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
   위 코드는 배열을 입력으로 들어올때 그 배열에서 빈도수가 가장 많은 숫자를 반환하는 메소드를 가진 class 입니다.

majority1 메소드의 경우에는 이중반복문을 사용함으로 시간복잡도는 N^2입니다. 하지만 majority2 메소드는 시간 복잡도가 N이죠. 같은 기능을 수행하면서도 이렇게 시간복잡도가 차이날 수 있습니다. 

 

선형시간 알고리즘

   "선형적이다."라는 것은 어떠한 데이터의 나열을 처음부터 끝까지 한번정도 본다라고 이해하면 편합니다. 따라서 데이터가 n배로 늘어나면 소요되는 시간도 n배로 증가하게 됩니다. 선형시간 알고리즘에 관한 예시로 FindMajorityNumber의 majority2 또한 한가지 예시가 될 수 있지만 다른 예로서 이동 평균을 구하는 알고리즘으로 설명을 하도록 하겠습니다.

 

   이동 평균이란 주식의 가격, GDP 등 시간에 따라 변화하는 값들을 관찰할때 유용하게 사용되는 통계적 기준입니다. M-이동 평균은 M개의 관찰 값에서 평균을 낸 지표라고 생각하면 됩니다. 구체적인 수치로 생각해보면

 

1, 2, 3, 4, 5, 6, 7, 8, 9 라는 관찰 값이 있고 3-이동평균을 구해야한다고 하면 결과값은

(1+2+3)/3, (2+3+4)/3,(3+4+5)/3 ......  (7+8+9)/3 이렇게 되는 것 입니다. 구체적인 코드를 보도록 하겠습니다.

 

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
movingAverage1의 경우에는 각 원소마다 range번씩  반복문을 돌기 때문에 시간복잡도는 N^M이 됩니다.

하지만 movingAverage2의 경우에는 선형시간 알고리즘이기 때문에 시간복잡도는 N이 되는 것이죠 따라서 N의 수가 2배가 되면 소요 시간도 2배가 되고 1/2배가 되면 소요시간도 1/2배가 됩니다.

이것이 선형시간 알고리즘 입니다.