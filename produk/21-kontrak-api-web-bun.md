# 21 Kontrak API Web (Bun) — Titeny (Varian 1)

> Status: disahkan V1. Stack: Bun 1.4.x. Nol TBD.

## 1. Basis

- Basis URL: `https://insight.titeny.id/api/v1`.
- Format: JSON UTF-8. Waktu ISO-8601 WIB. Uang integer IDR.
- Auth: header `Authorization: Bearer <token>` untuk CEO/analis; header `X-Zona-PIN: <6_digit>` untuk petani/vendor.
- Versi via path (`/v1`); versi minor kompatibel tidak mengubah path.
- Batas: 60 req/menit per token (429 + `Retry-After`), bodi maks 256 KB.

## 2. Amplop Error Standar

```json
{ "error": { "kode": "tidak_ditemukan", "pesan": "Komoditas tidak dikenal.", "field": "komoditas_id" } }
```

Kode terkunci: `tidak_ditemukan`, `validasi_gagal`, `tidak_berhak`, `kuota_habis`, `data_belum_cukup`, `kesalahan_server`.

## 3. Endpoint Baca (publik per peran)

### GET `/harga?komoditas_id=&pasar_id=&dari=&sampai=`

Respons 200:

```json
{
  "komoditas_id": "cmd_cabai_merah",
  "pasar_id": "psr_jakarta_timur_01",
  "deret": [{ "tanggal": "2026-10-05", "horizon": "H+1", "harga_prediksi": 42500, "rentang_bawah": 39000, "rentang_atas": 46000, "kepercayaan": "sedang" }],
  "model_version": "harga_v1.0.0",
  "dihitung_pada": "2026-10-04T04:00:12+07:00"
}
```

### GET `/demand?komoditas_id=&zona=&horizon=H+7`

```json
{
  "komoditas_id": "cmd_cabai_merah",
  "zona": "bekasi_utara",
  "horizon": "H+7_total_kg",
  "demand_prediksi_kg": 840.0,
  "sinyal": "defisit",
  "rekomendasi": ["Amankan pasokan dari Brebes; batasi promo; naikkan buffer stok 15%."],
  "model_version": "demand_v1.0.0"
}
```

### GET `/panen?komoditas_id=&kabupaten_id=&minggu=2026-W41`

Sama bentuknya dengan output model panen (§5 `model/20-prediksi-panen-demand.md`).

### GET `/rekomendasi?zona=&limit=5`

Daftar aksi prioritas siap tampil di papan CEO/vendor.

### GET `/kesehatan`

```json
{ "status": "ok", "job_terakhir": "2026-10-04T04:00:12+07:00", "versi_api": "v1.0.0", "bun": "1.4.x" }
```

## 4. Endpoint Tulis (terbatas)

### POST `/event/masuk`

Pintu masuk event §data/20 (sumber Lumbung/TitipO/Pasaree memakai token layanan).
Respons: `200 {"status":"diterima"}`, `200 {"status":"duplikat_diabaikan"}`, atau `422` validasi.

### POST `/umpan-balik`

Petani/vendor dapat memberi nilai `bermanfaat|kurang_tepat` + komentar maks 280 karakter.
Disimpan untuk metrik adopsi; tidak mengubah model otomatis.

### POST `/rekomendasi/:id/status`

CEO/analis mengubah `baru → dikerjakan → selesai` dengan `catatan` maks 140 karakter.

## 5. Cache dan Kinerja

- Respons prediksi di-cache 6 jam (`Cache-Control: public, max-age=21600`); `/kesehatan` 30 detik.
- Paginasi: `limit` default 50, maks 200; `cursor` opaque.
- SLO: p95 < 400 ms untuk baca cache-hit; p99 < 1.200 ms.

## 6. Contoh cURL

```bash
curl -H "X-Zona-PIN: 482013" \
  "https://insight.titeny.id/api/v1/harga?komoditas_id=cmd_cabai_merah&pasar_id=psr_jakarta_timur_01&dari=2026-10-05&sampai=2026-10-11"
```
