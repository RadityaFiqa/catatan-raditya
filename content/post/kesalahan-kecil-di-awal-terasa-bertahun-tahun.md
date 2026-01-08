---
title: "Kesalahan Kecil di Awal yang Terasa Bertahun-Tahun Kemudian"
date: 2025-11-05T10:00:00+07:00
description: "Refleksi tentang bagaimana keputusan kecil di awal development bisa berdampak besar bertahun-tahun kemudian."
tags: ["engineering", "architecture", "technical-debt", "software-engineer"]
categories: ["Engineering Journey"]
draft: false
---

## Masalah yang Sering Terjadi

"Tidak apa-apa, ini cuma temporary."

"Nanti kita perbaiki."

"Untuk sekarang, ini sudah cukup."

Kata-kata seperti ini sering muncul di awal development. Dan keputusan kecil yang terlihat tidak penting di awal, ternyata bisa berdampak besar bertahun-tahun kemudian.

Masalahnya: **kesalahan kecil di awal development itu seperti benih. Ketika tidak diperbaiki, benih itu tumbuh menjadi masalah besar yang sulit diatasi**.

Saya pernah melihat sistem yang punya masalah yang sebenarnya bisa dihindari di awal—misalnya menggunakan string untuk ID padahal seharusnya UUID, atau tidak ada migration system untuk database. Masalah-masalah kecil ini terlihat tidak penting di awal, tapi bertahun-tahun kemudian, masalah ini jadi sangat sulit diperbaiki.

---

## Kenapa Ini Sering Terjadi

Ada beberapa alasan kenapa kesalahan kecil di awal sering diabaikan:

**1. Terlihat tidak penting**

Kesalahan kecil terlihat tidak penting di awal. "Ini cuma temporary", "nanti kita perbaiki", adalah alasan yang sering muncul. Tapi "nanti" itu tidak pernah datang.

**2. Pressure untuk cepat selesai**

Di awal development, pressure untuk cepat selesai sering membuat kita mengambil shortcut. "Untuk sekarang, ini sudah cukup" adalah alasan yang sering muncul.

**3. Kurangnya pemahaman tentang dampak jangka panjang**

Banyak yang tidak menyadari bahwa keputusan kecil di awal bisa berdampak besar di masa depan. Mereka fokus pada "menyelesaikan sekarang", bukan "menyelesaikan dengan benar".

**4. Tidak ada mekanisme untuk memperbaiki**

Banyak tim tidak punya mekanisme untuk memperbaiki kesalahan kecil. Mereka mengira "nanti kita perbaiki", tapi tidak ada waktu atau prioritas untuk memperbaikinya.

---

## Contoh Kasus Nyata

Bayangkan skenario seperti ini: Di awal development, tim memutuskan untuk menggunakan integer auto-increment untuk ID.

**Keputusan di awal:**

```javascript
// ❌ Menggunakan integer auto-increment untuk ID
const user = await db.users.insert({
  name: 'John Doe',
  email: 'john@example.com'
  // ID otomatis: 1, 2, 3, ...
});

// Terlihat sederhana dan tidak masalah
```

**Masalah yang muncul bertahun-tahun kemudian:**

1. **Security issue**: ID bisa di-guess, membuat sistem rentan terhadap enumeration attack
2. **Scalability issue**: Ketika sistem perlu di-split ke multiple database, ID bisa conflict
3. **Privacy issue**: ID bisa di-guess, membuat data user bisa di-akses oleh orang yang tidak berhak
4. **Migration issue**: Sulit untuk migrate ke UUID karena sudah banyak data dan dependency

```javascript
// Bertahun-tahun kemudian, ingin migrate ke UUID
// Tapi sudah ada:
// - 10 juta records dengan integer ID
// - Banyak service yang depend pada integer ID
// - Banyak external system yang depend pada integer ID
// - Banyak code yang assume ID adalah integer

// Migrate jadi sangat sulit dan berisiko
```

**Yang seharusnya di awal:**

```javascript
// ✅ Menggunakan UUID dari awal
import { v4 as uuidv4 } from 'uuid';

const user = await db.users.insert({
  id: uuidv4(), // UUID dari awal
  name: 'John Doe',
  email: 'john@example.com'
});

// Tidak ada masalah security, scalability, atau privacy
// Dan tidak perlu migrate di masa depan
```

**Contoh lain: Tidak ada migration system**

```javascript
// ❌ Di awal: Database schema diubah manual
// Tidak ada migration file
// Tidak ada versioning
// Tidak ada rollback mechanism

// Bertahun-tahun kemudian:
// - Tidak tahu apa yang berubah
// - Tidak bisa rollback jika ada masalah
// - Tidak bisa apply perubahan ke environment lain dengan konsisten
// - Onboarding sulit karena tidak tahu history perubahan
```

**Yang seharusnya:**

```javascript
// ✅ Migration system dari awal
// migrations/001_create_users_table.js
export async function up(db) {
  await db.query(`
    CREATE TABLE users (
      id UUID PRIMARY KEY,
      name VARCHAR(255),
      email VARCHAR(255) UNIQUE
    )
  `);
}

export async function down(db) {
  await db.query('DROP TABLE users');
}

// Semua perubahan ter-track
// Bisa rollback jika perlu
// Bisa apply ke environment lain dengan konsisten
```

