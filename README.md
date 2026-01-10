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
- Nuclei Scan dari Target List
- Nuclei Scan dengan Template Lokal
- Nuclei Scan Single Target
- Pipeline Recon → Scan Otomatis
- Recon Only (Subfinder + Httpx)
- Catatan Penting
- Tips Akhir

---

## 🎯 Pengenalan

Dokumen ini berisi **tips command Nuclei** untuk:

- Scan vulnerability berbasis template
- Penggunaan target tunggal atau target list
- Integrasi recon → scan otomatis
- Kebutuhan reporting & automation

Dirancang agar **langsung siap pakai** dan mudah dimodifikasi sesuai kebutuhan.

---

## 1️⃣ Scan dari List Target + Template Tertentu + Output JSON

```bash
nuclei -l 2.txt \
-t nuclei-templates-main \
-s critical,high,medium,low \
-es info \
-ps 30 \
-dr --no-mhe \
-json-export 2.json
```

➜ Digunakan untuk:
- Banyak target (list)
- Template nuclei tertentu
- Kebutuhan reporting (JSON)
- Automation / CI / arsip hasil scan

---

## 2️⃣ Scan dari List Target + Template Lokal

```bash
nuclei -l 1.txt \
-t . \
-s critical,high,medium,low \
-es info \
-ps 30 \
-c 20 \
-timeout 15 \
-dr --no-mhe
```

➜ Digunakan untuk:
- Target list spesifik
- Template custom / hasil modifikasi sendiri
- Scan cepat & terkontrol

---

## 3️⃣ Scan 1 Target Langsung

```bash
nuclei -u https://target.com \
-t nuclei-templates-main \
-s critical,high,medium,low \
-es info \
-ps 30 \
-c 20 \
-timeout 15 \
-dr --no-mhe
```

➜ Digunakan untuk:
- Single target
- Fokus discovery vulnerability
- Validasi manual hasil recon

---

## 4️⃣ Pipeline Recon → Scan Otomatis

```bash
subfinder -d target.com -silent -recursive -all -active -nW -t 11 \
| httpx -p 80,443,3000 -timeout 60 -H "User-Agent: USER_AGENT" -silent \
| nuclei -t . \
-s critical,high,medium,low \
-es info \
-ps 30 \
-c 20 \
-rl 30 \
-timeout 15 \
-dr --no-mhe
```

➜ Alur kerja:
- Subdomain discovery
- Validasi host aktif
- Vulnerability scanning otomatis

---

## 🔍 Recon Only (Tanpa Scan Nuclei)

### 1️⃣ Recon dari List Domain (Windows Example)

```bash
C:\Users\EDWIN\Downloads>subfinder -dL terkenal.txt -silent -recursive -all -active -nW -t 11
```

---

### 2️⃣ Recon Single Domain → Simpan Host Aktif

```bash
subfinder -d target.com -silent -recursive -all -active -nW -t 11 \
| httpx -p 80,443,3000 -timeout 60 \
-H "User-Agent: USER_AGENT" -silent \
-o alive.txt
```

➜ Digunakan untuk:
- Recon saja (tanpa vulnerability scan)
- Menyimpan daftar host aktif
- Tahap awal sebelum scanning lanjutan

---

## 📌 Catatan Penting

- `-u`  → Scan 1 target langsung
- `-l`  → Scan banyak target (list)
- `-t`  → Template nuclei (folder / file)
- `-json-export` → Cocok untuk reporting & automation
- `-dr` → Drop response body (lebih ringan)
- `--no-mhe` → Hindari memory heap exhaustion

---

## ✨ Tips Akhir

```bash
# Gunakan recon terlebih dahulu sebelum scan
# Mulai dari severity rendah, naikkan bertahap
# Gunakan template custom untuk environment spesifik
# Simpan output JSON untuk analisa & laporan
# Selalu pastikan pengujian bersifat legal & berizin
```

---

Happy Scanning (Authorized Only)
