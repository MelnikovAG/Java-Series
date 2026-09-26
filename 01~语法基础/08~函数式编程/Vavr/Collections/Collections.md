# 集合

Vavr 实现了一套新的 Java 集合框架来匹配函数式编程范式，Vavr 提供的集合都是不可变的。Vavr 的 Stream 是惰性链表，元素只有在必要的时候才会参与计算，因此大部分操作都可以在常量时间内完成。一个持久化的集合在修改时，会产生一个新的版本，同时保留当前的版本。维护同一集合的多个版本可能会导致 CPU 和内存使用效率低下。然而，Vavr 集合库通过在一个集合的不同版本之间共享数据结构来克服这个问题。这与 Java 中 Collections 实用类的 unmodifiableCollection()有着本质的区别，后者只是提供了一个底层集合的包装器。试图修改这样一个集合的结果是 UnsupportedOperationException，而不是创建一个新的版本。而且，底层集合仍然可以通过它的直接引用进行修改。

在 Java 中使用 Stream，需要显示得将集合转成 Stream 的步骤，而在 Vavr 中则免去了这样的步骤。Vavr 的 List 是不可变的链表，在该链表对象上的操作都会生成一个新的链表对象。使用 Java 8 的代码：

```java
Arrays.asList(1, 2, 3).stream().reduce((i, j) -> i + j);
IntStream.of(1, 2, 3).sum();
```java
使用 Vavr 实现相同的功能，则更加直接：

```java
// io.vavr.collection.List
List.of(1, 2, 3).sum();
```java
## Java 转化我 Vavr

Vavr 中的每个集合实现都有一个静态的工厂方法 ofAll()，它接收一个 java.util.Iterable。这允许我们从一个 Java 集合中创建一个 Vavr 集合。同样的，另一个工厂方法 All()也直接取一个 Java Stream。

```java
java.util.List<Integer> javaList = java.util.Arrays.asList(1, 2, 3, 4);
List<Integer> vavrList = List.ofAll(javaList);

java.util.stream.Stream<Integer> javaStream = javaList.stream();
Set<Integer> vavrSet = HashSet.ofAll(javaStream);
```java
另一个有用的函数是 collector()，它可以和 Stream.collect()一起使用，以获得一个 Vavr 集合。

```java
List<Integer> vavrList = IntStream.range(1, 10)
  .boxed()
  .filter(i -> i % 2 == 0)
  .collect(List.collector());

assertEquals(4, vavrList.size());
assertEquals(2, vavrList.head().intValue());
```java
## Vavr 转化为 Java

```java
Integer[] array = List.of(1, 2, 3)
  .toJavaArray(Integer.class);
assertEquals(3, array.length);

java.util.Map<String, Integer> map = List.of("1", "2", "3")
  .toJavaMap(i -> Tuple.of(i, Integer.valueOf(i)));
assertEquals(2, map.get("2").intValue());

java.util.Set<Integer> javaSet = List.of(1, 2, 3)
  .collect(Collectors.toSet());

assertEquals(3, javaSet.size());
assertEquals(1, javaSet.toArray()[0]);
```java
# Seq（序列类型）

## List

```java
List<String> list = List.of(
  "Java", "PHP", "Jquery", "JavaScript", "JShell", "JAVA");

List list1 = list.drop(2);
assertFalse(list1.contains("Java") && list1.contains("PHP"));

List list2 = list.dropRight(2);
assertFalse(list2.contains("JAVA") && list2.contains("JShell"));

List list3 = list.dropUntil(s -> s.contains("Shell"));
assertEquals(list3.size(), 2);

List list4 = list.dropWhile(s -> s.length() > 0);
assertTrue(list4.isEmpty());
```java
drop(int n) 从列表中的第一个元素开始删除 n 个元素，而 dropRight() 从列表中的最后一个元素开始做同样的事情。dropUntil() 继续从列表中删除元素，直到谓词值为真，而 dropWhile()则在谓词为真时继续删除元素。接下来，take(int n) 用于从一个列表中抓取元素。它从列表中抓取 n 个元素，然后停止。还有一个 takeRight(int n)是从列表的末尾开始抓取元素。

