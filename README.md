### PXE Boot ###

Installation experiments Oracle Linux 8 and 9 using PXE (Boot, UEFI) and Kickstart.
PXE-Server was deployed on Oracle Linux 8.10
In all configs:
* 10.1.1.1 is the PXE Server address
* /var/lib/tfpboot - folder with boot files
* /var/lib/tfpboot/images - folder with system installation files.

Setting:

1) Install packages. Configuring firewall.

```
dnf install -y dhcp-server filewalld rsync tftp-server xinetd syslinux nginx 
firewall-cmd --permanent --add-service=dhcp
firewall-cmd --permanent --add-service=tftp
firewall-cmd --permanent --add-service=http
```

2) Configuring dhcp

* Copy the file from the repository /src/etc/dhcp/dhcpd.conf to /etc/dhcp/dhcp.conf

3) Configuring tftp.

* Copy the file from the repository /src/etc/xinetd.d/tftp to /etc/xinetd.d/tftp

4) Setting up boot files:: 

* Copy the file from the repository /src/var/lib/tfpboot to /var/lib/tfpboot

5) Copy installation files from ISO.
```
# For Oracle Linux 8
mount -o loop /dev/cdrom /mnt
rsync -av /mnt/. /var/lib/tfpboot/images/ol8/
umount /mnt

# For Oracle Linux 9
mount -o loop /dev/cdrom /mnt
rsync -av /mnt/. /var/lib/tfpboot/images/ol9/
umount /mnt
```

6) Configuring kickstart for PXE:
   
* Copy the file from the repository /src/var/lib/tfpboot/ol8/ks.cfg to /var/lib/tfpboot/ol8/ks.cfg
* Copy the file from the repository /src/var/lib/tfpboot/ol9/ks.cfg to /var/lib/tfpboot/ol9/ks.cfg

7) Making a symbolic link for nginx:

```
ln -s /var/lib/tftpboot/images /usr/share/nginx/html/
```

8) Enable services and reboot:

```
systemctl enable nginx
systemctl enable dhcpd
systemctl enable xinetd
reboot
```


