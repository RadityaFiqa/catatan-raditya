---
title: "Tech Debt: Kapan Harus Dibayar, Kapan Bisa Ditunda"
date: 2025-04-09T10:00:00+07:00
description: "Refleksi tentang bagaimana memprioritaskan technical debt: mana yang harus segera dibayar, dan mana yang bisa ditunda."
tags: ["engineering", "tech-debt", "architecture", "software-engineer"]
categories: ["Engineering Journey"]
draft: false
---

## Masalah yang Sering Terjadi

"Kita punya banyak technical debt. Harus segera dibayar semua."

Pernyataan ini sering muncul di retrospektif atau planning meeting. Tapi ketika waktu untuk membayar tech debt dialokasikan, hasilnya tidak selalu jelas. Beberapa tech debt memang penting untuk dibayar. Tapi beberapa tech debt bisa ditunda tanpa masalah.

Masalahnya: **tidak semua tech debt sama. Beberapa harus segera dibayar, beberapa bisa ditunda**.

Saya pernah melihat tim yang menghabiskan 2 minggu untuk refactor kode yang "tidak clean" padahal kode itu jarang diubah dan tidak bermasalah. Atau tim yang mengabaikan tech debt yang benar-benar berbahaya karena mengira "semua tech debt sama".

Padahal tech debt itu seperti utang finansial. Ada yang berbunga tinggi dan harus segera dilunasi. Ada yang berbunga rendah dan bisa ditunda. Yang penting adalah **memahami mana yang mana**.

---

## Kenapa Ini Sering Terjadi

Ada beberapa alasan kenapa tech debt sering disamakan semua:

**1. Tidak ada klasifikasi yang jelas**

Banyak tim tidak mengklasifikasi tech debt berdasarkan dampaknya. Semua tech debt dianggap "buruk" dan harus dibayar. Padahal dampaknya berbeda-beda.

**2. Pressure untuk "perfect code"**

Beberapa engineer merasa tidak nyaman dengan tech debt. Mereka ingin semua kode "perfect". Tapi di dunia kerja, perfect code itu tidak realistis. Trade-off harus dibuat.

**3. Kurangnya data untuk prioritas**

Tanpa data tentang dampak tech debt, sulit menentukan prioritas. Engineer cenderung memprioritaskan tech debt yang "terlihat" atau yang "mengganggu", bukan yang benar-benar berbahaya.

**4. Tidak ada framework untuk menilai**

Banyak tim tidak punya framework untuk menilai tech debt. Mereka tidak tahu kapan tech debt harus dibayar, dan kapan bisa ditunda.

---

## Contoh Kasus Nyata

Saya pernah melihat tim yang menghabiskan waktu untuk refactor kode yang "tidak clean":

```javascript
// Kode yang "tidak clean" tapi tidak bermasalah
function getUserOrders(userId) {
  const orders = db.query('SELECT * FROM orders WHERE user_id = ?', [userId]);
  return orders.map(o => ({
    id: o.id,
    total: o.total,
    date: o.created_at
  }));
}
```

Kode ini "tidak clean" karena:
- Tidak ada error handling
- Tidak ada validation
- SQL query langsung di function
- Mapping logic di function

Tapi kode ini:
- Jarang diubah (terakhir diubah 6 bulan lalu)
- Tidak pernah bermasalah di production
- Mudah dipahami
- Performa baik

Tim menghabiskan 2 minggu untuk refactor ini jadi "clean code" dengan repository pattern, error handling, validation, dll. Hasilnya? Kode jadi lebih "clean", tapi tidak ada improvement yang terasa. Waktu 2 minggu itu bisa dipakai untuk fitur baru atau memperbaiki bug yang lebih penting.

Di sisi lain, ada tech debt yang benar-benar berbahaya tapi diabaikan:

```javascript
// Tech debt yang berbahaya
function processPayment(order) {
  // Direct database call tanpa transaction
  db.orders.update({ id: order.id, status: 'paid' });
  db.inventory.decrement(order.items);
  emailService.send({ to: order.email, subject: 'Payment received' });
  
  // Jika email gagal, order sudah di-update tapi inventory belum
  // Data jadi inconsistent
}
```

