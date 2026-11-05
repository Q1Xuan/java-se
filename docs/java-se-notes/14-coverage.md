# 第 14 模块：资料覆盖与验证

[知识清单](../../notes/14/README.md)。以下保留各主题实际读取和验证记录；源码链接不等于已读取源码，课程完整 JDK 版本未确认。

## 第 14 模块第 1—4 节覆盖记录

任务 #13；知识清单位于 `notes/14/README.md`。

| 资料 | 已核对内容与处理 |
| --- | --- |
| 1 字幕、课件（2 页） | Java 8 引入 Lambda、行为参数化和与匿名类的联系：基础及比较器；方法引用、接口便利方法留待后续主题。 |
| 2 字幕、课件（9 页） | 姓名排序、稳定排序、匿名比较器与 Lambda、目标类型、SAM、继承抽象方法与注解：比较器和函数式接口。局部接口 Java 16 起可用，已由第 13 模块说明。 |
| 3 字幕、课件（6 页） | Consumer、forEach、单参数及代码块、作用域、函数式编程和流：参数、捕获及末段；不扩写函数式编程或流教程。 |
| 4 字幕、课件（2 页） | 泛型 Operation、计算器、装箱、多个参数、返回值及错误：参数与自定义操作。IDE 操作不属于复习规则。 |

逐项读取了四份字幕和 PPTX 中全部幻灯片文本；提取并目视检查第 2—4 节课件图片中的代码（比较器、Consumer、参数及返回值），表格文字和图片一并核对。第 1 节图片为课件装饰。第 2—4 节各有源码下载链接文本，但未读取链接指向的源码；第 3 节另有函数式编程外链文本，未读取外链。没有单独练习文件；以上覆盖基于字幕和课件，不能确认下载源码是否包含额外内容。原课件、字幕、链接文本和旧笔记未收入仓库。

### 核对与验证

- 本机 `java -version`：OpenJDK 21.0.12.1；课程完整 JDK 版本未确认。使用 `javac` 编译临时独立示例并运行：长度排序 `[Bo, Lin, Ada]`、整数相加 `7`、浮点除法 `4.0`、捕获可变对象输出 `Hi !Ada`。
- 负例使用 `javac` 检查：混用 `var` 和显式类型被拒绝。规范链接见知识清单；课程把代码块的返回规则概括为必须返回，笔记按有返回值的目标方法及正常完成路径限定。

## 第 14 模块资料核对：内置函数式接口（第 5—6 节）

