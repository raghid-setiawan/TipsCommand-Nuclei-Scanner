
# 🚀 Nuclei Command Tips – Direct Target & Target List (Template Specific)

## ⚠️ Ethical Disclaimer / Peringatan Penting

Repositori ini dibuat **hanya untuk tujuan pembelajaran, dokumentasi, dan riset keamanan**.

Semua command dan teknik di dalam repositori ini **WAJIB digunakan hanya pada sistem
yang Anda miliki atau memiliki izin tertulis dari pemiliknya**.

Pemindaian tanpa otorisasi merupakan **tindakan ilegal**.  
Penulis tidak bertanggung jawab atas penyalahgunaan informasi dalam repositori ini.

---

## 📖 Daftar Isi

- Pengenalan
- Prerequisites
- Filosofi & Strategi Scanning
- Tahap Awal – Exposure, Misconfiguration, Exposed Panels
- Tahap Lanjutan – CVE Based Scanning
- Scan dari Target List + Output JSON
- Scan dengan Template Lokal
- Scan Single Target
- Pipeline Recon → Scan Otomatis
- Recon Only (Tanpa Nuclei)
- Catatan Penting Parameter
- Tips Akhir
- Legal Notice

---

## 🎯 Pengenalan

Dokumen ini berisi **tips dan best practice penggunaan Nuclei** untuk:

- Vulnerability scanning berbasis template
- Scan target tunggal (`-u`) maupun banyak target (`-l`)
- Integrasi recon → scan otomatis
- Automation, reporting, dan CI/CD

Dirancang agar:
- Langsung siap pakai
- Mudah dimodifikasi
- Stabil terhadap resource & WAF
- Layak untuk bug bounty dan internal pentest

---

## 🧰 Prerequisites

Pastikan tools berikut sudah terpasang dan versi terbaru:

- nuclei
- nuclei-templates
- subfinder
- httpx
- Go >= 1.20

---

## 🧠 Filosofi & Strategi Scanning

❌ **Jangan langsung menjalankan template CVE**

Pendekatan bertahap membantu:
- Mengurangi noise & false positive
- Menjaga stabilitas scan
- Menghindari WAF / rate-limit
- Fokus pada attack surface nyata

**Prinsip utama:**

Recon → Exposure → Validasi → CVE

---

## 1️⃣ Tahap Awal – Exposure, Misconfiguration, Exposed Panels

Digunakan untuk **memetakan attack surface awal**.

### 🔹 A. Single Target (`-u`)

```bash
nuclei -u https://target.com \
-t http/exposures,http/misconfiguration,http/exposed-panels \
-s critical,high,medium,low \
-es info \
-ps 30 \
-c 20 \
-rl 30 \
-timeout 15 \
-dr --no-mhe \
-retries 1 \
-silent
```

Digunakan untuk:
- Validasi manual
- Scope kecil
- Proof-of-concept

---

### 🔹 B. Banyak Target dari File (`-l`)

```bash
nuclei -l targets.txt \
-t http/exposures,http/misconfiguration,http/exposed-panels \
-s critical,high,medium,low \
-es info \
-ps 30 \
-c 20 \
-rl 30 \
-timeout 15 \
-dr --no-mhe \
-retries 1 \
-silent
```

Digunakan untuk:
- Asset besar
- Automation
- Recon hasil massal

---

## 2️⃣ Tahap Lanjutan – CVE Based Scanning

⚠️ **Dijalankan SETELAH exposure tervalidasi**

### 🔹 A. Single Target (`-u`)

```bash
nuclei -u https://target.com \
-t http/cves \
-s critical,high,medium,low \
-es info \
-ps 30 \
-c 20 \
-rl 30 \
-timeout 15 \
-dr --no-mhe \
-retries 1 \
-silent
```

---

### 🔹 B. Banyak Target (`-l`)

```bash
nuclei -l targets.txt \
-t http/cves \
-s critical,high,medium,low \
-es info \
-ps 30 \
-c 20 \
-rl 30 \
-timeout 15 \
-dr --no-mhe \
-retries 1 \
-silent
```

Digunakan untuk:
- Identifikasi CVE spesifik versi
- Validasi temuan exposure
- Vulnerability berdampak tinggi

---

## 3️⃣ Scan dari Target List + Output JSON

```bash
nuclei -l targets.txt \
-t nuclei-templates \
-s critical,high,medium,low \
-es info \
-ps 30 \
-dr --no-mhe \
-json-export result.json
```

---

## 4️⃣ Scan dari Target List + Template Lokal

```bash
nuclei -l targets.txt \
-t . \
-s critical,high,medium,low \
-es info \
-ps 30 \
-c 20 \
-timeout 15 \
-dr --no-mhe
```

---

## 5️⃣ Scan Single Target

```bash
nuclei -u https://target.com \
-t nuclei-templates \
-s critical,high,medium,low \
-es info \
-ps 30 \
-c 20 \
-timeout 15 \
-dr --no-mhe
```

---

## 6️⃣ Pipeline Recon → Scan Otomatis (Direkomendasikan)

### 🔹 Step 1: Recon + Alive Host

```bash
subfinder -dL list.txt -silent -recursive -all -active -nW -t 11 \
| httpx -p 80,443,3000,8080,8443 \
-fc 403,404 \
-timeout 60 \
-retries 2 \
-threads 50 \
-H "User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)" \
-silent \
-o targets.txt
```

---

### 🔹 Step 2: Exposure Scan

```bash
nuclei -l targets.txt \
-t http/exposures,http/misconfiguration,http/exposed-panels \
-s critical,high,medium,low \
-es info \
-ps 30 \
-c 20 \
-rl 30 \
-timeout 15 \
-dr --no-mhe \
-silent
```

---

### 🔹 Step 3: CVE Scan

```bash
nuclei -l targets.txt \
-t http/cves \
-s critical,high,medium \
-es info \
-ps 30 \
-c 20 \
-rl 30 \
-timeout 15 \
-dr --no-mhe \
-silent
```

---

## 🔍 Recon Only (Tanpa Nuclei)

```bash
subfinder -dL domains.txt -silent -recursive -all -active -nW -t 11
```

```bash
subfinder -d target.com -silent -recursive -all -active -nW -t 11 \
| httpx -p 80,443,3000,8080,8443 -silent \
-o alive.txt
```

---

## 📌 Catatan Penting Parameter

- `-u` → Single target
- `-l` → Target list
- `-t` → Template nuclei
- `-json-export` → Reporting & automation
- `-dr` → Drop response body
- `--no-mhe` → Cegah memory heap exhaustion
- `-rl` → Rate limit
- `-retries` → Kontrol retry request

---

## ✨ Tips Akhir

```bash
# Lakukan recon sebelum scanning
# Jalankan exposure sebelum CVE
# Atur rate-limit dan concurrency
# Simpan output JSON untuk laporan
# Selalu pastikan scope legal dan berizin
```

---

## 📜 Legal Notice

Tools do not make you a hacker.  
Authorization does.

Use responsibly.


