---
layout: page
title: "Demo"
permalink: /demo/
description: Demo
---

## Basic Elements

### Heading 3

*italic*, **bold**, ~~strikethrough~~


> blockquote

horizontal rule

--------------------------------------------------


table

| A      | B    | C    |
|--------|------|------|
| 123456 | 7890 | ABCD |
| 1      | 3    | 9    |

## Ruby Charecter

{% include ruby_char.html below="<a href=\"https://en.wikipedia.org/wiki/Ruby_character\">Ruby Charecter</a>" above="旁注标记" %}

```
{% include ruby_char.html below="<a href=\"https://en.wikipedia.org/wiki/Ruby_character\">Ruby Charecter</a>" above="旁注标记" %}
```

## Code Highlighting

Python
```python
import os
import time

print("Hello world!")

```

C
```c
#include <stdio.h>

int main()
{
    printf("Hello world!");
}
```

Scheme
```scheme
(+ 1 2)

(define (cube x) (* x x x))
```

## LaTeX

\\[
\lim_{n \to \infty}
\sum_{k=1}^n \frac{1}{k^2}
= \frac{\pi^2}{6}
\\]

```latex
\\[
\lim_{n \to \infty}
\sum_{k=1}^n \frac{1}{k^2}
= \frac{\pi^2}{6}
\\]
```

KaTeX: https://katex.org


$$2^n$$

```
$$2^n$$
```
