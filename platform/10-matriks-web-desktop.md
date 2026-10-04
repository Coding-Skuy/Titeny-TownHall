# 10 Matriks Web vs. Desktop — Titeny (Varian 1)

> Status: disahkan V1. Prinsip: web = semua peran, baca-pertama; desktop = analis, kurasi-dan-ekspor.

## 1. Matriks Kapabilitas (● = tersedia)

| Kemampuan | Web responsif (Bun+SvelteKit) | Desktop Windows (KMP/Compose) | Keterangan |
|-----------|-------------------------------|-------------------------------|------------|
| Lihat harga H+7 + rentang | ● | ● | sumber sama |
| Lihat demand/panen + sinyal | ● | ● | sumber sama |
| Papan petani / vendor / CEO | ● | ○ | desktop fokus analis |
| Beri umpan-balik bermanfaat | ● | ○ | via web saja |
| Ubah status rekomendasi | ● (CEO) | ● (analis) | API sama |
| Validasi flag / koreksi nilai | ○ | ● | hanya analis desktop |
| Tahan prediksi meragukan | ○ | ● | hanya analis desktop |
| Bandingkan versi model | ○ | ● | hanya analis desktop |
| Ekspor CSV/XLSX massal | ○ (maks 500 baris) | ● (maks 50.000 baris) | batas konkret |
| Kerja luring | ○ (cache 6 jam) | ● (SQLite 90 hari) | |
| PIN zona (petani/vendor) | ● | ○ | desktop pakai token analis |
| Cetak laporan PDF rapi | △ (cetak browser) | ● (template kop) | △ = terbatas |

## 2. Aturan Keputusan

1. Fitur yang mengubah angka tayang (validasi, tahan, naik versi) hanya di desktop analis.
2. Fitur yang dibaca petani/vendor hanya di web; tidak diduplikasi ke desktop sebagai peran terpisah.
3. Ekspor web dibatasi 500 baris agar tidak disalahgunakan sebagai jalur data massal; kebutuhan analis lewat desktop beraudit.
4. Bila API web mati: web menampilkan cache + stempel waktu; desktop beralih ke SQLite luring. Tidak ada jalur tulis langsung ke database dari klien mana pun.

## 3. Konsistensi

- Satu kontrak API (`produk/21`, `produk/22`); tidak ada endpoint khusus desktop kecuali `/analis/*`.
- Satu bahasa visual: token warna sinyal hijau/kuning/merah identik di kedua platform.
- Nomor versi tayang: web menampilkan `model_version` per kartu; desktop menampilkan per baris antrean.
