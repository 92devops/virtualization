#### 安装 VNC

```
~]# yum install tigervnc-server -y
```

#### 创建 Unit 启动脚本

从VNC备份库中复制service文件到系统service服务管理目录,并且命名为 `vncserver@:1.service`

```
~]# cp /lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@:1.service
```

修改vncserver@:1.service文件

```
~]# grep -n "^[^#]" /etc/systemd/system/vncserver@\:1.service
32:[Unit]
33:Description=Remote desktop service (VNC)
34:After=syslog.target network.target
36:[Service]
37:Type=forking
38:User=root
41:ExecStartPre=-/usr/bin/vncserver -kill %i
42:ExecStart=/sbin/runuser -l root -c "/usr/bin/vncserver %i"
43:PIDFile=/root/.vnc/%H%i.pid
44:ExecStop=-/usr/bin/vncserver -kill %i
46:[Install]
47:WantedBy=multi-user.target
~]# systemctl daemon-reload
```

#### 为 `vncserver@:1.service` 设置密码

```
~]# vncpasswd
Password:
Verify:
Would you like to enter a view-only password (y/n)? y
Password:
Verify:
```

#### 启动 VNC

```
~]# systemctl start vncserver@:1.service
~]# systemctl enable vncserver@:1.service
```

```
~]#  netstat -lnt | grep 590*
tcp        0      0 0.0.0.0:5901            0.0.0.0:*               LISTEN     
tcp6       0      0 :::5901                 :::*                    LISTEN
```

#### WINDOWS 使用 VNC 远程连接

root@192.168.124.19:5901
