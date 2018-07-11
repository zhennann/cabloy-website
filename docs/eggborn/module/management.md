# Module Management

## Module Dependencies

EggBorn.js manages module dependencies through `package.json`.

For example, module `aa-module1` depends on `aa-module2`, and the following configuration is needed in the file `package.json` of the module `aa-module1`:
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

> Add `"egg-born-module-aa-module2": "^0.0.1"` so as to install the module `aa-module2` automatically when installing module `aa-module1`


## Module Data Version

Generally Modules should operate the database. The database structure is also possible to change when the module version upgrade. EggBorn.js manage the module data version so as to facilitate the accumulation of business modules.

In the file `package.json` of the module, configure `fileVersion` as the current data version of the module:
``` json
{
  "name": "egg-born-module-aa-module1",
  "version": "0.0.1",
  "eggBornModule": {
    "fileVersion": 1
  }
}
```

Add backend api route:
``` javascript
{ method: 'post', path: 'version/update', controller: version, middlewares: 'inner' }
```

Add backend controller:
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

Add backend service:
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

> When the project started, EggBorn.js detects the change of the module data version automatically, and executes the corresponding api route to upgrade the data version.

## Module Publish

When the module in the project is stable, you can publish and contribute the module to the Open Source Community. You can also create private npm registry in your company, and then publish the module there, so as to form company assets for easy reuse.

``` bash
$ cd path/to/module      -- Goto the module directory
$ npm install            -- Install module dependencies
$ npm run build:front    -- Build frontend
$ npm run build:backend  -- Build backend
$ npm publish            -- Publish to npm registry
```

