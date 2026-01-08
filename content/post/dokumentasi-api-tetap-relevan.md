---
title: "Cara Menjaga Dokumentasi API Tetap Relevan"
date: 2025-09-21T10:00:00+07:00
description: "Refleksi tentang bagaimana menjaga dokumentasi API tetap relevan dengan auto-generate menggunakan ZOD untuk request dan response validation."
tags: ["engineering", "api", "documentation", "zod", "software-engineer"]
categories: ["Engineering Journey"]
draft: false
---

## Masalah yang Sering Terjadi

"Dokumentasi API ini sudah outdated."

"Request body-nya berubah, tapi dokumentasinya tidak di-update."

"Response format berbeda dengan yang ada di dokumentasi."

Masalah seperti ini sering muncul ketika dokumentasi API ditulis manual. Ketika code berubah, dokumentasi tidak di-update. Dan ketika dokumentasi tidak di-update, developer yang menggunakan API jadi bingung—mana yang benar, code atau dokumentasi?

Masalahnya: **dokumentasi API yang ditulis manual itu tidak sustainable. Setiap perubahan code harus diikuti dengan perubahan dokumentasi, dan itu sering terlewat**.

Solusinya? Auto-generate dokumentasi dari code. Dan untuk memastikan dokumentasi selalu relevan, gunakan schema validation seperti ZOD untuk request dan response. Dengan ini, dokumentasi otomatis update ketika schema berubah.

---

## Kenapa Ini Sering Terjadi

Ada beberapa alasan kenapa dokumentasi API sering tidak relevan:

**1. Dokumentasi ditulis manual**

Ketika dokumentasi ditulis manual, setiap perubahan code harus diikuti dengan perubahan dokumentasi. Tapi perubahan dokumentasi sering terlewat, terutama ketika deadline mendekat.

**2. Tidak ada single source of truth**

Code dan dokumentasi adalah dua hal terpisah. Tidak ada jaminan bahwa keduanya konsisten. Code bisa berubah tanpa update dokumentasi, atau dokumentasi bisa salah dari awal.

**3. Tidak ada validation**

Tanpa validation, tidak ada cara untuk memastikan bahwa request/response sesuai dengan dokumentasi. Developer bisa mengirim request yang tidak sesuai, dan tidak ada error sampai di production.

**4. Kurangnya tooling**

Banyak tim tidak punya tooling untuk auto-generate dokumentasi. Mereka mengira auto-generate dokumentasi itu kompleks, padahal sebenarnya tidak.

---

## Contoh Kasus Nyata

Di kantor saya, ada masalah dengan dokumentasi API yang membuat saya belajar pentingnya auto-generate dokumentasi.

**Skenario: API untuk Upload File**

API endpoint untuk upload file punya dokumentasi yang ditulis manual. Tapi ketika request body berubah (misalnya menambah field `metadata`), dokumentasi tidak di-update. Developer yang menggunakan API mengirim request tanpa field `metadata`, dan tidak ada error—tapi fitur tidak berfungsi dengan benar.

Masalahnya:
- Dokumentasi tidak update ketika code berubah
- Tidak ada validation untuk memastikan request sesuai dengan schema
- Developer bingung mana yang benar—code atau dokumentasi

**Solusi dengan ZOD:**

```javascript
import { z } from 'zod';
import { validateAndDocs, validateResponse } from './middleware/validate-and-docs';

// Define schema dengan ZOD
const uploadFileSchema = z.object({
  file: z.string(), // base64 atau file path
  fileName: z.string().min(1),
  fileType: z.string(),
  metadata: z.object({
    userId: z.string(),
    category: z.string().optional(),
    tags: z.array(z.string()).optional()
  }).optional()
});

const uploadFileResponseSchema = z.object({
  success: z.boolean(),
  fileId: z.string(),
  url: z.string().url(),
  uploadedAt: z.string().datetime()
});

// Validation dengan middleware - dokumentasi otomatis ter-generate
app.post(
  '/api/files/upload',
  validateAndDocs({
    endpoint: '/api/files/upload',
    method: 'POST',
    requestSchema: uploadFileSchema,
    responseSchema: uploadFileResponseSchema,
    description: 'Upload file to storage'
  }),
  validateResponse,
  async (req, res) => {
    // Request sudah divalidasi di middleware, pakai req.validatedBody
    const result = await uploadFile(req.validatedBody);
    
    // Response akan divalidasi otomatis oleh middleware
    res.json(result);
  }
);
```

