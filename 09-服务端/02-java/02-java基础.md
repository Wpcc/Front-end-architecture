# Java基础

## 基本数据类型

- 整数：byte、short、int、long
- 浮点数：float、double
- 字符：char
- 布尔：boolean



**数据声明：**

随便写的整数是int类型，加上L/l就是long类型的数据。

随便写的小数默认是double类型，加上F/f就是float类型。



**数据比较：**

对于浮点数存在运算误差，故常用`math.abs`比较：

```java
// 比较x和y是否相等，先计算其差的绝对值:
double r = Math.abs(x - y);
// 再判断绝对值是否足够小:
if (r < 0.00001) {
    // 可以认为相等
} else {
    // 不相等
}
```



对于对象比较，`==`比较的是地址，而`equals`比较的是内容

```java
String zs = "zhangsan";
String zs2 = "zhangsan";

system.print.out(zs.equals(zs2))
```



### 枚举



```java
public enum StudentStatus {
    // 3、实现 -- 可以看作new ACTIVE
    ACTIVE(1, "在读", true),
    GRADUATED(2, "已毕业", false),
    SUSPENDED(3, "休学", true);

    private final int code;
    private final String description;
    private final boolean editable;

    // 1、定义构造函数
    StudentStatus(int code, String description, boolean editable) {
        this.code = code;
        this.description = description;
        this.editable = editable;
    }

    // 2、定义访问方法
    public int getCode() {
        return code;
    }

    public String getDescription() {
        return description;
    }

    public boolean isEditable() {
        return editable;
    }
}
```



### 集合框架

使用选择：

- 长度确定、性能要求高 → **数组 []**

- 长度不确定，频繁查询、末尾增删 → **ArrayList（90% 场景）**
- 频繁在中间插入删除 → **LinkedList**

- 需要元素去重 → **HashSet**

- 键值对存储 → **HashMap**



#### 数组

创建：

```java
int[] ns = new int[5];
    
int[] ns2 = new int[] {1,2,3,4,5};

int[] ns3 = {1,2,3,4,5};
```

遍历：

```java
// for循环
public class Main {
    public static void main(String[] args) {
        int[] ns = { 1, 4, 9, 16, 25 };
        for (int i=0; i<ns.length; i++) {
            int n = ns[i];
            System.out.println(n);
        }
    }
}
```

```java
// for each
// 遍历数组
public class Main {
    public static void main(String[] args) {
        int[] ns = { 1, 4, 9, 16, 25 };
        for (int n : ns) {
            System.out.println(n);
        }
    }
}
```

常见方法：

```java
// 转字符串
Arrays.toString(ns);
    
// 排序
Arrays.sort(ns);  
```



#### List

**创建：**

```java
List<String> list = new ArrayList<>();

// 不接受null值
List<Integer> list = List.of(1, 2, 5);
```



**遍历：**

通过for循环比较低效，通过Iterator高效，forEach内部实现了Iterator。

```java
// for循环：通过索引。缺点：LinkedList 访问速度慢
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<String> list = List.of("apple", "pear", "banana");
        for (int i=0; i<list.size(); i++) {
            String s = list.get(i);
            System.out.println(s);
        }
    }
}
```

```java
// 通过迭代器 Iterator：hasNext() 是否有下一个元素 next() 返回下一个元素
import java.util.Iterator;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<String> list = List.of("apple", "pear", "banana");
        for (Iterator<String> it = list.iterator(); it.hasNext(); ) {
            String s = it.next();
            System.out.println(s);
        }
    }
}
```

```java
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<String> list = List.of("apple", "pear", "banana");
        for (String s : list) {
            System.out.println(s);
        }
    }
}
```



**常见方法：**

```java
// 在末尾添加一个元素：boolean add(E e)
// 在指定索引添加一个元素：boolean add(int index, E e)
// 删除指定索引的元素：E remove(int index)
// 删除某个元素：boolean remove(Object e)
// 获取指定索引的元素：E get(int index)
// 获取链表大小（包含元素的个数）：int size()

// 是否包含某个元素：boolean contains(E e)
// 查找元素索引：int Indexof(E e)
```



List和Array转换：

List ==》Array

```java
// toArray方法会返回一个Object[]数组，会丢失信息，故很少使用
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<String> list = List.of("apple", "pear", "banana");
        Object[] array = list.toArray();
        for (Object s : array) {
            System.out.println(s);
        }
    }
}
```

```java
// 给toArray(T[])传入一个类型相同的Array，List内部会自动把元素复制到Array中
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<Integer> list = List.of(12, 34, 56);
        Integer[] array = list.toArray(new Integer[3]);
        for (Integer n : array) {
            System.out.println(n);
        }
    }
}

// 常用 Integer[] array = list.toArray(new Integer[list.size()]);
```

```java
// 函数式写法
Integer[] array = list.toArray(Integer[]::new);
```



Array ===》List

```java
// 转的List仅仅只读，不支持操作
Integer[] array = { 1, 2, 3 };
List<Integer> list = List.of(array);

List<String> list = Arrays.asList(array);
```

```java
// 包一层new ArrayList
String[] arr = {"A","B","C"};
// 包装一层，得到完整可操作List（支持add/remove/clear）
List<String> list = new ArrayList<>(Arrays.asList(arr));

list.add("D");
list.remove(0);
```



#### Map

**创建：**

```java
// 通常创建
Map<String, Integer> map = new HashMap<>();

// 如果key是enum的话，推荐enumMap创建，空间比hashMap紧凑
 Map<DayOfWeek, String> map = new EnumMap<>(DayOfWeek.class);

// 内部会对key进行排序
Map<String, Integer> map = new TreeMap<>();

// HashMap不支持并发写入，所以当存在多线程时，使用ConcurrentHashMap<String,ScheduledTask>
```



**遍历：**

```java
import java.util.HashMap;
import java.util.Map;

public class Main {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        map.put("apple", 123);
        map.put("pear", 456);
        map.put("banana", 789);
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            String key = entry.getKey();
            Integer value = entry.getValue();
            System.out.println(key + " = " + value);
        }
    }
}
```



**常见方法：**

```java
// 创建项 map.put("a", new Person("Xiao Ming"));
// 查询项 map.get("a"); // Person("Xiao Ming")
```



**hashcode：**

本质就是key对应的是内部索引，所以设置和获取的时候需要传递一样的对象，否则对应的索引就不一样了。





