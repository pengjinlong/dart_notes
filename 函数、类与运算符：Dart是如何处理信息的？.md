## 函数、类与运算符：Dart是如何处理信息的？

编程语言虽然千差万别，但归根结底，它们的设计思想无非就是回答两个问题：如何表示信息和如何处理信息。

作为一门真正面向对象的编程语言，Dart将处理信息的过程抽象为了对象，以结构化的方式将功能分解，而 函数、类与运算符就是抽象中最重要的手段。

接下来，我就从函数、类与运算符的角度，来进一步和你讲述Dart面向对象设计的基本思路。 

### 函数

在Dart中，所有类型都是对象类型，函数也是对象，它的类型叫作Function。这意味着函数也可以被定义为变量，甚至可以被定义为参数传递给另一个函数。 

在下面这段代码示例中，我定义了一个判断整数是否为0的isZero函数，并把它传递了给另一个printInfo函数，完成格式化打印出判断结果的功能。

```dart
bool isZero(int number) {
    return number = 0;
}
void printInfo(int numbner, Function check) {
    print('$number is Zero: ${check(numner)}');
}
Function f = isZero;
int x = 10;
int y = 0;
printInfo(x, f);
printInfo(y, f);
```

如果函数体只有一行表达式，就比如上面示例中的isZero和printInfo函数，我们还可以像JavaScript语言那样用箭头函数来简化这个函数：

```dart
bool isZero(int number) => number == 0;
```

有时，一个函数中可能需要传递多个参数。那么，如何让这类函数的参数声明变得更加优雅、可维护，同时降低调用者的使用成本呢？

C++与Java的做法是，提供函数的重载，即提供同名但参数不同的函数。**但dart认为重载会导致混乱，因此从设计之初就不支持重载，而是提供了可选命名参数和可选参数**

具体方式是，在声明函数时：

- 给参数增加{}，以paramName: value方式指定调用参数，也就是可选命名参数；
- 给参数增加[]，则意味着这些参数是可以忽略的，也就是可选参数；

在使用这两种方式定义函数时，我们还可以在参数未传递时设置默认值。以一个只有两个参数的简单函数为例：

```dart
// 要达到可选命名参数的⽤法，那就在定义函数的时候给参数加上 {}
void enable1Flags({bool bold, bool hidden}) => print('$bold, $hidden');
// 定义可选命名参数时增加默认值
void enable2Flags({bool bold = true, bool hidden = false}) => print("$bold ,$hidden");
// 可忽略的参数在函数定义时⽤[]符号指定
void enable3Flags(bool bold, [bool hidden]) => print("$bold ,$hidden");
// 定义可忽略参数时增加默认值
void enable4Flags(bool bold, [bool hidden = false]) => print("$bold ,$hidden");
// 可选命名参数函数调⽤
enable1Flags(bold: true, hidden: false); //true, false
enable1Flags(bold: true); //true, null 
enable2Flags(bold: false); //false, false
// 可忽略参数函数调⽤ 
enable3Flags(true, false); //true, false 
enable3Flags(true,); //true, null 
enable4Flags(true); //true, false 
enable4Flags(true,true); // true, true
```

### 类

类是特定类型的数据和方法的集合，也是创建对象的模板。与其他语言一样，Dart为类概念提供了内置支持。

#### 类的定义和初始化

Dart是面向对象的语言，每个对象都是一个类的实例，都继承自顶层类型Object。Dart中并没有public、protected、private这些关键字，我们只要在声明变量与方法时，在前面加上“_”即可作为private方法使用。如果不加“_”，则默认为public。

**不过，‘_’的限制范围并不是类访问级别的，而是库访问级别**

以一个具体的案例看看Dart是如何定义和使用类的。

在Point类中，定义了两个成员变量x和y，通过构造函数语法糖进行初始化，成员函数printInfo的作用是 打印它们的信息；而类变量factor，则在声明时就已经赋好了默认值0，类函数printZValue会打印出它的信息。

```dart
class Point { 
    num x, y; 
    static num factor = 0; 
    //语法糖，等同于在函数体内：this.x = x;this.y = y;
    Point(this.x,this.y); 
    void printInfo() => print('($x, $y)'); 
    static void printZValue() => print('$factor'); }
    var p = new Point(100,200); // new 关键字可以省略 
    p.printInfo(); // 输出(100, 200); 
    Point.factor = 10; 
    Point.printZValue(); // 输出10
}
```

