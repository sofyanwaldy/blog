---
title: "Cheatsheet Lengkap Git"
description: "Cheatsheet praktis Git untuk development sehari-hari — command, branching, merging, rebasing, stashing, logging, undo, dan workflow kolaborasi."
date: 2026-06-17
tags: ["git", "cheatsheet", "developer-tools", "version-control"]
url: /git
draft: false
---

Git itu melacak **snapshot** dari project kamu, bukan sekadar perbedaan baris kode (diff). Setiap commit adalah gambaran utuh dari seluruh project kamu pada momen tersebut. Memahami konsep ini bakal mengubah cara kamu berpikir tentang branching, merging, dan history.

Cheatsheet ini didesain buat kerjaan development sehari-hari — tipe panduan yang bakal benar-benar kamu bookmark dan buka lagi terus-menerus.

---

## Konsep Utama

Sebelum mulai menjalankan perintah-perintah Git, kamu harus paham dulu tiga area utama di Git:

```text
Working Directory  →  Staging Area (Index)  →  Repository (.git)
      ↑                     ↑                        ↑
  File kamu           Apa saja yang masuk     Riwayat commit
  di dalam disk       ke commit berikutnya    (snapshot permanen)
```

**Working Directory** adalah tempat kamu mengedit file. Ini simpelnya adalah folder project kamu di komputer.

**Staging Area** (sering disebut Index) adalah jembatan antara working directory dan repository. Di sini kamu memilih secara spesifik file mana saja yang mau dimasukkan ke commit berikutnya. Ini fitur andalan Git — kamu bisa komit *sebagian* perubahan saja tanpa harus semuanya langsung dimasukkan.

**Repository** adalah folder `.git` itu sendiri. Di sinilah semua snapshot commit, branch, tag, dan konfigurasi disimpan.

### Apa itu Commit?

Commit adalah snapshot dari seluruh project beserta metadata pendukungnya:

```text
commit 3a1f2b9
├── tree (snapshot dari semua file)
├── parent (commit sebelumnya)
├── author: Sofyan Waldy <sofyan@example.com>
├── date: 2026-06-17
└── message: "Add user authentication"
```

Setiap commit punya hash SHA-1 unik (contohnya `3a1f2b9c...`). Hash ini dihitung dari isi kontennya langsung, jadi kalau isinya sama persis, hash yang dihasilkan juga bakal selalu sama.

### Apa itu Branch?

Branch di Git itu cuma **pointer** (penunjuk) ke sebuah commit. Sesimpel itu. Bikin branch di Git itu instan banget karena Git cuma bikin file berukuran 41-byte (berisi hash + baris baru).

```text
main    → commit C
feature → commit C  (dua-duanya menunjuk ke commit yang sama sesaat setelah branching)
```

### Apa itu HEAD?

`HEAD` adalah pointer yang menunjuk ke **branch aktif saat ini** (yang mana branch tersebut menunjuk ke sebuah commit). Pas kamu pindah branch, `HEAD` bakal otomatis ikut pindah ke branch tersebut.

```text
HEAD → main → commit C
```

Kalau kamu checkout ke commit tertentu secara spesifik (bukan ke branch), kamu bakal masuk ke kondisi **detached HEAD** — artinya kamu sedang tidak berada di branch mana pun.

---

## Konfigurasi

Konfigurasi Git punya tiga level tingkatan:

| Level | Flag | File | Jangkauan (Scope) |
|-------|------|------|-------|
| System | `--system` | `/etc/gitconfig` | Semua user di komputer ini |
| Global | `--global` | `~/.gitconfig` | Akun user kamu saja |
| Local | `--local` | `.git/config` | Repositori aktif ini saja |

Local bakal menimpa global, dan global bakal menimpa system.

### Pengaturan Dasar

```bash
# Identitas (wajib — setiap commit bakal pakai info ini)
git config --global user.name "Sofyan Waldy"
git config --global user.email "sofyan@example.com"

# Nama branch default (biar ga pakai nama jadul 'master')
git config --global init.defaultBranch main

# Editor default
git config --global core.editor "nvim"

# Penanganan baris baru (penting kalau kerja tim beda OS)
git config --global core.autocrlf input    # untuk macOS/Linux
git config --global core.autocrlf true     # untuk Windows

# Aktifkan warna di output terminal
git config --global color.ui auto

# Atur default pull ke rebase (riwayat komit jadi lebih rapi dibanding merge)
git config --global pull.rebase true

# Default push hanya untuk branch aktif saat ini
git config --global push.default current

# Otomatis setup remote tracking saat pertama kali push
git config --global push.autoSetupRemote true
```

### Alias yang Bermanfaat

```bash
# Status versi singkat
git config --global alias.st "status -sb"

# Log indah dengan grafik satu baris
git config --global alias.lg "log --oneline --graph --all --decorate"

# Amend commit terakhir tanpa ubah pesannya
git config --global alias.amend "commit --amend --no-edit"

# Batalkan staging untuk semua file (unstage)
git config --global alias.unstage "reset HEAD --"

# Detail commit terakhir
git config --global alias.last "log -1 HEAD --stat"

# Tampilkan semua alias yang sudah dibuat
git config --global alias.aliases "config --get-regexp ^alias"

# Lihat perbedaan file yang sudah di-stage
git config --global alias.staged "diff --cached"

# Hapus branch lokal yang sudah di-merge (kecuali main/master dan branch aktif)
git config --global alias.cleanup "!git branch --merged | grep -v '\\*\\|main\\|master' | xargs -n 1 git branch -d"
```

### Melihat Konfigurasi

```bash
# Tampilkan semua settingan beserta asal filenya
git config --list --show-origin

# Cek settingan spesifik
git config user.email

# Edit konfigurasi global langsung di editor teks
git config --global --edit
```

---

## Workflow Dasar

### Memulai Project

```bash
# Bikin repositori baru
git init project-ku
cd project-ku

# Clone repositori yang sudah ada
git clone https://github.com/user/repo.git

# Clone ke dalam folder spesifik
git clone https://github.com/user/repo.git folder-ku

# Clone commit terakhir saja (cepat untuk repo berukuran besar)
git clone --depth 1 https://github.com/user/repo.git

# Clone branch spesifik
git clone -b develop https://github.com/user/repo.git
```

### Memasukkan File ke Staging Area (Stage)

```bash
# Masukkan file spesifik ke stage
git add README.md

# Masukkan beberapa file sekaligus
git add src/index.js src/utils.js

# Masukkan semua perubahan di folder aktif saat ini (rekursif)
git add .

# Masukkan seluruh perubahan di satu repositori penuh
git add -A

# Masukkan hanya file yang sudah dilacak (abaikan file baru yang belum dilacak)
git add -u

# Masukkan potongan baris kode secara interaktif
git add -p
```

#### Staging Interaktif dengan `git add -p`

Ini salah satu fitur Git paling keren. Git bakal nampilin potongan perubahan baris kode kamu dan nanya apa yang mau kamu lakukan:

```text
@@ -10,6 +10,8 @@ function processUser(user) {
+  validateEmail(user.email);
+  sanitizeInput(user.name);
    const result = saveUser(user);
Stage this hunk [y,n,q,a,d,s,e,?]?
```

| Tombol | Aksi |
|-----|--------|
| `y` | Masukkan potongan ini ke stage |
| `n` | Lewati potongan ini |
| `q` | Keluar (jangan masukkan sisa potongan lainnya) |
| `a` | Masukkan potongan ini dan semua sisa potongan lainnya |
| `s` | Pecah potongan kode jadi lebih kecil lagi |
| `e` | Edit potongan kode ini secara manual |

