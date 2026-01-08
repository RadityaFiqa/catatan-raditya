---
title: "Keputusan Teknis yang Benar di Hari Ini Bisa Salah Besok"
date: 2025-11-20T10:00:00+07:00
description: "Refleksi tentang bagaimana keputusan teknis yang benar di satu konteks bisa jadi salah di konteks lain, dan bagaimana menghadapinya."
tags: ["engineering", "decision-making", "architecture", "software-engineer"]
categories: ["Engineering Journey"]
draft: false
---

## Masalah yang Sering Terjadi

"Keputusan ini benar untuk sekarang."

"Teknologi ini yang terbaik saat ini."

"Arsitektur ini cocok untuk kebutuhan kita."

Keputusan teknis yang dibuat hari ini didasarkan pada konteks hari ini—kebutuhan, constraint, dan teknologi yang ada. Tapi konteks berubah. Kebutuhan berubah. Teknologi berubah. Dan keputusan yang benar hari ini bisa jadi salah besok.

Masalahnya: **keputusan teknis itu tidak absolut. Keputusan yang benar di satu konteks bisa jadi salah di konteks lain. Dan ketika konteks berubah, keputusan yang dulu benar bisa jadi tidak relevan lagi**.

Saya pernah melihat sistem yang menggunakan teknologi yang "terbaik" saat itu. Tapi beberapa tahun kemudian, teknologi itu sudah tidak di-maintain, atau ada teknologi baru yang lebih baik. Keputusan yang benar di hari itu jadi masalah di hari ini.

---

## Kenapa Ini Sering Terjadi

Ada beberapa alasan kenapa keputusan teknis yang benar hari ini bisa jadi salah besok:

**1. Konteks berubah**

Kebutuhan berubah. Constraint berubah. Teknologi berubah. Keputusan yang dibuat berdasarkan konteks lama bisa jadi tidak relevan lagi ketika konteks berubah.

**2. Teknologi berkembang**

Teknologi berkembang dengan cepat. Teknologi yang "terbaik" hari ini bisa jadi sudah usang beberapa tahun kemudian. Atau ada teknologi baru yang lebih baik.

**3. Sistem berkembang**

Sistem berkembang. Arsitektur yang cocok untuk sistem kecil bisa jadi tidak cocok untuk sistem besar. Keputusan yang benar untuk MVP bisa jadi tidak cocok untuk production yang sudah besar.

**4. Tidak ada yang sempurna**

Tidak ada keputusan teknis yang sempurna untuk selamanya. Setiap keputusan punya trade-off, dan trade-off itu bisa berubah seiring waktu.

---

## Contoh Kasus Nyata

Bayangkan skenario seperti ini: Di awal, sistem menggunakan monolith karena lebih sederhana dan cepat untuk development.

**Keputusan di awal (benar untuk konteks saat itu):**

```javascript
// ✅ Monolith - benar untuk sistem kecil
// Semua fitur di satu aplikasi
// Development cepat
// Deployment sederhana
// Tidak perlu manage banyak service

// app.js
app.post('/api/users', createUser);
app.post('/api/orders', createOrder);
app.post('/api/payments', processPayment);

// Semua di satu codebase
// Semua di satu database
// Semua di satu deployment
```

Keputusan ini benar untuk sistem kecil. Tapi ketika sistem berkembang:

**Masalah yang muncul:**

- Tim jadi besar, banyak developer bekerja di codebase yang sama
- Deployment jadi lambat karena harus deploy semua fitur sekaligus
- Scaling jadi sulit karena tidak bisa scale per-fitur
- Technology stack terikat—tidak bisa pakai teknologi berbeda untuk fitur berbeda

**Keputusan yang dulu benar jadi tidak cocok lagi:**

```javascript
// ❌ Monolith jadi masalah untuk sistem besar
// - Codebase terlalu besar, sulit di-maintain
// - Deployment lambat karena harus deploy semua
// - Scaling sulit karena semua fitur terikat
// - Technology stack terikat
```

**Contoh lain: Database choice**

```javascript
// ✅ Di awal: PostgreSQL - benar untuk kebutuhan saat itu
// - Relational data
// - Complex queries
// - ACID transactions

const users = await db.query(`
  SELECT u.*, o.*, p.*
  FROM users u
  JOIN orders o ON u.id = o.user_id
  JOIN payments p ON o.id = p.order_id
  WHERE u.status = 'active'
  ORDER BY o.created_at DESC
`);
```

Tapi ketika kebutuhan berubah:

```javascript
// ❌ PostgreSQL jadi tidak cocok untuk use case baru
// - Butuh high write throughput (PostgreSQL kurang optimal)
// - Butuh horizontal scaling (PostgreSQL sulit)
// - Butuh schema-less (PostgreSQL tidak support dengan baik)

// Use case baru butuh NoSQL
// Tapi sudah terikat dengan PostgreSQL
// Migrate jadi sulit karena banyak dependency
```

---

## Dampak Teknis & Non-Teknis

Keputusan teknis yang dulu benar tapi jadi tidak relevan punya dampak yang jelas:

**Dampak teknis:**

