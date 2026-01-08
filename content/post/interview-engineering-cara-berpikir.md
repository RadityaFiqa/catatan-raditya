---
title: "Interview Engineering Lebih Menguji Cara Berpikir daripada Hafalan"
date: 2024-12-10T10:00:00+07:00
description: "Refleksi tentang proses interview engineering yang seharusnya fokus pada cara berpikir, bukan hafalan syntax atau algoritma."
tags: ["career", "engineering", "interview", "software-engineer"]
categories: ["Engineering Journey"]
draft: false
---

## Masalah yang Sering Terjadi

"Bisa jelaskan perbedaan antara `map` dan `forEach` di JavaScript?"

Pertanyaan seperti ini sering muncul di interview engineering. Kandidat yang hafal jawabannya langsung menang. Yang tidak hafal, meski punya pengalaman nyata, langsung tereliminasi.

Masalahnya: **interview engineering terlalu sering menguji hafalan, bukan cara berpikir**.

Banyak kandidat yang lancar menjelaskan perbedaan `Promise.all` dan `Promise.allSettled`, tapi bingung ketika ditanya: "Kalau ada 10 request yang 3 di antaranya bisa gagal, dan kita butuh semua hasil—yang mana lebih tepat?"

Kandidat seperti ini tahu syntax-nya, tapi tidak paham trade-off di baliknya.

---

## Kenapa Ini Sering Terjadi

Ada beberapa alasan kenapa interview engineering terjebak pada hafalan:

**1. Lebih mudah diukur**

Menilai hafalan itu objektif: benar atau salah. Menilai cara berpikir lebih subjektif dan butuh waktu lebih lama. Interviewer yang terburu-buru cenderung pilih yang mudah.

**2. Asumsi yang salah**

Banyak yang mengira: kalau kandidat hafal konsep, berarti dia paham. Padahal hafal dan paham itu berbeda. Hafal itu ingatan jangka pendek. Paham itu kemampuan menerapkan konsep di konteks baru.

**3. Copy-paste dari sumber lain**

Banyak pertanyaan interview diambil langsung dari LeetCode atau artikel populer. Pertanyaan-pertanyaan itu memang bagus untuk latihan, tapi tidak selalu relevan untuk menilai kemampuan kerja nyata.

---

## Contoh Kasus Nyata

Bayangkan skenario interview seperti ini: Kandidat untuk posisi backend engineer menjawab pertanyaan standar tentang database indexing dengan sempurna. Tapi ketika diberi skenario:

> "Ada tabel `orders` dengan 10 juta row. Query `SELECT * FROM orders WHERE user_id = ? AND status = 'pending'` lambat. Index sudah ada di `user_id`. Masalahnya apa, dan solusinya?"

Jawaban pertama yang sering muncul: "Tambahkan index di `status`."

Jawaban itu tidak salah, tapi tidak lengkap. Yang lebih penting: **kenapa query itu lambat di production, padahal di development cepat?** 

Kandidat yang berpikir lebih dalam akan bertanya:
- Berapa banyak row yang match `user_id`?
- Apakah ada filter lain yang bisa mengurangi hasil?
- Apakah `status` punya cardinality yang tinggi?
- Apakah ada masalah dengan query plan?

Ini bukan soal hafal syntax SQL. Ini soal **kemampuan menganalisis masalah dari berbagai sudut**.

---

## Dampak Teknis & Non-Teknis

Ketika interview hanya menguji hafalan, dampaknya terasa di production:

**Dampak teknis:**

- Kode yang ditulis mengikuti pola yang dihafal, bukan yang sesuai konteks
- Masalah performance tidak terdeteksi sejak awal karena tidak ada kebiasaan menganalisis trade-off
- Refactoring sulit karena tidak ada pemahaman mendalam tentang "kenapa kode ini ditulis begini"

**Dampak non-teknis:**

- Tim kehilangan engineer yang sebenarnya bisa berkontribusi, hanya karena tidak hafal jawaban standar
- Culture engineering jadi fokus pada "tahu banyak hal" bukan "memecahkan masalah dengan baik"
- Onboarding jadi lebih lama karena engineer baru harus belajar ulang cara berpikir, bukan sekadar syntax

---

## Pendekatan Praktis

Interview engineering yang baik fokus pada **cara berpikir, bukan hafalan**. Beberapa pendekatan yang bisa dipakai:

**1. Skenario berbasis masalah nyata**

Alihkan dari "jelaskan konsep X" ke "bagaimana kamu menyelesaikan masalah Y". Contoh:

> "Ada API endpoint yang tiba-tiba lambat. Dari log terlihat query database yang sama dipanggil berulang kali. Bagaimana kamu mendiagnosis dan memperbaikinya?"

Pertanyaan ini tidak butuh hafalan. Butuh kemampuan menganalisis, bertanya, dan merancang solusi.

**2. Diskusi trade-off**

Tanyakan trade-off, bukan jawaban benar-salah. Contoh:

```javascript
// Opsi A: Simple, tapi tidak scalable
function getUserOrders(userId) {
  return db.query('SELECT * FROM orders WHERE user_id = ?', [userId]);
}

// Opsi B: Complex, tapi lebih efisien
function getUserOrders(userId) {
  return db.query(`
    SELECT o.*, u.name 
    FROM orders o 
    JOIN users u ON o.user_id = u.id 
    WHERE o.user_id = ? 
    LIMIT 100
  `, [userId]);
}
```

Tanyakan: "Kapan kamu pilih A, kapan pilih B? Apa trade-off-nya?"

**3. Pair programming ringan**

Minta kandidat menulis kode sederhana sambil berpikir keras. Bukan soal menyelesaikan algoritma kompleks, tapi soal **menjelaskan keputusan yang diambil**.

Contoh: "Tulis fungsi untuk validasi email. Tapi jelaskan kenapa kamu pilih pendekatan ini, bukan yang lain."

**4. Pertanyaan terbuka**

Alihkan dari "apa itu X?" ke "bagaimana kamu belajar tentang X?" atau "kapan terakhir kali kamu pakai X dan kenapa?"

Ini menguji kemampuan belajar dan refleksi, bukan hafalan.

---

## Trade-off yang Harus Diterima

Mengubah format interview punya trade-off:

**Kelemahan:**

- Butuh waktu lebih lama untuk menilai kandidat
- Hasilnya lebih subjektif, butuh diskusi antar interviewer
- Tidak semua kandidat nyaman dengan format ini (beberapa lebih suka pertanyaan jelas)

**Keuntungan:**

- Mendapat engineer yang benar-benar bisa berpikir, bukan sekadar hafal
- Culture engineering jadi lebih fokus pada problem-solving
- Onboarding lebih cepat karena engineer baru sudah punya cara berpikir yang tepat

Trade-off ini sepadan. Lebih baik invest waktu di interview daripada menghabiskan waktu memperbaiki kode yang ditulis engineer yang tidak bisa berpikir.

---

## Penutup

Interview engineering seharusnya menguji **kemampuan memecahkan masalah, bukan hafalan syntax atau algoritma**.

Kandidat yang hafal semua konsep tapi tidak bisa menganalisis masalah, akan jadi beban di production. Kandidat yang bisa berpikir, meski tidak hafal semua detail, akan lebih cepat adapt dan berkontribusi.

Pertanyaan interview yang baik bukan yang jawabannya ada di dokumentasi. Tapi yang memaksa kandidat untuk **berpikir, menganalisis, dan mengambil keputusan**—persis seperti yang dilakukan engineer di production setiap hari.