### 包装类

常见的包装类？

- Integer、Character

为啥有包装类？

- 基本数据类型不能使用泛型。

包装类的比较？

- 包装类是对象，所以不能单纯的用`==`比较，比较的是地址，需要用`equal`方法比较。

包装类的自动拆箱？

```java
// 以下面为列
Byte status = null;
```

它的意思是：

```java
status 是一个 Byte 类型的引用变量
↓
但现在没有指向任何 Byte 对象
↓
所以 status = null
```

那么拆箱就是

```java
status.byteValue();
↓
null.byteValue()
↓
NullPointerException
```

理解下面两个判断：

```java
Byte.valueOf((byte) 1).equals(activity.getStatus()
            
Boolean.TRUE.equals(firstRequest)  ---等于---
```



### 拆箱API

比如：

```
Boolean a = Boolean.TRUE;
Boolean b = Boolean.valueOf(true);
```

这两个最终得到的都是表示 `true` 的 `Boolean` 对象。



### 泛型类

**泛型类**：在定义类的时候，使用**类型形参**，让这个类可以适配多种数据类型，不用为每种类型单独写一套代码。

常见类型参数约定：

`T`：Type 任意类型

`E`：Element 元素（集合）

`K,V`：Key Value 键值对

`R`：返回值类型



定义：

```java
修饰符 class 类名<类型参数> {
}
```



比较：

```java
// 普通类
public class StringBox {
    private String data;
    public String getData(){return data;}
    public void setData(String data){this.data=data;}
}
```

```java
// 泛型类
public class Box<T> {
    private T data;
    public T getData(){return data;}
    public void setData(T data){this.data=data;}
}
```



```java
// 泛型推导
// <T> 声明方法泛型占位符 T
// 参数 IntFunction<T[]> generator：传入的函数，输入 int，产出 T[]
// 返回值 T[]：方法最终也要返回 T[]
default <T> T[] toArray(IntFunction<T[]> generator)
```



**泛型无法向上转型，这样 Java 才能保证泛型里的元素类型安全。**

```java
// 类比如数组，数组声明保存student类型数据就只能保存student数据，向上转型就会让其余类型数据混进来
Person person = new Student();             // 可以
List<Person> people = new ArrayList<Student>(); // 不可以
```



#### 泛型边界和通配符

PECS

```java
Producer Extends：只读取数据，用 ? extends T // 未知类型，但它一定是T的子类
Consumer Super：只写入数据，用 ? super T // 未知类型，但它一定是T本身或T的父类
```





### 常用API



 #### BigDecimal

怎么表示浮点数的精度问题？

- double和float都是用近似值表示，故涉及到金额的不要用`double/float`
- 改用`java.math.BigDecimal`



怎么使用`BigDecimal`?

- 需要传递字符串，如`BigDecimal a = new BigDecimal("0.1");`
- 运算需要使用方法
  - `BigDecimal add = a.add(b); `
  - `BigDecimal sub = a.subtract(b); // 减`
  - `BigDecimal mul = a.multiply(b); // 乘`
  - `BigDecimal div = a.divide(b);   // 除`

```java
public class JavaApiDemo {
  public static void main(String[] args) {
    BigDecimal price = new BigDecimal("199.90");
    BigDecimal discount = new BigDecimal("0.85");

    BigDecimal finalPrice = price.multiply(discount).setScale(2, RoundingMode.HALF_UP);
    System.out.println(finalPrice);

    BigDecimal first = new BigDecimal("0.1");
    BigDecimal second = new BigDecimal("0.2");
    BigDecimal result = first.add(second);
    System.out.println(result);

  }
}
```



#### 时间

```java
public class JavaApiDemo {
  public static void main(String[] args) {
    // 返回当前日期：年月日
    LocalDate today = LocalDate.now();
    // 返回当前日期：java中年月日和时分秒用T分割
    LocalDateTime now = LocalDateTime.now();
    LocalDate graduationDate = today.plusYears(4);

    DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");

    String formattedTime = now.format(formatter);

    System.out.println(today);
    System.out.println(graduationDate);
    System.out.println(formattedTime);
  }
}
```



### Optional

`Optional` 是 Java 8 引入的“可能有值、也可能没值”的容器。

它主要用来表达：

```java
// 这个方法不一定能找到结果

// 例如这个方法：找不到结果，返回null
public Student findById(int id) {
    return students.get(id);
}

// 调用方如果没有判断
Student student = studentService.findById(9999);

System.out.println(student.getName()); // NullPointerException

// -- 使用Optional后 --
// 找到学生 → Optional 中装着 Student
// 没找到学生 → Optional.empty()
public Optional<Student> findById(int id) {
    return Optional.ofNullable(students.get(id));
}
```



创建Optional

```java
// of要求student绝对不能是null
Optional<Student> optional = Optional.of(student);

// ofNullable可以接受null
Optional<Student> optional = Optional.ofNullable(student);
```



使用方法：

```java
// 有值就执行：（返回值是void）
studentService.findById(1001)
    .ifPresent(student -> System.out.println(student.getName()));

// 只想得到姓名：（返回值是boolean）
// 有 Student → 调用 getName()
// 没 Student → 返回“未知”
String name = studentService.findById(1001)
    .map(Student::getName)
    .orElse("未知");

// 没找到给默认值
Student student = studentService.findById(1001)
    .orElse(new Student(0, "未知", 0));

// 没找到抛异常
Student student = studentService.findById(1001)
    .orElseThrow(() ->
        new IllegalStateException("未找到学生")
    );

// 找到处理和没找到处理
 private static void studentsStatistics() {
    STUDENT_SERVICE.findOldestStudent()
        .ifPresentOrElse(
            student -> System.out.println("年龄最大的学生：" + student),
            () -> System.out.println("暂无学生数据。"));

 }

```





## 类型转换

- 类型范围小的变量，可以直接赋值给类型范围大的变量。
- 最终类型由表达式中的最高类型决定的
- **byte / short / char在表达式中的运输是当int类型计算的**

```java
byte i = 10;
byte j = 20;
int k = i + j
```

### 强类型转换

数据类型 变量2 = （数据类型）变量1

```java
int a = 20;
byte b = (byte)a;
```

快捷键：alt + enter

浮点数转换成整数，会丢弃小数部分。



## 注解

**写给程序、编译器或框架看的说明信息**，它本身通常不直接参与业务计算。

