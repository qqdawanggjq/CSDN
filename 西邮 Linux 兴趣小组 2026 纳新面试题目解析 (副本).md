
# 西邮 Linux 兴趣小组 2026 纳新面试题目解析

***
## 0. 树懒闪电的二进制车牌​
树懒闪电要办一张二进制车牌：16 个按钮里有四个连续按钮的数是 21、34、55、89，每个按钮最多按一次、不能按相邻的两个，按下数字之和即车牌号。

屏幕显示 11010100101，该按哪几个按钮？其十进制是多少？

>提示： 这 16 个数构成递推数列，每项等于前两项之和。
### 题目分析
- 此题考察斐波那契数列相关知识
- 将二进制`11010100101`转换为十进制**1701**
- 将斐波那契数列前 16 项列出
    1  2  3  5  8  13  21  34  55  89  144  233  377  610  987  1597
- 用**1701**减去其中小于它最大的那个数，然后记录这个数字
- 用余数重复上述步骤，直到余数为0
- 记录的所有数字就是车牌号
### 答案
1597  104  15  2

***
## 1. 一句印不完的欢迎词​
这句欢迎词是永无止境的吗？请解释运行结果：printf 的返回值扮演了什么角色，while 是否真的停不下来？
```c
int main() {
    while (1) {
        printf("Hi! ");
        if (!printf("The 202%d, Welcome to Xiyou Linux Group!\n",
                    printf("guys! "))) {
            break;
        }
    }
}
```
### 题目分析
- `while`里是一，表示为真，只要不是内存达到上限，或者是 `break`跳出循环，循环会一直执行下去
- `printf`返回的是成功输出的字符总数
    `printf("guys! ")`返回值是6
    `printf("The 202%d, Welcome to Xiyou Linux Group!\n",`返回一个非零的常数，否定后为 0。
- `If` 判断条件为假，括号里的 `break `永远不会执行，`while `不会停止
- 如果标准输出发生错误，`printf`返回负数， !负数 为0不触发break
### 答案
这句欢迎词是永无止境的，会一直打印
`The 2026, Welcome to Xiyou Linux Group!\n`

 ***
## 2. 失忆的交换生
 
两位交换生都帮` a` 和 `b `换座位：第一位换完就忘，第二位却成功了。请解释运行结果：函数修改外部变量的必要条件是什么？
```c
void swap_val(int x, int y) {
    int tmp = x;
    x = y;
    y = tmp;
}

void swap_ptr(int *x, int *y) {
    int tmp = *x;
    *x = *y;
    *y = tmp;
}

int main() {
    int a = 10, b = 20;
    swap_val(a, b);
    printf("swap_val: a=%d, b=%d\n", a, b);
    swap_ptr(&a, &b);
    printf("swap_ptr: a=%d, b=%d\n", a, b);
}

```
### 题目分析
- 第一位同学用两个整形形参来接收数据，相当于重新定义了两个变量，这两个变量之间的交换与实参无关
- 第二位同学用指针形参来接收数据，相当于直接根据地址操作两个实参，所以可以成功交换
### 答案
函数修改外部变量的必要条件：拿到外部变量的内存地址（指针），解引用操作原始内存；单纯传值只能修改副本

***
## 3. sizeof 的视力表
 
请预测输出，并解释每个  `sizeof`  和 ` strlen ` 的结果：注意字符串中间的 ` \0 `、数组退化与指针类型。
 ```c
 void inspect(char text[], int (*matrix)[4]) {
    printf("%zu %zu %zu %zu\n",
           sizeof(text), sizeof(matrix),
           sizeof(*matrix), strlen(text));
}

int main() {
    char a[] = "Linux\0Group";
    char *p = a;
    int b[2][4] = {{1, 2, 3, 4}, {5, 6, 7, 8}};

    printf("%zu %zu %zu\n",
           sizeof(a), sizeof(p), sizeof(a + 0));
    printf("%d %d\n", a[5] == '\0', strcmp(a, "Linux"));
    inspect(a, b);
}
 ```
