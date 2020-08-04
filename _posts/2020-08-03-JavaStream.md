---
layout:     post
title:      JavaSteam
subtitle:   李嘉图的博客
date:       2020-08-03
author:     李嘉图
header-img: img/about-LJT.jpg
catalog: false
tags:
- Java
---

#  Java Stream 

## Stream(1) 

• Stream流 

> –*a sequence of elements from source that supports aggregate operations* 
>
> –*sequence of elements:* 一个流对外提供一个接口，可以访问到一串 特定的数据。流不存储元素，但是可以根据需要进行计算转化 
>
> –*source*：数据来源，如数据结构，数组，文件等 
>
> –*aggregate operation*：聚合操作，流支持像SQL操作或者其他函数 式语言的操作，如filter/map/reduce/find/match/sorted等。

```java
import java.util.Arrays;
import java.util.List;

public class SimpleStreamDemo {

    public static void main(String[] args) {
        String[] planets = new String[] {
                "Mercury", "Venus", "Earth",
                "Mars", "Jupiter", "Saturn",
                "Uranus", "Neptune" };

        //查找星球名字大于等于5个字母
        long count = 0;
        for(String p : planets)	{
            if(p.length() > 5) {
                count ++;
            }
        }

        List<String> pList = Arrays.asList(planets);

        //采用流方法
        count = pList.stream()
                .filter(p->p.length()>5).count();//filter  	过滤器
        System.out.println(count);
        //采用并行流方法
        count = pList.parallelStream()
                .filter(p->p.length()>5).count();


    }

}

```



------

## Stream(2) 

• Stream流 

> –*pipelining*: **很多流操作也是返回一个流** 
>
> –*Internal Iteration*: **流操作进行迭代，用户感知不到循环遍历**

------

## Stream(4) 

• Stream语法 

> **–类似SQL语句，遵循“做什么而非怎么做”原则**

```java
import java.util.*;
import java.util.stream.Collectors;

public class StreamDemo {

    public static void main(String[] args) {
        List<Transaction> transactions = new ArrayList<Transaction>();
        transactions.add(new Transaction(1, 100, "batch"));
        transactions.add(new Transaction(3, 80, "grocery"));
        transactions.add(new Transaction(6, 120, "grocery"));
        transactions.add(new Transaction(7, 40, "batch"));
        transactions.add(new Transaction(10, 50, "grocery"));

        // 采用传统方法
        traditionalMethod(transactions);
        System.out.println("-------------------------------");
        streamMethod(transactions);
    }

    public static void traditionalMethod(List<Transaction> transactions) {
        // 过滤保留type = "grocery"的记录
        List<Transaction> groceryTransactions = new ArrayList<>();
        for (Transaction t : transactions) {
            if (t.getType().equals("grocery")) {
                groceryTransactions.add(t);
            }
        }

        // 根据value对符合的记录排序,从高到低
        Collections.sort(groceryTransactions, new Comparator<Transaction>() {
            public int compare(Transaction t1, Transaction t2) {
                return t2.getValue().compareTo(t1.getValue());
            }
        });

        // 获取记录中的id字段
        List<Integer> transactionIds = new ArrayList<>();
        for (Transaction t : groceryTransactions) {
            transactionIds.add(t.getId());
        }

        // 输出结果
        transactionIds.forEach(System.out::println);
    }
    //重点    一句话     解决
    public static void streamMethod(List<Transaction> transactions) {
        transactions.stream().filter(t->t.getType().equals("grocery"))
                .sorted(Comparator.comparing(Transaction::getValue).reversed())
                .map(Transaction::getId)
                .collect(Collectors.toList())
                .forEach(System.out::println);
    }
}

class Transaction {
    int id;
    Integer value;
    String type;

    public Transaction(int id, int value, String type) {
        super();
        this.id = id;
        this.value = value;
        this.type = type;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public Integer getValue() {
        return value;
    }

    public void setValue(int value) {
        this.value = value;
    }

    public String getType() {
        return type;
    }

    public void setType(String type) {
        this.type = type;
    }

}

```

