# PRD Baseline — PAUD Kasih Ibu

**Status:** as-built, disusun dari kode per 21 November 2026.
**Fungsi dokumen:** menggambarkan kondisi kode **sekarang**, bukan keinginan. Semua yang tertulis di sini dianggap **sudah benar dan terkunci**. Perubahan hanya boleh jika diminta eksplisit oleh pemilik proyek (lihat `AGENTS.md`).

---

## 1. Ringkasan

Dua aplikasi terpisah, masing-masing satu file HTML:

| Aplikasi | File | Domain | Pengguna |
|---|---|---|---|
| Website Utama | `website_utama_paud_kasih_ibu.html` | paudkasihibu.my.id | Calon orang tua, masyarakat |
| Portal Akademik | `portal_akademik_paud_kasih_ibu.html` | portal.paudkasihibu.my.id | Admin, wali kelas, orang tua |

- **Teknologi:** HTML + JavaScript biasa (tanpa framework, tanpa build). Tailwind via CDN, font Plus Jakarta Sans (Google Fonts).
- **Library tambahan:** website memakai `lucide` (unpkg); portal memakai SheetJS/`xlsx` (cdn.sheetjs.com) untuk impor/ekspor Excel.
- **Bahasa antarmuka:** Indonesia. Mobile-first.
- **Penyimpanan:** website tidak menyimpan data; portal menyimpan semuanya di `localStorage` dengan key `paud_kasih_ibu_portal_v6`.

---

## 2. Identitas visual (terkunci)

**Warna** (dari konfigurasi Tailwind website):

| Token | Hex | Pemakaian |
|---|---|---|
| `primer` | `#0968E5` | tombol, ikon, aksen |
| `primergelap` | `#064DB0` | hover, judul gelap |
| `toska` | `#00B090` | ujung gradasi hero |
| `birulembut` | `#EFF6FF` | latar ikon, kartu lembut |
| `biruborder` | `#DBEAFE` | bingkai lembut |
| `kanvas` | `#F8FAFC` | latar halaman |
| `teks` | `#0F172A` | teks utama |
| `sekunder` | `#475569` | teks sekunder |
| `garis` | `#E2E8F0` | garis/border |
| `sukses` / `bahaya` | `#15803D` / `#DC2626` | status |

- **Gradasi hero:** `102deg`, `#0968E5` → `#0582E8` (60%) → `#00B594`.
- **Font:** Plus Jakarta Sans.
- **Moto:** "Generasi Hebat untuk Bangsa yang Kuat". **Tagline header:** "Ayo Sekolah!!".
- **Logo saat ini:** belum ada file logo. Website memakai ikon perisai SVG inline di header; portal memakai ikon `school` di kop E-Raport. Tidak ada favicon dan tidak ada `og:image`.

---

## 3. Website Utama

### 3.1 Halaman
Perpindahan halaman memakai fungsi `navigateTo(route)` (tanpa URL terpisah per halaman):

`beranda`, `profil`, `visimisi`, `program`, `guru`, `fasilitas`, `galeri`, `berita`, `spmb`, `faq`, `kontak`, dan halaman 404.

Pengelompokan menu: **Tentang** (profil, visimisi, program, guru, fasilitas) dan **Publikasi** (galeri, berita, faq).

### 3.2 Fitur
- **Pencarian cepat** di hero (`handleHeroSearch`) dengan pilihan kategori.
- **Statistik** di beranda (alumni, peserta didik, guru/tendik, angkatan) dan sambutan Kepala Sekolah.
- **Galeri** dengan filter kategori dan lightbox (tombol panah kiri/kanan dan Esc di keyboard).
- **FAQ** berbentuk accordion.
- **SPMB:** formulir Google Form disematkan (embed) dan bisa dibuka di tab baru; ada pemberitahuan privasi UU No. 27 Tahun 2022. Status `dibuka` diatur di `SCHOOL_DATA.spmb`.
- **Kontak:** peta OpenStreetMap, formulir cepat yang mengirim ke WhatsApp (`sendQuickWhatsApp`), dan tombol WhatsApp mengambang.
- **Modal "Login E-Raport / Portal"** (`openPortalModal`) yang mengarahkan ke portal.
- **Footer** dengan tautan Instagram, Facebook, TikTok.
- **SEO:** meta description, Open Graph (judul, deskripsi, tipe, url), JSON-LD tipe `Preschool`.

