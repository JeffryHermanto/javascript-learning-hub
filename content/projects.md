# Mini Project Latihan JavaScript

Kumpulan latihan project untuk mempraktikkan konsep dari [Tutorial JavaScript](tutorial.html), diurutkan dari mudah ke sulit. Kerjakan berurutan — tiap project sengaja dirancang memakai konsep dari project sebelumnya.

Cara pakai: baca **Tujuan** dan **Requirement**, coba kerjakan sendiri dulu sebelum melihat **Hint**. Semua project bisa dikerjakan dengan JavaScript murni (vanilla), tanpa framework — cukup file `.html` + `.js`, atau `node file.js` untuk yang berbasis console.

---

## Level Pemula

### Project 1 — Kalkulator Sederhana

**Konsep:** input/output (`prompt`/console), operator, percabangan, fungsi.

**Tujuan:** program menerima dua angka dan satu operator (`+ - * /`), lalu menampilkan hasilnya.

**Requirement:**

- Buat fungsi `hitung(a, b, operator)` yang mengembalikan hasil sesuai operator menggunakan `switch`.
- Bisa dijalankan di browser (pakai `prompt()` untuk input, `alert()`/`console.log` untuk output) atau di Node.js dengan angka & operator hardcoded di variabel.
- Tangani pembagian dengan 0 — kembalikan pesan error, jangan biarkan hasilnya `Infinity` tanpa penjelasan.
- Validasi input: jika operator tidak dikenali, tampilkan pesan "Operator tidak valid".

**Hint:** `switch (operator) { case "+": return a + b; ... }`. Untuk pembagian nol: `if (operator === "/" && b === 0) return "Error: tidak bisa dibagi nol";` sebelum melakukan pembagian.

**Tantangan bonus:** buat versi HTML dengan `<input>` dan tombol, tampilkan hasil lewat DOM manipulation alih-alih `alert()`.

---

### Project 2 — Tebak Angka

**Konsep:** perulangan, percabangan, `Math.random()`, closure (menyimpan state).

**Tujuan:** komputer memilih angka acak 1–100, user menebak sampai benar, program memberi petunjuk "lebih besar"/"lebih kecil".

**Requirement:**

- Gunakan `Math.floor(Math.random() * 100) + 1` untuk menghasilkan angka rahasia.
- Buat fungsi `buatPermainanTebak()` yang me-return objek dengan method `tebak(angka)` — gunakan **closure** supaya angka rahasia tidak bisa diakses langsung dari luar.
- `tebak(angka)` mengembalikan `"lebih besar"`, `"lebih kecil"`, atau `"benar"`.
- Hitung dan tampilkan jumlah percobaan setelah tebakan benar.

**Hint:** pola closure sama seperti `buatCounter()` di tutorial bagian Closure — variabel `angkaRahasia` dan `jumlahPercobaan` didefinisikan di luar fungsi yang di-return supaya "tersembunyi".

**Tantangan bonus:** batasi maksimal 7 percobaan, kalah jika habis. Jalankan lewat browser dengan `prompt()` dalam `while` loop.

---

### Project 3 — Konversi Suhu & Kalkulator Multi-Fungsi

**Konsep:** fungsi murni (pure function), arrow function, default parameter, objek sebagai "menu".

**Tujuan:** buat beberapa fungsi konversi suhu (`celciusKeFahrenheit`, `celciusKeKelvin`, dst) dan sebuah "menu" berbasis objek yang memanggilnya secara dinamis.

**Requirement:**

- Minimal 3 fungsi konversi arrow function, masing-masing menerima `number` dan mengembalikan `number`.
- Buat objek `menuKonversi` yang key-nya nama konversi dan value-nya adalah fungsi konversinya (contoh: `{ "c-ke-f": celciusKeFahrenheit }`), lalu panggil lewat `menuKonversi[pilihan](nilai)`.
- Semua fungsi konversi harus **pure** — tidak mengubah variabel di luar dirinya, hanya menerima input dan mengembalikan output baru.

**Hint:** memanggil fungsi lewat object seperti ini adalah pola yang sering menggantikan `switch` panjang di JavaScript — disebut *lookup table* / *dictionary dispatch*.

---

## Level Menengah

### Project 4 — Manajemen Nilai Mahasiswa (Array Methods)

**Konsep:** array, `map`/`filter`/`reduce`, method chaining, destructuring.

**Tujuan:** program menyimpan data mahasiswa (nama + nilai) dalam array of objects, lalu menghitung rata-rata, nilai tertinggi/terendah, dan daftar yang lulus (nilai >= 60).

**Requirement:**

- Simpan data sebagai array of objects: `[{ nama: "Ani", nilai: 85 }, ...]` minimal 8 data.
- Gunakan `reduce` untuk menghitung rata-rata — **jangan** pakai `for` loop manual.
- Gunakan `filter` + `map` untuk mendapatkan daftar nama yang lulus, lalu `sort` berdasarkan nilai tertinggi.
- Gunakan `Math.max`/`Math.min` dikombinasikan dengan `map` untuk mencari nilai tertinggi/terendah.
- Tampilkan hasil akhir dengan `console.table(dataMahasiswa)`.

**Hint:** `data.map(m => m.nilai)` untuk mengambil semua nilai sebagai array angka biasa sebelum dipakai `Math.max(...arrArrays)` (perhatikan perlu spread `...`).

**Tantangan bonus:** kelompokkan mahasiswa jadi grade A/B/C/D pakai `reduce` yang mengembalikan object (`{ A: [...], B: [...] }`).

