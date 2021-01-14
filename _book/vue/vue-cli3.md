vue-cli3

**创建项目**

```bash
vue create test //test是项目名
```



![1570620394501](..\img\1570620394501.png)

选择Mannally，回车

![1570620421331](..\img\1570620421331.png)

选择basel和router(空格选择)，回车确认

![1570620447676](..\img\1570620447676.png)

使用history mode

![1570620517332](..\img\1570620517332.png)

选择packa.json

![1570620545505](..\img\1570620545505.png)

后面直接回车等待项目创建完成

```
npm run serve//启动项目
```



**安装axios**

```
cnpm i axios -S
```

在main.js文件中添加以下代码

```
//导入
import axios from 'axios'
//全局注册
Vue.prototype.$http=axios;
```

