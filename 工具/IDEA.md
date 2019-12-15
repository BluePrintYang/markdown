## IDEA中配置MySQL出现Server returns invalid timezone问题解决方法

### 原因：

首先，出现该问题的原因是MySQL驱动jar中的默认时区是UTC。

UTC代表的是全球标准时间 ，但是我们使用的时间是北京时区也就是东八区，领先UTC八个小时。

因为时区不一致，所以提示Server returns invalid timezone. Go to 'Advanced' tab and set 'serverTimezone' property manually
服务器返回了无效的时区，去“高级”标签中手工设置“serverTimezon"属性值。

### 解决方案：

去“高级”标签中手工设置“serverTimezon"属性值为Asia/Shanghai



## 添加.gitignore并删除无用的配置文件后push到GitHub

创建.ignore

```bash
type nul>.gitignore  #windows下创建名为.gitignore的空文件
```

编辑.gitignore

```
/target/
.idea
*.iml
```

清理git缓存

```bash
git rm -r --cached .idea
git rm -r --cached *.iml
git commit -m "删除无用配置文件"
```

VSC>Git>Push