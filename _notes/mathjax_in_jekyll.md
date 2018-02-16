---
title:  "Mathjax in Jekyll"
use_math: true
---

Some references I used to add Mathjax support in my notes: 

* [Add Mathjax support](http://haixing-hu.github.io/programming/2013/09/20/how-to-use-mathjax-in-jekyll-generated-github-pages/)
* [Color in equations](http://adereth.github.io/blog/2013/11/29/colorful-equations/)
    * Be aware of [downsides](https://math.meta.stackexchange.com/questions/4195/on-the-use-of-color-in-equations)
* [Jekyll Collections](https://jekyllrb.com/docs/collections/)

Test:

Inline: $\dot{x} = Ax+Bu$ 

Centered: 
$$P(A|X) = \frac{P(X|A)P(A)}{P(X)}$$

Set of equations (line break not working): 

\begin{align\*}
    \color{red}a \color{black} = 1,  \\\\
    \color{blue}{b} \color{black}= 1,  \\\\
    \color{red}{a} \color{black}+ \color{blue}{b} \color{black}= 2 
\end{align\*}