# AGENTS.md — Aturan Kerja untuk AI (PAUD Kasih Ibu)

Berlaku untuk AI mana pun (Gemini, Claude, ChatGPT, dll.) yang mengubah kode di proyek ini.

**Langkah pertama setiap sesi:** baca `docs/PRD-baseline.md`. Isinya adalah kondisi kode yang sudah benar dan terkunci. Jangan mengubah apa pun yang tertulis di sana tanpa diminta.

---

## 1. Prinsip utama: perubahan minimal

1. **Ubah hanya yang diminta, tidak lebih.** Jika diminta ganti logo, hanya logo yang berubah.
2. **Jangan menulis ulang file.** Edit bagian yang spesifik. Jangan mengirim ulang seluruh file kecuali diminta secara eksplisit.
3. **Jangan refactor, rename, reformat, merapikan, mengurutkan ulang, atau menghapus komentar.** Termasuk "sekalian diperbaiki" atau "biar lebih bersih".
4. **Jangan mengubah di luar permintaan:** teks, warna, font, layout, jarak, nama fungsi/variabel, ID elemen, key `localStorage`, nama koleksi/kolom data.
5. **Fitur baru bersifat tambahan (additive).** Tambahkan fungsi atau blok baru. Sambungkan ke kode lama sekecil mungkin (misalnya satu baris pemanggilan atau satu entri menu). Fitur lama harus berperilaku sama persis.
6. **Temuan di luar permintaan → laporkan, jangan perbaiki.** Tulis di bagian "Temuan di luar permintaan" pada jawaban.
7. **Kalau ragu, tanya dulu.** Jika permintaan ambigu, atau perubahan ternyata harus menyentuh area lain, berhenti dan tanyakan.

---

## 2. Yang dikunci (butuh izin eksplisit untuk mengubah)

- Identitas visual: palet warna, font, gradasi hero, moto, tagline (lihat PRD bagian 2).
- Daftar halaman dan menu website; daftar menu per peran di portal.
- Alur status E-Raport `draft → menunggu_persetujuan → terbit` dan siapa yang boleh melakukan tiap langkah.
- Aturan hak akses per peran.
- Struktur data portal (`state.db`) dan key `paud_kasih_ibu_portal_v6`. Jika struktur data memang harus berubah, **wajib** menyertakan migrasi untuk data lama, jangan mengganti key diam-diam.
- Tampilan cetak A4 E-Raport.
- Isi konten dan teks sekolah (visi, misi, sambutan, FAQ, dll.).

---

## 3. Format jawaban

Setiap kali selesai mengubah kode, jawab dengan urutan ini:

1. **Ringkasan** (1–3 kalimat): apa yang diubah.
2. **File dan lokasi:** nama file + nama fungsi/ID elemen/komentar yang diubah (nomor baris boleh sebagai pelengkap).
3. **Perubahan:** hanya blok yang berubah, dengan sedikit baris konteks di sekitarnya. Lebih baik dalam bentuk diff.
4. **Tidak diubah:** satu kalimat yang menegaskan bagian lain tidak disentuh.
5. **Temuan di luar permintaan** (jika ada), tanpa memperbaikinya.
6. **Cara mengecek:** langkah singkat untuk membuktikan perubahan bekerja dan fitur lain tetap normal.

---

## 4. Checklist sebelum menyatakan selesai

- [ ] Kode valid: tidak ada tag atau kurung yang terpotong, tidak ada error di konsol.
- [ ] Hanya bagian yang diminta yang berubah (periksa diff).
- [ ] Fitur lain tetap sama: login tiap peran, alur raport, impor/ekspor, navigasi halaman website.
- [ ] Tampilan di layar HP (lebar sekitar 360 px) tidak rusak.
- [ ] Tidak menambah dependensi CDN baru tanpa izin. Jika perlu, kunci versinya (jangan `@latest`).
- [ ] Tidak ada kunci rahasia, kata sandi, atau data siswa asli di dalam kode.

---

## 5. Keamanan dan data anak

Portal berisi data anak. Hormati UU No. 27 Tahun 2022 (Pelindungan Data Pribadi).

- Jangan menaruh kunci rahasia di HTML atau repo (misalnya Supabase `service_role`, API secret). Di sisi browser hanya boleh kunci publik (`anon`), dan hanya jika Row Level Security sudah aktif.
- Jangan memasukkan data siswa/orang tua asli ke repo, contoh kode, atau pesan commit.
- Setelah ada backend, kata sandi tidak boleh disimpan plaintext, dan hak akses harus dipaksakan di database, bukan hanya di tampilan.
- Jangan menambahkan pelacakan, analitik, atau pihak ketiga baru tanpa izin.

---

## 6. Alur Git

