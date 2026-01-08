---
title: "Senior Itu Soal Tanggung Jawab, Bukan Umur atau Lama Kerja"
date: 2025-01-09T10:00:00+07:00
description: "Refleksi tentang apa yang sebenarnya membuat seorang engineer menjadi senior: tanggung jawab, bukan pengalaman bertahun-tahun."
tags: ["career", "engineering", "software-engineer", "leadership"]
categories: ["Engineering Journey"]
draft: false
---

## Masalah yang Sering Terjadi

"Berapa tahun pengalaman untuk jadi senior?"

Pertanyaan ini sering muncul di diskusi career engineering. Jawaban standarnya biasanya: 5 tahun, 7 tahun, atau 10 tahun.

Tapi di production, saya melihat engineer dengan 10 tahun pengalaman yang masih menulis kode seperti junior. Dan engineer dengan 3 tahun pengalaman yang sudah mengambil keputusan teknis yang kompleks dengan baik.

Masalahnya: **senior itu bukan soal umur atau lama kerja. Senior itu soal tanggung jawab**.

---

## Kenapa Ini Sering Terjadi

Ada beberapa alasan kenapa "senior" sering dikaitkan dengan pengalaman bertahun-tahun:

**1. Lebih mudah diukur**

Mengukur pengalaman dalam tahun itu objektif: 5 tahun lebih banyak dari 3 tahun. Mengukur tanggung jawab lebih subjektif dan butuh observasi yang lebih dalam.

**2. Asumsi yang salah**

Banyak yang mengira: semakin lama kerja, semakin banyak yang diketahui. Padahal pengalaman 1 tahun yang diulang 10 kali, tetap pengalaman 1 tahun.

**3. Struktur organisasi tradisional**

Di banyak perusahaan, promosi ke senior dikaitkan dengan lama kerja. Engineer yang sudah bekerja 5 tahun otomatis dianggap senior, meski tanggung jawabnya masih sama seperti junior.

---

## Contoh Kasus Nyata

Saya pernah bekerja dengan dua engineer yang kontras:

**Engineer A: 8 tahun pengalaman**

- Menulis kode yang berfungsi, tapi sulit di-maintain
- Tidak pernah memikirkan edge case sampai muncul di production
- Ketika ada bug, langsung fix tanpa memahami root cause
- Tidak pernah mempertimbangkan dampak perubahan pada sistem lain

**Engineer B: 3 tahun pengalaman**

- Menulis kode yang tidak hanya berfungsi, tapi juga mudah di-maintain
- Selalu memikirkan edge case dan failure mode sebelum menulis kode
- Ketika ada bug, investigasi sampai menemukan root cause, baru fix
- Selalu mempertimbangkan dampak perubahan pada sistem lain dan tim

Engineer B lebih "senior" meski pengalamannya lebih sedikit. Kenapa? Karena Engineer B **mengambil tanggung jawab yang lebih besar**.

---

## Dampak Teknis & Non-Teknis

Mengaitkan senior dengan umur atau lama kerja punya dampak yang jelas:

**Dampak teknis:**

- Engineer yang dianggap senior karena lama kerja, tapi tidak punya kemampuan senior, membuat keputusan teknis yang buruk
- Kode yang ditulis tidak mempertimbangkan maintainability, scalability, dan edge case
- Technical debt menumpuk karena tidak ada yang mengambil tanggung jawab untuk mencegahnya

**Dampak non-teknis:**

- Engineer yang sebenarnya sudah senior, tapi tidak diakui karena belum cukup lama kerja, jadi frustrasi
- Engineer yang dianggap senior karena lama kerja, tapi tidak punya kemampuan, jadi beban untuk tim
- Culture engineering jadi fokus pada "berapa lama kerja" bukan "berapa besar kontribusi"

---

## Pendekatan Praktis

Senior itu soal **tanggung jawab, bukan umur atau lama kerja**. Beberapa indikator yang lebih tepat:

**1. Tanggung jawab atas kualitas kode**

Engineer senior tidak hanya menulis kode yang berfungsi. Dia memastikan kode yang ditulis:
- Mudah di-maintain oleh engineer lain
- Mempertimbangkan edge case dan failure mode
- Memiliki dokumentasi yang cukup
- Mengikuti best practice yang relevan

