# C\+\+知识点整合复习

# C\+\+ 知识点总结与复习

> 本文档整合了三份C\+\+复习资料的内容，按知识点系统梳理，涵盖基础语法、面向对象、内存管理、模板STL、设计模式及经典编程题。

---

## 一、C\+\+ 编译与内存模型

### 1\.1 编译过程

编写C\+\+程序一般需要经过的几个步骤依次是：**编辑、编译、链接、调试**。

C\+\+ 的编译过程通常分为**预处理、编译、链接**三大阶段：

- **预处理**：处理以 `#` 开头的指令（如 `#include`、`#define`、`#ifdef` 等）

- **编译**：词法分析和语法分析，对每个预处理后的源程序，编译并生成相应的二进制目标文件（object文件）。此过程中，要求编译器能够识别每个标识符，知道其各自的类型、含义，但不要求知道存放位置

- **链接**：在整个程序范围内，确定各标识符所代表的含义和地址

C\+\+ 是一种**编译型**的高级语言，而非解释型。

- 源代码（`.cpp`）经过编译器转化为目标文件（`.obj`）

- 目标文件通过链接器整合为可执行文件（`.exe`）

### 1\.2 内存分区

C\+\+程序的内存分为四大区域：

|区域|存储内容|特点|
|---|---|---|
|**代码区**|程序的二进制代码|只读，共享|
|**数据区**（全局/静态区）|全局变量、静态变量、常量|程序运行期间一直存在|
|**栈区**|局部变量、函数参数、返回地址|由系统自动分配释放，速度快，空间有限|
|**堆区**|`new`/`malloc` 动态分配的内存|由程序员手动管理，空间大，速度相对慢|

- 全局变量以及常量都储存在**数据区**

- 局部变量储存在**栈区**

- `static` 静态变量可以看作全局，储存在**数据区**

### 1\.3 声明与定义

- **头文件中可放声明，以及类定义，但不应放变量定义和函数定义**

- 声明可以多次，定义只能一次

- 变量声明且带初始化（无 `extern` 修饰）既是声明也是定义

- 声明通常建议放在头文件中，但普通全局函数和全局变量的定义**严禁直接放在头文件中**（除非使用 `inline` 或 `static` 修饰以改变内部链接属性），否则会导致链接错误

### 1\.4 作用域

作用域分类：

- 全局作用域

- 文件作用域

- 块级作用域

- 类级作用域

- 名字空间作用域

---

## 二、类与对象基础

### 2\.1 访问控制

- 在 `class` 中，未指定访问控制的成员默认是 `private`，并非 `public`

- 成员函数可以访问本类**任何对象**的私有成员，而不仅限于 `this` 指向的对象

- `class` 默认 `private`，`struct` 默认 `public`

### 2\.2 构造函数（Constructor）★★高频考点

创建对象时**自动调用**，用来初始化数据成员。

**特点（必背）：**

1. **函数名与类名完全相同**

2. **没有返回类型**（连 `void` 都不写）

3. 应定义为 `public`

4. 是对象**第一个被调用**的函数

5. **可以重载**（可有多个构造函数）

6. **不允许显式调用**（不能像普通函数那样 `obj.A();` 调用）

7. 创建对象时自动调用，且**只调用一次**

8. 构造函数不能声明为 `const`

9. 在构造函数中可以使用 `return` 语句，但只能是 `return;`

10. `new A()` 同样调用默认构造函数 `A()`

```C++
class A {
    int x, y;
public:
    A() { x = y = 0; }                 // 默认构造函数（无参）
    A(int x1, int y1) { x = x1; y = y1; }  // 带参构造函数（重载）
};
A a1;            // 调用 A()      —— 无参时不要写 A a1();！
A a2(3, 4);      // 调用 A(int,int)
```

**默认构造函数**：不带参数的（或所有参数都有默认值的）构造函数。

- 如果你**一个构造函数都不写**，编译器会隐式提供一个默认构造函数

- 但只要你写了**任意一个**构造函数，编译器就**不再**自动提供默认构造函数

- **定义对象数组时，必须有无参（默认）构造函数**：`A arr[10];`

**成员初始化表（初始化列表）写法：**

```C++
A(int x1, int y1) : x(x1), y(y1) { }   // 冒号后面给成员赋初值
```

**类的数据成员初始化规则：**

1. 非static非const成员变量，必须在构造函数中进行初始化

2. static成员变量，必须在类的外面进行初始化

3. const成员变量，必须在类的构造函数的初始化列表中初始化

4. static const成员变量，可以在类的内部声明时初始化

**初始化顺序**：类中数据成员的初始化顺序一定是严格按照**定义顺序**依次初始化的，与初始化列表中的顺序无关。

**必须在成员初始化列表中初始化的成员**：

- `const` 成员

- 引用成员

- 无默认构造函数的成员对象

### 2\.3 析构函数（Destructor）★高频考点

对象**消亡时自动调用**，做清理工作（如释放 `new` 出来的资源）。

**特点（必背）：**

1. 名字是 `~类名`（前面加波浪号）

2. **没有返回类型，也没有参数**

3. **不能重载**（一个类只有一个析构函数）

4. **不可显式调用**

5. 对象销毁前系统自动调用

```C++
class A {
    int *data;
public:
    A()  { data = new int[100]; }   // 构造时申请资源
    ~A() { delete[] data; }         // 析构时归还资源
};
```

- 如果不写，编译器隐式提供一个析构函数，作用是调用成员对象类、基类的析构函数

### 2\.4 静态成员 static

解决"同一个类的不同对象之间**共享**数据/函数"的问题。**静态成员在内存中只有一份拷贝**，被该类所有对象共享。

```C++
class A {
    static int count;       // 静态数据成员：声明
public:
    A() { count++; }
    static int getCount() { return count; }   // 静态成员函数
};
int A::count = 0;           // 静态数据成员必须在类外初始化！
// 调用静态成员函数：用 类名:: ，不需要对象
cout << A::getCount();
```

**static 在C\+\+中的全部作用：**

1. **定义静态局部变量**：在函数内部使用 `static` 关键字定义静态局部变量，它的生命周期与程序的执行时间相同，但只能在定义它的函数内部访问。静态局部变量在程序执行到它的定义处时被初始化一次，之后每次调用函数时都保持它的值不变。

2. **定义静态全局变量**：在全局作用域内使用 `static` 关键字定义静态全局变量，它的生命周期与程序的执行时间相同，但只能在定义它的文件内部访问。静态全局变量在 `main` 函数之前被初始化。

