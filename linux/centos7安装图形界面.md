## 安装

```
yum groupinstall "GNOME Desktop" "Graphical Administration Tools"
```

## 修改CentOS默认启动模式为图形化模式

```
systemctl get-default
systemctl set-default graphical.target 
```