```java
List list5 = list.take(1);
assertEquals(list5.single(), "Java");

List list6 = list.takeRight(1);
assertEquals(list6.single(), "JAVA");

List list7 = list.takeUntil(s -> s.length() > 6);
assertEquals(list7.size(), 3);
```java
最后，takeUntil() 继续从列表中提取元素，直到谓词为真。还有一个 takeWhile()的变体，也会取一个谓词参数。此外，API 中还有其他有用的方法，例如，实际上还有返回非重复元素列表的 distinct()，以及接受一个比较器来确定平等的 distinctBy()。非常有趣的是，还有 intersperse()，它可以在一个列表的每个元素之间插入一个元素。对于 String 操作来说，它可以非常方便。

```java
List list8 = list
  .distinctBy((s1, s2) -> s1.startsWith(s2.charAt(0) + "") ? 0 : 1);

assertEquals(list8.size(), 2);

String words = List.of("Boys", "Girls")
  .intersperse("and")
  .reduce((s1, s2) -> s1.concat( " " + s2 ))
  .trim();

assertEquals(words, "Boys and Girls");
```java
想把一个列表分成几类？那么，也有一个 API 可以实现。

```java
Iterator<List<String>> iterator = list.grouped(2);
assertEquals(iterator.head().size(), 2);

Map<Boolean, List<String>> map = list.groupBy(e -> e.startsWith("J"));
assertEquals(map.size(), 2);
assertEquals(map.get(false).get().size(), 1);
assertEquals(map.get(true).get().size(), 5);
```java
group(int n)将一个 List 划分为每组 n 个元素的组。groupdBy()接受一个包含划分列表逻辑的 Function，并返回一个有两个条目的 Map--true 和 false。true 键映射到满足 Function 中指定条件的元素的 List；false 键映射到不满足条件的元素的 List。正如预期的那样，当修改一个 List 时，原始 List 实际上并没有被修改。相反，总是返回一个新版本的 List。我们还可以使用堆栈语义与 List 进行交互--元素的最后进先出（LIFO）检索。在这个程度上，有一些 API 方法用于操作堆栈，如 peek()、pop()和 push()。

```java
List<Integer> intList = List.empty();

List<Integer> intList1 = intList.pushAll(List.rangeClosed(5,10));

assertEquals(intList1.peek(), Integer.valueOf(10));

List intList2 = intList1.pop();
assertEquals(intList2.size(), (intList1.size() - 1) );
```java
pushAll() 函数用来将一系列的整数插入到堆栈中，而 peek() 则用来获取堆栈的头部。还有 peekOption() 可以将结果包裹在 Option 对象中。更多操作如下：

- List Creation

```java
// java.util.List
List<String> animals = new ArrayList<>();
List<String> another = new ArrayList<>(animals);
List<String> animals = new LinkedList<>();
List<String> another = new LinkedList<>(animals);
List<String> animals = Arrays.asList("🐱", "🐶");
List<String> animals = Collections.singletonList("🐱");
List<String> animals = Collections.unmodifiableList(...);

// io.vavr.collection.List
List<String> animals = List.of("🐱", "🐶");
List<String> another = List.ofAll(animals);
List<String> empty = List.empty();
```java
- Add Element

```java
// java.util.List
List<String> animals = new ArrayList<>();
animals.add("🐱");
animals.add("🐶");
// "🐱", "🐶"
List<String> animals = new LinkedList<>();
animals.add("🐱");
animals.add("🐶");
// "🐱", "🐶"

// io.vavr.collection.List
List<String> animals = List.of("🐱", "🐶");
List<String> another = animals.prepend("🙂");
// animals: "🐱", "🐶"
// another: "🙂", "🐱", "🐶"
List<String> animals = List.of("🐱", "🐶");
List<String> another = animals.append("😌");
// animals: "🐱", "🐶"
// another: "🐱", "🐶", "😌"
```java
- Get Element

