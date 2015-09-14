title: 行为型设计模式 — 命令模式
date: 2014-09-06 19:14:53.0
tags:
- 设计模式
- 命令模式
categories:
- 设计模式

---

什么是命令模式命令模式在调用者与被调用者之间引入一个命令角色，即将调用者与被调用者分离开开，并将具体的调用过程独立封装。命令模式的类图如下：命令模式下有四类角色：Receiver：被调用者角色，用于接收处理调用请求Command：抽象命令角色，持有Receiver实例，声明execute接口ConcreteCommand：具体命令角色，实现execute方法，调用Receiver处理请求Invok...

<!-- more -->

#### **什么是命令模式** ####

命令模式在调用者与被调用者之间引入一个命令角色，即将调用者与被调用者分离开开，并将具体的调用过程独立封装。命令模式的类图如下：

![Image 1][]  


  


命令模式下有四类角色：

**Receiver**：被调用者角色，用于接收处理调用请求

**Command**：抽象命令角色，持有Receiver实例，声明execute接口

**ConcreteCommand**：具体命令角色，实现execute方法，调用Receiver处理请求

**Invoker**：调用者角色，持有Command实例，通过action方法调用Command的execute，从而将请求传递给Receiver执行

  


示例程序

Receiver被调用者类

    package patterns.behavior.command;
    
    /**
     * Receiver
     *
     * @author Charles Chen
     * @date 2014年9月6日
     */
    public class Receiver {
          /**
          * Execute logic
          */
          public void execute() {
               System. out.println( "Receiver received the request" );
         }
    }

  


Command命令抽象类

    package patterns.behavior.command;
    
    /**
     * Abstract command
     *
     * @author Charles Chen
     * @date 2014年9月6日
     */
    public abstract class Command {
          protected Receiver receiver;
    
          public Command(Receiver receiver) {
                // TODO Auto-generated constructor stub
                this. receiver = receiver;
         }
    
          public Receiver getReceiver() {
                return receiver;
         }
    
          public void setReceiver(Receiver receiver) {
                this. receiver = receiver;
         }
    
          public abstract void execute();
    }

  


ConcreteCommand具体命令类

    package patterns.behavior.command;
    
    /**
     * Concrete command
     *
     * @author Charles Chen
     * @date 2014年9月6日
     */
    public class ConcreteCommand extends Command {
          public ConcreteCommand(Receiver receiver) {
                // TODO Auto-generated constructor stub
                super( receiver);
         }
    
          @Override
          public void execute() {
                // TODO Auto-generated method stub
                this. receiver.execute();
         }
    
    }

  


Invoker调用者类

    package patterns.behavior.command;
    
    /**
     * Command invoker
     *
     * @author Charles Chen
     * @date 2014年9月6日
     */
    public class Invoker {
          private Command command ;
    
          public Invoker(Command command ) {
                // TODO Auto-generated constructor stub
                this. command = command;
         }
    
          public Command getCommand() {
                return command;
         }
    
          public void setCommand(Command command ) {
                this. command = command;
         }
         
          /**
          * Invoke the command
          */
          public void action() {
                command.execute();
         }
    }

  


#### **命令模式的适用场景** ####

命令模式的一般适用场景是，调用者需要在运行时根据不同的需求，执行对被调用者不同的调用逻辑。

例如，计算机的电源管理器，需要根据用户的不同设置，调用屏幕驱动程序来设置屏幕亮度。当用户选择“高节能”设置时，需要调低屏幕亮度，当用户选择“高性能”设置时，需要调低屏幕亮度。一般情况下，电源管理器需要提供两个接口来调用屏幕驱动程序，实现屏幕亮度的高低调整。如果使用命令模式，我们只需要实现两个命令类分别负责调用屏幕驱动来调高和调低亮度，电源管理器只需要调用命令接口，运行时传入具体的命令类，就可以实现亮度的切换。当需要添加新的亮度设置时，也只需要实现新的命令类，电源管理器的调用接口依旧可以复用。

命令模式也同样适用于，需要在调用时添加日志处理或需要支持撤销操作的场景。

  


#### **为什么要使用命令模式** ####

