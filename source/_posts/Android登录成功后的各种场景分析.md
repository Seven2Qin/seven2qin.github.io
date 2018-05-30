---
title: 登录成功后的各种场景分析  
date: 2018/5/29 10:51:25  # 文章发表时间
tags:
- Android
categories: Android # 分类
thumbnail: http://o835lhtor.bkt.clouddn.com/blog/img/1.jpg # 略缩图
---
-------------------
首先，贯穿App  的，应该有一个User  全局变量，在每次登录成功后，会将其isLogin  属性设置为true ，在退出登录后，则将该属性设置为false 。这个User  全局变量要支持序列化到本地的功能，这样数据才不会因内存回收而丢失。
其次，登录分为3  种情形：
**情形1**  ：点击登录按钮，进入登录页面LoginActivity ，登录成功后，直接进入个人中心
PersonCenterActivity 。这种情况最直截了当，一路执行startActivity(intent)  就能达到目的。
**情形2**  ：在页面A ，想要跳转到页面B ，并携带一些参数，却发现没有登录，于是先跳
转到登录页，登录成功后，再跳转到B  页面，同时仍然带着那些参数。
这就主要是setResult(intent, resultCode)  发挥作用的时候了，Activity  的回调机制这时候
派上了用场，如下所示：
```java
btnLogin2.setOnClickListener(new OnClickListener(){
@Override
public void onClick(View v) {
if(User.getInstance().isLogin()) {
gotoNewsActivity();
} else {
Intent intent = new Intent(LoginMainActivity.this,
LoginActivity.class);
intent.putExtra(AppConstants.NeedCallback, true);
startActivityForResult(intent,
LOGIN_REDIRECT_OUTSIDE);
}
}
});
```
**情形3**：在页面A，执行某个操作，却发现没有登录，于是跳转到登录页，登录成功后，再回到页面A，继续执行该操作。处理方式同于情形2，也是使用setResult 来完成回调。
```java
btnLogin3.setOnClickListener(new OnClickListener(){
@Override
public void onClick(View v) {
if(User.getInstance().isLogin()) {
changeText();
} else {
Intent intent = new Intent(LoginMainActivity.this,
LoginActivity.class);
intent.putExtra(AppConstants.NeedCallback, true);
startActivityForResult(intent,
LOGIN_REDIRECT_INSIDE);
}
}
});
```
-------------------
无论是上述哪种情形，登录页面LoginActivity  只有一个，所以要把上面的三个逻辑整合在一起，如下所示：
```java
RequestCallback loginCallback = new AbstractRequestCallback() {
@Override
public void onSuccess(String content) {
UserInfo userInfo = JSON.parseObject(content,
UserInfo.class);
if (userInfo != null) {
User.getInstance().reset();
User.getInstance().setLoginName(userInfo.getLoginName());
User.getInstance().setScore(userInfo.getScore());
User.getInstance().setUserName(userInfo.getUserName());
User.getInstance().setLoginStatus(true);
User.getInstance().save();
}
if(needCallback) {
setResult(Activity.RESULT_OK);
f inish();
} else {
Intent intent = new Intent(LoginActivity.this,
PersonCenterActivity.class);
startActivity(intent);
}
}
};
```
整合的关键在于从上个页面传过来needCallback  变量，它决定了是否要回到上个页面。另一方面，我们看到，在登录成功后，我们会把用户信息存储到User  这个全局变量并序列化到本地，这是因为各个模块都有可能使用到用户的信息。其中LoginStatus  是关键，接下来的篇幅将着重谈论这个属性。
最后在LoginMainActivity  中的onActivityResult  回调函数，它负责处理登录后的事情，如下所示：
```java
@Override
protected void onActivityResult(int requestCode,
int resultCode, Intent data) {
if (resultCode != Activity.RESULT_OK) {
return;
}
switch (requestCode) {
case LOGIN_REDIRECT_OUTSIDE:
gotoNewsActivity();
break;
case LOGIN_REDIRECT_INSIDE:
changeText();
break;
default:
break;
}
}
```
-------
我们看到，对于情形2 ，当用户在LoginMainActivity  点击按钮想跳转到NewsActivity ，如果已经登录，就直接跳转过去；否则，先到LoginActivity  登录，然后回调LoginMain-Activity  的onActivityResult ，仍然跳转到NewsActivity 。