### 3.3 Sumber konten
Semua konten ada di satu objek `SCHOOL_DATA` (spmb, stats, kepalaSekolah, visiMisi dengan 7 misi, news, teachers, programs, facilities, gallery, faqs). Mengubah isi konten = mengubah objek ini, bukan markup halaman.

### 3.4 Gambar
Galeri dan berita memakai foto stok Unsplash lewat URL. Jika gagal dimuat, `imgFallback` menampilkan gambar pengganti dari placehold.co. **Belum ada foto asli sekolah.**

---

## 4. Portal Akademik

### 4.1 Peran dan menu

| Peran | Menu |
|---|---|
| `admin` | Beranda, Peserta Didik, Rombongan Belajar, E-Raport, Pengaturan, Log Aktivitas |
| `guru` (wali kelas) | Beranda, Siswa Kelas Saya, Rombel Saya, E-Raport |
| `orang_tua` | Beranda Anak, E-Raport Anak |

### 4.2 Login dan pendaftaran
- **Login:** username, email, atau nomor HP, ditambah kata sandi. Hanya akun aktif yang bisa masuk. Setiap login tercatat di log aktivitas.
- **Pendaftaran orang tua:**
  1. Isi nama lengkap anak (minimal 3 huruf). Sistem mencocokkan dengan siswa aktif: cocok persis, atau hanya satu kecocokan sebagian.
  2. Jika ada saudara kandung (anak lain yang tertaut ke orang tua yang sama), orang tua bisa menautkannya sekaligus.
  3. Isi nama orang tua, hubungan, nomor HP (minimal 9 digit, harus unik), kata sandi (minimal 6 karakter).
  4. Wajib mencentang persetujuan Kebijakan Perlindungan Data Pribadi Anak (UU PDP). Waktu persetujuan disimpan.
- Nomor HP menjadi username akun orang tua.

### 4.3 Peserta Didik (admin; wali kelas hanya kelasnya)
- Daftar dengan filter dan pencarian.
- Tambah/ubah/hapus siswa. Hapus bersifat **soft delete** (`deleted_at`).
- **Ekspor Excel** dan **impor Excel**. Kolom dikenali: `Nama_Lengkap`, `NIS`, `NISN`, `Nama_Panggilan`, `Jenis_Kelamin`, `Tempat_Lahir`, `Tanggal_Lahir`, `Rombel`.
- **Reset semua siswa** (khusus admin).

### 4.4 Rombongan Belajar dan tahun ajaran
- Data awal: Kelompok B (5–6 tahun) dan Kelompok A (4–5 tahun), masing-masing satu wali kelas.
- **Transisi tahun ajaran:** membuat semester baru, menonaktifkan semester lama, menyalin rombel dan anggotanya ke semester baru.

### 4.5 E-Raport
**Isi raport:**
- 4 aspek penilaian: `NABP` (Nilai Agama dan Budi Pekerti), `JATIDIRI` (Jati Diri), `LSTEAM` (Literasi, Matematika, Sains, Teknologi, Rekayasa, Seni), `P5` (Projek Penguatan Profil Pelajar Pancasila). Tiap aspek punya deskripsi dan satu foto kegiatan.
- Berat badan, tinggi badan, lingkar kepala.
- Sakit, izin, alpa.
- Informasi perkembangan, catatan wali kelas, tanggal raport, dan kolom refleksi orang tua.

**Alur status (terkunci):**

```
draft  ──(wali kelas: finalisasi)──▶  menunggu_persetujuan  ──(admin: setujui)──▶  terbit
  ▲                                         │
  └──────────(admin: kembalikan ke draft)───┴──────────────────────────────────────┘
```

