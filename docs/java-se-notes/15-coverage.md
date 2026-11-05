# 第 15 模块逐节覆盖与验证记录

以下保留各主题任务的资料、依据与验证记录；其中“下一步”是当时的接续记录。整合后的状态见[进度记录](PROGRESS.md)。

## 第 15 模块 #20 覆盖与检查记录

基点 `b4f4c27f1b93d3aebdaa7d8ad96b5b2d22186fd8`，工作范围第 1—8 节，成果见 [知识清单](../../notes/15/README.md)。

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

## 第 15 模块 #21 覆盖与验证记录

基点：`b4f4c27`，范围第 9—14 节。成果：[哈希、HashSet 与集合运算](../../notes/15/README.md)。本记录供 #26 整合时合并进总覆盖和进度；未修改共享状态文件。

| 节 | 实际读取与处理 | 对应位置 |
| --- | --- | --- |
| 9 | 字幕、课件文字和全部内嵌图：身份、值相等、哈希桶、碰撞、卡牌及生成方法 | 相等与哈希 |
| 10 | 字幕、课件文字及联系人类图：构造器链、两类数据、字符串 Scanner、合并 | Set 和联系人 |
| 11 | 字幕、课件文字及图片：Set、HashSet、联系人相等字段和重复处理 | Set 和联系人 |
| 12 | 字幕、课件文字及所有维恩图：并、交、差、对称差和不同代表对象 | 集合运算 |
| 13 | 字幕、课件文字及图片：Task/TaskData、枚举、排序、解析 | 任务练习 |
| 14 | 字幕、课件文字及类图：任务集合问题、无输入修改的运算及重叠明细 | 集合运算、任务练习 |

每节源码资源文本及 13、14 节 CSV 资源文本均只有下载链接，未下载或读取其内容；因此不声称核对源码或 CSV。课上演示细节依据字幕与课件，未复制完整业务程序或数据。课件图片逐一目视检查，包含第 9 节代码和哈希示意、第 10、14 节类图及第 12 节维恩图；其余为装饰图。未发现课程具体 JDK 版本，文本块表明课程使用支持该语法的版本，不能据此确定精确版本。

课件第 9 节图把不同哈希码与不同桶关联，实际不同哈希码也可能映射到同一桶；第 14 节字幕误称交集不对称，与第 12 节课件及数学运算矛盾。笔记分别按碰撞和交集可交换处理。课件建议避开某些小质数并非通用正确性契约，不作为规则。第 13 节字幕将缺少第 4 字段的判断说成少于 3 个字段，无法据此确认源码的实际判断，笔记仅说明缺失字段须处理。

