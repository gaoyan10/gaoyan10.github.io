---
layout: post
title: Java中HashMap的key的Hash值修改问题
categories: [技术博客]
tags: [Java, HashMap, hashCode]
---

首先这个一个很无聊的问题，因为真正大家在用HashMap的时候，一般都是用简单类型的封装类，比如Integer, Long或者String不可修改的对象来作为key，所以不会遇到这篇博客讨论的问题。
先看这样一段代码：

```
public class MyObject {
	private int value = 0;
	public MyObject(int value) {
		this.value = value;
	}
	public void setValue(int value) {
		this.value = value;
	}
	@Override
	public int hashCode() {
		return value;
		//return super.hashCode();
	}
	
}
```
MyObject是一个自定义类。它修改了hashCode方法，Object中的hashCode是一个native方法，返回了对象在虚拟机中的地址（也不全是地址），子类的实现各有不同，String类的实现如下，和内容完全相关。

```
public int hashCode() {
    int h = hash;
    if (h == 0) {
        int off = offset;
        char val[] = value;
        int len = count;
 
            for (int i = 0; i < len; i++) {
                h = 31*h + val[off++];
            }
            hash = h;
        }
        return h;
}

```

在Main方法里，我们这么调用

```
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map.Entry;


public class Main {
	public static void main(String[] args) {
		HashMap<MyObject, MyObject> a = new HashMap<MyObject, MyObject>();
		MyObject aItem = new MyObject((int)'a');
		MyObject bItem = new MyObject((int)'b');
		a.put(aItem, new MyObject((int)'a'));
		a.put(bItem, new MyObject((int)'b'));
		Iterator<Entry<MyObject, MyObject>> it = a.entrySet().iterator();
		while (it.hasNext()) {
			Entry<MyObject, MyObject> item = it.next();
			System.out.println(item.getKey() + " " + item.getValue());
		}
		System.out.println(a.get(aItem));
		aItem.setValue(10);
		it = a.entrySet().iterator();
		while (it.hasNext()) {
			Entry<MyObject, MyObject> item = it.next();
			System.out.println(item.getKey() + " " + item.getValue());
		}
		System.out.println(a.get(aItem));
	}
}

```

输出结果如下：

```
MyObject@61 MyObject@61
MyObject@62 MyObject@62
MyObject@61
MyObject@a MyObject@61
MyObject@62 MyObject@62
null

```

我们要讨论的就是为什么第二次是null。
简单的说，value是根据key的hash存放在不同的Hash桶上，key的hashCode变了，自然无法找到Value所在的位置。

```
public V get(Object key) {
        Node<K,V> e;
        return (e = getNode(hash(key), key)) == null ? null : e.value;
}

final Node<K,V> getNode(int hash, Object key) {
        Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
        if ((tab = table) != null && (n = tab.length) > 0 &&
            (first = tab[(n - 1) & hash]) != null) {
            if (first.hash == hash && // always check first node
                ((k = first.key) == key || (key != null && key.equals(k))))
                return first;
            if ((e = first.next) != null) {
                if (first instanceof TreeNode)
                    return ((TreeNode<K,V>)first).getTreeNode(hash, key);
                do {
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        return e;
                } while ((e = e.next) != null);
            }
        }
        return null;
    }
    
```

getNode里返回了null。
总结一下，HashMap是根据key的hashCode来进行Hash映射，存储和取出value，所以一旦key的hashCode改变了，value肯定无法获取，所以key尽量使用hashCode不会改变的对象， 而默认的Object的hashCode是jvm的内存地址，所以不同的两个对象，虽然成员可能都一样，但是也无法get到正确的value。而String由于hashCode就是内容得到的，所以只要内容一致，一定能获取到需要的value。而Integer等简单类型封装类也是一样的道理。

```
@Override
public int hashCode() {
    return Integer.hashCode(value);
}
```

这里有一篇介绍HashMap的不错的文章
[http://www.cnblogs.com/dolphin0520/p/3681042.html](http://www.cnblogs.com/dolphin0520/p/3681042.html)