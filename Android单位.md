一直以来对Android的单位和适配都比较模糊：今天来浅浅的研究一下

#### 基本概念

1. 屏幕尺寸
   1. 含义：手机对角线的物理尺寸
   2. 单位：英寸（inch），1英寸=2.54cm
2. 屏幕分辨率：
   * 含义：手机在横向、纵向上的像素点数总和,一般描述成屏幕的"宽x高”=AxB
   * 单位：px（pixel），1px=1像素点
   * UI设计师的设计图会以px作为统一的计量单位
     Android手机常见的分辨率：320x480、480x800、720x1280、1080x1920、2560x1440
3. 术语：
   * PPI--Pixels per inch每英寸包含的像素的个数
   * DPI--dots per inch 每英寸包含的点的个数。dpi和ppi虽然概念不同，但是计算方式相同，只是应用领域不同。Android使用dpi来描述屏幕的密度，但是最终识别的单位是px。Android会将设备的dpi写入系统，并可以修改
   * dp(dip)--density-independent-pixels 密度无关像素,它是一个虚拟出来的像素单位，1 dp 约等于中密度屏幕（160dpi；“基准”密度）上的 1 像素，
   * 屏幕密度：density-对应于DisplayMetrics类中属性density的值，density=dpi/160。我们可以这样理解密度：每dp里有多少个像素点(px)。。。简单的适配原里--假设设计图是以720x1280 dpi：320为基准，某控件标注宽为500px，那么我们在xml文件中设置该控件的宽时，应设置为：500/（320/160） = 250dp
   * adb shell 进入 linux命令行  然后wm size 查看分辨率  wm density查看dpi
4. 参考文章
   * [Android 屏幕分辨率适配](https://juejin.cn/post/7015597280120176676)
   * [Android密度无关像素(dp)与屏幕适配](https://blog.liaoheng.me/2021/07/14/android-dp-and-screen-adaptation/)
   * [推荐Android两种屏幕适配方案](https://juejin.cn/post/6844903612926296072)

