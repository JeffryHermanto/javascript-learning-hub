# Cheatsheet JavaScript

Referensi cepat sintaks dan fungsi bawaan JavaScript. Untuk penjelasan konsep, lihat [Tutorial JavaScript](tutorial.html).

## Menjalankan

```bash
node file.js              # jalankan lewat Node.js
node --watch file.js       # auto-restart saat file berubah (Node 18+)

python3 -m http.server 8000  # server lokal untuk file HTML/JS di browser
```

```html
<script src="app.js" defer></script>       <!-- script biasa, non-blocking -->
<script type="module" src="app.js"></script> <!-- ES module -->
```

## Deklarasi Variabel

| | `var` | `let` | `const` |
|---|---|---|---|
| Scope | function | block | block |
| Reassign | ya | ya | tidak |
| Rekomendasi | hindari | jika nilai berubah | default, pakai ini |

## Tipe Data

| Tipe | Contoh | `typeof` |
|---|---|---|
| number | `42`, `3.14`, `NaN` | `"number"` |
| string | `"teks"`, `` `template` `` | `"string"` |
| boolean | `true`, `false` | `"boolean"` |
| undefined | `let x;` | `"undefined"` |
| null | `null` | `"object"` (kuirk) |
| bigint | `123n` | `"bigint"` |
| symbol | `Symbol()` | `"symbol"` |
| object/array/function | `{}`, `[]`, `()=>{}` | `"object"` / `"function"` |

Nilai **falsy** (hanya 8): `false` `0` `-0` `0n` `""` `null` `undefined` `NaN`. Sisanya truthy.

## Operator

```js
+ - * / % **            // aritmatika (** = pangkat)
== != > < >= <=          // perbandingan (== loose, coercion)
=== !==                  // perbandingan strict (disarankan)
&& || !                  // logika
?? a : b                 // ternary: kondisi ? a : b
??                       // nullish coalescing (fallback jika null/undefined)
?.                       // optional chaining: obj?.prop?.method?.()
= += -= *= /= %= **=      // assignment
++ --                    // increment/decrement
...                      // spread / rest
typeof x                 // cek tipe
x instanceof Kelas        // cek instance
```

## Struktur Kontrol

```js
if (kondisi) { } else if (kondisi2) { } else { }

switch (x) {
  case 1: /* ... */ break;
  case 2: /* ... */ break;
  default: /* ... */
}

for (let i = 0; i < n; i++) { }
for (const item of iterable) { }   // nilai (array, string, Map, Set)
for (const key in objek) { }        // key (object) — hindari untuk array
while (kondisi) { }
do { } while (kondisi);

const hasil = kondisi ? nilaiA : nilaiB; // ternary
break;     // hentikan loop/switch
continue;  // lompat ke iterasi berikutnya
```

## Fungsi

```js
function nama(a, b) { return a + b; }       // declaration -> hoisted penuh
const nama2 = function (a, b) { return a + b; }; // expression -> tidak hoisted
const nama3 = (a, b) => a + b;               // arrow -> tanpa this sendiri, implicit return
const nama4 = (a, b) => { return a + b; };   // arrow dengan block body -> butuh return eksplisit

function f(a, b = 10) { }        // default parameter
function f(...rest) { }           // rest parameter -> kumpulkan jadi array
f.call(thisObj, arg1, arg2);       // panggil dengan this custom
f.apply(thisObj, [arg1, arg2]);    // sama seperti call, argumen berupa array
const bound = f.bind(thisObj);     // buat fungsi baru dengan this terkunci
```

## String

```js
const s = "Halo Dunia";
s.length                 s.trim()               s.toUpperCase()
s.toLowerCase()           s.includes("Dunia")     s.startsWith("Halo")
s.endsWith("Dunia")       s.indexOf("D")          s.slice(0, 4)
s.split(" ")              s.replace("Halo","Hi")  s.replaceAll("a","4")
s.repeat(2)               s.padStart(15, "*")     s.padEnd(15, "*")
s.charAt(0)               s.at(-1)                 // index negatif dari belakang
`Halo ${nama}, umur ${umur + 1}`                    // template literal
```

## Array

```js
const a = [1, 2, 3];

// Mutating (mengubah array asli)
a.push(4); a.pop(); a.unshift(0); a.shift();
a.splice(1, 1);          // hapus 1 elemen mulai index 1
a.sort((x, y) => x - y); // WAJIB comparator untuk angka
a.reverse();

// Non-mutating (kembalikan array/nilai baru)
a.map((n) => n * 2)
a.filter((n) => n > 1)
a.reduce((total, n) => total + n, 0)
a.find((n) => n > 1)
a.findIndex((n) => n > 1)
a.some((n) => n > 2)
a.every((n) => n > 0)
a.includes(2)
a.indexOf(2)
a.slice(0, 2)             // potong tanpa mengubah asli
a.join(", ")               // gabung jadi string
a.concat([4, 5])           // gabung array
a.flat()                    // ratakan nested array 1 level
Array.isArray(a)            // cek apakah array
Array.from({ length: 5 }, (_, i) => i) // [0,1,2,3,4]
[...a]                       // shallow copy
```

## Object

