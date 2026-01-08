---
title: "Clean Code Itu Ideal, Tapi Dunia Kerja Tidak Selalu Ideal"
date: 2025-03-25T10:00:00+07:00
description: "Refleksi tentang gap antara ideal clean code di buku dan realitas dunia kerja yang penuh constraint dan trade-off."
tags: ["engineering", "clean-code", "quality", "software-engineer"]
categories: ["Engineering Journey"]
draft: false
---

## Masalah yang Sering Terjadi

"Function harus kecil, maksimal 20 baris."

"Variable name harus descriptive, minimal 3 kata."

"Jangan ada magic number, semua harus constant."

Prinsip clean code seperti ini sering diajarkan di buku dan course. Tapi di production, kode tidak selalu mengikuti prinsip ini. Ada function yang panjang. Ada variable name yang singkat. Ada magic number yang tidak di-extract ke constant.

Masalahnya: **clean code itu ideal, tapi dunia kerja tidak selalu ideal**.

Saya pernah melihat engineer yang frustrasi karena kode di production tidak "clean" seperti di buku. Atau engineer yang menghabiskan waktu berjam-jam untuk membuat kode "perfect" padahal deadline sudah dekat.

Padahal clean code itu bukan tujuan. Clean code itu tool untuk membuat kode lebih mudah di-maintain. Dan kadang, trade-off harus dibuat.

---

## Kenapa Ini Sering Terjadi

Ada beberapa alasan kenapa clean code ideal sulit diterapkan di dunia kerja:

**1. Constraint waktu**

Deadline tidak menunggu. Ketika waktu terbatas, engineer harus memilih: kode yang "perfect" tapi telat, atau kode yang "cukup baik" tapi on time. Di dunia kerja, on time sering lebih penting.

**2. Legacy code**

Tidak semua kode dimulai dari nol. Banyak kode yang sudah ada, ditulis oleh engineer lain, dengan constraint yang berbeda. Refactoring semua kode jadi "clean" itu tidak realistis.

**3. Trade-off yang tidak jelas**

Clean code itu trade-off. Function kecil lebih mudah dipahami, tapi bisa jadi terlalu banyak function. Variable name panjang lebih descriptive, tapi bisa jadi terlalu verbose. Di dunia kerja, trade-off ini tidak selalu jelas.

**4. Perbedaan konteks**

Apa yang "clean" di satu konteks, belum tentu "clean" di konteks lain. Kode library yang dipakai banyak orang butuh lebih "clean" daripada kode internal yang hanya dipakai satu tim.

---

## Contoh Kasus Nyata

Saya pernah melihat engineer yang menghabiskan 2 hari untuk refactor function yang panjang jadi beberapa function kecil:

```javascript
// Before: Function panjang tapi straightforward
function processOrder(order) {
  // Validate order
  if (!order.items || order.items.length === 0) {
    throw new Error('Order must have items');
  }
  if (!order.customerId) {
    throw new Error('Order must have customer');
  }
  
  // Calculate total
  let total = 0;
  for (const item of order.items) {
    total += item.price * item.quantity;
  }
  
  // Apply discount
  if (order.customerId.startsWith('VIP')) {
    total = total * 0.9;
  }
  
  // Save to database
  const orderRecord = {
    id: generateId(),
    customerId: order.customerId,
    items: order.items,
    total: total,
    status: 'pending',
    createdAt: new Date()
  };
  db.orders.insert(orderRecord);
  
  // Send notification
  emailService.send({
    to: order.customerEmail,
    subject: 'Order received',
    body: `Your order #${orderRecord.id} has been received`
  });
  
  return orderRecord;
}
```

Engineer itu refactor jadi:

```javascript
// After: Banyak function kecil
function processOrder(order) {
  validateOrder(order);
  const total = calculateTotal(order);
  const discountedTotal = applyDiscount(total, order.customerId);
  const orderRecord = createOrderRecord(order, discountedTotal);
  saveOrder(orderRecord);
  sendOrderNotification(orderRecord, order.customerEmail);
  return orderRecord;
}

function validateOrder(order) {
  if (!order.items || order.items.length === 0) {
    throw new Error('Order must have items');
  }
  if (!order.customerId) {
    throw new Error('Order must have customer');
  }
}

function calculateTotal(order) {
  let total = 0;
  for (const item of order.items) {
    total += item.price * item.quantity;
  }
  return total;
}

