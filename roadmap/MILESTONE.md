> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# MILESTONE Titeny — Status Living

Legenda status: todo berarti belum mulai, doing berarti sedang berjalan, done berarti selesai terverifikasi.

## Milestone v1.0.0

- M-001 Ingest 11 event berversi idempoten berjalan — status: done. Bukti: 11 fixture event lolos validasi zod dan uji kirim ulang kembali duplikat tanpa baris ganda.
- M-002 Prediksi harga 12 komoditas × 8 pasar H+1–H+7 tayang tiap 04.00 — status: done. Bukti: job 04.00 sukses 7 hari berturut dan papan petani menampilkan grafik plus rentang.
- M-003 MAPE H+7 harga median ≤ 12 persen selama 4 minggu — status: doing. Target: tidak ada komoditas yang memburuk lebih dari 2 poin antar rilis.
- M-004 Papan 3 peran dapat dibaca pada 360 px tanpa scroll horizontal — status: done. Bukti: uji 360 px dan tabel teks alternatif tiap grafik lolos.
- M-005 150 petani dan 80 vendor aktif mingguan pada hari ke-90 — status: doing. Bukti: hitung PIN unik mingguan dari event `papan.dibuka`.
- M-006 Desktop analis Windows MSI 1.0.0 terpasang dan antrean validasi berjalan — status: doing. Bukti: 1 flag `perlu_verifikasi` diputus penuh dari antrean sampai audit.
- M-007 DB `titeny` satu-satunya tulis; nol tulis ke DB sumber — status: doing. Bukti: audit string koneksi dan log 30 hari tanpa INSERT/UPDATE/DELETE di luar DB `titeny`.
- M-008 Laporan dampak bulanan pertama ke Lumbung keuangan — status: todo. Syarat mulai: 30 hari prediksi dan S10 lengkap.

## Aturan Pembaruan

- Status diubah hanya oleh Analis Penanggung Jawab dengan bukti tanggal. Milestone yang sudah done tidak dihapus, hanya ditambah catatan verifikasi.

## Batasan

Batasan dokumen ini: hanya status milestone dan bukti ringkas. Rincian angka ada di `versions/v1.0.0/BRD/` dan `versions/v1.0.0/PRD/30-kriteria.md`. Dokumen ini tidak mengubah janji beku v1.0.0.