### 题目分析
- `char a[] = "Linux\0Group" `
>字符串字面量：` L i n u x \0 G r o u p \0` ，一共12字节， `sizeof(a) `是数组总大小=12
- `char *p `
>指针变量， `sizeof(p)`=8
- ` a + 0 `
>数组名参与运算退化为指针， `sizeof(a+0)`= 8
- `a[5]  `
> `a[5] == '\0' `=1（真）
- `strcmp(a,"Linux") `
>两个字符串相同返回 0
>>`strcmp` 遇到`\0 `会停止
- `char text[] `
>等价` char *text `，数组传参时已经退化为指针， `sizeof(text)`=8 
- `int (*matrix)[4]`
> 指向 `int[4] `数组的数组指针，指针本身大小8字节， `sizeof(matrix)`=8 
- `*matrix `
>解引用得到` int[4] `数组，一个` int `为 4 个字节，` sizeof(*matrix)`= 4*4=16
### 答案
12  8  8
 1  0  
 8  8  16

***
## 4. XOR密钥：藏在字节里的悄悄话
 
一串数字里藏着一句悄悄话。请解释代码如何用位运算把数字还原成字符：` mask `怎么构造，为什么异或后恰好是原文？
```c
int main() {
    int nums[] = {167, 150, 134, 144, 138, 179, 150, 145,
                  138, 135, 184, 141, 144, 138, 143};
    int size = sizeof(nums) / sizeof(nums[0]);

    for (int i = 0; i < size; i++) {
        int mask = 0;
        int bitval = 1;
        int bits = 8;
        while (bits-- > 0) {
            mask |= bitval;
            bitval <<= 1;
        }

        printf("%c", nums[i] ^ mask);
        if (i == size - 1) {
            printf("\n");
        }
    }
}
```
### 题目分析
```c
while (bits-- > 0) {
            mask |= bitval;
            bitval <<= 1;
        }
   ```
   mask =255 (二进制 11111111)
  ```c
 printf("%c", nums[i] ^ mask);
   ```
   任何数和 255（全1）做异或，等价于把低8位全部取反（0变1，1变0）
   `%c`后面为整数，则按照 ASCII 码值进行输出
### 答案
`Welcome to Xiyou`

***
## 5. 宏召唤术：括号去哪儿了
 
这两个宏看着人畜无害，展开后却各有脾气。请写出四行  `printf  `的输出，并解释原因。
```c 
#define SQUARE(x) x * x
#define MAX(a, b) ((a) > (b) ? (a) : (b))

int main() {
    int i = 3;
    printf("%d\n", SQUARE(i + 1));
    printf("%d\n", MAX(i, 5));
    printf("%d\n", MAX(i++, 5));
    printf("%d\n", i);
}
```
### 题目分析
 ```c
    printf("%d\n", SQUARE(i + 1));
 ```
 等价于`i+(1*i )+1 `
 ```c 
 printf("%d\n", MAX(i, 5));
 ```
 3<5 则输出 5
 ```c
 printf("%d\n", MAX(i++, 5));
 ```
 为后置加加，先 3 与 5 进行比较，之后 i 等于 4
 ```c
 printf("%d\n", i);
 ```
 i  等于 4
 
### 答案
7
5
5
4

***
## 6. p 与 q 的步幅之争​
`p `和` q `指向同一个数组，“步幅”却大不相同。输出是什么？`p + 1 `和 `q + 1 `各跨过多少字节？
```c
int main() {
    int a[4] = {1, 2, 3, 4};
    int *p = a;
    int (*q)[4] = &a;

    printf("%td %td\n",
           (char *)(p + 1) - (char *)p,
           (char *)(q + 1) - (char *)q);
    printf("%d %d\n", *(p + 2), *(*q + 2));
}
```
### 题目分析
```c
       (char *)(p + 1) - (char *)p,
       (char *)(q + 1) - (char *)q)；
```
这两行本质是在做指针减法，算的是相差几个元素，但通过强制转换成  char *  来算出指针移动一个元素实际跨越了多少字节

-  int *p ：指向单个int，int占4字节。 p+1 跨过4字节。
​
-  int (*q)[4] ：数组指针，指向整个 int[4] 数组，数组大小 4*4=16 字节。 q+1 跨过16字节。
​
-  *(p+2) ：数组第2下标元素 → a[2]=3
​
-  *q  等价数组a， *q+2 等价 a+2 ， *(*q+2) 就是a[2]=3
### 答案
4  16
3  3

