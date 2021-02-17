# celery使用的是UTC时区

### 解决办法：

- 定时需要设置的时区

- 在配置文件中指定：
```
CELERY_TIMEZONE = 'Asia/Shanghai'
CELERY_ENABLE_UTC=True
```
- 在程序中指定：
```
app.conf.enable_utc = False
app.conf.timezone = "Asia/Shanghai"
```