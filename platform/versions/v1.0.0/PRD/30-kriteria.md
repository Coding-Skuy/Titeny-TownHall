> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# PRD 30 — Kriteria Keberhasilan

## Cerita Pengguna dan Acceptance

- US-001 Sebagai petani saya melihat prediksi H+1–H+7 sehingga tahu kapan jual. Acceptance: grafik plus rentang P10–P90 tampil; kepercayaan rendah menampilkan lencana Akurasi terbatas; Data harga belum cukup tampil bila S1 kosong ≥ 3 hari.
- US-002 Sebagai vendor saya melihat saran stok besok sehingga tidak kehabisan. Acceptance: tabel memuat harga besok, demand H+7, dan saran; warna sinyal benar; Estimasi tampil bila S3 telat 1 hari.
- US-003 Sebagai CEO saya melihat peta surplus/defisit sehingga tahu intervensi. Acceptance: matriks 5 × 12 tampil; 5 rekomendasi prioritas dapat diubah statusnya dengan catatan; jejak MAPE 4 minggu tampil.
- US-004 Sebagai analis saya memutus flag sehingga angka meragukan tidak tayang. Acceptance: keputusan butuh alasan 10–140 karakter; tahan maks 7 hari lalu kedaluwarsa; tombol Naikkan versi aktif hanya bila MAPE H+7 turun ≥ 0,5 poin dan tidak ada komoditas memburuk lebih dari 2 poin.
- US-005 Sebagai sistem saya mengingest 11 event idempoten sehingga tidak ada ganda. Acceptance: kirim ulang `event_id` sama kembali duplikat; `event_type` di luar daftar ditolak; validasi gagal kembali 422 dengan kode dan field.
- US-006 Sebagai Titeny saya tetap read-only terhadap DB sumber sehingga aman. Acceptance: audit 30 hari menunjukkan nol tulis ke DB Lumbung/TitipO/Pasaree; satu pool ke DB `titeny`; JWT salah `aud` ditolak.

## Non-Goals v1.0.0

- Tanpa jual-beli di Titeny; tanpa komentar sosial; tanpa ekspor massal di web; tanpa training model di desktop; tanpa app mobile khusus; tanpa grafik echarts (memakai SVG Svelte ringan).

## Batasan

Batasan dokumen ini: hanya kriteria produk yang dapat diuji. Rincian teknis API dan skema ada di FSD. Klaim sukses tanpa skor mingguan MAPE, adopsi, dan audit read-only dinyatakan tidak berlaku.
