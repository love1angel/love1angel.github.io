---
title: Markdown
date: 2022-03-19 11:44:08
categories:
tags:
---

# What is Markdown?

Markdown 是一种轻量级的文本标记写法（markup language），广泛用于 GitHub，博客等各种 Web 网站。[Markdown guide](https://www.markdownguide.org/) 网站源码：[GitHub](https://github.com/mattcone/markdown-guide)，网站教程已经写的很好，本文借鉴于此，做个笔记

## 原理

处理 markdown 文件的应用程序使用一种称为 Markdown 处理器，也叫解析器或者实现的东西将该格式文件转换为 HTML, 再通过 Web 浏览器进行显示


# Why use Markdown?

1. easy to learn, use, light-weight

2. place emphasis on content, not mark format

3. can be transfer into html or other format, easy for distribution

4. 微软的 word 等软件太生草了，写的时候纠结于格式，打开还要下载特定的软件，不适合到处纷发这种特性，**Markdown 注重的是写作的内容而不是格式问题**

5. Markdown 可以内嵌 HTML 语法，许多网站内嵌 Markdown 处理器，适合现在 Web 浏览器显示

## 生成 [GitHub](https://docs.github.com/cn) Pages

GitHub 为 Jekyll 提供免费的网页 host 服务

[GitHub Pages](http://markdown.p2hp.com/tools/github-pages/index.html) uses [Jekyll](http://markdown.p2hp.com/tools/jekyll/index.html) as the backend for its free website creation service

[其他可用的静态网站生成器](https://www.staticgen.com/)

如果您想使用内容管理系统（CMS）来为网站提供动力，请查看[Ghost](https://ghost.org/)。这是一个免费的开源博客平台，具有出色的Markdown编辑器。如果您是WordPress用户，您将很高兴知道WordPress.com上托管的网站有[Markdown支持](https://en.support.wordpress.com/markdown/)。自托管的WordPress网站可以使用[Jetpack插件](https://jetpack.com/support/markdown/)。

# How to use Markdown?

## Basic Syntax

1. 标题

   ```markdown
   # 标题
   or
   <h1></h1>
   <h2></h2>

   段落之间空行，且不要缩进。用 &nbsp;
   <p></p>
   <br>
   ```

2. 粗体

   ```markdown
   **bold**
   or
   <strong></strong>
   ```

3. 斜体

   ```markdown
   *italic*
   or
   <em></em>
   ```

4. 块引用

   ```markdown
   > blockquote
   >> two
   之间空行
   > ###
   >
   > -
   ```

5. 有序列表

   ```markdown
   1. ordered list
   2. two

   <ol>
       <li></li>
       <li></li>
   </ol>

   1968\. 转义

   在列表中通过缩进四个空格来添加其他元素
   ```

6. 无序列表

   ```markdown
   - unordered list
   <ul>
       <li></li>
       <li></li>
   </ul>
   ```

7. code

   ```markdown
   `code`
   <code></code>
   ``Use `code` in your Markdown file.``
   ```

8. 水平线 horizontal rule

   ```markdown
   ---
   之间空行
   ```

9. 链接

   ```markdown
   [title](link address "悬停提示")

   直接显示链接
   <https://www.markdownguide.org>
   <fake@example.com>

   I love supporting the **[EFF](https://eff.org)**.
   This is the *[Markdown Guide](https://www.markdownguide.org)*.
   See the section on [`code`](#code).

   <a href="" title="">link</a>
   与原来的链接一样，1 为 1 的链接
   [hobbit-hole][1]
   [1]: https://en.wikipedia.org/wiki/Hobbit#Lifestyle "Hobbit lifestyles"
   Markdown 中链接如果有空格用%20代替，否则用 HTML 的 tag
   ```

10. 图片

    ```markdown
    ![alt 不显示的时候](image.jpg "title")

    [![An old rock in the desert](/assets/images/shiprock.jpg "Shiprock, New Mexico by Beau Rogers")](https://www.flickr.com/photos/)
    ```

块级元素之间分行，尽量不要缩进 tag，会干扰格式，且不能在块级元素使用 Markdown 语法，不会工作

\进行转义

## Extended Syntax

[GitHub Flavored Markdown (GFM)](https://github.github.com/gfm/)

1. 表格 table

   ```markdown
   | class | name | test |
   |:------|:----:|-----:|
   | 1     | 2    | \|   |

   | 可以用 &#124; 表中可以用 HTML 语法加入 list
   ```

2. 代码块 fenced code block

   ```markdown
   ```json
   {
       "firstname": "John",
       "lastname": "Smith",
       "age": 25
   }
   \`\`\`
   ```

3. 脚注 footnote

   ```
   可以添加标识符，但实际上仍然是按顺序标号
   Here's a simple footnote,[^1] and here's a longer one.[^bignote]

   [^1]: This is the first footnote.

   [^bignote]: Here's one with multiple paragraphs and code.

       Indent paragraphs to include them in the footnote.

       `{ my code }`

       Add as many paragraphs as you like.
   ```

4. 标题 ID heading ID

   ```markdown
   ### My Great Heading {#custom-id}
   <h3 id="custom-id">My Great Heading</h3>
   [Heading IDs](#heading-ids)
   <a href="#heading-ids">Heading IDs</a>
   [Heading IDs](https://www.markdownguide.org/extended-syntax#heading-ids)
   ```

5. 定义列表 definition list

   ```markdown
   term
   : definitionFirst Term
   : This is the definition of the first term.

   Second Term
   : This is one definition of the second term.
   : This is another definition of the second term.

   <dl>
     <dt>First Term</dt>
     <dd>This is the definition of the first term.</dd>
     <dt>Second Term</dt>
     <dd>This is one definition of the second term. </dd>
     <dd>This is another definition of the second term.</dd>
   </dl>
   ```

6. 删除线 strikethrough

   ```markdown
   ~~The world is flat.~~
   ```

7. 任务列表 task list

   ```markdown
   - [x]
   - [ ]
   ```

8. 表情 emoji

   [Emojipedia](https://emojipedia.org/)

   [list of emoji shortcodes](https://gist.github.com/rxaviers/7360908)

   ```markdown
   That is so funny! :joy:
   ```

9. 高亮 highlight

   ```markdown
   I need to highlight these ==very important words==.
   <mark></mark>
   ```

10. 下标 subscript

    ```markdown
    H~2~0
    <sub>2</sub>
    ```

11. 上标 superscript

    ```markdown
    X^2^
    <sup>2</sup>
    ```

diable automatic URL linking

```markdown
`http://www.example.com`
```

## Hacks

```markdown
# 下划线 underline
<ins></ins>

# indent(Tab)
&nbsp; non-breaking space

# center
<p style="text-align:center">Center this text</p>

# color
<p style="color:blue">Make this text blue.</p>

# comment
[This is a comment that will be hidden.]: #

# 告诫 admonitions
> :warning: **Warning:** Do not push the big red button.

> :memo: **Note:** Sunrises are beautiful.

> :bulb: **Tip:** Remember to appreciate the little things in life.

# image size
<img src="image.png" width="200" height="100">

# 图像说明 image captions
<figure>
    <img src="/assets/images/albuquerque.jpg"
         alt="Albuquerque, New Mexico">
    <figcaption>A single track trail outside of Albuquerque, New Mexico.</figcaption>
</figure>

or

![Albuquerque, New Mexico](/assets/images/albuquerque.jpg)
*A single track trail outside of Albuquerque, New Mexico.*

# link target
<a href="https://www.markdownguide.org" target="_blank">Learn Markdown!</a>


# symbols
Copyright (©) — &copy;
Registered trademark (®) — &reg;
Trademark (™) — &trade;
Euro (€) — &euro;
Left arrow (←) — &larr;
Up arrow (↑) — &uarr;
Right arrow (→) — &rarr;
Down arrow (↓) — &darr;
Degree (°) — &#176;
Pi (π) — &#960;

# table 里的元素
<br>
list

# table of contents
- [Underline](#underline)
- [Indent](#indent)
- [Center](#center)
- [Color](#color)

# video
YouTube automatically generates an image for every video (https://img.youtube.com/vi/YOUTUBE-ID/0.jpg), so we can use that and link the image to the video on YouTube. After we replace the image alt text and add the ID of the video, our example looks like this:
[![Less Than Jake — Scott Farcas Takes It On The Chin](https://img.youtube.com/vi/PYCxct2e0zI/0.jpg)](https://www.youtube.com/watch?v=PYCxct2e0zI)
```
