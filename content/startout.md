起步
=======

### 预备，跑！

好了，让我们正式开始吧！如果你跳过了前面的简介，最好看一下最后一个章节，因为它解释了跟随本教程的一些准备工作以及如果装载函数。我们要做的第一件事是运行ghc的交互环境并调用一些函数以获得关于haskell的一些基本认知。打开终端并输入ghci，你将看到这样一个欢迎界面：

        GHCi, version 7.0.2: http://www.haskell.org/ghc/  :? for help
        Loading package ghc-prim ... linking ... done.
        Loading package integer-gmp ... linking ... done.
        Loading package base ... linking ... done.
        Loading package ffi-1.0 ... linking ... done.
        Prelude> 

恭喜，现在你已经进入到GHCI！这里的提示是 Prelude>，当你向会话中装载其他东西时，这个提示会变长。我们将使用ghci>。如果你想要相同的提示，输入:set prompt "ghci> "。

首先来看一些简单的算术：

    ghci> 2 + 15  
    17  
    ghci> 49 * 100  
    4900  
    ghci> 1892 - 1472  
    420  
    ghci> 5 / 2  
    2.5  
    ghci>

以上基本是自解释的。我们可以在同一行使用几个操作符，通常的优先级次序会得到遵守。如果想使用负数，最好用括号括起来。5 * -3将出错，而5 * (-3)是正确的。

布尔代数是非常直观的。&&表示布尔和，||表示布尔或，not反转True或者False。

    ghci> True && False  
    False  
    ghci> True && True  
    True  
    ghci> False || True  
    True   
    ghci> not False  
    True  
    ghci> not (True && True)  
    False

测试相等可以这样：

    ghci> 5 == 5  
    True  
    ghci> 1 == 0  
    False  
    ghci> 5 /= 5  
    False  
    ghci> 5 /= 4  
    True  
    ghci> "hello" == "hello"  
    True

5 + "llama"或者5 == True会怎么样呢？如果输入第一个，将得到一条吓人的错误信息！

    No instance for (Num [Char])  
    arising from a use of `+' at <interactive>:1:0-9  
    Possible fix: add an instance declaration for (Num [Char])  
    In the expression: 5 + "llama"  
    In the definition of `it': it = 5 + "llama"

GHCI告诉我们 "llama"不是一个数字所以它不知道怎么将它和5相加。即使不是"llama"而是"four"或者"4"，Haskell仍然不知道怎样将其转换成数字。+号期望其左右都是数字。如果尝试执行True == 4，GHCI将告诉我们类型不匹配。+号只对数字有效而==则只可以运用于两个可以比较的东西。关键是有相同的类型。不能将苹果和桔子进行比较。随后将详细探讨类型。提示：可以进行5 + 4.0因为5既可以被看作整数也可以被看作浮点数。4.0不能看作整数，因此5需要进行转型。

虽然可能你没有注意到，我们从一开始就在使用函数。如 * 是将两个数相乘的函数。如上所示，通过将 * 操作符夹在两个操作数中间来调用它。这就是所谓的*中缀*函数。大多数不是用于数字的函数是*前缀*函数，下面来看看它们。

由于函数一般都是前缀的，所以往后将不会陈述某个函数是前缀的，而是默认的。大多数命令式语言通过将函数名和包括它的参数的括号放在一起来调用，这些参数通过逗号来分隔。在Haskell里面，函数名和参数之间用空格进行分隔。我们先看看一些无聊的函数：

    ghci> succ 8  
    9

succ函数接受任何有后继的东西作为参数并返回其后继。如上所示，函数名和它的参数之间只有空格进行分隔。调用有多个参数的函数同样很简单。max和min函数接受两个能进行排序的东西作为参数。min返回较小的那个max则相反。

    ghci> min 9 10  
    9  
    ghci> min 3.4 3.2  
    3.2  
    ghci> max 100 101  
    101

函数调用的优先级最高，因此以下两行代码的效果是一样的：

    ghci> succ 9 + max 5 4 + 1  
    16  
    ghci> (succ 9) + (max 5 4) + 1  
    16

然而，如果想要得到9乘10的下一个数，不能写成 succ 9 * 10，因为这样将得到9的下一个数，即10。与10相乘得到100。必须写成 succ (9 * 10) 以得到正确结果91。

如果一个函数有两个参数，也可以通过将这个函数包围在反引号中作为中缀函数来调用。例如，div接受两个整数作为参数并返回两个数的商。div 92 10的结果是9。但是如果这样调用可能会比较另人费解，因为分不清除数和被除数。因此可以通过 92 `div` 10 将它作为中缀函数来调用，这样就非常清晰。

