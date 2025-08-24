# 🚀 Code Node.js Berantakan? Ini Cara Biar Rapi Pakai ESLint + Prettier di TypeScript / Express

Sebagai software developer yang terbiasa dengan JavaScript, kamu mungkin sering mengalami masalah seperti:

- Penulisan kode tidak konsisten
- Nama variabel berantakan
- Lupa titik koma (`;`)
- Gaya indentasi beda-beda

Hal ini wajar karena JavaScript memang fleksibel. Tapi kalau proyekmu sudah mulai **besar** dan melibatkan banyak developer, **konsistensi penulisan kode jadi krusial**.

Solusinya: gunakan **ESLint**, **Prettier**, **Husky**, dan **Lint-Staged**.
Dengan ini, kamu bisa memastikan kode tetap rapi, mudah dibaca, dan konsisten di seluruh tim.

---

## ⚡ QUICK START — Setup Otomatis

Kalau kamu cuma mau langsung pakai tanpa baca panjang-panjang, ikuti langkah di bawah ini:

```bash
# 1. Install semua dependency
npm install --save-dev eslint@latest @eslint/js@latest prettier husky lint-staged

# 2. Buat file konfigurasi ESLint
touch eslint.config.js
```

📄 **Isi `eslint.config.js`:**

```js
import { defineConfig } from "eslint/config";
import js from "@eslint/js";

export default defineConfig([
  {
    files: ["**/*.js"],
    plugins: { js },
    extends: ["js/recommended"],
    rules: {
      "no-unused-vars": "warn",
      "no-undef": "warn",
    },
  },
]);
```

```bash
# 3. Buat file konfigurasi Prettier
touch .prettierrc
```

📄 **Isi `.prettierrc`:**

```json
{
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": true,
  "trailingComma": "all"
}
```

```bash
# 4. Inisialisasi Husky
npx husky init
```

```bash
# 5. Edit file /.husky/pre-commit
npx lint-staged
```

📄 **Tambahkan konfigurasi Lint-Staged di `package.json`:**

```json
"lint-staged": {
  "*.{js,ts}": [
    "prettier --write",
    "eslint --cache --fix"
  ]
}
```

✅ **Selesai!** Sekarang setiap kamu melakukan `git commit`, kode akan otomatis:

- Diformat oleh Prettier
- Diperiksa oleh ESLint
- Diperbaiki secara otomatis jika bisa

---

## 📘 Penjelasan Step-by-Step

Kalau kamu ingin memahami setiap bagian lebih dalam, berikut penjelasan manualnya:

---

### 🔍 1. ESLint — Linting Kode

**Apa itu ESLint?**
ESLint adalah alat untuk mengecek apakah kode kamu sudah sesuai dengan aturan tertentu (_rules_). Misalnya: ada variabel yang tidak dipakai, lupa deklarasi, dsb.

📖 Dokumentasi resmi: [https://eslint.org](https://eslint.org/docs/latest/)

#### 📦 Instalasi

```bash
npm install --save-dev eslint@latest @eslint/js@latest
```

#### ⚙️ Konfigurasi

Buat file `eslint.config.js`:

```bash
touch eslint.config.js
```

Isi seperti ini:

```js
import { defineConfig } from "eslint/config";
import js from "@eslint/js";

export default defineConfig([
  {
    files: ["**/*.js"],
    plugins: { js },
    extends: ["js/recommended"],
    rules: {
      "no-unused-vars": "warn",
      "no-undef": "warn",
    },
  },
]);
```

📌 _Kamu bisa ubah rules sesuai kebutuhan tim/proyekmu._

#### ▶️ Menjalankan ESLint

```bash
npx eslint src/        # lint seluruh folder
npx eslint file.js     # lint file tunggal
```

---

### 🎨 2. Prettier — Format Otomatis

**Apa itu Prettier?**
Prettier adalah formatter otomatis yang mengatur gaya penulisan kode: indentasi, titik koma, kutip, dsb.

📖 Dokumentasi resmi: [https://prettier.io](https://prettier.io/docs/en/index.html)

#### 📦 Instalasi

```bash
npm install --save-dev prettier
```

#### ⚙️ Konfigurasi

Buat file `.prettierrc`:

```bash
touch .prettierrc
```

Isi seperti ini:

```json
{
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": true,
  "trailingComma": "all"
}
```

📌 _Ini adalah konfigurasi umum yang banyak dipakai di proyek TypeScript/Node.js modern._

---

### 🪝 3. Husky + Lint-Staged — Hook Git Otomatis

Agar proses linting & formatting dijalankan **sebelum commit**, kita bisa pakai **Husky** dan **Lint-Staged**.

#### 📦 Instalasi

```bash
npm install --save-dev husky lint-staged
npx husky init
```

Ini akan membuat folder `.husky/` dan file `.husky/pre-commit`.

#### ⚙️ Konfigurasi pre-commit

Edit file `.husky/pre-commit` dan tambahkan:

```bash
npx lint-staged
```

#### 🧩 Konfigurasi Lint-Staged

Tambahkan di `package.json`:

```json
"lint-staged": {
  "*.{js,ts}": [
    "prettier --write",
    "eslint --cache --fix"
  ]
}
```

Dengan ini:

- File `.js` & `.ts` yang di-_stage_ akan diformat & diperiksa sebelum commit.
- Commit akan **gagal otomatis** jika ada error yang tidak bisa diperbaiki otomatis.

---

## ✅ Kesimpulan / Highlight

- ✅ **ESLint**: memastikan kode sesuai aturan, mendeteksi error lebih awal.
- ✅ **Prettier**: membuat kode lebih rapi, konsisten, dan enak dibaca.
- ✅ **Husky + Lint-Staged**: memastikan semua kode diformat & di-lint otomatis sebelum commit.

> 🔥 Dengan setup ini, kamu bisa menjaga **kualitas kode tetap tinggi**, **review PR jadi lebih mudah**, dan **developer baru lebih cepat adaptasi gaya coding tim**.

---

### 🧠 Tips Tambahan

- Kalau kamu pakai **TypeScript**, kamu bisa install plugin tambahan seperti `@typescript-eslint/eslint-plugin` dan `@typescript-eslint/parser`.
- Untuk proyek besar, kamu juga bisa tambahkan `eslint-config-airbnb` atau `eslint-config-standard` untuk gaya linting yang lebih ketat.
