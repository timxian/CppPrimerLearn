# Day2 学习笔记

## 关于基本类型

1. 可寻址的最小内存块为byte
2. 储存的基本单元成为word
3. 前缀和后缀的几个重点关键
   1. u => char16_t
   2. U => char32_t
   3. L => wchar_t
   4. u8 => char

## 变量

对象通常是指一块能存储数据并具有某种类型的内存空间

### 1. 列表初始化

下面初始化方法都是可行的；在C++11中，通过{}来进行初始化得到全面应用

``` c++
int test = 0;
int test = {0};
int test{0};
int test(0);
```

为何C++11要以花括号作为列表初始化的标准形式，因为在初始化过程中，如果出现信息丢失，则编译器会报错。请看一下例子：

``` c++
long double pi = 3.1415926;
int a{pi}, b{pi};   // 编译器会报错，因为信息出现丢失
int a(pi), b(pi);   // 正确，编译器会自动转换类型，造成信息丢失
```

### 2. 默认初始化

定义与任何函数体之外的内置类型的变量被初始化为0，定义在函数体内部的内置类型变量将不被初始化。这个例外会造成很严重的后果，所以**建议**所有内置类型的变量都要初始化

### 3. 声明和定义

变量只能被定义一次，但是可以被多次声明。如果要在多个文件中使用同一个变量，变量的定义必须只能出现在一个文件中，每个用到这个变量的文件都必须对其进行声明；

``` C++
extern int i;   // 声明i而非定义
int j;  // 声明并且定义了j
extern double pi = 3.14 // 只要初始化就是定义

```

### 4. 引用

通常我们讲的引用指的是左值引用。

1. 引用实际上就是一个对象的别名；
2. 引用一定需要在定义的时候初始化，一旦定义无法修改；
3. 定义引用是将引用和初始值绑定在一起，而不是拷贝；
4. 引用不是实际的对象，没有地址

以下是一些引用的错误例子：

``` c++
int &refVal = 10;   // 错误，因为引用只允许将一个对象对其赋值

double num = 2.13；
int &refVal1 = num;     // 错误，因为num的类型是double，而引用的类型是int
```

### 5. 指针

1. 指针是一个对象，有具体地址，实现对其他对象的间接访问；
2. 允许对指针赋值和拷贝；
3. 块作用域中如果指针没初始化，则指针有一个不确定值；
4. 指针与指向的对象的类型需要匹配

**允许的指针值：**

1. 指向一个对象
2. 指向相邻对象所占空间的下一个位置
3. 空指针，以为这指针没有指向任何对象
4. 无效指针，也就是上述情况之外的值

``` c++
int i = 34;
int *pi = 0; // 定义了一个空指针
pi = &i; // 指针指向i
*pi = 13； // i的值改变成13，而指针pi依然指向i
```

**void* 指针：**

1. 可以存放任意类型的指针
2. 可以看做是一个内存空间

**指向指针的指针：**

通过解引用符*一层一层的找到指向的内容：

``` c++
int ival = 12;
int *p = &ival;
int **pp = &p;
```

**指向指针的引用：**

``` c++
int i = 34;
int *p； // 定义一个指针
int *&r = p;  // 定义一个指向指针p的引用

r = &i; // 说明引用r对应的p指针存放i的地址
*r = 0; // r就是指针p，*r等价于*p，也就是对i赋值0
```

## const相关

1. const用于定义一个不可变更的变量。
2. 通常const的定义只对一个文件有效，如果需要多个文件共享const的变量，需要在const前面加extern关键字

### const的引用

``` c++
int i = 43;
int &r1 = i;        // 定义r1为i的引用
const int &r2 = i;  // 定义r2为i的常量引用
r1 = 0;             // 正确，i被改成0；
r2 = 0;             // 错误，不能通过r2对i进行赋值改变
```

### 指针和const

``` c++
const double pi = 3.14;     // 定义了pi的const double
double *ptr = &pi;          // 错误，因为ptr是个double指针
const double *cptr = &pi;   // 正确，因为cptr是个const double指针
*cptr = 42;                 // 错误，因为不允许对cptr所指向的对象进行改变，即pi进行赋值
```

### constexpr变量

1. 一个常量表达式需要类型定义为const T，同时初始化的时候，初始化表达式需要也是常量表达式；
2. 如果一个表达式的初始化需要在运行时才能确定，则即便类型是const T也不是常量表达式
3. 因此C++11引入constexpr，在编译时验证定义是否是一个常量表达式

## 处理类型

### 类型别名

``` c++
typedef char *pstring;
const pstring cstr = 0;     // cstr是指向char的常量指针
const pstring *ptr;         // ptr是一个指针，他的对象是指向char的常量指针
```

### decltype

选择并返回操作数的数据类型

``` c++
decltype(f()) sum = x;      // sum的类型是f()返回的类型

const int ci = 0, &cj = ci; // 定义const int对象ci，引用cj（绑定的对象是const int）
decltype(ci) x = 9;         // x的类型是const int
decltype(cj) y = x;         // y的类型是const int&
decltype(cj) z;             // 错误，因为z是一个const int&类型，需要初始化
```