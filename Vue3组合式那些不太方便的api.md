Vue3的组合式api很香，但是也有一些不太方便的地方。下面给出一种解决方案，整理自codewhy老是的视频课。关于watch的原理还不太明白，后期补上

1. watch

   在vue3中观察一个响应式对象时，得到的新值和旧值是一样的，这在有时候就不方便。当然也有变通方法，如下：

```javascript
const objWatch = reactive({ name: 'parade', child: { age: 23 } })//响应式对象
const changeReactive = () => {
  objWatch.child.age++
}//改变响应式对象
//观察
watch(
  () => {
    return JSON.stringify(objWatch)
  },
  (newValue, oldValue) => {
    console.log(JSON.parse(newValue), JSON.parse(oldValue))
  }
)
```

2. vuex中的map系列函数

   在vue3中组合式api中使用vuex的map系列函数时，是无法使用展开语法的。当然也有变通方法，如下经过封装过的使用方法:

   ```javascript
   //useMappers.js
   import { useStore, mapMutations, mapActions } from 'vuex'
   import { computed } from 'vue'
   export function useMapper(mapper, mapperFn) {
     const store = useStore()
     //获取到对应的对象的function，{name:function,age:function}
     const storeStateFns = mapperFn(mapper)
   
     const storeState = {}
     Object.keys(storeStateFns).forEach((fnkey) => {
       const fn = storeStateFns[fnkey].bind({ $store: store })
       if (mapMutations === mapperFn || mapActions === mapperFn) {
         storeState[fnkey] = fn//mapMutations和mapActions的处理不太一样，不需要使用计算属性重新计算
       } else {
         storeState[fnkey] = computed(fn)
       }
     })
   
     return storeState
   }
   //useGetters.js
   import { useMapper } from './useMapper'
   import { mapGetters, createNamespacedHelpers } from 'vuex'
   export function useGetters(mapper, moduleName) {//第二个参数为vuex的模块名称，没有的可以不传
     let mapperFn = mapGetters
     if (moduleName) {
       mapperFn = createNamespacedHelpers(moduleName).mapGetters
     }
     return useMapper(mapper, mapperFn)
   }
   //useMutations.js
   import { mapMutations, createNamespacedHelpers } from 'vuex'
   import { useMapper } from './useMapper'
   export function useMutations(mapper, moduleName) {
     let mapperFn = mapMutations
     if (moduleName) {
       mapperFn = createNamespacedHelpers(moduleName).mapMutations
     }
     return useMapper(mapper, mapperFn)
   }
   //index.js
   import { useGetters } from './useGetters'
   import { useState } from './useState'
   import { useMutations } from './useMutations'
   import { useActions } from './useActions'
   export { useGetters, useState, useMutations, useActions }
   
   //组件中使用
   <script setup>
   import { useState, useGetters, useMutations, useActions } from '@/hook'
   import { INCREMENT_N } from '@/store/mutation-type'
   const { incre, decre, increN, homeIncrement } = useMutations({
     incre: 'increment',
     decre: 'decrement',
     increN: INCREMENT_N,
     homeIncrement: 'home/homeIncrement'
   })
   const { incrementAction, ['home/homeActions']: homeActions } = useActions([
     'incrementAction',
     'home/homeActions'
   ])
   const { name, age } = useState(['name', 'age'])
   const { sName, sAge } = useState({
     sName: (state) => state.name,
     sAge: (state) => state.age,
     homeCount1: (state) => state.home.homeCount
   })
   const { homeCount } = useState(['homeCount'], 'home')
   const {
     nameInfo,
     ageInfo,
     ['home/homeDobule']: homeDobule
   } = useGetters(['nameInfo', 'ageInfo', 'home/homeDobule'])
   const { sNameInfo, sAgeInfo, homeGetters } = useGetters({
     sNameInfo: 'nameInfo',
     sAgeInfo: 'ageInfo',
     homeGetters: 'home/homeDobule'
   })
   </script>
   ```

   