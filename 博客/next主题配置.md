[hexo文档](https://hexo.io/zh-cn/docs/index.html)

[next文档](https://theme-next.iissnan.com/getting-started.html)

### 如何更改字体？

[参考](https://io-oi.me/tech/noto-serif-sc-added-on-google-fonts/)

NexT 从 **5.0.1** 版本开始提供一个 [字体定制特性](https://theme-next.iissnan.com/theme-settings.html#fonts-customization)， 请先查看此特性是否能满足你的需求。以下的修改将覆盖 字体定制 的特性。 编辑主题下的 `source/css/_variables/custom.styl` 文件，新增两个变量：

```stylus
// 标题，修改成你期望的字体族
$font-family-headings = Georgia, sans

// 修改成你期望的字体族
$font-family-base = "Microsoft YaHei", Verdana, sans-serif

// 代码字体
$code-font-family = "Input Mono", "PT Mono", Consolas, Monaco, Menlo, monospace

// 正文字体的大小
$font-size-base = 16px

// 代码字体的大小
$code-font-size = 13px
```

#### 字体行高

themes/next/source/css/_variables/base.styl

```stylus
// Global line height
$line-height-base         = 1.6;
$line-height-code-block   = 1.3; // Can't be less than 1.3;
```



### 如何更改内容区域的宽度？

NexT 对于内容的宽度的设定如下：

- 700px，当屏幕宽度 < 1600px
- 900px，当屏幕宽度 >= 1600px
- 移动设备下，宽度自适应

如果你需要修改内容的宽度，同样需要编辑样式文件。 编辑主题的 `source/css/_variables/custom.styl` 文件，新增变量：

```stylus
// 修改成你期望的宽度
$content-desktop = 700px

// 当视窗超过 1600px 后的宽度
$content-desktop-large = 900px
```

### 文本居中的引用

此标签将生成一个带上下分割线的引用，同时引用内文本将自动居中。 文本居中时，多行文本若长度不等，视觉上会显得不对称，因此建议在引用单行文本的场景下使用。 例如作为文章开篇引用 或者 结束语之前的总结引用。

#### 使用方式

- HTML方式：使用这种方式时，给 `img` 添加属性 `class="blockquote-center"` 即可。
- 标签方式：使用 `centerquote` 或者 简写 `cq`。

此标签要求 NexT 的版本在 **0.4.5** 或以上。 若你正在使用的版本比较低，可以选择使用 `HTML` 方式。

 **标签调用方法**

```html
<!-- HTML方式: 直接在 Markdown 文件中编写 HTML 来调用 -->
<!-- 其中 class="blockquote-center" 是必须的 -->
<blockquote class="blockquote-center">blah blah blah</blockquote>

<!-- 标签 方式，要求版本在0.4.5或以上 -->
{% centerquote %}blah blah blah{% endcenterquote %}

<!-- 标签别名 -->
{% cq %} blah blah blah {% endcq %}
```



### 动态背景的设置

[参考](https://github.com/theme-next/theme-next-canvas-nest)

透明色：transparent

### 文章加密

找到`themes->next->layout->_partials->head.swig`文件，加入以下代码

按道理是添加在任何地方都行，但是推荐加在所有的``标签之后

```html
<script>
    (function(){
        if('{{ page.password }}'){
            if (prompt('请输入文章密码') !== '{{ page.password }}'){
                alert('密码错误！');
                history.back();
            }
        }
    })();
</script>
```

需要加密的markdown文章开头加上

```properties
password: password
```

### 鼠标点击小红心的设置

将 [love.js](https://github.com/Neveryu/Neveryu.github.io/blob/master/js/src/love.js) 文件添加到 hemes extsourcejssrc 文件目录下。
找到 hemes extlayout_layout.swing 文件， 在文件的后面， 标签之前 添加以下代码：

```html
<!-- 页面点击小红心 -->
<script type="text/javascript"   src="/js/src/love.js"></script>
```

### 自定义hexo new生成md文件的选项

在/scaffolds/post.md文件中添加：

```markdown
---
title: {{ title }}
date: {{ date }}
tags:
categories: 
copyright: true
permalink: 01
top: 0
password:
---
```

### 隐藏网页底部powered By Hexo / 强力驱动

打开themes/next/layout/_partials/footer.swig,使用””隐藏之间的代码即可，或者直接删除。

```html
<!--
<div class="powered-by">
  {{ __(footer.powered, <a class="theme-link" rel="external nofollow" href="https://hexo.io">Hexo</a>) }}</div>
<div class="theme-info">
  {{ __(footer.theme) }}-
  <a class="theme-link" rel="external nofollow" href="https://github.com/iissnan/hexo-theme-next">
    NexT.{{ theme.scheme }}  </a>
</div>
-->
```



### 一些命令的说明

命令hexo g之后，会把source文件里的.md格式的文件渲染为html文件并放到public下面；
命令hexo d之后，会把public下面的所有文件提交到对应的XXX.github.io这个仓库；



### 使用阿里图标库

先添加到购物车，新建项目

使用font class下载到本地

解压找到iconfont.css文件

复制其代码到blog/source/_data/styles.styl中

在next主题配置文件中取消下面的注释

```yml
custom_file_path:
  #head: source/_data/head.swig
  #header: source/_data/header.swig
  #sidebar: source/_data/sidebar.swig
  #postMeta: source/_data/post-meta.swig
  #postBodyEnd: source/_data/post-body-end.swig
  #footer: source/_data/footer.swig
  #bodyEnd: source/_data/body-end.swig
  #variable: source/_data/variables.styl
  #mixin: source/_data/mixins.styl
  style: source/_data/styles.styl  #取消这里的注释 
```

稍微修改styles.styl里面的代码

示例

```stylus
@font-face {font-family: "iconfont";
  src: url('iconfont.eot?t=1576244608406'); /* IE9 */
  src: url('iconfont.eot?t=1576244608406#iefix') format('embedded-opentype'), /* IE6-IE8 */
  url('data:application/x-font-woff2;charset=utf-8;base64,d09GMgABAAAAAAUMAAsAAAAACeAAAATAAAEAAAAAAAAAAAAAAAAAAAAAAAAAAAAAHEIGVgCDBgqHQIVbATYCJAMMCwgABCAFhG0HPRsoCCMRdnNvQpH9M8Gm87RQMu0pGFHV611amw5Gxfo+PzHyykqLpN8ET99Y+7Mzt6fSIIlGiKaWCF0TlK4heqWR8M4jkm/498xXKKlS8ZScxLWD/LnlNjebgx5cDAACYHPNkerNiAj1pHZTQA7QPqb4mGbk81xOm88Dmx8olzGX7YlHvQDjgAIda1CU3QFWIIFKTC3Da12qw94NASKUUod0dA1MEMIgWoGyg93tVUIJG1bBEUJGXzDTkAsQIOTO2GcAnHufl19iJASOgEJGjgzt3KDl2eLZSfH/OAsODciH8wD8PFCgDjCQpULfDNAgUYcSUXVWne8xBDdUuPjfk2kaT//wwCEgHqKLYXaV1h2U3JJF+gpQHLX84l73QBKkAWR1AIpAGN9o3vcSwg2XIGKZPETpM8qD2TzGJvRqN2q71t6kpnBVHUxQuh44iZEa2tW8AttmoWnois1BqpwrtqwyxC5zOYy96hkYHsKRsrtBDSjFDKIoL6O1iR00q/AEGUEBdgMJWThitp1U7UjZZWUYgqJwteXWMoyZq9U2EXo1FddT7HpbgXNNwI4UO+7SnKqgV6vPllPqgRWSlpQsImkNzWhPlxIDrj2zXX9uv/q025UnPY8Wbj9/PYT2YB5HgAN0azJa69UooXZ3BJEuiLTR0LE8V5CrZScaT1EqUuWCtiWY1EcLvYHeNsxDNfRwa3tRNzqoDrbXw15ipa5OB3f5X7d5joyuSy4H5jHQbdrVV+OtHuxYkWuNzrn2JEwkEoy2qatkbYstMri+wzAkYWjStYQFo5q//P7Iuw+lviINAFyVNwIc/zh86wP5bHeIG1oA8DOEZFD+8/HPu+koYVUEH9wTr5R+6nx2eN7haWh4LIKvWQiFrEGagj/lcpWrlKAAWYXAFtmLWLsUdOy32L/xNO3LCw7h7oIahsOq4DtLx5/ppuCJED9fiBdbcPPAqXx/ntEvRsmP7TlBenGK1OgeN+CG5Rk+wFeZnOU3R5+W81098YqWlxGjxVjg//SbXB8f7k0QOxJbFf2mE84Efz+npCB8/Xt/Mn6pUOhPRIRUCqr6izt2XNLsfYVsqFvkw3bM/TxDhky1rVomHlJXxUFi43K7QhMetfVNf2cfquQONNzCl7YPmMTzRzYmSOaKRMA/1wmLyEoSa+N4nZ3hx88LHtpjDM2fWdlxs33F8uuuGX01ez/HhcsdVjSHkyNyWgBA8a+OUtFv9h0eVjdn02Tafwg2AN7023CPV8QNaBEhHDSW/1Tyhg2mKYldTZFZMiOtXBN+ZRKQCEGKgIQyfHUP5CHyCCFBDnAEKYESooxu7DoEiFIPT4hmRKile36ULIdAEQsDNRwhCClu4UhwByXFC93YnwhQ4A+elCgiTLGUK0apkKtGbAlzxgqKP5iya6XpZOGX31C965ziSo/7QvImDHEQpdNHbJGGGOM/KmGWIKlr4ID2w7ruoKfOYsmBZu6XYSiLHhSUXTMSoy1hzlhB8QdTdq10WrPIvP+G6l3nVEGoir6QvOkc4iBqQD/q2kaES7nHf1TCLEFS18ABebCmiQ764m4WSw50h1i/DHEz2VQXTG9o7m8bEBmRiTp2omLMt6e0MLWx6jaz0lftaAQ=') format('woff2'),
  url('iconfont.woff?t=1576244608406') format('woff'),
  url('iconfont.ttf?t=1576244608406') format('truetype'), /* chrome, firefox, opera, Safari, Android, iOS 4.2+ */
  url('iconfont.svg?t=1576244608406#iconfont') format('svg'); /* iOS 4.1- */
}

.fa-custom {
  font-family: "iconfont" !important;
  font-size: 16px;
  font-style: normal;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.bilibili:before {
  content: "\e64e";
  font-size: 20px; //可以调整大小
}

.csdn:before {
  content: "\e638";
}
```

最后在这里引用即可

```yml
social:
  GitHub: https://github.com/BluePrintYang || github
  E-Mail: mailto:yll1671158@163.com || envelope
  CSDN: https://blog.csdn.net/bluepyang || custom csdn
  Bilibili: https://space.bilibili.com/130954022 || custom bilibili
```



### 待办

- [x] 文章字体修改，布局
- [x] 标签云
- [x] 支持更多markdown语法
- [x] 自定义icon

:star:

2^2^

H~2~O
$$
2+2=4
$$
