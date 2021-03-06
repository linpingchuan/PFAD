


###简介

我们将解决一个最早由Martin Rem提出的问题。Rem的算法用了二分查找，我们使用另一种基于分治的方法，复杂度是O(nlogn)。

定义：如果i < j且x[i] < x[j]，则x[j]为x[i]的“超越数”。统计字符串GENERATING的每个字母的超越数个数

    G   E   N   E   R   A   T   I   N   G 
    5   6   2   5   1   4   0   1   0   0  
    
”超越数“个数最多的是第二个E，它的“超越数”包括N，R，T，I，N，和G。

给定长为n的数组，每个元素都有0到n-1个“超越数”，求“超越数”个数的最大值


###说明

假设给定的输入是列表（Haskell的基础数据结构）而非数组。这个问题的解法可以很直观地表达

    msc :: Ord a => [a] -> Int 
    msc  xs  = maximum [scount z zs | z : zs <- tails xs] 
    
    -- scount作用为计算元素x的“超越数”个数
    scount x xs   = length (filter (x <) xs)  
    

tails是Data.List标准库的函数，作用为返回列表的所有后缀。复杂度为O(n)，也可以自己实习

    tails :: [a] -> [[a]] 
    tails [] = []
    tails (x : xs) = (x : xs) : tail xs 
    
msc先计算列表每一个元素的“超越数”个数，再求其中的最大值。 

算法的复杂度：tails函数O(n)，scount求第一个元素的“超越数”个数要n-1次，第二个n-2次...总共复杂度为O(n^2）。
最后maximum也是O(n)，故总复杂度为O(n^2)。

这个算法其实是暴力破解。


###分治法
我们的目标是O(nlogn)的算法，自然想到了分治。如果可以找到函数join，使得：

    msc (xs ++ ys)  =  join (msc xs) (msc ys) 
    
并且join是线性复杂度的，那么整体复杂度T(n)就可以表示成

    T(n) = 2T(n/2) + O(n)， 结果T(n) = O(nlogn) 






    msc' :: Ord a => [a] -> Int 
    msc' = maximum . map snd . table 
    
    table [x] = [(x, 0)]
    table xs = join (m-n) (table ys) (table zs)
        where m = length xs
            n = m `div` 2
            (ys, zs) = splitAt n xs

    join _ txs [] = txs
    join _ [] tys = tys
    join n txs@((x,c):txs') tys@((y,d):tys')
        | x < y = (x, c+n) : join n txs' tys
        | otherwise = (y,d): join (n-1) txs tys'