1.  命令模式实现调用者与被调用者的解耦，调用者发出请求时，并不知道接受请求的被调用者的接口，也不知道具体的调用过程。被调用者的变化不会影响到调用者，只需要调整命令角色即可
2.  命令模式有利于调用过程的扩展，如果需要添加新的调用逻辑，只需要实现新的命令类。客户端可以根据需要动态切换不同的命令类，从而执行不同的调用逻辑
3.  命令模式将调用逻辑独立封装，我们可以在调用逻辑中添加一些额外的处理逻辑，例如数据校验、日志操作等。这种模式使得调用者与被调用者可以专注于实现自身的逻辑，而不用考虑具体调用与被调用的过程
4.  命令模式支持可撤销操作，这也是完全得益于在调用者与被调用之间引入命令角色，我们可以在程序中维护一个命令调用的历史记录栈，当命令角色执行命令时，将自身进栈，命令角色同时提供undo接口，将自身出栈，并执行撤销逻辑
5.  命令模式下，调用者可以持有一个命令实例的队列，执行时依次调用队列中的命令，从而实现请求排队

####     ####

#### **如何实现命令模式** ####

我们可以使用命令模式来模拟电视机遥控控制电视机的开关、声量和亮度。在这个例子中，遥控相当于调用者角色，而电视机就是被调用者角色。

定义Television类，具有volume，light和turnOn属性，并提供changeVol()，changeLight()，和changeTurnOn()方法来修改者三个属性

定义Command抽象类，持有Television实例，声明execute和undo接口用来调用Television方法和撤销操作Command类有三个具体命令类，IncreVolCommand用来实现提高音量，IncreLightCommand用来实现提高亮度，TurnOnCommand用来实现开启电视开关

定义Monitor类，持有Command实例，声明action和undo方法实现对Television的操作和撤销操作

Monitor中维护一个命令栈，当某个具体命令执行操作时，进行进栈操作，撤销时进行出栈操作，并执行撤销逻辑

类图如下：

![Image 1][]  


示例程序：

Television类

    package patterns.behavior.command.sample;
    
    /**
     * Television POJO
     *
     * @author Charles Chen
     * @date 2014年9月6日
     */
    public class Television {
          private int volume; // Volumn value of the TV
          private int light; // Lightness value of the TV
          private boolean turnOn; // Turned on flag of the TV
    
          public Television() {
                // TODO Auto-generated constructor stub
                // Initialize TV properties
                this. volume = 10;
                this. light = 10;
                this. turnOn = false;
         }
    
          /**
          * Change volume value
          *
          * @param vol
          */
          public void changeVol( int vol) {
                if (getVolume() + vol >= 0) {
                    setVolume(getVolume() + vol);
               }
         }
          /**
          * Change light value
          * @param light
          */
          public void changeLight( int light){
                if(getLight()+ light>=0){
                    setLight(getLight()+ light);
               }
         }
         
          /**
          * Change turn on flag
          * @param turnOn
          */
          public void changeTurnOn( boolean turnOn){
               setTurnOn( turnOn);
         }
          public int getVolume() {
                return volume;
         }
    
          public void setVolume( int volume) {
                this. volume = volume;
         }
    
          public int getLight() {
                return light;
         }
    
          public void setLight( int light) {
                this. light = light;
         }
    
          public boolean isTurnOn() {
                return turnOn;
         }
    
          public void setTurnOn( boolean turnOn) {
                this. turnOn = turnOn;
         }
    
    }

  


Command抽象类

    package patterns.behavior.command.sample;
    
    import java.util.Stack;
    
    /**
     * Command abstract class
     *
     * @author Charles Chen
     * @date 2014年9月6日
     */
    public abstract class Command {
          protected Television tv;
    
          public Command(Television tv) {
                // TODO Auto-generated method stub
                this. tv = tv;
         }
    
          /**
          * Execute the command
          *
          * @param history
          */
          public abstract void execute(Stack<Command> history );
    
          /**
          * Undo the command
          *
          * @param history
          */
          public abstract void undo(Stack<Command> history);
    }

  


IncreVolCommand类，用于提升音量

    package patterns.behavior.command.sample;
    
    import java.util.Stack;
    
    /**
     * Command for increasing the volume
     *
     * @author Charles Chen
     * @date 2014年9月6日
     */
    public class IncreVolCommand extends Command {
          public IncreVolCommand(Television tv) {
                // TODO Auto-generated constructor stub
                super( tv);
         }
    
          @Override
          public void execute(Stack<Command> history) {
                // TODO Auto-generated method stub
                if ( tv != null & history != null) {
                     tv.changeVol(1);
                     // Push the current command into command histories
                     history.push( this);
               }
         }
    
          @Override
          public void undo(Stack<Command> history) {
                // TODO Auto-generated method stub
                if ( history != null && history.size() > 0
                         && history.lastElement() == this) {
                     // Pop the last executed command
                     history.pop();
                     tv.changeVol(-1);
               }
         }
    }

  