- Satu tugas = satu branch: `revisi/<topik>` atau `fitur/<topik>`. Contoh: `revisi/ganti-logo`, `fitur/foto-galeri`.
- Commit kecil, pesan dalam bahasa Indonesia, menjelaskan satu hal. Contoh: `Ganti emblem header website dengan logo sekolah`.
- Jangan langsung ke `main`. Pemilik proyek memeriksa diff, lalu menggabungkan.
- Jika hasilnya merusak sesuatu: buang branch, bukan menambal di atasnya.

---

## 7. Aset (logo, foto)

- Simpan di folder `assets/` (misalnya `assets/logo/`, `assets/foto/`), panggil lewat path relatif.
- **Jangan menanam gambar sebagai base64 di dalam HTML.**
- Foto: format WebP atau JPG, lebar maksimal sekitar 1200 px, dikompres. Logo: SVG atau PNG transparan.
- Nama file huruf kecil dengan tanda hubung (`kegiatan-menanam.webp`). Setiap gambar punya `alt` yang bermakna.
- Foto anak hanya boleh dipasang jika sekolah sudah memegang izin orang tua.

---

## 8. Peta kode (untuk menemukan bagian yang tepat)

Nomor baris adalah perkiraan per 21 November 2026 dan akan bergeser. **Cari berdasarkan nama fungsi atau komentar.**

### Website (`website_utama_paud_kasih_ibu.html`)

| Bagian | Lokasi |
|---|---|
| Meta, Open Graph, JSON-LD | baris 1–25 |
| Konfigurasi Tailwind (warna, font) | ± baris 39–63 |
| Header + emblem/logo | ± baris 99–122 |
| Konten halaman (`<main id="app-content">`) | ± baris 210 |
| Footer | ± baris 215 |
| Tombol WhatsApp mengambang | ± baris 287 |
| Lightbox galeri | ± baris 305 |
| Modal portal | ± baris 336 |
| **Semua konten: `SCHOOL_DATA`** | ± baris 374–560 |
| Routing (`navigateTo`, `renderPage`) | ± baris 585, 1632 |
| Halaman: `viewBeranda`, `viewProfil`, `viewVisiMisi`, `viewProgram`, `viewGuru`, `viewFasilitas`, `viewGaleri`, `viewBerita`, `viewSpmb`, `viewFaq`, `viewKontak`, `view404` | ± baris 713–1630 |
| Lightbox dan modal portal (script) | ± baris 1651–1690 |

### Portal (`portal_akademik_paud_kasih_ibu.html`)

Skrip dibagi 18 bagian bernomor lewat komentar `// N. NAMA BAGIAN`:

| No | Bagian | ± Baris |
|---|---|---|
| 1 | Blueprint SQL Supabase & konfigurasi Vercel | 160 |
| 2 | Tanda tangan dan SVG foto bawaan | 226 |
| 3 | Data awal (seed) | 259 |
| 4 | State aplikasi & penyimpanan (`loadDatabase`, `saveDatabase`) | 680 |
| 5 | Utilitas (escape, format, toast, modal, audit log) | 733 |
| 6 | Ikon SVG | 844 |
| 7 | Helper query data & deteksi saudara kandung | 862 |
| 8 | Modal kebijakan UU PDP | 942 |
| 9 | Login & pendaftaran orang tua | 994 |
| 10 | Shell navigasi per peran | 1436 |
| 11 | Dashboard admin & wali kelas | 1568 |
| 12 | Peserta didik (CRUD, Excel, reset) | 1759 |
| 13 | Rombel & transisi tahun ajaran | 2230 |
| 14 | E-Raport (kartu siswa + editor) | 2374 |
| 15 | Lembar cetak A4 E-Raport | 3063 |
| 16 | Dashboard & E-Raport orang tua | 3256 |
| 17 | Pengaturan, tanda tangan, log aktivitas | 3450 |
| 18 | Render utama & inisialisasi | 3681 |

---

## 9. Untuk pemilik proyek: cara memberi tugas

Salin template ini setiap kali memulai tugas:

```
Baca AGENTS.md dan docs/PRD-baseline.md terlebih dulu.

Tugas: <satu hal saja, misalnya "ganti logo di header website">
File: <website / portal>
Detail: <bahan yang diperlukan, misalnya nama file logo di assets/logo/>
Jangan ubah: semua yang tidak disebut di atas.

Jawab sesuai format di AGENTS.md (diff saja, bukan seluruh file).
```

Tips:
- **Satu tugas per sesi.** Jangan menggabungkan logo, foto, dan database dalam satu percakapan.
- Selalu buat branch baru sebelum menempelkan hasilnya, dan lihat diff sebelum commit.
- Jika ada perubahan yang diterima, perbarui `docs/PRD-baseline.md` (bagian yang relevan dan riwayat dokumen) di commit yang sama, agar baseline selalu akurat.
