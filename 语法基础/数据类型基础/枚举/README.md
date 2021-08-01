# 枚举类型

枚举类型（Enumerated Type）很早就出现在编程语言中，它被用来将一组类似的值包含到一种类型当中。而这种枚举类型的名称则会被定义成独一无二的类型描述符，在这一点上和常量的定义相似。不过相比较常量类型，枚举类型可以为申明的变量提供更大的取值范围。为了改进 Java 语言在这方面的不足弥补缺陷，5.0 版本 SDK 发布时候，在语言层面上增加了枚举类型。枚举类型的定义也非常的简单，用 enum 关键字加上名称和大括号包含起来的枚举值体即可：

```java
// 定义一周七天的枚举类型
public enum WeekDay { Mon, Tue, Wed, Thu, Fri, Sat, Sun }

// 读取当天的信息
WeekDay today = readToday();

// 根据日期来选择进行活动
switch(today) {
 Mon: do something; break;
 Tue: do something; break;
 Wed: do something; break;
 Thu: do something; break;
 Fri: do something; break;
 Sat: play sports game; break;
 Sun: have a rest; break;
}
```

最直接的益处就是扩大 switch 语句使用范围。5.0 之前，Java 中 switch 的值只能够是简单类型，比如 int、byte、short、char, 有了枚举类型之后，就可以使用对象了。需要注意的是，Java 中的枚举类型实际上会被编译为类文件，值即是这个类型的成员变量，譬如我们丰富下前文的枚举类型：

```java
public enum WeekDay {
  Mon("Monday"),
  Tue("Tuesday"),
  Wed("Wednesday"),
  Thu("Thursday"),
  Fri("Friday"),
  Sat("Saturday"),
  Sun("Sunday");
  private final String day;

  private WeekDay(String day) {
    this.day = day;
  }

  public static void printDay(int i) {
    switch (i) {
      case 1:
        System.out.println(WeekDay.Mon);
        break;
      // ...
        default:
        System.out.println("wrong number!");
    }
  }

  public String getDay() {
    return day;
  }
}
```

# 自定义枚举类型

在 JDK5 之前，Java 语言不支持枚举类型，只能用类（class）来模拟实现枚举类型。

```java
/** 订单状态枚举 */
public final class OrderStatus {
  /** 属性相关 */
  /** 状态取值 */
  private final int value;

  /** 状态描述 */
  private final String description;

  /** 常量相关 */
  /** 已创建(1) */
  public static final OrderStatus CREATED = new OrderStatus(1, "已创建");

  /** 进行中(2) */
  public static final OrderStatus PROCESSING = new OrderStatus(2, "进行中");

  /** 已完成(3) */
  public static final OrderStatus FINISHED = new OrderStatus(3, "已完成");

  /** 构造函数 */
  private OrderStatus(int value, String description) {
    this.value = value;
    this.description = description;
  }

  /** 获取状态取值 */
  public int getValue() {
    return value;
  }

  /** 获取状态描述 */
  public String getDescription() {
    return description;
  }
}
```
