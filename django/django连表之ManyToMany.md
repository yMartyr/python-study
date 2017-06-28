---
title: django连表之ManyToMany
tags: 新建,模板,小书匠
grammar_cjkRuby: true
---


欢迎使用 **{小书匠}(xiaoshujiang)编辑器**，您可以通过==设置==里的修改模板来改变新建文章的内容。



models.ManyToManyField()  随便写在哪个类都行

m=models.ManyToMany()

manytomany 帮我们只能生成三列字段。
manytomany给我们创建的第三张表，models没有对应类，所以我们无法直接对第三张表进行操作。