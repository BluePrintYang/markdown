A. UCU与SadBB的故事
在很久很久以前，大概也就是3年前，SadBB觉得他的ID过于愚蠢，所以决定换一个ID。于是一个奇怪的ID：UCU，横空出世。

大家觉得UCU一定有着奇怪的含义，比如说可能是一位美丽可爱的小姐姐的代号。

基于这样的猜测，某侦探开始了他的破案之旅，他认为UCU一定代表着，这个小姐姐的名字的第一个字和最后一个字的首字母是一样的，且第二个字的首字母为C。

他收集了SadBB认识的可爱的小姐姐的名字，筛选出了其中三个字的名字，并将她们名字转换成了拼音，眼看真相就要水落石出，来自东方的一股神秘力量将他封印了起来。

现在你拿到了这份名单（所有名字都被转换为拼音全拼了），你能帮忙寻找出这谜之可能存在的小姐姐吗？（本故事纯属虚构）

 

输入：

第1行为一个整数n，1<=n<=100

第2到n+1行，每行由三个拼音单词组成，拼音单词之间用空格隔开。

 

输出：

每行输出一个满足条件的名字的拼音，每个名字的三个拼音之间用空格隔开，名字的输出顺序应与输入顺序一致。

 

样例输入：

4

yu bu yu

jiang chun ji

guan jin chu

li chen liang

 

样例输出：

jiang chun ji

li chen liang

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

public class Test {
    public static void main(String[] args) {
        int n;
        Scanner scanner = new Scanner(System.in);
        n=scanner.nextInt();
        List<String> names = new ArrayList<>();
        scanner.nextLine();//跳过上面的换行
        while (n-->0){
            String name = scanner.nextLine();
            names.add(name);
        }
        for (String na:names) {
            String[] naSplit = na.split(" ");
            if (naSplit[0].charAt(0)==naSplit[2].charAt(0)&&naSplit[1].charAt(0)=='c') System.out.println(na);
        }
    }
}
```













