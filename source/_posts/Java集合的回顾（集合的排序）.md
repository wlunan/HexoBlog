# :star:Java集合的回顾（集合的排序）

![image-20220320100249565](D:\资源\学习工作\lnnau\typora笔记\笔记图片\image-20220320100249565.png)

## :one:Set

### **Set集合特点**

- 不包含重复元素的集合

- 没有带索引的方法，所以不能使用普通for循环遍历

  

### TreeSet

#### 特点

- **元素有序**：这里的有序不是指存储和取出的顺序，而是按照一定的规则进行排序，具体的排序取决于构造方法

  - TreeSet():根据元素的自然排序进行排序（**数字从小到大**）

  - TrssSet(Comparator comparator):根据指定的比较器进行排序

    

- 包含set集合的特点



#### 元素有序且不重复

```java
public class AboutSet {
    public static void main(String[] args) {
        //创建set集合
        Set set = new TreeSet();
        set.add(1);
        set.add(1);
        set.add(5);
        set.add(3);
        for(Object o:set) {
            System.out.println(o);
        }
    }
}

output:1 3 5
```

#### 比较器排序

##### **:rocket:从小到大排序-分析比较过程**

起始情况下：s1和s2都被赋值为集合添加的第一个值，也就是5	5

第二次添加：**s1(新元素)**,**s2(已经添加的元素)**,s1-s2<0,s1的位置和s2的位置**不进行交换**	2 5

第三次添加：s1和第一次添加的元素比较，s1-s2<0,不交换，再和第二次添加的元素比较，s1-s2==0不添加	2 5

第四次添加：1-5<0 不交换，1-2<0不交换，1 2 5

(规律)第五次添加：3-5<0 不交换，3-2>0交换（由于已经有序，2大于1,3和2交换，2前面的就不用再比较了），1 2 3 5

(事实上)：3-2>0放到2的后面，3-5<0放5的前面

**(按规律)**第六次添加：8-5>0 放5后面，结束	1 2 3  5 8

**(事实上)**：8-2>0放2后面，8-5>0放5后面	结束	1 2 3 5 8

###### :exclamation:注意

- 上面是模拟运行的过程，这是通过debug分析执行过程的出的过程，但是第五次和第六次添加的时候却出现了和前几次不同的情况，这一点我还在困惑中，不过我感觉**可能和在元素在空间中存放的位置**（地址有关）。至于为什么说是前几次形成的规律，是为了便于自己记忆比较的过程。

- 比较过程的描述用**交换**我觉得可能不太准确，因为在比较的过程中，并不能确定s1添加进去和s2比较的相对位置，所以按照从小到大（从左到右）的描述，我觉得用**放前面放后面**相对准确一些，也就是s1-s2>0放到s2的后面；s1-s2<0就放在s2的前面（s1-s2==0也放前面）**望周知**。

```java
public class AboutSet {
    public static void main(String[] args) {
        //创建set集合
        //通过比较器排序Comparator自定义排序方式
        Set<Integer> set = new TreeSet<>(new Comparator<Integer>() {
            @Override
            public int compare(Integer s1, Integer s2) {
                return s1-s2;
            }
        });
        set.add(5);
        set.add(2);
        set.add(2);
        set.add(1);
        set.add(3);
        set.add(8);
        for(Object o:set) {
            System.out.println(o);
        }
    }
}
```

###### 贴上运行完的截图

![image-20220320111034431](D:\资源\学习工作\lnnau\typora笔记\笔记图片\image-20220320111034431.png)



##### :robot:从大到小排序

起始：s2和s1都被赋值为第一个被添加的值5	5

第二次添加：s2(已经添加过的元素)-s1(新元素)>0，2放5的后面	5 2

第三次添加：5-2>0，放5后面，2-2==0，不添加	5 2

第四次添加：5-1>0，放5后面，2-1>0放2后面	5 2 1

第五次添加：2-3<0，放2前面，5-3>0放5后面	5 3 2 1

第六次添加：2-8<0，放2前面，5-8<0放5前面 	8 5 3 2 1

**需要注意的和从小到大类似，不再赘述**

```java
public class AboutSet {
    public static void main(String[] args) {
        //创建set集合
        //通过比较器排序Comparator自定义排序方式
        Set<Integer> set = new TreeSet<>(new Comparator<Integer>() {
            @Override
            public int compare(Integer s1, Integer s2) {
                return s2-s1;
            }
        });
        set.add(5);
        set.add(2);
        set.add(2);
        set.add(1);
        set.add(3);
        set.add(8);
        for(Object o:set) {
            System.out.println(o);
        }
    }
}
```

#### :end:总结

匿名类的方式

s1:新添加元素	s2:已经添加过的元素