------

Stream(5) 

• 流的工作流程 

> –流的创建 
>
> –流的转换，将流转换为其他流的中间操作，可包括多个步骤(惰性 操作) 
>
> –**流的计算结果。这个操作会强制执行之前的惰性操作。这个步骤以 后，流就再也不用了**。

------

## Stream的创建 

Stream的工作流程 

• 流的工作流程 

> –创建一个流 
>
> –指定将流转换为其他流的中间操作，可包括多个步骤(惰性操作) 
>
> –应用终止操作，从而产生结果。这个操作会强制执行之前的惰性操 作。这个步骤以后，流就再也不用了。

------

```java
import java.io.IOException;
import java.math.BigInteger;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.util.*;
import java.util.regex.Pattern;
import java.util.stream.*;

public class StreamCreator {

	public static void main(String[] args) throws IOException {

		// Collection子类产生stream
		Stream<String> a1 = new ArrayList<String>().stream();
		Stream<String> a2 = new HashSet<String>().stream();

		// 使用Arrays.stream 转化数组为stream
		Stream<String> b1 = Arrays.stream("a,b,c,d,e".split(","), 3, 5);

		// 数组产生stream
		Stream<Integer> c1 = Stream.of(new Integer[5]);
		Stream<String> c2 = Stream.of("a,b,c".split(","));
		Stream<String> c3 = Stream.of("a", "b", "c");
		
		//空流
		Stream<String> d1 = Stream.empty();
		
		//无限流,使用generate方法，根据Lambda表达式产生
		Stream<String> e1 = Stream.generate(()->"hello");
		Stream<Double> e2 = Stream.generate(Math::random);
		
		//无限流，使用iterate方法，第一个参数是种子
		//第二个是Lambda表达式
		Stream<BigInteger> e3 = Stream.iterate(BigInteger.ZERO, n -> n.add(BigInteger.ONE));
		
		//Files的lines方法读取一个文件，产生每一行内容的Stream
		Stream<String> contents = Files.lines(Paths.get("C:/abc.txt")); 
		
		//Pattern的splitAsStream方法，根据一个正则表达式，将内容分为一个字符串的Stream
		Stream<String> words = Pattern.compile(",").splitAsStream("a,b,c");
		
		//words.forEach(System.out::println);
	}

}






```



------



## Stream创建(1) 

• Collection接口的stream方法

> –还有其他的子类，如LinkedList, LinkedSet, TreeSet, Vector等

• Arrays.stream可以将数组转为Stream

------

## Stream创建(2) 

• 利用Stream类进行转化 

> –of 方法，直接将数组转化
> –empty方法，产生一个空流

------

## Stream创建(3) 

• 利用Stream类进行转化 

> –generate 方法，接收一个Lambda表达式
> –iterate方法，接收一个种子，和一个Lambda表达式

------

## Stream创建(4) 

• 基本类型流(只有三种) 

> –IntStream，LongStream，DoubleStream
>
> ```java
> import java.util.Arrays;
> import java.util.stream.IntStream;
> import java.util.stream.Stream;
> 
> public class BasicTypeStream {
> 
> 	public static void main(String[] args) {
> 		
> 		IntStream s1 = IntStream.of(1,2,3,4,5);
> 		
> 		s1 = Arrays.stream(new int[] {1,2,3});		
> 		s1 = IntStream.generate(()->(int)(Math.random() * 100));		
> 		s1 = IntStream.range(1,5); //1,2,3,4  step 1		
> 		s1 = IntStream.rangeClosed(1,5); //1,2,3,4,5
> 		
> 		IntStream s2 = IntStream.of(1,2,3,4,5);
> 		Stream<Integer> s3 = s2.boxed();//基本类型流与对象流的转换
> 		IntStream s5 = s3.mapToInt(Integer::intValue);
> 	}
> 
> }
> 
> ```
>
> 

------

## Stream创建(5) 

• 并行流 

