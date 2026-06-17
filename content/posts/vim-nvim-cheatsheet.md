---
title: "Cheatsheet Lengkap Vim / Neovim"
description: "Cheatsheet praktis Vim dan Neovim untuk coding harian, navigasi, edit teks, buffer, window, macro, search, LSP, terminal, dan workflow produktivitas."
date: 2026-06-17
tags: ["vim", "neovim", "nvim", "cheatsheet", "developer-tools"]
url: /vim
draft: false
---

Vim dan Neovim itu editor modal. Jadi alih-alih pakai mouse untuk semua hal, kamu bakal mengombinasikan **mode**, **motion**, **operator**, dan **text object** buat edit teks dengan cepat.

Cheatsheet ini didesain buat kerjaan development sehari-hari — panduan praktis yang bisa kamu bookmark dan buka lagi kapan saja.

---

## Konsep Utama

Perintah Vim biasanya dibangun dengan pola seperti ini:

```text
operator + motion
```

Contohnya:

```vim
dw      " hapus kata (delete word)
d$      " hapus sampai akhir baris
ciw     " ubah kata tempat kursor berada (change inside word)
yap     " salin satu paragraf (yank around paragraph)
```

Kamu juga bisa menambahkan jumlah (count):

```vim
3w      " maju 3 kata
d3w     " hapus 3 kata
5j      " turun 5 baris
```

Mindset paling penting:

> Jangan dihafal semuanya sekaligus. Pelajari motion dulu, lalu operator, baru kemudian text object.

---

## Mode

| Mode | Arti | Cara Masuk | Cara Keluar |
|---|---|---|---|
| Normal | Navigasi dan jalankan perintah | `Esc` | - |
| Insert | Ketik teks | `i`, `a`, `o` | `Esc` |
| Visual | Pilih/blok teks | `v` | `Esc` |
| Visual Line | Pilih seluruh baris | `V` | `Esc` |
| Visual Block | Pilih kolom/blok vertikal | `Ctrl-v` | `Esc` |
| Command-line | Jalankan perintah Ex | `:` | `Enter` / `Esc` |
| Replace | Timpa teks yang ada | `R` | `Esc` |
| Terminal Normal | Kontrol buffer terminal | `Ctrl-\\ Ctrl-n` | - |

---

## Navigasi Dasar

| Tombol | Aksi |
|---|---|
| `h` | Geser ke kiri |
| `j` | Geser ke bawah |
| `k` | Geser ke atas |
| `l` | Geser ke kanan |
| `gj` | Geser ke bawah berdasarkan baris visual (saat line wrap aktif) |
| `gk` | Geser ke atas berdasarkan baris visual (saat line wrap aktif) |

Gunakan count:

```vim
10j     " turun 10 baris
5l      " geser ke kanan 5 karakter
```

---

## Navigasi Kata

| Tombol | Aksi |
|---|---|
| `w` | Lompat ke awal kata berikutnya |
| `W` | Lompat ke awal WORD berikutnya (dipisahkan oleh spasi) |
| `e` | Lompat ke akhir kata berikutnya |
| `E` | Lompat ke akhir WORD berikutnya |
| `b` | Lompat mundur ke awal kata |
| `B` | Lompat mundur ke awal WORD |
| `ge` | Lompat mundur ke akhir kata sebelumnya |
| `gE` | Lompat mundur ke akhir WORD sebelumnya |

Perbedaan antara `word` dan `WORD`:

```text
hello.world example
```

- `w` menganggap tanda baca (seperti titik) sebagai pemisah kata.
- `W` hanya menganggap spasi sebagai pemisah kata.

---

## Navigasi Baris

| Tombol | Aksi |
|---|---|
| `0` | Lompat ke awal baris (kolom pertama) |
| `^` | Lompat ke karakter pertama yang bukan spasi |
| `$` | Lompat ke akhir baris |
| `g_` | Lompat ke karakter terakhir yang bukan spasi |
| `+` | Lompat ke awal baris berikutnya yang bukan spasi |
| `-` | Lompat ke awal baris sebelumnya yang bukan spasi |
| `gg` | Lompat ke awal file |
| `G` | Lompat ke akhir file |
| `42G` | Lompat ke baris nomor 42 |
| `:42` | Lompat ke baris nomor 42 |

---

## Navigasi Layar

| Tombol | Aksi |
|---|---|
| `Ctrl-d` | Turun setengah halaman |
| `Ctrl-u` | Naik setengah halaman |
| `Ctrl-f` | Turun satu halaman penuh |
| `Ctrl-b` | Naik satu halaman penuh |
| `zt` | Posisikan baris aktif di paling atas layar |
| `zz` | Posisikan baris aktif di tengah layar |
| `zb` | Posisikan baris aktif di paling bawah layar |
| `H` | Lompat ke baris paling atas layar (High) |
| `M` | Lompat ke baris tengah layar (Middle) |
| `L` | Lompat ke baris paling bawah layar (Low) |

---

## Pencarian dan Navigasi Hasil

| Tombol | Aksi |
|---|---|
| `/teks` | Cari `teks` ke arah depan |
| `?teks` | Cari `teks` ke arah belakang |
| `n` | Lompat ke hasil pencarian berikutnya |
| `N` | Lompat ke hasil pencarian sebelumnya |
| `*` | Cari kata di bawah kursor ke arah depan |
| `#` | Cari kata di bawah kursor ke arah belakang |
| `g*` | Cari kata parsial di bawah kursor ke arah depan |
| `g#` | Cari kata parsial di bawah kursor ke arah belakang |

Opsi pencarian yang berguna:

```vim
:set ignorecase      " cari tanpa sensitif huruf besar/kecil
:set smartcase       " jadi sensitif kalau kamu ngetik huruf kapital
:set hlsearch        " aktifkan highlight hasil pencarian
:set nohlsearch      " matikan highlight hasil pencarian
:noh                 " hapus highlight pencarian aktif saat ini
```

