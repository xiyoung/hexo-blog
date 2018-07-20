## [传送门](https://blog.xixiyoung.com/)

> 借用[Hexo](https://hexo.io/zh-cn/docs/)和[Next](http://theme-next.iissnan.com/)搭建的个人博客，代码库放在[Github Pages](https://pages.github.com/)并绑定了自己申请的域名，欢迎来踩~

#### 基础功能

- 基本配置（`_config.yml`文件配置）
- 设置网站icon
- 侧边栏Home、About、Tags、Categories、Archives、Rss、links等模块
- 底部显示建站时间和图标的修改
- 设置第三方JS库
- [来必力](https://livere.com/)评论系统
- [不蒜子](http://busuanzi.ibruce.info/)统计访客量、文章阅读量
- 文章字数、文章阅读时长、网站总字数
- 站内搜索功能
- [DaoVoice](http://www.daovoice.io/)在线联系

#### 个性化功能

- 设置字体
- 设置代码高亮主题
- 设置小型代码块颜色
- 左上角的Github样式
- 添加顶部加载条
- 点击出现小爱心效果
- 修改文章链接文本样式
- 修改文章底部标签样式
- 文章末尾统一添加“文本结束”标记
- 版权信息
- 文章置顶并显示置顶图标

#### 有待完善功能

- 打赏
- 浮动宠物
- 文章分享
- SEO(搜搜索引擎优化)
- ......

#### 如何部署

```javascript
//假如你已经配置好了node和git环境
$ git clone https://github.com/xiyoung/hexo-blog.git
$ cd hexo-blog
$ npm install
$ npm install -g hexo-cli
$ hexo server
访问：http://localhost:4000/
```

#### 注意

由于[来必力](https://livere.com/)评论系统和[DaoVoice](http://www.daovoice.io/)在线联系需要到他们各自平台注册，然后生成对应的id

所以贴上二者的正确获取姿势：

- 来必力： 

  1. 登陆 [来必力](https://link.jianshu.com/?t=https%3A%2F%2Flivere.com%2F) 注册获取，这里要注意，这个韩国的系统注册啥的真的太慢了，所以要记住不要耐不住关闭页面或者狂刷新，耐心等待就好。
  2. 注册后点击导航上边的安装，选择免费的city版本，点击现在安装后填入网站的一些信息就可以获取到安装代码，**第一行div中的data-uid属性值**就是你的来必力 id。
  3. 复制上边的id，在**主题配置文件**(/themes/_config.yml)里面搜索`livere_uid`，替换掉笔者的来必力id即可。另外可以点击用户头像进入管理界面个性化你的评论系统。

- DaoVoice：

  1. 首先去[注册](https://link.jianshu.com/?t=http%3A%2F%2Fdashboard.daovoice.io%2F)，这里需要[邀请码](https://link.jianshu.com/?t=http%3A%2F%2Fdashboard.daovoice.io%2Fget-started%3Finvite_code%3D5ea4435c)，贴一个`5ea4435c`，或者直接点击邀请码的链接。注册后就可以查看你的`app_id`
  2. 复制app_id，在**主题配置文件**(/themes/_config.yml)里面搜索`daovoice_app_id`，替换掉笔者的daovoice_app_id即可。具体样式设计可以在 应用设置-->聊天设置 后边改。 
