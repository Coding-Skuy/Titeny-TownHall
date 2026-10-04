# 20 Navigasi3 — Titeny Desktop Analis (Varian 1)

> Status: disahkan V1. Navigasi3 bersifat opsional di framework tetapi dokumen ini menguncinya untuk desktop.

## 1. Pilihan

Desktop analis memakai **Kotlin Multiplatform + Compose Multiplatform + Navigation3**
(`androidx.navigation3:navigation3-runtime:1.0.0`, `navigation3-ui:1.0.0`).
Web memakai router bawaan SvelteKit 2 (analog, bukan Navigation3) — peta rute web ada di §5.

## 2. Graf Navigasi Desktop (terkunci, 5 tujuan)

```
Masuk (Login) ──> AntreanValidasi ──> DetailFlag (:eventId)
     │                    ├─────────> TahanPrediksi
     │                    ├─────────> BandingModel
     │                    ├─────────> KelolaRekomendasi
     │                    └─────────> Ekspor
```

- Tujuan awal: `Masuk` bila token kedaluwarsa, selain itu `AntreanValidasi`.
- `DetailFlag` menerima argumen `eventId: String` (pola `evt_YYYYMMDD_NNNNNN`); argumen tak valid → kembali ke antrean + snackbar.
- Back stack maks 20 entri; Deep link `titeny://analis/flag/<eventId>` didukung untuk tautan dari email pager.

## 3. State dan Restorasi

- State tiap tujuan disimpan via `rememberSaveable` + `SavedStateHandle`; filter antrean (komoditas, status) bertahan saat rotasi jendela/resize.
- Posisi scroll daftar dipertahankan per tujuan selama sesi; tidak dipertahankan lintas restart (sengaja, agar analis selalu mulai dari antrean terbaru).
- Transisi: tanpa animasi kustom di V1 (default Navigation3), untuk menjaga kinerja pada laptop analis spek menengah.

## 4. Contoh Rangka (Kotlin, ringkas normatif)

```kotlin
@Serializable data object Masuk
@Serializable data object AntreanValidasi
@Serializable data class DetailFlag(val eventId: String)
@Serializable data object TahanPrediksi
@Serializable data object BandingModel
@Serializable data object KelolaRekomendasi
@Serializable data object Ekspor

val backStack = rememberNavBackStack(Masuk)
NavDisplay(
  backStack = backStack,
  onBack = { backStack.removeLastOrNull() },
  entryProvider = entryProvider {
    entry<Masuk> { LayarMasuk(keAntrean = { backStack.add(AntreanValidasi) }) }
    entry<AntreanValidasi> { LayarAntrean(buka = { id -> backStack.add(DetailFlag(id)) }) }
    entry<DetailFlag> { flag -> LayarDetail(flag.eventId, kembali = { backStack.removeLastOrNull() }) }
    entry<TahanPrediksi> { LayarTahan() }
    entry<BandingModel> { LayarBanding() }
    entry<KelolaRekomendasi> { LayarRekomendasi() }
    entry<Ekspor> { LayarEkspor() }
  }
)
```

## 5. Analogi Rute Web (SvelteKit 2, untuk paritas)

| Desktop (Navigation3) | Web (SvelteKit) |
|-----------------------|-----------------|
| Masuk | `/masuk` |
| AntreanValidasi | (tidak ada — analis memakai desktop) |
| DetailFlag | (tidak ada) |
| TahanPrediksi | (tidak ada) |
| KelolaRekomendasi | `/insight/ceo/rekomendasi` |
| Ekspor (massal) | `/insight/ceo/ekspor` (500 baris) |

Rute petani/vendor/CEO: `/insight/petani`, `/insight/vendor`, `/insight/ceo` + `+page.ts` memuat `komoditas_id`, `pasar_id`/`zona` dari query.
