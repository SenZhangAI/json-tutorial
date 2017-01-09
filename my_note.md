## tutorial01学习笔记

要点：

* CMake
* API设计(.h)与实现(.c)
* 测试驱动TDD

### 关于测试驱动的开发流程

1. 加入一个测试
2. 运行所有测试，新测试将会失败
3. 编写实现代码
4. 运行所有测试，若测试失败，回到3
5. 重构代码
6. 回到1

TDD是先写测试后实现，优点：需求明确，刚好满足测试，不会写不需要的代码，可靠。

不论TDD还是先实现后测试，都应该保证足够的覆盖率的单元测试。

我认为可以写一个test.h，设计一些基础的宏，然后每个文件写对应的测试用例。本代码为了简便只有test.c

### 学到的知识
milo yip的代码写得极好！思路非常清晰且层次分明，我试着还原一下思路，

#### API的设计思路

（1）先考虑最终的API效果

首先是宏观的总体目标，是对外的pulic API

`int lept_parse(lept_value* v, const char* json);`

parse取词的话，返回值用于表示结果对错，参数有两个，out为一个struct lept_value,用于保存结果，in为字符串。

那么这个数据结构里面的内容呢？这个后续交代，

想合理地表达null,boolean,string,array,object,float这六种JSON类型，可能的struct应该会是:

``` c
typedef enum { LEPT_NULL, LEPT_TRUE, LEPT_FALSE, LEPT_ARRAY, LEPT_OBJECT, LEPT_FLOAT } lept_type;
typedef struct lept_value_t {
    lept_type t; //保存数据类型
} lept_value;
```

注意这里的lept_value数据结构只包含一个枚举值，这是null和boolean特有的简化版，

如果是array或者其他需要修改此结构，对此我还没有比较好的思路，因为如果子结构包含信息过多，对于null和boolean而言又略显多余。

如果是一个较少信息量的类型，估计应该是加入一个`void*`类型指针，用于指向特定结构，例如string指向字符串，array指向`lept_value_t*`数值.

是否需要size等信息，拭目以待，我觉得如果不放在lept_value_t里面也会放在子类型里面。可能放在子类型里面更好，毕竟很多类型不需要。

（2）余下内容太多，自行体会吧，不写了


#### TDD的设计思路
首先写一个test的宏来做测试，为什么要用宏呢？因为c没有泛型编程嘛。 

**Milo的解答中说，原因是为了出错后显示的__LINE__宏找到对应位置，而不是每次都在该函数一个位置**

Milo Yip用了 EXPECT_EQ_BASE这个宏作为EXPECT_EQ_INT的辅助宏非常巧妙，
具体参见`test.c`

EXPECT_EQ_BASE中包含了与类型无关的内容，用format还获得实际不同类型的情况，
以及equality表达式更像是一个谓词Predicate，这样不仅可用于相等表达式。

我个人认为这种通用性强的代码应该在重构中写，实现的时候暂时别想这么复杂。

#### 解答篇收获


