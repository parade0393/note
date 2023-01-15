我是一名不正经的Android开发，由于工作原因兼职了前端的工作，开始喜欢上了前端，不仅因为前端够灵活、够随意，够洒脱，而且有很多非常输出的UI库供我们选择。使得我们在很多时候只需关注业务逻辑，但有的时候UI库没有现成的组件，就需要我们自己封装了。

### 功能需求

在使用`Vant`时，需要一个类似`DropdownMenu`的下拉弹出层，但是不需要其`menu`，简单来说就是点击标题栏上的某一个图标，自上而下有一个弹出层，最终弹出层显示在标题栏的正下方。使用`DropdownMenu`和`Popup`均不能满足需求，如果`Popup`可以设置偏移量那就完美了。

### 需求分析

结合`Popup`和组件`tab` 的`offset-top`属性来封装一个组件，组件具备的基本功能如下:

1. v-model控制显示和关闭
2. 是否显示遮罩层
3. 点击遮罩层是否可以关闭
4. 动态设置到顶部的偏移量
5. 动态设置显示的层级(`z-index`)

### 前置知识点

1. v-model的使用
2. computed的使用
3. 特殊组件`transition`的使用
4. 插槽的使用
5. css函数`v-bind`的使用

### 代码实现

```vue
<template>
  <transition name="dropdown-fade">
    <div
      v-if="overlay"
      v-show="isShow"
      @click="dissmissFilter"
      class="dropdown-overlay dropdown-filter"
      :class="isShow ? 'showMove' : ''"
    ></div>
  </transition>

  <transition name="dropdown-popup-slide-top">
    <div
      v-show="isShow"
      :class="isShow ? 'showMove' : ''"
      class="dropdown-container dropdown-filter"
    >
      <slot></slot>
    </div>
  </transition>
</template>

<script setup>
import { computed, ref } from 'vue'
const props = defineProps({
  show: {
    type: Boolean,
    default: false
  },
  offsetTop: {
    type: [String, Number],
    default: 0
  },
  overlay: {
    type: Boolean,
    default: true
  },
  closeOnClickOverlay: {
    type: Boolean,
    default: true
  },
  zIndex: {
    type: [String, Number],
    default: 2
  }
})
const emits = defineEmits(['update:show'])
const topDistance = computed(() => {
  if (typeof props.offsetTop === 'string') {
    return props.offsetTop
  } else {
    return `${props.offsetTop}px`
  }
})
const filterIndex = computed(() => {
  if (typeof props.zIndex == 'string') {
    return props.zIndex * 1
  } else {
    return props.zIndex
  }
})
const isShow = computed({
  get: () => props.show,
  set: (value) => {
    emits('update:show', value)
  }
})
const dissmissFilter = () => {
  if (props.closeOnClickOverlay) {
    isShow.value = false
  }
}
</script>

<style lang="less" scoped>
.dropdown-filter {
  position: fixed;
  top: v-bind(topDistance);

  left: 0;
  right: 0;
  z-index: v-bind(filterIndex);
}
.dropdown-overlay {
  background-color: rgba(0, 0, 0, 0.7);
  bottom: 0;
}
.dropdown-container {
  background-color: #fff;
  -webkit-transform: translate(0, -100%);
  transform: translate(0, -100%);
  -webkit-transition: all ease-in-out 0.3s;
  transition: all ease-in-out 0.3s;
  opacity: 0;
}
.showMove {
  opacity: 1;
  -webkit-transform: translate(0, 0);
  transform: translate(0, 0);
}

@keyframes van-fade-in {
  from {
    opacity: 0;
  }

  to {
    opacity: 1;
  }
}

@keyframes van-fade-out {
  from {
    opacity: 1;
  }

  to {
    opacity: 0;
  }
}
.dropdown-fade {
  &-enter-active {
    animation: 0.3s van-fade-in both ease-out;
  }
  &-leave-active {
    animation: 0.3s van-fade-out both ease-in;
  }
}
.dropdown-popup {
  &-slide-top-enter-active {
    transition-timing-function: ease-out;
  }
  &-slide-top-leave-active {
    transition-timing-function: ease-in;
  }
  &-slide-top-enter-from,
  &-slide-top-leave-active {
    transform: translate3d(0, -100%, 0);
  }
}
</style>

```

​		其中css样式现在不是我的强项，但也算勉强实现了动画效果

### 组件使用

```vue
<template>
    <div>
        <TitleBar ref="navBar" title="提案工作" @clickRight="clickFilter">
        <drop-down-dialog  v-model:show="showFilter" :offset-top="offsetTop">
            下拉弹窗内容
        </drop-down-dialog>
    </div>
</template>

<script setup>
import TitleBar from '@/components/title-bar'
import DropDownDialog from '@/components/DropDownDialog.vue'
import { useRect } from '@vant/use'
import {ref,onMounted} from "vue"
const showFilter = ref(false)
const navBar = ref()
const offsetTop = ref(0)
const clickFilter = () => {
    showFilter.value = true
}
onMounted(()=>{
    // navBar.value.$el获取组件的dom元素
    const rect = useRect(navBar.value.$el)
    offsetTop.value = rect.height
})
</script>

<style lang="less" scoped>

</style>
```

​		



