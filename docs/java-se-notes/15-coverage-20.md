# 第 15 模块 #20 覆盖与检查记录

基点 `b4f4c27f1b93d3aebdaa7d8ad96b5b2d22186fd8`，工作范围第 1—8 节，成果见 [知识清单](../../notes/15/20.md)。

| 节 | 已读取资料及覆盖 |
| --- | --- |
| 1 | 字幕、课件文字：框架引言，见“接口与实现”。 |
| 2 | 字幕、课件文字及图：Collection、List、Set、Queue、Map 层次、接口方法和多态算法，见“接口与实现”。附带文本为 Oracle 概览链接。 |
| 3 | 字幕、课件文字和类图：Card record、花色、52 张牌、生成与打印，见“纸牌练习中的选择”。不复制完整实现。 |
| 4 | 字幕、课件文字及类图：fill、容量、nCopies、addAll、copy、copyOf，见“填充与复制”。 |
| 5 | 字幕：shuffle、reverse、sort、子序列及 disjoint，见“重排、排序和查找”。本地无课件。 |
| 6 | 字幕：binarySearch、replaceAll、frequency、min/max、rotate、swap，见“重排、排序和查找”。本地无课件。 |
| 7 | 字幕、课件文字：纸牌挑战、洗牌切牌、发牌和排序，见“纸牌练习中的选择”。 |
| 8 | 字幕：剩余牌、占位列表、牌型频次、保留和弃牌、极值，见“纸牌练习中的选择”。本地无课件。 |

第 2 节课件中方法列表、接口层次、数组和链表、队列、集合和映射图片，以及第 3、4 节 Card 类图已提取并目视检查；其余图片为课件装饰。课件图把 priority queue 与 LIFO 栈等同；[PriorityQueue API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/PriorityQueue.html) 表明其按优先级取队首，笔记已纠正。第 3 节字幕一处将 A 的 rank 说成 13，与同节课件及其余讲解的 12 冲突，以课件和计算 9—12 为准。课程称花色字符为 ASCII，实际码点 9824 等超出 ASCII。

第 2—8 节的源码文本仅是 Google Drive 下载链接，未取得和阅读源码；只能依据字幕和课件整理，无法核验完整源码及课程运行结果。课程确切 JDK 版本未确认：字幕使用 record 与 `Random.nextInt(origin, bound)`，不足以确定完整版本。第 5、6、8 节本地无课件文件，不声称读取。练习保留集合操作及规则，省略完整业务代码和打印排版。

核对依据：[Java 21 Collections API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Collections.html)、[List API](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/List.html)；按官方契约说明二分查找前提、不可修改和视图，未沿用字幕中“重复值决定性能”的笼统建议。

验证环境为 OpenJDK 21.0.12.1（Ubuntu，`javac`/`java`）。临时程序编译并运行通过：空列表 fill、nCopies 后 copy、目标过短异常、二分查找插入点、rotate、连续子序列与 containsAll、subList 视图和 copyOf 不可修改；`git diff --check` 通过。未运行课程原程序，也未测试随机洗牌分布。下一步由整合任务合并各主题并统一导航、状态与跨主题重复。
