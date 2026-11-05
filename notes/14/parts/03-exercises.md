# Lambda 练习：传递行为与处理集合

## 将行为传给方法

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

## 数组、视图和可修改列表

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
