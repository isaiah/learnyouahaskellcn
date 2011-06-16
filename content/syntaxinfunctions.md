函数语法
========

模式匹配
--------

本章将从模式匹配开始介绍Haskell的一些语法结构。模式匹配包括指定数据应该符合的模式和从匹配的数据中解析出对应模式的相应数据。

定义函数时，可以为不同的模式定义不同的函数体。这使代码更加整洁和易读。可以匹配任何模式－－数字，字符，列表，元组等等。我们先定义一个非常简单的函数来检查一个数字是是否等于7。

    lucky :: (Integral a) => a -> String  
    lucky 7 = "LUCKY NUMBER SEVEN!"  
    lucky x = "Sorry, you're out of luck, pal!"

当调用`lucky`时，将从上往下检查每一个模式，将有获得一个匹配的模式时，相应的函数体将被调用。这里只有数字7才能匹配到第一个模式，如果它不匹配，那将往下找到第二个模式，这个模式匹配任何数字并将它绑定到`x`。这个函数也可以用`if`语句实现。设想这样一个函数：对从1到5的参数打印相应的英文单词，对其他数字打印`"Not between 1 and 5"`，如果没有模式匹配，将需要一长串的`if..else..`。

    sayMe :: (Integral a) => a -> String  
    sayMe 1 = "One!"  
    sayMe 2 = "Two!"  
    sayMe 3 = "Three!"  
    sayMe 4 = "Four!"  
    sayMe 5 = "Five!"  
    sayMe x = "Not between 1 and 5"

注意如果把最后一个模式放到第一个，它将总是打印`"Not betwwen 1 and 5"`，因为这个模式将捕获所有参数以至于后面的模式从来不可能被检查到[^1]。

还记得前面实现的阶乘函数吗？我们将数字`n`的阶乘定义为`product [1..n]`。也可以用递归定义，就如阶乘在数学中的定义一样。首先将0的阶乘定为1，然后规定任何正整数的阶乘是这个整数与它上一个整数的阶乘的乘积。翻译成Haskell如下：

    factorial :: (Integral a) => a -> a  
    factorial 0 = 1  
    factorial n = n * factorial (n - 1)

这是我们第一次递归定义函数。递归在Haskell中非常重要，我们将在随后更深入了解。为了得到3的阶乘，Haskell将尝试计算`3 * factorial 2`，2的阶乘是`2 * factorial 1`，现在我们有了`3 * (2 * factorial 1)`。`factorial 1`等于`1 * factorial 0`，所以结果是`3 * (2 * (1 * factorial 0))`。技巧就在这里，我们在第一个模式将0的阶乘定义为1，所以最后的结果就是`3 * (2 * (1 * 1))`。如果我们把第二个模式放到前面


[^1]:这里类似于switch..case语句，但是其实模式匹配比switch语句要强大得多－－译者。