许多来自命令式编程语言的人趋向于坚持括号是函数调用的象征。例如，C语言就是这样调用函数的foo()，bar(1)或者baz(3, "haha")。如前面所说，Haskell使用空格来表示函数调用。以上这些函数在Haskell就是foo，bar 1 和 baz 3 "haha"。所以如果看到bar (bar 3)，意思不是bar的参数是 bar 和 3，而是先通过参数3调用函数bar得到一个结果，再用这个结果调用bar。在C语言里这种情况表示为bar(bar(3))。

### Baby的第一个函数

在前一章，我们获得了函数调用的一些基本印象。现在来动手创建自己的函数。打开字符编辑器并敲入这个函数：

    doubleMe x = x + x

函数的定义方式与它们的调用方式类似。函数名与其参数通过空格分隔。不同的是在函数定义中有一个 = 并且其后是函数体。将这个文件保存为baby.hs，然后进入到保存这个文件的文件夹并运行ghci。进入到ghci后执行:l baby。这里脚本已经被载入，可以在ghci中调用刚刚定义的函数：

    ghci> :l baby  
    [1 of 1] Compiling Main             ( baby.hs, interpreted )  
    Ok, modules loaded: Main.  
    ghci> doubleMe 9  
    18  
    ghci> doubleMe 8.3  
    16.6

因为 + 可以运用于整数和浮点数（实际上是任何可以被当作是数的东西），这个函数可以运用于任何数字。现在我们来定义一个函数，接受两个数字作为参数，将两者都乘以2再将结果相加。

    doubleUs x y = x*2 + y*2

简单。也可以定义成 doubleUs x y = x + x + y + y。记得将这个函数加到baby.hs的末尾，保存然后在ghci执行:l baby。

    ghci> doubleUs 4 9  
    26  
    ghci> doubleUs 2.3 34.2  
    73.0  
    ghci> doubleUs 28 88 + doubleMe 123  
    478

如你所料，可以在新的函数的定义中调用其他函数，这样可以将doubleUs重新定义为：

{:.haskell}
        doubleUs x y = doubleMe x + doubleMe y

这是Haskell常见的模式，创建一些明显正确的函数然后再将这些函数组合成更为复杂的函数。这样能避免重复。

Haskell对函数的定义顺序没有要求，所以是先定义doubleMe再定义doubleUs或者反过来都无关紧要。

接下来创建一个函数，当其参数小于或等于100时返回其与2的乘积，否则返回本身！

{:.haskell}
        doubleSmallNumber x = if x > 100  
                        then x  
                        else x*2

![IMG](http://s3.amazonaws.com/lyah/baby.png)

这里引入了Haskell的 if 声明。Haskell的 if 的命令式语言的 if 的差别在于Haskell对else部分是强制的。在命令式语言中如果条件不满足可以跳过一些特定的步骤，但是在Haskell中每一个表达式和函数都必须具有返回值。也可以把整个if声明写在一行，不过这种方式比较可读。Haskell的if声明的另一个特点是它是一个*表达式*，表达式基本上就是具有返回值的段代码。5是一个表达式因为它的返回值是5，4 + 8是表达式，x + y 也是表达式因为返回x和y的和。因为else部分是强制的，所以if声明一定会有返回值。如果要给以上的函数返回值再增加1，可以将函数体写成这样：

{:.haskell}
    doubleSmallNumber' x = (if x > 100 then x else x*2) + 1

如果省略括号，就只会在x大于100时加1。注意函数名后面的"'"。单引号在Haskell的语法中没有任何特殊意义，是函数名的合法字符。一般使用 "'"来表示一个函数的严格版本（非惰性），或者一个函数或者变量的稍微变化的版本。因为"'"是函数名的合法字符，可以声明一个这样的函数：

    conanO'Brien = "It's a-me, Conan O'Brien!"

这里有两件事值得注意。第一Conan的首字母没有大字，那是因为函数名不能以大写字母开头，随后将解释这么做的原因。另一个特点是这个函数没有不接受任何参数。当一个函数不接受任何参数时，通常说这是一个*定义*（或者*名字*）。因为一旦定义之后我们不能改变名字（和函数）的含义， `conanO'Brien`和字符串`"It's a-me, Conan O'Brien!"`能没有差别地使用。

### 列表List

![Lists](http://s3.amazonaws.com/lyah/list.png)

正如真实世界的购物列表，列表在Haskell中也是非常有用的。
