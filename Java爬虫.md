---

---

### Jsoup 方式提取信息

 [学习链接](https://blog.csdn.net/z694644032/article/details/102363914)

Jsoup 的依赖

```xml
<dependency>
    <groupId>org.jsoup</groupId>
    <artifactId>jsoup</artifactId>
    <version>1.12.1</version>
</dependency>
```

 在列表页中，我们利用 F12 审查元素查看页面结构，经过我们分析发现列表新闻在  <div class="news-list">‘’标签下，每一条新闻都是一个`li`标签 

 结合浏览器的 Copy 功能，编写出我们 `a`标签的 css 选择器代码：`div.news-list > ul > li > div.list-hd > h4 > a` 

```Java
/**
 * jsoup方式 获取虎扑新闻列表页
 * @param url 虎扑新闻列表页url
 */
public void jsoupList(String url){
    try {
        Document document = Jsoup.connect(url).get();
        // 使用 css选择器 提取列表新闻 a 标签
        // <a href="https://voice.hupu.com/nba/2484553.html" target="_blank">霍华德：夏休期内曾节食30天，这考验了我的身心</a>
        Elements elements = document.select("div.news-list > ul > li > div.list-hd > h4 > a");
        for (Element element:elements){
//                System.out.println(element);
            // 获取详情页链接
            String d_url = element.attr("href");
            // 获取标题
            String title = element.ownText();

            System.out.println("详情页链接："+d_url+" ,详情页标题："+title);

        }
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

```Java
public static void main(String[] args) {
    String url = "https://voice.hupu.com/nba";
    CrawlerBase crawlerBase = new CrawlerBase();
    crawlerBase.jsoupList(url);
}
```

### httpclient + 正则表达式

>  httpclient + 正则表达式的方式涉及的知识点还是蛮多的，正则表达式、Java 正则表达式、httpclient 

依赖

```xml
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <version>4.5.10</version>
</dependency>
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpcore</artifactId>
    <version>4.4.10</version>
</dependency>
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpmime</artifactId>
    <version>4.5.10</version>
</dependency>
```

我们需要找到能够代表列表新闻的结构体，比如：<div class="list-hd"> <h4> <a href="https://voice.hupu.com/nba/2485508.html" target="_blank">直上云霄！魔术官方社媒晒富尔茨扣篮炫酷特效图</a></h4></div>这段结构体，每个列表新闻只有链接和标题不一样，其他的都一样，而且 <div class="list-hd">是列表新闻特有的。

最好不要直接正则匹配 a标签，因为 a标签在其他地方也有，这样我们就还需要做其他的处理，增加我们的难度。现在我们了解了正则结构体的选择，我们一起来看看 httpclient + 正则表达式方式提取的代码：


```Java
/**
 * httpclient + 正则表达式 获取虎扑新闻列表页
 * @param url 虎扑新闻列表页url
 */
public void httpClientList(String url){
    try {
        CloseableHttpClient httpclient = HttpClients.createDefault();
        HttpGet httpGet = new HttpGet(url);
        CloseableHttpResponse response = httpclient.execute(httpGet);
        if (response.getStatusLine().getStatusCode() == 200) {
            HttpEntity entity = response.getEntity();
            String body = EntityUtils.toString(entity,"utf-8");
   
            if (body!=null) {
                 /*
                 * 替换掉换行符、制表符、回车符，去掉这些符号，正则表示写起来更简单一些
                 * 只有空格符号和其他正常字体
                 */
                Pattern p = Pattern.compile("\t|\r|\n");
                Matcher m = p.matcher(body);
                body = m.replaceAll("");
                /*
                 * 提取列表页的正则表达式
                 * 去除换行符之后的 li
                 * <div class="list-hd">                                    <h4>                                        <a href="https://voice.hupu.com/nba/2485167.html"  target="_blank">与球迷亲切互动！凯尔特人官方晒球队开放训练日照片</a>                                    </h4>                                </div>
                 */
                Pattern pattern = Pattern
                        .compile("<div class=\"list-hd\">\\s* <h4>\\s* <a href=\"(.*?)\"\\s* target=\"_blank\">(.*?)</a>\\s* </h4>\\s* </div>" );

                Matcher matcher = pattern.matcher(body);
                // 匹配出所有符合正则表达式的数据
                while (matcher.find()){
//                        String info = matcher.group(0);
//                        System.out.println(info);
                    // 提取出链接和标题
                    System.out.println("详情页链接："+matcher.group(1)+" ,详情页标题："+matcher.group(2));
                }
            }else {
                System.out.println("处理失败！！！获取正文内容为空");
            }
        } else {
            System.out.println("处理失败！！！返回状态码：" + response.getStatusLine().getStatusCode());
        }
    }catch (Exception e){
        e.printStackTrace();
    }

}
```

```Java
public static void main(String[] args) {
    String url = "https://voice.hupu.com/nba";
    CrawlerBase crawlerBase = new CrawlerBase();
//        crawlerBase.jsoupList(url);
    crawlerBase.httpClientList(url);
}
```

