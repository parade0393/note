1. adb logcat  > d:\log.txt  将所有日志都输出到d盘的log.log文件中
2. adb logcat *:E 只打印Error级别的日志
3. adb shell ps | grep 包名  只看包名的进程信息 一般第二个就是进程id 
4. adb shell getprop | grep heap 查看设备的内存分配
5. adb shell 进入 linux命令行  然后wm size 查看分辨率  wm density查看dpi