**Aturan:**
- Hanya raport berstatus `draft` yang bisa diedit atau di-reset.
- Wali kelas hanya mengelola raport rombelnya sendiri; admin mengelola semuanya.
- Hanya admin yang boleh menyetujui/menerbitkan dan mengembalikan ke draft.
- Saat `terbit`, identitas siswa/rombel/wali/kepala sekolah disimpan sebagai `snapshot_identitas`.
- Orang tua hanya melihat raport anaknya yang berstatus `terbit`.
- **Cetak A4:** lembar cetak dengan kop (nama sekolah, subjudul, label "Kurikulum Merdeka"), tanda tangan wali kelas dan kepala sekolah.
- Saat raport baru dibuat otomatis, nilai awal BB/TB/lingkar kepala terisi angka bawaan (lihat bagian 7).

### 4.6 Pengaturan (admin)
- Data sekolah: nama, subjudul kop, moto, kota raport, telepon.
- Tanda tangan kepala sekolah dan guru: digambar di canvas.
- Modal blueprint skema SQL Supabase dan konfigurasi Vercel (hanya teks informasi).

### 4.7 Log aktivitas (admin)
Mencatat aksi: `LOGIN`, `SIMPAN_DRAFT_RAPORT`, `AJUKAN_FINAL_RAPORT`, `SETUJUI_TERBIT_RAPORT`, `KEMBALIKAN_DRAFT_RAPORT`, `RESET_DRAFT_RAPORT`, `TRANSISI_SEMESTER`, `IMPOR_EXCEL_SISWA`, dan sejenisnya.

### 4.8 Lainnya
- Modal Kebijakan Perlindungan Data Pribadi Anak (UU PDP).
- Tombol reset database demo di layar login.

---

## 5. Model data portal (`state.db`)

Koleksi: `pengaturan_sekolah`, `users`, `kepala_sekolah`, `guru`, `semester`, `rombel`, `siswa`, `rombel_siswa`, `orang_tua`, `orang_tua_siswa`, `aspek_penilaian`, `raport`, `log_aktivitas`.

Catatan struktur:
- Relasi memakai id teks (misalnya `sis-1`, `rom-1`). Penghapusan memakai `deleted_at`.
- Siswa ↔ rombel lewat `rombel_siswa` (per semester). Orang tua ↔ siswa lewat `orang_tua_siswa`.
- `raport.isi` adalah array `{ aspek_id, deskripsi, foto_url }`.
- `foto_url` saat ini berisi data URL (base64) atau SVG bawaan.

**Skema SQL di dalam kode (`SUPABASE_SQL_SCHEMA`) belum sinkron dengan data ini:**
- Skema hanya berisi 3 tabel (`users_profile`, `siswa`, `raport`). Tabel `guru`, `rombel`, `semester`, `kepala_sekolah`, `orang_tua`, `aspek_penilaian`, dan log belum ada.
- Tabel `raport` di skema tidak punya kolom `isi` (deskripsi dan foto per aspek), `refleksi_ortu`, `wali_id`, `kepsek_id`.
- Anggap skema itu sebagai sketsa awal, bukan acuan final.

---

## 6. Yang nyata vs. yang masih simulasi

| Aspek | Kondisi saat ini |
|---|---|
| Tampilan, alur, validasi form | Nyata (di sisi browser) |
| Autentikasi | **Simulasi.** Kata sandi disimpan apa adanya (plaintext) di data lokal dan dicocokkan di JavaScript |
| Hak akses per peran | **Hanya di JavaScript**, bisa dilewati lewat konsol browser |
| Penyimpanan | `localStorage` per browser: tidak dibagi antar perangkat/pengguna |
| Foto raport | Base64 di `localStorage` (batas kuota browser sekitar 5 MB) |
| Data siswa | 31 siswa fiktif (data awal), 2 rombel |
| Akun demo | 1 admin, 2 guru, 1 orang tua, kata sandi demo seragam |

> **Konsekuensi:** portal **belum boleh dipakai dengan data siswa asli** sebelum ada backend dan autentikasi sungguhan.

