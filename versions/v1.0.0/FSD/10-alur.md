> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# FSD 10 — Alur Sistem

## Urutan Ingest, Hitung, Tayang

1. Sumber mengirim HTTPS POST ke `/api/v1/event/masuk` dengan `X-Idempotency-Key: <event_id>` dan timeout 10 detik; penerima menjawab diterima, duplikat_diabaikan, atau 422 berkode.
2. Job harga 04.00 menghitung 12 × 8 pasangan H+1–H+7; job demand 04.30 menghitung H+7/H+30; job panen Senin 03.00 menghitung M+1–M+4. Kunci berkas mencegah ganda; gagal 3× memicu log `pager: true`; retry 04.30 dan 05.00 sebelum memakai label basi.
3. Respons prediksi di-cache 6 jam (`max-age=21600`); `/kesehatan` 30 detik. Web memakai stale-while-revalidate 6 jam untuk petani/vendor dan revalidasi 15 menit untuk CEO. SLO baca p95 di bawah 400 ms cache-hit dan p99 di bawah 1.200 ms.
4. Desktop analis polling 15 menit untuk prediksi dan 5 menit untuk antrean validasi; tulis antre saat luring dengan `aksi_id` dan terkirim saat daring. Cache SQLite `%LOCALAPPDATA%/Titeny/analis.db` maks 500 MB; data di atas 90 hari dipangkas FIFO; VACUUM tiap Minggu.
5. Batas darurat: web menampilkan cache plus stempel waktu bila API mati; desktop beralih ke SQLite luring plus banner. Tidak ada jalur tulis langsung ke database dari klien mana pun.

## Urutan Layar Web dan Desktop

- Web: Landing Peran, Masuk, Insight Petani (`/insight/petani`), Insight Vendor (`/insight/vendor`), Insight CEO (`/insight/ceo` plus rekomendasi dan ekspor 500 baris). Grafik memakai SVG Svelte ringan dengan tabel teks alternatif; state memakai `$state`, turunan `$derived`, efek `$effect` hanya untuk polling; dilarang `$:` gaya lama; token tidak disimpan di state global tanpa modul `sesi.ts`.
- Desktop analis: Masuk, AntreanValidasi, DetailFlag (`:eventId` pola `evt_YYYYMMDD_NNNNNN`), TahanPrediksi, BandingModel, KelolaRekomendasi, Ekspor. Graf Navigation3 terkunci, back stack maks 20, deep link `titeny://analis/flag/<eventId>`, state via `rememberSaveable` plus `SavedStateHandle`, tanpa animasi kustom.
- Jendela desktop min 1120×720; tabel padat zebra; status berwarna plus teks; tombol kirim nonaktif sampai alasan valid; shortcut Ctrl+R segarkan, Ctrl+E ekspor, Esc kembali.

## Batasan

Batasan dokumen ini: hanya urutan sistem dan aturan sinkron. Formula bisnis ada di BRD prediksi dan FSD model data. Di luar batas: desain visual dan merek. Target mutu: 11 fixture event lolos validasi dan beban ringan 100 req/detik cache-hit p95 di bawah 400 ms sebelum rilis.
