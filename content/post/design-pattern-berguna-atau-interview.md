---
title: "Design Pattern: Berguna atau Sekadar Bahan Interview?"
date: 2025-03-10T10:00:00+07:00
description: "Refleksi tentang kapan design pattern benar-benar berguna di production, dan kapan hanya jadi bahan interview."
tags: ["engineering", "design-pattern", "architecture", "software-engineer"]
categories: ["Engineering Journey"]
draft: false
---

## Masalah yang Sering Terjadi

"Bisa jelaskan perbedaan Singleton dan Factory pattern?"

Pertanyaan ini sering muncul di interview engineering. Kandidat yang hafal jawabannya langsung menang. Tapi di production, design pattern sering tidak dipakai—atau dipakai dengan cara yang salah.

Masalahnya: **design pattern sering jadi bahan interview, tapi jarang dipahami kapan sebenarnya berguna di production**.

Saya pernah melihat kode yang memaksa menggunakan Singleton pattern padahal tidak perlu. Atau kode yang menggunakan Factory pattern padahal simple object creation sudah cukup. Kenapa? Karena engineer mengira "pakai design pattern = kode lebih baik".

Padahal design pattern itu tool. Seperti tool lainnya, ia berguna ketika dipakai di konteks yang tepat, dan merugikan ketika dipaksa dipakai.

---

## Kenapa Ini Sering Terjadi

Ada beberapa alasan kenapa design pattern sering disalahpahami:

**1. Dijadikan bahan interview**

Banyak interview engineering menanyakan design pattern. Kandidat yang hafal pattern langsung dianggap "paham OOP" atau "paham best practice". Padahal hafal pattern tidak sama dengan tahu kapan memakainya.

**2. Dianggap sebagai "best practice"**

Banyak engineer mengira: kalau pakai design pattern, kode jadi lebih baik. Padahal design pattern itu solusi untuk masalah spesifik. Kalau masalahnya tidak ada, pattern jadi over-engineering.

**3. Kurangnya konteks nyata**

Buku dan tutorial design pattern sering menjelaskan pattern dengan contoh yang sederhana. Engineer yang baru belajar langsung mengira "ini cocok untuk project saya". Padahal di production, konteksnya lebih kompleks.

**4. Pressure untuk "terlihat profesional"**

Beberapa engineer memakai design pattern karena ingin terlihat "profesional" atau "senior". Padahal kode yang simple dan mudah dipahami lebih profesional daripada kode yang memaksa pakai pattern.

---

## Contoh Kasus Nyata

Saya pernah melihat kode yang memaksa menggunakan Singleton pattern:

```javascript
class DatabaseConnection {
  static instance = null;

  static getInstance() {
    if (!this.instance) {
      this.instance = new DatabaseConnection();
    }
    return this.instance;
  }

  private constructor() {
    // Initialize connection
  }
}

// Usage
const db = DatabaseConnection.getInstance();
```

Kode ini dibuat dengan alasan "kita hanya butuh satu koneksi database". Tapi di production, ini jadi masalah:
- Sulit di-test karena instance static
- Sulit di-mock karena constructor private
- Tidak fleksibel kalau nanti butuh multiple connection

Padahal di JavaScript/Node.js, module system sudah memberikan singleton behavior secara natural:

```javascript
// db.js
let connection = null;

function getConnection() {
  if (!connection) {
    connection = createConnection();
  }
  return connection;
}

module.exports = { getConnection };
```

Ini lebih simple, lebih mudah di-test, dan lebih mudah di-maintain. Tapi karena tidak "pakai pattern", beberapa engineer mengira ini kurang "profesional".

Contoh lain: Factory pattern yang dipaksa dipakai untuk object creation yang simple:

```javascript
// Over-engineered
class UserFactory {
  createUser(type, data) {
    if (type === 'admin') {
      return new AdminUser(data);
    } else if (type === 'regular') {
      return new RegularUser(data);
    }
    throw new Error('Invalid user type');
  }
}

// Usage
const factory = new UserFactory();
const user = factory.createUser('admin', userData);
```

