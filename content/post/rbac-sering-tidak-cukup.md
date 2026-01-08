---
title: "Kenapa Role-Based Access Control Sering Tidak Cukup"
date: 2025-12-05T10:00:00+07:00
description: "Refleksi tentang keterbatasan RBAC dan kapan perlu menggunakan pendekatan yang lebih granular seperti attribute-based atau policy-based access control."
tags: ["engineering", "security", "rbac", "access-control", "software-engineer"]
categories: ["Engineering Journey"]
draft: false
---

## Masalah yang Sering Terjadi

"User ini role-nya admin, jadi bisa akses semua."

"User ini role-nya user, jadi hanya bisa akses data sendiri."

Role-Based Access Control (RBAC) itu sederhana dan mudah dipahami. Tapi di praktiknya, RBAC sering tidak cukup. Ada banyak skenario yang tidak bisa di-handle dengan role saja.

Masalahnya: **RBAC itu terlalu sederhana untuk kebutuhan yang kompleks. Ketika permission tidak hanya bergantung pada role, tapi juga pada context, resource, atau attribute, RBAC jadi tidak cukup**.

Saya pernah melihat sistem yang menggunakan RBAC sederhana—admin bisa akses semua, user hanya bisa akses data sendiri. Tapi ketika ada kebutuhan seperti "user bisa edit document yang dia buat, tapi tidak bisa edit document yang dibuat user lain", atau "manager bisa akses data timnya, tapi tidak bisa akses data tim lain", RBAC sederhana jadi tidak cukup.

---

## Kenapa Ini Sering Terjadi

Ada beberapa alasan kenapa RBAC sering tidak cukup:

**1. Permission bergantung pada context**

Banyak permission yang bergantung pada context, bukan hanya role. User bisa edit document yang dia buat, tapi tidak bisa edit document yang dibuat user lain. Manager bisa akses data timnya, tapi tidak bisa akses data tim lain.

**2. Permission bergantung pada resource**

Permission bisa bergantung pada resource itu sendiri. Document yang "public" bisa diakses semua user, tapi document yang "private" hanya bisa diakses owner. RBAC tidak bisa handle ini dengan baik.

**3. Permission bergantung pada attribute**

Permission bisa bergantung pada attribute user atau resource. User dengan department "Engineering" bisa akses resource Engineering, tapi tidak bisa akses resource Marketing. RBAC tidak bisa handle ini dengan baik.

**4. Permission yang dinamis**

Permission bisa berubah secara dinamis. User bisa jadi "temporary admin" untuk project tertentu, atau resource bisa jadi "shared" untuk beberapa user. RBAC tidak bisa handle ini dengan baik.

---

## Contoh Kasus Nyata

Bayangkan skenario seperti ini: Sistem menggunakan RBAC sederhana—admin bisa akses semua, user hanya bisa akses data sendiri.

**RBAC sederhana:**

```javascript
// ❌ RBAC sederhana - tidak cukup untuk kebutuhan kompleks
function canAccess(user, resource) {
  if (user.role === 'admin') {
    return true; // Admin bisa akses semua
  }
  
  if (user.role === 'user') {
    return resource.ownerId === user.id; // User hanya bisa akses data sendiri
  }
  
  return false;
}

// Masalahnya:
// - Tidak bisa handle "user bisa edit document yang dia buat, tapi tidak bisa edit document yang dibuat user lain"
// - Tidak bisa handle "manager bisa akses data timnya, tapi tidak bisa akses data tim lain"
// - Tidak bisa handle "document yang 'public' bisa diakses semua user"
// - Tidak bisa handle "user dengan department 'Engineering' bisa akses resource Engineering"
```

**Skenario yang tidak bisa di-handle:**

1. **User bisa edit document yang dia buat, tapi tidak bisa edit document yang dibuat user lain**

```javascript
// ❌ RBAC tidak bisa handle ini
// User bisa akses document (read), tapi tidak bisa edit document yang dibuat user lain

// Dengan RBAC sederhana:
// - User bisa akses semua document (jika permission "read" diberikan)
// - Atau user tidak bisa akses document sama sekali (jika permission "read" tidak diberikan)
// - Tidak bisa: user bisa read semua, tapi hanya bisa edit yang dia buat
```

