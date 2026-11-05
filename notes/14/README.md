# 第 14 模块：Lambda、函数式接口与方法引用

面向学后复习：从 Lambda 和目标类型出发，了解内置接口、练习、方法引用及组合。资料核对与验证见[覆盖记录](../../docs/java-se-notes/14-coverage.md)。

## Lambda 与函数式接口基础

Lambda（Java 8 起）把一段行为交给需要函数式接口的代码，例如把排序规则交给 `List.sort`。它可以用更短的写法表达匿名类中的单个函数行为，但不是匿名类的简单文本替换。

### 从匿名比较器到 Lambda

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

匿名类还可以实现多个抽象方法或继承类；Lambda 的 `this` 指向外围对象，匿名类的 `this` 指向匿名类对象：

```java
class ThisExample {
    void show() {
        Runnable lambda = () -> System.out.println((Object) this == ThisExample.this);
        Runnable anonymous = new Runnable() {
            public void run() {
                System.out.println((Object) this == ThisExample.this);
            }
        };
        lambda.run();    // true：this 是调用 show 的外围对象
        anonymous.run(); // false：this 是匿名 Runnable 对象
    }

    public static void main(String[] args) {
        new ThisExample().show();
    }
}
```

参见[第 13 模块匿名类](../13/README.md#匿名类与比较器)。

### 参数和方法体

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

目标类型也可来自赋值或方法参数；单独的 Lambda 没有可供 `var` 推断的目标类型。例如 `var f = (String s) -> s.length();` 编译失败。Lambda 参数不能与外围方法的局部变量或参数同名；Lambda 块内声明的变量在块外不可见：

```java
import java.util.function.Function;

class ScopeExample {
    public static void main(String[] args) {
        int offset = 2;
        // Function<Integer, Integer> bad = offset -> offset + 1; // 参数名与局部变量冲突
        Function<Integer, Integer> addOffset = value -> {
            int result = value + offset;
            return result;
        };
        System.out.println(addOffset.apply(3)); // 5
        // System.out.println(result); // result 只在 Lambda 块内可见
    }
}
```

### 复用自定义操作

```java
// 使用前面声明的 Operation 接口
class CalculateExample {
    static <T> T calculate(Operation<T> op, T a, T b) {
        return op.operate(a, b);
    }

    public static void main(String[] args) {
        int total = calculate((a, b) -> a + b, 5, 2);
        Double quotient = calculate((a, b) -> a / b, 10.0, 2.5);
        String joined = calculate((a, b) -> a.toUpperCase() + b.toUpperCase(), "a", "b");
        System.out.println(total + ", " + quotient + ", " + joined); // 7, 4.0, AB
    }
}
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

## 内置函数式接口

`java.util.function` 提供常用函数式接口；Lambda 的参数和返回类型要匹配目标接口的函数方法。`Comparator` 等函数式接口也存在于其他包中。

| 接口 | 函数方法 | 用途 |
| --- | --- | --- |
| `Consumer<T>` / `BiConsumer<T,U>` | `void accept(T t)` / `void accept(T t, U u)` | 消费一个或两个值，不返回结果 |
| `Predicate<T>` / `BiPredicate<T,U>` | `boolean test(T t)` / `boolean test(T t, U u)` | 测试条件 |
| `Function<T,R>` / `BiFunction<T,U,R>` | `R apply(T t)` / `R apply(T t, U u)` | 计算结果；最后一个类型参数是结果类型 |
| `UnaryOperator<T>` / `BinaryOperator<T>` | `T apply(T t)` / `T apply(T a, T b)` | 参数和结果都是 `T`；分别是 `Function` 和 `BiFunction` 的特殊形式 |
| `Supplier<T>` | `T get()` | 不接收参数，提供结果 |

还有针对基本类型的变体，例如 `IntFunction<R>` 接收 `int` 并返回 `R`。完整列表见 [Java 21 API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/function/package-summary.html)。

### Consumer：执行操作

`forEach` 对每个元素调用 Consumer。声明 Lambda 只提供行为，直到调用函数方法或交给其他方法调用时才执行：

```java
import java.util.List;
import java.util.function.BiConsumer;

class ConsumerExample {
    static <T> void processPoint(T first, T second, BiConsumer<T, T> action) {
        action.accept(first, second);
    }

    public static void main(String[] args) {
        BiConsumer<Double, Double> print = (lat, lon) ->
                System.out.println(lat + ", " + lon);
        List<double[]> points = List.of(new double[]{1.0, 2.0}, new double[]{3.0, 4.0});
        points.forEach(p -> processPoint(p[0], p[1], print));
        // 依次输出 1.0, 2.0 和 3.0, 4.0
    }
}
```

`BiConsumer<T,U>` 的两个参数类型不必相同；示例用 `T,T` 要求相同类型。`p` 是数组，`p[0]` 和 `p[1]` 自动装箱为 `Double`。也可把 `print` 的 Lambda 直接写进 `processPoint` 调用，但嵌套过深会降低可读性。

### Predicate：判断与删除

`test` 返回 `boolean`；`removeIf` 删除使条件为 `true` 的元素，可能删除零个或多个。它修改原集合，因此需要支持删除的集合：

```java
import java.util.ArrayList;
import java.util.List;
import java.util.function.BiPredicate;

class PredicateExample {
    public static void main(String[] args) {
        BiPredicate<String, String> same = (a, b) -> a.equalsIgnoreCase(b);
        System.out.println(same.test("Hello", "hello")); // true
        List<String> words = new ArrayList<>(List.of("Alpha", "bravo", "echo", "easy", "earnest"));
        words.removeIf(s -> s.equalsIgnoreCase("BRAVO"));
        words.removeIf(s -> s.startsWith("ea"));
        System.out.println(words); // [Alpha, echo]
    }
}
```

`Collection.removeIf` 的默认实现使用迭代器的 `remove`；具体集合可以覆盖实现。不可修改的集合不支持这种删除，参见 [Collection API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Collection.html#removeIf(java.util.function.Predicate))。

### Function：转换值

`Function` 可以改变类型，`UnaryOperator` 则保持输入和输出类型相同；`BinaryOperator` 接收两个同类型值并返回该类型值。

```java
import java.util.Arrays;
import java.util.function.BinaryOperator;
import java.util.function.Function;

class FunctionExample {
    public static void main(String[] args) {
        Function<String, String[]> split = s -> s.split(",");
        System.out.println(Arrays.toString(split.apply("a,b"))); // [a, b]
        BinaryOperator<Integer> add = (a, b) -> a + b;
        System.out.println(add.apply(2, 3)); // 5
    }
}
```

`List.replaceAll` 把每个原元素交给 `UnaryOperator`，用结果替换该位置；列表必须支持替换。`Arrays.setAll` 则把每个**下标**交给生成函数，而不是传入原值：

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Locale;

class ReplaceExample {
    public static void main(String[] args) {
        List<String> words = new ArrayList<>(List.of("alpha", "echo"));
        words.replaceAll(s -> s.charAt(0) + " - " + s.toUpperCase(Locale.ROOT));
        System.out.println(words); // [a - ALPHA, e - ECHO]

        String[] labels = new String[3]; // 初始元素为 null
        Arrays.fill(labels, "");        // 每个元素使用同一个指定值
        Arrays.setAll(labels, i -> (i + 1) + ". " + switch (i) {
            case 0 -> "one";
            case 1 -> "two";
            default -> "other";
        });
        System.out.println(Arrays.toString(labels)); // [1. one, 2. two, 3. other]
    }
}
```

这里的 `setAll` 对对象数组使用 `IntFunction`；也有基本类型数组的重载。`switch` 表达式是 Lambda 的返回表达式的一部分。`replaceAll` 的默认实现使用 `ListIterator.set`，具体列表可以覆盖它。参见 [List API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/List.html#replaceAll(java.util.function.UnaryOperator)) 和 [Arrays API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Arrays.html#setAll(T%5B%5D,java.util.function.IntFunction))。

### Supplier：提供值

`get()` 没有参数；每次调用可以计算一个值，但不保证返回新对象或不同结果。下面用捕获的 `Random` 生成数组下标，范围为 `0`（含）至长度（不含）：

```java
import java.util.Arrays;
import java.util.Random;
import java.util.function.Supplier;

class SupplierExample {
    static String[] select(int count, String[] values, Supplier<Integer> index) {
        String[] result = new String[count];
        Arrays.setAll(result, i -> values[index.get()]);
        return result;
    }

    public static void main(String[] args) {
        String[] names = {"Ann", "Bob"};
        Random random = new Random(); // 引用 effectively final，Random 内部状态可以变化
        Supplier<Integer> index = () -> random.nextInt(names.length);
        String[] selected = select(3, names, index);
        System.out.println(selected.length); // 3；名字可重复
    }
}
```

调用者须保证 `values` 非空且 Supplier 产生有效下标。无参数 Lambda 的 `()` 不可省略；参数声明与变量捕获的完整规则见本模块的 Lambda 基础部分。[Supplier API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/function/Supplier.html) 明确不要求每次提供新值。

## Lambda 练习：传递行为与处理集合

### 将行为传给方法

`Consumer<String>` 接收字符串，不返回结果；可以把匿名类的 `accept` 方法体改成 Lambda。这里外层 Consumer 拆分句子，`forEach` 接收的另一个 Consumer 逐个打印：

```java
import java.util.Arrays;
import java.util.function.Consumer;

Consumer<String> words = sentence ->
        Arrays.asList(sentence.split(" ")).forEach(word -> System.out.println(word));
words.accept("Java is fun"); // 每行依次打印 Java、is、fun
```

`Function<String, String>` 接收并返回字符串；当输入和输出类型相同时，也可用它的子接口 `UnaryOperator<String>`。方法可接收函数作为参数，调用者决定具体行为：

```java
import java.util.function.Function;
import java.util.function.UnaryOperator;

class TransformExample {
    static String transform(Function<String, String> action, String source) {
        return action.apply(source);
    }

    public static void main(String[] args) {
        UnaryOperator<String> everySecond = source -> {
            StringBuilder result = new StringBuilder();
            for (int i = 1; i < source.length(); i += 2) {
                result.append(source.charAt(i));
            }
            return result.toString();
        };
        System.out.println(everySecond.apply("1234567890"));           // 24680
        System.out.println(transform(everySecond, "1234567890"));     // 24680
        System.out.println(transform(s -> s.toUpperCase(), "java"));  // JAVA
    }
}
```

这个例子按 `char` 下标取值，适用于例中的数字；它不保证按 Unicode 字符或用户感知的字符切分。带花括号的代码块要用 `return` 返回结果；表达式体不写 `return`。Lambda 创建后，其主体在调用函数式方法时执行，例如 `Supplier` 的 `get()`：

```java
import java.util.function.Supplier;

Supplier<String> message = () -> "I love Java";
Supplier<String> same = () -> { return "I love Java"; };
System.out.println(message.get()); // I love Java
```

### 数组、视图和可修改列表

`Arrays.setAll` 的生成函数接收**索引**，将返回值写入对应数组位置。`Arrays.asList(array)` 返回由数组支持的定长列表：列表的 `set`、`replaceAll` 和数组元素修改互相可见，但不能通过它增删元素。`replaceAll` 的操作接收的是**元素**，不是索引。

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Random;

class NamesExample {
    public static void main(String[] args) {
        String[] names = {"Anna", "Bob", "Ed"};
        Arrays.setAll(names, i -> names[i].toUpperCase());
        System.out.println(Arrays.toString(names)); // [ANNA, BOB, ED]

        List<String> view = Arrays.asList(names);
        Random random = new Random(7); // 固定种子便于复现；实际使用可不指定
        view.replaceAll(name -> name + " " + (char) random.nextInt('A', 'D' + 1) + ".");
        view.replaceAll(name -> {
            String first = name.split(" ")[0];
            return name + " " + new StringBuilder(first).reverse();
        });
        view.forEach(name -> System.out.println(name)); // 逐行打印，names 也已改变

        List<String> copy = new ArrayList<>(view);
        copy.removeIf(name -> {
            String[] parts = name.split(" ");
            return parts[0].equals(parts[parts.length - 1]);
        });
        System.out.println(copy); // 只剩 ED（及其随机首字母、姓 DE）
        System.out.println(names.length); // 3：从副本删除不改变原数组
    }
}
```

这里的字符串格式固定为“名 首字母. 姓”；通用姓名解析不能依赖这个假设。`nextInt(origin, bound)` 包含下界、不包含上界，故用 `'D' + 1` 包含 D；该双边界形式从 Java 17 起可用。`removeIf` 接收 Predicate，为 `true` 的元素会被删除；若在上述定长视图上有匹配项，会抛出 `UnsupportedOperationException`。`new ArrayList<>(view)` 创建独立的列表结构，允许增删；元素引用本身不会被深拷贝。

## 方法引用与接收者

方法引用用 `::` 引用已有方法或构造器，仍需要函数式接口作为目标类型。编译器根据目标方法的参数和返回类型选择兼容的重载；不是任何 Lambda 都能替换为方法引用。

| 形式 | 方法引用 | 对应的 Lambda（在表中给定的目标类型下） |
| --- | --- | --- |
| 静态方法 | `Integer::sum` | `(a, b) -> Integer.sum(a, b)` |
| 已绑定对象的实例方法 | `System.out::println` | `s -> System.out.println(s)` |
| 未绑定对象的实例方法 | `String::concat` | `(a, b) -> a.concat(b)` |
| 构造器 | `StringBuilder::new` | `() -> new StringBuilder()` |

例如：

```java
import java.util.function.*;

BinaryOperator<Integer> sum = Integer::sum;
Consumer<String> print = System.out::println;
BinaryOperator<String> join = String::concat;
Supplier<StringBuilder> create = StringBuilder::new;
System.out.println(sum.apply(10, 25)); // 35
print.accept(join.apply("Hello ", "World")); // Hello World
StringBuilder builder = create.get(); // 此时才调用构造器
```

引用本身不会调用所引用的方法；调用目标接口的方法时才执行。但已绑定引用的左侧表达式在**创建引用时**求值并保存接收者，不一定与稍后求值左侧表达式的 Lambda 等价：

```java
var out = System.out;
Consumer<String> print = out::println; // 现在确定 out 指向的对象
// 若 out 为 null，此处就抛 NullPointerException
```

静态方法引用用类型名，例如 `Integer::sum`；`System.out` 是 `System` 的静态字段，其值是 `PrintStream` 对象，`println` 是该对象的实例方法。引用语法不在方法名后写参数列表。具体规则见 [Java 语言规范 §15.13](https://docs.oracle.com/javase/specs/jls/se21/html/jls-15.html#jls-15.13)。

### 参数与接收者

已绑定引用的对象来自表达式外部，目标方法的参数传给该对象的方法；未绑定引用使用类型名，目标方法的**第一个参数**是接收者，其余参数传给实例方法。因此 `String::concat` 不是静态方法：

```java
BiFunction<String, String, String> join = String::concat;
System.out.println(join.apply("Java", " SE")); // Java SE，等同 "Java".concat(" SE")
UnaryOperator<String> upper = String::toUpperCase;
System.out.println(upper.apply("hello")); // HELLO
// UnaryOperator<String> bad = String::concat; // 错误：接收者占用唯一参数，concat 还需要一个参数
```

`Supplier` 没有参数，不能为未绑定实例方法提供接收者；它可以用于无参构造器或已绑定对象的无参方法。目标类型还须允许方法结果：`String::isEmpty` 可用于 `Predicate<String>`，`String::length` 可用于 `Function<String, Integer>`（结果装箱），`List::clear` 可用于 `Consumer<List<String>>`。返回值也可被 `void` 目标丢弃，例如 `BiConsumer<List<String>, String> add = List::add`。

`String.transform` 将当前字符串交给 `Function` 并返回函数结果，结果不必是字符串：

```java
String text = "Hello".transform(String::toUpperCase); // HELLO
boolean empty = text.transform(String::isEmpty);       // false
```

`transform` 自 Java 12 可用；见 [String API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/String.html#transform(java.util.function.Function))。

### 练习：依次转换数组元素

练习把多个 `UnaryOperator<String>` 按顺序应用到姓名数组。下面保留大写和追加反转名字两个步骤：

```java
import java.util.Arrays;
import java.util.List;
import java.util.function.UnaryOperator;

class Names {
    static String reverse(String s) {
        return new StringBuilder(s).reverse().toString();
    }

    public static void main(String[] args) {
        String[] names = {"Ada", "Bo"};
        List<UnaryOperator<String>> steps = List.of(
                String::toUpperCase,
                s -> s + " " + reverse(s));
        List<String> view = Arrays.asList(names);
        for (UnaryOperator<String> step : steps) {
            view.replaceAll(name -> name.transform(step));
        }
        System.out.println(Arrays.toString(names)); // [ADA ADA, BO OB]
    }
}
```

每一步处理全部名字，下一步读取上一步的结果。字符串不可变，`replaceAll` 将返回的新字符串写回列表；`Arrays.asList` 返回由原数组支持的固定大小视图，所以数组元素也改变，但不能通过该列表添加或删除元素。`new ArrayList<>(Arrays.asList(names))` 则是独立列表，替换其元素不会写回数组。见 [Arrays API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Arrays.html#asList(T...))。

课程还用随机字母添加中间名、`String::new`、静态反转方法和已绑定的对象方法尝试不同形式。重载构造器依目标类型选取；例如 `Function<String, String> copy = String::new` 使用接受字符串的构造器。需要额外加工或固定其他参数时，可保留 Lambda。

## 函数式接口的组合

组合方法返回新的函数式接口对象，调用其抽象方法时才执行链中的操作。

### Function：传递上一步的结果

`f.andThen(g)` 先执行 `f` 再执行 `g`；`f.compose(g)` 先执行 `g` 再执行 `f`。相邻步骤的输出和输入类型必须兼容，整个链的输入和输出由目标 `Function` 类型约束。

```java
import java.util.function.Function;

Function<String, String> upper = String::toUpperCase;
Function<String, String> addLast = s -> s + " Li";
System.out.println(upper.andThen(addLast).apply("Ada")); // ADA Li
System.out.println(upper.compose(addLast).apply("Ada")); // ADA LI

Function<String, Integer> length = upper.andThen(s -> s.split(" "))
        .andThen(parts -> String.join(", ", parts))
        .andThen(String::length);
System.out.println(length.apply("Ada Li")); // 7
```

这里中间类型依次是 `String`、`String[]`、`String`，最终结果是 `Integer`。如果下一步不能接收上一步的结果，就无法编译。

`Function` 有 `andThen`、`compose`，`UnaryOperator` 继承这两个方法；`BiFunction` 和 `BinaryOperator` 有 `andThen`。例如 `IntFunction`、`LongFunction`、`DoubleFunction` 没有这些组合方法；不同的原始类型接口提供的方法不完全相同，使用时查对应 API。

### Consumer：同一个输入依次执行操作

`Consumer.andThen` 不传递返回值：两个操作依次接收同一个输入。如果前一个操作抛出异常，后一个不会执行。输入可以是可变对象，前一步对对象的修改可能影响后一步。

```java
import java.util.function.Consumer;

Consumer<String> initial = s -> System.out.print(s.charAt(0));
Consumer<String> full = System.out::println;
initial.andThen(s -> System.out.print("-"))
       .andThen(full).accept("Ada"); // A-Ada
```

### Predicate：组合条件

`and`、`or`、`negate` 返回新的判断；`and` 和 `or` 短路：前者左侧为 `false`、后者左侧为 `true` 时，不执行右侧判断。

```java
import java.util.ArrayList;
import java.util.List;
import java.util.function.Predicate;

Predicate<String> startsA = s -> s.startsWith("A");
Predicate<String> endsA = s -> s.endsWith("a");
System.out.println(startsA.and(endsA).test("Ada")); // true
System.out.println(startsA.or(endsA).test("Mia"));  // true
System.out.println(startsA.and(endsA).negate().test("Ada")); // false

List<String> names = new ArrayList<>(List.of("Ada", "Bo", "Ann"));
names.removeIf(startsA); // 删除满足条件的元素
System.out.println(names); // [Bo]
```

### Comparator：按键排序和追加条件

`Comparator.comparing` 从对象提取排序键，按键的自然顺序比较；`thenComparing` 仅在前一比较结果为零时使用下一规则。它既可接收另一个比较器，也可接收键提取函数。`reversed()` 反转调用它时已有的整个比较规则。

```java
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;

record Person(String first, String last) { }
List<Person> people = new ArrayList<>(List.of(
        new Person("Minnie", "Mouse"), new Person("Ada", "Li"),
        new Person("Mickey", "Mouse")));
Comparator<Person> order = Comparator.comparing(Person::last)
        .thenComparing(Person::first);
people.sort(order);
System.out.println(people); // [Person[first=Ada, last=Li], Person[first=Mickey, last=Mouse], Person[first=Minnie, last=Mouse]]
people.sort(order.reversed()); // 姓和名的组合顺序一起反转
System.out.println(people.getFirst()); // Person[first=Minnie, last=Mouse]
```

等价的第一层比较器可写作 `(a, b) -> a.last().compareTo(b.last())`。比较器结果的负数、零、正数分别表示前者排前、两者在该规则下相等、前者排后。`Comparator.naturalOrder()` 使用元素的自然顺序，`reverseOrder()` 使用其反序；它们要求元素支持自然比较。对不可修改的列表调用 `sort` 会失败，因此例子用可修改的 `ArrayList`。

详见 [Function](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/function/Function.html)、[Consumer](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/function/Consumer.html)、[Predicate](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/function/Predicate.html) 和 [Comparator](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Comparator.html) API。