Tech debt ini berbahaya karena:
- Bisa menyebabkan data inconsistency
- Sudah pernah menyebabkan bug di production
- Sulit di-debug ketika terjadi masalah
- Dampaknya besar untuk bisnis

Tapi tech debt ini diabaikan karena "tidak terlihat" atau "belum ada masalah lagi". Padahal ini yang seharusnya diprioritaskan.

---

## Dampak Teknis & Non-Teknis

Tidak memprioritaskan tech debt dengan benar punya dampak yang jelas:

**Dampak teknis:**

- Waktu terbuang untuk membayar tech debt yang tidak penting
- Tech debt yang berbahaya tidak ditangani sampai menyebabkan masalah
- Refactoring yang tidak perlu menambah risiko bug

**Dampak non-teknis:**

- Tim frustrasi karena merasa "selalu ada tech debt"
- Stakeholder kecewa karena waktu dihabiskan untuk "perbaikan yang tidak terlihat"
- Prioritas jadi tidak jelas karena semua tech debt dianggap sama

---

## Pendekatan Praktis

Tech debt harus diprioritaskan berdasarkan **dampak dan urgensi**. Beberapa pendekatan yang bisa dipakai:

**1. Klasifikasi tech debt berdasarkan dampak**

Tidak semua tech debt sama. Klasifikasikan berdasarkan:
- **Critical**: Bisa menyebabkan bug, data loss, atau security issue
- **High**: Mempengaruhi maintainability atau performance secara signifikan
- **Medium**: Mempengaruhi maintainability tapi tidak critical
- **Low**: Tidak mempengaruhi maintainability atau performance

**2. Prioritaskan berdasarkan frekuensi perubahan**

Tech debt di kode yang sering diubah lebih penting daripada tech debt di kode yang jarang diubah. Karena:
- Kode yang sering diubah lebih mungkin menyebabkan bug
- Waktu yang dihabiskan untuk maintain kode yang "tidak clean" lebih besar

**3. Prioritaskan berdasarkan dampak bisnis**

Tech debt yang mempengaruhi user experience atau revenue lebih penting daripada tech debt yang hanya mempengaruhi "code quality". Contoh:
- Tech debt yang menyebabkan slow response time → High priority
- Tech debt yang menyebabkan inconsistent data → High priority
- Tech debt yang hanya "tidak clean" tapi tidak bermasalah → Low priority

**4. Buat tech debt register**

Dokumentasikan tech debt dengan:
- Deskripsi masalah
- Dampak (critical/high/medium/low)
- Frekuensi perubahan kode
- Estimasi effort untuk memperbaiki
- Prioritas (harus dibayar / bisa ditunda / bisa diabaikan)

Ini membantu tim membuat keputusan yang lebih baik tentang mana yang harus dibayar.

**5. Alokasikan waktu secara proporsional**

Tidak perlu membayar semua tech debt sekaligus. Alokasikan waktu secara proporsional:
- 20-30% waktu untuk membayar tech debt critical/high
- Sisanya untuk fitur baru atau perbaikan bug

Dan pastikan tech debt yang dibayar benar-benar memberikan value, bukan sekadar "memperbaiki code quality".

---

## Trade-off yang Harus Diterima

Membayar tech debt punya trade-off:

**Kelemahan:**

- Butuh waktu yang bisa dipakai untuk fitur baru
- Tidak selalu memberikan value yang langsung terlihat
- Bisa jadi over-engineering kalau tidak diprioritaskan dengan benar

**Keuntungan:**

- Kode lebih mudah di-maintain
- Bug lebih mudah di-debug
- Development lebih cepat di masa depan

Trade-off ini sepadan ketika tech debt yang dibayar benar-benar memberikan value. Tapi tidak sepadan ketika tech debt yang dibayar hanya "memperbaiki code quality" tanpa meningkatkan maintainability atau mengurangi bug.

---

## Contoh Praktis: Kapan Tech Debt Harus Dibayar

Berikut beberapa contoh kapan tech debt harus dibayar, dan kapan bisa ditunda:

**1. Tech debt yang menyebabkan bug → Harus dibayar**

