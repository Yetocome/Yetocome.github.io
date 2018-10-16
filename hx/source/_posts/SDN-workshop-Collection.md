title: SDN workshop Collection - Last Update on Feb 25
date: 2017-02-22 23:32:23
categories: Others
tags: [SDN, workshop, paper, network]
---

最近整理了SDN相关的会议以及它们的Topics of Interest，希望能够从中看出SDN发展的一些趋势。持续更新...

<!--more-->

研讨会：

HotSDN(SIGCOMM)/SOSR(SIGCOMM, ONS), CoolSDN(ICNP), SDNFLEX(NetSys), EWSDN, ManSDN/NFV(CNSM)

发表有SDN相关论文会议：

* A类: SIGCOMM, INFOCOM
* B类: CoNEXT, ICNP, IMC, NSDI
* C类: HotNets, LCN, ANCS, Globecom, ICC, ICCCN, IM, NOMS
* 其他: PAM, Hot-ICE, INM/WREN, NDSS,USENIX ATC, POPL, PADL, OSDI, ICFP

计算机网络领域顶级学术会议SIGCOMM在2012年召开了第一届HotSDN研讨会，算是最早一个也是最有名的一个针对SDN的研讨会了。

[HotSDN 2012](http://conferences.sigcomm.org/sigcomm/2012/hotsdn.php)
[HotSDN 2013](http://conferences.sigcomm.org/sigcomm/2013/hotsdn.php)

- Applications of SDN in home, wireless, cellular, enterprise, data-center, and backbone networks
- 2012: Application of SDN to network management, **performance** monitoring, security, etc.
- 2013: Applications of SDN to network management, monitoring, security, etc.
- 2012: Virtual appliances (e.g., firewalls, intrusion detection systems, load balancers, etc.) on SDN
- 2013: Virtual appliances (e.g., firewalls, intrusion detection systems, load balancers, etc.) built using SDN
* 2012: Virtualization **support** in SDN
* 2013: Virtualization in SDN
- Switch designs for SDN
- Application Programming Interfaces for SDN
- Control and management software **stack** for SDN
- Programming languages, verification techniques, and tools for SDN
- Performance evaluation of SDN network elements and controllers
- Experiences deploying SDN technology and applications in operational networks
- 2012: Hybrid SDN approaches (integration with other control planes)
- 2013: Hybrid SDN approaches (not in 2014)
- Transitioning existing networks to SDN
* Placement and factoring of SDN control logic
- Distributed systems abstractions for managing switches and controllers in SDN(2013 only)

到了2014年变动就比较大：

[HotSDN 2014](http://conferences.sigcomm.org/sigcomm/2014/hotsdn.php)

- SDN applications to home, wireless, cellular, enterprise, data-center, and backbone networks
- SDN applications to network management, monitoring, security, etc.
- Security of SDN infrastructure(2014 only)
- Virtualization in SDNs
- Switch designs for SDN
- Application programming interfaces for SDNs(2014 only)
- Control and management software for SDNs
- Programming languages, verification techniques, and tools for SDNs
- Performance evaluation of SDN switches and controllers
- Experiences deploying SDN technology and applications in operational networks
- Transitioning existing networks to SDN
- Placement and factoring of SDN control logic
- SDN-like control applied to other settings, e.g., transport, physical layer, wireless, etc.(2014 only)

[SOSR 2015](http://opennetsummit.org/2015-archive/sosr/)  
[SOSR 2016](http://conferences.sigcomm.org/sosr/2016/)  
[SOSR 2017](http://conferences.sigcomm.org/sosr/2017/)

- Applications of SDN in home, wireless, cellular, enterprise, data-center, backbone, and transport networks
- Applications of SDN to network management, monitoring, security, Internet of Things (IoT), content-centric networking, etc.
- Virtualized network functions (e.g., firewalls, intrusion detection systems, load balancers, etc.) built or managed using software-defined networks
- Virtualization of SDNs(not in 2015)
- White-box and bare-metal switching
- Novel data-plane architectures for software defined networks
- Programming languages, verification techniques, and testing techniques for SDN
- New SDN control frameworks
- Techniques for improving the security, reliability, performance, and scalability of SDNs
- Experiences deploying SDN technology and applications in operational networks
- Strategies for the incremental deployment of SDN in existing networks
- Distributed SDN control planes for better reliability, security, and performance

In addition to traditional research papers, we seek papers that present(not in 2015):

- The design and applications of useful SDN tools
- Surveys of important SDN concepts, techniques, and standards
- Technical overviews of larger research projects, production systems, and use cases
- Open-source benchmark suites or measurement data for evaluating SDN systems

[SDNFLEX 2015](https://www.netsys2015.com/workshops/sdnflex/)
[SDNFLEX 2017](http://sdnflex.org/)
Workshop on Software-Defined Networking and Network Function Virtualization for Flexible Network Management
SDNFLEX其实是两年一次的NetSys会议的研讨会之一，以下是关于NetSys的介绍：
> NetSys is a biennial event that originates from the key scientific conference on networked systems in German-speaking countries KiVS (Kommunikation in Verteilten Systemen) – which was initiated 37 years ago and has international orientation since 2013. NetSys is organized by the special interest group “Communication and Distributed Systems” (KUVS), which is anchored both in the German Computer Science society (Gesellschaft für Informatik (GI)) and in the Information Technology society (Informationstechnische Gesellschaft im VDE (ITG)). It is technically co-sponsored by IEEE Communications Society (ComSoc) and in cooperation with ACM SIGCOMM.  

两届的topics of interest没有变化：

* SDN/NFV architectures, applications, and use cases
* Network monitoring and QoE
* Reliability of virtualized network functions
* SDN/NFV-based service orchestration
* SDN/NFV-based network deployment and management
* Business considerations and economic aspects
* SDN/NFV security
* Theoretical foundations of SDN/NFV networks
* Network Operating Systems and Languages
* SDN in Mobile and Wireless Networks
* Network Service Chaining

会议网站还为期刊IJNM Special Issue on SDN/NFV刊登了CFP:

* SDN/NFV architectures and applications
* Monitoring and QoE management in virtualized networks
* Advanced content placement in virtualized networks
* Business considerations and economic aspects
* Network Operating Systems and Languages
* SDN in Mobile and Wireless Networks
* SDN/NFV Resource allocation algorithms
* Reliability of virtualized network functions
* SDN/NFV-based service orchestration
* SDN/NFV-based network deployment and Management
* Security Management in SDN/NFV-based systems
* Theoretical foundations of SDN/NFV networks
* Construction and deployment of Service Function Chains
* Fault correlation and diagnosis in virtualized networks

CoolSDN(全称COntrol, Operation, and appLication in SDN protocols，感觉像是特地凑出来的名字)是会议ICNP的研讨会，参会者主要来自亚洲，欧洲和北美。

[CoolSDN 2014](http://success.cse.tamu.edu/CoolSDN2014/)  
[CoolSDN 2015](http://success.cse.tamu.edu/CoolSDN2015/)  
[CoolSDN 2016](http://success.cse.tamu.edu/CoolSDN2016/index.php)

- SDN Challenges
- SDN Architecture
- Formal Methods in SDN
- SDN Protocol Engineering
- SDN Programming Correctness
- SDN Programming Interfaces (South Bound, North Bound, and East-west Bound)
- SDN Network Operating System
- SDN Data Plane Abstraction
- SDN Network Abstraction and View
- SDN Performance and Reliability
- SDN Protocol Application
- SDN Security
- SDN Survey

EWSDN也开始的比较早，从2012年便开始了(比HotSDN晚了两个月)，CFP的变动也是十分剧烈，几乎每年都不一样。

[EWSDN 2012](https://www.ewsdn.eu/index.php/EWSDN_2012.html)

* Optical extensions to OpenFlow, SDN
* Wireless integration, characterization of wireless interfaces, flows, handover support
* Evolved packet core, LTE support for OpenFlow
* New controller architectures, Application Programmer's Interfaces, SDN “north-bound” interfaces
* Higher level SDN abstractions, APIs, object models
* New networking paradigms like Content or Information Centric Networks (CCN/ICN) and their relation with SDN
* Carrier-grade SDN architectures and equipment
* Network Virtualization techniques based on SDN
* Practical experiments on OpenFlow / SDN based testbeds
* Identification of use cases for SDN, SDN related business models
* SDN frameworks and tools, SDN based equipment: controllers, switches, integrated development environment, testing and debugging environment

[EWSDN 2013](https://www.ewsdn.eu/index.php/EWSDN_2013.html)

* Forwarding abstractions, APIs, object models
* Network Function Virtualization and SDN, Network Virtualization techniques based on SDN
* Optical extensions to OpenFlow, SDN
* Wireless integration, characterization of wireless interfaces, flows, handover support
* Evolved packet core, LTE support for OpenFlow
* New controller architectures, Application Programmer's Interfaces, SDN "north-bound" interfaces
* Interoperation of cloud architectures and SDN
* New switch architectures supporting SDN
* New networking paradigms like Content or Information Centric Networks (CCN/ICN) and their relation with SDN
* Carrier-grade SDN architectures and equipment
* Practical experiments on OpenFlow / SDN based testbeds
* Identification of use cases for SDN, SDN related business models, strategic role of SDN in network evolution
* SDN frameworks and tools, SDN based equipment: controllers, switches, integrated development environment, testing and debugging environment

[EWSDN 2014](https://www.ewsdn.eu/index.php/EWSDN_2014.html)

* Virtualization architectures
* Forwarding abstractions, APIs, object models
* Network Function Virtualization and SDN, network virtualization techniques
* Optical extensions to OpenFlow, SDN
* Wireless integration, characterization of wireless interfaces, flows, handover support
* Evolved packet core, LTE support for OpenFlow
* New controller architectures, APIs and northbound interfaces
* Interoperation of cloud architectures and SDN
* New switch architectures supporting SDN
* New networking paradigms like Information Centric Networks (ICN) and relation to SDN
* Security implications of SDN and security enhancements to SDN, as well as new security services enabled by SDN
* Carrier-grade SDN architectures and equipment
* Practical experiments on SDN/Cloud/OpenFlow
* Testbeds
* Identification of use cases for SDN, SDN-related business models, strategic role of SDN in network evolution
* Regulatory aspects around network virtualization
* SDN frameworks and tools, SDN-based equipment: controllers, switches, integrated development environment, testing and debugging environment.
* Workshop proceedings


[EWSDN 2015](https://www.ewsdn.eu/index.php/EWSDN_2015.html)

论文链接： [IEEE Xplore - Conference Table of Contents](http://ieeexplore.ieee.org/xpl/mostRecentIssue.jsp?punumber=7312774)

官方没有提供信息，参考WikiCFP上的内容：http://www.wikicfp.com/cfp/servlet/event.showcfp?eventid=47908

[EWSDN 2016](https://www.ewsdn.eu/)

* Network Virtualization and Slicing techniques.
* Northbound Interfaces.
* Advances in Intent programming.
* New switch architectures and software/hardware implementations supporting SDN.
* Debugging SDNs.
* Deployments of SDN and NFV solutions.
* Network Function Virtualization and SDN, network virtualization techniques.
* Coordinated control of NFV and SDN in one infrastructure.
* Interoperation of cloud architectures and SDN.
* Security applications over, implications of and enhancements to SDN.
* Industry challenges to SDN deployment.
网站上没有2017的，最新的CFP待更新。

[ManSDN/NFV 2014](http://www.cnsm-conf.org/2014/sdnnfv2014.html)
[ManSDN/NFV 2015](http://www.cnsm-conf.org/2015/sdnnfv2015.html)

* Design of architectural building blocks for managing SDN and NFV systems
* SDN and NFV APIs and protocols
* Advances in measurement, management, security, accounting, mobility, and other functions using SDN or NFV systems
* Virtualization of resources, services and functions in SDN and NFV
* Management of telco cloud computing infrastructures
* Efficient deployment of Service Function Chains (SFCs)
* Management of federated SDN/NFV infrastructures
* Dynamic migration of network functions in NFV-based systems
* Efficient network and service monitoring for SDN or NFV
* Resource management for SDN or NFV-based systems
* Automated configurations and repair
* Integration of SDN and NFV solutions
* Distributed SDN/NFV control
* Abstractions for programmable network elements
* Computing and software as a network-centric service
* Information exchange between management layers, systems, and components
* Integrated resource management of network and services
* Virtualization of radio network functions
* Policy management in SDN or NFV systems
* Support for bug free Software Defined Networks
* Results from techno-economic, legal and regulatory studies driving the deployment of SDN and NFV technology
* Experience reports from management in experimental testbeds
* Transitioning existing networks to SDN

接下来两年的重点开始变化:

[ManSDN/NFV 2016](http://www.cnsm-conf.org/2016/sdnnfv2016.html)
[ManSDN/NFV 2017](http://www.cnsm-conf.org/2017/sdnnfv2017.html)

* Design of architectural building blocks for managing SDN and NFV systems
* SDN and NFV APIs and protocols
* Advances in measurement, management, security, accounting, mobility, and other functions using SDN or NFV systems
* Virtualization of resources, services and functions in SDN and NFV
* Management of telco cloud computing infrastructures
* Efficient deployment of Service Function Chains (SFCs)
* Management of federated SDN/NFV infrastructures
* Dynamic migration of network functions in NFV-based systems
* Efficient network and service monitoring for SDN or NFV
* Resource management for SDN or NFV-based systems
* Automated configurations and repair
* Integration of SDN and NFV solutions
* Distributed SDN/NFV control
* Abstractions for programmable network elements
* Policy management in SDN or NFV systems
* Support for bug free Software Defined Networks
* Computing and software as a network-centric service
* Information exchange between management layers, systems, and components
* Integrated resource management of network and services
* Virtualization of radio network functions
* Results from techno-economic, legal and regulatory studies driving the deployment of SDN and NFV technology
* Experience reports from management in experimental testbeds
* Transitioning existing networks to SDN
* Deployment of emerging network stacks with network virtualization
* Intent-based networking
* SDN/NFV management in 5G scenarios(2016)
* SDN/NFV management in 5G and wireless networks(2017)

作为新兴的领域，SDN相关的期刊还很少（前面提到过一个），这里不再整理，研讨会的paper我整理好之后会放在博文中。本文会持续更新跟踪最新的SDN相关的会议情况...

Journal 收集：

* [Future Generation Computer Systems](https://www.journals.elsevier.com/future-generation-computer-systems)
* [Computer Networks](https://www.journals.elsevier.com/computer-networks)

类型收集：

- onference and Workshop Papers
- Journal Articles
- Informal Publications
- Editorship
- Books and Theses
- Parts in Books or Collections