```java
// java.util.List
List<String> animals = Arrays.asList("🐱", "🐶");
animals.get(0)
// "🐱"

// io.vavr.collection.List
List<String> animals = List.of("🐱", "🐶");
animals.get();
// "🐱"
animals.head();
// "🐱"
animals.get(1);
// "🐶"
animals.last();
// "🐶"
```java
- Remove Element

```java
// Java
List<String> animals = Arrays.asList("🐱", "🐶");
List<String> animals = new ArrayList<>();
animals.add("🐱");
animals.add("🐶");
animals.remove(true); // remove(Object)
// "🐱", "🐶"
animals.remove("🐱");
// "🐶"

List<Integer> numbers = new ArrayList<>();
numbers.add(2);
numbers.add(3);
// numbers: 2, 3
numbers.remove(Ingeter.valueOf(1)); // remove(Object)
// numbers: 2, 3
numbers.remove(1); // remove(int)
// numbers: 2

// io.vavr.collection.List
List<String> animals = List.of("🐱", "🐶");
List<String> another = animals.remove("🐱");
// animals: "🐱", "🐶"
// another: "🐶"

List<Integer> numbers = List.of(2, 3);
List<Integer> another = numbers.removeAt(1);
// numbers: 2, 3
// another: 2
```java
- Streaming API

```java
// Java
Arrays.asList("🐱", "🐶")
      .stream()
      .map(s -> s + s)
      .collect(Collectors.toList());
// "🐱🐱", "🐶🐶"
Arrays.asList("🐱", "🐶")
      .stream()
      .filter("🐱"::equals)
      .collect(Collectors.toList());
// "🐱"
List<String> cats = Arrays.asList("🐱", "🐈");
List<String> dogs = Arrays.asList("🐶", "🐕");
List<List<String>> lists = Arrays.asList(cats, dogs);
List<String> animals = lists.stream().flatMap(Collection::stream).collect(Collectors.toList());
// "🐱", "🐈", "🐶", "🐕"

// Vavr
List.of("🐱", "🐶").map(s -> s + s);
// "🐱🐱", "🐶🐶"
List.of("🐱", "🐶").filter("🐱"::equals)
// "🐱"
List<String> cats = List.of("🐱", "🐈");
List<String> dogs = List.of("🐶", "🐕");
List<List<String>> lists = List.of(cats, dogs);
List<String> list = lists.flatMap(Function.identity());
// "🐱", "🐈", "🐶", "🐕"
```java
## Queue

一个不可改变的 Queue 存储元素，允许先入先出（FIFO）检索。一个 Queue 内部由两个链接的列表组成，一个前列表（Front List），一个后列表（Rear List）。前面的列表包含了去 quequeued 的元素，后面的列表包含了 enqueued 的元素。这使得 enqueue 和 dequeue 操作可以在 O(1)中执行。当前置 List 的元素用完时，前置 List 和后置 List 的元素被交换，后置 List 被反转。

```java
Queue<Integer> queue = Queue.of(1, 2);
Queue<Integer> secondQueue = queue.enqueueAll(List.of(4,5));

assertEquals(3, queue.size());
assertEquals(5, secondQueue.size());

Tuple2<Integer, Queue<Integer>> result = secondQueue.dequeue();
assertEquals(Integer.valueOf(1), result._1);

Queue<Integer> tailQueue = result._2;
assertFalse(tailQueue.contains(secondQueue.get(0)));
```java
dequeue 函数从 Queue 中移除头部元素，并返回一个 `Tuple2<T，Q>`。这个元组包含被移除的头部元素作为第一个条目，Queue 的其余元素作为第二个条目。我们可以使用组合(n)来获得 Queue 中所有可能的 N 种元素组合。

```java
Queue<Queue<Integer>> queue1 = queue.combinations(2);
assertEquals(queue1.get(2).toCharSeq(), CharSeq.of("23"));
```java
## Stream

Stream 是一个懒惰链接列表的实现，与 java.util.stream 有很大不同。与 java.util.stream 不同的是，Vavr Stream 存储的是数据，并且是懒惰地计算下一个元素。将 s.toString()的结果打印到控制台，只会显示 Stream(2，?)。这意味着只有 Stream 的头部被评估，而尾部没有被评估。

