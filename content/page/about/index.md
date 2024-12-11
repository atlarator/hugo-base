---
title: 转移中的故事
description: 那些逆天的博客框架
date: '2024-12-11'
aliases:
  - about-us
  - about-hugo
  - contact
license: CC BY-NC-ND
lastmod: '2024-12-11'
menu:
    main: 
        weight: -90
        params:
            icon: user
---

确切来讲，这个博客的转移拖得太久太久了。最开始我用的Hexo框架弄，结果Hexo在20年之后的几乎所有issue都在痛骂这个框架，帮助信息也是没几个正经的。我年中弄这个框架的时候就受不了两点，一是你一个博客框架在我电脑开性能模式下还敢狂转风扇你也是没谁了，二是这玩意儿的定制化程度低得可怕。我想把Hexo主页最上方的地球图片换了，结果前脚改完配置文件后脚一生成发布版就给我改回去了。牛的。

大概半年后就是今年十二月，我尝试了Hugo，怎么说呢，槽点还是很多。主要集中在你在Ubuntu上apt装的Hugo出了一堆在官网issue上头都找不到的离奇bug（点名批评server后白屏），最后把Hugo卸了，去snap上头安装一个，问题居然解决了。哈，你甚至没法界定到底是Ubuntu的锅还是Hugo的锅。

Hugo另外一个和Hexo逻辑不太一样的地方在于Hugo的自动化部署比较麻烦，需要借助脚本还得分两个repo存放。因为Hexo是不区分public和dev的而Hugo在每次发布的时候都会生成一个新的public。

有一篇关于Turing Complete的指令集速查表在这次博客迁移的过程中丢失了，剩下的文章都从我的大机械盘中找了回来。总之，盖新家是真折腾人啊，但愿Hugo别突然爆发什么奇怪的Breaking Change，能让我的博客活久一点儿。如果Hugo爆发Breaking Change，大概率我就只能转战W3C了（笑）

接下来就是找时间部署在Cloudflare上头然后尝试着把评论和Tag加上。

总之，很高兴认识你，各种意义上的。愿你在这儿找到你想要的东西！