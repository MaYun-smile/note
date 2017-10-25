## Yigo

#### 完成增加一个用户进入数据库
1. Yigo里面配置一个单据，姓名-username，密码-password.一个点击按钮-register
    ######    注意：想要在数据库里存在你创建的张单据对应的表的话，单据表单必须创建为实体表单。
    设置姓名和密码对应的标识。register注册按钮的点击事件写为
```
    InvokeService(name, withDoc, refresh, paras...)
    备注 ：name 服务名
           withDoc 是否将客户端文档发往中间层的标志
           refresh 是否在调用完成后刷新界面的标志
           paras 不定长参数，为服务指定的参数
    例子：InvokeService("RegisterDemo", false, false, username,password)
```
2.配置Yigo与创建的二次开发的连接。
    在自己创建Yigo项目的配置Enhance.xml里面写上对应的配置
```
    <?xml version="1.0" encoding="UTF-8" standalone="no"?>
        <Enhance>
            <ExtService>
                <Service Name="RegisterDemo" Description="注册"
                    Impl="com.bokesoft.yigo.pc.RegisterDemo"/>
            </ExtService>
        </Enhance>
        备注：Name             自己创建的项目对应的servlet服务名称 
              Description      对自己应用的描述
              Impl             创建的服务名称的路径地址
```
3.环境基于Tomcat 7.0搭建，发布包中内置了tomcat 7.0的环境。
```    
    core.properties
        SolutionPath=D:\\YigoTest\\register   //配置对应的yigo项目的地址
        DSN=MYSQL                             //配置要用到的配置数据库的文件名
        DEFAULT=
        LOGSVR=
        DEBUG=true
        
    MYSQL.properties                         
        DSNTag=
        Name=MYSQL                            //数据源的名称，同文件名相同
        ConnectionType=jdbc                   //连接类型，我这里选用的是jdbc 
        DBType=Mysql                          //数据库类型，我这里用的是mysql
        Driver=com.mysql.jdbc.Driver          //数据库的驱动
        URL=jdbc:mysql://localhost:3306/myku1?useUnicode=true&amp;characterEncoding=UTF-8
                                              //数据库连接路径
        User=root                             //数据库的用户名
        Password=root                         //数据库的密码
    其他的配置，我这个小项目需求很少，不需要改动
```

##### 这时候可以点击tomcat-bin-startup.开启服务，在客户端上输入localhost:8089/yigo进入工作台看是否成功开启，出现登录页面
4.创建自己的Servletd服务名。例如RegisterDemo，继承IExtService，去实现doCmd这个方法
```
    public class RegisterDemo implements IExtService {
	    @Override
	    public Object doCmd(DefaultContext context, ArrayList<Object> paras) throws Throwable {
            //写对应的相关逻辑，我这个是做将参数保存在数据库里
        }
    {
```
5.在tomcat里面要配置 path.xml 指定的项目Yigo的路径
######     为什么要配置这个
    开启tomcat后，tomcat去寻找path里面对应的yigo项目的路径，单据打开后，点击register的按钮后，Yigo的项目是通过
    tomcat走Enhance.xml里面的服务Demo,去判断逻辑
```
    例如
        1.在tomcat根路径下创建一个work的文件夹
        2.在D:\Yigo2.0资料文档(变化)\Env-Demo\Tomcat 7.0_x64\conf\Catalina\localhost\path.xml进行配置
          path.xml
              <Context path="/yigo" reloadable="true" 
                    docBase="D:\yigoDemo\yigoTest" 
                    workDir="D:\yigoDemo\yigoTest\work" />
```
6.在后台Demo里去完成想要的逻辑，实现需求，我是想保存数据
```
    //获取参数
		String username = TypeConvertor.toString(paras.get(0));
		String password = TypeConvertor.toString(paras.get(1));
	/*	//将密码转化为高密码
		String privateKey = SysPara.getInstance().get("PrivateKey");
		String tmpPassword = RSAClientUtil.decryptByPrivate(privateKey, password);
		String publicKey = SysPara.getInstance().get("PublicKey");
		password = RSAMidUtil.encryptByPublic(publicKey, tmpPassword);
	*/
	//查询sql语句判断是都有值
		String sql = "select OID from register where username=?";
		
		IDBManager dbManager = context.getDBManager();
		DataTable table = dbManager.execPrepareQuery(sql, new Object[] { username });
		if (table.size() > 0) {
			throw new DemoException(0, "用户名已存在!");
		}
    //如果没有则创建一个documnet去完成保存
		VE ve = context.getVE();
		IMetaFactory metaFactory = ve.getMetaFactory();
		MetaDataObject dataObject = metaFactory.getDataObject("register");
		Document document = DocumentUtil.newDocument(dataObject);
		document.setNew();
		DataTable headTable = document.get("register");
		headTable.setString("username", username);
		headTable.setString("password", password);
		SaveData saveData = new SaveData(dataObject, null, document);
		saveData.save(context);
		return Boolean.valueOf(true);
	}
```
7.在页面点击按钮，测试是否完成需求
    输出台页面显示sql语句，代表成功，在打开mysql看数据是都有值
```
    select DelegateID from WF_Delegate where Enable=1  and AlwaysValid=0 and EndTime<?  and AutoRetreat=1#0:2017-10-25 15:16:03.755;
    select OID from register where username=?#0:bbb;
    select DelegateID from WF_Delegate where Enable=1  and AlwaysValid=0 and EndTime<?  and AutoRetreat=1#0:2017-10-25 15:17:03.756;
    select DelegateID from WF_Delegate where Enable=1  and AlwaysValid=0 and EndTime<?  and AutoRetreat=1#0:2017-10-25 15:19:03.763;
```
8.总结：还需要学习，我只是实现了一个微小的功能，简单的理解了一下逻辑