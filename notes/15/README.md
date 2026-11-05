# 第 15 模块：集合框架、List、Set 与 Map

## 集合框架与 Collections

### 接口与实现

集合框架用接口表达行为，用实现类提供存储方式，用算法处理不同实现。`Collection<E>` 管理一组元素：`add`、`addAll`、`remove`、`removeIf`、`removeAll`、`retainAll`、`clear`、`contains`、`containsAll` 和 `iterator` 是常见操作；修改操作可能不受具体集合支持。`List`、`Set`、`Queue` 扩展集合接口；`Map` 属于框架，但不继承 `Collection`，它用唯一键关联值，值可以重复。数组和 `Arrays` 工具类不属于集合框架。

- `List` 有顺序和下标，通常允许重复。`ArrayList` 基于数组，`LinkedList` 基于链；两者都支持下标操作，但后者按下标访问可能需要遍历。`List` 还提供 `get`、`set`、按下标 `add`/`remove`、`indexOf`、`lastIndexOf`、双向 `listIterator`、`subList` 和 `sort`。
- `Set` 不允许重复；`HashSet` 不保证迭代顺序，`LinkedHashSet` 保持插入顺序，`TreeSet` 按排序规则排列。`SortedSet` 表示有排序的集合。
- `Queue` 保存待处理元素，常见队列先进先出；`Deque` 支持两端操作，也能实现后进先出的栈。`LinkedList` 同时实现 `List` 和 `Deque`。优先队列按优先级取元素，并非一般意义的后进先出栈。

接口类型决定能调用的方法，实现类型决定实际行为。例如 `Collection<String> c = new ArrayList<>();` 能使用 `c.add("A")`，却不能调用 `c.sort(...)`：`sort` 属于 `List`，不是 `Collection`。将实现换成 `HashSet` 后，重复和迭代顺序的行为也会改变。`Collections` 是工具类，不是整个框架；它的静态多态算法可用于符合要求的不同实现。

### 填充与复制

`new ArrayList<>(52)` 只预留容量，初始大小仍是 0。`Collections.fill(list, x)` 以同一个引用替换已有位置，不添加元素；`Arrays.fill` 则填充数组已有槽位。`Collections.nCopies(n, x)` 返回包含 n 个相同引用的不可修改列表；复制到 `ArrayList` 才能修改列表结构，元素本身仍是浅拷贝。

```java
List<String> cards = new ArrayList<>(52);
Collections.fill(cards, "A");          // 仍为空
cards.addAll(Collections.nCopies(3, "A"));
Collections.copy(cards, List.of("K", "Q")); // [K, Q, A]
```

`Collections.copy(dest, src)` 从头覆盖已有位置，目标大小不足抛 `IndexOutOfBoundsException`，不会扩容或返回新列表。`Collections.addAll(dest, elements...)` 接收可变参数或数组；`dest.addAll` 接收集合。`List.copyOf(source)` 返回不可修改的浅拷贝，拒绝 null；`new ArrayList<>(source)` 得到可修改的浅拷贝。`Arrays.asList(array)` 是数组支持的定长列表，可替换元素但不能增删，双方的元素替换互相可见。

### 重排、排序和查找

| 操作 | 规则 |
| --- | --- |
| `shuffle(list)` | 随机重排已有元素；可传 `Random` 控制随机源。 |
| `reverse(list)` / `swap(list, i, j)` | 原地反转 / 交换两个位置。 |
| `rotate(list, distance)` | 正数将末尾元素移到前面，负数将开头移到末尾，距离按大小循环。 |
| `sort(list)` / `sort(list, comparator)` | 按自然顺序 / 比较器排序；前者要求元素可比较。也可调用 `list.sort(comparator)`。 |
| `indexOfSubList(source, target)` / `lastIndexOfSubList` | 查找连续且顺序一致的子序列起点，未找到返回 -1；不同于 `containsAll` 只要求每个目标元素存在。 |
| `disjoint(a, b)` | 两集合无共同元素时为 true。 |
| `frequency(c, x)` | 按相等性统计出现次数。 |
| `min(c, comparator)` / `max` | 按自然顺序或比较器选极值；空集合抛 `NoSuchElementException`。 |
| `replaceAll(list, old, replacement)` | 替换所有相等元素，发生替换返回 true；与 `list.replaceAll(operator)` 对每个元素执行函数不同。 |

