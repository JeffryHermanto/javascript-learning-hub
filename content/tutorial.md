# Tutorial JavaScript

Panduan belajar JavaScript dari dasar sampai konsep menengah-lanjut, dengan penjelasan **konseptual** (bukan cuma sintaks) — bagaimana JavaScript benar-benar mengeksekusi kode di baliknya, kenapa bahasa ini dirancang seperti ini, dan jebakan umum yang perlu dihindari.

## 1. Pengantar: Apa dan Kenapa JavaScript

JavaScript dibuat oleh Brendan Eich tahun 1995 dalam waktu 10 hari, awalnya untuk membuat halaman web jadi interaktif di browser Netscape. Sejak itu JavaScript berkembang jauh melampaui browser: lewat Node.js (2009) JavaScript bisa menjalankan server, lewat React Native/Electron bisa membuat aplikasi mobile & desktop, dan kini menjadi salah satu bahasa paling banyak dipakai di dunia.

Kenapa ini penting untuk dipahami:

- JavaScript adalah **satu-satunya bahasa yang berjalan native di semua browser**. Tidak ada pilihan lain untuk membuat web interaktif di sisi klien.
- JavaScript **single-threaded** (satu alur eksekusi) tapi **non-blocking** lewat mekanisme *event loop* — ini konsep yang sangat berbeda dari bahasa seperti Java atau C yang biasanya multi-thread untuk konkurensi. Memahami event loop adalah kunci untuk tidak bingung dengan kode asynchronous.
- JavaScript adalah bahasa **dynamically typed** (tipe data ditentukan saat runtime, bukan saat kompilasi) dan punya banyak perilaku **type coercion** (konversi tipe otomatis) yang sering membingungkan pemula — misalnya `"5" + 1` menghasilkan `"51"`, tapi `"5" - 1` menghasilkan `4`.

**Konsep kunci yang akan berulang kali muncul di tutorial ini:** JavaScript mengeksekusi kode dalam satu *call stack*, menunda pekerjaan asynchronous ke *Web APIs* / *libuv* (di Node), lalu mengantre hasilnya kembali ke call stack lewat *event loop*. Ini menjelaskan hampir semua perilaku "aneh" JavaScript soal `setTimeout`, Promise, dan urutan eksekusi.

## 2. Menjalankan JavaScript

Ada tiga cara utama menjalankan JavaScript:

```html
<!-- 1. Inline di dalam tag <script> -->
<script>
  console.log("Hello dari HTML");
</script>

<!-- 2. File eksternal (disarankan) -->
<script src="app.js" defer></script>
```

```bash
# 3. Lewat Node.js (di luar browser, untuk server/CLI/tooling)
node app.js
```

Poin konseptual:

- Atribut `defer` penting: tanpa itu, browser akan **berhenti membaca HTML** (blocking) untuk mendownload dan menjalankan script tepat di posisi tag `<script>` berada. Dengan `defer`, script didownload paralel tapi baru dieksekusi setelah HTML selesai di-parse, dan tetap berjalan sesuai urutan dokumen.
- Browser & Node.js sama-sama menjalankan mesin **V8** (dari Chrome), tapi API yang tersedia berbeda: browser punya `window`, `document`, `fetch`; Node.js punya `require`/`module`, `fs`, `process` (tidak ada `window`/`document`).
- Console browser (buka DevTools dengan F12) adalah tempat tercepat untuk eksperimen kecil sebelum menulis di file.

## 3. Variabel: `var`, `let`, `const`

```js
var a = 1;    // cara lama, hindari
let b = 2;    // bisa diubah nilainya
const c = 3;  // tidak bisa di-assign ulang
```

Perbedaan ini bukan sekadar gaya penulisan — masing-masing punya perilaku scope yang berbeda:

| | `var` | `let` | `const` |
|---|---|---|---|
| Scope | function-scoped | block-scoped | block-scoped |
| Bisa di-reassign | ya | ya | tidak |
| Hoisting | di-hoist & diinisialisasi `undefined` | di-hoist tapi masuk "temporal dead zone" | di-hoist tapi masuk "temporal dead zone" |
| Redeklarasi di scope sama | boleh | error | error |

**Kenapa `var` dihindari di kode modern:** `var` hanya terikat pada *function scope*, bukan *block scope* (`{ }` di dalam `if`/`for`). Ini menyebabkan bug klasik:

```js
if (true) {
  var x = 10;
}
console.log(x); // 10 -> x "bocor" keluar dari blok if, ini mengejutkan bagi pemula

if (true) {
  let y = 10;
}
console.log(y); // ReferenceError: y is not defined -> sesuai ekspektasi
```

**`const` tidak berarti "immutable".** `const` hanya mencegah *reassignment* variabel, bukan mencegah perubahan isi objek/array:

```js
const arr = [1, 2, 3];
arr.push(4);       // OK — isi array berubah, tapi `arr` tetap menunjuk objek array yang sama
console.log(arr);  // [1, 2, 3, 4]

arr = [5, 6];       // TypeError: Assignment to constant variable
```

**Aturan praktis:** pakai `const` secara default, pakai `let` kalau variabel memang perlu diubah nilainya, dan hindari `var` sepenuhnya di kode baru.

## 4. Tipe Data

JavaScript punya 7 tipe **primitif** dan 1 tipe **object** (yang menaungi array, function, dan object biasa).

