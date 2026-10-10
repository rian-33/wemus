# Product Requirements Document (PRD)
## Web Museum Digital Makassar — Koleksi Peninggalan Sulawesi Selatan

**Versi:** 1.0
**Tanggal:** 10 Oktober 2026
**Status:** Draft

---

## 1. Ringkasan Produk

Web museum digital interaktif untuk Museum Makassar yang menampilkan koleksi peninggalan budaya Sulawesi Selatan secara lengkap dan interaktif. Pengguna dapat mengetahui informasi singkat sebuah peninggalan secara instan (tooltip saat hover/klik) tanpa kehilangan penjelasan lengkap yang tetap tampil di bawah konten.

---

## 2. Latar Belakang & Masalah

1. **Aksesibilitas terbatas** — Banyak orang sulit datang langsung ke museum (jarak, waktu, biaya, kondisi fisik).
2. **Informasi minim** — Untuk mengetahui peninggalan Sulawesi Selatan, sumber daring yang lengkap dan terpercaya masih sangat jarang; kebanyakan hanya artikel sporadis.
3. **Rotasi pajangan** — Peninggalan di museum memiliki jadwal pajang (tidak semua koleksi dipajang setiap hari), sehingga pengunjung fisik pun sering melewatkan koleksi tertentu.
4. **Pengalaman pasif** — Website museum yang ada umumnya statis dan tidak interaktif, tidak mengundang eksplorasi.

---

## 3. Tujuan & Metrik Keberhasilan

| Tujuan | Metrik |
|---|---|
| Menyediakan akses digital penuh ke seluruh koleksi | 100% koleksi terdigitalisasi (nama, deskripsi singkat, deskripsi lengkap, foto) |
| Pengalaman interaktif | Rata-rata durasi kunjungan > 3 menit; ≥ 60% pengguna menggunakan fitur tooltip |
| Menutupi kekurangan rotasi pajangan | Koleksi yang sedang tidak dipajang tetap dapat diakses daring |
| Kemudahan pengelolaan | Admin dapat menambah/mengubah koleksi tanpa menyentuh kode |

---

## 4. Ruang Lingkup (Scope)

### 4.1 In-Scope (MVP)
- Beranda dengan koleksi unggulan (featured)
- Katalog koleksi dengan pencarian dan filter kategori
- Halaman detail peninggalan dengan:
  - Tooltip penjelasan singkat saat hover/klik
  - Penjelasan lengkap yang tetap tampil di bawah konten
- Galeri foto per koleksi
- Form ulasan/feedback pengunjung
- Panel admin: CRUD koleksi, kategori, feedback
- Autentikasi admin

### 4.2 Out-of-Scope (Fase Berikutnya)
- Virtual tour 3D / model WebGL
- Multibahasa
- Tiket reservasi online
- Mobile app native

---

## 5. Persona Pengguna

1. **Pengunjung Umum** — masyarakat luas, pelajar, wisatawan; ingin belajar tentang budaya Sulsel secara mudah dan menarik.
2. **Admin Museum** — staf kurator/pengelola; bertanggung jawab mengelola konten koleksi dan ulasan.

---

## 6. Use Case

**Aktor:** Visitor (Pengunjung), Admin

```
            ┌─────────────────────────────┐
            │        Museum Makassar      │
            │                             │
 Visitor ───┼──▶ Lihat Koleksi/Beranda   │
            ├──▶ Filter/Search Koleksi    │
            ├──▶ Lihat Detail Peninggalan │
            ├──▶ Hover/Klik → Tooltip     │
            │    Penjelasan Singkat       │
            ├──▶ Lihat Penjelasan Lengkap │
            ├──▶ Galeri Foto Koleksi      │
            ├──▶ Kirim Ulasan/Feedback    │
            │                             │
 Admin ─────┼──▶ Login                    │
            ├──▶ CRUD Koleksi             │
            ├──▶ CRUD Kategori            │
            ├──▶ Kelola Feedback          │
            └──▶ Kelola Konten Beranda    │
            └─────────────────────────────┘
```

**Use case prioritas (MVP):** Lihat Detail Peninggalan (termasuk tooltip singkat + penjelasan lengkap), Filter/Search, CRUD Koleksi, Login Admin.

---

## 7. Fitur Utama & Kebutuhan Fungsional

| ID | Fitur | Deskripsi |
|---|---|---|
| F-01 | Tooltip Penjelasan Singkat | Saat pengguna hover/klik artefak, muncul tooltip animasi berisi `short_desc` (±150 karakter) |
| F-02 | Penjelasan Lengkap | `full_desc` selalu dirender di panel bawah konten, tidak bergantung tooltip (tidak di-hidden) |
| F-03 | Katalog & Pencarian | Daftar koleksi dengan search by name dan filter by kategori |
| F-04 | Galeri Foto | Multi-foto per koleksi dengan foto utama (primary) |
| F-05 | Ulasan/Feedback | Form nama, rating, pesan; tampil di admin |
| F-06 | Panel Admin | CRUD koleksi & kategori, kelola feedback, tandai koleksi unggulan |

---

## 8. Desain Interaksi (UX)

