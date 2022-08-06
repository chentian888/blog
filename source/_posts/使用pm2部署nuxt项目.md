---
title: 使用pm2部署nuxt项目
toc: true
date: 2022-08-06 22:31:29
categories: 前端
tags: nuxt
---
# 使用pm2部署nuxt项目
## 步骤1
确保服务器环境和本地开发环境已经全局安装过pm2
```
yanr add pm2 -g
```
或者
```
npm install pm2 -g
```

## 步骤2
需要本地配置免密登录远程服务器。不配置免密登录也行，只是在进行`pm2`部署的时候会频繁提示输入密码。
本地免密登录服务器的具体方案就是把本地的`~/.ssh/id_rsa.pub`(ssh公钥)文件中的内容加入到远程服务器`~/.ssh`目录下的`authorized_keys`文件中，若远程服务器中不存在`authorized_keys`文件则需要新增

## 步骤3
在项目根目录新增`pm2.config.js`文件
详细的配置可看[pm2官网](https://pm2.io/)

###  pm2配置文件内容如下

```js
module.exports = {
  apps: [
    {
      name: 'ibox',
      script: 'yarn run start',
      env: {
        NODE_ENV: 'development'
      },
      env_production: {
        NODE_ENV: 'production'
      },
      instances: 'max',
      instance_var: 'INSTANCE_ID'
    }
  ],
  deploy: {
    production: {
      user: 'root',
      host: '118.195.177.46',
      ref: 'origin/master',
      repo: 'git@gitee.com:chentian888/ibox.git',
      path: '/home/www/ibox',
      'pre-setup': 'rm -rf /home/www/ibox',
      'pre-deploy': 'git pull',
      'post-deploy': 'yarn install && yarn run build && pm2 startOrRestart pm2.config.js --env production'
    }
  }
}

```
以上配置文件的意思是`name`指定`pm2`进程的名称，就像每个人都会有一个名称一样。`script`指定`pm2`需要运行的命令，`pm2`在部署程序的时候执行`script`命令拉起`nuxt`服务。`env`和`env_production`则是在执行`pm2`部署命令的时候指定的环境。
例如`pm2 deploy pm2.config.js production`指定执行`pm2.config.js`文件中的`production`部署命令。
1. 使用指定用户名登录服务器`ssh root@118.195.177.46`
2. 在执行部署之前`pre-setup`会先删除之前已经部署过的目
3. 每次执行部署前( `pre-deploy`)会去`git`上获取最新的代码获取最新的代码
4. 在获取到代码后(`post-deploy`)进行安装`yarn install` 构建`yarn build`。最后重启`pm2`服务
5. 把`build`后的目录部署到`/home/www/ibox`这个目录下
6. `pm2`启动或者重新启动并执行`script`拉起`nuxt`服务


# pm2常用命令