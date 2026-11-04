# 第 13 模块：嵌套类

本页覆盖静态嵌套类、成员内部类、局部类和匿名类。

## 四种形式

嵌套类是在另一个类或接口中声明的类：

```mermaid
flowchart TD
    A[嵌套类] -->|static| B[静态嵌套类]
    A -->|非 static| C[内部类]
    C --> D[成员内部类]
    C --> E[局部类]
    C --> F[匿名类]
```

这里的 `static` 包括隐式声明为静态；例如局部 `record` 和 `enum` 是静态的，不属于内部类。类、接口、枚举和记录都可以作为嵌套类型。

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

在外部类的类体中声明非静态类，就是成员内部类。它可以使用 `public`、`protected`、`private` 或包访问权限。

```java
class Meal {
    private int price = 5;

    class Item {
        private int price = 2;

        int total() {
            return this.price + Meal.this.price; // 内部类的 price + 外部类的 price
        }
    }

    Item createItem() { return new Item(); } // 关联当前 Meal 对象
    int itemPrice(Item item) { return item.price; } // 通过对象访问内部类的私有成员
}
```

在外部创建时，先创建外部类对象，再用它创建内部类对象：

```java
Meal meal = new Meal();
Meal.Item item = meal.new Item(); // 外部类.内部类 对象名 = 外部对象.new 内部类();
System.out.println(item.total()); // 7
```

内部类可直接访问关联的外部对象的私有成员；同名时，用 `this.price` 和 `Meal.this.price` 区分。外部类也可通过内部类对象访问其私有成员。静态上下文没有当前外部对象，创建内部类或访问实例成员时需要提供相应对象；外部访问还须满足访问权限。

汉堡练习用私有 `Item` 表示套餐组成，用 `Burger` 继承 `Item`，并由 `Meal` 把添加配料的操作交给 `Burger`。嵌套关系允许访问私有成员，但不会改变继承规则：父类的私有字段不会作为子类成员被继承。配料枚举及其私有方法展示了嵌套类型的封装；内部类静态成员的版本限制见上文。

播放列表练习用 `SongList` 封装歌曲集合和查找操作，再由 `Album` 调用它。练习的在线评测明确要求将 `SongList` 声明为 `public static`；这时它是静态嵌套类，不需要外部 `Album` 对象。该声明是评测的特殊要求，不是 Java 对成员内部类的一般要求；在普通代码中可根据是否需要关联外部对象及访问范围选择声明。

## 局部类与局部变量

局部类在代码块中声明，类名从声明处起只在所在块内可用。它可以继承类、实现接口，但不能声明访问权限修饰符或显式声明为 `static`。

局部类可以访问外部对象的私有成员；捕获局部变量时，变量必须已赋值，且是 `final` 或 effectively final（没有写 `final`，但赋值情况符合 `final` 的要求）：

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

在静态上下文中没有当前外部对象，访问外部实例成员需要另有对象。局部类可以有私有成员，外层代码可通过对象访问；虽然类名不能在块外使用，对象仍可能通过其他类型的引用继续使用。

课程用局部类包装员工、增加派生名称并实现 `Comparable`，以便在方法中排序。作用域和捕获规则见 [Java 语言规范 §14.3](https://docs.oracle.com/javase/specs/jls/se21/html/jls-14.html#jls-14.3) 和 [§6.5.6.1](https://docs.oracle.com/javase/specs/jls/se21/html/jls-6.html#jls-6.5.6.1)。

Java 16 起可在块中声明局部 `interface` 和 `enum`，`record` 也在 Java 16 正式引入（此前有预览版本）。这些局部类型隐式为静态，不属于内部类，也不能捕获外层局部变量；参见 [Java 16 语言规范 §14.3](https://docs.oracle.com/javase/specs/jls/se16/html/jls-14.html#jls-14.3) 和 [JEP 395](https://openjdk.org/jeps/395)。

## 匿名类与比较器

匿名类没有类名，在表达式中声明并创建对象。下面的 `Comparator<String>` 是实现的接口，不是匿名类的名字：

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

匿名类也可以继承一个类：`new 父类(参数) { ... }`。它不能显式声明构造器，但可以向可访问的父类构造器传参或使用实例初始化块；不能继承 `final` 类。实现接口时须实现未实现的抽象方法。

匿名类捕获局部变量的规则与局部类相同。同一个创建表达式每次执行都可创建新对象；实例上下文中也可访问外部对象的成员。

`Comparator` 把排序规则与被比较对象分开：`compare(a, b)` 返回负数、零或正数，分别表示 `a` 排在 `b` 前、两者排序相等或排在后。可用普通类、静态嵌套类、成员内部类、局部类或匿名类实现；成员内部类的创建需要外部对象。`Comparable` 则由对象类型定义自然顺序。

`Comparator` 是函数式接口，可以用 Lambda 简写，例如 `(a, b) -> a.compareTo(b)`；Lambda 不声明匿名类，且其中的 `this` 指外层的 `this`，匿名类中的 `this` 指匿名类对象。需要继承类或使用自己的字段、多个方法时，可考虑匿名类。Lambda 从 Java 8 起可用；详见 [Java 语言规范 §15.9.5](https://docs.oracle.com/javase/specs/jls/se21/html/jls-15.html#jls-15.9.5)、[§15.27.2](https://docs.oracle.com/javase/specs/jls/se21/html/jls-15.html#jls-15.27.2) 和 [Comparator API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Comparator.html)。

综合练习中，局部类通过组合保存原来的员工 `record`，并增加全名、工作年数等派生信息；匿名比较器捕获排序字段，按姓名或工作年数比较包装对象，再对列表排序。课程以当前年份减入职年份估算工作年数，这不是按完整入职周年计算的精确工龄。