| Tipe | Contoh | Keterangan |
|---|---|---|
| `number` | `42`, `3.14`, `NaN`, `Infinity` | satu tipe untuk semua angka (integer & desimal sama-sama `number`) |
| `string` | `"halo"`, `'halo'`, `` `halo` `` | teks, immutable |
| `boolean` | `true`, `false` | |
| `undefined` | `let x;` | variabel dideklarasikan tapi belum diberi nilai |
| `null` | `let x = null;` | "sengaja kosong", di-assign secara eksplisit |
| `bigint` | `123n` | integer presisi arbitrer (lebih besar dari `Number.MAX_SAFE_INTEGER`) |
| `symbol` | `Symbol("id")` | nilai unik, biasa untuk key object yang tidak bentrok |
| `object` | `{}`, `[]`, `function(){}` | tipe "kotak" untuk segala struktur data kompleks |

Poin konseptual penting:

- **Tidak ada tipe integer terpisah.** Semua angka disimpan sebagai `double` 64-bit (IEEE 754) — sama seperti `double` di C. Ini kenapa `0.1 + 0.2 === 0.3` bernilai `false` (hasilnya `0.30000000000000004`), karena representasi biner desimal tidak selalu presisi.
- **`typeof null` menghasilkan `"object"`** — ini adalah bug historis dari implementasi JavaScript pertama yang tidak pernah diperbaiki karena akan merusak kompatibilitas kode lama. Anggap ini sebagai kuirk yang harus dihafal, bukan logika yang harus dipahami.
- **`undefined` vs `null`:** `undefined` berarti "belum diisi" (default dari sistem), `null` berarti "sengaja dikosongkan" (keputusan programmer). Cek gabungan keduanya dengan `== null` (loose equality otomatis mencakup `undefined` juga), atau cek satu-satu dengan `===`.

```js
typeof 42;          // "number"
typeof "halo";       // "string"
typeof true;         // "boolean"
typeof undefined;    // "undefined"
typeof null;         // "object" (kuirk historis!)
typeof {};           // "object"
typeof [];           // "object" (array adalah object khusus)
typeof function(){}; // "function"
```

## 5. Operator & Type Coercion

```js
// Aritmatika
+  -  *  /  %  **        // ** adalah pangkat (ES2016)

// Perbandingan
==   !=    // loose equality -> melakukan type coercion dulu
===  !==   // strict equality -> tanpa coercion, beda tipe = otomatis tidak sama

// Logika
&&  ||  !

// Nullish & optional chaining (ES2020)
??   // nullish coalescing
?.   // optional chaining
```

**Konsep terpenting di bagian ini: kapan pakai `==` vs `===`.** `==` akan mencoba mengonversi kedua sisi ke tipe yang sama sebelum membandingkan, dan aturan konversinya tidak selalu intuitif:

```js
0 == "0";        // true  ("0" dikonversi ke number 0)
0 == "";         // true  ("" dikonversi ke number 0)
0 == false;      // true  (false dikonversi ke number 0)
"" == false;     // true
null == undefined; // true (kasus khusus, hanya keduanya yang saling == satu sama lain)
NaN == NaN;      // false! NaN tidak pernah sama dengan apa pun, termasuk dirinya sendiri
```

**Aturan praktis: selalu pakai `===` dan `!==`**, kecuali kamu punya alasan spesifik memakai `==` (misalnya sengaja mengecek `x == null` untuk menangkap `null` dan `undefined` sekaligus).

**Nullish coalescing (`??`) vs logical OR (`||`).** Keduanya sering tertukar:

```js
let jumlah = 0;
console.log(jumlah || 10); // 10 -> karena 0 dianggap "falsy", padahal 0 itu valid!
console.log(jumlah ?? 10); // 0  -> ?? hanya fallback jika nilainya null/undefined, bukan semua falsy
```

Nilai yang dianggap **falsy** di JavaScript hanya ada 8: `false`, `0`, `-0`, `0n`, `""`, `null`, `undefined`, `NaN`. Semua nilai lain (termasuk `"0"`, `[]`, `{}`) dianggap **truthy**.

## 6. String & Template Literals

```js
const nama = "Budi";
const umur = 25;

// Concatenation cara lama
console.log("Nama saya " + nama + ", umur " + umur);

// Template literal (ES6) -> lebih terbaca, support multi-baris & ekspresi
console.log(`Nama saya ${nama}, umur ${umur + 1} tahun lagi`);

const multiBaris = `Baris pertama
Baris kedua`;
```

Method string yang paling sering dipakai:

```js
const s = "  Belajar JavaScript  ";

s.trim();               // "Belajar JavaScript" -> hapus spasi kiri/kanan
s.toUpperCase();         // "  BELAJAR JAVASCRIPT  "
s.toLowerCase();
s.length;                 // 23 (property, bukan method)
s.includes("Script");     // true
s.slice(2, 9);            // "Belajar" -> substring dari index 2 sampai sebelum 9
s.split(" ");             // ["", "", "Belajar", "JavaScript", "", ""]
s.replace("Belajar", "Suka"); // ganti kemunculan pertama
s.padStart(5, "0");       // tambah karakter di depan sampai panjang tertentu
```

**Konsep penting: string di JavaScript immutable.** Semua method string (`.toUpperCase()`, `.slice()`, dst) **mengembalikan string baru**, tidak pernah mengubah string aslinya:

```js
let x = "halo";
x.toUpperCase();
console.log(x); // masih "halo", bukan "HALO" -> harus ditampung: x = x.toUpperCase()
```

## 7. Percabangan

