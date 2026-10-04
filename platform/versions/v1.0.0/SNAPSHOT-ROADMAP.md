> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# SNAPSHOT-ROADMAP v1.0.0 — Salinan Beku

Salinan beku janji v1.0.0 pada 04 Okt 2026. Tidak diubah lagi. Perubahan masa depan dicatat di `roadmap/` living dan dirilis sebagai versi baru.

## Janji Beku

- Skala tayang: prediksi harga 12 komoditas (beras medium, telur ayam, cabai merah, cabai rawit, bawang merah, bawang putih, minyak goreng curah, gula pasir, daging ayam, daging sapi, tomat, kangkung) × 8 pasar rujukan (4 Jabodetabek, 2 Jawa Barat, 1 Jawa Tengah, 1 Jawa Timur), horizon H+1–H+7, hitung 04.00 plus hitung ulang 15.00 bila ada koreksi siang.
- Panen: 6 komoditas (padi, jagung, cabai merah, cabai rawit, bawang merah, tomat) × 5 kabupaten (Karawang, Subang, Garut, Brebes, Malang), horizon M+1–M+4, hitung Senin 03.00. Demand: 12 komoditas, H+7 dan H+30, hitung 04.30.
- Ingest: 11 event berversi idempoten via `POST /api/v1/event/masuk` dengan `X-Idempotency-Key`, retry 5/30/300 detik lalu DLQ, urutan tidak dijamin dengan toleransi keterlambatan 24 jam.
- Data: read-only terhadap DB sumber; satu-satunya tulis ke DB `titeny`; retensi mentah 365 hari dan agregat 3 tahun.
- Papan: 3 papan (petani, vendor, CEO) baca-pertama pada web responsif 360 px, PIN zona untuk petani/vendor, akun plus OTP untuk CEO, cache prediksi 6 jam.
- Analis: desktop Windows MSI 1.0.0, 5 layar terkunci, polling 15/5 menit, SQLite lokal maks 500 MB, token 8 jam via OTP.
- Sistem: web Bun latest (baseline 1.4.x) + Svelte 5 + SvelteKit 2 + TypeScript latest (baseline 5.9.x); analis-desktop KMP Windows; backend Rust axum 0.8.4; model dan pipeline Python 3.12. Autentikasi JWT `aud=titeny`, `iss=chefgenie-auth`.
- Sukses 90 hari: MAPE H+1 ≤ 8 persen, H+7 ≤ 12 persen, demand H+7 ≤ 15 persen, 150 petani dan 80 vendor aktif mingguan, 50 persen rekomendasi selesai dalam 14 hari.

## Sumber

- Dibekukan dari `data/`, `model/`, `produk/`, `platform/`, dan `metrik/` varian 1 yang disahkan 04 Okt 2026. File lama sudah dipindah dan dihapus dari lokasi asal.

## Batasan

Batasan dokumen ini: hanya salinan janji saat v1.0.0 disetujui. Tidak menjadi acuan operasional terkini; acuan terkini ada di `roadmap/TIMELINE.md` dan `roadmap/MILESTONE.md`.
