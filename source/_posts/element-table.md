---
title: element ui中table的多选selection会被重置的问题
date: 2020-04-20 16:17:26
categories:
- [前端]
tags:
- 前端
- vue
- element ui
---

# 遇到的问题

在element ui中，使用type = “selection“可以插入表格的多选

```html
<el-table
    ref="multipleTable"
    :data="tableData"
    style="width: 100%"
    @selection-change="handleSelectionChange">
    <el-table-column
      type="selection"
      width="55">
```

但是当同一页面中出现分页按钮，el-select选择框或带有选择的input时（具有‘选择’功能的按钮），table的多选会被重置，并触发change事件。

![element-table1](http://imagezxr.test.upcdn.net/blogimg/2020-04/element-table1.png)

先选中某几个

![element-table2](http://imagezxr.test.upcdn.net/blogimg/2020-04/element-table2.png)

在选中右下角el-select中的某一项，就会使多选消失。因为这个问题已经解决了，所以这里是手动去掉的选项，为了展示存在bug的效果。包括但不限于翻页，input的选择框，也会导致selection被重置。

# 解决方案

一开始我是准备通过js来控制selection的选中，但是el-select的change方法比selection的消失更早触发，而且，还需要判断多选的消失是否是我们想要的，不可能为每一个选中操作的绑定一个复杂的change。这个思路有点问题了。

后来在文档里发现了reserve-selection，可以用于解决这个问题。

> 仅对 type=selection 的列有效，类型为 Boolean，为 true 则会在数据更新之后保留之前选中的数据（需指定 `row-key`）

在使用这一属性的同时，要注意table必须存在row-key，以达到对不同分页的行对象进行区分。

```html
<el-table
      v-loading="listLoading"
      :data="list.slice((currentPage-1)*pagesize,currentPage*pagesize)"
      element-loading-text="Loading"
      border
      fit
      highlight-current-row
      :row-key="getRowKey"
      @selection-change="handleSelectionChange"
    >
      <el-table-column type="selection" width="40" :reserve-selection="true" />
```

```javascript
getRowKey(row) {
      return row.id
    },
```

这么做，就可以达到我们所需要的效果：

![element-table3](http://imagezxr.test.upcdn.net/blogimg/2020-04/element-table3.png)

此时即使翻页，也不会影响已经选中的部分。

# 总结

有一说一，这不算是element本身的bug，因为他对这种这种情况做出了解决的方案。但这算是一个缺少人性化的一个部分。为什么这个bug卡了好几个小时，还是与我没有认真看文档有关。过去用一个框架的习惯总是直接上手，遇到了问题再去查文档，以至于很多问题浪费了时间，还没有得到最优的解法。但这次el-table的属性和方法有点多，而且文档写的属实有点简洁。

其实这个问题在一月的时候，就有人在[issue](https://github.com/ElemeFE/element/issues/18635)上提出来了，后来在Stack Overflow上又遇到了这位老哥的问题，也是一个和我一样不看文档的（不过这哥们情有可原，看起来是阿拉伯语的人士，最多看看英文文档）。当然找到了解决方法后也给他发了解答，不过三个月过去了他可能已经不用了。

前几天vue3.0 的beta版已经开始公测了，感觉吸取了一部分react的东西，估计等开始商业化使用也要一年，这一年里，各大vue相关的框架应该也会推出支持3.0版的。element是个挺优秀的框架，但最近维护和更新都不怎么频繁了，十三号更新了一次，但再上次就是半年前了，不知道是不是去准备3.0的东西了。

4月21日晚上，b站直播会有尤大来谈vue3.0，正好可以去康康大佬直播。