---

### Project 5 — Aplikasi To-Do List (DOM Manipulation)

**Konsep:** DOM manipulation, event handling, event delegation, array method untuk state.

**Tujuan:** aplikasi to-do list di browser — tambah, tandai selesai, dan hapus tugas.

**Requirement:**

- Simpan daftar tugas sebagai array of objects di JavaScript (`{ id, teks, selesai }`), **jangan** baca langsung dari DOM sebagai sumber data (DOM hanya merefleksikan state, bukan sebaliknya).
- Buat fungsi `render()` yang membangun ulang tampilan `<ul>` dari array setiap kali data berubah.
- Tombol tambah memakai `event.preventDefault()` jika di dalam `<form>`.
- Klik pada tugas untuk toggle selesai (`classList.toggle("selesai")` + strikethrough via CSS), tombol hapus memakai `array.filter()` untuk membuang item dari state.
- Gunakan **event delegation**: satu `addEventListener` di elemen `<ul>` induk, bukan listener terpisah di tiap `<li>` (karena `<li>` dibuat dinamis).

**Hint:** simpan `data-id` di setiap `<li>` supaya event delegation tahu item mana yang diklik: `event.target.closest("li").dataset.id`.

**Tantangan bonus:** simpan state ke `localStorage` setiap kali berubah, dan load kembali saat halaman dibuka (lihat Project 8 untuk pola localStorage).

---

### Project 6 — Validator Formulir & Pengolah String

**Konsep:** string methods, regex dasar, validasi, error handling.

**Tujuan:** validasi formulir pendaftaran (nama, email, password) sebelum "dikirim", menampilkan pesan error yang jelas.

**Requirement:**

- Buat fungsi `validasiNama(nama)`, `validasiEmail(email)`, `validasiPassword(password)` — masing-masing mengembalikan `{ valid: boolean, pesan: string }`.
- Nama: tidak boleh kosong setelah `trim()`, minimal 3 karakter.
- Email: harus mengandung `@` dan `.` setelah `@` (boleh pakai regex sederhana `/^[^\s@]+@[^\s@]+\.[^\s@]+$/`).
- Password: minimal 8 karakter, mengandung minimal 1 angka (`/\d/`).
- Kumpulkan semua fungsi validasi dalam satu fungsi `validasiForm(data)` yang mengembalikan array semua pesan error (array kosong berarti valid).

**Hint:** `.test(string)` pada regex mengembalikan `true`/`false` — cocok untuk validasi tanpa perlu ekstraksi hasil match.

**Tantangan bonus:** hubungkan ke form HTML sungguhan, tampilkan pesan error di bawah tiap input secara real-time saat user mengetik (`input` event).

---

## Level Lanjutan

### Project 7 — Buku Kontak dengan LocalStorage

**Konsep:** object/array kompleks, `localStorage`, JSON, CRUD (Create Read Update Delete).

**Tujuan:** aplikasi buku kontak yang datanya tersimpan permanen di browser lewat `localStorage`, bertahan meskipun halaman di-refresh.

**Requirement:**

- Buat modul kecil (kumpulan fungsi) untuk CRUD: `tambahKontak`, `hapusKontak`, `updateKontak`, `cariKontak(keyword)`.
- Setiap fungsi yang mengubah data harus membaca dari `localStorage`, memodifikasi array di memori, lalu menulis kembali dengan `JSON.stringify` ke `localStorage`.
- `cariKontak` memakai `.filter()` dengan `.includes()` (case-insensitive — `toLowerCase()` dulu di kedua sisi) untuk mencari berdasarkan nama.
- Buat UI HTML sederhana: form tambah kontak, daftar kontak dengan tombol hapus/edit, kotak pencarian.

**Hint:** karena `localStorage` hanya menyimpan string, selalu `JSON.parse(localStorage.getItem("kontak") || "[]")` supaya tidak error saat data belum ada.

**Tantangan bonus:** tambahkan validasi supaya tidak ada dua kontak dengan nomor telepon yang sama, gunakan `some()` untuk mengecek duplikat sebelum menambah.

---

### Project 8 — Dashboard Cuaca dengan Fetch API

**Konsep:** `async`/`await`, `fetch`, error handling, `Promise.all`, DOM rendering dari data eksternal.

**Tujuan:** aplikasi yang mengambil data dari API publik (misal [Open-Meteo](https://open-meteo.com/) yang gratis tanpa API key) dan menampilkannya di halaman.

**Requirement:**

- Buat fungsi `async ambilCuaca(kota)` yang melakukan `fetch` ke API, cek `response.ok`, lalu `throw new Error` dengan pesan jelas jika gagal.
- Tangani loading state: tampilkan "Memuat..." sebelum data datang, ganti dengan data (atau pesan error) setelah selesai — gunakan `try/catch/finally`.
- Buat input kota, tombol "Cari", dan render hasil (suhu, kondisi) ke DOM.
- Tambahkan fitur bandingkan 2-3 kota sekaligus memakai `Promise.all` supaya semua request berjalan paralel, bukan berurutan.

**Hint:** untuk debugging network request, buka tab **Network** di DevTools browser untuk melihat request/response yang sebenarnya terjadi.

**Tantangan bonus:** cache hasil pencarian di `localStorage` dengan timestamp, dan hanya fetch ulang ke API jika data sudah lebih dari 10 menit — melatih kombinasi closure, Promise, dan storage sekaligus.
