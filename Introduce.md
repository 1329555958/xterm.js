
# 执行

```
#!/bin/bash
MASTER=
PORT=3000
HOSTNAME=`hostname`
LOG_DIR=/opt/logs
IP=`/sbin/ifconfig -a|grep inet|grep -v 127.0.0.1|grep -v 172.17| grep -v inet6 | awk '{print $2}' | tr -d "addr:"`

case "$1" in
    start)
        docker rm -f spider-web
        docker run -d -p $PORT:3000 -e PORT=$PORT -e HOSTNAME=$HOSTNAME -e IP=$IP -e MASTER=$MASTER -v $LOG_DIR:/opt/logs:ro --name spider-web spider-web start
        nohup docker logs -f spider-web > spider-web.log 2>&1 &
        tail -f spider-web.log
        ;;
 
    stop)
        docker stop spider-web
        echo stop success
        ;;
 
    restart)
        $0 stop
        sleep 2
        $0 start
        ;;
 
    *)
        echo "usage: $0 start|stop|restart"
        ;;
 
esac
```
# 参数说明
- MASTER
用于查看所有主机日志的服务地址
- PORT
默认3000，本机对外暴露的端口
- HOSTNAME
默认是hostname命令返回的值，在日志查看界面用于区分主机，可以直接指定固定值
- IP
对外暴露的IP，如果有多个IP时需要指定IP
- LOG_DIR
对外提供查看的日志目录
