> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# Platform ~ Aplikasi (Titeny)

Folder ini adalah **indeks aplikasi** divisi Titeny: tujuh repo kode yang hidup di
organisasi Coding-Skuy ([tim Titeny](https://github.com/orgs/Coding-Skuy/teams/titeny/repositories)),
ditautkan ke repo ini sebagai **git submodule**.

- `platform/versions/` — dokumen formal beku (BRD/PRD/FRD/FSD/SRS).
- `platform/apps/` — kode aplikasi (submodule, repo terpisah).

## Repo aplikasi

| Submodule | Repo | Peran | Stack |
|---|---|---|---|
| `app-kmp/` | [titeny-app-kmp](https://github.com/Coding-Skuy/titeny-app-kmp) | Aplikasi klien (papan insight mobile) | Kotlin MPP / CMP |
| `web/` | [titeny-web](https://github.com/Coding-Skuy/titeny-web) | Dashboard utama (papan 3 peran) | Bun + SvelteKit + TS |
| `backend-service/` | [titeny-backend-service](https://github.com/Coding-Skuy/titeny-backend-service) | API insight; satu-satunya tulis ke DB titeny | Rust |
| `ai-models/` | [titeny-ai-models](https://github.com/Coding-Skuy/titeny-ai-models) | Prediksi harga/panen (MAPE H+1/H+7) | Python (training) + Rust (serving) |
| `data-pipeline/` | [titeny-data-pipeline](https://github.com/Coding-Skuy/titeny-data-pipeline) | Ingest 11 event Lumbung/TitipO/Pasaree | — |
| `design/` | [titeny-design](https://github.com/Coding-Skuy/titeny-design) | Dashboard & kartu insight | Figma |
| `infra-devops/` | [titeny-infra-devops](https://github.com/Coding-Skuy/titeny-infra-devops) | Job terjadwal & secrets | CI/CD |

## Cara pakai

```bash
# clone beserta submodule
git clone --recurse-submodules https://github.com/Coding-Skuy/Titeny-TownHall.git
# bila repo sudah ter-clone tanpa submodule:
git submodule update --init --recursive
# perbarui ke commit main terbaru tiap repo app:
git submodule update --remote
```

## Batasan

Indeks ini hanya menautkan repo. Kode dan riwayatnya tetap milik repo app masing-masing;
keputusan versi di tulis di `platform/versions/vX.Y.Z/`, bukan di sini.