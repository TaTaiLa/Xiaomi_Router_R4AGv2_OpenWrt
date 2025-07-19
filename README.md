# Xiaomi_R4AGv2_OpenWrt自编译固件-防校园网检测
小米路由器4A千兆版v2，OpenWrt固件，带ua2f和rkp-ipid插件，防校园网检测，自编译固件
* 首次登录地址: http://192.168.1.1/cgi-bin/luci/
* 账号: root
* 密码: password

## 参考教程
* [视频教程](https://www.bilibili.com/video/BV1qM411w7W5/)
* [视频教程文档](https://y5hwpw3fsi.feishu.cn/docx/UO0wdHReDoUdewxwlpGckSjin3d)
* [R4AGv2固件编译教程](https://www.bilibili.com/read/cv21136299/)
* [编译视频教程](https://www.bilibili.com/video/BV1854y1W7fu/)

## 编译使用的源码
* https://github.com/coolsnowwolf/lede
* https://github.com/MrTaiKe/Action_OpenWrt_Xiaomi_R4AGv2
* https://github.com/P3TERX/Actions-OpenWrt
* https://github.com/openwrt/openwrt/tree/openwrt-23.05

## 插件源码
* https://github.com/EOYOHOO/UA2F
* https://github.com/EOYOHOO/rkp-ipid

## 使用Breed刷入步骤
* 参考：https://www.right.com.cn/forum/thread-8298740-1-1.html
* 因为这个Breed不为R4A定制的，所以不能使用Breed的网页刷入，会无限重启
1. 搭建一个web服务，将固件和eeprom备份文件放上去
2. 路由器进入Breed后，使用telnet连接到路由器`telnet 192.168.1.1`
3. 执行下载命令`wget 固件的web地址`，比如我执行的命令是：(`wget http://192.168.1.99/openwrt-ramips-mt7621-xiaomi_mi-router-4a-gigabit-v2-squashfs-sysupgrade.bin`)
4. 上面那个命令是将固件下载到路由器上，执行结束后可以看到固件的大小和保存位置，例如我这边出现的命令执行结果如下，固件大小：0xb4046a(11MB)，保存的位置：0x80001000
```
Length: 11797610/0xb4046a (11MB) [application/octet-stream]
Saving to address 0x80001000
```
5. 执行擦除命令`flash erase 0x180000 0xb50000`，这条命令中的`0x180000`就是要擦除的起始位置，`0xb50000`擦除的大小，这个值必须大于你的固件大小，我这里的擦除大小0xb50000就大于固件大小0xb4046a
6. 执行写入命令`flash write 0x180000 0x80001000 0xb4046a`，这条命令中的`0x180000`就是要写入的起始位置，`0x80001000`是固件的保存位置，`0xb4046a`是固件大小，根据你自己的实际情况对命令进行修改
7. 为了解决5G信号弱的问题，需要将备份的eeprom文件也刷进去
8. 执行下载命令`wget eeprom备份文件的web地址`，比如我执行的命令是：(`wget http://192.168.1.99/eeprom.bin`)
9. 执行擦除命令`flash erase 0x50000 0x10000`，eeprom备份文件大小是一样的，直接执行这个命令即可
10. 执行写入命令`flash write 0x50000 0x80001000 0x10000`，直接执行
11. 执行启动命令`boot flash 0x180000`，到这一步就已经刷入成功了，等待路由器启动就行了
12. 但是下次路由器重启时还是不能正常进入系统，需要进入Breed添加环境变量
13. 增加`autoboot.command`，值设为`boot flash 0x180000`