```javascript
// ❌ Tech debt: Race condition
let counter = 0;

function increment() {
  counter++; // Bisa terjadi race condition
}

// ✅ Harus dibayar: Gunakan atomic operation
function increment() {
  counter = atomicIncrement(counter);
}
```

**2. Tech debt yang mempengaruhi performance → Harus dibayar**

```javascript
// ❌ Tech debt: N+1 query problem
function getUserOrders(userId) {
  const orders = db.query('SELECT * FROM orders WHERE user_id = ?', [userId]);
  return orders.map(order => ({
    ...order,
    items: db.query('SELECT * FROM order_items WHERE order_id = ?', [order.id])
  }));
}

// ✅ Harus dibayar: Gunakan JOIN atau batch query
function getUserOrders(userId) {
  return db.query(`
    SELECT o.*, oi.* 
    FROM orders o 
    JOIN order_items oi ON o.id = oi.order_id 
    WHERE o.user_id = ?
  `, [userId]);
}
```

**3. Tech debt yang hanya "tidak clean" → Bisa ditunda**

```javascript
// ✅ Bisa ditunda: Kode "tidak clean" tapi tidak bermasalah
function formatDate(date) {
  const d = new Date(date);
  return `${d.getDate()}/${d.getMonth() + 1}/${d.getFullYear()}`;
}

// Tidak perlu di-refactor jadi "clean code" kalau:
// - Jarang diubah
// - Tidak pernah bermasalah
// - Mudah dipahami
```

**4. Tech debt yang mempengaruhi security → Harus dibayar**

```javascript
// ❌ Tech debt: SQL injection risk
function getUser(id) {
  return db.query(`SELECT * FROM users WHERE id = ${id}`);
}

// ✅ Harus dibayar: Gunakan parameterized query
function getUser(id) {
  return db.query('SELECT * FROM users WHERE id = ?', [id]);
}
```

**5. Tech debt yang mempengaruhi maintainability secara signifikan → Harus dibayar**

```javascript
// ❌ Tech debt: Duplikasi logic di banyak tempat
function calculatePriceA(items) {
  let total = 0;
  for (const item of items) {
    total += item.price * item.quantity;
  }
  if (total > 1000) total *= 0.9;
  return total;
}

function calculatePriceB(items) {
  let total = 0;
  for (const item of items) {
    total += item.price * item.quantity;
  }
  if (total > 1000) total *= 0.9;
  return total;
}

// ✅ Harus dibayar: Extract ke function reusable
function calculatePrice(items) {
  let total = items.reduce((sum, item) => sum + item.price * item.quantity, 0);
  if (total > 1000) total *= 0.9;
  return total;
}
```

---

## Penutup

Tech debt itu seperti utang finansial. Ada yang berbunga tinggi dan harus segera dilunasi. Ada yang berbunga rendah dan bisa ditunda.

Yang penting adalah **memahami mana yang mana**, dan memprioritaskan berdasarkan dampak dan urgensi. Jangan samakan semua tech debt. Jangan bayar tech debt yang tidak penting sementara tech debt yang berbahaya diabaikan.

Fokus pada tech debt yang:
- Bisa menyebabkan bug atau data inconsistency
- Mempengaruhi performance atau user experience
- Mempengaruhi maintainability secara signifikan
- Ada di kode yang sering diubah

Dan jangan terlalu khawatir dengan tech debt yang:
- Hanya "tidak clean" tapi tidak bermasalah
- Ada di kode yang jarang diubah
- Tidak mempengaruhi maintainability atau performance

Ingat: tech debt itu normal. Tidak ada sistem yang perfect. Yang penting adalah **mengelola tech debt dengan bijak**, bukan menghilangkannya semua.

Jadi, klasifikasikan tech debt. Prioritaskan berdasarkan dampak. Dan alokasikan waktu secara proporsional. Tech debt yang penting akan terbayar, dan tech debt yang tidak penting bisa ditunda—atau bahkan diabaikan.

Tech debt itu utang. Tapi seperti utang finansial, tidak semua utang harus segera dilunasi. Yang penting adalah **memahami mana yang berbunga tinggi, dan mana yang bisa ditunda**.
