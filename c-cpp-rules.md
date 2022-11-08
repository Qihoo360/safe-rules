<img src="logo.png" align="right"/>

# C/C++ 安全规则集合 ![Version](https://img.shields.io/badge/version-1.2.1-brightgreen)

> Bjarne Stroustrup: “*C makes it easy to shoot yourself in the foot; C++ makes it harder, but when you do it blows your whole leg off.*”

&emsp;&emsp;针对 C、C++ 语言，本文收录了 441 种需要重点关注的问题，可为制定编程规范提供依据，也可为代码审计以及相关培训提供指导意见，适用于桌面、服务端以及嵌入式等软件系统。  
&emsp;&emsp;每个问题对应一条规则，每条规则可直接作为规范条款或审计检查点，本文是适用于不同应用场景的规则集合，读者可根据自身需求从中选取某个子集作为规范或审计依据，从而提高软件产品的安全性。
<br/>

## 规则说明

规则按如下主题分为 17 个类别：

 1. [Security](#__Security)：敏感信息防护
 2. [Resource](#__Resource)：资源管理
 3. [Precompile](#__Precompile)：预处理、宏、注释
 4. [Global](#__Global)：全局及命名空间作用域
 5. [Type](#__Type)：类型设计与实现
 6. [Declaration](#__Declaration)：声明
 7. [Exception](#__Exception)：异常
 8. [Function](#__Function)：函数实现
 9. [Control](#__Control)：流程控制
 10. [Expression](#__Expression)：表达式
 11. [Literal](#__Literal)：常量
 12. [Cast](#__Cast)：类型转换
 13. [Buffer](#__Buffer)：缓冲区
 14. [Pointer](#__Pointer)：指针
 15. [Interruption](#__Interruption)：中断与信号处理
 16. [Concurrency](#__Concurrency)：异步与并发
 17. [Style](#__Style)：样式与风格

每条规则包括：

 - 编号：规则在本文中的章节编号，以“R”开头，称为 Section-ID
 - 名称：用简练的短语描述违反规则的状况，以“ID_”开头，称为 Fault-ID
 - 标题：规则的定义
 - 说明：规则设立的原因、示例、违反规则的后果、改进建议、参照依据、参考资料等内容

当代码出现违反规则的情况时，可分为：

 - Error：可直接导致错误的问题
 - Warning：可导致错误或存在隐患的问题
 - Suspicious：代码的可疑形式
 - Suggestion：对提高代码质量的建议

规则的说明包含：

 - 示例：规则相关的示例代码，指明符合规则（Compliant）的和违反规则（Non-compliant）的情况
 - 相关：与当前规则有相关性的规则，可作为扩展阅读的线索
 - 依据：规则依照的 ISO/IEC 标准，C 规则以 ISO/IEC 9899:2011 为主，C++ 规则以 ISO/IEC 14882:2011 为主
 - 配置：某些规则的对象可由用户指定，审计工具可以此为参照实现定制化功能
 - 参考：规则参考的其他规范条款，如 C++ Core Guidelines、MISRA、CWE、SEI CERT 等，也可作为扩展阅读的线索

规则的相关性分为：

 - 特化：设规则 A 的特殊情况需要由规则 B 阐明，称规则 B 是规则 A 的特化
 - 泛化：与特化相反，称规则 A 是规则 B 的泛化
 - 相交：设两个规则针对不同的问题，但在内容上有一定的交集，称这两个规则相交

规则以“``标准名称:版本 章节编号(段落编号)-性质``”的格式引用标准，如“``ISO/IEC 14882:2011 5.6(4)-undefined``”，表示引用 C++11 标准的第 5 章第 6 节第 4 段说明的具有 undefined 性质的问题。  

其中“性质”分为：

 - undefined：可使程序产生未定义的行为，这种行为造成的后果是不可预期的
 - unspecified：可使程序产生未声明的行为，这种行为由编译器或环境定义，具有随意性
 - implementation：可使程序产生由实现定义的行为，这种行为由编译器或环境定义，有明确的文档支持
 - deprecated：已被废弃的或不建议继续使用的编程方式

本文以 ISO/IEC 9899:2011、ISO/IEC 14882:2011 为主要依据，兼顾 C18、C++17 以及历史标准，没有特殊说明的规则同时适用于 C 语言和 C++ 语言，只适用于某一种语言的规则会另有说明。

## 规则选取
本文是适用于不同应用场景的规则集合，读者可选取适合自己需求的规则。

指出某种错误的规则，如有“不可”、“不应”等字样的规则应尽量被选取，有“禁用”等字样的规则可能只适用于某一场景，可酌情选取。

如果将本文作为培训内容，为了全面理解各种场景下存在的问题，应选取全部规则。

## 规则列表
<span id="__Security">**[1. Security](#security)**</span>
  - [R1.1 敏感数据不可写入代码](#ID_plainSensitiveInfo)
  - [R1.2 敏感数据不可被系统外界感知](#ID_secretLeak)
  - [R1.3 敏感数据在使用后应被有效清理](#ID_unsafeCleanup)
  - [R1.4 公共成员或全局对象不应记录敏感数据](#ID_sensitiveName)
  - [R1.5 预判用户输入造成的不良后果](#ID_hijack)
  - [R1.6 对文件设定合理的访问权限](#ID_unlimitedAuthority)
  - [R1.7 落实对用户的权限管理](#ID_improperAuthorization)
  - [R1.8 不应引用危险符号名称](#ID_dangerousName)
  - [R1.9 避免使用具有危险性的函数](#ID_dangerousFunction)
  - [R1.10 不应使用已过时的函数](#ID_obsoleteFunction)
  - [R1.11 禁用不安全的字符串函数](#ID_unsafeStringFunction)
  - [R1.12 确保字符串以空字符结尾](#ID_improperNullTermination)
  - [R1.13 避免除 0 等计算异常](#ID_divideByZero)
  - [R1.14 格式化字符串应为常量](#ID_variableFormatString)
  - [R1.15 与内存空间布局相关的信息不可被外界感知](#ID_addressExposure)
  - [R1.16 与网络地址相关的信息不应写入代码](#ID_hardcodedIP)
  - [R1.17 选择安全的异常处理方式](#ID_deprecatedErrno)
  - [R1.18 启用平台和编译器提供的防御机制](#ID_missingHardening)
<br/>

<span id="__Resource">**[2. Resource](#resource)**</span>
  - [R2.1 不可失去对已分配资源的控制](#ID_resourceLeak)
  - [R2.2 不可失去对已分配内存的控制](#ID_memoryLeak)
  - [R2.3 不可访问未初始化或已释放的资源](#ID_illAccess)
  - [R2.4 资源应接受对象化管理](#ID_ownerlessResource)
  - [R2.5 资源的分配与回收方法应成对提供](#ID_incompleteNewDeletePair)
  - [R2.6 资源的分配与回收方法应配套使用](#ID_incompatibleDealloc)
  - [R2.7 模块之间不应传递容器等对象](#ID_crossModuleTransfer)
  - [R2.8 对象申请的资源应在析构函数中释放](#ID_memberDeallocation)
  - [R2.9 对象被移动后不应再被使用](#ID_useAfterMove)
  - [R2.10 构造函数抛出异常需避免相关资源泄漏](#ID_throwInConstructor)
  - [R2.11 资源不可被重复释放](#ID_doubleFree)
  - [R2.12 用 delete 释放对象需保证其类型完整](#ID_deleteIncompleteType)
  - [R2.13 用 delete 释放对象不可多写中括号](#ID_excessiveDelete)
  - [R2.14 用 delete 释放数组不可漏写中括号](#ID_insufficientDelete)
  - [R2.15 非动态申请的资源不可被释放](#ID_illDealloc)
  - [R2.16 在一个表达式语句中最多使用一次 new](#ID_multiAllocation)
  - [R2.17 流式资源对象不应被复制](#ID_copiedStream)
  - [R2.18 避免使用在栈上分配内存的函数](#ID_stackAllocation)
  - [R2.19 避免不必要的内存分配](#ID_unnecessaryAllocation)
  - [R2.20 避免动态内存分配](#ID_dynamicAllocation)
  - [R2.21 判断资源分配函数的返回值是否有效](#ID_nullDerefAllocRet)
  - [R2.22 C\+\+ 代码中禁用 C 内存管理函数](#ID_forbidMallocAndFree)
<br/>

<span id="__Precompile">**[3. Precompile](#precompile)**</span>
  - [3.1 Include](#precompile.include)
    - [R3.1.1 include 指令应符合标准格式](#ID_illFormedInclude)
    - [R3.1.2 include 指令中禁用不合规的字符](#ID_nonStandardCharInHeaderName)
    - [R3.1.3 include 指令中不应使用反斜杠](#ID_forbidBackslashInHeaderName)
    - [R3.1.4 include 指令中不应使用绝对路径](#ID_forbidAbsPathInHeaderName)
    - [R3.1.5 禁用不合规的头文件](#ID_forbiddenHeader)
    - [R3.1.6 C\+\+ 代码不应引用 C 头文件](#ID_forbidCHeaderInCpp)
  - [3.2 Macro-definition](#precompile.macro-definition)
    - [R3.2.1 宏应遵循合理的命名方式](#ID_macro_badName)
    - [R3.2.2 不可定义具有保留意义的宏名称](#ID_macro_defineReserved)
    - [R3.2.3 不可取消定义具有保留意义的宏名称](#ID_macro_undefReserved)
    - [R3.2.4 可作为子表达式的宏定义应该用括号括起来](#ID_macro_expNotEnclosed)
    - [R3.2.5 与运算符相关的宏参数应该用括号括起来](#ID_macro_paramNotEnclosed)
    - [R3.2.6 由多个语句组成的宏定义应该用 do\-while(0) 括起来](#ID_macro_stmtNotEnclosed)
    - [R3.2.7 在宏定义中由 \# 修饰的参数后不应出现 \#\#](#ID_macro_complexConcat)
    - [R3.2.8 宏参数数量应在规定范围之内](#ID_macro_tooManyParams)
    - [R3.2.9 不应使用宏定义常量](#ID_macro_const)
    - [R3.2.10 不应使用宏定义类型](#ID_macro_typeid)
    - [R3.2.11 可由函数实现的功能不应使用宏实现](#ID_macro_function)
    - [R3.2.12 宏名称中不应存在拼写错误](#ID_macro_misspelling)
  - [3.3 Macro-usage](#precompile.macro-usage)
    - [R3.3.1 宏参数不应有副作用](#ID_macro_sideEffectArgs)
    - [R3.3.2 宏的实参个数不可小于形参个数](#ID_macro_insufficientArgs)
    - [R3.3.3 宏的实参个数不可大于形参个数](#ID_macro_redundantArgs)
    - [R3.3.4 va\_start 或 va\_copy 应配合 va\_end 使用](#ID_incompleteVaMacros)
    - [R3.3.5 va\_arg 的类型参数应符合要求](#ID_badVaArgType)
    - [R3.3.6 在 C\+\+ 代码中不应使用宏 offsetof](#ID_deprecatedOffsetof)
  - [3.4 Directive](#precompile.directive)
    - [R3.4.1 头文件不应缺少守卫](#ID_missingHeaderGuard)
    - [R3.4.2 不应出现非标准格式的预编译指令](#ID_illFormedDirective)
    - [R3.4.3 不应使用非标准预编译指令](#ID_nonStdDirective)
    - [R3.4.4 宏的参数列表中不应出现预编译指令](#ID_directiveInMacroArgument)
    - [R3.4.5 对编译警告的屏蔽应慎重](#ID_warningDisabled)
    - [R3.4.6 在高级别的警告设置下编译](#ID_warningDefault)
  - [3.5 Comment](#precompile.comment)
    - [R3.5.1 关注 TODO、FIXME、XXX、BUG 等特殊注释](#ID_specialComment)
    - [R3.5.2 注释不可嵌套](#ID_nestedComment)
    - [R3.5.3 注释应出现在合理的位置](#ID_badCommentPosition)
  - [3.6 Other](#precompile.other)
    - [R3.6.1 非空源文件应以换行符结尾](#ID_missingNewLineFileEnd)
    - [R3.6.2 除转义字符、宏定义之外不应使用反斜杠](#ID_badBackslash)
<br/>

<span id="__Global">**[4. Global](#global)**</span>
  - [R4.1 全局名称应遵循合理的命名方式](#ID_nameTooShort)
  - [R4.2 为代码设定合理的命名空间](#ID_missingNamespace)
  - [R4.3 main 函数只应处于全局作用域中](#ID_nonGlobalMain)
  - [R4.4 头文件中不应使用 using directive](#ID_usingNamespaceInHeader)
  - [R4.5 头文件中不应使用静态声明](#ID_staticInHeader)
  - [R4.6 头文件中不应定义匿名命名空间](#ID_anonymousNamespaceInHeader)
  - [R4.7 匿名命名空间中不应使用静态声明](#ID_staticInAnonymousNamespace)
  - [R4.8 全局对象的初始化不可依赖未初始化的对象](#ID_relyOnExternalObject)
  - [R4.9 全局对象只应为常量或静态对象](#ID_nonConstNonStaticGlobalObject)
  - [R4.10 全局对象只应为常量](#ID_nonConstGlobalObject)
  - [R4.11 全局对象不应同时被 static 和 const 关键字修饰](#ID_staticAndConst)
  - [R4.12 全局或命名空间作用域中禁用 using directive](#ID_forbidUsingDirectives)
  - [R4.13 避免无效的 using directive](#ID_usingSelf)
  - [R4.14 不应定义全局 inline 命名空间](#ID_topInlineNamespace)
  - [R4.15 不可修改 std 命名空间](#ID_stdNamespaceModified)
<br/>

<span id="__Type">**[5. Type](#type)**</span>
  - [5.1 Class](#type.class)
    - [R5.1.1 类的非常量数据成员均应为 private](#ID_nonPrivateData)
    - [R5.1.2 类的非常量数据成员不应定义为 protected](#ID_protectedData)
    - [R5.1.3 类不应既有 public 数据成员又有 private 数据成员](#ID_mixPublicPrivateData)
    - [R5.1.4 有虚函数的基类应具有虚析构函数](#ID_missingVirtualDestructor)
    - [R5.1.5 用虚基类避免冗余的基类实例](#ID_diamondInheritance)
    - [R5.1.6 存在赋值运算符或析构函数时，不应缺少拷贝构造函数](#ID_missingCopyConstructor)
    - [R5.1.7 存在拷贝构造函数或析构函数时，不应缺少拷贝赋值运算符](#ID_missingCopyAssignOperator)
    - [R5.1.8 存在拷贝构造函数或赋值运算符时，不应缺少析构函数](#ID_missingDestructor)
    - [R5.1.9 存在移动构造函数时，不应缺少移动赋值运算符](#ID_missingMoveAssignOperator)
    - [R5.1.10 存在移动赋值运算符时，不应缺少移动构造函数](#ID_missingMoveConstructor)
    - [R5.1.11 可接受一个参数的构造函数需用 explicit 关键字限定](#ID_missingExplicitConstructor)
    - [R5.1.12 重载的类型转换运算符需用 explicit 关键字限定](#ID_missingExplicitConvertor)
    - [R5.1.13 不应过度使用 explicit 关键字](#ID_excessiveExplicit)
    - [R5.1.14 带模板的赋值运算符不应覆盖拷贝或移动赋值运算符](#ID_roughTemplateAssignOperator)
    - [R5.1.15 带模板的构造函数不应覆盖拷贝或移动构造函数](#ID_roughTemplateConstructor)
    - [R5.1.16 抽象类禁用拷贝赋值运算符](#ID_unsuitableCopyAssignOperator)
    - [R5.1.17 数据成员的数量应在规定范围之内](#ID_tooManyFields)
    - [R5.1.18 数据成员之间的填充数据不应被忽视](#ID_ignorePaddingData)
    - [R5.1.19 存在构造、析构或虚函数的类不应采用 struct 关键字](#ID_unsuitableStructTag)
  - [5.2 Enum](#type.enum)
    - [R5.2.1 同类枚举项的值不应相同](#ID_duplicateEnumerator)
    - [R5.2.2 合理初始化各枚举项](#ID_casualInitialization)
    - [R5.2.3 不应使用匿名枚举声明](#ID_forbidUnnamedEnum)
    - [R5.2.4 用 enum class 取代 enum](#ID_forbidUnscopedEnum)
  - [5.3 Union](#type.union)
    - [R5.3.1 联合体内禁用非基本类型的对象](#ID_forbidNonBasicField)
    - [R5.3.2 禁用在类之外定义的联合体](#ID_forbidNakedUnion)
    - [R5.3.3 禁用联合体](#ID_forbidUnion)
<br/>

<span id="__Declaration">**[6. Declaration](#declaration)**</span>
  - [6.1 Naming](#declaration.naming)
    - [R6.1.1 遵循合理的命名方式](#ID_badName)
    - [R6.1.2 不应定义具有保留意义的名称](#ID_reservedName)
    - [R6.1.3 局部名称不应被覆盖](#ID_hideLocal)
    - [R6.1.4 成员名称不应被覆盖](#ID_hideMember)
    - [R6.1.5 类型名称不应与对象或函数名称相同](#ID_duplicatedName)
    - [R6.1.6 不应存在拼写错误](#ID_misspelling)
  - [6.2 Qualifier](#declaration.qualifier)
    - [R6.2.1 const、volatile 不应重复](#ID_qualifierRepeated)
    - [R6.2.2 const、volatile 修饰指针类型的别名是可疑的](#ID_qualifierForPtrAlias)
    - [R6.2.3 const、volatile 不可修饰引用](#ID_qualifierInvalid)
    - [R6.2.4 const、volatile 限定类型时应出现在左侧](#ID_badQualifierPosition)
    - [R6.2.5 const、volatile 等关键字不应出现在基本类型名称的中间](#ID_sandwichedModifier)
    - [R6.2.6 避免用常量字符串对非常量字符串指针赋值](#ID_missingConst)
    - [R6.2.7 枚举类型的底层类型不应为 const 或 volatile](#ID_uselessQualifier)
    - [R6.2.8 对常量的定义不应为引用](#ID_constLiteralReference)
    - [R6.2.9 禁用 restrict 指针](#ID_forbidRestrictPtr)
    - [R6.2.10 非适当场景禁用 volatile](#ID_forbidVolatile)
  - [6.3 Specifier](#declaration.specifier)
    - [R6.3.1 使用 auto 关键字需注意可读性](#ID_abusedAuto)
    - [R6.3.2 不应使用已过时的关键字](#ID_deprecatedSpecifier)
    - [R6.3.3 不应使用多余的 inline 关键字](#ID_inlineRedundant)
    - [R6.3.4 extern 关键字不应作用于类成员的声明或定义](#ID_invalidExternSpecifier)
    - [R6.3.5 重写的虚函数应声明为 override 或 final](#ID_missingExplicitOverride)
    - [R6.3.6 override 和 final 关键字不应同时出现](#ID_redundantOverride)
    - [R6.3.7 有 override 或 final 关键字时，不应再出现 virtual 关键字](#ID_redundantVirtual)
    - [R6.3.8 不应将 union 设为 final](#ID_invalidFinal)
    - [R6.3.9 inline、virtual、static、typedef 等关键字应出现在类型名的左侧](#ID_badSpecifierPosition)
  - [6.4 Declarator](#declaration.declarator)
    - [R6.4.1 用 auto 声明指针或引用时应显式标明 \*、& 等符号](#ID_roughAuto)
    - [R6.4.2 禁用可变参数列表](#ID_forbidVariadicFunction)
    - [R6.4.3 禁用柔性数组](#ID_forbidFlexibleArray)
    - [R6.4.4 接口的参数或返回值不应被声明为 void\*](#ID_forbidFunctionVoidPtr)
    - [R6.4.5 类成员不应被声明为 void\*](#ID_forbidMemberVoidPtr)
    - [R6.4.6 局部数组的长度不应过大](#ID_unsuitableArraySize)
    - [R6.4.7 不建议将类型定义和对象声明写在一个语句中](#ID_mixedTypeObjDefinition)
    - [R6.4.8 不应将函数或函数指针和其他声明写在同一个语句中](#ID_mixedDeclarations)
    - [R6.4.9 在一个语句中不应声明过多对象或函数](#ID_tooManyDeclarators)
  - [6.5 Object](#declaration.object)
    - [R6.5.1 不应产生无效的临时对象](#ID_inaccessibleTmpObject)
    - [R6.5.2 不应存在没有被用到的局部声明](#ID_invalidLocalDeclaration)
    - [R6.5.3 对象初始化不可依赖自身的值](#ID_selfDependentInitialization)
    - [R6.5.4 参与数值运算的 char 变量需显式声明 signed 或 unsigned](#ID_plainNumericChar)
    - [R6.5.5 字节的类型应为 unsigned char](#ID_plainBinaryChar)
  - [6.6 Parameter](#declaration.parameter)
    - [R6.6.1 函数原型声明中的参数应具有合理的名称](#ID_missingParamName)
    - [R6.6.2 不应将数组作为函数的形式参数](#ID_invalidParamArraySize)
    - [R6.6.3 parmN 的声明应符合要求](#ID_badParmN)
    - [R6.6.4 虚函数参数的默认值应与基类中声明的一致](#ID_inconsistentDefaultArgument)
    - [R6.6.5 不建议虚函数的参数有默认值](#ID_deprecatedDefaultArgument)
    - [R6.6.6 C 代码中参数列表如果为空应声明为“(void)”](#ID_missingVoid)
    - [R6.6.7 C\+\+ 代码中参数列表如果为空不应声明为“(void)”](#ID_superfluousVoid)
    - [R6.6.8 声明数组参数的大小时禁用 static 关键字](#ID_forbidStaticArrSize)
  - [6.7 Function](#declaration.function)
    - [R6.7.1 派生类不应重新定义与基类相同的非虚函数](#ID_nonVirtualOverride)
    - [R6.7.2 拷贝赋值、移动赋值运算符应返回所属类的非 const 引用](#ID_nonStdAssignmentRetType)
    - [R6.7.3 拷贝赋值运算符的参数应为同类对象的 const 左值引用](#ID_nonStdCopyAssignmentParam)
    - [R6.7.4 移动赋值运算符的参数应为同类对象的非 const 右值引用](#ID_nonStdMoveAssignmentParam)
    - [R6.7.5 不应重载取地址运算符](#ID_overloadAddressOperator)
    - [R6.7.6 不应重载逗号运算符](#ID_overloadComma)
    - [R6.7.7 不应重载“逻辑与”和“逻辑或”运算符](#ID_overloadLogicOperator)
    - [R6.7.8 拷贝赋值、移动赋值运算符不应为虚函数](#ID_virtualAssignment)
    - [R6.7.9 比较运算符不应为虚函数](#ID_virtualComparison)
    - [R6.7.10 final 类中不应声明虚函数](#ID_virtualInFinal)
  - [6.8 Bitfield](#declaration.bitfield)
    - [R6.8.1 位域长度不应超过类型约定的大小](#ID_exceededBitfield)
    - [R6.8.2 有符号变量的位域长度不应为 1](#ID_singleSignedBitfield)
    - [R6.8.3 不应对枚举变量声明位域](#ID_forbidEnumBitfield)
    - [R6.8.4 禁用位域](#ID_forbidBitfield)
  - [6.9 Complexity](#declaration.complexity)
    - [R6.9.1 不建议采用复杂的声明](#ID_complexDeclaration)
  - [6.10 Other](#declaration.other)
    - [R6.10.1 不应违反 One Definition Rule](#ID_violateODR)
    - [R6.10.2 不应存在没有被用到的标签](#ID_labelNotUsed)
    - [R6.10.3 不应存在没有被用到的静态声明](#ID_staticNotUsed)
    - [R6.10.4 不应存在没有被用到的 private 成员](#ID_privateNotUsed)
    - [R6.10.5 避免使用 std::auto\_ptr](#ID_deprecatedAutoPtr)
<br/>

<span id="__Exception">**[7. Exception](#exception)**</span>
  - [R7.1 保证异常安全](#ID_exceptionUnsafe)
  - [R7.2 不应抛出过于宽泛的异常](#ID_throwGenericException)
  - [R7.3 不应捕获过于宽泛的异常](#ID_catch_generic)
  - [R7.4 不应抛出非异常类型的对象](#ID_throwNonExceptionType)
  - [R7.5 不应捕获非异常类型的对象](#ID_catch_nonExceptionType)
  - [R7.6 析构函数不可抛出异常](#ID_throwInDestuctor)
  - [R7.7 内存回收函数不可抛出异常](#ID_throwInDelete)
  - [R7.8 对象的 swap 过程不可抛出异常](#ID_throwInSwap)
  - [R7.9 移动构造函数和移动赋值运算符不可抛出异常](#ID_throwInMove)
  - [R7.10 异常类的构造函数和异常信息相关的函数不应抛出异常](#ID_exceptionInException)
  - [R7.11 与标准库相关的 hash 过程不应抛出异常](#ID_throwInHash)
  - [R7.12 通过引用捕获异常](#ID_catch_value)
  - [R7.13 捕获异常时不应产生对象切片问题](#ID_catch_slicing)
  - [R7.14 捕获异常后不应直接再次抛出异常](#ID_catch_justRethrow)
  - [R7.15 重新抛出异常时应使用空 throw 表达式（throw;）](#ID_improperRethrow)
  - [R7.16 不应在 catch handler 外使用空 throw 表达式（throw;）](#ID_rethrowOutOfCatch)
  - [R7.17 不应抛出指针](#ID_throwPointer)
  - [R7.18 不应抛出 NULL](#ID_throwNULL)
  - [R7.19 不应抛出 nullptr](#ID_throwNullptr)
  - [R7.20 禁用含 throw 关键字的异常规格说明](#ID_forbidThrowSpecification)
  - [R7.21 禁用 C\+\+ 异常](#ID_forbidException)
<br/>

<span id="__Function">**[8. Function](#function)**</span>
  - [R8.1 main 函数的返回类型只应为 int](#ID_mainReturnsNonInt)
  - [R8.2 main 函数不应被重载，也不应声明为 inline、static 或 constexpr](#ID_illFormedMain)
  - [R8.3 函数不应在头文件中实现](#ID_definedInHeader)
  - [R8.4 函数的参数名称在声明和实现处应保持一致](#ID_inconsistentParamName)
  - [R8.5 多态类的对象作为参数时不应采用值传递的方式](#ID_paramMayBeSlicing)
  - [R8.6 不应存在未被使用的具名形式参数](#ID_paramNotUsed)
  - [R8.7 由 const 修饰的参数应为引用或指针](#ID_paramPassedByValue)
  - [R8.8 转发引用只应作为 std::forward 的参数](#ID_illForwardingReference)
  - [R8.9 局部对象在使用前必须初始化](#ID_localInitialization)
  - [R8.10 成员须在声明处或构造时初始化](#ID_memberInitialization)
  - [R8.11 基类对象构造完毕之前不可调用成员函数](#ID_illMemberCall)
  - [R8.12 在面向构造或析构函数体的 catch handler 中不可访问非静态成员](#ID_illMemberAccess)
  - [R8.13 成员初始化应遵循声明的顺序](#ID_disorderedInitialization)
  - [R8.14 在构造函数中不应调用虚函数](#ID_virtualCallInConstructor)
  - [R8.15 在析构函数中不应调用虚函数](#ID_virtualCallInDestuctor)
  - [R8.16 在析构函数中避免调用 exit 函数](#ID_exitCallInDestructor)
  - [R8.17 拷贝构造函数应避免实现复制之外的功能](#ID_sideEffectCopyConstructor)
  - [R8.18 拷贝赋值运算符应处理参数是自身对象时的情况](#ID_this_selfJudgement)
  - [R8.19 不应存在无效的写入操作](#ID_invalidWrite)
  - [R8.20 不应存在没有副作用的语句](#ID_missingSideEffect)
  - [R8.21 不应存在得不到执行机会的代码](#ID_unreachableCode)
  - [R8.22 有返回值的函数其所有分枝都应有明确的返回值](#ID_notAllBranchReturn)
  - [R8.23 不可返回局部对象的地址或引用](#ID_localAddressFlowOut)
  - [R8.24 合理设置 lambda 表达式的捕获方式](#ID_unsuitableCapture)
  - [R8.25 函数返回值不应为右值引用](#ID_returnRValueReference)
  - [R8.26 函数返回值不应为常量对象](#ID_returnConstObject)
  - [R8.27 函数返回值不应为基本类型的常量](#ID_returnSuperfluousConst)
  - [R8.28 被返回的表达式应与函数的返回类型相符](#ID_returnOdd)
  - [R8.29 被返回的表达式不应为相同的常量](#ID_returnSameConst)
  - [R8.30 具有 noreturn 属性的函数不应返回](#ID_unsuitableReturn)
  - [R8.31 具有 noreturn 属性的函数返回类型只应为 void](#ID_unsuitableReturnType)
  - [R8.32 由 atexit、at\_quick\_exit 指定的处理函数应正常返回](#ID_exitHandlerNoReturn)
  - [R8.33 函数模板不应被特化](#ID_functionSpecialization)
  - [R8.34 函数的标签数量应在规定范围之内](#ID_tooManyLabels)
  - [R8.35 函数的行数应在规定范围之内](#ID_tooManyLines)
  - [R8.36 lambda 表达式的行数应在规定范围之内](#ID_tooManyLambdaLines)
  - [R8.37 函数参数的数量应在规定范围之内](#ID_tooManyParams)
  - [R8.38 不应定义过于复杂的内联函数](#ID_complexInlineFunction)
  - [R8.39 避免递归实现](#ID_recursion)
  - [R8.40 避免重复的函数实现](#ID_functionRepetition)
<br/>

<span id="__Control">**[9. Control](#control)**</span>
  - [9.1 If](#control.if)
    - [R9.1.1 if 语句不应被分号隔断](#ID_if_semicolon)
    - [R9.1.2 在 if...else\-if 分枝中不应有重复的条件](#ID_if_identicalCondition)
    - [R9.1.3 在 if...else\-if 分枝中不应有被遮盖的条件](#ID_if_hiddenCondition)
    - [R9.1.4 if 分枝和 else 分枝的代码不应完全相同](#ID_if_identicalBlock)
    - [R9.1.5 if...else\-if 各分枝的代码不应完全相同](#ID_if_identicalElseIfBlock)
    - [R9.1.6 if 分枝和隐含的 else 分枝代码不应完全相同](#ID_if_identicalImplicitElseBlock)
    - [R9.1.7 没有 else 子句的 if 语句与其后续代码相同是可疑的](#ID_if_identicalSucceedingBlock)
    - [R9.1.8 if 分枝和 else 分枝的起止语句不应相同](#ID_if_commonStatements)
    - [R9.1.9 if 语句作用域的范围不应有误](#ID_if_scope)
    - [R9.1.10 如果 if 关键字前面是右大括号，if 关键字应另起一行](#ID_if_mayBeElseIf)
    - [R9.1.11 if 语句的条件不应为赋值表达式](#ID_if_assignment)
    - [R9.1.12 if 语句不应为空](#ID_if_emptyBlock)
    - [R9.1.13 if...else\-if 分枝数量应在规定范围之内](#ID_if_tooManyElseIf)
    - [R9.1.14 if 分枝中的语句应该用大括号括起来](#ID_if_brace)
    - [R9.1.15 所有 if...else\-if 分枝都应以 else 子句结束](#ID_if_missingEndingElse)
  - [9.2 For](#control.for)
    - [R9.2.1 for 语句不应被分号隔断](#ID_for_semicolon)
    - [R9.2.2 for 语句中不应存在无条件的跳转语句](#ID_for_uncondBroken)
    - [R9.2.3 for 语句作用域的范围不应有误](#ID_for_scope)
    - [R9.2.4 for 语句没有明显的循环变量时应改用 while 句语](#ID_for_simplification)
    - [R9.2.5 for 循环体不应为空](#ID_for_emptyBlock)
    - [R9.2.6 for 循环变量不应为浮点型](#ID_for_floatCounter)
    - [R9.2.7 for 循环变量不应在循环体内被改变](#ID_for_counterChangedInBody)
    - [R9.2.8 嵌套的 for 语句不应使用相同的循环变量](#ID_for_counterNested)
    - [R9.2.9 for 循环体应该用大括号括起来](#ID_for_brace)
  - [9.3 While](#control.while)
    - [R9.3.1 while 语句不应被分号隔断](#ID_while_semicolon)
    - [R9.3.2 while 语句中不应存在无条件的跳转语句](#ID_while_uncondBroken)
    - [R9.3.3 while 语句的条件不应为赋值表达式](#ID_while_assignment)
    - [R9.3.4 while 语句作用域的范围不应有误](#ID_while_scope)
    - [R9.3.5 while 循环体不应为空](#ID_while_emptyBlock)
    - [R9.3.6 while 循环体应该用大括号括起来](#ID_while_brace)
  - [9.4 Do](#control.do)
    - [R9.4.1 注意 do\-while(false) 中可疑的 continue 语句](#ID_do_suspiciousContinue)
    - [R9.4.2 do\-while 循环体不应为空](#ID_do_emptyBlock)
    - [R9.4.3 do\-while 循环体应该用大括号括起来](#ID_do_brace)
    - [R9.4.4 不建议使用 do 语句](#ID_do_deprecated)
  - [9.5 Switch](#control.switch)
    - [R9.5.1 switch 语句不应被分号隔断](#ID_switch_semicolon)
    - [R9.5.2 switch 语句不应为空](#ID_switch_emptyBlock)
    - [R9.5.3 case 常量的范围不可超出 switch 变量的范围](#ID_switch_caseOutOfRange)
    - [R9.5.4 switch 语句中任何子句都应从属于某个 case 或 default 分枝](#ID_switch_invalidStatement)
    - [R9.5.5 case 和 default 标签应直接从属于 switch 语句](#ID_switch_badFormedCase)
    - [R9.5.6 不应存在紧邻 default 标签的空 case 标签](#ID_switch_uselessFallThrough)
    - [R9.5.7 不应存在内容完全相同的 case 分枝](#ID_switch_identicalBranch)
    - [R9.5.8 switch 语句的条件不应为 bool 型](#ID_switch_bool)
    - [R9.5.9 switch 语句不应只包含 default 标签](#ID_switch_onlyDefault)
    - [R9.5.10 switch 语句不应只包含一个 case 标签](#ID_switch_onlyOneCase)
    - [R9.5.11 switch 语句分枝数量应在规定范围之内](#ID_switch_tooManyCases)
    - [R9.5.12 switch 语句应配有 default 分枝](#ID_switch_missingDefault)
    - [R9.5.13 switch 语句的每个非空分枝都应该用无条件的 break 语句终止](#ID_switch_breakOmitted)
    - [R9.5.14 switch 语句应该用大括号括起来](#ID_switch_brace)
    - [R9.5.15 switch 语句不应嵌套](#ID_switch_forbidNest)
  - [9.6 Try-catch](#control.try-catch)
    - [R9.6.1 不应存在空的 try 块](#ID_try_emptyBlock)
    - [R9.6.2 不应存在空的 catch handler](#ID_catch_emptyBlock)
    - [R9.6.3 不应嵌套 try\-catch 语句](#ID_try_forbidNest)
    - [R9.6.4 捕获所有异常的 catch\-all handler 应位于最后](#ID_try_disorderedEllipsis)
    - [R9.6.5 派生类的 catch handler 应排在基类 catch handler 之前](#ID_try_disorderedHandlers)
  - [9.7 Jump](#control.jump)
    - [R9.7.1 禁止 goto 语句向嵌套的或无包含关系的作用域跳转](#ID_forbidGotoBlocks)
    - [R9.7.2 禁止 goto 语句向前跳转](#ID_forbidGotoBack)
    - [R9.7.3 禁用 goto 语句](#ID_forbidGoto)
    - [R9.7.4 禁用 setjmp、longjmp](#ID_forbidLongjmp)
    - [R9.7.5 不应出现多余的跳转语句](#ID_redundantJump)
<br/>

<span id="__Expression">**[10. Expression](#expression)**</span>
  - [10.1 Logic](#expression.logic)
    - [R10.1.1 不应出现不合逻辑的重复子表达式](#ID_illIdentical)
    - [R10.1.2 各逻辑子表达式不应自相矛盾](#ID_conflictCondition)
    - [R10.1.3 条件表达式不应恒为真或恒为假](#ID_invalidCondition)
    - [R10.1.4 不应使用多余的逻辑子表达式](#ID_redundantCondition)
    - [R10.1.5 逻辑表达式及其子表达式的结果不应为常量](#ID_constLogicExpression)
    - [R10.1.6 逻辑表达式的右子表达式不应有副作用](#ID_shortCircuitSideEffect)
    - [R10.1.7 逻辑表达式应保持简洁明了](#ID_simplifiableCondition)
    - [R10.1.8 可化简为逻辑表达式的三元表达式应尽量化简](#ID_simplifiableTernary)
  - [10.2 Evaluation](#expression.evaluation)
    - [R10.2.1 不可依赖不会生效的副作用](#ID_unevaluatedSideEffect)
    - [R10.2.2 避免依赖特定的子表达式求值顺序](#ID_evaluationOrderReliance)
    - [R10.2.3 在表达式中不应多次读写同一对象](#ID_confusingAssignment)
    - [R10.2.4 注意运算符优先级，避免非预期的结果](#ID_unexpectedPrecedence)
    - [R10.2.5 不在同一数组中的指针不可比较或相减](#ID_illPtrDiff)
    - [R10.2.6 bool 值不应参与位运算、大小比较、数值增减](#ID_illBoolOperation)
    - [R10.2.7 不应出现复合赋值的错误形式](#ID_illFormedCompoundAssignment)
    - [R10.2.8 避免出现复合赋值的可疑形式](#ID_suspiciousCompoundAssignment)
    - [R10.2.9 &=、|=、\-=、/=、%= 左右子表达式不应相同](#ID_illSelfCompoundAssignment)
    - [R10.2.10 不应使用 NULL 对非指针变量赋值](#ID_oddNullAssignment)
    - [R10.2.11 注意赋值运算符与一元运算符的空格方式](#ID_stickyAssignmentOperator)
    - [R10.2.12 赋值运算符左右子表达式不应相同](#ID_selfAssignment)
    - [R10.2.13 除法和求余运算符左右子表达式不应相同](#ID_selfDivision)
    - [R10.2.14 减法运算符左右子表达式不应相同](#ID_selfSubtraction)
    - [R10.2.15 异或运算符左右子表达式不应相同](#ID_selfExclusiveOr)
    - [R10.2.16 负号不应作用于无符号整数](#ID_minusOnUnsigned)
    - [R10.2.17 不应重复使用一元运算符](#ID_repeatedUnaryOperators)
    - [R10.2.18 运算结果不应溢出](#ID_evalOverflow)
    - [R10.2.19 位运算符不应作用于有符号整数](#ID_bitwiseOperOnSigned)
    - [R10.2.20 移位数量不可超过相关类型比特位的数量](#ID_illShiftCount)
    - [R10.2.21 逗号表达式的子表达式应具有必要的副作用](#ID_invalidCommaSubExpression)
  - [10.3 Comparison](#expression.comparison)
    - [R10.3.1 比较运算应在正确的取值范围内进行](#ID_illComparison)
    - [R10.3.2 不应使用 == 或 != 判断浮点数是否相等](#ID_illFloatComparison)
    - [R10.3.3 指针不应与字符串常量直接比较](#ID_illPtrStrComparison)
    - [R10.3.4 不应比较非同类枚举值](#ID_differentEnumComparison)
    - [R10.3.5 比较运算符左右子表达式不应相同](#ID_selfComparison)
    - [R10.3.6 比较运算不可作为另一个比较运算的直接子表达式](#ID_successiveComparison)
  - [10.4 Call](#expression.call)
    - [R10.4.1 不应忽略重要的返回值](#ID_returnValueIgnored)
    - [R10.4.2 不可臆断返回值的意义](#ID_wrongUseOfReturnValue)
    - [R10.4.3 避免对象切片](#ID_objectSlicing)
    - [R10.4.4 避免显式调用析构函数](#ID_explicitDtorCall)
    - [R10.4.5 不应将非 POD 对象传入可变参数列表](#ID_nonPODVariadicArgument)
    - [R10.4.6 C 格式化字符串与其参数的个数应一致](#ID_inconsistentFormatArgNum)
    - [R10.4.7 C 格式化字符串与其参数的类型应一致](#ID_inconsistentFormatArgType)
    - [R10.4.8 在 C\+\+ 代码中禁用 C 风格字符串格式化方法](#ID_forbidCStringFormat)
    - [R10.4.9 禁用 atof、atoi、atol 以及 atoll 等函数](#ID_forbidAtox)
    - [R10.4.10 避免使用由实现定义的库函数](#ID_implementationDefinedFunction)
    - [R10.4.11 合理使用 std::move](#ID_unsuitableMove)
    - [R10.4.12 合理使用 std::forward](#ID_unsuitableForward)
  - [10.5 Sizeof](#expression.sizeof)
    - [R10.5.1 sizeof 不应作用于数组参数](#ID_sizeof_arrayParameter)
    - [R10.5.2 sizeof 不应作用于逻辑表达式](#ID_sizeof_oddExpression)
    - [R10.5.3 sizeof 作用于指针是可疑的](#ID_sizeof_pointer)
    - [R10.5.4 被除数不应是作用于指针的 sizeof 表达式](#ID_sizeof_pointerDivision)
    - [R10.5.5 指针加减偏移量时计入 sizeof 是可疑的](#ID_sizeof_suspiciousAdd)
    - [R10.5.6 sizeof 表达式的结果不应与 0 或负数比较](#ID_sizeof_zeroComparison)
    - [R10.5.7 sizeof 不应再作用于 sizeof 表达式](#ID_sizeof_sizeof)
    - [R10.5.8 C\+\+ 代码中 sizeof 不应作用于 NULL](#ID_sizeof_NULL)
    - [R10.5.9 sizeof 不可作用于 void](#ID_sizeof_void)
  - [10.6 Assertion](#expression.assertion)
    - [R10.6.1 断言中的表达式不应恒为真](#ID_badAssertion)
    - [R10.6.2 断言中的表达式不应有副作用](#ID_sideEffectAssertion)
    - [R10.6.3 断言中的表达式不应过于复杂](#ID_complexAssertion)
  - [10.7 Complexity](#expression.complexity)
    - [R10.7.1 运算符不应超过规定数量](#ID_complexExpression)
  - [10.8 Other](#expression.other)
    - [R10.8.1 不应访问填充数据](#ID_accessPaddingData)
    - [R10.8.2 new 表达式只可用于赋值或当作参数](#ID_oddNew)
    - [R10.8.3 数组下标应为整形表达式](#ID_oddSubscripting)
    - [R10.8.4 禁用逗号表达式](#ID_forbidCommaExpression)
    - [R10.8.5 合理使用括号](#ID_redundantParentheses)
<br/>

<span id="__Literal">**[11. Literal](#literal)**</span>
  - [R11.1 注意可疑的字符常量](#ID_literal_suspiciousChar)
  - [R11.2 字符常量中不可存在应转义而未转义的字符](#ID_literal_hardCodeChar)
  - [R11.3 字符串常量中不可存在应转义而未转义的字符](#ID_literal_hardCodeString)
  - [R11.4 不应使用非标准转义字符](#ID_literal_nonStandardEsc)
  - [R11.5 不同前缀的字符串常量不可连接在一起](#ID_literal_hybridConcat)
  - [R11.6 字符串常量中不应存在拼写错误](#ID_literal_misspelling)
  - [R11.7 整数或浮点数常量的后缀应使用大写字母](#ID_literal_confusingSuffix)
  - [R11.8 禁用 8 进制常量](#ID_literal_forbidOct)
  - [R11.9 整数或浮点数常量应使用标准后缀](#ID_literal_nonStandardSuffix)
  - [R11.10 小心遗漏逗号导致的非预期字符串连接](#ID_literal_oddConcat)
  - [R11.11 不应存在 magic number](#ID_literal_magicNumber)
  - [R11.12 不应存在 magic string](#ID_literal_magicString)
  - [R11.13 不应使用多字符常量](#ID_literal_multicharacter)
<br/>

<span id="__Cast">**[12. Cast](#cast)**</span>
  - [R12.1 避免类型转换造成数据丢失](#ID_narrowCast)
  - [R12.2 避免与 void\* 相互转换](#ID_voidCast)
  - [R12.3 避免向下类型转换](#ID_downCast)
  - [R12.4 指针与整数不应相互转换](#ID_ptrIntCast)
  - [R12.5 类型转换不应去掉 const、volatile 等属性](#ID_qualifierCastedAway)
  - [R12.6 不应强制转换无继承关系的指针或引用](#ID_castNoInheritance)
  - [R12.7 不应强制转换非公有继承关系的指针或引用](#ID_castNonPublicInheritance)
  - [R12.8 非 POD 类的指针与基本类型的指针不应相互转换](#ID_nonPODBinaryCast)
  - [R12.9 不同的字符串类型之间不可直接转换](#ID_charWCharCast)
  - [R12.10 避免向对齐要求更严格的指针转换](#ID_stricterAlignedCast)
  - [R12.11 避免转换指向数组的指针](#ID_arrayPointerCast)
  - [R12.12 避免转换函数指针](#ID_functionPointerCast)
  - [R12.13 向下类型转换应使用 dynamic\_cast](#ID_nonDynamicDownCast)
  - [R12.14 对 new 表达式不应进行类型转换](#ID_oddNewCast)
  - [R12.15 不应存在多余的类型转换](#ID_redundantCast)
  - [R12.16 可用其他方式完成的转换不应使用 reinterpret\_cast](#ID_unsuitableReinterpretCast)
  - [R12.17 合理使用 reinterpret\_cast](#ID_forbidReinterpretCast)
  - [R12.18 在 C\+\+ 代码中禁用 C 风格类型转换](#ID_forbidCStyleCast)
<br/>

<span id="__Buffer">**[13. Buffer](#buffer)**</span>
  - [R13.1 避免缓冲区溢出](#ID_bufferOverflow)
  - [R13.2 数组下标不可越界](#ID_arrayIndexOverflow)
  - [R13.3 为缓冲区分配足够的空间](#ID_insufficientBuffer)
  - [R13.4 memset 等函数不应作用于非 POD 对象](#ID_nonPODFilling)
  - [R13.5 memset 等函数长度相关的参数不应有误](#ID_badLength)
  - [R13.6 memset 等函数填充值相关的参数不应有误](#ID_valueOverflow)
<br/>

<span id="__Pointer">**[14. Pointer](#pointer)**</span>
  - [R14.1 避免空指针解引用](#ID_nullDerefInScp)
  - [R14.2 注意逻辑表达式内的空指针解引用](#ID_nullDerefInExp)
  - [R14.3 不可解引用已被释放的指针](#ID_danglingDeref)
  - [R14.4 避免无效的空指针检查](#ID_invalidNullCheck)
  - [R14.5 不应重复检查指针是否为空](#ID_repeatedNullCheck)
  - [R14.6 不应将非零常量值赋值给指针](#ID_fixedAddrToPointer)
  - [R14.7 不应使用常量 0 表示空指针](#ID_zeroAsPtrValue)
  - [R14.8 不应使用 false 对指针赋值](#ID_oddPtrBoolAssignment)
  - [R14.9 不应使用 '\\0' 等字符常量对指针赋值](#ID_oddPtrCharAssignment)
  - [R14.10 指针不应与 false 比较大小](#ID_oddPtrBoolComparison)
  - [R14.11 指针不应与 '\\0' 等字符常量比较大小](#ID_oddPtrCharComparison)
  - [R14.12 不应判断指针大于、大于等于、小于、小于等于 0](#ID_oddPtrZeroComparison)
  - [R14.13 不应判断 this 指针是否为空](#ID_this_zeroComparison)
  - [R14.14 析构函数中不可使用 delete this](#ID_this_deleteInDestructor)
  - [R14.15 禁用 delete this](#ID_this_forbidDeleteThis)
  - [R14.16 判断 dynamic\_cast 转换是否成功](#ID_nullDerefDynamicCast)
  - [R14.17 指针在释放后应置空](#ID_missingResetNull)
<br/>

<span id="__Interruption">**[15. Interruption](#interruption)**</span>
  - [R15.1 避免由信号处理产生的数据竞争](#ID_sig_dataRaces)
  - [R15.2 处理信号时避免使用非异步信号安全函数](#ID_sig_nonAsyncSafeCall)
  - [R15.3 SIGFPE、SIGILL、SIGSEGV 等信号的处理函数不可返回](#ID_sig_illReturn)
  - [R15.4 禁用 signal 函数](#ID_forbidSignalFunction)
<br/>

<span id="__Concurrency">**[16. Concurrency](#concurrency)**</span>
  - [R16.1 访问共享数据应遵循合理的同步机制](#ID_dataRaces)
  - [R16.2 避免在事务中通过路径多次访问同一文件](#ID_TOCTOU)
  - [R16.3 避免在事务中多次非同步地访问原子对象](#ID_atomicRaces)
  - [R16.4 避免死锁](#ID_deadlock)
  - [R16.5 避免异步终止线程](#ID_asynchronousTermination)
  - [R16.6 避免异步终止共享对象的生命周期](#ID_illLifetime)
  - [R16.7 避免虚假唤醒造成同步错误](#ID_spuriouslyWakeUp)
  - [R16.8 避免并发访问位域造成的数据竞争](#ID_bitfieldDataRaces)
  - [R16.9 多线程环境中不可使用 signal 函数](#ID_signalInMultiThreading)
<br/>

<span id="__Style">**[17. Style](#style)**</span>
  - [R17.1 空格应遵循统一风格](#ID_spaceStyle)
  - [R17.2 大括号应遵循统一风格](#ID_braceStyle)
  - [R17.3 NULL 和 nullptr 不应混用](#ID_mixNullptrAndNULL)
  - [R17.4 在 C\+\+ 代码中用 nullptr 代替 NULL](#ID_deprecatedNULL)
  - [R17.5 赋值表达式不应作为子表达式](#ID_assignmentAsSubExpression)
  - [R17.6 不应存在多余的分号](#ID_redundantSemicolon)<br/><br/>
## <span id="security">1. Security</span>

### <span id="ID_plainSensitiveInfo">▌R1.1 敏感数据不可写入代码</span>

ID_plainSensitiveInfo&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

代码中的敏感数据极易泄露，产品及相关运维、测试工具的代码均不可记录任何敏感数据。  
  
示例：
```
/**
 * My name is Rabbit
 * My passphrase is Y2Fycm90         // Non-compliant
 */

#define PASSWORD "Y2Fycm90"          // Non-compliant

const char* passcode = "Y2Fycm90";   // Non-compliant
```
将密码等敏感数据写入代码是非常不安全的，即使例中 Y2Fycm90 是实际密码的某种变换，聪明的读者也会很快将其破解。  
  
敏感数据的界定是产品设计的重要环节。对具有高可靠性要求的客户端软件，不建议保存任何敏感数据，对于必须保存敏感数据的软件系统，则需要落实安全的存储机制以及相关的评审与测试。
<br/>
<br/>

#### 相关
ID_secretLeak  
<br/>

#### 参考
CWE-798  
CWE-259  
SEI CERT MSC41-C  
<br/>
<br/>

### <span id="ID_secretLeak">▌R1.2 敏感数据不可被系统外界感知</span>

ID_secretLeak&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

敏感数据出入软件系统时需采用有效的保护措施。  
  
示例：
```
void foo(User* u) {
    log("username: %s, password: %s", u->name, u->pw);   // Non-compliant
}
```
显然，将敏感数据直接输出到界面、日志或其他外界可感知的介质中是不安全的，需避免敏感数据的有意外传，除此之外，还需要落实具体的保护措施。  
  
保护措施包括但不限于：  
 - 避免用明文或弱加密方式传输敏感数据  
 - 避免敏感数据从内存交换到外存  
 - 避免如除零、无效指针解引用等问题造成“[core dump](https://en.wikipedia.org/wiki/Core_dump)”  
 - 应具备反调试机制，使外界无法获得进程的内部数据  
 - 应具备反注入机制，使外界无法篡改程序的行为  
  
下面以 Windows 平台为例，给出阻止敏感数据从内存交换到外存的示例：
```
class SecretBuf {
    size_t len = 0;
    unsigned char* buf = nullptr;

public:
    SecretBuf(size_t size) {
        auto* tmp = (unsigned char*)VirtualAlloc(
            0, size, MEM_RESERVE | MEM_COMMIT, PAGE_READWRITE
        );
        if (VirtualLock(tmp, size)) {   // The key point
            buf = tmp;
            len = size;
        } else {
            VirtualFree(tmp, 0, MEM_RELEASE);
        }
    }

   ~SecretBuf() {
        SecureZeroMemory(buf, len);   // Clear the secret content
        VirtualUnlock(buf, len);
        VirtualFree(buf, 0, MEM_RELEASE);
        len = 0;
        buf = nullptr;
    }

    size_t size() const { return len; }
    unsigned char* ptr() { return buf; }
    const unsigned char* ptr() const { return buf; }
};
```
例中 SecretBuf 是一个缓冲区类，其申请的内存会被锁定在物理内存中，不会与外存交换，可在一定程度上防止其他进程的恶意嗅探，保障缓冲区内数据的安全。SecretBuf 在构造函数中通过 VirtualLock 锁定物理内存，在析构函数中通过 VirtualUnlock 解除锁定，解锁之前有必要清除数据，否则解锁之后残留数据仍有可能被交换到外存，进一步可参见 ID\_unsafeCleanup。  
  
SecretBuf 的使用方法如下：
```
void foo() {
    SecretBuf buf(256);
    if (buf.ptr()) {
        ....             // Do something secret using buf.ptr()
    } else {
        ....             // Handle memory error
    }
}
```
对于 Linux 等系统可参见如下有相似功能的系统 API：
```
int mlock(const void* addr, size_t len);     // In <sys/mman.h>
int munlock(const void* addr, size_t len);
int mlockall(int flags);
int munlockall(void);
```
<br/>
<br/>

#### 相关
ID_unsafeCleanup  
<br/>

#### 参考
CWE-528  
CWE-591  
SEI CERT MEM06-C  
<br/>
<br/>

### <span id="ID_unsafeCleanup">▌R1.3 敏感数据在使用后应被有效清理</span>

ID_unsafeCleanup&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

及时清理不再使用的敏感数据是重要的安全措施，且应保证清理过程不会因为编译器的优化而失效。  
  
程序会反复利用内存，敏感数据可能会残留在未初始化的对象或对象之间的填充数据中，如果被存储到磁盘或传输到网络就会造成敏感信息的泄露，可参见 ID\_secretLeak 和 ID\_ignorePaddingData 的进一步讨论。  
  
示例：
```
void foo() {
    char password[8] = {};
    ....
    memset(password, 0, sizeof(password));  // Non-compliant
}
```
示例代码调用 memset 覆盖敏感数据以达到清理目的，然而保存敏感信息的 password 为局部数组且 memset 之后没有再被引用，根据相关标准，编译器可将 memset 过程去掉，使敏感数据没有得到有效清理。  
  
C11 提供了 memset\_s 函数以避免这种问题，某些平台和库也提供了相关支持，如 SecureZeroMemory、explicit\_bzero、OPENSSL\_cleanse 等不会被优化掉的函数。  
  
在 C\+\+ 语言中，可用 volatile 限定相关数据以避免编译器的优化，再用 std::fill\_n 等方法清理，如：
```
void foo() {
    char password[8] = {};
    ....
    volatile char  v_padding = 0;
    volatile char* v_address = password;
    std::fill_n(v_address, sizeof(password), v_padding);  // Compliant
}
```
<br/>
<br/>

#### 相关
ID_secretLeak  
ID_ignorePaddingData  
<br/>

#### 依据
ISO/IEC 9899:2011 5.1.2.3(4)  
ISO/IEC 9899:2011 K.3.7.4.1  
<br/>

#### 参考
CWE-14  
CWE-733  
SEI CERT MSC06-C  
<br/>
<br/>

### <span id="ID_sensitiveName">▌R1.4 公共成员或全局对象不应记录敏感数据</span>

ID_sensitiveName&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

公共成员、全局对象可被外部代码引用，如果存有敏感数据则可能会被误用或窃取。  
  
示例：
```
extern string password;   // Non-compliant

struct A {
    string username;
    string password;      // Non-compliant
};
```
至少应将相关成员改为私有：
```
class A {
public:
    ....                  // Interfaces for accessing passwords safely
private:
    string username;
    string password;      // Compliant
};
```
敏感数据最好对引用者完全隐藏，使数据与接口进一步分离，可参见“[Pimpl idiom](https://en.cppreference.com/w/cpp/language/pimpl)”等模式。
<br/>
<br/>

#### 参考
CWE-766  
<br/>
<br/>

### <span id="ID_hijack">▌R1.5 预判用户输入造成的不良后果</span>

ID_hijack&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

须对用户输入的脚本、路径、资源请求等信息进行预判，对产生不良后果的输入予以拒绝。  
  
示例：
```
Result foo() {
    return sqlQuery(
        "select * from db where key='%s'", userInput()   // Non-compliant
    );
}
```
设 userInput 返回用户输入的字符串，sqlQuery 将用户输入替换格式化占位符后执行 SQL 语句，如果用户输入“xxx' or 'x'='x”一类的字符串则相当于执行的是“select \* from db where key='xxx' or 'x'='x'”，一个恒为真的条件使 where 限制失效，造成所有数据被返回，所以在执行 SQL 语句之前应判断用户输入的安全性。  
  
又如：
```
string bar() {
    return readFile(
        "/myhome/mydata/" + userInput()   // Non-compliant
    );
}
```
这段代码意在将用户输入的路径限制在 /myhome/mydata 目录下，然而这么做是不安全的，如果用户输入带有“../”这种相对路径，则仍可绕过限制，所以在读取文件之前应判断路径的可靠性。  
  
注意，“用户输入”不单指人的手工输入，源自环境变量、配置文件以及其他软硬件的输入均在此范围内。
<br/>
<br/>

#### 参考
CWE-89  
CWE-23  
CWE-73  
<br/>
<br/>

### <span id="ID_unlimitedAuthority">▌R1.6 对文件设定合理的访问权限</span>

ID_unlimitedAuthority&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

文件的访问权限不可过于宽松，否则很容易遭到窃取或篡改。  
  
示例：
```
#include <stdio.h>

int main() {
    umask(000);                     // Non-compliant
    FILE* fp = fopen("bar", "w");   // Old method
    ....
    fclose(fp);
}
```
例中 umask 函数开放了所有用户对文件的读写权限，这是很不安全的，进程之间不应直接通过文件通信，应实现安全的接口和交互机制。  
  
由于历史原因，C 语言的 fopen 和 C\+\+ 语言的 fstream 都不能确保文件只能被当前用户访问，C11 提供了 fopen\_s，C\+\+17 提供了 std::filesystem::permissions 以填补这方面的需求。  
  
C11 fopen\_s 简例：
```
#define __STDC_WANT_LIB_EXT1__ 1
#include <stdio.h>

int main() {
    FILE* fp = NULL;
    errno_t e = fopen_s(&fp, "bar", "w");   // Good
    ....
    fclose(fp);
}
```
与 fopen 不同，fopen\_s 可以不受 umask 等函数的影响，直接将文件的权限设为当前用户私有，其他用户不可访问，降低了文件被窃取或篡改的风险，是一种更安全的方法。  
  
除此之外，如果需要对文件等资源进行更精细的权限管理，可参见“[access control list（ACL）](https://en.wikipedia.org/wiki/Access-control_list)”。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 K.3.5.2.1(7)  
ISO/IEC 14882:2017 30.10.15.26  
<br/>

#### 参考
CWE-266  
CWE-732  
SEI CERT FIO06-C  
<br/>
<br/>

### <span id="ID_improperAuthorization">▌R1.7 落实对用户的权限管理</span>

ID_improperAuthorization&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

需落实对用户的权限管理，对无权限的请求予以拒绝。  
  
示例：
```
Result foo() {
    auto req = getRequest();
    auto res = sqlQuery(
        "select * from db where key='%s'", req["key"]
    );
    return res;
}
```
设 req 对应用户请求，sqlQuery 将请求中的 key 字段替换格式化占位符后执行查询，这个模式存在多种问题，应判断用户是否具有读取数据库相关字段的权限，而且还应判断 req\["key"\] 的值是否安全，详见 ID\_hijack。
<br/>
<br/>

#### 参考
CWE-285  
<br/>
<br/>

### <span id="ID_dangerousName">▌R1.8 不应引用危险符号名称</span>

ID_dangerousName&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

弱加密、弱哈希、弱随机、不安全的协议等相关库、函数、类、宏、常量等名称不应出现在代码中。  
  
这种危险符号名称主要来自：  
 - 低质量随机数生成算法，如 srand、rand 等  
 - 不再适用的哈希算法，如 MD2、MD4、MD5、MD6、RIPEMD 以及 SHA\-1 等  
 - 非加密协议，如 HTTP、FTP 等  
 - 低版本的传输层安全协议，如 TLSv1.2 之前的版本  
 - 弱加密算法，如 DES、3DES 等  
  
示例：
```
#include <openssl/md5.h>   // Non-compliant, obsolete hash algorithm

const string myUrl = "http://foo/bar";   // Non-compliant, use https instead

void foo() {
    MD5_CTX c;       // Non-compliant
    MD5_Init(&c);    // Non-compliant, obsolete hash algorithm
    ....
}

void bar() {
    srand(0);        // Non-compliant, unsafe random seed
    EVP_des_ecb();   // Non-compliant, unsafe encryption algorithm
    ....
}
```
审计工具不妨通过配置设定关注的危险符号名称，当代码中出现了这些名称时就给出警告。  
  
配置示例：
```
[ID_dangerousName]
srand|random_shuffle=Weak random
EVP_des_ecb|EVP_des_cbc=Weak encryption
http|ftp=Non encrypted protocol
CURL_SSLVERSION_TLSv1|TLS1_1_VERSION=Old TLS version
```
配置项等号左侧为危险符号名称，右侧为相关说明，可以指定多个名称对应一个说明，用“|”分隔。  
  
例中 srand 以及弱加密算法 EVP\_des\_ecb、EVP\_des\_cbc 等名称被设为危险名称，当代码中出现同名符号时就按相关说明给出警告。
<br/>
<br/>

#### 配置
详见说明  
<br/>

#### 参考
CWE-326  
CWE-327  
<br/>
<br/>

### <span id="ID_dangerousFunction">▌R1.9 避免使用具有危险性的函数</span>

ID_dangerousFunction&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

某些函数本身就具有危险性，使用这种函数相当于直接引入了风险。  
  
示例：
```
gets       // The most dangerous function
mktemp     // Every use of ‘mktemp’ is a security risk, use ‘mkstemp’ instead
getpass    // Unsafe and not portable
crypt      // Unsafe, exhaustive searches of the key space are possible
getpw      // It may overflow the provided buffer, use ‘getpwuid’ instead
cuserid    // Not portable and unreliable, use ‘getpwuid(geteuid())’ instead
chgrp      // Prone to TOCTOU race conditions, use ‘fchgrp’ instead
chown      // Prone to TOCTOU race conditions, use ‘fchown’ instead
chmod      // Prone to TOCTOU race conditions, use ‘fchmod’ instead

SuspendThread       // Forced suspension of a thread can cause many problems
TerminateThread     // Forced termination of a thread can cause many problems
GlobalMemoryStatus        // Return incorrect information, use ‘GlobalMemoryStatusEx’ instead
SetProcessWorkingSetSize  // Cause adverse effects on other processes and the entire system
```
gets 等函数无法检查缓冲区大小，是公认的危险函数，TerminateThread 等 Windows API 会强制结束线程的执行，线程持有的资源无法正确释放会导致泄漏或死锁，这类函数应避免使用。  
  
审计工具不妨通过配置设定关注的危险函数名称，当代码中出现了这些名称时就给出警告。  
  
配置示例：
```
[ID_dangerousFunction]
gets|_getws=The most dangerous function
TerminateThread=Forced termination of a thread can cause many problems
```
配置项等号左侧为危险函数名称，右侧为相关说明，可以指定多个名称对应一个说明，用“|”分隔。
<br/>
<br/>

#### 配置
详见说明  
<br/>

#### 参考
CWE-242  
CWE-474  
CWE-676  
<br/>
<br/>

### <span id="ID_obsoleteFunction">▌R1.10 不应使用已过时的函数</span>

ID_obsoleteFunction&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

某些函数存在缺陷或漏洞并已宣布过时，应使用更完善的替代方法。  
  
示例：
```
ctime           // Use ‘strftime’ instead
asctime         // Use ‘strftime’ instead
bcmp            // Use ‘memcmp’ instead
bcopy           // Use ‘memmove’ or ‘memcpy’ instead
bsd_signal      // Use ‘sigaction’ instead
gethostbyaddr   // Use ‘getnameinfo’ instead
gethostbyname   // Use ‘getaddrinfo’ instead

RegCreateKey    // Use ‘RegCreateKeyEx’ instead
RegEnumKey      // Use ‘RegEnumKeyEx’ instead
RegOpenKey      // Use ‘RegOpenKeyEx’ instead
RegQueryValue   // Use ‘RegQueryValueEx’ instead
RegSetValue     // Use ‘RegSetValueEx’ instead
```
例中 C89 声明的 ctime、asctime 等函数在 POSIX.1\-2008 中已宣告过时，应改用 strftime，RegCreateKey 等 16 位 Windows API 在 32 和 64 位平台中不应再使用。  
  
审计工具不妨通过配置设定关注的过时函数名称，当代码中出现了这些名称时就给出警告。  
  
配置示例：
```
[ID_obsoleteFunction]
asctime|asctime_r=use 'strftime' instead
bcopy=use 'memmove' or 'memcpy' instead
```
配置项等号左侧为过时函数名称，右侧为相关说明，可以指定多个名称对应一个说明，用“|”分隔。
<br/>
<br/>

#### 配置
详见说明  
<br/>

#### 参考
CWE-477  
<br/>
<br/>

### <span id="ID_unsafeStringFunction">▌R1.11 禁用不安全的字符串函数</span>

ID_unsafeStringFunction&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: security warning

<hr/>

由于历史原因，C 语言某些字符串函数不检查缓冲区长度，易造成运行时错误或安全漏洞。  
  
这类函数包括：
```
gets、sprintf、scanf、sscanf、fscanf、
vfscanf、vsprintf、vscanf、vsscanf、
strcpy、strcat、wcscpy、wcscat、
StrCpy、StrCpyA、StrCpyW、StrCat、StrCatA、StrCatW、
lstrcat、lstrcatA、lstrcatW、lstrcpy、lstrcpyA、lstrcpyW
```
对于 C\+\+ 代码，应采用 STL 标准库提供的相关功能。  
对于 C 代码，应采用更安全的库函数，如用 fgets 代替 gets，snprintf 代替 sprintf。  
  
示例：
```
char buf[100];
gets(buf);      // Non-compliant
```
例中 gets 函数无法检查缓冲区的大小，一旦输入超过了 buf 数组的边界，程序的数据或流程就会遭到破坏，这种情况会被攻击者利用，可参见 ID\_bufferOverflow 的进一步说明。如果代码中存在 gets 等函数，可以直接判定程序是有漏洞的。  
  
应改为：
```
char buf[100];
fgets(buf, sizeof(buf), stdin);  // Compliant
```
fgets 与 gets 不同，当输入超过缓冲区大小时会被截断，保证缓冲区之外的数据不会被破坏。  
  
又如：
```
char buf[100];
scanf("%s", buf);  // Non-compliant
```
例中 scanf 函数与 gets 函数有相同的问题，可改为：
```
char buf[100];
scanf("%99s", buf);  // Let it go, but ‘fgets’ is better
```
scanf、sprintf、strcpy 等函数无视缓冲区大小，需要在外部另行实现防止缓冲区溢出的代码，完全依赖于编写者的小心谨慎。历史表明，对人的单方面依赖是不可靠的，改用更安全的方法才是明智的选择。
<br/>
<br/>

#### 相关
ID_bufferOverflow  
<br/>

#### 依据
ISO/IEC 9899:2011 K.3.7  
<br/>

#### 参考
CWE-119  
CWE-120  
CWE-676  
MISRA C++ 2008 18-0-5  
<br/>
<br/>

### <span id="ID_improperNullTermination">▌R1.12 确保字符串以空字符结尾</span>

ID_improperNullTermination&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

语言要求字符串以空字符结尾，程序应保证有足够的内存空间安置空字符，否则会破坏程序基本的执行机制，造成严重问题。  
  
空字符指 '\\0'、L'\\0'、u'\\0'、U'\\0'，分别对应 char\*、wchar\_t\*、char16\_t\*、char32\_t\* 等字符串类型。  
  
示例：
```
void foo(const char* p) {
    char a[4];
    strncpy(a, p, sizeof(a));
    printf("%s\n", strupr(a));   // To upper case and print, dangerous
}
```
例示代码将字符串复制到数组中，转为大写并打印，然而如果 p 所指字符串的长度超过 3，strncpy 不会在数组的结尾安置空字符 '\\0'，会导致内存访问错误，程序可能会崩溃，也可能打印出本该隐藏的敏感数据。  
  
应改为：
```
void foo(const char* p) {
    char a[4] = "";                 // Initialize all to '\0'
    strncpy(a, p, sizeof(a));
    if (a[3] == '\0') {
        printf("%s\n", strupr(a));  // OK
    } else {
        ....                        // Handle string length exceptions
    }
}
```
将所有数组元素初始化为 '\\0'，调用 strncpy 后如果数组最后一个元素是 '\\0'，说明输入字符串的长度符合要求，否则可作出相应的异常处理。
<br/>
<br/>

#### 相关
ID_unsafeStringFunction  
<br/>

#### 依据
ISO/IEC 9899:2011 7.24.2.4  
<br/>

#### 参考
CWE-170  
<br/>
<br/>

### <span id="ID_divideByZero">▌R1.13 避免除 0 等计算异常</span>

ID_divideByZero&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security error

<hr/>

除 0 等在数学上没有定义的运算、浮点异常、非法指令、段错误等问题称为“计算异常”，意味着严重的底层运行时错误，而且这种异常无法用语言层面的常规方法捕获。  
  
示例：
```
int foo(int n) {
    if (n) {
        ....
    }
    return 100 / n;   // Non-compliant, must determine whether ‘n’ is 0
}
```
当除数为 0 时，对于整形数据的除法，进程往往会崩溃，对于浮点型数据的除法，一般会产生“[Inf](https://en.wikipedia.org/wiki/Infinity#Computing)”或“[NaN](https://en.wikipedia.org/wiki/NaN)”等无效结果，在特殊的设置下，也可以使进程异常终止。  
  
崩溃会给用户不好的体验，而且要注意如果崩溃可由外部输入引起，会被攻击者利用从而迫使程序无法正常工作，具有高可靠性要求的服务类程序更应该注意这一点，可参见“[拒绝服务攻击](https://en.wikipedia.org/wiki/Denial-of-service_attack)”的进一步说明。对于客户端程序，也要防止攻击者对崩溃产生的“[core dump](https://en.wikipedia.org/wiki/Core_dump)”进行恶意调试，避免泄露敏感数据，总之程序的健壮性与安全性是紧密相关的。
<br/>
<br/>

#### 相关
ID_sig_illReturn  
<br/>

#### 依据
ISO/IEC 9899:1999 6.5.5(5)-undefined  
ISO/IEC 9899:2011 6.5.5(5)-undefined  
ISO/IEC 14882:2011 5.6(4)-undefined  
ISO/IEC 14882:2017 8.6(4)-undefined  
<br/>

#### 参考
CWE-369  
C++ Core Guidelines ES.105  
<br/>
<br/>

### <span id="ID_variableFormatString">▌R1.14 格式化字符串应为常量</span>

ID_variableFormatString&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

出于可读性和安全性的考量，格式化字符串最好直接写成常量字符串的形式。  
  
本规则是 ID\_hijack 的特化。  
  
示例：
```
int a, b, c;
const char* fmt = foo();
....
printf(fmt, a, b, c);  // Non-compliant
```
例中格式化字符串 fmt 是变量，这种方式可读性较差，而且要注意如果 fmt 可受外界影响，则可能被攻击者利用造成不良后果。  
  
应将 fmt 改为常量：
```
printf("%d %d %d", a, b, c);  // Compliant
```
<br/>
<br/>

#### 相关
ID_hijack  
<br/>

#### 参考
CWE-134  
<br/>
<br/>

### <span id="ID_addressExposure">▌R1.15 与内存空间布局相关的信息不可被外界感知</span>

ID_addressExposure&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

函数、对象、缓冲区的地址以及相关内存区域的长度等信息不可被外界感知，否则会成为攻击者的线索。  
  
示例：
```
int foo(int* p, int n) {
    if (n >= some_value) {
        log("buffer address: %p, size: %d", p, n);   // Non-compliant
    }
}
```
示例代码将缓冲区的地址和长度输出到日志是不安全的，这种代码多以调试为目的，不应将其编译到产品的正式版本中。
<br/>
<br/>

#### 相关
ID_bufferOverflow  
<br/>

#### 参考
CWE-200  
<br/>
<br/>

### <span id="ID_hardcodedIP">▌R1.16 与网络地址相关的信息不应写入代码</span>

ID_hardcodedIP&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

在代码中记录网络地址不利于维护和移植，也容易暴露产品的网络结构，属于安全隐患。  
  
示例：
```
string host = "10.16.25.93";    // Non-compliant
foo("172.16.10.36:8080");       // Non-compliant
bar("https://192.168.73.90");   // Non-compliant
```
应从配置文件中获取地址，并配以加密措施：
```
MyConf cfg;
string host = cfg.host();   // Compliant
foo(cfg.port());            // Compliant
bar(cfg.url());             // Compliant
```
特殊的 IP 地址可不受本规则限制，如：
```
0.0.0.0
255.255.255.255
127.0.0.1-127.255.255.255
```
<br/>
<br/>

#### 相关
ID_addressExposure  
<br/>
<br/>

### <span id="ID_deprecatedErrno">▌R1.17 选择安全的异常处理方式</span>

ID_deprecatedErrno&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

避免使用 errno 和与其相同的模式，应根据实际需求选择通过函数返回值或 C\+\+ 异常机制来处理异常情况。  
  
errno 被设定的位置和被读取的位置相距较远，不遵循固定的静态结构，极易误用，是不安全的异常处理方式，对异常情况的错误处理往往会成为业务漏洞，使攻击者轻易地实现其目的。  
  
示例：
```
void foo() {
    if (somecall() == FAILED) {
        printf("somecall failed\n");
        if (errno == SOME_VALUE) {     // Non-compliant
            .... 
        }
    }
}
```
例中 somecall 执行异常，通过 errno 获取异常信息，但 errno 的值会被 printf 修改，相应的异常处理也失去了意义。  
  
又如：
```
void bar(const char* s) {
    int i = atoi(s);
    if (errno) {        // Non-compliant
        ....
    }
}
```
errno 并不能反映所有异常情况，atoi 等函数与 errno 无关，例中 errno 的值来自函数外部难以预料的位置，相应的异常处理也将是错误的。
<br/>
<br/>

#### 参考
MISRA C 2004 20.5  
MISRA C++ 2008 19-3-1  
C++ Core Guidelines E.28  
<br/>
<br/>

### <span id="ID_missingHardening">▌R1.18 启用平台和编译器提供的防御机制</span>

ID_missingHardening&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security suggestion

<hr/>

针对一些常见攻击，平台和编译器会提供防御机制，如：  
 - [数据执行保护（NX、DEP）](https://en.wikipedia.org/wiki/Executable_space_protection)  
 - [栈溢出防护（CANARY、GS）](https://en.wikipedia.org/wiki/Buffer_overflow_protection)  
 - [地址空间布局随机化（ASLR、PIE）](https://en.wikipedia.org/wiki/Address_space_layout_randomization)  
  
程序应利用这种机制加强自身的安全性，进一步可参见“[security hardening](https://en.wikipedia.org/wiki/Hardening_(computing))”。  
  
示例：
```
// In test.c
#include <stdio.h>

int main(void) {
    printf("%p\n", main);
}
```
如果在 Linux 等平台上按如下方式编译：
```
cc test.c -o test
```
各函数的地址在虚拟内存中是固定的，易被攻击者猜中，进而施展攻击手段。  
  
当平台启用了“[ASLR](https://en.wikipedia.org/wiki/Address_space_layout_randomization)”机制，再按如下方式编译：
```
cc test.c -o test -fPIE -pie
```
可使程序各结构的地址随机化，函数的地址在每次运行时均不相同， 有效提高了攻击难度。  
  
如无特殊原因，在编译程序时不应屏蔽这种防御机制，如：
```
cc test.c -o test -z execstack           # Non-compliant, disable NX
cc test.c -o test -z norelro             # Non-compliant, disable RELRO
cc test.c -o test -fno-stack-protector   # Non-compliant, disable CANARY
```
如果必须屏蔽，应落实相关的评审与测试。
<br/>
<br/>
<br/>

## <span id="resource">2. Resource</span>

### <span id="ID_resourceLeak">▌R2.1 不可失去对已分配资源的控制</span>

ID_resourceLeak&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

已分配资源的指针、句柄或描述符等信息不可被遗失，否则相关资源无法被访问也无法被回收，会导致资源耗尽以及死锁等问题，使程序无法正确运行。  
  
程序需要保证资源分配与回收之间的流程可达，且不可被异常中断，所在线程也不可在中途停止。  
  
关于内存资源，本规则特化为 ID\_memoryLeak。  
  
示例：
```
void foo(const char* path) {
    FILE* p = fopen(path, "w");
    if (cond) {
        return;    // Non-compliant, ‘p’ is lost
    }
    ....
    fclose(p);
}
```
例中 p 指向文件对象，关闭文件对象之前在某种情况下返回，造成了文件资源的遗失。
<br/>
<br/>

#### 相关
ID_memoryLeak  
<br/>

#### 参考
C++ Core Guidelines P.8  
C++ Core Guidelines E.13  
<br/>
<br/>

### <span id="ID_memoryLeak">▌R2.2 不可失去对已分配内存的控制</span>

ID_memoryLeak&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

已分配内存的地址不可被遗失，否则相关内存无法被访问也无法被回收，这种问题称为“[内存泄漏（memory leak）](https://en.wikipedia.org/wiki/Memory_leak)”，会导致可用内存被耗尽，使程序无法正确运行。  
  
程序需要保证内存分配与回收之间的流程可达，且不可被异常中断，所在线程也不可在中途停止。  
  
本规则是 ID\_resourceLeak 的特化。  
  
示例：
```
void foo(size_t size) {
    char* p = (char*)malloc(size);
    if (cond) {
        return;    // Non-compliant, ‘p’ is lost
    }
    ....
    free(p);
}
```
本例由局部变量 p 记录申请的内存空间，释放之前在某种情况下函数返回，之后便再也无法访问到这块内存空间了，造成了内存空间的遗失。
<br/>
<br/>

#### 相关
ID_resourceLeak  
ID_ownerlessResource  
<br/>

#### 参考
C++ Core Guidelines P.8  
C++ Core Guidelines E.13  
<br/>
<br/>

### <span id="ID_illAccess">▌R2.3 不可访问未初始化或已释放的资源</span>

ID_illAccess&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource error

<hr/>

访问未初始化或已释放的资源属于逻辑错误，也会导致标准未定义的行为。  
  
示例：
```
void foo(const char* path) {
    FILE* p = NULL;
    char buf[100] = {};
    if (path != NULL) {
        p = fopen(path, "rb");
    }
    fread(buf, 1, 100, p);       // Non-compliant, ‘p’ may be invalid
    ....
    fclose(p);
    ....
    fread(buf, 1, 100, p);       // Non-compliant, ‘p’ is closed
    ....
}
```
关于解引用已经释放的指针，特化为 ID\_danglingDeref。  
关于访问未初始化的局部对象，特化为 ID\_localInitialization。
<br/>
<br/>

#### 相关
ID_danglingDeref  
ID_localInitialization  
<br/>

#### 依据
ISO/IEC 9899:2011 7.22.3.3(2)-undefined  
ISO/IEC 14882:2011 3.7.4.2(4)-undefined  
<br/>

#### 参考
SEI CERT FIO46-C  
<br/>
<br/>

### <span id="ID_ownerlessResource">▌R2.4 资源应接受对象化管理</span>

ID_ownerlessResource&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

使资源接受对象化管理，免去繁琐易错的手工分配回收过程，是 C\+\+ 程序设计的重要方法。  
  
将资源分配的结果直接在程序中传递是非常不安全的，极易产生泄漏或死锁等问题。动态申请的资源如果只用普通变量引用，不受对象的构造或析构机制控制，则称为“无主”资源，在 C\+\+ 程序设计中应当避免。  
  
应尽量使用标准库提供的容器或智能指针，避免显式使用资源管理接口。本规则集合示例中的 new/delete、lock/unlock 意在代指一般的资源操作，仅作示例，在实际代码中应尽量避免。  
  
示例：
```
void foo(size_t size) {
    int* p = new int[size];   // Bad, ownerless
    ....                      // If any exception is thrown, or a wrong jump, leak
    delete[] p;
}

struct X {
    int* p;
};

void bar() {
    X x;
    x.p = new int[123];   // Bad, ‘X’ has no destructor, ‘x’ is not an owner
    ....
}

class Y {
    int* p;

public:
    Y(size_t n): p(new int[n]) {}
   ~Y() { delete[] p; }
};

void baz() {
    Y y(123);   // Good, ‘y’ is the owner of the resource
    ....
}
```
例中 foo 和 bar 函数的资源管理方式是不符合 C\+\+ 理念的，baz 函数中的 y 对象负责资源的分配与回收，称 y 对象具有资源的所有权，相关资源的生命周期与 y 的生命周期一致，有效避免了资源泄漏或错误回收等问题。  
  
资源的所有权可以发生转移，但应保证转移前后均有对象负责管理资源，并且在转移过程中不会产生异常。进一步理解对象化管理方法，可参见“[RAII（Resource Acquisition Is Initialization）](https://en.wikipedia.org/wiki/Resource_acquisition_is_initialization)”等机制。  
  
与资源相关的系统接口不应直接被业务代码引用，如：
```
void foo(const TCHAR* path) {
    HANDLE h;
    WIN32_FIND_DATA ffd;

    h = FindFirstFile(path, &ffd);  // Bad, ownerless
    ....
    CloseHandle(h);  // Is it right?
}
```
例中 Windows API FindFirstFile 返回资源句柄，是“无主”资源，很可能被后续代码误用或遗忘。  
  
应进行合理封装：
```
class MY_FIND_DATA
{
    struct HANDLE_DELETER
    {
        using pointer = HANDLE;
        void operator()(pointer p) { FindClose(p); }
    };
    WIN32_FIND_DATA ffd;
    unique_ptr<HANDLE, HANDLE_DELETER> uptr;

public:
    MY_FIND_DATA(const TCHAR* path): uptr(FindFirstFile(path, &ffd)) {}
    ....
    HANDLE handle() { return uptr.get(); }
};
```
本例将 FindFirstFile 及其相关数据封装成一个类，由 unique\_ptr 对象保存 FindFirstFile 的结果，FindClose 是资源的回收方法，将其作为 unique\_ptr 对象的组成部分，使资源可以被自动回收。
<br/>
<br/>

#### 参考
C++ Core Guidelines R.11  
C++ Core Guidelines R.12  
<br/>
<br/>

### <span id="ID_incompleteNewDeletePair">▌R2.5 资源的分配与回收方法应成对提供</span>

ID_incompleteNewDeletePair&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource suggestion

<hr/>

资源的分配方法和相应的回收方法应在同一模块中提供。  
  
如果一个模块分配的资源需要另一个模块回收，会打破模块之间的独立性，使维护成本显著增加，而且 so、dll、exe 等模块一般都有独立的堆栈，跨模块的分配与回收往往会造成严重错误。  
  
示例：
```
// In a.dll
int* foo() {
    return (int*)malloc(1024);
}

// In b.dll
void bar() {
    int* p = foo();
    ....
    free(p);   // Non-compliant, crash
}
```
例中 a.dll 分配的内存由 b.dll 释放，相当于混淆了不同堆栈中的数据，程序一般会崩溃。  
  
应改为：
```
// In a.dll
int* foo_alloc() {
    return (int*)malloc(1024);
}

void foo_dealloc(int* p) {
    free(p);
}

// In b.dll
void bar() {
    int* p = foo_alloc();
    ....
    foo_dealloc(p);   // Compliant
}
```
修正后 a.dll 成对提供分配回收函数，b.dll 配套使用这些函数，避免了冲突。  
  
对类等逻辑模块也有相同要求，在构造函数中分配了资源，应提供相应的析构函数，重载了 new 运算符，也应重载相应的 delete 运算符。  

```
class A {
    void* operator new(size_t);   // Non-compliant, missing ‘operator delete’
};

class B {
    void operator delete(void*);   // Non-compliant, missing ‘operator new’
};

class C {
    void* operator new(size_t);   // Compliant
    void operator delete(void*);   // Compliant
};
```
placement\-new 与 placement\-delete 也应成对提供：
```
class D {
    void* operator new(size_t, bool);   // Non-compliant

    void* operator new(size_t, int, int);   // Compliant
    void operator delete(void*, int, int);   // Compliant
};
```
<br/>
<br/>

#### 相关
ID_memberDeallocation  
ID_crossModuleTransfer  
ID_incompatibleDealloc  
<br/>

#### 参考
C++ Core Guidelines R.15  
<br/>
<br/>

### <span id="ID_incompatibleDealloc">▌R2.6 资源的分配与回收方法应配套使用</span>

ID_incompatibleDealloc&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource error

<hr/>

使用了某种分配方法，就应使用与其配套的回收方法，否则会引发严重错误。  
  
示例：
```
void foo() {
    T* p = new T;
    ....
    free(p);   // Non-compliant, use ‘delete’ instead
}

void bar(size_t n) {
    char* p = (char*)malloc(n);
    ....
    delete[] p;   // Non-compliant, use ‘free’ instead
}
```
不同的分配回收方法属于不同的资源管理体系，用 new 分配的资源应使用 delete 回收，malloc 分配的应使用 free 回收。
<br/>
<br/>

#### 相关
ID_incompleteNewDeletePair  
<br/>

#### 依据
ISO/IEC 9899:2011 7.22.3.3(2)-undefined  
ISO/IEC 9899:2011 7.22.3.4(3)-undefined  
ISO/IEC 14882:2011 3.7.4.2(3)-undefined  
<br/>

#### 参考
SEI CERT MEM51-CPP  
<br/>
<br/>

### <span id="ID_crossModuleTransfer">▌R2.7 模块之间不应传递容器等对象</span>

ID_crossModuleTransfer&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

容器的容量可以动态变化，在模块间传递这种对象会造成分配回收方面的冲突。  
  
具有同等功能的对象，如流、字符串、智能指针等均不应在模块间传递。  
  
在模块间传递复杂的对象也意味着模块耦合过于紧密，不是良好的设计。如果必须在模块间传递对象，需将所有相关资源的分配与回收过程限定在同一模块内，是繁琐且不利于维护的。  
  
示例：
```
// In a.dll
void foo(vector<int>& v) {
    v.reserve(100);
}

// In b.exe
int main() {
    vector<int> v {  // Allocation in b.exe
        1, 2, 3
    };
    foo(v);          // Non-compliant, reallocation in a.dll, crash
}
```
例中容器 v 的初始内存由 b.exe 分配，b.exe 与 a.dll 有各自独立的堆栈，由于模板库的内联实现，reserve 函数会调用 a.dll 的内存管理函数重新分配 b.exe 中的内存，造成严重冲突。  
  
另外，不同的模块可能由不同的编译器生成，声明与实现的差异也会导致冲突，参见“[Dll hell](https://en.wikipedia.org/wiki/DLL_Hell)”。
<br/>
<br/>

#### 相关
ID_incompleteNewDeletePair  
<br/>
<br/>

### <span id="ID_memberDeallocation">▌R2.8 对象申请的资源应在析构函数中释放</span>

ID_memberDeallocation&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

对象在析构函数中释放自己申请的资源是 C\+\+ 程序设计的重要原则，不可被遗忘，也不应要求用户释放。  
  
示例：
```
class A {
    int* p = nullptr;

public:
    A(size_t n): p(new int[n]) {
    }

   ~A() {  // Non-compliant, must delete[] p
    }
};
```
例中成员 p 与内存分配有关，但析构函数为空，不符合本规则要求。
<br/>
<br/>

#### 相关
ID_memoryLeak  
ID_resourceLeak  
<br/>

#### 参考
C++ Core Guidelines C.31  
C++ Core Guidelines E.6  
<br/>
<br/>

### <span id="ID_useAfterMove">▌R2.9 对象被移动后不应再被使用</span>

ID_useAfterMove&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

std::move 宣告对象的数据即将被转移到其他对象，转移之后对象在逻辑上不再有效，不应再被使用。  
  
示例：
```
string foo(string a) {
    string b = std::move(a);
    return a + b;              // Non-compliant
}
```
例中 a 对象的数据被转移到 b 对象，之后 a 对象不再有效，对 a 重新赋值之前访问 a 属于逻辑错误。
<br/>
<br/>

#### 相关
ID_unsuitableMove  
<br/>

#### 参考
SEI CERT EXP63-CPP  
C++ Core Guidelines ES.56  
<br/>
<br/>

### <span id="ID_throwInConstructor">▌R2.10 构造函数抛出异常需避免相关资源泄漏</span>

ID_throwInConstructor&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

构造函数抛出异常表示对象构造失败，不会再执行相关析构函数，需要保证已分配的资源被有效回收。  
  
示例：
```
class A {
    int *a, *b;

public:
    A(size_t n):
        a(new int[n]),
        b(new int[n])     // The allocations may fail
    {
        if (sth_wrong) {
            throw E();    // User exceptions
        }
    }

   ~A() {                 // May be invalid
        delete[] a;
        delete[] b;
    }
};
```
例中内存分配可能会失败，抛出 bad\_alloc 异常，在某种条件下还会抛出自定义的异常，任何一种异常被抛出析构函数就不会被执行，已分配的资源就无法被回收，但已构造完毕的对象还是会正常析构的，所以应采用对象化资源管理方法，使资源可以被自动回收。  
  
可改为：
```
A::A(size_t n) {
    // Use objects to hold resources
    auto holder_a = make_unique<int[]>(n);
    auto holder_b = make_unique<int[]>(n);

    // Do the tasks that may throw exceptions
    if (sth_wrong) {
        throw E();
    }

    // Transfer ownership, make sure no exception is thrown
    a = holder_a.release();
    b = holder_b.release();
}
```
先用 unique\_ptr 对象持有资源，完成可能抛出异常的事务之后，再将资源转移给相关成员，转移的过程不可抛出异常，这种模式可以保证异常安全，如果有异常抛出，资源均可被正常回收。对遵循 C\+\+11 及之后标准的代码，建议用 make\_unique 函数代替 new 运算符。  
  
示例代码意在讨论一种通用模式，实际代码可采用更直接的方式：
```
class A {
    vector<int> a, b;  // Or use ‘unique_ptr’

public:
    A(size_t n): a(n), b(n) {  // Safe and brief
        ....
    }
};
```
保证已分配的资源时刻有对象负责回收是重要的设计原则，可参见 ID\_ownerlessResource 的进一步讨论。  
  
注意，“未成功初始化的对象”在 C\+\+ 语言中是不存在的，应避免相关逻辑错误，如：
```
struct T {
    A() { throw CtorException(); }
};

void foo() {
    T* p = nullptr;
    try {
        p = new T;
    }
    catch (CtorException&) {
        delete p;              // Logic error, ‘p’ is nullptr
        return;
    }
    ....
    delete p;
}
```
例中 T 类型的对象在构造时抛出异常，而实际上 p 并不会指向一个未能成功初始化的对象，赋值被异常中断，catch  中的 p 仍然是一个空指针，new 表达式中抛出异常会自动回收已分配的内存。
<br/>
<br/>

#### 相关
ID_ownerlessResource  
ID_multiAllocation  
ID_memoryLeak  
<br/>
<br/>

### <span id="ID_doubleFree">▌R2.11 资源不可被重复释放</span>

ID_doubleFree&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource error

<hr/>

重复释放资源属于逻辑错误，导致标准未定义的行为。  
  
示例：
```
void foo(const char* path) {
    FILE* p = fopen(path, "r");
    if (p) {
        ....
        fclose(p);
    }
    fclose(p);  // Non-compliant
}
```
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 7.22.3.3(2)-undefined  
ISO/IEC 14882:2011 3.7.4.2(4)-undefined  
<br/>

#### 参考
CWE-415  
<br/>
<br/>

### <span id="ID_deleteIncompleteType">▌R2.12 用 delete 释放对象需保证其类型完整</span>

ID_deleteIncompleteType&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

如果用 delete 释放不完整类型的对象，而对象完整类型声明中有 non\-trivial 析构函数，会导致标准未定义的行为。  
  
示例：
```
struct T;

void foo(T* p) {
    delete p;       // Non-compliant, undefined behavior
}

struct T {
   ~T();            // Non-trivial destructor
};
```
例中 delete 作用于不完整类型的指针 p，析构函数不会正确执行，应保证 T 在 foo 之前定义：
```
struct T {
   ~T();
};

void foo(T* p) {
    delete p;       // Compliant
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 5.3.5(5)-undefined  
<br/>
<br/>

### <span id="ID_excessiveDelete">▌R2.13 用 delete 释放对象不可多写中括号</span>

ID_excessiveDelete&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource error

<hr/>

用 new 分配的对象应该用 delete 释放，不可用 delete\[\] 释放，否则导致标准未定义的行为。  
  
示例：
```
auto* p = new X;  // One object
....
delete[] p;       // Non-compliant, use ‘delete p;’ instead
```
<br/>
<br/>

#### 相关
ID_insufficientDelete  
<br/>

#### 依据
ISO/IEC 14882:2003 5.3.5(2)-undefined  
ISO/IEC 14882:2011 5.3.5(2)-undefined  
ISO/IEC 14882:2017 8.3.5(2)-undefined  
<br/>

#### 参考
C++ Core Guidelines ES.61  
<br/>
<br/>

### <span id="ID_insufficientDelete">▌R2.14 用 delete 释放数组不可漏写中括号</span>

ID_insufficientDelete&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource error

<hr/>

用 new 分配的数组应该用 delete\[\] 释放，不可漏写中括号，否则导致标准未定义的行为。  
  
示例：
```
void foo(int n) {
    auto* p = new X[n];  // n default constructed Xs
    ....
    delete p;            // Non-compliant, use ‘delete[] p;’ instead
}
```
在某些环境中，可能只有第一个对象的析构函数被执行，其他对象的析构函数都没有被执行，如果对象与资源分配有关就会导致资源泄漏。
<br/>
<br/>

#### 相关
ID_excessiveDelete  
<br/>

#### 依据
ISO/IEC 14882:2003 5.3.5(2)-undefined  
ISO/IEC 14882:2011 5.3.5(2)-undefined  
ISO/IEC 14882:2017 8.3.5(2)-undefined  
<br/>

#### 参考
C++ Core Guidelines ES.61  
<br/>
<br/>

### <span id="ID_illDealloc">▌R2.15 非动态申请的资源不可被释放</span>

ID_illDealloc&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource error

<hr/>

释放非动态申请的资源会导致标准未定义的行为。  
  
示例：
```
void foo(size_t size) {
    int* p = (int*)alloca(size);
    ....
    free(p);    // Non-compliant, ‘p’ should not be freed
}

void bar() {
    int i;
    free(&i);   // Non-compliant, naughty behaviour
}
```
释放在栈上分配的空间或者局部对象的地址会造成严重的运行时错误。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 7.22.3.3(2)-undefined  
ISO/IEC 9899:2011 7.22.3.4(3)-undefined  
ISO/IEC 14882:2011 5.3.5(2)-undefined  
<br/>

#### 参考
MISRA C 2012 22.2  
<br/>
<br/>

### <span id="ID_multiAllocation">▌R2.16 在一个表达式语句中最多使用一次 new</span>

ID_multiAllocation&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

如果表达式语句多次使用 new，一旦某个构造函数抛出异常就会造成内存泄漏。  
  
示例：
```
fun(
    shared_ptr<T>(new T),
    shared_ptr<T>(new T)   // Non-compliant, potential memory leak
);
```
例中 fun 的两个参数均为 new 表达式，实际执行时可以先为两个对象分配内存，再分别执行对象的构造函数，如果某个构造函数抛出异常，已分配的内存就得不到回收了。  
  
保证一次内存分配对应一个构造函数可解决这种问题：
```
auto a(shared_ptr<T>(new T));   // Compliant
auto b(shared_ptr<T>(new T));   // Compliant
fun(a, b);
```
这样即使构造函数抛出异常也会自动回收已分配的内存。  
  
更好的方法是避免显式资源分配：
```
fun(
    make_shared<T>(),
    make_shared<T>()    // Compliant, safe and brief
);
```
用 make\_shared、make\_unique 等函数代替 new 运算符可有效规避这种问题。
<br/>
<br/>

#### 参考
C++ Core Guidelines R.13  
<br/>
<br/>

### <span id="ID_copiedStream">▌R2.17 流式资源对象不应被复制</span>

ID_copiedStream&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

FILE 等流式对象不应被复制，如果存在多个副本会造成数据不一致的问题。  
  
示例：
```
FILE f;
FILE* fp = fopen(path, "r");

f = *fp;                      // Non-compliant
memcpy(fp, &f, sizeof(*fp));  // Non-compliant
```
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 7.19.3(6)  
ISO/IEC 9899:2011 7.21.3(6)  
<br/>

#### 参考
MISRA C 2012 22.5  
<br/>
<br/>

### <span id="ID_stackAllocation">▌R2.18 避免使用在栈上分配内存的函数</span>

ID_stackAllocation&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

alloca、\_\_builtin\_alloca 等在栈上分配内存的函数难以控制失败时的情况，尤其在循环中更不应使用这种函数。  
  
示例：
```
void fun(size_t size) {
    int* p = (int*)alloca(size);  // Non-compliant
    if (!p) {
        return;  // Invalid
    }
    ....
}
```
例中 alloca 函数在失败时会直接崩溃，不会返回空指针，对其返回值的检查是无效的，这种后果不可控的函数应避免使用。
<br/>
<br/>

#### 相关
ID_invalidNullCheck  
<br/>

#### 参考
CWE-770  
SEI CERT MEM05-C  
<br/>
<br/>

### <span id="ID_unnecessaryAllocation">▌R2.19 避免不必要的内存分配</span>

ID_unnecessaryAllocation&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

对单独的基本变量或只包含少量基本变量的对象不应使用动态内存分配。  
  
示例：
```
bool* pb = new bool;   // Non-compliant
char* pc = new char;   // Non-compliant
```
内存分配的开销远大于变量的直接使用，而且还涉及到回收问题，是得不偿失的。  
  
应改为：
```
bool b = false;   // Compliant
char c = 0;       // Compliant
```
用 new 分配数组时方括号被误写成小括号，或使用 unique\_ptr 等智能指针时遗漏了数组括号也是常见笔误，如：
```
int* pi = new int(32);            // Non-compliant
auto ui = make_unique<int>(32);   // Non-compliant
```
应改为：
```
int* pi = new int[32];              // Compliant
auto ui = make_unique<int[]>(32);   // Compliant
```
有时可能需要用指针指向一个变量，而指针为空时表示这个变量“不存在”，对于这种情况不妨用变量的特殊值表示变量的状态。
<br/>
<br/>

#### 相关
ID_dynamicAllocation  
<br/>
<br/>

### <span id="ID_dynamicAllocation">▌R2.20 避免动态内存分配</span>

ID_dynamicAllocation&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

标准库提供的动态内存分配方法，其算法或策略不在使用者的控制之内，很多细节是标准没有规定的，而且也是内存耗尽等问题的根源，有高可靠性要求的嵌入式系统应避免动态内存分配。  
  
在内存资源有限的环境中，由于难以控制具体的分配策略，很可能会导致已分配的空间用不上，未分配的空间不够用的情况。而在资源充足的环境中，也应尽量避免动态分配，如果能在栈上创建对象，就不应采用动态分配的方式，以提高效率并降低资源管理的复杂性。  
  
示例：
```
void foo() {
    std::vector<int> v;   // Non-compliant
    ....
}
```
例中 vector 容器使用了动态内存分配方法，容量的增长策略可能会导致内存空间的浪费，甚至使程序难以稳定运行。
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 7.20.3  
ISO/IEC 9899:2011 7.22.3  
<br/>

#### 参考
MISRA C 2004 20.4  
MISRA C 2012 21.3  
MISRA C++ 2008 18-4-1  
C++ Core Guidelines R.5  
<br/>
<br/>

### <span id="ID_nullDerefAllocRet">▌R2.21 判断资源分配函数的返回值是否有效</span>

ID_nullDerefAllocRet&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

malloc 等函数在分配失败时返回空指针，如果不加判断直接使用会导致标准未定义的行为。  
  
在有虚拟内存支持的平台中，正常的内存分配一般不会失败，但申请内存过多或有误时（如参数为负数）也会导致分配失败，而对于没有虚拟内存支持的或可用内存有限的嵌入式系统，检查分配资源是否成功是十分重要的，所以本规则应该作为代码编写的一般性要求。  
  
库的实现更需要注意这一点，如果库在分配失败时直接崩溃或不加说明地结束进程，相当于干扰了主程序的决策权，很可能会造成难以排查的问题，对于有高可靠性要求的软件，在极端环境中的行为是需要明确设定的。  
  
示例：
```
void foo(size_t n) {
    char* p = (char*)malloc(n);
    p[n - 1] = '\0';              // Non-compliant, check ‘p’ first
    ....
}
```
示例代码未检查 p 的有效性便直接使用是不符合要求的。
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 7.20.3(1)  
ISO/IEC 9899:2011 7.22.3(1)  
<br/>

#### 参考
CWE-476  
CWE-252  
<br/>
<br/>

### <span id="ID_forbidMallocAndFree">▌R2.22 C++ 代码中禁用 C 内存管理函数</span>

ID_forbidMallocAndFree&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: resource warning

<hr/>

在 C\+\+ 代码中不应使用 malloc、free 等 C 内存管理函数，应使用对象化管理方法。  
  
示例：
```
void foo(size_t n) {
    int* p = (int*)malloc(n * sizeof(int));  // Unsafe and verbose
    ....
    free(p);
}
```
应改为：
```
void foo(size_t n) {
    auto p = make_unique<int[]>(n);  // Safe and brief
    ....
}
```
<br/>
<br/>

#### 相关
ID_ownerlessResource  
<br/>

#### 参考
C++ Core Guidelines R.10  
<br/>
<br/>

## <span id="precompile">3. Precompile</span>

### <span id="precompile.include">3.1 Include</span>

### <span id="ID_illFormedInclude">▌R3.1.1 include 指令应符合标准格式</span>

ID_illFormedInclude&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: precompile error

<hr/>

\#include 后只应为 < 头文件路径 > 或 " 头文件路径 "，否则会导致标准未定义的行为。  
  
示例：
```
#include <string.h>         // Compliant
#include "string.h"         // Compliant 

#define HEADER "string.h"
#include HEADER             // Compliant

#include stdlib.h           // Non-compliant, undefined behavior
```
例中对 string.h 的引用符合标准，而对 stdlib.h 的引用会导致标准未定义的行为。  
  
注意，由引号标识的头文件路径并非字符串常量，不应对其使用字符串常量的特性，如：
```
#include "stdlib" ".h"      // Non-compliant, implementation defined
```
是否会将引号中的内容连接成一个路径是由实现定义的，这种代码是不可移植的。  
  
另外，如下形式的代码也是不符合标准的：
```
#include L"foo"             // Non-compliant
#include u"bar"             // Non-compliant
#include U"baz"             // Non-compliant
#include R"(..\foo\bar)"    // Non-compliant
```
<br/>
<br/>

#### 相关
ID_nonStandardCharInHeaderName  
<br/>

#### 依据
ISO/IEC 14882:2011 2.9  
ISO/IEC 14882:2011 16.2(4)-undefined  
ISO/IEC 14882:2011 16.2(4)-implementation  
<br/>

#### 参考
MISRA C 2004 19.3  
MISRA C 2012 20.3  
MISRA C++ 2008 16-2-6  
<br/>
<br/>

### <span id="ID_nonStandardCharInHeaderName">▌R3.1.2 include 指令中禁用不合规的字符</span>

ID_nonStandardCharInHeaderName&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: precompile warning

<hr/>

字母、数字、下划线、点号之外的字符可能与文件系统存在冲突，也可能导致标准未定义的行为，不应出现在头文件和相关目录名称中。  
  
示例：
```
#include <"foo">        // Non-compliant
#include <foo*>         // Non-compliant
#include <foo'bar>      // Non-compliant

#include <foo>          // Compliant
#include <foo.h>        // Compliant
#include <foo_bar>      // Compliant
```
可以用 / 作为路径分隔符，但不应出现  // 或 /\*，  如：
```
#include <foo//bar.h>   // Non-Compliant
#include <foo/*bar.h>   // Non-Compliant
```
另外，由于某些平台的文件系统不区分路径大小写，建议头文件名称只使用小写字母以减少移植类问题。
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 6.4.7(3)-undefined  
ISO/IEC 9899:2011 6.4.7(3)-undefined  
ISO/IEC 14882:2003 2.8(2)-undefined  
ISO/IEC 14882:2011 2.9(2)-implementation  
<br/>

#### 参考
MISRA C 2004 19.2  
MISRA C 2012 20.2  
MISRA C++ 2008 16-2-4  
<br/>
<br/>

### <span id="ID_forbidBackslashInHeaderName">▌R3.1.3 include 指令中不应使用反斜杠</span>

ID_forbidBackslashInHeaderName&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: precompile warning

<hr/>

如果在 include 指令中使用反斜杠，程序的行为在 C 和 C\+\+03 标准中是未定义的，在 C\+\+11 标准中是由实现定义的。  
  
示例：
```
#include <foo\bar.h>     // Non-compliant
#include "foo\\bar.h"    // Non-compliant

#include <foo/bar.h>     // Compliant
```
有高可移植性要求的代码应避免使用反斜杠。
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 6.4.7(3)-undefined  
ISO/IEC 9899:2011 6.4.7(3)-undefined  
ISO/IEC 14882:2003 2.8(2)-undefined  
ISO/IEC 14882:2011 2.9(2)-implementation  
<br/>

#### 参考
MISRA C++ 2008 16-2-5  
<br/>
<br/>

### <span id="ID_forbidAbsPathInHeaderName">▌R3.1.4 include 指令中不应使用绝对路径</span>

ID_forbidAbsPathInHeaderName&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: precompile warning

<hr/>

绝对路径使代码过分依赖编译环境，意味着项目的编译设置不完善，应使用相对路径。  
  
示例：
```
#include "C:\\foo\\bar.h"   // Non-compliant
#include "/foo/bar.h"       // Non-compliant
```
<br/>
<br/>
<br/>

### <span id="ID_forbiddenHeader">▌R3.1.5 禁用不合规的头文件</span>

ID_forbiddenHeader&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: precompile warning

<hr/>

无意义的，行为不明确的或有不良副作用的头文件应禁用。  
  
示例：
```
#include <tgmath.h>   // Non-compliant
#include <setjmp.h>   // Non-compliant
#include <stdbool.h>  // Non-compliant in C++
```
tgmath.h 和 ctgmath 会使用语言标准之外的技术实现某种重载效果，而且其中的部分函数名称会干扰其他标准库中的名称，setjmp.h 和 csetjmp 则包含危险的过程间跳转函数。  
  
iso646.h、stdalign.h 以及 stdbool.h 对于 C\+\+ 语言来说没有意义，在 C\+\+ 代码中不应使用。  
  
stdio.h、signal.h、time.h、fenv.h 等头文件对于有高可靠性要求的软件系统也不建议使用，这些头文件含有较多标准未声明、未定义或由实现定义的内容。  
  
审计工具不妨通过配置设定不合规头文件的名称：
```
[ID_forbiddenHeader]
tgmath.h|ctgmath=May result in undefined behaviour
setjmp.h|csetjmp=Forbidden header
```
表示将 tgmath.h、ctgmath、setjmp.h、csetjmp 设为不合规头文件，如发现代码中有 tgmath.h，则报告“May result in undefined behaviour”，如发现代码中有 setjmp.h 或 csetjmp ，则报告“Forbidden header”。
<br/>
<br/>

#### 配置
详见说明  
<br/>

#### 依据
ISO/IEC 14882:2017 C.5.1(4)  
<br/>

#### 参考
MISRA C 2012 21.5  
MISRA C 2012 21.10  
MISRA C 2012 21.11  
MISRA C 2012 21.12  
MISRA C++ 2008 18-7-1  
MISRA C++ 2008 18-0-4  
MISRA C++ 2008 27-0-1  
<br/>
<br/>

### <span id="ID_forbidCHeaderInCpp">▌R3.1.6 C++ 代码不应引用 C 头文件</span>

ID_forbidCHeaderInCpp&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: precompile warning

<hr/>

在 C\+\+ 代码中应使用 C\+\+ 标准头文件，stdio.h、stdlib.h 等 C 语言头文件不在 C\+\+ 标准之内，应改用 cstdio、cstdlib 等 C\+\+ 标准头文件。  
  
C 标准头文件均有对应的 C\+\+ 版本，C\+\+ 版本提供了更适合 C\+\+ 代码的命名空间、模板以及函数重载等功能。另外，按 C\+\+ 惯例，语言相关的标准头文件无扩展名，自定义及平台相关的以 .h 为扩展名，遵循统一的命名规范也有必要的。  
  
示例：
```
#include <assert.h>    // Non-compliant, use <cassert>
#include <ctype.h>     // Non-compliant, use <cctype>
#include <errno.h>     // Non-compliant, use <cerrno>
#include <float.h>     // Non-compliant, use <cfloat>
#include <limits.h>    // Non-compliant, use <climits>
#include <locale.h>    // Non-compliant, use <clocale>
#include <math.h>      // Non-compliant, use <cmath>
#include <setjmp.h>    // Non-compliant, use <csetjmp>
#include <signal.h>    // Non-compliant, use <csignal>
#include <stdarg.h>    // Non-compliant, use <cstdarg>
#include <stddef.h>    // Non-compliant, use <cstddef>
#include <stdio.h>     // Non-compliant, use <cstdio>
#include <stdlib.h>    // Non-compliant, use <cstdlib>
#include <string.h>    // Non-compliant, use <cstring>
#include <time.h>      // Non-compliant, use <ctime>
#include <wchar.h>     // Non-compliant, use <cwchar>
#include <wctype.h>    // Non-compliant, use <cwctype>
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 D.5  
ISO/IEC 14882:2011 D.5  
ISO/IEC 14882:2017 D.5  
<br/>

#### 参考
MISRA C++ 2008 18-0-1  
<br/>
<br/>

### <span id="precompile.macro-definition">3.2 Macro-definition</span>

### <span id="ID_macro_badName">▌R3.2.1 宏应遵循合理的命名方式</span>

ID_macro_badName&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: precompile suggestion

<hr/>

宏的名称应采用全大写字母的形式，非宏名称则应包含小写字母。  
  
宏用于文本处理，不受语言规则限制，易被误用，在命名方式上将其与普通代码分开可引起使用者或维护者的注意，有助于规避错误。  
  
本规则是 ID\_badName 的特化。  
  
示例：
```
#define word_size 8   // Non-compliant, like a normal variable
#define WORD_SIZE 8   // Compliant
```
<br/>
<br/>

#### 配置
maxWordLength：连续无大小写变化的字符个数上限，超过则报出  
<br/>

#### 相关
ID_badName  
<br/>

#### 参考
C++ Core Guidelines ES.32  
C++ Core Guidelines ES.9  
<br/>
<br/>

### <span id="ID_macro_defineReserved">▌R3.2.2 不可定义具有保留意义的宏名称</span>

ID_macro_defineReserved&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

重新定义已有特殊用途的名称，会使代码陷入难以维护的境地，也会导致标准未定义的行为。  
  
C\+\+ 标准指明不可重新定义的宏有：
```
__cplusplus、__TIME__、__DATE__、__FILE__、__ LINE__、
__STDC__、__STDC_HOSTED__、__STDCPP_THREADS__、
__STDC_MB_MIGHT_NEQ_WC__、__STDC_VERSION__、
__STDC_ISO_10646__、__STDCPP_STRICT_POINTER_SAFETY__
```
除此之外，平台、环境、框架相关的宏也不应在代码中重新定义。  
  
以下划线开头的名称用于表示标准库或系统的内部名称，自定义名称不应以下划线开头。  
  
示例：
```
#define defined  // Non-compliant
```
不可重定义关键字。  

```
#define __GNUC__ 1  // Non-compliant
```
标识编译器或平台的宏不可在代码中写死。  

```
#define NDEBUG 0  // Non-compliant
```
编译优化相关的宏不可在代码中写死。  

```
#define assert(x) ((void)x)  // Non-compliant
```
标准库中的宏不应重新实现。  

```
#define new new(std::nothrow)  // Non-compliant
```
应实现为调用 new(std::nothrow) 的函数。  
  
审计工具不妨通过配置设定保留名称：
```
[ID_macro_defineReserved]
keywordAsReserved=true
NULL|NDEBUG|EOF=Reserved name should not be defined or undefined
```
表示将 NULL、NDEBUG、EOF 为设为保留名称，当在代码中发现定义了相同名称的宏时则提示“Reserved name should not be redefined or undefined”。  
配置项 keywordAsReserved 为 true 表示关键字也作为保留名称，否则只认为用户设置的名称为保留名称。
<br/>
<br/>

#### 配置
详见说明  
<br/>

#### 相关
ID_macro_undefReserved  
ID_reservedName  
<br/>

#### 依据
ISO/IEC 9899:2011 7.1.3(2)-undefined  
ISO/IEC 14882:2011 16.8(4)-undefined  
<br/>

#### 参考
MISRA C 2012 21.1  
MISRA C 2012 20.4  
MISRA C++ 2008 17-0-1  
<br/>
<br/>

### <span id="ID_macro_undefReserved">▌R3.2.3 不可取消定义具有保留意义的宏名称</span>

ID_macro_undefReserved&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

取消定义已有特殊用途的宏名称，会使代码陷入难以维护的境地，也会导致标准未定义的行为。  
  
C\+\+ 标准指明不可取消定义的宏有：
```
__cplusplus、__TIME__、__DATE__、__FILE__、__ LINE__、
__STDC__、__STDC_HOSTED__、__STDCPP_THREADS__、
__STDC_MB_MIGHT_NEQ_WC__、__STDC_VERSION__、
__STDC_ISO_10646__、__STDCPP_STRICT_POINTER_SAFETY__
```
除此之外，平台、环境、框架相关的宏也不可被取消定义。  
  
示例：
```
#undef __LINE__     // Non-compliant
#undef __cplusplus  // Non-compliant
#undef NDEBUG       // Non-compliant
#undef _WIN64       // Non-compliant
#undef __unix__     // Non-compliant
```
审计工具不妨通过配置设定保留名称：
```
[ID_macro_undefReserved]
keywordAsReserved=true
NULL|NDEBUG|EOF=Reserved name should not be defined or undefined
```
表示将 NULL、NDEBUG、EOF 为设为保留名称，当在代码中发现 undef 相同名称的宏时则提示“Reserved name should not be redefined or undefined”。  
配置项 keywordAsReserved 为 true 表示关键字也作为保留名称，否则只认为用户设置的名称为保留名称。
<br/>
<br/>

#### 相关
ID_macro_defineReserved  
ID_reservedName  
<br/>

#### 依据
ISO/IEC 9899:2011 7.1.3(3)-undefined  
ISO/IEC 14882:2011 16.8(4)-undefined  
<br/>

#### 参考
MISRA C 2012 21.1  
MISRA C 2012 20.5  
MISRA C++ 2008 17-0-1  
MISRA C++ 2008 16-0-3  
<br/>
<br/>

### <span id="ID_macro_expNotEnclosed">▌R3.2.4 可作为子表达式的宏定义应该用括号括起来</span>

ID_macro_expNotEnclosed&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

由于宏只做文本处理，不考虑运算符优先级等问题，可作为子表达式的宏定义应该用括号括起来，否则很可容易产生意料之外的错误。  
  
示例：
```
#define ABS(x) (x) < 0? -(x): (x)  // Non-compliant
```
设 a 为变量，如果按如下使用方式：
```
a = ABS(a) + 1;
```
则相当于：
```
a = (a) < 0? -(a): (a) + 1;
```
这显然会造成意料之外的结果，所以 ABS 的定义应改为：
```
#define ABS(x) ((x) < 0? -(x): (x))  // Compliant
```
<br/>
<br/>

#### 参考
CWE-783  
MISRA C 2004 19.10  
MISRA C 2012 20.7  
<br/>
<br/>

### <span id="ID_macro_paramNotEnclosed">▌R3.2.5 与运算符相关的宏参数应该用括号括起来</span>

ID_macro_paramNotEnclosed&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

由于宏只做文本处理，不考虑运算符优先级等问题，故应将宏参数用括号括起来，否则很可容易产生意料之外的错误。  
  
示例：
```
#define SUM(a, b) (a + b)  // Non-compliant
```
应改为：
```
#define SUM(a, b) ((a) + (b))  // Compliant
```
<br/>
<br/>

#### 参考
CWE-783  
MISRA C++ 2008 16-0-6  
<br/>
<br/>

### <span id="ID_macro_stmtNotEnclosed">▌R3.2.6 由多个语句组成的宏定义应该用 do-while(0) 括起来</span>

ID_macro_stmtNotEnclosed&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

可以作为一条语句使用的宏，且宏包含多个并列子句时，应该用“do {”和 “} while(0)”括起来，否则易造成作用域的混乱。  
  
示例：
```
#define SWAP(a, b)\
    a ^= b; b ^= a; a ^= b   // Non-compliant
```
如果按如下使用方式：
```
if (x > y)
    SWAP(x, y);
```
展开后 b ^= a; a ^= b; 不在 if 语句的范围内，应改为：
```
#define SWAP(a, b) {\          // Compliant
    a ^= b; b ^= a; a ^= b;\
}
```
更进一步地，建议使用 do\-while(0) 结构：
```
#define SWAP(a, b) do {\       // Good
    a ^= b; b ^= a; a ^= b;\
} while(0)
```
这样在使用宏时必须以分号结尾，否则无法通过编译，使宏在使用风格上与函数相同，易于阅读。
<br/>
<br/>

#### 相关
ID_if_scope  
ID_while_scope  
ID_for_scope  
<br/>

#### 参考
CWE-483  
<br/>
<br/>

### <span id="ID_macro_complexConcat">▌R3.2.7 在宏定义中由 # 修饰的参数后不应出现 ##</span>

ID_macro_complexConcat&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

不同编译器对 \# 和 \#\# 的优先级有不同的实现，在有可移植性要求的代码中不应嵌套使用，而且 \#\# 连接的单词数量不应超过两个。  
  
示例：
```
#define M0(a, b) # a ## b          // Non-compliant
#define M1(a, b, c) a ## #b ## c   // Non-compliant

#define M2(a) #a                   // Compliant
#define M3(a, b) M1(a ## b)        // Compliant
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 16.3.2(2)-unspecified  
ISO/IEC 14882:2003 16.3.3(3)-unspecified  
ISO/IEC 14882:2011 16.3.2(2)-unspecified  
ISO/IEC 14882:2011 16.3.3(3)-unspecified  
ISO/IEC 14882:2017 19.3.2(2)-unspecified  
ISO/IEC 14882:2017 19.3.3(3)-unspecified  
<br/>

#### 参考
MISRA C 2004 19.12  
MISRA C 2012 20.11  
MISRA C++ 2008 16-3-1  
<br/>
<br/>

### <span id="ID_macro_tooManyParams">▌R3.2.8 宏参数数量应在规定范围之内</span>

ID_macro_tooManyParams&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

宏参数数量过多意味着宏功能过于复杂，不利于调试，应改为函数。
<br/>
<br/>

#### 配置
maxParamCount：参数个数上限，超过则报出  
<br/>

#### 相关
ID_tooManyParams  
<br/>
<br/>

### <span id="ID_macro_const">▌R3.2.9 不应使用宏定义常量</span>

ID_macro_const&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: precompile suggestion

<hr/>

宏用于文本处理，不受作用域等语言规则限制，不应使用宏实现常量等语言层面的概念。  
  
示例：
```
namespace U {
    #define PI 3.14F  // Non-compliant
}

namespace V {
    #define PI 3.14159L  // Non-compliant
}

namespace W {
    void fun(double PI);  // Disturbed
}
```
例中宏 PI 不受命名空间的限制，第二个宏定义会覆盖第一个宏定义，而且会干扰其他作用域中相同的名称。  
  
应改为：
```
namespace U {
    const float PI = 3.14F;  // Compliant
}

namespace V {
    const long double PI = 3.14159L;  // Compliant
}
```
为了避免混乱，建议宏只作为 \#if、\#elif 等指令的条件，不参与具体的功能实现。
<br/>
<br/>

#### 相关
ID_macro_typeid  
ID_macro_function  
<br/>

#### 参考
C++ Core Guidelines ES.31  
C++ Core Guidelines Enum.1  
<br/>
<br/>

### <span id="ID_macro_typeid">▌R3.2.10 不应使用宏定义类型</span>

ID_macro_typeid&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: precompile suggestion

<hr/>

宏用于文本处理，不受作用域等语言规则限制，不应使用宏实现类型等语言层面的概念。  
  
示例：
```
namespace U {
    #define Type int  // Non-compliant
}

namespace V {
    #define Type long  // Non-compliant
}

void foo(Type);  // Unreliable
```
例中 Type 的最终定义是 long，第二个宏定义会覆盖第一个宏定义，这显然是不可靠的。 
<br/>
<br/>

#### 相关
ID_macro_sideEffectArgs  
ID_macro_const  
ID_macro_function  
<br/>

#### 参考
C++ Core Guidelines ES.30  
<br/>
<br/>

### <span id="ID_macro_function">▌R3.2.11 可由函数实现的功能不应使用宏实现</span>

ID_macro_function&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: precompile suggestion

<hr/>

宏用于文本处理，不受作用域、参数传递、重载等语言规则限制，可由函数实现的功能不应使用宏实现。  
  
示例：
```
#define SUM(a, b) ((a) + (b))            // Non-compliant
#define SUM(a, b, c) ((a) + (b) + (c))   // Non-compliant

int foo(int a, int b) {
    return SUM(a, b);     // Error
}
```
例中宏 SUM 意在获取参数的和，但宏无法被重载，最终只有一个宏被定义， foo 函数中的宏展开会造成错误。
<br/>
<br/>

#### 相关
ID_macro_sideEffectArgs  
ID_macro_const  
ID_macro_typeid  
<br/>

#### 参考
C++ Core Guidelines ES.31  
MISRA C 2004 19.7  
MISRA C 2012 Dir 4.9  
MISRA C++ 2008 16-0-4  
<br/>
<br/>

### <span id="ID_macro_misspelling">▌R3.2.12 宏名称中不应存在拼写错误</span>

ID_macro_misspelling&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: precompile suggestion

<hr/>

宏的名称不应存在拼写错误，尤其是供他人调用的宏，错误拼写会使代码的使用者对代码的质量产生疑虑，应认真对待。  
  
示例：
```
#define FRIST(p) p->first()  // Non-compliant, should be FIRST
```
例中 FIRST 被误写成了 FRIST。
<br/>
<br/>

#### 相关
ID_misspelling  
ID_literal_misspelling  
<br/>
<br/>

### <span id="precompile.macro-usage">3.3 Macro-usage</span>

### <span id="ID_macro_sideEffectArgs">▌R3.3.1 宏参数不应有副作用</span>

ID_macro_sideEffectArgs&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

当宏参数有“[副作用（side effect）](https://en.wikipedia.org/wiki/Side_effect_(computer_science))”时，如果宏定义中没有或多次引用到该参数，会导致意料之外的错误。  
  
示例：
```
#define I(a)
#define M(a) ((a) + (a))

int foo(int& a) {
    return M(++a);   // Non-compliant, returns ‘((++a) + (++a))’
}

void bar(int& a) {
    I(a--);          // Non-compliant, does nothing
}
```
例中 M 和 I 看起来像是函数调用，而展开后的结果却在意料之外。
<br/>
<br/>

#### 相关
ID_sideEffectAssertion  
ID_macro_function  
<br/>

#### 参考
SEI CERT PRE31-C  
<br/>
<br/>

### <span id="ID_macro_insufficientArgs">▌R3.3.2 宏的实参个数不可小于形参个数</span>

ID_macro_insufficientArgs&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

宏的实参个数小于形参个数是不符合 C/C\+\+ 标准的，参数个数不一致必然意味着某种错误，然而在某些编译环境下却可以通过编译。  
  
示例：
```
#define M(a, b, c)  a ## b ## c

const char* foo() {
    return M("x", "y");  // Non-compliant
}
```
早期标准（ISO 9899:1990）对这种情况没有明确定义，后续标准对其进行了约束，但 MSVC 等编译器至今仍不把这种问题视作编译错误，需要特别注意。
<br/>
<br/>

#### 相关
ID_macro_redundantArgs  
<br/>

#### 参考
CWE-628  
MISRA C 2004 19.8  
<br/>
<br/>

### <span id="ID_macro_redundantArgs">▌R3.3.3 宏的实参个数不可大于形参个数</span>

ID_macro_redundantArgs&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

宏的实参个数大于形参个数是不符合 C/C\+\+ 标准的，参数个数不一致必然意味着某种错误，然而在某些编译环境下却可以通过编译。  
  
示例：
```
#define M(a, b, c)  a ## b ## c

const char* foo() {
    return M("a", "b", "c", "d");  // Non-compliant
}
```
<br/>
<br/>

#### 相关
ID_macro_insufficientArgs  
<br/>

#### 参考
CWE-628  
<br/>
<br/>

### <span id="ID_incompleteVaMacros">▌R3.3.4 va_start 或 va_copy 应配合 va_end 使用</span>

ID_incompleteVaMacros&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

可变参数列表相关的 va\_start 或 va\_copy 和 va\_end 应在同一函数中使用，否则会导致标准未定义的行为。  
  
示例：
```
void foo(const char* s, ...) {  // Non-compliant, missing ‘va_end(vl);’
    va_list vl;
    va_start(vl, s);
    for (const char* p = s; *p; p++) {
        ....
    }
}
```
示例代码应在返回前使用 va\_end。
<br/>
<br/>

#### 相关
ID_forbidVariadicFunction  
<br/>

#### 依据
ISO/IEC 9899:2011 7.16.1.3(2)-undefined  
<br/>
<br/>

### <span id="ID_badVaArgType">▌R3.3.5 va_arg 的类型参数应符合要求</span>

ID_badVaArgType&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: precompile error

<hr/>

对于 va\_arg 的类型参数，如果：  
 - 名称后加 \* 号不能表示指针类型  
 - 与默认参数提升后的类型不兼容  
 - 与下一个参数的类型不兼容，或没有实际的下一个参数  
  
会导致标准未定义的行为。  
  
以下类型不可作为 av\_arg 的参数：
```
bool、_Bool、
char、signed char、unsigned char、char16_t、
float、
short、unsigned short、signed short、
short int、signed short int、unsigned short int
```
这些类型的参数在传入可变参数列表时，会被提升为 int、unsigned int、double 等类型，va\_arg 如果再提升前的类型解析参数的值就会造成错误，参见“[默认参数提升（default argument promotion）](https://en.cppreference.com/w/cpp/language/variadic_arguments#Default_conversions)”机制。  
  
另外，C\+\+ 代码中非 POD 类型也不可作为 va\_arg 的参数，参见 ID\_nonPODVariadicArgument。  
  
示例：
```
void foo(int n, ...) {
    va_list vl;
    va_start(vl, n);
    for (int i = 0; i < n; i++) {
        char c = va_arg(vl, char);   // Non-compliant, use ‘va_arg(vl, int)’ instead
        ....
    }
    va_end(vl);
}
```
<br/>
<br/>

#### 相关
ID_badParmN  
ID_nonPODVariadicArgument  
ID_forbidVariadicFunction  
<br/>

#### 依据
ISO/IEC 9899:2011 7.16.1.1(2)-undefined  
<br/>

#### 参考
SEI CERT EXP47-C  
<br/>
<br/>

### <span id="ID_deprecatedOffsetof">▌R3.3.6 在 C++ 代码中不应使用宏 offsetof</span>

ID_deprecatedOffsetof&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: precompile suggestion

<hr/>

宏 offsetof 很难适用于具有 C\+\+ 特性的类，在 C\+\+ 代码中不应使用。  
  
如果 offsetof 用于：  
 - 非“[standard layout](https://en.cppreference.com/w/cpp/named_req/StandardLayoutType)”类型  
 - 计算静态成员或成员函数的偏移量  
  
会导致标准未定义的行为。  
  
示例：
```
struct A {
    int i;
    virtual int f();
};

int foo() {
    return offsetof(A, i);  // Non-compliant, undefined behavior
}

struct B {
    static int i;
    int f();
};

int bar() {
    return offsetof(B, i);  // Non-compliant, undefined behavior
}

int baz() {
    return offsetof(B, f);  // Non-compliant, undefined behavior
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 18.2(4)-undefined  
<br/>

#### 参考
SEI CERT EXP59-CPP  
<br/>
<br/>

### <span id="precompile.directive">3.4 Directive</span>

### <span id="ID_missingHeaderGuard">▌R3.4.1 头文件不应缺少守卫</span>

ID_missingHeaderGuard&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

以 .h 或 .hpp 为扩展名的头文件应包含头文件守卫。  
  
示例：
```
// Header file foo.h
#ifndef LIBRARY_FOO_H
#define LIBRARY_FOO_H
....
#endif
```
例中 foo.h 是“Library”模块中的头文件，宏 LIBRARY\_FOO\_H 即可作为它的守卫，保证头文件被重复引入也不会出现问题，守卫名称不可有重复，建议守卫名称遵循“模块名\_文件名”的形式。  
  
\#pragma once 指令也可作为头文件守卫，但并不是 C/C\+\+ 的标准方式，只是多数编译器均有支持。这种方式由编译器维护一个列表，引入头文件时，如果发现文件中有 \#pragma once 指令就将文件路径加入列表，当这个文件再次被 include 时便不会加载，而宏守卫的方式仍然要对文件进行预编译，所以 \#pragma once 方式在编译效率上会更高一些。  
  
宏守卫用宏名区分头文件，所以不能有重复。宏的引入可以使相关设定更灵活，比如声明头文件之间的依赖或排斥关系，如果 bar.h 依赖 foo.h，在 \#include "bar.h" 之前必须 \#include "foo.h"，可在 bar.h 中设置：
```
// Header file bar.h
#ifndef LIBRARY_FOO_H
#error foo.h should be included first
#endif
```
这样如果不满足条件无法通过编译。  
  
本规则建议使用宏守卫的方式，但 \#pragma once 方法也是惯用写法，不妨通过配置项决定其是否合规。
<br/>
<br/>

#### 配置
allowPragmaOnce：为 true 时 #pragma once 也可作为符合要求的头文件守卫  
<br/>

#### 参考
C++ Core Guidelines SF.8  
MISRA C 2004 19.15  
MISRA C++ 2008 16-2-3  
<br/>
<br/>

### <span id="ID_illFormedDirective">▌R3.4.2 不应出现非标准格式的预编译指令</span>

ID_illFormedDirective&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

非标准格式的预编译指令往往意味着错误，也会导致标准未定义的行为。  
  
需注意：  
 - defined 只应作用于宏名称或括号括起来的宏名称  
 - defined 不应出现在宏定义中  
 - \#if、\#elif 之后应为正确的常量表达式  
 - \#ifdef、\#ifndef 之后只应为宏名称  
 - \#else、\#endif 之后应直接换行  
 - \#line 之后应接整数常量，或整数常量和文件名称  
 - \#line 指定的行号应在有效范围内  
 - \#line 不应出现在非自动生成的代码中  
  
示例：
```
#if defined M            // Compliant
#if defined(M)           // Compliant
#if defined(M == 0)      // Non-compliant, undefined behaviour

#define DEFINED defined  // Non-compliant
#if DEFINED M            // Undefined behaviour

#line 0                  // Non-compliant, invalid line number
#line 4294967295         // Non-compliant, line number too large
```
例中作用于比较表达式的 defined 和 \#if 条件中由宏展开产生的 defined 均会导致未定义的行为，由 \#line 指定的行号应大于 0 且小于 2147483648（按 C\+\+03 标准则应小于 32768），否则也会导致未定义的行为。  
  
又如：
```
#define M 2

int foo() {
    int x = 0;
#ifdef M
        x = M;
#elif            // Non-compliant, use ‘#else’ instead
        x = 1;
#endif M         // Non-compliant, remove ‘M’
    return x;
}
```
这种代码是不符合标准的，但可被某些编译器接受，应避免。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 6.10(1)  
ISO/IEC 9899:2011 6.10.1(4)-undefined  
ISO/IEC 14882:2011 16.1(4)-undefined  
ISO/IEC 14882:2011 16.2(4)-undefined  
ISO/IEC 14882:2011 16.4(3)-undefined  
ISO/IEC 14882:2011 16.4(5)-undefined  
<br/>

#### 参考
MISRA C++ 2008 16-0-7  
MISRA C++ 2008 16-0-8  
MISRA C++ 2008 16-1-1  
<br/>
<br/>

### <span id="ID_nonStdDirective">▌R3.4.3 不应使用非标准预编译指令</span>

ID_nonStdDirective&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

使用非标准预编译指令并非是问题的正规解决方法，且易造成代码移植方面的隐患。  
  
示例：
```
#import "foo"          // Non-compliant
#include_next "bar.h"  // Non-compliant
#unknwon               // Non-compliant
```
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 6.10(1)  
ISO/IEC 9899:2011 6.10(1)  
<br/>

#### 参考
MISRA C 2004 19.16  
MISRA C 2012 20.13  
<br/>
<br/>

### <span id="ID_directiveInMacroArgument">▌R3.4.4 宏的参数列表中不应出现预编译指令</span>

ID_directiveInMacroArgument&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

如果预编译指令出现在宏的参数列表中，指令和参数的处理顺序将是混乱的，导致标准未定义的行为。  
  
示例：
```
#define PRINT(s) printf(#s)

PRINT(
#ifdef MAC      // Non-compliant, undefined behavior
    rabbit
#else           // Non-compliant
    hamster
#endif          // Non-compliant
);
```
结果可能是打印出 hamster，也可能是 \#ifdef MAC rabbit \#else hamster \#endif 这种怪异的结果。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 16.3(10)-undefined  
ISO/IEC 14882:2011 16.3(11)-undefined  
<br/>

#### 参考
MISRA C++ 2008 16-0-5  
<br/>
<br/>

### <span id="ID_warningDisabled">▌R3.4.5 对编译警告的屏蔽应慎重</span>

ID_warningDisabled&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: precompile suggestion

<hr/>

编译器一般允许使用预编译指令屏蔽某些编译警告，但对于反映风险或安全问题的警告不应屏蔽。  
  
示例：
```
#ifdef _MSC_VER
#pragma warning(disable: 4172)  // Non-compliant
#elif defined __GNUC__
#pragma GCC diagnostic ignored "-Wreturn-local-addr"  // Non-compliant
#endif
```
示例代码屏蔽了 Visual Studio C4172 和 GCC \-Wreturn\-local\-addr 对应的警告，当局部变量的地址被返回时编译器不会给出警告，但这种警告是不应该被屏蔽的，详见 ID\_localAddressFlowOut。  
  
本规则集合提到的部分问题编译器也可以给出警告，这种警告均不应被屏蔽。
<br/>
<br/>

#### 相关
ID_warningDefault  
<br/>
<br/>

### <span id="ID_warningDefault">▌R3.4.6 在高级别的警告设置下编译</span>

ID_warningDefault&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: precompile suggestion

<hr/>

编译器一般允许设定编译警告的级别，级别越高关注的问题就越多，也可以将警告设为错误，当有警告产生时停止编译，建议代码在高级别的警告设置下编译。  
  
应避免代码中出现 \#pragma warning(default:...) 等指令，这种指令将警告级别设为默认，可能与整个项目的设置不一致，如果一定要使用，应改用 \#pragma warning(pop) 方式。  
  
示例：
```
#pragma warning(disable:4706)
#include "somecode"
#pragma warning(default:4706)  // Non-compliant
```
示例代码在导入某些代码之前将代号为 4706 的警告屏蔽，之后又将其设为默认级别，首先要关注 4706 是否应该被屏蔽，还要关注如果将其设为默认是否与整个项目的设置有冲突。  
  
应改为：
```
#pragma warning(push)
#pragma warning(disable:4706)
#include "somecode"
#pragma warning(pop)  // Compliant
```
改用这种方式之后不必再关注是否与整个项目的设置有冲突了。
<br/>
<br/>

#### 相关
ID_warningDisabled  
<br/>

#### 参考
SEI CERT MSC00-C  
<br/>
<br/>

### <span id="precompile.comment">3.5 Comment</span>

### <span id="ID_specialComment">▌R3.5.1 关注 TODO、FIXME、XXX、BUG 等特殊注释</span>

ID_specialComment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

TODO、FIXME、XXX、BUG 等特殊注释表示代码中存在问题，这种问题不应被遗忘，应有计划地予以解决。  
  
及时记录问题是一种好习惯，而且最好有署名和日期。  
  
示例：
```
void foo() {
    /*
     * Some plans...         // Bad, easy to forget
     */
}

void foo() {
    /* TODO:
     * Some plans...  -- my name, date     // Good
     */
}
```
审计工具不妨定期搜索这些关键词对应的注释，以供相关人员核对问题解决情况。
<br/>
<br/>

#### 参考
CWE-546  
<br/>
<br/>

### <span id="ID_nestedComment">▌R3.5.2 注释不可嵌套</span>

ID_nestedComment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

嵌套的 /\*...\*/ 注释不符合标准，/\* 与 \*/ 之间不应出现 /\*，某些编译器可以接受嵌套，但不具备可移植性。  
  
示例：  

```
/*                         // #1
    /*                     // #2, Non-compliant
    nested comments
     */                    // #3
*/                         // #4, Non-compliant
```
根据标准，`#1` 处的 /\* 与 `#3` 处的 \*/ 匹配，而 `#4` 处的 \*/ 处于失配状态。
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 6.4.9(1)  
ISO/IEC 9899:2011 6.4.9(1)  
<br/>

#### 参考
MISRA C 2004 2.3  
MISRA C 2012 3.1  
MISRA C++ 2008 2-7-1  
<br/>
<br/>

### <span id="ID_badCommentPosition">▌R3.5.3 注释应出现在合理的位置</span>

ID_badCommentPosition&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: precompile suggestion

<hr/>

注释应出现在段落的前后或行尾，不应出现在行首或中间，否则对阅读产生较大干扰，也可能导致标准未定义的行为。  
  
示例：
```
#/*comment*/include "foo.h"         // Non-compliant
#include <bar.h /*comment*/>        // Non-compliant, undefined behavior

/*comment*/ int main()              // Non-compliant
{
    return a + b /*comment*/ + c;   // Non-compliant
}
```
应改为：
```
#include "foo.h"    // comment      // Compliant
#include <bar.h>    // comment      // Compliant

/*
 * comment                          // Compliant
 */
int main()
{
    return a + b + c;  // comment   // Compliant
}
```
例外：
```
void foo(int i = 0);                // Declaration

void foo(int i /*= 0*/) {           // Let it go
    ....
}
```
如果参数有默认值，在函数实现中参数声明的结尾可用注释说明，不受本规则限制。
<br/>
<br/>
<br/>

### <span id="precompile.other">3.6 Other</span>

### <span id="ID_missingNewLineFileEnd">▌R3.6.1 非空源文件应以换行符结尾</span>

ID_missingNewLineFileEnd&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: precompile suggestion

<hr/>

非空源文件未以换行符结尾，或以换行符结尾但换行符之前是反斜杠，在 C\+\+03 标准中会导致未定义的行为。  
  
一般情况下 IDE 或编辑器会保证源文件以空行结尾，而且 C\+\+11 规定编译器应补全所需的空行，但为了提高兼容性，尤其是自动生成的源文件，应以有效的换行符结尾。  
  
示例：
```
// a.h
#ifndef A_H
#define A_H
....
#endif      // Non-compliant if no line break

// a.cpp
#include "a.h"
int main() {
}
```
如果 a.h 不是以有效的换行符结尾，在某些编译环境中有可能会使 \#endif 和 int main 被合成一行。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.1(2)-undefined  
<br/>
<br/>

### <span id="ID_badBackslash">▌R3.6.2 除转义字符、宏定义之外不应使用反斜杠</span>

ID_badBackslash&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

反斜杠可用于标识转义字符，也可用于实现“伪换行”，即代码换行显示但在语法上并没有换行，一般用于宏定义，除此之外不应再使用反斜杠，否则没有实际意义，也会造成混乱。  
  
示例：
```
#define M(x, y) if(x) {\    // Compliant
    foo(y);\                // Compliant
}

void foo() {
    if (condition1 \        // Non-compliant, meaningless
     || condition2) {
    }
}

int a\                      // Non-compliant, odd usage
b\
c = 123;

void bar() {
    // comment  \           // Non-compliant, The next line is also commented out
    do_something();
}
```
如果“universal character name”被反斜杠截断会导致标准未定义的行为，如：
```
const char* s = "\u4e\      // Non-compliant, undefined behavior
2d";
```
应去掉反斜杠：
```
const char* s = "\u4e2d";   // Compliant
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 2.2(2)-undefined  
<br/>
<br/>

## <span id="global">4. Global</span>

### <span id="ID_nameTooShort">▌R4.1 全局名称应遵循合理的命名方式</span>

ID_nameTooShort&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: global suggestion

<hr/>

全局名称应具有标识性，长度不应过短，否则易与局部名称产生冲突。  
  
本规则是 ID\_badName 的特化。  
  
示例：
```
// In global scope
const int i = 0;     // Non-compliant, name too short
typedef int t;       // Non-compliant, name too short
class A { .... };    // Non-compliant, name too short

int foo(int i) {
    return i + i;    // Confusing
}
```
名称适用的作用域范围越广，其长度也应该越长，建议全局名称长度不小于 3 个字符。
<br/>
<br/>

#### 配置
minFunctionNameLength：全局函数名称长度下限，小于则报出  
minNameSpaceNameLength：全局命名空间名称长度下限，小于则报出  
minTypeNameLength：全局类型名称长度下限，小于则报出  
minVariableNameLength：全局对象名称长度下限，小于则报出  
<br/>

#### 相关
ID_badName  
<br/>

#### 参考
C++ Core Guidelines NL.7  
<br/>
<br/>

### <span id="ID_missingNamespace">▌R4.2 为代码设定合理的命名空间</span>

ID_missingNamespace&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

命名空间是 C\+\+ 项目的必要组成结构，可有效规避名称冲突等问题。  
  
C\+\+ 代码的顶层作用域应为具名非内联命名空间，命名空间名称应与项目名称相符，且具有标识性。  
  
示例：
```
namespace NS {
    int foo();           // Compliant
    int foo(char*);      // Compliant
    int foo(wchar_t*);   // Compliant
}

int foo() {              // Non-compliant, it is not ‘int NS::foo()’
    ....
}

int NS::foo(char*) {     // Compliant
    ....
}

namespace NS {
    int foo(wchar_t*) {  // Compliant
        ....
    }
}
```
对于 main 函数和 extern "C" 声明的代码可不受本规则限制，如：
```
extern "C" int bar();    // Compliant

int main () {            // Compliant
    ....
}
```
<br/>
<br/>

#### 相关
ID_usingNamespaceInHeader  
ID_forbidUsingDirectives  
<br/>

#### 参考
MISRA C++ 2008 7-3-1  
<br/>
<br/>

### <span id="ID_nonGlobalMain">▌R4.3 main 函数只应处于全局作用域中</span>

ID_nonGlobalMain&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

main 函数作为程序的入口，链接器需对其特殊处理，不应受命名空间等作用域的限制。  
  
示例：
```
int main() {       // Compliant
    ....
}

namespace {
    int main() {   // Non-compliant
        ....
    }
}

namespace NS {
    int main() {   // Non-compliant
        ....
    }
}
```
<br/>
<br/>

#### 参考
MISRA C++ 2008 7-3-2  
<br/>
<br/>

### <span id="ID_usingNamespaceInHeader">▌R4.4 头文件中不应使用 using directive</span>

ID_usingNamespaceInHeader&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

在头文件的全局作用域中使用 using directive，极易造成命名冲突，且影响范围难以控制。  
  
如果代码涉及多个命名空间，而这些命名空间中又有名称相同且功能相似的代码元素时，将造成难以排查的混乱。对于库的头文件，更应该严禁使用全局的 using directive，否则造成对用户命名空间的干扰。  
  
示例：
```
// In a header file
namespace NS {
    void foo(short);
}

using namespace NS;   // Non-compliant
using namespace std;  // Non-compliant
```
下例展示的问题是头文件不同的包含顺序竟导致同一函数产生了不同的行为：
```
// In a.h
void foo(char);

namespace ns {
    void foo(int);
}

inline void bar() {
    foo(0);
}

// In b.h
namespace ns {}
using namespace ns;

// In a.cpp
#include "a.h"
#include "b.h"

void fun1() {
    bar();      // ‘bar’ calls ‘foo(char)’
}

// In b.cpp
#include "b.h"
#include "a.h"

void fun2() {
    bar();      // ‘bar’ calls ‘foo(int)’
}
```
头文件 a.h 和 b.h 以不同的顺序被包含，使 bar 函数调用了不同的 foo 函数，导致这种混乱的正是 b.h 中的 using directive。
<br/>
<br/>

#### 相关
ID_forbidUsingDirectives  
<br/>

#### 参考
C++ Core Guidelines SF.7  
MISRA C++ 2008 7-3-6  
<br/>
<br/>

### <span id="ID_staticInHeader">▌R4.5 头文件中不应使用静态声明</span>

ID_staticInHeader&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

头文件中由 static 关键字声明的对象、数组或函数，会在每个包含该头文件的翻译单元或模块中生成副本造成数据冗余，如果将静态数据误用作全局数据也会造成逻辑错误。  
  
类的静态成员不受本规则限制。  
  
示例：
```
// In a header file
static int i = 0;    // Non-compliant

static int foo() {   // Non-compliant
    return i;
}
```
在编译每个包含该头文件的源文件时，变量 i 和函数 foo 都会生成不必要的副本。  
  
在头文件中实现的内联或模板函数中，也不应使用静态声明，如：
```
// In a header file
inline void bar() {
    static Type obj;   // Non-compliant
    ....
}
```
如果该头文件被不同的模块（so、dll、exe）包含，obj 对象会生成不同的副本，很可能造成逻辑错误。  
  
另外， 由 const 或 constexpr 关键字修饰的常量也具有静态数据的特性，在头文件中定义常量也面对这种问题，基本类型的常量经过编译优化可以不占用存储空间（有取地址操作的除外），而对于非基本类型的常量对象或数组也不应在头文件中定义，建议采用单件模式，将其数据定义在 cpp 等源文件中，在头文件中定义访问这些数据的接口，如：
```
// In arr.h
using Arr = int[256];
const Arr& getArr();

// In arr.cpp
#include "arr.h"

const Arr& getArr() {
    static Arr a = {
        1, 2, 3, ....
    };
    return a;
}
```
在需要用到常量数组的地方调用 getArr 函数，即可获取该数组的引用，没有任何重复的数据产生，并可保证数组在使用之前被有效初始化。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 3.5(3)  
<br/>
<br/>

### <span id="ID_anonymousNamespaceInHeader">▌R4.6 头文件中不应定义匿名命名空间</span>

ID_anonymousNamespaceInHeader&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

头文件中定义了匿名命名空间，即相当于在头文件中定义了静态数据，头文件被多个源文件包含时便会造成数据冗余。  
  
可参见 ID\_staticInHeader 的进一步讨论。  
  
示例：
```
// In a header file
namespace {       // Non-compliant
    void foo();
}
```
<br/>
<br/>

#### 相关
ID_staticInHeader  
<br/>

#### 依据
ISO/IEC 14882:2011 7.3.1.1  
<br/>

#### 参考
C++ Core Guidelines SF.21  
MISRA C++ 2008 7-3-3  
<br/>
<br/>

### <span id="ID_staticInAnonymousNamespace">▌R4.7 匿名命名空间中不应使用静态声明</span>

ID_staticInAnonymousNamespace&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

匿名命名空间中的元素已具有静态链接性（internal linkage），不应再用 static 关键字修饰。  
  
示例：
```
namespace {
    static int i = 0;   // Non-compliant
    static int foo() {  // Non-compliant
        return i++;
    }
}
```
例中 static 关键字是多余的。  
  
应改为：
```
namespace {
    int i = 0;          // Compliant
    int foo() {         // Compliant
        return i++;
    }
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 3.5(4)  
<br/>
<br/>

### <span id="ID_relyOnExternalObject">▌R4.8 全局对象的初始化不可依赖未初始化的对象</span>

ID_relyOnExternalObject&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

全局对象的初始化不可依赖在其他源文件中定义的全局对象，也不可依赖在其后面定义的对象。  
  
在不同源文件中定义的全局对象，以及类的静态成员对象，其初始化顺序是不确定的，在同一源文件中定义的对象，排在前面的会先于后面的初始化。为避免产生问题，建议只使用基本类型的常量作为全局对象，且尽量不要使用 extern 关键字。  
  
示例：
```
extern int i;  // Defined in other translate unit
int j = i;     // Non-compliant
```
例中 i 是在其他源文件中定义的对象，j 初始化时无法保证 i 已被正确初始化。  
  
又如：
```
extern int x;  // Defined after ‘y’
int y = x;     // Non-compliant
int x = 123;
```
例中 x 在 y 的后面定义，y 会先于 x 初始化，但 y 却依赖 x，所以 y 的初始化是无效的。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 3.6.2(2 3)  
ISO/IEC 14882:2017 6.6.2(3)  
ISO/IEC 14882:2017 6.6.3(2)  
<br/>

#### 参考
C++ Core Guidelines I.22  
<br/>
<br/>

### <span id="ID_nonConstNonStaticGlobalObject">▌R4.9 全局对象只应为常量或静态对象</span>

ID_nonConstNonStaticGlobalObject&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

非常量全局对象破坏了面向对象的封装理念，如果必须使用全局对象，应将其限定在文件范围之内。  
  
本规则放宽了 ID\_nonConstGlobalObj 的要求，对于 C\+\+ 代码不建议选取本规则，对于 C 代码可酌情选取。  
  
示例：
```
// In global scope
int i = 0;          // Non-compliant
static int j = 0;   // Let it go
const int k = 0;    // Compliant
```
<br/>
<br/>

#### 相关
ID_nonConstGlobalObject  
<br/>

#### 参考
C++ Core Guidelines I.2  
C++ Core Guidelines CP.3  
C++ Core Guidelines R.6  
<br/>
<br/>

### <span id="ID_nonConstGlobalObject">▌R4.10 全局对象只应为常量</span>

ID_nonConstGlobalObject&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

非常量全局对象与类的公有数据成员一样对外部的读写没有限制，破坏了面向对象的封装理念。  
  
关于封装的讨论可参见 ID\_nonPrivateData。  
  
示例：
```
char foo;         // Non-compliant
extern char bar;  // Non-compliant, worse

void fun() {
    do_something(foo, bar);
}
```
改进方法（将全局变量和与其相关的函数封装成类）：
```
class A {
public:
    void fun() {
        do_something(foo, bar);
    }
private:
    char foo;  // Compliant
    char bar;  // Compliant
};
```
如果变量 foo、bar 确实具有全局意义，多个文件都需要访问，不妨将其单件化：
```
A& getObject() {
    static A a;
    return a;
}
```
用 getObject 函数获取对象，再由其成员函数对 foo、bar 进行读写，有效实现封装理念，而且可以保证对象在使用之前被有效初始化。
<br/>
<br/>

#### 相关
ID_nonPrivateData  
<br/>

#### 参考
C++ Core Guidelines I.2  
C++ Core Guidelines CP.3  
C++ Core Guidelines R.6  
<br/>
<br/>

### <span id="ID_staticAndConst">▌R4.11 全局对象不应同时被 static 和 const 关键字修饰</span>

ID_staticAndConst&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

由 const 关键字修饰的全局对象已具有静态链接性（internal linkage），不应再用 static 关键字修饰。  
  
示例：
```
static const int i = 123;  // Non-compliant, redundant ‘static’
```
应改为：
```
const int i = 123;         // Compliant
```
<br/>
<br/>

#### 相关
ID_staticInHeader  
<br/>

#### 依据
ISO/IEC 14882:2003 7.1.1(6)  
ISO/IEC 14882:2011 7.1.1(7)  
<br/>
<br/>

### <span id="ID_forbidUsingDirectives">▌R4.12 全局或命名空间作用域中禁用 using directive</span>

ID_forbidUsingDirectives&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: global suggestion

<hr/>

将其他命名空间中的名称一并引入当前命名空间，是对命名空间机制的破坏。  
  
标准库命名空间作为基础设施可不受本规则约束。  
  
示例：
```
// In global namespace
using namespace std;           // Let it go
using namespace myspace0;      // Non-compliant
using namespace myspace1;      // Non-compliant

namespace myspace1 {
    using namespace myspace2   // Non-compliant
}
```
建议在函数作用域内用 using declaration 代替 using directive：
```
void foo() {
    using myspace2::Type;      // Compliant
    using myspace2::some_fun;  // Compliant

    Type x;
    some_fun(x);
}
```
<br/>
<br/>

#### 相关
ID_usingNamespaceInHeader  
<br/>

#### 参考
C++ Core Guidelines SF.6  
MISRA C++ 2008 7-3-4  
<br/>
<br/>

### <span id="ID_usingSelf">▌R4.13 避免无效的 using directive</span>

ID_usingSelf&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

用 using directive 引用当前命名空间属于无效代码，可能意味着某种错误。  
  
示例：
```
namespace NS
{
    using namespace NS;  // Non-compliant, meaningless
}
```
<br/>
<br/>
<br/>

### <span id="ID_topInlineNamespace">▌R4.14 不应定义全局 inline 命名空间</span>

ID_topInlineNamespace&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: global suggestion

<hr/>

定义全局 inline 命名空间相当于没有命名空间，应在普通命名空间之内使用 inline 命令空间。  
  
示例：
```
namespace V0 {
    int foo();
}

inline namespace V1 {  // Non-compliant
    int foo();
}
```
应该用普通命名空间加以限定：
```
namespace NS
{
    namespace V0 {
        int foo();
    }

    inline namespace V1 {  // Compliant
        int foo();
    }
}
```
<br/>
<br/>
<br/>

### <span id="ID_stdNamespaceModified">▌R4.15 不可修改 std 命名空间</span>

ID_stdNamespaceModified&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

可以为用户定义的类型特化某些标准模板类，除此之外对 std 命名空间添加、修改甚至删除任何代码所导致的后果都是标准未定义的。  
  
示例：
```
class MyType { .... };

namespace std
{
    size_t foo(const MyType& x);   // Non-compliant

    template <>
    struct hash<MyType> {
        size_t operator()(const MyType& x) const {
            return foo(x);
        }
    };
}
```
例中对 hash 标准模板类的特化是可被允许的，但在 std 命名空间中添加的 foo 函数是不被允许的。  
  
应去掉 std 命名空间作用域声明，改为：
```
size_t foo(const MyType& x);   // OK

template <>
struct std::hash<MyType> {
    size_t operator()(const MyType& x) const {
        return foo(x);
    }
};
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 17.6.4.2.1(1 2)  
ISO/IEC 14882:2017 20.5.4.2.1(1 2)  
<br/>

#### 参考
SEI CERT DCL58-CPP  
<br/>
<br/>

## <span id="type">5. Type</span>

### <span id="type.class">5.1 Class</span>

### <span id="ID_nonPrivateData">▌R5.1.1 类的非常量数据成员均应为 private</span>

ID_nonPrivateData&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: type suggestion

<hr/>

类的数据成员均应设为私有，对外统一由成员函数提供访问方法。  
  
将类的所有接口都实现为成员函数，由成员函数按指定逻辑读写数据，以便保证有效地改变对象状态。良好的接口设计会对代码的职责进行合理划分，显著提升可维护性。理想状态下，当有错误需要修正或有功能需要调整时，只改动相关接口的实现即可，调用接口的代码不需要改动，从而将改动降到最低。这种设计的基础便是将数据设为私有，只能由本类的成员函数访问，否则数据可被各个模块随意读写，当有一处需要改动时，很难控制其影响范围。  
  
常量数据成员不可被改变，所以可不受本规则约束。  
  
示例：
```
struct A {
    int *p, n;   // Non-compliant

    A(int n): p(new int[n]), n(n) {}
   ~A() { delete[] p; }
};
```
例中类的数据成员 p 指向动态分配的内存区域，n 记录区域大小，p 和 n 之间存在紧密的逻辑关系，这种内在关系应由成员函数统一维护，不暴露给类的使用者，这便是面向对象的封装理念，也是 C\+\+ 语言的核心理念之一。  
  
应改为：
```
class A {
    int *p, n;   // Compliant

public:
    A(int n): p(new int[n]), n(n) {}
   ~A() { delete[] p; }

    int* begin() { return p; }     // Interfaces for members
    int* end() { return p + n; }
};
```
这样数据成员不能被外界直接访问，成员之间的关系也不会被随意打破，显著提升可维护性。
<br/>
<br/>

#### 相关
ID_protectedData  
ID_mixPublicPrivateData  
<br/>

#### 参考
MISRA C++ 2008 11-0-1  
<br/>
<br/>

### <span id="ID_protectedData">▌R5.1.2 类的非常量数据成员不应定义为 protected</span>

ID_protectedData&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: type suggestion

<hr/>

protected 数据成员在派生类中仍可被随意读写，破坏了封装理念。  
  
本规则是 ID\_nonPrivateData 的特化，关于封装的进一步讨论可参见 ID\_nonPrivateData。  
  
常量数据成员不可被改变，所以可不受本规则约束。  
  
示例：
```
class A {
    ....
protected:
    int data;   // Non-compliant
};
```
应改为由接口访问：
```
class A {
    ....
protected:
    int access_data();   // Interfaces for data
private:
    int data;   // Compliant
};
```
<br/>
<br/>

#### 相关
ID_mixPublicPrivateData  
ID_nonPrivateData  
<br/>

#### 参考
C++ Core Guidelines C.9  
C++ Core Guidelines C.133  
<br/>
<br/>

### <span id="ID_mixPublicPrivateData">▌R5.1.3 类不应既有 public 数据成员又有 private 数据成员</span>

ID_mixPublicPrivateData&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: type suggestion

<hr/>

类的设计应遵循：  
 - 成员之间没有依赖关系，且都可以随意被读写时，则都应声明为 public  
 - 成员之间有依赖关系，或成员的状态会影响到整个对象的状态时，则都应声明为 private  
  
否则应对类进行改造或拆分。  
  
面向对象的封装理念更倾向于将所有数据成员都设为 private，由成员函数按指定逻辑控制每个成员的读写方法，以供外部访问，对代码的职责进行有效的划分，从而提高可维护性并降低风险，关于封装的进一步讨论可参见 ID\_nonPrivateData。  
  
常量数据成员不可被改变，所以可不受本规则约束。  
  
示例：
```
class A {  // Non-compliant
public:
    int n;
    ....
private:
    int d;
};
```
应改为：
```
class A {  // Compliant
public:
    int method_for_n();
    ....
private:
    int n, d;
};
```
<br/>
<br/>

#### 相关
ID_nonPrivateData  
ID_protectedData  
<br/>

#### 参考
C++ Core Guidelines C.9  
C++ Core Guidelines C.134  
<br/>
<br/>

### <span id="ID_missingVirtualDestructor">▌R5.1.4 有虚函数的基类应具有虚析构函数</span>

ID_missingVirtualDestructor&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: type warning

<hr/>

为了避免意料之外的资源泄漏，有虚函数的基类，都应该具有虚析构函数。  
  
通过基类指针析构派生类对象时，如果基类没有虚析构函数会导致标准未定义的行为，无法正确执行派生类的析构函数。  
  
示例：
```
class A {
public:
    A() = default;
   ~A() = default;          // Non-compliant, missing ‘virtual’
    virtual int foo() = 0;
};

class B: public A {
    int *m, n;              // New resource

public:
    B(int s): m(new int[s]), n(s) {}
   ~B() { delete[] m; }
    int foo() override { return n; }
};

A* p = new B(10);
....
delete p;                   // Undefined behavior, may leak
```
由于基类 A 的析构函数不是虚函数，delete p 只调用了基类析构函数，派生类对象的资源没有得到释放。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 12.4(9)  
ISO/IEC 14882:2011 5.3.5(3)-undefined  
<br/>

#### 参考
CWE-1079  
CWE-1087  
CWE-1045  
C++ Core Guidelines C.35  
C++ Core Guidelines C.127  
<br/>
<br/>

### <span id="ID_diamondInheritance">▌R5.1.5 用虚基类避免冗余的基类实例</span>

ID_diamondInheritance&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: type suggestion

<hr/>

当一个类有多个基类，这些基类又继承自同一个类时，会产生多个不同的基类实例，造成逻辑上的冗余和不必要的存储开销。  
  
示例：
```
struct A {
    int i = 0;
};

class B: public A {};
class C: public A {};
class D: public B, public C {};

void foo(D& d) {
    d.i = 1;       // Complie error
    d.B::i = 1;    // Odd
    d.C::i = 1;    // Odd
}
```
在 D 类对象 d 中，基类 A 的成员 i 有两个不同的实例，d 不能直接访问 i，只能通过 d.B::i 或 d.C::i 这种怪异的方式访问。  
  
将共同的基类设为虚基类可以解决这种问题： 
```
class B: virtual public A {};
class C: virtual public A {};
class D: public B, public C {};

void foo(D& d) {
    d.i = 1;       // OK
}
```
注意，直接将虚基类指针转为派生类指针会导致标准未定义的行为，如：
```
void bar(A* a) {
    B* p = (B*)a;  // Undefined behavior
    ....
}
```
这种转换一般不会通过编译，但标准并未要求编译器必须阻止这种转换，改用 dynamic\_cast 可解决这些问题：
```
void bar(A* a) {
    B* p = dynamic_cast<B*>(a);  // OK
    ....
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 10.1(4 5 6 7)  
ISO/IEC 14882:2003 5.2.9(5 8)-undefined  
ISO/IEC 14882:2011 5.2.9(11 12)-undefined  
<br/>

#### 参考
C++ Core Guidelines C.137  
<br/>
<br/>

### <span id="ID_missingCopyConstructor">▌R5.1.6 存在赋值运算符或析构函数时，不应缺少拷贝构造函数</span>

ID_missingCopyConstructor&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: type warning

<hr/>

三个紧密相关的函数：  
 1. 拷贝构造函数  
 2. 析构函数  
 3. 赋值运算符  
  
当这三个函数中的任何一个函数被定义时，说明对象在复制和资源管理方面有特定的行为，所以其他两个函数也需要被定义，这种规则称为“[Rule of three](https://en.wikipedia.org/wiki/Rule_of_three_(C%2B%2B_programming))”。如果缺少某个函数，编译器会生成相关函数，但其特定需求不会被实现。  
  
示例：
```
class A {    // Non-compliant
    int* p;

public:
    A(): p(new int[123]) {
    }

   ~A() {
        delete[] p;
    }
};
```
例中 A 有析构函数，但没有拷贝构造函数和赋值运算符，编译器会生成相关默认函数，但只进行变量值的复制，使多个对象的成员 p 指向同一块内存区域，导致析构时内存被重复释放，所以应定义拷贝构造函数和赋值运算符重新分配内存并复制数据。  
  
注意，当类只负责成员的组合而没有特殊的复制或析构需求时，这三个函数就都不要定义，这种规则称为“[Rule of zero](http://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rc-zero)”。  
  
示例：
```
class B {
    string a, b;

public:
    B(const B& rhs): a(rhs.a), b(rhs.b) {  // Unnecessary
    }

    B& operator = (const B& rhs) {  // Unnecessary
        a = rhs.a;
        b = rhs.b;
    }

   ~B() {  // Unnecessary
    }
};
```
例中 B 只涉及字符串对象的组合，复制和析构可交由成员对象完成，其拷贝构造、赋值运算符及析构函数是不必要的，应该去掉，编译器会进行更好的处理。  
  
同理，在遵循 C\+\+11 及之后标准的代码中，对于：  
 1. 拷贝构造函数  
 2. 析构函数  
 3. 赋值运算符  
 4. 移动拷贝构造函数  
 5. 移动赋值运算符  
  
当定义了这五个函数中的任何一个函数时，其他四个函数也需要定义，这种规则称为“[Rule of five](https://en.wikipedia.org/wiki/Rule_of_three_(C%2B%2B_programming)#Rule_of_Five)”。  
  
如果确实不需要某个函数，也需要用“=delete”指明，以明确约束对象的行为。
<br/>
<br/>

#### 相关
ID_missingDestructor  
ID_missingCopyAssignOperator  
<br/>

#### 参考
C++ Core Guidelines C.21  
<br/>
<br/>

### <span id="ID_missingCopyAssignOperator">▌R5.1.7 存在拷贝构造函数或析构函数时，不应缺少拷贝赋值运算符</span>

ID_missingCopyAssignOperator&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: type warning

<hr/>

三个紧密相关的函数：  
 1. 拷贝构造函数  
 2. 析构函数  
 3. 赋值运算符  
  
当这三个函数中的任何一个函数被定义时，其他两个函数也需要被定义，详见“[Rule of three](https://en.wikipedia.org/wiki/Rule_of_three_(C%2B%2B_programming))”。  
  
值得强调的是，如果确实不需要赋值运算符，需明确将其声明为“=delete”，如果确实只需要浅拷贝，需将其声明为“=default”，这样明确了复制对象时的行为，规避意料之外的错误。  
  
示例：
```
class A {  // Non-compliant, missing assignment operator
public:
    A();
    A(const A&);
   ~A();
};
```
应明确定义赋值运算符：
```
class A {  // Compliant
public:
    A();
    A(const A&);
   ~A();

    A& operator = (const A&);  // Assignment operator
};
```
<br/>
<br/>

#### 相关
ID_missingDestructor  
ID_missingCopyConstructor  
<br/>

#### 参考
C++ Core Guidelines C.21  
<br/>
<br/>

### <span id="ID_missingDestructor">▌R5.1.8 存在拷贝构造函数或赋值运算符时，不应缺少析构函数</span>

ID_missingDestructor&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: type warning

<hr/>

三个紧密相关的函数：  
 1. 拷贝构造函数  
 2. 析构函数  
 3. 赋值运算符  
  
当这三个函数中的任何一个函数被定义时，其他两个函数也需要被定义，详见“[Rule of three](https://en.wikipedia.org/wiki/Rule_of_three_(C%2B%2B_programming))”。  
  
示例：
```
class A {  // Non-compliant, missing destructor
public:
    A();
    A(const A&);
    A& operator = (const A&);
};
```
应明确定义析构函数：
```
class A {  // Compliant
public:
    A();
    A(const A&);
    A& operator = (const A&);

   ~A();   // Destructor
};
```
<br/>
<br/>

#### 相关
ID_missingCopyConstructor  
ID_missingCopyAssignOperator  
<br/>

#### 参考
C++ Core Guidelines C.21  
C++ Core Guidelines C.30  
C++ Core Guidelines C.33  
<br/>
<br/>

### <span id="ID_missingMoveAssignOperator">▌R5.1.9 存在移动构造函数时，不应缺少移动赋值运算符</span>

ID_missingMoveAssignOperator&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: type warning

<hr/>

五个紧密相关的函数：  
 1. 拷贝构造函数  
 2. 析构函数  
 3. 赋值运算符  
 4. 移动拷贝构造函数  
 5. 移动赋值运算符  
  
当这五个函数中的任何一个函数被定义时，其他四个函数也需要被定义，详见“[Rule of five](https://en.wikipedia.org/wiki/Rule_of_three_(C%2B%2B_programming)#Rule_of_Five)”，尤其是存在移动构造函数时，不应缺少移动赋值运算符。
<br/>
<br/>

#### 相关
ID_missingCopyAssignOperator  
<br/>

#### 参考
C++ Core Guidelines C.21  
<br/>
<br/>

### <span id="ID_missingMoveConstructor">▌R5.1.10 存在移动赋值运算符时，不应缺少移动构造函数</span>

ID_missingMoveConstructor&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: type warning

<hr/>

五个紧密相关的函数：  
 1. 拷贝构造函数  
 2. 析构函数  
 3. 赋值运算符  
 4. 移动拷贝构造函数  
 5. 移动赋值运算符  
  
当这五个函数中的任何一个函数被定义时，其他四个函数也需要被定义，详见“[Rule of five](https://en.wikipedia.org/wiki/Rule_of_three_(C%2B%2B_programming)#Rule_of_Five)”，尤其是存在移动赋值运算符时，不应缺少移动构造函数。
<br/>
<br/>

#### 相关
ID_missingCopyConstructor  
<br/>

#### 参考
C++ Core Guidelines C.21  
<br/>
<br/>

### <span id="ID_missingExplicitConstructor">▌R5.1.11 可接受一个参数的构造函数需用 explicit 关键字限定</span>

ID_missingExplicitConstructor&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: type suggestion

<hr/>

为了避免意料之外的类型转换，可接受一个参数的构造函数应该用 explicit 关键字限定。  
  
示例：
```
class String {
public:
    String(int capacity);   // Non-compliant, missing ‘explicit’
    ....
};

void foo(const String&);

int bar() {
    foo(100);   // Can be compiled, but very odd
}
```
由于 String 类的构造函数接受一个 int 型参数，foo(100) 相当于将 100 隐式转为 String 类的对象，这种隐式转换是怪异的，也往往意味着意料之外的错误。  
  
应改为：
```
class String {
public:
    explicit String(int capacity);   // Compliant
    ....
};
```
这样 foo(100) 这种写法便不会通过编译。  
  
例外：
```
class String {
public:
    String(const String&);   // Explicit or not depends on your design intent
    String(String&&);        // ditto
    ....
};
```
拷贝、移动构造函数可不受本规则约束，如果将拷贝、移动构造函数声明为 explicit 则无法再按值传递参数或按值返回对象。在类的接口设计中，应尽量减少隐式转换以避免不易察觉的问题。
<br/>
<br/>

#### 相关
ID_missingExplicitConvertor  
<br/>

#### 参考
C++ Core Guidelines C.46  
MISRA C++ 2008 12-1-3  
<br/>
<br/>

### <span id="ID_missingExplicitConvertor">▌R5.1.12 重载的类型转换运算符需用 explicit 关键字限定</span>

ID_missingExplicitConvertor&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: type suggestion

<hr/>

为了避免意料之外的类型转换，重载的类型转换运算符需用 explicit 关键字限定。  
  
示例：
```
struct A {
    ....
    operator char*();   // Non-compliant
};

A foo();

char* bar() {
    return foo();   // Invalid address returned
}
```
例中 foo 返回临时对象，类型转换运算符被隐式调用，然而当 bar 返回后，临时对象被销毁，返回的指针是无效的。  
  
将类型转换运算符用 explicit 关键字限定，有问题的代码例便不会通过编译：
```
struct A {
    ....
    explicit operator char*();   // Compliant
};
```
在类的接口设计中，应尽量减少隐式转换以避免不易察觉的问题。
<br/>
<br/>

#### 相关
ID_missingExplicitConstructor  
<br/>

#### 参考
C++ Core Guidelines C.164  
<br/>
<br/>

### <span id="ID_excessiveExplicit">▌R5.1.13 不应过度使用 explicit 关键字</span>

ID_excessiveExplicit&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: type warning

<hr/>

对类的拷贝、移动以及不接受 1 个参数的构造函数一般不用 explicit 限定，否则有损代码的易用性和可扩展性。  
  
示例：
```
class A {
public:
    explicit A(const A&);  // In general, ‘explicit’ is not required
    explicit A(A&&);       // Ditto
    explicit A(int, int);  // Ditto
    ....
};
```
当类的拷贝、移动构造函数被 explicit 限定时，无法再按值传递参数或按值返回对象，当不接受 1 个参数的构造函数被 explicit 限定时，无法再用初始化列表定义临时对象，如下代码将无法通过编译：
```
void foo(A);
void bar(const A&);

A a(1, 2);

foo(a);       // Compile error
bar({3, 4});  // Compile error
```
<br/>
<br/>

#### 参考
C++ Core Guidelines C.46  
<br/>
<br/>

### <span id="ID_roughTemplateAssignOperator">▌R5.1.14 带模板的赋值运算符不应覆盖拷贝或移动赋值运算符</span>

ID_roughTemplateAssignOperator&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: type warning

<hr/>

带模板的赋值运算符覆盖拷贝或移动赋值运算符，很可能导致意料之外的错误。  
  
示例：
```
class A {
public:
    template <class T> A& operator = (T x);  // Non-compliant
    ....
};
```
例中的赋值运算符可以同时作为普通赋值运算符、拷贝赋值运算符和移动赋值运算符，是一种混乱的设计。  
  
应明确其拷贝赋和移动赋值运算符：
```
class A {
public:
    A& operator = (const A&);
    A& operator = (A&&);
    template <class T> A& operator = (T x);  // Compliant
    ....
};
```
<br/>
<br/>

#### 相关
ID_roughTemplateConstructor  
<br/>

#### 参考
MISRA C++ 2008 14-5-3  
<br/>
<br/>

### <span id="ID_roughTemplateConstructor">▌R5.1.15 带模板的构造函数不应覆盖拷贝或移动构造函数</span>

ID_roughTemplateConstructor&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: type warning

<hr/>

带模板的构造函数覆盖拷贝或移动构造函数，很可能导致意料之外的错误。  
  
示例：
```
class A {
public:
    template <class T> A(T x);  // Non-compliant
    ....
};
```
例中的构造函数可以同时作为普通构造函数、拷贝构造函数和移动构造函数，是一种混乱的设计。  
  
应明确其拷贝和移动构造函数：
```
class A {
public:
    A(const A&);
    A(A&&);
    template <class T> A(T x);  // Compliant
    ....
};
```
<br/>
<br/>

#### 相关
ID_roughTemplateAssignOperator  
<br/>

#### 参考
MISRA C++ 2008 14-5-2  
<br/>
<br/>

### <span id="ID_unsuitableCopyAssignOperator">▌R5.1.16 抽象类禁用拷贝赋值运算符</span>

ID_unsuitableCopyAssignOperator&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: type warning

<hr/>

抽象类只能作为基类，没有独立的对象，不应调用拷贝赋值运算符。  
  
示例：
```
struct A {
    virtual ~A() = 0;
    A& operator = (const A&);   // Non-compliant
    ....
};

void foo(A& x, A& y) {
    x = y;               // Incomplete assignment
}
```
例中 foo 函数的参数只能是 A 的派生类对象，派生类对象调用基类的拷贝赋值运算符会得到不完整的复制结果。  
  
应改为：
```
struct A {
    virtual ~A() = 0;
    A& operator = (const A&) = delete;   // Compliant
    ....
};
```
将虚基类的拷贝赋值运算符设为 =delete 或 private，可在编译期阻止不完整的复制。
<br/>
<br/>

#### 参考
MISRA C++ 2008 12-8-2  
C++ Core Guidelines C.67  
<br/>
<br/>

### <span id="ID_tooManyFields">▌R5.1.17 数据成员的数量应在规定范围之内</span>

ID_tooManyFields&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: type warning

<hr/>

类或联合体的数据成员过多意味着一个逻辑或功能单位承担了过多的职责，违反了模块化设计理念，是难以维护的。  
  
示例：
```
class C
{
    // ... 3000 members ...
    // Who has the courage to read?
};

union U
{
    // ... 3000 members ...
    // Here is hell!
};
```
<br/>
<br/>

#### 配置
maxClassFieldsCount：类数据成员的数量上限，超过则报出  
maxUnionFieldsCount：联合体数据成员的数量上限，超过则报出  
<br/>
<br/>

### <span id="ID_ignorePaddingData">▌R5.1.18 数据成员之间的填充数据不应被忽视</span>

ID_ignorePaddingData&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: type warning

<hr/>

成员之间存在填充数据，且没有声明对齐方式时，填充数据的长度是由实现定义的，这种数据不应在不同的环境之间传输，而且应注意成员的声明顺序，避免由填充数据造成的空间浪费。  
  
关于填充数据的具体组织方式，详见“[内存对齐](https://en.wikipedia.org/wiki/Data_structure_alignment)”。  
  
示例：
```
struct T {
    int8_t  a;
    int32_t b;
} obj;

recv(sockfd, &obj, sizeof obj, flags);   // Non-compliant
```
例中成员 a 和 b 之间存在填充数据，但没有声明对齐方式，直接在网络上传输这种类型的对象是不符合要求的，如果发送端的对齐方式与接收端不一致就会造成混乱。  
  
应在发送端和接收端统一声明对齐方式：
```
struct alignas(4) T {   // Or use _Alignas in C
    int8_t  a;
    int32_t b;
};
```
注意，敏感数据可能会残留在填充数据中，所以当存储或传输对象前有必要清理填充数据的值，如：
```
T obj;
memset(&obj, 0, sizeof(obj));   // Required
....
fwrite(&obj, sizeof(obj), 1, fp);
```
<br/>
<br/>

#### 相关
ID_accessPaddingData  
<br/>

#### 依据
ISO/IEC 9899:2011 6.2.8(1)-implementation  
ISO/IEC 14882:2011 3.11(1)-implementation  
<br/>

#### 参考
SEI CERT DCL39-C  
<br/>
<br/>

### <span id="ID_unsuitableStructTag">▌R5.1.19 存在构造、析构或虚函数的类不应采用 struct 关键字</span>

ID_unsuitableStructTag&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: type suggestion

<hr/>

简单结构体应采用 struct 关键字，具有封装或多态等特性的类应采用 class 关键字，以便提高可读性。  
  
示例：
```
struct A {     // Compliant
    int x, y;
};

struct B {     // Non-compliant
    B();
   ~B();

private:
    int x, y;
};
```
<br/>
<br/>

#### 参考
C++ Core Guidelines C.2  
C++ Core Guidelines C.8  
<br/>
<br/>

### <span id="type.enum">5.2 Enum</span>

### <span id="ID_duplicateEnumerator">▌R5.2.1 同类枚举项的值不应相同</span>

ID_duplicateEnumerator&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: type warning

<hr/>

枚举项用于标记不同的事物，名称不同但值相同的枚举项往往意味着错误。  
  
示例：
```
enum Color {
    red = 1,
    yellow = 2,
    blue = 2,    // Non-compliant, see ‘yellow’
};
```
例中三个枚举项应分别表示三种颜色，但 blue 与 yellow 的值相同会造成逻辑错误。  
  
又如：
```
enum Fruit {
    apple,
    pear,
    grape,
    favourite = grape,  // Non-compliant
};
```
例中 Fruit 定义了三种水果，而 favourite 表示最喜欢的水果，与其他枚举项不是同一层面的概念，不应聚为一类。  
  
应采用更结构化的方式：
```
enum Fruit {
    apple, pear, grape
};

Fruit favourite() {
    return grape;
}
```
<br/>
<br/>

#### 参考
C++ Core Guidelines Enum.8  
<br/>
<br/>

### <span id="ID_casualInitialization">▌R5.2.2 合理初始化各枚举项</span>

ID_casualInitialization&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: type suggestion

<hr/>

合理初始化各枚举项，只应从下列方式中选择一种：   
 - 全不初始化  
 - 只初始化第一个  
 - 全部初始化为不同的值  
  
示例：
```
enum Colour {
    red,
    blue,
    green,
    yellow = 2   // Non-compliant
};
```
应改为：
```
enum Colour {
    red,
    blue,
    green,
    yellow   // Compliant
};
```
<br/>
<br/>

#### 相关
ID_duplicateEnumerator  
<br/>

#### 参考
MISRA C 2004 9.3  
MISRA C++ 2008 8-5-3  
<br/>
<br/>

### <span id="ID_forbidUnnamedEnum">▌R5.2.3 不应使用匿名枚举声明</span>

ID_forbidUnnamedEnum&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: type suggestion

<hr/>

匿名枚举声明相当于在当前作用域定义常量，但类型不够明确。  
  
示例：
```
enum { rabbit = 0xAA, carrot = 1234 };  // Non-compliant
```
如果无法确定枚举类型的名称，也意味着各枚举项不应聚为一类。  
  
应改为：
```
const int rabbit = 0xAA;  // Compliant
const int carrot = 1234;  // Compliant
```
<br/>
<br/>

#### 参考
C++ Core Guidelines Enum.6  
<br/>
<br/>

### <span id="ID_forbidUnscopedEnum">▌R5.2.4 用 enum class 取代 enum</span>

ID_forbidUnscopedEnum&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: type suggestion

<hr/>

传统 C 枚举没有有效的类型和作用域控制，极易造成类型混淆和名称冲突，在 C\+\+ 语言中建议改用 enum class。  
  
示例：
```
enum E {      // Non-compliant
    e0 = 0,
    e1 = 1,
    e2 = -1
};

E foo();

void bar() {
    if (foo()) {   // ‘e1’ or ‘e2’??
        ....
    }
}
```
传统 C 枚举值与 int 等类型可以随意转换，如果 e0 和 e2 表示某种错误情况，e1 表示正确情况，那么 bar 函数中对 foo 返回值的判断就是错误的，这也是一种常见问题，C\+\+11 提出了 enum class 的概念加强了类型检查，提倡在新项目中尽量使用 enum class。  
  
应改为：
```
enum class E {   // Compliant
    e0 = 0,
    e1 = 1,
    e2 = -1
};

void bar() {
    if (foo() == E::e1) {   // OK
        ....
    }
    if (foo()) {   // Compile error, cannot cast the enum class casually
        ....
    }
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 7.2(2)  
<br/>

#### 参考
C++ Core Guidelines Enum.3  
<br/>
<br/>

### <span id="type.union">5.3 Union</span>

### <span id="ID_forbidNonBasicField">▌R5.3.1 联合体内禁用非基本类型的对象</span>

ID_forbidNonBasicField&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: type warning

<hr/>

因为联合体成员之间共享内存地址，所以成员具有构造或析构函数时会导致混乱。  
  
C\+\+98/03 禁止具有拷贝构造函数或析构函数的对象出现在联合体中，C\+\+11 解除了这条禁令，但在语言层面上不保障正确性，相当于把问题抛给了用户。  
  
示例：
```
union U {
    int i;
    string s;  // Non-compliant

    U(int x): i(x) {
    }

    U(const char* x) {
        new(&s) string(x);
    }

   ~U() {
        s.~string();
    }
};

U u(1);
u.s = "abc";  // No error, no warning, just crash
```
示例代码在某些环境中会崩溃，原因是没能正确区分对象当前持有的类型，执行了错误的构造或析构过程。  
  
正确的做法是在类中用一个成员变量记录当前持有的类型，再将匿名联合体与类的构造函数以及析构函数相关联，从而根据当前持有的类型正确地初始化或销毁对象。
<br/>
<br/>

#### 依据
ISO/IEC 14882:1998 9.5(1)  
ISO/IEC 14882:2003 9.5(1)  
ISO/IEC 14882:2011 9.5(2 3 4)  
<br/>
<br/>

### <span id="ID_forbidNakedUnion">▌R5.3.2 禁用在类之外定义的联合体</span>

ID_forbidNakedUnion&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: type suggestion

<hr/>

联合体各成员共享存储地址，易引发意料之外的错误。如果一定要使用联合体，需对其进行一定的封装，避免对成员的错误访问。  
  
不应出现：  
 - 在命名空间作用域内定义的联合体  
 - 在类中定义的具有 public 访问权限的联合体  
  
示例：
```
union U {      // Non-compliant, global union
    ....
};

class A {
public:
    union {    // Non-compliant, public union
        ....
    };
};

class B {
public:
    ....       // Interfaces about the union
private:
    union {    // Compliant, the union is under control
        ....
    };
};
```
类的公有数据成员本来就违反了封装原则，如果这种公有数据成员又在联合体中，就进一步加大了风险。
<br/>
<br/>

#### 相关
ID_forbidUnion  
<br/>

#### 参考
C++ Core Guidelines C.181  
MISRA C 2004 18.4  
MISRA C 2012 19.2  
MISRA C++ 2008 9-5-1  
<br/>
<br/>

### <span id="ID_forbidUnion">▌R5.3.3 禁用联合体</span>

ID_forbidUnion&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: type suggestion

<hr/>

联合体的问题主要有：  
 - 无法只通过对象获取当前有效的成员  
 - 访问不同的成员相当于不安全的类型转换  
 - 对非基本类型的成员造成构造和析构的混乱  
 - 不能作为基类  
  
这些问题在本质上是对类型理念的破坏，面向对象的程序设计应避免使用联合体。  
  
示例：
```
union U {    // Non-compliant
    int i;
    char c;
};

U u;
u.i = 1000;
cout << u.c << '\n';   // Equivalent to a cast without any restrictions
```
对联合体的使用也相当于一种没有限制的强制类型转换，在 C\+\+ 中建议用 std::variant 或 std::any 取代联合体：
```
std::variant<int, char> u;
u = 123;
cout << get<int>(u) << '\n';    // OK
cout << get<char>(u) << '\n';   // Throw ‘std::bad_variant_access’
```
std::variant 可以有效记录对象当前持有的类型，如果以不正确的类型访问对象会及时抛出异常。  
  
本规则比 ID\_forbidNakedUnion 更严格，针对所有联合体。
<br/>
<br/>

#### 相关
ID_forbidNakedUnion  
<br/>

#### 参考
MISRA C 2004 18.4  
MISRA C 2012 19.2  
MISRA C++ 2008 9-5-1  
<br/>
<br/>

## <span id="declaration">6. Declaration</span>

### <span id="declaration.naming">6.1 Naming</span>

### <span id="ID_badName">▌R6.1.1 遵循合理的命名方式</span>

ID_badName&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

应遵循易于读写，并可准确表达代码意图的命名方式。  
  
不应出现下列情况：  
 - 超长的名称  
 - 易造成混淆或冲突的名称  
 - 无意义或意义过于空泛的名称  
 - 不易于读写的名称  
 - 有违公序良俗的名称  
  
示例：
```
namespace xxx   // Bad, meaningless name
{
    void fun(int);   // Bad, vague

    const int nVarietyisthespiceoflife = 123;   // Bad, hard to read
}
```
例中 xxx、fun 这种无意义或过于空泛的名称是不符合要求的，名称中各单词间应有下划线或大小写变化，否则是不便于读写的。本规则集合示例中出现的 foo、bar 等名称，意在代指一般的代码元素，仅作示例，实际代码中不应出现。  
  
不良命名方式甚至会导致标准未定义的行为，如：
```
extern int identifier_of_a_very_very_long_name_1;
extern int identifier_of_a_very_very_long_name_2;   // Dangerous
```
注意，如果两个名称有相同的前缀，而且相同前缀超过一定长度时是危险的，有可能会导致编译器无法有效区分相关名称。C 标准指明，保证名称前 31 位不同即可避免这种问题，可参见 ISO/IEC 9899:2011 5.2.4.1 的相关规定。  
  
不建议采用相同“长前缀”\+ 不同“短后缀”的命名方式，这种名称非常容易形成笔误或由复制粘贴造成错误，如：
```
struct BinExpr {
    BinExpr* sub0;   // Bad
    BinExpr* sub1;   // Bad
};
```
设 BinExpr 是“二元表达式”类，sub0、sub1 为左右子表达式，这种命名方式应改进：
```
struct BinExpr {
    BinExpr* left;   // Better
    BinExpr* right;  // Better
};
```
<br/>
<br/>

#### 配置
maxWordLength：连续无大小写变化的字符个数上限，超过则报出  
<br/>

#### 依据
ISO/IEC 9899:1999 5.2.4.1(1)  
ISO/IEC 9899:1999 6.4.2.1(6)-undefined  
ISO/IEC 9899:2011 5.2.4.1(1)  
ISO/IEC 9899:2011 6.4.2.1(6)-undefined  
<br/>

#### 参考
C++ Core Guidelines NL.19  
C++ Core Guidelines ES.8  
MISRA C 2004 5.1  
MISRA C 2012 5.1  
<br/>
<br/>

### <span id="ID_reservedName">▌R6.1.2 不应定义具有保留意义的名称</span>

ID_reservedName&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

自定义的名称不应与关键字、标准库或系统中的名称重复，否则极易造成阅读和维护上的困扰。  
  
对于宏，本规则特化为 ID\_macro\_defineReserved、ID\_macro\_undefReserved，如果宏名称出现这种问题，会导致标准未定义的行为。  
  
示例：
```
#include <errno.h>

struct A {
    void foo() {
        if (errno != 0) {  // Which errno?
            ....
        }
    }
private:
    int errno;  // Non-compliant
};
```
例中成员变量 errno 与标准库中的 errno 名称相同，不便于区分是自定义的还是系统定义的，造成不必要的困扰。  
  
为避免冲突和误解，以下命名方式可供参考：  
 - 避免名称以下划线开头  
 - 无命名空间限制的全局名称以模块名称开头  
 - 从名称上体现作用域，如全局对象名以 g\_ 开头，成员对象名以 m\_ 开头或以 \_ 结尾  
 - 从名称上体现类别，如宏名采用全大写字母，类型名以大写字母开头，函数或对象名以小写字母开头  
  
本规则集合对具体的命名方式暂不作量化要求，但代码编写者应具备相关意识。
<br/>
<br/>

#### 相关
ID_macro_defineReserved  
ID_macro_undefReserved  
<br/>

#### 依据
ISO/IEC 9899:2011 7.1.3(1)  
<br/>

#### 参考
SEI CERT DCL37-C  
SEI CERT DCL51-CPP  
MISRA C 2012 21.2  
MISRA C++ 2008 17-0-1  
MISRA C++ 2008 17-0-2  
MISRA C++ 2008 17-0-3  
<br/>
<br/>

### <span id="ID_hideLocal">▌R6.1.3 局部名称不应被覆盖</span>

ID_hideLocal&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

嵌套的作用域中不应出现相同的名称，否则干扰阅读，极易产生误解。  
  
示例：
```
int foo() {
    int i = 0;       // Declares an object ‘i’
    if (cond) {
        int i = 1;   // Non-compliant, hides previous ‘i’
        ....
    }
    return i;
}
```
在一个函数中出现了多个名为 i 的变量，当实际代码较为复杂时，很容易出现意图与实现不符的问题。
<br/>
<br/>

#### 参考
CWE-1109  
C++ Core Guidelines ES.12  
MISRA C 2004 5.2  
MISRA C 2012 5.3  
MISRA C++ 2008 2-10-2  
<br/>
<br/>

### <span id="ID_hideMember">▌R6.1.4 成员名称不应被覆盖</span>

ID_hideMember&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

如果成员函数内的局部名称与成员名称相同，会干扰阅读，易产生误解。  
  
示例：
```
class A {
    int i = 0;          // Member object ‘i’

public:
    int foo() {
        int i = 0;      // Non-compliant, hides the member ‘i’
        return bar(i);
    }

    int bar(int i) {    // Non-compliant, hides the member ‘i’
        return i + i;   // Which ‘i’ is used?
    }
};
```
建议成员变量遵循统一的命名约定，如以“\_”结尾或以“m\_”开头，可有效规避这类问题：
```
class A {
    int i_ = 0;         // Member object ‘i_’

public:
    int foo() {
        int i = 0;      // Compliant
        return bar(i);
    }

    int bar(int i) {    // Compliant
        return i_ + i;
    }
};
```
<br/>
<br/>

#### 参考
CWE-1109  
MISRA C 2004 5.2  
MISRA C 2012 5.3  
MISRA C++ 2008 2-10-2  
<br/>
<br/>

### <span id="ID_duplicatedName">▌R6.1.5 类型名称不应与对象或函数名称相同</span>

ID_duplicatedName&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

如果不同的代码元素使用相同的名称，极易造成困扰。  
  
示例：
```
struct A {
    ....
};

enum {
    A, B, C   // Non-compliant
};

size_t x = sizeof(A);   // What is ‘A’?
```
例中类名 A 与枚举项 A 重名，sizeof(A) 的意义是非常令人困惑的。
<br/>
<br/>

#### 参考
MISRA C++ 2008 2-10-6  
<br/>
<br/>

### <span id="ID_misspelling">▌R6.1.6 不应存在拼写错误</span>

ID_misspelling&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

代码中不应存在拼写错误，尤其是供他人调用的代码，如命名空间名称、公共接口名称等，更不应存在拼写错误。  
  
拼写错误会使代码的使用者对代码的质量产生疑虑，而且相关代码被大量引用后也不便于改正。  
  
示例：
```
class A {
public:
    virtual void destory() = 0;  // Non-compliant, should be ‘destroy’
};
```
例中“destory”函数的名称有拼写错误，应改为“destroy”。
<br/>
<br/>
<br/>

### <span id="declaration.qualifier">6.2 Qualifier</span>

### <span id="ID_qualifierRepeated">▌R6.2.1 const、volatile 不应重复</span>

ID_qualifierRepeated&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: declaration error

<hr/>

重复的 const 或 volatile 限定符是没意义的，也可能意味着某种错误。  
  
示例：
```
const const char* p0 = "....";   // Non-compliant
const char const* p1 = "....";   // Non-compliant
char* const const p2 = "....";   // Non-compliant
```
对于 p0 和 p1，const 重复修饰 char，很可能应该修饰 \* 号，属于常见笔误，应改为：
```
const char * const p0 = "....";  // Compliant
const char * const p1 = "....";  // Compliant
```
对于 p2，const 重复修饰 \* 号，符合语言文法，但没有实际意义，很可能应该修饰 char，应改为：
```
const char * const p2 = "....";  // Compliant
```
<br/>
<br/>

#### 相关
ID_badQualifierPosition  
<br/>
<br/>

### <span id="ID_qualifierForPtrAlias">▌R6.2.2 const、volatile 修饰指针类型的别名是可疑的</span>

ID_qualifierForPtrAlias&emsp;&emsp;&emsp;&emsp;&nbsp;:question: declaration suspicious

<hr/>

如果 const、volatile 修饰指针类型的别名，很可能会造成意料之外的问题。  
  
示例：
```
struct Type {
    void foo();
    void foo() const;
};

typedef Type* Alias;

void bar(const Alias a) {  // Rather suspicious
    a->foo();              // Calls ‘void Type::foo();’
}
```
例中 Alias 是 Type\* 的别名，“const Alias a”很容易引起误解，好像对象是不可被改变的，但实际上 a 的类型是 Type \*const，const 限定的是指针，而不是指针指向的对象，这种情况下，对象仍可以被修改，其调用的函数也可能与预期不符。  
  
应避免为指针类型定义别名，如果必须定义应提供常量和非常量两种别名，如：
```
typedef Type* Alias;
typedef const Type* ConstAlias;

void bar(ConstAlias a) {
    a->foo();              // Calls ‘void Type::foo() const;’
}
```
注意，如果 const、volatile 修饰引用的别名则是错误的，详见 ID\_qualifierInvalid。
<br/>
<br/>

#### 相关
ID_qualifierInvalid  
<br/>

#### 参考
SEI CERT DCL05-C  
<br/>
<br/>

### <span id="ID_qualifierInvalid">▌R6.2.3 const、volatile 不可修饰引用</span>

ID_qualifierInvalid&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: declaration error

<hr/>

C\+\+ 标准规定，const 或 volatile 可修饰指针，但不可修饰引用，否则起不到任何作用。  
  
示例：
```
int a = 0;
int &const i = a;     // Non-compliant
int &volatile j = a;  // Non-compliant
```
修饰 & 号的 const 和 volatile 是无效的，i 可被随意修改，j 也可能被优化。  
  
应去掉限定符，或使限定符修饰引用的对象：
```
const int& i = a;     // Compliant
volatile int& j = a;  // Compliant
```
注意，如果限定符修饰引用类型的别名，会引起很大误解，如：
```
typedef int& int_r;   // Reference type alias, bad
const int_r r0 = a;   // Non-compliant, r0 is not a const-reference at all
const int_r& r1 = a;  // Non-compliant, r1 is not a const-reference at all
```
例中 r0 像是一个常量对象，而 r1 像是常量对象的引用，但 const int\_r 展开后相当于 int & const，r0 不是常量，r1 也不是常量的引用。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 8.3.2(1)  
ISO/IEC 14882:2011 8.3.2(1)  
ISO/IEC 14882:2017 11.3.2(1)  
<br/>
<br/>

### <span id="ID_badQualifierPosition">▌R6.2.4 const、volatile 限定类型时应出现在左侧</span>

ID_badQualifierPosition&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

语言允许 const、volatile 出现在类型名的左侧，也可以出现在其右侧，甚至可以出现在基本类型名的中间，为了提高可读性，应对其位置进行统一规范。  
  
示例：
```
unsigned int const a = 0;          // Non-compliant
long double const volatile b = 0;  // Non-compliant
```
应改为：
```
const unsigned int a = 0;          // Compliant
const volatile long double b = 0;  // Compliant
```
const、volatile 出现在类型名右侧时，易与 \* 号造成误解，如：
```
const char const* p = "....";      // Non-compliant
```
应改为：
```
const char * const p = "....";     // Compliant
```
<br/>
<br/>

#### 相关
ID_sandwichedModifier  
ID_badSpecifierPosition  
<br/>

#### 参考
C++ Core Guidelines NL.26  
<br/>
<br/>

### <span id="ID_sandwichedModifier">▌R6.2.5 const、volatile 等关键字不应出现在基本类型名称的中间</span>

ID_sandwichedModifier&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

某些基本类型可由多个符号组成，const 或 volatile 等关键字不应出现在这些符号的中间，否则可读性较差。  
  
示例：
```
const long volatile long cvll = 0;    // Non-compliant
long const double volatile cvld = 0;  // Non-compliant
```
应改为：
```
const volatile long long cvll = 0;    // Compliant
const volatile long double cvld = 0;  // Compliant
```
本规则对下列关键字有同样的要求：
```
const、volatile、
inline、virtual、explicit、
register、static、thread_local、extern、mutable、
friend、typedef、constexpr
```
<br/>
<br/>

#### 相关
ID_badQualifierPosition  
ID_badSpecifierPosition  
<br/>

#### 参考
C++ Core Guidelines NL.26  
<br/>
<br/>

### <span id="ID_missingConst">▌R6.2.6 避免用常量字符串对非常量字符串指针赋值</span>

ID_missingConst&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

用常量字符串对非常量字符串指针赋值，如果相关内存被修改会导致标准未定义的行为。  
  
示例：
```
char* p = "....";   // Non-compliant
p[x] = '\0';        // Undefined behavior
```
例中 p 指向常量字符串，通过 p 修改常量数据一般会引发“[段错误](https://en.wikipedia.org/wiki/Segmentation_fault)”而导致崩溃，应改为：
```
const char* p = "....";   // Compliant
p[x] = '\0';              // Compile-time protected
```
改为常量字符串指针后，错误的操作无法通过编译。  
  
常量字符串的参数传递同样受本规则约束，如：
```
void foo(char*);

void bar() {
    foo("....");   // Non-compliant
}
```
应将 foo 的参数类型改为 const char\*，或将常量字符串复制后传给 foo 函数。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.13.4(2)-undefined  
ISO/IEC 14882:2011 2.14.5(12)-undefined  
ISO/IEC 14882:2011 5.13.5(16)-undefined  
<br/>

#### 参考
MISRA C 2012 7.4  
<br/>
<br/>

### <span id="ID_uselessQualifier">▌R6.2.7 枚举类型的底层类型不应为 const 或 volatile</span>

ID_uselessQualifier&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

将 enum 或 enum class 的底层类型（underlying type）设为 const 或 volatile 是没有意义的，会被编译器忽略，为语言运用错误。  
  
示例：
```
enum E: const unsigned int  // Non-compliant, ‘const’ is invalid
{
    e0, e1, e2
};
E e = e0;  // ‘e’ is not const
```
应改为：
```
enum E: unsigned int  // Compliant
{
    e0, e1, e2
};
const E e = e0;  // OK, ‘e’ is const
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 7.2(2)  
ISO/IEC 14882:2011 10.2(2)  
<br/>
<br/>

### <span id="ID_constLiteralReference">▌R6.2.8 对常量的定义不应为引用</span>

ID_constLiteralReference&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

虽然 C\+\+ 语言十分灵活，可以通过多种方式达到同一种目的，但应该选择最简洁且通俗易懂的方式实现。  
  
示例：
```
const int& i = 1024;   // Non-compliant
const int&& j = 1024;  // Non-compliant
```
应改为：
```
const int i = 1024;  // Compliant
const int j = 1024;  // Compliant
```
<br/>
<br/>
<br/>

### <span id="ID_forbidRestrictPtr">▌R6.2.9 禁用 restrict 指针</span>

ID_forbidRestrictPtr&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: declaration warning

<hr/>

C 语言中的 restrict 指针要求其他指针不能再指向相同区域，有助于编译器优化，但不符合这种限制时会导致标准未定义的行为，相当于增加了误用的风险，也提高了测试成本。  
  
示例：
```
void foo(int c[]) {
    int *restrict a = &c[0];   // Non-compliant
    int *restrict b = &c[1];   // Non-compliant
    ....
    a = b;                     // Undefined behavior
    ....
}

int bar(int *restrict x, int *restrict y) {   // Non-compliant
    return *x + *y;
}

int baz(int* p) {
    return bar(p, p);   // Undefined behavior
}
```
restrict 指针虽然有助于编译器优化，但应在效率的提高和存在的风险之间进行取舍，非系统库中的代码、改动频繁的代码不建议使用 restrict 指针，而且这种优化大部分情况下也难以真正解决效率的瓶颈问题。
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 6.7.3.1(4 9 11)-undefined  
ISO/IEC 9899:2011 6.7.3.1(4 9 11)-undefined  
<br/>

#### 参考
MISRA C 2012 8.14  
SEI CERT EXP43-C  
<br/>
<br/>

### <span id="ID_forbidVolatile">▌R6.2.10 非适当场景禁用 volatile</span>

ID_forbidVolatile&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: declaration suggestion

<hr/>

应在适当的场景中合理使用 volatile，否则会导致优化或同步相关的多种问题。  
  
只应在以下场景使用 volatile：  
 - 与汇编等语言混合处理同一对象  
 - 信号或中断处理函数处理共享对象  
 - 对象地址对应外设地址  
 - 出于安全目的清理内存中的对象  
  
在这些场景中，如果相关对象没有用 volatile 限定，会导致程序和预期不符，volatile 关键字可以保证对象具有稳定的内存地址，任何读取或写入都可以来源于或作用于内存中的实际数据。  
  
除此之外不应使用 volatile，而且要注意 volatile 和 C/C\+\+ 的并发或同步机制是没有关系的，也无法保证相关操作的原子性。  
  
示例：
```
volatile int x;  // Non-compliant, ‘volatile’ is abused

void thd() {
    LockGuard g;
    read_and_write(x);
}
```
设 thd 是线程函数，LockGuard 是某种 RAII 锁，在已落实同步机制的情况下，不应再使用 volatile 限定共享对象。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 7.1.5.1(8)  
ISO/IEC 14882:2011 7.1.6.1(7)  
<br/>

#### 参考
C++ Core Guidelines CP.8  
C++ Core Guidelines CP.200  
<br/>
<br/>

### <span id="declaration.specifier">6.3 Specifier</span>

### <span id="ID_abusedAuto">▌R6.3.1 使用 auto 关键字需注意可读性</span>

ID_abusedAuto&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

auto 关键字隐藏了类型名称，在使用时需注意不应降低可读性。  
  
非局部对象不宜用 auto 声明，如接口的返回类型、参数、全局对象等，如果局部对象的类型对程序的行为有显著影响，也不宜用 auto 声明。  
  
示例：
```
auto foo() {
    ....
}

auto bar() {
    auto x = foo();
    ....
    return x;
}

auto obj = bar();  // What the hell is ‘obj’??
```
如果想确定 obj 对象的类型，必须通读所有与之相关的代码，可读性很差。  
  
将代码中所有可以替换成 auto 的标识符全部替换成 auto，其结果是不可想象的，与 Python 等语言不同，C\+\+ 语言存在重载、模板等多种严格依赖于类型的特性，如果类型名称不明确，必然会造成阅读和维护等方面的障碍。  
  
下面给出 auto 关键字的合理用法：
```
Type* a = static_cast<Type*>(ptr);               // Repeated type name
unique_ptr<Type[]> b = make_unique<Type[]>(10);  // Repeated type name
```
重复的类型名称使代码变得繁琐，这种情况使用 auto 是更好的方法：
```
auto* a = static_cast<Type*>(ptr);  // OK
auto b = make_unique<Type[]>(10);   // OK
```
又如：
```
vector<Type> v{ .... };
vector<Type>::iterator i = v.begin();  // Verbose
```
begin 函数返回迭代器是一种常识，且迭代器类型名称往往较长，这种情况应使用 auto：
```
auto i = v.begin();  // OK
```
又如：
```
struct SomeClass {
    struct Sub {
        ....
    };
    Sub foo();
};

SomeClass::Sub SomeClass::foo() {  // Repeated ‘SomeClass’
    ....
}
```
重复的类作用域声明十分繁琐，可用 auto 关键字配合后置返回类型改善：
```
auto SomeClass::foo() -> Sub {  // OK
    ....
}
```
总之，使用 auto 关键字的目的应是提升可读性，而不是单纯地简化代码。
<br/>
<br/>

#### 相关
ID_roughAuto  
<br/>

#### 参考
C++ Core Guidelines ES.11  
<br/>
<br/>

### <span id="ID_deprecatedSpecifier">▌R6.3.2 不应使用已过时的关键字</span>

ID_deprecatedSpecifier&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

根据 C\+\+11 标准，register 等关键字已过时，auto 关键字也不可再作为存储类说明符（storage class specifier）。  
  
本规则对 C\+\+ 代码适用，C 代码可不受限制。  
  
示例：
```
register int a;            // Non-compliant
auto int b;                // Non-compliant
int foo(register int x);   // Non-compliant
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 D.2(1)-deprecated  
ISO/IEC 14882:2011 7.1.6.4  
<br/>
<br/>

### <span id="ID_inlineRedundant">▌R6.3.3 不应使用多余的 inline 关键字</span>

ID_inlineRedundant&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

constexpr 关键字修饰的函数已经相当于被声明为 inline，不应再重复声明。  
  
示例：
```
inline constexpr int foo(int n) {  // Non-compliant, ‘inline’ is redundant
    return n + 1;
}
```
应改为：
```
constexpr int foo(int n) {  // Compliant
    return n + 1;
}
```
另外，在类声明中实现的函数也相当于被声明为 inline，不应重复声明：
```
class A {
    ....

public:
    inline int foo() {  // Non-compliant, ‘inline’ is redundant
        return 123;
    }

    int bar() {         // Compliant
        return 456;
    }
};
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 7.1.5(2)  
<br/>
<br/>

### <span id="ID_invalidExternSpecifier">▌R6.3.4 extern 关键字不应作用于类成员的声明或定义</span>

ID_invalidExternSpecifier&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

extern 关键字作用于类成员的声明或定义是没有意义的，为语言运用错误。  
  
示例：
```
class A {
    void foo();
};

extern void A::foo() {  // Non-compliant, invalid ‘extern’
    ....
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 9.2(6)  
ISO/IEC 14882:2011 9.2(6)  
ISO/IEC 14882:2017 12.2(9)  
<br/>
<br/>

### <span id="ID_missingExplicitOverride">▌R6.3.5 重写的虚函数应声明为 override 或 final</span>

ID_missingExplicitOverride&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

将重写的虚函数都声明为 override 或 final 可明显提升可读性，并可确保虚函数被有效重写。  
  
示例：
```
class A {
    virtual int foo();
    virtual int bar();
};

class B: public A {
    int foo();          // Non-compliant
    virtual int bar();  // Non-compliant
};
```
例中 B 重写 A 的 foo 和 bar 这两个虚函数，如果不看 A 的声明，则看不出 B::foo 是虚函数，也看不出 B::bar 是重写的虚函数。  
  
改为如下方式会清晰很多：
```
class B: public A {
    int foo() override;  // Compliant
    int bar() override;  // Compliant
};
```
而且当重写的函数名、参数、返回类型与基类声明不符时，不能通过编译，可及时修正问题。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 10.3(4 5)  
<br/>

#### 参考
C++ Core Guidelines C.128  
<br/>
<br/>

### <span id="ID_redundantOverride">▌R6.3.6 override 和 final 关键字不应同时出现</span>

ID_redundantOverride&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

final 表示不可重写的重写，override 表示可再次重写的重写，这两个关键字不应同时出现。  
  
示例：
```
class D: public B {
public:
   int foo() override final;  // Non-compliant, ‘override’ is redundant
};
```
<br/>
<br/>

#### 参考
C++ Core Guidelines C.128  
<br/>
<br/>

### <span id="ID_redundantVirtual">▌R6.3.7 有 override 或 final 关键字时，不应再出现 virtual 关键字</span>

ID_redundantVirtual&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

只应在定义新的虚函数时使用 virtual 关键字，重写虚函数应使用 override 或 final 关键字，不应再出现 virtual 关键字。  
  
示例：
```
class A {
public:
    virtual int foo();   // Compliant, a new virtual function
    virtual int bar();   // Compliant, a new virtual function
};

class B: public A {
public:
    virtual int foo() final;     // Non-compliant, ‘virtual’ is redundant
    virtual int bar() override;  // Non-compliant, ‘virtual’ is redundant
};
```
去掉多余的 virtual 关键字使代码更简洁：
```
class B: public A {
public:
    int foo() final;     // Compliant
    int bar() override;  // Compliant
};
```
<br/>
<br/>

#### 参考
C++ Core Guidelines C.128  
<br/>
<br/>

### <span id="ID_invalidFinal">▌R6.3.8 不应将 union 设为 final</span>

ID_invalidFinal&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

标准规定 union 不可作为基类，所以将 union 声明为 final 是没有意义的，属于语言运用错误。  
  
示例：
```
union U final  // Non-compliant, meaningless
{
    ....
};
```
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 9.5(2)  
<br/>
<br/>

### <span id="ID_badSpecifierPosition">▌R6.3.9 inline、virtual、static、typedef 等关键字应出现在类型名的左侧</span>

ID_badSpecifierPosition&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

语言允许 inline、virtual、static、typedef 等关键字可以出现在类型名的左侧，也可以出现在其右侧，甚至可以出现在基本类型名的中间，为了提高可读性，应对其位置进行统一规范。  
  
示例：
```
struct A {
    long long typedef ll_t;     // Non-compliant
    bool static foo();          // Non-compliant
    char friend bar();          // Non-compliant
    unsigned inline int baz();  // Non-compliant
};
```
应统一规定出现在类型名的左侧：
```
struct A {
    typedef long long ll_t;     // Compliant
    static bool foo();          // Compliant
    friend char bar();          // Compliant
    inline unsigned int baz();  // Compliant
};
```
本规则对下列关键字有同样的要求：
```
inline、virtual、explicit、
register、static、thread_local、extern、mutable、
friend、typedef、constexpr
```
对于 const 和 volatile 也建议出现在类型名的左侧。
<br/>
<br/>

#### 相关
ID_sandwichedModifier  
ID_badQualifierPosition  
<br/>
<br/>

### <span id="declaration.declarator">6.4 Declarator</span>

### <span id="ID_roughAuto">▌R6.4.1 用 auto 声明指针或引用时应显式标明 *、& 等符号</span>

ID_roughAuto&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

用 auto 声明指针时显式标明 \* 号有利于提高代码可读性，否则会使人误以为是某种非指针的对象。在声明引用时必须显式标明 & 或 && 号，否则成为对象声明，导致逻辑错误或造成不必要的复制开销。  
  
示例：
```
int* foo();
int& bar();

auto p = foo();   // Bad
auto* q = foo();  // Good

auto r = bar();   // Becareful, ‘r’ is not a reference

for (auto e: container) {  // Is it necessary to copy elements?
    ....
}
```
例中 p 为指针，但看起来像是个对象，bar 返回引用，但 r 并不是引用，e 是容器元素的复本，这些问题可能与预期不符，需谨慎对待。
<br/>
<br/>
<br/>

### <span id="ID_forbidVariadicFunction">▌R6.4.2 禁用可变参数列表</span>

ID_forbidVariadicFunction&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: declaration warning

<hr/>

可变参数列表对参数的类型和数量缺乏有效的限定和控制，是公认的不安全因素。  
  
示例：
```
string format(const char* fmt, ...);  // Non-compliant
```
假设 format 函数与 sprintf 函数功能相似，由参数 fmt 设定格式，将其他参数转为字符串后依次替换 fmt 中的占位符并返回结果。设 '@' 和 '$' 为占位符，分别对应字符串和整数，如调用 format("@: $", "value", 123) 则返回字符串 "value: 123"。  
  
如果用可变参数列表实现：
```
string format(const char* fmt, ...) {
    string res;
    va_list vl;
    va_start(vl, fmt);
    for (auto* p = fmt; *p; p++) {
        stringstream ss;
        switch(*p) { 
            case '@': ss << va_arg(vl, char*); break;
            case '$': ss << va_arg(vl, int); break;
            default:  ss << *p; break;
        }
        res.append(ss.str());
    }
    va_end(vl);
    return res;
}
```
例中 va\_start、va\_arg、va\_end 是可变参数列表的标准支持，这种方法只能在运行时以 fmt 为依据获取后续参数，当实际参数与 fmt 不符时会造成严重问题，单纯地要求代码编写者小心谨慎是不可靠的，改用更安全的方法才是明智的选择。  
  
在 C\+\+ 代码中可采用“[模板参数包](https://en.cppreference.com/w/cpp/language/parameter_pack)”来实现这种功能：
```
template <class T, class ...Args>
void get_argstrs(vector<string>& vs, const T& arg, const Args& ...rest) {
    ostringstream oss;
    oss << arg;
    vs.emplace_back(oss.str());
    if constexpr(sizeof...(rest) > 0) {
        get_argstrs(vs, rest...);
    }
}

template <class ...Args>
string format(const char* fmt, const Args& ...args) {  // Compliant
    string res;
    if constexpr(sizeof...(args) > 0) {
        vector<string> vs;
        const size_t n = strlen(fmt);
        get_argstrs(vs, args...);
        for (size_t i = 0, j = 0; i < n; i++) {
            if ((fmt[i] == '@' || fmt[j] == '$') && j < vs.size()) {
                res.append(vs[j++]);
            } else {
                res.push_back(fmt[i]);
            }
        }
    }
    return res;
}
```
示例代码用 get\_argstrs 函数递归地将参数都转为 string 对象存入容器，再将 fmt 中的 '@' 和 '$' 依次替换成容器中的字符串，实际上这种实现是可以不区分 '@' 和 '$' 的，这个过程中参数的个数和类型是可以由代码主动判断的，如果参数不能转为字符串则不会通过编译，如果参数个数与占位符不符也容易作出处理。  
  
“[模板参数包](https://en.cppreference.com/w/cpp/language/parameter_pack)”、“[constexpr](https://en.cppreference.com/w/cpp/language/constexpr)”等特性是 C\+\+ 语言在编译理论上的重大突破，合理运用这些特性可以有效提升代码的安全性和可维护性。
<br/>
<br/>

#### 相关
ID_badParmN  
ID_badVaArgType  
<br/>

#### 依据
ISO/IEC 14882:2003 5.2.2(7)-undefined  
<br/>

#### 参考
C++ Core Guidelines ES.34  
C++ Core Guidelines F.55  
MISRA C 2004 16.1  
MISRA C++ 2008 8-4-1  
<br/>
<br/>

### <span id="ID_forbidFlexibleArray">▌R6.4.3 禁用柔性数组</span>

ID_forbidFlexibleArray&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: declaration suggestion

<hr/>

柔性数组（flexible array）一般是指结构体最后不完整定义的数组成员，表示不占用空间的指针，这种数组在 C99 中有所定义，但不在 C\+\+ 标准之中，在 C\+\+ 代码中不应使用。  
  
示例：
```
struct A {
    int len;
    int dat[];  // Non-compliant
};

A* cpy(const A* p) {
    A* a = (A*)malloc(
        sizeof(A) + p->len * sizeof(int)
    );
    *a = *p;    // Error, only p->len is copied
    return a;
}
```
例中 \*a = \*p 这种拷贝赋值运算会漏掉数组的内容，而且数组不会计入 sizeof 的结果，易引起意料之外的错误，所以在 C 语言中也不建议使用这种柔性数组。
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 6.7.2.1(16)  
<br/>

#### 参考
MISRA C 2012 18.7  
<br/>
<br/>

### <span id="ID_forbidFunctionVoidPtr">▌R6.4.4 接口的参数或返回值不应被声明为 void*</span>

ID_forbidFunctionVoidPtr&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: declaration warning

<hr/>

与接口相关的数据类型应保持精确，不应将参数或返回值声明为 void\*。  
  
在 C\+\+ 语言中，如果参数或返回值需要面对多种不同类型的数据，应合理使用重载或模板机制。  
  
示例：
```
class A {
public:
    void* foo();      // Non-compliant
    void bar(void*);  // Non-compliant
};
```
例中 foo 和 bar 函数的返回值以及参数是不符合要求的。  
  
C 语言中存在大量的库函数不符合本规则要求，在 C\+\+ 语言中应避免使用，如：
```
int buf[123];
memset(buf, 0, 123);  // Logic error, should be ‘123 * sizeof(int)’
```
例中 memset 函数的第一个形式参数就是 void\* 型，只能通过更底层的二进制方式访问对象序列，是一种对类型设计的破坏，应改用 STL 标准库提供的方法：
```
int buf[123];
std::fill_n(buf, 123, 0);  // Safe and brief
```
改用类型明确的方法可以使很多问题在编译期得到控制。  
  
例外：
```
struct T {
    void* operator new(size_t);   // Compliant
    void operator delete(void*);  // Compliant
};
```
C\+\+ 语言规定 new 运算符的返回类型为 void\*，delete 运算符的参数类型为 void\*，这些情况可不受本规则约束。
<br/>
<br/>

#### 相关
ID_voidCast  
ID_forbidMemberVoidPtr  
<br/>

#### 参考
C++ Core Guidelines I.4  
<br/>
<br/>

### <span id="ID_forbidMemberVoidPtr">▌R6.4.5 类成员不应被声明为 void*</span>

ID_forbidMemberVoidPtr&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: declaration warning

<hr/>

与接口相关的数据类型应保持精确，不应将类成员声明为 void\*，尤其是非 private 成员，更不应声明为 void\*。  
  
在 C\+\+ 语言中，如果成员需要面对多种不同类型的数据，应合理使用模板机制。  
  
示例：
```
class A {
public:
    void* dat;   // Non-compliant
    ....
};
```
应改为：
```
template <class T>
class A {
public:
    T* method_about_dat();
private:
    T* dat;   // Compliant
    ....
};
```
<br/>
<br/>

#### 相关
ID_voidCast  
ID_forbidFunctionVoidPtr  
<br/>

#### 参考
C++ Core Guidelines I.4  
<br/>
<br/>

### <span id="ID_unsuitableArraySize">▌R6.4.6 局部数组的长度不应过大</span>

ID_unsuitableArraySize&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

局部数组的长度过大增加函数堆栈的压力，易导致溢出错误。  
  
示例：
```
void foo() {
    int arr[1024 * 1024 * 1024];   // Non-compliant, too large
    ....
}
```
局部数组在栈上分配空间，无法控制失败情况，大型数组应在堆上分配，或优化算法降低空间成本：
```
void foo() {
    int* arr = (int*)malloc(1024 * 1024 * 1024 * sizeof(int));   // Compliant
    if (arr) {
        ....     // Normal procedure
    } else {
        ....     // Handle allocation failures
    }
}
```
量化评估程序需要的堆栈空间是产品设计的重要环节，相关的评审与测试也需要落实。
<br/>
<br/>

#### 配置
maxLocalArraySize：局部数组的长度上限，超过则报出  
<br/>

#### 参考
CWE-770  
SEI CERT MEM05-C  
<br/>
<br/>

### <span id="ID_mixedTypeObjDefinition">▌R6.4.7 不建议将类型定义和对象声明写在一个语句中</span>

ID_mixedTypeObjDefinition&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

将类型定义和对象声明写在一个语句中可读性较差，应分开书写。  
  
示例：
```
struct T {
    ....
} obj, *ptr;  // Bad
```
应改为：
```
struct T {
    ....
};

T obj;   // OK
T* ptr;  // OK
```
<br/>
<br/>

#### 参考
C++ Core Guidelines C.7  
<br/>
<br/>

### <span id="ID_mixedDeclarations">▌R6.4.8 不应将函数或函数指针和其他声明写在同一个语句中</span>

ID_mixedDeclarations&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

每条语句只应声明一个函数或函数指针，否则可读性较差。  
  
示例：
```
int* foo(int), bar(0), (*baz)(char);   // Non-compliant, very bad
```
例中 foo 是函数，bar 是整数，baz 是函数指针，这种混在一起的声明是非常混乱的。  
  
应分开声明：
```
int* foo(int);      // Compliant
int bar = 0;        // Compliant
int (*baz)(char);   // Compliant
```
<br/>
<br/>

#### 相关
ID_tooManyDeclarators  
<br/>

#### 参考
C++ Core Guidelines ES.10  
<br/>
<br/>

### <span id="ID_tooManyDeclarators">▌R6.4.9 在一个语句中不应声明过多对象或函数</span>

ID_tooManyDeclarators&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

在一个语句中不应声明过多对象或函数，建议在每个语句中只声明一个对象或函数，提高可读性也可减少笔误。  
  
示例：
```
int* a, b[8], c, d(int), e = 0;  // Bad
```
例中只有 a 是指针，也只有 e 被初始化，d 为函数，应分开声明。
<br/>
<br/>

#### 配置
maxDeclaratorCount：一个声明语句能包含的对象个数上限，超过则报出  
<br/>

#### 参考
C++ Core Guidelines ES.10  
MISRA C++ 2008 8-0-1  
<br/>
<br/>

### <span id="declaration.object">6.5 Object</span>

### <span id="ID_inaccessibleTmpObject">▌R6.5.1 不应产生无效的临时对象</span>

ID_inaccessibleTmpObject&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: declaration error

<hr/>

无名且不受控制的临时对象在构造之后会立即析构，在逻辑上没有意义，往往意味着错误。  
  
示例：
```
class A {
    int a;

public:
    A() {
        A(0);   // Non-compliant, just created an inaccessible temporary object
    }

    A(int x): a(x) {}
};
```
示例代码意在调用重载的构造函数，但 A(0); 只生成了一个无效的临时对象，成员并没有被正确初始化，应改用 this\->A::A(0); 等形式，在遵循 C\+\+11 标准的代码中也可将 A(0) 移入初始化列表：
```
class A {
    int a;

public:
    A(): A(0) {}        // Compliant
    A(int x): a(x) {}
};
```
又如：
```
class LockGuard { .... };

void fun() {
    LockGuard();   // Non-compliant, meaningless
    ....
} 
```
设 LockGuard 是某种 RAII 锁，LockGuard(); 只生成了一个临时对象，该对象会立即析构，起不到作用，这也是一种常见的错误。  
  
应改为：
```
void fun() {
    LockGuard guard;   // Compliant
    ....
}
```
<br/>
<br/>

#### 参考
CWE-665  
C++ Core Guidelines ES.84  
<br/>
<br/>

### <span id="ID_invalidLocalDeclaration">▌R6.5.2 不应存在没有被用到的局部声明</span>

ID_invalidLocalDeclaration&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

没有被用到的局部声明是没有意义的，往往意味着代码冗余或功能不完整，也可能导致严重的逻辑错误。  
  
示例：
```
int foo(int n) {
    int x = 0;
    if (n) {
        int x = 100 / n;   // Non-compliant
    }
    return x;
}
```
在 if 作用域中声明的 x 对象没有被使用，与其相关的计算过程是无效的。  
  
应改为：
```
int foo(int n) {
    int x = 0;
    if (n) {
        x = 100 / n;   // Compliant
    }
    return x;
}
```
具有特定构造或析构函数的 C\+\+ 对象可以做到“声明即使用”，但要注意如下情况：
```
class LockGuard {
    LockGuard();
   ~LockGuard();
};

void bar() {
    LockGuard guard();   // Non-compliant, this is a function
    do_something();
}
```
例中 guard 意在实现某种 RAII 锁，但 LockGuard guard(); 声明的是函数而不是对象，构造和析构函数不会按预期执行，这也是一种常见笔误。  
  
应改为：
```
void bar() {
    LockGuard guard;   // Compliant
    do_something();
}
```
<br/>
<br/>
<br/>

### <span id="ID_selfDependentInitialization">▌R6.5.3 对象初始化不可依赖自身的值</span>

ID_selfDependentInitialization&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: declaration error

<hr/>

对象初始化依赖自身的值属于逻辑错误，也是常见的笔误。  
  
示例：
```
void foo(int i) {
    if (i > 0) {
        int i = i + 1;  // Non-compliant
        ....
    }
}
```
例中局部变量 i 的初始化依赖自身的值，这种问题往往是错误地定义了与外层作用域中名称相同的对象。  
  
应改为：
```
void foo(int i) {
    if (i > 0) {
        int j = i + 1;  // OK
        ....
    }
}
```
<br/>
<br/>
<br/>

### <span id="ID_plainNumericChar">▌R6.5.4 参与数值运算的 char 变量需显式声明 signed 或 unsigned</span>

ID_plainNumericChar&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

没有 signed 或 unsigned 限制的 char 类型，是否有符号由具体的编译器决定。  
  
示例：
```
bool foo(char c) {    // Non-compliant
    return c < 180;   // May be always true
}

void bar() {
    char c = 180;          // Non-compliant
    printf("%d", 2 * c);   // What is output?
}
```
示例代码可移植性较差，foo 在某些环境中可能只会返回 true，而 bar 可能输出 \-152，也可能输出 360。  
  
char 类型在桌面、服务端等环境中可能是有符号的，在移动端或嵌入式系统中可能是无符号的，需明确其具体编译环境。  
  
应改为：
```
bool foo(unsigned char c) {   // Compliant
    return c < 180;
}

void bar() {
    unsigned char c = 180;    // Compliant
    printf("%d", 2 * c);      // 360
}
```
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 6.2.5(15)-implementation  
ISO/IEC 14882:2011 3.9.1(1)-implementation  
<br/>

#### 参考
MISRA C++ 2008 5-0-11  
SEI CERT INT07-C  
<br/>
<br/>

### <span id="ID_plainBinaryChar">▌R6.5.5 字节的类型应为 unsigned char</span>

ID_plainBinaryChar&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

字节等二进制概念不应受符号位干扰，应声明为 unsigned char。  
  
示例：
```
typedef char byte;       // Non-compliant
byte buf[100];
FILE* fp = fopen("foo", "rb");
fread(buf, 1, 100, fp);
if (buf[0] == 0xff) {    // May be always false
    ....
}
if (buf[1] << 1) {       // May cause undefined behavior
    ....
}
```
char 类型的符号由实现定义，有符号的 char 变量在数值计算、位运算等方面很容易产生意料之外的结果。  
  
应改为：
```
typedef unsigned char byte;   // Compliant
```
这样做也可有效区分二进制数据与字符串，提高可读性。
<br/>
<br/>

#### 相关
ID_plainNumericChar  
ID_bitwiseOperOnSigned  
<br/>

#### 依据
ISO/IEC 14882:2003 3.9.1(1)-implementation  
ISO/IEC 14882:2011 3.9.1(1)-implementation  
<br/>
<br/>

### <span id="declaration.parameter">6.6 Parameter</span>

### <span id="ID_missingParamName">▌R6.6.1 函数原型声明中的参数应具有合理的名称</span>

ID_missingParamName&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

参数的名称是其用途的直接说明，合理的名称可显著提高可读性。  
  
示例：
```
char* strstr(const char* haystack, const char* needle);   // Good
```
这是标准库函数 strstr 的原型声明，利用形象的比喻，表示在 haystack 中查找 needle。  
  
如果将声明改为如下形式，就令人费解了：
```
char* strstr(const char*, const char*);       // Bad
char* strstr(const char* a, const char* b);   // Bad
```
<br/>
<br/>

#### 参考
MISRA C 2004 16.3  
MISRA C 2012 8.2  
<br/>
<br/>

### <span id="ID_invalidParamArraySize">▌R6.6.2 不应将数组作为函数的形式参数</span>

ID_invalidParamArraySize&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

在形式参数中对数组大小的声明起不到实际的限制作用。  
  
示例：
```
int foo(int a[100]);  // Non-compliant

int bar() {
    int a[50] = {};
    return foo(a);    // It can be compiled
}
```
在 C\+\+ 语言中可改为数组的引用：
```
void foo(int (&a)[100]);     // Compliant

template <size_t size>
void foo(int (&a)[size]) {   // Compliant
    ....
}
```
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 6.7.5.3(7)  
ISO/IEC 9899:2011 6.7.6.3(7)  
<br/>

#### 参考
C++ Core Guidelines I.13  
C++ Core Guidelines R.14  
MISRA C++ 2008 5-2-12  
<br/>
<br/>

### <span id="ID_badParmN">▌R6.6.3 parmN 的声明应符合要求</span>

ID_badParmN&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: declaration error

<hr/>

可变参数列表中省略号的前一个参数称为 parmN，如果：  
 - 在 C 语言中，parmN 为数组、函数，或具有寄存器存储期，以及与默认参数提升后不兼容的类型  
 - 在 C\+\+ 语言中，parmN 为引用、数组、函数，或具有与默认参数提升后不兼容的类型  
  
会导致标准未定义的行为。  
  
示例：
```
void foo(float f, ...);          // Non-compliant
void bar(int& i, ...);           // Non-compliant in C++
void baz(register int n, ...);   // Non-compliant in C
```
例中参数 f 为 float 型，与“[默认参数提升（default argument promotion）](https://en.cppreference.com/w/cpp/language/variadic_arguments#Default_conversions)”后的类型 double 不兼容，参数 i 为引用，参数 n 被 register 修饰具有寄存器存储期，这种代码均会导致标准未定义的行为。
<br/>
<br/>

#### 相关
ID_badVaArgType  
ID_forbidVariadicFunction  
<br/>

#### 依据
ISO/IEC 9899:2011 7.16.1.4(4)-undefined  
ISO/IEC 14882:2011 3.2(5)-undefined  
<br/>

#### 参考
SEI CERT EXP58-CPP  
<br/>
<br/>

### <span id="ID_inconsistentDefaultArgument">▌R6.6.4 虚函数参数的默认值应与基类中声明的一致</span>

ID_inconsistentDefaultArgument&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: declaration error

<hr/>

虚函数参数的默认值不受多态规则控制，通过基类指针或引用调用派生类重写的虚函数时，默认值仍采用基类中的定义。  
  
示例：
```
class A {
public:
    virtual int foo(int i = 0) {
        return i;
    }
};

class B: public A {
public:
    int foo(int i = 1) override {  // Non-compliant
        return i + 1;
    }
};

A* p = new B;
cout << p->foo() << '\n';  // What is output?
```
输出 1，这种虚函数的非多态行为是非常令人困惑的。
<br/>
<br/>

#### 相关
ID_deprecatedDefaultArgument  
<br/>

#### 依据
ISO/IEC 14882:2003 8.3.6(10)  
ISO/IEC 14882:2011 8.3.6(10)  
ISO/IEC 14882:2017 11.3.6(10)  
<br/>

#### 参考
CWE-628  
C++ Core Guidelines C.140  
MISRA C++ 2008 8-3-1  
<br/>
<br/>

### <span id="ID_deprecatedDefaultArgument">▌R6.6.5 不建议虚函数的参数有默认值</span>

ID_deprecatedDefaultArgument&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

虚函数参数的默认值不受多态规则控制，通过基类指针或引用调用派生类重写的虚函数时，默认值仍采用基类中的定义，易造成混淆，建议虚函数参数不使用默认值。  
  
示例：
```
class A {
public:
    virtual int foo(int i = 0);   // Bad
};
```
应尽量去掉默认参数值，或改用重载函数的方式：
```
class A {
public:
    virtual int foo();        // OK
    virtual int foo(int i);   // OK
};
```
<br/>
<br/>

#### 相关
ID_inconsistentDefaultArgument  
<br/>

#### 依据
ISO/IEC 14882:2003 8.3.6(10)  
ISO/IEC 14882:2011 8.3.6(10)  
ISO/IEC 14882:2017 11.3.6(10)  
<br/>

#### 参考
CWE-628  
C++ Core Guidelines C.140  
MISRA C++ 2008 8-3-1  
<br/>
<br/>

### <span id="ID_missingVoid">▌R6.6.6 C 代码中参数列表如果为空应声明为“(void)”</span>

ID_missingVoid&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

在 C 语言中，如果函数的参数列表声明为空括号，表示函数的参数还没有声明，而不是表示没有参数，这很容易使人误解，所以在 C 代码中没有参数的参数列表应声明为“(void)”。  
  
示例：
```
// In a.h
int foo();  // Non-compliant

// In a.c
#include "a.h"

int foo(int a) {
    return a + 1;
}

// In main.c
#include <stdio.h>
#include "a.h"

int main() {
    printf("%d\n", foo(1));  // Output: 2
    printf("%d\n", foo());   // Can be compiled, but what is output?
}
```
例中 foo(1) 和 foo() 两种调用都可以通过编译，然而声明与实现不一致的问题总是令人困惑的，如果明确将参数声明为 void 或 int a 则可以解决这种问题。  
  
应改为：
```
int foo(void);   // Compliant, ‘foo(1)’ cannot be compiled
```
或者：
```
int foo(int a);  // Compliant, ‘foo()’ cannot be compiled
```
<br/>
<br/>

#### 相关
ID_superfluousVoid  
<br/>

#### 依据
ISO/IEC 9899:2011 6.7.6.3(14)  
ISO/IEC 9899:2011 6.11.6(1)  
<br/>

#### 参考
MISRA C 2004 16.5  
<br/>
<br/>

### <span id="ID_superfluousVoid">▌R6.6.7 C++ 代码中参数列表如果为空不应声明为“(void)”</span>

ID_superfluousVoid&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

与 C 语言不同，在 C\+\+ 中空括号和“(void)”均表示没有参数，所以应采用更简洁的方式。  
  
示例：
```
struct A {
    int foo(void);  // Verbose
    int bar();      // OK
};
```
<br/>
<br/>

#### 相关
ID_missingVoid  
<br/>

#### 依据
ISO/IEC 14882:2003 C.1.6 Clause 8  
ISO/IEC 14882:2011 C.1.7 Clause 8  
ISO/IEC 14882:2017 C.1.7 Clause 11  
<br/>

#### 参考
C++ Core Guidelines NL.25  
<br/>
<br/>

### <span id="ID_forbidStaticArrSize">▌R6.6.8 声明数组参数的大小时禁用 static 关键字</span>

ID_forbidStaticArrSize&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: declaration warning

<hr/>

C 语言规定数组作为形式参数时，可用 static 关键字修饰大小，要求传入数组的大小不能小于由 static  关键字修饰的值，有助于编译器优化，但不符合这种限制时会导致标准未定义的行为，相当于增加了误用的风险，也提高了测试成本。  
  
示例：
```
int foo(int a[static 5], int n) {   // Non-compliant
    int i;
    int s = 0;
    for (i = 0; i < n; i++) {
        s += a[i];
    }
    return s;
}

int bar() {
    int a[3] = {1, 2, 3};
    return foo(a, 3);       // Undefined behavior
}
```
这种机制虽然有助于编译器优化，但应在效率的提高和存在的风险之间进行取舍，非系统库中的代码、改动频繁的代码不建议使用这种机制，而且这种优化大部分情况下也难以真正解决效率的瓶颈问题。
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 6.7.6.3(7)  
ISO/IEC 9899:2011 6.7.6.3(7)  
<br/>

#### 参考
MISRA C 2012 17.6  
<br/>
<br/>

### <span id="declaration.function">6.7 Function</span>

### <span id="ID_nonVirtualOverride">▌R6.7.1 派生类不应重新定义与基类相同的非虚函数</span>

ID_nonVirtualOverride&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

派生类不应重新定义与基类相同的非虚函数，否则与多态机制相矛盾，易造成意料之外的问题。  
  
示例：
```
struct A {
    int foo() const { return 0; }
};

struct B: A {
    int foo() const { return 1; }  // Non-compliant
};

int bar(A* a) {
    return a->foo();
}

int main() {
    B b;
    return bar(&b);  // Problematic
}
```
如果将 B 类型的指针传入 bar 函数，将执行 A::foo，然而参数实际指向的是 B 类型的对象，但 B::foo 不会被执行，这就形成了逻辑上的矛盾，极易造成意料之外的问题。
<br/>
<br/>

#### 参考
Effective C++ item 36  
<br/>
<br/>

### <span id="ID_nonStdAssignmentRetType">▌R6.7.2 拷贝赋值、移动赋值运算符应返回所属类的非 const 引用</span>

ID_nonStdAssignmentRetType&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

拷贝赋值、移动赋值运算符应返回所属类的非 const 引用，便于调用者使用并满足泛型编程的要求。  
  
对赋值运算符的合理重载，可以使对象的赋值表达式作为子表达式灵活地出现在各种语句中，这也是“泛型程序设计”的必要条件，使算法的代码实现既可以适应普通变量，也可以适应自定义对象。如果类的对象与标准模板库相关，其赋值运算符应满足本规则的要求，否则无法满足连续赋值等语法要求，在标准模板库的使用上会受到限制。  
  
本规则对 \+=、\-= 等复合赋值运算符也有相同的要求。  
  
示例：
```
struct A {
    A& operator = (const A&);    // Compliant
    A& operator = (A&&);         // Compliant
};

struct B {
    void operator = (const B&);  // Non-compliant, should return ‘B&’
    void operator = (B&&);       // Non-compliant, should return ‘B&’
};
```
<br/>
<br/>

#### 参考
C++ Core Guidelines F.47  
C++ Core Guidelines C.60  
C++ Core Guidelines C.63  
<br/>
<br/>

### <span id="ID_nonStdCopyAssignmentParam">▌R6.7.3 拷贝赋值运算符的参数应为同类对象的 const 左值引用</span>

ID_nonStdCopyAssignmentParam&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

拷贝赋值运算符的参数不应按值传递，否则会造成不必要的复制，以及“[对象切片](https://en.wikipedia.org/wiki/Object_slicing)”等问题。  
  
示例：
```
struct A {
    A& operator = (A);  // Non-compliant
};
```
应改为：
```
struct A {
    A& operator = (const A&);  // Compliant
};
```
<br/>
<br/>

#### 参考
C++ Core Guidelines C.60  
<br/>
<br/>

### <span id="ID_nonStdMoveAssignmentParam">▌R6.7.4 移动赋值运算符的参数应为同类对象的非 const 右值引用</span>

ID_nonStdMoveAssignmentParam&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

移动赋值运算符的参数不可为 const 右值引用，否则将失去移动赋值的意义。  
  
示例：
```
class A {
    char* p;

public:
    A& operator = (const A&& a) {  // Non-compliant
        free(p);
        p = copy(a.p);             // Not necessary
        return *this;
    }

    ....
};
```
例中赋值运算符先释放持有的资源，再复制 a 的资源，不是真正的移动赋值，仍是一种低效实现。应将 a.p 与 this\->p 交换，省去复制过程，并使原有资源由 a 的析构函数释放，才是真正意义上的移动赋值：
```
class A {
    char* p;

public:
    A& operator = (A&& a) noexcept {  // Compliant
        char* tmp = p;
        p = a.p;
        a.p = tmp;
        return *this;
    }

    ....
};
```
<br/>
<br/>

#### 参考
C++ Core Guidelines C.63  
<br/>
<br/>

### <span id="ID_overloadAddressOperator">▌R6.7.5 不应重载取地址运算符</span>

ID_overloadAddressOperator&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

取地址运算符（一元 & 运算符），重载之后可以返回任意地址，极易误用。  
  
获取不完整类型的对象地址时，如果其完整类型重载了取地址运算符，会导致标准未定义的行为。  
  
示例：
```
struct X;   // Incomplete type

X* foo(X& x) {
    return &x;   // Undefined behaviour
}

struct X {
    X* operator &() {   // Non-compliant
        return nullptr;
    }
};

X* bar(X& x) {
    return &x;   // Call ‘X::operator&’
}
```
例中 foo 会导致未定义的行为，可能会返回 x 对象的实际地址，而 bar 会调用重载了的取地址运算符，这是一种混乱的局面。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.3.1(4)-undefined  
ISO/IEC 14882:2011 5.3.1(5)-undefined  
<br/>

#### 参考
C++ Core Guidelines C.166  
MISRA C++ 2008 5-3-3  
<br/>
<br/>

### <span id="ID_overloadComma">▌R6.7.6 不应重载逗号运算符</span>

ID_overloadComma&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

逗号表达式意在从左至右依次执行各子表达式，但重载逗号运算符会打破这一规则，易造成意料之外的结果。  
  
示例：
```
class A { .... };

A& foo(int);
A& operator , (int, A&);  // Non-compliant

int bar(int i) {
    ++i, foo(i);   // Disordered
}
```
例中逗号运算符被重载后变成了一个函数，\+\+i 和 foo(i) 变成了函数参数，函数参数的求值顺序在标准中是未声明的，foo(i) 很有可能会先被求值，\+\+i 则失去了意义。  
  
另外，也不应重载逻辑运算符，参见 ID\_overloadLogicOperator。
<br/>
<br/>

#### 相关
ID_overloadLogicOperator  
<br/>

#### 依据
ISO/IEC 14882:2011 5.2.2(8)  
<br/>

#### 参考
MISRA C++ 2008 5-2-11  
<br/>
<br/>

### <span id="ID_overloadLogicOperator">▌R6.7.7 不应重载“逻辑与”和“逻辑或”运算符</span>

ID_overloadLogicOperator&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

对“逻辑与”、“逻辑或”等运算符的重载会影响效率，甚至造成不符合预期的结果。  
  
C/C\+\+ 标准明确规定了内置逗号、逻辑与、逻辑或等运算符的子表达式求值顺序。对于逻辑表达式，从左到右计算子表达式的值，当可以确定整个表达式的值时立即结束计算，如果还有其他子表达式未求值也不再计算了，这种规则称为“短路规则”，意在提高效率，然而运算符的重载却打破了这一规则。  
  
示例：
```
class A {
    int i;

public:
    A(int x = 0): i(x) {
    }

    bool valid() const {
        return i != 0;
    }

    A& assign(const A& a) {
        i = a.i;
        return *this;
    }
};

bool operator && (const A& a, const A& b) {  // Non-compliant
    return a.valid() && b.valid();
}
```
注意表达式（设 a 和 b 为 A 的对象）：
```
b && a.assign(b)
```
按常理，此表达式的意思应该是如果 b 在某种意义上“有效”，就将 b 赋给 a，所以 b 的值应先被求出，但由于 && 被重载成了一个函数，其左右子表达式成了函数的参数，“短路规则”不再有效，而且参数的求值顺序在标准中是未声明的，所以常规逻辑子表达式的求值顺序无法得到保证。目前 MSVC、g\+\+ 等主流编译器默认都是从右到左计算参数的值，例中 a.assign(b) 会先被执行，造成完全不符合预期的结果。  
  
解决方法：
```
class A {
    ....

public:
    operator bool() const {
        return valid();
    }
};
```
去掉对 && 的重载，在 A 中定义 bool 类型转换运算符，既可保证“短路规则”，又可保证求值顺序。
<br/>
<br/>

#### 相关
ID_overloadComma  
<br/>

#### 依据
ISO/IEC 14882:2011 5.2.2(8)  
<br/>

#### 参考
MISRA C++ 2008 5-2-11  
<br/>
<br/>

### <span id="ID_virtualAssignment">▌R6.7.8 拷贝赋值、移动赋值运算符不应为虚函数</span>

ID_virtualAssignment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

拷贝或移动赋值运算符的返回类型应为所属类的非 const 引用，这类运算符即使是虚函数也不便于被重写。  
  
示例：
```
class A {
public:
    virtual A& operator = (const A&);  // Non-compliant
};

class B: public A {
public:
    virtual B& operator = (const B&);  // Not override
    virtual A& operator = (const A&);  // Override, but too complex
};
```
<br/>
<br/>

#### 相关
ID_nonStdAssignmentRetType  
<br/>

#### 参考
C++ Core Guidelines C.60  
C++ Core Guidelines C.63  
<br/>
<br/>

### <span id="ID_virtualComparison">▌R6.7.9 比较运算符不应为虚函数</span>

ID_virtualComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

重载的比较运算符很难正确触发 C\+\+ 的多态机制，将其设为虚函数很可能引发意料之外的错误。  
  
运算符 ==、!=、<、>、 <=、>= 均受本规则限制。  
  
示例：
```
class A {
    ....
public:
    virtual bool operator == (const A&) const;  // Non-compliant
};

class B: public A {
    ....
public:
    virtual bool operator == (const B&) const;  // Non-compliant, not overloaded
};
```
<br/>
<br/>

#### 参考
C++ Core Guidelines C.87  
<br/>
<br/>

### <span id="ID_virtualInFinal">▌R6.7.10 final 类中不应声明虚函数</span>

ID_virtualInFinal&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

final 类不再产生派生类，其中的虚函数也不会再被重写，故不应声明虚函数。  
  
示例：
```
class A final {
public:
    virtual int foo();  // Non-compliant, a new virutal function in final class
};

class B {
public:
    virtual int bar();
};

class C final: public B {
public:
    virtual int bar();  // Non-compliant, use keyword ‘override’ or ‘final’
};
```
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 9(3)  
<br/>
<br/>

### <span id="declaration.bitfield">6.8 Bitfield</span>

### <span id="ID_exceededBitfield">▌R6.8.1 位域长度不应超过类型约定的大小</span>

ID_exceededBitfield&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

位域长度不应超过类型约定的大小，否则没有意义且会造成不必要的空间浪费。  
  
C 标准规定位域长度不可超过类型约定的大小，但 C\+\+ 标准规定可以超过，超过的部分作为“padding bits”不参与数据的存储。  
  
示例：
```
struct A {
    int x: 50;  // Non-compliant
};

int main() {
    A a;
    cout << sizeof(a) << '\n';  // What is output?
}
```
输出 8，例中成员 x 自身的位域长度仍为 32，而多出来的大小会形成一个不可访问的填充值。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 6.7.2.1(4)  
ISO/IEC 14882:2011 9.6(1)  
<br/>
<br/>

### <span id="ID_singleSignedBitfield">▌R6.8.2 有符号变量的位域长度不应为 1</span>

ID_singleSignedBitfield&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

有符号变量的位域长度如果为 1 表示只有一个比特位，而该比特位是符号位，极易造成意料之外的错误。  
  
本规则不针对匿名成员。  
  
示例：
```
struct X {
    int a: 1;           // Non-compliant
    unsigned int b: 1;  // Compliant
    int c: 2;           // Compliant
};

int main() {
    X x;
    x.a = 1; cout << x.a << '\n';  // What is output?
    x.b = 1; cout << x.b << '\n';
    x.c = 1; cout << x.c << '\n';
}
```
按常规思维，x.b 和 x.c 为 1 与预期相符，x.a 预期是 1，但实际是 \-1。
<br/>
<br/>

#### 参考
MISRA C++ 2008 9-6-4  
<br/>
<br/>

### <span id="ID_forbidEnumBitfield">▌R6.8.3 不应对枚举变量声明位域</span>

ID_forbidEnumBitfield&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: declaration warning

<hr/>

枚举变量的类型可以是有符号整数，符号位与位域结合易导致意料之外的错误，且不利于枚举类型的扩展。  
  
示例：
```
enum E {
    A, B, C, D
};

struct X {
    E e: 2;   // Non-compliant
};

int main() {
    X x;
    x.e = D;
    if (x.e == D) {    // What is output?
        cout << "OK";
    } else {
        cout << "Oops";
    }
}
```
输出 Oops，例中 E 各枚举项的取值范围是 \[0,3\]，按常规思维，位域长度为 2 即可满足这个范围，然而符号位的存在否定了这一点，导致 x.e 用 D 赋值后再与 D 比较竟然得到不相等的结果（因为 D 的值为 3 而 x.e 的值为 \-1）。
<br/>
<br/>

#### 相关
ID_singleSignedBitfield  
<br/>

#### 参考
MISRA C++ 2008 9-6-3  
<br/>
<br/>

### <span id="ID_forbidBitfield">▌R6.8.4 禁用位域</span>

ID_forbidBitfield&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: declaration suggestion

<hr/>

引入位域的本意是为了节省空间，然而位域改变了变量约定俗成的取值范围和存储方式，易造成理解上的偏差，也会提高维护成本。  
  
位域与“引用”等 C\+\+ 概念有冲突，而且 C\+\+ 标准在位域的内存分配和数据对齐等方面定义的不够充分，存在很多由实现定义的内容，所以应改用某种更有效的算法达到节省空间或时间的目的。  
  
多线程访问位域还可能造成数据竞争，参见 ID\_bitfieldDataRaces。  
  
示例：
```
struct A {
    int a: 3;   // Non-compliant
    int b;      // Compliant
};
```
<br/>
<br/>

#### 相关
ID_exceededBitfield  
ID_singleSignedBitfield  
ID_bitfieldDataRaces  
ID_forbidEnumBitfield  
<br/>

#### 依据
ISO/IEC 14882:2003 9.6(1)-implementation  
ISO/IEC 14882:2003 9.6(3)  
ISO/IEC 14882:2011 9.6(1)-implementation  
ISO/IEC 14882:2011 9.6(3)  
ISO/IEC 14882:2017 12.2.4(1)-implementation  
ISO/IEC 14882:2017 12.2.4(3)  
<br/>
<br/>

### <span id="declaration.complexity">6.9 Complexity</span>

### <span id="ID_complexDeclaration">▌R6.9.1 不建议采用复杂的声明</span>

ID_complexDeclaration&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

复杂的声明可读性较差，容易造成理解上的偏差。  
  
对于：  
 - 函数指针的数组  
 - 返回函数指针、数组指针的函数  
 - 以函数指针、数组指针为参数的函数  
  
应先将各子类型取别名，再用简单声明的方式书写。  
  
示例：
```
int (*foo(int))(bool);   // Bad, returns a function pointer
int (*foo(char))[123];   // Bad, returns an array pointer
```
例中声明的是两个函数，但看起来像是函数指针，而且参数列表也显得混乱。  
  
应改为：
```
typedef int(*funptr)(bool);
typedef int(*arrptr)[123];

funptr foo(int);    // Good
arrptr foo(char);   // Good
```
另外，指针的星号个数不宜超过两个，否则意味着指针的解引用逻辑过于复杂，如：
```
T *** x;   // Bad
T * volatile * * const * y;   // Horrible
```
其中 T 为任意类型，如果发现这种指针，意味着需要改进对相关数据的访问方式。
<br/>
<br/>
<br/>

### <span id="declaration.other">6.10 Other</span>

### <span id="ID_violateODR">▌R6.10.1 不应违反 One Definition Rule</span>

ID_violateODR&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

One Definition Rule，即任何翻译单元不得包含变量、函数、类型、枚举或模板的多个定义，否则会导致标准未定义的行为。  
  
示例：
```
// In a.cpp
struct T {    // One Definition
    int i;
};

T* foo() {
    ....
}

// In b.cpp
struct T {    // Non-compliant, another definition
    long i;
};

void bar(T*) {
    ....
}

// In c.cpp
struct T;
T* foo();
void bar(T*);

void baz() {
    bar(foo());   // Problems
}
```
例中 T 类型在两个翻译单元中有不同的定义，违反了 One Definition Rule，易造成难以排查的错误，应将类型声明移入头文件。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 7.16.1.4(4)-undefined  
ISO/IEC 14882:2011 18.10(3)-undefined  
<br/>

#### 参考
MISRA C++ 2008 3-2-2  
<br/>
<br/>

### <span id="ID_labelNotUsed">▌R6.10.2 不应存在没有被用到的标签</span>

ID_labelNotUsed&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

没有被用到的标签意味着 goto 语句的缺失，也可能是残留代码，应当去除。  
  
示例：
```
int foo() {
    int i = 0;
lab:                 // Non-compliant
    return bar(i);
}
```
标签只应与 goto 语句对应，不可有其他用途，如：
```
int bar(int x) {
    int i = 0;
strange_comment:     // Non-compliant
    return x + i;
}
```
例中标签被当作注释，这是一种怪异的用法，也可能会干扰编译器的优化。
<br/>
<br/>

#### 参考
MISRA C 2012 2.6  
<br/>
<br/>

### <span id="ID_staticNotUsed">▌R6.10.3 不应存在没有被用到的静态声明</span>

ID_staticNotUsed&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

没有被用到的静态声明往往是没有意义的，应删除或修正引用关系。  
  
类的静态成员可不受本规则限制。  
  
示例：
```
static int foo();   // Compliant, used
static int bar();   // Non-compliant, unused

int main() {
    return foo();
}
```
<br/>
<br/>

#### 相关
ID_unreachableCode  
<br/>

#### 参考
MISRA C++ 2008 0-1-10  
<br/>
<br/>

### <span id="ID_privateNotUsed">▌R6.10.4 不应存在没有被用到的 private 成员</span>

ID_privateNotUsed&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

没有被用到的 private 成员是没有意义的，应删除或修正引用关系。  
  
示例：
```
struct A {
    int foo() { return 1; }     // Compliant

private:
    int bar;                    // Non-compliant, unused
    int foo(int) { return 0; }  // Non-compliant, unused
};
```
<br/>
<br/>

#### 相关
ID_unreachableCode  
<br/>

#### 参考
MISRA C++ 2008 0-1-10  
<br/>
<br/>

### <span id="ID_deprecatedAutoPtr">▌R6.10.5 避免使用 std::auto_ptr</span>

ID_deprecatedAutoPtr&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

std::auto\_ptr 在 C\+\+11 标准中已被废弃，应使用 std::unique\_ptr。  
  
std::auto\_ptr 在转移资源所有权等方面易被误用，std::unique\_ptr 在相关方面有更严格的限制。  
  
示例：
```
class T { .... };
void bar(auto_ptr<T> p);

auto_ptr<T> a(new T);
auto_ptr<T> b;
....
b = a;      // ‘a’ is invalid after the assignment
bar(b);     // ‘b’ is invalid after this call
....        // Undefined behavior if dereference ‘a’ or ‘b’
```
auto\_ptr 对象的赋值或按值传参都会引起资源所有权的转移，如 b = a 表示 a 的资源被 b 占有，foo(b) 表示 b 的资源被参数占有，之后再对 a 或 b 解引用就会造成错误，这种方式很容易被人误解，C\+\+11 标准已弃用。  
  
unique\_ptr 禁止资源所有权的隐式转移，语义更为明确：
```
unique_ptr<T> a = make_unique<T>();
unique_ptr<T> b;
....
b = move(a);   // OK, explicit moving
foo(b);        // Complie error
```
unique\_ptr 对象必须通过 move 显式转移资源所有权，否则无法通过编译。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 D.10-deprecated  
<br/>

#### 参考
C++ Core Guidelines R.20  
<br/>
<br/>

## <span id="exception">7. Exception</span>

### <span id="ID_exceptionUnsafe">▌R7.1 保证异常安全</span>

ID_exceptionUnsafe&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

当产生异常时，保证：  
 - 相关资源不会泄漏  
 - 相关对象处于正确状态  
  
是 C\+\+ 异常机制可以正确工作的重要基础。  
  
示例：
```
void foo() {
    lock();
    procedure_may_throw();  // Unsafe
    unlock();
}
```
设 lock 是某种获取资源的操作，unlock 是释放资源的操作，procedure\_may\_throw 是可能抛出异常的过程，那么 foo 函数就不是异常安全的，一旦有异常抛出会导致死锁或泄露等问题。  
  
应保证资源从分配到回收的过程不被异常中断，采用对象化管理方法，使分配和回收得以自动完成：
```
void foo() {
    LockOwner object;
    procedure_may_throw();  // Safe
}
```
将 lock 和 unlock 分别由 object 的构造和析构函数完成，即使 procedure\_may\_throw 抛出异常，相关资源也可被自动回收，实现了异常安全，资源的对象化管理方法可参见 ID\_ownerlessResource。  
  
异常安全的另一个重要方面是抛出异常时应保证相关对象的状态是正确的，事务或算法在处理对象时可能要分多个步骤处理对象的多个成员，要注意中途抛出异常会造成数据不一致等问题。
```
class X {
    T a, b;

public:
    void foo() {
        proc(a);
        // ... if throw an exception ...
        proc(b);
    }
};
```
设 a 和 b 是两个密切相关的成员，如账号和金额等，foo 是一个处理事务的函数，如果在中途抛出异常就会使对象处于错误的状态，解决方法可以考虑“复制 \- 交换”模式，如：
```
class X {
    T a, b;

public:
    void foo() {
        X copy(*this);
        proc(copy.a);
        proc(copy.b);
        this->swap(copy);
    }

    void swap(X& v) noexcept {
        ....
    }
};
```
事务先处理对象的副本，处理成功后交换副本与对象的数据，交换过程需要保证不抛出异常，这样从对象副本的生成到事务处理完毕的过程中即使抛出异常也不影响对象的状态。  
  
swap 过程不可抛出异常也是一个规则，参见 ID\_throwInSwap。
<br/>
<br/>

#### 相关
ID_resourceLeak  
ID_ownerlessResource  
ID_throwInSwap  
<br/>

#### 参考
Effective C++ item 29  
<br/>
<br/>

### <span id="ID_throwGenericException">▌R7.2 不应抛出过于宽泛的异常</span>

ID_throwGenericException&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

抛出过于宽泛的异常，如 std::exception、std::logic\_error、std::runtime\_error 等，会使异常处理失去针对性，而且处理这种异常时很可能会将本不应处理的异常一并捕获，造成混乱。  
  
示例：
```
int foo(int a) {
    if (a < 0) {
        throw std::exception();  // Non-compliant
    }
    return bar(a);  // Other exceptions may be thrown
}

void baz(int a) {
    try {
        foo(a);
    } catch (std::exception& e) {  // Other exceptions are also caught
        ....
    }
}
```
foo 函数在参数不符合要求时抛出 std::exception 类的异常，过于宽泛，如果 bar 函数抛出其他异常，也会被当作“参数不符合要求”处理，这显然是错误的。  
  
正确的做法是为每种异常定义明确的子类：
```
class WrongArg: public std::exception {
public:
    WrongArg() {}
};

int foo(int a) {
    if (a < 0) {
        throw WrongArg();  // Compliant
    }
    return bar(a);  // Other exceptions may be thrown
}

void baz(int a) {
    try {
        foo(a);
    } catch (WrongArg& e) {  // Right
        ....
    }
}
```
<br/>
<br/>

#### 相关
ID_catch_generic  
<br/>

#### 参考
CWE-397  
<br/>
<br/>

### <span id="ID_catch_generic">▌R7.3 不应捕获过于宽泛的异常</span>

ID_catch_generic&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

捕获过于宽泛的异常会使异常处理失去针对性，而且很可能会将本不应处理的异常一并捕获，造成混乱。  
  
相关讨论详见 ID\_throwGenericException。  
  
示例：
```
try {
    ....
} catch (std::exception&) {  // Non-compliant
    ....
}

try {
    ....
} catch (std::runtime_error&) {  // Non-compliant
    ....
}

try {
    ....
} catch (std::logic_error&) {  // Non-compliant
    ....
}
```
<br/>
<br/>

#### 相关
ID_throwGenericException  
<br/>

#### 参考
CWE-396  
<br/>
<br/>

### <span id="ID_throwNonExceptionType">▌R7.4 不应抛出非异常类型的对象</span>

ID_throwNonExceptionType&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

不应将整数、字符串等非异常相关的对象当作异常抛出，否则意味着异常相关的设计是不健全的。  
  
完善的异常类型应满足：  
 - 将异常情况合理分类  
 - 提供对异常情况的准确描述  
 - 使异常便于处理和调试  
  
非异常类型难以满足这种需求。  
  
示例：
```
void foo() {
    if (cond) {
        throw 1;       // Non-compliant
    }
    throw "message";   // Non-compliant
}
```
整数或字符串无法区分异常的种类，如果不同的模块均将简单变量作为异常，很容易产生冲突。  
  
应明确定义异常类：
```
class E1: public std::exception { .... };
class E2: public std::exception { .... };

void foo() {
    if (cond1) {
        throw E1();        // Compliant
    }
    throw E2("message");   // Compliant
}
```
注意，throw、try、catch 等关键字应专注于异常处理，不应使用这些关键字控制程序的业务流程，业务代码与异常处理代码应有明显区别，否则会使代码含混不明，效率也会降低，如：
```
void bar(const vector<string>& v, const string& s) {
    auto b = v.begin();
    auto e = v.end();
    for (auto i = b; i != e; ++i) {
        if (*i == s) {
            throw i - b;   // Non-compliant
        }
    }
    throw -1;   // Non-compliant
}
```
例中 bar 抛出字符串 s 在容器 v 中的位置，用异常机制实现与异常无关的功能，是不符合要求的。
<br/>
<br/>

#### 相关
ID_catch_nonExceptionType  
<br/>

#### 参考
C++ Core Guidelines E.14  
C++ Core Guidelines E.3  
<br/>
<br/>

### <span id="ID_catch_nonExceptionType">▌R7.5 不应捕获非异常类型的对象</span>

ID_catch_nonExceptionType&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

字符串或变量以及非异常相关的对象不应被当作异常捕获，否则意味着异常相关的设计是不健全的。  
  
相关讨论详见  ID\_throwNonExceptionType。  
  
示例：
```
try {
    ....
} catch (int) {  // Non-compliant
    ....
}

try {
    ....
} catch (char*) {  // Non-compliant
    ....
}

try {
    ....
} catch (string&) {  // Non-compliant
    ....
}
```
<br/>
<br/>

#### 相关
ID_throwNonExceptionType  
<br/>

#### 参考
C++ Core Guidelines E.14  
<br/>
<br/>

### <span id="ID_throwInDestuctor">▌R7.6 析构函数不可抛出异常</span>

ID_throwInDestuctor&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: exception error

<hr/>

析构函数抛出异常是违反异常处理机制的。  
  
当抛出异常时，从异常被抛出到异常被处理之间的对象，也就是从“throw”到“catch”各层调用栈中的对象会被自动析构，如果在这个过程中某个对象的析构函数又抛出了异常，将引发混乱。标准规定，这种情况会直接引发 std::terminate 函数的执行，所以从析构函数抛出的异常有可能是无法被捕获和处理的。  
  
示例（设 E0 和 E1 是不相关的异常类）：
```
class A {
    ....
public:
   ~A() try {
        if (cond0) {
            throw E0();    // Non-compliant, propagate out
        }
        else if (cond1) {
            throw E1();    // OK, digested by itself
        }
    }
    catch (const E1&) {
        ....
    }
};
```
建议将析构函数声明为 noexcept。  
  
与析构相关的过程也不应抛出异常：  
 - 资源回收  
 - delete、delete\[\] 运算符  
 - 具有 free、clear、release 等语义的函数  
  
另外，具有 swap、hash 等语义以及移动构造或赋值相关的过程也不应抛出异常，详见相关规则。
<br/>
<br/>

#### 相关
ID_throwInDelete  
ID_throwInHash  
ID_throwInSwap  
ID_throwInMove  
<br/>

#### 依据
ISO/IEC 14882:2003 15.2(3)  
ISO/IEC 14882:2011 15.2(3)  
ISO/IEC 14882:2011 3.7.4.2(3)-undefined  
<br/>

#### 参考
C++ Core Guidelines C.36  
C++ Core Guidelines C.37  
SEI CERT DCL57-CPP  
MISRA C++ 2008 15-5-1  
<br/>
<br/>

### <span id="ID_throwInDelete">▌R7.7 内存回收函数不可抛出异常</span>

ID_throwInDelete&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: exception error

<hr/>

内存回收函数抛出异常会导致标准未定义的行为。  
  
示例：
```
class A {
    ....
public:
    void operator delete(void* p) {
        if (!p) {
            throw Exception();    // Non-compliant, undefined behavior
        }
        ....
    }
};
```
<br/>
<br/>

#### 相关
ID_throwInDestuctor  
ID_throwInHash  
ID_throwInSwap  
ID_throwInMove  
<br/>

#### 依据
ISO/IEC 14882:2003 15.2(3)  
ISO/IEC 14882:2011 15.2(3)  
ISO/IEC 14882:2011 3.7.4.2(3)-undefined  
<br/>

#### 参考
C++ Core Guidelines E.16  
<br/>
<br/>

### <span id="ID_throwInSwap">▌R7.8 对象的 swap 过程不可抛出异常</span>

ID_throwInSwap&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

两个对象在 swap（交换）过程中，每个对象的状态都是不完整的，如果在交换中途抛出异常，对象将处于错误的状态。  
  
标准库中存在大量与 swap 相关的接口和算法，如果 swap 抛出异常，也会使标准库无法按约定工作，所有 swap 函数均应标记为 noexcept。  
  
注意，swap 是保证异常安全的重要手段，不抛出异常是基本要求，详见 ID\_exceptionUnsafe。  
  
示例：
```
struct T {
    int* ptr = nullptr;

    void swap(T& a) {
        int* tmp = ptr;
        ptr = a.ptr;
        if (!ptr) {
            throw exception();  // Non-compliant
        }
        a.ptr = tmp;
    }

   ~T() {
        delete[] p;  // Problems
    }
};
```
<br/>
<br/>

#### 相关
ID_exceptionUnsafe  
ID_throwInMove  
<br/>

#### 参考
C++ Core Guidelines C.84  
C++ Core Guidelines C.85  
<br/>
<br/>

### <span id="ID_throwInMove">▌R7.9 移动构造函数和移动赋值运算符不可抛出异常</span>

ID_throwInMove&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

移动构造函数和移动赋值运算符在本质上相当于将当前对象与临时对象“交换”，交换过程不可抛出异常，可参见 ID\_throwInSwap。  
  
示例：
```
class T {
    ....

public:
    T(T&& a) noexcept {
        this->swap(a);
    }

    T& operator = (T&& a) noexcept {
        this->swap(a);
        return *this;
    }

    void swap(T& a) noexcept {  // Do not throw exceptions
        ....
    }
};
```
如能保证成员被正确初始化，可采用例中模式有效实现各种移动接口。
<br/>
<br/>

#### 相关
ID_throwInSwap  
<br/>

#### 参考
C++ Core Guidelines C.66  
<br/>
<br/>

### <span id="ID_exceptionInException">▌R7.10 异常类的构造函数和异常信息相关的函数不应抛出异常</span>

ID_exceptionInException&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

构造异常对象或获取异常信息时，如果再抛出其他异常不利于异常的定位与处理。  
  
示例：
```
class Exception {
    string msg;

public:
    Exception(const char* s) {
        if (!s) {
            throw AnotherException();  // Non-compliant
        }
        msg.assign(s);
    }

    const char* what() const {
        if (msg.empty()) {
            throw AnotherException();  // Non-compliant
        }
        return msg.c_str();
    }
};
```
例中在构造函数和 what 函数中抛出异常是不符合要求的，而且要注意 string 类型的构造函数需要动态内存分配，当分配失败时也会抛出异常，有高可靠性要求的软件系统需要规避。  
  
自定义的异常类应从标准异常类派生，成员也应尽量简单，如：
```
class Exception: public std::exception {
public:
    const char* what() const noexcept override {
        return "message";
    }
};
```
由标准异常类的定义，Exception 的构造函数可以保证不会抛出异常。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 18.8.1  
<br/>
<br/>

### <span id="ID_throwInHash">▌R7.11 与标准库相关的 hash 过程不应抛出异常</span>

ID_throwInHash&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: exception suggestion

<hr/>

对象的 hash 过程中不应抛出异常，否则相关的容器和算法无法正常工作。  
  
示例：
```
template <>
struct std::hash<MyType> {
    using result_type = size_t;
    using argument_type = MyType;

    size_t operator()(const MyType& x) const {
        if (!x.ptr) {
            throw exception();   // Non-compliant
        }
        return hash<size_t>()((size_t)x.ptr);
    }
};
```
标准库规定容器的 find、count 等方法应通过返回值表示对象存在与否，然而如果 hash 过程抛出异常，这些方法也会抛出异常，相当于打破了这种约定，易造成意料之外的结果。
<br/>
<br/>

#### 参考
C++ Core Guidelines C.89  
<br/>
<br/>

### <span id="ID_catch_value">▌R7.12 通过引用捕获异常</span>

ID_catch_value&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

通过值捕获异常可能会导致“[对象切片](https://en.wikipedia.org/wiki/Object_slicing)”，通过指针捕获异常会增加内存管理成本，通过引用捕获异常才是最合理的方式。  
  
示例：
```
try {
    ....
} catch (Exception e) {   // Non-compliant
    ....
}
```
例中 Exception 是异常类，用传值的方式捕获异常是不符合要求的。  
  
应改为：
```
try {
    ....
} catch (Exception& e) {   // Compliant
    ....
}
```
通过指针捕获异常也是不符合要求的，参见 ID\_throwPointer。
<br/>
<br/>

#### 相关
ID_catch_slicing  
ID_throwPointer  
<br/>

#### 参考
C++ Core Guidelines E.15  
C++ Core Guidelines ES.63  
MISRA C++ 2008 15-3-5  
<br/>
<br/>

### <span id="ID_catch_slicing">▌R7.13 捕获异常时不应产生对象切片问题</span>

ID_catch_slicing&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

通过值捕获多态类的异常对象，会使对象的多态性失效，造成异常处理方面的错误。  
  
本规则是 ID\_catch\_value 与 ID\_objectSlicing 的特化。  
  
示例：
```
class Exception {
    ....
public:
    virtual const char* what() const { return nullptr; }
};

void foo() {
    try {
        ....   // If objects derived from Exception may be thrown
    }
    catch (Exception e) {   // Non-compliant, use reference instead
        log(e.what());
    }
}
```
设例中 Exception 是所有异常类的基类，不论哪种异常被捕获，what 只能返回 nullptr，丧失了多态性，使异常被错误处理。
<br/>
<br/>

#### 相关
ID_catch_value  
ID_objectSlicing  
<br/>

#### 参考
C++ Core Guidelines C.145  
C++ Core Guidelines ES.63  
<br/>
<br/>

### <span id="ID_catch_justRethrow">▌R7.14 捕获异常后不应直接再次抛出异常</span>

ID_catch_justRethrow&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

捕获异常后将其直接再次抛出是没有意义的，还会造成不必要的开销。  
  
示例：
```
void foo() {
    try {
        bar();
    }
    catch (...) {
        throw;      // Non-compliant
    }
}
```
例中的 catch 是没有意义的，应将其去掉，或对异常进行有效处理。
<br/>
<br/>
<br/>

### <span id="ID_improperRethrow">▌R7.15 重新抛出异常时应使用空 throw 表达式（throw;）</span>

ID_improperRethrow&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

重新抛出异常时应使用空 throw 表达式，避免异常对象的精度损失或不必要的复制。  
  
示例：
```
class EBase {};
class EDerive: public EBase {};

void foo() {
    try {
        throw EDerive();
    }
    catch (EBase& e) {
        throw e;            // Non-compliant, use ‘throw;’ instead
    }
}

void bar() {
    try {
        foo();
    }
    catch (EDerive& e) {
        ....                // Cannot catch EDerive
    }
}
```
注意，例中 foo 函数虽然捕获的是 EDerive 对象，但 throw e; 抛出的是 EBase 对象，这也是一种“[对象切片](https://en.wikipedia.org/wiki/Object_slicing)”问题，造成了对象类型的“精度损失”。将 throw e; 改为 throw; 可解决这种问题。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 15.1(6)  
ISO/IEC 14882:2011 15.1(8)  
<br/>
<br/>

### <span id="ID_rethrowOutOfCatch">▌R7.16 不应在 catch handler 外使用空 throw 表达式（throw;）</span>

ID_rethrowOutOfCatch&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

空 throw 表达式用于重新抛出当前捕获的异常，用在 catch handler 外是危险的，增大了流程控制的复杂性。  
  
如果当前没有捕获异常的话，空 throw 表达式会引发 std::terminate 函数的执行，这种情况下是否清理调用栈之间的对象则是由实现定义的。  
  
示例：
```
void foo() {
    throw;           // Non-compliant
}

void bar() {
    try {
        throw;       // Non-compliant
    }
    catch (...) {
        ....         // Cannot catch "throw;"
    }
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 15.1(6 8)  
ISO/IEC 14882:2003 15.3(9)-implementation  
ISO/IEC 14882:2011 15.1(8 9)  
ISO/IEC 14882:2011 15.3(9)-implementation  
<br/>

#### 参考
MISRA C++ 2008 15-1-3  
<br/>
<br/>

### <span id="ID_throwPointer">▌R7.17 不应抛出指针</span>

ID_throwPointer&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: exception suggestion

<hr/>

抛出指针会增加不必要的内存管理成本，也容易造成意料之外的错误。  
  
示例：
```
class E { .... } e;

void foo() {
    if (cond) {
        throw &e;     // Non-compliant
    } else {
        throw new E;  // Non-compliant
    }
}

void bar() {
    try {
        foo();
    } catch (E* p) {
        ....          // ‘p’ should be deleted or not??
    }
}
```
例中对捕获的异常指针不论释放还是不释放都有问题，改为抛出对象的方式可有效避免这种问题。
<br/>
<br/>

#### 参考
MISRA C++ 2008 15-0-2  
<br/>
<br/>

### <span id="ID_throwNULL">▌R7.18 不应抛出 NULL</span>

ID_throwNULL&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

在 C\+\+ 语言中，虽然 NULL 表示空指针，然而在多数环境中 throw NULL 相当于 throw 0，类型的不明确会造成对异常的错误捕捉。  
  
示例：
```
void foo() {
    throw NULL;  // Non-compliant
}

void bar() {
    try {
        foo();
    } catch (int) {  // Which handler?
        ....
    } catch (int*) {
        ....
    }
}
```
本例意在抛出一个空指针，然而会被捕获整数的 catch handler 捕获。
<br/>
<br/>

#### 相关
ID_deprecatedNULL  
ID_throwNonExceptionType  
ID_throwPointer  
<br/>

#### 依据
ISO/IEC 14882:2003 C.2.2.3(1)-implementation  
ISO/IEC 14882:2011 C.3.2.4(1)-implementation  
ISO/IEC 14882:2017 C.5.2.7(1)-implementation  
<br/>

#### 参考
CWE-351  
MISRA C++ 2008 15-1-2  
<br/>
<br/>

### <span id="ID_throwNullptr">▌R7.19 不应抛出 nullptr</span>

ID_throwNullptr&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

nullptr 可被所有接受指针的 catch handler 捕捉，使异常处理失去针对性，故不应抛出 nullptr。  
  
示例：
```
void foo() {
    throw nullptr;  // Non-compliant
}

void bar() {
    try {
        foo();
    } catch (int*) {  // Which handler?
        ....
    } catch (char*) {
        ....
    }
}
```
<br/>
<br/>

#### 相关
ID_throwPointer  
<br/>

#### 参考
MISRA C++ 2008 15-0-2  
<br/>
<br/>

### <span id="ID_forbidThrowSpecification">▌R7.20 禁用含 throw 关键字的异常规格说明</span>

ID_forbidThrowSpecification&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: exception warning

<hr/>

由 throw 关键字声明的动态异常规格说明已过时，应采用由 noexcept 关键字声明的方式。  
  
将所有可能抛出的异常详细列出，尤其是牵扯到第三方不可控代码时，会增大代码的管理成本，而且各编译器相关的实现方式并未统一，现已移出标准。  
  
示例：
```
int foo() throw(Exception);  // Non-compliant
```
应改为：
```
int foo() noexcept(false);   // Compliant
```
例外：
```
int bar() throw();           // Let it go?
```
空的 throw 异常规格说明与 noexcept 等价，是一种惯用写法，审计工具不妨通过配置决定是否放过这种方式。
<br/>
<br/>

#### 配置
forbidEmptyThrowSpecification：为 true 时报出空 throw 异常规格说明，否则放过  
<br/>

#### 依据
ISO/IEC 14882:2011 D.4-deprecated  
ISO/IEC 14882:2017 D.3-deprecated  
<br/>

#### 参考
C++ Core Guidelines E.12  
C++ Core Guidelines E.30  
<br/>
<br/>

### <span id="ID_forbidException">▌R7.21 禁用 C++ 异常</span>

ID_forbidException&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: exception warning

<hr/>

本规则适用如下场景，可酌情选取。  
 1. 对时空性能有严格要求的项目  
 2. 代码所属框架不支持异常处理  
 3. 与 C 或其他语言兼容的接口实现  
 4. 项目没有依照异常安全的理念实施  
  
利用返回值或错误码的错误处理方式要求检查可能产生错误的每一个步骤，有些出错情况可能被遗漏，C\+\+ 的异常机制可大幅简化这种繁琐的方式，使代码更专注于事务或算法的实现，而且 C\+\+ 异常是不可被忽略的，然而 C\+\+ 的异常机制是需要一定开销的，对代码的设计与实现也有更严格的要求。  
  
如果异常情况频繁出现，其成本是不可被忽视的，不适用于具有高性能要求的实时软件系统。如果代码所属项目没有依照异常安全的理念实施，使用异常反而会造成更多问题，可参见 ID\_exceptionUnsafe 的进一步讨论。
<br/>
<br/>

#### 相关
ID_exceptionUnsafe  
<br/>

#### 参考
C++ Core Guidelines E.6  
Google C++ Style Guide.Other C++ Features.Exceptions  
<br/>
<br/>

## <span id="function">8. Function</span>

### <span id="ID_mainReturnsNonInt">▌R8.1 main 函数的返回类型只应为 int</span>

ID_mainReturnsNonInt&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

main 函数的返回值可作为整个进程执行情况的总结，按惯例返回 0 或 EXIT\_SUCCESS 表示执行成功，非 0 或 EXIT\_FAILURE 表示执行失败，main 函数的返回值会作为标准 exit 函数的参数。  
  
应采用标准明确支持的方式：  

```
int main(void) { .... }                     // Compliant
int main(int argc, char *argv[]) { .... }   // Compliant
```
如果将返回值设为 void 或其他非 int 类型，均不合规。  

```
void main() { .... }   // Non-compliant
bool main() { .... }   // Non-compliant
```
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 5.1.2.2.1(1)  
ISO/IEC 9899:2011 5.1.2.2.3(1)  
<br/>

#### 参考
C++ Core Guidelines F.46  
<br/>
<br/>

### <span id="ID_illFormedMain">▌R8.2 main 函数不应被重载，也不应声明为 inline、static 或 constexpr</span>

ID_illFormedMain&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

main 函数作为程序的入口，链接器需对其特殊处理，标准规定 main 函数不应被重载，也不应声明为 inline、static 或 constexpr。  
  
示例：
```
static int main(const char* s) {  // Non-compliant
    ....
}

static int main(const wchar_t* w) {  // Non-compliant
    ....
}

int main()
{
    main(L"abc");  // Non-compliant
}
```
那么在 C\+\+ 语言中可以用 noexcept 修饰 main 函数吗？标准没有明确规定，本规则也不建议这样做，不论是否使用 noexcept，在 main 函数中抛出异常都会引起 std::terminate 的执行，而且这样做也不符合惯例。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.6.1(2 3)  
ISO/IEC 14882:2011 3.6.1(2 3)  
ISO/IEC 14882:2017 6.6.1(2 3)  
<br/>
<br/>

### <span id="ID_definedInHeader">▌R8.3 函数不应在头文件中实现</span>

ID_definedInHeader&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

在头文件中实现的函数，如果不是内联、静态或模板函数，则可能被引入不同的翻译单元（translate\-unit）造成编译冲突。  
  
头文件也是项目文档的重要组成部分，头文件的主要内容应是类型或接口的声明，有必要保持头文件简洁清晰。除非函数很简短，否则不建议在头文件中内联实现，大段的函数实现会影响头文件的可读性。  
  
示例：
```
// In a header file
int foo() {          // Non-compliant, add ‘inline’ or move it to a cpp file
    return 1;
}

inline int bar() {   // Compliant
    return 2;
}
```
对于较为复杂的模板函数，建议将其实现与主体头文件分离，如：
```
// In B.h
template <class T>
struct B {
    T foo(T&);
};

#include "impl/B.inc"

// In impl/B.inc
template <class T>
T B<T>::foo(T& p) {
    ....             // Complex implementation
}
```
<br/>
<br/>

#### 参考
C++ Core Guidelines SF.2  
<br/>
<br/>

### <span id="ID_inconsistentParamName">▌R8.4 函数的参数名称在声明和实现处应保持一致</span>

ID_inconsistentParamName&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

函数的参数名称在声明和实现处不一致甚至顺序也不相同，会对函数的调用者造成误导，而且不能排除是实现上的错误。  
  
示例：
```
int foo(int a, int b);    // Prototype

int foo(int b, int a) {   // Non-compliant, which is which??
    return a? b + 1: 0;
}
```
<br/>
<br/>

#### 参考
MISRA C++ 2008 8-4-2  
<br/>
<br/>

### <span id="ID_paramMayBeSlicing">▌R8.5 多态类的对象作为参数时不应采用值传递的方式</span>

ID_paramMayBeSlicing&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

将派生类对象通过传值的方式转换为基类对象后，不再遵循多态机制，易产生意料之外的错误，应采用指针或引用的方式传递多态类对象。  
  
示例：
```
class A {
public:
    virtual int fun();
};

void foo(A);   // Non-compliant

void bar(A&);  // Compliant
void baz(A*);  // Compliant
```
<br/>
<br/>

#### 相关
ID_objectSlicing  
<br/>

#### 参考
C++ Core Guidelines C.145  
C++ Core Guidelines ES.63  
<br/>
<br/>

### <span id="ID_paramNotUsed">▌R8.6 不应存在未被使用的具名形式参数</span>

ID_paramNotUsed&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: function suggestion

<hr/>

如果函数的某个参数在函数内没有被用到，意味着函数的功能与设计之间存在差距。  
  
如果某个参数确实不需要被用到，应尽量从参数列表中将其删除，如果需要遵循某种约定而必须保留参数的话（如虚函数或回调函数），在 C\+\+ 语言中不妨将参数的名称删掉，在 C 语言中可用 (void) 转换说明未使用的参数在预期之内，并用注释说明参数未被使用的原因。  
  
示例：
```
int foo(int x) {   // Non-compliant, ‘x’ is not used
    return 0;
}
```
应改为：
```
int foo(int x) {   // Compliant in C
    (void)x;
    return 0;
}

int foo(int) {     // Compliant in C++
    return 0;   
}
```
<br/>
<br/>

#### 参考
C++ Core Guidelines F.9  
MISRA C 2012 2.7  
MISRA C++ 2008 0-1-11  
<br/>
<br/>

### <span id="ID_paramPassedByValue">▌R8.7 由 const 修饰的参数应为引用或指针</span>

ID_paramPassedByValue&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

参数按值传递时会产生复制开销，而且如果有 const 修饰，意味着对象不可改变，按值传递是没有意义的。  
  
示例：
```
void fun(const string s) {    // Non-compliant
    ....
}
```
例中 s 为按值传递的参数，每当 fun 被调用时，s 都会作为一个新的对象被构造，因为其值又不能被改变，所以这种构造是没有意义的，利用常量引用即可解决这个问题：
```
void fun(const string& s) {   // Compliant
    ....
}
```
改为常量引用后，s 的值和原来一样不可被改变，而且不需要额外的开销。
<br/>
<br/>

#### 参考
C++ Core Guidelines F.16  
<br/>
<br/>

### <span id="ID_illForwardingReference">▌R8.8 转发引用只应作为 std::forward 的参数</span>

ID_illForwardingReference&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

不应混淆转发引用与右值引用，除作为 std::forward 的参数之外，不应对转发引用再有任何操作。  
  
转发引用是类型为 T&& 的参数，T 为函数模板类型，无论左值还是右值均可被这种参数接受，而且 const、volatile 等属性也会被忽略，由于含有不确定的状态，所以直接操作转发引用是不妥的，只应通过 std::forward<T> 交由合适的接口处理。  
  
示例：
```
template <class T>
void bar(T&& x) {
    x.foo();        // Non-compliant
}
```
例中参数 x 是转发引用，并不是右值引用，在 bar 函数内部并不知道 x 是左值还是右值，而且 x 对应的实际参数也可能被 const 或 volatile 修饰，所以直接调用 x 的 foo 成员会引发逻辑上的混乱。
<br/>
<br/>

#### 相关
ID_unsuitableForward  
<br/>

#### 参考
C++ Core Guidelines F.19  
<br/>
<br/>

### <span id="ID_localInitialization">▌R8.9 局部对象在使用前必须初始化</span>

ID_localInitialization&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: function error

<hr/>

未经初始化的局部对象具有不确定的值，在初始化前使用局部对象会导致标准未定义的行为。  
  
示例：
```
int foo() {
    int a;
    if (cond) {
        a = 0;
    }
    return a;  // Non-compliant, an indeterminate value
}
```
例中 a 的初始化依赖某种条件，在条件范围之外使用是错误的。  
  
建议对象在声明处初始化，即使不方便在声明处初始化，也应该在声明的附近进行无条件初始化：
```
int a = 0;     // Compliant

int b;
b = foo();     // Compliant
```
不建议的模式：
```
int a;
if (x) {
    a = 0;
}
....
if (y) {
    use(a);    // Dangerous
}
```
例中 a 的初始化依赖条件 x，并在满足条件 y 时被使用，即使条件 x 和条件 y 有一定相关性可以保证对 a 的使用是正确的，也会造成潜在的维护困难，当条件比较复杂或有变化时极易出错。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 6.3.2.1(2)-undefined  
ISO/IEC 14882:2011 4.1(1)-undefined  
ISO/IEC 14882:2011 8.5(11)  
<br/>

#### 参考
CWE-909  
CWE-908  
CWE-824  
CWE-457  
C++ Core Guidelines ES.20  
MISRA C 2004 9.1  
MISRA C 2012 9.1  
MISRA C++ 2008 8-5-1  
<br/>
<br/>

### <span id="ID_memberInitialization">▌R8.10 成员须在声明处或构造时初始化</span>

ID_memberInitialization&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

成员的声明和使用相距较远，更容易造成未初始化先使用的问题，所以应在声明处或构造函数中初始化所有成员。  
  
示例：
```
struct A {
    int x;
    int y = 0;
    int z;

    A(int i): x(i) {  // Non-compliant, Missing the initialization for ‘z’
    }
};
```
例中构造函数没有对 z 初始化是不符合要求的，尤其是公有成员出现这种问题时会造成更大的风险。  
  
建议所有成员都在声明处初始化：
```
struct A {
    int x = 0;  // Good
    int y = 0;  // Good
    int z = 0;  // Good

    A(int i): x(i) {  // Compliant
    }
};
```
<br/>
<br/>

#### 参考
CWE-908  
CWE-824  
C++ Core Guidelines C.41  
<br/>
<br/>

### <span id="ID_illMemberCall">▌R8.11 基类对象构造完毕之前不可调用成员函数</span>

ID_illMemberCall&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

基类对象未构造完毕时调用成员函数会导致标准未定义的行为。  
  
示例：
```
struct A {
    A(int);
};

struct B: A {
    B(): A(member()) {  // Non-compliant, undefined behavior
    }

    int member();
};
```
例中成员函数的返回值作为基类构造函数的参数，而这时基类对象尚未构造，相当于成员函数的调用者没有被初始化，这是一种逻辑错误。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 12.6.2(13)-undefined  
<br/>
<br/>

### <span id="ID_illMemberAccess">▌R8.12 在面向构造或析构函数体的 catch handler 中不可访问非静态成员</span>

ID_illMemberAccess&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: function error

<hr/>

当流程进入面向构造或析构函数体的 catch handler 时，非静态成员的生命周期已结束，如果继续访问会导致标准未定义的行为。  
  
示例：
```
class A {
    int i = 0;

public:
    A() try {        // Function-try-block
        ....
    } catch (...) {  // A handler of the function-try-block
        access(i);   // Non-compliant, ‘i’ may no longer exist 
    }

   ~A() try {
        ....
    } catch (...) {
        access(i);   // Non-compliant, ‘i’ may no longer exist 
    }
};
```
例中 access(i) 等访问是有问题的。  
  
应调整实现或将“function\-try\-block”改为普通“try\-block”：
```
A::A() {
    try {
        ....
    } catch (...) {
        access(i);   // Compliant
    }
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 15.3(10)-undefined  
<br/>

#### 参考
MISRA C++ 2008 15-3-3  
<br/>
<br/>

### <span id="ID_disorderedInitialization">▌R8.13 成员初始化应遵循声明的顺序</span>

ID_disorderedInitialization&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: function error

<hr/>

类成员的初始化顺序是按声明的顺序进行的，如果用后面的成员初始化前面的成员，就会造成错误。  
  
示例：
```
struct T {
    int* p;
    size_t n;

    T(size_t s): n(s), p(new int[n])  // Non-compliant
    {}
};
```
虽然在初始化列表中 n 在 p 的前面，但实际上 n 仍然在 p 之后被初始化，“new int\[n\]”会造成严重错误。  
  
应改为：
```
struct T {
    size_t n;
    int* p;

    T(size_t s): n(s), p(new int[n])  // Compliant
    {}
};
```
调整了 n 和 p 的声明顺序，使 n 先于 p 初始化即可解决问题。  
  
几个特例：
```
struct A {
    int*& a;
    int*  b;
    int*  c;
    int   d[123];
    
    A(int): a(b), b(c), c(d) {}
};
```
a 为引用， b 的地址在初始化之前就确定了，所以“a(b)”没问题  
b 为指针，用 c 的值初始化 b 的值是不对的  
d 为数组，也是一个地址，所以“c(d)”没有问题
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 12.6.2(5)  
ISO/IEC 14882:2011 12.6.2(10)  
<br/>

#### 参考
C++ Core Guidelines C.47  
<br/>
<br/>

### <span id="ID_virtualCallInConstructor">▌R8.14 在构造函数中不应调用虚函数</span>

ID_virtualCallInConstructor&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

虚函数的多态性在构造函数中不生效，而且在构造函数中调用纯虚函数会导致标准未定义的行为。  
  
派生类重写的虚函数与派生类对象紧密相关，但执行基类构造函数时，派生类对象尚未构造完毕，所以基类构造函数不能调用派生类重写的虚函数，标准规定构造函数只能调用当前类或基类的虚函数，而调用未实现的纯虚函数则会导致未定义的行为。  
  
示例：
```
class A {
    int a, b;
    virtual int foo() = 0;
    virtual int bar() { return 0; }

public:
    A():
     a(foo()),    // Non-compliant, undefined behavior
     b(bar()) {   // Non-compliant, ‘b’ is always 0
     }
};

class B: public A {
    int foo() override { return 1; }   // Invalid
    int bar() override { return 2; }   // Invalid
};
```
派生类重写的虚函数在基类的构造函数中不生效，也会使维护者产生相当大的误解。
<br/>
<br/>

#### 参考
C++ Core Guidelines C.82  
Effective C++ item 9  
<br/>
<br/>

### <span id="ID_virtualCallInDestuctor">▌R8.15 在析构函数中不应调用虚函数</span>

ID_virtualCallInDestuctor&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

虚函数的多态性在析构函数中不生效，而且在析构函数中调用纯虚函数会导致标准未定义的行为。  
  
派生类重写的虚函数与派生类对象紧密相关，但执行基类析构函数时，对象属于派生类的成员已被析构，所以基类析构函数不能调用派生类重写的虚函数，标准规定析构函数只能调用当前类或基类的虚函数，而调用未实现的纯虚函数则会导致未定义的行为。  
  
示例：
```
class A {
    virtual void clear() {}

public:
    A() {}
   ~A() { clear(); }   // Non-compliant
};

class B: public A {
    int* p = new int[8];
    void clear() override { delete[] p; }   // Invalid
};
```
虽然派生类 B 重写了 clear 函数，但在基类 A 的析构函数中不生效，相关内存没有被正确释放。  
  
应将基类的析构函数设为虚函数，在派生类的析构函数中释放资源：
```
class A {
    ....
    virtual ~A() {}
};

class B: public A {
    ....
   ~B() { delete[] p; }   // Compliant
};
```
<br/>
<br/>

#### 参考
C++ Core Guidelines C.82  
Effective C++ item 9  
<br/>
<br/>

### <span id="ID_exitCallInDestructor">▌R8.16 在析构函数中避免调用 exit 函数</span>

ID_exitCallInDestructor&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

程序调用 exit 后，具有静态或线程存储期的对象开始析构，如果这种对象的析构函数再调用 exit 会导致标准未定义的行为，为规避意料之外的错误，应统一要求在析构函数中避免直接或间接地调用 exit。  
  
示例：
```
class T {
    ....

public:
   ~T() {
        exit(1);  // Non-compliant
    }
};
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.6.1(4)-undefined  
ISO/IEC 14882:2011 3.6.1(4)-undefined  
<br/>
<br/>

### <span id="ID_sideEffectCopyConstructor">▌R8.17 拷贝构造函数应避免实现复制之外的功能</span>

ID_sideEffectCopyConstructor&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

拷贝构造函数的执行在某些情况下可被省略，其执行次数可能与预期不符，复制之外的功能也可能无法生效。  
  
示例：
```
class A {
    int i;
    static int s;

public:
    A();
    A(const A& rhs) {
        i = rhs.i;     // Compliant
        s++;           // Non-compliant
    }
};
```
例中拷贝构造函数对静态成员有所读写，这种复制之外的功能是不符合要求的。  
  
如果按下列方式使用相关类：
```
A foo();

int main() {
    A a = foo();   // Copy elision
    ....
}
```
用 foo 函数返回的临时对象构造对象 a，理论上应执行拷贝构造函数，但标准允许编译器将临时对象直接作为对象 a，省略拷贝构造函数的执行以提高效率，这种优化称为“[copy elision](https://en.wikipedia.org/wiki/Copy_elision)”，复制之外的功能会因此无法生效。在 C\+\+17 之前，是否执行这种优化由实现定义，从 C\+\+17 开始，在某些情况下必须执行这种优化，具体可参见“[mandatory copy elision](https://en.cppreference.com/w/cpp/language/copy_elision#Mandatory_elision_of_copy.2Fmove_operations)”。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 12.8(15)-implementation  
ISO/IEC 14882:2011 12.8(31)-implementation  
ISO/IEC 14882:2017 15.8.3(1)-implementation  
<br/>

#### 参考
MISRA C++ 2008 12-8-1  
<br/>
<br/>

### <span id="ID_this_selfJudgement">▌R8.18 拷贝赋值运算符应处理参数是自身对象时的情况</span>

ID_this_selfJudgement&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

如果拷贝赋值运算符的参数是自身对象，需防止资源分配和回收方面的冲突。  
  
示例：
```
class A { .... };

void foo(A* p, A* q) {
    *p = *q;             // If ‘p’ and ‘q’ point to the same object ...
}
```
设例中 A 是需要深拷贝的类，其赋值运算符往往需要先释放自身的资源，再复制参数的资源，如果参数就是自身，则需要避免资源被释放，可在赋值运算符中判断 this 与参数地址是否相同：
```
A& A::operator = (const A& rhs) {
    if (this != &rhs) {             // Required
        ....
    }
    return *this;
}
```
如果 A 的拷贝构造函数和交换方法齐备，也可按“复制 \- 交换”模式实现：
```
A& A::operator = (const A& rhs) {
    A tmp(rhs);
    this->swap(tmp);                // Good
    return *this;
}
```
利用创建临时对象并与之交换的方法，也有效规避了冲突，这种方法使各函数更专注于自己的职责，不必重复编写分配和回收相关的代码，建议采用这种方法。
<br/>
<br/>

#### 参考
C++ Core Guidelines C.62  
<br/>
<br/>

### <span id="ID_invalidWrite">▌R8.19 不应存在无效的写入操作</span>

ID_invalidWrite&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

对于内存中的数据，写入之后应被读取，如果出现：  
1. 写入后未经读取再次被无条件写入  
2. 写入后未经读取即结束了函数的执行  
  
这种写入是无效的，出现这种问题往往意味着逻辑错误或功能不完整。  
  
对象在初始化时的写入和 volatile 型数据可不受本规则限制。  
  
示例：
```
void foo(int& a, int& b) {
    ....
    a = 123;   // Non-compliant
    a = 456;
    ....
}
```
例中参数 a 被赋值为 123 之后，又被无条件地赋值为 456，显然第一次赋值是没有意义的，很有可能是漏掉了什么。  
  
又如：
```
int bar() {
    int i = ....;
    return i++;     // Non-compliant
}
```
例中 bar 函数返回变量 i 自增前的值，自增运算是没有意义的。  
  
对象的初始化可不受本规则限制，如：
```
int baz() {
    int n = 0;      // OK
    if (cond) {
        n = 123;
    } else {
        n = 456;
    }
    return n;
}
```
例中局部变量 n 初始化后经由 if\-else 分枝，在其两个分枝中都被赋值，也相当于被无条件写入，但在声明处初始化是值得提倡的，故这种情况不受本规则限制。
<br/>
<br/>
<br/>

### <span id="ID_missingSideEffect">▌R8.20 不应存在没有副作用的语句</span>

ID_missingSideEffect&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: function error

<hr/>

不能对程序状态产生影响的语句称为无“[副作用（side effect）](https://en.wikipedia.org/wiki/Side_effect_(computer_science))”的语句，往往属于笔误或调试痕迹，应当修正或去除。  
  
示例（设 a、b、p 为变量或指针）：
```
a == b;  // Non-compliant
```
单纯的判等是没有副作用的，很可能是赋值语句的笔误。  

```
*p++;    // Non-compliant
```
单纯从某个地址进行读取是没有副作用的，这是一种对运算符优先级理解不当造成的常见错误，应改为 (\*p)\+\+;  

```
p->fun;  // Non-compliant
```
由变量名或无实参列表的函数名作为一个语句是没有副作用的，此语句应改为正确的函数调用。  

```
+a;      // Non-compliant
```
正号是没有副作用的，此句很可能应为 \+\+a;  
  
如果语句为逻辑与表达式，左子表达式可以作为右子表达式的条件，故左子表达式可以无副作用，而右子表达式一定要有副作用，如：
```
p && p->fun();  // OK
p && p->fun;    // Non-compliant
p->fun() && p;  // Non-compliant
```
如果语句为逻辑或表达式，则要求其左右子表达式均有副作用，如：
```
p || p->fun();  // Non-compliant
p || p->fun;    // Non-compliant
p->fun() || p;  // Non-compliant
```
<br/>
<br/>

#### 参考
CWE-1164  
CWE-482  
MISRA C 2004 14.2  
MISRA C 2012 2.2  
MISRA C++ 2008 0-1-9  
<br/>
<br/>

### <span id="ID_unreachableCode">▌R8.21 不应存在得不到执行机会的代码</span>

ID_unreachableCode&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: function error

<hr/>

得不到执行机会的代码是没有意义的，往往意味着逻辑错误。  
  
这种代码的成因主要有：  
1. 所在函数无法被调用  
2. 之前的所有分枝都提前结束了函数的执行  
3. 之前的必经分枝中存在不会结束执行的代码  
4. 所在分枝的条件恒为假  
5. 所在分枝被其他分枝遮盖  
  
第 1 点特化为：ID\_staticNotUsed、ID\_privateNotUsed  
第 4 点特化为：ID\_constLogicExpression、ID\_invalidCondition、ID\_switch\_caseOutOfRange  
第 5 点特化为：ID\_if\_identicalCondition、ID\_if\_hiddenCondition  
  
示例：
```
int fun() {
    if (cond) {
        return 0;
    } else {
        return 1;
    }
    statements    // Non-compliant, unreachable
}
```
例中 statements 之前的所有分枝都会结束函数的执行，所以 statements 不会被执行。  
  
例外：  
在多分枝结构的末尾，统一安置一条结束函数执行的语句是一种好的编程习惯，即当 statements 只包含一条 return 或 throw 语句时可以不算作违规。  
  
另外，在正式代码中不应存在如下形式的代码：
```
if (false) { .... }      // Non-compliant
while (false) { .... }   // Non-compliant
for (;false;) { .... }   // Non-compliant
```
也不应该在 return 语句之后存在其他语句，这种代码如果不是被人恶意篡改，就是出于某种目的将本已无效的代码遗留了下来，可参见 ID\_constLogicExpression、ID\_invalidCondition 的进一步讨论。  
  
建议时刻保持代码的整洁，并将维护过程中的变动及时地保存在版本管理系统中，这样可以清晰地查看各版本之间的变动，而如果将无效代码与有效代码混在一起，势必造成维护的负担。
<br/>
<br/>

#### 相关
ID_staticNotUsed  
ID_privateNotUsed  
ID_constLogicExpression  
ID_invalidCondition  
ID_switch_caseOutOfRange  
ID_if_identicalCondition  
ID_if_hiddenCondition  
<br/>

#### 参考
CWE-561  
MISRA C 2004 14.1  
MISRA C 2012 2.1  
MISRA C++ 2008 0-1-1  
<br/>
<br/>

### <span id="ID_notAllBranchReturn">▌R8.22 有返回值的函数其所有分枝都应有明确的返回值</span>

ID_notAllBranchReturn&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: function error

<hr/>

当函数的某个分枝没有明确的返回值时会导致标准未定义的行为。  
  
示例：
```
int fun() {
    if (condition1) {
        return 1;
    }
    else if (condition2) {
        return 2;
    }
    else if (condition3) {  // Non-compliant if no return value
    }
    else {
        return 4;
    }
}
```
当符合 condition3 的条件时，fun 函数的调用者将得到一个错误的返回值。  
  
例外：  
在 main 函数的结尾如果不显式调用 return 语句，编译器会自动添加 return 0，故对 main 函数不作要求。
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 6.9.1(12)-undefined  
ISO/IEC 9899:1999 5.1.2.2.3(1)  
ISO/IEC 9899:2011 6.9.1(12)-undefined  
ISO/IEC 9899:2011 5.1.2.2.3(1)  
ISO/IEC 14882:2011 6.6.3(2)-undefined  
ISO/IEC 14882:2017 9.6.3(2)-undefined  
<br/>

#### 参考
CWE-394  
MISRA C 2004 16.8  
MISRA C 2012 17.4  
MISRA C++ 2008 8-4-3  
<br/>
<br/>

### <span id="ID_localAddressFlowOut">▌R8.23 不可返回局部对象的地址或引用</span>

ID_localAddressFlowOut&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: function error

<hr/>

对象的生命周期结束后，其地址或引用也会失效，如果继续访问会导致标准未定义的行为。  
  
示例：
```
int* foo() {
    int i = 0;    // Local object
    ....
    return &i;    // Non-compliant
}

int& bar() {
    int i = 0;    // Local object
    ....
    return i;     // Non-compliant
}

int&& baz() {
    int i = 0;    // Local object
    ....
    return std::move(i);    // Non-compliant
}
```
局部对象的生命周期在函数返回后结束，返回与局部对象相关的指针或引用是不符合要求的。  
  
注意，除了 return 语句，throw、赋值等表达式也受本规则限制，禁止将内层作用域中的地址向外层作用域传递，如：
```
char* global;

void fun() {
    char local[] = "....";
    global = local;          // Non-compliant
}
```
例中 local 是局部数组，函数返回后，全局指针会指向无效的内存区域。  
  
另外，将对象地址传入异步过程时也需要注意对象的生命周期，参见 ID\_illLifetime。
<br/>
<br/>

#### 相关
ID_danglingDeref  
ID_illLifetime  
<br/>

#### 依据
ISO/IEC 9899:1999 6.2.4(2)-undefined  
ISO/IEC 9899:2011 6.2.4(2)-undefined  
<br/>

#### 参考
CWE-562  
C++ Core Guidelines F.43  
MISRA C 2012 18.6  
MISRA C++ 2008 7-5-1  
MISRA C++ 2008 7-5-2  
<br/>
<br/>

### <span id="ID_unsuitableCapture">▌R8.24 合理设置 lambda 表达式的捕获方式</span>

ID_unsuitableCapture&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

如果 lambda 表达式只在函数内部使用，可采用捕获引用的方式；如果 lambda 表达式可以超出函数作用域，应采用捕获值的方式。  
  
示例：
```
auto foo() -> function<int()> {
    int i = 0;
    ....
    return [&]() { return ++i; };  // Non-compliant
}
```
例中的 lambda 表达式引用了局部变量 i，但返回后 i 的地址不再有效，会引发标准未定义的行为。  
  
另外，要注意解引用指针造成的间接引用：
```
class A {
    int i;

public:
    auto bar() {
        return [=]() { return i; };  // Bad
    }
};
```
例中的 lambda 表达式通过值捕获变量，this 指针也被捕获，成员变量 i 是通过 this 指针的隐式解引用获取到的，如果 lambda 表达式在 this 指针的生命周期之外执行，就会造成错误。  
  
应改为：
```
auto A::bar() {
    return [*this]() { return i; };  // OK
}
```
如果需要捕获 this 指针，则应显式捕获所有相关变量，避免使用“\[=\]”。
<br/>
<br/>

#### 相关
ID_localAddressFlowOut  
<br/>

#### 依据
ISO/IEC 14882:2011 5.1.2(7)  
<br/>

#### 参考
C++ Core Guidelines F.52  
C++ Core Guidelines F.53  
C++ Core Guidelines F.54  
<br/>
<br/>

### <span id="ID_returnRValueReference">▌R8.25 函数返回值不应为右值引用</span>

ID_returnRValueReference&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: function suggestion

<hr/>

函数返回右值引用的实际价值有限，而且很容易产生错误。  
  
示例：
```
class A { .... };

A&& foo() {         // Non-compliant
    return A();
}

void bar() {
    A&& a = foo();
    access(a);      // Undefined behavior, ‘a’ refers to an invalid object
}
```
例中 foo 函数返回类型为右值引用，这种情况下返回临时对象一定是错误的，临时对象在返回前析构，返回的是无效引用。  
  
也不应返回局部对象的右值引用，如：
```
A&& baz() {         // Non-compliant
    A a;
    ....
    return std::move(a);
}
```
和返回临时对象一样，对象 a 在函数返回前析构，返回的也是无效引用。  
  
应直接返回对象，而不是对象的右值引用：
```
A foo() {           // Compliant
    return A();
}

A baz() {           // Compliant
    A a;
    ....
    return a;
}
```
对于函数引用的参数，或函数作用域之外的对象，如果通过 move 返回右值引用，如：
```
A&& baz(A& a) {     // Non-compliant
    access(a);
    return std::move(a);
}
```
这种情况在运行机制上可能没有问题，但满足的实际需求较为有限，而且相当于将 access(a) 和 move(a) 两种事务合在一个函数中，在某种程度上违反了“[单一职责原则](https://en.wikipedia.org/wiki/Single-responsibility_principle)”。  
  
综上所述，应统一要求函数不应返回右值引用。
<br/>
<br/>

#### 相关
ID_localAddressFlowOut  
<br/>

#### 参考
C++ Core Guidelines F.45  
<br/>
<br/>

### <span id="ID_returnConstObject">▌R8.26 函数返回值不应为常量对象</span>

ID_returnConstObject&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: function suggestion

<hr/>

函数返回常量对象不利于移动构造或移动赋值等机制，也可能本意是返回引用，但遗漏了引用符号。  
  
示例：
```
const vector<int> fun() {  // Non-compliant
    return { 1, 2, 3 };
}

vector<int> obj(fun());    // Call ‘vector(const vector&)’
```
fun 返回 const 对象，构造 obj 对象时只能进行深拷贝，无法利用移动构造等特性。  
  
应改为：
```
vector<int> fun() {        // Compliant
    return { 1, 2, 3 };
}

vector<int> obj(fun());    // Call ‘vector(vector&&)’, more efficient
```
这样可以利用移动构造函数提高效率。  
  
对于遵循 C\+\+11 之前标准的代码，也不应返回常量对象，函数返回的对象本来就需要通过常量引用或传值的方式被后续代码使用，将返回值设为常量的意义不大。
<br/>
<br/>

#### 相关
ID_returnSuperfluousConst  
<br/>

#### 参考
C++ Core Guidelines F.20  
<br/>
<br/>

### <span id="ID_returnSuperfluousConst">▌R8.27 函数返回值不应为基本类型的常量</span>

ID_returnSuperfluousConst&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

基本类型的对象作为返回值时，本来就是不可被修改的右值，const 修饰符是多余的。  
  
出现这种问题说明设计与使用存在一定的偏差，也可能本意是返回引用或指针，而书写时漏掉了相关符号。  
  
本规则是 ID\_returnConstObject 的特化。  
  
示例：
```
const int* foo();  // Compliant
const int bar();   // Non-compliant, ‘const’ is superfluous

class A {
    ....

public:
    int& fun();
    const int fun() const;  // Non-compliant, missing ‘&’
};
```
<br/>
<br/>

#### 相关
ID_returnConstObject  
<br/>

#### 依据
ISO/IEC 14882:2003 3.10(5)  
ISO/IEC 14882:2011 3.10(1)  
<br/>
<br/>

### <span id="ID_returnOdd">▌R8.28 被返回的表达式应与函数的返回类型相符</span>

ID_returnOdd&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

不应出现下列情况：  
 - 返回类型为 bool，却返回了非 true 非 false、非 0 非 1 的常量  
 - 返回类型为指针，却返回了非 0、非 NULL、非 nullptr 的常量  
 - 返回类型为整数，却返回了 NULL、true、false 等常量  
  
示例：
```
bool foo() { return NULL; }   // Non-compliant
long bar() { return false; }  // Non-compliant
int* baz() { return '\0'; }   // Non-compliant
```
这种问题可能是在维护过程中产生的，也可能意味着逻辑错误，而且很容易造成误导，需谨慎对待。
<br/>
<br/>

#### 参考
MISRA C++ 2008 4-10-1  
<br/>
<br/>

### <span id="ID_returnSameConst">▌R8.29 被返回的表达式不应为相同的常量</span>

ID_returnSameConst&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

被返回的表达式均为相同的常量是没有意义的。  
  
示例：
```
bool foo(int a) {
    if (a > 100) {
        return true;
    }
    if (a > 50) {
        return true;
    }
    return true;   // Non-compliant, all the values returned are the same
}
```
<br/>
<br/>
<br/>

### <span id="ID_unsuitableReturn">▌R8.30 具有 noreturn 属性的函数不应返回</span>

ID_unsuitableReturn&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

具有 noreturn 属性的函数返回会导致标准未定义的行为。  
  
示例：
```
[[noreturn]] void foo() {  // Use _Noreturn in C
    if (condition) {
        abort();
    }
}       // Non-compliant, the function returns if ‘condition’ is false
```
noreturn 表示不会返回，编译器可能不会为 foo 函数生成跳转回调用处的指令，一旦 foo 函数返回就会造成严重错误。
<br/>
<br/>

#### 相关
ID_unsuitableReturnType  
<br/>

#### 依据
ISO/IEC 14882:2011 7.6.3(2)-undefined  
<br/>
<br/>

### <span id="ID_unsuitableReturnType">▌R8.31 具有 noreturn 属性的函数返回类型只应为 void</span>

ID_unsuitableReturnType&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

返回类型不是 void 说明函数有返回值，与 noreturn 属性矛盾。  
  
示例：
```
_Noreturn    int foo();     // Non-compliant in C
[[noreturn]] int bar();     // Non-compliant in C++
```
例中函数的返回类型为 int，与 noreturn 属性矛盾，也会对使用者造成困扰。  
  
应改为：
```
_Noreturn    void foo();    // Compliant in C
[[noreturn]] void bar();    // Compliant in C++
```
<br/>
<br/>

#### 相关
ID_unsuitableReturn  
<br/>

#### 依据
ISO/IEC 14882:2011 7.6.3(2)-undefined  
<br/>
<br/>

### <span id="ID_exitHandlerNoReturn">▌R8.32 由 atexit、at_quick_exit 指定的处理函数应正常返回</span>

ID_exitHandlerNoReturn&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

如果 atexit、at\_quick\_exit 指定的处理函数未正常返回，会导致标准未定义的行为。  
  
示例：
```
void handler() {
    exit(1);      // Non-compliant
}

int main() {
    atexit(handler);
}
```
例中程序在调用 exit 时会执行 handler，而 handler 又调用 exit，在逻辑上形成无限递归，其后果在标准中是未定义的。  
  
又如：
```
jmp_buf buf;

void handler() {
    longjmp(buf, 1);   // Non-compliant
}

int main() {
    atexit(handler);
    if (setjmp(buf) == 0) {
        return 0;
    }
    return 1;
}
```
例中 main 返回后会调用 handler，而 handler 又调用 longjmp 跳回 main 函数，在逻辑上形成死循环。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 7.22.4.4(2 3)-undefined  
<br/>

#### 参考
SEI CERT ENV32-C  
<br/>
<br/>

### <span id="ID_functionSpecialization">▌R8.33 函数模板不应被特化</span>

ID_functionSpecialization&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

特化的函数模板不参与重载函数的选取，不属于常规用法，且容易造成混乱。  
  
示例：
```
template <class T>
int foo(T) {         // #1
    return 0;
}

template <class T>
int foo(T*) {        // #2
    return 1;
}

template <>
int foo<int*>(int*) {   // #3, non-compliant, specialization of #1
    return 2;
}

int main() {
    int* p = nullptr;
    cout << foo(p) << '\n';   // What is output?
}
```
输出 1，特化的函数模板不参与重载函数的选取，所以只会在 `#1` 和 `#2` 中选取，foo(p) 与 `#2` 更贴近，而 `#3` 是 `#1` 的特化，所以不会选取 `#3`，这种情况下 `#3` 是无效的。  
  
应去除对函数模板的特化，改为普通重载函数：
```
int foo(int*) {   // #3, compliant, safe and brief
    return 2;
}
```
这样例中 main 函数会输出 2。  
  
如果某些特殊情况确实需要特化模板，不妨将函数委托给模板类实现，通过特化模板类实现特殊的需求，参见 ID\_narrowCast 的示例。
<br/>
<br/>

#### 参考
C++ Core Guidelines T.144  
MISRA C++ 2008 14-8-1  
<br/>
<br/>

### <span id="ID_tooManyLabels">▌R8.34 函数的标签数量应在规定范围之内</span>

ID_tooManyLabels&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

标签过多意味着函数内部的跳转逻辑过于复杂，违反结构化设计理念，应适当重构。  
  
对于 C 代码，建议一个函数只用一个标签作为函数统一出口，对于 C\+\+ 代码，不建议使用标签。  
  
示例：
```
void foo()
{
L0:
    ....
L1:
    ....   // Lots of labels require lots of gotos
    ....   // Lots of gotos make functions terrible
L100:
    ....
}
```
<br/>
<br/>

#### 配置
maxLabelCount：标签数量上限，超过则报出  
<br/>
<br/>

### <span id="ID_tooManyLines">▌R8.35 函数的行数应在规定范围之内</span>

ID_tooManyLines&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

函数体过大违反模块化编程理念，使人难以阅读，更不便于维护，很有可能隐藏着各种错误，应适当重构。  
  
示例：
```
int main()
{
    // ... 3000 lines ...
    // Who has the courage to read?
}
```
建议函数体不超过 60 行，以不需要拖拽滚动条就可以在屏幕上完整显示为宜。
<br/>
<br/>

#### 配置
maxLineCount：行数上限，超过则报出  
<br/>

#### 参考
C++ Core Guidelines F.2  
C++ Core Guidelines F.3  
<br/>
<br/>

### <span id="ID_tooManyLambdaLines">▌R8.36 lambda 表达式的行数应在规定范围之内</span>

ID_tooManyLambdaLines&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

复杂的 lambda 表达式与其调用者的代码混在一起时，是难以阅读的，引入 lambda 表达式的目的应该是“化简”，否则应使用普通函数。  
  
示例：
```
void foo()
{
    auto f0 = []() {
        // ... Many lines ...
    };
        // ... Many lambdas ...
        // ... Even lambdas nest lambdas ...
    auto f100 = []() {
        // ...
    };
    // Tut tut, this is a function, not a namespace,
    // use common functions instead
}
```
建议 lambda 表达式不超过 5 行，一个函数中不应有多个复杂的 lambda 表达式。
<br/>
<br/>

#### 配置
maxLambdaLineCount：lambda 表达式行数上限，超过则报出  
<br/>
<br/>

### <span id="ID_tooManyParams">▌R8.37 函数参数的数量应在规定范围之内</span>

ID_tooManyParams&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

函数参数过多，意味着：  
 - 缺少合理的抽象机制：应将多而零散的参数按其内在联系封装成对象，从而方便地处理其逻辑关系，而不是简单地线性罗列  
 - 违反单一职责原则：参数越多，函数处理的事务自然越多，代码的可维护性自然越差  
  
建议可供外部使用的全局函数、public 或 protected 成员函数的参数不超过 4 个，内部使用的 static 函数、private 成员函数的参数不超过 8 个。  
  
示例：
```
void foo(A a, B b, C c, D d, E e) {   // Bad
    ....
}
```
当函数参数过多时，应按参数的逻辑职责进行封装。假设 a 和 b 有直接逻辑关系，c、d、e 有直接逻辑关系，不妨将 a 和 b 封装成一个类，c、d、e 封装成一个类，在类的成员函数中实现相关功能，可更为清晰直观地保证逻辑关系的正确性：
```
class X {
    ....    // Members and methods for ‘a’, ‘b’ ...
};

class Y {
    ....    // Members and methods for ‘c’, ‘d’, ‘e’ ... 
};

void foo(X x, Y y) {   // Good
    x.methods();
    y.methods();
}
```
<br/>
<br/>

#### 配置
maxInnerFunParamCount：static 函数或 private 成员函数参数数量上限，超过则报出  
maxParamCount：参数数量上限，超过则报出  
<br/>

#### 参考
C++ Core Guidelines F.2  
C++ Core Guidelines I.23  
<br/>
<br/>

### <span id="ID_complexInlineFunction">▌R8.38 不应定义过于复杂的内联函数</span>

ID_complexInlineFunction&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: function suggestion

<hr/>

不适合将函数声明为内联函数的情况：  
 - 行数超过指定限制  
 - 存在循环或异常处理语句  
 - 存在 switch 分枝语句  
 - 函数存在递归实现  
  
建议内联函数的实现不要超过 3 个语句。
<br/>
<br/>

#### 配置
maxInlineFunctionLineCount：内联函数行数上限，超过则报出  
<br/>

#### 参考
C++ Core Guidelines F.5  
<br/>
<br/>

### <span id="ID_recursion">▌R8.39 避免递归实现</span>

ID_recursion&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

递归实现，如函数直接或间接地调用自身，易导致难以控制的堆栈溢出等错误。  
  
示例：
```
size_t foo(size_t n) {
    return n + foo(n - 1);  // Non-compliant
}
```
例中 foo 函数无条件地调用自身，是一种逻辑错误，导致无限的递归调用。  
  
又如：
```
size_t bar(size_t n) {
    if (n > 1) {
        return n + bar(n - 1);  // Non-compliant
    }
    return n;
}
```
例中 bar 函数设置了递归条件，但仍是不可取的，当参数 n 较大时仍然可以造成堆栈溢出错误。  
  
对于一般的功能，应尽量采用迭代、堆栈等非递归手段实现，对于难以使用非递归方式实现的特殊算法，应做到递归深度可控。
<br/>
<br/>

#### 参考
MISRA C 2012 17.2  
MISRA C++ 2008 7-5-4  
<br/>
<br/>

### <span id="ID_functionRepetition">▌R8.40 避免重复的函数实现</span>

ID_functionRepetition&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: function suggestion

<hr/>

不同的函数代码却完全相同或过于相似是不利于维护的。
<br/>
<br/>

#### 参考
CWE-1041  
C++ Core Guidelines ES.3  
<br/>
<br/>

## <span id="control">9. Control</span>

### <span id="control.if">9.1 If</span>

### <span id="ID_if_semicolon">▌R9.1.1 if 语句不应被分号隔断</span>

ID_if_semicolon&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

if 语句不应被分号隔断。  
  
示例：
```
if (condition);  // Non-compliant, see the semicolon
{
    ....
}
```
一个分号使整个 if 语句失效，这可能是笔误，也可能是需求变化不再需要条件判断了，在维护过程中加入了分号，形成了令人费解的残留代码，也不能排除是有人恶意篡改了代码，应立即修正。
<br/>
<br/>

#### 参考
CWE-670  
<br/>
<br/>

### <span id="ID_if_identicalCondition">▌R9.1.2 在 if...else-if 分枝中不应有重复的条件</span>

ID_if_identicalCondition&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

if...else\-if 分枝的条件不应有重复，否则相同条件排在前面的分枝会得以执行，而排在后面的分枝得不到执行机会。  
  
示例：
```
if (condition1) {
    branch1
}
else if (condition2) {
    branch2
}
else if (condition1) {  // Non-compliant, see the previous ‘condition1’
    branch3
}
else {
    branch4
}
```
例中 branch1 和 branch3 的条件是相同的，所以 branch3 不会被执行。  
  
此问题为常见笔误，多数由复制粘贴造成，也可能是维护代码时对之前的逻辑不够了解造成的错误。
<br/>
<br/>

#### 相关
ID_if_hiddenCondition  
<br/>

#### 参考
CWE-670  
CWE-561  
<br/>
<br/>

### <span id="ID_if_hiddenCondition">▌R9.1.3 在 if...else-if 分枝中不应有被遮盖的条件</span>

ID_if_hiddenCondition&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

if...else\-if 分枝中，如果前面的条件被满足，后面的分枝就不会被执行，所以如果前面的条件是后面条件的一部分，或者前面的条件包含后面的条件，即使后面的条件可以被满足，其分枝也得不到执行机会。  
  
示例：
```
if (condition1) {
    branch1
}
else if (condition2) {
    branch2
}
else if (condition1 && condition3) {  // Non-compliant, see the previous ‘condition1’
    branch3
}
else {
    branch4
}
```
如果 condition1 为 true，branch1 将得以执行，branch3 不会被执行，如果 condition1 为 false，branch3 还是不会被执行，称 branch3 被 condition1 遮盖了，branch3 永远不会得到执行机会。  
  
如果前面的条件包含后面的条件，同样也会遮盖后面的条件，如：
```
if (condition1 || condition2) {
    branch1
}
else if (condition2) {  // Non-compliant, see the previous ‘condition2’
    branch2
}
else {
    branch3
}
```
同理，branch2 永远也不会被执行。
<br/>
<br/>

#### 相关
ID_if_identicalCondition  
<br/>

#### 参考
CWE-670  
CWE-561  
<br/>
<br/>

### <span id="ID_if_identicalBlock">▌R9.1.4 if 分枝和 else 分枝的代码不应完全相同</span>

ID_if_identicalBlock&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

if 分枝和 else 分枝完全相同会使条件判断失去意义，往往是由复制粘贴造成的错误。  
  
示例：
```
if (condition) {
    branch
}
else {
    branch  // Non-compliant
}
```
例中 branch 表示完全相同的代码，需修正本应存在的差异，或去掉 if\-else 结构。
<br/>
<br/>

#### 相关
ID_if_identicalElseIfBlock  
ID_if_identicalImplicitElseBlock  
<br/>

#### 参考
CWE-670  
<br/>
<br/>

### <span id="ID_if_identicalElseIfBlock">▌R9.1.5 if...else-if 各分枝的代码不应完全相同</span>

ID_if_identicalElseIfBlock&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

内容完全相同的分枝是没有意义的，也可能是由复制粘贴造成的错误。  
  
示例：
```
if (condition1) {
    branch1
}
else if (condition2) {
    branch2
}
else {
    branch1  // Non-compliant
}
```
例中 condition1 对应分枝和 else 分枝的内容完全相同，应该合并成一个分枝，或修正本应存在的差异：
```
if (condition2) {
    branch2
}
else {
    branch1  // Compliant
}
```
例外：  
如果分枝内容较少，为了使代码更清晰可以接受适当的重复，但如果分枝内容很多就不应重复了，审计工具不妨指定一个数量限制，当重复分枝的符号数量超过这个限制时算作违规，否则放过。
<br/>
<br/>

#### 配置
blockTokenCountThreshold：重复分枝符号数量限制，不检查符号数量小于该值的分枝  
<br/>

#### 相关
ID_if_identicalBlock  
<br/>

#### 参考
CWE-670  
<br/>
<br/>

### <span id="ID_if_identicalImplicitElseBlock">▌R9.1.6 if 分枝和隐含的 else 分枝代码不应完全相同</span>

ID_if_identicalImplicitElseBlock&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

带有 return、throw 或 break 等子句的 if 语句，其同一作用域的后续代码相当于它的 else 分枝，显然这种隐含的 else 分枝与 if 分枝完全相同是没有意义的，很可能是由复制粘贴造成的错误。  
  
示例：
```
if (condition) {
    statements
    return;
}
statements  // Non-compliant
return;
```
例中 if 语句之内的 statements 以及 return 语句和 if 语句之外的语句完全相同，这种情况下 if 语句的条件判断是没有意义的，需修正本应存在的差异，或去掉 if 语句。
<br/>
<br/>

#### 相关
ID_if_identicalElseIfBlock  
ID_if_identicalSucceedingBlock  
<br/>

#### 参考
CWE-670  
<br/>
<br/>

### <span id="ID_if_identicalSucceedingBlock">▌R9.1.7 没有 else 子句的 if 语句与其后续代码相同是可疑的</span>

ID_if_identicalSucceedingBlock&emsp;&emsp;&emsp;&emsp;&nbsp;:question: control suspicious

<hr/>

if 语句与其同一作用域的后续代码完全相同是可疑的，可能是由复制粘贴造成的错误。  
  
示例：
```
if (condition) {
    a = SOME_VALUE;
}
a = SOME_VALUE;  // Rather suspicious
```
例中对变量 a 的赋值是没有意义的。
<br/>
<br/>

#### 相关
ID_if_identicalImplicitElseBlock  
<br/>

#### 参考
CWE-670  
<br/>
<br/>

### <span id="ID_if_commonStatements">▌R9.1.8 if 分枝和 else 分枝的起止语句不应相同</span>

ID_if_commonStatements&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

if 分枝和 else 分枝的起止语句如果相同，则应将其从分枝结构中提取出来，否则重复的代码不利于阅读和维护。  
  
示例：
```
if (condition) {
    foo();
    ....
    bar();
}
else {
    foo();
    ....
    bar();
}
```
if 与 else 分枝的开头和结尾相同，应提取出来：
```
foo();
if (condition) {
    ....
}
else {
    ....
}
bar();
```
当条件分枝中的所有语句都相同时，特化为 ID\_if\_identicalBlock，这种情况往往意味着错误。
<br/>
<br/>

#### 相关
ID_if_identicalBlock  
<br/>

#### 参考
CWE-670  
C++ Core Guidelines ES.3  
<br/>
<br/>

### <span id="ID_if_scope">▌R9.1.9 if 语句作用域的范围不应有误</span>

ID_if_scope&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

if 语句作用域的范围不应有误。  
  
示例：
```
if (condition)
    statement1; statement2;  // Non-compliant

if (condition)
    statement1;
    statement2;  // Non-compliant
```
例中 statement2 不在 if 语句的作用域中，但看起来又和 if 语句相关，这种问题多数是由错误的宏展开或无效的缩进造成的。  
  
为了避免这种问题，if 语句应使用大括号括起来：
```
if (condition) {
    statement1; statement2;  // Compliant
}
```
<br/>
<br/>

#### 相关
ID_if_brace  
<br/>

#### 参考
CWE-483  
<br/>
<br/>

### <span id="ID_if_mayBeElseIf">▌R9.1.10 如果 if 关键字前面是右大括号，if 关键字应另起一行</span>

ID_if_mayBeElseIf&emsp;&emsp;&emsp;&emsp;&nbsp;:question: control suspicious

<hr/>

当 if 关键字前面是右大括号，且 if 关键字与该大括号在同一行时，属于不良换行方式，易造成 else 等关键字的遗漏。  
  
示例：
```
if (condition1) {
    ....
} if (condition2) {  // Rather suspicious, should it be ‘else if’??
    ....
}
```
这种情况很可能是漏掉了 else 关键字，即使没有被漏掉，也应该让 if 关键字另起一行，否则这种换行习惯会增加遗漏 else 关键字的风险，而且可读性较差。
<br/>
<br/>
<br/>

### <span id="ID_if_assignment">▌R9.1.11 if 语句的条件不应为赋值表达式</span>

ID_if_assignment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

虽然语言允许在 if 语句的条件中赋值，但 = 和 == 极易混淆，建议所有产生 bool 型结果的表达式，都不要包含赋值运算符。  
  
示例：
```
void foo(HRESULT r) {
    if (r = S_OK) {     // Non-compliant
        ....
    }
}
```
例中 S\_OK 为常量，在条件中用常量对变量赋值是没有逻辑意义的，如果条件中等号右侧为常量，基本可以判定是 == 被误写成了 =。  
  
又如：
```
if (r = fun()) {   // Non-compliant
    ....
}
```
例中 fun 为函数，这也是一种公认的不良风格，应将赋值表达式拆分出来，或者在 C\+\+ 代码中改为：
```
if (auto r = fun()) {   // Compliant
    ....
}
```
将赋值表达式加上括号表示有意为之，是一种惯用写法：
```
if ((r = fun())) {   // Let it go?
    ....
}
```
审计工具不妨通过配置决定是否放过这种情况。
<br/>
<br/>

#### 配置
allowEnclosedAssignment：为 true 可以放过括号括起来的赋值表达式  
<br/>

#### 参考
CWE-480  
CWE-481  
CWE-783  
<br/>
<br/>

### <span id="ID_if_emptyBlock">▌R9.1.12 if 语句不应为空</span>

ID_if_emptyBlock&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

如果 if 语句没有 else 分枝，且其分枝内容为空，这样的 if 语句无任何意义，即使其条件有副作用，也不应继续保留该 if 结构。  
  
示例：
```
if (a = foo());      // Non-compliant
if (a == bar()) {}   // Non-compliant
```
<br/>
<br/>

#### 参考
CWE-1071  
<br/>
<br/>

### <span id="ID_if_tooManyElseIf">▌R9.1.13 if...else-if 分枝数量应在规定范围之内</span>

ID_if_tooManyElseIf&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

if...else\-if 分枝过多不利于维护，而且执行时各分枝的条件需逐一判断，效率较低，建议改为遵循某种算法的索引结构。  
  
示例：
```
if (rabbit) {
}
else if (hamster) {
}
// ... 3000 branches ...
// Computers have the courage to execute,
// but do you have the courage to read?
else {
}
```
建议 if...else\-if 分枝数量不超过 5 个。
<br/>
<br/>

#### 配置
maxElseIfCount：分枝数量上限，超过则报出  
<br/>
<br/>

### <span id="ID_if_brace">▌R9.1.14 if 分枝中的语句应该用大括号括起来</span>

ID_if_brace&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

组成 if 分枝的语句应为大括号括起来的复合语句，即使该复合语句只包含一条语句。  
  
这是非常好的编程习惯，如果能一直遵循此规则，可以杜绝由错误缩进、宏的错误使用造成的多种问题。  
  
示例：
```
if (cond1)           // Non-compliant
    if (cond2)       // Non-compliant
        action1();
else                 // Non-compliant
    action2();
```
这段代码想表达的逻辑应是：
```
if (cond1) {
    if (cond2) {
        action1();
    }
} else {
    action2();
}
```
但 C/C\+\+ 规定 else 子句与最近的 if 子句配对，所以实际逻辑是：
```
if (cond1) {
    if (cond2) {
        action1();
    } else {
        action2();
    }
}
```
这显然是与预期不符的。  
  
又如：
```
if (cond)      // Non-compliant
    y = 2;
else           // Non-compliant
    x = 3;
    y = 1;
```
注意 else 子句中的 y = 1;  由于缩进的关系此句看起来应该是 else 分枝的一部分，但它实际上并不在 else 的作用范围之内，所以用大括号括起来，可杜绝此类问题：
```
if (cond) {    // Compliant
    y = 2;
} else {       // Compliant
    x = 3;
    y = 1;
}
```
<br/>
<br/>

#### 相关
ID_do_brace  
ID_for_brace  
ID_while_brace  
ID_switch_brace  
<br/>

#### 参考
MISRA C 2004 14.9  
MISRA C 2012 15.6  
MISRA C++ 2008 6-4-1  
<br/>
<br/>

### <span id="ID_if_missingEndingElse">▌R9.1.15 所有 if...else-if 分枝都应以 else 子句结束</span>

ID_if_missingEndingElse&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

所有 if...else\-if 分枝都以 else 子句结束是“[防御性编程](https://en.wikipedia.org/wiki/Defensive_programming)”思想的良好体现。  
  
单独的一个 if 分枝不要求接有 else 子句：
```
if (x > 0) {
    ....
}
```
存在多个 if...else\-if 分枝时，要求接有 else 子句：
```
if (x > 0) {
    ....
}
else if (y < 0){
    ....
}
else {
    // Comment is the minimum requirement,
    // if here is unreachable logically,
    // it's better to log or throw an exception
}
```
<br/>
<br/>

#### 相关
ID_switch_missingDefault  
<br/>

#### 参考
MISRA C 2012 15.7  
MISRA C++ 2008 6-4-2  
<br/>
<br/>

### <span id="control.for">9.2 For</span>

### <span id="ID_for_semicolon">▌R9.2.1 for 语句不应被分号隔断</span>

ID_for_semicolon&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

for 语句不应被分号隔断。  
  
示例：
```
for (....);  // Non-compliant, see the semicolon
{
    ....
}
```
分号使循环失效，这可能是笔误，也可能是有人恶意篡改了代码，应立即修正。
<br/>
<br/>

#### 相关
ID_do_brace  
ID_if_brace  
ID_switch_brace  
ID_while_brace  
<br/>

#### 参考
CWE-670  
<br/>
<br/>

### <span id="ID_for_uncondBroken">▌R9.2.2 for 语句中不应存在无条件的跳转语句</span>

ID_for_uncondBroken&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

无条件的 return、throw 或 break 语句会使循环失效，无条件的 continue 语句会使其后面的代码失效，如果其后没有代码时，该 continue 语句是没有意义的。  
  
示例：
```
for (....) {
    if (cond)
        foo();
        break;  // Non-compliant
}
```
这种问题多数由错误的缩进或混乱的逻辑造成。
<br/>
<br/>

#### 相关
ID_while_uncondBroken  
<br/>

#### 参考
CWE-670  
<br/>
<br/>

### <span id="ID_for_scope">▌R9.2.3 for 语句作用域的范围不应有误</span>

ID_for_scope&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

for 语句作用域的范围不应有误。  
  
示例：
```
for (....)
    statement1; statement2;  // Non-compliant

for (....)
    statement1;
    statement2;  // Non-compliant
```
例中 statement2 不在 for 循环的作用域中，但看起来又和 for 循环相关，这种问题多数是由宏展开或无效的缩进造成的。为了避免这种问题，for 语句应使用大括号括起来。
<br/>
<br/>

#### 相关
ID_for_brace  
<br/>

#### 参考
CWE-483  
<br/>
<br/>

### <span id="ID_for_simplification">▌R9.2.4 for 语句没有明显的循环变量时应改用 while 句语</span>

ID_for_simplification&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

如果 for 语句的第 1 个和第 3 个表达式为空，应改为 while 循环，使代码更简洁。  
  
示例：
```
for (;condition;)   // Non-compliant
```
不妨化简为：
```
while (condition)   // Compliant
```
例外：  
for (;;) 被当作一种无限循环的惯用方法可不受本规则约束。
<br/>
<br/>

#### 参考
C++ Core Guidelines ES.73  
<br/>
<br/>

### <span id="ID_for_emptyBlock">▌R9.2.5 for 循环体不应为空</span>

ID_for_emptyBlock&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

空的 for 循环结构不清晰，可读性较差。  
  
示例：
```
int foo1(int n) {
    int s = 0;
    for (int i = 1; i <= n; s += i++);   // Non-compliant
    return s;
}
```
for 语句小括号内的三个表达式应专注于循环变量的初始化、循环条件的判断以及循环变量的增减，循环体应专注于迭代算法的实现，使程序具有清晰的静态结构，便于阅读，利于维护。  
  
应改为：
```
int foo(int n) {
    int s = 0;
    for (int i = 1; i <= n; i++) {   // Compliant
        s += i;
    }
    return s;
}
```
另外，在 C\+\+ 语言中，如果只通过 for 语句遍历容器，而不对元素进行操作是没有意义的，如：
```
void bar(vector<int>& v) {
    for (auto i: v);         // Non-compliant
}
```
这种代码多为残留代码或功能未实现，应当及时去除或补全功能。
<br/>
<br/>

#### 参考
C++ Core Guidelines ES.85  
<br/>
<br/>

### <span id="ID_for_floatCounter">▌R9.2.6 for 循环变量不应为浮点型</span>

ID_for_floatCounter&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

用于控制循环次数的变量称为“循环变量”，这种变量不应采用浮点类型，否则循环的次数难以控制。  
  
由于浮点型变量的不精确性使浮点型变量不适用于控制循环次数，参见 ID\_illFloatComparison。  
  
示例：
```
size_t n = 0;
for (float f = 0.f; f < 1.f; f += 0.001f) {  // Non-compliant
    ....
    n++;
}
cout << n << '\n';  // Not 1000
```
本例按常识应循环 1000 次，然而由于 f 无法精确表示 0.001，导致实际循环次数与预期产生偏差。  
  
可变通地建立整形循环变量与浮点数的关系：
```
for (size_t n = 0; n < 1000; n++) {  // Compliant
    float f = n * 0.001f;
    ....
}
```
这样循环的次数便与预期相符。
<br/>
<br/>

#### 相关
ID_illFloatComparison  
<br/>

#### 参考
MISRA C 2004 13.4  
MISRA C 2012 14.1  
MISRA C++ 2008 6-5-1  
<br/>
<br/>

### <span id="ID_for_counterChangedInBody">▌R9.2.7 for 循环变量不应在循环体内被改变</span>

ID_for_counterChangedInBody&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

用于控制循环次数的变量称为“循环变量”，这种变量只应在 for 语句的第 3 个表达式中被改变，否则陡增逻辑复杂度，且可读性较差。  
  
示例：
```
for (int i = 0; i < 8; i++) {
    ....
    if (cond) {
        ++i;      // Non-compliant
    }
    ....
}
```
<br/>
<br/>

#### 参考
C++ Core Guidelines ES.86  
MISRA C 2004 13.6  
MISRA C++ 2008 6-5-3  
<br/>
<br/>

### <span id="ID_for_counterNested">▌R9.2.8 嵌套的 for 语句不应使用相同的循环变量</span>

ID_for_counterNested&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

同一个循环变量在内外层 for 循环中均被修改，使循环次数难以控制，是过于复杂的循环逻辑，也可能是某种错误。  
  
示例：
```
for (int i = 0; i < 100; i++) {
    for (; i < 10; i++) {         // Non-compliant
        ....
    }
}
```
<br/>
<br/>

#### 相关
ID_for_counterChangedInBody  
<br/>
<br/>

### <span id="ID_for_brace">▌R9.2.9 for 循环体应该用大括号括起来</span>

ID_for_brace&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

for 循环体应为复合语句，即使只包含一条语句。  
  
示例：
```
int a = 0;
for (int i = 0; i < 10; i++)  // Non-compliant
    a += i;
```
应改为：
```
int a = 0;
for (int i = 0; i < 10; i++) {  // Compliant
    a += i;
}
```
<br/>
<br/>

#### 参考
MISRA C 2004 14.8  
MISRA C 2012 15.6  
MISRA C++ 2008 6-3-1  
<br/>
<br/>

### <span id="control.while">9.3 While</span>

### <span id="ID_while_semicolon">▌R9.3.1 while 语句不应被分号隔断</span>

ID_while_semicolon&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

while 语句不应被分号隔断。  
  
示例：
```
while (condition);  // Non-compliant, see the semicolon
{
    ....
}
```
分号使循环失效，有造成死循环的危险。
<br/>
<br/>

#### 参考
CWE-670  
<br/>
<br/>

### <span id="ID_while_uncondBroken">▌R9.3.2 while 语句中不应存在无条件的跳转语句</span>

ID_while_uncondBroken&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

不受条件限制的 return、throw 或 break 语句会使循环失效，不受条件限制的 continue 语句会使其后面的代码失效，如果其后没有代码，该 continue 语句是没有意义的。  
  
示例：
```
while (condition) {
    ....
    return;     // Non-compliant
}

while (condition) {
    ....
    break;      // Non-compliant, becomes an if-statement
}

while (condition) {
    ....
    continue;   // Non-compliant, meaningless continue
}
```
这种问题多数由错误的缩进或混乱的逻辑造成。
<br/>
<br/>

#### 相关
ID_for_uncondBroken  
<br/>

#### 参考
CWE-670  
<br/>
<br/>

### <span id="ID_while_assignment">▌R9.3.3 while 语句的条件不应为赋值表达式</span>

ID_while_assignment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

虽然语言允许在 while 语句的条件中赋值，但 = 和 == 极易混淆，建议所有产生 bool 型结果的表达式，都不要包含赋值运算符。  
  
示例：
```
while (x = 123)  // Non-compliant
{
    ....
}
```
<br/>
<br/>

#### 相关
ID_if_assignment  
<br/>

#### 参考
CWE-480  
CWE-783  
<br/>
<br/>

### <span id="ID_while_scope">▌R9.3.4 while 语句作用域的范围不应有误</span>

ID_while_scope&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

while 语句作用域的范围不应有误。  
  
示例：
```
while (condition)
    statement1; statement2;  // Non-compliant

while (condition)
    statement1;
    statement2;  // Non-compliant
```
例中 statement2 不在 while 循环的作用域中，但看起来又和 while 循环相关，这种问题多数是由宏展开或无效的缩进造成的。  
为了避免这种问题，while 语句应使用大括号括起来。
<br/>
<br/>

#### 相关
ID_while_brace  
<br/>

#### 参考
CWE-483  
<br/>
<br/>

### <span id="ID_while_emptyBlock">▌R9.3.5 while 循环体不应为空</span>

ID_while_emptyBlock&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

空的 while 循环将功能全部压缩到了条件表达式中，各子句的职责划分不明确，可读性较差。  
  
示例：
```
while (flg.test_and_set());     // Non-compliant
i = (i * j) % n;                // The indent is odd here
flg.clear();
```
示例代码循环检测并设置某标志位，这些功能被压缩到了循环条件中，而且容易使人误以为下一行代码也与循环有关。  
  
应改为：
```
while (true) {                  // Compliant
    if (!flg.test_and_set()) {
        break;
    }
}
i = (i * j) % n;
flg.clear();
```
循环体的内容才是循环主体功能的体现，不应为空。
<br/>
<br/>

#### 参考
CWE-1071  
C++ Core Guidelines ES.85  
<br/>
<br/>

### <span id="ID_while_brace">▌R9.3.6 while 循环体应该用大括号括起来</span>

ID_while_brace&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

while 循环体应为复合语句，即使只包含一条语句。  
  
示例：
```
while (condition)   // Non-compliant
    statement;

while (condition)   // Non-compliant
    statement1;
    statement2;
```
应改为：
```
while (condition) {   // Compliant
    statement;
}

while (condition) {   // Compliant
    statement1;
    statement2;
}
```
<br/>
<br/>

#### 相关
ID_do_brace  
ID_for_brace  
ID_if_brace  
ID_switch_brace  
ID_switch_onlyDefault  
ID_switch_onlyOneCase  
<br/>

#### 参考
MISRA C 2004 14.8  
MISRA C 2012 15.6  
MISRA C++ 2008 6-3-1  
<br/>
<br/>

### <span id="control.do">9.4 Do</span>

### <span id="ID_do_suspiciousContinue">▌R9.4.1 注意 do-while(false) 中可疑的 continue 语句</span>

ID_do_suspiciousContinue&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

continue 语句和 break 语句在语义上是不同的，但在 do\-while(false) 中它们的功效是一样的。  
  
在 do\-while(false) 的循环体中如果既有 break 语句又有 continue 语句，continue 语句被误用的可能性较大。  
  
示例：
```
int foo() {
    do {
        ....
        if (cond1) {
            break;
        }
        ....
        if (cond2) {
            continue;   // Rather suspicious
        }
        ....
    } while (false);
}
```
为了减少误解，建议在 do\-while(false) 中只使用 break 语句，不使用 continue 语句。
<br/>
<br/>

#### 参考
CWE-670  
<br/>
<br/>

### <span id="ID_do_emptyBlock">▌R9.4.2 do-while 循环体不应为空</span>

ID_do_emptyBlock&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

空的 do\-while 循环将功能全部压缩到了条件表达式中，各子句的职责划分不明确，可读性较差。  
  
示例：
```
void foo(char* d, const char* s) {
    do {} while (*d++ = *s++);       // Non-compliant
}
```
示例代码将循环的条件和循环产生的副作用叠加在了一起，易使人误解。
<br/>
<br/>

#### 相关
ID_while_emptyBlock  
<br/>

#### 参考
CWE-1071  
C++ Core Guidelines ES.85  
<br/>
<br/>

### <span id="ID_do_brace">▌R9.4.3 do-while 循环体应该用大括号括起来</span>

ID_do_brace&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

do\-while 循环体应为复合语句，即使只包含一条语句。如果没有合理的大括号，可能会与内嵌的 while 语句形成难以发觉的错误。  
  
示例：
```
do           // Non-compliant
    foo();
    while (cond1);
while (cond2);
```
例中 while 关键字与 do 关键字产生了错误的对应关系，导致最后一个 while 形成了死循环，应改为：  

```
do {         // Compliant
    foo();
    while (cond1) {
        ....
    }
} while (cond2);
```
<br/>
<br/>

#### 相关
ID_for_brace  
ID_if_brace  
ID_switch_brace  
ID_while_brace  
<br/>

#### 参考
MISRA C 2004 14.9  
MISRA C 2012 15.6  
MISRA C++ 2008 6-4-1  
<br/>
<br/>

### <span id="ID_do_deprecated">▌R9.4.4 不建议使用 do 语句</span>

ID_do_deprecated&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

do 语句的终止条件在末尾，且第一次执行时不检查终止条件，可读性较低，不利于维护。  
  
示例：
```
int foo(int n) {
    do {
        if (n < 0) {
            break;
        }
        ....
        if (n > 0) {
            continue;
        }
        ....
    } while (cond);  // Too complex
    ....
    return n;
}
```
do 语句糅合循环和流程跳转，使代码过于复杂，建议将复杂的 do 语句抽取成函数，使代码的结构更明确。
<br/>
<br/>

#### 参考
C++ Core Guidelines ES.75  
<br/>
<br/>

### <span id="control.switch">9.5 Switch</span>

### <span id="ID_switch_semicolon">▌R9.5.1 switch 语句不应被分号隔断</span>

ID_switch_semicolon&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

switch 语句不应被分号隔断。  
  
示例：
```
switch (v);  // Non-compliant
```
这是毫无意义的 switch 语句，可能是残留代码，应及时去除。
<br/>
<br/>

#### 参考
CWE-670  
<br/>
<br/>

### <span id="ID_switch_emptyBlock">▌R9.5.2 switch 语句不应为空</span>

ID_switch_emptyBlock&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

空的 switch 语句没有意义。  
  
示例：
```
switch (v) {}  // Non-compliant
```
这是毫无意义的 switch 语句，可能是残留代码，也可能是功能未实现。
<br/>
<br/>

#### 参考
CWE-1071  
<br/>
<br/>

### <span id="ID_switch_caseOutOfRange">▌R9.5.3 case 常量的范围不可超出 switch 变量的范围</span>

ID_switch_caseOutOfRange&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

如果 case 常量的范围超出了 switch 变量的范围，会导致相应分枝永远不会被执行。  
  
示例：
```
void foo(char c)
{
    switch (c)
    {
    case 100:  // Compliant
        ....
        break;
    case 256:  // Non-compliant, unreachable branch
        ....
        break;
    }
}
```
例中变量 c 的值不可能为 256，所以 case 256 对应的分枝永远不会被执行。
<br/>
<br/>

#### 相关
ID_illComparison  
<br/>

#### 参考
CWE-561  
<br/>
<br/>

### <span id="ID_switch_invalidStatement">▌R9.5.4 switch 语句中任何子句都应从属于某个 case 或 default 分枝</span>

ID_switch_invalidStatement&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

switch 语句中任何子句都应从属于某个 case 或 default 分枝，否则不会被执行。  
  
示例：
```
switch (v)
{
    int i;    // Non-compliant
    i = 0;    // Non-compliant
case 1:
    ....
    break;
default:
    bar(i);   // Logic error, ‘i’ is not initialized
    break;
}
```
例中对变量 i 的声明和赋值不从属于任何 case 或 default 分枝，是无效语句。
<br/>
<br/>

#### 参考
CWE-561  
<br/>
<br/>

### <span id="ID_switch_badFormedCase">▌R9.5.5 case 和 default 标签应直接从属于 switch 语句</span>

ID_switch_badFormedCase&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

不直接从属于 switch 语句的 case 或 default 标签用于非结构性跳转，是公认的不良实现。  
  
关于非结构性跳转的进一步讨论可参见 ID\_forbidGoto。  
  
示例：
```
switch (v)
{
case 1:         // Compliant 
    if (cond)
    {
case 2:         // Non-compliant 
        ....
    }
    else
    {
default:        // Non-compliant 
        ....
    }
}
```
例中 case 1 直接从属于 switch 语句，而 case 2 和 default 直接从属于 if 语句，当 v 的值不是 1 时，会绕过 if 语句的条件判断，产生非结构性跳转，与 goto 语句的问题一样，很容易导致逻辑混乱且不利于维护。  
  
虽然有些编程技巧会将 case 置于循环中，如“[Duff’s device](https://en.wikipedia.org/wiki/Duff's_device)”等，但当今主流的编程语言均已不再提倡非结构性跳转。
<br/>
<br/>

#### 相关
ID_forbidGotoBlocks  
<br/>

#### 参考
MISRA C 2004 15.1  
MISRA C++ 2008 6-4-4  
<br/>
<br/>

### <span id="ID_switch_uselessFallThrough">▌R9.5.6 不应存在紧邻 default 标签的空 case 标签</span>

ID_switch_uselessFallThrough&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

紧邻 default 标签的空 case 标签是没有意义的，应当去除。  
  
示例：
```
switch (v)
{
case 0:     // Compliant
    ....
    break;

case 1:     // Non-compliant
default:
case 2:     // Non-compliant
    ....
    break;
}
```
应改为：
```
switch (v)
{
case 0:     // Compliant
    ....
    break;

default:    // Compliant
    ....
    break;
}
```
<br/>
<br/>
<br/>

### <span id="ID_switch_identicalBranch">▌R9.5.7 不应存在内容完全相同的 case 分枝</span>

ID_switch_identicalBranch&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

内容完全相同的分枝应合并为一个分枝，也可能是由复制粘贴造成的错误。  
  
示例：
```
switch (v)
{
case 1:
    branch1
    break;
case 2:
    branch2
    break;
case 3:
    branch1   // Non-compliant
    break;
}
```
例中 case 3 对应的分枝和 case 1 对应的分枝内容完全相同，应将其合并为一个分枝，或修正本应存在的差异。  
  
例外：  
如果分枝内容较少，为了使代码更清晰可以接受适当的重复，但如果分枝内容很多就不应重复了，审计工具不妨指定一个数量限制，当重复分枝的符号数量超过这个限制时算作违规，否则放过。
<br/>
<br/>

#### 配置
branchTokenCountThreshold：重复分枝符号数量限制，不检查符号数量小于该值的分枝  
<br/>

#### 相关
ID_if_identicalBlock  
ID_if_identicalElseIfBlock  
<br/>

#### 参考
C++ Core Guidelines ES.3  
<br/>
<br/>

### <span id="ID_switch_bool">▌R9.5.8 switch 语句的条件不应为 bool 型</span>

ID_switch_bool&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

如果条件为 bool 型，应改用 if\-else 语句使代码的结构更清晰。  
  
示例：
```
void foo(bool b)
{
    switch (b)   // Non-compliant
    {
    case true:
        ....
        break;
    case false:
        ....
        break;
    }
}
```
应改为：
```
void foo(bool b)
{
    if (b)  // Compliant
    {
        ....
    }
    else
    {
        ....
    }
}
```
<br/>
<br/>

#### 参考
MISRA C 2004 15.4  
MISRA C 2012 16.7  
MISRA C++ 2008 6-4-7  
<br/>
<br/>

### <span id="ID_switch_onlyDefault">▌R9.5.9 switch 语句不应只包含 default 标签</span>

ID_switch_onlyDefault&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

只有 default 标签的 switch 语句是没有意义的，起不到分枝选择的作用，往往是残留代码或功能未实现。  
  
示例： 
```
switch (v)
{
default:    // Non-compliant
    ....
}
```
这种空的可以 fallthrough 到 default 标签的空 case 标签也是没有意义的：
```
switch (v)
{
case 1:     // Non-compliant
case 2:
default:
    ....
}
```
<br/>
<br/>

#### 参考
MISRA C 2012 16.6  
<br/>
<br/>

### <span id="ID_switch_onlyOneCase">▌R9.5.10 switch 语句不应只包含一个 case 标签</span>

ID_switch_onlyOneCase&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

只有一个 case 标签的 switch 语句与 if 语句语义相同，但形式上更为复杂，应改为 if 语句。  
  
示例：
```
switch (v)
{
case 123:    // Non-compliant
    ....
    break;
}
```
应改为：
```
if (v == 123)
{
    ....
}
```
<br/>
<br/>

#### 参考
MISRA C 2012 16.6  
<br/>
<br/>

### <span id="ID_switch_tooManyCases">▌R9.5.11 switch 语句分枝数量应在规定范围之内</span>

ID_switch_tooManyCases&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

switch 语句分枝过多会使代码过于庞大不利于维护，分枝很多时建议将每个 case 的执行逻辑抽取成函数，再按遵循某种算法的索引结构组织在一起。  
  
示例：
```
switch (v)
{
case 1: .... break;
case 2: .... break;
        ....             // Lots of cases
case 1000: .... break;   // Non-compliant
}
```
建议 case 数量不超过 10 个。
<br/>
<br/>

#### 配置
maxCasesCount：分枝数量上限，超过则报出  
<br/>
<br/>

### <span id="ID_switch_missingDefault">▌R9.5.12 switch 语句应配有 default 分枝</span>

ID_switch_missingDefault&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

所有 switch 语句都配有 default 分枝是“[防御性编程](https://en.wikipedia.org/wiki/Defensive_programming)”思想的良好体现。  
  
示例：
```
switch (v)
{
case 0:
    ....
    break;
case 1:
    ....
    break;
default:
    // Comment is the minimum requirement,
    // if here is unreachable logically,
    // it's better to log or throw an exception
    break;
}
```
例外：  
当 switch 变量为枚举类型，且 case 标签已对应所有枚举项时，不再要求有 default 分枝。
<br/>
<br/>

#### 相关
ID_if_missingEndingElse  
<br/>

#### 参考
CWE-478  
MISRA C++ 2008 6-4-6  
<br/>
<br/>

### <span id="ID_switch_breakOmitted">▌R9.5.13 switch 语句的每个非空分枝都应该用无条件的 break 语句终止</span>

ID_switch_breakOmitted&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

每个非空分枝都应该用无条件的 break 语句终止，break 语句的缺失或误用是导致错误的常见原因。  
  
示例：
```
switch (a)
{
case 0:
    b = 1;
    break;  // Compliant
case 1:
    b = 2;  // Non-compliant, missing ‘break’
default:
    b = 3;
    break;  // Compliant
}
```
相连的 case 标签不受本规则约束：
```
switch (c)
{
case 0:     // Compliant
case 1:
    ....
    break;
}
```
少数情况下，如果确实不能有 break 语句，应添加注释说明情况，或在 C\+\+ 语言中用 \[\[fallthrough\]\] 注明：
```
switch (v)
{
case 1:
    do_something();
    [[fallthrough]];   // Compliant, since C++17
default:
    do_something_default();
    break;
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2017 10.6.5  
<br/>

#### 参考
CWE-484  
C++ Core Guidelines ES.78  
MISRA C 2004 15.2  
MISRA C 2012 16.3  
MISRA C++ 2008 6-4-5  
<br/>
<br/>

### <span id="ID_switch_brace">▌R9.5.14 switch 语句应该用大括号括起来</span>

ID_switch_brace&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

switch 语句应为包含多条语句的复合语句，且用大括号括起来，否则不应选用 switch 语句。  
  
示例：
```
switch (v)   // Non-compliant
    case 0:
        foo(v);
```
应改为 if 语句：
```
if (v == 0) {   // Compliant
    foo(v);
}
```
<br/>
<br/>

#### 相关
ID_if_brace  
ID_switch_onlyDefault  
ID_switch_onlyOneCase  
<br/>

#### 参考
MISRA C 2004 14.8  
MISRA C 2012 15.6  
MISRA C++ 2008 6-3-1  
<br/>
<br/>

### <span id="ID_switch_forbidNest">▌R9.5.15 switch 语句不应嵌套</span>

ID_switch_forbidNest&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: control suggestion

<hr/>

嵌套的 switch 语句使代码显得复杂，不利于维护。  
  
示例：
```
switch (u)
{
case 1:
    ....
case 2:
    switch (v)  // Non-compliant
    {
    case 3:     // Confusing
        ....
    case 4:
        ....
    }
default:
    ....
}
```
不同 switch 的 case 交织在一起，较难看出哪个 case 对应哪个变量，尤其是代码行数较多时这种问题会更为明显，应尽量不使用嵌套，或将内嵌的 switch 语句抽取成一个函数，使代码更清晰。
<br/>
<br/>
<br/>

### <span id="control.try-catch">9.6 Try-catch</span>

### <span id="ID_try_emptyBlock">▌R9.6.1 不应存在空的 try 块</span>

ID_try_emptyBlock&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

空的 try 块是毫无意义的，有可能是残留代码或功能未实现。  
  
示例：
```
try {
    // Empty block or some code commented out
}
catch (Exception& e) {
    // The whole statement is meaningless
}
```
如果是残留代码应及时删去，否则引入无意义的异常处理会影响代码优化。
<br/>
<br/>

#### 参考
CWE-1071  
<br/>
<br/>

### <span id="ID_catch_emptyBlock">▌R9.6.2 不应存在空的 catch handler</span>

ID_catch_emptyBlock&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

空的 catch handler 掩盖了异常，不利于问题的排查与纠正，应至少添加日志记录等操作。  
  
示例：
```
void foo() {
    try {
        ....
    }
    catch (...)   // Non-compliant, very bad
    {}
}
```
这样做并不能真正提高程序的稳定性，相当于逃避了问题，而且掩盖没有被处理的异常也可能会影响到其他方面的正常运行。  
  
对于要求不能抛出异常的接口，不妨按下例处理，记录意料之外的异常情况，以便问题的排查：
```
void foo() noexcept {
    try {
        ....
    }
    catch (...) {
        log_unexpected_and_exit(__FILE__, __LINE__, "messages");   // Compliant
    }
}
```
<br/>
<br/>

#### 参考
CWE-1069  
CWE-1071  
CWE-391  
<br/>
<br/>

### <span id="ID_try_forbidNest">▌R9.6.3 不应嵌套 try-catch 语句</span>

ID_try_forbidNest&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: control suggestion

<hr/>

嵌套的 try\-catch 使代码显得复杂，不利于维护。  
  
示例：
```
try {
    ....
    try {       // Non-compliant
        ....
    } catch (A&) {
        ....
    }
} catch (B&) {
    ....
}
```
嵌套的 try\-catch 较难看出哪个 try 对应哪个 catch，当代码行数较多时这种问题会更为明显。
<br/>
<br/>

#### 参考
C++ Core Guidelines E.17  
<br/>
<br/>

### <span id="ID_try_disorderedEllipsis">▌R9.6.4 捕获所有异常的 catch-all handler 应位于最后</span>

ID_try_disorderedEllipsis&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

如果 catch\-all handler 排在前面，其后面的 handler 将失去作用。  
  
示例：
```
try {
    ....
} catch (...) {  // Catch-all handler, non-compliant
    ....
} catch (const E&) {
    ....
}
```
应改为：
```
try {
    ....
} catch (const E&) {
    ....
} catch (...) {  // Compliant
    ....
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 15.3(6)  
ISO/IEC 14882:2011 15.3(5)  
ISO/IEC 14882:2011 18.3(5)  
<br/>

#### 参考
CWE-561  
C++ Core Guidelines E.31  
MISRA C++ 2008 15-3-7  
<br/>
<br/>

### <span id="ID_try_disorderedHandlers">▌R9.6.5 派生类的 catch handler 应排在基类 catch handler 之前</span>

ID_try_disorderedHandlers&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

如果违反这个顺序，派生类的 catch handler 将失去作用。  
  
示例：
```
class B { .... };
class D: public B { .... };

try {
    ....
} catch (const B&) {
    ....
} catch (const D&) {  // Non-compliant, unreachable
    ....
}
```
例中 B 为基类，D 为派生类，D 类异常会被 B 的 handler 捕获，D 的 handler 失去了作用。  
  
应改为：
```
try {
    ....
} catch (const D&) {
    ....
} catch (const B&) {  // Compliant
    ....
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 15.3  
ISO/IEC 14882:2011 15.3  
ISO/IEC 14882:2011 18.3  
<br/>

#### 参考
CWE-561  
C++ Core Guidelines E.31  
<br/>
<br/>

### <span id="control.jump">9.7 Jump</span>

### <span id="ID_forbidGotoBlocks">▌R9.7.1 禁止 goto 语句向嵌套的或无包含关系的作用域跳转</span>

ID_forbidGotoBlocks&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: control warning

<hr/>

不同的作用域对应不同的条件约束，在不同的作用域间跳转是对约束的破坏，很容易导致逻辑混乱。  
  
向嵌套的或无包含关系的作用域跳转是不应被允许的，如果是为了结束当前流程而在同层或向外层作用域跳转，则可被本规则允许。  
  
示例：
```
int foo(int a) {
    if (a == 0) {
        goto L;    // Non-compliant, jumps into a block
    }
    for (int i = 0; i < a; i++) {
        for (int j = 0; j < a; j++) {
L:
            if (cond) {
                goto M;   // Compliant, jumps out of loops
            }
        }
        ....
    }
M:
    return a;
}
```
例中 goto L 从 if 语句跳入循环语句是应当被禁止的，而 goto M 用于结束循环流程，可以保留。
<br/>
<br/>

#### 相关
ID_forbidGotoBack  
ID_forbidGoto  
<br/>

#### 参考
MISRA C 2012 15.3  
MISRA C++ 2008 6-6-1  
<br/>
<br/>

### <span id="ID_forbidGotoBack">▌R9.7.2 禁止 goto 语句向前跳转</span>

ID_forbidGotoBack&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: control suggestion

<hr/>

向先于当前 goto 语句定义的标签跳转，可读性较差，是公认的不良实现。  
  
示例：
```
int foo() {
    int i = 0, j = 0;
L:
    j += 1;
    i += j;
    if (j > 100) {
        goto M;      // Compliant
    }
    goto L;          // Non-compliant
M:
    return i;
}
```
例中 goto M 向后跳转符合本规则要求，而 goto L 向前跳转不符合要求，应改用循环等结构性语句。
<br/>
<br/>

#### 相关
ID_forbidGotoBlocks  
ID_forbidGoto  
<br/>

#### 参考
MISRA C 2012 15.2  
MISRA C++ 2008 6-6-2  
<br/>
<br/>

### <span id="ID_forbidGoto">▌R9.7.3 禁用 goto 语句</span>

ID_forbidGoto&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: control suggestion

<hr/>

历史表明，goto 语句会破坏程序的结构性规划，很容易导致逻辑混乱且不利于维护，在非自动生成的、对可读性有要求的代码中，建议禁用 goto 语句。  
  
示例：
```
if (cond0) {
    goto L;    // Non-compliant
}
....
if (cond1) {
L:
    ....
}
```
语句的排列和作用域的嵌套描述了程序的静态结构，清晰的静态结构使人易于理解程序的行为，而 goto 语句会打破这种结构，无规律的跳转会显著降低代码的可读性，例中 goto L 会绕过第二个 if 语句的条件约束，可读性很差，应被禁止。  
  
C 语言的流程管理较为简单，goto 语句可提供一定的灵活性，但不应作为常规实现手段，也应受一定的限制，在 C 代码中使用 goto 语句应遵循 ID\_forbidGotoBlocks 和 ID\_forbidGotoBack 等规则。  
  
C\+\+ 语言提供了更丰富的流程管理功能，在 C\+\+ 代码中不应再使用 goto 语句。  
  
下面给出 goto 语句的一种常用模式：
```
void foo(size_t n)
{
    int *a = NULL, *b = NULL, *c = NULL;
    a = (int*)malloc(n);
    if (!a) {
        goto E;
    }
    b = (int*)malloc(n);
    if (!b) {
        goto E;
    }
    c = (int*)malloc(n);    // Multiple resource allocation
    if (!c) {
        goto E;
    }
    ....
E:                          // Single exit point
    free(a);
    free(b);
    free(c);
}
```
在多次资源分配过程中，如果某次分配失败则需要释放已分配的资源，利用 goto 语句可实现资源的统一释放，在 C 代码中如果不用 goto 语句反而会很繁琐，所以这种模式在 C 代码中可以复用。  
  
由于 C\+\+ 提供容器、智能指针等更丰富的资源管理手段，所以不建议在 C\+\+ 代码中再使用这种模式，即使标准库没有和相关资源对应的功能，也应该利用“[RAII](https://en.wikipedia.org/wiki/Resource_acquisition_is_initialization)”等机制对其先封装再使用。
```
void foo(size_t n) {
    std::vector<int> a, b, c;   // Safe and brief
    ....
}
```
<br/>
<br/>

#### 相关
ID_forbidGotoBlocks  
ID_forbidGotoBack  
<br/>

#### 参考
C++ Core Guidelines ES.76  
MISRA C 2012 15.1  
<br/>
<br/>

### <span id="ID_forbidLongjmp">▌R9.7.4 禁用 setjmp、longjmp</span>

ID_forbidLongjmp&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: control warning

<hr/>

setjmp、longjmp 可以在函数间跳转，进一步破坏了结构化编程理念，非框架代码不应使用。  
  
示例：
```
jmp_buf buf;

float div(int a, int b) {
    if (b == 0) {
        longjmp(buf, 1);            // Non-compliant
    }
    return (float)a / b;
}

int main() {
    if (setjmp(buf) == 0) {          // Non-compliant
        printf("%f\n", div(3, 0));
    } else {
        return 1;
    }
}
```
setjmp 返回 0 表示设置跳转位置成功，之后如果调用 longjmp 会跳回 setjmp 的位置，这时 setjmp 返回非 0 值，这种机制在 C 语言中可以用作异常处理，也可以实现“协程”等概念，但会使代码的可维护性显著降低，在普通的业务逻辑或算法实现中不应使用。  
  
setjmp 与 longjmp 由 jmp\_buf 参数关联，应在同一线程中使用，如果调用 longjmp 时没有对应的 setjmp，或 setjmp 所在函数已经结束执行，会导致标准未定义的行为，而且要注意 setjmp、longjmp 无法与 C\+\+ 对象自动析构等机制兼容，极易造成意料之外的错误。
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 7.13.2.1(2)-undefined  
ISO/IEC 14882:2011 18.10(4)-undefined  
<br/>

#### 参考
MISRA C 2004 20.7  
MISRA C 2012 21.4  
MISRA C++ 2008 17-0-5  
C++ Core Guidelines SL.C.1  
<br/>
<br/>

### <span id="ID_redundantJump">▌R9.7.5 不应出现多余的跳转语句</span>

ID_redundantJump&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

无返回值函数的最后一条 return 语句、循环体的最后一条 continue 语句、goto 到下一条语句等是多余的，应当去除。  
  
示例：
```
void foo() {
    ....
    return;   // Redundant
}

void bar() {
    while (condition) {
        ....
        continue;    // Redundant
    }
}

void baz() {
    goto L;   // Redundant
L:
    ....
}
```
<br/>
<br/>
<br/>

## <span id="expression">10. Expression</span>

### <span id="expression.logic">10.1 Logic</span>

### <span id="ID_illIdentical">▌R10.1.1 不应出现不合逻辑的重复子表达式</span>

ID_illIdentical&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

逻辑与、逻辑或、按位与、按位或的子表达式以及三元表达式的两个分枝不应重复，否则失去逻辑意义。  
  
示例：
```
bool foo(int* p, char* s) {
    return p != NULL && p != NULL;  // Non-compliant
}

long bar() {
    return FLAG0 | FLAG1 | FLAG0;   // Non-compliant
}

char baz(bool cond) {
    return cond? 'a': 'a';          // Non-compliant
}
```
例中重复的子表达式都是有问题的，这是一种很常见的错误。  
  
这种错误往往由复制粘贴引起，所以修正时不要只是删去重复的子表达式，要考虑是否有某些项被漏掉。  
  
例外：
```
if (fin.get() == 'a' && fin.get() == 'a') {   // Let it go
    ....
}
```
重复的子表达式有一定副作用时可不受本规则限制，但不值得提倡。例中逻辑表达式从文件流中读取相邻的字符，第二个子表达式可能不会被执行，这种代码即使没有逻辑错误也是不利于维护的，参见 ID\_shortCircuitSideEffect。
<br/>
<br/>

#### 参考
CWE-682  
<br/>
<br/>

### <span id="ID_conflictCondition">▌R10.1.2 各逻辑子表达式不应自相矛盾</span>

ID_conflictCondition&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

在逻辑表达式中，相互矛盾的子表达式会使整个表达式的结果恒为真或恒为假，造成条件失效。  
  
1. 判断同一变量同时等于不同的值是无效的：
```
a == 1 && a == 2  // always false
a != 1 || a != 2  // always true
```
2. 判断同一变量的上限小于下限是无效的：
```
a < -128 && a > 127    // always false
a >= -128 || a <= 127  // always true
```
3. 如下逻辑判断也是无效的：
```
a > b && a < b    // always false
a > b && a <= b   // always false
a > b || a <= b   // always true
a == b && a != b  // always false
a == b || a != b  // always true
```
这类问题均为常见笔误，须认真对待。
<br/>
<br/>

#### 参考
CWE-570  
CWE-571  
<br/>
<br/>

### <span id="ID_invalidCondition">▌R10.1.3 条件表达式不应恒为真或恒为假</span>

ID_invalidCondition&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

变量初始化后不经修改即作为条件往往意味着某种逻辑错误。  
  
示例：
```
void foo() {
    int i = 0;
    if (i > 0) {  // Non-compliant
        ....
    }
}
```
例中变量 i 初始化为 0 后在没有被修改过的情况下，仍对其进行判断是没有意义的，这显然是逻辑错误。
<br/>
<br/>

#### 相关
ID_constLogicExpression  
<br/>

#### 参考
CWE-570  
CWE-571  
MISRA C 2004 13.7  
MISRA C 2012 14.3  
<br/>
<br/>

### <span id="ID_redundantCondition">▌R10.1.4 不应使用多余的逻辑子表达式</span>

ID_redundantCondition&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

逻辑或、逻辑与的子表达式有包含关系时，其中的一个表达式是多余的。  
  
示例：
```
a > b && a != b     // None-compliant, a != b is redundant
a < b || a != b     // None-compliant, a < b is redundant
a >= b && a == b    // None-compliant, a >= b is redundant
a == b || a <= b    // None-compliant, a == b is redundant
```
这种多余的子表达式很可能包含某种错误，需认真对待。
<br/>
<br/>
<br/>

### <span id="ID_constLogicExpression">▌R10.1.5 逻辑表达式及其子表达式的结果不应为常量</span>

ID_constLogicExpression&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

逻辑表达式及其子表达式的结果不应为常量，否则使逻辑判断失去意义。  
  
示例：
```
if (false) {  // Non-compliant
    ....
}

while (false) {  // Non-compliant
    ....
}

const bool False = false;
if (False && other_condition) {  // Non-compliant
    ....
}
```
这种代码往往是调试或维护后的残留代码，没有意义的控制语句应及时去除。  
  
例外：  
true 或 1 等常量可用在 while 或 do\-while 循环的条件中，false 或 0 等常量可用在 do\-while 循环的条件中。
```
while (true) {  // Compliant
    ....
}

do {
    ....
} while (0);  // Compliant
```
但这种特例不包括逻辑表达式的子表达式，如：
```
while (other_condition || 1) {  // Non-compliant
    ....
}
```
另外，预编译阶段定义的常量也不应该作为逻辑条件或逻辑子表达式，如：
```
#define M 123

if (M) {  // Non-compliant
    ....
}
```
能在预编译阶段确定的代码应统一用预编译方式处理，不应占用运行时资源：
```
#if M  // Compliant
    ....
#endif
```
或：
```
if constexpr (M) {  // Compliant, since C++17
    ....
}
```
<br/>
<br/>

#### 参考
CWE-570  
CWE-571  
MISRA C 2004 13.7  
MISRA C 2012 14.3  
<br/>
<br/>

### <span id="ID_shortCircuitSideEffect">▌R10.1.6 逻辑表达式的右子表达式不应有副作用</span>

ID_shortCircuitSideEffect&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: expression suggestion

<hr/>

逻辑表达式的右子表达式有副作用会使代码复杂度显著增加，易产生错误，且不利于维护。  
  
对于逻辑表达式的求值，标准规定从左至右计算各子表达式的值，当可以确定整个表达式的值时，即使还有未计算的子表达式，也会立即结束求值，这种方法可提高效率，称为“短路规则（short\-circuit evaluation）”。  
  
逻辑表达式的右子表达式受左子表达式影响，可能不会被执行，如果有副作用也可能不会生效。  
  
示例：
```
if (a == foo || b == bar++) {  // Non-compliant
    do_something(bar);         // Consider that ‘bar++’ may not be evaluated
}
```
如果 a == foo 为真，不论 b 是否等于 bar\+\+，整个条件表达式的值一定为真，所以 b == bar\+\+ 不一定会被执行，if 语句要同时考虑 bar\+\+ 执行与未执行的两种状态，复杂度较高。
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 6.5.13(4) 6.5.14(4)  
ISO/IEC 9899:2011 6.5.13(4) 6.5.14(4)  
<br/>

#### 参考
MISRA C 2004 12.4  
MISRA C 2012 13.5  
MISRA C++ 2008 5-14-1  
<br/>
<br/>

### <span id="ID_simplifiableCondition">▌R10.1.7 逻辑表达式应保持简洁明了</span>

ID_simplifiableCondition&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

逻辑或、逻辑与的子表达式可以合并成一个表达式时应尽量合并。  
  
示例：
```
a < b || a > b      // None-compliant, use a != b instead
a > b || a == b     // None-compliant, use a >= b instead
a < b || a == b     // None-compliant, use a <= b instead
a <= b && a >= b    // None-compliant, use a == b instead
a > b && a >= b     // None-compliant, use a >  b instead
a < b || a <= b     // None-compliant, use a <= b instead
```
这种不合常理的繁琐写法也可能包含某种错误，需认真对待。
<br/>
<br/>
<br/>

### <span id="ID_simplifiableTernary">▌R10.1.8 可化简为逻辑表达式的三元表达式应尽量化简</span>

ID_simplifiableTernary&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: expression suggestion

<hr/>

当三元表达式的分枝是 true 或 false 时可化简为逻辑表达式，应化简代码。  
  
示例：
```
void foo(int a) {
    if (a > 123? true: false) {   // Non-compliant, too verbose
        ....
    }
}

bool bar(int a) {
    return a > 123? fun(): false;   // Non-compliant, verbose
}
```
应改为：
```
void foo(int a) {
    if (a > 123) {   // Compliant
        ....
    }
}

bool bar(int a) {
    return a > 123 && fun();   // Compliant
}
```
<br/>
<br/>
<br/>

### <span id="expression.evaluation">10.2 Evaluation</span>

### <span id="ID_unevaluatedSideEffect">▌R10.2.1 不可依赖不会生效的副作用</span>

ID_unevaluatedSideEffect&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

有些运算符只关注类型，其子表达式不会被求值，子表达式的副作用也不会有实际效果。  
  
这类运算在 C 语言中有：
```
sizeof、_Alignof、_Generic
```
在 C\+\+ 语言中有：
```
sizeof、typeid、noexcept、decltype、declval
```
其中 typeid 较为特殊，当其子表达式是函数调用，且返回多态类型的引用时，也会执行具体函数。  
  
示例：
```
int a = 0;
cout << sizeof(a++) << ' ';   // Non-compliant
cout << a << '\n';            // What is output?
```
输出 4 0，a\+\+ 不会生效。
<br/>
<br/>

#### 相关
ID_sideEffectAssertion  
<br/>

#### 依据
ISO/IEC 14882:2003 5.3.3(1)  
ISO/IEC 14882:2011 5.3.3(1)  
ISO/IEC 14882:2017 8.3.3(1)  
<br/>

#### 参考
MISRA C 2004 12.3  
MISRA C 2012 13.6  
MISRA C++ 2008 5-3-4  
SEI CERT EXP44-C  
SEI CERT EXP52-CPP  
<br/>
<br/>

### <span id="ID_evaluationOrderReliance">▌R10.2.2 避免依赖特定的子表达式求值顺序</span>

ID_evaluationOrderReliance&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

不同的求值顺序不应产生不同的结果，否则极易导致意料之外的错误，也会降低代码的可移植性。  
  
C 标准用“[序列点（sequence point）](https://en.wikipedia.org/wiki/Sequence_point)”定义求值顺序，序列点前面的表达式先于后面的表达式求值并落实相关副作用，逻辑与、逻辑或、三元、逗号等运算符以及函数调用的左括号与序列点相关，其左子表达式先于右子表达式求值并落实副作用，赋值、算术、位运算等运算符与序列点无关，其左右子表达式的求值顺序是未声明的，函数各参数的求值顺序也是未声明的，C\+\+ 标准与 C 标准大致相同，C\+\+17 明确了赋值、移位等运算符的求值顺序。  
  
要注意子表达式的副作用在不同求值顺序下的正确性，可参见 ID\_confusingAssignment 的进一步说明。  
  
示例：
```
Stack s {1, 2, 3};      // A stack, the top is 1
int pop();              // Pop and return the top element from the stack
int x = pop() - pop();  // Non-compliant
```
设 pop 函数弹出并返回栈顶元素，减号左右的两个 pop 函数哪个先执行呢？这是标准未声明的，x 的值可以是 1 \- 2，也可以是 2 \- 1，由编译器决定。  
  
应改为：
```
int a = pop();
int b = pop();
x = a - b;      // Compliant, or ‘b – a’, depends on your needs
```
这样便确定是栈项的第一个元素减第二个元素。  
  
又如：
```
fun(pop(), pop());  // Non-compliant
```
设 fun 是函数名称或获取函数指针的表达式，标准规定 fun 会先于参数求值，但参数之间的求值顺序是未声明的。  
  
逻辑与、逻辑或、三元、逗号等表达式可不受本规则限制，但其子表达式需受本规则限制。
<br/>
<br/>

#### 相关
ID_confusingAssignment  
<br/>

#### 依据
ISO/IEC 9899:2011 5.1.2.3(3)  
ISO/IEC 9899:2011 Annex C  
<br/>

#### 参考
CWE-758  
C++ Core Guidelines ES.43  
C++ Core Guidelines ES.44  
<br/>
<br/>

### <span id="ID_confusingAssignment">▌R10.2.3 在表达式中不应多次读写同一对象</span>

ID_confusingAssignment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

在表达式中多次引用并修改同一对象，很可能会因为非预期的求值顺序而产生错误的结果。  
  
关于对象的副作用在求值过程中何时生效这一问题，相关标准既复杂又有大量未声明和未定义的情况，故需注意：  
1. 写入对象的次数不应超过 1 次  
2. 对象不应既被读取又被写入，除非是为了计算对象的新状态并写入对象  
  
注意，对 volatile 对象的读取相当于更新对象的值，也是一种副作用，故：  
1. volatile 对象在表达式中只应出现 1 次  
2. volatile 对象不应既被读取又被写入  
  
本规则是 ID\_evaluationOrderReliance 的特化。  
  
示例：
```
a = a++;        // Non-compliant
a = ++a;        // Non-compliant

++b = b;        // Non-compliant
b = a++ + a;    // Non-compliant

arr[i] = ++i;   // Non-compliant
p->fun(p++);    // Non-compliant

fun(a, a++);    // Non-compliant
fun(++a, a++);  // Non-compliant
```
例中 \+\+ 泛指写入操作。  
  
设 a 是值为 0 的整型变量，如下表达式：
```
a = a++;        // Non-compliant
```
对变量 a 有两次写入，分别是增 1 和赋值为 0（子表达式 a\+\+ 的值为 0），这两次写入的次序在 C 和 C\+\+17 之前的标准中是未声明的，如果先增 1 再赋 0，a 的值最终为 0，如果先赋 0 再增 1，a 的值最终为 1，这种不确定的结果应当避免，C\+\+17 规定了右子表达式的副作用先于赋值生效，所以在 C\+\+17 之后例中表达式是无效的。  
  
虽然新的标准强化了求值顺序，但这种代码使人费解，很容易造成理解上的偏差，故不应使用。  
  
如果 a 不是 volatile 变量，应改为：
```
a++;            // Compliant, or
a += 1;         // Compliant, or
a = a + 1;      // Compliant
```
如果 a 是 volatile 变量，应改为：
```
int tmp = a;
a = tmp + 1;    // Compliant
```
对于逻辑与、逻辑或、三元以及逗号表达式，标准明确规定了子表达式从左至右求值，左子表达式的副作用也会在右子表达式求值前生效，故可不受本规则限制，但其子表达式仍需受本规则限制，进一步可参见 “[序列点（sequence point）](https://en.wikipedia.org/wiki/Sequence_point)”以及“[求值顺序](https://en.cppreference.com/w/cpp/language/eval_order)”等概念。
<br/>
<br/>

#### 相关
ID_evaluationOrderReliance  
<br/>

#### 依据
ISO/IEC 9899:2011 6.5(2)-undefined  
ISO/IEC 14882:2003 5(4)-unspecified  
ISO/IEC 14882:2011 1.9(15)-undefined  
ISO/IEC 14882:2011 5.17(1)  
ISO/IEC 14882:2017 8.18(1)  
ISO/IEC 9899:2011 Annex C  
<br/>

#### 参考
C++ Core Guidelines ES.43  
SEI CERT EXP50-CPP  
MISRA C++ 2008 5-0-1  
MISRA C 2012 13.2  
<br/>
<br/>

### <span id="ID_unexpectedPrecedence">▌R10.2.4 注意运算符优先级，避免非预期的结果</span>

ID_unexpectedPrecedence&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

对运算符优先级的错误理解是产生逻辑错误的主要原因之一。  
  
示例：
```
int foo(bool cond) {
    return 1 + cond? 2: 3;  // Rather suspicious
}
```
加号的优先级大于三元运算符，但 cond 是 bool 型变量，所以这种情况十分可疑。  
  
很可能应改为：
```
int foo(bool cond) {
    return 1 + (cond? 2: 3);
}
```
<br/>
<br/>

#### 参考
CWE-783  
<br/>
<br/>

### <span id="ID_illPtrDiff">▌R10.2.5 不在同一数组中的指针不可比较或相减</span>

ID_illPtrDiff&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

不在同一数组中的指针比较或相减属于逻辑错误，会导致标准未定义的行为。  
  
示例：
```
ptrdiff_t foo() {
    int a = 0, b = 0;
    int arr0[10] = {};
    int arr1[10] = {};

    if (&a < &b) {  // Non-compliant
        ....
    }
    return &arr1[5] - arr0;  // Non-compliant
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.7(6)-undefined  
ISO/IEC 14882:2011 5.7(6)-undefined  
ISO/IEC 14882:2011 8.7(5)-undefined  
<br/>

#### 参考
MISRA C 2004 17.3  
MISRA C++ 2008 5-0-17  
MISRA C++ 2008 5-0-18  
C++ Core Guidelines ES.62  
<br/>
<br/>

### <span id="ID_illBoolOperation">▌R10.2.6 bool 值不应参与位运算、大小比较、数值增减</span>

ID_illBoolOperation&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

bool 值只能为真或假，不具有“大小”等数值意义，bool 值参与位运算、大小比较、数值增减是不合理的。  
  
示例：
```
bool foo(unsigned flags, unsigned flag) {
    return flags & flag != 0;               // Non-compliant
}
```
由于 != 的优先级高于 &，所以例中的 return 语句相当于先判断 flag 是否为 0，再将这个 bool 型的结果与 flags 按位与，这是没有意义的。  
  
应改为：
```
bool foo(unsigned flags, unsigned flag) {
    return (flags & flag) != 0;             // Compliant
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 5.3.2(1 2) D.1-deprecated  
<br/>

#### 参考
CWE-1024  
CWE-1025  
CWE-682  
CWE-783  
MISRA C 2004 12.7  
MISRA C 2012 10.1  
MISRA C++ 2008 5-0-21  
<br/>
<br/>

### <span id="ID_illFormedCompoundAssignment">▌R10.2.7 不应出现复合赋值的错误形式</span>

ID_illFormedCompoundAssignment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

如下形式的复合赋值表达式（设 a 和 x 为变量或表达式）：
```
a -= a - x;
a /= a / x;
a &= a & x;
a |= a | x;
a ^= a ^ x;
```
是没有意义的，均为常见笔误，应将复合赋值改为普通赋值，或去掉重复的子表达式。
<br/>
<br/>

#### 参考
CWE-682  
<br/>
<br/>

### <span id="ID_suspiciousCompoundAssignment">▌R10.2.8 避免出现复合赋值的可疑形式</span>

ID_suspiciousCompoundAssignment&emsp;&emsp;&emsp;&emsp;&nbsp;:question: expression suspicious

<hr/>

如下形式的复合赋值表达式（设 a 和 x 为变量或表达式）：
```
a += a + x;
a *= a * x;
a %= a % x;
a <<= a << x;
a >>= a >> x;
```
均为常见笔误，但在特定需求下也有其逻辑意义，故对这种表达式应给出可疑提醒。即使这类表达式没有逻辑错误，也应该换成普通赋值表达式，以提高可读性。  
  
示例：
```
a += a + x;   // Rather suspicious
```
应改为：
```
a = a + x;         // OK
a = 2 * a + x;     // OK
a = a + (a + x);   // OK
```
<br/>
<br/>

#### 参考
CWE-682  
<br/>
<br/>

### <span id="ID_illSelfCompoundAssignment">▌R10.2.9 &=、|=、-=、/=、%= 左右子表达式不应相同</span>

ID_illSelfCompoundAssignment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

&=、|= 左右子表达式如果相同则没有任何效果，\-=、/=、%= 左右子表达式相同则结果总为 1 或 0，这种表达式往往意味着笔误或逻辑错误。  
  
示例（设 a 为变量或表达式）：
```
a &= a;  // Non-compliant, no effect
a |= a;  // Non-compliant, no effect
```
如果目的是清零或置 1，也不建议使用下列表达式：
```
a -= a;  // Non-compliant, tedious
a /= a;  // Non-compliant, low efficiency
a %= a;  // Non-compliant, low efficiency
```
对于高级语言来说，应该直接将变量赋值为 0 或 1，而不是采用更繁琐甚至低效的方式。
<br/>
<br/>

#### 参考
CWE-682  
<br/>
<br/>

### <span id="ID_oddNullAssignment">▌R10.2.10 不应使用 NULL 对非指针变量赋值</span>

ID_oddNullAssignment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

标识符 NULL 只应该用来表示空指针，否则会对代码阅读造成误导，而且也可能是书写错误。  
  
示例：
```
const int i = NULL;        // Non-compliant
const char c = NULL;       // Non-compliant
void foo(bool x = NULL);   // Non-compliant
```
应改为：
```
const int i = 0;           // Compliant
const char c = '\0';       // Compliant
void foo(bool x = false);  // Compliant
```
<br/>
<br/>

#### 参考
MISRA C++ 2008 4-10-1  
<br/>
<br/>

### <span id="ID_stickyAssignmentOperator">▌R10.2.11 注意赋值运算符与一元运算符的空格方式</span>

ID_stickyAssignmentOperator&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

如果 = 与 \+、\-、\*、!、&、\~ 等一元运算符之间没有空格，而一元运算符与其子表达式之间有空格，是一种非常怪异的格式，也可能是 \+=、\-=、\*=、&= 等复合赋值运算符的笔误。  
  
示例：
```
a =+ b;    // Non-compliant
a =- b;    // Non-compliant
a =~ b;    // Non-compliant
a =! b;    // Non-compliant

a += b;    // Compliant
a = -b;    // Compliant
a = ~b;    // Compliant
a = !b;    // Compliant
```
<br/>
<br/>

#### 参考
CWE-480  
<br/>
<br/>

### <span id="ID_selfAssignment">▌R10.2.12 赋值运算符左右子表达式不应相同</span>

ID_selfAssignment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

赋值运算符左右子表达式相同是没有逻辑意义的，往往是笔误或残留代码。  
  
示例：
```
a = a;       // Non-compliant
a = b = a;   // Non-compliant
```
也可能是对语言特性不了解所致，如：
```
class A {
    int a;

public:
    A(int a) {
        a = a;   // Non-compliant, ‘a’ is not the member
    }
};
```
例中构造函数对成员 a 的赋值是无效的，应改为 this\->a = a;  
  
有时这种代码被用来消除编译警告，编译器可能会报出没有被用到的参数，将参数赋值给自身可去除警告，但这并不是一种好方法，引入了没有实际意义的代码，改进方法可参见 ID\_paramNotUsed。  
  
有时为了设置调试断点，但又找不到合适的位置，可以增加这种代码作为断点，但这种非正式的代码是不应被保留的。
<br/>
<br/>

#### 参考
CWE-682  
<br/>
<br/>

### <span id="ID_selfDivision">▌R10.2.13 除法和求余运算符左右子表达式不应相同</span>

ID_selfDivision&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

除法或求余运算符左右子表达式相同，结果总为 1 或 0 以及产生除零异常，往往是某种笔误。  
  
示例：
```
int foo(int* p) {
    return p[0] % p[0];  // Non-compliant
}
```
<br/>
<br/>

#### 参考
CWE-682  
<br/>
<br/>

### <span id="ID_selfSubtraction">▌R10.2.14 减法运算符左右子表达式不应相同</span>

ID_selfSubtraction&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

与自身做减法，结果总为 0，往往是某种笔误。  
  
示例：
```
int *p0, *p1;
....
ptrdiff_t d = p0 - p0;  // Non-compliant
```
例中减法表达式是没有意义的，很可能是 p1 被误写成了 p0。
<br/>
<br/>

#### 参考
CWE-682  
<br/>
<br/>

### <span id="ID_selfExclusiveOr">▌R10.2.15 异或运算符左右子表达式不应相同</span>

ID_selfExclusiveOr&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

与自身异或，结果总为 0，而且可能意味着某种错误。  
  
对变量的清零，有一种惯用写法：
```
a = a ^ a;   // Non-compliant
```
这种复杂的写法在 C/C\+\+ 等高级语言中已不再提倡，a ^= a 也不再提倡，应将变量直接赋值为 0，编译器会作更好的优化。
<br/>
<br/>

#### 参考
CWE-682  
<br/>
<br/>

### <span id="ID_minusOnUnsigned">▌R10.2.16 负号不应作用于无符号整数</span>

ID_minusOnUnsigned&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

负号作用于无符号整数，结果仍是无符号整数，令人费解易产生意料之外的错误。  
  
示例：
```
unsigned int a = 1;
long long b = -a;    // Non-compliant, ‘b’ is 4294967295, confusing
```
例外：  
 - unsigned char、unsigned short 等可以“[类型提升](https://en.wikipedia.org/wiki/Type_conversion#Type_promotion)”为 int 的无符号类型可不受本规则约束  
 - \-1U、\-1UL、\-1ULL 作为 UINT\_MAX、ULONG\_MAX、ULLONG\_MAX 的惯用简写形式可不受本规则约束
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 6.5.3.3(3)  
ISO/IEC 9899:2011 6.5.3.3(3)  
<br/>

#### 参考
MISRA C 2004 12.9  
MISRA C 2012 10.1  
MISRA C++ 2008 5-3-2  
<br/>
<br/>

### <span id="ID_repeatedUnaryOperators">▌R10.2.17 不应重复使用一元运算符</span>

ID_repeatedUnaryOperators&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

重复的一元运算符没有意义，为常见笔误。  
  
示例：
```
int a = 1;
int b = ~~a;    // Non-compliant
int c = -+a;    // Non-compliant
int d = - -a;   // Non-compliant
```
例外：
```
bool e = !!a;   // Let it go
```
两个连续的 ! 是 bool 转换的惯用方法，可不受本规则约束。
<br/>
<br/>
<br/>

### <span id="ID_evalOverflow">▌R10.2.18 运算结果不应溢出</span>

ID_evalOverflow&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

运算结果超出对应类型的存储范围往往意味着错误。  
  
这种情况对于有符号整数，会引发标准未定义的行为，而对于无符号整数，则只保留有效范围内的值，相当于一种模运算，标准认为这是一种“语言特性”，规定无符号整数不存在溢出，然而实践表明，运算结果超出无符号整数的范围很容易引起意料之外的问题，所以不论是否有符号，均应规避这种问题。  
  
示例：
```
uint64_t a = 0xffffffffU + 1;  // Non-compliant
```
例中 0xffffffffU 是 32 位无符号整数的最大值，根据 C/C\+\+ 语言的计算规则，0xffffffffU \+ 1 仍是 32 位无符号整数，不会自动转为 64 位整数，所以 a 的值是 0，而不会是 0x100000000。  
  
应改为：
```
uint64_t a = 0xffffffffULL + 1;  // Compliant
```
又如：
```
int32_t a = foo();
int32_t b = bar();
int64_t c = a * b;  // Rather suspicious
```
例中 a 和 b 是 32 位整数，a\*b 仍为 32 位整数，如果 a\*b 的预期结果超过了 32 位就会造成溢出，这也是很常见的错误。  
  
应改为：
```
int64_t c = static_cast<int64_t>(a) * b;  // OK
```
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 6.5(5)-undefined  
ISO/IEC 9899:2011 6.2.5(9)  
ISO/IEC 14882:2011 5(4)-undefined  
<br/>

#### 参考
CWE-190  
C++ Core Guidelines ES.103  
C++ Core Guidelines ES.104  
<br/>
<br/>

### <span id="ID_bitwiseOperOnSigned">▌R10.2.19 位运算符不应作用于有符号整数</span>

ID_bitwiseOperOnSigned&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

符号位在位运算方面没有逻辑意义，对负数进行位运算往往意味着逻辑错误。  
  
示例：
```
int foo(signed s, unsigned u) {
    return s & u;                 // Non-compliant
}

int bar(signed s, unsigned u) {
    if (s < 0) {
        int a = s << u;           // Non-compliant, undefined
        int b = s >> u;           // Non-compliant, implementation-defined
        return a + b;
    }
    return 0;
}
```
例中 s 为有符号整数，其符号位对位运算没有意义，对负数左移会导致标准未定义的行为，对负数右移则由实现定义。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 5.8(2)-undefined  
ISO/IEC 14882:2011 5.8(3)-implementation  
<br/>

#### 参考
CWE-682  
MISRA C 2004 12.7  
MISRA C 2012 10.1  
MISRA C++ 2008 5-0-21  
C++ Core Guidelines ES.101  
<br/>
<br/>

### <span id="ID_illShiftCount">▌R10.2.20 移位数量不可超过相关类型比特位的数量</span>

ID_illShiftCount&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

移位数量超过相关类型比特位的数量会导致标准未定义的行为。  
  
示例：
```
uint64_t foo(uint32_t u) {
    return u << 32;          // Non-compliant, undefined behavior
}
```
例中 u 为 32 位整型变量，将其左移 32 位并不能得到 64 位整数，反而会导致标准未定义的行为。  
  
应改为：
```
uint64_t foo(uint32_t u) {
    return static_cast<uint64_t>(u) << 32;   // Compliant
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.8(1)-undefined  
ISO/IEC 14882:2011 5.8(1)-undefined  
ISO/IEC 14882:2017 8.8(1)-undefined  
<br/>

#### 参考
MISRA C++ 2008 5-8-1  
<br/>
<br/>

### <span id="ID_invalidCommaSubExpression">▌R10.2.21 逗号表达式的子表达式应具有必要的副作用</span>

ID_invalidCommaSubExpression&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

逗号表达式的子表达式应具有必要的副作用，否则没有意义。  
  
示例：
```
void foo(int& a, int& b) {
    a, b = 0, 1;             // Non-compliant
}
```
例中逗号表达式有 3 个子表达式，只有第 2 个子表达式有效，第 1 和第 3 个没有意义。  
  
应改为：
```
void foo(int& a, int& b) {
    a = 0, b = 1;            // Compliant, but bad
}
```
本规则集合不建议使用逗号表达式，将逗号表达式拆分成合理的语句是更好的选择。  

```
void foo(int& a, int& b) {
    a = 0;
    b = 1;                   // Compliant, good
}
```
<br/>
<br/>

#### 相关
ID_forbidCommaExpression  
<br/>
<br/>

### <span id="expression.comparison">10.3 Comparison</span>

### <span id="ID_illComparison">▌R10.3.1 比较运算应在正确的取值范围内进行</span>

ID_illComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

应在正确的取值范围内进行比较，否则会造成恒为真或恒为假的无效结果。  
  
示例：
```
void foo(string& txt, string& sub) {
    size_t n = txt.find(sub);
    if (n >= 0) {               // Non-compliant, always true
        ....
    }
}
```
无符号变量不可能小于 0，也一定大于等于 0，例中 n >= 0 恒为真，是没有意义的条件。  
  
又如：
```
typedef unsigned short X;
void fun(X x) {
    if (x == -1) {   // Non-compliant, always false
        ....
    }
}
```
例中 x 为无符号短整型变量，其取值范围为 \[0, 65535\]，x == \-1 恒为假。由于“[类型提升](https://en.wikipedia.org/wiki/Type_conversion#Type_promotion)”，x 会被转为 int 型再与 \-1 比较，x 恒为正数，\-1 为负数，故不可能相等。  
  
对于有符号字符型变量，与其比较的数值不在 \[\-128, 127\] 范围内时，也是无效的：
```
CodePage encodingDetect(const char* src) {
    char b0 = src[0];
    char b1 = src[1];
    char b2 = src[2];
    if (b0 == 0xef && b1 == 0xbb && b2 == 0xbf) {   // Non-compliant, always false
        return cpUtf8;
    }
    ....
}
```
即使例中 b0 的二进制绝对值确实为 0xef，但由于“[类型提升](https://en.wikipedia.org/wiki/Type_conversion#Type_promotion)”，b0 转为 int 型后为负数，0xef 为正数，比较的结果恒为假。char 型变量是否有符号由实现定义，可参见 ID\_plainNumericChar 的进一步说明，将 b0 等变量设为 unsigned char 可解决这个问题。
<br/>
<br/>

#### 相关
ID_switch_caseOutOfRange  
<br/>

#### 参考
CWE-697  
CWE-1024  
CWE-1025  
<br/>
<br/>

### <span id="ID_illFloatComparison">▌R10.3.2 不应使用 == 或 != 判断浮点数是否相等</span>

ID_illFloatComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

一般来说，除了可以记作 a \* 2<sup>n</sup>（a、n 为整数）的浮点数值可以被精确存储之外，其他均为近似值。用 == 或 != 判断浮点数（float、double、long double）是否相等往往得不到预期的结果。  
  
如 0、1、2、3、1.5、1.25、… 可以被精确存储，而除此之外绝大部分数值如 0.1、0.2、0.3、… 只能存储其近似值。  
  
示例：
```
float f = 1;
f /= 10;
if (f == 0.1) {     // Non-compliant, do not use ‘==’ or ‘!=’
    cout << "OK";
} else {
    cout << "Oops";
}
```
输出 Oops，这是因为 f == 0.1 在运算时将变量和常量转为 double 型，而转换过程中生成了两个不同的对 0.1 的近似值（如 0.10000000149011611938 和 0.10000000000000000555），其结果自然为 false。  
这非常容易造成意料之外的混乱，所以判断浮点数是否相等不应直接使用 == 或 != 运算符，即使浮点数可以被精确存储。  
  
解决方法：
```
bool feq(float a, float b, float e = 0.0001f) {
    return fabs(a - b) < e;
}
```
利用 feq 函数判断浮点数是否相等，如果两个浮点数的差值非常小则可以认为相等，其中 fabs 为计算浮点数差值绝对值的函数，如果差值绝对值小于 e 则认为相等，否则不等。
```
if (feq(f, 0.1)) {   // Compliant
    cout << "OK";
}
```
<br/>
<br/>

#### 参考
CWE-1025  
MISRA C 2004 13.3  
MISRA C++ 2008 6-2-2  
<br/>
<br/>

### <span id="ID_illPtrStrComparison">▌R10.3.3 指针不应与字符串常量直接比较</span>

ID_illPtrStrComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

直接比较指针和字符串常量的结果往往总是 false，应改用字符串比较函数。  
  
示例：
```
bool is_name(const char* p) {
    return p == "bar";          // Non-compliant
}
```
如果例中 is\_name 函数只接受常量字符串作为参数，该函数在某些环境中也可能正常工作，如：
```
if (is_name("foo")) {   // May be false
    ....
}
if (is_name("bar")) {   // May be true
    ....
}
```
然而相同的字符串常量是否一定拥有相同的地址呢？对这个问题不同的编译器有不同的实现，有可移植性要求的代码要规避这种问题，而且这种问题极易导致错误，一般的程序都应该避免这种问题。  
  
应改为：
```
bool is_name(const char* p) {
    return !strcmp(p, "bar");   // Compliant
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.13.4(2)-implementation  
ISO/IEC 14882:2011 2.14.5(12)-implementation  
ISO/IEC 14882:2011 5.13.5(16)-unspecified  
<br/>

#### 参考
CWE-595  
CWE-697  
CWE-1024  
CWE-1025  
<br/>
<br/>

### <span id="ID_differentEnumComparison">▌R10.3.4 不应比较非同类枚举值</span>

ID_differentEnumComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

比较非同类枚举值相当于比较不同类别的事物，没有逻辑意义，往往是设计缺陷或逻辑错误。  
  
示例：
```
enum Fruit { apple, orange, banana };
enum Pet { hamster, chipmunk, chinchilla };

void foo(Fruit f) {
    if (f == hamster) {  // Non-compliant
        ....
    }
}

void bar(Pet p) {
    switch (p) {
    case apple:     // Non-compliant
        ....
        break;
    case chipmunk:  // Compliant
        ....
        break;
    }
}
```
<br/>
<br/>
<br/>

### <span id="ID_selfComparison">▌R10.3.5 比较运算符左右子表达式不应相同</span>

ID_selfComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

与自身的比较是没意义的，往往是某种笔误。  
  
示例（设 a 为变量或表达式）：
```
a == a   // Non-compliant
a != a   // Non-compliant
a > a    // Non-compliant
a >= a   // Non-compliant
a < a    // Non-compliant
a <= a   // Non-compliant
```
例外：
```
void foo(float a) {
    if (a != a) {     // Let it go
        ....
    }
}
```
当 a != a 为真时表示 a 为无效值“[NaN](https://en.wikipedia.org/wiki/NaN)”，这是判断浮点数是否有效的惯用方法，可不受本规则约束。
<br/>
<br/>

#### 参考
CWE-1025  
<br/>
<br/>

### <span id="ID_successiveComparison">▌R10.3.6 比较运算不可作为另一个比较运算的直接子表达式</span>

ID_successiveComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

在 C/C\+\+ 语言中，连续的比较运算是没有意义的，本规则是 ID\_illBoolOperation 的特化。  
  
示例：
```
bool foo(int a, int b, int c) {
    return a >= b >= c;           // Non-compliant, logic error
}
```
例中 a >= b 的结果为 bool 型，bool 型数据是没有大小概念的，这个结果再与 c 比较大小是没有意义的。  
  
例外：
```
a > b == true        // Compliant, but odd
a != b != true       // Compliant, but odd
(a > b) == (c > d)   // Compliant, but complex
```
如果是判断两个 bool 表达式是否相等，可不受本规则约束，但这种复杂的形式并不值得提倡。
<br/>
<br/>

#### 相关
ID_illBoolOperation  
<br/>

#### 参考
CWE-697  
CWE-1024  
CWE-1025  
<br/>
<br/>

### <span id="expression.call">10.4 Call</span>

### <span id="ID_returnValueIgnored">▌R10.4.1 不应忽略重要的返回值</span>

ID_returnValueIgnored&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

与资源分配、信息获取、状态判断有关的返回值不应被忽略。  
  
示例：
```
void foo(string& s) {
    s.empty();          // Non-compliant
}
```
例中 empty 返回字符串是否为空，如果忽略返回值会使函数调用失去意义。示例代码很可能是误将 std::string 的 empty 函数当成了 MFC CString 的 Empty 函数，这两个函数有不同的功能，需区别对待。  
  
又如：
```
[[nodiscard]] int getStatus();

void baz() {
    getStatus();   // Non-compliant
}
```
C\+\+ 中由用户添加的具有 \[\[nodiscard\]\] 属性的函数，返回值也不应被忽略。
<br/>
<br/>

#### 参考
MISRA C 2012 17.7  
MISRA C++ 2008 0-1-7  
<br/>
<br/>

### <span id="ID_wrongUseOfReturnValue">▌R10.4.2 不可臆断返回值的意义</span>

ID_wrongUseOfReturnValue&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

对接口的使用应遵循接口文档，不可臆断返回值的意义，否则造成逻辑错误。  
  
示例：
```
void foo(const string& s) {
    if (s.find("bar")) {      // Non-compliant
        ....
    }
}
```
例中 find 函数返回 "bar" 在 s 中的位置，当 s 中不存在 "bar" 时返回 string::npos，将 find 函数的返回值转为 bool 型是没有逻辑意义的。  
  
应改为：
```
void foo(const string& s) {
    if (s.find("bar") != string::npos) {   // Compliant
        ....
    }
}
```
想当然地认为返回 0 表示失败或不存在，非 0 表示成功或存在，是造成错误的常见原因。  
  
又如：
```
bool gt(const char* a, const char* b) {
    return strcmp(a, b) == 1;             // Non-compliant
}
```
strcmp 函数的返回值可以是等于、大于或小于 0 的任意整数，分别对应字符串的等于、大于或小于关系，认为其只能返回 0、1 或 \-1 是一种常见的误解。  
  
应改为：
```
bool gt(const char* a, const char* b) {
    return strcmp(a, b) > 0;              // Compliant
}
```
strcmp、wcscmp 以及 memcmp 等函数不应与 0 之外的任何值比较。  
  
下列函数的返回值不应与 0 比较，也不应转为 bool 型：  
 - open、create 等 Linux 系统调用，失败时返回负数，成功时返回非负数  
 - CreateFile、CreateNamedPipe 等 Windows API，失败时返回 INVALID\_HANDLE\_VALUE，而不是 0  
 - HRESULT 型 Windows API 返回值，负数表示失败、非负数表示成功  
  
另外，有相当一部分函数成功时返回 0，失败时返回非 0，如 access、chmod、rename 等 Linux 系统调用，不可将其返回值当作 bool 型使用。
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 7.21.4  
ISO/IEC 9899:2011 7.24.4  
<br/>

#### 参考
CWE-253  
<br/>
<br/>

### <span id="ID_objectSlicing">▌R10.4.3 避免对象切片</span>

ID_objectSlicing&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

将派生类对象复制为基类对象的行为称为“[对象切片（object slicing）](https://en.wikipedia.org/wiki/Object_slicing)”，基类对象不再持有派生类的属性，不再遵循多态机制，意味着某种精度上的损失，往往会造成意料之外的错误。  
  
示例：
```
struct A { .... };
struct B: A { .... };

void foo(A);

A a;
B b;

a = b;    // Slicing
foo(b);   // Slicing

vector<A> v{b};   // Slicing
v.push_back(b);   // Slicing
```
尤其是函数传参或容器收纳对象时发生切片，会引起相当大的困惑，明明传入的是派生类对象，但虚函数都不生效了，所以要求多态性的接口或容器均应使用指针或引用。  
  
在少数情况下，对象切片可能也有其逻辑意义，但不建议“隐式切片”，应定义特定名称的函数标明这是一种特殊处理，如：
```
A a;
B b;

a = b;                   // Bad, implicit slicing
a = to_base_object(b);   // OK
```
其中 to\_base\_object 是一个返回基类对象的函数，表示有意为之。
<br/>
<br/>

#### 相关
ID_paramMayBeSlicing  
<br/>

#### 参考
C++ Core Guidelines ES.63  
C++ Core Guidelines C.145  
SEI CERT OOP51-CPP  
<br/>
<br/>

### <span id="ID_explicitDtorCall">▌R10.4.4 避免显式调用析构函数</span>

ID_explicitDtorCall&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: expression suggestion

<hr/>

显式调用析构函数会提前结束对象的生命周期，之后对该对象的任何访问都会导致标准未定义的行为，对于在栈上定义的对象，流程离开相关作用域时会再次自动调用其析构函数，对于动态创建的对象，用 delete 回收时也会调用其析构函数，使对象在生命周期之外被访问。  
  
示例：
```
class A {
    int* p = new int[123];

public:
   ~A() {
        delete[] p;
    }
};

void foo() {
    A a;
    a.~A();   // Non-compliant, explicitly call the destructor
}             // ~A() twice called, crash...
```
例中对象 a 的析构函数被显式调用，foo 返回前会再次调用析构函数，造成内存被重复释放。应去掉显式调用，由类提供提前释放资源的方法，并保证资源不会被重复释放。  
  
例外：
```
char* p = new char[sizeof(Type)];
Type* q = new (p) Type;
....
q->~Type();   // Compliant, used with replacement new
delete[] p;
```
与 replacement new 配合的显式析构是实现容器或内存池的常规手段，可不受本规则约束。
<br/>
<br/>

#### 相关
ID_missingResetNull  
<br/>

#### 依据
ISO/IEC 14882:2011 12.4(15)-undefined  
<br/>
<br/>

### <span id="ID_nonPODVariadicArgument">▌R10.4.5 不应将非 POD 对象传入可变参数列表</span>

ID_nonPODVariadicArgument&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

如果将非“[POD](https://en.cppreference.com/w/cpp/named_req/PODType)”对象传入可变参数列表，程序的行为在 C\+\+03 中是未定义的，在 C\+\+11 中是部分由实现定义的。  
  
示例：
```
void foo(int n, ...) {
    va_list vl;
    ....
    string x = va_arg(vl, string);   // Undefined behavior
    ....
}

void bar(string& s) {
    foo(1, s);          // Non-compliant, undefined behavior
}
```
例中 string 类对象 s 不是 POD 对象，其拷贝构造和析构过程难以与可变参数列表机制兼容，通过 va\_arg 也难以获取正确的对象。  
  
又如：
```
struct A {
    string s;
    operator const char*() const {
        return s.c_str();
    }
};

void foo(const A& a) {
    printf("%s\n", a);   // Non-compliant
}
```
即使对象有转为 const char\* 的方法，在可变参数列表中也是无效的，printf 无法正确获取字符串地址，造成内存访问错误。  
  
应改为：
```
void foo(const A& a) {
    printf("%s\n", static_cast<const char*>(a));   // Compliant
}
```
<br/>
<br/>

#### 相关
ID_badVaArgType  
<br/>

#### 依据
ISO/IEC 14882:2003 5.2.2(7)-undefined  
ISO/IEC 14882:2011 5.2.2(7)-implementation  
<br/>

#### 参考
CWE-686  
<br/>
<br/>

### <span id="ID_inconsistentFormatArgNum">▌R10.4.6 C 格式化字符串与其参数的个数应一致</span>

ID_inconsistentFormatArgNum&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

格式化字符串与其对应参数的个数应严格一致，否则会引发严重的运行时堆栈错误。  
  
示例：
```
void foo(int type, const char* msg) {
    printf("Error (type %d): %s\n", type);  // Non-compliant
}
```
示例代码的格式化参数需要两个，但只传入一个，参数 msg 之外的不相关栈信息也会被读入。  
  
由于可变参数列表自身的局限，很难在编译时发现这种问题，有些编译器会检查 printf、sprintf 等标准函数，但无法检查自定义函数，建议在 C\+\+ 代码中禁用可变参数列表和 C 风格的格式化函数。
<br/>
<br/>

#### 相关
ID_inconsistentFormatArgType  
ID_forbidCStringFormat  
<br/>

#### 依据
ISO/IEC 9899:2011 7.16.1.1(2)-undefined  
ISO/IEC 9899:2011 7.21.6.1(2)-undefined  
<br/>

#### 参考
SEI CERT FIO47-C  
<br/>
<br/>

### <span id="ID_inconsistentFormatArgType">▌R10.4.7 C 格式化字符串与其参数的类型应一致</span>

ID_inconsistentFormatArgType&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

格式化字符串与其对应参数的类型应严格一致，否则会引发严重的运行时堆栈错误。  
  
示例：
```
void foo(const string& msg) {
    printf("Message: %s\n", msg);  // Non-compliant
}
```
例中 %s 要求对应 char\* 型参数，则 msg 实际上是 string 类型，造成栈读取错误。  
  
应改为：
```
void foo(const string& msg) {
    cout << "Message: " << msg << '\n';  // Compliant
}
```
由于可变参数列表自身的局限，很难在编译时发现这种问题，有些编译器会检查 printf、sprintf 等标准函数，但无法检查自定义函数，建议在 C\+\+ 代码中禁用可变参数列表和 C 风格的格式化函数。
<br/>
<br/>

#### 相关
ID_nonPODVariadicArgument  
ID_inconsistentFormatArgNum  
ID_forbidCStringFormat  
<br/>

#### 依据
ISO/IEC 9899:2011 7.16.1.1(2)-undefined  
ISO/IEC 9899:2011 7.21.6.1(2)-undefined  
<br/>

#### 参考
CWE-686  
SEI CERT FIO47-C  
<br/>
<br/>

### <span id="ID_forbidCStringFormat">▌R10.4.8 在 C++ 代码中禁用 C 风格字符串格式化方法</span>

ID_forbidCStringFormat&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: expression suggestion

<hr/>

printf、sprintf 等 C 风格字符串格式化方法，即由可变参数列表实现的格式化方法，主要问题有：  
 - 在编译期无法保证安全性，易出错或造成可移植性问题，提高了测试成本  
 - 与 C\+\+ 的强类型理念不符，也不在 C\+\+ 标准之内  
 - 只接受基本类型的参数，不利于数据的对象化管理  
  
示例：
```
size_t a = -1;
ptrdiff_t b = -2;
```
如果要按 16 进制打印 a，10 进制打印 b：
```
printf("%x %d", a, b);  // Non-compliant, #1
printf("%lx %ld", a, b);  // Non-compliant, #2
printf("%llx %lld", a, b);  // Non-compliant, #3
```
size\_t、ptrdiff\_t 等类型是由实现定义的，标准没有规定其是否一定对应 unsigned int、long 或 long long 类型，而 %d、%lx、%llx 只对应 int、long、long long 类型，所以示例代码都是不合理的。`#1` 在 64 位环境中会丢失数据，`#3` 在 32 位环境中会造成参数栈读取错误，`#2` 只在某些环境下可以正常工作不具备可移值性。  
  
在 C 语言中正确的做法是 a 对应 %zx，b 对应 %zd，如：
```
printf("%zx %zd", a, b);  // Non-compliant in C++, even if the result is correct
```
参数的类型与个数和占位符必须严格对应，否则就会导致未定义的行为，当参数较多时极易出错，利用 C\+\+ iostream 可规避这类问题：
```
std::cout << std::hex << a << ' ' << std::dec << b;  // Compliant
```
然而当参数较多时，iostream 的方式在形态上可能较为“松散”，可读性可能不如 printf 等函数，这个问题可参见 ID\_forbidVariadicFunction 的示例，用“[模板参数包](https://en.cppreference.com/w/cpp/language/parameter_pack)”等更安全的方法实现 printf 函数的功能。另外，C\+\+20 的“[std::format](https://en.cppreference.com/w/cpp/utility/format/format)”也提供了更多的格式化方法。
<br/>
<br/>

#### 相关
ID_forbidVariadicFunction  
<br/>

#### 依据
ISO/IEC 9899:2011 7.16.1.1(2)-undefined  
ISO/IEC 9899:2011 7.21.6.1(2)-undefined  
<br/>

#### 参考
C++ Core Guidelines SL.io.3  
<br/>
<br/>

### <span id="ID_forbidAtox">▌R10.4.9 禁用 atof、atoi、atol 以及 atoll 等函数</span>

ID_forbidAtox&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: expression warning

<hr/>

当字符串无法被正确转为数值时，stdlib.h 或 cstdlib 中的 atof、atoi、atol 以及 atoll 等函数存在标准未定义的行为。  
  
对于 C 语言应改用 strtof、strtol 等函数，对于 C\+\+ 语言应改用标准流转换的方式。  
  
示例：
```
cout << atoi("abcdefg") << '\n';        // Non-compliant
cout << atoi("100000000000") << '\n';   // Non-compliant
```
例中字符串“abcdefg”不表示数字，字符串“100000000000”超出了正常 int 型变量的范围，这些情况会导致标准未定义的行为。  
  
更严重的问题是无法通过这种函数判断转换是否成功，这种不确定性也意味着代码在实现上存在缺陷。  
  
C\+\+ 标准流转换示例：
```
int foo(const char* s) {
    int v = 0;
    stringstream ss(s);
    ss >> v;
    if (ss.fail()) {              // Or use ‘!ss.eof() || ss.fail()’
        throw some_exception();
    }
    return v;
}
```
本例通过 ss.fail() 判断字符串前面的字符是否可以转为 int 型变量，也可通过 !ss.eof() || ss.fail() 判断字符串整体是否可以转为 int 型变量。
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 7.20.1(1)-undefined  
ISO/IEC 9899:2011 7.22.1(1)-undefined  
<br/>

#### 参考
CWE-190  
MISRA C 2004 20.10  
MISRA C 2012 21.7  
MISRA C++ 2008 18-0-2  
<br/>
<br/>

### <span id="ID_implementationDefinedFunction">▌R10.4.10 避免使用由实现定义的库函数</span>

ID_implementationDefinedFunction&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

由实现定义的（implementation\-defined）库函数会增加移植或兼容等方面的成本。  
  
如：  
 - cstdlib、stdlib.h 中的 abort、exit、getenv 或 system 等函数  
 - ctime、time.h 中的 clock 等函数  
 - csignal、signal.h 中的 signal 等函数  
  
这些函数的行为取决于编译器、库或环境的生产厂家，同一个函数不同的厂家会有不同的实现，故称这种函数的行为是“由实现定义”的。有高可靠性要求的软件系统应避免使用这种函数，否则需明确各种实现上的具体差异，提高了移植、发布以及兼容性等多方面的成本。  
  
示例：
```
#include <cstdlib>

void foo() {
    abort();   // Non-compliant
}
```
标准规定调用 abort 后进程应被终止，但进程打开的流是否会被关闭，创建的临时文件是否会被清理等问题没明确定义。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 7.22.4.1(2)-implementation  
ISO/IEC 9899:2011 7.22.4.4(5)-implementation  
ISO/IEC 9899:2011 7.22.4.6(2)-implementation  
ISO/IEC 9899:2011 7.22.4.8(3)-implementation  
ISO/IEC 9899:2011 7.27.2.1(3)-implementation  
ISO/IEC 9899:2011 7.14.1.1(3)-implementation  
<br/>

#### 参考
MISRA C 2004 20.8  
MISRA C 2004 20.11  
MISRA C 2004 20.12  
MISRA C 2012 21.5  
MISRA C 2012 21.8  
MISRA C 2012 21.10  
MISRA C++ 2008 18-0-3  
MISRA C++ 2008 18-0-4  
MISRA C++ 2008 18-7-1  
<br/>
<br/>

### <span id="ID_unsuitableMove">▌R10.4.11 合理使用 std::move</span>

ID_unsuitableMove&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

std::move 的参数应为左值，返回值应直接作为接口的参数，除此之外的使用方式价值有限，且易产生错误。  
  
std::move 将左值转为右值，意在宣告对象的数据将被转移到其他对象，应由合适的接口完成数据转移。  
  
示例：
```
string foo();
string s = move(foo());  // Non-compliant
```
例中 foo 函数返回的是右值，如果再调用 std::move 是多余的，应将 std::move 去掉。  
  
又如：
```
string a("....");
string&& b = move(a);  // Non-compliant
string c(b);           // Not move construction
```
例中 b 是具有名称的右值引用，其实是左值，c 仍是拷贝构造。  
  
应改为：
```
string a("....");
string c(move(a));  // Compliant
```
这样构造 c 时会自动选取移动构造函数，避免了复制。  
  
又如：
```
string foo() {
    string s("....");
    ....
    return move(s);  // Non-compliant
}
```
例中 foo 函数返回对象，编译器会进行“[RVO（Return Value Optimization）](https://en.wikipedia.org/wiki/Copy_elision#Return_value_optimization)”优化，显式调用 move 是多余的，而且会干扰优化，不应出现 return std::move(....) 这种代码。  
  
应改为：
```
string foo() {
    string s("....");
    ....
    return s;  // Compliant
}
```
<br/>
<br/>

#### 参考
C++ Core Guidelines ES.56  
C++ Core Guidelines F.18  
C++ Core Guidelines F.48  
<br/>
<br/>

### <span id="ID_unsuitableForward">▌R10.4.12 合理使用 std::forward</span>

ID_unsuitableForward&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

std::forward 的参数应为“[转发引用（forwarding references）](https://en.cppreference.com/w/cpp/language/reference#Forwarding_references)”，返回值应直接作为接口的参数，除此之外的使用方式价值有限，且易产生错误。  
  
转发引用是类型为 T&& 的参数，T 为函数模板类型，无论左值还是右值均可被这种参数接受，而且 const、volatile 等属性也会被忽略，这种参数应通过 std::forward<T> 交由合适的接口处理。  
  
关于转发引用，可参见 ID\_illForwardingReference 的进一步说明。  
  
示例：
```
struct A { .... };

void foo(A&);        // #1
void foo(const A&);  // #2
void foo(A&&);       // #3

template <class T>
void bar(T&& x) {        // Forwarding reference
    foo(forward<T>(x));  // Compliant
}

void baz(const A& a) {
    A b(a);
    bar(b);    // Calls #1

    bar(a);    // Calls #2

    bar(A());  // Calls #3
}
```
例中 bar 接口的参数为转发引用，在 baz 函数中，bar 接口将左值、常量引用和临时对象分别转发给对应的 foo 接口，这种模式称为“完美转发”，std::forward 应在这种模式内使用。  
  
下面给出几种错误示例：
```
void bar(A&& x) {
    foo(forward<A>(x));  // Non-compliant, ‘x’ is not a forwarding reference
}

template <class T>
struct X {
    void bar(T&& x) {
        foo(forward<T>(x));  // Non-compliant, ‘x’ is not a forwarding reference
    }
};
```
注意，转发引用的类型只能是函数模板类型，非模板和类模板不构成转发引用。  

```
template <class T>
void bar(T&& x) {
    T y = forward<T>(x);  // Non-compliant, ‘y’ is always an lvalue
    foo(y);
}

template <class T>
void bar(T&& x) {
    foo(forward<T&>(x));  // Non-compliant, use ‘forward<T>(x)’
}
```
forward 的返回值应直接作为接口的参数，且只应使用 forward<T>。
<br/>
<br/>

#### 相关
ID_illForwardingReference  
<br/>

#### 参考
C++ Core Guidelines F.19  
<br/>
<br/>

### <span id="expression.sizeof">10.5 Sizeof</span>

### <span id="ID_sizeof_arrayParameter">▌R10.5.1 sizeof 不应作用于数组参数</span>

ID_sizeof_arrayParameter&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

当函数的形式参数为数组时，实际上是一个指针，对这种参数使用 sizeof 无法获取到数组大小，往往意味着错误。  
  
示例：
```
void fun(char arr[32]) {
    memset(arr, 0, sizeof(arr));  // Non-compliant
}
```
例中参数 arr 是一个指针，而不是一个真实的数组。  
  
在 C\+\+ 语言中，如果有必要将参数设为数组，建议使用引用的方式，如：
```
void fun(char (&arr)[32]) {
    memset(arr, 0, sizeof(arr));  // Compliant
}
```
这样 sizeof(arr) 的结果就会和预期一致。
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 6.7.5.3(7)  
ISO/IEC 9899:2011 6.7.6.3(7)  
<br/>

#### 参考
CWE-467  
<br/>
<br/>

### <span id="ID_sizeof_oddExpression">▌R10.5.2 sizeof 不应作用于逻辑表达式</span>

ID_sizeof_oddExpression&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

sizeof 作用于 <、>、<=、>=、==、!=、&&、|| 等逻辑表达式为常见笔误，逻辑运算符往往应该移出 sizeof 表达式。  
  
示例：
```
if (sizeof(a == x)) {  // Non-compliant, may be sizeof(a) == x
    ....
}
if (sizeof(a >= x)) {  // Non-compliant, may be sizeof(a) >= x
    ....
}
```
<br/>
<br/>
<br/>

### <span id="ID_sizeof_pointer">▌R10.5.3 sizeof 作用于指针是可疑的</span>

ID_sizeof_pointer&emsp;&emsp;&emsp;&emsp;&nbsp;:question: expression suspicious

<hr/>

sizeof 作用于指针获取到的是指针变量的大小，而不是指针指向内容的大小，sizeof 作用于指针很容易造成错误。  
  
示例：
```
void foo(int* p) {
    memset(p, 0, sizeof(p));   // Logic error
}
```
应改为：
```
void foo(int* p, int n) {
    memset(p, 0, n * sizeof(*p));   // OK
}
```
其中参数 n 是数组元素的个数。
<br/>
<br/>

#### 相关
ID_sizeof_pointerDivision  
<br/>

#### 参考
CWE-467  
<br/>
<br/>

### <span id="ID_sizeof_pointerDivision">▌R10.5.4 被除数不应是作用于指针的 sizeof 表达式</span>

ID_sizeof_pointerDivision&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

形如 sizeof(p)/n 的表达式往往是为了获取数组元素的个数，如果 p 是指针，sizeof(p) 只是指针变量的大小，并不是数组的大小，所以这种表达式往往意味着逻辑错误。  
  
示例：
```
void foo(T* p) {
    size_t n = sizeof(p[0]);
    qsort(p, sizeof(p) / n, n, cmp);  // Non-compliant, logic error
}
```
例中 sizeof(p) / n 并不能获取 p 所指数组中元素的个数。  
  
本规则是 ID\_sizeof\_pointer 的特化，sizeof 作用于指针是可疑的，如果这种表达式又作为被除数，就更加可疑了。
<br/>
<br/>

#### 相关
ID_sizeof_pointer  
<br/>

#### 参考
CWE-467  
<br/>
<br/>

### <span id="ID_sizeof_suspiciousAdd">▌R10.5.5 指针加减偏移量时计入 sizeof 是可疑的</span>

ID_sizeof_suspiciousAdd&emsp;&emsp;&emsp;&emsp;&nbsp;:question: expression suspicious

<hr/>

指针加减偏移量时会自动计入指针指向类型的大小，如果再计入 sizeof 的值，很可能是某种错误。  
  
示例：
```
int foo(int* p, int i) {
    return *(p + i * sizeof(int));   // Rather suspicious
}
```
如果 foo 函数是为了获取指针 p 之后第 i 个整数的值，那么这种实现是错误的，应改为：
```
int foo(int* p, int i) {
    return *(p + i);       // Right
}
```
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 6.5.6(8)  
ISO/IEC 9899:2011 6.5.6(8)  
<br/>

#### 参考
CWE-468  
<br/>
<br/>

### <span id="ID_sizeof_zeroComparison">▌R10.5.6 sizeof 表达式的结果不应与 0 或负数比较</span>

ID_sizeof_zeroComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

将 sizeof 表达式的结果与 0 或负数比较往往意味着逻辑错误。  
  
标准规定，如果 sizeof 作用于完整类型结果一定大于 0，如果作用于不完整的类型则无法通过编译。  
  
示例：
```
void foo(int* p) {
    if (sizeof(p)) {  // Non-compliant, always true
        ....
    }
}
```
注意，在某些 C 环境中，sizeof 作用于空结构体或联合体结果可能是 0，但这属于未定义的行为，如：
```
struct A {} a;
printf("%zu\n", sizeof(a));  // What is output?
```
空结构体或联合体在 C 标准中属于非法类型，示例代码可能会输出 0，也可能不会通过编译。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.3.3(1 6) 9(3)  
ISO/IEC 14882:2011 5.3.3(1 6) 9(3)  
ISO/IEC 14882:2017 8.3.3(1 6) 12(4)  
<br/>

#### 参考
CWE-1025  
<br/>
<br/>

### <span id="ID_sizeof_sizeof">▌R10.5.7 sizeof 不应再作用于 sizeof 表达式</span>

ID_sizeof_sizeof&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

sizeof(sizeof(....)) 等价于 sizeof(size\_t)，在实际应用中没有任何必要写成连续 sizeof 的形式，往往意味着逻辑错误，多数由复制粘贴或错误的宏展开导致。  
  
示例：
```
void foo() {
    T* p = (T*)malloc(sizeof(T));
    p->nLength = sizeof(sizeof(T));  // Non-compliant, copy-paste error
    ....
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.3.3(6)  
ISO/IEC 14882:2011 5.3.3(6)  
ISO/IEC 14882:2017 8.3.3(6)  
<br/>

#### 参考
CWE-682  
<br/>
<br/>

### <span id="ID_sizeof_NULL">▌R10.5.8 C++ 代码中 sizeof 不应作用于 NULL</span>

ID_sizeof_NULL&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

在 C\+\+ 语言中，标识符 NULL 并不能有效区分整型常量 0 和空指针，sizeof(NULL) 一类的表达式预期是获取指针变量的大小，而实际结果可能是整型变量的大小。  
  
示例：
```
size_t n = sizeof(NULL);     // Non-compliant
```
不同的编译器对示例代码有不同的处理，有些会把 NULL 当作指针，有些会当作常量 0。  
  
应改为：
```
size_t n = sizeof(nullptr);  // Compliant
```
<br/>
<br/>

#### 相关
ID_deprecatedNULL  
<br/>

#### 依据
ISO/IEC 9899:1999 7.17(3)-implementation  
ISO/IEC 9899:2011 7.19(3)-implementation  
ISO/IEC 14882:2003 C.2.2.3(1)-implementation  
ISO/IEC 14882:2011 C.3.2.4(1)-implementation  
ISO/IEC 14882:2017 C.5.2.7(1)-implementation  
<br/>

#### 参考
CWE-351  
<br/>
<br/>

### <span id="ID_sizeof_void">▌R10.5.9 sizeof 不可作用于 void</span>

ID_sizeof_void&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

void 表示不存在的类型，也是不完整的类型，sizeof 作用于 void 是没意义的，属于语言运用错误，也可能是 sizeof(void\*) 的笔误。  
  
示例：
```
size_t a = sizeof(void);   // Non-compliant
size_t b = sizeof(void*);  // Compliant
```
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 6.3.2.2(1)  
ISO/IEC 9899:1999 6.2.5(19)  
<br/>
<br/>

### <span id="expression.assertion">10.6 Assertion</span>

### <span id="ID_badAssertion">▌R10.6.1 断言中的表达式不应恒为真</span>

ID_badAssertion&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

恒为真的断言是没有意义的。  
  
示例：
```
void foo(int a[]) {
    assert(sizeof(a));        // Non-compliant
    assert("some comments");  // Non-compliant
}
```
也不建议使用恒为假的断言表示异常，在 C\+\+ 语言中应改用异常处理的方式。
```
void bar(int x) {
    if (x < 0) {
        assert(0);  // Bad, use exceptions instead
    }
}
```
<br/>
<br/>

#### 配置
names：断言函数或宏的名称，如 assert、_ASSERT_EXPR 等，用“|”分隔  
<br/>

#### 依据
ISO/IEC 9899:2011 7.2  
<br/>
<br/>

### <span id="ID_sideEffectAssertion">▌R10.6.2 断言中的表达式不应有副作用</span>

ID_sideEffectAssertion&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

断言中的表达式如果有副作用，不能保证在所有编译设置下都有效。  
  
如标准断言 assert 会受宏 NDEBUG 的影响，当该宏被定义时 assert 中的表达式不会被执行。  
  
示例：
```
void foo(int* p) {
    assert(++(*p) > 0);  // Non-compliant
}
```
表达式的副作用均应在 assert 之前完成：
```
void foo(int* p) {
    ++(*p);
    assert(*p > 0);      // Compliant
}
```
<br/>
<br/>

#### 配置
names：断言函数或宏的名称，如 assert、_ASSERT_EXPR 等，用“|”分隔  
<br/>

#### 相关
ID_macro_sideEffectArgs  
ID_unevaluatedSideEffect  
<br/>

#### 依据
ISO/IEC 9899:2011 7.2  
<br/>

#### 参考
SEI CERT PRE31-C  
<br/>
<br/>

### <span id="ID_complexAssertion">▌R10.6.3 断言中的表达式不应过于复杂</span>

ID_complexAssertion&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: expression suggestion

<hr/>

断言中的表达式不应过于复杂， 否则不易定位具体是哪一项不符合断言，不利于调试。  
  
对于“逻辑与”表达式应将各子表达式分成多个断言。  
  
示例：
```
void foo(int a, int b, int c) {
    assert(a != 0 && b > 10 && c == b + 1);  // Bad
}
```
应改为：
```
void foo(int a, int b, int c) {
    assert(a != 0);
    assert(b > 10);
    assert(c == b + 1);  // Good
}
```
本着使代码便于调试的理念展开工作，可有效降低测试及维护成本。
<br/>
<br/>

#### 配置
maxLogicOperatorCount：断言表达式中“逻辑与”运算符的最大数量，超过则报出  
names：断言函数或宏的名称，如 assert、_ASSERT_EXPR 等，用“|”分隔  
<br/>
<br/>

### <span id="expression.complexity">10.7 Complexity</span>

### <span id="ID_complexExpression">▌R10.7.1 运算符不应超过规定数量</span>

ID_complexExpression&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: expression suggestion

<hr/>

运算符超过一定数量意味着表达式过于复杂，易包含潜在的错误，更不利于调试与维护，应进行适当拆分。
<br/>
<br/>

#### 配置
maxLogicOperatorCount：逻辑运算符最大数量，超过则报出  
maxOperatorCount：运算符最大数量，超过则报出  
<br/>

#### 参考
C++ Core Guidelines ES.40  
<br/>
<br/>

### <span id="expression.other">10.8 Other</span>

### <span id="ID_accessPaddingData">▌R10.8.1 不应访问填充数据</span>

ID_accessPaddingData&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

内存中各对象之间可能存在填充数据，这种数据只为实现“[内存对齐](https://en.wikipedia.org/wiki/Data_structure_alignment)”而无数值意义，而且填充数据的值是标准未声明的。  
  
示例：
```
struct A {
    char a;
    long b;
};

void foo(A* x, A* y) {
    if (memcmp(x, y, sizeof(*x)) == 0) {  // Non-compliant
        ....
    }
}
```
如果按常见的 4 或 8 字节对齐，A 的成员 a 和 b 之间会存在填充数据，填充数据参与比较将得到错误的结果。  
  
应改为：
```
void foo(A* x, A* y) {
    if (x->a == y->a && x->b == y->b) {  // Compliant
        ....
    }
}
```
即使成员之间没有填充数据也不应使用 memcmp 等函数比较，应实现类型明确的比较接口以供调用，否则很容易产生错误。
<br/>
<br/>

#### 相关
ID_ignorePaddingData  
<br/>

#### 依据
ISO/IEC 9899:2011 6.2.6.2(5)-unspecified  
<br/>
<br/>

### <span id="ID_oddNew">▌R10.8.2 new 表达式只可用于赋值或当作参数</span>

ID_oddNew&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

new 表达式只应作为“=”的直接右子表达式，或直接作为参数，其他形式均有问题。  
  
本规则对 replacement new 不作要求。  
  
示例：
```
int& i = *new int(123);        // Non-compliant

if (new int[123]) {            // Non-compliant
    ....
}

char* p = new char[123] + n;   // Non-compliant
```
这种问题多数是由笔误或错误的宏展开造成的。
<br/>
<br/>

#### 相关
ID_multiAllocation  
<br/>
<br/>

### <span id="ID_oddSubscripting">▌R10.8.3 数组下标应为整形表达式</span>

ID_oddSubscripting&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

C/C\+\+ 语言规定，数组下标可以在中括号的右侧也可以在左侧，然而这只是一种理论上的设计，在实际代码中，应采用约定俗成的方式，即数组的名称在中括号的左侧，下标在中括号的右侧。  
  
示例：
```
void fun() {
    int a[5] = {1, 2, 3, 4};
    4[a] = 5;                       // Non-compliant, use a[4] instead
    for (int i = 0; i < 4; i++) {
        cout << i + 1[a] << '\n';   // Non-compliant, may be a[i + 1]
    }
}
```
由于 a\[4\] 相当于 \*(a\+4)，所以与其等价的 \*(4\+a) 也应该可以解释为 4\[a\]，但下标在左侧的写法容易造成运算符优先级相关的问题，将下标写在右侧才是符合惯例的方式。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 8.3.4(6)  
ISO/IEC 14882:2011 8.3.4(6)  
<br/>
<br/>

### <span id="ID_forbidCommaExpression">▌R10.8.4 禁用逗号表达式</span>

ID_forbidCommaExpression&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: expression suggestion

<hr/>

逗号表达式将多个语句合成一个表达式，易形成笔误并造成阅读困难。  
  
示例：
```
a = b++, b + 1;      // Non-compliant
a, b, c = 0, 1, 2;   // Non-compliant
delete p, q;         // Non-compliant
foo((a, b), c);      // Non-compliant
return a, b, c;      // Non-compliant
```
逗号运算符和其他运算符组合在一起，易造成各种错误。  
  
例外：
```
for (a = 0, b = 0; a < 100; a++, b++)  {   // let it go?
    ....
}
```
在 for 语句的第 1 个和第 3 个表达式中使用逗号表达式为惯用方式，但这种方式并不值得提倡，审计工具不妨通过配置决定是否放过这种方式。
<br/>
<br/>

#### 配置
allowCommaExpressionInForIteration：为 true 时放过 for 语句中的逗号表达式  
<br/>

#### 参考
MISRA C 2004 12.10  
MISRA C 2012 12.3  
MISRA C++ 2008 5-18-1  
<br/>
<br/>

### <span id="ID_redundantParentheses">▌R10.8.5 合理使用括号</span>

ID_redundantParentheses&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: expression suggestion

<hr/>

重复的、作用于单个对象或一元运算符的括号使代码显得繁琐，应去掉。  
  
宏定义中的括号不受本规则限制。  
  
示例：
```
a = 1 + (p[0]);      // Non-compliant
a = 2 + (p->n);      // Non-compliant
a = (sizeof(b));     // Non-compliant
a = (fun(b));        // Non-compliant
a = ((a + b)) * b;   // Non-compliant
```
应去掉多余的括号：
```
a = 1 + p[0];        // Compliant
a = 2 + p->n;        // Compliant
a = sizeof(b);       // Compliant
a = fun(b);          // Compliant
a = (a + b) * b;     // Compliant
```
但如果可以更好的表达逻辑意义，或不确定运算符优先级时，应及时使用括号，如：
```
if (a == b && b == c || x == y && y == z) {  // Bad
    ....
}
```
设例中两个 && 表达式对应两个逻辑步骤，虽然有无括号不影响计算结果，但建议加入括号提高可读性：
```
if ((a == b && b == c) || (x == y && y == z)) {  // Good
    ....
}
```
<br/>
<br/>
<br/>

## <span id="literal">11. Literal</span>

### <span id="ID_literal_suspiciousChar">▌R11.1 注意可疑的字符常量</span>

ID_literal_suspiciousChar&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: literal warning

<hr/>

注意字符常量的错误书写，如正反斜杠的误用，'\\n' 误写为 '/n'、'\\\\' 误写为 '//' 等。  
  
由于 C/C\+\+ 语言允许在单引号内写入多个字符来表示一个整形常量，如：
```
auto i = '/t';   // Non-compliant
```
例中 i 为 int 型变量，值可以为 12148，这种语言特性可以让一些笔误通过编译，造成不易察觉的问题。  
  
又如：
```
auto* tab = wcschr(str, L'/t');   // Non-compliant
```
在某些环境中执行结果和下列代码一样：
```
auto* tab = wcschr(str, L'/');
```
字符 t 将被忽略，这显然是错误的，L'/t' 应改为 L'\\t'。
<br/>
<br/>

#### 相关
ID_literal_multicharacter  
<br/>

#### 依据
ISO/IEC 14882:2011 2.13.2(1)-implementation  
ISO/IEC 14882:2011 2.14.3(1)-implementation  
ISO/IEC 14882:2017 5.13.3(2)-implementation  
<br/>
<br/>

### <span id="ID_literal_hardCodeChar">▌R11.2 字符常量中不可存在应转义而未转义的字符</span>

ID_literal_hardCodeChar&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: literal warning

<hr/>

在字符常量中，如果存在制表符或控制字符，应使用转义字符。  
  
示例：
```
char c = '	';  // Non-compliant
```
例中空白符为 tab，由于 tab 可以按不同的宽度被展示，往往会被代码的阅读者误解为空格，而且经过复制粘贴后有可能会变成数目不确定的空格，使代码难以维护。  
  
应使用转义字符：
```
char c = '\t';  // Compliant
```
<br/>
<br/>

#### 相关
ID_literal_hardCodeString  
<br/>
<br/>

### <span id="ID_literal_hardCodeString">▌R11.3 字符串常量中不可存在应转义而未转义的字符</span>

ID_literal_hardCodeString&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: literal warning

<hr/>

在字符串常量中，如果存在制表符或控制字符，应使用转义字符。  
  
示例：
```
const char* s = "a	b";  // Non-compliant
```
例中空白符为 tab，由于 tab 可以按不同的宽度被展示，往往会被代码的阅读者误解为空格，而且经过复制粘贴后有可能会变成数目不确定的空格，使代码难以维护。  
  
应使用转义字符：
```
const char* s = "a\tb";  // Compliant
```
<br/>
<br/>

#### 相关
ID_literal_hardCodeChar  
<br/>
<br/>

### <span id="ID_literal_nonStandardEsc">▌R11.4 不应使用非标准转义字符</span>

ID_literal_nonStandardEsc&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: literal warning

<hr/>

使用非标准转义字符会导致标准未定义的行为，也可能是忘了将反斜杠转义。  
  
示例：
```
string path("C:\Files\x.cpp");   // Non-compliant
```
例中 \\F 不是标准转义字符，\\x 也不符合 16 进制转义字符的格式，这显然是路径中的反斜杠忘了转义。  
  
附 C/C\+\+ 标准转义字符：
```
'\a'          // Alert
'\b'          // Backspace
'\f'          // Formfeed page break
'\n'          // New line
'\r'          // Carriage return
'\t'          // Horizontal tab
'\v'          // Vertical tab
'\\'          // Backslash
'\?'          // Question mark
'\''          // Single quotation mark
'\"'          // Double quotation mark
'\0'          // Null character
'\ddd'        // Any character, ‘d’ is an octal number
'\xhh'        // Any character, ‘h’ is a hex number
'\uhhhh'      // Universal character name, ‘h’ is a hex number
'\Uhhhhhhhh'  // Universal character name, ‘h’ is a hex number
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.13.2(3)-undefined  
ISO/IEC 14882:2011 2.14.3(3)-implementation  
ISO/IEC 14882:2017 5.13.3(7)-implementation  
<br/>

#### 参考
MISRA C 2004 4.1  
MISRA C++ 2008 2-13-1  
<br/>
<br/>

### <span id="ID_literal_hybridConcat">▌R11.5 不同前缀的字符串常量不可连接在一起</span>

ID_literal_hybridConcat&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: literal warning

<hr/>

L、U、u、u8 等字符串前缀表示不同的类型，不可连接在一起。  
  
示例：
```
auto* a = L"123" U"456";   // Non-compliant
auto* b = U"123" u"456";   // Non-compliant
```
应保持一致：
```
auto* a = L"123" L"456";   // Compliant
auto* b = U"123" U"456";   // Compliant
```
C\+\+03 规定宽字符串与窄字符串连接会导致未定义的行为，C\+\+11 规定如果一个字符串有前缀另一个没有，结果以有前缀的为准，其他情况由实现定义，如：
```
auto* x = L"123" "456";    // Undefined in C++03
auto* y = L"123" "456";    // A wide string in C++11
auto* z = L"123" u8"456";  // Ill-formed in C++11
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 2.13.4(3)-undefined  
ISO/IEC 14882:2011 2.14.5(13)-implementation  
<br/>

#### 参考
MISRA C++ 2008 2-13-5  
<br/>
<br/>

### <span id="ID_literal_misspelling">▌R11.6 字符串常量中不应存在拼写错误</span>

ID_literal_misspelling&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: literal warning

<hr/>

如果含有拼写错误的常量字符串对用户可见，也可以认为是产品的一种 bug，会对用户造成困扰，故应认真对待。  
  
示例：
```
void showMessage(int errCode) {
    if (errCode) {
        cout << "Error\n";
    } else {
        cout << "Successfull\n";  // Non-compliant
    }
}
```
例中“Successfull”存在拼写错误，应改为“Successful”。
<br/>
<br/>

#### 相关
ID_misspelling  
ID_macro_misspelling  
<br/>
<br/>

### <span id="ID_literal_confusingSuffix">▌R11.7 整数或浮点数常量的后缀应使用大写字母</span>

ID_literal_confusingSuffix&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: literal warning

<hr/>

整数或浮点数常量的后缀应使用大写字母，否则小写字母“l”极易与数字“1”混淆。  
  
示例：
```
long long a = 100ll;    // Non-compliant, misread as 10011
long double b = 100.l;  // Non-compliant, misread as 100.1
```
应改为：
```
long long a = 100LL;    // Compliant
long double b = 100.L;  // Compliant
```
后缀大小写混用的情况会使人更加困惑：
```
long long c = 100lL;            // Non-compliant, very bad
unsigned long long d = 100lLU;  // Non-compliant, very bad
```
其中，小写的 l 和大写的 L 混在了一起。  
  
应改为：
```
long long c = 100LL;            // Compliant
unsigned long long d = 100LLU;  // Compliant
```
<br/>
<br/>

#### 参考
C++ Core Guidelines NL.19  
MISRA C 2012 7.3  
MISRA C++ 2008 2-13-4  
<br/>
<br/>

### <span id="ID_literal_forbidOct">▌R11.8 禁用 8 进制常量</span>

ID_literal_forbidOct&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: literal suggestion

<hr/>

8 进制不像 10 进制那样符合人们的常规思维，也不像 2 进制或 16 进制那样便于展示数据的存储格式，而且 C/C\+\+ 中 8 进制表示法只是在数字前置 0，与十进制过于相似，易被误用。  
  
示例：
```
const int K_0 = 5592;
const int K_1 = 0631;  // Non-compliant
const int K_2 = 3817;
const int K_3 = 4257;
```
为了格式上的对齐，错误地在 10 进制数前写 0 是常见笔误，例中 k\_1 的实际值为 409。
<br/>
<br/>

#### 参考
MISRA C 2004 7.1  
MISRA C 2012 7.1  
MISRA C++ 2008 2-13-2  
<br/>
<br/>

### <span id="ID_literal_nonStandardSuffix">▌R11.9 整数或浮点数常量应使用标准后缀</span>

ID_literal_nonStandardSuffix&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: literal suggestion

<hr/>

整数常量后缀只应为 L、LL、UL、ULL，浮点数常量的后缀只应为 L、f 或 F，其他非标准后缀没有可移植性。  
  
示例：
```
unsigned int a = 100ui32;  // Non-compliant, not common between compilers
long long b = 100i64;      // Non-compliant
```
应改为：
```
unsigned int a = 100U;  // Compliant
long long b = 100LL;    // Compliant
```
<br/>
<br/>

#### 相关
ID_literal_confusingSuffix  
<br/>

#### 依据
ISO/IEC 14882:2003 2.14.2(2) 2.14.4(1)  
ISO/IEC 14882:2011 2.14.2(2) 2.14.4(1)  
ISO/IEC 14882:2017 5.13.2(2) 5.13.4(1)  
<br/>
<br/>

### <span id="ID_literal_oddConcat">▌R11.10 小心遗漏逗号导致的非预期字符串连接</span>

ID_literal_oddConcat&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: literal warning

<hr/>

注意可能导致非预期结果的字符串连接，尤其在初始化列表中，小心逗号被遗漏。  
  
示例：
```
string strs[] = {
    "123", "456", "789",
    "123", "456", "789"   // Rather suspicious, missing a comma?
    "123", "456", "789"
};
```
例中初始化列表第 2 行的 "789" 与第 3 行的 "123" 中间没有逗号，会连接成 "789123"，显然是不符合预期的，这种问题属于常见笔误。  
  
又如：
```
void foo(const char*);
void foo(const char*, const char*);

void bar() {
    foo("abc" "123");  // Rather suspicious, which ‘foo’ is right?
}
```
常量字符串连接的用途：  
 - 字符串过长不便于显示时可将字符串拆成多个子串分行书写  
 - 宏和字符串连接在一起完成一些更灵活的操作  
  
除此之外不应再将一个字符串拆成多个常量。
<br/>
<br/>
<br/>

### <span id="ID_literal_magicNumber">▌R11.11 不应存在 magic number</span>

ID_literal_magicNumber&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: literal suggestion

<hr/>

对于直接出现在代码中的字面数值（magic number），建议改用具有适当名称的常量或枚举项表示。  
  
示例：
```
for (int i = 0; i < 12345; i++) {  // Non-compliant, 12345 is a magic number
    ....
}
```
例中 12345 不能表示其具体的含义，而且当这种 magic number 散落在代码的各个角落时，不便于统一管理，造成维护上的困难。  
  
应改为具有名称的常量：
```
const int maxId = 12345;

for (int i = 0; i < maxId; i++) {  // Compliant
    ....
}
```
<br/>
<br/>

#### 配置
magicNumberDigitThreshold：数字常量的位数上限，超过则报出  
<br/>

#### 相关
ID_literal_magicString  
<br/>

#### 参考
C++ Core Guidelines ES.45  
<br/>
<br/>

### <span id="ID_literal_magicString">▌R11.12 不应存在 magic string</span>

ID_literal_magicString&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: literal suggestion

<hr/>

对于直接出现在代码中的字符串常量（magic string），建议改用具有适当名称的常量表示。  
  
示例：
```
void foo(const string& s) {
    if (s == "https://foo.net/bar") {  // Non-compliant
        bar("https://foo.net/bar");    // Non-compliant
    }
}
```
当这种常量字符串散落在代码的各个角落时，不便于统一管理，造成维护上的困难。  
  
应改为：
```
const char url[] = "https://foo.net/bar";  // Compliant

void foo(const string& s) {
    if (s == url) {
        bar(url);
    }
}
```
<br/>
<br/>

#### 相关
ID_literal_magicNumber  
<br/>

#### 参考
C++ Core Guidelines ES.45  
<br/>
<br/>

### <span id="ID_literal_multicharacter">▌R11.13 不应使用多字符常量</span>

ID_literal_multicharacter&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: literal suggestion

<hr/>

多字符常量形式上与字符串常量相似，但类型为整型，易被误用，而且不同编译器对这种常量的处理方式也有所不同，故建议禁用。  
  
示例：
```
void foo(int x) {
    if (x == 'tcp') {  // Non-compliant
        ....
    }
    else if (x == 'udp') {  // Non-compliant
        ....
    }
}
```
例中 'tcp'、'udp' 为多字符常量，应改用普通常量。  
  
本例在 C\+\+ 中也可使用 enum class 实现：
```
enum class PROT { tcp, udp };

void foo(PROT x) {
    if (x == PROT::tcp) {  // Compliant
        ....
    }
    else if (x == PROT::udp) {  // Compliant
        ....
    }
}
```
<br/>
<br/>

#### 相关
ID_literal_suspiciousChar  
<br/>

#### 依据
ISO/IEC 14882:2011 2.13.2(1)-implementation  
ISO/IEC 14882:2011 2.14.3(1)-implementation  
ISO/IEC 14882:2017 5.13.3(2)-implementation  
<br/>
<br/>

## <span id="cast">12. Cast</span>

### <span id="ID_narrowCast">▌R12.1 避免类型转换造成数据丢失</span>

ID_narrowCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

应检查类型转换的结果是否正确，避免数据丢失等问题。  
  
取值范围大的类型向取值范围小的类型转换需要考虑数据丢失问题，这种问题在浮点型转整型、整型转浮点型，以及浮点型转浮点型时会导致标准未定义的行为。  
  
示例：
```
void foo(int i, double d) {
    char c = i;              // Non-compliant
    float f = (float)d;      // Non-compliant, may cause undefined behavior
    ....
}
```
直接将 int 转为 char、double 转为 float 是不符合要求的，应判断源对象的值是否在目标对象的取值范围内。  
  
下面给出判断转换是否安全的简单示例：
```
template <class To, class From>
struct CastChecker
{
    static To cast(From x) {
        auto y = static_cast<To>(x);
        auto z = static_cast<From>(y);
        return x == z? y: throw DataLoss();
    }
};

template <class To, class From>
To checked_cast(From x) {
    return CastChecker<To, From>::cast(x);
}
```
函数 checked\_cast 委托类 CastChecker 将源类型转为目标类型，再将目标类型转回源类型，如果经两次转换得到的数据与源数据不符，说明转换存在数据丢失，抛出 DataLoss 异常，使用方法如下：
```
void foo(int i, double d) {
    char c = checked_cast<char>(i);     // Compliant
    float f = checked_cast<float>(d);   // Compliant
    ....
}
```
浮点型转换可能导致标准未定义的行为，所以应在转换之前判断取值范围，可通过特化 CastChecker 实现：
```
template <>
struct CastChecker<float, double>
{
    static bool check(double x) {
        return !isnan(x)
            && !isgreater(fabs(x), FLT_MAX)
            && !isless(fabs(x), FLT_MIN);
    }
    static float cast(double x) {
        return check(x)? static_cast<float>(x): throw DataLoss();
    }
};
```
这样当 double 对象的值超出 float 对象的取值范围时会抛出异常。另外，浮点型转整型时小数部分如何取舍、负数是否可以转为无符号数等问题均可以根据实际需求通过特化 CastChecker 来实现。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 6.3.1.4(1)-undefined  
ISO/IEC 9899:2011 6.3.1.5(1)-undefined  
ISO/IEC 14882:2011 4.8(1)-undefined  
ISO/IEC 14882:2011 4.9(1 2)-undefined  
<br/>

#### 参考
C++ Core Guidelines ES.46  
SEI CERT FLP34-C  
<br/>
<br/>

### <span id="ID_voidCast">▌R12.2 避免与 void* 相互转换</span>

ID_voidCast&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: cast suggestion

<hr/>

与 void\* 相互转换会打破类型限制，是不安全的类型转换。  
  
C 语言的某些传统接口会使用 void\*，可不受本规则限制，但在 C\+\+ 代码中应避免使用。  
  
示例：
```
struct A { .... };

void foo(void* v) {
    A* a = (A*)v;     // Non-compliant
    ....
}
```
例中参数 v 可以随意地接受非 A 对象的指针，进而导致标准未定义的行为，这种代码的正确性单方面依赖编写者，是不可靠的。
<br/>
<br/>

#### 相关
ID_forbidMemberVoidPtr  
ID_forbidFunctionVoidPtr  
<br/>

#### 参考
MISRA C 2012 11.5  
MISRA C++ 2008 5-2-8  
<br/>
<br/>

### <span id="ID_downCast">▌R12.3 避免向下类型转换</span>

ID_downCast&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: cast suggestion

<hr/>

当代码中出现了从基类到派生类的向下类型转换，以及可以造成数据损失的类型转换，意味着现有接口或流程不能满足需求，需要“特殊处理”，所以这种转换越少越好。  
  
示例：
```
class A {
    ....
};

class B: public A {
    ....
public:
    void bar();  // A new function
};

void foo(A* a) {
    ....
    if (auto* p = dynamic_cast<B*>(a)) {  // Bad
        p->bar();
    }
    ....
}
```
例中 foo 接口对 B 类型进行特殊处理，是不利于维护的，当这种特殊处理较多时，应利用虚函数、重载或模板等方法进行合理重构。
```
class A {
    ....
public:
    virtual void bar();
};

class B: public A {
    ....
public:
    void bar() override;
};

void foo(A* a) {
    ....
    if (a) {
        a->bar();  // Good
    }
    ....
}
```
<br/>
<br/>

#### 相关
ID_nonDynamicDownCast  
ID_narrowCast  
<br/>

#### 参考
C++ Core Guidelines ES.48  
<br/>
<br/>

### <span id="ID_ptrIntCast">▌R12.4 指针与整数不应相互转换</span>

ID_ptrIntCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

指针与整数相互转换容易造成地址不完整、寻址错误、降低可移植性等多种问题。  
  
指针与整数的转换由实现定义，整数的符号和取值范围可能与指针有冲突，错误的值转为指针也会导致标准未定义的行为。在当今大多数平台上，指针可以安全地转为 size\_t，但标准对此并未明确规定，审计工具不妨通过配置决定是否放过这种转换。C 标准规定指针可以转为 intptr\_t 或 uintptr\_t，但也声明了这些类型不是编译器必须实现的类型。  
  
示例：
```
void foo(int* p) {
    vector<int> v;
    v.emplace_back((int)p);  // Non-compliant
    ....
}
```
例中将指针转为 int 是不符合要求的，指针的值可能会超过 int 的范围。
<br/>
<br/>

#### 配置
allowPointerToSizeType：为 true 时可以放过指针与 size_t 的转换  
<br/>

#### 相关
ID_fixedAddrToPointer  
<br/>

#### 依据
ISO/IEC 9899:2011 6.3.2.3(5)-implementation  
ISO/IEC 14882:2011 5.2.10(4 5)-implementation  
ISO/IEC 9899:2011 7.20.1.4(1)  
<br/>

#### 参考
SEI CERT INT36-C  
MISRA C 2004 11.3  
MISRA C 2012 11.4  
MISRA C++ 2008 5-2-9  
<br/>
<br/>

### <span id="ID_qualifierCastedAway">▌R12.5 类型转换不应去掉 const、volatile 等属性</span>

ID_qualifierCastedAway&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

去掉 const、volatile 等属性会使相关机制失去意义，往往意味着设计上的缺陷，也会导致标准未定义的行为。  
  
示例：
```
const int N = 123;

void foo(const int& n) {
    const_cast<int&>(n) = 456;   // Non-compliant
}

void bar() {
    foo(N);   // Undefined behavior, modifies a const object
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 7.1.5.1(4 5)-undefined  
ISO/IEC 14882:2011 7.1.6.1(4)-undefined  
<br/>

#### 参考
C++ Core Guidelines Type.3  
MISRA C 2004 11.5  
MISRA C 2012 11.8  
MISRA C++ 2008 5-2-5  
<br/>
<br/>

### <span id="ID_castNoInheritance">▌R12.6 不应强制转换无继承关系的指针或引用</span>

ID_castNoInheritance&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

无继承关系的指针或引用之间没有逻辑关系，不应强制转换，否则意味着设计缺陷或逻辑错误。  
  
示例：
```
float f = 0;
int* p = (int*)&f;  // Non-compliant
(*p)++;             // Undefined behavior
```
基本类型之间没有继承关系，float\* 转为 int\* 属于逻辑错误，导致标准未定义的行为。  
  
有时为了考察对象内部结构，需要将对象指针转为 unsigned char\* 等类型，但这种转换打破了类型的边界，超越了数据处理的常规方法，易造成移植等方面的问题，审计工具不妨通过配置决定是否放过这种转换。  
  
又如：
```
class A { .... };
class B { .... };
class C: public A, public B { .... };

A* a = new C;

B* b0 = (B*)a;                    // Non-compliant
B* b1 = reinterpret_cast<B*>(a);  // Non-compliant

B* b2 = static_cast<B*>(a);       // Compliant, compile-time protected
B* b3 = dynamic_cast<B*>(a);      // Compliant, run-time protected
```
例中 A 与 B 没有继承关系，C 从 A 和 B 继承，指针 a 为 A 类型但实际指向 C 的实例，这种情况下将 a 直接强制转为 B 类型的指针将得到错误的结果，这种问题在实际代码中也很常见。  
  
本规则限制无继承关系的 C 风格类型转换以及 reinterpret\_cast 转换，不限制 static\_cast 和 dynamic\_cast 转换，示例中的 static\_cast 转换将得到编译错误从而锁定问题，如果 A 和 B 是多态类型，用 dynamic\_cast 会得到正确的结果。  
  
例外：
```
class V { .... };

class U {
    ....
public:
    operator V*();   // Conversion operator
};

U* u = new U;

V* v0 = (V*)u;                     // Compliant, but bad
V* v1 = reinterpret_cast<V*>(u);   // Still non-compliant
```
例中 U 和 V 是无继承关系的类，但 U 实现了向 V 的转换运算符，U 和 V 之间存在逻辑关系，这时的 C 风格类型转换可不受本规则限制，但不符合规则 ID\_forbidCStyleCast，这种情况仍然不能使用 reinterpret\_cast，参见 ID\_unsuitableReinterpretCast。
<br/>
<br/>

#### 配置
allowWeakerCast：为 true 可放过对象指针向 unsigned char* 的转换  
<br/>

#### 相关
ID_stricterAlignedCast  
ID_castNonPublicInheritance  
<br/>

#### 依据
ISO/IEC 9899:2011 6.5(7)-undefined  
ISO/IEC 14882:2011 3.10(10)-undefined  
ISO/IEC 14882:2011 4.1(1)-undefined  
<br/>

#### 参考
MISRA C 2012 11.3  
MISRA C++ 2008 5-2-7  
SEI CERT EXP39-C  
<br/>
<br/>

### <span id="ID_castNonPublicInheritance">▌R12.7 不应强制转换非公有继承关系的指针或引用</span>

ID_castNonPublicInheritance&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

公有继承表示派生类是基类的某种扩展，而非公有继承往往表示派生类是基类的某种“例外”，基类的方法不再适用于派生类的对象。  
  
示例：
```
class A { .... };
class B: private A { .... };

void bar(A* a);

void foo(B* b) {
    bar((A*)b);    // Non-compliant
}
```
例中 B 是对 A 的某种改造，如果再用 A 的方法去处理 B 的对象，显然是有问题的。
<br/>
<br/>

#### 相关
ID_castNoInheritance  
<br/>

#### 依据
ISO/IEC 14882:2011 4.10(3)  
<br/>
<br/>

### <span id="ID_nonPODBinaryCast">▌R12.8 非 POD 类的指针与基本类型的指针不应相互转换</span>

ID_nonPODBinaryCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

非“[POD](https://en.cppreference.com/w/cpp/named_req/PODType)”对象相关数据之间存在特殊的内在关系，尤其是虚函数表指针、虚基类表指针这种由实现定义的运行时数据，不应当作普通二进制数据处理，非 POD 对象的指针也不应与 void\*、unsigned char\* 等基本类型的指针相互转换。  
  
示例：
```
class A {
    ....

public:
    virtual ~A() = 0;

    void save() const {
        FILE* fp = fopen("dat", "wb");
        fwrite(this, sizeof(A), 1, fp);   // Non-compliant
        fclose(fp);
    }

    void load() {
        FILE* fp = fopen("dat", "rb");
        fread(this, sizeof(A), 1, fp);    // Non-compliant
        fclose(fp);
    }
};
```
例中 A 是多态类型，save 函数将对象写入文件，fwrite 的第一个参数 this 被隐式转为 void\*，不符合本规则要求。对象的虚表指针等数据一并被写入文件，但虚表指针是运行时数据不应被保存，load 函数从文件中读取对象便破坏了运行时数据。
<br/>
<br/>

#### 相关
ID_nonPODFilling  
<br/>

#### 参考
CWE-843  
<br/>
<br/>

### <span id="ID_charWCharCast">▌R12.9 不同的字符串类型之间不可直接转换</span>

ID_charWCharCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

char\* 和 wchar\_t\* 直接转换并不进行字符集转换，属于语言运用错误，char\*、wchar\_t\*、char16\_t\* 以及 char32\_t\* 之间均不可直接转换。  
  
本规则是 ID\_castNoInheritance 的特化。  
  
示例：
```
wchar_t* to_unicode(char* str) {
    return (wchar_t*)str;          // Remarkably brave, but totally wrong
}
```
示例代码显然是错误的，应改用 iconv、MultiByteToWideChar 等字符集编码转换函数。  
  
由于 unsigned char\* 一般针对二进制数据，unsigned char\* 与其他字符串类型之间的转换可不受本规则约束，但 char\* 不应作为二进制数据的类型，参见 ID\_plainBinaryChar。
<br/>
<br/>

#### 相关
ID_castNoInheritance  
ID_plainBinaryChar  
<br/>

#### 参考
CWE-704  
SEI CERT STR38-C  
<br/>
<br/>

### <span id="ID_stricterAlignedCast">▌R12.10 避免向对齐要求更严格的指针转换</span>

ID_stricterAlignedCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

访问不符合对齐要求的数据会导致标准未定义的行为。  
  
对象的存储地址与其占用空间的长度相关，如变量的地址往往是其长度的整数倍，这种机制称为“[内存对齐](https://en.wikipedia.org/wiki/Data_structure_alignment)”，可提高处理器访问数据的效率，如果对象的地址不符合这种要求，访问对象的效率就会降低，在某些平台上甚至会崩溃，详见“[unaligned access](https://en.wikipedia.org/wiki/Bus_error#Unaligned_access)”。  
  
每种对象类型都有一个“[对齐要求（alignment requirement）](https://en.cppreference.com/w/c/language/object#Alignment)”，一般来说占用空间越大的类型，对齐要求越严格，如 char 对象可以存储在任意地址，而 int 对象的地址只应是 sizeof(int) 的整数倍，所以解引用由 char 指针转换成的 int 指针很可能会造成“[unaligned access](https://en.wikipedia.org/wiki/Bus_error#Unaligned_access)”。  
  
示例：
```
void foo(unsigned char* p) {
    char c = *(char*)p;            // Compliant
    long n = *(long*)(p + 1);      // Non-compliant
    ....
}
```
二进制数据转向结构化数据时，这种问题较为常见，例中 p \+ 1 与 long 型变量的对齐要求不同，不应直接转换。  
  
应改为：
```
void foo(unsigned char* p) {
    char c = *(char*)p;            // Compliant
    long n;
    memcpy(&n, p + 1, sizeof(n));  // Compliant
    ....
}
```
用 memcpy 等函数将低对齐要求的数据复制到高对齐要求的对象中，是避免相关问题的通用模式。
<br/>
<br/>

#### 相关
ID_castNoInheritance  
<br/>

#### 依据
ISO/IEC 9899:2011 6.3.2.3(7)-undefined  
<br/>

#### 参考
SEI CERT EXP36-C  
<br/>
<br/>

### <span id="ID_arrayPointerCast">▌R12.11 避免转换指向数组的指针</span>

ID_arrayPointerCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

指针的逻辑大小与类型相关，转换指向数组的指针极易造成元素定位、空间计算等方面的错误。  
  
示例：
```
struct A { int x; };
struct B: A { int y; };

void foo(A* p, int n) {
    for (int i = 0; i < n; i++) {
        p[i].x = 1; 
    }
}

void bar() {
    B arr[100];
    foo(arr, 100);   // Non-compliant
    ....
}
```
例中派生类对象的数组被转换成了基类指针，foo 函数中对数组元素的定位将是错误的。  
  
另外，在回收动态分配的数组时，如果指针的类型与实际元素的类型不一致，会导致标准未定义的行为：
```
A* p = new B[100];   // Non-compliant
delete[] p;          // Undefined behavior
```
这是一个危险的问题，本规则针对所有数组相关的隐式和显式类型转换。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 6.5.6(8)  
ISO/IEC 14882:2011 5.3.5(3)-undefined  
<br/>

#### 参考
C++ Core Guidelines C.152  
<br/>
<br/>

### <span id="ID_functionPointerCast">▌R12.12 避免转换函数指针</span>

ID_functionPointerCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

函数指针和不兼容的类型转换会导致标准未定义的行为。  
  
示例：
```
void foo();
typedef void (*fnp_t)(int);

void* p0 = (void*)&foo;    // Non-compliant
fnp_t p1 = (fnp_t)&foo;    // Non-compliant

p1(123);                   // Undefined behavior
```
例外：
```
fnp_t p = NULL;            // Compliant

(void)p;                   // Let it go
p = (fnp_t)dlsym(h, "f");  // Let it go
```
对函数指针进行 (void) 转换，或 dlsym、GetProcAddress 等动态导入函数的系统接口可不受本规则约束。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 6.3.2.3(6 7 8)-undefined  
ISO/IEC 9899:2011 6.5.2.2(9)-undefined  
ISO/IEC 14882:2011 5.2.10(6)-undefined  
ISO/IEC 14882:2011 5.2.10(8)-implementation  
<br/>

#### 参考
MISRA C 2004 11.1  
MISRA C 2012 11.1  
MISRA C++ 2008 5-2-6  
<br/>
<br/>

### <span id="ID_nonDynamicDownCast">▌R12.13 向下类型转换应使用 dynamic_cast</span>

ID_nonDynamicDownCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

向下类型转换如果不用 dynamic\_cast 难以保证安全性。  
  
示例：
```
class A { .... };
class B: public A { .... };

void foo(A* a) {
    bar((B*)a);                 // Non-compliant
    baz(dynamic_cast<B*>(a));   // Compliant
}
```
如果参数 a 实际指向的不是 B 类的对象，(B\*)a 将得到一个无法判断对错的值，而 dynamic\_cast<B\*>(a) 会得到一个空值，便于进一步处理。  
  
注意，虚基类指针只能通过 dynamic\_cast 转换为派生类指针，否则导致标准未定义的行为：
```
struct A { .... };
struct B: virtual A { .... };
struct C: virtual A { .... };
struct D: B, C { .... };

void foo(A* a) {
    D* d0 = (D*)a;                 // Undefined behavior
    D* d1 = dynamic_cast<D*>(a);   // Right
    ....
}
```
应尽量减少向下类型转换，参见 ID\_downCast。
<br/>
<br/>

#### 相关
ID_downCast  
<br/>

#### 依据
ISO/IEC 14882:2011 5.2.7  
ISO/IEC 14882:2003 5.2.9(5 8)-undefined  
ISO/IEC 14882:2011 5.2.9(11 12)-undefined  
<br/>

#### 参考
MISRA C++ 2008 5-2-2  
C++ Core Guidelines Type.2  
<br/>
<br/>

### <span id="ID_oddNewCast">▌R12.14 对 new 表达式不应进行类型转换</span>

ID_oddNewCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

new 表达式本身是类型明确的，转换 new 表达式的类型也容易造成分配、访问或回收相关的错误。  
  
示例：
```
int* p = (int*)new char[123];  // Non-compliant
....
delete[] p;  // What will happen?
```
例中 char 数组被转为 int 数组，由于元素个数不兼容也会导致内存访问与回收的错误。
<br/>
<br/>

#### 相关
ID_arrayPointerCast  
<br/>
<br/>

### <span id="ID_redundantCast">▌R12.15 不应存在多余的类型转换</span>

ID_redundantCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

转换前后的类型完全相同是没有意义的，很可能意味着某种笔误。  
  
示例：
```
float foo(int a) {
    return (int)a;  // Non-compliant
}
```
应改为：
```
float foo(int a) {
    return (float)a;  // Compliant
}
```
<br/>
<br/>

#### 参考
CWE-704  
<br/>
<br/>

### <span id="ID_unsuitableReinterpretCast">▌R12.16 可用其他方式完成的转换不应使用 reinterpret_cast</span>

ID_unsuitableReinterpretCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

reinterpret\_cast 将地址强行按另一种类型解释，不考虑转换需要的逻辑，可用 static\_cast、dynamic\_cast 完成的转换不应使用 reinterpret\_cast。  
  
示例：
```
struct A { int a = 1; };
struct B { int b = 2; };
struct C: A, B {};

int main() {
    C c;
    cout << static_cast<B*>(&c)->b << ' ';
    cout << reinterpret_cast<B*>(&c)->b << '\n';  // Non-compliant, what is output?
}
```
输出 2 1，如果想将派生类对象的地址 &c 转为基类指针，应使用 static\_cast 进行正确的偏移转换，使用 reinterpret\_cast 不会进行偏移转换，得到的成员 b 不是真实的成员 b。
<br/>
<br/>

#### 相关
ID_voidCast  
<br/>

#### 依据
ISO/IEC 14882:2011 5.2.10(7)-unspecified  
<br/>

#### 参考
C++ Core Guidelines Type.1  
<br/>
<br/>

### <span id="ID_forbidReinterpretCast">▌R12.17 合理使用 reinterpret_cast</span>

ID_forbidReinterpretCast&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: cast suggestion

<hr/>

语言对 reinterpret\_cast 的定位不是为了安全性，而是为了灵活性，可以用其他方式实现的功能不应使用 reinterpret\_cast，如果必须使用需提供明确的文档说明。  
  
示例：
```
class MyData { .... };

void foo(const char* path) {
    unsigned char* p = read_from_file(path);
    MyData* dat = reinterpret_cast<MyData*>(p);  // Bad
    ....
}
```
设例中 read\_from\_file 读取并返回文件的二进制数据，用 reinterpret\_cast 将二进制数据直接转为对象是不安全的，妥善的做法是根据文件数据将对象的成员逐一构造出来，可参见 ID\_stricterAlignedCast 介绍的方法，这样也可以及时发现并处理问题。  
  
又如：
```
void* external_interface();  // If it's out of control ...

void foo() {
    auto* data = external_interface();
    auto* mydata = reinterpret_cast<MyData*>(data);  // Attention
    ....
}
```
例中 external\_interface 是项目外部的一个接口，它的实现方式完全不受控制，返回类型也不可见，甚至需要某种“hacking”才能使用这个接口，这已经不属于正常的开发范围了，可以用 reinterpret\_cast 强调这是一种非正常的转换，但需注明这种情况产生的原因，以及是否有改进的余地等信息。
<br/>
<br/>

#### 相关
ID_stricterAlignedCast  
<br/>

#### 参考
CWE-843  
C++ Core Guidelines Pro.safety  
<br/>
<br/>

### <span id="ID_forbidCStyleCast">▌R12.18 在 C++ 代码中禁用 C 风格类型转换</span>

ID_forbidCStyleCast&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: cast suggestion

<hr/>

C 语言的类型观念弱于 C\+\+，易造成逻辑错误或数据丢失，应尽量避免类型转换，或使用 static\_cast、dynamic\_cast 等方法。  
  
示例：
```
class A { .... };
class B { .... };

void foo(A* a) {
    B* b = (B*)a;  // Non-compliant, an error value with no logical meaning
    ....
}

void bar(A* a) {
    B* b = dynamic_cast<B*>(a);  // Compliant, prevent errors at compile time
    ....
}
```
例中 A 和 B 是两种不相关的类型，用 C 语言的转换方式是可以转换成功的，但并没有逻辑意义，在 C\+\+ 语言中应使用 static\_cast 或 dynamic\_cast 等方法在编译时或运行时保障转换的有效性。
<br/>
<br/>

#### 参考
MISRA C++ 2008 5-2-4  
C++ Core Guidelines ES.49  
<br/>
<br/>

## <span id="buffer">13. Buffer</span>

### <span id="ID_bufferOverflow">▌R13.1 避免缓冲区溢出</span>

ID_bufferOverflow&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: buffer warning

<hr/>

“缓冲区（buffer）”的本意是指内存等高速设备上的区域，程序在这种区域内接收或处理数据，之后再一并输出到网络或磁盘等低速环境，起到提高效率的作用，故称缓冲区。连续的内存区域均可称为缓冲区，在 C/C\+\+ 语言中对应数组等结构。  
  
缓冲区之外可能是程序的其他数据，也可能是函数返回地址、资源分配信息等重要数据，对缓冲区的越界读写往往意味着逻辑错误，而且会使程序遭到破坏。  
  
示例：
```
void foo(const char* s) {
    char buf[100];
    strcpy(buf, s);   // Non-compliant
    ....
}

int main() {
    foo(userInput());
}
```
例中 userInput 函数返回用户输入的字符串，其长度不确定，而缓冲区 buf 的长度为 100 字节，如果用户输入超过这个长度就会使程序遭到破坏，这种问题称为“[缓冲区溢出（buffer overflow）](https://en.wikipedia.org/wiki/Buffer_overflow)”，也是程序遭受攻击的常见原因。  
  
缓冲区溢出可造成严重危害，如：  
 - 破坏堆栈或段结构，扰乱程序执行  
 - 改写关键信息，篡改程序行为  
 - 注入并运行恶意代码  
 - 攻击高权限进程获取非法权限  
  
所以将读写限定在缓冲区边界之内是十分重要的，示例代码应改为：
```
void foo(const char* s) {
    char buf[100] = "";
    strncpy(buf, s, sizeof(buf) - 1);   // Compliant
    ....
}
```
strncpy 与 strcpy 不同，当源字符串长度超过指定限制时会结束复制，但要注意 strncpy 对空字符的处理。  
  
对数组下标越界的问题，本规则特化为 ID\_arrayIndexOverflow。
<br/>
<br/>

#### 相关
ID_arrayIndexOverflow  
ID_unsafeStringFunction  
<br/>

#### 参考
CWE-119  
CWE-131  
CWE-788  
<br/>
<br/>

### <span id="ID_arrayIndexOverflow">▌R13.2 数组下标不可越界</span>

ID_arrayIndexOverflow&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: buffer error

<hr/>

数组下标不在数组大小范围之内，意味着逻辑错误，会导致标准未定义的行为。  
  
通过 \[ \]、\+、\-、\+\+、\-\- 等运算符完成的数组元素定位均受本规则约束。  
  
示例：
```
int foo() {
    int a[10] = {};
    ....
    return a[10];  // Non-compliant, overflow
}
```
例中数组声明的大小是 10，其下标有效范围是 0 至 9，10 不能再作为下标，这也是常见笔误。  
  
如果数组下标可受用户控制，更应该判断是否在有效范围内，如：
```
const char* fruits[] = {
    "apple", "banana", "grape"
};

const char* bar() {
    return fruits[userInput()];  // Non-compliant
}
```
设 userInput 返回用户输入的整数，将其直接作为数组下标是不安全的。  
  
应改为：
```
const char* bar() {
    int i = userInput();
    if (i >= 0 && i < 3) {
        return fruits[i];    // Compliant
    }
    return nullptr;
}
```
<br/>
<br/>

#### 相关
ID_bufferOverflow  
<br/>

#### 依据
ISO/IEC 14882:2011 5.7(5)-undefined  
<br/>

#### 参考
CWE-119  
CWE-125  
CWE-131  
CWE-787  
CWE-788  
C++ Core Guidelines ES.103  
SEI CERT ARR30-C  
<br/>
<br/>

### <span id="ID_insufficientBuffer">▌R13.3 为缓冲区分配足够的空间</span>

ID_insufficientBuffer&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: buffer warning

<hr/>

为缓冲区分配足够的空间，避免溢出等问题。  
  
示例：
```
void foo() {
    int* p = (int*)malloc(123);  // Non-compliant
    ....
}
```
例中 foo 函数为 int 型数组分配了 123 个字节的空间，而 123 不能被 sizeof(int) 整除，最后一个元素会越界。  
  
应改为：
```
void foo() {
    int* p = (int*)malloc(123 * sizeof(int));  // Compliant
    ....
}
```
需要注意数组的逻辑大小和字节大小的区别，不可漏掉 sizeof 因子。  
  
又如：
```
void bar(const char* s) {
    char* p = (char*)malloc(strlen(s));  // May be ‘strlen(s) + 1’?
    ....
}
```
字符串以空字符结尾，在分配字符串空间时不可漏掉空字符的空间。
<br/>
<br/>

#### 相关
ID_bufferOverflow  
<br/>

#### 参考
CWE-131  
CWE-135  
<br/>
<br/>

### <span id="ID_nonPODFilling">▌R13.4 memset 等函数不应作用于非 POD 对象</span>

ID_nonPODFilling&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: buffer error

<hr/>

memset、memcpy、memmove 等具有填充功能的函数不应作用于非“[POD](https://en.cppreference.com/w/cpp/named_req/PODType)”对象，否则会破坏其数据的内在关系。  
  
本规则是 ID\_nonPODBinaryCast 的特化。  
  
示例：
```
class A {
    ....
public:
    virtual ~A();
};

void foo(A& a) {
    memset(&a, 0, sizeof(a));   // Non-compliant, the ‘vfptr’ is corrupted
}
```
例中 memset 填充非 POD 对象，其虚函数表指针会被破坏，造成严重的运行时错误。
<br/>
<br/>

#### 相关
ID_nonPODBinaryCast  
<br/>

#### 参考
CWE-463  
C++ Core Guidelines SL.con.4  
C++ Core Guidelines C.90  
<br/>
<br/>

### <span id="ID_badLength">▌R13.5 memset 等函数长度相关的参数不应有误</span>

ID_badLength&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: buffer error

<hr/>

对于 memset、memcpy、memmove、memcmp 及同类函数，表示长度的参数不应存在常见笔误。  
  
示例：
```
char buf[1024];
memset(buf, 1024, 0);  // Non-compliant
```
长度和填充值参数被写反是常见笔误，应改为：
```
memset(buf, 0, 1024);  // Compliant
```
又如：
```
int arr[1024];
memset(buf, 0, 1024);  // Rather suspicious
memset(buf, 1, 123);   // Non-compliant
```
memset 等函数的长度单位为字节，不应遗漏 sizeof 因子，应改为：
```
memset(buf, 0, 1024 * sizeof(int));  // Compliant
memset(buf, 1, 123 * sizeof(int));   // Compliant
```
又如（设 p 为对象指针）：
```
memset(p, 0, sizeof(p));   // Non-compliant
```
应改为：
```
memset(p, 0, sizeof(*p));  // Compliant
```
sizeof 作用于指针并不能获取到对象的大小，可参见 ID\_sizeof\_pointer 的进一步讨论。  
  
又如（设 a、b 是对象）：
```
memset(&a, 0, sizeof(&a));   // Non-compliant
memcpy(&a, &b, sizeof(&a));  // Non-compliant
```
应改为：
```
memset(&a, 0, sizeof(a));    // Compliant
memcpy(&a, &b, sizeof(a));   // Compliant
```
这是常见的复制粘贴错误。  
  
又如：
```
if (memcmp(&a, &b, sizeof(a) != 0)) {  // Non-compliant
    ....
}
```
长度参数不应为比较表达式，应改为：
```
if (memcmp(&a, &b, sizeof(a)) != 0) {  // Compliant
    ....
}
```
括号的错误嵌套也是常见的笔误。
<br/>
<br/>

#### 参考
CWE-130  
CWE-805  
<br/>
<br/>

### <span id="ID_valueOverflow">▌R13.6 memset 等函数填充值相关的参数不应有误</span>

ID_valueOverflow&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: buffer error

<hr/>

memset、memset\_s 等函数的填充值参数会被转为 unsigned char 型，所以其值不应超出一个字节的范围。  
  
示例：
```
char buf[32];
memset(buf, 1024, 32);  // Non-compliant
```
例中填充值为 1024，超出了一个字节的范围，在实际代码中也可能是长度参数与填充值参数被写反了。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 7.24.6.1(2)  
ISO/IEC 9899:2011 K.3.7.4.1(4)  
<br/>

#### 参考
CWE-130  
<br/>
<br/>

## <span id="pointer">14. Pointer</span>

### <span id="ID_nullDerefInScp">▌R14.1 避免空指针解引用</span>

ID_nullDerefInScp&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: pointer error

<hr/>

通过指针的值访问相应地址中的数据称为“解引用（dereference）”，空指针是没有指向任何数据的指针，空指针解引用是一种逻辑错误，会导致标准未定义的行为。  
  
示例：
```
int foo(int i) {
    int* p = NULL;
    if (cond) {
        p = &i;
    }
    return *p;    // Non-compliant
}
```
例中指针 p 为空的状态可以到达解引用处，往往会引发“[段错误](https://en.wikipedia.org/wiki/Segmentation_fault)”而导致崩溃。  
  
崩溃会给用户不好的体验，而且要注意如果崩溃可由外部输入引起，会被攻击者利用从而迫使程序无法正常工作，具有高可靠性要求的服务类程序更应该注意这一点，可参见“[拒绝服务攻击](https://en.wikipedia.org/wiki/Denial-of-service_attack)”的进一步说明。对于客户端程序，也要防止攻击者对崩溃产生的“[core dump](https://en.wikipedia.org/wiki/Core_dump)”进行恶意调试，避免泄露敏感数据，总之程序的健壮性与安全性是紧密相关的。  
  
例外：
```
struct T {
    int foo() { return 0; }
    static int bar() { return 1; }
};

T* p = nullptr;
auto b = p->bar();   // Compliant, but bad, use ‘T::bar()’ instead
auto c = p->foo();   // Non-compliant, even if it may not crash
```
在 C\+\+ 语言中通过指针访问静态成员不算作解引用，可不受本规则约束，但这种风格易引起维护者的疑虑而增加维护成本。  
  
另外，调用非静态成员函数意味着访问对象的数据，即使成员函数没有实际地访问成员数据，也不应通过空指针调用非静态成员函数，否则仍属于逻辑错误，而且如果调用的是虚函数或虚基类的成员函数仍会造成崩溃。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 6.5.3.2(4)-undefined  
ISO/IEC 14882:2011 8.3.2(5)-undefined  
<br/>

#### 参考
CWE-476  
C++ Core Guidelines ES.65  
<br/>
<br/>

### <span id="ID_nullDerefInExp">▌R14.2 注意逻辑表达式内的空指针解引用</span>

ID_nullDerefInExp&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: pointer error

<hr/>

在逻辑表达式中，需注意逻辑关系以及运算符优先级，不可出现空指针解引用等问题。  
  
示例（设 foo、bar 是指针 p 所指对象的非静态成员函数）：
```
p || p->foo();  // Non-compliant
```
当 p 为空时执行“||”的右子表达式，恰好使空指针被解引用。  

```
p && p->foo() || p->bar();  // Non-compliant
```
“&&”的优先级高于“||”，由“||”的左子表达式可知 p 可能为空，而右子表达式却没有限制，导致空指针被解引用。  

```
p->foo() && p;  // Non-compliant
```
这是颠倒了对指针的判断和解引用次序，属于语言运用错误。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 6.5.3.2(4)-undefined  
ISO/IEC 14882:2011 8.3.2(5)-undefined  
<br/>

#### 参考
CWE-476  
CWE-783  
C++ Core Guidelines ES.65  
<br/>
<br/>

### <span id="ID_danglingDeref">▌R14.3 不可解引用已被释放的指针</span>

ID_danglingDeref&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: pointer error

<hr/>

已被释放的指针指向无效的内存空间，再次对其解引用会造成严重错误，导致标准未定义的行为。  
  
示例：
```
int foo() {
    int* p = new int[100];
    if (cond) {
        ....
        delete[] p;
    }
    return p[0];  // Non-compliant, ‘p’ may be deallocated
}
```
本来指针 p 指向有效的内存空间，但由于某种原因相关内存被释放，p 的值不变但已无效，这种情况被形象地称为“指针悬挂”， 未经初始化的指针和这种“被悬挂”的指针统称“野指针”，均指向无效地址不可被解引用。  
  
应避免内层作用域中的地址向外层传递，如：
```
int foo(int i) {
    int* p = &i;
    if (cond) {
        int j = 0;
        ....
        p = &j;   // Bad practice
    }
    return *p;    // Non-compliant, ‘p’ may be deallocated
}
```
例中局部变量 j 的地址被传给了外层作域中的 p，j 的生命周期结束后，p 为野指针。  
  
另外，在 C\+\+ 语言中，应避免持有可被自动销毁的对象地址，如容器中对象的地址、智能指针所指对象的地址等。
```
int bar(vector<int>& v) {
    int* p = &v.front();         // Bad practice
    v.push_back(some_value);
    return *p;                   // ‘p’ may be invalid
}
```
例中指针 p 记录了 vector 容器中对象的地址，根据 vector 容器持有对象的策略，随着元素的增加原有对象的地址可能不再有效。  
  
又如：
```
int baz() {
    auto u = make_unique<int>();
    auto p = u.get();            // Bad practice
    u = make_unique<int>();
    return *p;                   // ‘p’ is invalid
}
```
例中指针 p 记录了 unique\_ptr 所指对象的地址，当 unique\_ptr 指向新的对象时，原对象的地址不再有效。
<br/>
<br/>

#### 相关
ID_illAccess  
ID_localAddressFlowOut  
ID_illLifetime  
<br/>

#### 依据
ISO/IEC 9899:1999 6.5.3.2(4)-undefined  
ISO/IEC 9899:2011 6.5.3.2(4)-undefined  
<br/>

#### 参考
CWE-822  
CWE-825  
C++ Core Guidelines ES.65  
SEI CERT EXP54-CPP  
<br/>
<br/>

### <span id="ID_invalidNullCheck">▌R14.4 避免无效的空指针检查</span>

ID_invalidNullCheck&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: pointer warning

<hr/>

当指针的值一定不为空时，再对其进行检查是没有意义的，往往意味着逻辑错误。  
  
示例：
```
if (int* p = new int[100]) {   // Non-compliant
    ....
} else {   // Invalid
    ....
}
```
标准规定默认 new 运算符的返回值不会为空，如果分配失败则抛出异常，所以这种检查和相关错误处理是无效的。  
  
应改为：
```
if (int* p = new(std::nothrow) int[100]) {   // Compliant
    ....
} else {   // OK
    ....
}
```
又如：
```
if (p) {        // Meaningless
    delete p;
}
```
对于可接受空指针的接口，不必总在调用前判断指针是否为空，否则会使代码变得繁琐。delete 关键字或 free 函数可以作用于空指针，调用之前的检查是没有意义的。
<br/>
<br/>

#### 相关
ID_repeatedNullCheck  
<br/>

#### 依据
ISO/IEC 9899:2011 18.6  
<br/>
<br/>

### <span id="ID_repeatedNullCheck">▌R14.5 不应重复检查指针是否为空</span>

ID_repeatedNullCheck&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: pointer warning

<hr/>

重复的空指针检查是不必要的，使代码显得繁琐，且干扰编译器优化。  
  
示例：
```
void foo(int* p) {
    if (!p) {
        return;
    }
    if (p) {   // Non-compliant, ‘p’ is not nullptr
        ....
    } else {
        ....   // Unreachable
    }
}
```
<br/>
<br/>

#### 相关
ID_invalidNullCheck  
<br/>
<br/>

### <span id="ID_fixedAddrToPointer">▌R14.6 不应将非零常量值赋值给指针</span>

ID_fixedAddrToPointer&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: pointer warning

<hr/>

固定地址是不可移植的，且存在安全隐患。  
  
示例：
```
const void* badAddr = (void*)0xffffffff;  // Non-compliant
```
示例代码的本意是声明一个表示无效地址的值，但在 64 位系统中这个地址可能是有效的。  
  
又如：
```
typedef int (*fp_t)(int);
fp_t fp = (fp_t)0x1234abcd;  // Non-compliant
int res = (*fp)(123);        // Unsafe
```
示例代码假设在特定地址可以找到特定的函数，将该地址赋给一个指针并调用，这种假设是不可移植的，也可能本身就是错误的，会导致崩溃，而且函数地址往往也属于敏感信息，不应被写入代码，否则一旦泄露，攻击者可以更改预期地址上的数据，导致恶意代码被执行。  
  
某些框架或系统会以 \-1 表示无效地址，但不具备通用性，审计工具不妨通过配置决定是否放过。
<br/>
<br/>

#### 配置
allowMinusOneAsPointerValue：为 true 时可以放过 -1 作为指针值的情况  
<br/>

#### 相关
ID_addressExposure  
ID_plainSensitiveInfo  
ID_ptrIntCast  
<br/>

#### 参考
CWE-587  
<br/>
<br/>

### <span id="ID_zeroAsPtrValue">▌R14.7 不应使用常量 0 表示空指针</span>

ID_zeroAsPtrValue&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: pointer suggestion

<hr/>

在 C 代码中应使用 NULL 表示空指针，在 C\+\+ 代码中应使用 nullptr 表示空指针。  
  
标准允许使用 0、'\\0'、false、1 \- 1 等值为 0 的常量表达式表示空指针，但易出现类型相关的错误，且不利于阅读。  
  
示例：
```
int* p;
void foo(int*);

p = 0;   // Non-compliant
foo(0);  // Non-compliant
```
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 6.3.2.3(3)  
ISO/IEC 14882:2011 4.10(1)  
<br/>

#### 参考
MISRA C++ 2008 4-10-2  
C++ Core Guidelines ES.47  
<br/>
<br/>

### <span id="ID_oddPtrBoolAssignment">▌R14.8 不应使用 false 对指针赋值</span>

ID_oddPtrBoolAssignment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: pointer warning

<hr/>

用 false 对指针赋值是非常怪异的，会对代码阅读造成误导，而且也可能是书写错误。  
  
示例：
```
void set_false(bool* p) {
    p = false;              // Non-compliant, should be ‘*p = false’
}
```
<br/>
<br/>

#### 参考
CWE-351  
<br/>
<br/>

### <span id="ID_oddPtrCharAssignment">▌R14.9 不应使用 '\0' 等字符常量对指针赋值</span>

ID_oddPtrCharAssignment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: pointer warning

<hr/>

用 '\\0'、L'\\0'、u'\\0'、U'\\0' 等字符常量对指针赋值是非常怪异的，往往意味错误。  
  
示例：
```
void set_terminate(char* p) {
    p = '\0';                   // Non-compliant
}
```
应改为：
```
void set_terminate(char* p) {
    *p = '\0';                  // Compliant
}
```
<br/>
<br/>

#### 参考
CWE-351  
<br/>
<br/>

### <span id="ID_oddPtrBoolComparison">▌R14.10 指针不应与 false 比较大小</span>

ID_oddPtrBoolComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: pointer warning

<hr/>

指针与 false 比较大小是非常怪异的，往往是某种笔误。  
  
示例（设 p 为指针）：
```
p == false  // Non-compliant
p != false  // Non-compliant
```
如果判断指针是否为空，只应将指针与 NULL 或 nullptr 比较，其他常量均不合规。
<br/>
<br/>

#### 参考
CWE-1025  
<br/>
<br/>

### <span id="ID_oddPtrCharComparison">▌R14.11 指针不应与 '\0' 等字符常量比较大小</span>

ID_oddPtrCharComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: pointer warning

<hr/>

指针与 '\\0'、L'\\0'、u'\\0'、U'\\0' 等字符常量比较大小是非常怪异的，往往是某种笔误。  
  
示例（设 p 为指针）：
```
if (p == '\0') {    // Non-compliant
    ....
}
```
这种情况很有可能是漏写了 \* 号：
```
if (*p == '\0') {   // Non-compliant
    ....
}
```
否则只应将指针与 NULL 或 nullptr 比较。
<br/>
<br/>

#### 参考
CWE-1025  
<br/>
<br/>

### <span id="ID_oddPtrZeroComparison">▌R14.12 不应判断指针大于、大于等于、小于、小于等于 0</span>

ID_oddPtrZeroComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: pointer warning

<hr/>

指针的值是地址的编号，并没有大小的语义，指针可以与指针比较从而确定某种前后关系，但指针与整数比较大小则是没有意义的，尤其是与 0 的比较，往往意味着错误。  
  
示例（设 p 为指针）：
```
p < 0    // Non-compliant, always false
p >= 0   // Non-compliant, always true
p > 0    // Non-compliant, use p != nullptr instead
p <= 0   // Non-compliant, use p == nullptr instead
```
另外，指针与 NULL 或 nullptr 只应使用 == 或 != 进行比较，其他比较运算符均不合规：
```
p <= NULL      // Non-compliant
p > nullptr    // Non-compliant
```
应改为：
```
p == NULL      // Compliant
p != nullptr   // Compliant
```
<br/>
<br/>

#### 参考
CWE-1025  
<br/>
<br/>

### <span id="ID_this_zeroComparison">▌R14.13 不应判断 this 指针是否为空</span>

ID_this_zeroComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: pointer warning

<hr/>

正常情况下 this 指针不会为空，而且判断 this 指针是否为空会影响编译器对 this 指针的优化，造成难以预料的后果。  
  
在某些环境中通过空指针调用非静态成员函数时，this 指针可能为空，但这并不符合标准。值得强调的是，任何情况下都不应逃避解引用空指针造成的问题。  
  
示例：
```
struct A {
    int x = 0;
    int getX() {
        return this? x: 0;  // Non-compliant
    }
};

A* p = foo();
// Suppose an error has occurred and a null pointer is returned
cout << p->getX() << '\n';
```
假设 foo 函数不应返回空指针，而某个错误导致其返回了空指针，程序本应崩溃，而 getX 函数却逃避了崩溃，这非但不能真正地解决问题，反而使问题难以定位，使程序难以调试，大大降低了可维护性。
<br/>
<br/>

#### 参考
CWE-1025  
<br/>
<br/>

### <span id="ID_this_deleteInDestructor">▌R14.14 析构函数中不可使用 delete this</span>

ID_this_deleteInDestructor&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: pointer error

<hr/>

析构函数中不可使用 delete this，否则造成无限递归。  
  
示例：
```
struct A {
    ~A() {
        delete this;  // Non-compliant
    }
};
```
<br/>
<br/>

#### 参考
CWE-674  
<br/>
<br/>

### <span id="ID_this_forbidDeleteThis">▌R14.15 禁用 delete this</span>

ID_this_forbidDeleteThis&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: pointer suggestion

<hr/>

因为正确使用 delete this 限制条件太多，稍不留意就会为 bug 埋下伏笔，所以禁用这种方式是明智的选择。  
  
要想正确使用 delete this，必须保证：  
 - 对象是用 new 创建的，但不能是 new\[\] 或 replacement new  
 - 使用 delete this 之后不能再访问相关非静态成员及成员函数  
 - 不能在析构函数中使用 delete this  
  
示例：
```
class A {
    ....
public:
    void foo() {
        delete this;   // Non-compliant
    }
};

auto* p = new A;
p->foo();              // Looks innocent

p = new A[10];
p->foo();              // Memory is still leaking
```
如果一定要使用 delete this，类的析构函数应设为私有，这样可以阻止类的对象在栈上定义而引发更大的混乱，并且要确保执行 delete this 后 this 指针再也不会被解引用，而且不能用 new\[\] 创建，否则仍然存在内存泄漏问题。  
  
所以对框架以及语言工具之外的业务类或算法类代码建议禁用 delete this。
<br/>
<br/>
<br/>

### <span id="ID_nullDerefDynamicCast">▌R14.16 判断 dynamic_cast 转换是否成功</span>

ID_nullDerefDynamicCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: pointer warning

<hr/>

dynamic\_cast 转换指针失败会返回空指针，转换引用失败会抛出异常，如果不作判断则失去了使用 dynamic\_cast 的意义。  
  
示例：
```
void foo(A* a) {
    dynamic_cast<B*>(a)->foo();  // Non-compliant
}
```
应改为：
```
void foo(A* a) {
    if (auto* b = dynamic_cast<B*>(a)) {  // Compliant
        b->foo();
    }
}
```
使用 dynamic\_cast 需要一定开销，如果不对其结果作判断，还不如使用 static\_cast 等转换，但本规则集合不建议采用非 dynamic\_cast 之外的向下类型转换，参见 ID\_nonDynamicDownCast。
<br/>
<br/>

#### 相关
ID_nonDynamicDownCast  
<br/>

#### 依据
ISO/IEC 14882:2011 5.2.7(9)  
<br/>

#### 参考
CWE-476  
C++ Core Guidelines C.148  
<br/>
<br/>

### <span id="ID_missingResetNull">▌R14.17 指针在释放后应置空</span>

ID_missingResetNull&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: pointer suggestion

<hr/>

内存空间被回收后相关指针不再有效，这时应将指针设为空指针，可避免重复释放等问题，如果后续对指针有错误的访问，也可使问题立即显现出来，便于修正。  
  
示例：
```
class T {
    int* p = new int[123];
    ....

public:
   ~T() {
        dealloc();
    }

    void dealloc() {
        delete[] p;
        p = nullptr;    // Good
    }
};
```
例中 dealloc 函数释放指针 p 后将其置为空指针，如果 dealloc 函数被外界反复调用也没有问题。本规则是对“指针悬挂”等问题的有效措施，参见 ID\_danglingDeref。
<br/>
<br/>

#### 相关
ID_danglingDeref  
ID_explicitDtorCall  
<br/>

#### 参考
SEI CERT MEM01-C  
<br/>
<br/>

## <span id="interruption">15. Interruption</span>

### <span id="ID_sig_dataRaces">▌R15.1 避免由信号处理产生的数据竞争</span>

ID_sig_dataRaces&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: interruption warning

<hr/>

异步信号处理函数的调用会随时打断主程序的流程，当处理函数返回后，主程序在被打断的位置继续执行，这种方式称为“[中断（interrupt）](https://en.wikipedia.org/wiki/Interrupt)”，与执行非并发的线程相似，但没有锁等同步机制，而且信号处理函数本身也可能被中断，所以在信号处理函数中访问共享数据应格外小心。  
  
异步信号处理函数的安全模式：  
 - 调用“[异步信号安全](https://man7.org/linux/man-pages/man7/signal-safety.7.html)”函数执行清理或结束进程，如 abort、\_Exit 等  
 - 对 volatile sig\_atomic\_t 等类型的共享对象赋值，主程序周期性地检查共享对象并执行相应动作  
 - 利用 sigsetjmp、siglongjmp 等函数使流程跳转到主程序中的预定位置  
 - 通过管道等方式与主程序通信，向管道写入一个字节，主程序监控该管道并执行相应动作  
  
只应选择其中一种方式，且尽量避免访问共享数据，否则对共享数据的错误处理会使程序产生未定义的行为。  
  
示例：
```
char msg[32];

void handler(int signum) {
    strcpy(msg, "SIGINT received");      // Non-compliant
}

int main() {
    signal(SIGINT, handler);
    strcpy(msg, "No signal received");   // Race condition
    ....
    printf("%s\n", msg);
}
```
例中信号处理函数和主程序均访问了共享数据，handler 中的 strcpy 可以在 main 中的 strcpy 执行之前或中途执行，造成非预期的结果。  
  
应改为：
```
volatile sig_atomic_t flag = 0;

void handler(int signum) {
    flag = 1;                // Compliant
}

int main() {
    signal(SIGINT, handler);
    ....
    printf("%s received\n", flag? "SIGINT": "No signal");
}
```
用 SIG\_ATOMIC\_MIN 和 SIG\_ATOMIC\_MAX 之间的值对 sig\_atomic\_t 类型的对象赋值可以保证原子性，超出范围的赋值，或赋值之外的操作不能保证原子性，需要避免。
<br/>
<br/>

#### 相关
ID_dataRaces  
ID_sig_nonAsyncSafeCall  
<br/>

#### 依据
ISO/IEC 9899:2011 7.14.1.1(5)-undefined  
ISO/IEC 14882:2011 1.9(6)-undefined  
<br/>

#### 参考
SEI CERT SIG31-C  
<br/>
<br/>

### <span id="ID_sig_nonAsyncSafeCall">▌R15.2 处理信号时避免使用非异步信号安全函数</span>

ID_sig_nonAsyncSafeCall&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: interruption warning

<hr/>

不处理共享数据也不会影响程序状态的函数，以及不会被信号中断的函数称为“[异步信号安全](https://man7.org/linux/man-pages/man7/signal-safety.7.html)”函数，处理信号时只应使用这种函数。  
  
示例：
```
#include <signal.h>

void handler(int signum) {
    printf("....");          // Non-compliant
}

int main() {
    signal(SIGINT, handler);
    printf("....");
}
```
printf 不是异步信号安全函数，例中两个 printf 的输出结果可能会交织在一起，甚至会产生运行时错误。
<br/>
<br/>

#### 相关
ID_sig_dataRaces  
ID_dataRaces  
<br/>

#### 依据
ISO/IEC 9899:2011 7.14.1.1(5)-undefined  
<br/>

#### 参考
SEI CERT SIG30-C  
<br/>
<br/>

### <span id="ID_sig_illReturn">▌R15.3 SIGFPE、SIGILL、SIGSEGV 等信号的处理函数不可返回</span>

ID_sig_illReturn&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: interruption warning

<hr/>

与计算异常相关的信号由不可恢复的错误引起，相关信号处理函数应终止程序的执行，否则导致标准未定义的行为。  
  
示例：
```
#include <signal.h>

void handler(int signum) {
    ....
    return;   // Non-compliant
}

int main() {
    signal(SIGFPE, handler);
    ....
}
```
当发生除 0 等计算异常时，程序会收到 SIGFPE 信号，这种信号对应的处理函数应使用 abort、\_Exit 等函数终止程序的执行，不可正常返回，否则可能会造成更严重的损失。  
  
应改为：
```
void handler(int signum) {
    ....
    _Exit(1);   // Compliant
}
```
或使用 sigsetjmp 和 siglongjmp 使流程跳转到主程序中的预定位置：
```
sigjmp_buf buf;

void handler(int x) {
    siglongjmp(buf, 1);   // Compliant
}

int main() {
    signal(SIGFPE, handler);
    if (sigsetjmp(buf, 1)) {
        ....                   // #1, Normal procedure
        return 0;              // Normal exit
    } else {
        ....                   // #2, handle error
        return 1;              // Abnormal exit
    }
}
```
在这种模式下，`#1` 实现程序的功能，如果收到了 SIGFPE 信号，流程就会跳转到 `#2`。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 7.14.1.1(3)-undefined  
<br/>

#### 参考
SEI CERT SIG35-C  
<br/>
<br/>

### <span id="ID_forbidSignalFunction">▌R15.4 禁用 signal 函数</span>

ID_forbidSignalFunction&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: interruption suggestion

<hr/>

signal 函数具有一定的局限性，且各平台实现差异较大，可用 sigaction 函数代替。  
  
示例：
```
#include <signal.h>

void handler(int signum) {     // #1
    signal(signum, handler);   // #2, non-compliant, race condition
    ....
}
```
设例中 handler 是某种信号的处理函数。在某些平台上，signal 指定的函数只能被执行一次，所以需要在 handler 中再次调用 signal 指定处理函数，但如果程序在运行到 `#1` 和 `#2` 之间时收到同样的信号，会执行不符合预期的默认处理函数，这是一种竞态条件；而在另一些平台上，signal 指定的函数会一直有效，handler 再次调用 signal 是多余的。  
  
sigaction 函数不存在这些问题，也可提供更多的功能，但要注意该函数尚未在语言标准中定义。
<br/>
<br/>

#### 相关
ID_signalInMultiThreading  
ID_implementationDefinedFunction  
<br/>

#### 依据
ISO/IEC 9899:2011 7.14.1.1(3)-implementation  
ISO/IEC 9899:2011 7.14.1.1(7)-implementation  
<br/>

#### 参考
MISRA C 2012 21.5  
SEI CERT SIG01-C  
SEI CERT SIG34-C  
<br/>
<br/>

## <span id="concurrency">16. Concurrency</span>

### <span id="ID_dataRaces">▌R16.1 访问共享数据应遵循合理的同步机制</span>

ID_dataRaces&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: concurrency warning

<hr/>

如果一份数据同时被多个线程、进程或中断处理过程读写，会产生不确定的结果，这种情况称为“[数据竞争（data race）](https://en.cppreference.com/w/cpp/language/memory_model#Threads_and_data_races)”，导致标准未定义的行为，应落实合理的同步机制来控制访问共享数据的先后顺序。  
  
示例：
```
int foo() {
    static int id = 0;
    return id++;        // Data races in multithreading
}
```
这个函数意在每次被调用都可以返回不同的整数，但如果多个线程同时执行 id\+\+，会使读取、计算、写入等步骤交织在一起，得到错误的结果，这是一种典型的数据竞争。  
  
应改为：
```
int foo() {
    static atomic<int> id(0);
    return id.fetch_add(1);    // OK
}
```
其中 atomic 是 C\+\+ 标准原子类，fetch\_add 将对象持有的整数增 1 并返回之前的值，这个过程不会被多个线程同时执行，只能依次执行，从而保证了返回值的唯一性和正确性。  
  
对共享数据访问次序的控制称为“[同步（synchronization）](https://en.wikipedia.org/wiki/Synchronization_(computer_science))”，可使用锁、条件变量、原子操作等方法实现对线程的同步。与共享数据相关，但未落实同步机制的函数不应在多线程环境中使用，如：
```
asctime         // use asctime_r or asctime_s instead
ctime           // use ctime_r or ctime_s instead
localtime       // use localtime_r or localtime_s instead
gmtime          // use gmtime_r or gmtime_s instead
strtok          // use strtok_r or strtok_s instead
strerror        // use strerror_r or strerror_s instead
tmpnam          // use tmpnam_r or tmpnam_s instead
setlocale       // use mutex to protect multithreaded access
rand, srand     // use random, srandom or BCryptGenRandom instead
```
与线程同步不同，中断处理过程的同步较为特殊，可参见 ID\_sig\_dataRaces 的进一步讨论。  
  
考虑比数据竞争更高层面的问题，如果程序的正确性依赖进线程处理数据的特定时序，一旦这种特定时序被打破便会产生错误或漏洞，攻击者可以抢在某关键过程前后通过修改共享数据达到攻击目的，这种情况称为“[竞态条件（race conditon）](https://en.wikipedia.org/wiki/Race_condition)”，如：
```
int* p = get_shared();   // #0, ‘p’ points to shared data
if (*p == 0) {           // #1, ‘*p’ is unreliable
    ....
}
else if (*p == 1) {      // #2, ‘*p’ is unreliable
    ....
}
else {                   // #3
    ....
}
```
如果 p 指向共享数据，那么攻击者可以通过修改共享数据实现对程序流程的劫持，比如在 `#0` 处 \*p 的值本为 0，攻击者在 `#1` 之前改变 \*p 的值，迫使流程向 `#2` 或 `#3` 处跳转。  
  
关于竞态条件的进一步讨论可参见 ID\_TOCTOU、ID\_forbidSignalFunction 等规则。
<br/>
<br/>

#### 相关
ID_sig_dataRaces  
ID_sig_nonAsyncSafeCall  
<br/>

#### 依据
ISO/IEC 9899:2011 5.1.2.4(3)-undefined  
ISO/IEC 9899:2011 5.1.2.4(20)-undefined  
ISO/IEC 9899:2011 5.1.2.4(25)-undefined  
<br/>

#### 参考
CWE-362  
C++ Core Guidelines CP.2  
SEI CERT CON33-C  
SEI CERT CON43-C  
<br/>
<br/>

### <span id="ID_TOCTOU">▌R16.2 避免在事务中通过路径多次访问同一文件</span>

ID_TOCTOU&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: concurrency warning

<hr/>

攻击者可以在两次通过路径访问文件的中途对文件做手脚，从而造成不良后果。  
  
这种问题称为“[TOCTOU（Time\-of\-check to time\-of\-use）](https://en.wikipedia.org/wiki/Time-of-check_to_time-of-use)”。有时需要先检查文件的某种状态，如果状态满足条件的话，再使用该文件，如果“检查”和“使用”都是通过路径完成的，攻击者可以在中途将文件替换成不满足条件的文件，如将文件替换成指向另一个文件的链接，从而对系统造成破坏，这是一种典型的“[竞态条件](https://en.wikipedia.org/wiki/Race_condition)”。  
  
示例：
```
void create(const char* path) {
    FILE* fp = fopen(path, "r");
    if (fp != NULL) {              // #1, time-of-check
        fclose(fp);
        return;
    }
    fp = fopen(path, "w");         // #2, time-of-use, non-compliant
    if (fp != NULL) {
        fwrite("abc", 1, 3, fp);
        fclose(fp);
    }
}
```
示例代码先通过路径判断文件是否存在，如果存在则不作处理，如果不存在则再次通过路径创建文件并写入数据。如果攻击者把握住时机，在程序执行到 `#1` 和 `#2` 之间时按 path 创建指向其他文件的链接，那么被指向的文件会遭到破坏，尤其是当被攻击的进程权限比较高时，破坏力是难以控制的。  
  
应只通过路径打开文件对象一次，只通过文件对象操作文件：
```
void create(const char* path) {
    FILE* fp = fopen(path, "wx");   // Compliant, since C11
    if (fp != NULL) {
        fwrite("abc", 1, 3, fp);
        fclose(fp);
    }
}
```
利用“wx”模式即可保证 fopen 在文件不存在时创建文件，文件存在时返回空。  
  
注意，目前 C\+\+ 的 fstream 尚无法完成与“wx”模式相同的功能，相同功能的代码要用 fopen 实现。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 7.21.5.3(3)  
<br/>

#### 参考
CWE-367  
<br/>
<br/>

### <span id="ID_atomicRaces">▌R16.3 避免在事务中多次非同步地访问原子对象</span>

ID_atomicRaces&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: concurrency warning

<hr/>

原子对象可以保证某些特定操作的原子性，但特定操作的组合并不具备原子性，非同步地访问原子对象仍然存在数据竞争。  
  
示例：
```
atomic_int i = ATOMIC_VAR_INIT(0);

void thd() {
    i = i + 1;   // Non-compliant, data races
}
```
设 thd 为线程函数，原子对象 i 在表达式中出现了多次，其读取、计算、写入等过程在多线程中仍然是交织在一起的，造成数据竞争。  
  
应改为：
```
void thd() {
    atomic_fetch_add(&i, 1);   // Compliant, or use ‘operator++’ in C++
}
```
对于一些复杂的原子运算，如：
```
i = (i + 1) % 5;   // Non-compliant
```
可采用 “[CAS（compare and swap）](https://en.wikipedia.org/wiki/Compare-and-swap)” 方法同步：
```
int old_i = atomic_load(&i);
int new_i = 0;
do {
    new_i = (old_i + 1) % 5;
} while (!compare_and_swap(&i, &old_i, new_i));   // Compliant
```
首先读取原子对象的值 old\_i，old\_i 经过运算得到新值 new\_i，再通过 compare\_and\_swap 更新原子对象的值。compare\_and\_swap 具有原子性，将 old\_i 和原子对象当前值比较，相等则说明在运算过程中原子对象没有被其他线程更新，将原子对象的值设为 new\_i，不相等则说明原子对象已被其他线程更新，将 old\_i 设为原子对象当前值，再重复这个过程，直到原子对象可用 new\_i 更新。  
  
compare\_and\_swap 是重要的原子对象同步手段，在实际代码中可与 atomic\_compare\_exchange\_weak、atomic\_compare\_exchange\_strong 等函数对应。
<br/>
<br/>

#### 相关
ID_dataRaces  
<br/>

#### 参考
SEI CERT CON40-C  
<br/>
<br/>

### <span id="ID_deadlock">▌R16.4 避免死锁</span>

ID_deadlock&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: concurrency warning

<hr/>

对于锁等资源，错误的请求时序或管理方式会使程序永远陷入等待状态，这种问题称为“[死锁（deadlock）](https://en.wikipedia.org/wiki/Deadlock)”。  
  
示例：
```
mtx_t m;               // Non-recursive mutex

void foo() {
    mtx_lock(&m);      // Lock the mutex
    ....
}

void bar() {
    mtx_lock(&m);      // Lock the mutex
    foo();             // Undefined behavior, may deadlock
    ....
}
```
设 m 是非递归互斥量，bar 锁定互斥量后调用 foo，而 foo 也会锁定互斥量，导致 foo 等待 bar 解锁，而 foo 返回之前 bar 不可能解锁，这是一种导致死锁的逻辑错误，C11 也明确规定在同一线程中不可重复锁定非递归互斥量。  
  
另外，线程之间相互等待对方解锁也是死锁的主要原因，如：
```
struct A {
    ....
    mtx_t m;          // Mutex
} a, b;

void thr1() {
    mtx_lock(&a.m);   // Lock
    mtx_lock(&b.m);
    ....
}

void thr2() {
    mtx_lock(&b.m);   // Lock in another order
    mtx_lock(&a.m);   // May deadlock
    ....
}
```
设 thr1 和 thr2 是两个可以并发执行的函数，如果 a.m 被 thr1 锁定，b.m 被 thr2 锁定，thr1 等待 b.m 解锁，而 thr2 等待 a.m 解锁，这种相互等待导致了死锁的局面。例中 a 和 b 是具名全局对象，在各线程中按统一的顺序加锁可避免死锁。  
  
在更普遍的情况下，为不同对象加锁前，可使对象按某种内在的标准“排序”，再依次加锁，如：
```
struct A {
    int id;           // Unique identifier
    ....
    mtx_t m;          // Mutex
};

void lock_in_order(A* p, A* q) {
    if (p->id > q->id) {
        A* t = p; p = q; q = t;
    }
    mtx_lock(&p->m);
    mtx_lock(&q->m);
}
```
为每个对象分配一个 id 以标识不同的对象，每次 id 小的先加锁，可有效避免相互等待造成的死锁。示例代码忽略了 id 相等的情况，在实际代码中应补全，否则也会造成第一个例子中的问题。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 7.26.4.3(2)-undefined  
<br/>

#### 参考
SEI CERT CON35-C  
SEI CERT CON53-CPP  
SEI CERT CON56-CPP  
<br/>
<br/>

### <span id="ID_asynchronousTermination">▌R16.5 避免异步终止线程</span>

ID_asynchronousTermination&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: concurrency warning

<hr/>

资源的使用情况在异步过程中是难以掌控的，异步终止线程往往会导致泄漏或死锁等严重问题。  
  
示例：
```
void* foo(void* param) {
    pthread_setcanceltype(
        PTHREAD_CANCEL_ASYNCHRONOUS, ....  // Non-compliant
    );
    ....                                   // Allocate or lock
}

void bar() {
    pthread_t thd;
    pthread_create(&thd, NULL, foo, NULL);
    ....
    pthread_cancel(thd);   // Non-compliant, leak or deadlock
}
```
以 pthread 线程库为例，foo 和 bar 是两个相关的异步过程，foo 通过 PTHREAD\_CANCEL\_ASYNCHRONOUS 指定其线程可以随时被终止，bar 调用 pthread\_cancel 终止 foo 线程，在一个过程中暴力终止另一个过程是非常危险的，会使锁、信号量或动态分配的资源无法释放。  
  
PTHREAD\_CANCEL\_ASYNCHRONOUS 等选项、TerminateThread 等 Windows API，以及具有相同功能的选项或 API 均不应使用，应使线程主动执行清理并正常结束执行。
<br/>
<br/>

#### 相关
ID_illLifetime  
<br/>

#### 参考
SEI CERT POS47-C  
<br/>
<br/>

### <span id="ID_illLifetime">▌R16.6 避免异步终止共享对象的生命周期</span>

ID_illLifetime&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: concurrency warning

<hr/>

共享对象的使用情况在异步过程中是难以掌控的，贸然终止共享对象的生命周期往往会导致标准未定义的行为。  
  
示例：
```
void foo(mutex* pm) {
    lock(pm);
    ....
}

void bar() {
    mutex m;
    beginThread(foo, &m);   // Non-compliant, ‘m’ is a local object
    beginThread(foo, &m);   // Non-compliant
}
```
设例中 beginThread 创建执行 foo 函数的线程，bar 与 foo 是异步过程，共享对象 m 在 bar 返回后失效，如果 foo 继续访问共享对象就会出错，bar 应等待线程执行完毕或调整共享对象的生命周期。
<br/>
<br/>

#### 相关
ID_asynchronousTermination  
ID_localAddressFlowOut  
ID_danglingDeref  
<br/>

#### 依据
ISO/IEC 9899:2011 30.4.1.2.1(5)-undefined  
<br/>

#### 参考
SEI CERT CON31-C  
SEI CERT CON50-CPP  
<br/>
<br/>

### <span id="ID_spuriouslyWakeUp">▌R16.7 避免虚假唤醒造成同步错误</span>

ID_spuriouslyWakeUp&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: concurrency warning

<hr/>

条件不满足时条件变量也可以被唤醒，这种情况称为“[虚假唤醒（spurious wakeup）](https://en.wikipedia.org/wiki/Spurious_wakeup)”，条件变量被唤醒后应检查相关条件是否满足，否则会造成同步相关的错误。  
  
示例：
```
mtx_t m;    // Mutex
cnd_t cv;   // Condition variable
bool cnd;   // Represents the condition

void thd() {                 // Thread function
    mtx_lock(&m);            // Lock
    if (!cnd) {              // Non-compliant, use a while loop instead
        cnd_wait(&cv, &m);   // Wait
    }
    ....
}
```
设例中 cv 是条件变量，cnd 代表相关条件，cnd\_wait 等待条件被其他异步过程满足，条件的判断与更改应是互斥的，cnd\_wait 会解锁并进入等待状态，当得到 cnd\_signal 或 cnd\_broadcast 的通知后会退出等待状态并再次加锁，但在条件不满足时也可能退出等待，原因主要有：  
 - 一个条件变量对应多个条件，与当前条件无关的条件被满足并通知了条件变量  
 - 在退出等待并加锁的过程中其他线程使条件不被满足  
 - 等待过程被信号打断  
  
这些问题取决于程序和系统的具体实现，在循环中等待并判断条件可一并解决这些问题：
```
void thd() {
    mtx_lock(&m);
    while (!cnd) {           // Compliant
        cnd_wait(&cv, &m);
    }
    ....
}
```
<br/>
<br/>

#### 参考
SEI CERT CON36-C  
SEI CERT CON54-CPP  
<br/>
<br/>

### <span id="ID_bitfieldDataRaces">▌R16.8 避免并发访问位域造成的数据竞争</span>

ID_bitfieldDataRaces&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: concurrency warning

<hr/>

相邻的位域成员可能在一个存储单元中，所以并发访问位域也可能造成数据竞争。  
  
示例：
```
struct A {
    unsigned x: 1;
    unsigned y: 1;
} a;

void thr1() {
    a.x = 0;    // Non-compliant, missing lock
}

void thr2() {
    a.y = 1;    // Non-compliant, missing lock
}
```
设例中 thr1 和 thr2 是可以并发执行的函数，位域成员 x 和 y 在一个存储单元中，对 x 或 y 的更新相当于用新数据更新存储单元的值，再将存储单元的值整体写入内存，这个过程如果是异步并发执行的就会产生错误，所以对不同位域成员的并发访问也应保证合理的同步措施。
<br/>
<br/>

#### 相关
ID_dataRaces  
ID_forbidBitfield  
<br/>

#### 依据
ISO/IEC 9899:2011 3.14(3)  
ISO/IEC 14882:2011 1.7(4)  
<br/>

#### 参考
SEI CERT CON32-C  
SEI CERT CON52-CPP  
<br/>
<br/>

### <span id="ID_signalInMultiThreading">▌R16.9 多线程环境中不可使用 signal 函数</span>

ID_signalInMultiThreading&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: concurrency warning

<hr/>

在多线程环境中使用 signal 函数会导致标准未定义的行为。  
  
示例：
```
#include <signal.h>
#include <threads.h>

void handler(int);

int thread(void* param) {
    signal(SIGINT, handler);  // Non-compliant
    ....
}
```
设例中 thread 是线程函数，在多线程环境中使用 signal 函数是不符合要求的。
<br/>
<br/>

#### 相关
ID_forbidSignalFunction  
<br/>

#### 依据
ISO/IEC 9899:2011 7.14.1.1(7)-undefined  
<br/>

#### 参考
SEI CERT CON37-C  
<br/>
<br/>

## <span id="style">17. Style</span>

### <span id="ID_spaceStyle">▌R17.1 空格应遵循统一风格</span>

ID_spaceStyle&emsp;&emsp;&emsp;&emsp;&nbsp;:womans_hat: style suggestion

<hr/>

与运算符、标点符、关键字相关的空格应遵循统一风格，过于随意的空格会干扰阅读，甚至形成笔误。  
  
本规则暂不限定具体风格，但强调一致性，同类运算符、标点符、关键字的空格方式应保持一致，tab 等变长空白符不应用作空格。  
  
示例：
```
a= 0;                   // Bad
b = a +1;               // Bad
c =~ a;                 // Bad
while (d --> 0)         // Bad

a == 0 ? foo() :bar();  // Bad
baz (1 , 2 ,3);         // Bad

if (cond)
if(cond)                // Missing a unified style
```
例中运算符和关键字相关的空格风格不一致，代码显得很混乱。  
  
本规则是 ID\_stickyAssignmentOperator 的泛化，该规则描述了一种由空格造成的错误。
<br/>
<br/>

#### 相关
ID_stickyAssignmentOperator  
<br/>
<br/>

### <span id="ID_braceStyle">▌R17.2 大括号应遵循统一风格</span>

ID_braceStyle&emsp;&emsp;&emsp;&emsp;&nbsp;:womans_hat: style suggestion

<hr/>

大括号应遵循统一的换行和缩进风格，否则会干扰阅读，甚至形成笔误。  
  
命名空间、类、函数体、复合语句等不同类别的大括号，换行方式可以不同，但同类大括号的换行方式应该是一致的，本规则暂不限定具体风格，但强调一致性。  
  
示例：
```
void foo() {     // Non-compliant, mssing a unified style
    if (cond)
    {
        ....
    }
    else {
        ....
    }
}

void bar()       // Non-compliant, mssing a unified style
{
    if (cond) {
        ....
    } else
    {
        ....
    }
}
```
例中大括号换行的方式不一致，代码显得很混乱。  
  
本规则是 ID\_if\_mayBeElseIf 的泛化，该规则描述了一种由换行造成的错误。
<br/>
<br/>

#### 相关
ID_if_mayBeElseIf  
<br/>
<br/>

### <span id="ID_mixNullptrAndNULL">▌R17.3 NULL 和 nullptr 不应混用</span>

ID_mixNullptrAndNULL&emsp;&emsp;&emsp;&emsp;&nbsp;:womans_hat: style warning

<hr/>

NULL 和 nullptr 不应混用，应统一使用 nullptr。  
  
示例：
```
void foo(int* a = NULL, int* b = nullptr);     // Non-compliant
void bar(int* a = nullptr, int* b = nullptr);  // Compliant
```
<br/>
<br/>

#### 相关
ID_deprecatedNULL  
<br/>

#### 参考
C++ Core Guidelines ES.47  
<br/>
<br/>

### <span id="ID_deprecatedNULL">▌R17.4 在 C++ 代码中用 nullptr 代替 NULL</span>

ID_deprecatedNULL&emsp;&emsp;&emsp;&emsp;&nbsp;:womans_hat: style suggestion

<hr/>

在 C\+\+ 中，NULL 虽然可以表示空指针，但容易与整型常量 0 混淆，应使用 nullptr 表示空指针。  
  
示例：
```
void foo(int) {
    cout << "foo-1\n";
}

void foo(int*) {
    cout << "foo-2\n";
}

int main() {
    foo(NULL);  // Non-compliant, what is output?
}
```
NULL 表示指针，所以应该调用参数为指针的重载函数，但不同的编译器对这段代码有不同的处理，有的无法通过编译，有的编译执行后会输出 foo\-1，用 nullptr 代替 NULL 可解决这种问题。
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 7.17(3)-implementation  
ISO/IEC 9899:2011 7.19(3)-implementation  
ISO/IEC 14882:2003 C.2.2.3(1)-implementation  
ISO/IEC 14882:2011 C.3.2.4(1)-implementation  
ISO/IEC 14882:2017 C.5.2.7(1)-implementation  
ISO/IEC 14882:2011 2.14.7(1)  
<br/>

#### 参考
C++ Core Guidelines ES.47  
<br/>
<br/>

### <span id="ID_assignmentAsSubExpression">▌R17.5 赋值表达式不应作为子表达式</span>

ID_assignmentAsSubExpression&emsp;&emsp;&emsp;&emsp;&nbsp;:womans_hat: style suggestion

<hr/>

赋值表达式作为子表达式增加了复杂性，且容易产生优先级相关的问题。  
  
普通赋值表达式、复合赋值表达式均受本规则约束。  
  
示例：
```
int a, b = foo();
if (a = b != 0) {    // Non-compliant
    ....
}
int c = bar();
return a += b += c;  // Non-compliant
```
<br/>
<br/>

#### 参考
CWE-481  
MISRA C 2004 13.1  
MISRA C 2012 13.4  
MISRA C++ 2008 6-2-1  
<br/>
<br/>

### <span id="ID_redundantSemicolon">▌R17.6 不应存在多余的分号</span>

ID_redundantSemicolon&emsp;&emsp;&emsp;&emsp;&nbsp;:womans_hat: style suggestion

<hr/>

多余的分号使代码显得繁琐，也可能包含某种错误，应该去掉。  
  
示例：
```
namespace N {
    void foo() {
        bar();;    // Non-compliant
    };             // Non-compliant
};                 // Non-compliant
```
<br/>
<br/>
<br/>


## 附录
&emsp;&emsp;[`c-ub-list.md`](./c-ub-list.md)：C 未定义行为成因列表  
&emsp;&emsp;[`cpp-ub-list.md`](./cpp-ub-list.md)：C++ 未定义行为成因列表与标准解读  

<br/>
<br/>


## 结语
&emsp;&emsp;保障软件安全、提升产品质量是宏大的主题，需要不断地学习、探索与实践，也难以在一篇文章中涵盖所有要点，这 441 条规则就暂且讨论至此了。欢迎提供修订意见和扩展建议，由于本文档是自动生成的，请不要直接编辑本文档，可在 Issue 区发表高见，管理员修正数据库后会在致谢列表中存档。

&emsp;&emsp;此致

祝编程愉快！
