---
title: "Culture Engineering Tidak Dibangun dari Rule, Tapi Contoh"
date: 2025-01-24T10:00:00+07:00
description: "Refleksi tentang bagaimana culture engineering yang baik dibangun dari contoh nyata, bukan dari aturan yang ditulis di dokumen."
tags: ["engineering", "culture", "leadership", "software-engineer"]
categories: ["Engineering Journey"]
draft: false
---

## Masalah yang Sering Terjadi

"Kita perlu culture engineering yang baik," kata manager.

Lalu dibuatlah dokumen yang panjang: coding standards, code review guidelines, testing requirements, documentation rules.

Dokumen itu dibagikan ke semua engineer. Diadakan meeting untuk menjelaskannya. Dipasang di wiki dan di-referensi setiap kali ada yang melanggar.

Tapi setelah beberapa bulan, culture engineering tidak berubah. Kode masih ditulis dengan standar yang berbeda-beda. Code review masih sekadar "LGTM". Testing masih diabaikan.

Masalahnya: **culture engineering tidak dibangun dari rule. Culture engineering dibangun dari contoh**.

---

## Kenapa Ini Sering Terjadi

Ada beberapa alasan kenapa banyak yang mengira culture bisa dibangun dari rule:

**1. Lebih mudah dibuat**

Menulis dokumen itu mudah. Membuat contoh nyata yang konsisten itu sulit. Manager yang sibuk cenderung pilih yang mudah.

**2. Ilusi kontrol**

Dokumen yang jelas memberi ilusi bahwa culture bisa dikontrol. Padahal culture itu hidup, dinamis, dan dibentuk oleh perilaku sehari-hari—bukan oleh dokumen.

**3. Asumsi yang salah**

Banyak yang mengira: kalau rule sudah ditulis, engineer akan mengikutinya. Padahal engineer lebih cenderung mengikuti apa yang mereka lihat, bukan apa yang mereka baca.

---

## Contoh Kasus Nyata

Saya pernah bekerja di tim yang punya coding standards yang sangat detail. Dokumennya 20 halaman. Setiap engineer wajib membacanya sebelum menulis kode.

Tapi di production, kode yang ditulis tidak mengikuti standar itu. Kenapa?

Karena engineer yang lebih berpengalaman yang menulis kode pertama tidak mengikuti standar itu. Dan ketika engineer lain melihat kode tersebut, mereka mengikuti contoh yang mereka lihat—bukan standar yang mereka baca.

Baru saat itu saya sadar: **engineer belajar dari contoh, bukan dari dokumen**.

Contoh lain: Tim punya rule "setiap PR harus di-review minimal 2 orang". Tapi di praktiknya, PR yang dibuat oleh engineer tertentu langsung di-approve tanpa review yang proper.

Apa yang terjadi? Engineer lain melihat ini, dan mereka mulai melakukan hal yang sama. Rule ada, tapi tidak diikuti—karena contoh yang mereka lihat mengatakan sebaliknya.

---

## Dampak Teknis & Non-Teknis

Membangun culture dari rule punya dampak yang jelas:

**Dampak teknis:**

- Kode tidak konsisten karena engineer mengikuti contoh yang berbeda-beda
- Code review tidak efektif karena tidak ada contoh review yang baik
- Technical debt menumpuk karena tidak ada contoh bagaimana mencegahnya

**Dampak non-teknis:**

- Engineer frustrasi karena rule tidak diikuti oleh yang membuat rule
- Trust menurun karena ada gap antara yang dikatakan dan yang dilakukan
- Culture engineering jadi sekadar dokumen, bukan praktik nyata

---

## Pendekatan Praktis

Culture engineering yang baik dibangun dari **contoh, bukan rule**. Beberapa pendekatan yang bisa dipakai:

**1. Lead by example**

Engineer yang lebih berpengalaman harus menjadi contoh. Kalau mereka menulis kode yang baik, melakukan code review yang proper, dan mengikuti best practice—engineer lain akan mengikuti.

Contoh: Kalau kamu ingin culture code review yang baik, jangan hanya menulis rule. Lakukan code review yang baik untuk setiap PR. Engineer lain akan melihat dan belajar.

**2. Konsistensi lebih penting dari kesempurnaan**

Lebih baik konsisten melakukan hal yang baik, daripada sesekali melakukan hal yang sempurna. Culture dibangun dari kebiasaan, bukan dari insiden.

Contoh: Lebih baik setiap PR di-review dengan standar yang konsisten (meski tidak sempurna), daripada sesekali ada review yang sangat detail tapi kebanyakan PR di-approve tanpa review.