Padahal simple function sudah cukup:

```javascript
function createUser(type, data) {
  if (type === 'admin') {
    return new AdminUser(data);
  } else if (type === 'regular') {
    return new RegularUser(data);
  }
  throw new Error('Invalid user type');
}

// Usage
const user = createUser('admin', userData);
```

Factory pattern berguna ketika object creation kompleks atau butuh abstraction. Tapi untuk kasus sederhana seperti ini, simple function lebih tepat.

---

## Dampak Teknis & Non-Teknis

Memaksa pakai design pattern punya dampak yang jelas:

**Dampak teknis:**

- Kode jadi over-engineered dan sulit dipahami
- Test jadi lebih sulit karena pattern menambah complexity
- Refactoring jadi lebih sulit karena pattern menciptakan coupling yang tidak perlu

**Dampak non-teknis:**

- Engineer junior jadi bingung karena kode terlalu kompleks
- Diskusi teknis jadi fokus pada "pattern mana yang dipakai" bukan "masalah apa yang diselesaikan"
- Culture engineering jadi fokus pada "pakai pattern" bukan "selesaikan masalah dengan cara yang tepat"

---

## Pendekatan Praktis

Design pattern itu berguna, tapi hanya ketika dipakai di konteks yang tepat. Beberapa pendekatan yang bisa dipakai:

**1. Pahami masalahnya dulu, baru pilih pattern**

Jangan mulai dari "saya mau pakai pattern X". Mulai dari "masalah apa yang sedang saya hadapi?" Lalu pilih pattern yang cocok—atau tidak pakai pattern sama sekali kalau simple solution sudah cukup.

Contoh: Kalau masalahnya "butuh satu instance untuk seluruh aplikasi", pertimbangkan:
- Apakah module system sudah memberikan ini? (JavaScript/Node.js)
- Apakah dependency injection sudah cukup? (Java/Spring)
- Apakah benar-benar butuh Singleton, atau bisa pakai global variable yang lebih simple?

**2. Pattern itu solusi, bukan tujuan**

Pattern adalah solusi untuk masalah yang sudah terjadi berkali-kali. Kalau masalahnya belum terjadi, jangan paksa pakai pattern. Mulai dengan simple solution, lalu refactor ke pattern kalau memang diperlukan.

**3. Fokus pada intent, bukan implementasi**

Yang penting dari pattern adalah intent-nya (masalah apa yang diselesaikan), bukan implementasinya. Implementasi bisa disesuaikan dengan konteks.

Contoh: Observer pattern intinya adalah "notify multiple objects ketika ada perubahan". Implementasinya bisa:
- Event emitter (Node.js)
- Pub/Sub system (Redis, RabbitMQ)
- Callback function
- Custom observer class

Pilih implementasi yang paling cocok untuk konteks, bukan yang "paling sesuai dengan pattern di buku".

**4. Simple solution lebih baik daripada pattern yang dipaksa**

Kode yang simple dan mudah dipahami lebih baik daripada kode yang memaksa pakai pattern. Pattern itu menambah complexity. Complexity itu justified kalau memang menyelesaikan masalah. Kalau tidak, lebih baik simple.

**5. Pattern muncul secara natural ketika diperlukan**

Ketika kode berkembang, pattern akan muncul secara natural. Refactor ke pattern ketika:
- Ada duplikasi yang tidak bisa dihindari dengan simple refactoring
- Ada complexity yang butuh abstraction
- Ada masalah yang pattern sudah solve dengan baik

Jangan paksa pakai pattern sejak awal. Biarkan pattern muncul ketika memang diperlukan.

---

## Trade-off yang Harus Diterima

Menggunakan design pattern punya trade-off:

**Kelemahan:**

- Menambah complexity
- Butuh waktu lebih lama untuk dipahami engineer baru
- Bisa jadi over-engineering kalau dipaksa

**Keuntungan:**

