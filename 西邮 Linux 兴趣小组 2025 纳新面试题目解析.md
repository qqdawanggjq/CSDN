
# 西邮 Linux 兴趣小组 2025 纳新面试题目解析

***
    
## 0. 拼命的企鹅

一只企鹅在爬山，每隔一段路都会遇到一块石头。第 1 块石头重量是 a，每往上走一段路，石头重量就会变成上一段的平方。企鹅可以选择把某些石头捡起来，最后把捡到的石头重量相乘。

它怎样捡石头，才能得到重量乘积恰好是 a 的 b 次方的石头？（比如 b = 173 时， 要捡哪些石头？）
### 题目分析
- 重量相乘相当于指数相加
- 第一块石头指数是 2^0^ 之后递加2^1^,   2^2^......
- 将b写为二进制
>173==10101101
- 从右往左哪里是1, 选那块石头
>也就是第1 3 4 6 8块石头

### 答案
把指数  b  转成二进制，二进制位为  1  的位置，就选对应的那块石头

***

## 1. 西邮Linux欢迎你啊

请解释以下代码的运行结果。
```c
int main() {
    if (printf("Hi guys! ") || printf("Xiyou Linux ")) {
        printf("%d\n", printf("Welcome to Xiyou Linux 2%d", printf("")));
    }
    return 0;
}
```
### 题目分析
- 打印第一句后返回非0值，或短路，后面不执行
- 从后往前打印，空字符返回0，然后是一个非0常数
>`printf`返回值是成功打印字符个数
### 答案

Hi guys! Welcome to Xiyou Linux 2025
***


## 2. 可以和 \0 组一辈子字符串吗？

你能找到成功打印的语句吗？你能看出每个 if 中函数的返回值吗？这些函数各有什么特点？
```c
int main() {
    char p1[] = { 'W', 'e', 'l', 'c', 'o', 'm', 'e', '\0' };
    char p2[] = "Join us\0", p4[] = "xiyou_linux_group";
    const char* p3 = "Xiyou Linux Group\0\n2025\0";
    if (strcmp(p1, p2)) {
        printf("%s to %s!\n", p1, p2);
    }
    if (strlen(p3) > sizeof(p3)) {
        printf("%s", p3);
    }
    if (sizeof(p1) == sizeof(p2)) {
        printf("%s", p4);
    }
    return 0;
}
```
### 题目分析
`if (strcmp(p1, p2)) {
printf(“%s to %s!\n”, p1, p2);
}`
- p1 != p2,返回一个非0值，成功执行

 `if (strlen(p3) > sizeof(p3)) {
        printf("%s", p3);
    }`

- `strlen`可以访问指针里的内容，直到\0  17
- `size of`算一个指针的大小  8
- 成功执行

`if (sizeof(p1) == sizeof(p2)) {
printf(“%s”, p4);
}`

- p1与p2长度相等，成功执行
### 答案
Welcome to Join us!
Xiyou Linux Group
xiyou_linux_group


***

## 3. 数学没问题，浮点数有鬼

这个程序的输出是什么？解释为什么会这样？
```c
int main() {
    float a1 = 0.3, b1 = 6e-1, sum1 = 0.9;
    printf("a1 + b1 %s sum1\n", (a1 + b1 == sum1) ? "==" : "!=");
    float a2 = 0x0.3p0, b2 = 0x6p-4, sum2 = 0x0.9p0;
    printf("a2 + b2 %s sum2\n", (a2 + b2 == sum2) ? "==" : "!=");
    return 0;
}
```
### 题目分析
- 十进制小数转二进制小数大多是无限循环的，会有误差
a1 + b1 != sum1
- 0x是16进制的标志，`p常数`相当于`*2的常数次方`
a2 +b2 ==  sum2
### 答案
a1 + b1 != sum1
a2 + b2 == sum2

***

## 4. 不合群的数字

