---
title: "Kapan Shared Library Membantu, Kapan Justru Memperlambat Tim"
date: 2025-09-06T10:00:00+07:00
description: "Refleksi tentang trade-off shared library: kapan membantu tim berkembang, dan kapan justru memperlambat karena terlalu strict dan tidak fleksibel."
tags: ["engineering", "architecture", "shared-library", "microservices", "software-engineer"]
categories: ["Engineering Journey"]
draft: true
---

## Masalah yang Sering Terjadi

"Semua service harus menggunakan shared library ini."

"Tidak boleh pakai library lain, harus pakai yang sudah disediakan."

Aturan seperti ini sering muncul di organisasi yang ingin standarisasi. Tujuannya baik—memastikan semua service konsisten, mengurangi duplikasi code, dan memudahkan maintenance.

Tapi di praktiknya, shared library yang terlalu strict justru memperlambat tim. Service yang butuh fleksibilitas tidak bisa berkembang. Fitur yang seharusnya sederhana jadi kompleks karena harus mengikuti constraint shared library.

Masalahnya: **shared library itu tool, bukan tujuan. Ketika shared library jadi constraint yang membatasi, bukan enabler yang membantu, itu sudah jadi masalah**.

Di kantor saya saat ini, semua development harus menggunakan shared library/framework kantor yang terlalu strict. Contoh konkret: HTTP client library yang memaksa semua request menggunakan format tertentu, database ORM yang tidak bisa di-customize untuk use case khusus, atau validation library yang terlalu opinionated sehingga tidak fleksibel untuk kebutuhan spesifik.

---

## Kenapa Ini Sering Terjadi

Ada beberapa alasan kenapa shared library sering jadi terlalu strict:

**1. Keinginan untuk standarisasi**

Banyak organisasi ingin semua service konsisten. Tapi standarisasi yang berlebihan justru menghilangkan fleksibilitas yang dibutuhkan.

**2. Ketakutan akan duplikasi code**

Duplikasi code memang buruk. Tapi menghindari duplikasi dengan memaksa semua service pakai shared library yang sama juga bisa jadi masalah—terutama ketika service punya kebutuhan yang berbeda.

**3. Kurangnya pemahaman tentang trade-off**

Banyak yang tidak menyadari bahwa shared library punya trade-off. Shared library membantu mengurangi duplikasi, tapi juga mengurangi fleksibilitas. Dan ketika fleksibilitas hilang, development jadi lebih lambat.

**4. Tidak ada mekanisme untuk exception**

Ketika shared library tidak cocok untuk use case tertentu, tidak ada mekanisme untuk exception. Semua service dipaksa pakai shared library, meski tidak cocok.

---

## Contoh Kasus Nyata

Di kantor saya, ada beberapa masalah dengan shared library yang membuat saya belajar tentang trade-off shared library.

**Skenario 1: HTTP Client Library yang Terlalu Opinionated**

Shared library untuk HTTP client memaksa semua request menggunakan format tertentu:

```javascript
// ❌ Dengan shared library yang strict
import { apiClient } from '@company/shared-http-client';

// Dipaksa pakai format tertentu, tidak bisa customize
const response = await apiClient.post('/api/users', {
  data: userData,
  // Harus pakai format yang sudah ditentukan
  // Tidak bisa set custom headers dengan mudah
  // Tidak bisa customize retry logic
  // Tidak bisa set timeout per-request
});

// Masalahnya:
// - Semua request harus follow format yang sama
// - Tidak bisa customize untuk use case khusus
// - Error handling terbatas
// - Retry logic tidak bisa di-customize
```

**Yang seharusnya:**

```javascript
// ✅ Dengan HTTP client yang fleksibel (axios, fetch, dll)
import axios from 'axios';

// Bisa customize sesuai kebutuhan
const response = await axios.post('/api/users', userData, {
  headers: {
    'X-Custom-Header': 'value',
    'Authorization': `Bearer ${token}`
  },
  timeout: 5000, // Custom timeout
  retry: {
    retries: 3,
    retryDelay: (retryCount) => retryCount * 1000
  }
});

// Atau untuk use case sederhana, pakai fetch native
const response = await fetch('/api/users', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify(userData)
});
```

**Skenario 2: Database ORM yang Tidak Bisa Di-customize**

Shared library untuk database memaksa semua query menggunakan ORM tertentu:

```javascript
// ❌ Dengan shared library ORM yang strict
import { db } from '@company/shared-db-library';

// Dipaksa pakai ORM, tidak bisa raw query dengan mudah
const users = await db.User.findAll({
  where: { status: 'active' },
  include: [db.Profile] // Harus pakai include, tidak bisa JOIN manual
});

// Masalahnya:
// - Complex query jadi sulit
// - Performance optimization terbatas
// - Tidak bisa pakai database-specific features
// - N+1 query problem sulit dihindari
```

**Yang seharusnya:**

```javascript
// ✅ Bisa pakai raw query untuk use case kompleks
import { db } from './db'; // Database connection biasa

// Untuk query kompleks, pakai raw SQL
const users = await db.query(`
  SELECT u.*, p.* 
  FROM users u
  LEFT JOIN profiles p ON u.id = p.user_id
  WHERE u.status = 'active'
  AND u.created_at > ?
`, [startDate]);

// Untuk query sederhana, bisa pakai query builder atau ORM
// Tapi tidak dipaksa untuk semua use case
```

**Skenario 3: Validation Library yang Terlalu Opinionated**

Shared library untuk validation memaksa semua validation menggunakan cara tertentu:

```javascript
// ❌ Dengan shared library validation yang strict
import { validate } from '@company/shared-validation';

// Dipaksa pakai format tertentu
const result = validate(userData, {
  name: 'required|string|min:3',
  email: 'required|email',
  // Format terbatas, tidak bisa custom validation logic
  // Tidak bisa async validation
  // Error message tidak bisa di-customize dengan mudah
});

// Masalahnya:
// - Custom validation logic sulit
// - Async validation tidak didukung
// - Error message format terbatas
// - Tidak bisa integrate dengan business logic
```

**Yang seharusnya:**

```javascript
// ✅ Dengan validation library yang fleksibel (ZOD, Joi, dll)
import { z } from 'zod';

// Bisa custom validation logic
const userSchema = z.object({
  name: z.string().min(3),
  email: z.email(),
  age: z.number().refine((age) => age >= 18, {
    message: "Must be 18 or older"
  }),
  // Bisa async validation
  username: z.string().refine(async (username) => {
    const exists = await checkUsernameExists(username);
    return !exists;
  }, {
    message: "Username already exists"
  })
});

// Error message bisa di-customize
const result = userSchema.safeParse(userData);
```

Shared library yang strict membuat use case sederhana jadi kompleks, dan menghilangkan fleksibilitas yang dibutuhkan untuk optimasi dan customization.

---

## Dampak Teknis & Non-Teknis

Shared library yang terlalu strict punya dampak yang jelas:

**Dampak teknis:**

- Service tidak bisa independent karena harus bergantung pada shared library
- Development jadi lebih lambat karena harus mengikuti constraint shared library
- Error handling tidak fleksibel
- Setup jadi lebih rumit untuk use case yang sederhana

**Dampak non-teknis:**

- Tim frustrasi karena tidak bisa mengembangkan fitur dengan cepat
- Innovation terhambat karena semua harus mengikuti shared library
- Onboarding lebih sulit karena harus belajar shared library yang kompleks

---

## Pendekatan Praktis

Shared library itu membantu, tapi hanya ketika digunakan dengan benar. Beberapa pendekatan yang bisa dipakai:

**1. Shared library untuk common functionality**

Gunakan shared library untuk functionality yang benar-benar common dan tidak berubah:
- Authentication/authorization
- Logging
- Error handling (basic)
- Database connection (basic)

Jangan gunakan shared library untuk functionality yang spesifik atau sering berubah.

**2. Buat shared library yang fleksibel**

Shared library harus fleksibel, bukan strict. Berikan opsi untuk customize, bukan memaksa semua service pakai cara yang sama.

```javascript
// ✅ Shared library yang fleksibel
export function createLogger(config) {
  return winston.createLogger({
    level: config.level || 'info',
    format: config.format || winston.format.json(),
    transports: config.transports || [new winston.transports.Console()],
    ...config.customOptions // Bisa customize
  });
}

// ❌ Shared library yang strict
export const logger = winston.createLogger({
  level: 'info', // Hard-coded
  format: winston.format.json(), // Hard-coded
  transports: [new winston.transports.Console()] // Hard-coded
});
```

**3. Izinkan exception untuk use case khusus**

Ketika shared library tidak cocok untuk use case tertentu, izinkan exception. Jangan memaksa semua service pakai shared library.