---

## Dampak Teknis & Non-Teknis

Kesalahan kecil di awal yang tidak diperbaiki punya dampak yang jelas:

**Dampak teknis:**

- Masalah jadi semakin sulit diperbaiki seiring waktu
- Technical debt menumpuk
- Sistem jadi tidak scalable atau tidak secure
- Migration jadi sangat sulit dan berisiko

**Dampak non-teknis:**

- Waktu dan effort yang besar untuk memperbaiki masalah yang seharusnya bisa dihindari
- Frustrasi karena masalah seharusnya tidak terjadi
- Trust menurun karena sistem punya masalah yang seharusnya bisa dihindari

---

## Pendekatan Praktis

Mencegah kesalahan kecil di awal butuh pendekatan yang tepat. Beberapa pendekatan yang bisa dipakai:

**1. Pikirkan dampak jangka panjang**

Ketika membuat keputusan, pikirkan dampak jangka panjang. Apakah keputusan ini akan jadi masalah di masa depan?

**2. Jangan mengambil shortcut tanpa rencana**

Shortcut itu boleh, tapi harus ada rencana untuk memperbaikinya. Jangan mengambil shortcut tanpa tahu kapan akan diperbaiki.

**3. Setup best practice dari awal**

Setup best practice dari awal—migration system, proper ID generation, error handling, dll. Lebih mudah setup dari awal daripada memperbaiki nanti.

**4. Code review yang fokus pada long-term impact**

Code review tidak hanya fokus pada "apakah code benar", tapi juga "apakah keputusan ini akan jadi masalah di masa depan".

**5. Refactor secara berkala**

Jangan menunggu masalah jadi besar. Refactor secara berkala untuk memperbaiki kesalahan kecil sebelum jadi masalah besar.

---

## Trade-off yang Harus Diterima

Mencegah kesalahan kecil di awal punya trade-off:

**Keuntungan:**

- Tidak ada masalah yang menumpuk
- Sistem lebih maintainable
- Tidak perlu effort besar untuk memperbaiki masalah

**Kelemahan:**

- Butuh waktu lebih lama di awal
- Bisa jadi over-engineering jika terlalu paranoid
- Tidak semua keputusan bisa diprediksi dampaknya

Trade-off ini sepadan. Lebih baik invest waktu di awal daripada menghabiskan waktu bertahun-tahun kemudian untuk memperbaiki masalah yang seharusnya bisa dihindari.

---

## Contoh Praktis: Checklist untuk Mencegah Kesalahan Kecil

Berikut checklist untuk mencegah kesalahan kecil di awal:

**1. ID Generation**

```javascript
// ❌ Jangan pakai integer auto-increment
// ✅ Pakai UUID atau proper ID generation
const id = uuidv4();
```

**2. Migration System**

```javascript
// ❌ Jangan ubah database manual
// ✅ Pakai migration system
// migrations/001_create_table.js
```

**3. Error Handling**

```javascript
// ❌ Jangan ignore error
// ✅ Handle error dengan proper
try {
  await operation();
} catch (error) {
  logger.error('Operation failed', { error });
  throw error;
}
```

**4. Validation**

```javascript
// ❌ Jangan trust input
// ✅ Validate semua input
const schema = z.object({
  email: z.string().email(),
  name: z.string().min(1)
});
const data = schema.parse(req.body);
```

**5. Logging**

```javascript
// ❌ Jangan hanya console.log
// ✅ Pakai structured logging
logger.info('User created', {
  userId: user.id,
  email: user.email,
  timestamp: new Date().toISOString()
});
```

**6. Testing**

```javascript
// ❌ Jangan skip test untuk "simple" code
// ✅ Test semua code, terutama yang critical
test('should create user with valid data', async () => {
  const user = await createUser({ email: 'test@example.com', name: 'Test' });
  expect(user.id).toBeDefined();
  expect(user.email).toBe('test@example.com');
});
```

---

## Penutup

Kesalahan kecil di awal development itu seperti benih. Ketika tidak diperbaiki, benih itu tumbuh menjadi masalah besar yang sulit diatasi.

Yang penting adalah mencegah kesalahan kecil di awal, bukan memperbaikinya bertahun-tahun kemudian. Pikirkan dampak jangka panjang, jangan mengambil shortcut tanpa rencana, dan setup best practice dari awal.

Jadi, ketika membuat keputusan di awal development:
- Pikirkan dampak jangka panjang
- Jangan mengambil shortcut tanpa rencana
- Setup best practice dari awal
- Code review yang fokus pada long-term impact
- Refactor secara berkala

Dan yang paling penting: **kesalahan kecil itu tidak akan hilang dengan sendirinya**. Jika tidak diperbaiki, kesalahan kecil akan jadi masalah besar. Dan memperbaiki masalah besar itu jauh lebih sulit daripada mencegah kesalahan kecil di awal.

Ingat: waktu yang dihabiskan untuk mencegah kesalahan kecil di awal akan terbayar bertahun-tahun kemudian. Dan waktu yang dihabiskan untuk memperbaiki masalah besar itu jauh lebih mahal daripada mencegah kesalahan kecil.

Kesalahan kecil di awal itu seperti lubang kecil di dinding. Jika tidak diperbaiki, lubang itu akan membesar, dan memperbaikinya jadi jauh lebih sulit.