这些原地操作需要列表支持相应的替换操作，不可修改列表通常会抛 `UnsupportedOperationException`。`subList(from, to)` 的结束下标不包含在范围内，是原列表的视图；需要独立的列表结构时用 `new ArrayList<>(list.subList(from, to))`，这仍只复制元素引用。

二分查找必须先按相同的自然顺序或比较器排序。找到重复值时不保证返回哪一个；未找到返回 `-(插入点) - 1`，不能直接用负数作为下标。未按相同顺序排序时结果未定义；反转后仍用原比较器查找就是错误例子。`indexOf` 不要求排序，使用相等性查找；二分查找使用比较顺序，二者的匹配规则可能不同。性能取决于列表实现和数据，不能仅凭重复元素断言哪种方法更快。

```java
List<Integer> values = new ArrayList<>(List.of(4, 1, 3));
values.sort(null);                              // [1, 3, 4]
int index = Collections.binarySearch(values, 2); // -2，插入点 1
Collections.rotate(values, 1);                 // [4, 1, 3]
```

### 纸牌练习中的选择

课程以 `Card` record 保存花色、牌面和 rank，嵌套枚举表示四种花色。每花色 2—10 对应 rank 0—8，J、Q、K、A 对应 9—12；四花色组成 52 张牌。花色符号使用 Unicode 字符，不是 ASCII。record 的组件引用不可重新赋值，若组件自身可变则并不自动深度不可变。

五张抽牌练习先洗牌、`rotate` 切牌，再逐轮向每位玩家发一张。按 rank 降序、花色作次序排序后，用 `frequency` 找重复牌面；有序手牌中同牌面连续，可用首次和末次下标计算个数并用 `subList` 取出保留牌。统计对子、三条等，再从手牌副本移除保留牌，建议最多弃三张；`min`/`max` 可以按选定比较器求最小和最大牌。课程只评估重复牌面相关牌型，并未实现完整扑克规则或完整游戏。

剩余牌可用 `new ArrayList<>(deck.subList(dealt, deck.size()))`。课程也演示先以 `nCopies` 创建占位元素再 `replaceAll`；仅设容量不会创建元素，因而对空列表执行 `replaceAll` 不会填入任何牌。发牌前应保证玩家数乘手牌数不超过牌数。

## 哈希、HashSet 与集合运算

### 相等与哈希

引用的 `==` 判断是否为同一对象；`equals` 可以定义值相等。`Object.equals` 默认按身份比较，而 `String.equals` 按字符比较。哈希集合用 `hashCode` 帮助定位候选，再判断相等；哈希碰撞不代表对象相等。

- `equals` 为真时，两对象的 `hashCode` 必须相同；反过来不成立。相等性比较所用信息不变时，同一次运行中的哈希码须稳定，不要求跨运行相同。
- 自定义值相等时同时实现 `equals` 和 `hashCode`，使用一致的身份字段。常量哈希码满足相等对象哈希相同的要求，但会造成大量碰撞，影响性能；随机或时间值不适合作哈希码。
- 对象在 Set 内时不要改变参与相等判断的字段，否则集合行为未指定；应先删除，再修改并重新加入。课上任务的 project 和 description 可编辑，正有这个风险。

```java
record Card(String suit, String face) {} // record 按组件生成 equals/hashCode
var cards = new HashSet<Card>();
cards.add(new Card("hearts", "A")); // true
cards.add(new Card("hearts", "A")); // false，值相等
```

