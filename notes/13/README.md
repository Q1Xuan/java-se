# 第 13 模块：嵌套类

本页覆盖静态嵌套类、成员内部类、局部类和匿名类；全章整合与样稿风格待确认。

## 四种形式

嵌套类是在另一个类或接口中声明的类。内部类是没有显式或隐式声明为 `static` 的嵌套类；“内部类”不等于“成员内部类”。

| 形式 | 位置或特点 |
| --- | --- |
| 静态嵌套类 | 在类体中声明为 `static`，创建时不需要外部类对象。 |
| 成员内部类 | 在类体中声明的非静态成员类，实例与外部类实例关联。 |
| 局部类 | 在代码块中声明的具名类，作用范围受所在块限制。 |
| 匿名类 | 没有名字，在表达式中声明并创建实例。 |

局部类和匿名类也是内部类；某些局部类型（如局部 `record` 和 `enum`）隐式为静态，不属于内部类。类、接口、枚举和记录都可以作为嵌套类型。

## 静态嵌套类

- 在外部类的类体中用 `static class` 声明；在外部使用时可用 `外部类.嵌套类` 指定类型，仍需遵守访问权限。
- 嵌套类可以直接访问外部类的静态成员，包括私有成员。它没有自动关联的外部类实例；访问外部实例成员时，需要提供外部类对象。
- 外部类也可以访问嵌套类的私有成员；访问实例成员同样需要相应的嵌套类对象。

```java
class Employee {
    private static String company = "Acme";
    private int year;

    Employee(int year) { this.year = year; }

    static class Inspector {
        private int marker = 1;

        String company() { return Employee.company; }
        int yearOf(Employee employee) { return employee.year; }
        // int year() { return year; } // 无外部对象，不能编译
    }

    int markerOf(Inspector inspector) { return inspector.marker; }
}

class Example {
    public static void main(String[] args) {
        Employee.Inspector inspector = new Employee.Inspector();
        Employee employee = new Employee(2020);
        System.out.println(inspector.company());
        System.out.println(inspector.yearOf(employee));
    }
}
```

例如，把比较器放进员工类后，它可以通过传入的员工对象读取私有字段，而不必为排序专门增加 getter。比较器的其他写法见下文。

