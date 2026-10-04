> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# BRD 20 — Prediksi Harga, Panen, dan Demand

## Konteks

Model menjawab tiga pertanyaan harian: berapa harga wajar H+1–H+7, berapa panen M+1–M+4, dan berapa demand H+7/H+30. Sumber isi lama: `model/10-prediksi-harga.md` dan `model/20-prediksi-panen-demand.md`.

## Kebutuhan Bisnis

- BR-201 Cakupan harga dikunci: 12 komoditas (beras medium, telur ayam, cabai merah, cabai rawit, bawang merah, bawang putih, minyak goreng curah, gula pasir, daging ayam, daging sapi, tomat, kangkung) × 8 pasar rujukan (4 Jabodetabek, 2 Jawa Barat, 1 Jawa Tengah, 1 Jawa Timur), horizon H+1–H+7, hitung 04.00 plus hitung ulang 15.00 bila ada koreksi siang. Di atas H+7 tidak ditampilkan.
- BR-202 Metode harga tanpa deep learning: baseline musiman-naif median 14 hari pada `jenis_hari` sama; koreksi tren regresi 21 hari dibatasi ±8 persen/hari; koreksi stok ±4 persen; koreksi logistik 80 persen selisih diteruskan; potong ke 0,70–1,60× median 30 hari lalu bulatkan ke 500 IDR. Target MAPE H+7 ≤ 12 persen.
- BR-203 Cakupan panen-demand dikunci: panen 6 komoditas × 5 kabupaten (Karawang, Subang, Garut, Brebes, Malang) M+1–M+4 hitung Senin 03.00; demand 12 komoditas per 8 zona TitipO plus 8 pasar Pasaree H+7/H+30 hitung 04.30. Rumus panen memakai luas ekspektasi × yield baseline median 3 musim × faktor cuaca dan musim, dibulatkan ke 50 kg. Rumus demand memakai baseline 28 hari × faktor hari besar × elastisitas harga, dibatasi ±30 persen.
- BR-204 Sinyal dikunci: surplus bila panen ≥ 120 persen demand, defisit bila ≤ 80 persen, selain itu seimbang. Rekomendasi otomatis maks 3 aksi per zona dengan angka dibulatkan ke 100 kg.
- BR-205 Kepercayaan dikunci: tinggi bila sampel ≥ 5 lapak dan MAPE_14h di bawah 8 persen; rendah bila sampel di bawah 3 atau MAPE_14h di atas 15 persen; papan wajib menampilkan lencana kuning Akurasi terbatas bila rendah. Override analis hanya menyembunyikan tayangan, tidak mengubah angka.
- BR-206 Versi model dikunci: `harga_v1.0.0`, `panen_v1.0.0`, `demand_v1.0.0`. Naik versi hanya bila backtest 90 hari menunjukkan MAPE H+7 turun ≥ 0,5 poin dan tidak ada komoditas yang memburuk lebih dari 2 poin.

## Metrik

- MAPE per komoditas-pasar-zona tiap Minggu 23.00, bias over/under, dan cakupan rentang P10–P90 70–90 persen.

## Batasan

Batasan segmen ini: hanya kebutuhan prediksi dan ambang. Fitur input rinci dan kontrak output ada di FSD. Di luar batas: sentimen media sosial, teks bebas, dan training model di desktop analis.
