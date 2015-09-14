title: 结构型设计模式 — 适配器模式
date: 2014-07-13 16:50:00.0
tags:
- 设计模式
- 适配器模式
categories:
- 设计模式

---

什么是适配器模式适配器模式就是将一个类的接适配成客户端期望的另一个接口，目的是解决接口之间的不兼容。为什么要使用适配器模式如果一个类与一个接口的行为很相似，而客户端希望该类具有该接口的行为，可以将该类当成该接口使用，比如将该类传给一个接受该接口作为参数的方法。一般情况下我们需要修改该类的代码，让该类去实现该接口，但这并不符合“对修改关闭，对扩展开放”的设计准则。此时我们可以利用适配器模式。如何实现...

<!-- more -->

#### **什么是适配器模式** ####

适配器模式就是将一个类的接适配成客户端期望的另一个接口，目的是解决接口之间的不兼容。

####     ####

#### **为什么要使用适配器模式** ####

如果一个类与一个接口的行为很相似，而客户端希望该类具有该接口的行为，可以将该类当成该接口使用，比如将该类传给一个接受该接口作为参数的方法。一般情况下我们需要修改该类的代码，让该类去实现该接口，但这并不符合“对修改关闭，对扩展开放”的设计准则。此时我们可以利用适配器模式。

####     ####

#### **如何实现适配器模式** ####

适配器模式有三种使用情况：

1.  类的适配器模式
2.  对象的适配器模式
3.  接口的适配器模式

（1）类的适配器模式

对类进行适配，使其具有另一个接口的行为。需要定义一个适配器类，继承原有的类，并实现希望适配成的接口，那么该适配器类便具有类的成员，也实现了接口的行为。举个实际的例子，手机类Cellphone具有通话call和拍照photoByPhone功能，照相机接口Camera具有拍照photo功能，而客户端希望将手机当成照相机来使用，此时我们可以使用类的适配器模式，定义CameraAdapter适配器类，继承Cellphone类，并实现Camera接口，类图如下：

![Image 1][]  


  


示例代码如下：

手机类

    package patterns.structure;
    
    /**
     * 手机类，具有通话和拍照接口
     *
     * @author Charles Chen
     *
     */
    public class Cellphone {
       public void call() {
            System. out.println("Call someone using cellphone" );
      }
    
       public void photoByPhone() {
            System. out.println("Take photos using cellphone" );
      }
    }

相机接口

    package patterns.structure;
    /**
     * 相机接口，具有拍照方法
     * @author Charles Chen
     *
     */
    public interface Camera {
       void photo();
    }

相机适配器类

    package patterns.structure;
    
    /**
     * 相机适配器，将Cellphone适配成Camera接口
     *
     * @author Charles Chen
     *
     */
    public class CameraAdapter extends Cellphone implements Camera {
    
       @Override
       public void photo() {
            // TODO Auto-generated method stub
            super.photoByPhone();
      }
    }

测试程序

    package patterns.structure;
    
    public class AdapterPatternTest {
    
       /**
       * @param args
       */
       public static void main(String[] args) {
            // TODO Auto-generated method stub
            CameraAdapter adapter = new CameraAdapter();
            takePhoto(adapter);
      }
    
       public static void takePhoto(Camera camera) {
            camera.photo();
      }
    }

程序输出

Take photos using cellphone

上面程序可以看到，通过CameraAdapter适配器，我们将Cellphone适配成Camera接口，在实现的photo方法里，实际调用的是Cellphone的photoByPhone方法。因此在客户端，我们可以将Cellphone当成camera接口，传给takePhoto方法使用。

  


（2）对象的适配器模式

对对象实例进行适配，使其具有另一个接口的行为。与类的适配器模式不同，这种模式是对已经实例化的对象进行适配。同样需要定义一个适配器类，实现客户端希望适配成的接口，但不直接继承待适配的类，而是持有该类的实例，通过调用实例的方法完成适配。还是使用上面的例子，对象适配器模式的类图如下：

![Image 1][]  


示例代码：

相机适配器类

    package patterns.structure;
    
    /**
     * 相机适配器，将Cellphone实例适配成Camera接口
     *
     * @author Charles Chen
     *
     */
    public class ObjectCameraAdapter extends Cellphone implements Camera {
       private Cellphone phone ;
    
       public ObjectCameraAdapter(Cellphone phone) {
            this.phone = phone;
      }
    
       @Override
       public void photo() {
            // TODO Auto-generated method stub
            super.photoByPhone();
      }
    }

测试程序 package patterns.structure;

    public class AdapterPatternTest {
    
       /**
       * @param args
       */
       public static void main(String[] args) {
            // TODO Auto-generated method stub
            Cellphone phone = new Cellphone();
            ObjectCameraAdapter adapter = new ObjectCameraAdapter(phone);
            takePhoto(adapter);
      }
    
       public static void takePhoto(Camera camera) {
            camera.photo();
      }
    }

  


（3）接口的适配器模式

如果一个接口具有很多抽象方法，而我们只需要使用到其中的一小部分，但是当我们去实现该接口时，又不得不实现其所有的方法，这难免会增加很多不必要的代码。此时我们可以考虑使用接口的适配器模式，将接口适配为一个抽象类，即使用一个抽象类去实现该接口所有方法（方法体可留空），当需要实现该接口时，我们不直接与接口打交道，而是去继承抽象类，选择性地实现我们感兴趣的方法。


[Image 1]: 