```java
// 废弃api

public List<Student> findAllSorted(SortStrategy<Student> strategy) {
    List<Student> students = findAll();
    return strategy.sort(students);
}

@Deprecated(since = "2.0", forRemoval = false)
// 这个api在2.0版本将会废弃，目前暂未删除
public List<Student> findAllOrder(SortStrategy<Student> strategy) {
    return findAllSorted(strategy);
}
```



元数据：**写给程序、编译器或框架看的说明信息**，它本身通常不直接参与业务计算。

```java
// 这个字段展示时叫“姓名” 它的展示顺序是第 2 个
@DisplayField(label = "姓名", order = 2)
private final String name;
```



元注解：标注注解本身的注解，叫做元注解

```java
// 标注注解的生命周期：
// SOURCE：只在源码中，编译后丢弃。
// CLASS：保留在 .class 文件中，但运行时通常不能通过反射读取。（默认值）
// RUNTIME：运行时仍存在，可通过反射读取。
@Retention(RetentionPolicy.RUNTIME)

// 限制注解的使用位置：只能标注字段
@Target(ElementType.FIELD)

// 表示生成 Javadoc 文档时，也把 @DisplayField 显示在文档中
@Documented

// 定义注解类型
public @interface DisplayField {
  String label();

  int order();
}

// 使用
@DisplayField(label = "年龄", order = 3)
private int age;
```





## 反射

为什么会有反射？

有些时候，程序在写代码的时候并不知道将来要操作哪些类。

- 比如Spring在程序运行扫描你的项目，发现你的类标了`Servie`，然后帮你创建对象。
- 比如配置文件中写的是字符串`serviceClass=com.example.UserService`



**class对象（包含类的所有信息）**：

```java
Class<?> c1 = User.class;

User user = new User();
Class<?> c2 = user.getClass();

Class<?> c3 = Class.forName("com.example.User");
```



**通过反射创建对象**：

```java
Class<?> clazz = Class.forName("com.example.User"); // 通过反射获取类的class对象
Object user = clazz.getDeclaredConstructor().newInstance(); // 通过该对象的构造函数创建对象
```



**通过反射获取字段和方法：**

```java
// 反射获取字段
// 获取当前类的所有字段：getDeclaredFields，获取当前类和父类的public方法：getFields
List<Field> fields = target.getClass().getDeclaredFields()
    
// 反射获取方法
// 获取当前类的所有方法：getDeclaredMethods，获取当前类和父类的public方法：getMethods
Method method = target.getClass().getMethod(methodName);
```





## 多线程

主要用来解决并发问题。

- Run方法：任务载体，真正要跑的逻辑都写在这里
- Start方法：正确开启新线程

```java
// 传统方式
public class Main {
    public static void main(String[] args) {
        Thread t = new Thread(new MyRunnable());
        t.start(); // 启动新线程
    }
}

class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("start new thread!");
    }
}
```

```java
// Lambda方式
public class Main {
    public static void main(String[] args) {
        Thread t = new Thread(() -> {
            System.out.println("start new thread!");
        });
        t.start(); // 启动新线程
    }
}
```



**等待**

```java
// thread.join() 等待线程完成
try {
    for (Thread thread : threads) {
        thread.join();
    }
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();
    throw new IllegalStateException("等待线程完成时被中断", e);
}
```



#### 加锁

##### synchronized

- `synchronized` 解决“多个线程同时修改共享数据”；
- 线程池解决“怎么更高效地管理很多线程”。

```java
// 通过 synchronized 给 Counter 加锁：一个线程进入 increment() 后，其他线程必须等待它执行完，才能继续累加。

public class Counter {
  private int value;

  public synchronized void increment() {
    value++;
  }

  public synchronized int getValue() {
    return value;
  }
}
```



##### atomic/volatile

```java
// AtomicInteger:保证读-改-写是一个原子操作，适合并发计数。
// volatile：保证变量的可见性，适合“停止标记”等简单状态。可以看作数据在多个线程中共享
public class AtomicCounter {
  private final AtomicInteger value = new AtomicInteger();
  private volatile boolean running = true;

  public void increment() {
    value.incrementAndGet();
  }

  public int getValue() {
    return value.get();
  }

  public boolean isRunning() {
    return running;
  }

  public void stop() {
    running = false;
  }
}
```



**区别**

`AtomicInteger` 与 `synchronized` 都能保证计数正确，但适用范围不同：

- `AtomicInteger`：适合单个数值的原子操作，例如计数、库存数加减；通常开销更小，不需要线程长时间阻塞。
- `synchronized`：适合需要把“一组操作”作为整体保护的情况，例如同时修改余额和交易记录。它能保护任意代码块，不限于一个整数。



**原理**

1. CAS 是 Compare-And-Set（比较并设置）。它的逻辑可以理解为：

```
如果当前值仍然等于“我刚才看到的旧值”
    就把它更新成新值
否则
    说明有别的线程先修改过了，我重新读取后再试
```

例如两个线程都看到 `value = 10`，都想加一：

- 线程 A CAS：确认当前仍是 10，成功写入 11；
- 线程 B CAS：发现当前已经不是 10，失败；它重新读到 11，再尝试写入 12。

`AtomicInteger.incrementAndGet()` 底层就是类似这种“失败则重试”的 CAS 机制，因此无需像 `synchronized` 一样持有一把互斥锁。



#### 线程池

```java
// 创建线程池
ExecutorService executor = Executors.newFixedThreadPool(3);

// 提交任务
for (int i = 0; i <= 5; i++) {
    int taskNumber = i;

    executor.submit(() -> {
        System.out.println(
            "任务 " + taskNumber + " 正在线程 "
            + Thread.currentThread().getName() + " 执行");

        try {
            Thread.sleep(200);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            System.out.println("任务" + taskNumber + "被中断");
            return;
        }

        System.out.println("任务" + taskNumber + "执行完成");
    });
}

// 关闭提交：shutdown是关闭不接受线程提交
executor.shutdown();

// 关闭正在进行的线程：shutdownNow
try {
    boolean finished = executor.awaitTermination(5, TimeUnit.SECONDS);

    if (!finished) {
        executor.shutdownNow();
    }
} catch (InterruptedException e) {
    executor.shutdownNow();
    Thread.currentThread().interrupt();
}
```



**异常**

- 通过Callable创建run，可以获取返回值，不会阻塞主线程
- 通过Future触发线程执行，可以获取返回值，会阻塞主线程

