---
title: pm2 常用命令
categories: 杂项
tags: [server, node]
date: 2021-04-27 17:21:48
---

# pm2 常用命令

### 1、node守护进程比较
**node 守护进程比较**
* nodemon：开发环境使用，修改后自动重启。
* forever：管理多个站点，每个站点访问量不大，不需要监控。
* pm2：网站访问量比较大，需要完整的监控界面。

### pm2主要特性
* 内建负载均衡（使用Node cluster 集群模块）
* 后台运行
* 0 秒停机重载
* 开机自启动脚本
* 停止不稳定的进程（避免无限循环）
* 控制台检测
* 提供远程控制和实时的接口API (允许和PM2进程管理器交互)


### pm2 常用命令

##### 1. 安装
`npm install -g pm2`

##### 2. 单个启动
`pm2 start app.js`  # 启动
`pm2 start app.js -i 4` # 启动4个应用实例，自动负载均衡

`pm2 start app.js --watch` # 监听文件变化并重启

### 3. 批量重启

使用 `pm2 ecosystem` 或 `pm2 init` 会生成 `ecosystem.config.js` 文件

然后运行 `pm2 start ecosystem.config.js --env dev`

```js
module.exports = {
  apps : [{
    name: 'app',
    script: './app.js',
    env_dev: {
      NODE_ENV: 'dev', // 环境参数，当前指定为生产环境 process.env.NODE_ENV
      REMOTE_ADDR: 'https://www.test.com',
    },
    env_sit: {
      NODE_ENV: 'sit',
      REMOTE_ADDR: 'https://www.test.com',
    },
    env_prod: {
      NODE_ENV: 'prod',
      REMOTE_ADDR: 'https://www.test.com',
    },
  }, {
     name: 'worker',
     script: 'worker.js'
  }]
}
```

##### 4. 重启
`pm2 restart app_id | app_name` # 重启
`pm2 restart all` # 重启所有进程，相当stop+start
`pm2 reload all` # 0秒停机重载进程 (用于不间断进程)

##### 5. 停止
`pm2 stop app_id | app_name` # 重启
`pm2 stop all` # 重启所有进程，相当stop+start

##### 6. 删除
`pm2 delete app_id | app_name`  # 从列表中删除指定的进程
`pm2 delete all` # 从列表中删除全部进程
`pm2 kill` # 杀死守护进程

##### 7. 查看
`pm2 list` # 查看进程
`pm2 logs` # 查看日志
`pm2 show app_name|app_id` # 查看进程详情
`pm2 monit`  # 查看CPU和内存资源占用

##### 8. 启动 HTTP 服务
`pm2 serve|expose [options] [path] [port]`

例如 `pm2 serve ./dist 8888`，在 dist 目录启动 http 服务，端口号为 8888

