---
layout: post
title: "Apache Tuscany: Extension Point Mechanism"
date: 2012-06-08 09:34
comments: true
categories: [SOA, Architecture, CBSD]
---

Service Component Architecture (SCA)将业务功能作为一系列的服务，通过装配Assemble（或者说组合Composite）这些服务满足特定的需求，即SCA为基于SOA的应用构建提供一个编程模型。尽管理念是好的，不可回避的是其复杂性，该模型试图包含各种服务构件技术及协议等：

* Binding Type: 包含不同的绑定类型，如ATOM、HTTP、EJB、CORBA、REST、Web service等
* Databinding Type: 包含不同的数据格式，如JSON、SDO等
* Implementation Type: 包含不同的服务构件实现技术，如BPEL、Java、Python、Spring、Web等
* Interface Type: 包含不同的接口类型，如Java、WSDL、JAX-RS、JAX-WS等

Apache Tuscany是SCA规范的一个开源实现，自然也面临如何为上述愿景提供支持的问题。Tuscany将其欲支持的技术协议等实现为不同的Extensions，并定义了一个Tuscany core，该内核只提供基本的运行时和扩展机制支撑，最终通过扩展点（Extension Point）建立core和extensions之间的桥梁，允许extensions则以plugin的方式提供给内核。本文主要尝试讨论Tuscany core如何通过扩展点在运行时刻对extensions加以组织和使用以及extensions如何灵活的被core所发现并加载。

_注：本文的讨论基于Apache Tuscany 2.0版本的源码，虽然2.0版本比1.6有所改动，但基本原理仍然适用于Tuscany 1.6。_

<!-- more -->
#Tuscany Architecture
下图展示了Tuscany的体系结构:

{% img /images/ApacheTuscany/tuscany-architecture.jpg Figure 1: Tuscany Architecture %}

* Tuscany Hosting是Tuscany的运行环境，如J2SE
* Tuscany Core只提供运行时和扩展机制支撑
* SCA Extensions包含了诸多的扩展，如各类绑定、数据格式、实现技术等
* SCA API: Tuscany应用程序（图中所示的Composite Application）通过SCA API访问Tuscany服务

本文中我们主要关心Tuscany Core和SCA Extensions两个模块。

#Extension Point
Tuscany将其欲支持的技术协议等实现为不同的Extensions，对于这些扩展，通过扩展点（Extension Point）与Tuscany core发生交互与联系。

扩展点是Tuscany core与扩展之间的一组规约，定义了扩展应该遵循的规则、具备的行为，具体表现为扩展应该实现某些特定的Java接口或者继承某些base class。为满足不同的需求，Tuscany定义诸多的扩展点，下图示例性的展示了部分扩展点：

{% img /images/ApacheTuscany/Tuscany-Extension-Point-Mechanism_an-example.jpg Figure 2: Tuscany Extension Point Mechanism _ an example %}

上图中，以ContributionScannerExtensionPoint为例，Tuscany应用程序可能以Folder、Jar、Zip等不同格式存在，我们将这些不同格式的应用程序称为一个Contribution，而图中所示的三个ContributionScanner分别用于读取Folder、Jar和Zip格式的Contribution。当这些Foler、Jar、Zip格式的contribution scanner被加载以后，需要将其向ContributionScannerExtensionPoint注册。同样地，对于StAXArtifactProcessorExtensionPoint中注册的各个processor主要用于处理.composite文件中不同的XML元素。

##Extension Point Registry (EPR)
Tuscany core通过Extension Point Registry (EPR)对扩展点进行管理，EPR中保存了每个扩展点的信息。以contribution scanner为例，当需要JarContributionScanner时，通过查找EPR获取ContributionScannerExtensionPoint，再通过该扩展点查找Jar scanner，代码示例如下：

``` java look up extension point
//创建EPR（Tuscany启动过程创建）
ExtensionPointRegistry registry = new DefaultExtensionPointRegistry();
	
//查找扩展点
ContributionScannerExtensionPoint scanners= 
	registry.getExtensionPoint(ContributionScannerExtensionPoint.class);
	
//查找JarContributionScanner
ContributionScanner jarScanner = 
	scanners.getContributionScanner("application/java-archive");
	
//向扩展点注册ZipContributionScanner
ContributionScanner zipScanner = new ZipContributionScanner(...);
scanners.add(zipScanner);
```
#JAR File Specification: Service Provider 
前文说明了Tuscany运行时刻如何通过扩展点对扩展加以组织管理、引用，本章则主要关注一个扩展如何被Tuscany运行时刻发现、加载以及如何与扩展点关联。扩展只有被运行时刻发现才能发挥作用。

