---
title: "基于SSH三大框架的员工管理系统"
date: 2016-11-02T15:32:34+00:00
tags: ["Java"]
---

## 摘要

本系统为本人学习 SSH 三大框架时所做的整合实例，系统角色包括普通用户和管理员两种，首页有管理员登录入口链接。系统功能主要包括管理员对用户的基本增、删、改、查和分页显示用户信息等。

## 系统环境

- 本系统使用 eclipse+mysql+jdk1.8+tomcat8 进行开发
- 框架使用 struts2+hibernate3+spring3

## 页面展示

- 首页一开始没考虑屏幕分辨率和比例问题以及浏览器的兼容问题，后该用 bootstrap 简单模板，达到兼容旧版 IE 浏览器效果，并且为响应式布局，屏幕可任意缩放。
  ![首页](20161102144350917.png#center#center)

---

- 注册页面采用 angularJS 前端框架实现客户端表单验证

![这里写图片描述](20161102145206140.png#center)

---

- 日期使用[jedate.js](http://www.jayui.com/jedate/)控件

![这里写图片描述](20161102145310781.png#center)

---

- 注册成功提示（后台为新注册用户分配三个随机邀请码用于邀请其他用户注册本系统）
  ![这里写图片描述](20161102150106378.png#center)

---

- 个人主页使用 easyui 框架
  ![这里写图片描述](20161102150253739.png#center)

---

- 管理员首页（可分页显示用户）
  ![这里写图片描述](20161102154604257.png#center)

---

- 添加用户
  ![这里写图片描述](20161102154604257.png#center)

---

## 核心代码解析

### 1、随机邀请码生成

使用 UUID 并将其切片，取前八位作为验证码（本算法尚不成熟，在大量使用后可能出现重复）

```java
public static String[] codeMaker() {
  String[] code = new String[3];
  for (int i=0; i<3; i++) {
    code[i] = UUID.randomUUID().toString().substring(0,8).toUpperCase();
  }
  return code;
}
```

### 2、登录验证

- action 层

```java
public String login() {
  User existUser = userService.login(user);
  if (existUser == null) {
    this.addActionError("用户名或密码错误");
    return INPUT;
  } else {
    ActionContext.getContext().getSession().put("currUser", existUser);
    return SUCCESS;
  }
}
```

- service 层
  拿到从表单提交并封装到实体的 user 对象，将其作为参数传递给数据库，调用 dao 层函数，核对相应用户名和密码是否存在并一致

```java
@Override
/**
 * 业务层登陆的方法
 */
public User login(User user) {
  User existUser = userDao.findByUsernameAndPassword(user);
  return existUser;
}
```

- dao 层
  使用 hibernate 模板方法查询相应对象，若查到则返回对象，用于 action 层作处理

```java
@SuppressWarnings("unchecked")
@Override
/**
 * dao中根据用户名和密码查询用户的方法
 */
public User findByUsernameAndPassword(User user) {
  String hql = "from User where username = ? and password = ?";
  List<User> list = this.getHibernateTemplate().find(hql, user.getUsername(), user.getPassword());
  if (list.size() > 0) {
    return list.get(0);
  }
  return null;
}
```

- view 层

```html
<body>
  <div id="cc" class="easyui-layout" fit="true" style="width:100%;height:100%;">
    <div region="west" split="true" title="菜单" style="width:200px;">
      <div id="aa" class="easyui-accordion" fit="true">
        <div
          title="功能导航"
          selected="true"
          style="overflow:auto;padding:10px;"
        >
          <div class="tab" style="border: 1px;">
            <a href="user_info">个人信息</a>
          </div>
          <div class="tab" style="border: 1px;">
            <a href="user_invite">已邀请人的信息</a>
          </div>
          <div class="tab" style="border: 1px;">
            <a href="user_code">邀请码信息</a>
          </div>
        </div>
      </div>
    </div>
    <div region="center" title="主界面" style="padding:5px;">
      <div id="welcome" class="tab">
        <span>欢迎你，${currUser.name }</span>
        <span>
          <a href="${ctx }/index.jsp">退出系统</a>
        </span>
      </div>
      <div
        id="tt"
        class="easyui-tabs"
        fit="true"
        style="width:500px;height:250px;"
      ></div>
    </div>
  </div>
</body>
```

### 3、分页显示

- action 层

```java
public String findAll() {
  PageBean<User> pageBean = adminService.findAll(currPage);
  List<User> list = pageBean.getList();
  ActionContext.getContext().getValueStack().push(pageBean);
  ActionContext.getContext().getSession().put("users", list);
  return "findAll";
}
```

- 实体层

```java
private int currPage;		//当前页
private int pageSize;		//每页显示记录数
private int totalCount;		//总记录
private int totalPage;		//总页数
private List<T> list;		//封装实体
```

- 业务层

```java
// 分页查询所有用户
@Override
public PageBean<User> findAll(int currPage) {
  PageBean<User> pageBean = new PageBean<User>();
  // 封装pageBean
  pageBean.setCurrPage(currPage);		// 当前页数
  int pageSize = 10;
  pageBean.setPageSize(pageSize);		// 每页记录数
  int totalCount = adminDao.findCount();
  pageBean.setTotalCount(totalCount);		// 总记录数
  double tc = totalCount;				// 总页数
  Double num = Math.ceil(tc / pageSize);
  pageBean.setTotalPage(num.intValue());
  int begin = (currPage - 1) * pageSize;	// 每页开始的记录索引
  List<User> list = adminDao.findByPage(begin,pageSize);	// list用来封装用户集合
  pageBean.setList(list);

  return pageBean;
}
```

- dao 层

```java
@SuppressWarnings("unchecked")
@Override
public List<User> findAll() {
  String hql = "select * from User where uid != 1";
  List<User> list = this.getHibernateTemplate().find(hql);
  if (list.size() > 0) {
    return list;
  }
  return null;
}

@SuppressWarnings("unchecked")
@Override
public int findCount() {
  String hql = "select count(*) from User";
  List<Long> list = this.getHibernateTemplate().find(hql);
  if(list.size() > 0) {
    return list.get(0).intValue();
  }
  return 0;
}

/**
 * 分页函数
 */
@SuppressWarnings("unchecked")
@Override
public List<User> findByPage(int begin, int pageSize) {
  DetachedCriteria criteria = DetachedCriteria.forClass(User.class);
  List<User> list = this.getHibernateTemplate().findByCriteria(criteria, begin, pageSize);
  return list;
}
```

- view 层

```html
<div id="page" align="left" class="font">
  <table border="0" cellspacing="0" cellpadding="0" width="500px">
    <tr>
      <td align="left">
        <span>第<s:property value="currPage" />/<s:property value="totalPage" />页</span>&nbsp;&nbsp;
        <span>
          总记录数：<s:property value="totalCount" />&nbsp;&nbsp;
          每页显示：<s:property value="pageSize" />
        </span>&nbsp;&nbsp;
        <span>
          <s:if test="currPage != 1">
            <a href="${ctx }/admin_findAll?currPage=1">[首页]</a>&nbsp;&nbsp;
            <a href="${ctx }/admin_findAll?currPage=<s:property value="currPage-1"/>">[上一页]</a>&nbsp;&nbsp;
          </s:if>
          <s:if test="currPage != totalPage">
            <a href="${ctx }/admin_findAll?currPage=<s:property value="currPage+1"/>">[下一页]</a>&nbsp;&nbsp;
            <a href="${ctx }/admin_findAll?currPage=<s:property value="totalPage"/>">[尾页]</a>&nbsp;&nbsp;
          </s:if>
        </span>
      </td>
    </tr>
  </table>
</div>
```
