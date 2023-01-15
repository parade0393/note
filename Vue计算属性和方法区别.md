`Vue`的计算属性和方法的区别大家都很清除了，计算属性由缓存并且**是基于它们的响应依赖关系缓存**，只有依赖的响应式关系发生变化时才会重新执行函数(重新渲染模板时如果依赖关系没有变化则立即返回结果，不会重新执行函数)，demo见证如下：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0,maximum-scale=1.0, user-scalable=no"/>
    <title>Title</title>
    <script type="text/javascript" src="./js/vue.js"></script>
</head>
<body>
<div id="root">
    <h1>Data中的属性{{num}}</h1>
    <h1>计算属性{{cName}}</h1>
    <h1>方法调用:{{mName()}}</h1>
    <h1>引起变化的数字:{{num}}</h1>
</div>
<script>
    Vue.config.productionTip = false
    let data = {
        name:"parade",
        num:0,
        timer:null
    }
    let vue = new Vue({
        el:"#root",
        data,
        computed:{
            cName(){
                console.log("计算属性")
                return this.name.slice(0,2)
            }
        },
        methods:{
            mName(){
                console.log("方法调用")
                return this.name.slice(0,2)
            }
        },
        watch:{
            num(val){
                if (val >=10){
                    clearInterval(this.timer)
                }
            }
        },
        mounted(){
            this.timer = setInterval(()=>{
                this.num++//num每变化一次都会重新渲染模板，mName方法也会重新执行，而计算属性cName只会执行一次
            },500)
        }
    })
</script>
</body>
</html>

```