function applyDiscount(total, customerId) {
  if (customerId.startsWith('VIP')) {
    return total * 0.9;
  }
  return total;
}

function createOrderRecord(order, total) {
  return {
    id: generateId(),
    customerId: order.customerId,
    items: order.items,
    total: total,
    status: 'pending',
    createdAt: new Date()
  };
}

function saveOrder(orderRecord) {
  db.orders.insert(orderRecord);
}

function sendOrderNotification(orderRecord, email) {
  emailService.send({
    to: email,
    subject: 'Order received',
    body: `Your order #${orderRecord.id} has been received`
  });
}
```

Refactor ini "lebih clean" menurut buku. Tapi apakah lebih baik?

Di konteks ini, function original sebenarnya sudah cukup jelas. Semua step ada di satu tempat, mudah diikuti. Refactor jadi banyak function kecil memang lebih modular, tapi juga lebih sulit diikuti karena harus lompat-lompat antar function.

Dan engineer menghabiskan 2 hari untuk refactor ini, padahal ada bug critical yang perlu di-fix. Trade-off-nya tidak sepadan.

---

## Dampak Teknis & Non-Teknis

Memaksa clean code ideal punya dampak yang jelas:

**Dampak teknis:**

- Waktu development jadi lebih lama karena fokus pada "perfection" bukan "getting things done"
- Over-engineering untuk kasus yang simple
- Refactoring yang tidak perlu menambah risiko bug

**Dampak non-teknis:**

- Engineer frustrasi karena merasa kode tidak "cukup clean"
- Diskusi teknis jadi fokus pada "apakah ini clean?" bukan "apakah ini menyelesaikan masalah?"
- Deadline terlewat karena waktu dihabiskan untuk refactoring yang tidak perlu

---

## Pendekatan Praktis

Clean code itu penting, tapi harus dengan konteks yang tepat. Beberapa pendekatan yang bisa dipakai:

**1. Clean code itu relative, bukan absolute**

Tidak ada definisi "clean" yang universal. Yang penting adalah: **apakah kode mudah dipahami dan di-maintain di konteksnya?**

Function 50 baris yang straightforward bisa lebih "clean" daripada 10 function kecil yang sulit diikuti. Variable name singkat yang jelas di konteksnya bisa lebih "clean" daripada variable name panjang yang verbose.

**2. Fokus pada maintainability, bukan perfection**

Tujuan clean code adalah membuat kode lebih mudah di-maintain. Kalau refactoring untuk "clean code" malah membuat kode lebih sulit di-maintain, jangan lakukan.

Contoh: Memecah function jadi banyak function kecil memang "lebih clean", tapi kalau function kecil itu hanya dipakai sekali dan tidak reusable, lebih baik biarkan di function original.

**3. Prioritaskan berdasarkan impact**

Tidak semua kode perlu "perfect". Prioritaskan:
- Kode yang sering diubah → butuh lebih "clean"
- Kode yang dipakai banyak orang → butuh lebih "clean"
- Kode yang critical untuk bisnis → butuh lebih "clean"

Kode yang jarang diubah, hanya dipakai internal, dan tidak critical bisa "kurang clean" asalkan masih bisa dipahami.

**4. Incremental improvement, bukan big bang refactoring**

Jangan coba refactor semua kode jadi "clean" sekaligus. Lakukan incremental:
- Ketika mengubah kode, perbaiki yang bisa diperbaiki dengan mudah
- Ketika ada waktu, refactor bagian yang benar-benar bermasalah
- Fokus pada area yang sering diubah

**5. Trade-off itu normal**

Di dunia kerja, trade-off itu normal. Kadang harus pilih antara:
- Kode yang "perfect" tapi telat
- Kode yang "cukup baik" tapi on time

Pilih yang sesuai dengan konteks. Kalau deadline critical, "cukup baik" lebih penting. Kalau ada waktu, baru perbaiki jadi "perfect".

---

## Trade-off yang Harus Diterima

Menerapkan clean code punya trade-off:

**Kelemahan:**

- Butuh waktu lebih lama untuk development
- Bisa jadi over-engineering untuk kasus yang simple
- Tidak semua kode bisa jadi "perfect" karena constraint

**Keuntungan:**

- Kode lebih mudah di-maintain
- Bug lebih mudah di-debug
- Onboarding lebih cepat

Trade-off ini sepadan ketika clean code benar-benar meningkatkan maintainability. Tapi tidak sepadan ketika clean code hanya memenuhi "checklist" tanpa meningkatkan maintainability.

---

## Contoh Praktis: Kapan Clean Code Penting

Berikut beberapa contoh kapan clean code penting, dan kapan tidak:

**1. Kode yang sering diubah → Butuh lebih clean**

```javascript
// ❌ Sulit diubah karena logic tersebar
function calculatePrice(items, customer) {
  let total = 0;
  for (let i = 0; i < items.length; i++) {
    total += items[i].price * items[i].qty;
  }
  if (customer.startsWith('VIP')) total *= 0.9;
  if (total > 1000) total *= 0.95;
  return total;
}

