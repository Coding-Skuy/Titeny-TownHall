# 10 Sumber Data — Titeny AI Insight Platform (Varian 1)

> Status: disahkan V1. Bahasa: Indonesia. Nol TBD — semua sumber konkret dan terikat ke sistem asal.

Titeny tidak menghimpun data mentah sendiri. Semua data analitik berasal dari tiga sistem asal:
**Lumbung** (stok, gudang, keuangan), **TitipO** (transaksi komunitas + vendor keliling),
**Pasaree** (katalog pasar, harga lapak, produsen lokal). Ditambah 2 sumber eksternal
(cuaca, kalender) yang dicatat sebagai data referensi.

## Konvensi Umum

- Zona waktu: `Asia/Jakarta` (WIB). Format tanggal: ISO-8601 (`2026-10-04T07:00:00+07:00`).
- Mata uang: IDR (integer rupiah, tanpa desimal).
- Satuan berat: kilogram (kg) dengan 2 desimal; satuan volume: liter (L).
- Bahasa field event: `snake_case`. Versi skema: `schema_version` integer mulai dari `1`.
- Setiap sumber punya pemilik data (owner) dan SLA keterlambatan maksimum.

## Daftar 10 Sumber (S1–S10)

### S1. Harga pasar harian — Pasaree

- Asal: `Pasaree-TownHall` → modul katalog & lapak.
- Isi: `komoditas_id`, `pasar_id`, `harga_per_kg`, `satuan`, `waktu_catat`.
- Volume: ±120 komoditas × 8 pasar = 960 titik harga/hari.
- Frekuensi: harian pukul 06.00–08.00 WIB, plus koreksi maksimal 1× pukul 14.00.
- SLA: keterlambatan maks 3 jam dari jadwal; kekosongan data maks 2%.
- Kualitas V1: rentang wajar per komoditas (contoh: cabai 15.000–120.000/kg);
  di luar rentang → ditandai `perlu_verifikasi`, tidak dibuang.

### S2. Stok dan mutasi gudang — Lumbung

- Asal: `Lumbung-Backend` → inventori gudang.
- Isi: `gudang_id`, `komoditas_id`, `stok_kg`, `mutasi_masuk`, `mutasi_keluar`, `susut_kg`.
- Frekuensi: mutasi real-time (event), snapshot stok 1×/hari pukul 23.00.
- SLA: event masuk < 5 menit dari transaksi gudang.
- Aturan: stok tidak boleh negatif; selisih snapshot vs. mutasi > 1% → flag rekonsiliasi.

### S3. Transaksi komunitas — TitipO

- Asal: `TitipO-TownHall` → pemesanan rumah tangga ke vendor keliling.
- Isi: `transaksi_id`, `komoditas_id`, `qty_kg`, `harga_satuan`, `vendor_id`, `zona_rute`.
- Volume acuan V1: 400–1.500 transaksi/hari.
- Frekuensi: real-time (event `transaksi.dibuat`), agregat harian pukul 23.30.
- Privasi: nama pembeli tidak dibawa ke Titeny; hanya `zona_rute` + id anonim.

### S4. Katalog dan lapak aktif — Pasaree

- Asal: `Pasaree-TownHall` → katalog produk + status lapak.
- Isi: `lapak_id`, `komoditas_id`, `status` (`aktif|tutup|libur`), `jam_buka`, `lokasi_pasar`.
- Frekuensi: perubahan status real-time; snapshot 1×/hari.
- Fungsi: penyebut ketersediaan (supply footprint) untuk prediksi demand.

### S5. Catatan panen petani — Lumbung + input TitipO

- Asal: form panen Lumbung (utama) + koreksi dari koordinator TitipO.
- Isi: `petani_id_anonim`, `komoditas_id`, `lahan_ha`, `hasil_kg`, `tanggal_panen`, `mutu` (`A|B|C`).
- Frekuensi: per kejadian panen; agregat mingguan per komoditas per kabupaten.
- Validasi: `hasil_kg / lahan_ha` harus dalam 500–15.000 kg/ha (padi/sayur);
  di luar itu wajib ada catatan `keterangan`.

