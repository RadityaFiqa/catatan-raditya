---
title: "Case File Upload di Microservices: Dari Chaos ke Controlled Storage"
date: 2025-12-27T17:00:00+07:00
description: "Refleksi desain file upload di microservices dan dampaknya ketika failure dan lifecycle tidak diperhitungkan."
tags: ["microservices", "backend", "architecture", "file-upload", "storage"]
categories: ["Engineering Journey"]
draft: false
---

> File upload jarang meledak di awal, tapi hampir selalu menghantui di belakang.

## Pendahuluan

File upload sering diposisikan sebagai detail implementasi.  
Selama request berhasil dan file tersimpan, fitur dianggap selesai.

Masalahnya, dalam arsitektur **microservices**, file upload bukan sekadar fitur — ia adalah **state**.  
Dan state yang tidak dikontrol akan selalu bocor.

Di sistem yang saya tangani, tidak ada error besar.  
Yang ada hanyalah akumulasi:
- File berhasil di-upload, tetapi proses bisnis gagal
- File tidak pernah digunakan, namun tidak pernah dihapus
- Storage tumbuh tanpa owner dan tujuan yang jelas

---

## Di Mana Desain Mulai Salah

Pola yang digunakan sebenarnya umum:
1. Request masuk
2. File disimpan
3. Proses bisnis dijalankan

Masalahnya bukan di alurnya, tetapi di **asumsi desain**.

File dianggap sekadar efek samping request.  
Kegagalan dianggap kejadian langka.  
Storage diasumsikan bisa “ikut rollback”.

Padahal:
- File storage bersifat **stateful**
- Tidak ada transaksi lintas sistem
- Failure adalah jalur eksekusi normal

Setiap request yang gagal setelah upload meninggalkan **artefak permanen**.

---

## Prinsip Teknis yang Seharusnya Diterapkan

Solusi bukan sekadar menambah tooling, melainkan **mendisiplinkan file sebagai resource**.

Beberapa prinsip teknis yang wajib ada:

- **Explicit file identity**  
  Setiap file yang masuk ke storage harus memiliki identitas yang dapat ditelusuri, bukan sekadar nama file acak.

- **Ownership dan domain context**  
  File harus selalu tahu *siapa* yang membuatnya dan *untuk konteks apa*.  
  File tanpa konteks adalah file yang siap menjadi orphan.

- **Lifecycle-aware metadata**  
  Setiap file harus ditandai apakah ia bersifat sementara atau permanen.

- **Expired-by-default mindset**  
  File **tidak boleh diasumsikan hidup selamanya**.  
  `expired_at` harus menjadi atribut wajib, bukan opsional.

Tidak semua file layak disimpan lama.  
Sebagian besar file hanya valid selama proses bisnis berlangsung.

---

## Desain yang Lebih Dewasa

Pendekatan yang lebih matang adalah memisahkan **lifecycle file** dari **lifecycle request**.

Artinya:
- Upload tidak berarti commit
- Request gagal tidak meninggalkan state permanen
- Cleanup berjalan deterministik, bukan manual

File yang tidak pernah mencapai kondisi akhir dapat dihapus otomatis berdasarkan waktu hidupnya.

Dengan pendekatan ini, storage berhenti menjadi tempat sampah yang diam-diam tumbuh.

---

## Kesimpulan

Masalah file upload di microservices hampir tidak pernah soal teknologi.  
Ia adalah **utang desain** akibat kegagalan mendefinisikan lifecycle sejak awal.

Ketika setiap file memiliki identitas, konteks, dan batas waktu hidup, sistem:
- Lebih mudah diaudit
- Lebih aman
- Lebih murah untuk dioperasikan

Desain yang matang bukan tentang memastikan request selalu berhasil,  
tetapi tentang memastikan **kegagalan tidak meninggalkan jejak permanen**.
