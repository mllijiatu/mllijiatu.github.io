---
layout:     post
title:      Java-Lambda表达式
subtitle:   李嘉图的博客
date:       2020-07-28
author:     李嘉图
header-img: img/about-LJT.jpg
catalog: false
tags:
- Java
---
 Lambda表达式 

```java
import java.util.Arrays;

public class StringOrderTest {

	public static void main(String[] args) {
		String[] planets = new String[] { 
				"Mercury", "Venus", "Earth", "Mars", 
				"Jupiter", "Saturn", "Uranus",
				"Neptune" };	

		System.out.println(Arrays.toString(planets));
		System.out.println("=======================");

		System.out.println("使用长度从小到大的比较器:");
		Arrays.sort(planets, new LengthAscComparator());
		System.out.println(Arrays.toString(planets));
		System.out.println("使用长度从大到小的比较器:");
		Arrays.sort(planets, new LengthDescComparator());
		System.out.println(Arrays.toString(planets));		

		System.out.println("=======================");
		System.out.println("使用Lambda, 长度从小到大:");
		Arrays.sort(planets, 
				(String first, String second) 
				   -> first.length() - second.length());
		System.out.println(Arrays.toString(planets));
		
		System.out.println("使用Lambda, 长度从大到小:");
		Arrays.sort(planets, (first, second) -> (-1) * (first.length() - second.length()));
		System.out.println(Arrays.toString(planets));
		
		System.out.println("使用Lambda, 长度从大到小:");
		Arrays.sort(planets, 
			(String first, String second) -> //（语法糖 推导符号）
			{
				int result = (-1) * (first.length() - second.length());
				return result;
			}
		);
		System.out.println(Arrays.toString(planets));

	}

}

```

------

## Lambda表达式(1) 

• 面向过程程序语言：参数传递是基本类型的变量 

• 面向对象语言 

–传递基本类型的变量 

–传递对象变量 

• 传递方法/代码块(函数式程序语言设计) 

–刚开始，Java为了简单性、一致性，拒绝此功能 

–为了市场和技术的需要，*Java 8开始*，支持此项功能，提出Java的 Lambda表达式实现

## Lambda表达式(2) 

• Lambda表达式 –数学家/逻辑学家Alonzo Church提出的λ演算

–表示可有效计算的数学函数，用字母λ表示 

–在计算机编程中，通常用来表示一个匿名函数 

–Lambda表达式可以当作参数，传递给其他高阶函数

------

## Java Lambda表达式(1) 

• Lambda表达式 

–参数，箭头，一个表达式

(String first, String second) 
				   -> first.length() - second.length());

–参数，箭头，{多个语句}

(String first, String second) -> //（语法糖 推导符号）
			{
				int result = (-1) * (first.length() - second.length());
				return result;
			}





## Java Lambda表达式(2) 

• Lambda表达式 

–类似于匿名方法，一个没有名字的方法 

–参数，箭头，表达式语句 

–可以忽略写参数类型 

***–坚决不声明返回值类型*** 

–没有public/protected/private/static/final等修饰符 

–单句表达式，将直接返回值，不用大括号 

*–带return语句， 算多句，必须用大括号*



## Java Lambda表达式(3) 

• Lambda表达式 

–无参数，***仅保留括号***，箭头，表达式

```java
public class RunnableTest {

	public static void main(String[] args) {
		
		new Thread(
			()->	//括号要保留
				{
					int sum=0;
					for(int i=1;i<=100;i++)
					{
						sum = sum + i;
					}
					System.out.println("总和:" + sum);
				}
			).start();

	}

}

```



## Java Lambda表达式(4) 

• Lambda表达式 

–一个参数，可省略括号，箭头，表达式

```java
public class AdderTest {

	public static void main(String[] args) {
		Adder  c1 = 
				x -> 
				{
					x++;
				};
		//不合法		
		Adder  c2 =
				x ->
				{
					if(x>0)
						return x+1;
				};
	}

}

```