---

## Pencarian Karakter: `f`, `F`, `t`, `T`

Pencarian karakter ini berguna banget buat navigasi cepat dalam satu baris.

| Tombol | Aksi |
|---|---|
| `f<char>` | Lompat maju **ke** karakter yang dituju |
| `F<char>` | Lompat mundur **ke** karakter yang dituju |
| `t<char>` | Lompat maju **sampai sebelum** karakter yang dituju (till) |
| `T<char>` | Lompat mundur **sampai setelah** karakter yang dituju |
| `;` | Ulangi pencarian `f`, `F`, `t`, atau `T` sebelumnya |
| `,` | Ulangi pencarian sebelumnya tapi dengan arah berlawanan |

Contoh baris kode:

```js
const user = { name: "Pian", age: 31 };
```

### Lompat ke `{`

```vim
f{
```

Ini bakal mindahin kursor maju **tepat di atas** karakter `{`.

### Lompat sebelum `{`

```vim
t{
```

Ini bakal mindahin kursor maju **satu karakter sebelum** `{`.

### Hapus sampai sebelum `{`

```vim
dt{
```

Sebelum:

```js
const user = { name: "Pian", age: 31 };
```

Sesudah:

```js
{ name: "Pian", age: 31 };
```

### Hapus termasuk `{`

```vim
df{
```

Ini bakal menghapus teks dari posisi kursor sampai karakter `{`.

### Ubah teks di dalam kurung kurawal

```vim
ci{
```

Ini bakal menghapus isi di dalam `{ ... }` dan langsung masuk ke mode Insert.

### Hapus beserta kurung kurawal

```vim
da{
```

Ini bakal menghapus `{ ... }` beserta seluruh isinya.

---

## Operator

Operator adalah aksi yang dijalankan. Biasanya butuh motion atau text object setelahnya.

| Operator | Arti |
|---|---|
| `d` | Hapus / potong (delete/cut) |
| `c` | Ubah / hapus lalu masuk ke mode Insert (change) |
| `y` | Salin (yank/copy) |
| `v` | Blok teks secara visual (visual select) |
| `>` | Geser indentasi ke kanan |
| `<` | Geser indentasi ke kiri |
| `=` | Auto-format indentasi |
| `gq` | Format teks (wrap text) |
| `gu` | Ubah ke huruf kecil semua |
| `gU` | Ubah ke huruf besar semua |
| `~` | Toggle huruf besar/kecil (lowercase <-> uppercase) |

Kalau operator diketik dua kali, aksinya bakal diterapkan ke baris aktif saat ini:

| Perintah | Aksi |
|---|---|
| `dd` | Hapus satu baris |
| `cc` | Ubah satu baris |
| `yy` | Salin satu baris |
| `>>` | Geser indentasi baris ke kanan |
| `<<` | Geser indentasi baris ke kiri |
| `==` | Auto-format indentasi baris aktif |
| `gUU` | Ubah satu baris jadi huruf besar |
| `guu` | Ubah satu baris jadi huruf kecil |

---

## Contoh Kombinasi Operator + Motion

| Perintah | Aksi |
|---|---|
| `dw` | Hapus sampai awal kata berikutnya |
| `dW` | Hapus sampai awal WORD berikutnya |
| `de` | Hapus sampai akhir kata aktif |
| `db` | Hapus mundur sampai awal kata |
| `d$` | Hapus sampai akhir baris |
| `d0` | Hapus mundur sampai awal baris |
| `d^` | Hapus mundur sampai karakter pertama yang bukan spasi |
| `dgg` | Hapus dari kursor sampai awal file |
| `dG` | Hapus dari kursor sampai akhir file |
| `cw` | Ubah sampai awal kata berikutnya |
| `c$` | Ubah sampai akhir baris |
| `ciw` | Ubah kata tempat kursor berada |
| `ci"` | Ubah teks di dalam tanda kutip dua |
| `ci'` | Ubah teks di dalam tanda kutip satu |
| `ci(` | Ubah teks di dalam tanda kurung |
| `ci{` | Ubah teks di dalam kurung kurawal |
| `y$` | Salin sampai akhir baris |
| `yG` | Salin dari kursor sampai akhir file |
| `gUiw` | Ubah kata aktif jadi huruf besar |
| `guw` | Ubah kata berikutnya jadi huruf kecil |

---

## Text Object

Text object memungkinkan kamu buat memanipulasi struktur teks yang bermakna.

```text
operator + i/a + object
```

- `i` berarti **inside** (di dalam objek saja).
- `a` berarti **around** (termasuk karakter pembungkus atau spasi di sekitarnya).

| Text Object | Arti |
|---|---|
| `iw` | Di dalam kata (inside word) |
| `aw` | Seluruh kata beserta spasinya (around word) |
| `is` | Di dalam kalimat (inside sentence) |
| `as` | Seluruh kalimat (around sentence) |
| `ip` | Di dalam paragraf (inside paragraph) |
| `ap` | Seluruh paragraf (around paragraph) |
| `i"` | Di dalam tanda kutip dua |
| `a"` | Seluruh tanda kutip dua beserta isinya |
| `i'` | Di dalam tanda kutip satu |
| `a'` | Seluruh tanda kutip satu beserta isinya |
| `` i` `` | Di dalam backticks |
| `` a` `` | Seluruh backticks beserta isinya |
| `i(` atau `ib` | Di dalam tanda kurung |
| `a(` atau `ab` | Seluruh tanda kurung beserta isinya |
| `i[` | Di dalam kurung siku |
| `a[` | Seluruh kurung siku beserta isinya |
| `i{` or `iB` | Di dalam kurung kurawal |
| `a{` or `aB` | Seluruh kurung kurawal beserta isinya |
| `it` | Di dalam tag HTML/XML (inside tag) |
| `at` | Seluruh tag HTML/XML beserta isinya |

