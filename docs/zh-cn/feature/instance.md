# 多实例

一个后台服务可以支持多实例，实例与网站子域名一一对应，不同实例的数据完全隔离。比如`instance1.cabloy.org`和`instance2.cabloy.org`。

!> 为了便于测试与开发，IP地址对应一个缺省的实例。

## 如何使用

### 引用实例Id

```javascript
// iid
const iid=this.ctx.instance.id;
// add todo
await this.ctx.db.insert('aTestTodo', {
  iid,
  atomId: key.atomId,
});
```

### 使用model

model封装了实例Id的设置，所以使用model不必考虑实例。

```javascript
// add todo
await this.ctx.model.todo.insert({
  atomId: key.atomId,
});
```

## 创建实例

必须先创建实例，才能提供相应的服务。创建`instance1.cabloy.org`对应的实例如下：

`src/backend/config/config.prod.js`

```javascript
// instances
config.instances = [
  { subdomain: 'instance1', password: '', title: '',
    meta: {
      jsonp: { whiteList: null },
    },
  },
];
```

> - subdomain: 子域名
> - password: 实例中用户`root`的访问密码
> - title: 网址标题
> - meta: 扩展信息

!> `test`和`local`环境自动创建了与IP对应的缺省实例，从而方便测试与开发
