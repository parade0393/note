本片主要讲在正则表达式中使用`()`和`[]`的区别，关于`()`的捕获不做深入探讨

### 一、概念

1. `()`代表分组,`()`匹配到的内容会分为一组，内容需要和`()`整体配才会匹配成功
2. `[]`代表字符集，内容匹配到`[]`里的任意一条规则，即为匹配成功

### 二、Demo

```javascript
<script>
   let str = "abc"
   console.log(/(ad)/.test(str))//false/(ad)/正则表示ad作为一个整体去匹配字符串
   console.log(/[ad]/.test(str))//true /[ad]/表示ad作符字符集去匹配字符串(匹配到a匹配到d都算匹配成功)
</script>
```

```javascript
<script>
    let strA = "abc"
    console.log(/(a|d)/.test(strA))//true `()`分组符号配合`|`选择符号配合可以达到和`[]`类似的效果 --`(a|d)`中的`|`左边是一个整体，右边是一个整体，如果字符串匹配到`|`左边或者右边任意一个整体即为匹配成功
    console.log(/[ad]/.test(strA))//true
</script>
```

```javascript
<script>
let strB = "abc"
console.log(/(.)/.test(strB))//true
console.log(/[.]/.test(strB))//false
/**
 * 正则里有一些字符是元字符，这些元字符有着特殊的含义,例如`.`和`?`
 * 在()里这些元字符含义保持不变，但是在[]里这些元字符就是普通的字符没有特殊含义
 */
</script>
```

### 三、案例

1. 获取url获取参数

   ```javascript
   <script>
       function getParamByUrl(url,name) {
           let reg = new RegExp(`([?&])${name}=[a-zA-Z0-9]*(&)?`)
           let match = url.match(reg);
           if (match){
              return match[0].split("=")[1].replaceAll("&","")
           }else{
               return null
           }
       }
       let url = "https://m.weibo.cn/u/5902368392?topnav=1&wvr=6&is_all=1&jumpfrom=weibocom"
       console.log(getParamByUrl(url,"wvr"))//6
   </script>
   ```

四、更多

关于正则更多可以参考[正则表达式对比(Java和JavaScript)](https://blog.csdn.net/parade0393/article/details/124087201)