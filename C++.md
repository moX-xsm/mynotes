# C++

理解程序的运行流程

c++中有一些看不见的运行流程

内存管理https://blog.csdn.net/cherrydremsover/article/details/81627855

c/c++区别（含内存管理）https://blog.csdn.net/cx2479750196/article/details/81129053



1. **栈**：在执行函数时，函数内局部变量的存储单元都可以在栈上创建，函数执行结束时这些存储单元自动被释放。栈内存分配运算内置于处理器的指令集中，效率很高，但是分配的内存容量有限。
2. **堆**：就是那些由`malloc`等分配的内存块，用`free`来结束自己的生命的。
3. **自由存储区**：就是那些由 `new`分配的内存块，他们的释放编译器不去管，由我们的应用程序去控制，一般一个`new`就要对应一个 `delete`。如果程序员没有释放掉，那么在程序结束后，操作系统会自动回收。
4. **全局/静态存储区**：全局变量和静态变量被分配到同一块内存中，在以前的C语言中，全局变量又分为初始化的和未初始化的，在C++里面没有这个区分了，他们共同占用同一块内存区。
5. **常量存储区**：这是一块比较特殊的存储区，他们里面存放的是常量，不允许修改。



内存管理方面的不同（new/delete、malloc/free的区别）：

1. new/delete是操作符，malloc/free是函数。（本身性质不同）
2. new在使用时会调用malloc先开空间在调用构造函数进行初始化，delete会先调用析构函数清理对象，然后在调用free释放空间。（申请内存底层不同是否调用构造函数和析构函数）
3. malloc在堆上开空间，new是在自由存储区（堆或者 静态存储区）开空间。（开辟空间的位置不同）

4. malloc开空间的时候需要指定空间的大小，而new只需要类型名（AA a1=new AA();）（开辟空间大小是否要指定）

5. malloc开辟的空间可以给 单个对象使用，也可以给对象数组使用释放交给free;new 给对象数组开空间使用new[],对应释放使用delete[].（给对象和对象数组开辟空间的不同）

6. malloc申请空间成功返回void\*的指针，失败返回NULL；new 申请对象空间成功后返回对象指针，失败会抛异常（成功返回值不同，失败返回的也不同*）

7. malloc开辟的空间如果不够用可以使用realloc来扩大空间，但是new不可以。（能否扩容）

# 1.从c到c++

![image-20200721195141819](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200721195141819.png)

c++继承了c的所有内容并且有一些扩展

![image-20200721181900264](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200721181900264.png)

#include<stdio.h> 这是c语言风格的头文件

#incliude<cstdio\>  c++语言风格  继承了本来的库 而且还扩展了

## 1.1编程范式

c++明显比c复杂很多  因为编程范式

一定要按编程范式来学

快速上手语言——编程范式

递进关系——开发效率越来越高

- 代码开发
- 测试
- 维护

