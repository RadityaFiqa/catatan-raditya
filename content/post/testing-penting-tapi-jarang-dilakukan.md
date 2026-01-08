---
title: "Kenapa Testing Selalu Dianggap Penting, Tapi Jarang Dilakukan"
date: 2025-02-23T10:00:00+07:00
description: "Refleksi tentang gap antara pentingnya testing di teori dan praktiknya di dunia kerja nyata."
tags: ["engineering", "testing", "quality", "software-engineer"]
categories: ["Engineering Journey"]
draft: false
---

## Masalah yang Sering Terjadi

"Testing itu penting," kata semua orang.

Setiap engineer tahu bahwa testing membantu mencegah bug, meningkatkan confidence saat refactoring, dan membuat kode lebih maintainable. Tapi di production, testing sering diabaikan.

PR dikirim tanpa test. Feature baru di-deploy dengan "nanti kita tambahkan test-nya". Bug muncul di production, dan baru saat itu test ditulis—untuk memastikan bug tidak terulang.

Masalahnya: **testing selalu dianggap penting, tapi jarang dilakukan dengan konsisten**.

---

## Kenapa Ini Sering Terjadi

Ada beberapa alasan kenapa testing sering diabaikan meski dianggap penting:

**1. Tekanan deadline**

Ketika deadline mendekat, testing jadi yang pertama dikorbankan. "Kode sudah jalan, test bisa ditambah nanti" adalah alasan yang sering muncul. Tapi "nanti" itu tidak pernah datang.

**2. Perasaan bahwa testing tidak langsung terlihat**

Testing tidak menghasilkan fitur baru yang bisa ditunjukkan ke stakeholder. Hasilnya tidak langsung terlihat. Engineer yang menulis test menghabiskan waktu, tapi hasilnya baru terasa ketika ada bug yang terhindar—yang tidak pernah terjadi kalau test tidak ada.

**3. Kurangnya contoh yang baik**

Banyak engineer tidak pernah melihat test yang benar-benar membantu. Mereka hanya melihat test yang:
- Terlalu kompleks dan sulit di-maintain
- Test implementation detail, bukan behavior
- Test yang brittle dan sering break tanpa alasan jelas

Ketika test seperti ini yang mereka lihat, wajar kalau mereka mengira testing itu membuang waktu.

**4. Tidak ada enforcement**

Di banyak tim, tidak ada requirement yang jelas tentang testing. PR tanpa test tetap di-approve. Feature tanpa test tetap di-deploy. Tanpa enforcement, testing jadi optional—dan yang optional sering diabaikan.

---

## Contoh Kasus Nyata

Saya pernah bekerja di tim yang punya code coverage 80%+. Tapi ketika ada bug di production, test tidak menangkapnya. Kenapa?

Karena test yang ada hanya test "happy path". Test memastikan bahwa ketika input benar, output benar. Tapi tidak ada test untuk:
- Input yang tidak valid
- Edge case yang tidak terduga
- Failure scenario

Contoh konkret: Ada fungsi yang menghitung diskon berdasarkan jumlah item:

```javascript
function calculateDiscount(items) {
  if (items.length >= 10) {
    return 0.2; // 20% discount
  }
  return 0;
}
```

Test yang ada:
```javascript
test('should return 20% discount for 10+ items', () => {
  const items = Array(10).fill({});
  expect(calculateDiscount(items)).toBe(0.2);
});
```

Test ini pass. Tapi di production, ada bug: ketika `items` adalah `null` atau `undefined`, aplikasi crash. Test tidak menangkap ini karena hanya test happy path.

Test yang seharusnya ada:
```javascript
test('should handle null input', () => {
  expect(() => calculateDiscount(null)).not.toThrow();
});

test('should handle empty array', () => {
  expect(calculateDiscount([])).toBe(0);
});

test('should handle undefined', () => {
  expect(() => calculateDiscount(undefined)).not.toThrow();
});
```

Tapi test seperti ini jarang ditulis karena "tidak ada requirement untuk handle null". Padahal di production, null bisa datang dari mana saja.

---

## Dampak Teknis & Non-Teknis

Tidak menulis test punya dampak yang jelas:

**Dampak teknis:**

- Bug tidak terdeteksi sampai di production
- Refactoring jadi menakutkan karena tidak ada safety net
- Kode jadi sulit di-maintain karena tidak ada dokumentasi dalam bentuk test
- Technical debt menumpuk karena tidak ada confidence untuk memperbaiki kode lama

