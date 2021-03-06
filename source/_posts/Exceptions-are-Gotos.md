---
title: Exceptions are Gotos
date: 2018-11-04 15:10:06
tags:
categories: 技术
---
### **Exceptions, they are one of those most complex and ridiculous thing ever devised** ——Jonathan Blow

<!-- more -->

- Programming languages differ substantially in their notion of what is an exception. Your notion of Exception in Python, Java, are totally different from other language's “exception”.
- Exceptions, in most imperative languages (e.g. Java, Python, JavaScript), is a form of goto.
- In some languages, “exception” is just a syntactic form of goto, and THAT IS ALL. You write if statement to check condition, and if so and so, you call “throw”, which jumps to a “catch” in YOUR CODE. That's ALL. (for example, emacs lisp. [see [Emacs Lisp: Exit Loop/Function, catch/throw](http://ergoemacs.org/emacs/elisp_break_loop.html)])
- In some languages, such as Java, Python, exception is more than just goto, but, goto with a tree of gotos, which runs out of your control. You can simply “throw”, or simply “try”, and the language will do things in a magic realm.
- Google's new language golang, does not have exceptions, because the designers consider Exceptions harmful.
- If you don't like the typical notion of Exceptions in {C++, Java, Python}, be cheered that many well-known expert programers, do not like them neither, and never use such feature in their own code.

### Semipredicate问题

Semipredicate发生在一个可能会出错的子程序试图返回一个有效值时，此时，调用函数无法确认接收到的值是有效值还是发生错误的信号。

解决这个问题的一种办法就是Exceptions，但是异常机制的引入在程序中造成了"invisible exit points that runs in some magic realm", 降低了程序的可读性。

为了避免Exceptions造成的问题，可以使用以下几种解决办法：

1. 返回列表，列表的第一个元素包含子程序返回值，第二个元素包含错误码，第三个元素包含供程序员检查的错误信息。
2. 一些语言支持多返回值，比如Common Lisp.
3. 使用“外部参数”，一个例子就是C或者C++中的指针，子程序运行时可以修改一个指针所指向的值，这样在子程序结束时，调用函数可以查看这个值来确认子程序是否发生错误。
4. 返回一个对象。
5. 使用全局变量，记录子程序退出时的状态。
6. 返回特殊值，比如nil, null, -1等。
7. 返回一个特殊的类型。

### 异常的分类

- those languages in which exceptions “are designed to be used as flow control structures”; according to this paper, Ada, C++, Java, Modula-3, ML, OCaml, Python, and Ruby fall in this category

- those languages in which exceptions “are designed to represent and handle abnormal, unpredictable, erroneous situations”; according to the paper these include: C#, Common Lisp, Eiffel, and Modula-2

  by Joseph R.kiniry

所以结论就是，如果是为了控制程序的逻辑(flow control)，那就尽量不要使用异常机制，而应该将异常用在错误处理上。

参考资料：

http://xahlee.info/comp/why_i_hate_exceptions.html