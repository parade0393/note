### 功能需求

项目中经常遇到这样一种需求:有一个表单页面，里面有大量的字段，有些表单项需要跳转新页面(比如选择联系人等)，这个时候就需要缓存表单页面的数据，从而在选择完联系人返回表单页面，之前填写的数据不会丢失。

### 功能分析

实现这样的需求，第一时间就想到了Vue的内置组件`keep-alive`，但是仅仅简单的使用`keep-alive`会带来一个问题:每次进入表单页面希望重新提交数据的时候，都会显示上次的数据。而实际的需求是每次进入表单都是一个新页面，这就要求提交完成后要清除缓存的数据。在使用Vue2的时候，在官方的issue区找到了一种解决方法[[希望keep-alive能增加可以动态删除已缓存组件的功能](https://github.com/vuejs/vue/issues/6509#)](https://github.com/vuejs/vue/issues/6509#issuecomment-403693256)，但是解决方案是非官方的，有兴趣的小伙按可以了解一下。不幸的是这种解决方案在Vue3不能使用了，因为`#destroy`被废弃了。下面说一下我使用Vue3时的一种思路

### 前置条件

1. key属性:在列表中的item使用**唯一的key**，可以最小化的重新渲染dom，当然key不同，肯定会触发替换，[参见官方文档](https://v3.cn.vuejs.org/api/special-attributes.html#key)
2. is属性
   1. 当使用非单文件组件时，可以把原生html元素渲染成Vue组件
   2. 在单文件组件使用，通常用在动态组件`Component`上
3. router-view:`<router-view>` 暴露了一个 `v-slot` API，主要使用 `<transition>` 和 `<keep-alive>` 组件来包裹你的路由组件。

### 代码编写

1. 在App.vue中使用`keep-alive`

   ```vue
   <router-view v-slot="{ Component, route }">
       <keep-alive :max="5">
         <component
           :is="Component"
           :key="route.params.keepKey ? route.params.keepKey : route.path"
           v-if="$route.meta.keepAlive"
           v-wechat-title="$route.meta.title"
         />
       </keep-alive>
       <component
         :is="Component"
         :key="$route.name"
         v-if="!$route.meta.keepAlive"
         v-wechat-title="$route.meta.title"
       />
     </router-view>
   ```

   这里和Vue2的写法不太一样，Vue2是用`keep-alive`b包裹`router-view`，通过在路由表的meta中添加`keepAlive`标适确定哪个组件使用缓存，通过动态的`key`来间接的到达清除缓存的目的

2. 进入表单页面的之前的代码:

   ```javascript
   const goSave = () => {
     router.push({
       name: 'proposal-save',
       params: {
         keepKey: new Date().getTime()
       }
     })
   }
   ```

   每次进入表单页面都会使用新的key，来保证每次进入表单页面都会替换到原来的缓存

3. 表单页面进入选择联系人页面:

   ```javascript
   const goContact = () => {
     router.push({
       name: 'proposal-contact',
       params: {
         keepKey: route.params.keepKey,
         ids
       }
     })
   }
   ```

   从表单进入页面时携带者从上个页面带过来的动态key

4. 通讯录页面选择完成返回表单页面的代码

   ```javascript
   onBeforeRouteLeave((to, from, next) => {
     if (to.name == 'proposal-save') {
       to.params.keepKey = route.params.keepKey
       to.params.selectedData = selectedData.value
     } else {
       to.params.keepKey = undefined
     }
     next()
   })
   ```

   在局部路由导航守卫里，根据条件判断把表单页面带过来的动态key，再回传过去，这样返回的时候由于组件相同(key也相同)会保留之前的数据，从而来满足开篇提到的需求

   ### 总结

   这只能说是一种解决问题的思路，但应该不是最优雅最好的，如果有大佬有更好的解决方案，还希望可以分享出来