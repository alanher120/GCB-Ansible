# 基礎服務,磁碟與檔案系統

* version: 1.0

## 手動處理項目

### 安裝時獨立磁區

下列目錄需要獨立磁區處理，建議在安裝作業系統時處理

* /var : 依下列 TWGCB-ID 要求
  * TWGCB-01-003-0002
  * TWGCB-01-008-0008
* /var/tmp : 依下列 TWGCB-ID 要求
  * TWGCB-01-003-0001
  * TWGCB-01-008-0009
* /var/log : 依下列 TWGCB-ID 要求
  * TWGCB-01-003-0003
  * TWGCB-01-008-0013
* /var/log/audit : 依下列 TWGCB-ID 要求
  * TWGCB-01-003-0004
  * TWGCB-01-008-0014
* /home : 依下列 TWGCB-ID 要求
  * TWGCB-01-003-0005
  * TWGCB-01-008-0015

### 藍芽核心模組

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-003-0177

1. 編輯/etc/modprobe.conf檔案，新增或修改成以下內容：

```conf
alias net-pf-31 off
alias bluetooth off
```

### 非root分割區

* 依下列 TWGCB-ID 要求設定
- TWGCB-01-003-0013

編輯/etc/fstab檔案，在掛載點不是「/」且檔案系統為ext2或ext3之列，於第4欄加入「,nodev」

### 設定/tmp

* 設定/tmp目錄之檔案系統，依下列 TWGCB-ID 要求
  * TWGCB-01-008-0004
  * TWGCB-01-012-0004
* 設定/tmp目錄之nodev選項，依下列 TWGCB-ID 要求
  * TWGCB-01-003-0017
  * TWGCB-01-008-0005
  * TWGCB-01-012-0005
* 設定/tmp目錄之nosuid選項，依下列 TWGCB-ID 要求
  * TWGCB-01-003-0018
  * TWGCB-01-008-0006
  * TWGCB-01-012-0006
* 設定/tmp目錄之noexec選項，依下列 TWGCB-ID 要求
  * TWGCB-01-003-0019
  * TWGCB-01-008-0007
  * TWGCB-01-012-0007

#### 說明

##### 設定/tmp目錄之檔案系統

* 這項原則設定決定/tmp目錄是否使用tmpfs檔案系統
* /tmp是具有全域寫入權限之目錄，所有使用者與某些應用程式都將其用於暫存檔案
* tmpfs是一個不存在於實體硬碟上，而是駐存在記憶體的特殊檔案系統，可提供優於傳統機械硬碟的存取速度
* 將/tmp目錄掛載到tmpfs，可於掛載選項上使用noexec選項，指定/tmp目錄不能啟動可執行二進制檔案，使攻擊者不能安裝執行惡意程式以降低風險

##### 設定/tmp目錄之nodev選項

* 這項原則設定決定/tmp目錄是否啟用nodev選項，以禁止在/tmp目錄中建立裝置檔案
* 由於/tmp目錄用途不在於支援裝置，設定nodev選項以確保使用者無法在/tmp目錄建立裝置檔案或存取隨機硬體裝置，降低惡意程式感染風險
* 可設定之參數如下：
  1. dev：允許建立裝置檔案
  2. nodev：禁止建立裝置檔案

##### 設定/tmp目錄之nosuid選項

* 這項原則設定決定/tmp目錄是否啟用nosuid選項，以禁止/tmp目錄存在具有SUID屬性的檔案
* SUID(Set User ID)是針對可執行二進制檔案(Binary file)設計的一項功能，任何使用者執行該程式時，會以該程式之擁有者的身分執行，藉由短暫提權讓一般使用者存取未被授權之檔案
* 可設定之參數如下：
  1. suid：允許存在具有SUID屬性的檔案
  2. nosuid：禁止存在具有SUID屬性的檔案

##### 設定/tmp目錄之noexec選項

* 這項原則設定決定/tmp目錄是否啟用noexec選項，以禁止在/tmp目錄中啟動可執行二進制檔案
* /tmp目錄主要做為暫時存放檔案之用，禁止使用者啟動可執行二進制檔案，以避免感染惡意程式
* 可設定之參數如下：
  1. exec：允許啟動可執行二進制檔案
  2. noexec：禁止啟動可執行二進制檔案

#### 設定方法

執行以下任一操作以設定/tmp目錄之檔案系統：

1. 開啟終端機，執行下列指令，編輯/etc/fstab

```bash
vim /etc/fstab
```

/tmp的掛載設定範例如下：

```conf
# TWGCB-01-008-0004
tmpfs /tmp tmpfs defaults,rw,nosuid,nodev,noexec,relatime 0 0
```

2. 開啟終端機，執行下列指令，運用systemd掛載/tmp：

```bash
systemctl unmask tmp.mount
systemctl enable tmp.mount
```

編輯`/etc/systemd/system/local-fs.target.wants/tmp.mount`以掛載/tmp：

```bash
sudo mkdir -p /etc/systemd/system/local-fs.target.wants/
vim /etc/systemd/system/local-fs.target.wants/tmp.mount
```

```conf
[Mount]
What=tmpfs
Where=/tmp
Type=tmpfs
Options=mode=1777,strictatime,noexec,nodev,nosuid
```

### 設定 /var/tmp

#### 獨立磁區

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0009

#### 將/var/tmp綁定掛載到/tmp

奇爬的設定，當初是誰想的

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-003-0023

##### 說明

▪  這項原則設定決定是否將/var/tmp系統服務暫存資料目錄，綁定掛載到/tmp
▪  啟用這項原則，可將/var/tmp與/tmp暫存資料皆寫入/tmp目錄中，讓/var/tmp與/tmp擁有相同保護機制
▪  若使用者欲透過/var/tmp中之暫存檔案執行惡意程式，將會受限於/tmp權限限制，防止惡意程式執行

