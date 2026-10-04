# 10 Akurasi dan Adopsi — Titeny (Varian 1)

> Status: disahkan V1. Bahasa: Indonesia. Nol TBD. Semua target berangka.

## 1. Dua Keluarga Metrik

- **Akurasi:** apakah prediksi benar? (MAPE, bias, cakupan rentang).
- **Adopsi & dampak:** apakah insight dipakai dan mengubah hasil? (pengguna, keputusan, margin).

## 2. Target Akurasi V1 (dihitung tiap Minggu 23.00 WIB)

| Model | Metrik | Target V1 | Batas rilis |
|-------|--------|-----------|-------------|
| harga H+1 | MAPE per komoditas-pasar | ≤ 8% median 12 komoditas | tidak ada komoditas > 15% |
| harga H+7 | MAPE | ≤ 12% | memburuk > 2 poin pada 1 komoditas = tolak rilis |
| demand H+7 | MAPE per zona | ≤ 15% | — |
| demand H+30 | MAPE | ≤ 22% | hanya pantau |
| panen per musim | MAPE total musim | ≤ 20% | — |
| rentang P10–P90 | cakupan aktual | 70–90% titik di dalam rentang | < 60% = kalibrasi ulang |

Bias (rata-rata over/under) dilaporkan terpisah; bias > +5% (overpredict) 2 minggu berturut memicu tinjauan analis.

## 3. Target Adopsi V1 (90 hari pertama)

| Metrik | Definisi | Target hari-90 |
|--------|----------|----------------|
| Petani aktif mingguan | PIN unik buka papan petani | ≥ 150 |
| Vendor aktif mingguan | PIN unik buka papan vendor | ≥ 80 |
| CEO/operator aktif | login + 1 aksi status | 100% (8 akun) |
| Umpan-balik | % tayangan dengan nilai bermanfaat | ≥ 60% dari yang menilai; penilai ≥ 15% tayangan |
| Tindak lanjut | % rekomendasi `selesai` ≤ 14 hari | ≥ 50% |
| Waktu-ke-insight | klik → kartu tampil (p95) | < 3 dtk (4G) |

## 4. Dampak (dilaporkan bulanan ke Lumbung keuangan)

- Selisih margin kotor vs. baseline 3 bulan sebelum Titeny, per gudang.
- Kisah sukses konkret: 2 contoh per bulan (misal: "tahan cabai 3 hari → +Rp1,2 jt untuk 40 kg").
- Dampak tidak dipakai sebagai KPI kinerja petani/vendor; hanya untuk keputusan intervensi CEO.

## 5. Instrumentasi (konkret)

- Event analitik: `papan.dibuka`, `kartu.terlihat`, `umpan_balik.diberi`, `rekomendasi.status_berubah`, `ekspor.dibuat` — masing-masing dengan `peran`, `komoditas_id`/`zona`, `model_version`, tanpa data pribadi.
- Dasbor metrik: `/insight/ceo` tab "Akurasi" (grafik MAPE 4 minggu) + ekspor mingguan ke Lumbung (`keuangan.ringkasan_harian` sebagai pembanding).
- Alarm: MAPE H+7 > 15% 2 minggu berturut → pager analis; adopsi < 50% target 2 minggu berturut → tinjauan UX.

## 6. Anti-Gaming

- Umpan-balik dari PIN yang sama > 20×/hari diabaikan.
- Rekomendasi `selesai` tanpa catatan/diverifikasi gudang ditandai `klaim_sepihak`.
