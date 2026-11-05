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
