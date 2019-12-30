
# webs

web site for vpn+centos7+nginx. for flask+uwsgi.

## make build

1. virtualenv

```bash
pip install --upgrade virtualenv
virtualenv -p python3.* .env
```

2. uwsgi
```bash
#ubuntu: 
apt-get install python3.5-dev
#centos: 
yum install -y  python3-devel

pip3 install uwsgi

uwsgi --ini uwsgi.ini             # 启动
uwsgi --reload uwsgi.pid          # 重启
uwsgi --stop uwsgi.pid            # 关闭
```

uwsgi.ini

```ini
  [uwsgi]
  #配合nginx使用 注意和/etc/nginx/nginx.conf一致
  socket          = 127.0.0.1:3031
  #项目路径 !!请替换自身的实际目录
  chdir           = /home/*/webs
  #wsgi文件 run就是flask启动文件去掉后缀名 app是run.py里面的Flask对象
  module          = webs:app
  #指定工作进程
  processes       = 4
  #主进程
  master          = true
  #每个工作进程有2个线程
  threads = 2
  #指的后台启动 日志输出的地方
  daemonize       = uwsgi.log
  #保存主进程的进程号
  pidfile = uwsgi.pid
  #虚拟环境环境路径 !!请替换自身的实际目录 见virtualenv部分
  virtualenv = /home/*/webs/.env
```

3. nginx conf
/etc/nginx/nginx.conf

```nginx
location / {
                include uwsgi_params;
                uwsgi_pass 127.0.0.1:3031;
        }
```

4. flask
pip install flask
/home/*/webs/webs.py

```python
#encoding: utf-8
from flask import Flask

app = Flask(__name__)

@app.route("/helloWorld")
def helloWorld():
    return "Hello World"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8987, debug=True)
```