Ini berguna banget biar kamu bisa bikin satu commit yang fokus dan logis, meskipun kamu lagi ngedit banyak hal yang berbeda di dalam satu file.

### Melakukan Commit

```bash
# Commit perubahan yang ada di stage dengan pesan singkat
git commit -m "Add email validation to user signup"

# Commit dengan pesan beberapa baris
git commit -m "Add email validation" -m "Validates format and checks for disposable providers"

# Otomatis stage file yang sudah dilacak sekaligus commit dalam satu langkah
git commit -am "Fix typo in error message"

# Buka editor buat nulis pesan commit yang lebih detail
git commit

# Commit dengan nama author spesifik
git commit --author="Partner <partner@example.com>" -m "Pair programming session"

# Bikin commit kosong (berguna untuk memicu CI/CD trigger)
git commit --allow-empty -m "Trigger deployment pipeline"
```

#### Menulis Pesan Commit yang Baik

```text
Add OAuth2 authentication for API endpoints

- Implement token-based auth using JWT
- Add refresh token rotation for security
- Include rate limiting per API key
- Update API documentation with auth headers

Closes #142
```

Aturannya:
- **Baris pertama**: bentuk perintah (imperative), ≤50 karakter (seperti subjek email)
- **Baris kosong** sebagai pemisah antara subjek dan isi deskripsi
- **Isi deskripsi**: jelaskan *apa* dan *kenapa* (bukan *bagaimana* caranya — karena codenya sendiri sudah menjelaskan bagaimana caranya)

### Memeriksa Status

```bash
# Status lengkap
git status

# Status versi singkat (jauh lebih gampang dibaca)
git status -sb
```

Arti output status versi singkat:

```text
## main...origin/main [ahead 2]
  M src/utils.js          # Diedit tapi belum di-stage
 M  src/index.js          # Diedit dan sudah di-stage
 A  src/newfile.js        # File baru, sudah di-stage
 ?? docs/notes.txt        # Belum dilacak (untracked)
 MM src/config.js         # Sudah di-stage, lalu diedit lagi
```

Dua kolom di depan: kolom kiri = staging area, kolom kanan = working directory.

---

## Branching (Percabangan)

Branch di Git itu sangat ringan. Gunakan sesering mungkin. Setiap fitur baru, perbaikan bug, atau eksperimen harus punya branch-nya sendiri.

### Membuat Branch

```bash
# Bikin branch baru (tapi posisi kamu tetap di branch aktif)
git branch feature/user-auth

# Bikin branch baru dan langsung pindah ke sana
git switch -c feature/user-auth

# Sintaks lama (masih bisa dipakai)
git checkout -b feature/user-auth

# Bikin branch dari commit hash tertentu
git branch hotfix/login-crash abc1234

# Bikin branch dari tag tertentu
git branch release/v2.1 v2.0.0

# Bikin branch lokal yang melacak branch remote
git switch -c feature/api origin/feature/api
```

### Berpindah Branch

```bash
# Pindah ke branch lain
git switch main

# Pindah balik ke branch sebelumnya (seperti cd -)
git switch -

# Sintaks lama
git checkout main
```

> **Kenapa pakai `git switch` dibanding `git checkout`?** Perintah `checkout` itu terlalu banyak fungsinya — bisa pindah branch, restore file, sampai detach HEAD. Makanya di Git versi 2.23 dikenalkan `switch` (khusus urusan branch) dan `restore` (khusus urusan file) agar lebih teratur.

### Melihat Daftar Branch

```bash
# Daftar branch lokal (* menunjukkan branch aktif)
git branch

# Daftar branch remote
git branch -r

# Daftar semua branch (lokal + remote)
git branch -a

# Daftar branch beserta info commit terakhirnya
git branch -v

# Daftar branch yang sudah di-merge ke branch aktif saat ini
git branch --merged

# Daftar branch yang BELUM di-merge
git branch --no-merged

# Daftar branch yang mengandung commit hash tertentu
git branch --contains abc1234
```

### Menghapus Branch

```bash
# Hapus branch yang sudah di-merge (aman)
git branch -d feature/user-auth

# Hapus paksa branch yang belum di-merge (berpotensi kehilangan data)
git branch -D experiment/crazy-idea

# Hapus branch di server remote
git push origin --delete feature/user-auth

# Cara alternatif untuk menghapus branch remote
git push origin :feature/user-auth
```

### Mengubah Nama Branch

```bash
# Ubah nama branch aktif saat ini
git branch -m nama-baru

# Ubah nama branch lokal lain
git branch -m nama-lama nama-baru

# Ubah nama branch dan sinkronkan ke remote
git branch -m nama-lama nama-baru
git push origin --delete nama-lama
git push origin nama-baru
git push origin -u nama-baru
```

---

## Merging (Penggabungan)

Merging adalah cara menggabungkan pekerjaan dari dua branch. Git bakal otomatis menentukan strategi merge yang pas.

### Fast-Forward Merge

Kalau branch target belum punya commit baru semenjak kamu bikin branch fitur, Git tinggal memajukan pointer branch target ke depan. Tidak ada commit merge tambahan yang dibuat.

```text
Sebelum:
main:    A --- B
                \
feature:         C --- D

Setelah menjalankan git merge feature (dari branch main):
main:    A --- B --- C --- D
```

```bash
git switch main
git merge feature/user-auth
# Output: "Fast-forward"
```

### Three-Way Merge

Kalau kedua branch sama-sama punya commit baru setelah terpisah, Git bakal bikin satu **commit merge** baru yang punya dua parent.

```text
Sebelum:
main:    A --- B --- E
                \
feature:         C --- D

Setelah menjalankan git merge feature (dari branch main):
main:    A --- B --- E --- M (commit merge baru)
                \         /
feature:         C --- D
```

```bash
git switch main
git merge feature/user-auth
# Output: "Merge made by the 'ort' strategy."
```

### Opsi Merge

```bash
# Merge dengan pesan commit custom
git merge feature/user-auth -m "Merge user authentication feature"

# Paksa bikin commit merge meskipun sebenarnya bisa fast-forward
# (bagus biar riwayat branch fitur tetap kelihatan terpisah)
git merge --no-ff feature/user-auth

# Merge tapi jangan langsung di-commit (biar bisa kita review dulu hasilnya)
git merge --no-commit feature/user-auth

# Batalkan proses merge yang sedang berjalan (kalau ada konflik)
git merge --abort

# Cek apakah branch bisa di-merge dengan aman tanpa konflik sebelum melakukan merge asli
git merge --no-commit --no-ff feature/user-auth
git merge --abort   # batalkan merge uji coba tadi
```

### Menyelesaikan Konflik Merge (Merge Conflicts)

Saat Git tidak bisa menggabungkan kode otomatis, Git bakal menandai konflik di dalam file:

```text
<<<<<<< HEAD
const API_URL = "https://api.production.com";
=======
const API_URL = process.env.API_URL || "https://api.staging.com";
>>>>>>> feature/config-env
```

- `<<<<<<< HEAD`: versi dari branch kamu saat ini
- `=======`: batas pemisah
- `>>>>>>> feature/config-env`: versi dari branch yang mau kamu masukkan

**Langkah penyelesaian:**

```bash
# 1. Cek file mana saja yang konflik
git status

# 2. Buka file yang konflik dan edit manual
#    Hapus tanda pembatas konflik dan rapikan kodenya

# 3. Setelah beres, masukkan file ke staging area
git add src/config.js

# 4. Selesaikan proses merge
git commit
# Git bakal otomatis generates pesan commit merge
```