哈希将整数映射到有限桶，不需要为每个可能的整数分配一个桶。良好分布能减少候选比较，但桶数增加并不保证耗时恰好按比例减少。常见实现使用 31 组合字段；这不是正确性要求，具体分布取决于数据和算法。

### Set 和联系人

`Set` 不含相等的重复元素，没有按索引获取的方法，可用增强 for 或迭代器遍历。顺序取决于实现：`HashSet` 不保证遍历顺序，允许一个 `null`；其他实现可能拒绝 `null`。`add`、`remove` 返回集合是否改变，`contains` 查询成员，`clear` 清空。`HashSet` 的基本操作在哈希分布良好时通常为常数时间，底层由 `HashMap` 支撑；遍历成本还与容量有关。

```java
Set<String> names = new HashSet<>(List.of("Mickey", "Mickey", "Minnie"));
names.contains("Mickey"); // true
names.remove("Minnie");   // true
```

联系人练习将邮箱和电话分别放进 Set，构造时过滤空邮箱，将非零的十位电话格式化；合并时建立新联系人，复制原集合并 `addAll` 另一份数据。课程用 `Scanner` 读取字符串中的行、按逗号切分、去空白并将电话解析为 `long`，模拟两个数据源。此简化格式假设数据合法，真实电话可能有前导零，应保留为字符串。

若联系人按姓名判等，两个不同实例同名也被视为重复；`add` 不会合并或替换已有对象的邮箱和电话。若姓名、邮箱、电话全部参与判等，同名而其他数据不同就不是重复。应先明确身份规则，再显式合并其他字段；同名也不一定是同一个真实人。

### 集合运算

下例先复制，批量操作改变的是接收集合，不改变输入；复制是浅复制，元素本身仍共享。这里 `A={1,2}`，`B={2,3}`：

```java
Set<Integer> a = Set.of(1, 2), b = Set.of(2, 3);
Set<Integer> union = new HashSet<>(a);
union.addAll(b);                       // {1, 2, 3}
Set<Integer> intersection = new HashSet<>(a);
intersection.retainAll(b);             // {2}
Set<Integer> difference = new HashSet<>(a);
difference.removeAll(b);               // {1}，即 A − B
Set<Integer> symmetric = new HashSet<>(union);
symmetric.removeAll(intersection);     // {1, 3}
a.containsAll(intersection);           // true，判断是否包含全部
```

并集、交集和对称差在成员意义上可交换；差集有方向，`A − B` 与 `B − A` 通常不同。对称差也可写成 `(A − B) ∪ (B − A)`。相等对象可能有未参与判等的不同字段：例如同名联系人分别带邮箱和电话，交集保留接收集合中的对象，交换输入后虽然集合按相等性相同，保留下来的附加数据可能不同。集合运算不会自动合并这些数据。

### 任务练习：从问题选运算

任务以项目和描述共同确定身份，负责人、优先级、状态不参与判等；自然顺序先按项目再按描述。先复制到列表再排序，可用自然顺序或按优先级的比较器；枚举自然顺序是声明顺序，例如 HIGH、MEDIUM、LOW。数据按行和逗号拆分，状态文本转大写并将空格换为下划线后传给 `valueOf`；输入不合法或字段缺失需要另行处理。

设主管清单为 `M`，三位员工清单为 `A`、`B`、`C`：

- 已分配：`A ∪ B ∪ C`；完整清单：`M ∪ A ∪ B ∪ C`；主管遗漏：完整清单 `− M`。
- 未分配：`M − (A ∪ B ∪ C)`。
- 分配给至少两人：`(A ∩ B) ∪ (B ∩ C) ∪ (A ∩ C)`；三者交集只找分配给所有三人的任务。

如果要展示每位负责人的详情，应分别把每位员工集合与重叠任务求交，再放入列表，避免 Set 按任务身份去重而丢失不同负责人的记录。

## 有序集合与座位练习

