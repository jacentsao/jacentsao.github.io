---
title: java-HashMap容量
date: 2019-10-31 16:02:35
tags: [java]
categories: java
---

学习HashMap初始容量和扩容策略Demo

<!--more-->

```
//根据传参的容量数字，返回一个2的整数次幂的容量大小
static final int tableSizeFor(int cap) {
        int n = cap - 1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
    }
```

```java
  public static void main(String[] args) {
        for (int i = 1 ;i < 50; i++) {
            testInitCapacity(i);
        }
        HashMap m = new HashMap();
        for (int i = 1 ;i < 50; i++) {
            testExpandCapacity(m, i);
        }
    }

    private static void testExpandCapacity(HashMap map, int i) {
        map.put(i, i);
        printParams(map);
    }

    private static void testInitCapacity(int initCapacity) {
        HashMap map = new HashMap(initCapacity);
        printParams(map);
    }

    private static void printParams(HashMap map) {
        try {
            Method method = map.getClass().getDeclaredMethod("capacity");
            Field threshold = map.getClass().getDeclaredField("threshold");
            method.setAccessible(true);
            threshold.setAccessible(true);
            Integer realCapacity = (Integer) method.invoke(map);
            Object thresholdValue = threshold.get(map);
            System.out.println("size is " + map.size() + " and capacity is " + realCapacity + " and threshold is " + thresholdValue);
        } catch (NoSuchMethodException | IllegalAccessException | InvocationTargetException e) {
            e.printStackTrace();
        } catch (NoSuchFieldException e) {
            e.printStackTrace();
        }
    }

```


```shell
size is 0 and capacity is 1 and threshold is 1
size is 0 and capacity is 2 and threshold is 2
size is 0 and capacity is 4 and threshold is 4
size is 0 and capacity is 4 and threshold is 4
size is 0 and capacity is 8 and threshold is 8
size is 0 and capacity is 8 and threshold is 8
size is 0 and capacity is 8 and threshold is 8
size is 0 and capacity is 8 and threshold is 8
size is 0 and capacity is 16 and threshold is 16
size is 0 and capacity is 16 and threshold is 16
size is 0 and capacity is 16 and threshold is 16
size is 0 and capacity is 16 and threshold is 16
size is 0 and capacity is 16 and threshold is 16
size is 0 and capacity is 16 and threshold is 16
size is 0 and capacity is 16 and threshold is 16
size is 0 and capacity is 16 and threshold is 16
size is 0 and capacity is 32 and threshold is 32
size is 0 and capacity is 32 and threshold is 32
size is 0 and capacity is 32 and threshold is 32
size is 0 and capacity is 32 and threshold is 32
size is 0 and capacity is 32 and threshold is 32
size is 0 and capacity is 32 and threshold is 32
size is 0 and capacity is 32 and threshold is 32
size is 0 and capacity is 32 and threshold is 32
size is 0 and capacity is 32 and threshold is 32
size is 0 and capacity is 32 and threshold is 32
size is 0 and capacity is 32 and threshold is 32
size is 0 and capacity is 32 and threshold is 32
size is 0 and capacity is 32 and threshold is 32
size is 0 and capacity is 32 and threshold is 32
size is 0 and capacity is 32 and threshold is 32
size is 0 and capacity is 32 and threshold is 32
size is 0 and capacity is 64 and threshold is 64
size is 0 and capacity is 64 and threshold is 64
size is 0 and capacity is 64 and threshold is 64
size is 0 and capacity is 64 and threshold is 64
size is 0 and capacity is 64 and threshold is 64
size is 0 and capacity is 64 and threshold is 64
size is 0 and capacity is 64 and threshold is 64
size is 0 and capacity is 64 and threshold is 64
size is 0 and capacity is 64 and threshold is 64
size is 0 and capacity is 64 and threshold is 64
size is 0 and capacity is 64 and threshold is 64
size is 0 and capacity is 64 and threshold is 64
size is 0 and capacity is 64 and threshold is 64
size is 0 and capacity is 64 and threshold is 64
size is 0 and capacity is 64 and threshold is 64
size is 0 and capacity is 64 and threshold is 64
size is 0 and capacity is 64 and threshold is 64
size is 1 and capacity is 16 and threshold is 12
size is 2 and capacity is 16 and threshold is 12
size is 3 and capacity is 16 and threshold is 12
size is 4 and capacity is 16 and threshold is 12
size is 5 and capacity is 16 and threshold is 12
size is 6 and capacity is 16 and threshold is 12
size is 7 and capacity is 16 and threshold is 12
size is 8 and capacity is 16 and threshold is 12
size is 9 and capacity is 16 and threshold is 12
size is 10 and capacity is 16 and threshold is 12
size is 11 and capacity is 16 and threshold is 12
size is 12 and capacity is 16 and threshold is 12
size is 13 and capacity is 32 and threshold is 24
size is 14 and capacity is 32 and threshold is 24
size is 15 and capacity is 32 and threshold is 24
size is 16 and capacity is 32 and threshold is 24
size is 17 and capacity is 32 and threshold is 24
size is 18 and capacity is 32 and threshold is 24
size is 19 and capacity is 32 and threshold is 24
size is 20 and capacity is 32 and threshold is 24
size is 21 and capacity is 32 and threshold is 24
size is 22 and capacity is 32 and threshold is 24
size is 23 and capacity is 32 and threshold is 24
size is 24 and capacity is 32 and threshold is 24
size is 25 and capacity is 64 and threshold is 48
size is 26 and capacity is 64 and threshold is 48
size is 27 and capacity is 64 and threshold is 48
size is 28 and capacity is 64 and threshold is 48
size is 29 and capacity is 64 and threshold is 48
size is 30 and capacity is 64 and threshold is 48
size is 31 and capacity is 64 and threshold is 48
size is 32 and capacity is 64 and threshold is 48
size is 33 and capacity is 64 and threshold is 48
size is 34 and capacity is 64 and threshold is 48
size is 35 and capacity is 64 and threshold is 48
size is 36 and capacity is 64 and threshold is 48
size is 37 and capacity is 64 and threshold is 48
size is 38 and capacity is 64 and threshold is 48
size is 39 and capacity is 64 and threshold is 48
size is 40 and capacity is 64 and threshold is 48
size is 41 and capacity is 64 and threshold is 48
size is 42 and capacity is 64 and threshold is 48
size is 43 and capacity is 64 and threshold is 48
size is 44 and capacity is 64 and threshold is 48
size is 45 and capacity is 64 and threshold is 48
size is 46 and capacity is 64 and threshold is 48
size is 47 and capacity is 64 and threshold is 48
size is 48 and capacity is 64 and threshold is 48
size is 49 and capacity is 128 and threshold is 96
```