```js
if (nilai >= 90) {
  console.log("A");
} else if (nilai >= 75) {
  console.log("B");
} else {
  console.log("C");
}

// switch -> cocok untuk banyak pilihan nilai diskrit
switch (hari) {
  case "Senin":
  case "Selasa":
    console.log("Awal minggu");
    break;
  case "Jumat":
    console.log("Akhir minggu kerja");
    break;
  default:
    console.log("Hari lain");
}

// Ternary -> ekspresi, bukan statement, jadi bisa langsung dipakai sebagai nilai
const status = umur >= 18 ? "dewasa" : "anak";
```

**Jangan lupa `break` di dalam `switch`.** Tanpa `break`, eksekusi akan "jatuh" (fall-through) ke `case` berikutnya meskipun kondisinya tidak cocok — ini kadang disengaja (seperti contoh `Senin`/`Selasa` di atas yang menumpuk dua case), tapi sering jadi bug tak disengaja.

## 8. Perulangan

```js
// for klasik -> kontrol penuh atas index
for (let i = 0; i < 5; i++) {
  console.log(i);
}

// while -> kondisi dicek dulu sebelum badan loop dijalankan
let i = 0;
while (i < 5) {
  console.log(i);
  i++;
}

// do-while -> badan loop dijalankan minimal sekali, baru kondisi dicek
let j = 0;
do {
  console.log(j);
  j++;
} while (j < 5);

// for...of -> iterasi NILAI dari struktur iterable (array, string, Map, Set)
for (const item of ["a", "b", "c"]) {
  console.log(item); // "a", "b", "c"
}

// for...in -> iterasi KEY/index dari object atau array (hindari untuk array!)
for (const key in { a: 1, b: 2 }) {
  console.log(key); // "a", "b"
}
```

**`for...of` vs `for...in` adalah sumber kebingungan klasik.** `for...of` mengambil *nilai* dari iterable, `for...in` mengambil *key* (nama properti/index). Untuk array, selalu pakai `for...of` atau method array (`forEach`, `map`, dst) — `for...in` pada array bisa ikut mengiterasi properti tambahan yang ditempel ke array dan urutannya tidak dijamin.

`break` menghentikan loop sepenuhnya, `continue` melompati sisa iterasi saat ini dan lanjut ke iterasi berikutnya — sama seperti di kebanyakan bahasa lain.

## 9. Fungsi

JavaScript punya beberapa cara mendefinisikan fungsi, dan perbedaannya bukan cuma gaya penulisan:

```js
// 1. Function declaration -> di-hoist penuh, bisa dipanggil sebelum baris definisinya
function tambah(a, b) {
  return a + b;
}

// 2. Function expression -> disimpan ke variabel, TIDAK di-hoist (mengikuti aturan let/const)
const kurang = function (a, b) {
  return a - b;
};

// 3. Arrow function (ES6) -> sintaks ringkas, TIDAK punya `this` sendiri
const kali = (a, b) => a * b;              // implicit return jika satu ekspresi
const kuadrat = (a) => {                    // butuh {} + return jika banyak statement
  const hasil = a * a;
  return hasil;
};
```

Fitur parameter modern:

```js
function sapa(nama = "Tamu") {   // default parameter
  console.log(`Halo, ${nama}`);
}
sapa(); // "Halo, Tamu"

function jumlahkanSemua(...angka) {  // rest parameter -> kumpulkan sisa argumen jadi array
  return angka.reduce((total, n) => total + n, 0);
}
jumlahkanSemua(1, 2, 3, 4); // 10
```

