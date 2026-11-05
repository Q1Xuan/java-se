# 第 15 模块 #25：综合库存练习覆盖记录

基点 `075d52d`；交付为 [知识清单](../../notes/15/25.md)。本记录仅覆盖第 27—29 节，整合任务负责模块级状态和导航。

| 资料 | 实际读取与处理 |
| --- | --- |
| 第 27 节字幕 | 全文读取：概念模型、库存数量、购物车增减、`merge`、销售小票，见清单各节。 |
| 第 27 节 PPTX | 提取 7 页文字并目视查看全部 6 个内嵌图片：概念模型图及其分解图、背景和 logo；字段及操作见模型、预留与结账。当前环境无 LibreOffice，未渲染完整幻灯片页面。 |
| 第 28 节字幕 | 全文读取：集合选择、嵌套 EnumMap/TreeMap、库存与货架索引、实体购物车与预留，见模型与集合选择。随机价格及逐行输出属于演示数据，不复制。 |
| 第 29 节字幕 | 全文读取：虚拟购物车、逐项结账、低库存、范围视图与放弃购物车，见预留与结账、放弃购物车。练习测试数据及完整程序不复制。 |
| 27、28、29 节源码 TXT，28 节 ProductData TXT | 四份均只含下载链接，已读链接文本但未下载或核对源码及数据；因此无法确认字幕未展示的实现细节。 |

课程字幕使用 record、枚举、`LocalDate` 等，但未确认具体 JDK 版本。检查环境 OpenJDK 21.0.12.1；不能据此推断课程版本。课程先合并购物车数量再预留、逐项销售及按 ID 前缀和年内日清理的限制已在清单说明。

## 核验

- Oracle Java SE 21 [Map](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Map.html)、[EnumMap](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/EnumMap.html)、[NavigableSet](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/NavigableSet.html)：核对 merge、键顺序、范围视图和 poll 行为。
- 编译并运行临时 Java 示例，检查 merge 累加、EnumMap/TreeMap 顺序和 headSet 视图移除；结果见下方实际记录。
- 完整库存业务及课程源码未运行；仅从字幕提炼，不能声称已验证完整程序。
- 实际执行 `javac /tmp/Inventory25Check.java` 和 `java -cp /tmp Inventory25Check`：通过，输出 `merge, order, backed view: OK`；`git diff --check` 通过。示例为独立最小复现，不是课程源码。

下一步由 #26 将本清单与其他主题整合、检查重复和全模块覆盖，并更新共享任务与进度文件；若取得源码，可补核对未展示的实现细节。