Cara cepat: pilih salah satu versi seutuhnya:

```bash
git checkout --ours src/config.js     # pakai versi branch aktif kita
git checkout --theirs src/config.js   # pakai versi branch yang mau dimasukkan
```

### Strategi Merge

```bash
# Merge tapi kalau ada konflik, otomatis pakai perubahan punya kita
git merge -X ours feature/user-auth

# Merge tapi kalau ada konflik, otomatis pakai perubahan dari branch sebelah
git merge -X theirs feature/user-auth

# Strategi "Ours" — anggap merge selesai tapi buang semua perubahan dari branch sebelah
# (berbeda dari -X ours — ini mengabaikan SEMUA perubahan, bukan cuma yang konflik)
git merge -s ours feature/deprecated-api
```

---

## Rebasing

Rebasing itu memindahkan dasar (base) branch kamu ke commit paling ujung dari branch lain, membuat riwayat commit jadi lurus (linear). Ini bakal menulis ulang hash commit.

### Rebase Standar

```text
Sebelum:
main:    A --- B --- E
                \
feature:         C --- D

Setelah menjalankan git rebase main (dari branch feature):
main:    A --- B --- E
                      \
feature:               C' --- D'  (commit baru dengan perubahan yang sama)
```

```bash
# Dari branch fitur kamu
git switch feature/user-auth
git rebase main
```

**C' dan D'** adalah *commit baru* — isinya sama tapi hash commit-nya berubah karena parent-nya sudah berganti. Commit C dan D lama bakal ditinggalkan (dan otomatis dibersihkan oleh sistem berkala).

### Interactive Rebase (Rebase Interaktif)

Rebase interaktif membolehkan kamu edit, gabungkan (squash), susun ulang, atau hapus commit. Ini cara terbaik merapikan history commit yang berantakan sebelum di-merge ke main.

```bash
# Lakukan rebase interaktif untuk 4 commit terakhir
git rebase -i HEAD~4

# Lakukan rebase interaktif dari titik awal pisah dengan branch main
git rebase -i main
```

Git bakal membuka editor dengan teks seperti ini:

```text
pick a1b2c3d Add user model
pick e4f5g6h Add user controller
pick i7j8k9l Fix typo in user model
pick m0n1o2p Add user validation

# Perintah:
# p, pick   = gunakan commit ini
# r, reword = gunakan commit ini, tapi edit pesan commit-nya
# e, edit   = gunakan commit ini, tapi berhenti sejenak untuk amend
# s, squash = gabungkan commit ini ke commit di atasnya
# f, fixup  = mirip "squash", tapi buang pesan commit ini (pakai pesan commit atasnya)
# d, drop   = hapus commit ini
# x, exec   = jalankan perintah shell
```

#### Skenario Rebase Interaktif yang Sering Dipakai

**Menggabungkan commit "fix typo" ke commit aslinya:**

```text
pick a1b2c3d Add user model
fixup i7j8k9l Fix typo in user model     ← diubah dari pick ke fixup
pick e4f5g6h Add user controller
pick m0n1o2p Add user validation
```

**Mengubah pesan commit lama:**

```text
reword a1b2c3d Add user model             ← diubah dari pick ke reword
pick e4f5g6h Add user controller
pick m0n1o2p Add user validation
```

**Menyusun ulang urutan commit:**

```text
pick m0n1o2p Add user validation           ← dipindahkan ke atas
pick a1b2c3d Add user model
pick e4f5g6h Add user controller
```

**Menghapus commit seutuhnya:**

```text
pick a1b2c3d Add user model
drop e4f5g6h Add user controller           ← commit ini bakal hilang
pick m0n1o2p Add user validation
```

### Menyelesaikan Konflik saat Rebase

```bash
# Kalau ada konflik pas rebase, beresin dulu kodenya lalu:
git add src/conflicted-file.js
git rebase --continue

# Lewati commit ini
git rebase --skip

# Batalkan proses rebase dan balikkan ke kondisi sebelum mulai rebase
git rebase --abort
```

### Rebase vs Merge

| Aspek | Merge | Rebase |
|--------|-------|--------|
| Riwayat (History) | Menjaga riwayat asli apa adanya dengan commit merge | Bikin riwayat lurus dan bersih |
| Hash Commit | Tetap sama | Ditulis ulang (bikin hash baru) |
| Konflik | Cukup selesaikan sekali | Bisa jadi harus menyelesaikan per commit |
| Branch Bersama | Aman | **Bahaya** — jangan pernah rebase branch yang dipakai bersama |
| Kegunaan | Integrasi akhir | Merapikan riwayat sebelum digabung |

**Aturan Emas**: Jangan pernah melakukan rebase pada commit yang sudah kamu push ke repositori bersama. Kamu bakal merusak riwayat kerjaan tim lain yang mengambil update dari branch tersebut.

```bash
# Aman: rebase branch fitur lokal kamu ke main
git switch feature/my-work
git rebase main

# BAHAYA: rebase branch main setelah orang lain mengambil update dari sana
git switch main
git rebase feature/something   # JANGAN LAKUKAN INI
```

### Workflow Autosquash

Cara super rapi untuk memperbaiki commit yang sudah lalu:

```bash
# Kamu sadar commit abc1234 ada bug. Bikin commit perbaikan khusus (fixup):
git commit --fixup=abc1234

# Nanti, gabungkan semua commit perbaikan otomatis ke commit targetnya:
git rebase -i --autosquash main
# Git bakal otomatis menandai perintah commit perbaikan dengan status "fixup"
```

---

## Remote Repository (Repositori Server)

### Mengelola Remote

```bash
# Tampilkan daftar remote
git remote -v

# Tambah remote baru
git remote add origin https://github.com/user/repo.git

# Tambah remote sekunder (misal repositori utama/hulu)
git remote add upstream https://github.com/original/repo.git

# Ubah nama panggilan remote
git remote rename origin github

# Hapus remote
git remote remove upstream

# Ubah URL remote (misal dari HTTPS ke SSH)
git remote set-url origin git@github.com:user/repo.git

# Tampilkan info detail tentang remote tertentu
git remote show origin
```

### Fetch, Pull, Push

**Fetch** mendownload perubahan dari server tapi tidak langsung menggabungkannya ke file aktif kamu:

```bash
# Fetch dari remote default (origin)
git fetch

# Fetch dari remote spesifik
git fetch upstream

# Fetch branch spesifik saja
git fetch origin main

# Fetch dari semua remote
git fetch --all

# Fetch sekaligus bersihkan branch remote yang sudah dihapus di server
git fetch --prune
```

**Pull** itu gabungan dari fetch + merge (atau fetch + rebase kalau diatur begitu):

```bash
# Pull branch aktif saat ini
git pull

# Pull dengan rebase alih-alih merge (riwayat commit lebih rapi)
git pull --rebase

# Pull dari branch spesifik
git pull origin main

# Pull sekaligus otomatis simpan sementara perubahan lokal (autostash)
git pull --autostash
```

**Push** mengunggah commit kamu ke server:

```bash
# Push branch aktif saat ini
git push

# Push dan atur default tracking untuk pertama kali
git push -u origin feature/user-auth

# Push semua branch sekaligus
git push --all

# Push semua tag
git push --tags

# Push paksa (BAHAYA — menimpa riwayat di server)
git push --force

# Push paksa dengan verifikasi (jauh lebih aman — bakal gagal kalau server punya update baru dari orang lain)
git push --force-with-lease

# Push branch lokal ke nama branch remote yang berbeda
git push origin branch-lokal:branch-remote
```

