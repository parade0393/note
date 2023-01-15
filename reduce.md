1. kotlin的reduce和fold

   ```kotlin
   fun main() {
      val numbers = listOf(5,2,10,4)
       //而 reduce() 的第一步则将第一个和第二个元素作为第一步的操作参数，后续会把操作的结果赋值给第一个参数，第二个参数往后延，依次去第3个参数，第四个参数等
       val sum = numbers.reduce { acc, i ->
           println("acc:${acc},i:${i}")
           acc+i
       }
       println(sum)
       /**
        * acc:5,i:2
        * acc:7,i:10
        * acc:17,i:4
        * 21
        */
       println("-------------------------")
       val total = numbers.fold(6){acc, i ->
           println("acc:${acc},i:${i}")//由于指定了初始值，acc的初始值也是6，i从数组第一个元素往后迭代
           acc+i
       }
       println(total)
       /**
        * acc:6,i:5
        * acc:11,i:2
        * acc:13,i:10
        * acc:23,i:4
        * 27
        */
   }
   ```

2. Js中的reduce，[关于reduce的骚操作](https://mp.weixin.qq.com/s/_S9kiL_bDPHkLg8WS_NJBA)

   ```javascript
   <script>
       let numbers = [5,2,10,4]
       //reduce接收两个参数，第一个参数是函数，第二个参数是初始值(可以不指定)
       //第一个函数的参数值，第一个表示返回的返回值(初始值是：如果reduce有第二个参数，则为reduce的第二个参数值，否则为数组的第一个元素)，
       //                第二个表示迭代的数组元素(reduce没有第二个参数，则从数组的第二个元素往后迭代，否则从第一个元素迭代)
       //                第三个表示当前的索引，第四个表示原数组
       let sum = numbers.reduce(function(pre,cur,index,arr){
           console.log(pre,cur)
           return pre+cur
       },0)
       console.log(`sum:${sum}`)
       /**
        * 0 5
        * 5 2
        * 7 10
        * 17 4
        * sum:21
        */
   </script>
   ```

3. Dart的reduce,没有提供指定初始值的功能

   ```dart
   void main() {
     
     var nums = [5,2,10,4];
     
     var sum = nums.reduce((total,cur){
       print("total:$total,cur:$cur");
       return total + cur;
     });
     print(sum);
     /**
      * total:5,cur:2
      * total:7,cur:10
      * total:17,cur:4
      * 21
    */
   }
   ```

4. Java中的reduce

   ```java
   Optional<Integer> reduce = Stream.of(4, 5, 3, 9).reduce((x, y) -> {
       System.out.println("x:" + x + "y:" + y);
       return x + y;
   });
   /** 打印结果
    * x:4y:5
    * x:9y:3
    * x:12y:9
    * 21
    */
   ```

   