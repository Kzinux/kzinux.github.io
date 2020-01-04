---
layout: post_layout
title: Jekyll博客的分页和摘要
time: 2020年1月4日 星期六
location: 中国
pulished: true
excerpt_separator: "<!--more-->"
---
Github的博客写了也两年多了，虽然Jekyll立志于小巧简洁，不过文章多了连分页也没有太不方便，加载也慢。

搜索了下分页和摘要都是Jekyll默认支持的，网上内容很多，步骤也简单。

#### **分页**

1. 首先编辑_config.yml，在最下方添加下面两行设置，paginate代表每页显示的文章数目，path是后面要用到的不用管。
   ```
   paginate: 5
   paginate_path: "page:num"
   ```
2. 把index.html替换成下面的内容。github显示不全html代码，放个[index.html](https://github.com/Kzinux/kzinux.github.io/blob/master/index.html)链接。
  <!--more-->
  
3. push到github刷新博客就生效了。

#### **摘要「ReadMore」**

既然开了头打算把摘要也弄下，这样主页看起来整齐点。摘要其实也很简单，不过自己挖了个坑折腾半天才弄好。

1. 第一步也是编辑_config.yml添加如下设置，就是定义分隔符，主页只显示文章里分隔符上面的内容。`excerpt_separator:  '<!-- more -->'`
2. 接着确认intex.html里文章显示使用的是「{{ post.excerpt }}」，我上面的index.html就是。
3. 最后在文章里添加分隔符「<!-- more -->」就可以push刷新生效了。

但是我却卡在这一步，折腾半天最后发现原来文章开头已经定义了分隔符，所以_config.yml里的设置不生效。当初是fork别人的博客拿来用的，没注意眼皮底下白忙一通。

如果md文章里也定义了分隔符，也改成和_config.yml里一样就行了。

但是一堆的文章一个个添加分隔符麻烦，可以用sed批量添加。下面的命令在所有md文章的第21行添加分隔符。`sed -i '20 a<!-- more -->' *.md`
