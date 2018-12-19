---
title: "Sublime3各种技巧"
date: 2018-12-19T15:37:24+08:00

featuredImg: ""
tags: 
  - sublime
---
# 代码片段
我不管用哪个ide，最先找的就是快捷键和怎么插入自己定义的代码片段。因为只要不断的使用，打磨出来的有用的代码片段会极大的提高效率；甚至规范我们的代码。让别人和自己都能很轻松的理解。
<!--more-->

具体的使用办法：

按照如下顺序，点击添加代码片段：

Tools --- Developer --- new snippet

```
<snippet>
	<content><![CDATA[
${1:this}
]]></content>
	<!-- Optional: 这里中间添加触发片段的关键词 -->
	<tabTrigger></tabTrigger>
	<!-- Optional: 这里是在哪个语言区域生效 -->
	<scope>source.go</scope>
	<!-- Optional: 对这个片段的描述 -->
	<description></description>

</snippet>

```

**重点：**
* 保存的代码片段文件，后缀：.sublime-snippet
like this:
`printSlice.sublime-snippet`


