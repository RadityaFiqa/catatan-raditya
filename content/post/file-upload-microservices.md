---
title: "Case File Upload di Microservices: Dari Chaos ke Controlled Storage"
date: 2025-05-09T10:00:00+07:00
description: "Refleksi file upload, distributed state, dan lifecycle resource di sistem microservices."
tags: ["backend", "microservices", "architecture", "file-upload", "engineering-story"]
categories: ["Engineering Journey"]
draft: false
---

## Prolog

File upload hampir selalu datang sebagai permintaan kecil.  
“Cuma upload dokumen,” katanya.

Di sistem yang masih sederhana, asumsi itu jarang bermasalah.  
File tersimpan, response 200, fitur dianggap selesai.

Masalahnya baru terasa ketika sistem membesar — ketika service mulai terpisah, proses bisnis tidak lagi linear, dan kegagalan tidak bisa diasumsikan sebagai kejadian langka.  
Di titik itu, file upload berhenti menjadi detail teknis. Ia berubah menjadi **state terdistribusi** yang diam-diam ikut menentukan stabilitas sistem.

---

## Masalah Nyata di Lapangan

Di production, tidak ada satu insiden besar yang langsung menjelaskan semuanya.  
Yang muncul justru pola kegagalan kecil tapi berulang:

- File berhasil di-upload, tapi proses bisnis berikutnya gagal  
- Retry terjadi, namun file lama tetap tersimpan  
- Ada file yang secara teknis valid, tetapi secara bisnis tidak pernah benar-benar digunakan  

Awalnya ini terlihat seperti masalah housekeeping.  
“Tambahkan cleanup job, selesai.”

Pendekatan itu terasa masuk akal — sampai disadari bahwa file-file tersebut tidak bisa dihapus begitu saja.  
Karena tidak ada kejelasan: **siapa yang sebenarnya memiliki file tersebut, dan sampai kapan ia dianggap valid**.

---

## Analisis Teknis

Dalam arsitektur microservices, file bukan sekadar payload.  
Begitu ia disimpan, ia menjadi **distributed state**.

Masalah muncul dari beberapa asumsi implisit:

- Storage service diperlakukan sebagai utilitas pasif  
- Ownership file diasosiasikan dengan request, bukan dengan proses bisnis  
- Lifecycle file diasumsikan selesai ketika request selesai  

Kenyataannya berbeda:

- Storage service tidak tahu apakah file akan pernah dipakai  
- Business service tidak punya kontrol penuh atas penyimpanan  
- Request bisa gagal di tengah jalan, sementara file sudah menjadi state permanen  

Kondisi ini menciptakan **failure mode yang tidak simetris**:  
proses bisnis gagal, tetapi storage sukses.

Dan state yang “setengah hidup” inilah yang paling sulit ditangani.

---

## Pendekatan Solusi

Solusinya bukan sekadar membersihkan file yang tertinggal.

Perubahan paling penting justru ada pada cara memandang file itu sendiri:  
**file adalah resource dengan lifecycle yang harus didefinisikan secara eksplisit**.

Beberapa prinsip yang kemudian dipakai:

- **Ownership harus jelas**  
  File selalu memiliki konteks bisnis yang eksplisit, bukan sekadar hasil upload.

- **Resource bersifat sementara secara default**  
  File tidak langsung dianggap permanen sampai proses bisnis menyatakan ia valid.

- **Expiration sebagai bagian desain**  
  Setiap file memiliki batas waktu hidup.  
  Tidak semua data memang perlu disimpan lama.

- **Eventual consistency diterima, tapi dikontrol**  
  Sinkronisasi sempurna tidak dikejar.  
  Yang dijaga adalah idempotency, kompensasi, dan batas dampak kegagalan.

Pendekatan ini tidak menghilangkan kegagalan.  
Ia hanya memastikan kegagalan tidak meninggalkan state yang sulit dijelaskan.

---

## Insight

Pelajaran terpenting dari kasus ini bukan tentang file upload.  
Melainkan tentang **cara memandang resource di sistem terdistribusi**.

Banyak masalah muncul bukan karena implementasi yang salah,  
tetapi karena lifecycle resource tidak pernah benar-benar dirancang.

Beberapa trade-off tetap harus diterima:

- Kompleksitas sistem bertambah  
- Ada latency dan inkonsistensi sementara  
- Tidak semua edge case bisa ditangani secara otomatis  

Namun trade-off ini lebih sehat dibanding sistem yang perlahan menumpuk state tanpa pemilik.

---

## Penutup

Seiring sistem tumbuh, hal-hal kecil yang dulu terasa sepele  
mulai menuntut keputusan desain yang lebih sadar.

File upload hanyalah salah satu contohnya —  
bukan karena ia rumit, tetapi karena ia memaksa kita untuk serius memikirkan **ownership, failure, dan lifecycle** sejak awal.
