Challenge 1: Bash Scripting - Server Health Check

Challenge ini bertujuan untuk membuat **bash script** sederhana yang dapat melakukan *server health check* secara otomatis.  
Script akan melakukan pengecekan konektivitas server, status layanan web, serta penggunaan disk, kemudian hasilnya akan dicatat ke dalam file log.

---

## Langkah Pengerjaan

### 1. Buat file script
```bash
nano health_check.sh

### 2. Isi script berikut
#!/bin/bash

LOGFILE="health_coba.log"
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

3. Beri permission agar bisa dieksekusi
chmod +x health_check.sh

4. Jalankan script dengan parameter hostname/IP dan port
./health_check.sh localhost 80

Contoh Output

Ketika script dijalankan, output di terminal:

Server is reachable.
Web service on port 80 is UP.
Disk usage on / is 37%.
Results logged to health_coba.log

Cek Log File

Untuk melihat isi log:

cat health_coba.log


Contoh isi log:

===== Health Check pada 2025-10-02 07:15:21 =====
Server is reachable.
Web service on port 80 is UP.
Disk usage on / is 37%.
Results logged to health_coba.log
