---
title: "Arsitektur yang Terlihat Rapi Tapi Sulit Dikembangkan"
date: 2025-10-21T10:00:00+07:00
description: "Refleksi tentang bagaimana arsitektur yang terlihat rapi di diagram bisa jadi sulit dikembangkan di praktiknya."
tags: ["engineering", "architecture", "design", "software-engineer"]
categories: ["Engineering Journey"]
draft: false
---

## Masalah yang Sering Terjadi

"Arsitektur ini terlihat rapi di diagram."

"Semua service terpisah dengan jelas, layer-nya rapi, dependency-nya jelas."

Tapi ketika mulai development, ternyata:
- Menambah fitur baru butuh perubahan di 5 service sekaligus
- Testing jadi sulit karena banyak dependency
- Development jadi lambat karena harus sync dengan banyak service

Masalahnya: **arsitektur yang terlihat rapi di diagram belum tentu mudah dikembangkan. Terkadang, arsitektur yang "sempurna" justru membuat development jadi lebih sulit**.

Saya pernah melihat arsitektur yang terlihat sangat rapi di diagram—semua service terpisah dengan jelas, layer-nya rapi, dependency-nya jelas. Tapi ketika mulai development, ternyata menambah fitur sederhana butuh perubahan di banyak tempat, dan development jadi sangat lambat.

---

## Kenapa Ini Sering Terjadi

Ada beberapa alasan kenapa arsitektur yang terlihat rapi bisa jadi sulit dikembangkan:

**1. Over-engineering**

Banyak yang mengira arsitektur yang "sempurna" harus punya banyak layer, banyak service, banyak abstraction. Tapi abstraction yang berlebihan justru membuat development jadi lebih sulit.

**2. Fokus pada struktur, bukan pada development experience**

Arsitektur dirancang untuk terlihat rapi, bukan untuk memudahkan development. Developer harus mengikuti struktur yang kompleks, meski fitur yang dikembangkan sederhana.

**3. Kurangnya pertimbangan tentang trade-off**

Banyak yang tidak mempertimbangkan trade-off antara "rapi di diagram" dan "mudah dikembangkan". Arsitektur yang rapi di diagram belum tentu mudah dikembangkan.

**4. Tidak ada feedback dari developer**

Arsitektur dirancang tanpa feedback dari developer yang akan menggunakannya. Hasilnya, arsitektur terlihat bagus di kertas, tapi sulit digunakan di praktiknya.

---

## Contoh Kasus Nyata

Bayangkan skenario seperti ini: Arsitektur dirancang dengan banyak layer dan service yang terpisah dengan jelas.

**Arsitektur yang terlihat rapi:**

```
┌─────────────┐
│   API Layer │
└──────┬──────┘
       │
┌──────▼──────┐
│ Service Layer│
└──────┬──────┘
       │
┌──────▼──────┐
│ Domain Layer │
└──────┬──────┘
       │
┌──────▼──────┐
│  Data Layer │
└─────────────┘
```

Terlihat rapi. Tapi ketika menambah fitur baru:

```javascript
// ❌ Arsitektur yang terlalu berlapis
// Untuk menambah fitur sederhana, harus ubah banyak layer

// 1. API Layer
app.post('/api/users', async (req, res) => {
  const result = await userService.create(req.body);
  res.json(result);
});

// 2. Service Layer
class UserService {
  async create(data) {
    // Validasi
    const validated = await this.validator.validate(data);
    
    // Business logic
    const user = await this.domainService.create(validated);
    
    // Save
    await this.repository.save(user);
    
    // Event
    await this.eventBus.publish('user.created', user);
    
    return user;
  }
}

// 3. Domain Layer
class UserDomainService {
  async create(data) {
    // Business rules
    const user = new User(data);
    user.validate();
    return user;
  }
}

// 4. Data Layer
class UserRepository {
  async save(user) {
    return await this.db.users.insert(user);
  }
}
```

Untuk menambah fitur sederhana (misalnya validasi email), harus ubah:
- API Layer (jika perlu error handling khusus)
- Service Layer (untuk validasi)
- Domain Layer (untuk business rule)
- Data Layer (jika perlu constraint database)

**Yang lebih sederhana tapi tetap rapi:**

```javascript
// ✅ Arsitektur yang lebih sederhana
// Fitur sederhana bisa ditambah dengan mudah

// routes/users.js
app.post('/api/users', async (req, res) => {
  try {
    // Validasi
    const schema = z.object({
      email: z.string().email(),
      name: z.string().min(1)
    });
    const data = schema.parse(req.body);
    
    // Business logic (simple, langsung di route)
    const existingUser = await db.users.findOne({ email: data.email });
    if (existingUser) {
      return res.status(400).json({ error: 'Email already exists' });
    }
    
    // Save
    const user = await db.users.insert({
      ...data,
      createdAt: new Date()
    });
    
    // Event (optional, hanya jika perlu)
    await eventBus.publish('user.created', user);
    
    res.json(user);
  } catch (error) {
    if (error instanceof z.ZodError) {
      res.status(400).json({ error: 'Validation failed', details: error.errors });
    } else {
      res.status(500).json({ error: 'Internal server error' });
    }
  }
});
```

Untuk fitur sederhana, tidak perlu banyak layer. Tapi ketika sistem berkembang, baru tambahkan layer yang diperlukan.

---

