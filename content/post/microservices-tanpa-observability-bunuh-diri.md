---
title: "Microservices Tanpa Observability Itu Bunuh Diri Perlahan"
date: 2025-08-07T10:00:00+07:00
description: "Refleksi tentang pentingnya observability di sistem microservices, dan pengalaman mulai implementasi OpenTelemetry dan Winston logging."
tags: ["engineering", "microservices", "observability", "opentelemetry", "logging"]
categories: ["Engineering Journey"]
draft: false
---

## Masalah yang Sering Terjadi

"API endpoint ini lambat, tapi kita tidak tahu kenapa."

"Error muncul di production, tapi tidak ada log yang jelas."

"Service A memanggil Service B, tapi kita tidak tahu apakah panggilan itu sukses atau gagal."

Masalah seperti ini sering muncul di sistem microservices. Dan ketika muncul, debugging jadi seperti mencari jarum di tumpukan jerami—tanpa observability yang baik, kita tidak tahu apa yang terjadi.

Di kantor saya saat ini, sistem sudah menggunakan microservices. Tapi observability belum ada. Tidak ada distributed tracing. Logging tidak terstruktur. Metrics tidak dikumpulkan. Ketika ada masalah, kita harus menebak-nebak apa yang terjadi.

Masalahnya: **microservices tanpa observability itu seperti terbang buta. Sistem bisa jalan, tapi ketika ada masalah, kita tidak tahu harus mulai dari mana**.

---

## Kenapa Ini Sering Terjadi

Ada beberapa alasan kenapa observability sering diabaikan di sistem microservices:

**1. Dianggap tidak urgent**

Observability tidak menghasilkan fitur baru. Ketika deadline mendekat, observability jadi yang pertama dikorbankan. "Nanti kita tambahkan observability-nya" adalah alasan yang sering muncul. Tapi "nanti" itu tidak pernah datang.

**2. Kompleksitas yang terlihat tinggi**

Banyak yang mengira observability itu kompleks dan butuh setup yang rumit. Padahal, observability bisa dimulai dengan sederhana—dari logging yang terstruktur, lalu berkembang ke tracing dan metrics.

**3. Tidak tahu harus mulai dari mana**

Banyak engineer tidak tahu harus mulai dari mana untuk implementasi observability. Ada banyak tool: OpenTelemetry, Jaeger, Prometheus, Grafana. Pilihan yang banyak membuat bingung.

**4. Kurangnya pemahaman tentang dampak**

Banyak yang tidak menyadari bahwa tanpa observability, debugging di production bisa memakan waktu berjam-jam—atau bahkan berhari-hari. Waktu yang dihabiskan untuk debugging itu lebih mahal daripada waktu untuk setup observability.

---

## Contoh Kasus Nyata

Di kantor saya, ada insiden yang membuat saya sadar betapa pentingnya observability.

Skenarionya: User melaporkan bahwa fitur upload dokumen lambat. Tapi kita tidak tahu kenapa. Apakah masalahnya di:
- Service upload yang lambat?
- Service storage yang lambat?
- Network yang lambat?
- Database yang lambat?

Tanpa observability, kita harus menebak-nebak. Kita cek log service upload—tidak ada error. Kita cek log service storage—juga tidak ada error. Tapi user tetap melaporkan lambat.

Setelah beberapa jam debugging, baru ketemu: masalahnya di service storage yang memang lambat, tapi error-nya tidak ter-log dengan jelas. Dan karena tidak ada tracing, kita tidak tahu bahwa request dari service upload ke service storage memang lambat.

Kalau ada observability, kita bisa langsung lihat:
- Request dari service upload ke service storage butuh 5 detik
- Service storage memang lambat karena query database yang tidak optimal
- Masalahnya jelas dari awal, tidak perlu menebak-nebak

---

## Dampak Teknis & Non-Teknis

Tidak punya observability punya dampak yang jelas:

**Dampak teknis:**

