一些谈经验的博客

[如何正确使用开源项目？](http://mp.weixin.qq.com/s?__biz=MzA4NTQwNDcyMA==&mid=2650661623&idx=1&sn=ab28ac6587e8a5ef1241be7870851355#rd)

- 不要轻易修改开源项目的源码，可以拓展
- 对于开源项目，请一定封装一层，方便后期替换
- 使用Gradle远程依赖

[Gradle依赖的统一管理 ](http://mp.weixin.qq.com/s?__biz=MzA4NTQwNDcyMA==&mid=402733201&idx=1&sn=052e12818fe937e28ef08331535a179e&scene=21#wechat_redirect)

- 在一个配置文件中管理依赖的版本


[开篇介绍和工程目录结构【从零开始搭建android框架系列（1）】](http://www.jianshu.com/p/d0fee882a0fe)

- 把多个library放到一个文件夹中，然后指定路径

    // setting.gradle

    include':app', ':volley'
    
    project(:volley).projectDir = new File(ThirdPart/volley)

[从零开始的Android新项目2 - Gradle篇](http://www.jianshu.com/p/edd495d8efc8)

- gradle的配置
 

[网络图片加载的封装【从零开始搭建android框架系列（4）】](http://www.jianshu.com/p/e26130a93289)

-  对图片加载框架进行封装一层，ImageLoad使用了策略和Builder设计模式