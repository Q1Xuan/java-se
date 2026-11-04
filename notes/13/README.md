# 第 13 模块：嵌套类

本页目前覆盖分类、静态嵌套类和成员内部类；其他主题待整理。

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

例如，把比较器放进员工类后，它可以通过传入的员工对象读取私有字段，而不必为排序专门增加 getter。比较器与排序的其他用法后续整理。

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
