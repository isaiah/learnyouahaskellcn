语法
========

### 模式匹配

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

这是我们第一次递归定义函数。递归在Haskell中非常重要，我们将在随后更深入了解。为了得到3的阶乘，Haskell将尝试计算`3 * factorial 2`，2的阶乘是`2 * factorial 1`，现在我们有了`3 * (2 * factorial 1)`。`factorial 1`等于`1 * factorial 0`，所以结果是`3 * (2 * (1 * factorial 0))`。技巧就在这里，我们在第一个模式将0的阶乘定义为1，所以最后的结果就是`3 * (2 * (1 * 1))`。如果我们把第二个模式放到前面，它将捕获包括0在内的所有数字，计算将永远无法终结。这就是为什么设置模式的时候顺序非常重要，越特殊的模式放在越前面而越一般的应该放在后面。

模式匹配也能失败。例如定义如下函数：

    charName :: Char -> String  
    charName 'a' = "Albert"  
    charName 'b' = "Broseph"  
    charName 'c' = "Cecil"

然后用非预期的参数调用，就会发生下面的情况：

    ghci> charName 'a'  
    "Albert"  
    ghci> charName 'b'  
    "Broseph"  
    ghci> charName 'h'  
    "*** Exception: tut.hs:(53,0)-(55,21): Non-exhaustive patterns in function charName"

提示函数的模式不是穷尽的。当设置模式时，应该包含一个能捕获所有的模式这样程序就不会在碰到非预期的输入时崩溃。

模式匹配也能用于元组。假设需要一个函数将2D空间的两个向量（用二元组表示）进行相加。两个向量相加需要分别将每个向量的x部分和y部分相加，下面是不用模式匹配的写法：

    addVectors :: (Num a) => (a, a) -> (a, a) -> (a, a)  
    addVectors a b = (fst a + fst b, snd a + snd b)

这是正确的，但是可以用更好的方法实现。下面用模式匹配改造这个函数。

    addVectors :: (Num a) => (a, a) -> (a, a) -> (a, a)  
    addVectors (x1, y1) (x2, y2) = (x1 + x2, y1 + y2)

看起来好多了，并且这个是一个包含所有情况的模式。`addVectors`的类型是`addVectors :: (Num a) => (a, a) -> (a, a) - > (a, a)`，这样就能保证参数是两个二元组。

`fst`和`snd`能够分解二元组。但是三元组呢？系统没有预定义的函数，需要我们自己定义。

    first :: (a, b, c) -> a  
    first (x, _, _) = x  
      
    second :: (a, b, c) -> b  
    second (_, y, _) = y  
      
    third :: (a, b, c) -> c  
    third (_, _, z) = z

下划线`_`与它在列表集合中的含意相同。它意味着我们根本不关心那个部分是什么，所以只是一个`_`。

这让我想起在列表集合中也可以使用模式匹配，看看下面的集合：

    ghci> let xs = [(1,3), (4,3), (2,4), (5,3), (5,6), (3,1)]  
    ghci> [a+b | (a,b) <- xs]  
    [4,7,6,8,11,4]

如果一个模式匹配失败，它直接转移到下一个元素。

列表也能用于模式匹配。可以匹配空列表`[]`或者任何涉及`:`和空列表的模式。因为`[1,2,3]`只是`1:2:3:[]`的语法糖衣，也可以使用这个正式的模式。像`x:xs`这样的模式能将列表的头绑定到`x`，剩下的部分绑定到`xs`，即使这个列表只有一个元素，这样`xs`的值就是空列表。

> 注意： `x:xs`模式使用非常频繁，尤其在递归函数中。但是带有`:`的模式只能匹配到长度为1以上的列表。

如果想要绑定列表的头三个元素到变量[^4]，剩下的部分绑定到另一个变量，可以使用`x:y:z:zs`这样的模式。它只能匹配长度大于等于3的列表。

我们知道了怎么去匹配一个列表，现在来实现一个我们自己的`head`函数。

    head' :: [a] -> a  
    head' [] = error "Can't call head on an empty list, dummy!"  
    head' (x:_) = x

检查它能不能运行：

    ghci> head' [4,5,6]  
    4  
    ghci> head' "Hello"  
    'H'

注意如果想要绑定多个变量（即使其中一个只是`_`，并且实际上没有绑定什么东西），必须用括号括起来。同时注意到`error`函数，它接受一个字符串并生成一个运行时错误，用这个字符串表示发生的什么错误。它将导致程序崩溃，所以不要太多地使用它。但是对空列表使用`head`并没有什么意义。

现在让我们一个函数来用英语陈述一个列表的头几个元素。

    tell :: (Show a) => [a] -> String  
    tell [] = "The list is empty"  
    tell (x:[]) = "The list has one element: " ++ show x  
    tell (x:y:[]) = "The list has two elements: " ++ show x ++ " and " ++ show y  
    tell (x:y:_) = "This list is long. The first two elements are: " ++ show x ++ " and " ++ show y
    
