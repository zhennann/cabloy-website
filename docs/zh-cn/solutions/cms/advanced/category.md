# 再谈目录：高级属性

在Cabloy-CMS中可以非常便捷的创建`目录`，这已经可以满足大多数场景的需求。

事实上，还可以给`目录`添加更多高级属性，从而实现更灵活的定制

## 高级属性

![](../../../assets/images/cms/16.png)

|名称|中文|类型|说明|
|-|-|-|-|
|categoryName|目录名称|String||
|hidden|是否隐藏|Boolean|如果true，则不渲染|
|sorting|排序|Number|渲染顺序|
|flag|标记|String|自定义字段，可根据需要存储合适的值|
|catalog|目录|Boolean|标记是否是`父目录`|
|language|语言|String|标记归属的语言|

- hidden: 比如要实现一个`About`页面，这个页面直接显示在菜单上。但是`文章`必须归属于`目录`，这时就可以创建一个隐藏目录