## Java Lambda表达式(5) 

• Lambda表达式 

*–如果有返回值，返回值类型会在上下文推断出来的，无需声明* 

–只在某几个分支有返回值，这样是不合法的

------

## 函数式接口(1) 

**• 函数式接口(Functional  Interface)** 

–*是一个接口，符合Java接口的定义* 

–*只包含一个抽象方法的接口* 

–可以包括其他的default方法、static方法、private方法 

–由于只有一个未实现的方法，所以Lambda表达式可以自动填上这 个尚未实现的方法 

–采用Lambda表达式，可以自动创建出一个(**伪**)嵌套类的对象(*没有实 际的嵌套类class文件产生*)，然后使用，比真正嵌套类更加轻量，更 加简洁高效



**函数式接口(2)** 

• Lambda表达式

```java
Comparator<String> c2 = (String first, String second) ->       
			first.length() - second.length();	
```

• 匿名内部类

```java
Comparator<String> c = new Comparator<String>()	{
					public int compare(String first, String second)     
					{
						return first.length() - second.length();
					}
				};
```

• Lambda表达式(匿名函数)自动成为接口方法的实现

```java
import java.util.Arrays;
import java.util.Comparator;

public class StringOrderTest {

	public static void main(String[] args) {
		String[] planets = new String[] { 
				"Mercury", "Venus", "Earth", "Mars", 
				"Jupiter", "Saturn", "Uranus",
				"Neptune" };	

		System.out.println(Arrays.toString(planets));
		System.out.println("=======================");

		System.out.println("使用Lambda, 长度从小到大:");
		Arrays.sort(planets, 
				(String first, String second) 
				   -> first.length() - second.length());
		System.out.println(Arrays.toString(planets));
		
		System.out.println("使用Lambda, 长度从大到小:");
		Arrays.sort(planets, 
			(first, second) ->       
			{
				//形参不写类型  可以从上下文推断出来
				int result = (-1) * (first.length() - second.length());
				return result;
			}
		);
		System.out.println(Arrays.toString(planets));
		
		Comparator<String> c = new Comparator<String>()	{
					public int compare(String first, String second)     
					{
						return first.length() - second.length();
					}
				};
		
		Comparator<String> c2 = (String first, String second) ->       
			first.length() - second.length();		
			
			
		Arrays.sort(planets, c2);
		
		

	}

}

```

**函数式接口(3)** 

• Comparator接口：有2个未实现的方法
• 任何实现Comparator接口的类，肯定继承了Object，也就有 equals实现。

------

**系统自带的函数式接口** 

• 函数式接口 

–只带有一个未实现的方法，内容简单 

–大量重复性的函数式接口，使得源码膨胀 

• 系统自带的函数式接口 

*–涵盖大部分常用的功能，可以重复使用* 

*–位于java.util.function包中*



**系统自带的函数式接口(部分常用)**

1. Predicate<T>: 接收一个参数，返回一个布尔值

   ```java
   import java.util.function.Predicate;
   
   public class PredicateTest {
   
   	public static void main(String[] args) {
   		String[] planets = new String[] { 
   				"Mercury", "Venus", "Earth", "Mars", 
   				"Jupiter", "Saturn", "Uranus", "Neptune" };
   		
   		StringChecker evenLength = s -> 
   			{
   				if(s.length()%2 == 0)
   					return true;
   				return false;
   			};
   			
   		for(String p : planets)	{
   			if(evenLength.test(p)) {
   				System.out.println(p);
   			}
   		}
   		
   		
   		Predicate<String> oddLength = s -> 
   			s.length()%2 == 0 ? false:true;
   			
   		for(String p : planets)	{
   			if(oddLength.test(p)) {
   				System.out.println(p);
   			}
   		}
   	}
   }
   
   ```

   