---

## 7. Masalah yang sudah diketahui

Dicatat agar tidak "ditemukan ulang" setiap sesi. **Jangan diperbaiki kecuali diminta.**

**Keamanan dan data**
1. Pendaftaran orang tua hanya diverifikasi lewat nama lengkap anak. Siapa pun yang tahu nama anak bisa membuat akun dan melihat raport yang terbit.
2. Raport baru otomatis terisi BB 17, TB 108, lingkar kepala 49,5. Jika guru lupa mengganti, angka bawaan ini ikut tersimpan seolah data asli.
3. Impor Excel mengisi nilai bawaan bila kolom kosong: tempat lahir "Indramayu", tanggal lahir 2021-01-01, jenis kelamin L.
4. Transisi tahun ajaran hanya menyalin rombel dan anggota. Tidak menaikkan kelompok dan tidak menandai siswa lulus.

**Teknis**
5. Website: perpindahan halaman tidak mengubah URL. Refresh selalu kembali ke beranda dan halaman tidak bisa dibagikan langsung.
6. Website memakai `lucide@latest` (versi tidak dikunci). Portal dan website memakai Tailwind lewat CDN.
7. Warna toska tidak seragam: `#00B090` (konfigurasi website) vs `#00B594` (gradasi hero dan portal).

**Konten**
8. Nomor WhatsApp di website (`6281234567890`) tampak placeholder dan berbeda dari telepon sekolah di pengaturan portal. Perlu dikonfirmasi pemilik.
9. Tautan Instagram/Facebook/TikTok mengarah ke beranda platform, bukan akun sekolah.
10. Foto galeri/berita adalah foto stok, bukan foto kegiatan sekolah.

---

## 8. Permintaan yang sedang direncanakan

Belum dikerjakan. Kerjakan **satu per sesi, satu branch per tugas**.

### 8.1 Ganti logo
Lokasi yang perlu disentuh (cari lewat komentar/nama, nomor baris bisa bergeser):
- Website: emblem di header (komentar "Emblem Crest Sekolah", sekitar baris 104–111). Footer perlu dicek.
- Belum ada: favicon dan `og:image` (boleh ditambahkan saat ganti logo, jika diminta).
- Portal: kop E-Raport di `renderRaportPageHeader` (ikon `school`) dan layar login/menu samping (perlu dicek saat dikerjakan).
- Aset disimpan di `assets/` (lihat aturan di `AGENTS.md`), bukan base64 di dalam HTML.

### 8.2 Tambah foto
- **Website:** ganti `src` di `SCHOOL_DATA` (gallery, news, teachers, facilities) dari Unsplash ke file di `assets/`.
- **Portal:** foto kegiatan raport (`handleActivityPhotoUpload`) saat ini base64 di `localStorage`. Pemindahan ke storage sungguhan bergantung pada keputusan database (8.3).

### 8.3 Konek database
**Keputusan yang masih terbuka** dan perlu ditulis di dokumen teknis terpisah sebelum mulai:
- Layanan yang dipakai (rencana awal di kode: Supabase + Vercel) dan apakah login memakai Supabase Auth.
- Daftar tabel final (menyelaraskan skema SQL dengan data di bagian 5).
- Aturan akses per peran di sisi database (Row Level Security): orang tua hanya anaknya dan hanya raport `terbit`; wali kelas hanya rombelnya; admin semuanya.
- Cara login orang tua (nomor HP + nama anak saja tidak cukup aman).
- Nasib data awal 31 siswa fiktif (dibuang atau diimpor) dan akun demo.
- Penyimpanan foto raport (Storage) dan ukuran maksimum.
- Bagian kode yang disentuh: idealnya hanya lapisan data (`loadDatabase`, `saveDatabase`, dan akses `state.db`), bukan tampilan.

---

## 9. Riwayat dokumen

| Tanggal | Perubahan |
|---|---|
| 2026-11-21 | Versi awal (baseline) dari kode hasil Gemini |
