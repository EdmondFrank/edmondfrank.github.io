---
layout: post
title: "JavaScript-DOM学习笔记"
date: 2016-06-27 16:23:25 +0800
comments: true
categories: 学习心得
---
#JavaScript DOM对象学习笔记
##节点属性
在文档对象(DOM)中,每个节点都是一个对象.DOM节点有三个重要属性:

(1) nodeName:节点名称,相当于tagName属性节点返回属性名,文本节点返回#text.
> 
1.元素节点的nodeName与标签名相同<br>
2.属性节点的nodeName是属性的名称<br>
3.文本节点的nodeName永远是#text<br>
4.文档节点的nodeName永远是#document<br>

**(注:nodeName,是只读的)**

(2) noType:节点类型 => 返回值:
> 
返回值为<br>
1,元素节点;<br>
2,属性节点;<br>
3,文本节点;<br>
8,注释;<br>
9.文档;<br>

**(注:nodeType,也是只读的)**

(3) nodeValue:节点的值,返回一个字符串,表示这个节点的值.元素节点返回null,属性节点返回属性值,文本节点返回文本.
> 
1.元素节点的nodeValue是undefined或null<br>
2.文本节点的nodeValue是文本自身<br>
3.属性节点的nodeValue是属性的值<br>

**(nodeValue除了对元素节点不能写外,可读可写,但一般只用于设置文本节点的值)**

##元素内容
我们有两种方法替换元素的内容,一个是innerHTML,另一个是innerText

- innerHTML属性用于获取或替换元素的内容.
- innerText属性用于获取或替换元素的文本内容，只有文本内容，没有其它HTML标签.

###使用语法
> 
Object.innerHTML
Object.innerText

**(Object 是获取的元素对象,如通过document.getElementById("ID")获取的元素)**

innerHTML 和 innerText 都是既可读又可写的,他们既可以直接输出值或赋值给变量.

eg:
```javascript
alert(Object.innerHTML)或
var ostr = Object.innerHTML
```
##元素样式
###1.style的使用语法:
> 
Object.style.property = new style;

**(property为css样式属性,比如 color,width等等)**
###2.className属性可以设置或返回元素的class 属性
>
Object.className = classname;

**(className为元素的class属性)**
##遍历节点树
###父子节点
可以使用childNodes对象访问选定元素节点下的所有子节点的列表,返回的值可以看作是一个数组,他具有length属性。如果选定的节点没有子节点,则该属性返回不包含节点的 NodeList.
> elementNode.childNodes

有子节点,相应的,就有父亲节点.
parentNode可获取指定节点的父节点
> elementNode.parentNode

**(注意:父节点只能有一个)**
###兄弟节点
在 DOM 树形结构中,除了父子关系属性外,还有同辈关系属性.
同辈关系中,也分为两种,一种是向前的同辈关系 previousSibling,另一种是向后的同辈关系nextSibling
**(注:两个属性获取的是节点,所以我们可以先判断节点nodeType是否为1,如不为元素节点,跳过)**
##创建节点
###创建元素
> document.createElement(tagName);

> 参数说明：
tagName：字符串值,这个字符串用来指明创建元素的类型.

**(注意:createElement()方法只是创建元素,并不会自动出现在文档中,如果想显示在浏览器中,还需要与appendChild() 或 insertBefore()方法联合使用)**

###创建文本节点
> document.createTextNode(str);

> str,为字符串值,可规定此节点文本.

##添加与删除节点
> nodeObject.appendChild(newnode);

nodeObject:父节点;newnode指定追加的节点.

> nodeObject.removeChild(node);

参数意义同上.

**(注:把删除的子节点赋值给rm,这个子节点不在 DOM 树中,但是还存在内存中,如果要完全删除对象,需给rm赋null值)**

##指定位置插入字节点
> fnode.insertBefore(newnode,node);

参数说明:
> 
newnode:必需,表示需要插入的新节点<br>
node:可选,在其之前插入新节点的子节点,默认为末尾插入<br>
返回值:插入的节点<br>

##子节点的替换
> fnode.replaceChild(newnode,oldnode);

参数说明:
> 
newnode:用于替换的新节点<br>
oldnode:被newnode替换的节点<br>
返回值:被替换的节点<br>

