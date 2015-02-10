## 2 + 2 = 5

This is probably one of the well discussed topic, but I found it interesting.

if you run the following code-

```java
    Integer a = 1000, b = 1000;  
    System.out.println(a == b);//1
    Integer c = 100, d = 100;  
    System.out.println(c == d);//2
```

You will get -

```java
    false
    true
```

**Here is the basic:** we know that , if two references point to the same object, they are equal in terms of  ==. If two references point to different objects, they are not equal in terms of == even if they have the same contents.

So, here last statement should be `false` as well.

This actually where it gets interesting, if you look into the `Integer.java` class , you will find that there is a inner private class,` IntegerCache.java` that caches all Integer objects between **-128** and **127**.  

So thing is, all small integers are cached internally and when we declare something like -

```java
	Integer c = 100;
```

What it does internally is:

```java
	Integer i = Integer.valueOf(100);
```

Now if we look into the valueOf() method , we will see-

```java
    public static Integer valueOf(int i) {
    
      if (i >= IntegerCache.low && i
          return IntegerCache.cache[i + (-IntegerCache.low)];
    
      return new Integer(i);
    }
```

If the value in the range **-128 to 127**, it returns the instance from the cache.

So

```java
	Integer c = 100, d = 100;  
```
basically point to the same object.

Thats why we do

```java
System.out.println(c == d);
```

We get true.

Now you might ask why does this caching require?

Well, the logical rationale is that "smaller" integers in this range are used much more often than larger ones, so using the same underlying objects is worth it to reduce the potential memory footprint.

Well, everything seems alright. But I’m more like interested to abuse this thing using reflection API.

I'm sure everyone at some point gets to argue on pointless stuff like can you prove it, 
**2 +2 =5** . Well I’m too, but I want to java to do it-

```java
    public static void main(String[] args) throws NoSuchFieldException, IllegalAccessException {
    
      Class cache = Integer.class.getDeclaredClasses()[0]; //1
      Field myCache = cache.getDeclaredField("cache"); //2
      myCache.setAccessible(true);//3
     
      Integer[] newCache = (Integer[]) myCache.get(cache); //4
      newCache[132] = newCache[133]; //5
    
      int a = 2;
      int b = a + a;
      System.out.printf("%d + %d = %d", a, a, b); //
    }
```

1. Access the integer class
2. Find the the cache  
3. Make it accessible for you
4. Get  the cache array
5. Change the value of the index of 4 which is 132 ot 5
6. And now grab the popcorn :D 