> –使得所有的中间转换操作都将被并行化 
>
> –Collections.parallelStream()将任何集合转为并行流 
>
> –Stream.parallel()方法，产生一个并行流



***–注意：需要保证传给并行流的操作不存在竞争***

------

## Stream创建(6) 

• 其他类/方法产生Stream流 

> –Files.lines方法
>
> –Pattern的splitAsStream方法

------

### 总结

• 流的内容来源 

> –Collection的子类 
>
> –数组 
>
> –Lambda表达式产生 
>
> –文件 
>
> –其他的方法…………

------

## Stream的转换

### Stream转换(1) 

• Stream的转换，是从一种流到另外一种流 

> –过滤，去重 
>
> –排序 
>
> –转化 
>
> –抽取/跳过/连接 
>
> –其他

------

### Stream转换(2) 

• 过滤filter 

> –filter(Predicate<? super T> predicate) 
>
> –接收一个Lambda表达式，对每个元素进行判定，符合条件留下

```java
import java.util.stream.Stream;

public class StreamFilter {

	public static void main(String[] args) {
		System.out.println("======对每个元素进行过滤判定================");
		
		Stream<Integer> s1 = Stream.of(1, 2, 3, 4, 5);
		Stream<Integer> s2 = s1.filter(n -> n>2);
		s2.forEach(System.out::println);
		//3, 4, 5
	}
}

```

------

### Stream转换(3) 

• 去重distinct –distinct() 

> –对流的元素进行过滤，去除重复，只留下不重复的元素

• 去重distinct 

> –distinct() 对流的元素进行过滤，去除重复，只留下不重复的元素 
>
> –对象的判定，先调用hashCode方法，再调用equals方法

------

### Stream转换(5) 

• 排序sorted 

> –sorted() 
>
> –对流的基本类型包装类元素进行排序
>
> –对流的自定义对象元素进行排序，调用对象的compareTo方法

```java
import java.util.ArrayList;
import java.util.Comparator;
import java.util.stream.Stream;

public class StreamOrder {
	public static void main(String[] args) {
		System.out.println("=======对基本类型包装类对象进行排序======");
		
		Stream<Integer> s1 = Stream.of(3,2,4,1,5);
		Stream<Integer> s2 = s1.sorted();
		s2.forEach(System.out::println);
		//1, 2, 3, 4, 5
		
		System.out.println("=======提供Comparator进行排序===============");
		
		
		String[] planets = new String[] { 
				"Mercury", "Venus", "Earth", 
				"Mars", "Jupiter", "Saturn", 
				"Uranus", "Neptune" };
		
		Stream<String> s3 = Stream.of(planets).sorted(
				Comparator.comparing(String::length));
		s3.forEach(System.out::println);
		
		System.out.println("========对自定义对象进行排序==============");
		
		ArrayList<Cat> cats = new ArrayList<>();
		cats.add(new Cat(3));
		cats.add(new Cat(2));
		cats.add(new Cat(5));
		cats.add(new Cat(1));
		cats.add(new Cat(4));
		Stream<Cat> s4 = cats.stream().sorted();
		s4.forEach(System.out::println);
	}	
}

class Cat implements Comparable<Cat> {
	private int size;

	public Cat(int size) {
		super();
		this.size = size;
	}

	@Override
	public int compareTo(Cat o) {
		Cat c = new Cat(5);
		System.out.println(c.size);
		return this.size - o.size;	
	}
	
	public String toString()
	{
		return "Size:" + size;
	}
}

```

------