3. **定义静态成员变量**：在类定义中使用 `static` 关键字定义静态成员变量，它是类的所有对象共享的一个变量。静态成员变量在 `main` 函数之前被初始化，必须在类外初始化。

4. **定义静态成员函数**：在类定义中使用 `static` 关键字定义静态成员函数，它不依赖于类的对象，可以通过类名直接调用。静态成员函数不能访问非静态数据成员和非静态成员函数。

5. **控制函数和变量的链接性**：在全局作用域内使用 `static` 关键字定义函数或变量时，可以控制它们的链接性。具有静态链接性的函数或变量只能在定义它们的文件内部访问。

**要点总结：**

- 静态成员不属于对象存储空间，函数（包括虚函数）不占对象大小，但虚函数指针（vptr）占空间

- 静态成员函数**没有 this 指针**，不能直接访问非静态成员

- 静态数据成员属于类本身，不属于单个对象，拷贝时不会处理

- `static const int` 允许在类内初始化

- 静态成员函数不能声明为 `const`

- 静态数据成员在 `main` 函数之前构造，生命周期等同于全局对象，但需要注意访问权限（公有、保护或私有）

### 2\.5 const 成员

**Const 修饰指针和引用时：左定值，右定向**

- `const int *p` 或 `int const *p`：指向常量的指针，指针指向的值不能改

- `int * const p`：常量指针，指针的指向不能改

**const 成员函数：**

- 在类的成员函数后面加 `const`

- 只有类的成员函数才能定义为常量成员函数

- 目的：禁止在该函数内修改数据成员（保护数据）

- **常量对象只能调用常量成员函数和静态成员函数**

- 非 const 对象可以调用所有成员函数

- const 和非 const 成员函数可以构成重载

**this 指针类型：**

- 非 const 成员函数：`this` 的类型是 `T *const`

- const 成员函数：`this` 的类型是 `const T * const`

**其他：**

- 常量左值引用可以绑定到临时对象，并延长其生命周期至引用的作用域结束

- 非 const 的普通变量，即使位于匿名命名空间，也不是编译时常量，不能用作数组大小

### 2\.6 this 指针 ★高频考点

- 每个**成员函数**内部都隐含一个 `this` 指针，指向**调用该函数的当前对象**

- 数据成员：每个对象各有一份拷贝；成员函数：所有对象**共用一份**（靠 this 区分是哪个对象）

```C++
class A {
    int x;
public:
    void setX(int x) { this->x = x; }    // this->x 是成员，x 是参数
    A* getThis() { return this; }        // 返回当前对象指针
};
```

**注意**：`this` 指针并不是对象自身的一部分，它不占用内存空间。因此，对对象进行 `sizeof` 运算时，并不会包含 `this` 指针占用的内存大小。

### 2\.7 友元 friend

让"外部函数 / 别的类"能够访问本类的 `private` 和 `protected` 成员。

```C++
class A {
    friend void show(A &a);      // 友元函数：普通函数，不是成员函数
    friend class B;              // 友元类：B 的所有成员函数都能访问 A 的私有成员
    int x;
};
void show(A &a) { cout << a.x; } // 虽然 x 是私有，但 show 是友元，能访问
```

**友元关系的概念与副作用：**

- 友元函数**是普通函数，不是类的成员函数**（不属于这个类）

- 友元类：友元类的所有成员函数都是本类的友元

- **友元关系不是双向的**（A 把 B 当友元，不等于 B 把 A 当友元）

- **友元关系不能继承**

**副作用**：友元关系破坏了类的封装性，因为它允许外部函数或类直接访问类的私有和保护成员。如果类的实现发生了变化，那么与之相关的友元函数或友元类也可能需要进行相应的修改。此外，过多地使用友元关系会使代码变得难以理解和维护。

### 2\.8 其他特性

- 引用必须初始化并且在初始化后不能重新绑定

- 不能对非 `new` 分配的指针使用 `delete`

- Outer 的大小完全由它自己的非静态数据成员决定，与嵌套类 Inner 的大小没有必然的大小关系

- `swap` 用引用：`void swap(int &a, int &b){int t=a;a=b;b=t;}`

---

## 三、拷贝与赋值

### 3\.1 拷贝构造函数（Copy Constructor）★★高频考点

**用一个同类型的已存在对象，去初始化新对象时调用。**

格式：`类名(const 类名 &)`

```C++
class A {
public:
    A();                  // 默认构造函数
    A(const A &a);        // 拷贝构造函数：参数是 const 引用
};
```

**会调用拷贝构造函数的三种情况（必背）：**

1. **用对象初始化对象**：`A a2(a1);` 或 `A a2 = a1;`（用 a1 初始化 a2）

2. **对象作为值参数传给函数**：`void f(A x);` 调用 `f(a);`（用 a 初始化形参 x）

3. **对象作为函数返回值**：`A f() { A a; return a; }`（用 a 初始化返回值）

**关键规则：**

- 如果不写，编译器提供**隐式拷贝构造函数**，对数据成员**逐个拷贝**（浅拷贝）

- **什么时候必须自己写？** 当类中有**指针类型数据成员**（或 `new` 出来的资源）时，浅拷贝会让两个对象的指针指向同一块内存，析构时**重复释放 → 崩溃**。此时必须自己写拷贝构造函数做**深拷贝**

```C++
// 深拷贝示例
A(const A &a) {
    data = new int[100];                  // 自己申请新内存
    for (int i = 0; i < 100; i++)
        data[i] = a.data[i];              // 复制内容，而不是复制指针
}
```

**浅拷贝的详细说明：**

- 对于对象类型的非静态数据成员，编译器会通过调用该成员所属类的拷贝构造函数来实现拷贝

- 对于对象指针类型和对象引用类型的非静态数据成员，"浅"拷贝只会直接拷贝指针的地址值或引用的绑定，并不会自动调用指针或引用所指向对象的拷贝构造函数。这是浅拷贝导致"指针悬挂"等问题的根本原因

- 内置类型（如 int、double）在浅拷贝时直接逐字节复制，相当于按二进制数据块拷贝

- 浅拷贝时，对于对象类型成员会调用其拷贝构造函数；但指针类型成员仅复制指针的值（地址），不会调用所指对象的拷贝构造函数；引用类型成员会直接绑定到源对象引用所指的对象

> 注意：自定义的拷贝构造函数，对成员对象**默认调用的是成员对象的"默认构造函数"**，若想调用成员对象的"拷贝构造函数"，需在**成员初始化表**里显式写出。

