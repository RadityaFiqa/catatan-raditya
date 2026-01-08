---
title: "Error Handling yang Seharusnya Ada di Production"
date: 2025-04-24T10:00:00+07:00
description: "Refleksi tentang error handling yang benar-benar dibutuhkan di production, bukan sekadar try-catch yang kosong."
tags: ["engineering", "error-handling", "reliability", "software-engineer"]
categories: ["Engineering Journey"]
draft: false
---

## Masalah yang Sering Terjadi

"Error handling itu penting," kata semua orang.

Tapi di production, error handling sering sekadar `try-catch` yang kosong. Error ditangkap, di-log, lalu diabaikan. Atau error ditangkap, di-return null, lalu caller tidak tahu apa yang salah.

Masalahnya: **error handling di production sering tidak memadai. Error ditangkap, tapi tidak ditangani dengan benar**.

Saya pernah melihat kode yang menangkap error tapi tidak melakukan apa-apa:

```javascript
try {
  processPayment(order);
} catch (error) {
  console.log(error);
  // Error di-log, tapi payment tetap dianggap sukses
}
```

Atau kode yang menangkap error lalu return null tanpa konteks:

```javascript
function getUser(id) {
  try {
    return db.query('SELECT * FROM users WHERE id = ?', [id]);
  } catch (error) {
    return null; // Caller tidak tahu kenapa null
  }
}
```

Padahal error handling yang baik tidak hanya menangkap error. Error handling yang baik **memberikan konteks, memungkinkan recovery, dan memastikan sistem tetap stabil**.

---

## Kenapa Ini Sering Terjadi

Ada beberapa alasan kenapa error handling sering tidak memadai:

**1. Tidak tahu apa yang harus dilakukan**

Banyak engineer tidak tahu apa yang harus dilakukan ketika error terjadi. Mereka menangkap error, tapi bingung bagaimana menanganinya. Akhirnya error di-log dan diabaikan.

**2. Tekanan untuk "kode cepat jalan"**

Ketika deadline mendekat, error handling jadi yang pertama dikorbankan. "Nanti kita tambahkan error handling-nya" adalah alasan yang sering muncul. Tapi "nanti" itu tidak pernah datang.

**3. Kurangnya pemahaman tentang failure mode**

Banyak engineer tidak memikirkan failure mode sejak awal. Mereka mengasumsikan bahwa semua operasi akan sukses. Ketika error terjadi, mereka tidak siap.

**4. Tidak ada contoh yang baik**

Banyak engineer tidak pernah melihat error handling yang benar-benar baik. Mereka hanya melihat `try-catch` yang kosong atau `return null`. Mereka tidak tahu bahwa error handling bisa lebih dari itu.

---

## Contoh Kasus Nyata

Saya pernah melihat kode yang menangani error dengan cara yang salah:

```javascript
// ❌ Error handling yang tidak memadai
async function processOrder(order) {
  try {
    await db.orders.insert(order);
    await emailService.send({ to: order.email, subject: 'Order received' });
    await inventoryService.decrement(order.items);
  } catch (error) {
    console.log('Error:', error);
    // Error di-log, tapi tidak ada yang tahu apa yang terjadi
    // Order mungkin sudah di-insert, email mungkin sudah terkirim
    // Tapi inventory mungkin belum di-decrement
    // Data jadi inconsistent
  }
}
```

Kode ini bermasalah karena:
- Tidak ada konteks tentang error apa yang terjadi
- Tidak ada informasi tentang state sistem ketika error terjadi
- Tidak ada recovery mechanism
- Data bisa jadi inconsistent

Error handling yang seharusnya:

