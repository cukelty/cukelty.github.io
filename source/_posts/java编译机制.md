---
title: 'java编译机制 '
date: 2015-10-23 11:44:50
tags:
	-java
	-编译
	-反射
---
摘要：本文从编译机制来介绍关于java编译和反射方面的相关、
<!--more-->
正文：
##JAVA代码编译机制

Java 源码编译由以下三个过程组成：

分析和输入到符号表

注解处理

语义分析和生成class文件

![](http://hi.csdn.net/attachment/201009/25/0_1285420122jhjH.gif)


##Java反射机制

Java有个Object 类，是所有Java 类的继承根源，其内声明了数个应该在所有Java 类中被改写的方法：hashCode()、equals()、clone()、toString()、getClass()等。其中getClass()返回一个Class 对象。

Class 类十分特殊。它和一般类一样继承自Object，其实体用以表达Java程序运行时的classes和interfaces，也用来表达enum、array、primitive Java types（boolean, byte, char, short, int, long, float, double）以及关键词void。当一个class被加载，或当加载器（class loader）的defineClass()被JVM调用，JVM 便自动产生一个Class 对象。

1、定义

        Java反射机制是在“运行状态”中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制。Java反射机制主要提供了几个功能：在运行时判断任意一个对象所属的类、在运行时构造任意一个类的对象、在运行时判断任意一个类所具有的成员变量和方法、在运行时调用任意一个对象的方法。

2、获取Class对象

        当我们编译一个 Java 项目，所有的 Java 文件都会被编译成一个.class 文件，这些 class 文件在程序运行时会被 ClassLoader 加载到虚拟机中。当一个类被加载以后，Java 虚拟机就会在内存中自动产生一个 Class 对象。Java中，无论生成某个类的多少个对象，这些对象都会对应于同一个Class对象，这个Class对象是由JVM生成的，通过它能够获悉整个类的结构。我们通过 new 构造函数的形式创建对象实际上也是通过这些 Class 来创建。那么，我们在代码中如何获得Class对象呢？通常有三个方法，如下所示：
        
        
 	/** 
 	* 获取Class对象的三种方式 
 	*/  
	public static Class<?> getClassObj() {  
    // 根据类名获取Class对象  
    Class<?> clazz1 = People.class;  
  
    // 根据对象获取Class对象  
    People people = new People();  
    Class<?> clazz2 = people.getClass();  
  
    // 根据完整类名获取Class对象  
    try {  
        Class<?> clazz3 = Class.forName("com.yuyh.reflection.java.People");  
    } catch (ClassNotFoundException e) {  
        Log.e(TAG, e.toString());  
    }  
  
    Log.i(TAG, "clazz1 = " + clazz1);  
  
    return clazz1; // clazz2 clazz3  
}  


3、通过Class对象获取目标类的对象

        平时所熟悉的创建对象的方式就是去new一个类，执行他们的构造函数，那么当我们拿到Class对象想去创建目标类对象，说是通过反射，实际上还是去执行类的构造函数。如下所示：
        
        /** 
 		* 反射获取类的对象 
 		* 
 		* @return 
 		*/  
	public static Object getObject() {  
    try {  
        // 获取类的Class对象  
        Class<?> clz = getClassObj();  
        // 获取类对象的Constructor  
        Constructor<?> constructor = clz.getConstructor(String.class, int.class, 		String.class);  
        // 在使用时取消 Java语言访问检查，提升反射性能  
        constructor.setAccessible(true);  
        // 通过 Constructor 来创建对象  
        Object obj = constructor.newInstance("yuyh", 25, "xxx@gmail.com");  
        Log.i(TAG, obj.toString());  
  
        return obj;  
    } catch (Exception e) {  
        Log.e(TAG, e.toString());  
    }  
    return null;  
	}  
	
4、通过Class对象获取类的所有方法

		同样，拿到Class对象之后我们可以通过调用getDeclaredMethods或getMethods（包括从父类继承下来的方法） 去获取类的所有方法，也可调用getDeclaredMethod (String name, Class...<?> parameterTypes) 来根据方法名获取某个方法。如下所示：	
		
		
		/** 
 		* 反射获取类的方法 
		 */  
	public static void getDeclaredMethods() {  
    People people = (People) getObject();  
    // 获取到类中的所有方法(不包含从父类继承的方法)  
    Method[] methods = people.getClass().getDeclaredMethods();  
    for (int i = 0; i < methods.length; i++) {  
        Log.i(TAG, "method[" + i + "] = " + methods[i].getName());  
    }  
  
    try {  
        // 获取类中的某个方法  
        Method method = people.getClass().getDeclaredMethod("setEMail", String.class);  
        // 判断是否是public方法  
        Log.i(TAG, "method is public = " + Modifier.isProtected(method.getModifiers()));  
        // 获取该方法的参数类型列表  
        Class<?>[] paramTypes = method.getParameterTypes();  
        for (int i = 0; i < paramTypes.length; i++) {  
            Log.i(TAG, "paramTypes[" + i + "] = " + paramTypes[i].getName());  
        }  
  
        Log.i(TAG, "people.email befor= " + people.getEMail());  
  
        // 执行该方法  
        method.invoke(people, "xxx@163.com");  
  
        Log.i(TAG, "people.email after= " + people.getEMail());  
    } catch (Exception e) {  
        Log.e(TAG, e.toString());  
    }  
	}  
	
	
5、通过Class对象获取类的所有属性

        同理，可通过getDeclaredFields、 getFields、 getDeclaredField (String name)、 getField (String name) 来获取类的所有属性或单个属性。如下所示：
        
        /** 
 		* 反射获取类的属性 
 		*/  
		public static void getDeclaredFields() {  
    People people = (People) getObject();  
    // 获取当前类和父类的所有属性  
    Field[] fields = people.getClass().getDeclaredFields();  
    for (int i = 0; i < fields.length; i++) {  
        Log.i(TAG, "fields[" + i + "] = " + fields[i].getName());  
    }  
  
    try {  
        // 获取当前类的某个属性  
        Field field = people.getClass().getDeclaredField("name");  
        // 获取属性值  
        Log.i(TAG, "people.name before = " + field.get(people));  
  
        // 设置属性值  
        field.set(people, "yuyh1");  
  
        Log.i(TAG, "people.name after = " + field.get(people));  
    } catch (Exception e) {  
        e.printStackTrace();  
    }  
	} 
	
	
##JAVA注解 
	元注解：

	　　元注解的作用就是负责注解其他注解。Java5定义了4个标准的meta-annotation类型，它们被用来提供对其它 annotation类型作说明。Java5  定义的元注解：
		1.@Target,
		2.@Retention,
		3.@Documented,
		4.@Inherited
		这些类型和它们所支持的类在java.lang.annotation包中可以找到。下面我们看一下每个元注解的作用和相应分参数的使用说明。
		
		
		@Target：
		@Target说明了Annotation所修饰的对象范围：Annotation可被用于 packages、types（类、接口、枚举、Annotation类型）、类型成员（方法、构造方法、成员变量、枚举值）、方法参数和本地变量（如循环变量、catch参数）。在Annotation类型的声明中使用了target可更加明晰其修饰的目标。
		
		
		@Retention：
		@Retention定义了该Annotation被保留的时间长短：某些Annotation仅出现在源代码中，而被编译器丢弃；而另一些却被编译在class文件中；编译在class文件中的Annotation可能会被虚拟机忽略，而另一些在class被装载时将被读取（请注意并不影响class的执行，因为Annotation与class在使用上是被分离的）。使用这个meta-Annotation可以对 Annotation的“生命周期”限制。
		作用：表示需要在什么级别保存该注释信息，用于描述注解的生命周期（即：被描述的注解在什么范围内有效）
		取值（RetentionPoicy）有：
		1.SOURCE:在源文件中有效（即源文件保留）
		2.CLASS:在class文件中有效（即class保留）
		3.RUNTIME:在运行时有效（即运行时保留）
		
		
		@Inherited：
		@Inherited 元注解是一个标记注解，@Inherited阐述了某个被标注的类型是被继承的。如果一个使用了@Inherited修饰的annotation类型被用于一个class，则这个annotation将被用于该class的子类。
		注意：@Inherited annotation类型是被标注过的class的子类所继承。类并不从它所实现的接口继承annotation，方法并不从它所重载的方法继承annotation。
		当@Inherited annotation类型标注的annotation的Retention是RetentionPolicy.RUNTIME，则反射API增强了这种继承性。如果我们使用java.lang.reflect去查询一个@Inherited annotation类型的annotation时，反射代码检查将展开工作：检查class和其父类，直到发现指定的annotation类型被发现，或者到达类继承结构的顶层。
		
		
	自定义注解：
	使用@interface自定义注解时，自动继承了java.lang.annotation.Annotation接口，由编译程序自动完成其他细节。在定义注解时，不能继承其他的注解或接口。@interface用来声明一个注解，其中的每一个方法实际上是声明了一个配置参数。方法的名称就是参数的名称，返回值类型就是参数的类型（返回值类型只能是基本类型、Class、String、enum）。可以通过default来声明参数的默认值。
	
	定义注解格式：
	
	public @interface 注解名 {定义体}
	注解参数的可支持数据类型：
	1.所有基本数据类型（int,float,boolean,byte,double,char,long,short)
	2.String类型
	3.Class类型
	4.enum类型
	5.Annotation类型
	6.以上所有类型的数组
	
	Annotation类型里面的参数该怎么设定: 
	第一,只能用public或默认(default)这两个访问权修饰.例如,String value();这里把方法设为defaul默认类型；　 　
	第二,参数成员只能用基本类型byte,short,char,int,long,float,double,boolean八种基本数据类型和 String,Enum,Class,annotations等数据类型,以及这一些类型的数组.例如,String value();这里的参数成员就为String;　　
	第三,如果只有一个参数成员,最好把参数名称设为"value",后加小括号.例:下面的例子FruitName注解就只有一个参数成员。
