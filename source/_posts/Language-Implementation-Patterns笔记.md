---
title: Language Implementation Patterns笔记
date: 2018-11-04 22:19:00
tags:
categories: 笔记
---

Language Implementation Patterns 笔记

<!-- more -->

### Chapter 2 Basic Parsing Patterns

#### 2.1 Identify Phrase Structure

parse tree

![image-20181102102847128](/Users/illiant/Library/Application%20Support/typora-user-images/image-20181102102847128.png)

#### 2.2 Building Recursive-Descent Parsers

1. The basic method: make a function for each named substructure of the parse tree. Each function execute code to match its children.

   ![image-20181102103144258](/Users/illiant/Library/Application%20Support/typora-user-images/image-20181102103144258.png)

2. LL(1) Building Recursive-Descent Parser

   First L means "read the input from left to right". Second L means "descend into parse tree children from left to right". 1 means how many chars we look ahead.

#### 2.3 Parser Construction Using a Grammar DSL

The parsing functions above are similar and consistent, so we need to find a way to build this functions automaticlly.

Grammars are concise and act like functional specifications for languages.

Substructures in the parse tree and functions in the parser correspond to rules in a grammar.

![image-20181102105302281](/Users/illiant/Library/Application%20Support/typora-user-images/image-20181102105302281.png)

use syntax diagram to visualize the control flow

![image-20181102105359853](/Users/illiant/Library/Application%20Support/typora-user-images/image-20181102105359853.png)

#### 2.4 Tokenizer Sentences

词法: Recognizers that feed off character streams are called tokenizers or leaxers.

Example: lists of names such as [a,b,c] and nested lists such as [a, [b,c],c].

![image-20181102184013084](/Users/illiant/Library/Application%20Support/typora-user-images/image-20181102184013084.png)

词法分析和语法分析的设计模式是相似的。区别在于输入的符号，词法分析器接受的输入是characters，而语法分析器接受的输入是tokens。

**Classic Parsing Patterns**

##### 1 Mapping Grammars to Recursive-Descent Recognizers

![image-20181102193710650](/Users/illiant/Library/Application%20Support/typora-user-images/image-20181102193710650.png)

Converting Rules

对语法中的每一个规则 r，建立一个同名方法

```java 
public void r(){
    ...
}
```

Converting Tokens

Token references for token type T become calls to match(T).

match( ) is a support method in Parser that consumes a token if T is the current lookahead token. If there is a mismatch, match( ) throws an exception

对于每一个token，需要定义种别码

```java
public static final int T = <<sequential-integer>>;
//对于错误和无效的token
public static final int INVALID_TOKEN_TYPE = 0;
public static final int EOF = -1; 
```

Converting Subrules

对于以下的subrules

(<<alt1>>|<<alt2>>|..|<<altN>)

![image-20181102194939685](/Users/illiant/Library/Application%20Support/typora-user-images/image-20181102194939685.png)

(T)? looks like

![image-20181102204215389](/Users/illiant/Library/Application%20Support/typora-user-images/image-20181102204215389.png)

```java
if(<<lookahead-is-T>>) {match(T);}
```

(...)+ become do-while loops

![image-20181102204344476](/Users/illiant/Library/Application%20Support/typora-user-images/image-20181102204344476.png)

```java
do {
    <<code-matching-alternatives>>
} while (<<lookahead-predicts-an-alt-of-subrule>>)
```

(...)* looks like

![image-20181102204515197](/Users/illiant/Library/Application%20Support/typora-user-images/image-20181102204515197.png)

```java
while (<<lookahead-predicts-an-alt-of-subrule>>) {
    <<code-matching-alternatives>>
}
```

##### 2. LL(1) Recursive-Descent Lexer

##### 3. LL(1) Recursive-Descent Parser

​	Compute lookahead sets: First and Follow

##### 4. LL(K) Recursive-Descent Parser

​	The strength of a recursive-descent parser depends entirely on the strength of its lookahead decision. By allowing a lager (but still fixed) lookahead buffer, we get a parser strong enough for most computer language.

### Chapter3 Enhanced Parsing Patterns

#### 3.1 Parsing with Arbitrary Lookahead

Sometimes LL(k) method is too weak to distinguish function definitions from declarations using a natural grammar. For example:

![image-20181103151142243](/Users/illiant/Library/Application%20Support/typora-user-images/image-20181103151142243.png)

在这种情况下，需要用到Backtracking Parser.

Backtracking Parser 试探性地进行语法分析，如果不能匹配，就回溯输入，再进行下一次尝试。

试探性尝试存在顺序，这样规定好的顺序可以解决语法上的不确定性。

#### 3.3 Directing the Parse with Semantic Information

Some programming languages have context-sensitive phrases. To handle context-sensitive phrases with context-free parser, we have to predicate alternatives. In effect, **a predicate is just a run-time boolean test that says when it's OK to match an alternative**