Dengan middleware:
- Dokumentasi otomatis di-generate dan ter-register ketika route didefinisikan
- Request validation dilakukan di middleware, code route handler lebih clean
- Response validation juga di middleware, memastikan response selalu sesuai schema
- Error message jelas ketika validation gagal
- Dokumentasi selalu up-to-date karena di-generate dari schema yang sama dengan validation

---

## Dampak Teknis & Non-Teknis

Dokumentasi API yang tidak relevan punya dampak yang jelas:

**Dampak teknis:**

- Developer menggunakan API dengan cara yang salah
- Bug muncul di production karena request/response tidak sesuai
- Integration jadi lebih sulit karena dokumentasi tidak akurat

**Dampak non-teknis:**

- Waktu terbuang untuk debugging masalah yang seharusnya bisa dihindari
- Trust menurun karena dokumentasi tidak bisa dipercaya
- Onboarding lebih sulit karena dokumentasi tidak akurat

---

## Pendekatan Praktis

Menjaga dokumentasi API tetap relevan butuh pendekatan yang tepat. Beberapa pendekatan yang bisa dipakai:

**1. Auto-generate dari schema**

Gunakan schema validation (seperti ZOD) untuk request dan response, lalu auto-generate dokumentasi dari schema. Dengan ini, dokumentasi otomatis update ketika schema berubah.

**2. Single source of truth**

Schema adalah single source of truth. Code, validation, dan dokumentasi semua berasal dari schema yang sama. Tidak ada inkonsistensi.

**3. Validation di runtime**

Validasi request/response di runtime dengan schema. Ini memastikan bahwa request/response selalu sesuai dengan dokumentasi.

**4. Error message yang jelas**

Ketika validation gagal, return error message yang jelas. Ini membantu developer memahami apa yang salah.

**5. Versioning API**

Ketika API berubah, versioning membantu developer migrate secara bertahap. Dokumentasi untuk setiap version tetap relevan.

---

## Trade-off yang Harus Diterima

Auto-generate dokumentasi dengan ZOD punya trade-off:

**Keuntungan:**

- Dokumentasi selalu relevan karena auto-generate dari schema
- Request/response selalu divalidasi
- Tidak ada inkonsistensi antara code dan dokumentasi
- Error message jelas ketika validation gagal

**Kelemahan:**

- Butuh waktu untuk setup awal
- Harus define schema untuk semua endpoint
- Bisa jadi terlalu strict jika tidak dikonfigurasi dengan baik

Trade-off ini sepadan. Lebih baik invest waktu untuk setup auto-generate dokumentasi daripada menghabiskan waktu untuk update dokumentasi manual yang sering terlewat.

---

## Contoh Praktis: Setup Auto-Generate Dokumentasi dengan ZOD

Berikut contoh setup auto-generate dokumentasi dengan ZOD:

**1. Install library**

```bash
npm install @asteasolutions/zod-to-openapi zod
```

**2. Define schema dengan ZOD dan register ke OpenAPI**

