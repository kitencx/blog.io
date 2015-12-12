---
layout: post
title: Java7使用Lambda导致的泛型问题
date: 2015-3-02
categories: blog
tags: [Android,Lambda,泛型]
descrption:记录下Android开发用Lambda碰到的问题
---

##Java7使用Lambda导致的泛型问题

Android开发时，听说Java7也能使用Java8的Lambda特性，只需要引入gradle插件，遂决定尝试一下

首先在Project的`build.gradle`的android节点添加如下代码：

```
compileOptions {
	sourceCompatibility JavaVersion.VERSION_1_8
    targetCompatibility JavaVersion.VERSION_1_8
}
```

表示使用Java8（当然你得先安装Java8）

之后再在项目的`build.gradle`的buildscript->dependencies节点下添加如下依赖：

```
classpath ‘me.tatarka:gradle-retrolambda:3.2.0‘
```

这里表示的是编译脚本的依赖

最后是引用插件，在Project的`build.gradle`开始添加如下代码：

```
apply plugin: ‘me.tatarka.retrolambda‘
```

引入Lambda成功，sync完毕，立刻去体验了一把。

原代码是

```
ApiService.getInstance().login(uname, pwd, new Listener<Account>() {
	@Override
	onSuccess(Account data) {
		...
	}
}, new ErrorListener() {
	onException(Exception e) {
		...
	}
})
```

替换成Lambda后变成

```
ApiService.getInstance().login(uname, pwd, data -> {
	...
}, error -> {
	...
})
```

代码一下子少了很多，然后Run，登录

纳尼，Crash了。好像就改成Lambda啊，为什么Crash呢

查看CrashLog，发现Exception是无法获取泛型信息，因为原先的泛型信息，是通过`Listener<Account>`这个对象，采用`getGenericInterfaces()`的方式获取的，为什么一换成Lambda就无法获取泛型信息了呢

原来Lambda采用了自动类型识别的方式，所以Lambda表达式会将对象泛型信息擦除掉。

So sad，Lambda表示不支持原先的接口回调方式



