---
title: "Siapa yang Bertanggung Jawab Saat Security Issue Lolos ke Production?"
date: 2025-12-20T10:00:00+07:00
description: "Refleksi tentang tanggung jawab ketika security issue lolos ke production—apakah engineer, reviewer, atau seluruh tim?"
tags: ["engineering", "security", "responsibility", "software-engineer"]
categories: ["Engineering Journey"]
draft: false
---

## Masalah yang Sering Terjadi

"Security issue ini sudah ada di production selama 3 bulan."

"Vulnerability ini seharusnya terdeteksi di code review."

"Kenapa tidak ada yang melihat masalah ini?"

Ketika security issue lolos ke production, pertanyaan yang sering muncul adalah: siapa yang bertanggung jawab? Apakah engineer yang menulis kode? Reviewer yang approve? Atau seluruh tim?

Masalahnya: **menyalahkan satu orang itu tidak produktif. Security issue itu masalah sistem, bukan masalah individu. Dan menyelesaikan masalah sistem butuh pendekatan sistem juga**.

Saya pernah melihat security issue yang lolos ke production. Engineer yang menulis kode disalahkan. Tapi sebenarnya, masalahnya bukan hanya di engineer—tidak ada security review, tidak ada automated security scan, tidak ada security training. Masalahnya ada di sistem, bukan di individu.

---

## Kenapa Ini Sering Terjadi

Ada beberapa alasan kenapa security issue sering lolos ke production:

**1. Tidak ada security review**

Banyak tim tidak punya security review process. Code review fokus pada functionality, bukan security. Security issue tidak terdeteksi sampai di production.

**2. Kurangnya security awareness**

Banyak engineer tidak aware tentang security best practices. Mereka tidak tahu bahwa kode yang mereka tulis punya security issue.

**3. Tidak ada automated security scan**

Banyak tim tidak punya automated security scan. Security issue tidak terdeteksi secara otomatis, hanya terdeteksi ketika ada yang melihat manual.

**4. Pressure untuk cepat selesai**

Pressure untuk cepat selesai sering membuat security diabaikan. "Nanti kita perbaiki security-nya" adalah alasan yang sering muncul. Tapi "nanti" itu tidak pernah datang.

**5. Tidak ada clear ownership**

Tidak jelas siapa yang bertanggung jawab untuk security. Apakah engineer? Reviewer? Security team? Ketika tidak ada clear ownership, security sering diabaikan.

---

## Contoh Kasus Nyata

Bayangkan skenario seperti ini: Security issue SQL injection lolos ke production.

**Kode yang punya security issue:**

```javascript
// ❌ SQL injection vulnerability
app.get('/api/users', async (req, res) => {
  const { search } = req.query;
  
  // Vulnerable to SQL injection
  const users = await db.query(
    `SELECT * FROM users WHERE name LIKE '%${search}%'`
  );
  
  res.json(users);
});

// Engineer yang menulis kode ini mungkin tidak tahu tentang SQL injection
// Atau terburu-buru, jadi tidak pakai parameterized query
```

**Siapa yang bertanggung jawab?**

1. **Engineer yang menulis kode?**
   - Mungkin tidak tahu tentang SQL injection
   - Atau terburu-buru, jadi tidak pakai parameterized query
   - Tapi apakah ini kesalahan engineer?

2. **Reviewer yang approve?**
   - Mungkin tidak melihat security issue
   - Atau fokus pada functionality, bukan security
   - Tapi apakah ini kesalahan reviewer?

3. **Tim yang tidak punya security process?**
   - Tidak ada security review
   - Tidak ada automated security scan
   - Tidak ada security training
   - Tapi apakah ini kesalahan tim?

**Yang seharusnya:**

```javascript
// ✅ Secure code dengan parameterized query
app.get('/api/users', async (req, res) => {
  const { search } = req.query;
  
  // Parameterized query - safe from SQL injection
  const users = await db.query(
    'SELECT * FROM users WHERE name LIKE ?',
    [`%${search}%`]
  );
  
  res.json(users);
});

// Tapi untuk mencegah ini, perlu:
// 1. Security training untuk engineer
// 2. Security review process
// 3. Automated security scan
// 4. Security best practices documentation
```

Masalahnya bukan hanya di engineer yang menulis kode. Masalahnya ada di sistem yang tidak mencegah security issue.

---

## Dampak Teknis & Non-Teknis

Menyalahkan individu ketika security issue lolos punya dampak yang jelas:

**Dampak teknis:**

- Security issue tidak teratasi dengan benar karena fokus pada menyalahkan, bukan memperbaiki
- Engineer jadi takut untuk membuat perubahan karena takut disalahkan
- Security issue lain bisa muncul karena sistem tidak diperbaiki

**Dampak non-teknis:**

- Engineer frustrasi karena disalahkan untuk masalah sistem
- Trust menurun karena ada budaya menyalahkan
- Tim jadi tidak produktif karena fokus pada menyalahkan, bukan memperbaiki

---

## Pendekatan Praktis

