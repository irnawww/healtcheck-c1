# Challenge 1: Bash Scripting - Server Health Check

## Deskripsi
Challenge ini bertujuan untuk membuat **bash script** sederhana yang dapat melakukan *server health check* secara otomatis.  
Script akan melakukan pengecekan konektivitas server, status layanan web, serta penggunaan disk, kemudian hasilnya akan dicatat ke dalam file log.

---

## Langkah Pengerjaan

### 1. Buat file script
```bash
nano health_check.sh
```
### 2. Isi script berikut
```bash
#!/bin/bash

LOGFILE="health_check.log"
TIMESTAMP=$(date "+%Y-%m-%d %H:%M:%S")

# --- Error handling untuk argumen ---
if [ -z "$1" ]; then
  echo "Usage: $0 <server_ip/hostname> [port]"
  exit 1
fi

SERVER=$1
PORT=${2:-80}  # default port 80

echo "===== Health Check pada $TIMESTAMP =====" >> "$LOGFILE"

# --- Ping Test ---
ping -c 1 -W 2 "$SERVER" > /dev/null 2>&1
if [ $? -ne 0 ]; then
  echo "Server unreachable." | tee -a "$LOGFILE"
  exit 2
else
  echo "Server is reachable." | tee -a "$LOGFILE"
fi

# --- HTTP/S Check ---
curl -s --connect-timeout 5 "http://$SERVER:$PORT" > /dev/null 2>&1
if [ $? -eq 0 ]; then
  echo "Web service on port $PORT is UP." | tee -a "$LOGFILE"
else
  echo "Web service on port $PORT is DOWN." | tee -a "$LOGFILE"
fi

# --- Disk Usage Check ---
DISK_USAGE=$(df -h / | awk 'NR==2 {print $5}')
echo "Disk usage on / is $DISK_USAGE." | tee -a "$LOGFILE"

echo "Results logged to $LOGFILE"

```
## Cara Menjalankan Script

### 1. File akan langsung disimpan dengan nama health_check.sh
### 2. Beri permission agar script bisa dieksekusi:
```bash
chmod +x health_check.sh
```
### 3. Script dijalankan dengan parameter hostname/IP dan port:
```bash
./health_check.sh localhost 80
```

## Output
### Output di terminal:
```
Server is reachable.
Web service on port 80 is UP.
Disk usage on / is 37%.
Results logged to health_coba.log
```

### Coba Cek isi file log
Dengan menjalankan command
```
cat health_check.log
```
### Dengan Hasil :

===== Health Check pada 2025-10-02 07:15:21 =====
Server is reachable.
Web service on port 80 is UP.
Disk usage on / is 37%.
Results logged to health_coba.log