```java
// 成功构建
Callable<String> successTask = () -> {
    Thread.sleep(200);

    return "任务完成，执行线程：" + Thread.currentThread().getName();
};

Future<String> successFuture = executor.submit(successTask);

String result = successFuture.get();
System.out.println(result);

// 失败构建
Callable<String> failedTask = () -> {
    throw new IllegalStateException("模拟任务失败");
};

Future<String> failedFuture = executor.submit(failedTask);
System.out.println(failedFuture);

try {
    failedFuture.get();
} catch (ExecutionException e) {
    System.out.println("任务失败原因：" + e.getCause().getMessage());
}
```



**内存泄露**

`ThreadLocal` 让每个线程保存一份独立变量，常用于请求 ID、当前用户、日志追踪上下文等。

```java
// 创建一个独立变量
public class RequestContext {

  private static final ThreadLocal<String> REQUEST_ID = new ThreadLocal<>();

  public static void setRequestId(String requestId) {
    REQUEST_ID.set(requestId);
  }

  public static String getRequestId() {
    return REQUEST_ID.get();
  }

  public static void clear() {
    REQUEST_ID.remove();
  }
}
```



**线程池会复用工作线程。**如果任务执行完不调用 `remove()`，同一工作线程处理下一个请求时，可能读到上一个请求遗留的上下文；这会造成数据串线，也可能导致内存泄漏。

```java
// 使用完成后需要清理线程数据
public class ThreadLocalDemo {
  public static void main(String[] args) {
    ExecutorService executor = Executors.newFixedThreadPool(2);

    try {
      for (int i = 1; i <= 4; i++) {
        String requestId = "request-" + i;

        executor.submit(() -> {
          RequestContext.setRequestId(requestId);
          try {
            System.out
                .println("任务-" + RequestContext.getRequestId() + "正在线程" + Thread.currentThread().getName() + "中执行");
          } finally {
            RequestContext.clear();
          }

        });
      }
    } finally {
      shutdownExecutor(executor);
    }
  }

  private static void shutdownExecutor(ExecutorService executor) {
    executor.shutdown();

    try {
      boolean finished = executor.awaitTermination(5, TimeUnit.SECONDS);

      if (!finished) {
        executor.shutdownNow();
      }
    } catch (InterruptedException e) {
      executor.shutdownNow();
      Thread.currentThread().interrupt();
    }
  }
}
```



#### 事务

事务把多个数据库操作看作一个整体：要么全部成功提交，要么任一步失败后整体回滚。

创建订单中，扣库存、插入订单、插入订单明细必须属于同一事务。



#### ACID

- **原子性（Atomicity）**：事务内操作不可分割，要么全部成功，要么全部回滚。
- **一致性（Consistency）**：事务执行前后，数据仍满足业务约束，例如库存不能为负、订单金额正确。
- **隔离性（Isolation）**：并发事务之间不会以不合适的方式互相干扰。
- **持久性（Durability）**：事务提交成功后，数据会被持久保存；即使数据库进程随后重启，已提交的数据也应存在。

区分原子性与一致性：原子性关注“这几步是否一起成功/失败”；一致性关注“结果是否仍符合规则”。



## 函数式编程

直接传递类的方法。

- 保证类只有一个方法：对应参数类型必须是【**函数式接口**】（**有且仅有一个抽象方法，排除 Object 原生方法**）

- 保证类唯一的重载方法：通过**函数签名**，可以确定该函数对应的方法



### Lambda

Lambda类似于Js中的箭头函数，Java中的方法都是构建在对象上，所以当需要传递一个方法时，往往需要创建一个对象，为了简化该方式，Java8引入了Lambda。

```java
// 传统方法传递
String[] array = ...
Arrays.sort(array, new Comparator<String>() {
    public int compare(String s1, String s2) {
        return s1.compareTo(s2);
    }
});
```

```java
// Lambda
import java.util.Arrays;

public class Main {
    public static void main(String[] args) {
        String[] array = new String[] { "Apple", "Orange", "Banana", "Lemon" };
        Arrays.sort(array, (s1, s2) -> {
            return s1.compareTo(s2);
        });
        System.out.println(String.join(", ", array));
    }
}
```



### 常用的函数式接口

```java
// Predicate<T>：判断，返回 boolean
// Function<T, R>：转换，T 转 R
// Consumer<T>：消费数据，无返回值
// Supplier<T>：提供数据，无入参

public static void main(String[] args) {
    Student student = new Student(1001, "张三", 20);

    Predicate<Student> isAdult = value -> value.getAge() > 18;

    Function<Student, String> toSummary = Student::getSummary;

    Consumer<Student> printStudent = System.out::println;

    Supplier<Student> defaultStudent = () -> new Student(9999, "默认学生", 18);

    boolean adult = isAdult.test(student);
    System.out.println("是否成年：" + adult);

    String summary = toSummary.apply(student);
    System.out.println("学生摘要：" + summary);

    printStudent.accept(student);

    Student createdStudent = defaultStudent.get();
    System.out.println("默认学生：" + createdStudent);

}
```





### 方法引用

**静态方法引用**

```java
Function<String, Integer> f = Integer::parseInt;
//等价：s -> Integer.parseInt(s)
```



实例方法引用（对象：：方法）

```java
Consumer<String> consumer = System.out::println;
```



类名：：实例方法

```java
Function<String, Integer> func = String::length;
//等价 s -> s.length()
```



构造器引用 / 数组构造引用

```java
Supplier<User> sup = User::new;
IntFunction<Integer[]> gen = Integer[]::new; //你刚才代码里的写法
```





### Steam

Java Stream（流）是 **Java8 引入的一套「集合数据处理 API」**，采用**函数式编程风格**，用来替代**传统 for 循环遍历集合**；



**创建流:**

数据源：Collection、数组、文件、IO 通道等

```java
List<String> list = Arrays.asList("a","b","c");
Stream<String> stream = list.stream(); // 集合获取串行流
```



**中间操作（Intermediate）:**

- `.filter()`、`.map() `、`.sorted()`、`.distinct()`、`.skip()`、`.limit()`

- 惰性求值--不会立即执行，只是组装流水线
- 返回Stream，可以链式继续调用



**终端操作（Terninal）**

- `.collect()` `.forEach()` `.count()` `.anyMatch()` `.max()`
- 一旦调用终端操作，流水线启动，流立刻被消费，关闭！

