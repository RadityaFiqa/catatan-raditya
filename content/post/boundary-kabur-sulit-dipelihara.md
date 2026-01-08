---
title: "Boundary yang Kabur Membuat Sistem Sulit Dipelihara"
date: 2025-10-06T10:00:00+07:00
description: "Refleksi tentang bagaimana boundary yang kabur antara komponen sistem membuat maintenance jadi sulit—contoh kasus database migration tanpa log di aplikasi."
tags: ["engineering", "architecture", "boundary", "maintenance", "software-engineer"]
categories: ["Engineering Journey"]
draft: false
---

## Masalah yang Sering Terjadi

"Database schema berubah, tapi tidak ada log migration di aplikasi."

"Service A mengubah database, tapi Service B tidak tahu."

"Perubahan database perlu approval atasan, tapi tidak ada tracking di code."

Masalah seperti ini sering muncul ketika boundary antara komponen sistem kabur. Database seharusnya punya boundary yang jelas—siapa yang boleh mengubah, bagaimana cara mengubah, dan bagaimana tracking perubahan. Tapi ketika boundary kabur, perubahan database jadi tidak ter-track, dan maintenance jadi sulit.

Masalahnya: **boundary yang kabur membuat sistem sulit dipelihara. Ketika tidak jelas siapa yang bertanggung jawab, bagaimana cara melakukan perubahan, dan bagaimana tracking perubahan, sistem jadi tidak maintainable**.

Di kantor saya, ada contoh konkret: seluruh perubahan database perlu document approval atasan, tapi tidak ada log migrations di aplikasi. Ketika database berubah, tidak ada yang tahu:
- Siapa yang mengubah?
- Kapan diubah?
- Kenapa diubah?
- Apakah perubahan sudah di-approve?

Tanpa tracking yang jelas, maintenance jadi sulit. Dan ketika ada masalah, sulit untuk trace back apa yang berubah.

---

## Kenapa Ini Sering Terjadi

Ada beberapa alasan kenapa boundary sering kabur:

**1. Tidak ada definisi yang jelas**

Banyak tim tidak mendefinisikan boundary dengan jelas. Tidak jelas siapa yang bertanggung jawab untuk apa, bagaimana cara melakukan perubahan, dan bagaimana tracking perubahan.

**2. Proses yang tidak ter-automasi**

Ketika proses tidak ter-automasi, boundary jadi kabur. Perubahan database dilakukan manual, tidak ada tracking otomatis, dan tidak ada validasi.

**3. Kurangnya dokumentasi**

Tanpa dokumentasi yang jelas tentang boundary, engineer tidak tahu apa yang boleh dan tidak boleh dilakukan. Mereka melakukan perubahan tanpa tahu dampaknya.

**4. Tidak ada enforcement**

Bahkan ketika boundary sudah didefinisikan, tidak ada enforcement. Engineer bisa melakukan perubahan yang melanggar boundary, dan tidak ada yang tahu.

---

## Contoh Kasus Nyata

Di kantor saya, ada masalah dengan boundary database yang membuat saya belajar pentingnya boundary yang jelas.

**Skenario: Database Migration Tanpa Log**

Di kantor saya, perubahan database perlu approval atasan. Tapi prosesnya manual:
1. Engineer membuat document perubahan database
2. Document dikirim ke atasan untuk approval
3. Setelah approval, perubahan dilakukan manual di database
4. Tidak ada log migration di aplikasi

Masalahnya:
- Tidak ada tracking perubahan di code
- Tidak ada cara untuk tahu apakah perubahan sudah di-approve
- Tidak ada cara untuk rollback jika ada masalah
- Tidak ada cara untuk apply perubahan ke environment lain

**Contoh konkret:**

```sql
-- Perubahan database dilakukan manual
-- Tidak ada migration file di aplikasi
-- Tidak ada log di aplikasi

-- Engineer A mengubah schema
ALTER TABLE users ADD COLUMN phone_number VARCHAR(20);

-- Engineer B tidak tahu perubahan ini
-- Engineer B deploy aplikasi yang expect column phone_number
-- Tapi di staging/production, column belum ada
-- Aplikasi crash karena column tidak ada
```

**Yang seharusnya:**

