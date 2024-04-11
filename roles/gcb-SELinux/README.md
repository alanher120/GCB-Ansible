# SELinux

* version: 1.0

## 手動處理項目

### TWGCB-01-003-0117: 標記為unlabeled_t的裝置檔案


1. 執行下列指令，尋找是否存在被標記為unlabeled_t的裝置檔案：

```bash
ls -Z | grep unlabeled_t
```

2. 若有，請執行下列指令針對單一檔案設定標籤：

```bash
chcon -t (標籤名稱) (檔案名稱)
```

3. 如欲針對整個檔案系統重新設定標籤，可執行下列指令：

```bash
touch /.autorelabel
reboot
```

### 開機載入程式啟用SELinux

* TWGCB-01-008-0186

* 編輯/etc/default/grub檔案，檢查所有CMDLINE_LINUX參數設定，並移除所有「selinux=0」與「enforcing=0」內容，範例如下：

```bash
GRUB_CMDLINE_LINUX_DEFAULT="quiet"
GRUB_CMDLINE_LINUX=""
```

* 開啟終端機，執行以下指令更新grub2設定檔：

```bash
grub2-mkconfig -o "$(dirname "$(find /boot -type f \( -name 'grubenv' -o -name 'grub.conf' -o -name 'grub.cfg' \) -exec grep -Pl '^\h*(kernelopts=|linux|kernel)' {} \;)")/grub.cfg"
```