# BriefStudio

## Tentang BriefStudio

BriefStudio adalah aplikasi berbasis browser untuk menyusun creative brief iklan menjadi JSON terstruktur yang dapat digunakan oleh AI design tools, image generators, atau workflow produksi kreatif lainnya.

Project ini saat ini berada pada tahap prototype yang berfungsi. Fokus engineering berikutnya adalah memperkuat kontrak data, modularitas, aksesibilitas, pengujian, dan developer experience tanpa mengubah pengalaman pengguna yang sudah ada secara tidak terkontrol.

## Visi Project

BriefStudio bertujuan menjadi workspace yang sederhana, cepat, dan konsisten untuk menerjemahkan kebutuhan kampanye menjadi instruksi visual yang dapat dieksekusi. Satu model brief yang kompatibel harus dapat melayani berbagai format kreatif tanpa memaksa pengguna memahami struktur prompt atau JSON secara manual.

Prinsip produk utamanya:

- Mempercepat proses dari ide ke brief siap produksi.
- Menjaga konsistensi brand di berbagai format iklan.
- Menghasilkan data yang dapat diproses ulang, divalidasi, dan diekspor.
- Menawarkan workflow yang mudah dipahami oleh desainer, marketer, dan content creator.
- Bertumbuh secara incremental tanpa mengorbankan kompatibilitas output lama.

## Cara Kerja BriefStudio

1. Pilih Studio sesuai format konten yang ingin dibuat.
2. Isi informasi brand, pesan, visual, audiens, dan output yang tersedia.
3. BriefStudio membaca konfigurasi aktif dan membentuk object brief.
4. Object tersebut diserialisasi menjadi JSON dan ditampilkan untuk ditinjau.
5. JSON dapat disalin, diunduh, atau diteruskan ke workflow AI yang didukung.

Seluruh proses saat ini berjalan di browser. Tidak ada backend, database, autentikasi, atau pengiriman data form ke server BriefStudio.

## Studio Mode

BriefStudio menargetkan tujuh Studio berikut:

| Studio | Tujuan |
| --- | --- |
| Feed Studio | Menyusun iklan feed dengan brand, copy, komposisi, audiens, dan output terperinci. |
| Carousel Studio | Merancang rangkaian slide yang kohesif, termasuk peran dan konten setiap slide. |
| Typography Ads Studio | Membuat iklan dengan tipografi sebagai elemen visual utama. |
| Cover Studio | Menyusun cover vertikal atau platform-specific dengan hierarki dan safe area yang jelas. |
| Thumbnail Studio | Membuat brief thumbnail yang terbaca pada ukuran kecil dan berorientasi CTR. |
| Grid Studio | Menyusun sembilan tile yang kuat secara individual dan kohesif sebagai grid 3x3. |
| Product Showcase Studio | Membuat creative brief produk yang berorientasi konversi dan platform. |

> Catatan status: implementasi saat ini menggunakan nama **Story/Reels** untuk mode yang paling dekat dengan Cover Studio. Penyelarasan terminology dan scope akan dilakukan melalui keputusan produk sebelum perubahan implementasi.

## Arsitektur Project

Implementasi saat ini adalah single-page application tanpa framework:

- `index.html` memuat struktur HTML dan referensi asset aplikasi.
- `style.css` memuat seluruh styling, design token, dan responsive rules.
- `app.js` memuat state, interaction helpers, generator JSON, output renderer, dan exporter.
- Nilai form utama disimpan pada DOM.
- Carousel dan Grid memiliki state tambahan dalam array JavaScript.
- Setiap Studio membangun object JSON secara independen.
- Output terakhir disimpan pada state browser dan dapat disalin atau diunduh.

Arsitektur target menggunakan incremental modularization. UI dan workflow saat ini dipertahankan, sementara concerns dipisahkan menjadi application shell, reusable components, Studio modules, JSON engine, styles, dan tests.

Dokumentasi lebih lanjut:

- [Architecture](docs/architecture.md)
- [JSON Specification](docs/json-spec.md)
- [Engineering Roadmap](docs/roadmap.md)
- [Architecture Decision Records](docs/adr/0001-incremental-vanilla-architecture.md)
- [Changelog](docs/changelog.md)

## Menjalankan Project

