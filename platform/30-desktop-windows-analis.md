# 30 Desktop Windows Analis — Titeny (Varian 1)

> Status: disahkan V1. Bahasa: Indonesia. Nol TBD. Windows 64-bit.

## 1. Stack Terkunci

- Kotlin 2.1.x, Compose Multiplatform 1.7.x, Navigation3 1.0.0, Ktor-client 3.1.x (CIO), SQLDelight 2.0.x (SQLite lokal), Koin 4.x (DI).
- Target: `desktop` (JVM 17) + kemas MSI via `WiX Toolset 3.14`. Tidak ada target macOS/Linux di V1.
- Syarat mesin analis: Windows 10 21H2+ / 11 64-bit, RAM 8 GB, disk 1 GB.

## 2. Struktur Modul

```
apps/desktop/
  composeApp/
    src/commonMain/kotlin/id/titeny/analis/
      App.kt                 # NavDisplay + tema
      navigasi/Tujuan.kt     # 7 tujuan §platform/20
      layar/                 # Antrean, Detail, Tahan, Banding, Rekomendasi, Ekspor, Masuk
      data/                  # ApiAnalis.kt, DbLokal.kt
      domain/                # AturanTahan.kt, AmbangFlag.kt
    src/desktopMain/kotlin/  # kredensial Windows, path %LOCALAPPDATA%
  gradle/libs.versions.toml  # katalog versi terkunci
```

## 3. Jaringan dan Penyimpanan

- `ApiAnalis.kt`: base `https://insight.titeny.id/api/v1`, timeout 15 dtk, retry 2× untuk GET; POST memakai `Idempotency-Key: <aksi_id>`.
- Token analis di Windows Credential Manager (`Titeny/analis`), tidak di `localStorage` atau berkas.
- SQLDelight: tabel `cache_prediksi`, `antrean_flag`, `aksi_tertunda`; VACUUM tiap Minggu; batas 500 MB lalu pangkas FIFO 90 hari.

## 4. Aturan Tampilan

- Jendela min 1120×720; tabel padat dengan zebra; status flag berwarna + teks (bukan warna saja).
- Semua keputusan butuh alasan 10–140 karakter; tombol kirim nonaktif sampai valid.
- Shortcut: `Ctrl+R` segarkan, `Ctrl+E` ekspor, `Esc` kembali.

## 5. Build dan Rilis

```powershell
./gradlew :composeApp:createDistributable
./gradlew :composeApp:packageMsi
```

- Artefak: `Titeny-Analis-1.0.0.msi` + `SHA256SUMS.txt`.
- Versi: `1.0.0` = baseline dokumen ini; skema SemVer; changelog Indonesia di `CHANGELOG.md`.
- Penandatanganan kode: Sertifikat EV perusahaan (di luar repo); build tanpa tanda hanya untuk uji internal dengan banner kuning.

## 6. Diagnostik

- Log di `%LOCALAPPDATA%/Titeny/logs/analis-YYYYMMDD.log`, level INFO default; tombol "Kirim log" membuat ZIP anonim (tanpa token) untuk tim platform.
- Crash: dialog Indonesia + ID laporan `TNY-<tanggal>-<4_digit>`; tidak ada unggah otomatis di V1.
