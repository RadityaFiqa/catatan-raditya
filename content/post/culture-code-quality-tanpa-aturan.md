---
title: "Membangun Culture Code Quality Tanpa Banyak Aturan"
date: 2025-07-23T10:00:00+07:00
description: "Refleksi tentang bagaimana membangun culture code quality yang kuat tanpa banyak aturan dan enforcement yang ketat."
tags: ["engineering", "culture", "code-quality", "leadership", "software-engineer"]
categories: ["Engineering Journey"]
draft: false
---

## Masalah yang Sering Terjadi

"Kita perlu code quality yang lebih baik," kata banyak tim.

Lalu dibuatlah aturan: coding standards, code review guidelines, testing requirements, documentation rules. Semua aturan ini di-enforce dengan ketat. PR tidak di-approve kalau tidak sesuai aturan. Engineer yang melanggar diingatkan.

Tapi setelah beberapa bulan, code quality tidak berubah. Atau bahkan memburuk karena engineer fokus pada "memenuhi aturan" bukan "menulis kode yang baik".

Masalahnya: **code quality tidak bisa dibangun dengan aturan. Code quality dibangun dengan culture—dan culture dibangun dengan contoh, bukan aturan**.

Saya pernah melihat tim yang punya aturan coding standards yang sangat detail. Tapi kode yang ditulis tetap tidak konsisten. Kenapa? Karena engineer senior yang menulis kode pertama tidak mengikuti aturan itu. Dan ketika junior engineer melihat kode senior, mereka mengikuti contoh yang mereka lihat—bukan aturan yang mereka baca.

---

## Kenapa Ini Sering Terjadi

Ada beberapa alasan kenapa code quality sering dianggap bisa dibangun dengan aturan:

**1. Lebih mudah dibuat**

Menulis aturan itu mudah. Membangun culture yang kuat itu sulit. Banyak tim cenderung pilih yang mudah.

**2. Ilusi kontrol**

Aturan yang jelas memberi ilusi bahwa code quality bisa dikontrol. Padahal code quality itu hidup, dinamis, dan dibentuk oleh perilaku sehari-hari—bukan oleh aturan.

**3. Asumsi yang salah**

Banyak yang mengira: kalau aturan sudah ditulis, engineer akan mengikutinya. Padahal engineer lebih cenderung mengikuti apa yang mereka lihat, bukan apa yang mereka baca.

**4. Kurangnya contoh yang baik**

Banyak engineer tidak pernah melihat kode yang benar-benar baik. Mereka hanya melihat kode yang "memenuhi aturan" tapi tidak benar-benar baik. Tanpa contoh yang baik, sulit membangun culture code quality.

---

## Contoh Kasus Nyata

Bayangkan skenario seperti ini: Tim punya aturan "setiap function harus punya test". Tapi di praktiknya, test yang ditulis hanya test happy path. Test tidak menangkap edge case atau failure scenario.

Kenapa? Karena engineer senior yang menulis test pertama hanya test happy path. Dan ketika junior engineer melihat test senior, mereka mengikuti contoh yang mereka lihat—bukan aturan yang mereka baca.

Aturan ada, tapi culture tidak terbentuk. Karena culture dibentuk oleh contoh, bukan aturan.

Contoh lain: Tim punya aturan "code review harus detail". Tapi di praktiknya, code review hanya "LGTM" atau "fix typo". Kenapa? Karena engineer yang lebih berpengalaman yang melakukan code review pertama hanya "LGTM". Dan ketika engineer lain melihat review tersebut, mereka mengikuti contoh yang mereka lihat.

Aturan detail, tapi culture tidak terbentuk.

---

## Dampak Teknis & Non-Teknis

Membangun code quality dengan aturan punya dampak yang jelas:

**Dampak teknis:**

- Kode tidak konsisten karena engineer mengikuti contoh yang berbeda-beda
- Code review tidak efektif karena tidak ada contoh review yang baik
- Testing tidak membantu karena tidak ada contoh test yang baik

**Dampak non-teknis:**

- Engineer frustrasi karena merasa "selalu ada aturan baru"
- Trust menurun karena ada gap antara aturan dan praktik
- Culture code quality jadi sekadar aturan, bukan praktik nyata