这个函数是安全的，因为它处理了空列表，单元素列表，两个元素的列表和超过两个元素的列表。`(x:[])`和`(x:y:[])`也可以写成`[x]`和`[x,y]`（因为它是语法糖衣，不需要括号）。但是不能将`x:y:_)`改写成方括号的形式，因为它匹配任何超过两元素的列表。

前面已经用列表集合实现过`length`，现在将使用模式匹配和递归实现它：

    length' :: (Num b) => [a] -> b  
    length' [] = 0  
    length' (_:xs) = 1 + length' xs

这与阶乘函数类似。首先定义一个特定输入（空列表）的结果，这通常也被称为边界条件。在第二个模式中将这个列表分割成头和尾两部分。总长度等于1加长尾巴的长度。我们用`_`来匹配头元素，因为我们实际上不关心这是什么东西。我们也照顾到了列表的所有情况，第一个模式匹配空列表，第二个模式能够匹配所有非空列表。

下面来看看用`"ham"`调用`langth'`的结果。首先，它将检查参数是否空列表，第一个模式没有匹配成功，接着尝试匹配第二个模式。第二个模式匹配成功，将参数分裂成头和尾两个部分，并返回结果`1 + length' "am"`。类似地，`length' "am"`等于`1 + length' "m"`。现在已经得到`1 + (1 + length' "m")`。`length' "m"`等于`1 + length' ""`（也可以写成`1 + length' []`）。前面已经将`length' []`定义为0。所以最后的结果就是`1 + (1 + (1 + 0))`。

现在让我们来实现`sum`，我们已经知道空列表的和是0，将这作为第一个模式。列表的长度是头的长度与剩余部分长度之和，可以得到：

    sum' :: (Num a) => [a] -> a  
    sum' [] = 0  
    sum' (x:xs) = x + sum' xs  

有一种叫做_作为模式_[^2]的东西，是用来根据某种模式将参数分解成多个部分的同时保持整个参数的引用。在模式前面加上`@`和一个名字就可以将整个部分绑定到这个名字。例如，模式`xs@(x:y:ys)`，这个模式匹配的东西和`x:y:ys`一模一样，但是却仍然可以通过`xs`取得整个列表，而不是在函数体内再用`x:y:ys`连接起来。下面是一个简单的例子：

    capital :: String -> String  
    capital "" = "Empty string, whoops!"  
    capital all@(x:xs) = "The first letter of " ++ all ++ " is " ++ [x]  

    ghci> capital "Dracula"  
    "The first letter of Dracula is D" 

通常我们使用“作为模式”来避免在匹配一个很长的模式的同时又要使用整体时出现重复。

还有一件事值得注意－－不能在模式匹配中使用`++`。试想匹配一个`(xs ++ ys)`的模式，怎样区分第一个列表和第二个列表的元素？这是没有意义的。匹配`(xs ++ [x,y,z])`或者`(xs ++　[ｘ])`可能有些意义，但是因为列表的天性，我们不能这样做。

###　守卫，守卫！

