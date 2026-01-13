# 🚀 Nuclei Command Tips – Direct Target & Target List (Template Specific)

## ⚠️ Ethical Disclaimer / Peringatan Penting

Repositori ini dibuat **hanya untuk tujuan pembelajaran, dokumentasi, dan riset keamanan**.

Semua command dan teknik di dalam repositori ini **WAJIB digunakan hanya pada sistem
yang Anda miliki atau memiliki izin tertulis dari pemiliknya**.

Pemindaian tanpa otorisasi adalah **tindakan ilegal**.
Penulis tidak bertanggung jawab atas penyalahgunaan informasi ini.

---

## 📖 Daftar Isi

- Pengenalan
- Prerequisites
- Strategi Pemilihan Template Nuclei
- Nuclei Scan dari Target List
- Nuclei Scan dengan Template Lokal
- Nuclei Scan Single Target
- Pipeline Recon → Scan Otomatis
- Recon Only (Subfinder + Httpx)
- Catatan Penting
- Tips Akhir
- Legal Notice

---

## 🎯 Pengenalan

Dokumen ini berisi **tips command Nuclei** untuk:

- Scan vulnerability berbasis template
- Penggunaan target tunggal atau target list
- Integrasi recon → scan otomatis
- Kebutuhan reporting & automation

Dirancang agar **langsung siap pakai** dan mudah dimodifikasi sesuai kebutuhan.

---

## 🧰 Prerequisites

Pastikan tools berikut sudah terpasang dan ter-update:

- nuclei
- nuclei-templates
- subfinder
- httpx
- Go >= 1.20

---

## 🧠 Strategi Pemilihan Template Nuclei (Disarankan)

Disarankan **tidak langsung menjalankan template CVE**.

Pendekatan bertahap membantu:
- Mengurangi noise
- Menghindari false positive
- Menjaga stabilitas scan
- Lebih aman terhadap WAF / rate-limit

---

### 1️⃣ Tahap Awal – Exposure, Misconfiguration, Exposed Panels

Gunakan template ringan untuk memetakan attack surface awal.

```bash
nuclei -l/-u targets.txt \
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

Digunakan untuk menemukan:
- File sensitif terbuka
- Konfigurasi salah
- Admin panel / dashboard terekspos
- Endpoint menarik untuk scan lanjutan

---

### 2️⃣ Tahap Lanjutan – CVE Based Scanning

Setelah surface awal tervalidasi, lanjutkan ke template CVE.

```bash
nuclei -l/-u targets.txt \
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
- Validasi temuan exposure sebelumnya
- Discovery vulnerability berdampak tinggi

---

## 1️⃣ Scan dari List Target + Template Tertentu + Output JSON

```bash
nuclei -l targets.txt \
-t nuclei-templates \
-s critical,high,medium,low \
-es info \
-ps 30 \
-dr --no-mhe \
-json-export result.json
```

Digunakan untuk:
- Banyak target (list)
- Template nuclei tertentu
- Reporting berbasis JSON
- Automation / CI / arsip hasil scan

---

## 2️⃣ Scan dari List Target + Template Lokal

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

Digunakan untuk:
- Template custom
- Pengujian internal
- Kontrol penuh terhadap scope

---

## 3️⃣ Scan 1 Target Langsung

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

Digunakan untuk:
- Single target
- Validasi manual
- Proof-of-concept

---

## 4️⃣ Pipeline Recon → Scan Otomatis

```bash
subfinder -d target.com -silent -recursive -all -active -nW -t 11 \
| httpx -p 80,443,3000 -timeout 60 -silent \
| nuclei -t . \
-s critical,high,medium,low \
-es info \
-ps 30 \
-c 20 \
-rl 30 \
-timeout 15 \
-dr --no-mhe
```

Alur:
- Subdomain discovery
- Validasi host aktif
- Vulnerability scanning otomatis

---

## 🔍 Recon Only (Tanpa Scan Nuclei)

### Recon dari List Domain

```bash
subfinder -dL domains.txt -silent -recursive -all -active -nW -t 11
```

---

### Recon Single Domain → Simpan Host Aktif

```bash
subfinder -d target.com -silent -recursive -all -active -nW -t 11 \
| httpx -p 80,443,3000 -timeout 60 -silent \
-o alive.txt
```

Digunakan untuk:
- Recon awal
- Menyimpan host aktif
- Persiapan tahap scanning

---

## 📌 Catatan Penting

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
# Gunakan template exposure sebelum CVE
# Atur rate-limit dan concurrency
# Simpan output JSON untuk laporan
# Selalu pastikan scope legal & berizin
```

---

## 📜 Legal Notice

Tools do not make you a hacker.  
Authorization does.

Use responsibly.

