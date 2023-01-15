在开发中我们通常在表单元素上使用v-model来实现数据的双向绑定。其实v-model只是语法糖，是v-bind和v-on的缩写

官方解释：

```vue
// 以下两种是等价的
<input v-model="searchText">
<input
  v-bind:value="searchText"
  v-on:input="searchText = $event.target.value"
>
```

基于以上的理论只是我们来展开说明Vue和和Vue3在组件上使用`v-model`的区别

#### Vue2

1. 写法一

   ```vue
   <template>
     <div>
       <child v-model="test" />
       <p>父组件:{{ test }}</p>
       <button @click="fn1">父组件</button>
     </div>
   </template>
   
   <script>
   //父组件
   import child from './child.vue'
   export default {
     components: { child },
     name: 'Home',
     data() {
       return {
         test: ''
       }
     },
     methods: {
       fn1() {
         this.test += 1
       }
     }
   }
   </script>
   //------------------------------------------------------
   <template>
     <div>
       <p>子组件普通元素的值{{ msg }}</p>
       <button @click="fn">子组件普通元素的按钮</button>
     </div>
   </template>
   
   <script>
   export default {
       //子组件
     // props是对象的时候，里面的属性值不能用基本类型了
     /**
      * model 表示了v-model使用的属性和事件名称
      * 一个组件上的v-model默认利用名为value的prop和名为input的事件
      */
     model: {
       prop: 'msg', // v-bind 的属性
       event: 'cc' // v-on的属性
     },
     props: {
       msg: {}
     },
     methods: {
       fn() {
         this.$emit('cc', this.msg + 2)
       }
     }
   }
   </script>
   
   <style></style>
   
   
   ```

2.  写法二：

   ```vue
   <template>
     <div>
       <child-input v-model="content" />
       <p>父组件input的值:{{ content }}</p>
     </div>
   </template>
   
   <script>
    //父组件
   import childInput from './childInput.vue'
   export default {
     components: { childInput },
     name: 'Home',
     data() {
       return {
         content: ''
       }
     }
   }
   </script>
   //-----------
   <template>
     <div>
       <input v-model="editContent" />
     </div>
   </template>
   
   <script>
   export default {
    //子组件
     props: {
       // v-mode默认时使用名称为value的prop
       value: String
     },
     data() {
       return {
         content: ''
       }
     },
     computed: {
       editContent: {
         get() {
           return this.value
         },
         set(newVal) {
           this.$emit('input', newVal)//必须使用input发送数据，父组件的v-model才会接收到。默认的是input
         }
       }
     }
   }
   </script>
   
   <style></style>
   
   
   ```

   #### vue3

   vue3和vue2是不兼容的：vue3的语法糖原里是:自定义组件上的 `v-model` 相当于传递了 `modelValue` prop 并接收抛出的 `update:modelValue` 事件：

   写法：

   ```vue
   <template>
     <div class="home">
       <child v-model="pContent" />
       <p>父组件的值{{ pContent }}</p>
     </div>
   </template>
   
   <script setup>
   import { ref } from 'vue'
   import child from './child.vue'
   const pContent = ref('')
   </script>
   //-----------------------------------
   <template>
     <div>
       <input v-model="value" />
     </div>
   </template>
   
   <script setup>
   //其实defineProps和 defineEmits 是不需要导的，eslint会检查就写上了
   import { defineProps, defineEmits, computed } from 'vue'
   const props = defineProps(['modelValue'])
   const emits = defineEmits(['update:modelValue'])
   const value = computed({
     get: () => props.modelValue,
     set: (val) => {
       emits('update:modelValue', val)
     }
   })
   </script>
   
   <style lang="less" scoped></style>
   
   ```

具体差别详见官方地址: [v-model](https://v3.cn.vuejs.org/guide/migration/v-model.html#%E6%A6%82%E8%A7%88)