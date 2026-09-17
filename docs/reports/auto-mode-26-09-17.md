# Laporan Auto Mode — 2026-09-17

## Apa yang dikerjakan

Permintaan: buka folder `../ariel web`, pull repo `kemalzaki/ariel`, lalu kerjakan tugas
kuliah "Halaman Profil Diri dengan HTML5" (struktur semantik, foto + alt, biodata, tabel
mata kuliah, link, metadata title/description/og).

Eksekusi:

1. Folder `ariel web` ternyata kosong dan belum jadi repo, jadi dilakukan `git clone https://github.com/kemalzaki/ariel .` (branch `master`, satu commit "Initial commit").
2. Repo ternyata situs portofolio Astro milik M. Ariel Davinsyah P. (data di `src/config.ts`).
   Keputusan: halaman tugas dibuat sebagai HTML murni di `public/profil/index.html` supaya
   (a) kode sumbernya bisa dinilai langsung dosen sesuai tuntutan tugas, (b) folder `public/`
   Astro tersalin apa adanya saat build sehingga path `/profil/` otomatis tersedia jika repo
   di-deploy, dan (c) foldernya bisa juga di-upload sendiri ke hosting tugas. Portofolio
   tidak tersentuh sama sekali.
3. Dibuat: `public/profil/index.html` (HTML5 semantik, CSS inline, nol JavaScript),
   `public/profil/foto-profil.png` (placeholder 1200x1200 digenerate dengan Python stdlib,
   dipakai juga sebagai og:image), salinan `favicon.svg`, design brief
   `docs/halaman-profil/index.html`, laporan E2E `docs/reports/halaman-profil/index.html`
   beserta screenshot di `images/`, dan laporan ini.
4. GitHub Pages untuk repo ini dicek: belum aktif (API 404). Jadi hosting belum ada;
   upload/pengaktifan Pages menjadi langkah pemilik repo (branch `master` terlindungi,
   tidak saya push).

## Perintah verifikasi dan hasilnya

| Perintah / cara | Hasil |
|---|---|
| Clone repo | Berhasil, `master` bersih, tanpa konflik |
| `python3` generator PNG | Berhasil, 1200x1200, 30 KB |
| `python3 -m http.server 8123` + curl dua sisi (WSL dan Windows) | 200 / 200 |
| Chrome headless screenshot 1280px | Berhasil, layout lengkap dan rapi (dilihat langsung) |
| Chrome headless screenshot 375px | Berhasil, satu kolom, tanpa scroll horizontal halaman |
| Chrome headless screenshot design brief | Berhasil, Mermaid ter-render |
| Chrome `--enable-logging=stderr` untuk console | Nol pesan error/warning |
| curl aset (foto, favicon, font CDN, uinsgd.ac.id) | Semua 200 |
| grep anchor `href="#..."` vs `id="..."` | Lima pasang, semuanya cocok |

## Yang gagal, dilewati, atau belum terverifikasi

- **Verifikasi interaktif (klik menu satu per satu, hover, mailto)**: tidak dijalankan.
  MCP chrome-devtools tidak terpasang di sesi ini dan Playwright tidak ada di WSL.
  Penggantinya: Chrome headless dengan screenshot nyata + konsistensi anchor diperiksa
  lewat grep. Skenario yang bergantung klik ditandai "belum diuji langsung" di laporan E2E.
- **Skenario font diblokir dan foto dihapus**: tidak disimulasikan; mitigasinya (fallback
  font stack, alt deskriptif + width/height) diverifikasi dari kode, bukan dari browser.
  Alasan: tanpa browser interaktif, blokir per-request tidak bisa disimulasikan.
- **ASTro build (`npm run build`)**: tidak dijalankan. Node tidak tersedia di PATH WSL ini
  (hanya ada node.exe sisi Windows dan pnpm standalone). Halaman tugas sendiri bukan bagian
  pipeline Astro (hanya file statis di `public/`), jadi risiko terhadap build praktis nol,
  tapi ini tetap perintah yang tidak saya jalankan, bukan klaim bahwa pasti hijau.

## Kelemahan fondasi yang ditemukan

- Repo tidak punya CI/lint/test (bawaan template portofolio). Tidak saya tambahkan: ini repo
  tugas statis satu halaman, menambah pipeline build di luar lingkup permintaan. Dicatat di
  sini supaya pemilik repo tahu.
- Tidak ada masalah secret: tidak ada kunci/token di berkas yang dibuat.

## Pemeriksaan keamanan

Agent `agent-security` tidak tersedia di lingkungan sesi ini, jadi pemeriksaan dilakukan
sendiri dengan hasil sebagai berikut.

- **Kode aplikasi**: HTML statis tanpa JavaScript, tanpa form, tanpa input, tanpa penyimpanan
  apa pun. Tidak ada permukaan XSS/injection karena tidak ada data dinamis.
- **Rahasia bocor**: tidak ada kunci, token, atau password di semua berkas baru (dicek
  dengan membaca isi berkas). Satu-satunya data pribadi adalah nama, email, dan kampus,
  semuanya sudah publik di `src/config.ts` portofolio sebelum perubahan ini.
- **Dependency**: tidak ada dependensi baru. Satu-satunya permintaan eksternal halaman
  adalah CSS Google Fonts (IBM Plex Mono).
- **Konfigurasi berisiko**: kedua tautan `target="_blank"` memakai `rel="noopener noreferrer"`.
  Tidak ada cookie, tidak ada izin browser, tidak ada iframe pihak ketiga.

## Keputusan yang saya ambil sendiri (perlu diketahui)

- Halaman dibuat di `public/profil/`, bukan mengubah portofolio Astro: tugas meminta file
  `index.html` yang dinilai strukturnya.
- Data mata kuliah, NIM (masih strip), angkatan (masih strip), nama fakultas, dan jadwal
  adalah asumsi yang wajar dan sudah ditandai TODO di kode; Ariel wajib mengoreksinya.
- Foto masih placeholder (siluet); wajib diganti foto asli sebelum dikumpulkan, nama file
  tetap `foto-profil.png` supaya og:image ikut benar.
- `og:image` awalnya path relatif menunggu domain final. Setelah user menyebutkan domain
  (`https://ariel-chi.vercel.app`, deploy via Vercel), nilai ini diganti ke URL absolut
  `https://ariel-chi.vercel.app/profil/foto-profil.png`. Saat pengecekan, deployment live
  masih menunjukkan `/profil/` 404 karena commit memang belum di-push.
- Commit dilakukan lokal di branch `master` dan TIDAK di-push (master termasuk branch
  terlindungi). Push dilakukan oleh pemilik repo, setelah itu Vercel otomatis mendeploy
  dan URL pengumpulan menjadi `https://ariel-chi.vercel.app/profil/`.

## Commit

- `git add` seluruh berkas baru di `public/profil/`, `docs/halaman-profil/`,
  `docs/reports/`, lalu commit di `master` lokal dengan pesan
  "tambah halaman profil diri (public/profil) untuk tugas HTML5".
- Hash akhir tertera di `git log` (commit diamend sekali setelah laporan ini diperbarui dengan
  domain final). Status akhir: `master` ahead 1 dari `origin/master`, belum di-push (branch
  terlindungi).
- Identitas git di WSL ini belum pernah diatur, jadi diset lokal untuk repo ini saja:
  `kemalzaki <kemalmzaki@gmail.com>`, mengikuti penulis commit pertama repo dan akun `gh`
  yang aktif. Config global tidak disentuh.
