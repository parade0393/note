1. 安装 `npm i @tinymce/tinymce-vue tinymce`--如果有购买`tinymce`服务的话只需要安装`tinymce-vue`就可以了

2. 导入依赖并引用组件

   ```vue
   <template>
     <div class="guideline-detail">
       <Editor  v-model="content" :init="init" :disabled="disabled"/>
     </div>
   </template>
   <script setup>
   import tinymce from "tinymce/tinymce"; //tinymce默认hidden，不引入不显示
   import Editor from "@tinymce/tinymce-vue"; // 编辑器引入
   </script>
   ```

3. 发现控制台报错，还需导入以下依赖

   ```vue
   <script setup>
   import tinymce from "tinymce/tinymce"; //tinymce默认hidden，不引入不显示
   import Editor from "@tinymce/tinymce-vue"; // 编辑器引入
   import "tinymce/themes/silver/theme"; // 编辑器主题
   import "tinymce/icons/default"; // 引入编辑器图标icon，不引入则不显示对应图标
   import "tinymce/models/dom";
   </script>
   ```

4. 此时组件时英文的，还需下载语言包并引入，[下载地址](https://www.tiny.cloud/get-tiny/language-packages/)，在public下新建tinymce文件夹，把下载好的语言包放到这,配置如下：至此`tinymce`已经可以使用了

   ```javascript
   const init = reactive({
     language_url: "/tinymce/langs/zh-Hans.js",
     language: "zh-Hans",//这里具体的设置需要根据下载的语言包里设置
     height: 300,
     branding: false, //隐藏右下角技术支持
     menubar: true, //
   });
   ```

5. 下面是详细配置，简单封装了组件，paste插件6以上版本没有了，换成了收费的`PowerPaste`，[版本差异点击官方地址](https://www.tiny.cloud/docs/tinymce/6/migration-from-5x/)，其实从5升级到6好多插件都变成收费的了

   ```vue
   <template>
     <div>
       <Editor v-model="content" :init="init" :disabled="disabled" />
     </div>
   </template>
   
   <script setup>
   import tinymce from "tinymce/tinymce"; //tinymce默认hidden，不引入不显示
   import Editor from "@tinymce/tinymce-vue"; // 编辑器引入
   import "tinymce/themes/silver/theme"; // 编辑器主题
   import "tinymce/icons/default"; // 引入编辑器图标icon，不引入则不显示对应图标
   import "tinymce/plugins/image"; // 插入上传图片插件
   import "tinymce/plugins/importcss"; //图片工具
   import "tinymce/plugins/media"; // 插入视频插件
   import "tinymce/plugins/table"; // 插入表格插件
   import "tinymce/plugins/lists"; // 列表插件
   import "tinymce/plugins/charmap"; // 特殊字符
   import "tinymce/plugins/wordcount"; // 字数统计插件
   import "tinymce/plugins/code"; // 查看源码
   import "tinymce/plugins/fullscreen"; //全屏
   import "tinymce/plugins/link"; //
   import "tinymce/plugins/preview"; // 预览
   import "tinymce/plugins/save"; // 保存
   import "tinymce/plugins/searchreplace"; //查询替换
   import "tinymce/plugins/pagebreak"; //分页
   import "tinymce/plugins/insertdatetime"; //时间插入
   import "tinymce/plugins/quickbars"; //时间插入
   import "tinymce/plugins/paste";
   import { ref, reactive, computed } from "vue";
   const props = defineProps({
     modelValue: {
       type: String,
       default: "",
     },
     disabled: {
       type: Boolean,
       default: false,
     },
   });
   const emits = defineEmits(["update:modelValue"]);
   const content = computed({
     get: () => props.modelValue,
     set: (val) => {
       emits("update:modelValue", val);
     },
   });
   
   const init = reactive({
     language_url: "/tinymce/langs/zh-Hans.js",
     language: "zh-Hans",
     min_height: 400,
     branding: false, //隐藏右下角技术支持
     menubar: false, //菜单栏，默认显示
     auto_focus: true, //自动获取焦点
     placeholder: "请输入内容", //
     //工具栏 最新样式用styles表示，旧版本可能是formatselect或者styleselect
     toolbar:
       "undo redo | fullscreen preview  | styles fontsize  alignleft aligncenter alignright alignjustify | link unlink | numlist bullist | image  table | fontsizeselect forecolor backcolor | indent outdent | removeformat | code |",
   
     plugins: "link image table lists fullscreen  code preview wordcount",
     quickbars_selection_toolbar: false, //执行选择后出现的工具栏
     quickbars_insert_toolbar: false, //执行插入操作出现的工具栏
     fontsize_formats: "12px 13px 14px 15px 16px 18px",
     lineheight_formats: "1 1.2 1.5 1.6 1.8 2 2.4", //行高列表
     default_link_target: "_blank",
     images_upload_url: "your_upload_api_url...",
     link_title: false,
     nonbreaking_force_tab: true,
     content_style: "body { font-size:16px }",
     file_picker_callback: function (callback, value, meta) {},
   });
   </script>
   
   <style lang="less" scoped></style>
   
   ```

   