```java
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Stream;

public class StreamMap {

	public static void main(String[] args) {
		System.out.println("======用方法引用对每个元素进行计算=====");
		
		//map使用方法引用，输入一个参数，返回一个结果
		Stream<Double> s1 = Stream.of(-1.5, 2.5, -3.5);
		Stream<Double> s2 = s1.map(Math::abs);
		s2.forEach(System.out::println);
		
		System.out.println("======用Lambda表达式对每个元素进行计算=");
		
		//map使用Lambda表达式，输入一个参数，返回一个结果
		Stream<Integer> s3 = Stream.of(1,2,3,4,5);
		Stream<Integer> s4 = s3.map(n->n*n);
		s4.forEach(System.out::println);
		
		System.out.println("======对每个元素进行计算，返回Stream==");
		
		//map使用方法引用，输入一个参数，返回一个Stream
		String[] planets = new String[] { 
				"Mercury", "Venus", "Earth"};
		
		Stream<String> allLetters2 = 
				Stream.of(planets).flatMap(word -> letters(word));
		allLetters2.forEach(System.out::print);
		//flatMap 执行一对多的转换，然后将所有的Map都展开
		//['M','e','r','c','u','r','y',
		// 'V','e','n','u','s',
		// 'E','a','r','t','h']
		
		Stream<Stream<String>> allLetters = 
				Stream.of(planets).map(word -> letters(word));
		allLetters.forEach(System.out::print);
		//[['M','e','r','c','u','r','y'],
		// ['V','e','n','u','s'],
		// ['E','a','r','t','h']]
		
		System.out.println("======对每个元素进行计算，最后综合返回经过合并的Stream==");
		
		
		
	}
	
	public static Stream<String> letters(String word) {
		List<String> result = new ArrayList<>();
		for(int i=0;i<word.length();i++)
		{
			result.add(word.substring(i, i+1));
		}
		return result.stream();
	}
}



```

### Stream转换(8) 

***map*** 

> –利用方法引用对流每个元素进行函数计算
>
> –利用Lambda表达式对流每个元素进行函数计算
>
> –利用方法引用，对流每个元素进行函数计算返回Stream
>
> –map –利用方法引用，对流每个元素进行函数计算返回Stream，并合并
>
> –抽取limit
>
> –跳过skip
>
> –连接concat

------

## 总结

• 流的转换 

> –从一个流得到另外一个流 
>
> –一个流只能使用一次 
>
> –转换过程，需要使用类本身的方法，如compareTo, hashCode, equals等方法

------

## Java Stream 

### Stream的工作流程 

• 流的工作流程 

> –创建一个流 
>
> –指定将流转换为其他流的中间操作，可包括多个步骤(惰性操作) 
>
> –应用终止操作，从而产生结果。这个操作会强制执行之前的惰性操 作。这个步骤以后，流就再也不用了。

------

Optional(1) 

• Optional<T> 

> –一个包装器对象 
>
> –要么包装了类型T的对象，要么没有包装任何对象(*还是null* ) 
>
> –*如果T有值，那么直接返回T的对象* 
>
> *–如果T是null，那么可以返回一个**替代物***

Optional(2) 

```java
import java.util.Optional;

public class StringOptionalTest {

	public static void main(String[] args) {
		Optional<String> s1 = Optional.of(new String("abc"));
		String s2 = s1.get();
		System.out.println("s2: " + s2); //abc
		
		Optional<String> s3 = Optional.empty();
		String s4 = s3.orElse("def"); 
		System.out.println("s4: " + s4); //def
		
		
		Optional<String> s5 = Optional.of(new String("abc"));
		Optional<String> s6 = Optional.empty();
		String s7 = null;
		Optional<String> s8 = Optional.ofNullable(s7); 
		//s7不为Null，s8就是s7, 否则s8就为Optional.empty()
		
	}

}

```



> • Optional<T>

Optional(3) 

• Optional<T>创建 

> –of方法 
>
> –empty方法 
>
> *–ofNullable方法，对于对象有可能为null情况下，安全创建*

**Optional(4)** 

• Optional<T>使用 

> –get方法，获取值，*不安全的用法* 
>
> –**orElse**方法，获取值，如果为null，采用替代物的值 （推荐）
>
> –**orElseGet**方法，获取值，如果为null，采用Lambda表达式值返回 （推荐）
>
> –orElseThrow方法，获取值，如果为null，*抛出异常* 
>
> –ifPresent方法，判断是否为空，不为空返回true 
>
> –isPresent(*Consumer*), 判断是否为空，如果不为空，则进行后续 Consumer操作,如果为空，则不做任何事情 
>
> –map(Function), 将值传递给Function函数进行计算。如果为空，则不 计算

