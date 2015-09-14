title: 结构型设计模式 — 外观模式
date: 2014-07-13 19:38:07.0
tags:
- 设计模式
- 外观模式
categories:
- 设计模式

---

什么是外观模式？外观模式(Fascade)用来为一组子系统提供统一的高层接口，从而简化客户端对这些子系统的访问。当一个系统由多个具有复杂关系的子系统组件构成时，客户端代码调用这些子系统时会显得比较复杂，如下图所示：而外观模式引入了一个外观角色，负责整合各个子系统的接口，客户端代码只需要与外观角色通信，子系统之间的复杂关系由外观角色来实现。如下图所示：举个实际的例子，一个办公室里不同区域都有各自的灯...

<!-- more -->

#### **什么是外观模式？** ####

外观模式(Fascade)用来为一组子系统提供统一的高层接口，从而简化客户端对这些子系统的访问。

当一个系统由多个具有复杂关系的子系统组件构成时，客户端代码调用这些子系统时会显得比较复杂，如下图所示：

![498aa136-fe76-466d-b15b-f7ccf75d6151.png][]  


而外观模式引入了一个外观角色，负责整合各个子系统的接口，客户端代码只需要与外观角色通信，子系统之间的复杂关系由外观角色来实现。如下图所示：

![Image 1][]  


举个实际的例子，一个办公室里不同区域都有各自的灯开关、空调开关、通风口开关、指示灯开关电灯，当需要关闭所有开关时，如果一个个去关会非常麻烦，而总开关充当了一个外观角色，通过总开关提供的开、关接口，就可以控制所有区域的子开关。

####     ####

#### **为什么要使用外观模式** ####

外观模式的优势：

1.  简化客户端调用。客户端只需要访问外观角色提供的统一接口，而无需关心各个子系统接口的调用次序或复杂关系
2.  实现客户端调用与子系统的解耦。当子系统接口发生变化时，只需要调整外观角色，而不需要修改客户端代码

####     ####

#### **如何实现外观模式** ####

外观模式下，我们需要定义一个外观类，并提供统一接口去整合各个子模块组件的接口，供客户端程序调用。

例如，一个数据库交互系统，由三个组件组成，组件ConneciontBuilder提供createConnection()方法用来创建数据库连接，组件SQLBuilder提供buildSQL()方法用来创建数据库查询语句，SQLExecutor提供executeSQL()方法用来执行数据查询，定义DBFascade外观角色，提供executeQuery()方法整合三个组件的接口，客户端程序可以直接调用该接口。类图如下：

![Image 1][]  


  


示例代码：

ConnectionBuilder组件

    package patterns.structure.fascade;
    
    public class ConnectionBuilder {
         /**
         * 创建连接，返回一个字符串模拟连接创建完毕
         * @return
         */
         public String createConnection() {
            return "Connection is created";
         }
    }

SQLBuilder组件

    package patterns.structure.fascade;
    
    public class SQLBuilder {
       /**
       * 创建查询语句
       * @return
       */
       public String createSQL(){
        return "SQL is builded" ;
      }
    }

SQLExecutor组件

    package patterns.structure.fascade;
    
    public class SQLExecutor {
     /**
     * 模拟执行数据查询
     * @param connection
     * @param sql
     */
     public void executeSQL(String connection, String sql){
        System.out.println(connection);
        System.out.println(sql);
        System.out.println("SQL is executed");
     }
    }

DBFascade外观角色类

    package patterns.structure.fascade;
    
    public class DBFascade {
       /**
       * 整合三个组件，模拟数据库查询
       */
       public void executeQuery(){
            ConnectionBuilder connBuilder = new ConnectionBuilder();
            String connection = connBuilder.createConnection();
            SQLBuilder sqlBuidler = new SQLBuilder();
            String sql = sqlBuidler.createSQL();
            SQLExecutor executor = new SQLExecutor();
            executor.executeSQL(connection, sql);
       }
    }

客户端测试程序

    package patterns.structure.fascade;
    
    public class ClientTest {
    
       /**
       * @param args
       */
       public static void main(String[] args) {
            // TODO Auto-generated method stub
            DBFascade fascade = new DBFascade();
            fascade.executeQuery();
      }
    
    }


[498aa136-fe76-466d-b15b-f7ccf75d6151.png]: http://www.charlestech.org/xheditor_img/20140713/498aa136-fe76-466d-b15b-f7ccf75d6151.png
[Image 1]: 