- Debugging jadi sangat lama karena harus menebak-nebak
- Masalah tidak terdeteksi sampai user melaporkan
- Tidak ada data untuk optimasi performance
- Sulit memahami flow request di sistem yang kompleks

**Dampak non-teknis:**

- Waktu terbuang untuk debugging yang seharusnya bisa lebih cepat
- User experience buruk karena masalah tidak terdeteksi lebih awal
- Tim frustrasi karena tidak tahu harus mulai dari mana ketika ada masalah

---

## Pendekatan Praktis

Observability itu penting, tapi tidak harus sempurna sejak awal. Beberapa pendekatan yang bisa dipakai:

**1. Mulai dari logging yang terstruktur**

Logging yang terstruktur adalah dasar observability. Gunakan format yang konsisten (JSON), dan pastikan log mengandung informasi yang berguna.

Contoh dengan Winston:

```javascript
import winston from 'winston';

const logger = winston.createLogger({
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  defaultMeta: { service: 'upload-service' },
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});

// Usage
logger.info('File upload started', {
  userId: user.id,
  fileName: file.name,
  fileSize: file.size,
  traceId: req.traceId
});

logger.error('File upload failed', {
  userId: user.id,
  fileName: file.name,
  error: error.message,
  stack: error.stack,
  traceId: req.traceId
});
```

**2. Implementasikan distributed tracing**

Distributed tracing membantu memahami flow request di sistem yang kompleks. OpenTelemetry adalah standar yang baik untuk ini.

Contoh setup OpenTelemetry:

```javascript
import { NodeSDK } from '@opentelemetry/sdk-node';
import { getNodeAutoInstrumentations } from '@opentelemetry/auto-instrumentations-node';
import { OTLPTraceExporter } from '@opentelemetry/exporter-otlp-http';

const sdk = new NodeSDK({
  traceExporter: new OTLPTraceExporter({
    url: 'http://jaeger:4318/v1/traces'
  }),
  instrumentations: [getNodeAutoInstrumentations()],
  serviceName: 'upload-service'
});

sdk.start();
```

Dengan ini, setiap request otomatis di-trace. Kita bisa lihat:
- Request masuk ke service upload
- Service upload memanggil service storage
- Service storage memanggil database
- Berapa lama setiap step

**3. Tambahkan correlation ID**

Correlation ID membantu melacak request yang sama di berbagai service. Setiap request harus punya correlation ID yang unik, dan ID ini harus di-pass ke semua service yang dipanggil.

```javascript
// Middleware untuk generate correlation ID
app.use((req, res, next) => {
  req.correlationId = req.headers['x-correlation-id'] || generateId();
  res.setHeader('x-correlation-id', req.correlationId);
  next();
});

// Pass correlation ID ke service lain
const response = await fetch('http://storage-service/upload', {
  method: 'POST',
  headers: {
    'x-correlation-id': req.correlationId,
    'content-type': 'application/json'
  },
  body: JSON.stringify(fileData)
});
```

**4. Kumpulkan metrics yang penting**

Tidak perlu mengumpulkan semua metrics. Fokus pada metrics yang penting:
- Request rate
- Error rate
- Latency (p50, p95, p99)
- Resource usage (CPU, memory)

**5. Buat dashboard yang berguna**

Dashboard observability harus membantu menjawab pertanyaan:
- Apakah sistem sehat?
- Di mana masalahnya?
- Berapa lama waktu untuk resolve?

---

## Trade-off yang Harus Diterima

Implementasi observability punya trade-off:

**Kelemahan:**

- Butuh waktu untuk setup
- Butuh storage untuk menyimpan log dan trace
- Bisa menambah overhead jika tidak dikonfigurasi dengan baik

**Keuntungan:**

- Debugging jadi jauh lebih cepat
- Masalah terdeteksi lebih awal
- Ada data untuk optimasi performance

Trade-off ini sepadan. Lebih baik invest waktu untuk setup observability daripada menghabiskan waktu berjam-jam untuk debugging.