Optional(5) 

• Optional<T>**注意事项** 

> *–直接使用get，很容易引发NoSuchElementException异常*
>
> *–使用isPresent判断值是否存在，这和判断null是一样的低效*

### **总结**

• Optional 

> **–数据对象容器** 
>
> **–Java用来解决NullPointerException的一把钥匙** 
>
> **–在流运算中，*避免对象是否null的判定***

------

## Stream的工作流程 

• 流的工作流程 

> –创建一个流 
>
> –指定将流转换为其他流的中间操作，可包括多个步骤(惰性操作) 
>
> –应用终止操作，从而产生结果。这个操作会强制执行之前的惰性操 作。这个步骤以后，流就再也不用了。

流的计算结果(1) 

• 流的计算 

> –简单约简(聚合函数)：count/max/min/… 
>
> –自定义约简：reduce 
>
> –查看/遍历元素：iterator/forEach 
>
> –存放到数据结构中



流的计算结果(2) 

• 流的计算：简单约简(聚合函数) 

> –count(), 计数 
>
> –max(Comparator)，最大值，需要比较器 
>
> –min(Comparator)，最小值，需要比较器 
>
> –findFirst(), 找到第一个元素 
>
> –findAny(), 找到任意一个元素 
>
> –anyMatch(Predicate)，如有任意一个元素满足Predicate，返回true 
>
> –allMatch(Predicate)，如所有元素满足Predicate，返回true 
>
> –noneMatch(Predicate)，如没有任何元素满足Predicate，返回true

```JAVA
import java.util.Arrays;
import java.util.Optional;
import java.util.stream.Stream;

public class SimpleAggregation {

	public static void main(String[] args) {
		
		Integer[] a = new Integer[] {2,4,6,8};		
		
		Stream<Integer> s1 = Stream.of(a);
		long countResult = s1.filter(n-> n>0).count();
		System.out.println("the count result of s1 is " + countResult);
		
		Stream<Integer> s2 = Stream.of(a);
		Optional<Integer> maxResult = s2.max((n1,n2)->n1-n2);
		System.out.println("the max result of s2 is " + maxResult.get());
		
		Stream<Integer> s3 = Stream.of(a);
		Optional<Integer> minResult = s3.min((n1,n2)->n1-n2);
		System.out.println("the min result of s3 is " + minResult.get());
		
		
		Stream<Integer> s4 = Stream.of(a);
		Optional<Integer> first = s4.findFirst();
		System.out.println("the first result of s4 is " + first.get());
		
		Stream<Integer> s5 = Stream.of(a);
		Optional<Integer> random = s5.findAny();
		System.out.println("the random result of s5 is " + random.get());
		
		Stream<Integer> s6 = Stream.of(a);
		System.out.println(s6.anyMatch(n -> n>6));
		Stream<Integer> s7 = Stream.of(a);
		System.out.println(s7.allMatch(n -> n>6));
		Stream<Integer> s8 = Stream.of(a);
		System.out.println(s8.noneMatch(n->n>100));

	}

}


```

流的计算结果(3) 

• 流的计算：自定义约简 

> –reduce，传递一个二元函数BinaryOperator，对流元素进行计算 
>
> –如求和、求积、字符串连接等

```java
import java.util.Optional;
import java.util.stream.Stream;

public class Reduce {

	public static void main(String[] args) {
		
		Integer[] a = new Integer[] {2,4,6,8};
		
		Stream<Integer> s1 = Stream.of(a);
		Optional<Integer> sum = s1.reduce(Integer::sum);
		System.out.println(sum.get());
		
		Stream<Integer> s2 = Stream.of(a);
		Optional<Integer> product = s2.reduce((x,y)->x*y);
		System.out.println(product.get());
		
		Stream<Integer> s3 = Stream.of(a);
		Integer product3 = s3.reduce(1,(x,y)->x*y);
		System.out.println(product3);
		
		String[] b = new String[] {"abc","def","ghi"};
		Stream<String> s4 = Stream.of(b);
		String bigStr = s4.reduce("",(x,y)->x+y);
		System.out.println(bigStr);
		

	}

}


```

