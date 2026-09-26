# 流程控制

# 运算符

## 一元运算符

### ++ / --

```java
public class Test{

    public static void main(String args[]){
        int i=0;
        i=i++;
        i=i++;
        System.out.println(i);

    }
}
// 输出结果为 0
```java
整个过程实际上如下所示:

```java
int oldValue = i;
i = i + 1;
i = oldValue;
```java
# 循环

## while

## for

### for-in

### forEach

# 迭代器
