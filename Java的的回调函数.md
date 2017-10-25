###  Java的的回调函数：
    首先创建一个回调对象，然后再创建一个控制器对象，将回调对象需要被调用的方法告诉控制器对象
    控制器对象负责检查某个场景是否出现或某个条件是否满足。当此场景出现或此条件满足时，自动调用回调对象的方法。
#####  例子：
    假设你公司的总经理出差前需要你帮他办件事情，这件事情你需要花些时间去做，	
		这时候总经理肯定不能守着你做完再出差吧，于是就他告诉你他的手机号码叫你如果事情办完了你就打电话告诉他一声；
		总经理出差前要你去办件事情，这件事情通过doSomething告诉你了，
		事情要是办完了就打总经理的电话phoneCall通知他一声。
		这里的phoneCall我们就称为回调方法，
		Manager—–>Personnel—–>Manager
#####    代码：
        // 回调接口
        public interface CallBack {
       	    	// 回调方法 
            	public void backResult(String result);
	}
       
       //该类用来模拟总经理
        public class Manager implements CallBack {
        	//传入一个员工类的对象
            	public Manager(Personnel personnel) {
          	  // 想让该让员工做什么 
         	  personnel.doSomething(this, "整理公司文件");
		 }
	}
        
	//当员工做完总经理让他做的事后就通过该方法通知总经理 
	 //事情结果
	 public void backResult(String result) { 
	 	 System.out.println("事情" + result);
		 }
	  }
	
	//该类用来模拟员工 
	  public class Personnel {
	  	public void doSomething(CallBack callBack, String task) {
		// 总经理通过doSomething方法告诉员工要做什么 
		System.out.println("总经理要你做" + task); 
		String result = "做完了";
		// 当事情做完了我们就通过总经理公布的phoneCall方法通知总经理结果  
		callBack.backResult(result);
	        }
	  }
	
	//测试方法
	  public class Main {
	  	public static void main(String[] args) {
			// 首先我们需要一个员工 
			Personnel personnel = new Personnel();
			// 其次把这个员工指派给总经理  
			 new Manager(personnel); 
			 }
		}