# 一.语言入门基础

## 1.发展

c语言出现的要比操作系统早

linux是类unix系统

mac的内核是uinx

## 2.输入输出函数

### 原型

![image-20200108101958535](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200108101958535.png)

返回值为打印字符的个数

![image-20200108102008929](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200108102008929.png)

返回值为输入参数的个数 

```c
printf("hello world\n");//返回值12
scanf("%d%d", &a, &b);//返回值2
```

%g去掉无用的0
字符读入尽量少用%c

习题1：

使用printf函数，求解一个数字n的十进制表示的数字位数

```c
#include<stdio.h>
int main(){
    int n;
    scanf("%d", &n);//第一个参数为格式控制字符串
    printf(" has %d digit\n", printf("%d", n));
    return 0;
}
```



```c
char str[100];
scanf("%s",str);//这样输入的字符串应无空格，有空格只能输入空格之前的字符串

char str[100];
scanf("%[^\n]s",str);//这样输入可以包含空格
```

int printf/scanf 为流式输入输出

getchar()获取一个字符

```c
int a, b;
scanf("%d%d", &a, &b);
//当输入为2 3时，输入的过程为空格前的2给a
//然后吞掉空格，将b赋值为3
```

习题2：

写一个程序，读入一个行字符串（可能包含空格），输出字符数量

```c
#include<stdio.h>
int main(){
    char str[1000]; 
    char str1[1000];
    
    scanf("%[^\n]s", str);//%s,d,c等为格式占位符 格式控制字符串可以用正则表达式
    getchar();//重点  吞掉/n
    scanf("%[^\n]s", str1);
    
    printf("%s\n", str);
    printf("%s\n", str1);
    return 0;
}
```

^表示异或

EOF  end of file表示文档末尾 EOF=-1



### sprintf/fprintf

sprintf(字符串的首地址(目的地)，"%d%f等格式控制字符串"， 要写入的东西)；

```c
//将输入的数字转为ip地址
#include<stdio.h>

int main(){
    char str[100];
    char str2[100];
    int a, b, c, d;
    scanf("%d%d%d%d", &a, &b, &c, &d);
    sprintf(str2, "%d.%d.%d.%d", a, b, c, d);
    printf("%s\n", str2);
    return 0;
}

```

fprintf(文件指针，"格式控制字符串"，要写入的东西)；

```c
//将ip地址写入out文件中
#include<stdio.h>

int main(){
    char str[100];
    char str2[100];
    int a, b, c, d;
    scanf("%d%d%d%d", &a, &b, &c, &d);
    sprintf(str2, "%d.%d.%d.%d", a, b, c, d);
    FILE *fd = fopen("out", "wb");
    fprintf(fd, "%s", str2);
    printf("%s\n", str2);
    return 0;
}

```



### 标准的输入输出：stdin,stdout,stderr

stdin:标准输入   终端中的输入

stdout:标准输出  显示在终端上

stderr:标准错误输出

```c
#include<stdio.h>

int main(){
    char str[100];
    char str2[100];
    int a, b, c, d;
    scanf("%d%d%d%d", &a, &b, &c, &d);
    sprintf(str2, "%d.%d.%d.%d", a, b, c, d);
    //FILE *fd = fopen("out", "wb");
    fprintf(stdout, "stdout = %s\n", str2);//将str2显示在终端上
    fprintf(stderr, "stderr = %s\n", str2);
    printf("%s\n", str2);
    return 0;
}

```

![image-20200108112826638](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200108112826638.png)

./a.out > out将a.out的输入打印到out文件中 

当执行./a.out时，stdout与stderr效果一样

当执行./a.out > stdout时，只有stderr的打印在终端上，其余的都会打印在stdout文件中

./a.out > stdout 为标准输出重定项，不包括错误输出

将bug的错误输出和正确的输出分开

![image-20200108113520118](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200108113520118.png)

./a.out 2> stderr 将标准错误输出写在stderr



## 3.编码规范

![image-20200108113922325](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200108113922325.png)

## 4.推荐读物

![image-20200108104012093](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200108104012093.png)

![image-20200108104021195](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200108104021195.png)

https://wiki.jikexueyuan.com/list/c/极客学院网站

# 二.数学运算

学习一门语言要抽出时间看数学运算方法（不同的语言不一样）python  3 ** 4

## 1.基础知识

![image-20200108114417683](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200108114417683.png)

![image-20200108114532718](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200108114532718.png)

![image-20200108114543717](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200108114543717.png)

<<左移 末尾补0    >> 右移 头部补符号位

位操作是最快的 比四则运算快8-10倍

补码将减法变为加法
负数一直右移不能变为0，最大为-1

c语言左值和右值

实现两个数的交换可以不用定义交换变量:

<img src="https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200108151629157.png" alt="image-20200108151629157" style="zoom:50%;" />

```c
a ^= b;
b ^= a;
a ^= b;//a,b两两异或得到c  那么abc知道其中两者就能知道第三个
```

