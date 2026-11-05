# Lambda 与函数式接口基础

Lambda（Java 8 起）把一段行为交给需要函数式接口的代码，例如把排序规则交给 `List.sort`。它可以用更短的写法表达匿名类中的单个函数行为，但不是匿名类的简单文本替换。

## 从匿名比较器到 Lambda

```java
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;

List<String> names = new ArrayList<>(List.of("Lin", "Ada", "Bo"));
Comparator<String> byLength = new Comparator<String>() {
    @Override public int compare(String a, String b) {
        return Integer.compare(a.length(), b.length());
    }
};
names.sort(byLength);
names.sort((a, b) -> Integer.compare(a.length(), b.length()));
System.out.println(names); // [Bo, Lin, Ada]
```

`sort` 的参数提供目标类型 `Comparator<String>`；编译器据此知道 `a`、`b` 是字符串，表达式的结果对应 `compare` 的 `int` 返回值。比较结果为负、零、正分别表示前者排前、排序相等、前者排后；列表排序稳定，比较结果相等的元素保留原有相对顺序。

函数式接口有一个逻辑上的抽象方法（SAM）；继承的抽象方法也要计入，同签名的覆盖和与 `Object` 公有方法对应的声明有特殊规则。`default` 和 `static` 方法不增加抽象方法数。`@FunctionalInterface` 不是必需的，但能让编译器检查声明是否满足要求。

```java
@FunctionalInterface
interface Operation<T> {
    T operate(T a, T b);
    default String description() { return "operation"; }
}

// 继承 compare 又增加 second，因此不是函数式接口；不能用于 Lambda。
interface Enhanced<T> extends Comparator<T> {
    int second(T a, T b);
}
```

匿名类还可以实现多个抽象方法或继承类；Lambda 的 `this` 指向外围对象，匿名类的 `this` 指向匿名类对象。参见[第 13 模块匿名类](../../13/README.md#匿名类与比较器)。

## 参数和方法体

Lambda 的形式是参数、`->`、表达式或代码块。参数及结果须与目标函数方法兼容：

```java
import java.util.function.Consumer;

Consumer<String> a = s -> System.out.println(s);           // 一个推断类型的参数可省括号
Consumer<String> b = (String s) -> System.out.println(s);  // 写类型时必须带括号
Consumer<String> c = (var s) -> { System.out.println(s); }; // var 也必须带括号
Runnable task = () -> System.out.println("go");           // 零参数须带括号
Operation<Integer> add = (x, y) -> x + y;                // 多参数须带括号
Operation<Integer> add2 = (var x, var y) -> { return x + y; };
// Operation<Integer> bad = (Integer x, var y) -> x + y; // 不能混用类型形式
```

多参数要么全部省略类型，要么全部显式声明类型，要么全部使用 `var`；Lambda 参数使用 `var` 从 Java 11 起支持。表达式体不写 `return`；有返回值的代码块必须在每条正常完成的路径返回兼容的值。`void` 函数方法可使用语句表达式或不返回值的代码块，不需要返回值。

```java
Operation<Integer> sum = (x, y) -> { return x + y; };
// Operation<Integer> bad = (x, y) -> { int z = x + y; }; // 缺少返回值
// Operation<Integer> bad = (x, y) -> System.out.println(x + y); // println 返回 void
```

目标类型也可来自赋值或方法参数；单独的 Lambda 没有可供 `var` 推断的目标类型。例如 `var f = (String s) -> s.length();` 编译失败。Lambda 参数不能与外围方法的局部变量或参数同名；Lambda 块内声明的变量在块外不可见。

## 复用自定义操作

```java
static <T> T calculate(Operation<T> op, T a, T b) {
    return op.operate(a, b);
}

int total = calculate((a, b) -> a + b, 5, 2);       // 7
Double quotient = calculate((a, b) -> a / b, 10.0, 2.5); // 4.0
String joined = calculate((a, b) -> a.toUpperCase() + b.toUpperCase(), "a", "b"); // AB
```

同一个方法接收不同操作；泛型使用包装类型（这里分别为 `Integer`、`Double`），基本类型参数可自动装箱，赋给 `int` 时拆箱。结果也必须与推断出的目标返回类型兼容。

Lambda 可以捕获外围的局部变量，但该变量必须已赋值且是 `final` 或 effectively final。修改所引用对象的状态不等于重赋值引用：

```java
StringBuilder prefix = new StringBuilder("Hi ");
Consumer<String> say = s -> System.out.println(prefix + s);
prefix.append("!");
say.accept("Ada"); // Hi !Ada
// prefix = new StringBuilder(); // 重赋值后 prefix 不再 effectively final，捕获处不能编译
```

`Consumer<T>` 的函数方法 `accept(T)` 接收一个值且返回 `void`，所以适合 `forEach` 这样的逐项操作。流（stream）的许多操作也接受函数式接口，能将操作串成处理流水线；本主题只涉及其用途，不展开流 API。

规则参见 [Java 语言规范：函数式接口](https://docs.oracle.com/javase/specs/jls/se21/html/jls-9.html#jls-9.8)、[Lambda 表达式](https://docs.oracle.com/javase/specs/jls/se21/html/jls-15.html#jls-15.27) 和 [List.sort](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/List.html#sort(java.util.Comparator))。
