# Belajar JavaScript — Tutorial & Cheatsheet

Website statis untuk belajar JavaScript: tutorial konseptual, cheatsheet referensi cepat, dan mini project latihan.

## Struktur

```
.
├── index.html            # shell website (sidebar, TOC, search)
├── assets/
│   ├── style.css
│   └── app.js             # router hash + render markdown (marked.js + highlight.js)
├── content/
│   ├── tutorial.md         # tutorial JS: dasar -> closure, this, async/event loop, class, module
│   ├── cheatsheet.md       # referensi cepat sintaks & fungsi bawaan
│   └── projects.md         # 8 mini project latihan, pemula -> lanjutan
└── .claude/launch.json     # konfigurasi dev server lokal
```

## Menjalankan

File markdown dimuat lewat `fetch`, jadi harus diakses lewat HTTP server lokal (tidak bisa dibuka langsung sebagai `file://`):

```bash
python3 -m http.server 8843
```

Lalu buka `http://localhost:8843`.

## Konten

- **Tutorial** — 27 bab, mencakup variabel (`var`/`let`/`const`), tipe data, operator & type coercion, string, percabangan, perulangan, fungsi, scope & hoisting, closure, `this`, array & array methods, object & destructuring, error handling, asynchronous JavaScript (callback, Promise, async/await, event loop), DOM manipulation, event handling, JSON, Fetch API, class & OOP, module (`import`/`export`), dan kesalahan umum/kuirk JavaScript.
- **Cheatsheet** — tabel referensi cepat: tipe data, operator, array & object methods, class, DOM, storage, dan daftar kesalahan umum.
- **Mini Project** — 8 latihan bertingkat (kalkulator, tebak angka, konversi suhu, manajemen nilai mahasiswa, to-do list, validator form, buku kontak dengan localStorage, dashboard cuaca dengan fetch API) untuk mempraktikkan konsep dari tutorial.

## Tech stack

Vanilla HTML/CSS/JS, [marked.js](https://marked.js.org/) untuk parsing markdown, dan [highlight.js](https://highlightjs.org/) untuk syntax highlighting — semua dimuat lewat CDN, tanpa build step.