> **Selalu biasakan pakai `--force-with-lease` daripada `--force`**. Opsi ini memastikan remote server tidak punya update baru dari orang lain sejak fetch terakhirmu. Jadi kamu tidak sengaja menimpa kerjaan teman satu tim.

### Melacak Branch (Tracking Branches)

```bash
# Atur tracking upstream untuk branch aktif saat ini
git branch --set-upstream-to=origin/main

# Lihat info relasi tracking antar branch
git branch -vv

# Contoh output:
#   feature/auth  abc1234 [origin/feature/auth: ahead 2] Add JWT support
#   main          def5678 [origin/main] Update README
# * develop       ghi9012 [origin/develop: behind 3] Refactor API
```

`ahead 2` berarti kamu punya 2 commit lokal yang belum di-push. `behind 3` berarti server punya 3 commit baru yang belum kamu ambil (pull).

### Bekerja dengan Repositori Fork

```bash
# 1. Fork repositori di GitHub, lalu clone hasil fork kamu sendiri
git clone https://github.com/akun-kamu/repo.git

# 2. Daftarkan repositori asli sebagai remote bernama "upstream"
git remote add upstream https://github.com/original/repo.git

# 3. Cara menyinkronkan fork kamu agar tetap update:
git fetch upstream
git switch main
git merge upstream/main
git push origin main

# 4. Bikin branch baru untuk mulai berkontribusi
git switch -c feature/kontribusi-saya
```

---

## Stashing (Penyimpanan Sementara)

Stash menyimpan perubahan kode yang belum di-commit untuk sementara waktu — mirip seperti menyimpan barang di laci meja kerja agar meja bersih dulu, nanti tinggal diambil lagi.

### Perintah Dasar Stash

```bash
# Simpan perubahan sementara (hanya file yang sudah dilacak)
git stash

# Simpan stash disertai deskripsi pesan agar mudah diingat
git stash push -m "WIP: login form setengah jalan"

# Simpan stash termasuk file baru yang belum dilacak (untracked)
git stash -u

# Simpan semuanya, termasuk file yang diabaikan (.gitignore)
git stash -a

# Simpan stash untuk file spesifik saja
git stash push -m "Simpan settingan config" src/config.js src/env.js

# Pilih potongan mana saja yang mau disimpan ke stash secara interaktif
git stash -p
```

### Mengambil Kembali Stash

```bash
# Terapkan stash terakhir (tapi file stash tetap disimpan di daftar)
git stash apply

# Terapkan stash terakhir dan hapus dari daftar stash
git stash pop

# Terapkan stash nomor tertentu
git stash apply stash@{2}

# Terapkan dan hapus stash nomor tertentu
git stash pop stash@{1}
```

### Mengelola Stash

```bash
# Lihat daftar semua stash yang tersimpan
git stash list
# Contoh output:
# stash@{0}: On feature/auth: WIP login form setengah jalan
# stash@{1}: On main: Perbaikan cepat coba-coba
# stash@{2}: WIP on develop: abc1234 Add API routes

# Tampilkan perbedaan isi file di dalam stash
git stash show
git stash show -p              # tampilkan detail kode yang berbeda
git stash show stash@{2} -p   # tampilkan detail kode stash tertentu

# Hapus salah satu stash dari daftar
git stash drop stash@{1}

# Hapus SEMUA stash (hati-hati, ini permanen!)
git stash clear

# Bikin branch baru dari isi stash (sangat berguna kalau stash bentrok dengan kondisi branch aktif)
git stash branch nama-branch-baru stash@{0}
```

### Kasus Nyata Penggunaan Stash

**Skenario: Kamu harus buru-buru pindah branch tapi ada kerjaan yang belum selesai di-commit:**

```bash
git stash push -m "Belum selesai: login screen"
git switch main
# ... selesaikan kerjaan perbaikan cepat ...
git switch feature/login
git stash pop
```

**Skenario: Kamu tidak sengaja menulis kode di branch yang salah:**

```bash
# Kamu nulis kode di main, padahal harusnya di feature/api
git stash
git switch feature/api
git stash pop
```

---

## Log dan Riwayat History

### Log Dasar

```bash
# Log standar
git log

# Log format ringkas (satu baris per commit)
git log --oneline

# Tampilkan 5 commit terakhir saja
git log -5

# Tampilkan log beserta perbedaan kodenya (diff)
git log -p

# Tampilkan statistik file yang berubah (baris tambah/kurang)
git log --stat

# Tampilkan statistik versi ringkas
git log --shortstat
```

### Mempercantik Format Log

```bash
# Tampilkan grafik riwayat percabangan lengkap
git log --oneline --graph --all --decorate

# Format log kustom
git log --pretty=format:"%h %ad | %s%d [%an]" --date=short

# Arti kode format penampung (placeholders):
# %h  = hash commit versi pendek
# %H  = hash commit versi lengkap
# %an = nama pembuat commit (author)
# %ae = email pembuat commit
# %ad = tanggal commit dibuat
# %s  = subjek commit (baris pertama pesan)
# %b  = isi detail pesan commit (body)
# %d  = nama referensi (branch, tag)
```

Contoh output `git log --oneline --graph --all`:

```text
* 3a1f2b9 (HEAD -> feature/auth) Add JWT validation
* c4d5e6f Implement login endpoint
| * 8f9a0b1 (main) Update README
|/
* 1a2b3c4 Initial project setup
```

### Memfilter Log

```bash
# Cari commit berdasarkan nama author
git log --author="Sofyan"

# Cari commit dalam rentang tanggal tertentu
git log --after="2026-01-01" --before="2026-06-01"

# Cari commit yang pesannya mengandung kata tertentu
git log --grep="authentication"

# Cari commit yang memodifikasi file spesifik
git log -- src/auth.js

# Cari commit yang menambah atau menghapus teks kode tertentu
git log -S "API_KEY"      # pencarian tipe pickaxe

# Cari commit menggunakan regex pada isi perubahan kode
git log -G "function\s+auth"

# Cari perbedaan commit di antara dua referensi
git log main..feature/auth     # commit di feature/auth yang ga ada di main
git log main...feature/auth    # commit yang ada di salah satu tapi ga keduanya

# Tampilkan commit di branch A tapi lewatkan branch B
git log feature/auth --not main
```

### Git Blame

Melihat siapa yang terakhir kali mengubah baris kode di suatu file:

```bash
# Blame satu file
git blame src/auth.js

# Blame hanya untuk baris tertentu
git blame -L 10,20 src/auth.js

# Abaikan perubahan spasi saja
git blame -w src/auth.js

# Deteksi kode yang dipindahkan atau disalin dari baris lain
git blame -M src/auth.js

# Deteksi kode yang disalin dari file lain
git blame -C src/auth.js
```

Contoh output:

```text
a1b2c3d4 (Sofyan  2026-03-15 10:30:00 +0700  1) const express = require('express');
e5f6g7h8 (Partner 2026-04-01 14:22:00 +0700  2) const jwt = require('jsonwebtoken');
a1b2c3d4 (Sofyan  2026-03-15 10:30:00 +0700  3) const router = express.Router();
```

### Git Show

```bash
# Lihat isi detail commit tertentu (diff + metadata)
git show abc1234

# Tampilkan hanya daftar nama file yang berubah di commit tersebut
git show --stat abc1234

# Tampilkan isi file di commit tertentu masa lalu
git show abc1234:src/auth.js

# Tampilkan commit yang ditunjuk oleh sebuah tag
git show v2.0.0
```