### 3\.2 赋值运算符

```C++
B& operator=(const B& rhs) 
{
    if ( &rhs != this ) {        // ① 检查自赋值（重要）
        delete mpCh;             // ② 释放旧资源
        mpCh = new char(*rhs.mpCh);  // ③ 分配新资源并拷贝
    }
    return *this;                // ④ 返回 *this 支持链式赋值
}
```

**要点：**

- 有非静态的引用成员，禁止赋值（因为引用不能重新绑定）

- 赋值运算符通常不定义为虚的

### 3\.3 禁止对象拷贝

**方法1**：拷贝构造函数是 `private` 且没有实现
**方法2**：使用 `= delete` 显式删除拷贝构造函数

```C++
// C++11 方式
class NonCopyable {
public:
    NonCopyable(const NonCopyable&) = delete;
    NonCopyable& operator=(const NonCopyable&) = delete;
};
```

### 3\.4 派生类的拷贝构造函数与赋值

**派生类的拷贝构造函数：**

```C++
class D : public B {
private:
    int d_val;
public:
    // 派生类拷贝构造函数
    D(const D& other) 
        : B(other)      // ① 显式调用基类的拷贝构造函数
        , d_val(other.d_val) // ② 拷贝派生类自己的成员
    {
        // 如果派生类有动态分配的内存，需要在这里写深拷贝代码
    }
};
```

**派生类的赋值运算符：**

```C++
class D : public B {
private:
    int d_val;
public:
    D& operator=(const D& other) {
        // ① 检查自赋值（重要）
        if (this == &other) {
            return *this; 
        }
        // ② 显式调用基类的赋值运算符（处理基类部分）
        B::operator=(other); 
        // ③ 拷贝派生类自己的普通成员
        d_val = other.d_val; 
        // ④ 如果派生类有动态分配的内存，必须先 delete 旧内存，再 new 新内存进行深拷贝
        return *this;
    }
};
```

**要点：**

- 派生类**隐式**拷贝构造函数会调用基类的拷贝构造函数

- 派生类**自定义**的拷贝构造函数，默认只调用基类的**默认构造函数**，需要时要在初始化表里显式调用基类拷贝构造函数

- 派生类**不从基类继承赋值运算符**。自定义赋值运算符时，要在函数体内**显式调用基类的赋值运算符**

---

## 四、指针与引用

### 4\.1 左值与右值

- **左值 \(Lvalue\)**：有地址、能取址、有名字的对象。`int x = 10;` // x 是左值

- **右值 \(Rvalue\)**：没有持久地址、临时性的对象。`int y = 20;` // 20 是右值（字面常量）

### 4\.2 指针数组与数组指针

- **指针数组**：`int * array[5];` （数组，每个元素是指针）

- **数组指针**：`int (*p) [5];` （指针，指向一个数组）

### 4\.3 指针的引用

- `*&` = 指针的引用

- 作用：在函数里直接修改原指针的指向

- 比二级指针 `**` 更简洁、更好用，是现代 C\+\+ 的标准写法

### 4\.4 void\* 指针

- `void*` 可以接受任何指针类型的隐式转换，因此通过 `void*` 数组存放 `new A` 和 `new B` 返回的指针

- 在 C/C\+\+ 中，不同数据类型的指针之间没有隐式转换规则，编译器会报告类型不匹配的错误

---

## 五、函数与运算符重载

### 5\.1 内联函数

- 内联的作用：建议编译器在调用处直接展开函数代码，只是建议

- 在类体内直接给出函数体的成员函数自动成为内联函数，无论是否显式写出 `inline` 关键字

- 如果类内加了 `inline`，类外也要加

### 5\.2 函数重载

- **返回类型、缺省参数值不能作为区分标志**

- 异常声明和返回类型都不能作为区分函数的标志，尤其是返回类型（单选时）

- 函数定义和声明时都可以省略形参名

**默认参数匹配规则**：
对于函数 `void func(int a=0, int b=0, int c=0)`，使用 `func(2)` 进行调用时，参数匹配的结果是：`a=2, b=0, c=0`（从左到右依次匹配）。

### 5\.3 运算符重载基础

- `= () [] ->` 必须是成员形式

- **永远不要重载 ****`&&`****, ****`||`**** 和 ****`,`**** 三种操作符**（因为会失去短路求值特性）

**两种方式：成员函数 / 友元函数**

```C++
class Complex {
    double re, im;
public:
    Complex(double r = 0, double i = 0) : re(r), im(i) {}
    // 方式1：成员函数重载 +
    Complex operator+(const Complex &c) const {
        return Complex(re + c.re, im + c.im);
    }
    // 方式2：友元函数重载 <<
    friend ostream& operator<<(ostream &out, const Complex &c) {
        out << c.re << "+" << c.im << "i";
        return out;
    }
};
```

**要点：**

- 双目运算符：用**成员函数**重载时参数比操作数少 1（左操作数是当前对象）；用**友元函数**重载时参数与操作数个数相同

- 单目运算符：成员函数重载时无参（`++a`），后置 `a++` 用一个 `int` 占位参数区分

- `<<`（插入/输出）和 `>>`（提取/输入）运算符：因为左操作数是 `cout`/`cin`（不是自己的对象），**必须用友元函数**重载，且返回流的引用以支持链式

**可重载与不可重载：**

- 可重载：`++ -- + - * / = += *= /= [] () << >>` 等

- 不能重载：`.`（成员访问）、`::`（作用域）、`?:`（三目）、`sizeof`、`.*`

### 5\.4 自增自减运算符

```C++
// 前置++
A& operator++( ) { ++x; return *this; }

// 后置++
A operator++(int) { int temp=x; ++x; return A(temp); }
```

- 后置\+\+：先保存对象当前值的副本，然后对对象执行递增，最后返回递增前的副本

### 5\.5 \-\> 运算符重载

C\+\+规定，重载`->`时，必须满足1或2：

1. 函数`operator->()` 返回指针类型

2. 函数`operator->()` 返回自定义类型，且该类型中重载了 `operator->()`

```C++
A * operator->( ) { return pa; }
B& operator->( ) { return b; }
```

### 5\.6 其他运算符重载

**下标运算符 ****`[]`** 通常成对重载（const 和非 const 版本）：

```C++
int operator[ ](int index) const
{ return nums[index]; }

int& operator[ ](int index) 
{ return nums[index]; }
```

**函数调用运算符 ****`()`** 可以重载，使对象像函数一样使用：

