# 第 15 模块 #23 资料核对：Map 基础、更新与视图

基点：`65574b7d7115c7af40a8de081c5baff4c70b642a`，集成分支 `notes/module-15-integration`。主题笔记：[Map](../../notes/15/23.md)。

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