在一个数组中，所有数字都出现了偶数次，只有两个数字出现了奇数次，请聪明的你帮我看看以下的代码是如何找到这两个数字的呢？
```c
void findUndercoverIDs(int nums[], int size) {
    int xorAll = 0,id_a = 0,id_b = 0;
    for (int i = 0; i < size; i++) {
        xorAll ^= nums[i];
    }
    int diffBit = xorAll & -xorAll;
    for (int i = 0; i < size; i++) {
        if(nums[i] & diffBit){
            id_a ^= nums[i];
        } else {
            id_b ^= nums[i];
        }
    }
    printf("These nums are %d %d\n", id_a, id_b);
}
```
### 题目分析
- `xorAll ^= nums[i];`
1. 相同的数异或结果为 0，0 异或任何数等于该数本身

2. 偶数次出现的数异或后会相互抵消，只剩奇数次的数

3. 最后只剩下俩个奇数异或

- ` int diffBit = xorAll & -xorAll;`
能取出 ` xorAll  `最右侧的 1
这一位上，两个目标数必然一个是 0、一个是 1，以此可以把数组分成两组

- `if(nums[i] & diffBit){
            id_a ^= nums[i];
        } else {
            id_b ^= nums[i];
        }`
该位为1的数字分为一组，偶数出现的数字被抵消，剩下目标数字
同理，该位为0的目标数字也被取出
### 答案
如上
***

## 5. 会一直循环吗？

你了解 argc 和 argv 吗，程序的输出是什么？为什么会这样？
```c
int main(int argc, char* argv[]) {
    printf("argc = %d\n", argc);
    while (argc++ > 0) {
        if(argc < 0){
            printf("argv[0] %s\n", argv[0]);
            break;
        }
    }
    printf("argc = %d\n", argc);
    return 0;
}
```
### 题目分析
 -  `argc`  和  `argv  `是 C 语言  `main  `函数接收命令行参数的两个形参，程序从终端启动时，操作系统会把命令行上的内容传进来
 `int main(int argc, char *argv[])`
- argc = argument count，参数个数，是个整数。它包含程序名本身，所以最少是 1。
>比如程序叫`text` , `argc`就等于1

- argv = argument vector，参数内容，是一个**字符串指针数组**， argv[0]  到  argv[argc-1]  依次是各个参数， argv[argc]  保证是  NULL （哨兵值）。
> `char*`是字符指针，但如果它指向一个字符串的首字符地址时，它就是字符串指针
-  `while (argc++ > 0) {
        if(argc < 0){
            printf("argv[0] %s\n", argv[0]);
            break;
        }
    }`
    执行过程
 
1. 初始  `argc` ≥ 1 ，进入  `while(argc++ > 0) `：后置自增，先判断再自增
​
2. 每次循环  `argc ` 不断 +1，有符号 int 达到最大值后继续自增会溢出为负数
​
3. 当  `argc  `变为负数时，满足 ` if(argc < 0) `，打印 ` argv[0]  `并跳出循环
​
4. 最后打印溢出后的 ` argc  `值
### 答案
argc = 1
argv[0] （***程序的名称***）
argc = -2147483648

***

## 6. const 与指针：谁能动，谁不能动？
```c
struct P {
    int x;
    const int y;
};

int main() {
    struct P p1 = { 10, 20 }, p2 = { 30, 40 };
    const struct P p3 = { 50, 60 };
    struct P* const ptr1 = &p1;
    const struct P* ptr2 = &p2;
    const struct P* const ptr3 = &p3;
    return 0;
}
```
说说下列操作是否合法，并解释原因：

ptr1->x = 100, ptr2->x = 300, ptr3->x = 500

ptr1->y = 200, ptr1 = &p2, ptr2->y = 400

ptr2 = &p1, ptr3->y = 600, ptr3 = &p1
### 题目分析
`ptr1->x = 100`

合法

`ptr2->x = 300`

ptr2内容被锁，不合法

`ptr3->x = 500`

ptr3内容被锁，不合法

`ptr1->y = 200`

y内容被锁，不合法

`ptr1 = &p2`

