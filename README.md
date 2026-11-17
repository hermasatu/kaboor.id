# Runlog v2 — Coach dinamis + OCR Huawei Health

## Yang baru di v2
1. **Coach dinamis (lokal, tanpa AI)** — setiap kali kamu simpan aktivitas / recovery, kartu "Sesi berikutnya" di Ringkasan dihitung ulang dari data aktualmu:
   - Jadwal dasar: Day 1 = 4 Nov 2026, Selasa (BHHH trail), Kamis (easy → strides → tempo/interval sesuai fase), Sabtu (endurance), deload minggu 4/8/12, tes 5K akhir hanya jika siap.
   - Batas HR easy otomatis (HR maks dari tanggal lahir + resting HR dari Recovery).
   - Durasi naik +10% bila 2 sesi easy terakhir terasa ringan; ditahan bila sesi terakhir terlalu keras (RPE, talk test, HR di atas batas).
   - Dipotong otomatis bila ankle terasa, recovery jelek (tidur/lelah/pegal), resting HR naik ≥7, beban 7 hari melonjak (ACWR), atau jeda ≥10 hari. Ankle sakit → istirahat aktif.
   - Format sesi "jog + jalan" dipakai sampai tubuh siap jog kontinu.
   - Evaluasi sesi terakhir: intensitas, durasi vs target, efisiensi aerobik (meter per detak) vs awal program, tips cadence.
2. **OCR screenshot Huawei Health (lokal)** — tombol "📷 Baca screenshot" di form aktivitas. Tesseract.js berjalan di browser HP/laptop, tanpa API AI, tanpa biaya token. Bisa pilih 1–3 screenshot, tempel (Ctrl+V), atau seret gambar. Kolom yang terbaca berwarna hijau; data **tidak pernah tersimpan otomatis** — kamu cek dulu lalu klik Simpan.
3. **Hemat server** — semua data dimuat 1× saat login dan disimpan di cache; setiap simpan = 1 kali kirim. Isi form tersimpan sebagai draft di perangkat (tidak hilang saat refresh).
4. **Perbaikan bug MVP lama** — insert sebelumnya gagal karena `user_id` tidak dikirim. Sekarang dikirim + default `auth.uid()` di database.
5. Durasi kini bisa diketik `33:59` / `1:05:20` (atau menit desimal), pace tampil otomatis.

## File
- `index.html` — aplikasi lengkap (replace-all).
- `supabase/schema.sql` — schema lengkap + blok v2 di paling bawah (aman dijalankan ulang).
- `supabase/functions/manual-entry-validator/index.ts` — tidak berubah, opsional.

## Setup langkah demi langkah
1. Buka https://supabase.com → **New project** → isi nama (mis. `runlog`), password database, region Singapore → tunggu selesai.
2. Menu kiri **SQL Editor** → **New query** → tempel seluruh isi `supabase/schema.sql` → **Run**. Harus muncul "Success".
3. Menu **Project Settings → API** → salin **Project URL** dan **anon / publishable key**.
4. Buka `index.html` dengan Notepad/VS Code → cari baris `const SB_URL=` → ganti `https://YOUR_PROJECT.supabase.co` dan `YOUR_SUPABASE_ANON_KEY` dengan nilai tadi → simpan. (Jangan pernah pakai service_role key.)
5. GitHub → **New repository** (mis. `runlog`, Public) → **Add file → Upload files** → upload `index.html` → **Commit**.
6. Repo → **Settings → Pages** → Source: *Deploy from a branch*, Branch: `main` / `(root)` → **Save**. Tunggu 1–2 menit, alamat muncul: `https://USERNAME.github.io/runlog/`.
7. Supabase → **Authentication → URL Configuration** → Site URL = alamat GitHub Pages tadi, tambahkan juga di Redirect URLs.
8. Buka alamat aplikasi → daftar dengan email + password → konfirmasi email → masuk.
9. Isi **Profil & target** (tanggal lahir penting untuk batas HR), lalu catat aktivitas 4 Nov (dan sesi setelahnya).

## Cara pakai OCR
1. Setelah lari, buka Huawei Health → detail aktivitas → screenshot (halaman ringkasan; bila data terpotong, ambil 2 screenshot).
2. Runlog → Aktivitas → **📷 Baca screenshot** → pilih gambar. Pemakaian pertama mengunduh mesin OCR (±5–10 MB, sekali saja).
3. Cek kolom hijau, isi RPE, talk test, ankle → **Simpan aktivitas** → otomatis pindah ke Ringkasan dengan evaluasi + sesi berikutnya.
4. Kalau ada angka salah baca, buka "Teks terbaca" dan kirim teksnya ke developer untuk penyesuaian parser.

## Uji cepat setelah deploy
- Simpan aktivitas manual → muncul di Riwayat + kartu Ringkasan berubah.
- Isi Recovery dengan ankle "Sedikit terasa" → sesi berikutnya berkurang ±20%.
- Upload screenshot yang sama 2× → muncul pesan "sudah pernah disimpan".
