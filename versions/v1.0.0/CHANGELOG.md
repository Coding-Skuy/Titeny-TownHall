> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# CHANGELOG v1.0.0 — Versi Awal Titeny

## Ringkasan Isi

v1.0.0 adalah versi awal TownHall Titeny yang dibekukan mengikuti template emas. Seluruh isi lama dari folder `data/`, `model/`, `produk/`, `platform/`, dan `metrik/` dipecah dan dipindah ke struktur versi ini, lalu folder lama dihapus agar hanya ada satu sumber kebenaran.

## Isi per Direktori

- BRD: `00-ikhtisar.md` memuat peran AI Insight konsumen 3 divisi, skala 12 komoditas × 8 pasar H+1–H+7, prinsip read-only terhadap DB sumber, DB `titeny`, dan JWT `aud=titeny`. `10-sumber-data.md` memuat 10 sumber S1–S10 dan matriks keterlacakan. `20-prediksi.md` memuat metode harga tanpa deep learning dan metode panen-demand plus sinyal surplus/defisit. `30-akurasi-dampak.md` memuat target MAPE, adopsi 90 hari, dan pelaporan dampak ke Lumbung.
- PRD: `10-pengguna.md` memuat 4 peran: petani, vendor, CEO/operasional, analis. `20-alur.md` memuat 3 papan insight dan keadaan kosong/error. `30-kriteria.md` memuat US-001 dan seterusnya, acceptance, dan non-goals.
- FRD: `10-fungsional.md` memuat FR-001 dan seterusnya untuk ingest 11 event idempoten, prediksi, papan, dan kurasi analis, tanpa cara implementasi.
- FSD: `10-alur.md` memuat urutan sistem ingest-hitung-tayang dan aturan sinkron desktop. `20-model-data.md` memuat entitas event_masuk, prediksi_harga, prediksi_panen, rekomendasi, dan aturan angka. `30-kontrak.md` memuat kontrak API web Bun dan desktop analis, 11 event, idempotensi, galat baku, dan autentikasi JWT `aud=titeny` plus PIN zona.
- `SNAPSHOT-ROADMAP.md` memuat salinan beku janji tayang 90 hari.

## Sumber Pemindahan

- `data/10-sumber-data.md` menjadi BRD sumber-data. `data/20-kontrak-event.md` menjadi BRD sumber-data bagian event dan FSD kontrak bagian event.
- `model/10-prediksi-harga.md` dan `model/20-prediksi-panen-demand.md` menjadi BRD prediksi.
- `produk/10-papan-insight.md` menjadi PRD pengguna dan alur. `produk/21-kontrak-api-web-bun.md` dan `produk/22-kontrak-desktop-windows.md` menjadi FSD kontrak dan FSD alur.
- `platform/10-matriks-web-desktop.md`, `platform/20-navigasi3.md`, `platform/30-desktop-windows-analis.md`, `platform/50-web-bun-svelte.md` menjadi FSD alur dan FSD kontrak bagian platform.
- `metrik/10-akurasi-dan-adopsi.md` menjadi BRD akurasi-dampak dan PRD kriteria.

## Batasan

Batasan versi ini: hanya insight 12 komoditas × 8 pasar H+1–H+7, panen M+1–M+4 untuk 5 kabupaten, demand H+7/H+30, 11 event idempoten, DB `titeny` sebagai satu-satunya tulis, dan JWT `aud=titeny`. Perubahan setelah ini wajib masuk v1.1.0 atau v2.0.0 dan dicatat di `roadmap/` living, bukan dengan mengubah file beku ini.
