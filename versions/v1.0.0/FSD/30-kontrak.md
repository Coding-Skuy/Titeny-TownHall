> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# FSD 30 — Kontrak API, Event, dan Galat

## Basis dan Autentikasi

- Basis: `https://insight.titeny.id/api/v1` dengan PIN zona via `X-Zona-PIN: <6_digit>` untuk petani/vendor dan `Authorization: Bearer <token>` JWT `aud=titeny`, `iss=chefgenie-auth` untuk CEO/analis/layanan. Sehat: `GET /v1/kesehatan` dan `GET /api/v1/kesehatan` kembali status ok, job terakhir, versi API v1.0.0.
- Token analis kedaluwarsa 8 jam dan refresh via OTP; disimpan di Windows Credential Manager, bukan berkas teks. Batas 60 req/menit per token (429 plus `Retry-After`); bodi maks 256 KB; paginasi limit default 50 maks 200 dengan cursor opaque.
- Stack pengikat: web Bun latest (baseline 1.4.x) + Svelte 5 + SvelteKit 2 + TypeScript latest (baseline 5.9.x) + zod 3.23.x; analis-desktop KMP Windows (Kotlin 2.1.x, Compose 1.7.x, Navigation3 1.0.0, Ktor 3.1.x, SQLDelight 2.0.x); backend Rust axum 0.8.4 dengan `DATABASE_URL` ke DB `titeny`; model dan pipeline Python 3.12.

## Kontrak per Konsumen

- Web baca 5 endpoint: `GET /harga`, `GET /demand`, `GET /panen`, `GET /rekomendasi`, `GET /kesehatan` dengan bentuk respons per BRD prediksi dan FSD model data. Contoh harga membawa `model_version` `harga_v1.0.0` dan `dihitung_pada`.
- Web tulis 3 endpoint: `POST /event/masuk` untuk sumber layanan (200 diterima, 200 duplikat_diabaikan, 422 validasi); `POST /umpan-balik` untuk petani/vendor; `POST /rekomendasi/:id/status` untuk CEO/analis dengan catatan.
- Desktop analis 2 endpoint tulis: `POST /api/v1/analis/validasi` dan `POST /api/v1/analis/tahan` memakai `aksi_id` idempoten dan amplop error web yang sama. Desktop tidak menghitung model sendiri; semua angka berasal dari API web.
- Validasi event memakai zod persis mengikuti kontrak 11 event; pesan error Indonesia; 11 fixture wajib lolos sebelum rilis.

## Event dan Galat

- Event: 11 terkunci `harga.diperbarui`, `stok.berubah`, `stok.snapshot_harian`, `transaksi.dibuat`, `lapak.status_berubah`, `panen.dicatat`, `logistik.biaya_diperbarui`, `vendor.status_berubah`, `cuaca.diperbarui`, `kalender.dikunci`, `keuangan.ringkasan_harian`. Setiap event membawa UUID waktu dan versi; tambah field opsional tetap minor; ubah tipe atau makna menaikkan mayor dengan dual-read 30 hari.
- Idempotensi: semua POST tulis membawa kunci (`event_id` atau `aksi_id`); kirim ulang sama kembali 200 duplikat tanpa rekaman ganda.
- Galat baku: `tidak_ditemukan`, `validasi_gagal`, `tidak_berhak`, `kuota_habis`, `data_belum_cukup`, `kesalahan_server`; 401 token kedaluwarsa, 403 peran ditolak, 409 konflik versi, 422 validasi berkode plus field, 429 kuota.

## Batasan

Batasan dokumen ini: hanya kontrak, event, dan galat. Implementasi server ada di repo titeny-backend-service. Konsumen dilarang menulis ke DB sumber; satu-satunya tulis ke DB `titeny`. Tidak ada pengecualian read-only tanpa keputusan TownHall ini.