---

## Membatalkan Perubahan (Undoing Things)

Ini bagian yang paling sering bikin panik. Berikut panduan jelas mana yang aman dan mana yang berpotensi menghapus data.

### Memperbaiki Commit Terakhir

```bash
# Ubah pesan commit terakhir
git commit --amend -m "Pesan commit yang lebih baik"

# Masukkan file yang kelupaan ke commit terakhir tanpa edit pesan commit
git add file-ketinggalan.js
git commit --amend --no-edit

# Ganti nama author dari commit terakhir
git commit --amend --author="Nama Asli <email@asli.com>"
```

> **Catatan**: Perintah `--amend` itu mengganti commit terakhir dengan commit yang baru (hash berubah). Hanya gunakan ini untuk commit yang belum di-push ke remote, atau kamu terpaksa harus force-push nanti.

### Mengeluarkan File dari Staging Area (Unstage)

```bash
# Keluarkan file dari stage (tapi perubahan kodenya tetap aman di komputer)
git restore --staged src/auth.js

# Keluarkan semua file sekaligus
git restore --staged .

# Sintaks lama (masih bisa)
git reset HEAD src/auth.js
```

### Membatalkan Perubahan di Working Directory

```bash
# Batalkan perubahan di file tertentu (PERMANEN — perubahan kode bakal hilang!)
git restore src/auth.js

# Batalkan semua perubahan di folder aktif saat ini
git restore .

# Sintaks lama
git checkout -- src/auth.js
```

### Git Reset

Reset itu memindahkan pointer branch. Ada tiga mode reset yang menentukan nasib staging area dan working directory kamu:

```text
                      Pointer Branch   Staging Area   Working Directory
git reset --soft         Pindah          Gak Berubah       Gak Berubah
git reset --mixed        Pindah            Direset         Gak Berubah
git reset --hard         Pindah            Direset           Direset
```

#### Soft Reset

Memindahkan pointer branch tapi menjaga file kamu tetap berada di stage. Sangat pas untuk membatalkan commit terakhir buat diperbaiki isinya.

```bash
# Sebelum:
# A --- B --- C (HEAD)

git reset --soft HEAD~1

# Sesudah:
# A --- B (HEAD)
# Perubahan dari commit C tersimpan di stage, tinggal di-commit lagi nanti

# Contoh kasus: menggabungkan 3 commit terakhir jadi 1 saja
git reset --soft HEAD~3
git commit -m "Combined feature: user auth dengan validasi"
```

#### Mixed Reset (Default)

Memindahkan pointer branch dan mengeluarkan file dari stage, tapi membiarkan kodenya tetap aman di working directory.

```bash
# Sebelum:
# A --- B --- C (HEAD)

git reset HEAD~1        # --mixed adalah pilihan default

# Sesudah:
# A --- B (HEAD)
# Perubahan dari commit C tersimpan di working directory tapi statusnya belum di-stage

# Contoh kasus: commit terlalu cepat, ingin menata ulang staging file per bagian
git reset HEAD~1
git add src/model.js
git commit -m "Add user model"
git add src/controller.js
git commit -m "Add user controller"
```

#### Hard Reset (Menghapus Data!)

Memindahkan pointer branch DAN membuang seluruh perubahan kode tanpa sisa.

```bash
# Sebelum:
# A --- B --- C (HEAD)
# + ada juga perubahan yang belum di-stage di komputer

git reset --hard HEAD~1

# Sesudah:
# A --- B (HEAD)
# Commit C hilang. Perubahan yang belum di-stage hilang. Semua file sama persis dengan commit B.

# Contoh kasus: ingin membuang semua eksperimen kode yang gagal total
git reset --hard HEAD~3

# Samakan persis isi repositori lokal dengan server remote aktif saat ini
git reset --hard origin/main
```

> **Peringatan**: `--hard` adalah satu-satunya reset yang bisa melenyapkan perubahan kode yang belum di-commit secara permanen. Kalau sudah pernah di-commit sebelumnya, kodenya masih bisa diselamatkan lewat fitur reflog.

### Git Revert

Revert membuat **commit baru** yang serves untuk membatalkan efek dari commit sebelumnya. Ini sangat aman untuk branch bersama karena tidak merusak riwayat masa lalu.

```bash
# Sebelum:
# A --- B --- C (HEAD)

git revert C

# Sesudah:
# A --- B --- C --- C' (HEAD)
# Commit C' berisi kode pembatalan dari semua hal yang dilakukan commit C

# Revert commit tertentu berdasarkan hash
git revert abc1234

# Batalkan perubahan di commit tertentu tapi jangan langsung di-commit (taruh di stage saja dulu)
git revert --no-commit abc1234

# Batalkan commit merge (harus ditentukan induk/parent mana yang mau dipertahankan)
git revert -m 1 hash-commit-merge
# -m 1 artinya pertahankan parent pertama (biasanya branch utama tujuan merge)
```

### Perbandingan Reset vs Revert

| Fitur | `git reset` | `git revert` |
|---|---|---|
| Riwayat (History) | Menulis ulang (menghapus commit) | Menjaga (menambahkan commit baru) |
| Aman di Branch Bersama | Tidak | Ya |
| Berisiko Hilang Data | `--hard` bisa | Tidak |
| Kasus Penggunaan | Berbenah kode lokal | Membatalkan kode di branch tim |

### Reflog — Jaring Pengaman Kamu

Git merekam setiap kali pointer HEAD berpindah. Bahkan setelah kamu melakukan hard reset, kamu masih bisa mengembalikan commit yang hilang lewat reflog.

```bash
# Tampilkan daftar reflog
git reflog

# Contoh output:
# abc1234 HEAD@{0}: reset: moving to HEAD~3
# def5678 HEAD@{1}: commit: Add user validation
# ghi9012 HEAD@{2}: commit: Add user controller
# jkl3456 HEAD@{3}: commit: Add user model

# Selamatkan kode! Pindahkan branch balik ke titik sebelum reset
git reset --hard HEAD@{1}

# Atau bikin branch baru dari commit yang sempat hilang tadi
git branch selamatkan-kode def5678
```

> **Catatan**: Riwayat reflog punya masa kedaluwarsa, biasanya setelah 90 hari (untuk commit yang bisa dijangkau) atau 30 hari (untuk commit yatim yang tidak terhubung). Jangan tunggu terlalu lama untuk menyelamatkannya.

### Mengambil File Tertentu dari Riwayat Masa Lalu

```bash
# Kembalikan file tertentu ke kondisinya pada commit hash spesifik
git restore --source=abc1234 src/auth.js

# Kembalikan file ke kondisinya pada 3 commit yang lalu
git restore --source=HEAD~3 src/auth.js

# Menyelamatkan file yang sempat terhapus:
git log --diff-filter=D -- path/ke/file-yang-dihapus.js   # cari commit mana yang menghapusnya
git restore --source=abc1234^ path/ke/file-yang-dihapus.js  # kembalikan file dari commit SEBELUM terhapus
```

---

## Cherry-pick

Cherry-pick mengambil sebuah commit dari branch lain lalu menerapkannya ke branch aktif saat ini. Ini bakal membuat **commit baru** dengan isi perubahan yang sama tapi hash-nya berbeda.

```bash
# Terapkan commit tertentu ke branch aktif saat ini
git cherry-pick abc1234

# Terapkan commit tanpa langsung menjadikannya commit (taruh di stage saja)
git cherry-pick --no-commit abc1234

# Cherry-pick beberapa commit sekaligus
git cherry-pick abc1234 def5678

# Cherry-pick rentang commit (tidak termasuk commit awal, termasuk commit akhir)
git cherry-pick abc1234..ghi9012

# Cherry-pick rentang commit (termasuk commit awal dan akhir)
git cherry-pick abc1234^..ghi9012
```