Contoh penggunaan:

```vim
ciw     " ubah kata tempat kursor berada
daw     " hapus kata beserta spasinya
yi"     " salin isi di dalam kutip dua
da"     " hapus kutip dua beserta seluruh isinya
ci{     " ubah isi di dalam kurung kurawal
ya(     " salin seluruh kurung beserta isinya
vit     " blok visual isi di dalam tag HTML
```

Contoh kasus:

```js
const message = "Hello world";
```

Jalankan perintah ini saat kursor di dalam kata "Hello":

```vim
ci"
```

Hasilnya:

```js
const message = "|";
```

Kursor kamu langsung masuk ke mode Insert di dalam tanda kutip.

---

## Shortcut Mode Insert

| Tombol | Aksi |
|---|---|
| `i` | Masuk mode Insert sebelum posisi kursor |
| `I` | Masuk mode Insert di awal baris (karakter non-spasi pertama) |
| `a` | Masuk mode Insert setelah posisi kursor (append) |
| `A` | Masuk mode Insert di akhir baris |
| `o` | Buka baris baru di bawah baris aktif lalu masuk mode Insert |
| `O` | Buka baris baru di atas baris aktif lalu masuk mode Insert |
| `s` | Hapus satu karakter di kursor lalu masuk mode Insert |
| `S` | Hapus seluruh baris lalu masuk mode Insert |
| `C` | Hapus sampai akhir baris lalu masuk mode Insert |
| `r<char>` | Ganti satu karakter di kursor dengan karakter baru |
| `R` | Masuk mode Replace (menimpa teks) |

Ketika berada di dalam mode Insert:

| Kombinasi | Aksi |
|---|---|
| `Ctrl-h` | Backspace (hapus satu karakter ke belakang) |
| `Ctrl-w` | Hapus satu kata ke belakang |
| `Ctrl-u` | Hapus sampai awal baris |
| `Ctrl-o` | Jalankan satu perintah mode Normal, lalu otomatis balik ke mode Insert |
| `Ctrl-r <register>` | Tempel/paste isi dari register tertentu |
| `Ctrl-n` | Autocomplete kata berikutnya |
| `Ctrl-p` | Autocomplete kata sebelumnya |

Contoh penggunaan:

```vim
Ctrl-o zz
```

Saat lagi ngetik di mode Insert, perintah ini bakal memposisikan baris aktif ke tengah layar tanpa perlu keluar dari mode Insert.

---

## Mengedit Teks

| Tombol | Aksi |
|---|---|
| `x` | Hapus karakter di bawah kursor |
| `X` | Hapus karakter di belakang kursor |
| `s` | Hapus karakter di kursor lalu masuk mode Insert |
| `S` | Hapus satu baris penuh lalu masuk mode Insert |
| `r<char>` | Timpa satu karakter di kursor |
| `J` | Gabungkan baris aktif dengan baris di bawahnya |
| `gJ` | Gabungkan baris tanpa menambahkan spasi pemisah |
| `.` | Ulangi aksi perubahan terakhir |
| `u` | Undo (batalkan aksi) |
| `Ctrl-r` | Redo (ulangi aksi yang dibatalkan) |

Contoh penggunaan:

```vim
ciwhello<Esc>   " ganti kata aktif jadi 'hello'
A;<Esc>         " tambahkan titik koma di akhir baris
J               " gabungkan baris berikutnya ke baris aktif
```

---

## Copy, Cut, dan Paste

| Tombol | Aksi |
|---|---|
| `y` | Salin (yank) dengan motion |
| `yy` | Salin satu baris aktif |
| `Y` | Salin sampai akhir baris (di kebanyakan konfigurasi; di Vim/Neovim modern defaultnya sama dengan `yy` kecuali diubah) |
| `d` | Hapus/potong (delete/cut) dengan motion |
| `dd` | Hapus/potong satu baris aktif |
| `p` | Tempel (paste) setelah kursor atau di bawah baris aktif |
| `P` | Tempel (paste) sebelum kursor atau di atas baris aktif |
| `gp` | Tempel teks lalu taruh kursor setelah teks tempelan tersebut |
| `gP` | Tempel teks sebelum kursor lalu taruh kursor setelah teks tempelan tersebut |

Contoh penggunaan:

```vim
yy      " salin baris aktif
3yy     " salin 3 baris
dd      " hapus baris aktif
3dd     " hapus 3 baris
p       " tempel di bawah
P       " tempel di atas
```

---

## Register

Register itu tempat penyimpanan sementara buat teks yang kamu salin atau hapus.

| Register | Arti |
|---|---|
| `"` | Register default (unnamed register) |
| `0` | Register hasil salin (yank) terakhir |
| `1`-`9` | Register riwayat penghapusan (delete history) |
| `+` | Clipboard sistem operasi |
| `*` | Clipboard seleksi sistem (di beberapa OS) |
| `_` | Register lubang hitam (black hole); hapus tanpa menyimpannya ke clipboard |
| `%` | Nama file aktif saat ini |
| `.` | Teks terakhir yang dimasukkan |
| `:` | Perintah command-line terakhir yang dijalankan |

Cara pakai register:

```vim
"+y     " salin teks ke clipboard sistem OS
"+p     " tempel teks dari clipboard sistem OS
"_d     " hapus teks tanpa menimpa register default
"0p     " tempel teks hasil salinan terakhir, bukan hasil hapusan terakhir
```

Contoh praktis:

```vim
"+yy    " salin baris aktif ke clipboard komputer kamu
"_dd    " hapus satu baris tanpa merusak teks yang udah kamu salin sebelumnya
"0p     " tempel teks yang terakhir kamu salin (bukan teks yang terakhir kamu hapus)
```