***
## 7. 记性特别好的 x​
这个 x 走遍千山万水仍记得来时的路。请预测每行输出，并解释 static 局部变量在递归中的行为。
```c
int visit(int n) {
    static int x = 0;
    if (n == 0)
        return x;

    x += n;
    printf("before %d %d\n", n, x);
    int result = visit(--n);
    printf("after %d %d %d\n", n, x, result);
    return x + result;
}

int main() {
    printf("first = %d\n", visit(3));
    printf("second = %d\n", visit(2));
}
```
### 题目分析
- visit(3)  x=0; x=3; pri1(before 3 3); result=18; pri6(after 3 6 18);
return 24;
- visit(2)  x=3; x=5; pri2(before 2 5); result=12; pri5(after 2 6 12);
return 18;
- visit(1)  x=5; x=6; pri3(before 1 6); result=6; pri4(after 1 6 6); 
return 12;
- visit(0)  return 6;
- pri7(first = 24);
- visit(2)  x=6;  x=8; pri8(before 2 8); result=18; pri11(after 2 9 18);
return 27;
- visit(1)  x=8; x=9; pri9(before 1 9); result=9; pri10(after 1 9 9);
return 18;
- visit(0)  return 9;
- pri12(secend = 27);
>**被static修饰的局部变量只定义一次，虽然作用域只在局部，但不会被销毁**
### 答案
before 3 3
before 2 5
before 1 6
after 0 6 6
after 1 6 12
after 2 6 18
first = 24
before 2 8
before 1 9
after 0 9 9
after 1 9 18
second = 27

***
## 8. 谁能动 const 大神的奶酪​
一眼以为“const 就全不能动”，逐条判断却错一片。请判断九个操作是否合法，并说明原因。
```c
struct P { int x; const int y; };

int main() {
    struct P p1 = {11, 22}, p2 = {33, 44};
    const struct P p3 = {55, 66};
    struct P* const ptr1 = &p1;
    const struct P* ptr2 = &p2;
    const struct P* const ptr3 = &p3;

    ptr1->x = 111; ptr2->x = 333; ptr3->x = 555;
    ptr1->y = 222; ptr1 = &p2;    ptr2->y = 444;
    ptr2 = &p1;    ptr3->y = 666; ptr3 = &p1;
}
```
### 题目分析
 - ptr1->x = 111; 
`const`锁内容不锁指向，合法
 - ptr2->x = 333; 
  `ptr2`锁内容，不合法
 - ptr3->x = 555;
 `ptr3`锁内容，不合法
  - ptr1->y = 222;
 `ptr1`指向被锁，`y`内容被锁，不合法  
  - ptr1 = &p2;    
  `ptr1`指向被锁，不合法
  - ptr2->y = 444;
  `y`内容被锁，不合法
  - ptr2 = &p1;  
  合法  
  - ptr3->y = 666;
  `ptr3`指向内容都被锁，`y`内容被锁，不合法 
  - ptr3 = &p1;
  `ptr3`指向内容都被锁，不合法 
  
### 答案
ptr1->x = 111; → 合法

ptr2->x = 333; → 非法

ptr3->x = 555; → 非法

ptr1->y = 222; → 非法

ptr1 = &p2; → 非法

ptr2->y = 444; → 非法

ptr2 = &p1; → 合法

ptr3->y = 666; → 非法

ptr3 = &p1; → 非法

***
## 9. 当函数变成了数字​

在这里，函数既能装进数组，也能被加减乘除。这段代码写得标准吗？注意 INT_MIN 和 0xFFFFFFFF。
```c
typedef int (*BinOp)(int, int);

int add(int a, int b) { return a + b; }
int sub(int a, int b) { return a - b; }
int mul(int a, int b) { return a * b; }

BinOp ops[3] = { add, sub, mul };

int (*get_op(char c))(int, int) {
    switch (c) {
        case '+': return add;
        case '-': return sub;
        default: return mul;
    }
}

int main() {
    printf("%d\n", get_op('*')(7, -2));
    printf("%d\n", ops[1](INT_MIN, 20));
    printf("%d\n", get_op('+')(0xFFFFFFFF, 2027));
}
```
### 题目分析
- `typedef int (*BinOp)(int, int);`
定义一个函数指针`BinOp`
- `int (*get_op(char c))(int, int) `
     - `get_op(char c)`
一个函数，参数是`char`,返回一个函数指针
### 1.
    7*（-2）==-14

