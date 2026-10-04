> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# BRD 30 — Akurasi, Adopsi, dan Dampak

## Konteks

Sukses Titeny diukur dua keluarga: apakah prediksi benar dan apakah insight dipakai. Sumber isi lama: `metrik/10-akurasi-dan-adopsi.md`.

## Kebutuhan Bisnis

- BR-301 Target akurasi dihitung tiap Minggu 23.00: harga H+1 MAPE ≤ 8 persen median 12 komoditas dengan tidak ada komoditas di atas 15 persen; harga H+7 MAPE ≤ 12 persen; demand H+7 MAPE ≤ 15 persen; demand H+30 MAPE ≤ 22 persen pantau; panen per musim MAPE ≤ 20 persen; cakupan P10–P90 70–90 persen dengan kalibrasi ulang bila di bawah 60 persen. Bias overpredict di atas 5 persen 2 minggu berturut memicu tinjauan analis.
- BR-302 Target adopsi 90 hari: petani aktif mingguan ≥ 150 PIN unik; vendor aktif mingguan ≥ 80; CEO/operator 100 persen dari 8 akun; umpan-balik bermanfaat ≥ 60 persen dari penilai dengan penilai ≥ 15 persen tayangan; 50 persen rekomendasi selesai dalam 14 hari; waktu-ke-insight p95 di bawah 3 detik pada 4G.
- BR-303 Dampak dilaporkan bulanan ke Lumbung keuangan: selisih margin kotor vs. baseline 3 bulan sebelum Titeny per gudang plus 2 kisah sukses konkret per bulan. Dampak tidak dipakai sebagai KPI kinerja petani/vendor.
- BR-304 Instrumentasi dikunci: event `papan.dibuka`, `kartu.terlihat`, `umpan_balik.diberi`, `rekomendasi.status_berubah`, `ekspor.dibuat` dengan peran, komoditas/zona, dan `model_version` tanpa data pribadi. Alarm bila MAPE H+7 di atas 15 persen 2 minggu berturut atau adopsi di bawah 50 persen target 2 minggu berturut.
- BR-305 Anti-gaming: umpan-balik dari PIN sama di atas 20×/hari diabaikan; rekomendasi selesai tanpa catatan atau verifikasi gudang ditandai `klaim_sepihak`.

## Metrik

- Dasbor akurasi di papan CEO tab Akurasi plus ekspor mingguan pembanding `keuangan.ringkasan_harian`.

## Batasan

Batasan segmen ini: hanya target dan tata ukur. Implementasi instrumentasi ada di FSD. Di luar batas: penilaian kinerja individu petani/vendor berbasis dampak.
