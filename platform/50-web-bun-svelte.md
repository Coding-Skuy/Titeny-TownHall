# 50 Web Bun + Svelte — Titeny (Varian 1)

> Status: disahkan V1. Stack terkunci: Bun 1.4.x + Svelte 5 + SvelteKit 2 + TypeScript 5.9.x.

## 1. Struktur Folder Terkunci

```
apps/web/
  src/
    routes/
      +layout.svelte
      +page.svelte                 # landing peran
      masuk/+page.svelte
      insight/
        petani/+page.svelte
        vendor/+page.svelte
        ceo/+page.svelte
      api/v1/...                   # hanya proxy tipis bila perlu; logika di server Bun
    lib/
      komponen/                    # KartuHarga.svelte, TabelVendor.svelte, MatriksCEO.svelte
      agregat.ts                   # format IDR, tanggal WIB
      api-klien.ts                 # fetch + timeout 12 dtk
    server/
      index.ts                     # entry Bun.serve
      pekerjaan/                   # hitung-04.00.ts, demand-04.30.ts
      validasi.ts                  # zod skema event
  svelte.config.js                 # adapter-node
  tsconfig.json                    # strict true
  package.json                     # packageManager bun
```

## 2. Versi dan Perintah

- `bun --version` → `1.4.x`; `svelte` → `5`; `@sveltejs/kit` → `2`; `typescript` → `5.9.x`; `zod` → `3.23.x`; `echarts` tidak dipakai di V1 (grafik memakai SVG Svelte ringan).
- Perintah: `bun install`, `bun run dev --port 5173`, `bun run build`, `bun run start --port 3000`, `bun test`.
- CI: `bun install --frozen-lockfile && bun run build && bun test` harus hijau sebelum merge.

## 3. Aturan Svelte 5 (Runes)

- State reaktif memakai `$state`, turunan memakai `$derived`, efek samping memakai `$effect` hanya untuk langganan/polling.
- Dilarang `$:` gaya Svelte 4 pada kode baru; dilarang menyimpan token di `$state` global tanpa enkapsulasi modul `sesi.ts`.
- Setiap grafik wajib punya `<table>` alternatif (aksesibilitas) — pola `GrafikDanTabel.svelte`.

```svelte
<script lang="ts">
  let { deret }: { deret: TitikHarga[] } = $props();
  let maks = $derived(Math.max(...deret.map((d) => d.harga_prediksi)));
</script>
```

## 4. Data Loading (SvelteKit 2)

- `+page.ts` memuat prediksi via `fetch('/api/v1/harga?...')` dengan `export const prerender = false`.
- Halaman petani/vendor memakai `stale-while-revalidate` 6 jam; papan CEO memakai revalidasi 15 menit + tombol segarkan.
- Form umpan-balik memakai `use:enhance` + fallback POST biasa bila JS mati.

## 5. Server Bun

- Satu proses `Bun.serve` melayani SSR + `/api/v1/*`; tidak ada server Node terpisah di V1.
- Job 04.00/04.30/23.xx memakai `setInterval` penjaga + kunci berkas agar tidak ganda; kegagalan 3× memicu log `pager: true`.
- Validasi event memakai `zod` persis mengikuti `data/20-kontrak-event.md`; pesan error Indonesia.
- Koneksi DB: SQLite (WAL) untuk V1, berkas `data/titeny.db`; migrasi bernomor `0001_awal.sql` dst.

## 6. Pengujian

- Unit: `bun test` untuk pembulatan 500 IDR, batas ±8%/hari, dan amplop error.
- Kontrak: 11 contoh event §data/20 harus lolos `validasi.ts` (fixture `fixtures/event/*.json`).
- Beban ringan: 100 req/detik baca cache-hit p95 < 400 ms pada laptop CI 2-core.
