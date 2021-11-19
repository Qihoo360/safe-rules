<img src="logo.png" align="right"/>

# C/C++安全规则集合 ![Version](https://img.shields.io/badge/version-1.0.0--alpha-brightgreen)

> Bjarne Stroustrup: “*C makes it easy to shoot yourself in the foot; C++ makes it harder, but when you do it blows your whole leg off.*”

&emsp;&emsp;针对C、C++语言，本文收录了409种需要重点关注的问题，可为制定编程规范提供依据，也可为代码审计以及相关培训提供指导意见，适用于桌面、服务端以及嵌入式等软件系统。  
&emsp;&emsp;每个问题对应一条规则，每条规则可直接作为规范条款或审计检查点，本文是适用于不同应用场景的规则集合，读者可根据自身需求从中选取某个子集作为规范或审计依据，从而提高软件产品的安全性。
<br/>

## 规则说明

规则按如下主题分为15个类别：

 1. [Security：](#__Security)敏感数据保护、攻击防御等问题
 2. [Resource：](#__Resource)资源分配、使用与回收
 3. [Precompile：](#__Precompile)预处理指令、宏、注释等问题
 4. [Global：](#__Global)全局及命名空间作用域相关问题
 5. [Type：](#__Type)类型相关的设计与实现
 6. [Declaration：](#__Declaration)声明
 7. [Exception：](#__Exception)异常
 8. [Function：](#__Function)函数实现
 9. [Control：](#__Control)流程控制
 10. [Expression：](#__Expression)表达式
 11. [Literal：](#__Literal)常量
 12. [Cast：](#__Cast)类型转换
 13. [Buffer：](#__Buffer)缓冲区
 14. [Pointer：](#__Pointer)指针
 15. [Style：](#__Style)代码书写的样式、风格等问题

每条规则包括：

 - 编号：规则在本文中的章节编号，以“R”开头，称为Section-ID
 - 名称：用简练的短语描述违反规则的状况，以“ID_”开头，称为Fault-ID
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
 - 依据：规则依照的ISO/IEC标准，C规则以ISO/IEC 9899:2011为主，C++规则以ISO/IEC 14882:2011为主
 - 配置：某些规则的对象可由用户指定，审计工具可以此为参照实现定制化功能
 - 参考：规则参考的其他规范条款，如C++ Core Guidelines、MISRA、CWE、SEI CERT等，也可作为扩展阅读的线索

规则的相关性分为：

 - 特化：设规则A的特殊情况需要由规则B阐明，称规则B是规则A的特化
 - 泛化：与特化相反，称规则A是规则B的泛化
 - 相交：设两个规则针对不同的问题，但在内容上有一定的交集，称这两个规则相交

规则以“``标准名称:版本 章节编号(段落编号)-性质``”的格式引用标准，如“``ISO/IEC 14882:2011 5.6(4)-undefined``”，表示引用C++11标准的第5章第6节第4段说明的具有undefined性质的问题。  

其中“性质”分为：

 - undefined：一般指某种错误，使程序产生undefined behavior
 - unspecified：标准不作明确规定的情况，由编译器或环境自主定义，具有随意性
 - implementation defined：由实现定义，也是由编译器或环境自主定义，与unspecified不同，要求有明确的文档支持
 - deprecated：已过时或已废弃的用法

本文以ISO/IEC 9899:2011以及ISO/IEC 14882:2011为主要依据，兼顾C18、C++17以及历史标准，没有特殊说明的规则同时适用于C语言和C++语言，只适用于某一种语言的规则会另有说明。

## 规则选取
本文是适用于不同应用场景的规则集合，读者可选取适合自己需求的规则。

指出某种错误的规则，如有“不可”、“不应”等字样的规则应尽量被选取，有“禁用”等字样的规则可能只适用于某一场景，可酌情选取。

如果将本文作为培训内容，为了全面理解各种场景下存在的问题，应选取全部规则。

## 规则列表
<span id="__Security">**[1. Security](#security)**</span>
  - [R1.1 敏感数据不应以明文形式写入代码](#ID_plainSensitiveInfo)
  - [R1.2 敏感数据不可被系统外界感知](#ID_secretLeak)
  - [R1.3 敏感数据在使用后应被有效清理](#ID_unsafeCleanup)
  - [R1.4 公共成员或全局对象不应记录敏感数据](#ID_sensitiveName)
  - [R1.5 预判由用户输入造成的不良后果](#ID_hijack)
  - [R1.6 避免在一个事务中通过路径多次访问同一文件](#ID_TOCTOU)
  - [R1.7 访问共享数据应遵循合理的同步机制](#ID_dataRaces)
  - [R1.8 对文件设定合理的权限](#ID_unlimitedAuthority)
  - [R1.9 对用户设置合理的权限](#ID_nullACL)
  - [R1.10 不应引用危险符号名称](#ID_dangerousName)
  - [R1.11 避免调用具有危险性的函数](#ID_dangerousFunction)
  - [R1.12 不应调用已过时的函数](#ID_obsoleteFunction)
  - [R1.13 禁用不安全的字符串函数](#ID_unsafeStringFunction)
  - [R1.14 确保字符串以空字符结尾](#ID_improperNullTermination)
  - [R1.15 避免使用由实现定义的库函数](#ID_implementationDefinedFunction)
  - [R1.16 除数不可存在值为0的可能性](#ID_divideByZero)
  - [R1.17 禁用atof、atoi、atol以及atoll等函数](#ID_forbidAtox)
  - [R1.18 格式化字符串应为常量](#ID_variableFormatString)
  - [R1.19 与程序实现相关的信息不可被外界感知](#ID_addressExposure)
  - [R1.20 不应硬编码IP地址](#ID_hardcodedIP)
  - [R1.21 避免使用errno](#ID_deprecatedErrno)
<br/>

<span id="__Resource">**[2. Resource](#resource)**</span>
  - [R2.1 不可失去对已分配资源的控制](#ID_resourceLeak)
  - [R2.2 不可失去对已分配内存的控制](#ID_memoryLeak)
  - [R2.3 不可访问未初始化或已释放的资源](#ID_illAccess)
  - [R2.4 在栈上分配的空间以及非动态申请的资源不可被释放](#ID_illDealloc)
  - [R2.5 资源不可被重复释放](#ID_doubleFree)
  - [R2.6 资源的分配与回收方法应配套使用](#ID_incompatibleDealloc)
  - [R2.7 用new分配的单个对象应该用delete释放，不应该用delete\[\]释放](#ID_excessiveDelete)
  - [R2.8 用new分配的数组应使用delete\[\]释放，不应使用delete释放](#ID_insufficientDelete)
  - [R2.9 对象被move之后的值不应再被使用](#ID_useAfterMove)
  - [R2.10 资源管理应遵循面向对象的方法](#ID_ownerlessResource)
  - [R2.11 对象申请的资源须在析构函数中释放](#ID_memberDeallocation)
  - [R2.12 如果构造函数抛出异常需确保相关资源没有泄漏](#ID_throwInConstructor)
  - [R2.13 C\+\+代码中禁用malloc、free等内存分配与回收函数](#ID_forbidMallocAndFree)
  - [R2.14 避免不必要的内存分配](#ID_unnecessaryAllocation)
  - [R2.15 在一个语句中最多执行一次显式资源分配](#ID_multiAllocation)
  - [R2.16 避免使用在栈上分配内存的函数](#ID_stackAllocation)
  - [R2.17 避免动态内存分配](#ID_dynamicAllocation)
  - [R2.18 标准FILE对象不应被复制](#ID_copiedFILE)
<br/>

<span id="__Precompile">**[3. Precompile](#precompile)**</span>
  - [3.1 Include](#precompile.include)
    - [R3.1.1 字母、数字、下划线以及点号之外的字符不应出现在头文件名称中](#ID_nonStandardCharInHeaderName)
    - [R3.1.2 include指令中不应使用反斜杠](#ID_forbidBackslashInHeaderName)
    - [R3.1.3 include指令中不应使用绝对路径](#ID_forbidAbsPathInHeaderName)
    - [R3.1.4 禁用不合规的头文件](#ID_forbiddenHeader)
    - [R3.1.5 在C\+\+代码中不应引用C头文件](#ID_forbidCHeaderInCpp)
  - [3.2 Macro](#precompile.macro)
    - [R3.2.1 宏的命名应遵循合理的方式](#ID_macro_badName)
    - [R3.2.2 禁止定义具有保留意义的宏名称](#ID_macro_defineReserved)
    - [R3.2.3 禁止取消定义具有保留意义的宏名称](#ID_macro_undefReserved)
    - [R3.2.4 可作为子表达式的宏定义应该用括号括起来](#ID_macro_expNotEnclosed)
    - [R3.2.5 宏参数在宏定义的表达式中应该用括号括起来](#ID_macro_paramNotEnclosed)
    - [R3.2.6 由多个语句组成的宏定义应该用do\-while(0)括起来](#ID_macro_stmtNotEnclosed)
    - [R3.2.7 宏的实参个数不可小于形参个数](#ID_macro_insufficientArgs)
    - [R3.2.8 宏的实参个数不可大于形参个数](#ID_macro_redundantArgs)
    - [R3.2.9 宏参数不应有副作用](#ID_macro_sideEffectArgs)
    - [R3.2.10 宏参数数量应在规定范围之内](#ID_macro_tooManyParams)
    - [R3.2.11 宏名称中不应存在拼写错误](#ID_macro_misspelling)
    - [R3.2.12 不应使用宏定义常量](#ID_macro_const)
    - [R3.2.13 不应使用宏定义类型](#ID_macro_typeid)
    - [R3.2.14 可由函数实现的功能不应使用宏实现](#ID_macro_function)
    - [R3.2.15 在C\+\+代码中不应使用宏offsetof](#ID_deprecatedOffsetof)
    - [R3.2.16 在宏定义中由\#修饰的参数后不应出现\#\#](#ID_macro_complexConcat)
  - [3.3 Directive](#precompile.directive)
    - [R3.3.1 头文件不应缺少守卫](#ID_missingHeaderGuard)
    - [R3.3.2 不应出现非标准格式的预编译指令](#ID_illFormedDirective)
    - [R3.3.3 不应使用非标准预编译指令](#ID_nonStdDirective)
    - [R3.3.4 对编译警告的屏蔽应慎重](#ID_warningDisabled)
    - [R3.3.5 代码在高级别的警告设置下编译](#ID_warningDefault)
  - [3.4 Comment](#precompile.comment)
    - [R3.4.1 关注TODO、FIXME、XXX、BUG等特殊注释](#ID_specialComment)
    - [R3.4.2 注释不可嵌套](#ID_nestedComment)
    - [R3.4.3 注释应出现在合理的位置](#ID_badCommentPosition)
  - [3.5 Other](#precompile.other)
    - [R3.5.1 除转义字符、宏定义之外不应使用反斜杠](#ID_badBackslash)
<br/>

<span id="__Global">**[4. Global](#global)**</span>
  - [R4.1 不可修改std命名空间](#ID_stdNamespaceModified)
  - [R4.2 main函数不应被重载，也不应声明为inline、static或constexpr](#ID_illFormedMain)
  - [R4.3 非全局命名空间中不应存在以main命名的函数](#ID_nonGlobalMain)
  - [R4.4 全局对象的初始化不应依赖尚未初始化的其他对象](#ID_relyOnExternalObject)
  - [R4.5 不应在头文件的全局作用域中使用using directive](#ID_usingNamespaceInHeader)
  - [R4.6 头文件中不应定义匿名命名空间](#ID_anonymousNamespaceInHeader)
  - [R4.7 不应定义过于复杂的内联函数](#ID_complexInlineFunction)
  - [R4.8 函数不应在头文件中实现](#ID_definedInHeader)
  - [R4.9 全局或命名空间作用域中不应存在即不是const也不是static的对象](#ID_nonConstNonStaticGlobalObject)
  - [R4.10 全局或命名空间作用域中不应存在非const对象](#ID_nonConstGlobalObject)
  - [R4.11 全局对象不应同时被static和const关键字修饰](#ID_staticAndConst)
  - [R4.12 匿名命名空间中不应使用静态声明](#ID_staticInAnonymousNamespace)
  - [R4.13 头文件中不应存在由static关键字修饰的非成员对象、数组或函数](#ID_staticInHeader)
  - [R4.14 命名空间作用域中禁用using namespace std之外的using directive](#ID_forbidUsingDirectives)
  - [R4.15 不应在命名空间中引用自身](#ID_usingSelf)
  - [R4.16 不应定义全局inline命名空间](#ID_topInlineNamespace)
  - [R4.17 全局对象、函数、类型以及命名空间名称不应太短](#ID_nameTooShort)
<br/>

<span id="__Type">**[5. Type](#type)**</span>
  - [5.1 Class](#type.class)
    - [R5.1.1 数据成员的数量应在规定范围之内](#ID_tooManyFields)
    - [R5.1.2 类不应即有public数据成员又有private数据成员](#ID_mixPublicPrivateData)
    - [R5.1.3 类的非常量数据成员均应为private](#ID_nonPrivateData)
    - [R5.1.4 类的非常量数据成员不应定义为protected](#ID_protectedData)
    - [R5.1.5 对于菱形继承应将基类设为虚基类](#ID_diamondInheritance)
    - [R5.1.6 带模板的赋值运算符不应覆盖拷贝或移动赋值运算符](#ID_roughTemplateAssignOperator)
    - [R5.1.7 带模板的构造函数不应覆盖拷贝或移动构造函数](#ID_roughTemplateConstructor)
    - [R5.1.8 存在拷贝构造函数或析构函数时，不应缺少拷贝赋值运算符](#ID_missingCopyAssignOperator)
    - [R5.1.9 存在赋值运算符或析构函数时，不应缺少拷贝构造函数](#ID_missingCopyConstructor)
    - [R5.1.10 存在拷贝构造函数或赋值运算符时，不应缺少析构函数](#ID_missingDestructor)
    - [R5.1.11 有虚函数的基类应具有虚析构函数](#ID_missingVirtualDestructor)
    - [R5.1.12 存在移动构造函数时，不应缺少移动赋值运算符](#ID_missingMoveAssignOperator)
    - [R5.1.13 存在移动赋值运算符，不应缺少移动构造函数](#ID_missingMoveConstructor)
    - [R5.1.14 可接受一个参数的构造函数需用explicit关键字限定](#ID_missingExplicitConstructor)
    - [R5.1.15 重载的类型转换运算符需用explicit关键字限定](#ID_missingExplicitConvertor)
    - [R5.1.16 不应过度使用explicit关键字](#ID_excessiveExplicit)
    - [R5.1.17 重载的new和delete运算符应配对出现](#ID_incompleteNewDeletePair)
    - [R5.1.18 抽象类禁用拷贝赋值运算符](#ID_unsuitableCopyAssignOperator)
    - [R5.1.19 存在构造、析构或虚函数的类不应采用struct关键字](#ID_unsuitableStructTag)
    - [R5.1.20 不应出现无效的匿名类](#ID_invalidAnonymousType)
  - [5.2 Enum](#type.enum)
    - [R5.2.1 同类枚举值不应有重复](#ID_duplicateEnumerator)
    - [R5.2.2 各枚举值全不初始化，或者只初始化第一个，或者全部初始化，不应存在其他情况](#ID_casualInitialization)
    - [R5.2.3 不应使用匿名枚举声明](#ID_forbidUnnamedEnum)
    - [R5.2.4 用enum class取代enum](#ID_forbidUnscopedEnum)
  - [5.3 Union](#type.union)
    - [R5.3.1 联合体内禁用非基本类型的对象](#ID_forbidNonBasicField)
    - [R5.3.2 禁用在类之外定义的联合体](#ID_forbidNakedUnion)
    - [R5.3.3 禁用联合体](#ID_forbidUnion)
<br/>

<span id="__Declaration">**[6. Declaration](#declaration)**</span>
  - [R6.1 命名空间、类型、对象、函数的命名应遵循合理的方式](#ID_badName)
  - [R6.2 不应定义具有保留意义的名称](#ID_reservedName)
  - [R6.3 命名空间、类型、对象、函数的名称中不应存在拼写错误](#ID_misspelling)
  - [R6.4 用auto声明指针或引用时应显式标明\*、&等符号](#ID_missingStar)
  - [R6.5 const、volatile限定类型时应出现在左侧](#ID_badQualifierPosition)
  - [R6.6 inline、virtual、static、typedef等关键字应出现在类型名的左侧](#ID_badSpecifierPosition)
  - [R6.7 对常量的定义不应为引用](#ID_constLiteralReference)
  - [R6.8 避免使用std::auto\_ptr](#ID_deprecatedAutoPtr)
  - [R6.9 不建议虚函数的参数有默认值](#ID_deprecatedDefaultArgument)
  - [R6.10 不应使用已过时的关键字](#ID_deprecatedSpecifier)
  - [R6.11 同一命名空间内的类型名称不应与对象或函数名称相同](#ID_duplicatedName)
  - [R6.12 位域长度不应超过类型约定的大小](#ID_exceededBitfield)
  - [R6.13 局部名称不应被覆盖](#ID_hideLocal)
  - [R6.14 成员名称不应被覆盖](#ID_hideMember)
  - [R6.15 不应产生不受控制的无效临时对象](#ID_inaccessibleTmpObject)
  - [R6.16 虚函数参数的默认值应与基类中声明的一致](#ID_inconsistentDefaultArgument)
  - [R6.17 不应使用多余的inline关键字](#ID_inlineRedundant)
  - [R6.18 extern关键字不应作用于类成员的声明或定义](#ID_invalidExternSpecifier)
  - [R6.19 不应将union设为final](#ID_invalidFinal)
  - [R6.20 不应出现不会被用到的局部声明](#ID_invalidLocalDeclaration)
  - [R6.21 不应将数组作为函数的形式参数](#ID_invalidParamArraySize)
  - [R6.22 不应出现无效的声明限定符](#ID_invalidSpecifier)
  - [R6.23 不应存在没有用到的标签](#ID_label_unused)
  - [R6.24 不建议在函数作用域外将lambda表达式定义为具名对象](#ID_lambdaOutOfFunction)
  - [R6.25 指向常量字符串的指针应有const限定符修饰](#ID_missingConst)
  - [R6.26 所有重写的虚函数都应声明为override或final](#ID_missingExplicitOverride)
  - [R6.27 函数原型声明中的参数应具有合理的名称](#ID_missingParamName)
  - [R6.28 C代码中函数参数列表如果为空须声明为“(void)”](#ID_missingVoid)
  - [R6.29 不应将函数或函数指针和其他声明写在同一个语句中](#ID_mixedDeclarations)
  - [R6.30 不建议将类型定义和对象声明写在一个语句中](#ID_mixedTypeObjDefinition)
  - [R6.31 拷贝赋值、移动赋值运算符应返回所属类的非const引用](#ID_nonStdAssignmentRetType)
  - [R6.32 拷贝赋值运算符的参数应为同类对象的const左值引用](#ID_nonStdCopyAssignmentParam)
  - [R6.33 移动赋值运算符的参数应为同类对象的非const右值引用](#ID_nonStdMoveAssignmentParam)
  - [R6.34 派生类不应重新定义与基类相同的非虚函数](#ID_nonVirtualOverride)
  - [R6.35 不应重载取地址运算符](#ID_overloadAddressOperator)
  - [R6.36 不应重载逗号运算符](#ID_overloadComma)
  - [R6.37 不应重载“逻辑与”和“逻辑或”运算符](#ID_overloadLogicOperator)
  - [R6.38 const或volatile不可修饰引用](#ID_qualifierInvalid)
  - [R6.39 const或volatile不应重复](#ID_qualifierRepeated)
  - [R6.40 override和final关键字不应同时出现](#ID_redundantOverride)
  - [R6.41 当有override或final关键字时，不应再出现virtual关键字](#ID_redundantVirtual)
  - [R6.42 const、volatile等关键字不应出现在基本类型名称的中间](#ID_sandwichedModifier)
  - [R6.43 对象初始化不可依赖自身的值](#ID_selfDependentInitialization)
  - [R6.44 参与数值运算的char变量需显式声明signed或unsigned](#ID_plainNumericChar)
  - [R6.45 表示二进制数据的char指针或数组应显式声明unsigned](#ID_plainBinaryChar)
  - [R6.46 有符号变量的位域长度不应为1](#ID_singleSignedBitfield)
  - [R6.47 不应存在未被使用的本地static函数](#ID_staticNotUsed)
  - [R6.48 C\+\+代码中函数参数列表如果为空不应声明为“(void)”](#ID_superfluousVoid)
  - [R6.49 在一个语句中不应声明过多对象或函数](#ID_tooManyDeclarators)
  - [R6.50 初始化列表中的项目数量不应过多](#ID_tooManyInitItems)
  - [R6.51 局部数组的长度不应过大](#ID_unsuitableArraySize)
  - [R6.52 enum的底层类型（underlying type）中不应出现const或volatile](#ID_uselessQualifier)
  - [R6.53 拷贝赋值、移动赋值运算符不应为虚函数](#ID_virtualAssignment)
  - [R6.54 比较运算符不应为虚函数](#ID_virtualComparison)
  - [R6.55 final类中不应声明虚函数](#ID_virtualInFinal)
  - [R6.56 禁用位域](#ID_forbidBitfield)
  - [R6.57 禁止对枚举变量声明位域](#ID_forbidEnumBitfield)
  - [R6.58 禁用柔性数组](#ID_forbidFlexibleArray)
  - [R6.59 接口的参数或返回值不应被声明为void\*](#ID_forbidFunctionVoidPtr)
  - [R6.60 类成员不应被声明为void\*](#ID_forbidMemberVoidPtr)
  - [R6.61 禁用restrict指针](#ID_forbidRestrictPtr)
  - [R6.62 声明数组参数的大小时禁用static关键字](#ID_forbidStaticArrSize)
  - [R6.63 禁用可变参数列表](#ID_forbidVariadicFunction)
  - [R6.64 慎用volatile关键字](#ID_forbidVolatile)
  - [R6.65 不建议采用复杂的声明](#ID_complexDeclaration)
  - [R6.66 不建议采用复杂的参数声明](#ID_complexParameter)
  - [R6.67 不建议在using别名中定义新的类](#ID_complexUsingAlias)
<br/>

<span id="__Exception">**[7. Exception](#exception)**</span>
  - [R7.1 需确保异常的安全性](#ID_exceptionUnsafe)
  - [R7.2 异常类的构造函数或异常信息相关的函数不应抛出异常](#ID_exceptionInException)
  - [R7.3 析构函数不可抛出异常](#ID_throwInDestuctor)
  - [R7.4 与STL标准库相关的hash过程不应抛出异常](#ID_throwInHash)
  - [R7.5 对象的swap过程不可抛出异常](#ID_throwInSwap)
  - [R7.6 移动构造函数和移动赋值运算符不可抛出异常](#ID_throwInMove)
  - [R7.7 禁用含throw关键字的异常规格说明](#ID_forbidThrowSpecification)
  - [R7.8 重新抛出异常时应使用空throw表达式（throw;）](#ID_improperRethrow)
  - [R7.9 不应在catch块外使用空throw表达式（throw;）](#ID_rethrowOutOfCatch)
  - [R7.10 不应抛出过于宽泛的异常](#ID_throwGenericException)
  - [R7.11 不应抛出非异常类型的对象](#ID_throwNonExceptionType)
  - [R7.12 不应将指针作为异常抛出](#ID_throwPointer)
  - [R7.13 不应抛出NULL](#ID_throwNULL)
  - [R7.14 不应抛出nullptr](#ID_throwNullptr)
  - [R7.15 禁用C\+\+异常](#ID_forbidException)
<br/>

<span id="__Function">**[8. Function](#function)**</span>
  - [R8.1 main函数返回值的类型只应为int](#ID_mainReturnsNonInt)
  - [R8.2 函数的参数名称在声明和实现处应保持一致](#ID_inconsistentParamName)
  - [R8.3 多态类的对象作为参数时不应采用值传递的方式](#ID_paramMayBeSlicing)
  - [R8.4 不应存在未被使用的具名形式参数](#ID_paramNotUsed)
  - [R8.5 由const修饰的参数应为引用或指针](#ID_paramPassedByValue)
  - [R8.6 局部变量在使用前必须初始化](#ID_localInitialization)
  - [R8.7 成员变量须在声明处或构造时初始化](#ID_memberInitialization)
  - [R8.8 在面向构造或析构函数体的catch块中不可访问非静态成员](#ID_illMemberAccess)
  - [R8.9 成员初始化应遵循声明的顺序](#ID_disorderedInitialization)
  - [R8.10 在构造函数中不应调用虚函数](#ID_virtualCallInConstructor)
  - [R8.11 在析构函数中不应调用虚函数](#ID_virtualCallInDestuctor)
  - [R8.12 拷贝构造函数应避免复制之外的功能](#ID_sideEffectCopyConstructor)
  - [R8.13 赋值运算符应妥善处理参数就是自身对象时的情况](#ID_this_selfJudgement)
  - [R8.14 不应存在得不到执行机会的代码](#ID_unreachableCode)
  - [R8.15 有返回值的函数其所有分枝都应有明确的返回值](#ID_notAllBranchReturn)
  - [R8.16 不可返回局部对象的地址或引用](#ID_localAddressFlowOut)
  - [R8.17 函数返回值不应为const对象](#ID_returnConstObject)
  - [R8.18 返回值应与函数的返回类型相符](#ID_returnOdd)
  - [R8.19 函数返回值不应为相同的常量](#ID_returnSameConst)
  - [R8.20 基本类型的返回值不应使用const修饰](#ID_returnSuperfluousConst)
  - [R8.21 属性为noreturn的函数中不应出现return语句](#ID_unsuitableReturn)
  - [R8.22 属性为noreturn的函数返回类型只应为void](#ID_unsuitableReturnType)
  - [R8.23 不应出现多余的跳转语句](#ID_redundantJump)
  - [R8.24 函数的标签数量应在规定范围之内](#ID_tooManyLabels)
  - [R8.25 函数的行数应在规定范围之内](#ID_tooManyLines)
  - [R8.26 lambda表达式的行数应在规定范围之内](#ID_tooManyLambdaLines)
  - [R8.27 函数参数的数量应在规定范围之内](#ID_tooManyParams)
  - [R8.28 禁止goto语句向平级的或更深层的其他作用域跳转](#ID_forbidGotoBlocks)
  - [R8.29 禁止goto语句向前跳转](#ID_forbidGotoBack)
  - [R8.30 禁用goto语句](#ID_forbidGoto)
  - [R8.31 禁用setjmp、longjmp](#ID_forbidLongjmp)
  - [R8.32 避免递归实现](#ID_recursion)
  - [R8.33 不应存在重复的函数实现](#ID_functionRepetition)
<br/>

<span id="__Control">**[9. Control](#control)**</span>
  - [9.1 If](#control.if)
    - [R9.1.1 if语句不应被分号隔断](#ID_if_semicolon)
    - [R9.1.2 在if...else\-if分枝中不应有重复的条件](#ID_if_identicalCondition)
    - [R9.1.3 在if...else\-if分枝中不应有被遮盖的条件](#ID_if_hiddenCondition)
    - [R9.1.4 if分枝和else分枝的代码不应完全相同](#ID_if_identicalBlock)
    - [R9.1.5 if...else\-if各分枝的代码不应完全相同](#ID_if_identicalElseIfBlock)
    - [R9.1.6 if分枝和其隐含的else分枝的代码不应完全相同](#ID_if_identicalImplicitElseBlock)
    - [R9.1.7 没有else子句的if语句与其后续代码相同是可疑的](#ID_if_identicalSucceedingBlock)
    - [R9.1.8 if分枝和else分枝的起止语句不应相同](#ID_if_commonStatements)
    - [R9.1.9 if语句作用域的范围不应有误](#ID_if_scope)
    - [R9.1.10 如果if关键字前面是右大括号，if关键字应另起一行](#ID_if_mayBeElseIf)
    - [R9.1.11 if语句的条件不应为赋值表达式](#ID_if_assignment)
    - [R9.1.12 if语句不应为空](#ID_if_emptyBlock)
    - [R9.1.13 if...else\-if分枝数量应在规定范围之内](#ID_if_tooManyElseIf)
    - [R9.1.14 if分枝中的语句应该用大括号括起来](#ID_if_brace)
    - [R9.1.15 所有if...else\-if分枝都应以else子句结束](#ID_if_missingEndingElse)
  - [9.2 For](#control.for)
    - [R9.2.1 for语句不应被分号隔断](#ID_for_semicolon)
    - [R9.2.2 for循环中不应存在无条件的throw、break、continue或return语句](#ID_for_uncondBroken)
    - [R9.2.3 for语句作用域的范围不应有误](#ID_for_scope)
    - [R9.2.4 如果for语句没有明显的循环变量则应改为while循环](#ID_for_simplification)
    - [R9.2.5 for循环体不应为空](#ID_for_emptyBlock)
    - [R9.2.6 for循环变量不应为浮点型](#ID_for_floatCounter)
    - [R9.2.7 for循环变量不应在循环体内被改变](#ID_for_counterChangedInBody)
    - [R9.2.8 嵌套的for循环不应使用相同的循环变量](#ID_for_counterNested)
    - [R9.2.9 for循环体应该用大括号括起来](#ID_for_brace)
  - [9.3 While](#control.while)
    - [R9.3.1 while语句不应被分号隔断](#ID_while_semicolon)
    - [R9.3.2 while语句中不应存在无条件的throw、break、continue或return语句](#ID_while_uncondBroken)
    - [R9.3.3 while语句的条件不应为赋值表达式](#ID_while_assignment)
    - [R9.3.4 while语句作用域的范围不应有误](#ID_while_scope)
    - [R9.3.5 while循环体不应为空](#ID_while_emptyBlock)
    - [R9.3.6 while循环体应该用大括号括起来](#ID_while_brace)
  - [9.4 Do](#control.do)
    - [R9.4.1 注意do\-while(false)中可疑的continue语句](#ID_do_suspiciousContinue)
    - [R9.4.2 do\-while循环体不应为空](#ID_do_emptyBlock)
    - [R9.4.3 do\-while循环体应该用大括号括起来](#ID_do_brace)
    - [R9.4.4 不建议使用do语句](#ID_do_deprecated)
  - [9.5 Switch](#control.switch)
    - [R9.5.1 switch语句不应被分号隔断](#ID_switch_semicolon)
    - [R9.5.2 switch语句不应为空](#ID_switch_emptyBlock)
    - [R9.5.3 case常量的范围不可超出switch变量的范围](#ID_switch_caseOutOfRange)
    - [R9.5.4 switch语句中任何子句都应从属于某个case或default分枝](#ID_switch_invalidStatement)
    - [R9.5.5 每个case分枝应直接从属于switch结构](#ID_switch_badFormedCase)
    - [R9.5.6 不应存在紧邻default标签的空case标签](#ID_switch_uselessFallThrough)
    - [R9.5.7 不应存在内容完全相同的case分枝](#ID_switch_identicalBranch)
    - [R9.5.8 switch语句的条件变量或表达式不应为bool型](#ID_switch_bool)
    - [R9.5.9 不应使用只有default标签的switch语句](#ID_switch_onlyDefault)
    - [R9.5.10 不应使用只有一个case标签的switch语句](#ID_switch_onlyOneCase)
    - [R9.5.11 switch语句分枝数量应在规定范围之内](#ID_switch_tooManyCases)
    - [R9.5.12 switch语句应配有default分枝](#ID_switch_missingDefault)
    - [R9.5.13 每个非空的switch分枝都应该用无条件的break语句终止](#ID_switch_breakOmitted)
    - [R9.5.14 switch语句应该用大括号括起来](#ID_switch_brace)
    - [R9.5.15 switch语句不应嵌套](#ID_switch_forbidNest)
  - [9.6 Try](#control.try)
    - [R9.6.1 不应存在空的try块](#ID_try_emptyBlock)
    - [R9.6.2 catch块序列中catch\-all块（ellipsis handler）应位于最后](#ID_try_disorderedEllipsis)
    - [R9.6.3 catch块序列中针对派生类的应排在前面，针对基类的应排在后面](#ID_try_disorderedHandlers)
    - [R9.6.4 try块不应嵌套](#ID_try_forbidNest)
  - [9.7 Catch](#control.catch)
    - [R9.7.1 应通过引用捕获异常](#ID_catch_value)
    - [R9.7.2 捕获异常时不应产生对象切片问题](#ID_catch_slicing)
    - [R9.7.3 不应存在空的catch块](#ID_catch_emptyBlock)
    - [R9.7.4 捕获异常后不应直接重新抛出异常，需对异常进行有效处理](#ID_catch_justRethrow)
    - [R9.7.5 不应捕获过于宽泛的异常](#ID_catch_generic)
    - [R9.7.6 不应捕获非异常类型](#ID_catch_nonExceptionType)
<br/>

<span id="__Expression">**[10. Expression](#expression)**</span>
  - [10.1 Logic](#expression.logic)
    - [R10.1.1 不应出现不合逻辑的重复子表达式](#ID_illIdentical)
    - [R10.1.2 逻辑表达式中各子表达式不应自相矛盾](#ID_conflictCondition)
    - [R10.1.3 条件表达式不应恒为真或恒为假](#ID_invalidCondition)
    - [R10.1.4 不应使用多余的逻辑子表达式](#ID_redundantCondition)
    - [R10.1.5 逻辑表达式及其子表达式的结果不应为常量](#ID_constLogicExpression)
    - [R10.1.6 短路规则下不应存在有副作用的子表达式](#ID_shortCircuitSideEffect)
    - [R10.1.7 逻辑表达式应尽量保持简洁明了](#ID_simplifiableCondition)
    - [R10.1.8 可化简为逻辑表达式的三元表达式应尽量化简](#ID_simplifiableTernary)
  - [10.2 Calulation](#expression.calulation)
    - [R10.2.1 不应出现没有效果的语句](#ID_missingSideEffect)
    - [R10.2.2 逗号表达式的子表达式应具有必要的副作用](#ID_invalidCommaSubExpression)
    - [R10.2.3 赋值表达式中不应存在被赋值变量的自增或自减运算](#ID_confusingAssignment)
    - [R10.2.4 各子表达式的求值不应依赖特定的顺序](#ID_evaluationOrderReliance)
    - [R10.2.5 注意运算符优先级，不可产生非预期的结果](#ID_unexpectedPrecedence)
    - [R10.2.6 不在同一数组中的指针不可比较或相减](#ID_illPtrDiff)
    - [R10.2.7 bool型变量或表达式不应参与大小比较、位运算、自增自减等运算](#ID_illBoolOperation)
    - [R10.2.8 不应出现复合赋值的错误形式](#ID_illFormedCompoundAssignment)
    - [R10.2.9 &=、|=、\-=、/=、%=左右子表达式不应相同](#ID_illSelfCompoundAssignment)
    - [R10.2.10 不应使用NULL对非指针变量赋值或初始化](#ID_oddNullAssignment)
    - [R10.2.11 赋值运算符与单目运算符之间应有空格，单目运算符与变量或表达式之间不应有空格](#ID_stickyAssignmentOperator)
    - [R10.2.12 避免出现复合赋值的可疑形式](#ID_suspiciousCompoundAssignment)
    - [R10.2.13 赋值运算符左右子表达式不应重复](#ID_selfAssignment)
    - [R10.2.14 除法运算符、求余运算符左右子表达不应重复](#ID_selfDivision)
    - [R10.2.15 减法运算符左右子表达式不应重复](#ID_selfSubtraction)
    - [R10.2.16 异或运算符左右子表达式不应重复](#ID_selfExclusiveOr)
    - [R10.2.17 负号不应作用于无符号整数](#ID_minusOnUnsigned)
    - [R10.2.18 不应重复使用一元运算符](#ID_repeatedUnaryOperators)
    - [R10.2.19 常量表达式结果不应溢出](#ID_constValueOverflow)
    - [R10.2.20 位运算符不应作用于有符号整数](#ID_bitwiseOperOnSigned)
    - [R10.2.21 移位数量不可超过相关类型比特位的数量](#ID_illShiftCount)
  - [10.3 Comparison](#expression.comparison)
    - [R10.3.1 比较运算应在正确的范围内进行](#ID_illComparison)
    - [R10.3.2 不应使用==或!=判断浮点数是否相等](#ID_illFloatComparison)
    - [R10.3.3 指针不可与字符串常量直接比较](#ID_illPtrStrComparison)
    - [R10.3.4 不同类型的枚举值不应进行比较](#ID_differentEnumComparison)
    - [R10.3.5 比较运算符左右子表达式不应重复](#ID_selfComparison)
    - [R10.3.6 比较运算不应作为另一个比较运算的直接子表达式](#ID_successiveComparison)
    - [R10.3.7 不应访问填充数据](#ID_accessPaddingData)
    - [R10.3.8 不可臆断返回值的意义](#ID_strcmpNoneZeroComparison)
  - [10.4 Call](#expression.call)
    - [R10.4.1 C风格的格式化字符串与其参数的个数应严格一致](#ID_inconsistentFormatArgNum)
    - [R10.4.2 C风格的格式化字符串与其参数的类型应严格一致](#ID_inconsistentFormatArgType)
    - [R10.4.3 非基本类型的对象不应传入可变参数列表](#ID_userObjectAsVariadicArgument)
    - [R10.4.4 函数返回值不应被忽略](#ID_returnValueIgnored)
    - [R10.4.5 避免对象切片](#ID_objectSlicing)
    - [R10.4.6 不应显式调用析构函数](#ID_explicitDtorCall)
    - [R10.4.7 在C\+\+代码中禁用C风格字符串格式化方法](#ID_forbidCStringFormat)
  - [10.5 Sizeof](#expression.sizeof)
    - [R10.5.1 sizeof不应作用于有副作用的表达式](#ID_sizeof_sideEffect)
    - [R10.5.2 sizeof的结果不应与0以及负数比较](#ID_sizeof_zeroComparison)
    - [R10.5.3 对数组参数不应使用sizeof](#ID_sizeof_arrayParameter)
    - [R10.5.4 sizeof不应作用于逻辑表达式](#ID_sizeof_oddExpression)
    - [R10.5.5 被除数不应是作用于指针的sizeof表达式](#ID_sizeof_pointerDivision)
    - [R10.5.6 指针加减偏移量时计入sizeof是可疑的](#ID_sizeof_suspiciousAdd)
    - [R10.5.7 sizeof不应再作用于sizeof](#ID_sizeof_sizeof)
    - [R10.5.8 C\+\+代码中sizeof不应作用于NULL](#ID_sizeof_NULL)
    - [R10.5.9 sizeof不可作用于void](#ID_sizeof_void)
  - [10.6 Assertion](#expression.assertion)
    - [R10.6.1 断言中的表达式不应恒为真](#ID_badAssertion)
    - [R10.6.2 断言中的表达式不应有副作用](#ID_sideEffectAssertion)
    - [R10.6.3 断言中的表达式不应过于复杂](#ID_complexAssertion)
  - [10.7 Complexity](#expression.complexity)
    - [R10.7.1 表达式的运算符不应超过规定数量](#ID_complexExpression)
  - [10.8 Other](#expression.other)
    - [R10.8.1 new表达式只可用于赋值或当作参数](#ID_oddNew)
    - [R10.8.2 数组下标应为整形表达式](#ID_oddSubscripting)
    - [R10.8.3 禁用逗号表达式](#ID_forbidCommaExpression)
    - [R10.8.4 不应使用多余的括号](#ID_redundantParentheses)
<br/>

<span id="__Literal">**[11. Literal](#literal)**</span>
  - [R11.1 注意可疑的字符常量](#ID_literal_suspiciousChar)
  - [R11.2 字符常量中不可存在应转义而未转义的字符](#ID_literal_hardCodeChar)
  - [R11.3 字符串常量中不可存在应转义而未转义的字符](#ID_literal_hardCodeString)
  - [R11.4 不应使用非标准转义字符](#ID_literal_nonStandardEsc)
  - [R11.5 不同前缀的字符串常量不可连接在一起](#ID_literal_hybridConcat)
  - [R11.6 字符串常量中不应存在拼写错误](#ID_literal_misspelling)
  - [R11.7 整数或浮点数常量的后缀应使用大写字母](#ID_literal_confusingSuffix)
  - [R11.8 禁用8进制常量](#ID_literal_forbidOct)
  - [R11.9 整数或浮点数常量应使用标准后缀](#ID_literal_nonStandardSuffix)
  - [R11.10 小心遗漏逗号导致的非预期字符串连接](#ID_literal_oddConcat)
  - [R11.11 代码中不应出现magic number](#ID_literal_magicNumber)
  - [R11.12 代码中不应出现magic string](#ID_literal_magicString)
  - [R11.13 不应使用多字符常量](#ID_literal_multicharacter)
  - [R11.14 字符串常量不应超过规定长度](#ID_literal_tooLarge)
<br/>

<span id="__Cast">**[12. Cast](#cast)**</span>
  - [R12.1 避免类型转换造成的数据丢失](#ID_narrowCast)
  - [R12.2 避免向下类型转换](#ID_downCast)
  - [R12.3 指针与整数不应相互转换](#ID_ptrIntCast)
  - [R12.4 类型转换时不应去掉const、volatile等属性](#ID_qualifierCastedAway)
  - [R12.5 不应强制转换无继承关系的类型](#ID_castNoInheritance)
  - [R12.6 不应强制转换非公有继承关系的类型](#ID_castNonPublicInheritance)
  - [R12.7 多态类型与基本类型不应相互转换](#ID_castViolatePolymorphism)
  - [R12.8 不可直接转换不同的字符串类型](#ID_charWCharCast)
  - [R12.9 避免类型转换造成的指针运算错误](#ID_arrayPointerCast)
  - [R12.10 对函数以及函数指针不应进行类型转换](#ID_functionPointerCast)
  - [R12.11 向下类型转换时应使用dynamic\_cast](#ID_nonDynamicDownCast)
  - [R12.12 对new表达式不应进行类型转换](#ID_oddNewCast)
  - [R12.13 不应存在多余的类型转换](#ID_redundantCast)
  - [R12.14 可用static\_cast、dynamic\_cast完成的类型转换不可使用reinterpret\_cast](#ID_unsuitableReinterpretCast)
  - [R12.15 在C\+\+代码中禁用C风格类型转换](#ID_forbidCStyleCast)
  - [R12.16 使用reinterpret\_cast需有文档说明](#ID_forbidReinterpretCast)
<br/>

<span id="__Buffer">**[13. Buffer](#buffer)**</span>
  - [R13.1 对内存的读写应在有效的边界内进行](#ID_bufferOverflow)
  - [R13.2 数组下标不可越界](#ID_arrayIndexOverflow)
  - [R13.3 为缓冲区分配足够的空间](#ID_insufficientBuffer)
  - [R13.4 memset等函数不应作用于带有虚函数的对象](#ID_nonPODFilling)
  - [R13.5 memset等函数长度相关的参数不应有误](#ID_badLength)
  - [R13.6 memset等函数填充值相关的参数不应有误](#ID_valueOverflow)
<br/>

<span id="__Pointer">**[14. Pointer](#pointer)**</span>
  - [R14.1 不应将非零常量值赋值给指针](#ID_fixedAddrToPointer)
  - [R14.2 注意逻辑表达式内的空指针解引用](#ID_nullDerefInExp)
  - [R14.3 避免空指针解引用](#ID_nullDerefInScp)
  - [R14.4 应判断malloc等内存分配函数的返回值是否为空](#ID_nullDerefAllocRet)
  - [R14.5 用dynamic\_cast转换指针时应判断结果是否为空](#ID_nullDerefDynamicCast)
  - [R14.6 对已经被释放的指针不应再次解引用](#ID_danglingDeref)
  - [R14.7 不应使用false等布尔常量对指针赋值或初始化](#ID_oddPtrBoolAssignment)
  - [R14.8 指针不应与bool型常量比较大小](#ID_oddPtrBoolComparison)
  - [R14.9 不应使用'\\0'或L'\\0'等字符常量对指针赋值或初始化](#ID_oddPtrCharAssignment)
  - [R14.10 指针不应与char型常量比较大小](#ID_oddPtrCharComparison)
  - [R14.11 不应判断指针大于、大于等于、小于、小于等于0](#ID_oddPtrZeroComparison)
  - [R14.12 避免无效的空指针检查](#ID_invalidNullCheck)
  - [R14.13 不应重复检查指针是否为空](#ID_repeatedNullCheck)
  - [R14.14 sizeof作用于指针是可疑的](#ID_sizeof_pointer)
  - [R14.15 析构函数中不可使用delete this](#ID_this_deleteInDestructor)
  - [R14.16 禁用delete this](#ID_this_forbidDeleteThis)
  - [R14.17 不应判断this指针是否为空](#ID_this_zeroComparison)
  - [R14.18 指针在释放后应置空](#ID_missingResetNull)
  - [R14.19 不应使用常数0对指针赋值](#ID_zeroAsPtrValue)
<br/>

<span id="__Style">**[15. Style](#style)**</span>
  - [R15.1 空格应遵循统一风格](#ID_spaceStyle)
  - [R15.2 大括号应遵循统一风格](#ID_braceStyle)
  - [R15.3 在C\+\+代码中不应使用NULL，应使用nullptr](#ID_deprecatedNULL)
  - [R15.4 NULL和nullptr不应混用](#ID_mixNullptrAndNULL)
  - [R15.5 不应存在多余的分号](#ID_redundantSemicolon)
  - [R15.6 赋值表达式不应作为子表达式](#ID_assignmentAsSubExpression)<br/><br/>
## <span id="security">1. Security</span>

### <span id="ID_plainSensitiveInfo">▌R1.1 敏感数据不应以明文形式写入代码</span>

ID_plainSensitiveInfo&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

敏感数据以明文形式写入代码易造成信息泄露。  
  
示例：
```
string get_data() {
    const char* username = "abc";
    const char* password = "123456";  // Non-compliant
    return access_database(username, password);
}
```
对具有高可靠性要求的客户端软件系统，不建议保存任何敏感数据，对于必须保存敏感数据的软件系统，需要落实安全的存储机制以及相关的评审与测试。
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
    log("Messages for %s and %s", u->name, u->password);  // Non-compliant
}
```
显然，将敏感数据直接输出到界面、日志或其他外界可感知的介质中是不安全的，需避免敏感数据的有意外传，除此之外，还需要落实具体的保护措施。  
  
保护措施包括但不限于：  
 ● 避免敏感数据由内存交换到外存  
 ● 避免如除零、无效指针解引用等问题造成core dump  
 ● 应具备反调试机制，使外界无法获得进程的内部数据  
 ● 应具备反注入机制，使外界无法篡改程序的行为  
  
下面以Windows平台为例，给出屏蔽内存交换的简单示例：
```
class SecretBuf {
    size_t len = 0;
    unsigned char* buf = nullptr;

public:
    SecretBuf(size_t size) {
        auto* tmp = (unsigned char*)VirtualAlloc(
            0, size, MEM_RESERVE | MEM_COMMIT, PAGE_READWRITE
        );
        if (VirtualLock(tmp, size)) {  // The key point
            buf = tmp;
            len = size;
        } else {
            VirtualFree(tmp, 0, MEM_RELEASE);
        }
    }

   ~SecretBuf() {
       SecureZeroMemory(buf, len);     // Clear the secret content
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
SecretBuf在构造函数中通过VirtualLock将内存页面锁定在物理内存中，阻止了与外存的交换，可在一定程度上阻止恶意嗅探工具的运行，在析构函数中解除锁定并归还相关内存，解除锁定之前清除敏感数据是有必要的，否则其残留数据在解除锁定之后仍可能被交换到外存，可进一步参见ID\_unsafeCleanup。  
  
SecretBuf的使用方法如下：
```
void foo() {
    SecretBuf buf(256);
    if (buf.ptr()) {
        // ... Do something secret using buf.ptr() ...
    } else {
        // ... Handle memory error ...
    }
}
```
对于Linux等系统可参见如下有相似功能的系统API：
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
SEI CERT MEM06-CPP  
<br/>
<br/>

### <span id="ID_unsafeCleanup">▌R1.3 敏感数据在使用后应被有效清理</span>

ID_unsafeCleanup&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

及时清理不再使用的敏感数据是重要的安全措施，且应保证清理过程不会因为编译器的优化而失效。  
  
示例：
```
void foo() {
    char user[32] = {};
    char password[32] = {};
    if (get_input(user, password, 32)) {
        login(user, password);
    }
    memset(password, 0, sizeof(password));  // Non-compliant
}
```
本例调用memset覆盖敏感数据以达到清理的目的，然而敏感信息password为局部数组且memset之后没有再被引用，根据相关标准，编译器可将memset过程去掉，结果是没有得到有效清理。  
  
C11提供了memset\_s函数以避免这种问题，某些平台和库也提供了相关支持，如SecureZeroMemory、explicit\_bzero、OPENSSL\_cleanse等不会被优化掉的函数。  
  
对于C\+\+语言，可将敏感数据地址设为volatile以避免编译器的优化，再用std::fill等方法清理，如：
```
void foo() {
    ....
    volatile char  v_padding = 0;
    volatile char* v_address = password;
    std::fill_n(v_address, sizeof(password), v_padding);   // Compliant
}
```
也可以仿照std::fill\_n利用volatile关键字的特性自行实现相关功能，关于volatile关键字的特性可参见ID\_forbidVolatile。  
  
关于本规则的实际应用可参见ID\_secretLeak的相关示例。
<br/>
<br/>

#### 相关
ID_secretLeak  
ID_forbidVolatile  
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
应至少将相关成员改为私有：
```
class A {
public:
    // ... methods for accessing passwords safely
private:
    string username;
    string password;      // Compliant
};
```
对敏感数据的存贮最好对引用者完全隐藏。
<br/>
<br/>

#### 参考
CWE-766  
<br/>
<br/>

### <span id="ID_hijack">▌R1.5 预判由用户输入造成的不良后果</span>

ID_hijack&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

须对用户输入的脚本、路径、资源请求等信息进行预判，对产生不良后果的输入予以拒绝。  
  
示例：
```
Result foo() {
    Result res = sqlQuery(
        "select * from users where user='%s'", userInput()  // Non-compliant
    );
    return res;
}
```
将用户的输入直接拼入SQL脚本是不安全的，如果用户输入“xxx' or 'x'='x”一类的字符串则相当于执行的是“select \* from users where user='xxx' or 'x'='x'”，一个恒为真的条件使where限制失效，造成所有数据被返回。  
  
又如：
```
string bar() {
    string path = "/myhome/mydata/" + userInput();  // Non-compliant
    return read_file(path);
}
```
这段代码意在将用户输入限制在/myhome/mydata目录下，然而这么做是不安全的，如果用户的输入是带有“../”这种相对路径的形式，则仍可绕过限制，所以在read\_file之前应判断路径的可靠性。
<br/>
<br/>

#### 参考
CWE-89  
CWE-23  
CWE-73  
<br/>
<br/>

### <span id="ID_TOCTOU">▌R1.6 避免在一个事务中通过路径多次访问同一文件</span>

ID_TOCTOU&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

攻击者可以在两次通过路径访问同一文件的中途对该文件作手脚，从而造成不良后果。  
  
这种问题称为TOCTOU（Time\-of\-check Time\-of\-use）竞态条件。有时需要先检查文件的某种状态，如果状态满足条件的话，再使用这文件，如果“检查”和“使用”都是通过路径完成的，攻击者可以在中途将文件替换成不满足条件的文件，如将文件替换成指向另一个文件的链接，从而对系统造成破坏。  
  
示例：
```
int exists(const char* path) {
    int r = 0;
    FILE* fp = fopen(path, "r");
    if (fp) {
        r = 1;
        fclose(fp);
    }
    return r;
}

void create(const char* path) {
    if (exists(path)) {           // #1, time-of-check
        return;
    }
    FILE* fp = fopen(path, "w");  // #2, time-of-use, non-compliant
    if (fp != NULL) {
        fwrite("abc", 1, 3, fp);
        fclose(fp);
    }
}
```
示例代码先通过路径判断文件是否存在，如果存在则不作处理，如果不存在则再次通过路径创建文件并向文件写入数据。如果在\#1和\#2之间攻击者创建了一个同名的指向其他文件的链接，那么被指向的文件会遭到破坏，尤其是当进程的权限比较高时，其破坏力是难以控制的。  
  
应只通过路径打开文件对象一次，只通过文件对象操作文件：
```
void create(const char* path) {
    FILE* fp = fopen(path, "wx");  // Compliant, since C11
    if (fp != NULL) {
        fwrite("abc", 1, 3, fp);
        fclose(fp);
    }
}
```
利用“wx”模式即可保证fopen函数在文件不存在的时候执行成功，文件存在的时候执行失败。  
  
注意，目前C\+\+语言的fstream类尚无法完成与“wx”模式相同的功能，相同功能的代码要用fopen函数实现。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 7.21.5.3(3)  
<br/>

#### 参考
CWE-367  
<br/>
<br/>

### <span id="ID_dataRaces">▌R1.7 访问共享数据应遵循合理的同步机制</span>

ID_dataRaces&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

共享数据可被多个执行单位或硬件读写，需要合理控制访问的先后顺序。  
  
示例：
```
int foo() {       // Call in multiple threads
    static int id = 0;
    return id++;  // Data races
}
```
例中foo函数意在每次被调用都能返回一个不同的整数，但多个线程同时读写变量会造成混乱，对id这种函数内的静态变量处理不当是多线程相关错误的常见原因。  
  
应改为：
```
int foo() {
    static atomic<int> id(0);
    return id.fetch_add(1);  // OK
}
```
其中atomic是C\+\+标准原子类，fetch\_add将对象持有的整数增1并返回之前的值，可以保证线程对整数的操作是有序进行的，不会造成混乱。  
  
不同的访问顺序会对结果产生影响，往往意味着错误，这种问题称为“竞态条件（Race Conditon）”。
```
void bar() {
    int* p = baz();  // Points to shared data
    if (*p == 0) {   // ‘*p’ is changing
        ....
    }
    else if (*p == 1) {  // ‘*p’ is changing
        ....
    }
    else {
        ....
    }
}
```
如果指针p指向共享数据，那么bar函数中的条件分枝是不可靠的，攻击者可以通过控制共享数据实现对关键流程的劫持，所以应合理使用锁、信号量等同步手段保证共享数据的可靠性。
<br/>
<br/>

#### 参考
CWE-362  
C++ Core Guidelines CP.2  
<br/>
<br/>

### <span id="ID_unlimitedAuthority">▌R1.8 对文件设定合理的权限</span>

ID_unlimitedAuthority&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

进程创建的文件不应具有被其他用户访问的权限，而且进程之间不应直接通过文件进行通信，应实现合理的接口和交互机制。  
  
示例：
```
FILE* fp;
umask(000);  // Non-compliant
fp = fopen("bar.txt", "w");  // Old method
....
fclose(fp);
```
例中umask函数开放了所有用户对bar.txt文件的读写权限，这是不安全的，很可能遭到攻击者的窃取或篡改。  
  
由于历史原因，C语言的fopen和C\+\+语言的fstream都不能确保创建的文件只能被当前用户访问，C11提供了fopen\_s函数，C\+\+17提供了std::filesystem::permissions等方法填补了这方面的需求。  
  
fopen\_s函数简例：
```
FILE* fp;
errno_t e = fopen_s(&fp, "bar.txt", "w");  // Good
....
fclose(fp);
```
fopen\_s函数与fopen函数不同，fopen\_s函数不受umask等函数的影响，直接将文件的权限设为当前用户私有，是一种更安全的方法，建议用fopen\_s函数替代fopen函数。
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

### <span id="ID_nullACL">▌R1.9 对用户设置合理的权限</span>

ID_nullACL&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

合理设置用户的访问权限，不可不加限定。  
  
示例：
```
SECURITY_DESCRIPTOR sd;
InitializeSecurityDescriptor(&sd, SECURITY_DESCRIPTOR_REVISION);
SetSecurityDescriptorDacl(&sd, TRUE, NULL, FALSE);  // Non-compliant
```
本例使用Windows API SetSecurityDescriptorDac创建了NULL ACL，与之相关的资源可被任意用户访问，这是不安全的。
<br/>
<br/>

#### 参考
CWE-732  
<br/>
<br/>

### <span id="ID_dangerousName">▌R1.10 不应引用危险符号名称</span>

ID_dangerousName&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

弱加密、弱哈希、弱随机、不安全的协议等相关库、函数、类、宏、常量等名称不应出现在代码中。  
  
这种危险符号名称主要来自：  
 ● 低质量随机数生成算法，如srand、rand等  
 ● 不再适用的哈希算法，如MD2、MD4、MD5、MD6、RIPEMD以及SHA\-1等  
 ● 非加密协议，如HTTP、FTP等  
 ● 低版本的传输层安全协议，如TLSv1.2之前的版本  
 ● 弱加密算法，如DES、3DES等  
  
示例：
```
#include <openssl/md5.h>   // Non-compliant

const string myUrl = "http://foo/bar";  // Non-compliant, use https

void foo() {
    MD5_CTX c;       // Non-compliant
    MD5_Init(&c);    // Non-compliant
    ....
}

void bar() {
    srand(0);        // Non-compliant
    EVP_des_ecb();   // Non-compliant
    ....
}
```
可以在ID\_dangerousName一节的末尾配置关注的危险符号名称，当代码中出现了这些名称时就给出警告。  
  
配置示例：
```
[ID_dangerousName]
$open=true
srand|random_shuffle=Weak random
EVP_des_ecb|EVP_des_cbc=Weak encryption
http|ftp=Non encrypted protocol
CURL_SSLVERSION_TLSv1|TLS1_1_VERSION=Old TLS version
```
配置项等号左侧为危险符号名称，右侧为相关说明，可以指定多个名称对应一个说明，用“|”分隔。  
  
例中srand以及弱加密算法EVP\_des\_ecb、EVP\_des\_cbc等名称被设为危险名称，当代码中出现同名符号时就按相关说明给出警告。
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

### <span id="ID_dangerousFunction">▌R1.11 避免调用具有危险性的函数</span>

ID_dangerousFunction&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

某些库函数或系统API在设计和实现上本身就具有危险性，使用这种函数相当于直接将风险引入了系统。  
  
示例：
```
gets       // The most dangerous function
mktemp     // Every use of ‘mktemp’ is a security risk, use ‘mkstemp’ instead
getpass    // Unsafe and not portable
crypt      // Unsafe, exhaustive searches of the key space are possible
getpw      // It may overflow the provided buffer, use ‘getpwuid’ instead
cuserid    // Not portable and unreliable, use ‘getpwuid(geteuid())’ instead
chown      // Prone to TOCTOU race conditions, use ‘fchown’ instead
chmod      // Prone to TOCTOU race conditions, use ‘fchmod’ instead

TerminateThread    // Forced termination of a thread can cause many problems
SuspendThread      // Forced suspension of a thread can cause many problems
```
gets等函数无法检查缓冲区大小，是公认的不安全函数，TerminateThread等Windows API会强制结束线程的执行，线程持有的资源无法正确释放导致泄漏或死锁，这类函数应避免使用。  
  
可以在ID\_dangerousFunction一节的末尾配置关注的危险函数名称，当代码中出现了这些名称时就给出警告。  
  
配置示例：
```
[ID_dangerousFunction]
$open=true
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

### <span id="ID_obsoleteFunction">▌R1.12 不应调用已过时的函数</span>

ID_obsoleteFunction&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

某些库函数或系统API在设计和实现上存在缺陷并已宣布过时，应使用有更完善的替代方法。  
  
示例：
```
asctime         // use ‘strftime’ instead
bcmp            // use ‘memcmp’ instead
bcopy           // use ‘memmove’ or ‘memcpy’ instead
bsd_signal      // use ‘sigaction’ instead
gethostbyaddr   // use ‘getnameinfo’ instead
gethostbyname   // use ‘getaddrinfo’ instead
```
可以在ID\_obsoleteFunction一节的末尾配置关注的危险函数名称，当代码中出现了这些名称时就给出警告。  
  
配置示例：
```
[ID_obsoleteFunction]
$open=true
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

### <span id="ID_unsafeStringFunction">▌R1.13 禁用不安全的字符串函数</span>

ID_unsafeStringFunction&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: security warning

<hr/>

不检查缓冲区长度的字符串函数易造成运行时错误或安全漏洞。  
  
这类函数包括：
```
gets、sprintf、scanf、sscanf、fscanf、
vfscanf、vsprintf、vscanf、vsscanf、
strcpy、strcat、wcscpy、wcscat、
StrCpy、StrCpyA、StrCpyW、StrCat、StrCatA、StrCatW、
lstrcat、lstrcatA、lstrcatW、lstrcpy、lstrcpyA、lstrcpyW
```
由于历史原因，传统C语言字符串函数在设计上过于简单，要想安全地使用这些函数必须谨慎地检查缓冲区大小，否则对缓冲区合法边界之外的读写会造成运行时错误或安全漏洞。  
  
对于C代码，应改用较为安全的库函数，如用gets\_s替换gets，sprintf\_s替换sprintf。  
对于C\+\+代码，应改用STL标准库提供的相关功能。  
  
示例：
```
#include <cstring>

void foo(const char* s) {
    char arr[10];
    strcpy(arr, s);  // Non-compliant, note the length of s
    ....
}
```
要想正确使用strcpy等函数完全依赖于代码编写者的小心谨慎，这种对人为因素的依赖是不可靠的，禁用不安全的库函数才是明智的选择。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 K.3.7  
<br/>

#### 参考
CWE-119  
CWE-120  
CWE-676  
MISRA C++ 2008 18–0–5  
<br/>
<br/>

### <span id="ID_improperNullTermination">▌R1.14 确保字符串以空字符结尾</span>

ID_improperNullTermination&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

语言要求字符串以空字符结尾，程序应保证有足够的内存空间安置空字符，否则会破坏程序基本的执行机制，造成严重问题。  
  
空字符指'\\0'、L'\\0'、u'\\0'、U'\\0'，分别对应char\*、wchar\_t\*、char16\_t\*、char32\_t\*等字符串类型。  
  
示例：
```
void foo(const char* p) {
    char a[4];
    strncpy(a, p, sizeof(a));
    printf("%s\n", strupr(a));  // To upper case and print, dangerous
}
```
例示代码将字符串复制到数组中，然后转为大写并打印，strncpy函数不会在数组的结尾安置空字符'\\0'，一旦p所指字符串的长度超过3，就会导致内存访问错误。  
  
应改为：
```
void foo(const char* p) {
    char a[4] = "";                 // Initialize all to '\0'
    strncpy(a, p, sizeof(a));
    if (a[3] == '\0') {             // Right
        printf("%s\n", strupr(a));  // OK
    } else {
        ....                        // String length exceptions
    }
}
```
将所有数组元素初始化为'\\0'，调用strncpy函数后如果数组最后一个元素是'\\0'，说明输入字符串的长度符合要求，否则可做出相应的异常处理。
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

### <span id="ID_implementationDefinedFunction">▌R1.15 避免使用由实现定义的库函数</span>

ID_implementationDefinedFunction&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

由实现定义的(implementation\-defined)库函数存在语言标准之外的行为。  
  
如：  
 ● 在cstdlib或stdlib.h中声明的abort、exit、getenv或system等函数  
 ● 在ctime或time.h中声明的clock等函数  
 ● 在csignal或signal.h中声明的signal等函数  
  
这些函数的行为取决于编译器、库或环境的生产厂家，同一个函数不同的厂家会有不同的实现，故称这种函数的行为是“由实现定义”的。有高可靠性要求的软件系统应避免使用这种函数，否则需明确各种实现上的具体差异，提高了移植、发布以及兼容性等多方面的成本。  
  
示例：
```
#include <cstdlib>

void foo() {
    abort();  // Non-compliant
}
```
标准规定调用abort后进程应被终止，但进程打开的流是否会被关闭，创建的临时文件是否会被清理等问题是没被明确定义的，由库函数的具体实现定义。
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

### <span id="ID_divideByZero">▌R1.16 除数不可存在值为0的可能性</span>

ID_divideByZero&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security error

<hr/>

除数为0会导致标准未定义的错误。  
  
示例：
```
void foo() {
    size_t n = 0;
    if (condition) {
        n = bar();
    }
    return 100 / n;  // Non-compliant, must determine whether ‘n’ is 0
}
```
当除数为0时，对于整形数据的除法，进程往往会崩溃，对于浮点型数据的除法，一般会产生NaN这种无效的结果。  
  
崩溃会给用户不好的体验，而且要注意如果崩溃可由外部输入引起，会被攻击者利用从而迫使程序无法正常工作，具有高可靠性要求的服务类程序更应该注意这一点。对于客户端程序，也要防止攻击者对崩溃产生的core dump进行恶意调试，避免泄露敏感数据，程序的健壮性与安全性是紧密相关的。
<br/>
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

### <span id="ID_forbidAtox">▌R1.17 禁用atof、atoi、atol以及atoll等函数</span>

ID_forbidAtox&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: security warning

<hr/>

当字符串无法被正确转为数值时，stdlib.h或cstdlib中声明的atof、atoi、atol以及atoll等函数存在标准未定义的行为。  
  
对于C语言应改用strtof、strtol等函数，对于C\+\+语言应改用标准流转换的方式。  
  
示例：
```
cout << atoi("abcdefg") << endl;  // Non-compliant
cout << atoi("100000000000") << endl;  // Non-compliant
```
例中字符串“abcdefg”不表示数字，字符串“100000000000”超出了正常int型变量的范围，这些情况会导致标准未定义的问题。  
  
更严重的问题是无法通过这种函数判断转换是否成功，这种不确定性也意味着代码在实现上存在缺陷。  
  
C\+\+标准流转换示例：
```
int foo(const char* s) {
    int v = 0;
    std::stringstream ss(s);
    ss >> v;
    if (ss.fail()) {  // Or ‘!ss.eof() || ss.fail()’
        throw some_exception();
    }
    return v;
}
```
本例通过ss.fail()判断字符串前面的字符是否可以转为int型变量，也可以通过!ss.eof()||ss.fail()判断字符串整体是否可以转为int型变量。
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

### <span id="ID_variableFormatString">▌R1.18 格式化字符串应为常量</span>

ID_variableFormatString&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

出于可读性和安全性的考量，格式化字符串最好直接写成常量字符串的形式。  
  
本规则是ID\_hijack的特化。  
  
示例：
```
int a, b, c;
const char* fmt = foo();
....
printf(fmt, a, b, c);  // Non-compliant
```
例中格式化字符串fmt是变量，这种方式较为严重地降低了代码的可读性，而且要注意如果fmt可受外界影 响，则可能被攻击者利用造成不良后果。  
  
应将fmt改为常量：
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

### <span id="ID_addressExposure">▌R1.19 与程序实现相关的信息不可被外界感知</span>

ID_addressExposure&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

函数或对象的地址、缓冲区的地址和长度等信息不可被外界感知，否则会成为攻击者的线索。  
  
示例：
```
int foo(int* p, int n) {
    if (n >= some_value) {
        log("messages for %p and %d", p, n);  // Non-compliant
    }
}
```
这种代码多以调试为目的，应合理控制这种代码，不应将其编译到产品的正式版本中。  
  
程序采用的协议、算法以及网络结构等信息也不应暴露，如果一定要存储非用户关注的信息，需要落实可靠的加密机制。
<br/>
<br/>

#### 相关
ID_hardcodedIP  
<br/>

#### 参考
CWE-200  
<br/>
<br/>

### <span id="ID_hardcodedIP">▌R1.20 不应硬编码IP地址</span>

ID_hardcodedIP&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

字符串常量中不应存在硬编码的IP址地，不利于维护和移植，也容易暴露产品的网络结构，属于安全隐患。  
  
示例：
```
string host = "10.16.25.93";  // Non-compliant
foo("172.16.10.36:8080");     // Non-compliant
bar("https://192.168.73.90/index.html");  // Non-compliant
```
应从配置文件中获取IP地址，并配以加密措施：
```
MyConf cfg;
string host = cfg.host();     // Compliant
foo(cfg.port());              // Compliant
bar(cfg.url());               // Compliant
```
某些特殊的IP地址可以被排除：
```
0.0.0.0
255.255.255.255
127.0.0.1-127.255.255.255
```
<br/>
<br/>

#### 参考
CVE-2006-5901  
<br/>
<br/>

### <span id="ID_deprecatedErrno">▌R1.21 避免使用errno</span>

ID_deprecatedErrno&emsp;&emsp;&emsp;&emsp;&nbsp;:shield: security warning

<hr/>

正确使用errno需要注意很多繁琐的细节，极易误用。  
  
示例：
```
void foo() {
    if (somecall() == FAILED) {
        printf("somecall() failed\n");
        if (errno == SOME_VALUE) {  // ‘errno’ may have been changed by the printf
            .... 
        }
    }
}

void bar() {
    somecall0();
    somecall1();
    somecall2();
    if (errno) {  // ‘errno’ may come from any of the above functions
        ....
    }
}

void baz(const char* s) {
    errno = 0;
    int i = atoi(s);
    if (errno) {  // Invalid, ‘atoi’ has nothing to do with ‘errno’
        ....
    }
}
```
对异常情况的错误处理往往会成为务业漏洞，使攻击者轻易地实现其目的，应通过返回值或面向对象的异常机制来判断函数的执行情况。
<br/>
<br/>

#### 参考
MISRA C 2004 20.5  
MISRA C++ 2008 19-3-1  
C++ Core Guidelines E.28  
<br/>
<br/>

## <span id="resource">2. Resource</span>

### <span id="ID_resourceLeak">▌R2.1 不可失去对已分配资源的控制</span>

ID_resourceLeak&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

已分配资源的指针、句柄或描述符等信息不可被遗失，否则相关资源无法被访问也无法被回收，会导致资源耗尽以及死锁等问题，使程序无法正确运行。  
  
程序需要保证资源分配与回收之间的流程可达，且不可被异常中断，所在线程也不可在中途停止。  
  
关于内存资源，本规则特化为ID\_memoryLeak。  
  
示例：
```
void foo(const char* path) {
    FILE* p = fopen(path, "w");
    if (condition) {
        return;  // Non-compliant, ‘p’ is lost
    }
    do_something(p);
    fclose(p);
}
```
本例由局部变量p与文件对象关联，关闭文件对象之前在某种情况下函数返回，便造成了文件资源的遗失。
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

已分配内存的地址不可被遗失，否则相关内存无法被访问也无法被回收，这种问题称为“内存泄漏”，会导致可用内存被耗尽，使程序无法正确运行。  
  
程序需要保证内存分配与回收之间的流程可达，且不可被异常中断，所在线程也不可在中途停止。  
  
本规则是ID\_resourceLeak的特化。  
  
示例：
```
void foo(size_t size) {
    int* p = (int*)malloc(size * sizeof(*p));
    if (condition) {
        return;  // Non-compliant, ‘p’ is lost
    }
    do_something(p);
    free(p);
}
```
本例由局部变量p记录申请的内存空间，释放之前在某种情况下函数返回，之后便再也无法访问到这块内存空间了，造成了内存空间的遗失。
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
关于解引用已经释放的指针，特化为ID\_danglingDeref。  
关于访问未初始化的局部对象，特化为ID\_localInitialization。
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

### <span id="ID_illDealloc">▌R2.4 在栈上分配的空间以及非动态申请的资源不可被释放</span>

ID_illDealloc&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource error

<hr/>

释放在栈上分配的空间以及非动态申请的资源会导致标准未定义的错误。  
  
示例：
```
void foo(size_t size) {
    int* p = (int*)alloca(size);
    ....
    free(p);  // Non-compliant, ‘p’ should not be freed
}

void bar() {
    int i;
    free(&i);  // Non-compliant, naughty behaviour
}
```
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 7.22.3.3(2)-undefined  
ISO/IEC 9899:2011 7.22.3.4(3)-undefined  
ISO/IEC 14882:2011 3.7.4.2(4)-undefined  
<br/>
<br/>

### <span id="ID_doubleFree">▌R2.5 资源不可被重复释放</span>

ID_doubleFree&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource error

<hr/>

重复释放资源属于逻辑错误，也会导致标准未定义的问题。  
  
示例：
```
void foo(const char* path) {
    char buf[100] = {};
    FILE* p = fopen(path, "r");
    if (condition) {
        fread(buf, 1, 100, p);
        do_something(buf);
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

### <span id="ID_incompatibleDealloc">▌R2.6 资源的分配与回收方法应配套使用</span>

ID_incompatibleDealloc&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource error

<hr/>

不同的资源分配与回收函数采用不同的资源管理方式，如果不配套使用会引发严重错误。  
  
示例：
```
void foo() {
    T* p = new T;
    ....
    free(p);   // Non-compliant, use ‘delete’ instead
}

void bar(int n) {
    int* p = (int*)malloc(n * sizeof(int));
    ....
    delete[] p;  // Non-compliant, use ‘free’ instead
}
```
用new分配的资源应使用delete回收，malloc分配的应使用free回收，不可混用。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 7.22.3.3(2)-undefined  
ISO/IEC 9899:2011 7.22.3.4(3)-undefined  
<br/>

#### 参考
SEI CERT MEM51-CPP  
<br/>
<br/>

### <span id="ID_excessiveDelete">▌R2.7 用new分配的单个对象应该用delete释放，不应该用delete[]释放</span>

ID_excessiveDelete&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource error

<hr/>

用new分配的单个对象应该用delete释放，不应该用delete\[\]释放，否则引发标准未定义的错误。  
  
示例：
```
auto* p = new X;  // One object
....
delete[] p;       // Non-compliant, use ‘delete’ instead
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

### <span id="ID_insufficientDelete">▌R2.8 用new分配的数组应使用delete[]释放，不应使用delete释放</span>

ID_insufficientDelete&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource error

<hr/>

用new分配的数组不应该用delete释放，应该用delete\[\]释放，否则引发标准未定义的错误。  
  
示例：
```
void f(int n) {
    auto* p = new X[n];  // n default constructed Xs
    ....
    delete p;            // Non-compliant, use ‘delete[]’ instead
}
```
在某些环境中，可能只有数组第一个对象的析构函数被执行，其它对象的析构函数都没有被执行，如果对象与资源分配有关，则会导致资源泄漏。
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

### <span id="ID_useAfterMove">▌R2.9 对象被move之后的值不应再被使用</span>

ID_useAfterMove&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

对象被move相当于将自己的数据转让给了别的对象，move之后对象的值在逻辑上不在有效，不应再被使用。  
  
示例：
```
string fun() {
    string a = foo();
    string b = bar();
    b = std::move(a);
    return a;  // Non-compliant
}
```
<br/>
<br/>

#### 参考
SEI CERT EXP63-CPP  
<br/>
<br/>

### <span id="ID_ownerlessResource">▌R2.10 资源管理应遵循面向对象的方法</span>

ID_ownerlessResource&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

动态申请的资源应接受对象化管理，使资源的生命周期与对象的生命周期一致，从而实现资源的自动回收，这是C\+\+程序设计的重要方法。  
  
将new表达式或malloc等函数的结果直接在程序中传递是非常不安全的，极易产生泄漏甚至死锁等问题。将动态申请的资源赋值给普通变量，不受对象的构造或析构机制控制，这种资源称为“无主”资源，在C\+\+程序设计中应当避免。  
  
示例：
```
void foo(size_t size) {
    int* p = new int[size];  // Bad, ownerless
    ....                     // If any exception is thrown, or a wrong jump, leak
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

struct Y {
    int* p = nullptr;
    Y(size_t n): p(new int[n]) {}
   ~Y() { delete[] p; }
};

void baz() {
    Y y(123);   // OK, ‘y’ is the owner of the resource
    ....
}
```
例中foo和bar函数对资源的管理是不符合C\+\+理念的，baz函数中的y对象负责资源的分配与回收，称y对象具有资源的所有权，相关资源的生命周期与y的生命周期一致，有效避免了资源泄漏或错误回收等问题。  
  
资源的所有权可以发生转移，但应保证转移前后均有对象负责管理相关资源，以及在转移过程中不会产生异常。  
  
在C\+\+程序设计中应尽量使用标准库提供的容器或unique\_ptr、shared\_ptr等资源管理手段，避免new和delete等方法的显式调用。对于遵循C\+\+11之后标准的代码，建议用make\_unique函数代替new运算符。  
  
如需进一步理解资源的对象化管理方法，可参见RAII等模式。  
  
对系统API尤其是资源相关的API应进行合理封装，不应直接被业务代码引用，如：
```
void foo(const TCHAR* path) {
    HANDLE h;
    WIN32_FIND_DATA ffd;

    h = FindFirstFile(path, &ffd);  // Bad, ownerless
    ....
    CloseHandle(h);  // Is it right?
}
```
例中Windows API FindFirstFile返回资源句柄，这是一种“无主”资源，很可能被后续代码误用或遗忘，应对其进行合理的封装。
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
本例将FindFirstFile及其相关数据封装成一个类，由unique\_ptr对象保存FindFirstFile的结果，FindClose是资源的回收方法，将其作为unique\_ptr对象的组成部分，使资源可以被自动回收。
<br/>
<br/>

#### 参考
C++ Core Guidelines R.11  
C++ Core Guidelines R.12  
<br/>
<br/>

### <span id="ID_memberDeallocation">▌R2.11 对象申请的资源须在析构函数中释放</span>

ID_memberDeallocation&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

对象在析构函数中释放自己申请的资源是C\+\+程序设计的重要原则，不可被遗忘，也不应要求用户释放。  
  
示例：
```
class A
{
public:
    A(size_t n): p(new int[n]) {
    }

   ~A() {}  // Non-compliant, must delete[] p

    ....

private:
    int* p = nullptr;
};
```
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

### <span id="ID_throwInConstructor">▌R2.12 如果构造函数抛出异常需确保相关资源没有泄漏</span>

ID_throwInConstructor&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

构造函数抛出异常表示对象构造失败，不会再执行相关析构函数，需要保证已分配的资源被有效回收。  
  
示例：
```
class A {
    int* a = nullptr;
    int* b = nullptr;

public:
    A(size_t n):
        a(new int[n]),
        b(new int[n])   // If the allocation fails ...
    {}

   ~A() {
        delete[] a;
        delete[] b;
    }
};
```
如果对成员b的内存分配失败，抛出bad\_alloc异常，A的析构函数不会被执行，已分配的资源无法被正确回收，但已构造完毕的对象还是会正常析构的，所以应将资源相关的成员指针对象化，利用RAII等模式使相关资源可以被自动回收。  
  
应改为：
```
class A {
    vector<int> a, b;

public:
    A(size_t n): a(n), b(n) {}  // Safe and brief
};
```
另外，需要注意访问“未成功初始化的对象”造成的逻辑错误，如：
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
        delete p;   // Logic error, ‘p’ is nullptr
        return;
    }
    ....
    delete p;
}
```
例中T类型的对象在构造时会抛出异常，而实际上p并不会指向一个未能成功初始化的对象，赋值被异常中断，catch块中的p仍然是一个空指针，对于未能成功执行的new表达式会自动回收其分配的内存，未能成功初始化的对象在C\+\+语言中是不存在的。
<br/>
<br/>

#### 相关
ID_ownerlessResource  
ID_multiAllocation  
ID_memoryLeak  
<br/>
<br/>

### <span id="ID_forbidMallocAndFree">▌R2.13 C++代码中禁用malloc、free等内存分配与回收函数</span>

ID_forbidMallocAndFree&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: resource warning

<hr/>

malloc、free等函数是C语言的内存管理函数，在C\+\+代码中应使用C\+\+面向对象的内存管理方法。  
  
示例：
```
void foo(size_t size) {
    int* p = (int*)malloc(size * sizeof(int));  // Unsafe and verbose
    ....
    free(p);
}
```
应改为：
```
void foo(size_t size) {
    auto p = make_unique<int[]>(size);  // Safe and brief
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

### <span id="ID_unnecessaryAllocation">▌R2.14 避免不必要的内存分配</span>

ID_unnecessaryAllocation&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

对单独的基本变量或只包含少量基本变量的对象不应使用动态内存分配。  
  
示例：
```
bool* pb = new bool;  // Non-compliant
char* pc = new char;  // Non-compliant
```
内存分配的开销远大于变量的直接使用，而且还涉及到回收问题，是得不偿失的。  
  
应改为：
```
bool b = false;   // Compliant
char c = 0;       // Compliant
```
用new分配数组时方括号被误写成小括号，或使用unique\_ptr等智能指针时遗漏了数组括号也是常见笔误，如：
```
int* pi = new int(32);             // Non-compliant
auto ui = make_unique<int>(32);    // Non-compliant
```
应改为：
```
int* pi = new int[32];             // Compliant
auto ui = make_unique<int[]>(32);  // Compliant
```
有时可能需要用指针指向一个变量，而指针为空时表示这个变量“不存在”，对于这种情况不妨用变量的特殊值表示变量的状态。
<br/>
<br/>

#### 相关
ID_dynamicAllocation  
<br/>
<br/>

### <span id="ID_multiAllocation">▌R2.15 在一个语句中最多执行一次显式资源分配</span>

ID_multiAllocation&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

在一个语句中最多执行一次显式资源分配，否则某个对象的构造函数抛出异常的话，会造成内存泄漏。  
  
示例：
```
void foo(shared_ptr<T> a, shared_ptr<T> b);

void bar(int x, int y) {
    foo(
        shared_ptr<T>(new T(x)),
        shared_ptr<T>(new T(y))    // Non-compliant
    );
}
```
示例代码在一个语句中调用了两次new分配，实际执行时可以先为两个对象分配内存，再分别执行对象的构造函数，如果某个构造函数抛出异常，已分配的内存就得不到回收了。  
  
应改为：
```
void bar(int x, int y) {
    auto a = shared_ptr<T>(new T(x));  // Compliant, ‘make_shared’ is better
    auto b = shared_ptr<T>(new T(y));  // Compliant
    foo(a, b);
}
```
保证一次内存分配后执行一个构造函数可解决这种问题，如果构造函数抛出异常已分配的内存也可自动被回收。
<br/>
<br/>

#### 参考
C++ Core Guidelines R.13  
<br/>
<br/>

### <span id="ID_stackAllocation">▌R2.16 避免使用在栈上分配内存的函数</span>

ID_stackAllocation&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

alloca、\_\_builtin\_alloca等在栈上分配内存的函数难以控制失败时的情况，尤其在循环中更不应使用这种函数。  
  
示例：
```
void fun(size_t size) {
    int* p = (int*)alloca(size);  // Bad practice
    if (!p) {
        return;  // Invalid
    }
    ....
}
```
例中alloca函数在失败时往往会直接崩溃，而不会像malloc等函数返回空指针，对其返回值的检查是无效的，这种后果不可控的函数应避免使用。
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

### <span id="ID_dynamicAllocation">▌R2.17 避免动态内存分配</span>

ID_dynamicAllocation&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

对于标准库提供的动态内存分配方法，其策略和算法不在使用者的控制之内，很多细节是标准没有明确规定的，而且也是内存泄漏或耗尽等问题的根源，对于有高可靠性要求的嵌入式软件系统可酌情选取本规则。  
  
示例：
```
void foo(const char* s) {
    char* p = strdup(s);    // Non-compliant
    ....
    free(p);
}

void bar() {
    std::vector<int> v;     // Non-compliant
    ....
}
```
例中strdup函数和vector容器均使用了动态内存分配方法，是不符合本规则要求的。  
  
对于要求相对宽松的的程序，也应该尽量避免使用动态内存分配，如果能在栈上定义对象，就不应采用动态内存分配的方式。
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

### <span id="ID_copiedFILE">▌R2.18 标准FILE对象不应被复制</span>

ID_copiedFILE&emsp;&emsp;&emsp;&emsp;&nbsp;:drop_of_blood: resource warning

<hr/>

每个文件流只应对应一个FILE对象，如果存在多个副本会造成数据不一致的问题。  
  
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

## <span id="precompile">3. Precompile</span>

### <span id="precompile.include">3.1 Include</span>

### <span id="ID_nonStandardCharInHeaderName">▌R3.1.1 字母、数字、下划线以及点号之外的字符不应出现在头文件名称中</span>

ID_nonStandardCharInHeaderName&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

字母、数字、下划线以及点号之外的字符可能与文件系统造成冲突，也可能导致标准之外的问题，所以不应出现在头文件名称中。  
  
示例：
```
#include < >           // Non-compliant
#include <"foo">       // Non-compliant
#include <foo,bar>     // Non-compliant
#include <foo*>        // Non-compliant
#include <?bar?>       // Non-compliant

#include <foo>         // Compliant
#include <foo.h>       // Compliant
#include <foo_bar.h>   // Compliant
```
可以用 / 作为路径分隔符，但不应出现  // 或 \*/，  如：
```
#include <foo//bar.h>  // Non-Compliant
#include <foo/*bar.h>  // Non-Compliant
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

### <span id="ID_forbidBackslashInHeaderName">▌R3.1.2 include指令中不应使用反斜杠</span>

ID_forbidBackslashInHeaderName&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: precompile warning

<hr/>

在include指令的尖括号或引号之间使用反斜杠不利于代码移植，而且可能会导致标准之外的问题。  
  
示例：
```
#include <\r\n.h>        // Non-compliant
#include <foo\\bar.h>    // Non-compliant
#include "foo\\bar.h"    // Non-compliant

#include <foo/bar.h>     // Compliant
```
C\+\+11之前的标准指明反斜杠出现在尖括号或引号之间的行为是未定义的，C\+\+11之后则由实现定义，所以对有高可移植性要求的代码应避免使用反斜杠。
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

### <span id="ID_forbidAbsPathInHeaderName">▌R3.1.3 include指令中不应使用绝对路径</span>

ID_forbidAbsPathInHeaderName&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: precompile warning

<hr/>

绝对路径使代码过分依赖编译环境，意味着项目的编译设置不完善，应使用相对路径。  
  
示例：
```
#include "C:\\foo\\bar.h"  // Non-compliant
#include "/foo/bar.h"      // Non-compliant
```
<br/>
<br/>
<br/>

### <span id="ID_forbiddenHeader">▌R3.1.4 禁用不合规的头文件</span>

ID_forbiddenHeader&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: precompile warning

<hr/>

无意义的，行为不明确的或有不良副作用的头文件应禁用。  
  
示例：
```
#include <tgmath.h>   // Non-compliant
#include <setjmp.h>   // Non-compliant
#include <stdbool.h>  // Non-compliant in C++
```
tgmath.h和ctgmath会使用语言标准之外的技术实现某种重载效果，而且其中的部分函数名称会干扰其它标准库中的名称，setjmp和csetjmp则包含危险的过程间跳转函数。  
  
iso646.h、stdalign.h以及stdbool.h对于C\+\+语言来说没有意义，在C\+\+代码中不应使用。  
stdio.h、signal.h、time.h、fenv.h等头文件对于有高可靠性要求的软件系统也不建议使用，这些头文件含有较多标准未声明、未定义或由实现定义的内容。  
  
用户可在ID\_include一节的末尾设置不合规头文件的名称。  
  
配置示例：
```
[ID_include]
$open=true
$ID_forbiddenHeader=1
tgmath.h|ctgmath=May result in undefined behaviour
setjmp.h|csetjmp=Forbidden header
```
表示将tgmath.h、ctgmath、setjmp.h、csetjmp设为不合规头文件，如发现代码中有tgmath.h，则报告“May result in undefined behaviour”，如发现代码中有setjmp.h或csetjmp，则报告“Forbidden header”。
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

### <span id="ID_forbidCHeaderInCpp">▌R3.1.5 在C++代码中不应引用C头文件</span>

ID_forbidCHeaderInCpp&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: precompile warning

<hr/>

在C\+\+语言中应使用C\+\+标准头文件，stdio.h、stdlib.h等C语言头文件不在C\+\+标准之内，应改用cstdio、cstdlib等符合C\+\+标准的头文件。  
  
C标准头文件均有对应的C\+\+版本，C\+\+版本提供了更适合C\+\+语言的命名空间、模板以及函数重载等功能。另外，按C\+\+惯例，语言相关的标准头文件无扩展名，自定义及平台相关的以.h为扩展名，遵循统一的命名规范也有必要的。  
  
示例：
```
#include <assert.h>  // Non-compliant, use <cassert>
#include <ctype.h>   // Non-compliant, use <cctype>
#include <errno.h>   // Non-compliant, use <cerrno>
#include <float.h>   // Non-compliant, use <cfloat>
#include <limits.h>  // Non-compliant, use <climits>
#include <locale.h>  // Non-compliant, use <clocale>
#include <math.h>    // Non-compliant, use <cmath>
#include <setjmp.h>  // Non-compliant, use <csetjmp>
#include <signal.h>  // Non-compliant, use <csignal>
#include <stdarg.h>  // Non-compliant, use <cstdarg>
#include <stddef.h>  // Non-compliant, use <cstddef>
#include <stdio.h>   // Non-compliant, use <cstdio>
#include <stdlib.h>  // Non-compliant, use <cstdlib>
#include <string.h>  // Non-compliant, use <cstring>
#include <time.h>    // Non-compliant, use <ctime>
#include <wchar.h>   // Non-compliant, use <cwchar>
#include <wctype.h>  // Non-compliant, use <cwctype>
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

### <span id="precompile.macro">3.2 Macro</span>

### <span id="ID_macro_badName">▌R3.2.1 宏的命名应遵循合理的方式</span>

ID_macro_badName&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: precompile suggestion

<hr/>

宏的命名应遵循合理的方式，建议用大写字母表示宏的名称。  
  
不应出现下列情况：  
 ● 超长的名称  
 ● 单词间无大小写变化也无下划线的名称  
 ● 无意义或意义太过宽泛的名称  
 ● 有违公序良俗的名称  
  
示例：
```
#define xxxxx 8         // Bad
#define abcabcabcabc 8  // Bad
```
而且，宏的名称应采用全大写字母的形式，宏之外的名称不应采用全大写字母的形式，这样有利于区分宏与非宏名称，便于阅读和维护。
```
#define word_size 8     // Bad
#define WORD_SIZE 8     // OK
```
<br/>
<br/>

#### 配置
ID_macro/maxWordLength：连续无大小写变化的字符个数上限，超过则报出  
<br/>

#### 相关
ID_badName  
<br/>

#### 参考
C++ Core Guidelines ES.32  
C++ Core Guidelines ES.9  
<br/>
<br/>

### <span id="ID_macro_defineReserved">▌R3.2.2 禁止定义具有保留意义的宏名称</span>

ID_macro_defineReserved&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: precompile warning

<hr/>

定义与关键字或其他保留名称相同的宏，会使代码陷入难以维护的境地，也会导致标准未定义的问题。  
  
C\+\+标准指明不可重新定义的宏有：
```
__cplusplus、__DATE__、__FILE__、__ LINE__
__STDC_HOSTED__、__TIME__、
__STDC__、__STDC_MB_MIGHT_NEQ_WC__、__STDC_VERSION__
__STDC_ISO_10646__、__STDCPP_STRICT_POINTER_SAFETY__、
__STDCPP_THREADS__
```
除此之外，平台、环境、框架相关的宏也不应在代码中重新定义。  
  
示例：
```
#define __GNUC__ 1  // Non-compliant
```
只应由编译器决定  

```
#define NDEBUG 0  // Non-compliant
```
只应由项目的编译设置决定，不可在代码中写死  

```
#define assert(x) ((void)x)  // Non-compliant
```
只应由标准库实现  

```
#define new new(std::nothrow)  // Non-compliant
```
应实现为调用new(std::nothrow)的函数  
  
用户可在ID\_macro一节的末尾设置保留名称。  
  
配置示例：
```
[ID_macro]
$open=true
$ID_macro_defineReserved=1
keywordAsReserved=true
NULL|NDEBUG|EOF=Reserved name should not be defined or undefined
```
表示将NULL、NDEBUG、EOF为设为保留名称，当在代码中发现定义了相同名称的宏时则提示“Reserved name should not be redefined or undefined”。  
配置项keywordAsReserved为true表示关键字也作为保留名称，否则只认为用户设置的名称为保留名称。
<br/>
<br/>

#### 配置
详见说明  
<br/>

#### 相关
ID_macro_undefReserved  
<br/>

#### 依据
ISO/IEC 14882:2003 16.8(3)-undefined  
ISO/IEC 14882:2011 16.8(4)-undefined  
ISO/IEC 14882:2017 19.8(4)-undefined  
<br/>

#### 参考
MISRA C 2012 21.1  
MISRA C 2012 20.4  
MISRA C++ 2008 17-0-1  
<br/>
<br/>

### <span id="ID_macro_undefReserved">▌R3.2.3 禁止取消定义具有保留意义的宏名称</span>

ID_macro_undefReserved&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: precompile warning

<hr/>

取消定义（undef）具有保留意义的宏名称，会使代码陷入难以维护的境地，也会导致标准未定义的问题。  
  
C\+\+标准指明不可取消定义的宏有：
```
__cplusplus、__DATE__、__FILE__、__ LINE__
__STDC_HOSTED__、__TIME__、
__STDC__、__STDC_MB_MIGHT_NEQ_WC__、__STDC_VERSION__
__STDC_ISO_10646__、__STDCPP_STRICT_POINTER_SAFETY__、
__STDCPP_THREADS__
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
用户可在ID\_macro一节的末尾设置保留名称。  
  
配置示例：
```
[ID_macro]
$open=true
$ID_macro_undefReserved=1
keywordAsReserved=true
NULL|NDEBUG|EOF=Reserved name should not be defined or undefined
```
表示将NULL、NDEBUG、EOF为设为保留名称，当在代码中发现undef相同名称的宏时则提示“Reserved name should not be redefined or undefined”。  
配置项keywordAsReserved为true表示关键字也作为保留名称，否则只认为用户设置的名称为保留名称。
<br/>
<br/>

#### 相关
ID_macro_defineReserved  
<br/>

#### 依据
ISO/IEC 14882:2003 16.8(3)-undefined  
ISO/IEC 14882:2011 16.8(4)-undefined  
ISO/IEC 14882:2017 19.8(4)-undefined  
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

由于宏的展开只做文本上的处理，不考虑运算符优先级等问题，所以可作为子表达式的宏定义应该用括号括起来，否则很可容易产生意料之外的错误。  
  
示例：
```
#define ABS(x) (x) < 0? –(x): (x)  // Non-compliant
```
如果按如下使用方式：
```
a = ABS(a) + 1;
```
则相当于：
```
a = (a) < 0? -(a): (a) + 1;
```
这显然会造成意料之外的结果，所以ABS的定义应改为：
```
#define ABS(x) ((x) < 0? –(x): (x))  // Compliant
```
<br/>
<br/>

#### 参考
CWE-783  
MISRA C 2004 19.10  
MISRA C 2012 20.7  
<br/>
<br/>

### <span id="ID_macro_paramNotEnclosed">▌R3.2.5 宏参数在宏定义的表达式中应该用括号括起来</span>

ID_macro_paramNotEnclosed&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

由于宏的展开只做文本上的处理，不考虑运算符优先级等问题，故应将宏的参数在宏定义中用括号括起来，否则很可容易产生意料之外的错误。  
  
示例：
```
#define SUM(a, b) a + b  // Non-compliant
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

### <span id="ID_macro_stmtNotEnclosed">▌R3.2.6 由多个语句组成的宏定义应该用do-while(0)括起来</span>

ID_macro_stmtNotEnclosed&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

可以作为一条语句使用的宏，且宏包含多个并列子句时，应该用“do {”和 “} while(0)”括起来，否则易造成作用域的混乱。  
  
示例：
```
#define SWAP(a, b)\
  a ^= b; b ^= a; a ^= b    // Non-compliant
```
如果按如下使用方式：
```
if (x > y)
  SWAP(x, y);
```
展开后b ^= a; a ^= b;不在if的控制范围内，故应改为：
```
#define SWAP(a, b) {\       // Compliant
  a ^= b; b ^= a; a ^= b;\
}
```
更进一步地，建议使用do\-while(0)结构：
```
#define SWAP(a, b) do {\    // Good
  a ^= b; b ^= a; a ^= b;\
} while(0)
```
这样在使用宏时必须以分号结尾（否则无法通过编译），使宏在使用风格上与函数相同，易于阅读。
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

### <span id="ID_macro_insufficientArgs">▌R3.2.7 宏的实参个数不可小于形参个数</span>

ID_macro_insufficientArgs&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

宏的实参个数小于形参个数是不符合C/C\+\+标准的，参数个数不一致必然意味着某种错误，然而在某些编译环境下却可以通过编译（如MSVC等）。  
  
示例：
```
#define M(a, b, c)  a ## b ## c

char* foo() {
    return M("x", "y");  // Non-compliant
}
```
在早期标准中（如ISO 9899:1990）这种情况是未定义的，而后续标准对其进行了约束，但MSVC 2017等编译器不把这种问题视作编译错误，需要特别注意。
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

### <span id="ID_macro_redundantArgs">▌R3.2.8 宏的实参个数不可大于形参个数</span>

ID_macro_redundantArgs&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

宏的实参个数大于形参个数是不符合C/C\+\+标准的，参数个数不一致必然意味着某种错误，然而在某些编译环境下却可以通过编译（如MSVC等）。  
  
示例：
```
#define M(a, b, c)  a ## b ## c

char* foo() {
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

### <span id="ID_macro_sideEffectArgs">▌R3.2.9 宏参数不应有副作用</span>

ID_macro_sideEffectArgs&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

如果宏的参数有副作用，如果在宏定义中没有或多次引用到该参数，会引发非预期的错误。  
  
示例：
```
#define I(a)
#define M(a) ((a) + (a))

int foo(int& a) {
    return M(++a);  // Non-compliant
}

void bar(int& a) {
    I(a--);  // Non-compliant
}
```
例中M和I看起来像是函数调用，而展开后的结果却在意料之外。
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

### <span id="ID_macro_tooManyParams">▌R3.2.10 宏参数数量应在规定范围之内</span>

ID_macro_tooManyParams&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

宏参数数量过多意味着宏功能过于复杂，不利于调试，应改为函数。
<br/>
<br/>

#### 配置
ID_macro/maxParamCount：参数个数上限，超过则报出  
<br/>

#### 相关
ID_tooManyParams  
<br/>
<br/>

### <span id="ID_macro_misspelling">▌R3.2.11 宏名称中不应存在拼写错误</span>

ID_macro_misspelling&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: precompile suggestion

<hr/>

宏的名称不应存在拼写错误，尤其是供他人调用的宏，错误拼写会使代码的使用者对代码的质量产生疑虑，故须认真对待。  
  
示例：
```
#define FRIST(p) p->first()  // Non-compliant, should be FIRST
```
<br/>
<br/>

#### 相关
ID_misspelling  
ID_literal_misspelling  
<br/>
<br/>

### <span id="ID_macro_const">▌R3.2.12 不应使用宏定义常量</span>

ID_macro_const&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: precompile suggestion

<hr/>

宏只关注文本层面，不考虑作用域、类型、参数传递等具体语言层面的机制，极易造成意料之外的问题，建议除了作为预编译的条件，禁止宏参与程序具体的功能实现。  
  
示例：
```
namespace N {
    #define PI 3.1415
}
namespace M {
    void fun(double PI);  // Disturbed
}
```
示例代码中，宏PI干扰了其它作用域，造成代码的语法错误。
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

### <span id="ID_macro_typeid">▌R3.2.13 不应使用宏定义类型</span>

ID_macro_typeid&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: precompile suggestion

<hr/>

宏只关注文本层面，不考虑作用域、类型、参数传递等具体语言层面的机制，极易造成意料之外的问题，建议除了作为预编译的条件，禁止宏参与程序具体的功能实现。  
  
示例：
```
namespace N {
    #define MyType int
}

namespace M {
    #define MyType long
}

void foo(MyType);  // Unreliable
```
例中MyType的最终定义是long，命名空间N中的宏被M中的宏覆盖，这显然是不可靠的。 
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

### <span id="ID_macro_function">▌R3.2.14 可由函数实现的功能不应使用宏实现</span>

ID_macro_function&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: precompile suggestion

<hr/>

宏只关注文本层面，不考虑作用域、类型、参数传递等具体语言层面的机制，极易造成意料之外的问题，建议除了作为预编译的条件，禁止宏参与程序具体的功能实现。  
  
示例：
```
#define MAX(a, b) ((a) > (b)? (a): (b))

int foo(int& a, int& b) {
    return MAX(++a, ++b);  // Oops...
}
```
例中\+\+a和\+\+b随着宏的展开将被执行多次，造成意料之外的错误。
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

### <span id="ID_deprecatedOffsetof">▌R3.2.15 在C++代码中不应使用宏offsetof</span>

ID_deprecatedOffsetof&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: precompile suggestion

<hr/>

宏offsetof很难适用于具有C\+\+特性的类，易引发未定义的错误。  
  
示例：
```
#include <cstddef>

struct A {
    A();
    virtual ~A();

    int i, j;
};

int foo() {
    return offsetof(A, i);  // Non-compliant, undefined behavior
}

struct B {
    static int i;
    int j;
    int fun();
};

int bar() {
    return offsetof(B, i);  // Non-compliant, undefined behavior
}

int baz() {
    return offsetof(B, fun);  // Non-compliant, undefined behavior
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 18.1(5)  
ISO/IEC 14882:2011 18.2(4)  
ISO/IEC 14882:2017 21.2.4(1)  
<br/>
<br/>

### <span id="ID_macro_complexConcat">▌R3.2.16 在宏定义中由#修饰的参数后不应出现##</span>

ID_macro_complexConcat&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

不同编译器对\#和\#\#的优先级有不同的实现，在有可移植性要求的代码中不应嵌套使用，而且\#\#连接的单词数量不应超过两个。  
  
示例：
```
#define M0(a,b) # a ## b  // Non-compliant
#define M1(a,b,c) a ## #b ## c  // Non-compliant

#define M2(a) #a  // Compliant
#define M3(a,b) M1(a##b)  // Compliant
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

### <span id="precompile.directive">3.3 Directive</span>

### <span id="ID_missingHeaderGuard">▌R3.3.1 头文件不应缺少守卫</span>

ID_missingHeaderGuard&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

以.h或.hpp为扩展名的头文件应包含头文件守卫。  
  
示例：
```
// Header file foo.h
#ifndef LIBRARY_FOO_H
#define LIBRARY_FOO_H
....
#endif
```
例中foo.h是“Library”模块中的头文件，宏LIBRARY\_FOO\_H即可作为它的守卫，保证头文件被重复引入也不会出现问题，守卫名称不可有重复，建议守卫名称遵循“模块名\_文件名”的形式。  
  
\#pragma once指令也可作为头文件守卫，但并不是C/C\+\+的标准方式，只是多数编译器均有支持。这种方式由编译器维护一个列表，引入头文件时，如果发现文件中有\#pragma once指令就将文件路径加入列表，当这个文件再次被include时便不会加载，而宏守卫的方式仍然要对文件进行预编译，所以\#pragma once方式在编译效率上会更高一些。  
  
宏守卫用宏名区分头文件，所以不能有重复。宏的引入可以使相关设定更灵活，比如声明头文件之间的依赖或排斥关系，如果bar.h依赖foo.h，在\#include "bar.h"之前必须\#include "foo.h"，可在bar.h中设置：
```
// Header file bar.h
#ifndef LIBRARY_FOO_H
#error foo.h should be included first
#endif
```
这样如果不满足条件无法通过编译。  
  
本规则建议使用宏守卫的方式，但\#pragma once方法也是惯用写法，不妨通过配置项决定其是否合规。
<br/>
<br/>

#### 配置
ID_missingHeaderGuard/allowPragmaOnce：为true时#pragma once也可作为符合要求的头文件守卫  
<br/>

#### 参考
C++ Core Guidelines SF.8  
MISRA C 2004 19.15  
MISRA C++ 2008 16-2-3  
<br/>
<br/>

### <span id="ID_illFormedDirective">▌R3.3.2 不应出现非标准格式的预编译指令</span>

ID_illFormedDirective&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

非标准格式的预编译指令会导致标准未定义的问题。  
  
示例：
```
#define M 2

int foo() {
    int x = 0;
#ifdef M
        x = 1;
#elif            // Non-compliant
        x = M;
#endif M         // Non-compliant
    return x;
}
```
例中elif和endif指令的格式是不对的。
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 6.10(1)  
ISO/IEC 9899:2011 6.10(1)  
<br/>

#### 参考
MISRA C++ 2008 16-0-7  
MISRA C++ 2008 16-0-8  
MISRA C++ 2008 16-1-1  
<br/>
<br/>

### <span id="ID_nonStdDirective">▌R3.3.3 不应使用非标准预编译指令</span>

ID_nonStdDirective&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

使用非标准预编译指令并非是问题的正规解决方法，且易造成代码扩展或移植的隐患。  
  
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

### <span id="ID_warningDisabled">▌R3.3.4 对编译警告的屏蔽应慎重</span>

ID_warningDisabled&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: precompile suggestion

<hr/>

编译器一般允许使用预编译指令屏蔽某些编译警告，但对于反映风险或安全问题的警告不应屏蔽。  
  
示例：
```
#pragma warning(disable: 4172)  // Non-compliant
#pragma warning(disable: 4996)  // Non-compliant
```
示例代码屏蔽了Visual Studio C4172和C4996对应的警告，当局部变量的地址被返回或调用了scanf等危险函数时编译器不会给出警告，但这种警告是不应该被屏蔽的。  
  
本规则集合提到的部分问题编译器也可以给出警告，这种警告均不应被屏蔽。
<br/>
<br/>

#### 相关
ID_warningDefault  
<br/>

#### 参考
SEI CERT MSC00-C  
<br/>
<br/>

### <span id="ID_warningDefault">▌R3.3.5 代码在高级别的警告设置下编译</span>

ID_warningDefault&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: precompile suggestion

<hr/>

编译器一般允许设定编译警告的级别，级别越高报出的问题越多，也可以将警告设为错误，当有警告产生时停止编译，建议代码在高级别的警告设置下编译。  
  
应避免代码中出现\#pragma warning(default:...)等指令，这种指令将警告级别设为默认，可能与整个项目的设置不一致，如果一定要使用，应改用\#pragma warning(pop)方式。  
  
示例：
```
#pragma warning(disable:4706)
#include "somecode"
#pragma warning(default:4706)  // Non-compliant
```
示例代码在导入某些代码之前将代号为4706的警告屏蔽，之后又将其设为默认级别，首先要关注4706是否应该被屏蔽，还要关注如果将其设为默认是否与整个项目的设置有冲突。  
  
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

### <span id="precompile.comment">3.4 Comment</span>

### <span id="ID_specialComment">▌R3.4.1 关注TODO、FIXME、XXX、BUG等特殊注释</span>

ID_specialComment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

TODO、FIXME、XXX、BUG等特殊注释表示代码中存在问题，这种问题不应被遗忘，应有计划地予以解决。  
  
当存在问题时在注释中及时记录是好的编程习惯，而且记录时最好有署名和日期。  
  
示例：
```
void foo() {
    /*
     * Some plans...         // Bad, easy to forget
     */
}

void foo() {
    /* TODO:
     * Some plans...  --my name, date     // Good
     */
}
```
<br/>
<br/>

#### 参考
CWE-546  
<br/>
<br/>

### <span id="ID_nestedComment">▌R3.4.2 注释不可嵌套</span>

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
根据标准，\#1处的/\*与\#3处的\*/匹配，而\#4处的\*/处于失配状态。
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

### <span id="ID_badCommentPosition">▌R3.4.3 注释应出现在合理的位置</span>

ID_badCommentPosition&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: precompile suggestion

<hr/>

注释应出现在段落的前后或行尾，不应出现在行首或中间，否则对阅读产生较大干扰，也可能产生标准未定义的问题。  
  
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
当函数参数有默认值时，可以在函数实现时在参数声明的结尾用注释说明。
```
void foo(int i = 0);                // Declartion

void foo(int i /*= 0*/) {           // Let it go
}
```
<br/>
<br/>
<br/>

### <span id="precompile.other">3.5 Other</span>

### <span id="ID_badBackslash">▌R3.5.1 除转义字符、宏定义之外不应使用反斜杠</span>

ID_badBackslash&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: precompile warning

<hr/>

反斜杠可以用于标识转义字符，也可以用来形成“伪换行”效果，即代码换行显示但在语法意义上并没有换行，一般用于宏定义中，除此之外不应再使用反斜杠，否则没有实际意义，也可能造成某种混乱。  
  
示例：
```
#define M(x,y) if(x) {\   // Compiant
    foo(y);\              // Compiant
}

void foo() {
    if (condition1 \      // Non-compiant, meaningless
     || condition2) {
    }
}

int a\                    // Non-compliant, odd usage
b\
c = 123;

/\                        // Non-compiant, odd usage
/\ comment

void bar() {
    // comment  \         // Non-compiant, The next line is also commented out
    do_something();
}
```
<br/>
<br/>
<br/>

## <span id="global">4. Global</span>

### <span id="ID_stdNamespaceModified">▌R4.1 不可修改std命名空间</span>

ID_stdNamespaceModified&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

可以为用户定义的类型特化某些标准模板类，除此之外对std命名空间添加、修改甚至删除任何代码所导致的后果都是标准未定义的。  
  
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
例中对hash标准模板类的特化是可被允许的，但在std命名空间中添加的foo函数是不被允许的。  
  
应去掉std命名空间作用域声明，改为：
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

### <span id="ID_illFormedMain">▌R4.2 main函数不应被重载，也不应声明为inline、static或constexpr</span>

ID_illFormedMain&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

main函数作为程序的入口是一个特殊的函数，链接器需要对其特殊处理，标准规定main函数不应被重载，也不应声明为inline、static或constexpr。  
  
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
那么在C\+\+语言中可以用noexcept修饰main函数吗？标准没有明确规定，本规则也不建议这样做，不论是否使用noexcept，在main函数中抛出异常都会引起std::terminate的执行，而且这样做也不符合惯例。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.6.1(2 3)  
ISO/IEC 14882:2011 3.6.1(2 3)  
ISO/IEC 14882:2017 6.6.1(2 3)  
<br/>
<br/>

### <span id="ID_nonGlobalMain">▌R4.3 非全局命名空间中不应存在以main命名的函数</span>

ID_nonGlobalMain&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

main函数作为程序的入口是一个特殊的函数，链接器需要对其特殊处理，不应被重载也应不受C\+\+语言命名空间的限制。  
  
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

### <span id="ID_relyOnExternalObject">▌R4.4 全局对象的初始化不应依赖尚未初始化的其他对象</span>

ID_relyOnExternalObject&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

全局对象的初始化不应依赖其他源文件中定义的对象，也不应依赖在其后面定义的对象。  
  
示例：
```
extern int i;  // Defined in other translate unit
int j = i;     // Non-compliant
```
例中i是其他源文件中定义的对象，j初始化时无法保证i已被正确初始化。不同源文件全局对象初始化的顺序在标准中是不确定的（indeterminately）。  
  
又如：
```
extern int x;  // Defined after y
int y = x;     // Non-compliant
int x = 0;
```
在同一源文件中，x在y的后面定义，语言标准规定了x的初始化也将在y后执行，而y依赖x，所以y的初始化是无效的。
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

### <span id="ID_usingNamespaceInHeader">▌R4.5 不应在头文件的全局作用域中使用using directive</span>

ID_usingNamespaceInHeader&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

在头文件全局作用域中使用using directive，极易造成命名冲突，且影响范围难以控制。  
  
如果代码涉及多个命名空间，而这些命名空间中又有名称相同且功能相似的代码元素时，将造成难以排查的混乱。对于库的头文件，更应该严禁使用全局的using directive，否则造成对用户命名空间的干扰。  
  
示例：
```
// In a header file
namespace NS
{
    void foo(short);
}
using namespace NS;   // Non-compliant
using namespace std;  // Non-compliant
```
下例展示的问题是头文件不同的包含顺序竟导致调用同一函数产生了不同的行为：
```
// a.h
void foo(char);
namespace ns {
    void foo(int);
}
inline void bar() {
    foo(0);
}

// b.h
namespace ns {}
using namespace ns;

// a.cpp
#include "a.h"
#include "b.h"

void fun1() {
    bar();  // ‘bar’ calls ‘foo(char)’
}

// b.cpp
#include "b.h"
#include "a.h"

void fun2() {
    bar();  // ‘bar’ calls ‘foo(int)’
}
```
头文件a.h和b.h以不同的顺序被包含，使bar函数调用了不同的foo函数，导致这种混乱的正是b.h中的using directive。
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

### <span id="ID_anonymousNamespaceInHeader">▌R4.6 头文件中不应定义匿名命名空间</span>

ID_anonymousNamespaceInHeader&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

头文件中定义了匿名命名空间，即相当于在头文件中定义了静态数据，头文件被多个源文件包含时便会造成数据冗余。  
  
可参见ID\_staticInHeader的进一步讨论。  
  
示例：
```
// In a header file
namespace {  // Non-compliant
    void foo();
}
```
<br/>
<br/>

#### 相关
ID_staticInHeader  
<br/>

#### 参考
C++ Core Guidelines SF.21  
MISRA C++ 2008 7-3-3  
<br/>
<br/>

### <span id="ID_complexInlineFunction">▌R4.7 不应定义过于复杂的内联函数</span>

ID_complexInlineFunction&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: global suggestion

<hr/>

不适合将函数声明为内联的情况：  
 ● 行数超过指定限制  
 ● 存在循环或异常处理语句  
 ● 存在switch分枝语句    
 ● 函数存在递归实现  
  
建议内联函数的实现不要超过3个语句。
<br/>
<br/>

#### 配置
ID_function/maxInlineFunctionLineCount：内联函数行数上限，超过则报出  
<br/>

#### 参考
C++ Core Guidelines F.5  
<br/>
<br/>

### <span id="ID_definedInHeader">▌R4.8 函数不应在头文件中实现</span>

ID_definedInHeader&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

在头文件中实现的函数，如果不是内联、静态、模板函数，则可能随着头文件被包含而被引入不同的编译单元（translate\-unit）中，造成编译冲突。  
  
示例：
```
// In a header file
int foo() {            // Non-compliant, add inline or move it to a cpp file
    return 1;
}

inline int bar() {     // Compliant
    return 2;
}

constexpr int baz() {  // Compliant
    return 3;
}
```
<br/>
<br/>

#### 参考
C++ Core Guidelines SF.2  
<br/>
<br/>

### <span id="ID_nonConstNonStaticGlobalObject">▌R4.9 全局或命名空间作用域中不应存在即不是const也不是static的对象</span>

ID_nonConstNonStaticGlobalObject&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

非const全局对象破坏了面向对象的封装理念，如果必须使用全局对象，应将其限定在文件范围之内。  
  
本规则放宽了ID\_nonConstGlobalObj的要求，可酌情选取。  
  
示例：
```
int i = 0;  // Non-compliant
static int j = 0;  //  Let it go
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

### <span id="ID_nonConstGlobalObject">▌R4.10 全局或命名空间作用域中不应存在非const对象</span>

ID_nonConstGlobalObject&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

非const全局对象与类的公有数据成员一样对外部的读写没有限制，破坏了面向对象的封装理念。  
  
关于封装的讨论可参见ID\_nonPrivateData。  
  
示例：
```
char foo;         // Non-compliant
extern char bar;  // Non-compliant, worse

void baz() {
    do_something(foo, bar);
}
```
改进方法（将全局变量和与其相关的函数封装成类）：
```
class A {
public:
    void baz() {
        do_something(foo, bar);
    }
private:
    char foo;  // Compliant
    char bar;  // Compliant
};
```
如果变量foo、bar确实具有全局意义，多个文件都需要访问，不妨将其单件化：
```
A& getObject() {
    static A a;
    return a;
}
```
在其他文件中用getObject函数获取对象，再由其成员函数对foo、bar进行读写，有效实现封装理念，而且可以保证对象在使用之前被有效初始化。
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

### <span id="ID_staticAndConst">▌R4.11 全局对象不应同时被static和const关键字修饰</span>

ID_staticAndConst&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

由const关键字修饰的全局对象已具有静态属性（internal linkage），不应再用static关键字修饰。  
  
示例：
```
static const int i = 123;  // Non-compliant, drop ‘static’
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

### <span id="ID_staticInAnonymousNamespace">▌R4.12 匿名命名空间中不应使用静态声明</span>

ID_staticInAnonymousNamespace&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

匿名命名空间中的元素相当于已具有静态属性（internal linkage），不应再用static关键字修饰。  
  
示例：
```
namespace {
    static int i = 0;   // Non-compliant
    static int foo() {  // Non-compliant
        return i++;
    }
}
```
应改为：
```
namespace {
    int i = 0;   // Compliant
    int foo() {  // Compliant
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

### <span id="ID_staticInHeader">▌R4.13 头文件中不应存在由static关键字修饰的非成员对象、数组或函数</span>

ID_staticInHeader&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

头文件中由static关键字修饰的非成员对象、数组或函数，即静态数据，会在每个包含该头文件的编译单元（translate\-unit）中生成副本而造成数据冗余，如果将静态数据误用作全局数据也会造成逻辑错误。  
  
示例：
```
// In a header file
static int i = 0;   // Non-compliant

static int foo() {  // Non-compliant
    return i;
}
```
在编译每个包含例中头文件的源文件时，变量i和函数foo都会生成副本。  
  
由const或constexpr关键字修饰的常量也具有静态数据的特性，在头文件中定义常量也面对这种问题，基本类型的常量经过编译优化可以不占用存储空间（有取地址操作的除外），而对于非基本类型的常量对象或数组也不应在头文件中定义，建议采用单件模式，将其数据定义在cpp等源文件中，在头文件中定义访问这些数据的接口。  
  
如：
```
// myarr.h
using MyArr = int[256];
const MyArr& getMyArr();

// myarr.cpp
#include "myarr.h"

const MyArr& getMyArr() {
    static MyArr arr = {
        1, 2, 3, ....
    };
    return arr;
}
```
在需要用到arr的地方，调用getMyArr函数，即可获取对该数组的引用，没有任何多余的数据产生，而且可保证在使用之前被有效初始化。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 3.5(3)  
<br/>
<br/>

### <span id="ID_forbidUsingDirectives">▌R4.14 命名空间作用域中禁用using namespace std之外的using directive</span>

ID_forbidUsingDirectives&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: global suggestion

<hr/>

命名空间内的using directive相当于是对命名空间机制的破坏，易造成名称冲突。  
  
尤其是命名空间和版本控制有关时，全局的using directive很可能造成难以排查的混乱。  
  
示例：
```
using namespace std;          // Let it go
using namespace myspace0;     // Non-compliant
using namespace myspace1;     // Non-compliant

namespace myspace1 {
    using namespace myspace2  // Non-compliant
}
```
建议在函数作用域内用using declaration代替using directive：
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

### <span id="ID_usingSelf">▌R4.15 不应在命名空间中引用自身</span>

ID_usingSelf&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: global warning

<hr/>

在命名空间中引用自身属于无效代码，也可能是某种错误。  
  
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

### <span id="ID_topInlineNamespace">▌R4.16 不应定义全局inline命名空间</span>

ID_topInlineNamespace&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: global suggestion

<hr/>

定义全局inline命名空间相当于没有命名空间，应当在普通命名空间之内使用inline命令空间。  
  
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
namespace NS {
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

### <span id="ID_nameTooShort">▌R4.17 全局对象、函数、类型以及命名空间名称不应太短</span>

ID_nameTooShort&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: global suggestion

<hr/>

长度过短的全局名称极易造成与局部名称的混淆或冲突，建议全局名称长度不小于3个字符。  
  
示例：
```
const int i = 0;   // Non-compliant, name too short
typedef int t;     // Non-compliant, name too short
class A { .... };  // Non-compliant, name too short

int foo(int i) {
    return i + i;  // Confusing
}
```
为了避免混淆或冲突，名称适用的作用域范围越广，名称的长度也应该越长。可通过配置项指定最小名称长度，为0时表示忽略，详见“配置”一栏。
<br/>
<br/>

#### 配置
ID_global/ minVariableNameLength：全局对象名称长度下限，小于则报出  
ID_global/minFunctionNameLength：全局函数名称长度下限，小于则报出  
ID_global/minNameSpaceNameLength：全局命名空间名称长度下限，小于则报出  
ID_global/minTypeNameLength：全局类型名称长度下限，小于则报出  
<br/>

#### 参考
C++ Core Guidelines NL.7  
<br/>
<br/>

## <span id="type">5. Type</span>

### <span id="type.class">5.1 Class</span>

### <span id="ID_tooManyFields">▌R5.1.1 数据成员的数量应在规定范围之内</span>

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
    // It's actually the hell...
};
```
<br/>
<br/>

#### 配置
ID_class/maxFieldsCount：类数据成员的数量上限，超过则报出  
ID_union/maxFieldsCount：联合体数据成员的数量上限，超过则报出  
<br/>
<br/>

### <span id="ID_mixPublicPrivateData">▌R5.1.2 类不应即有public数据成员又有private数据成员</span>

ID_mixPublicPrivateData&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: type suggestion

<hr/>

类的设计应遵循：  
 ● 成员之间没有依赖关系，且都可以随意被读写时，则都应声明为public  
 ● 成员之间有依赖关系，或成员的状态会影响到整个对象的状态时，则都应声明为private  
否则应对类进行改造或拆分。  
  
面向对象的封装理念更倾向于将所有数据成员都设为private，由成员函数按指定逻辑控制每个成员的读写方法，以供外部访问，对代码的职责进行有效的划分，从而提高可维护性并降低风险，关于封装的进一步讨论可参见ID\_nonPrivateData。  
  
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
例外：  
常量数据成员不可被改变，所以可不受本规则约束。
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

### <span id="ID_nonPrivateData">▌R5.1.3 类的非常量数据成员均应为private</span>

ID_nonPrivateData&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: type suggestion

<hr/>

建议将类的数据成员均设为private，对外统一由成员函数提供读写方法。  
  
将类的所有接口都实现为成员函数，由成员函数按指定逻辑读写数据，可以保证对象的状态被有效的改变。良好的接口设计会对代码的职责进行合理的划分，显著提升可维护性。理想状态下，当有错误需要修正或有功能需要调整时，只改动相关接口的实现即可，调用接口的代码不需要改动，从而将改动降到最低，实现高度可维护的设计目的。  
这种设计的基础便是将数据设为private，只能由本类的成员函数访问，否则对数据的读写散落在各个模块之间，当有一处需要改动时，很难控制其影响的范围。  
  
常量数据成员不可被改变，所以可不受本规则约束。  
  
示例：
```
struct Fraction {
    int n, d;  // Bad

    double result() {
        return double(n) / d;
    }
};
```
例中成员n和d可由外部随意访问，如果d被设为0则无法进行除法运算，破坏了对象的有效性。  
  
对数据的限定以及数据之间的内在关系应由成员函数统一维护，不暴露给类的使用者，这便是面向对象的封装理念，也是C\+\+语言的核心理念之一。  
  
应改为：
```
class Fraction {
public:
    Fraction(int x, int y): n(x), d(y) {
        if (!d) {
            throw BadFraction();
        }
    }
    double result() noexcept {
        return double(n) / d;
    }
private:
    int n, d;  // Good
};
```
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

### <span id="ID_protectedData">▌R5.1.4 类的非常量数据成员不应定义为protected</span>

ID_protectedData&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: type suggestion

<hr/>

protected数据成员在派生类中仍可随意读写，破坏了封装理念。  
  
本规则是ID\_nonPrivateData的特化，关于封装的进一步讨论可参见ID\_nonPrivateData。  
  
常量数据成员不可被改变，所以可不受本规则约束。  
  
示例：
```
class A {
    ....
protected:
    int member;  // Non-compliant
};
```
应改为由接口访问：
```
class A {
    ....
protected:
    int access_member();  // Compliant
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

### <span id="ID_diamondInheritance">▌R5.1.5 对于菱形继承应将基类设为虚基类</span>

ID_diamondInheritance&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: type suggestion

<hr/>

当一个类有多个基类，而这些基类又同时继承于某个类时，就出现了“菱形继承”问题。在派生类的实例中，基类的成员将有多个不同的实例，这造成了逻辑上的重复与存储上的负担。如果一定需要“菱形继承”，建议将基类设为虚基类。  
  
示例：
```
class A {
public:
    int i = 0;
};

class B: public A {};
class C: public A {};
class D: public B, public C {};
```
在D的实例中，基类A的成员i将有两个不同的实例，故可改为：
```
class B: virtual public A {};
class C: virtual public A {};
```
需要特别注意的是，在这种继承关系下，将基类的对象地址转为子类指针会导致标准未定义的错误。
```
A a;
B* p = (B*)&a;  // Undefined behavior
```
如果一定要这么做可改用dynamic\_cast ：
```
B* p = dynamic_cast<B*>(&a);  // OK
```
编译器一般会将这种情况算作编译错误，但也有例外（较低版本的编译器），在设计类的关系时还是应该尽量避免菱形继承。
<br/>
<br/>

#### 参考
C++ Core Guidelines C.137  
<br/>
<br/>

### <span id="ID_roughTemplateAssignOperator">▌R5.1.6 带模板的赋值运算符不应覆盖拷贝或移动赋值运算符</span>

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

### <span id="ID_roughTemplateConstructor">▌R5.1.7 带模板的构造函数不应覆盖拷贝或移动构造函数</span>

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

### <span id="ID_missingCopyAssignOperator">▌R5.1.8 存在拷贝构造函数或析构函数时，不应缺少拷贝赋值运算符</span>

ID_missingCopyAssignOperator&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: type warning

<hr/>

三个紧密相关的函数：  
 1. 拷贝构造函数  
 2. 析构函数  
 3. 赋值运算符  
  
当这三个函数中的任何一个函数被定义时，其它两个函数也需要被定义，请参见ID\_missingCopyConstructor中对“Rule of three”的解释。  
  
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
应明确赋值运算符的行为：
```
class A {  // Compliant
public:
    A();
    A(const A&);
   ~A();

    A& operator=(const A&);  // Assignment operator
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

### <span id="ID_missingCopyConstructor">▌R5.1.9 存在赋值运算符或析构函数时，不应缺少拷贝构造函数</span>

ID_missingCopyConstructor&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: type warning

<hr/>

三个紧密相关的函数：  
 1. 拷贝构造函数  
 2. 析构函数  
 3. 赋值运算符  
  
当这三个函数中的任何一个函数被定义时，说明对象的复制以及资源的分配与回收需要遵循某种特定的行为，所以其它两个函数也需要被定义，否则容易造成冲突或泄漏，这条规则称为“Rule of three”。  
如果不显式定义，编译器虽然也会自动生成相关函数，但可能难以满足实际需求。  
  
请注意，当类只负责成员的组合而且不需要特殊的复制行为时，这三个函数就都不要定义，这条规则称为“Rule of zero”，在类的设计上应尽量遵循“Rule of zero”，从而达到简约代码的目的。  
  
同理，在C\+\+11中，对于：  
 1. 拷贝构造函数  
 2. 析构函数  
 3. 赋值运算符  
 4. 移动拷贝构造函数  
 5. 移动赋值运算符  
  
当这五个函数中的任何一个函数被定义时，其他四个函数也需要显示定义，这条规则称为“Rule of five”。  
  
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

### <span id="ID_missingDestructor">▌R5.1.10 存在拷贝构造函数或赋值运算符时，不应缺少析构函数</span>

ID_missingDestructor&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: type warning

<hr/>

三个紧密相关的函数：  
 1. 拷贝构造函数  
 2. 析构函数  
 3. 赋值运算符  
  
当这三个函数中的任何一个函数被定义时，其它两个函数也需要被定义，请参见ID\_missingCopyConstructor中对“Rule of three”的解释。  
  
示例：
```
class A {  // Non-compliant, missing destructor
public:
    A();
    A(const A&);
    A& operator=(const A&);
};
```
应明确析构函数的行为：
```
class A {  // Compliant
public:
    A();
    A(const A&);
    A& operator=(const A&);
   ~A();  // Destructor
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

### <span id="ID_missingVirtualDestructor">▌R5.1.11 有虚函数的基类应具有虚析构函数</span>

ID_missingVirtualDestructor&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: type warning

<hr/>

为了避免意料之外的资源泄漏，所有有虚函数的基类，都应该具有虚析构函数。  
  
当通过基类指针析构派生类对象时，如果基类没有虚析构函数，那么派生类对象的析构函数是无法被执行的，造成不易排查的资源泄漏，如：
```
class A {
public:
    A() {}
   ~A() {}  // Non-compliant, missing ‘virtual’
    virtual size_t size() = 0;
};

class B: public A {
    int* x;
    size_t n;
public:
    B(size_t s): n(s), x(new int[s]) {
    }
   ~B() {
       delete[] x;
    }
    size_t size() override {
        return n;
    }
};
```
按下列调用：
```
A* p = new B(32);
cout << p->size();  // OK, output 32
delete p;           // But only A::~A is called, B::x leaks
```
由于A的析构函数不是虚函数，所以delete p只调用了A的析构函数，导致派生类对象中的资源没有得到释放。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 12.4(7) 5.3.5(3)  
ISO/IEC 14882:2011 12.4(9) 5.3.5(3)  
ISO/IEC 14882:2017 15.4(10) 8.3.5(3)  
<br/>

#### 参考
CWE-1079  
CWE-1087  
CWE-1045  
C++ Core Guidelines C.35  
C++ Core Guidelines C.127  
<br/>
<br/>

### <span id="ID_missingMoveAssignOperator">▌R5.1.12 存在移动构造函数时，不应缺少移动赋值运算符</span>

ID_missingMoveAssignOperator&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: type warning

<hr/>

存在移动构造函数时，不应缺少移动赋值运算符，详见“Rule of five”。
<br/>
<br/>

#### 相关
ID_missingCopyAssignOperator  
<br/>

#### 参考
C++ Core Guidelines C.21  
<br/>
<br/>

### <span id="ID_missingMoveConstructor">▌R5.1.13 存在移动赋值运算符，不应缺少移动构造函数</span>

ID_missingMoveConstructor&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: type warning

<hr/>

存在移动赋值运算符，不应缺少移动构造函数，详见“Rule of five”。
<br/>
<br/>

#### 相关
ID_missingCopyConstructor  
<br/>

#### 参考
C++ Core Guidelines C.21  
<br/>
<br/>

### <span id="ID_missingExplicitConstructor">▌R5.1.14 可接受一个参数的构造函数需用explicit关键字限定</span>

ID_missingExplicitConstructor&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: type suggestion

<hr/>

为了避免意料之外的类型转换，可接受一个参数的构造函数应该用explicit关键字限定。  
  
示例：
```
class String {
public:
  String(int capacity);   // Missing ‘explicit’
  ....
};
String str = 100;   // Odd, either str is "100" or str can hold 100 chars??
```
应改为：
```
class String {
public:
  explicit String(int capacity);
  ....
};
String str(100);   // Better
```
例外：  
对于拷贝、移动构造函数不受本规则约束，如果将拷贝、移动构造函数声明为explicit则无法再按值传递参数或按值返回对象。  

```
class String {
public:
  String(const String&);  // Explicit or not depends on your design intent
  String(String&&);       // ditto
  ....
};
```
在类的接口设计中，应尽量减少隐式转换以避免不易察觉的问题。
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

### <span id="ID_missingExplicitConvertor">▌R5.1.15 重载的类型转换运算符需用explicit关键字限定</span>

ID_missingExplicitConvertor&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: type suggestion

<hr/>

为了避免意料之外的类型转换，重载的类型转换运算符需用explicit关键字限定。  
  
示例（选自C\+\+ Core Guidelines）：
```
struct S1 {
  string s;
  // ...
  operator char*() { return s.data(); }  // BAD, likely to cause surprises
};

struct S2 {
  string s;
  // ...
  explicit operator char*() { return s.data(); }
};

void f(S1 s1, S2 s2)
{
  char* x1 = s1;  // OK, but can cause surprises in many contexts
  char* x2 = s2;  // error (and that's usually a good thing)
  char* x3 = static_cast<char*>(s2); // we can be explicit (on your head be it)
}
```
一个由隐式类型转换引发的错误：
```
S1 ff();

char* g()
{
  return ff();
}
```
S1的类型转换函数被隐式调用，然而当g函数返回后，临时对象被销毁，返回的指针为无效地址。  
  
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

### <span id="ID_excessiveExplicit">▌R5.1.16 不应过度使用explicit关键字</span>

ID_excessiveExplicit&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: type warning

<hr/>

对于类的拷贝构造函数、移动构造函数以及不接受1个参数的构造函数一般不用explicit关键字限定，否则有损代码的易用性和扩展性。  
  
示例：
```
class A {
public:
    explicit A(const A&);  // In general, ‘explicit’ is not required
    explicit A(A&&);       // Ditto
    explicit A(int, int);  // Ditto
    ....
};
void foo(A);
void bar(const A&);
```
当类的拷贝、移动构造函数被explicit限定时，无法再按值传递参数或按值返回对象。  
当不接受一个参数的构造函数被explicit限定时，无法再用初始化列表定义临时对象。  
  
如下调用将无法通过编译：
```
A a(1, 2);
foo(a);       // Compile error
bar({1, 2});  // Compile error
```
<br/>
<br/>

#### 参考
C++ Core Guidelines C.46  
<br/>
<br/>

### <span id="ID_incompleteNewDeletePair">▌R5.1.17 重载的new和delete运算符应配对出现</span>

ID_incompleteNewDeletePair&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: type suggestion

<hr/>

如果重载内存的分配方式，一定要重载相应的回收方式，反之亦然，否则容易造成分配与回收的冲突。  
  
示例：
```
class A {
    void* operator new(size_t);   // Non-compliant, missing ‘operator delete’
};

class B {
    void operator delete(void*);  // Non-compliant, missing ‘operator new’
};

class C {
    void* operator new(size_t);   // Compliant
    void operator delete(void*);  // Compliant
};
```
placement\-new与placement\-delete也必须配对出现：
```
class E {
    void* operator new(size_t, bool);       // Non-compliant

    void* operator new(size_t, int, int);   // Compliant
    void operator delete(void*, int, int);  // Compliant
};
```
<br/>
<br/>

#### 参考
C++ Core Guidelines R.15  
<br/>
<br/>

### <span id="ID_unsuitableCopyAssignOperator">▌R5.1.18 抽象类禁用拷贝赋值运算符</span>

ID_unsuitableCopyAssignOperator&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: type warning

<hr/>

抽象类没有独立的对象，不应存在拷贝赋值运算符，否则赋值是不完整的。  
  
示例：
```
struct A {
    virtual ~A() = 0;
    A& operator = (const A&);  // Non-compliant
    ....
};

void foo(A& x, A& y) {
    x = y;  // Incomplete assignment
}
```
例中foo函数的参数只能是A的派生类对象，派生类对象调用基类的赋值运算符会造成数据不一致等问题。  
  
应将赋值运算符设为=delete或private：
```
struct A {
    virtual ~A() = 0;
    A& operator = (const A&) = delete;  // Compliant
    ....
};
```
使这种问题无法通过编译，可及时更换正确的方法。
<br/>
<br/>

#### 参考
MISRA C++ 2008 12-8-2  
C++ Core Guidelines C.67  
<br/>
<br/>

### <span id="ID_unsuitableStructTag">▌R5.1.19 存在构造、析构或虚函数的类不应采用struct关键字</span>

ID_unsuitableStructTag&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: type suggestion

<hr/>

为了便于区分简单结构体和具有封装或多态属性的类，建议struct关键字只用于结构体，其它情况均采用class关键字。  
  
示例：
```
struct A {     // Non-compliant
    A();
   ~A();
    int x, y;
};

struct B {     // Compliant
    int x, y;
};

class C {      // Compliant
public:
    C();
   ~C();
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

### <span id="ID_invalidAnonymousType">▌R5.1.20 不应出现无效的匿名类</span>

ID_invalidAnonymousType&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: type warning

<hr/>

函数体之外的匿名类、联合体或结构体，以及函数体之内的匿名类或结构体是没有意义的，多为遗迹代码，应当去除。  
  
示例：
```
union {  // Non-compliant, meaningless
    int a;
    long b;
};

char foo(int x)
{
    struct {  // Non-compliant, meaningless
        int a, b;
    };

    union {  // Compliant
        int i;
        char c;
    };

    i = x;
    return c;
}
```
<br/>
<br/>
<br/>

### <span id="type.enum">5.2 Enum</span>

### <span id="ID_duplicateEnumerator">▌R5.2.1 同类枚举值不应有重复</span>

ID_duplicateEnumerator&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: type warning

<hr/>

枚举值应被用作不同事物的标记，重复的枚举值往往意味着错误。  
  
示例：
```
enum Color {
    red = 1,
    yellow = 2,
    blue = 2,    // Non-compliant, see ‘yellow’
};

enum Fruit {
    apple = 1,
    pear,
    peach = 2,   // Non-compliant, see ‘pear’
    grape,
    favourite_fruit = grape,  // Non-compliant, change it to a function
};
```
<br/>
<br/>

#### 参考
C++ Core Guidelines Enum.8  
<br/>
<br/>

### <span id="ID_casualInitialization">▌R5.2.2 各枚举值全不初始化，或者只初始化第一个，或者全部初始化，不应存在其他情况</span>

ID_casualInitialization&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: type suggestion

<hr/>

声明枚举类型时，只应从下列方式中选择一种：   
 ● 全不初始化  
 ● 只初始化第一个  
 ● 全部初始化  
  
示例：
```
enum Colour {
    red,
    blue,
    green,
    yellow = 2   // Non-compliant
};
```
应改为:
```
enum Colour {
    red = 1,     // Compliant
    blue,
    green,
    yellow
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

匿名枚举声明相当于在当前作用域定义常量，然而这些常量的类型是不明确的（虽然可以转为int），而且当无法为枚举类型起一个确定的名子时，也意味着各枚举值不应聚为一类。  
  
示例：
```
enum { red = 0xFF0000, scale = 4, is_signed = 1 };  // Non-compliant
```
应改为：
```
const int red = 0xFF0000;
const short scale = 4;
const bool is_signed = true;
```
<br/>
<br/>

#### 参考
C++ Core Guidelines Enum.6  
<br/>
<br/>

### <span id="ID_forbidUnscopedEnum">▌R5.2.4 用enum class取代enum</span>

ID_forbidUnscopedEnum&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: type suggestion

<hr/>

传统C枚举没有有效的类型和作用域控制，极易造成类型混淆和名称冲突，在C\+\+语言中建议改用enum class。  
  
示例：
```
enum E {     // Non-compliant
    e0 = 0,
    e1 = 1,
    e2 = -1
};

E foo() {
    if (cond1) {
        return e0;
    } else if (cond2) {
        return e1;
    } else {
        return e2;
    }
}

void bar() {
    if (foo()) {  // ‘e1’ or ‘e2’??
        ....
    }
}
```
传统C枚举值与int等类型可以随意转换，造成了太多的不确定因素，例中如果e0和e2表示某种错误情况，e1表示正确情况，那么bar函数中对foo返回值的判断就是错误的，这是实际代码编写中非常常见的问题，C\+\+11提出了enum class的概念加强了类型检查，提倡在新项目中应尽量使用enum class。  
  
应改为：
```
enum class E {  // Compliant
    e0 = 0,
    e1 = 1,
    e2 = -1
};

void bar() {
    if (foo() == E::e1) {  // OK
        ....
    }
    if (foo()) {  // Compile error, cannot cast the enum class casually
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
  
C\+\+98/03禁止具有拷贝构造函数或析构函数的对象出现在联合体中，C\+\+11解除了这条禁令，但在语言层面上不保障正确性，相当于把问题抛给了用户。  
  
示例：
```
union U {
    int i;
    std::string s;  // Non-compliant

    U(int x): i(x) {
    }
    U(const char* x) {
        new(&s) std::string(x);
    }
   ~U() {
        s.~string();
    }
};

U u(1);
u.s = "abc";  // No error, no warning, just crash
```
示例代码在某些环境中会崩溃，原因是没能正确区分对象当前持有的类型，执行了错误的构造或析构过程。  
正确的做法是在类中用一个成员变量记录当前持有的类型，再将匿名union和类的构造函数以及析构函数相关联，从而根据当前持有的类型正确地初始化或销毁对象。
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
  
代码中不应出现：  
 ● 在命名空间作用域内定义的联合体  
 ● 在类中定义的具有public访问权限的联合体  
  
示例：
```
union U {  // Non-compliant
    int a, b;
};

class A {
public:
    union {  // Non-compliant
        int a, b;
    };
};

class B {
public:
    // ... methods about union
private:
    union {  // Compliant
        int a, b;
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
 ● 无法只通过对象获取当前有效的成员  
 ● 访问不同的成员相当于不安全的类型转换  
 ● 对非基本类型的成员造成构造和析构的混乱  
 ● 不能作为基类  
  
这些问题在本质上是对类型理念的破坏，面向对象的程序设计应避免使用联合体。  
  
示例：
```
union U {  // Non-compliant
    int i;
    char c;
};

U u;
u.i = 1000;
cout << u.c << endl;  // Equivalent to a cast without any restrictions
```
对联合体的使用也相当于一种没有限制的强制类型转换，在C\+\+中建议用std::variant或std::any取代联合体：
```
std::variant<int, char> u;
u = 123;
cout << get<int>(u) << endl;   // OK
cout << get<char>(u) << endl;  // Throw ‘std::bad_variant_access’
```
std::variant可以有效记录对象当前持有的类型，如果以不正确的类型访问对象会及时抛出异常。  
  
本规则比ID\_forbidNakedUnion更严格，针对所有联合体。
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

### <span id="ID_badName">▌R6.1 命名空间、类型、对象、函数的命名应遵循合理的方式</span>

ID_badName&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

代码中的各种名称应遵循易于读写，并可准确表达代码意图的方式。  
  
不应出现：  
 ● 超长的名称  
 ● 单词间无大小写变化也无下划线的名称  
 ● 无意义或意义太过宽泛的名称  
 ● 有违公序良俗的名称  
  
示例：
```
namespace xxxxx  // Bad, no meaning
{
    void fun(int);  // Bad, vague

    class CFriend {
    public:
        bool m_bVarietyisthespiceoflife = true;  // Bad, hard to read
    };
}
```
建议名称的字符数量不要超过31位。
```
extern int identifier_of_a_very_very_long_name_1;
extern int identifier_of_a_very_very_long_name_2;   // Dangerous
```
需要注意的是，如果两个名称有相同的前缀，而且相同前缀超过一定长度时是危险的，很可能导致编译器无法有效区分相关名称，造成标准未定义的问题。  
C语言标准指明，保证名称前31位不同即可避免这种问题，可参见ISO/IEC 9899:2011 5.2.4.1的相关规定。  
  
不建议采用相同“长前缀”\+不同“短后缀”的命名方式，这种名称非常容易形成笔误或由复制粘贴造成错误，如：
```
struct BinExpr {
    BinExpr* sub0;   // Bad
    BinExpr* sub1;   // Bad
};
```
设BinExpr是二元“表达式”类，sub0、sub1为左右子表达式，这种命名方式应改进：
```
struct BinExpr {
    BinExpr* left;   // Better
    BinExpr* right;  // Better
};
```
不同实体或概念的名称不应过于相似。
<br/>
<br/>

#### 配置
ID_declaration/maxWordLength：连续无大小写变化的字符个数上限，超过则报出  
<br/>

#### 依据
ISO/IEC 9899:1999 5.2.4.1(1)  
ISO/IEC 9899:1999 6.4.2.1(6)-undefined  
ISO/IEC 9899:2011 5.2.4.1(1)  
ISO/IEC 9899:2011 6.4.2.1(6)-undefined  
<br/>

#### 参考
C++ Core Guidelines NL.19  
MISRA C 2004 5.1  
MISRA C 2012 5.1  
<br/>
<br/>

### <span id="ID_reservedName">▌R6.2 不应定义具有保留意义的名称</span>

ID_reservedName&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

命名空间、类型、对象、函数等名称不应与关键字名称、系统或标准库中的名称重复，否则极易造成阅读和维护上的困扰。  
  
示例：
```
#include <errno.h>

struct A {
    void foo() {
        if (errno != 0) {  // Which errno?
            bar();
        }
    }
private:
    int errno;  // Non-compliant
};
```
例中成员变量errno与标准库中的errno名称重复，导致在阅读成员函数代码时无法区分该变量到底是自定义的还是系统定义的，造成不必要的困扰。
<br/>
<br/>
<br/>

### <span id="ID_misspelling">▌R6.3 命名空间、类型、对象、函数的名称中不应存在拼写错误</span>

ID_misspelling&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

代码中不应存在拼写错误，尤其是供他人调用的代码，如命名空间名称、类的公有成员名称，全局函数名称等，更不应存在拼写错误。  
  
错误拼写会使代码的使用者对代码的质量产生疑虑，而且这种代码被大量引用后也不便于改正。  
  
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

### <span id="ID_missingStar">▌R6.4 用auto声明指针或引用时应显式标明*、&等符号</span>

ID_missingStar&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

用auto声明指针时显式标明\*号有利于提高代码可读性，否则会使人误以为是某种非指针的对象。在声明引用时必须显式标明&号或&&号，否则成为对象声明，导致逻辑错误或造成不必要的复制开销。  
  
示例：
```
int* foo();
int& bar();

auto p = foo();   // Bad
auto* q = foo();  // Good

auto r = bar();   // Becareful, ‘r’ is not a reference
```
<br/>
<br/>
<br/>

### <span id="ID_badQualifierPosition">▌R6.5 const、volatile限定类型时应出现在左侧</span>

ID_badQualifierPosition&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

语言允许const、volatile出现在类型名的左侧，也可以出现在其右侧，甚至可以出现在基本类型名的中间，为了提高可读性，应对其位置进行统一规范。  
  
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
const、volatile出现在类型名右侧时，易与\*号造成误解，如：
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

### <span id="ID_badSpecifierPosition">▌R6.6 inline、virtual、static、typedef等关键字应出现在类型名的左侧</span>

ID_badSpecifierPosition&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

语言允许inline、virtual、static、typedef等关键字可以出现在类型名的左侧，也可以出现在其右侧，甚至可以出现在基本类型名的中间，为了提高可读性，应对其位置进行统一规范。  
  
示例：
```
struct A {
    long long typedef ll_t;     // Non-compliant
    bool static foo();          // Non-compliant
    char friend bar();          // Non-compliant
    unsigned inline int baz();  // Non-compliant
};
```
应统一规定其出现在类型名的左侧：
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
inline、virtual、explicit
register、static、thread_local、extern、mutable
friend、typedef、constexpr
```
对于const和volatile也建议出现在类型名的左侧。
<br/>
<br/>

#### 相关
ID_sandwichedModifier  
ID_badQualifierPosition  
<br/>
<br/>

### <span id="ID_constLiteralReference">▌R6.7 对常量的定义不应为引用</span>

ID_constLiteralReference&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

虽然C\+\+语言十分灵活，可以通过多种方式达到同一种目的，但应该选择最简洁且通俗易懂的方式实现。  
  
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

### <span id="ID_deprecatedAutoPtr">▌R6.8 避免使用std::auto_ptr</span>

ID_deprecatedAutoPtr&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

std::auto\_ptr在C\+\+11标准中已被废弃，应使用std::unique\_ptr。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 D.10-deprecated  
<br/>

#### 参考
C++ Core Guidelines R.20  
<br/>
<br/>

### <span id="ID_deprecatedDefaultArgument">▌R6.9 不建议虚函数的参数有默认值</span>

ID_deprecatedDefaultArgument&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

虚函数参数的默认值不受多态规则控制，通过基类指针或引用调用派生类重写的虚函数时，默认值仍采用基类中的定义，易造成混淆，建议虚函数参数不使用默认值。  
  
示例：
```
class A {
public:
    virtual int foo(int i = 0);  // Bad
};
```
应尽量去掉默认参数值，或改用重载函数的方式：
```
class A {
public:
    virtual int foo();  // OK
    virtual int foo(int i);  // OK
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

### <span id="ID_deprecatedSpecifier">▌R6.10 不应使用已过时的关键字</span>

ID_deprecatedSpecifier&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

根据C\+\+11标准，register等关键字已过时，不应再使用。另外，auto关键字也不应再作为存储类说明符（storage class specifier）继续使用。  
  
示例：
```
class A {
public:
    register int a;  // Non-compliant
    auto int b;      // Non-compliant
    int foo(register int x);  // Non-compliant
};
```
本规则对于C\+\+适用，对于C可适当放宽要求。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 D.2(1)-deprecated  
ISO/IEC 14882:2011 7.1.6.4  
<br/>
<br/>

### <span id="ID_duplicatedName">▌R6.11 同一命名空间内的类型名称不应与对象或函数名称相同</span>

ID_duplicatedName&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

如果不同的代码元素使用相同的名称，极易造成困扰。  
  
示例：
```
struct A {
    int a, b, c;
};

enum {
    A, B, C    // Non-compliant
};

size_t x = sizeof(A);  // What is ‘A’?
```
例中类名A与枚举值A重名，sizeof(A)的意义是非常令人困惑的。
<br/>
<br/>

#### 参考
MISRA C++ 2008 2-10-6  
C++ Core Guidelines ES.8  
<br/>
<br/>

### <span id="ID_exceededBitfield">▌R6.12 位域长度不应超过类型约定的大小</span>

ID_exceededBitfield&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

位域长度不应超过类型约定的大小，否则没有意义且会造成不必要的空间浪费，出现这种问题往往意味着设计上的错误。  
  
C语言规定位域长度不可超过类型约定的大小否则不可编译，而C\+\+语言规定可以超过，但超过的部分会作为“padding bits”不参与数据的存储。  
  
示例：
```
struct A {
    int x: 50;  // Non-compliant
};

int main() {
    A a;
    std::cout << sizeof(a) << std::endl;  // What is output?
}
```
输出8  
例中成员x自身的位域长度仍为32，而多出来的大小会形成一个不可访问的填充值。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 6.7.2.1(4)  
ISO/IEC 14882:2011 9.6(1)  
<br/>
<br/>

### <span id="ID_hideLocal">▌R6.13 局部名称不应被覆盖</span>

ID_hideLocal&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

如果下层作用域中的名称与上层作用域中的名称相同，会对可读性造成较大干扰，极易产生误解。  
  
示例：
```
int foo() {
    int i = 0;  // Declares an object ‘i’
    if (cond) {
        int i = 1;  // Non-compliant, hides previous ‘i’
        return bar(i);
    }
    return i;
}
```
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

### <span id="ID_hideMember">▌R6.14 成员名称不应被覆盖</span>

ID_hideMember&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

如果成员函数内的局部名称与成员名称相同，会对可读性造成较大干扰，极易产生误解。  
  
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
建议成员变量都遵循统一的命名约定，如以“\_”或“m\_”开头，可有效规避这类问题：
```
class A {
    int _i = 0;         // Member object ‘_i’

public:
    int foo() {
        int i = 0;      // OK
        return bar(i);
    }

    int bar(int i) {    // OK
        return _i + i;
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

### <span id="ID_inaccessibleTmpObject">▌R6.15 不应产生不受控制的无效临时对象</span>

ID_inaccessibleTmpObject&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: declaration error

<hr/>

无名不受控制的临时对象在构造之后会立即析构，在逻辑上没有意义，往往意味着错误。  
  
示例：
```
class A {
public:
    A() {
        A(0);  // Non-compliant, just created an inaccessible temporary object
    }

    A(int x): a(x)
    {}

private:
    int a;
};
```
例中A(0);只是生成了一个无用的临时对象，各成员并没有被正确初始化。如果要调用A::A(int)来完成初始化，应改为this\->A::A(0);的形式。  
  
又如：
```
void fun() {
    LockGuard();   // Meaningless
    do_something_critical();
} 
```
设LockGuard是某种锁的类名，那么LockGuard();一行只生成了一个临时对象，该对象会立即析构，起不到作用，这也是一种常见的错误。  
  
应改为：
```
void fun() {
    LockGuard guard;  // Compliant
    do_something_critical();
}
```
<br/>
<br/>

#### 参考
CWE-665  
C++ Core Guidelines ES.84  
<br/>
<br/>

### <span id="ID_inconsistentDefaultArgument">▌R6.16 虚函数参数的默认值应与基类中声明的一致</span>

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
cout << p->foo() << endl;  // What is output?
```
输出1，这种虚函数的非多态行为是非常令人困惑的。
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

### <span id="ID_inlineRedundant">▌R6.17 不应使用多余的inline关键字</span>

ID_inlineRedundant&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

constexpr关键字修饰的函数已经相当于被声明为inline，不应再重复声明。  
  
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
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 7.1.5(2)  
<br/>
<br/>

### <span id="ID_invalidExternSpecifier">▌R6.18 extern关键字不应作用于类成员的声明或定义</span>

ID_invalidExternSpecifier&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

extern关键字作用于类成员的声明或定义是没有意义的，为语言用法错误。  
  
示例：
```
class A {
    void foo();
};

extern void A::foo() {  // Non-compliant, drop ‘extern’
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

### <span id="ID_invalidFinal">▌R6.19 不应将union设为final</span>

ID_invalidFinal&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

标准规定union不可作为基类，所以将union声明为final是没有意义的，为语言运用错误。  
  
示例：
```
union U final  // Non-compliant, meaningless
{
    int a, b, c;
};
```
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 9.5(2)  
<br/>
<br/>

### <span id="ID_invalidLocalDeclaration">▌R6.20 不应出现不会被用到的局部声明</span>

ID_invalidLocalDeclaration&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

不会被用到的局部声明是没有意义的，往往意味着笔误或者代码功能不完整。  
  
示例：
```
int foo(int a, int b) {
    if (a)
        int b = a + 1;  // Non-compliant
    return b;
}
```
例中if作用域中的最后一个元素是对象的声明，是没有意义的，这种情况也是较为常见的笔误。  
  
应改为：
```
int foo(int a, int b) {
    if (a) {
        b = a + 1;  // OK
    }
    return b;
}
```
<br/>
<br/>
<br/>

### <span id="ID_invalidParamArraySize">▌R6.21 不应将数组作为函数的形式参数</span>

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
在C\+\+语言中可改为数组的引用：
```
void foo(int (&a)[100]);   // Compliant

template <size_t size>
void foo(int(&a)[size]) {  // Compliant
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

### <span id="ID_invalidSpecifier">▌R6.22 不应出现无效的声明限定符</span>

ID_invalidSpecifier&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

只定义类型，不定义对象时不应使用typedef、static等声明限定符，这种情况多为笔误或遗迹代码，应当去除。  
  
示例：
```
static class A;    // Non-compliant
typedef struct B;  // Non-compliant
const union C;     // Non-compliant
```
<br/>
<br/>
<br/>

### <span id="ID_label_unused">▌R6.23 不应存在没有用到的标签</span>

ID_label_unused&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

没有用到的标签意味着goto语句的缺失，也可能是残留代码，应当去除。  
  
示例：
```
int foo() {
    int i = 0;
lab:               // Non-compliant
    return bar(i);
}
```
标签只应与goto语句对应，不可有其他用途，如：
```
int bar(int x) {
    int i = 0;
strange_comment:   // Non-compliant
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

### <span id="ID_lambdaOutOfFunction">▌R6.24 不建议在函数作用域外将lambda表达式定义为具名对象</span>

ID_lambdaOutOfFunction&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

不建议在函数作用域外将lambda表达式定义为具名对象，应采用普通函数定义的方式。  
  
示例：
```
namespace N {
    auto foo = [](int a, int b) {  // Non-compliant
        return a + b;
    };
}
```
这种写法与lambda表达式的匿名特性相矛盾，而且foo是不能被重载的。  
  
应改为常规函数：
```
namespace N {
    int foo(int a, int b) {  // Compliant
        return a + b;
    }
}
```
虽然C\+\+语言十分灵活，可以通过多种方式达到同一种目的，但应该选择最简洁且通俗易懂的方式实现。
<br/>
<br/>
<br/>

### <span id="ID_missingConst">▌R6.25 指向常量字符串的指针应有const限定符修饰</span>

ID_missingConst&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

定义指向常量字符串的指针时应有const限定符修饰，否则常量字符串被修改会导致标准未定义的问题。  
  
示例：
```
char* p = "a const string";  // Non-compliant
....
p[x] = '\0';   // Undefined
```
应改为：
```
const char* p = "a const string";  // Compliant
....
p[x] = '\0';   // Compile-time protected
```
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

### <span id="ID_missingExplicitOverride">▌R6.26 所有重写的虚函数都应声明为override或final</span>

ID_missingExplicitOverride&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

将重写的虚函数都声明为override或final，可明显提升代码可读性，并可确保虚函数被有效重写。  
  
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
如果B重写A中的foo和bar两个虚函数，可以按以上代码声明，但如果不看A的声明，无法确定B中的foo和bar是对基类虚函数的重写，也看不出bar是否是一个新的虚函数。  
  
如果改为：
```
class B: public A {
    int foo() override;  // Compliant
    int bar() override;  // Compliant
};
```
则会清晰很多，而且当重写的函数名、参数或返回值与基类声明不符时，不能通过编译，可及时修正问题。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 10.3(4 5)  
<br/>

#### 参考
C++ Core Guidelines C.128  
<br/>
<br/>

### <span id="ID_missingParamName">▌R6.27 函数原型声明中的参数应具有合理的名称</span>

ID_missingParamName&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

参数名称可以对参数的用途起到最直接的说明作用，建议对外公开的接口参数都要有合理的名称。  
  
示例：
```
char* strstr(const char* haystack, const char* needle);  // Good
```
这是标准库函数strstr的原型声明，利用形象的比喻，表示要在haystack中查找needle。  
  
如果将声明改为如下形式，是令人费解的：
```
char* strstr(const char*, const char*);  // Bad
char* strstr(const char* a, const char* b);  // Bad
```
<br/>
<br/>

#### 参考
MISRA C 2004 16.3  
MISRA C 2012 8.2  
<br/>
<br/>

### <span id="ID_missingVoid">▌R6.28 C代码中函数参数列表如果为空须声明为“(void)”</span>

ID_missingVoid&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

在C语言中，如果函数的参数列表声明为空的括号，表示函数的参数还没有声明，而不是表示没有参数。这便引入了相当大的不确定性，为了减少混乱，没有参数的参数列表应声明为“(void)”。  
  
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
例中foo(1)和foo()两种调用都可以通过编译，然而声明与实现不一致的问题总是令人困惑的，如果明确将参数声明为void或int a则可以解决这种问题。  
  
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

### <span id="ID_mixedDeclarations">▌R6.29 不应将函数或函数指针和其他声明写在同一个语句中</span>

ID_mixedDeclarations&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

每条语句只应声明一个函数或函数指针，否则可读性较差。  
  
示例：
```
int* foo(int, int), bar(0), baz(char, long);  // Non-compliant, very bad
```
应改为：
```
int* foo(int, int);   // Compliant
int bar = 0;          // Compliant
int baz(char, long);  // Compliant
```
<br/>
<br/>

#### 参考
C++ Core Guidelines ES.10  
<br/>
<br/>

### <span id="ID_mixedTypeObjDefinition">▌R6.30 不建议将类型定义和对象声明写在一个语句中</span>

ID_mixedTypeObjDefinition&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

不建议将类型定义和对象声明写在一个语句中，否则可读性较差。  
  
示例：
```
struct _A
{
    int a, b, c;
} A;  // Bad

typedef struct _B
{
    int x, y, z;
} B;  // Bad
```
<br/>
<br/>

#### 参考
C++ Core Guidelines C.7  
<br/>
<br/>

### <span id="ID_nonStdAssignmentRetType">▌R6.31 拷贝赋值、移动赋值运算符应返回所属类的非const引用</span>

ID_nonStdAssignmentRetType&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

按赋值表达式的标准语法要求，以及C\+\+语言的惯例，拷贝赋值、移动赋值应返回所属类的非const引用，便于调用者使用并满足泛型编程的要求。  
  
对“=”的合理重载，可以使对象的赋值表达式作为子表达式灵活地出现在各种语句中，这也是“泛型程序设计”的必要条件，使算法的代码实现即可以适应普通变量，也可以适应自定义对象。如果类的对象与标准模板库相关，则其赋值运算符应满足本规则的要求。  
如果“=”运算符没有返回所属类的非const引用，便无法满足连续赋值等表达式的语法要求，对标准模板类的使用上会受到限制。  
  
对于“\+=”、“\-=”等复合赋值运算符也有相同的要求。  
  
示例：
```
class A {
    int i = 0;

public:
    A& operator = (int x) {  // Compliant
        i = x;
        return *this;
    }

    A& operator = (const A& rhs) {  // Compliant
        i = rhs.i;
        return *this;
    }
};

class B {
    int i = 0;

public:
    void operator = (int x) { i = x; }  // Non-compliant, should return B&
    void operator = (const B& rhs) { i = rhs.i; }  // Non-compliant
};

template <class T>
void foo(T& x, T& y) {
    x = y = 1;
}

A u, v;
B w, x;
foo(u, v);  // OK
foo(w, x);  // Compile error
```
<br/>
<br/>

#### 参考
C++ Core Guidelines F.47  
C++ Core Guidelines C.60  
C++ Core Guidelines C.63  
<br/>
<br/>

### <span id="ID_nonStdCopyAssignmentParam">▌R6.32 拷贝赋值运算符的参数应为同类对象的const左值引用</span>

ID_nonStdCopyAssignmentParam&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

拷贝赋值运算符的参数不应为值传递，否则会造成不必要的复制，以及对象切片等问题。  
  
示例：
```
class A {
public:
    A& operator = (A);  // Non-compliant
};
```
应改为：
```
class A {
public:
    A& operator = (const A&);  // Compliant
};
```
<br/>
<br/>

#### 参考
C++ Core Guidelines C.60  
<br/>
<br/>

### <span id="ID_nonStdMoveAssignmentParam">▌R6.33 移动赋值运算符的参数应为同类对象的非const右值引用</span>

ID_nonStdMoveAssignmentParam&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

移动赋值运算符的参数不可为const右值引用，否则将失去移动赋值的意义。  
  
示例：
```
struct A {
    char* p;
    A& operator = (const A&& a) {  // Non-compliant
        free(p);
        p = copy(a.p);             // Not necessary
    }
    ....
};
```
例中赋值运算符不是真正的移动赋值运算符，仍是一种低效实现。  
应将a.p与this\->p交换，利用a的析构函数完成资源的释放，这才是真正意义上的移动赋值。  
  
应改为：
```
struct A {
    char* p;
    A& operator = (A&& a) noexcept {  // Compliant
        char* tmp = p;
        p = a.p;
        a.p = tmp;
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

### <span id="ID_nonVirtualOverride">▌R6.34 派生类不应重新定义与基类相同的非虚函数</span>

ID_nonVirtualOverride&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

派生类不应重新定义与基类相同的非虚函数，否则与多态机制相矛盾，易造成意料之外的问题。  
  
示例：
```
class A {
public:
    int foo() const { return 0; }
};

class B: public A {
public:
    int foo() const { return 1; }  // Non-compliant
};

int bar(A* a) {
    return a->foo();
}
```
如果将B类型的指针传入bar函数，将执行A::foo，然而参数实际指向的是B类型的对象，但B::foo不会被执行，这就形成了逻辑上的矛盾，造成意料之外的问题。
<br/>
<br/>

#### 参考
Effective C++ item 36  
<br/>
<br/>

### <span id="ID_overloadAddressOperator">▌R6.35 不应重载取地址运算符</span>

ID_overloadAddressOperator&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

对于取地址运算符，重载之后可以返回任意地址，极易误用，建议禁止重载该运算符。  
  
需要特别注意的是当类的定义不完整时，取地址运算符会变为内置运算符，不按重载逻辑执行。  
  
示例：
```
class X;
X* foo(X& x) {
    return &x;
}

class X {
public:
    X* operator &() {
        return nullptr;
    }
};

X* bar(X& x) {
    return &x;
}
```
其中foo函数会返回x对象的实际地址，而bar函数按重载逻辑执行，当源文件和头文件的包含关系较为复杂时，这种问题会造成很大的混乱。
<br/>
<br/>

#### 参考
C++ Core Guidelines C.166  
MISRA C++ 2008 5-3-3  
<br/>
<br/>

### <span id="ID_overloadComma">▌R6.36 不应重载逗号运算符</span>

ID_overloadComma&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

对于内置逗号表达式，C\+\+明确规定要从左到右计算子表达式的值，而对逗号运算符的重载打破了这一规则，往往会造成不符合预期的计算结果。  
可参见ID\_overloadLogicOperator对这种问题的详细说明。  
  
示例：
```
struct A;
A& operator , (const A&, const A&);  // Non-compliant
```
<br/>
<br/>

#### 相关
ID_overloadLogicOperator  
<br/>

#### 参考
MISRA C++ 2008 5-2-11  
<br/>
<br/>

### <span id="ID_overloadLogicOperator">▌R6.37 不应重载“逻辑与”和“逻辑或”运算符</span>

ID_overloadLogicOperator&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

对“逻辑与”、“逻辑或”等运算符的重载会影响效率，甚至造成与预期完全不符的结果。  
  
C/C\+\+语言明确地规定了内置的逗号、逻辑与、逻辑或等表达式的子表达式求值顺序。对于逻辑表达式，从左到右计算子表达式的值，当可以确定整个表达式的值时立即结束计算，如果还有其它子表达式未求值也不再计算了，这种规则称为“短路规则”，意在提升执行效率，然而对运算符的重载却打破了这一规则。  
  
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
注意表达式（设a和b为A的对象）：
```
b && a.assign(b)
```
按常理，此表达式的意思应该是如果b在某种意义上“有效”，那么就将b赋给a，所以b的值应该先被求出，如果满足条件，再执行后边的动作。  
但由于&&被重载，变成了一个函数调用，其左子表达式和右子表达式成了函数的参数，C\+\+标准对函数参数的求值顺序并无明确规定，所以常规逻辑表达式的计算顺序无法得到保证。目前MSVC、g\+\+等主流编译器默认情况下都是从右到左计算参数的值，这个例子中a.assign(b)将会先被执行，这与预期完全不符。  
  
解决方法：  
去掉对&&的重载，对类A引入与bool类型的转换。
<br/>
<br/>

#### 相关
ID_overloadComma  
<br/>

#### 参考
MISRA C++ 2008 5-2-11  
<br/>
<br/>

### <span id="ID_qualifierInvalid">▌R6.38 const或volatile不可修饰引用</span>

ID_qualifierInvalid&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: declaration error

<hr/>

C\+\+标准规定，const或volatile可修饰指针，但不可修饰引用，否则起不到任何作用。  
  
示例：
```
int a = 0;
int &const i = a;     // Non-compliant
int &volatile j = a;  // Non-compliant
typedef int& int_r;
const int_r r0 = a;   // Non-compliant, r0 is not a const-reference at all
const int_r& r1 = a;  // Non-compliant, r1 is not a const-reference at all
```
例中声明的引用均无效，应去掉限定符，或改为：
```
const int& i = a;     // Compliant
volatile int& j = a;  // Compliant
int_r r = a;          // Compliant
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 8.3.2(1)  
ISO/IEC 14882:2011 8.3.2(1)  
ISO/IEC 14882:2017 11.3.2(1)  
<br/>
<br/>

### <span id="ID_qualifierRepeated">▌R6.39 const或volatile不应重复</span>

ID_qualifierRepeated&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: declaration error

<hr/>

重复的const或volatile限定符是没意义的，也可能意味着某种错误。  
  
示例：
```
const const char* p0 = "....";   // Non-compliant
const char const* p1 = "....";   // Non-compliant
char* const const p2 = "....";   // Non-compliant
```
第一和第二种情况，const重复修饰char，很可能应该修饰\*号，属于常见笔误，应改为：
```
const char * const p1 = "....";  // Compliant
```
第三种情况，const重复修饰\*号，符合语言文法，但没有实际意义，很可能应该修饰char，应改为：
```
const char * const p2 = "....";  // Compliant
```
<br/>
<br/>

#### 相关
ID_badQualifierPosition  
<br/>
<br/>

### <span id="ID_redundantOverride">▌R6.40 override和final关键字不应同时出现</span>

ID_redundantOverride&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

final关键字表示不可重写的重写，override表示可再次重写的重写，出现final关键字时不应再出现override关键字，使代码更清晰简洁。  
  
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

### <span id="ID_redundantVirtual">▌R6.41 当有override或final关键字时，不应再出现virtual关键字</span>

ID_redundantVirtual&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

只应在定义新的虚函数时使用virtual关键字，当重写虚函数时，应使用override或final关键字，不应再出现virtual关键字，使代码更清晰简洁。  
  
示例：
```
class D: public B {
public:
    virtual int foo() final;     // Non-compliant, ‘virtual’ is redundant
    virtual int bar() override;  // Non-compliant, ‘virtual’ is redundant
};
```
<br/>
<br/>

#### 参考
C++ Core Guidelines C.128  
<br/>
<br/>

### <span id="ID_sandwichedModifier">▌R6.42 const、volatile等关键字不应出现在基本类型名称的中间</span>

ID_sandwichedModifier&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

某些基本类型可由多个符号组成，const或volatile等关键字不应出现在这些符号的中间，否则可读性较差。  
  
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
const、volatile
inline、virtual、explicit
register、static、thread_local、extern、mutable
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

### <span id="ID_selfDependentInitialization">▌R6.43 对象初始化不可依赖自身的值</span>

ID_selfDependentInitialization&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: declaration error

<hr/>

对象初始化依赖自身的值属于逻辑错误。  
  
示例：
```
void foo(int i) {
    if (i > 0) {
        int i = i + 1;  // Non-compliant
        bar(i);
    }
}
```
例中局部变量i的初始化依赖自身的值，这种问题往往是错误地定义了与外层作用域中名称相同的对象。  
  
应改为：
```
void foo(int i) {
    if (i > 0) {
        int j = i + 1;  // OK
        bar(j);
    }
}
```
<br/>
<br/>
<br/>

### <span id="ID_plainNumericChar">▌R6.44 参与数值运算的char变量需显式声明signed或unsigned</span>

ID_plainNumericChar&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

没有signed或unsigned限制的char变量，其符号是由实现定义的，具有可移植性要求的代码需明确其符号。  
  
示例：
```
bool foo(char c) {          // Non-compliant
    return c < 180;         // May be always true
}

void bar() {
    int i = 2;
    char c = 180;           // Non-compliant
    printf("%d", i * c);    // What is output?
}
```
foo函数可能只会返回true，而bar函数可能输出\-152也可能输出360。  
char型变量在PC桌面、服务端等环境中一般是有符号的，在移动端或嵌入式系统中往往是无符号的，需明确其具体实现。
```
bool foo(unsigned char c) { // Compliant
    return c < 180;
}

void bar() {
    int i = 2;
    unsigned char c = 180;  // Compliant
    printf("%d", i * c);    // 360
}
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.9.1(1)-implementation  
ISO/IEC 14882:2011 3.9.1(1)-implementation  
<br/>

#### 参考
MISRA C++ 2008 5-0-11  
SEI CERT INT07-C  
<br/>
<br/>

### <span id="ID_plainBinaryChar">▌R6.45 表示二进制数据的char指针或数组应显式声明unsigned</span>

ID_plainBinaryChar&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

char类型的符号由实现定义，二进制数据不应受符号位干扰，应显式声明unsigned。  
  
示例：
```
char buf[100];  // Non-compliant
FILE* fp = fopen("foo.bin", "wb");
if (fp) {
    fread(buf, 1, 100, fp);
    ....
}
```
应改为：
```
unsigned char buf[100];  // Compliant
```
这样做也可有效区分二进制数据与字符串，提高可维护性。
<br/>
<br/>

#### 相关
ID_plainNumericChar  
<br/>
<br/>

### <span id="ID_singleSignedBitfield">▌R6.46 有符号变量的位域长度不应为1</span>

ID_singleSignedBitfield&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

有符号变量的位域长度如果为1表示只有一个比特位，而该比特位是符号位，极易造成意料之外的错误。  
  
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
    x.a = 1; std::cout << x.a << std::endl;  // What is output?
    x.b = 1; std::cout << x.b << std::endl;
    x.c = 1; std::cout << x.c << std::endl;
}
```
按常规思维，x.b和x.c为1与预期相符，x.a预期是1，但实际是\-1。
<br/>
<br/>

#### 参考
MISRA C++ 2008 9-6-4  
<br/>
<br/>

### <span id="ID_staticNotUsed">▌R6.47 不应存在未被使用的本地static函数</span>

ID_staticNotUsed&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

未被使用的本地static函数得不到任何执行机会，应删除或修正调用关系。
<br/>
<br/>

#### 参考
MISRA C++ 2008 0-1-10  
<br/>
<br/>

### <span id="ID_superfluousVoid">▌R6.48 C++代码中函数参数列表如果为空不应声明为“(void)”</span>

ID_superfluousVoid&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

与C语言不同，在C\+\+中如果函数的参数列表声明为空的括号，与声明为“(void)”的方式完全相同，均表示没有参数，所以如果在C\+\+仍采用C的声明方式就显得很啰嗦。  
  
示例：
```
class A {
public:
    A(void);  // Verbose
   ~A(void);  // Verbose

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

### <span id="ID_tooManyDeclarators">▌R6.49 在一个语句中不应声明过多对象或函数</span>

ID_tooManyDeclarators&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

在一个语句中不应声明过多对象或函数，建议在每个语句中只声明一个对象或函数，提高可读性也可减少笔误。  
  
示例：
```
int* a, b[10], c, d(int), e = 0;  // Bad
```
例中只有a是指针，也只有e被初始化，d为函数，将它们分开声明是更好的选择。
<br/>
<br/>

#### 配置
ID_declaration/maxDeclaratorCount：一个声明语句能包含的对象个数上限，超过则报出  
<br/>

#### 参考
C++ Core Guidelines ES.10  
MISRA C++ 2008 8-0-1  
<br/>
<br/>

### <span id="ID_tooManyInitItems">▌R6.50 初始化列表中的项目数量不应过多</span>

ID_tooManyInitItems&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

当初始化列表中的项目过多时应考虑将数据移入资源或数据文件，并考虑一定的加密措施，使数据更安全，也更利于维护。  
  
示例：
```
std::string arr[] = {
    "rabbit",
    "hamster",
    // ... 3000 items ...
    // It's really hard to maintain
    "puppie",
};
```
<br/>
<br/>

#### 配置
ID_expression/maxInitItemCount：初始化项目的最大个数，超过则报出  
<br/>
<br/>

### <span id="ID_unsuitableArraySize">▌R6.51 局部数组的长度不应过大</span>

ID_unsuitableArraySize&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

局部数组的长度过大增加函数堆栈的压力，易导致溢出错误。  
  
示例：
```
void foo() {
    int arr[1024 * 1024 * 1024];  // Non-compliant, too large
    ....
}
```
局部数组在栈上分配空间，是无法控制失败情况的，应改在堆上分配，或优化算法减小空间成本。
<br/>
<br/>

#### 配置
ID_declaration/maxLocalArraySize：局部数组的长度上限，超过则报出  
<br/>

#### 参考
CWE-770  
SEI CERT MEM05-C  
<br/>
<br/>

### <span id="ID_uselessQualifier">▌R6.52 enum的底层类型（underlying type）中不应出现const或volatile</span>

ID_uselessQualifier&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

enum或enum class的底层类型（underlying type）中出现const或volatile是没有意义的，会被编译器忽略，为语言用法错误。  
  
示例：
```
enum E: volatile unsigned int  // Non-compliant, ‘volatile’ is invalid
{
    e0, e1, e2
};
E e = e0;  // ‘e’ is not volatile
```
应改为：
```
enum E: unsigned int  // Compliant
{
    e0, e1, e2
};
volatile E e = e0;  // OK, ‘e’ is volatile
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 7.2(2)  
ISO/IEC 14882:2011 10.2(2)  
<br/>
<br/>

### <span id="ID_virtualAssignment">▌R6.53 拷贝赋值、移动赋值运算符不应为虚函数</span>

ID_virtualAssignment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

由于拷贝赋值、移动赋值运算符的返回值应为所属类的非const引用，这与虚函数的机制是相矛盾的，将赋值运算符设为虚函数很可能是没有作用的。  
  
示例：
```
class A {
public:
    virtual A& operator = (const A&);
};

class B {
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

### <span id="ID_virtualComparison">▌R6.54 比较运算符不应为虚函数</span>

ID_virtualComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

对于重载的比较运算符，很难正确触发C\+\+的多态机制，将其设为虚函数很可能引发意料之外的错误。  
  
运算符==、!=、<、>、 <=、>=均受本规则限制。  
  
示例（选自C\+\+ Core Guidelines）：
```
class B {
  string name;
  int number;
  virtual bool operator==(const B& a) const
  {
    return name == a.name && number == a.number;
  }
  // ...
};

class D : B {
  char character;
  virtual bool operator==(const D& a) const
  {
    return name == a.name && number == a.number && character == a.character;
  }
  // ...
};

B b = ...
D d = ...
b == d;    // compares name and number, ignores d's character
d == b;    // error: no == defined
D d2;
d == d2;   // compares name, number, and character
B& b2 = d2;
b2 == d;   // compares name and number, ignores d2's and d's character
```
<br/>
<br/>

#### 参考
C++ Core Guidelines C.87  
<br/>
<br/>

### <span id="ID_virtualInFinal">▌R6.55 final类中不应声明虚函数</span>

ID_virtualInFinal&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: declaration warning

<hr/>

final类不再产生派生类，其中的virutal函数也不会再被重写，故不应声明虚函数。  
  
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

### <span id="ID_forbidBitfield">▌R6.56 禁用位域</span>

ID_forbidBitfield&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: declaration suggestion

<hr/>

引入位域的本意是为了节省空间，然而位域改变了变量约定俗成的取值范围，易造成理解上的偏差，也会造成维护困难。  
  
位域与“引用”等C\+\+概念有冲突，而且C\+\+标准在位域的内存分配或数据对齐等方面定义的不够充分，存在很多由实现定义的内容，所以应改用某种更有效的算法达到节省空间或时间的目的。
<br/>
<br/>

#### 相关
ID_exceededBitfield  
ID_singleSignedBitfield  
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

### <span id="ID_forbidEnumBitfield">▌R6.57 禁止对枚举变量声明位域</span>

ID_forbidEnumBitfield&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: declaration warning

<hr/>

枚举变量的值可以为有符号整数，由于符号位的存在极易导致意料之外的结果，且不利于枚举类型的扩展，故禁止对枚举变量声明位域。  
  
示例：
```
enum E {
    A, B, C, D
};

struct X {
    E e: 2;  // Non-compliant
};

int main() {
    X x;
    x.e = D;
    if (x.e == D) {  // What is output?
        std::cout << "OK";
    } else {
        std::cout << "Oops";
    }
}
```
输出为Oops  
例中E各枚举值的范围是\[0,3\]，按常规思维，位域长度为2即可满足所有枚举值的范围，然而由于符号位的存在否定了这一点，导致x.e用D赋值后再与D比较竟然得到不相等的结果（因为D的值为3而x.e的值为\-1）。
<br/>
<br/>

#### 相关
ID_singleSignedBitfield  
<br/>

#### 参考
MISRA C++ 2008 9-6-3  
<br/>
<br/>

### <span id="ID_forbidFlexibleArray">▌R6.58 禁用柔性数组</span>

ID_forbidFlexibleArray&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: declaration suggestion

<hr/>

柔性数组（flexible array）一般是指结构体最后不完整定义的数组成员，表示不占用空间的指针，这种数组在C99中有所定义，但不在C\+\+标准之中，在C\+\+代码中不应使用。  
  
示例：
```
struct A {
    int len;
    int dat[];  // Non-compliant
};

A* cpy(const A* p) {
    A* a = (A*)malloc(sizeof(A) + p->len * sizeof(int));
    *a = *p;   // Error, only p->len is copied
    return a;
}
```
例中\*a=\*p这种拷贝赋值运算会漏掉数组的内容，而且数组不会计入sizeof的结果，易引起意料之外的错误，所以在C语言中也不建议使用这种柔性数组。
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 6.7.2.1(16)  
<br/>

#### 参考
MISRA C 2012 18.7  
<br/>
<br/>

### <span id="ID_forbidFunctionVoidPtr">▌R6.59 接口的参数或返回值不应被声明为void*</span>

ID_forbidFunctionVoidPtr&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: declaration warning

<hr/>

与接口相关的数据类型应保持精确，不应将参数或返回值声明为void\*。  
  
在C\+\+语言中，如果参数或返回值需要面对多种不同类型的数据，应合理使用重载或模板机制。  
  
示例：
```
class A {
public:
    void* foo();      // Non-compliant
    void bar(void*);  // Non-compliant
};
```
例中foo和bar函数的返回值以及参数是不符合要求的。  
  
C语言中存在大量的库函数不符合本规则要求，在C\+\+语言中应避免使用，如：
```
int buf[123];
memset(buf, 0, 123);  // Logic error, should be ‘123 * sizeof(int)’
```
例中memset函数的第一个形式参数就是void\*型，只能通过更底层的二进制方式访问对象序列，是一种对类型设计的破坏，应改用STL标准库提供的方法：
```
int buf[123];
std::fill_n(buf, 123, 0);  // Safe and brief
```
改用类型明确的方法可以使很多问题在编译期得到控制。  
  
例外：  
C\+\+语言规定new运算符的返回类型为void\*，delete运算符的参数类型为void\*，这些情况可被排除。
<br/>
<br/>

#### 相关
ID_forbidMemberVoidPtr  
<br/>

#### 参考
C++ Core Guidelines I.4  
<br/>
<br/>

### <span id="ID_forbidMemberVoidPtr">▌R6.60 类成员不应被声明为void*</span>

ID_forbidMemberVoidPtr&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: declaration warning

<hr/>

与接口相关的数据类型应保持精确，不应将类成员声明为void\*，尤其是非private成员，更不应声明为void\*。  
  
在C\+\+语言中，如果成员需要面对多种不同类型的数据，应合理使用模板机制。  
  
示例：
```
class A {
public:
    void* dat;  // Non-compliant
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
    T* dat;     // Compliant
    ....
};
```
<br/>
<br/>

#### 相关
ID_forbidFunctionVoidPtr  
<br/>

#### 参考
C++ Core Guidelines I.4  
<br/>
<br/>

### <span id="ID_forbidRestrictPtr">▌R6.61 禁用restrict指针</span>

ID_forbidRestrictPtr&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: declaration warning

<hr/>

C语言中的restrict指针要求其他指针不能再指向相同区域，有助于编译器优化，但不符合这种限制时会导致标准未定义的错误，相当于增加了误用的风险，也提高了测试成本。  
  
示例：
```
void foo(int c[]) {
    int *restrict a = &c[0];    // Non-compliant
    int *restrict b = &c[1];    // Non-compliant
    ....
    a = b;                      // Undefined behavior
    ....
}

int bar(int *restrict x, int *restrict y) {  // Non-compliant
    return *x + *y;
}

int baz(int* p) {
    return bar(p, p);  // Undefined behavior
}
```
restrict指针虽然有助于编译器优化，但应在效率的提高和存在的风险之间进行取舍，非系统库中的代码、改动频繁的代码不建议使用restrict指针，而且这种优化大部分情况下也难以真正解决效率的瓶颈问题。
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

### <span id="ID_forbidStaticArrSize">▌R6.62 声明数组参数的大小时禁用static关键字</span>

ID_forbidStaticArrSize&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: declaration warning

<hr/>

C语言规定数组作为形式参数时，可用static关键字修饰大小，要求传入数组的大小不能小于由static 关键字修饰的值，这种机制有助于编译器优化，但不符合这种限制时会导致标准未定义错误，相当于增加了误用的风险，也提高了测试成本。  
  
示例：
```
int foo(int a[static 5], int n) {  // Non-compliant
    int i;
    int s = 0;
    for (i = 0; i < n; i++) {
        s += a[i];
    }
    return s;
}

int bar() {
    int a[3] = {1, 2, 3};
    return foo(a, 3);      // Undefined behavior
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

### <span id="ID_forbidVariadicFunction">▌R6.63 禁用可变参数列表</span>

ID_forbidVariadicFunction&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: declaration warning

<hr/>

可变参数列表对参数的类型和数量缺乏有效的限定和控制，是公认的不安全因素。  
  
示例：
```
string format(const char* fmt, ...);  // Non-compliant
```
假设format函数与sprintf函数功能相似，由参数fmt设定格式，将其他参数转为字符串后依次替换fmt中的占位符并返回结果。设'@'和'$'为占位符，分别对应字符串和整数，如调用format("@: $", "value", 123)则返回字符串"value: 123"。  
  
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
例中va\_start、va\_arg、va\_end是可变参数列表的标准支持，这种方法只能在运行时以fmt为依据获取后续参数，当实际参数与fmt不符时会造成严重问题，单纯地要求代码编写者小心谨慎是不可靠的，改用更安全的方法才是明智的选择。  
  
在C\+\+代码中可采用模板参数包来实现这种功能：
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
示例代码用get\_argstrs函数递归地将参数都转为string对象存入容器，再将fmt中的'@'和'$'依次替换成容器中的字符串，实际上这种实现是可以不区分'@'和'$'的，这个过程中参数的个数和类型是可以由代码主动判断的，如果参数不能转为字符串则不会通过编译，如果参数个数与占位符不符也容易作出处理。  
  
模板参数包、constexpr等特性是C\+\+语言在编译理论上的重大突破，合理运用这些特性可以有效提升代码的安全性和可维护性。
<br/>
<br/>

#### 参考
C++ Core Guidelines ES.34  
C++ Core Guidelines F.55  
MISRA C 2004 16.1  
MISRA C++ 2008 8-4-1  
<br/>
<br/>

### <span id="ID_forbidVolatile">▌R6.64 慎用volatile关键字</span>

ID_forbidVolatile&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: declaration suggestion

<hr/>

对于硬件无关的功能性代码不妨禁用volatile关键字，否则误用该关键字会引发优化及同步相关的多种问题。  
  
volatile关键字仅保证编译器不会对其修饰的变量进行额外优化，确保其有稳定的地址以供读写，在并发编程中容易使人产生误解，其实该关键字在C/C\+\+语言中与同步机制并无关系。  
  
当需要访问某个硬件地址，而其它进程或硬件可能会修改该地址上的数据时，应将其指针声明为volatile：
```
volatile int* vp = get_hardware_address();
```
如果当前进程不需要主动改变数据，则可加const修饰符：
```
const volatile int* cvp = get_hardware_address();
```
这样一来vp或cvp的值一直会与内存地址对应，利用\*vp或\*cvp总会执行从内存的读取操作，而不会因为编译器的优化而被忽略，可能需要保证进程之间的同步，但这与volatile关键字是没有关系的，这一点与Java等语言有较大区别，需要特别注意。  
  
除此之外，利用volatile关键字阻止编译优化的特性可实现一些安全措施，参见ID\_unsafeCleanup。
<br/>
<br/>

#### 参考
C++ Core Guidelines CP.8  
C++ Core Guidelines CP.200  
<br/>
<br/>

### <span id="ID_complexDeclaration">▌R6.65 不建议采用复杂的声明</span>

ID_complexDeclaration&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

对于返回函数指针的函数、返回数组指针的函数、函数指针的数组等复合型声明，应先将各子类型取别名，再用简单声明的方式书写，否则可读性较差。  
  
示例：
```
int (*foo(int))(bool);  // Bad, returns a function pointer
int (*foo(char))[123];  // Bad, returns an array pointer
```
应改为：
```
typedef int(*funptr)(bool);
typedef int(*arrptr)[123];

funptr foo(int);   // Good
arrptr foo(char);  // Good
```
<br/>
<br/>

#### 相关
ID_complexParameter  
<br/>
<br/>

### <span id="ID_complexParameter">▌R6.66 不建议采用复杂的参数声明</span>

ID_complexParameter&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

函数指针、数组指针等类型作为函数参数时，应先将其类型取别名，再用简单声明的方式书写，否则可读性较差。  
  
示例：
```
int foo(int(*fp)(int, int));  // Bad
```
应改为：
```
typedef int(*fp_t)(int, int);

int foo(fp_t fp);  // Good
```
<br/>
<br/>
<br/>

### <span id="ID_complexUsingAlias">▌R6.67 不建议在using别名中定义新的类</span>

ID_complexUsingAlias&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: declaration suggestion

<hr/>

不建议在using别名中定义新的类，类的定义应采用常规方式。  
  
示例：
```
using A = struct {  // Non-compliant
    int a, b, c;
};
```
应改为：
```
struct A {
    int a, b, c;
};
```
虽然C\+\+语言十分灵活，可以通过多种方式达到同一种目的，但应该选择最简洁且通俗易懂的方式实现。
<br/>
<br/>
<br/>

## <span id="exception">7. Exception</span>

### <span id="ID_exceptionUnsafe">▌R7.1 需确保异常的安全性</span>

ID_exceptionUnsafe&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

当产生异常时，应保证：  
 ● 相关资源不会泄漏  
 ● 相关对象处于正确状态  
是保证C\+\+异常机制可以正确工作的重要基础。  
  
示例：
```
void foo() {
    lock();
    process();   // Non-compliant if it may throw ...
    unlock();
}
```
如果例中lock是某种加锁操作，unlock是解锁操作，process是某个可能抛出异常的过程，那么foo函数就不是异常安全的，当异常抛出后解锁操作无法执行从而造成死锁等严重后果。  
  
需要保证资源从申请到回收的过程不被异常中断，应对资源采用面向对象的管理方法，使资源的申请和回收得以自动完成，可参见ID\_ownerlessResource的进一步讨论。  
  
异常安全的另一个重要方面是抛出异常时应保证相关对象的状态是正确的，事务或算法在处理对象时可能要分多个步骤处理对象的多个成员，要注意中途抛出异常会造成数据不一致等问题。
```
class X {
    T a, b;
public:
    void foo() {
        proc(a);
        // ... If throw an exception ...
        proc(b);
    }
};
```
设a和b是两个密切相关的成员，如账户和金额等，foo是一个处理事务的函数，如果在中途抛出异常就会使对象处于一种错误的状态，解决方法可以考虑“复制\-交换”模式，如：
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
        .....
    }
};
```
事务先处理对象的副本，处理成功后交换副本与对象的数据，交换过程需要保证不抛出异常，这样从对象副本的生成到事务处理完毕的过程中即使抛出异常也不影响对象的状态。  
  
swap过程不可抛出异常也是一个规则，可参见ID\_throwInSwap。
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

### <span id="ID_exceptionInException">▌R7.2 异常类的构造函数或异常信息相关的函数不应抛出异常</span>

ID_exceptionInException&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

抛出异常时，或获取异常相关的信息时，如果再抛出异常不利于异常的处理与定位。  
  
示例：
```
class MyException {
    std::string msg;
public:
    MyException(const char* s) {
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
例中在构造函数和what函数中抛出异常是不符合要求的，而且要注意string类型的构造函数需要动态内存分配，当分配失败时也会抛出异常，有高可靠性要求的软件系统需要规避。  
  
自定义的异常类应从标准库定义的相关异常基类派生，异常类的成员应尽量简单，如：
```
class MyException: public std::exception {
public:
    const char* what() const noexcept override {
        return "message";
    }
};
```
由标准异常类的定义，MyException的构造函数可以保证不会抛出异常。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 18.8.1  
<br/>
<br/>

### <span id="ID_throwInDestuctor">▌R7.3 析构函数不可抛出异常</span>

ID_throwInDestuctor&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: exception error

<hr/>

析构函数抛出异常是违反异常处理机制的。  
  
当抛出异常时，从异常被抛出到异常被处理之间的对象，也就是从“throw”到“catch”各层调用栈中的对象会被自动析构，如果在这个过程中某个对象的析构函数又抛出了异常，将引发混乱。  
标准规定，这种情况会直接引发std::terminate函数的执行，所以从析构函数抛出的异常有可能是无法被捕获和处理的。  
  
示例（设E0和E1是不相关的异常类）：
```
class A {
    ....
public:
   ~A() try {
        if (cond0) {
            throw E0();  // Non-compliant, propagate out
        }
        else if (cond1) {
            throw E1();  // OK, digested by itself
        }
    }
    catch (const E1&) {
        ....
    }
};
```
建议将析构函数声明为noexcept。  
  
与析构相关的过程也不应抛出异常：  
 ● 资源回收  
 ● delete、delete\[\]运算符  
 ● 具有free、clear、release等语义的函数  
  
另外，具有swap、hash等语义以及移动构造或赋值相关的过程也不应抛出异常，详见相关规则。
<br/>
<br/>

#### 相关
ID_throwInHash  
ID_throwInSwap  
ID_throwInMove  
<br/>

#### 依据
ISO/IEC 14882:2003 15.2(3)  
ISO/IEC 14882:2011 15.2(3)  
ISO/IEC 14882:2011 3.7.4.2-undefined  
<br/>

#### 参考
C++ Core Guidelines C.36  
C++ Core Guidelines C.37  
C++ Core Guidelines E.16  
SEI CERT DCL57-CPP  
MISRA C++ 2008 15-5-1  
<br/>
<br/>

### <span id="ID_throwInHash">▌R7.4 与STL标准库相关的hash过程不应抛出异常</span>

ID_throwInHash&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: exception suggestion

<hr/>

对象的hash过程中不应抛出异常，否则相关的容器和算法无法正常工作。  
  
示例：
```
template <>
struct std::hash<MyType> {
    using result_type = size_t;
    using argument_type = MyType;

    size_t operator()(const MyType& x) const {
        if (!x.ptr) {
            throw exception();  // Non-compliant
        }
        return hash<size_t>()((size_t)x.ptr);
    }
};
```
标准库规定容器的find或count等方法应通过返回值表示对象存在与否，然而如果hash过程抛出异常，这些方法也会抛出异常，相当于打破了这种约定，易造成意料之外的结果。
<br/>
<br/>

#### 参考
C++ Core Guidelines C.89  
<br/>
<br/>

### <span id="ID_throwInSwap">▌R7.5 对象的swap过程不可抛出异常</span>

ID_throwInSwap&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

两个对象在swap（交换）过程中，每个对象的状态都是不完整的，如果在交换中途抛出异常，对象将处于错误的状态无法恢复。  
  
标准库中存在大量与swap相关的接口或算法，如果swap过程抛出异常，也会使标准库无法按照约定的方式工作，所有swap过程均应标记为noexcept。  
  
注意，swap是保证异常安全的重要手段，不抛出异常是基本要求，详见ID\_exceptionUnsafe。  
  
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

### <span id="ID_throwInMove">▌R7.6 移动构造函数和移动赋值运算符不可抛出异常</span>

ID_throwInMove&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

移动构造函数和移动赋值运算符在本质上相当于将当前对象与临时对象“交换”，交换过程不可抛出异常，可参见ID\_throwInSwap。  
  
示例：
```
class T {
    int i = 0;
    int* p = nullptr;
    ....

public:
    T(T&& a) noexcept {
        this->swap(a);
    }
    T& operator = (T&& a) noexcept {
        this->swap(a);
        return *this;
    }
    void swap(T& a) noexcept {
        // Use your intelligence,
        // be careful not to throw exceptions
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

### <span id="ID_forbidThrowSpecification">▌R7.7 禁用含throw关键字的异常规格说明</span>

ID_forbidThrowSpecification&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: exception warning

<hr/>

由throw关键字声明的动态异常规格说明已过时，应采用由noexcept关键字声明的方式。  
  
将所有可能抛出的异常详细列出，尤其是牵扯到第三方不可控代码时，会增大代码的管理成本，而且各编译器相关的实现方式并未统一，现已移出标准。  
  
示例：
```
int foo() throw(Exception);  // Non-compliant
```
应改为：
```
int foo() noexcept(false);   // Compliant
int bar() noexcept;          // Compliant
```
例外：
```
int bar() throw();           // Let it go?
```
空的throw异常规格说明与noexcept等价，是一种惯用写法，可根据配置项放宽要求，本规则不建议使用throw关键字。
<br/>
<br/>

#### 配置
ID_declaration/forbidEmptyThrowSpecification：为true时报出空throw异常规格说明，否则放过  
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

### <span id="ID_improperRethrow">▌R7.8 重新抛出异常时应使用空throw表达式（throw;）</span>

ID_improperRethrow&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

重新抛出异常时应使用空throw表达式，避免异常对象的精度损失或不必要的复制。  
  
示例：
```
class EBase {};
class EDerive: public EBase {};

void foo() {
    try {
        throw EDerive();
    }
    catch (const EBase& e) {
        throw e;  // Non-compliant, use throw;
    }
}

void bar() {
    try {
        foo();
    }
    catch (const EDerive& e) {
        // Cannot catch EDerive
    }
}
```
注意，例中foo函数虽然捕获的是EDerive对象，但throw e;抛出的是EBase对象，这也是一种切片问题，造成了对象类型的“精度损失”。  
将throw e;改为throw;可解决这种问题。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 15.1(6)  
ISO/IEC 14882:2011 15.1(8)  
<br/>
<br/>

### <span id="ID_rethrowOutOfCatch">▌R7.9 不应在catch块外使用空throw表达式（throw;）</span>

ID_rethrowOutOfCatch&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

空throw表达式用于重新抛出当前捕获的异常，用在catch块外是危险的，增大了流程控制的复杂性。  
  
如果当前没有捕获异常的话，空throw表达式会引发std::terminate函数的执行，这种情况下是否清理调用栈之间的对象则是由实现定义的。  
  
示例：
```
void foo() {
    throw;   // Non-compliant
}

void bar() {
    try {
        throw;  // Non-compliant
    }
    catch (...) {
        // Cannot catch "throw;"
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

### <span id="ID_throwGenericException">▌R7.10 不应抛出过于宽泛的异常</span>

ID_throwGenericException&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

抛出过于宽泛的异常如std::exception、std::logic\_error、std::runtime\_error等，使异常处理失去针对性，无法做到具体问题具体处理，而且处理这种异常时很可能将本不应处理的异常一并捕获，造成混乱。  
  
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
foo函数的参数不符合要求时抛出std::exception类的异常，过于宽泛，如果bar函数抛出其它异常，也会被当作“参数不符合要求”处理，这显然是错误的。  
  
正确的做法是为每种异常定义明确的子类：
```
class FooWrongArg: public std::exception {
public:
    FooWrongArg() {}
};

int foo(int a) {
    if (a < 0) {
        throw FooWrongArg();  // Compliant
    }
    return bar(a);  // Other exceptions may be thrown
}

void baz(int a) {
    try {
        foo(a);
    } catch (FooWrongArg& e) {  // Right
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

### <span id="ID_throwNonExceptionType">▌R7.11 不应抛出非异常类型的对象</span>

ID_throwNonExceptionType&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

字符串或普通变量以及非异常相关的对象不应被当作异常抛出，否则意味着异常相关的设计是不健全的。  
  
完善的异常处理机制应包含如下几点：  
 ● 可提供对异常情况的准确描述  
 ● 可将异常情况有效分类  
 ● 可方便地处理异常并进行调试  
  
值得强调的是throw、try、catch等关键字应专注于异常处理，不应使用这些关键字控制程序的业务流程，业务代码与异常处理代码应有明显区别，否则会使代码含混不明，效率也会降低。  
  
示例（选自C\+\+ Core Guidelines）：
```
// don't: exception not used for error handling
int find_index(vector<string>& vec, const string& x)
{
    try {
        for (gsl::index i = 0; i < vec.size(); ++i)
            if (vec[i] == x) throw i;  // found x
    }
    catch (int i) {
        return i;
    }
    return -1;   // not found
}
```
这段代码比常规的实现要慢很多。
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

### <span id="ID_throwPointer">▌R7.12 不应将指针作为异常抛出</span>

ID_throwPointer&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: exception suggestion

<hr/>

如果将指针作为异常抛出，并且该指针指向动态创建的对象，会增加不必要的内存管理开销，也容易造成意料之外的错误。  
  
示例：
```
class E { .... };

void foo(int i) {
    static E e1;
    E* e2 = new E;
    if (i > 0) {
        throw &e1;  // Non-compliant
    } else {
        throw e2;   // Non-compliant
    }
}
	
void bar(int i) {
    try {
        foo(i);
    } catch (E* e) {
        // ‘e’ should be deleted or not??
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

### <span id="ID_throwNULL">▌R7.13 不应抛出NULL</span>

ID_throwNULL&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

在C\+\+语言中，虽然NULL表示空指针，然而在多数环境中throw NULL相当于throw 0，类型的不明确会造成对异常的错误捕捉。  
  
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
本例意在抛出一个空指针，然而会被捕获整数的catch块捕获。
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

### <span id="ID_throwNullptr">▌R7.14 不应抛出nullptr</span>

ID_throwNullptr&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: exception warning

<hr/>

nullptr可被所有接受指针的catch块捕捉，使异常处理失去针对性，故不应抛出nullptr。  
  
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

### <span id="ID_forbidException">▌R7.15 禁用C++异常</span>

ID_forbidException&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: exception warning

<hr/>

本规则适用如下场景，可酌情选取。  
 1. 对时空性能有严格要求的项目  
 2. 代码所属框架不支持异常处理  
 3. 对C语言高度兼容的代码  
 4. 项目整体没有依照面向对象的理念实施  
  
利用返回值或错误码的错误处理方式要求检查可能产生错误的每一个步骤，有些出错情况可能被遗漏，C\+\+的异常机制可大幅简化这种繁琐的方式，使代码更专注于事务或算法的实现，而且C\+\+异常是不可被忽略的，然而C\+\+的异常机制是需要一定开销的，对代码的设计与实现也有更严格的要求。  
  
如果异常情况频繁出现，其成本是不可被忽视的，不适于具有高性能要求的实时软件系统。如果代码所属项目没有依照面向对象的理念实施，无法保证异常安全，也是不建议使用异常机制的，可参见ID\_exceptionUnsafe的进一步讨论。
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

### <span id="ID_mainReturnsNonInt">▌R8.1 main函数返回值的类型只应为int</span>

ID_mainReturnsNonInt&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

main函数的返回值可作为整个进程执行情况的总结，按惯例返回0或EXIT\_SUCCESS表示执行成功，非0或EXIT\_FAILURE表示执行失败，main函数的返回值会作为标准exit函数的参数。  
  
main函数应采用标准明确支持的方式：  

```
int main(void) { .... }  // Compliant
int main(int argc, char *argv[]) { .... }  // Compliant
```
如果将返回值设为void或其它非int类型，均不合规。  

```
void main() { .... }  // Non-compliant
bool main() { .... }  // Non-compliant
```
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 5.1.2.2.1(1) 5.1.2.2.3(1)  
ISO/IEC 9899:2011 5.1.2.2.1(1) 5.1.2.2.3(1)  
<br/>

#### 参考
C++ Core Guidelines F.46  
<br/>
<br/>

### <span id="ID_inconsistentParamName">▌R8.2 函数的参数名称在声明和实现处应保持一致</span>

ID_inconsistentParamName&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

函数的参数名称在声明和实现处不一致甚至顺序也不相同，会对函数的调用者造成误导，而且不能排除是实现上的错误。  
  
示例：
```
int foo(int a, int b);  // Prototype

int foo(int b, int a) {  // Non-compliant, which is which??
    return a? b + 1: 0;
}
```
<br/>
<br/>

#### 参考
MISRA C++ 2008 8-4-2  
<br/>
<br/>

### <span id="ID_paramMayBeSlicing">▌R8.3 多态类的对象作为参数时不应采用值传递的方式</span>

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

### <span id="ID_paramNotUsed">▌R8.4 不应存在未被使用的具名形式参数</span>

ID_paramNotUsed&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: function suggestion

<hr/>

如果函数的某个参数在函数内没有被用到过，意味着函数的功能与设计预期存在差距。  
  
如果某个参数确实不需要被用到，建议尽量从函数的声明中将其删除，如果需要遵循某种约定而必须保留参数的话（如虚函数或回调函数），不妨在参数的声明中将参数的名称删掉，使该参数成为抽象声明，并佐以一定的注释说明。  
  
有时编译器会对没有用到的参数给出警告，为了消除警告有人会采用“参数 = 参数”或“(void)参数”的方式来消除警告，这是不可取的，如：
```
void fun(int a) {
  a = a;  // Or ‘(void)a’, not recommended
  ....
}
```
应改为：
```
void fun(int) {
  ....
}
```
这样编译器不会给出警告，而且也不会有多余的代码。
<br/>
<br/>

#### 参考
C++ Core Guidelines F.9  
MISRA C 2012 2.7  
MISRA C++ 2008 0-1-11  
<br/>
<br/>

### <span id="ID_paramPassedByValue">▌R8.5 由const修饰的参数应为引用或指针</span>

ID_paramPassedByValue&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

函数参数按值传递时会产生复制及构造开销，而且如果有const修饰，意味着对象不可改变，那么按值传递是没有意义的。  
  
示例：
```
void fun(const std::string s) {  // Non-compliant
    ....
}
```
例中参数s为按值传递的对象，每当fun函数被调用时，s都会作为一个新的对象被构造，因为其值又不能被改变，所以这种构造是没有意义的，利用常量引用即可解决这个问题：
```
void fun(const std::string& s) {  // Compliant
    ....
}
```
改为常量引用后，s的值和原来一样不可被改变，而且不需要额外的开销。
<br/>
<br/>

#### 参考
C++ Core Guidelines F.16  
<br/>
<br/>

### <span id="ID_localInitialization">▌R8.6 局部变量在使用前必须初始化</span>

ID_localInitialization&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: function error

<hr/>

未经初始化即使用的局部变量，其值是不确定的，意味着程序存在严重逻辑错误。  
  
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
例中变量a的初始化依赖条件cond，在条件范围之外使用是错误的。  
建议变量在声明处初始化，即使不方便在声明处初始化，也应该在声明的附近进行不依赖条件的初始化。  
  
建议的模式：
```
int a = 0;  // Compliant

int b;
b = foo();  // Compliant
```
不建议的模式：
```
int a;
if (some_cond) {
    a = foo();
}
....
if (another_cond) {
    use(a);
}
```
a的初始化依赖于条件some\_cond，即使some\_cond和another\_cond有一定相关性可以保证对a的读取是正确的，也会造成潜在的维护困难，当条件比较复杂时极易出错。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 8.5(9)  
ISO/IEC 14882:2011 8.5(11)  
ISO/IEC 14882:2017 11.6(12)  
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

### <span id="ID_memberInitialization">▌R8.7 成员变量须在声明处或构造时初始化</span>

ID_memberInitialization&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

由于成员变量声明的位置和使用的位置相距较远，所以更容易造成未初始化先使用的问题，应该在声明处或构造函数中对所有需要显式初始化的成员进行初始化。  
  
示例：
```
struct A {
    int x;
    int y = 0;
    int z;

    A(int i): x(i) {  // Non-compliant, Missing initialization of ‘z’
    }
};
```
例中构造函数没有对z初始化是不符合要求的，尤其是公有成员出现这种问题时会造成更大的风险，建议所有成员都在声明处初始化。  
  
应改为：
```
struct A {
    int x = 0;  // Good
    int y = 0;  // Good
    int z = 0;  // Good

    A(int i): x(i) {  // OK
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

### <span id="ID_illMemberAccess">▌R8.8 在面向构造或析构函数体的catch块中不可访问非静态成员</span>

ID_illMemberAccess&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: function error

<hr/>

当流程进入面向构造或析构函数体的catch块时，非静态成员的生命周期已经结束，如果再次访问会导致标准未定义的错误。  
  
示例：
```
class A {
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

private:
    int i;
};
```
例中access(i)等访问是有问题的。  
  
应调整实现或将“function\-try\-block”改为普通“try\-block”：
```
A::A() {
    try {
        ....
    }
    catch (...) {
        access(i);   // OK
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

### <span id="ID_disorderedInitialization">▌R8.9 成员初始化应遵循声明的顺序</span>

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
虽然在初始化列表中n在p的前面，但实际上n仍然在p之后被初始化，“new int\[n\]”会造成严重错误。  
  
应改为：
```
struct T {
    size_t n;
    int* p;

    T(size_t s): n(s), p(new int[n])  // Compliant
    {}
};
```
调整了n和p的声明顺序，使n先于p初始化即可解决问题。  
  
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
a为引用， b的地址在初始化之前就确定了，所以“a(b)”没问题  
b为指针，用c的值初始化b的值是不对的  
d为数组，也是一个地址，所以“c(d)”没有问题
<br/>
<br/>

#### 参考
C++ Core Guidelines C.47  
<br/>
<br/>

### <span id="ID_virtualCallInConstructor">▌R8.10 在构造函数中不应调用虚函数</span>

ID_virtualCallInConstructor&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

虚函数在构造函数中的多态性不生效，而且调用未定义的纯虚函数会导致标准未定义的错误。  
  
示例：
```
class A {
public:
    A(): a(foo()) {}  // Non-compliant
private:
    virtual int foo() { return 0; }
    int a;
};

class B: public A {
    int foo() override { return 1; }
};

class C: public A {
    int foo() override { return 2; }
};
```
例中B和C对foo函数的重写是无效的，成员a的值总为0，这会使维护者产生相当大的误解。
<br/>
<br/>

#### 参考
C++ Core Guidelines C.82  
Effective C++ item 9  
<br/>
<br/>

### <span id="ID_virtualCallInDestuctor">▌R8.11 在析构函数中不应调用虚函数</span>

ID_virtualCallInDestuctor&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

虚函数在析构函数中的多态性不生效，而且调用未定义的纯虚函数会导致标准未定义的错误。  
  
示例：
```
class A {
public:
    A() {}
   ~A() { clear(); }  // Non-compliant
private:
    virtual void clear() {}
};

class B: A {
    int* b = foo();
    void clear() override { delete b; }
};
```
例中B对clear函数的重写是无效的，相关内存也没有被释放，这会使维护者产生相当大的误解。
<br/>
<br/>

#### 参考
C++ Core Guidelines C.82  
Effective C++ item 9  
<br/>
<br/>

### <span id="ID_sideEffectCopyConstructor">▌R8.12 拷贝构造函数应避免复制之外的功能</span>

ID_sideEffectCopyConstructor&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

拷贝构造函数可以被优化而不被执行，而且这种优化是由实现定义的。  
  
拷贝构造函数只应专注于复制对象，避免复制之外的功能，如修改静态成员或对环境产生影响。  
  
示例：
```
class A {
    int i;
    static int s;

public:
    A(): i(0) {}
    A(const A& rhs) {
        i = a.i;     // Compliant
        s++;         // Non-compliant
        ++i;         // Non-compliant
    }
};

A foo() {
    return A();
}

void bar() {
    A a = foo();
    ....
}
```
bar函数用foo函数返回的对象初始化a对象，理论上应执行拷贝构造函数，但标准允许编译器将foo函数返回的临时对象直接作为a对象，这种优化称为“copy elision”，拷贝构造函数被执行的次数可能与预期不符，所以拷贝构造函数不应存在超出复制的副作用。  
  
进一步可参见RVO与NRVO相关介绍。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 12.8(15)-implementation  
ISO/IEC 14882:2011 12.8(31)-implementation  
<br/>

#### 参考
MISRA C++ 2008 12-8-1  
<br/>
<br/>

### <span id="ID_this_selfJudgement">▌R8.13 赋值运算符应妥善处理参数就是自身对象时的情况</span>

ID_this_selfJudgement&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

赋值运算符应妥善处理参数就是自身对象时的情况，防止资源分配或回收的冲突。  
  
示例：
```
A a;
a = a;  // Self assignment ...
```
如果例中A是需要深拷贝的类，在赋值运算符中应判断这种对自身赋值的情况：
```
A& A::operator = (const A& rhs) {
    if (this != &rhs) {  // Required
        ....
    }
    return *this;
}
```
如果A的拷贝构造函数和交换方法齐备，也可按“复制\-交换”模式实现：
```
A& A::operator = (const A& rhs) {
    A tmp(rhs);
    this->swap(tmp);
    return *this;
}
```
利用创建临时对象并与之交换的方法，也有效规避了冲突，这种方法使各函数更专注于自已的职责，不必重复编写分配或回收相关的代码，建议采用这种方法。
<br/>
<br/>

#### 参考
C++ Core Guidelines C.62  
<br/>
<br/>

### <span id="ID_unreachableCode">▌R8.14 不应存在得不到执行机会的代码</span>

ID_unreachableCode&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: function error

<hr/>

得不到执行机会的代码往往意味着逻辑错误，或者是遗迹代码。  
  
其成因主要有：  
1. 之前的所有分枝都提前结束了函数的执行  
2. 之前的必经分枝中存在永远也不会结束的代码  
3. 所在分枝的条件恒为假  
4. 所在分枝被其它分枝遮盖  
  
第3点特化为：ID\_constLogicExpression、ID\_invalidCondition  
第4点特化为：ID\_if\_identicalCondition、ID\_if\_hiddenCondition  
  
示例：
```
int fun() {
  if (condition) {
      return 0;
  } else {
      return 1;
  }
  statements    // Non-compliant, unreachable
}
```
例中statements之前的所有分枝都会结束函数的执行，所以statements不会被执行。  
  
例外：  
在多分枝结构的末尾，统一安置一条结束函数执行的语句是一种好的编程习惯，即当statements只包含一条return或throw语句时可以不算作违规。  
  
另外，在正式代码中不应存在如下形式的代码：
```
if (false) { .... }
while (false) { .... }
for (;false;) { .... }
```
也不应该在return语句之后存在其他语句，这种代码如果不是被人恶意篡改，就是出于某种目的将本已无效的代码遗留了下来，请参见ID\_constLogicExpression、ID\_invalidCondition。  
  
建议时刻保持代码的整洁，并将维护过程中的变动及时地保存在版本管理系统中，这样可以清晰的查看各版本之间的变动，而如果将无效代码与有效代码混在一起，势必造成维护的负担。
<br/>
<br/>

#### 参考
CWE-561  
MISRA C 2004 14.1  
MISRA C 2012 2.1  
MISRA C++ 2008 0-1-1  
<br/>
<br/>

### <span id="ID_notAllBranchReturn">▌R8.15 有返回值的函数其所有分枝都应有明确的返回值</span>

ID_notAllBranchReturn&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: function error

<hr/>

当函数的某个分枝没有明确的返回值时会引发标准未定义的错误。  
  
示例：
```
int fun() {
  if (condition1) {
      return 1;
  } else if (condition2) {
      return 2;
  } else if (condition3) {
      // Non-compliant if no return value
  } else {
      return 4;
  }
}
```
当符合condition3的条件时，fun函数的调用者将得到一个错误的返回值。  
建议存在多分枝时，在函数结尾统一返回。  
  
例外：  
在main函数的结尾如果不显式调用return语句，编译器会自动添加return 0，故对main函数不作要求。
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

### <span id="ID_localAddressFlowOut">▌R8.16 不可返回局部对象的地址或引用</span>

ID_localAddressFlowOut&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: function error

<hr/>

局部对象的生命周期在函数返回后结束，其地址或引用也会失效，如果继续访问会造成标准未定义的错误。  
  
示例：
```
int* foo() {
    int i = 0;
    ....
    return &i;  // Non-compliant
}

int& bar() {
    int i = 0;
    ....
    return i;   // Non-compliant
}
```
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 6.2.4(2)-undefined  
ISO/IEC 9899:2011 6.2.4(2)-undefined  
<br/>

#### 参考
CWE-562  
C++ Core Guidelines F.43  
<br/>
<br/>

### <span id="ID_returnConstObject">▌R8.17 函数返回值不应为const对象</span>

ID_returnConstObject&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: function suggestion

<hr/>

函数返回const对象不利于移动构造或移动赋值等优化机制，也可能本意是返回引用，但遗漏了引用符号。  
  
示例：
```
const vector<int> foo() {  // Non-compliant
    return { 1, 2, 3 };
}
vector<int> bar(foo());    // vector(const vector&) called
```
foo()函数返回const vector对象，构造bar对象时只能进行深拷贝，无法利用移动构造等特性。  
  
应改为：
```
vector<int> foo() {      // Compliant
    return { 1, 2, 3 };
}
vector<int> bar(foo());  // vector(vector&&) called, more efficient
```
这样可利用vector的移动构造，效率更高。  
  
对于遵循C\+\+11之前标准的代码，也不应返回const对象，函数返回的对象本来就需要通过const引用或传值的方式被后续代码使用，所以将返回值设为const的意义不大。
<br/>
<br/>

#### 参考
C++ Core Guidelines F.20  
<br/>
<br/>

### <span id="ID_returnOdd">▌R8.18 返回值应与函数的返回类型相符</span>

ID_returnOdd&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

返回值与返回类型不符的情况：  
 ● 返回类型为bool型，却返回了非true非false、非0非1的常量  
 ● 返回类型为指针，却返回了非0、非NULL、非nullptr等常量  
 ● 返回类型为整数，却返回了NULL、true、false等常量  
  
这些问题可能是在维护过程中产生的，也可能意味着逻辑错误，而且很容易造成误导，需谨慎对待。  
  
示例：
```
bool foo() { return NULL; }   // Non-compliant
int  bar() { return false; }  // Non-compliant
int* baz() { return '\0'; }   // Non-compliant
```
<br/>
<br/>

#### 参考
MISRA C++ 2008 4-10-1  
<br/>
<br/>

### <span id="ID_returnSameConst">▌R8.19 函数返回值不应为相同的常量</span>

ID_returnSameConst&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

如果一个函数有多个返回语句，但所有返回值都是相同的常量，那么这个函数的返回值是没有意义的。  
  
示例：
```
bool foo(int a) {
    if (a > 100) {
        return true;
    } else if (a > 50) {
        return true;
    } else {
        return true;  // Non-compliant, all return values are the same
    }
}
```
例外：  
当函数可以抛出异常时可不受本规则限制，但需要在文档中指明。
<br/>
<br/>
<br/>

### <span id="ID_returnSuperfluousConst">▌R8.20 基本类型的返回值不应使用const修饰</span>

ID_returnSuperfluousConst&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

基本类型（char、int、long等）的返回值本来就不能作为可修改的左值，const修饰符是多余的。  
  
示例：
```
const int foo() {  // Non-compliant, ‘const’ is superfluous
    return 123;
}

class A {
    int a = 123;
public:
    int& foo() { return a;}
    const int foo() const { return a; }  // Non-compliant, missing ‘&’
};
```
出现这种问题说明设计与使用存在一定的偏差，也可能本意是返回引用，而书写时漏掉了引用符号造成的。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 3.10(5)  
ISO/IEC 14882:2011 3.10(1)  
<br/>
<br/>

### <span id="ID_unsuitableReturn">▌R8.21 属性为noreturn的函数中不应出现return语句</span>

ID_unsuitableReturn&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

属性为noreturn的函数中出现return语句说明函数还是可以正常返回的，这种矛盾会对调用者造成很大困扰。  
  
示例：
```
[[noreturn]] void foo(int a) {
    if (a < 0) {
        return;  // Non-compliant
    }
    abort();
}
```
<br/>
<br/>
<br/>

### <span id="ID_unsuitableReturnType">▌R8.22 属性为noreturn的函数返回类型只应为void</span>

ID_unsuitableReturnType&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

属性为noreturn的函数返回类型不是void说明函数还是有返回值的，这种矛盾会对调用者造成很大困扰。  
  
示例：
```
[[noreturn]] int foo();   // Non-compliant
[[noreturn]] void bar();  // Ccompliant
```
<br/>
<br/>
<br/>

### <span id="ID_redundantJump">▌R8.23 不应出现多余的跳转语句</span>

ID_redundantJump&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

无返回值函数的最后一条return语句、循环体的最后一条continue语句、goto到下一条语句等是多余的，应当去除。  
  
示例：
```
void foo() {
    do_something();
    return;   // Redundant
}

void bar() {
    while (condition) {
        do_something();
        continue;    // Redundant
    }
}

void baz() {
    goto L;   // Redundant
L:
    do_something();
}
```
<br/>
<br/>
<br/>

### <span id="ID_tooManyLabels">▌R8.24 函数的标签数量应在规定范围之内</span>

ID_tooManyLabels&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

标签过多意味着函数内部的跳转逻辑过于复杂，违反结构化设计理念，应适当重构。  
  
对于C代码，建议一个函数最多只有一个标签，作为函数统一的出口，或资源回收过程的入口。  
对于C\+\+代码，不建议使用标签。  
  
示例：
```
void foo()
{
L0:
    ....
L1:
    // ... lots of labels require lots of gotos ...
    // ... lots of gotos make functions terrible ...
L100:
    ....
}
```
<br/>
<br/>

#### 配置
ID_function/maxLabelCount：标签数量上限，超过则报出  
<br/>
<br/>

### <span id="ID_tooManyLines">▌R8.25 函数的行数应在规定范围之内</span>

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
建议函数体不超过60行，以不需要拖拽滚动条就可以在屏幕上完整显示为宜。
<br/>
<br/>

#### 配置
ID_function/maxLineCount：行数上限，超过则报出  
<br/>

#### 参考
C++ Core Guidelines F.2  
C++ Core Guidelines F.3  
<br/>
<br/>

### <span id="ID_tooManyLambdaLines">▌R8.26 lambda表达式的行数应在规定范围之内</span>

ID_tooManyLambdaLines&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

复杂的lambda表达式与其调用者的代码混在一起时，是难以阅读的，引入lambda表达式的目的应该是“化简”，否则应使用普通函数。  
  
示例：
```
void foo()
{
    auto f0 = []() {
        // ... Many lines ...
    };
        // ... Many lambdas ...
        // ... Even lambdas nested lambdas ...
    auto f100 = []() {
        // ...
    };
    // Tut tut, this is a function, not a namespace,
    // use common functions instead
}
```
建议lambda表达式不超过5行，一个函数中不应有多个复杂的lambda表达式。
<br/>
<br/>

#### 配置
ID_function/maxLambdaLineCount：行数上限，超过则报出  
<br/>
<br/>

### <span id="ID_tooManyParams">▌R8.27 函数参数的数量应在规定范围之内</span>

ID_tooManyParams&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

函数参数过多，意味着：  
 ● 缺少合理的抽象机制：应将多而零散的参数按其内在联系封装成对象，从而方便地处理其逻辑关系，而不是简单地线性罗列  
 ● 违反“一个函数一个职责”理念：参数越多，函数处理的事务自然越多，代码的可维护性自然越差  
  
建议可供外部使用的全局函数、public或protected成员函数的参数不超过4个，内部使用的static函数、private成员函数的参数不超过8个。  
  
示例：
```
void foo(A a, B b, C c, D d, E e) {  // Bad
    ....
}
```
当函数参数过多时，应按参数的逻辑职责进行封装。  
假设a和b有直接逻辑关系，c、d、e有直接逻辑关系，不妨将a和b封装成一个类，c、d、e封装成一个类，在类的成员函数中实现相关功能，可更为清晰直观的保证逻辑关系的正确性。
```
class X {
    // ... Members and methods for ‘a’, ‘b’ ...
};
class Y {
    // ... Members and methods for ‘c’, ‘d’, ‘e’ ... 
};
void foo(X x, Y y) {  // Good
    x.methods();
    y.methods();
}
```
<br/>
<br/>

#### 配置
ID_declaration/maxInnerFunParamCount：static函数或private成员函数参数数量上限，超过则报出  
ID_declaration/maxParamCount：参数数量上限，超过则报出  
<br/>

#### 参考
C++ Core Guidelines F.2  
C++ Core Guidelines I.23  
<br/>
<br/>

### <span id="ID_forbidGotoBlocks">▌R8.28 禁止goto语句向平级的或更深层的其他作用域跳转</span>

ID_forbidGotoBlocks&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: function warning

<hr/>

使用goto语句向平级的或更深层的其它作用域跳转，可读性较差，是公认的不良实现。  
  
示例：
```
int foo(int a) {
  if (a == 0) {
      goto LAB1;  // Non-compliant, jumps into a block
  }
  for (int i = 0; i < a; i++) {
      for (int j = 0; j < a; j++) {
      LAB1:
          if (cond) {
              goto LAB2;  // Compliant, jumps out of nested loop
          }
      }
      ....
  }
LAB2:
  return a;
}
```
例中goto LAB1从分枝跳入循环是应当被禁止的，goto LAB2只是跳出了嵌套的循环，可以保留。
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

### <span id="ID_forbidGotoBack">▌R8.29 禁止goto语句向前跳转</span>

ID_forbidGotoBack&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: function suggestion

<hr/>

向先于当前goto语句定义的标签跳转，可读性较差，是公认的不良实现。  
  
示例：
```
int foo() {
    int i = 0, j = 0;
L:
    j += 1;
    i += j;
    if (j > 100) {
        goto M;    // Compliant
    }
    goto L;        // Non-compliant
M:
    return i;
}
```
应使用循环语句完成相同的功能。
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

### <span id="ID_forbidGoto">▌R8.30 禁用goto语句</span>

ID_forbidGoto&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: function suggestion

<hr/>

在非自动生成的、对可读性有要求的代码中，禁用goto语句。  
  
对于C语言建议选取ID\_forbidGotoBlocks和ID\_forbidGotoBack，对于C\+\+语言建议选取本规则。
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

### <span id="ID_forbidLongjmp">▌R8.31 禁用setjmp、longjmp</span>

ID_forbidLongjmp&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: function warning

<hr/>

setjmp、longjmp可以在过程间跳转，绕过常规的函数调用机制，进一步破坏了结构化编程理念，而且无法与C\+\+语言的基本实现机制兼容，极易造成意料之外的错误。  
  
示例：
```
jmp_buf buf;

float div(int a, int b) {
    if (b == 0) {
        longjmp(buf, 1);     // Non-compliant
    }
    return (float)a / b;
}

int main() {
    if (setjmp(buf) == 0) {  // Non-compliant
        printf("%f\n", div(3, 0));
    } else {
        return 1;
    }
}
```
例中div函数的参数不符合要求时，会跳转到setjmp处，从而进行异常处理，这也是setjmp、longjmp的常用场景，但在C\+\+代码中应使用语言提供的异常处理方式，以更简练的代码实现更高的可维护性。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 18.7(4)-undefined  
ISO/IEC 14882:2011 18.10(4)-undefined  
<br/>

#### 参考
MISRA C 2004 20.7  
MISRA C 2012 21.4  
MISRA C++ 2008 17-0-5  
C++ Core Guidelines SL.C.1  
<br/>
<br/>

### <span id="ID_recursion">▌R8.32 避免递归实现</span>

ID_recursion&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: function warning

<hr/>

递归实现，如函数直接或间接地调用自身，易导致难以控制的堆栈溢出错误。  
  
示例：
```
size_t foo(size_t n) {
    return n + foo(n - 1);  // Non-compliant
}
```
例中foo函数无条件地调用自身，是一种逻辑错误，导致无限的递归调用。  
  
又如：
```
size_t bar(size_t n) {
    if (n > 1) {
        return n + bar(n - 1);  // Non-compliant
    }
    return n;
}
```
例中bar函数设置了递归条件，但仍是不可取的，当参数n较大时仍然可以造成堆栈溢出错误。  
  
对于一般的功能，应尽量采用迭代、堆栈等非递归手段实现，对于难以使用非递归方式实现的特殊算法，应做到递归深度可控。
<br/>
<br/>

#### 参考
MISRA C 2012 17.2  
MISRA C++ 2008 7-5-4  
<br/>
<br/>

### <span id="ID_functionRepetition">▌R8.33 不应存在重复的函数实现</span>

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

### <span id="ID_if_semicolon">▌R9.1.1 if语句不应被分号隔断</span>

ID_if_semicolon&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

if语句不应被分号隔断。  
  
示例：
```
if (condition);  // Non-compliant, see the semicolon
{
    ....
}
```
一个分号使整个if语句失效，这可能是笔误，也可能是需求变化不再需要条件判断了，在维护过程中加入了分号，形成了令人费解的残留代码，也不能排除是有人恶意篡改了代码，应立即修正。
<br/>
<br/>

#### 参考
CWE-670  
<br/>
<br/>

### <span id="ID_if_identicalCondition">▌R9.1.2 在if...else-if分枝中不应有重复的条件</span>

ID_if_identicalCondition&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

if...else\-if分枝的条件不应有重复，否则相同条件排在前面的分枝会得以执行，而排在后面的分枝得不到执行机会。  
  
示例：
```
if (condition1) {
    branch1
}
else if (condition2) {
    branch2
}
else if (condition1) {  // Non-compliant, see previous ‘condition1’
    branch3
}
else {
    branch4
}
```
例中branch1和branch3的条件是相同的，所以branch3不会被执行。  
  
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

### <span id="ID_if_hiddenCondition">▌R9.1.3 在if...else-if分枝中不应有被遮盖的条件</span>

ID_if_hiddenCondition&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

if...else\-if分枝中，如果前面的条件被满足，后面的分枝就不会被执行，所以如果前面的条件是后面条件的一部分，或者前面的条件包含后面的条件，即使后面的条件可以被满足，其分枝也得不到执行机会。  
  
示例：
```
if (condition1) {
    branch1
} else if (condition2) {
    branch2
} else if (condition1 && condition3) {  // Non-compliant, see previous ‘condition1’
    branch3
} else {
    branch4
}
```
如果condition1为true，branch1将得以执行，branch3不会被执行，如果condition1为false，branch3还是不会被执行，称branch3被condition1遮盖了，branch3永远不会得到执行机会。  
  
如果前面的条件包含后面的条件，同样也会遮盖后面的条件，如：
```
if (condition1 || condition2) {
    branch1
} else if (condition2) {  // Non-compliant, see previous ‘condition2’
    branch2
} else {
    branch3
}
```
同理，branch2永远也不会被执行。
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

### <span id="ID_if_identicalBlock">▌R9.1.4 if分枝和else分枝的代码不应完全相同</span>

ID_if_identicalBlock&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

if分枝和else分枝完全相同会使条件判断失去意义，往往是由复制粘贴造成的错误。  
  
示例：
```
if (condition) {
    branch
} else {
    branch  // Non-compliant
}
```
例中branch表示内容完全相同的代码，其条件判断是没有意义的，需修正本应存在的差异，或去掉if\-else结构。
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

### <span id="ID_if_identicalElseIfBlock">▌R9.1.5 if...else-if各分枝的代码不应完全相同</span>

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
例中condition1对应分枝和else分枝的内容完全相同，应该合并成一个分枝，或修正本应存在的差异：
```
if (condition2) {
    branch2
}
else {
    branch1  // Compliant
}
```
例外：  
如果分枝内容较少，为了使代码更清晰可以接受适当的重复，但如果分枝内容很多，就不应重复了，不妨指定一个数量限制，当重复分枝的符号数量超过这个限制时算作违规，否则放过。
<br/>
<br/>

#### 配置
ID_if/identicalBlockThreshold：重复分枝符号数量限制，不检查符号数量小于该值的分枝  
<br/>

#### 相关
ID_if_identicalBlock  
<br/>

#### 参考
CWE-670  
<br/>
<br/>

### <span id="ID_if_identicalImplicitElseBlock">▌R9.1.6 if分枝和其隐含的else分枝的代码不应完全相同</span>

ID_if_identicalImplicitElseBlock&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

带有return、throw或break等子句的if语句，其同一作用域的后续代码相当于它的else分枝，显然这种隐含的else分枝与if分枝完全相同是没有意义的，很可能是由复制粘贴造成的错误。  
  
示例：
```
if (condition) {
    statements
    return;
}
statements  // Non-compliant
return;
```
例中if语句之内的statements以及return语句和if语句之外的语句完全相同，这种情况下if语句的条件判断是没有意义的，需修正本应存在的差异，或去掉if语句。
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

### <span id="ID_if_identicalSucceedingBlock">▌R9.1.7 没有else子句的if语句与其后续代码相同是可疑的</span>

ID_if_identicalSucceedingBlock&emsp;&emsp;&emsp;&emsp;&nbsp;:question: control suspicious

<hr/>

if语句与其同一作用域的后续代码完全相同是可疑的，可能是由复制粘贴造成的错误。  
  
示例：
```
if (condition) {
    a = SOME_VALUE;
}
a = SOME_VALUE;  // Rather suspicious
```
例中对变量a的赋值是没有意义的。
<br/>
<br/>

#### 相关
ID_if_identicalImplicitElseBlock  
<br/>

#### 参考
CWE-670  
<br/>
<br/>

### <span id="ID_if_commonStatements">▌R9.1.8 if分枝和else分枝的起止语句不应相同</span>

ID_if_commonStatements&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

if分枝和else分枝的起止语句如果相同，则应将其从分枝结构中提取出来，否则重复的代码不利于阅读和维护。  
  
示例：
```
if (condition) {
    foo();
    ....
    bar();
} else {
    foo();
    ....
    bar();
}
```
if与else分枝的开头和结尾相同，应提取出来：
```
foo();
if (condition) {
    ....
} else {
    ....
}
bar();
```
当条件分枝中的所有语句都相同时，特化为ID\_if\_identicalBlock，这种情况往往意味着错误。
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

### <span id="ID_if_scope">▌R9.1.9 if语句作用域的范围不应有误</span>

ID_if_scope&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

if语句作用域的范围不应有误。  
  
示例：
```
if (condition)
    statement1; statement2;  // Non-compliant

if (condition)
    statement1;
    statement2;  // Non-compliant
```
例中statement2不在if语句的作用域中，但看起来又和if语句相关，这种问题多数是由错误的宏展开或无效的缩进造成的。  
为了避免这种问题，if语句应使用大括号括起来：
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

### <span id="ID_if_mayBeElseIf">▌R9.1.10 如果if关键字前面是右大括号，if关键字应另起一行</span>

ID_if_mayBeElseIf&emsp;&emsp;&emsp;&emsp;&nbsp;:question: control suspicious

<hr/>

当if关键字前面是右大括号，且if关键字与该大括号在同一行时，属于不良换行方式，易造成else等关键字的遗漏。  
  
示例：
```
if (condition1) {
    ....
} if (condition2) {  // Rather suspicious, should it be ‘else if’??
    ....
}
```
这种情况可能是漏掉了else关键字，即使没有被漏掉，也应该让if关键字另起一行，否则这种换行习惯会增加遗漏else关键字的风险，而且可读性较差。
<br/>
<br/>
<br/>

### <span id="ID_if_assignment">▌R9.1.11 if语句的条件不应为赋值表达式</span>

ID_if_assignment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

虽然语言允许在if语句的条件中赋值，但=和==极易混淆，本规则不建议在条件中使用赋值表达式，建议所有产生bool型结果的表达式，都不要包含赋值运算符。  
  
示例：
```
HRESULT r = URLDownloadToFile(....);
if (r = S_OK) {  // Non-compliant
    ....
}
```
其中if语句的条件将r的值设成了S\_OK，而S\_OK的值为零，造成了逻辑错误。  
  
注意，当条件中的赋值运算符右侧为常量的时候，基本可以判定是==被误写成了=。  
  
又如：
```
if (n = strlen(s)) {  // Non-compliant
    ....
}
```
这也是一种公认的不良风格，应将赋值表达式拆分出来，或者在C\+\+代码中改为：
```
if (auto n = strlen(s)) {  // Compliant
    ....
}
```
将赋值表达式加上括号，表示有意为之，是一种惯用写法：
```
if ((n = strlen(s))) {  // Let it go?
    ....
}
```
这种情况可通过配置决定是否放过。
<br/>
<br/>

#### 配置
ID_if_assignment/allowEnclosedAssignment：为true可以放过括号括起来的赋值表达式  
<br/>

#### 参考
CWE-480  
CWE-481  
CWE-783  
<br/>
<br/>

### <span id="ID_if_emptyBlock">▌R9.1.12 if语句不应为空</span>

ID_if_emptyBlock&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

如果if语句没有else分枝，且其分枝内容为空，这样的if语句无任何意义，即使其条件有副作用，也不应继续保留该if结构。  
  
示例：
```
if (a = foo());  // Non-compliant
if (a == bar()) {}  // Non-compliant
```
这种情况多为维护或调试产生的残留代码，在正式代码中应当去除。
<br/>
<br/>

#### 参考
CWE-1071  
<br/>
<br/>

### <span id="ID_if_tooManyElseIf">▌R9.1.13 if...else-if分枝数量应在规定范围之内</span>

ID_if_tooManyElseIf&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

if...else\-if分枝超过指定数量，代码较为复杂不利于维护，而且在执行时各分枝的条件需逐一判断，效率较低，建议改为遵循某种算法的索引结构。  
  
示例：
```
if (rabbit) {
}
else if (hamster) {
}
// 3000 branches...
// Computers have the courage to execute,
// but do you have the courage to read?
else if (puppie) {
}
else {
}
```
建议if...else\-if分枝数量不超过5个。
<br/>
<br/>

#### 配置
ID_if/maxElseIfCount：分枝数量上限，超过则报出  
<br/>
<br/>

### <span id="ID_if_brace">▌R9.1.14 if分枝中的语句应该用大括号括起来</span>

ID_if_brace&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

组成if分枝的语句应为大括号括起来的复合语句，即使该复合语句只包含一条语句。  
  
这是非常好的编程习惯，如果能一直遵循此规则，可以杜绝由错误缩进、宏的错误使用造成的多种问题。  
  
示例：
```
if (cond1)         // Non-compliant
    if (cond2)     // Non-compliant
        action1();
else               // Non-compliant
    action2();
```
本例表达的逻辑与实际执行的逻辑完全不同，这段代码表达的逻辑应是：
```
if (cond1) {
    if (cond2) {
        action1();
    }
} else {
    action2();
}
```
由于C/C\+\+规定else子句总与其相临的最近if子句配对，所以这段代码的实际逻辑是：
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
if (cond)    // Non-compliant
    y = 2;
else         // Non-compliant
    x = 3;
    y = 1;
```
注意else子句中的y = 1；由于缩进的关系此句看起来应该是else分枝的一部分，但它实际上并不在else的作用范围之内，所以用大括号括起来，可杜绝此类问题：
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

### <span id="ID_if_missingEndingElse">▌R9.1.15 所有if...else-if分枝都应以else子句结束</span>

ID_if_missingEndingElse&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

所有if...else if分枝都以else子句结束是非常好的编程习惯，这与要求switch语句包含defualt分枝一样，是“防御性编程”思想的良好体现，参见ID\_switch\_missingDefault。  
  
单独的一个if分枝不要求接有else子句，如：
```
if (x > 0) {
    do_something();
}
```
存在多个if…else if分枝时，需要接有else子句
```
if (x > 0) {
    do_something0();
}
else if (y < 0){
    do_something1();
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

### <span id="ID_for_semicolon">▌R9.2.1 for语句不应被分号隔断</span>

ID_for_semicolon&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

for语句不应被分号隔断。  
  
示例：
```
for (auto i: container);  // Non-compliant, see the semicolon
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

### <span id="ID_for_uncondBroken">▌R9.2.2 for循环中不应存在无条件的throw、break、continue或return语句</span>

ID_for_uncondBroken&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

无条件的break或return语句会使循环失效，无条件的continue句语会使其后面的代码失效，如果其后没有代码时，该continue语句是没有意义的。  
  
示例：
```
for (int i = 0; i < size; i++) {
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

### <span id="ID_for_scope">▌R9.2.3 for语句作用域的范围不应有误</span>

ID_for_scope&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

for语句作用域的范围不应有误。  
  
示例：
```
for (iteration-declaration)
    statement1; statement2;  // Non-compliant

for (iteration-declaration)
    statement1;
    statement2;  // Non-compliant
```
例中statement2不在for循环的作用域中，但看起来又和for循环相关，这种问题多数是由宏展开或无效的缩进造成的。  
为了避免这种问题，for语句应使用大括号括起来。
<br/>
<br/>

#### 相关
ID_for_brace  
<br/>

#### 参考
CWE-483  
<br/>
<br/>

### <span id="ID_for_simplification">▌R9.2.4 如果for语句没有明显的循环变量则应改为while循环</span>

ID_for_simplification&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

如果for迭代声明中的第1个和第3个表达式为空，应改为while循环，使代码更简洁。  
  
示例：
```
for (;condition;)
```
不妨化简为：
```
while (condition)
```
例外：  
for (;;)被当作一种无限循环的惯用方法可被排除。
<br/>
<br/>

#### 参考
C++ Core Guidelines ES.73  
<br/>
<br/>

### <span id="ID_for_emptyBlock">▌R9.2.5 for循环体不应为空</span>

ID_for_emptyBlock&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

空的for循环将逻辑功能全部压缩到了迭代表达式中，可读性较差。  
  
示例：
```
void foo(int n, vector<int>& v) {
    int i = 0;
    for (; i < n; i++);  // Non-compliant
    bar(i);              // The indent is odd here
    for (auto x: v);     // Non-compliant, meaningless
}
```
应改为：
```
void foo(int n, vector<int>& v) {
    int i = 0;
    for (; i < n; ) {     // Compliant, but a while-loop is better
        i++;
    }
    bar(i);
    for (auto x: v) {     // Compliant
        ....
    }
}
```
<br/>
<br/>

#### 参考
C++ Core Guidelines ES.85  
<br/>
<br/>

### <span id="ID_for_floatCounter">▌R9.2.6 for循环变量不应为浮点型</span>

ID_for_floatCounter&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

用于控制循环次数的变量称为循环变量，这种变量不应采用浮点类型，否则循环的次数难以控制。  
  
由于浮点型变量的不精确性使浮点型变量不适用于控制循环次数，相关讨论请参见ID\_illFloatComparison。  
  
示例：
```
size_t n = 0;
for (float f = 0.f; f < 1.f; f += 0.001f) {  // Non-compliant
    ....
    n++;
}
cout << n << endl;  // Not 1000
```
本例按常识应循环1000次，然而由于f无法精确表示0.001，导致实际循环次数与预期产生偏差。  
  
可变通地建立整形循环变量与浮点数的关系：
```
for (size_t n = 0; n < 1000; n++) {  // Compliant
    float f = n * 0.001f;
    ....
}
```
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

### <span id="ID_for_counterChangedInBody">▌R9.2.7 for循环变量不应在循环体内被改变</span>

ID_for_counterChangedInBody&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

用于控制循环次数的变量称为循环变量，这种变量只应在for迭代声明的第3个表达式中被改变，否则陡增逻辑复杂度，且可读性较差。  
  
示例（选自C\+\+ Core Guidelines）：
```
for (int i = 0; i < 10; ++i) {
  // no updates to i -- ok
}
for (int i = 0; i < 10; ++i) {
  //
  if (/*something*/) ++i; // BAD
  //
}
bool skip = false;
for (int i = 0; i < 10; ++i) {
  if (skip) { skip = false; continue; }
  //
  if (/*something*/) skip = true; // Better:using two variables for two concepts.
  //
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

### <span id="ID_for_counterNested">▌R9.2.8 嵌套的for循环不应使用相同的循环变量</span>

ID_for_counterNested&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

同一个循环变量在内外层for循环中均被修改，使循环次数难以控制，是过于复杂的循环逻辑，也可能是某种错误。  
  
示例：
```
for (int i = 0; i < 100; i++) {
    for (; i < 10; i++) {  // Non-compliant
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

### <span id="ID_for_brace">▌R9.2.9 for循环体应该用大括号括起来</span>

ID_for_brace&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

for循环体应为复合语句，即使只包含一条语句。  
  
示例：
```
int foo() {
    int a = 0;
    for (int i = 0; i < 10; i++)  // Non-compliant
        a += i;
    return a;
}
```
应改为：
```
int foo() {
    int a = 0;
    for (int i = 0; i < 10; i++) {  // Compliant
        a += i;
    }
    return a;
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

### <span id="ID_while_semicolon">▌R9.3.1 while语句不应被分号隔断</span>

ID_while_semicolon&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

while语句不应被分号隔断。  
  
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

### <span id="ID_while_uncondBroken">▌R9.3.2 while语句中不应存在无条件的throw、break、continue或return语句</span>

ID_while_uncondBroken&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

不受条件限制的break或return语句会使循环失效，不受条件限制的continue句语会使其后面的代码失效，如果其后没有代码，该continue语句是没有意义的。  
  
示例：
```
while (condition) {
    ....
    return;  // Non-compliant
}

while (condition) {
    ....
    break;  // Non-compliant, becomes an if-statement
}

while (condition) {
    ....
    continue;  // Non-compliant, meaningless continue
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

### <span id="ID_while_assignment">▌R9.3.3 while语句的条件不应为赋值表达式</span>

ID_while_assignment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

虽然语言允许在while语句的条件中赋值，但=和==极易混淆，本规则不建议在条件中使用赋值表达式，建议所有产生bool型结果的表达式，都不要包含赋值运算符。  
  
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

### <span id="ID_while_scope">▌R9.3.4 while语句作用域的范围不应有误</span>

ID_while_scope&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

while语句作用域的范围不应有误。  
  
示例：
```
while (condition)
    statement1; statement2;  // Non-compliant

while (condition)
    statement1;
    statement2;  // Non-compliant
```
例中statement2不在while循环的作用域中，但看起来又和while循环相关，这种问题多数是由宏展开或无效的缩进造成的。  
为了避免这种问题，while语句应使用大括号括起来。
<br/>
<br/>

#### 相关
ID_while_brace  
<br/>

#### 参考
CWE-483  
<br/>
<br/>

### <span id="ID_while_emptyBlock">▌R9.3.5 while循环体不应为空</span>

ID_while_emptyBlock&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

空的while循环将逻辑功能全部压缩到了条件表达式中，可读性较差。  
  
示例：
```
void foo(char* d, const char* s) {
    while (*d++ = *s++);  // Non-compliant
}
```
应将条件和运算分开，提高可读性：
```
void foo(char* d, const char* s) {
    int i = 0;
    while (s[i] != '\0') {  // Compliant
        d[i] = s[i];
        i += 1;
    }
    d[i] = '\0';
}
```
经过现代编译器的优化，这两种方式在效率上并无区别，而后者可读性更高。
<br/>
<br/>

#### 参考
CWE-1071  
C++ Core Guidelines ES.85  
<br/>
<br/>

### <span id="ID_while_brace">▌R9.3.6 while循环体应该用大括号括起来</span>

ID_while_brace&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

while循环体应为复合语句，即使只包含一条语句。  
  
示例：
```
while (condition)  // Non-compliant
    statement;

while (condition)  // Non-compliant
    statement1;
    statement2;

while (condition) {  // Compliant
    statement;
}

while (condition) {  // Compliant
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

### <span id="ID_do_suspiciousContinue">▌R9.4.1 注意do-while(false)中可疑的continue语句</span>

ID_do_suspiciousContinue&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

continue语句和break语句在语义上是不同的，但在do\-while(false)中它们的功效是一样的。  
  
在do\-while(false)的循环体中如果即有break语句又有continue语句，那么continue语句被误用的可能性较大。  
  
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
      continue;  // Rather suspicious
    }
    ....
  } while (false);
}
```
为了减少代码阅读者的误解，建议在do\-while(false)中只使用break语句，不使用continue语句。
<br/>
<br/>

#### 参考
CWE-670  
<br/>
<br/>

### <span id="ID_do_emptyBlock">▌R9.4.2 do-while循环体不应为空</span>

ID_do_emptyBlock&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

空的do\-while循环将逻辑功能全部压缩到了条件表达式中，可读性较差。  
  
示例：
```
void foo(char* d, const char* s) {
    do {} while (*d++ = *s++);  // Non-compliant
}
```
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

### <span id="ID_do_brace">▌R9.4.3 do-while循环体应该用大括号括起来</span>

ID_do_brace&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

do\-while循环体应为复合语句，即使只包含一条语句。如果没有合理的大括号，可能会与内嵌的while语句形成难以发觉的错误。  
  
示例：
```
do  // Non-compliant
    foo();
    while (cond1);
while (cond2);
```
例中while关键字与do关键字产生了错误的对应关系，导致最后一个while形成了死循环，应改为：  

```
do {  // Compliant
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

### <span id="ID_do_deprecated">▌R9.4.4 不建议使用do语句</span>

ID_do_deprecated&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

do语句的终止条件在末尾，且第一次执行的时候不会检查终止条件，使do语句的可读性较低，易引发错误。  
  
do语句的本意是循环，而在实际代码中往往被用来进行流程控制，增加了不必要的复杂性，不妨将do语句抽取成一个函数，使代码有更好的结构。  
  
示例：
```
void foo(int cond) {
  do {
    if (cond < 0) {
      break;
    }
    ....
    if (cond > 0) {
      break;
    }
    ....
  } while (false);  // Too complex
  ....
}
```
<br/>
<br/>

#### 参考
C++ Core Guidelines ES.75  
<br/>
<br/>

### <span id="control.switch">9.5 Switch</span>

### <span id="ID_switch_semicolon">▌R9.5.1 switch语句不应被分号隔断</span>

ID_switch_semicolon&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

switch语句不应被分号隔断。  
  
示例：
```
switch (var);  // Non-compliant
```
这是毫无意义的switch语句，可能是遗迹代码，应及时去除。
<br/>
<br/>

#### 参考
CWE-670  
<br/>
<br/>

### <span id="ID_switch_emptyBlock">▌R9.5.2 switch语句不应为空</span>

ID_switch_emptyBlock&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

空的switch语句没有意义。  
  
示例：
```
switch (foo);  // Non-compliant
switch (bar) {}  // Non-compliant
```
这是毫无意义的switch分枝结构，有可能是残留代码，也可能是功能未实现。
<br/>
<br/>

#### 参考
CWE-1071  
<br/>
<br/>

### <span id="ID_switch_caseOutOfRange">▌R9.5.3 case常量的范围不可超出switch变量的范围</span>

ID_switch_caseOutOfRange&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

如果case常量的范围超出了switch变量的范围，会导致相应分枝永远不会被执行。  
  
示例：
```
void foo(char c) {
    switch (c) {
    case 100:  // Compliant
        ....
        break;
    case 256:  // Non-compliant, dead branch
        ....
        break;
    }
}
```
<br/>
<br/>

#### 相关
ID_illComparison  
<br/>

#### 参考
CWE-561  
<br/>
<br/>

### <span id="ID_switch_invalidStatement">▌R9.5.4 switch语句中任何子句都应从属于某个case或default分枝</span>

ID_switch_invalidStatement&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

switch语句中任何子句都应从属于某个case或default分枝，否则不会被执行。  
  
示例：
```
void foo(int a) {
    switch (a) {
        int i;    // Non-compliant
        i = 0;    // Non-compliant
    case 1:
        ....
        break;
    default:
        bar(i);   // Error, ‘i’ is not initialized
        break;
}
```
<br/>
<br/>
<br/>

### <span id="ID_switch_badFormedCase">▌R9.5.5 每个case分枝应直接从属于switch结构</span>

ID_switch_badFormedCase&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

case分枝如果不直接从属于switch结构，那么case所直接从属的结构往往是没有意义的。  
  
示例：
```
switch (v) {
    if (v > 0) {  // Non-compliant
case 1:
    ....
case 2:
    ....
    } else {      // Non-compliant
case -1:
    ....
    }
}
```
例中if语句不会被执行，其分枝结构也毫无意义。  
  
例外：  
如果case从属于do\-while循环，那么这个循环还是有意义的，但这并不是一种好的实现。
<br/>
<br/>

#### 参考
MISRA C 2004 15.1  
MISRA C++ 2008 6-4-4  
<br/>
<br/>

### <span id="ID_switch_uselessFallThrough">▌R9.5.6 不应存在紧邻default标签的空case标签</span>

ID_switch_uselessFallThrough&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

紧邻default标签的空case标签是没有意义的，应当去除。  
  
示例：
```
switch (i) {
case 0:
    ....
    break;

case 1:   // Non-compliant
default:
case 2:   // Non-compliant
    ....
    break;
}
```
应改为：
```
switch (i) {
case 0:
    ....
    break;

default:  // Compliant
    ....
    break;
}
```
<br/>
<br/>
<br/>

### <span id="ID_switch_identicalBranch">▌R9.5.7 不应存在内容完全相同的case分枝</span>

ID_switch_identicalBranch&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

内容完全相同的分枝应合并为一个分枝，也可能是由复制粘贴造成的错误。  
  
示例：
```
switch (v) {
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
例中case 3对应的分枝和case 1对应的分枝内容完全相同，应将其合并为一个分枝，或修正本应存在的差异。  
  
例外：  
如果分枝内容较少，为了使代码更清晰可以接受适当的重复，但如果分枝内容很多，就不应重复了，不妨指定一个数量限制，当重复分枝的符号数量超过这个限制时算作违规，否则放过。
<br/>
<br/>

#### 相关
ID_if_identicalBlock  
ID_if_identicalElseIfBlock  
<br/>

#### 参考
C++ Core Guidelines ES.3  
<br/>
<br/>

### <span id="ID_switch_bool">▌R9.5.8 switch语句的条件变量或表达式不应为bool型</span>

ID_switch_bool&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

switch语句的条件表达式为bool型时不应采用switch语句，应采用if\-else语句。  
  
示例：
```
void foo(bool b) {
    switch (b) {  // Non-compliant
    case true:
        .....
        break;
    case false:
        ....
        break;
    }
}
```
应改为：
```
void foo(bool b) {
    if (b) {  // Compliant
        ....
    } else {
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

### <span id="ID_switch_onlyDefault">▌R9.5.9 不应使用只有default标签的switch语句</span>

ID_switch_onlyDefault&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

只有default标签的switch语句是没有意义的，起不到分枝选择的作用，往往是残留代码或功能未实现。  
  
示例： 
```
switch (i) {
default:  // Meaningless
    ....
}
```
这种空的可以fallthrough到default标签的空case标签也是没有意义的：
```
switch (i) {
case 1:  // Meaningless
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

### <span id="ID_switch_onlyOneCase">▌R9.5.10 不应使用只有一个case标签的switch语句</span>

ID_switch_onlyOneCase&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

只有一个case标签的switch语句与if语句语义相同，但形式上更为复杂，应改为if语句。  
  
示例：
```
switch (i) {
case 123:  // Non-compliant
    ....
}
```
应改为：
```
if (i == 123) {
    ....
}
```
<br/>
<br/>

#### 参考
MISRA C 2012 16.6  
<br/>
<br/>

### <span id="ID_switch_tooManyCases">▌R9.5.11 switch语句分枝数量应在规定范围之内</span>

ID_switch_tooManyCases&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

switch语句分枝过多会使代码过于庞大不利于维护，分支很多时建议将每个case的执行逻辑抽取成函数，再按遵循某种算法的索引结构组织在一起。  
  
示例：
```
switch (x) {
case 1: foo(x); break;
case 2: bar(x); break;
    // ... Lots of cases ...
case 1000: baz(x); break;  // Non-compliant
}
```
建议case数量不超过10个。
<br/>
<br/>

#### 配置
ID_switch/maxCasesCount：分枝数量上限，超过则报出  
<br/>
<br/>

### <span id="ID_switch_missingDefault">▌R9.5.12 switch语句应配有default分枝</span>

ID_switch_missingDefault&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

所有switch语句都配有default分枝是非常好的编程习惯，这与if...else\-if分枝要求有else分枝一样，是“防御性编程”思想的良好体现，参见ID\_if\_missingEndingElse。  
  
示例：
```
switch (x)
{
case 0:
    do_something0();
    break;
case 1:
    do_something1();
    break;
default:
    // Comment is the minimum requirement,
    // if here is unreachable logically,
    // it's better to log or throw an exception
    break;
}
```
例外：  
当switch的条件变量为枚举类型，而分枝已经覆盖所有枚举值时，不必再要求有default分枝。
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

### <span id="ID_switch_breakOmitted">▌R9.5.13 每个非空的switch分枝都应该用无条件的break语句终止</span>

ID_switch_breakOmitted&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

每个非空的switch分枝都应该用无条件的break语句终止，break语句的缺失或误用是导致错误的常见原因。  
  
示例：
```
switch (a) {
case 0:
    b = 1;
    break;
case 1:
    b = 2;  // Non-compliant, missing ‘break’
default:
    b = 3;
    break;
}
```
例外，相连的case标签不受本规则约束：
```
switch (c) {
case 'a':   // OK
case 'b':
    do_something(c);
    break;
}
```
在少数情况下，如果确实不能有break语句，在C\+\+语言中可使用\[\[fallthrough\]\]注明，或用明确的注释说明情况：
```
switch (a) {
case 1:
    do_something_special();
    [[fallthrough]];  // OK, since C++17
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

### <span id="ID_switch_brace">▌R9.5.14 switch语句应该用大括号括起来</span>

ID_switch_brace&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

switch语句应为包含多条语句的复合语句，且用大括号括起来，否则不应选用switch语句。  
  
示例：
```
switch (x)  // Non-compliant
    case 0:
        foo(x);
```
应改为if语句：
```
if (x == 0) {  // Compliant
    foo(x);
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

### <span id="ID_switch_forbidNest">▌R9.5.15 switch语句不应嵌套</span>

ID_switch_forbidNest&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: control suggestion

<hr/>

嵌套的switch语句会使程序变得复杂，较难看出哪个case针对哪个变量，不利于维护。  
  
示例：
```
void foo(int n, int m) {
  switch (n) {
  case 1:
    ....
  case 2:
    switch (m) {  // Non-compliant
    case 3:       // Confusing
      ....
    case 4:
      ....
    }
  default:
    ....
  }
}
```
不同switch的case交织在了一起，较难看出哪个case对应哪个变量，应尽量不使用嵌套，或将内嵌的switch语句抽取成一个函数，使代码更清晰。
<br/>
<br/>
<br/>

### <span id="control.try">9.6 Try</span>

### <span id="ID_try_emptyBlock">▌R9.6.1 不应存在空的try块</span>

ID_try_emptyBlock&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

空的try块是毫无意义的，有可能是残留代码或功能未实现。  
  
示例：
```
try {
    // Empty block or some code commented out
} catch (Exception& e) {
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

### <span id="ID_try_disorderedEllipsis">▌R9.6.2 catch块序列中catch-all块（ellipsis handler）应位于最后</span>

ID_try_disorderedEllipsis&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

catch块序列中catch\-all块（ellipsis handler）应位于最后，否则其后的catch块将失去作用。  
  
示例：
```
try {
    foo();
} catch (...) {  // Non-compliant
    ....
} catch (const E&) {
    ....
}
```
应改为：
```
try {
    foo();
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

### <span id="ID_try_disorderedHandlers">▌R9.6.3 catch块序列中针对派生类的应排在前面，针对基类的应排在后面</span>

ID_try_disorderedHandlers&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: control error

<hr/>

catch块序列中针对派生类的应排在前面，针对基类的应排在后面，如果违反这个顺序，针对派生类的catch块将失去作用。  
  
示例：
```
class B { .... };
class D: public B { .... };

try {
    foo();
} catch (const B&) {
    ....
} catch (const D&) {  // Non-compliant, dead code
    ....
}
```
应改为：
```
try {
    foo();
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

### <span id="ID_try_forbidNest">▌R9.6.4 try块不应嵌套</span>

ID_try_forbidNest&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: control suggestion

<hr/>

嵌套的try\-catch结构会使程序的控制流变得复杂，较难看出异常究竟是从哪个try块抛出在哪个catch块被处理，不利于维护。
<br/>
<br/>

#### 参考
C++ Core Guidelines E.17  
<br/>
<br/>

### <span id="control.catch">9.7 Catch</span>

### <span id="ID_catch_value">▌R9.7.1 应通过引用捕获异常</span>

ID_catch_value&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

如果按传值的方式捕获异常会造成不必要的复制开销，也可能产生对象切片问题；如果通过指针捕获异常，会增加不必要的内存管理开销，通过引用捕获异常才是合理的方式。  
  
示例：
```
try {
    ....
} catch (Exception e) {   // Non-compliant
    ....
}
```
例中Exception是异常类，用传值的方式捕获异常是不符合要求的。  
  
应改为：
```
try {
    ....
} catch (Exception& e) {   // Compliant
    ....
}
```
通过指针捕获异常也是不符合要求的，可参见ID\_throwPointer中的示例与讨论。
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

### <span id="ID_catch_slicing">▌R9.7.2 捕获异常时不应产生对象切片问题</span>

ID_catch_slicing&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

如果按传值的方式捕获多态类的异常对象，会使对象的多态性失效，造成错误的异常处理。  
  
本规则是ID\_catch\_value与ID\_objectSlicing的特化。  
  
示例：
```
class Exception {
public:
    Exception();
    virtual ~Exception();
    virtual const char* what() const { return nullptr; }
};

void foo() {
    try {
        // Objects derived from Exception may be thrown...
    }
    catch (Exception e) {  // Non-compliant
        log(e.what());
    }
}
```
如果例中Exception类是所有异常类的基类，不论哪个异常被捕获，what函数只能返回nullptr，丧失了多态性，造成了异常的错误处理。
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

### <span id="ID_catch_emptyBlock">▌R9.7.3 不应存在空的catch块</span>

ID_catch_emptyBlock&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: control suggestion

<hr/>

空的catch块掩盖了异常，不利于问题的排查与纠正，应至少添加日志记录等操作。  
  
示例：
```
void foo() {
    try {
        __foo();
    }
    catch (...)
    {}  // Non-compliant, very bad
}
```
这样做并不能真正提高程序的稳定性，相当于逃避了问题，而且掩盖没有被处理的异常也可能会影响到其它方面的正常运行。  
  
对于要求不能抛出异常的接口，不妨按下例处理，记录意料之外的异常情况，以便问题的排查：
```
void foo() noexcept {
    try {
        __foo();
    }
    catch (...) {  // For all unexpected exceptions
        log_unexpected_and_exit(__FILE__, __LINE__, "some messages");
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

### <span id="ID_catch_justRethrow">▌R9.7.4 捕获异常后不应直接重新抛出异常，需对异常进行有效处理</span>

ID_catch_justRethrow&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

捕获异常后将其直接重新抛出是没有意义的，还会造成不必要的开销。  
  
示例：
```
void foo() {
    try {
        bar();
    }
    catch (...) {  // Non-compliant
        throw;
    }
}
```
例中的catch块是没有意义的，应将其去掉。
<br/>
<br/>
<br/>

### <span id="ID_catch_generic">▌R9.7.5 不应捕获过于宽泛的异常</span>

ID_catch_generic&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

捕获过于宽泛的异常如std::exception、std::logic\_error、std::runtime\_error等，使异常处理失去针对性，无法做到具体问题具体处理，而且很可能将本不应处理的异常一并捕获，造成混乱。  
  
相关讨论详见ID\_throwGenericException。  
  
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

### <span id="ID_catch_nonExceptionType">▌R9.7.6 不应捕获非异常类型</span>

ID_catch_nonExceptionType&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: control warning

<hr/>

字符串或变量以及非异常相关的对象不应被当作异常捕获，否则意味着异常相关的设计是不健全的。  
  
相关讨论详见ID\_throwNonExceptionType。  
  
示例：
```
try {
    ....
} catch (int) {  // Non-compliant
    .....
}

try {
    ....
} catch (char*) {  // Non-compliant
    ....
}

try {
    ....
} catch (std::string&) {  // Non-compliant
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

## <span id="expression">10. Expression</span>

### <span id="expression.logic">10.1 Logic</span>

### <span id="ID_illIdentical">▌R10.1.1 不应出现不合逻辑的重复子表达式</span>

ID_illIdentical&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

逻辑与、逻辑或、按位与、按位或的子表达式，三目表达式的两个分支子表达式不应重复，否则失去逻辑意义，这是一种很常见的错误，往往由复制粘贴造成。  
  
示例：
```
bool isValid(int* pDat, char* pStr) {
    return pDat != NULL && pDat != NULL;  // Non-compliant
}

unsigned getFlags() {
    return FLAG0 | FLAG1 | FLAG0;  // Non-compliant
}

void onShow(CSettings& setting) {
    setting.valid()? setting.onShow(): setting.onShow();  // Non-compliant
}
```
例中重复的子表达式都是有问题的。  
因为这种问题往往由复制粘贴引起，所以对于这类问题的修正，不要只是删去重复的子表达式，要考虑是否有某些项被漏掉。  
  
例外，当重复的子表达式有一定副作用时，如：
```
if (charRead() == 'a' && charRead() == 'a') {
    ....
}
```
如果这段代码的目的是从文件中判断并读取相邻的两个字符，就不能算是逻辑错误，但这并不是好的编程风格，具体讨论可参见ID\_shortCircuitSideEffect。
<br/>
<br/>

#### 参考
CWE-682  
<br/>
<br/>

### <span id="ID_conflictCondition">▌R10.1.2 逻辑表达式中各子表达式不应自相矛盾</span>

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
    ....          // ‘i’ is not changed
    if (i > 0) {  // Non-compliant
        ....
    }
}
```
例中变量i初始化为0后在没有被修改过的情况下，仍对其进行判断是没有意义的，这显然是逻辑错误。
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
true或1等常量可用在while或do\-while循环的条件中，false或0等常量可用在do\-while循环的条件中。
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

### <span id="ID_shortCircuitSideEffect">▌R10.1.6 短路规则下不应存在有副作用的子表达式</span>

ID_shortCircuitSideEffect&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: expression suggestion

<hr/>

对于逻辑表达式，求值时从左至右计算子表达式的值，当可以确定整个表达式的值时，即使还有未计算的子表达式，也会立即结束计算，这称为“短路规则”。  
  
&&、||的右子表达式会受到左子表达式的影响，可能不会被执行，即如果右子表达式有副作用，其副作用可能不会生效，这使表达式的复杂度显著增加，极易引入难以察觉的错误。  
  
示例：
```
if (a == foo || b == bar++) {  // Non-compliant
    ....
}
```
如果a == foo为真，不论b是否等于bar\+\+，整个条件表达式的值一定为真，所以b == bar\+\+不一定会被执行，bar的自增也不会被执行。  
  
可参见ID\_missingSideEffect中对“副作用”的介绍。
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

### <span id="ID_simplifiableCondition">▌R10.1.7 逻辑表达式应尽量保持简洁明了</span>

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

当三元表达式的分枝是true或false时可化简为逻辑表达式，应化简代码。  
  
示例：
```
void foo(int a) {
    if (a > 123? true: false) {    // Non-compliant, too verbose
        ....
    }
}

bool bar(int a) {
    return a > 123? fun(): false;  // Non-compliant, verbose
}
```
应改为：
```
void foo(int a) {
    if (a > 123) {
        ....
    }
}

bool bar(int a) {
    return a > 123 && fun();
}
```
<br/>
<br/>
<br/>

### <span id="expression.calulation">10.2 Calulation</span>

### <span id="ID_missingSideEffect">▌R10.2.1 不应出现没有效果的语句</span>

ID_missingSideEffect&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

不能对程序状态产生影响的语句称为无副作用（side effect）的语句，也可以说是没有效果的语句，往往属于笔误或调试痕迹，应当修正或去除。  
  
示例（设a、b、p为变量或指针）：
```
a == b;  // Non-compliant
```
单纯的判等是没有副作用的，很可能是赋值语句的笔误。  

```
*p++;    // Non-compliant
```
单纯从某个地址进行读取是没有副作用的，这是一种对运算符优先级理解不当造成的常见错误，应改为(\*p)\+\+;  

```
p->fun;  // Non-compliant
```
由变量名或无实参列表的函数名作为一个语句是没有副作用的，此语句应改为正确的函数调用。  

```
+a;      // Non-compliant
```
正号是没有副作用的，此句很可能应为\+\+a;  
  
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

### <span id="ID_invalidCommaSubExpression">▌R10.2.2 逗号表达式的子表达式应具有必要的副作用</span>

ID_invalidCommaSubExpression&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

逗号表达式的子表达式应具有必要的副作用，否则没有意义。  
  
示例：
```
void foo(int& a, int& b) {
    a, b = 0, 1;   // Non-compliant
}
```
例中逗号表达式有3个子表达式，只有第2个子表达式有效，第1和第3个没有意义。  
  
应改为：
```
void foo(int& a, int& b) {
    a = 0, b = 1;  // Compliant, but bad
}
```
本规则不建议使用逗号表达式，将逗号表达式拆分成合理的语句是更好的选择。  

```
void foo(int& a, int& b) {
    a = 0;
    b = 1;         // Compliant, good
}
```
<br/>
<br/>

#### 相关
ID_forbidCommaExpression  
<br/>
<br/>

### <span id="ID_confusingAssignment">▌R10.2.3 赋值表达式中不应存在被赋值变量的自增或自减运算</span>

ID_confusingAssignment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

设a为变量，不应出现下列形式的赋值表达式：
```
a = a++;       // Non-compliant
a = ++a;       // Non-compliant
++a = a;       // Non-compliant
++a = ++a;     // Non-compliant
++a = a++;     // Non-compliant
```
这种形式的赋值非但令人费解，而且不同的编译器会给出不同的解释从而得到不同的执行结果。  

```
++a;           // Compliant
a += 1;        // Compliant
a = a + 1;     // Compliant
```
本规则是ID\_evaluationOrderReliance的特化。
<br/>
<br/>

#### 相关
ID_evaluationOrderReliance  
<br/>

#### 参考
C++ Core Guidelines ES.43  
SEI CERT EXP50-CPP  
<br/>
<br/>

### <span id="ID_evaluationOrderReliance">▌R10.2.4 各子表达式的求值不应依赖特定的顺序</span>

ID_evaluationOrderReliance&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

注意子表达式的求值顺序，不同的求值顺序不应导致主表达式有不同的结果。  
C语言和C\+\+语言（C\+\+17之前）对子表达式的求值顺序并没有明确的规定，由各编译器厂商自行决定。  
  
示例：
```
a[i] = ++i;         // Non-compliant
p->bar(p++);        // Non-compliant
foo(++i, ++i);      // Non-compliant
```
应去除不确定性，如：
```
foo(i + 1, i + 2);  // Compliant
i += 2;             // Compliant
```
注意，参数的求值顺序和进栈顺序是不同的概念，进栈顺序可以通过stdcall、cdecl等调用约定指明，而求值顺序在C\+\+17之前是没有明确标准的。  
  
即使遵循C\+\+17标准，也不应出现这种代码，不同的人对这种代码往往会有不同的理解，对维护工作产生较大困扰。
<br/>
<br/>

#### 相关
ID_confusingAssignment  
<br/>

#### 参考
CWE-758  
C++ Core Guidelines ES.43  
C++ Core Guidelines ES.44  
<br/>
<br/>

### <span id="ID_unexpectedPrecedence">▌R10.2.5 注意运算符优先级，不可产生非预期的结果</span>

ID_unexpectedPrecedence&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

本规则目前主要针对三目运算符，当问号左侧为二元表达式，且与问号直接相临的子表达式为bool型时，情况较为可疑，很可能是忘了加括号。  
  
示例：
```
int fun(bool b) {
    return 1 + b? 2: 3;  // Rather suspicious, may be ‘(1 + b)? 2: 3’
}
```
<br/>
<br/>

#### 参考
CWE-783  
<br/>
<br/>

### <span id="ID_illPtrDiff">▌R10.2.6 不在同一数组中的指针不可比较或相减</span>

ID_illPtrDiff&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

不在同一数组中的指针比较或相减属于逻辑错误，会导致标准未定义的问题。  
  
示例：
```
ptrdiff_t foo() {
    int a = 0, b = 0;
    int arr0[10] = {};
    int arr1[10] = {};

    if (&a < &b) {  // Non-compliant
        ....
    }
    return &arr1[5] – arr0;  // Non-compliant
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

### <span id="ID_illBoolOperation">▌R10.2.7 bool型变量或表达式不应参与大小比较、位运算、自增自减等运算</span>

ID_illBoolOperation&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

bool值只能为真或假，不具有“大小”这种逻辑意义，所以bool型变量或表达式参与大小比较、位运算、自增自减等运算都是不合理的。  
  
示例：
```
bool foo(unsigned flags, unsigned flag) {
    return flags & flag != 0;  // Non-compliant
}
```
由于!=的优先级高于&，所以例中的return语句相当于先判断flag是否为0，再将这个bool型的结果与flags按位与，这是没有意义的。  
  
应改为：
```
bool foo(unsigned flags, unsigned flag) {
    return (flags & flag) != 0;  // Compliant
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

### <span id="ID_illFormedCompoundAssignment">▌R10.2.8 不应出现复合赋值的错误形式</span>

ID_illFormedCompoundAssignment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

如下形式的复合赋值表达式（设a和x为变量或表达式）：
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

### <span id="ID_illSelfCompoundAssignment">▌R10.2.9 &=、|=、-=、/=、%=左右子表达式不应相同</span>

ID_illSelfCompoundAssignment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

&=、|=左右子表达式如果相同则没有任何效果，\-=、/=、%=左右子表达式相同则结果总为1或0，这种表达式往往意味着笔误或逻辑错误。  
  
示例（设a为变量或表达式）：
```
a &= a;  // Non-compliant, no effect
a |= a;  // Non-compliant, no effect
```
如果目的是为了清零或置1，也不建议使用下列表达式：
```
a -= a;  // Non-compliant, tedious
a /= a;  // Non-compliant, low efficiency
a %= a;  // Non-compliant, low efficiency
```
对于高级语言来说，应该直接将变量赋值为0或1，而不是采用更繁琐甚至低效的方式。
<br/>
<br/>

#### 参考
CWE-682  
<br/>
<br/>

### <span id="ID_oddNullAssignment">▌R10.2.10 不应使用NULL对非指针变量赋值或初始化</span>

ID_oddNullAssignment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

标识符NULL只应该用来表示空指针，否则会对代码阅读造成误导，而且也可能是书写错误。  
  
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

### <span id="ID_stickyAssignmentOperator">▌R10.2.11 赋值运算符与单目运算符之间应有空格，单目运算符与变量或表达式之间不应有空格</span>

ID_stickyAssignmentOperator&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

如果赋值运算符与之后的\+、\-、\*、!、&、\~等单目运算符之间没有空格，而单目运算符与变量或表达式之间有空格，是一种非常怪异的格式，造成阅读困难，而且也可能是\+=、\-=、\*=、&=、\~=等复合赋值相关的笔误。  
  
示例：
```
int a(1), b(0);

b =+ a;    // Non-compliant
b =- a;    // Non-compliant
b =~ a;    // Non-compliant
b =! a;    // Non-compliant

b = +a;    // Compliant
b = -a;    // Compliant
b ~= a;    // Compliant
```
<br/>
<br/>

#### 参考
CWE-480  
<br/>
<br/>

### <span id="ID_suspiciousCompoundAssignment">▌R10.2.12 避免出现复合赋值的可疑形式</span>

ID_suspiciousCompoundAssignment&emsp;&emsp;&emsp;&emsp;&nbsp;:question: expression suspicious

<hr/>

如下形式的复合赋值表达式（设a和x为变量或表达式）：
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
a += a + x;  // Rather suspicious
```
应改为：
```
a = a + x;  // OK
a = 2 * a + x;  // OK
a = a + (a + x);  // OK
```
<br/>
<br/>

#### 参考
CWE-682  
<br/>
<br/>

### <span id="ID_selfAssignment">▌R10.2.13 赋值运算符左右子表达式不应重复</span>

ID_selfAssignment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

对自身赋值是没有逻辑意义的，往往是笔误或残留代码。  
  
示例：
```
a = .... = b = a = ....  // Non-compliant
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
例中构造函数对成员a的赋值是无效的，应改为this\->a = a;  
  
有时这种代码是为了消除编译警告，编译器可能会报出没有被用到的函数参数，有人将参数赋值给自身从而去除警告，但毕竟引入了没有实际意义的代码，改进方法可参见ID\_paramNotUsed。  
  
有时为了设置调试断点，但又找不到合适的位置，于是增加了这种代码作为断点，显然这种非正式的代码是不应被保留的。
<br/>
<br/>

#### 参考
CWE-682  
<br/>
<br/>

### <span id="ID_selfDivision">▌R10.2.14 除法运算符、求余运算符左右子表达不应重复</span>

ID_selfDivision&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

除法运算符、求余运算符左右子表达重复，结果总为1或0以及产生除零异常，这是没有意义的，往往是某种笔误。  
  
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

### <span id="ID_selfSubtraction">▌R10.2.15 减法运算符左右子表达式不应重复</span>

ID_selfSubtraction&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

与自身做减法，结果总为0，往往是某种笔误。  
  
示例：
```
ptrdiff_t distance(const int* p0, const int* p1) {
    return p0 - p0;  // Non-compliant
}
```
例中减法表达式是没有意义的，很可能是p1被误写成了p0，应改为：
```
ptrdiff_t distance(const int* p0, const int* p1) {
    return p0 – p1;  // Compliant
}
```
<br/>
<br/>

#### 参考
CWE-682  
<br/>
<br/>

### <span id="ID_selfExclusiveOr">▌R10.2.16 异或运算符左右子表达式不应重复</span>

ID_selfExclusiveOr&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

与自身异或，结果总为0，而且可能意味着某种错误。  
  
对变量的清零，有一种惯用写法：
```
a = a ^ a;
```
但这种复杂的写法在C/C\+\+等高级语言中已不再提倡，a ^= a也不再提倡。应将变量直接赋值为0，编译器会作更好的优化。
<br/>
<br/>

#### 参考
CWE-682  
<br/>
<br/>

### <span id="ID_minusOnUnsigned">▌R10.2.17 负号不应作用于无符号整数</span>

ID_minusOnUnsigned&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

负号作用于无符号整数，结果仍是无符号整数，令人费解易产生意料之外的错误。  
  
示例：
```
unsigned int a = 1;
long long b = -a;    // Non-compliant, b is 4294967295, confusing
```
例外：  
unsigned char、unsigned short等可以promote为int的无符号类型被放过。  
\-1U、\-1UL、\-1ULL作为UINT\_MAX、ULONG\_MAX、ULLONG\_MAX的惯用简写形式被放过。
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

### <span id="ID_repeatedUnaryOperators">▌R10.2.18 不应重复使用一元运算符</span>

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
两个连续的!运算符是向bool型转换的惯用方法，不算违规，如：
```
bool e = !!a;   // Compliant
```
但如果超过两个!还是会算作违规，如：
```
bool f = !!!a;  // Non-compliant
```
<br/>
<br/>
<br/>

### <span id="ID_constValueOverflow">▌R10.2.19 常量表达式结果不应溢出</span>

ID_constValueOverflow&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

常量表达式结果不应溢出。  
  
示例：
```
auto a = 0xffffffff + 1;  // Non-compliant
```
<br/>
<br/>

#### 参考
CWE-190  
<br/>
<br/>

### <span id="ID_bitwiseOperOnSigned">▌R10.2.20 位运算符不应作用于有符号整数</span>

ID_bitwiseOperOnSigned&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

有符号整数的符号位对于位运算符来说是没有逻辑意义的，对负数进行位运算往往意味着逻辑错误。  
  
示例：
```
int foo(signed s, unsigned u) {
    return s & u;  // Non-compliant
}

int bar(signed s, unsigned u) {
    if (s < 0) {
        auto a = s << u;  // Non-compliant, undefined
        auto b = s >> u;  // Non-compliant, implementation-defined
        return a + b;
    }
    return 0;
}
```
注意，对负数进行移位运算会导致标准之外的问题。
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

### <span id="ID_illShiftCount">▌R10.2.21 移位数量不可超过相关类型比特位的数量</span>

ID_illShiftCount&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

移位数量不可过大，否则会导致标准未定义的错误。  
  
示例：
```
uint64_t foo(uint32_t u) {
    return u << 32;  // Non-compliant
}
```
例中u为32位整型变量，将其左移32位并不能得到64位的整数，其结果一般为0，取决于编译器的具体实现。  
  
应改为：
```
uint64_t foo(uint32_t u) {
    return (uint64_t)u << 32;  // Compliant
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

### <span id="expression.comparison">10.3 Comparison</span>

### <span id="ID_illComparison">▌R10.3.1 比较运算应在正确的范围内进行</span>

ID_illComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

比较运算应在正确的范围内进行，否则易造成恒为真或恒为假的无效结果。判断无符号变量小于0或大于等于0是没有意义的，或者用来比较的数值超出了变量的取值范围也是没有意义的。  
  
示例：
```
void foo(string& txt, string& sep) {
  size_t n = txt.length();
  size_t start = txt.find_first_not_of(sep);
  while (start >= 0 && start < n) {  // Non-compliant, start >= 0 is always true
      ....
  }
}
```
其中start >= 0恒为真，是不必要的条件。  
  
又如：
```
typedef unsigned short X;
void fun(X x) {
    if (x == -1) {  // Non-compliant, always false
        ....
    }
}
```
例中x为无符号短整型变量，其取值范围为\[0,65535\]，x == \-1永远为假。  
由C/C\+\+语言的类型转换机制，有符号和无符号的字符型及短整型变量与整数进行比较时，会将该变量转为整型再进行计算。本例中的x转为整型后恒为正数，\-1为负数，故不可能相等，即使该函数按fun(\-1)调用也不会得到正确的结果，因为这相当于整型的65535与整形的\-1相比较。  
  
又如，对于有符号字符型变量，与其进行比较的数值不在\[\-128，127\]之中时，也是无效的比较：
```
CodePage encodingDetect(const char* src) {
  char b0 = src[0];
  char b1 = src[1];
  char b2 = src[2];
  if (b0 == 0xef && b1 == 0xbb && b2 == 0xbf) {  // Non-compliant, always false
      return cpUtf8;
  }
  ....
}
```
和上个例子一样，即使b0的二进制绝对值确实为0xef，但由于类型转换，b0转为整型后为负数，0xef转为整型后为正数，故结果为假。  
将b0、b1、b2设为无符号字符类型可解决这个问题。本例中char为有符号字符类型，可通过扫描设置决定char是否有符号，默认为有符号类型。
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

### <span id="ID_illFloatComparison">▌R10.3.2 不应使用==或!=判断浮点数是否相等</span>

ID_illFloatComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

一般来说，除了可以计作 a \* 2<sup>n</sup>（a、n为整数）的浮点数值可以被精确存储之外，其他均为近似值。用==或!=判断浮点数(float、double、long double)是否相等往往得不到预期的结果。  
  
如0、1、2、3、1.5、1.25…可以被精确存储，而除此之外绝大部分数值如0.1、0.2、0.3…只能存储其近似值。  
  
示例：
```
float f = 1;
f /= 10;
if (f == 0.1) {   // Non-compliant, donot use ‘==’ or ‘!=’
    cout << "OK";
} else {
    cout << "Oops";
}
```
输出Oops，这是因为f == 0.1在运算时将变量和常量转为double型，而转换过程中生成了两个不同的对0.1的近似值（如0.10000000149011611938和0.10000000000000000555），其结果自然为false。  
这非常容易造成意料之外的混乱，所以判断浮点数是否相等不应直接使用==或!=运算符，即使浮点数可以被精确存储。  
  
解决方法：
```
bool feq(float a, float b, float e = 0.0001f) {
    return fabs(a - b) < e;
}
```
利用feq函数判断浮点数是否相等，如果两个浮点数的差值非常小则可以认为相等，其中fabs为计算浮点数差值绝对值的函数，如果差值绝对值小于e则认为相等，否则不等。
```
if (feq(f, 0.1)) {  // Compliant
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

### <span id="ID_illPtrStrComparison">▌R10.3.3 指针不可与字符串常量直接比较</span>

ID_illPtrStrComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

直接比较指针和字符串常量的结果往往总是false，应改用字符串比较函数。  
  
示例：
```
bool is_name(const char* p) {
    return p == "bar";  // Non-compliant
}
```
如果例中is\_name函数只接受常量字符串作为参数，该函数在某些环境中也可能正常工作，如：
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
    return !strcmp(p, "bar");  // Compliant
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

### <span id="ID_differentEnumComparison">▌R10.3.4 不同类型的枚举值不应进行比较</span>

ID_differentEnumComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

比较不同类型的枚举值相当于比较不同类别的事物，没有逻辑意义，往往是设计缺陷或逻辑错误。  
  
示例：
```
enum Fruit { apple, orange, banana };
enum Pet { hamster, chipmunk, chinchilla };

int foo(Fruit f, Pet p) {
    return p > f;  // Non-compliant
}
```
<br/>
<br/>
<br/>

### <span id="ID_selfComparison">▌R10.3.5 比较运算符左右子表达式不应重复</span>

ID_selfComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

与自身的比较是没意义的，往往是某种笔误。  
  
示例（设a为变量或表达式）：
```
a == a   // Non-compliant
a != a   // Non-compliant
a > a    // Non-compliant
a >= a   // Non-compliant
a < a    // Non-compliant
a <= a   // Non-compliant
```
例外：  
如果a为浮点数类型，判断a是否为无效值（NaN）的惯用方法是判断a != a是否为真，对于这种情况可以放过。
<br/>
<br/>

#### 参考
CWE-1025  
<br/>
<br/>

### <span id="ID_successiveComparison">▌R10.3.6 比较运算不应作为另一个比较运算的直接子表达式</span>

ID_successiveComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

在C/C\+\+语言中，连续的比较运算是没有意义的，本规则是ID\_illBoolOperation的一种特化。  
  
示例：
```
bool foo(int a, int b, int c) {
    return a >= b >= c;  // Non-compliant, logic error
}
```
例中a >= b的结果为bool型，bool型数据是没有大小概念的，这个结果再与c比较大小是没有意义的。  
  
如果是判断两个bool表达式是否相等，可以被本规则放过，如：
```
a > b == true       // Compliant, but odd
a != b != true      // Compliant, but odd
(a > b) == (c > d)  // Compliant, but complex
```
这种表达式虽然没有逻辑错误，但其复杂的形式并不值得提倡。
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

### <span id="ID_accessPaddingData">▌R10.3.7 不应访问填充数据</span>

ID_accessPaddingData&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

变量之间可能存在填充数据，这种数据只为实现“内存对齐”而无数值意义，而且填充数据的值是标准未声明的。  
  
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
如果按常见的4或8字节对齐，A的成员a和b之间会存在填充数据，填充数据参与比较将得到错误的结果。  
  
应改为：
```
void foo(A* x, A* y) {
    if (x->a == y->a && x->b == y->b) {  // Compliant
        ....
    }
}
```
即使成员之间没有填充数据也不应使用memcmp等函数比较，应实现类型明确的比较接口以供调用，否则很容易产生错误。
<br/>
<br/>

#### 依据
ISO/IEC 9899:2011 6.2.6.2(5)-unspecified  
<br/>
<br/>

### <span id="ID_strcmpNoneZeroComparison">▌R10.3.8 不可臆断返回值的意义</span>

ID_strcmpNoneZeroComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

对接口的使用应遵循接口文档，不可臆断返回值的意义，否则造成逻辑错误。  
  
示例：
```
void foo(const std::string& s) {
    if (s.find("bar")) {   // Non-compliant
        ....
    }
}
```
例中find函数返回"bar"在s中的位置，当s中不存在"bar"时将返回std::string::npos，将find函数的返回值转为bool型是没有逻辑意义的。  
  
应改为：
```
void foo(const std::string& s) {
    if (s.find("bar") != std::string::npos) {   // Compliant
        ....
    }
}
```
想当然地认为返回0表示失败或不存在，非0表示成功或存在，是造成错误的常见原因。  
  
又如：
```
bool gt(const char* a, const char* b) {
    return strcmp(a, b) == 1;  // Non-compliant
}
```
strcmp函数的返回值可以是等于、大于或小于0的任意整数，分别对应字符串的等于、大于或小于关系，认为其只能返回0、1或\-1是一种常见的误解。  
  
应改为：
```
bool gt(const char* a, const char* b) {
    return strcmp(a, b) > 0;   // Compliant
}
```
strcmp、wcscmp以及memcmp等函数不应与0之外的任何值比较。  
  
下列函数的返回值不应与0比较，也不应转为bool型：  
 ● open、create等Linux系统调用，失败时返回负数，成功时返回非负数  
 ● CreateFile、CreateNamedPipe等Windows API，失败时返回INVALID\_HANDLE\_VALUE，而不是0  
 ● HRESULT型Windows API返回值，负数表示失败、非负数表示成功  
  
另外，有相当一部分函数成功时返回0，失败时返回非0，如access、chmod、rename等Linux系统调用，成功时返回0，失败时返回\-1，故不可将其返回值误当作bool型使用。
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

### <span id="expression.call">10.4 Call</span>

### <span id="ID_inconsistentFormatArgNum">▌R10.4.1 C风格的格式化字符串与其参数的个数应严格一致</span>

ID_inconsistentFormatArgNum&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

对于C风格的格式化函数（如printf、fprintf、sprintf等），格式化字符串与其对应参数的个数应严格一致，否则会引发严重的运行时堆栈错误。  
  
示例：
```
void foo(int type, const char* msg) {
    printf("Error (type %d): %s\n", type);  // Non-compliant
}
```
示例代码的格式化参数需要两个，但只传入一个，参数msg之外的不相关栈信息也会被读入。  
  
由于可变参数列表自身的局限，很难在编译时发现这种问题，而且在实现上也很难针对错误情况进行处理，故建议在C\+\+代码中禁用C风格的格式化函数。
<br/>
<br/>

#### 相关
ID_inconsistentFormatArgType  
ID_forbidCStringFormat  
<br/>

#### 依据
ISO/IEC 14882:2011 7.16.1.1(2)-undefined  
<br/>

#### 参考
SEI CERT FIO47-C  
<br/>
<br/>

### <span id="ID_inconsistentFormatArgType">▌R10.4.2 C风格的格式化字符串与其参数的类型应严格一致</span>

ID_inconsistentFormatArgType&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

对于C风格的格式化函数（如printf、fprintf、sprintf等），格式化字符串与其对应参数的类型应严格一致，否则会引发严重的运行时堆栈错误。  
  
示例：
```
void foo(const std::string& msg) {
    printf("Message: %s\n", msg);  // Non-compliant
}
```
例中%s要求对应char\*型参数，则msg实际上是std::string类型，造成栈读取错误。  
  
应改为：
```
void foo(const std::string& msg) {
    std::cout << "Message: " << msg << std::endl;  // Compliant
}
```
由于可变参数列表自身的局限，很难在编译时发现这种问题，而且在实现上也很难针对错误情况进行处理，故建议在C\+\+代码中禁用C风格的格式化函数。
<br/>
<br/>

#### 相关
ID_userObjectAsVariadicArgument  
ID_inconsistentFormatArgNum  
ID_forbidCStringFormat  
<br/>

#### 依据
ISO/IEC 14882:2011 7.16.1.1(2)-undefined  
<br/>

#### 参考
CWE-686  
SEI CERT FIO47-C  
<br/>
<br/>

### <span id="ID_userObjectAsVariadicArgument">▌R10.4.3 非基本类型的对象不应传入可变参数列表</span>

ID_userObjectAsVariadicArgument&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

非基本类型的对象与可变参数列表的机制很难相容，如果这种对象被传入可变参数列表，往往意味着错误。  
  
示例：
```
struct A {
    std::string s;
    operator const char*() const {
        return s.c_str();
    }
};

void foo(const A& a) {
    printf("%s\n", a);  // Non-compliant
}
```
例中即使对象a有转为const char\*的相关方法，在可变参数列表中也是无效的。  
  
应改为：
```
void foo(const A& a) {
    printf("%s\n", static_cast<const char*>(a));  // Compliant
}
```
传入可变参数列表的只应是基本类型的变量、枚举值或指针。标准相关的定义较为粗略，如果传入的对象存在自定义的拷贝、移动构造或析构等函数时，这种情况是“conditionally\-supported”也是“implementation\-defined”。   
  
由于可变参数列表不满足C\+\+严谨的类型理念，本规则禁止所有非基本类型的对象传入可变参数列表。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2011 5.2.2(7)-implementation  
ISO/IEC 14882:2011 7.16.1.1(2)-undefined  
<br/>

#### 参考
CWE-686  
SEI CERT EXP47-C  
<br/>
<br/>

### <span id="ID_returnValueIgnored">▌R10.4.4 函数返回值不应被忽略</span>

ID_returnValueIgnored&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

函数返回值不应被忽略，尤其是与资源分配、信息获取、状态判断有关的返回值。  
  
示例：
```
void foo(string& s) {
    s.empty();     // Non-compliant
}

void bar(unique_ptr<int>& p) {
    p.release();   // Non-compliant
}
```
另外，C\+\+中由用户添加的具有\[\[nodiscard\]\]属性的函数，返回值也不应被忽略，如：  

```
[[nodiscard]] int getStatus();

void baz() {
    getStatus();   // Non-compliant
}
```
<br/>
<br/>

#### 参考
MISRA C 2012 17.7  
MISRA C++ 2008 0-1-7  
<br/>
<br/>

### <span id="ID_objectSlicing">▌R10.4.5 避免对象切片</span>

ID_objectSlicing&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

将派生类对象复制为基类对象的行为称为“对象切片”，基类对象不再持有派生类的属性，不再遵循多态机制，意味着某种精度上的损失，往往会造成意料之外的错误。  
  
示例：
```
struct A { .... };
struct B: A { .... };

void foo(A);

A a;
B b;

a = b;   // Slicing
foo(b);  // Slicing
vector<A> v{b};  // Slicing
v.push_back(b);  // Slicing
```
尤其是函数传参或容器收纳对象时发生切片，会引起相当大的困惑，明明传入的是派生类对象，但虚函数都不生效了，所以要求多态性的接口或容器均应使用指针或引用。  
  
在少数情况下，对象切片可能也有其逻辑意义，但不建议“隐式切片”，应定义特定名称的函数标明这是一种特殊处理，如：
```
A a;
B b;

a = b;   // Bad, implicit slicing
a = to_base_object(b);   // OK
```
其中to\_base\_object是一个返回基类对象的函数，表示有意为之。
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

### <span id="ID_explicitDtorCall">▌R10.4.6 不应显式调用析构函数</span>

ID_explicitDtorCall&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: expression suggestion

<hr/>

显式调用析构函数使对象在生命周期未结束的时候被析构，在逻辑上使人困惑，而且在对象生命周期结束时其析构函数仍会被执行，有可能造成资源重复释放的问题。  
  
示例：
```
class A {
    int* p = new int[123];
public:
   ~A() {
        delete[] p;
    }
};

void fun() {
    A a;
    a.~A();  // Explicit call of destructor
             // ~A() twice called, crash...
}
```
例中对象a的析构函数被显式调用，之后a的生命周期结束会再次调用析构函数，造成内存的重复释放。  
  
修正方法：  
去掉显示调用，或保证相关资源不会被重复释放，如在delete\[\] p之后，将p设为nullptr。
<br/>
<br/>

#### 相关
ID_missingResetNull  
<br/>
<br/>

### <span id="ID_forbidCStringFormat">▌R10.4.7 在C++代码中禁用C风格字符串格式化方法</span>

ID_forbidCStringFormat&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: expression suggestion

<hr/>

C风格字符串格式化方法（如printf、sprintf等），即由可变参数列表实现的格式化方法是不安全的，在编译期无法限定参数的类型和数量，极易产生各种错误。  
  
示例：
```
size_t a = -1;
ptrdiff_t b = -2;
```
如果按16进制打印a，10进制打印b：
```
printf("%x %d", a, b);      // Non-compliant
printf("%lx %ld", a, b);    // Non-compliant
printf("%llx %lld", a, b);  // Non-compliant
```
size\_t、ptrdiff\_t等类型是由实现定义的，标准没有规定其是否一定对应unsigned int、long或long long类型，而%d、%lx、%llx只对应int、long、long long类型，所以示例代码都是不合理的。  
  
在C语言中正确的作法是a对应%zx，b对应%zd，如：
```
printf("%zx %zd", a, b);    // Non-compliant in C++, even if the result is correct
```
参数的类型与个数和占位符必须严格对应，否则就会导致标准未定义的错误，当参数较多时极易出错，C\+\+语言提供了更好的方法以规避这种问题，所以C\+\+代码中不应再使用C风格的字符串格式化方法。
```
std::cout << std::hex << a << ' ' << std::dec << b;  // Compliant
```
利用C\+\+输入输出流可有效规避printf等函数的问题，然而当参数较多时在形态上可能较为“松散”，其可读性可能不如printf等函数，对于这个问题可参见ID\_forbidVariadicFunction中的示例，可以用一种更安全的方法实现printf函数的功能。另外，C\+\+20的std::format也提供了更多的格式化方法。
<br/>
<br/>

#### 相关
ID_forbidVariadicFunction  
<br/>

#### 依据
ISO/IEC 14882:2011 7.16.1.1(2)-undefined  
<br/>

#### 参考
C++ Core Guidelines SL.io.3  
<br/>
<br/>

### <span id="expression.sizeof">10.5 Sizeof</span>

### <span id="ID_sizeof_sideEffect">▌R10.5.1 sizeof不应作用于有副作用的表达式</span>

ID_sizeof_sideEffect&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

sizeof只关注类型，其子表达式不会被求值，如果存在可以影响程序状态的运算符或函数调用，也不会有实际效果。  
  
示例：
```
int a = 0;
cout << sizeof(a++) << ' ';  // Non-compliant
cout << a << endl;  // What is output?
```
输出4 0，a\+\+不会被执行。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.3.3(1)  
ISO/IEC 14882:2011 5.3.3(1)  
ISO/IEC 14882:2017 8.3.3(1)  
<br/>

#### 参考
SEI CERT EXP52-CPP  
MISRA C 2004 12.3  
MISRA C 2012 13.6  
MISRA C++ 2008 5-3-4  
<br/>
<br/>

### <span id="ID_sizeof_zeroComparison">▌R10.5.2 sizeof的结果不应与0以及负数比较</span>

ID_sizeof_zeroComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

C\+\+标准规定，sizeof的结果为无符号整型，对于完整类型结果一定不为0，对于不完整类型则无法通过编译，所以将sizeof的结果与0甚至负数比较往往意味着逻辑错误。  
  
示例：
```
void foo(int* p) {
    if (sizeof(p)) {  // Non-compliant, always true
        ....
    }
}
```
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

### <span id="ID_sizeof_arrayParameter">▌R10.5.3 对数组参数不应使用sizeof</span>

ID_sizeof_arrayParameter&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

当函数的形式参数为数组时，实际上是一个指针，对这种参数使用sizeof无法获取到数组大小，往往意味着错误。  
  
示例：
```
void fun(char arr[32]) {
    memset(arr, 0, sizeof(arr));  // Non-compliant
}
```
例中参数arr是一个指针，而不是一个真实的数组。  
  
如果有必要将参数设为数组，建议使用引用的方式，如：
```
void fun(char (&arr)[32]) {
    memset(arr, 0, sizeof(arr));  // Compliant
}
```
这样sizeof(arr)的结果就会和预期一致。
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

### <span id="ID_sizeof_oddExpression">▌R10.5.4 sizeof不应作用于逻辑表达式</span>

ID_sizeof_oddExpression&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

sizeof作用于<、>、<=、>=、==、!=、&&、||等逻辑表达式为常见笔误，逻辑运算符往往应该移出sizeof表达式。  
  
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

### <span id="ID_sizeof_pointerDivision">▌R10.5.5 被除数不应是作用于指针的sizeof表达式</span>

ID_sizeof_pointerDivision&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

形如sizeof(p)/n的表达式往往是为了获取数组元素的个数，如果p是指针，sizeof(p)只是指针变量的大小，并不是数组的大小，所以这种表达式往往意味着逻辑错误。  
  
示例：
```
void foo(T* p) {
    size_t n = sizeof(p[0]);
    qsort(p, sizeof(p) / n, n, cmp);  // Non-compliant, logic error
}
```
例中sizeof(p)/n并不能获取p所指数组中元素的个数。  
  
本规则是ID\_sizeof\_pointer的特化，sizeof作用于指针是可疑的，如果这种表达式又作为被除数，就更加可疑了。
<br/>
<br/>

#### 相关
ID_sizeof_pointer  
<br/>

#### 参考
CWE-467  
<br/>
<br/>

### <span id="ID_sizeof_suspiciousAdd">▌R10.5.6 指针加减偏移量时计入sizeof是可疑的</span>

ID_sizeof_suspiciousAdd&emsp;&emsp;&emsp;&emsp;&nbsp;:question: expression suspicious

<hr/>

指针加减偏移量时会自动计入指针指向类型的大小，如果再计入sizeof的值，很可能是某种错误。  
  
示例：
```
int foo(int* p, int i) {
    return *(p + i * sizeof(*p));  // Rather suspicious
}
```
如果foo函数是为了获取指针p之后的第i个整数的值，那么这种实现是错误的，应改为：
```
int foo(int* p, int i) {
    return *(p + i);  // Right
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

### <span id="ID_sizeof_sizeof">▌R10.5.7 sizeof不应再作用于sizeof</span>

ID_sizeof_sizeof&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

sizeof(sizeof(....))等价于sizeof(size\_t)，在实际应用中没有任何必要写成连续sizeof的形式，属于常见笔误，多数由复制粘贴或错误的宏展开导致。  
  
示例：
```
void foo() {
    auto* sa = (PSecAttr)LocalAlloc(LPTR, sizeof(PSecAttr));
    sa->nLength = sizeof(sizeof(PSecAttr));  // Non-compliant, copy-paste error
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

### <span id="ID_sizeof_NULL">▌R10.5.8 C++代码中sizeof不应作用于NULL</span>

ID_sizeof_NULL&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

在C\+\+语言中，标识符NULL并不能有效区分整型常量0和空指针，sizeof(NULL)一类的表达式预期是获取指针变量的大小，而实际结果可能是整型变量的大小。  
  
示例：
```
auto a = sizeof(NULL);     // Non-compliant
```
应改为：
```
auto a = sizeof(nullptr);  // Compliant
```
不同的编译器编译后a的值是不同的，有些编译器把NULL当作指针，而有些会当作常量0，故应明确地使用nullptr。
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

### <span id="ID_sizeof_void">▌R10.5.9 sizeof不可作用于void</span>

ID_sizeof_void&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

void表示不存在的类型，也是不完整的类型，sizeof作用于void是没意义的，属于语言运用错误，也可能是sizeof(void\*)的笔误。  
  
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
    assert(sizeof(a));  // Non-compliant
    assert("some comments");  // Non-compliant
}
```
也不建议使用恒为假的断言表示异常，在C\+\+语言中应改用异常处理的方式。
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
ID_assertion/names：断言函数或宏的名称，如assert、_ASSERT_EXPR等，用“|”分隔  
<br/>
<br/>

### <span id="ID_sideEffectAssertion">▌R10.6.2 断言中的表达式不应有副作用</span>

ID_sideEffectAssertion&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: expression error

<hr/>

断言中的表达式如果有副作用，不能保证在所有编译设置下都有效。  
  
如标准断言assert会受宏NDEBUG的影响，当宏NDEBUG有定义时assert中的表达式不会被执行。  
  
示例：
```
void foo(int* p) {
    assert(++(*p) > 0);  // Non-compliant
}
```
表达式的副作用均应在assert之前完成：
```
void foo(int* p) {
    ++(*p);
    assert(*p > 0);      // Compliant
}
```
<br/>
<br/>

#### 配置
ID_assertion/names：断言函数或宏的名称，如assert、_ASSERT_EXPR等，用“|”分隔  
<br/>

#### 相关
ID_macro_sideEffectArgs  
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
    assert(c == b + 1);
}
```
本着使代码便于调试的理念展开工作，可有效降低测试及维护成本。
<br/>
<br/>

#### 配置
ID_assertion/maxLogicOperatorCount：断言表达式中“逻辑与”运算符的最大数量，超过则报出  
ID_assertion/names：断言函数或宏的名称，如assert、_ASSERT_EXPR等，用“|”分隔  
<br/>
<br/>

### <span id="expression.complexity">10.7 Complexity</span>

### <span id="ID_complexExpression">▌R10.7.1 表达式的运算符不应超过规定数量</span>

ID_complexExpression&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: expression suggestion

<hr/>

运算符超过一定数量意味着表达式过于复杂，易包含潜在的错误，更不利于调试与维护，应进行适当拆分。
<br/>
<br/>

#### 配置
ID_expression/maxLogicOperatorCount：逻辑运算符最大数量，超过则报出  
ID_expression/maxOperatorCount：运算符最大数量，超过则报出  
<br/>

#### 参考
C++ Core Guidelines ES.40  
<br/>
<br/>

### <span id="expression.other">10.8 Other</span>

### <span id="ID_oddNew">▌R10.8.1 new表达式只可用于赋值或当作参数</span>

ID_oddNew&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

new表达式只应作为“=”的直接右子表达式，或直接作为参数表达式，其他形式均有问题。  
  
本规则对replacement\-new暂不作要求。  
  
示例：
```
int& i = *new int(123);        // Non-compliant

if (new int[123]) {            // Non-compliant
  ....
}
char* p = new char[123] + n;   // Non-compliant
```
这些问题多数是笔误或错误的宏展开造成的。
<br/>
<br/>

#### 相关
ID_multiAllocation  
<br/>
<br/>

### <span id="ID_oddSubscripting">▌R10.8.2 数组下标应为整形表达式</span>

ID_oddSubscripting&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: expression warning

<hr/>

C/C\+\+语法规定，在数组取值时，数组下标可以在中括号的右侧也可以在左侧，然而这只是一种理论上的设计，在实际编码中，应采用约定俗成的方式，即数组的名称在中括号的左侧，下标在中括号的右侧。  
  
示例：
```
void fun() {
    int a[5] = {1, 2, 3, 4};
    4[a] = 5;                      // Non-compliant, use a[4] instead
    for (int i = 0; i < 4; i++) {
        cout << i + 1[a] << endl;  // Non-compliant, may be a[i + 1]
    }
}
```
由于a\[4\]相当于\*(a\+4)，所以与其等价的\*(4\+a)也应该可以解释为4\[a\]，但下标在左侧的写法容易造成运算符优先级相关的问题，将下标写在右侧才是符合惯例的方式。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 8.3.4(6)  
ISO/IEC 14882:2011 8.3.4(6)  
<br/>
<br/>

### <span id="ID_forbidCommaExpression">▌R10.8.3 禁用逗号表达式</span>

ID_forbidCommaExpression&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: expression suggestion

<hr/>

逗号表达式将多个语句合成一个表达式，易形成笔误并造成阅读困难。  
  
示例：
```
a, b, c = 0, 1, 2;    // Non-compliant
delete p0, p1;        // Non-compliant
foo((a, b), c);       // Non-compliant
return a, b, c;       // Non-compliant
```
例外：  
在for迭代声明中的第1个和第3个表达式中使用逗号表达式为惯用方式，但这种方式并不值得提倡，不妨根据配置项选择是否放过这种情况。
```
for (a = 0, b = 0; a < 100; a++, b++)  {  // let it go?
    bar(a, b);
}
```
<br/>
<br/>

#### 配置
ID_expression/allowCommaExpressionInForIteration：为true可以放过for迭代声明中的逗号表达式  
<br/>

#### 参考
MISRA C 2004 12.10  
MISRA C 2012 12.3  
MISRA C++ 2008 5-18-1  
<br/>
<br/>

### <span id="ID_redundantParentheses">▌R10.8.4 不应使用多余的括号</span>

ID_redundantParentheses&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: expression suggestion

<hr/>

多余的括号使代码显得繁琐，应当去掉。  
  
示例：
```
int foo(int x, int y, int z) {
    int a = ((x + y));  // Non-compliant
    a = ((a + z)) + 1;  // Non-compliant
    return ((a));       // Non-compliant
}
```
例外：  
由宏展开造成的多余括号不受本规则限制。
<br/>
<br/>
<br/>

## <span id="literal">11. Literal</span>

### <span id="ID_literal_suspiciousChar">▌R11.1 注意可疑的字符常量</span>

ID_literal_suspiciousChar&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: literal warning

<hr/>

注意字符常量的错误书写，如正反斜杠的误用，'\\n'误写为'/n'、'\\\\'误写为'//'等。  
  
由于C/C\+\+语言允许在单引号内写入多个字符来表示一个整形常量，如：
```
auto i = '/t';
cout << typeid(i).name() << i << endl;  // What is output?
```
这种语言特性可以让一些笔误通过编译，使问题不易察觉。  
  
示例：
```
auto* tab = wcschr(str, L'/t');
```
在某些编译器上执行结果和下列代码一样：
```
auto* tab = wcschr(str, L'/');
```
字符t将被忽略，这显然是错误的，L'/t'应改为L'\\t'。
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

在字符常量中，如果存在制表符（tab）或回车、换行、控制等特殊字符，应使用转义字符。  
  
示例：
```
char c = '	';  // Non-compliant
```
例中空白符为tab，由于tab可以按不同的宽度被展示，往往会被代码的阅读者误解为空格，而且经过复制粘贴后有可能会变成数目不确定的空格，使代码难以维护。  
  
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

在字符串常量中，如果存在制表符（tab）或回车、换行、控制等特殊字符，应使用转义字符。  
  
示例：
```
const char* s = "a	b";  // Non-compliant
```
例中空白符为tab，由于tab可以按不同的宽度被展示，往往会被代码的阅读者误解为空格，而且经过复制粘贴后有可能会变成数目不确定的空格，使代码难以维护。  
  
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

使用非标准转义字符会导致标准之外的问题，也很有可能是反斜杠忘了转义。  
  
示例：
```
string path("C:\Files\x.cpp");  // Non-compliant
```
例中\\F不是标准转义字符，\\x也不符合16进制转义字符的格式，这显然是路径中的反斜杠忘了转义。  
  
附C/C\+\+标准转义字符：
```
'\a'      // Alert
'\b'      // Backspace
'\f'      // Formfeed page break
'\n'      // New line
'\r'      // Carriage return
'\t'      // Horizontal tab
'\v'      // Vertical tab
'\\'      // Backslash
'\?'      // Question mark
'\''      // Single quotation mark
'\"'      // Double quotation mark
'\0'      // Null character
'\ddd'    // Any character, d is an octal number
'\xhh'    // Any character, h is a hex number
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

L、U、u、u8等字符串前缀表示不同的类型，连接在一起会导致标准之外的问题。  
  
示例：
```
auto* a = L"123" U"456";  // Non-compliant
auto* b = U"123" u"456";  // Non-compliant
```
应保持一致：
```
auto* a = L"123" L"456";  // Compliant
auto* b = U"123" U"456";  // Compliant
```
C\+\+03规定宽字符串与窄字符串连接是未定义的。  
C\+\+11规定一个字符串有前缀一个没有的话，结果以有前缀的为准，其它情况由实现定义。  
  
如：
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

如果含有拼写错误的常量字符串对用户可见，也可以认为是产品的一种bug，会对用户造成困扰，故应认真对待。  
  
示例：
```
void showMessage(int errCode) {
    if (errCode) {
        cout << "Error" << endl;
    } else {
        cout << "Successfull" << endl;  // Non-compliant
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

整数或浮点数常量的后缀应使用大写字母，否则'l'等小写字母极易与数字'1'混淆。  
  
示例：
```
long long a = 100ll;  // Non-compliant, misread as 10011
long double b = 100.l;  // Non-compliant, misread as 100.1
```
应改为：
```
long long a = 100LL;  // Compliant
long double b = 100.L;  // Compliant
```
后缀大小写混用的情况会使人更加困惑：
```
long long c = 100lL;  // Non-compliant, very bad
unsigned long long d = 100lLU;  // Non-compliant, very bad
```
其中，小写的l和大写的L混在了一起。  
  
应改为：
```
long long c = 100LL;  // Compliant
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

### <span id="ID_literal_forbidOct">▌R11.8 禁用8进制常量</span>

ID_literal_forbidOct&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: literal suggestion

<hr/>

8进制不像10进制那样符合人们的常规思维，也不像2进制或16进制那样便于展示数据的存储格式。  
而且在C/C\+\+语言中，8进制表示法只是在数值前置0，但这种写法用十进制也可以解释的通，被误用的可能性较大。  
  
示例：
```
const int K_0 = 5592;
const int K_1 = 0631;  // Non-compliant
const int K_2 = 3817;
const int K_3 = 4257;
```
为了格式上的对齐，错误的在10进制数前写0是常见笔误，例中K\_1的实际值为409。
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

整数常量后缀只应为L、LL、UL、ULL，浮点数常量的后缀只应为L、f或F，其它非标准后缀没有可移植性。  
  
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
std::string strs[] = {
    "123", "456", "789",
    "123", "456", "789"   // Rather suspicious, missing a comma?
    "123", "456", "789"
};
```
例中初始化列表第2行的"789"与第3行的"123"中间没有逗号，会连接成"789123"，显然是不符合预期的，这种问题属于常见笔误。  
  
又如：
```
void foo(const char* a);
void foo(const char* a, const char* b);

void bar() {
    foo("abc" "123");  // Rather suspicious, which ‘foo’ is right?
}
```
常量字符串连接的用途：  
 ● 字符串过长不便于显示时可将字符串拆成多个子串分行书写  
 ● 宏和字符串连接在一起完成一些更灵活的操作  
  
除此之外不应再将一个字符串拆成多个常量。
<br/>
<br/>
<br/>

### <span id="ID_literal_magicNumber">▌R11.11 代码中不应出现magic number</span>

ID_literal_magicNumber&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: literal suggestion

<hr/>

对于直接出现在代码中的字面数值（magic number），建议改用具有适当名称的常量表示，提高代码可读性，且便于维护。  
  
示例：
```
void fun() {
    for (int i = 0; i < 12345; i++) {  // Non-compliant, 12345 is magic
        bar(i);
    }
}
```
“12345”应改为具有名称的常量：
```
constexpr int maxId = 12345;
void fun() {
    for (int i = 0; i < maxId; i++) {  // Compliant
        bar(i);
    }
}
```
“12345”并不能明确表示其具体的含义，改为maxId后会好很多，而且当这种常量散落在代码的各个角落时，不便于统一修改，造成维护上的困难。
<br/>
<br/>

#### 配置
ID_literal/magicNumberDigitThreshold：数字常量的位数上限，超过则报出  
<br/>

#### 相关
ID_literal_magicString  
<br/>

#### 参考
C++ Core Guidelines ES.45  
<br/>
<br/>

### <span id="ID_literal_magicString">▌R11.12 代码中不应出现magic string</span>

ID_literal_magicString&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: literal suggestion

<hr/>

对于直接出现在代码中的字符串常量（magic string），建议改用具有适当名称的常量表示，提高代码可读性，且便于维护。  
  
示例：
```
void fun(const string& s) {
    if (s == "https://foo.net/bar") {  // Non-compliant
        foo("https://foo.net/bar");    // Non-compliant
    }
}
```
应改为：
```
const char fooUrl[] = "https://foo.net/bar";

void fun(const string& s) {
    if (s == fooUrl) {  // Compliant
        foo(fooUrl);    // Compliant
    }
}
```
当这种常量字符串散落在代码的各个角落时，不便于统一修改，造成维护上的困难。
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

多字符常量形式上与字符串常量相似，但类型为整型，易造成误用，而且不同编译器对这种常量的处理方式也有所不同，故建议禁用。  
  
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
这种常量在早期的程序实现中往往被当作一种枚举值来使用，现在应改用类型更加明确的enum class来实现。  
  
应改为：
```
enum class protocol {
    tcp, udp
};
void foo(protocol x) {
    if (x == protocol::tcp) {  // Compliant
        ....
    }
    else if (x == protocol::udp) {  // Compliant
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

### <span id="ID_literal_tooLarge">▌R11.14 字符串常量不应超过规定长度</span>

ID_literal_tooLarge&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: literal warning

<hr/>

大段的文本内容不应存在于代码之中，应存入数据文件中，并配以一定的加密措施，否则不利于维护，每次细微的调整都会牵扯到代码的编译。  
  
示例：
```
const char message[] = "... lots of chars ...";  // Non-compliant
```
<br/>
<br/>

#### 配置
ID_literal/maxStringLiteralLength：字符串常量长度上限，超过则报出  
<br/>

#### 相关
ID_literal_magicString  
<br/>
<br/>

## <span id="cast">12. Cast</span>

### <span id="ID_narrowCast">▌R12.1 避免类型转换造成的数据丢失</span>

ID_narrowCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

类型转换时应检查转换的结果是否正确，避免数据丢失等错误。  
  
示例：
```
void foo(int i) {
    char c = i;        // Non-compliant, check it first
    char d = (char)i;  // Non-compliant, irresponsible
    ....
}
```
下面给出判断转换后数据是否完整的简单示例：
```
template <class To, class From>
To checked_cast(From x) {
    To t = static_cast<To>(x);
    if ((From)t != x) {
        throw BadCast("....");
    }
    return t;
}

void foo(int i) {
    char c = checked_cast<char>(i);  // Compliant
    ....
}
```
在实际代码中还需要考虑将负数转为无符号数、浮点数转为整数等问题是否存在逻辑意义。
<br/>
<br/>

#### 参考
C++ Core Guidelines ES.46  
<br/>
<br/>

### <span id="ID_downCast">▌R12.2 避免向下类型转换</span>

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
例中foo接口对B类型进行特殊处理，是不利于维护的，当这种特殊处理较多时，应利用虚函数、重载或模板等方法进行合理重构。
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

### <span id="ID_ptrIntCast">▌R12.3 指针与整数不应相互转换</span>

ID_ptrIntCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

指针转为整数，或整数转为指针（尤其是小于64位的整数或有符号的整数），容易造成地址不完整、寻址错误、降低可移植性等问题。  
  
示例：
```
class A {
    std::vector<int> v;
public:
    void save(int* p) {
        v.emplace_back((int)p);  // Non-compliant
    }
};
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.2.10(4 5)-implementation  
ISO/IEC 14882:2011 5.2.10(4 5)-implementation  
<br/>

#### 参考
SEI CERT INT36-C  
MISRA C 2004 11.3  
MISRA C 2012 11.4  
MISRA C++ 2008 5-2-8  
MISRA C++ 2008 5-2-9  
<br/>
<br/>

### <span id="ID_qualifierCastedAway">▌R12.4 类型转换时不应去掉const、volatile等属性</span>

ID_qualifierCastedAway&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

类型转换时去掉const、volatile等属性使相关机制失去了意义，这往往意味着设计上的缺陷，也会导致标准未定义的错误。  
  
示例（选自C\+\+ Core Guidelines）：
```
void f(const int& x)
{
    const_cast<int&>(x) = 42;   // BAD
}

static int i = 0;
static const int j = 0;

f(i); // silent side effect
f(j); // undefined behavior
```
<br/>
<br/>

#### 参考
C++ Core Guidelines Type.3  
MISRA C 2004 11.5  
MISRA C 2012 11.8  
MISRA C++ 2008 5-2-5  
<br/>
<br/>

### <span id="ID_castNoInheritance">▌R12.5 不应强制转换无继承关系的类型</span>

ID_castNoInheritance&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

无继承关系的类型之间没有逻辑关系，不应强制转换，否则意味着设计缺陷或逻辑错误。  
  
示例：
```
class A { .... };
class B { .... };
class C: public A, public B { .... };

void foo() {
    A* a = new C;

    B* b0 = (B*)a;                    // Non-compliant
    B* b1 = reinterpret_cast<B*>(a);  // Non-compliant

    B* b2 = static_cast<B*>(a);       // Compliant, compile-time protected
    B* b3 = dynamic_cast<B*>(a);      // Compliant, run-time protected

    ....
}
```
例中A与B没有继承关系，C从A和B继承，指针a为A类型但实际指向C的实例，这种情况下将a直接强制转为B类型的指针将得到错误的结果，这个问题在实际代码中也很常见。  
  
本规则限制无继承关系的C风格类型转换以及reinterpret\_cast转换，放过static\_cast和dynamic\_cast转换，示例中的static\_cast转换将得到编译错误从而锁定问题，如果A和B是多态类型，用dynamic\_cast会得到正确的结果。  
  
例外：
```
class V { .... };

class U {
    ....
public:
    operator V*();
};

void bar(U* u) {
    V* v0 = (V*)u;                    // Compliant, but bad
    V* v1 = reinterpret_cast<V*>(u);  // Still non-compliant
    ....
}
```
例中U和V是无继承关系的类，但U实现了向V的转换方法，U和V之间存在逻辑关系，这时的C风格类型转换可被本规则放过，但不符合规则ID\_forbidCStyleCast，这种情况仍然不能使用reinterpret\_cast，可参见ID\_unsuitableReinterpretCast。  
  
注意，对于基本类型，指针或引用之间的转换受本规则限制，值之间的转换可被放过，但应注意转换造成的精度损失，可参见ID\_narrowCast。
<br/>
<br/>

#### 相关
ID_narrowCast  
ID_unsuitableReinterpretCast  
<br/>

#### 依据
ISO/IEC 14882:2003 5.2.10(7)-unspecified  
ISO/IEC 14882:2011 5.2.10(7)-unspecified  
<br/>

#### 参考
MISRA C 2012 11.3  
MISRA C++ 2008 5-2-7  
<br/>
<br/>

### <span id="ID_castNonPublicInheritance">▌R12.6 不应强制转换非公有继承关系的类型</span>

ID_castNonPublicInheritance&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

公有继承表示派生类是基类的一种扩展，而非公有继承往往表示派生类是基类的一种“例外”，这种情况下将派生类转为非公有的基类是有问题的。  
按C\+\+语言的本意，这种情况也是无法进行隐式类型转换的。  
  
示例：
```
class A { .... };
class B: private A { .... };

void bar(A* a);

void foo(B* b) {
    bar((A*)b);  // Non-compliant
}
```
例中B是对A的某种改造，如果再用A的定义去处理B的对象，显然是有问题的。
<br/>
<br/>
<br/>

### <span id="ID_castViolatePolymorphism">▌R12.7 多态类型与基本类型不应相互转换</span>

ID_castViolatePolymorphism&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

多态类型会维护虚表指针等用户不可见的数据以保证多态机制的执行，将其与基本类型转换会破坏这种机制。  
  
示例：
```
struct A {
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

    ....
};
```
例中A是多态类型，save函数将对象写入文件，fwrite的第一个参数this被隐式转为void\*，不符合本规则要求。对象的虚表指针等数据一并被写入文件，但虚表指针是运行时数据不应被保存，load函数从文件中读取对象便破坏了运行时数据。  
  
注意，正常的代码不应显式访问虚表指针等运行时数据，否则是不可移植的，标准只定义了多态类的行为，并未规定具体实现方式。
<br/>
<br/>

#### 参考
CWE-843  
<br/>
<br/>

### <span id="ID_charWCharCast">▌R12.8 不可直接转换不同的字符串类型</span>

ID_charWCharCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

char\*和wchar\_t\*直接转换并不进行字符集编码转换，往往意味着语言运用错误，对于C\+\+语言，要求char\*、wchar\_t\*、char16\_t\*和char32\_t\*之间均不可直接转换。  
  
本规则是ID\_castNoInheritance的特化。  
  
示例：
```
wchar_t* to_unicode(const char* str) {
    return (wchar_t*)str;  // Remarkably brave, but totally wrong
}
```
本规则限制相关的C风格转换以及reinterpret\_cast转换，由于unsigned char\*一般针对二进制数据，可被放过。  
  
注意，不应使用char\*作为二进制数据的类型，可参见ID\_plainBinaryChar。
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

### <span id="ID_arrayPointerCast">▌R12.9 避免类型转换造成的指针运算错误</span>

ID_arrayPointerCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

指针的逻辑大小与类型有关，不适当的类型转换会造成指针运算错误，应避免转换指向数组的指针。  
  
示例：
```
struct A { int x; };
struct B: A { int y; };

void foo(A* p, int n) {
    for (int i = 0; i < n; i++) {
        p[i].x = 123; 
    }
}

void bar() {
    B arr[10];
    foo(arr, 10);  // Bug
    ....
}
```
例中B类型的数组arr作为foo函数的参数，被转换成了基类指针，foo函数中对基类指针的运算将是错误的，B的成员y也会被错误的赋值。  
  
这是一个较为危险的问题，本规则针对所有数组相关的指针类型转换。
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 6.5.6(8)  
ISO/IEC 9899:2011 6.5.6(8)  
<br/>

#### 参考
C++ Core Guidelines C.152  
<br/>
<br/>

### <span id="ID_functionPointerCast">▌R12.10 对函数以及函数指针不应进行类型转换</span>

ID_functionPointerCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

将函数指针转换为其他类型会导致标准未定义的问题。  
  
示例：
```
void foo();

void* p0 = (void*)&foo;  // Non-compliant
void(*p1)(int) = (void (*)(int))&foo;  // Non-compliant
```
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.2.10(6)-undefined  
ISO/IEC 14882:2003 5.2.10(6)-unspecified  
<br/>

#### 参考
MISRA C 2004 11.1  
MISRA C 2012 11.1  
MISRA C++ 2008 5-2-6  
<br/>
<br/>

### <span id="ID_nonDynamicDownCast">▌R12.11 向下类型转换时应使用dynamic_cast</span>

ID_nonDynamicDownCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

向下类型转换如果不用dynamic\_cast难以保证安全性。  
  
示例：
```
class A { .... };
class B: public A { .... };

void foo(A* a) {
  bar((B*)a);  // Non-compliant
  baz(dynamic_cast<B*>(a));  // Compliant
}
```
如果参数a实际指向的不是B类的对象，(B\*)a将得到一个无法判断对错的值，而dynamic\_cast<B\*>(a)会得到一个空值，便于进一步处理。  
  
需要注意的是，指向虚基类的指针只能通过dynamic\_cast转换为指向派生类的指针：
```
struct A { .... };
struct B: virtual A { .... };
struct C: virtual A { .... };
struct D: B, C { .... };

void foo(A* a) {
  D* d0 = (D*)a;  // Error
  D* d1 = dynamic_cast<D*>(a);  // Right
  ....
}
```
应尽量减少向下类型转换，可参见ID\_downCast的进一步讨论。
<br/>
<br/>

#### 相关
ID_downCast  
<br/>

#### 依据
ISO/IEC 14882:2011 5.2.7  
<br/>

#### 参考
MISRA C++ 2008 5-2-2  
C++ Core Guidelines Type.2  
<br/>
<br/>

### <span id="ID_oddNewCast">▌R12.12 对new表达式不应进行类型转换</span>

ID_oddNewCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

new表达式本身是类型明确的，转换new表达式的类型不符合C\+\+严谨的类型理念，也容易造成分配、访问或回收相关的错误。  
  
示例：
```
int* p = (int*)new char[123];  // Non-compliant
```
例中char数组转为int数组，由于元素个数不兼容也会导致内存访问与回收的错误。
<br/>
<br/>

#### 相关
ID_forbidFlexibleArray  
<br/>
<br/>

### <span id="ID_redundantCast">▌R12.13 不应存在多余的类型转换</span>

ID_redundantCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

转换前后的类型完全相同是没有意义的，可能意味着某种笔误。  
  
示例：
```
float foo(int a) {
    return (int)a;    // Non-compliant
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
<br/>

### <span id="ID_unsuitableReinterpretCast">▌R12.14 可用static_cast、dynamic_cast完成的类型转换不可使用reinterpret_cast</span>

ID_unsuitableReinterpretCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: cast warning

<hr/>

reinterpret\_cast将某地址强行按另一种类型解释，不考虑类型转换需要的逻辑，当可以用其他方法转换时不可使用reinterpret\_cast。  
  
示例：
```
struct A {
  int a = 1;
};

struct B {
  int b = 2;
};

struct C: A, B {
};

int main() {
  C c;
  cout << static_cast<B*>(&c)->b << ' ';
  cout << reinterpret_cast<B*>(&c)->b << endl; // Non-compliant, what is output?
}
```
输出2 1  
如果想将派生类的地址&c转为基类指针，应使用static\_cast进行正确的偏移转换，而如果使用reinterpret\_cast，不会进行偏移转换，这种情况下得到的成员b是错误的。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 5.2.10(7)  
ISO/IEC 14882:2011 5.2.10(7)  
<br/>

#### 参考
C++ Core Guidelines Type.1  
<br/>
<br/>

### <span id="ID_forbidCStyleCast">▌R12.15 在C++代码中禁用C风格类型转换</span>

ID_forbidCStyleCast&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: cast suggestion

<hr/>

C语言的类型观念没有C\+\+语言强，使用C风格类型转换易造成逻辑错误或数据丢失，应尽量避免类型转换，如果必须进行类型转换，应使用static\_cast、dynamic\_cast等方法。  
  
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
例中A和B是两种不相关的类型，用C语言的转换方式是可以转换成功的，但并没有逻辑意义，在C\+\+语言中应使用static\_cast或dynamic\_cast等方法在编译时或运行时保障转换的有效性。
<br/>
<br/>

#### 参考
MISRA C++ 2008 5-2-4  
C++ Core Guidelines ES.49  
<br/>
<br/>

### <span id="ID_forbidReinterpretCast">▌R12.16 使用reinterpret_cast需有文档说明</span>

ID_forbidReinterpretCast&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: cast suggestion

<hr/>

语言对reinterpret\_cast的定位不是为了安全性，而是为了灵活性，可以用其他方式实现的功能不可使用reinterpret\_cast，如果必须使用需有明确文档说明。  
  
示例：
```
class MyData { .... };

void foo(const char* path) {
    unsigned char* p = read_from_file(path);
    MyData* dat = reinterpret_cast<MyData*>(p);  // Bad
    ....
}
```
例中通过reinterpret\_cast将二进制数据直接转为MyData类的对象，这不是一种安全的方式，妥善的作法是根据文件数据将MyData类的成员逐一构造出来，这样也可及时发现问题并处理。  
  
又如：
```
struct data_type {
    void* _dummy;
};

data_type* external_interface();  // If it's out of control ...

void foo() {
    auto* data = external_interface();
    auto* mydata = reinterpret_cast<MyData*>(data);  // Attention
    ....
}
```
例中external\_interface是项目外部的一个接口，它的实现方式完全不受控制，返回类型也不是有效类型，甚至需要某种“hacking”才能使用这个接口，这已经不属于正常的开发范围了，可以用reinterpret\_cast强调这是一种非正常的转换，但需注明这种情况产生的原因，以及是否有改进的余地等信息。
<br/>
<br/>

#### 相关
ID_forbidCStyleCast  
<br/>

#### 参考
CWE-843  
C++ Core Guidelines Pro.safety  
<br/>
<br/>

## <span id="buffer">13. Buffer</span>

### <span id="ID_bufferOverflow">▌R13.1 对内存的读写应在有效的边界内进行</span>

ID_bufferOverflow&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: buffer warning

<hr/>

程序读写内存之前应保证内存区域的有效性，任何在有效边界之外的读写都会导致严重后果。  
  
示例：
```
char buf[256];

void foo() {
    gets(buf);  // Non-compliant
    ....
}
```
例中gets函数无法检查输入缓冲区的大小，是公认的危险函数，如果用户输入超过了buf数组的边界，输入数据就会覆盖程序数据。  
  
应改为：
```
void foo() {
    fgets(buf, sizeof(buf), stdin);  // Compliant
    ....
}
```
fgets函数与gets不同，当输入超过缓冲区大小时会被截断，保证无效区域不会被写入。  
  
当数组下标存在越界时，本规则特化为ID\_arrayIndexOverflow。
<br/>
<br/>

#### 相关
ID_arrayIndexOverflow  
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

数组下标不在数组声明的大小范围之内，意味着内存读写错误，会导致难以控制的后果。  
  
示例：
```
int foo() {
    int a[10] = {};
    ....
    return a[10];  // Non-compliant, overflow
}
```
例中数组声明的大小是10，其下标有效范围是0至9，10不能再作为下标，这也是常见笔误。  
  
如果数组下标可受用户控制，更应该判断是否在有效范围内：
```
const char* bar() {
    static const char* fruits[] = {
        "apple", "banana", "grape"
    };
    return fruits[userInput()];  // Non-compliant
}
```
应改为：
```
const char* bar() {
    static const char* fruits[] = {
        "apple", "banana", "grape"
    };
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
例中foo函数为int型数组分配了123个字节的空间，而123不能被sizeof(int)整除，最后一个元素会越界。  
  
应改为：
```
void foo() {
  int* p = (int*)malloc(123 * sizeof(int));  // Compliant
  ....
}
```
需要注意数组的逻辑大小和字节大小的区别，不应漏掉sizeof因子。  
  
又如：
```
void bar(const char* s) {
  char* p = (char*)malloc(strlen(s));  // May be ‘strlen(s) + 1’?
  ....
}

void baz(const wchar_t* s) {
  wchar_t* p = (wchar_t*)malloc(wcslen(s));  // May be ‘(wcslen(s) + 1) * sizeof(*s)’?
  ....
}
```
字符串以空字符结尾，在分配字符串空间时不应漏掉空字符的空间。
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

### <span id="ID_nonPODFilling">▌R13.4 memset等函数不应作用于带有虚函数的对象</span>

ID_nonPODFilling&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: buffer error

<hr/>

memset、memcpy、memmove等具有填充功能的函数不应作用于带有虚函数的对象，否则会破坏其虚函数表等结构。  
  
示例：
```
class A {
    int i;
public:
    virtual ~A();
};

void foo(A& a) {
    memset(&a, 0, sizeof(a));   // Non-compliant, the vftable is corrupted
}

void bar(A& a, A& b) {
    memcpy(&a, &b, sizeof(a));  // Non-compliant, the vftable is corrupted
}
```
对象应利用其构造函数进行初始化或实现其专有的复制逻辑，memset、memcpy等函数破坏了类型边界，在面向对象的程序设计中应避免使用。
<br/>
<br/>

#### 参考
CWE-463  
C++ Core Guidelines SL.con.4  
C++ Core Guidelines C.90  
<br/>
<br/>

### <span id="ID_badLength">▌R13.5 memset等函数长度相关的参数不应有误</span>

ID_badLength&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: buffer error

<hr/>

对于memset、memcpy、memmove、memcmp及同类函数，表示长度的参数不应存在常见笔误。  
  
示例：
```
char buf[1024];
memset(buf, 1024, 0);  // Non-compliant
```
应改为：
```
memset(buf, 0, 1024);  // Compliant
```
长度和填充值参数被写反是常见的笔误。  
  
又如：
```
int arr[1024];
memset(buf, 0, 1024);  // Rather suspicious
memset(buf, 1, 123);   // Non-compliant
```
memset等函数的长度单位为字节，不应与对象序列的逻辑长度有冲突，应改为：
```
memset(buf, 0, 1024 * sizeof(int));  // Compliant
memset(buf, 1, 123 * sizeof(int));   // Compliant
```
要注意不应遗漏sizeof因子。  
  
又如，设p为对象的指针：
```
memset(p, 0, sizeof(p));   // Non-compliant
```
应改为：
```
memset(p, 0, sizeof(*p));  // Compliant
```
sizeof作用于指针并不能获取到对象的大小，可参见ID\_sizeof\_pointer的进一步讨论。  
  
又如，设a、b是对象：
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

### <span id="ID_valueOverflow">▌R13.6 memset等函数填充值相关的参数不应有误</span>

ID_valueOverflow&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: buffer error

<hr/>

memset、memset\_s等函数的填充值参数会被转为unsigned char型，所以其值不应超出一个字节的范围。  
  
示例：
```
char buf[32];
memset(buf, 1024, 32);  // Non-compliant
```
例中填充值为1024，超出了一个字节的范围，在实际代码中也可能是长度参数与填充值参数被写反了。
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

### <span id="ID_fixedAddrToPointer">▌R14.1 不应将非零常量值赋值给指针</span>

ID_fixedAddrToPointer&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: pointer warning

<hr/>

固定地址是不可移植的，且存在安全隐患。  
  
示例：
```
const void* invalidPtrVal = (void*)0xffffffff;  // Non-compliant
```
示例代码的本意是声明一个表示无效地址的值，而在64位系统中这个地址可能是有效的。  
  
又如：
```
typedef int (*fp_t)(int);
fp_t fp = (fp_t)0x1234abcd;  // Non-compliant
int res = (*fp)(123);
```
这段代码假设在特定地址始终可以找到特定的函数，将该地址赋给一个指针并调用，这种假设可能本身就是错误的，导致了崩溃，或者攻击者可以更改预期地址上的内存，从而导致任意代码的执行。
<br/>
<br/>

#### 配置
ID_expression/allowMinusOneAsPointerValue：为true时可以放过-1作为指针值的情况  
<br/>

#### 参考
CWE-587  
<br/>
<br/>

### <span id="ID_nullDerefInExp">▌R14.2 注意逻辑表达式内的空指针解引用</span>

ID_nullDerefInExp&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: pointer error

<hr/>

在逻辑表达式中，判断针指是否为空的子表达式可以作为指针解引用的条件，需注意其逻辑关系及运算符优先级，不可出现空指针解引用的问题。  
  
示例：
```
bool fun0(T* p) {
    return p || p->foo();  // Non-compliant
}
```
当p为空时执行右子表达式，恰好发生空指针解引用问题。  

```
bool fun1(T* p) {
    return p && p->foo() || p->bar();  // Non-compliant
}
```
由左子表达式可知p可能为空，而右子表达式却没有限制，有可能发生空指针解引用问题。  

```
bool fun2(T* p) {
    return p->foo() && p;  // Non-compliant
}
```
这是颠倒了对p的判断和解引用次序，也是一种常见错误。  
  
空指针解引用会导致标准未定义的错误，进程一般会崩溃，给用户不好的体验，而且要注意如果崩溃可由外部输入引起，会被攻击者利用从而迫使程序无法正常工作。
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 6.5.3.2(4)-undefined  
ISO/IEC 9899:2011 6.5.3.2(4)-undefined  
<br/>

#### 参考
CWE-476  
CWE-783  
C++ Core Guidelines ES.65  
<br/>
<br/>

### <span id="ID_nullDerefInScp">▌R14.3 避免空指针解引用</span>

ID_nullDerefInScp&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: pointer error

<hr/>

解引用空指针会导致程序崩溃等标准未定义的错误。  
  
示例：
```
int foo() {
    int i = 123;
    int* p = nullptr;
    if (condition) {
        p = &i;
    }
    return *p;   // Non-compliant
}
```
例中指针p为空的状态可以到达解引用处，应避免这种情况。  

```
void bar(T* p) {
    if (p) {
        p->baz();
    }
    p->qux();  // Non-compliant
}
```
例中对p\->bar的调用超出了p的检查范围，这也属于常见指针错误。  
  
解引用空指针一般会使进程崩溃，给用户不好的体验，而且要注意如果崩溃可由外部输入引起，会被攻击者利用从而迫使程序无法正常工作，具有高可靠性要求的服务类程序应该注意这一点。对于客户端程序，也要防止攻击者对崩溃产生的core dump进行恶意调试，避免泄露敏感数据，程序的健壮性与安全性是紧密相关的。
<br/>
<br/>

#### 依据
ISO/IEC 9899:1999 6.5.3.2(4)-undefined  
ISO/IEC 9899:2011 6.5.3.2(4)-undefined  
<br/>

#### 参考
CWE-476  
C++ Core Guidelines ES.65  
<br/>
<br/>

### <span id="ID_nullDerefAllocRet">▌R14.4 应判断malloc等内存分配函数的返回值是否为空</span>

ID_nullDerefAllocRet&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: pointer warning

<hr/>

malloc等函数在分配失败时返回空指针，如果不加判断直接解引用会造成标准未定义的错误。  
  
这类函数还包括aligned\_alloc、calloc、realloc等。  
  
示例：
```
T* foo(size_t size) {
    T* p = (T*)malloc(size * sizeof(T));
    for (int i = 0; i < size; i++) {
        bar(p[i]);  // Non-compliant, check ‘p’ first
    }
    return p;
}
```
在有虚拟内存支持的平台中，正常的内存分配一般不会失败，但申请内存过多或有误时（如参数为负数）也会导致分配失败，而对于没有虚拟内存支持的或可用内存有限的嵌入式以及单片机系统，对申请资源是否成功的检查是十分重要的，所以本规则应该作为代码编写的一般性要求。  
  
对内存等资源分配失败的处理应作为软件设计的必要部分，应有明确的文档支持。
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

### <span id="ID_nullDerefDynamicCast">▌R14.5 用dynamic_cast转换指针时应判断结果是否为空</span>

ID_nullDerefDynamicCast&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: pointer warning

<hr/>

dynamic\_cast转换指针失败时结果为空指针，如果不作判断则失去了使用dynamic\_cast的意义。  
  
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
使用dynamic\_cast需要一定开销，如果不对其结果作判断，还不如使用static\_cast等转换，但本规则集合不建议采用非dynamic\_cast之外的向下类型转换，可参见ID\_nonDynamicDownCast的相关讨论。
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

### <span id="ID_danglingDeref">▌R14.6 对已经被释放的指针不应再次解引用</span>

ID_danglingDeref&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: pointer error

<hr/>

已经被释放的指针指向无效的内存空间，如果再次对其解引用会造成标准未定义的错误。  
  
示例：
```
void foo() {
    int* p = new int[100];
    if (cond) {
        delete[] p;
    }
    do_somthing(p[0]);  // Non-compliant, ‘p’ is deallocated
}
```
本来指针p指向有效的内存空间，但由于某种原因相关内存被释放，p的值不变但已无效，这种情况被形象地称为“指针悬挂”， 未经初始化的指针和这种“被悬挂”的指针统称“野指针”，均指向无效地址不可被解引用。  
  
在C\+\+语言中，应避免持有可自动销毁的对象地址，如容器中对象的地址、智能指针所指对象的地址等。
```
int bar(vector<int>& v) {
    int* p = &v.front();         // Bad practice
    v.push_back(some_value);
    return *p;                   // ‘p’ may be invalid
}
```
例中指针p记录了vector容器中对象的地址，根据vector容器持有对象的策略，随着元素的增加原有对象的地址可能不再有效。  
  
又如：
```
int baz() {
    auto u = make_unique<int>();
    auto p = u.get();            // Bad practice
    u = make_unique<int>();
    return *p;                   // ‘p’ is invalid
}
```
例中指针p记录了unique\_ptr所指对象的地址，当unique\_ptr指向新的对象时，原对象的地址不再有效。
<br/>
<br/>

#### 相关
ID_illAccess  
<br/>

#### 依据
ISO/IEC 9899:1999 6.5.3.2(4)-undefined  
ISO/IEC 9899:2011 6.5.3.2(4)-undefined  
<br/>

#### 参考
CWE-822  
CWE-825  
C++ Core Guidelines ES.65  
<br/>
<br/>

### <span id="ID_oddPtrBoolAssignment">▌R14.7 不应使用false等布尔常量对指针赋值或初始化</span>

ID_oddPtrBoolAssignment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: pointer warning

<hr/>

用false等布尔常量对指针赋值或初始化是非常怪异的，会对代码阅读造成误导，而且也可能是书写错误。  
  
示例：
```
void set_false(bool* p) {
    p = false;  // Non-compliant, should be *p = false
}
```
<br/>
<br/>

#### 参考
CWE-351  
<br/>
<br/>

### <span id="ID_oddPtrBoolComparison">▌R14.8 指针不应与bool型常量比较大小</span>

ID_oddPtrBoolComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: pointer warning

<hr/>

指针与bool型常量比较大小是非常怪异的，往往是某种笔误。  
  
示例（设p为指针）：
```
p == false  // Non-compliant
p != false  // Non-compliant
```
如果判断指针是否为空，只应将指针与NULL或nullptr比较，其它常量均不合规。在C\+\+语言中应优先使用nullptr。
<br/>
<br/>

#### 参考
CWE-1025  
<br/>
<br/>

### <span id="ID_oddPtrCharAssignment">▌R14.9 不应使用'\0'或L'\0'等字符常量对指针赋值或初始化</span>

ID_oddPtrCharAssignment&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: pointer warning

<hr/>

用'\\0'或L'\\0'等字符常量对指针赋值或初始化是非常怪异的，往往意味错误。  
  
示例：
```
void set_terminate(char* p) {
    p = '\0';  // Non-compliant
}
```
应改为：
```
void set_terminate(char* p) {
    *p = '\0';  // Compliant
}
```
<br/>
<br/>

#### 参考
CWE-351  
<br/>
<br/>

### <span id="ID_oddPtrCharComparison">▌R14.10 指针不应与char型常量比较大小</span>

ID_oddPtrCharComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: pointer warning

<hr/>

指针与char型常量比较大小是非常怪异的，往往是某种笔误。  
  
示例（设p为指针）：
```
p == '\0'    // Non-compliant
p == L'\0'   // Non-compliant
```
这种情况很有可能是解引用时的笔误：
```
*p == '\0'   // Compliant
*p == L'\0'  // Compliant
```
否则只应将指针与NULL或nullptr比较。
<br/>
<br/>

#### 参考
CWE-1025  
<br/>
<br/>

### <span id="ID_oddPtrZeroComparison">▌R14.11 不应判断指针大于、大于等于、小于、小于等于0</span>

ID_oddPtrZeroComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: pointer warning

<hr/>

指针的值是地址的编号，并没有大小的语义，指针可以与指针比较从而确定某种前后关系，但指针与整数比较大小则是没有意义的，尤其是与0的比较，往往意味错误。  
  
示例（设p为指针）：
```
p < 0    // Non-compliant, always false
p >= 0   // Non-compliant, always true
p > 0    // Non-compliant, use p != nullptr instead
p <= 0   // Non-compliant, use p == nullptr instead
```
另外，指针与NULL或nullptr只应使用==或!=进行比较，其他比较运算符均不合规：
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

### <span id="ID_invalidNullCheck">▌R14.12 避免无效的空指针检查</span>

ID_invalidNullCheck&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: pointer warning

<hr/>

当指针的值一定不为空时，再对其进行检查是没有意义的，往往意味着逻辑错误。  
  
示例：
```
void bar() {
    if (int* p = new int[100]) {  // Non-compliant
        // ... 
    } else {
        // ... Handle errors, but it’s in vain ...
    }
}
```
标准规定默认new运算符的返回值不会为空，如果分配失败则抛出异常，所以这种检查和相关错误处理是无效的。  
  
应改为：
```
void bar() {
    if (int* p = new(std::nothrow) int[100]) {  // Compliant
        // ...
    } else {
        // ... Handle errors, OK ...
    }
}
```
又如：
```
void foo(T* p) {
    if (p) {  // Meaningless
        delete p;
    }
}
```
对于可接受空指针的接口，不必总在调用前判断指针是否为空，否则会使代码变得繁琐。delete关键字或free函数可以作用于空指针，不会产生不良后果，所以调用之前的检查是没有意义的。
<br/>
<br/>

#### 相关
ID_repeatedNullCheck  
<br/>

#### 依据
ISO/IEC 9899:2011 18.6  
<br/>
<br/>

### <span id="ID_repeatedNullCheck">▌R14.13 不应重复检查指针是否为空</span>

ID_repeatedNullCheck&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: pointer warning

<hr/>

重复的空指针检查是不必要的，使代码显得繁琐，且干扰编译器优化。  
  
示例：
```
void foo(int* p) {
    if (!p) {
        return;
    }
    if (p) {      // Non-compliant, p is not nullptr
        ....
    } else {
        ....      // Dead code
    }
}
```
<br/>
<br/>

#### 相关
ID_invalidNullCheck  
<br/>
<br/>

### <span id="ID_sizeof_pointer">▌R14.14 sizeof作用于指针是可疑的</span>

ID_sizeof_pointer&emsp;&emsp;&emsp;&emsp;&nbsp;:question: pointer suspicious

<hr/>

sizeof作用于指针获取到的是指针变量的大小，而不是指针指向内容的大小，sizeof作用于指针很容易造成错误。  
  
示例：
```
void foo(int* p) {
    memset(p, 0, sizeof(p));   // Logic error
}
```
应改为：
```
void foo(int* p, int n) {
    memset(p, 0, n * sizeof(*p));  // OK
}
```
其中参数n可以是数组元素的个数。
<br/>
<br/>

#### 相关
ID_sizeof_pointerDivision  
<br/>

#### 参考
CWE-467  
<br/>
<br/>

### <span id="ID_this_deleteInDestructor">▌R14.15 析构函数中不可使用delete this</span>

ID_this_deleteInDestructor&emsp;&emsp;&emsp;&emsp;&nbsp;:boom: pointer error

<hr/>

析构函数中不可使用delete this，否则造成无限递归。  
  
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

### <span id="ID_this_forbidDeleteThis">▌R14.16 禁用delete this</span>

ID_this_forbidDeleteThis&emsp;&emsp;&emsp;&emsp;&nbsp;:no_entry: pointer suggestion

<hr/>

因为正确使用delete this限制条件太多，稍不留意就会为bug埋下伏笔，所以禁用这种方式是明智的选择。  
  
要想正确使用delete this，必须保证：  
 ● 对象是用new创建的，但不能是new\[\]或replacement new  
 ● 使用delete this之后不能再访问相关非静态成员及成员函数  
 ● 不能在析构函数中使用delete this  
  
示例：
```
class A {
public:
    void foo() {
        delete this;  // Non-compliant
    }
private:
    int data;
};

auto* p = new A;
p->foo();  // Looks innocent

p = new A[10];
p->foo();  // Memory is still leaking
```
如果一定要使用delete this，类的析构函数应设为私有，这样可以阻止类的对象在栈上定义而引发更大的混乱，并且要确保执行delete this后this指针再也不会被解引用，而且不能用new\[\]创建，否则仍然存在内存泄漏问题。  
所以对框架以及语言工具之外的业务类或算法类代码建议禁用delete this。
<br/>
<br/>
<br/>

### <span id="ID_this_zeroComparison">▌R14.17 不应判断this指针是否为空</span>

ID_this_zeroComparison&emsp;&emsp;&emsp;&emsp;&nbsp;:fire: pointer warning

<hr/>

正常情况下this指针不会为空，而且判断this指针是否为空会影响编译器对this指针的优化，造成难以预料的后果。  
  
在某些环境中用空指针调用非静态成员函数时，this指针可能为空，但这并不符合标准。值得强调的是，任何情况下都不应逃避解引用空指针造成的问题。  
  
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
cout << p->getX() << endl;
```
假设foo函数不应返回空指针，而某个错误导致其返回了空指针，程序本应崩溃，而getX函数却逃避了崩溃，这非但不能真正地解决问题，反而使问题难以定位，使程序难以调试，大大降低了可维护性。
<br/>
<br/>

#### 依据
ISO/IEC 14882:2003 4.1(1)  
ISO/IEC 14882:2011 4.1(1)  
<br/>

#### 参考
CWE-1025  
<br/>
<br/>

### <span id="ID_missingResetNull">▌R14.18 指针在释放后应置空</span>

ID_missingResetNull&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: pointer suggestion

<hr/>

指针指向的动态内存空间被回收后指针不再有效，这时应将指针设为空指针，可避免重复释放造成的问题，如果后续对指针仍有错误的读写，也可使问题立即显现出来，不至于造成难以排查的问题。  
  
尤其是在析构函数中，建议所有需要释放的成员指针在释放后都置为空指针。  
  
示例：
```
class T {
public:
   ~T() {
        delete[] p;
        p = nullptr;  // Good
    }
private:
    int* p = new int[123];
};
```
例中指针p被释放之后置为nullptr，如果T的析构函数被反复调用，也不会造成问题。本规则是关于“指针悬挂”问题的有效措施，可参见ID\_danglingDeref的进一步讨论。
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

### <span id="ID_zeroAsPtrValue">▌R14.19 不应使用常数0对指针赋值</span>

ID_zeroAsPtrValue&emsp;&emsp;&emsp;&emsp;&nbsp;:bulb: pointer suggestion

<hr/>

不应使用常数0对指针赋值，在C\+\+代码中应使用nullptr，在C代码中应使用NULL，否则易出现类型转换相关的错误，且不利于阅读。  
  
示例：
```
int* p = 0;  // Non-compliant
int foo(int* p);
int i = foo(0);  // Non-compliant
int j = foo(nullptr);  // Compliant
```
<br/>
<br/>

#### 参考
MISRA C++ 2008 4-10-2  
C++ Core Guidelines ES.47  
<br/>
<br/>

## <span id="style">15. Style</span>

### <span id="ID_spaceStyle">▌R15.1 空格应遵循统一风格</span>

ID_spaceStyle&emsp;&emsp;&emsp;&emsp;&nbsp;:womans_hat: style suggestion

<hr/>

与缩进、运算符、关键字有关的空格应遵循统一风格，过于随意的空格会对代码阅读产生较大干扰，甚至形成笔误。  
  
本规则暂不强调具体风格，但强调一致性，即同类运算符、关键字的空格方式应该是一致的。  
  
本规则是ID\_stickyAssignmentOperator的泛化，该规则描述了一种由空格造成的错误。  
  
示例：
```
a= 0;                   // Bad
b = a +1;               // Bad
c = ~ a;                // Bad
d = b ++;               // Bad

a == 0 ? foo() :bar();  // Bad
baz (1 , 2 ,3);         // Bad

if (cond)
if(cond)                // Missing a unified style
```
<br/>
<br/>

#### 相关
ID_stickyAssignmentOperator  
<br/>
<br/>

### <span id="ID_braceStyle">▌R15.2 大括号应遵循统一风格</span>

ID_braceStyle&emsp;&emsp;&emsp;&emsp;&nbsp;:womans_hat: style suggestion

<hr/>

大括号应遵循统一风格。  
  
对于命名空间、类、函数体、复合语句等不同属的大括号，其换行方式可以不同，但同属大括号的换行方式应该是一致的。  
  
示例：
```
void foo() {
    if (cond)
    {
        ....
    }
    else {
        ....
    }
}

void bar()
{
    if (cond) {
        ....
    } else
    {
        ....
    }
}
```
例中大括号换行的方式不一致，影响阅读。
<br/>
<br/>
<br/>

### <span id="ID_deprecatedNULL">▌R15.3 在C++代码中不应使用NULL，应使用nullptr</span>

ID_deprecatedNULL&emsp;&emsp;&emsp;&emsp;&nbsp;:womans_hat: style suggestion

<hr/>

在C\+\+语言中，标识符NULL虽然可以用来表示空指针，但该标识符是由实现定义的，而且往往不能有效区分整型常量0和空指针，根据C\+\+11标准，应使用nullptr表示空指针。  
  
示例：
```
void foo(int*) {
    cout << "foo(int*)" << endl;
}

void foo(int) {
    cout << "foo(int)" << endl;
}

int main() {
    foo(NULL);  // Non-compliant, what is output?
}
```
显然，例中main函数中的foo函数预期应调用参数为指针的版本，然而不同的编译器对这段代码有不同的行为，有的无法通过编译，有的编译执行后会输出“foo(int)”，用nullptr代替NULL可解决这种问题。
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

### <span id="ID_mixNullptrAndNULL">▌R15.4 NULL和nullptr不应混用</span>

ID_mixNullptrAndNULL&emsp;&emsp;&emsp;&emsp;&nbsp;:womans_hat: style warning

<hr/>

NULL和nullptr不应混用，应统一使用nullptr。  
  
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

### <span id="ID_redundantSemicolon">▌R15.5 不应存在多余的分号</span>

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

### <span id="ID_assignmentAsSubExpression">▌R15.6 赋值表达式不应作为子表达式</span>

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


## 结语
保障软件安全、提升产品质量是宏大的主题，需要不断地学习、探索与实践，也难以在一篇文章中涵盖所有要点，这409条规则就暂且讨论至此了。欢迎指正错误并提供扩展意见，由于本文档是自动生成的，请不要直接编辑本文档，可在Issue区发表高见，管理员修正数据库后会在致谢列表中存档。

祝编程愉快！