调用 s.get(3)并随后显示 s.tail()的结果会返回 Stream(1，3，4，?)。相反，如果不先调用 s.get(3)--这将导致 Stream 评估最后一个元素--s.tail()的结果将只有 Stream(1，?)。这意味着只评估了尾部的第一个元素。这种行为可以提高性能，并且使得使用 Stream 表示（理论上）无限长的序列成为可能。Vavr Stream 是不可改变的，可以是 Empty 或 Cons。一个 Cons 由一个头部元素和一个懒惰计算的尾部 Stream 组成。与 List 不同的是，对于 Stream，只有头部元素保存在内存中。尾元素是按需计算的。

```java
Stream<Integer> intStream = Stream.iterate(0, i -> i + 1)
  .take(10);

assertEquals(10, intStream.size());

long evenSum = intStream.filter(i -> i % 2 == 0)
  .sum()
  .longValue();

assertEquals(20, evenSum);
```java
相对于 Java 8 Stream API 而言，Vavr 的 Stream 是一种用于存储元素序列的数据结构。因此，它有 get()、append()、insert()等方法来操作其元素。前面考虑的 drop()、distinct()和其他一些方法也都可以使用。最后，我们快速演示一下 Stream 中的 tabulate()。该方法返回一个长度为 n 的 Stream，其中包含的元素是应用一个函数的结果。

```java
Stream<Integer> s1 = Stream.tabulate(5, (i)-> i + 1);
assertEquals(s1.get(2).intValue(), 3);
```java
我们还可以使用 zip()生成一个 Tuple2<Integer，Integer>的 Stream，其中包含的元素是由两个 Stream 组合而成。

```java
Stream<Integer> s = Stream.of(2,1,3,4);

Stream<Tuple2<Integer, Integer>> s2 = s.zip(List.of(7,8,9));
Tuple2<Integer, Integer> t1 = s2.get(0);

assertEquals(t1._1().intValue(), 2);
assertEquals(t1._2().intValue(), 7);
```java
## Array

数组是一个不可变的、有索引的、允许高效随机访问的序列。它是由一个 Java 对象数组支持的。本质上，它是一个 T 类型的对象数组的可遍历包装器。我们可以通过使用静态方法 of()来实例化一个 Array。我们也可以通过使用静态的 range()和 rangeBy()方法生成一个范围元素。rangeBy()有第三个参数，让我们定义步长。

range()和 rangeBy() 方法只会生成从开始值到结束值减一的元素。如果我们需要包含结束值，我们可以使用 rangeClosed()或 rangeClosedBy()。

```java
Array<Integer> rArray = Array.range(1, 5);
assertFalse(rArray.contains(5));

Array<Integer> rArray2 = Array.rangeClosed(1, 5);
assertTrue(rArray2.contains(5));

Array<Integer> rArray3 = Array.rangeClosedBy(1,6,2);
assertEquals(rArray3.size(), 3);
```java
让我们通过索引来操作元素：

```java
Array<Integer> intArray = Array.of(1, 2, 3);
Array<Integer> newArray = intArray.removeAt(1);

assertEquals(3, intArray.size());
assertEquals(2, newArray.size());
assertEquals(3, newArray.get(1).intValue());

Array<Integer> array2 = intArray.replace(1, 5);
assertEquals(array2.get(0).intValue(), 5);
```java
## Vector

向量是介于数组和列表之间的一种，它提供了另一个有索引的元素序列，允许在恒定的时间内随机访问和修改。

```java
Vector<Integer> intVector = Vector.range(1, 5);
Vector<Integer> newVector = intVector.replace(2, 6);

assertEquals(4, intVector.size());
assertEquals(4, newVector.size());

assertEquals(2, intVector.get(1).intValue());
assertEquals(6, newVector.get(1).intValue());
```java
## CharSeq

CharSeq 是一个集合对象，用于表达一个原始字符序列。它本质上是一个增加了集合操作的 String 包装器。

