1. 创建正则--简单使用

   ```javascript
   <script>
       //正则字面量 //包裹
       console.log(/20\d\d/.test("2019"))//true
       console.log(/20\d\d/.test("2100"))//false
       let a = "r"
       console.log(/a/.test("345a"))//true
    console.log(eval(`/${a}/`).test("345a"))//false
       let content = "baidu.com"
       let reg = new RegExp("u")//使用对象的形式就不用//包裹了
       console.log(reg.test(content))//true
   </script>
   ```
   
   ```java
public static void main(String[] args) {
       //因为正则表达式可以用字符串来描述规则，所以都可以使用变量
       String variable = "\\d";
       String regex = "20\\d"+variable;//这相当于正则字面量的形式
       System.out.println("2019".matches(regex));//true
       System.out.println("2100".matches(regex));//false
       boolean matches = Pattern.matches(regex, "2019");//这就是String类matches的源码
       System.out.println(matches);
       Pattern pattern = Pattern.compile("20\\d\\d");//这相当于正则对象
       Matcher matcher = pattern.matcher("2019");
       while (matcher.find()){
           System.out.println(matcher.group());//2019
       }
   }
   ```
   
2. 选择符

   ```javascript
   <script>
       let tel = "010-9999999"//true
       tel = "020-9999999"//true
       tel = "010"//true
       tel = "020"//false
       //，这样表示是否包含010或者020\-\d{7,8}整体匹配到的
   	//其实真实的意思是/[010|020]\-\d{7,8}/
       console.log(/010|020\-\d{7,8}/.test(tel))//test是检测字符串是否包含正则匹配到的内容,只要包含就返回true
   </script>
   ```

   ```java
   public static void main(String[] args) {
       String content = "010-9999999";//false
       content = "020-9999999";//true
       content = "020-9999";//false
       content = "010";//true
       content = "020";//false
       System.out.println(content.matches("010|020-\\d{7,8}"));
       //这样写的意思是010或者020-\d{7,8}
       //即选择符右边的是一组，并且matches是整体匹配
       //真实的意思是"[010-020]-\\d{7,8}"
   }
   ```

 3. 转义-关于转义的理解，js和Java是一样的

    ```javascript
    <script>
        //js中\是转义字符,正则中\也是转义字符,由于字面量正则/是特殊字符,所以字面量要匹配/也需要转义\/
        console.log("d"==="d")//true
        console.log("\d" === "d")//true
        // console.log("\")//这样写会报错的
        console.log("\\")// \
        console.log("\d+\.\d+")//d+.d+
        console.log("\\d+\\.\\d+")//\d+\.\d+
        console.log("\\\\")// \\
        console.log("\\r\\")// \r\
    </script
    ```

    ```java
    /**
     * 转义理解
     * Java中\是转义字符，作用是转义后面一个字符(只转义后面第一个字符)，把它当作普通字符
     * ”“包裹起来表示字符串 "\"表示一个"和一个普通的"，所以这样写是会报错的
     * \\\\ 会输出\\ 可以这样理解 第一个\把第二个\转义了，两个最终输出一个普通的\ 第3个\把第四个\转义了，最终输出一个普通的\ 整体最后输出两个普通的\\
     * \\r\\ 第一个\把第二个\转义 第3个\把第4个\转义 最终输出字符串 \r\
     *
     * 正则当中\也是转义字符 作用后边的一个字符，整体变得特殊 例如 d在正则里就是普通的d \d在正则里就代表数字
     * 所以正则匹配时要写\\d 因为\\d最终代表\d，在正则里才代表数字
     */
    public class RegTransfer {
        public static void main(String[] args) {
            System.out.println("\\");// \
            System.out.println("\\\\");// \\
            System.out.println("\\r\\");// \r\
            System.out.println("234".matches("\\d"));
        }
    }
    ```

