# 第 14 模块第 9—11 节资料覆盖记录

对应 #16；清单位置：[方法引用与接收者](../../notes/14/parts/04-method-references.md)。

| 资料 | 已核对内容与位置 |
| --- | --- |
| 第 9 节 VTT、PPTX | `println`、静态方法、构造器、延迟调用和 Supplier 填充数组 → 方法引用与接收者；填充数组示例提炼为构造器调用，不复制完整程序。课件图片中的 Lambda 与 `System.out::println` 已核对。 |
| 第 10 节 VTT、PPTX | 类型名、已绑定和未绑定接收者、`concat`、`toUpperCase`、`transform` → 参数与接收者。已检查四类表格及图片代码：零、一、双参数接口，`isEmpty`、`length`、`clear`、`add`、`printf`、`split`、构造器及随机对象方法；按参数匹配规则归纳，未逐格复制。 |
| 第 11 节 VTT、PPTX | 操作列表、顺序转换、数组支持的列表、反转名字、各类引用 → 练习；随机中间名和其余形式简述，不复制完整程序。 |
| 第 9—11 节 TXT | 均为下载链接，未读取链接中的课程源码；因此无法核对源码完整实现和完整课程 JDK 版本。字幕与课件足以提炼已显示的规则和练习，不能据此声称源码已核对。 |

已读取三份字幕的全部文本与三份课件的全部幻灯片文字，检查课件中图片代码和表格；背景和标识未收入。课件第 10 节的通用示意图在 `::` 方法名后画了参数列表，实际引用语法不写参数列表；以 [JLS §15.13](https://docs.oracle.com/javase/specs/jls/se21/html/jls-15.html#jls-15.13) 为准。课件称静态方法在 Lambda 中也必须用类型调用；普通方法调用允许经实例表达式访问静态方法，方法引用则须按规范使用类型形式。字幕将 `System.out` 称为方法、暗示 `String.valueOf` 必然创建新字符串，均未沿用。`transform` 自 Java 12 起可用，课程完整 JDK 版本未确认。

验证：本机 OpenJDK `21.0.12.1`（2026-08-18）；用 `/usr/lib/jvm/java-21-openjdk-amd64/bin/javac /tmp/ReferenceCheck.java` 编译，再以同目录 `java -cp /tmp ReferenceCheck` 运行自编检查。实际输出包含 `35`、`Hello World`、`Java SE`、`HELLO`、`false`、`[ADA ADA, BO OB]`；还确认 null 已绑定接收者在创建时抛异常、数组视图添加时报 `UnsupportedOperationException`，以及表中各类引用和构造器编译。单独编译 `UnaryOperator<String> bad = String::concat` 得到 invalid method reference 错误。原始资料和临时验证文件不纳入公开仓库。

接续：本主题资料与关键规则已核对；合并时将本主题纳入模块总清单及总覆盖、进度记录，并检查跨主题重复与链接。
