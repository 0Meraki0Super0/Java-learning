
**一、next() :** 只读缓冲区中空格之前的数据,并且光标指向本行。  
**二、nextLine() :** 读取除回车以外的所有符号(整行内容)，光标定位在下一行  
**三、hasNext() ：** 检查下一个标记（token），也就是以空格、制表符或==换行符==为分隔符的一个单词。如果输入流中还有下一个标记，则返回 true；否则返回 false。  
**四、hasNextLine()：** 检查下一行是否存在。如果输入流中还有下一行，则返回 true；否则返回 false。

采用`hasNextXxxx()` 的话，后面也要用`nextXxxx()`:
- 比如前面用`hasNextLine()`，那么后面要用 `nextLine()` 来处理输入;
- 后面用 `nextInt()` 方法的话,那么前面要使用 `hasNext()`方法去判断.
## 1.1 多组空格分隔的两个正整数

> 输入包括两个正整数a,b(1 <= a, b <= 10^9),输入数据包括多组；

```
import java.util.Scanner;
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        while (in.hasNextInt()) { // 注意 while 处理多个 case
            int a = in.nextInt();
            int b = in.nextInt();
            //处理
        }
    }
}
```

## 1.2 第一行组数接空格分隔的两个正整数

> 第一行输入数据个数，后面输入数据；

```
import java.util.Scanner;
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int num = in.nextInt();
        for(int i = 0; i < num; i++) { // 注意 while 处理多个 case
            int a = in.nextInt();
            int b = in.nextInt();
            //处理
        }
    }
}
```

## 1.3 空格分隔的两个正整数为0 0 结束

> 输入包括两个正整数a,b(1 <= a, b <= 10^9), 如果输入为0 0则结束输入；

```
import java.util.Scanner;
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        while (in.hasNextInt()) { // 注意 while 处理多个 case
            int a = in.nextInt();
            int b = in.nextInt();
            if(a ==0 && b == 0) break;
            //处理
        }
    }
}
```

## 1.4 每行第一个为个数后带空格分割整数为0结束

```
import java.util.Scanner;
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        while (in.hasNextInt()) { // 注意 while 处理多个 case
            int n = in.nextInt();
            if(n == 0) break;
            int sum = 0;
            for (int i = 0; i < n; i++) {
                sum += in.nextInt();
            }
            System.out.println(sum);
        }
    }
}
```

  

# 2. 字符串处理

|比较项|next( )|nextLine( )|
|---|---|---|
|**说明**|只能读取到空格之前的字符串|可以读取空格的字符串|
|**比如“你好 java”**|“你好”|“你好 java”|
|**使用前判断**|in.hasNext()|in.hasNextLine()|

## 2.1 第一行个数第二行字符串

```
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int n = in.nextInt();
        in.nextLine();
        while (in.hasNext()) { // 注意 while 处理多个 case
            String[] s = in.nextLine().split(" ");
            //处理
        }
    }
}
```

## 2.2 多行空格分开的字符串

```
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        while (in.hasNextLine()) { // 注意 while 处理多个 case
            String[] s = in.nextLine().split(" ");
            //处理
        }
    }
}
```

  

# 3. 输出格式化相关

- 输出有两种方式：**String str=String.format(示例)** 或 **System.out.printf(示例)**；
- 向上取整用：**Math.ceil(1.01)**，向下取整用：**Math.floor(1.01)**；

## 3.1 转换符

|转换符|说明|示例|输出|
|---|---|---|---|
|**%s**|字符串|"Hi,%s:%s.%s", "王南","王力","王张"|Hi,王南:王力.王张|
|**%c**|字符|"字母a的大写是：%c %n", 'A'|字母a的大写是：A|
|**%b**|布尔|"3>7的结果是：%b %n", 3>7|3>7的结果是：false|
|**%d**|整数(十进制)|"100的一半是：%d %n", 100/2|100的一半是：50|
|**%x**|整数(十六进制)|"100的16进制数是：%x %n", 100|100的16进制数是：64|
|**%o**|整数(八进制)|"100的8进制数是：%o %n", 100|100的8进制数是：144|
|**%f**|浮点|"50元的书打8.5折扣是：%f 元%n", 50*0.85|50元的书打8.5折扣是：42.500000 元|
|**%a**|浮点(十六进制)|"上面价格的16进制数是：%a %n", 50*0.85|上面价格的16进制数是：0x1.54p5|
|**%e**|指数|"上面价格的指数表示：%e %n", 50*0.85|上面价格的指数表示：4.250000e+01|
|**%g**|通用浮点(f和e类型中较短的)|"上面价格的指数和浮点数结果的长度较短的是：%g %n", 50*0.85|上面价格的指数和浮点数结果的长度较短的是：42.5000|
|**%h**|散列码|"字母A的散列码是：%h %n", 'A'|字母A的散列码是：41|
|**%%**|百分比|"上面的折扣是%d%% %n", 85|上面的折扣是85%|
|**%n**|换行符|||
|**%tx**|日期与时间|||

## 3.2 搭配转换符的标志

|标志|说明|示例|输出|
|---|---|---|---|
|**.**|后接保留多少位小数(四舍五入)|("%.2f",3.555)|3.56|
|**+**|为正数或者负数添加符号|("%+d",15)|+15|
|**−**|左对齐|("%-5d",15)|\|15 \||
|**0**|数字前面补0|("%04d", 99)|0099|
|**空格**|在整数之前添加指定数量的空格|("% 4d", 99)|\|99\||
|**,**|以“,”对数字分组|("%,f", 9999.99)|9,999.990000|
|**(**|使用括号包含负数|("%(f", -99.99)|(99.990000)|
|**#**|如果是浮点数则包含小数点，如果是16进制或8进制则添加0x或0|("%#x", 99) ("%#o", 99)|0x63 0143|
|**<**|格式化前一个转换符所描述的参数|("%f和<3.2f", 99.45)|99.450000和99.45|
|**$**|被格式化的参数索引|("%1&dollar;d,\%2&dollar;s", 99,"abc")|99,abc|

  

# 4. ACM 模式模板

```
public class Main {

    public static void main(String[] args) {
        //1.数据输入
        Scanner in = new Scanner(System.in);
        //读数字
        int numLen = in.nextInt();
        int[] numArr = new int[numLen];
        int i = 0;
        while(in.hasNextInt() && i < numLen){
            numArr[i] = in.nextInt();
            i++;
        }
        //读字符串
        int strLen = in.nextInt();
        in.nextLine(); //数字到字符串要换行
        String[] strArr = new String[strLen];
        //或者 strArr[] = in.nextLine().split(" ");
        int j = 0;
        while(in.hasNextLine() && j < strLen){
            strArr[j] = in.nextLine();
            j++;
        }
        
        //2. 处理
        Solution solution = new Solution();
        String result = solution.process(numArr, strArr);
        
        //3. 输出
        System.out.println(result);
        //四舍五入输出小数
        String str = String.format("%.2f",3.555);
        System.out.println(str);
    }
}

//下面类似 LeetCode 的核心代码模式
class Solution {
    public String process(int[] nums, String[] strs) {
        StringBuilder sb = new StringBuilder();
        sb.append(Arrays.toString(nums));
        sb.append(" && ");
        sb.append(Arrays.toString(strs));
        return sb.toString();
    }
}
```