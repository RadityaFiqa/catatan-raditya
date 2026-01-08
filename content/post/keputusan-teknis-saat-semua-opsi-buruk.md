---
title: "Cara Mengambil Keputusan Teknis Saat Semua Opsi Terlihat Buruk"
date: 2025-05-24T10:00:00+07:00
description: "Refleksi tentang bagaimana mengambil keputusan teknis ketika semua opsi memiliki trade-off yang tidak ideal."
tags: ["engineering", "decision-making", "leadership", "software-engineer"]
categories: ["Engineering Journey"]
draft: false
---

## Masalah yang Sering Terjadi

"Kita harus pilih: refactor sekarang atau nanti?"

"Migrasi ke teknologi baru atau tetap pakai yang lama?"

"Tambah engineer atau outsource?"

Pertanyaan seperti ini sering muncul di dunia engineering. Dan sering kali, semua opsi terlihat buruk. Refactor sekarang = risiko tinggi, tapi nanti = technical debt menumpuk. Migrasi = learning curve, tapi tetap = teknologi usang. Tambah engineer = butuh waktu onboarding, tapi outsource = kurang kontrol.

Masalahnya: **di dunia engineering, tidak ada keputusan yang sempurna. Semua punya trade-off**.

Tapi di dunia kerja, keputusan tetap harus dibuat. Dan keputusan itu harus dibuat dengan sadar, bukan karena terpaksa atau menunda-nunda.

---

## Kenapa Ini Sering Terjadi

Ada beberapa alasan kenapa semua opsi sering terlihat buruk:

**1. Constraint yang bertentangan**

Di dunia engineering, constraint sering bertentangan. Butuh cepat tapi juga butuh kualitas. Butuh fleksibel tapi juga butuh stabil. Butuh scalable tapi juga butuh simple. Ketika constraint bertentangan, semua opsi terlihat buruk.

**2. Ketidakpastian masa depan**

Kita tidak tahu apa yang akan terjadi di masa depan. Teknologi baru mungkin lebih baik, tapi mungkin juga tidak. Refactor mungkin membantu, tapi mungkin juga menambah bug. Ketidakpastian ini membuat semua opsi terlihat berisiko.

**3. Pressure dari berbagai pihak**

Product ingin fitur cepat. Business ingin cost rendah. Engineering ingin kualitas tinggi. Pressure dari berbagai pihak membuat semua opsi terlihat tidak memuaskan semua orang.

**4. Kurangnya data untuk keputusan**

Banyak keputusan teknis dibuat tanpa data yang cukup. Tanpa data, sulit menilai trade-off dengan akurat. Semua opsi terlihat sama buruknya karena tidak ada yang tahu mana yang lebih baik.

---

## Contoh Kasus Nyata

Bayangkan skenario seperti ini: Sistem legacy yang sudah berjalan 5 tahun mulai menunjukkan masalah. Performance lambat, bug sering muncul, dan sulit di-maintain. Tapi sistem ini critical untuk bisnis—jika down, revenue langsung terpengaruh.

Opsi yang ada:

**Opsi A: Refactor bertahap**

- Pro: Risiko rendah, bisa dilakukan sambil tetap develop fitur baru
- Con: Butuh waktu lama, technical debt tetap ada selama proses
- Trade-off: Lebih aman, tapi masalah tidak langsung selesai

**Opsi B: Big bang rewrite**

- Pro: Masalah langsung selesai, bisa pakai teknologi terbaru
- Con: Risiko tinggi, butuh waktu lama tanpa fitur baru
- Trade-off: Lebih cepat selesai, tapi lebih berisiko

**Opsi C: Tetap maintain, perbaiki yang penting**

- Pro: Tidak ada risiko, bisa fokus pada fitur baru
- Con: Masalah akan semakin parah, technical debt menumpuk
- Trade-off: Paling aman, tapi masalah tidak pernah selesai

Semua opsi terlihat buruk. Tapi kita tetap harus memilih.

---

## Dampak Teknis & Non-Teknis

Tidak mengambil keputusan atau mengambil keputusan yang salah punya dampak yang jelas:

**Dampak teknis:**

- Masalah tidak pernah selesai karena terus ditunda
- Technical debt menumpuk karena tidak ada keputusan untuk mengatasinya
- Sistem menjadi semakin sulit di-maintain karena tidak ada arah yang jelas

**Dampak non-teknis:**

- Tim frustrasi karena tidak ada keputusan yang jelas
- Stakeholder kecewa karena masalah tidak pernah selesai
- Trust menurun karena terlihat tidak bisa mengambil keputusan

---

## Pendekatan Praktis

Ketika semua opsi terlihat buruk, kita tetap harus memutuskan. Beberapa pendekatan yang bisa dipakai:

**1. Identifikasi constraint yang paling penting**

Tidak semua constraint sama pentingnya. Identifikasi constraint yang paling critical, lalu pilih opsi yang memenuhi constraint itu—meski harus mengorbankan constraint lain.

Contoh: Kalau sistem critical untuk bisnis dan tidak bisa down, pilih opsi yang paling aman—meski butuh waktu lebih lama.

**2. Buat eksperimen kecil untuk mengurangi ketidakpastian**

Ketidakpastian bisa dikurangi dengan eksperimen. Buat eksperimen kecil untuk menguji opsi yang paling menjanjikan.