1. **Beranda** — hero section, koleksi unggulan dalam grid kartu interaktif (Framer Motion).
2. **Katalog** — grid dengan animasi hover; tooltip singkat muncul instan dengan easing animation.
3. **Detail** — foto utama besar, tooltip `short_desc`, di bawahnya panel `full_desc` lengkap + galeri foto tambahan + info era/asal-usul.
4. Prinsip: interaktif tapi ringan — animasi tooltip ±200–300ms, lazy-load gambar.

---

## 9. Arsitektur Teknologi

| Layer | Teknologi | Alasan |
|---|---|---|
| Frontend | Next.js 14+ (App Router) + TypeScript + Tailwind CSS | SSR untuk SEO, routing mudah |
| Interaktivitas | Framer Motion + React Three Fiber (fase 2, opsional) | Animasi tooltip & galeri |
| Backend | Next.js Route Handlers (BFF) | Fullstack satu repo, hosting murah |
| Database | Supabase (PostgreSQL) | Realtime, Storage, Auth siap pakai |
| Auth | Supabase Auth (role: admin) | JWT built-in |
| Storage | Supabase Storage | Foto koleksi |
| Hosting | Vercel | Deploy Next.js paling mulus |

### Diagram Arsitektur

```
┌──────────────────────────────────────────────┐
│                 Vercel (Edge)                 │
│  ┌────────────┐        ┌──────────────────┐  │
│  │ Next.js UI │───────▶│ Route Handlers    │  │
│  │ (SSR/CSR)  │  REST  │ (BFF layer)       │  │
│  └────────────┘        └────────┬─────────┘  │
└─────────────────────────────────┼────────────┘
                                  │
                    ┌─────────────▼─────────────┐
                    │        Supabase           │
                    │  ┌─────┐ ┌──────┐ ┌────┐ │
                    │  │ PG  │ │ Auth │ │Storage│
                    │  └─────┘ └──────┘ └────┘ │
                    └───────────────────────────┘
```

### Alur Interaksi Fitur Inti (Sequence)

```
Visitor ──▶ Web ──▶ GET /api/artifacts/:id ──▶ Supabase
                                                  │
Web ◀── { id, name, short_desc, full_desc, ──────┘
          images[] }
Web ──▶ Render tooltip (short_desc) + panel bawah (full_desc)
```

---

## 10. ERD

```
┌─────────────┐         ┌──────────────┐        ┌─────────────┐
│  categories │         │  artifacts   │        │   images    │
├─────────────┤ 1     * ├──────────────┤ 1    * ├─────────────┤
│ id (PK)     │────────▶│ id (PK)      │────────▶│ id (PK)     │
│ name        │         │ category_id  │        │ artifact_id │
│ slug        │         │ name         │        │ url         │
└─────────────┘         │ short_desc   │        │ is_primary  │
                        │ full_desc    │        └─────────────┘
┌─────────────┐         │ era/period   │
├─────────────┤         │ origin       │        ┌─────────────┐
│  feedback   │         │ is_featured  │        │    admins   │
├─────────────┤         │ created_at   │        ├─────────────┤
│ id (PK)     │         └──────────────┘        │ id (PK)     │
│ visitor_name│                                 │ email (FK → │
│ message     │   RLS: artifacts SELECT = public│ │  auth.users)│
│ rating      │   INSERT/UPDATE/DELETE = admin  │ │ role       │
│ created_at  │                                 └─────────────┘
└─────────────┘
```

### Kebijakan Keamanan (Supabase RLS)

- `artifacts`, `categories`, `images` → `SELECT` publik; `INSERT/UPDATE/DELETE` hanya admin
- `feedback` → `INSERT` publik; `SELECT/DELETE` hanya admin
- RLS wajib aktif; keamanan tidak mengandalkan penyembunyian tombol di UI

---

## 11. Kebutuhan Non-Fungsional

1. **Performa** — lazy-load semua gambar via `next/image`; LCP < 2,5 detik pada koneksi 4G.
2. **Skalabilitas** — arsitektur stateless (BFF + Supabase) siap scale horizontal.
3. **SEO** — SSR untuk halaman beranda, katalog, dan detail koleksi.
4. **Aksesibilitas** — kontras warna memadai, tooltip dapat diakses keyboard (focus trigger).
5. **Ketersediaan** — hosting Vercel + Supabase (managed) dengan SLA tinggi.

---

## 12. Catatan Kritis & Keputusan Desain

1. `short_desc` dan `full_desc` dipisahkan di level database — **bukan** dipotong di frontend (menghindari pemotongan teks berantakan).
2. Rotasi pajangan fisik tidak menghambat konten digital — seluruh koleksi tersedia daring tanpa jadwal.
3. MVP tanpa 3D/WebGL (berat); tooltip + galeri interaktif sudah memenuhi kebutuhan interaktivitas. 3D dipertimbangkan fase 2.
4. Read semua data via BFF agar RLS tetap menjadi garis pertahanan utama.

---

## 13. Roadmap

| Fase | Cakupan | Estimasi |
|---|---|---|
| Fase 1 (MVP) | F-01 s.d. F-06, auth admin, deployment | 6–8 minggu |
| Fase 2 | Virtual tour 3D, multibahasa, analytics | Menyusul |