4. 修饰符

   1. i和g大小写

      ```javascript
      <script>
          let content = "123sd#&^%SD"
          let regStr = /[a-z]/ //默认是区分大小写的和非贪婪匹配的 得到的结果 s
          regStr = /[a-z]+/g//得到的结果sd
          regStr = /[a-z]+/ig//得到的结果sd SD
          let match = content.match(regStr);
          console.log(...match)
      </script>
      ```

      ```java
      public static void main(String[] args) {
          //大小写
          String content = "123sd#&^%SD";
          String regStr = "(?i)[a-z]+";//(?i)代表后边的忽略大小写不会计入分组 这种方式可以灵活的控制大小写
          regStr = "[a-z]+";
          Pattern pattern = Pattern.compile(regStr);
          pattern = Pattern.compile(regStr, Pattern.CASE_INSENSITIVE);//这样代表整体忽略大小写
          Matcher matcher = pattern.matcher(content);
          while (matcher.find()) {//如果只想取匹配到的第一个这里用if就行了
              System.out.println("找到了："+matcher.group());
          }
      }
      ```

   2. 多行操作符

      ```javascript
      <script>
         //多行匹配
          let str = `
                  #1 js,90 #
                  #2 java,86 #
                  #3 djfi,3 # dsf
                  #4 Kotlin,87 #
          `
          let reg = /^\s*#\d+.+\s+#$/gm  //m的意思是每行单独对待
         let lessons =  str.match(reg).map(v => {
              v = v.replace(/\s*#\d+\s*/,"").replace(/\s+#/,"");
              [name,price] = v.split(",")
              return {name,price}
          })
          console.log(JSON.stringify(lessons))//[{"name":"js","price":"90"},{"name":"java","price":"86"},{"name":"Kotlin","price":"87"}]
          //
      </script>
      ```

      ```kotlin
      fun main() {
          val str =
              """     #1 js,90 #
                  #2 java,86 #
                  #3 djfi,93 # dsf 
                  #4 Kotlin,87 #
          """
          val regStr = "^\\s*#\\d+.+\\s+#$";
          val pattern = Pattern.compile(regStr, Pattern.MULTILINE)
          val matcher = pattern.matcher(str)
          val list = mutableListOf<String>()
          while (matcher.find()) {
              list.add(matcher.group())
          }
          val result = list.map { v ->
             val (name,price) =  v.replace("\\s*#\\d+\\s*".toRegex(), "").replace("\\s+#".toRegex(),"").split(",")
             mapOf("name" to name,"price" to price)
          }
          println(result)//[{name=js, price=90}, {name=java, price=86}, {name=Kotlin, price=87}]
      }
      
      ```

      ```java
      public static void main(String[] args) {
         //多行匹配
         String content =
                  "            #1 js,90 #\n" +
                          "            #2 java,86 #\n" +
                          "            #3 djfi,93 # dsf \n" +
                          "            #4 Kotlin,87 #\n" +
                          "    ";
         String regStr = "^\\s*#\\d+.+\\s+#$";
         Pattern pattern = Pattern.compile(regStr, Pattern.MULTILINE);//如果不用MULTILINE模式是不行的
         Matcher matcher = pattern.matcher(content);
          List<String> list = new ArrayList<>();
          while (matcher.find()) {
              list.add(matcher.group());
          }
        List<Map<String,String>> result =  list.stream().map(s -> {
              String[] array = s.replaceAll("\\s*#\\d+", "").replaceAll("\\s#", "").split(",");
              HashMap<String,String> map =new HashMap<>();
              map.put("name", array[0]);
              map.put("price", array[1]);
              return map;
          }).collect(Collectors.toList());
          for (Map<String, String> map : result) {
              System.out.println(map.entrySet());
          }
      }
      ```

