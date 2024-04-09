# 掛載CD

redhat 發行板會需要掛載 CD，指令如下:

```bash
[root@template-redhat-6 ~]# mount /dev/cdrom /mnt
mount: block device /dev/sr0 is write-protected, mounting read-only
[root@template-redhat-6 ~]# cat /mnt/.discinfo
1529061393.073916
Red Hat Enterprise Linux 6.10
x86_64
1
```

* /etc/yum.repos.d/media.repo

```
[InstallMedia]
name=Red Hat Enterprise Linux 6.10
baseurl=file:///mnt/
mediaid=1529061393.073916
metadata_expire=-1
gpgcheck=0
cost=500
```

