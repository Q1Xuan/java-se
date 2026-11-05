# 第 15 模块 #24 覆盖记录

交付：[知识清单](../../notes/15/24.md)。逐节阅读 22—26 节字幕，提取五份 PPTX 全部幻灯片文字，并逐一目视检查 13 个嵌入图像。第 22、23 节图为地图及控制台示例，其余为背景或标识；枚举位图及范围表是幻灯片文字/形状。环境没有 LibreOffice，未渲染整页，无法排除其他绘图或布局内容。

| 资料 | 处理 |
| --- | --- |
| 22 冒险游戏挑战及设置 | 嵌套映射、方向枚举、解析与输入；省略游戏叙述和完整程序 |
| 23 完成游戏与自定义 | 查询、无效方向、先默认后替换、移动与退出；省略交互打印 |
| 24 LinkedHashMap、TreeMap | 顺序、键比较、购买记录按日期分组与计数；省略模拟学生和随机数据代码 |
| 25 NavigableMap | 首尾、最接近键、范围与逆序视图、poll 删除、迭代陷阱；省略随机购买统计程序 |
| 26 EnumSet、EnumMap | 工厂、补集、范围、枚举顺序、键类型及内部表示；省略运行时具体内部实现类名和位图逐格演示 |

22—26 节的源码 TXT 和第 22 节 CSV TXT 都仅含下载链接，未下载，不能声称核对了源码或 CSV；游戏结构和例子依据字幕、课件。课中使用 record、文本块等语法，精确 JDK 版本未确认；因此不能断定与验证环境相同。

课件第 25 节误写 `descendingKeyMap()`；API 为 `descendingMap()`。字幕还提到不存在的 `pollFirstKey` / `pollLastKey`；实际使用 `pollFirstEntry` / `pollLastEntry`。第 26 节课件说 EnumMap 从 EnumSet 推断类型，实际构造参数为 Class、EnumMap 或 Map。位向量图的长度是枚举常量数量，不是集合 `size()`；后者是已包含元素的数量。性能仅作一般说明。

官方依据：Java SE 17 [NavigableMap](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/NavigableMap.html)、[EnumMap](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/EnumMap.html)、[EnumSet](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/EnumSet.html)、[LinkedHashMap](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/LinkedHashMap.html)。此版本用作 API 核对，非对课程版本的推断。

验证环境：OpenJDK / javac 21.0.12.1（Ubuntu）。临时程序编译并运行，断言插入顺序、四种导航边界、视图双向修改和越界异常、逆序视图删除、枚举补集和顺序、空普通集合的 `copyOf` 异常，输出 `OK`。未运行完整课程程序。已检查代码围栏配对和公开文件范围；整合时需复查跨任务重复、导航与课程版本，保留源码和整页渲染的限制。
