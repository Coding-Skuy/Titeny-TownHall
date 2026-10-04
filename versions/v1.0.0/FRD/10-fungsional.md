> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# FRD 10 — Kebutuhan Fungsional

Dokumen ini menyatakan apa yang wajib dilakukan sistem, tanpa menyatakan cara implementasi.

## Ingest Event

- FR-001 Sistem wajib menerima 11 event berversi melalui satu pintu masuk dengan amplop `event_id`, `event_type`, `schema_version`, `source`, `occurred_at`, `received_at`, `batch_id`, dan `payload`.
- FR-002 Sistem wajib menolak `event_type` di luar daftar terkunci dengan `event_type_tidak_dikenal`.
- FR-003 Sistem wajib idempoten: duplikat `event_id` kembali duplikat tanpa rekaman ganda, memakai kunci per tipe (tanggal, id transaksi, id panen, gudang plus tanggal).
- FR-004 Sistem wajib memvalidasi payload per tipe (harga positif dan sampel ≥ 3; stok tidak negatif; qty maks 50 kg per transaksi; hasil/ha 500–15.000 kg/ha) dan mengembalikan 422 berkode bila gagal.
- FR-005 Sistem wajib menangani keterlambatan hingga 24 jam dengan rekalkulasi agregat H-1 dan antrean mati setelah retry 5, 30, 300 detik.

## Prediksi

- FR-101 Sistem wajib menghitung harga 12 komoditas × 8 pasar H+1–H+7 tiap 04.00 plus hitung ulang 15.00 bila ada koreksi, memakai metode 5 langkah dan batas 0,70–1,60× median 30 hari.
- FR-102 Sistem wajib menghitung panen mingguan M+1–M+4 tiap Senin 03.00 dan demand H+7/H+30 tiap 04.30 beserta sinyal surplus/seimbang/defisit.
- FR-103 Sistem wajib memberi `kepercayaan` tinggi/sedang/rendah, `rentang_bawah/atas` P10/P90 residual 30 hari, `model_version`, dan `alasan_singkat` pada tiap output.
- FR-104 Sistem wajib menerapkan fallback: median 7 hari plus label data kemarin bila S1 kosong 1 hari; hentikan pasangan itu bila kosong ≥ 3 hari; pakai prediksi H-1 berlabel basi bila job gagal setelah retry 04.30 dan 05.00.
- FR-105 Sistem wajib mengunci versi model dan menaikkan versi hanya bila syarat backtest 90 hari terpenuhi.

## Papan dan Kurasi

- FR-201 Sistem wajib menayangkan 3 papan peran dengan aturan lencana, saran stok, matriks CEO, dan 5 rekomendasi prioritas.
- FR-202 Sistem wajib mencatat umpan-balik bermanfaat/kurang_tepat plus komentar maks 280 karakter tanpa mengubah model otomatis.
- FR-203 Sistem wajib mengizinkan analis menahan prediksi maks 7 hari, memutus flag dengan alasan 10–140 karakter, dan mengekspor massal beraudit.
- FR-204 Sistem wajib menegakkan hak peran dan batas baca web 500 baris vs. desktop 50.000 baris.

## Lintas Segmen

- FR-301 Sistem wajib read-only terhadap DB sumber dan hanya menulis ke DB `titeny`.
- FR-302 Sistem wajib mencatat setiap aksi analis berisi siapa, kapan, apa, dan alasan dalam log audit 2 tahun yang tidak dapat dihapus analis.
- FR-303 Sistem wajib memakai Bahasa Indonesia untuk pesan, format IDR bertitik, dan waktu Asia/Jakarta.

## Batasan

Batasan dokumen ini: hanya kebutuhan fungsional. Bahasa pemrograman, pustaka, basis data lokal, dan pola navigasi tidak diatur di sini dan hanya boleh muncul di FSD. Setiap kebutuhan di atas wajib punya uji penerimaan di PRD/30-kriteria.md.