```C++
int operator() ( ) const { return 999; }
int operator() (int a,int b ) const  
{ return a+b; }
```

**流插入/提取运算符**通常以友元或自由函数形式重载：

```C++
ostream& operator<<(ostream& out, const A & a ) {
    out<<a.getA()<<endl;
    out<<a.getB()<<endl;
    return out;
}

istream& operator>>(istream& in, A& aA ) {
    int a,b;
    in>>a>>b; 
    aA.setA(a); aA.setB(b);
    return in;
}
```

**算术运算符**建议以自由函数形式重载，并利用复合赋值运算符实现：

```C++
// 成对重载，利用 += 实现 +
A operator+(const A& lhs, const A& rhs)
{
    return A(lhs)+=rhs;
}
```

自由函数形式的好处：适应用户的不同使用习惯（`a = a+b;` 和 `a += b;`）、可以去掉 `friend` 声明、便于更改数据成员

### 5\.7 静态工厂方法

当一个类中存在多个重载的构造函数，尤其是参数类型相似或数量较多时，直接使用构造函数会导致语义不清楚和不易扩展等问题。此时推荐采用静态工厂方法（Static Factory Method）来替代部分构造函数。

---

## 六、类型转换

### 6\.1 隐式转换与显式转换

- **显式转换（强制转换）**：在代码中明确写出了 `(类型)`、`类型()` 或强制转换运算符的，属于强制类型转换

- 一个隐式转换序列中最多只能包含一次用户自定义转换（构造函数或转换函数）

### 6\.2 单参构造函数与隐式转换

- 单参构造函数常被用于隐式类型转换（`explicit` 除外）

- `explicit` 关键字可以禁止单参构造函数的隐式转换

**转换构造函数示例：**

```C++
class B {
private:
    int value;
public:
    B(int n) : value(n) {}
    int getValue() const { return value; }
};

class A {
private:
    int value;
public:
    A(int n) : value(n) {}
    // 转换构造函数，接收 B 类型对象
    A(const B& b) {
        this->value = b.getValue();
    }
};

int main() {
    B b1(10);
    A a1 = b1;     // 调用转换构造函数
    A a2(0);
    a2 = b2;       // 先调用转换构造函数生成临时 A 对象，再赋值
}
```

### 6\.3 类型转换运算符

在源类中定义类型转换运算符，告诉编译器"该类型的对象可以隐式地转换成目标类型"。

```C++
class B {
    // ...
public:
    // 定义类型转换运算符
    operator A() const {
        return A(/* 从 B 中提取的初始化参数 */); 
    }
};
```

**实现 ****`A a = b;`**** 或 ****`a = b;`**** 的三种手段：**

|手段|位置|说明|
|---|---|---|
|手段一|类 A 中重载 `operator=`|直接赋值，最高效|
|手段二|类 B 中定义 `operator A()`|类型转换运算符，先转成临时 A 再赋值|
|手段三|类 A 中定义转换构造函数 `A(const B&)`|建立从 B 到 A 的隐式类型转换|

### 6\.4 static\_cast

- `static_cast<T>(exp)` 其中 T 表示指针、引用、内置类型、枚举类型，但不能是对象

- 用于编译期可确定的类型转换

### 6\.5 dynamic\_cast

- `dynamic_cast<T>(exp)` T只能为类的指针、类的引用、`void *` 三种形式

- 用于运行时类型识别（RTTI），需要类有虚函数才能使用

---

## 七、继承与多态

### 7\.1 多态概述

多态是面向对象编程中的一个重要概念。它指的是同一个实体（如函数或运算符）在不同情况下表现出不同的行为。

C\+\+ 中的多态可以通过以下几种方式实现：

- **函数重载**：可以使用相同名称但具有不同参数（类型或参数数量）的两个函数。根据参数的数量/类型，调用不同的函数

- **运算符重载**：可以重载运算符，只要我们对用户定义的类型（如对象或结构）进行操作。我们不能对基本类型（如 int、double 等）使用运算符重载

- **函数覆盖**：派生类中的虚函数必须与基类中被覆盖的虚函数具有相同的函数名、参数个数、参数类型、参数顺序和返回类型

- **虚函数**：虚成员是可以在派生类中重新定义的成员函数，同时保留其通过引用调用的属性

多态分为：

- **静态多态**（编译期决定）：函数重载、运算符重载、模板

- **动态多态**（运行期决定）：靠**虚函数**实现，称为**动态绑定**

### 7\.2 继承方式

|继承方式|语义|基类成员在派生类中的访问属性|
|---|---|---|
|**public**|"is a kind of", "is a", "like a"|public→public, protected→protected, private不可访问|
|**protected**|"has\-a", "contain\-a", "implement of"|public→protected, protected→protected, private不可访问|
|**private**|"has\-a", "contain\-a", "implement of"|public→private, protected→private, private不可访问|

- 尽可能使用指针或引用型进行向上类型转换

- public 继承方式下，向上/向下类型转换有意义

**里氏替换原则**：需要基类对象的任何地方都可以使用其派生类的对象来替代，程序的行为没有变化。

> 注意：`B b; A a = b;` 这种情况下，类 B 不一定是通过 public 继承方式从类 A 派生的。也有可能类 B 和类 A 没有任何继承关系，但是定义了从类 B 到类 A 的类型转换运算符或者类 A 有一个接受类 B 对象作为参数的构造函数。

### 7\.3 派生类的构造函数与析构函数 ★高频考点

**派生类构造时，必须先构造基类部分。** 在派生类构造函数的**初始化表**中调用基类构造函数：

```C++
class Base {
public:
    Base(int x) { cout << "Base构造\n"; }
    ~Base()     { cout << "Base析构\n"; }
};
class Derived : public Base {
public:
    Derived(int x) : Base(x) { cout << "Derived构造\n"; }
    ~Derived()              { cout << "Derived析构\n"; }
};
```

**调用顺序（必背，常考）：**

- **构造顺序**：基类构造函数 → 成员对象构造函数 → 派生类自己的构造函数（**先父后子**）

- **析构顺序**：与构造**完全相反**——派生类析构 → 成员对象析构 → 基类析构（**先子后父**）

### 7\.4 虚函数与多态 ★★高频考点

**动态绑定**：在程序**运行时**，根据指针/引用**实际指向的对象类型**决定调用哪个函数。