```java
CharSeq chars = CharSeq.of("vavr");
CharSeq newChars = chars.replace('v', 'V');

assertEquals(4, chars.size());
assertEquals(4, newChars.size());

assertEquals('v', chars.charAt(0));
assertEquals('V', newChars.charAt(0));
assertEquals("Vavr", newChars.mkString());
```java
# Set

在本节中，我们将详细介绍集合库中各种 Set 的实现。Set 数据结构的独特之处在于它不允许有重复的值。然而，Set 有不同的实现：HashSet 是基本的实现。TreeSet 不允许重复的元素，并且可以进行排序。LinkedHashSet 保持其元素的插入顺序。

## HashSet

HashSet 有静态的工厂方法用于创建新的实例--其中一些方法我们之前在本文中已经探讨过了--比如 of()、ofAll()和 range()方法的变体。我们可以通过使用 diff()方法获得两个集合之间的差异。另外，union()和 intersect()方法也会返回两个集合的联合集和交集。

```java
HashSet<Integer> set0 = HashSet.rangeClosed(1,5);
HashSet<Integer> set1 = HashSet.rangeClosed(3, 6);

assertEquals(set0.union(set1), HashSet.rangeClosed(1,6));
assertEquals(set0.diff(set1), HashSet.rangeClosed(1,2));
assertEquals(set0.intersect(set1), HashSet.rangeClosed(3,5));
```java
我们还可以进行基本的操作，如添加和删除元素：

```java
HashSet<String> set = HashSet.of("Red", "Green", "Blue");
HashSet<String> newSet = set.add("Yellow");

assertEquals(3, set.size());
assertEquals(4, newSet.size());
assertTrue(newSet.contains("Yellow"));
```java
## TreeSet

一个不可改变的 TreeSet 是 SortedSet 接口的一个实现。它存储一个排序元素的 Set，并使用二进制搜索树来实现。它的所有操作都以 O(log n)时间运行。默认情况下，TreeSet 的元素是按照自然顺序排序的。让我们创建一个使用自然排序顺序的 SortedSet。

```java
SortedSet<String> set = TreeSet.of("Red", "Green", "Blue");
assertEquals("Blue", set.head());

SortedSet<Integer> intSet = TreeSet.of(1,2,3);
assertEquals(2, intSet.average().get().intValue());
```java
要以自定义的方式对元素进行排序，可以在创建 TreeSet 时传递一个比较器实例。我们也可以从集合元素中生成一个字符串。

```java
SortedSet<String> reversedSet
  = TreeSet.of(Comparator.reverseOrder(), "Green", "Red", "Blue");
assertEquals("Red", reversedSet.head());

String str = reversedSet.mkString(" and ");
assertEquals("Red and Green and Blue", str);
```java
## BitSet

Vavr 集合还包含一个不可变的 BitSet 实现。BitSet 接口扩展了 SortedSet 接口。BitSet 可以使用 BitSet.Builder 中的静态方法来实例化。与 Set 数据结构的其他实现一样，BitSet 不允许向集合中添加重复的条目。

它继承了 Traversable 接口的操作方法。注意，它与标准 Java 库中的 java.util.BitSet 不同。BitSet 数据不能包含 String 值。让我们看看如何使用工厂方法 of()创建一个 BitSet 实例。

```java
BitSet<Integer> bitSet = BitSet.of(1,2,3,4,5,6,7,8);
BitSet<Integer> bitSet1 = bitSet.takeUntil(i -> i > 4);
assertEquals(bitSet1.size(), 4);
```java
我们使用 takeUntil()来选择 BitSet 的前四个元素。该操作返回一个新的实例。请注意，takeUntil()是在 Traversable 接口中定义的，Traversable 接口是 BitSet 的父接口。上面演示的其他方法和操作，是在 Traversable 接口中定义的，也同样适用于 BitSet。

# Map

Map 是一种键值数据结构。Vavr 的 Map 是不可变的，有 HashMap、TreeMap 和 LinkedHashMap 的实现。一般来说，Map 不允许重复的键：虽然可能有重复的值映射到不同的键。