![image-20200721183354268](https://gitee.com/xsm970228/images/raw/master/image-20200721183354268.png)

## 1.2初识STL

![image-20200721185003312](https://gitee.com/xsm970228/images/raw/master/image-20200721185003312.png)

命名空间标记标识符的姓氏

std——标准命名空间

queue<data_type> q;就是泛型编程  ——将类型抽象  去掉类型限定

![image-20200721185646655](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200721185646655.png)

![image-20200721185723071](https://gitee.com/xsm970228/images/raw/master/image-20200721185723071.png)

strlen时间复杂度为o(n)

s.length()时间复杂度为o(1)   s为字符串对象 里面有存储空间存储字符串长度

![image-20200721190637302](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200721190637302.png)

非标准 不在std命名空间中

将key值映射为下标 在对应位置存val hash_func为映射规则

h[key] = val   相当于数组  key的类型任意

![image-20200721191938429](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200721191938429.png)

数组是展开的函数   函数是压缩的数组

数组是空间   函数是时间   时间换空间

在c++函数数组打破界限 任何类型都行

unordered_map哈希表——非排序映射表——底层实现哈系表

map排序映射表——底层实现红黑树

![image-20200721193014881](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200721193014881.png)



二叉排序树由一个虚拟头节点 无限大  插入元素是 虚拟头节点只会有左子树

![image-20200721203608305](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200721203608305.png)

5后面为0   arr.end() 找的就是0节点

sort底层是快速排序做了一些优化  优化方法：

- 单边递归法
- 无监督式partition

nth_element:快速选择

面试选取第k大的元素：

- 建小顶堆维护前k大的元素 大数据  n大k小  不能一次性放入内存
- n小 k小 快速选择算法 partition   时间复杂度O(n + n/2 + n/4 +…… = 2n)

快速选择算法的时间复杂度：

- 基准值最大最小 O(n<sup>2</sup>)
- 基准值中间值O(nlogn)

代码演示

```cpp
#include<iostream>
#include<queue>

using namespace std;

int main(){
    int op, val;
    queue<int> q;
    while(cin >> op >> val){
        switch(op){
            case 1:  q.push(val); break;
            case 2: q.pop(); break;
            case 3: cout << q.front() << endl; break;
        }
     
        cout << "queue size :" << q.size() << ", empty" << (q.empty() ? "true" : "false") << endl;
        printf("que size: %lu, empty : %s\n", q.size(), q.empty() ? "true" : "false");
        //格式化字符串printf更好用
    }
    return 0;
}

```

```cpp
#include<iostream>
using namespace std;
#include<cstring>

int main(){
    string s1 = "hello", s2 = "world";
    s1 += " ";
    s1 += s2;
    cout << s1 << endl;
    for(unsigned int i = 0; i < s1.length(); i++){
        cout << s1[i] << endl;//[]是一个运算符
    }
    return 0;
}

```

```cpp
#include<iostream>
using namespace std;
#include<map>
int main(){
    /*
    map<double, string> arr;
    arr[1.2] = "hello";
    arr[4.3] = "world";
    arr[-4.5] = "haizei";
    arr[89.7] = "world";
    //map<double, string>::iterator iter = auto iter
    for(auto iter = arr.begin(); iter != arr.end(); iter++){
        cout << iter->first << iter->second << endl;
        //按key值大小排列  说明map底层由红黑树实现
    }
    return 0;
    */

    map<double, string> arr;
    arr[1.2] = "hello";
    arr[4.3] = "world";
    arr[-4.5] = "haizei";
    arr[89.7] = "world";
    double n;
    while(cin >> n){
        //每次查看arr[n] 都会在map中添加一项 底层红黑树 查看多了影响map效率 只能用find方法查看 
        /*
        if(arr[n] != ""){
            cout << "find: " << arr[n] << endl;
        }else{
            cout << "not find" << endl;
        }
        */
        if(arr.find(n) != arr.end()){
            cout << "find: " << arr[n] << endl;
        }else{
            cout << "not find" << endl;
        }
    }
    //map<double, string>::iterator iter = auto iter
    for(auto iter = arr.begin(); iter != arr.end(); iter++){
        cout << iter->first << iter->second << endl;
        //按key值大小排列  说明map底层由红黑树实现
    }
    return 0;

}

```

```cpp
#include<iostream>
using namespace std;
#include<unordered_map>
int main(){
    unordered_map<double, string> arr;
    arr[1.2] = "hello";
    arr[4.3] = "world";
    arr[-4.5] = "haizei";
    arr[89.7] = "world";
    //map<double, string>::iterator iter = auto iter
    for(auto iter = arr.begin(); iter != arr.end(); iter++){
        cout << iter->first << iter->second << endl;
        //顺序不确定  说明unordered_map底层由哈希表实现
    }
    return 0;
}

```

```cpp
#include<iostream>
using namespace std;
#include<algorithm>

bool cmp(int a, int b){
    return a > b; //返回值为a在什么情况下排在b的前面
}

struct CMP_FUNC{
    bool operator()(int a, int b){
        return a > b;
    }
};

int main(){
    int arr[1000], n;
    cin >> n;
    for(int i = 0; i < n; i++) cin >> arr[i];
    //sort(arr, arr + n, CMP_FUNC());//cmp参数为对象  所以加括号
    sort(arr, arr + n, [](int a, int b) -> bool { return a > b; });  
    for(int i = 0; i < n; i++){
        cout << arr[i] << endl;
    }
    nth_element(arr, arr + 1, arr + n);//找到排名第二位的值
    cout << arr[1] << endl;
    return 0;
}

```

```cpp
#include<iostream>
using namespace std;
#include<set>
//set的行为方式类似于堆 当我们用堆时，可以用set表示

struct cmp{
    bool operator()(int a, int b){
        return a > b;
    }
};

int main(){
    //map<int, int>  set<int>集合  本是同根生，低层都是红黑树
    set<int> s;
    //set<int, cmp> s;//建一个由大到小的堆  cmp为类型
    s.insert(345);//插入
    s.insert(65);
    s.insert(9973);
    s.insert(9000);
    cout << s.size() << endl;
    s.insert(65);
    cout << s.size() << endl;//set 去重
    cout << *(s.begin()) << endl;//s.begin()返回地址
    for(auto iter = s.begin(); iter != s.end(); iter++){
        cout << *iter << endl;
    }
    s.erase(s.begin());//传入地址 删除
    cout << *s.begin() << endl;
    return 0;
}

```





微扰 OJ256

![image-20200723194130952](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200723194130952.png)

![image-20200723194833579](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200723194833579.png)

![image-20200723201004203](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200723201004203.png)

```cpp
#include<iostream>
#include<algorithm>
#include<vector>
using namespace std;
#define MAX_N 1000
int a[MAX_N + 5], b[MAX_N + 5], ind[MAX_N + 5]; //第n个大臣的位置
//注意排序！！！ 通过外部数据对自身数据排序
bool cmp(int i, int j) {
    return a[i] * b[i] < a[j] * b[j];
}
//数据结构
struct BigInt : vector<int> {
    BigInt() {}
    BigInt(int x) {
        push_back(x);
        process_digit();
    }
    BigInt(vector<int> &v) : vector<int>(v) {process_digit();}


    BigInt operator/(int x) {
        int y = 0;
        vector<int> ret(size());
        for (int i = size() - 1; i >= 0; i--) {
            y = y * 10 + at(i);
            ret[i] = y / x;
            y %= x;
        }
        return ret;
    }
    void operator*=(int x) {
        for (int i = 0; i < size(); i++) at(i) *= x;
        process_digit();
        return ;
    }
    bool operator>(const BigInt &a) {
        if(size() - a.size()) return size() > a.size();
        for (int i = size() - 1; i >= 0; i--) {
            if (at(i) - a[i]) return at(i) > a[i];
        }
        return false;
    }

    void process_digit() {
        for (int i = 0; i < size(); i++) {
            if (at(i) < 10) continue;
            if (i + 1 == size()) push_back(0);
            at(i + 1) += at(i) / 10;
            at(i) %= 10;
        }
        while (size() > 1 && at(size() - 1) == 0) pop_back();
        return ;
    }
};
ostream &operator<<(ostream &out, const BigInt &v) {
    for (int i = v.size() - 1; i >= 0; --i) {
        out << v[i];
    }
    return out;
}

int main() {
    int n;
    cin >> n;
    for (int i = 0; i <= n; i++) {
        cin >> a[i] >> b[i];
        ind[i] = i;
    }
    //算法
    sort(ind + 1, ind + n + 1, cmp);
    //排序完成后表示排在第i位置的大臣的序号
    BigInt p = a[ind[0]], ans = 0;
    for (int i = 1; i <= n; i++) {
        BigInt temp = p / b[ind[i]];
        if (temp > ans) ans = temp;
        p *= a[ind[i]];
    }
    cout << ans << endl;

    return 0;
}


```

# 2.类和对象（封装）

## 2.1性质



![image-20200725154105058](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200725154105058.png)

类型 = 类型数据+类型操作

存储大小 支持哪些操作

![image-20200725154411994](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200725154411994.png)

类型与变量的关系就是类与对象的关系

数据+操作

类实例出对象

![image-20200725155202430](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200725155202430.png)

没有现成的类 我们可以自己写一个类



## 2.2访问控制权限

![image-20200725155634552](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200725155634552.png)

- public下的属性和方法：对于当前类外部可以访问的
- private：类外的东西都访问不到（只能在类的{}范围内访问）
- protected：主要用在继承
- friend：友元方法可以访问类内部的private的内容

- class默认的访问控制权限为private   
- struct默认public（struct底层实现为class）

标准的程序编写规范需要将访问控制权限类型定义好！！！

当修改private值时会报错！！！

学习c++重点在于把运行时的bug变成编译时的bug（定义好访问控制权限）

c++继承的是c语言的语法 底层实现不一样

```cpp
//私有属性与公有属性
#include<iostream>
using namespace std;

class People{
//public:
    int x, y;
};

struct People2{
    int x, y;
};

int main(){
    People a;
    //struct People2 b;
    //cpp的struct的底层实现就是class
    People2 b;
    a.x = 3;//报错  访问了私有属性
    b.x = 4;
    cout << a.x << endl;
    cout << b.x << endl;//不报错
    return 0;
}

```

```cpp
//修改访问私有属性可以通过定义公有方法来实现
#include<iostream>
using namespace std;
class People{
    int x, y;
public:
    //之不能用初始化列表 只有构造函数才能用初始化列表
    void set(int x){
        cout << "set function: " << this << endl;
        this->x = x;//this指向实例化的对象本身
    }
    void say(){
        cout << x << " " << y << endl;
    }
};

int main(){
    People a;
    cout << "a object : " << &a << endl;
    a.set(3);//通过类内的public函数访问private属性
    a.say();
    return 0;
}

```

```cpp
//友元函数
#include<iostream>
using namespace std;
//类声明
class People{
    friend int main();//这样main函数就可以访问private
    int x, y;
public:
    void set(int x);
    void say();
};

//类内的方法定义  类内声明 类外定义
void People::set(int x){
    cout << "set func: " << this << endl;
    this->x = x;
    return ;
}

void People::say(){
    cout << x << " " << y << endl;
    return ;
}

int main(){
    People a;
    cout << "a object : " << &a << endl;
    a.x = 3;
	cout << a.x << endl;
    return 0;
}

```

## 2.3构造函数与析构函数

![image-20200726180944062](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200726180944062.png)

1. 默认构造函数自动调用People(){}
2. 有参构造 声明对象时加上参数 只有一个参数的时候又叫转换构造函数
3. 拷贝构造
4. 析构函数 ～

作用：构造函数开辟资源 析构函数释放资源

### 默认构造函数

```cpp
//作用：构造函数开辟资源 析构函数释放资源
#include<iostream>
using namespace std;

class People{
public:
    People(){
        arr = new int[10];
        cout << "default constructor" << endl;
    }

    ~People(){
        delete arr;
        cout << "disconstructor" << endl;
    }
    int *arr;

};

int main(){
    People a;//自动调用默认构造函数  在作用域最后调用析构函数
    printf("hello world\n");
    a.arr[9] = 18;
    return 0;
}

```

### 有参构造函数

```cpp
#include<iostream>
using namespace std;

class People{
    public:
    People(){}//加一个默认构造函数 
    //当定义了其他构造函数时，默认构造函数就被删除了
    //传入一个参数的有参构造也叫转换构造函数
    People(string name){
        cout << "string param constructor" << endl;
        this->name = name;
    }
    People(int x){
        cout << "int param constructor" << endl;
        this->x = x;
    }
    ~People(){
        cout << "disconstructor" << endl;
    }
    string name;
    int x;

};

int main(){
    People a;
    People b("hug");//自动匹配有参构造函数
    People c = string("xsm");//单纯的"xsm"会被认为成char[]
    People d = 678;
    People e(576);
    cout << e.x << endl;
    cout << b.name << endl;
    e = 999;//调用的是operator=
    //先创建了一个999的临时对象
    //e = People(999)；上面语句的等价 
    cout << e.x << endl;
    return 0;
}

```

### 引用&

引用时不发生数据的拷贝 速度快  还不需要开辟单独的空间

```c
void increase(int &a){
    a += 1;
    return ;
}
int main(){
    int y = 7;
    increase(y);
    cout << y << endl;//a还是7（没有引用时）  实参与形参
    //对引用的修改就作用在传入的参数  这样不需要开辟独立的空间
	return 0;
}
```

![image-20200726205504857](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200726205504857.png)

### 拷贝构造函数

copy constructor为什么一定要&？？？

![image-20200726185143601](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200726185143601.png)

People a = b;时会调用copy constructor 如果没有引用

那么copy constructor时会有个People(People a)拷贝行为 （形参copy实参）又有了People a（形参） = b；（实参） 从而递归死循环，所以copy constructor必须使用&引用



当c=a；赋值时，调用的不是copy constructor  而是c++默认的赋值函数（重载=）

```cpp
//赋值函数与copy constructor
#include<iostream>
using namespace std;

class People{
    public:
    People(){
        cout << "constructor" << endl;
    }
    People(string name){
        cout << "string param constructor" << endl;
        this->name = name;
    }
    People(int x){
        cout << "int param constructor" << endl;
        this->x = x;
    }
    People(const People &a){
        cout << "copy constructor" << endl;
        this->name = a.name;
    }
    //赋值函数
    void operator=(const People &a){
        cout << "===" << endl;
        this->name = a.name;
    }
    ~People(){
        cout << "disconstructor" << endl;
    }
    string name;
    int x;

};
int add(People a, People b){
    return a.x + b.x;
}
int main(){
    cout << add(4, 5) << endl;//!!!!用到了转换构造函数  两次
    People a;
    People c("xsm");
    //copy constructor
    People f = a;//调用copy constructor   （我们不写也会有默认的 因为类型一致）
    cout << f.name << endl;
    c = a;//把a赋值给c  没有调用copy constructor  通过赋值函数实现
    return 0;
}

```

```cpp
//default与delete
class People{
    public:
    //People(){}//加一个默认构造函数 
    //上面那行=
    People() = default;
    //People() = delete;//将隐藏的规则表现为显示的规则
    People(string name){
        this->name = name;
    }
    People(int x){
        cout << "int param constructor" << endl;
        this->x = x;
    }
    People(const People &a){
        cout << "copy constructor" << endl;
        this->name = a.name;
    }
    //赋值函数
    void operator=(const People &a){
        cout << "===" << endl;
        this->name = a.name;
    }
    ~People(){
        cout << "disconstructor" << endl;
    }
    string name;
    int x;

};


```

当我们写了一个类时，c++提供的默认的

- 默认构造函数(当自己写了其他构造函数时就没有了)
- 默认copy constructor（类型一致）
- 默认赋值运算符
- 默认的析构函数
- 默认的地址运算符

### explicit/implicit用法

explicit:[https://zhuanlan.zhihu.com/p/159212695](https://zhuanlan.zhihu.com/p/159212695)

explicit关键字用于修饰构造函数，使得构造函数只能显式调用。explicit**禁止编译器执行非预期（往往也不被期望）的类型转换**

未加explicit的构造函数都是implicit的！！

**隐式转换的构造函数**

首先介绍隐式调用构造函数的例子：

```cpp
#include <iostream>
using namespace std;

class Solution {
public:
    Solution(int i) {
        cout << "构造函数：int" << endl;
    };
};

void Fun(Solution s) {}

int main() {
    Solution s = 1;
    Fun(1);
}
```

相应的输出：

```console
构造函数：int
构造函数：int
```

在主函数中，`Solution s = 1;Fun(1);`，隐式调用了构造函数。

**使用explicit**

通过在构造函数前加入`explicit`关键字，可以指明该构造函数必须显式调用。

```cpp
#include <iostream>
using namespace std;

class Solution {
public:
    explicit Solution(int i) {
        cout << "构造函数：int" << endl;
    };
};

void Fun(Solution s) {}

int main() {
    Solution s = 1; // 编译报错，不存在从int到Solution的适当构造函数
    Fun(1); // 编译报错
}
```

**什么时候用**

**构造函数只有0个或1个非默认参数，即：`A();A(int a);A(int a,int b = 0);`时，建议使用。**

除非自己知道要使用`A a = 1;`等形式的写法，其他情况能加就加。

对于一些可加可不加的，如下面的写法，虽然比较常见，不过个人还是建议能加就加。

```cpp
#include <iostream>
using namespace std;

class MyString {
public:
    MyString(const char* c) {};
};

class MyVector {
public:
    MyVector(std::initializer_list<int> l) {}
};

int main() {
    MyString s = "abcde";
    MyVector v = { 1,2,3,4,5 };
}
```

上面的情况当构造函数前加上explicit时，会报错！！

## 2.4static类属性与方法

- 对象（成员）属性 每个对象都有自己的属性

- 对象（成员）方法不仅能访问自己的方法，还都能访问到this这个成员指针（特殊）

- 类属性：整个类的属性 有一个对象就多一个对象属性  而类的属性全局唯一 所有成员共享 不需要实例化对象就可以访问

  ![image-20200726193704407](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200726193704407.png)

  加一个static就是定义一个类属性！！！

  eg：统计这一类的对象有多少个？ 用total_num来统计  构造+1 析构-1

- 类方法：static方法访问不到this

- 注意static方法不能操作成员属性 因为static方法访问不到this指针 所以找不到对应的成员属性

- 构造函数不能是static的  static访问不到this指针！！！！而构造函数需要对成员属性进行操作 所以不行！！

```cpp
#include<iostream>
using namespace std;
class Point{
public:
    Point(){
        cout << "constructor" << endl;
        Point::total_cnt += 1;
    }
    Point(const Point &a) : Point(){
        cout << "copy constructor" << endl;
        this->x = a.x;
        this->y = a.y;

    }
    Point(double z) : Point(){
        cout << "double param construct" << endl;
        this->z = z;
    }
    Point(int x, int y) : Point(){
		cout << "int,int parm constructor" << endl;
        this->x = x;
        this->y = y;
    }
    void operator=(const Point &a){
        cout << "======" << endl;
        this->x = a.x;
        this->y = a.y;
        return ;
    }
    static int T() {return Point::total_cnt; }
    ~Point(){
        cout << "desstructor" << endl;
        Point::total_cnt -= 1;
    }

private:
    int x, y;
    double z;
    static int total_cnt;
    //类属性需要类内定义，在类外面赋值 
    //c++禁止在类内初始化非const 的类属性 
};

int Point::total_cnt = 0;

void test(){
    Point a;
    cout << Point::T() << endl;
    return ;
}

int main(){
    Point a;
    cout << a.T() << endl;
    //cout << Point::T() << endl;//两者等价
    test();
    Point b;
    cout << b.T() <<endl;
    //输出122  test()内部有一个析构
    Point c(3, 4);
    cout << c.T() << endl;
    Point d(3.4);
    cout << c.T() << endl;
    d = 5.2;//先将5.2转换成匿名对象（单浮点构造） 再用赋值 最后将匿名对象析构
    cout << c.T() << endl;
    a = d;//没有调用copy constructor  直接赋值
    cout << a.T() <<endl;
    return 0;
    //构造压栈 析构时弹栈
    //后产生的信息依赖于之前的信息 所以要先释放后面的信息最后释放最先的信息
}

```

```cpp
#include <iostream>
#include <cstdio>
#include <cstdlib>
#include <queue>
#include <stack>
#include <algorithm>
#include <string>
#include <map>
#include <set>
#include <vector>
using namespace std;

class Point {
public :
    Point() {
        cout << "constructor : " << this << endl;
        Point::total_cnt += 1;
    }
    Point(const Point &a) : Point() {
        cout << "copy constructor : " << this << endl;
        this->x = a.x;
        this->y = a.y;
    }
    Point(double z) : Point() {
        cout << "convert constructor : " << this << endl;
        this->x = 99, this->y = 99;
    }
    Point(int x, int y) : Point() {
        cout << "param constructor : " << this << endl;
        this->x = x;
        this->y = y;
    }

    void operator=(const Point &a) {
        cout << "operator= : " << this << endl;
        this->x = a.x, this->y = a.y;
        return ;
    }
    static int T() { return Point::total_cnt; }

    ~Point() {
        cout << "destructor : " << this << endl;
        Point::total_cnt -= 1;
    }
    
private:
    int x, y;
    static int total_cnt;
};

int Point::total_cnt = 0;

void test() {
    Point a;
    cout << Point::T() << endl;
    return ;
}

int main() {
    Point a;
    cout << a.T() << endl;
    test();
    Point b;
    cout << b.T() << endl;
    Point c(3, 4);
    cout << c.T() << endl;
    Point d(3.4);
    cout << d.T() << endl;
    c = 5.6;
    cout << c.T() << endl;
    cout << &a << endl;
    cout << &b << endl;
    cout << &c << endl;
    cout << &d << endl;
    return 0;
}
//析构顺序 d c b a
```

**c++能不能给类的成员变量在声明的时候初始化？**：[https://blog.csdn.net/liyunxin_c_language/article/details/83188185](https://blog.csdn.net/liyunxin_c_language/article/details/83188185)

## 2.5const方法

![image-20200726194053357](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200726194053357.png)

string &name() const; 不能去修改对象中任何的属性值

const类型的对象 只能调用const 类型的方法

```cpp
#include<iostream>
using namespace std;
class Point{
public:
    Point(){
        cout << "constructor" << endl;
        Point::total_cnt += 1;
    }
    Point(const Point &a) : Point(){
        cout << "copy" << endl;
        this->x = a.x;
        this->y = a.y;

    }
    Point(double z) : Point(){
        cout << "convert" << endl;
        this->z = z;
    }
    Point(int x, int y) : Point(){
        //Point::total_cnt += 1;
        this->x = x;
        this->y = y;
    }
    void operator=(const Point &a){
        cout << "======" << endl;
        this->x = a.x;
        this->y = a.y;
        return ;
    }
    void set(int x, int y){
        this->x = x;
        this->y = y;
    }
    
    //const方法内部不能对本身的属性作出改变 eg：计算调用seek的次数
    void seek() const{
        seek_cnt += 1;//const内部想要改变的数值类型前加mutable
        cout << x << " " << y << endl;
    }
    int S() const {return seek_cnt;}
    static int T() {return Point::total_cnt; }
    ~Point(){
        cout << "desstructor" << endl;
        Point::total_cnt -= 1;
    }

private:
    int x, y;
    double z;
    mutable int seek_cnt = 0;
    static int total_cnt;
};

int Point::total_cnt = 0;


int main(){
    Point c(3, 4);
    c.seek();
    const Point e(6, 7);//const意味着Point中的值都不能改
    e.seek();//程序报错 seek方法不是const方法
    cout << e.S() << endl//输出e对象调用seek的次数
    cout << e.T() << endl;//const对象可以调用static函数
    return 0;
}

```

##  2.6返回值优化RVO

![image-20200726210038943](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200726210038943.png)

一个对象的出现：

1. 开辟数据存储区
2. 初始化 匹配不同的构造函数
3. 完成构造

![image-20200726210154412](https://gitee.com/xsm970228/images/raw/master/image-20200726210154412.png)

![image-20200726210312512](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200726210312512.png)

发生了两次copy

![image-20200726210607565](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200726210607565.png)

![image-20200726210928241](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200726210928241.png)

第一次优化 消除匿名变量

第二次优化直接将temp_a看成a的别名

![image-20200726210950634](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200726210950634.png)

在各种编辑器中，已经进行了默认的优化

- GNU：无copy行为
- 微软：1次copy

`g++ -fno-elide-constructors *.cpp` 编译时将返回值优化去掉

```cpp
//返回值优化
#include<iostream>
using namespace std;

class People{
public:
    People(string name){
        cout << "param constructor" << endl;
        this->name = name;
    }
    People(const People &a){
        cout << "copy constructor" << endl;
        this->name = a.name;
    }
private:
    string name;
};

People func(){
    //返回原理 给this指针传入a
    //People temp_a(&a, "hug");
    People temp_a("hug");
    return temp_a;
}

int main(){
    People a = func();
    return 0;
}

```

## 2.7运算符重载

![image-20200728181724756](https://gitee.com/xsm970228/images/raw/master/image-20200728181724756.png)

cin >>    cout << 方法不应该是cin()  cout()吗？ >>  <<  运算符

运算符重载可以类内  可以类外

重新的定义运算符的功能

### 类内重载

```c
#include<iostream>
using namespace std;

namespace haizei{
class istream{
    public:
    //注意引用  减少不必要的copy
    void operator>>(int &n){
        std::cin >> n;
        return ;
    }
    private:
};
class ostream{
    public:
    void operator<<(int &n){
        std::cout << n;
        return;
    }
    private:
};

istream cin;
ostream cout;
};

int main(){
    int n;
    haizei::cin >> n;//haizei::cin.operator>>(n);  重载运算符里的数据类型就看这个n
    haizei::cout << n;
    return 0;
}
```

```cpp
#include<iostream>
using namespace std;

namespace haizei{
class istream{
    public:
    //注意引用  引用不需要copy
    istream &operator>>(int &n){
        std::cin >> n;
        return *this;
    }
    private:
};
class ostream{
    public:
    ostream &operator<<(int &n){
        std::cout << n;
        return *this; //返回当前的对象  一定要用引用 不需要copy
    }
    ostream &operator<<(const char *msg){
        std::cout << msg;
        return *this;
    }
    private:
};

istream cin;
ostream cout;
};

//类外重载
//传入<< 左右的参数
haizei::ostream &operator<<(haizei::ostream &out, double &z){
    std::cout << z;
    return out;
}

int main(){
    int n, m;
    haizei::cin >> n >> m;// >> 返回值为void  之后void >> m; 发生错误
    //cin >> n 执行完成后返回当前的对象cin  执行下一步cin>>m 引用避免不必要的copy
    haizei::cout << n << " " << m;
    double k = 5.6;
    haizei::cout << k << "\n";
    
    return 0;
}

```

使用引用可以减少copy！！！！（形参与实参）

某种类型的运算符重载的返回值类型是可以自由设定的

- haizei::cin 这是haizei命名空间中的对象
- haizei::istream  这是haizei命名空间中的类

重载运算符不能改变传入参数个数（单目双目等）和优先级

### 类外重载

```cpp
//加const  cout  输出的是字面量  加const是将a绑定在常量身上  
//如果不加const绑定在变量身上 如果在方法内部改变a的值？？？？  不能改字面量！！！！
//实质右值不能绑定在左值引用上
std::ostream &operator+(std::ostream &out, const int &a){
    std::cout << a;
    return out;
}
    cout + 8 + 9 + 10 ;
    //运算符重载不改变优先级！！！
    (((((cout + 8) << " ")+ 9) << " ") + 10);

```

### 特殊运算符重载

```cpp
// + +=
#include<iostream>
using namespace std;

class Point{
    public:
    //: 与{}自己中间为初始化列表
    Point(): __x(0), __y(0){}
    Point(int x, int y) : __x(x), __y(y){}
    int x() const {return __x;}
    int y() const {return __y;}
    //返回的是一个新的点 不能用引用
    Point operator+(const Point &a){
        return Point(x() + a.x(), y() + a.y());
    }
    //为什么void类型不行 (c += b)++ void不行！！！
    Point &operator+=(const Point &a){
        this->__x += a.x();
        this->__y += a.y();
        return *this;
    }
    private:
    int __x, __y;
};
//const对象只能访问const方法
ostream &operator<<(ostream &out, const Point &a){
    out << "Point(" << a.x() << "," << a.y() <<")";
    return out;
}



int main(){
    Point a(5, 6), b(3, 4), c(1, 1);
    cout << a << " " << b << endl;
    cout << a + b << endl;    
    c += b;
    cout << c << endl;
    return 0;
}

```



```cpp
//前++  后++
#include<iostream>
using namespace std;

class Point{
    public:
    //: 与{}自己中间为初始化列表
    Point(): __x(0), __y(0){}
    Point(int x, int y) : __x(x), __y(y){}
    int x() const {return __x;}
    int y() const {return __y;}
    Point operator+(const Point &a){
        return Point(x() + a.x(), y() + a.y());
    }
    //为什么void类型不行 (c += b)++ void不行！！！
    Point &operator+=(const Point &a){
        this->__x += a.x();
        this->__y += a.y();
        return *this;
    }
    /*
    Point &operator++(){
        this->__x += 1;
        this->__y += 1;
        return *this;
    }
    //有int的是后++  int没用
    Point &operator++(int){
        this->__x += 1;
        this->__y += 1;
        return *this;
    }*/
    Point &operator++(){
        this->__x += 1;
        this->__y += 1;
        return *this;
    }
    
    //这里不能返回引用！！！  返回的不是其本身！！
    Point operator++(int){
        /*
        int temp_x = this->__x;
        int temp_y = this->__y;*/
        Point temp(*this);
        this->__x += 1;
        this->__y += 1;
        return temp;
    }
    private:
    int __x, __y;
};
//const对象只能访问const方法
ostream &operator<<(ostream &out, const Point &a){
    out << "Point(" << a.x() << "," << a.y() <<")";
    return out;
}



int main(){
    Point a(5, 6), b(3, 4), c(1, 1);
    cout << a << " " << b << endl;
    cout << a + b << endl;    
    ++(c += b);
    c++;
    cout << c << endl;
    cout << c++ << endl;
    cout << ++c << endl;
    return 0;
}

```

- 重载（）得到函数对象
- 重载[]得到数组对象
- 重载->得到指针对象

```cpp
//()  []  ->   *  
#include<iostream>
using namespace std;

class A{
    public:
    int x, y;
};
class B{
    public:
    B() : obj(nullptr){
        arr = new int[10];
        arr[3] = 9973;
    }
    //委托构造函数 不能跟初始化列表同时使用
    B(A *obj) : B(){
        this->obj = obj;
    }
    //引用&  可以修改值   如果不引用 只读
    int &operator[](int ind){
        return arr[ind];
    }
    //重载（）
    int operator()(int a, int b){
        return a + b;
    }
    void operator[](const char *msg){
        cout << msg << endl;
        return ;
    }
    //重载->  得到指针对象  ps：记住->是单目运算符
    A *operator->(){
        return obj;
    }
    //重载*运算符
    A &operator*(){
        return *obj;
    }
    ~B(){
        delete arr;
    }
    private:
    int *arr;
    A *obj;

};

ostream &operator<<(ostream &out, const A &a){
    out << a.x << " | " << a.y << endl;
    return out;
}

int main(){
    B add;//add 函数对象  长的像函数的对象 通过（）运算符重载实现 
    //我们用到的map unordermap等都是函数对象
    cout << add(3, 4) << endl;
    cout << add[3] << endl;//数组对象 通过对[]重载得到
    add[3] = 986;
    cout << add[3] << endl;
    add["hello world"];
    A a;
    a.x = 67, a.y = 99;
    B p = &a;
    cout << p->x << " " << p->y << endl;//指针对象 重载->获得
    cout << *p << endl;
    return 0;
}

```

有4类运算符只能在类内重载

- ()
- []
- -> 间接引用
- =

不能重载的运算符

- .
- 成员指针引用运算符  .*
- sizeof
- ？ ：
- ：：

程序底层无变量 直接对地址进行操作 所以上述对地址操作的不能重载

## 2.8深copy与浅copy

默认的copy函数为浅copy函数 

默认的浅copy函数只是两对象对应变量的值相等，如果碰到指针变量，两个指针变量就会指向同一个地址

深copy多了开辟自身空间的一步

![image-20210313134200611](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210313134200611.png)

对象占用的空间存储的都是对象的属性

想要深copy就应该自己写一个copy方法

```cpp
#include<iostream>
using namespace std;

class A{
    public:
    A(){
        arr = new int[10];
    }
    A(const A &a) : A(){
        for(int i = 0; i < 10; i++){
            this->arr[i] = a.arr[i];
        }
        this->x = a.x;
        this->y = a.y;
    }
    int x, y;
    int *arr;
};

int main(){
    A a, b(a);
    cout << b.x << " " << b.y << endl;
    b.x = 6;
    cout << a.x << " " << a.y << endl;
    cout << b.x << " " << b.y << endl;
    a.arr[3] = 9973;
    b.arr[3] = 8864;
    cout << a.arr[3] << endl;
    cout << b.arr[3] << endl;//默认的copy函数为浅copy函数 a.arr[3] = b.arr[3] 
    //ab中的arr指向同一片数据区  
    //对象占用的空间存储的都是对象的属性
    //想要深copy就应该自己写一个copy方法
    return 0;
}

```



作业一：

5中cpp中stl容器  现有的类型  整理相关的类型都重载了什么运算符

eg : string  +  []

按照自己的理解实现一个string类

https://www.cnblogs.com/zxouxuewei/p/6681926.html

```cpp
#include<cstring>
#include<iostream>
using namespace std;

namespace haizei{
class string{
    public:
    string(){
        this->__buff_size = 10;
        this->buff = new char[this->__buff_size];
        this->__length = 0;
    }
    string(const char *str){
        this->__buff_size = strlen(str) + 1;
        this->buff = new char[this->__buff_size];
        strcpy(this->buff, str);
        this->__length = this->__buff_size - 1;
    }
    char &at(int ind){
        if(ind < 0 || ind >= __length){
            cout << "string error : out of range" << endl;
            return __end;
        }
        return this->operator[](ind);
    }
    char &operator [] (int ind){
        return buff[ind];
    }
    const char *c_str() const {
        return buff;
    }
    string operator + (const string &s){
        int size = this->__length + s.__length + 1;
        char *temp = new char[size];
        strcpy(temp, this->buff);
        strcat(temp, s.buff);
        return temp;
    }
    int size(){return this->__length;}
    private:
    int __length, __buff_size;
    char *buff;
    char __end;

};
}

ostream &operator << (ostream &out, const haizei::string &s){
    out << s.c_str();
    return out;
}

int main(){
    haizei::string s = "hello world", s2 = ", haizei", s3 = " harbin.";
    //cin >> s1;
    cout << s << endl;
    s[3] = '6';
    cout << s << endl;
    cout << s + s2 + s3 << endl;
    for(int i = 0; i < s.size(); i++){
        cout << s[i] << endl;
    }
    return 0;
}
```



# 3.继承



![image-20200728182535581](https://gitee.com/xsm970228/images/raw/master/image-20200801154504700.png)

![image-20200728182535581](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200728182535581.png)

继承 发生在类与类之间

![image-20200728182923801](https://gitee.com/xsm970228/images/raw/master/image-20200728182923801.png)

animal中所有的属性和方法都会被包含在cat类

__name属于animal 私有成员  cat不能直接访问到name

- cat： 子类 派生类
- animal：父类  基类

为什么子类需要继承所有的父类的东西？（private访问不到为什么继承）

功能的有效性取决于数据的完整性！！！ 包括父类的所有属性和方法

## 3.1继承权限

class Cat : public Animal  中的public是继承权限

![image-20200728183358090](https://gitee.com/xsm970228/images/raw/master/image-20200728183358090.png)

不管继承权限是什么 子类只能访问父类中的public和protected的属性和方法

继承权限影响的是外部对于子类内部继承自父类的属性和方法的访问权限

继承得到的属性和方法的访问权限只能更低  不能更高

不影响当前子类对父类方法的访问

![image-20200728183551199](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200728183551199.png)

- 继承权限是public时，继承过来的方法和属性的访问权限不变
- 继承权限是protected时，继承过来的方法和属性都是protected
- 继承权限是private时，继承过来的方法和属性都是private



## 3.2子类与父类构造函数的顺序

声明一个子类的对象 先调用父类的构造函数初始化复制与父类的属性 在调用自己的构造函数初始化自己的东西

子类对象由两部分构成  前半部分为父类对象！！（）后半部分为子类特有的属性，虽然这样子类对象有了父类所有的东西，包括private属性，但是这不是自己的private属性！！！通过子类内的方法还是访问不了父类继承过来的一部分私有属性！！

![image-20200728190041041](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200728190041041.png)



```cpp
#include<iostream>
#include<string>
using namespace std;

class Animal{
    public:
    Animal(string name, int age) : __name(name), age(age){}
    void say(){
        cout << "my name is :" << __name << ",my age is :" << age << endl;
    }
    protected:
    string __name;
    private:
    int age;//成员属性的声明！！ 编译器先处理声明部分  没有对象 就没有age 有了对象才会有age
};

class Cat : public Animal{
    public:
    Cat()=delete;
    Cat(string name, int age) : Animal(name, age){}
};

class Bat : protected Animal{
    public:
    Bat() = delete;
    Bat(string name, int age) : Animal(name, age){}
    void say(){
        this->Animal::say();//成员方法都需要绑定在某个对象中！！加不加this都相当于加this
        cout << " class Bat : " << __name << endl;//正常 __name子类访问父类时是protected
        //cout << "age " << age << endl; //error  age私有
    }
};

int main(){
    //Cat a;当Animal中无默认构造函数时报错  Cat a时先调用父类的默认构造
    Cat a("kitty", 5);
    a.say();
    Bat b("hug", 16384);
    //b.say();//error Animal继承权限是protected，外部访问继承于父类的属性和方法的权限是protected
    b.say();
    return 0;
}


```

子类和父类都有say方法  子类对象调用的是子类的say方法  想要调用父类的加父类名和作用域::

## 3.3初始化列表

只有构造函数有初始化列表

初始化列表调用的都是构造函数

![image-20200728194858023](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200728194858023.png)



构造函数的头部 () : 初始化类表{构造逻辑}   初始化列表分为隐性和显性

执行初始化列表时  先初始化父类的东西 调用父类的构造函数 再初始化子类的属性

初始化列表执行后就相当于对象构造完成了  当进入到构造逻辑了 当前对象的属性和方法都可以使用 意味着进入构造逻辑之前整个对象构造完了

调用构造函数时：子类调用 父类调用 父类完成  子类完成



```cpp
#include<iostream>
using namespace std;
class D{
    public:
    D(){cout << "D constructor" << endl;}
    ~D(){cout << "D destructor" << endl;}
};
class A{
    public:
    A() {cout << "A constructor" << endl;}
    ~A() {cout << "A desstructor" << endl;}
};
class B{
    public:
    B(){cout << "B constructor" << endl;}
    ~B(){cout << "B desstructor" << endl;}
};
class C : public D{
    public:
    //下面3个一样 初始化列表只能定义初始化方式 不能定义初始化的顺序 构造顺序由声明顺序决定的
    //C() : a(), b() {cout << "C constructor" << endl;}
    //C() : b(), a() {cout << "C constructor" << endl;}
    C(){cout << "C constructor" << endl;}
    ~C(){cout << "c destructor" << endl;}
    private:
    A a;
    B b;
};
int main(){
    C c;
    return 0;
}
//顺序D A B C 父类构造先完成 在定义自身的变量 a b  最后c类结束
//析构顺序 子类先析构 父类析构
```

**初始化列表的作用**

[https://blog.csdn.net/lws123253/article/details/80368047](https://blog.csdn.net/lws123253/article/details/80368047)

构造过程：

1. 初始化阶段：所有类型的成员都会先进行初始化再进入构造函数的执行逻辑 即使没出现在初始化列表中
2. 计算阶段：通常指赋值阶段

使用原因：

> 初始化成员属性有两种方法：初始化列表和构造函数体内的赋值
>
> 对于内置类型int、float等类型来说初始化初始化列表和构造函数体内的赋值速度是一样的
>
> 但对于自定义的类类型来说，使用初始化列表少调用了一次构造函数，直接copy构造，比先默认构造再赋值操作快

必须使用初始化列表的类型：

1. 常量const不能赋值
2. 引用的类型不能赋值
3. 没有默认构造函数的类类型

成员属性初始化顺序：

- 按照定义的顺序来初始化，并非初始化列表中的顺序



## 3.4菱形继承

![image-20200728204058732](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200728204058732.png)

无法确定继承的run是哪一个  每种编译器处理方式不同

避免菱形继承！！

- 尽量避免多重继承
- 避免不了多重继承改成一个实体类，多个接口类的形式

```cpp
//菱形继承
#include<iostream>
using namespace std;

struct A{
    int x;
};
struct B : public A {
    void set(int x){
        cout << "set" << &this->x << endl;
        this->x = x;
    }
};
struct C : public A {
    int get(){
        cout << "get" << &this->x << endl;
        return this->x;
    }
};
struct D : public B, public C{};

int main(){
    D d;
    d.set(9973);
    cout << d.get() << endl;//不会输出9973
    //d继承BC  有两个x
	//cout << d.x << endl;//error 歧义
    return 0;
}
```

![image-20200801152113875](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200801152113875.png)

指代不明

解决：虚拟继承virtual 当发现两个继承是virtual继承时， 而且指向的都是同一个类 底层会自动合并为一个  但是D类的内存占的更大（为了维护virtual继承） 

不同的编译器实现virtual继承的方式不同 所以尽量少的多重继承

java  c# 不支持多重继承

```cpp
//菱形继承
#include<iostream>
using namespace std;

struct A{
    int x;
};
struct B : virtual A {
    void set(int x){
        cout << "set" << &this->x << endl;
        this->x = x;
    }
};
struct C : virtual A {
    int get(){
        cout << "get" << &this->x << endl;
        return this->x;
    }
};
struct D : public B, public C{};

int main(){
    D d;
    d.set(9973);
    cout << d.get() << endl;
	cout << d.x << endl;

    return 0;
}

```

![image-20200801154356153](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200801154356153.png)



**虚继承的目的是让某个类做出声明，承诺愿意共享它的基类**。其中，这个被共享的基类就称为虚基类（Virtual Base Class），本例中的 A 就是一个虚基类。在这种机制下，不论虚基类在继承体系中出现了多少次，在派生类中都只包含一份虚基类的成员。

现在让我们重新梳理一下本例的继承关系，如下图所示：

![菱形继承和虚继承](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210808101353.png)




观察这个新的继承体系，我们会发现虚继承的一个不太直观的特征：必须在虚派生的真实需求出现前就已经完成虚派生的操作。在上图中，当定义 D 类时才出现了对虚派生的需求，但是如果 B 类和 C 类不是从 A 类虚派生得到的，那么 D 类还是会保留 A 类的两份成员。

换个角度讲，**虚派生只影响从指定了虚基类的派生类中进一步派生出来的类，它不会影响派生类本身**。

在实际开发中，位于中间层次的基类将其继承声明为虚继承一般不会带来什么问题。通常情况下，使用虚继承的类层次是由一个人或者一个项目组一次性设计完成的。对于一个独立开发的类来说，很少需要基类中的某一个类是虚基类，况且新类的开发者也无法改变已经存在的类体系。

C++标准库中的 iostream 类就是一个虚继承的实际应用案例。iostream 从 istream 和 ostream 直接继承而来，而 istream 和 ostream 又都继承自一个共同的名为 base_ios 的类，是典型的菱形继承。此时 istream 和 ostream 必须采用虚继承，否则将导致 iostream 类中保留两份 base_ios 类的成员。



![虚继承在C++标准库中的实际应用](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210808101356.png)



## 3.5copy构造函数&赋值运算符继承

![image-20200728204423999](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200728204423999.png)



![image-20200728205147817](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200728205147817.png)

引用是一个说明形式

只有在声明时会用到int &b； 其余情况是&取地址符

```cpp
#include<iostream>
using namespace std;

class A{
    public:
    A(){
        cout << "class A constr" << endl;
        this->x = 0x01020304;
    }
    A(const A &a){cout << "class A copyconstr" << this <<endl;}
    int x;
};
class B : public A{
    public:
    B(){
        this->y = 0x05060708;
        cout << "class B constr" << endl;
    }
    B(const B &b) : A(b){cout << "class B copyconstr" << this << endl;}//不要忘记对父类A的初始化
    //隐式的调用调用的是默认的构造
    //看逻辑 当需要的父类初始化在默认构造函数时用默认的 
    //当子类中的父类需要copy构造时 初始化表一定要显示 不然调用默认构造 没有把父类的东西copy一份
    //A(b) b是B类 而A(const A &a) 需要的是a的引用 传b的&可以吗？
    //注意继承 B是一个A （猫是一个动物！！） 反过来不行 所以b对象可以看成是一个a对象
    //两个this指针一样的！！！为什么？？？
    //对于一个子类的存储最开始的部分一定是父类的东西，这样把b对象当成a对象时，最开始的指针就是指向父类的东西
    int y;
};

int main(){
    B b1;
    B b2(b1);
    const char *msg = (const char *)(&b1);
    for(int i = 0; i < sizeof(B); i++){
        printf("%X", msg[i]);
    }
    //输出43218765说明b1最开始的存储区存储的父类a的属性
    printf("\n");
    return 0;
}

```





# 4.多态

![image-20200801205550287](https://gitee.com/xsm970228/images/raw/master/image-20200801205550287.png)

## 4.1普通成员方法

![image-20200801154853186](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200801154853186.png)

![image-20200801155752956](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200801155752956.png)

虽然abc都指向猫对象，但是只有a调用的Cat的run方法
bc都是Animal类！！调用的父类的run

```cpp
#include<iostream>
using namespace std;

class Animal{
    public:
    void run(){
        cout << "i dont know how run" << endl;
    }
};

class Cat : public Animal{
    public:
    void run(){
        cout << "cat run" << endl;
    }
};

int main(){
    Cat a;
    Animal &b = a;
    Animal *c = &a;
    a.run();
    b.run();
    c->run();
    //虽然abc都指向猫对象，但是只有a调用的Cat的run方法
    //bc都是Animal类！！调用的父类的run
    return 0;
}

```

普通的成员方法跟着类走

- Cat a   Cat类 调用Cat::run
- Animal &b  Animal类 调用Animal::run
- Animal *c   Animal类 调用Animal::run

普通的成员继承在逻辑上上是说不通的！！！

## 4.2虚函数virtual

![image-20200801155841303](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200801155841303.png)

override 覆盖

虚函数这种成员方法跟着对象走

```cpp
#include<iostream>
using namespace std;

class Animal{
    public:
    virtual void run(){
        cout << "i dont know how run" << endl;
    }
};

class Cat : public Animal{
    public:
    void run(){
        cout << "cat run" << endl;
    }
};

int main(){
    Cat a;
    Animal &b = a;
    Animal *c = &a;
    a.run();
    b.run();
    c->run();
    return 0;
}

```

abc指向的都是猫类对象 所以调用的都是Cat::run

override不影响功能实现   能把运行时的bug暴露在编译时  

c++让编译器让我们找bug

```cpp
#include<iostream>
using namespace std;

class Animal{
    public:
    virtual void runI(){
        cout << "i dont know how run" << endl;
    }
};

class Cat : public Animal{
    public:
    //函数名打错了可以用override检查出来
    void runl() override {
        cout << "cat run" << endl;
    }
};

int main(){
    Cat a;
    Animal &b = a;
    Animal *c = &a;
    a.runI();
    b.runI();
    c->runI();
    return 0;
}

```

override能够检查是否覆盖的virtual函数

- 编译器状态：编译时就可以确定的值 n=10；
- 运行时状态：运行时才能确定的值  scanf("%d", &n);

- 普通成员方法在编译时就能确定调用的是是哪个类的方法
- 虚函数不能 虚函数得确定这个对象是什么类对象 运行时状态

运行时状态需要记录额外的信息

调用方式一样 但是表现不一样 可能需要用虚函数   e

```cpp
#include<iostream>
using namespace std;

class Animal{
    public:
    virtual void run(){
        cout << "i dont know how run" << endl;
    }
};

class Cat : public Animal{
    public:
    void run() override {
        cout << "cat run" << endl;
    }
};

class Dog : public Animal{
    public:
    void run() override {
        cout << "dog run" << endl;
    }
};


class Bat : public Animal{
    public:
    void run() override {
        cout << "bat fly" << endl;
    }
};

int main(){
    Cat a;
    Animal &b = a;
    Animal *c = &a;
    a.run();
    b.run();
    c->run();
    Animal *e[10];
    for(int i = 0; i < 10; i++){
        int op = rand() % 3;
        switch(op){
            case 0: e[i] = new Cat(); break;
            case 1: e[i] = new Dog(); break;
            case 2: e[i] = new Bat(); break;
        }
    }
    for(int i = 0; i < 10; i++){
        e[i]->run();
    }
    //虽然e是Animal类的数组 但其调用的方法看其指向的对象
    return 0;
}

```

虚函数会使内存扩大

## 4.3virtual实现原理

为什么虚函数跟着对象走，普通的成员函数跟着类走？

空对象至少占一个字节->虚函数 变为8字节（一个地址的大小）

增加了一个地址  地址指向了虚函数表  虚函数表存储虚函数的指针

![image-20200801163912679](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200801163912679.png)

```cpp
class A{
    public:
    int x;  //输出4
    //void run(){}  //还是4
    virtual void run(){} //占用16个字节 struct以占用最多的变量类型对齐
    virtual void run1(){} //还是占用16个字节 只是函数表中多加了一个run1函数地址
};
cout << sizeof(A) << endl;

```

虚函数一个和多个对于对象的内存没有影响  存储的是虚函数表的地址  只是虚函数表中虚函数地址增加



![image-20210316100820143](F:\学习\Typora\image-20210316100820143.png)

同一类的对象的虚函数表指针是一样的

```cpp
#include<iostream>
using namespace std;


class Animal{
public:
    int x, y;
    virtual void say(){cout << "Animal" << endl;}
    virtual void run(){}

};
class Cat : public Animal{
public:
    void say() override{cout << "Cat" << endl;}
    void run() override{}
};

void output_addr_obj(unsigned char *p, int n){
    for(int i = 0; i < n; i++){
        printf("%02X ", p[i]);
    }
    printf("\n");
    return ;
}

int main(){
    Cat a, b;
    Animal c;
    
    //a,b的前8个字节是一样的 打印的是内容 前八个字节是虚函数指针 一样的
    ////c和ab的虚函数表指针是不一样的 这是因为Cat类对继承过来的虚函数进行了重写
    output_addr_obj((unsigned char *)&a, sizeof(a));
    output_addr_obj((unsigned char *)&b, sizeof(b));
    output_addr_obj((unsigned char *)&c, sizeof(c));
    //虽然虚函数指针是改变了 但是子类对象还是可以通过作用域的方法访问到父类方法 这种索引靠的不是虚函数表 应该是直接访问的函数地址
    a.say();
    a.Animal::say();
    
    cout << sizeof(Animal) << endl;
    cout << sizeof(Cat) << endl;
    return 0;
}
```

c++成员方法不占对象的大小 那是怎么调用到成员方法的？为什么成员方法可以不占用对象的大小就可以被调用到？

https://blog.csdn.net/fuzhongmin05/article/details/59112081/

当实例化一个对象后，只有属性占用对象的存储空间

成员方法放在代码段，不管实例化多少个对象，成员方法的地址都是一样的，共享的

编译之后实际上成员函数是不存储在类内的，同一类的成员共享成员函数，通过函数指针调用，所以调用会隐含带着一个this指针用于区分是哪个对象调用的

![image-20210316131615876](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210316131615876.png)

![image-20210316131640402](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20210316131640402.png)

objdump -s -d a.out 对二进制文件进行反汇编

## 4.4this

虚函数表中每个位置存放的是虚函数的地址func，所以虚函数表是func *型（存放func的数组） 在对象的存储空间 第一个存放的是虚函数表的地址func *型  要取出这个地址  对象的首地址看成func **型

![image-20200801165634233](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200801165634233.png)

```cpp
#include<iostream>
using namespace std;

class A{
    public:
    A() = default;
    int x;
    virtual void say(int xx){
        cout << "this = " << this << endl;
        cout << "Class A : I can say, xx = " << xx << endl;
    }
};
typedef void (*func)(void *, int);
//func是指向void ****(int)的一个地址   *func就是函数
int main(){
    A temp_a;
    cout << "&temp_a = "<< &temp_a << endl;
    ((func **)(&temp_a))[0][0](&temp_a, 6);
    //((func **)(&temp_a))[0][0](6);
    //((func **)(&temp_a))[0] 取出虚函数表的地址  再添[0]取出第一个虚函数
    //但是运行效果没有xx的值，为什么 明明传的6
    //那么在say方法中打印一下this的值
    //可以看到this的值是6   为什么呢？ 我们传给虚函数say的参数传给了this
    //我们想一下为什么成员方法可以访问到this呢？this是一个啥？关键字还是别的？
    //this不是关键字 是一个参数 是成员方法传进去的第一个参数 默认情况下 编译器会给我们自动的添加
    //也就是void say(this,int xx)  但是因为上面我们通过底层地址调用这个函数 所以不能自动添加
    //所以6就传给了this  也是因为这样 成员方法可以访问到这个特殊的变量this
    //为了实现预想的效果，我们把this也传进去 typedef也得修改
    //这样 我们实现了预期的效果
    //当然，这样的话this我们就可以随便赋值了
    return 0;
}

```

![image-20200801170359452](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200801170359452.png)

![image-20200801170903194](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200801170903194.png)

this  和 python中的self一样  只是c++不需要手动添加

现在 我们来试想一下返回值优化是怎么做到的？

```cpp
People func(){
    People temp_a("hug");
    //People temp_a(&a, "hug");
    return temp_a;
}

int main(){
    People a = func();
    return 0;
}
```

就是在temp_a构造的时候将第一个参数this的值赋值成&a就行了 就可以避免这两次copy

## 4.5纯虚函数

虚函数：子类和父类都有

纯虚函数不需要子类和父类都有，父类有即可

加上=0

![image-20200801183226166](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200801183226166.png)

拥有纯虚函数的类不完整，不能实例化对象  功能未定义

抽象类/接口类=有纯虚函数的类

继承了抽象类的子类必须实现对应的父类中未定义的方法

![image-20200801184240121](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200801184240121.png)

具有虚函数之间的继承关系也叫多态  

static，类方法，只能跟着类走

![image-20200801184410466](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200801184410466.png)

例如USB  对外表现形式一样（usb接口）内部实现不一样（鼠标 键盘等）

析构函数是普通成员方法 当析构时，看类标识符

在拥有继承的关系中 父类的析构函数一定得是虚函数 不然可能会造成内存泄露

```cpp
class Base{
    public:
    Base(){
        cout << "base constr" << endl;
    }
    ~Base(){
        cout << "base desconstr" << endl;
    }
};

class Base_A : public Base{
    public:
    Base_A(){
        cout << "base_A constr" << endl;
    }
    ~Base_A(){
        cout << "base_A desconstr" << endl;
    }
};
    Base *ba = new Base_A();//父类构造先完成 子类构造后完成
    delete ba;//delete ba时需要调用析构函数 析构函数是普通的成员函数 看ba的类 ba类为Base  只会调用父类虚构    造成内存泄露

//在继承关系的父类中的析构函数一定是虚函数！！！


```

实现一个可以自定义哈希函数的哈系表类

```cpp
#include <iostream>
#include <cstdio>
#include <cstdlib>
#include <queue>
#include <stack>
#include <algorithm>
#include <string>
#include <map>
#include <set>
#include <vector>
using namespace std;

namespace haizei {
//接口类 继承的子类重载（）成函数对象
class IHashFunc {
public :
    virtual int operator()(int) = 0;
};
//hashtable声明
class HashTable {
    typedef int (*HashFunc_T)(int);
    typedef pair<int, int> PII;
public :
    HashTable(HashFunc_T);
    HashTable(IHashFunc &);
    int &operator[](int);

private:
    HashTable(HashFunc_T, IHashFunc *, int);
    int hash_type;
    HashFunc_T func1;
    IHashFunc *func2;

    int __size;
    PII **data;
};

HashTable::HashTable(HashFunc_T func1, IHashFunc *func2, int hash_type) 
: func1(func1), func2(func2), hash_type(hash_type) {
    this->__size = 1000;
    this->data = new PII*[this->__size];
    for (int i = 0; i < this->__size; i++) this->data[i] = nullptr;
}

HashTable::HashTable(HashFunc_T func) 
: HashTable(func, nullptr, 1) {}

HashTable::HashTable(IHashFunc &func) 
: HashTable(nullptr, &func, 2) {}


int &HashTable::operator[](int x) {
    int hash = 0;
    switch (hash_type) {
        case 1: hash = func1(x); break;
        case 2: hash = (*func2)(x); break;
    }
    if (hash < 0) hash &= 0x7fffffff;
    int ind = hash % __size, t = 1;
    while (data[ind] && data[ind]->first != x) {
        ind += t * t;
        if (ind < 0) ind = ind & 0x7fffffff;
        ind %= __size;
        t += 1;
    }
    if (data[ind] == nullptr) data[ind] = new PII(x, 0);
    return data[ind]->second;
}

} // end of namespace haizei
//函数
int hash1(int x) {
    return (x << 1) ^ (x << 3) ^ (x >> 5);
}
//函数对象
class MyHashFunc : public haizei::IHashFunc {
public :
    int operator()(int x) override {
        return (x << 1) ^ (x << 3) ^ (x >> 5);
    }
};

int main() {
    MyHashFunc hash2;
    haizei::HashTable h1(hash1);
    haizei::HashTable h2(hash2);
    h1[123] = 345;
    h2[123] = 678;
    cout << h1[123] << endl;
    cout << h2[123] << endl;
    cout << h1[789] << endl;
    cout << h2[1000000] << endl;
    return 0;
}

```

同样的类的多个对象指向的虚函数表只有一个 头八个字节是一样的

同类的多个对象的虚函数是一样的

虚函数表在继承中变化的样子

![image-20200801202116503](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200801202116503.png)

B继承A但是重写了say方法 C继承B重写了run方法

## 4.6 dynamic_cast转换方法

子类地址转化为父类地址直接使用即可（猫是动物），那么父类地址怎么转换为子类地址？

转换方法  父类地址转换为子类地址  dynamic_cast

```cpp
#include<iostream>
#include<string>
using namespace std;

class A{
    public:
    virtual ~A(){}
    private:

};
class B : public A{
    public:
    void sayB(){
        cout << "class B, x = " << x << endl;
    }
    int x;
};
class C : public A{
    public:
    void sayC(){
        cout << "class C, x = " << x << endl;
    }
    double x;

};
class D : public A{
    public:
    void sayD(){
        cout << "class D, x = " << x << endl;
    }
    string x;
};


int main(){
    srand(time(0));
    A *pa;
    B *pb;
    C *pc;
    D *pd;
    switch(rand() % 3){
        case 0: pb = new B(); pa = pb; pb->x = 123; break;
        case 1: pc = new C(); pa = pc; pc->x = 45.6; break;
        case 2: pd = new D(); pa = pd; pd->x = "hello world"; break;
    }
    //if pa 指向的不是一个B类对象 转换失败 返回nullptr
    if((pb = dynamic_cast<B *>(pa))){
        cout << "class B : ";
        pb->sayB();
    }else if((pc = dynamic_cast<C *>(pa))){
        cout << "class C : ";
        pc->sayC();
    }else if((pd = dynamic_cast<D *>(pa))){
        cout << "class D : ";
        pd->sayD();
    }
    //如果A类没有虚函数 那么会提示A类不是多态 需要把A类变成多态
    return 0;
}

```

dynamic_cast时提示父类不是多态时，父类的析构函数加virtual

为什么转换时一定要求父类是多态的？

因为dynamic_cast转换时判断的是对象内容的前八个字节，即虚函数表的地址 如果不是多态就没有这个表地址 所以不行

![image-20200801204555821](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200801204555821.png)

```cpp
#include<iostream>
#include<string>
using namespace std;

class A{
    public:
    virtual ~A(){}
    private:

};
class B : public A{
    public:
    void sayB(){
        cout << "class B, x = " << x << endl;
    }
    int x;
};
class C : public A{
    public:
    void sayC(){
        cout << "class C, x = " << x << endl;
    }
    double x;

};
class D : public A{
    public:
    void sayD(){
        cout << "class D, x = " << x << endl;
    }
    string x;
};
//自己实现一个简易的dynamic_cast
int my_dynamic_cast(A *ta){
    char **pa = (char **)(ta);//pa 指针数组 每一个位置放一个指针
    char **pb = (char **)(new B());
    char **pc = (char **) (new C());
    char **pd = (char **) (new D());
    int ret = -1;
    if(pa[0] == pb[0]) ret = 0;
    else if(pa[0] == pc[0]) ret = 1;
    else if(pa[0] == pd[0]) ret = 2;
    return ret;
}

int main(){
    srand(time(0));
    A *pa;
    B *pb;
    C *pc;
    D *pd;
    switch(rand() % 3){
        case 0: pb = new B(); pa = pb; pb->x = 123; break;
        case 1: pc = new C(); pa = pc; pc->x = 45.6; break;
        case 2: pd = new D(); pa = pd; pd->x = "hello world"; break;
    }
    //if pa 指向的不是一个B类对象 转换失败 返回nullptr
    if((pb = dynamic_cast<B *>(pa))){
        cout << "class B : ";
        pb->sayB();
    }else if((pc = dynamic_cast<C *>(pa))){
        cout << "class C : ";
        pc->sayC();
    }else if((pd = dynamic_cast<D *>(pa))){
        cout << "class D : ";
        pd->sayD();
    }
    switch(my_dynamic_cast(pa)){
        case 0: ((B *)(pa))->sayB(); break;
        case 1: ((C *)(pa))->sayC(); break;
        case 2: ((D *)(pa))->sayD(); break;
    }
    return 0;
}

```

子类对象继承父类对象

子类对象前一部分存储的是父类的东西，但是前8个字节会被更换为指向子类虚函数表的地址

# 5.总结与思考

## 5.1auto关键字

c语言本身就有auto关键字  用auto关键字修饰的变量是自动变量

能够被自动释放  但是局部变量本身就能被自动释放   所以就很鸡肋

c++中auto自动推导变量类型 省略类型 **推导编译阶段可以确定的类型**

标准c++11

- auto不能作用于函数的参数推导
- auto不能进行模板参数的推导
- auto不能定义数组
- auto不能用于非静态成员变量(声明阶段不能推导变量类型)

```cpp
#include<iostream>
using namespace std;
#include<map>

class A{
public:
    auto const static x = 123;
};

int main(){
    auto a = 123;//自动推理变量类型 编译期就能确定
    cout << sizeof(a) << endl;
    map<int, int> arr;
    arr[112112] = 223212;
    arr[2121] = 22;
    arr[12] = 55645;
    //auto iter = arr.begin() 与下面的语句一样
    for(map<int, int>::iterator iter = arr.begin(); iter != arr.end(); iter++){
        cout << iter->first << " " << iter->second << endl;
    }

    //c++11新引入的
    for(auto x : arr){
        cout << x.first << " " << x.second << endl;
    }
    return 0;
}

```

## 5.2constexpr关键字

修饰编译期常量

声明常量表达式

在编译期就能确定变量值 兼具const属性 一旦定义 不能修改

在c++11之前 由一个boost库 用来丰富加快执行效率

const是运行期常量

```cpp

#include<iostream>
using namespace std;

//c++11不支持递归函数为constexpr
constexpr int f(int x){
    if(x == 1) return 1;
    return x * f(x - 1);
}
class A{
    public:
    constexpr A(int x, int y) :  x(x), y(y) {}
    int x, y;
};
int main(){
    const int n = 123;//n不能修改 当对n修改时报错 const感觉是编译期的功能
    cout << n << endl;
    int a;
    cin >> a;
    const int m = 2 * a;//这里没报错 说明const是运行时的状态
    cout << m << endl;
    //constexpr int x = 2 * a;//这里会报错 constexpr 定义的是严格的编译期常量
    constexpr int x = 2 * 12;//编译期常量能够确定编译期常量
    constexpr int y = f(12);//报错！！ f返回值不是编译期常量 需要在函数前加constexpr 函数可以当正常函数使用

    constexpr A c(2, 3);//为啥类就报错 调用了构造函数 构造函数不是编译期常量 加constexpr即可
    return 0;
}

```

- 定义编译期常量
- 在函数前加constexpr指这个函数的返回值可以是编译器常量，这个函数仍可以当作正常函数使用
- 定义一个constexpr的类时，调用的构造函数前也需要加constexpr

## 5.3final关键字

用来做代码检查

- 防止子类对于父类相关方法的重写
- 防止final类被其他类继承

```cpp
#include<iostream>
using namespace std;
#include<map>
//自己开发的类 可以继承任何现在已知的类 除了final类
class A : public map<int, int>{
    public:
    virtual void say(){
        cout << "Class A : hello world" << endl;
    }
};
//class B不允许任何类继承
//用于单立模式
class B final : public A{
    public:
    //final指重写的say函数是最后一个版本 防止子类对于父类相关方法的重写
    /*
    void say() final override{
        cout << "Class B : hello world" << endl;
    }
    */
};

class C: public B{
    public:
    void say() override{
        cout << "class C : hello world" << endl;
    }
};

int main(){
    A a;
    a[25] = 25;
    a[9973] = 2123;

    return 0;
}

```

## 5.4nullptr关键字

c++中用来替换空指针的

c的NULL 与c++的nullptr

- NULL:(void *)0   本质是int型 的0  但是我们当成地址 0值的强制转化 可能造成编译期的混淆
- nullptr:本质上就是严格的地址

当c++中我们需要用到地址时，用nullptr

```cpp
#include<iostream>
using namespace std;

int f(int x){
    cout << "output value: ";
    cout << x << endl;
    return 0;
}
int f(int *x){
    cout << "output address: ";
    cout << x << endl;
    return 0;
}


int main(){
    //cout << nullptr << endl;//报错
    printf("%lld\n", (long long)nullptr);//0值
    cout << NULL << endl;//0值
    int n = 123, *p = &n;
    f(n);
    f(p);
    f(nullptr);
    //f(NULL);//程序报错 NULL 本质是int 0  当成地址   nullptr本来就是地址 严格意义上的空地址
    //报错是因为编译器不能分辨现在传的是0还是地址

    return 0;
}

```

## 5.5 override关键字

## 5.6左/右值

让c++重回神坛的c++11

![image-20200802202235611](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200802202235611.png)

以上是4个概念  不是两个概念

用同样的变量 单一的方式 访问到同样的结果就叫左值  否则就是右值

右值表临时，是一个临时值 123+456用来给a赋值，用完之后就没用了 曾经临时存在过

- 左值可以放在=左边和右边
- 右值只能放在=右边

一般情况下放在=右边的双目运算符返回的是右值

```cpp
#include<iostream>
using namespace std;

int main(){
    int x;
    x = 123 + 456;//=左边x是左值 =右边123+456是右值？ 比较肤浅的理解
    //用同样的变量 单一的方式 访问到同样的结果就叫左值  否则就是右值
    //6行之后可以用x访问到579 左值    而123不是左值是右值
    
    int a, b = 1, c = 3;
    a = b + c;//b，c是左值  b + c表达式产生的临时值是右值
    a++;//返回临时值 是一个右值
    ++a;//返回的是和a一样的值 是个左值
    (++a) = b + c;//左值可以放在=左边   a++不行
    int n;
    (++n) = b + c;
    cout << n << endl; //输出4   ++n-->n  ++n返回的是n的引用
    (a = b) = c;//=表达式返回是左值 a = b 返回a  再a = c
    cout << a << " " << b << endl;
    int y[10] = {0};
    y[1] = 10;//[]可以放在=左边 所以重载是要引用
    return 0;
}

```

左值优先绑定在左值引用&

右值优先绑定在右值引用&&

```cpp
#include<iostream>
using namespace std;

#define TEST(a) {\
    cout << #a << ": ";\
    f(a);\
}
//左值引用 &
void f(int &x){
    cout << "left" << endl;
}
//右值引用 &&
void f(int &&x){
    cout << "right" << endl;
}

int main(){
    
    int a, b = 1, c = 3;
    a = b + c;//b，c是左值  b + c表达式产生的临时值是右值
    TEST(a += 3);
    TEST(1 + 4);
    TEST(b + c);
    TEST(a++);
    TEST(++a);
    return 0;
}

```

```cpp
#include<iostream>
using namespace std;

#define TEST(a, f) {\
    cout << #a << ": " << #f << " ";\
    f(a);\
}

void f2(int &x){
    cout << "left" << endl;
}

void f2(int &&x){
    cout << "right" << endl;
}
void f(int &x){
    //x是左值引用
    //x是左值，在f函数内部
    cout << "left" << endl;
    TEST(x, f2);
}
void f(int &&x){
    //x是右值引用
    //但是x是左值！！！
    cout << "right" << endl;
    //TEST(x, f2);
    TEST(forward<int &&>(x), f2); //向前按照固定类型传递
    //TEST(move(x), f2);//按右值传递
}

int main(){  
    int a, b = 1, c = 3;
    a = b + c;
    TEST(a += 3, f);
    TEST(1 + 4, f);
    TEST(b + c, f);
    TEST(a++, f);
    TEST(++a, f);
    //转了一次之后不管是左值引用还是右值引用 都成了左值 不行！！
    //可以使用forward move
    return 0;
}

```

- TEST(forward<int &&>(x), f2); //向前按照固定类型传递
- TEST(move(x), f2);//按右值传递

move和forward是为了解决c++11中的完美传值问题 左（右）值往下传一直是左（右）值

注意左右值和operator重载的联系！！！左值一般需要传引用 右值不是引用

- +返回值为右值  不传引用
- []返回值为左值  传引用

加入左值和右值区分了处理流程 极大的加速了运行效率

## 5.7move constructor(左右值)

将右值绑定在左值引用上

![image-20200806093030371](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200806093030371.png)

`test_func(123);`     可以把右值绑定在常量的左值引用上

![image-20200806093414224](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200806093414224.png)

`test_func(a);`  能将左值绑定在常量右值引用上？不能  本来左值就不能绑定在右值引用上，加上const更不能   右值表临时 会被销毁

关闭返回值优化！！！！`g++ -fno-elide-constructors  *.cpp`

```cpp
#include<iostream>
#include<cstring>
using namespace  std;


namespace haizei{
class string{
    public:
    string(){
        cout << "constr" << endl;
        this->__buff_size = 10;
        this->buff = new char[this->__buff_size];
        this->__length = 0;
    }
    string(const char *str){
        cout << "string param constr" << endl;
        this->__buff_size = strlen(str) + 1;
        this->buff = new char[this->__buff_size];
        strcpy(this->buff, str);
        this->__length = this->__buff_size - 1;
    }
    //常量的左值引用  左值和右值都可以绑定
    //普通copy构造  O(n) 
    string(const string &s){
        cout << "copy constr" << endl;
        this->__buff_size = s.__buff_size;
        this->__length = s.__length;
        this->buff = new char[this->__buff_size] ;
        strcpy(this->buff, s.c_str());       
    }
    //移动构造o(1)
    //绑定右值
    string(string &&s){
        cout << " move constr" <<endl;
        this->__buff_size = s.__buff_size;
        this->__length = s.__length;
        this->buff = s.buff;
        s.buff = nullptr;
    }
    char &at(int ind){
        if(ind < 0 || ind >= __length){
            cout << "string error : out of range" << endl;
            return __end;
        }
        return this->operator[](ind);
    }
    char &operator [] (int ind){
        return buff[ind];
    }
    const char *c_str() const {
        return buff;
    }

    string operator + (const string &s){
        cout << "++++++++" << endl;
        int size = this->__length + s.__length + 1;
        char *temp = new char[size];
        strcpy(temp, this->buff);
        strcat(temp, s.buff);
        return temp;
    }
    
    int size(){return this->__length;}
    ~string(){
        cout << "desconstr" << endl;
        if(this->buff) delete this->buff;
    }
    private:
    int __length, __buff_size;
    char *buff;
    char __end;

};
}

ostream &operator << (ostream &out, const haizei::string &s){
    out << s.c_str();
    return out;
}

int main(){
    haizei::string s = "hello world", s2 = ", haizei", s3 = " harbin.";
    cout << "s4 begin================" << endl; 
    haizei::string s4 = s + s2 + s3; //++++     string param constr   copy  ++++++++++   string param const  copy   copy
    //s + s2 调用一个string param constructor生成一个匿名对象 s+s2生成一个右值 因为没有RVO 还需将匿名对象copy给s + s2的另一个匿名对象
    //就相当于执行了string st = s + s2 s + s2通过string param constructor构造一个匿名对象，在copy给st
    //string st2 = st + s3 先string param constructor 再copy
    //最后string s4 = st2 copy一次
    //copy的匿名对象是右值 表临时  匿名变量是右值 优先绑定在右值引用上move constr O(1)
    //cin >> s1;
    cout << "s4 end================" << endl;
    haizei::string s5 = s4;
    cout << s4 << endl;
    cout << s5 << endl;
    s4[3] = '=';
    cout << s4 << endl;
    cout << s5 << endl;
    //s4  s5 打印的还是同一个有=的    浅拷贝  因为没有实现深copy
    cout << s << endl;
    s[3] = '6';
    cout << s << endl;
    cout << s + s2 + s3 << endl;
    for(int i = 0; i < s.size(); i++){
        cout << s[i] << endl;
    }
    return 0;
}

```

![image-20200806104307056](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200806104307056.png)

![image-20200806104247903](https://gitee.com/xsm970228/images/raw/master/image-20200806104247903.png)

38号存储区先构造  copy给a0后接着析构  

38号存储区的作用：脱了裤子放屁   没用

构造的38号存储区的对象是个右值，表示临时变量

将其绑定在右值引用（move constr）上，提升copy效率

```cpp
    //常量的左值引用  左值和右值都可以绑定
    //普通copy构造  O(n) 
    string(const string &s){
        cout << "copy constr" << endl;
        this->__buff_size = s.__buff_size;
        this->__length = s.__length;
        this->buff = new char[this->__buff_size] ;
        strcpy(this->buff, s.c_str());       
    }
    //移动构造o(1)
    //绑定右值
	//s右值 是个临时变量 用完了就没用了 所以可以指向nullptr
    string(string &&s){
        cout << " move constr" <<endl;
        this->__buff_size = s.__buff_size;
        this->__length = s.__length;
        this->buff = s.buff;
        s.buff = nullptr;
    }
```

move constr之后：虽然只是将copy换成move copy 但是时间复杂度O(n)->O(1)

![image-20200806105159330](https://gitee.com/xsm970228/images/raw/master/image-20200806105159330.png)

stl模板库里的copy是深copy   将左值引用和右值引用分开处理 优化流程结构 加速

# 6.模板

## 6.1基本性质

![image-20200806112129039](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200806112129039.png)

任意类型？？？

模板：将任意类型从程序设计中抽象出来 

函数：将程序设计过程中具体的数据（3，4，5）抽象出来  add(3,4)  add(5, 6)

模板抽象的数据的类型

函数进行了一次抽象（具体数据）  模板抽象的是两次 （函数 ->模板）

![image-20200806113016965](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200806113016965.png)



|              | 范型编程       |
| ------------ | -------------- |
| 面向过程编程 | 用模板实现函数 |
| 面向对象编程 | 用模板实现类   |

map<int, int>当要使用时才定义数据类型

实现一个模板=实现一组函数

![image-20200806113358249](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200806113358249.png)

T类型就相当于任意数据

抽象过程

3+4--->函数--->模板

![image-20200806115012758](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200806115012758.png)

调用模板时才会确定类型

## 6.2模板函数

decltype()推导表达式返回值类型 注意两个类型不能直接相加  得调用构造函数实例出一个对象

```cpp
#include<iostream>
using namespace std;
#include<map>

//template<class>  没有区别
//decltype()推导表达式返回值类型
/*
template<typename T, typename U>
T add(T a, U b){
    return a + b;
}*/

template<typename T, typename U>
decltype(T() + U()) add(T a, U b){
    return a + b;
}


int main(){
    map<int, int> m;//显式的模板参数的实例化
    cout << add(2, 3) << endl;//隐式的推导模板类型
    cout << add(2.3, 4.3) << endl;
    //cout << add(2, 3.3) << endl;//报错 T冲突
    cout << add<double>(2, 3.3) << endl;//有点麻烦 隐式的能实现这种推导吗？给一个类型参数也可以
    cout << add(3.5, 2) << endl;
    return 0;
}
 

```

以上的程序存在的问题：当类T和U没有默认构造函数时会报错

解决：返回值后置->

```cpp

#include<iostream>
using namespace std;
#include<map>


class A{
    public:
    A(int x) : x(x) {}
    int x;
};

class B{
    public:
    B(int y) : y(y) {}
    int y;
};

int operator + (const A &a, const B &b){
    return a.x + b.y;
}
/*
//template<class>  没有区别
//decltype()推导表达式返回值类型
template<typename T, typename U>
decltype(T() + U()) add(T a, U b){
    return a + b;
}
*/

//返回值后置 就直到了a和b对象
template<typename T, typename U>
auto add(T a, U b) -> decltype(a + b){
    return a + b;
}

int main(){
    cout << add<double>(2, 3.3) << endl;//有点麻烦 隐式的能实现这种推导吗？给一个类型参数也可以
    cout << add(3.5, 2) << endl
    cout << add(string("hello"), "hello world") << endl;
    A a(1000);
    B b(652);
    cout << a + b << endl;
    cout << add(a, b) << endl;//报错 ab没有默认构造  decltype(T() + U())默认T U有默认构造
    cout << max(4, 3) << endl;
    //cout << max(4.3, 3) << endl;//报错 默认类型一样  不是完美的
    return 0;
}

```

- 宏 预编译期

- 模板 编译期


typeof--->decltype类比着看

```cpp
namespace haizei{
//同时使用max min  只需要重载<即可
// <  -->  >  -->  == -->  !=
//三目运算符不能重载 所以max min必须可以使用三目运算符

template<typename T, typename U>
auto  max(T a, U b) -> decltype(a < b ? b : a){
    return a < b ? b : a;
}
    
template<typename T, typename U>
auto  min(T a, U b) -> decltype(a < b ? a : b){
    return a < b ? a : b;
}
};


```



## 6.3模板类

```cpp
#include<iostream>
using namespace std;
/*
//这个类是模板类
template<typename T>
class PrintAny{
    public:
    void operator()(const T &a){
        cout << a << endl;
    }
};
*/

//普通类中有一个模板参数
class PrintAny{
    public:
    template<typename T>
    void operator()(const T &a){
        cout << a << endl;
    }
};

int main(){
    //想打印什么类型 得先实例化一个对应的类 复杂
    //PrintAny<int> print1;//必须带int 显式
    //PrintAny<string> print2;
    PrintAny print;
    print(123);
    print(25.6);
    print("hello world");
    return 0;
}

```

模板实例化产生特定的类/函数

模板类--->普通类带模板函数

## 6.4模板在编译过程中的问题

模板实例化产生特定的类/函数

```cpp
    print(123);
    print(25.6);
    print("hello world");
```

printany{

1. operator()(int)
2. operator()(double)
3. operator()(const char *)

}



`nm -C  +目标文件 `将编译好的文件转化为人工可读

T开头：本文件内部的

U开头：外部的东西 通过包含头文件得到

![image-20200806152626918](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200806152626918.png)

目标文件已经不存在模板了 说明任何目标文件中都没有模板了  需要的是具体函数的定义  模板是用来生成具体函数的 

模板不能放在其他源文件中通过连接来实现（编译时展开成函数）  模板的定义一般在头文件中

宏作用在模板之前  模板涉及类型推导 不作用在预编译 作用在编译期间

template放在头文件中，可能会重定义？

c++在链接时会将模板函数生成的同样的函数定义合并，所以不会重定义



![image-20200806154439066](https://gitee.com/xsm970228/images/raw/master/image-20200806154439066.png)

即使最后的可执行程序也是有外部链接的 这就说明了在我电脑上能执行的可执行文件放在其他电脑上不好用，因为这个可执行文件链接的库是我电脑上的

电脑和电脑之间环境是不一样的

## 6.5模板的特化与偏特化

当模板遇到特定的情况需要做的特定的处理

处理某些特定的情况

- 偏特化：模板的一部分情况  template<***>
- 特化：模板的一种情况 tempalte<>

偏特化和特化分别可以没那么明显

```cpp
//返回值后置 就直到了a和b对象
template<typename T, typename U>
auto add(T a, U b) -> decltype(a + b){
    return a + b;
}

//模板的特化 比函数多一个template<>
//对于两个int传进来的话 会匹配这个
template<>
int add(int a, int b){
    cout << " add int :" << endl;
    return a + b;
}
```

```cpp
template<typename T>
class FoolPrintAny{
    public:
    void operator()(const T &a){
        cout << a << endl;
    }
};
//模板类的特化
template<>
class FoolPrintAny<int>{
    public:
    void operator()(const int &a){
        cout << "naughty :";
        cout << a << endl;
    }
};

```

```cpp
//返回值后置 就直到了a和b对象
template<typename T, typename U>
auto add(T a, U b) -> decltype(a + b){
    return a + b;
}

//模板的特化 比函数多一个template<>
//对于两个int传进来的话 会匹配这个
template<>
int add(int a, int b){
    cout << " add int :" << endl;
    return a + b;
}
//偏特化 一个模板的不同实现
template<typename T, typename U>
auto add(T *a, U *b) -> decltype(*a + *b){
    cout << "*a + *b : " << endl;
    return add(*a, *b);//可以递归调用
    //return *a + *b;//当传入**a 与**b时出错
}

int main(){
    int x = 45, y = 40;
    int *p = &x, *q = &y;
    cout << add(x, y) << endl;
    cout << add(p, q) << endl;
    return 0;
}

```

## 6.6可变参模板

```cpp
#include<iostream>
using namespace std;
//代表递归停止条件的模板函数要放在前面
template<typename T>
void print(const T &a){
    cout << a;
    return ;    
}
//...变参 ARGS是可变参的type包的名字
template<typename T, typename ...ARGS>
void print(const T &a, ARGS...args){//给ARGS...可变参量包起个名字为args
    cout << a << " ";
    print(args...);//递归打印变参列表 4个参数的模板调用3个参数的  3个参数的调用2个 2个调1个 没有1个参数的模板 需要（偏）特化 要放在上面 事先看到
    //args使用时一定要带...
    return ;    
}

int main(){
    int a;
    print(123, 34.5, "hello world", &a);
    cout << endl;
    return 0;
}

```

- 对于模板函数中用到了此模板函数的（偏）特化版本 需要将（偏）特化版本放在模板函数上边——————函数调用 下面的函数不能被上面的函数调用
- 对于模板类 不管模板类中有没有用到（偏）特化版本 都需要将（偏）特化版本放在模板类下面——————放在上面编译器不认识这是一个类



设计模板工具类解析模板的变参列表

```cpp
#include<iostream>
using namespace std;

template<typename T>
void print(const T &a){
    cout << a;
    return ;    
}



//...变参 ARGS是可变参的type包的名字
template<typename T, typename ...ARGS>
void print(const T &a, ARGS...args){//给ARGS...可变参量包起个名字为args
    cout << a << " ";
    print(args...);//递归打印变参列表 4个参数的模板调用3个参数的  3个参数的调用2个 2个调1个 没有1个参数的模板 需要（偏）特化 要放在上面 事先看到
    //args使用时一定要带...
    return ;
}

//设计模板工具类解析模板
template<typename T, typename ...ARGS>
struct ARG{
    typedef T __type;
    typedef ARG<ARGS...> __rest;//递归
};

//特化 递归出口
template<typename T>
struct ARG<T>{
    typedef T __type;
    typedef T __finaltype;
};

template<typename T, typename ...ARGS> class Test;
//因为这相当于一个特化版本 所以首先应该有这个模板类
template<typename T, typename ...ARGS> 
class Test<T(ARGS...)>{
    public:
    T operator()(typename ARG<ARGS ...>::__type a,
                 typename ARG<ARGS...>::__rest::__type b){

        return a + b;
    }
    //ps！！！！！！！！！！！！！！！！
    //这个地方不能用typename ARG<ARGS ...>::T 因为ARG是一个模板
    //它展开后就没有T变量的  T会变成具体的类型  当试图展开Test时找不到T了
};

int main(){
    int a;
    print(123, 34.5, "hello world", &a);
    cout << endl;
    //Test<int, float, double> d;
    //返回值类型为int ()中第一个参数类型为float  第二个为double
    //cout << d(4.5, 6) << endl;//float 4.5   double 6
    //这样不像函数 Test<int(float, double)>  这样更像函数
    Test<int(float, double)> f;
    cout << f(3.5, 4.8) << endl;
    //问题Test<int(float, double, int, int)>也能通过编译
    return 0;
}

```

问题Test<int(float, double, int, int)>也能通过编译

```cpp
//设计模板工具类解析模板
template<typename T, typename ...ARGS>
struct ARG{
    typedef T __type;
    typedef ARG<ARGS...> __rest;//递归
};

//特化 递归出口
template<typename T>
struct ARG<T>{
    typedef T __type;
    typedef T __finaltype;
};

template<typename T, typename ...ARGS> class Test;
//因为这相当于一个特化版本 所以首先应该有这个模板类
template<typename T, typename ...ARGS> 
class Test<T(ARGS...)>{
    public:
    T operator()(typename ARG<ARGS ...>::__type a,
                 typename ARG<ARGS...>::__rest::__finaltype b){

        return a + b;
    }
};

    //Test<int(float, double, int, int)> d;
    //cout << d(3.5, 4.8) << endl;报错

```

ARG工具的使用越简单越好

```cpp
//设计模板工具类解析模板
template<int n, typename T, typename ...ARGS>
struct ARG{
    typedef typename ARG<n - 1, ARGS...>::__type __type;//递归
    typedef ARG<n - 1, ARGS...> N;
};

//特化 递归出口
template<typename T, typename ...ARGS>
struct ARG<0, T, ARGS...>{
    typedef T __type;
};

template<typename T>
struct ARG<0, T>{
    typedef T __type;
    typedef T __finaltype;
};

template<typename T, typename ...ARGS> class Test;
//因为这相当于一个特化版本 所以首先应该有这个模板类
template<typename T, typename ...ARGS> 
class Test<T(ARGS...)>{
    public:
    T operator()(typename ARG<0, ARGS ...>::__type a,
                 typename ARG<1, ARGS...>::N::__finaltype b){

        return a + b;
    }
};
    //ARG工具的使用越简单越好
    //当调用第五个参数时需要这样 太麻烦 
    //ARG<int, double, float, int, int>::__rest::__rest::__rest::__rest::__type m = 13;
    //能不能简化为 ARG<4, int, double, float, int, int>::__type m = 13?
    ARG<4, int, double, float, float, int>::__type m = 13;
    cout << sizeof(m) << endl; 
	//ARG<4, int, double, float, float, int>::N::__finaltype m = 13;
	//报错 因为需要展开就需要N::N::N::N::__finaltype
```

```cpp

#include<iostream>
using namespace std;

template<typename T>
void print(const T &a){
    cout << a;
    return ;    
}



//...变参 ARGS是可变参的type包的名字
template<typename T, typename ...ARGS>
void print(const T &a, ARGS...args){//给ARGS...可变参量包起个名字为args
    cout << a << " ";
    print(args...);//递归打印变参列表 4个参数的模板调用3个参数的  3个参数的调用2个 2个调1个 没有1个参数的模板 需要（偏）特化 要放在上面 事先看到
    //args使用时一定要带...
    return ;
}

//设计模板工具类解析模板     n是编译期常量———模板作用在编译期
template<int n, typename T, typename ...ARGS>
struct ARG{
    typedef typename ARG<n - 1, ARGS...>::__type __type;//递归
    typedef ARG<n - 1, ARGS...> N;
    //需要N来调用__finaltype 因为上面是将__type赋成__type找不到__finaltype
    //可以修改为
    //typedef typename ARG<n - 1, ARGS ...>::__finaltype __finaltype;
	//下面可以通过typename ARG<1, ARGS ...>::__finaltype 调用__finaltype
};

//特化 递归出口
template<typename T, typename ...ARGS>
struct ARG<0, T, ARGS...>{
    typedef T __type;
};

template<typename T>
struct ARG<0, T>{
    typedef T __type;
    typedef T __finaltype;
};

template<typename T, typename ...ARGS> class Test;
//因为这相当于一个特化版本 所以首先应该有这个模板类
template<typename T, typename ...ARGS> 
class Test<T(ARGS...)>{
    public:
    //typename主要用来说明后面是一个类型 避免c++语义歧义
    T operator()(typename ARG<0, ARGS ...>::__type a,
                 typename ARG<1, ARGS...>::N::__finaltype b){
        return a + b;
    }
};

int main(){
    int a;
    print(123, 34.5, "hello world", &a);
    cout << endl;
    //Test<int, float, double> d;
    //返回值类型为int ()中第一个参数类型为float  第二个为double
    //cout << d(4.5, 6) << endl;//float 4.5   double 6
    //这样不像函数 Test<int(float, double)>  这样更像函数
    Test<int(float, double)> f;
    cout << f(3.5, 4.8) << endl;
    //问题Test<int(float, double, int, int)>也能通过编译
    //Test<int(float, double, int, int)> d;
    //cout << d(3.5, 4.8) << endl;报错
    //ARG工具的使用越简单越好
    //当调用第五个参数时需要这样 太麻烦 
    //ARG<int, double, float, int, int>::__rest::__rest::__rest::__rest::__type m = 13;
    //能不能简化为 ARG<4, int, double, float, int, int>::__type m = 13?
    ARG<4, int, double, float, float, int>::__type m = 13;
    cout << sizeof(m) << endl; 
    return 0;
}

```

以上的TEST都是两项相加 怎样实现多项相加？

用不到工具类ARG就不用

```cpp
template<typename T, typename ...ARGS> class Test;
//因为这相当于一个特化版本 所以首先应该有这个模板类
template<typename T, typename ...ARGS> 
class Test<T(ARGS...)>{
    public:
    //typename主要用来说明后面是一个类型 避免c++语义歧义
    T operator()(ARGS ...args){        
        return add<T>(args...);//只传第一个模板参数T 后面自行推导
    }
    private:
    template<typename T1, typename U, typename ...US>
    T1 add(U a, US ...args){
        return a + add<T1>(args...);
    }
    template<typename T1, typename U>
    T1 add(U a){
        return a;
    }
};
    Test<int(int, int, int, int)> f4;
    cout << f4(1, 2, 3, 4) << endl;

/*
template<typename T, typename ...ARGS> class Test{};
template<typename T, typename ...ARGS> 
class Test<T(ARGS ...)>{
public:
    T operator () (ARGS ...args){
        return add<T, ARGS ...>(args ...);
    }
private:
    template<typename T1, typename U, typename ...US>
    T1 add(U a, US ...args){
        return a + add<T1, US ...>(args ...);
    }
    template<typename T1, typename U>
    T1 add(U a){
        return a;
    }
};
*/
```

## 6.7模板函数作为参数时 类型的自动推导

```cpp
//模板参数作为参数 
template<typename T, typename U>
T test_param_func(U a){
    return a * 2;
}

//虽然模板函数参数不确定 但是会自行推导
void func2(int (*func)(double)){
    cout << func(2.3) << endl;
}

func2(test_param_func);

```

## 6.8模板函数参数引用（左右值）

```cpp
template<typename T>
T add2(T &a, T &b){
    return a + b;
}

    int l = 123, k = 456;
    cout << add2(l, k) << endl;
//上述模板展开为int add2(int, int)  T推导为int
//看起来是传的左值引用
//当想要运行下面代码时， 报错
    cout << add2(123, 456) << endl;//这样报错 定义的模板函数是左值引用
//右值肯定不能传到左值引用上  所以我们需要定义一个右值引用的模板函数

```

```cpp
template<typename T>
T add2(T &a, T &b){
    T c = a + b;
    return a + b;
}

template<typename T>
T add2(T &&a, T &&b){
    return a + b;
}
    int l = 123, k = 456;
    cout << add2(l, k) << endl;
    cout << add2(123, 456) << endl;
//这样就可以了 看似我们定义了一个左值引用和一个右值引用
//展开形式如下图
//每次定义引用的模板函数都需要定义两个？？？不方便

```

![image-20200808110636587](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200808110636587.png)

我们删除一下左值引用的模板参数

```cpp


template<typename T>
T add2(T &&a, T &&b){
    return a + b;
}
    int l = 123, k = 456;
    cout << add2(l, k) << endl;//报错
//上面模板函数展开为int& add2(int& &&, int& &&) T推导为T&
//T被推导为左值引用
cout << add2(123, 456) << endl;
//int add2(int &&, int &&)正常
```



引用折叠

- & 左值引用
- && 右值引用
- &&& 左值引用

奇数个& 左值引用   偶数个&为右值引用

我们可以看到 当用两个&&时，不一定这就是右值引用，模板函数会自行推导

如果传入的是左值，T被推倒为int &   加上后面的两个&&就成了左值引用

如果传入的是右，T被推倒为int   加上后面的两个&&就成了右值引用

![image-20200808111205130](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200808111205130.png)

为什么程序还会报错？ 

```cpp
template<typename T>
T add2(T &&a, T &&b){
    return a + b;
}
    int l = 123, k = 456;
    cout << add2(l, k) << endl;//报错
```

因为return时a+b为右值，返回值类型为左值引用  所以报错！！！

typename用法：[https://blog.csdn.net/lyn631579741/article/details/110730145](https://blog.csdn.net/lyn631579741/article/details/110730145)

下一步：将返回值T  int &---->int

```cpp
template<typename T> 
struct remove_reference1{
    typedef T type;
};

template<typename T> 
struct remove_reference1<T &>{
    typedef T type;
};

template<typename T> 
struct remove_reference1<T &&>{
    typedef T type;
};
template<typename T>
typename remove_reference1<T>::type add2(T &&a, T &&b){
    typename remove_reference1<T>::type c = a + b;
    return a + b;
}
    int l = 123, k = 456;
    cout << add2(l, k) << endl;
    cout << add2(123, 456) << endl;

```

这样就可以了  remove_reference1相当于类型强转T &/T &&---->T  

**PS：**上面的引用折叠技巧一般用在模板函数中 因为模板类的类型T都会给定 不会自行推导

remove_reference  c++中自带的内置函数  作用  去掉引用

![image-20200808114739887](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200808114739887.png)

```cpp
    template<typename T> struct add_const{typedef const T type;};
    template<typename T> struct add_const<const T>{typedef const T type;};

    template<typename T> struct add_lvalue_reference{typedef T & type;};
    template<typename T> struct add_lvalue_reference<T &>{typedef T & type;};
    template<typename T> struct add_lvalue_reference<T &&>{typedef T & type;};

    template<typename T> struct add_rvalue_reference{typedef T && type;};
    template<typename T> struct add_rvalue_reference<T &>{typedef T && type;};
    template<typename T> struct add_rvalue_reference<T &&>{typedef T && type;};

    template<typename T> struct remove_pointer{typedef T type;};
    template<typename T> struct remove_pointer<T *>{typedef typename remove_pointer<T>::type type;};

    template<typename T>
    typename add_rvalue_reference<T>::type move(T &&a){
        return typename add_rvalue_reference<T>::type(a);//强转
        //float(3) 将3强转为3.0000..
    }

```

## 6.9模板类function

函数 函数对象统一叫做可调用对象

function相当于简化了以后的函数指针 是一个类

#include< functional >

函数和函数对象（可调用对象）都可以传入function这个模板类

- 给g赋值  肯定会调用构造函数 需要给其增加两类构造函数（函数、函数对象）
- 将函数/函数对象包装成另外一个函数对象 地址保存

```cpp
#include<iostream>
#include<functional>
using namespace std;

void f(function<int(int, int)> g){
    cout << g(3, 4) << endl;
    return ;
}

int add(int a, int b){
    return a + b;
}

struct MaxClass{
    int operator()(int a, int b){
        return a > b ? a : b;
    
    }
};

namespace haizei{
//ptr有两种 一种函数指针 一种对象指针 base为接口类
template<typename RT, typename ...PARAMS>
class base{
    public:
    virtual RT operator()(PARAMS...) = 0;
    virtual ~base(){}
};
//normal_func内的ptr属性指向函数
template<typename RT, typename ...PARAMS>
class normal_func : public base<RT, PARAMS...>{
    public:
    typedef RT(*func_type)(PARAMS...);
    normal_func(func_type func) : ptr(func) {}
    //调用函数
    virtual RT operator()(PARAMS...args) override{
        return this->ptr(args...);
    };
    private:
    func_type ptr;

};

 //functor的ptr是一个指向函数对象的引用
template<typename C, typename RT, typename ...PARAMS>
class functor : public base<RT, PARAMS...>{
    public:
    functor(C &func) : ptr(func) {}
    //调用函数
    virtual RT operator()(PARAMS...args) override{
        return this->ptr(args...);
    };
    private:
    C &ptr;

};

//封装一个function的模板类 主要的属性就是一个指向课调用对象的指针ptr
//关键就是怎样拿到ptr 有两种 一种是函数 一种是函数对象（lambda表达式为函数对象）
template<typename RT, typename ...PARAMS> class function{};
template<typename RT, typename ...PARAMS>
class function<RT(PARAMS...)>{
    public:
    //new返回的是一个纯右值指针
    //ptr对象是base类 父类的指针可以直接当子类的使用 猫是一个动物
    function(RT (*func)(PARAMS...)) : ptr(new normal_func<RT, PARAMS...>(func)){
        //cout << "normal function constr" << endl;
    }
    //去掉引用 如果是左值 模板第一个参数T推断为一个引用（int &）
    //当new functor对象时 C为int &  ptr为&&形式 错误
    template<typename T>
    function(T &&a) : ptr(new functor<typename remove_reference<T>::type, RT, PARAMS...>(a)){
        //cout << "object function constr" << endl;
    }
    //function重载（）运算符  运行函数
    RT operator()(PARAMS... args){
        return this->ptr->operator()(args...);
    }
    ~function(){
        delete ptr;
    }
    private:
    base<RT, PARAMS...> *ptr;
};

};//end of haizei

int main(){
    MaxClass max;
    f(add);
    f(max);
    haizei::function<int(int, int)> g1(add);
    haizei::function<int(int, int)> g2(max);
    //haizei::function<int(int, int)> g3(MaxClass());    
    //有歧义 c++会把MaxClass()当成函数声明 换成MaxClass{} 或再加一层()即可
    cout << g1(3, 4) << endl;
    cout << g2(3, 4) << endl;
    return 0;
}

```

![image-20200819094708930](https://gitee.com/xsm970228/images/raw/master/image-20200819094708930.png)

![image-20200819094721367](https://gitee.com/xsm970228/images/raw/master/image-20200819094721367.png)

![image-20200819094742888](https://gitee.com/xsm970228/images/raw/master/image-20200819094742888.png)

![image-20200819094800739](https://gitee.com/xsm970228/images/raw/master/image-20200819094800739.png)





完善functioncnt

![image-20200813095629962](https://gitee.com/xsm970228/images/raw/master/image-20200813095629962.png)



```cpp
#include <iostream>
#include <cstring>
#include <string>
#include <cmath>
#include <algorithm>
#include <queue>
#include <set>
#include <vector>
#include <functional>
#include <map>
using namespace std;
namespace haizei {
template <typename RT, typename ...PARAMS>
class base {
public :
    virtual RT operator()(PARAMS...) = 0;
    virtual ~base() {};
};

template<typename RT, typename ...PARAMS> class normal_func :  public base<RT, PARAMS...> {
public :
    typedef RT(*func_type)(PARAMS...);
    normal_func(func_type func) : ptr(func) {}
    virtual RT operator()(PARAMS... args) override {
        return this->ptr(args...);
    }
private :
    func_type ptr;
};

template<typename C, typename RT, typename ...PARAMS> class functor :  public base<RT, PARAMS...> {
public :
    typedef RT(*func_type)(PARAMS...);
    functor(C &func) : ptr(func) {}
    virtual RT operator()(PARAMS... args) override {
        return this->ptr(args...);
    }
private :
    C &ptr;
};

template<typename RT, typename ...PARAMS> class function;
template<typename RT, typename ...PARAMS>
class function<RT(PARAMS...)> {
public :
    function(RT (*func)(PARAMS...)) : ptr(new normal_func<RT, PARAMS...>(func)) {
    }
    template<typename T>
    function(T &&a) : ptr(new functor<typename remove_reference<T>::type, RT, PARAMS...>(a)){
    }
    RT operator()(PARAMS... args) {
        return this->ptr->operator()(args...);
    }
    ~function() {}
private :
    base<RT, PARAMS...> *ptr;
};
//需要一个函数指针，需要一个私有的解析参数列表的方法， 重载() 运算符
}
void f(function<int(int, int)> g) {
    cout << g(3, 4) << endl;
    return ;
}

int add(int a, int b) {
    return a + b;
}


struct MaxClass {
    int operator()(int a, int b) {
        return a > b ? a : b;
    }  
};

int add2(int a, int b, int c, int d) {
    return a + b + c + d;
}

template <typename RT, typename ...PARAMS>
class FunctionCnt;
template <typename RT, typename ...PARAMS>
class FunctionCnt<RT(PARAMS... params)> {
public :
    //FunctionCnt(haizei::function<RT(PARAMS...)> g) : g(g), __cnt(0) {}
    //发生段错误 传入参数g 是局部变量，haizei::中的function  拷贝行为是浅拷贝，g(g) 最终都会被析构；所以发生了段错误；
    FunctionCnt(function<RT(PARAMS...)> g) : g(g), __cnt(0) {}
    RT operator()(PARAMS... params) {
        __cnt++;
        return g(params...);
    }
    int cnt() { return this->__cnt; }
private :
    function<RT(PARAMS...)> g;
    int __cnt;
};



int main() {
    MaxClass max;
    f(max);
    f(add);
    haizei::function<int(int, int)> g1(max);
    haizei::function<int(int, int)> g2(add);
    FunctionCnt<int(int, int)> g3(max);
    FunctionCnt<int(int, int, int, int)> g4(add2);
    cout << g3(5,11111) << endl;
    cout << "gan:" << endl;
    cout << g4(1, 2, 3, 4) << endl;
    cout << g4.cnt() << endl;


    return 0;
}
```



```cpp

/*************************************************************************
	> File Name: 30.function.cpp
	> Author: huguang
	> Mail: hug@haizeix.com
	> Created Time: 六  8/ 8 15:25:29 2020
 ************************************************************************/

#include <iostream>
#include <cstdio>
#include <cstdlib>
#include <queue>
#include <stack>
#include <algorithm>
#include <string>
#include <map>
#include <set>
#include <vector>
#include <functional>
using namespace std;

namespace haizei {

template<typename RT, typename ...PARAMS> 
class base {
public :
    virtual RT operator()(PARAMS...) = 0;
    virtual base<RT, PARAMS...> *getCopy() = 0;
    virtual ~base() {}
};

template<typename RT, typename ...PARAMS> 
class normal_func : public base<RT, PARAMS...> {
public :
    typedef RT (*func_type)(PARAMS...);
    normal_func(func_type func) : ptr(func) {}
    virtual RT operator()(PARAMS...args) override {
        return this->ptr(args...);
    }
    virtual base<RT, PARAMS...> *getCopy() override {
        return new normal_func<RT, PARAMS...>(ptr);
    }

private:
    func_type ptr;
};

template<typename C, typename RT, typename ...PARAMS> 
class functor : public base<RT, PARAMS...> {
public :
    functor(C &func) : ptr(func) {}
    virtual RT operator()(PARAMS...args) override {
        return this->ptr(args...);
    }
    virtual base<RT, PARAMS...> *getCopy() override {
        return new functor<C, RT, PARAMS...>(ptr);
    }

private:
    C &ptr;
};

template<typename RT, typename ...PARAMS> class function;
template<typename RT, typename ...PARAMS>
class function<RT(PARAMS...)> {
public :
    function(RT (*func)(PARAMS...)) 
    : ptr(new normal_func<RT, PARAMS...>(func)) {}

    template<typename T>
    function(T a) 
    : ptr(new functor<typename remove_reference<T>::type, RT, PARAMS...>(a)){}
    
    function(const function &f) {
        this->ptr = f.ptr->getCopy();
    }
    function(function &&f) {
        this->ptr = f.ptr;
        f.ptr = nullptr;
    }
    
    RT operator()(PARAMS... args) {
        return this->ptr->operator()(args...);
    }
    ~function() {
        if (ptr != nullptr) delete ptr;
    }

private:
    base<RT, PARAMS...> *ptr;
};

} // end of haizei

void f(function<int(int, int)> g) {
    cout << g(3, 4) << endl;
    return ;
}

int add(int a, int b) {
    return a + b;
}

int add2(int a, int b, int c) {
    return a + b + c;
}

struct MaxClass {
    int operator()(int a, int b) {
        return a > b ? a : b;
    }
};

template<typename T, typename ...ARGS> class FunctionCnt;
template<typename T, typename ...ARGS>
class FunctionCnt<T(ARGS...)> {
public :
    FunctionCnt(haizei::function<T(ARGS...)> g) : g(g), __cnt(0) {}
    int operator()(ARGS... args) {
        __cnt += 1;
        return g(args...);
    }
    int cnt() { return __cnt; }

private:
    haizei::function<T(ARGS...)> g;
    int __cnt;
};

int main() {
    function<int(int, int)> test_p;
    MaxClass max;
    test_p = add;
    cout << test_p(4, 10) << endl;
    test_p = max;
    cout << test_p(5, 10) << endl;
    f(add);
    f(max);
    haizei::function<int(int, int)> g1(add);
    haizei::function<int(int, int)> g2(max);
    cout << g1(3, 4) << endl;
    cout << g2(3, 4) << endl;
    
    FunctionCnt<int(int, int)> add_cnt(add);
    add_cnt(3, 4);
    add_cnt(4, 5);
    add_cnt(7, 9);
    cout << add_cnt.cnt() << endl;
    FunctionCnt<int(int, int, int)> add_cnt2(add2);
    add_cnt2(1, 2, 3);
    add_cnt2(4, 5, 6);
    cout << add_cnt2.cnt() << endl;
    return 0;
}


```



![image-20200813120610258](https://gitee.com/xsm970228/images/raw/master/image-20200813120610258.png)





## 6.10模板的图灵完备性

模板能实现绝大多数的功能

将计算过程放在编译期

作业 输出n以内的所有素数

不能用if for 这都是运行时期的状态

for循环用递归代替

if语句用?:表达式来代替  都是有返回值的！！！

```cpp
#include<iostream>
#include<string>
#include<set>
#include<queue>
#include<vector>
#include<stack>
#include<map>
#include<unordered_map>
using namespace std;
//----------------------iseven-----------------------------
template<int n>
struct IsEven{
    static constexpr int r = !(n % 2);//编译期常量 在编译期就确定了
};
//-------------------------add---------------------
template<int a, int b>
struct add{
    static constexpr int r = a + b;
};
//--------------------------sum-----------------------
template<int n>
struct Sum{
    static constexpr int r = n + Sum<n - 1>::r;
};
//偏特化相当于递归出口
template<>
struct Sum<0>{
    static constexpr int r = 0;
};
//-------------------------factorial--------------------
template<int n>
struct Factorial{
    static constexpr int r = n * Factorial<n - 1>::r;
};
template<>
struct Factorial<0>{
    static constexpr int r = 1;
};
//--------------------------prime-----------------------

template<int n, int i>
struct getNextN{
    static constexpr int r = (i * i > n ? 0 : n);
};

template<int n, int i>
struct getNextI{
    static constexpr int r = (n % i == 0 ? 0 : i + 1);
};


template<int n, int i>
struct IsTest{
    static constexpr int r = IsTest<getNextN<n, i>::r, getNextI<n, i>::r>::r;
};
//偏特化IsTest 当n为0说明i*i>n 素数  当i = 0说明n%i为0 不是素数
template<int i>
struct IsTest<0, i>{
    static constexpr int r = 1;
};

template<int n>
struct IsTest<n, 0>{
    static constexpr int r = 0;
};

template<>
struct IsTest<0, 0>{
    static constexpr int r = 1;
};
template<int n>
struct Isprime{
    static constexpr int r = IsTest<n, 2>::r;
};
/*
//hzb版本---
template<int n, int m>
struct Check{
    static constexpr bool r = (n%m != 0) && Check<n, m-1>::r;
};

template<int n>
struct Check<n, 2>{
    static constexpr bool r = (n%2 != 0);
};


template<int n>
struct IsPrime{
    static constexpr int r = Check<n, (int)sqrt(n)>::r;
};
//hzbend---------
*/
//-----------------------primesum---------------
template<int n>
struct SumPrime{
    static constexpr int r = n * Isprime<n>::r + SumPrime<n - 1>::r;
};
template<>
struct SumPrime<1>{
    static constexpr int r = 0;
};

int main(){
    cout << IsEven<123>::r << endl;
    cout << IsEven<124>::r << endl;
    cout << add<123, 456>::r << endl;
    cout << Sum<10>::r << endl;
    cout << Sum<100>::r << endl;
    //运行时数据已经算好了 运行期速度O(1) 但是编译期需要存放11个类/101类
    //编译期的代码段长（机器码01表示的代码逻辑）
    //空间换时间 时间换空间
    cout << Factorial<5>::r << endl;
    cout << Isprime<9973>::r << endl;
    cout << Isprime<5>::r << endl;
    cout << Isprime<6>::r << endl;
    cout << SumPrime<10>::r << endl;
    return 0;
}

```

## 项目：封装pthread类

线程不能无限多 得有阈值 线程占用资源 是资源就需要控制

线程池

将任务扔进任务队列中，线程池中空闲的线程取任务队列中的任务

任务一定需要某个函数方法和参数来执行任务

函数方法和函数参数打包在一起bind

成员方法的函数名不能代表地址

普通的函数名就是函数地址

```cpp
#include<iostream>
#include<string>
#include<set>
#include<queue>
#include<vector>
#include<stack>
#include<map>
#include<unordered_map>
#include<functional>
#include<thread>
using namespace std;

//--------------打包函数方法和参数bind-----------------
//任务类对象 将函数方法与参数打包
class Task{
public:
    //打包的构造函数
    template<typename T, typename ...ARGS>
    Task(T func, ARGS... args){
        this->func = std::bind(func, forward<ARGS>(args)...);//forward准确向前传递 引用就是引用 数值就是数值
    }
    //operator()执行函数
    void operator()(){
        this->func();
        return ;
    }
private:
    function<void()> func;
};
//-------------------task-END----------------------------
//----------------thread_class------------------------
class ThreadPool{
public:
    ThreadPool(int n = 4) : is_running(false), max_threads_num(n){}
    //启动线程池
    void start(){
        if(is_running) return ;
        is_running = true;
        for(int i = 0; i < max_threads_num; i++){
            threads.push_back(new thread(&ThreadPool::worker, this));
            //以成员方法作为入口
            //需要绑定默认的参数this
        }
        return ;
    }
    //结束线程池
    void stop(){
        if(is_running == false) return ;
        is_running = false;
        for(int i = 0; i < max_threads_num; i++){
            threads[i]->join();
            delete threads[i];
        }
        threads.clear();
    }
    //线程池入口函数
    void worker(){
        cout << "worker: i am a worker" << endl;
    }
private:
    bool is_running;
    int max_threads_num;
    vector<thread *> threads;
};
//--------------------------thread_class_END-------------------------

void thread_func1(int a, int b){
    cout << a << " + " << b << " = " << a + b << endl;
}

void thread_func2(int &n){
    n++;
    return ;
}

int main(){
    ThreadPool tp(6);
    tp.start();
    tp.stop();
    
    return 0;
}

```

外在表现

临界区资源 线程都能访问得到  需要考虑多线程时的线程安全

线程安全——当前的资源在多个线程同时的操作下 逻辑不能出错 

c++提供的stl基本都是非线程安全

线程的同步操作的本质是将临界区资源加锁实现串行操作  对原子操作加锁

原子操作——最小的一个操作 不能插入其他操作  

![image-20200815104138341](https://gitee.com/xsm970228/images/raw/master/image-20200815104138341.png)



GUN 无锁化编程工具

![image-20200815161840933](https://gitee.com/xsm970228/images/raw/master/image-20200815161840933.png)



强制类型转换：

- static_cast
- dynamic_case
- const_cast
- reinter_cast



**static_cast:** [https://www.cnblogs.com/QG-whz/p/4509710.html](https://www.cnblogs.com/QG-whz/p/4509710.html)

- 编译器隐式执行的任何类型转换都可以由static_cast来完成，比如int与float、double与char、enum与int之间的转换等。
- 使用static_cast可以找回存放在void\*指针中的值。
- static_cast也可以用在于基类与派生类指针或引用类型之间的转换。然而它不做运行时的检查，不如dynamic_cast安全。
- static_cast可以用来给变量加cosnt属性

**const_cast:** [https://www.cnblogs.com/QG-whz/p/4513136.html](https://www.cnblogs.com/QG-whz/p/4513136.html)

- 将转换掉表达式的const性质。
- 只有使用const_cast才能将const性质性质转化掉。试图使用其他三种形式的强制转换都会导致编译时的错误。（添加const还可以用其他转换符，如static_const）
- 除了添加const或删除const特性，使用const_cast符来执行其他任何类型的转换都会引起编译错误。

**reinterpret_cast**:

- reinterpret_cast是最危险的一种cast，之所以说它最危险，是因为它的表现和C-Like一般强大，稍微不注意就会出现错误。它一般在一些low-level的转换或者位操作当中运用。

**dynamic_cast:**

- 动态转换 一般用在多态的情况下 

```c++
     ----------------
     /  dynamic_cast \ -->同一继承体系（virtual）的类指针或引用[更安全的downcast]
    ~~~~~~~~~~~~~~~~~~~~  
    /   static_cast  \ -->基础类型[更安全]，同一继承体系的类指针或引用
   ~~~~~~~~~~~~~~~~~~~~~~~~
   /  reinterpret_cast  \ -->与C-Like的作用一致，没有任何静态或者动态的checking机制
  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  /     C-Like      \ -->基础类型，同一继承体系的类指针或引用,不同继承体系类的指针或引用
 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

```



# lambda表达式

三种可调用对象

- 函数

- 函数对象

- lambda表达式



```cpp
#include<iostream>
#include<string>
#include<set>
#include<queue>
#include<vector>
#include<stack>
#include<map>
#include<unordered_map>
using namespace std;


int main(){
    //定义的所有的lambda表达式类型都不一样
    //有了auto才能有lambda表达式
    //auto两大主要功能 for(auto x : vec)  lambda表达式
    //[口袋] 用来装入外面的变量
    //返回值类型自动推导/返回值后置
    auto a = [](int a, int b){return a + b;};
    auto b = [](int a, int b){return a + b;};
    cout << typeid(a).name() << endl;
    cout << typeid(b).name() << endl;//名字不一样 定义的所有的lambda表达式类型都不一样
    cout << a(123, 456) << endl;
    cout << b(987, 120) << endl;
    auto c = [](int a, int b) -> double{
        double c = 12.3;
        if(rand() % 2){
            return c;
        }else{
            return a + b;
        }
    };//lambda表达式就是个对象 是个值 赋值语句加分号；
    return 0;

}

```

```cpp
#include<iostream>
#include<string>
#include<set>
#include<queue>
#include<vector>
#include<stack>
#include<map>
#include<unordered_map>
using namespace std;


int main(){
    //函数
    //函数对象
    //lambda表达式
    //定义的所有的lambda表达式类型都不一样
    //有了auto才能有lambda表达式
    //auto两大主要功能 for(auto x : vec)  lambda表达式
    //[口袋] 用来装入外面的变量
    //返回值类型自动推导/返回值后置
    
    int n = 100;
    auto a = [&n](int a, int b){return a + b + n;};
    n = 100000000;
    auto b = [&n](int a, int b){return a + b + n;};
    cout << typeid(a).name() << endl;
    cout << typeid(b).name() << endl;//名字不一样 定义的所有的lambda表达式类型都不一样
    cout << a(123, 456) << endl;
    cout << b(987, 120) << endl;//调用ab时 n的值不一样
    //当[n]里面只写一个变量n时 值捕获 a捕获到100  b捕获到100000000
    //当[&n]里面是&引用捕获时 ab都捕获到100000000
    //当需要传的变量很多时 单个写不方便 [=]以值捕获的形式捕获所有的外部变量 [&]以引用捕获
    //当没有捕获任何外部变量时，lambda表达式不能使用任何外部变量 
    //lambda表达式只能访问捕获列表和参数的值
    auto c = [](int a, int b) -> double{
        double c = 12.3;
        if(rand() % 2){
            return c;
        }else{
            return a + b;
        }
    };//lambda表达式就是个对象 是个值 赋值语句加分号；
    return 0;

}

```

```cpp
#include<iostream>
#include<string>
#include<set>
#include<queue>
#include<vector>
#include<stack>
#include<map>
#include<unordered_map>
#include<functional>
using namespace std;


int main(){
    //函数
    //函数对象
    //lambda表达式
    //定义的所有的lambda表达式类型都不一样
    //有了auto才能有lambda表达式
    //auto两大主要功能 for(auto x : vec)  lambda表达式
    //[口袋] 用来装入外面的变量
    //返回值类型自动推导/返回值后置
    
    int n = 100;
    auto a = [&n](int a, int b){return a + b + n;};
    n = 100000000;
    auto b = [&n](int a, int b){return a + b + n;};
    auto d = [](function<int(int, int)> a, function<int(int, int)> b){
        return [=](int x){
            return a(x, x) + b(x, x);  
        };
    };
    cout << d(a, b)(5) << endl;
    //d返回的是一个lambda表达式！！还需要参数 
    //d是一个可配置的可调用对象 可以传入不同的可调用对象来构造新的可调用对象
    cout << typeid(a).name() << endl;
    cout << typeid(b).name() << endl;//名字不一样 定义的所有的lambda表达式类型都不一样
    cout << a(123, 456) << endl;
    cout << b(987, 120) << endl;//调用ab时 n的值不一样
    //当[n]里面只写一个变量n时 值捕获 a捕获到100  b捕获到100000000
    //当[&n]里面是&引用捕获时 ab都捕获到100000000
    //当需要传的变量很多时 单个写不方便 [=]以值捕获的形式捕获所有的外部变量 [&]以引用捕获
    //当没有捕获任何外部变量时，lambda表达式不能使用任何外部变量 
    //lambda表达式只能访问捕获列表和参数的值
    auto c = [](int a, int b) -> double{
        double c = 12.3;
        if(rand() % 2){
            return c;
        }else{
            return a + b;
        }
    };//lambda表达式就是个对象 是个值 赋值语句加分号；
    return 0;

}


```

# C++异常处理

**C++异常处理：** [https://www.runoob.com/cplusplus/cpp-exceptions-handling.html](https://www.runoob.com/cplusplus/cpp-exceptions-handling.html)

- **throw:** 当问题出现时，程序会抛出一个异常。这是通过使用 **throw** 关键字来完成的。
- **catch:** 在您想要处理问题的地方，通过异常处理程序捕获异常。**catch** 关键字用于捕获异常。
- **try:** **try** 块中的代码是被保护代码。它后面通常跟着一个或多个 catch 块。当try中的代码执行完没有异常抛出时，后面的catch代码都不执行，直接执行后边的代码。当保护代码中有异常抛出时，直接跳到对应的catch执行相关代码，之后再执行catch后的代码

```cpp
try
{
   // 保护代码
}catch( ExceptionName e1 )
{
   // catch 块
}catch( ExceptionName e2 )
{
   // catch 块
}catch( ExceptionName eN )
{
   // catch 块
}
```

```cpp
#include <iostream>
using namespace std;
 
double division(int a, int b)
{
   if( b == 0 )
   {
      throw "Division by zero condition!";
   }
   return (a/b);
}
 
int main ()
{
   int x = 50;
   int y = 0;
   double z = 0;
 
   try {
     z = division(x, y);
     cout << z << endl;
   }catch (const char* msg) {
     cerr << msg << endl;
   }
 
   return 0;
}

//执行结果
//Division by zero condition!
```

C++ 提供了一系列标准的异常，定义在 **<exception>** 中，我们可以在程序中使用这些标准的异常。它们是以父子类层次结构组织起来的

![C++ 异常的层次结构](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210517090051.png)

![image-20210517090214209](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210517090217.png)

```cpp
#include <iostream>
#include <exception>
using namespace std;
 
struct MyException : public exception
{
  const char * what () const throw ()
  {
    return "C++ Exception";
  }
};
 
int main()
{
  try
  {
    throw MyException();
  }
  catch(MyException& e)
  {
    std::cout << "MyException caught" << std::endl;
    std::cout << e.what() << std::endl;
  }
  catch(std::exception& e)
  {
    //其他的错误
  }
}

//代码执行结果
//MyException caught
//C++ Exception
```

**what()** 是异常类提供的一个公共方法，它已被所有子异常类重载。这将返回异常产生的原因。

# 多线程

[https://www.runoob.com/cplusplus/cpp-multithreading.html](https://www.runoob.com/cplusplus/cpp-multithreading.html)



# 设计模式

指导我们在面向对象的编程范式下程序如何设计

用面向对象的编程范式下不知道如何设计程序 可能会用到设计模式

## 访问者模式

根据不同类型做出不同的功能映射

访问者模式在我们添加了一个新类型 但是忘记加相应的功能映射时 会在编译时报错  将运行时的bug转化为编译时

```cpp
#include<iostream>
#include<string>
#include<set>
#include<queue>
#include<vector>
#include<stack>
#include<map>
#include<algorithm>
using namespace std;

class B;
class C;
class D;
class E;

class A{
public:
	//接口类
	//要访问以A类作为基类的系列对象 需要在A类定义一个访问接口类
	class IVisitor{
	public:
		virtual void visit(B *) = 0;
		virtual void visit(C *) = 0;
		virtual void visit(D *) = 0;
		virtual void visit(E *) = 0;

	};
	//在基类中提供接受相关访问者对象的  一个访问者对象代表一种功能
	//其在子类的实现都一样
	//void Accept(IVisitor *vis){
	//	vis->visit(this);
	//}
	virtual void Accept(IVisitor *) = 0;
	virtual ~A(){}
};
class B : public A{

public:
	void Accept(IVisitor *vis){
		vis->visit(this);
	}
};
class C : public A{
public:
	void Accept(IVisitor *vis){
		vis->visit(this);
	}
};
class D : public A{
public:
	void Accept(IVisitor *vis){
		vis->visit(this);
	}
};
class E : public A{
public:
	void Accept(IVisitor *vis){
		vis->visit(this);
	}
};

class OutputVisitor : public A::IVisitor{
	virtual void visit(B *obj){
		cout << "this is class B" << endl;
	}
	virtual void visit(C *obj){
		cout << "this is class C" << endl;
	}
	virtual void visit(D *obj){
		cout << "this is class D" << endl;
	}
	virtual void visit(E *obj){
		cout << "this is class E" << endl;
	}
};

int main(){
	A *arr[10];
	for(int i = 0; i < 10; i++){
		switch(rand() % 4){
			case 0: arr[i] = new B(); break;
			case 1: arr[i] = new C(); break;
			case 2: arr[i] = new D(); break;
			case 3: arr[i] = new E(); break;
		}
	}
	OutputVisitor vis;
	for(int i = 0; i < 10; i++){
		arr[i]->Accept(&vis);
	}
	//假设arr[i]为c类
	//Accept为虚函数 看对象 c对象 执行c类中的的Accept方法 52行
	//Accept中传进去的是一个OutputVisitor对象 用父类IVisitor指针接受
	//执行isit方法 这是一个虚函数 所以看对象是OutputVisitor对象 执行OutputVisitor中的传入c类对象的visit对象
	
	//根据父类指针的所指对象的原始类型做出不同的响应
	//下面这段代码存在严重的bug
	//如果再加一个派生类 所有的这样的功能设计都需要加一个分支
	//即使没忘记加了也能通过编译 执行完才能看出来
	/*for(int i = 0; i < 10; i++){
		if(dynamic_cast<B *>(arr[i])){
			cout << "this is class B" << endl;
		}else if(dynamic_cast<C *>(arr[i])){
			cout << "this is class C" << endl;
		}else if(dynamic_cast<D *>(arr[i])){
			cout << "this is class D" << endl;
		}else{
			cout << "error" << endl;
		}
	}*/


	return 0;
}

```

好处：

1. 增加派生类 修改了访问者接口 只要有还没有修改的映射的功能模块 会报错  编译时的错误
2. A类下有100个派生类 if else需要写100次 时间执行效率跟基类派生类数量有关  而访问者模式无关 简洁 效率高

![image-20200819130901254](https://gitee.com/xsm970228/images/raw/master/image-20200819130901254.png)

![image-20200819130909729](https://gitee.com/xsm970228/images/raw/master/image-20200819130909729.png)



```cpp
//设计新的功能  一个val 遇到不同的类对象执行+-*/操作
/*
class B;//+5
class C;//*2
class D;//-4
class E;//+6
*/
class CalcVisitor : public A::IVisitor{
public:
	CalcVisitor(int val = 1) : val(val){}
	virtual void visit(B *obj){
		val += 5;
	}
	virtual void visit(C *obj){
		val *= 2;
	}
	virtual void visit(D *obj){
		val -= 4;
	}
	virtual void visit(E *obj){
		val += 6;
	}
	int val;
};
CalcVisitor vis2;
for(int i = 0; i < 10; i++){
arr[i]->Accept(&vis);
arr[i]->Accept(&vis2);
}
cout << vis2.val << endl;
```

## 责任链模式

![image-20200829151557533](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200829151557533.png)

想要买以一辆车先去文奔驰  奔驰不行 再去奥迪 还是不行  最后去宝马 可以 

怎样将不同的类串成一个链表？？？需要有一个父类！！！

每个节点不仅需要有干活的方法  还需要有判断是不是属于这个节点干活的方法

大量的if-else可以用责任链改写

![image-20200829151957459](https://gitee.com/xsm970228/images2020.9.5/raw/master/image-20200829151957459.png)



## 抽象工厂模式

由抽象工厂模式想到抽象类 抽象类提供接口

![image-20200908110212087](https://gitee.com/xsm970228/images2020.9.5/raw/master/20210315093647.png)







# 智能指针

智能指针详解[https://www.cnblogs.com/WindSun/p/11444429.html](https://www.cnblogs.com/WindSun/p/11444429.html)

智能指针是包含重载运算符的类，其行为像常规指针，但智能指针能够及时、妥善的销毁动态分配的数据，并实现了明确的对象生命周期，因此更有价值

## unique_ptr

#include<memory>

std::unique_ptr 是通过指针占有并管理另一对象，并在 unique_ptr 离开作用域时释放该对象的智能指针。

在下列两者之一发生时用关联的删除器释放对象：

- 销毁了管理的 unique_ptr 对象
- 通过 operator= 或 reset() 赋值另一指针给管理的 unique_ptr 对象。

简而言之，一个对象只能由一个unqiue_ptr指向控制  当此指针没了 则所指向的对象立马销毁

unique支持的操作：

1. 赋值操作/构造时，右边是一个左值，禁止
2. 赋值操作/构造时，右边是一个右值（临时），可以
3. 允许通过move移交控制权（move转右值传递）

```cpp
#include<iostream>
#include<memory>
using namespace std;

struct A{
public:
    A() : a(0){}
    A(int a) : a(a){cout << "A constructor : " << this << endl;}
    ~A() {cout << "A desconstructor : " << this << endl;}
private:
    int a;
};



int main(){
    //unique_ptr<A> p(new A(5)), q = p;//报错 unique_ptr指向的对象只能由一个unique_ptr指向
    //unique_ptr对象不能复制！！
    //shared_ptr<A> p(new A(5)), q = p;//不会报错 
    unique_ptr<A> p(new A(5)), q(new A(6));
    //指向对象的unique_ptr被销毁或者转移指向目标则该对象销毁
    //p = nullptr;
    p = move(q);
    cout << "end of main()" << endl;
    return 0;
}
```

```cpp
A a;
unique_ptr<A> p(&a);
//这样样在析构时会报错
//先析构智能指针对象 则对象a也会被析构
//再析构对象a时 段错误
```

## shared_ptr

#include<memory>

std::shared_ptr 是通过指针保持对象共享所有权的智能指针。多个 shared_ptr 对象可占有同一对象。下列情况之一出现时销毁对象并解分配其内存：

- 最后剩下的占有对象的 shared_ptr 被销毁；
- 最后剩下的占有对象的 shared_ptr 被通过 operator= 或 reset() 赋值为另一指针。

简而言之，一个对象可以被多个shared_ptr指向控制  当这些指向同一个对象的智能指针没了 则所指向的对象立马销毁

自己实现一个简单的shared_ptr：

```cpp
#include<iostream>
using namespace std;

namespace haizei{
template<typename T>
class shared_ptr{
public:
    shared_ptr() : ptr(nullptr), pcnt(nullptr){}
    shared_ptr(T *ptr) : ptr(ptr), pcnt(new int(1)){}
    shared_ptr(const shared_ptr<T> &p) : ptr(p.ptr), pcnt(p.pcnt){
        *pcnt += 1;
    }
    shared_ptr<T> &operator=(shared_ptr<T> &p){
        this->use_count_des();//p = q  说明p原本指向的对象的计数器需要-1 q指向指向的对象的计数器需要+1
        this->ptr = p.ptr;
        this->pcnt = p.pcnt;
        *pcnt += 1;
        return *this;
    }
    int use_count(){
        return (pcnt ? *pcnt : 0);
    }
    T *operator->(){return this->ptr;}
    T &operator*() {return *(this->ptr);}   
    ~shared_ptr(){
        use_count_des();
    }
private:
    void use_count_des(){
        if(pcnt){
            *pcnt -= 1;
            if(*pcnt == 0){
                delete pcnt;
                delete ptr;
            }
        }
    }
    T *ptr;  
    int *pcnt;   //地址 所有的指向同一个对象的智能指针计数器在同一个位置                                                        
};


class A{
public:
    int x, y; 
    A() : x(123), y(456){
        cout << "A constr : " << this << endl;
    }
    ~A(){
        cout << "A desconstr : " << this << endl;
    }
    haizei::shared_ptr<A> p; 
};

};

int main(){
    //haizei::A a;
    haizei::shared_ptr<haizei::A> p(new haizei::A()), q = p;
    haizei::shared_ptr<haizei::A> k(new haizei::A());
    cout << p->x << " " << p->y << endl;
    cout << q->x << " " << q->y << endl;
    cout << p.use_count() << endl;//2  p和q指向同一个对象1 k单独2
    q = k;
    cout << p.use_count() << endl;//1  k和q指向同一个对象2 p单独1
    //p = k;//第一个对象A被析构
    //cout << p.use_count() << endl;//3 pqk指向同一个对象2
   /*
    p->p = k;
    k->p = p;//环形引用 两个对象没有被析构 想析构1必须先析构2 想析构2必须先析构1
    //为了解决环形引用引入weak_ptr 和 unique_ptr
    */
    return 0;
}
```

## weak_ptr

std::weak_ptr 是一种智能指针，它对被 std::shared_ptr 管理的对象存在非拥有性（“弱”）引用。在访问所引用的对象前必须先转换为 std::shared_ptr。

- std::weak_ptr 用来表达临时所有权的概念：当某个对象只有存在时才需要被访问，而且随时可能被他人删除时，可以使用 std::weak_ptr 来跟踪该对象。需要获得临时所有权时，则将其转换为 std::shared_ptr，此时如果原来的 std::shared_ptr 被销毁，则该对象的生命期将被延长至这个临时的 std::shared_ptr 同样被销毁为止。

- std::weak_ptr 的另一用法是打断 std::shared_ptr 所管理的对象组成的环状引用。若这种环被孤立（例如无指向环中的外部共享指针），则 shared_ptr 引用计数无法抵达零，而内存被泄露。能令环中的指针之一为弱指针以避免此情况。

```cpp
#include<iostream>
#include<string>
#include<memory>
using namespace std;

class A{
public:
    string name;
    A(const string &theName) : name(theName){}
};

int main(){
    auto x = make_shared<A>("A");
    cout << x->name << endl;
    {
        auto y = x;
        cout << y->name << endl;
    }
    weak_ptr<A> w = x;
    //将作用域去掉下面输出w不为空 因为临时的y为shared_ptr形式一直存在 对象一直没被释放掉
    //如果有作用域符号 下面输出w为空  临时的y shared_ptr已经被释放 x = nullptr说明没有shared_ptr指向这个对象 对象就被释放掉了
    {
        auto y = w.lock();
        cout << y->name << endl;
    }
    x = nullptr;
    {
        auto y = w.lock();
        if(y) cout << "w不为空" << endl;
        else cout << "w为空" << endl;
    }
    return 0;
}
```



# 类和动态内存分配（new和delete）

动态内存分配，在程序运行期间才确定内存的大小，而不是编译期间

编译期间确定程序的内存大小可能会造成浪费

而动态的内存分配需要用到new和delete

在类中使用动态内存分配就必须要有析构函数来释放内存



# END

有多大的愿望 就得付出多大的努力

在生活工作中 不要承担自己能力所不能胜任的东西

对于做自己的事情 多长时间都是有价值 如果我们出卖时间 我们肯定是受到剥削的





# 面试

## 1.c++空的类有哪些成员函数

[https://blog.csdn.net/ox0080/article/details/84162776](https://blog.csdn.net/ox0080/article/details/84162776)

- 缺省构造函数
- 缺省拷贝构造函数
- 缺省析构函数
- 缺省赋值运算符
- 缺省取地址运算符
- 缺省取地址运算符const

## 2.初始化列表的作用

[https://blog.csdn.net/lws123253/article/details/80368047](https://blog.csdn.net/lws123253/article/details/80368047)

构造过程：

1. 初始化阶段：所有类型的成员都会先进行初始化再进入构造函数的执行逻辑 即使没出现在初始化列表中
2. 计算阶段：通常指复制阶段

使用原因：

> 初始化成员属性有两种方法：初始化列表和构造函数体内的赋值
>
> 对于内置类型int、float等类型来说初始化初始化列表和构造函数体内的赋值速度是一样的
>
> 但对于自定义的类类型来说，使用初始化列表少调用了一次构造函数，直接copy构造，比先默认构造再赋值操作快

必须使用初始化列表的类型：

1. 常量const不能赋值
2. 引用的类型不能赋值
3. 没有默认构造函数的类类型

成员属性初始化顺序：

- 按照定义的顺序来初始化，并非初始化列表中的顺序

## 3.在构造/析构函数中调用虚函数

在构造/析构函数中调用虚函数bu会实现多态

当构造一个派生类时，会先构造基类，这时在构造函数中调用虚函数不会实现多态，只会调用基类的对应函数，再构造派生类，再构造函数中调用虚函数会调用派生类对应的函数

## 4.多态的应用场景

[http://c.biancheng.net/view/265.html](http://c.biancheng.net/view/265.html)

使用多态能够增强程序的可扩充性，即程序需要修改或增加功能时，只需改动或增加较少的代码。此外，使用多态也能起到精简代码的作用。

## 5.重载、覆盖（重写）和隐藏

[https://www.cnblogs.com/tianzeng/p/9775672.html](https://www.cnblogs.com/tianzeng/p/9775672.html)

重载：

1. 相同的范围（在同一个作用域中）
2. 函数名字相同
3. 参数不同列表
4. virtual 关键字可有可无
5. 返回类型可以不同

覆盖：

1. 不在同一个作用域（分别位于派生类与基类）
2. 函数名字相同
3. 参数相同列表（参数个数，两个参数列表对应的类型）
4. 基类函数必须有 virtual 关键字，不能有 static，大概是多态的原因吧...
5. 返回值类型（或是协变），否则报错
6. 重写函数的访问修饰符可以不同。尽管 virtual 是 private 的，派生类中重写改写为 public,protected 也是可以的

隐藏：

1. 不在同一个作用域（分别位于派生类与基类）
2. 函数名字相同
3. 返回类型可以不同
4. 参数不同，此时，不论有无virtual关键字，基类的函数将被隐藏（注意别与重载混淆）而不是被重写
5. 参数相同，但是基类函数有无virtual关键字都会被隐藏。此时，基类的函数被隐藏（注意别与覆盖混淆）

## 6.指针和引用的区别

[https://www.cnblogs.com/dolphin0520/archive/2011/04/03/2004869.html](https://www.cnblogs.com/dolphin0520/archive/2011/04/03/2004869.html)

1. 指针：指针是一个变量，只不过这个变量存储的是一个地址，指向内存的一个存储单元；而引用跟原来的变量实质上是同一个东西，只不过是原变量的一个别名而已。如：

   int a=1;int *p=&a;

   int a=1;int &b=a;

   上面定义了一个整形变量和一个指针变量p，该指针变量指向a的存储单元，即p的值是a存储单元的地址。

   而下面2句定义了一个整形变量a和这个整形a的引用b，事实上a和b是同一个东西，在内存占有同一个存储单元。

2. 可以有const指针，但是没有const引用；

3. 指针可以有多级，但是引用只能是一级（int **p；合法 而 int &&a是不合法的）

4. 指针的值可以为空，但是引用的值不能为NULL，并且引用在定义的时候必须初始化；

5. 指针的值在初始化后可以改变，即指向其它的存储单元，而引用在进行初始化后就不会再改变了。

6. "sizeof引用"得到的是所指向的变量(对象)的大小，而"sizeof指针"得到的是指针本身的大小；

7. 指针和引用的自增(++)运算意义不一样；

## 7.进程与线程的区别

看了一遍排在前面的答案，类似”**进程是资源分配的最小单位，线程是CPU调度的最小单位“**这样的回答感觉太抽象，都不太容易让人理解。

做个简单的比喻：进程=火车，线程=车厢

- 线程在进程下行进（单纯的车厢无法运行）
- 一个进程可以包含多个线程（一辆火车可以有多个车厢）
- 不同进程间数据很难共享（一辆火车上的乘客很难换到另外一辆火车，比如站点换乘）
- 同一进程下不同线程间数据很易共享（A车厢换到B车厢很容易）
- 进程要比线程消耗更多的计算机资源（采用多列火车相比多个车厢更耗资源）
- 进程间不会相互影响，一个线程挂掉将导致整个进程挂掉（一列火车不会影响到另外一列火车，但是如果一列火车上中间的一节车厢着火了，将影响到所有车厢）
- 进程可以拓展到多机，进程最多适合多核（不同火车可以开在多个轨道上，同一火车的车厢不能在行进的不同的轨道上）
- 进程使用的内存地址可以上锁，即一个线程使用某些共享内存时，其他线程必须等它结束，才能使用这一块内存。（比如火车上的洗手间）－"互斥锁"
- 进程使用的内存地址可以限定使用量（比如火车上的餐厅，最多只允许多少人进入，如果满了需要在门口等，等有人出来了才能进去）－“信号量”

https://blog.csdn.net/whui19890911/article/details/8662850

https://blog.csdn.net/weixin_44363885/article/details/100108553

线程是轻量级的进程

线程可以共享内存空间，进程间都是独立的

进程不能进行资源共享，安全但是切换进程代价大

线程可以进行资源共享，如果多个线程访问一片内存空间会出问题

1个cpu只能处理一个进程 将一个进程分解成多个线程，分布在多个cpu---并发

## 8.存储区

[https://blog.csdn.net/cherrydreamsover/article/details/81627855](https://blog.csdn.net/cherrydreamsover/article/details/81627855)

1. **栈**：在执行函数时，函数内局部变量的存储单元都可以在栈上创建，函数执行结束时这些存储单元自动被释放。栈内存分配运算内置于处理器的指令集中，效率很高，但是分配的内存容量有限。
2. **堆**：就是那些由`malloc`等分配的内存块，用`free`来结束自己的生命的。
3. **自由存储区**：就是那些由 `new`分配的内存块，他们的释放编译器不去管，由我们的应用程序去控制，一般一个`new`就要对应一个 `delete`。如果程序员没有释放掉，那么在程序结束后，操作系统会自动回收。（可以在堆区，可以在静态存储区）
4. **全局/静态存储区**：全局变量和静态变量被分配到同一块内存中，在以前的C语言中，全局变量又分为初始化的和未初始化的，在C++里面没有这个区分了，他们共同占用同一块内存区。
5. **常量存储区**：这是一块比较特殊的存储区，他们里面存放的是常量，不允许修改。
6. **代码区**：存放函数的二进制代码

## 9.C和C++内存管理区别

[https://blog.csdn.net/cherrydreamsover/article/details/81022039](https://blog.csdn.net/cherrydreamsover/article/details/81022039)

内存管理方面的不同（new/delete、malloc/free的区别）：

1. new/delete是操作符，malloc/free是函数。（本身性质不同）
2. new在使用时会调用malloc先开空间在调用构造函数进行初始化，delete会先调用析构函数清理对象，然后在调用free释放空间。（申请内存底层不同是否调用构造函数和析构函数）
3. malloc在堆上开空间，new是在自由存储区（堆或者 静态存储区）开空间。（开辟空间的位置不同）

4. malloc开空间的时候需要指定空间的大小，而new只需要类型名（AA a1=new AA();）（开辟空间大小是否要指定）

5. malloc开辟的空间可以给 单个对象使用，也可以给对象数组使用释放交给free;new 给对象数组开空间使用new[],对应释放使用delete[].（给对象和对象数组开辟空间的不同）

6. malloc申请空间成功返回void\*的指针，失败返回NULL；new 申请对象空间成功后返回对象指针，失败会抛异常（成功返回值不同，失败返回的也不同*）

7. malloc开辟的空间如果不够用可以使用realloc来扩大空间，但是new不可以。（能否扩容）

## 10.用户态和内核态

[https://www.cnblogs.com/maxigang/p/9041080.html](https://www.cnblogs.com/maxigang/p/9041080.html)

## 11.堆和栈的区别

[https://blog.csdn.net/cherrydreamsover/article/details/81627855](https://blog.csdn.net/cherrydreamsover/article/details/81627855)

1. 管理方式不同：栈是由编译器自动申请和释放空间，堆是需要程序员手动申请和释放；
2. 空间大小不同：栈的空间是有限的，在32位平台下，VC6下默认为1M，堆最大可以到4G；
3. 能否产生碎片：栈和数据结构中的栈原理相同，在弹出一个元素之前，上一个已经弹出了，不会产生碎片，如果不停地调用malloc、free对造成内存碎片很多；
4. 生长方向不同：堆生长方向是向上的，也就是向着内存地址增加的方向，栈刚好相反，向着内存减小的方向生长。
5. 分配方式不同：堆都是动态分配的，没有静态分配的堆。栈有静态分配和动态分配。静态分配是编译器完成的，比如局部变量的分配。动态分配由 malloc 函数进行分配，但是栈的动态分配和堆是不同的，它的动态分配是由编译器进行释放，无需我们手工实现。
6. 分配效率不同：栈的效率比堆高很多。栈是机器系统提供的数据结构，计算机在底层提供栈的支持，分配专门的寄存器来存放栈的地址，压栈出栈都有相应的指令，因此比较快。堆是由库函数提供的，机制很复杂，库函数会按照一定的算法进行搜索内存，因此比较慢。

## 12.哈希表索引快？冲突解决？

[https://www.cnblogs.com/zzdbullet/p/10512670.html](https://www.cnblogs.com/zzdbullet/p/10512670.html)

数值-->通过哈希函数的映射-->得到存储该数值的地址-->将数值放在对应的地址

如果要寻找value，则我们只需要通过映射得到地址，对比地址上的数是否为value即可

查找的时间复杂度O（1）

冲突处理方法：

1. 开放定址法：一个哈希函数，一次哈希得到的地址冲突就再一次哈希
2. 拉链法：在每个地址上放一个链表即可
3. 再哈希法：2个及以上的哈希函数，一种哈希得到的地址冲突就用另一种哈希
4. 建立公共溢出区：凡是冲突的数值都放在溢出区

## 13.map和hashtable的区别

1. map底层红黑树 hashtable底层哈希表
2. map有序的 hashtable