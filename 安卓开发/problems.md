#### 不清楚两个读卡数据之间的关系

数据1：RFID *05958C94#    十位十进制数  2492241157

数据2：RFID *89FA709E#     十位十进制数 2658204297

2658204297 转16进制 9e 70 fa 89 

厉害厉害

10进制转16进制之后按字节逆序读入

我来转回去

```Java
BigInteger s10 = new BigInteger("2492241157",10);
String s16 = s10.toString(16);
StringBuilder to10 = new StringBuilder();
for (int i=s16.length()-1;i>=0;i-=2){
    to10.append(s16.charAt(i-1));
    to10.append(s16.charAt(i));
}
System.out.println("十进制:"+s10);
System.out.println("十六进制:"+s16);
System.out.println("转换后:"+to10);
```

出现BigInteger的格式错误，不知道为什么总是有个读空字符串转BigInteger

增加一个非空判断后问题解决

找到原因了，那个if语句后面没有大括号，是只跟了一条的。。。



##### BigInteger与int转换    

```Java
//BigInteger与int转换        
//int 转换为 BigInteger的方法：
int p = 1;
BigInteger a = BigInteger.valueOf(p);
       
          
//BigInteger 转换为 int的方法：      
BigInteger d = new BigInteger("9");
int temp = d.intValue();
```



##### String与int的转换

```Java
//String与int的转换
//String转int
String s = "123";
int i  = Integer.parseInt(s);

//int转String
int i = 123;
String s = i+"";
```



##### 10进制转16进制

```Java
//10进制转16进制
int i = 324;
String hex = Integer.toHexString(i);
```



##### 大小写转换

```Java
String a = "ABC";
system.out.println(a.toLowerCase());//abc
String b = "abc";
system.out.println(b.toUpperCase());//ABC
```





#### 通过url获取json数据，在一般的Java类中运行正常，在安卓主界面却不行



通过url获取json代码

```Java
public static String loadJson (String urlStr) {
    //请求的url
    URL url = null;
    //建立的http链接
    HttpURLConnection httpConn = null;
    //请求的输入流
    BufferedReader in = null;
    //输入流的缓冲
    StringBuilder sb = new StringBuilder();
    try{
        url = new URL(urlStr);
        in = new BufferedReader(new InputStreamReader(url.openStream(), "UTF-8") );
        String str = null;
        //一行一行进行读入
        while((str = in.readLine()) != null) {
            sb.append( str );
        }
    } catch (Exception ignored) {
    } finally{
        try{
            if(in!=null) {
                in.close(); //关闭流
            }
        }catch(IOException ignored) {
        }
    }
    return sb.toString();
}
```

调用

```Java
 String url =  getPropertiesUtil.getNetConfigProperties().getProperty("SERVER_URL") + "CheckCardIdentity?RFID=" + RFID;
String jsonString = GetData.loadJson(url);//调用上面的函数
System.out.println("请求的url:" + url);
System.out.println("user的json字符串" + jsonString);
JSONObject user = JSON.parseObject(jsonString);
System.out.println("user的json格式" + user);
System.out.println(user.get("userName"));
//展示读卡的数据
System.out.println("data:" + user.get("userName"));
```

会访问不到json数据

解决方法：创建一个线程，让线程访问

```Java
final Thread getUserInfo = new Thread(new Runnable() 
    @Override
    public void run() {
    	try {
        	//通过卡号查询用户信息的url
            String url =  getPropertiesUtil.getNetConfigProperties().getProperty("SERVER_URL") + "CheckCardIdentity?RFID=" + RFID;
            String jsonString = GetData.loadJson(url);
            JSONObject user = JSON.parseObject(jsonString);
         }catch (Exception e){
         	System.out.println("Have Exception:"+e.getMessage());
         }
	}
});
//启动获取用户信息的线程
getUserInfo.start();
```

还有一个问题

#### 线程不能调用那个弹窗的方法