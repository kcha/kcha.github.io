---
layout: post
title: Splitting data frames into smaller subsets
---

I recently needed a way to break my large data frame into 
smaller subgroups for batch processing. Sounds easy enough. Thanks to [Stack Overflow](https://stackoverflow.com/questions/3302356/how-to-split-a-data-frame), I learned
that there is the R command [`split()`](https://stat.ethz.ch/R-manual/R-devel/library/base/html/split.html). Re-using the example from the first answer
in the Stack Overflow post:

{% highlight r %}
x = data.frame(num = 1:26, let = letters, LET = LETTERS)
set.seed(10)
split(x, sample(rep(1:2, 13)))

$`1`
   num let LET
3    3   c   C
6    6   f   F
10  10   j   J
12  12   l   L
14  14   n   N
15  15   o   O
17  17   q   Q
18  18   r   R
20  20   t   T
21  21   u   U
22  22   v   V
23  23   w   W
26  26   z   Z

$`2`
   num let LET
1    1   a   A
2    2   b   B
4    4   d   D
5    5   e   E
7    7   g   G
8    8   h   H
9    9   i   I
11  11   k   K
13  13   m   M
16  16   p   P
19  19   s   S
24  24   x   X
25  25   y   Y
{% endhighlight %}

The second argument of `split()` essentially defines which groupings of `x` into
one of two groups. Each group consists of 13 values. While this solution is great
if you know exactly how to split your data (e.g. two groups of size 13), it's
not as intuitive if you have a thousands or millions of rows -- how many items should each group contain? Say you want to split it into `k` equal groups, what if `nrow(x)` does not evenly divide by `k`? What happens to the remainder? These can
be easily solved by some simiple arithmetic. To save time, I have created a 
convenience function, `split_k()`, to do just that.

<script src="https://gist.github.com/kcha/776adbead433e9df20bb.js"></script> 

Here, `split_k()` takes as input the data frame `x`, and the number of groups `k`.
The method will then split `x` into `k` evenly sized subsets. If `x` does not
divide evenly into `k`, then the remainder (computed by `rem <- nrow(x) %% k` ) is added
as the `k`'th group. Alternatively, the remainder can be assigned to its own `k+1` group.

Using the same example above, say we wanted to now split the data frame into 4 groups. Then, using `split_k()`, we would get:

{% highlight r %}
split_k(x, 4)

$`1`
  num let LET
1   1   a   A
2   2   b   B
3   3   c   C
4   4   d   D
5   5   e   E
6   6   f   F

$`2`
   num let LET
7    7   g   G
8    8   h   H
9    9   i   I
10  10   j   J
11  11   k   K
12  12   l   L

$`3`
   num let LET
13  13   m   M
14  14   n   N
15  15   o   O
16  16   p   P
17  17   q   Q
18  18   r   R

$`4`
   num let LET
19  19   s   S
20  20   t   T
21  21   u   U
22  22   v   V
23  23   w   W
24  24   x   X
25  25   y   Y
26  26   z   Z
{% endhighlight %}