### 2. 
    INT_MIN (即 -2147483648) 的补码：
>`1000 0000 0000 0000 0000 0000 0000 0000`
(最高位为 1，是负数；这是 32 位有符号数能表示的最小值)


-20 的补码（用于将减法转为加法）：

>20 的二进制：`0000 ... 0001 0100`
>
>按位取反：`1111 1111 1111 1111 1111 1111 1110 1011`
>
>末位加 1：`1111 1111 1111 1111 1111 1111 1110 1100`

    执行二进制加法（补码运算）
INT_MIN - 20 等价于 INT_MIN + (-20)。

我们将两个补码对齐相加：

  `1000 0000 0000 0000 0000 0000 0000 0000`   (INT_MIN)
  `+1111 1111 1111 1111 1111 1111 1110 1100`   (-20)
  
`1 0111 1111 1111 1111 1111 1111 1110 1100 `  (产生了第33位的进位)
截断与结果
在 32 位系统中，寄存器或内存只能保存 32 位。因此，最高位的进位 1 会被硬件直接丢弃（截断）。

丢弃最高位后，得到 32 位结果：
`0111 1111 1111 1111 1111 1111 1110 1100`
>补码相加时，符号位也正常进行相加，超出的部分舍去

### 3. 
0xFFFFFFFF 的二进制（32位全1）：

`1111 1111 1111 1111 1111 1111 1111 1111`
> ***十六进制转化为有符号二进制时就是补码***
>那么它转化为原码时为`-1`
>
原题=`(-1)+2027`

### 答案
-14
2147483628
2026

***
## 10. 字节战队排排站：大端、小端与内存对齐​

同一片内存，不同视角看到不同世界。请写出全部输出，并解释大端、小端与内存对齐的原理。
```c
struct data_box {
    int num;
    union {
        unsigned int u32_val;
        unsigned char bytes[4];
        char string[32];
    } un;
    short tag;
    long long magic;
    int buf[4];
};

int main() {
    int arr[] = {0x00000000, 0x6F796958, 0x694C2075,
                 0x2078756E, 0x756F7247, 0x00000070,
                 0x44556677, 0x8899aabb};

    printf("%s\n", ((struct data_box *)arr)->un.string);
    printf("byte0 = 0x%02X\n", ((struct data_box *)arr)->un.bytes[0]);
    printf("byte1 = 0x%02X\n", ((struct data_box *)arr)->un.bytes[1]);
    printf("byte2 = 0x%02X\n", ((struct data_box *)arr)->un.bytes[2]);
    printf("byte3 = 0x%02X\n", ((struct data_box *)arr)->un.bytes[3]);
}
```
### 题目分析
- `(struct data_box *)arr`
强制转换，将“按int数组解读”变为“按结构体解读”
- 小端存储：低字节存低地址，高字节存高地址
 >倒着存
 - 大端存储：低字节存高地址，高字节存低地址
 >正着存
- num  0 - 4;
un 4 - 36;
tag 36 - 38;
magic 40 - 48;
buf 48 - 64;
- `printf("%s\n", ((struct data_box *)arr)->un.string);`
>一个十六进制四个比特位，俩个十六进制占一个字节，读取时一个字节内的俩个十六进制转化为十进制ASCII，打印字符


我们把偏移4开始的字节，逐个转成ASCII字符：
 
偏移4： 0x58  → 'X'
​
偏移5： 0x69  → 'i'
​
偏移6： 0x79  → 'y'
​
偏移7： 0x6F  → 'o'
​
偏移8： 0x75  → 'u'
​
偏移9： 0x20  → 空格
​
偏移10： 0x4C  → 'L'
​
偏移11： 0x69  → 'i'
​
偏移12： 0x6E  → 'n'
​
偏移13： 0x75  → 'u'
​
偏移14： 0x78  → 'x'
​
偏移15： 0x20  → 空格
​
偏移16： 0x47  → 'G'
​
偏移17： 0x72  → 'r'
​
偏移18： 0x6F  → 'o'
​
偏移19： 0x75  → 'u'
​
偏移20： 0x70  → 'p'
​
偏移21： 0x00  →  \0
- `printf("byte0 = 0x%02X\n", ((struct data_box *)arr)->un.bytes[0]);`
**byte0 = 0x58**
- `printf("byte1 = 0x%02X\n", ((struct data_box *)arr)->un.bytes[1]);`
**byte0 = 0x69**
- `printf("byte2 = 0x%02X\n", ((struct data_box *)arr)->un.bytes[2]);`
**byte0 = 0x79**
- `printf("byte3 = 0x%02X\n", ((struct data_box *)arr)->un.bytes[3]);`
**byte0 = 0x6F**