// ✅ Lebih mudah diubah karena logic terpisah
function calculatePrice(items, customer) {
  const subtotal = calculateSubtotal(items);
  const customerDiscount = getCustomerDiscount(customer, subtotal);
  const bulkDiscount = getBulkDiscount(subtotal);
  return subtotal - customerDiscount - bulkDiscount;
}
```

**2. Kode yang jarang diubah → "Cukup baik" sudah cukup**

```javascript
// ✅ Simple dan jelas, tidak perlu di-refactor
function formatDate(date) {
  const d = new Date(date);
  return `${d.getDate()}/${d.getMonth() + 1}/${d.getFullYear()}`;
}
```

Tidak perlu di-refactor jadi:
```javascript
// ❌ Over-engineered untuk kasus simple
const DAY_INDEX = 0;
const MONTH_INDEX = 1;
const YEAR_INDEX = 2;

function formatDate(date) {
  const dateObject = createDateObject(date);
  const day = extractDay(dateObject);
  const month = extractMonth(dateObject);
  const year = extractYear(dateObject);
  return combineDateParts(day, month, year);
}
```

**3. Magic number yang penting → Extract ke constant**

```javascript
// ❌ Magic number yang tidak jelas
if (user.age < 18) {
  throw new Error('Must be 18 or older');
}

// ✅ Lebih jelas
const MINIMUM_AGE = 18;
if (user.age < MINIMUM_AGE) {
  throw new Error('Must be 18 or older');
}
```

**4. Magic number yang jelas di konteksnya → Tidak perlu extract**

```javascript
// ✅ Sudah jelas di konteksnya
function calculateDaysBetween(startDate, endDate) {
  const diff = endDate - startDate;
  return diff / (1000 * 60 * 60 * 24); // milliseconds to days
}

// ❌ Over-abstracted
const MILLISECONDS_PER_SECOND = 1000;
const SECONDS_PER_MINUTE = 60;
const MINUTES_PER_HOUR = 60;
const HOURS_PER_DAY = 24;
const MILLISECONDS_PER_DAY = MILLISECONDS_PER_SECOND * SECONDS_PER_MINUTE * MINUTES_PER_HOUR * HOURS_PER_DAY;

function calculateDaysBetween(startDate, endDate) {
  const diff = endDate - startDate;
  return diff / MILLISECONDS_PER_DAY;
}
```

---

## Penutup

Clean code itu ideal. Tapi dunia kerja tidak selalu ideal. Ada constraint waktu, legacy code, dan trade-off yang harus dibuat.

Jangan frustrasi kalau kode di production tidak "perfect" seperti di buku. Yang penting adalah: **apakah kode mudah dipahami dan di-maintain di konteksnya?**

Fokus pada maintainability, bukan perfection. Prioritaskan berdasarkan impact. Dan terima bahwa trade-off itu normal.

Clean code itu tool untuk membuat kode lebih mudah di-maintain. Tapi seperti tool lainnya, ia harus dipakai dengan bijak. Jangan paksa pakai clean code ideal kalau trade-off-nya tidak sepadan.

Ingat: kode yang "cukup baik" dan on time, lebih baik daripada kode yang "perfect" tapi telat. Dan kode yang mudah dipahami di konteksnya, lebih baik daripada kode yang memenuhi checklist clean code tapi sulit dipahami.

Clean code itu penting. Tapi yang lebih penting adalah **menyelesaikan masalah dengan cara yang tepat di konteks yang ada**—bukan memaksa mengikuti ideal yang tidak realistis.
