# 22 Kontrak Desktop Windows (Analis) — Titeny (Varian 1)

> Status: disahkan V1. Bahasa: Indonesia. Nol TBD. Desktop = analis internal, bukan petani/vendor.

## 1. Peran Desktop

Aplikasi desktop Windows untuk analis: validasi data, menahan prediksi meragukan,
mengekspor laporan, dan mengelola rekomendasi. Semua angka tetap berasal dari API web;
desktop tidak menghitung model sendiri di V1.

## 2. Kontrak Sinkronisasi

- Basis API sama: `https://insight.titeny.id/api/v1` + token analis (`peran: analis`).
- Polling: 15 menit untuk prediksi; 5 menit untuk antrean validasi; manual "Segarkan" selalu tersedia.
- Cache lokal: SQLite `%LOCALAPPDATA%/Titeny/analis.db`, maks 500 MB; data > 90 hari dipangkas otomatis.
- Mode luring: baca cache terakhir + banner "Luring — data 2026-10-03 23.00"; tulis (tahan/validasi) antre dan terkirim saat daring dengan kunci idempoten `aksi_id`.

## 3. Layar Terkunci (5 layar)

1. **Antrean Validasi** — daftar flag `perlu_verifikasi` (harga di luar rentang, stok selisih > 1%, panen outlier). Aksi: `setuju | koreksi_nilai | tolak` + alasan wajib 10–140 karakter.
2. **Tahan Prediksi** — sembunyikan pasangan komoditas-pasar dari papan petani dengan alasan; penahanan maks 7 hari lalu kedaluwarsa otomatis.
3. **Bandingkan Model** — tabel MAPE versi berjalan vs. kandidat backtest 90 hari; tombol "Naikkan versi" hanya aktif bila syarat §model terpenuhi.
4. **Kelola Rekomendasi** — ubah status + tetapkan penanggung jawab (nama + tanggal target konkret).
5. **Ekspor** — CSV/XLSX: harga H+7, demand, panen, jejak akurasi. Nama berkas `titeny_<jenis>_YYYYMMDD_HHmm.xlsx`, maks 50.000 baris per berkas.

## 4. Kontrak Aksi Tulis

### POST `/api/v1/analis/validasi`

```json
{ "aksi_id": "aks_20261004_001", "event_id": "evt_20261004_000123", "keputusan": "koreksi_nilai", "nilai_baru": 41000, "alasan": "Salah input lapak 042, nota terlampir." }
```

### POST `/api/v1/analis/tahan`

```json
{ "aksi_id": "aks_20261004_002", "komoditas_id": "cmd_cabai_merah", "pasar_id": "psr_jakarta_timur_01", "alasan": "Sampel hanya 2 lapak.", "berlaku_sampai": "2026-10-07" }
```

Respons memakai amplop error yang sama dengan API web (§produk/21).

## 5. Keamanan dan Audit

- Token analis kedaluwarsa 8 jam; refresh via OTP. Token disimpan di Windows Credential Manager, bukan berkas teks.
- Setiap aksi tulis mencatat `siapa, kapan, apa, alasan` di log audit 2 tahun, tidak dapat dihapus analis.
- Jejak ekspor dicatat (nama berkas + hash SHA-256) untuk kebutuhan audit Lumbung keuangan.

## 6. Batasan V1

- Tidak ada training model di desktop; training di server Bun terjadwal.
- Tidak ada akses data pribadi; desktop memakai data anonim yang sama dengan web.
- Instalasi: MSI 64-bit Windows 10 21H2+ / Windows 11; pembaruan via MSI penuh tiap rilis minor.
