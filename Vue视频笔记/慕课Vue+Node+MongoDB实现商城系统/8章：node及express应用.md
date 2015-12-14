## 一、node应用
### 1.1 Node.js 启动调试方式
> 1、node启动
- 通过node命令启动
- webstorm配置启动入口（也可以启动调试模式）
- pm2启动工具

> 2、说明：
- 在本项目中，将node后台服务（/server）跟前端vue服务整合到了一起（在package.json中即有vue项目的依赖，也有node的依赖）
- 在server文件夹中主要设置的就是后台服务api

> 3、使用webstorm调试node.js服务
- node属于后端服务，无法在前端进行调试，因而可以使用webstorm 的debug及断点调试功能

> 4、pm2启动调试
- 项目开发完成部署后，可以通过pm2来进行服务器上的调试
- process manage，PM2是Node的生产进行管理应用。它是热加载的，可以保持服务应用开启的同时，进行热重载。
- 它是基于进和管理的，可以实现当前机器上的负载均衡
```
// 1、安装pm2
cnpm i -g pm2
// 2、使用pm2
pm2 start app.js
// 3、停止进行
pm2 stop app.js
```