对应 [任务 #14](https://github.com/Q1Xuan/java-se/issues/14)，笔记见 [内置函数式接口](../../notes/14/README.md)。

### 实际读取的资料

- 第 5 节字幕全文；课件 6 页的文字、表格及嵌入图片中的方法签名和 Lambda 示例。
- 第 6 节字幕全文；课件 7 页的文字、表格及嵌入图片中的签名、Lambda 示例和参数语法。
- 两节各有一个源码下载链接文本，内容仅是 Google Drive 地址；未下载或读取实际源码，不能确认课程项目的其他细节。字幕和课件足以提炼本节演示的规则，但不证明源码中没有额外内容。两节没有单独的本地练习文件；课程中的操作示例按下表处理。

### 覆盖对应

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

### 核对与验证

- Oracle Java 21 [函数式接口包](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/function/package-summary.html)、[Supplier](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/function/Supplier.html)、[Collection.removeIf](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Collection.html#removeIf(java.util.function.Predicate))、[List.replaceAll](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/List.html#replaceAll(java.util.function.UnaryOperator)) 和 [Arrays.setAll](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Arrays.html#setAll(T%5B%5D,java.util.function.IntFunction)) 用于核对签名、删除/替换、下标生成及 Supplier 不保证新值。默认实现不应误写为所有实现的内部算法。课程完整 JDK 版本未确认；示例使用的 switch 表达式在本机 JDK 21 验证。
- 本机 OpenJDK `21.0.12.1`（2026-08-18）。将笔记中 5 个 Java 代码块分别提取到 `/tmp/issue14-check`，运行 `/usr/lib/jvm/java-21-openjdk-amd64/bin/javac /tmp/issue14-check/*.java`，再用同目录的 `java -cp /tmp/issue14-check` 分别运行五个类，均成功。输出与注释一致：坐标两行、`true`、`[Alpha, echo]`、`[a, b]`、`5`、转换后的列表和数组、长度 `3`。随机选择的具体名字未断言。

## 第 14 模块覆盖记录：第 7—8 节

对应任务 [#15](https://github.com/Q1Xuan/java-se/issues/15)，内容在 `notes/14/README.md`。按主题提炼，不复制完整练习程序。

| 资料和内容 | 处理 |
| --- | --- |
| 第 7 节字幕、9 页课件；图片代码包括匿名 Consumer 拆词、隔字符方法及 UnaryOperator、Function 变量、forEach | 匿名类转 Consumer、嵌套 forEach；隔字符及 apply、函数参数和直接传 Lambda；Supplier 的表达式体、代码块及 get。课件图片已逐一查看，无表格。 |
| 第 8 节字幕、2 页课件 | setAll 索引、大写、数组支持的列表与 replaceAll、随机首字母、反转首名、forEach、复制到 ArrayList 后 removeIf，以及代码块返回值。课件图片为背景和标志，无代码或表格。 |
| 两节源码链接文本 | 只读到下载链接，未取得课程源码；字幕和课件足以提炼所见练习，但不能确认源码中是否有额外内容。 |

课程代码的 IDE 操作、完整业务程序和随机输出逐行抄录未收入；示例保留相关行为，固定随机种子使验证可复现。课程完整 JDK 版本未确认；双边界 `Random.nextInt` 从 Java 17 起可用，见 [RandomGenerator API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/random/RandomGenerator.html#nextInt(int,int))；数组视图与删除限制见 [Arrays.asList](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Arrays.html#asList(T...))。

### 验证与接续

使用 OpenJDK 21.0.12.1：将笔记四段 Java 代码中的独立语句包入 `main`，`javac` 编译并运行；输出依次符合拆词、`24680`、`JAVA`、供应字符串及数组/列表示例的注释。另以小程序验证数组视图上 `removeIf` 命中时抛出 `UnsupportedOperationException`。JShell 因沙箱禁止本地套接字未能启动，改用上述编译运行方式。检查了代码围栏和覆盖对应关系。

## 第 14 模块第 9—11 节资料覆盖记录

对应 #16；清单位置：[方法引用与接收者](../../notes/14/README.md)。

| 资料 | 已核对内容与位置 |
| --- | --- |
| 第 9 节 VTT、PPTX | `println`、静态方法、构造器、延迟调用和 Supplier 填充数组 → 方法引用与接收者；填充数组示例提炼为构造器调用，不复制完整程序。课件图片中的 Lambda 与 `System.out::println` 已核对。 |
| 第 10 节 VTT、PPTX | 类型名、已绑定和未绑定接收者、`concat`、`toUpperCase`、`transform` → 参数与接收者。已检查四类表格及图片代码：零、一、双参数接口，`isEmpty`、`length`、`clear`、`add`、`printf`、`split`、构造器及随机对象方法；按参数匹配规则归纳，未逐格复制。 |
| 第 11 节 VTT、PPTX | 操作列表、顺序转换、数组支持的列表、反转名字、各类引用 → 练习；随机中间名和其余形式简述，不复制完整程序。 |
| 第 9—11 节 TXT | 均为下载链接，未读取链接中的课程源码；因此无法核对源码完整实现和完整课程 JDK 版本。字幕与课件足以提炼已显示的规则和练习，不能据此声称源码已核对。 |

已读取三份字幕的全部文本与三份课件的全部幻灯片文字，检查课件中图片代码和表格；背景和标识未收入。课件第 10 节的通用示意图在 `::` 方法名后画了参数列表，实际引用语法不写参数列表；以 [JLS §15.13](https://docs.oracle.com/javase/specs/jls/se21/html/jls-15.html#jls-15.13) 为准。课件称静态方法在 Lambda 中也必须用类型调用；普通方法调用允许经实例表达式访问静态方法，方法引用则须按规范使用类型形式。字幕将 `System.out` 称为方法、暗示 `String.valueOf` 必然创建新字符串，均未沿用。`transform` 自 Java 12 起可用，课程完整 JDK 版本未确认。

验证：本机 OpenJDK `21.0.12.1`（2026-08-18）；用 `/usr/lib/jvm/java-21-openjdk-amd64/bin/javac /tmp/ReferenceCheck.java` 编译，再以同目录 `java -cp /tmp ReferenceCheck` 运行自编检查。实际输出包含 `35`、`Hello World`、`Java SE`、`HELLO`、`false`、`[ADA ADA, BO OB]`；还确认 null 已绑定接收者在创建时抛异常、数组视图添加时报 `UnsupportedOperationException`，以及表中各类引用和构造器编译。单独编译 `UnaryOperator<String> bad = String::concat` 得到 invalid method reference 错误。原始资料和临时验证文件不纳入公开仓库。

## 第 14 模块第 12—13 节资料覆盖记录

对应 #17，知识清单位于 [函数式接口的组合](../../notes/14/README.md)。

| 资料 | 已处理内容 |
| --- | --- |
| 第 12 节 VTT、PPTX | Function 的 `andThen`、`compose`、中间类型与接口差异 → Function；Consumer 链执行 → Consumer；Predicate 的 `and`、`or`、`negate` 与 `removeIf` → Predicate |
| 第 13 节 VTT、PPTX | 组合方法汇总 → 前三节；比较器的自然顺序、反序、键提取、次级排序和反转 → Comparator |
| 第 12、13 节源码 TXT | 各只有下载链接，未读取课程源码；不能据此核验完整程序或课程完整 JDK 版本 |

两份字幕已通读；两份课件共四页，文字和表格已检查。课件图片已逐一查看，仅为背景和标识，无图片代码。字幕中的姓名处理、字符打印和人物排序被提炼为短例；完整演示程序、重复讲解、装饰及过渡内容未收入。课程画面中的代码未作为独立资料提供；字幕和课件足以支持上述规则，但不能核对画面与源码的全部细节。

本机 OpenJDK 21.0.12.1：将清单四段代码分别放入 `main`，运行 `javac` 和 `java`，输出均与注释一致；另用抛异常的右侧 Predicate 确认 `and`、`or` 短路，并确认 Consumer 前一步抛异常时后一步未执行。将 `String` 结果传给需要 `Integer` 的后续函数，`javac` 按预期拒绝。起初 JShell 默认执行引擎因沙箱禁止本地套接字启动失败，改为上述独立类执行。

对照清单所链接的 Java 21 官方 API 核对执行顺序、短路、异常、排序和类型；课程中关于原始类型接口的概括限于具体接口，避免误认为所有原始类型特化接口都没有组合方法。课程完整 JDK 版本未确认，本机版本不代表课程版本。

## 整合检查

整合时以 OpenJDK `21.0.12.1` 编译运行独立检查，验证长度排序、`String::concat`、Predicate 短路、Function 组合顺序和 `removeIf`，结果通过；命令为 `javac /tmp/Integration14.java` 和 `java -cp /tmp Integration14`。检查仓库 Markdown 相对路径和代码围栏，均通过；未验证外部链接可用性。前述各主题验证是各任务的实际记录，不代表整合时重新运行了全部示例。

## 审查修订验证（2026-11-05）

为 `this` 和 Lambda 作用域补充紧邻规则的例子，并明确 `calculate` 声明与调用所在的类和方法；检查其余含静态方法声明的代码块，均有类上下文。本机 OpenJDK `21.0.12.1`：从笔记提取三个例子，补入前文的 `Operation` 接口后用 `javac` 编译、`java` 运行，分别输出 `true`、`false`；`5`；`7, 4.0, AB`。分别取消作用域例子中的两个错误行注释，编译器按预期报告参数名重复和找不到块内变量。检查本笔记、进度及覆盖记录的相对文件链接和代码围栏，以及 `git diff --check`，均通过；未检查外部链接可用性。