```javascript
// ✅ Error handling yang memadai
async function processOrder(order) {
  const transaction = db.beginTransaction();
  
  try {
    // Step 1: Insert order
    const orderRecord = await transaction.orders.insert(order);
    
    // Step 2: Decrement inventory
    await transaction.inventory.decrement(order.items);
    
    // Step 3: Commit transaction
    await transaction.commit();
    
    // Step 4: Send email (outside transaction, bisa retry)
    try {
      await emailService.send({ 
        to: order.email, 
        subject: 'Order received',
        body: `Your order #${orderRecord.id} has been received`
      });
    } catch (emailError) {
      // Email gagal, tapi order sudah sukses
      // Log untuk retry later, jangan fail order
      logger.warn('Failed to send order email', {
        orderId: orderRecord.id,
        error: emailError.message
      });
      // Bisa tambahkan ke queue untuk retry
      await emailQueue.add({ orderId: orderRecord.id, email: order.email });
    }
    
    return orderRecord;
    
  } catch (error) {
    // Rollback transaction jika ada error
    await transaction.rollback();
    
    // Log dengan konteks yang jelas
    logger.error('Failed to process order', {
      orderId: order.id,
      customerId: order.customerId,
      error: error.message,
      stack: error.stack,
      timestamp: new Date().toISOString()
    });
    
    // Throw error dengan konteks
    throw new OrderProcessingError('Failed to process order', {
      orderId: order.id,
      originalError: error
    });
  }
}
```

Error handling ini lebih baik karena:
- Menggunakan transaction untuk memastikan data consistency
- Memberikan konteks yang jelas tentang error
- Memisahkan critical operation (database) dari non-critical (email)
- Memungkinkan recovery untuk non-critical operation
- Memberikan informasi yang cukup untuk debugging

---

## Dampak Teknis & Non-Teknis

Error handling yang tidak memadai punya dampak yang jelas:

**Dampak teknis:**

- Data inconsistency karena error tidak ditangani dengan benar
- Sulit di-debug karena tidak ada konteks tentang error
- Sistem tidak stabil karena error tidak di-handle dengan baik
- User experience buruk karena error tidak dikomunikasikan dengan jelas

**Dampak non-teknis:**

- Waktu debugging lebih lama karena tidak ada informasi yang cukup
- Trust menurun karena sistem sering error tanpa penjelasan
- Onboarding lebih sulit karena tidak ada dokumentasi tentang error handling

---

## Pendekatan Praktis

Error handling yang baik harus **memberikan konteks, memungkinkan recovery, dan memastikan sistem tetap stabil**. Beberapa pendekatan yang bisa dipakai:

**1. Pahami failure mode sejak awal**

Jangan menunggu error terjadi. Pikirkan failure mode sejak awal:
- Apa yang bisa gagal?
- Apa dampaknya jika gagal?
- Bagaimana menanganinya?

Contoh: Kalau operasi database bisa gagal, pikirkan:
- Apakah perlu transaction?
- Apakah perlu retry?
- Apakah perlu fallback?

**2. Berikan konteks yang jelas**

Error message harus memberikan konteks yang jelas:
- Apa yang sedang dilakukan ketika error terjadi?
- Apa input yang menyebabkan error?
- Apa state sistem ketika error terjadi?

```javascript
// ❌ Error tanpa konteks
catch (error) {
  console.log(error);
}

// ✅ Error dengan konteks
catch (error) {
  logger.error('Failed to process payment', {
    orderId: order.id,
    amount: order.amount,
    paymentMethod: order.paymentMethod,
    error: error.message,
    stack: error.stack
  });
}
```

**3. Pisahkan critical dan non-critical operation**

Critical operation (seperti database transaction) harus di-handle dengan ketat. Non-critical operation (seperti email notification) bisa di-handle dengan lebih fleksibel.

```javascript
// Critical operation: Harus sukses
await db.transaction(async (tx) => {
  await tx.orders.insert(order);
  await tx.inventory.decrement(order.items);
});

// Non-critical operation: Bisa gagal, retry later
try {
  await emailService.send({ to: order.email, subject: 'Order received' });
} catch (error) {
  // Log dan queue untuk retry
  await emailQueue.add({ orderId: order.id, email: order.email });
}
```

**4. Gunakan custom error class**

Custom error class membantu memberikan konteks dan memungkinkan error handling yang lebih spesifik:

```javascript
class OrderProcessingError extends Error {
  constructor(message, context) {
    super(message);
    this.name = 'OrderProcessingError';
    this.context = context;
  }
}

// Usage
try {
  await processOrder(order);
} catch (error) {
  if (error instanceof OrderProcessingError) {
    // Handle order processing error specifically
    logger.error('Order processing failed', error.context);
    return { success: false, error: 'Failed to process order' };
  }
  // Handle other errors
  throw error;
}
```

**5. Implementasikan retry untuk transient error**

Beberapa error bersifat transient (sementara) dan bisa di-retry. Implementasikan retry mechanism untuk error seperti ini:

```javascript
async function retryOperation(operation, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await operation();
    } catch (error) {
      if (i === maxRetries - 1) throw error;
      
      // Check if error is retryable
      if (error.code === 'ECONNRESET' || error.code === 'ETIMEDOUT') {
        await sleep(1000 * (i + 1)); // Exponential backoff
        continue;
      }
      
      // Not retryable, throw immediately
      throw error;
    }
  }
}
```

**6. Komunikasikan error ke user dengan jelas**

Error yang dikomunikasikan ke user harus jelas dan actionable:

```javascript
// ❌ Error message yang tidak jelas
catch (error) {
  return { success: false, error: 'Something went wrong' };
}

