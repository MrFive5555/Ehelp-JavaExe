# 6.30相关问题总结
# 关于final是什么
final指的是一个指针常量，类似与C语言的int* const, 不能修改它的指向，但能修改它指向的内容
如下面代码,可以修改final指向内容的[^1]
```java
public class Lambda {
	static class A {
		public int a = 1;
	}
	public static void main(String[] args) {
		final A aPtr = new A(); 
		aPtr.a = 2;
		System.out.println(aPtr.a);
	}
}
```
  
[^1]:注意到代码中A被定义成一个静态内部类。静态内部类用得很少，不用花费太多精力理解。有兴趣可以参考[这篇博客](http://blog.sina.com.cn/s/blog_605f5b4f0100zbps.html)

# 匿名类相关
## 直接实现接口参数
练习：使用匿名类把参数传入Greeting函数，完成打招呼的任务（输出“hello， world"）
```java
public class Lambda {
	interface Politer {
		void SayingHello();
	}
	public static void main(String[] args) {
		// Greeting( ... );
	}
	static void Greeting(Politer p) {
		p.SayingHello();
	}
}
```

## 为什么匿名类里面引用变量要用final
看下面一段代码，这段代码是上面的变化，打招呼的人从书上找到一句打招呼的话，打招呼。
```java
public class Lambda {
	interface Politer {
		void SayingHello();
	}
	public static void main(String[] args) {
		final Book book = new Book1();
		Greeting(new Politer() {
			public void SayingHello() {
				System.out.println(book.word);
			}
		});
	}
	static class Book {
		String word = "Hello World";
	}
	static void Greeting(Politer p) {
		p.SayingHello();
	}
}
```
现在假设不使用final，打招呼的人打完招呼后换了一本书，代码如下：
```java
Greeting(new Politer() {
  public void SayingHello() {
    System.out.println(book.word);
    Book newBook = new Book();
    newBook.word = "Ni Hao A";
    book = new Book();
  }
});
```
这时候在主函数里，book.word应该是什么呢？  
在逻辑上，因为book不是final了，book应该指向新的newBook，book.word应该等于"Ni Hao A"  
但在实现上，内部类在构造时仅仅是按值传递进原来book的指针，对指针副本的修改不会影响原指针  
因此事实上，book仍然指向旧的book，book.word仍然为"Hello World"  
为了避免这种反直觉的行为，Java的设计者强制规定：内部类里引用变量需要使用final修饰  

但是一个需要注意的是，下面代码中，对于没有final修饰的参数book，book.word这样的写法是可以的，但下面book = null这样的语句却会报错。具体原因不明。
```java
static void func(Book book) {
	Greeting(new Politer() {
		public void SayingHello() {
			System.out.println(book.word);
			book = null;
		}
	});
}
```

# 参考资料
[java为什么匿名内部类的参数引用时final？](https://www.zhihu.com/question/21395848)