```C++
class Shape {
public:
    virtual double area() { return 0; }    // 虚函数：加 virtual 关键字
    virtual ~Shape() = default;            // 基类析构函数最好也是虚函数
};
class Circle : public Shape {
    double r;
public:
    Circle(double r) : r(r) {}
    double area() override { return 3.14159 * r * r; }
};
```

**必背要点：**

- 声明虚函数的关键字：**`virtual`**（只需在基类写，派生类可不写）

- **覆盖\(override\)条件**：派生类函数与基类虚函数**同名、参数类型和个数相同、返回值类型相同**（这叫"相同型构"）

- 虚函数**必须通过基类的指针或引用调用**才能体现动态绑定（直接用对象调用是静态绑定）

- **没有 virtual** → 静态绑定（看指针类型）；**有 virtual** → 动态绑定（看实际对象）

**虚函数的其他规则：**

- 虚函数必须是成员函数，静态成员函数和构造函数、拷贝构造函数不能是虚的

- 若类中有其它虚函数，那么析构函数也应该是虚的

- 赋值函数通常不定义为虚的

- 虚函数可以带 const 修饰，也可以不带

- 若基类的析构函数是虚的，那么派生类中的析构函数也是虚的

### 7\.5 静态绑定与动态绑定

**静态绑定**是在编译时确定被调用函数的过程。编译器根据函数的名称、参数类型和数量来确定被调用的函数。静态绑定通常用于非虚成员函数和非成员函数的调用。

**动态绑定**是在运行时确定被调用函数的过程。它通常用于虚成员函数的调用。当通过基类指针或引用调用虚成员函数时，程序会根据指针或引用所指向的对象的实际类型来确定调用哪个版本的虚成员函数。

### 7\.6 虚函数表工作原理

虚函数的工作机制依赖于**虚函数表（vtable）**和**虚表指针（vptr）**。

- 每个包含虚函数的类都有一个虚函数表，其中存储了该类的虚函数地址

- 每个包含虚函数的类的对象都有一个虚表指针，它指向该类的虚函数表

- 当通过基类指针或引用调用虚成员函数时，程序会根据虚表指针来查找虚函数表，并根据虚函数表中存储的地址来确定调用哪个版本的虚成员函数

> 注意：虚函数指针（vptr）会占用对象的内存空间。即使类中只有成员函数，如果有虚函数，`sizeof` 也会包含 vptr 的大小。

### 7\.7 纯虚函数与抽象类

```C++
class Shape {
public:
    virtual double area() = 0;       // 纯虚函数：= 0，没有函数体
};
```

- **纯虚函数**：`virtual 返回类型 函数名(参数) = 0;` —— 只声明不实现，**目的是给派生类规定接口**，强制派生类去实现

- 含有纯虚函数的类叫**抽象类**

- **抽象类不能创建对象**，只能作为基类，用其指针/引用指向派生类对象

> 注意：C\+\+ 不允许实例化抽象类的对象，但是抽象类仍然可以定义构造函数。因为抽象类的派生类的构造函数可能需要调用基类的构造函数来初始化基类部分。

### 7\.8 虚基类（解决多继承的二义性）

多继承时，若多个基类又继承自同一个公共基类，公共基类会被重复继承多份，产生二义性。用 **`virtual`**** 声明虚基类**，让公共基类只保留一份。

```C++
class A { public: int x; };
class B : virtual public A {};      // 虚继承
class C : virtual public A {};      // 虚继承
class D : public B, public C {};    // D 中只有一份 A::x，不会二义
```

### 7\.9 其他继承特性

- 引入基类成员的 `using` 声明必须出现在类定义的成员声明中，不能出现在块作用域（函数体）内

---

## 八、类间关系

### 8\.1 关系强度

类间关系强度依次：**依赖 \< 关联 \< 聚合 \< 组合 \< 继承**

|关系|语义|实现方式|
|---|---|---|
|**依赖**|临时"用一下"（uses\-a）|成员函数的参数/成员函数的局部变量/成员函数的返回值|
|**关联**|长期"认识"（knows\-a）|指针或引用作为数据成员|
|**聚合**|松散的"集体"（has\-a）|部分可以脱离整体独立存在。指针（或引用），对象从外部传入|
|**组合**|牢固的"器官"（contains\-a）|部分的生命周期完全由整体控制。直接定义对象成员/指针 \+ 内部 new/delete|
|**继承**|天生的"血脉"（is\-a）|public 继承|

### 8\.2 关联强度细分

类间的联系依次减弱：**继承 \> 硬联系\(硬关联\) \> 强联系\(强关联\) \> 弱联系\(弱关联\) \> 软联系\(软关联\)**

- **硬关联**：A和B类间有双向关系，且B与A之间有强关联，则A与B之间存在硬关联

- **强关联**：B\.h必须包含A\.h才能编译成功，则A与B之间存在强关联

- **弱关联**：A\.cpp中必须包含B\.h才能编译通过，则A与B之间存在弱关联（最常见）

- **软关联**：A只使用了B的指针或引用，那么A与B之间存在软关联

### 8\.3 类间关系的实现方式

|实现方式|缺点|优点|
|---|---|---|
|**引用形式**|必须使用初始化列表、A类对象禁止赋值、aB1的生存期必须长于A类对象|\-|
|**对象形式**|定义类A前，必须有类B的完整定义|缺省的构造、复制、赋值就可以保证aB2对象数据的构造、复制、赋值|
|**指针形式**|可能需要自定义构造、复制、赋值、析构|灵活，完全由程序员控制|

### 8\.4 复用方式

- **黑盒复用**：比如通过组合或调用库函数，只关心接口不关心实现

- **白盒复用**：比如继承，了解内部细节后进行复用

### 8\.5 头文件依赖与前置声明

- 直接使用内联方式定义成员函数，可能增强文件的依赖性，可使用前置声明和外联实现避免

- 尽可能使用指针或引用数据成员，函数参数以指针或引用形式传递，函数的返回值不要使用传值方式

- Student 类当前包含 Dorm 对象，这要求 Dorm 的完整定义；仅持有 Dorm 的指针或引用，只需前置声明

- 外联实现只需前置声明；内联实现需要头文件

**去掉头文件依赖的条件：**
要去掉 `b.h` 中的 `#include "a.h"`，必须满足两个条件：

1. `b.h` 中只使用 A 的指针或引用，因为前置声明 `class A;` 只能用于不完整类型

2. 所有需要 A 完整定义的代码（如 `new A`、调用 `A::func()`）必须移到 `.cpp` 外联实现中，那里可以重新包含 `a.h`

---

## 九、枚举类型

