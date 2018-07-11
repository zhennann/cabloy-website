# 模块管理

## 模块依赖
EggBorn.js通过`package.json`文件管理模块依赖关系。
比如，模块`aa-module1`依赖`aa-module2`，需要在模块`aa-module1`的`package.json`文件中作如下配置
``` json
{
  "name": "egg-born-module-aa-module1",
  "version": "0.0.1",
  "eggBornModule": {
    "dependencies": {
      "aa-module2": "0.0.1"
    }
  },
  "dependencies": {
    "egg-born-module-aa-module2": "^0.0.1"
  }
}
```

> 设置`"egg-born-module-aa-module2": "^0.0.1"`，是为了在安装模块`aa-module1`时自动安装模块`aa-module2`。如果模块没有公开发布，就不必设置。

## 模块数据版本
模块一般都要操作数据库，当模块版本升级时，数据库结构也有可能变动。EggBorn.js实现了模块数据版本的管理，便于业务模块的积累沉淀。

在模块的`package.json`文件中配置`fileVersion`为当前数据版本
``` json
{
  "name": "egg-born-module-aa-module1",
  "version": "0.0.1",
  "eggBornModule": {
    "fileVersion": 1
  }
}
```

在模块后端添加Api路由
``` javascript
{ method: 'post', path: 'version/update', controller: version, middlewares: 'inner' }
```

添加version Controller
``` javascript
module.exports = app => {
  class VersionController extends app.Controller {

    async update() {
      await this.service.version.update(this.ctx.getInt('version'));
      this.ctx.success();
    }

  }
  return VersionController;
};
```

添加version Service
``` javascript
module.exports = app => {

  class Version extends app.Service {

    async update(version) {
      if (version === 1) {
        // do something
      }

      if (version === 0) {
        // can provide test data here
      }
    }

  }

  return Version;
};
```

> 当启动后端服务时，EggBorn.js自动检测模块数据版本的变化，并执行相应的路由，完成数据的版本升级。

## 模块发布
当项目中的模块代码稳定后，可以将模块公开发布，贡献到开源社区。也可以在公司内部建立npm私有仓库，然后把模块发布到私有仓库，形成公司资产，便于重复使用。
模块发布步骤如下
``` bash
$ cd path/to/module      -- 进入模块目录
$ npm install            -- 安装模块依赖
$ npm run build:front    -- 构建前端代码
$ npm run build:backend  -- 构建后端代码
$ npm publish            -- 发布至npm仓库
```
