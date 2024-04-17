# 帳號與存取控制

## 手動處理項目

### TWGCB-01-003-0063

* 執行下列指令，確認已存在wheel群組

```bash
grep ^wheel /etc/group | wc -l
```

* 若不存在wheel群組，執行下列指令以建立wheel群組

```bash
groupadd wheel
```

### TWGCB-01-003-0065: 非root系統帳號登入方式

* 執行下列指令，列出所有使用者、UID及shell：

```bash
awk -F： '{print $1 "：" $3 "：" $7}' /etc/passwd
```

* 針對UID小於500之非root系統帳號，執行下列指令進行帳號鎖定與shell設定

```bash
# 將密碼凍結，使該帳號無法登入
usermod -L [username]

# 設定shell
usermod -s /sbin/nologin [username]
```

### TWGCB-01-003-0066: 使用空白密碼之帳號登入方式

* 執行下列指令列出使用空白密碼之帳號

```bash
awk -F: '($2 == "") {print}' /etc/shadow
```

* 若有使用空白密碼之帳號，則執行下列指令以設定密碼：

```bash
passwd [username]
```

### TWGCB-01-003-0067: 所有帳號的密碼遮蔽

* 執行下列指令，確認所有帳號的密碼欄位顯示為x，沒有儲存密碼雜湊於/etc/passwd中：

```bash
awk -F： '($2 != "x") {print}' /etc/passwd
```
* 若有帳號的密碼欄位出現非x值，請執行下列指令進行密碼設定：

```bash
passwd [username]
```

### TWGCB-01-003-0068: UID=0之帳號

* 執行下列指令，列出UID=0之帳號：

```bash
awk -F: '($3 == "0") {print}' /etc/passwd
```

* 若存在非root帳號，可執行下列指令移除帳號或重新設定UID：

```bash
userdel [username]
# 或
usermod -u [UID] [username]
```

### TWGCB-01-003-0073: /etc/group檔案行首的「+」符號

* 執行下列指令，確認/etc/group 檔案行首是否存在「+」符號：

```bash
grep "^+:" /etc/group
```

* 如果有，請編輯/etc/group檔案，將行首為「+」符號之列移除

### TWGCB-01-003-0074: /etc/passwd 檔案行首的「+」符號

* 執行下列指令，確認/etc/passwd 檔案行首是否存在「+」符號：

```bash
grep "^+:" /etc/passwd
```

* 如果有，請編輯/etc/passwd 檔案，將行首為「+」符號之列移除

### TWGCB-01-003-0075: /etc/shadow 檔案行首的「+」符號

* 執行下列指令，確認/etc/shadow 檔案行首是否存在「+」符號：

```bash
grep "^+:" /etc/shadow
```

* 如果有，請編輯/etc/shadow 檔案，將行首為「+」符號之列移除

### root帳號的路徑變數

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-003-0085

* 執行下列指令以顯示 PATH變數內容：

```bash
echo $PATH
```

* 如出現「.」、「..」、路徑開頭不是「/」及空元素等內容，請編輯/etc/profile檔案進行修改

### TWGCB-01-003-0086: root帳號的路徑變數不包含world-writable或group-writable目錄

* 執行下列指令以顯示 PATH變數內容：

```bash
echo $PATH
```

* 如出現具有world-writable權限或group-writable權限之目錄，請編輯/etc/profile檔案進行修改，或執行下列指令變更目錄權限：

```bash
chmod g-w (目錄)
#或
chmod o-w (目錄)
```

### 使用者家目錄權限

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-003-0087

* 針對每個使用者的家目錄執行下列指令：

```bash
ls -ld /home/(使用者帳號)
```

* 如出現具有world-readable或group-writable權限之家目錄，請執行下列指令變更目錄權限：

```bash
chmod g-w /home/(使用者帳號)
chmod o-rwx /home/(使用者帳號)
```

### 以密碼保護GRUB開機載入程式

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-003-0095

* 請先選擇一組密碼，執行下列指令產生雜湊密碼：

```bash
#grub-md5-crypt
Password：(輸入密碼)
Retype Password：(再次輸入密碼)
```

* 編輯/etc/grub.conf檔案，將上一個步驟產生的雜湊密碼，新增或修改成以下內容：

```bash
password --md5 (雜湊密碼)
```

### 以密碼保護單一使用者模式

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-003-0096

* 編輯/etc/inittab檔案，新增或修改成以下內容：

```
~:S:wait:/sbin/sulogin
```

### 強制root密碼須符合密碼規則

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0209
  * TWGCB-01-012-0207

