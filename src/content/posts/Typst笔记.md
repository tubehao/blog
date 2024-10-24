---

title: typst

published: 2024-10-24

description: 'rt'

image: ''

tags: [tutorial]

category: 'tools'

draft: false 

---

**`= Introduction`**

```
+ The climate
  - Temperature
  - Precipitation
+ The topography
+ The geology
```

```
Glaciers as the one shown in
@glaciers will cease to exist if
we don't take action soon!

#figure(
  image("glacier.jpg", width: 70%),
  caption: [
_Glaciers_ form an important part
    of the earth's climate system.
  ],
) <glaciers>
```

```
#bibliography("works.bib")

```

公式是内联排版的，与周围的文本在同一行上。 如果你想把它放在它自己的新行上，你应该在其开头和结尾插入一个空格：

要插入列向量，我们可以使用 [`vec`](https://typst-doc-cn.github.io/docs/reference/math/vec/) 函数。 在数学模式下，函数调用不需要以 `#` 字符开头。

```
$ Q = rho A v + "time offset" $

$ 7.32 beta +
  sum_(i=0)^nabla Q_i / 2 $

$ v := vec(x_1, x_2, x_3) $

gt.eq.not
```

公式也可以包含换行\。 每一行可以包含一个或多个*对齐点*（`&`）

```
#set text(
  font: "New Computer Modern",
  size: 10pt
)
#set page(
  paper: "a6",
  margin: (x: 1.8cm, y: 1.5cm),
)
#set par(
  justify: true,//左右对齐
  leading: 0.52em,//行间距
)

#set heading(numbering: "1.a")

= Introduction
In this report, we will explore the
various factors that influence fluid
dynamics in glaciers and how they
contribute to the formation and
behavior of these natural structures.

...

#align(center + bottom)[
  #image("glacier.jpg", width: 70%)

*Glaciers form an important
  part of the earth's climate
  system.*]
```

例如简单地实现类似 Markdown 中的引用文本样式：

`+ #lorem(10) \
  #rect(fill: luma(240), stroke: (left: 0.25em))[
    ***Solution:*** #lorem(10)

    $ a_(n+1)x^n = 2... $
  ]`