**Kapan pakai arrow function vs function biasa?** Arrow function tidak punya `this` miliknya sendiri — ia "mewarisi" `this` dari scope tempat ia didefinisikan (*lexical this*). Ini sangat berguna di dalam method/callback supaya `this` tetap merujuk ke objek yang benar (lihat bagian [`this`](#12-this-keyword)), tapi berarti arrow function **tidak cocok** dipakai sebagai method object yang mengandalkan `this`, atau sebagai constructor.

```js
const obj = {
  nama: "Budi",
  sapaBiasa: function () {
    console.log(this.nama); // "Budi" -> this merujuk ke obj
  },
  sapaArrow: () => {
    console.log(this.nama); // undefined -> this di sini bukan obj, tapi scope luar (module/global)
  },
};
```

## 10. Scope & Hoisting

**Scope** menentukan di bagian kode mana sebuah variabel bisa diakses. JavaScript punya *global scope*, *function scope*, dan (sejak ES6) *block scope*.

**Hoisting** adalah perilaku JavaScript yang "mengangkat" deklarasi ke atas scope-nya sebelum kode dieksekusi — tapi caranya berbeda untuk tiap jenis deklarasi:

```js
console.log(a); // undefined (bukan error!) -> deklarasi var di-hoist, diinisialisasi undefined
var a = 5;

console.log(b); // ReferenceError: Cannot access 'b' before initialization
let b = 5;       // let/const di-hoist tapi masuk "Temporal Dead Zone" sampai baris deklarasinya

console.log(sapa()); // "Halo!" -> function declaration di-hoist BESERTA isinya
function sapa() {
  return "Halo!";
}

console.log(sapa2()); // TypeError: sapa2 is not a function -> nilai belum ter-assign saat hoisting
var sapa2 = function () {
  return "Halo!";
};
```

**Kenapa ini penting:** hoisting menjelaskan kenapa mendeklarasikan variabel/fungsi di bagian atas file (atau tidak bergantung pada urutan sama sekali dengan `const`/`let`) adalah praktik yang lebih aman — kamu tidak perlu menghafal aturan hoisting kalau kode sudah ditulis dengan urutan yang jelas.

## 11. Closure

Closure adalah salah satu konsep JavaScript yang paling sering ditanyakan di wawancara kerja, karena mendasari banyak pola penting (module pattern, currying, memoization, event handler dengan state).

**Definisi konseptual:** closure terjadi ketika sebuah fungsi "mengingat" variabel dari scope tempat ia dibuat, bahkan setelah scope luar tersebut selesai dieksekusi.

```js
function buatCounter() {
  let hitung = 0; // variabel ini "private" — hanya bisa diakses lewat fungsi yang di-return

  return function () {
    hitung++;
    return hitung;
  };
}

const counter1 = buatCounter();
console.log(counter1()); // 1
console.log(counter1()); // 2 -> `hitung` tetap "hidup" di antara pemanggilan

const counter2 = buatCounter();
console.log(counter2()); // 1 -> counter2 punya closure & `hitung` sendiri, terpisah dari counter1
```

**Kenapa ini bisa terjadi:** setiap kali `buatCounter()` dipanggil, JavaScript membuat *scope* baru untuk eksekusi itu. Fungsi yang di-return "menutup" (closure = "penutupan") akses ke variabel `hitung` di scope tersebut, sehingga scope itu tidak dibuang oleh garbage collector selama fungsi dalam masih bisa dipanggil.

Closure sering dipakai untuk membuat **variabel privat**, karena JavaScript (sebelum ES2022 punya `#field` privat di class) tidak punya keyword `private` bawaan:

```js
function buatAkunBank(saldoAwal) {
  let saldo = saldoAwal; // tidak bisa diakses langsung dari luar

  return {
    cekSaldo: () => saldo,
    setor: (jumlah) => (saldo += jumlah),
    tarik: (jumlah) => {
      if (jumlah > saldo) return "Saldo tidak cukup";
      saldo -= jumlah;
      return saldo;
    },
  };
}

const akun = buatAkunBank(100000);
akun.setor(50000);
console.log(akun.cekSaldo()); // 150000
console.log(akun.saldo);      // undefined -> tidak bisa diakses langsung dari luar closure
```

**Jebakan closure klasik di dalam loop:**

```js
// Dengan var -> semua callback berbagi SATU variabel i yang sama
for (var i = 1; i <= 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// Output: 4, 4, 4 -> saat setTimeout jalan, loop sudah selesai dan i sudah jadi 4

// Dengan let -> setiap iterasi punya binding i-nya sendiri (block scope)
for (let i = 1; i <= 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// Output: 1, 2, 3 -> sesuai ekspektasi
```

## 12. `this` Keyword

`this` adalah salah satu bagian JavaScript yang paling sering membingungkan, karena nilainya **ditentukan oleh bagaimana fungsi dipanggil**, bukan di mana fungsi itu didefinisikan (kecuali arrow function).

```js
const obj = {
  nama: "Budi",
  sapa() {
    console.log(this.nama); // "Budi" -> dipanggil sebagai obj.sapa(), this = obj
  },
};
obj.sapa();

const fnLepas = obj.sapa;
fnLepas(); // undefined (atau error di strict mode) -> dipanggil tanpa "pemilik", this hilang konteksnya

// Kontrol eksplisit dengan call / apply / bind
function sapaDenganNama() {
  console.log(this.nama);
}
sapaDenganNama.call({ nama: "Ani" });   // "Ani" -> jalankan sekarang, this diset manual
sapaDenganNama.apply({ nama: "Cici" }); // sama seperti call, bedanya cara passing argumen array
const sapaAni = sapaDenganNama.bind({ nama: "Deni" }); // buat fungsi baru dengan this terkunci
sapaAni(); // "Deni"
```

Aturan ringkas nilai `this`:

| Cara pemanggilan | Nilai `this` |
|---|---|
| `obj.method()` | `obj` (objek di kiri titik) |
| `fungsiLepas()` | `undefined` (strict mode) atau `globalThis` (non-strict) |
| Arrow function | `this` dari scope leksikal (tempat arrow function ditulis) |
| `new Fungsi()` | objek baru yang sedang dibuat |
| `fn.call(obj)` / `fn.apply(obj)` | `obj`, dipaksa secara eksplisit |
| Event handler DOM (`onclick`) | elemen yang menerima event (kecuali arrow function) |

## 13. Array & Method Array

Array di JavaScript adalah object khusus yang elemennya diindeks dengan angka, dan bisa menampung tipe campuran.

```js
const buah = ["apel", "jeruk", "mangga"];

buah.push("pisang");     // tambah di akhir, ubah array asli (mutating)
buah.pop();               // hapus dari akhir, ubah array asli
buah.unshift("nanas");    // tambah di awal
buah.shift();             // hapus dari awal
buah.length;               // 3
buah[0];                   // "apel"
buah.indexOf("jeruk");     // 1
buah.includes("mangga");   // true
```

**Method array modern (ES5/ES6) yang paling penting** — semua ini **tidak mengubah array asli**, tapi mengembalikan array/nilai baru:

```js
const angka = [1, 2, 3, 4, 5];

angka.map((n) => n * 2);            // [2, 4, 6, 8, 10] -> transformasi tiap elemen
angka.filter((n) => n % 2 === 0);    // [2, 4] -> ambil elemen yang lolos kondisi
angka.reduce((total, n) => total + n, 0); // 15 -> "lipat" array jadi satu nilai
angka.find((n) => n > 3);            // 4 -> elemen pertama yang cocok
angka.findIndex((n) => n > 3);       // 3 -> index elemen pertama yang cocok
angka.some((n) => n > 4);            // true -> ada minimal satu yang cocok?
angka.every((n) => n > 0);           // true -> semua elemen cocok?
angka.sort((a, b) => b - a);         // [5,4,3,2,1] -> MUTATING! selalu kasih comparator untuk angka
angka.forEach((n) => console.log(n)); // jalankan fungsi per elemen, tidak return apa-apa
```

**Kenapa `map`/`filter`/`reduce` lebih disukai daripada `for` loop manual:** kode jadi lebih deklaratif (menyatakan "apa" yang diinginkan, bukan "bagaimana" langkah-langkahnya), lebih mudah dirangkai (chaining), dan tidak punya efek samping mengubah array asli — lebih aman untuk debugging.

```js
// Contoh chaining: ambil nama mahasiswa lulus (nilai >= 60), urutkan, jadi 1 string
const mahasiswa = [
  { nama: "Ani", nilai: 85 },
  { nama: "Budi", nilai: 55 },
  { nama: "Cici", nilai: 90 },
];

const hasil = mahasiswa
  .filter((m) => m.nilai >= 60)
  .sort((a, b) => b.nilai - a.nilai)
  .map((m) => m.nama)
  .join(", ");

console.log(hasil); // "Cici, Ani"
```

**Jebakan `sort()` tanpa comparator:** secara default `sort()` mengurutkan berdasarkan **representasi string**, bukan angka:

```js
[10, 1, 2].sort();               // [1, 10, 2] -> salah! "10" < "2" secara string
[10, 1, 2].sort((a, b) => a - b); // [1, 2, 10] -> benar, comparator eksplisit angka
```

## 14. Object

```js
const orang = {
  nama: "Budi",
  umur: 25,
  alamat: { kota: "Jakarta" },
  sapa() {
    return `Halo, saya ${this.nama}`;
  },
};

orang.nama;          // dot notation -> saat nama properti sudah pasti/valid identifier
orang["nama"];        // bracket notation -> wajib kalau nama properti dinamis atau ada spasi
orang.alamat.kota;    // "Jakarta" -> akses nested object

// Menambah / mengubah properti secara dinamis
orang.pekerjaan = "Developer";
delete orang.umur;
```

**Object methods yang sering dipakai** untuk memeriksa atau mengubah bentuk object:

```js
Object.keys(orang);     // ["nama", "alamat", "sapa", "pekerjaan"] -> array semua key
Object.values(orang);    // array semua value
Object.entries(orang);   // [["nama","Budi"], ["alamat",{...}], ...] -> pasangan [key, value]
Object.assign({}, orang, { umur: 30 }); // gabungkan beberapa object jadi satu (shallow copy)
```

**Destructuring (ES6)** — cara ringkas mengekstrak nilai dari object/array ke variabel:

```js
const { nama, umur = 0 } = orang;  // umur=0 sebagai default kalau properti tidak ada
console.log(nama); // "Budi"

const [a, b, ...sisanya] = [1, 2, 3, 4, 5];
console.log(a, b, sisanya); // 1 2 [3, 4, 5]

// Sering dipakai untuk parameter fungsi
function tampilkanProfil({ nama, umur }) {
  console.log(`${nama}, ${umur} tahun`);
}
```

**Spread operator (`...`)** membongkar isi array/object — sering dipakai untuk *copy* dan *merge* tanpa mengubah data asli (immutable update, penting di React):

```js
const angka1 = [1, 2, 3];
const angka2 = [...angka1, 4, 5]; // [1,2,3,4,5] -> shallow copy + tambahan

const objA = { a: 1, b: 2 };
const objB = { ...objA, b: 99 };  // { a: 1, b: 99 } -> properti belakang menang jika key sama
```

**Konsep penting: shallow copy vs deep copy.** `{ ...obj }` dan `Object.assign` hanya menyalin **satu level**. Kalau ada nested object/array di dalamnya, referensinya tetap dibagi (shared):

```js
const original = { alamat: { kota: "Jakarta" } };
const copy = { ...original };
copy.alamat.kota = "Bandung";
console.log(original.alamat.kota); // "Bandung" juga ikut berubah! -> nested object masih "satu" referensi
```

## 15. Equality & Perbandingan Referensi

Object dan array dibandingkan **berdasarkan referensi**, bukan isi:

```js
const a = { x: 1 };
const b = { x: 1 };
console.log(a === b); // false -> dua object berbeda di memori, walau isinya identik
console.log(a === a); // true -> merujuk objek yang sama persis

const arr1 = [1, 2, 3];
const arr2 = [1, 2, 3];
console.log(arr1 === arr2); // false
```

Untuk membandingkan **isi** dua object/array, perlu perbandingan manual (loop/`JSON.stringify` untuk kasus sederhana) atau library (`lodash.isEqual`). Ini beda dengan primitif (`number`, `string`, `boolean`) yang dibandingkan berdasarkan **nilai**.

## 16. Error Handling

```js
function bagi(a, b) {
  if (b === 0) {
    throw new Error("Tidak bisa membagi dengan nol");
  }
  return a / b;
}

try {
  const hasil = bagi(10, 0);
  console.log(hasil);
} catch (error) {
  console.error("Terjadi error:", error.message);
} finally {
  console.log("Selalu dijalankan, apa pun hasilnya");
}
```

Poin konseptual:

- `throw` bisa melempar nilai apa pun, tapi konvensinya selalu `throw new Error("pesan")` supaya penerima dapat `.message` dan `.stack` (jejak pemanggilan) yang berguna untuk debugging.
- `finally` selalu dijalankan — baik `try` berhasil, gagal (`catch`), maupun ada `return` di dalamnya. Cocok untuk cleanup (menutup koneksi, mematikan loading spinner).
- Error yang tidak ditangkap (`uncaught`) di browser akan muncul di console dan menghentikan eksekusi script saat itu; di Node.js bisa menyebabkan proses berhenti sepenuhnya.
- Untuk kode asynchronous (Promise), error harus ditangkap dengan `.catch()` atau `try/catch` di dalam `async function` — `try/catch` biasa **tidak menangkap** error dari callback asynchronous seperti `setTimeout`.

## 17. Asynchronous JavaScript: Callback

JavaScript berjalan di satu thread, tapi operasi yang "lama" (timer, request network, baca file) tidak boleh memblokir thread itu. Solusinya: operasi tersebut didelegasikan ke luar (Web API di browser / libuv di Node), dan hasilnya dikembalikan lewat **callback** — fungsi yang dipanggil nanti setelah pekerjaan selesai.

```js
console.log("1. Mulai");

setTimeout(() => {
  console.log("2. Selesai setelah 2 detik");
}, 2000);

console.log("3. Lanjut duluan, tidak menunggu setTimeout");

// Output urutan: 1, 3, lalu 2 (dua detik kemudian)
```

**Masalah callback: "Callback Hell".** Kalau beberapa operasi asynchronous harus berurutan (hasil A dipakai untuk B, hasil B dipakai untuk C), callback bertumpuk jadi sulit dibaca:

```js
ambilUser(1, (user) => {
  ambilPostingan(user.id, (postingan) => {
    ambilKomentar(postingan[0].id, (komentar) => {
      console.log(komentar); // makin dalam, makin sulit dibaca & di-debug ("pyramid of doom")
    });
  });
});
```

Masalah inilah yang mendorong lahirnya **Promise**.

## 18. Promise

Promise adalah object yang merepresentasikan **hasil dari operasi asynchronous yang belum tentu selesai** — bisa nanti berhasil (`fulfilled`) atau gagal (`rejected`). Promise punya 3 state: `pending`, `fulfilled`, `rejected`, dan sekali berubah dari `pending` statenya tidak bisa berubah lagi.

```js
function ambilData() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const sukses = true;
      if (sukses) {
        resolve({ data: "Ini data" }); // operasi berhasil
      } else {
        reject(new Error("Gagal ambil data")); // operasi gagal
      }
    }, 1000);
  });
}

ambilData()
  .then((hasil) => console.log(hasil))
  .catch((error) => console.error(error.message))
  .finally(() => console.log("Selesai, apa pun hasilnya"));
```

**Chaining Promise** menyelesaikan masalah "pyramid of doom" karena setiap `.then()` mengembalikan Promise baru, sehingga bisa disambung datar, bukan bersarang:

```js
ambilUser(1)
  .then((user) => ambilPostingan(user.id))
  .then((postingan) => ambilKomentar(postingan[0].id))
  .then((komentar) => console.log(komentar))
  .catch((error) => console.error("Error di mana pun dalam rantai:", error.message));
```

**Kombinator Promise** untuk menjalankan beberapa Promise sekaligus:

```js
Promise.all([ambilUser(1), ambilUser(2)]);
// Tunggu SEMUA selesai. Kalau satu saja reject, keseluruhan Promise.all langsung reject.

Promise.allSettled([ambilUser(1), ambilUser(2)]);
// Tunggu semua selesai, apa pun hasilnya (fulfilled/rejected) -> hasilnya array status masing-masing

Promise.race([ambilUser(1), ambilUser(2)]);
// Selesai secepat salah satu dari mereka selesai (menang/kalah), abaikan sisanya
```

## 19. Async/Await

`async`/`await` (ES2017) adalah **sintaks gula (syntactic sugar)** di atas Promise — membuat kode asynchronous terbaca seperti kode synchronous, tanpa mengubah cara kerja event loop di baliknya.

```js
async function ambilSemuaData() {
  try {
    const user = await ambilUser(1);         // "jeda" di sini sampai Promise selesai...
    const postingan = await ambilPostingan(user.id); // ...tapi thread utama TIDAK diblokir
    console.log(postingan);
  } catch (error) {
    console.error("Error:", error.message); // try/catch biasa BISA menangkap error di sini
  }
}
```

Poin konseptual penting:

- Fungsi yang diberi keyword `async` **selalu mengembalikan Promise**, meskipun isinya `return` nilai biasa.
- `await` hanya boleh dipakai di dalam fungsi `async` (kecuali *top-level await* di ES module modern).
- `await` tidak memblokir thread — ia menjeda eksekusi fungsi itu saja dan mengembalikan kontrol ke event loop, sehingga kode lain tetap bisa berjalan selama menunggu.
- Untuk beberapa operasi paralel, jangan `await` satu-satu berurutan (lambat) — gunakan `Promise.all`:

```js
// Lambat -> berurutan, total waktu = jumlah semua durasi
const a = await ambilA(); // tunggu 1 detik
const b = await ambilB(); // baru mulai setelah a selesai, tunggu 1 detik lagi

// Cepat -> paralel, total waktu = durasi terlama saja
const [a2, b2] = await Promise.all([ambilA(), ambilB()]);
```

## 20. Event Loop (Konsep di Balik Semua Ini)

Ini adalah konsep yang menyatukan semua bagian asynchronous di atas. Ada 3 komponen utama:

1. **Call Stack** — tempat fungsi yang sedang berjalan ditumpuk. JavaScript hanya bisa menjalankan satu hal pada satu waktu di sini.
2. **Web API / Node API** — tempat operasi asynchronous (timer, network, file) "dititipkan" untuk diproses di luar call stack.
3. **Callback Queue (macrotask) & Microtask Queue** — antrean tempat callback menunggu giliran kembali ke call stack setelah pekerjaannya selesai.

**Aturan emas event loop:** call stack harus **benar-benar kosong** sebelum event loop mengambil task berikutnya dari antrean. Dan microtask queue (Promise `.then`/`.catch`, `queueMicrotask`) **selalu diproses lebih dulu, sampai habis**, sebelum event loop mengambil satu task dari macrotask queue (`setTimeout`, event DOM).

```js
console.log("1");

setTimeout(() => console.log("2 (macrotask)"), 0);

Promise.resolve().then(() => console.log("3 (microtask)"));

console.log("4");

// Output: 1, 4, 3, 2
// Meskipun setTimeout diberi delay 0, ia tetap masuk antrean macrotask
// dan harus menunggu call stack kosong DAN microtask queue habis dulu.
```

Memahami ini menjelaskan kenapa `setTimeout(fn, 0)` tidak berarti "jalankan sekarang juga" — artinya "jalankan sesegera mungkin setelah call stack kosong dan semua microtask selesai".

## 21. DOM Manipulation

DOM (Document Object Model) adalah representasi HTML sebagai pohon object yang bisa dimanipulasi lewat JavaScript. Ini hanya berlaku di browser, bukan Node.js.

```js
// Mencari elemen
document.getElementById("judul");
document.querySelector(".card");        // selector pertama yang cocok (sintaks CSS)
document.querySelectorAll(".item");     // NodeList semua yang cocok

// Mengubah konten & atribut
const el = document.querySelector("#judul");
el.textContent = "Judul Baru";  // aman dari XSS, treat sebagai teks murni
el.innerHTML = "<b>Tebal</b>";   // parse sebagai HTML -> hati-hati, rawan XSS jika dari input user
el.setAttribute("data-id", "42");
el.classList.add("active");
el.classList.toggle("hidden");

// Membuat & menyisipkan elemen baru
const li = document.createElement("li");
li.textContent = "Item baru";
document.querySelector("ul").appendChild(li);
```

**Kenapa `textContent` lebih aman daripada `innerHTML`:** kalau isinya berasal dari input user dan kamu pakai `innerHTML`, string HTML/script berbahaya bisa ikut ter-render dan dieksekusi (Cross-Site Scripting / XSS). Gunakan `textContent` untuk teks polos, dan `innerHTML` hanya untuk konten yang kamu percaya sumbernya (atau sudah di-sanitasi).

## 22. Event Handling

```js
const tombol = document.querySelector("#simpan");

tombol.addEventListener("click", function (event) {
  console.log("Tombol diklik!");
  console.log(event.target); // elemen yang memicu event
});

// Mencegah perilaku default (misal submit form yang reload halaman)
document.querySelector("form").addEventListener("submit", (event) => {
  event.preventDefault();
  console.log("Form dikirim tanpa reload halaman");
});
```

**Event bubbling:** ketika sebuah elemen menerima event, event itu "menggelembung" (bubble up) ke elemen induknya satu per satu sampai ke `document`. Ini memungkinkan teknik **event delegation** — memasang satu listener di parent untuk menangani event dari banyak child, alih-alih memasang listener di tiap child satu-satu (lebih efisien, terutama untuk elemen yang dibuat secara dinamis):

```js
document.querySelector("ul").addEventListener("click", (event) => {
  if (event.target.tagName === "LI") {
    console.log("Item diklik:", event.target.textContent);
  }
});
```

## 23. JSON

JSON (JavaScript Object Notation) adalah format teks untuk pertukaran data — dipakai hampir di semua komunikasi API. Meskipun namanya mengandung "JavaScript", JSON adalah format universal yang didukung hampir semua bahasa pemrograman.

```js
const obj = { nama: "Budi", umur: 25, hobi: ["baca", "kode"] };

const jsonString = JSON.stringify(obj);
// '{"nama":"Budi","umur":25,"hobi":["baca","kode"]}' -> object JS jadi string JSON

const objKembali = JSON.parse(jsonString);
// { nama: "Budi", umur: 25, hobi: ["baca", "kode"] } -> string JSON jadi object JS
```

**Batasan JSON:** hanya mendukung tipe data dasar (string, number, boolean, null, array, object polos). Fungsi, `undefined`, `Symbol`, dan referensi siklik (object yang menunjuk balik ke dirinya sendiri) **tidak bisa** direpresentasikan di JSON dan akan hilang/error saat `JSON.stringify`.

## 24. Fetch API

`fetch` adalah API browser modern untuk melakukan HTTP request (menggantikan `XMLHttpRequest` yang lebih verbose). `fetch` mengembalikan Promise.

```js
async function ambilData() {
  try {
    const response = await fetch("https://api.example.com/users");
    if (!response.ok) {
      throw new Error(`HTTP error: ${response.status}`);
    }
    const data = await response.json(); // parsing body JSON juga asynchronous!
    console.log(data);
  } catch (error) {
    console.error("Gagal fetch:", error.message);
  }
}
```

**Jebakan penting: `fetch` tidak reject saat status HTTP error (4xx/5xx)** — hanya reject kalau ada masalah jaringan (koneksi putus, DNS gagal). Karena itu **wajib cek `response.ok` secara manual** sebelum memakai data, seperti contoh di atas.

```js
// Mengirim data (POST) dengan body JSON
fetch("https://api.example.com/users", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ nama: "Budi" }),
});
```

## 25. Class & OOP di JavaScript

JavaScript sebenarnya tidak punya "class" dalam arti klasik — di baliknya class adalah **syntactic sugar** di atas *prototype*, mekanisme pewarisan asli JavaScript. Tapi sintaks `class` (ES6) jauh lebih mudah dibaca daripada manipulasi prototype manual.

```js
class Hewan {
  constructor(nama) {
    this.nama = nama;
  }

  bersuara() {
    console.log(`${this.nama} bersuara`);
  }
}

class Anjing extends Hewan {
  constructor(nama, ras) {
    super(nama); // wajib panggil super() dulu sebelum pakai `this` di constructor turunan
    this.ras = ras;
  }

  bersuara() { // override method dari parent
    console.log(`${this.nama} menggonggong`);
  }
}

const kucing = new Hewan("Kitty");
kucing.bersuara(); // "Kitty bersuara"

const bobi = new Anjing("Bobi", "Pudel");
bobi.bersuara(); // "Bobi menggonggong" -> method di child menang atas parent
```

**Di balik layar, `class` tetap prototype-based:** setiap object punya properti tersembunyi (`[[Prototype]]`, bisa diakses lewat `Object.getPrototypeOf` atau properti `__proto__`) yang menunjuk ke object lain. Saat kamu mengakses `bobi.bersuara()`, JavaScript mencari `bersuara` di `bobi` dulu, tidak ketemu, lalu naik ke prototype `Anjing`, ketemu di sana — inilah yang disebut **prototype chain**.

**Private field** (ES2022) memakai prefiks `#`, benar-benar tidak bisa diakses dari luar class (berbeda dengan konvensi lama `_nama` yang hanya "sinyal jangan diakses" tapi tetap bisa diakses):

```js
class Akun {
  #saldo = 0; // benar-benar private, error jika diakses dari luar class

  setor(jumlah) {
    this.#saldo += jumlah;
  }

  get saldo() { // getter -> diakses seperti properti, bukan method (tanpa tanda kurung)
    return this.#saldo;
  }
}

const akun = new Akun();
akun.setor(1000);
console.log(akun.saldo);  // 1000
console.log(akun.#saldo); // SyntaxError: Private field '#saldo' must be declared in an enclosing class
```

## 26. Modules (`import`/`export`)

Modules memungkinkan kode dipecah ke banyak file dengan scope terisolasi (variabel di satu file tidak otomatis bocor ke file lain), lalu saling menghubungkan lewat `import`/`export` secara eksplisit.

```js
// file: math.js
export function tambah(a, b) {
  return a + b;
}
export const PI = 3.14159;
export default function kali(a, b) { // hanya boleh 1 default export per file
  return a * b;
}
```

```js
// file: main.js
import kali, { tambah, PI } from "./math.js";

console.log(tambah(2, 3)); // 5
console.log(kali(2, 3));   // 6
```

Untuk memakai ES Module di browser, tag script perlu `type="module"`:

```html
<script type="module" src="main.js"></script>
```

**ESM (`import`/`export`) vs CommonJS (`require`/`module.exports`):** CommonJS adalah sistem module lama bawaan Node.js, bersifat *synchronous* dan memuat module saat runtime. ESM adalah standar resmi JavaScript modern, mendukung *static analysis* (bundler bisa tahu dependency tanpa menjalankan kode) dan `tree-shaking` (membuang kode yang tidak dipakai saat build). Node.js modern mendukung keduanya, dibedakan lewat ekstensi file (`.mjs`/`.cjs`) atau field `"type"` di `package.json`.

## 27. Kesalahan Umum & Kuirk JavaScript

Ringkasan jebakan yang paling sering menjebak pemula — anggap ini daftar "waspada" saat menulis atau membaca kode JS:

- **`NaN !== NaN`.** Untuk mengecek apakah suatu nilai adalah `NaN`, jangan pakai `x === NaN` (selalu `false`) — pakai `Number.isNaN(x)`.
- **Perbandingan floating point.** `0.1 + 0.2 !== 0.3` karena presisi biner. Untuk perbandingan desimal, bandingkan dengan toleransi: `Math.abs(a - b) < Number.EPSILON`.
- **Mutasi tak sengaja lewat referensi.** Array/object di-passing sebagai referensi ke fungsi — mengubah isinya di dalam fungsi ikut mengubah aslinya di luar, kecuali kamu sengaja copy dulu (`[...arr]`, `{...obj}`).
- **`this` yang hilang saat method dilepas dari objeknya**, misalnya `setTimeout(obj.method, 1000)` — `this` di dalam `method` tidak lagi merujuk ke `obj`. Solusi: `setTimeout(() => obj.method(), 1000)` atau `.bind(obj)`.
- **Lupa `return` di arrow function dengan `{}`.** `const f = (x) => { x * 2 }` mengembalikan `undefined` karena memakai `{}` berarti butuh `return` eksplisit — beda dengan `(x) => x * 2` yang implicit return.
- **Membandingkan array/object dengan `==`/`===`** akan selalu `false` kecuali membandingkan referensi yang sama persis — perbandingan isi butuh logika manual.
- **Automatic Semicolon Insertion (ASI)** bisa menyisipkan `;` di tempat tak terduga. Contoh klasik:
  ```js
  function buatObjek() {
    return
    { nilai: 1 }; // ASI menyisipkan ; setelah `return`, jadi fungsi mengembalikan undefined!
  }
  ```
  Solusi: selalu taruh `{` di baris yang sama dengan `return`.
- **Modifikasi array saat iterasi** (misalnya `splice` di dalam `forEach`) bisa melompati elemen karena index bergeser saat item dihapus. Lebih aman memakai `filter` untuk menghasilkan array baru daripada memodifikasi array asli saat iterasi berjalan.
