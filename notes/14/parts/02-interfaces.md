# 内置函数式接口

`java.util.function` 提供常用函数式接口；Lambda 的参数和返回类型要匹配目标接口的函数方法。`Comparator` 等函数式接口也存在于其他包中。

| 接口 | 函数方法 | 用途 |
| --- | --- | --- |
| `Consumer<T>` / `BiConsumer<T,U>` | `void accept(T t)` / `void accept(T t, U u)` | 消费一个或两个值，不返回结果 |
| `Predicate<T>` / `BiPredicate<T,U>` | `boolean test(T t)` / `boolean test(T t, U u)` | 测试条件 |
| `Function<T,R>` / `BiFunction<T,U,R>` | `R apply(T t)` / `R apply(T t, U u)` | 计算结果；最后一个类型参数是结果类型 |
| `UnaryOperator<T>` / `BinaryOperator<T>` | `T apply(T t)` / `T apply(T a, T b)` | 参数和结果都是 `T`；分别是 `Function` 和 `BiFunction` 的特殊形式 |
| `Supplier<T>` | `T get()` | 不接收参数，提供结果 |

还有针对基本类型的变体，例如 `IntFunction<R>` 接收 `int` 并返回 `R`。完整列表见 [Java 21 API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/function/package-summary.html)。

## Consumer：执行操作

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

## Predicate：判断与删除

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

## Function：转换值

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

## Supplier：提供值

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