IncreLightCommand类，用于提高亮度

    package patterns.behavior.command.sample;
    
    import java.util.Stack;
    
    /**
     * Command for increasing lightness of TV
     *
     * @author Charles Chen
     * @date 2014年9月6日
     */
    public class IncreLightCommand extends Command {
          public IncreLightCommand(Television tv) {
                // TODO Auto-generated constructor stub
                super( tv);
         }
    
          @Override
          public void execute(Stack<Command> history) {
                // TODO Auto-generated method stub
                if ( tv != null & history != null) {
                     tv.changeLight(1);
                     // Push the current command into command histories
                     history.push( this);
               }
         }
    
          @Override
          public void undo(Stack<Command> history) {
                // TODO Auto-generated method stub
                if ( history != null && history.size() > 0
                         && history.lastElement() == this) {
                     // Pop the last executed command
                     history.pop();
                     tv.changeLight(-1);
               }
         }
    }

  


TurnOnCommand类，用于打开电视开关

    package patterns.behavior.command.sample;
    
    import java.util.Stack;
    
    /**
     * Command for turning on TV
     *
     * @author Charles Chen
     * @date 2014年9月6日
     */
    public class TurnOnCommand extends Command {
          public TurnOnCommand(Television tv) {
                // TODO Auto-generated constructor stub
                super( tv);
         }
    
          @Override
          public void execute(Stack<Command> history) {
                // TODO Auto-generated method stub
                if ( tv != null && history != null) {
                     tv.changeTurnOn( true);
                     // Push current command into histories
                     history.push( this);
               }
         }
    
          @Override
          public void undo(Stack<Command> history) {
                // TODO Auto-generated method stub
                if ( history != null & history.size() > 0
                         && history.lastElement() == this) {
                     // Pop last executed command
                     history.pop();
                     tv.changeTurnOn( false);
               }
         }
    }

  


Monitor遥控器类

    package patterns.behavior.command.sample;
    
    import java.util.Stack;
    
    /**
     * TV monitor POJO
     *
     * @author Charles Chen
     * @date 2014年9月6日
     */
    public class Minitor {
          private Stack<Command> history; // Command histories
          private Command cmd;
    
          public Minitor(Command cmd) {
                // TODO Auto-generated constructor stub
                history = new Stack<Command>();
                this. cmd = cmd;
         }
    
          /**
          * Execute command for TV
          */
          public void action() {
                if ( cmd != null) {
                     cmd.execute( history);
               }
         }
    
          /**
          * Undo the command
          */
          public void undo() {
                if ( cmd != null) {
                     cmd.undo( history);
               }
         }
    
          public Command getCmd() {
                return cmd;
         }
    
          public void setCmd(Command cmd) {
                this. cmd = cmd;
         }
    
    }

  


客户端测试程序

    package patterns.behavior.command.sample;
    
    /**
     * Client test for command design pattern
     *
     * @author Charles Chen
     * @date 2014年9月6日
     */
    public class ClientTest {
    
          public static void main(String[] args) {
                // TODO Auto-generated method stub
               Television tv = new Television();
               Minitor monitor = new Minitor( new TurnOnCommand( tv));
                // Turn on the TV
                monitor.action();
               System. out.println( "TV power: " + (tv .isTurnOn() ? "on" : "off" ));
    
                // Increase TV's volume
                monitor.setCmd( new IncreVolCommand( tv));
                monitor.action();
               System. out.println( "TV volume: " + tv .getVolume());
    
                // Increase TV's lightness
                monitor.setCmd( new IncreLightCommand( tv));
                monitor.action();
               System. out.println( "TV lightness: " + tv .getLight());
                // Undo increasing lightness
                monitor.undo();
               System. out.println( "TV lightness: " + tv .getLight());
         }
    
    }

  


执行结果：

TV power: on  
TV volume: 11  
TV lightness: 11  
TV lightness: 10  



[Image 1]: 