Tuscany定义了诸多扩展点，这些扩展点正如电脑主板上的插槽一般，任何与插槽接口相匹配的扩展都可以被Tuscany core所发现、使用。

每一个扩展必须显式的声明它所涉及的扩展点并指定与这些扩展点相应的具体实现类，二者的关系正如前文所述的ContributionScannerExtensionPoint和JarContributionScanner。Tuscany通过[JAR File Specification: Service Provider](http://docs.oracle.com/javase/1.4.2/docs/guide/jar/jar.html)达到这一目的。关于该规范此处不再赘述。具体来看，就是在扩展的META-INF/services目录下定义属性文件，属性文件以扩展点名进行命名，而文件内容则为该扩展点的具体实现类。以binding-http扩展为例，该扩展在其META-INF/services目录下有两个属性文件，文件名如下:

* org.apache.tuscany.sca.binding.http.HTTPBindingFactory
* org.apache.tuscany.sca.contribution.processor.StAXArtifactProcessor

上述两个文件的内容摘录分别如下：

	//HTTPBindingFactory
	org.apache.tuscany.sca.binding.http.impl.HTTPBindingFactoryImpl
	
	//StAXArtifactProcessor
	org.apache.tuscany.sca.binding.http.xml.HTTPBindingProcessor;qname=http://tuscany.apache.org/xmlns/sca/1.1#binding.http,model=org.apache.tuscany.sca.binding.http.HTTPBinding,factory=org.apache.tuscany.sca.binding.http.HTTPBindingFactory
	org.apache.tuscany.sca.binding.http.xml.HTTPDefaultWireFormatProcessor;qname=http://tuscany.apache.org/xmlns/sca/1.1#wireFormat.httpDefault,model=org.apache.tuscany.sca.binding.http.HTTPDefaultWireFormat
	org.apache.tuscany.sca.binding.http.xml.HTTPXMLWireFormatProcessor;qname=http://tuscany.apache.org/xmlns/sca/1.1#wireFormat.httpXml,model=org.apache.tuscany.sca.binding.http.HTTPXMLWireFormat
	org.apache.tuscany.sca.binding.http.xml.HTTPDefaultOperationSelectorProcessor;qname=http://tuscany.apache.org/xmlns/sca/1.1#operationSelector.httpDefault,model=org.apache.tuscany.sca.binding.http.HTTPDefaultOperationSelector
HTTPBindingFactory是该扩展涉及的一个扩展点，HTTPBindingFactoryImpl则为该扩展点的一个实现类；同理，StAXArtifactProcessor是该扩展涉及的另一个扩展点，HTTPBindingProcessor、HTTPDefaultWireFormatProcessor、HTTPXMLWireFormatProcessor和HTTPDefaultOperationSelectorProcessor则为其实现类。

当该扩展被加载时，Tuscany会通过Tuscany SPI读取META-INF/services目录下的属性文件，将属性文件中指定的实现类向该文件名指定的扩展点进行注册，进而可被Tuscany core进行组织管理和使用。

#Maven Dependency
Tuscany通过扩展点机制结合JAR File Service Provider的方式实现扩展，为扩展的添加和移除带来了高度的可定制性。

因为Tuscany使用Apache Maven对每个扩展进行管理，当我们也使用Maven管理自己的项目时，Tuscany扩展的灵活性、可定制性则更为明显和容易。我们只需为自己的项目添加它所需的maven dependencies，即extensions，不出现在直接或者间接dependencies中的扩展则完全不会被加载，即使处于分布式环境下的多个Tuscany结点也可以按需定制所需的扩展。



#*See also*
1. [Apache Tuscany Docs 2.x: SCA Java Runtime Overview](http://tuscany.apache.org/documentation-2x/sca-java-runtime-overview.html)
2. [Apache Tuscany Docs 2.x: Tuscany 2.x Extensibility and SPIs](http://tuscany.apache.org/documentation-2x/tuscany-2x-extensibility-and-spis.html)