## Dampak Teknis & Non-Teknis

Arsitektur yang terlalu kompleks punya dampak yang jelas:

**Dampak teknis:**

- Development jadi lambat karena harus mengikuti banyak layer
- Testing jadi sulit karena banyak dependency
- Refactoring jadi sulit karena perubahan di satu layer bisa mempengaruhi layer lain

**Dampak non-teknis:**

- Developer frustrasi karena harus mengikuti struktur yang kompleks
- Onboarding lebih sulit karena harus memahami banyak layer
- Innovation terhambat karena perubahan butuh effort yang besar

---

## Pendekatan Praktis

Arsitektur yang baik itu seimbang antara "rapi" dan "mudah dikembangkan". Beberapa pendekatan yang bisa dipakai:

**1. Mulai sederhana, tambah kompleksitas ketika diperlukan**

Jangan langsung membuat arsitektur yang kompleks. Mulai dengan yang sederhana, lalu tambahkan kompleksitas ketika memang diperlukan.

**2. Fokus pada development experience**

Arsitektur harus memudahkan development, bukan hanya terlihat rapi. Pertimbangkan: apakah developer bisa menambah fitur dengan mudah?

**3. Pertimbangkan trade-off**

Setiap layer dan abstraction punya trade-off. Tambahkan layer hanya ketika manfaatnya lebih besar daripada kompleksitas yang ditambahkan.

**4. Dapatkan feedback dari developer**

Libatkan developer dalam desain arsitektur. Mereka yang akan menggunakan arsitektur, jadi feedback mereka penting.

**5. Refactor ketika diperlukan**

Tidak perlu membuat arsitektur yang "sempurna" sejak awal. Refactor ketika diperlukan, bukan sebelum diperlukan.

---

## Trade-off yang Harus Diterima

Arsitektur yang sederhana vs kompleks punya trade-off:

**Arsitektur sederhana:**

- Keuntungan: Development cepat, mudah dipahami, mudah di-test
- Kelemahan: Bisa jadi tidak scalable untuk sistem yang sangat besar

**Arsitektur kompleks:**

- Keuntungan: Lebih terstruktur, bisa handle sistem yang sangat besar
- Kelemahan: Development lambat, sulit dipahami, sulit di-test

Trade-off ini sepadan ketika kompleksitas sesuai dengan kebutuhan. Tapi tidak sepadan ketika kompleksitas ditambahkan sebelum diperlukan.

---

## Contoh Praktis: Evolusi Arsitektur

Berikut contoh bagaimana arsitektur bisa berevolusi:

**Fase 1: Sederhana (MVP)**

```javascript
// routes/users.js
app.post('/api/users', async (req, res) => {
  const user = await db.users.insert(req.body);
  res.json(user);
});
```

**Fase 2: Tambah validasi (ketika diperlukan)**

```javascript
// routes/users.js
app.post('/api/users', async (req, res) => {
  const schema = z.object({
    email: z.string().email(),
    name: z.string().min(1)
  });
  const data = schema.parse(req.body);
  
  const user = await db.users.insert(data);
  res.json(user);
});
```

**Fase 3: Tambah service layer (ketika business logic kompleks)**

```javascript
// services/userService.js
export class UserService {
  async createUser(data) {
    // Business logic yang kompleks
    const user = await this.validateAndCreate(data);
    await this.sendWelcomeEmail(user);
    return user;
  }
}

// routes/users.js
app.post('/api/users', async (req, res) => {
  const user = await userService.createUser(req.body);
  res.json(user);
});
```

**Fase 4: Tambah domain layer (ketika domain logic sangat kompleks)**

```javascript
// domain/User.js
export class User {
  constructor(data) {
    this.email = data.email;
    this.name = data.name;
  }
  
  validate() {
    // Complex business rules
  }
}

// services/userService.js
export class UserService {
  async createUser(data) {
    const user = new User(data);
    user.validate();
    return await this.repository.save(user);
  }
}
```

Evolusi ini terjadi ketika diperlukan, bukan sejak awal.

---

## Penutup

Arsitektur yang terlihat rapi di diagram belum tentu mudah dikembangkan. Terkadang, arsitektur yang "sempurna" justru membuat development jadi lebih sulit.

Yang penting adalah keseimbangan—arsitektur yang cukup rapi untuk maintainable, tapi cukup sederhana untuk mudah dikembangkan. Mulai dengan yang sederhana, lalu tambahkan kompleksitas ketika memang diperlukan.

Jadi, ketika merancang arsitektur:
- Mulai sederhana, tambah kompleksitas ketika diperlukan
- Fokus pada development experience
- Pertimbangkan trade-off
- Dapatkan feedback dari developer
- Refactor ketika diperlukan

Dan yang paling penting: **arsitektur itu untuk memudahkan development, bukan untuk terlihat rapi di diagram**. Arsitektur yang rapi tapi sulit dikembangkan itu tidak berguna.

Ingat: arsitektur yang baik itu evolutif. Bisa berubah seiring waktu, bukan harus "sempurna" sejak awal. Dan perubahan itu normal—yang penting adalah perubahan itu dilakukan dengan sadar, bukan karena terpaksa.

Arsitektur yang terlihat rapi tapi sulit dikembangkan itu seperti rumah yang indah tapi tidak bisa ditinggali. Terlihat bagus, tapi tidak fungsional.
