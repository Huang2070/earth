* null是Java中的关键字，像public、static、final。它是大小写敏感的，你不能将null写成Null或NULL，编译器将不能识别它们然后报错。
* 就像每种原始类型都有默认值一样，如int默认值为0，boolean的默认值为false，null是任何引用类型的默认值，不严格的说是所有object类型的默认值。如成员变量、局部变量、实例变量、静态变量.（但当你使用一个没有初始化的局部变量，编译器会警告你）
* null既不是对象也不是一种类型，它仅是一种特殊的值，你可以将其赋予任何引用类型，你也可以将null转化成任何类型. 
```Java
String myStr = (String) null; // null can be type cast to String
Integer myItr = (Integer) null; // it can also be type casted to Integer
Double myDbl = (Double) null; // yes it's possible, no error

你可以看到在编译和运行时期，将null强制转换成任何引用类型都是可行的，在运行时期都不会抛出空指针异常。
```
* null可以赋值给引用变量，你不能将null赋给基本类型变量，例如int、double、float、boolean。
```Java
int i = null; //编译不通过
Integer itr = null; // this is ok
int j = itr; // this is also ok, but NullPointerException at runtime

正如你看到的那样，当你直接将null赋值给基本类型，会出现编译错误。但是如果将null赋值给包装类object，然后将object赋给各自的基本类型，编译器不会报，但是你将会在运行时期遇到空指针异常。这是Java中的自动拆箱导致的.
```
* 任何含有null值的包装类在Java拆箱生成基本数据类型时候都会抛出一个空指针异常。一些程序员犯这样的错误，他们认为自动装箱会将null转换成各自基本类型的默认值，例如对于int转换成0，布尔类型转换成false，但是那是不正确的.
* 如果使用了带有null值的引用类型变量，instanceof操作将会返回false
```Java
Integer iAmNull = null;
if(iAmNull instanceof Integer) {

} else {
    //这里结果为false.
}
```
* 你可能知道不能调用非静态方法来使用一个值为null的引用类型变量。它将会抛出空指针异常，但是你可能不知道，你可以使用静态方法来使用一个值为null的引用类型变量。因为静态方法使用`静态绑定`，不会抛出空指针异常。
```Java
public class Testing {            
   public static void main(String args[]){
      Testing myObject = null;
      myObject.iAmStaticMethod();
      myObject.iAmNonStaticMethod();                            
   }

   private static void iAmStaticMethod(){
        System.out.println("I am static method, can be called by null reference");
   }

   private void iAmNonStaticMethod(){
       System.out.println("I am NON static method, don't date to call me by null");
   }
}

输出: 
I am static method, can be called by null reference

Exception in thread "main" java.lang.NullPointerException
               at Testing.main(Testing.java:11)
```

* 可以使用==或者!=操作来比较null值，但是不能使用其他算法或者逻辑操作，例如小于或者大于。跟SQL不一样，在Java中null==null将返回true.