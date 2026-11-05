# 第 14 模块覆盖记录：第 7—8 节

对应任务 [#15](https://github.com/Q1Xuan/java-se/issues/15)，内容在 `notes/14/parts/03-exercises.md`。按主题提炼，不复制完整练习程序。

| 资料和内容 | 处理 |
| --- | --- |
| 第 7 节字幕、9 页课件；图片代码包括匿名 Consumer 拆词、隔字符方法及 UnaryOperator、Function 变量、forEach | 匿名类转 Consumer、嵌套 forEach；隔字符及 apply、函数参数和直接传 Lambda；Supplier 的表达式体、代码块及 get。课件图片已逐一查看，无表格。 |
| 第 8 节字幕、2 页课件 | setAll 索引、大写、数组支持的列表与 replaceAll、随机首字母、反转首名、forEach、复制到 ArrayList 后 removeIf，以及代码块返回值。课件图片为背景和标志，无代码或表格。 |
| 两节源码链接文本 | 只读到下载链接，未取得课程源码；字幕和课件足以提炼所见练习，但不能确认源码中是否有额外内容。 |

课程代码的 IDE 操作、完整业务程序和随机输出逐行抄录未收入；示例保留相关行为，固定随机种子使验证可复现。课程完整 JDK 版本未确认；双边界 `Random.nextInt` 从 Java 17 起可用，见 [RandomGenerator API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/random/RandomGenerator.html#nextInt(int,int))；数组视图与删除限制见 [Arrays.asList](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Arrays.html#asList(T...))。

## 验证与接续

使用 OpenJDK 21.0.12.1：将笔记四段 Java 代码中的独立语句包入 `main`，`javac` 编译并运行；输出依次符合拆词、`24680`、`JAVA`、供应字符串及数组/列表示例的注释。另以小程序验证数组视图上 `removeIf` 命中时抛出 `UnsupportedOperationException`。JShell 因沙箱禁止本地套接字未能启动，改用上述编译运行方式。检查了代码围栏和覆盖对应关系。后续由模块集成任务合并各主题、检查整体链接与重复内容并更新进度。
