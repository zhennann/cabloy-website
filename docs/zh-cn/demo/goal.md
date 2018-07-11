# 目标

基于cabloy的项目，所有业务功能都通过`模块`的方式实现。
在这个演示中，我们在前面已经生成的项目`cabloy-demo`中，创建一个模块`test-todo`，主要演示以下特性：

> 1. 原子类型：实现一个原子类型todo，具备增删改查功能
> 2. 原子指令：实现一个原子类型`review`，只有经过`review`之后的todos，才允许公开访问
> 3. 角色权限清单：
>   - 角色`registered`（所有注册用户）都允许新建并管理自己的todos
>   - 角色`reviewer`允许review todos
>   - 角色`registered`（所有注册用户）都允许查看所有reviewed todos
> 4. 用户清单：
>   - 用户`demo001`，属于角色`registered`
>   - 用户`demo002`，属于角色`reviewer`
> 5. 测试驱动


