---
layout: post
title: Grails3的domain使用注意点
date: 2016-02-23
categories: blog
tags: [grails,grails3,gorm,domain]
description:grails使用过程中踩过的坑
---

1. 修改domain的static属性后run-app，发生

	`org.hibernate.MappingException: Missing type or column for column[xxx] on domain[xxx]`
	
	stackoverflow好久，还是没有解决，结果后来手动build一发之后，再run，问题解决。。特地记录一下，domain对象的static映射每次修改之后，需要手动build一发才行，不然该错误必现。
	
	IDE：IntelliJ IDEA 15.0.3