---

## Pendekatan Praktis

Code quality yang baik dibangun dengan **culture, bukan aturan**. Beberapa pendekatan yang bisa dipakai:

**1. Lead by example**

Engineer yang lebih berpengalaman harus menjadi contoh. Kalau mereka menulis kode yang baik, melakukan code review yang proper, dan menulis test yang membantu—engineer lain akan mengikuti.

Contoh: Kalau kamu ingin culture code review yang baik, jangan hanya menulis aturan. Lakukan code review yang baik untuk setiap PR. Engineer lain akan melihat dan belajar.

**2. Konsistensi lebih penting dari kesempurnaan**

Lebih baik konsisten melakukan hal yang baik, daripada sesekali melakukan hal yang sempurna. Culture dibangun dari kebiasaan, bukan dari insiden.

Contoh: Lebih baik setiap PR di-review dengan standar yang konsisten (meski tidak sempurna), daripada sesekali ada review yang sangat detail tapi kebanyakan PR di-approve tanpa review.

**3. Perbaiki contoh yang buruk**

Ketika ada contoh yang buruk (misalnya kode yang tidak mengikuti standar), perbaiki—jangan hanya mengingatkan. Perbaikan yang nyata lebih efektif daripada reminder.

Contoh: Ketika kamu melihat kode yang tidak mengikuti standar, jangan hanya comment "ini tidak sesuai standar". Refactor kode itu, atau buat PR untuk memperbaikinya. Ini menunjukkan bahwa standar itu penting, bukan sekadar aturan.

**4. Rayakan contoh yang baik**

Ketika ada engineer yang melakukan hal yang baik (misalnya code review yang sangat detail, atau refactoring yang memperbaiki technical debt), rayakan—jangan hanya dianggap biasa. Ini menunjukkan bahwa hal-hal ini dihargai.

Contoh: Di standup atau retrospektif, sebutkan engineer yang melakukan code review yang baik atau refactoring yang membantu. Ini memberi contoh yang jelas tentang apa yang dihargai.

**5. Aturan hanya untuk dokumentasi**

Aturan itu penting untuk dokumentasi dan onboarding. Tapi aturan tidak membangun culture. Yang membangun culture adalah contoh yang konsisten.

Jadi, tulis aturan yang jelas. Tapi ingat: aturan itu hanya efektif kalau diikuti oleh yang membuat aturan. Kalau tidak, aturan itu hanya dokumen yang tidak berarti.

**6. Fokus pada "mengapa" bukan "apa"**

Ketika menjelaskan code quality, fokus pada "mengapa" bukan "apa". Ini membantu engineer memahami nilai di balik praktik, bukan sekadar mengikuti aturan.

Contoh: Daripada bilang "setiap function harus punya test", bilang "test membantu kita refactor dengan confidence dan menangkap bug sebelum di-deploy. Tanpa test, refactoring jadi menakutkan dan bug sering muncul di production."

**7. Buat proses yang memudahkan, bukan memaksa**

Proses yang memudahkan engineer menulis kode yang baik lebih efektif daripada aturan yang memaksa. Contoh: setup linter yang otomatis, atau template untuk test yang mudah dipakai.

---

## Trade-off yang Harus Diterima

Membangun code quality dengan culture punya trade-off:

**Kelemahan:**

- Butuh waktu lebih lama karena harus konsisten dalam jangka panjang
- Butuh komitmen dari engineer yang lebih berpengalaman
- Tidak bisa "dipaksakan" dengan cepat seperti aturan

**Keuntungan:**

- Culture yang dibangun lebih kuat dan bertahan lama
- Engineer benar-benar menulis kode yang baik, bukan sekadar mengikuti aturan
- Trust dan respect lebih tinggi karena tidak ada gap antara aturan dan praktik

Trade-off ini sepadan. Lebih baik invest waktu untuk membangun culture yang kuat, daripada membuat aturan yang tidak diikuti.

---

## Contoh Praktis: Membangun Culture Code Review

Berikut contoh bagaimana membangun culture code review dari contoh, bukan aturan:

**Yang tidak efektif (dari aturan):**

```markdown
## Code Review Guidelines

1. Setiap PR harus di-review minimal 2 orang
2. Review harus cek:
   - Code quality
   - Test coverage
   - Documentation
   - Performance
3. Review harus memberikan feedback yang konstruktif
```

Hasil: PR tetap di-review dengan cepat dan tidak detail, karena tidak ada contoh review yang baik.

**Yang efektif (dari contoh):**

Engineer yang lebih berpengalaman melakukan code review yang detail untuk setiap PR, termasuk PR dari engineer lain. Di code review, mereka tidak hanya cek syntax, tapi juga:
- Apakah kode mudah di-maintain?
- Apakah ada edge case yang terlewat?
- Apakah ada cara yang lebih baik?
- Apakah ada dampak pada sistem lain?

Mereka memberikan feedback yang konstruktif, bukan hanya "LGTM" atau "fix this".

Hasil: Engineer lain melihat contoh review yang baik, dan mereka mulai melakukan hal yang sama. Culture code review yang baik terbentuk secara alami.

---

## Contoh Praktis: Membangun Culture Testing

Berikut contoh bagaimana membangun culture testing dari contoh, bukan aturan:

**Yang tidak efektif (dari aturan):**

```markdown
## Testing Requirements

1. Setiap function harus punya test
2. Code coverage minimal 80%
3. Test harus cover happy path dan edge case
```

Hasil: Test tetap hanya test happy path, karena tidak ada contoh test yang baik.

**Yang efektif (dari contoh):**

Engineer yang lebih berpengalaman menulis test yang baik untuk setiap fitur. Test tidak hanya test happy path, tapi juga:
- Edge case (null, undefined, empty, boundary)
- Failure scenario (network error, database error)
- Integration dengan sistem lain

Mereka menjelaskan "mengapa" test ini penting, bukan hanya "apa" yang harus di-test.

Hasil: Engineer lain melihat contoh test yang baik, dan mereka mulai menulis test yang sama. Culture testing yang baik terbentuk secara alami.

---

## Penutup

Code quality tidak bisa dibangun dengan aturan. Code quality dibangun dengan **culture—dan culture dibangun dengan contoh, bukan aturan**.

Jangan mulai dari menulis aturan. Mulai dari **menjadi contoh yang baik**. Tulis kode yang baik. Lakukan code review yang proper. Tulis test yang membantu. Ikuti best practice yang kamu inginkan.

Aturan itu penting untuk dokumentasi. Tapi yang membangun culture adalah contoh—bukan aturan.

Jadi, kalau kamu ingin membangun culture code quality:
- Lead by example
- Konsistensi lebih penting dari kesempurnaan
- Perbaiki contoh yang buruk
- Rayakan contoh yang baik
- Aturan hanya untuk dokumentasi
- Fokus pada "mengapa" bukan "apa"
- Buat proses yang memudahkan, bukan memaksa

Dan yang paling penting: **jangan terlalu banyak aturan**. Aturan yang terlalu banyak justru membuat engineer fokus pada "memenuhi aturan" bukan "menulis kode yang baik".

Ingat: culture itu hidup. Culture itu dibentuk oleh perilaku sehari-hari. Dan perilaku itu menular. Jadi, pastikan perilaku yang kamu tunjukkan adalah perilaku yang ingin kamu lihat di tim.

Code quality yang baik tidak datang dari aturan. Code quality yang baik datang dari engineer yang benar-benar peduli pada kualitas—dan itu dibangun dengan contoh, bukan aturan.

Membangun culture code quality yang kuat itu penting. Dan itu dimulai dari menjadi contoh yang baik, bukan dari menulis aturan yang banyak.

Culture code quality yang kuat akan membuat engineer menulis kode yang baik secara alami—bukan karena dipaksa oleh aturan, tapi karena itu adalah cara mereka bekerja.

Dan ketika culture sudah terbentuk, aturan menjadi tidak perlu. Karena engineer sudah tahu apa yang harus dilakukan, bukan karena aturan mengatakan begitu, tapi karena itu adalah cara mereka bekerja.