### 9\.1 普通枚举 \(enum\)

- 枚举值必须是整数

- 第一个枚举值，缺省为0

- 后一个枚举值，若没有指定，则为前一个枚举值\+1

- 内部就是 int

- int 与枚举量可随意转换，不安全

- 存在同名枚举值的名字冲突问题

```C++
enum weekday{MON=1,TUR,WED,THU,FRI,SAT, SUN=0};
```

- 枚举值 N 是编译时常量

### 9\.2 枚举类 \(enum class\)

- 枚举值可以指定类型、指定数值

- 枚举值不再允许与 int 随意转换、比较

- 使用时必须指明 scope（即枚举类名）

- 需要显式转换，能避免名字冲突

---

## 十、内存管理

### 10\.1 new/delete 与 malloc/free 的区别

|对比项|new / delete|malloc / free|
|---|---|---|
|**类型安全**|类型安全。返回具体类型的指针|非类型安全。返回 `void*`，必须强制类型转换|
|**构造/析构**|分配内存 \+ 调用构造函数；释放前调用析构函数|只分配/释放原始内存，不调用构造/析构|
|**大小计算**|自动计算所需空间大小|必须手动计算大小（`sizeof`）|
|**错误处理**|分配失败抛出 `std::bad_alloc` 异常|分配失败返回 `NULL`|
|**是否可重载**|允许重载全局或类的 `operator new`|C标准库函数，不可重载|

> ⚠️ 重要提示：如果是 C\+\+ 对象（尤其是含有虚函数或 STL 容器的类），绝对不能用 `malloc` 来分配，否则导致程序崩溃。

### 10\.2 operator new/delete

- 用户可自定义重载的 `operator new` 和 `operator delete`

- 一定是静态\(static\)的，无论是否有static关键字

- 若没有显式提供，则使用全局的 `::operator new` 和 `::operator delete`

```C++
void * operator new(std::size_t);
void * operator new(std::size_t, const string& a);
void   operator delete(void *);
```

### 10\.3 数组与构造函数

- 对象数组（如 `Card cards[54];`）必须要有无参构造函数

- 指针对象数组（如 `Card * cards[54];`）不需要无参构造函数

### 10\.4 智能指针

- 需要包含头文件：`#include <memory>`

```C++
unique_ptr<T> ptrT = make_unique<T>(3);
```

---

## 十一、模板与 STL

### 11\.1 函数模板 ★高频考点

```C++
template <class T>              // 或写 template <typename T>
T myMax(T a, T b) {             // T 是类型参数
    return a > b ? a : b;
}
myMax(3, 5);          // T 自动推导为 int
myMax(3.1, 2.2);      // T 自动推导为 double
myMax<int>(3, 5);     // 也可显式指定类型
```

格式：

```C++
template <class T1, class T2, ...>
返回值类型 函数名(形参列表) { ... }
```

### 11\.2 类模板 ★★设计题高频（栈、容器）

```C++
template <class T>
class Stack {                   // 一个能存任意类型的栈
    T data[100];
    int top;
public:
    Stack() : top(-1) {}
    void push(T x) { data[++top] = x; }
    T    pop()     { return data[top--]; }
    bool empty()   { return top == -1; }
    bool full()    { return top == 99; }
};

// 类模板的成员函数在类外定义时，写法：
// template <class T>
// 返回类型 Stack<T>::函数名(参数) { ... }

int main() {
    Stack<int> s1;        // 存 int 的栈，必须显式写类型！
    s1.push(10);
    Stack<string> s2;     // 存 string 的栈
    s2.push("hello");
    return 0;
}
```

**要点：**

- 类模板**创建对象时必须显式指定类型**：`Stack<int> s;`（不能像函数模板那样自动推导）

- 类外定义成员函数时，每个函数前都要加 `template <class T>`，且类名要写成 `Stack<T>`

### 11\.3 STL 容器基础

**vector 常用操作：**

- `v.push_back(x);` —— 在末尾添加元素

- `v.size();` —— 获取元素个数

- `v.begin();` —— 首元素迭代器

- `v.end();` —— 尾后迭代器

---

## 十二、输入输出

### 12\.1 控制台 I/O

- 预定义流对象：`cin`（istream 类，标准输入）、`cout`（ostream 类，标准输出）、`cerr`、`clog`

- 需要 `#include <iostream>`

- `cout` 和 `cin` 是 C\+\+ 标准库中预定义的对象，而不是函数

### 12\.2 getline —— 读取一整行 ★填空题常考

`cin >> s` 遇到空格就停止，读不了带空格的整行。读整行要用 `getline`：

```C++
#include <string>
string line;
getline(cin, line);      // 读一整行（含空格），直到换行；换行不存入 line
```

### 12\.3 istringstream —— 从字符串里提取数据 ★填空题常考

把一个字符串当成输入流，按空格逐个提取出来（常用于解析一行数据）。

```C++
#include <sstream>       // istringstream 的头文件
#include <string>
string line = "10 20 30 hello";
istringstream iss(line);  // 用字符串构造一个输入字符串流
int a, b, c;
string word;
iss >> a >> b >> c >> word;   // 像 cin 一样按空格提取
```

**典型组合用法（先读一整行，再拆分）：**

```C++
string line;
getline(cin, line);            // 读一整行
istringstream iss(line);       // 放进字符串流
int x;
while (iss >> x) {             // 逐个提取数字
    cout << x << endl;
}
```

### 12\.4 文件 I/O

```C++
#include <fstream>
ofstream fout("data.txt");     // 输出文件流（写文件）
fout << "hello" << 123;
fout.close();

ifstream fin("data.txt");      // 输入文件流（读文件）
int n;
fin >> n;
fin.close();
```

**打开方式（mode）：**

- `ios::out`：写。文件已存在则**先清空内容**；不存在则创建。文件指针指向开头。（相当于 C 的 `"w"`）

- `ios::app`：追加。文件指针指向**末尾**，不清空原内容；不存在则创建。（相当于 C 的 `"a"`）

- `ios::binary`：二进制方式，可与上面组合，如 `ios::out | ios::binary`

- 默认是**文本方式**打开

---

## 十三、设计模式与综合应用

### 13\.1 单例模式

```C++
class Singleton {
public:
    // 提供全局访问点
    static Singleton& getInstance() {
        static Singleton instance; // 局部静态变量，第一次调用时初始化，且线程安全
        return instance;
    }

    // 业务逻辑函数
    void doSomething() { /* ... */ }

private:
    // 1. 私有构造、析构函数（防止外部 new 和 delete）
    Singleton()  = default;
    ~Singleton() = default;

    // 2. 禁用拷贝构造和赋值函数
    Singleton(const Singleton&) = delete;
    Singleton& operator=(const Singleton&) = delete;
};
```

