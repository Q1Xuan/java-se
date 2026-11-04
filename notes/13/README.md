# 第 13 模块：嵌套类

本页目前覆盖分类和静态嵌套类；其他主题待整理。

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