Java 16 起内部类可以声明静态成员和静态初始化器；此前内部类声明静态成员有更严格的限制。详见 [Java 语言规范 §8.1.3](https://docs.oracle.com/javase/specs/jls/se16/html/jls-8.html#jls-8.1.3)。

## 成员内部类

- 成员内部类是在外部类的类体中声明的非静态类，可以使用 `public`、`protected`、`private` 或包访问权限。
- 每个成员内部类对象关联一个外部类对象。在外部创建时，先有外部对象，再写 `外部对象.new 内部类()`；类型名写作 `外部类.内部类`，并且要满足访问权限。
- 在外部类的实例上下文中，`new 内部类()` 默认关联当前外部对象；静态上下文没有当前外部对象，必须提供一个。
- 内部类的实例代码可以直接访问关联的外部对象的成员，包括私有成员；外部类也能通过内部类对象访问其私有成员。静态代码不能直接使用实例成员，须有相应对象。
- 同名时，`this.成员` 指内部类对象的成员，`外部类.this.成员` 指关联的外部对象的成员；未限定的名称受当前作用域及遮蔽规则影响。

```java
class Meal {
    private int price = 5;

    class Item {
        private int price = 2;

        int total() { return this.price + Meal.this.price; }
    }

    Item createItem() { return new Item(); }
    int itemPrice(Item item) { return item.price; }
}

class InnerExample {
    public static void main(String[] args) {
        Meal meal = new Meal();
        Meal.Item first = meal.new Item();
        Meal.Item second = meal.createItem();
        System.out.println(first.total());       // 7
        System.out.println(meal.itemPrice(second)); // 2
        // Meal.Item bad = new Meal.Item(); // 没有外部对象，不能编译
    }
}
```

汉堡练习用私有 `Item` 表示套餐组成，用 `Burger` 继承 `Item`，并由 `Meal` 把添加配料的操作交给 `Burger`。嵌套关系允许访问私有成员，但不会改变继承规则：父类的私有字段不会作为子类成员被继承。配料枚举及其私有方法展示了嵌套类型的封装；内部类静态成员的版本限制见上文。

播放列表练习用 `SongList` 封装歌曲集合和查找操作，再由 `Album` 调用它。练习的在线评测明确要求将 `SongList` 声明为 `public static`；这时它是静态嵌套类，不需要外部 `Album` 对象。该声明是评测的特殊要求，不是 Java 对成员内部类的一般要求；在普通代码中可根据是否需要关联外部对象及访问范围选择声明。

## 局部类与局部变量

- 局部类是在代码块中声明的具名类，常见于方法、构造器或初始化块。类名从声明处起在所在块内可用，包括嵌套块；块外不能用这个名字。对象仍可能通过其他类型的引用在块结束后继续使用。
- 局部类不能声明为 `public`、`protected`、`private` 或显式 `static`；它可以继承类、实现接口，也可以有私有成员。所在的外层代码可通过对象访问这些私有成员。
- 在实例上下文声明的普通局部类，其实例代码可以访问外部对象的成员，包括私有成员；在静态上下文中没有当前外部对象，只能直接使用外部类的静态成员。要访问实例成员，需要另有外部对象。
- 局部类使用所在作用域的局部变量或方法参数，称为捕获。编译器必须能确定被捕获的变量在读取前已经赋值，而且它必须是 `final` 或 effectively final（实际不可变）。
- `final` 明确要求变量赋值后不能再赋值；effectively final 指没有写 `final`，但按代码的赋值情况，同样可以加上 `final` 而不违反赋值规则。即使重新赋值写在类声明之后，也会破坏这一条件。
- 对引用变量来说，限制的是引用不能重新指向别的对象；如果对象本身可变，仍可调用方法修改它的内容。

```java
class LocalExample {
    private int base = 10;

    void show(String prefix) {
        StringBuilder text = new StringBuilder(prefix); // effectively final
        class Message {
            private String value() { return text.toString() + base; }
        }
        text.append(": ");                 // 修改对象，不是重给 text 赋值
        System.out.println(new Message().value()); // Hi: 10
        // text = new StringBuilder();    // 重新赋值，捕获 text 的代码不能编译
    }

    public static void main(String[] args) {
        new LocalExample().show("Hi");
    }
}
```

课程用局部类包装员工、增加派生名称并实现 `Comparable`，以便在方法中排序。作用域和捕获规则见 [Java 语言规范 §14.3](https://docs.oracle.com/javase/specs/jls/se21/html/jls-14.html#jls-14.3) 和 [§6.5.6.1](https://docs.oracle.com/javase/specs/jls/se21/html/jls-6.html#jls-6.5.6.1)。

Java 16 起可在块中声明局部 `interface` 和 `enum`，`record` 也在 Java 16 正式引入（此前有预览版本）。这些局部类型隐式为静态，不属于内部类，也不能捕获外层局部变量；参见 [Java 16 语言规范 §14.3](https://docs.oracle.com/javase/specs/jls/se16/html/jls-14.html#jls-14.3) 和 [JEP 395](https://openjdk.org/jeps/395)。

## 匿名类与比较器

- 匿名类没有类名，通过 `new 类型(参数) { 类体 }` 在表达式中声明类并创建对象。表达式可赋给变量，也可直接作为方法参数；赋值语句以分号结束。
- `new` 后写的是要继承的类或要实现的接口，不是匿名类的名字。匿名类继承指定类，或实现指定接口；实现接口时仍须实现其未实现的抽象方法。
- 匿名类不能显式声明构造器；继承类时可向可访问的父类构造器传参，也可用实例初始化块执行初始化。不能继承 `final` 类，例如 `record`。
- 匿名类捕获局部变量和方法参数时，与局部类一样要求变量已赋值且为 `final` 或 effectively final；引用对象的内容仍可修改。实例上下文中可访问外部对象的成员。
- 具名局部类可在作用域内多次用类名创建对象；匿名类不能通过类名再次创建，但同一个创建表达式每次执行都可创建新对象。一次性定制行为时可用匿名类。

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Comparator;
import java.util.List;

class AnonymousExample {
    static void sort(List<String> names, boolean reverse) {
        Comparator<String> order = new Comparator<String>() {
            @Override
            public int compare(String a, String b) {
                return reverse ? b.compareTo(a) : a.compareTo(b);
            }
        };
        names.sort(order);
    }

    public static void main(String[] args) {
        List<String> names = new ArrayList<>(Arrays.asList("Lin", "Ada"));
        sort(names, false);
        System.out.println(names); // [Ada, Lin]
    }
}
```

`Comparator` 把排序规则与被比较对象分开：`compare(a, b)` 返回负数、零或正数，分别表示 `a` 排在 `b` 前、两者排序相等或排在后。可用普通类、静态嵌套类、成员内部类、局部类或匿名类实现；成员内部类的创建需要外部对象。`Comparable` 则由对象类型定义自然顺序。

`Comparator` 是函数式接口，可以用 Lambda 简写，例如 `(a, b) -> a.compareTo(b)`；Lambda 不声明匿名类，且其中的 `this` 指外层的 `this`，匿名类中的 `this` 指匿名类对象。需要继承类或使用自己的字段、多个方法时，可考虑匿名类。Lambda 从 Java 8 起可用；详见 [Java 语言规范 §15.9.5](https://docs.oracle.com/javase/specs/jls/se21/html/jls-15.html#jls-15.9.5)、[§15.27.2](https://docs.oracle.com/javase/specs/jls/se21/html/jls-15.html#jls-15.27.2) 和 [Comparator API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Comparator.html)。

综合练习中，局部类通过组合保存原来的员工 `record`，并增加全名、工作年数等派生信息；匿名比较器捕获排序字段，按姓名或工作年数比较包装对象，再对列表排序。课程以当前年份减入职年份估算工作年数，这不是按完整入职周年计算的精确工龄。
