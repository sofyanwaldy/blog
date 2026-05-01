+++
title = 'Membuat Aplikasi Catatan Keuangan (Bukuin) dengan Vibe Coding'
date = 2026-05-01T23:30:00+07:00
draft = false
+++

Akhir-akhir ini lagi rame istilah "vibe coding" di media sosial, apalagi Threads (soalnya udah ga make Twitter, hehe). Walaupun sudah banyak disebutkan bahwa coding dengan pakai AI tidak semerta-merta disebut vibe coding, hal ini juga disebutkan oleh bang [@ariya di Thread-nya](https://www.threads.com/@ariya114/post/DHpes5WxFqR) dengan mengambil referensi dari Simon Willison: [Not All AI-Assisted Programming is Vibe Coding](https://simonw.substack.com/p/not-all-ai-assisted-programming-is).

## Latar Belakang

Jadi aku mencoba membuat aplikasi keuangan yang kuberi nama **Bukuin** ([bukuin.id](https://bukuin.id)). Awalnya tujuanku adalah untuk penggunaan pribadi agar mempermudah lacak pengeluaran/pendapatan—karena sebelumnya melakukan pencatatan di Obsidian.md dan masih belum membentuk kebiasaanku untuk mencatat.

Seperti yang dikatakan James Clear di buku _Atomic Habits_, ada poin menarik di "4 Laws of Behavior Change", salah satunya adalah **make it easy**.

## Tech Stack

Aplikasi ini dibangun menggunakan **Flutter**. Walaupun aku tidak terlalu pro dalam menggunakan Flutter, tapi aku memahami dasarnya, dan dengan bantuan AI, proses development jadi lebih accessible.

## Fase Development

Kemudian masuk ke fase development:

1. **Brainstorming dengan AI** — Aku lebih sering pakai model Sonnet dari Anthropic. Mulai menyusun prompt yang tujuannya adalah membuat plan termasuk fitur MVP dari aplikasi ini.

2. **Design** — Masih brainstorming, tapi aku lanjut dengan prompt untuk design mulai dari color-schema, font, dan UI-nya. Dan seperti kebanyakan orang, awal-awal biasanya direkomendasikan warna ungu.

<div style="display: flex; gap: 10px; flex-wrap: wrap; justify-content: center; margin: 20px 0;">
  <img src="/images/bukuin/onboardscreen.png" alt="Onboarding Screen" width="150">
  <img src="/images/bukuin/homescreen.png" alt="Home Screen" width="150">
  <img src="/images/bukuin/transactionscreen.png" alt="Transaction Screen" width="150">
  <img src="/images/bukuin/insightscreen.png" alt="Insight Screen" width="150">
</div>

3. **Eksekusi** — Aku minta dia define color, design, dan screens.

4. **Testing** — Suruh nulis test untuk setiap function yang dia buat, terutama fitur-fitur utama.

5. **Validasi** — Validasi hasil dengan cara manual testing, lalu prompt, dan begitulah terus mengulang sampai hasilnya seperti saat ini. Jadi sepertinya _Human-in-the-Loop_ relevan sampai di sini.

## Durasi Development

Total waktu dari ide sampai rilis pertama: **kurang lebih 3 bulan**.

- **2 bulan** untuk development
- **1 bulan** untuk proses masuk ke Play Store—karena ada kebijakan wajib 14 hari testing, ditambah terjadi perubahan di fase testing

Perlu dicatat, ini dikerjakan di waktu kosong: Sabtu, Minggu, dan malam hari karena bekerja di siang hari.

## Tantangan & Lessons Learned

Tantangan terbesar adalah aku bikin aplikasi ini sebelum Sonnet 3.6 rilis. Jadi aku gonta-ganti model mulai dari GPT-4, SWE dari Windsurf, Gemini 1.5, dan Sonnet 3.5.

Hasilnya? Masih banyak fitur yang menyebabkan bugs—makanya perlu di-test sebaik mungkin dan divalidasi manual. Bahkan di awal bikin, masih ada linter yang error dan inkonsistensi dari design/color schema.

**Takeaway:** Vibe coding bukan berarti full autopilot. Human validation tetap krusial, apalagi kalau berganti-ganti model AI yang punya "kepribadian" coding berbeda-beda.

## Penutup

Jadi beginilah ceritanya dalam proses pembuatan aplikasi Bukuin ini dengan vibe coding.

Untuk aplikasinya kalian bisa download di [Play Store](https://play.google.com/store/apps/details?id=com.waldy.bukuin&hl=id) dan webnya di [bukuin.id](https://bukuin.id). Jangan lupa di-rating ya!