ptr1指向被锁，不合法

`ptr2->y = 400`

y内容被锁，不合法

`ptr2 = &p1`

合法

`ptr3->y = 600`

ptr3和y的内容都被锁，不合法

`ptr3 = &p1`

ptr3指向被锁，不合法
### 答案
如上
***
## 7. 指针！数组!

在主函数中定义如下变量:
```c
int main() {
    int a[3] = { 2, 4, 8 };
    int(*b)[3] = &a;
    int* c[3] = { a, a + 1, a + 2 };
    int (*f1(int))(int*, int);
    return 0;
}
```
说说这几个表达式的输出分别是什么？

a, *b, *b + 1, b, b + 1, * (*b + 1), c, sizeof(a), sizeof(b), sizeof(&a), sizeof(f1)
### 题目分析
`int (*f1(int))(int*, int);`
>f1函数声明
参数为int , 返回值是函数指针
该函数参数是int 指针 ，int ， 返回int 

**什么是函数的入口地址？**
 
程序运行时，每个函数的二进制机器指令都会加载到内存的代码段区域
 
- 函数入口地址，就是这段函数指令的第一个字节所在的内存地址

- 通俗类比：它就是函数在内存里的「门牌号」。CPU调用函数时，就是跳到这个地址，开始逐行执行函数里的指令

- 函数指针变量里存储的值，就是这个入口地址


- 绝大多数场景下，函数名会隐式转换成「指向该函数的指针」，也就是自动变成它的入口地址。


`a`

退化为数组首元素地址，`&a[0]`

`*b`

b解引用得到a数组，数组退化为数组首元素地址，`&a[0]`

`*b + 1`

首元素地址向后偏移一个单位，指向第二个元素，`&a[1]`

`b`

一个有三个整形元素的数组的地址`&a`

`b + 1`

指针向后偏移`3*4==12`个字节，指向越界地址

`* (*b + 1)`

对指向a数组第二个元素的指针解引用，得到`4`

`c`

指针数组名退化，得到指向 指向a数组首元素的指针 的指针

`sizeof(a)`

数组字节大小: `3*4==12`


`sizeof(b)`

指针大小 `8`

`sizeof(&a)`

数组地址==指针 `8`

`sizeof(f1)`

函数名退化为函数指针，大小`8`


### 答案
如上
***
## 8. 全局还是局部！！！

观察程序输出，思考为什么？
```c
int g;
int func() {
    static int j = 98;
    j += g;
    return j;
}

int main() {
    g += 3;
    char arr[6] = {};
    arr[1] = func();
    arr[0] = func();
    arr[2] = arr[3] = func() + 1;
    arr[4] = func() + 1;
    printf("%s linux\n",arr);
    return 0;
}
```
### 题目分析
1. 全局变量 `g` ：全局变量默认初始化为`0`，执行` g += 3 `后， `g = 3 `。
​
2. 静态局部变量 j ：` static int j = 98 `只在第一次调用函数时初始化，后续调用保留值，每次调用 `j += g `也就是加3。
​
3. 数组 `arr[6] `初始全为0（即字符串结束符` \0` ），按调用顺序赋值：
​
- 第1次调用 func() ：` j = 98+3 = 101 ， arr[1] = 101 `
​
- 第2次调用 func() ：` j = 101+3 = 104 ， arr[0] = 104 `
​
- 第3次调用 func() ：` j = 104+3 = 107 ， arr[2] = arr[3] = 107+1 = 108 `
​
- 第4次调用 func() ：` j = 107+3 = 110 ， arr[4] = 110+1 = 111 `
4. 转ASCII码： 104→h、101→e、108→l、108→l、111→o、0→\0 ，字符串为` hello `。
### 答案
`hello  Linux`
***

## 9. 宏函数指针

