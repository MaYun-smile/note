##  接口和Class.forName

#### 接口 (interface)
    接口，英文称作interface，在软件工程中，接口泛指供别人调用的方法或者函数
    Java接口是一系列方法的声明，是一些方法特征的集合，一个接口只有方法的特征没有方法的实现，因此这些方法可以在不同的地方被不同的
    类实现，而这些实现可以具有不同的行为
```
//定义普通的接口
public interface Common{
    //可以定义方法，但不能实现化
    void read();
    //定义变量,接口里面变量定义public static final
    public static final int i=10;
}
//定义实现类
    public class Car implements Common {
    //接口实现implements，一定重写接口里所有的方法
    @Override
	public void read() {
	}
}
```
#### 接口的使用
#####   1、 由于接口里面存在抽象方法，所以接口对象不能直接使用关键字new进行实例化
    （1）接口必须要有子类，但此时一个子类可以使用implements关键字实现多个接口；
    （2）接口的子类（如果不是抽象类），那么必须要覆写接口中的全部抽象方法； 
    （3）接口的对象可以利用子类对象的向上转型进行实例化
    
    
```
class X implements A,B{//X类实现了A和B两个接口
```

#####    2、对于子类而言，除了实现接口外，还可以继承抽象类 

```
class 子类 [extends 父类] [implemetns 接口1,接口2,...] {}
```
##### 3、一个抽象类只能继承一个抽象类，但一个接口却可以使用extends关键字同时继承多个接口

```
interface C extends A,B{//使用的是extends
```
#### 接口拓展
    接口的替换方法，假设有个项目，你已经完成，之后客户觉得需求不够他们现在的需求，要求程序修改代码。例如，现在有这样的一个项目：
    已知每种交通工具的参数都是3个整数A、B、C的表达式。
    现有两种工具：Car 和Plane，其中Car 的速度运算公式为：A*B/C，
                  Plane的速度运算公式为：A+B+C。
                  需要编写三类：ComputeTime.java,Plane.java,Car.java和接口Common.java，要求在未来如果增加第3种交通工具的时候，不必修改以前的任何程序，
                  只需要编写新的交通工具的程序。其运行过程如下，
                  从命令行输入ComputeTime的四个参数，第一个是交通工具的类型，第二、三、四个参数分别时整数A、B、C，
                  举例：
                  计算Plane的时间："java ComputeTime Plane 20 30 40" 
                  计算Car007的时间："java ComputeTime Car 23 34 45" 
                  如果第3种交通工具为Ship,则只需要编写Ship.java，运行时输入："java ComputeTime Ship 22 33 44" 
##### 代码：

```
//通用接口
public interface Common {
    //定义接口里的公共方法
	 public int jisuan(int a,int b,int c);
}
//定义实现类car汽车类
public class Car implements Common {
	public Car() {
		System.out.println("这是个Car");
	}
	@Override
	public int jisuan(int a, int b, int c) {
		// TODO 自动生成的方法存根
		return  a*b/c;
	}
}
//定义实现类Plane飞机类
public class Plane implements Common {
	public Plane() {
		System.out.println("Plane");
	}
	@Override
	public int jisuan(int a, int b, int c) {
		// TODO 自动生成的方法存根
		return a+b+c;
	}
}
//执行类
public class ComputeTime {
	 public ComputeTime(String o2,int a,int b,int c){
		  try {//调用经典
			  String str="com.yun.demo.Car";
			  Common o=(Common)Class.forName(str).newInstance();	
			 //Common o1=(Common)Class.forName("com.yun.demo."+o2).newInstance();	   
		   System.out.println(o.jisuan(a, b, c));   
		  } catch (Exception e) {
			  System.out.println(e.toString());
		  } 
		 }
		 public static void main(String[] args) {
		 // new ComputeTime(20,24,3);
		  new ComputeTime("Car",20,24,3);
		//  new ComputeTime("Plane",1,2,3);
		 }
}
输出内容：

这是个Car
160
```
    

    


    
