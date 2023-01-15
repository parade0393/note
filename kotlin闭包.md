1. ### 什么是闭包

   一段程序代码通常由变量，常量，表达式组成，然后使用一堆"{}"来闭合，并包裹这些代码，由这对花括号包裹着的代码就是闭包。函数，Lambda，if语句，when语句都可以称之为闭包，但通常情况下我们所说的闭包就是Lambda

   简单的闭包

   ```kotlin
   fun main() {
       test//yes
   }
   
   val test = if (5 > 3) {
       println("yes")
   } else {
       println("no")
   }
   ```

2. ### 为什么会设计闭包这种结构

   变量的作用域无非就是两种，全局变量和局部变量，使用变量时会从当前作用域向上查找变量，所以函数内部可以直接读取全局变量，而再函数外部自然就无法读取函数内部的局部变量，那么如何读取局部的变量呢？答案就是**闭包**，这里我们给闭包下一个定义，闭包就是可以读取函数内部变量的函数(参考下面示例)

3. ### 普通函数和闭包函数

   普通函数没有状态，调用完毕后里面的内容就会被释放掉

   ```kotlin
   fun main() {
       test()//3
       test()//3
       test()//3
   }
   
   fun test() {
       var a = 3
       a++;
       println(a)
   }
   ```

   闭包可以让函数携带状态，**闭包捕获的变量可以脱离原始作用域而存在**

   ```kotlin
   fun main() {
       val t = test()//t是个函数Function0，准确的来说是个 函数类型的对象 类似于js的Object和Function
       t()//4 调用t.invoke方法
       t()//5
       t()//6
   }
   
   fun test():()->Unit{//test()函数的返回值是类型为()->Unit的函数类型
      var a = 3//状态
      return fun (){
         a++
         println(a)
      }
   }
   //总结：函数里面声明函数，函数返回值是函数，就是闭包
   //1.函数内部的局部变量的状态保存了，变量的值就是状态
   //2.a被隐藏起来了，但我们可以调用函数改变状态，或者获得a的值。这点就是函数的面向对象，让函数具有封装的能力，让函数具有了状态
   ```

4. ### 自执行闭包

   ```kotlin
   fun main() {
       {x:Int,y:Int->
           println(x+y)
       }(1,2)//3
   }
   ```

5. ### java于kotlin捕获局部变量的区别

   ```java
   public interface Clickable {
       void click();
   }
   ```

   ```java
   class TestJava {
       private void closure(Clickable clickable){
           clickable.click();
       }
   
       public  void main(ArrayList<String> args) {
           int count = 0;
           closure(()->{
   //            count+=1;//count必须设置为final才能访问，修改count只能把count声明为成员变量
           });
       }
   }
   ```

   ```kotlin
   class Test {
       private fun closure(clickable: Clickable){
           clickable.click()
       }
       fun main() {
           var count: Int = 0
          closure(Clickable {
              count+=1//编译正常
          })
           println(count)//1
       }
   }
   ```

6. ### 闭包捕获的变量可以脱离原始作用域而存在

   ```kotlin
   fun main() {
       //f的类型是(Int)->Int类型，是函数类型的对象，是lambda的引用,lambda的返回值是局部变量total，但是total的作用域是在高阶函数sum中
       //每次执行完f后，total变量的作用域就不存在了，可是total的变量值都能够被保持，由此说明被捕获的变量都存储在一个特殊的容器
       val f = sum()
       println(f(10))
       println(f(10))
       println(f(10))
   }
   
   fun sum():(Int)->Int{//声明高阶函数sum的返回值是(Int)->Int类型
       var total = 0//total是sum高阶函数的局部变量
       return {//使用lambda作为sum()高阶函数的返回值
           total = total+it
           total
       }
   }
   ```

   文章参考以下文章：

   1. [Kotlin函数式编程 (3)✔️闭包与捕获变量](https://www.jianshu.com/p/b968524a0e95)
   2. [kotlin 闭包简单例子](https://segmentfault.com/a/1190000013333527)