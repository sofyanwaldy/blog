+++
title = "Ketergantungan Preprocessor CSS `Sass` itu bikin mumet kemudian."
date = 2024-07-20T01:31:20+07:00
draft = false
+++

Bagi front-end developer mungkin sudah tidak asing lagi dengan preprocessor css yang akan aku bahasa kali ini yaitu `sass`.
tapi untuk yang belum familiar bisa lihat sendiri didokumentasi [Sass](https://sass-lang.com/). Jadi singkatnya kalian bisa menulis syntax css dengan fitur tambahan yang disediakan oleh `sass`, serperti nested, variables, modules, dan sebagainya. Namun yang namanya ketergantungan akan membuatmu candu, mau dilepaskan susah mau diteruskan gelisah.

Note: ini pengalamaan pribadi ya, bisa jadi tidak semua developer relate dengan masalah yang ku alami...

## Awal Mula Ngicipin

Pada awal aku bikin project aplikasi front-end berbasis javascript yang menggunakan `sass` rasanya sangat nyaman cepat apalagi banyak front-end framework menawarkan penggunaan sass pada saat setup projectnya seperti vuejs dan reactjs, Lalu mulailah terlena menggunakan syntax-syntax dari sass itu sendiri.

## Masalah `LibSass`

Setelah cukup lama menggunakan `sass` pasti kalian akan menemukan bahwa sass dibangun dengan `libSass` sebagai core nya, yang mana `LibSass` itu ditulis dengan `c/c++`. Jadi ketika kalian menginstall `node-sass` maka dibackground `npm` akan memanggil `node-gyp` untuk compile `libSass` dan siap digunakan di `Nodej.js`. Nah sampai disini mulailah masalah-masalah akan muncul kedepannya.

Karena`node-gyp` itu menggunakan `python` untuk mengcompile native addons. jadi ketika build libSass menggunakan `node-gyp` ada kemungkinan akan memunculkan error apa bila environment pythonnya itu sendiri berbeda, seperti `distutils` module yang dihapus mulai dari `python3.12` dan setelahnya.

Jadi apabila kalian menemukan error pada saat install node-sass kalian bisa coba cek python version kalian `python --version` apabila >= 3.12.x maka kalian harus menurunkannya kalau aku sendiri pakai python 3.9.6.
setelah menginstall python versi yang lebih rendah lalu tambahkan `export PYTHON=/path/to/python3.9.6` di `~/.bashrc` atau `~/.zshrc` lalu update source terminalnya `source ~/.zshrc` atau `source ~/.bashrc`.

## `LibSass` Deprecated

Masalah selanjutnya yang aku temuin adalah `LibSass` deprecated, dan sass menyarakan menggantinya dengan `dart-sass`. lihat selengkapnya di [LibSass](https://sass-lang.com/libsass/).

## `dart-sass` di NPM deprecated

ketika kalian mencari dart-sass di `NPM` kalian akan menemukan kalau library ini juga deprecated [Dart-Sass](https://www.npmjs.com/package/dart-sass). Dan ini cukup membuat bingung developer yang sebenarnya dart-sass itu sendiri sebagai core tidak deprecated namun package `dart-sass` di npm lah yang deprecated dan direname menjadi `sass`.

## `npm install sass`

setelah cukup dibingunkan dengan libSass dan dart-sass aku mencoba install `npm install sass`.
Lalu kemudian terjadilah kekacauan di projectku, karena syntax sass yang baru ternyata banyak yang tidak backward-compatible.
dan akupun dilema apa harus dilanjutkan pakai yang deprecated LibSass atau upgrade ke `sass`? namun codebase yang sudah banyak pastinya diperlukan waktu yang lama untuk refactoring dan memperbaiki syntax sass yang ada.

## Kesimpulan

Menjadi ketergantungan terhadap dependencies itu memang tidak selamanya nikmat.
Jadi menurutku ada sebaiknya untuk kedepannya mengurangi dependencies yang tidak perlu khususnya preprocessor css. karena CSS itu sendiri sudah cukup stabil dan konsisten untuk pengembangan syntaxnya.
