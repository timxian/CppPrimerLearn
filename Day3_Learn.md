# Day3 学习笔记

## string相关

### 直接初始化 vs 拷贝初始化

``` c++
string s = "hiya";      // 拷贝初始化
string s1("hiya");      // 直接初始化
string s2(10, 'c');     // 直接初始化
```

### string::size_type类型

``` c++
string tmp("hello");
auto len = tmp.size();  // 此处tmp.size()返回的类型是一个size_type类型，是一个无符号类型，切记在表达式中如果已经有size()，就不要再用int以免混用int和unsigned带来问题
tmp.size() < n          // 这里如果n是一个负值int，则这个判断永远都是true，因为n在转化为size_type的时候会变成一个非常大的值
```

### 字面值和string对象的相加

1. 两个字面值不能直接相加，例如："hello" + ", " 是错误的，因为是字面值
2. string可以与字面值相加
3. 在C++中为了兼容C，字符串的字面值不是string对象

## vector相关

1. vector是一个模板二位类型
2. 通过vector生成的类型必须包含vector中的元素类型
3. 元素只能是对象，因为引用不是对象，所以不存在引用作为元素

``` c++
vector<int> v1(10);     // 10个元素，值为0
vector<int> v2{10};     // 1个元素，值为10
vector<int> v3(10,1);   // 10个元素，值为1
vector<int> v4{10,1};   // 2个元素，值为10,1；

vector<string> v5{"hi"};    // 正确
vector<string> v6("hi");    // 错误
vector<string> v7{10, "hi"} // 10个元素，值为“hi”
vector<string> v8{10}       // 10个元素，初始值为空字符串
```

`注意` 如果循环体内部包含向vector对象添加元素的语句，则不能使用范围for循环“for (declaration : expression)”，因为范围for要求vector的size不变

## Iteration相关

1. 标准库容器支持迭代器的\==或!=，所以一般在循环中都会使用迭代器并且使用\==或者!=来判定，而不是用下标和<,>这种比较运算符来使用
2. vector<int>::iterator， 可读可写，vector<int>::const_iterator，只可读
3. 任何改变vector对象容量的操作都会使得vector对象的迭代器失效

## 数组相关

1. 数组的名字是一个指向数组首元素的指针
2. 数组的下标运算符的索引值不是无符号类型，也就是可以为负数

``` c++
int ia[] = {0,2,3,6,8};     // 定义一个数组，5个元素
int *p = &ia[2];            // p指向ia[2]元素的地址，值为3
int j = p[1];               // j是p指针+1，也就是ia[3]，值为6
int k = p[-2];              // k是p指针-2，也就是ia[0]，值为0
```

## C风格字符串和旧代码的接口

1. 在C下面的字符串实际上是一个结尾是'\0'的char数组
2. 两个C字符串是不能直接比较大小的，必须通过strcmp函数来比较

``` c++
const char ca1[] = "A string example";
const char ca2[] = "A different string";
if (ca1 < ca2) // 这个是两个未定义的无关地址比较，会报错；因为ca1，ca2实际上是指向数组第一个元素的const char*，比较两个地址没有太大意义
```

关于旧代码的接口，string提供了相关初始化和赋值的方法。

``` c++
char *str = s;      // 错误：不能用string对象初始化char*
const char *str = c.c_str();    // 正确
```

`注意` 如果执行完 c_str()函数后程序想一直都能使用其返回的数组，最好将该数组重新拷贝一份。不然const char*指针有可能指向别的内存地址