```java
// 一个流只能执行一次终端操作，重负使用会抛异常
Stream<String> s = list.stream();
s.forEach(System.out::println);
s.filter(x->true); // ❌ IllegalStateException，流已经关闭
```



具体操作：

```java
// collect：grouping将条件作为key进行分组，partitioningBy将条件作为判断分为两组
// 分组 groupingBy
public Map<Integer, List<Student>> groupStudentsByAge() {
    return students.values()
        .stream()
        .collect(Collectors.groupingBy(Student::getAge));
}

private static void groupStudentsByAge() {
    Map<Integer, List<Student>> students = STUDENT_SERVICE.groupStudentsByAge();
    students.forEach((age, studentList) -> {
        System.out.println(age + " 岁：");

        studentList.forEach(System.out::println);
    });
}

// 分区 partitionStudentsByAdult
 public Map<Boolean, List<Student>> partitionStudentsByAdult() {
     return students.values()
         .stream()
         .collect(Collectors.partitioningBy(student -> student.getAge() >= 18));
 }

private static void partitionStudentsByAdult() {
    Map<Boolean, List<Student>> result = STUDENT_SERVICE.partitionStudentsByAdult();

    result.forEach((isAdult, studentList) -> {
        String label = isAdult ? "成年学生" : "未成年学生";
        System.out.println(label + "：" + studentList);
    });
}
```



**注意**

```java
// 返回不可修改的列表：这里修改值得是数组，而不是里面的值
// List.copyOf(...) 返回的也是不可修改的列表
return tasks.values().stream()
    .filter(task -> task.getStatus() == status)
    .toList();
```

```java
// 返回可修改的列表
return tasks.values().stream()
    .filter(task -> task.getStatus() == status)
    .collect(Collectors.toCollection(ArrayList::new));

return new ArrayList<>(
    tasks.values().stream()
        .filter(task -> task.getStatus() == status)
        .toList()
);
```

```java
// map可以自动转发为Optional - 存在即转换，不存在则为空
```





## Spring开发

### IoC+Bean

inversion of controlltrol 换言之也就是依赖注入（dependence inject），通过参数解决频繁创建对象问题。



支持**属性注入**和**构造方法**注入：

```Java
// 属性注入：setDataSource
public class BookService {
    private DataSource dataSource;

    public void setDataSource(DataSource dataSource) {
        this.dataSource = dataSource;
    }
}

// 构造方法注入：BookService
public class BookService {
    private DataSource dataSource;

    public BookService(DataSource dataSource) {
        this.dataSource = dataSource;
    }
}
```



同时，需要告诉Spring怎么进行注入，也就是创建组件，这一步也就是**装配Bean**，通过XML文件实现。

```java
<beans>
    <bean id="dataSource" class="HikariDataSource" />
    <bean id="bookService" class="BookService">
        <property name="dataSource" ref="dataSource" />
    </bean>
    <bean id="userService" class="UserService">
        <property name="dataSource" ref="dataSource" />
    </bean>
</beans>
```



配置好了，还需要做加载，也就是**创建IoC容器**实例，并加载配置文件。

```java
public class Main {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("application.xml");
        UserService userService = context.getBean(UserService.class);
        User user = userService.login("bob@example.com", "password");
        System.out.println(user.getName());
    }
}
```



### annotation

通过注解去**简化装载Bean**操作。



添加**Component**，也就是定义一个Bean

```java
@Component
public class MailService {
    ...
}
```



通过**Autowired**把指定类型的Bean注入到指定字段（包含set函数和构造方法）

```java
// 指定字段
@Component
public class UserService {
    @Autowired
    MailService mailService;

    ...
}

// 构造方法参数
@Component
public class UserService {
    MailService mailService;

    public UserService(@Autowired MailService mailService) {
        this.mailService = mailService;
    }
    ...
}
```



最后通过**Configuration**创建一个配置类，也就是入口。

**ComponentScan**自动搜索当前类所在的包以及子包，并把所有标注为`Component`的Bean自动创建出来，并根据AutoWired进行装配。

```java
@Configuration
@ComponentScan
public class AppConfig {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
        UserService userService = context.getBean(UserService.class);
        User user = userService.login("bob@example.com", "password");
        System.out.println(user.getName());
    }
}
```



### 定制Bean

#### 单例和原型

component指定的bean，默认是单例，也就是`getBean(Class)`获取的Bean都是同一个实例。



通过Scope可以定义原型（Prototype），即每次调用`getBean(Class)`，容器都返回一个新的实例。

```java
@Component
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE) // @Scope("prototype")
public class MailSession {
    ...
}
```



#### 注入List

接口 - list - bean，通过list的方式简化操作（具体可参考定制Bean相关文章），这里主要涉及到排序的问题，即可以通过Order注解，可以指定List中Bean的顺序。

```java
@Component
@Order(1)
public class EmailValidator implements Validator {
    ...
}

@Component
@Order(2)
public class PasswordValidator implements Validator {
    ...
}

@Component
@Order(3)
public class NameValidator implements Validator {
    ...
}
```



#### 可选注入

`@autowired`如果没有找到对应类型Bean，会抛出`NoSuchBeanDefintionException`异常。

可以给`@autowired`添加一个`required = false`的参数：

````java
@Component
public class MailService {
    @Autowired(required = false)
    ZoneId zoneId = ZoneId.systemDefault();
    ...
}
````



#### 第三方Bean

如果一个Bean不在package管理之内，如何创建它？

在`@Configuration`类中编写一个Java方法创建并返回它，注意给方法标记一个`@Bean`注解：

```java
@Configuration
@ComponentScan
public class AppConfig {
    // 创建一个Bean:
    @Bean
    ZoneId createZoneId() {
        return ZoneId.of("Z");
    }
}
```



#### 初始化和销毁

也就是Bean的钩子函数

```java
@Component
public class MailService {
    @Autowired(required = false)
    ZoneId zoneId = ZoneId.systemDefault();

    @PostConstruct
    public void init() {
        System.out.println("Init mail service with zoneId = " + this.zoneId);
    }

    @PreDestroy
    public void shutdown() {
        System.out.println("Shutdown mail service");
    }
}
```



#### 别名

如果要在一个容器，对一种类型Bean创建多个实例，比如连接多个数据库，那么必须创建多个DataSource实例。这里需要使用别名。