观察程序结果，说说程序运行的过程：
```c
#define CALL_MAIN(main, x) (*(int (*)(int))*main)(x);
#define DOUBLE(x) 2 * x
int (*registry[1])(int);
int main(int argc) {
    if (argc > 2e3) return 0;
    printf("%d ", argc + 1);
    *registry = (int(*)(int))main;
    CALL_MAIN(registry, DOUBLE(argc + 1));
    return 0;
}
```
### 题目分析
- `(*(int (*)(int))*main)(x)`
>`(int (*)(int))`
强制转换为函数指针，参数为`int`,返回值为`int`
>>**强制转换后无论之前函数上传的是什么，类型被强转为`int`
>>(比如本来上传`int`类型的参数`36`，被强转为`char`,当成ASCII解读)
>

>`int (*registry[1])(int)`
>这是一个函数指针数组


>`*main`
>带入题目为`*registry`
>一个函数指针

>`*(int (*)(int))*main`
>对函数指针解引用，得到参数`int`,返回值`int`的函数

>`(*(int (*)(int))*main)(x)`
>最后调用这个函数

- 程序启动， `argc `初始为1
​
- 每次进入` main `先判断：若 `argc > 2000` 直接返回；否则打印 `argc+1` ，再递归调用 `main `，参数为 `2*argc + 1 `
>`DOUBLE(argc + 1)`
​
- 递归序列：
 `argc=1` →打印2→调用参数3
 `argc=3 `→打印4→调用参数7
 `argc=7 `→打印8→调用参数15
……
 `argc=1023 `→打印1024→调用参数2047
 `argc=2047 `→大于2000，直接返回，不打印
### 答案
2 4 8 16 32 64 128 256 512 1024
***

## 10. 拼接 排序 去重

本题要求你编写以下函数，不能改动 main 函数里的代码。实现对 arr1 和 arr2 的拼接、排序和去重。你需要自行定义 result 结构体并使用 malloc 手动开辟内存。
```c
int main() {
    int arr1[] = { 6, 1, 2, 1, 9, 1, 3, 2, 6, 2 };
    int arr2[] = { 4, 2, 2, 1, 6, 2 };
    int len1 = sizeof(arr1) / sizeof(arr1[0]);
    int len2 = sizeof(arr2) / sizeof(arr2[0]);

    struct result result;
    your_concat(arr1, len1, arr2, len2, result);
    print_result(result);
    your_sort(result);
    print_result(result);
    your_dedup(result);
    print_result(result);
    free(result.arr);
    return 0;
}
```
### 题目分析
```c
#include<stdio.h>
 #include<stdlib.h>
struct result
{
    int* arr;
    int len;
};
void your_concat(int *arr1,int len1,int *arr2,int len2,struct result *res)
{
    res->len=len1+len2;
    res->arr=(int*)malloc((res->len)*sizeof(int));
    for(int i=0;i<len1;i++)
    {
        res->arr[i]=arr1[i];
    }
    for(int i=0;i<len2;i++)
    {
        res->arr[len1+i]=arr2[i];
    }
}
void print_result(struct result res)
{
    for(int i=0;i<res.len;i++)
    {
        printf("%d",res.arr[i]);
    }
    printf("\n");
}
void your_sort(struct result res)
{
    for(int i=0;i<res.len-1;i++)
    {
        for(int j=0;j<res.len-1-i;j++)
        {
            if(res.arr[j]>res.arr[j+1])
            {
                int t=res.arr[j+1];
                res.arr[j+1]=res.arr[j];
                res.arr[j]=t;
            }
        }
    }

}
void your_dedup(struct result *res)
{
    int slow=0;
    for(int fast=1;fast<res->len;fast++)
    {
        if(res->arr[fast]!=res->arr[slow])
        {
            slow++;
            res->arr[slow]=res->arr[fast];
        }
    }
    res->len=slow+1;
}
int main()
 {
    int arr1[] = { 6, 1, 2, 1, 9, 1, 3, 2, 6, 2 };
    int arr2[] = { 4, 2, 2, 1, 6, 2 };
    int len1 = sizeof(arr1) / sizeof(arr1[0]);
    int len2 = sizeof(arr2) / sizeof(arr2[0]);

    struct result result;
    your_concat(arr1, len1, arr2, len2, &result);
    print_result(result);
    your_sort(result);
    print_result(result);
    your_dedup(&result);
    print_result(result);
    free(result.arr);
    return 0;
}
```
### 答案
如上
***
## 11. 指针魔法