```javascript
// schemas/upload.js
import { z } from 'zod';
import { registry } from '../openapi-registry.js';

// Register request schema ke OpenAPI registry
export const uploadFileRequestSchema = registry.register(
  'UploadFileRequest',
  z.object({
    file: z.string().describe('File content (base64 or file path)'),
    fileName: z.string().min(1).describe('File name'),
    fileType: z.string().describe('File MIME type'),
    metadata: z.object({
      userId: z.string().describe('User ID who uploads the file'),
      category: z.string().optional().describe('File category'),
      tags: z.array(z.string()).optional().describe('File tags')
    }).optional().describe('Additional metadata')
  })
);

// Register response schema ke OpenAPI registry
export const uploadFileResponseSchema = registry.register(
  'UploadFileResponse',
  z.object({
    success: z.boolean().describe('Whether upload was successful'),
    fileId: z.string().describe('Unique file identifier'),
    url: z.string().url().describe('URL to access the file'),
    uploadedAt: z.string().datetime().describe('Upload timestamp')
  })
);
```

**2. Setup OpenAPI Registry**

```javascript
// openapi-registry.js
import { OpenAPIRegistry, OpenApiGeneratorV3 } from '@asteasolutions/zod-to-openapi';

// Buat registry instance yang bisa di-share
export const registry = new OpenAPIRegistry();

// Function untuk generate OpenAPI document
export function generateOpenAPIDocument() {
  const generator = new OpenApiGeneratorV3(registry.definitions);
  
  return generator.generateDocument({
    openapi: '3.0.0',
    info: {
      version: '1.0.0',
      title: 'File Upload API',
      description: 'API untuk upload dan manage files'
    },
    servers: [
      { url: 'http://localhost:3000', description: 'Development' },
      { url: 'https://api.example.com', description: 'Production' }
    ]
  });
}
```

**3. Register schemas dan routes ke OpenAPI Registry**

```javascript
// schemas/upload.js
import { z } from 'zod';
import { registry } from '../openapi-registry.js';

// Register request schema
export const uploadFileRequestSchema = registry.register(
  'UploadFileRequest',
  z.object({
    file: z.string().describe('File content (base64 or file path)'),
    fileName: z.string().min(1).describe('File name'),
    fileType: z.string().describe('File MIME type'),
    metadata: z.object({
      userId: z.string().describe('User ID who uploads the file'),
      category: z.string().optional().describe('File category'),
      tags: z.array(z.string()).optional().describe('File tags')
    }).optional().describe('Additional metadata')
  })
);

// Register response schema
export const uploadFileResponseSchema = registry.register(
  'UploadFileResponse',
  z.object({
    success: z.boolean().describe('Whether upload was successful'),
    fileId: z.string().describe('Unique file identifier'),
    url: z.string().url().describe('URL to access the file'),
    uploadedAt: z.string().datetime().describe('Upload timestamp')
  })
);

// Register route ke OpenAPI
registry.registerPath({
  method: 'post',
  path: '/api/files/upload',
  summary: 'Upload file to storage',
  description: 'Upload file dengan metadata opsional',
  request: {
    body: {
      content: {
        'application/json': {
          schema: uploadFileRequestSchema
        }
      }
    }
  },
  responses: {
    200: {
      description: 'File berhasil di-upload',
      content: {
        'application/json': {
          schema: uploadFileResponseSchema
        }
      }
    },
    400: {
      description: 'Validation error',
      content: {
        'application/json': {
          schema: z.object({
            error: z.string(),
            details: z.array(z.object({
              path: z.string(),
              message: z.string()
            }))
          })
        }
      }
    }
  }
});
```

**4. Buat middleware untuk validation**

```javascript
// middleware/validate.js
import { z } from 'zod';

export function validateRequest(schema) {
  return async (req, res, next) => {
    try {
      req.validatedBody = schema.parse(req.body);
      next();
    } catch (error) {
      if (error instanceof z.ZodError) {
        return res.status(400).json({
          error: 'Validation failed',
          details: error.errors.map(err => ({
            path: err.path.join('.'),
            message: err.message
          }))
        });
      }
      next(error);
    }
  };
}

export function validateResponse(schema) {
  return (req, res, next) => {
    const originalJson = res.json.bind(res);
    
    res.json = function(data) {
      try {
        const validatedData = schema.parse(data);
        return originalJson(validatedData);
      } catch (error) {
        if (error instanceof z.ZodError) {
          // Log error tapi tetap return response
          console.error('Response validation failed:', error.errors);
        }
      }
      return originalJson(data);
    };
    
    next();
  };
}
```