- Sistem jadi tidak optimal untuk kebutuhan baru
- Migrate ke solusi yang lebih cocok jadi sulit
- Technical debt menumpuk karena harus maintain solusi yang tidak optimal

**Dampak non-teknis:**

- Frustrasi karena sistem tidak bisa mengikuti kebutuhan baru
- Waktu terbuang untuk workaround solusi yang tidak optimal
- Trust menurun karena sistem tidak bisa berkembang dengan baik

---

## Pendekatan Praktis

Menghadapi keputusan teknis yang dulu benar tapi jadi tidak relevan butuh pendekatan yang tepat. Beberapa pendekatan yang bisa dipakai:

**1. Terima bahwa keputusan bisa berubah**

Keputusan teknis itu tidak final. Ketika konteks berubah, keputusan bisa berubah. Terima bahwa perubahan itu normal.

**2. Buat keputusan yang bisa di-reverse**

Ketika membuat keputusan, pertimbangkan apakah keputusan itu bisa di-reverse jika konteks berubah. Keputusan yang bisa di-reverse lebih aman daripada yang tidak bisa.

**3. Monitor konteks dan kebutuhan**

Monitor konteks dan kebutuhan secara berkala. Ketika konteks berubah, evaluasi apakah keputusan masih relevan.

**4. Siap untuk refactor**

Siap untuk refactor ketika keputusan tidak relevan lagi. Refactor itu normal, bukan kegagalan.

**5. Dokumentasikan konteks keputusan**

Dokumentasikan konteks ketika membuat keputusan. Ini membantu memahami kenapa keputusan dibuat, dan kapan keputusan perlu diubah.

---

## Trade-off yang Harus Diterima

Membuat keputusan yang bisa berubah punya trade-off:

**Keuntungan:**

- Sistem bisa berkembang dengan baik
- Tidak terikat dengan keputusan yang tidak relevan
- Bisa adapt dengan kebutuhan baru

**Kelemahan:**

- Butuh effort untuk refactor
- Bisa jadi tidak optimal jika terlalu sering berubah
- Butuh waktu untuk evaluasi dan perubahan

Trade-off ini sepadan. Lebih baik invest effort untuk refactor daripada terikat dengan keputusan yang tidak relevan.

---

## Contoh Praktis: Evolusi Keputusan Teknis

Berikut contoh bagaimana keputusan teknis bisa berevolusi:

**Fase 1: Monolith (benar untuk sistem kecil)**

```javascript
// ✅ Benar untuk sistem kecil
// - Development cepat
// - Deployment sederhana
// - Tidak perlu manage banyak service

app.post('/api/users', createUser);
app.post('/api/orders', createOrder);
```

**Fase 2: Modular Monolith (transisi)**

```javascript
// ✅ Transisi sebelum microservices
// - Code terpisah per-module
// - Tapi masih satu deployment
// - Lebih mudah migrate ke microservices nanti

// modules/users/routes.js
app.post('/api/users', createUser);

// modules/orders/routes.js
app.post('/api/orders', createOrder);
```

**Fase 3: Microservices (benar untuk sistem besar)**

```javascript
// ✅ Benar untuk sistem besar
// - Bisa scale per-service
// - Bisa deploy per-service
// - Bisa pakai teknologi berbeda

// service-users/index.js
app.post('/api/users', createUser);

// service-orders/index.js
app.post('/api/orders', createOrder);
```

**Fase 4: Evaluasi kembali (ketika microservices jadi masalah)**

```javascript
// ✅ Evaluasi kembali ketika microservices jadi masalah
// - Complexity terlalu tinggi
// - Overhead terlalu besar
// - Kembali ke modular monolith atau hybrid

// Hybrid: Core services tetap microservices
// Supporting services kembali ke monolith
```

Evolusi ini normal. Keputusan yang benar di satu fase bisa jadi tidak cocok di fase berikutnya.

---

## Penutup

Keputusan teknis yang benar di hari ini bisa jadi salah besok. Konteks berubah, kebutuhan berubah, teknologi berubah. Dan keputusan yang dibuat berdasarkan konteks lama bisa jadi tidak relevan lagi.

Yang penting adalah menerima bahwa keputusan bisa berubah, dan siap untuk refactor ketika keputusan tidak relevan lagi. Buat keputusan yang bisa di-reverse, monitor konteks secara berkala, dan dokumentasikan konteks keputusan.

Jadi, ketika membuat keputusan teknis:
- Terima bahwa keputusan bisa berubah
- Buat keputusan yang bisa di-reverse
- Monitor konteks dan kebutuhan
- Siap untuk refactor
- Dokumentasikan konteks keputusan

Dan yang paling penting: **keputusan teknis itu tidak absolut**. Keputusan yang benar di satu konteks bisa jadi salah di konteks lain. Dan ketika konteks berubah, keputusan perlu diubah juga.

Ingat: perubahan keputusan itu bukan kegagalan. Itu adalah bagian dari evolusi sistem. Sistem yang baik adalah sistem yang bisa berevolusi, bukan sistem yang terikat dengan keputusan yang tidak relevan.

Keputusan teknis yang benar hari ini bisa jadi salah besok. Tapi itu tidak masalah—yang penting adalah siap untuk berubah ketika konteks berubah.
