title: Java编程题——在一个字符串中查找第一个非重复的字符
date: 2014-05-25 12:41:54.0
tags:
- 编程题
- 字符串
categories:
- 算法/数据结构

---

原文链接： javarevisited 翻译： ImportNew.com - chenchenchao译文链接： http://www.importnew.com/11493.html  编写一个Java程序来查找一个字符串中第一个非重复的字符，这是在编程测试中很常见的一个问题，因为字符串处理在程序员面试中是一个普遍...

<!-- more -->

原文链接： [javarevisited][] 翻译： [ImportNew.com ][ImportNew.com]\- [chenchenchao][]  
译文链接： [http://www.importnew.com/11493.html][http_www.importnew.com_11493.html]

  

编写一个Java程序来查找一个字符串中第一个非重复的字符，这是在编程测试中很常见的一个问题，因为字符串处理在程序员面试中是一个普遍的话题。面试前最好是准备好一些熟知的编程问题，例如使用递归反转字符串，或者检查一个字符串是否是回文（即正反读顺序一致）。查找第一个非重复字符的问题也是在同一个范畴。在给出解决方案之前，我们先来弄懂这个问题。我们需要编写一个函数，这个函数接受一个字符串作为参数，并返回第一个不重复的字符。例如字符串“hello”，除了“l”之外所有字符都是不重复的，但是“h”是第一个不重复的字符。同样，字符串“swiss”中“w”是第一个不重复的字符。一种解决该问题的方法就是创建一张表来记录每个字符的出现次数，然后选出第一个不重复的元素。关键在于字符的次序，你的代码必须返回第一个非重复的字符。另外，在这篇文章中，我们可以看到3个解决该问题的示例。我们的第一个方案是使用LinkedHashMap来记录字符个数，因为LinkedHashMap维持的元素顺序与插入顺序一致，而我们正是按照字符串中字符出现的顺序来将字符插入Map中的。当我们扫描字符串时，只需要迭代LinkedHashMap并找出值为1的元素。是的，这种方案只需要一个LinkedHashMap以及两个循环。我们的第二种解决方案是时间和空间的折衷，在一次遍历中找出不重复的字符。这次，我们使用一个Set和一个List去分开保存重复和不重复的字符。当我们完成一次时间复杂度为O(n)的字符串扫描后，我们可以通过访问List来获得这个神奇的不重复字符，该时间为复杂度为O(1)，因为List是有序的，我们可以通过get(0)获得第一个元素。我们的第三种解决方案也是类似的，不过这次我们使用HashMap而不是LinkedHashMap，我们会在第一扫描计算各个字符的出现次数之后，再次扫描字符串去找到第一个不重复的字符。接下来我们将为这个问题编写示例代码和单元测试程序。你也可以去我的[字符串面试问题][Link 1]列表中看看更多类似的Java编程题。 

## 如何在字符串中找到第一个不重复的字符 ##

这里有在给定字符串中找到第一个非重复字符的完整示例代码，该程序给出了三个找到第一个非重复字符的方法，每个方法都有自己的解决问题的算法。第一个算法实现在getFirstNonRepeatedChar(String str)方法中。它首先获得字符串的字符数组，然后遍历数组并建立一个哈希表，哈希表的键为字符，值为该字符出现的次数。下一步它会遍历LinkedHashMap去找到第一个值为1的元素，那便是第一个非重复的字符，因为LinkedHashMap维护的元素顺序与插入顺序一致，而我们遍历字符数组是从头遍历到尾。这种算法的缺点在于它需要两个循环，第一个循环的次数与字符串的字符个数成正比，而第二个循环的次数与字符串中重复的字符个数成正比。最差的情况是非重复的字符出现在字符串的最尾部，那么这个算法需要2\*N的时间去解决这个问题。第二个算法实现在firstNonRepeatingChar(String word)方法中。这种解决方案可以在一次字符串扫描中找到第一个不重复的字符，它应用了典型的空间时间权衡技术。它使用了两个存储空间来减少一次循环，是标准的空间-时间折衷。由于我们将重复和不重复的字符分开存放，在循环结束后，存放不重复字符的列表中的第一个元素就是我们所要找的第一个非重复字符。这种解决方案稍微优于上一种。如果在字符串中没有不重复的字符，你可以选择返回null或者空字符串。第三个算法实现在firstNonRepeatedCharacter(String word)方法中，它与第一种方案非常类似，唯一不同在于它使用了HashMap。由于HashMap并不保证一种特定的顺序，我们必须依赖源字符串去找到第一个不重复的字符。第三种解决方案的算法如下：

1.  扫描字符串并将每个字符的出现次数保存在HashMap中
2.  遍历字符串并从Map中获取每个字符的个数

由于我们从左往右扫描字符，当找到某个字符的计数为1时，我们就可以跳出循环，它就是第一个非重复的字符。在这里，字符次序是靠再次遍历源字符串来实现。

    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.HashMap;
    import java.util.HashSet;
    import java.util.LinkedHashMap;
    import java.util.List;
    import java.util.Map;
    import java.util.Map.Entry;
    import java.util.Set;
    
    public class Programming {
        /**
         * Java Program to find first duplicate, non-repeated character in a String.
         * It demonstrate three simple example to do this programming problem.
         * 
         * @author Javarevisited
         */
        public static char getFirstNonRepeatedChar(String str) {
            Map<Character, Integer> counts = new LinkedHashMap<Character, Integer>(
                    str.length());
            for (char c : str.toCharArray()) {
                counts.put(c, counts.containsKey(c) ? counts.get(c) + 1 : 1);
            }
            for (Entry<Character, Integer> entry : counts.entrySet()) {
                if (entry.getValue() == 1) {
                    return entry.getKey();
                }
            }
            throw new RuntimeException("didn't find any non repeated Character");
        }
    
        /**
         * Using HashMap to find first non-repeated character from String in Java.
         * Algorithm :
         * 
         * Step 1 : Scan String and store count of each character in HashMap
         * 
         * Step 2 : traverse String and get count for each character from Map. Since
         * we are going through String from first to last character, * when count
         * for any character is 1, we break, it's the first * non repeated
         * character. Here order is achieved by going * through String again.
         */
        public static char firstNonRepeatingChar(String word) {
            Set<Character> repeating = new HashSet<Character>();
            List<Character> nonRepeating = new ArrayList<Character>();
            for (int i = 0; i < word.length(); i++) {
                char letter = word.charAt(i);
                if (repeating.contains(letter)) {
                    continue;
                }
                if (nonRepeating.contains(letter)) {
                    nonRepeating.remove((Character) letter);
                    repeating.add(letter);
                } else {
                    nonRepeating.add(letter);
                }
            }
            return nonRepeating.get(0);
        }
    
        /**
         * Using HashMap to find first non-repeated character from String in Java.
         * Algorithm :
         * 
         * Step 1 : Scan String and store count of each character in HashMap
         * 
         * Step 2 : traverse String and get count for each character from Map. Since
         * we are going through String from first to last character, when count for
         * any character is 1, we break, it's the first non repeated character. Here
         * order is achieved by going through String again.
         * 
         * @return
         */
        public static char firstNonRepeatedCharacter(String word) {
            HashMap<Character, Integer> scoreboard = new HashMap<Character, Integer>();
            // build table [char -> count]
            for (int i = 0; i < word.length(); i++) {
                char c = word.charAt(i);
                if (scoreboard.containsKey(c)) {
                    scoreboard.put(c, scoreboard.get(c) + 1);
                } else {
                    scoreboard.put(c, 1);
                }
            }
            // since HashMap doesn't maintain order, going through string again
            for (int i = 0; i < word.length(); i++) {
                char c = word.charAt(i);
                if (scoreboard.get(c) == 1) {
                    return c;
                }
            }
            throw new RuntimeException("Undefined behaviour");
        }
    }

  


  


  


 

## 找到第一个唯一字符的JUnit单元测试 ##

这里有一些JUnit测试用例来测试程序的每个方法。我们测试不同类型的输入，一种包含重复字符而另一种不包含。由于程序并没有定义当输入空字符串，null或者只包含重复字符的字符串该如何处理，你可以自己定义一些有效的处理。

  


    import static org.junit.Assert.*;
    import org.cc.source.Programming;
    import org.junit.Test;
    
    public class ProgrammingTest {
        @Test
        public void testFirstNonRepeatedCharacter() {
            assertEquals('b', Programming.firstNonRepeatedCharacter("abcdefghija"));
            assertEquals('h', Programming.firstNonRepeatedCharacter("hello"));
            assertEquals('J', Programming.firstNonRepeatedCharacter("Java"));
            assertEquals('i', Programming.firstNonRepeatedCharacter("simplest"));
        }
    
        @Test
        public void testFirstNonRepeatingChar() {
            assertEquals('b', Programming.firstNonRepeatingChar("abcdefghija"));
            assertEquals('h', Programming.firstNonRepeatingChar("hello"));
            assertEquals('J', Programming.firstNonRepeatingChar("Java"));
            assertEquals('i', Programming.firstNonRepeatingChar("simplest"));
        }
    
        @Test
        public void testGetFirstNonRepeatedChar() {
            assertEquals('b', Programming.getFirstNonRepeatedChar("abcdefghija"));
            assertEquals('h', Programming.getFirstNonRepeatedChar("hello"));
            assertEquals('J', Programming.getFirstNonRepeatedChar("Java"));
            assertEquals('i', Programming.getFirstNonRepeatedChar("simplest"));
        }
    }

  


你可以改进一下测试用例以便测试更多的场景。没有什么比编写详细且有创造性的测试用例更能打动面试官的了，很多程序员都想不到或者没有努力去思考这些。这就是这篇文章所有要阐述的内容。我们看到了三种解决这个问题的方法，尽管他们的逻辑很类似，但不尽相同，示例程序对于初学者学习Java容器框架也是非常不错的。它让你有机会去了解不同Map的实现，并明白[HashMap与LinkedHashMap的区别][HashMap_LinkedHashMap],以便决定什么时候使用他们。另外，如果你知道解决该问题的其他方法，请分享一下。如果你在面试过程中也遇到过类似问题，你也可以分享一下你的面试经历。


[javarevisited]: http://javarevisited.blogspot.com/2014/03/3-ways-to-find-first-non-repeated-character-String-programming-problem.html
[ImportNew.com]: http://www.importnew.com/
[chenchenchao]: http://www.importnew.com/author/chenchenchao
[http_www.importnew.com_11493.html]: http://www.importnew.com/11493.html
[Link 1]: http://javarevisited.blogspot.sg/2012/10/10-java-string-interview-question-answers-top.html
[HashMap_LinkedHashMap]: http://java67.blogspot.sg/2012/08/difference-between-hashmap-and-LinkedHashMap-Java.html