---
site: pemikatwalet.com
deploy: { host: unknown, cf_account: null, branch: main, deploy_hook: unknown }
url_patterns: { artikel: "/{slug}.html (flat di root) + /blog/{slug}.html", kota: "/kota/kotaparfum-walet-kota-{kabupaten}-{provinsi}-nomor-1-di-indonesia.html", page: "/{slug}/ (folder + index.html)", desain: "/desain/rumah-walet-ukuran-{NxN}-m-terbaik-di-indonesia-markas-walet/" }
chrome: { header_lines: "opens@4367 (id=masthead)", footer_lines: "opens@5911 (id=colophon), ends ~6100 of 6108", byte_identical: "variants:per-page-active-menu" }
analytics: { gtm: "GTM-KNC25DD", ga4_property_id: "", tiktok: "CJA4IDBC77U5K7SPBH0G", fb_verify: "", adsense: "" }
brand: { primary: "#1ecd56", font: "Poppins + Roboto + Roboto Slab", logo: "/wp-content/uploads/2023/03/logo-walet.png", favicon: "/wp-content/uploads/2023/04/logo-favicon.png" }
ecosystem_menu: present
contact: { wa: "6287725260196", email: "sales@markaswalet.com" }
media: { local_mb: 58, hotlink: "blogspot:6" }
content: { articles: 7, kota_unique: 489, kota_admin_level: kabupaten }
cleanup: ["wp-json/ = 511 file JSON statis MATI (dump REST, bukan API)", "WooCommerce dimuat tapi cart mati -> /produk = halaman marketing, bukan toko", "kota_admin: nama file dobel-kata 'kotaparfum-walet-kota-' -> JANGAN diubah (pertahankan URL)", "media belum ada WebP (jpg/png saja)", "wp-json menyimpan URL dev lama http://localhost/installwp (di file mati, tak berdampak)"]
blockers: ["Konfirmasi host yang menyajikan main: GitHub Pages ATAU Cloudflare Pages (integrasi CMS sama: commit ke main). deploy_hook present/absent belum diketahui", "Akses tulis CMS ke repo Website-Markas-Walet/pemikatwalet.com (rekomendasi: GitHub App scoped, contents:write)", "Konfirmasi main auto-deploy on push (vs perlu Deploy Hook)"]
---

# Catatan kesiapan integrasi — pemikatwalet.com

## Identitas & deploy
- **Situs statis git-hosted hasil export WordPress 6.2** (plugin Simply Static; tema Astra + Elementor + ElementsKit + WooCommerce + RankMath). **Tidak ada runtime, DB, PHP, atau build step.**
- **Nol file config deploy** di repo (tak ada `CNAME`, `.nojekyll`, `.github/workflows`, `wrangler.toml`, `netlify.toml`, `vercel.json`, `_redirects`, `.htaccess`).
- Histori commit berpola `Create X.html` / `Update X.html` = **editing langsung via GitHub web UI → live**. Branch produksi = `main`.
- Host live (GitHub Pages vs Cloudflare Pages) **tidak terdeteksi dari isi repo** — ada di Settings repo / dashboard CF. **Apa pun jawabannya, titik integrasi CMS sama: commit ke `main`, host auto-deploy on push.**

## Chrome (header/menu/footer)
- Header/menu/footer **ter-hardcode di 1.022 file**, bukan partial bersama.
- **Footer BYTE-IDENTIK** di semua halaman (aman diangkat jadi 1 template apa adanya).
- **Header TIDAK byte-identik**: hanya berbeda pada penanda menu aktif (`current-menu-item` / `current_page_item`). Saat CMS mengangkat header jadi template bersama, **strip penanda active-state** lalu set per halaman.
- Batas di `index.html`: `<header id="masthead">` mulai **baris 4367**; `<footer id="colophon">` mulai **baris 5911**; konten berada di antara keduanya (total 6108 baris). Slot untuk CMS: `<!-- CONTENT -->` = wilayah antara penutup header dan pembuka footer.

## Menu jaringan (ekosistem) — kandidat kontrol pusat #1
Sudah tertanam 9 brand: **MarkasWalet · NestOne · ParfumWalet · GedungWalet · CuciWalet · BudidayaWalet · SuaraWalet · SarangWalet · SeminarMarkasWalet**.

## Konten
- **Artikel: 7** (6 flat di root + 1 di `/blog/`). Format HTML penuh (bukan markdown/blok).
- **Kota (pSEO): 489 unik**, level **kabupaten/kota**, 1 template seragam → CMS cukup simpan **1 template + daftar 489 kabupaten/provinsi**, jangan 489 file manual.
- **Desain: 5** (ukuran 4x4 → 14x14). **Page: 6** (home, produk, layanan, tentang, kontak, testimoni).
- Link internal **root-relative** (`/produk`) → domain-agnostic, aman tanpa rewrite. **Default: URL/slug TIDAK diubah** (tak perlu redirect 301).

## Media
- Lokal ~32 gambar (17 jpg + 15 png), **58 MB**, di `/wp-content/uploads/2023/{02,03,04}/`. **Belum ada WebP.**
- **Hotlink eksternal minim: 6 gambar `bp.blogspot.com`** → rehome ke R2. (Termasuk paling bersih di jaringan.)
- Embed: YouTube (~1 video berulang di halaman kota), GTM, 1 gravatar — embed, bukan aset untuk di-rehome.

## Sisa yang bisa dieksekusi session ini kapan saja (non-blocker)
- Ekstrak chrome → 1 layout snippet dengan slot `<!-- CONTENT -->` (footer identik; header di-strip active-state).
- Ekspor artikel terstruktur (title, slug, body HTML, meta, tanggal, kategori, author, cover) + dataset kota master (1 template + 489 kabupaten/provinsi).
