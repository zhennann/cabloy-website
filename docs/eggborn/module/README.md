# Module Development

## Naming Convention

In order to increase the business modules continuously and achieve a highly reusable effect, the namespace of all modules must be fully isolated, to avoid mutual pollution and conflict. Thus the naming convention is as follows:
> egg-born-module-{providerId}-{moduleName}

Such as the module `egg-born-module-a-version`, the naming information is as follows:
> - `providerId`: a
> - `moduleName`: version
> - `fullName`: egg-born-module-a-version
> - `relativeName`: a-version
> - frontend page route url: /a/version/{page}
> - backend api route url: /a/version/{controller}/{action}

## Loading Mechanism

The module supports both asynchronous loading and synchronous loading. Generally, the default is asynchronous loading. If you want to change it into synchronous loading, just add `-sync` suffix behind the module name, such as the module `egg-born-module-a-components-sync`.

## Create a module

``` bash
$ egg-born src/module/test-todo --type=module
```
