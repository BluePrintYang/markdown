### NO.2 求最值(多组输入)

##### Description:

给定N个整数（1<=N<=100），求出这N个数中的最大值，最小值。

##### Input:

多组数据，第一行为一个整数N，第二行为N个不超过100的正整数，用空格隔开。

##### Output:

对每组数据输出一行，包含两个整数，用一个空格隔开，分别表示N个数中的最大值和最小值

##### Sample Input:

```
5
4 6 7 3 1
4
4 3 5 1
```

##### Sample Output:

```
7 1
5 1
```

```Java
import java.util.Scanner;

public class Main {
    public static void main(String[] args){
        Scanner s = new Scanner(System.in);
        while (s.hasNextInt()){//多组输入
            int n = s.nextInt();
            int max = 0;
            int min = 101;
            while(n-->0){
                int num = s.nextInt();
                if(num>max)max=num;
                if(num<min)min=num;
            }
            System.out.println(max+" "+min);
        }
    }
}
```



### NOJ1005 多项式加法

错误代码

```Java
import java.util.Scanner;

public class Main {
    public static void main(String[] args){
        Scanner s = new Scanner(System.in);
        int k,n;
        k = s.nextInt();
        n =s .nextInt();
        if (k<0) System.out.print("-");
        do{
            if (n==0) System.out.print(k);
            else System.out.print(k+"X"+"^"+n);
            k=s.nextInt();
            n=s.nextInt();
            if (k>0) System.out.print("+");
            if (k==0&&n==-1) System.out.println();
        }while (k!=0&&n!=-1&&s.hasNextInt());

    }
}
```



### NOJ1015最大公约数最小公倍数

```Java
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner s =new Scanner(System.in);
        int a =s.nextInt();
        int b =s.nextInt();
        int gcd = gcd(a,b);
        int gcb = a*b/gcd;//最小公倍数为a*b除以最大公约数
        System.out.println(gcd+" "+gcb);

    }

    static public int gcd(int a, int b) {
        if (a == 0) return a;
        if (b == 0) return b;
        if (a > b) {
            int swap = a;
            a = b;
            b = swap;
        }
        while (a>0){
            int c = b%a;
            b=a;
            a=c;
        }
        return b;
    }
}
```



