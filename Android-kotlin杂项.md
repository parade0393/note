### Databinding

1. 使用步骤：

   1. 添加`kotlin-kapt`插件

   2. 在module的gradle配置增加

      ```groovy
      buildFeatures {
          dataBinding true
      }
      ```

   3. 转为布局文件为layout布局

   完成以上步骤即可生成binding类

### ConstrainLayout

