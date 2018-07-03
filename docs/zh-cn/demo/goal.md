# 目标

基于cabloy的项目，所有业务功能都通过`模块`的方式实现。通过这个演示，我们开发一个模块，实现`todos`的功能，主要演示以下特性：

> 1. 原子类型：实现一个原子类型todo，具备增删改查功能
> 2. 原子指令：实现一个原子类型`review`，只有经过`review`之后的todos，才允许公开访问
> 3. 角色权限清单：
>   - 角色`registered`（所有注册用户）都允许新建并管理自己的todos，
>   - 角色`reviewer`允许review todos，
>   - 角色`registered`（所有注册用户）都允许查看所有reviewed todos。
> 4. 用户清单：
>   - 用户`demo001`，属于角色`registered`，
>   - 用户`demo002`，属于角色`reviewer`。
> 5. 用户代理:
>   - 用户`demo002`可把自己的权限代理给用户`demo001`，
>   - 用户`demo001`可以代理用户`demo002`进行`review`操作。