![image-20200108114746109](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200108114746109.png)

log换底公式    本身公式以e为底
arccos(-1) 精确圆周率

这些函数的头文件math.h

c语言29个标准头文件
非标准头文件（eg:windows.h）



!!归一化操作

switch(a) a为一个整型（这里指可以转化为ASCII码） 

for(int i = 1; i < 101; i++) i为局部变量

if(a – b)与if(a != b)相同

短路运算 if(a && b)当a为假时不需要执行b 逻辑或同理

i && printf(“ ”) 打印空格问题

time(0) 1900.1.1到现在的秒数

判断奇偶数时注意最后一位时是1或0，可以用n & 1判断最后一位

计算位数时可以while n / 10 操作（注意do while 和 while 的区别）

随机数的算法是特定的，给它什么什么数（随机种子），它一定会输出与之对应的确定的数，所以一般取系统时间戳为随机种子

cpu底层命令 执行快

%g去掉无用的0

字符读入尽量少用%c



习题：求x的立方根

pow（x, 1.0 / 3.0）;

## 2.蒙特卡罗法求pi

通过概率求

随机数的算法是特定的，给它什么什么数（随机种子），它一定会输出与之对应的确定的数，所以一般取系统时间戳为随机种子

![image-20200108115414031](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200108115414031.png)

```c
//蒙特卡罗法求pi
#include<iostream>
#include<stdio.h>
#include<time.h>
#define MAX_OP 1000000
int main(){
// time(0) 1900.1.1到现在的秒数
    srand(time(0));//生成一个随机种子（用时间戳）
    int n = 0, m = 0;
    for(int i = 0; i < MAX_OP; i++){
        double x = rand() * 1.0 / RAND_MAX * (rand() % 2 ? 1 : -1);//RAND_MAX表示能生成的最大的随机数
        double y = rand() * 1.0 / RAND_MAX * (rand() % 2 ? 1 : -1);
        if(x * x + y * y <= 1.0) m++;//随机生成的点是否在圆内
        n++;
    }
    printf("%lf\n", m * 1.0 / n * 4.0);
    return 0;
}

```

## 3.实现sqrt函数

a.用二分法

边界条件

当x>1时，sqrt（x）的范围为0-x

当x<1时，sqrt(x)的范围为0-1

代码1：

```c
#include<stdio.h>
#define EPS 1e-7

double my_sqrt(double x){
    double l = 0, r = x <= 1.0 ? 1.0 : x;//确定上界
    while(r - l > EPS){
        double mid = (l + r) / 2.0;
        if(mid * mid <= x) l = mid;
        else r = mid;
    }
    return l;
}

int main(){
    double x;
    scanf("%lf", &x);
    printf("%lf\n", my_sqrt(x));
    return 0;
}
```

代码2：

```c
#include<stdio.h>
#define EPS 1e-7

double my_sqrt(double x){
    double l = 0, r = x <= 1.0 ? 1.0 : x;
    for(int i = 0; i < 100000; i++){//次数100000足够了已经 以log2的速度查询
        double mid = (l + r) / 2.0;
        if(mid * mid <= x) l = mid;
        else r = mid;
    }
    return l;
}

int main(){
    double x;
    scanf("%lf", &x);
    printf("%lf\n", my_sqrt(x));
    return 0;
}

```

b.用牛顿迭代法

![image-20200108144040550](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200108144040550.png)

![image-20200108144100999](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200108144100999.png)

```c
//牛顿迭代
#include<stdio.h>
#include<math.h>
#define EPS 1e-7

double f(double x, double n){//f(x)
    return x * x - n;
}

double f_prime(double x){//f(x)导数
    return 2 * x;
}

double my_sqrt(double n){
    double x = 1.0;
    int cnt = 0;
    while(fabs(f(x, n)) > EPS){
        x = x - f(x, n) / f_prime(x);//x不能从0开始，否则分母为0
        cnt++;
    }
    printf("run %d steps\n", cnt);//迭代次数与初始的x值有关
    return x;
}

int main(){
    double x;
    scanf("%lf", &x);
    printf("%lf\n", my_sqrt(x));
    return 0;
}
```

c.大神卡马克

https://www.cnblogs.com/pkuoliver/archive/2010/10/06/1844725.html

```c
//雷神之锤 大神卡马克 永远对技术怀有敬畏之心
//不需要循环o(1)
#include<stdio.h>
#include<math.h>
#define EPS 1e-7

float my_sqrt(float x)//输出1 / sqrt（x）
{
	float xhalf = 0.5f * x;
	int i = *(int*) & x; // get bits for floating VALUE 
	i = 0x5f375a86- (i>>1); // gives initial guess y0
	x = *(float*)&i; // convert bits BACK to float
	x = x*(1.5f-xhalf*x*x); // Newton step, repeating increases accuracy
	return x;
}  


int main(){
    double x;
    scanf("%lf", &x);
    printf("%lf\n", 1.0 / my_sqrt(x));
    return 0;
}

```

