---
layout: post
title: 写一份报告
categories: [Toolbox]
tags: [Toolbox, Org mode, Git, Emacs]
---

## 写作正文
我认为对于写一份报告而言 Org mode 是最好的选择（谦虚点……之一）。

### 为什么不用 Markdown？

Markdown 表达能力不足。Markdown 设计初衷就是简单、可读性强，对于可能会比较复杂的报告表达能力略显不足。因此也出现了不少 Markdown 的扩展格式。

### 为什么不用 LaTeX？

- LaTeX 可读性较差，尤其是对于长文档。虽然可以通过增加类似 Org mode 的大纲功能改善体验，但是 `\begin{}...\end{}` 仍然是不可避免的。在 Org mode 中虽然也有 begin-end 块，但不如 LaTeX 中频繁。
- Org mode 有高效的键绑定。编辑 Org mode 可以使用 `M-<left>` 和 `M-<right>` 改变标题和列表项的层级，表格的编辑也格外惊艳，而编辑 LaTeX 没有类似的操作，当然这和编辑器的支持不无关系。


### Org mode 结合 LaTeX
尽管 LaTeX 有以上不足，但是它仍然是强大的排版系统，尤其对于数学公式。用 Org mode 无法表达的内容，最终还是由 LaTeX 解决。

所以最好的方法就是把它们结合在一起。Org mode 本身也支持嵌入 LaTeX 片段。

以下是一个简单的例子：

```
#+TITLE: report
#+AUTHOR:
#+DATE:

* Introduction

* Section
- Item 1
  - Item 2
- Item 3

** Subsection
\[ E = m c^2 \]

* Conclusion

```

## 调整格式
一般一份报告都是导出为 PDF，这项工作实际上是 Org mode 导出为 LaTeX 再生成 PDF。所以对格式的调整本质上是用 LaTeX 进行排版。

有以下方法可以调整格式：
- `#+OPTIONS:` 修改一些选项，例如 `toc:nil` 取消生成目录。
- `#+LATEX_HEADER:` 会写在 preamble 区域。
- `#+LATEX_CLASS:` 和 `#+LATEX_CLASS_OPTIONS:` 修改文档类。
- `#+ATTR_LATEX:` 对表格、图像、代码等修改环境选项。
- `#+BEGIN_EXPORT latex #+END_EXPORT` 直接插入 LaTeX 片段。

对前面的例子进行修改：
```
#+TITLE: report
#+AUTHOR:
#+DATE:
#+OPTIONS: toc:nil
#+LATEX_CLASS: article
#+LATEX_CLASS_OPTIONS: [12pt, a4paper]
#+LATEX_HEADER: \usepackage{ctex}

* Introduction

* Section
- Item 1
  - Item 2
- Item 3

** Subsection
\[ E = m c^2 \]

* Conclusion
```

## 生成报告
`C-c C-e l p` 导出为 LaTeX 生成 PDF 文件。

然而封面有时是给定的，需要合并封面和正文。首先需要在 `#+OPTIONS:` 中加入 `title:nil` 然后合并封面和正文。

可以用以下方法合并：
- 用 PDF 合并工具， 例如 `pdfjam cover.pdf report.pdf --outfile report-with-cover.pdf` 可以写成 Makefile。
- 用 LaTeX 宏包 `pdfpages`，例如 `\usepackage{pdfpages} \includepdf[pages=-]{cover}`。

最终：
```
#+TITLE: report
#+AUTHOR:
#+DATE:
#+OPTIONS: toc:nil title:nil
#+LATEX_CLASS: article
#+LATEX_CLASS: [12pt, a4paper]
#+LATEX_HEADER: \usepackage{ctex}
#+LATEX_HEADER_OPTIONS: \usepackage{pdfpages}

#+BEGIN_EXPORT latex
\includepdf[pages=-]{cover}
#+END_EXPORT

* Introduction

* Section
- Item 1
  - Item 2
- Item 3

** Subsection
\[ E = m c^2 \]

* Conclusion
```

## 记录修改
用 Git 记录写作过程中修改，改乱了也不怕，至少能避免出现“初稿、v1、v2、……、终稿、终稿2”之类的情况。另外还方便与人协作。

## 局限性
1. 如果封面给定，封面和正文的风格可能不统一。
2. 有些格式的导出是默认配置，如果默认配置修改，这份报告就不能做到可复现。
3. 如果要修改默认配置，这种修改是全局的，可能会影响其他文档的导出。