### 选择集合与排序

| 实现 | 遍历顺序 | 基本操作 `add`、`contains`、`remove` |
| --- | --- | --- |
| `HashSet` | 不保证顺序 | 哈希分布良好时预期 O(1) |
| `LinkedHashSet` | 插入顺序；重复添加已有元素不会移动它 | 哈希分布良好时预期 O(1)，维护链表有额外开销 |
| `TreeSet` | 自然顺序或构造时指定的比较器顺序 | O(log n) |

`LinkedHashSet` 扩展 `HashSet`，通过链表维护顺序；它不是 `NavigableSet`。`TreeSet` 基于树，自动保持排序，适合同时需要去重、排序和范围查询的场景。课程中的树图是概念图，不意味着根节点总是数据的中位数。

`Collection → Set → SortedSet → NavigableSet` 是接口继承关系，`TreeSet` 实现 `NavigableSet`。变量声明为 `Set` 时不能直接调用导航方法；需要这些方法时可声明为 `NavigableSet`。

```java
NavigableSet<String> names = new TreeSet<>(); // String 的自然顺序
names.addAll(List.of("Linus", "Daffy", "Charlie"));
// 自定义类型可实现 Comparable，或者构造时传 Comparator
record Contact(String name, String email) {}
Comparator<Contact> byName = Comparator.comparing(Contact::name);
NavigableSet<Contact> contacts = new TreeSet<>(byName);
```

未提供比较器时，元素必须能按自然顺序相互比较；否则插入时可能抛 `ClassCastException`。`TreeSet` 用比较结果为零判断元素是否重复：若只按姓名比较，同名联系人会被当成一个元素，即便其他字段不同。排序应与 `equals` 一致，才能遵守 `Set` 的通用约定；需要区分同名者时应补充唯一键。不要在元素存入集合后修改参与排序的字段，否则树的顺序和查找会失效。

从 `SortedSet` 构造 `TreeSet` 会沿用其排序；`comparator()` 返回指定比较器，自然顺序时返回 `null`。`first()` / `last()` 读取首末元素，空集合抛 `NoSuchElementException`；`pollFirst()` / `pollLast()` 读取并移除，空集合返回 `null`。

### 导航和范围

以下比较均按集合当前的排序；无符合元素时返回 `null`。

| 方法 | 返回与目标最接近的元素 |
| --- | --- |
| `lower(x)` | 严格小于 x 的最大元素 |
| `floor(x)` | 小于或等于 x 的最大元素 |
| `ceiling(x)` | 大于或等于 x 的最小元素 |
| `higher(x)` | 严格大于 x 的最小元素 |

```java
NavigableSet<Integer> s = new TreeSet<>(List.of(10, 20, 30));
s.floor(20);    // 20
s.lower(20);    // 10
s.ceiling(21);  // 30
s.higher(30);   // null
```

`headSet(to)` 默认为 `< to`；`tailSet(from)` 默认为 `>= from`；`subSet(from, to)` 默认为 `[from, to)`。带布尔参数的重载可分别指定边界是否包含；边界本身不必在集合中。

```java
NavigableSet<Integer> middle = s.subSet(10, true, 30, false); // 10、20
middle.remove(10);                 // s 也删除 10
s.descendingSet().pollFirst();     // 删除 s 的最大值 30
```

范围集合和 `descendingSet()` 是原集合的**视图**，双方修改会反映到另一方；视图添加越界元素会抛 `IllegalArgumentException`。需要独立的集合结构时可用 `new TreeSet<>(middle)` 复制，元素对象本身仍是共享引用。降序视图里的首末和导航方向按降序解释。

### 座位练习：查找与连续区间

课程把 `Seat` 作为 `Theatre` 的嵌套类，包含座位编号和预订标志；行号 A—Z、每行座位数相同，编号例如 `A005`，按编号比较并放进 `TreeSet`。三位补零让限定范围内的字符串编号按数值顺序排列；实际使用须约束位数，或直接按行和数字比较。`reserved` 不参与比较，修改预订状态不会改变排序。

