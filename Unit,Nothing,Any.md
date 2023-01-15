A **parameter** is a named variable passed into a function. ... Note the **difference between parameters** and **arguments**: Function **parameters** are the names listed **in the** function's definition. Function **arguments** are the real values passed to the function

### Any

Any是kotlin中所有非空类型的父类(注意：这里是非空类型)，它和Java的Object方法很像

​	相同点：

* Java中不指定class的父类会默认继承Object，kotlin中不指定class的父类会默认继承Any

​    不同点：

* Java的Object中有11个方法，kotlin的Any中只有3个方法,如下

  ```kotlin
  public open class Any {
      public open operator fun equals(other: Any?): Boolean
      public open fun hashCode(): Int
      public open fun toString(): String
  }
  
  ```

  在JVM层，并没有Any类型，编译器会把他转成java.lang.Object,所以即使我们在kotlin中不能直接调用Any中缺失的方法(比如Wait，notify这些)，但实际上所有kotlin对象都是由这些方法的

* java中基本类型(int,boolean等)不在类型系统中，比如1不是继承字Object的，但是kotlin中1是继承自Any的(实际上kotlin中的字面量是Int对象类型)

### Unit

Unit是一个object对象类型

```kotlin
/**
 * The type with only one value: the `Unit` object. This type corresponds to the `void` type in Java.
 */
public object Unit {
    override fun toString() = "kotlin.Unit"
}
```

Kotlin中Unit相当于Java中的void

```kotlin
class Test {
    fun test():Unit{}//waring:Redundant 'Unit' return type
    // fun test(){}//这两种写法是等价的，如果没有写返回值类型，那么默认是Unit
}
```

```java
public class TestJava {
    public void test(){ }//必须声明返回值类型  return type required
}
```

   区别：

* Unit可以是变量的类型或者反应的类型，因此它可以当作参数使用,void不能是变量的类型

* Unit是隐式返回，不需要return

  ```kotlin
  interface DataProcessor<T> {
      fun processData(): T
  }
  
  class NoResultDataProcessor : DataProcessor<Unit> { // Use "no value" as a type argument
      override fun processData() {
          ...
          // No need of a explicit return 
      }
  }
  ```

* Unit的父类是Any，可空的Unit?父类型是Any?，void没有父类

### Nothing

```kotlin
/**
 * Nothing has no instances. You can use Nothing to represent "a value that never exists": for example,
 * if a function has the return type of Nothing, it means that it never returns (always throws an exception).
 */
public class Nothing private constructor()
```

Nothing没有java中对应的类型，在kotlin中当一个函数没有正常终止因此返回值没有意义的时候可以用Nothing当返回值，Nothing是其它所有类的子类，但是Nothing只有一个私有构造函数，所以无法实例化。在任何语言里，不管是何种返回类型，都有可能因为异常等原因不返回任何值，Kotlin使用Nothing让这个特性更明确易懂

举一个例子就是TODO()函数

```kotlin
public inline fun TODO(): Nothing = throw NotImplementedError()

class Demo:RecyclerView.Adapter<CommonViewHolder>() {
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): CommonViewHolder {
        TODO("Not yet implemented")//TODO返回值是Nothing，Nothing是任何类得到子类型，而Java默认实现是return null
    }

    override fun onBindViewHolder(holder: CommonViewHolder, position: Int) {
        TODO("Not yet implemented")
    }

    override fun getItemCount(): Int {
        TODO("Not yet implemented")//java默认实现是return 0,这里直接抛出异常
    }
}
```

另外一个例子

```kotlin
var  i = 1
fun reportError():Nothing{
    throw RuntimeException()
}
fun example(){
    reportError()//抛出异常
    i = 2//编译器警告，Unreachable code
}

fun example2(map:Map<String,String>){
    val data = map["key"]?: reportError()//编译成功,另外这里可以使用kotlin提供的error函数
}

```

```java
public class TestJava {
    int i = 0;
    void reportError(){
        throw new RuntimeException();
    }
    private void example(){
        reportError();//抛出错误
        i = 2;//这行是无法执行的，但是编译器不会提出警告
    }
    private void exampleTwo(Map<String,String> map){
        String data = map.containsKey("key")? map.get("key") : reportError();//编译失败，需要返回String,实际是void
    }
}
```

上述的例子使用Nothing克服了在java中，当明确的需要一个类型返回值时，因为传参不合理，不得已返回null,-1等，然后又要在调用出检查的副作用

Java中有一个类型Void(大写的Void)，是void的自动装箱类型，如果你想让一个方法的返回类型永远是null的话，可以定义返回类型定义为大写的Void，而Void对应Kotlin的类型是Nothing?,Nothing?唯一可被访问的返回值也是null,Kotlin中类型层次结构最底层就是Nothing

### null

null比较特殊，它不是一个类型但又可以和任意类型结合使用(类型后面加?),String?就是一个组合类型String+null,Unit?Nothing?Any?这些也都是可以用的:

* Unit? 允许返回一个声明 或者 null.我不知道它有什么有趣的用途.

* Nothing? 比较特殊 因为这是让一个方法只能返回 null 的返回类型.据我所知,目前还不知道这个的实际用途.

* Any? 这个就很重要了.因为它相当于Java的Object,并且在Java是可以返回null的.还有当你声明一个泛型类,假设`MyClass<T>`,跟Java一样,这个T类型的隐示约束就是`Any?`.如果你想限制这个泛型类不能为可空类型,你就必须显示的指定:`MyClass<T:Any>`

### 总结

Unit与Nothing之间的区别是Unit类型表达式计算结果返回值是Unit，和void相似；Nothing类型表达式计算结果永远是不会返回的

Nothing?唯一的允许值是null

Any?是可空类型的根。Any?是Any的超集，Any?是Kotlin类型层次的最顶端

```kotlin
println(1 is Any)//true
println(1 is Any?)//true
println(null is Any)//false
println(1 is Any?)//true
println(Any() is Any?)//true
```