**3. Perbaiki contoh yang buruk**

Ketika ada contoh yang buruk (misalnya kode yang tidak mengikuti standar), perbaiki—jangan hanya mengingatkan. Perbaikan yang nyata lebih efektif daripada reminder.

Contoh: Ketika kamu melihat kode yang tidak mengikuti standar, jangan hanya comment "ini tidak sesuai standar". Refactor kode itu, atau buat PR untuk memperbaikinya. Ini menunjukkan bahwa standar itu penting, bukan sekadar dokumen.

**4. Rayakan contoh yang baik**

Ketika ada engineer yang melakukan hal yang baik (misalnya code review yang sangat detail, atau refactoring yang memperbaiki technical debt), rayakan—jangan hanya dianggap biasa. Ini menunjukkan bahwa hal-hal ini dihargai.

Contoh: Di standup atau retrospektif, sebutkan engineer yang melakukan code review yang baik atau refactoring yang membantu. Ini memberi contoh yang jelas tentang apa yang dihargai.

**5. Rule hanya untuk dokumentasi**

Rule itu penting untuk dokumentasi dan onboarding. Tapi rule tidak membangun culture. Yang membangun culture adalah contoh yang konsisten.

Jadi, tulis rule yang jelas. Tapi ingat: rule itu hanya efektif kalau diikuti oleh yang membuat rule. Kalau tidak, rule itu hanya dokumen yang tidak berarti.

---

## Trade-off yang Harus Diterima

Membangun culture dari contoh punya trade-off:

**Kelemahan:**

- Butuh waktu lebih lama karena harus konsisten dalam jangka panjang
- Butuh komitmen dari engineer yang lebih berpengalaman
- Tidak bisa "dipaksakan" dengan cepat seperti rule

**Keuntungan:**

- Culture yang dibangun lebih kuat dan bertahan lama
- Engineer benar-benar mengikuti praktik yang baik, bukan sekadar mengikuti rule
- Trust dan respect lebih tinggi karena tidak ada gap antara yang dikatakan dan yang dilakukan

Trade-off ini sepadan. Lebih baik invest waktu untuk membangun culture yang kuat, daripada membuat rule yang tidak diikuti.

---

## Contoh Praktis: Membangun Culture Code Review

Berikut contoh bagaimana membangun culture code review dari contoh, bukan rule:

**Yang tidak efektif (dari rule):**

- Tulis dokumen "Code Review Guidelines" yang detail
- Wajibkan setiap PR di-review minimal 2 orang
- Pasang reminder di Slack setiap kali ada PR yang belum di-review

Hasil: PR tetap di-review dengan cepat dan tidak detail, karena tidak ada contoh review yang baik.

**Yang efektif (dari contoh):**

- Engineer yang lebih berpengalaman melakukan code review yang detail untuk setiap PR, termasuk PR dari engineer lain
- Di code review, mereka tidak hanya cek syntax, tapi juga:
  - Apakah kode mudah di-maintain?
  - Apakah ada edge case yang terlewat?
  - Apakah ada cara yang lebih baik?
  - Apakah ada dampak pada sistem lain?
- Mereka memberikan feedback yang konstruktif, bukan hanya "LGTM" atau "fix this"
- Ketika ada PR yang perlu perbaikan, mereka menjelaskan kenapa dan memberikan saran yang jelas

Hasil: Engineer lain melihat contoh review yang baik, dan mereka mulai melakukan hal yang sama. Culture code review yang baik terbentuk secara alami.

---

## Penutup

Culture engineering tidak dibangun dari rule yang ditulis di dokumen. Culture engineering dibangun dari **contoh yang konsisten dari engineer yang lebih berpengalaman**.

Engineer belajar dari apa yang mereka lihat, bukan dari apa yang mereka baca. Kalau mereka melihat kode yang baik, code review yang proper, dan praktik yang benar—mereka akan mengikuti.

Jadi, kalau kamu ingin membangun culture engineering yang baik, jangan mulai dari menulis rule. Mulai dari **menjadi contoh yang baik**. Tulis kode yang baik. Lakukan code review yang proper. Ikuti best practice yang kamu inginkan.

Rule itu penting untuk dokumentasi. Tapi yang membangun culture adalah contoh—bukan rule.

Ingat: culture itu hidup. Culture itu dibentuk oleh perilaku sehari-hari. Dan perilaku itu menular. Jadi, pastikan perilaku yang kamu tunjukkan adalah perilaku yang ingin kamu lihat di tim.