5. 分组()括住的代表一组以及原子组引用和原子组非捕获

   ```java
   public static void main(String[] args) {
   //()括起来的是第一组，从左边第一个(括号算是第一组，第二个(是第二组，后面用\1和\2用来引用分组的内容
   //        String content = "df的话覅的1991返回发1234覆盖3333erd12321-333999111";
   //        String regStr = "(\\d)(\\d)\\2\\1";//连续4位数，第1位和第四位相同，第二位和第三位相同
   //        String regStr = "(\\d)\\1{3}";//连续4位数，都相同
           String regStr = "\\d{5}-(\\d)\\1{2}(\\d)\\2{2}(\\d)\\3{2}";//前面是一个5位数，然后是一个-号，然后是一个9位数，连续的每3位相同
   
           String content = "我....我要....学学学学....编程Java!";//结巴语句变成我哟啊学编程Java
           content = content.replaceAll("\\.", "").replaceAll("(.)\\1+","$1");
   //        String $1 = Pattern.compile("(.)\\1+").matcher(content).replaceAll("$1");//$1是在外部使用反向引用
   //        System.out.println($1);
           System.out.println(content);//我要学编程java
   
           Pattern pattern = Pattern.compile(regStr);
           Matcher matcher = pattern.matcher(content);
           while (matcher.find()) {
               System.out.println("找到了："+matcher.group(0));
           }
       
       //----------------------------------- 非捕获分组和js差不多
               String str = "parade岁月山大佛i和房符合parade0393df发parade同学";
           String regEStr = "parade(?:岁月|0393)";//不会捕获分组
   //        String regEStr = "parade(?:岁月|0393|同学)";//parade岁月和parade0393和parade同学 不会捕获分组
   //        String regEStr = "parade(岁月|0393)";//parade岁月和parade0393 会捕获分组
   //        String regEStr = "parade(?=岁月|0393)";//parade和parade 非捕获parade岁月的parade和parade0393的parade
   //        String regEStr = "parade(?!岁月|0393)";//parade和parade 非捕获parade岁月的parade和parade0393的parade
           Pattern compile = Pattern.compile(regEStr);//parade后边不是岁月和0393的parade  和上面的是取反
           Matcher match = compile.matcher(str);
           while (match.find()) {
               System.out.println(match.group(0));
   //            System.out.println(match.group(1));//使用非捕获分组，这里写就会报错IndexOutOfBoundsException
           }
       //其他的非捕获分组?<= ?<! 这些非捕获分组只是条件 准确的来说叫断言 修饰符是不能修饰这些断言的
       }
   ```

   ```javascript
   <script>
       let content = "df的话覅的1991返回发1234覆盖3333erd12321-333999111"
       let reg = /(\d)(\d)\2\1/g//连续四位数，第一位和第4位相同，第2位和第3位相同 --1991和3333
       reg = /(\d)\1{3}/g//连续4位数都相同--3333
       reg = /\d{5}-(\d)\1{2}(\d)\2{2}(\d)\3{2}/g//前面是一个5位数，然后是一个-号，然后是一个9位数，连续的每3位相同--12321-333999111
       content = "我....我要....学学学学....编程Java!"//我要学编程Java
       reg = /\./g
      content = content.replace(reg,"").replace(/(.)\1+/g,"$1")
       console.log(content)//我要学编程Java
       // console.log(content.match(reg))//
       content = "parade岁月山大佛i和房符合parade0393df发parade同学";
       //reg = /parade(?<alias>岁月|0393)/g//会捕获分组 res[1]输出岁月和0393 ?<name>代表给分组起别名
       // reg = /parade(?:岁月|0393)/g//不会捕获分组 res[1]输出undefined 这里需要使用g修饰符，否则只会匹配一个
   	// reg = /parade(?=0393|岁月)/g//不会捕获分组
   	reg = /parade(?!0393|岁月)/g//不会捕获分组 parade同学的parade
       let result = []
       while ((res = reg.exec(content))){
           result.push(res)
           // console.log(res[1])//代表第一个分组,使用?:
           // console.log(res.groups.alias)//和res[1]效果是一样的 alias是给分组起的别名
       }
       console.log(result)
   </script>
   ```

