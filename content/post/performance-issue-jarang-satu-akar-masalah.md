---
title: "Performance Issue Jarang Hanya Satu Akar Masalah"
date: 2025-08-22T10:00:00+07:00
description: "Refleksi tentang bagaimana performance issue di production sering memiliki multiple root causes yang saling terkait—database, query, locking, dan lainnya."
tags: ["engineering", "performance", "database", "optimization", "software-engineer"]
categories: ["Engineering Journey"]
draft: false
---

## Masalah yang Sering Terjadi

"API endpoint ini lambat. Pasti masalahnya di database query."

Pernyataan seperti ini sering muncul ketika ada performance issue. Tapi setelah diinvestigasi, ternyata masalahnya tidak hanya di database query. Ada masalah di connection pool, locking, indexing, dan bahkan di application code.

Masalahnya: **performance issue jarang hanya punya satu akar masalah. Sering kali ada multiple root causes yang saling terkait**.

Saya pernah melihat performance issue yang awalnya terlihat seperti masalah query database. Tapi setelah diinvestigasi lebih dalam, ternyata ada masalah di:
- Query yang tidak optimal
- Connection pool yang terlalu kecil
- Locking yang terjadi karena transaction yang terlalu lama
- Missing index yang menyebabkan full table scan
- N+1 query problem di application code

Semua masalah ini saling terkait. Dan ketika kita hanya fix satu masalah, performance issue tidak benar-benar selesai.

---

## Kenapa Ini Sering Terjadi

Ada beberapa alasan kenapa performance issue sering punya multiple root causes:

**1. Sistem yang kompleks**

Di sistem yang kompleks, komponen saling terkait. Masalah di satu komponen bisa memicu masalah di komponen lain. Query yang lambat bisa menyebabkan connection pool habis, yang kemudian menyebabkan locking, yang kemudian menyebabkan query lain juga lambat.

**2. Asumsi yang terlalu cepat**

Ketika ada performance issue, kita sering langsung berasumsi bahwa masalahnya di satu tempat—misalnya database. Tapi sebenarnya, masalahnya bisa di banyak tempat sekaligus.

**3. Tidak ada observability yang baik**

Tanpa observability yang baik, sulit melihat semua komponen yang terlibat. Kita hanya melihat gejala, bukan akar masalah yang sebenarnya.

**4. Fix yang tidak komprehensif**

Ketika kita fix satu masalah, masalah lain mungkin tidak terlihat karena ter-cover oleh masalah yang lebih besar. Tapi ketika masalah besar sudah di-fix, masalah kecil jadi terlihat.

---

## Contoh Kasus Nyata

Di kantor saya, ada performance issue yang membuat saya belajar bahwa performance issue jarang hanya punya satu akar masalah.

Skenarionya: API endpoint untuk generate report lambat. Awalnya, kita mengira masalahnya di query database yang kompleks. Tapi setelah diinvestigasi, ternyata ada multiple masalah:

**Masalah 1: Query yang tidak optimal**

```sql
-- Query awal: Full table scan
SELECT * FROM orders o
JOIN users u ON o.user_id = u.id
WHERE o.created_at BETWEEN ? AND ?
ORDER BY o.created_at DESC;
```

Query ini melakukan full table scan karena tidak ada index di `created_at`.

**Masalah 2: Missing index**

Tabel `orders` tidak punya index di `created_at`, menyebabkan query harus scan semua row.

**Masalah 3: Connection pool yang terlalu kecil**

Connection pool hanya 10 connection. Ketika ada banyak request, connection pool habis, dan request harus menunggu.

**Masalah 4: Transaction yang terlalu lama**

Transaction dibuka di awal, lalu melakukan banyak operasi, baru commit di akhir. Ini menyebabkan locking yang lama.

**Masalah 5: N+1 query problem**

Di application code, ada loop yang melakukan query untuk setiap item:

```javascript
// ❌ N+1 query problem
const orders = await db.query('SELECT * FROM orders WHERE ...');
for (const order of orders) {
  const user = await db.query('SELECT * FROM users WHERE id = ?', [order.user_id]);
  order.user = user;
}
```

Semua masalah ini saling terkait. Query yang lambat menyebabkan connection pool habis. Connection pool yang habis menyebabkan locking. Locking menyebabkan query lain juga lambat.

Dan ketika kita hanya fix satu masalah—misalnya menambahkan index—masalah lain masih ada. Performance memang membaik, tapi tidak optimal.

---

## Dampak Teknis & Non-Teknis

Hanya fix satu akar masalah punya dampak yang jelas:

**Dampak teknis:**

- Performance issue tidak benar-benar selesai
- Masalah lain muncul setelah masalah besar di-fix
- Optimasi tidak optimal karena tidak menyelesaikan semua masalah

**Dampak non-teknis:**

- Waktu debugging lebih lama karena harus investigasi berkali-kali
- Tim frustrasi karena masalah sepertinya tidak pernah selesai
- User experience tetap buruk meski sudah ada optimasi

---

## Pendekatan Praktis

Ketika ada performance issue, jangan langsung berasumsi bahwa masalahnya hanya di satu tempat. Beberapa pendekatan yang bisa dipakai:

**1. Investigasi secara komprehensif**

Jangan langsung fix masalah pertama yang terlihat. Investigasi semua komponen yang terlibat:
- Application code
- Database query
- Connection pool
- Locking
- Network
- Resource (CPU, memory, disk)

**2. Gunakan observability tools**

