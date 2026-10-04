# 20 Prediksi Panen & Demand — Titeny (Varian 1)

> Status: disahkan V1. Bahasa: Indonesia. Nol TBD.

## 1. Tujuan Ganda

1. **Panen:** berapa kg komoditas X akan dipanen di kabupaten K pada minggu M+1 s.d. M+4?
2. **Demand:** berapa kg komoditas X akan diminta (TitipO + lapak Pasaree) pada 7 dan 30 hari ke depan?

Selisihnya (surplus/defisit) menjadi rekomendasi: serap, distribusikan, atau tahan.

## 2. Cakupan V1

- Panen: 6 komoditas (padi, jagung, cabai merah, cabai rawit, bawang merah, tomat),
  5 kabupaten (Karawang, Subang, Garut, Brebes, Malang), horizon mingguan M+1–M+4.
- Demand: 12 komoditas harga (sama dengan model harga), horizon H+7 dan H+30, zona: 8 zona rute TitipO + 8 pasar Pasaree.
- Frekuensi hitung: mingguan Senin 03.00 WIB (panen) dan harian 04.30 WIB (demand).

## 3. Fitur

### Panen

| Fitur | Sumber |
|-------|--------|
| luas tanam berjalan (ha) | S5 agregat + kalender tanam S9 |
| hasil per ha 3 musim terakhir (kg/ha) | S5 |
| curah hujan 30 hari + prakiraan 14 hari | S8 |
| peringatan banjir/kekeringan | S8 |
| sebaran mutu A/B/C | S5 |

### Demand

| Fitur | Sumber |
|-------|--------|
| volume transaksi 7/28 hari | S3 |
| lapak aktif + kapasitas vendor | S4, S7 |
| harga prediksi H+7 (dari model harga) | model harga |
| jenis hari libur/puasa/lebaran | S9 |
| ringkasan omzet 30 hari (skala) | S10 |

## 4. Metode V1

**Panen (per komoditas per kabupaten):**
`panen_minggu = luas_panen_ekspektasi_ha × yield_baseline_kg_ha × faktor_cuaca × faktor_musim`.
- `luas_panen_ekspektasi` dari catatan tanam mundur 90–120 hari (padi) / 60–75 hari (sayur).
- `yield_baseline` = median 3 musim terakhir, maks perubahan ±20% per tahun.
- `faktor_cuaca`: 1,00 normal; 0,85 bila hujan > 300 mm/30 hari atau peringatan banjir; 0,90 bila kekeringan.
- Output dibulatkan ke 50 kg.

**Demand (per komoditas per zona):**
regresi linier musiman: `baseline_28h × faktor_hari_besar × elastisitas_harga`.
- `faktor_hari_besar`: puasa 1,18; lebaran H-7–H+7 1,35 (cabai/daging), 1,10 (beras); natal/tahun baru 1,12.
- `elastisitas_harga`: tiap +10% harga prediksi → demand −4% (pangan pokok) atau −7% (sayur segar).
- Batas: perubahan maks ±30% vs. rerata 28 hari.

## 5. Output

```json
{
  "jenis": "panen",
  "komoditas_id": "cmd_padi",
  "kabupaten_id": "kab_karawang",
  "minggu": "2026-W41",
  "panen_prediksi_kg": 12500,
  "rentang_bawah_kg": 10200,
  "rentang_atas_kg": 14800,
  "kepercayaan": "sedang",
  "model_version": "panen_v1.0.0"
}
```

```json
{
  "jenis": "demand",
  "komoditas_id": "cmd_cabai_merah",
  "zona": "bekasi_utara",
  "tanggal": "2026-10-11",
  "horizon": "H+7_total_kg",
  "demand_prediksi_kg": 840,
  "rentang_bawah_kg": 720,
  "rentang_atas_kg": 960,
  "sinyal": "defisit",
  "model_version": "demand_v1.0.0"
}
```

`sinyal`: `surplus` (panen ≥ 120% demand), `seimbang`, `defisit` (panen ≤ 80% demand) pada level kabupaten↔zona terdekat.

## 6. Rekomendasi Otomatis (terkunci)

| Sinyal | Rekomendasi teks (ditampilkan apa adanya) |
|--------|--------------------------------------------|
| surplus | "Serap 500–800 kg ke gudang Bekasi sebelum Jumat; tawarkan ke 3 vendor zona utara." |
| seimbang | "Tahan pola distribusi; pantau ulang Senin." |
| defisit | "Amankan pasokan dari Brebes/Malang; batasi promo; naikkan buffer stok 15%." |

Angka rekomendasi dihitung dari selisih absolut, dibulatkan ke 100 kg, maks 3 aksi per zona.

## 7. Evaluasi dan Fallback

- Panen: evaluasi per musim (MAPE total musim ≤ 20%); demand: MAPE H+7 ≤ 15%, H+30 ≤ 22%.
- Jika catatan panen < 10 baris per kabupaten/musim → kepercayaan `rendah` + rekomendasi generik.
- Jika S3 kosong 1 hari: demand memakai rerata 7 hari + label `estimasi`.
