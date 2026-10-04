> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# FSD 20 — Model Data

## Entitas Inti

- event_masuk: `event_id` format `evt_YYYYMMDD_NNNNNN` unik global, `event_type` 11 terkunci, `schema_version` mulai 1, `source` enum lumbung/titipo/pasaree/cuaca/internal, `occurred_at`, `received_at`, `batch_id`, `payload` JSON. Contoh: `harga.diperbarui` pasar `psr_jakarta_timur_01` komoditas `cmd_cabai_merah` tanggal 2026-10-04 harga 42000 flag ok dengan 6 sampel.
- prediksi_harga: `komoditas_id` 12 terkunci, `pasar_id` 8 rujukan, `tanggal_prediksi`, `horizon` enum H+1–H+7, `harga_prediksi` IDR integer kelipatan 500, `rentang_bawah/atas` P10/P90, `kepercayaan` enum tinggi/sedang/rendah, `alasan_singkat`, `model_version` `harga_v1.0.0`, `dihitung_pada`. Contoh H+1 42500 dengan rentang 39000–46000.
- prediksi_panen: `jenis` panen, `komoditas_id` 6 terkunci, `kabupaten_id` 5 terkunci, `minggu` format `2026-W41`, `panen_prediksi_kg` kelipatan 50, rentang kg, kepercayaan, `model_version` `panen_v1.0.0`. Contoh padi Karawang W41 12500 kg.
- prediksi_demand: `jenis` demand, `komoditas_id` 12 terkunci, `zona` 8 zona plus 8 pasar, `tanggal`, `horizon` H+7_total_kg/H+30, `demand_prediksi_kg`, rentang kg, `sinyal` surplus/seimbang/defisit, `model_version` `demand_v1.0.0`. Contoh cabai bekasi_utara H+7 840 kg defisit.
- rekomendasi: id, zona, teks maks 3 aksi, `status` baru/dikerjakan/selesai, penanggung jawab plus target tanggal, `catatan` maks 140 karakter.
- Entitas pendukung: umpan_balik (nilai plus komentar maks 280), validasi_analis (`aksi_id`, `event_id`, keputusan, nilai_baru, alasan 10–140), tahan_prediksi (komoditas, pasar, alasan, berlaku_sampai maks 7 hari), audit_aksi (siapa, kapan, apa, alasan, hash ekspor).

## Aturan Angka

- Uang selalu IDR integer; berat kg 2 desimal di API dan gram integer dihitung internal bila perlu; harga dibulatkan ke 500 IDR; panen ke 50 kg; rekomendasi ke 100 kg; PIN 6 digit; bodi event maks 256 KB; ekspor web 500 baris dan desktop 50.000 baris.
- Retensi: mentah 365 hari, agregat 3 tahun, audit analis 2 tahun, foto tidak ada di Titeny v1.0.0.

## Batasan

Batasan dokumen ini: hanya definisi entitas, kunci, enum, dan aturan angka. Serialisasi JSON dan endpoint ada di `30-kontrak.md`. Perubahan skema butuh versi mayor, dual-read 30 hari, dan migrasi teruji.
