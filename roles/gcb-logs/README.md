# 日誌與稽核

* version: 1.0

## 手動處理項目

### TWGCB-01-003-0154: 記錄特權指令使用情形

1. 執行下列指令，針對每個磁區(PART)的特權指令產生稽核規則：

```bash
find PART -xdev \( -perm -4000 -o -perm -2000 \) -type f | awk '{print \ "-a always,exit -F path=" $1 " -F perm=x -F auid>=500 -F auid!=4294967295 \ -k privileged" }'
```

2. 將上述指令所產出之規則內容新增至/etc/audit/audit.rules檔案中

### TWGCB-01-003-0155: 記錄資料匯出至媒體


1. 先使用uname -m，查詢作業系統是32位元(b32)或64位元(b64)
2. 編輯/etc/audit/audit.rules檔案，新增或修改成以下內容(32位元系統請將ARCH改為b32，64位元系統請將ARCH改為b64)：

```bash
-a always,exit -F arch=ARCH -S mount -F auid>=500 -F auid!=4294967295 -k export
```

### TWGCB-01-003-0156: 記錄檔案刪除事件

1. 先使用uname -m，查詢作業系統是32位元(b32)或64位元(b64)
2. 編輯/etc/audit/audit.rules檔案，新增或修改成以下內容(32位元系統請將ARCH改為b32，64位元系統請將ARCH改為b64)：

```bash
-a always,exit -F arch=ARCH -S unlink -S unlinkat -S rename -S renameat -F auid>=500 -F auid!=4294967295 -k delete
```

### TWGCB-01-003-0158: 記錄核心模組掛載與卸載事件

1. 先使用uname -m，查詢作業系統是32位元(b32)或64位元(b64)
2. 編輯/etc/audit/audit.rules檔案，新增或修改成以下內容(32位元系統請將ARCH改為b32，64位元系統請將ARCH改為b64)：

```bash

-w /sbin/insmod -p x -k modules
-w /sbin/rmmod -p x -k modules
-w /sbin/modprobe -p x -k modules
-a always,exit -F arch=ARCH -S init_module -S delete_module -k modules
```

### 日誌與稽核

* 稽核auditd服務啟動前之程序
  * TWGCB-01-008-0134
* 稽核待辦事項數量限制
  * TWGCB-01-008-0135

* /etc/default/grub

1. 新增「,audit=1, audit_backlog_limit=8192」
2. 移除所有「selinux=0」與「enforcing=0」內容

```bash
GRUB_CMDLINE_LINUX="audit=1 audit_backlog_limit=8192"
```

```bash
grub2-mkconfig -o /boot/grub2/grub.cfg
```

### 稽核日誌檔案、目錄權限 

* 稽核日誌檔案所有權
  * TWGCB-01-008-0137
* 稽核日誌檔案權限
  * TWGCB-01-008-0138
* 稽核日誌目錄所有權
  * TWGCB-01-008-0139
* 稽核日誌目錄權限
  * TWGCB-01-008-0140

```bash
chown root:root (稽核日誌檔案名稱) # ex: chown root:root /var/log/audit/audit.log
chmod 600 (稽核日誌檔案名稱) # ex: chmod 700 /var/log/audit/audit.log
chown root:root (稽核日誌目錄名稱) # ex: chown root:root /var/log/audit
chmod 700 (稽核日誌目錄名稱) # ex: chmod 700 /var/log/audit
```

### 稽核工具權限

* TWGCB-01-008-0143

* 開啟終端機，執行以下指令，檢查稽核工具權限：

```bash
#stat -c "%a %n" /sbin/auditctl /sbin/aureport /sbin/ausearch /sbin/autrace /sbin/auditd /sbin/audisp-remote /sbin/audisp-syslog /sbin/augenrules /sbin/rsyslogd 2>/dev/null
```

* 若有稽核工具權限高於750，則執行以下指令，設定稽核工具權限為750或更低權限：

```bash
chmod 750 (稽核工具名稱)
```

### 稽核工具所有權

* TWGCB-01-008-0144

* 開啟終端機，執行以下指令，檢查稽核工具擁有者與群組：

```bash
#ls -al /sbin/auditctl /sbin/aureport /sbin/ausearch /sbin/autrace /sbin/auditd /sbin/audisp-remote /sbin/audisp-syslog /sbin/augenrules /sbin/rsyslogd 2>/dev/null
```

* 若有稽核工具擁有者與群組非root，則執行以下指令，設定稽核工具擁有者與群組為root：

```bash
chown root:root (稽核工具名稱)
```

### 記錄特權指令使用情形

* TWGCB-01-008-0158

* 開啟終端機，執行以下指令，在系統上所有磁區(partition)，逐一檢查磁區所掛載之檔案目錄(如根目錄)中的特權程式，並為每個特權程序的執行，建立稽核規則：

```bash
find (partition) -xdev \( -perm -4000 -o -perm -2000 \) -type f | awk '{print "-a always,exit -F path=" $1 " -F perm=x -F auid>=1000 -F auid!=4294967295 -k privileged" }'
```

* 在/etc/audit/rules.d/目錄，新增或編輯「privileged.rules」檔案，將根磁區(root partition)的特權程式稽核規則加入到該檔案，指令範例如下：

```bash
find / -xdev \( -perm -4000 -o -perm -2000 \) -type f | awk '{print "-a always,exit -F path=" $1 " -F perm=x -F auid>=1000 -F auid!=4294967295 -k privileged" }' >> /etc/audit/rules.d/privileged.rules
```

* 開啟終端機，執行以下指令，載入修改後之設定檔：

```bash
augenrules --load
```

* 開啟終端機，執行以下指令，若指令輸出enabled 2，表示稽核組態已鎖定，需重新開機才能載入修改後之設定檔：

```bash
auditctl -s | grep enabled
```

### 記錄系統管理者活動日誌變更

* TWGCB-01-008-0161

* 開啟終端機，執行指令如下，尋找sudo日誌檔案路徑

```bash
grep -r logfile /etc/sudoers* | sed -e 's/.*logfile=//;s/,? .*//'
```

* 執行vim指令，在/etc/audit/rules.d/目錄，新增或編輯「actions.rules」檔案，範例如下：

```bash
vim /etc/audit/rules.d/actions.rules
```

* 在檔案中加入以下內容：

```bash
-w (sudo日誌檔案路徑) -p wa -k actions
```

* 以系統預設路徑「/var/log/sudo.log」為例，在「actions.rules」檔案中加入以下內容：

```bash
-w /var/log/sudo.log -p wa -k actions
```

* 開啟終端機，執行以下指令，載入修改後之設定檔：

```bash
augenrules --load
```

* 開啟終端機，執行以下指令，若指令輸出enabled 2，表示稽核組態已鎖定，需重新開機才能載入修改後之設定檔：

```bash
auditctl -s | grep enabled
```

### auditd設定不變模式

* 在/etc/audit/rules.d/目錄，新增或編輯稽核規則中最後被執行的「.rules」檔案：
* 在該檔案的最後一行加入以下內容：

```bash
-e 2
--loginuid-immutable
```

* 執行以下終端機指令，若輸出結果出現「-e 2」與「--loginuid-immutable」，代表設定完成：

```bash
grep -h "^\s*[^#]" /etc/audit/rules.d/*.rules | egrep "(-e 2)|(--loginuid-immutable)"
```