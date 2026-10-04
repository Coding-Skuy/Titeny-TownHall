# 20 Kontrak Event — Titeny (Varian 1)

> Status: disahkan V1. Bahasa: Indonesia. Nol TBD. Semua event berversi dan idempoten.

## 1. Amplop Standar (wajib untuk semua event)

```json
{
  "event_id": "evt_20261004_000123",
  "event_type": "harga.diperbarui",
  "schema_version": 1,
  "source": "pasaree",
  "occurred_at": "2026-10-04T07:00:00+07:00",
  "received_at": "2026-10-04T07:00:41+07:00",
  "batch_id": "batch_pasaree_20261004_pagi",
  "payload": { "...": "..." }
}
```

Aturan amplop:

- `event_id` unik global, format `evt_YYYYMMDD_NNNNNN`. Duplikat `event_id` wajib diabaikan penerima (idempoten).
- `event_type` = `<domain>.<aksi>` huruf kecil dengan titik. Daftar terkunci di §2.
- `source` salah satu dari: `lumbung`, `titipo`, `pasaree`, `cuaca`, `internal`.
- `occurred_at` waktu kejadian di sumber; `received_at` waktu diterima Titeny.
- `schema_version` dimulai dari `1`; perubahan tak-kompatibel menaikkan versi mayor dan didokumentasikan di §5.

## 2. Daftar Event Terkunci V1 (11 event)

| # | `event_type` | Sumber | Frekuensi | Kunci idempoten |
|---|--------------|--------|-----------|-----------------|
| 1 | `harga.diperbarui` | pasaree | harian + koreksi | `pasar_id + komoditas_id + tanggal` |
| 2 | `stok.berubah` | lumbung | real-time | `event_id` |
| 3 | `stok.snapshot_harian` | lumbung | harian 23.00 | `gudang_id + tanggal` |
| 4 | `transaksi.dibuat` | titipo | real-time | `transaksi_id` |
| 5 | `lapak.status_berubah` | pasaree | real-time | `lapak_id + occurred_at` |
| 6 | `panen.dicatat` | lumbung | per kejadian | `panen_id` |
| 7 | `logistik.biaya_diperbarui` | titipo/pasaree | episodik | `rute_id + berlaku_mulai` |
| 8 | `vendor.status_berubah` | titipo/pasaree | real-time | `vendor_id + occurred_at` |
| 9 | `cuaca.diperbarui` | cuaca | harian 05.00 | `kabupaten_id + tanggal` |
| 10 | `kalender.dikunci` | internal | tahunan | `tahun` |
| 11 | `keuangan.ringkasan_harian` | lumbung | harian 23.45 | `tanggal` |

Event di luar daftar ini ditolak di pintu masuk dengan error `event_type_tidak_dikenal`.

## 3. Skema Payload (ringkas, normatif)

### 3.1 `harga.diperbarui` (v1)

```json
{
  "pasar_id": "psr_jakarta_timur_01",
  "komoditas_id": "cmd_cabai_merah",
  "tanggal": "2026-10-04",
  "harga_per_kg": 42000,
  "satuan": "kg",
  "jumlah_sampel_lapak": 6,
  "flag": "ok"
}
```

- `harga_per_kg`: integer IDR > 0. `flag`: `ok | perlu_verifikasi`.
- Validasi: wajib ada `jumlah_sampel_lapak >= 3`; jika kurang → `flag = perlu_verifikasi`.

### 3.2 `stok.berubah` (v1)

```json
{
  "gudang_id": "gdg_bekasi_01",
  "komoditas_id": "cmd_beras_medium",
  "perubahan_kg": -150.50,
  "stok_akhir_kg": 4850.00,
  "alasan": "keluar_penjualan"
}
```

`alasan`: `masuk_panen | masuk_beli | keluar_penjualan | susut | koreksi_opname`.

### 3.3 `stok.snapshot_harian` (v1)

```json
{
  "gudang_id": "gdg_bekasi_01",
  "tanggal": "2026-10-03",
  "baris": [{ "komoditas_id": "cmd_beras_medium", "stok_kg": 4850.00 }],
  "jumlah_baris": 42
}
```

