title: WSDL绑定的style和use
date: 2014-04-03 16:23:23.0
tags:
- WSDL
- webservice
categories:
- Webservice

---

在WSDL文档详解中提到，wsdl binding中，<wsdlsoap:binding>标签可以指定其style，style有两种：rpc和doucment。对于每个绑定的operation，也可以通过"use"指定其编码方式，use也有两种：encoded和literal。这里将对着两种style和use的优缺点以及组合方式进行分析。1. WSDL绑定的样式s...

<!-- more -->

在[WSDL文档详解][WSDL]中提到，wsdl binding中，<wsdlsoap:binding>标签可以指定其style，style有两种：rpc和doucment。对于每个绑定的operation，也可以通过"use"指定其编码方式，use也有两种：encoded和literal。这里将对着两种style和use的优缺点以及组合方式进行分析。

  


**1. WSDL绑定的样式style有两种取值：rpc和doucment**

rpc和document的区别在于，生成的SOAP消息的主体Body中，是否出现操作名。

（1）若使用rpc绑定样式，生成的SOAP消息的Body中，以操作名来调用对应的接口。

 *  优点：操作名出现在消息中，服务接受者可以根据操作名直接调用对应的接口
 *  缺点：以操作名标签的定义是在WSDL中，没有定义在schema中，所以不能直接验证SOAP消息的有效性

（2）若使用document绑定样式，生成的SOAP消息的Body中，不出现操作名，而是直接发送传入参数。所以在WSDL文档中，需要在types标签中用schema定义参数类型element，SOAP消息用定义的element发送传入参数  


 *  优点：操作名没有出现在消息中，服务接受者难以根据操作名直接调用对应的接口
 *  缺点：body下所有标签都定义在schema中，可以直接验证SOAP消息的有效性

  


**2. WSDL绑定operation的编码用法也有两种取值：encoded和literal**

encoded和literal的区别在于，生成的SOAP消息的主体Body中，是否出现参数的类型

（1）若使用encoded方式，生成的SOAP消息的传入参数包含类型定义

 *  优点：服务请求端可以定义传入参数的类型
 *  缺点：需要对传入参数进行编码，增加了开销

（2）若使用literal方式，生成的SOAP消息的传入参数不包含类型定义

 *  优点：不需要对传入参数进行编码，减少了开销服务
 *  缺点：请求端不可以定义传入参数的类型

  


**3. 常用的style和use组合方式**

（1）上面提到style有两种方式，use有两种方式，所以组合起来有四种。

另外还有一种样式叫wrapped包装，wrapped包装是结合了document和rpc的优缺点，wrapped样式下，WSDL文档为接口的输入参数包装了一个符合类型element，这个element的名称与该接口操作名一致，那么SOAP消息中，就可以用与该element同名的标签包装输入参数值，这种情况下，SOAP消息的Body下所有标签都是在schema中定义过的，可以直接进行有效性验证，而由于element名称与操作名相同，接受者可以直接调用到对应的服务接口。

  


（2）WSDL常用的样式组合方式有5种，以接口double add(double a, double b)为例：

 *  rpc/encoded方式

WSDL文档：

    <wsdl:message name="addRequest">
    <wsdl:part name="a" type="xsd:double"/>
    <wsdl:part name="b" type="xsd:double"/>
    </wsdl:message>
    ......
    
    <wsdl:portType name="ComputerServicePortType">
    <wsdl:operation name="add">
    <wsdl:input name="addRequest" message="tns:addRequest"/>
    <wsdl:output name="addResponse" message="tns:addResponse"/>
    </wsdl:operation>
    </wsdl:portType>
    
    <wsdl:binding name="ComputerServiceHttpBinding" type="tns:ComputerServicePortType">
    <wsdlsoap:binding style="rpc" transport="http://schemas.xmlsoap.org/soap/http"/>
    <wsdl:operation name="add">
    <wsdlsoap:operation soapAction=""/>
    <wsdl:input name="addRequest">
    <wsdlsoap:body use="encoded"/>
    </wsdl:input>
    <wsdl:output name="addResponse">
    <wsdlsoap:body use="encoded"/>
    </wsdl:output>
    </wsdl:operation>
    </wsdl:binding>

  


  


SOAP消息：

    <soap:envelope>
        <soap:body>
            <add>
                <a xsi:type="xsd:int">5</a>
                <b xsi:type="xsd:float">5.0</b>
            </add>
        </soap:body>
    </soap:envelope>

  


  


优点：

a） WSDL文档中不定义输入输出参数的类型schema，而是在message中直接指定。文档简洁，可读性好

b） SOAP消息中，发送操作名add，接受者可以直接根据操作名调用相应接口

  


缺点：

a）SOAP消息中出现传入参数类型定义，需要对类型进行编码，加大了开销

b）add标签的定义来自WSDL文档，所以不能直接对SOAP消息进行有效性的验证

  


  


 *  rpc/literal方式

WSDL文档：

同rpc/encoded方式，use改为literal

  


SOAP消息：

    <soap:envelope>
        <soap:body>
            <add>
                <a>5</a>
                <b>5.0</b>
            </add>
        </soap:body>
    </soap:envelope>

  