![guards](http://s3.amazonaws.com/lyah/guards.png)模式是作用是测试某个值是否满足一定的模式并分解它，而守卫的作用则是判断一个值（或多个值）的某些属性为真或假。听起来像是if语句，实际也非常类似。不同点在于当有多个条件时，守卫的可读性更强，而且能与模式很好的配合。

不管守卫的语法，先来看一个用守卫构建的函数。这个函数将根据你的身体质量指数BMI[^3]对你进行不同的斥责。身体质量指数等于体重除以身高平方。BMI低于18.5的人偏瘦，18.5和25之间与正常，25到30之间的人过重，超过30就是肥胖。下面是这个函数（现在不会进行计算，这个函数只是接收BMI作为参数并告诉你相应的结果）：

    bmiTell :: (RealFloat a) => a -> String  
    bmiTell bmi  
        | bmi <= 18.5 = "You're underweight, you emo, you!"  
        | bmi <= 25.0 = "You're supposedly normal. Pffft, I bet you're ugly!"  
        | bmi <= 30.0 = "You're fat! Lose some weight, fatty!"  
        | otherwise   = "You're a whale, congratulations!"

守卫用一个跟随在函数名和参数后面的管道表示。通常这些守卫会向右缩进并纵身对齐。一个守卫本质上就是一个布尔表达式。如果这个表达式的求值为`True`，则执行相应的函数体。如果求值为`False`，则继续检查下一个。如果用24.3调用这个函数，它首先将检查参数是否小于或等于18.5，因为是大于，所以会接着检查下一个守卫。这个检查对被二个守卫截断，因为24.3小于25.0，函数返回第二个字符串。

这让人回忆起命令式语言中的if else树，只不过这种方式更为可读。虽然大家都反对大的if else树状结构，但有些时候就是无法避免。守卫为这种情况提供了另一种优雅的解决办法。

通常最后一个守卫是`otherwise`，`otherwise`的定义是`otherwise = True`，它将捕获所有的情况。这与模式很相似，不同的是守卫检查的是布尔条件，而后者检查的是输入是否满足一定的模式。

守卫的应用当然不仅于上面的情况，可以将上面的函数修改一下，以让用户可以直接输入身高和体重，而不用用户调用这个函数之前自己计算BMI。

    bmiTell :: (RealFloat a) => a -> a -> String  
    bmiTell weight height  
        | weight / height ^ 2 <= 18.5 = "You're underweight, you emo, you!"  
        | weight / height ^ 2 <= 25.0 = "You're supposedly normal. Pffft, I bet you're ugly!"  
        | weight / height ^ 2 <= 30.0 = "You're fat! Lose some weight, fatty!"  
        | otherwise                 = "You're a whale, congratulations!"

看看我是不是太胖了。。

    ghci> bmiTell 85 1.90  
    "You're supposedly normal. Pffft, I bet you're ugly!"

YEH！我不是胖子。但是Haskell说我丑，管它呢！

注意在函数参数和第一个守卫之间没有`=`号。很多新人可能犯这个错误。

另一个非常简单的例子，让我们实现`max`函数，这个函数接受两个可以进行比较的东西作为参数并返回其中较大者。

    max' :: (Ord a) => a -> a -> a  
    max' a b   
        | a > b     = a  
        | otherwise = b

守卫也可以写成一行，但是这样的可读性很差，强烈建议不要这样做，即使是很短的函数。为了演示，可以将`max'`写成这样：

    max' :: (Ord a) => a -> a -> a  
    max' a b | a > b = a | otherwise = b

很不好读！接着用守卫实现我们自己的`compare`。

    myCompare :: (Ord a) => a -> a -> Ordering  
    a `myCompare` b  
        | a > b     = GT  
        | a == b    = EQ  
        | otherwise = LT

    ghci> 3 `myCompare` 2  
    GT  

>注意：函数不但可以通用反引号进行中缀调用，还可以用反引号定义。这种方式有时候更好读一些。

### Where!?

我们在上一节定义了如下一个函数计算BMI：

    bmiTell :: (RealFloat a) => a -> a -> String  
    bmiTell weight height  
        | weight / height ^ 2 <= 18.5 = "You're underweight, you emo, you!"  
        | weight / height ^ 2 <= 25.0 = "You're supposedly normal. Pffft, I bet you're ugly!"  
        | weight / height ^ 2 <= 30.0 = "You're fat! Lose some weight, fatty!"  
        | otherwise                   = "You're a whale, congratulations!"

注意在上面有代码重复了三次，程序里面的重复（三次）比照脑袋踢上一脚还要糟糕。因为是同一个表达式重复了三次，如果能计算一次这个结果并绑定到一个变量，然后在函数体内使用这个变量而不是这个表达式就好了。那样可以将这个函数修改成如下形式：

    bmiTell :: (RealFloat a) => a -> a -> String  
    bmiTell weight height  
        | bmi <= 18.5 = "You're underweight, you emo, you!"  
        | bmi <= 25.0 = "You're supposedly normal. Pffft, I bet you're ugly!"  
        | bmi <= 30.0 = "You're fat! Lose some weight, fatty!"  
        | otherwise   = "You're a whale, congratulations!"  
        where bmi = weight / height ^ 2

我们将`where`关键词放到所有的守卫后面（最好和管道保持同样的缩进），然后定义一些变量和函数。这些变量对所有守卫可见，使我们可以不必在每个守卫重复同样的表达式。如果决定用不同的方式计算BMI，也只需要修改一次表达式。因为给表达式赋予了一个名字增强了程序的可读性，也因为只需要计算一次而加快的程序的执行速度。甚至可以将函数写成这样：

    bmiTell :: (RealFloat a) => a -> a -> String  
    bmiTell weight height  
        | bmi <= skinny = "You're underweight, you emo, you!"  
        | bmi <= normal = "You're supposedly normal. Pffft, I bet you're ugly!"  
        | bmi <= fat    = "You're fat! Lose some weight, fatty!"  
        | otherwise     = "You're a whale, congratulations!"  
        where bmi = weight / height ^ 2  
              skinny = 18.5  
              normal = 25.0  
              fat = 30.0  



[^1]:这里类似于switch..case语句，但是其实模式匹配比switch语句要强大得多－－译者。
[^2]: as patterns
[^3]: Body mass index
[^4]: 这里的原文是“names”，与通常意义上的变量是不同的。因为Haskell实际上不存在可变的(mutable)量。
