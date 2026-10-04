> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# Titeny-TownHall — Divisi AI Insight (Konsumen Data 3 Divisi) PT ChefGenie

## Peran Titeny

Titeny adalah divisi AI Insight PT ChefGenie: mengubah data Lumbung, TitipO, dan Pasaree menjadi keputusan harian petani, vendor, dan CEO. Setiap pola menceritakan sebuah kisah — papan insight prediksi harga dan panen untuk 12 komoditas × 8 pasar pada horizon H+1–H+7, plus panen mingguan M+1–M+4 dan demand H+7/H+30. Titeny tidak menghimpun data mentah sendiri dan read-only terhadap DB sumber: tidak pernah menulis ke DB Lumbung, TitipO, atau Pasaree. Satu-satunya basis tulis adalah DB `titeny` (tabel `event_masuk`, `prediksi_harga`, `prediksi_panen`). Ingest mengunci 11 event berversi yang idempoten via `event_id` dan `X-Idempotency-Key`. Autentikasi: JWT `aud=titeny`, `iss=chefgenie-auth` untuk CEO/analis/layanan, plus PIN 6 digit zona untuk petani/vendor baca via web responsif. Analis internal mengkurasi via aplikasi desktop Windows; satu-satunya peran tulis-terhadap-tayang. Bahasa: Indonesia.

## Peta Versi Aktif

- Versi aktif: v1.0.0 (disetujui). Isi beku ada di `versions/v1.0.0/`.
- `versions/v1.0.0/CHANGELOG.md` — ringkasan versi awal.
- `versions/v1.0.0/BRD/` — kebutuhan bisnis BR-001 dan seterusnya.
- `versions/v1.0.0/PRD/` — pengguna dan kriteria US-001 dan seterusnya.
- `versions/v1.0.0/FRD/` — kebutuhan fungsional FR-001 dan seterusnya.
- `versions/v1.0.0/FSD/` — rancangan alur, model data event_masuk dan prediksi, dan kontrak API.
- `versions/v1.0.0/SNAPSHOT-ROADMAP.md` — salinan beku janji v1.0.0.
- Peta hidup lintas versi ada di `roadmap/`: `TIMELINE.md`, `MILESTONE.md`, `ROADMAP.md`.

## Cara Baca History

1. Mulai dari `versions/v1.0.0/CHANGELOG.md` untuk ringkasan versi.
2. Lanjut ke `versions/v1.0.0/BRD/00-ikhtisar.md` untuk konteks bisnis, lalu `PRD/10-pengguna.md` untuk peran.
3. Untuk janji waktu itu, baca `versions/v1.0.0/SNAPSHOT-ROADMAP.md` yang sudah dibekukan dan tidak diubah lagi.
4. Untuk kondisi terkini lintas versi, baca `roadmap/TIMELINE.md` dan `roadmap/MILESTONE.md`.
5. Riwayat perubahan antar versi dilacak lewat `git log` dan `CHANGELOG.md` tiap versi. File lama sengaja dihapus setelah dipindah agar tidak ada dua sumber kebenaran.

## TownHall Lain dan Pedoman Induk

Pedoman induk: https://github.com/Coding-Skuy/ChefGenie-TownHall.

Lima TownHall lain yang memakai pola template emas yang sama:

- https://github.com/Coding-Skuy/Lumbung-TownHall — hulu dan supply, pemilik DB Lumbung dan event `stok.*`, `panen.dicatat`, `keuangan.ringkasan_harian`.
- https://github.com/Coding-Skuy/Pawonee-TownHall — dapur dan pengolahan.
- https://github.com/Coding-Skuy/Pasaree-TownHall — pasar dan penjualan, pemilik event `harga.diperbarui`, `lapak.status_berubah`.
- https://github.com/Coding-Skuy/Pedaree-TownHall — pengantar dan last-mile.
- https://github.com/Coding-Skuy/TitipO-TownHall — titip dan kemitraan, pemilik event `transaksi.dibuat`, `vendor.status_berubah`.

Pola yang ditiru: penamaan `versions/vX.Y.Z/BRD|PRD|FRD|FSD/`, file `NN-nama-kebab.md`, header versi satu baris, dan bagian Batasan di tiap file.

## Batasan

Batasan ruang lingkup repo ini: hanya insight prediksi harga H+1–H+7 untuk 12 komoditas × 8 pasar, prediksi panen M+1–M+4 dan demand H+7/H+30, papan insight 3 peran, kontrak API web Bun dan desktop analis Windows, serta metrik akurasi dan adopsi. Di luar batas: penghimpunan data mentah milik Lumbung/TitipO/Pasaree, transaksi jual-beli milik sistem asal, penentuan resep dapur milik Pawonee, routing last-mile milik Pedaree, dan skema titip milik TitipO. Titeny tidak menulis ke DB sumber dalam keadaan apa pun.