实现接口的方式

this. 新添加的元素	s:已经添加的元素

##### 从小到大排序

格式：s1-s2

> s1-s2=num	num>0	s1放在s2的后面
>
> ​						num<0	s1放在s2的前面
>
> ​						num==0	s1不添加

​					

##### 从大到小排序

> s2-s1=num	num>0	s1放在s2的后面
>
> ​						num<0	s1放在s2的前面
>
> ​						num==0	s1不添加

**s1在前降序排列，s1在后升序排列**

### 处理多重条件排序

```java
package com.reback;

import java.util.*;

/**
 * @Author Lunau
 * @Create 2022-03-20 10:00
 * @Description 对set集合及其实现类探究
 * @Result	
杜甫 2013 21
李贺 2014 25
李白 2014 20
王维 2015 22
 */
public class AboutSet {
    public static void main(String[] args) {
        //创建set集合
        //通过比较器排序Comparator自定义排序方式
        Set<Student> set = new TreeSet<Student>(new Comparator<Student>() {
            @Override
            public int compare(Student s1, Student s2) {
                //按照id从小到大，age从大到小排序
                int num1 = s1.id-s2.id;
                int num2 = num1==0?s2.age-s1.age:num1;
                return num2;
            }
        });
        //添加元素
        set.add(new Student(2014,20,"李白"));
        set.add(new Student(2013,21,"杜甫"));
        set.add(new Student(2015,22,"王维"));
        set.add(new Student(2014,25,"李贺"));
        for(Student s:set) {
            System.out.println(s.name+" "+s.id+" "+s.age);
        }
    }
}
class Student{
    int id;
    int age;
    String name;

    public Student(int id, int age, String name) {
        this.id = id;
        this.age = age;
        this.name = name;
    }
}
```

## :two:List

### 2.1 List集合子类特点

**ArrayList**：底层数据结构是数组，查询快，增删慢

**LinkedList**：底层数据结构是链表，查询慢，增删快

### 2.2 List集合排序方法

以ArrayList实现类为例

由于比较器排序的过程和Set集合排序的方法相同，但是需要知道的是，**Set集合的排序可以直接在创建Set集合的时候进行比较器排序**（通过创建比较器new Comparator）,而**List列表需要用Collections里的sort方法进行排序**。

**:star:但是排序比较器的构造是一致的，只是排序的方法不一样**

```java
package com.reback;

import java.util.Collections;
import java.util.Comparator;
import java.util.LinkedList;
import java.util.List;

/**
 * @Author Lunau
 * @Create 2022-03-20 12:57
 * @Description 关于list排序探究
 * @Result
 */
public class AboutList {
    public static void main(String[] args) {
        //创建list集合对象    以LikeList为例
        List<Student> list = new LinkedList<>();

          //添加元素到集合
        list.add(new Student(2014,20,"李白"));
        list.add(new Student(2013,21,"杜甫"));
        list.add(new Student(2015,22,"王维"));
        list.add(new Student(2014,25,"李贺"));
        //采用
        Collections.sort(list, new Comparator<Student>() {
            @Override
            public int compare(Student s1, Student s2) {
                //按照id从小到大，age从大到小排序
                int num1 = s1.id-s2.id;
                int num2 = num1==0?s2.age-s1.age:num1;
                return num2;
            }
        });
        //打印集合
        for(Student s:list) {
            System.out.println(s.name+" "+s.id+" "+s.age);
        }
    }
}
```

## :three: 容易混淆的

### Collection和Collections的区别

Collection：是一个集合接口。它提供了对集合对象进行基本操作的通用接口方法。Collection接口在Java类库中有很多具体的实现。Collection接口的意义是为各种具体的集合提供最大化的统一操作方式。

Collections：是一个包装类。它包含各种有关[集合](https://so.csdn.net/so/search?q=集合&spm=1001.2101.3001.7020)操作的**静态多态方法**。此类**不能实例化**，就像一个工具类，服务于Java的Collection框架。

简单理解：Collection是**定义集合**的，实现集合；Collections是**管理集合**，用来对集合进行**操作**的，比如排序、反转等操作。

### Comparator比较器

该比较器中提供了许多方法，需要终点记忆的是compara()方法，在自定义排序规则的时候就要用对该方法进行重写，

重写的规则前面已经探讨过了。

# :point_right:总结

写这篇总结的起因是在写蓝桥杯真题利用List列表排序时，发现过去学习的知识忘记了好多，之前一直用的Set的比较器排序（comparator）比较多，突然让用列表进行比较器排序，发现和自己想的并不是那么一回事，列表排序的方法和set有些不同，查阅一些其他人的博客，加上自己的一些debug，总结出一点能用的知识。