- 直接指定
- 通过qualifier指定

```java
@Configuration
@ComponentScan
public class AppConfig {
    // 直接指定
    @Bean("z")
    ZoneId createZoneOfZ() {
        return ZoneId.of("Z");
    }

    // 通过qualifier指定
    @Bean
    @Qualifier("utc8")
    ZoneId createZoneOfUTC8() {
        return ZoneId.of("UTC+08:00");
    }
}
```



别名注入的Bean在通过`Autowired`注入的时候，需要指定名称（存在多个Bean），否则会报错。

```java
@Component
public class MailService {
	@Autowired(required = false)
	@Qualifier("z") // 指定注入名称为"z"的ZoneId
	ZoneId zoneId = ZoneId.systemDefault();
    ...
}
```



不指定名称报错，可以通过`primary`避免，也就是指定一个默认Bean的意思。

```java
@Configuration
@ComponentScan
public class AppConfig {
    @Bean
    @Primary // 指定为主要Bean
    @Qualifier("z")
    ZoneId createZoneOfZ() {
        return ZoneId.of("Z");
    }

    @Bean
    @Qualifier("utc8")
    ZoneId createZoneOfUTC8() {
        return ZoneId.of("UTC+08:00");
    }
}
```



### 使用Resource

通过`value`，可以快速读取注入文件。

```java
@Component
public class AppService {
    @Value("classpath:/logo.txt")
    private Resource resource;

    private String logo;

    @PostConstruct
    public void init() throws IOException {
        try (var reader = new BufferedReader(
                new InputStreamReader(resource.getInputStream(), StandardCharsets.UTF_8))) {
            this.logo = reader.lines().collect(Collectors.joining("\n"));
        }
    }
}

```



### 注入配置

Spring Boot  -  自动加载  -  application.properties/application.yml配置文件

如果是其他文件，则需要通过`PropertySource`来自动**读取配置文件**，然后通过`Value`注入

```java
@Configuration
@ComponentScan
@PropertySource("app.properties") // 表示读取classpath的app.properties
public class AppConfig {
    // 如果app.zone不存在，则使用Z
    @Value("${app.zone:Z}")
    String zoneId;

    @Bean
    ZoneId createZoneId() {
        return ZoneId.of(zoneId);
    }
}
```



其他文件，可以通过`#{}`的方式，从**bean读取**

```java
// 从配置文件中注入
@Component
public class SmtpConfig {
    @Value("${smtp.host}")
    private String host;

    @Value("${smtp.port:25}")
    private int port;

    public String getHost() {
        return host;
    }

    public int getPort() {
        return port;
    }
}
```

```java
// 从bean中注入
@Component
public class MailService {
    @Value("#{smtpConfig.host}")
    private String smtpHost;

    @Value("#{smtpConfig.port}")
    private int smtpPort;
}
```



### AOP

AOP是Aspect Oriented Programming，即面向切面编程。

本质上实现的目的，就是把公用代码镶嵌到业务代码中去，减少重复性工作。

```java
// 声明一个切面
@Aspect
@Component
public class LoggingAspect {
    // 在执行UserService的每个方法前执行:
    @Before("execution(public * com.itranswarp.learnjava.service.UserService.*(..))")
    public void doAccessCheck() {
        System.err.println("[Before] do access check...");
    }

    // 在执行MailService的每个方法前后执行:
    @Around("execution(public * com.itranswarp.learnjava.service.MailService.*(..))")
    public Object doLogging(ProceedingJoinPoint pjp) throws Throwable {
        System.err.println("[Around] start " + pjp.getSignature());
        Object retVal = pjp.proceed();
        System.err.println("[Around] done " + pjp.getSignature());
        return retVal;
    }
}
```

```java
@Configuration
@ComponentScan
// 添加切面注解
@EnableAspectJAutoProxy
public class AppConfig {
    ...
}
```



完整的注解使用：



**定义顶层注解**：

- 方便引用，以及使用元注解规定使用规则

```java
@Target(METHOD)
@Retention(RUNTIME)
public @interface MetricTime {
    String value();
}
```



**标注注解（打标记）**

```java
@Component
public class UserService {
    // 监控register()方法性能:
    @MetricTime("register")
    public User register(String email, String password, String name) {
        ...
    }
    ...
}
```



**定义Aspect**

```java
@Aspect
@Component
public class MetricAspect {
    @Around("@annotation(metricTime)")
    public Object metric(ProceedingJoinPoint joinPoint, MetricTime metricTime) throws Throwable {
        String name = metricTime.value();
        long start = System.currentTimeMillis();
        try {
            return joinPoint.proceed();
        } finally {
            long t = System.currentTimeMillis() - start;
            // 写入日志或发送至JMX:
            System.err.println("[Metrics] " + name + ": " + t + "ms");
        }
    }
}

```



## Spring Boot



### 启动顺序

这里以秒杀活动中的预热为例：

```
main 方法
  ↓
Spring 创建所有 Bean、连接 MySQL/Redis/RabbitMQ
  ↓
应用上下文准备完成
  ↓
自动调用所有 ApplicationRunner.run(...)
  ↓
执行 warmUpStocks()  -- 开发者声明的方法
  ↓
从 MySQL 查询活动并预热 Redis 库存
```



### 注释

基本流程**

```java
// 入口
@SpringBootApplication

// Bean
@Component   通用组件
@Repository  数据访问层
@Service     业务逻辑层
```



#### Controller相关

```java
// 处理 HTTP 请求的 Controller，返回值会自动转成 JSON
@RestController

// 定义统一路径前缀
@RequestMapping("/api")

// 处理 GET /api/health
@GetMapping("/health")

// 声明请求体 -- 将请求json 转换成 java类
@RequestBody

// 获取请求路径
@PathVariable
```



### 基本对象（DTO）

| 类型                | 用途                                                         |
| ------------------- | ------------------------------------------------------------ |
| Entity / 实体类     | 表示系统核心业务对象，常对应数据库表                         |
| DTO                 | 接收或返回接口数据，例如 `CreateUserRequest`、`UserResponse` |
| Mapper 查询结果对象 | 承载某条 SQL 实际查出的字段，例如 `DatabaseUser`             |
| `record`            | Java 语法形式，常用于 DTO 或查询结果对象，不等于实体类       |



**实体类**

