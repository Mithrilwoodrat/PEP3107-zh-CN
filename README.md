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
* 对于参数和返回值的函数注解，都是可选而非必须。
* 函数注解只是一种在编译时（complie-time）将任意的 Python 表达式 和 Python 函数联系起来的方式。

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

* 根据观点二，即使是对内置类型（build-in type），这个 PEP 也并不尝试引进任何种类的标准语义。这项工作将留给第三方库来完成。
