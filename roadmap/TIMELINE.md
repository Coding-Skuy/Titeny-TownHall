> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# TIMELINE Titeny — Garis Waktu Hidup Lintas Versi

Dokumen living: diperbarui tiap ada versi baru. Salinan beku v1.0.0 ada di `versions/v1.0.0/SNAPSHOT-ROADMAP.md` dan tidak diubah lagi.

## Garis Waktu

- 04 Okt 2026 — Varian 1 disahkan. 10 sumber S1–S10 dikunci, 11 event berversi idempoten dikunci, model harga 12 komoditas × 8 pasar H+1–H+7 dan model panen-demand dikunci. Sumber isi lama: `data/10-sumber-data.md`, `data/20-kontrak-event.md`, `model/10-prediksi-harga.md`, `model/20-prediksi-panen-demand.md`.
- 04 Okt 2026 — v1.0.0 disetujui. Struktur versi BRD, PRD, FRD, FSD dibekukan mengikuti template emas. Aturan keras: read-only terhadap DB sumber, satu-satunya tulis ke DB `titeny`, JWT `aud=titeny`, web Bun + Svelte 5 + SvelteKit 2, desktop analis KMP Windows MSI.
- Hari 1 sampai 30 tayang — Operasi awal. Job 04.00/04.30 berjalan, papan 3 peran dibaca via PIN zona dan akun CEO, analis memutus flag `perlu_verifikasi` via desktop. Target arus: event masuk di bawah 5 menit, prediksi segar tiap pagi, umpan-balik terkumpul.
- Hari 31 sampai 60 tayang — Penguatan. Kalibrasi rentang pertama bila cakupan di luar 70–90 persen, tinjauan bias bila overpredict 2 minggu berturut, laporan dampak bulanan pertama ke Lumbung.
- Hari 61 sampai 90 tayang — Kesiapan lepas awal. Genap 150 petani dan 80 vendor aktif mingguan, 50 persen rekomendasi selesai dalam 14 hari, MAPE memenuhi target. Syarat lulus: M-003 dan M-005 done 4 minggu berturut.
- Setelah 90 hari — Skala dan versi berikutnya. Penambahan komoditas dan kabupaten, kandidat model naik versi hanya bila backtest 90 hari memenuhi syarat. Rencana rinci menunjuk `ROADMAP.md` untuk v1.1.0 dan v2.0.0.

## Keterkaitan Versi

- v1.0.0 menjadi acuan awal. Perubahan jadwal pada versi baru dicatat di sini dengan tanggal dan nomor versi, tanpa mengubah snapshot beku.

## Batasan

Batasan dokumen ini: hanya mencatat tonggak waktu dan fase. Detail kebutuhan tetap di `versions/v1.0.0/BRD/`, detail kriteria lulus di `versions/v1.0.0/PRD/30-kriteria.md`, dan detail janji beku di `SNAPSHOT-ROADMAP.md`. Dokumen ini tidak mengatur tarif, grade, atau kontrak API.
