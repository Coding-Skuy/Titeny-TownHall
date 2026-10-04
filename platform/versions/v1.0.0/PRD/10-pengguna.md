> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# PRD 10 — Pengguna Titeny

## Daftar Peran

- Petani: menjawab kapan jual atau tahan dan sinyal panen kabupaten. Kebutuhan: kartu harga H+1–H+7 plus rentang, lencana JUAL SEKARANG/TAHAN 3 HARI/CICIL JUAL dengan 1 kalimat alasan, kartu cuaca-panen, dan tombol Tawarkan ke gudang yang membuka form Lumbung/TitipO. Masuk via PIN zona, baca via web responsif.
- Vendor keliling/lapak: menjawab stok berapa untuk besok per zona. Kebutuhan: tabel besok berisi komoditas, harga besok, demand H+7, dan saran stok; saran sama dengan rerata harian × 1,15 buffer dikurangi stok tangan; peringatan hijau/kuning/merah. Masuk via PIN zona, baca via web responsif.
- CEO/operasional: menjawab margin aman dan intervensi di mana. Kebutuhan: matriks surplus/defisit 5 kabupaten × 12 komoditas, kurva omzet vs. margin 30 hari dari S10 plus anotasi, 5 rekomendasi prioritas dengan status baru/dikerjakan/selesai, dan jejak akurasi MAPE 4 minggu. Masuk via akun plus OTP.
- Analis internal: satu-satunya peran tulis-terhadap-tayang. Kebutuhan: antrean validasi flag, tahan prediksi maks 7 hari, banding model backtest 90 hari, kelola rekomendasi plus penanggung jawab, dan ekspor CSV/XLSX maks 50.000 baris per berkas beraudit. Masuk via token analis 8 jam plus OTP di desktop Windows.

## Hak Akses

- Petani dan vendor hanya melihat zona dan komoditasnya serta memberi umpan-balik bermanfaat/kurang_tepat maks 280 karakter. CEO melihat seluruh matriks dan mengubah status rekomendasi dengan catatan maks 140 karakter. Analis memutus flag dengan keputusan setuju/koreksi_nilai/tolak plus alasan 10–140 karakter. Tidak ada akun bersama: 1 orang 1 kredensial.
- Autentikasi: JWT akses untuk CEO/analis dengan `aud=titeny` dan `iss=chefgenie-auth`; PIN 6 digit zona untuk petani/vendor; token layanan untuk sumber event; token analis disimpan di Windows Credential Manager.

## Batasan

Batasan dokumen ini: hanya peran, kebutuhan pandang, dan hak akses. Aturan bisnis rinci ada di BRD, langkah sistem ada di FSD. Di luar batas: peran kasir pasar dan operator gudang yang diatur TownHall masing-masing.
