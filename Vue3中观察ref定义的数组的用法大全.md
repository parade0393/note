数组是JavaScript常用的一种数据格式，在Vue3中使用数组作为响应式数据，应该有两种方式，一种是用`ref`包裹，一种是用`reactive`，一般来说，用`ref`定义基本数据，用`reactive`数据，之所以大家都喜欢用`ref`定义数组，可能是改变(清空或者重新赋值)数组方便,直接`ref.value=[]`,

这就造成了用`watch`监听`ref`定义的数组有不同的写法

首先数组的变化分两种:one:数组本身的变化(数组长度的变化):two:数组元素的变化

### 先来说数组本身的变化

1. 数组被替换`emptyArray.value = []`，常规写法，这样可以检测到变化

   ```js
   watch(emptyArray, () => {//这里使用emptyArray.value是监测不到被替换的
     console.log('空数组长度变化了')
   })
   ```

2. 使用数组的变更方法(`emptyArray.value.push(1)`)，

   ```
   watch(emptyArray, () => {//必须使用deep才能检测到使用数组变化变更方法的改变，但是这些改变可以引起UI的改变
     console.log('空数组长度变化了')
   },{
    deep:true
   })
   ```

### 数组元素的变化

1. 创建时已经初始化了元素数据的数组--这种方式对于数组里是复杂数据也可以

   ```javascript
   import { ref, watch } from 'vue'
   const emptyArray = ref([1, 2, 3, 4])
   watch(emptyArray.value, () => {//这个时候通过直接修改和利用数组的方法修改都可以监测到
     console.log('数组变化了')
   })
   const pushEmptyArray = () => {
     emptyArray.value.splice(0, 0, 19)
   }
   const changeEmptyArrayItem = () => {
     emptyArray.value[0] = 10
   }
   ```

2. 先创建后初始化--这种再用第一种方式已经检测不到数据的变化了，但是UI可以更新。这个时候必须用不带value的deep监听

   ```javascript
   <script setup>
   import { ref, watch, onMounted } from 'vue'
   const emptyArray = ref([])
   watch(//这个时候不能用.value且必须是深度监听，这种写法不仅可以监听数组本身的变化，也可以监听 数组元素的变化
     emptyArray,
     () => {
       console.log('空数组变化了')
     },
     {
       deep: true
     }
   )
   const pushEmptyArray = () => {
     emptyArray.value.splice(0, 0, { value: 12 })
   }
   const changeEmptyArrayItem = () => {
     emptyArray.value[0] = { value: 32 }
   }
   onMounted(() => {
     emptyArray.value = [{ value: 5 }, { value: 2 }, { value: 3 }, { value: 4 }]
   })
   </script
   ```

   Vue官方也给出了解释[侦听数组](https://v3.cn.vuejs.org/guide/migration/watch.html#frontmatter-title)

   ### 结论

   由于工作中数据大多数时候创建的时候都不会初始化，加上官方的说明，所以基本都使用最后一种方式监听