---

## Contoh Praktis: Setup Observability Sederhana

Berikut contoh setup observability yang sederhana tapi efektif:

**1. Structured logging dengan Winston**

```javascript
// logger.js
import winston from 'winston';

export const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  defaultMeta: {
    service: process.env.SERVICE_NAME || 'unknown',
    environment: process.env.NODE_ENV || 'development'
  },
  transports: [
    new winston.transports.Console({
      format: winston.format.combine(
        winston.format.colorize(),
        winston.format.simple()
      )
    }),
    new winston.transports.File({ 
      filename: 'error.log', 
      level: 'error' 
    }),
    new winston.transports.File({ 
      filename: 'combined.log' 
    })
  ]
});
```

**2. OpenTelemetry untuk tracing**

```javascript
// tracing.js
import { NodeSDK } from '@opentelemetry/sdk-node';
import { getNodeAutoInstrumentations } from '@opentelemetry/auto-instrumentations-node';
import { Resource } from '@opentelemetry/resources';
import { SemanticResourceAttributes } from '@opentelemetry/semantic-conventions';

const sdk = new NodeSDK({
  resource: new Resource({
    [SemanticResourceAttributes.SERVICE_NAME]: 'upload-service',
    [SemanticResourceAttributes.SERVICE_VERSION]: '1.0.0'
  }),
  traceExporter: new OTLPTraceExporter({
    url: process.env.JAEGER_ENDPOINT || 'http://localhost:4318/v1/traces'
  }),
  instrumentations: [getNodeAutoInstrumentations()]
});

sdk.start();
```

**3. Usage di code**

```javascript
import { logger } from './logger.js';
import { trace } from '@opentelemetry/api';

async function uploadFile(file, userId) {
  const tracer = trace.getTracer('upload-service');
  
  return tracer.startActiveSpan('upload-file', async (span) => {
    try {
      span.setAttribute('user.id', userId);
      span.setAttribute('file.name', file.name);
      span.setAttribute('file.size', file.size);
      
      logger.info('File upload started', {
        userId,
        fileName: file.name,
        fileSize: file.size
      });
      
      const result = await processUpload(file);
      
      span.setAttribute('upload.success', true);
      logger.info('File upload completed', {
        userId,
        fileName: file.name,
        fileId: result.id
      });
      
      return result;
    } catch (error) {
      span.setAttribute('upload.success', false);
      span.recordException(error);
      
      logger.error('File upload failed', {
        userId,
        fileName: file.name,
        error: error.message,
        stack: error.stack
      });
      
      throw error;
    } finally {
      span.end();
    }
  });
}
```

---

## Penutup

Microservices tanpa observability itu seperti terbang buta. Sistem bisa jalan, tapi ketika ada masalah, kita tidak tahu harus mulai dari mana.

Observability tidak harus sempurna sejak awal. Mulai dari logging yang terstruktur, lalu berkembang ke tracing dan metrics. Yang penting adalah mulai—dari yang sederhana, lalu berkembang seiring waktu.

Di kantor saya, saya mulai dengan Winston untuk logging dan OpenTelemetry untuk tracing. Setup-nya tidak rumit, tapi dampaknya langsung terasa. Ketika ada masalah, kita bisa langsung lihat di mana masalahnya—tidak perlu menebak-nebak lagi.

Jadi, kalau sistem kamu sudah menggunakan microservices tapi belum punya observability, mulai sekarang. Tidak perlu sempurna. Mulai dari logging yang terstruktur, lalu berkembang. Trust me, waktu yang dihabiskan untuk setup observability akan terbayar ketika ada masalah di production.

Ingat: observability itu investasi. Investasi untuk debugging yang lebih cepat, masalah yang terdeteksi lebih awal, dan sistem yang lebih mudah di-maintain.

Microservices tanpa observability itu bunuh diri perlahan. Jangan biarkan sistem kamu mati perlahan karena tidak tahu apa yang terjadi.
