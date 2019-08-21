# cppcc-self

> 记录cppcc项目自发创建开发步骤及其所遇到的问题

## 步骤

> 先实现功能，再考虑安全、性能。

* [x] 项目启动后在Redis中存入13种角色所限定的列表，即：初始化所有权限菜单到redis

  ```
  run（MyApplicationRunner）
  
  ->加载方法（cacheDataService.SysPermissionRoleMapper();）
  
  ->列表缓存（在以上方法中操作）
  ```

  

* [x] 登录页(shiro--安全认证后续再添加)，验证码，添加拦截器 （链接转发）

  * 验证码显示；Shiro如何校验密码；页面跳转失败；

* [ ] easy-ui学习（边做主页内容边学）



[x] asd

* [ ] 根据登录所传递的RoleId显示该角色的左边目录(操作角色：admin)
* [ ] 点击对应内容显示信息表格（显示内容：系统设置/角色管理）