用你智慧的眼睛，透过这指针魔法的表象，看清其本质：
```c
void magic(int(*pa)[6], int** pp) {
    **pp += (*pa)[2];
    *pp = (*pa) + 5;
    **pp -= (*pa)[0];
    *pp = (*pa) + ((*(*pa + 3) & 1) ? 3 : 1);
    *(*pp) += *(*pp - 1);
    *pp = (*pa) + 2;
}
int main() {
    int a[6] = { 2, 4, 6, 8, 10, 12 };
    int* p = a + 1,** pp = &p;
    magic(&a, pp);
    printf("%d %d\n%d %d %d\n%d %d\n",*p,**pp,a[1],a[2],a[3],a[5],p-a);
    return 0;
}
```
### 题目分析
- 
```c
    **pp += (*pa)[2];
    *pp = (*pa) + 5;
    **pp -= (*pa)[0];
    *pp = (*pa) + ((*(*pa + 3) & 1) ? 3 : 1);
    *(*pp) += *(*pp - 1);
    *pp = (*pa) + 2;
   ```
-    `**pp`=`*p`=`a[1]`=4+6=10;
- `*pp`=`p`->`a[5]`;
- `**pp`=`*p`=`a[5]`=12-2=10;
- `*pp`=`p`->`a[0]`+1=`a[1]`;

>`*pa+3`=`a[3]`=8
>8  &  1 == 0
>选1
- `*(*pp)`=`*p`=`a[1]`=10+`a[0]`=10+2=12;
- `*pp`=`p`->`a[2]`;
   
### 答案
```c
6 6
12 6 8
10 2
```
***

## 12. 奇怪的循环

你能看明白这个程序怎样运行吗？试着理解这个程序吧！
```c
union data {
    void**** p;
    char arr[20];
};
typedef struct node {
    int a;
    union data b;
    void (*use)(struct node* n);
    char string[0];
} Node;
void func2(Node* node);

void func1(Node* node) {
    node->use = func2;
    printf("%s\n", node->string);
}
void func2(Node* node) {
    node->use = func1;
    printf("%d\n", ++(node->a));
}
int main() {
    const char* s = "Your journey begins here!";
    Node* P = (Node*)malloc(sizeof(Node) + (strlen(s) + 1) * sizeof(char));
    strcpy(P->string, s);
    P->use = func1;
    P->a = sizeof(Node) * 50 + sizeof(union data);
    while (P->a < 2028) {
        P->use(P);
    }
    free(P);
    return 0;
}
```
### 题目分析
- `char string[0]  `
放在结构体最后面
👉 这个数组本身，不占结构体的内存！它只是一个标记、一个“占位标签”，告诉编译器：结构体结束之后，后面跟着一块额外内存，把那块内存当成这个数组用
数组名  `string ` 本身就代表一个起始地址，它刚好指向结构体的末尾,
` malloc ` 申请的一整块内存是连续的，结构体后面多出来的空间，自然就从  `string  `这个地址开始往后排。
所以你操作  `string  `数组，用的就是后面额外申请的内存，不是数组本身那0字节


- `void func2(Node* node);`
因为后面  `func1 ` 里用到了 ` func2 `，但  `func2 ` 还没写，所以先跟编译器打个招呼：“后面有这个函数，别报错”

- `void**** p`
这是一个四级指针，它指向一个三级指针

- `typedef struct node {
    int a;
    union data b;
    void (*use)(struct node* n);
    char string[0];
} Node;`
将结构体重命名为Node,
如果没有`typedef`则是新创一个变量

- `Node* P = (Node*)malloc(sizeof(Node) + (strlen(s) + 1) * sizeof(char))`
申请一块大小为结构体+字符串+‘0’的内存存入`p`

