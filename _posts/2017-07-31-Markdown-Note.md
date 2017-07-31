---
layout: page
title: markdown语法笔记
---

### 标题字号 
```markdown
# 各级的标题
###### h6
```

### 区块引用

```markdown
> 
> you have use a format of blockquote.		#不一定每行都要加这个引导号，一个段落一个也行
>> This is nested blockquote.				#可以嵌套(多种markdown语法样式)
>#(1.)
```

### 列表

```markdown
*			#无序列表表示
+			#无序列表表示
-			#无序列表表示
1.			#有序列表表示
2.			#有序列表表示
3.			#有序列表表示
```

列表项目可以包含多个段落，段首加Tab。总而言之一句话，凡是嵌套在列表内部，要缩进。如：

```markdown
1.  This is a list item with two paragraphs. 

    Vestibulum enim wisi, viverra nec, fringilla in, laoreet
    vitae, risus. Donec sit amet nisl. Aliquam semper ipsum
    sit amet velit.

2.  This is a example.
	
	> this is a quote.
```

### 代码区块/分隔线

直接一个Tab开头写。

```markdown
ordinary;				#普通区域
	codeField;			#代码区块
*** --- 			  #分隔线
```

### 链接

```markdown
This is [an example](http://example.com/ "Title") inline link.	#有标题（可以相对路径）
[This link](http://example.net/) has no title attribute.		#无标题
This is [an example][id] reference-style link.					#id代替法
[id]: http://example.com/  (Optional Title Here)				#id的链接表示（用数字一般title要写）
```

### 强调\小段代码

```markdown
*single asterisks*			<em>single asterisks</em>
**strong asterisks**		<strong>double asterisks</strong>
`short code`				#小段代码的引用
```

### 图片

```markdown
![Alt text](/path/to/img.jpg "Optional title")		#图片的插入
![Alt text][id]
[id]: url/to/image  "Optional title attribute"		#与网址一致
```

### 另注

需要注意灵活使用<html>相关代码标签，会使得文档更加的协调。

##### datagridview