### 3.4 `transaksi.dibuat` (v1)

```json
{
  "transaksi_id": "trx_20261004_881201",
  "komoditas_id": "cmd_telur_ayam",
  "qty_kg": 2.00,
  "harga_satuan": 28500,
  "vendor_id": "vnd_keliling_014",
  "zona_rute": "bekasi_utara",
  "waktu": "2026-10-04T08:12:00+07:00"
}
```

Tanpa nama pembeli. `qty_kg` maks 50 kg per transaksi (di atas itu → flag grosir).

### 3.5 `lapak.status_berubah` (v1)

```json
{ "lapak_id": "lpk_042", "status": "aktif", "komoditas_utama": "cmd_cabai_merah" }
```

`status`: `aktif | tutup | libur`.

### 3.6 `panen.dicatat` (v1)

```json
{
  "panen_id": "pn_20261001_0091",
  "komoditas_id": "cmd_padi",
  "lahan_ha": 1.25,
  "hasil_kg": 6875.00,
  "tanggal_panen": "2026-10-01",
  "mutu": "A",
  "kabupaten_id": "kab_karawang",
  "keterangan": ""
}
```

### 3.7 `logistik.biaya_diperbarui` (v1)

```json
{
  "rute_id": "rte_bekasi_jaktim",
  "jarak_km": 18.5,
  "biaya_per_kg": 900,
  "moda": "mobil_pickup",
  "berlaku_mulai": "2026-10-05",
  "sebab": "penyesuaian_bbm"
}
```

### 3.8 `vendor.status_berubah` (v1)

```json
{
  "vendor_id": "vnd_keliling_014",
  "jenis": "keliling",
  "status_aktif": true,
  "zona_operasi": "bekasi_utara",
  "kapasitas_harian_kg": 120.0
}
```

### 3.9 `cuaca.diperbarui` (v1)

```json
{
  "kabupaten_id": "kab_karawang",
  "tanggal": "2026-10-04",
  "curah_hujan_mm": 12.5,
  "suhu_avg_c": 28.4,
  "peringatan": "tidak_ada"
}
```

### 3.10 `kalender.dikunci` (v1)

```json
{
  "tahun": 2027,
  "baris": [{ "tanggal": "2027-03-20", "jenis_hari": "puasa", "musim": "peralihan" }],
  "jumlah_baris": 365,
  "dikunci_pada": "2026-12-01T00:00:00+07:00"
}
```

### 3.11 `keuangan.ringkasan_harian` (v1)

```json
{
  "tanggal": "2026-10-03",
  "omzet_idr": 48250000,
  "margin_kotor_idr": 7210000,
  "biaya_operasional_idr": 4350000,
  "piutang_idr": 1200000,
  "final": true
}
```

## 4. Aturan Pengiriman dan Kegagalan

1. Pengiriman via HTTPS POST ke `/api/v1/event/masuk` dengan header `X-Idempotency-Key: <event_id>`.
2. Timeout sumber 10 detik; retry maks 3× dengan jeda 5, 30, 300 detik. Setelah itu masuk antrean mati (DLQ) dan pager berbunyi.
3. Urutan tidak dijamin; penerima wajib menangani keterlambatan hingga 24 jam (rekalkulasi agregat H-1).
4. Duplikat: respons `200 {"status":"duplikat_diabaikan"}` — bukan error.
5. Validasi gagal: respons `422 {"status":"ditolak","alasan":"<kode>","field":"<nama>"}`; sumber wajib memperbaiki dan kirim ulang dengan `event_id` baru.

## 5. Versioning

- Tambah field opsional = versi minor, tetap `schema_version: 1`, dicatat di changelog.
- Ubah tipe/hapus/ubah makna field = versi mayor baru (`schema_version: 2`), dual-read 30 hari, lalu migrasi.
- Changelog V1: tidak ada perubahan mayor; baseline 2026-10-04.

## 6. Retensi dan Arsip

- Mentah: 365 hari di penyimpanan panas. Agregat harian: 3 tahun.
- Arsip dingin per `batch_id` dalam berkas JSONL gzip, diberi checksum SHA-256 dan didaftar di `data/arsip/manifest.csv`.
