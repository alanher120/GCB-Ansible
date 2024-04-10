# 系統設定與維護-System setup and maintenance(SSM)

* version: 

## 手動設定項目

### GPG簽章驗證

* 依下列 TWGCB-ID 要求設定
- TWGCB-01-008-0032

#### 設定方法

* 編輯/etc/yum.conf檔案，在「main」段落新增或修改成以下內容：

```conf
gpgcheck=1
localpkg_gpgcheck=1
```

* 編輯/etc/yum.repos.d目錄下的所有檔案，將全部的「gpgcheck」設定，修改成以下內容：

```conf
gpgcheck=1
localpkg_gpgcheck=1
```

### 設定sudo指令使用pty,

* 設定sudo指令使用pty
  * 依下列 TWGCB-ID 要求設定
    * TWGCB-01-008-0034
* sudo自定義日誌檔案
  * 依下列 TWGCB-ID 要求設定
    * TWGCB-01-008-0035

#### 設定方法

開啟終端機，執行「visudo -f」指令編輯/etc/sudoers檔案或 /etc/sudoers.d/目錄下的檔案，新增以下內容：
Defaults use_pty

* ex: /etc/sudoers.d/gcb

```bash
visudo -f /etc/sudoers.d/gcb
```

```conf
# TWGCB-01-008-0034
Defaults use_pty
# TWGCB-01-008-0035
Defaults logfile="/var/log/sudo.log"
```

### 開機載入程式設定檔之所有權

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0038

#### 設定方法

* 若是基於BIOS的系統，開啟終端機，執行以下指令，設定GRUB設定檔之擁有者與群組：

```bash
chown root:root /boot/grub2/grub.cfg
chown root:root /boot/grub2/grubenv
[ -f /boot/grub2/user.cfg ] && chown root:root /boot/grub2/user.cfg
```

* 若是基於UEFI的系統，編輯/etc/fstab檔案，在掛載點為「/boot/efi」列，於第4欄加入「uid=0, and gid=0」，設定GRUB設定檔之擁有者與群組，範例如下：

/etc/fstab

```
<device> /boot/efi vfat umask=0027,shortname=winnt, uid=0,gid=0 0 2
```

### 開機載入程式設定檔之權限

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0039

* 若是基於BIOS的系統，開啟終端機，執行以下指令，設定GRUB設定檔之權限為600或更低權限：

```bash
chmod 600 /boot/grub2/grub.cfg
chmod 600 /boot/grub2/grubenv
[ -f /boot/grub2/user.cfg ] && chmod 600 /boot/grub2/user.cfg
```

* 若是基於UEFI的系統，編輯/etc/fstab檔案，在掛載點為「/boot/efi」列，於第4欄加入「fmask=0177」，設定GRUB設定檔之權限，範例如下：

```
<device> /boot/efi vfat umask=0027,shortname=winnt,fmask=0177 0 2        
```

### 開機載入程式之密碼

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0040

* 開啟終端機，執行以下指令，使用grub2-setpassword建立一組密碼：

```bash
#grub2-setpassword
Enter password: (輸入密碼)
Confirm password: (再次輸入密碼)
```

* 執行以下指令更新grub2設定檔：

```bash
grub2-mkconfig -o "$(dirname "$(find /boot -type f \( -name 'grubenv' -o -name 'grub.conf' -o -name 'grub.cfg' \) -exec grep -Pl '^\h*(kernelopts=|linux|kernel)' {} \;)")/grub.cfg"
```