2. Consumer<T>：接收一个参数，做操作，无返回 

   ```java
   import java.util.function.Consumer;
   import java.util.function.Function;
   import java.util.function.Supplier;
   import static java.lang.Math.*;
   
   public class ConsumerTest {
   
   	public static void main(String[] args) {
   		String[] planets = new String[] { 
   				"Mercury", "Venus", "Earth", "Mars", 
   				"Jupiter", "Saturn", "Uranus", "Neptune" };		
   		
   		
   		//Consumer 有一个accept的抽象方法
   		//接收一个参数，没有返回
   		Consumer<String> printer = s -> 
   			System.out.println("Planet :" + s);
   							
   		for(String p : planets)	{
   			printer.accept(p);
   		}			
   		
   	}
   }
   
   ```

   

3. Supplier<T>：无输入参数，返回一个数据

   ```java
   import static java.lang.Math.floor;
   import static java.lang.Math.random;
   
   import java.util.function.Consumer;
   import java.util.function.Predicate;
   import java.util.function.Supplier;
   
   public class SupplierTest {
   
   	public static void main(String[] args) {
   		String[] planets = new String[] { 
   				"Mercury", "Venus", "Earth", "Mars", 
   				"Jupiter", "Saturn", "Uranus", "Neptune" };
   
   		// Supplier 有一个get的抽象方法
   		// 无输入参数，返回一个数据
   		Supplier<String> planetFactory = () -> 
   			planets[(int) floor(random() * 8)];
   
   		for (int i = 0; i < 5; i++) {
   			System.out.println(planetFactory.get());
   		}
   	}
   }
   
   
   ```

     

4. Function<T>：接收一个参数，返回一个参数

   ```java
   import java.util.function.Consumer;
   import java.util.function.Function;
   import java.util.function.Supplier;
   import static java.lang.Math.*;
   
   public class FunctionTest {
   
   	public static void main(String[] args) {
   		String[] planets = new String[] { 
   				"Mercury", "Venus", "Earth", "Mars", 
   				"Jupiter", "Saturn", "Uranus", "Neptune" };		
   		
   		
   		//Function 有一个apply的抽象方法
   		//接收一个参数，返回一个结果
   		Function<String, String> upper = s -> 
   			{
   				//可以做更复杂的操作
   				return s.toUpperCase();
   			};
   							
   		for(String p : planets)	{
   			System.out.println(upper.apply(p));
   		}
   	}
   }
   
   
   ```

   

------

Java Lambda表达式 

• Lambda表达式 

–类似于匿名方法，一个没有名字的方法 

–可以将方法当作变量，传递给其他方法



## 方法的引用

**方法引用(1)** 

• 方法引用：Method Reference 

*–Lambda表达式支持传递现有的类库函数*

```java
import java.util.Arrays;

public class ThisInstanceMethodTest {
	public static void main(String[] args) {
		ThisInstanceMethodTest obj = new ThisInstanceMethodTest();
		obj.test();
	}	
	//start
	public void test()	{
		String[] planets = new String[] { 
				"Mercury", "Venus", "Earth", "Mars", 
				"Jupiter", "Saturn", "Uranus", "Neptune" };	
				
		Arrays.sort(planets, this::lengthCompare);
		System.out.println(Arrays.toString(planets));	
	}
	//end
	public int lengthCompare(String first, String second){
		return first.length() - second.length();
	}
}




```

**方法引用(2)** 

• 方法引用：Method Reference 

> –Class::staticMethod，如Math::abs方法 
>
> –Class::instanceMethod，如String::compareToIgnoreCase方法 
>
> –object::instanceMethod，如System.out::println方法 
>
> > • 支持this::instanceMethod调用 
> >
> > • 支持super::instanceMethod调用 
>
> –Class::new，调用某类构造函数，支持单个对象构建 
>
> –Class[]::new，调用某类构造函数，支持数组对象构建

**方法引用(3)**

• 类::静态方法(Class::staticMethod)，如 Math.abs方法 

