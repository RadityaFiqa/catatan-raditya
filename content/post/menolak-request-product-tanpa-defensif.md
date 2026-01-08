---
title: "Cara Menolak Request Product Tanpa Terlihat Defensif"
date: 2025-07-08T10:00:00+07:00
description: "Refleksi tentang bagaimana menolak request product dengan cara yang konstruktif, tanpa terlihat defensif atau tidak kooperatif."
tags: ["engineering", "leadership", "product", "communication", "software-engineer"]
categories: ["Engineering Journey"]
draft: false
---

## Masalah yang Sering Terjadi

"Fitur ini bisa selesai dalam 1 minggu?"

"Bisa tambahkan fitur X juga?"

"Kenapa tidak bisa pakai teknologi Y? Katanya lebih cepat."

Request dari Product Manager itu biasa. Tapi tidak semua request bisa atau harus dikabulkan. Dan ketika kita menolak, sering kali kita terlihat defensif atau tidak kooperatif.

Masalahnya: **menolak request itu perlu, tapi cara menolaknya menentukan apakah kita terlihat sebagai partner atau blocker**.

Saya pernah melihat engineer yang menolak request dengan cara yang defensif: "Tidak bisa, terlalu kompleks" atau "Tidak masuk akal, tidak mungkin". Hasilnya? Product Manager merasa tidak didengar, dan hubungan jadi tegang.

Padahal, menolak request itu bukan tentang mengatakan "tidak". Menolak request itu tentang **menjelaskan constraint, memberikan alternatif, dan mencari solusi bersama**.

---

## Kenapa Ini Sering Terjadi

Ada beberapa alasan kenapa kita sering terlihat defensif ketika menolak request:

**1. Fokus pada "tidak bisa" bukan "bagaimana bisa"**

Ketika menolak request, kita sering fokus pada alasan kenapa tidak bisa, bukan pada bagaimana bisa dengan constraint yang ada. Ini membuat kita terlihat defensif.

**2. Tidak menjelaskan constraint dengan jelas**

Banyak engineer menolak request tanpa menjelaskan constraint dengan jelas. Product Manager tidak paham kenapa tidak bisa, jadi merasa tidak didengar.

**3. Tidak memberikan alternatif**

Menolak tanpa memberikan alternatif membuat kita terlihat tidak kooperatif. Padahal, dengan memberikan alternatif, kita menunjukkan bahwa kita ingin membantu, bukan memblokir.

**4. Bahasa yang terlalu teknis**

Ketika menjelaskan kenapa tidak bisa, kita sering pakai bahasa teknis yang tidak dipahami Product Manager. Ini membuat komunikasi tidak efektif.

**5. Ego dan defensif**

Beberapa engineer menolak request karena ego—tidak mau terlihat "tidak bisa" atau tidak mau mengakui constraint. Ini membuat kita terlihat defensif.

---

## Contoh Kasus Nyata

Bayangkan skenario seperti ini: Product Manager minta fitur baru dalam 1 minggu. Tapi berdasarkan estimasi, fitur itu butuh 3 minggu.

Reaksi yang defensif:

**Engineer:** "Tidak bisa, butuh 3 minggu. 1 minggu tidak mungkin."

**Product Manager:** "Tapi ini urgent. Bisa dikurangi scope-nya?"

**Engineer:** "Tetap tidak bisa. Fitur ini kompleks, tidak bisa dipaksa."

Hasilnya? Product Manager merasa tidak didengar, dan hubungan jadi tegang.

Reaksi yang konstruktif:

**Engineer:** "Saya paham ini urgent. Tapi berdasarkan estimasi, fitur lengkap butuh 3 minggu. Tapi kita bisa buat MVP dalam 1 minggu dengan scope yang dikurangi. MVP ini bisa:
- Memberikan value dasar yang dibutuhkan user
- Bisa diiterasi untuk fitur lengkap dalam 2 minggu berikutnya
- Mengurangi risiko karena kita bisa test dengan user lebih cepat

Bagaimana? Atau ada prioritas fitur yang bisa ditunda untuk mempercepat?"

Hasilnya? Product Manager merasa didengar, dan kita bisa mencari solusi bersama.

---

## Dampak Teknis & Non-Teknis