### 13\.2 综合编程题范例

#### 范例一：小汽车类 Car

```C++
#include<iostream>
#include<cstring>
using namespace std;

class Car
{
    char* pName; /*发动机名字*/
    int iId; /*车架号，自动产生具有唯一性*/
    int iI; /*行车里程数，初始化为0*/
    static int idCount; //静态成员变量，用于生成唯一的车架号
public:
    Car(const char* name) : iI(0) {
        pName = new char[strlen(name) + 1];
        strcpy(pName, name);
        iId = ++idCount;
    }
    Car(const Car& car) : iI(car.iI), iId(++idCount) {
        pName = new char[strlen(car.pName) + 1];
        strcpy(pName, car.pName);
    }
    ~Car() {
        delete[] pName;
    }
    Car& operator=(const Car& car) {
        if (this != &car) {
            delete[] pName;
            pName = new char[strlen(car.pName) + 1];
            strcpy(pName, car.pName);
            iI = car.iI;
        }
        return *this;
    }
    Car& operator+(int x) {
        iI += x;
        return *this;
    }
    Car& operator++() {
        ++iI;
        return *this;
    }
    friend ostream& operator<<(ostream& os, const Car& car) {
        os << "发动机名字: " << car.pName << endl;
        os << "车架号: " << car.iId << endl;
        os << "行车里程数: " << car.iI << endl;
        return os;
    }
};

int Car::idCount = 0;
```

#### 范例二：手机业务管理系统（多态）

```C++
#include <iostream>
#include <vector>
using namespace std;

class Service {
public:
    virtual double getCharge() const = 0;
    virtual ~Service() = default;
};

class CallService : public Service {
public:
    CallService(double rate, int minutes) : rate(rate), minutes(minutes) {}
    virtual double getCharge() const {
        return rate * minutes;
    }
private:
    double rate;
    int minutes;
};

class DataService : public Service {
public:
    DataService(double rate, int bytes) : rate(rate), bytes(bytes) {}
    virtual double getCharge() const {
        return rate * bytes;
    }
private:
    double rate;
    int bytes;
};

class Customer {
public:
    void addService(Service* service) {
        services.push_back(service);
    }
    double getTotalCharge() const {
        double total = 0.0;
        for (auto it : services) {
            total += it->getCharge();
        }
        return total;
    }
private:
    vector<Service*> services;
};
```

#### 范例三：通信工具系统（桥接模式思想）

移动通信功能（3G、4G、WiFi等）和通信软件（QQ、微信等）两个独立变化维度，通过组合实现灵活搭配。

```C++
// 移动通信功能抽象基类
class MobileCommunication {
public:
    virtual ~MobileCommunication() = default;
    virtual void send(const string& data) = 0;
    virtual string receive() = 0;
};

// 通信软件抽象基类
class CommunicationSoftware {
public:
    virtual ~CommunicationSoftware() = default;
    virtual void login(const string& username, const string& password) = 0;
    virtual void sendMessage(const string& message) = 0;
    virtual string receiveMessage() = 0;
};

// 通信工具类，组合两者
class CommunicationTool {
public:
    CommunicationTool(MobileCommunication* mc, CommunicationSoftware* cs)
        : mc(mc), cs(cs) {}
    // ... 通过组合调用实现功能
private:
    MobileCommunication* mc;
    CommunicationSoftware* cs;
};
```

#### 范例四：群的设计（组合模式）

支持任意个数、任意深度组合不同性质子群，体现信息共享功能。

```C++
class Group {
public:
    Group(const string& name, const string& description)
        : name(name), description(description) {}

    void addMember(const string& member) { members.push_back(member); }
    void addSubgroup(Group* group) { subgroups.push_back(group); }

    void sendMessage(const string& message) {
        for (const string& member : members) {
            cout << "Send message to " << member << ": " << message << endl;
        }
    }

private:
    string name;
    string description;
    vector<string> members;
    vector<Group*> subgroups; // 子群列表，支持任意深度嵌套
};
```

#### 范例五：小猫钓鱼（多态 \+ 策略模式）

鱼的效果由鱼自己决定，猫只负责钓鱼，符合开闭原则。

```C++
class Cat; // 前向声明

class Fish {
protected:
    int weight;
public:
    Fish(int w) : weight(w) {}
    virtual ~Fish() {}
    virtual void ApplyEffect(Cat& cat) = 0; // 纯虚函数，子类实现具体效果
};

class Octopus : public Fish {
public:
    Octopus(int w) : Fish(w) {}
    void ApplyEffect(Cat& cat) override;
};

class Cat {
private:
    int level;
    int exp;
    void HandleLevelUp() { /* 升级逻辑 */ }
public:
    Cat(int lv = 1, int e = 0) : level(lv), exp(e) {}
    void CatchFish(Fish* fish) {
        fish->ApplyEffect(*this); // 多态：鱼自己决定对猫的影响
        HandleLevelUp();
    }
};
```

#### 范例六：宝箱与角色（双重分派）

```C++
class Box {
public:
    virtual ~Box() {}
};

class MoneyBox : public Box {
public:
    int money;
    MoneyBox(int m) : money(m) {}
};

class PoisonBox : public Box {};

class Role {
public:
    int hp;
    int money;
    Role(int h = 100, int m = 0) : hp(h), money(m) {}
    virtual ~Role() {}
    virtual void Open(Box* b) = 0;
};

class Soldier : public Role {
public:
    void Open(Box* b) override {
        if (auto mb = dynamic_cast<MoneyBox*>(b)) {
            // 战士开金钱宝箱的效果
        } else if (auto pb = dynamic_cast<PoisonBox*>(b)) {
            // 战士开毒物宝箱的效果
        }
    }
};
```

#### 范例七：艺术家与艺术品（多态）

```C++
class Artwork {
protected:
    string author;
    string title;
    string year;
public:
    Artwork(const string& a, const string& t, const string& y) 
        : author(a), title(t), year(y) {}
    virtual ~Artwork() {}
    virtual void Display() const = 0;
};

class Painting : public Artwork {
private:
    int width, height;
public:
    Painting(const string& a, const string& t, const string& y, int w, int h) 
        : Artwork(a, t, y), width(w), height(h) {}
    void Display() const override {
        cout << "【油画】作者:" << author << ", 标题:" << title 
             << ", 创作年份:" << year << ", 尺寸: 宽" << width << "x高" << height << endl;
    }
};

class Artist {
private:
    string name;
public:
    Artist(const string& n) : name(n) {}
    void Show(const Artwork& artwork) const {
        cout << "艺术家 " << name << " 正在鉴赏：\n";
        artwork.Display(); // 多态调用
    }
};
```