2. **Manager bisa akses data timnya, tapi tidak bisa akses data tim lain**

```javascript
// ❌ RBAC tidak bisa handle ini
// Manager perlu akses data semua user di timnya, tapi tidak bisa akses data tim lain

// Dengan RBAC sederhana:
// - Manager bisa akses semua data (jika role "manager" diberikan full access)
// - Atau manager tidak bisa akses data sama sekali
// - Tidak bisa: manager bisa akses data timnya, tapi tidak bisa akses data tim lain
```

3. **Document yang "public" bisa diakses semua user**

```javascript
// ❌ RBAC tidak bisa handle ini
// Document dengan visibility "public" bisa diakses semua user, meski bukan owner

// Dengan RBAC sederhana:
// - User hanya bisa akses document yang dia buat
// - Tidak bisa: user bisa akses document "public" yang dibuat user lain
```

**Solusi dengan Attribute-Based Access Control (ABAC):**

```javascript
// ✅ ABAC - lebih fleksibel
function canAccess(user, resource, action) {
  // Check role
  if (user.role === 'admin') {
    return true;
  }
  
  // Check ownership
  if (resource.ownerId === user.id) {
    return true; // Owner bisa akses
  }
  
  // Check resource attribute (visibility)
  if (resource.visibility === 'public' && action === 'read') {
    return true; // Public resource bisa di-read semua user
  }
  
  // Check user attribute (department)
  if (resource.department === user.department && user.role === 'manager') {
    return true; // Manager bisa akses resource di department-nya
  }
  
  // Check team membership
  if (resource.teamId && user.teams.includes(resource.teamId)) {
    return true; // User bisa akses resource di team-nya
  }
  
  // Check shared access
  if (resource.sharedWith && resource.sharedWith.includes(user.id)) {
    return true; // User bisa akses resource yang di-share
  }
  
  return false;
}

// Lebih fleksibel dan bisa handle berbagai skenario
```

**Atau dengan Policy-Based Access Control:**

```javascript
// ✅ Policy-based - lebih ekspresif
const policies = [
  {
    // Admin bisa akses semua
    role: 'admin',
    action: '*',
    resource: '*',
    effect: 'allow'
  },
  {
    // Owner bisa akses resource-nya
    action: '*',
    resource: '*',
    condition: (user, resource) => resource.ownerId === user.id,
    effect: 'allow'
  },
  {
    // Public resource bisa di-read semua
    action: 'read',
    resource: '*',
    condition: (user, resource) => resource.visibility === 'public',
    effect: 'allow'
  },
  {
    // Manager bisa akses resource di department-nya
    role: 'manager',
    action: '*',
    resource: '*',
    condition: (user, resource) => resource.department === user.department,
    effect: 'allow'
  },
  {
    // Team member bisa akses resource di team-nya
    action: '*',
    resource: '*',
    condition: (user, resource) => 
      resource.teamId && user.teams.includes(resource.teamId),
    effect: 'allow'
  }
];

function canAccess(user, resource, action) {
  for (const policy of policies) {
    // Check role
    if (policy.role && policy.role !== user.role) {
      continue;
    }
    
    // Check action
    if (policy.action !== '*' && policy.action !== action) {
      continue;
    }
    
    // Check condition
    if (policy.condition && !policy.condition(user, resource)) {
      continue;
    }
    
    // Return effect
    return policy.effect === 'allow';
  }
  
  return false; // Default deny
}
```

---

## Dampak Teknis & Non-Teknis

RBAC yang tidak cukup punya dampak yang jelas:

**Dampak teknis:**

- Permission tidak bisa di-handle dengan tepat
- Workaround yang tidak aman (misalnya memberikan permission yang terlalu luas)
- Security issue karena permission tidak tepat

**Dampak non-teknis:**

- User frustrasi karena tidak bisa akses resource yang seharusnya bisa
- Atau user bisa akses resource yang seharusnya tidak bisa (security issue)
- Maintenance sulit karena permission logic tersebar di banyak tempat

---

## Pendekatan Praktis

Ketika RBAC tidak cukup, beberapa pendekatan yang bisa dipakai:

**1. Gunakan ABAC (Attribute-Based Access Control)**

ABAC lebih fleksibel karena permission bergantung pada attribute user dan resource, bukan hanya role.