### 答案
Xiyou Linux Group
byte0 = 0x58
byte1 = 0x69
byte2 = 0x79
byte3 = 0x6F
## 11. GNU/Linux & AI（选做）​
注： 嘿！你或许对 Linux 命令不是很熟悉，甚至没听说过 Linux；你或许对 AI 的了解仅仅止步于豆包，甚至只是听说过。但别担心，这是选做题，了解 Linux 和 AI 是加分项，但不了解也不扣分哦！

### GNU/Linux​
你知道 ps aux 命令所展示的结果是什么吗？
请你在 shell 下创建一个目录，然后在目录内创建几个文件并且列出它们，最后完整地删除整个目录。操作完成之后，请你解释一下，每个命令在整个流程中都起什么作用？请你进一步思考一下，Linux 系统的目录结构是什么？
你还了解哪些 Linux 的发行版？如果条件允许，你可以尝试安装一个你最喜欢的版本。
### 答:
     
 1. ps aux 命令展示的是什么？
简单说，就是查看系统里当前正在运行的所有程序（进程）。
它会列出谁启动了这些程序、占用了多少 CPU 和内存、启动时间以及具体的启动命令。

2. 目录操作流程及命令解释
在终端依次输入以下命令：

mkdir mydir：创建一个叫 mydir 的新目录。

cd mydir：进入这个目录。

touch file1.txt file2.txt：创建两个空文件。

ls：列出当前目录下的所有文件（你会看到 file1 和 file2）。

cd ..：返回上一级目录。

rm -rf mydir：彻底删除 mydir 目录以及里面的所有文件（-r 代表递归，-f 代表强制）。

3. Linux 系统的目录结构是什么？
它是一个树状结构，最顶层是根目录 /。常见的目录有：

/home：普通用户的家目录。

/etc：存放系统配置文件。

/bin：存放常用命令。

/var：存放日志等经常变化的文件。

/tmp：临时文件存放处。

4. 了解哪些 Linux 发行版？
常见的发行版有：Ubuntu（最流行，适合新手）、CentOS（服务器常用）、Debian（稳定）、Fedora、Kali Linux（黑客渗透专用）、Arch Linux（极客定制）。如果推荐新手，可以试试 Ubuntu。
### AI​
模型（model）和智能体（agent）的区别是什么？你可以举一个实际的例子说明一下。
skill 可以强化一个 agent 在某个问题上回答的专业能力，但是 skill 越多越好吗？
如果现在要求你使用 agent 来快速 vibe coding 一个项目，目前较为公认的流程是什么？如果条件允许，你可以尝试实现一个。
### 答:
1. 模型（Model）和智能体（Agent）的区别是什么？

模型：像一个聪明的大脑，但它没有手和脚。你问它问题，它只能基于学过的知识回答（比如 ChatGPT 本身）。

智能体：像一个完整的人。它不仅有大脑（模型），还有手脚（工具）和记忆。它能自己思考、做计划、调用外部工具来完成任务。

举例：你问“明天北京天气怎么样？”。模型只能说“我没有实时数据”；而智能体会自动去调用“天气API”，然后把结果告诉你。

2. Skill 越多越好吗？
不是。
Skill 就像智能体的工具箱。工具太多反而会出问题：比如选错工具、工具之间互相冲突、占用太多上下文空间，或者维护成本太高。所以，Skill 应该是按需添加、少而精，质量比数量更重要。

3. 使用 Agent 快速 Vibe Coding 的公认流程是什么？

说需求：用自然语言告诉 Agent 你想做个什么项目。

搭骨架：让 Agent 生成项目目录、依赖和基础配置。

写代码：对话式地让 Agent 逐个模块生成代码。

跑测试：运行代码，如果报错，直接把错误丢给 Agent 让它修。

人工审查：关键逻辑自己看一眼，确认没问题后提交 Git。

部署：让 Agent 生成部署脚本，跑起来验证。

核心就是：你当项目经理，Agent 当程序员，边聊边写，快速迭代。