**Dampak non-teknis:**

- Engineer jadi takut mengubah kode karena tidak tahu apakah perubahan akan break sesuatu
- Waktu debugging di production lebih lama karena harus reproduce bug manual
- Trust menurun karena bug sering muncul di production
- Onboarding lebih sulit karena tidak ada test yang menjelaskan bagaimana kode seharusnya bekerja

---

## Pendekatan Praktis

Testing yang baik tidak harus sempurna. Yang penting adalah **konsisten dan fokus pada yang penting**. Beberapa pendekatan yang bisa dipakai:

**1. Mulai dari yang paling penting**

Tidak perlu test semua kode. Fokus pada:
- Business logic yang kompleks
- Code yang sering diubah
- Code yang punya banyak edge case
- Code yang critical untuk bisnis

Contoh: Test fungsi kalkulasi harga lebih penting daripada test fungsi format tanggal.

**2. Test behavior, bukan implementation**

Test seharusnya menjelaskan "apa yang kode lakukan", bukan "bagaimana kode melakukannya". Test behavior lebih robust terhadap perubahan implementation.

```javascript
// ❌ Test implementation detail
test('should call database.query with correct SQL', () => {
  const mockQuery = jest.fn();
  getUserOrders(123, mockQuery);
  expect(mockQuery).toHaveBeenCalledWith('SELECT * FROM orders WHERE user_id = ?', [123]);
});

// ✅ Test behavior
test('should return orders for given user', async () => {
  const orders = await getUserOrders(123);
  expect(orders).toHaveLength(5);
  expect(orders[0].userId).toBe(123);
});
```

**3. Test edge case, bukan hanya happy path**

Happy path penting, tapi edge case lebih sering menyebabkan bug di production. Test untuk:
- Input yang tidak valid (null, undefined, empty)
- Boundary condition (0, -1, max value)
- Failure scenario (network error, database error)

**4. Buat test yang mudah di-maintain**

Test yang brittle (sering break tanpa alasan) lebih merugikan daripada tidak ada test. Pastikan test:
- Tidak terlalu coupled dengan implementation detail
- Menggunakan data yang jelas dan mudah dipahami
- Memiliki nama yang menjelaskan apa yang di-test

**5. Integrasikan ke workflow**

Testing harus jadi bagian dari workflow, bukan optional. Beberapa cara:
- Require test untuk PR tertentu (misalnya PR yang mengubah business logic)
- Set minimum code coverage untuk area tertentu
- Review test sebagai bagian dari code review

Tapi jangan terlalu ketat di awal. Mulai dari requirement yang ringan, lalu tingkatkan secara bertahap.

---

## Trade-off yang Harus Diterima

Menulis test punya trade-off:

**Kelemahan:**

- Butuh waktu lebih lama untuk development
- Butuh effort untuk maintain test ketika kode berubah
- Bisa jadi bottleneck jika test terlalu lambat

**Keuntungan:**

- Bug terdeteksi lebih awal
- Confidence lebih tinggi saat refactoring
- Kode lebih mudah di-maintain karena test adalah dokumentasi

Trade-off ini sepadan. Lebih baik invest waktu di test daripada menghabiskan waktu debugging di production.

Tapi ingat: test yang buruk lebih merugikan daripada tidak ada test. Jadi, pastikan test yang ditulis benar-benar membantu, bukan sekadar memenuhi requirement.

---

## Penutup

Testing itu penting. Tapi penting di teori tidak cukup. Testing harus jadi bagian dari praktik sehari-hari.

Tidak perlu test semua kode. Tidak perlu code coverage 100%. Yang penting adalah **test yang konsisten untuk kode yang penting**, dan test yang benar-benar membantu—bukan sekadar memenuhi requirement.

Mulai dari yang kecil. Test business logic yang kompleks. Test edge case yang berbahaya. Test code yang sering diubah. Dari situ, kebiasaan testing akan terbentuk secara alami.

Dan ketika test benar-benar membantu—ketika test menangkap bug sebelum di-deploy, atau ketika test memberi confidence untuk refactoring—baru saat itu engineer akan melihat nilai testing yang sebenarnya.

Testing tidak akan pernah jadi prioritas sampai engineer merasakan manfaatnya secara langsung. Jadi, mulailah dengan test yang benar-benar membantu, bukan test yang sekadar memenuhi angka.
