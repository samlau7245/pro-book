# 概述

业务系统中，用户操作数据的权限流程：

* 用户进入系统，这需要后台管理员给用户**分配账号**(后台系统是没有注册功能的)。
* 用户进入系统能看到那些菜单导航，这需要后台管理员**分配菜单**。
* 用户点击菜单导航中的具体页面(页面内能访问多少数据量也是有访问限制的)，这需要后台管理员**分配页面**。
* 用户在页面中可以对数据做多少操作，这需要后台管理员进行**配置页面操作**。

除了第一步需要管理员分配账号意外，其他操作都需要配置权限。 

# 功能权限

> 功能权限：是指用户具体能访问哪些菜单，以及对菜单页面中的那些按钮有操作权限。把多个菜单和对应页面的功能按钮组织到一起就形成了一个**权限列表**。

```
++++++ 权限列表 ++++++

系统设置
	菜单管理	查看
	组织架构	查看
	权限管理	查看
职员管理
	职员信息	查看、添加、编辑、删除
	账号设置	开通账号、关闭账号
```

对于用户来说，通常有以下几种方案来获得权限列表：


## ACL(Access control list)，权限访问列表

> `ACL(Access control list)，权限访问列表`: 是通过直接将用户和权限列表绑定在一起，实现权限管理。

* 优点： 可以为每个用户的权限进行个性化设置。
* 缺点： 当拥有相同职能的用户比较多时，修改一次权限需要耗时很多。（如销售部有很多的销售专员，因为他们的职位相同，所有获得的操作权限相同，但后期添加或者删除一个访问权限，那么需要给所有人相同职位的人都操作一遍）， 所以这种设计方案在开发中使用频率很低。


## RBAC(Role-Based Access Control)，基于角色的访问控制

> `RBAC(Role-Based Access Control)，基于角色的访问控制`: 通过先创建一个`角色`，然后将`权限列表`绑定在这个角色上，再将这个`角色`绑定到`用户`上，用户就可以获得这个角色的所有权利。

如果处于同一职位的员工，正常情况下权利都是一样的，也有特殊情况，比如给其中一个添加或减少部分权限时，就比较难办。

**通常的方案就是在给角色绑定权限时，先采用权限最小化原则，能少给就少给，然后再做一个角色绑定多余的权限，再把这个角色也绑定给职员，这个时候两个角色的权限就合并到一起了，也就是用户和角色之间是一对多的关系。**

<img src="/assets/images/e_commerce/03.png"/>

角色管理：

<img src="/assets/images/e_commerce/04.png"/>

权限列表：

<img src="/assets/images/e_commerce/05.png"/>

# 数据权限

> `数据权限`: 数据权限比较复杂，这个权限的具体实现主要有具体业务来定。

## 场景1：分上下级的数据访问

企业中通常都有销售部，销售部下又分各个大区，大区下又划分小组。对于销售人员来说，他们的薪资是和业绩挂钩的，所以没有人会把自己的客户资源让你别人的，这就导致数据处理上，销售专员通常只能看自己的(自愿共享的就不再这里讨论)，而对于销售主管则能看到手下所有的销售专员的客户资料，同理，大区经理，和销售总监也都能看到自己手下所有人的客户资源。

在这种场景下的数据，很明显是能看出，这是根据企业的组织架构，根据职员的职位高低来控制数据的访问权限。所以它通常的解决方案是和组织架构相关联的，具体逻辑如下：

* 在给职员分配账号的时候，设置好对应的部门和职位。
* 当用户获取数据时，根据自己当前的部门和职位，获取到所有下属的职员信息。
* 将属于这些下属职员的所有客户信息显示出来，这个数据控制就完成了。

> 这涉及到系统设置中**组织架构**设计。

## 场景2：分区块的数据访问

分区块的典型案列就是电商企业员工对多个仓库的访问问题，我们假设有一个大的电商平台，它在全国有多个仓库，如北京仓、上海仓、西安仓、甘肃仓。对于各个仓库的职员来说，通常他们只有当前仓库的数据访问权限，而对于总公司的买手、技术等人员来说，他们平时要巡检查看各个仓库数据，所以需要有全部仓库的数据。而仓库的组织架构和买手、技术所属部门并不存在上下级关系。

在这种场景下的数据，它就是按照分区块的方式来划分。要解决这个问题其实也很简单，就是上面讲的【用户-角色-权限列表】，只是这里的权限列表内容不再是菜单和操作按钮，而是各个仓库。

## 场景3：数据共享

数据共享的场景是很常见的，像我们经常使用的协同文档软件，里面都会有将内容共享给个人或者共享给某个组的功能。

这种场景下的的数据，我们需要通过单独建表，维护共享者和被共享对象的关系，获取数据时从对应关系表中读取对应共享数据即可。

## 场景4：分状态的数据访问

* [电商后台设计：权限设计](http://www.woshipm.com/pd/4055860.html)
