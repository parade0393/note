Postman是日常接口测试的一个重要工具，有时候需要可能需要把请求的响应数据导出到本地文件。单纯使用postman客户端是不行的。应该是为postman内置了安全机制，不允许这样操作。但是可以通过newman来操作

newman是执行postman请求集合的一个命令行工具，也可以作为一个npm包，再配合其他npm包就可以把数据写入excel文件了

1. 首先把api集合导出json文件
2. 初始化一个node项目，把导出的api的json文件放入项目根目录
3. 安装newman和exceljs`npm install newman exceljs` excel是处理excel的js库
4. newman主要使用run方法和监听一些事件，run方法的参数和可以监听的事件可以参考官方文档`https://github.com/postmanlabs/newman#newmanrunevents`
5. exceljs文档`https://github.com/exceljs/exceljs/blob/master/README_zh.md`
6. 接口使用了wandroid的开放api，感谢`https://www.wanandroid.com/index`
7. 源代码`https://github.com/parade0393/newman.git`
8. 使用截图

