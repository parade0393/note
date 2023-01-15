1. java8

   语法规则：

   ```java
   //（参数列表，参数类型:参数名称）-> {代码块} 
   //有时候参数类型可省略，一个参数的参数列表括号可去掉
   //有返回值的时候，如果代码块只有一行，则代码块的括号也可以去掉
   //可以给接口增加@FunctionalInterface，此注解表明该接口只能声明一个抽象方法
   List<Person> personList = new ArrayList<>();
   personList.add(new Person("Tom", 8900, 23, "male", "new York"));
   personList.add(new Person("Jack", 7000, 25, "male", "Washington"));
   personList.add(new Person("Lily", 7800, 21, "female", "Washington"));
   personList.add(new Person("Anni", 8200, 24, "female", "new York"));
   personList.add(new Person("Owen", 9500, 25, "male", "new York"));
   personList.add(new Person("Alisa", 7900, 26, "female", "new York"));
   personList.sort(new Comparator<Person>() {
       @Override
       public int compare(Person o1, Person o2) {
           return o1.getSalary() - o2.getSalary();
       }
   });
   personList.sort((x, y) -> x.getSalary() - y.getSalary());
   //匿名内部类的本质是在编译时生成一个xxxx$0.class
   //使用lambda的前提是具有函数式接口(@FunctionalInterface),而lambda表达式使用时不关心接口名、抽象方法名。只关心抽象方法的参数列表和返回值类型，因此为了让我们使用Lambda更加方便，在JDK中提供了大量常用的函数式接口：Consumer Supplier Function Predicater等

   
   //有时候lambda表达式的函数体和既有函数的功能一模一样，于是为了方便JDK又提供了方法引用，通常有以下几种格式：被引用的方法参数要和接口中的抽象方法的参数一样，当接口抽象方法有返回值时，被引用的方法也要有返回值(返回值类型也要一样)
   //1.instanceName::methodName 对象::方法名
   //2.ClassName::staticMethodName 类名::静态方法
   //3.ClassName::methodName//类名::普通方法---类型名称作为方法的调用者
   //4.ClassName::New new调用的构造器
   //5.TypeName[]::new String[]::new 调用数组的构造器
   Date now = new Date();
   Supplier<Long> supplier = () -> now.getTime();
   System.out.println(supplier.get());
   Supplier<Long> supplier1 = now::getTime;
   System.out.println(supplier1.get());
   
   Function<String,Integer> function = (s -> s.length());
   System.out.println(function.apply("Hello"));
   Function<String,Integer> function1 = String::length;//String作为方法的调用者
   System.out.println(function1.apply("Hello"));
   
   Function<Integer, String[]> function = (len) -> new String[len];
   Function<Integer,String[]> function1 = String[]::new;
   ```
   
   