---

## Undo dan Redo

| Tombol | Aksi |
|---|---|
| `u` | Undo |
| `Ctrl-r` | Redo |
| `U` | Kembalikan baris aktif ke kondisi awal sebelum kursor pindah |
| `:earlier 5m` | Kembalikan file ke kondisi 5 menit yang lalu |
| `:later 5m` | Majukan file ke kondisi 5 menit setelahnya |

Aktifkan riwayat undo yang persisten di konfigurasi:

```vim
set undofile
```

Neovim dengan Lua:

```lua
vim.opt.undofile = true
```

---

## Mode Visual

| Tombol | Aksi |
|---|---|
| `v` | Blok teks per karakter |
| `V` | Blok teks per baris |
| `Ctrl-v` | Blok teks secara kolom/vertikal (block-wise) |
| `o` | Pindahkan kursor ke ujung blok seleksi lainnya |
| `gv` | Blok ulang area seleksi visual terakhir |
| `y` | Salin area yang diblok |
| `d` | Hapus area yang diblok |
| `c` | Ubah area yang diblok |
| `>` | Geser indentasi area blok ke kanan |
| `<` | Geser indentasi area blok ke kiri |
| `=` | Auto-format indentasi area blok |

Contoh penggunaan Visual Block:

```vim
Ctrl-v
j/j/k/k untuk memblok beberapa baris ke bawah
I// <Esc>
```

Ini bakal menambahkan komentar `//` di awal semua baris yang diblok secara bersamaan.

Menambahkan sesuatu di akhir baris secara masal:

```vim
Ctrl-v
blok beberapa baris
A; <Esc>
```

Ini bakal menambahkan karakter `;` di akhir semua baris yang diblok secara bersamaan.

---

## Indentasi

| Tombol | Aksi |
|---|---|
| `>>` | Geser indentasi baris aktif ke kanan |
| `<<` | Geser indentasi baris aktif ke kiri |
| `>}` | Geser indentasi sampai paragraf berikutnya |
| `<}` | Kurangi indentasi sampai paragraf berikutnya |
| `=}` | Auto-format indentasi sampai paragraf berikutnya |
| `gg=G` | Auto-format indentasi seluruh file dari atas sampai bawah |

Di Mode Visual:

```vim
>       " geser indentasi seleksi ke kanan
<       " geser indentasi seleksi ke kiri
=       " auto-format indentasi seleksi
```

---

## Komentar Code

Secara default, Vim gak punya shortcut komentar bawaan yang otomatis paham semua bahasa pemrograman. Berikut cara mengakalinya:

### Cara manual bawaan Vim

```vim
I// <Esc>       " tambah // di awal baris aktif
```

Untuk banyak baris sekaligus:

```vim
Ctrl-v
blok baris yang mau dikomentari
I// <Esc>
```

### Menggunakan Plugin

Kebanyakan pengguna Neovim menggunakan plugin komentar seperti `numToStr/Comment.nvim` atau sejenisnya.

Shortcut bawaan plugin yang umum:

| Perintah | Aksi |
|---|---|
| `gcc` | Pasang/lepas komentar pada baris aktif |
| `gc` (Mode Visual) | Pasang/lepas komentar pada teks yang diblok |
| `gc{motion}` | Pasang/lepas komentar berdasarkan motion |

Contoh:

```vim
gcc     " komentari/uncomment baris aktif saat ini
gcip    " komentari/uncomment seluruh isi paragraf
```

---

## Cari dan Ganti (Search & Replace)

Format dasar penggantian teks:

```vim
:s/teks_lama/teks_baru/
```

Ini bakal mengganti kecocokan pertama di baris aktif saja.

Ganti semua kecocokan di baris aktif saja:

```vim
:s/teks_lama/teks_baru/g
```

Ganti semua kecocokan di seluruh file:

```vim
:%s/teks_lama/teks_baru/g
```

Ganti dengan konfirmasi satu per satu:

```vim
:%s/teks_lama/teks_baru/gc
```

Ganti hanya pada baris yang diblok saja:

```vim
:'<,'>s/teks_lama/teks_baru/g
```

Flag yang sering digunakan:

| Flag | Arti |
|---|---|
| `g` | Ganti semua kecocokan di setiap baris (global) |
| `c` | Minta konfirmasi sebelum mengganti (confirm) |
| `i` | Abaikan sensitivitas huruf kapital (ignore case) |
| `I` | Paksa sensitif huruf kapital (case-sensitive) |
| `n` | Hitung jumlah kecocokan tanpa mengganti teksnya |

Contoh kasus:

```vim
:%s/console.log/logger.info/g
:%s/\<user\>/customer/gc
:%s/foo/bar/gn
```

Karakter spesial saat mengganti teks:

| Simbol | Arti |
|---|---|
| `&` | Seluruh teks yang cocok |
| `\1`, `\2` | Capture group hasil regex pencarian |
| `\r` | Membuat baris baru (newline) pada teks pengganti |
| `\=` | Menggunakan ekspresi evaluasi teks pengganti |

Contoh pakai capture group:

```vim
:%s/\(first\)_\(name\)/\1Name/g
```

---

## Perintah Global (`:g`)

Perintah global menjalankan perintah Ex pada semua baris yang cocok dengan pola pencarian.

```vim
:g/pola/perintah
```

Contoh:

```vim
:g/TODO/p          " cetak semua baris yang ada tulisan TODO
:g/TODO/d          " hapus semua baris yang ada tulisan TODO
:g/^$/d            " hapus semua baris kosong
:g/import/s/foo/bar/g
:v/TODO/d          " hapus semua baris yang GAK ada tulisan TODO
```

Contoh pola praktis:

```vim
:g/^import/normal A;      " tambahkan titik koma di akhir semua baris import
:g/console.log/d          " hapus semua baris yang berisi console.log
:g/TODO/t$                " salin semua baris TODO ke bagian paling bawah file
```

---

## File dan Buffer

| Perintah | Aksi |
|---|---|
| `:e file` | Edit / buka file baru |
| `:edit file` | Edit / buka file baru |
| `:w` | Simpan file (write) |
| `:w nama_file` | Simpan sebagai nama file baru |
| `:wa` | Simpan semua buffer aktif (write all) |
| `:q` | Keluar (quit) |
| `:q!` | Keluar paksa tanpa menyimpan perubahan |
| `:wq` | Simpan perubahan lalu keluar |
| `:x` | Simpan jika ada perubahan, lalu keluar |
| `ZZ` | Simpan jika ada perubahan, lalu keluar (mode Normal) |
| `ZQ` | Keluar tanpa simpan (mode Normal) |
| `:r file` | Baca isi file lain dan masukkan ke baris aktif |
| `:r !cmd` | Jalankan perintah bash lalu masukkan hasilnya ke baris aktif |

Contoh penggunaan:

```vim
:e src/index.ts
:w
:q
:wq
:r package.json
:r !date
```

---

## Buffer

Buffer adalah file yang sedang terbuka di memori.

| Perintah | Aksi |
|---|---|
| `:ls` or `:buffers` | Tampilkan daftar buffer yang terbuka |
| `:bnext` or `:bn` | Pindah ke buffer berikutnya |
| `:bprevious` or `:bp` | Pindah ke buffer sebelumnya |
| `:buffer 3` or `:b 3` | Pindah ke buffer nomor 3 |
| `:b nama_file` | Pindah ke buffer berdasarkan nama filenya |
| `:bd` | Tutup buffer aktif (buffer delete) |
| `:bd!` | Tutup paksa buffer aktif |
| `:bufdo command` | Jalankan perintah ke semua buffer yang terbuka |

Shortcut mapping yang direkomendasikan di config:

```vim
nnoremap <leader>bn :bnext<CR>
nnoremap <leader>bp :bprevious<CR>
nnoremap <leader>bd :bd<CR>
```

Di Neovim menggunakan Lua:

```lua
vim.keymap.set("n", "<leader>bn", ":bnext<CR>")
vim.keymap.set("n", "<leader>bp", ":bprevious<CR>")
vim.keymap.set("n", "<leader>bd", ":bd<CR>")
```

---

## Windows / Splits

Window adalah area visual untuk melihat isi buffer.

| Perintah | Aksi |
|---|---|
| `:split` or `:sp` | Bagi layar secara horizontal |
| `:vsplit` or `:vsp` | Bagi layar secara vertikal |
| `Ctrl-w s` | Bagi layar secara horizontal |
| `Ctrl-w v` | Bagi layar secara vertikal |
| `Ctrl-w h` | Pindah fokus ke split sebelah kiri |
| `Ctrl-w j` | Pindah fokus ke split bagian bawah |
| `Ctrl-w k` | Pindah fokus ke split bagian atas |
| `Ctrl-w l` | Pindah fokus ke split sebelah kanan |
| `Ctrl-w w` | Pindah fokus antar split secara bergantian |
| `Ctrl-w q` | Tutup split yang aktif saat ini |
| `Ctrl-w o` | Tutup split lainnya, sisakan split aktif (only) |
| `Ctrl-w =` | Buat ukuran semua split sama rata |
| `Ctrl-w _` | Maksimalkan tinggi split aktif |
| `Ctrl-w |` | Maksimalkan lebar split aktif |
| `Ctrl-w +` | Perbesar tinggi split aktif |
| `Ctrl-w -` | Perkecil tinggi split aktif |
| `Ctrl-w >` | Perlebar split aktif |
| `Ctrl-w <` | Persempit split aktif |

Membuka file langsung di split baru:

```vim
:sp src/index.ts
:vsp src/index.ts
```

---

## Tab

Di Vim, tab itu kumpulan susunan window, bukan file tab seperti pada IDE modern.

| Perintah | Aksi |
|---|---|
| `:tabnew` | Buka tab baru |
| `:tabnew file` | Buka file di tab baru |
| `:tabnext` or `gt` | Pindah ke tab berikutnya |
| `:tabprevious` or `gT` | Pindah ke tab sebelumnya |
| `:tabclose` | Tutup tab aktif |
| `:tabonly` | Tutup tab lainnya |
| `:tabs` | Daftar semua tab yang terbuka |

---

## Mark

Mark membantumu menandai posisi penting agar bisa lompat ke sana lagi dengan cepat.