**2. Gunakan Policy-Based Access Control**

Policy-based access control lebih ekspresif karena permission didefinisikan sebagai policy yang bisa di-evaluate.

**3. Hybrid: RBAC + Resource-based**

Gunakan RBAC untuk permission dasar, lalu tambahkan resource-based check untuk permission yang lebih granular.

**4. Document permission model dengan jelas**

Dokumentasikan permission model dengan jelas—siapa bisa akses apa, dalam kondisi apa.

**5. Test permission logic**

Test permission logic secara menyeluruh untuk memastikan permission bekerja dengan benar.

---

## Trade-off yang Harus Diterima

Menggunakan ABAC atau Policy-Based Access Control punya trade-off:

**Keuntungan:**

- Lebih fleksibel untuk kebutuhan kompleks
- Bisa handle berbagai skenario permission
- Lebih ekspresif dan mudah di-extend

**Kelemahan:**

- Lebih kompleks daripada RBAC
- Butuh waktu lebih lama untuk setup
- Bisa jadi over-engineering untuk kebutuhan sederhana

Trade-off ini sepadan ketika kebutuhan permission kompleks. Tapi tidak sepadan ketika kebutuhan permission sederhana.

---

## Contoh Praktis: Evolusi Access Control

Berikut contoh bagaimana access control bisa berevolusi:

**Fase 1: RBAC sederhana (cukup untuk kebutuhan sederhana)**

```javascript
// ✅ Cukup untuk kebutuhan sederhana
function canAccess(user, resource) {
  if (user.role === 'admin') return true;
  if (user.role === 'user' && resource.ownerId === user.id) return true;
  return false;
}
```

**Fase 2: RBAC + Resource attribute (ketika perlu visibility)**

```javascript
// ✅ Tambah resource attribute
function canAccess(user, resource) {
  if (user.role === 'admin') return true;
  if (resource.ownerId === user.id) return true;
  if (resource.visibility === 'public') return true; // Tambah visibility check
  return false;
}
```

**Fase 3: ABAC (ketika kebutuhan kompleks)**

```javascript
// ✅ ABAC untuk kebutuhan kompleks
function canAccess(user, resource, action) {
  // Multiple checks berdasarkan attribute
  if (user.role === 'admin') return true;
  if (resource.ownerId === user.id) return true;
  if (resource.visibility === 'public' && action === 'read') return true;
  if (user.department === resource.department && user.role === 'manager') return true;
  if (user.teams.includes(resource.teamId)) return true;
  return false;
}
```

**Fase 4: Policy-Based (ketika kebutuhan sangat kompleks)**

```javascript
// ✅ Policy-based untuk kebutuhan sangat kompleks
const policies = [
  // Define policies
];

function canAccess(user, resource, action) {
  return evaluatePolicies(policies, user, resource, action);
}
```

---

## Penutup

RBAC itu sederhana dan mudah dipahami. Tapi ketika permission tidak hanya bergantung pada role, RBAC jadi tidak cukup.

Yang penting adalah memilih access control model yang sesuai dengan kebutuhan. Untuk kebutuhan sederhana, RBAC sudah cukup. Tapi untuk kebutuhan kompleks, perlu menggunakan ABAC atau Policy-Based Access Control.

Jadi, ketika merancang access control:
- Evaluasi kebutuhan permission dengan jelas
- Pilih model yang sesuai (RBAC, ABAC, atau Policy-Based)
- Dokumentasikan permission model dengan jelas
- Test permission logic secara menyeluruh

Dan yang paling penting: **access control itu trade-off antara simplicity dan flexibility**. Pilih yang sesuai dengan kebutuhan—jangan over-engineer untuk kebutuhan sederhana, tapi jangan under-engineer untuk kebutuhan kompleks.

Ingat: RBAC itu baik untuk kebutuhan sederhana. Tapi ketika kebutuhan kompleks, RBAC tidak cukup. Dan itu normal—yang penting adalah mengenali kapan RBAC tidak cukup, dan kapan perlu menggunakan pendekatan yang lebih kompleks.

RBAC sering tidak cukup karena permission itu kompleks. Tapi dengan memilih model yang tepat, kita bisa membuat access control yang tepat untuk kebutuhan kita.
