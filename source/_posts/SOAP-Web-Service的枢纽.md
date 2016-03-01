---
title: SOAP，WebService的枢纽
date: 2015-01-12 00:02:38
tags:
- Nodejs
- Javascript
- SOAP
category: 搬砖码农
---

## 前言

前两天，室友给我发了一套代码......

![](/image/blog/79702-e7cf075365536286.jpeg)
![](/image/blog/79702-e7cf075365536286.jpeg)

一段对话下来，我心中多个疑问：SOAP是什么？node调用java接口？

一向懒散的我，不知为何却对些问题有点兴趣，平常我们处理不同应用程序之间的通讯，都是以RESTful API的方式的，基于HTTP协议，互相传输JSON格式的数据以达到程序间的交流。现在却有一个称SOAP的东东，可以直接调用程序的接口？

## SOAP，何方妖孽？

*   SOAP（Simple Object Access Protocol 简单对象访问协议）
*   SOAP 是基于 XML 的简易协议，可使应用程序在 HTTP 之上进行信息交换。
*   SOAP 独立于平台、语言，且简单和可扩展

## SOAP 怎么来的？

此标准由IBM、Microsoft、UserLand和DevelopMentor在1998年共同提出，并得到IBM，莲花（Lotus），康柏（Compaq）等公司的支持，于2000年提交给万维网联盟（World Wide Web Consortium；W3C），目前SOAP 1.1版是业界共同的标准，属于第二代的XML协定（第一代具主要代表性的技术为XML-RPC以及WDDX）。

## SOAP 的 好搭档：WSDL、Web Service

为了能了解SOAP的做的事，你首先需要了解和SOAP一同工作的两个同事。

**Web Service** 是一套应用程序的组件，也可以说是对外接口，它使用SOAP这一套标准，提供公共服务给别的程序调用。

**WSDL** 是基于XML语言的，用来描述web service的消息格式和协议细节，以及如何访问它们。它的全称是：Web Service Description Language web服务描述语言。

## SOAP是怎么工作的？

在了解了SOAP、WSDL、Web Service的定义之后，我们就来窥探一下它们三个是怎么P的？

**假设**现在有两个应用程序，分别用两种语言进行开发的，一个是用nodejs开发的程序，一个是用java开发的程序。而java程序其中有一个方法(getUser)可以查询数据库中的某用户的信息。那么在nodejs程序请求java程序的(getUser)方法获取某用户信息的流程是怎么走的？

我在应用程序的层面上解释一下这个流程，

请看下图，然后我再对每一个步骤说明：

![](/image/blog/79702-5d2b1c9e8970c3ea.jpeg)

首先java程序会把`getUser()`做成Web Service，然后要提供对这个service的描述文档，那便是WSDL文件。nodejs程序要调用的方法以及参数的信息都存放在遵循了SOAP标准的WSDL文件中，所以要访问远端的web service，必须先获得对方提供的WSDL。

步骤一：nodejs程序通过http请求，获取到java程序提供的WSDL文件。nodejs程序得到这个WSDL文件之后，就会对它进行解析，得知java程序提供了`getUser()`，而且也知道这个方法需要的参数是`string`类型的且变量名`userUUID`。

步骤二：在nodejs程序知道java程序有`getUser()`这个方法之后，它通过http请求，发送SOAP信息到java程序，java程序得到这个SOAP信息之后，进行解析，知道了nodejs程序需要请求`getUser()`方法，且提供了所需要的参数。

步骤三：java程序对nodejs的请求进行逻辑处理，查询数据库，然后把结果翻译成SOAP信息，再通过http协议发送到nodejs程序。

本例中，nodejs程序和java程序沟通的SOAP实例：

SOAP 请求：

    POST /InStock HTTP/1.1
    Host: www.example.org
    Content-Type: application/soap+xml; charset=utf-8
    Content-Length: nnn

    <?xml version="1.0"?>
    <soap:Envelope
    xmlns:soap="http://www.w3.org/2001/12/soap-envelope"
    soap:encodingStyle="http://www.w3.org/2001/12/soap-encoding">

      <soap:Body xmlns:m="http://www.example.org/stock">
        <m:getUser>
          <m:userUUID>123456789</m:userUUID>
        </m:getUser>
      </soap:Body>

    </soap:Envelope>`</pre>

    SOAP 响应：

    <pre>`HTTP/1.1 200 OK
    Content-Type: application/soap+xml; charset=utf-8
    Content-Length: nnn

    <?xml version="1.0"?>
    <soap:Envelope
    xmlns:soap="http://www.w3.org/2001/12/soap-envelope"
    soap:encodingStyle="http://www.w3.org/2001/12/soap-encoding">

      <soap:Body xmlns:m="http://www.example.org/stock">
        <m:getUserResponse>
          <m:userName>JerryC</m:userName>
        </m:getUserResponse>
      </soap:Body>

    </soap:Envelope>

综上，程序之间的调用，无论进行请求还是返回结果，每一次都需要构造一则SOAP信息，固然SOAP便是程序之间沟通的语言。

## node开发中的soap

我是一名nodejs开发者，知道有这么一种程序之间交流信息的协议之后，很自然就想知道用nodejs怎么使用这个协议。

nodejs针对SOAP也有不少的第三方模块：

*   [node-soap](https://github.com/milewise/node-soap)
*   [douche](https://github.com/kr1sp1n/douche)
*   [soapjs](https://github.com/jmoyers/soapjs)
....
[更多nodejs的SOAP第三方模块](https://nodejsmodules.org/tags/soap)

其中，node-soap是最受欢迎的(根据github上star数量)，也是我最喜欢的。

##### 本文写作参考：

[http://www.w3school.com.cn/soap/index.asp](http://www.w3school.com.cn/soap/index.asp)
[http://zh.wikipedia.org/wiki/SOAP](http://zh.wikipedia.org/wiki/SOAP)
