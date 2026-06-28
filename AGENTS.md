# BriefStudio Engineering Constitution

Dokumen ini adalah aturan kerja utama bagi seluruh human contributor dan AI Agent di repository BriefStudio. Instruksi task yang eksplisit dapat menentukan scope yang lebih sempit, tetapi tidak boleh menurunkan standar kualitas, keamanan, atau kompatibilitas yang ditetapkan di sini.

## Project Goal

Membangun platform penyusunan creative brief yang menghasilkan JSON terstruktur, konsisten, tervalidasi, dan dapat digunakan kembali untuk berbagai workflow produksi iklan berbasis AI.

## Product Vision

BriefStudio harus membantu pengguna berpindah dari kebutuhan kampanye menuju brief siap eksekusi dengan cepat, tanpa mengharuskan pengguna memahami prompt engineering atau struktur data internal.

Produk harus:

- Mudah digunakan oleh marketer, desainer, dan content creator.
- Menjaga konsistensi brand lintas format.
- Mendukung workflow yang dapat diprediksi dan dipulihkan.
- Menghasilkan output yang kompatibel, versioned, dan dapat divalidasi.
- Bertumbuh tanpa mengorbankan fitur atau output pengguna lama.

## Architecture Principles

1. **Incremental over rewrite.** Ubah sistem melalui langkah kecil yang dapat diverifikasi.
2. **Separation of concerns.** Pisahkan UI, state, domain model, JSON generation, validation, rendering, dan export.
3. **Single source of truth.** Setiap nilai aplikasi harus memiliki satu pemilik state yang jelas.
4. **Contract first.** Perubahan pada output JSON dimulai dari specification, schema, dan compatibility plan.
5. **Composition over duplication.** Bangun Studio dari reusable components dan shared schema fragments.
6. **Progressive enhancement.** Core workflow harus tetap dapat digunakan pada browser modern tanpa ketergantungan yang tidak diperlukan.
7. **Accessibility by default.** Gunakan semantic HTML, keyboard interaction, visible focus, dan accessible naming.
8. **Explicit boundaries.** Studio-specific behavior tidak boleh bocor ke shared engine tanpa abstraksi yang disepakati.
9. **Backward compatibility.** Existing behavior dan output harus dilindungi oleh tests sebelum diubah.
10. **Evidence-based change.** Setiap perubahan harus memiliki alasan, verification steps, dan risiko yang dapat ditinjau.

## Coding Standards

- Gunakan bahasa dan fitur web platform yang didukung target browser project.
- Tulis fungsi kecil dengan satu tanggung jawab yang jelas.
- Hindari global mutable state.
- Hindari inline event handler pada kode baru.
- Hindari raw `innerHTML` untuk user-controlled data.
- Gunakan `textContent`, DOM APIs, atau sanitization yang teruji.
- Gunakan semantic HTML sebelum membuat custom interactive element.
- Jangan menyembunyikan error; berikan failure state yang dapat dipahami.
- Validate input pada boundary sebelum membuat atau mengimpor JSON.
- Tambahkan test untuk bug fix, schema change, dan shared utility.
- Comment menjelaskan alasan atau constraint, bukan mengulang isi kode.
- Jangan menambahkan dependency tanpa menjelaskan kebutuhan, ukuran, maintenance, dan alternatifnya.

## Naming Convention

### General

- Gunakan nama bahasa Inggris untuk identifier teknis, schema keys, file, dan folder.
- Gunakan Indonesian copy untuk UI selama product language belum berubah.
- Gunakan istilah domain yang sama di UI, kode, schema, dan dokumentasi.

### JavaScript

- Variable dan function: `camelCase`
- Constructor atau component: `PascalCase`
- Constant lintas modul: `UPPER_SNAKE_CASE`
- Boolean: awali dengan `is`, `has`, `can`, atau `should`
- Event handler: awali dengan `handle`
- Builder: akhiri dengan `Builder` atau gunakan pola `buildX`
- Validator: gunakan pola `validateX`

### CSS

- Gunakan nama component yang deskriptif, bukan singkatan baru.
- State harus konsisten, misalnya `is-active` atau `data-state="active"`.
- Gunakan design tokens untuk warna, spacing, typography, radius, shadow, dan motion.
- Hindari selector yang bergantung pada urutan DOM bila ownership component dapat digunakan.

### JSON

- Gunakan `snake_case` untuk key eksternal.
- Gunakan controlled values yang stabil dan tidak berasal langsung dari label UI.
- Setiap document wajib memiliki `schema_version`, `studio`, dan `task_type`.

## Folder Convention

- `src/app/`: bootstrap, routing mode, registry, dan shared application state.
- `src/components/`: reusable UI components tanpa business logic Studio.
- `src/engine/`: schema, validation, generation, migration, summary, dan export.
- `src/studios/<studio>/`: config, state adapter, validator, builder, dan Studio-specific UI.
- `src/styles/`: tokens, base, layout, components, dan responsive styles.
- `src/utils/`: pure utilities yang tidak memiliki domain ownership lain.
- `tests/unit/`: pure function dan component behavior.
- `tests/schema/`: fixtures, validation, migration, dan compatibility tests.
- `tests/e2e/`: critical user workflows.
- `docs/`: product dan engineering documentation.
- `docs/adr/`: keputusan arsitektur yang signifikan dan immutable secara historis.

