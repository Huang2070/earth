# 区别

1. Apache是web服务器，Tomcat是应用（java）服务器，它只是一个servlet容器，是Apache的扩展。

2. Apache和Tomcat都可以做为独立的web服务器来运行，但是Apache不能解释java程序（jsp,serverlet）。

3. Apache是普通服务器，本身只支持html即普通网页。不过可以通过插件支持php,还可以与Tomcat连通(单向Apache连接Tomcat,就是说通过Apache可以访问Tomcat资源。反之不然)

4. 两者都是一种容器，只不过发布的东西不同：Apache是html容器，功能像IIS一样；Tomcat是jsp/servlet容器，用于发布jsp及java的，类似的有IBM的webshere、EBA的Weblogic，sun的JRun等等。

5. Apache和Tomcat是独立的，在通一台服务器上可以集成。

打个比方：Apache是一辆卡车，上面可以装一些东西如html等。但是不能装水，要装水必须要有容器（桶），Tomcat就是一个桶（装像Java这样的水），而这个桶也可以不放在卡车上。

Apache只支持静态网页，但像asp,php,cgi,jsp等动态网页就需要Tomcat来处理。

Apache和Tomcat整合使用：如果客户端请求的是静态页面，则只需要Apache服务器响应请求；如果客户端请求动态页面，则是Tomcat服务器响应请求；因为jsp是服务器端解释代码的，这样整合就可以减少Tomcat的服务开销 。

Apache是世界使用排名第一的Web服务器。它可以运行在几乎所有广泛使用的计算机平台上。

Apache 源于 NCSAhttpd服务器，经过多次修改，成为世界上最流行的Web服务器软件之一。Apache取自“a patchy server”的读音，意思是充满补丁的服务器，因为它是自由软件，所以不断有人来为它开发新的功能、新的特性、修改原来的缺陷。Apache的特点是简单、速度快、性能稳定，并可做代理服务器来使用。Apache对Linux的支持相当完美。

Tomcat是一个开放源代码、运行servlet和JSP Web应用软件的基于Java的Web应用软件容器。Tomcat Server是根据servlet和JSP规范进行执行的，因此我们就可以说Tomcat Server也实行了Apache-Jakarta规范且比绝大多数商业应用软件服务器要好。

Tomcat是Java Servlet 2.2和JavaServer Pages 1.1技术的标准实现，是基于Apache许可证下开发的自由软件。Tomcat是完全重写的Servlet API 2.2和JSP 1.1兼容的Servlet/JSP容器。Tomcat使用了JServ的一些代码，特别是Apache服务适配器。随着Catalina Servlet引擎的出现，Tomcat第四版号的性能得到提升，使得它成为一个值得考虑的Servlet/JSP容器，因此目前许多WEB服务器都是采用Tomcat。