Menolak request dengan cara yang defensif punya dampak yang jelas:

**Dampak teknis:**

- Keputusan teknis jadi tidak optimal karena dibuat di bawah tekanan konflik
- Kualitas kode menurun karena harus mengikuti deadline yang tidak realistis
- Technical debt menumpuk karena tidak ada waktu untuk best practice

**Dampak non-teknis:**

- Hubungan dengan Product Manager jadi tegang
- Trust menurun karena terlihat tidak kooperatif
- Engineering terlihat sebagai blocker, bukan partner

---

## Pendekatan Praktis

Menolak request dengan cara yang konstruktif butuh pendekatan yang tepat. Beberapa pendekatan yang bisa dipakai:

**1. Mulai dengan mengakui kebutuhan**

Sebelum menolak, akui bahwa kebutuhan itu valid. Ini menunjukkan bahwa kita mendengar dan memahami, bukan hanya memblokir.

Contoh: "Saya paham ini urgent dan penting untuk user. Tapi ada constraint yang perlu kita pertimbangkan..."

**2. Jelaskan constraint dengan jelas**

Jelaskan constraint dengan bahasa yang dipahami Product Manager. Jangan pakai bahasa teknis yang terlalu kompleks.

Contoh: Daripada bilang "kita butuh refactor karena code smell", bilang "kalau tidak refactor, development fitur baru akan 2x lebih lama dan risiko bug lebih tinggi."

**3. Berikan alternatif, bukan hanya "tidak bisa"**

Ketika menolak, berikan alternatif yang mempertimbangkan constraint. Ini menunjukkan bahwa kita ingin membantu, bukan memblokir.

Contoh: "Kita tidak bisa selesai dalam 1 minggu dengan fitur lengkap. Tapi kita bisa:
- Buat MVP dalam 1 minggu, lalu iterasi untuk fitur lengkap
- Prioritaskan fitur yang paling penting dulu
- Tambah engineer untuk mempercepat (tapi butuh budget)"

**4. Fokus pada solusi, bukan masalah**

Alihkan fokus dari "kenapa tidak bisa" ke "bagaimana bisa dengan constraint yang ada". Ini membuat kita terlihat sebagai problem solver, bukan blocker.

**5. Gunakan data dan fakta**

Ketika menolak, gunakan data dan fakta, bukan opini. Ini membuat penolakan lebih objektif dan bisa dipertanggungjawabkan.

Contoh: "Berdasarkan estimasi tim, fitur ini butuh 3 minggu. Kalau dipaksa 1 minggu, berdasarkan pengalaman sebelumnya, risiko bug meningkat 40% dan waktu fix bug butuh 1 minggu tambahan."

**6. Libatkan Product Manager dalam keputusan**

Ketika menolak, libatkan Product Manager dalam mencari solusi. Ini membuat mereka merasa bagian dari solusi, bukan hanya menerima penolakan.

Contoh: "Kita punya beberapa opsi. Mana yang paling sesuai dengan prioritas bisnis?"

**7. Komunikasikan trade-off dengan jelas**

Ketika memberikan alternatif, komunikasikan trade-off dengan jelas. Ini membantu Product Manager membuat keputusan yang informed.

Contoh: "Opsi A lebih cepat tapi kurang fitur. Opsi B lebih lengkap tapi butuh waktu lebih lama. Mana yang lebih sesuai dengan kebutuhan user?"

---

## Trade-off yang Harus Diterima

Menolak request dengan cara yang konstruktif punya trade-off:

**Kelemahan:**

- Butuh waktu lebih lama untuk menjelaskan dan memberikan alternatif
- Harus lebih komunikatif dan diplomatis
- Harus lebih fleksibel dan mau mencari solusi bersama

**Keuntungan:**

- Hubungan dengan Product Manager lebih baik
- Trust meningkat karena terlihat sebagai partner, bukan blocker
- Keputusan jadi lebih optimal karena dibuat bersama

Trade-off ini sepadan. Lebih baik invest waktu untuk komunikasi yang baik daripada terus berkonflik.

---

## Contoh Praktis: Template untuk Menolak Request

Berikut template untuk menolak request dengan cara yang konstruktif:

**Template 1: Request yang tidak realistis dari segi waktu**

```markdown
1. Akui kebutuhan:
"Saya paham ini urgent dan penting untuk user."

2. Jelaskan constraint:
"Tapi berdasarkan estimasi tim, fitur lengkap butuh 3 minggu. 
Kalau dipaksa 1 minggu, ada risiko:
- Bug yang bisa menyebabkan masalah lebih besar
- Technical debt yang akan memperlambat development selanjutnya"

3. Berikan alternatif:
"Tapi kita bisa:
- Buat MVP dalam 1 minggu dengan scope yang dikurangi
- Prioritaskan fitur yang paling penting dulu
- Tambah engineer untuk mempercepat (tapi butuh budget)"

4. Libatkan dalam keputusan:
"Mana yang paling sesuai dengan prioritas bisnis?"
```

**Template 2: Request yang tidak feasible secara teknis**

```markdown
1. Akui kebutuhan:
"Saya paham user butuh fitur ini."

2. Jelaskan constraint:
"Tapi secara teknis, fitur ini tidak feasible karena:
- Infrastructure yang ada tidak support
- Butuh perubahan arsitektur yang besar
- Risiko tinggi untuk sistem yang sedang berjalan"

3. Berikan alternatif:
"Tapi kita bisa:
- Buat fitur serupa yang memberikan value yang sama, tapi lebih feasible
- Fase implementasi: mulai dengan yang feasible, lalu iterasi
- Evaluasi ulang infrastructure untuk support fitur ini di masa depan"

4. Libatkan dalam keputusan:
"Bagaimana? Atau ada prioritas lain yang bisa kita kerjakan dulu?"
```

**Template 3: Request yang tidak sesuai dengan prioritas**

```markdown
1. Akui kebutuhan:
"Saya paham fitur ini penting."

2. Jelaskan constraint:
"Tapi kita punya prioritas lain yang lebih urgent:
- Bug critical yang perlu di-fix
- Fitur yang sudah dijanjikan ke client
- Technical debt yang berbahaya"

3. Berikan alternatif:
"Tapi kita bisa:
- Masukkan ke backlog dengan prioritas tinggi
- Mulai setelah prioritas yang lebih urgent selesai
- Evaluasi ulang prioritas jika ada perubahan kebutuhan"

4. Libatkan dalam keputusan:
"Bagaimana? Atau ada prioritas yang bisa ditunda untuk fitur ini?"
```

---

## Penutup

Menolak request product itu perlu. Tapi cara menolaknya menentukan apakah kita terlihat sebagai partner atau blocker.

Jangan fokus pada "tidak bisa". Fokus pada **"bagaimana bisa dengan constraint yang ada"**. Akui kebutuhan, jelaskan constraint dengan jelas, berikan alternatif, dan libatkan Product Manager dalam mencari solusi.

Ingat: **menolak request itu bukan tentang mengatakan "tidak". Menolak request itu tentang menjelaskan constraint, memberikan alternatif, dan mencari solusi bersama**.

Jadi, ketika ada request yang tidak bisa dikabulkan:
- Mulai dengan mengakui kebutuhan
- Jelaskan constraint dengan jelas
- Berikan alternatif, bukan hanya "tidak bisa"
- Fokus pada solusi, bukan masalah
- Gunakan data dan fakta
- Libatkan Product Manager dalam keputusan
- Komunikasikan trade-off dengan jelas

Dan yang paling penting: **jangan terlihat defensif**. Kita bukan memblokir request. Kita membantu mencari solusi yang mempertimbangkan semua constraint—termasuk constraint engineering.

Sebagai engineer, salah satu skill penting yang perlu dipelajari adalah **menolak request dengan cara yang konstruktif**. Karena ketika kita bisa menolak dengan baik, kita terlihat sebagai partner yang membantu, bukan blocker yang menghambat.

Dan ketika kita terlihat sebagai partner, Product Manager akan lebih terbuka untuk mendengar constraint engineering, dan kita bisa bekerja bersama untuk membuat produk yang lebih baik.

Menolak itu perlu. Tapi menolak dengan cara yang konstruktif itu yang membuat perbedaan antara engineer yang dihargai dan engineer yang dihindari.
