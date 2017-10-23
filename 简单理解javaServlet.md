## javaServlet


#### 一、Servlet是什么
Servlet是java语言边写应用到Web服务器端的扩展技术，可以方便的对Web应用中的HTTP请求进行处理。在java Web程序开发中，Servlet主要用于处理各种业务逻辑。servlet从客户端(通过Web服务器)接收请求，执行某种作业，然后返回结果。

---想要了解Servlet先去了解一下Tomcat和Servlet关系.简单的讲一下Tomcat---
#### Tomcat与Servlet关系
    Tomcat 是Web应用服务器,是一个Servlet/JSP容器. 
    Tomcat 作为Servlet容器,负责处理客户请求,把请求传送给Servlet,并将Servlet的响应传送回给客户
    1：Tomcat将http请求文本接收并解析，然后封装成HttpServletRequest类型的request对象， 所有的HTTP头数据读可以通过request对象
    调用对应的方法查询
    2：Tomcat同时会要响应的信息封装为HttpServletResponse类型的response对象，通过设置response属性就可以控制要输出到浏览器的内
    容，然后将response交给tomcat，tomcat就会将其变成响应文本的格式发送给浏览器
    
    
#### 二、编写Servlet

   1、创建一个MyServlet继承HttpServlet，重写doGet和doPost方法，也就是看请求的方式是get还是post，然后用不同的处理方式来处理请求，
    
```python
    public class TestServlet extends HttpServlet {

	//处理HTTP GET请求
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException,
		IOException {
	    System.out.printin("get");
	}
	//处理HTTP POST请求
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException,
		IOException {
		System.out.printin("post");
	}
```	
	
2、在web.xml中配置MyServlet，为什么需要配置？让浏览器发出的请求知道到达哪个servlet，也就是让tomcat将封装好的request找到对应的servlet让其使用。
   
```python
    <servlet>
		<!-- 给Servlet取名字。这个无所谓去什么，一般和我们servet想同 -->
		<servlet-name>MyServleet</servlet-name>
		<!-- Servlet的位置 -->
		<servlet-class>com.test.MyServleet</servlet-class>
	</servlet>
	<servlet-mapping>
		<!-- 跟上面的Servlet名字一样 -->
		<servlet-name>MyServleet</servlet-name>
		<!-- 浏览器通过给url找到servlet，/相当于项目名称 -->
		<url-pattern>/MyServleet</url-pattern>
	</servlet-mapping>
```
3、按照步骤，首先浏览器通过http://localhost:8080/ServletTest/MyServlet来找到web.xml中的url-
pattern，这就是第一步，匹配到了url-pattern后，就会找到第二步servlet的名字MyServlet，知道了名字
，就可以通过servlet-name找到第三步，到了第三步，也就能够知道servlet的位置了。然后到其中找到对
应的处理方式进行处理
Java Servlet API 是Servlet容器(tomcat)和servlet之间的接口，它定义了serlvet的各种方法，还定义了Servlet容器传送给Servlet的对象类，其中最重要的就是ServletRequest和ServletResponse。所以说我们在编写servlet时，需要实现Servlet接口，按照其规范进行操作。


#### 三、Servlet的原理
#####   servlet的生命周期是什么？
服务器启动时，第一次请求该servlet时，就会初始化一个Servlet对象，也就是会执行初始化方法init
　该servlet对象去处理所有客户端请求，在service(ServletRequest req，ServletResponse res)方法中执行
　最后服务器关闭时，才会销毁这个servlet对象，执行destroy()方法。
###### 代码
   ```python
    public class ServletTest extends HttpServlet {
	//初始化方法
        public void init(ServletConfig config) throws ServletException {
    	}
	//处理业务逻辑
    	protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException,
		IOException {
    	}
	//处理HTTP GET请求
    	protected void doGet(HttpServletRequest request, HttpServletResponse     response) throws ServletException,
		IOException {
    	}
	//处理HTTP POST请求
    	protected void doPost(HttpServletRequest request, HttpServletResponse     response) throws ServletException, 
		IOException {
    		//一些业务get的是完不成，例如表单的提交用post，这样方便处理请求，不需要在post和get来回写很多代码。
    				doGet(request, response);
	}
	//处理HTTP Delete请求
    	protected void doDelete(HttpServletRequest request, HttpServletResponse response) throws ServletException, 
		IOException {
    	}
	//销毁方法
    	public void destroy() {
    	}
    }
```
#####  Servlet API编程常用的接口和类

我们查看上面的源码，httpServlet的继承结构。
    httpServlet继承GenericServlet，会想到GenericServlet是怎么用的，应该就是实现Servlet接口的方法，简化写Servlet步骤。
   ```python
    public class ServletTest extends HttpServlet
   ```
GenericServlet的继承结构，实现了Servlet接口和ServletConfig接口
   ```python 
    public abstract class GenericServlet implements Servlet, ServletConfig,   
   ``` 
######    Servlet接口
    public void init(ServletConfig config);	
    public void service(ServletRequest request,ServletResponse Response);
    public void destroy();
    public ServletConfig getServletConfig();	
    public String getServletInfo();
Servlet中，主要的方法是service(),当客户端请求到来时，Servlet容器讲调用Servlet实例的service()方法对请求进行处理。

######    ServletConfig接口
    public String getInitParamerter(String name)
    public Enumeration getInitParameterNames();
    public ServletContext getServletContext()
    public String getServletName()
这个接口封装了Servlet的配置信息，在Servlet初始化期间被传递。每个Servlet都有且一个ServletConfig对象。
其中ServletContext对象是servlet上下文对象，功能有很多，获得了ServletContext对象，就能获取大部分我们需要的信息，比如获取servlet的路径，等方法。

######    GenericServlet类的内容详解

 GenericServlet类是一个抽象类，分别实现了Servlet接口与ServletConfig接口，该类实现了除service（）之外的其他方法，创建Servlet时，可以继承 GenericServlet类来简化代码，但要实现service（）方法
######    HttpServlet类的内容详解
在实际开发中，大多数应用都是用了Servlet处理HTTP协议请求，并对请求做出相应，并通过继承GenericServlet类，但任然不是很方便。所以将拓展一个类叫HttpServlet,为http请求处理提供了灵活的方法

HttpServlet类。实现了service（）方法，并提供请求类型7中。HttpServlet类继承了GenericServlet类，通过对GenericServlet类进行拓展，可以和方便的对HTTP请求进行处理及响应。

### 总结
1.什么是servlet？如果编写servlet？

1.明白了Servlet的工作原理以及生命周期

2.理解了一些Servlet接口的调用和类拓展的方便性

3.分析了servlet的部分源码，知道了其中的一些设计的东西。
