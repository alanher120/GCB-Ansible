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

下列是 RHEL6 RHEL7 設定方式

```
[InstallMedia]
name=Red Hat Enterprise Linux 6.10
baseurl=file:///mnt/
mediaid=1529061393.073916
metadata_expire=-1
gpgcheck=0
cost=500
```

下列是 RHEL8 設定方式

```
[dvd-BaseOS]
name=DVD for RHEL8 – BaseOS
baseurl=file:///mnt/BaseOS
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release

[dvd-AppStream]
name=DVD for RHEL8 – AppStream
baseurl=file:///mnt/AppStream
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
```