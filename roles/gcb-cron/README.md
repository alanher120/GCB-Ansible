# cron設定

## 手動處理項目

### cron日誌記錄功能

* 依下列 TWGCB-ID 要求設定
  * TWGCB-01-008-0207
  * TWGCB-01-012-0205

▪  開啟終端機，執行以下指令，檢查cron日誌記錄功能設定：
#grep cron /etc/rsyslog.conf /etc/rsyslog.d/*.conf
▪  若無發現cron日誌記錄功能設定，請編輯/etc/rsyslog.conf檔案或/etc/rsyslog.d/目錄下的檔案，新增或修改成以下內容，以記錄所有cron訊息：
cron.* /var/log/cron
▪  開啟終端機，執行以下指令重新啟動rsyslog服務：
#systemctl restart rsyslog.service