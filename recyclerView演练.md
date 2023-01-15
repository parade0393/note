### DataBinding(viewBinding)

1. 必备条件

   1. module下的gradle配置文件增加kotlin-kapt插件

   2. 启用绑定

      ```groovy
      buildFeatures {
          viewBinding  true
      }//二者根需求选迁移  启用dataBinding包括viewBinding
      buildFeatures {
          dataBinding  true
      }
      ```

   3. 布局文件转成DataBinding需要的(使用viewBinding不需要这一步)

   4. Activity使用

      ```kotlin
      class MainActivity : AppCompatActivity() {
          private lateinit var mBinding:ActivityMainBinding
          override fun onCreate(savedInstanceState: Bundle?) {
              super.onCreate(savedInstanceState)
              mBinding = ActivityMainBinding.inflate(layoutInflater)
              setContentView(mBinding.root)
          }
      }
      ```

   5. RecyclerView的adapter使用-需要把Binding类传到ViewHolder

      ```kotlin
      override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): RecyclerView.ViewHolder {
         return TitleViewHolder(ItemDemoDataHeaderBinding.inflate(LayoutInflater.from(parent.context),parent,false))
      }
      
      inner class TitleViewHolder(binding: ItemDemoDataHeaderBinding):RecyclerView.ViewHolder(binding.root){
          fun bind(item:DemoDataTitle){
          }
      }
      ```

### 密封类

### 属性访问器

1. 简单使用

   ```kotlin
   var mData: List<DemoData> = emptyList()
       set(value) {
           field = value
           notifyDataSetChanged()
       }
   ```

### Lambda

 1. 设置监听器

    ```
    var onItemClickListener:((view:View,position:Int) -> Unit)? = null
    it.onItemClickListener =  { _,pos ->
     //写代码
    }
    ```

    