单座预订用 `floor(request)` 或 `ceiling(request)` 取出集合中存储的对象，再检查返回值非空且编号与请求完全相同；最近值不一定是目标座位。匹配且未预订才设置标志，否则报告不存在或已预订。`contains` 能判断存在，但不返回存储的对象；`first`、`last` 可说明有效范围（须先保证集合非空）。

连续预订先检查请求数量为正、行和座位范围有效，且 `maxSeat - minSeat + 1 >= count`。逐行取 `subSet(首座, true, 末座, true)`，顺序扫描：遇已预订座位就将连续计数归零；找到足够数量后才统一标记预订。只在一行内扫描，避免跨行拼接；未找到时不修改任何座位。课上返回新 `TreeSet` 保存选中座位的结构，但其中的座位对象仍与原集合共享。

## Map：键值映射、更新与视图

### 键与值

`Map<K, V>` 的键和值都是引用类型；每个键至多对应一个值。`HashMap` 不保证遍历顺序；`LinkedHashMap` 默认按插入顺序，`TreeMap` 按键排序。

```java
Map<String, Integer> counts = new HashMap<>();
counts.put("Ada", 1);                 // 新键，返回 null
counts.put("Ada", 2);                 // 替换旧值，返回 1
counts.putIfAbsent("Ada", 3);         // 已有非 null 值，保留 2，返回 2
counts.get("Ada");                    // 2；没有该键时返回 null
counts.getOrDefault("Lin", 0);        // 0，但不会添加 Lin
counts.containsKey("Ada");            // true
counts.forEach((key, value) -> System.out.println(key + ": " + value));
```

`HashMap` 允许 null 键和值；`get` 和 `put` 返回 null 时，原值可能是 null，也可能不存在，可用 `containsKey` 区分。`getOrDefault` 只在键不存在时返回默认值，已有键映射到 null 时仍返回 null。`putIfAbsent` 将不存在或映射到 null 的键视为待填入。不同 Map 实现对 null 的限制可能不同。

### 合并和计算

联系人按姓名归并时，可以逐条把信息合入同一个键。`merge(key, value, function)` 在没有非 null 旧值时放入提供的非 null 值；否则函数接收旧值、新值并将结果作为新值。函数返回 null 时删除该映射。

```java
Map<String, Integer> counts = new HashMap<>();
counts.merge("Ada", 1, Integer::sum);  // 不存在：放入 1
counts.merge("Ada", 2, Integer::sum);  // 已存在：1 + 2 = 3
```

| 方法 | 何时调用函数 | 函数参数与结果 |
| --- | --- | --- |
| `compute(k, f)` | 无论键是否存在 | 键、旧值（不存在时为 null）；返回新值 |
| `computeIfAbsent(k, f)` | 键不存在或值为 null | 只有键；返回要填入的值 |
| `computeIfPresent(k, f)` | 有非 null 旧值 | 键、旧值；返回新值 |

`compute` 和 `computeIfPresent` 的函数返回 null 时删除已有映射；`computeIfAbsent` 返回 null 时不填入。函数运行期间不要修改同一个 Map。

```java
counts.computeIfAbsent("Lin", k -> k.length()); // 3
counts.computeIfPresent("Lin", (k, v) -> v + 1); // 4
counts.compute("Lin", (k, v) -> v == null ? 1 : v + 1); // 5
counts.replaceAll((k, v) -> v * 2);               // 对每个映射替换值
```

### 替换与删除

- `replace(k, newValue)` 仅在键存在时替换，返回旧值；不存在返回 null。允许 null 值的 Map 中，返回 null 也可能是旧值本身。
- `replace(k, expected, newValue)` 仅当键存在且当前值与 expected 相等时替换，返回是否成功；值的相等性由 `equals` 决定，而不是看对象展示的全部字段。
- `remove(k)` 删除键对应映射并返回旧值；`remove(k, expected)` 仅在键和值匹配时删除，返回是否成功。
- `clear()` 删除所有映射。键和对象中的某个字段没有自动同步的要求；例如键为简称，值中的姓名可以是全名。