Jangan membuat folder generik seperti `misc`, `helpers`, atau `common` tanpa ownership yang jelas.

## Studio Rules

Studio yang wajib didukung:

1. Feed Studio
2. Carousel Studio
3. Typography Ads Studio
4. Cover Studio
5. Thumbnail Studio
6. Grid Studio
7. Product Showcase Studio

Setiap Studio wajib:

- Terdaftar pada satu Studio registry.
- Memiliki stable identifier.
- Mendefinisikan initial state, validation rules, dan JSON builder.
- Menggunakan shared fields untuk brand, content, visual, audience, output, dan constraints ketika relevan.
- Mengisolasi field khusus pada `studio_data`.
- Mendukung shared output renderer dan exporters melalui adapter yang terdokumentasi.
- Menjaga input pengguna saat berpindah tab atau Studio sesuai product requirement.
- Menyediakan error yang spesifik dan dapat diperbaiki pengguna.
- Memiliki schema fixtures dan compatibility tests.

Jangan menyamakan dua Studio hanya karena rasio output serupa. Cover Studio dan Story/Reels, misalnya, harus memiliki keputusan product scope yang eksplisit.

## JSON Rules

Semua Studio harus menghasilkan JSON yang kompatibel dengan canonical envelope yang didefinisikan pada `docs/json-spec.md`.

Aturan wajib:

- Sertakan `schema_version`, `studio`, dan `task_type`.
- Gunakan struktur shared untuk `brand`, `content`, `visual`, `audience`, `output`, `assets`, dan `constraints`.
- Simpan data khusus Studio di `studio_data`.
- Jangan mengubah arti key yang sudah dipublikasikan tanpa menaikkan versi dan menyediakan migration/adapter.
- Jangan bergantung pada label UI sebagai machine value.
- Jangan mengirim nilai kosong yang tidak bermakna apabila field bersifat optional.
- Validate sebelum render, copy, download, atau export.
- Exporter tidak boleh mengasumsikan schema dari satu Studio.
- Pertahankan legacy output melalui compatibility adapter selama periode migrasi.
- Tambahkan fixture untuk setiap Studio dan setiap schema version yang didukung.

## Refactor Rules

- Jangan mengubah UI tanpa instruksi dan persetujuan eksplisit.
- Jangan mengubah workflow user tanpa product decision.
- Jangan menghapus fitur yang sudah ada.
- Prioritaskan backward compatibility.
- Hindari duplicate code.
- Gunakan reusable component untuk pola yang benar-benar sama.
- Lindungi current behavior dengan tests sebelum memindahkan kode.
- Satu refactor harus memiliki scope dan rollback path yang jelas.
- Jangan mencampur refactor struktural dengan redesign visual atau fitur baru.
- Jangan melakukan big-bang rewrite.
- Update dokumentasi dan ADR ketika boundary atau contract berubah.

## Git Rules

- Gunakan branch yang terfokus untuk setiap perubahan.
- Jangan force-push branch bersama tanpa koordinasi.
- Jangan commit secret, credential, generated artifact, atau local configuration.
- Gunakan Conventional Commits:

```text
feat: add a user-visible capability
fix: correct faulty behavior
refactor: restructure code without changing behavior
docs: change documentation only
style: change formatting or presentation without logic changes
test: add or update tests
chore: update tooling or repository maintenance
```

- Commit harus kecil, coherent, dan dapat direview.
- Subject commit menggunakan imperative mood dan menjelaskan outcome.
- Breaking change harus ditandai dan memiliki migration note.

## Pull Request Rules

Semua perubahan besar harus dijelaskan. Pull request minimal memuat:

- Problem dan objective
- Scope dan non-goals
- Pendekatan yang dipilih
- File atau boundary yang berubah
- Dampak UI, workflow, dan JSON compatibility
- Risiko dan rollback plan
- Verification steps dan hasil test
- Screenshot untuk perubahan visual
- Contoh before/after untuk perubahan schema
- Dokumentasi atau ADR terkait

PR tidak boleh digabungkan jika required checks gagal, behavior penting tidak teruji, atau compatibility impact belum dijelaskan.

## Quality Rules

Selalu prioritaskan:

1. Maintainability
2. Scalability
3. Readability
4. Reusability

Definition of quality:

- Behavior penting memiliki automated tests.
- Tidak ada known runtime error pada critical workflow.
- Input dan output divalidasi.
- UI dapat digunakan dengan keyboard.
- Shared logic tidak diduplikasi antar-Studio.
- Naming menjelaskan domain intent.
- Perubahan dapat ditinjau tanpa membaca seluruh application.
- Dokumentasi tetap sinkron dengan implementasi.

Jika keempat prioritas bertentangan dengan delivery speed, pilih solusi terkecil yang aman, terdokumentasi, dan menyediakan jalur peningkatan berikutnya.
