title: '[译]Twisted Mail Tutorial: Building an SMTP Client from Scratch'
date: 2016-11-13 01:21:12
categories: Practice
tags: [twisted, python, network, TLDR]
---
> This tutorial will walk you through the creation of an extremely simple SMTP client application. By the time the tutorial is complete, you will understand how to create and start a TCP client speaking the SMTP protocol, have it connect to an appropriate mail exchange server, and transmit a message for delivery.  
>   
> For the majority of this tutorial, twistd will be used to launch the application. Near the end we will explore other possibilities for starting a Twisted application. Until then, make sure that you have twistd installed and conveniently accessible for use in running each of the example .tac files.  

教程整理自twitsed的官方文档， **原文有删改** ， [原文链接](https://twistedmatrix.com/documents/current/mail/tutorial/smtpclient/smtpclient.html)

**Caution**：本文较长，建议抽出一段较长的时间边运行代码边读。

因为时间有限，很多细节没有翻译，有时间一定补上( ̀⌄ ́)

主要是通过介绍一些及其简单的客户端应用来让我们理解如何创建并启动一个用SMTP协议的TCP客户端，让它能够连接到合适的邮件交换服务器，并能提交用于转发的信息。
<!--more-->

### 开始之前
* 已经配置安装好python，这里使用的版本是2.7.12（注意现在twisted对python 3 的支持还不完全）
* 安装好twisted，这里使用的版本是16.5.0
* 确保电脑上的twistd服务可用（应该会随twisted自动安装）


### 启动SMTP服务器

将下面的代码保存为 `email_server.tac` ，运行 `sudo twistd -ny emailserver.tac` 在本地启动服务器，注意sudo不是必须的，因为电脑限制了25端口的开放，所以必须调用最高权限来启动这段代码，你也可以修改其中的25为其他未占用的端口，但注意也要改动之后的SMTP客户端的代码端口为你修改的端口。


	# Copyright (c) Twisted Matrix Laboratories.
	# See LICENSE for details.

	# You can run this module directly with:
	#    twistd -ny emailserver.tac

	"""
	A toy email server.
	"""
	from __future__ import print_function

	from zope.interface import implementer

	from twisted.internet import defer
	from twisted.mail import smtp
	from twisted.mail.imap4 import LOGINCredentials, PLAINCredentials

	from twisted.cred.checkers import InMemoryUsernamePasswordDatabaseDontUse
	from twisted.cred.portal import IRealm
	from twisted.cred.portal import Portal



	@implementer(smtp.IMessageDelivery)
	class ConsoleMessageDelivery:
	    def receivedHeader(self, helo, origin, recipients):
	        return "Received: ConsoleMessageDelivery"


	    def validateFrom(self, helo, origin):
	        # All addresses are accepted
	        return origin


	    def validateTo(self, user):
	        # Only messages directed to the "console" user are accepted.
	        if user.dest.local == "console":
	            return lambda: ConsoleMessage()
	        raise smtp.SMTPBadRcpt(user)



	@implementer(smtp.IMessage)
	class ConsoleMessage:
	    def __init__(self):
	        self.lines = []


	    def lineReceived(self, line):
	        self.lines.append(line)


	    def eomReceived(self):
	        print("New message received:")
	        print("\n".join(self.lines))
	        self.lines = None
	        return defer.succeed(None)


	    def connectionLost(self):
	        # There was an error, throw away the stored lines
	        self.lines = None



	class ConsoleSMTPFactory(smtp.SMTPFactory):
	    protocol = smtp.ESMTP

	    def __init__(self, *a, **kw):
	        smtp.SMTPFactory.__init__(self, *a, **kw)
	        self.delivery = ConsoleMessageDelivery()


	    def buildProtocol(self, addr):
	        p = smtp.SMTPFactory.buildProtocol(self, addr)
	        p.delivery = self.delivery
	        p.challengers = {"LOGIN": LOGINCredentials, "PLAIN": PLAINCredentials}
	        return p



	@implementer(IRealm)
	class SimpleRealm:
	    def requestAvatar(self, avatarId, mind, *interfaces):
	        if smtp.IMessageDelivery in interfaces:
	            return smtp.IMessageDelivery, ConsoleMessageDelivery(), lambda: None
	        raise NotImplementedError()



	def main():
	    from twisted.application import internet
	    from twisted.application import service

	    portal = Portal(SimpleRealm())
	    checker = InMemoryUsernamePasswordDatabaseDontUse()
	    checker.addUser("guest", "password")
	    portal.registerChecker(checker)

	    a = service.Application("Console SMTP Server")
	    internet.TCPServer(25, ConsoleSMTPFactory(portal)).setServiceParent(a)

	    return a

	application = main()


### 第一个SMTP客户端
可能的话，创建一个 `smtpclient-1.tac` 为twistd所使用。

源代码：

```
from twisted.application import service
# The first line of the .tac file imports twisted.application.service , a module
# which contains many of the basic service classes and helper functions available
# in Twisted. In particular, we will be using the Application function to create
# a new application service . An application service simply acts as a central
# object on which to store certain kinds of deployment configuration.

application = service.Application("SMTP Client Tutorial")

# The second line of the .tac file creates a new application service and binds
# it to the local name application . twistd requires this local name in each .tac
# file it runs. It uses various pieces of configuration on the object to determine
# its behavior. For example, "SMTP Client Tutorial" will be used as the name of
# the .tap file into which to serialize application state, should it be necessary
# to do so.
```

运行我们的服务器，这里-n代表以非后台形式运行，-y表示在python源文件里读取applicaiton：


	❯ twistd -ny smtpclient-1.tac
	2016-11-12T16:55:36+0800 [-] Loading smtpclient-1.tac...
	2016-11-12T16:55:36+0800 [-] Loaded.
	2016-11-12T16:55:37+0800 [twisted.scripts._twistd_unix.UnixAppLogger#info] twistd 16.5.0 (/usr/local/opt/python/bin/python2.7 2.7.12) starting up.
	2016-11-12T16:55:37+0800 [twisted.scripts._twistd_unix.UnixAppLogger#info] reactor class: twisted.internet.selectreactor.SelectReactor.



正如显示的这样，没有什么多余的动作了，用^C 来结束它：

	^C2016-11-12T15:42:31+0800 [-] Received SIGINT, shutting down.
	2016-11-12T15:42:31+0800 [-] Main loop terminated.
	2016-11-12T15:42:31+0800 [twisted.scripts._twistd_unix.UnixAppLogger#info] Server Shut Down.

### 第二个SMTP客户端

第一个客户端并不是十分有趣，它甚至没有建立任何TCP连接，接下来这个 `smtpclient-2.tac` 会更加接近于那个复杂程度。

源代码：

	from twisted.application import service

	application = service.Application("SMTP Client Tutorial")

	from twisted.application import internet
	from twisted.internet import protocol

	# twisted.application.internet is another application service module. It provides
	# services for establishing outgoing connections (as well as creating network
	# servers, though we are not interested in those parts for the moment).
	# twisted.internet.protocol provides base implementations of many of the core
	# Twisted concepts, such as factories and protocols .

	# The next line of smtpclient-2.tac instantiates a new client factory .

	smtpClientFactory = protocol.ClientFactory()
	# Client factories are responsible for constructing protocol instances whenever
	# connections are established. They may be required to create just one instance,
	# or many instances if many different connections are established, or they may
	# never be required to create one at all, if no connection ever manages to be
	# established.

	# Now that we have a client factory, we’ll need to hook it up to the network
	# somehow. The next line of smtpclient-2.tac does just that:

	smtpClientService = internet.TCPClient(None, None, smtpClientFactory)

	# We’ll ignore the first two arguments to internet.TCPClient for the moment and
	# instead focus on the third. TCPClient is one of those application service
	# classes. It creates TCP connections to a specified address and then uses its
	# third argument, a client factory , to get a protocol instance . It then
	# associates the TCP connection with the protocol instance and gets out of the way.

	smtpClientService.setServiceParent(application)


我们来试试运行这个客户端：

	❯ twistd -ny smtpclient-2.tac
	2016-11-12T16:55:57+0800 [-] Loading smtpclient-2.tac...
	2016-11-12T16:55:57+0800 [-] Loaded.
	2016-11-12T16:55:58+0800 [twisted.scripts._twistd_unix.UnixAppLogger#info] twistd 16.5.0 (/usr/local/opt/python/bin/python2.7 2.7.12) starting up.
	2016-11-12T16:55:58+0800 [twisted.scripts._twistd_unix.UnixAppLogger#info] reactor class: twisted.internet.selectreactor.SelectReactor.
	2016-11-12T16:55:58+0800 [stderr#error] Traceback (most recent call last):
	2016-11-12T16:55:58+0800 [stderr#error]   File "/usr/local/bin/twistd", line 11, in <module>
	2016-11-12T16:55:58+0800 [stderr#error]     sys.exit(run())
	2016-11-12T16:55:58+0800 [stderr#error]   File "/usr/local/lib/python2.7/site-packages/twisted/scripts/twistd.py", line 29, in run
	2016-11-12T16:55:58+0800 [stderr#error]     app.run(runApp, ServerOptions)
	2016-11-12T16:55:58+0800 [stderr#error]   File "/usr/local/lib/python2.7/site-packages/twisted/application/app.py", line 648, in run
	2016-11-12T16:55:58+0800 [stderr#error]     runApp(config)
	2016-11-12T16:55:58+0800 [stderr#error]   File "/usr/local/lib/python2.7/site-packages/twisted/scripts/twistd.py", line 25, in runApp
	2016-11-12T16:55:58+0800 [stderr#error]     _SomeApplicationRunner(config).run()
	2016-11-12T16:55:58+0800 [stderr#error]   File "/usr/local/lib/python2.7/site-packages/twisted/application/app.py", line 383, in run
	2016-11-12T16:55:58+0800 [stderr#error]     self.postApplication()
	2016-11-12T16:55:58+0800 [stderr#error]   File "/usr/local/lib/python2.7/site-packages/twisted/scripts/_twistd_unix.py", line 248, in postApplication
	2016-11-12T16:55:58+0800 [stderr#error]     self.startApplication(self.application)
	2016-11-12T16:55:58+0800 [stderr#error]   File "/usr/local/lib/python2.7/site-packages/twisted/scripts/_twistd_unix.py", line 444, in startApplication
	2016-11-12T16:55:58+0800 [stderr#error]     app.startApplication(application, not self.config['no_save'])
	2016-11-12T16:55:58+0800 [stderr#error]   File "/usr/local/lib/python2.7/site-packages/twisted/application/app.py", line 664, in startApplication
	2016-11-12T16:55:58+0800 [stderr#error]     service.IService(application).startService()
	2016-11-12T16:55:58+0800 [stderr#error]   File "/usr/local/lib/python2.7/site-packages/twisted/application/service.py", line 283, in startService
	2016-11-12T16:55:58+0800 [stderr#error]     service.startService()
	2016-11-12T16:55:58+0800 [stderr#error]   File "/usr/local/lib/python2.7/site-packages/twisted/application/internet.py", line 177, in startService
	2016-11-12T16:55:58+0800 [stderr#error]     self._connection = self._getConnection()
	2016-11-12T16:55:58+0800 [stderr#error]   File "/usr/local/lib/python2.7/site-packages/twisted/application/internet.py", line 195, in _getConnection
	2016-11-12T16:55:58+0800 [stderr#error]     'connect%s' % (self.method,))(*self.args, **self.kwargs)
	2016-11-12T16:55:58+0800 [stderr#error]   File "/usr/local/lib/python2.7/site-packages/twisted/internet/posixbase.py", line 482, in connectTCP
	2016-11-12T16:55:58+0800 [stderr#error]     c = tcp.Connector(host, port, factory, timeout, bindAddress, self)
	2016-11-12T16:55:58+0800 [stderr#error]   File "/usr/local/lib/python2.7/site-packages/twisted/internet/tcp.py", line 1164, in __init__
	2016-11-12T16:55:58+0800 [stderr#error]     if abstract.isIPv6Address(host):
	2016-11-12T16:55:58+0800 [stderr#error]   File "/usr/local/lib/python2.7/site-packages/twisted/internet/abstract.py", line 522, in isIPv6Address
	2016-11-12T16:55:58+0800 [stderr#error]     if '%' in addr:
	2016-11-12T16:55:58+0800 [stderr#error] TypeError: argument of type 'NoneType' is not iterable

发生了什么？看来我们忽略掉的那两个参数还挺重要的。

### 第三个SMTP客户端

我们给前面两个参数填上localhost和25（SMTP知名端口号）。


	smtpClientService = internet.TCPClient('localhost', 25, smtpClientFactory)


再次尝试运行：

	❯ twistd -ny smtpclient-3.tac
	2016-11-12T18:28:52+0800 [-] Loading smtpclient-3.tac...
	2016-11-12T18:28:52+0800 [-] Loaded.
	2016-11-12T18:28:52+0800 [twisted.scripts._twistd_unix.UnixAppLogger#info] twistd 16.5.0 (/usr/local/opt/python/bin/python2.7 2.7.12) starting up.
	2016-11-12T18:28:52+0800 [twisted.scripts._twistd_unix.UnixAppLogger#info] reactor class: twisted.internet.selectreactor.SelectReactor.
	2016-11-12T18:28:52+0800 [twisted.internet.protocol.ClientFactory#info] Starting factory <twisted.internet.protocol.ClientFactory instance at 0x11087f908>
	2016-11-12T18:28:52+0800 [Uninitialized] Unhandled Error
		Traceback (most recent call last):
		  File "/usr/local/lib/python2.7/site-packages/twisted/python/log.py", line 101, in callWithLogger
		    return callWithContext({"system": lp}, func, *args, **kw)
		  File "/usr/local/lib/python2.7/site-packages/twisted/python/log.py", line 84, in callWithContext
		    return context.call({ILogContext: newCtx}, func, *args, **kw)
		  File "/usr/local/lib/python2.7/site-packages/twisted/python/context.py", line 118, in callWithContext
		    return self.currentContext().callWithContext(ctx, func, *args, **kw)
		  File "/usr/local/lib/python2.7/site-packages/twisted/python/context.py", line 81, in callWithContext
		    return func(*args,**kw)
		--- <exception caught here> ---
		  File "/usr/local/lib/python2.7/site-packages/twisted/internet/selectreactor.py", line 149, in _doReadOrWrite
		    why = getattr(selectable, method)()
		  File "/usr/local/lib/python2.7/site-packages/twisted/internet/tcp.py", line 586, in doConnect
		    self._connectDone()
		  File "/usr/local/lib/python2.7/site-packages/twisted/internet/tcp.py", line 600, in _connectDone
		    self.protocol = self.connector.buildProtocol(self.getPeer())
		  File "/usr/local/lib/python2.7/site-packages/twisted/internet/base.py", line 1074, in buildProtocol
		    return self.factory.buildProtocol(addr)
		  File "/usr/local/lib/python2.7/site-packages/twisted/internet/protocol.py", line 131, in buildProtocol
		    p = self.protocol()
		exceptions.TypeError: 'NoneType' object is not callable

	2016-11-12T18:28:52+0800 [twisted.internet.protocol.ClientFactory#info] Stopping factory <twisted.internet.protocol.ClientFactory instance at 0x11087f908>

看来还是有问题，不过这一次是因为我们没有明确protocol类给factory用造成的，让我们来看看第四个版本。

### 第四个SMTP客户端

因为我们没有给客户端factory的protocol实行设定一个正确的值，我们无法成功运行上一个例子。ClientFactory.buildProtocol是一个负责创建一个protocol实例的方法，我们这里暂且使用protocol的基类来完成修改。

	from twisted.application import service

	application = service.Application("SMTP Client Tutorial")

	from twisted.application import internet
	from twisted.internet import protocol

	smtpClientFactory = protocol.ClientFactory()
	smtpClientFactory.protocol = protocol.Protocol

	smtpClientService = internet.TCPClient('localhost', 25, smtpClientFactory)
	smtpClientService.setServiceParent(application)

再次运行：

	❯ twistd -ny smtpclient-4.tac
	2016-11-12T18:46:08+0800 [-] Loading smtpclient-4.tac...
	2016-11-12T18:46:08+0800 [-] Loaded.
	2016-11-12T18:46:08+0800 [twisted.scripts._twistd_unix.UnixAppLogger#info] twistd 16.5.0 (/usr/local/opt/python/bin/python2.7 2.7.12) starting up.
	2016-11-12T18:46:08+0800 [twisted.scripts._twistd_unix.UnixAppLogger#info] reactor class: twisted.internet.selectreactor.SelectReactor.
	2016-11-12T18:46:08+0800 [twisted.internet.protocol.ClientFactory#info] Starting factory <twisted.internet.protocol.ClientFactory instance at 0x109a3e908>
	^C2016-11-12T18:46:33+0800 [-] Received SIGINT, shutting down.
	2016-11-12T18:46:33+0800 [twisted.internet.protocol.ClientFactory#info] Stopping factory <twisted.internet.protocol.ClientFactory instance at 0x109a3e908>
	2016-11-12T18:46:33+0800 [-] Main loop terminated.
	2016-11-12T18:46:33+0800 [twisted.scripts._twistd_unix.UnixAppLogger#info] Server Shut Down.

对于传统UNIX网络服务熟悉的人，twisted.internet.protocol其实等价于discard服务——它将永远不会制造任何输出并且丢弃它所有的输入，可以说毫无用处。

### 第五个SMTP客户端

在这个例子中，我们将第一次使用Twisted的SMTP协议实现。我们将作出明显的改变，仅仅是将 twisted.internet.protocol.Protocol换成twisted.mail.smtp.ESMTPClient，不用担心ESMTP中的E，它告诉我们只是使用一个更新版本的SMTP协议。虽然Twisted中存在SMTPClient，但是我们没有理由去使用它。

	from twisted.application import service

	application = service.Application("SMTP Client Tutorial")

	from twisted.application import internet
	from twisted.internet import protocol

	smtpClientFactory = protocol.ClientFactory()

	from twisted.mail import smtp
	smtpClientFactory.protocol = smtp.ESMTPClient

	smtpClientService = internet.TCPClient('localhost', 25, smtpClientFactory)
	smtpClientService.setServiceParent(application)

运行：

	❯ twistd -ny smtpclient-5.tac
	2016-11-12T18:53:19+0800 [-] Loading smtpclient-5.tac...
	2016-11-12T18:53:19+0800 [-] Loaded.
	2016-11-12T18:53:19+0800 [twisted.scripts._twistd_unix.UnixAppLogger#info] twistd 16.5.0 (/usr/local/opt/python/bin/python2.7 2.7.12) starting up.
	2016-11-12T18:53:19+0800 [twisted.scripts._twistd_unix.UnixAppLogger#info] reactor class: twisted.internet.selectreactor.SelectReactor.
	2016-11-12T18:53:19+0800 [twisted.internet.protocol.ClientFactory#info] Starting factory <twisted.internet.protocol.ClientFactory instance at 0x110622908>
	2016-11-12T18:53:19+0800 [Uninitialized] Unhandled Error
		Traceback (most recent call last):
		  File "/usr/local/lib/python2.7/site-packages/twisted/python/log.py", line 101, in callWithLogger
		    return callWithContext({"system": lp}, func, *args, **kw)
		  File "/usr/local/lib/python2.7/site-packages/twisted/python/log.py", line 84, in callWithContext
		    return context.call({ILogContext: newCtx}, func, *args, **kw)
		  File "/usr/local/lib/python2.7/site-packages/twisted/python/context.py", line 118, in callWithContext
		    return self.currentContext().callWithContext(ctx, func, *args, **kw)
		  File "/usr/local/lib/python2.7/site-packages/twisted/python/context.py", line 81, in callWithContext
		    return func(*args,**kw)
		--- <exception caught here> ---
		  File "/usr/local/lib/python2.7/site-packages/twisted/internet/selectreactor.py", line 149, in _doReadOrWrite
		    why = getattr(selectable, method)()
		  File "/usr/local/lib/python2.7/site-packages/twisted/internet/tcp.py", line 586, in doConnect
		    self._connectDone()
		  File "/usr/local/lib/python2.7/site-packages/twisted/internet/tcp.py", line 600, in _connectDone
		    self.protocol = self.connector.buildProtocol(self.getPeer())
		  File "/usr/local/lib/python2.7/site-packages/twisted/internet/base.py", line 1074, in buildProtocol
		    return self.factory.buildProtocol(addr)
		  File "/usr/local/lib/python2.7/site-packages/twisted/internet/protocol.py", line 131, in buildProtocol
		    p = self.protocol()
		exceptions.TypeError: __init__() takes at least 2 arguments (1 given)

	2016-11-12T18:53:19+0800 [twisted.internet.protocol.ClientFactory#info] Stopping factory <twisted.internet.protocol.ClientFactory instance at 0x110622908>

看来还是有问题，这次的原因在于默认的buildProtocol在实例化protocol时没有参数，但是ESMTPClient想要至少一个参数，在下一个版本里，我们将通过重载buildProtocol函数来解决问题。

### 第六个SMTP客户端

代码：

	from twisted.application import service

	application = service.Application("SMTP Client Tutorial")

	from twisted.application import internet
	from twisted.internet import protocol
	from twisted.mail import smtp

	class SMTPClientFactory(protocol.ClientFactory):
	    protocol = smtp.ESMTPClient

	    def buildProtocol(self, addr):
	        return self.protocol(secret=None, identity='example.com')
	# The overridden method does almost the same thing as the base implementation:
	# the only change is that it passes values for two arguments to
	# twisted.mail.smtp.ESMTPClient ‘s initializer. The secret argument is used for
	# SMTP authentication (which we will not attempt yet). The identity argument is
	# used as a to identify ourselves Another minor change to note is that the
	# protocol attribute is now defined in the class definition, rather than tacked
	# onto an instance after one is created. This means it is a class attribute,
	# rather than an instance attribute, now, which makes no difference as far as
	# this example is concerned. There are circumstances in which the difference is
	# important: be sure you understand the implications of each approach when
	# creating your own factories.

	# One other change is required: instead of instantiating
	# twisted.internet.protocol.ClientFactory , we will now instantiate
	# SMTPClientFactory :
	smtpClientFactory = SMTPClientFactory()

	smtpClientService = internet.TCPClient('localhost', 25, smtpClientFactory)
	smtpClientService.setServiceParent(application)

运行：

	❯ twistd -ny smtpclient-6.tac
	2016-11-12T19:53:54+0800 [-] Loading smtpclient-6.tac...
	2016-11-12T19:53:54+0800 [-] Loaded.
	2016-11-12T19:53:54+0800 [twisted.scripts._twistd_unix.UnixAppLogger#info] twistd 16.5.0 (/usr/local/opt/python/bin/python2.7 2.7.12) starting up.
	2016-11-12T19:53:54+0800 [twisted.scripts._twistd_unix.UnixAppLogger#info] reactor class: twisted.internet.selectreactor.SelectReactor.
	2016-11-12T19:53:54+0800 [__builtin__.SMTPClientFactory#info] Starting factory <__builtin__.SMTPClientFactory instance at 0x1011ed3b0>
	2016-11-12T19:53:54+0800 [ESMTPClient,client] Unhandled Error
		Traceback (most recent call last):
		  File "/usr/local/lib/python2.7/site-packages/twisted/python/log.py", line 101, in callWithLogger
		    return callWithContext({"system": lp}, func, *args, **kw)
		  File "/usr/local/lib/python2.7/site-packages/twisted/python/log.py", line 84, in callWithContext
		    return context.call({ILogContext: newCtx}, func, *args, **kw)
		  File "/usr/local/lib/python2.7/site-packages/twisted/python/context.py", line 118, in callWithContext
		    return self.currentContext().callWithContext(ctx, func, *args, **kw)
		  File "/usr/local/lib/python2.7/site-packages/twisted/python/context.py", line 81, in callWithContext
		    return func(*args,**kw)
		--- <exception caught here> ---
		  File "/usr/local/lib/python2.7/site-packages/twisted/internet/selectreactor.py", line 149, in _doReadOrWrite
		    why = getattr(selectable, method)()
		  File "/usr/local/lib/python2.7/site-packages/twisted/internet/tcp.py", line 208, in doRead
		    return self._dataReceived(data)
		  File "/usr/local/lib/python2.7/site-packages/twisted/internet/tcp.py", line 214, in _dataReceived
		    rval = self.protocol.dataReceived(data)
		  File "/usr/local/lib/python2.7/site-packages/twisted/protocols/basic.py", line 571, in dataReceived
		    why = self.lineReceived(line)
		  File "/usr/local/lib/python2.7/site-packages/twisted/mail/smtp.py", line 913, in lineReceived
		    why = self._okresponse(self.code,'\n'.join(self.resp))
		  File "/usr/local/lib/python2.7/site-packages/twisted/mail/smtp.py", line 1350, in esmtpState_serverConfig
		    self.tryTLS(code, resp, items)
		  File "/usr/local/lib/python2.7/site-packages/twisted/mail/smtp.py", line 1395, in tryTLS
		    self.authenticate(code, resp, items)
		  File "/usr/local/lib/python2.7/site-packages/twisted/mail/smtp.py", line 1462, in authenticate
		    self.smtpState_from(code, resp)
		  File "/usr/local/lib/python2.7/site-packages/twisted/mail/smtp.py", line 940, in smtpState_from
		    self._from = self.getMailFrom()
		  File "/usr/local/lib/python2.7/site-packages/twisted/mail/smtp.py", line 1035, in getMailFrom
		    raise NotImplementedError
		exceptions.NotImplementedError:

	2016-11-12T19:53:54+0800 [__builtin__.SMTPClientFactory#info] Stopping factory <__builtin__.SMTPClientFactory instance at 0x1011ed3b0>

> What we have accomplished with this iteration of the example is to navigate far enough into an SMTP transaction that Twisted is now interested in calling back to application-level code to determine what its next step should be. In the next example, we’ll see how to provide that information to it.  
（这句有点长，怕翻译的不到位……）

### 第七个SMTP客户端

这个版本时我们的SMTP客户端第一次真正包括了数据传递。

为了简洁性考虑，信息被定义为新类的一部分。在实际可用于发送邮件的客户端，信息数据可能会从文件系统、数据库，还可能由用户输入中引入。

在这里我们在类里定义了三个属性 `(mailFrom , mailTo , and mailData )`

	from __future__ import print_function
	import StringIO

	from twisted.application import service

	application = service.Application("SMTP Client Tutorial")

	from twisted.application import internet
	from twisted.internet import protocol
	from twisted.mail import smtp

	class SMTPTutorialClient(smtp.ESMTPClient):
	    mailFrom = "tutorial_sender@example.com"
	    mailTo = "tutorial_recipient@example.net"
	    mailData = '''\
	Date: Fri, 6 Feb 2004 10:14:39 -0800
	From: Tutorial Guy <tutorial_sender@example.com>
	To: Tutorial Gal <tutorial_recipient@example.net>
	Subject: Tutorate!

	Hello, how are you, goodbye.
	'''

	    def getMailFrom(self):
	        result = self.mailFrom
	        self.mailFrom = None
	        return result

	    def getMailTo(self):
	        return [self.mailTo]

	    def getMailData(self):
	        return StringIO.StringIO(self.mailData)

	    def sentMail(self, code, resp, numOk, addresses, log):
	        print('Sent', numOk, 'messages')

	class SMTPClientFactory(protocol.ClientFactory):
	    protocol = SMTPTutorialClient

	    def buildProtocol(self, addr):
	        return self.protocol(secret=None, identity='example.com')

	smtpClientFactory = SMTPClientFactory()

	smtpClientService = internet.TCPClient('localhost', 25, smtpClientFactory)
	smtpClientService.setServiceParent(application)

我们来剖析一下其中的四个函数：

* `def getMailFrom(self):` 这个方法用于确定 `reverse-path`  ——也被称作发件地址。这个值被用于SMTP中的 `MAIL` 命令。这个方法必须返回符合 RFC 2821 定义的一个 `reverse-path` 。简单的说，就是它可以是i 个像"alice@example.com”这样的字符串。注意只有一个发件地址被SMTP协议所允许；
* `def getMailTo(self):` 和上一个方法差不多，返回一个或多个RFC 2821定义的 `forward-path` —— 转发地址，注意它必须包括至少一个地址，即使只有一个，它也将是一个列表（感觉这里好啰嗦）；
* `def getMailData(self):` 这个也很简单，它将会返回一个包含信息内容的文件或者类文件对象。在这里，它将返回一个StringIO因为我们已经有一个包括信息的字符窜了。假使文件内容包括多行（正如邮件通常的样子），行间应该由 `\n` 来划分（正如我们经常以“rt”模式打开文本文件）：必要的newline会由 `SMTPClient ` 自动转换；
* `def sentMail(self, code, resp, numOk, addresses, log):` 这个方法和邮件发送没有太大关系，是用给twisted来提供信息传递成功与失败信息的；其中 `code` 是最终命令返回的状态码：
> For successful transactions, it will be 250. For transient failures (those which should be retried), it will be between 400 and 499, inclusive. For permanent failures (this which will never work, no matter how many times you retry them), it will be between 500 and 599.  

	❯ twistd -ny smtpclient-7.tac
	2016-11-12T20:06:32+0800 [-] Loading smtpclient-7.tac...
	2016-11-12T20:06:32+0800 [-] Loaded.
	2016-11-12T20:06:32+0800 [twisted.scripts._twistd_unix.UnixAppLogger#info] twistd 16.5.0 (/usr/local/opt/python/bin/python2.7 2.7.12) starting up.
	2016-11-12T20:06:32+0800 [twisted.scripts._twistd_unix.UnixAppLogger#info] reactor class: twisted.internet.selectreactor.SelectReactor.
	2016-11-12T20:06:32+0800 [__builtin__.SMTPClientFactory#info] Starting factory <__builtin__.SMTPClientFactory instance at 0x10c04fa70>
	2016-11-12T20:06:32+0800 [stdout#info] Sent 0 messages
	2016-11-12T20:06:32+0800 [__builtin__.SMTPClientFactory#info] Stopping factory <__builtin__.SMTPClientFactory instance at 0x10c04fa70>

### 第八个SMTP客户端

这个版本就添加了两行代码，避免了每次发送完邮件后都要输入^C 的尴尬……

	from __future__ import print_function
	import StringIO

	from twisted.application import service

	application = service.Application("SMTP Client Tutorial")

	from twisted.application import internet
	from twisted.internet import protocol
	from twisted.mail import smtp

	class SMTPTutorialClient(smtp.ESMTPClient):
	    mailFrom = "tutorial_sender@example.com"
	    # mailTo = "tutorial_recipient@example.net"
	    mailTo = "console@example.net"
	    mailData = '''\
	Date: Fri, 6 Feb 2004 10:14:39 -0800
	From: Tutorial Guy <tutorial_sender@example.com>
	To: Tutorial Gal <tutorial_recipient@example.net>
	Subject: Tutorate!

	Hello, how are you, goodbye.
	'''

	    def getMailFrom(self):
	        result = self.mailFrom
	        self.mailFrom = None
	        return result

	    def getMailTo(self):
	        return [self.mailTo]

	    def getMailData(self):
	        return StringIO.StringIO(self.mailData)

	    def sentMail(self, code, resp, numOk, addresses, log):
	        print('Sent', numOk, 'messages')

	        from twisted.internet import reactor
	        reactor.stop()

	class SMTPClientFactory(protocol.ClientFactory):
	    protocol = SMTPTutorialClient

	    def buildProtocol(self, addr):
	        return self.protocol(secret=None, identity='example.com')

	smtpClientFactory = SMTPClientFactory()

	smtpClientService = internet.TCPClient('localhost', 25, smtpClientFactory)
	smtpClientService.setServiceParent(application)

stop作为reactor的一个方法，是退出twisted事件循环的重要函数。（关于对twisted异步编程的机制，在过段时间对twisted有了更深入的理解后会总结成一篇博文。）

运行：

	❯ twistd -ny smtpclient-8.tac
	2016-11-12T20:55:58+0800 [-] Loading smtpclient-8.tac...
	2016-11-12T20:55:58+0800 [-] Loaded.
	2016-11-12T20:55:58+0800 [twisted.scripts._twistd_unix.UnixAppLogger#info] twistd 16.5.0 (/usr/local/opt/python/bin/python2.7 2.7.12) starting up.
	2016-11-12T20:55:58+0800 [twisted.scripts._twistd_unix.UnixAppLogger#info] reactor class: twisted.internet.selectreactor.SelectReactor.
	2016-11-12T20:55:58+0800 [__builtin__.SMTPClientFactory#info] Starting factory <__builtin__.SMTPClientFactory instance at 0x10213ca70>
	2016-11-12T20:55:58+0800 [stdout#info] Sent 0 messages
	2016-11-12T20:55:58+0800 [__builtin__.SMTPClientFactory#info] Stopping factory <__builtin__.SMTPClientFactory instance at 0x10213ca70>
	2016-11-12T20:55:58+0800 [-] Main loop terminated.
	2016-11-12T20:55:58+0800 [twisted.scripts._twistd_unix.UnixAppLogger#info] Server Shut Down.

### 第九个SMTP客户端
还存在一个遗留任务让这个tutorial SMTP变完整：相对总是通过知名主机寄送邮件，我们还会查询邮件交换服务器来寻找收件人的地址并且尝试递交信息给那个主机。

在这个版本，我们先是通过定义一个函数来完成这个特性

	def getMailExchange(host):
	    return 'localhost'

很明显这并没有返回正确的邮件交换主机（实际上，它返回我们现在一直使用的主机），但是它给出了决定连接哪个主机的逻辑。我们用它来构造新的TCPClient服务：

	smtpClientService = internet.TCPClient(
	    getMailExchange('example.net'), 25, smtpClientFactory)

下个版本我们会扩充 `getMailExchange` 的定义。


### 第十个SMTP客户端

对一个特定的域名确定一个邮件交换主机需要包括网络交通（特别的，一些DNS请求）。则可能要花一个非常长的时间，所以我们介绍 `Deferred` 来表示 `getMailExchange` 的返回结果，修改函数为：

	def getMailExchange(host):
	    return defer.succeed('localhost')

`defer.succeed` 用已经存在的结果创建一个新的 `Deferred` 对象，现在我需要调整TCPClient来构造能够期望并且恰当处理这个 `Deferred` 代码。

	def cbMailExchange(exchange):
	    smtpClientFactory = SMTPClientFactory()

	    smtpClientService = internet.TCPClient(exchange, 25, smtpClientFactory)
	    smtpClientService.setServiceParent(application)

	getMailExchange('example.net').addCallback(cbMailExchange)

深入了解 `Deferred` 已经超出了本文档的范畴，访问这里获得更多信息 [Deferred Reference — Twisted 16.5.0 documentation](https://twistedmatrix.com/documents/current/core/howto/defer.html)

### 第十一个SMTP客户端

我们已经准备好对邮件交换的查询。现在我们将defer换成真正的查找函数—— `twisted.mail.relaymanager.MXCalculator`

	def getMailExchange(host):
	    def cbMX(mxRecord):
	        return str(mxRecord.name)
	    return relaymanager.MXCalculator().getMX(host).addCallback(cbMX)

因为getMX返回Record_MX对象而不是string对象，在返回之前我们再多做一些预处理。现在我们完成了这个tutorial  SMTP客户端，它能够：

* look up the mail exchange host for the recipient domain
* connect to it
* complete an SMTP transaction
* report its results
* finally shut down the reacto

最终版本：

	from __future__ import print_function
	import StringIO

	from twisted.application import service

	application = service.Application("SMTP Client Tutorial")

	from twisted.application import internet
	from twisted.internet import protocol
	from twisted.internet import defer
	from twisted.mail import smtp, relaymanager

	class SMTPTutorialClient(smtp.ESMTPClient):
	    mailFrom = "tutorial_sender@example.com"
	    mailTo = "tutorial_recipient@example.net"
	    mailData = '''\
	Date: Fri, 6 Feb 2004 10:14:39 -0800
	From: Tutorial Guy <tutorial_sender@example.com>
	To: Tutorial Gal <tutorial_recipient@example.net>
	Subject: Tutorate!

	Hello, how are you, goodbye.
	'''

	    def getMailFrom(self):
	        result = self.mailFrom
	        self.mailFrom = None
	        return result

	    def getMailTo(self):
	        return [self.mailTo]

	    def getMailData(self):
	        return StringIO.StringIO(self.mailData)

	    def sentMail(self, code, resp, numOk, addresses, log):
	        print('Sent', numOk, 'messages')

	        from twisted.internet import reactor
	        reactor.stop()

	class SMTPClientFactory(protocol.ClientFactory):
	    protocol = SMTPTutorialClient

	    def buildProtocol(self, addr):
	        return self.protocol(secret=None, identity='example.com')

	def getMailExchange(host):
	    def cbMX(mxRecord):
	        return str(mxRecord.name)
	    return relaymanager.MXCalculator().getMX(host).addCallback(cbMX)

	def cbMailExchange(exchange):
	    smtpClientFactory = SMTPClientFactory()

	    smtpClientService = internet.TCPClient(exchange, 25, smtpClientFactory)
	    smtpClientService.setServiceParent(application)

	getMailExchange('example.net').addCallback(cbMailExchange)