- 字段是 `private final`，创建后不能重新赋值；
- 自动生成构造器、访问方法、`equals()`、`hashCode()`、`toString()`；
- 不能继承普通类，因为 record 本身已经隐式继承 `java.lang.Record`；
- 可以实现接口，也可以在 `{}` 中添加校验、普通方法或静态方法。

```java
public record User(Long id, String username) {
}
```

```java
// 自动生成
public final class User {
    private final Long id;
    private final String username;

    public User(Long id, String username) {
        this.id = id;
        this.username = username;
    }

    public Long id() {
        return id;
    }

    public String username() {
        return username;
    }

    // 还自动生成 equals、hashCode、toString
}
```



DTO 是 **Data Transfer Object，数据传输对象**

- 它专门定义“接口允许接收什么、接口应该返回什么”。
- 相当于接口的“白名单”
- 以免前端修改password等重要信息

```java
// 前端只能提交允许创建时填写的字段，也只能收到允许展示的字段。实体如何存储、以后增加什么内部字段，都不会直接泄露或破坏接口。
public record CreateUserRequest(String username) {
}

public record UserResponse(Long id, String username) {
}
```



如果涉及到数据库数据回填，则只能使用普通类

```java
// 比如订单需要回填id -- record类不能更改
// 使用options注释 使用主键回填 数据库字段名id java对象字段名id
 @Options(useGeneratedKeys = true, keyProperty = "id", keyColumn = "id")
```





### 请求处理

`ResponseEntity`

- 是 Spring 用来表示“一整个 HTTP 响应”的对象
- 它不仅能返回数据，还能控制状态码和响应头

```java
 @PostMapping
public ResponseEntity<User> createUser(@RequestBody User user) {
    User createdUser = userService.createUser(user.username());
    return ResponseEntity.status(HttpStatus.CREATED).body(createdUser);
}
```

```java
// 查询成功：200 + 用户数据
return ResponseEntity.ok(userResponse);

// 新增成功：201 + 用户数据
return ResponseEntity.status(HttpStatus.CREATED).body(userResponse);

// 删除成功：204，无响应体
return ResponseEntity.noContent().build();

// 查不到用户：404
return ResponseEntity.notFound().build();
```



`@RequestBody`

- 让Spring把HTTP请求体的数据，转换成Java对象

```java
POST /api/users
Content-Type: application/json

{
  "username": "alice"
}
```

```java
// Controller
public ResponseEntity<User> createUser(@RequestBody User user)
    
// Spring 会自动把 JSON 转成
// 这里通常由 Jackson 完成 JSON ↔ Java 对象的转换。
// 然后就可以使用 user.username()
User user = new User(null, "alice"); // 这里的null，后续不使用
```



请求体解析

```java
@GetMapping("/{id}")
public ResponseEntity<User> findUserById(@PathVariable long id) {
    return userService.findUserById(id)
        .map(ResponseEntity::ok)
        .orElseGet(() -> ResponseEntity.notFound().build());
}

// 等价于
Optional<User> optionalUser = userService.findUserById(id);

if (optionalUser.isPresent()) {
    return ResponseEntity.ok(optionalUser.get());
}

return ResponseEntity.notFound().build();
```

流程如下：

```java
查到 User
   ↓
map(ResponseEntity::ok)
   ↓
ResponseEntity.ok(user) → 200 + 用户 JSON

查不到 User
   ↓
map 不执行
   ↓
orElseGet(...)
   ↓
ResponseEntity.notFound().build() → 404 + 空响应体
```



### 校验

一般出现在Controller层，对前端数据做校验。涉及Controller层和DTO数据。

```java
// 声明需要校验数据
 @PostMapping
  public ResponseEntity<UserResponse> createUser(@Valid @RequestBody CreateUserRequest user) {
    User createUser = userService.createUser(user.username());
    return ResponseEntity.status(HttpStatus.CREATED).body(toResponse(createUser));
  }
```

```java
// DTO中添加数据校验
public record CreateUserRequest(
    @NotBlank(message = "用户名不能为空") @Size(min = 2, max = 32, message = "用户名长度必须在 2 到 32 个字符之间") String username) {

}
```

`@NotNull`：值不能是 `null`；

`@NotBlank`：仅用于字符串，不能是 `null`、空字符串或全空白字符串；

`@Size`：校验字符串、集合等长度范围。单独使用时，`null` 通常会通过，所以常和 `@NotNull` 配合。



### 全局校验

整体流程：

```
Controller 接收请求
→ DTO 校验或 Service 业务处理
→ 出现异常
→ GlobalExceptionHandler 自动接住异常
→ 返回统一 JSON 错误响应
```

你不需要手动调用 `GlobalExceptionHandler` 里的方法，Spring 会自动调用。

```
@RestControllerAdvice
public class GlobalExceptionHandler {
```

`@RestControllerAdvice` 表示“全局 Controller 异常处理器”。

- `RestController`：返回 JSON。
- `Advice`：对所有 Controller 生效。
- `GlobalExceptionHandler`：类名，意思是全局异常处理器。

 核心分工：

 DTO 格式、字段校验失败

 → MethodArgumentNotValidException

 → 返回 fieldErrors



 用户不存在、商品不存在、库存不足等业务失败

 → IllegalArgumentException

 → 返回 message，fieldErrors 为空



## 注册登录

### 密码加密

注意：`passwordHash` 也属于敏感凭据，不应暴露给前端、日志或第三方

```java
// 使用PasswordEncode包
// 调用 passwordEncoder.encode(request.password()) 给密码加载
// 后续通过 matches 方法验证 passwordEncoder.matches(明文密码, 数据库哈希)
```



### token

Token 是“令牌”的统称；JWT 是其中一种具体格式。

```
Token（泛称）
├── JWT Token
├── Session ID
├── OAuth Access Token
└── 随机字符串 Token
```

例如：

```
Token：a8f92b1c...              ← 可能只是随机字符串
JWT：eyJhbGciOiJIUzI1NiJ9...   ← 有固定 Header.Payload.Signature 结构
```

区别：

| 项目             | 普通 Token / Session ID       | JWT                            |
| ---------------- | ----------------------------- | ------------------------------ |
| 内容             | 通常只是随机 ID               | 可携带 userId、过期时间等声明  |
| 服务端是否存状态 | 通常需查 Session/Redis/数据库 | 通常可通过签名直接验证         |
| 是否可读         | 只是随机串，不能解出信息      | Payload 可被解码查看           |
| 作废能力         | 删除服务端记录即可            | 较困难，常依赖过期时间或黑名单 |