```javascript
// migrations/20250203_add_phone_number_to_users.js
export async function up(db) {
  await db.query(`
    ALTER TABLE users 
    ADD COLUMN phone_number VARCHAR(20)
  `);
  
  // Log migration
  await db.query(`
    INSERT INTO schema_migrations (version, name, applied_at)
    VALUES ('20250203', 'add_phone_number_to_users', NOW())
  `);
}

export async function down(db) {
  await db.query(`
    ALTER TABLE users 
    DROP COLUMN phone_number
  `);
  
  // Log rollback
  await db.query(`
    DELETE FROM schema_migrations 
    WHERE version = '20250203'
  `);
}

// Migration runner
async function runMigrations() {
  const appliedMigrations = await getAppliedMigrations();
  const pendingMigrations = await getPendingMigrations();
  
  for (const migration of pendingMigrations) {
    // Check approval sebelum apply
    if (!await isMigrationApproved(migration.version)) {
      throw new Error(`Migration ${migration.version} not approved`);
    }
    
    await migration.up(db);
    logger.info('Migration applied', { version: migration.version });
  }
}
```

Dengan ini:
- Semua perubahan database ter-track di code
- Approval bisa di-check sebelum apply migration
- Bisa rollback jika ada masalah
- Bisa apply ke environment lain dengan konsisten

---

## Dampak Teknis & Non-Teknis

Boundary yang kabur punya dampak yang jelas:

**Dampak teknis:**

- Perubahan tidak ter-track, sulit untuk trace back
- Tidak ada cara untuk rollback jika ada masalah
- Tidak ada cara untuk apply perubahan ke environment lain dengan konsisten
- Sistem jadi tidak maintainable

**Dampak non-teknis:**

- Waktu terbuang untuk debugging masalah yang seharusnya bisa dihindari
- Tim frustrasi karena tidak tahu apa yang berubah
- Trust menurun karena tidak ada tracking yang jelas

---

## Pendekatan Praktis

Membuat boundary yang jelas butuh pendekatan yang tepat. Beberapa pendekatan yang bisa dipakai:

**1. Define boundary dengan jelas**

Define boundary untuk setiap komponen:
- Siapa yang bertanggung jawab?
- Bagaimana cara melakukan perubahan?
- Bagaimana tracking perubahan?
- Bagaimana validasi perubahan?

**2. Automate proses**

Automate proses untuk memastikan boundary diikuti:
- Migration system untuk database
- CI/CD untuk deployment
- Validation untuk perubahan

**3. Dokumentasikan boundary**

Dokumentasikan boundary dengan jelas:
- Apa yang boleh dan tidak boleh dilakukan
- Bagaimana cara melakukan perubahan
- Siapa yang harus di-approve

**4. Enforce boundary**

Enforce boundary dengan tooling:
- Pre-commit hooks untuk validasi
- CI/CD checks untuk enforcement
- Monitoring untuk detect violation

**5. Track semua perubahan**

Track semua perubahan dengan jelas:
- Migration log untuk database
- Git history untuk code
- Change log untuk dokumentasi

---

## Trade-off yang Harus Diterima

Membuat boundary yang jelas punya trade-off:

**Keuntungan:**

- Sistem jadi lebih maintainable
- Perubahan ter-track dengan jelas
- Bisa rollback jika ada masalah
- Bisa apply perubahan ke environment lain dengan konsisten

**Kelemahan:**

- Butuh waktu untuk setup awal
- Proses jadi lebih formal (tapi lebih aman)
- Harus maintain migration system

Trade-off ini sepadan. Lebih baik invest waktu untuk membuat boundary yang jelas daripada menghabiskan waktu untuk debugging masalah yang seharusnya bisa dihindari.

---

## Contoh Praktis: Setup Boundary yang Jelas

Berikut contoh bagaimana setup boundary yang jelas untuk database:

**1. Migration system**

