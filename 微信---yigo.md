## 微信---yigo
#### 需求:yigo创建界面,从yigo发送一句话给关注过微信公众号的用户消息
#####  ==注意==:个人查资料:如果想主动发消息给用户,可能服务号可以做,但是目前还没有服务号还没有提供接口,如果想推送消息可以通过模板推送.企业号有接口可以发送消息给指定的用户.
#### 讲讲实现
##### 一.首先微信公众平台功能-->模板消息,加模板消息,获取模板ID.
##### 二.看过一遍文档之后,发送模板消息的时候,需要这个参数:
```
        {
           "touser":"OPENID",
           "template_id":"模板id",
           "url":"http://weixin.qq.com/download",  
           "miniprogram":{
             "appid":"xiaochengxuappid12345",
             "pagepath":"index?foo=bar"
           },          
           "data":{
                   "first": {
                       "value":"恭喜你购买成功！",
                       "color":"#173177"
                   },
                   "keynote1":{
                       "value":"巧克力",
                       "color":"#173177"
                   },
                   "keynote2": {
                       "value":"39.8元",
                       "color":"#173177"
                   },
                   "keynote3": {
                       "value":"2014年9月22日",
                       "color":"#173177"
                   },
                   "remark":{
                       "value":"欢迎再次购买！",
                       "color":"#173177"
                   }
           }
       }
        Apppid--->开发者id为了获取access_token调用接口用的
        touser就是 openid-->关注微信后,可以用过openid发送指定用户的消息
        template_id就是上面的模板ID
```
##### 三.post 请求提交url
        url="https://api.weixin.qq.com/cgi-bin/message/template/send?access_token=ACCESS_TOKEN";  
        我们要通过外部请求post.
        我们要定义个一post请求:
    ```
         public static JSONObject httpsRequest(String requestUrl, String requestMethod, String outputStr) {
	        JSONObject jsonObject = null;
	        try {
	            URL url = new URL(requestUrl);
	            HttpsURLConnection conn = (HttpsURLConnection) url.openConnection();
	         
	            conn.setDoOutput(true);
	            conn.setDoInput(true);
	            conn.setUseCaches(false);
	            // 设置请求方式（GET/POST）
	            conn.setRequestMethod(requestMethod);

	            // 当outputStr不为null时向输出流写数据
	            if (null != outputStr) {
	                OutputStream outputStream = conn.getOutputStream();
	                // 注意编码格式
	                outputStream.write(outputStr.getBytes("UTF-8"));
	                outputStream.close();
	            }

	            // 从输入流读取返回内容
	            InputStream inputStream = conn.getInputStream();
	            InputStreamReader inputStreamReader = new InputStreamReader(inputStream, "utf-8");
	            BufferedReader bufferedReader = new BufferedReader(inputStreamReader);
	            String str = null;
	            StringBuffer buffer = new StringBuffer();
	            while ((str = bufferedReader.readLine()) != null) {
	                buffer.append(str);
	            }

	            // 释放资源
	            bufferedReader.close();
	            inputStreamReader.close();
	            inputStream.close();
	            inputStream = null;
	            conn.disconnect();
	            
	            jsonObject = JSONObject.fromObject(buffer.toString());
	        }catch (Exception e) {
	         System.out.println("请求异常:"+e.getMessage());
	        }
	        return jsonObject;
	    }
    ```
##### 四.发送模板信息方法
```
    private final static String SEND_TEMPLAYE_MESSAGE_URL = "https://api.weixin.qq.com/cgi-bin/message/template/send?
    access_token=ACCESS_TOKEN";  
	public static void sendTemplateMessage(String accessToken, WechatTemplate wechatTemplate) {      
	    String jsonString = new Gson().toJson(wechatTemplate).toString();     
	    String requestUrl = SEND_TEMPLAYE_MESSAGE_URL.replace("ACCESS_TOKEN", accessToken);   
	    JSONObject jsonObject = Oauth2.httpsRequest(requestUrl, "POST", jsonString);   
	  //  logger.info("jsonObject="+jsonObject); 
	    System.out.println(jsonObject);
	    if (null != jsonObject) {  
	        int errorCode = jsonObject.getInt("errcode");         
	        if (0 == errorCode) {  
	           System.out.println("模板消息发送成功");  
	        } else {  
	            String errorMsg = jsonObject.getString("errmsg");  
	            System.out.println("模板消息发送失败,错误是 "+errorCode+",错误信息是"+ errorMsg);  
	        }  
	    }  
	
```
##### 五.编辑模板消息
```
    
		String accessToken = Utils.getAccessToken();
		System.out.println("为模板消息接口获取的accessToken是"+accessToken);  
		WechatTemplate wechatTemplate = new WechatTemplate();  
		wechatTemplate.setTemplate_id("jdco30Br3PHwGO0VlFXWiFvwjW_UZL4LZOTCiMJEB6w");  
		wechatTemplate.setTouser(openid);  //获得要发送的用户openid 
		  
		Map<String,TemplateData> m = new HashMap<String,TemplateData>();  
		TemplateData first = new TemplateData();  
		first.setColor("#000000");    
		first.setValue(news);           //要发送的消息
		m.put("first", first); 
		
		
	/*  根据需要去编写
	    TemplateData keyword1 = new TemplateData();    
		keyword1.setColor("#000000");    
		keyword1.setValue("2017.1.2");    
		m.put("keyword1", keyword1);  
		  
		TemplateData keyword2 = new TemplateData();    
		keyword2.setColor("#000000");    
		keyword2.setValue("5");    
		m.put("keyword2", keyword2);  
		  
		TemplateData remark = new TemplateData();    
		remark.setColor("#000000");    
		remark.setValue(news);    
		m.put("remark", remark);
	*/	
		wechatTemplate.setData(m);          
		try {  
		    Utils.sendTemplateMessage(accessToken, wechatTemplate);  
		} catch (Exception e) {  
		    System.out.println("异常"+e.getMessage());  
		}   

```