Tidak ada build step atau dependency installation pada versi saat ini.

### Opsi 1: buka langsung

Buka `index.html` pada browser modern. Beberapa browser dapat membatasi Clipboard API ketika halaman dijalankan melalui `file://`.

### Opsi 2: static server lokal

Direkomendasikan menjalankan static server dari root repository:

```bash
python3 -m http.server 8000
```

Kemudian buka `http://localhost:8000`.

Static server lain seperti `npx serve` atau extension Live Server juga dapat digunakan. Tidak ada environment variable yang diperlukan.

## Tech Stack

- HTML5
- CSS3 dan CSS custom properties
- Vanilla JavaScript (ES6+)
- Browser DOM, Clipboard, Local Storage, dan Blob APIs
- Google Fonts: Inter, Space Grotesk, dan JetBrains Mono

Belum ada framework UI, TypeScript, bundler, package manager, backend, atau database.

## Folder Structure

### Saat ini

```text
briefstudio/
├── .editorconfig
├── .gitignore
├── AGENTS.md
├── LICENSE
├── README.md
├── app.js
├── assets/
├── components/
├── docs/
├── json/
├── studios/
├── index.html
└── style.css
```

### Target

Struktur ini adalah arah engineering, bukan struktur implementasi saat ini:

```text
briefstudio/
├── index.html
├── public/
│   └── assets/
├── src/
│   ├── app/
│   ├── components/
│   ├── engine/
│   ├── studios/
│   │   ├── feed/
│   │   ├── carousel/
│   │   ├── typography/
│   │   ├── cover/
│   │   ├── thumbnail/
│   │   ├── grid/
│   │   └── product/
│   ├── styles/
│   ├── utils/
│   └── main.js
├── tests/
│   ├── unit/
│   ├── schema/
│   └── e2e/
├── docs/
│   └── adr/
└── README.md
```

Perubahan menuju struktur target harus dilakukan bertahap dan mengikuti compatibility tests.

## Roadmap

Roadmap dibagi menjadi enam Sprint:

1. **Engineering Foundation** — dokumentasi, prinsip arsitektur, dan kontrak awal.
2. **Foundation Refactor** — test harness, modular boundary, dan tooling dasar tanpa redesign.
3. **Shared Component System** — reusable controls, event handling, dan state yang konsisten.
4. **Unified JSON Engine** — schema versioning, validation, adapters, dan exporters.
5. **Studio Migration** — migrasi tujuh Studio secara bertahap dengan backward compatibility.
6. **Production Hardening** — accessibility, responsive QA, CI, performance, dan release readiness.

Detail objective, deliverables, risiko, dan exit criteria tersedia di [docs/roadmap.md](docs/roadmap.md).

## Status Development

**Current stage:** Sprint 2 — Foundation Refactor.

- Prototype utama dapat digunakan.
- Tujuh slot Studio telah didefinisikan pada product vision.
- JSON telah dapat dibuat, disalin, dan diunduh.
- Belum ada unified JSON schema atau automated tests.
- CSS dan JavaScript telah dipisahkan dari dokumen HTML tanpa perubahan behavior.
- Boundary component, Studio module, dan JSON engine belum diimplementasikan.

Dokumentasi status ini tidak menyatakan bahwa seluruh Studio telah memiliki tingkat kelengkapan yang sama.

## Kontribusi

Sebelum berkontribusi:

1. Baca [AGENTS.md](AGENTS.md) sebagai Engineering Constitution.
2. Baca dokumen arsitektur dan JSON specification.
3. Buat perubahan yang terfokus dan tidak mengubah UI atau workflow tanpa persetujuan.
4. Tambahkan atau perbarui tests ketika behavior berubah.
5. Gunakan Conventional Commits, misalnya `docs: clarify studio contract`.
6. Jelaskan dampak, risiko, kompatibilitas, dan langkah verifikasi pada pull request.

Perubahan besar harus memiliki issue, design note, atau ADR sebelum implementasi.

## License

License project belum ditetapkan pada repository ini. Sampai file `LICENSE` ditambahkan oleh maintainer, penggunaan, modifikasi, dan distribusi source code tidak otomatis memperoleh izin open-source.

Maintainer perlu menetapkan license secara eksplisit sebelum release publik pertama.
