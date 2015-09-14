title: 创建型设计模式 — 原型模式
date: 2014-06-12 11:26:19.0
tags:
- 设计模式
- 原型模式
categories:
- 设计模式

---

什么是原型模式原型模式就是以一个对象实例为原型，复制克隆产生其他实例。实现原型模式的类需要实现Cloneable接口，该接口是一个空接口，作用是告诉JVM可以安全地调用clone方法，如果不实现Clonable接口而调用clone方法，运行时会抛出CloneNotSupportedException。 为什么要使用原型模式使用原型模式的有点有：简化对象的创建，创建一个新的实例只需要调用原...

<!-- more -->

#### 什么是原型模式 ####

原型模式就是以一个对象实例为原型，复制克隆产生其他实例。实现原型模式的类需要实现Cloneable接口，该接口是一个空接口，作用是告诉JVM可以安全地调用clone方法，如果不实现Clonable接口而调用clone方法，运行时会抛出CloneNotSupportedException。

####   ####

#### 为什么要使用原型模式 ####

使用原型模式的有点有：

1.  简化对象的创建，创建一个新的实例只需要调用原有对象的clone方法，就好比文档的复制粘贴一样简单
2.  当所要创建的对象是一个大对象时，调用clone方法比直接new一个对象在性能上要好很多，因为clone方法是一个本地方法。

####   ####

#### 原型模式的实现 ####

实现原型模式的类需要实现Cloneable接口，并将重写clone方法，将其访问权限设置为public。

Java语言中，对象的复制有浅复制和深复制之分。

**浅复制**： 将一个对象复制后，只有基本数据类型被复制，对于引用数据类型，只复制了引用，引用所指向的对象并没有被复制，指向的还是原对象。java.lang.Object的clone方法实现了浅复制。

**深复制**： 将一个对象复制后，基本数据类型和引用数据类型都会被复制。实现深复制需要通过对象的序列化与反序列化来实现。实现深复制的类必须实现Serializable接口，Serializable接口的作用同样只是告诉JVM可以安全地对该类的实例进行序列化，如果不实现该接口而直接进行序列化会抛出NotSerializableException异常

以下示例代理实现了原型模式的浅复制和深复制：

    import java.io.ByteArrayInputStream;
    import java.io.ByteArrayOutputStream;
    import java.io.IOException;
    import java.io.ObjectInputStream;
    import java.io.ObjectOutputStream;
    import java.io.Serializable;
    
    public class Prototype implements Cloneable, Serializable {
           private static final long serialVersionUID = 1L;
           public int id = 0;
           public ReferenceType rt = new ReferenceType();
    
           /**
           * Shallow clone
           *
           * @return
           */
           public Prototype clone() throws CloneNotSupportedException {
                Prototype proto = (Prototype) super.clone();
                 return proto;
          }
    
           /**
           * Deep clone
           *
           * @return
           * @throws IOException
           * @throws ClassNotFoundException
           */
           public Prototype deepClone() throws IOException, ClassNotFoundException {
                 // Serialize object
                ByteArrayOutputStream bos = new ByteArrayOutputStream();
                ObjectOutputStream oos = new ObjectOutputStream(bos);
                oos.writeObject( this);
    
                 // Deserialize object
                ByteArrayInputStream bis = new ByteArrayInputStream(bos.toByteArray());
                ObjectInputStream ois = new ObjectInputStream(bis);
                 return (Prototype) ois.readObject();
    
          }
    
           public static void main(String[] args) {
                Prototype proto = new Prototype();
                System. out.println(proto);
                System. out.println(proto.rt );
    
                 try {
                      Prototype clone1 = proto.clone();
                      System. out.println(clone1);
                      System. out.println(clone1.rt );
    
                      Prototype clone2 = proto.deepClone();
                      System. out.println(clone2);
                      System. out.println(clone2.rt );
    
                } catch (Exception e) {
                       // TODO Auto-generated catch block
                      e.printStackTrace();
                }
    
          }
    }

 