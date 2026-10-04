# 10 Papan Insight — Titeny (Varian 1)

> Status: disahkan V1. Bahasa: Indonesia. Nol TBD. Mobile hanya baca via web responsif.

## 1. Peran dan Pertanyaan

| Peran | Pertanyaan utama | KPI utama di papan |
|-------|-----------------|--------------------|
| Petani | Kapan jual / tahan? | harga H+7 + sinyal panen kabupaten |
| Vendor (keliling/lapak) | Stok berapa untuk besok? | demand H+7 zona + harga besok |
| CEO / Operasional | Margin aman? Intervensi di mana? | surplus/defisit peta + margin vs. prediksi |

## 2. Struktur Papan (3 papan, 1 basis)

Basis `/insight` (web responsif): pemilih peran → 3 tampilan.

### Papan Petani (`/insight/petani?komoditas=&kab=`)

1. Kartu harga: harga hari ini, prediksi H+1–H+7 (grafik garis + rentang P10–P90).
2. Kartu keputusan: lencana `JUAL SEKARANG / TAHAN 3 HARI / CICIL JUAL` + 1 kalimat alasan.
3. Kartu cuaca-panen: hujan 7 hari + risiko + panen prediksi M+1–M+4 kabupaten.
4. Tombol aksi: "Tawarkan ke gudang" (membuka form TitipO/Lumbung — Titeny tidak memproses jual-beli).

Aturan keputusan V1 (contoh cabai): jika harga H+3 ≥ +8% vs. hari ini dan kepercayaan ≥ sedang → `TAHAN 3 HARI`; jika diprediksi turun ≥ 8% → `JUAL SEKARANG`; selain itu `CICIL JUAL`.

### Papan Vendor (`/insight/vendor?zona=&komoditas=`)

1. Tabel besok: `komoditas | harga besok | demand H+7 (kg) | saran stok (kg)`.
2. Saran stok = demand harian rerata × 1,15 buffer − stok di tangan (input manual, tersimpan lokal).
3. Peringatan defisit/surplus zona dengan warna: hijau seimbang, kuning waspada, merah defisit.

### Papan CEO (`/insight/ceo`)

1. Peta surplus/defisit 5 kabupaten × 12 komoditas (matriks warna).
2. Kurva omzet vs. margin 30 hari (S10) + anotasi intervensi.
3. Daftar 5 rekomendasi prioritas (dari model panen-demand) dengan status `baru|dikerjakan|selesai`.
4. Jejak akurasi: MAPE H+7 per komoditas 4 minggu terakhir.

## 3. Non-Fungsional

- Mobile: hanya baca; semua papan dapat dibaca pada layar 360 px tanpa scroll horizontal; tidak ada app khusus.
- Akses: petani/vendor via tautan + PIN zona (tanpa password rumit); CEO via akun + OTP.
- Bahasa: Indonesia; angka IDR dengan pemisah titik (42.500).
- Kinerja: First Contentful Paint < 2,5 dtk pada 4G; data prediksi di-cache 6 jam di CDN.
- Aksesibilitas: kontras AA, semua grafik punya tabel teks alternatif.

## 4. Keadaan Kosong dan Error (konkret)

- "Data harga belum cukup" bila S1 kosong ≥ 3 hari.
- "Estimasi — transaksi kemarin belum masuk" bila S3 telat.
- "Akurasi terbatas" (lencana kuning) bila kepercayaan rendah.
- Tidak ada spinner tanpa batas: timeout 12 detik → tampilkan cache terakhir + stempel waktu.

## 5. Batasan V1 yang Disengaja

- Tidak ada jual-beli di Titeny (transaksi tetap di TitipO/Pasaree/Lumbung).
- Tidak ada komentar/sosial; komunikasi via kanal masing-masing sistem asal.
- Tidak ada ekspor massal di web; ekspor CSV/XLSX hanya di desktop analis.