```java
counts.replace("Ada", 6, 7); // 只有当前值为 6 才替换
counts.remove("Lin", 10);    // 只有当前值为 10 才删除
```

### 三种集合视图

视图由原 Map 支撑：原 Map 的变化可从视图看到，通过支持的视图删除操作也会删除原映射。`HashMap` 的这些视图支持删除、`removeAll`、`retainAll` 和 `clear`，不支持 `add` 或 `addAll`；其他 Map 的可选修改操作依实现而定。

| 方法 | 返回类型 | 内容 |
| --- | --- | --- |
| `keySet()` | `Set<K>` | 不重复的键 |
| `values()` | `Collection<V>` | 值，允许重复 |
| `entrySet()` | `Set<Map.Entry<K, V>>` | 键值对；通过 `getKey()`、`getValue()` 读取 |

```java
Map<String, Integer> map = new HashMap<>();
map.put("Ada", 1);
map.put("Lin", 1);
Set<String> keys = map.keySet();
Set<String> copy = new TreeSet<>(keys); // 独立集合，按键排序
keys.remove("Ada");                    // 原 Map 的 Ada 映射也被删除
copy.remove("Lin");                    // 原 Map 不受影响
// keys.add("Bo");                     // 编译通过，运行抛 UnsupportedOperationException
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    System.out.println(entry.getKey() + "=" + entry.getValue());
}
```

`values()` 可传给 `ArrayList` 后排序，或传给 `HashSet` 去重；两者都是独立集合，但元素对象本身并未克隆。对于允许修改的 entry，`setValue` 可更新对应映射。遍历视图时若要删除，使用迭代器的 `remove`；不要在遍历过程中通过 Map 直接结构性修改。

## Map 练习、有序映射与枚举集合

### 冒险游戏：用 Map 表达地点和方向

外层 `Map<String, Location>` 用地点名查找地点；每个地点的 `Map<Compass, String>` 将方向映射到下一个地点名。先在当前地点的方向表中查目的地，再用目的地名查外层地图。不存在的方向或目的地要提示用户，不能当成有效移动。

```java
enum Compass { N, E, S, W }
record Location(String description, Map<Compass, String> exits) {}
Map<String, Location> board = new HashMap<>();
board.put("road", new Location("道路", Map.of(Compass.N, "forest")));
board.put("forest", new Location("森林", Map.of(Compass.S, "road")));
String destination = board.get("road").exits().get(Compass.N); // forest
Location next = board.get(destination);                         // 森林
```

课程把每行资料按换行拆开，再用 `split(",", 3)` 保留第三段中的方向列表；每个方向项再按冒号拆分，并用 `Compass.valueOf` 转换名称。解析前应处理空输入、非法方向和缺失字段；简单按逗号拆分不支持带引号的通用 CSV。

先加载默认地点，再用 `put` 加载用户自定义地点：相同地点名会替换整个 `Location`，包括其方向表，因此保留的出口也须写进新值。输入循环可规范化方向、检查出口并在退出命令时结束；取输入首字符前须处理空行。

### 插入顺序与键排序

- `LinkedHashMap` 默认按键首次插入的顺序迭代；更新已有键的值不会改变位置。也可通过构造器选择访问顺序。
- `TreeMap` 按键的自然顺序或指定的 `Comparator` 排序；自然排序要求键可比较。比较结果为零的键被视为同一个键，因此比较器应与预期的键相等关系一致。
- 例如购买记录按插入顺序保存在 `LinkedHashMap`，学生按姓名、购买按 `LocalDate` 分组保存在 `TreeMap`。同一天可有多条记录，值应是列表而非单条记录：

