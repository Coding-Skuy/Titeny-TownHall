# Titeny-TownHall — AI Insight Platform

> **Peran:** papan insight prediksi harga dan panen untuk petani, vendor, dan CEO.
> Setiap pola menceritakan sebuah kisah — Titeny mengubah data Lumbung/TitipO/Pasaree menjadi keputusan harian.
> Bahasa: Indonesia. Varian 1 (disahkan 2026-10-04). Nol TBD.

## Peran Pengguna

- **Petani:** kapan jual/tahan; sinyal panen kabupaten. Masuk via PIN zona, baca via web responsif.
- **Vendor (keliling/lapak):** saran stok besok per zona. Masuk via PIN zona, baca via web responsif.
- **CEO/Operasional:** peta surplus/defisit, margin, prioritas intervensi. Masuk via akun + OTP.
- **Analis (internal):** kurasi data dan model via aplikasi desktop Windows. Satu-satunya peran tulis-terhadap-tayang.

Mobile: **hanya baca**, cukup web responsif 360 px. Tidak ada app khusus di V1.

## Peta Folder

```
data/     10-sumber-data.md ......... 10 sumber dari Lumbung/TitipO/Pasaree + cuaca + kalender
          20-kontrak-event.md ........ amplop + 11 event berversi + aturan idempoten
model/    10-prediksi-harga.md ....... 12 komoditas × 8 pasar, horizon H+1–H+7, metode transparan
          20-prediksi-panen-demand.md  panen M+1–M+4 + demand H+7/H+30 + sinyal surplus/defisit
produk/   10-papan-insight.md ........ 3 papan (petani/vendor/CEO) + keadaan kosong/error
          21-kontrak-api-web-bun.md .. REST /api/v1 (baca + event masuk + umpan-balik)
          22-kontrak-desktop-windows.md  5 layar analis + kontrak aksi tulis + audit
platform/ 10-matriks-web-desktop.md ... siapa-bisa-apa di web vs. desktop
          20-navigasi3.md ............ graf Navigation3 desktop + analogi rute SvelteKit
          50-web-bun-svelte.md ....... struktur + runes + SSR + job + pengujian
          30-desktop-windows-analis.md  KMP/Compose, MSI, SQLite luring, diagnostik
metrik/   10-akurasi-dan-adopsi.md .... MAPE + adopsi 90 hari + alarm
```

## Stack Terkunci

- **Web dashboard:** Bun 1.4.x + Svelte 5 + SvelteKit 2 + TypeScript 5.9.x (+ zod 3.23.x).
- **Analis-desktop Windows:** Kotlin Multiplatform + Compose Multiplatform + Navigation3 1.0.0 (Ktor 3.1.x, SQLDelight 2.0.x). Windows 10 21H2+ / 11 64-bit, MSI.
- **Mobile:** tidak ada app khusus; web responsif baca-saja.

## Data Berasal Dari

- Lumbung (stok, panen, keuangan): `Lumbung-Backend` + `Lumbung-TownHall`.
- TitipO (transaksi, rute, vendor keliling): `TitipO-TownHall`.
- Pasaree (harga, lapak, produsen): `Pasaree-TownHall`.

## Tautan ke Lumbung (metrik & keuangan)

- Lumbung-TownHall: https://github.com/Coding-Skuy/Lumbung-TownHall — folder `metrik/` (definisi omzet/margin) dan `keuangan/` (ringkasan harian yang dipakai S10).
- Lumbung-Backend: https://github.com/Coding-Skuy/Lumbung-Backend — sumber event `stok.*`, `panen.dicatat`, `keuangan.ringkasan_harian`.
- Dampak Titeny dilaporkan bulanan ke Lumbung keuangan (lihat `metrik/10-akurasi-dan-adopsi.md` §4).

## Mulai Cepat

```bash
git clone https://github.com/Coding-Skuy/Titeny-TownHall
# Web:
cd apps/web && bun install && bun run dev --port 5173
# Desktop analis (Windows):
./gradlew :composeApp:packageMsi
```

Dokumen rinci mulai dari `data/10-sumber-data.md` → `model/` → `produk/` → `platform/` → `metrik/`.
