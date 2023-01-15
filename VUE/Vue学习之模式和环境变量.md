开发过程中的不同阶段往往会使用不同的环境，例如测试环境和正式环境，不同环境使用的一些变量往往不同(比如api)，环境切换的时候就需要不同的配置参数，我们可以用环境变量和模式来解决这个问题

1. 环境变量

   * 可以在项目的根目录中放置**环境文件**来指定环境变量--[官方链接](https://cli.vuejs.org/zh/guide/mode-and-env.html#%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F)

     ```
     .env                # 在所有的环境中被载入
     .env.local          # 在所有的环境中被载入，但会被 git 忽略
     .env.[mode]         # 只在指定的模式中被载入
     .env.[mode].local   # 只在指定的模式中被载入，但会被 git 忽略
     ```

     环境文件是通过`vue-cli-service`命令载入的，因此环境文件发生变化，需要重启服务；

   * 环境变量的使用

     * 自定义的变量必须以VUE_APP_开头
     * 在代码中使用`console.log(process.env.VUE_APP_SECRET)`
     * 在html中以[HTML插值](https://cli.vuejs.org/zh/guide/html-and-static-assets.html#%E6%8F%92%E5%80%BC)中介绍的方式使用

2. 模式

   默认情况下，一个 Vue CLI 项目有三个模式：`development`(开发)、`production`(生产)、`test`(测试)

   `vue-cli-service serve` 命令默认使用`development`模式

   `vue-cli-service build`和`vue-cli-service test:e2e`默认使用`production`模式

   可以通过`--mode`选项参数为命令行覆写默认的模式，例如，如果你想要在构建命令中使用开发环境变量：

   `vue-cli-service build --mode development`

   当运行``vue-cli-service`命令时，所有的环境变量都从上述的环境文件中载入，如果文件内部不包含 `NODE_ENV` 变量，它的值将取决于模式，例如，在 `production` 模式下被设置为 `"production"`，在 `test` 模式下被设置为 `"test"`，默认则是 `"development"`，针对不同的`NODE_ENV`变量，Vue CLI会创建不同的webpack 配置

   

   