* 開啟終端機，執行以下指令，建立客製化profile：

```bash
authselect create-profile <profile名稱> -b sssd --symlink-meta
authselect select custom/<profile名稱> with-sudo with-faillock without-nullok
```

### 強制執行密碼歷程記錄

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0222
  * TWGCB-01-012-0220

* 開啟終端機，執行以下指令，建立客製化profile：

```bash
authselect create-profile <profile名稱> -b sssd --symlink-meta
authselect select custom/<profile名稱> with-sudo with-faillock without-nullok
```

### 密碼雜湊演算法

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0224
  * TWGCB-01-012-0222

* 開啟終端機，執行以下指令，建立客製化profile：

```bash
authselect create-profile <profile名稱> -b sssd --symlink-meta
authselect select custom/<profile名稱> with-sudo with-faillock without-nullok
```

### 密碼最短使用期限

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0226

* 針對進行上述設定前就已存在之使用者帳號，須再執行下列指令，才能使密碼到期前14天提醒使用者變更密碼：

```bash
chage --warndays 14 (使用者帳號名稱)
```

### 密碼最長使用期限

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-003-0071
  * TWGCB-01-008-0227
  * TWGCB-01-012-0225

* 針對進行上述設定前就已存在之使用者帳號，須再執行下列指令，才能使密碼最長使用期限變更為90天：

```bash
chage --maxdays 90 (使用者帳號名稱)
```

### 密碼到期後，帳號停用前的天數

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0228
  * TWGCB-01-012-0226

* 針對進行上述設定前就已存在之使用者帳號，須再執行下列指令，才能使密碼到期後，超過30天就進行帳號停用：

```bash
chage --inactive 30 (使用者帳號名稱)
```

### 要求使用者必須經過身分驗證才能提升權限

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0231
  * TWGCB-01-012-0229

* 開啟終端機，執行以下指令，尋找「NOPASSWD」或「!authenticate」：

```bash
grep -i '(nopasswd|!authenticate)' /etc/sudoers /etc/sudoers.d/*
```

* 執行「visudo -f」指令編輯/etc/sudoers檔案或 /etc/sudoers.d/目錄下的檔案，針對包含「NOPASSWD」或「!authenticate」的指令行，予以註解(#)或刪除

### 可使用su指令之群組

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0243
  * TWGCB-01-012-0241

* 編輯/etc/group檔案之wheel群組設定，建立允許使用su指令的使用者帳號清單，如下所示：

```bash
wheel:x:GID:root,(使用者帳號清單)
```

* 範例：如欲在wheel群組(GID為10)建立允許root、user1及user2帳號使用su指令，內容如下：

```bash
wheel:x:10:root,user1,user2
```

### GNOME 桌面相關

* 使用者會談鎖定
  * TWGCB-01-008-0234
  * TWGCB-01-012-0232
* GNOME使用者會談逾時時間
  * TWGCB-01-008-0235
  * TWGCB-01-012-0233
* 禁止GNOME使用者自動登入
  * TWGCB-01-008-0236
  * TWGCB-01-012-0234
* 防止修改圖形使用者介面(GUI)設定
  * TWGCB-01-008-0239
  * TWGCB-01-012-0237

開啟終端機，執行以下指令，建立或編輯資料庫，設定螢幕保護裝置：

* /etc/dconf/db/local.d/00-screensaver

```bash
## 使用者會談鎖定: TWGCB-01-008-0234, TWGCB-01-012-0232
[org/gnome/desktop/screensaver]
##Set this to true to lock the screen when the screensaver activates
lock-enabled=true

## GNOME使用者會談逾時時間: TWGCB-01-008-0235, TWGCB-01-012-0233
[org/gnome/desktop/session]
##Set the lock time out to 900 seconds before the session is considered idle
idle-delay=uint32 900
```

* /etc/gdm/custom.conf

```conf
## 禁止GNOME使用者自動登入: TWGCB-01-008-0236, TWGCB-01-012-0234
[daemon]
AutomaticLoginEnable=false
```

* /etc/dconf/db/local.d/locks/session

```conf
# 防止修改圖形使用者介面(GUI)設定: TWGCB-01-008-0239, TWGCB-01-012-0237
/org/gnome/desktop/session/idle-delay
/org/gnome/desktop/screensaver/lock-enabled
/org/gnome/desktop/screensaver/lock-delay
/org/gnome/desktop/lockdown/disable-lock-screen
```

* 執行以下指令更新系統資料庫：

```bash
dconf update
```
