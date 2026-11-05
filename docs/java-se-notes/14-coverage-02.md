# 第 14 模块资料核对：内置函数式接口（第 5—6 节）

对应 [任务 #14](https://github.com/Q1Xuan/java-se/issues/14)，笔记见 [内置函数式接口](../../notes/14/parts/02-interfaces.md)。本记录只覆盖这两节，供汇总模块覆盖与进度时使用。

## 实际读取的资料

- 第 5 节字幕全文；课件 6 页的文字、表格及嵌入图片中的方法签名和 Lambda 示例。
- 第 6 节字幕全文；课件 7 页的文字、表格及嵌入图片中的签名、Lambda 示例和参数语法。
- 两节各有一个源码下载链接文本，内容仅是 Google Drive 地址；未下载或读取实际源码，不能确认课程项目的其他细节。字幕和课件足以提炼本节演示的规则，但不证明源码中没有额外内容。两节没有单独的本地练习文件；课程中的操作示例按下表处理。

## 覆盖对应

| 资料内容 | 笔记处理 |
| --- | --- |
| 第 5 节四类接口、函数方法；`BinaryOperator` 替代自定义操作接口 | 接口表、Function 例子；自定义接口另由基础主题处理 |
| `Consumer` / `BiConsumer`、坐标与 `forEach`、声明后调用和嵌套 Lambda | Consumer 例子，保留两个坐标的泛型处理与遍历；不复制完整坐标数据和重复打印 |
| `Predicate` / `BiPredicate`、忽略大小写、`removeIf`、迭代器 | Predicate 例子及默认实现说明；区分默认实现和可覆盖实现 |
| 第 6 节 `Function` / `BiFunction` / `UnaryOperator` / `BinaryOperator`、`split` | 接口表、Function 例子 |
| `replaceAll`、`ListIterator.set`；`Arrays.fill`、`setAll`、按索引生成及 `switch` 表达式 | Function 的列表和数组例子；说明对象数组的 `IntFunction` 与默认实现 |
| `Supplier`、随机下标、可重复结果、捕获局部变量 | Supplier 例子及边界说明；未复制完整随机姓名程序 |
| Lambda 零、一、两个参数及 `var` 和显式类型的幻灯片 | Lambda 参数语法和捕获由基础主题统一讲解；此处保留无参数括号和 effectively final 实例 |
| 原始类型变体、包外 `Comparator` | 开头概述；不罗列所有变体，Comparator 由排序主题讲解 |

## 核对与验证

- Oracle Java 21 [函数式接口包](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/function/package-summary.html)、[Supplier](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/function/Supplier.html)、[Collection.removeIf](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Collection.html#removeIf(java.util.function.Predicate))、[List.replaceAll](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/List.html#replaceAll(java.util.function.UnaryOperator)) 和 [Arrays.setAll](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Arrays.html#setAll(T%5B%5D,java.util.function.IntFunction)) 用于核对签名、删除/替换、下标生成及 Supplier 不保证新值。默认实现不应误写为所有实现的内部算法。课程完整 JDK 版本未确认；示例使用的 switch 表达式在本机 JDK 21 验证。
- 本机 OpenJDK `21.0.12.1`（2026-08-18）。将笔记中 5 个 Java 代码块分别提取到 `/tmp/issue14-check`，运行 `/usr/lib/jvm/java-21-openjdk-amd64/bin/javac /tmp/issue14-check/*.java`，再用同目录的 `java -cp /tmp/issue14-check` 分别运行五个类，均成功。输出与注释一致：坐标两行、`true`、`[Alpha, echo]`、`[a, b]`、`5`、转换后的列表和数组、长度 `3`。随机选择的具体名字未断言。
- 接续：合并模块时将本主题接入目录，并核对基础主题的参数语法、捕获和排序主题的 Comparator 覆盖；汇总本记录和源码未读取的限制。