所以“登录后给前端一个 token”是业务概念；这个 token 可以是 JWT，也可以是随机 Session ID。

JWT 不是比 Token 更高级的平级概念，而是 **Token 的一种实现方式**。



### Session

Session ID 是服务端创建的一段随机标识，用来找到服务端保存的登录状态。

```
登录成功
→ 服务端创建 Session：
   sessionId = "abc123"
   userId = 1001
→ 服务端保存这份对应关系（内存、Redis、数据库）
→ 浏览器只保存 sessionId
→ 后续请求带回 sessionId
→ 服务端据此找到 userId = 1001
```

浏览器通常通过 Cookie 自动携带它：

```
Cookie: JSESSIONID=abc123
```

关键点：

- **Session**：服务端保存的登录状态，例如“用户 1001 已登录”。
- **Session ID**：客户端保存的“钥匙/索引”，用来找到 Session。
- 客户端一般拿不到具体用户数据，只拿到随机字符串。

和 JWT 对比：

```
Session ID：服务端存用户状态，客户端只存一个随机 ID
JWT：客户端携带用户声明和签名，服务端通常不存会话状态
```

Session ID 的优点是注销简单：服务端删除对应 Session 即可立刻失效；缺点是在多台服务器部署时，通常需要 Redis 共享 Session。实际项目中 Cookie 应设置 `HttpOnly`、`Secure`、`SameSite` 等安全属性。



### JWT

JWT（JSON Web Token）是一种“登录凭证”格式。用户登录成功后，服务端生成一个 JWT 返回给前端；之后前端每次请求携带它，服务端验证后就能知道“这是哪个用户”。JWT 的标准定义是可在双方之间传递声明信息的紧凑、URL 安全字符串。[RFC 7519](https://datatracker.ietf.org/doc/html/rfc7519)

典型流程：

```
用户登录 → 服务端校验账号密码 → 签发 JWT
前端保存 JWT → 后续请求携带 JWT
服务端验证 JWT → 确认当前用户身份
```

通常放到请求头：

```
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9...
```

JWT 常见长这样，有三段，以 `.` 分隔：

```
Header.Payload.Signature
```

- `Header`：说明令牌类型、签名算法，例如 `HS256`。
- `Payload`：存放声明信息，例如用户 id、过期时间。
- `Signature`：服务端用密钥生成，用来防篡改。

Payload 解码后可能是：

```
{
  "sub": "1001",
  "username": "alice",
  "iat": 1788420000,
  "exp": 1788427200
}
```

其中：

- `sub`：主体，通常是用户 ID；
- `iat`：签发时间；
- `exp`：过期时间。

最重要的一点：**JWT 通常是签名，不是加密。** Payload 只是 Base64URL 编码，别人能解码看到内容；但只要没有服务端密钥，就无法篡改后仍通过签名验证。因此 JWT 里不能放密码、身份证号等敏感信息。JWT 的时间字段使用从 1970 年起计算的秒数。[RFC 7519 的 NumericDate 定义](https://datatracker.ietf.org/doc/html/rfc7519#section-2)

它的优点是服务端无需每次查询 Session 表，适合多服务、多实例场景；缺点是签发后难以立即作废，所以必须设置较短过期时间，复杂系统还会配合刷新令牌或黑名单机制。

你当前项目中，后面登录成功时可以把 `userId` 和 `exp` 写进 JWT；注册阶段只负责保存 BCrypt 密码哈希。





## OpenAPI/Swagger

OpenAPI 是一套“描述接口应该长什么样”的标准；Swagger 是围绕这套标准发展出来的一组工具。

可以简单理解为：

```
OpenAPI：接口说明书的标准格式
Swagger：读取/生成/展示这份说明书的工具
```

例如一个“创建订单”接口，OpenAPI 会描述：

- 路径：`POST /orders`
- 请求参数：`userId`、`productId`、`quantity`
- 请求体 JSON 长什么样
- 成功时返回什么 JSON
- 失败时可能是 `400`、`404`、`409`
- 每种状态码的响应结构

这些信息一般会被生成成一个 `openapi.json` 或 `openapi.yaml` 文件。这份文件既能给 Swagger UI 展示，也能被前端、测试工具、代码生成工具读取。



- 在 `pom.xml` 的 `<dependencies>` 添加：

```
<dependency>
  <groupId>org.springdoc</groupId>
  <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
  <version>3.1.0</version>
</dependency>
```



- 新建 `common/config/OpenApiConfig.java`：

```
@Configuration
@OpenAPIDefinition(
    info = @Info(
        title = "用户订单服务 API",
        version = "v1",
        description = "用户与订单管理接口文档"
    )
)
public class OpenApiConfig {
}
```



- 给 `UserController` 添加类级别标签：

```
@Tag(name = "用户管理", description = "用户创建与查询接口")
```

给两个方法添加文档：

- 创建用户：`@Operation(summary = "创建用户")`，响应码 `201`、`400`
- 按 ID 查询：`@Operation(summary = "按 ID 查询用户")`，响应码 `200`、`404`

响应码用 `@ApiResponses` 和 `@ApiResponse` 描述。

```java
  @Operation(summary = "创建用户", requestBody = @io.swagger.v3.oas.annotations.parameters.RequestBody(description = "待创建的用户", required = true, content = @Content(examples = @ExampleObject(value = "{\"username\":\"alice\"}"))))
  @ApiResponses({
      @ApiResponse(responseCode = "201", description = "创建成功"),
      @ApiResponse(responseCode = "400", description = "请求参数不合法")
  })
  @PostMapping
  public ResponseEntity<UserResponse> createUser(
      @Valid @RequestBody CreateUserRequest user) {
    User createUser = userService.createUser(user.username());
    return ResponseEntity.status(HttpStatus.CREATED).body(toResponse(createUser));
  }
```





## 快捷键

`ctrl + alt + r`:

- 选中代码块，会自动出现条件判断语句





1、passwordEncode每次生成的hash值都不相同

2、保证业务的独立性，后面该数据库接入其他接口，其他接口导致数据库用户名重复

3、防止传输过程中暴露

4、以免暴露数据，同时DTO可以保证数据返回字段的独立性

5、代码功能性、代码的边界线、数据库在真实环境下mapper的调用是否正常

6、以免影响开发环境数据库



