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

### 設定全系統加密原則

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0044

* 開啟終端機，執行以下指令，確認全系統加密原則是否為FUTURE或FIPS：

```bash
grep -E -i '^\s*(FUTURE|FIPS)\s*(\s+#.*)?$' /etc/crypto-policies/config
```

* 若不是使用FUTURE或FIPS原則，請執行以下任一操作，變更全系統加密原則：

1. 執行以下指令，將系統設定為「FUTURE」原則：

```bash
update-crypto-policies --set FUTURE
```

接續執行以下指令，以套用更新後之全系統加密原則：

```bash
update-crypto-policies
```

2. 執行以下指令，將系統設定為「FIPS」原則，並重新開機，以使生效：

```bash
fips-mode-setup --enable
```

### 其他使用者寫入具有全域寫入權限的檔案

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0061

* 開啟終端機，執行以下指令，在系統上所有磁區(partition)，逐一檢查磁區所掛載之檔案目錄(如根目錄)，找出具有全域寫入權限(World-writable)的檔案：

```bash
find (partition) -xdev -type f -perm -0002
```

* 針對所找到的檔案，執行下列指令，以移除其他身分寫入權限：

```bash
chmod o-w (檔案名稱)
```

### 檢查所有檔案與目錄之擁有者

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0062

* 開啟終端機，執行以下指令，在系統上所有磁區(partition)，逐一檢查磁區所掛載之檔案目錄(如根目錄)，找出擁有者不是合法使用者之檔案或目錄：

```bash
find (partition) -xdev -nouser
```

* 針對所找到的檔案與目錄指定合法使用者或移除：

```bash
chown (使用者) (檔案名稱或目錄名稱)
```

或

```bash
rm (檔案名稱或目錄名稱)
```

### 檢查所有檔案與目錄之擁有群組

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0063

* 開啟終端機，執行以下指令，在系統上所有磁區(partition)，逐一檢查磁區所掛載之檔案目錄(如根目錄)，找出擁有群組不是合法群組之檔案或目錄：

```bash
find (partition) -xdev -nogroup
```

* 針對所找到的檔案與目錄指定合法群組或移除：

```bash
chgrp (群組) (檔案名稱或目錄名稱)
```

或

```bash
rm (檔案名稱或目錄名稱)
```

### 所有具有全域寫入權限目錄之擁有者

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0064

* 開啟終端機，執行以下指令，在系統上所有磁區(partition)，逐一檢查磁區所掛載之檔案目錄(如根目錄)，找出一般使用者(UID大於999)具有全域寫入(World-writable)權限的目錄

```bash
find (partition) -xdev -type d -perm -0002 -uid +999 -print
```

* 針對所找到的目錄，執行以下指令，設定目錄擁有者為root或其他系統帳號

```bash
chown root (目錄名稱)
```

### 所有具有全域寫入權限目錄之擁有群組

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0065

* 開啟終端機，執行以下指令，在系統上所有磁區(partition)，逐一檢查磁區所掛載之檔案目錄(如根目錄)，找出一般使用者群組(GID大於999)具有全域寫入(World-writable)權限的目錄

```bash
find (partition) -xdev -type d -perm -0002 -gid +999 -print
```

* 針對所找到的目錄，執行以下指令，設定目錄擁有群組為root或其他系統群組(sys、bin或應用程式群組)：

```bash
chgrp root (目錄名稱)
```

### 系統命令檔案權限

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0066

* 開啟終端機，執行以下指令，檢查系統命令檔案之權限：

```bash
find -L /bin /sbin /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin -perm /0022 -exec ls -l {}\;
```

* 若找到系統命令檔案權限高於755，執行以下指令，設定系統命令檔案之權限為755或更低權限：

```bash
chmod 755 (系統命令檔案名稱)
```

### 系統命令檔案擁有者

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0067

* 開啟終端機，執行以下指令，檢查系統命令檔案擁有者：

```bash
find -L /bin /sbin /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin ! -user root -exec ls -l {}\;
```

* 若找到系統命令檔案擁有者非root，執行以下指令，設定系統命令檔案擁有者為root：

```bash
chown root (系統命令檔案名稱)
```

### 系統命令檔案擁有群組

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0068

* 開啟終端機，執行以下指令，檢查系統命令檔案擁有群組：

```bash
find -L /bin /sbin /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin ! -group root ! -group tty ! -group slocate ! -group lock -exec ls -l {}\;
```

* 若找到系統命令檔案擁有群組非root，執行以下指令，設定系統命令檔案擁有群組為root：

```bash
chgrp root (系統命令檔案名稱)
```

### 程式庫檔案權限

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0069

* 開啟終端機，執行以下指令，檢查程式庫檔案之權限：

```bash  
find -L /lib /lib64 /usr/lib /usr/lib64 -perm /0022 -type f -exec ls -l {}\;
```

* 若找到程式庫檔案權限高於755，執行以下指令，設定程式庫檔案之權限為755或更低權限：

```bash
chmod 755 (程式庫檔案名稱)
```

### 程式庫檔案擁有者

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0070

* 開啟終端機，執行以下指令，檢查程式庫檔案擁有者：

```bash
find -L /lib /lib64 /usr/lib /usr/lib64 ! -user root -exec ls -l {}\;
```

* 若找到程式庫檔案擁有者非root，執行以下指令，設定程式庫檔案擁有者為root：

```bash
chown root (程式庫檔案名稱)
```

### 程式庫檔案擁有群組

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0071

* 開啟終端機，執行以下指令，檢查程式庫檔案擁有群組：

```bash
find -L /lib /lib64 /usr/lib /usr/lib64 ! -group root -exec ls -l {}\;
```

* 若找到程式庫檔案擁有群組非root，執行以下指令，設定程式庫檔案擁有群組為root：

```bash
chgrp root (程式庫檔案名稱)
```

### 帳號不使用空白密碼

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0072

* 開啟終端機，執行以下指令，列出使用空白密碼之帳號：

```bash
awk -F: '($2 == "" ) { print $1 " does not have a password "}' /etc/shadow
```

* 若有使用空白密碼之帳號，則執行下列指令以設定密碼：

```bash
passwd (帳號名稱)
```

或執行下列指令以鎖定帳號：

```bash
passwd -l (帳號名稱)
```

### root帳號的路徑變數

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0073