### S6. Biaya logistik dan rute — TitipO + Pasaree

- Asal: rute vendor TitipO + ongkos angkut pasar Pasaree.
- Isi: `rute_id`, `jarak_km`, `biaya_per_kg`, `moda` (`motor|mobil_pickup|truk`), `waktu_tempuh_menit`.
- Frekuensi: master rute diubah manual; biaya diperbarui tiap ada perubahan BBM > 5%.
- Acuan V1: biaya dasar 1.200 IDR/kg untuk < 10 km motor; 800 IDR/kg untuk pickup > 10 km.

### S7. Data vendor dan produsen — TitipO + Pasaree

- Asal: registrasi vendor (TitipO) + produsen lokal (Pasaree).
- Isi: `vendor_id`, `jenis` (`keliling|lapak|produsen`), `zona_operasi`, `kapasitas_harian_kg`, `status_aktif`.
- Frekuensi: perubahan status real-time; profil lengkap diperbarui maks 30 hari sekali.
- Aturan: vendor nonaktif > 30 hari tidak masuk denominator kapasitas.

### S8. Cuaca harian per kabupaten — referensi eksternal (BMKG)

- Isi: `kabupaten_id`, `tanggal`, `curah_hujan_mm`, `suhu_avg_c`, `peringatan` (`tidak_ada|banjir|kekeringan`).
- Frekuensi: harian pukul 05.00 WIB; peringatan diteruskan sebagai event prioritas.
- Fungsi: fitur koreksi panen dan risiko gagal panen; bukan penentu tunggal harga.

### S9. Kalender tanam dan hari besar — referensi internal

- Isi: `tanggal`, `jenis_hari` (`biasa|puasa|lebaran|natal|tahun_baru|panen_raya`), `musim` (`hujan|kemarau|peralihan`).
- Frekuensi: master tahunan, dikunci tiap 1 Desember untuk tahun berikutnya.
- Fungsi: fitur musiman untuk prediksi harga dan demand (efek puasa/lebaran = +18–35% demand cabai/daging).

### S10. Ringkasan keuangan harian — Lumbung

- Asal: `Lumbung-Backend` → buku kas + margin.
- Isi: `tanggal`, `omzet_idr`, `margin_kotor_idr`, `biaya_operasional_idr`, `piutang_idr`.
- Frekuensi: 1×/hari pukul 23.45 WIB, final (tidak ada koreksi H+1 kecuali `koreksi_keuangan`).
- Fungsi: denominator metrik adopsi vs. dampak (apakah insight menaikkan margin).

## Matriks Keterlacakan

| ID | Sistem asal | Event utama | Konsumen model |
|----|-------------|-------------|----------------|
| S1 | Pasaree | `harga.diperbarui` | prediksi harga |
| S2 | Lumbung | `stok.berubah`, `stok.snapshot_harian` | harga + panen/demand |
| S3 | TitipO | `transaksi.dibuat` | demand |
| S4 | Pasaree | `lapak.status_berubah` | demand |
| S5 | Lumbung | `panen.dicatat` | panen |
| S6 | TitipO/Pasaree | `logistik.biaya_diperbarui` | harga (koreksi) |
| S7 | TitipO/Pasaree | `vendor.status_berubah` | demand (kapasitas) |
| S8 | BMKG | `cuaca.diperbarui` | panen (risiko) |
| S9 | Internal | `kalender.dikunci` | harga + demand (musiman) |
| S10 | Lumbung | `keuangan.ringkasan_harian` | metrik dampak |

## Aturan Keras V1

1. Tidak ada sumber baru tanpa memperbarui dokumen ini dan `data/20-kontrak-event.md`.
2. Data pribadi (nama, alamat, telepon) dilarang masuk Titeny.
3. Setiap batch harian wajib membawa `batch_id` dan `jumlah_baris`; selisih > 5% vs. rerata 7 hari → flag.
4. Retensi mentah 365 hari, agregat 3 tahun. Setelah itu arsip dingin (lihat kontrak event).
