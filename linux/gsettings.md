
>在桌面版 Ubuntu 中，它的桌面环境设置，包括系统代理设置，都存储在 DConf 数据库，这是简单的键值对存储。如果你想通过系统设置菜单修改桌面属性，更改会持久保存在后端的 DConf 数据库。在 Ubuntu 中更改 DConf 数据库有基于图像用户界面和非图形用户界面的两种方式。系统设置或者 dconf-editor 是访问 DConf 数据库的图形方法，而 gsettings 或 dconf 就是能更改数据库的命令行工具。

```shell
# set & get
gsettings set <schema> <key> <value>
gsettings get <schema> <key>

# 更换壁纸
gsettings set org.gnome.desktop.background picture-uri 'file:///home/jj/Desktop/%E2%98%86V%C2%B7PC%C2%B716(2019.04)/58.jpg'

# 获取代理模式
gsettings get org.gnome.system.proxy mode
```
使用时不需要加sudo