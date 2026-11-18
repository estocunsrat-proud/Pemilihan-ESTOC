# Pemira KSPM Equil Stock Club UNSRAT

Aplikasi e-voting Calon Presiden Organisasi. Satu file HTML statis (`index.html`) + Firebase
(Authentication Google + Firestore). Tidak ada server yang perlu dijalankan.

File di repo ini:

| File | Fungsi |
|---|---|
| `index.html` | Seluruh aplikasi (isi `FIREBASE_CONFIG` dan `ADMIN_EMAILS` di bagian atas script) |
| `logo.png` | Logo Equil Stock Club (putih, transparan) |
| `firestore.rules` | Aturan keamanan database (dipasang di Firebase Console) |

## Cara memasang

### 1. Buat project Firebase
1. Buka https://console.firebase.google.com lalu **Add project** (Google Analytics boleh dimatikan).

### 2. Aktifkan login Google
1. **Build → Authentication → Get started → Sign-in method → Google → Enable**.
2. Isi *Project support email*, lalu **Save**.
3. Buka tab **Settings → Authorized domains → Add domain**, lalu tambahkan domain Vercel Anda
   (contoh `pemira-estoc.vercel.app`, tanpa `https://`). Tanpa ini login ditolak (`auth/unauthorized-domain`).

### 3. Buat database Firestore
1. **Build → Firestore Database → Create database**.
2. Pilih lokasi (mis. `asia-southeast2` Jakarta) dan mode **Production**.

### 4. Pasang aturan keamanan (WAJIB)
1. Buka tab **Rules** di Firestore.
2. Ganti seluruh isinya dengan isi `firestore.rules`.
3. Ubah email admin di fungsi `isAdmin()` (`['rulan0144@gmail.com']`) menjadi email admin yang benar.
4. Klik **Publish**.

Aturan inilah yang menjaga: satu akun satu suara, tepat 2 kandidat berbeda, suara hanya bisa dikirim
saat sesi aktif (waktu dihitung server), suara tidak bisa diubah, dan hanya admin yang bisa membaca
seluruh suara atau mengubah kandidat/pengaturan.

### 5. Ambil konfigurasi web
1. **Project settings (ikon roda gigi) → General → Your apps → ikon web `</>`** → daftarkan app.
2. Salin objek `firebaseConfig` ke konstanta `FIREBASE_CONFIG` di bagian atas script `index.html`.
3. Pastikan `ADMIN_EMAILS` di `index.html` sama dengan email admin di `firestore.rules`.

> Nilai `apiKey` memang tampak publik; itu normal untuk Firebase. Keamanan ada di aturan Firestore.

### 6. Unggah dan deploy
Commit `index.html`, `logo.png`, dan `firestore.rules` ke GitHub. Vercel akan men-deploy otomatis
(Framework Preset: **Other**, tanpa build command).

### 7. Uji sebelum hari H
1. Login sebagai admin, 13 kandidat awal otomatis dimuat jika database kosong.
2. Mulai voting, lalu login dengan akun Google lain (HP / jendela Incognito) dan kirim suara.
3. Coba kirim suara kedua dari akun yang sama: harus ditolak.
4. Hentikan voting, lalu **Publikasikan Hasil (Top 3)** dan cek dari akun pemilih.
5. Klik **Reset Data Pemilu** agar data uji hilang.

## Pembatasan pemilih (opsional)
Di Panel Admin ada kartu **Daftar Email Pemilih yang Diizinkan**. Jika diisi, hanya email di daftar itu
(dan admin) yang bisa masuk. Jika kosong, semua akun Google boleh memilih. Daftar ini dicek oleh Firebase.

## Catatan
- Admin dapat melihat siapa memilih siapa (fitur pemantauan). Pemilih tidak dapat melihat suara siapa pun.
- Hasil Top 3 hanya bisa dipublikasikan setelah voting dihentikan, supaya hasil yang tampil final.
- Paket gratis Firebase (Spark) cukup untuk organisasi berukuran ratusan anggota.

## Masalah umum
| Gejala | Penyebab / solusi |
|---|---|
| Halaman login menampilkan "belum dikonfigurasi" | `FIREBASE_CONFIG` belum diisi |
| `auth/unauthorized-domain` | Domain Vercel belum ditambahkan di Authorized domains |
| `auth/operation-not-allowed` | Login Google belum di-Enable |
| Admin ditolak setelah login | Rules belum dipasang, atau email admin di rules tidak sama |
| "Database Firestore belum dibuat" | Langkah 3 belum dilakukan |
| Pemilih ditolak "tidak terdaftar" | Email tidak ada di Daftar Email yang Diizinkan |
| Suara ditolak | Sesi tidak aktif / waktu habis / akun itu sudah memilih |
