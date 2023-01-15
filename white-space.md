本文涉及到white-space的几个场景

先看大佬的文章[为什么white-space:nowrap可以让文字一行显示？](https://www.zhangxinxu.com/wordpress/2021/07/css-white-space-nowrap/)

从这篇文章可以得出以下结论:

1. `white-space:nowrap`可以让所有的空格、换行都不再换行，因此，文字内容会一行显示
2. `white-space:pre`可以让空格不合并，同时不会换行显示；
3. `white-space:pre-wrap`可以让空格不合并，同时宽度不足时候可以换行显示；

案例一 文字一行显示：

```css
overflow: hidden;
white-space: nowrap;
text-overflow: ellipsis;
```

案例一解析：因为文字一行显示，所以难免会溢出，所以就需要`overflow: hidden`控制溢出不显示，text-overflow属性仅是注解，当文本溢出时是否显示省略标记。并不具备其它的样式属性定义。要实现溢出时产生省略号的效果还须定义：强制文本在一行内显示（white-space:nowrap）及溢出内容为隐藏（overflow:hidden），只有这样才能实现溢出文本显示省略号的效果。

案例二：横向滚动

css横向滚动三要素：
1、父元素设置横向滚动`overflow-x: scroll`或者`overflow-x: auto`;
2、父元素不换行`white-space: nowrap`，这个非常关键，否则item的元素会换行，宽度不受控制;
3、子元素设置为行内块级元素`display: inline-block`或者`display: flex`;

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0,maximum-scale=1.0, user-scalable=no"/>
    <title>Title</title>
    <style>
        *{
            padding: 0;
            margin: 0;
        }
        ul{
            display: flex;
            overflow-x: auto;
            list-style: none;
            white-space: nowrap;
        }
        li{
            display: flex;
            margin: 0 10px;
        }
        li span{
            width: 50px;
            height: 50px;
            background-color: #24c789;
            clip-path: circle();
        }
        li div{

        }
    </style>
</head>
<body>
    <ul>
        <li>
            <span></span>
            <div>
                <p>的冲锋号后的覅和IC v和的后果</p>
                <p>奋斗和</p>
            </div>
        </li>
        <li>
            <span></span>
            <div>
                <p>的冲锋号后的覅和IC v和的后果</p>
                <p>奋斗和</p>
            </div>
        </li>
        <li>
            <span></span>
            <div>
                <p>的冲锋号后的覅和IC v和的后果</p>
                <p>奋斗和</p>
            </div>
        </li>
        <li>
            <span></span>
            <div>
                <p>的冲锋号后的覅和IC v和的后果</p>
                <p>奋斗和</p>
            </div>
        </li> <li>
        <span></span>
        <div>
            <p>的冲锋号后的覅和IC v和的后果</p>
            <p>奋斗和</p>
        </div>
    </li>

    </ul>
</body>
</html>

```

