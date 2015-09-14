title: 结构型设计模式 — 组合模式
date: 2014-06-24 10:59:08.0
tags:
- 设计模式
- 组合模式
categories:
- 设计模式

---

什么是组合模式组合模式将对象组合成树形结构以实现“部分整体”的层次模型，又称部分—整体模式。组合模式一般用来解决树形结构问题，从而优化分级数据结构。组合模式下一般有三个角色：接口，组合对象类，单个对象类。组合对象和单个对象实现同一个接口，组合对象中持有单个对象的集合，并提供添加、移除、访问单个对象的接口。组合模式最典型的应用案例是文件系统，文件系统由目录和普通文件构成，目录事实上也是一个文件，目录...

<!-- more -->

#### **什么是组合模式** ####

组合模式将对象组合成树形结构以实现“部分整体”的层次模型，又称部分—整体模式。组合模式一般用来解决树形结构问题，从而优化分级数据结构。

组合模式下一般有三个角色：接口，组合对象类，单个对象类。组合对象和单个对象实现同一个接口，组合对象中持有单个对象的集合，并提供添加、移除、访问单个对象的接口。

组合模式最典型的应用案例是文件系统，文件系统由目录和普通文件构成，目录事实上也是一个文件，目录由子目录或普通文件的集合组合而成。

####   ####

#### **为什么要使用组合模式** ####

使用组合模式的优势：

1.  使用组合模式使得递归或分级数据结构更加清晰
2.  组合模式下，客户端代码可以用处理单个对象的方式来处理组合对象，实现客户端调用与对象内部结构的解耦

####   ####

#### **组合模式的实现** ####

以文件系统为例，假设目录和文件都实现File接口，File接口声明了description, addFile, removeFile, listFiles四个抽象方法，分别用于描述文件信息，添加文件，移除文件，列出路径下所有文件。文本文件TextFile实现File接口，只重写description方法，目录Directory实现File接口，重写所有方法。类图如下所示，其中AbstarctFile是一个实现File接口的抽象类，作用是定义实现类的公共属性和方法，同时实现接口的适配，使得普通文件类可以选择性重写File接口的方法。

![Image 1][]

**示例程序**

File接口

    package patterns.structure.composite;
    
    public interface File {
           void description();
    
           void listFiles();
    
           void addFile(File file);
    
           void removeFile(File file);
    }

AbstractFile抽象类

    package patterns.structure.composite;
    
    import java.util.Date;
    
    public class AbstractFile implements File {
           protected String fileName ;
           protected Date creationDate ;
    
           public String getFileName() {
                 return fileName ;
          }
    
           public void setFileName(String fileName) {
                 this .fileName = fileName;
          }
    
           public Date getCreationDate() {
                 return creationDate ;
          }
    
           public void setCreationDate(Date creationDate) {
                 this .creationDate = creationDate;
          }
    
           @Override
           public void description() {
                 // TODO Auto-generated method stub
          }
    
           @Override
           public void addFile(File file) {
                 // TODO Auto-generated method stub
    
          }
    
           @Override
           public void listFiles() {
                 // TODO Auto-generated method stub
    
          }
    
           @Override
           public void removeFile(File file) {
                 // TODO Auto-generated method stub
    
          }
    
    }

普通文本类TextFile

    package patterns.structure.composite;
    
    import java.text.SimpleDateFormat;
    import java.util.Date;
    
    /**
      * 普通单个文件类
      *
      * @author Charles Chen
      *
      */
    public class TextFile extends AbstractFile {
           /**
           * Constructor
           *
           * @param fileName
           * @param creationDate
           */
           public TextFile(String fileName, Date creationDate) {
                 this .fileName = fileName;
                 this .creationDate = creationDate;
          }
    
           @Override
           public void description() {
                 // TODO Auto-generated method stub
                System. out .println("File Name: "
                            + fileName
                            + ", Creation Date: "
                            + new SimpleDateFormat("yyyy-MM-dd HH:mm:ss" )
                                        .format( creationDate ));
          }
    }

目录类Directory

    package patterns.structure.composite;
    
    import java.text.SimpleDateFormat;
    import java.util.Enumeration;
    import java.util.Vector;
    import java.util.Date;
    
    /**
      * 目录文件
      *
      * @author Charles Chen
      *
      */
    public class Directory extends AbstractFile {
           private Vector<File> files = new Vector<File>();
    
           /**
           * Constructor
           *
           * @param fileName
           * @param creationDate
           */
           public Directory(String fileName, Date creationDate) {
                 this .fileName = fileName;
                 this .creationDate = creationDate;
          }
    
           @Override
           public void description() {
                 // TODO Auto-generated method stub
                System. out .println("Directory Name: "
                            + fileName
                            + ", Creation Date: "
                            + new SimpleDateFormat("yyyy-MM-dd HH:mm:ss" )
                                        .format( creationDate ));
          }
    
           @Override
           public void listFiles() {
                 // TODO Auto-generated method stub
                Enumeration<File> fileEnum = files .elements();
                 while (fileEnum.hasMoreElements()) {
                      File file = fileEnum.nextElement();
                      file.description();
                      file.listFiles();
                }
          }
    
           @Override
           public void addFile(File file) {
                 // TODO Auto-generated method stub
                 files .add(file);
          }
    
           @Override
           public void removeFile(File file) {
                 // TODO Auto-generated method stub
                 files .remove(file);
          }
    
    }

客户端测试程序

    package patterns.structure.composite;
    
    import java.util.Date;
    
    /**
      * 客户端测试程序
      *
      * @author Charles Chen
      *
      */
    public class ClientTest {
    
           /**
           * @param args
           */
           public static void main(String[] args) {
                 // TODO Auto-generated method stub
                 // Create text files
                File textFile1 = new TextFile("text_file_1.txt" , new Date());
                textFile1.description();
                File textFile2 = new TextFile("text_file_2.txt" , new Date());
                textFile2.description();
    
                System. out
                            .println( "--------------------------------------------------------------" );
    
                 // Create directory
                File dir = new Directory("dir_1" , new Date());
                 // Add files into directory
                dir.addFile(textFile1);
                dir.addFile(textFile2);
                 // List files
                dir.description();
                dir.listFiles();
    
                System. out
                            .println( "--------------------------------------------------------------" );
    
                 // Remove text file 1
                dir.removeFile(textFile2);
                dir.listFiles();
          }
    
    }

程序输出如下，

File Name: text\_file\_1.txt, Creation Date: 2014-06-23 23:24:00  
File Name: text\_file\_2.txt, Creation Date: 2014-06-23 23:24:00  
\--------------------------------------------------------------  
Directory Name: dir\_1, Creation Date: 2014-06-23 23:24:00  
File Name: text\_file\_1.txt, Creation Date: 2014-06-23 23:24:00  
File Name: text\_file\_2.txt, Creation Date: 2014-06-23 23:24:00  
\--------------------------------------------------------------  
File Name: text\_file\_1.txt, Creation Date: 2014-06-23 23:24:00 


[Image 1]: 