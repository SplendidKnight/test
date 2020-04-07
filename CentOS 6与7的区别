### 1. 字符集

[CentOS](https://www.linuxidc.com/topicnews.aspx?tid=14) 6

- 方法: /etc/sysconfig/i18n

CentOS 7

- 方法1: localectl set-locale LANG=en_GB.utf8
- 方法2: /etc/locale.conf中的LANG=

### 2. 主机名

CentOS 6

- 在线生效: hostname
- 重启生效: /etc/sysconfig/network中的HOSTNAME=

CentOS 7

- 在线+重启生效: hostnamectl set-hostname

### 3. 时区

CentOS 6

- 方法: ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

CentOS 7

- 方法1: 同CentOS 6
- 方法2: timedatectl set-timezone Asia/Shanghai

### 4. 时间同步

CentOS 6

- 逐步: ntpd或ntpdate
- 直接: ntpdate -b（通常加到crontab）

CentOS 7

- 方法1: systemctl start chronyd

- 方法2: timedatectl set-ntp yes（同systemctl start chronyd）

  > 可以通过timedatectl | grep "NTP synchronized"判断当前时间是否已同步
  > 不建议用ntpd和ntpdate，[RedHat](https://www.linuxidc.com/topicnews.aspx?tid=10)强烈推荐chrony，可用于网络不稳定的环境
  > chrony.conf关键参数makestep 1.0 -1
  > [ntpd和chronyd区别](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_the_chrony_suite#sect-differences_between_ntpd_and_chronyd)

### 5. 手动更改时间

CentOS 6

- 方法: date -s "2018-07-08 11:11:11"

CentOS 7

- 方法1: 同CentOS 6
- 方法2: timedatectl set-time "2018-07-08 11:11:12"（前提是timedatectl set-ntp false）

### 6. 单用户修改密码

CentOS 6: `grub`界面键入`e`，在`kernel`行最后加`1`，键入`b`启动进入单用户模式，之后输入`passwd`修改密码

CentOS 7: `grub`界面键入`e`，在`linux16`行上将`ro`改为`rw`，并在当前行最后加`init=/bin/sh`，键入`ctrl-x`进入，之后输入`passwd`修改密码

- 如果有开启selinux，则需要在修改密码后，重启前，执行`touch /.autorelabel`
- passwd执行后，最好执行sync，防止强制重启导致修改密码没有落地

### 7. grub添加参数

CentOS 6:

- /boot/grub/grub.conf的kernel中加入需要添加的参数

CentOS 7:

- 步骤1：/etc/default/grub的GRUB_CMDLINE_LINUX中加入需要添加的参数
- 步骤2：grub2-mkconfig -o /boot/grub2/grub.cfg

### 8. 查看开机记录

CentOS 6: last

CentOS 7: journalctl --list-boots或last

### 9. 修改启动内核

1. 查看当前启动内核
   - CentOS 6: cat /boot/grub/grub.conf中的default
   - CentOS 7: grub2-editenv list
2. 查看有哪些内核
   - CentOS 6: cat /boot/grub/grub.conf | sed -n '/^title/s/^title //p'
   - CentOS 7: cat /boot/grub2/grub.cfg | grep '^menuentry' | awk -F"'" '{print $2}'
3. 设置启动内核
   - CentOS 6:
     - 修改/boot/grub/grub.conf中的default
   - CentOS 7:
     - 步骤1：确保/etc/default/grub中的`GRUB_DEFAULT`为saved
     - 步骤2：grub2-set-default 'CentOS Linux (3.10.0-693.el7.x86_64) 7 (Core)'

### 10. rc.local

执行顺序

- CentOS 6: 串行的最后一个执行
- CentOS 7: 和其他服务并行执行

可执行权限

- CentOS 6: 默认有可执行权限
- CentOS 7: 默认没有可执行权限（官方不推荐使用rc.local），需要自行增加（chmod +x /etc/rc.d/rc.local）

CentOS 7的注意事项

- rc.local由rc-local.service执行，并行执行，仅能保证在network之后启动，因此建议rc.local里增加sleep 10来尽可能在最后执行
- 需要在rc.local的最后一行增加exit 0，否则可能导致已启动的进程被关闭（echo 'exit 0' >> /etc/rc.d/rc.local）
- 建议尽量使用systemd来配置服务，不要使用rc.local

### 11. limit配置

CentOS 6:

- 全局设置: 没有全局设置的方法（/etc/security/limits.conf仅针对使用pam的进程，且有加载pam_limits.so的模块，因为limits.conf是pam_limits.so的配置文件）
- 服务设置: 只能在服务启动前设置ulimit，才能在启动后看到效果

CentOS 7:

- 全局设置: /etc/systemd/system.conf里DefaultLimitNOFILE=65535
- 服务设置: [Service]里增加LimitNOFILE=65535

### 12. yum仅使用ipv4

CentOS 6: yum没有自带方法

CentOS 7: yum.conf里增加ip_resolve=4

### 13. 彻底禁用ipv6

CentOS 6和CentOS 7相同

- 在grub上增加ipv6.disable=1

查看是否彻底关闭

- sysctl -a | grep -i ipv6如果没有任何输出，则表示彻底关闭

### 14. 防火墙

CentOS 6

- 默认开启iptables服务，只不过默认没有条目

CentOS 7

- 默认安装并开启firewalld服务
- 默认不安装iptables服务（yum install iptables-services）

### 15. NetworkManager

CentOS 6: 默认未安装

CentOS 7: 默认安装并启动

### 16. 网卡名

CentOS 6:

- 系统安装完，默认是em1开始，这其实是在装机完成时在udev里做的绑定
- 把/etc/udev/rules.d/70-persistent-net.rules内容清空，则恢复成eth0开始编号

CentOS 7:

- 不再通过udev绑定网卡名，默认是em1开始，有的是eno、enp、ens等名字
- 如果想恢复eth0，则/etc/default/grub里增加net.ifnames=0 biosdevname=0
- 如果想让CentOS 6的网卡名不受udev影响，达到CentOS 7的效果，则删除3个文件即可

```
rm -f /etc/udev/rules.d/70-persistent-net.rules
rm -f /lib/udev/write_net_rules
rm -f /lib/udev/rules.d/75-persistent-net-generator.rules
```

网卡名规则

- eno：主板板载网卡
- enp：独立网卡（PCI网卡）
- ens：热插拔网卡（usb之类）

### 17. CPU频率(performance)

CentOS 6

- 始终：2.1GHz

CentOS 7:

- 空闲：1.2GHz

- sysbench 1线程压测：一个物理cpu所有核的频率瞬间增长，其中最高打到2.6GHz

- sysbench 42线程压测：所有cpu所有核的频率全部达到2.4GHz

- 若要和6一样保持频率，则在/etc/default/grub里增加intel_pstate=disable（不建议，因为性能没有任何提升，还在某些情况下降）