#### 范例八：字典（键值对映射）

```C++
class Dictionary {
private:
    vector<pair<L*, R*>> elements; 
public:
    void add(L* objL, R* objR) {
        // 检查左元是否已存在，存在则更新
        for (auto& entry : elements) {
            if (*(entry.first) == *objL) { 
                entry.second = objR;
                return;
            }
        }
        elements.push_back({objL, objR});
    }

    R* find(L* objL) {
        for (auto& entry : elements) {
            if (*(entry.first) == *objL) {
                return entry.second;
            }
        }
        return nullptr;
    }
};
```

#### 范例九：果树与果园（多态）

```C++
class FruitTree {
protected:
    int age;
public:
    FruitTree(int a) : age(a) {}
    virtual ~FruitTree() {}
    virtual int getAnnualValue() const = 0; // 纯虚函数：计算年产值
    void grow() { age++; }
};

class AppleTree : public FruitTree {
public:
    AppleTree(int a) : FruitTree(a) {}
    int getAnnualValue() const override {
        // 根据树龄计算苹果树年产值
        if (age < 5) return 0;
        else if (age <= 15) return 1200 + (age - 5) * 50;
        else if (age <= 20) return 2000 - age * 60;
        else return 200;
    }
};

// 计算果园总产值
int calculateTotalValue(const vector<FruitTree*>& orchard) {
    int total = 0;
    for (const auto& tree : orchard) {
        total += tree->getAnnualValue();
    }
    return total;
}
```

---

## 十四、经典判断题与易错题

### 判断题精选

1. **❌ 错误**：C\+\+ 不允许实例化抽象类的对象，因此抽象类中不需要设计构造函数。

    - 解析：抽象类仍然可以定义构造函数，因为派生类的构造函数可能需要调用基类的构造函数来初始化基类部分。

2. **❌ 错误**：在异常处理时，异常抛出后，会导致所有程序被终止运行。

    - 解析：当异常被抛出时，程序并不会立即终止运行。程序会在调用栈中查找能够处理该异常的异常处理程序。如果找到了，程序将继续执行；否则，程序将终止运行。

3. **✅ 正确**：通过常量对象只能直接调用常成员函数和静态成员函数。

4. **❌ 错误**：如果类B和类A中都只由成员函数组成，则 `sizeof(B)` 一定等于 `sizeof(A)`。

    - 解析：如果类中有虚函数，对象中会包含一个指向虚函数表的指针。如果类 B 和类 A 的虚函数表不同，`sizeof` 的值可能不同。此外，多重继承或虚继承也会影响对象大小。

5. **❌ 错误**：若有类A、类B，且 `B b; A a = b;`，则类B一定是通过public继承方式从类A派生的。

    - 解析：类 B 可能是通过公有、保护或私有继承方式从类 A 派生的。也有可能类 B 和类 A 没有任何继承关系，但是定义了从类 B 到类 A 的类型转换运算符或者类 A 有一个接受类 B 对象作为参数的构造函数。

6. **❌ 错误**：对已知函数 `void func(int a=0, int b=0, int c=0)`，用 `func(2)` 进行调用，参数匹配时各参数的实参值分别为 `a=0, b=0, c=2`。

    - 解析：默认参数从左到右依次匹配，结果是 `a=2, b=0, c=0`。

7. **❌ 错误**：对象可以隐式或显式地使用this指针来引用自身数据成员和函数成员，this指针是对象自身的一部分，this指针占用的内存大小可以反映在对对象进行sizeof的运算结果中。

    - 解析：this指针并不是对象自身的一部分，它不占用内存空间。

8. **✅ 正确**：根据里氏替换原则，需要基类对象的任何地方都可以使用其派生类的对象来替代，程序的行为没有变化。

9. **✅ 正确**：虚函数是派生类对基类的覆盖而不是重载，其判断依据是参数的个数、类型、顺序、返回类型必须相同。

10. **❌ 错误**：静态数据成员在main函数之前构造、生命周期等同于全局对象，所以不需要其他声明情况下就可以在程序的任何地方访问到静态数据成员。

    - 解析：静态数据成员在程序的任何地方都可以访问到（只要它在作用域内），但是需要注意访问权限（如公有、保护或私有）。

### 高频易混淆点总结

- 构造顺序：基类→成员对象→派生类；析构顺序相反

- 静态成员只有一份、被共享，静态数据成员**类外初始化**，静态成员函数**没有 this**

- 友元不是成员函数、关系不双向、不能继承

- 虚函数靠 `virtual`，**通过基类指针/引用调用**才有动态绑定

- 纯虚函数 `=0`，含纯虚函数的类是抽象类，**不能创建对象**

- 拷贝构造函数三种调用时机：初始化对象、值传参、做返回值

- **有指针成员就要自己写深拷贝**

- 重载只看**参数（个数/类型）**，**返回值不同不算重载**

- 引用必须初始化、不能改指向；指针可后赋值、可改指向

- `class` 默认 `private`，`struct` 默认 `public`

---

## 十五、实用技巧

### 15\.1 取模运算技巧

把任意整数x映射到0\~n\-1之间的非负整数：

```C++
int res = (x % n + n) % n;
```

任意整数映射到对称区间 \[\-n\+1, n\-1\]：

```C++
int res = (x % n + n) % n;
if(res > n/2) res -= n;
```

### 15\.2 常用代码片段速查

|功能|代码|
|---|---|
|读一整行|`getline(cin, line);`|
|拆分字符串|`istringstream iss(line); iss >> a >> b;`|
|vector添加元素|`v.push_back(x);`|
|vector大小|`v.size();`|
|重载 `<<`|`friend ostream& operator<<(ostream&out,const T&t){...;return out;}`|
|swap（引用）|`void swap(int &a, int &b){int t=a;a=b;b=t;}`|

---

> **整理说明**：本文档整合了《根据奔腾整理的》、《C\+\+知识点总结》、《C\+\+复习》三份资料的内容，按知识点系统归类，适合期末复习和考前冲刺使用。

> （注：部分内容可能由 AI 生成）