- 步骤
	- 先将`s`存入`string`
	- `use`指向`func1`
	- 给`a`初始化
>`date`  24
>`a`  4
>`*use`  8
>`string`  0
>`Node`  40
>a=40*50+24==2024

- 
	- 执行`func1`
	使`use`指向`func2`
	打印`string`中的字符串
	- 执行`func2`
	使`use`指向`func2`
	`a`前置加加后打印`a`
	- 到`a`==2028后释放`p`中内存


### 答案
```c
Your journey begins here!
2025
Your journey begins here!
2026
Your journey begins here!
2027
Your journey begins here!
2028
```

***

## 13. GNU/Linux (选做)

注：嘿！你或许对 Linux 命令不是很熟悉，甚至你没听说过 Linux。但别担心，这是选做题，了解 Linux 是加分项，但不了解也不扣分哦！

    你知道 cd 命令的用法与 / ~ - 这些符号的含义吗？
    你知道 Linux 系统如何创建和删除一个目录吗？
    请问你还懂得哪些与 GNU/Linux 相关的知识呢？

### 一、cd 命令与  /   ~   -  符号含义
 
 cd  是 Linux 的目录切换命令（全称 change directory），用来修改当前的工作路径。
 
-  / ：根目录，Linux 文件系统的最顶层，所有目录都从根目录向下延伸。
例： cd /  直接切换到系统根目录。
​
-  ~ ：当前用户的家目录。普通用户默认路径是  /home/用户名 ，root 用户是  /root 。
例： cd ~  一键回到自己的家目录； cd ~/code  进入家目录下的 code 文件夹。
​
-  \- ：上一次所在的目录，用来在两个目录之间快速往返切换。
例：你从  /home  切换到  /etc ，输入  cd -  就立刻回到  /home ，再输一次又跳回  /etc 。<br><br>
 -  . ：代表当前目录。
​
-  .. ：代表上一级目录。例： cd ..  回到上一层文件夹。
 
 
 
### 二、创建和删除目录
 
1. 创建目录： mkdir （make directory）
 
- 创建单个目录： mkdir 目录名 
例： mkdir test  在当前目录生成一个 test 文件夹。
​
- 一次性创建多层嵌套目录： mkdir -p 路径 
例： mkdir -p a/b/c  直接建好 a → b → c 三层目录；不加  -p  会因为父目录不存在而报错。
 
2. 删除目录
 
- 删除空目录： rmdir 目录名 
只能删除内部完全为空的文件夹，有内容就会报错。
​
- 删除非空目录： rm -r 目录名 
 -r  表示递归，会把目录和里面所有文件、子目录全部删掉。
常用  rm -rf 目录名  强制递归删除（删除后无法恢复，属于高危操作）。
 
 
 
### 三、GNU/Linux 相关知识
 
1. 高频基础命令
​
-  ls ：列出当前目录下的文件和文件夹； ls -l  查看详细权限、大小、修改时间； ls -a  显示隐藏文件。
​
-  pwd ：打印你当前所在的完整目录路径。
​
-  touch 文件名 ：创建一个空文件。
​
-  cat 文件名 ：查看文件内的文本内容。
​
-  cp ：复制文件；复制目录要加  -r ： cp -r 源目录 目标目录 。
​
-  mv ：移动文件，也可以给文件/目录重命名。
​
-  man 命令名 ：查看命令的官方帮助手册，比如  man cd 。
​
2. 核心设计思想
Linux 遵循一切皆文件：目录、硬件设备、甚至网络连接，在系统中都以文件的形式存在和访问。
​
3. 实用小技巧
按  Tab  键可以自动补全命令、文件名；连按两次 Tab 会列出所有匹配项，不用完整敲完路径。
​
4. 权限概念
文件和目录都有三种权限：读(r)、写(w)、执行(x)，分别对应「文件所有者」「所属用户组」「其他用户」三类身份