```java
NavigableMap<LocalDate, List<String>> purchases = new TreeMap<>();
LocalDate day = LocalDate.of(2025, 1, 3);
purchases.computeIfAbsent(day, k -> new ArrayList<>()).add("Java");
purchases.computeIfAbsent(day, k -> new ArrayList<>()).add("Python");
// 统计课程出现次数：缺键置 1，有值则加 1
Map<String, Integer> counts = new TreeMap<>();
for (String course : purchases.get(day)) counts.merge(course, 1, Integer::sum);
```

### NavigableMap：边界、导航与视图

`TreeMap` 实现 `NavigableMap`，可声明为 `NavigableMap<K,V>` 以使用导航方法。下表按映射的比较顺序理解；无匹配时导航方法返回 `null`。

| 方法 | 选中的键 |
| --- | --- |
| `lowerKey` / `lowerEntry` | 严格小于目标的最大键 |
| `floorKey` / `floorEntry` | 小于或等于目标的最大键 |
| `ceilingKey` / `ceilingEntry` | 大于或等于目标的最小键 |
| `higherKey` / `higherEntry` | 严格大于目标的最小键 |

`firstKey` / `lastKey` 在空映射上抛 `NoSuchElementException`；`firstEntry` / `lastEntry` 返回 `null`。`pollFirstEntry` / `pollLastEntry` 返回并删除首尾映射，空时返回 `null`。按已有键倒序逐项走访时用 `lowerEntry(key)`；`floorEntry(key)` 会再次返回当前项，循环可能无法前进。

```java
NavigableMap<Integer, String> m = new TreeMap<>(Map.of(1, "a", 2, "b", 3, "c"));
NavigableMap<Integer, String> first = m.headMap(2, true); // 1、2；默认 headMap(2) 不含 2
NavigableMap<Integer, String> last = m.tailMap(2, true);  // 2、3；默认包含 2
NavigableMap<Integer, String> middle = m.subMap(1, true, 3, false); // [1, 3)
first.remove(1);               // 视图修改同步影响 m
m.descendingMap().pollFirstEntry(); // 删除原映射中的最大键 3
```

`headMap`、`tailMap`、`subMap` 及 `descendingMap` 都是源映射的视图，双向反映修改；向范围视图放入越界键会抛 `IllegalArgumentException`。`descendingKeySet()` 是逆序键视图；逆序映射用 `descendingMap()`。逆序视图中大小和方向依其逆序比较器理解。普通 `keySet`、`values`、`entrySet` 也是视图。

### EnumSet 与 EnumMap

两者针对单一枚举类型：`EnumSet` 存枚举常量，`EnumMap` 以枚举常量为键；迭代顺序为常量声明顺序，而非插入顺序。`EnumSet` 是抽象类，通过静态工厂创建，底层用位向量；`EnumMap` 用数组表示键值映射。通常紧凑高效，但性能优势不是保证。两者不允许 `null` 元素或键，`EnumMap` 允许 `null` 值。

```java
enum Day { SUN, MON, TUE, WED, THU, FRI, SAT }
EnumSet<Day> ann = EnumSet.of(Day.MON, Day.TUE, Day.THU, Day.FRI);
EnumSet<Day> all = EnumSet.allOf(Day.class);
EnumSet<Day> available = EnumSet.complementOf(ann); // SUN、WED、SAT
EnumSet<Day> business = EnumSet.range(Day.MON, Day.FRI); // 两端均包含
EnumSet<Day> empty = EnumSet.noneOf(Day.class);
Map<Day, String> crew = new EnumMap<>(Day.class);
crew.put(Day.FRI, "Ann");
crew.put(Day.MON, "Bob"); // 迭代时 MON 在 FRI 之前
```

`EnumSet.copyOf` 可复制非空普通集合；空的普通集合无法推断枚举类型，应使用 `noneOf(Day.class)`。`EnumMap` 没有无参构造器，可传枚举类型的 `Class`、另一个 `EnumMap`，或含枚举键的非空普通 `Map`。

