# 1.起因

在学习瑞吉外卖的时候，老师经常把一些逻辑语句放到controller中，导致我对这些代码是该写到controller还是service中很不清楚，我将那段代码发到了java吧，希望大家能给一点建议，代码中有个在foreach中进行查询数据库id删除的操作，大佬们说不能循环中删除，但我也不明白原因，只好查询资料找到了如下的信息。

![Snipaste_2022-08-12_17-32-33](D:\软件\工具\截图工具\自动保存、\Snipaste_2022-08-12_17-32-33.png)





# 2.学习

## 学习java基础时

我翻看了之前学习java基础时的视频，找到了关于foreach的视频，里面粗略的演示了报错的操作（在循环中删除集合元素），虽然没有具体讲原因，但是我是确实没有记住这个异常。

## 现在

我看了一些博主写的资料，也通过debug去看了源代码的执行过程，发现确实会出现异常**ConcurrentModificationException**，

**贴上发生异常的代码。**

```java
 private class ListItr implements ListIterator<E> {
        private Node<E> lastReturned;
        private Node<E> next;
        private int nextIndex;
        private int expectedModCount = modCount;	// modCount会随着对集合的操作次数而相应增加

     // 省略部分代码
     
        public void remove() {
            checkForComodification();
            if (lastReturned == null)
                throw new IllegalStateException();

            Node<E> lastNext = lastReturned.next;
            unlink(lastReturned);
            if (next == lastReturned)
                next = lastNext;
            else
                nextIndex--;
            lastReturned = null;
            expectedModCount++;
        }

        public void set(E e) {
            if (lastReturned == null)
                throw new IllegalStateException();
            checkForComodification();
            lastReturned.item = e;
        }

        public void add(E e) {
            checkForComodification();
            lastReturned = null;
            if (next == null)
                linkLast(e);
            else
                linkBefore(e, next);
            nextIndex++;
            expectedModCount++;
        }

        public void forEachRemaining(Consumer<? super E> action) {
            Objects.requireNonNull(action);
            while (modCount == expectedModCount && nextIndex < size) {
                action.accept(next.item);
                lastReturned = next;
                next = next.next;
                nextIndex++;
            }
            checkForComodification();
        }

        final void checkForComodification() {
            if (modCount != expectedModCount)
                // 异常抛出
                throw new ConcurrentModificationException();	
        }
    }
```



我们知道 java.util.ArrayList不是线程安全的，因此如果在使用迭代器的过程中有其他线程修改了list，那么将抛出ConcurrentModificationException，这就是所谓fail-fast策略。这一策略在[源码](https://so.csdn.net/so/search?q=源码&spm=1001.2101.3001.7020)中的实现是通过 modCount 域，modCount 顾名思义就是修改次数，对ArrayList 内容的修改都将增加这个值，那么在迭代器初始化过程中会将这个值赋给迭代器的 expectedModCount。在迭代过程中，**判断 modCount 跟 expectedModCount 是否相等，如果不相等就表示已经有其他线程修改了 list**：注意到 modCount 声明为 volatile，保证线程之间修改的可见性。

## 解决方式

使用迭代器进行操作时能够确保 **modCount 跟 expectedModCount 相等**，从而不会抛出异常

```java
package top.plutos;

import java.util.*;

/**
 * @Author Lunau
 * @Create 2022-08-15 10:13
 * @Description 关于foreach的使用
 * @Result
 */

public class foreach {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("1");
        list.add("2");
        list.add("3");
        list.add("4");

        //正确方式
        Iterator<String> iterator = list.iterator();
        while (iterator.hasNext()) {
            String next = iterator.next();
            if ("2".equals(next)) {
                iterator.remove();
            }
        }

//
//        //错误方式
//        for (String s : list) {
//            //删除第二个
//            if ("2".equals(s)) {
//                list.remove(s);
//            }
//        }
        System.out.println(list.toString());
    }
}
```

# 参考：

[Java 集合中常见 checkForComodification()方法的作用? modCount和expectedModCount作用?_Dwxqnswxl的博客-CSDN博客_expectedmodcount](https://blog.csdn.net/weixin_40254498/article/details/81386920)

[ArrayList底层jdk源码之modCount、expectedModCount解读_技匠三石弟弟的博客-CSDN博客_expectedmodcount](https://blog.csdn.net/xiaoleilei666/article/details/102881314)

[LinkedList之modCount和expectedModCount - 不吃陈皮 - 博客园 (cnblogs.com)](https://www.cnblogs.com/zhangcaiwang/p/7131035.html)

[JAVA集合为什么不能在foreach 循环中添加或删除元素？_java编程艺术的博客-CSDN博客_foreach不能增加删除](https://blog.csdn.net/penriver/article/details/117743726)