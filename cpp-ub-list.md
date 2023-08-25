<img src="logo.png" align="right"/>

# 附录：C++ 未定义行为成因列表 ![Version](https://img.shields.io/badge/version-1.3.2-brightgreen)

&emsp;&emsp;未定义的行为（Undefined Behavior），指程序不可预测的执行效果，一般由错误的代码实现引起。出于效率、兼容性等多方面原因，语言标准不便于定义错误程序的明确行为，而是将其统称为“未定义”的行为，可以是崩溃，也可以是非预期的任意表现，有些问题在编译器和执行环境的特殊处理下也可能不会造成实质性的危害，但不具备可移植性。代码质量管理的一个重要目标就是消除会导致未定义行为的代码。

&emsp;&emsp;C\+\+ 标准声明了导致未定义行为的情况，本文梳理了 ISO/IEC 14882:2003 和 ISO/IEC 14882:2011 前 18 章的相关内容，后 12 章的主题为标准库实现，相关内容的主旨在前 18 章中已有体现，C 语言相关内容可参见《[C 未定义行为成因列表](https://github.com/Qihoo360/safe-rules/blob/main/c-ub-list.md)》。
 
<br/>

|   ID   | Circumstance |  Provenance  |
| :----: |    :----     |   :----      |
| 1 | [代码行以反斜杠结尾，且与下一行连接后生成通用字符名称](#_1) | [`11-2.2(2)`](#_1) |
| 2 | [非空源文件未以换行符结尾，或以换行符结尾但换行符之前是反斜杠](#_2) | [`03-2.1(2)`](#_2) |
| 3 | [连接预处理符号时产生了通用字符名称](#_3) | [`11-2.2(4)`](#_3) |
| 4 | [存在不符合词法的单引号或双引号](#_4) | [`11-2.5(2)`](#_4) |
| 5 | [在 \#include 指令中，'、"、\\、//、/\* 出现在定界符 <  > 之间，或 '、\\、//、/\* 出现在定界符 " 之间](#_5) | [`03-2.8(2)`](#_5) |
| 6 | [无后缀 10 进制整数字面常量超过 long int 取值范围](#_6) | [`03-2.13.1(2)`](#_6) |
| 7 | [使用非标准转义字符](#_7) | [`03-2.13.2(3)`](#_7) |
| 8 | [修改字符串字面常量](#_8) | [`11-2.14.5(12)`](#_8) |
| 9 | [窄字符串与宽字符串连接](#_9) | [`03-2.13.4(3)`](#_9) |
| 10 | [违反 One Definition Rule](#_10) | [`11-3.2(5)`](#_10) |
| 11 | [具有静态或线程存储期的对象在析构函数中调用 std::exit 函数](#_11) | [`11-3.6.1(4)`](#_11) |
| 12 | [函数内具有静态或线程存储期的对象已析构，之后该函数又被调用并引用到已析构的对象](#_12) | [`11-3.6.3(2)`](#_12) |
| 13 | [在对象析构之后使用对象](#_13) | [`11-3.6.3(4)`](#_13) |
| 14 | [指针指向长度为 0 的内存空间并被解引用](#_14) | [`11-3.7.4.1(2)`](#_14) |
| 15 | [内存回收函数抛出异常](#_15) | [`11-3.7.4.2(3)`](#_15) |
| 16 | [使用不匹配的方法分配回收资源](#_16) | [`11-3.7.4.2(3)`](#_16) |
| 17 | [使用已被释放的指针](#_17) | [`11-3.7.4.2(4)`](#_17) |
| 18 | [对象生命周期已结束，但未调用其有副作用的析构函数](#_18) | [`11-3.8(4)`](#_18) |
| 19 | [在分配空间后，生命周期开始前，或在生命周期结束后，回收空间前，通过指针访问对象](#_19) | [`11-3.8(5)`](#_19) |
| 20 | [在分配空间后，生命周期开始前，或在生命周期结束后，回收空间前，通过 glvalue 访问对象](#_20) | [`11-3.8(6)`](#_20) |
| 21 | [具有静态、线程或自动存储期和 non\-trivial 析构函数的对象，其空间被非兼容类型的对象占据](#_21) | [`11-3.8(8)`](#_21) |
| 22 | [常量对象的空间或曾属于常量对象的空间被其他对象占据](#_22) | [`11-3.8(9)`](#_22) |
| 23 | [通过 glvalue 访问对象，但 glvalue 的类型不符合要求](#_23) | [`11-3.10(10)`](#_23) |
| 24 | [通过 glvalue 引用不相关类型的对象或未初始化的对象](#_24) | [`11-4.1(1)`](#_24) |
| 25 | [浮点类型转换产生的结果无法在相应的空间中表示](#_25) | [`11-4.8(1)`](#_25) |
| 26 | [浮点类型转为整数类型时，整数类型无法存储浮点类型的整数部分](#_26) | [`11-4.9(1)`](#_26) |
| 27 | [整数类型转为浮点类型时，浮点类型无法存储整数的值](#_27) | [`11-4.9(2)`](#_27) |
| 28 | [表达式求值依赖无确定顺序的副作用](#_28) | [`03-5(4)`](#_28) |
| 29 | [表达式的结果在数学上没有定义](#_29) | [`11-5(4)`](#_29) |
| 30 | [被调用函数的语言链接性与该函数定义的语言链接性不符](#_30) | [`11-5.2.2(1)`](#_30) |
| 31 | [将非 POD 对象传入可变参数列表](#_31) | [`03-5.2.2(7)`](#_31) |
| 32 | [用 static\_cast 将基类引用转为派生类引用，基类为虚基类，或引用的实际对象并非派生类对象](#_32) | [`11-5.2.9(2)`](#_32) |
| 33 | [用 static\_cast 将基类指针转为派生类指针，基类为虚基类，或指向的实际对象并非派生类对象](#_33) | [`11-5.2.9(11)`](#_33) |
| 34 | [用 static\_cast 将成员指针转为基类成员指针时，基类中没有相关成员](#_34) | [`11-5.2.9(12)`](#_34) |
| 35 | [函数指针被转为不兼容的类型并执行](#_35) | [`11-5.2.10(6)`](#_35) |
| 36 | [类型转换时去掉对象 const 属性并修改对象](#_36) | [`11-5.2.11(7)`](#_36) |
| 37 | [对不完整类型的对象取地址，但该对象的完整类型重载了 operator &](#_37) | [`11-5.3.1(5)`](#_37) |
| 38 | [new 运算符第一个数组维度的参数为负数](#_38) | [`03-5.3.4(6)`](#_38) |
| 39 | [用 delete 释放数组漏写中括号，用 delete 释放对象多写中括号，用 delete 释放非 new 表达式的结果](#_39) | [`11-5.3.5(2)`](#_39) |
| 40 | [被 delete 释放的对象或数组类型不符合要求](#_40) | [`11-5.3.5(3)`](#_40) |
| 41 | [用 delete 释放不完整类型的对象，但在对象完整类型声明中有 non\-trivial 析构函数](#_41) | [`11-5.3.5(5)`](#_41) |
| 42 | [对象解引用成员指针时，对象的动态类型不包含成员指针引用的成员](#_42) | [`11-5.5(4)`](#_42) |
| 43 | [对象解引用成员指针时，成员指针为空指针](#_43) | [`11-5.5(6)`](#_43) |
| 44 | [/ 或 % 运算符第二个操作数的值为 0](#_44) | [`11-5.6(4)`](#_44) |
| 45 | [指针加减整数，结果超出了指针指向数组的地址范围，使指针的值溢出](#_45) | [`11-5.7(5)`](#_45) |
| 46 | [未指向同一数组的指针相减](#_46) | [`11-5.7(5)`](#_46) |
| 47 | [移位运算符右操作数为负数或超过相关类型比特位的数量](#_47) | [`11-5.8(1)`](#_47) |
| 48 | [对有符号整数进行超出取值范围的左移运算](#_48) | [`11-5.8(2)`](#_48) |
| 49 | [将对象的值赋给具有部分重叠区域的对象](#_49) | [`11-5.17(8)`](#_49) |
| 50 | [有返回值的函数没有通过 return 语句返回](#_50) | [`11-6.6.3(2)`](#_50) |
| 51 | [递归地定义和初始化静态对象](#_51) | [`11-6.7(4)`](#_51) |
| 52 | [修改非 mutable 常量对象](#_52) | [`11-7.1.6.1(4)`](#_52) |
| 53 | [使用没有 volatile 限定的 glvalue 访问有 volatile 限定的对象](#_53) | [`11-7.1.6.1(6)`](#_53) |
| 54 | [具有 noreturn 属性的函数返回至调用方](#_54) | [`11-7.6.3(2)`](#_54) |
| 55 | [空指针解引用](#_55) | [`11-8.3.2(5)`](#_55) |
| 56 | [对象的实际类型与当前静态类型不相关，并调用其非静态成员函数](#_56) | [`11-9.3.1(2)`](#_56) |
| 57 | [在构造函数或析构函数中调用纯虚函数](#_57) | [`11-10.4(6)`](#_57) |
| 58 | [对象的实际类型与当前静态类型不相关，并调用其析构函数](#_58) | [`11-12.4(13)`](#_58) |
| 59 | [在对象生命周期结束后调用其析构函数](#_59) | [`11-12.4(15)`](#_59) |
| 60 | [基类对象构造完毕之前调用成员函数](#_60) | [`11-12.6.2(13)`](#_60) |
| 61 | [对成员或基类对象的不合理引用](#_61) | [`11-12.7(1)`](#_61) |
| 62 | [将对象指针转为其基类对象的指针时，基类对象尚未开始构造或已结束析构](#_62) | [`11-12.7(3)`](#_62) |
| 63 | [正在构造或析构的对象通过 . 或 \-> 调用虚函数，而且该对象与当前构造或析构函数不属于同一个类或基类](#_63) | [`11-12.7(4)`](#_63) |
| 64 | [typeid 作用于正在构造或析构的对象，而且该对象与当前构造或析构函数不属于同一个类或基类](#_64) | [`11-12.7(5)`](#_64) |
| 65 | [dynamic\_cast 作用于正在构造或析构的对象，而且该对象与当前构造或析构函数不属于同一个类或基类](#_65) | [`11-12.7(6)`](#_65) |
| 66 | [对模板函数进行非良构调用，或在模板定义和实例化上下文之外有更好的候选函数匹配](#_66) | [`11-14.6.4.2(1)`](#_66) |
| 67 | [需要无限递归的模版实例化](#_67) | [`11-14.7.1(15)`](#_67) |
| 68 | [构造或析构函数在 function\-try\-block 的 handler 中访问非静态成员](#_68) | [`11-15.2(10)`](#_68) |
| 69 | [有返回值的函数在 function\-try\-block 的 handler 中没有正确返回](#_69) | [`11-15.3(15)`](#_69) |
| 70 | [在 \#if、 \#elif 的条件中，由宏展开产生了 defined 表达式，或 defined 表达式格式不正确](#_70) | [`11-16.1(4)`](#_70) |
| 71 | [\#include 指令经宏展开后不满足标准格式](#_71) | [`11-16.2(4)`](#_71) |
| 72 | [宏的实参列表中出现预处理指令](#_72) | [`11-16.3(11)`](#_72) |
| 73 | [预处理运算符 \# 的结果不是有效的字符串](#_73) | [`11-16.3.2(2)`](#_73) |
| 74 | [预处理运算符 \#\# 的结果不是有效的符号](#_74) | [`11-16.3.3(3)`](#_74) |
| 75 | [\#line 指定的行号为 0 或大于规定值](#_75) | [`11-16.4(3)`](#_75) |
| 76 | [\#line 指令不符合标准格式](#_76) | [`11-16.4(5)`](#_76) |
| 77 | [用 \#define 定义或用 \#undef 取消定义具有保留意义的名称](#_77) | [`11-16.8(4)`](#_77) |
| 78 | [供语言机制调用的函数不符合要求](#_78) | [`11-17.3.21`](#_78) |
| 79 | [程序实现了应由标准库提供的功能](#_79) | [`11-17.3.22`](#_79) |
| 80 | [未经许可，向 std 命名空间添加声明或定义](#_80) | [`11-17.6.4.2.1(1)`](#_80) |
| 81 | [对标准库，特化模板类成员函数，特化模板类成员模板函数，特化、偏特化成员类模版](#_81) | [`11-17.6.4.2.1(2)`](#_81) |
| 82 | [未经许可，向 posix 命名空间添加声明或定义](#_82) | [`11-17.6.4.2.2(1)`](#_82) |
| 83 | [声明或定义标准库保留名称](#_83) | [`11-17.6.4.3(2)`](#_83) |
| 84 | [编译器未提供标准头文件，但编译时引入了非标准同名头文件](#_84) | [`11-17.6.4.4(1)`](#_84) |
| 85 | [为标准库函数提供不符合要求的参数](#_85) | [`11-17.6.4.9(1)`](#_85) |
| 86 | [多线程调用标准库函数造成数据竞争](#_86) | [`11-17.6.4.10(1)`](#_86) |
| 87 | [违反标准库函数要求的前置条件，除非标准库函数声明了这种情况会抛出异常](#_87) | [`11-17.6.4.11(1)`](#_87) |
| 88 | [offsetof 用于非 standard layout 类型，或用于计算静态成员以及成员函数的偏移量](#_88) | [`11-18.2(4)`](#_88) |
| 89 | [可变参数列表中省略号的前一个形式参数为引用、数组、函数，或具有与默认参数提升后不兼容的类型](#_89) | [`11-18.10(3)`](#_89) |
| 90 | [longjmp 跳转使应被执行的析构函数未被执行](#_90) | [`11-18.10(4)`](#_90) |

<br/>
<br/>
<br/>

### <span id="_1">1. 代码行以反斜杠结尾，且与下一行连接后生成通用字符名称</span>
<br/>

如果代码行以反斜杠结尾，预处理器会删除行尾的反斜杠和换行符，使其与下一行连接，如果连接后产生了以 \\u 或 \\U 开头的通用字符名称（universal character name）会导致未定义的行为。  
  
示例：
```
auto s = "\u54\
0D";               // Undefined behavior

auto \u54\
0D = 'a';          // Undefined behavior
```
这种代码可能不会通过编译，也可能不会造成实际问题，但一定不具备可移植性。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.1(2)-undefined  
ISO/IEC 14882:2011 2.2(2)-undefined  
<br/>

#### 规则
[ID_badBackslash](./c-cpp-rules.md#badbackslash)  
<br/>

<br/>
<br/>

### <span id="_2">2. 非空源文件未以换行符结尾，或以换行符结尾但换行符之前是反斜杠</span>
<br/>

示例：
```
namespace NS {
    ....
}\                 // Undefined behavior if this is the last line
```
C\+\+03 声明了这种情况会导致未定义的行为，C\+\+11 规定在这种情况下编译器应补全所需的空行。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.1(2)-undefined  
<br/>

#### 规则
[ID_missingNewLineFileEnd](./c-cpp-rules.md#missingnewlinefileend)  
[ID_badBackslash](./c-cpp-rules.md#badbackslash)  
<br/>

<br/>
<br/>

### <span id="_3">3. 连接预处理符号时产生了通用字符名称</span>
<br/>

预处理运算符 \#\# 可以将两个符号连接成一个符号，如果产生了以 \\u 或 \\U 开头的通用字符名称会导致未定义的行为。  
  
示例：
```
#define M(a, b) a ## b

int M(\u54, 0d) = 123;    // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.1(4)-undefined  
ISO/IEC 14882:2011 2.2(4)-undefined  
<br/>

<br/>
<br/>

### <span id="_4">4. 存在不符合词法的单引号或双引号</span>
<br/>

预处理器连接以反斜杠结尾的各行代码后将其转为预处理符号序列，在处理指令和展开宏之前，如果出现了不符合词法的单引号或双引号，会导致未定义的行为。  
  
示例：
```
#defined X  '     // Undefined behavior
#defined Y  "     // Undefined behavior
```
例中的引号无法与其他字符组成预处理符号，可能不会通过编译，也可能产生非预期的结果。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.4(2)-undefined  
ISO/IEC 14882:2011 2.5(2)-undefined  
<br/>

<br/>
<br/>

### <span id="_5">5. 在 \#include 指令中，'、"、\\、//、/\* 出现在定界符 <  > 之间，或 '、\\、//、/\* 出现在定界符 " 之间</span>
<br/>

示例：
```
#include <"foo">      // Undefined behavior in C++03
#include "foo//bar"   // Undefined behavior in C++03
```
程序在这种情况下的行为在 C\+\+03 中是未定义的，在 C\+\+11 中是由实现定义的。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.8(2)-undefined  
ISO/IEC 14882:2011 2.9(2)-implementation  
<br/>

#### 规则
[ID_nonStandardCharInHeaderName](./c-cpp-rules.md#nonstandardcharinheadername)  
[ID_forbidBackslashInHeaderName](./c-cpp-rules.md#forbidbackslashinheadername)  
<br/>

<br/>
<br/>

### <span id="_6">6. 无后缀 10 进制整数字面常量超过 long int 取值范围</span>
<br/>

示例：
```
cout << 2147483648;   // Undefined behavior in C++03
```
如果 long int 为 32 位有符号整数类型，字面常量 2147483648 超出了范围，其行为在 C\+\+03 中是未定义的，在 C\+\+11 中则会将 2147483648 归为 unsigned long int 类型。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.13.1(2)-undefined  
<br/>

<br/>
<br/>

### <span id="_7">7. 使用非标准转义字符</span>
<br/>

标准转义字符：
```
\a          // Alert
\b          // Backspace
\f          // Formfeed page break
\n          // New line
\r          // Carriage return
\t          // Horizontal tab
\v          // Vertical tab
\\          // Backslash
\?          // Question mark
\'          // Single quotation mark
\"          // Double quotation mark
\0          // Null character
\ddd        // Any character, ‘d’ is an octal number
\xhh        // Any character, ‘h’ is a hex number
```
反斜杠后如果出现其他形式的字符或字符序列会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.13.2(3)-undefined  
ISO/IEC 14882:2011 2.14.3(3)-implementation  
<br/>

#### 规则
[ID_literal_nonStandardEsc](./c-cpp-rules.md#literal_nonstandardesc)  
<br/>

<br/>
<br/>

### <span id="_8">8. 修改字符串字面常量</span>
<br/>

示例：
```
*((char*)"oops") = 'O';   // Undefined behavior, may crash
cout << "oops";           // If it doesn't crash, this might output ‘Oops’
```
修改字面常量是一种逻辑错误。多数通用系统会在运行时保护常量数据，相关修改会造成崩溃，在没有这种保护机制的系统中，字符串常量可能会被修改，但也可能会影响到其他相同的字符串常量，因为相同的字符串常量可能共用相同的存储空间。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.13.4(2)-undefined  
ISO/IEC 14882:2011 2.14.5(12)-undefined  
<br/>

#### 规则
[ID_constStrToNonConstPtr](./c-cpp-rules.md#conststrtononconstptr)  
<br/>

<br/>
<br/>

### <span id="_9">9. 窄字符串与宽字符串连接</span>
<br/>

示例：
```
auto* x = L"123" "456";    // Undefined in C++03
auto* y = L"123" "456";    // A wide string in C++11
```
C\+\+03 规定宽字符串与窄字符串连接会导致未定义的行为。C\+\+11 规定一个字符串有前缀一个没有的话，结果以有前缀的为准，其他情况由实现定义。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.13.4(3)-undefined  
ISO/IEC 14882:2011 2.14.5(13)-implementation  
<br/>

#### 规则
[ID_literal_hybridConcat](./c-cpp-rules.md#literal_hybridconcat)  
<br/>

<br/>
<br/>

### <span id="_10">10. 违反 One Definition Rule</span>
<br/>

任何翻译单元不得包含变量、函数、类型、枚举或模板的多个定义，即 One Definition Rule，违反此规则会导致未定义的行为。  
  
示例：
```
// In a.cpp 
struct T {
    int i;
};

// In b.cpp 
struct T {
    long i;    // Undefined behavior, violates One Definition Rule
};
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.2(5)-undefined  
ISO/IEC 14882:2011 3.2(5)-undefined  
<br/>

<br/>
<br/>

### <span id="_11">11. 具有静态或线程存储期的对象在析构函数中调用 std::exit 函数</span>
<br/>

程序调用 exit 函数后，全局、静态或 thread\_local 对象开始析构，而这种对象的析构函数再调用 exit 会导致未定义的行为。  
  
示例：
```
class T {
    ....
public:
   ~T() {
        std::exit(1);  // Dangerous
    }
};

static T obj;  // Undefined behavior during destruct
```
例中 obj 对象在析构时会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.6.1(4)-undefined  
ISO/IEC 14882:2011 3.6.1(4)-undefined  
<br/>

#### 规则
[ID_exitCallInDestructor](./c-cpp-rules.md#exitcallindestructor)  
<br/>

<br/>
<br/>

### <span id="_12">12. 函数内具有静态或线程存储期的对象已析构，之后该函数又被调用并引用到已析构的对象</span>
<br/>

示例：
```
// In T.cpp
void foo() {
    static T sObj;
    ....
}

// In U.cpp
class U {
    ....
public:
   ~U() {
        foo();   // Undefined behavior if ‘sObj’ is destructed
    }
};

U gObj;   // Problematic
```
当例中全局对象 gObj 析构时会调用 foo 函数，如果这时 foo 函数中的静态对象 sObj 已析构，会导致未定义的行为，gObj 与 sObj 的析构顺序在标准中是不确定的。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.6.3(2)-undefined  
ISO/IEC 14882:2011 3.6.3(2)-undefined  
<br/>

<br/>
<br/>

### <span id="_13">13. 在对象析构之后使用对象</span>
<br/>

示例：
```
extern T obj;

void foo() {
    obj.fun();   // Undefined behavior if ‘obj’ is destructed
}
```
当全局对象 obj 析构之后，再调用 foo 函数会导致未定义的行为（如在另一个全局对象的析构函数中调用 foo 函数）。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 3.6.3(4)-undefined  
<br/>

#### 规则
[ID_danglingDeref](./c-cpp-rules.md#danglingderef)  
<br/>

<br/>
<br/>

### <span id="_14">14. 指针指向长度为 0 的内存空间并被解引用</span>
<br/>

示例：
```
void foo(size_t n) {
    int* p = new int[n];
    *p = 123;              // Undefined behavior if ‘n’ is zero
    ....
    delete[] p;
}
```
如果例中参数 n 为 0，对 p 的解引用会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.7.3.1(2)-undefined  
ISO/IEC 14882:2011 3.7.4.1(2)-undefined  
<br/>

#### 规则
[ID_bufferOverflow](./c-cpp-rules.md#bufferoverflow)  
<br/>

<br/>
<br/>

### <span id="_15">15. 内存回收函数抛出异常</span>
<br/>

示例：
```
class A {
    ....
public:
    void operator delete(void* p) {
        if (!p) {
            throw Exception();    // Undefined behavior
        }
        ....
    }
};
```
在 delete 运算符中抛出异常会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 3.7.4.2(3)-undefined  
<br/>

#### 规则
[ID_throwInDelete](./c-cpp-rules.md#throwindelete)  
<br/>

<br/>
<br/>

### <span id="_16">16. 使用不匹配的方法分配回收资源</span>
<br/>

示例：
```
T* p = new T;
....
free(p);   // Undefined behavior
```
例中用 free 释放由 new 分配的内存空间，会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 3.7.4.2(3)-undefined  
<br/>

#### 规则
[ID_incompatibleDealloc](./c-cpp-rules.md#incompatibledealloc)  
<br/>

<br/>
<br/>

### <span id="_17">17. 使用已被释放的指针</span>
<br/>

示例：
```
int* p = new int(1);

delete p;      // Well-defined
delete p;      // Undefined behavior
cout << *p;    // Undefined behavior
```
指针指向的对象被回收后，指针的值和指针曾指向的对象均失效，继续访问会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.7.3.2(4)-undefined  
ISO/IEC 14882:2011 3.7.4.2(4)-undefined  
<br/>

#### 规则
[ID_illAccess](./c-cpp-rules.md#illaccess)  
[ID_doubleFree](./c-cpp-rules.md#doublefree)  
[ID_danglingDeref](./c-cpp-rules.md#danglingderef)  
<br/>

<br/>
<br/>

### <span id="_18">18. 对象生命周期已结束，但未调用其有副作用的析构函数</span>
<br/>

示例：
```
struct U {
    ....
};

struct T {
   ~T();     // If it has side effects
    ....
};

void* p = malloc(max(sizeof(T), sizeof(U)));
T* pT = new (p) T;
U* pU = new (p) U;   // Undefined behavior
```
例中第二个 new 表达式结束了 pT 所指对象的生命周期，但没有调用其析构函数，如果其析构函数存在副作用，则会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.8(4)-undefined  
ISO/IEC 14882:2011 3.8(4)-undefined  
<br/>

<br/>
<br/>

### <span id="_19">19. 在分配空间后，生命周期开始前，或在生命周期结束后，回收空间前，通过指针访问对象</span>
<br/>

在对象的生命周期之外，通过指针进行如下操作会导致未定义的行为：  
 - 访问非静态成员函数  
 - 将指针转为基类指针  
 - 用 static\_cast 转换指针（转为 void\*、char\*、unsigned char\* 等情况除外）  
 - 用 dynamic\_cast 转换指针  
  
示例：
```
struct T {
    T();
   ~T();
    void fun();
};

T* p = (T*)malloc(sizeof(T));
p->fun();   // Undefined behavior, the lifetime has not yet started

new (p) T();
p->fun();   // Well-defined

p->~T();
p->fun();   // Undefined behavior, the lifetime has ended

free(p);
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.8(5)-undefined  
ISO/IEC 14882:2011 3.8(5)-undefined  
<br/>

<br/>
<br/>

### <span id="_20">20. 在分配空间后，生命周期开始前，或在生命周期结束后，回收空间前，通过 glvalue 访问对象</span>
<br/>

在对象的生命周期之外，通过 glvalue 进行如下操作会导致未定义的行为：  
 - 进行 lvalue\-to\-rvalue 转换  
 - 访问非静态成员函数  
 - 向基类引用转换  
 - 用 static\_cast 转换 glvalue（转为 char& 或 unsigned char& 等情况除外）  
 - 用 dynamic\_cast 转换 glvalue  
 - 将 typeid 作用于 glvalue  
  
示例：
```
struct B { void foo(); };
struct D: B { .... };

void B::foo() {
    new (this) D;    // Ends the lifetime of *this
}

void bar(B& b) {
    b.foo();
    if (typeid(b) == ....) {   // Undefined behavior
        ....
    }
}
```
例中 b.foo 执行后 b 的生命周期结束，之后再对 b 的访问会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.8(6)-undefined  
ISO/IEC 14882:2011 3.8(6)-undefined  
<br/>

<br/>
<br/>

### <span id="_21">21. 具有静态、线程或自动存储期和 non\-trivial 析构函数的对象，其空间被非兼容类型的对象占据</span>
<br/>

示例：
```
struct A { ~A(); };
struct B { ~B(); };

void foo() {
    A a;
    new (&a) B;
}   // Undefined behavior
```
例中局部对象 a 的空间被不相关类型的对象占据，在 foo 函数返回前仍会调用 A 的析构函数，导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.8(8)-undefined  
ISO/IEC 14882:2011 3.8(8)-undefined  
<br/>

<br/>
<br/>

### <span id="_22">22. 常量对象的空间或曾属于常量对象的空间被其他对象占据</span>
<br/>

示例：
```
struct T {
    T();
   ~T();
};

const T obj;

void foo() {
    obj.~T();
    new (&obj) const T;   // Undefined behavior
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.8(9)-undefined  
ISO/IEC 14882:2011 3.8(9)-undefined  
<br/>

<br/>
<br/>

### <span id="_23">23. 通过 glvalue 访问对象，但 glvalue 的类型不符合要求</span>
<br/>

只应通过以下类型的 glvalue 访问对象，否则导致未定义的行为：  
 - 对象的动态类型  
 - 用 const 或 volatile 限定的对象动态类型  
 - 与对象动态类型相似的类型（参见 ISO/IEC 14882:2011 4.4）  
 - 与对象动态类型对应的有符号或无符号类型  
 - 用 const 或 volatile 限定的与对象动态类型对应的有符号或无符号类型  
 - 包含上述类型的聚合或联合类型（不包括静态成员类型）  
 - 对象动态类型的基类类型（也包括被 const 或 volatile 限定的基类类型）  
 - char 或 unsigned char  
  
示例：
```
int i = 0;

cout << i;                 // Well-defined
cout << (const int&)i;     // Well-defined
cout << (unsigned int&)i;  // Well-defined
cout << (char&)i;          // Well-defined

cout << (long&)i;          // Undefined behavior
cout << (float&&)i;        // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.10(15)-undefined  
ISO/IEC 14882:2011 3.10(10)-undefined  
<br/>

#### 规则
[ID_castNoInheritance](./c-cpp-rules.md#castnoinheritance)  
<br/>

<br/>
<br/>

### <span id="_24">24. 通过 glvalue 引用不相关类型的对象或未初始化的对象</span>
<br/>

glvalue 的类型与其引用的对象类型不同且没有继承关系，或引用的对象未初始化，会导致未定义的行为。  
  
示例：
```
struct A { int i; };
struct B { int i; };

int foo(A& a) {
    return ((B&)a).i;   // Undefined behavior, unrelated type conversion
}

int foo() {
    int i;
    return i;   // Undefined behavior, ‘i’ is not initialized
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 4.1(1)-undefined  
ISO/IEC 14882:2011 4.1(1)-undefined  
<br/>

#### 规则
[ID_castNoInheritance](./c-cpp-rules.md#castnoinheritance)  
[ID_localInitialization](./c-cpp-rules.md#localinitialization)  
<br/>

<br/>
<br/>

### <span id="_25">25. 浮点类型转换产生的结果无法在相应的空间中表示</span>
<br/>

示例：
```
double d = FLT_MAX;
d = d * 10;
float f = d;   // Non-defined behavior
```
例中 d 的值超过了 float 的取值范围，将 d 的值转为 float 会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 4.8(1)-undefined  
ISO/IEC 14882:2011 4.8(1)-undefined  
<br/>

#### 规则
[ID_narrowCast](./c-cpp-rules.md#narrowcast)  
<br/>

<br/>
<br/>

### <span id="_26">26. 浮点类型转为整数类型时，整数类型无法存储浮点类型的整数部分</span>
<br/>

示例：
```
double f = 1e60;
signed i = f;      // Undefined behavior
```
例中 1e60 无法被整型变量存储，导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 4.9(1)-undefined  
ISO/IEC 14882:2011 4.9(1)-undefined  
<br/>

#### 规则
[ID_narrowCast](./c-cpp-rules.md#narrowcast)  
<br/>

<br/>
<br/>

### <span id="_27">27. 整数类型转为浮点类型时，浮点类型无法存储整数的值</span>
<br/>

示例：
```
long n = 1234567890L;
float f0 = n;            // Cannot be represented exactly
double f1 = n;           // OK
```
例中 1234567890 无法被 float 变量存储，但可被 double 变量存储。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 4.9(2)-undefined  
<br/>

#### 规则
[ID_narrowCast](./c-cpp-rules.md#narrowcast)  
<br/>

<br/>
<br/>

### <span id="_28">28. 表达式求值依赖无确定顺序的副作用</span>
<br/>

示例：
```
int a = 0;
int b = a + a++;    // Undefined behavior
```
例中加法运算符左右子表达式无明确的求值顺序，如果左子表达式先求值，b 的值是 0，如果右子表达式先求值，b 的值可能是 1 也可能是 0，因为 a\+\+ 的值是 0，但 a\+\+ 的副作用在表达式求值过程中何时生效也是不确定的。  
  
又如：
```
volatile int* p = foo();
int n = *p + *p;          // Undefined behavior
```
例中 n 的值在数学上应是 \*p 的二倍，但由于 p 指向 volatile 数据，结果可能不符合预期的数学关系。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5(4)-undefined  
<br/>

#### 规则
[ID_evaluationOrderReliance](./c-cpp-rules.md#evaluationorderreliance)  
[ID_confusingAssignment](./c-cpp-rules.md#confusingassignment)  
<br/>

<br/>
<br/>

### <span id="_29">29. 表达式的结果在数学上没有定义</span>
<br/>

如除 0、有符号整数溢出、负数位运算、浮点异常等均会导致未定义的行为。  
  
示例：
```
signed s = INT_MAX + 1;     // Undefined
unsigned u = UINT_MAX + 1;  // Well-defined
```
例中变量 u 的值一定是 0，而 s 的值是标准未定义的，往往由编译器和执行环境决定。  
  
设无符号整数的最大值为 M，无符号整数运算在程序中的结果是数学上的结果与 (M \+ 1) 取模的结果，这在数学上是有明确定义的，而对于有符号整数，将符号位移出相关比特位，或将非符号位移入符号位，在数学上是没有意义的，故称无符号整数不存在溢出问题，有符号整数存在溢出问题，溢出会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5(5)-undefined  
ISO/IEC 14882:2011 5(4)-undefined  
<br/>

#### 规则
[ID_divideByZero](./c-cpp-rules.md#dividebyzero)  
[ID_evalOverflow](./c-cpp-rules.md#evaloverflow)  
<br/>

<br/>
<br/>

### <span id="_30">30. 被调用函数的语言链接性与该函数定义的语言链接性不符</span>
<br/>

示例：
```
// In a.c
int foo() {
    return 0;
}

// In b.cpp
int foo();          // Missing extern "C"

int bar() {
    return foo();   // Undefined behavior
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.2.2(1)-undefined  
ISO/IEC 14882:2011 5.2.2(1)-undefined  
<br/>

<br/>
<br/>

### <span id="_31">31. 将非 POD 对象传入可变参数列表</span>
<br/>

可变参数列表是 C 语言的概念，C\+\+ 中具有拷贝构造或析构函数的对象难以与其兼容，如果将非 POD 对象传入可变参数列表，程序的行为在 C\+\+03 中是未定义的，在 C\+\+11 中是部分由实现定义的。  
  
示例：
```
string str;
void foo(int, ...);

foo(1, str);   // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.2.2(7)-undefined  
ISO/IEC 14882:2011 5.2.2(7)-implementation  
<br/>

#### 规则
[ID_nonPODVariadicArgument](./c-cpp-rules.md#nonpodvariadicargument)  
[ID_badVaArgType](./c-cpp-rules.md#badvaargtype)  
[ID_forbidVariadicFunction](./c-cpp-rules.md#forbidvariadicfunction)  
<br/>

<br/>
<br/>

### <span id="_32">32. 用 static\_cast 将基类引用转为派生类引用，基类为虚基类，或引用的实际对象并非派生类对象</span>
<br/>

示例：
```
struct A {};
struct B: A {};
struct C: virtual B {};

A a;
C c;
A& ra = a;
A& rc = c;

static_cast<B&>(ra);    // Undefined behavior
static_cast<C&>(rc);    // Undefined behavior
```
例中 ra 引用的是基类对象，将其转为派生类引用会导致未定义的行为，A 和 B 是 C 的虚基类，需要运行时数据体现虚基类对象和派生类对象的空间关系，static\_cast 不考虑与运行时相关的转换逻辑，无法正确转换。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.2.9(5)-undefined  
ISO/IEC 14882:2011 5.2.9(2)-undefined  
<br/>

#### 规则
[ID_downCast](./c-cpp-rules.md#downcast)  
<br/>

<br/>
<br/>

### <span id="_33">33. 用 static\_cast 将基类指针转为派生类指针，基类为虚基类，或指向的实际对象并非派生类对象</span>
<br/>

示例：
```
struct A {};
struct B: A {};
struct C: virtual B {};

A a;
C c;
A* pa = &a;
A* pc = &c;

static_cast<B*>(pa);    // Undefined behavior
static_cast<C*>(pc);    // Undefined behavior
```
例中 pa 指向基类对象，将其转为派生类指针会导致未定义的行为，A 和 B 是 C 的虚基类，需要运行时数据体现虚基类对象和派生类对象的空间关系，static\_cast 不考虑与运行时相关的转换逻辑，无法正确转换。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.2.9(8)-undefined  
ISO/IEC 14882:2011 5.2.9(11)-undefined  
<br/>

#### 规则
[ID_downCast](./c-cpp-rules.md#downcast)  
<br/>

<br/>
<br/>

### <span id="_34">34. 用 static\_cast 将成员指针转为基类成员指针时，基类中没有相关成员</span>
<br/>

示例：
```
struct B { int b; };
struct D: B { int d; };

int D::* mpb = &D::b;
int D::* mpd = &D::d;

static_cast<int B::*>(mpb);    // OK
static_cast<int B::*>(mpd);    // Undefined behavior
```
例中基类没有成员 d，将指向成员 d 的成员指针转为基类成员指针会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.2.9(9)-undefined  
ISO/IEC 14882:2011 5.2.9(12)-undefined  
<br/>

#### 规则
[ID_downCast](./c-cpp-rules.md#downcast)  
<br/>

<br/>
<br/>

### <span id="_35">35. 函数指针被转为不兼容的类型并执行</span>
<br/>

示例：
```
void foo();

return ((int(*)())(&foo))();  // Undefined behavior
```
例中 foo 函数没有返回值，将其强转为有返回值的函数并调用，会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.2.10(6)-undefined  
ISO/IEC 14882:2011 5.2.10(6)-undefined  
<br/>

#### 规则
[ID_functionPointerCast](./c-cpp-rules.md#functionpointercast)  
<br/>

<br/>
<br/>

### <span id="_36">36. 类型转换时去掉对象 const 属性并修改对象</span>
<br/>

示例：
```
const int ci = 0;
const_cast<int&>(ci) = 1;  // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.2.11(7)-undefined  
ISO/IEC 14882:2011 5.2.11(7)-undefined  
<br/>

#### 规则
[ID_qualifierCastedAway](./c-cpp-rules.md#qualifiercastedaway)  
<br/>

<br/>
<br/>

### <span id="_37">37. 对不完整类型的对象取地址，但该对象的完整类型重载了 operator &</span>
<br/>

示例：
```
struct T;   // Incomplete type

T* foo(T& obj) {
    return &obj;   // Undefined behavior
}

struct T {
    T* operator &();   // Overload
};
```
在 foo 函数中参数 obj 的类型是不完整的，但其完整类型重载了 operator &，导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.3.1(4)-undefined  
ISO/IEC 14882:2011 5.3.1(5)-undefined  
<br/>

#### 规则
[ID_overloadAddressOperator](./c-cpp-rules.md#overloadaddressoperator)  
<br/>

<br/>
<br/>

### <span id="_38">38. new 运算符第一个数组维度的参数为负数</span>
<br/>

示例：
```
int* foo(int n) {
    return new int[n];   // Undefined in C++03 if ‘n’ is negative
}
```
如果 n 为负数，程序的行为在 C\+\+03 中是未定义的，C\+\+11 去掉了这项未定义行为的声明。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.3.4(6)-undefined  
<br/>

<br/>
<br/>

### <span id="_39">39. 用 delete 释放数组漏写中括号，用 delete 释放对象多写中括号，用 delete 释放非 new 表达式的结果</span>
<br/>

示例：
```
string object;

delete &object;       // Undefined behavior
delete "string";      // Undefined behavior
delete new int[n];    // Undefined behavior
delete[] new int(n);  // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.3.5(2)-undefined  
ISO/IEC 14882:2011 5.3.5(2)-undefined  
<br/>

#### 规则
[ID_excessiveDelete](./c-cpp-rules.md#excessivedelete)  
[ID_insufficientDelete](./c-cpp-rules.md#insufficientdelete)  
[ID_incompatibleDealloc](./c-cpp-rules.md#incompatibledealloc)  
[ID_illDealloc](./c-cpp-rules.md#illdealloc)  
<br/>

<br/>
<br/>

### <span id="_40">40. 被 delete 释放的对象或数组类型不符合要求</span>
<br/>

用 delete 释放对象时，对象的静态类型应与动态类型兼容，如果静态类型是动态类型的基类，静态类型应提供虚析构函数，否则导致未定义的行为；用 delete\[\] 释放数组时，对象的静态类型应与动态类型一致，否则导致未定义的行为。  
  
示例：
```
struct B { ~B(); };
struct D: B { ~D(); };

B* pDObj = new D;
B* pDArr = new D[123];

delete pDObj;      // Undefined behavior
delete[] pDArr;    // Undefined behavior
```
例中基类 B 缺少虚析构函数，pDArr 的类型应为派生类指针。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.3.5(3)-undefined  
ISO/IEC 14882:2011 5.3.5(3)-undefined  
<br/>

#### 规则
[ID_missingVirtualDestructor](./c-cpp-rules.md#missingvirtualdestructor)  
[ID_arrayPointerCast](./c-cpp-rules.md#arraypointercast)  
<br/>

<br/>
<br/>

### <span id="_41">41. 用 delete 释放不完整类型的对象，但在对象完整类型声明中有 non\-trivial 析构函数</span>
<br/>

示例：
```
struct T;

void foo(T* p) {
    delete p;       // Undefined behavior
}

struct T {
   ~T();            // Non-trivial destructor
};
```
例中 delete 作用于不完整类型的指针 p，会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.3.5(5)-undefined  
ISO/IEC 14882:2011 5.3.5(5)-undefined  
<br/>

#### 规则
[ID_deleteIncompleteType](./c-cpp-rules.md#deleteincompletetype)  
<br/>

<br/>
<br/>

### <span id="_42">42. 对象解引用成员指针时，对象的动态类型不包含成员指针引用的成员</span>
<br/>

示例：
```
struct A { int a; };
struct B { int a, b; };

int foo(A* pa) {
    B* pb = (B*)pa;
    int B::* m = &B::b;
    return pb->*m;         // Undefined behavior
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.5(4)-undefined  
ISO/IEC 14882:2011 5.5(4)-undefined  
<br/>

<br/>
<br/>

### <span id="_43">43. 对象解引用成员指针时，成员指针为空指针</span>
<br/>

示例：
```
struct T { .... };

int foo(T& obj) {
    int T::* mp = nullptr;
    return obj.*mp;          // Undefined behavior
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.5(6)-undefined  
ISO/IEC 14882:2011 5.5(6)-undefined  
<br/>

<br/>
<br/>

### <span id="_44">44. / 或 % 运算符第二个操作数的值为 0</span>
<br/>

示例：
```
int foo(int a, int b) try
{
    return a / b;   // Undefined behavior if ‘b’ is zero
}
catch (...)
{
    return 0;       // Unreachable
}
```
除数为 0 会导致未定义的行为，且不受语言的异常机制控制。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.6(4)-undefined  
ISO/IEC 14882:2011 5.6(4)-undefined  
<br/>

#### 规则
[ID_divideByZero](./c-cpp-rules.md#dividebyzero)  
<br/>

<br/>
<br/>

### <span id="_45">45. 指针加减整数，结果超出了指针指向数组的地址范围，使指针的值溢出</span>
<br/>

设数组 a 的元素个数为 N，a 至 a \+ N 均为有效值（但不可对 a \+ N 解引用），超出这个范围的指针加减运算可能会使指针的值溢出，导致未定义的行为。  
  
示例：
```
int a[N];    // Let ‘N’ be the array size
int* p;

p = a;       // OK
p = a + N;   // OK, won't overflow

p = a - 1;       // May overflow, resulting in undefined behavior
p = a + N + 1;   // May overflow, resulting in undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.7(5)-undefined  
ISO/IEC 14882:2011 5.7(5)-undefined  
<br/>

#### 规则
[ID_arrayIndexOverflow](./c-cpp-rules.md#arrayindexoverflow)  
<br/>

<br/>
<br/>

### <span id="_46">46. 未指向同一数组的指针相减</span>
<br/>

示例：
```
int a[10];
int b[10];
ptrdiff_t d = a - b;  // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.7(5)-undefined  
ISO/IEC 14882:2011 5.7(5)-undefined  
<br/>

#### 规则
[ID_illPtrDiff](./c-cpp-rules.md#illptrdiff)  
<br/>

<br/>
<br/>

### <span id="_47">47. 移位运算符右操作数为负数或超过相关类型比特位的数量</span>
<br/>

示例：
```
int a = 1 << -1;    // Undefined behavior
int b = 1 << 100;   // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.8(1)-undefined  
ISO/IEC 14882:2011 5.8(1)-undefined  
<br/>

#### 规则
[ID_illShiftCount](./c-cpp-rules.md#illshiftcount)  
<br/>

<br/>
<br/>

### <span id="_48">48. 对有符号整数进行超出取值范围的左移运算</span>
<br/>

将符号位移出相关比特位，或将非符号位移入符号位，在数学上是没有意义的。  
  
示例：
```
int b = -1 << 1;    // Undefined
```
编译器可能会用乘法或除法实现有符号整数的移位，但不属于标准行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 5.8(2)-undefined  
<br/>

#### 规则
[ID_bitwiseOperOnSigned](./c-cpp-rules.md#bitwiseoperonsigned)  
<br/>

<br/>
<br/>

### <span id="_49">49. 将对象的值赋给具有部分重叠区域的对象</span>
<br/>

示例：
```
struct T {
    int a[10];
};

struct W {
    int i;
    T t;
};

union U {
    T t;
    W w;
};

void foo(U& u) {
    u.w.t = u.t;    // Undefined behavior
}
```
例中 u.w.t 和 u.t 具有部分重叠区域，导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.17(8)-undefined  
ISO/IEC 14882:2011 5.17(8)-undefined  
<br/>

#### 规则
[ID_overlappingAssignment](./c-cpp-rules.md#overlappingassignment)  
<br/>

<br/>
<br/>

### <span id="_50">50. 有返回值的函数没有通过 return 语句返回</span>
<br/>

示例：
```
bool foo(int i) {
    if (i >= 0) {
        return true;   // OK
    }
}   // Undefined behavior if i < 0
```
例中 foo 函数有返回值，当 i 的值小于 0 时没有通过 return 语句返回，导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 6.6.3(2)-undefined  
ISO/IEC 14882:2011 6.6.3(2)-undefined  
<br/>

#### 规则
[ID_notAllBranchReturn](./c-cpp-rules.md#notallbranchreturn)  
<br/>

<br/>
<br/>

### <span id="_51">51. 递归地定义和初始化静态对象</span>
<br/>

示例：
```
int foo(int i) {
    if (i < 100) {
        static int s = foo(2 * i);   // Undefined behavior
        return s;
    }
    return i;
}
```
例中静态局部变量 s 的初始化需要递归调用所属函数，是否会产生多个 s 的实例以及是否能正常结束递归，均是未定义的。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 6.7(4)-undefined  
ISO/IEC 14882:2011 6.7(4)-undefined  
<br/>

<br/>
<br/>

### <span id="_52">52. 修改非 mutable 常量对象</span>
<br/>

示例：
```
struct T {
    mutable int i;
    int j;
};

const T obj;
obj.i++;            // Well-defined
obj.j++;            // Ill-formed

T* p = (T*)&obj;
p->i = 0;           // Well-defined
p->j = 1;           // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 7.1.5.1(4)-undefined  
ISO/IEC 14882:2011 7.1.6.1(4)-undefined  
<br/>

#### 规则
[ID_qualifierCastedAway](./c-cpp-rules.md#qualifiercastedaway)  
<br/>

<br/>
<br/>

### <span id="_53">53. 使用没有 volatile 限定的 glvalue 访问有 volatile 限定的对象</span>
<br/>

示例：
```
volatile int v;   // Changed by other devices

void wait(int& r) {
    r = 0;
    while (r != 100);
}

int main() {
    wait((int&)v);   // Undefined behavior
}
```
例中变量 v 在定义处由 volatile 限定，但通过无 volatile 限定的引用访问，导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 7.1.5.1(7)-undefined  
ISO/IEC 14882:2011 7.1.6.1(6)-undefined  
<br/>

#### 规则
[ID_qualifierCastedAway](./c-cpp-rules.md#qualifiercastedaway)  
<br/>

<br/>
<br/>

### <span id="_54">54. 具有 noreturn 属性的函数返回至调用方</span>
<br/>

示例：
```
[[noreturn]] void foo() {
    throw Exception();          // OK
}

[[noreturn]] int bar() {
    return 0;                   // Undefined behavior
}

[[noreturn]] void baz(int i) {  // Undefined behavior if ‘i’ != 0
    if (i == 0) {
        throw Exception();
    }
}
```
例中 bar 可以正常返回，baz 在某条件下正常返回，会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 7.6.3(2)-undefined  
<br/>

#### 规则
[ID_unsuitableReturn](./c-cpp-rules.md#unsuitablereturn)  
<br/>

<br/>
<br/>

### <span id="_55">55. 空指针解引用</span>
<br/>

空指针表示没有指向任何对象的指针，通过空指针访问对象属于逻辑错误。  
  
示例：
```
struct T {
    int i;

    int foo() { return i; }
    int bar() { return 0; }

    static int baz();
};

T* p = nullptr;

p->foo();   // Undefined behavior
p->bar();   // Undefined behavior
p->baz();   // Well-defined, ‘baz’ is a static member
```
通过空指针调用对象的非静态成员函数会导致未定义的行为，即使成员函数没有引用成员数据。通过空指针调用静态成员函数不属于访问对象，所以这种情况不属于逻辑错误。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 8.3.2(4)-undefined  
ISO/IEC 14882:2011 8.3.2(5)-undefined  
<br/>

#### 规则
[ID_nullDerefInScp](./c-cpp-rules.md#nullderefinscp)  
[ID_nullDerefInExp](./c-cpp-rules.md#nullderefinexp)  
<br/>

<br/>
<br/>

### <span id="_56">56. 对象的实际类型与当前静态类型不相关，并调用其非静态成员函数</span>
<br/>

示例：
```
struct A { int foo(); };
struct B { .... };

int bar(void* p) {
    return ((A*)p)->foo();
}

int main() {
    B b;
    return bar(&b);  // Undefined behavior
}
```
例中 A 与 B 是不同且没有继承关系的类，通过不合理的类型转换调用非静态成员函数会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 9.3.1(1)-undefined  
ISO/IEC 14882:2011 9.3.1(2)-undefined  
<br/>

#### 规则
[ID_castNoInheritance](./c-cpp-rules.md#castnoinheritance)  
[ID_forbidMemberVoidPtr](./c-cpp-rules.md#forbidmembervoidptr)  
[ID_forbidFunctionVoidPtr](./c-cpp-rules.md#forbidfunctionvoidptr)  
<br/>

<br/>
<br/>

### <span id="_57">57. 在构造函数或析构函数中调用纯虚函数</span>
<br/>

示例：
```
struct T {
    virtual ~T() {
        release();   // Undefined behavior
    }

    virtual void release() = 0;
};
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 10.4(6)-undefined  
ISO/IEC 14882:2011 10.4(6)-undefined  
<br/>

#### 规则
[ID_virtualCallInConstructor](./c-cpp-rules.md#virtualcallinconstructor)  
[ID_virtualCallInDestructor](./c-cpp-rules.md#virtualcallindestructor)  
<br/>

<br/>
<br/>

### <span id="_58">58. 对象的实际类型与当前静态类型不相关，并调用其析构函数</span>
<br/>

示例：
```
void foo(void* p) {
    delete (T*)p;      // Undefined if delete an object of type other than T
}
```
如果例中 p 指向的不是 T 类型的对象会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 12.4(12)-undefined  
ISO/IEC 14882:2011 12.4(13)-undefined  
<br/>

#### 规则
[ID_forbidFunctionVoidPtr](./c-cpp-rules.md#forbidfunctionvoidptr)  
[ID_forbidMemberVoidPtr](./c-cpp-rules.md#forbidmembervoidptr)  
[ID_castNoInheritance](./c-cpp-rules.md#castnoinheritance)  
<br/>

<br/>
<br/>

### <span id="_59">59. 在对象生命周期结束后调用其析构函数</span>
<br/>

示例：
```
struct T { ~T(); };

void foo() {
    T obj;
    obj.~T();  // End of lifetime
}              // Undefined behavior
```
显式调用 obj 的析构函数后，obj 的生命周期结束，但函数返回前还会调用 obj 的析构函数，导致未定义的行为。  
  
又如：
```
struct B { .... };
struct D: B { .... };

D* p = new D;
p->B::~B();    // End of lifetime of base class object
delete p;      // Undefined behavior
```
调用 p\->B::\~B() 后，基类对象的生命周期结束，但在 delete p 时基类析构函数仍会被执行，导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 12.4(14)-undefined  
ISO/IEC 14882:2011 12.4(15)-undefined  
<br/>

#### 规则
[ID_explicitDtorCall](./c-cpp-rules.md#explicitdtorcall)  
<br/>

<br/>
<br/>

### <span id="_60">60. 基类对象构造完毕之前调用成员函数</span>
<br/>

示例：
```
struct A {
    A(int);
};

struct B: A {
    int i;
    int fun();

    B(): A(fun()),     // Undefined
         i(fun()) {    // Well-defined
    }   
};
```
例中成员函数 fun 的返回值是基类构造函数的参数，但基类尚未开始构造，会导致未定义的行为，用成员函数 fun 初始化成员 i 则没有问题，因为此时基类对象已构造完毕。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 12.6.2(8)-undefined  
ISO/IEC 14882:2011 12.6.2(13)-undefined  
<br/>

#### 规则
[ID_illMemberCall](./c-cpp-rules.md#illmembercall)  
<br/>

<br/>
<br/>

### <span id="_61">61. 对成员或基类对象的不合理引用</span>
<br/>

对具有 non\-trivial 构造函数的对象，在构造函数执行之前引用其非静态成员或基类对象，或者在析构函数执行之后引用其非静态成员或基类对象，会导致未定义的行为。  
  
示例：
```
struct A {              // Trivial
    int a;
};

struct B: A {           // Non-trivial
    B();
    int b;
};

extern B obj;           // The object is defined later
B* p = &obj;            // OK
int* p1 = &obj.a;       // Undefined, refers to base class member
int* p2 = &obj.b;       // Undefined, refers to member

A* pa = &obj;           // Undefined, upcast to a base class type

extern A trvlObj;       // The object is defined later
int* p3 = &trvlObj.a;   // OK, A is a trivial class

B obj;                  // Define the object
A trvlObj;              // Define the object
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 12.7(1)-undefined  
ISO/IEC 14882:2011 12.7(1)-undefined  
<br/>

<br/>
<br/>

### <span id="_62">62. 将对象指针转为其基类对象的指针时，基类对象尚未开始构造或已结束析构</span>
<br/>

示例：
```
struct A {
    ....
};

struct B: A {
    B();
    B(A*);
};

struct C: B {
    C(): B(this) {     // Undefined behavior
    }
};
```
例中将 C 的 this 指针作为基类 B 构造函数的参数，相当于将 C\* 转为 A\*，而这时基类 B 和 A 均未开始构造，会导致未定义的行为。  
  
又如（各项声明接上例）：
```
struct X {
    X(A*);
};

struct D: B, X {
    D(): B(), X(this) {  // OK
    }
};
```
将 D 的 this 指针作为基类 X 构造函数的参数，相当于将 D\* 转为 A\*，这时 B 的构造函数已经执行完毕，所以这种情况没有问题。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 12.7(2)-undefined  
ISO/IEC 14882:2011 12.7(3)-undefined  
<br/>

<br/>
<br/>

### <span id="_63">63. 正在构造或析构的对象通过 . 或 \-> 调用虚函数，而且该对象与当前构造或析构函数不属于同一个类或基类</span>
<br/>

示例：
```
struct V {
    virtual void foo();
    virtual void bar();
};

struct A: virtual V {
    void foo() override;
};

struct B: virtual V {
    B(V*, A*);
    void bar() override;
};

struct C: A, B {
    C(): B((A*)this, this) {
    }
    void foo() override;
    void bar() override;
};

B::B(V* v, A* a) {
    v->bar();       // Well-defined, V is the base of B
    a->foo();       // Undefined behavior, A is not a base of B
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 12.7(3)-undefined  
ISO/IEC 14882:2011 12.7(4)-undefined  
<br/>

<br/>
<br/>

### <span id="_64">64. typeid 作用于正在构造或析构的对象，而且该对象与当前构造或析构函数不属于同一个类或基类</span>
<br/>

示例：
```
struct V {
    virtual void foo();
};

struct A: virtual V {};
struct B: virtual V { B(V*, A*); };

struct C: A, B {
    C(): B((A*)this, this) {}
};

B::B(V* v, A* a) {
    typeid(*v);        // Well-defined, V is the base of B
    typeid(*a);        // Undefined behavior, A is not a base of B
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 12.7(4)-undefined  
ISO/IEC 14882:2011 12.7(5)-undefined  
<br/>

<br/>
<br/>

### <span id="_65">65. dynamic\_cast 作用于正在构造或析构的对象，而且该对象与当前构造或析构函数不属于同一个类或基类</span>
<br/>

示例：
```
struct V {
    virtual void foo();
};

struct A: virtual V {};
struct B: virtual V { B(V*, A*); };

struct C: A, B {
    C(): B((A*)this, this) {}
};

B::B(V* v, A* a) {
    dynamic_cast<B*>(v);    // Well-defined, V is the base of B
    dynamic_cast<B*>(a);    // Undefined behavior, A is not a base of B
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 12.7(5)-undefined  
ISO/IEC 14882:2011 12.7(6)-undefined  
<br/>

<br/>
<br/>

### <span id="_66">66. 对模板函数进行非良构调用，或在模板定义和实例化上下文之外有更好的候选函数匹配</span>
<br/>

示例：
```
namespace N
{
    struct A {};

    template <class T>
    void foo(T&, double) { .... }
}

int main()
{
    N::A a;
    foo(a, 1);
}

namespace N
{
    template <class T>
    void foo(T&, int) { .... }   // Undefined behavior, a better match for ‘foo(a, 1)’
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 14.6.4.2(1)-undefined  
ISO/IEC 14882:2011 14.6.4.2(1)-undefined  
<br/>

<br/>
<br/>

### <span id="_67">67. 需要无限递归的模版实例化</span>
<br/>

示例：
```
template <class T>
class A
{
    A<T>* p;   // OK
    A<T*> a;   // Undefined behavior
};
```
如果实例化 A，需要实例化 A<T\*>，如果实例化 A<T\*>，需要实例化 A<T\*\*>，依次类推，形成了无限递归。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 14.7.1(14)-undefined  
ISO/IEC 14882:2011 14.7.1(15)-undefined  
<br/>

<br/>
<br/>

### <span id="_68">68. 构造或析构函数在 function\-try\-block 的 handler 中访问非静态成员</span>
<br/>

示例：
```
class T {
    int err;

public:
    T() try { .... } catch (...) { log(err); }  // Undefined behavior
   ~T() try { .... } catch (...) { log(err); }  // Undefined behavior
};
```
流程进入 function\-try\-block 的 handler 时，非静态成员的生命周期已结束，不可再被访问。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 15.3(10)-undefined  
ISO/IEC 14882:2011 15.2(10)-undefined  
<br/>

#### 规则
[ID_illMemberAccess](./c-cpp-rules.md#illmemberaccess)  
<br/>

<br/>
<br/>

### <span id="_69">69. 有返回值的函数在 function\-try\-block 的 handler 中没有正确返回</span>
<br/>

示例：
```
int foo() try {    // Function-try-block
    return bar();
}
catch (...) {      // Undefined behavior if there is no return statement
}
```
例中 function\-try\-block 的 handler 没有 return 语句，导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 15.3(16)-undefined  
ISO/IEC 14882:2011 15.3(15)-undefined  
<br/>

#### 规则
[ID_notAllBranchReturn](./c-cpp-rules.md#notallbranchreturn)  
<br/>

<br/>
<br/>

### <span id="_70">70. 在 \#if、 \#elif 的条件中，由宏展开产生了 defined 表达式，或 defined 表达式格式不正确</span>
<br/>

示例：
```
#define DEFINED(x) defined(x)

#if DEFINED(__cplusplus)   // Undefined behavior
....
#endif
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 16.1(4)-undefined  
ISO/IEC 14882:2011 16.1(4)-undefined  
<br/>

#### 规则
[ID_macro_defineReserved](./c-cpp-rules.md#macro_definereserved)  
[ID_illFormedDirective](./c-cpp-rules.md#illformeddirective)  
<br/>

<br/>
<br/>

### <span id="_71">71. \#include 指令经宏展开后不满足标准格式</span>
<br/>

示例：
```
#define STDIO_H <stdio.h>
#define STDLIB_H stdlib.h

#include STDIO_H        // OK
#include STDLIB_H       // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 16.2(4)-undefined  
ISO/IEC 14882:2011 16.2(4)-undefined  
<br/>

#### 规则
[ID_illFormedDirective](./c-cpp-rules.md#illformeddirective)  
<br/>

<br/>
<br/>

### <span id="_72">72. 宏的实参列表中出现预处理指令</span>
<br/>

示例：
```
#define PRINT(s) printf(#s)

PRINT(
#ifdef MAC      // Undefined behavior
    rabbit
#else
    hamster
#endif
);
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 16.3(10)-undefined  
ISO/IEC 14882:2011 16.3(11)-undefined  
<br/>

#### 规则
[ID_directiveInMacroArgument](./c-cpp-rules.md#directiveinmacroargument)  
[ID_macro_function](./c-cpp-rules.md#macro_function)  
<br/>

<br/>
<br/>

### <span id="_73">73. 预处理运算符 \# 的结果不是有效的字符串</span>
<br/>

示例：
```
#define M(x) #x

cout << M();      // Undefined behavior
```
例中宏 M 的参数不足，无法生成有效的字符串，这可能不会通过编译，也可能产生空串，或其他非预期的结果。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 16.3.2(2)-undefined  
ISO/IEC 14882:2011 16.3.2(2)-undefined  
<br/>

#### 规则
[ID_macro_complexConcat](./c-cpp-rules.md#macro_complexconcat)  
<br/>

<br/>
<br/>

### <span id="_74">74. 预处理运算符 \#\# 的结果不是有效的符号</span>
<br/>

示例：
```
#define M(a, b)  a ## b

int M(x, 0) = 0;   // OK, ‘x0’ is a valid token
int M(0, x) = 0;   // Undefined behavior, ‘0x’ is not a valid token
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 16.3.3(3)-undefined  
ISO/IEC 14882:2011 16.3.3(3)-undefined  
<br/>

#### 规则
[ID_macro_complexConcat](./c-cpp-rules.md#macro_complexconcat)  
<br/>

<br/>
<br/>

### <span id="_75">75. \#line 指定的行号为 0 或大于规定值</span>
<br/>

C\+\+03 规定行号不可大于 32767，C\+\+11 规定不可大于 2147483647，否则导致未定义的行为。  
  
示例：
```
#line 0             // Undefined behavior
#line 2147483648    // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 16.4(3)-undefined  
ISO/IEC 14882:2011 16.4(3)-undefined  
<br/>

#### 规则
[ID_illFormedDirective](./c-cpp-rules.md#illformeddirective)  
<br/>

<br/>
<br/>

### <span id="_76">76. \#line 指令不符合标准格式</span>
<br/>

\#line 后只应为整数常量或整数常量和文件名称字符串，其他形式均会导致未定义的行为。  
  
示例：
```
#line "name.cpp"        // Undefined behavior
#line NUM + 1234        // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 16.4(5)-undefined  
ISO/IEC 14882:2011 16.4(5)-undefined  
<br/>

#### 规则
[ID_illFormedDirective](./c-cpp-rules.md#illformeddirective)  
<br/>

<br/>
<br/>

### <span id="_77">77. 用 \#define 定义或用 \#undef 取消定义具有保留意义的名称</span>
<br/>

如 \_\_LINE\_\_、\_\_FILE\_\_、\_\_DATE\_\_、\_\_TIME\_\_、\_\_STDC\_\_、\_\_cplusplus、defined 等名称被定义或取消定义。  
  
示例：
```
#undef __cplusplus   // Undefined behavior
#define __STDC__ 0   // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 16.8(3)-undefined  
ISO/IEC 14882:2011 16.8(4)-undefined  
<br/>

#### 规则
[ID_macro_defineReserved](./c-cpp-rules.md#macro_definereserved)  
[ID_macro_undefReserved](./c-cpp-rules.md#macro_undefreserved)  
<br/>

<br/>
<br/>

### <span id="_78">78. 供语言机制调用的函数不符合要求</span>
<br/>

如 replacement functions 或 handler functions 不符合要求（required behavior），会导致未定义的行为。  
  
示例：
```
struct T {
    void* operator new(size_t) {  // A replacement function
        return nullptr;           // Undefined behavior
    }
};
```
标准要求 operator new 返回非空地址，分配失败则抛出 bad\_alloc 异常（见 ISO/IEC 14882:2011 18.6.1.1），否则导致未定义的行为。  
  
又如：
```
void my_handler() {  // A handler function
    return;          // Do nothing
}

int main() {
    set_terminate(my_handler);   // Undefined behavior
    ....
}
```
标准要求 terminate handler 结束进程的执行，不可返回至调用方（见 ISO/IEC 14882:2011 18.8.3.1），否则导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 17.1.15-undefined  
ISO/IEC 14882:2011 17.3.21-undefined  
<br/>

<br/>
<br/>

### <span id="_79">79. 程序实现了应由标准库提供的功能</span>
<br/>

示例：
```
namespace std
{
    const int& max(const int& a, const int& b) {  // Undefined behavior
        ....
    }
}
```
示例代码实现的 max 会干扰标准库中的 max，导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 17.1.17-undefined  
ISO/IEC 14882:2011 17.3.22-undefined  
<br/>

<br/>
<br/>

### <span id="_80">80. 未经许可，向 std 命名空间添加声明或定义</span>
<br/>

示例：
```
namespace std
{
    using tstring = basic_string<
        TCHAR, char_traits<TCHAR>, allocator<TCHAR>   // Undefined behavior
    >;
}
```
对特殊命名空间的修改是否会生效，以及是否会造成非预期的影响，均是未定义的。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 17.6.4.2.1(1)-undefined  
<br/>

#### 规则
[ID_stdNamespaceModified](./c-cpp-rules.md#stdnamespacemodified)  
<br/>

<br/>
<br/>

### <span id="_81">81. 对标准库，特化模板类成员函数，特化模板类成员模板函数，特化、偏特化成员类模版</span>
<br/>

示例：
```
template <>
void std::vector<int>::push_back(const int&) {  // Undefined behavior
    ....
}
```
示例代码特化了 vector 类的 push\_back 函数，其产生的影响是未定义的。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 17.6.4.2.1(2)-undefined  
<br/>

<br/>
<br/>

### <span id="_82">82. 未经许可，向 posix 命名空间添加声明或定义</span>
<br/>

示例：
```
namespace posix {    // Undefined behavior
    ....
}
```
posix 命名空间是 C\+\+ 的保留命名空间，自定义代码中不应出现名为 posix 的命名空间。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 17.6.4.2.2(1)-undefined  
<br/>

<br/>
<br/>

### <span id="_83">83. 声明或定义标准库保留名称</span>
<br/>

示例：
```
#define override             // Undefined behavior
#define new new(nothrow)     // Undefined behavior
#define string vector<char>  // Undefined behavior
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 17.4.3.1(1)-undefined  
ISO/IEC 14882:2011 17.6.4.3(2)-undefined  
<br/>

#### 规则
[ID_reservedName](./c-cpp-rules.md#reservedname)  
[ID_macro_defineReserved](./c-cpp-rules.md#macro_definereserved)  
<br/>

<br/>
<br/>

### <span id="_84">84. 编译器未提供标准头文件，但编译时引入了非标准同名头文件</span>
<br/>

应选用健全的编译器，并保证编译环境的安全，否则程序的行为是未定义的。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 17.4.3.2(1)-undefined  
ISO/IEC 14882:2011 17.6.4.4(1)-undefined  
<br/>

<br/>
<br/>

### <span id="_85">85. 为标准库函数提供不符合要求的参数</span>
<br/>

<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 17.4.3.7(1)-undefined  
ISO/IEC 14882:2011 17.6.4.9(1)-undefined  
<br/>

<br/>
<br/>

### <span id="_86">86. 多线程调用标准库函数造成数据竞争</span>
<br/>

某些库函数与共享数据相关，但并未提供安全的同步机制，在多线程环境中使用会导致未定义的行为。  
  
示例：
```
void foo() {
    while (true) {
        auto r = rand();   // Undefined behavior
        ....
    }
}

int main() {
    thread t0(foo), t1(foo);
    ....
}
```
多线程并发调用 rand、srand 等函数会造成数据竞争，导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 17.6.4.10(1)-undefined  
<br/>

#### 规则
[ID_dataRaces](./c-cpp-rules.md#dataraces)  
<br/>

<br/>
<br/>

### <span id="_87">87. 违反标准库函数要求的前置条件，除非标准库函数声明了这种情况会抛出异常</span>
<br/>

示例：
```
string s{"abc"};

cout << s[8];       // Undefined behavior
cout << s.at(8);    // Well-defined, throw out_of_range
```
标准规定 string::at 在参数超出范围时抛出 out\_of\_range 异常，而 string::operator \[\] 的参数超出范围会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 17.4.3.8(1)-undefined  
ISO/IEC 14882:2011 17.6.4.11(1)-undefined  
<br/>

<br/>
<br/>

### <span id="_88">88. offsetof 用于非 standard layout 类型，或用于计算静态成员以及成员函数的偏移量</span>
<br/>

示例：
```
struct T {
    int* m;

    T();
    virtual ~T();
};

size_t s = offsetof(T, m);  // Undefined behavior
```
例中 T 不是 standard layout 类型，用 offsetof 求成员 m 的偏移量会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 18.1(5)-undefined  
ISO/IEC 14882:2011 18.2(4)-undefined  
<br/>

#### 规则
[ID_deprecatedOffsetof](./c-cpp-rules.md#deprecatedoffsetof)  
<br/>

<br/>
<br/>

### <span id="_89">89. 可变参数列表中省略号的前一个形式参数为引用、数组、函数，或具有与默认参数提升后不兼容的类型</span>
<br/>

示例：
```
void foo(int& n, ...);      // Undefined behavior
void foo(int f(), ...);     // Undefined behavior
void foo(int a[10], ...);   // Undefined behavior
void foo(char c, ...);      // Undefined behavior
```
例中参数 n、f、a 分别为引用、函数和数组，会导致未定义的行为，c  与默认参数提升后的类型不符，也会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 18.7(3)-undefined  
ISO/IEC 14882:2011 18.10(3)-undefined  
<br/>

#### 规则
[ID_badParmN](./c-cpp-rules.md#badparmn)  
[ID_forbidVariadicFunction](./c-cpp-rules.md#forbidvariadicfunction)  
<br/>

<br/>
<br/>

### <span id="_90">90. longjmp 跳转使应被执行的析构函数未被执行</span>
<br/>

示例：
```
void foo() {
    string obj;
    ....
    longjmp(buf, x);   // Undefined behavior
}
```
例中局部对象 obj 的析构函数不会被执行，由此引发的问题会导致未定义的行为。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 18.7(4)-undefined  
ISO/IEC 14882:2011 18.10(4)-undefined  
<br/>

#### 规则
[ID_forbidLongjmp](./c-cpp-rules.md#forbidlongjmp)  
<br/>

<br/>
<br/>

<br/>
<br/>
