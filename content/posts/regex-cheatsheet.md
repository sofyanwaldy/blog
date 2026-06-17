---
title: "Cheatsheet Lengkap Regex"
description: "Cheatsheet praktis regular expressions (regex) untuk coding harian — sintaks, quantifier, group, lookahead, pola umum, dan contoh di JavaScript, Python, dan Vim."
date: 2026-06-17
tags: ["regex", "regular-expressions", "cheatsheet", "developer-tools"]
url: /regex
draft: false
---

Regular Expressions (regex) itu alat pencocokan pola (pattern-matching) yang ada di hampir semua bahasa pemrograman, text editor, dan command-line tool. Regex membantumu mencari, memvalidasi, mengekstrak, dan mengubah teks dengan bahasa pola yang ringkas.

Cheatsheet ini didesain sebagai referensi harian kamu — tidak cuma berisi daftar sintaks, tapi juga contoh-contoh praktis yang bakal benar-benar kamu pakai saat memparsing log, memvalidasi input, merefaktorkan kode, atau mencari teks di dalam codebase.

---

## Apa itu Regex?

Regular Expression (regex) adalah urutan karakter yang membentuk suatu pola pencarian. Kamu memberikan pola tersebut ke engine regex, dan dia bakal mencarikan teks yang cocok dengan pola itu.

```text
Pola (Pattern):  \d{3}-\d{4}
Teks Cocok:      "555-1234" di dalam kalimat "Hubungi 555-1234 untuk info"
```

Regex dipakai di mana-mana:

- **Validasi** — cek apakah format email atau nomor telepon sudah benar
- **Pencarian (Search)** — temukan semua URL di dalam sebuah dokumen
- **Ekstraksi (Extract)** — ambil data tanggal dari baris log
- **Penggantian (Replace)** — ubah nama variabel di seluruh codebase
- **Pemisahan (Split)** — pecah baris data CSV menjadi kolom-kolom

Sintaks regex sebagian besar standar di berbagai bahasa pemrograman (kompatibel dengan PCRE), tapi Vim punya dialeknya sendiri. Cheatsheet ini membahas keduanya.

---

## Pencocokan Dasar (Basic Matching)

Di tingkat paling sederhana, regex itu cuma berupa string biasa. Pola `error` bakal mencocokkan potongan kata "error" di mana pun kata itu muncul.

```text
Pola (Pattern):  error
Teks:            "Terjadi error pada server"
Hasil Cocok:     "error" (posisi karakter ke 8-12)
```

### Sensitivitas Huruf (Case Sensitivity)

Secara default, pencarian regex itu **sensitif huruf kapital** (case-sensitive):

```text
Pola (Pattern):  Error
Teks:            "error" → tidak cocok
Teks:            "Error" → cocok
Teks:            "ERROR" → tidak cocok
```

