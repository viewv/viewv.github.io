---
title: Program Analysis 1 Introduction
date: 2024-10-14 20:21:02 +0200 # date +"%Y-%m-%d %H:%M:%S %z"
categories: [Program Analysis, Programming Language]
tags: [program-analysis]     # TAG names should always be lowercase
---

Program analysis 课程的笔记，第一部分，随上随写，可能里面有些概念我现在还解释不清楚，因为老师没也没讨论，也许后面会更新更清楚的版本。

## Program Analysis 1 Introduction

> **What is Program Analysis?**
>
> A possible definition:
>
> A way to establish a particular property of a program automatically.

这里给出了一个Program Analysis的可能的定义，也是一个比较模糊的定义，但是这里用的单词是establish其实说明了程序分析的基本限制。

### Fundamental Limits

> **Theorem of Rice (1953)**
>
> All non-trivial, semantic properties of programs are undecidable.

对于任何非平凡的语义性质，无法通过算法来判定程序是否具有该性质。

这个定理非常有名，但是十分抽象，我们需要拆解其概念来解释这到底是什么。

#### 非平凡

> A **non-trivial** property is one which is neither true for every program, nor false for every program.

如果某个性质P对所有图灵机M都成立或对所有图灵机都不成立，那么这个性质就是平凡的。针对这个性质的判定一定是可以判定的，所以关注与非平凡问题。

#### Semantic properties

定理关注的性质的semantic的，而不是syntactic的，是语义的，不是句法的，比如我们想知道一个程序是否会在所有输入下停机？这就是语义的。另一个问题如一个程序是否包含有if-then-else语句？这是一个句法问题。

#### 不可判定性

没有算法能够在有限时间内判定一个给定的图灵机（程序）是否具有某个非平凡的语义性质。

所以我们可以发现，通过Rice定理，无论我们多么努力设计复杂的算法、优化程序分析的工具，最终都会面临不可判定性的问题。这限制了我们对程序自动化分析的能力。但是在特定情况下或针对某类特定的程序，找到一个有效的算法来判定某些性质。比如我们限制使用有限自动机程序，这就“简化”了程序，比如这时候判定有限自动机是否接受空语言？那么可以通过检查从初始状态是否有路径到达某个接受状态来判断有限自动机是否接受空语言。所以我们从Rice定理知道，我们的程序分析工具不可能实现一个通用的，完美的工具，但是仍然有意义。

### Property holds or not holds

Property holds 或者 not holds 是个问题，我们从静态分析和动态分析来讨论这个问题。

#### 静态分析

> property definitely holds | property may not hold

一般情况下，静态分析工具在分析程序时，通常通过分析程序的源代码、控制流、数据流等静态信息，而不真正运行程序。因为它不执行程序，也无法穷尽所有可能的程序执行路径，具体来说，静态分析在处理程序性质时，通常会面临以下两种主要情况：

- **Property Definitely Holds: **当静态分析工具确定某个性质一定成立时，它通常采用的是一种保守的分析策略，确保在所有可能的执行路径上该性质都成立。意味着一般情况下分析工具找到了足够的证据，能够证明在每一个可能的程序状态下，某个性质始终成立。这种结论通常是正确的，即使在所有可能的输入或路径下，该性质都能保持。比如我们判定变量是否被访问，我们的静态分析工具可以分析所有可能的程序分支，之后发现都会访问给定变量，那么我们可以给出这个程序必然holds这个property。
- **Property may not hold: **当静态分析工具不能确定某个性质在所有执行路径上都成立时，就会导致**Property may not hold**。比如一个程序分支众多，分支爆炸，这种情况下，我们的静态分析工具无法遍历分析所有的情况，这种情况下，只能给出一个may的结果。比如空指针访问问题，静态分析工具可能找到一条路径有这个问题，但是由于没有遍历所有的情况，有可能在实际程序运行中，永远不会触发这个问题。

另外一般情况下静态分析工具倾向于保守分析，宁可多报一些可能的错误（即“may not hold”），也不愿漏掉真实的错误。这意味着在有不确定性的情况下，工具会发出警告（false alarms），以避免遗漏潜在问题。

#### 动态分析

> property may hold | property definitely does not hold

动态分析是在实际运行程序的过程中，通过观察程序的行为和执行结果来推断程序的性质。由于程序的执行路径和结果依赖于运行时的输入、状态和环境，动态分析中的性质验证常常有如下两种情况：

- **Property May Hold: **当动态分析发现某个性质在当前的执行路径成立的时候，也只能给出“may hold”的结论，因为这只针对当前输入导致的执行行为下的结果成立，这并不意味着该性质在所有可能的输入和执行路径上都成立。比如数组越界检查，动态分析可能没有观测到任何越界情况，那么能给出可能程序没有数组越界问题，因为动态分析没有办法覆盖所有输入。
- **Property definitely does not hold: **动态分析是通过执行程序得到的行为数据，因此如果在某次执行中，某个性质被违反了（例如出现了错误或异常），我们可以确信这个错误确实发生了。这种情况下，动态分析能够确定该性质不成立。比如针对空指针访问，在动态分析中出现了空指针访问，那么程序必然does not hold这个属性。

动态分析由于观测到的属性违反问题一定是实际发生的的问题，所以有**property definitely does not hold**这个属性，又因为没有办法覆盖所有输入，会导致**property may hold**。

### Data-Flow Analysis: Principles
... 未完待续
