
### java 的Stream函数式编程

这两天学习了一下java的函数式编程，这也是java8的特性，尽管java8增加了lambda表达式，能够实现部分函数式编程的功能（函数式编程简单来说就是对于相同的输入，一定会产生相同的输出，与状态无关，其它知识请查阅相关资料），但是对于这个面向对象的语言来说，这个改动有很多妥协，当然支持的功能也有限。接下来通过介绍java的Stream库来了解java的函数式编程。

#### 1.怎么产生Stream
流（Stream）可以看成一个存储数据的仓库，我们只关心需要什么，而不用关心怎么从仓库中得到这些东西。java很多API都能将数据转为Stream，比如我们可能需要得到某个目录下的所有文件（或者目录），那么可以使用** Stream<Path> **来存储。Stream的产生更多的是通过IO流来得到的。直接调用stream()方法便可得到Stream，当然还可以直接通过调用Stream.of()实现流的构造
    
    Stream<String> words = Stream.of("first","second","third");

Stream接口还有两个静态方法用来创建流，  ** generate(Supplier<T> s) **,所以我们可以通过
    
    Stream<double> random = Stream.generate(Math:random);
来得到随机数。


#### 2.Stream怎么使用
  
Stream里面的提供了很多API来处理流，通过一层的处理最后得到自己想要的流，先上一个实例，比如我需要得到一篇英语文章中的单词的长度大于10的单词的数量，使用filter()可以过滤得到结果。

    int count = words.stream().filter(s -> s.length > 10).count()

这个操作的思路明显，而且我们只需要关注想要的结果就可以了，不用再去写for循环来一个一个查找，** s -> s.length > 10 **就是一个简单的lambda表达式，s代表输入参数，这里就是stream里面的字符串，类型可以由编译器直接推断出来（因为words的类型为String），所有不用显示的指定, 
** -> **是lambda表达式的标记，后面是具体的内容，因为只有一句话，所以没用{}括起来。意思是输出字符串的长度是否大于10，可能会有疑问？怎么看出 ** 是否 **的意思呢？看一下filter的参数类型就知道，是一个**Predicate<? super T> predicate**，意思是返回是否满足给定的Predicate条件。最后一个**count()**就是将之前满足条件的全部加起来。这个操作是不是很简单明了，实现的具体实现的部分都是交给编译器自己去完成。

当然，还有很多选择，过滤的操作，比如**findFirst(),anyMatch()**,这些都能筛选出我们想要的部分。具体的每一个API可以查文档来得到。


#### 3.怎么得到想要的结果

我们过滤完之后还是一个流，很多情况下我们**需要的是一个数字或者java的基本类型。比如前面的**count()**操作,Stream里面也有不少这样的方法，我们称之为规约的操作，比如**min(),max(),reduce(),collect()**,其中**reduce(),collect()**的功能很强大，可以得到很多自定义的结果。如果能掌握的话，java函数式编程也就掌握大半了。这里举一个简答的例子，比如我需要求总单词列表的总的字母的数量，采用**reduce()**实现，（当然还有很多简便的方法）

    Optional<Integer> sum = words.stream.reduce((x,y) -> (x.length+y.length))
这里的**Optional<Integer>**可以简单的看成一个**Integer**，只不过这个类型提供了对**null**值时的保护措施。通过**ifPresent()**方法来选择性的执行。


#### 4.一个函数式编程具体的例子：
能够实现将一个文本的单词读取出来，并且将单词流排序输出到列表当中。

    package com.seanhust.eight;
    import java.io.*;
    import java.nio.file.FileSystems;
    import java.nio.file.Path;
    import java.util.*;
    import java.util.stream.Collectors;
    import java.util.stream.Stream;

    public class Question1 {
        public static void main(String[] args){
            Path path = FileSystems.getDefault().getPath("");
            List<String> wordsList = new ArrayList<>();
            wordsList = ReadFileToWords.readFileToWords(path);
            Stream<String> longString = wordsList.stream().sorted(Comparator.comparing(String::length,Comparator.reverseOrder()));
            List<String> strings = longString.collect(Collectors.toList());
        }
    }

    class ReadFileToWords{
        public static List<String> readFileToWords(Path path){
            List<String> wordsList = new ArrayList<>();
            try(Scanner scan = new Scanner(path)){
                while(scan.hasNext()) {
                    String[] line = scan.nextLine().split("[^a-zA-Z]");
                    for (String word : line) {
                        if(!"".equals(word.trim())) {
                            wordsList.add(word.trim());
                        }
                    }
                }
            }catch (IOException e){
                e.printStackTrace();
            }
            return wordsList;
        }
    }