// ✅ Error message yang jelas dan actionable
catch (error) {
  if (error instanceof ValidationError) {
    return { 
      success: false, 
      error: 'Invalid input', 
      details: error.validationErrors 
    };
  }
  
  if (error instanceof PaymentError) {
    return { 
      success: false, 
      error: 'Payment failed', 
      message: 'Please check your payment method and try again' 
    };
  }
  
  // Generic error untuk user, detail untuk log
  logger.error('Unexpected error', { error: error.message, stack: error.stack });
  return { 
    success: false, 
    error: 'An unexpected error occurred. Please try again later.' 
  };
}
```

---

## Trade-off yang Harus Diterima

Error handling yang baik punya trade-off:

**Kelemahan:**

- Butuh waktu lebih lama untuk development
- Kode jadi lebih kompleks
- Butuh pemahaman yang lebih dalam tentang failure mode

**Keuntungan:**

- Sistem lebih stabil dan reliable
- Debugging lebih mudah karena ada konteks yang jelas
- User experience lebih baik karena error dikomunikasikan dengan jelas

Trade-off ini sepadan. Lebih baik invest waktu di error handling daripada menghabiskan waktu debugging di production.

---

## Contoh Praktis: Error Handling yang Seharusnya Ada

Berikut beberapa contoh error handling yang seharusnya ada di production:

**1. Database operation dengan transaction**

```javascript
async function transferMoney(fromAccount, toAccount, amount) {
  const transaction = db.beginTransaction();
  
  try {
    // Check balance
    const fromBalance = await transaction.accounts.getBalance(fromAccount);
    if (fromBalance < amount) {
      throw new InsufficientBalanceError('Insufficient balance');
    }
    
    // Debit from account
    await transaction.accounts.debit(fromAccount, amount);
    
    // Credit to account
    await transaction.accounts.credit(toAccount, amount);
    
    // Commit
    await transaction.commit();
    
    return { success: true };
    
  } catch (error) {
    await transaction.rollback();
    
    logger.error('Money transfer failed', {
      fromAccount,
      toAccount,
      amount,
      error: error.message
    });
    
    throw error;
  }
}
```

**2. External API call dengan retry**

```javascript
async function callExternalAPI(url, data, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      const response = await fetch(url, {
        method: 'POST',
        body: JSON.stringify(data),
        headers: { 'Content-Type': 'application/json' }
      });
      
      if (!response.ok) {
        throw new APIError(`API returned ${response.status}`, { status: response.status });
      }
      
      return await response.json();
      
    } catch (error) {
      if (i === maxRetries - 1) {
        logger.error('External API call failed after retries', {
          url,
          attempts: maxRetries,
          error: error.message
        });
        throw error;
      }
      
      // Retry for transient errors
      if (error.code === 'ECONNRESET' || error.code === 'ETIMEDOUT') {
        await sleep(1000 * (i + 1));
        continue;
      }
      
      // Don't retry for client errors
      if (error instanceof APIError && error.status >= 400 && error.status < 500) {
        throw error;
      }
      
      // Retry for other errors
      await sleep(1000 * (i + 1));
    }
  }
}
```

**3. File operation dengan cleanup**

```javascript
async function processFile(filePath) {
  let fileHandle = null;
  
  try {
    fileHandle = await fs.open(filePath, 'r');
    const content = await fileHandle.readFile();
    
    // Process content
    const result = await processContent(content);
    
    return result;
    
  } catch (error) {
    logger.error('File processing failed', {
      filePath,
      error: error.message
    });
    throw error;
    
  } finally {
    // Always cleanup
    if (fileHandle) {
      await fileHandle.close();
    }
  }
}
```

---

## Penutup

Error handling yang baik tidak hanya menangkap error. Error handling yang baik **memberikan konteks, memungkinkan recovery, dan memastikan sistem tetap stabil**.

Jangan hanya menangkap error lalu mengabaikannya. Pikirkan:
- Apa yang harus dilakukan ketika error terjadi?
- Bagaimana memberikan konteks yang jelas?
- Bagaimana memastikan sistem tetap stabil?
- Bagaimana memungkinkan recovery?

Error handling itu investasi. Investasi untuk sistem yang lebih stabil, debugging yang lebih mudah, dan user experience yang lebih baik.

Jadi, pahami failure mode sejak awal. Berikan konteks yang jelas. Pisahkan critical dan non-critical operation. Dan komunikasikan error ke user dengan jelas.

Error handling yang baik tidak terlihat ketika semuanya berjalan lancar. Tapi ketika error terjadi, error handling yang baik membuat perbedaan antara sistem yang crash dan sistem yang tetap stabil.

Ingat: error itu tidak bisa dihindari. Tapi error handling yang baik bisa memastikan bahwa error tidak membuat sistem tidak stabil, dan error bisa di-debug dan di-recover dengan mudah.

Error handling itu bukan sekadar `try-catch`. Error handling itu **memastikan sistem tetap reliable meski ada error**.