6. 贪婪匹配

   ```java
   /**
    * java默认是贪婪匹配的
    * 任何一个其他限制符 (*, +, ?, {n}, {n,}, {n,m}) 后面时，匹配模式是非贪婪的后边紧跟是非贪婪的
    * 非贪婪匹配是尽可能少的匹配
    */
   public static void main(String[] args) {
           String content = "123000";
           String regStr = "(\\d+?)(0*)";//如果"(\\d+)(0*)"，那么第一组是123000，第二组是""空字符串
           content = "hello234okdfsdf34ok";
           regStr = "\\d+?ok";//此时"\\d+?ok"效果一样
           regStr = "\\d+";//此时匹配 234和34
           regStr = "\\d+?";//此时匹配 2 3 4 3 4
           Pattern pattern = Pattern.compile(regStr);
           Matcher matcher = pattern.matcher(content);
   //        if (matcher.matches()) {//matches是整体匹配
   //            System.out.println("整体找到："+matcher.group());
   //            System.out.println("group1:"+matcher.group(1));
   //            System.out.println("group2:"+matcher.group(2));
   //        }
           StringBuffer buffer = new StringBuffer();
           while (matcher.find()) {
               matcher.appendReplacement(buffer, "-");
               System.out.println("找到了：" + matcher.group());
           }
           matcher.appendTail(buffer);//如果没有这一句下面会输出hello---okdfsdf--，有这一句会输出hello---okdfsdf--ok
           System.out.println(buffer);
       }
   ```

   ```javascript
   <script>
       let content = "123000"
       let reg = /^(\d+)(0*)$///默认也是贪婪的
       console.log(content.match(reg))//第一组是123000
       reg = /^(\d+?)(0*)$///禁止贪婪
       console.log(content.match(reg))//第一组是123 match默认不是整体匹配
   </script>
   ```

   