**2. Tanggung jawab atas keputusan teknis**

Engineer senior tidak hanya mengimplementasikan requirement. Dia:
- Mempertanyakan requirement yang tidak jelas atau berpotensi bermasalah
- Mempertimbangkan trade-off dari berbagai solusi
- Membuat keputusan teknis yang mempertimbangkan jangka panjang
- Menerima tanggung jawab ketika keputusan yang diambil ternyata salah

**3. Tanggung jawab atas tim**

Engineer senior tidak hanya fokus pada tugas sendiri. Dia:
- Membantu engineer lain yang kesulitan
- Membagikan pengetahuan dan pengalaman
- Memberikan feedback yang konstruktif
- Membangun culture engineering yang baik

**4. Tanggung jawab atas sistem**

Engineer senior tidak hanya fokus pada fitur yang sedang dikerjakan. Dia:
- Memahami bagaimana sistem bekerja secara keseluruhan
- Mempertimbangkan dampak perubahan pada sistem lain
- Proaktif mengidentifikasi dan mencegah masalah
- Menerima tanggung jawab ketika sistem bermasalah

---

## Trade-off yang Harus Diterima

Mengaitkan senior dengan tanggung jawab punya trade-off:

**Kelemahan:**

- Lebih sulit diukur secara objektif
- Butuh observasi yang lebih dalam dari manager atau tim
- Bisa jadi subjektif, tergantung siapa yang menilai

**Keuntungan:**

- Engineer yang benar-benar senior diakui, meski belum lama kerja
- Engineer yang belum siap tidak dipaksa mengambil tanggung jawab yang terlalu besar
- Culture engineering jadi fokus pada kontribusi, bukan lama kerja

Trade-off ini sepadan. Lebih baik invest waktu untuk menilai tanggung jawab dengan benar, daripada mengandalkan ukuran yang mudah tapi tidak akurat.

---

## Contoh Praktis: Perbedaan Junior dan Senior

Berikut beberapa contoh perbedaan cara berpikir junior vs senior:

**Ketika ada bug di production:**

- **Junior:** Fix bug secepat mungkin, deploy, selesai
- **Senior:** Investigasi root cause, pahami kenapa bug ini terjadi, fix bug, pastikan tidak ada bug serupa di tempat lain, dokumentasikan lesson learned

**Ketika diminta menambah fitur:**

- **Junior:** Implementasikan sesuai requirement, selesai
- **Senior:** Pahami konteks bisnis, pertanyakan requirement yang tidak jelas, pertimbangkan edge case, pikirkan dampak pada sistem lain, baru implementasikan

**Ketika ada technical debt:**

- **Junior:** Abaikan, fokus pada fitur baru
- **Senior:** Identifikasi technical debt yang berbahaya, prioritaskan yang perlu diperbaiki, rencanakan refactoring yang tepat

**Ketika ada engineer lain yang kesulitan:**

- **Junior:** Fokus pada tugas sendiri
- **Senior:** Bantu engineer lain, bagikan pengetahuan, pastikan tim bisa bekerja dengan baik

Perbedaannya bukan pada skill teknis. Perbedaannya pada **tanggung jawab yang diambil**.

---

## Penutup

Senior itu bukan soal umur atau lama kerja. Senior itu soal **tanggung jawab**.

Engineer yang mengambil tanggung jawab atas kualitas kode, keputusan teknis, tim, dan sistem—itu yang sebenarnya senior. Meski pengalamannya baru 3 tahun.

Engineer yang sudah bekerja 10 tahun tapi tidak mengambil tanggung jawab yang lebih besar—itu masih junior. Meski gelarnya sudah senior.

Jadi, kalau kamu ingin jadi senior, jangan fokus pada "berapa lama saya harus kerja". Fokus pada **"tanggung jawab apa yang bisa saya ambil, dan bagaimana saya bisa mengambilnya dengan baik"**.

Tanggung jawab itu tidak datang dengan sendirinya. Kamu harus mengambilnya. Dan ketika kamu mengambil tanggung jawab yang lebih besar, kamu akan belajar lebih cepat, berkontribusi lebih banyak, dan benar-benar menjadi senior—tidak peduli berapa tahun pengalamanmu.
