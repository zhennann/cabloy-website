## What is EggBorn.js?
> EggBorn.js is the Ultimate Javascript Full Stack Framework.

EggBorn.js is the best practice for implementing full stack with Javascript.
EggBorn.js does not 'Reinvent the Wheel', but uses the latest open source technology, so as to achieve the best combination of the full stack development technology.
The frontend of EggBorn.js uses Vue.js + Framework 7 + Webpck, while the backend uses Koa.js + Egg.js, and the database uses mysql.
EggBorn.js keeps track of the latest achievements in open source technology, and continues to optimize to keep the entire framework in the best status.

## EggBorn.js Focus on: Business Modularization

With the developing of the javascript technology, the experience of the frontend and backend development becomes smoother, and the development efficiency enhance significantly. However, some friends still doubt whether it can be competent for the development of large-scale web applications. Large-scale web applications are characterized by the need to develop a large number of page components as business grows. Faced with this situation, generally there are two solutions:

> 1 Build as a single page application: The disadvantage is that the deployment package is very large.
> 2 All page components are loaded asynchronously: The disadvantage is that the pages are too scattered, so the frontend should interact with the backend frequently.

EggBorn.js implements the third solution:
> 3 The page components are classified by business requirements, which is business modularization, and implements the business module’s asynchronous loading mechanism. Thus it makes up the shortcomings of the first two solutions and satisfies the needs of the large-scale web application.


## EggBorn.js Technical Features

- **Business Modularization**: The page components are arranged as modules.
- **Loading Flexibility**: Modules can be loaded asynchronously or synchronously.
- **Highly Modular Cohesion**: The module includes frontend page components and backend business logics.
- **Parameter Configuration Flexibility**: Both frontend and backend of the module can be configured separately.
- **I18N**: Both frontend and backend of the module support i18n separately.
- **Module Isolation**: Module’s pages, data, logic, routing, configuration and other elements have been through namespace isolation processing, which avoids the variable pollutions and conflicts.
- **Easy Transaction Processing**: Just configuring a parameter on the routing record can process the database transaction perfectly.
- **Progressive Development**: Due to the high degree of cohesion of the module, EggBorn.js can deposit the business in the form of module, which can be reused in multiple projects. Business modules can be contributed to the npm Open Source Community, and can also be deployed to the company’s internal private npm registry.

> With EggBorn.js, not only the components can be reused, but also the business modules do in the future.
