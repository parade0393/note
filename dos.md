1. echo---用来将字符输出到屏幕或者文本中，也用来控制是否显示命令自身的开关，执行了echo off命令之后 只显示执行的结果，不显示指令![](https://img-blog.csdnimg.cn/2020092318011576.png)

   ![](https://img-blog.csdnimg.cn/2020092318014918.png)

   

2. @：命令行回显屏蔽符个字符在批处理中的意思是关闭当前行的回显

   ![](https://img-blog.csdnimg.cn/20200923180238619.png) ![](https://img-blog.csdnimg.cn/20200923180307246.png)

3. 通常脚本第一行会这样写@echo off，会屏蔽所有命令回显

   ![](https://img-blog.csdnimg.cn/20200923180352655.png)

4. %%来包括系统变量或者自己的变量  %cd%当前的目录

   ![](https://img-blog.csdnimg.cn/20200923180422445.png)

5. set time1=%time%,其中time是系统变量 ，利用echo %time1%输出时间，无论合适都是一个  而利用echo %time%输出的时间是即时时间，每次都不一样

   ![](https://img-blog.csdnimg.cn/20200923180525250.png)

6. pause暂停命令，调试dos命令的时候，有时候会闪退，可用pause命令阻止闪退查看错误信息

7. 压缩文件 可在系统环境变量的path中设置压缩软件的path

8. rem和:: 当echo处于打开时使用rem注释的语言会被打印出来  而::注释的则不会
