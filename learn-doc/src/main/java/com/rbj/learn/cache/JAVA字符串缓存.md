# JAVA字符串类型缓存详解

```java
class StringCacheTest {
  
  public static void main(String[] args) {
  
      String s = new String("1"); //常量池中缓存1这个值
      s.intern(); //缓存一个指向堆中对象的指针到常量池
      String s2 = "1"; //常量池中已经存在1这个值，则直接使用
      System.out.println(s == s2); //指向堆中指针和常量池中1比较 返回false
  
  
      String s3 = new String("1") + new String("1"); //常量池中缓存1这个值
      s3.intern(); //缓存一个指向堆中11这个对象的指针到常量池
      String s4 = "11"; //缓存中虽然没有11这个常量，但是已经存在一个指向堆中s3的指针，所以不需要再次缓存11到常量池，直接使用指向堆中s3的指针
      System.out.println(s3 == s4); //两个相同的指针比较  返回true
  
  
      String s5 = new String("1") + new String("1"); //常量池中缓存1这个值
      String s6 = "11"; //缓存11至常量池中
      s5.intern(); //缓存一个指向堆中s5这个对象的指针到常量池
      System.out.println(s5 == s6); //指向堆的s5的指针和常量池中的11比较  返回false
  
  }
  
}
```
