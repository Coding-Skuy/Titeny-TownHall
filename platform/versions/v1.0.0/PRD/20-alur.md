> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# PRD 20 — Alur Produk

## Alur Insight sampai Tindak Lanjut

1. Masuk via peran: petani/vendor memakai tautan plus PIN zona; CEO memakai akun plus OTP; analis memakai desktop bila token kedaluwarsa kembali ke Masuk.
2. Lihat papan `/insight`: pemilih peran membuka `/insight/petani`, `/insight/vendor`, atau `/insight/ceo` dengan query `komoditas_id` dan `pasar_id`/zona/kabupaten.
3. Ambil keputusan: petani mengikuti lencana (contoh cabai: H+3 ≥ +8 persen dan kepercayaan ≥ sedang berarti TAHAN 3 HARI; turun ≥ 8 persen berarti JUAL SEKARANG; selain itu CICIL JUAL); vendor menyiapkan saran stok; CEO mengubah rekomendasi baru menjadi dikerjakan lalu selesai.
4. Umpan-balik dan kurasi: petani/vendor menilai bermanfaat/kurang_tepat; analis menahan prediksi meragukan maks 7 hari atau mengoreksi nilai via desktop; penahanan kedaluwarsa otomatis.
5. Ekspor terbatas: web maks 500 baris; analis mengekspor massal via desktop dengan nama `titeny_<jenis>_YYYYMMDD_HHmm.xlsx` dan hash SHA-256 tercatat.

## Contoh Nyata

Petani cabai Karawang membuka papan pagi: harga hari ini Rp42.000, prediksi H+3 naik 9 persen dengan kepercayaan sedang sehingga lencana TAHAN 3 HARI tampil beserta alasan stok_gudang_72_persen_dari_rerata. Vendor Bekasi Utara melihat demand H+7 840 kg sinyal defisit dan menyiapkan buffer 15 persen. CEO melihat matriks merah pada cabai dan menandai rekomendasi Amankan pasokan dari Brebes menjadi dikerjakan.

## Batasan

Batasan alur ini: hanya lihat, putus, umpan-balik, dan ekspor. Di luar batas: jual-beli yang tetap di TitipO/Pasaree/Lumbung, komentar sosial, dan ekspor massal via web. Tanpa spinner tanpa batas: timeout 12 detik menampilkan cache terakhir plus stempel waktu.
