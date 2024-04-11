# 安裝作業系統時規化或是設定

## 安裝 OS 時設定

### 獨立磁區

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

## 手動設定項目

### 裝置檢查

```bash
#!/bin/bash
# TWGCB-01-003-0043
find PART -xdev -type d \( -perm -0002 -a ! -perm -1000 \) -print
# TWGCB-01-003-0044
find PART -xdev -type f -perm -0002 -print
# TWGCB-01-003-0045
find PART -xdev -type f -perm -2000 -print
# TWGCB-01-003-0046
find PART -xdev -type f -perm -4000 -print
# TWGCB-01-003-0047
find PART -xdev \( -nouser -o -nogroup \) -print
# TWGCB-01-003-0048
find PART -xdev \( -nouser -o -nogroup \) -print
# TWGCB-01-003-0049
find PART -xdev -type d -perm -0002 -uid +500 -print

grep -E "ttyS0|ttyS1" /etc/securetty
```


## 依需求設定

### TWGCB-01-008-0266

啟用限制存取SSH功能，有助於確保只有授權使用者才能透過SSH遠端存取系統

* `/etc/ssh/sshd_config`檔案可設定以下參數，以限制可透過SSH存取系統的使用者與群組：

1. AllowUsers：由空格分隔的使用者名稱組成，設定允許登入的使用者
2. AllowGroups：由空格分隔的群組名稱組成，設定允許登入的群組
3. DenyUsers：由空格分隔的使用者名稱組成，設定拒絕登入的使用者
4. DenyGroups：由空格分隔的群組名稱組成，設定拒絕登入的群組

設定完成後執行以下指令，重新啟動SSH服務使其生效

```bash
systemctl restart sshd
```

### TWGCB-01-008-0093: chrony校時設定

### TWGCB-01-008-0096: SNMP服務

```bash
dnf install net-snmp net-snmp-utils net-snmp-libs net-snmp-devel

systemctl stop snmpd

net-snmp-create-v3-user -ro -A snmp@PWD1 -a SHA -X snmp@PWD1 -x AES snmpAdmin
```

* /etc/snmp/snmpd.conf

將包含com2sec、group、view及access參數之行內容註解(新增#符號於行首)，以停用SNMPv1與SNMPv2，範例如下：

```conf
#com2sec notConfigUser  default       public
#group   notConfigGroup v1           notConfigUser
#group   notConfigGroup v2c          notConfigUser
#view    systemview    included   .1.3.6.1.2.1.1
#view    systemview    included   .1.3.6.1.2.1.25.1.1
#access  notConfigGroup  ""      any       noauth    exact  systemview none none
```

### TWGCB-01-008-0137,TWGCB-01-008-0138: 稽核日誌檔案所有權

1.  開啟終端機，執行以下指令，尋找稽核日誌檔案

```bash
grep -iw log_file /etc/audit/auditd.conf
```

2. 執行以下指令，設定稽核日誌檔案擁有者與群組, 設定稽核日誌檔案權限為600或更低權限

```bash
chown root:root (稽核日誌檔案名稱)
chmod 600 (稽核日誌檔案名稱)
```

### TWGCB-01-008-0205,TWGCB-01-008-0206: at.allow與cron.allow檔案

### 帳戶設定

* TWGCB-01-008-0225
* TWGCB-01-008-0226
* TWGCB-01-008-0227
* TWGCB-01-008-0228

### 安裝 GUI 設定

* TWGCB-01-012-0234
* TWGCB-01-008-0235
* TWGCB-01-008-0236
* TWGCB-01-008-0239

### 磁碟檢查項目

* TWGCB-01-008-0064
* TWGCB-01-008-0065

```bash
find (partition) -xdev -type f -perm -0002
find (partition) -xdev -nouser
find (partition) -xdev -nogroup
find (partition) -xdev -type d -perm -0002 -uid +999 -print
```



### 全系統GPG簽章驗證

依據下列規範檢查，如果大於零需要寫特殊原因

* TWGCB-01-003-0009
* TWGCB-01-003-0010

```bash
grep gpgcheck=0 /etc/yum.conf /etc/yum.repos.d/* | wc -l
```

### 使用RPM驗證套件完整性

依據下列規範檢查，如果有列出需要特別注意

* TWGCB-01-003-0012

```bash
rpm -qVa | awk '$2!="c" {print $0}'
```
