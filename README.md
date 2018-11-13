## 读书笔记
- 用于记录阅读书籍中的精髓，mark一些技能

# Markdown语法汇总

* Markdown兼容Html语法

## 标题
* 以`#`号开头
```
# 这是 H1

## 这是 H2

###### 这是 H6
```

## 区块引用
> 以`>`开头，多个表示嵌套使用

> 一段引用
>> 嵌套引用
>>> 三层嵌套

```
> 一段引用
>> 嵌套引用
>>> 三层嵌套
```

## 列表
* **无序列表** ： 使用`* 或 + 或 -`都可以开户无序列表
* **有序列表** ：则使用数字接着一个英文句点`.`
* 列表内允许缩进：通过换行，使下一段落缩进
1. 缩进段落

    这是缩进
```
- 无序列表

1. 缩进段落

    这是缩进
```

## 字体
- **加粗**、*斜体*、***加粗斜体***、~~删除线~~
```
**加粗**、*斜体*、***加粗斜体***、~~删除线~~
```

## 分割线
* 3个或3个以上的`-`或`*`

-----

```
-----
```

## 表格

表头|表头|表头
---|:--:|---:
内容|内容|内容

```
表头|表头|表头
---|:--:|---:   # 此行一个 - 就可以， 两端 ：表示居中，右：表示右对齐，左：表示左对齐
内容|内容|内容
```

## 任务列表

- 兼容性一般，要隔开一行

- [x] 任务一
- [ ] 任务二
- [ ] 任务三

## Emoji
- 兼容性一般

## HTML

- markdown是支持原生HTML的，所以要以直接用html标签书写
    - 也可以添加行内样式

<h3 style="color:orange">橙色大标题</h3>

```
<h3 style="color:orange">橙色大标题</h3>
```