流的计算结果(4) 

• 流的计算：查看/遍历元素 

> –iterator()，遍历元素 
>
> –forEach(Consumer)，应用一个函数到每个元素上

```java
import java.util.Iterator;
import java.util.stream.Stream;

public class StreamView {

	public static void main(String[] args) {
		Integer[] a = new Integer[] {2,4,6,8};
		
		Stream<Integer> s1 = Stream.of(a);
		Iterator<Integer> it1 = s1.filter(n->n>2).iterator();
		while(it1.hasNext()) {
			System.out.println(it1.next());
		}
		
		Stream<Integer> s2 = Stream.of(a);
		s2.filter(n->n>2).forEach(System.out::println);
		
		
	}

}


```

流的计算结果(5) 

• 流的计算：存放到数据结构中 

> –toArray()，将结果转为数组 
>
> –collect(Collectors.toList()),将结果转为List 
>
> –collect(Collectors.toSet()),将结果转为Set 
>
> –collect(Collectors.toMap()), 将结果转为Map 
>
> –collect(Collectors.joining()), 将结果连接起来

```java
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.Map;
import java.util.Set;
import java.util.stream.Collectors;
import java.util.stream.Stream;

public class StreamCollect {

	public static void main(String[] args) {
		Integer[] a = new Integer[] {2,4,6,8};
		
		//将流存储为List
		Stream<Integer> s1 = Stream.of(a);
		List<Integer> list1 = s1.collect(Collectors.toList());
		
		//将流存储为指定的LinkedList
		Stream<Integer> s2 = Stream.of(a);
		List<Integer> list2 = s2.collect(Collectors.toCollection(LinkedList::new));
		
		//将流存储为Set
		Stream<Integer> s3 = Stream.of(a);
		Set<Integer> set1 = s3.collect(Collectors.toSet());
		
		//将流变换为字符流，并连接起来
		Stream<Integer> s4 = Stream.of(a);
		String result = s4.map(String::valueOf).collect(Collectors.joining());
		System.out.println(result); //2468
		
		//将流变换为字符流，并连接起来
		Stream<Integer> s5 = Stream.of(a);
		String result2 = s5.map(String::valueOf).collect(Collectors.joining(","));
		System.out.println(result2); //2,4,6,8
		
		
		
		List<Person> persons = new ArrayList<Person>();
		persons.add(new Person(1, "Jerry"));
		persons.add(new Person(2, "Tom"));
		
		//将流存储为Map
		Stream<Person> s6 = persons.stream();
		Map<Integer, String> map1 = s6.collect(Collectors.toMap(Person::getId, Person::getName));
		for(Integer i:map1.keySet())
		{
			System.out.println("id:" + i + ", name:" + map1.get(i));
		}
		
	}

}

class Person
{
	int id;
	String name;
	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public Person(int id, String name) {
		super();
		this.id = id;
		this.name = name;
	}	
}

```

流的计算结果(6) 

• 流的高阶计算： 

> –分组groupingBy和分区partitionBy 
>
> –分组后的约简 
>
> > • counting 
> >
> > • summing 
> >
> > • maxBy 
> >
> > • minBy 
>
> –以上方法均在java.util.stream.Collectors中

### 总结

• 流的计算 

> –流的最终运算结果 
>
> –惰性计算，启动先前的转换环节
>
> –三种结果 
>
> > • 约简 
> >
> > • 遍历/查看 
> >
> > • 转存为其他的数据结构
>
> 

------

##  Java Stream的应用



Stream的工作流程 

• Stream的工作流程 

> –创建一个流 
>
> –将流转换为其他流的中间操作，可包括多个步骤(惰性操作，lazy) 
>
> ***–应用终止操作，从而产生结果 –只有第三步启动，才会执行第二步的惰性操作***