```javascript
// migration-system.js
import fs from 'fs';
import path from 'path';

export class MigrationSystem {
  constructor(db, migrationDir) {
    this.db = db;
    this.migrationDir = migrationDir;
  }
  
  async getAppliedMigrations() {
    // Create migrations table if not exists
    await this.db.query(`
      CREATE TABLE IF NOT EXISTS schema_migrations (
        version VARCHAR(255) PRIMARY KEY,
        name VARCHAR(255),
        applied_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
        applied_by VARCHAR(255),
        approval_doc VARCHAR(255)
      )
    `);
    
    const result = await this.db.query(`
      SELECT version, name, applied_at, applied_by, approval_doc
      FROM schema_migrations
      ORDER BY applied_at
    `);
    
    return result.rows;
  }
  
  async getPendingMigrations() {
    const applied = await this.getAppliedMigrations();
    const appliedVersions = new Set(applied.map(m => m.version));
    
    const files = fs.readdirSync(this.migrationDir)
      .filter(f => f.endsWith('.js'))
      .sort();
    
    return files
      .map(file => {
        const version = file.split('_')[0];
        return { file, version };
      })
      .filter(m => !appliedVersions.has(m.version));
  }
  
  async isMigrationApproved(version) {
    // Check approval dari document atau system
    const migration = await this.db.query(`
      SELECT approval_doc FROM schema_migrations WHERE version = ?
    `, [version]);
    
    if (migration.rows.length > 0) {
      return migration.rows[0].approval_doc !== null;
    }
    
    // Check dari approval system
    return await this.checkApprovalSystem(version);
  }
  
  async applyMigration(migration) {
    const { file, version } = migration;
    
    // Check approval
    if (!await this.isMigrationApproved(version)) {
      throw new Error(`Migration ${version} not approved`);
    }
    
    // Load migration
    const migrationModule = await import(
      path.join(this.migrationDir, file)
    );
    
    // Apply migration
    await migrationModule.up(this.db);
    
    // Log migration
    await this.db.query(`
      INSERT INTO schema_migrations (version, name, applied_by, approval_doc)
      VALUES (?, ?, ?, ?)
    `, [
      version,
      migrationModule.name || file,
      process.env.USER || 'system',
      await this.getApprovalDoc(version)
    ]);
    
    logger.info('Migration applied', { version, file });
  }
  
  async rollbackMigration(version) {
    const migration = await this.db.query(`
      SELECT name FROM schema_migrations WHERE version = ?
    `, [version]);
    
    if (migration.rows.length === 0) {
      throw new Error(`Migration ${version} not found`);
    }
    
    // Load migration
    const file = `${version}_*.js`;
    const migrationModule = await import(
      path.join(this.migrationDir, file)
    );
    
    // Rollback migration
    await migrationModule.down(this.db);
    
    // Remove from log
    await this.db.query(`
      DELETE FROM schema_migrations WHERE version = ?
    `, [version]);
    
    logger.info('Migration rolled back', { version });
  }
  
  async runMigrations() {
    const pending = await this.getPendingMigrations();
    
    for (const migration of pending) {
      await this.applyMigration(migration);
    }
  }
}
```

**2. Usage**

```javascript
// Run migrations on startup
const migrationSystem = new MigrationSystem(db, './migrations');

await migrationSystem.runMigrations();

// Check migration status
const applied = await migrationSystem.getAppliedMigrations();
console.log('Applied migrations:', applied);

// Rollback if needed
await migrationSystem.rollbackMigration('20250203');
```

**3. Migration file example**

```javascript
// migrations/20250203_add_phone_number_to_users.js
export const name = 'add_phone_number_to_users';
export const requiresApproval = true;

export async function up(db) {
  await db.query(`
    ALTER TABLE users 
    ADD COLUMN phone_number VARCHAR(20)
  `);
}

export async function down(db) {
  await db.query(`
    ALTER TABLE users 
    DROP COLUMN phone_number
  `);
}
```

---

## Penutup

Boundary yang kabur membuat sistem sulit dipelihara. Ketika tidak jelas siapa yang bertanggung jawab, bagaimana cara melakukan perubahan, dan bagaimana tracking perubahan, sistem jadi tidak maintainable.

Di kantor saya, saya belajar bahwa database migration tanpa log membuat maintenance jadi sulit. Perubahan tidak ter-track, tidak ada cara untuk rollback, dan tidak ada cara untuk apply ke environment lain dengan konsisten.

Jadi, buat boundary yang jelas untuk setiap komponen:
- Define siapa yang bertanggung jawab
- Automate proses untuk memastikan boundary diikuti
- Dokumentasikan boundary dengan jelas
- Enforce boundary dengan tooling
- Track semua perubahan

Dan yang paling penting: **boundary yang jelas itu investasi**. Investasi untuk sistem yang lebih maintainable, perubahan yang ter-track, dan debugging yang lebih mudah.

Ingat: boundary yang kabur itu seperti jalan tanpa rambu. Engineer tidak tahu apa yang boleh dan tidak boleh dilakukan, dan itu bisa menyebabkan masalah yang sulit di-debug.

Boundary yang jelas membuat sistem lebih mudah dipelihara. Dan ketika sistem mudah dipelihara, development jadi lebih cepat, bug jadi lebih sedikit, dan tim jadi lebih produktif.
