1. Dart

   1. 在 Dart 中，所有类型都是对象类型，未初始化变量的值都是 null

   2. 对于多行字符串可以用三个单引号或者三个双引号的方式声明

      ```dart
      void main() {
        
      var s3 = """This is a
      multi-line string.""";
        print(s3);
      }
      ```

   3. const表示在编译时期就确定了的值，final表示可以在运行时确定值，一旦确定后就不可再改变

   4. 判断变量的类型

      ```dart
      void main() {
      var s = "1";
      print(s.runtimeType) ;//String
      print(s is String);//true
      }
      ```

   5. flutter源码中构造函数都是用const,常量构造函数，初始化完就不让改了

   6. 在 Dart 中，所有类型都是对象类型，函数也是对象，它的类型叫作 Function。这意味着函数也可以被定义为变量，甚至可以被定义为参数传递给另一个函数

   7. 可选命名参数和可忽略的参数

      ```dart
      void main() {
        //如果一个变量可空，要在变量类型后加?
      //要达到可选命名参数的用法，那就在定义函数的时候给参数加上 {}
      void enable1Flags({bool? bold, bool? hidden}) => print("$bold , $hidden");
      
      //定义可选命名参数时增加默认值
      void enable2Flags({bool bold = true, bool hidden = false}) => print("$bold ,$hidden");
      
      //可忽略的参数在函数定义时用[]符号指定
      void enable3Flags(bool bold, [bool? hidden]) => print("$bold ,$hidden");
      
      //定义可忽略参数时增加默认值
      void enable4Flags(bool bold, [bool hidden = false]) => print("$bold ,$hidden");
      
      //可选命名参数函数调用
      enable1Flags(bold: true, hidden: false); //true, false
      enable1Flags(bold: true); //true, null
      enable2Flags(bold: false); //false, false
      
      //可忽略参数函数调用
      enable3Flags(true, false); //true, false
      enable3Flags(true,); //true, null
      enable4Flags(true); //true, false
      enable4Flags(true,true); // true, true
      }
      ```

   8. 变量作用域

      * 值得一提的是，Dart 中并没有 public、protected、private 这些关键字，我们只要在声明变量与方法时，在前面加上“_”即可作为 private 方法使用。如果不加“_”，则默认为 public。不过，“_”的限制范围并不是类访问级别的，而是库访问级别

   9. class的使用

      ```dart
      void main() {
        var p = new Point(100,200); // new 关键字可以省略
      p.printInfo();  // 输出(100, 200);
      Point.factor = 10;
      Point.printZValue(); // 输出10
      }
      
      
      class Point {
        num x, y;
        static num factor = 0;
        //语法糖，等同于在函数体内：this.x = x;this.y = y;
        Point(this.x,this.y);
        void printInfo() => print('($x, $y)');
        static void printZValue() => print('$factor');
      }
      ```

      * 重定向构造函数

        ```dart
        void main() {
          var p = Point.bottom(100);
          p.printInfo(); // 输出(100,0,0)
        }
        
        class Point {
          num x, y, z;
          Point(this.x, this.y): z = 0; // 初始化变量z 
          Point.bottom(num x) : this(x, 0); // 重定向构造函数 
          void printInfo() => print('($x,$y,$z)');
        }
        ```

      * 继承，接口实现和混入

        -- 继承父类意味着，子类由父类派生，会自动获取父类的成员变量和方法实现，子类可以根据需要覆写构造函数及父类方法

        -- 接口实现则意味着，子类获取到的仅仅是接口的成员变量符号和方法符号，需要重新实现成员变量，以及方法的声明和初始化，否则编译器会报错。

        -- 为了解决 Dart 缺少对多重继承的支持问题，除了继承和接口实现之外，Dart 还提供了另一种机制来实现类的复用，即“混入”（Mixin）

        ```dart
        
        class Point {
          num x = 0, y = 0;
          void printInfo() => print('($x,$y)');
        }
        
        //Vector继承自Point
        class Vector extends Point{
          num z = 0;
          @override
          void printInfo() => print('($x,$y,$z)'); //覆写了printInfo实现
        }
        
        //Coordinate是对Point的接口实现
        class Coordinate implements Point {
          num x = 0, y = 0; //成员变量需要重新声明
          void printInfo() => print('($x,$y)'); //成员函数需要重新声明实现
        }
        
        var xxx = Vector(); 
        xxx
          ..x = 1
          ..y = 2
          ..z = 3; //级联运算符，等同于xxx.x=1; xxx.y=2;xxx.z=3;
        xxx.printInfo(); //输出(1,2,3)
        
        var yyy = Coordinate();
        yyy
          ..x = 1
          ..y = 2; //级联运算符，等同于yyy.x=1; yyy.y=2;
        yyy.printInfo(); //输出(1,2)
        print (yyy is Point); //true
        print(yyy is Coordinate); //true
        ```

        -- 要使用混入，只需要 with 关键字即可

        ```dart
        
        class Coordinate with Point {
        }
        
        var yyy = Coordinate();
        print (yyy is Point); //true
        print(yyy is Coordinate); //true
        //通过混入，一个类里可以以非继承的方式使用其他类中的变量与方法
        ```

      * 运算符

        Dart 和绝大部分编程语言的运算符一样，所以你可以用熟悉的方式去执行程序代码运算。不过，Dart 多了几个额外的运算符，用于简化处理变量实例缺失（即 null）的情况

        * ?. 运算符：假设 Point 类有 printInfo() 方法，p 是 Point 的一个可能为 null 的实例。那么，p 调用成员方法的安全代码，可以简化为 p?.printInfo() ，表示 p 为 null 的时候跳过，避免抛出异常。

        * ??= 运算符：如果 a 为 null，则给 a 赋值 value，否则跳过。这种用默认值兜底的赋值语句在 Dart 中我们可以用 a ??= value 表示。

        * ?? 运算符：如果 a 不为 null，返回 a 的值，否则返回 b。在 Java 或者 C++ 中，我们需要通过三元表达式 (a != null)? a : b 来实现这种情况。而在 Dart 中，这类代码可以简化为 a ?? b

        * 运算符重载

          ```dart
          
          class Vector {
            num x, y;
            Vector(this.x, this.y);
            // 自定义相加运算符，实现向量相加
            Vector operator +(Vector v) =>  Vector(x + v.x, y + v.y);
            // 覆写相等运算符，判断向量相等
            bool operator == (dynamic v) => x == v.x && y == v.y;
          }
          
          final x = Vector(3, 3);
          final y = Vector(2, 2);
          final z = Vector(1, 1);
          print(x == (y + z)); //  输出true
          
          ```

          