**4. Versioning yang jelas**

Shared library harus punya versioning yang jelas. Service bisa upgrade secara bertahap, bukan dipaksa upgrade semua sekaligus.

**5. Dokumentasi yang jelas**

Shared library harus punya dokumentasi yang jelas tentang:
- Kapan harus digunakan
- Kapan tidak harus digunakan
- Bagaimana cara customize
- Trade-off yang ada

---

## Trade-off yang Harus Diterima

Shared library punya trade-off:

**Keuntungan:**

- Mengurangi duplikasi code
- Memastikan konsistensi
- Memudahkan maintenance (fix sekali, semua service dapat update)

**Kelemahan:**

- Mengurangi fleksibilitas
- Bisa memperlambat development jika terlalu strict
- Bisa jadi single point of failure jika tidak didesain dengan baik

Trade-off ini sepadan ketika shared library digunakan dengan benar. Tapi tidak sepadan ketika shared library jadi constraint yang membatasi.

---

## Contoh Praktis: Kapan Shared Library Membantu

Berikut contoh kapan shared library membantu, dan kapan tidak:

**Shared library yang membantu:**

```javascript
// ✅ Authentication - common, tidak berubah
import { authenticate } from '@company/auth-library';

app.use(authenticate({
  jwtSecret: process.env.JWT_SECRET,
  excludePaths: ['/health', '/public']
}));

// ✅ Logging - common, bisa customize
import { createLogger } from '@company/logging-library';

const logger = createLogger({
  service: 'upload-service',
  level: 'info',
  transports: [/* custom transports */]
});

// ✅ Database connection - common, bisa customize
import { createDbPool } from '@company/db-library';

const db = createDbPool({
  host: process.env.DB_HOST,
  poolSize: 20,
  // Bisa customize sesuai kebutuhan service
});
```

**Shared library yang tidak membantu:**

```javascript
// ❌ HTTP client yang terlalu opinionated
import { apiClient } from '@company/shared-http-client';

// Dipaksa pakai format tertentu, tidak bisa customize
const result = await apiClient.post('/api/users', {
  // Tidak bisa set custom headers dengan mudah
  // Tidak bisa customize retry logic
  // Tidak bisa set timeout per-request
  // Semua request harus follow format yang sama
});

// ❌ Database ORM yang tidak fleksibel
import { db } from '@company/shared-db-library';

// Dipaksa pakai ORM untuk semua query
const users = await db.User.findAll({
  // Complex query jadi sulit
  // Tidak bisa pakai raw SQL dengan mudah
  // Performance optimization terbatas
});

// ❌ Validation library yang terlalu strict
import { validate } from '@company/shared-validation';

// Format terbatas, tidak bisa custom validation
const result = validate(data, {
  // Tidak bisa async validation
  // Error message tidak bisa di-customize
  // Custom validation logic sulit
});

// ❌ Business logic - terlalu spesifik
import { processOrder } from '@company/order-library';

// Business logic seharusnya di service, bukan di shared library
// Setiap service mungkin punya business logic yang berbeda
```

---

## Penutup

Shared library itu membantu ketika digunakan untuk common functionality yang tidak berubah. Tapi shared library jadi masalah ketika terlalu strict dan menghilangkan fleksibilitas yang dibutuhkan.

Di kantor saya, saya belajar bahwa shared library yang terlalu strict justru memperlambat development. HTTP client yang terlalu opinionated membuat custom request jadi sulit, database ORM yang tidak fleksibel membatasi optimasi query, dan validation library yang strict menghambat custom validation logic.

Jadi, gunakan shared library dengan bijak:
- Untuk common functionality yang tidak berubah
- Yang fleksibel, bukan strict
- Yang izinkan exception untuk use case khusus
- Yang punya versioning dan dokumentasi yang jelas

Dan yang paling penting: **shared library itu tool, bukan tujuan**. Ketika shared library jadi constraint yang membatasi, bukan enabler yang membantu, itu sudah jadi masalah.

Ingat: duplikasi code itu buruk. Tapi menghilangkan fleksibilitas dengan memaksa semua service pakai shared library yang strict juga buruk. Yang penting adalah keseimbangan—shared library untuk yang common, fleksibilitas untuk yang spesifik.

Shared library membantu ketika digunakan dengan benar. Tapi shared library justru memperlambat tim ketika terlalu strict dan tidak fleksibel.