## HashMap

HashMap 是一个不可变的 Map 接口的实现，它使用键的哈希码存储键值对。它使用键的哈希码来存储键值对。

```java
Map<Integer, List<Integer>> map = List.rangeClosed(0, 10)
  .groupBy(i -> i % 2);

assertEquals(2, map.size());
assertEquals(6, map.get(0).get().size());
assertEquals(5, map.get(1).get().size());
```java
类似于 HashSet，HashMap 的实现是由一个哈希数组映射的 Trie(HAMT)支持的，导致几乎所有的操作都是恒定的时间。我们可以使用 filterKeys()方法按键过滤映射条目，或者使用 filterValues()方法按值过滤。这两种方法都接受一个 Predicate 作为参数。

```java
Map<String, String> map1
  = HashMap.of("key1", "val1", "key2", "val2", "key3", "val3");

Map<String, String> fMap
  = map1.filterKeys(k -> k.contains("1") || k.contains("2"));
assertFalse(fMap.containsKey("key3"));

Map<String, String> fMap2
  = map1.filterValues(v -> v.contains("3"));
assertEquals(fMap2.size(), 1);
assertTrue(fMap2.containsValue("val3"));
```java
我们还可以通过使用 map()方法来转换 Map 条目。例如，让我们将 map1 转换为 Map<String, Integer>。

```java
Map<String, Integer> map2 = map1.map(
  (k, v) -> Tuple.of(k, Integer.valueOf(v.charAt(v.length() - 1) + "")));

assertEquals(map2.get("key1").get().intValue(), 1);
```java
更多 HashMap 用法如下：

```java
/** 创建 */
// Java
Map<String, String> map = new HashMap<>();
map.put("cat", "🐱");
map.put("dog", "🐶");

// VAVR
// Solution 1: of
Map<String, String> map = HashMap.of("cat", "🐱", "dog", "🐶");

// Solution 2: ofEntries
map = HashMap.ofEntries(Tuple.of("cat", "🐱"), Tuple.of("dog", "🐶"));

// Solution 3: ofAll
map = HashMap.ofAll(javaMap);

/** 遍历 */
// Java
for (Map.Entry<String, String> e : map.entrySet()) {
  System.out.println(e.getKey() + ": " + e.getValue());
}
// "cat: 🐱"
// "dog: 🐶"

// VAVR
for (Tuple2<String, String> t : map) {
  System.out.println(t._1 + ": " + t._2);
}
// "cat: 🐱"
// "dog: 🐶"

/** Entries Streaming */
// Java
List<String> list =
    map.entrySet()
        .stream()
        .map(e -> e.getKey() + ": " + e.getValue())
        .collect(Collectors.toList());

// VAVR
List<String> list = map.map(t -> t._1 + ": " + t._2).toList();

/** Side Effect */
// Java
String cat = map.get("cat");
System.out.println(cat.isEmpty());
// false

String duck = map.get("duck");
System.out.println(duck.isEmpty());
// NullPointerException! 💥

// VAVR
Option<String> cat = map.get("cat");
if (cat.isDefined()) {
  ...
}
```java
## TreeMap

一个不可改变的 TreeMap 是 SortedMap 接口的一个实现。与 TreeSet 类似，一个比较器实例用于自定义 TreeMap 的元素排序。我们来演示一下 SortedMap 的创建。

```java
SortedMap<Integer, String> map
  = TreeMap.of(3, "Three", 2, "Two", 4, "Four", 1, "One");

assertEquals(1, map.keySet().toJavaArray()[0]);
assertEquals("Four", map.get(4).get());
```java
默认情况下，TreeMap 的条目是按照键的自然顺序进行排序的。但是，我们可以指定一个用于排序的比较器。

```java
TreeMap<Integer, String> treeMap2 =
  TreeMap.of(Comparator.reverseOrder(), 3,"three", 6, "six", 1, "one");
assertEquals(treeMap2.keySet().mkString(), "631");
```java
# Links

- https://www.baeldung.com/vavr-collections
