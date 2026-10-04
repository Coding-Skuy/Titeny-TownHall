> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# BRD 00 — Ikhtisar Titeny

## Konteks

Divisi Titeny adalah AI Insight PT ChefGenie. Tugasnya mengubah data tiga divisi — Lumbung (stok, panen, keuangan), TitipO (transaksi, rute, vendor keliling), Pasaree (harga, lapak, produsen) — ditambah cuaca BMKG dan kalender internal menjadi keputusan harian. Konsumen: petani (kapan jual/tahan), vendor (stok besok per zona), CEO/operasional (peta surplus/defisit dan margin). Basis tulis tunggal: DB `titeny`. Titeny read-only terhadap DB sumber dan tidak pernah menjalankan INSERT/UPDATE/DELETE di luar DB `titeny`.

## Kebutuhan Bisnis

- BR-001 Titeny wajib menayangkan prediksi harga wajar untuk 12 komoditas prioritas × 8 pasar rujukan pada horizon H+1–H+7 setiap hari.
- BR-002 Titeny wajib menayangkan prediksi panen mingguan M+1–M+4 dan demand H+7/H+30 beserta sinyal surplus, seimbang, atau defisit.
- BR-003 Titeny wajib mengingest 11 event berversi secara idempoten; duplikat `event_id` diabaikan dengan respons duplikat dan tanpa baris ganda.
- BR-004 Titeny wajib read-only terhadap DB Lumbung, TitipO, dan Pasaree; satu-satunya basis tulis adalah DB `titeny`.
- BR-005 Autentikasi dikunci: JWT `aud=titeny`, `iss=chefgenie-auth` untuk CEO/analis/layanan; PIN 6 digit zona untuk petani/vendor baca via web.
- BR-006 Sukses diukur sebagai akurasi plus adopsi: MAPE H+7 harga ≤ 12 persen dan 150 petani serta 80 vendor aktif mingguan pada hari ke-90.
- BR-007 Bahasa Indonesia, angka IDR integer dengan pemisah titik, waktu ISO-8601 Asia/Jakarta, tanpa data pribadi (nama, alamat, telepon) masuk Titeny.

## Metrik

- MAPE H+1/H+7 harga, MAPE demand H+7/H+30, MAPE panen per musim, cakupan rentang P10–P90. Pengguna aktif mingguan, umpan-balik, tindak lanjut rekomendasi, waktu-ke-insight. Selisih margin vs. baseline dilaporkan bulanan ke Lumbung keuangan.

## Batasan

Batasan dokumen ini: hanya menyatakan kebutuhan bisnis dan angka ambang. Cara pemenuhan diatur di PRD, FRD, dan FSD. Di luar batas: penghimpunan data mentah, transaksi jual-beli, dan audit independen sistem sumber yang menjadi milik TownHall masing-masing.
