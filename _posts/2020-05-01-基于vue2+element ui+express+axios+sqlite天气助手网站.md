---
layout: post
title:  "基于vue2+element ui+express+axios+sqlite天气助手网站"
date:   2020-05-01
excerpt: "基于vue2+element ui+express+axios+sqlite天气助手网站"
tags: [Front-End]
comments: false
---



#### 一、当前实现功能

✅ 允许用户查询全球超过二十万个城市的实时天气情况以及天气预告

![屏幕快照 2020-04-30 19.12.48.png](https://i.loli.net/2020/05/01/YQZd4jTmyNIMacq.png)

![屏幕快照 2020-04-30 19.13.07.png](https://i.loli.net/2020/05/01/xz9AyY1kGKc4PiT.png)



✅ 用户登录与注册功能（测试账号 - username:test password:123456）

![屏幕快照 2020-04-30 19.13.58.png](https://i.loli.net/2020/05/01/yZ5DbRJXuASUokV.png)

![屏幕快照 2020-04-30 19.13.33.png](https://i.loli.net/2020/05/01/ClShbDwUKEQMWyV.png)



✅ 用户中心（可以修改个人信息以及密码）

![屏幕快照 2020-04-30 19.14.39.png](https://i.loli.net/2020/05/01/fmjWLKz8Jer43Ct.png)

![屏幕快照 2020-04-30 19.21.31.png](https://i.loli.net/2020/05/01/t83b4F2XzR6MgHr.png)

![屏幕快照 2020-04-30 19.21.54.png](https://i.loli.net/2020/05/01/n3ke9sq1RDLY8Kx.png)



#### 二、存在的bug🐛

- 验证码不可用。
- 注册的表单有些繁琐，可以尽量减少需要填的选项。
- 没有保持查询界面的会话alive。



#### 三、需要增加的功能🔧

- 一键开启🔛夜间模式。
- 用户查询某个城市天气信息时，界面对应显示该城市的一张照片（打算用Google places API）。
- 用户可以保存一些城市，当他登录系统后，系统会自动显示用户保存的这些城市的当前天气信息。
- 可以添加一张地图，用户点击地图的某个位置就会显示出对应城市天气（目前没有想到对应的技术支持）。



#### 四、开发日志（记录踩到的坑以及开发进度）

##### 5月1日😣：

今日主要在做验证码，将之前的验证码功能改成了用express-session和svg-captcha来实现。因为前后端是分离的，所以会出现跨域的问题。当更新验证码时，生成验证码的方法会重新开session，而获取验证码的登录方法会一直在获取之前的那个session中的验证码，也就是一直记住的是初始验证码。这样存在的问题就是，用户不能更新验证码，只能不断尝试第一个验证码，否则永远登录不进去系统。尝试了很多网上的方法，还是没有解决，打算等忙完其他DDL再来弄啦～

