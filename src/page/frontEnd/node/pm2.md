## pm2使用

### 启动
pm2 start app.js/pm2.conf.json

### 重启
pm2 restart all 同时杀死并重启所有进程，短时间内服务不可用
pm2 reload all 重启所有进程，保证一个服务可用

### 显示所有进程
pm2 list
pm2 logs [appName/appId]

### 停止
pm2 stop all
pm2 stop appName/appId

### 删除
pm2 delete appName/appId/all

### 查看每个应用占用情况
pm2 monit
pm2 show appName/appId