## 集合综合练习：商店库存

库存练习的重点是按查找方式、顺序和唯一性选集合，而不是实现完整商店。

### 模型与集合选择

- `Product` 保存 SKU（商品唯一编号）、名称、制造商和类别；课程用 `record` 表示不变的商品信息。`InventoryItem` 保存商品、价格、总库存、已预留量、低库存阈值和补货数量。
- 总库存包括购物车中尚未结账的商品；可用量为总库存减已预留量。加入购物车只增加预留量；移除或放弃购物车释放预留量；结账同时减少总库存和预留量。库存降到阈值或以下时触发补货提示。
- 购物车有 ID、日期、实体或虚拟类型；`Map<String, Integer>` 以 SKU 对应数量。重复加入同一商品可用 `merge(sku, qty, Integer::sum)` 累加；部分移除可用 `merge(sku, qty, (oldQty, removed) -> oldQty - removed)`，全部移除则删除键。先确保数量为正且不超过购物车数量。
- 店铺库存用 `HashMap<SKU, InventoryItem>` 按编号查找，不承诺遍历顺序。货架用 `EnumMap<Category, Map<String, InventoryItem>>` 按类别查找，内层 `TreeMap` 按名称排序；枚举类别按声明顺序遍历。同一类别中若名称重复，后一个映射会覆盖前一个，实际数据需要保证键唯一或换键。
- 购物车用按 ID 比较的 `TreeSet`，可用导航方法取范围；比较器认为相等的 ID 不能作为两个元素共存。`LocalDate.now().minusDays(n)` 可构造过去日期用于练习。

```java
// Category 是枚举，Item 是库存条目；假设类别和名称已取得。
Map<Category, Map<String, Item>> aisles = new EnumMap<>(Category.class);
aisles.computeIfAbsent(category, ignored -> new TreeMap<>()).put(name, item);
```

嵌套映射中的值可以与按 SKU 的库存映射指向同一个 `InventoryItem`；通过任一索引修改它，另一处看到的也是该对象的状态，而不是独立库存副本。

### 预留与结账

例如总库存 10、已预留 3，最多还能预留 7。成功预留 2 后总库存仍是 10、预留量变为 5；结账这 2 件后分别为 8 和 3。`entrySet()` 遍历购物车的 SKU 和数量，可查库存单价、计算每项金额和总额。

课程先在购物车 `merge`，再尝试预留；若库存不足，只打印错误会留下购物车数量与预留量不一致。应先验证并成功预留再更新购物车，且对失败操作保持两边一致。课程按条目逐一销售，途中失败可能留下部分已销售状态；需要全成或全不成的系统须先验证全部条目并提供原子更新或回滚。以上练习未处理并发和货币精度。

### 放弃购物车与范围视图

课程将旧购物车排在今日购物车之前，找到最后一个旧购物车，再使用 `headSet(lastOld, true)`。该集合是原 `TreeSet` 的视图；`pollFirst()` 返回并移除首元素，因而也从原集合移除。处理每辆车时还要按 SKU 和数量释放预留。

```java
NavigableSet<Integer> carts = new TreeSet<>(List.of(1, 2, 4));
NavigableSet<Integer> old = carts.headSet(2, true); // 包含 1、2
while (!old.isEmpty()) {
    int id = old.pollFirst(); // 实际场景还需释放该车的预留量
}
// carts 现在只剩 4
```

这个范围方法依赖排序与日期一致：按 ID 排序并不天然保证旧车排在前面，回填或乱序数据会漏掉旧车；无旧车时不能把空边界传给 `headSet`。课程只比较 `getDayOfYear()`，跨年同一日序会误判，应比较完整 `LocalDate`；若不能保证旧车连续，逐辆检查日期并通过迭代器移除更可靠。
