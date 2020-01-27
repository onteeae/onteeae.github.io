---
title: "String vs StringBuffer vs StringBuilder"
date: 2020-01-26 23:00:00 +0900
categories:
  - Java
---
### String in Java
Java의 String, Wrapper Type(Integer ...)들은 Immutable Object이다.
   * Java에서는 이를 위해 final keyword가 존재함. Immutable Object는 thread safe하고 reference로 인한 side-effect가 적다.
   * 특히 String은 여러 String Object가 같은 final char[]을 참조하도록 하여 메모리를 절약하고, 또 Caching되는 효과도 있음
```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];
```

String과 StringBuilder/StringBuffer의 차이는 바로 Immutability로 부터 온다. value[]가 final이기 때문에,  
```java
String string = "ab" + "cd" + "ef"
```  
위의 연산을 위해서는 각각의 String Literal 외에 "abcd", "abcdef"를 위한 Heap의 할당이 필요하다.  
=> 따라서 더욱 Time/Space inefficient

### StringBuffer, StringBuilder
반면에, StringBuilder/Buffer extends AbstractStringBuilder는 다음과 같이 Muttable
```java
abstract class AbstractStringBuilder implements Appendable, CharSequence {
    /**
     * The value is used for character storage.
     */
    char[] value;

    /**
     * The count is the number of characters used.
     */
    int count;
```
Immutable 하지 않기 때문에, append 연산에서 추가적인 heap allocation을 필요로 하지 않음.
* StringBuffer
```java
    @Override
    public synchronized StringBuffer append(Object obj) {
        toStringCache = null;
        super.append(String.valueOf(obj));
        return this;
    }
```
* StringBuilder
```java
    @Override
    public StringBuilder append(String str) {
        super.append(str);
        return this;
    }
```
위와 같이, StringBuffer Class는 synchronized 되어있어 thread-safe하다.  
그렇기에, 최신 Java Compiler에서는 String을 StringBuffer로 바꾸어 최적화한다고 알려져있다.  
-> 이는 별도 포스팅
### 참고
[James Gosling Interview](https://www.artima.com/intv/gosling3.html#part13)  
[String Immutable](https://www.baeldung.com/java-string-immutable)