Untuk mencari tanpa memedulikan huruf besar/kecil, gunakan flag `i` (dibahas di bagian [Flag Umum](#flag-umum)):

```text
Pola (Pattern):  /error/i
Teks Cocok:      "error", "Error", "ERROR", "eRrOr"
```

### Mencocokkan Karakter Berurutan

Pola dicocokkan dari kiri ke kanan, karakter demi karakter:

```text
Pola (Pattern):  404
Teks:            "HTTP 404 Not Found"
Hasil Cocok:     "404" (posisi karakter ke 5-7)
```

```text
Pola (Pattern):  not found
Teks:            "HTTP 404 Not Found" → tidak cocok (karena beda huruf kapital)
Pola (Pattern):  /not found/i → cocok dengan "Not Found"
```

---

## Karakter Khusus (Metacharacters)

Karakter-karakter ini punya arti khusus di regex. Kalau kamu mau mencari karakter ini secara harfiah, kamu harus menambah garis miring terbalik (backslash) `\` di depannya (escape).

| Karakter | Arti | Contoh | Teks Cocok |
|----------|---------|---------|---------|
| `.` | Karakter apa saja (kecuali baris baru) | `c.t` | "cat", "cut", "c9t" |
| `^` | Awal baris/string | `^Dari:` | "Dari:" di awal baris |
| `$` | Akhir baris/string | `\.js$` | "app.js" di akhir baris |
| `\|` | Pilihan (ATAU / OR) | `cat\|dog` | "cat" atau "dog" |
| `\` | Karakter escape (untuk mencari simbol literal) | `\.` | karakter titik "." literal |
| `(` `)` | Pengelompokan (Grouping) | `(ab)+` | "ab", "abab" |
| `[` `]` | Kelas karakter (Character class) | `[aeiou]` | semua huruf vokal |
| `{` `}` | Batasan jumlah (Quantifier range) | `\d{3}` | tiga digit angka |
| `*` | Nol kali atau lebih | `bo*` | "b", "bo", "boo" |
| `+` | Satu kali atau lebih | `bo+` | "bo", "boo", "booo" |
| `?` | Nol atau satu kali | `colou?r` | "color", "colour" |

### Cara Meng-escape Karakter Khusus

Kalau kamu butuh mencari karakter khusus secara harfiah, taruh `\` di depannya:

```text
Pola (Pattern):  \$100\.00
Teks:            "Harga: $100.00"
Hasil Cocok:     "$100.00"
```

```text
Pola (Pattern):  index\.html
Teks:            "Buka index.html dari folder /var/www"
Hasil Cocok:     "index.html"
```

```text
Pola (Pattern):  file\[0\]
Teks:            "Akses variabel file[0] di array"
Hasil Cocok:     "file[0]"
```

### Karakter Titik `.`

Titik mencocokkan satu karakter apa saja **kecuali baris baru** (kecuali flag `s` aktif):

```text
Pola (Pattern):  192\.168\.0\..
Teks:            "192.168.0.1" → cocok
Teks:            "192.168.0.A" → cocok
Teks:            "192x168.0.1" → tidak cocok (karena titik pertama di-escape, jadi wajib karakter titik asli)
```

Kesalahan umum adalah lupa meng-escape karakter titik saat ingin mencari titik asli — selalu escape di URL, alamat IP, dan nama file:

```text
Salah:    192.168.0.1     → juga bakal cocok dengan "192x168y0z1"
Benar:    192\.168\.0\.1  → hanya cocok dengan "192.168.0.1"
```

---

## Kelas Karakter (Character Classes)

Kelas karakter mencocokkan **satu karakter** dari kumpulan yang sudah ditentukan.

### Kumpulan Dasar

```text
[abc]     → cocok dengan "a", "b", atau "c"
[0123]    → cocok dengan "0", "1", "2", atau "3"
[aeiou]   → cocok dengan huruf vokal kecil
```

### Rentang Karakter (Ranges)

```text
[a-z]     → huruf kecil dari a sampai z
[A-Z]     → huruf besar dari A sampai Z
[0-9]     → angka dari 0 sampai 9
[a-zA-Z]  → huruf apa saja (besar maupun kecil)
[a-zA-Z0-9] → huruf atau angka (alfanumerik)
```

### Negasi (Pengecualian)

Tanda caret `^` di dalam kurung siku berarti negasi (mencocokkan karakter yang tidak ada di daftar):

```text
[^0-9]    → karakter apa saja yang BUKAN angka
[^aeiou]  → karakter apa saja yang BUKAN huruf vokal kecil
[^ ]      → karakter apa saja yang BUKAN spasi
```

### Singkatan Kelas Karakter

| Singkatan | Setara Dengan | Arti |
|-----------|------------|---------|
| `\d` | `[0-9]` | Digit angka |
| `\D` | `[^0-9]` | Selain digit angka |
| `\w` | `[a-zA-Z0-9_]` | Karakter kata (huruf, angka, underscore) |
| `\W` | `[^a-zA-Z0-9_]` | Selain karakter kata |
| `\s` | `[ \t\r\n\f]` | Karakter spasi (spasi, tab, baris baru, dll) |
| `\S` | `[^ \t\r\n\f]` | Selain karakter spasi |

### Contoh Praktis

Mencocokkan kode warna hex CSS:

```text
Pola (Pattern):  #[0-9a-fA-F]{6}
Teks Cocok:      "#ff5733", "#00AABB", "#123def"
Tidak Cocok:     "#xyz", "#12"
```

Mencocokkan nama class CSS (harus diawali huruf atau underscore):

```text
Pola (Pattern):  [a-zA-Z_][\w-]*
Teks Cocok:      "nav-bar", "_hidden", "Container"
```

Mencocokkan username (alfanumerik, underscore, panjang 3-16 karakter):

```text
Pola (Pattern):  ^[a-zA-Z0-9_]{3,16}$
Teks Cocok:      "john_doe", "user42", "dev"
Tidak Cocok:     "ab", "user name", "username_yang_kepanjangan_banget"
```

### Karakter Khusus di Dalam Kelas Karakter

Di dalam `[ ]`, sebagian besar karakter khusus kehilangan makna magisnya. Hanya karakter berikut yang punya arti khusus:

| Karakter | Aturan |
|-----------|------|
| `]` | Penutup kelas — escape menjadi `\]` atau letakkan di paling awal `[]abc]` |
| `\` | Karakter escape — tetap berfungsi untuk meng-escape |
| `^` | Negasi — hanya berfungsi jika ditaruh di karakter pertama setelah `[` |
| `-` | Rentang — escape menjadi `\-` atau letakkan di paling awal/akhir `[-abc]` atau `[abc-]` |

```text
Pola (Pattern):  [$@#!]
Teks Cocok:      salah satu dari "$", "@", "#", "!" — tidak perlu di-escape
```

### Kelas Karakter POSIX (Terminal / grep)

| POSIX | Setara Dengan | Arti |
|-------|------------|---------|
| `[:alpha:]` | `[a-zA-Z]` | Huruf |
| `[:digit:]` | `[0-9]` | Angka |
| `[:alnum:]` | `[a-zA-Z0-9]` | Alfanumerik |
| `[:space:]` | `[ \t\r\n\f\v]` | Spasi dan sejenisnya |
| `[:upper:]` | `[A-Z]` | Huruf besar |
| `[:lower:]` | `[a-z]` | Huruf kecil |
| `[:punct:]` | Simbol tanda baca | Karakter simbol |

Digunakan di dalam kurung siku: `[[:digit:]]` — perhatikan kurung sikunya double.

---

## Quantifiers (Penentu Jumlah)

Quantifiers mengatur **berapa kali** elemen sebelumnya boleh muncul.

| Quantifier | Arti | Contoh | Teks Cocok |
|------------|---------|---------|---------|
| `*` | 0 kali atau lebih | `https?://.*` | URL apa saja yang diawali http atau https |
| `+` | 1 kali atau lebih | `\d+` | "1", "42", "99999" |
| `?` | 0 atau 1 kali | `colou?r` | "color", "colour" |
| `{n}` | Tepat n kali | `\d{4}` | "2026", "1999" |
| `{n,}` | Minimal n kali atau lebih | `\w{8,}` | Kata dengan 8 karakter atau lebih |
| `{n,m}` | Antara n sampai m kali | `\d{1,3}` | "1", "42", "255" |

### Rakus (Greedy) vs. Malas (Lazy / Non-Greedy)

Secara default, quantifiers itu bersifat **greedy** (rakus) — mereka bakal mencocokkan teks sebanyak mungkin yang bisa diambil:

```text
Pola (Pattern):  ".+"
Teks:            Dia berkata "halo" dan "dadah"
Greedy (Rakus):  "halo" dan "dadah"   ← mencocokkan dari kutip pertama sampai kutip terakhir
```

Tambahkan tanda `?` setelah quantifier untuk membuatnya bersifat **lazy** (malas) — mencocokkan teks sesedikit mungkin:

```text
Pola (Pattern):  ".+?"
Teks:            Dia berkata "halo" dan "dadah"
Lazy (Malas):    "halo"  dan  "dadah"  ← menghasilkan dua kecocokan terpisah
```

| Greedy (Rakus) | Lazy (Malas) | Arti |
|--------|------|---------|
| `*` | `*?` | 0 kali atau lebih (paling pendek) |
| `+` | `+?` | 1 kali atau lebih (paling pendek) |
| `?` | `??` | 0 atau 1 kali (pilih yang 0 jika bisa) |
| `{n,m}` | `{n,m}?` | Antara n sampai m kali (paling pendek) |

### Contoh Kasus Greedy vs. Lazy

**Mengambil isi tag HTML:**

```text
Greedy:   <.+>
Teks:     <span>Halo</span>
Cocok:    <span>Halo</span>   ← mencocokkan dari < pertama sampai > terakhir

Lazy:     <.+?>
Teks:     <span>Halo</span>
Cocok:    <span>  dan  </span>  ← dua kecocokan terpisah
```

**Mengambil nilai JSON:**

```text
Greedy:   "value": "(.+)"
Teks:     "value": "halo", "other": "dunia"
Cocok:    halo", "other": "dunia    ← terlalu rakus memakan tanda kutip berikutnya

Lazy:     "value": "(.+?)"
Teks:     "value": "halo", "other": "dunia"
Cocok:    halo   ← benar
```

---

## Anchors (Jangkar Posisi)

Anchors tidak mencocokkan karakter — melainkan mencocokkan **posisi** di dalam teks.

| Anchor | Arti | Contoh |
|--------|---------|---------|
| `^` | Awal string (atau awal baris jika flag `m` aktif) | `^#!/bin/bash` |
| `$` | Akhir string (atau akhir baris jika flag `m` aktif) | `\.py$` |
| `\b` | Batas kata (word boundary) | `\berror\b` |
| `\B` | Selain batas kata | `\Berror\B` |

### Awal `^` dan Akhir `$`

```text
Pola (Pattern):  ^#
Teks:            "# Ini heading"  → cocok (karena diawali #)
Teks:            "Bukan # heading" → tidak cocok

Pola (Pattern):  ;$
Teks:            "let x = 5;"    → cocok (karena diakhiri ;)
Teks:            "let x = 5"     → tidak cocok
```

Kombinasikan keduanya untuk validasi teks utuh:

```text
Pola (Pattern):  ^\d{5}$
Teks Cocok:      "90210" (tepat 5 digit angka saja, tanpa teks lain)
Tidak Cocok:     "KODE: 90210" (ada teks tambahan di depannya)
```

### Batas Kata `\b`

Batas kata adalah posisi di antara karakter kata `\w` dan karakter non-kata `\W` (atau awal/akhir string):

```text
Pola (Pattern):  \berror\b
Teks:            "An error occurred"    → cocok dengan "error"
Teks:            "RuntimeError thrown"  → tidak cocok ("error" ada di dalam kata lain)
Teks:            "error_code is set"   → tidak cocok (karena "_" dianggap karakter kata)
```

Ini berguna banget buat mencari kata kunci di file kode agar tidak salah deteksi:

```text
Pola (Pattern):  \bclass\b
Teks Cocok:      "class User"         (keyword class asli)
Tidak Cocok:     "className"          (bagian dari kata lain)
Tidak Cocok:     "subclass"           (bagian dari kata lain)
```

```text
Pola (Pattern):  \buser_id\b
Teks Cocok:      "SELECT user_id FROM" (nama variabel presisi)
Tidak Cocok:     "user_id_backup"     (karena tersambung underscore)
```

---

## Groups dan Capturing (Pengelompokan & Penangkapan)

Tanda kurung `()` membuat grup. Grup punya dua fungsi: **mengelompokkan** pola (buat quantifier/pilihan) dan **menangkap** teks hasil pencocokan untuk dipakai kembali nanti.

### Capturing Group Dasar

```text
Pola (Pattern):  (\d{4})-(\d{2})-(\d{2})
Teks:            "Tanggal: 2026-06-17"
Grup 0:          "2026-06-17"  (seluruh teks cocok)
Grup 1:          "2026"        (tahun)
Grup 2:          "06"          (bulan)
Grup 3:          "17"          (hari)
```

### Non-Capturing Group `(?:...)`

Digunakan saat kamu butuh mengelompokkan pola tapi tidak perlu menangkap hasilnya:

```text
Pola (Pattern):  (?:https?|ftp)://[\w.-]+
Teks:            "Buka https://example.com"
Hasil Cocok:     "https://example.com"
```

Sintaks `(?:...)` mengelompokkan `https?` dan `ftp` untuk pilihan (ATAU) tanpa membuat tangkapan grup baru. Ini lebih cepat dan menjaga urutan index grup kamu tetap rapi.

### Named Capturing Group (Tangkapan Grup Bernama)

Membuat grup tangkapan punya nama khusus agar regex kamu lebih mudah dibaca:

```python
# Sintaks Python
Pola:  (?P<year>\d{4})-(?P<month>\d{2})-(?P<day>\d{2})

# Sintaks JavaScript
Pola:  (?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})
```

```text
Teks:     "2026-06-17"
year  →   "2026"
month →   "06"
day   →   "17"
```

### Backreferences (Referensi Balik) `\1`, `\2`

Merujuk kembali hasil tangkapan grup sebelumnya di dalam pola regex yang sama:

```text
Pola (Pattern):  <(\w+)>.*?</\1>
Teks:            "<div>Halo</div>"
Grup 1:          "div"
\1:              bakal mencocokkan kata "div" lagi di tag penutupnya
Hasil Cocok:     "<div>Halo</div>"
```

Ini memastikan tag pembuka dan penutupnya sama persis:

```text
Pola (Pattern):  <(\w+)>.*?</\1>
Teks:            "<div>Halo</span>" → tidak cocok (karena div ≠ span)
```

**Mencari kata yang ditulis double** (contoh kasus klasik):

```text
Pola (Pattern):  \b(\w+)\s+\1\b
Teks:            "hari ini ini saya belajar"
Hasil Cocok:     "ini ini"
```

---

## Alternation (Pilihan / OR)

Simbol pipa `|` berfungsi sebagai ATAU. Simbol ini punya **prioritas paling rendah** di antara semua operator regex, jadi dia bakal membagi seluruh pola di sebelah kiri dan kanannya seutuhnya:

```text
Pola (Pattern):  cat|dog
Teks Cocok:      "cat" atau "dog"

Pola (Pattern):  error|warning|info
Teks Cocok:      "error", "warning", atau "info"
```

### Alternation dengan Pengelompokan

Jika tidak dikelompokkan, `|` bakal membagi seluruh pola. Gunakan kurung untuk membatasinya:

```text
Pola (Pattern):  gray|grey        → cocok dengan "gray" atau "grey"
Lebih Rapi:      gr(a|e)y         → hasil sama, lebih spesifik batasnya
Bahkan:          gr[ae]y          → hasil sama, terbaik jika cuma 1 karakter beda
```

### Contoh Praktis: Ekstensi File

```text
Pola (Pattern):  \.(js|ts|jsx|tsx)$
Teks Cocok:      "App.tsx", "index.js", "utils.ts"
Tidak Cocok:     "style.css", "README.md"
```

### Urutan Pilihan itu Penting

Engine regex mencoba pilihan dari kiri ke kanan dan bakal berhenti di kecocokan pertama:

```text
Pola (Pattern):  http|https
Teks:            "https://example.com"
Hasil Cocok:     "http"  ← berhenti di sini, tidak pernah mencoba "https"

Solusi:          https?          ← pendekatan lebih baik
Atau:            https|http      ← taruh pilihan yang lebih panjang di kiri
```

---

## Lookahead dan Lookbehind (Peneropongan Posisi)

Lookarounds adalah **zero-width assertions** — mereka mengecek apakah ada pola lain di depan atau di belakang posisi aktif saat ini, tanpa memakan karakter teks tersebut. Hasil peneropongan tidak akan masuk ke output kecocokan teks.

### Positive Lookahead `(?=...)`

"Cocokkan X hanya jika di depannya ada Y":

```text
Pola (Pattern):  \d+(?= rupiah)
Teks:            "Harganya 100 rupiah"
Hasil Cocok:     "100"  (kata " rupiah" cuma diteropong saja, tidak ikut diambil)
```

```text
Pola (Pattern):  \w+(?=\()
Teks:            "Jalankan fungsi getData() di sini"
Hasil Cocok:     "getData"  (kata yang di depannya langsung diikuti tanda kurung buka)
```

### Negative Lookahead `(?!...)`

"Cocokkan X hanya jika di depannya BUKAN Y":

```text
Pola (Pattern):  \d{3}(?!-)
Teks:            "555-1234 dan 9990"
Hasil Cocok:     "999"  (tiga digit yang di depannya bukan tanda strip)
Tidak Cocok:     "555"  (karena di depannya ada strip)
```

### Positive Lookbehind `(?<=...)`

"Cocokkan X hanya jika di belakangnya ada Y":

```text
Pola (Pattern):  (?<=\$)\d+(\.\d{2})?
Teks:            "Total: $49.99"
Hasil Cocok:     "49.99"  (angka yang di belakangnya ada simbol $, tapi $ tidak ikut diambil)
```

### Negative Lookbehind `(?<!...)`

"Cocokkan X hanya jika di belakangnya BUKAN Y":

```text
Pola (Pattern):  (?<!un)happy
Teks:            "I am happy"   → cocok dengan "happy"
Teks:            "I am unhappy"  → tidak cocok (karena di belakang "happy" ada kata "un")
```

### Mengombinasikan Lookarounds

**Validasi Password** — harus ada minimal satu angka, satu huruf besar, satu huruf kecil, dan panjang minimal 8 karakter:

```text
Pola (Pattern):  ^(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{8,}$
```

Penjelasan:
- `(?=.*\d)` — di depan sana, wajib ada angka
- `(?=.*[a-z])` — di depan sana, wajib ada huruf kecil
- `(?=.*[A-Z])` — di depan sana, wajib ada huruf besar
- `.{8,}` — total panjang minimal 8 karakter apa saja

### Tabel Rangkuman Peneropongan

| Sintaks | Nama | Arti |
|--------|------|---------|
| `(?=...)` | Positive lookahead | Di depannya ada ... |
| `(?!...)` | Negative lookahead | Di depannya BUKAN ... |
| `(?<=...)` | Positive lookbehind | Di belakangnya ada ... |
| `(?<!...)` | Negative lookbehind | Di belakangnya BUKAN ... |

---

## Flag Umum

Flag mengubah cara engine regex menafsirkan pola pencarian.

| Flag | Nama | Efek |
|------|------|--------|
| `g` | Global | Cari semua kecocokan, jangan berhenti di hasil pertama |
| `i` | Case-insensitive | Mengabaikan sensitivitas huruf besar/kecil |
| `m` | Multiline | Membuat `^` dan `$` berlaku per awal/akhir baris baru, bukan seluruh file |
| `s` | Dotall / Single-line | Membuat karakter titik `.` bisa mencocokkan baris baru (`\n`) |
| `u` | Unicode | Menangani karakter Unicode (pada JS) |
| `x` | Extended / Verbose | Mengabaikan spasi kosong di pola dan membolehkan komentar (Python, PCRE) |

### Flag Global `g`

```javascript
// Tanpa g — cuma dapat kecocokan pertama
"aaa".match(/a/)        // ["a"]

// Pakai g — dapat semua kecocokan
"aaa".match(/a/g)       // ["a", "a", "a"]
```

### Flag Multiline `m`

```text
Teks:
  "Baris 1\nBaris 2\nBaris 3"

Tanpa m:
  ^Baris   → hanya cocok dengan "Baris 1" di awal teks utama

Pakai m:
  ^Baris   → cocok dengan "Baris 1", "Baris 2", dan "Baris 3" (awal tiap baris)
```

---

## Pola Praktis yang Sering Dipakai

### Email (versi simpel)

```text
Pola (Pattern):  ^[\w.+-]+@[\w-]+\.[\w.-]+$
```

```text
✓  user@example.com
✓  john.doe+tag@company.co.uk
✓  admin@sub.domain.org
✗  @example.com
✗  user@
✗  user@.com
```

### URL

```text
Pola (Pattern):  https?://[\w.-]+(?::\d+)?(?:/[\w./?%&=~#-]*)?
```

```text
✓  https://example.com
✓  http://api.example.com:8080/v1/users?page=2
✓  https://en.wikipedia.org/wiki/Regular_expression
✗  ftp://files.example.com (butuh (?:https?|ftp) jika ingin mendukung ftp)
✗  example.com (tidak ada protokol http/https)
```

### Alamat IPv4

```text
Ketat (0-255 per segmen):
(?:25[0-5]|2[0-4]\d|[01]?\d\d?)\.(?:25[0-5]|2[0-4]\d|[01]?\d\d?)\.(?:25[0-5]|2[0-4]\d|[01]?\d\d?)\.(?:25[0-5]|2[0-4]\d|[01]?\d\d?)
```

```text
✓  192.168.1.1
✓  10.0.0.255
✗  256.1.1.1    (versi ketat menolak angka di atas 255)
✗  192.168.1    (kurang satu oktet)
```

### Tanggal (YYYY-MM-DD)

```text
Pola (Pattern):  ^\d{4}-(0[1-9]|1[0-2])-(0[1-9]|[12]\d|3[01])$
```

```text
✓  2026-06-17
✓  2000-01-31
✗  2026-13-01  (bulan tidak valid)
✗  2026-06-32  (tanggal tidak valid)
```

---

## Regex di JavaScript

JavaScript menggunakan sintaks `/pola/flag` secara literal atau class constructor `RegExp`.

### Membuat Objek Regex

```javascript
// Menggunakan sintaks literal (lebih disukai kalau polanya statis)
const pattern = /\d{3}-\d{4}/g;

// Menggunakan constructor (dipakai kalau polanya dinamis dari input user)
const userInput = "error";
const pattern = new RegExp(`\\b${userInput}\\b`, "gi");
// Catatan: butuh double backslash '\\' di dalam string biasa
```

### Method `test()` — Cek Kecocokan

Menghasilkan `true` atau `false`:

```javascript
const emailRegex = /^[\w.+-]+@[\w-]+\.[\w.-]+$/;

emailRegex.test("user@example.com");     // true
emailRegex.test("bukan-email");          // false
```

### Method `match()` — Cari Hasil Cocok

Menghasilkan array isi kecocokan (jika pakai flag `g`) atau detail kecocokan (tanpa `g`):

```javascript
// Semua hasil cocok
const text = "Error ada di baris 42, 87, dan 156";
const numbers = text.match(/\d+/g);
// ["42", "87", "156"]

// Hasil cocok tunggal dengan detail grup tangkapan
const logLine = "[2026-06-17] ERROR: Koneksi ditolak";
const result = logLine.match(/\[(\d{4}-\d{2}-\d{2})\]\s(\w+):\s(.+)/);
// result[0] = "[2026-06-17] ERROR: Koneksi ditolak"
// result[1] = "2026-06-17"
// result[2] = "ERROR"
// result[3] = "Koneksi ditolak"
```

### Method `replace()` — Ganti Teks

```javascript
// Membersihkan spasi double
const cleaned = "  halo   dunia  ".replace(/\s+/g, " ").trim();
// "halo dunia"

// Menggunakan capture groups untuk tukar format tanggal
const isoDate = "2026-06-17";
const usDate = isoDate.replace(/(\d{4})-(\d{2})-(\d{2})/, "$2/$3/$1");
// "06/17/2026"

// Menggunakan fungsi callback untuk penggantian dinamis
const template = "Halo {name}, kamu punya {count} pesan baru";
const data = { name: "Alice", count: 5 };
const result = template.replace(/\{(\w+)\}/g, (match, key) => data[key] ?? match);
// "Halo Alice, kamu punya 5 pesan baru"
```

### Method `split()` — Potong String

```javascript
// Potong string CSV dengan spasi tidak teratur
const line = "Alice,  Bob, Charlie ,  Dave";
const names = line.split(/\s*,\s*/);
// ["Alice", "Bob", "Charlie", "Dave"]
```

---

## Regex di Python

Python menggunakan module bawaan `re`.

### Penggunaan Dasar

```python
import re

# Cari di mana saja di dalam teks
result = re.search(r"\d+", "Order #12345 berhasil")
if result:
    print(result.group())   # "12345"
    print(result.start())   # 7
    print(result.end())     # 12

# Cari khusus di awal string saja
result = re.match(r"\d+", "12345 berhasil")
if result:
    print(result.group())   # "12345"
```

### Fungsi `findall()` — Ambil Semua Hasil

```python
text = "Suhu saat ini: 72°F, 68°F, 75°F"
temps = re.findall(r"\d+(?=°F)", text)
# ['72', '68', '75']
```

### Fungsi `sub()` — Cari dan Ganti

```python
text = "Nama saya John Smith"
swapped = re.sub(r"(\w+)\s(\w+)", r"\2 \1", text)
# "Nama saya Smith John"
```

---

## Regex di Vim

Vim punya dialek regex sendiri. Perbedaan terbesarnya: secara default, banyak karakter khusus yang harus di-escape agar bisa berfungsi (kebalikan dari PCRE).

### Opsi Magic Mode Vim

| Mode | Awalan | Efek / Perilaku |
|------|--------|----------|
| `\v` (very magic) | `\v` | Paling mirip PCRE — `+`, `(`, `)`, `{`, `}`, `|` otomatis dianggap karakter khusus |
| `\m` (magic) | `\m` | Default Vim — `.`, `*`, `^`, `$`, `[` khusus; tapi `+`, `(`, `{` wajib di-escape |
| `\M` (nomagic) | `\M` | Hampir semua dianggap karakter literal biasa |
| `\V` (very nomagic) | `\V` | Semua literal kecuali simbol `\` |

**Rekomendasi:** Selalu pakai awalan `\v` (very magic) di awal pencarian Vim agar perilakunya mirip regex modern pada umumnya.

```vim
" Mode magic bawaan Vim — wajib escape kurung, tambah, dll
/\(error\|warning\)\+

" Mode very magic (\v) — bersih dan gampang dibaca seperti PCRE
/\v(error|warning)+
```

### Pencarian di Vim

```vim
" Cari ke arah depan (very magic)
/\v pola_cari

" Cari ke arah belakang
?\v pola_cari

" Lompat ke kecocokan berikutnya
n

" Lompat ke kecocokan sebelumnya
N

" Bersihkan highlight pencarian aktif
:noh
```

### Sensitivitas Kapital saat Mencari di Vim

```vim
" Cari tanpa sensitif kapital (ignorecase)
/\cpola
/\cerror        " bakal cocok dengan error, Error, ERROR

" Cari sensitif kapital
/\Cpola
```

### Cari dan Ganti di Vim (Substitute)

Sintaks dasar:

```vim
:%s/pola/pengganti/flag
```

Contoh kasus substitute:

```vim
" Ganti semua kata di satu file penuh
:%s/fungsiLama/fungsiBaru/g

" Ganti disertai konfirmasi satu per satu
:%s/namaLama/namaBaru/gc
" ketik y = ya, n = lewati, a = ganti semua sisa, q = batalkan

" Ganti hanya pada baris visual yang diblok saja
:'<,'>s/lama/baru/g

" Ganti nama menggunakan very magic (\v)
:%s/\v(\w+)\.length/\1.size/g
" "array.length" menjadi "array.size"

" Menghapus spasi di akhir baris
:%s/\s\+$//e
```

### Simbol Pengganti Spesifik di Vim

| Simbol | Arti |
|----------|---------|
| `\1`, `\2` | Hasil tangkapan grup 1, 2, dll |
| `\0` atau `&` | Seluruh teks yang cocok |
| `\u` | Ubah satu karakter berikutnya jadi huruf besar |
| `\l` | Ubah satu karakter berikutnya jadi huruf kecil |
| `\U` | Ubah seluruh teks setelahnya jadi huruf besar sampai ketemu `\E` |
| `\L` | Ubah seluruh teks setelahnya jadi huruf kecil sampai ketemu `\E` |
| `\E` | Batas akhir pengubahan huruf |
| `\r` | Membuat baris baru (newline) pada teks pengganti |

Contoh penggunaan:

```vim
" Mengubah huruf pertama kata menjadi kapital (Title Case)
:%s/\v<(\w)/\u\1/g
" "hello world" → "Hello World"
```

### Karakter Khusus Unik Vim Regex

| Simbol | Arti |
|------|---------|
| `\<` | Batas awal kata |
| `\>` | Batas akhir kata |
| `\zs` | Penentu awal kecocokan (memotong teks sebelum simbol ini dari output) |
| `\ze` | Penentu akhir kecocokan |
| `\_s` | Karakter spasi termasuk baris baru |
| `\{-}` | Quantifier non-greedy (seperti `*?` pada PCRE) |

```vim
" Mencocokkan tag HTML secara non-greedy di Vim:
/\v<.{-}>
" (Setara dengan <.+?> di PCRE)

" Mencocokkan isi argumen di dalam kurung saja
/\v\(\zs[^)]+\ze\)
" Pada "hitung(a, b)" → mencocokkan "a, b"
```

---

## Regex di Terminal (Shell)

### grep — Mencari di File

```bash
# Pencarian dasar
grep "error" /var/log/syslog

# Abaikan huruf kapital
grep -i "error" server.log

# Gunakan Extended Regex (ERE) — mendukung |, +, () tanpa escape
grep -E "(error|warning|critical)" server.log

# Tampilkan nomor baris hasil cocok
grep -n "TODO" src/*.js

# Hanya tampilkan teks yang cocok saja (bukan seluruh baris)
grep -o "[0-9]\+\.[0-9]\+" package.json

# Tampilkan log baris sebelum dan sesudah hasil cocok
grep -B 2 -A 2 "error" app.log   # 2 baris sebelum (before) dan sesudah (after)
```

---

## Kesalahan Umum & Solusinya

### 1. Lupa Meng-escape Titik `.`

```text
Salah:   192.168.0.1     → juga bakal cocok dengan "192x168y0z1"
Benar:   192\.168\.0\.1  → hanya cocok dengan "192.168.0.1"
```

Karena titik `.` di regex berarti karakter apa saja. Selalu escape titik jika ingin mendeteksi karakter titik literal.

### 2. Terjebak Masalah Greedy (Rakus)

```text
Salah:   <.+>
Teks:    <b>Tebal</b>
Cocok:   <b>Tebal</b>     ← memakan seluruh baris tag

Benar:   <.+?>           ← bersifat lazy (malas), memisahkan <b> dan </b>
Lebih Oke: <[^>]+>        ← kelas karakter negasi, tidak butuh backtracking
```

### 3. Tidak Memasang Anchor Saat Validasi

```text
Salah:   \d{5}
Teks:    "KODE POS: 90210-1234"
Hasil:   Mendeteksi "90210" di dalam string panjang tersebut

Benar:   ^\d{5}$          ← memaksa string harus tepat berisi 5 digit saja
```

---

## Tips Performa Regex

1. **Hindari Greedy `.*` jika bisa**: Gunakan kelas karakter negasi. Misalnya, mencari teks di dalam kutip dua, gunakan `"[^"]*"` daripada `".*?"`. Ini jauh lebih cepat dan mencegah backtracking berlebih.
2. **Compile Pola yang Dipakai Berulang**: Pada Python atau JS (constructor), lakukan compile regex sekali di luar perulangan (loop) agar tidak membebani memori compiler berkali-kali.
3. **Gunakan Non-Capturing Group `(?:...)`**: Jika kamu hanya butuh mengelompokkan pilihan (ATAU) tanpa berniat memakai isi teksnya di kemudian waktu, non-capturing group menghemat penggunaan alokasi memori.

---

## Tabel Rangkuman Cepat

### Sintaks Utama

| Pola | Arti |
|---------|---------|
| `.` | Karakter apa saja (kecuali baris baru) |
| `\d` | Digit angka `[0-9]` |
| `\w` | Karakter kata (huruf, angka, `_`) |
| `\s` | Karakter spasi |
| `[abc]` | Salah satu dari a, b, atau c |
| `[^abc]` | Selain dari a, b, atau c |
| `*` | 0 kali atau lebih |
| `+` | 1 kali atau lebih |
| `?` | 0 atau 1 kali |
| `{n,m}` | Antara n sampai m kali |
| `*?` | Versi lazy (non-greedy) dari `*` |
| `^`, `$` | Awal / akhir baris |
| `\b` | Batas kata (word boundary) |
| `(...)` | Capturing group |
| `(?:...)` | Non-capturing group |
| `(?=...)` | Lookahead positif |
| `(?!...)` | Lookahead negatif |

### Referensi Cepat Pola Umum

| Target | Pola |
|------|---------|
| Tanggal (ISO) | `\d{4}-\d{2}-\d{2}` |
| Alamat IPv4 | `\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}` |
| Kode Warna Hex | `#[0-9a-fA-F]{3,6}` |
| Baris Kosong | `^\s*$` |
| Spasi di Akhir | `\s+$` |
| Tag HTML | `<[^>]+>` |

---

## Sumber Belajar Lainnya

- [regex101.com](https://regex101.com) — Tester regex interaktif yang memberikan penjelasan detail per karakter pola (mendukung PCRE, JS, Python).
- [regexr.com](https://regexr.com) — Tester visual regex yang juga sangat bagus dan interaktif.
- [Regular-Expressions.info](https://www.regular-expressions.info) — Situs tutorial dan panduan regex terlengkap di internet.