| Tombol | Aksi |
|---|---|
| `ma` | Tandai posisi saat ini sebagai mark lokal `a` |
| `` `a `` | Lompat tepat ke posisi mark `a` |
| `'a` | Lompat ke awal baris mark `a` |
| `mA` | Tandai posisi saat ini sebagai mark global `A` |
| `` `A `` | Lompat ke mark global `A` (bisa antar file) |
| `:marks` | Tampilkan semua daftar mark |
| `` `. `` | Lompat ke lokasi perubahan terakhir |
| `` `\" `` | Lompat ke posisi kursor terakhir saat file ditutup |
| `` `[ `` | Lompat ke awal teks yang terakhir diubah/disalin |
| `` `] `` | Lompat ke akhir teks yang terakhir diubah/disalin |

Contoh penggunaan:

```vim
ma      " simpan posisi saat ini ke mark a
G       " lompat ke baris paling bawah file
`a      " lompat balik ke posisi awal tadi secara presisi
```

---

## Jumps dan List Perubahan (Change List)

| Tombol | Aksi |
|---|---|
| `Ctrl-o` | Lompat mundur di daftar riwayat lompatan (jump list) |
| `Ctrl-i` | Lompat maju di daftar riwayat lompatan |
| `:jumps` | Tampilkan riwayat lompatan |
| `g;` | Lompat mundur ke riwayat perubahan teks sebelumnya |
| `g,` | Lompat maju ke riwayat perubahan teks berikutnya |
| `:changes` | Tampilkan riwayat perubahan teks |

Berguna banget setelah kamu selesai nyari teks, cari definisi fungsi, atau pindah-pindah file.

---

## Macro

Macro digunakan untuk merekam serangkaian aksi lalu memutarnya kembali secara otomatis.

| Tombol | Aksi |
|---|---|
| `qa` | Mulai merekam ke register `a` |
| `q` | Berhenti merekam |
| `@a` | Jalankan macro dari register `a` |
| `@@` | Jalankan ulang macro yang terakhir kali dipakai |
| `10@a` | Jalankan macro `a` sebanyak 10 kali |

Contoh kasus: menambahkan titik koma `;` di akhir baris berurutan.

1. Taruh kursor di baris pertama.
2. Rekam macro:

```vim
qaA;<Esc>jq
```

3. Jalankan 10 kali ke baris berikutnya:

```vim
10@a
```

Penjelasan langkah perekaman:

| Langkah | Arti |
|---|---|
| `qa` | Mulai rekam ke register `a` |
| `A;` | Masuk ke akhir baris lalu ketik `;` |
| `<Esc>` | Kembali ke mode Normal |
| `j` | Pindah ke baris di bawahnya |
| `q` | Berhenti merekam |

---

## Folds (Melipat Teks)

Folds digunakan untuk menyembunyikan/melipat bagian teks biar kode terlihat lebih rapi.

| Tombol | Aksi |
|---|---|
| `za` | Toggle lipatan (buka/tutup lipatan) |
| `zo` | Buka lipatan (open) |
| `zc` | Tutup lipatan (close) |
| `zR` | Buka seluruh lipatan di file (open all) |
| `zM` | Tutup seluruh lipatan di file (close all) |
| `zj` | Pindah ke lipatan berikutnya |
| `zk` | Pindah ke lipatan sebelumnya |

Mengatur metode pelipatan (fold method):

```vim
:set foldmethod=indent
:set foldmethod=syntax
:set foldmethod=marker
```

Contoh metode marker:

```js
// {{{ Helper Auth
function login() {}
function logout() {}
// }}}
```

---

## Quickfix dan Location List

Quickfix berguna untuk melacak error se-project, hasil pencarian grep, atau output compiler.

| Perintah | Aksi |
|---|---|
| `:copen` | Buka jendela quickfix list |
| `:cclose` | Tutup jendela quickfix list |
| `:cnext` or `:cn` | Pindah ke item quickfix berikutnya |
| `:cprevious` or `:cp` | Pindah ke item quickfix sebelumnya |
| `:cfirst` | Pindah ke item quickfix pertama |
| `:clast` | Pindah ke item quickfix terakhir |
| `:colder` | Tampilkan daftar quickfix yang lebih lama |
| `:cnewer` | Tampilkan daftar quickfix yang lebih baru |

Location list mirip quickfix tapi bersifat lokal per window:

| Perintah | Aksi |
|---|---|
| `:lopen` | Buka location list |
| `:lclose` | Tutup location list |
| `:lnext` | Pindah ke item location list berikutnya |
| `:lprevious` | Pindah ke item location list sebelumnya |

Mencari di dalam project dengan grep bawaan:

```vim
:grep TODO **/*.ts
:copen
```

Kalau pakai ripgrep:

```vim
:set grepprg=rg\ --vimgrep
:grep TODO
:copen
```

---

## Mode Diff (Komparasi File)

| Perintah | Aksi |
|---|---|
| `vimdiff file1 file2` | Buka file langsung dalam mode komparasi |
| `:diffsplit file` | Bandingkan file aktif dengan file lain |
| `]c` | Lompat ke perbedaan berikutnya |
| `[c` | Lompat ke perbedaan sebelumnya |
| `do` | Ambil perubahan dari split sebelah (diff obtain) |
| `dp` | Terapkan perubahan aktif ke split sebelah (diff put) |
| `:diffupdate` | Refresh tampilan komparasi |
| `:diffoff` | Matikan mode komparasi |

---

## Pemeriksaan Ejaan (Spell Checking)

| Perintah | Aksi |
|---|---|
| `:set spell` | Aktifkan spell check |
| `:set nospell` | Matikan spell check |
| `]s` | Lompat ke ejaan salah berikutnya |
| `[s` | Lompat ke ejaan salah sebelumnya |
| `z=` | Tampilkan saran ejaan |
| `zg` | Masukkan kata ke kamus (good word) |
| `zw` | Tandai kata sebagai salah (wrong word) |
| `zug` | Batalkan aksi `zg` |

Mengatur bahasa spell check:

```vim
:set spelllang=en_us
```

---

## Formatting Teks / Kode

| Tombol | Aksi |
|---|---|
| `=` | Operator auto-indent |
| `==` | Auto-indent baris aktif |
| `gg=G` | Auto-indent seluruh isi file |
| `gq` | Format bungkus teks sesuai lebar batas kolom |
| `gqap` | Format bungkus teks untuk seluruh paragraf aktif |

Mengatur lebar batas kolom:

```vim
:set textwidth=80
```

Format teks yang diblok saja:

```vim
v blok teksnya
=
```

Format menggunakan LSP Neovim:

```vim
:lua vim.lsp.buf.format()
```

Rekomendasi mapping di config:

```lua
vim.keymap.set("n", "<leader>f", function()
  vim.lsp.buf.format({ async = true })
end)
```

---

## Terminal Bawaan

### Membuka terminal di dalam Vim / Neovim

| Perintah | Aksi |
|---|---|
| `:terminal` | Buka jendela terminal baru |
| `:term` | Buka jendela terminal baru |
| `i` | Masuk ke mode ketik di terminal |
| `Ctrl-\\ Ctrl-n` | Keluar dari mode ketik terminal (kembali ke mode Normal) |
| `:bd!` | Tutup paksa buffer terminal |

Membuka terminal langsung dengan split layar:

```vim
:split | terminal
:vsplit | terminal
```

Contoh mapping di Neovim Lua agar keluar terminal lebih gampang dengan `Esc`:

```lua
vim.keymap.set("t", "<Esc>", [[<C-\\><C-n>]])
```

---

## Neovim LSP (Language Server Protocol)

Neovim sudah punya dukungan LSP bawaan. Walaupun konfigurasinya bisa berbeda-beda tiap user, berikut perintah umum yang sering dipakai.

| Aksi | Perintah Lua |
|---|---|
| Lompat ke definisi fungsi | `vim.lsp.buf.definition()` |
| Lompat ke deklarasi fungsi | `vim.lsp.buf.declaration()` |
| Lompat ke implementasi | `vim.lsp.buf.implementation()` |
| Lompat ke definisi tipe data | `vim.lsp.buf.type_definition()` |
| Tampilkan dokumentasi hover | `vim.lsp.buf.hover()` |
| Bantuan parameter fungsi | `vim.lsp.buf.signature_help()` |
| Ubah nama variabel se-project | `vim.lsp.buf.rename()` |
| Jalankan Code Action | `vim.lsp.buf.code_action()` |
| Format kode | `vim.lsp.buf.format()` |
| Lihat daftar referensi variabel | `vim.lsp.buf.references()` |

Mapping tombol yang umum dipakai:

```lua
vim.keymap.set("n", "gd", vim.lsp.buf.definition)
vim.keymap.set("n", "gD", vim.lsp.buf.declaration)
vim.keymap.set("n", "gi", vim.lsp.buf.implementation)
vim.keymap.set("n", "gr", vim.lsp.buf.references)
vim.keymap.set("n", "K", vim.lsp.buf.hover)
vim.keymap.set("n", "<leader>rn", vim.lsp.buf.rename)
vim.keymap.set("n", "<leader>ca", vim.lsp.buf.code_action)
vim.keymap.set("n", "<leader>f", function()
  vim.lsp.buf.format({ async = true })
end)
```

---

## Diagnostics Neovim (Error / Warning)

| Aksi | Perintah Lua |
|---|---|
| Buka jendela diagnostik detail | `vim.diagnostic.open_float()` |
| Lompat ke diagnostik berikutnya | `vim.diagnostic.goto_next()` |
| Lompat ke diagnostik sebelumnya | `vim.diagnostic.goto_prev()` |
| Kirim info diagnostik ke location list | `vim.diagnostic.setloclist()` |

Mapping tombol yang umum dipakai:

```lua
vim.keymap.set("n", "<leader>e", vim.diagnostic.open_float)
vim.keymap.set("n", "[d", vim.diagnostic.goto_prev)
vim.keymap.set("n", "]d", vim.diagnostic.goto_next)
vim.keymap.set("n", "<leader>q", vim.diagnostic.setloclist)
```

---

## Perintah Ex yang Sering Berguna

| Perintah | Aksi |
|---|---|
| `:set number` | Tampilkan nomor baris |
| `:set relativenumber` | Tampilkan nomor baris relatif |
| `:set nonumber` | Sembunyikan nomor baris |
| `:set wrap` | Aktifkan line wrap (teks otomatis melipat ke baris baru di layar) |
| `:set nowrap` | Matikan line wrap |
| `:set list` | Tampilkan karakter tak terlihat (spasi, tab, newline) |
| `:set nolist` | Sembunyikan karakter tak terlihat |
| `:set paste` | Aktifkan mode tempel teks (berguna untuk Vim jadul agar format rapi) |
| `:set nopaste` | Matikan mode tempel teks |
| `:syntax on` | Aktifkan pewarnaan kode (syntax highlighting) |
| `:filetype plugin indent on` | Aktifkan deteksi jenis file dan aturan indentasinya |
| `:checkhealth` | Jalankan cek kesehatan konfigurasi Neovim |
| `:messages` | Tampilkan pesan log sistem |
| `:version` | Lihat informasi versi Vim/Neovim |
| `:h topik` | Buka bantuan dokumentasi tentang topik tersebut |
| `:h key-notation` | Lihat format tombol konfigurasi di dokumentasi |

Contoh membuka dokumentasi bantuan:

```vim
:h motion.txt
:h text-objects
:h registers
:h :substitute
:h vim.lsp.buf
```

---

## Workflow Praktis Sehari-hari

### 1. Rename variabel dalam satu file

```vim
:%s/namaLama/namaBaru/gc
```

*Pakai flag `c` biar kamu bisa memverifikasi perubahannya satu per satu.*

---

### 2. Hapus semua log debugging (console.log)

```vim
:g/console.log/d
```

---

### 3. Salin teks di dalam tanda kutip

```vim
yi"
```

---

### 4. Ganti semua isi di dalam kurung kurawal

```vim
ci{
```

---

### 5. Lompat ke suatu karakter dengan cepat

```vim
f{
```

*Lompat ke depan tepat pada posisi `{` di baris yang sama.*

```vim
t{
```

*Lompat ke depan tepat satu karakter sebelum `{`.*

---

### 6. Ganti isi argumen sebuah fungsi

Contoh baris kode:

```js
sendEmail(user, subject, body)
```

Taruh kursor di dalam tanda kurung fungsi tersebut, lalu jalankan:

```vim
ci(
```

Hasilnya:

```js
sendEmail(|)
```

*Kursor kamu otomatis berada di dalam tanda kurung dan langsung siap mengetik.*

---

### 7. Blok teks di dalam tag HTML

```html
<div class="card">
  Hello world
</div>
```

Taruh kursor di dalam teks isi tag, lalu ketik:

```vim
vit
```

Setelah terblok, kamu bisa melakukan:

```vim
d       " hapus area blok tersebut
c       " ubah isi area blok tersebut
y       " salin area blok tersebut
```

---

### 8. Perbaiki indentasi satu file penuh

```vim
gg=G
```

*Untuk Neovim yang sudah terpasang LSP:*

```vim
:lua vim.lsp.buf.format()
```

---

### 9. Rekam perubahan berulang menggunakan Macro

```vim
qaA;<Esc>jq
10@a
```

*Ini bakal menambahkan karakter `;` di akhir 10 baris ke bawah.*

---

### 10. Hapus teks tanpa merusak isi clipboard utama

```vim
"_dd
```

*Ini membuang baris teks ke register lubang hitam (black hole register).*

---

## Rencana Latihan 7 Hari

### Hari 1: Navigasi Dasar

Latihan tombol berikut:

```vim
h j k l
w b e
0 ^ $
gg G
Ctrl-d Ctrl-u
```

**Target:** Navigasi file tanpa menyentuh tombol panah (arrow keys) atau mouse.

---

### Hari 2: Operator Dasar

Latihan kombinasi berikut:

```vim
dw
dd
cw
ciw
yy
p
u
Ctrl-r
```

**Target:** Edit teks menggunakan rumus `operator + motion`.

---

### Hari 3: Karakter Search

Latihan tombol pencarian satu baris:

```vim
f{
t{
f,
t)
;
,
```

**Target:** Lompat secara instan di dalam baris kode.

---

### Hari 4: Text Object

Latihan manipulasi blok:

```vim
ci"
ci'
ci(
ci{
dap
yiw
```

**Target:** Edit kode berdasarkan struktur logisnya, bukan karakter per karakter.

---

### Hari 5: Buffer, Split, dan Pencarian File

Latihan manajemen file:

```vim
:e nama_file
:ls
:bn
:bp
:vsp
Ctrl-w h/j/k/l
/teks
n
N
```

**Target:** Pindah-pindah file dan mengelola split window dengan lancar.

---

### Hari 6: Cari dan Ganti Teks

Latihan pengeditan massal:

```vim
:%s/lama/baru/gc
:g/pola_teks/d
:noh
```

**Target:** Beres-beres kode kotor atau refactoring file dengan cepat.

---

### Hari 7: Macro dan Register

Latihan otomatisasi aksi:

```vim
qa ... q
@a
@@
"+y
"+p
"_d
```

**Target:** Otomatisasi pekerjaan edit teks yang berulang-ulang.

---

## Tabel Rangkuman Singkat

| Target Aksi | Perintah |
|---|---|
| Simpan | `:w` |
| Keluar | `:q` |
| Simpan dan keluar | `:wq` |
| Keluar tanpa simpan | `:q!` |
| Lompat ke awal kata berikutnya | `w` |
| Lompat mundur ke awal kata sebelumnya | `b` |
| Lompat ke akhir baris | `$` |
| Lompat ke awal baris | `0` |
| Lompat ke karakter pertama bukan spasi | `^` |
| Pergi ke bagian paling atas file | `gg` |
| Pergi ke bagian paling bawah file | `G` |
| Cari teks | `/teks` |
| Hasil pencarian berikutnya | `n` |
| Hasil pencarian sebelumnya | `N` |
| Bersihkan highlight pencarian | `:noh` |
| Lompat tepat ke `{` | `f{` |
| Lompat sebelum `{` | `t{` |
| Hapus satu kata | `dw` |
| Ubah kata | `cw` |
| Ubah kata aktif saat ini | `ciw` |
| Ubah isi di dalam tanda kutip dua | `ci"` |
| Ubah isi di dalam kurung kurawal | `ci{` |
| Hapus satu baris | `dd` |
| Salin satu baris | `yy` |
| Tempel di bawah | `p` |
| Tempel di atas | `P` |
| Undo | `u` |
| Redo | `Ctrl-r` |
| Blok visual biasa | `v` |
| Blok visual per baris | `V` |
| Blok visual kolom (blok vertikal) | `Ctrl-v` |
| Buka file | `:e nama_file` |
| Lihat daftar buffer | `:ls` |
| Pindah ke buffer berikutnya | `:bn` |
| Pindah ke buffer sebelumnya | `:bp` |
| Layar split vertikal | `:vsp` |
| Layar split horizontal | `:sp` |
| Pindah antar split layar | `Ctrl-w h/j/k/l` |
| Rekam macro ke register `a` | `qa` |
| Stop rekaman macro | `q` |
| Putar/jalankan macro `a` | `@a` |
| Ulangi aksi pengeditan terakhir | `.` |

---

## Catatan Penutup

Beberapa perintah yang paling sering memberikan peningkatan produktivitas yang drastis adalah:

```vim
f{      " lompat tepat ke suatu karakter
ciw     " ubah isi kata tempat kursor berada
ci"     " ubah isi teks di dalam kutip dua
ci{     " ubah isi teks di dalam kurung kurawal
.       " ulangi aksi pengeditan terakhir
:%s     " cari dan ganti teks massal
:g      " jalankan perintah Ex pada baris yang cocok
qa/@a   " rekam dan putar ulang macro otomatis
```

Kalau kamu masih baru belajar Vim atau Neovim, coba terapkan aturan harian sederhana ini:

> Tiap kali kamu reflek mau menggerakkan mouse atau tombol panah keyboard, berhenti sejenak, lalu cari motion Vim apa yang bisa menggantikan aksi tersebut.
