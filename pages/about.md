---
layout: page
title: About
description: 技术容不得半点含糊
keywords: Linc Zhang, 张连聘
comments: true
menu: 关于
permalink: /about/
---

我是张连聘。

直接删除电脑桌面图标用以卸载软件，从那时起，我开始了我的计算机学习生涯。

学生时代，在学校信息管理中心接触计算机网络，WEB服务器端的计算机程序开发，接着学习计算机组成原理，C语言，单片机，模电、数电，嵌入式操作系统，数据库，项目管理.

经过大学的学习，了解了计算机的世界。同时，对抽象，层次，接口等概念有了深刻理解。
 
工作了，发现自己解决事情的综合能力欠缺，正努力学习各种技能。努力成长为一名合格的职场人。

一直在寻找自我，个人比较喜欢和别人沟通，喜欢思考别人认为没有用的东西。但我始终觉着思考、不断学习、成长是幸福的事情。 喜欢以通俗的方式讲解知识。喜欢哲学，关注教育。 梦想着有一天能够成为一名坚持独立教育理念的知识传播者。

个人弱点（列在这里，用于提醒自我系统升级）：
1. 前怕狼后怕虎，没有担当。
2. 遇到突发事件不能冷静面对并思考解决方案。
3. 想法千千万，实践寥寥无。有严重的拖延症。
4. 单线程脑子，并发能力太差。心里有个事就不安，定不能成大事。

## 联系

{% for website in site.data.social %}
* {{ website.sitename }}：[@{{ website.name }}]({{ website.url }})
{% endfor %}

## Skill Keywords

{% for category in site.data.skills %}
### {{ category.name }}
<div class="btn-inline">
{% for keyword in category.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}