### Menyelesaikan Konflik saat Cherry-pick

```bash
# Jika terjadi konflik:
# 1. Bereskan konfliknya di dalam file
# 2. Stage file tersebut
git add src/fixed-file.js

# 3. Lanjutkan cherry-pick
git cherry-pick --continue

# Atau batalkan cherry-pick seutuhnya
git cherry-pick --abort
```

### Kapan Menggunakan Cherry-pick?

```bash
# Menerapkan perbaikan bug (hotfix) dari main ke branch rilis stabil
git switch release/v2.0
git cherry-pick hash-commit-hotfix

# Mengambil satu commit fitur spesifik tanpa perlu merge seluruh isi branch fiturnya
git switch main
git cherry-pick hash-commit-fitur
```

> **Catatan**: Cherry-pick itu menduplikasi commit (isi sama, hash beda). Jika nanti kamu me-merge branch asalnya, Git biasanya cukup pintar menyinkronkannya, tapi bisa memicu kebingungan riwayat commit kalau terlalu sering dilakukan.

---

## Tag (Penanda Versi)

Tag digunakan untuk menandai titik penting dalam riwayat rilis — biasanya untuk nomor versi aplikasi.

### Lightweight Tag (Tag Sederhana)

Hanya pointer penunjuk biasa ke sebuah commit (seperti branch tapi posisinya tidak bisa bergerak maju):

```bash
# Bikin tag sederhana di commit saat ini
git tag v1.0.0

# Bikin tag pada commit spesifik di masa lalu
git tag v0.9.0 abc1234
```

### Annotated Tag (Tag Beranotasi - Direkomendasikan)

Menyimpan metadata lengkap: nama pembuat, email, tanggal, dan pesan deskripsi tag. Tag ini disimpan sebagai objek Git utuh.

```bash
# Bikin tag beranotasi
git tag -a v2.0.0 -m "Rilis versi 2.0.0 — Dukungan OAuth2"

# Bikin tag beranotasi pada commit tertentu
git tag -a v1.5.0 -m "Perbaikan bug keamanan login" abc1234
```

### Mengelola Tag

```bash
# Tampilkan semua tag
git tag

# Tampilkan tag yang polanya cocok
git tag -l "v2.*"

# Tampilkan detail info sebuah tag
git show v2.0.0

# Hapus tag di lokal komputer
git tag -d v1.0.0

# Hapus tag di server remote
git push origin --delete v1.0.0

# Push satu tag ke remote server
git push origin v2.0.0

# Push semua tag sekaligus ke remote server
git push origin --tags

# Push tag beranotasi saja yang relevan dengan commit terunggah
git push origin --follow-tags

# Checkout ke suatu tag (masuk ke detached HEAD)
git checkout v2.0.0

# Bikin branch baru dari sebuah tag
git switch -c hotfix/v2.0.1 v2.0.0
```

### Aturan Penomoran Versi (Semantic Versioning)

```text
v1.0.0  →  MAJOR.MINOR.PATCH
  │  │  │
  │  │  └── Perbaikan Bug (tidak merusak fitur lama / backward compatible)
  │  └───── Fitur Baru (tidak merusak fitur lama)
  └──────── Perubahan Besar (merusak fitur lama / breaking changes)
```

---

## Git Diff (Komparasi Perbedaan)

### Komparasi Kode yang Sedang Ditulis

```bash
# Cek perbedaan di working directory yang belum dimasukkan ke stage
git diff

# Cek perbedaan yang sudah ada di stage (siap di-commit)
git diff --staged
git diff --cached       # fungsi sama saja

# Cek semua perubahan (stage + unstage) dibandingkan commit terakhir (HEAD)
git diff HEAD

# Cek perbedaan pada file tertentu saja
git diff src/auth.js
git diff --staged src/auth.js
```

### Komparasi Antar Branch dan Commit

```bash
# Cek perbedaan antara dua branch
git diff main..feature/auth

# Hanya tampilkan daftar nama file yang berbeda saja
git diff --name-only main..feature/auth

# Tampilkan perbedaan disertai statistik ringkas
git diff --stat main..feature/auth

# Cek perbedaan di antara dua commit
git diff abc1234 def5678

# Perbedaan antara commit lama dengan kondisi HEAD saat ini
git diff abc1234 HEAD

# Perubahan yang dibawa oleh salah satu commit spesifik
git diff abc1234^..abc1234
# cara termudahnya:
git show abc1234
```

### Opsi Diff Lainnya

```bash
# Abaikan perubahan spasi saja
git diff -w

# Abaikan perbedaan jumlah spasi kosong
git diff -b

# Tampilkan perbedaan per kata bukan per baris seutuhnya
git diff --word-diff

# Ekspor perbedaan kode menjadi file patch
git diff > perubahan-saya.patch

# Terapkan file patch tersebut ke repositori
git apply perubahan-saya.patch
```

---

## Aturan .gitignore

File `.gitignore` memberi tahu Git file atau folder mana saja yang tidak boleh dilacak. Aturan ditulis relatif terhadap posisi file `.gitignore` tersebut berada.

### Sintaks Penulisan

```gitignore
# Ini adalah komentar

# Abaikan file spesifik bernama ini
secrets.env

# Abaikan semua file yang ekstensinya ini
*.log
*.tmp

# Abaikan satu folder utuh (wajib diakhiri garis miring)
node_modules/
dist/
build/

# Abaikan semua file pyc di folder tingkat mana saja
**/*.pyc

# Hanya abaikan file TODO.md yang ada di root folder saja
/TODO.md

# Pengecualian: jangan abaikan file ini (negasi)
*.log
!important.log

# Abaikan semua isi folder docs kecuali file README.md
docs/*
!docs/README.md

# Abaikan folder temp di kedalaman folder tingkat satu saja
*/temp/
# Abaikan folder temp di kedalaman tingkat mana saja
**/temp/
```

### Contoh Isi .gitignore yang Umum Dipakai

```gitignore
# === Package Dependencies ===
node_modules/
vendor/
.venv/
__pycache__/

# === Output Hasil Build ===
dist/
build/
*.o
*.pyc

# === Setingan IDE / Text Editor ===
.idea/
.vscode/
*.swp
*.swo
*~
.DS_Store

# === File Environment (Rahasia) ===
.env
.env.local
.env.*.local

# === Log File ===
*.log
logs/

# === OS Files ===
.DS_Store
Thumbs.db

# === Folder Pengujian (Testing) ===
coverage/
.nyc_output/

# === File Kunci Rahasia (JANGAN PERNAH DI-COMMIT) ===
*.pem
*.key
*.p12
credentials.json
```

### Mengelola File yang Terlanjur Dilacak

```bash
# Berhenti melacak file tapi biarkan filenya tetap ada di komputer
git rm --cached secrets.env
# Setelah ini, tambahkan secrets.env ke dalam file .gitignore

# Berhenti melacak satu folder utuh
git rm -r --cached node_modules/

# Cara bersihkan cache tracking setelah mengedit isi .gitignore:
git rm -r --cached .
git add .
git commit -m "Apply update aturan .gitignore baru"
```

### File `.gitignore` Global

Bermanfaat untuk mengabaikan file sampah OS di semua repositori lokal kamu:

```bash
# Bikin file gitignore global
echo ".DS_Store\n*.swp\n.idea/" >> ~/.gitignore_global

# Beritahu Git untuk memakai file gitignore global tersebut
git config --global core.excludesfile ~/.gitignore_global
```

### Memeriksa Status Abaikan File

```bash
# Cek aturan mana di .gitignore yang mengabaikan file ini
git check-ignore -v debug.log
# Contoh output: .gitignore:3:*.log    debug.log

# Tampilkan daftar seluruh file yang diabaikan
git status --ignored
```

---

## Worktrees

Worktree membolehkan kamu checkout beberapa branch **secara bersamaan** di folder yang terpisah. Jadi kamu tidak perlu stash atau buru-buru commit kerjaan setengah matang hanya untuk pindah branch sejenak.

### Perintah Dasar Worktree

```bash
# Bikin folder worktree baru untuk suatu branch
git worktree add ../hotfix-login hotfix/login-bug

# Sekarang struktur folder kamu:
# /project           → branch main aktif (repositori asal)
# /hotfix-login      → branch hotfix/login-bug aktif (terpisah)

# Bikin folder worktree sekaligus bikin branch baru
git worktree add -b feature/new-api ../new-api main
```

### Mengelola Worktree

```bash
# Tampilkan daftar seluruh worktree aktif
git worktree list
# Contoh output:
# /Users/sofyan/project          abc1234 [main]
# /Users/sofyan/hotfix-login     def5678 [hotfix/login-bug]

# Hapus folder worktree setelah selesai dipakai
git worktree remove ../hotfix-login

# Bersihkan metadata worktree yang foldernya terhapus manual
git worktree prune
```

### Skenario Nyata Penggunaan Worktree

```bash
# Kamu lagi asyik bikin fitur baru, tiba-tiba ada bug kritis di production

# Dibandingkan harus stash-switch branch:
git worktree add ../emergency-fix -b hotfix/prod-crash main

# Pindah ke folder darurat tersebut dan bereskan bug-nya
cd ../emergency-fix
# ... lakukan perbaikan ...
git add .
git commit -m "Fix null pointer in payment processing"
git push origin hotfix/prod-crash

# Balik lagi ke folder project utama kamu tadi — tanpa stashing atau kehilangan fokus
cd ../project
# Branch fitur kamu tetap di kondisi terakhir saat kamu tinggalkan

# Setelah kelar, bersihkan folder daruratnya
git worktree remove ../emergency-fix
```

---

## Bisect (Pencarian Bug Binary)

`git bisect` melakukan pencarian binary search pada riwayat commit untuk mendeteksi commit mana yang pertama kali membawa bug. Jadi kamu tidak usah mengecek commit satu per satu secara manual.

### Cara Manual Bisect

```bash
# Mulai bisect
git bisect start

# Tandai commit saat ini berstatus rusak/ada bug (bad)
git bisect bad

# Tandai commit lama di masa lalu yang statusnya normal/tidak ada bug (good)
git bisect good v1.0.0

# Git bakal otomatis checkout ke commit pertengahan. Tes aplikasinya, lalu:
git bisect good    # kalau versi ini normal
# atau
git bisect bad     # kalau versi ini rusak / ada bug
```

Git bakal terus membelah sisa commit sampai akhirnya mendeteksi:

```text
# abc1234 is the first bad commit
# commit abc1234
# Author: Seseorang
# Date: ...
# Message: Refactor payment module
```

Setelah ketemu biang keroknya, kembalikan posisi branch ke kondisi awal:

```bash
git bisect reset
```

### Cara Otomatis Bisect

Kalau kamu punya script testing yang exit codenya `0` untuk sukses (good) dan non-zero untuk error (bad):

```bash
git bisect start
git bisect bad HEAD
git bisect good v1.0.0

# Suruh Git mengetes tiap commit secara otomatis pakai script tadi
git bisect run npm test

# Atau pakai script kustom kamu sendiri
git bisect run ./test-login.sh

# Git bakal otomatis mencari commit yang merusak kodenya sendiri sampai selesai
```

### Memulai dengan Rentang Commit

```bash
# Kalau kamu tahu bug-nya ada di antara commit HEAD dan abc1234
git bisect start HEAD abc1234
# ini sama saja dengan:
# git bisect start
# git bisect bad HEAD
# git bisect good abc1234
```

### Contoh Kasus Praktis

```bash
# Menu login rusak. Kemarin pas rilis (v2.3.0) statusnya masih aman berjalan.
git bisect start
git bisect bad HEAD
git bisect good v2.3.0

# Git berkata: "Bisecting: 23 revisions left to test (roughly 5 steps)"
# Git checkout otomatis. Kamu jalankan test:
npm test
# Kalau test error → git bisect bad
# Kalau test aman → git bisect good

# Setelah sekitar 5 langkah:
# "abc1234 is the first bad commit"
# Ketemu! Sekarang kamu tahu persis bagian kode mana yang merusaknya.

git bisect reset
git show abc1234   # telusuri commit yang bersalah
```

---

## Workflow Praktis yang Sering Dipakai

### Workflow Branch Fitur (Feature Branch)

Workflow paling umum yang sering dipakai di dalam tim:

```bash
# 1. Pastikan main kamu sudah versi terbaru
git switch main
git pull

# 2. Bikin branch fitur baru
git switch -c feature/user-dashboard

# 3. Mulai nulis kode (bebas commit beberapa kali)
git add .
git commit -m "Add dashboard layout"
# ... kerja lagi ...
git add .
git commit -m "Add charts component"
# ... kerja lagi ...
git add .
git commit -m "Connect dashboard to API"

# 4. Sinkronkan branch kamu dengan update main terbaru
git fetch origin
git rebase origin/main
# (selesaikan konflik jika ada)

# 5. Rapikan riwayat commit sebelum diajukan ke Pull Request (PR)
git rebase -i origin/main
# Squash commit-commit kecil, perbaiki pesan commit yang kurang oke

# 6. Push branch ke server remote
git push -u origin feature/user-dashboard

# 7. Setelah PR di-merge oleh tim, bersihkan branch lokalnya
git switch main
git pull
git branch -d feature/user-dashboard
```

### Workflow Hotfix (Perbaikan Cepat Production)

```bash
# 1. Bikin branch langsung dari main (atau tag rilis production aktif)
git switch main
git pull
git switch -c hotfix/payment-crash

# 2. Selesaikan perbaikan bug-nya
git add .
git commit -m "Fix null check in payment processing"

# 3. Push dan buat PR
git push -u origin hotfix/payment-crash

# 4. Setelah digabungkan, pasang tag versi rilis baru
git switch main
git pull
git tag -a v2.3.1 -m "Hotfix: payment crash"
git push origin v2.3.1

# 5. Bersihkan branch hotfix-nya
git branch -d hotfix/payment-crash
git push origin --delete hotfix/payment-crash
```

### Merapikan Commit sebelum Membuat PR

```bash
# Kamu punya commit lokal yang riwayat pesannya berantakan:
# "Add feature"
# "fix typo"
# "WIP"
# "actually fix it"
# "review feedback"

# Satukan semua commit kecil tadi ke dalam commit logis utama:
git rebase -i origin/main

# Di dalam editor, ubah daftarnya menjadi:
pick a1b2c3d Add user notification system
fixup e4f5g6h fix typo
fixup i7j8k9l WIP
fixup m0n1o2p actually fix it
fixup q3r4s5t review feedback

# Hasilnya: tersisa satu commit bersih berbunyi "Add user notification system"
```

### Menyinkronkan Repositori Fork