- Menyelesaikan masalah yang sudah dikenal dengan baik
- Memberikan struktur yang konsisten
- Memudahkan komunikasi (nama pattern menjelaskan intent)

Trade-off ini sepadan ketika pattern benar-benar menyelesaikan masalah. Tapi tidak sepadan ketika pattern dipaksa dipakai tanpa alasan yang jelas.

---

## Contoh Praktis: Kapan Pattern Berguna

Berikut beberapa contoh kapan pattern benar-benar berguna:

**1. Strategy Pattern: Ketika ada multiple algorithms yang bisa dipertukarkan**

```javascript
// Masalah: Butuh multiple cara kalkulasi harga
// Simple solution tidak cukup karena algoritma bisa bertambah

class PriceCalculator {
  constructor(strategy) {
    this.strategy = strategy;
  }

  calculate(items) {
    return this.strategy.calculate(items);
  }
}

class RegularPriceStrategy {
  calculate(items) {
    return items.reduce((sum, item) => sum + item.price, 0);
  }
}

class DiscountPriceStrategy {
  calculate(items) {
    const total = items.reduce((sum, item) => sum + item.price, 0);
    return total * 0.9; // 10% discount
  }
}

// Usage
const calculator = new PriceCalculator(new DiscountPriceStrategy());
const price = calculator.calculate(items);
```

Ini berguna karena:
- Algoritma bisa bertambah tanpa mengubah existing code
- Mudah di-test karena strategy bisa di-mock
- Intent jelas: ada multiple cara kalkulasi

**2. Observer Pattern: Ketika butuh notify multiple objects**

```javascript
// Masalah: Multiple components perlu tahu ketika order status berubah
// Simple callback tidak cukup karena subscriber bisa bertambah

class Order {
  constructor() {
    this.observers = [];
    this.status = 'pending';
  }

  subscribe(observer) {
    this.observers.push(observer);
  }

  setStatus(status) {
    this.status = status;
    this.observers.forEach(observer => observer.update(this));
  }
}

// Usage
const order = new Order();
order.subscribe(new EmailNotifier());
order.subscribe(new SMSNotifier());
order.subscribe(new AnalyticsTracker());
order.setStatus('completed');
```

Ini berguna karena:
- Subscriber bisa bertambah tanpa mengubah Order class
- Loose coupling antara Order dan notifier
- Intent jelas: ada multiple objects yang perlu di-notify

**Yang tidak berguna: Memaksa pakai pattern untuk kasus sederhana**

```javascript
// ❌ Over-engineered: Simple function sudah cukup
class UserFactory {
  createUser(data) {
    return new User(data);
  }
}

// ✅ Simple dan jelas
function createUser(data) {
  return new User(data);
}
```

---

## Penutup

Design pattern itu berguna ketika dipakai di konteks yang tepat. Tapi ketika dipaksa dipakai tanpa alasan yang jelas, pattern jadi over-engineering yang merugikan.

Jangan mulai dari "saya mau pakai pattern X". Mulai dari **"masalah apa yang sedang saya hadapi?"** Lalu pilih solusi yang tepat—bisa pattern, bisa simple solution.

Pattern itu tool. Seperti tool lainnya, ia berguna ketika dipakai untuk menyelesaikan masalah yang tepat. Tapi ketika dipaksa dipakai, ia jadi beban yang tidak perlu.

Jadi, pahami masalahnya dulu. Pilih solusi yang tepat. Dan jangan takut pakai simple solution kalau memang sudah cukup. Kode yang simple dan mudah dipahami lebih profesional daripada kode yang memaksa pakai pattern.

Ingat: pattern itu muncul secara natural ketika diperlukan. Jangan paksa pakai pattern sejak awal. Biarkan pattern muncul ketika kode berkembang dan masalahnya menjadi jelas.

Design pattern itu bahan interview? Bisa jadi. Tapi yang lebih penting: **pahami kapan pattern benar-benar berguna di production, bukan sekadar hafal definisinya**.