**5. Gunakan middleware di route**

```javascript
// routes/upload.js
import { uploadFileRequestSchema, uploadFileResponseSchema } from '../schemas/upload.js';
import { validateRequest, validateResponse } from '../middleware/validate.js';

// Route handler dengan middleware
// Route sudah ter-register ke OpenAPI di schemas/upload.js
app.post(
  '/api/files/upload',
  validateRequest(uploadFileRequestSchema),
  validateResponse(uploadFileResponseSchema),
  async (req, res) => {
    // Request sudah divalidasi di middleware, pakai req.validatedBody
    const result = await uploadFile(req.validatedBody);
    
    // Response akan divalidasi otomatis oleh middleware
    res.json(result);
  }
);
```

**6. Serve OpenAPI dokumentasi**

```javascript
// Serve auto-generated OpenAPI docs
import { generateOpenAPIDocument } from './openapi-registry.js';

// Serve OpenAPI JSON
app.get('/api/docs/openapi.json', (req, res) => {
  const openApiDoc = generateOpenAPIDocument();
  res.json(openApiDoc);
});

// Serve Swagger UI (optional, butuh swagger-ui-express)
import swaggerUi from 'swagger-ui-express';

app.use('/api/docs', swaggerUi.serve);
app.get('/api/docs', swaggerUi.setup(generateOpenAPIDocument()));
```

Dengan menggunakan `@asteasolutions/zod-to-openapi`:
- Schemas otomatis ter-register ke OpenAPI registry
- Routes ter-register dengan metadata lengkap (summary, description, responses)
- OpenAPI document otomatis ter-generate dari registry
- Dokumentasi selalu up-to-date karena berasal dari schema yang sama dengan validation
- Bisa langsung digunakan dengan Swagger UI untuk interactive documentation

---

## Penutup

Dokumentasi API yang ditulis manual itu tidak sustainable. Setiap perubahan code harus diikuti dengan perubahan dokumentasi, dan itu sering terlewat.

Solusinya adalah auto-generate dokumentasi dari schema. Dan untuk memastikan dokumentasi selalu relevan, gunakan ZOD untuk request dan response validation. Dengan ini, dokumentasi otomatis update ketika schema berubah, dan request/response selalu divalidasi.

Di kantor saya, saya mulai menggunakan ZOD dengan `@asteasolutions/zod-to-openapi` untuk auto-generate dokumentasi API. Setup-nya tidak rumit, tapi dampaknya langsung terasa. Dokumentasi selalu relevan karena auto-generate dari schema yang sama dengan validation, request/response selalu divalidasi, dan tidak ada inkonsistensi antara code dan dokumentasi.

Library `@asteasolutions/zod-to-openapi` memudahkan proses ini dengan menyediakan `OpenAPIRegistry` untuk register schemas dan routes, lalu `OpenApiGeneratorV3` untuk generate OpenAPI document. Dengan ini, kita bisa langsung dapat Swagger UI yang interactive tanpa harus menulis dokumentasi manual.

Jadi, kalau dokumentasi API kamu masih ditulis manual, pertimbangkan untuk auto-generate dari schema. Gunakan ZOD dengan `@asteasolutions/zod-to-openapi` untuk generate OpenAPI documentation. Dan pastikan request/response divalidasi di runtime dengan schema yang sama.

Ingat: dokumentasi API yang tidak relevan itu lebih buruk daripada tidak ada dokumentasi. Karena dokumentasi yang salah menyesatkan developer, dan itu bisa menyebabkan bug di production.

Dokumentasi API yang relevan itu investasi. Investasi untuk developer experience yang lebih baik, bug yang lebih sedikit, dan integration yang lebih mudah.