官方依据：[Object 的 equals/hashCode 契约](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Object.html#hashCode())、[HashSet](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/HashSet.html)、[Set](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Set.html)。

验证环境为 OpenJDK 21.0.12.1（Ubuntu，2026-08-18），与课程精确版本是否相同未知。使用临时独立 Java 程序编译并运行卡牌去重、并交差、对称差、包含与输入未改变的断言，输出 `OK`；这是针对性验证，不代表运行了课程源码。检查两个文件代码围栏配对、相对链接目标及 `git diff --check`；待整合时复查跨任务重复与整章导航。

## 第 15 模块 #22 覆盖记录

基点 `b4f4c27f1b93d3aebdaa7d8ad96b5b2d22186fd8`；本任务仅覆盖 15—18 节，整合及状态更新由后续任务处理。

| 节 | 实际核对资料 | 知识清单位置与处理 |
| --- | --- | --- |
| 15 | 完整字幕、课件文字与嵌入图像；源码文件仅为下载链接 | 选择集合与排序：插入顺序、树、复杂度、接口、比较器、首末与复制；课件树和接口图已目视检查。 |
| 16 | 完整字幕、课件文字与嵌入图像；源码文件仅为下载链接 | 导航和范围：四种最近值、边界、降序和共享视图。 |
| 17 | 完整字幕、课件文字与嵌入图像；源码文件仅为下载链接 | 座位练习：嵌套座位、格式、排序、单座精确查找与状态。 |
| 18 | 完整字幕、课件文字与嵌入图像；源码文件仅为下载链接 | 座位练习：区间扫描、连续计数和独立结构的浅复制。 |

课件文字从 PPTX XML 提取，10 个嵌入图像逐一目视检查；图像包括背景、标识及第 15 节树和接口图。环境无 LibreOffice，未渲染完整幻灯片，因此无法排除 XML 提取以外的绘制元素或布局信息。源码下载链接未下载，未声称读取课程源码；完整程序细节和课程 JDK 精确版本无法核实。字幕中的预订演示和打印格式提炼为规则，未复制完整业务程序。课程把二叉搜索树简称 Btree、将根称为中点，以及座位范围使用“每座的行数”等表述不够准确；笔记不沿用这些说法。连续区间采用包含端点的 `max - min + 1`。

官方依据：Java SE 17 [TreeSet](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/TreeSet.html)、[NavigableSet](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/NavigableSet.html)、[LinkedHashSet](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/LinkedHashSet.html)。它们确认比较器去重、复杂度、导航及视图规则；不以此推断课程所用 JDK。

验证环境：OpenJDK / javac 21.0.12.1。临时 Java 程序已编译并运行，通过对导航边界、视图双向变化和越界添加、比较器去重、插入顺序、空集合首元素行为的断言；没有运行完整课程座位程序。检查两个 Markdown 文件的围栏配对、公开文件范围及差异空白。下一步由 #26 合并四节与其余主题并完成全模块检查；无法读取源码和完整课件渲染的限制须保留。

## 第 15 模块 #23 资料核对：Map 基础、更新与视图

基点：`65574b7d7115c7af40a8de081c5baff4c70b642a`，集成分支 `notes/module-15-integration`。主题笔记：[Map](../../notes/15/README.md)。

| 资料 | 实际核对及覆盖 |
| --- | --- |
| 第 19 节字幕 | 全文已读。Map 与 Collection、键值类型和唯一性、实现顺序、`put`、`get`、`getOrDefault`、`putIfAbsent`、联系人合并、`merge` 和 `forEach` 见“键与值”“合并和计算”。 |
| 第 19 节课件 | 4 页 XML 文本及所有嵌入图片已检查，图片已目视查看：接口层级、接口声明、实现比较均已处理。未能渲染整页（无 LibreOffice），因此不能声称完整页版式已核对。 |
| 第 20 节字幕 | 全文已读。`compute` 三种形式、`replaceAll`、两种 `replace`、两种 `remove` 见“合并和计算”“替换与删除”。 |
| 第 20 节课件 | 目录中未发现本节课件。 |
| 第 21 节字幕 | 全文已读。视图与副本、keySet 的批量操作、values 的重复值及复制排序/去重、entrySet 与键值访问见“三种集合视图”。HashMap 内部 Node 和 table 为实现细节，未放入复习主线。 |
| 第 21 节课件 | 7 页 XML 文本及所有嵌入图片已检查，图片已目视查看：Map/Entry 图、三个视图和 keySet 操作已处理。同样无法渲染完整页。 |
| 19—21 节源码文本 | 三份均仅含下载链接；未下载或阅读源码，故课程演示的完整工程及 JDK 配置不能确认。 |

未复制联系人业务程序或逐行操作过程；保留其合并和视图操作的知识。课程具体 JDK 版本未能从本任务资料确认；字幕提到相关默认方法从 JDK 8 引入，也使用了 `List.of` 和局部变量类型推断。第 20 节开头误称 `computeIfAbsent` 使用双参数函数，随后演示改为单参数；按 [Java 17 Map API](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Map.html) 的 `Function` 签名整理。该 API 也确认 null、视图和计算方法条件；[HashMap API](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/HashMap.html) 确认允许 null、无顺序保证和视图修改限制。

验证：本机 OpenJDK / javac 21.0.12.1；以 `javac --release 17` 编译临时 `MapCheck.java`，使用 `java -ea` 运行通过，检查 put、null 与默认值、putIfAbsent、merge、compute 系列、条件替换/删除、视图与副本、视图 add 抛异常和重复值。此检查不是课程源码验证，也不是实际 JDK 17 运行。整理完成后需由整合任务检查跨主题重复、链接、统一覆盖及状态；完整课件页可在有渲染工具时补核。

## 第 15 模块 #24 覆盖记录

交付：[知识清单](../../notes/15/README.md)。逐节阅读 22—26 节字幕，提取五份 PPTX 全部幻灯片文字，并逐一目视检查 13 个嵌入图像。第 22、23 节图为地图及控制台示例，其余为背景或标识；枚举位图及范围表是幻灯片文字/形状。环境没有 LibreOffice，未渲染整页，无法排除其他绘图或布局内容。

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

## 第 15 模块 #25：综合库存练习覆盖记录

基点 `075d52d`；交付为 [知识清单](../../notes/15/README.md)。本记录仅覆盖第 27—29 节，整合任务负责模块级状态和导航。

| 资料 | 实际读取与处理 |
| --- | --- |
| 第 27 节字幕 | 全文读取：概念模型、库存数量、购物车增减、`merge`、销售小票，见清单各节。 |
| 第 27 节 PPTX | 提取 7 页文字并目视查看全部 6 个内嵌图片：概念模型图及其分解图、背景和 logo；字段及操作见模型、预留与结账。当前环境无 LibreOffice，未渲染完整幻灯片页面。 |
| 第 28 节字幕 | 全文读取：集合选择、嵌套 EnumMap/TreeMap、库存与货架索引、实体购物车与预留，见模型与集合选择。随机价格及逐行输出属于演示数据，不复制。 |
| 第 29 节字幕 | 全文读取：虚拟购物车、逐项结账、低库存、范围视图与放弃购物车，见预留与结账、放弃购物车。练习测试数据及完整程序不复制。 |
| 27、28、29 节源码 TXT，28 节 ProductData TXT | 四份均只含下载链接，已读链接文本但未下载或核对源码及数据；因此无法确认字幕未展示的实现细节。 |

课程字幕使用 record、枚举、`LocalDate` 等，但未确认具体 JDK 版本。检查环境 OpenJDK 21.0.12.1；不能据此推断课程版本。课程先合并购物车数量再预留、逐项销售及按 ID 前缀和年内日清理的限制已在清单说明。

### 核验

- Oracle Java SE 21 [Map](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Map.html)、[EnumMap](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/EnumMap.html)、[NavigableSet](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/NavigableSet.html)：核对 merge、键顺序、范围视图和 poll 行为。
- 编译并运行临时 Java 示例，检查 merge 累加、EnumMap/TreeMap 顺序和 headSet 视图移除；结果见下方实际记录。
- 完整库存业务及课程源码未运行；仅从字幕提炼，不能声称已验证完整程序。
- 实际执行 `javac /tmp/Inventory25Check.java` 和 `java -cp /tmp Inventory25Check`：通过，输出 `merge, order, backed view: OK`；`git diff --check` 通过。示例为独立最小复现，不是课程源码。

下一步由 #26 将本清单与其他主题整合、检查重复和全模块覆盖，并更新共享任务与进度文件；若取得源码，可补核对未展示的实现细节。
