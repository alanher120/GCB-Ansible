# GCB-Ansible

GCB-Ansible-2.0是依據「政府組態基準(GCB) - 國家資通安全研究院」 作業系統說明文件： TWGCB-01-008_Red Hat Enterprise Linux 8政府組態基準說明文件v1.0_1100924.pdf 撰寫 。

## 執行環境

* OS: Debian11
* ansible version: ansible 2.10.8

## 部屬環境

* OS: Oracle-Linux-9, CentOS-7

## 部屬前設定

### 目標主機設定

### RedHat6

1. 掛載光碟
2. 安裝必要套件

```bash
yum install -y libselinux-python
```

### 開發環境設定

```bash
tee /etc/sudoers.d/pollo-develop<<EOF
ansible ALL=(ALL)   NOPASSWD: ALL
EOF
```

### (Optional)建立帳號:ansible

```bash
sudo groupadd ansible
sudo useradd -g ansible -d /home/ansible -s /bin/bash ansible
sudo usermod -aG wheel ansible
sudo passwd ansible
```

### ssh-keygen

1. 新增存放目的資料夾

```bash
mkdir -p /home/jameschang/Documents/gitContent/jameschang/GCB-Ansible/.ssh
```

2. 產生ssh key，並放置在專案目錄中

```bash
ssh-keygen # /home/jameschang/Documents/gitContent/jameschang/GCB-Ansible/.ssh/id_rsa
```

3. 將公鑰設定放到伺服器上

```bash
ssh-copy-id -i .ssh/id_rsa ansible@10.192.3.100
```

4. 連接測試

```bash
ssh -i .ssh/id_rsa ansible@10.192.3.100
```

### log 設定

```bash
mkdir -p logs
touch logs/ansible.log
```

## tools

### 檢查部屬OS版本資訊

```bash
ansible-playbook check-os-version.yml -i inventorys/develop
```

## 注意事項

* 設定完 playbook-base.yml 需要重新開機

## 測試

### 已完成測試的Linux發行板

* RedHat 6.10

### 測試項目

1. reboot