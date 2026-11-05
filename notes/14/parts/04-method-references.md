# 方法引用与接收者

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

静态方法引用用类型名，例如 `Integer::sum`；`System.out` 是 `PrintStream` 对象的字段，`println` 是该对象的实例方法。引用语法不在方法名后写参数列表。具体规则见 [Java 语言规范 §15.13](https://docs.oracle.com/javase/specs/jls/se21/html/jls-15.html#jls-15.13)。

## 参数与接收者

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

## 练习：依次转换数组元素

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