有时候类的实例化需要根据参数提供多种初始化方式。除了可选命名参数和可选参数之外，Dart还提供了**命名构造函数**的方式，使得类的实例化过程语义更清晰。

此外，与与C++类似，**Dart支持初始化列表**。在构造函数的函数体真正执行之前，你还有机会给实例变量赋值，甚至重定向至另一个构造函数。

如下面实例所示，Point类中有两个构造函数Point.bottom与Point，其中：Point.bottom将其成员变量的初 始化重定向到了Point中，而Point则在初始化列表中为z赋上了默认值0

```dart
class Point { 
	num x, y, z;
 	Point(this.x, this.y) : z = 0; // 初始化变量z 
    Point.bottom(num x) : this(x, 0); // 重定向构造函数 
    void printInfo() => print('($x,$y,$z)'); }
	var p = Point.bottom(100); 
	p.printInfo(); // 输出(100,0,0)
}
```

### 复用

在面向对象的编程语言中，将其他类的变量与方法纳入本类中进行复用的方式一般有两种：继承父类和接口 实现。当然，在Dart也不例外。

在Dart中，你可以对同一个父类进行继承或接口实现：

- 继承父类意味着，子类由父类派生，会自动获取父类的成员变量和方法实现，子类可以根据需要覆写构造 函数及父类方法； 
- 接口实现则意味着，子类获取到的仅仅是接口的成员变量符号和方法符号，需要重新实现成员变量，以及 方法的声明和初始化，否则编译器会报错。

接下来，以一个例子说明**Dart中继承和接口的差别**

Vector通过继承Point的方式增加了成员变量，并覆写了printInfo的实现；而Coordinate，则通过接口实现 的方式，覆写了Point的变量定义及函数实现：

```dart
class Point { 
    num x = 0, y = 0; 
    void printInfo() => print('($x,$y)'); 
}
//Vector继承⾃Point 
class Vector extends Point{ 
    num z = 0; 
    @override void printInfo() => print('($x,$y,$z)'); // 覆写了printInfo实现
}
//Coordinate是对Point的接⼝实现 
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

可以看出，子类Coordinate采用接口实现的方式，仅仅是获取到了父类Point的一个“空壳子”，只能从语 义层面当成接口Point来用，但并不能复用Point的原有实现。**那么，我们是否能够找到方法去复用Point的对应方法呢？**

也许你很快就想到了，我可以让Coordinate继承Point，来复用其对应的方法。但，如果Coordinate还有其 他的父类，我们又该如何处理呢？

其实，**出了继承和接口实现之外，Dart还提供了另一种机制来实现类的复用， 即“混入”（MIXin）**。混入鼓励代码重用，可以被视为具有实现方法的接口。这样一来，不仅可以解决Dart缺少对多重继承的支持问 题，还能够避免由于多重继承可能导致的歧义（菱形问题）。

>备注：继承歧义，也叫菱形问题，是支持多继承的编程语言中一个相当棘手的问题。当B类和 C类继承自A类，而D类继承自B类和C类时会产生歧义。如果A中有一个方法在B和C中已经覆 写，而D没有覆写它，那么D继承的方法的版本是B类，还是C类的呢？

**要使用Mixin，只需要with关键字即可**。我们来试着改造Coordinate的实现，把类中的变量声明和函数实现全部删掉： 

```dart
class Coordinate with Point { }
var yyy = Coordinate(); 
print (yyy is Point); //true 
print(yyy is Coordinate); //true
```

### 运算符

Dart和绝大部分编程语言的运算符一样，所以你可以用熟悉的方式去执行程序代码运算。**不过，Dart多了几个额外的运算符，用于简化处理变量实例null的情况**

- **？.运算符：** 假设Point类有printInfo()方法，p是Point的一个可能为null的实例。那么，p调用成员方法的安全代码，可以简化为p?.printInfo() ，表示p为null的时候跳过，避免抛出异常；
- **??=运算符：** 如果a为null，则给a赋值value，否则跳过。这种用默认值兜底的赋值语句在Dart中我们可以用a ??= value表示。 
- **??运算符：** 如果a不为null，返回a的值，否则返回b。在Java或者C++中，我们需要通过三元表达式(a != null)? a : b来实现这种情况。而在Dart中，这类代码可以简化为a ?? b。

**在Dar中中，一切都是对象，就连运算符也是对象成员函数的一部分**

