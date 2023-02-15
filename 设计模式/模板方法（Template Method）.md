## 意图：
定义一个操作中的算法骨架，而将一些步骤延迟到子类中。Template Method使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。
## 结构：
![[Pasted image 20221019000954.png]]
* AbstractClass（抽象类）定义抽象的原语操作，具体的子类将重定义他们以实现一个算法的各个步骤；实现模板方法，定一个算法的骨架，该模板方法不经调用原语操作，也调用定义在AbstractClass或其他对象中的操作
* ConcreteClass（具体类）实现原语操作以完成算法中与特定子类相关的步骤

## 适用于：
* 一次性实现一个算法的不变部分，并将可变的行为留给子类来实现。
* 各个类中公共的行为应被提取出来并集中到一个公共父类中，以避免代码重复
* 控制子类扩展。模板方法旨在特定点调用”hook“操作（默认的行为，子类可以在必要时进行重定义扩展），这就只允许在这些点进行扩展

## Example
制作豆浆程序
-   制作豆浆的流程：选材—>添加配料—>浸泡—>放到豆浆机打碎 
-   通过添加不同的配料，可以制作出不同口味的豆浆
-   选材、浸泡和放到豆浆机打碎这几个步骤对于制作每种口味的豆浆都是一样的

```java

// 抽象类，表示豆浆	SoyaMilk.java
public abstract class SoyaMilk {
	// 模板方法：可以做成final，不让子类去覆盖
	final void make() {
		select();
		addCondiment();
		soak();
		beat();
	}
	
	//选材料
	void select() { System.out.println("第一步：选择新鲜的豆子"); }
	//添加不同的配料：抽象方法，由子类具体实现
	abstract void addCondiment();
	//浸泡
	void soak() { System.out.println("第三步：豆子和配料开始浸泡3H"); }
	//榨汁
	void beat() { System.out.println("第四步：豆子和配料放入豆浆机榨汁"); }
}

// RedBeanSoyaMilk.java
public class ReadBeanSoyaMilk extends SoyaMilk {
	@Override
	void addCondiment() {
		System.out.println("第二步：加入上好的红豆");
	}
}

// PeanutSoyMilk.java
public class PeanutSoyaMilk extends SoyaMilk {
	@Override
	void addCondiment() {
		System.out.println("第二步：加入上好的花生");
	}
}

// Client.java
public class Client {
	public static void main(String[] args) {
		System.out.println("=======制作红豆豆浆=======");
		SoyaMilk redBeanSoyaMilk = new ReadBeanSoyaMilk();
		redBeanSoyaMilk.make();
		
		System.out.println("=======制作花生豆浆=======");
		SoyaMilk peanutSoyaMilk = new PeanutSoyaMilk();
		peanutSoyaMilk.make();
	}
}

```

**”holk“操作（钩子方法）**
在模板方法模式的父类中，可以<u>定义一个方法，它默认不做任何事，子类可以视情况要不要覆盖它，该方法称为“钩子”</u>。
使用钩子方法对前面的模板方法进行改造。
```java
// RedBeanSoyaMilk.java/PeanutSoyaMilk.java同上，略

//抽象类，表示豆浆，SoyaMilk
public abstract class SoyaMilk {

	//模板方法：可以做成final，不让子类去覆盖
	final void make() {
		select();
		if(customerWantCondiment()) {
			addCondiment();
		}	
		soak();
		beat();
	}

	//1.选材料
	void select() { System.out.println("第一步：选择新鲜的豆子"); }
	//2.添加不同的配料：抽象方法，由子类具体实现
	abstract void addCondiment();
	//3.浸泡
	void soak() { System.out.println("第三步：豆子和配料开始浸泡3H"); }
	//4.榨汁
	void beat() { System.out.println("第四步：豆子和配料放入豆浆机榨汁"); }

	//钩子方法：决定是否需要添加配料
	boolean customerWantCondiment() {
		return true;//默认情况下是要加配料的
	}
}

// PureSoyaMilk.java
public class PureSoyaMilk extends SoyaMilk {
	@Override
	void addCondiment() {
		// 添加配料的方法 空实现 即可
	}
	@Override
	boolean customerWantCondiment() {
		return false;
	}
}

// Client.java
public class Client {
	public static void main(String[] args) {
		System.out.println("=制作纯豆浆=");
		SoyaMilk pureSoyMilk = new PureSoyaMilk();
		pureSoyMilk.make();
	}
}

```