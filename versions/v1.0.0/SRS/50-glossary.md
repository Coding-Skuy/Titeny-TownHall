> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# SRS Glossary

Titeny mengelola AI insight platform dengan prediksi harga 12 komoditas × 8 pasar dan 11 event idempoten.

## Terminology

- **TDD (Test-Driven Development):** Requirement → Red Test → Green Code → Refactor.
- **Acceptance Criteria:** Kondisi konkret yang harus dipenuhi feature untuk diterima QA.
- **QA Gate:** Barrier otomatis (test, coverage, review) sebelum merge/release.
- **DB per Service:** Database logis terpisah per divisi; satu cluster Postgres untuk pilot.
- **JWT Audiens:** Token JWT memiliki audiens ('aud') per service untuk validasi cross-service.

## Batasan

Glossary hanya referensi; definisi formal ada di spec teknis masing-masing.