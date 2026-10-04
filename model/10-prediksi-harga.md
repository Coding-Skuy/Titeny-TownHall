# 10 Prediksi Harga — Titeny (Varian 1)

> Status: disahkan V1. Bahasa: Indonesia. Nol TBD. Horizon dan ambang konkret.

## 1. Tujuan

Menjawab pertanyaan harian: **"berapa harga wajar komoditas X di pasar Y untuk 7 hari ke depan?"**
Konsumen: papan insight petani (jual/tahan), vendor (stok), CEO (margin).

## 2. Cakupan V1

- Komoditas: 12 prioritas — beras medium, telur ayam, cabai merah, cabai rawit,
  bawang merah, bawang putih, minyak goreng curah, gula pasir, daging ayam, daging sapi,
  tomat, kangkung.
- Pasar: 8 pasar rujukan Pasaree (4 Jabodetabek, 2 Jawa Barat, 1 Jawa Tengah, 1 Jawa Timur).
- Horizon: H+1 sampai H+7 (harian). Di atas H+7 tidak ditampilkan (dipotong).
- Frekuensi hitung: 1×/hari pukul 04.00 WIB + hitung ulang 15.00 WIB bila ada koreksi harga siang.

## 3. Fitur Input (terkunci)

| Fitur | Sumber | Jendela |
|-------|--------|---------|
| harga median 7/14/30 hari | S1 | gulir |
| laju perubahan 7 hari (%) | S1 | H-7→H-1 |
| jumlah lapak aktif | S4 | H-1 |
| stok gabungan gudang (kg) | S2 | H-1 |
| volume transaksi TitipO 7 hari | S3 | gulir |
| biaya logistik per kg | S6 | berlaku |
| musim + jenis hari (one-hot) | S9 | H+i |
| curah hujan 7 hari terakhir | S8 | gulir |

Tidak ada fitur teks bebas atau sentimen media sosial di V1.

## 4. Metode V1 (tanpa deep learning)

Per pasangan `(komoditas, pasar)`:

1. Baseline musiman-naif: median 14 hari terakhir pada `jenis_hari` yang sama.
2. Koreksi tren: regresi linier 21 hari terakhir; kemiringan dibatasi ±8%/hari.
3. Koreksi stok: jika stok gabungan < 60% rerata 30 hari → +4%; jika > 140% → −4%.
4. Koreksi logistik: selisih `biaya_per_kg` vs. bulan lalu diteruskan 80% ke harga.
5. Batas akhir: hasil dipotong ke rentang `[0,70×median_30h, 1,60×median_30h]` lalu dibulatkan ke kelipatan 500 IDR.

Alasan: transparan, dapat dijelaskan ke petani, murah di Bun, dan cukup akurat untuk horizon 7 hari
(target MAPE ≤ 12% — lihat `metrik/10-akurasi-dan-adopsi.md`).

## 5. Output (kontrak)

```json
{
  "komoditas_id": "cmd_cabai_merah",
  "pasar_id": "psr_jakarta_timur_01",
  "tanggal_prediksi": "2026-10-05",
  "horizon": "H+1",
  "harga_prediksi": 42500,
  "rentang_bawah": 39000,
  "rentang_atas": 46000,
  "kepercayaan": "sedang",
  "alasan_singkat": "stok_gudang_72%_dari_rerata + musim_peralihan",
  "model_version": "harga_v1.0.0"
}
```

- `kepercayaan`: `tinggi` (sampel ≥ 5 lapak & MAPE_14h < 8%), `sedang`, `rendah` (sampel < 3 atau MAPE_14h > 15%).
- `rentang_*` = persentil P10/P90 residual 30 hari terakhir.
- Jika `kepercayaan = rendah`, papan insight wajib menampilkan lencana kuning "Akurasi terbatas".

## 6. Evaluasi

- Metrik utama: MAPE H+1 dan H+7 per komoditas per pasar, dihitung tiap Minggu 23.00.
- Backtest wajib: 90 hari terakhir sebelum rilis model baru; model naik versi hanya bila MAPE H+7 turun ≥ 0,5 poin dan tidak ada komoditas yang memburuk > 2 poin.
- Override analis: analis desktop dapat menandai prediksi `ditahan` dengan alasan; override tidak mengubah angka, hanya menyembunyikan dari papan petani.

## 7. Fallback

- Jika S1 kosong untuk 1 hari: gunakan median 7 hari + lencana "data kemarin".
- Jika kosong ≥ 3 hari berturut: prediksi dihentikan untuk pasangan itu, tampilkan "Data harga belum cukup".
- Jika job 04.00 gagal: retry 04.30 dan 05.00; setelah itu gunakan prediksi H-1 dengan label `basi`.

## 8. Versi Model

- `harga_v1.0.0` = baseline dokumen ini. Perubahan ambang/kolom fitur menaikkan minor; ganti algoritma menaikkan mayor.
- Artefak tersimpan: `model/artefak/harga_v1.0.0/{koefisien.json, residual_p90.json, backtest.csv}`.
