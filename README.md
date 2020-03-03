# HashSet-HashMap
在本文中有自己设计的哈希表数据结构，也有利用Java语言创建的哈希集合和哈希映射。直接利用题目中现有的数据当作键，这是比较简单且容易上手，有些难题是要自己设计键，对我来说不是那么容易。题目全部来自[LeetCode](https://leetcode-cn.com/explore/learn/card/hash-table/)。<br>

## 设计哈希集合
不使用任何内建的哈希表库设计一个哈希集合，具体地说，你的设计应该包含以下的功能:<br>
add(value)：向哈希集合中插入一个值。<br>
contains(value) ：返回哈希集合中是否存在这个值。<br>
remove(value)：将给定值从哈希集合中删除。如果哈希集合中没有这个值，什么也不做。<br>

```java
MyHashSet hashSet = new MyHashSet();
hashSet.add(1);         
hashSet.add(2);         
hashSet.contains(1);    // 返回 true
hashSet.contains(3);    // 返回 false (未找到)
hashSet.add(2);          
hashSet.contains(2);    // 返回 true
hashSet.remove(2);          
hashSet.contains(2);    // 返回  false (已经被删除)
```
自己设计的哈希函数，就是简单的取模运算，利用动态数据的特性，就可以实现哈希集合数据结构的特征了。<br>
```java
package practice;

import java.util.ArrayList;
import java.util.List;

// 设计哈希集合
public class MyHashSet {
    public static void main(String[] args) {
        MyHashSet hashSet = new MyHashSet();
        hashSet.add(1);
        hashSet.add(2);
        System.out.println(hashSet.contains(1));  // true
        System.out.println(hashSet.contains(3));  // false
        hashSet.add(2);
        System.out.println(hashSet.contains(2));  // true
        hashSet.remove(2);
        System.out.println(hashSet.contains(2));  // false

    }
    List<List<Integer>> list;
    /** Initialize your data structure here. */
    public MyHashSet() {
        this.list = new ArrayList<>();
        for (int i = 0; i < 10000; i++) {
            this.list.add(null);
        }
    }

    public void add(int key) {
        int index = getIndex(key);
        List<Integer> subList = this.list.get(index);
        if(subList == null) {
            this.list.set(index, new ArrayList<>());
            this.list.get(index).add(key);
        }else {
            if(!subList.contains(key)) {
                subList.add(key);
            }
        }
    }

    public void remove(int key) {
        int index = getIndex(key);
        List<Integer> subList = this.list.get(index);
        if(subList == null) return;
        else {
            for (int i = 0; i < subList.size(); i++) {
                if(subList.get(i) == key) {
                    subList.remove(i);
                    return;
                }
            }
        }
    }

    /** Returns true if this set contains the specified element */
    public boolean contains(int key) {
        int index = getIndex(key);
        List<Integer> subList = this.list.get(index);
        if(subList == null) return false;
        else {
            if(subList.contains(key)) return true;
        }
        return false;
    }

    public int getIndex(int key) {
        return key % 10000;
    }
}
```

## 设计哈希映射
不使用任何内建的哈希表库设计一个哈希映射，具体地说，你的设计应该包含以下的功能:<br>
put(key, value)：向哈希映射中插入(键,值)的数值对。如果键对应的值已经存在，更新这个值。<br>
get(key)：返回给定的键所对应的值，如果映射中不包含这个键，返回-1。<br>
remove(key)：如果映射中存在这个键，删除这个数值对。<br>
```java
MyHashMap hashMap = new MyHashMap();
hashMap.put(1, 1);          
hashMap.put(2, 2);         
hashMap.get(1);            // 返回 1
hashMap.get(3);            // 返回 -1 (未找到)
hashMap.put(2, 1);         // 更新已有的值
hashMap.get(2);            // 返回 1 
hashMap.remove(2);         // 删除键为2的数据
hashMap.get(2);            // 返回 -1 (未找到) 
```
同样是使用的取模运算的哈希函数，自己针对哈希映射特殊的数据结构，设计了一个MyList数据结构用来存储键值对。<br>
```java
package practice;

import java.util.ArrayList;
import java.util.List;

// 设计哈希映射
public class MyHashMap {
    public static void main(String[] args) {
        MyHashMap hashMap = new MyHashMap();
        hashMap.put(1, 1);
        hashMap.put(2, 2);
        System.out.println(hashMap.get(1));  // 返回 1
        System.out.println(hashMap.get(3));  // 返回 -1 (未找到)
        hashMap.put(2, 1);  // 更新已有的值
        System.out.println(hashMap.get(2));  // 返回 1
        hashMap.remove(2);  // 删除键为2的数据
        System.out.println(hashMap.get(2));  // 返回 -1 (未找到)
    }

    List<MapList> list;

    /**
     * Initialize your data structure here.
     */
    public MyHashMap() {
        list = new ArrayList<>();
        for (int i = 0; i < 10000; i++) {
            list.add(null);
        }
    }

    /**
     * value will always be non-negative.
     */
    public void put(int key, int value) {
        int index = getIndex(key);
        if (this.list.get(index) == null) {
            this.list.set(index, new MapList(key, value));
        } else {
            MapList head = this.list.get(index);
            while (head.next != null) {
                if (head.kVal == key) {
                    head.vVal = value;
                    return;
                } else {
                    head = head.next;
                }
            }
            if (head.kVal == key) {
                head.vVal = value;
                return;
            }
            head.next = new MapList(key, value);
        }
    }

    /**
     * Returns the value to which the specified key is mapped, or -1 if this map contains no mapping for the key
     */
    public int get(int key) {
        int index = getIndex(key);
        if (this.list.get(index) == null) return -1;
        MapList head = this.list.get(index);
        while (head != null) {
            if (head.kVal == key) return head.vVal;
            head = head.next;
        }
        return -1;
    }

    /**
     * Removes the mapping of the specified value key if this map contains a mapping for the key
     */
    public void remove(int key) {
        int index = getIndex(key);
        if (this.list.get(index) == null) return;
        MapList head = this.list.get(index);
        if (head.kVal == key) {  // 如果为头节点
            this.list.set(index, head.next);
            return;
        }
        MapList pre = head;
        while (head != null) {
            if (head.kVal == key) {  // 需要移除该节点
                pre.next = head.next;
                return;
            }
            pre = head;
            head = head.next;
        }
    }

    public int getIndex(int key) {
        return key % 10000;
    }
}

class MapList {
    public int kVal;
    public int vVal;
    public MapList next;

    MapList(int kVal, int vVal) {
        this.kVal = kVal;
        this.vVal = vVal;

    }
}
```

## 两数之和
给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。<br>
```java
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```
这道题的关键在于目标值target - nums[i]作为一个键进行检索。map中存储(nums[i], i)的值，key为nums值，val为序号。使用for循环对nums迭代，用diffVal记录target - nums[i]的值，判断map中的key是否等于diffVal，如果等于，map(key)与i就是两个返回的索引。<br>
```java
package practice;

import java.util.Arrays;
import java.util.HashMap;
import java.util.Map;

// 两数之和
public class TwoSum {
    public static void main(String[] args) {
        int[] nums = {2, 7, 11, 15};
        TwoSum obj = new TwoSum();
        System.out.println(Arrays.toString(obj.twoSum(nums, 18)));
    }

    /**
     * 时间复杂度是O(n)
     * map中存储(nums[i], i)的值，key为nums值，val为序号
     * 使用for循环对nums迭代，用diffVal记录target - nums[i]的值
     * 判断map中的key是否等于diffVal，如果等于，map(key)与i就是两个返回的索引。
     * @param nums
     * @param target
     * @return
     */
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        map.put(nums[0], 0);
        int[] res = new int[2];
        for (int i = 1; i < nums.length; i++) {
            int diffVal = target - nums[i];
            if(map.containsKey(diffVal)) {
                res[0] = map.get(diffVal);
                res[1] = i;
                return res;
            }else {
                map.put(nums[i], i);
            }
        }
        return res;
    }
}

```

## 同构字符串
给定两个字符串 s 和 t，判断它们是否是同构的。如果 s 中的字符可以被替换得到 t ，那么这两个字符串是同构的。所有出现的字符都必须用另一个字符替换，同时保留字符的顺序。两个字符不能映射到同一个字符上，但字符可以映射自己本身。<br>
```java
输入: s = "egg", t = "add"
输出: true

输入: s = "foo", t = "bar"
输出: false
```
该方法建立了两个Map，分别从两个方向上确保映射的唯一性。还有一个方法，分别将s和t中每个字符按顺序映射为序号，判断序号是否一致即可。<br>
```java
package practice;

import java.util.HashMap;
import java.util.Map;

// 同构字符串
public class IsIsomorphic {
    public static void main(String[] args) {
        String s = "ab";
        String t = "ca";
        IsIsomorphic obj = new IsIsomorphic();
        System.out.println(obj.isIsomorphic(s, t));
    }

    /**
     * 该方法建立了两个Map，分别从两个方向上确保映射的唯一性。
     * 还有一个方法，分别将s和t中每个字符按顺序映射为序号，判断序号是否一致即可。
     * @param s
     * @param t
     * @return
     */
    public boolean isIsomorphic(String s, String t) {
        if(s.length() != t.length()) return false;
        char[] charS = s.toCharArray();
        char[] charT = t.toCharArray();
        Map<Character, Character> map = new HashMap<>();
        Map<Character, Character> map1 = new HashMap<>();
        for (int i = 0; i < charS.length; i++) {
            if(map.containsKey(charS[i])){
                if(map.get(charS[i]).equals(charT[i])) continue;
                else return false;
            }else{
                if(map1.containsKey(charT[i])) {
                    if(map1.get(charT[i]).equals(charS[i])) continue;
                    else return false;
                }
                map.put(charS[i], charT[i]);
                map1.put(charT[i], charS[i]);
            }
        }
        return true;
    }
}
```
## 两个列表的最小索引总和
假设Andy和Doris想在晚餐时选择一家餐厅，并且他们都有一个表示最喜爱餐厅的列表，每个餐厅的名字用字符串表示。你需要帮助他们用最少的索引和找出他们共同喜爱的餐厅。 如果答案不止一个，则输出所有答案并且不考虑顺序。 你可以假设总是存在一个答案。<br>
```java
输入:
["Shogun", "Tapioca Express", "Burger King", "KFC"]
["Piatti", "The Grill at Torrey Pines", "Hungry Hunter Steakhouse", "Shogun"]
输出: ["Shogun"]
解释: 他们唯一共同喜爱的餐厅是“Shogun”。
```
我的想法是先用一个Map存储Andy选中的餐厅和索引，然后再创建一个Map，对Doris选中的餐厅进行检索，找出两个人一样的餐厅，并且val值为索引和。这样再将Map转为集合以便进行排序，找出最大值。
```java
package practice;

import java.util.*;

// 两个列表的最小索引总和
public class FindRestaurant {
    public static void main(String[] args) {
        String[] list1 = {"Shogun", "Tapioca Express", "Burger King", "KFC"};
        String[] list2 = {"KFC", "Shogun", "Burger King"};
        FindRestaurant obj = new FindRestaurant();
        System.out.println(Arrays.toString(obj.findRestaurant(list1, list2)));
    }

    /**
     * 可以在HashMap<Index, List<String>>中存储。这样比较好。
     * @param list1
     * @param list2
     * @return
     */
    public String[] findRestaurant(String[] list1, String[] list2) {
        Map<String, Integer> map1 = new HashMap<>();  // 存储list1和index
        for (int i = 0; i < list1.length; i++) {
            map1.put(list1[i], i);  // val值存储索引
        }
        Map<String, Integer> map2 = new HashMap<>();  // 存储list1和list2相同餐馆，和索引和
        for (int i = 0; i < list2.length; i++) {
            if (map1.containsKey(list2[i])) {  // list1和list2中有相同餐馆
                map2.put(list2[i], map1.get(list2[i]) + i);
            }
        }
        List<Map.Entry<String, Integer>> list = new ArrayList<>(map2.entrySet());
        Collections.sort(list, new Comparator<Map.Entry<String, Integer>>() {
            @Override
            public int compare(Map.Entry<String, Integer> o1, Map.Entry<String, Integer> o2) {
                return o1.getValue() - o2.getValue();
            }
        });
        int head = list.get(0).getValue();
        int nums = 1;
        for (int i = 1; i < list.size(); i++) {
            if(list.get(i).getValue() != head) {
                break;
            }else {
                head = list.get(i).getValue();
                nums++;
            }
        }
        String[] res = new String[nums];
        for (int i = 0; i < nums; i++) {
            res[i] = list.get(i).getKey();
        }
        return res;
    }
}
```
## 字符串中的第一个唯一字符
给定一个字符串，找到它的第一个不重复的字符，并返回它的索引。如果不存在，则返回 -1。<br>
```java
s = "leetcode"
返回 0.

s = "loveleetcode",
返回 2.
```
使用Map的LinkedHashMap数据结构，因为它是有序存储的，遍历一遍给定的字符串，碰到相同的键加1，最后最LinkedHashMap进行遍历，找出第一个val值为1的键，返回即可。
```java
package practice;

import java.util.HashMap;
import java.util.LinkedHashMap;
import java.util.Map;
import java.util.Set;

// 字符串中第一个唯一字符
public class FirstUniqChar {
    public static void main(String[] args) {
        FirstUniqChar obj = new FirstUniqChar();
        String s = "loveleetcode";
        System.out.println(obj.firstUniqChar(s));
    }

    public int firstUniqChar(String s) {
        if (s.length() == 0 || s.trim().length() == 0) return -1;
        char[] charS = s.toCharArray();
        Map<Character, Integer> map = new LinkedHashMap<>();  // HashMap无序，LinkedHashMap有序
        for (int i = 0; i < charS.length; i++) {
            if (map.containsKey(charS[i])) {
                map.put(charS[i], map.get(charS[i]) + 1);
            } else {
                map.put(charS[i], 1);
            }
        }

        for (Map.Entry<Character, Integer> entry : map.entrySet()) {
            if(entry.getValue() == 1) {
                for (int i = 0; i < charS.length; i++) {
                    if(entry.getKey().equals(charS[i])) return i;
                }
            }
        }
        return -1;
    }
}
```
## 两个数组的交集 II
给定两个数组，编写一个函数来计算它们的交集。<br>
```java
输入: nums1 = [1,2,2,1], nums2 = [2,2]
输出: [2,2]

输入: nums1 = [4,9,5], nums2 = [9,4,9,8,4]
输出: [4,9]
```
