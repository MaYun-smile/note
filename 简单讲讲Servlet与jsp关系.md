## jsp servlet的区别和联系

#### 概述
    简单的说，Sun首先发展出Servlet，其功能比较强劲，体系设计也很先进，只是，它输出
    HTML语句还是采用了老的CGI方式，是一句一句输出，所以，编写和修改HTML非常不方便
    后来,Sun推出了类似于ASP的镶嵌型的JSP，把JSP镶嵌到HTML语句中，这样，就大大简化
    和方便了网页的设计和修改。
    
#### 理解Servlet与Jsp区别    
    1、JSP在本质上就是Servlet,但是两者的创建方式不一样.
    2、Servlet完全是JAVA程序代码构成，擅长于流程控制和事务处理，通过Servlet来生成动态网页很不直观.
    3、JSP由HTML代码和JSP标签构成，可以方便地编写动态网页.因此在实际应用
        中采Servlet来控制业务流程，而采用JSP来生成动态网页
    4、JSP是Servlet技术的扩展，本质上就是Servlet的简易方式。JSP编译后是“类servlet”。
    5、Servlet和JSP最主要的不同点在于，Servlet的应用逻辑是在Java文件中，并且完全从表示层中的HTML里分
    离开来。而JSP是Java和HTML组合成一个扩展名为.jsp的文件。
    6、JSP侧重于视图，Servlet主要用于控制逻辑。
#### 简单的Jsp+Servlet例子
    在webroot 目录下创建login.jsp文件
```
login.jsp

    <body>
      <form action="login">
          username:<input type="text" name="username"><br>
          password:<input type="password" name="pwd"><br>
          <input type="submit"> 
      </form>
    </body>

在scr目录下的com.ht.servlet
编写AcountBean.java文件
public class AcountBean {
    private String username="";
    private String password="";
    public String getPassword() {
         return password;
    }
    public void setPassword(String password) {
         this.password = password;
    }
    public String getUsername() {
         return username;
    }
    public void setUsername(String username) {
         this.username = username;
    }
}

编写servlet类CheckAccount.java文件
public class CheckAccount extends HttpServlet {
    public void doGet(HttpServletRequest request, HttpServletResponse response)
      throws ServletException, IOException {
      
     HttpSession session = request.getSession();
     AcountBean account = new AcountBean();
     String username = request.getParameter("username");
     String pwd = request.getParameter("pwd");
     account.setUsername(username);
     account.setPassword(pwd);
     if ((username != null) && (username.trim().equals("jsp"))) {
      if ((pwd != null) && (pwd.trim().equals("1"))) {
       System.out.println("session");
       session.setAttribute("account", account);
       String logon_suc = "session.jsp";
       response.sendRedirect(logon_suc);
       return;
      }
     }
     String logon_fail = "fail.jsp";
     response.sendRedirect(logon_fail);
     return;
}
public void doPost(HttpServletRequest request, HttpServletResponse response)
      throws ServletException, IOException {
     doGet(request, response);
}
}

在webroot目录下
编写session.jsp文件 成功后跳转
     <body>
        <%
            com.ht.servlet.AcountBean account=(com.ht.servlet.AcountBean)session.getAttribute("account");
        %>
        username: <%=account.getUsername() %>
       <br>
       password:<%=account.getPassword() %>
     </body>

编写fail.jsp文件 失败后跳转
    <body>
       Logon Failed <br>
    </body>

下面是web.xml文件
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.4" 
xmlns="http://java.sun.com/xml/ns/j2ee" 
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee 
http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd">
     <welcome-file-list><welcome-file>login.jsp</welcome-file></welcome-file-list>
     <servlet>
            <description>This is the description of my J2EE component</description>
       <display-name>This is the display name of my J2EE component</display-name>
         <servlet-name>CheckAccount</servlet-name>
       <servlet-class>com.ht.servlet.CheckAccount</servlet-class>
     </servlet>
     <servlet-mapping>
       <servlet-name>CheckAccount</servlet-name>
       <url-pattern>/login</url-pattern>
     </servlet-mapping>
</web-app>
运行tomcat
```