> –等价于提供方法参数的Lambda表达式 
>
> –Math::abs 等价于 x -> Math.abs(x)

```java
public class ClassStaticMethodTest {
	public static double worker(NumFunction nf, double num)
	{
		return nf.calculate(num);
	}
	public static void main(String[] args) {
		double a = -5.3;
		double b = worker(Math::abs, a);//重点
		System.out.println(b);
		
		double c = worker(Math::floor, a);
		System.out.println(c);
	}	
}

interface NumFunction {
	double calculate(double num);
}


```

**方法引用(4)** 

• Class::instanceMethod，如String::compareToIgnoreCase方法 

> –第一个参数将变成方法的执行体 
>
> –String::compareToIgnoreCase等价于(x,y)->x.compareToIgnoreCase(y)

**方法引用(5)** 

• object::instanceMethod，如System.out.println方法 

> –等价于提供方法参数的Lambda表达式 
>
> –System.out::println等价于x->System.out.println(x)

**方法引用(6)** 

• object::instanceMethod，支持this::instanceMethod

**方法引用(7)** 

• object::instanceMethod，支持super::instanceMethod

**方法引用(8)** 

• Class::new，调用某类构造函数，创造一个对象

**方法引用(9)** 

• Class[]::new，支持数组对象创建

------

## Lambda表达式应用

**Lambda表达式应用(1)** 

• Lambda表达式应用 

> –类型信息 
>
> –重载 
>
> –变量遮蔽 
>
> –this指代 
>
> –Lambda表达式使用，以及和接口方法、嵌套类的关系

**Lambda表达式应用(2)** 

• Lambda表达式 

> –类似于匿名方法，一个没有名字的方法
>
> –被赋值后，*可以看作是一个函数式接口的实例(对象)* 
>
> –但是Lambda表达式*没有存储目标类型(target type)的信息*

**Lambda表达式应用(3)** 

• Lambda表达式 

> –重载调用，依据重载的规则和类型参数推理

**Lambda表达式应用(4)** 

• Lambda表达式变量遮蔽 

–*Lambda表达式和匿名内部类/局部内部类一样，可以捕获变量 (capture variables)，即访问外部嵌套块的变量* 

> • 但是变量要求是final或者是effectively final的 

–*Lambda表达式没有变量遮蔽问题，因为它的内容和嵌套块有着相 同的作用域* 

> • 在Lambda表达式中，不可以声明与(*外部嵌套块)局部变量同名* 的参数或者局 部变量

**Lambda表达式应用(5)**

• Lamdba的this指代 

> –表达式中的this，就是创建这个表达式的方法的this参数

**Lambda表达式应用(6)** 

• Lambda表达式 

> –填充接口的一个抽象方法 
>
> –从JDK8开始支持的接口有默认方法/静态方法/私有方法

**Lambda表达式应用(7)** 

• Lambda表达式 

> –优先级比嵌套类要高 

• 短小精干，本身可以自描述的 

• 无法创建命名实例，无法获取自身的引用(this)

**Lambda表达式应用(8)** 

• Lambda表达式 

> –方法引用比自定义Lambda表达式的优先级高 

• 系统自带的方法引用更简洁高效 

• 对于复杂的Lambda表达式，采用方法引用比内嵌Lambda表达式更清晰，更 容易维护

**Lambda表达式应用(9)** 

• Lambda表达式 

> –坚持使用标准的函数式接口 

• 更容易学习，提高互操作性

**Lambda表达式应用(10)** 

• Lambda表达式(官方文档) 

> – Use it if you are encapsulating a single unit of behavior that you want to pass to other code. 如果你需要封装一个单独的行为，并且 传递给其他的代码。 
>
> – Use it if you need a simple instance of a functional interface and none of the preceding criteria apply (for example, you do not need a constructor, a named type, fields, or additional methods).如果你 需要一个简单的函数式接口的实例，并且不需要执行任何的先决条 件(如，调用构造函数，给属性赋值，执行额外的方法)