Menghadapi security issue yang lolos ke production butuh pendekatan sistem, bukan menyalahkan individu. Beberapa pendekatan yang bisa dipakai:

**1. Fokus pada sistem, bukan individu**

Security issue itu masalah sistem, bukan masalah individu. Fokus pada memperbaiki sistem, bukan menyalahkan individu.

**2. Blameless post-mortem**

Ketika ada security issue, lakukan blameless post-mortem. Fokus pada "apa yang bisa diperbaiki", bukan "siapa yang salah".

**3. Perbaiki proses, bukan hanya kode**

Perbaiki proses untuk mencegah security issue di masa depan:
- Security training
- Security review process
- Automated security scan
- Security best practices documentation

**4. Shared responsibility**

Security adalah tanggung jawab semua orang, bukan hanya security team. Semua engineer harus aware tentang security.

**5. Learn from mistake**

Gunakan security issue sebagai learning opportunity. Apa yang bisa dipelajari? Apa yang bisa diperbaiki?

---

## Trade-off yang Harus Diterima

Pendekatan sistem untuk security punya trade-off:

**Keuntungan:**

- Security issue teratasi dengan benar
- Sistem diperbaiki untuk mencegah issue di masa depan
- Engineer tidak takut untuk membuat perubahan
- Trust dan produktivitas meningkat

**Kelemahan:**

- Butuh waktu untuk setup proses
- Butuh investasi untuk training dan tooling
- Tidak ada "quick fix" dengan menyalahkan individu

Trade-off ini sepadan. Lebih baik invest waktu untuk memperbaiki sistem daripada menyalahkan individu.

---

## Contoh Praktis: Setup Security Process

Berikut contoh bagaimana setup security process untuk mencegah security issue:

**1. Security training**

```javascript
// Security training untuk engineer
// - OWASP Top 10
// - Common vulnerabilities (SQL injection, XSS, etc.)
// - Security best practices
// - Secure coding guidelines
```

**2. Security review process**

```javascript
// Security checklist untuk code review
const securityChecklist = [
  '✅ Tidak ada SQL injection (pakai parameterized query)',
  '✅ Tidak ada XSS (sanitize input, escape output)',
  '✅ Authentication dan authorization sudah benar',
  '✅ Sensitive data tidak di-log',
  '✅ Error message tidak expose sensitive information',
  '✅ Input validation sudah dilakukan',
  '✅ Rate limiting sudah diimplementasikan (jika perlu)'
];
```

**3. Automated security scan**

```javascript
// Setup automated security scan
// - SAST (Static Application Security Testing)
// - Dependency scanning
// - Secret scanning

// package.json
{
  "scripts": {
    "security:scan": "npm audit && snyk test",
    "security:fix": "npm audit fix"
  }
}

// CI/CD pipeline
// - Run security scan sebelum merge
// - Block merge jika ada critical vulnerability
```

**4. Security best practices documentation**

```markdown
# Security Best Practices

## SQL Injection Prevention
- Always use parameterized queries
- Never concatenate user input into SQL

## XSS Prevention
- Sanitize all user input
- Escape output when rendering

## Authentication
- Use strong password requirements
- Implement rate limiting for login
- Use secure session management
```

**5. Blameless post-mortem process**

```markdown
# Security Issue Post-Mortem

## What happened?
- Describe the security issue

## Why did it happen?
- Root cause analysis (focus on system, not individual)

## What can we improve?
- Process improvements
- Tooling improvements
- Training improvements

## Action items
- [ ] Add security review checklist
- [ ] Setup automated security scan
- [ ] Conduct security training
```

---

## Penutup

Ketika security issue lolos ke production, menyalahkan individu itu tidak produktif. Security issue itu masalah sistem, bukan masalah individu. Dan menyelesaikan masalah sistem butuh pendekatan sistem juga.

Yang penting adalah fokus pada memperbaiki sistem, bukan menyalahkan individu. Lakukan blameless post-mortem, perbaiki proses, dan gunakan security issue sebagai learning opportunity.

Jadi, ketika ada security issue:
- Fokus pada sistem, bukan individu
- Lakukan blameless post-mortem
- Perbaiki proses, bukan hanya kode
- Shared responsibility untuk security
- Learn from mistake

Dan yang paling penting: **security adalah tanggung jawab semua orang, bukan hanya security team atau engineer yang menulis kode**. Semua orang harus aware tentang security, dan semua orang harus bertanggung jawab untuk mencegah security issue.

Ingat: menyalahkan individu itu mudah. Tapi itu tidak menyelesaikan masalah. Yang menyelesaikan masalah adalah memperbaiki sistem—proses, tooling, training, dan budaya security.

Siapa yang bertanggung jawab saat security issue lolos ke production? Jawabannya adalah: **semua orang**. Karena security adalah tanggung jawab bersama, dan memperbaiki sistem adalah tanggung jawab semua orang juga.

Security issue yang lolos ke production itu bukan kegagalan individu. Itu adalah kegagalan sistem. Dan memperbaiki sistem itu tanggung jawab semua orang.