优点：

a） 同rpc/encoded方式，WSDL文档中不定义输入输出参数的类型schema，而是在message中直接指定。文档简洁，可读性好

b） 同rpc/encoded方式，SOAP消息中，发送操作名add，接受者可以直接根据操作名调用相应接口

c） SOAP消息中不出现传入参数类型定义，减小了开销

  


缺点：

a）同rpc/encoded方式, add标签的定义来自WSDL文档，所以不能直接对SOAP消息进行有效性的验证

  


  


 *  document/encoded方式

这种方式不被现有平台支持，所以几乎没有被使用

  


 *  document/literal方式

WSDL文档：

    <wsdl:types>
    <xsd:schema xmlns:xsd="http://www.w3.org/2001/XMLSchema" attributeFormDefault="qualified" elementFormDefault="qualified" targetNamespace="http://service.charlestech.org">
         <xsd:element name="a" type="xsd:double"/>
         <xsd:element name="b" type="xsd:double"/>
    </xsd:schema>
    </wsdl:types>
    
    <wsdl:message name="addRequest">
    <wsdl:part name="a" element="tns:a"/>
    <wsdl:part name="b" element="tns:b"/>
    </wsdl:message>
    
    ......
    
    <wsdl:portType name="ComputerServicePortType">
    <wsdl:operation name="add">
    <wsdl:input name="addRequest" message="tns:addRequest"/>
    <wsdl:output name="addResponse" message="tns:addResponse"/>
    </wsdl:operation>
    </wsdl:portType>
    
    <wsdl:binding name="ComputerServiceHttpBinding" type="tns:ComputerServicePortType">
    <wsdlsoap:binding style="document" transport="http://schemas.xmlsoap.org/soap/http"/>
    <wsdl:operation name="add">
    <wsdlsoap:operation soapAction=""/>
    <wsdl:input name="addRequest">
    <wsdlsoap:body use="literal"/>
    </wsdl:input>
    <wsdl:output name="addResponse">
    <wsdlsoap:body use="literal"/>
    </wsdl:output>
    </wsdl:operation>
    </wsdl:binding>

  


  


SOAP消息：

    <soap:envelope>
        <soap:body>
                <a>5</b>
                <a>5.0</b>
        </soap:body>
    </soap:envelope>

  


优点：

a） SOAP消息中所有标签都在schema中定义，可以直接验证SOAP消息的有效性

b） SOAP消息中不出现传入参数类型定义，减小了开销

  


缺点：

a）WSDL文档中有类型的定义，文档结构较为复杂

a）SOAP消息中没有出现操作名，服务接受者很难将请求绑定到具体的接口调用上

  


  


  


 *  document/literal wrapped方式

WSDL文档：

    <wsdl:types>
    <xsd:schema xmlns:xsd="http://www.w3.org/2001/XMLSchema" attributeFormDefault="qualified" elementFormDefault="qualified" targetNamespace="http://service.charlestech.org">
         <xsd:element name="add">
         <xsd:complexType>
         <xsd:sequence>
         <xsd:element name="a" type="xsd:double"/>
         <xsd:element name="b" type="xsd:double"/>
         </xsd:sequence>
         </xsd:complexType>
         </xsd:element>
    </xsd:schema>
    </wsdl:types>
    
    <wsdl:message name="addRequest">
    <wsdl:part name="a" element="tns:a"/>
    <wsdl:part name="b" element="tns:b"/>
    </wsdl:message>
    
    ......
    
    <wsdl:portType name="ComputerServicePortType">
    <wsdl:operation name="add">
    <wsdl:input name="addRequest" message="tns:addRequest"/>
    <wsdl:output name="addResponse" message="tns:addResponse"/>
    </wsdl:operation>
    </wsdl:portType>
    
    <wsdl:binding name="ComputerServiceHttpBinding" type="tns:ComputerServicePortType">
    <wsdlsoap:binding style="document" transport="http://schemas.xmlsoap.org/soap/http"/>
    <wsdl:operation name="add">
    <wsdlsoap:operation soapAction=""/>
    <wsdl:input name="addRequest">
    <wsdlsoap:body use="literal"/>
    </wsdl:input>
    <wsdl:output name="addResponse">
    <wsdlsoap:body use="literal"/>
    </wsdl:output>
    </wsdl:operation>
    </wsdl:binding>

  


  


SOAP消息：

    <soap:envelope>
        <soap:body>
              <add>
                <a>5</b>
                <a>5.0</b>
              </add>
        </soap:body>
    </soap:envelope>

  


  


这里的SOAP消息看起来很像rpc/literal，应该说是几乎一样。但是，在rpc/literal组合方式下，add标签代表的是操作，而document/literal wrapped方式下，add标签代表的是WSDL文档中types标签下定义的复合类型。document/literal wrapped方式对参数类型进行了包装，且规定包装后的组合类型element名称要与操作名相同。

  


所以，document/literal wrapped方式综合了rpc和document的优点，既能够直接对SOAP消息进行有效性验证，又能让服务接受者轻松找到对应接口进行调用，这种方式也使用得最广泛。


[WSDL]: http://www.charlestech.org/view_article.shtml?uid=SXQZCADREW58