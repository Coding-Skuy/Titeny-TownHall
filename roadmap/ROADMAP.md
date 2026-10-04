> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# ROADMAP Titeny — Rencana ke Depan

Dokumen living. Janji v1.0.0 yang sudah beku tidak diubah di sini; perubahan masa depan dirilis sebagai versi baru.

## v1.1.0 — Penguatan Akurasi dan Adopsi

- Tujuan: menekan MAPE H+7 harga ke median ≤ 10 persen dan mencapai 150 petani aktif mingguan.
- Isi rencana: kalibrasi ulang rentang P10–P90 bila cakupan di bawah 60 persen, retry job 04.00 yang lebih agresif, lencana akurasi yang lebih jelas di papan petani, dan ekspor analis terjadwal mingguan ke Lumbung keuangan.
- Prasyarat rilis: M-003 dan M-005 berstatus done selama 4 minggu berturut-turut.

## v2.0.0 — Skala Komoditas dan Kabupaten

- Tujuan: tambah 2 komoditas prediksi harga dan 2 kabupaten prediksi panen tanpa melanggar janji read-only terhadap DB sumber.
- Isi rencana: dual-read skema event v2 selama 30 hari bila ada field baru, backtest 90 hari wajib sebelum naik versi model, dan MSI analis 2.0 dengan banding model yang lebih kaya.
- Prasyarat rilis: akurasi v1.0.0 memenuhi target 90 hari dan audit log analis bersih.

## Non-Tujuan Roadmap

- Tidak menambah penulisan ke DB sumber dalam bentuk apa pun. Titeny tetap read-only terhadap Lumbung, TitipO, dan Pasaree; satu-satunya tulis ke DB `titeny`.
- Tidak membuka transaksi jual-beli di Titeny sebelum v2.0.0 disetujui.

## Batasan

Batasan dokumen ini: hanya arah rencana dan prasyarat versi. Keputusan bisnis rinci tiap versi masa depan wajib ditulis ulang di `versions/vX.Y.Z/` masing-masing. Dokumen ini tidak menjadi kontrak API atau acuan pembayaran.
