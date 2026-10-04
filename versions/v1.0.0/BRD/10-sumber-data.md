> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# BRD 10 — Sumber Data

## Konteks

Titeny tidak menghimpun data mentah sendiri. Semua data analitik berasal dari tiga sistem asal plus 2 referensi. Sumber isi lama: `data/10-sumber-data.md` dan `data/20-kontrak-event.md` bagian daftar event.

## Kebutuhan Bisnis

- BR-101 Titeny wajib memakai 10 sumber terkunci S1–S10: S1 harga pasar harian Pasaree (960 titik/hari, 06.00–08.00 plus koreksi 14.00, SLA telat 3 jam, kosong maks 2 persen); S2 stok dan mutasi gudang Lumbung (real-time plus snapshot 23.00, event di bawah 5 menit, stok tidak negatif, selisih di atas 1 persen flag rekonsiliasi); S3 transaksi komunitas TitipO (400–1.500/hari, real-time, tanpa nama pembeli); S4 katalog dan lapak aktif Pasaree; S5 catatan panen Lumbung plus koreksi TitipO (validasi 500–15.000 kg/ha); S6 biaya logistik TitipO plus Pasaree; S7 vendor dan produsen; S8 cuaca BMKG harian 05.00; S9 kalender tanam dan hari besar dikunci tiap 1 Desember; S10 ringkasan keuangan harian Lumbung 23.45 final.
- BR-102 Konvensi dikunci: zona Asia/Jakarta, ISO-8601, IDR integer, kg 2 desimal, field `snake_case`, `schema_version` mulai 1, tiap batch membawa `batch_id` dan `jumlah_baris` dengan flag bila selisih di atas 5 persen vs. rerata 7 hari.
- BR-103 Ingest mengunci 11 event: `harga.diperbarui`, `stok.berubah`, `stok.snapshot_harian`, `transaksi.dibuat`, `lapak.status_berubah`, `panen.dicatat`, `logistik.biaya_diperbarui`, `vendor.status_berubah`, `cuaca.diperbarui`, `kalender.dikunci`, `keuangan.ringkasan_harian`. Event di luar daftar ditolak dengan `event_type_tidak_dikenal`.
- BR-104 Setiap event idempoten: `harga.diperbarui` per pasar plus komoditas plus tanggal; `transaksi.dibuat` per `transaksi_id`; `panen.dicatat` per `panen_id`; `stok.snapshot_harian` per gudang plus tanggal; `keuangan.ringkasan_harian` per tanggal; duplikat `event_id` format `evt_YYYYMMDD_NNNNNN` wajib diabaikan.
- BR-105 Retensi mentah 365 hari dan agregat 3 tahun, lalu arsip dingin JSONL gzip dengan checksum SHA-256 dan manifest per `batch_id`.

## Metrik

- Keterlambatan per sumber vs. SLA, kekosongan S1, antrean DLQ, dan penolakan 422 per hari.

## Batasan

Batasan segmen ini: hanya daftar sumber, frekuensi, SLA, dan kunci idempoten. Skema payload rinci ada di FSD kontrak. Di luar batas: perbaikan data di sistem asal dan penambahan sumber baru tanpa pembaruan dokumen ini.
