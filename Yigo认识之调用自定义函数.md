## Yigo认识之调用自定义函数

##### Yigo自定义函数
1. 
    在yigo认识的时候创建函数是用了平台自己的函数InvokeService(),然后配置Enhance.xml,通过中间服务层做逻辑业务处理，这次我们将InvokeService()改成自
    己编写的函数名字
    ```
        RegisterDemo("RegisterDemo",username,password)
        备注：RegisterDemo可以随意编写，但是第一个参数是对应服务名称
    ```
2.Yigo认识里面是直接就经过了后台就是中间服务层，现在不行，因为直接经过后台，后台不认识自己编写的代码，现在我们就用到客户端二次开发。因为这里的配置我不太了解，就用别人已经弄好的项目拿过了修改。
    创建一个YigoApp二开坏境项目里面我们主要用到两个包，开启服务的包和客户端的包
    
    ```
        com.erp.uifunction                  //这个包是客户端二次开发的包
            Uifunction                      //客户端具体注册和实现的
            UifunctionProvider              //客户端接口实现类和用户初始化
        start                               //开启服务
            DevSuite                        //开启yigo服务的类
            
        在yigoAPP里面还需要配置一个就是项目的地方。不配置这个,开启哪个项目也不知道
        setting
            app.properties
                SolutionPath=D:\\YigoTest\\register        //指定我yigo项目的路径
    ```
3.配置好这个，在yigo点击的时候，也不会自动去找客户端二开的这个类呀。还需要在Enhance.xml文件里面配置
  
    ```
        <?xml version="1.0" encoding="UTF-8" standalone="no"?>
        <Enhance>
             <ExtService>
                 <Service Name="RegisterDemo" Description="注册"
                      Impl="com.bokesoft.yigo.pc.RegisterDemo"/>
                      备注：RegisterDemo采用的Yigo认识里面的
             </ExtService>
             <ExtUIFunction>
	            <UIFunction Description="服务二次开发" 
	                Provider="com.erp.uifunction.UifunctionProvider"/>
             </ExtUIFunction>
        </Enhance>	
    ```
4.这样配置下来，可以看在yigo客户端写二次开发需要的信息
   
    ```
    //服务端二次开发
        public class Uifunction extends BaseFunImplCluster {
            	public Uifunction() {
		   super();
	    }
	@Override
	public Object[][] getImplTable() {
	    //这里registerDemo对应的yigo按钮的点击事件写的事件名称
		return new Object[][] { { "RegisterDemo", new RegisterDemo() } };//RegisterDemo这个是界面上调用的函数名字
	}
	private class RegisterDemo extends BaseViewFunctionImpl {
		//完成客户端需求的内容
		@Override
		public Object evalImpl(String name, ViewEvalContext context, Object[] args, IExecutor executor) throws Throwable {
			IForm form = context.getForm();
			IServiceProxy proxy = ServiceProxyFactory.getInstance().newProxy(form.getVE());
			String serviceName = TypeConvertor.toString(args[0]);
			ArrayList<Object> paras = new ArrayList<Object>();
			//获取参数
			String username =TypeConvertor.toString(args[1]);
			String password = TypeConvertor.toString(args[2]);
			paras.add(username);
			paras.add(password);
			proxy.invokeService(serviceName, null,paras);   //serviceName是对应service服务的名称
			return true;
		    }
	    }
    }
    ```
5.这样开启的话，是找不到昨天我写的项目RegisterDemo的。要把两者做关联，
    一般的做法是将RegisterDemo打成jar包放入到YigoApp的lib下，开启。
    我今天做的是将RegisterDemo引入到我的YigoApp下的，这样开启服务，最后在yigo输入数据可以保存导数据库里了。