Observability tools membantu melihat semua komponen yang terlibat. Gunakan:
- APM (Application Performance Monitoring) untuk melihat slow queries
- Database monitoring untuk melihat connection pool dan locking
- Logging untuk melihat flow request

**3. Identifikasi semua root causes**

Buat list semua masalah yang ditemukan, lalu prioritaskan berdasarkan impact:
- Masalah yang menyebabkan masalah lain (high priority)
- Masalah yang berdiri sendiri (medium priority)
- Masalah yang kecil tapi bisa dioptimasi (low priority)

**4. Fix secara bertahap, tapi komprehensif**

Fix masalah satu per satu, tapi pastikan semua masalah di-fix. Jangan berhenti setelah fix satu masalah.

**5. Test setelah setiap fix**

Setelah fix satu masalah, test apakah performance membaik. Tapi jangan berhenti di situ—masih ada masalah lain yang perlu di-fix.

---

## Trade-off yang Harus Diterima

Investigasi performance issue secara komprehensif punya trade-off:

**Kelemahan:**

- Butuh waktu lebih lama untuk investigasi
- Butuh observability tools yang baik
- Harus fix multiple masalah, bukan hanya satu

**Keuntungan:**

- Performance issue benar-benar selesai
- Tidak ada masalah yang terlewat
- Optimasi lebih optimal

Trade-off ini sepadan. Lebih baik invest waktu untuk investigasi yang komprehensif daripada fix masalah berkali-kali.

---

## Contoh Praktis: Investigasi Performance Issue

Berikut contoh bagaimana investigasi performance issue secara komprehensif:

**1. Identifikasi gejala**

```markdown
Gejala:
- API endpoint /reports/generate lambat (5-10 detik)
- Timeout terjadi ketika ada banyak request
- Database CPU usage tinggi
- Connection pool sering habis
```

**2. Investigasi semua komponen**

```markdown
## Application Code
- Ada N+1 query problem di loop
- Transaction dibuka terlalu lama
- Tidak ada caching

## Database Query
- Query melakukan full table scan
- Missing index di created_at
- Query tidak menggunakan index yang ada

## Connection Pool
- Pool size hanya 10 (terlalu kecil)
- Connection tidak di-release dengan benar
- Timeout terlalu pendek

## Locking
- Transaction terlalu lama
- Deadlock terjadi karena locking order yang salah
- Row-level locking tidak digunakan
```

**3. Prioritaskan masalah**

```markdown
High Priority:
1. Missing index (menyebabkan full table scan)
2. N+1 query problem (menyebabkan banyak query)
3. Connection pool terlalu kecil (menyebabkan timeout)

Medium Priority:
4. Transaction terlalu lama (menyebabkan locking)
5. Tidak ada caching (menyebabkan query berulang)

Low Priority:
6. Query bisa dioptimasi lebih lanjut
```

**4. Fix secara bertahap**

```sql
-- Fix 1: Tambahkan index
CREATE INDEX idx_orders_created_at ON orders(created_at);

-- Fix 2: Optimasi query dengan JOIN
SELECT o.*, u.name, u.email
FROM orders o
JOIN users u ON o.user_id = u.id
WHERE o.created_at BETWEEN ? AND ?
ORDER BY o.created_at DESC
LIMIT 100;
```

```javascript
// Fix 3: Fix N+1 query dengan JOIN
const orders = await db.query(`
  SELECT o.*, u.name, u.email
  FROM orders o
  JOIN users u ON o.user_id = u.id
  WHERE o.created_at BETWEEN ? AND ?
  ORDER BY o.created_at DESC
  LIMIT 100
`, [startDate, endDate]);

// Fix 4: Perbaiki transaction
await db.transaction(async (tx) => {
  // Hanya operasi yang perlu transaction
  const report = await generateReport(tx, startDate, endDate);
  await saveReport(tx, report);
  // Commit secepat mungkin
});
```

```javascript
// Fix 5: Perbesar connection pool
const pool = mysql.createPool({
  connectionLimit: 50, // dari 10 ke 50
  host: 'localhost',
  user: 'user',
  password: 'password',
  database: 'database'
});
```

**5. Test dan monitor**

Setelah setiap fix, test apakah performance membaik. Dan monitor untuk memastikan tidak ada masalah baru yang muncul.

---

## Penutup

Performance issue jarang hanya punya satu akar masalah. Sering kali ada multiple root causes yang saling terkait.

Ketika ada performance issue, jangan langsung berasumsi bahwa masalahnya hanya di satu tempat. Investigasi semua komponen yang terlibat. Identifikasi semua root causes. Dan fix secara komprehensif, bukan hanya satu masalah.

Di kantor saya, saya belajar bahwa fix satu masalah tidak cukup. Query yang lambat bisa disebabkan oleh missing index, connection pool yang terlalu kecil, locking, dan N+1 query problem—semuanya saling terkait. Dan ketika kita hanya fix satu masalah, masalah lain masih ada.

Jadi, ketika ada performance issue, investigasi secara komprehensif. Gunakan observability tools. Identifikasi semua root causes. Dan fix semuanya—bukan hanya yang terlihat paling besar.

Ingat: performance issue itu seperti gunung es. Yang terlihat di permukaan hanya sebagian kecil. Yang sebenarnya ada di bawah permukaan jauh lebih besar.

Performance issue jarang hanya satu akar masalah. Tapi ketika kita investigasi dan fix secara komprehensif, performance issue akan benar-benar selesai—bukan hanya terlihat selesai.
