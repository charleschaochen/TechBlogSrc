title: WSDL文档详解
date: 2014-01-31 11:40:28.0
tags:
- Webservice
- WSDL
categories:
- Webservice

---

1. 什么是WSDL      WSDL(Web Service Description Language)，Web服务描述语言。WSDL是基于XML的web接口描述语言，用来描述Web服务和如何与Web服务进行通信，相当于一个接口说明文件。  2. WSDL文档结构      ...

<!-- more -->

### 1. 什么是WSDL ###

      WSDL(Web Service Description Language)，Web服务描述语言。WSDL是基于XML的web接口描述语言，用来描述Web服务和如何与Web服务进行通信，相当于一个接口说明文件。

 

 

### **2. WSDL文档结构** ###

      一个WSDL文档的主要结构如下，

    <definitions>
    
    <types>
       definition of types........
    </types>
    
    <message>
       definition of a message....
    </message>
    
    <portType>
       definition of a port.......
    </portType>
    
    <binding>
       definition of a binding....
    </binding>
    
    </definitions>

WSDL 文档可包含其它的元素，比如 extension 元素，以及一个 service 元素，此元素可把若干个 web services 的定义组合在一个单一的 WSDL 文档中。

 

### 3. WSDL文档主要元素 ###

（1） <wsdl:types>用来定义各个接口的输入和输出参数的数据类型。

 *  types的定义使用的是XML Schema定义的方式。一个类型的说明由一个<xsd:element>来组织。关于XML Shema Definition的教程可以参考[http://www.w3school.com.cn/schema/index.asp][http_www.w3school.com.cn_schema_index.asp]
 *  一个接口一般需要在types中定义两个复合类型，一个是输入参数的复合类型，一个是输出参数的复合类型，
 *  示例：接口方法double add(int a, int b)，数据类型定义如下，

    <xsd:element name="add">
    	<xsd:complexType>
    		<xsd:sequence>
    			<xsd:element maxOccurs="1" minOccurs="1" name="in0" type="xsd:double"/>
    			<xsd:element maxOccurs="1" minOccurs="1" name="in1" type="xsd:double"/>
    		</xsd:sequence>
    	</xsd:complexType>
    </xsd:element>
    <xsd:element name="addResponse">
    	<xsd:complexType>
    		<xsd:sequence>
    			<xsd:element maxOccurs="1" minOccurs="1" name="out" type="xsd:double"/>
    		</xsd:sequence>
    	</xsd:complexType>
    </xsd:element>

复合类型"add"中定义了输入参数的两个类型，复合类型"addResponse"定义了输出参数的类型。其中，<xsd:sequence>元素是用来指定类型元素的顺序必须和声明的一致。

 

  


（2） <wsdl:message>用来定义各个接口的消息部件，

 *  通常一个接口有两个消息部件，一个是request，代表请求消息，一个是response，代表相应消息。例如sayHello这个接口，一般需要为其定义sayHelloRequest作为输入消息，sayHelloResponse作为输出消息。
 *  消息的数据结构是使用types中定义的类型元素。
 *  示例：接口方法double add(int a, int b)，消息定义如下，

    <wsdl:message name="addRequest">
    	<wsdl:part name="parameters" element="tns:add"/>
    </wsdl:message>
    <wsdl:message name="addResponse">
    	<wsdl:part name="parameters" element="tns:addResponse"/>
    </wsdl:message>

  


  


（3）<wsdl:portType>是最重要的元素，用来描述当前webservice可被调用的操作(operation)集合及其相关信息，相当于该webservice所有接口的函数库。

 *  portType由若干个<wsdl:operation>组成，每个operation代表一个可被调用接口的操作描述，定义了访问接口的请求/相应消息对。请求消息由<wsdl:input name="\*" message="\*"/>来组织，相应消息由<wsdl:output name="\*" message="\*"/>来组织。
 *  示例：webservice提供double add(int a, int b)和double minus(int a, int b)两个接口，portType定义如下，

    <wsdl:portType name="ComputerServicePortType">
    	<wsdl:operation name="minus">
    		<wsdl:input name="minusRequest" message="tns:minusRequest"/>
    		<wsdl:output name="minusResponse" message="tns:minusResponse"/>
    	</wsdl:operation>
    	<wsdl:operation name="add">
    		<wsdl:input name="addRequest" message="tns:addRequest"/>
    		<wsdl:output name="addResponse" message="tns:addResponse"/>
    	</wsdl:operation>
    </wsdl:portType>

  


  


（4） <wsdl:binding>描述web服务与具体协议之间的绑定。通过binding可将portType绑定到具体的协议上。

 *  WSDL是基于XML的，具有平台无关性，支持多种协议。可绑定的协议有SOAP, HTTP, SMTP, FTP等等，**一般情况下会使用SOAP作为绑定协议**
 *  <wsdl:binding>由一个全局<wsdlsoap:binding>绑定和若干个<wsdl:operation>具体绑定组成
 *  <wsdl:binding>可指定style属性，用来规定请求和相应的消息格式，style有两种取值：**rpc**和**document**

**如果全局的<wsdlsoap:binding>指定了style，那么该绑定下所有的operation都是默认使用这种style，如果operation也有指定的style，该operation就使用自己指定的style。如果都没有指定，默认为document。**

  


 *  <wsdl:operation>由一个<wsdlsoap:operation soapAction="\*">和<wsdl:input>, <wsdl:output>组成，<wsdl:input>和<wsdl:output>中的<wsdlsoap:body use="\*">描述请求和相应消息的编码，有两种方式：encoded和literal

 

（5） <wsdl:service>描述所有对外提供服务的portType集合以及其具体的协议binding和service地址。

 *  <wsdl:service>由若干个<wsdl:port>组成，每个port标签需要指定portType的name和binding
 *  每个<wsdl:port>下通过<wsdlsoap:address location="\*">指定服务的URL
 *  示例：webservice提供SayHelloPortType和SayGoodbyePortType两个operation集合，service定义如下，

    <wsdl:service>
         <wsdl:port name="SayHelloPortType" binding="SayHelloHttpBinding">
              <wsdlsoap:address location="http://locahost:8080/service/SayHello" />
         </wsdl:port>
         <wsdl:port name="SayGoodbyePortType" binding="SayGoodbyeHttpBinding">
              <wsdlsoap:address location="http://locahost:8080/service/SayGoodbye" />
         </wsdl:port>
    </wsdl:service>

  
 

 


[http_www.w3school.com.cn_schema_index.asp]: http://www.w3school.com.cn/schema/index.asp