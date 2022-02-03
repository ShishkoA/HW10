1. enable recovery options for grub, update main configuration file and find new item in grub2 config in /boot.
```bash 
nano /etc/default/grub
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swa$
GRUB_DISABLE_RECOVERY="False"
grub2-mkconfig -o /boot/grub2/grub.cfg
2. modify option vm.dirty_ratio:
   - using echo utility
   echo "95" > /proc/sys/vm/dirty_ratio
   - using sysctl utility
    sysctl -w vm.dirty_ratio=25
   - using sysctl configuration files
[root@localhost linux]# cat /etc/sysctl.d/20-sysctl.conf
vm.dirty_ratio = 35
[root@localhost linux]#
```
​
* extra
1. Inspect initrd file contents. Find all files that are related to XFS filesystem and give a short description for every file.
2. Study dracut utility that is used for rebuilding initrd image. Give an example for adding driver/kernel module for your initrd and recreating it.
3. Explain the difference between ordinary and rescue initrd images.
​
## Selinux
​
Disable selinux using kernel cmdline
```bash
echo 0 > /sys/fs/selinux/enforce
```
​
## Firewalls
​
1. Add rule using firewall-cmd that will allow SSH access to your server *only* from network 192.168.56.0/24 and interface enp0s8 (if your network and/on interface name differs - change it accordingly).
```bash
nano /etc/firewalld/zones/public.xml
<?xml version="1.0" encoding="utf-8"?>
<zone>
  <short>Public</short>
  <description>For use in public areas. You do not trust the other computers on$
  <service name="dhcpv6-client"/>
  <rule family="ipv4">
    <source address="192.168.56.0/24"/>
    <port protocol="tcp" port="22"/>
    <accept/>
  </rule>
</zone>
firewalld-cmd --reload
```
2. Shutdown firewalld and add the same rules via iptables.
```bash
systemctl stop firewalld
systemctl mask firewalld
iptables -A INPUT -s 192.168.56.0/24 -p tcp --dport 22 -j ACCEPT
```
