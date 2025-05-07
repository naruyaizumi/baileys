# <div align='center'>Baileys - WhatsApp API</div>

<div align='center'>

![WhatsApp API](https://i.supa.codes/yJrRqe)

</div>

Baileys adalah library TypeScript berbasis WebSocket untuk berinteraksi dengan WhatsApp Web API.

# Penggunaan
Panduan terbaru telah dipublikasikan di https://baileys.wiki.

# Penyangkalan
Proyek ini **tidak berafiliasi, tidak berasosiasi, tidak diotorisasi, tidak didukung, dan tidak memiliki koneksi resmi dengan WhatsApp** maupun anak perusahaan atau afiliasinya.  
Situs resmi WhatsApp dapat ditemukan di whatsapp.com.  
"WhatsApp" serta nama-nama, merek dagang, lambang, dan gambar terkait adalah hak milik terdaftar dari pemilik masing-masing.

Para pengelola Baileys **tidak mendukung penggunaan aplikasi ini untuk praktik yang melanggar Ketentuan Layanan WhatsApp**.  
Kami menyerukan kepada setiap pengguna untuk menggunakan aplikasi ini secara bertanggung jawab dan sesuai tujuan awal pengembangannya.  
Gunakan atas kebijakan pribadi masing-masing. Jangan gunakan untuk spam. Kami tidak mendukung penggunaan untuk stalkerware, pesan massal, ataupun automasi pesan yang bersifat mengganggu.

### Lisensi

Proyek ini menggunakan lisensi [MIT License](https://github.com/WhiskeySockets/Baileys?tab=readme-ov-file#license), dan merupakan karya turunan dari Baileys oleh Rajeh Taher/WhiskeySockets.

Dengan menggunakan proyek ini, Anda dianggap telah menyetujui ketentuan lisensi tersebut.

## Tentang Modifikasi

Proyek ini merupakan hasil **modifikasi penuh dari Baileys**, library open-source yang awalnya ditulis dalam TypeScript oleh komunitas pengembang WhatsApp Web API.  
Modifikasi ini dilakukan untuk memenuhi kebutuhan pengembangan sistem berbasis JavaScript murni tanpa dukungan TypeScript, sehingga lebih mudah diintegrasikan pada ekosistem runtime yang tidak memerlukan proses transpilasi tambahan.

Beberapa poin penting dari modifikasi ini:

- **Seluruh kode sumber telah dikonversi dari TypeScript ke JavaScript**, dengan tetap mempertahankan struktur logika, arsitektur modular, dan kompatibilitas fungsional terhadap API Web WhatsApp Multi-Device.
- Penyesuaian dilakukan pada **sistem build, konfigurasi module, serta interface** agar sesuai dengan standar ECMAScript modern tanpa dependensi TypeScript.
- Sejumlah bagian dalam library juga dioptimalkan dan disederhanakan untuk mempermudah debugging, deployment, dan integrasi dengan proyek berbasis Node.js lainnya.
- Tujuan utama dari perubahan ini adalah untuk memberikan fleksibilitas lebih kepada developer yang ingin menggunakan Baileys namun dengan lingkungan kerja berbasis JavaScript penuh.

Harap dicatat bahwa meskipun proyek ini mengadaptasi struktur asli Baileys, seluruh proses refaktor dan penyesuaian kode dilakukan secara independen oleh pengelola repositori ini.

## Instalasi

Gunakan salah satu manajer paket berikut untuk menginstal versi stabil:

```bash
npm install naruyaizumi
```

```bash
yarn add naruyaizumi
```

```bash
pnpm add naruyaizumi
```

### Penggunaan

**Untuk proyek dengan ESM (ECMAScript Modules):**
```javascript
import makeWASocket from 'naruyaizumi'
```

**Untuk proyek dengan CJS (CommonJS):**
```javascript
const makeWASocket = require('naruyaizumi')
```

naruyaizumi secara otomatis mendukung ESM dan CJS tanpa perlu konfigurasi tambahan.

## Informasi

Saat ini, `naruyaizumi` membutuhkan Node.js versi **20 atau lebih tinggi** untuk berjalan.

Proyek ini secara eksplisit ditujukan untuk lingkungan modern dan tidak mendukung Node versi lama. Dukungan akan selalu mengikuti versi LTS terbaru dari Node.js untuk menjaga performa dan kompatibilitas dengan ekosistem terbaru.

## Menghubungkan Akun

WhatsApp menyediakan API multi-perangkat yang memungkinkan Baileys untuk diautentikasi sebagai klien WhatsApp kedua dengan cara memindai **kode QR** atau menggunakan **Kode Pairing** melalui aplikasi WhatsApp di ponsel Anda.

### Memulai Socket dengan **Kode QR**

> [!TIP]  
> Anda dapat menyesuaikan nama browser jika terhubung menggunakan **Kode QR** dengan konstanta `Browser`.  
> Beberapa konfigurasi browser tersedia, **lihat dokumentasinya [di sini](https://baileys.whiskeysockets.io/types/BrowsersMap.html)**

```javascript
import makeWASocket, { Browsers } from 'naruyaizumi'

const sock = makeWASocket({
  browser: Browsers.ubuntu('Safari'),
  printQRInTerminal: true
})
```

Jika koneksi berhasil, kode QR akan ditampilkan di terminal Anda.  
Pindai kode tersebut menggunakan WhatsApp di ponsel Anda, dan Anda akan berhasil masuk!

### Memulai Socket dengan **Kode Pairing**

> [!IMPORTANT]  
> Pairing Code *bukan* API Mobile. Ini adalah metode untuk terhubung ke WhatsApp Web **tanpa memindai kode QR**.  
> Metode ini hanya memungkinkan koneksi dari **satu perangkat saja**.  
> Lihat penjelasan resmi [di sini](https://faq.whatsapp.com/1324084875126592/?cms_platform=web)

Nomor telepon **tidak boleh menggunakan karakter** seperti `+`, `()`, atau `-`.  
Gunakan hanya angka dan pastikan menyertakan kode negara.

```javascript
import makeWASocket from 'naruyaizumi'

const sock = makeWASocket({
  // Konfigurasi tambahan dapat disesuaikan di sini
  printQRInTerminal: false // Harus disetel ke false untuk pairing
})

if (!sock.authState.creds.registered) {
  const number = '628XXXXXXXXX'
  const code = await sock.requestPairingCode(number) 
  // atau gunakan pairing code kustom:
  // const code = await sock.requestPairingCode(number, 'CODEZUMI')
  console.log(code)
}
```

Setelah pairing code berhasil dibuat, masukkan kode tersebut melalui WhatsApp Web seperti biasa untuk menyelesaikan proses autentikasi.

### Menerima Riwayat Pesan Lengkap

1. Atur opsi `syncFullHistory` ke `true`
2. Secara default, Baileys menggunakan konfigurasi browser Chrome.
   - Jika Anda ingin **meniru koneksi desktop** (dan menerima riwayat pesan yang lebih banyak), gunakan konfigurasi browser seperti berikut pada pengaturan Socket Anda:

```javascript
import makeWASocket, { Browsers } from 'naruyaizumi'

const sock = makeWASocket({
  ...otherOpts,
  // Anda dapat menggunakan Windows, Ubuntu, dll.
  browser: Browsers.ubuntu('Safari'),
  syncFullHistory: true
})
```

## Catatan Penting Mengenai Konfigurasi Socket

### Caching Metadata Grup (Direkomendasikan)

- Jika Anda menggunakan Baileys untuk mengelola grup, sangat disarankan untuk mengatur opsi `cachedGroupMetadata` pada konfigurasi socket Anda.  
- Anda perlu mengimplementasikan sistem cache sederhana seperti contoh berikut:

```javascript
import makeWASocket from 'naruyaizumi'
import NodeCache from 'node-cache'

const groupCache = new NodeCache({ stdTTL: 5 * 60, useClones: false })

const sock = makeWASocket({
  cachedGroupMetadata: async (jid) => groupCache.get(jid)
})

sock.ev.on('groups.update', async ([event]) => {
  const metadata = await sock.groupMetadata(event.id)
  groupCache.set(event.id, metadata)
})

sock.ev.on('group-participants.update', async (event) => {
  const metadata = await sock.groupMetadata(event.id)
  groupCache.set(event.id, metadata)
})
```

Dengan menggunakan cache ini, Anda dapat mengurangi jumlah permintaan metadata yang berulang dan mempercepat proses interaksi bot dengan grup secara signifikan.

### Meningkatkan Sistem Retry & Mendekripsi Suara Polling

- Jika Anda ingin meningkatkan pengiriman pesan, melakukan retry otomatis saat terjadi error, dan mendekripsi hasil polling, Anda perlu menggunakan `store` dan mengatur opsi `getMessage` pada socket seperti berikut:

```javascript
const sock = makeWASocket({
  getMessage: async (key) => await getMessageFromStore(key)
})
```

### Menerima Notifikasi di Aplikasi WhatsApp

- Untuk tetap menerima notifikasi di aplikasi WhatsApp saat socket aktif, atur `markOnlineOnConnect` ke `false`:

```javascript
const sock = makeWASocket({
  markOnlineOnConnect: false
})
```

## Menangani Event

- Baileys menggunakan pola `EventEmitter` untuk menangani berbagai peristiwa (*event*).  
Seluruh event telah diketik (typed) dengan baik, sehingga editor seperti **VS Code** akan memberikan dukungan Intellisense secara optimal.

> [!IMPORTANT]  
> **Daftar lengkap event tersedia [di sini](https://baileys.whiskeysockets.io/types/BaileysEventMap.html)**.  
> Sangat penting untuk memahami setiap event yang bisa digunakan.

Contoh penggunaan listener untuk menangani pesan masuk:

```javascript
import makeWASocket from 'naruyaizumi'

const sock = makeWASocket()

sock.ev.on('messages.upsert', ({ messages }) => {
  console.log('Pesan diterima:', messages)
})
```

## Menyimpan & Memulihkan Sesi

Tentu Anda tidak ingin terus-menerus memindai QR code setiap kali ingin terkoneksi.

Anda bisa menyimpan kredensial dan menggunakannya kembali saat login berikutnya:

```javascript
import makeWASocket, { useMultiFileAuthState } from 'naruyaizumi'

const { state, saveCreds } = await useMultiFileAuthState('auth_info_baileys')

// Akan menggunakan kredensial yang tersedia untuk koneksi ulang
// Jika kredensial valid ditemukan, akan langsung login tanpa QR
const sock = makeWASocket({ auth: state })

// Fungsi ini akan dipanggil setiap kali kredensial diperbarui
sock.ev.on('creds.update', saveCreds)
```

> [!IMPORTANT]  
> `useMultiFileAuthState` adalah fungsi utilitas untuk menyimpan state autentikasi dalam satu folder.  
> Fungsi ini juga dapat dijadikan dasar untuk menulis sistem autentikasi dan penyimpanan kunci pada database SQL atau NoSQL — sangat direkomendasikan untuk sistem berskala produksi.

> [!NOTE]  
> Ketika pesan dikirim/diterima, sistem Signal dapat menyebabkan update pada kunci autentikasi (`authState.keys`).  
> Setiap kali terjadi perubahan, **kunci tersebut wajib disimpan kembali**.  
> Jika tidak, pesan bisa gagal terkirim dan menyebabkan masalah tak terduga.  
> `useMultiFileAuthState` sudah menangani hal ini secara otomatis. Namun jika Anda membuat sistem penyimpanan sendiri, **pastikan manajemen state-nya ditangani dengan hati-hati**.

### Contoh untuk Memulai

> [!NOTE]  
> Contoh ini juga sudah mencakup penyimpanan kredensial secara otomatis

```javascript
import makeWASocket, { DisconnectReason, useMultiFileAuthState } from 'naruyaizumi'
import { Boom } from '@hapi/boom'

async function connectToWhatsApp () {
  const { state, saveCreds } = await useMultiFileAuthState('./auth_info_baileys')

  const sock = makeWASocket({
    auth: state,
    printQRInTerminal: true
  })

  sock.ev.on('connection.update', (update) => {
    const { connection, lastDisconnect } = update

    if (connection === 'close') {
      const shouldReconnect = (lastDisconnect.error as Boom)?.output?.statusCode !== DisconnectReason.loggedOut
      console.log('Koneksi terputus karena', lastDisconnect.error, ', mencoba sambung ulang:', shouldReconnect)

      // reconnect jika tidak logout permanen
      if (shouldReconnect) {
        connectToWhatsApp()
      }
    } else if (connection === 'open') {
      console.log('Koneksi berhasil dibuka')
    }
  })

  sock.ev.on('messages.upsert', async (event) => {
    for (const m of event.messages) {
      console.log(JSON.stringify(m, undefined, 2))

      console.log('Membalas ke', m.key.remoteJid)
      await sock.sendMessage(m.key.remoteJid!, { text: 'Hello Word' })
    }
  })

  // Menyimpan kredensial setiap kali diperbarui
  sock.ev.on('creds.update', saveCreds)
}

// Jalankan fungsi utama
connectToWhatsApp()
```

### Contoh Penggunaan `useSingleFileAuthState` dan `useMongoFileAuthState`

```javascript
import makeWASocket, {
  useSingleFileAuthState,
  useMongoFileAuthState
} from 'naruyaizumi'

// Autentikasi menggunakan file tunggal (Single File Auth)
const { state, saveState } = await useSingleFileAuthState('./auth_info_baileys.json')
const sock = makeWASocket({
  auth: state,
  printQRInTerminal: true
})

sock.ev.on('creds.update', saveState)


// Autentikasi menggunakan MongoDB
import { MongoClient } from 'mongodb'

const connectAuth = async () => {
  global.client = new MongoClient('mongoURL')

  global.client.connect(err => {
    if (err) {
      console.warn('Peringatan: Link MongoDB tidak valid atau gagal terhubung.')
    } else {
      console.log('Berhasil terhubung ke server MongoDB')
    }
  })
}

await client.connect()
const collection = client.db('naruyaizumi').collection('sessions')
const Authentication = await useMongoFileAuthState(collection)

const { state, saveCreds } = Authentication
const sock = makeWASocket({
  auth: state,
  printQRInTerminal: true
})

sock.ev.on('creds.update', saveCreds)
```

> [!IMPORTANT]  
> Dalam event `messages.upsert`, sangat disarankan menggunakan perulangan `for (const message of event.messages)` untuk menangani semua pesan dalam array secara individual.

### Mendekripsi Suara Polling

Secara default, suara polling di WhatsApp dienkripsi dan diproses melalui event `messages.update`.

```javascript
import pino from 'pino'
import {
  makeInMemoryStore,
  getAggregateVotesInPollMessage
} from 'naruyaizumi'

const logger = pino({ timestamp: () => `,"time":"${new Date().toJSON()}"` }).child({ class: 'NaruyaIzumi' })
logger.level = 'fatal'
const store = makeInMemoryStore({ logger })

async function getMessage(key) {
  if (store) {
    const msg = await store.loadMessage(key.remoteJid, key.id)
    return msg?.message
  }
  return {
    conversation: 'Polling Tidak Ditemukan'
  }
}

sock.ev.on('messages.update', async (chatUpdate) => {
  for (const { key, update } of chatUpdate) {
    if (update.pollUpdates && key.fromMe) {
      const pollCreation = await getMessage(key)
      if (pollCreation) {
        const pollUpdate = await getAggregateVotesInPollMessage({
          message: pollCreation,
          pollUpdates: update.pollUpdates
        })
        const toCmd = pollUpdate.filter(v => v.voters.length !== 0)[0]?.name
        if (!toCmd) return
        console.log(toCmd)
      }
    }
  }
})
```

Fungsi `getAggregateVotesInPollMessage()` akan membantu mendekripsi hasil polling dan mengagregasi suara berdasarkan pemilih. Ini berguna untuk sistem voting interaktif melalui grup WhatsApp.

### Ringkasan Event Saat Koneksi Pertama

1. Saat pertama kali Anda terhubung, event `connection.update` akan dipicu — biasanya meminta untuk merestart socket.
2. Setelah itu, riwayat pesan akan diterima melalui event `messaging.history-set`.

## Mengimplementasikan Data Store

- Baileys tidak menyediakan sistem penyimpanan (*storage*) bawaan untuk chat, kontak, atau pesan.  
Namun, tersedia implementasi sederhana menggunakan **in-memory store**. Store ini akan memantau pembaruan chat, pesan baru, dan perubahan lainnya agar data Anda tetap mutakhir.

> [!IMPORTANT]  
> Sangat disarankan untuk membangun sistem penyimpanan sendiri.  
> Menyimpan seluruh riwayat chat di RAM akan memakan memori yang besar dan tidak efisien untuk jangka panjang.

Berikut contoh penggunaan store:

```javascript
import makeWASocket, { makeInMemoryStore } from 'naruyaizumi'

// Store akan menyimpan data koneksi WhatsApp dalam memori
const store = makeInMemoryStore({})

// Membaca data dari file (jika tersedia)
store.readFromFile('./baileys_store.json')

// Menyimpan state ke file setiap 10 detik
setInterval(() => {
  store.writeToFile('./baileys_store.json')
}, 10_000)

const sock = makeWASocket({})

// Store akan mulai mendengarkan event dari socket ini
// Jika socket diganti, store masih bisa digunakan ulang
store.bind(sock.ev)

sock.ev.on('chats.upsert', () => {
  // Akses semua chat tersimpan
  console.log('Data chat diterima:', store.chats.all())
})

sock.ev.on('contacts.upsert', () => {
  // Akses semua kontak tersimpan
  console.log('Kontak diperbarui:', Object.values(store.contacts))
})
```

Store ini juga menyediakan beberapa fungsi tambahan seperti `loadMessages` untuk mempercepat pengambilan data dari cache internal.

## Penjelasan Tentang WhatsApp ID

- `id` atau biasa disebut juga `jid` adalah **identitas WhatsApp** dari seseorang atau grup yang menjadi tujuan pengiriman pesan.  
- Format ID harus sesuai dengan jenis akun tujuan:

    - **Untuk pengguna pribadi:**  
      Format:  
      ```
      [kode negara][nomor telepon]@s.whatsapp.net
      ```
      Contoh:  
      ```
      6283143663697@s.whatsapp.net
      ```

    - **Untuk grup:**  
      Format:  
      ```
      [timestamp grup dibuat]-[random id]@g.us
      ```
      Contoh:  
      ```
      1234567890-987654321@g.us
      ```

    - **Untuk daftar siaran (broadcast):**  
      Format:  
      ```
      [timestamp]@broadcast
      ```

    - **Untuk status/story:**  
      Format:  
      ```
      status@broadcast
      ```

## Fungsi Utilitas (Utility Functions)

Beberapa fungsi penting yang tersedia di Baileys:

- `getContentType` – Mengembalikan jenis konten dari sebuah pesan  
- `getDevice` – Menentukan jenis perangkat pengirim pesan  
- `makeCacheableSignalKeyStore` – Meningkatkan performa autentikasi dengan membuat key store yang dapat di-cache  
- `downloadContentFromMessage` – Mengunduh konten (media/file) dari pesan apa pun  

## Mengirim Pesan

- Semua jenis pesan dapat dikirim menggunakan **satu fungsi saja**, yaitu `sendMessage()`.  
- Lihat daftar jenis pesan yang didukung [di sini](https://baileys.whiskeysockets.io/types/AnyMessageContent.html)  
- Dan semua opsi pengiriman pesan [di sini](https://baileys.whiskeysockets.io/types/MiscMessageGenerationOptions.html)

Contoh:

```javascript
const jid = '6283143663697@s.whatsapp.net' // tujuan
const content = { text: 'Halo, ini pesan dari bot!' } // isi pesan
const options = { quoted: null } // opsi tambahan (misalnya: balasan)

await sock.sendMessage(jid, content, options)
```

### Pesan Non-Media

#### Pesan Teks
```javascript
await sock.sendMessage(jid, { text: 'Halo dunia' })
```

#### Pesan Balasan (Quote)
```javascript
await sock.sendMessage(jid, { text: 'Ini balasan pesan kamu' }, { quoted: message })
```

#### Mention Pengguna (Tag)
- Gunakan `@nomor` dalam teks dan sertakan `mentions` di payload.
```javascript
await sock.sendMessage(
  jid,
  {
    text: '@6283143663697 Hai Naruya!',
    mentions: ['6283143663697@s.whatsapp.net']
  }
)
```

#### Meneruskan Pesan (Forward)
- Butuh objek pesan (`WAMessage`). Bisa didapat dari store atau pesan sebelumnya.
```javascript
const msg = getMessageFromStore() // Anda buat sendiri sesuai struktur
await sock.sendMessage(jid, { forward: msg, force: true }) // Pesan diteruskan
```

#### Lokasi Biasa
```javascript
await sock.sendMessage(
  jid,
  {
    location: {
      degreesLatitude: -6.200000,
      degreesLongitude: 106.816666
    }
  }
)
```

#### Lokasi Langsung (Live Location)
```javascript
await sock.sendMessage(
  jid,
  {
    location: {
      degreesLatitude: -6.200000,
      degreesLongitude: 106.816666
    },
    live: true
  }
)
```

#### Kirim Kontak (vCard)
```javascript
const vcard =
  'BEGIN:VCARD\n' +
  'VERSION:3.0\n' +
  'FN:Naruya Izumi\n' +
  'ORG:ZERO DEV;\n' +
  'TEL;type=CELL;type=VOICE;waid=6283143663697:+62 831-4366-3697\n' +
  'END:VCARD'

await sock.sendMessage(
  jid,
  {
    contacts: {
      displayName: 'Naruya Izumi',
      contacts: [{ vcard }]
    }
  }
)
```

#### Pesan Reaksi (Reaction Message)

- Anda perlu mengirimkan `key` dari pesan yang ingin diberikan reaksi.  
  `key` bisa diambil dari [store](#mengimplementasikan-data-store) atau menggunakan [WAMessageKey](https://baileys.whiskeysockets.io/types/WAMessageKey.html).

```javascript
await sock.sendMessage(
  jid,
  {
    react: {
      text: '🔥', // gunakan string kosong '' untuk menghapus reaksi
      key: message.key
    }
  }
)
```

#### Pin Pesan (Pin Message)

- Anda juga perlu memberikan `key` dari pesan yang ingin dipin.  
  Anda dapat mengatur durasi pin berdasarkan waktu dalam detik.

| Durasi | Detik        |
|--------|--------------|
| 24 jam | 86.400       |
| 7 hari | 604.800      |
| 30 hari| 2.592.000    |

```javascript
await sock.sendMessage(
  jid,
  {
    pin: {
      type: 1, // 1 untuk pin, 2 untuk unpin
      time: 86400,
      key: message.key
    }
  }
)
```

### Menandai Pesan (Keep Message)

- Untuk menyimpan pesan tertentu agar tidak terhapus otomatis.

```javascript
await sock.sendMessage(
  jid,
  {
    keep: {
      key: message.key,
      type: 1 // 1 = simpan, 2 = batalkan simpan
    }
  }
)
```

#### Pesan Polling (Poll Message)

- Kirim polling ke grup atau kontak pribadi. Dapat menentukan apakah polling bersifat publik (announcement group).

```javascript
await sock.sendMessage(
  jid,
  {
    poll: {
      name: 'Polling Hari Ini',
      values: ['Opsi A', 'Opsi B', 'Opsi C'],
      selectableCount: 1,
      toAnnouncementGroup: false
    }
  }
)
```

#### Pesan Hasil Polling (Poll Result)

- Kirim hasil polling secara manual jika dibutuhkan. Cocok untuk sistem polling terintegrasi.

```javascript
await sock.sendMessage(
  jid,
  {
    pollResult: {
      name: 'Hasil Polling',
      values: [
        ['Opsi A', 120],
        ['Opsi B', 350],
        ['Opsi C', 75]
      ]
    }
  }
)
```

### Pesan Panggilan (Call Message)

- Digunakan untuk mengirim notifikasi panggilan, bisa suara atau video.

```javascript
await sock.sendMessage(
  jid,
  {
    call: {
      name: 'Hay',
      type: 1 // 1 = suara, 2 = video
    }
  }
)
```

### Pesan Event (Event Message)

- Cocok untuk mengumumkan acara atau undangan dengan detail lokasi dan waktu.

```javascript
await sock.sendMessage(
  jid,
  {
    event: {
      isCanceled: false, // true jika dibatalkan
      name: 'Liburan Bareng!',
      description: 'Siapa yang mau ikut?', 
      location: {
        degreesLatitude: 24.121231,
        degreesLongitude: 55.1121221,
        name: 'Pantai Sanur'
      },
      startTime: 1715000000, 
      endTime: 1715086400, 
      extraGuestsAllowed: true // apakah boleh bawa tamu
    }
  }
)
```

### Pesan Pemesanan (Order Message)

- Digunakan untuk menampilkan detail pemesanan dari katalog bisnis WhatsApp.

```javascript
await sock.sendMessage(
  jid,
  {
    order: {
      orderId: '574XXX',
      thumbnail: 'your_thumbnail', 
      itemCount: 3,
      status: 'INQUIRY', // atau ACCEPTED / DECLINED
      surface: 'CATALOG',
      message: 'Deskripsi pesanan',
      orderTitle: 'Judul Pesanan',
      sellerJid: '628xxx@s.whatsapp.net',
      token: 'your_token',
      totalAmount1000: '150000',
      totalCurrencyCode: 'IDR'
    }
  }
)
```

### Pesan Produk (Product Message)

- Menampilkan detail produk dari katalog bisnis.

```javascript
await sock.sendMessage(
  jid,
  {
    product: {
      productImage: { 
        url: 'https://your-image.url/image.jpg'
      },
      productId: 'PRD-001', 
      title: 'Produk Spesial',
      description: 'Deskripsi lengkap produk kamu di sini', 
      currencyCode: 'IDR', 
      priceAmount1000: '50000', 
      retailerId: 'store-izumi', // opsional
      url: 'https://linkproduk.com', // opsional
      productImageCount: 1, 
      firstImageId: 'img-001', // opsional
      salePriceAmount1000: '45000', 
      signedUrl: 'https://your.signed.url' // opsional
    },
    businessOwnerJid: '628xxx@s.whatsapp.net'
  }
)
```

### Pesan Pembayaran (Payment Message)

- Digunakan untuk mengirimkan informasi pembayaran, cocok untuk chatbot belanja.

```javascript
await sock.sendMessage(
  jid,
  {
    payment: {
      note: 'Hi!',
      currency: 'IDR',
      offset: 0,
      amount: '10000',
      expiry: 0,
      from: '628xxxx@s.whatsapp.net',
      image: {
        placeholderArgb: '#222222', 
        textArgb: '#FFFFFF',  
        subtextArgb: '#AAAAAA'
      }
    }
  }
)
```

#### Pesan Undangan Pembayaran (Payment Invite Message)

- Digunakan untuk mengundang pengguna lain melakukan pembayaran.

```javascript
await sock.sendMessage(
  jid, 
  { 
    paymentInvite: {
      type: 1, // 1 = request, 2 = accept, 3 = decline (sesuaikan sesuai konteks)
      expiry: 0
    }
  }
)
```

### Pesan Undangan Admin Channel (Admin Invite Message)

- Meminta pengguna untuk menjadi admin di saluran (newsletter) Anda.

```javascript
await sock.sendMessage(
  jid,
  {
    adminInvite: {
      jid: '123xxx@newsletter',
      name: 'Channel Naruya',
      caption: 'Tolong jadi admin channel saya ya!',
      expiration: 86400, // dalam detik (24 jam)
      jpegThumbnail: Buffer // opsional, bisa berupa buffer gambar
    }
  }
)
```

### Undangan Grup WhatsApp (Group Invite Message)

- Mengirim undangan ke grup tertentu menggunakan kode undangan.

```javascript
await sock.sendMessage(
  jid,
  {
    groupInvite: {
      jid: '123xxx@g.us',
      name: 'Grup Dev Naruya',
      caption: 'Ayo gabung ke grup WhatsApp kami!',
      code: 'ABCD1234', // kode undangan grup
      expiration: 86400,
      jpegThumbnail: Buffer // opsional
    }
  }
)
```

### Pesan Bagikan Nomor Telepon (Share Phone Number)

- Mengirim permintaan eksplisit untuk membagikan nomor telepon pengguna.

```javascript
await sock.sendMessage(
  jid,
  {
    sharePhoneNumber: {}
  }
)
```

### Pesan Permintaan Nomor Telepon (Request Phone Number)

- Meminta pengguna untuk membagikan nomor telepon mereka secara langsung.

```javascript
await sock.sendMessage(
  jid,
  {
    requestPhoneNumber: {}
  }
)
```

### Pesan Balasan Tombol (Button Reply Message)

- Digunakan untuk merespons interaksi tombol yang diklik pengguna. Tipe pesan dibedakan berdasarkan jenis tombol yang digunakan.

#### Tombol Tipe List
```javascript
await sock.sendMessage(
  jid,
  {
    buttonReply: {
      name: 'Hai', 
      description: 'Deskripsi pilihan', 
      rowId: 'pilihan_1'
    }, 
    type: 'list'
  }
)
```

#### Tombol Tipe Plain
```javascript
await sock.sendMessage(
  jid,
  {
    buttonReply: {
      displayText: 'Halo', 
      id: 'plain_id'
    }, 
    type: 'plain'
  }
)
```

#### Tombol Tipe Template
```javascript
await sock.sendMessage(
  jid,
  {
    buttonReply: {
      displayText: 'Pilih Saya', 
      id: 'template_id', 
      index: 1
    }, 
    type: 'template'
  }
)
```

#### Tombol Tipe Interactive (Native Flow)
```javascript
await sock.sendMessage(
  jid,
  {
    buttonReply: {
      body: 'Mau pilih yang mana?', 
      nativeFlows: {
        name: 'menu_options', 
        paramsJson: JSON.stringify({ id: 'menu_1', description: 'Deskripsi interaktif' }),
        version: 1 // bisa juga 2 atau 3
      }
    }, 
    type: 'interactive'
  }
)
```

### Pesan dengan Tombol (Buttons Message)

- Pesan biasa yang disertai hingga **3 tombol** untuk respon cepat.

```javascript
await sock.sendMessage(
  jid,
  {
    text: 'Ini adalah pesan tombol!',
    caption: 'Gunakan jika memakai gambar/video',
    footer: 'Salam dari Naruya Izumi!',
    buttons: [
      { 
        buttonId: 'btn1', 
        buttonText: { displayText: 'Tombol 1' }
      },
      { 
        buttonId: 'btn2', 
        buttonText: { displayText: 'Tombol 2' }
      },
      { 
        buttonId: 'btn3', 
        buttonText: { displayText: 'Tombol 3' }
      }
    ]
  }
)
```

### Pesan List Tombol (Buttons List Message)

- Hanya bisa digunakan di **chat pribadi**, bukan grup.

```javascript
await sock.sendMessage(
  jid,
  {
    text: 'Ini adalah daftar pilihan!',
    footer: 'Dipersembahkan oleh Naruya Izumi',
    title: 'Judul Daftar Pilihan',
    buttonText: 'Klik untuk melihat opsi',
    sections: [
      {
        title: 'Bagian 1',
        rows: [
          { title: 'Opsi 1', rowId: 'opsi1' },
          { title: 'Opsi 2', rowId: 'opsi2', description: 'Deskripsi Opsi 2' }
        ]
      },
      {
        title: 'Bagian 2',
        rows: [
          { title: 'Opsi 3', rowId: 'opsi3' },
          { title: 'Opsi 4', rowId: 'opsi4', description: 'Deskripsi Opsi 4' }
        ]
      }
    ]
  }
)
```

### Pesan Daftar Produk dengan Tombol (Buttons Product List Message)

- Hanya dapat digunakan di **chat pribadi**, bukan grup.  
- Menampilkan daftar produk dari katalog bisnis WhatsApp Anda.

```javascript
await sock.sendMessage(
  jid,
  {
    text: 'Ini adalah daftar produk!',
    footer: 'Dikirim oleh Naruya Izumi',
    title: 'Pilih Produk Unggulan',
    buttonText: 'Lihat Daftar Produk',
    productList: [
      {
        title: 'Kategori Produk Utama',
        products: [
          { productId: '1234' },
          { productId: '5678' }
        ]
      }
    ],
    businessOwnerJid: '628xxx@s.whatsapp.net',
    thumbnail: 'https://example.jpg' // atau buffer gambar
  }
)
```

### Pesan Kartu dengan Tombol (Buttons Cards Message)

- Menampilkan beberapa kartu (card) interaktif dengan gambar atau video + tombol.

```javascript
await sock.sendMessage(
  jid,
  {
    text: 'Isi Utama Pesan',
    title: 'Judul Utama',
    subtile: 'Subjudul Opsional',
    footer: 'Footer Pesan',

    cards: [
      {
        image: { url: 'https://example.jpg' }, // bisa juga Buffer
        title: 'Judul Kartu',
        body: 'Isi Konten Kartu',
        footer: 'Footer Kartu',
        buttons: [
          {
            name: 'quick_reply',
            buttonParamsJson: JSON.stringify({
              display_text: 'Tombol Cepat',
              id: 'ID_TOMBOL_1'
            })
          },
          {
            name: 'cta_url',
            buttonParamsJson: JSON.stringify({
              display_text: 'Kunjungi Website',
              url: 'https://www.example.com'
            })
          }
        ]
      },
      {
        video: { url: 'https://example.mp4' }, // bisa juga Buffer video
        title: 'Judul Kartu Video',
        body: 'Deskripsi Konten',
        footer: 'Footer Kartu',
        buttons: [
          {
            name: 'quick_reply',
            buttonParamsJson: JSON.stringify({
              display_text: 'Respon Cepat',
              id: 'ID_TOMBOL_2'
            })
          },
          {
            name: 'cta_url',
            buttonParamsJson: JSON.stringify({
              display_text: 'Lihat Selengkapnya',
              url: 'https://www.example.com'
            })
          }
        ]
      }
    ]
  }
)
```

### Pesan Tombol Template (Buttons Template Message)

- Menampilkan tombol dengan tipe URL, panggilan, atau tombol balasan cepat.

```javascript
await sock.sendMessage(
  jid,
  {
    text: 'Ini adalah pesan template tombol!',
    footer: 'Dikirim oleh Naruya Izumi',
    templateButtons: [
      {
        index: 1,
        urlButton: {
          displayText: 'Ikuti Channel',
          url: 'https://whatsapp.com/channel/0029Vag9VSI2ZjCocqa2lB1y'
        }
      },
      {
        index: 2,
        callButton: {
          displayText: 'Hubungi Saya!',
          phoneNumber: '628xxxx'
        }
      },
      {
        index: 3,
        quickReplyButton: {
          displayText: 'Balas Cepat',
          id: 'id-button-reply'
        }
      }
    ]
  }
)
```

### Pesan Tombol Interaktif (Interactive Buttons)

- Mendukung berbagai jenis tombol dan dapat digunakan dengan media.

```javascript
await sock.sendMessage(
  jid,
  {
    text: 'Ini pesan interaktif!',
    title: 'Hai!',
    subtitle: 'Subjudul di sini',
    footer: 'Dikirim oleh Naruya Izumi',
    interactiveButtons: [
      {
        name: 'quick_reply',
        buttonParamsJson: JSON.stringify({
          display_text: 'Klik Aku!',
          id: 'id_anda'
        })
      },
      {
        name: 'cta_url',
        buttonParamsJson: JSON.stringify({
          display_text: 'Kunjungi Channel',
          url: 'https://whatsapp.com/channel/0029Vag9VSI2ZjCocqa2lB1y',
          merchant_url: 'https://whatsapp.com/channel/0029Vag9VSI2ZjCocqa2lB1y'
        })
      },
      {
        name: 'cta_copy',
        buttonParamsJson: JSON.stringify({
          display_text: 'Salin Link',
          copy_code: 'https://whatsapp.com/channel/0029Vag9VSI2ZjCocqa2lB1y'
        })
      },
      {
        name: 'cta_call',
        buttonParamsJson: JSON.stringify({
          display_text: 'Telepon Saya',
          phone_number: '628xxxx'
        })
      },
      {
        name: 'single_select',
        buttonParamsJson: JSON.stringify({
          title: 'Pilih Opsi',
          sections: [
            {
              title: 'Pilihan Utama',
              highlight_label: 'Rekomendasi',
              rows: [
                {
                  header: 'Header 1',
                  title: 'Opsi 1',
                  description: 'Deskripsi 1',
                  id: 'id1'
                },
                {
                  header: 'Header 2',
                  title: 'Opsi 2',
                  description: 'Deskripsi 2',
                  id: 'id2'
                }
              ]
            }
          ]
        })
      }
    ]
  }
)
```

#### Versi dengan Media

##### Gambar
```javascript
await sock.sendMessage(
  jid,
  {
    image: { url: 'https://example.jpg' },
    caption: 'Isi Pesan',
    title: 'Judul',
    subtitle: 'Subjudul',
    footer: 'Footer',
    interactiveButtons: [ /* tombol seperti di atas */ ],
    hasMediaAttachment: false
  }
)
```

##### Video
```javascript
await sock.sendMessage(
  jid,
  {
    video: { url: 'https://example.mp4' },
    caption: 'Isi Video',
    title: 'Judul',
    subtitle: 'Subjudul',
    footer: 'Footer',
    interactiveButtons: [ /* tombol seperti di atas */ ],
    hasMediaAttachment: false
  }
)
```

##### Dokumen
```javascript
await sock.sendMessage(
  jid,
  {
    document: { url: 'https://example.jpg' },
    mimetype: 'image/jpeg',
    jpegThumbnail: await sock.resize('https://example.jpg', 320, 320),
    caption: 'Isi Dokumen',
    title: 'Judul',
    subtitle: 'Subjudul',
    footer: 'Footer',
    interactiveButtons: [ /* tombol seperti di atas */ ],
    hasMediaAttachment: false
  }
)
```

##### Lokasi
```javascript
await sock.sendMessage(
  jid,
  {
    location: {
      degreesLatitude: -6.2,
      degreesLongitude: 106.8,
      name: 'Naruya HQ'
    },
    caption: 'Ayo ke sini!',
    title: 'Lokasi Tujuan',
    subtitle: 'Subjudul Lokasi',
    footer: 'Peta lokasi',
    interactiveButtons: [ /* tombol seperti di atas */ ],
    hasMediaAttachment: false
  }
)
```

##### Produk
```javascript
await sock.sendMessage(
  jid,
  {
    product: {
      productImage: { url: 'https://example.jpg' },
      productId: '836xxx',
      title: 'Produk Pilihan',
      description: 'Deskripsi produk terbaik',
      currencyCode: 'IDR',
      priceAmount1000: '283000',
      retailerId: 'NaruyaStore',
      url: 'https://example.com',
      productImageCount: 1
    },
    businessOwnerJid: '628xxx@s.whatsapp.net',
    caption: 'Produk baru tersedia!',
    title: 'Nama Produk',
    subtitle: 'Subjudul Produk',
    footer: 'Info Produk',
    interactiveButtons: [ /* tombol seperti di atas */ ],
    hasMediaAttachment: false
  }
)
```

### Mention Status (Status Mentions Message)

- Digunakan untuk membuat status WhatsApp yang menyebut seseorang secara langsung.

```javascript
await sock.sendStatusMentions(
  jid, 
  {
    image: {
      url: 'https://example.com.jpg'
    }, 
    caption: 'Halo dari Naruya!'
  }
)
```

### Pesan Album (Send Album Message)

- Mengirim beberapa gambar atau video sebagai album (sekuens media). Bisa pakai `Buffer` atau URL.

```javascript
await sock.sendAlbumMessage(
  jid,
  [
    {
      image: { url: 'https://example.jpg' }, 
      caption: 'Gambar 1'
    },
    {
      image: Buffer, 
      caption: 'Gambar 2'
    },
    {
      video: { url: 'https://example.mp4' }, 
      caption: 'Video 1'
    }, 
    {
      video: Buffer, 
      caption: 'Video 2'
    }
  ],
  { 
    quoted: message, // opsional, untuk membalas pesan
    delay: 2000 // jeda antar media (ms)
  }
)
```

### Pesan Toko (Shop Message)

- Digunakan untuk mengarahkan pengguna ke katalog atau produk dalam fitur bisnis WhatsApp.

#### Teks Saja
```javascript
await sock.sendMessage(
  jid, 
  {      
    text: 'Body pesan',
    title: 'Judul Toko', 
    subtitle: 'Subjudul', 
    footer: 'Powered by Naruya',
    shop: {
      surface: 1,
      id: 'https://example.com'
    }, 
    viewOnce: true
  }
)
```

#### Gambar
```javascript
await sock.sendMessage(
  jid, 
  { 
    image: { url: 'https://example.jpg' },
    caption: 'Deskripsi produk',
    title: 'Judul',
    subtitle: 'Subjudul',
    footer: 'Footer',
    shop: {
      surface: 1,
      id: 'https://example.com'
    },
    hasMediaAttachment: false,
    viewOnce: true
  }
)
```

#### Video
```javascript
await sock.sendMessage(
  jid, 
  { 
    video: { url: 'https://example.mp4' },
    caption: 'Tonton videonya!',
    title: 'Judul Video',
    subtitle: 'Subjudul',
    footer: 'Footer',
    shop: {
      surface: 1,
      id: 'https://example.com'
    },
    hasMediaAttachment: false,
    viewOnce: true
  }
)
```

#### Dokumen
```javascript
await sock.sendMessage(
  jid, 
  {
    document: { url: 'https://example.jpg' },
    mimetype: 'image/jpeg',
    jpegThumbnail: await sock.resize('https://example.jpg', 320, 320),
    caption: 'Lampiran dokumen',
    title: 'Judul',
    subtitle: 'Subjudul',
    footer: 'Footer',
    shop: {
      surface: 1,
      id: 'https://example.com'
    },
    hasMediaAttachment: false,
    viewOnce: true
  }
)
```

#### Lokasi
```javascript
await sock.sendMessage(
  jid, 
  { 
    location: {
      degreesLatitude: -6.2000, 
      degreesLongitude: 106.8166,
      name: 'Lokasi Toko'
    },
    caption: 'Lihat lokasi kami!',
    title: 'Judul Lokasi',
    subtitle: 'Subjudul',
    footer: 'Peta lokasi',
    shop: {
      surface: 1,
      id: 'https://example.com'
    },
    hasMediaAttachment: false,
    viewOnce: true
  }
)
```

#### Produk
```javascript
await sock.sendMessage(
  jid,
  {
    product: {
      productImage: { url: 'https://example.jpg' },
      productId: '836xxx',
      title: 'Nama Produk',
      description: 'Deskripsi produk menarik',
      currencyCode: 'IDR',
      priceAmount1000: '283000',
      retailerId: 'NaruyaStore',
      url: 'https://example.com',
      productImageCount: 1
    },
    businessOwnerJid: '628xxx@s.whatsapp.net',
    caption: 'Lihat produk unggulan kami!',
    title: 'Judul Produk',
    subtitle: 'Subjudul Produk',
    footer: 'Info produk',
    shop: {
      surface: 1,
      id: 'https://example.com'
    },
    hasMediaAttachment: false,
    viewOnce: true
  }
)
```

### Pesan Koleksi (Collection Message)

- Fitur ini digunakan untuk menampilkan koleksi katalog dari bisnis tertentu di WhatsApp.

#### Teks Saja
```javascript
await sock.sendMessage(
  jid, 
  {
    text: 'Isi pesan',
    title: 'Judul Koleksi',
    subtitle: 'Subjudul',
    footer: 'Dari Naruya Izumi',
    collection: {
      bizJid: '628xxx@s.whatsapp.net', 
      id: 'https://example.com', 
      version: 1
    },
    viewOnce: true
  }
)
```

#### Gambar
```javascript
await sock.sendMessage(
  jid, 
  { 
    image: { url: 'https://example.jpg' },
    caption: 'Koleksi Gambar',
    title: 'Judul Koleksi',
    subtitle: 'Subjudul',
    footer: 'Katalog Naruya',
    collection: {
      bizJid: '628xxx@s.whatsapp.net', 
      id: 'https://example.com',
      version: 1
    },
    hasMediaAttachment: false,
    viewOnce: true
  }
)
```

#### Video
```javascript
await sock.sendMessage(
  jid, 
  {
    video: { url: 'https://example.mp4' },
    caption: 'Koleksi Video',
    title: 'Judul Video',
    subtitle: 'Subjudul',
    footer: 'Video Katalog',
    collection: {
      bizJid: '628xxx@s.whatsapp.net', 
      id: 'https://example.com',
      version: 1
    },
    hasMediaAttachment: false,
    viewOnce: true
  }
)
```

#### Dokumen
```javascript
await sock.sendMessage(
  jid, 
  {
    document: { url: 'https://example.jpg' },
    mimetype: 'image/jpeg',
    jpegThumbnail: await sock.resize('https://example.jpg', 320, 320),
    caption: 'Dokumen Katalog',
    title: 'Judul Dokumen',
    subtitle: 'Subjudul',
    footer: 'Lampiran Koleksi',
    collection: {
      bizJid: '628xxx@s.whatsapp.net',
      id: 'https://example.com',
      version: 1
    },
    hasMediaAttachment: false,
    viewOnce: true
  }
)
```

#### Lokasi
```javascript
await sock.sendMessage(
  jid, 
  {
    location: {
      degreesLatitude: -6.2, 
      degreesLongitude: 106.8,
      name: 'Lokasi Bisnis'
    },
    caption: 'Lihat lokasi koleksi',
    title: 'Judul Lokasi',
    subtitle: 'Subjudul',
    footer: 'Lokasi Katalog',
    collection: {
      bizJid: '628xxx@s.whatsapp.net',
      id: 'https://example.com',
      version: 1
    },
    hasMediaAttachment: false,
    viewOnce: true
  }
)
```

#### Produk
```javascript
await sock.sendMessage(
  jid,
  {
    product: {
      productImage: { url: 'https://example.jpg' },
      productId: '836xxx',
      title: 'Nama Produk',
      description: 'Deskripsi produk',
      currencyCode: 'IDR',
      priceAmount1000: '283000',
      retailerId: 'NaruyaStore',
      url: 'https://example.com',
      productImageCount: 1
    },
    businessOwnerJid: '628xxx@s.whatsapp.net',
    caption: 'Koleksi Produk',
    title: 'Judul',
    subtitle: 'Subjudul',
    footer: 'Produk Katalog',
    collection: {
      bizJid: '628xxx@s.whatsapp.net',
      id: 'https://example.com',
      version: 1
    },
    hasMediaAttachment: false,
    viewOnce: true
  }
)
```

### Mengirim Pesan dengan Pratinjau Link (Link Preview)

1. Secara default, WhatsApp Web tidak menampilkan pratinjau link.
2. Namun, Baileys menyediakan fungsi pembangkit preview link otomatis.
3. Untuk mengaktifkannya, install dulu dependensinya dengan:  
   ```bash
   yarn add link-preview-js
   ```
4. Contoh kirim pesan dengan pratinjau link:
```javascript
await sock.sendMessage(
  jid,
  {
    text: 'Hai! Ini dikirim dari https://github.com/whiskeysockets/baileys'
  }
)
```

### Pesan Media (Media Messages)

Mengirim media (gambar, video, audio, stiker) jauh lebih efisien dengan Baileys.

> [!NOTE]  
> Anda bisa menggunakan `Buffer`, `{ stream }`, atau `{ url }`.  
> Lihat lebih lengkap di [dokumentasi media](https://baileys.whiskeysockets.io/types/WAMediaUpload.html)

> [!TIP]  
> Gunakan **stream** atau **url langsung** agar lebih hemat memori.

#### Pesan GIF (video pendek)

> WhatsApp tidak mendukung file `.gif`, maka harus dikirim dalam bentuk `.mp4` dengan flag `gifPlayback: true`

```javascript
await sock.sendMessage(
  jid,
  {
    video: fs.readFileSync('Media/ma_gif.mp4'),
    caption: 'Halo dari GIF!',
    gifPlayback: true
  }
)
```

#### Pesan Video
```javascript
await sock.sendMessage(
  jid,
  {
    video: { url: './Media/ma_video.mp4' },
    caption: 'Ini videonya'
  }
)
```

#### Pesan Video PTV (Picture to Video / video bulat WA)

```javascript
await sock.sendMessage(
  jid,
  {
    video: { url: './Media/ma_video.mp4' },
    ptv: true
  }
)
```

#### Pesan Audio

> Agar audio kompatibel di semua perangkat, sebaiknya gunakan `ffmpeg` dengan pengaturan berikut:

```bash
ffmpeg -i input.mp4 -avoid_negative_ts make_zero -ac 1 output.ogg
```

```javascript
await sock.sendMessage(
  jid,
  {
    audio: { url: './Media/audio.ogg' },
    mimetype: 'audio/ogg; codecs=opus'
  }
)
```

#### Pesan Gambar

```javascript
await sock.sendMessage(
  jid,
  {
    image: { url: './Media/ma_img.png' },
    caption: 'Halo dari gambar!'
  }
)
```

#### Pesan View Once

> Fitur **View Once** memungkinkan media hanya bisa dilihat satu kali.

```javascript
await sock.sendMessage(
  jid,
  {
    image: { url: './Media/ma_img.png' },
    viewOnce: true,
    caption: 'Media hanya bisa dilihat sekali'
  }
)
```

## Memodifikasi Pesan

### Menghapus Pesan (Untuk Semua Orang)

- Digunakan untuk menarik pesan yang sudah dikirim (delete for everyone).

```javascript
const msg = await sock.sendMessage(jid, { text: 'Halo dunia' })
await sock.sendMessage(jid, { delete: msg.key })
```

> **Catatan:**  
> Untuk menghapus pesan **hanya untuk diri sendiri**, gunakan `chatModify` (lihat bagian [Modifikasi Chat](#modifying-chats)).

### Mengedit Pesan

- Anda dapat mengedit isi pesan yang telah dikirim sebelumnya, selama masih berada dalam konteks yang diizinkan oleh WhatsApp.

```javascript
await sock.sendMessage(jid, {
  text: 'Teks yang sudah diperbarui di sini',
  edit: response.key
})
```

## Memanipulasi Pesan Media

### Menambahkan Thumbnail pada Media

- Thumbnail (gambar pratinjau) untuk **gambar** dan **stiker** bisa dihasilkan secara otomatis jika Anda menambahkan salah satu dari dependency berikut:

```bash
yarn add jimp
# atau
yarn add sharp
```

- Untuk **video**, Anda juga bisa menghasilkan thumbnail otomatis, tapi pastikan Anda sudah install `ffmpeg` di sistem Anda.

> Contoh penggunaan otomatis biasanya tidak perlu Anda atur manual — Baileys akan meng-generate thumbnail bila dependensi sudah tersedia.

### Mengunduh Media dari Pesan (Downloading Media Messages)

Jika kamu ingin menyimpan media yang diterima dari pengguna:

```javascript
import { createWriteStream } from 'fs'
import { downloadMediaMessage, getContentType } from 'naruyaizumi'

sock.ev.on('messages.upsert', async ({ messages }) => {
let m = messages[0]
if (!m.message) return // jika tidak ada media atau isi pesan

let messageType = getContentType(m.message) // deteksi tipe pesan (image, video, audio, dll)

if (messageType === 'imageMessage') {
let stream = await downloadMediaMessage(
m,
'stream', // bisa juga 'buffer' kalau ingin langsung di-handle tanpa file
{},
{
logger,
reuploadRequest: sock.updateMediaMessage // agar bisa reupload jika file sudah tidak ada
}
)

let file = createWriteStream('./downloaded-image.jpeg')
stream.pipe(file)
}
})
```

### Re-upload Media ke WhatsApp

Jika media sudah dihapus dari server WhatsApp, kamu bisa minta perangkat pengirim untuk melakukan *reupload*:

```javascript
await sock.updateMediaMessage(msg)
```

> Fitur ini penting saat media gagal diunduh karena sudah tidak tersedia di server WhatsApp.

## Menolak Panggilan (Reject Call)

- Kamu bisa mendapatkan `callId` dan `callFrom` dari event `call`.

```javascript
await sock.rejectCall(callId, callFrom)
```

## Mengirim Status ke Chat (Send States in Chat)

### Menandai Pesan Dibaca (Reading Messages)

- Kamu harus menandai pesan satu per satu menggunakan key dari `WAMessage`.
- Tidak bisa menandai seluruh chat sebagai terbaca secara langsung seperti di WhatsApp Web.

```javascript
const key = {
remoteJid: '628xxx@s.whatsapp.net',
fromMe: false,
id: 'ABCDEF123456'
}

// bisa juga array untuk banyak pesan sekaligus
await sock.readMessages([key])
```

> Kamu bisa mendapatkan `messageID` dari:
```javascript
let messageID = message.key.id
```

### Memperbarui Status Kehadiran (Update Presence)

- Status `presence` bisa berupa:  
  `available`, `unavailable`, `composing`, `recording`, `paused`, dll.  
  [Lihat daftar lengkapnya di sini](https://baileys.whiskeysockets.io/types/WAPresence.html)

```javascript
await sock.sendPresenceUpdate('available', jid) // online
await sock.sendPresenceUpdate('composing', jid) // mengetik
await sock.sendPresenceUpdate('unavailable', jid) // offline
```

> **Catatan:**  
> Jika kamu menggunakan WhatsApp Desktop secara bersamaan, maka WA tidak akan mengirim notifikasi ke perangkat lain.  
> Kalau kamu ingin tetap terima notifikasi di HP, kamu bisa set status bot jadi offline:
```javascript
await sock.sendPresenceUpdate('unavailable')
```

## Memodifikasi Chat (Modifying Chats)

WhatsApp menggunakan komunikasi terenkripsi untuk memperbarui status chat atau aplikasi. Beberapa fitur modifikasi sudah didukung oleh Baileys, dan bisa kamu kirim seperti di bawah ini.

> **PERINGATAN:**  
> Jika kamu salah menggunakan modifikasi ini (misal kirim data invalid), WhatsApp bisa **logout semua perangkat** dan kamu harus scan ulang QR.

### Mengarsipkan Chat (Archive)

```javascript
let lastMsgInChat = await getLastMessageInChat(jid) // kamu buat fungsi ini sendiri
await sock.chatModify({ archive: true, lastMessages: [lastMsgInChat] }, jid)
```

### Membisukan / Mengaktifkan Notifikasi (Mute / Unmute)

| Durasi    | Milidetik       |
|-----------|------------------|
| Hapus     | `null`           |
| 8 Jam     | `86400000`       |
| 7 Hari    | `604800000`      |

```javascript
await sock.chatModify({ mute: 8 * 60 * 60 * 1000 }, jid) // bisukan 8 jam
await sock.chatModify({ mute: null }, jid) // aktifkan kembali notifikasi
```

### Tandai Sebagai Terbaca / Belum Dibaca

```javascript
let lastMsgInChat = await getLastMessageInChat(jid)
await sock.chatModify({ markRead: false, lastMessages: [lastMsgInChat] }, jid)
```

### Hapus Pesan Hanya untuk Saya

```javascript
await sock.chatModify(
  {
    clear: {
      messages: [
        {
          id: 'ATWYHDNNWU81732J',
          fromMe: true,
          timestamp: '1654823909'
        }
      ]
    }
  },
  jid
)
```

### Hapus Chat Secara Keseluruhan

```javascript
let lastMsgInChat = await getLastMessageInChat(jid)
await sock.chatModify({
  delete: true,
  lastMessages: [
    {
      key: lastMsgInChat.key,
      messageTimestamp: lastMsgInChat.messageTimestamp
    }
  ]
}, jid)
```

### Pin / Unpin Chat

```javascript
await sock.chatModify({
  pin: true // false untuk unpin
}, jid)
```

### Tandai / Hapus Bintang dari Pesan

```javascript
await sock.chatModify({
  star: {
    messages: [
      {
        id: 'messageID',
        fromMe: true
      }
    ],
    star: true // true: beri bintang, false: hapus bintang
  }
}, jid)
```

### Pesan Menghilang Otomatis (Disappearing Messages)

| Durasi    | Detik (Seconds) |
|-----------|------------------|
| Nonaktif  | `0`              |
| 24 Jam    | `86400`          |
| 7 Hari    | `604800`         |
| 90 Hari   | `7776000`        |

#### Aktifkan

```javascript
await sock.sendMessage(jid, {
  disappearingMessagesInChat: 604800 // 7 hari
})
```

#### Kirim Pesan dengan Mode Menghilang

```javascript
await sock.sendMessage(
  jid,
  { text: 'halo' },
  { ephemeralExpiration: 604800 }
)
```

#### Nonaktifkan

```javascript
await sock.sendMessage(jid, {
  disappearingMessagesInChat: false
})
```

### Menghapus Pesan Tertentu (Clear Messages)
```javascript
await sock.clearMessage(jid, key, timestamps)
```

## Query Pengguna (User Queries)

### Cek Apakah Nomor Terdaftar di WhatsApp
```javascript
let [result] = await sock.onWhatsApp(jid)
if (result.exists) console.log(`${jid} terdaftar di WhatsApp sebagai ${result.jid}`)
```

### Ambil Riwayat Chat (termasuk grup)

> Kamu perlu mengambil pesan paling lama dari chat tersebut

```javascript
let msg = await getOldestMessageInChat(jid)
await sock.fetchMessageHistory(
  50, // maksimal 50 per query
  msg.key,
  msg.messageTimestamp
)
```

- Hasilnya akan dikirimkan melalui event `messaging.history-set`

### Ambil Status WhatsApp (Bio)

```javascript
let status = await sock.fetchStatus(jid)
console.log('Status: ' + status)
```

### Ambil Foto Profil (Profil, Grup, Channel)

```javascript
let ppUrl = await sock.profilePictureUrl(jid)
console.log('Foto profil: ' + ppUrl)
```

### Ambil Profil Bisnis (Business Profile)

> Cocok untuk akun bisnis WhatsApp, seperti deskripsi & kategori bisnis

```javascript
let profile = await sock.getBusinessProfile(jid)
console.log('Deskripsi bisnis: ' + profile.description + ', Kategori: ' + profile.category)
```

### Cek Kehadiran Seseorang (Presence: Online / Typing)

```javascript
sock.ev.on('presence.update', console.log)
await sock.presenceSubscribe(jid)
```

## Ubah Profil

### Ubah Status Profil (Bio)

```javascript
await sock.updateProfileStatus('Halo Dunia!')
```

### Ubah Nama Profil

```javascript
await sock.updateProfileName('Naruya Izumi')
```

### Ubah Foto Profil (termasuk grup)

> Sama seperti pesan media, kamu bisa pakai:  
> `{ url }`, `Buffer`, atau `{ stream }`

```javascript
await sock.updateProfilePicture(jid, { url: './foto-baru.jpeg' })
```

### Hapus Foto Profil (termasuk grup)

```javascript
await sock.removeProfilePicture(jid)
```

## Grup WhatsApp (Groups)

> Untuk mengubah pengaturan grup, kamu harus menjadi admin grup tersebut.

### Membuat Grup
```javascript
let group = await sock.groupCreate('Grup Hebat Naruya', ['1234@s.whatsapp.net', '4564@s.whatsapp.net'])
console.log('Grup berhasil dibuat dengan ID: ' + group.gid)

await sock.sendMessage(group.id, { text: 'Halo semuanya!' })
```

### Tambah / Hapus / Jadikan Admin / Turunkan Admin

```javascript
await sock.groupParticipantsUpdate(
  jid,
  ['abcd@s.whatsapp.net', 'efgh@s.whatsapp.net'],
  'add' // bisa diganti: 'remove', 'promote', 'demote'
)
```

### Ubah Nama Grup

```javascript
await sock.groupUpdateSubject(jid, 'Nama Baru Grup!')
```

### Ubah Deskripsi Grup

```javascript
await sock.groupUpdateDescription(jid, 'Deskripsi baru untuk grup ini')
```

### Ubah Pengaturan Grup

```javascript
// hanya admin yang bisa kirim pesan
await sock.groupSettingUpdate(jid, 'announcement')

// semua anggota bisa kirim pesan
await sock.groupSettingUpdate(jid, 'not_announcement')

// semua anggota bisa ubah info grup (foto, nama, dll.)
await sock.groupSettingUpdate(jid, 'unlocked')

// hanya admin yang bisa ubah info grup
await sock.groupSettingUpdate(jid, 'locked')
```

### Keluar dari Grup

```javascript
await sock.groupLeave(jid)
```

### Dapatkan Kode Undangan Grup

```javascript
let code = await sock.groupInviteCode(jid)
console.log('Kode undangan grup: ' + code)
// gabung pakai: https://chat.whatsapp.com/ + code
```

### Reset / Ganti Kode Undangan Grup

```javascript
let newCode = await sock.groupRevokeInvite(jid)
console.log('Kode undangan baru: ' + newCode)
```

### Gabung Grup dengan Kode Undangan

```javascript
let response = await sock.groupAcceptInvite('ABC123DEF456')
console.log('Berhasil gabung ke grup: ' + response)
```

### Lihat Info Grup dari Kode Undangan

```javascript
let response = await sock.groupGetInviteInfo('ABC123DEF456')
console.log('Info grup: ', response)
```

### Lihat Metadata Grup (peserta, nama, deskripsi, dll.)

```javascript
let metadata = await sock.groupMetadata(jid)
console.log(metadata.id + ', Nama: ' + metadata.subject + ', Deskripsi: ' + metadata.desc)
```

### Gabung Grup dari `groupInviteMessage`

```javascript
let response = await sock.groupAcceptInviteV4(jid, groupInviteMessage)
console.log('Gabung ke grup: ' + response)
```

### Lihat Daftar Pengguna yang Minta Gabung

```javascript
let response = await sock.groupRequestParticipantsList(jid)
console.log(response)
```

### Setujui / Tolak Permintaan Gabung

```javascript
let response = await sock.groupRequestParticipantsUpdate(
  jid,
  ['abcd@s.whatsapp.net', 'efgh@s.whatsapp.net'],
  'approve' // atau 'reject'
)
console.log(response)
```

### Dapatkan Metadata Semua Grup yang Kamu Ikuti

```javascript
let allGroups = await sock.groupFetchAllParticipating()
console.log(allGroups)
```

### Aktifkan Pesan Sementara di Grup (Ephemeral Message)

| Durasi    | Detik (Seconds) |
|-----------|------------------|
| Nonaktif  | 0                |
| 24 Jam    | 86400            |
| 7 Hari    | 604800           |
| 90 Hari   | 7776000          |

```javascript
await sock.groupToggleEphemeral(jid, 86400) // contoh: aktif 1 hari
```

### Ubah Mode Penambahan Anggota Grup

```javascript
await sock.groupMemberAddMode(
  jid,
  'all_member_add' // atau 'admin_add'
)
```

## Privasi (Privacy)

### Blokir / Buka Blokir Pengguna

```javascript
await sock.updateBlockStatus(jid, 'block') // Blokir pengguna
await sock.updateBlockStatus(jid, 'unblock') // Buka blokir pengguna
```

### Ambil Pengaturan Privasi

```javascript
let privacySettings = await sock.fetchPrivacySettings(true)
console.log('Pengaturan privasi:', privacySettings)
```

### Lihat Daftar Blokir

```javascript
let blocklist = await sock.fetchBlocklist()
console.log(blocklist)
```

### Ubah Privasi Terakhir Dilihat (Last Seen)

```javascript
let value = 'all' // bisa juga: 'contacts', 'contact_blacklist', 'none'
await sock.updateLastSeenPrivacy(value)
```

### Ubah Privasi Status Online

```javascript
let value = 'all' // atau 'match_last_seen'
await sock.updateOnlinePrivacy(value)
```

### Ubah Privasi Foto Profil

```javascript
let value = 'all' // bisa juga: 'contacts', 'contact_blacklist', 'none'
await sock.updateProfilePicturePrivacy(value)
```

### Ubah Privasi Status WhatsApp

```javascript
let value = 'all' // bisa juga: 'contacts', 'contact_blacklist', 'none'
await sock.updateStatusPrivacy(value)
```

### Ubah Privasi Centang Biru (Read Receipts)

```javascript
let value = 'all' // atau 'none'
await sock.updateReadReceiptsPrivacy(value)
```

### Ubah Privasi Siapa yang Bisa Menambahkan ke Grup

```javascript
let value = 'all' // bisa juga: 'contacts', 'contact_blacklist'
await sock.updateGroupsAddPrivacy(value)
```

### Ubah Mode Default Pesan Sementara

Durasi dalam detik:

| Durasi    | Detik (Seconds) |
|-----------|------------------|
| Nonaktif  | 0                |
| 24 Jam    | 86400            |
| 7 Hari    | 604800           |
| 90 Hari   | 7776000          |

```javascript
let ephemeral = 86400
await sock.updateDefaultDisappearingMode(ephemeral)
```

### NEWSLETTER

- **Mendapatkan informasi newsletter**
```javascript
const metadata = await sock.newsletterMetadata("invite", "xxxxx")
// atau
const metadata = await sock.newsletterMetadata("jid", "abcd@newsletter")
console.log(metadata)
```

- **Mengubah deskripsi newsletter**
```javascript
await sock.newsletterUpdateDescription("abcd@newsletter", "Deskripsi Baru")
```

- **Mengubah nama newsletter**
```javascript
await sock.newsletterUpdateName("abcd@newsletter", "Nama Baru")
```

- **Mengubah foto profil newsletter**
```javascript
await sock.newsletterUpdatePicture("abcd@newsletter", buffer)
```

- **Menghapus foto profil newsletter**
```javascript
await sock.newsletterRemovePicture("abcd@newsletter")
```

- **Mematikan notifikasi newsletter**
```javascript
await sock.newsletterMute("abcd@newsletter")
```

- **Mengaktifkan kembali notifikasi newsletter**
```javascript
await sock.newsletterUnmute("abcd@newsletter")
```

- **Membuat newsletter baru**
```javascript
const metadata = await sock.newsletterCreate("Nama Newsletter", "Deskripsi Newsletter")
console.log(metadata)
```

- **Menghapus newsletter**
```javascript
await sock.newsletterDelete("abcd@newsletter")
```

- **Mengikuti newsletter**
```javascript
await sock.newsletterFollow("abcd@newsletter")
```

- **Berhenti mengikuti newsletter**
```javascript
await sock.newsletterUnfollow("abcd@newsletter")
```

- **Mengirim reaksi ke pesan di newsletter**
```javascript
const id = "175"
await sock.newsletterReactMessage("abcd@newsletter", id, "🥳")
```

### Ikon AI

```javascript
// cukup tambahkan "ai: true" pada sendMessage
await sock.sendMessage(id, { text: "Hello World", ai: true })
```

## Broadcast & Status WhatsApp

### Kirim Broadcast dan Status (Stories)

- Kamu bisa kirim pesan ke broadcast & story WhatsApp menggunakan `sendMessage()` seperti biasa, tapi dengan tambahan properti khusus:

```javascript
await sock.sendMessage(
  jid,
  {
    image: {
      url: url
    },
    caption: 'Halo dari broadcast!'
  },
  {
    backgroundColor: '#ffffff', // opsional
    font: 'default', // opsional
    statusJidList: ['628xxx@s.whatsapp.net'], // daftar kontak yang akan terima status
    broadcast: true // aktifkan mode broadcast
  }
)
```

- Konten pesan bisa berupa `extendedTextMessage`, `imageMessage`, `videoMessage`, atau `voiceMessage`.  
  [Lihat semua tipe konten pesan di sini](https://baileys.whiskeysockets.io/types/AnyRegularMessageContent.html)

- Kamu juga bisa menggunakan `backgroundColor`, `font`, dan pengaturan lainnya pada opsi pengiriman.  
  [Lihat semua opsi di sini](https://baileys.whiskeysockets.io/types/MiscMessageGenerationOptions.html)

- ID broadcast biasanya berbentuk: `12345678@broadcast`

### Ambil Info Daftar Broadcast

```javascript
let bList = await sock.getBroadcastListInfo('1234@broadcast')
console.log(`Nama list: ${bList.name}, Penerima: ${bList.recipients}`)
```

## Menulis Fungsionalitas Kustom (Custom Functionality)

Baileys dirancang untuk **ekstensi & kustomisasi**. Kamu tidak perlu fork repo untuk modifikasi — cukup tulis kode kamu sendiri dan panggil lewat API yang disediakan.

### Mengaktifkan Log Debug WhatsApp

- Untuk melihat semua pesan mentah dari WhatsApp, aktifkan logger debug saat inisialisasi soket:

```javascript
import P from 'pino'

const sock = makeWASocket({
  logger: P({ level: 'debug' })
})
```

> Ini sangat berguna kalau kamu ingin memahami **bagaimana WhatsApp bekerja di balik layar** atau mau buat fitur-fitur advance yang gak didokumentasikan.

## Bagaimana WhatsApp Berkomunikasi Dengan Kita

> **TIP:**  
> Kalau kamu ingin mempelajari protokol komunikasi WhatsApp, disarankan untuk memahami tentang **LibSignal Protocol** dan **Noise Protocol**.

---

### Contoh Kasus

Misalnya, kamu ingin melacak **persentase baterai** dari HP yang terhubung.  
Kalau kamu mengaktifkan log `debug`, maka akan muncul pesan seperti ini di terminal:

```
{
    "level": 10,
    "fromMe": false,
    "frame": {
        "tag": "ib",
        "attrs": {
            "from": "@s.whatsapp.net"
        },
        "content": [
            {
                "tag": "edge_routing",
                "attrs": {},
                "content": [
                    {
                        "tag": "routing_info",
                        "attrs": {},
                        "content": {
                            "type": "Buffer",
                            "data": [8,2,8,5]
                        }
                    }
                ]
            }
        ]
    },
    "msg": "communication"
}
```

---

### Penjelasan Struktur `frame`

Setiap pesan dari WhatsApp memiliki struktur `frame` dengan komponen utama berikut:

- `tag` — menandakan tipe pesan (contoh: `'message'`)
- `attrs` — objek berisi key-value untuk metadata (biasanya mengandung ID pesan)
- `content` — data utama dari isi pesan (contoh: isi teks dari pesan)

> Untuk dokumentasi lebih lanjut, lihat [struktur WABinary](/src/WABinary/readme.md)

---

### Daftarkan Callback Untuk Event WebSocket

> **TIP:**  
> Lihat fungsi `onMessageReceived` di file `socket.ts` untuk memahami cara event websocket diproses.

```javascript
// untuk semua pesan dengan tag 'edge_routing'
sock.ws.on('CB:edge_routing', (node) => { })

// untuk pesan dengan tag 'edge_routing' dan atribut id = abcd
sock.ws.on('CB:edge_routing,id:abcd', (node) => { })

// untuk pesan dengan tag 'edge_routing', id = abcd & isi pertama adalah 'routing_info'
sock.ws.on('CB:edge_routing,id:abcd,routing_info', (node) => { })
```

---

> **Lisensi:**  
> Repositori ini sekarang menggunakan lisensi **MIT** untuk kebebasan penggunaan dan modifikasi penuh oleh komunitas developer, tanpa batasan dari GPL.