## 4.inttypes

https://zh.cppreference.com/w/c/types/integer

```c
#include <stdio.h>
#include <inttypes.h>
 
int main(void)
{
    printf("%zu\n", sizeof(int64_t));//64bit 占8字节
    printf("%s\n", PRId64);
    printf("%+"PRId64"\n", INT64_MIN);
    printf("%+"PRId64"\n", INT64_MAX);
 
    int64_t n = 7;
    printf("%+"PRId64"\n", n);
}
```

![image-20200109111902685](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200109111902685.png)

# 三.程序流程控制方法

## 1.关系运算符

![image-20200108150619815](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200108150619815.png)

任何表达式都有返回值
赋值表达式a=3 返回值3

逗号表达式(a=1,b=2,c=3)的返回值为最后一个3

关系表达时只有0  1两个返回值
！！（x）归一化

百度 运算符优先级  结合顺序（同等级）[运算符优先级](https://baike.baidu.com/item/%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BC%98%E5%85%88%E7%BA%A7/4752611?fr=aladdin)

## 2.分支结构

switch(a)  a为一个整型（这里指可以转化为ASCII码） 

 短路运算 if(a && b)当a为假时不需要执行b  逻辑或同

- if(a – b)与if(a != b)相同
- 判断奇偶数时注意最后一位时是1或0，可以用n & 1判断最后一位
- i && printf(“ ”)  打印空格问题

```c
for(int i = 0; i < 10; i++){
    i && printf(" ");
    printf("%d", i);
}
```

习题1：

![image-20200108154943480](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200108154943480.png)

```c
#include<stdio.h>
int main(){
    int n;
    scanf("%d", &n);
    if(n == 0)    printf("FOOLISH\n");
    else if(n < 60)    printf("FAIL\n");
    else if(n < 75)    printf("MEDIUM\n");
    else    printf("GOOD\n");
    return 0;
}
```



习题2：

![image-20200108155035212](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200108155035212.png)

```c
#include<stdio.h>
int main(){
    int n;
    scanf("%d", &n);
    switch(n) {
        case 1: printf("one\n"); break;
        case 2: printf("two\n"); break;
        case 3: printf("three\n"); break;
        default: printf("error\n");
    }
    return 0;
}

```



习题3：

![image-20200108155117787](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200108155117787.png)

```c
#include<stdio.h>
int main(){
    int n;
    scanf("%d", &n);
    switch(n) {
        case 1: printf("one\n"); 
        case 2: printf("two\n");
        case 3: printf("three\n"); break;
        default: printf("error\n");
    }
    return 0;
}
```



### CPU选择预测

计算机很讨厌if等语句的判断

![image-20200108160753049](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200108160753049.png)

负数肯定不是回文数

```c
#include<iostream>
using namespace std;

bool isPalindrome(int x){
    //if(x < 0) return false;
    if(__builtin_expect(!!(x < 0), 0)) return false;//x为负数 返回false x<0的概率不大
    int y = 0, z = x;
    while(x){
        y = y * 10 + x % 10;
        x /= 10;
    }
    return z == y;
}

int main(){
    int n;
    cin >> n;
    if(isPalindrome(n)) cout << "YES" << endl;
    else cout << "NO" << endl;
    return 0;
}
```

![image-20200109101250849](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200109101250849.png)



上述的函数__builtin_expect(EXP,N)是linux内核里的函数，经常的使用方法为定义宏

> 这个指令是gcc引入的，作用是**允许程序员将最有可能执行的分支告诉编译器**。这个指令的写法为：`__builtin_expect(EXP, N)`。
>  意思是：EXP==N的概率很大。

所以上面我们调用函数__builtin_expect()的意思是告诉计算机x<0的概率比较小

![image-20200109101540235](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200109101540235.png)



cpu对一条指令的处理：取地址，指令预解析，取数据，执行，写回

计算机对指令的处理是并行的，流水线工作方式，并不是等这条指令执行完再进行下一条指令的处理，为什么说cpu讨厌if判断

![image-20200109102248922](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200109102248922.png)

if条件判断时，计算机做并行运算是计算if里的还是else里的，cpu会作一个预测，预测对了就还是并行处理，预测错了前面所作的处理全部清0，耗费时间

上面的likely和unlikely是帮助cpu进行预测的，提高预测成功率

<u>上面我们调用函数__builtin_expect()的意思是告诉计算机x<0的概率比较小 所以会帮助cpu预测为x > 0 从而让cpu加载程序x > 0的部分，提高预测成功率，如果我们单纯的用if（x < 0）,cpu会自行预测，这样预测成功率会降低</u>

![image-20200109102515830](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200109102515830.png)



## 3.循环结构

习题：

使用while打印1-100

```c
#include<stdio.h>
int main(){
    int i = 0;
    while((++i) < 101){
        printf("%d\n", i);
    }
    return 0;
}
```

# 四.函数

## 1.递归

注意边界条件  陷入死循环时程序报错 核信息转储（爆栈） 栈区8MB

![image-20200914100801689](https://gitee.com/xsm970228/images/raw/master/20200914100820.png)

～scanf(“%d”, &n)等同于scanf（“%d”, &n) != EOF (-1 二进制补码全1，按位取反为0)

```cpp
#include<stdio.h>

int fac(int n){
    if(n == 0) return 1;
    return n * fac(n - 1);
}

int main(){
    int n;
    while(~scanf("%d", &n)){
        printf("fac(%d) = %d\n", n, fac(n));
    }
    return 0;
}

```

判断输入流到达末尾的三种方式：

- scanf("%d", &n) != EOF
- scanf("%d", &n) != -1
- ~scanf("%d", &n)

为什么不是scanf("%d", &n) != 0？？

匹配错误返回0，到文件末尾返回-1

>  On success, these functions return the number of input items successfully matched and assigned; this can be fewer than provided for, or even zero, in the event of an early matching failure.
>
>    The value EOF is returned if the end of input is reached before either the first successful conversion or a matching failure occurs.  EOF is also returned if a read  error  occurs,  in  which
>    case the error indicator for the stream (see ferror(3)) is set, and errno is set to indicate the error.

## 2.变参函数

变参函数va家族

![image-20200914104607392](https://gitee.com/xsm970228/images/raw/master/20200914104623.png)

```cpp
#include<iostream>
#include<stdarg.h>
#include<inttypes.h>
using namespace std;

//n指在多少个数中找到最大值
int max_int(int n, ...){
    va_list arg;//arg变量
    va_start(arg, n);//将n后边的参数放入arg中
    int ans = INT32_MIN;
    while(n--){
        int tmp = va_arg(arg, int);//按int型取数据 宏
        tmp > ans && (ans = tmp);
    }
    va_end(arg);
    return ans;
}

int main(){
    cout << max_int(3, 3, 5, 16) << endl;
    cout << max_int(3, 3, 5, 16, 21) << endl;
    cout << max_int(4, 3, 5, 21, -32) << endl;
    return 0;
}

```

- va_list arg：变量名，定义一个va_list的变量arg用来存放变参   

- va_start(arg, n)： 函数，将参数n后边的所有参数放入arg中

- va_arg(arg, int) ：函数，按int类型在arg中取参数 取一个销毁一个

- va_end（arg）：函数，释放arg变量

可以看到va_arg(arg, int)的第二个参数是int，所以这不是个函数，这是一个宏！！

# 五.编译及预处理

## 1.基础知识

宏以#开始

源文件 预编译（展开宏） 编译（生成对象文件.o/.obj） 链接（将所有的.o文件链接在一起） 生成可执行文件a.out

```cpp
#include<iostream>
using namespace std;
#define swap(a, b){\
    __typeof(a) __temp = a;\
    a = b, b = __temp;\
}

int main(){
    int a, b;
    cin >> a >> b;
    swap(a, b);
    cout << a << " " << b << endl;
}

```

\表示连接当前行与下一行 宏必须放在同一行

__typeof(a) 表示获取a的数据类型 

`g++ -E 0.cpp`查看预处理后将宏展开的代码

![image-20200914110925681](https://gitee.com/xsm970228/images/raw/master/20200914110927.png)

函数声明后（未定义）可以通过编译和预编译（函数可以放在其他文件中），当执行.o文件链接时报错

gcc -c 编译和预编译（变成对象文件） 函数在哪无所谓，但必须在链接的文件中

将两个.o文件链接

![image-20200914111153036](https://gitee.com/xsm970228/images/raw/master/20200914111206.png)

所有的函数声明写在头文件中，函数定义写在c文件中，否则，将函数定义写在头文件.h中时，两次包含头文件.h中时会出现函数重定义问题

## 2.宏

预处理以#开头

![image-20200914111259895](https://gitee.com/xsm970228/images/raw/master/20200914111424.png)

\#define S(a, b) a * b (傻瓜式替换)

比如S（3+4，5+6） 替换为3 + 4 * 5 + 6

宏必须放在一行 否则用 \ 连接

gcc -E 可以查看宏展开后的代码

预先定义好的宏：

![image-20200914111540833](https://gitee.com/xsm970228/images/raw/master/20200914142850.png)

![image-20200914111615368](https://gitee.com/xsm970228/images/raw/master/20200914142853.png)

gcc -DDEBUG可以在编译时首先定义一个DEBUG

```c
#include<stdio.h>
//下面的宏用（）抱住是因为我们需要宏的返回值赋给%d
#define Max(a, b) ({\
    __typeof(a) __a = (a);\
    __typeof(b) __b = (b);\
    __a > __b ? __a : __b;\
})

#define P(func){\
    printf("%s = %d\n", #func, func);\
}

int main(){
    int a = 7;
    P(Max(2, 3));
    P(5 + Max(2, 3));
    P(Max(2, Max(3, 4)));
    P(Max(2, 3 > 4 ? 3 : 4));
    P(Max(a++, 6));
    P(a);
    return 0;
}

```

用宏定义一个日志函数：

```cpp
#include<stdio.h>

#define DEBUG
#ifdef DEBUG

#define log(frm, arg...) {\
    printf("[%s : %s : %d]", __FILE__, __func__, __LINE__);\
    printf(frm, ##arg);\
    printf("\n");\
}
#else
#define log(frm, arg...)
#endif
#define contact(a, b) a##b//## 将a，b链接 宏前面有#和##表示停止展开

int main(){
    int a = 123, abcdef = 15;
    printf("contact = %d\n", contact(abc, def));
    log("%d", a);
    log("hello world");
    log("%d", abcdef);
    return 0;
}

```

- #：#arg指将arg以字符“arg”的形式输出，表示停止展开
- ##：表示将前后两个元素连接成一个

上述代码展开形式：

![image-20200914142334252](https://gitee.com/xsm970228/images/raw/master/20200914142837.png)

**第8行##表示连接前面的frm, 与后面的arg，让这两项变为一个参数为(frm, arg) 这样做可以让arg为空的时候也合法**

将第8行代码换为

```c
    printf(frm, #arg);\
```

代码展开后为：

![image-20200914143030042](https://gitee.com/xsm970228/images/raw/master/20200914143202.png)

**#arg就是将arg转化为字符串输出**

如果arg前面没有# 或者 ## 则arg可以持续展开 展开时先参数的合法性，当arg为空时，则不能继续展开，报错，如果有#或者##，则停止展开 即当arg为空时也合法

将第8行代码换为

```c
    printf(frm, arg);\
```

代码展开后为：

![image-20200914143246186](https://gitee.com/xsm970228/images/raw/master/20200914143249.png)

# 六.指针与结构体

```cpp
#include<stdio.h>
#include<stdlib.h>
int main(){
    int arr[100];
    int *p = arr;
    int *q = (int *)malloc(sizeof(int) * 100);
    printf("arr size : %d\n", sizeof(arr));
    printf("p size : %d\n", sizeof(q));
    printf("q size : %d\n", sizeof(q));
    return 0;
}
/*
arr size : 400
p size : 8
q size : 8
*/
//数组地址的指针sizeof为整个数组的大小
//自行定义的指针和从堆区申请的指针大小为8
```

## 1.指针

所有的指针变量都占8字节

![image-20200914154655660](https://gitee.com/xsm970228/images/raw/master/20200914154659.png)

```c
#include<iostream>
using namespace std;

//除法向0取整
//右移一位向下取整

int f1(int n){
    return n * 2;
}
int f2(int n){
    return n * n + 3;
}
int f3(int n){
    return n >> 1;
}

int (*f[3])(int) = {//函数指针数组
    f1, f2, f3
};

int main(){
    srand(time(0));
    int n;
    cin >> n;
    /*
    switch(rand() % 3){
        case 0: cout << f1(n) << endl; break;
        case 1: cout << f2(n) << endl; break;
        case 2: cout << f3(n) << endl; break;
    }*/
    cout << f[rand() % 3](n) << endl;
    return 0;
}

```

函数名就是函数地址

![image-20200914154713171](https://gitee.com/xsm970228/images/raw/master/20200914154716.png)

- argc 为传入的参数个数 

- *argv[]为具体的参数是什么 

- char **env为传入的环境变量

存储由高地址往低地址存储

空地址 NULL

## 2.结构体

![image-20200914143508670](https://gitee.com/xsm970228/images/raw/master/20200914143510.png)

![image-20200914143526361](https://gitee.com/xsm970228/images/raw/master/20200914143527.png)

struct占用的内存以最大类型的数据类型为一个标准，即当前用int/float的4字节为对齐标准，char name[20]用了5个对齐标准，而gender要占一个标准，即4个字节，后面的height 4 字节不能与gender放在同一个对齐标准中，所以下移

同一个对齐标准可以放多个变量，前提必须放的下

宏program（num）可以定义不同的对齐标准，一个标准为2^num个字节

![image-20200914143751109](F:\学习\Typora\image-20200914143751109.png)

第一个占 1+1+2 +4   第二个占4+4+4

```c
#include<stdio.h>

struct node1{
    char a;
    char b;
    int c;
}node1;

struct node2{
    char a;
    int b;
    char c;
}node2;

int main(){
    printf("sizeof(node1) = %ld\n", sizeof(node1));
    printf("sizeof(node2) = %ld\n", sizeof(node2));
    return 0;
}

```

![image-20200914144042879](https://gitee.com/xsm970228/images/raw/master/20200914144045.png)

struct node1 a；定义一个变量 （c语言中struct是一个类型，不能省略，而c++中是类，可以省略）

访问方式：直接访问node1.a  间接访问node1 → a

![image-20200914154456649](F:\学习\Typora\image-20200914154456649.png)

- (a+1) → x  
- *(a+1).x 
- (p+1) → x 
- *(p+1).x 
- p[1].x 
- (&a[1]) → x 
- (&a[0]+1) → x

结构体内的对齐标准：

- 每个成员的偏移量都必须是当前成员所占内存大小的整数倍如果不是编译器会在成员之间加上填充字节。
- 当所有成员大小计算完毕后，编译器判断当前结构体大小是否是结构体中最宽的成员变量大小的整数倍 如果不是会在最后一个成员后做字节填充。

```cpp
#include<iostream>
using namespace std;
//16个字节
//a:偏移量0 大小1
//b:偏移量1 不是shrot2个字节的整数倍 偏移量2 大小2
//c:偏移量4 大小1
//d:偏移量5 不是double8个字节的整数倍 偏移量8 大小8
struct Data1{
    char a;
    short b;
    char c;
    double d;
};

//24个字节
//a:偏移量0 大小1
//b:偏移量1 不是int4个字节的整数倍 偏移量4 大小4
//c:偏移量8 大小1
//d:偏移量9 不是double8个字节的整数倍 偏移量16 大小8
struct Data2{
    char a;
    int b;
    char c;
    double d;
};

int main(){
    cout << sizeof(Data1) << endl;
    Data1 data1;
    printf("%p\n", &data1.a);
    cout << &data1.b << endl;
    printf("%p\n", &data1.c);
    cout << &data1.d << endl;

    cout << sizeof(Data2) << endl;
    Data2 data2;
    printf("%p\n", &data2.a);
    cout << &data2.b << endl;
    printf("%p\n", &data2.c);
    cout << &data2.d << endl;
    
    return 0;
}
```

为什么cout可以打印int、float、double等类型的地址，但是不能打印char类型的地址？

```cpp
#include<iostream>
using namespace std;

int main(){
    int a = 10;
    double b = 1.2;
    char c = 'x';
    cout << &a << endl;
    cout << &b << endl;
    cout << &c << endl;
    return 0;
}
```

![image-20210703095911189](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210703095920.png)

因为<<运算符重载是会将字符类型当做字符串类型处理，输出字符串的地址就是输出字符串的内容

正确的输出字符类型地址

```cpp
printf("%p\n", &c);
cout << static_cast<void *>(&c) << endl;
```



```c
//查看结构体中的偏移量各是多少
#include<stdio.h>
#include<iostream>
using namespace std;
#define offset(T, item) (long)(&(((T *)(NULL)) -> item))
struct Data{//占16个字节
    char a;
    short b;
    char c;
    double d;
};


int main(){
    Data data;
    printf("%p\n", &data.a);
    cout << &data.b << endl;
    printf("%p\n", &data.c);
    cout << &data.d << endl;

    printf("%ld\n", offset(struct Data, a));
    printf("%ld\n", offset(struct Data, b));
    printf("%ld\n", offset(struct Data, c));
    printf("%ld\n", offset(struct Data, d));
    return 0;
}

```



## 3.共用体

![image-20200914144126455](https://gitee.com/xsm970228/images/raw/master/20200914144248.png)

- 具有一片共用的存储空间，值变对于所有的都变
- 取当中所有变量类型最大的内存为整体的内存 当前union占四个字节

![image-20200914144245157](https://gitee.com/xsm970228/images/raw/master/20200914144251.png)

用union写ip转整型

```c
#include<stdio.h>

union IP{
    unsigned int num;
    struct {
        unsigned char a1, a2, a3, a4;
    }ip;
}p;

int main(){
    char str[25];
    int arr[4];
    while(~scanf("%s", str)){
        sscanf(str, "%d.%d.%d.%d", arr, arr + 1, arr + 2, arr + 3);
        p.ip.a1 = arr[0];
        p.ip.a2 = arr[1];
        p.ip.a3 = arr[2];
        p.ip.a4 = arr[3];
        printf("%d\n", p.num);
    }

    return 0;
}
```

![image-20200914145601082](https://gitee.com/xsm970228/images/raw/master/20200914145602.png)

![image-20200914145621135](https://gitee.com/xsm970228/images/raw/master/20200914145628.png)

64位操作系统64是地址编码范围

64位操作系统指针变量8字节，32位占4字节

地址 间接访问

值 直接访问

# 七.其它

## 1.大端机与小端机

计算机读取数据由低位到高位读

大端：数据的高位字节存放在低地址内，数据的低位字节存放在高地址内。

小端：数据的高位字节存放在高地址内，数据的低位字节存放在低地址内。

一个整型是4个字节，如：0x1a2b3c4d。电脑读取内存数据时，是从低位地址到高位地址进行读取（从左到右）。

在小端机器中从低地址到高地址的存放方式为：0x4d,0x3c,0x2b,0x1a；(低地址存低位)

在大端机器中从低地址到高地址的存放方式为：0x1a,0x2b,0x3c,0x4d；（低地址存高位）

```c
#include<iostream>
using namespace std;


int main(){
    int data = 1;
    char *p = (char *)&data;
    if(*p == 1) cout << "小端" << endl;
    if(*p == 0) cout << "大端" << endl;
    return 0;
}

```

- 普通人用的桌面电脑，只要是Intel或AMD的x86/x64架构就一定是小端字节序。
- 此外很多ARM的CPU可以选择数据指令字节序，不过通常也都是运行小端字节序（比如我们的智能手机）
- 网络设备，像PowerPC核心的一些路由器，默认运行大端字节序。

## 2.引用

两个数字实现交换：

![image-20200109113935233](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200109113935233.png)

&a &b为引用（c++）给a， b标号  可以实现交换

## 3.关键字

static 静态

extern 引入外部变量

extern a；表示变量a在其他的文件定义

const  常量   只读  不能修改

字面量  字段存储在字面量中 一个程序涉及很多hello world   这些所有的hello world都在一个字面量中



# 八.oj题/欧拉题

oj118：

![image-20200109113548118](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200109113548118.png)

```c
#include<iostream>
using namespace std;

static char name[][10] = {"rat", "ox", "tiger", "rabbit", "dragon", "snake", 
                          "horse", "sheep", "monkey", "rooster", "dog", "pig"};

int main(){
    int y;
    cin >> y;
    cout << name[((y - 1900) % 12 +12) % 12] << endl;
    return 0;
}
```

oj122: 

![image-20200109113806242](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200109113806242.png)

![image-20200109113904923](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200109113904923.png)

欧拉45

![image-20200914104318927](https://gitee.com/xsm970228/images/raw/master/20200914104633.png)

![image-20200914104326795](https://gitee.com/xsm970228/images/raw/master/20200914104640.png)

优化方案： 二分法的区间用三角形的项数或者用六边形数的值  是六边形数的肯定是三角形数



# 九.算法

## 1.二分查找

折半法查找时间复杂度o（log n）

对于2000000000个数查找 折半查找最多查32次

1. 数组实现

   ```cpp
   #include<iostream>
   using namespace std;
   
   int bs(int *arr, int x, int n){
       int l = 0, r = n - 1;
       while(l <= r){
           int mid = (l + r) >> 1;
           if(arr[mid] == x) return mid;
           else if(arr[mid] < x) l = mid + 1;
           else r = mid - 1;
       }
       return -1;
   }
   
   
   int main(){
       int arr[100], n, x;
       cin >> n;
       for(int i = 0; i < n; i++){
           cin >> arr[i];
       }
       cin >> x;
       cout << bs(arr, x, n) << endl;
       return 0;
   }
   
   ```

2. 递归实现

   ```cpp
   #include<iostream>
   using namespace std;
   
   int bs(int *arr, int head, int tail, int x){
       if(head > tail) return -1;
       int mid = (head + tail) >> 1;
       if(arr[mid] == x) return mid;
       else if(arr[mid] < x) return bs(arr, mid + 1, tail, x);
       else return bs(arr, head, mid - 1, x);
   }
   
   
   int main(){
       int arr[100], n, x;
       cin >> n;
       for(int i = 0; i < n; i++){
           cin >> arr[i];
       }
       cin >> x;
       cout << bs(arr, 0, n - 1, x) << endl;
       return 0;
   }
   
   ```

缺点：

- 必须为有序数组
- 只能返回一个符合条件的数值

变形：

1. 00000000000111111111111找到第一个1

   ```c
   #include<iostream>
   using namespace std;
   
   int bs(int *arr, int n){
       int l = 0, r = n - 1;
       while(l < r){
           int mid = (l + r) >> 1;
           if(arr[mid] == 1) r = mid;
           else l = mid + 1;
       }
       return l == n - 1 ? -1 : l;
   }
   
   
   int main(){
       int arr[100];
       int n;
       cin >> n;
       for(int i = 0; i < n; i++){
           cin >> arr[i];
       }
       cout << bs(arr, n) << endl;
       return 0;
   }
   
   ```

   循环条件l不能等于r  因为有个条件是r = mid 当l == r并且r = mid时会死循环

2. 111111111111111000000000000找到最后一个1

   ```cpp
   #include<iostream>
   using namespace std;
   
   int bs(int *arr, int n){
       int l = -1, r = n - 1;
       while(l < r){
           int mid = (l + r + 1) >> 1;
           if(arr[mid] == 1) l = mid;
           else r = mid - 1;
       }
       return l;
   }
   
   
   int main(){
       int arr[100];
       int n;
       cin >> n;
       for(int i = 0; i < n; i++){
           cin >> arr[i];
       }
       cout << bs(arr, n) << endl;
       return 0;
   }
   
   ```

   mid = (l + r + 1) / 2  

   arr[l] = 1, arr[r] = 0会死循环

## 2.辗转相除法（欧几里得）

欧几里得算法（辗转相除法）

![image-20200915091626925](https://gitee.com/xsm970228/images/raw/master/20200915091635.png)

![image-20200915091654250](https://gitee.com/xsm970228/images/raw/master/20200915091655.png)

gcd(a,b)=gcd(b,a%b)=gcd(a%b,b%a%b)=… 当b为0时 a即位gcd

扩展欧几里得

![image-20200915091704798](https://gitee.com/xsm970228/images/raw/master/20200915091706.png)

[https://blog.csdn.net/moX980/article/details/103440959](https://blog.csdn.net/moX980/article/details/103440959)

 同余方程求解

## 3.数学归纳法（递归本质）

数学归纳法思想

a%b = a – k * b(k=(a/b)下取整)

## 4.素数筛

函数是压缩的数组 数组是函数的展开（数组映射的值已经算好了）

数组可以通过下标（整型）映射到数组位置上的值 比如a[i]=i+1

[https://blog.csdn.net/moX980/article/details/103605733](https://blog.csdn.net/moX980/article/details/103605733)

# 十.项目

## 1.my_printf

```c
#include<stdio.h>
#include<stdarg.h>
#include<string.h>
#include<stdlib.h>
#define PUTC(a) putchar(a), ++cnt

int my_printf(const char *frm, ...){
    va_list arg;
    va_start(arg, frm);
    int cnt = 0;
    for(int i = 0; frm[i]; i++){
        switch(frm[i]){
            case '%':{
                switch(frm[++i]){
                    case 'd':{
                        int x = va_arg(arg, int), temp = 0, digit = 0;
                        if(x < 0) PUTC('-'), x = -x;
                        //789怎么输出？先转成987，然后从低位开始输出
                        do{
                            temp = temp * 10 + x % 10;
                            x /= 10;
                            digit++;
                        }while(x);
                        while(digit--){
                            PUTC(temp % 10 + '0');
                            temp /= 10;
                        }
                    }break;
                    case 'f':{
                        double x = va_arg(arg, double);
                        char str[50] = {0};
                        if(x < 0) PUTC('-'), x = -x;
                        gcvt(x, 10, str);//double2string 精度为10
                        //找到小数点位置并让小数点后有6位
                        int pos = 0;
                        for(; str[pos];pos++){
                            if(str[pos] == '.') break;
                        } 
                        str[pos] = '.';
                        for(int i = pos + 1; i <= pos + 6; i++){
                            if(str[i]) continue;
                            str[i] = '0';
                        }

                        for(int j = 0; str[j]; j++){
                            if(str[j]) PUTC(str[j]);
                            else PUTC('0');
                        }
                    }break;
                    case 'c':{
                        int x = va_arg(arg, int);
                        PUTC(x);
                    }break;
                    case '%':{
                        PUTC(frm[i]);
                    }break;
                }
            }break;

            default: PUTC(frm[i]);
        }
    }
    va_end(arg);
    return cnt;

}

int main(){
    int a = 123;
    printf("hello world\n");
    my_printf("hello world\n");
    int os = printf("a = %d, %d, %f, %f, %f, %c\n", a, 1, 1.236, 2.369, 1.0, 'a');
    int my = my_printf("a = %d, %d, %f, %f, %f, %c\n", a, 1, 1.236, 2.369, 1.0, 'a');
    printf("os = %d, my = %d\n", os, my);
    return 0;
}

```

## 2.google测试框架

gtest单元测试框架

- 单元测试：c语言中单元测试指函数测试 java为类的测试

- gtest

- makefile对于不同的主机环境是不同的，而cmake会根据本机当前的环境生成makefile

  `cmake CMakeList.txt`  之后在make即可

若干个对象文件.o文件打包后就是一个静/动态链接库  放的是函数的定义

函数的声明会放在一组头文件中

任何一个第三方库给我们提供相应的功能时，最好的方法就是给我们一个链接库+一组头文件

静态链接库一般放在lib中 头文件一般放在include中

- 添加静态链接库 -L  `g++ -L ./lib test.cpp -lgtest` 在lib中寻找libgtest.a静态库
- 添加头文件搜索路径 `g++ -I ./include test.cpp` cpp文件中的头文件用<>

最终编译命令`g++ -L ./lib -I ./include test.cpp -lgtest -lpthread`

test.cpp

```cpp
#include<stdio.h>
#include<stdlib.h>
#include<gtest/gtest.h>

int add(int a, int b){
    return a + b;
}

TEST(testFunc, add){
    EXPECT_EQ(add(5, 3), 8);
    ASSERT_EQ(add(5, 3), 9);
    EXPECT_EQ(add(6, 7), 9);
}

int main(int argc, char **argv){
    testing::InitGoogleTest(&argc,argv);
    return RUN_ALL_TESTS();
}

```

makefile内容：

```cpp
all:
	g++ --std=c++11 -I./include -L./lib/ test.cpp -lgtest -lpthread
clean:
	rm ./a.out

```

设计链表结构存储未知个数个函数信息  减少存储空间

![image-20200922154843671](https://gitee.com/xsm970228/images/raw/master/20200922154854.png)

