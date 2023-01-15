### WebPack

WebPack+Vue3

其中`~`和`@`的使用参考[官方文档](https://cli.vuejs.org/zh/guide/html-and-static-assets.html#%E4%BB%8E%E7%9B%B8%E5%AF%B9%E8%B7%AF%E5%BE%84%E5%AF%BC%E5%85%A5)和[Vue3.0在template、script、style中引用图片资源的方式](https://blog.csdn.net/Liu_yunzhao/article/details/102706776)

```vue
<template>
  <div>
    <img class="wh" src="~@/assets/logo.png" alt="">
    <p class="wh ig"></p>
    <p :style="styleObject"></p>
    <p :style="styleO"></p>
    <p :style="styleIm"></p>
  </div>
</template>

<script setup>
import logo from '@/assets/logo.png'
const styleObject = {
  marginLeft: '13px',
  width: '100px',
  height: '100px',
  backgroundImage: 'url(' + require('@/assets/logo.png') + ')',
  backgroundSize: '100% 100%'
}
const styleO = {
  marginLeft: '13px',
  width: '100px',
  height: '100px',
  backgroundImage: `url(${require('@/assets/logo.png')})`,
  backgroundSize: '100% 100%'
}

const styleIm = {
  marginLeft: '13px',
  width: '100px',
  height: '100px',
  backgroundImage: `url(${logo})`,
  backgroundSize: '100% 100%',
  backgroundColor: 'red'
}
</script>

<style lang="scss">
  .wh{
    width: 100px;
    height: 100px;
  }
  .mr13{
    margin-right: 13px;
  }
  .ig{
    background-image: url("~@/assets/logo.png");
    background-size: 100% 100%;
  }
</style>

```

### Vite

**在vite中不能使用require**，并且需要显示设备`@`别名

```vue
<template>
  <div class="about">
    <img class="wh" src="@/assets/images/1.png" alt="" />
    <p class="wh mr13" :style="styleObject"></p>
    <p class="wh ig"></p>
    <p class="wh mr13" :style="styleO"></p>
  </div>
</template>

<script setup>
import pp from "@/assets/images/1.png";//import 必须放入顶部
const styleObject = {
  marginLeft: "13px",
  backgroundImage: `url(${pp})`,
  backgroundSize: "100% 100%"
};
const styleO = {
  marginLeft: "13px",
  backgroundSize: "100% 100%",
  backgroundImage: new URL('@/assets/images/1.png',import.meta.url).href
}
</script>

<style scoped>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}
.wh {
  width: 100px;
  height: 100px;
}
.mr13{
  margin-right: 13px;
}
.ig{
  background-image: url("@/assets/images/1.png");
  background-size: 100% 100%;
}
</style>

//vite.config.js
import { fileURLToPath, URL } from 'url'

import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue()],
  resolve: {
    alias: {
      '@': fileURLToPath(new URL('./src', import.meta.url))
    }
  }
})

```