##### 設定方法

編輯/etc/fstab，新增或修改成以下內容：

```
/tmp /var/tmp none rw,noexec,nosuid,nod ev,bind 0 0
```

#### 設定/var/tmp目錄之nodev選項

* 依下列 TWGCB-ID 要求設定
- TWGCB-01-008-0010
- TWGCB-01-012-0010

#### 設定/var/tmp目錄之nosuid選項

* 依下列 TWGCB-ID 要求設定
- TWGCB-01-008-0011
- TWGCB-01-012-0011

#### 設設定/var/tmp目錄之noexec選項

* 依下列 TWGCB-ID 要求設定
- TWGCB-01-008-0012
- TWGCB-01-012-0012

#### 設定方法

編輯/etc/fstab檔案

* /etc/fstab

```
/tmp /var/tmp none rw,noexec,nosuid,nod ev,bind 0 0
```

### 設定/var目錄

* 設定/var目錄之檔案系統
  * TWGCB-01-012-0008

#### 設定/var目錄之檔案系統

#gdisk /dev/sda
#n(新增磁區)
#3(建立磁區編號)
#enter(在First sector輸入)
#+256M(在Last sector輸入)
#8e00(變更Hex code，LVM磁區代碼)
#w(寫入磁區分割表格)
#partprobe(強制重新抓磁區分割表格)
#pvcreate /dev/sda3(對/dev/sda3建立PV)
#vgcreate -s 16M varvg /dev/sda3(對/dev/sda3建立1個VG，VG名稱varvg，且指定1個PE大小16MB)
#vgdisplay(顯示目前系統VG狀態，本範例的Total PE數顯示15)
#lvcreate -l 15 -n varlv varvg (從varvg分割出1個LV，LV名稱varlv，並將15個PE配置給varlv)
#mkfs.xfs /dev/varvg/varlv(對/dev/varvg/varlv進行格式化)
接著，編輯/etc/fstab檔案，新增或修改成以下內容：
/dev/varvg/varlv /var xfs defaults  0  0


### 設定/var/log

#### 獨立磁區

* 依下列 TWGCB-ID 要求設定
- TWGCB-01-008-0013

### 設定/var/log/audit

#### 獨立磁區

* 依下列 TWGCB-ID 要求設定
- TWGCB-01-008-0014

### 設定/home

#### 獨立磁區

* 依下列 TWGCB-ID 要求設定
- TWGCB-01-008-0015

#### 設定/home目錄之nodev選項

* 依下列 TWGCB-ID 要求設定
- TWGCB-01-008-0016
- TWGCB-01-012-0016

#### 設定方法

* 編輯`/etc/fstab`檔案，在掛載點為/home列，於第4欄加入「,nodev」

```conf
/dev/mapper/ol-home     /home                   xfs     defaults,nodev        0 0
```

* 開啟終端機，執行下列指令，重新掛載/home：

```bash
systemctl daemon-reload
mount -o remount,nodev /home
```

### 設定/dev/shm

#### 獨立磁區

* 依下列 TWGCB-ID 要求設定
- TWGCB-01-008-0017
- TWGCB-01-012-0017

#### 設定/dev/shm目錄之nodev選項

* 依下列 TWGCB-ID 要求設定
- TWGCB-01-003-0020

#### 設定/dev/shm目錄之nosuid選項

* 依下列 TWGCB-ID 要求設定
- TWGCB-01-003-0021
- TWGCB-01-008-0018
- TWGCB-01-012-0018

#### 設設定/dev/shm目錄之noexec選項

* 依下列 TWGCB-ID 要求設定
- TWGCB-01-003-0022
- TWGCB-01-008-0019
- TWGCB-01-012-0019


#### 設定方法

* 編輯`/etc/fstab`檔案，在掛載點為/dev/shm列，於第4欄加入「,nodev」

```conf
tmpfs /dev/shm tmpfs defaults,nodev,nosuid,noexec 0 0
```

* 開啟終端機，執行下列指令，重新掛載/home：

```bash
systemctl daemon-reload
mount -o remount,nodev,nosuid,noexec /dev/shm
```

### 設定可攜式儲存裝置

如果有 USB 等裝置一下列要求設定

* 設定可攜式儲存裝置之nodev選項，依下列 TWGCB-ID 要求設定
  * TWGCB-01-003-0014
  * TWGCB-01-008-0020
  * TWGCB-01-012-0020
* 設定可攜式儲存裝置之nosuid選項，依下列 TWGCB-ID 要求設定
  * TWGCB-01-003-0015
  * TWGCB-01-008-0021
  * TWGCB-01-012-0021
* 設定可攜式儲存裝置之noexec選項，依下列 TWGCB-ID 要求設定
  * TWGCB-01-003-0016
  * TWGCB-01-008-0022
  * TWGCB-01-012-0022

### 設定使用者家目錄

* 設定使用者家目錄之nodev選項，依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0023
  * TWGCB-01-012-0023
* 設定使用者家目錄之nosuid選項，依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0024
  * TWGCB-01-012-0024
* 設定使用者家目錄之noexec選項，依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0025
  * TWGCB-01-012-0025

### 設定NFS檔案系統

* 設定NFS檔案系統之nodev選項，依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0026
  * TWGCB-01-012-0026
* 設定NFS檔案系統之nosuid選項，依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0027
  * TWGCB-01-012-0027
* 設定NFS檔案系統之noexec選項，依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0028
  * TWGCB-01-012-0028
  