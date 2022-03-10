Integer类型的数据有时候可以相等，有时候不能相等。

```java
public class TestInteger {
  public static void main(String[] args) {
    Integer a=1;
    Integer b=1;
    Integer c=11100;
    Integer d=11100;
    System.out.println(a==b);
    System.out.println(c==d);
  }
}
结果：
true
false
```
自动拆箱：  将对象类型的值转换为基本类型的数据
自动装箱： 将基本数据转换为对象类型

Integer num = 10; 
//以上的声明就是用到了自动的装箱：解析为 
Integer num = new Integer(10);以上就是一个很好的体现，因为10是属于基本数据类型的，原则上它是不能直接赋值给一个对象Integer的，但jdk1.5后你就可以进行这样的声明，这就是自动装箱的魅力.

自动拆箱：故名思议就是将对象重新转化为基本数据类型： 
//装箱 
Integer num = 10; 
//拆箱 
int num1 = num;自动拆箱有个很典型的用法就是在进行运算的时候：因为对象时不恩直接进行运算的，而是要转化为基本数据类型后才能进行加减乘除.
Integer num = 10; 
//进行计算时隐含的有自动拆箱 
System.out.print(num--);

```java
//在-128~127 之外的数 
Integer num1 = 297; 
Integer num2 = 297; 
System.out.println("num1==num2: "+(num1==num2)); 
// 在-128~127 之内的数
Integer num3 = 97;
Integer num4 = 97; 
System.out.println("num3==num4: "+(num3==num4)); 
打印的结果是：num1==num2: false num3==num4: true 
```
很奇怪吧：***\*这就归结于java对于Integer与int的自动装箱与拆箱的设计，是一种模式：叫享元模式（flyweight） 
为了加大对简单数字的重利用，java定义：在自动装箱时对于值从–128到127之间的值，它们被装箱为Integer对象后，会存在内存中被重用，始终只存在一个对象\**** 
而如果超过了从–128到127之间的值，被装箱后的Integer对象并不会被重用，即相当于每次装箱时都新建一个 Integer对象（还是需要用equals来比较，已经重写）；明白了吧  
以上的现象是由于使用了自动装箱所引起的，如果你没有使用自动装箱，而是跟一般类一样，用new来进行实例化，就会每次new就都一个新的对象；