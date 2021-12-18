# Dart笔记

> [Dart官方文档](https://dart.dev/tutorials)

## 基础语法与变量类型

1. 变量:
   1. 可用`var`或者具体类型申明变量, 使用`var`时具体类型由编译器推断. 未初始化的变量值为`null`.
   2. **所有类型都是对象类型, 继承自`Object`对象**. 所以一切变量都是对象.
2. 数据类型:
   1. 数值类型`num`: 64位int和IEEE754标准的64位double. 
   2. bool: `true`/`false`, 编译时常量.
   3. String: UTF-16字符串. 
      1. 可使用`''`或者`""`构造字符串. 
      2. 可使用`${express}`格式化字符串. 如果是一个标识符, 可省略`{}`.
      3. 字符串拼接可使用`+`运算符. 例如 `'a' + 'b'`.
      4. 多行字符串可像swift一样使用三个引号.
   4. List与Map: 数组与字典集合类型.
   5. 常量:
      1. 定义不可变的变量, 需要在定义变量前加上final或const关键字.
      2. final适用于定义运行时常量, const用于定义编译常量. 例如

        ```dart
        var num1 = 0;
        var num2 = 1; 
        final sum = num1 + num2; 
        
        const age = 18;
        ```
3. 函数
   1. 函数也是对象, 类型为`Function`. 意味着函数可以被定义为变量, 可作为参数传递.
   2. 支持箭头函数.
   3. Dart不支持函数重载, 但是提供了可选命名参数和可选参数.

      ```dart
      //要达到可选命名参数的用法，那就在定义函数的时候给参数加上 {}
      void enable1Flags({bool bold, bool hidden}) => print("$bold , $hidden");

      //定义可选命名参数时增加默认值
      void enable2Flags({bool bold = true, bool hidden = false}) => print("$bold ,$hidden");

      //可忽略的参数在函数定义时用[]符号指定
      void enable3Flags(bool bold, [bool hidden]) => print("$bold ,$hidden");

      //定义可忽略参数时增加默认值
      void enable4Flags(bool bold, [bool hidden = false]) => print("$bold ,$hidden");

      //可选命名参数函数调用
      enable1Flags(bold: true, hidden: false); //true, false
      enable1Flags(bold: true); //true, null
      enable2Flags(bold: false); //false, false

      //可忽略参数函数调用
      enable3Flags(true, false); //true, false
      enable3Flags(true); //true, null
      enable4Flags(true); //true, false
      enable4Flags(true,true); // true, true
      ```
4. 类
   1. 特定类型的数据和方法的集合, 也是创建对象的模板.
   2. 每个对象都是类的实例, 都继承自顶层类型Object.
   3. 声明和使用与java类似.
   4. 没有`public`, `projected`, `private`等关键字. 可在声明变量与方法时添加前缀`_`作为private使用. **`_`的限制范围不是类访问级别, 而是库访问级别**.
   5. 可通过构造函数语法糖进行初始化. 提供了命名构造函数的方式使类的实例化过程语义更清晰. 此外支持初始化列表, 在构造函数函数体执行前, 有机会给变量赋默认值, 甚至重定向至里一个构造函数.
      ```dart
      class Point {
         num x, y, z;
         Point(this.x, this.y, this.z); //语法糖, 等同在函数体内: this.x = x;this.y = y;this.z = z;
         Point(this.x, this.y) : z = 0; // 初始化变量z提供默认值
         Point.bottom(num x) : this(x, 0); // 重定向构造函数
         void printInfo() => print('($x,$y,$z)');
      }

      var p = Point.bottom(100);
      p.printInfo(); // 输出(100,0,0)
      ```
   6. 支持继承和接口实现(跟swift的协议不一样). 接口实现是只复用变量和函数声明, 不复用实现.
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

      //Coordinate是对Point的接口实现, 获取到Point的一个空壳, 不能复用Point的原有实现.
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
   7. Mixin机制. 可看作具有实现方法的接口. Dart不支持多继承, 通过Mixin可以使一个类以非继承的方式使用其他类中的变量与方法, 同时避免多继承可能导致的菱形问题.
      ```dart
      // 修改上个例子中的接口实现, 改为Mixin的方式
      class Coordinate with Point {
      }

      var yyy = Coordinate();
      print (yyy is Point); //true
      print(yyy is Coordinate); //true
      ```
5. 运算符
   1. `?.`: 有点像swift的可选类型函数调用. 假设 Point 类有 printInfo() 方法，p 是 Point 的一个可能为 null 的实例。那么，p 调用成员方法的安全代码，可以简化为 p?.printInfo() ，表示 p 为 null 的时候跳过，避免抛出异常。
   2. `??=`: 如果 a 为 null，则给 a 赋值 value，否则跳过。这种用默认值兜底的赋值语句在 Dart 中我们可以用 a ??= value 表示。
   3. `??`: 类似oc和swift中的`?:`
   4. 支持运算符重载.