Stream的优点 

• Java Stream的优点 

> –统一转换元素
>
> –过滤元素 
>
> –利用单个操作合并元素 
>
> –将元素序列存放到某一个集合中 
>
> –搜索满足某些条件的元素的序列 
>
> ***–类似SQL操作，遵循“做什么而非怎么做”原则*** 
>
> ***–简化了串行/并行的大批量操作***
>
> 



Stream和循环迭代代码的**区别** 

• Java Stream vs 循环迭代代码 

> –Stream广泛使用Lambda表达式，只能读取外围的final或者effectively final变量，循环迭代代码可以读取/修改任意的局部变量。 
>
> –在循环迭代代码块中，可以随意break/continue/return，或者抛出异 常，而Lambda表达式无法完成这些事情。 
>
> ***–Stream流不是淘汰循环迭代代码，应该是两者相互搭配使用。***

------

Stream应用注意事项(1) 

> • 一个流，一次只能一个用途，不能多个用途，用了不能再用

```java
import java.util.Optional;
import java.util.stream.Stream;

public class MultipleUse {

	public static void main(String[] args) {
		
		Integer[] a = new Integer[] {2,4,6,8};
		
		Stream<Integer> s1 = Stream.of(a);
		Stream<Integer> s2 = s1.filter(x->x>4);		
		//Stream<Integer> s3 = s1.filter(x->x>2);  //error
		
		s1.forEach(System.out::println);         //error
		s2.forEach(System.out::println);
		//s2.forEach(System.out::println);           //error
		//报错：非法 状态 异常

	}

}


```





Stream应用注意事项(2) 

> • 避免创建无限流

```java
import java.util.stream.IntStream;

public class InfiniteStream {

	public static void main(String[] args) {
		//无限流
        //谨慎
		IntStream.iterate(0, i -> i + 1)
			.forEach(System.out::println);
		
		//需要对流元素进行限制
		IntStream.iterate(0, i -> i + 1)
		.limit(10).forEach(System.out::println);
		
		//又一个无限流
		IntStream.iterate(0, i -> ( i + 1 ) % 2)
		.distinct().limit(10).forEach(System.out::println);

	}

}


```





Stream应用**注意**事项(3) 

> • 注意操作顺序

```java
import java.util.stream.IntStream;

public class OrderStream {

	public static void main(String[] args) {
		IntStream.iterate(0, i -> i + 1)
		.limit(10) // LIMIT limit
		.skip(5) // OFFSET offset
		.forEach(System.out::println); //5-9
		
		IntStream.iterate(0, i -> i + 1)		
		.skip(5) // OFFSET  skip（去掉前面的5个元素）
		.limit(10) // LIMIT
		.forEach(System.out::println); //5-14

	}

}


```







Stream应用**注意**事项(4) 

• *谨慎使用并行流* 

> –底层使用Fork-Join Pool，处理计算密集型任务 
>
> –数据量过小不用 
>
> –数据结构不容易分解的时候不用，如LinkedList等 
>
> –数据频繁拆箱装箱不用 
>
> –涉及findFirst或者limit的时候不用

```java
import java.util.stream.IntStream;

public class ParallelStream {

	public static void main(String[] args) {
		
		IntStream s1 = IntStream.range(1,10000000); 
		long evenNum = s1.parallel().filter(n->n%2==0).count();
		System.out.println(evenNum);

	}

}


```





Stream应用**注意**事项(5) 

• Stream ***vs*** Collection 

> –Stream和Collection两者可以互相转化 
>
> –如果数据可能无限，用Stream 
>
> –如果数据很大很大，用Stream 
>
> –如果调用者将使用查找/过滤/聚合等操作，用Stream 
>
> –当调用者使用过程中，发生数据改变，而调用者需要对数据一致性有 较高要求，用Collection 





## 总结

• Java Stream 

> ***–Stream很强大，效率很高*** 
>
> ***–需要认真学习，小心使用***
