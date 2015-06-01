## Python Enhancement Proposal 3107 部分内容的中文翻译
===
> 翻译自 [PEP 3107 -- Function Annotations](https://www.python.org/dev/peps/pep-3107/#functerm)

##译者的话
最近修改的 [PEP 0484](https://www.python.org/dev/peps/pep-0484/) 表明 py3 中将增加对 Type Hints 的支持，

相关的理论介绍在 [PEP 0483](https://www.python.org/dev/peps/pep-0483/)。

PEP 0484还在 Accepted PEPs (accepted; may not be implemented yet) 列表中，但是 PEP 3107 已经在 py3 中实现。

翻译格式参考[PEP333-zh-CN](https://github.com/mainframer/PEP333-zh-CN)

##内容
* [摘要](#Abstract)
* [函数注解的基础](#Fundamentals of Function Annotations)
* [语法](#Syntax)
  * [参数](#Parameters)
  * [返回值](#Return Values)
  * [匿名函数(lambda)](#Lambda)
* [访问函数注解](#Accessing Function Annotations)

<a name="Abstract">
## [摘要](#Abstract)
这个 PEP 介绍了一种给 Python 的函数添加任意的元数据注解的语法。

<a name="Fundamentals of Function Annotations">
## [函数注解的基础](#Fundamentals of Function Annotations)
1. 对于参数和返回值的函数注解，都是可选而非必须。
2. 函数注解只是一种在编译时（complie-time）将任意的 Python 表达式 和 Python 函数联系起来的方式。

Python 自身不会给注解本身加上具体的意思或者某种信号。也就是说，Python 仅仅让这些表达式可以按照下面描述的 访问函数注解（ Accessing Function Annotations ） 的方式去访问。

只有当第三方库去解释函数注解时，这些表达式才有意义。三方库可以对这些注解做任何想做的事情。举例来说，一个库也许会使用字符类型（string-based）的函数注解来提供更有效的帮助信息。如：

```Python
def compile(source: something compilable,
            filename: where the compilable thing comes from,
            mode: is this a single statement or a suite?):
    ...
```

一个其他的库或许会被用来为 Python 的函数（function）和方法（method）提供类型检查。这个库可以使用函数注解来指明参数类型和函数返回值。可能的例子如下：

```Python
def haul(item: Haulable, *vargs: PackAnimal) -> Distance:
    ...
```

然而，无论是第一个例子中的字符类型还是第二个例子中的类型信息（type information），它们独自存在的时候没有任何意义，只有当第三方的库使用他们时，他们才有具体的意义

3. 根据观点二，即使是对内置类型（build-in type），这个 PEP 也并不尝试引进任何种类的标准语义。这项工作将留给第三方库来完成。

<a name="Syntax">
## [语法](#Syntax)

<a name="Parameters">
###[参数](#Parameters)
参数的注解按照参数名后跟随可选的表达式的形式：

```Python
def foo(a: expression, b: expression = 5):
    ...
```
在这种伪语法（pseudo）中参数现在以 identifier [: expression] [= expression] 的形式出现。所以参数注解永远先于参数的默认值，注解和默认值都是可选项。就像用等好来标志默认值，参数注解使用冒号来表明。和参数默认值一样，所有的注解表达式都会在函数定义执行时被求值（evaluated）。

同理，可变参数（excess parameters）的参数注解也用类似的方法指明。

```Python
def foo(*args: expression, **kwargs: expression):
    ...
```

嵌套参数的注解同样是参数名字后跟注解的形式，并不是跟随在最后的括号之后。没有必要为所有的嵌套参数加上注解。

```Python
def foo((x1, y1: expression),
        (x2: expression, y2: expression)=(None, None)):
    ...
```

<a name="Return Values">
###[返回值](#Return Values)
目前为止的例子还没有涉及到如何给函数返回值加注解，给返回值注解像下面这样：

```Python
def sum() -> expression:
    ...
```

参数序列后跟随一个 -> 符号，后面跟随 Python 表达式。和对参数的注解一样，这个注解将在函数定义执行时被求值。

函数的语法定义现在变成了：

```Python
decorator: \'@\' dotted_name [ \'(\' [arglist] \')\' ] NEWLINE
decorators: decorator+
funcdef: [decorators] \'def\' NAME parameters [\'->\' test] \':\' suite
parameters: \'(\' [typedargslist] \')\'
typedargslist: ((tfpdef [\'=\' test] \',\')*
                (\'*\' [tname] (\',\' tname [\'=\' test])* [\',\' \'**\' tname]
                 | \'**\' tname)
                | tfpdef [\'=\' test] (\',\' tfpdef [\'=\' test])* [\',\'])
tname: NAME [\':\' test]
tfpdef: tname | \'(\' tfplist \')\'
tfplist: tfpdef (\',\' tfpdef)* [\',\']
```

<a name="Lambda">
###[匿名函数(lambda)](#Lambda)
匿名函数的语法现在不支持函数注解。匿名函数的语法可以通过在参数序列外加括号的形式系改变为支持函数注解。但是由于一下原因决定不这样做。

1. 它将是一个不兼容的更改。
2. Lambda\'s are neutered anyway.(这句不知道怎么翻译好 个人认为，可以理解为Lambda不是一个完整的函数)
3. lambda表达式可以转换成函数。

<a name="Accessing Function Annotations">
## [访问函数注解](#Accessing Function Annotations)
一旦进过编译，一个函数的注解可以通过函数的 `func_annotations` 属性访问，这个属性是一个可变的字典，键和值分别为参数名和一个类型代表被求值后的注解表达式。

`func_annotations` 键值对中有一个特别的键，`"retrun"`。这个键只有提供了函数返回值的注解时才有。

例如，有如下的注解：

```Python
def foo(a: \'x\', b: 5 + 6, c: list) -> max(2, 9):
    ...
```

返回结果func_annotations的键值对为：

```Python
{\'a\': \'x\',
 \'b\': 11,
 \'c\': list,
 \'return\': 9}
```

选择`"return"`键因为它不会和参数名冲突，尝试使用`"return"`作为参数名会导致语法错误。

如果该函数没有任何函数注解，或者该函数为一个`lambda`表达式，`func_annotations`将为空。

##译者注
翻译出于兴趣和学习目的，水平十分有限，翻译不好或有误之处请指正 : )