Contoh: Sebelum big bang rewrite, buat proof of concept untuk menguji teknologi baru. Ini mengurangi risiko dan memberikan data untuk keputusan.

**3. Pilih opsi yang bisa di-reverse**

Ketika semua opsi terlihat buruk, pilih opsi yang bisa di-reverse jika ternyata salah. Ini mengurangi risiko keputusan yang salah.

Contoh: Refactor bertahap bisa di-stop jika ternyata tidak efektif. Big bang rewrite lebih sulit di-reverse.

**4. Komunikasikan trade-off dengan jelas**

Ketika mengambil keputusan, komunikasikan trade-off dengan jelas ke semua pihak. Ini membantu semua orang memahami kenapa keputusan ini diambil, dan mengurangi konflik.

Contoh: "Kita pilih refactor bertahap karena sistem critical dan tidak bisa down. Ini berarti butuh waktu lebih lama, tapi lebih aman. Kita akan update progress setiap sprint."

**5. Set timeline untuk evaluasi**

Keputusan tidak harus final selamanya. Set timeline untuk evaluasi, dan siap untuk mengubah keputusan jika perlu.

Contoh: "Kita coba refactor bertahap selama 3 bulan. Setelah itu, kita evaluasi apakah efektif. Kalau tidak, kita pertimbangkan opsi lain."

**6. Terima bahwa tidak ada keputusan yang sempurna**

Yang penting bukan keputusan yang sempurna, tapi keputusan yang dibuat dengan sadar dan bisa dipertanggungjawabkan. Terima bahwa trade-off itu normal, dan fokus pada eksekusi yang baik.

---

## Trade-off yang Harus Diterima

Mengambil keputusan ketika semua opsi terlihat buruk punya trade-off:

**Kelemahan:**

- Harus mengorbankan sesuatu—tidak ada opsi yang memuaskan semua pihak
- Bisa jadi keputusan yang salah karena ketidakpastian
- Harus menerima kritik dari pihak yang tidak puas

**Keuntungan:**

- Masalah mulai teratasi karena ada arah yang jelas
- Tim bisa fokus pada eksekusi, bukan terus berdebat
- Bisa belajar dari keputusan dan memperbaikinya di masa depan

Trade-off ini sepadan. Lebih baik mengambil keputusan yang tidak sempurna daripada tidak mengambil keputusan sama sekali.

---

## Contoh Praktis: Framework untuk Keputusan

Berikut framework sederhana untuk mengambil keputusan ketika semua opsi terlihat buruk:

**1. List semua opsi dan trade-off-nya**

```markdown
## Opsi A: Refactor bertahap
- Pro: [list]
- Con: [list]
- Risiko: [low/medium/high]
- Waktu: [estimasi]
- Cost: [estimasi]

## Opsi B: Big bang rewrite
- Pro: [list]
- Con: [list]
- Risiko: [low/medium/high]
- Waktu: [estimasi]
- Cost: [estimasi]
```

**2. Identifikasi constraint yang paling penting**

```markdown
Constraint yang paling penting:
1. Sistem tidak boleh down (critical)
2. Butuh fitur baru dalam 3 bulan (urgent)
3. Budget terbatas (important)
```

**3. Pilih opsi yang memenuhi constraint paling penting**

```markdown
Keputusan: Opsi A (Refactor bertahap)
Alasan: Memenuhi constraint "sistem tidak boleh down"
Trade-off yang diterima: Butuh waktu lebih lama
```

**4. Buat rencana eksekusi dan evaluasi**

```markdown
Rencana:
- Sprint 1-2: Setup infrastructure untuk refactor
- Sprint 3-6: Refactor module yang paling bermasalah
- Sprint 7: Evaluasi hasil dan adjust jika perlu

Success criteria:
- Performance meningkat 20%
- Bug berkurang 30%
- Developer satisfaction meningkat
```

**5. Komunikasikan ke semua pihak**

Komunikasikan keputusan, alasan, dan trade-off ke semua pihak. Pastikan semua orang memahami kenapa keputusan ini diambil, dan apa yang diharapkan.

---

## Penutup

Di dunia engineering, tidak ada keputusan yang sempurna. Semua opsi punya trade-off. Tapi di dunia kerja, keputusan tetap harus dibuat.

Yang penting bukan keputusan yang sempurna, tapi **keputusan yang dibuat dengan sadar, berdasarkan data yang ada, dan bisa dipertanggungjawabkan**.

Jadi, ketika semua opsi terlihat buruk:
- Identifikasi constraint yang paling penting
- Buat eksperimen kecil untuk mengurangi ketidakpastian
- Pilih opsi yang bisa di-reverse
- Komunikasikan trade-off dengan jelas
- Set timeline untuk evaluasi
- Terima bahwa tidak ada keputusan yang sempurna

Dan yang paling penting: **ambil keputusan, eksekusi dengan baik, dan siap untuk belajar dari hasilnya**.

Keputusan yang tidak sempurna tapi dieksekusi dengan baik, lebih baik daripada tidak mengambil keputusan sama sekali. Karena masalah tidak akan selesai dengan sendirinya—harus ada yang memutuskan untuk menyelesaikannya.

Ingat: mengambil keputusan yang sulit ketika semua opsi terlihat buruk itu tidak mudah. Tapi itu adalah bagian dari bertumbuh sebagai engineer—belajar mengevaluasi trade-off dan membuat keputusan yang bisa dipertanggungjawabkan.