```js
const o = { a: 1, b: 2 };

Object.keys(o)      // ["a","b"]
Object.values(o)     // [1,2]
Object.entries(o)    // [["a",1],["b",2]]
Object.assign({}, o, { c: 3 }) // merge, shallow copy
{ ...o, c: 3 }        // spread merge (lebih umum dipakai)
Object.freeze(o)      // cegah perubahan properti (shallow)
"a" in o              // cek key ada
delete o.a            // hapus properti

const { a, b = 0, ...rest } = o;   // destructuring + default + rest
const [x, y] = [1, 2];              // destructuring array
```

## Error Handling

```js
try {
  throw new Error("pesan error");
} catch (err) {
  console.error(err.message);
} finally {
  // selalu dijalankan
}

// Custom error
class ValidationError extends Error {
  constructor(msg) { super(msg); this.name = "ValidationError"; }
}
```

## Asynchronous

```js
// Promise
new Promise((resolve, reject) => { /* ... */ });
promise.then(onSuccess).catch(onError).finally(onDone);
Promise.all([p1, p2]);        // tunggu semua, reject jika ada 1 gagal
Promise.allSettled([p1, p2]); // tunggu semua, tidak pernah reject
Promise.race([p1, p2]);       // selesai duluan menang

// Async/Await
async function f() {
  try {
    const hasil = await promise;
  } catch (err) { /* tangani error */ }
}

// Timer
setTimeout(() => {}, 1000);   // jalankan sekali setelah delay
setInterval(() => {}, 1000);  // jalankan berulang tiap interval
clearTimeout(id); clearInterval(id);
```

## Fetch API

```js
const res = await fetch(url);
if (!res.ok) throw new Error(`HTTP ${res.status}`); // fetch TIDAK reject pada 4xx/5xx!
const data = await res.json();

fetch(url, {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify(payload),
});
```

## JSON

```js
JSON.stringify(obj)          // object -> string
JSON.stringify(obj, null, 2) // dengan indentasi rapi (pretty print)
JSON.parse(jsonString)       // string -> object
```

## Class

```js
class Hewan {
  #privat = 0;                 // private field (ES2022)
  static jumlah = 0;            // static property (milik class, bukan instance)

  constructor(nama) {
    this.nama = nama;
    Hewan.jumlah++;
  }

  bersuara() { return `${this.nama} bersuara`; }
  get info() { return `Nama: ${this.nama}`; }     // getter
  set namaBaru(v) { this.nama = v; }               // setter
  static buatDefault() { return new Hewan("Tanpa nama"); } // static method
}

class Anjing extends Hewan {
  constructor(nama) { super(nama); }
  bersuara() { return `${this.nama} menggonggong`; } // override
}
```

## DOM (khusus browser)

```js
document.getElementById("id")
document.querySelector(".class")
document.querySelectorAll("div")

el.textContent = "teks aman"   el.innerHTML = "<b>html</b>"
el.setAttribute("data-x", "1")  el.getAttribute("data-x")
el.classList.add("x")           el.classList.remove("x")
el.classList.toggle("x")         el.classList.contains("x")
el.style.color = "red"

document.createElement("div")
parent.appendChild(child)
parent.removeChild(child)
el.remove()

el.addEventListener("click", (e) => { });
e.preventDefault()    e.stopPropagation()    e.target
```

## Storage (browser)

```js
localStorage.setItem("key", JSON.stringify(value));  // persisten, tidak ada expiry
const value = JSON.parse(localStorage.getItem("key"));
localStorage.removeItem("key");
localStorage.clear();

sessionStorage.setItem(...);  // hilang saat tab ditutup, API sama seperti localStorage
```

## Console

```js
console.log(...)     console.error(...)   console.warn(...)
console.table(arr)    // tampilkan array/object sebagai tabel
console.group("x"); console.log("y"); console.groupEnd();
console.time("x"); /* ... */ console.timeEnd("x"); // ukur durasi eksekusi
```

## Number & Math

```js
Number("42")           parseInt("42px")       parseFloat("3.14m")
Number.isInteger(4)     Number.isNaN(NaN)       Number.parseFloat(...)
(3.14159).toFixed(2)    // "3.14" (string!)

Math.round(4.5)   Math.floor(4.9)   Math.ceil(4.1)   Math.abs(-5)
Math.max(1,2,3)   Math.min(1,2,3)  Math.pow(2,10)   Math.sqrt(16)
Math.random()      // [0, 1) -> Math.floor(Math.random() * 100) + 1 untuk 1-100
```

## Kesalahan Umum

| Masalah | Penyebab | Solusi |
|---|---|---|
| `NaN === NaN` selalu `false` | `NaN` tidak sama dengan apa pun | pakai `Number.isNaN(x)` |
| `0.1 + 0.2 !== 0.3` | presisi floating point biner | bandingkan dengan toleransi (`Math.abs`) |
| `this` hilang saat method dilepas | `this` ditentukan cara pemanggilan | `.bind()` atau arrow function |
| Array/object berubah tak sengaja | dioper sebagai referensi | `[...arr]` / `{...obj}` untuk copy |
| `for...in` di array urutan aneh | meng-iterasi semua enumerable key | pakai `for...of` atau `forEach` |
| `var` "bocor" dari blok `if`/`for` | `var` function-scoped, bukan block-scoped | pakai `let`/`const` |
| `fetch` tidak `catch` error 404/500 | `fetch` cuma reject saat error jaringan | cek `response.ok` manual |
| `sort()` angka hasil aneh (`[10,1,2]`) | default sort membandingkan string | `sort((a,b) => a-b)` |
| Lupa `await`, dapat `Promise {pending}` | fungsi async selalu return Promise | pakai `await` atau `.then()` |