```bash
# Tambahkan alamat remote repositori hulu/asli jika belum ada
git remote add upstream https://github.com/original/repo.git

# Ambil dan gabungkan perubahan dari upstream ke lokal main kamu
git fetch upstream
git switch main
git merge upstream/main
git push origin main

# Rebase branch fitur kamu di atas main yang sudah terupdate tadi
git switch feature/my-work
git rebase main
```

### Memperbaiki Kesalahan Umum

**Salah menulis commit di branch main (seharusnya di branch fitur):**

```bash
# Batalkan commit terakhir tapi biarkan kodenya tetap aman
git reset --soft HEAD~1

# Simpan kodenya sementara, lalu pindah branch dan terapkan kembali kodenya
git stash
git switch branch-fitur-yang-benar
git stash pop
git add .
git commit -m "Pesan commit kamu"
```

**Ingin membatalkan commit yang telanjur di-push ke branch bersama:**

```bash
# Gunakan revert (aman, tidak merusak riwayat commit rekan kerja)
git revert abc1234
git push
```

**Tidak sengaja menghapus branch lokal:**

```bash
# Cari hash commit terakhir di branch yang terhapus tadi
git reflog

# Bikin ulang branch-nya berdasarkan hash commit tersebut
git branch branch-selamat abc1234
```

**Tidak sengaja menjalankan `git reset --hard`:**

```bash
# Jangan panik — reflog siap menyelamatkanmu
git reflog
# Cari hash commit tepat sesaat sebelum reset dijalankan
git reset --hard HEAD@{1}
```

**Merge salah jalan dan ingin dibatalkan:**

```bash
# Jika commit merge belum di-push ke server remote
git reset --hard HEAD~1

# Jika commit merge sudah terlanjur di-push ke server remote
git revert -m 1 hash-commit-merge
git push
```

### Memecah Satu Commit Menjadi Dua

Jika sebuah commit berisi perubahan yang terlalu banyak dan ingin kamu pisah:

```bash
# Lakukan rebase interaktif, tandai commit target dengan status 'edit'
git rebase -i HEAD~3

# Di editor: ubah status 'pick' menjadi 'edit' pada commit yang mau dipecah

# Git bakal berhenti di commit tersebut. Batalkan commit tapi pertahankan kodenya:
git reset HEAD~1

# Sekarang tinggal masukkan kodenya satu per satu ke commit terpisah:
git add src/model.js
git commit -m "Add user model"

git add src/controller.js
git commit -m "Add user controller"

# Lanjutkan proses rebase sampai selesai
git rebase --continue
```

### Menjaga Kebersihan Working Directory

```bash
# Cek daftar file sampah yang belum dilacak (uji coba dulu)
git clean -n

# Hapus file sampah yang belum dilacak dari repositori (eksekusi)
git clean -f

# Hapus file sampah DAN folder sampah yang belum dilacak
git clean -fd

# Hapus semua file sampah, folder sampah, termasuk file yang diabaikan di .gitignore
git clean -fdx

# Jalankan proses pembersihan secara interaktif satu per satu
git clean -i
```

---

## Tabel Rangkuman Singkat

### Perintah Harian

| Perintah | Aksi / Fungsi |
|---------|-------------|
| `git status -sb` | Status singkat disertai info branch |
| `git add -p` | Masukkan perubahan kode ke stage secara interaktif |
| `git commit -m "pesan"` | Commit perubahan dengan pesan |
| `git commit --amend --no-edit` | Tambahkan file baru ke commit terakhir |
| `git pull --rebase` | Ambil update baru dengan metode rebase |
| `git push` | Unggah commit branch aktif ke server remote |
| `git switch -c nama_branch` | Bikin branch baru dan langsung pindah ke sana |
| `git switch -` | Pindah balik ke branch sebelumnya |
| `git stash` | Simpan sementara perubahan kode aktif |
| `git stash pop` | Terapkan kembali perubahan kode dari stash terakhir |
| `git log --oneline -10` | Lihat 10 log commit terakhir secara ringkas |
| `git diff --staged` | Cek perbedaan kode yang sudah ada di stage |

### Urusan Branching

| Perintah | Aksi / Fungsi |
|---------|-------------|
| `git branch` | Tampilkan daftar branch lokal |
| `git branch -a` | Tampilkan daftar seluruh branch (lokal + remote) |
| `git switch -c nama` | Bikin branch baru dan langsung pindah |
| `git branch -d nama` | Hapus branch lokal yang sudah di-merge |
| `git branch -D nama` | Hapus paksa branch lokal yang belum di-merge |
| `git branch -m nama_baru` | Ubah nama branch aktif saat ini |
| `git push origin --delete nama` | Hapus branch di server remote |

### Pembatalan Kode (Undoing)

| Perintah | Aksi / Fungsi |
|---------|-------------|
| `git restore nama_file` | Buang perubahan kode di working directory |
| `git restore --staged nama_file` | Keluarkan file dari staging area (unstage) |
| `git reset --soft HEAD~1` | Batalkan commit, simpan filenya di stage |
| `git reset HEAD~1` | Batalkan commit, keluarkan filenya dari stage |
| `git reset --hard HEAD~1` | Batalkan commit, buang seluruh perubahan kode |
| `git revert hash_commit` | Batalkan efek commit masa lalu lewat commit baru |
| `git reflog` | Tampilkan riwayat lengkap perpindahan HEAD |
| `git cherry-pick hash_commit` | Terapkan commit tertentu dari branch sebelah |

### Riwayat Sejarah (History)

| Perintah | Aksi / Fungsi |
|---------|-------------|
| `git log --oneline --graph --all` | Tampilkan grafik visual alur commit repositori |
| `git log --author="nama"` | Filter log commit berdasarkan nama pembuatnya |
| `git log -S "kode"` | Cari commit berdasarkan penambahan/penghapusan kode |
| `git log -- nama_file` | Tampilkan log commit khusus untuk file ini saja |
| `git blame nama_file` | Lihat siapa pembuat terakhir tiap baris kode file |
| `git show hash_commit` | Detail isi data dan diff dari commit tertentu |
| `git diff A..B` | Bandingkan kode di branch A dengan branch B |

### Urusan Remote Server

| Perintah | Aksi / Fungsi |
|---------|-------------|
| `git remote -v` | Tampilkan daftar alamat remote repositori |
| `git fetch --prune` | Ambil update server sekaligus bersihkan branch mati |
| `git pull --rebase` | Ambil update server dengan metode rebase |
| `git push -u origin branch` | Push branch sekaligus setting default tracking |
| `git push --force-with-lease` | Push paksa yang aman (tidak menimpa kerjaan teman) |

### Fitur Lanjutan (Advanced)

| Perintah | Aksi / Fungsi |
|---------|-------------|
| `git rebase -i HEAD~n` | Rebase interaktif untuk n commit terakhir |
| `git bisect start` | Jalankan binary search pencari biang bug |
| `git worktree add path branch` | Buka branch lain di folder terpisah bersamaan |
| `git stash -u` | Stash beserta file baru yang belum terlacak |
| `git clean -fd` | Hapus file sampah dan folder sampah tak terlacak |
| `git tag -a v1.0 -m "msg"` | Bikin tag beranotasi untuk rilis versi |
| `git commit --fixup=hash` | Bikin commit perbaikan khusus untuk autosquash |
| `git rebase --autosquash` | Jalankan autosquash otomatis saat rebase |

---

*Cheatsheet ini mencakup perintah Git yang bakal kamu pakai 99% dari waktu kerja kamu. Kuasai perintah dasar ini, dan kamu bakal bisa menangani workflow apa pun.*
