# Lambda表达式

Lambda表达式，也可称为闭包，其允许把函数作为一个方法的参数（即函数作为参数传递进方法之中）

## 语法

~~~java
(parameters) -> expression
或者
(parameters) -> {statements;}
~~~

**特征**

* **不需要声明类型：**不需要声明参数类型，编译器可以统一识别参数值
* **可选的参数：**一个参数无需括号，多个参数需要
* **可选的方法体：**若主体只包含一个语句，则不需要使用大括号
* **可选的返回值：**若主体只有一个表达式，则编译器会自动编译返回值，大括号需要指定表达式返回了一个数值

## 实例

~~~java
//	不需要变量，返回值为5
() -> 5

//	接受一个数字，返回值为其三倍
x -> 3 * x

//	接收两个数字，返回值为其和
(x, y) -> x + y

//	接收两个int类型的参数，返回其和
(int x, int y) -> x + y

//	接收一个String对象，并打印出来，没有返回值
(String string) -> System.out.print(string)
~~~

*测试以下代码*

~~~java
public class JavaCodeTest {
    public static void main(String[] args) {
        JavaCodeTest test = new JavaCodeTest();

        //  声明类型
        MathOperation type = (int a, int b) -> a + b;

        //  不声明类型
        MathOperation NoType = (a, b) -> a + b;

        //  带大括号返回语句
        MathOperation braces = (int a, int b) -> {return a + b;};

        //  不带大括号返回语句
        MathOperation NoBraces = (int a, int b) -> a + b;

        System.out.println("2 + 1 = " + test.testCode(2, 1, type));
        System.out.println("2 + 1 = " + test.testCode(2, 1, NoType));
        System.out.println("2 + 1 = " + test.testCode(2, 1, braces));
        System.out.println("2 + 1 = " + test.testCode(2, 1, NoBraces));

        //  参数不使用括号
        Greeting greeting1 = message -> System.out.print("say " + message);
        
        //  参数使用括号
        Greeting greeting2 = (message) -> System.out.print("Hi " + message); 
        
        greeting1.sayMessage("world");
        greeting2.sayMessage("w o r l d");
    }

    interface MathOperation {
        int operation(int a, int b);
    }
    
    interface Greeting {
        void sayMessage(String message);
    }

    private int testCode(int a, int b, MathOperation mathOperation) {
        return mathOperation.operation(a, b);
    }
}

~~~

*测试结果为*

~~~java
2 + 1 = 3
2 + 1 = 3
2 + 1 = 3
2 + 1 = 3
say world
Hi w o r l d
~~~

**注意事项**

- lambda表达式主要用来定义行内执行的方法类型接口，即一个简单方法接口。例如上文中的``MathOperation`接口的方法，`sayMessage`方法。
- lambda表达式解决了使用匿名方法的烦恼

## 变量作用域

lambda表达式只能引用被标记了final的外层局部变量，因此，lambda表达式不支持内部修改定义再外部的局部变量

*测试以下代码*

~~~java
public class JavaCodeTest {

    final static String string = "say ";

    public static void main(String[] args) {
        Greeting greeting = message -> System.out.println(string + message);
        greeting.sayMessage("hi");
    }

    interface Greeting {
        void sayMessage(String message);
    }
}

~~~

*测试结果为*

~~~java
say hi
~~~

*测试在lambda表达式中访问外层的局部变量*

```java
public class JavaCodeTest {
    public static void main(String[] args) {
        final int num = 2;
        Calculate<Integer, String> s = param ->
                System.out.println(String.valueOf(param + num));
        s.calculate(3); //  计算结果为 5
    }

    interface Calculate<T1, T2> {
        void calculate(int i);
    }
}
```

**lambda表达式的局部变量语句隐藏的final定义，即可以不声明为final，但是不可被后续的代码修改**

**在lambda表达式中不允许声明与局部变量同名的参数或者局部变量**