7. 方法使用对比

   ```java
   /**
    * String类的方法
    * 1.split(String regex) 注意如果开头就匹配，那最终数组第一个元素是空字符
    * 2.replaceAll(String regx)
    * 3.replaceFirst(String regx)
    * 4.matches(String regx)//注意此方法是整体匹配  部分匹配请使用Matcher 类的find方法
    * Matcher类的方法
    * 1.find
    * 2.matches 和String类的matches效果一样
    * 3.lookAt() 从头开始找 非整体，找不到就返回false即有子字符串符合
    * 4.replaceAll 和replaceFirst和String类的效果一样
    * 5.appendReplacement 和 appendTail 结合 find可以在循环查找的时候定义每个匹配到的字符串的替换规则
    */
   public class RegSummary {
       public static void main(String[] args) {
           String content = "1parade岁月山大佛i和房符合parade0393df发parade同学";
           String regStr = "parade(0393|岁月)";
           String[] split = content.split(regStr);
           //注意如果开头就匹配，那最终数组第一个元素是空字符
   //        System.out.println(split.length);//3
           for (String s : split) {
   //            System.out.println(s);//山大佛i和房符合  和  df发parade同学 其实数组有3个元素，第一个是空字符
           }
           //如果limit参数为 0 或 负数，则split()返回包含所有子字符串的数组。
           //如果limit参数为正（例如n），则split()返回数组的最大长度。
           String[] strings = content.split(regStr, 2);
           for (String a : strings) {
   //            System.out.println(a);
           }
   //        System.out.println(content.replaceAll(regStr,""));//山大佛i和房符合df发parade同学
   //        System.out.println(content.replaceFirst(regStr,""));//山大佛i和房符合parade0393df发parade同学
   //        System.out.println(content.matches(regStr));//false 因为matches是整体匹配
   
           Pattern pattern = Pattern.compile(regStr);
           Matcher matcher = pattern.matcher(content);
   //        System.out.println(matcher.replaceAll(""));
           while (matcher.find()) {
   //            System.out.println("找到了："+matcher.group(0));
           }
   
           Pattern p = Pattern.compile("java", Pattern.CASE_INSENSITIVE);
           Matcher m = p.matcher("java Java JAVA JAva I love Java and you ?");
           StringBuffer sb = new StringBuffer();
           int index = 1;
           while(m.find()){
               //m.appendReplacement(sb, (index++ & 1) == 0 ? "java" : "JAVA"); 较为简洁的写法
               if((index & 1) == 0){//偶数
                   m.appendReplacement(sb, "java");
               }else{
                   m.appendReplacement(sb, "JAVA");
               }
               index++;
           }
           m.appendTail(sb);//把剩余的字符串加入
           System.out.println(sb);
       }
   }
   ```

   ```javascript
   <script>
       /**
        * 正则方法：
        * 1.test 默认不是整体匹配的，整体匹配需要加上边界符^和$
        * 2.exec 类似于Java的find方法 ***注意使用此方法必须使用g模式 否则会死循环***
        * 字符串方法：
        * 1.search：检索匹配的子符串开始的索引 只匹配第一个符合的子串
        * 2.match 默认只匹配第一个找到的子串返回一个数组(包括匹配到的内容和分组等信息) 如果使用g模式会返回匹配的所有子串但不包括分组等其他信息
        * 3.matchAll 此方法必须使用g模式，否则会抛异常 返回一个迭代器 迭代器里的每个元素都有详细的信息(匹配的内容分组等信息)
        * 4.split 拆分字符串
        * 5.replace 替换
        */
       const content = "23parade岁月山大佛i和房符合parade0393df发parade同学";
       let reg = /parade(0393|岁月)/
       // console.log(reg.test(content))//true 这里正则默认不是整体匹配的，只要有子字符串满足正则就返回true
       // reg = /^parade(0393|岁月)&/
       // console.log(reg.test(content))//false 整体匹配就是false了
       reg = /parade(0393|岁月)/g//这里必须使用g模式，否则while会进入死循环
       let list = []
       while ((res = reg.exec(content))){
           list.push(res)//返回值res是一个数组，res[0]是匹配到的对象，res[1]是第一个分组 res[2]是第二个分组，还有groups属性存储了命名分组
       }
       console.log(list)
       reg = /parade(0393|岁月)/g
       console.log(content.search(reg))//2
       for (let item of content.matchAll(reg)) {
           console.log(item)
       }
   
       let str = "(010)9999999 (020)8888888"
       let reg$ = /\((\d{3,4})\)(\d{7,8})/g
       console.log(str.replace(reg$,"$1-$2"))//010-9999999 020-8888888  //$1和$2是在外部使用分组
       //$& - 正则表达式匹配的文本
       //$` - 匹配文本的左侧内容
       //' - 匹配文本的右侧内容
           let text = "abc123def";
       console.log(text.replace(/\d+/g, "[$&]"))   // abc[123]def
       console.log(text.replace(/\d+/g, "[$`]"))     // abc[abc]def
       console.log(text.replace(/\d+/g, "[$']"));   // abc[def]def
   
       let testStr = "23parade岁月山大佛i和房符合parade0393df发parade同学"
       console.log(testStr.replace(/parade(0393|岁月)/g,"$`"))//2323山大佛i和房符合23parade岁月山大佛i和房符合df发parade同学 这个有点坑，匹配左边的
   </script>
   ```

   

8. 修饰符

   | 修饰符 | 含义                                   |                             描述                             |
   | :----- | :------------------------------------- | :----------------------------------------------------------: |
   | i      | ignore - 不区分大小写                  | 将匹配设置为不区分大小写，搜索时不区分大小写: A 和 a 没有区别。 |
   | g      | global - 全局匹配                      |                      查找所有的匹配项。                      |
   | m      | multi line - 多行匹配                  | 使边界字符 **^** 和 **$** 匹配每一行的开头和结尾，记住是多行，而不是整个字符串的开头和结尾。 |
   | s      | 特殊字符圆点 **.** 中包含换行符 **\n** |                                                              |

9. 元字符表

   | 字符         |                             描述                             |
   | :----------- | :----------------------------------------------------------: |
   | \            | 将下一个字符标记为一个特殊字符、或一个原义字符、或一个 向后引用、或一个八进制转义符。例如，'n' 匹配字符 "n"。'\n' 匹配一个换行符。序列 '\\' 匹配 "\" 而 "\(" 则匹配 "("。 |
   | ^            | 匹配输入字符串的开始位置。如果设置了 RegExp 对象的 Multiline 属性，^ 也匹配 '\n' 或 '\r' 之后的位置。 |
   | $            | 匹配输入字符串的结束位置。如果设置了RegExp 对象的 Multiline 属性，$ 也匹配 '\n' 或 '\r' 之前的位置。 |
   | *            | 匹配前面的子表达式零次或多次。例如，zo* 能匹配 "z" 以及 "zoo"。* 等价于{0,}。 |
   | +            | 匹配前面的子表达式一次或多次。例如，'zo+' 能匹配 "zo" 以及 "zoo"，但不能匹配 "z"。+ 等价于 {1,}。 |
   | ?            | 匹配前面的子表达式零次或一次。例如，"do(es)?" 可以匹配 "do" 或 "does" 。? 等价于 {0,1}。 |
   | {n}          | n 是一个非负整数。匹配确定的 n 次。例如，'o{2}' 不能匹配 "Bob" 中的 'o'，但是能匹配 "food" 中的两个 o。 |
   | {n,}         | n 是一个非负整数。至少匹配n 次。例如，'o{2,}' 不能匹配 "Bob" 中的 'o'，但能匹配 "foooood" 中的所有 o。'o{1,}' 等价于 'o+'。'o{0,}' 则等价于 'o*'。 |
   | {n,m}        | m 和 n 均为非负整数，其中n <= m。最少匹配 n 次且最多匹配 m 次。例如，"o{1,3}" 将匹配 "fooooood" 中的前三个 o。'o{0,1}' 等价于 'o?'。请注意在逗号和两个数之间不能有空格。 |
   | ?            | 当该字符紧跟在任何一个其他限制符 (*, +, ?, {n}, {n,}, {n,m}) 后面时，匹配模式是非贪婪的。非贪婪模式尽可能少的匹配所搜索的字符串，而默认的贪婪模式则尽可能多的匹配所搜索的字符串。例如，对于字符串 "oooo"，'o+?' 将匹配单个 "o"，而 'o+' 将匹配所有 'o'。 |
   | .            | 匹配除换行符（\n、\r）之外的任何单个字符。要匹配包括 '\n' 在内的任何字符，请使用像"**(.\|\n)**"的模式。 |
   | (pattern)    | 匹配 pattern 并获取这一匹配。所获取的匹配可以从产生的 Matches 集合得到，在VBScript 中使用 SubMatches 集合，在JScript 中则使用 $0…$9 属性。要匹配圆括号字符，请使用 '\(' 或 '\)'。 |
   | (?:pattern)  | 匹配 pattern 但不获取匹配结果，也就是说这是一个非获取匹配，不进行存储供以后使用。这在使用 "或" 字符 (\|) 来组合一个模式的各个部分是很有用。例如， 'industr(?:y\|ies) 就是一个比 'industry\|industries' 更简略的表达式。 |
   | (?=pattern)  | 正向肯定预查（look ahead positive assert），在任何匹配pattern的字符串开始处匹配查找字符串。这是一个非获取匹配，也就是说，该匹配不需要获取供以后使用。例如，"Windows(?=95\|98\|NT\|2000)"能匹配"Windows2000"中的"Windows"，但不能匹配"Windows3.1"中的"Windows"。预查不消耗字符，也就是说，在一个匹配发生后，在最后一次匹配之后立即开始下一次匹配的搜索，而不是从包含预查的字符之后开始。 |
   | (?!pattern)  | 正向否定预查(negative assert)，在任何不匹配pattern的字符串开始处匹配查找字符串。这是一个非获取匹配，也就是说，该匹配不需要获取供以后使用。例如"Windows(?!95\|98\|NT\|2000)"能匹配"Windows3.1"中的"Windows"，但不能匹配"Windows2000"中的"Windows"。预查不消耗字符，也就是说，在一个匹配发生后，在最后一次匹配之后立即开始下一次匹配的搜索，而不是从包含预查的字符之后开始。 |
   | (?<=pattern) | 反向(look behind)肯定预查，与正向肯定预查类似，只是方向相反。例如，"`(?<=95|98|NT|2000)Windows`"能匹配"`2000Windows`"中的"`Windows`"，但不能匹配"`3.1Windows`"中的"`Windows`"。 |
   | (?<!pattern) | 反向否定预查，与正向否定预查类似，只是方向相反。例如"`(?<!95|98|NT|2000)Windows`"能匹配"`3.1Windows`"中的"`Windows`"，但不能匹配"`2000Windows`"中的"`Windows`"。 |
   | x\|y         | 匹配 x 或 y。例如，'z\|food' 能匹配 "z" 或 "food"。'(z\|f)ood' 则匹配 "zood" 或 "food"。 |
   | [xyz]        | 字符集合。匹配所包含的任意一个字符。例如， '[abc]' 可以匹配 "plain" 中的 'a'。 |
   | [^xyz]       | 负值字符集合。匹配未包含的任意字符。例如， '[^abc]' 可以匹配 "plain" 中的'p'、'l'、'i'、'n'。 |
   | [a-z]        | 字符范围。匹配指定范围内的任意字符。例如，'[a-z]' 可以匹配 'a' 到 'z' 范围内的任意小写字母字符。 |
   | [^a-z]       | 负值字符范围。匹配任何不在指定范围内的任意字符。例如，'[^a-z]' 可以匹配任何不在 'a' 到 'z' 范围内的任意字符。 |
   | \b           | 匹配一个单词边界，也就是指单词和空格间的位置。例如， 'er\b' 可以匹配"never" 中的 'er'，但不能匹配 "verb" 中的 'er'。 |
   | \B           | 匹配非单词边界。'er\B' 能匹配 "verb" 中的 'er'，但不能匹配 "never" 中的 'er'。 |
   | \cx          | 匹配由 x 指明的控制字符。例如， \cM 匹配一个 Control-M 或回车符。x 的值必须为 A-Z 或 a-z 之一。否则，将 c 视为一个原义的 'c' 字符。 |
   | \d           |               匹配一个数字字符。等价于 [0-9]。               |
   | \D           |             匹配一个非数字字符。等价于 [^0-9]。              |
   | \f           |             匹配一个换页符。等价于 \x0c 和 \cL。             |
   | \n           |             匹配一个换行符。等价于 \x0a 和 \cJ。             |
   | \r           |             匹配一个回车符。等价于 \x0d 和 \cM。             |
   | \s           | 匹配任何空白字符，包括空格、制表符、换页符等等。等价于 [ \f\n\r\t\v]。 |
   | \S           |         匹配任何非空白字符。等价于 [^ \f\n\r\t\v]。          |
   | \t           |             匹配一个制表符。等价于 \x09 和 \cI。             |
   | \v           |           匹配一个垂直制表符。等价于 \x0b 和 \cK。           |
   | \w           |        匹配字母、数字、下划线。等价于'[A-Za-z0-9_]'。        |
   | \W           |      匹配非字母、数字、下划线。等价于 '[^A-Za-z0-9_]'。      |
   | \xn          | 匹配 n，其中 n 为十六进制转义值。十六进制转义值必须为确定的两个数字长。例如，'\x41' 匹配 "A"。'\x041' 则等价于 '\x04' & "1"。正则表达式中可以使用 ASCII 编码。 |
   | \num         | 匹配 num，其中 num 是一个正整数。对所获取的匹配的引用。例如，'(.)\1' 匹配两个连续的相同字符。 |
   | \n           | 标识一个八进制转义值或一个向后引用。如果 \n 之前至少 n 个获取的子表达式，则 n 为向后引用。否则，如果 n 为八进制数字 (0-7)，则 n 为一个八进制转义值。 |
   | \nm          | 标识一个八进制转义值或一个向后引用。如果 \nm 之前至少有 nm 个获得子表达式，则 nm 为向后引用。如果 \nm 之前至少有 n 个获取，则 n 为一个后跟文字 m 的向后引用。如果前面的条件都不满足，若 n 和 m 均为八进制数字 (0-7)，则 \nm 将匹配八进制转义值 nm。 |
   | \nml         | 如果 n 为八进制数字 (0-3)，且 m 和 l 均为八进制数字 (0-7)，则匹配八进制转义值 nml。 |
   | \un          | 匹配 n，其中 n 是一个用四个十六进制数字表示的 Unicode 字符。例如， \u00A9 匹配版权符号 (?)。 |