# Rencana Kerja Kasir Offline Sampai Production/Publish

Dokumen ini adalah roadmap hidup untuk eksekusi proyek `kasir_offline` dan
`kasir_license_manager`. Detail historis tetap berada di
`HANDOFF_ROADMAP_LENGKAP_KASIR_OFFLINE_SAMPAI_RILIS_2026-07-25.txt`, sedangkan
hasil batch yang sudah lulus dicatat berurutan di `fitur yg sudah diterapkan.md`.

Terakhir diperbarui: 16 November 2026.

## Aturan tetap

- Audit source aktual sebelum perubahan.
- Kerjakan satu batch yang masih satu domain.
- Jangan mengubah file generated atau migration lama secara manual.
- Jangan pernah memasukkan private key produksi ke source, test, log, atau chat.
- Urutan verifikasi: format, targeted test, regression, analyzer, full test,
  kemudian device/release test jika memang diperlukan.
- Batch hanya berstatus selesai setelah semua gerbang yang relevan lulus.
- Setiap batch yang selesai wajib ditambahkan ke `fitur yg sudah diterapkan.md`.
- Jika test gagal, selesaikan akar kegagalan pertama sebelum melanjutkan.

## SOP pindah laptop pengembangan

SOP ini dijalankan bila pengembangan berpindah laptop. AI pada **laptop lama**
wajib membantu pengguna menyelesaikan langkah berikut sebelum laptop lama tidak
dapat diakses lagi:

1. Di setiap repository aktif (`kasir_offline`, `kasir_license_manager`, dan
   backend bila ada), periksa `git status`, selesaikan atau catat perubahan
   yang memang ingin dibawa, lalu `git add`, `git commit`, dan `git push` ke
   remote yang benar. Jangan menyimpan secret dalam commit.
2. Salin lewat media/penyimpanan aman yang terenkripsi, **bukan GitHub/chat**:
   file `.env`, private key lisensi, keystore release beserta
   `key.properties`, sertifikat, dan credential production lain yang memang
   diperlukan. Pastikan terdapat minimal dua salinan aman untuk private key
   produksi.
3. Salin `C:\Users\<pengguna>\.android\debug.keystore` bila aplikasi
   debug/profile dari laptop lama masih terpasang pada perangkat uji. Dengan
   keystore yang sama, APK dari laptop baru dapat di-update tanpa uninstall.
   Jangan uninstall aplikasi POS di HP sebelum backup toko tersedia karena data
   lokal dapat ikut hilang.
4. Catat versi toolchain yang sedang lulus (`flutter --version`, Android SDK,
   Java/JDK, serta versi Android Studio) dan pastikan backup toko terbaru dapat
   dibuka.

AI pada **laptop baru** wajib melakukan ini sebelum melanjutkan batch fitur:

1. Install Git, Flutter/toolchain yang sama, Android Studio, Android SDK, dan
   JDK yang kompatibel; jalankan `flutter doctor` hingga dependency penting
   terbaca.
2. Clone repository dari remote; jangan menyalin folder `.git`, `build`, atau
   database development lama secara manual.
3. Kembalikan secret hanya ke file/lokasi lokal yang diabaikan Git, termasuk
   `debug.keystore` ke `C:\Users\<pengguna-baru>\.android\debug.keystore`
   bila diperlukan untuk update aplikasi uji yang sudah terpasang.
4. Pada setiap proyek Flutter jalankan `flutter pub get`, targeted test yang
   relevan, kemudian `flutter test`; hubungkan perangkat dan cek `adb devices`
   sebelum membuat atau memasang APK.
5. Sebelum melanjutkan pekerjaan, konfirmasi repository bersih selain perubahan
   batch aktif, remote mengarah ke GitHub yang benar, dan tidak ada `.env`,
   private key, keystore, backup toko, atau database nyata yang ikut ter-track.

## Status saat ini

- Gerbang Rekonsiliasi Source Tahap 8-16 selesai pada 2 September 2026; source,
  test, schema, dan histori aktif kembali selaras pada seluruh domain yang
  diperiksa.
- Rekonsiliasi Batch 1 selesai: role runtime `Admin` dan fixture `Admin Demo`
  sudah dihapus, perubahan stok memakai sesi persisten dan validasi ulang akun
  Manager aktif dari database, serta audit stok/backup/pembatalan menyimpan UID
  aktor persisten.
- Rekonsiliasi Batch 2 selesai: seluruh mutasi kategori/produk menerima sesi,
  memvalidasi ulang Manager aktif, menolak entity lintas toko, dan menulis audit
  atomik dengan UID aktor dari database.
- Rekonsiliasi Batch 3 selesai: API akun generik dihapus, bootstrap/recovery
  Manager dipisahkan dari manajemen Kasir, seluruh operasi manajemen akun
  memvalidasi Manager aktif di repository, dan audit lifecycle dasar atomik.
- Rekonsiliasi Batch 4 selesai: rename Kasir hanya dapat dilakukan Manager
  aktif, memvalidasi target toko/role, menulis audit atomik yang aman, dan
  mempertahankan snapshot nama pada transaksi lama.
- Rekonsiliasi Batch 5 selesai: Manager dapat mengubah nama dan PIN sendiri;
  PIN lama diverifikasi, PIN baru unik, audit atomik, dan sesi persisten tetap
  valid tanpa membuka jalur recovery tersembunyi.
- Rekonsiliasi Batch 6 selesai: login memiliki limiter progresif non-blocking,
  pesan autentikasi generik, countdown UI, dan reset state setelah login benar.
- Rekonsiliasi Batch 7 selesai penuh: lifecycle lock menjaga sesi sebelum 5
  menit, mengunci dan membersihkan keranjang mulai 5 menit, membersihkan PIN,
  dan smoke test Android nyata telah dikonfirmasi lulus.
- Rekonsiliasi Batch 8 selesai: penyimpanan pengaturan printer/desain struk
  hanya dapat dilakukan Manager aktif, diaudit secara aman dan atomik, serta
  tombol pengaturan tidak tersedia bagi Kasir tanpa membatasi fungsi cetak.
- Rekonsiliasi Batch 9 selesai: penyimpanan tema memakai revalidasi Manager
  aktif, no-op, audit atomik, sesi eksplisit dari shell, dan error persist
  generik tanpa membocorkan exception database.
- Rekonsiliasi Batch 10 selesai: penggantian lisensi hanya oleh Manager aktif,
  token divalidasi sebelum mutasi, audit bebas secret, dan kegagalan audit
  memulihkan token lama dari secure storage.
- Rekonsiliasi Batch 11 selesai: wiring produksi backup/restore, diagnostik,
  dan cleanup startup dipulihkan; permission state, timeout, duplicate lock,
  sanitasi error, dan toleransi logo rusak printer kembali aktif.
- Database POS: schema runtime 24; generated database sudah diregenerasi dan
  migrasi seluruh snapshot lama tetap menjaga data.
- Tahap 17.1: deterministic performance seed selesai.
- Tahap 17.2: baseline desktop selesai.
- Tahap 17.3: agregasi SQL, timezone toko, identitas grouping, query plan, dan
  benchmark desktop terbaru sudah diverifikasi; laporan 56.448 transaksi
  tercatat 2.427 ms dari baseline 8.809 ms.
- Tahap 17.4 Batch 1: ukuran decode gambar produk disesuaikan ukuran tampilan;
  profiling memory nyata belum selesai.
- Tahap 17.4 Batch 2: hasil filter katalog digunakan ulang sampai kriterianya
  berubah; snapshot/ringkasan riwayat tidak dialokasikan dan dihitung ulang
  setiap rebuild; widget riwayat kini dibangun secara lazy.
- Tahap 17.4 Batch 3: profile APK diuji pada V2322 Android 15; startup, login,
  katalog/pencarian, riwayat, perpindahan halaman, dialog akun, checkout tanpa
  menyimpan, dan diagnostik backup stabil tanpa pola pertumbuhan memory linear.
  Menu Manager `Backup & Restore` yang sebelumnya tidak memiliki pintu masuk
  kini tersedia dan teruji tanpa memberi akses kepada Kasir.
- Tahap 17.4 Batch 4: harness profile perangkat memakai database khusus yang
  terpisah dari database produksi. Pada V2322, katalog 10.000 produk termuat
  652 ms, laporan SQL 100.000 transaksi 2.934 ms, dan riwayat 100.000
  transaksi 7.043-7.768 ms. Checksum database produksi tetap identik.
- Tahap 17.4 Batch 5: riwayat dibatasi 50 transaksi per halaman, ringkasan
  seluruh filter dihitung di SQL, dan hasil memakai urutan deterministik.
  Profile ulang 100.000 transaksi memuat halaman pertama 362 ms; warm reload
  102-119 ms dan PSS akhir 195,3 MB, dibanding 498-528 MB sebelum pagination.
  Ordering halaman kedua, pencarian, scroll, reload, checksum database produksi,
  pembersihan database uji, serta pemulihan APK normal telah lulus.
- Tahap 17 performance dan memory: **selesai**.
- Tahap 18 Batch 1: shell, dashboard, dan Kasir telah diaudit pada portrait,
  landscape, breakpoint 899/900/1199/1200 px, keyboard inset, serta text scale
  1,3-2,0. Overflow drawer, AppBar, header dashboard, dan kartu produk sudah
  diperbaiki; semantics label, target sentuh, dan fokus keyboard telah diuji.
- Tahap 18 Batch 2: error backup/restore kini aman, terpusat, diumumkan sebagai
  live region, dan menyediakan retry; exception pemilih file serta kegagalan
  storage/permission tidak lagi lolos atau membocorkan path internal. Dialog
  restore, reset PIN, hapus foto, dan penggantian lisensi menyebut target serta
  dampaknya; progress memiliki semantic label dan overflow dropdown kategori
  produk sudah diperbaiki.
- Tahap 18 UX, responsive, dan accessibility: **selesai**.
- Tahap 19 Batch 1: fondasi backup/restore registry License Manager selesai.
  Format JSON terversi memuat schema serta checksum SHA-256; seluruh toko,
  lisensi, dan relasi renewal divalidasi sebelum restore. Ekspor file atomik,
  proteksi overwrite, transaksi restore, rollback, kompatibilitas schema lama,
  dan batas ukuran/jumlah record sudah diuji.
- Tahap 19 Batch 2 selesai: UI backup/restore registry, pemilih lokasi sistem,
  validasi sebelum konfirmasi overwrite, refresh registry, progress aksesibel,
  dan error aman sudah diterapkan. Save ke Download, pemilihan ulang file, dan
  restore telah dikonfirmasi berhasil pada V2322 Android 15.
- Tahap 19 Batch 3 selesai: fondasi backup private signing key terenkripsi
  memakai Argon2id dan AES-256-GCM, metadata terautentikasi, fingerprint,
  validasi ketat, ekspor atomik, serta proteksi overwrite. Recovery rehearsal
  generated test key berhasil memulihkan kemampuan signing tanpa secret produksi.
- Tahap 19 Batch 4 selesai: UI backup/recovery signing key memakai passphrase,
  konfirmasi fingerprint dan overwrite, pemilih file sistem, serta error aman.
- Tahap 19 Batch 5 selesai: penggantian perangkat menjaga satu identitas toko,
  memindahkan instalasi secara atomik, dan mempertahankan rantai lisensi.
- Tahap 19 Batch 6 selesai: reminder kedaluwarsa, filter 7/30/60 hari,
  pengurutan, status penerbitan ulang, serta ekspor CSV/JSON tanpa token.
- Tahap 19 Batch 7 selesai: jejak audit persisten mencatat penerbitan,
  perpanjangan, penggantian perangkat, operasi signing key, backup, dan restore
  melalui metadata allowlist tanpa token, private key, atau passphrase.
- Tahap 19 Batch 8 selesai: app lock PIN enam digit memakai hash PBKDF2 di secure
  storage, cooldown percobaan gagal, lifecycle lock lima menit, `FLAG_SECURE`,
  guard operasi key/lisensi, serta pembersihan clipboard sensitif.
- Tahap 19 Batch 9 selesai: unlock biometrik opsional memakai dialog native,
  gagal tertutup, mencegah dialog ganda, dan selalu mempertahankan fallback PIN.
- Tahap 20 Batch 1 selesai: release matrix waktu dan format token mengunci skew
  10 menit, expiry boundary, jam mundur/maju, timezone, whitespace, dan truncation.
- Tahap 20 Batch 2 selesai: secure storage gagal secara tertutup dengan pesan
  aman, identity hilang memutus lisensi lama, update mempertahankan identity dan
  token, serta restore perangkat lain tidak mengimpor lisensi.
- Tahap 20 Batch 3 selesai: lisensi berhenti tepat saat expiry tanpa grace
  period, shell aktif dan resume memvalidasi ulang lisensi, akses data serta
  backup ditutup, dan jalur aktivasi/perpanjangan tetap tersedia.
- Tahap 20 license validation edge cases: **selesai**.
- Tahap 20A fitur pesanan suara gratis: Batch 1-4 dan hardening 5A-5G sudah
  diterapkan; uji perangkat lifecycle terbaru dinyatakan berjalan lancar.
  Batch 5H-1 pembentuk ringkasan dan Batch 5H-2 integrasi TTS native sudah
  diterapkan; fungsi pembacaan lulus uji perangkat dan penyelarasan tempo ringkas
  terpasang. Batch 5I tambah produk manual ke draft suara beserta pemeriksaan UI
  perangkat telah lulus.
  Contextual biasing, indikator sinyal, recovery sesi, countdown, satuan produk,
  fonetik nama majemuk, serta sinkronisasi kesiapan/terminal native sudah lulus.
  Batch 6 dan benchmark perangkat telah lulus dengan keputusan **GO**:
  47/50 first-attempt, 50/50 resolved, dan nol pelanggaran keselamatan.
  Pengenalan native, parser katalog, penggabungan sesi, koreksi draf, pemisahan
  hasil, serta konfirmasi atomik ke keranjang sudah tersedia. Tahap 20A selesai.
- Tahap 20B Batch 1-5 selesai: pencarian langsung, scanner kamera/pusat
  Perangkat/HID, serta pembacaan isi keranjang telah lulus otomatis dan gerbang
  perangkat yang tersedia. Audit Log Manager juga telah lulus implementasi,
  analyzer, full suite, build, verifikasi APK, dan pemeriksaan perangkat. Produk
  Terlaris beserta empat periodenya juga telah lulus pemeriksaan UI perangkat;
  Tahap 20C Batch 1A-1D-B telah selesai, termasuk gerbang suara nyata
  multi-satuan. Batch 2 telah lulus pemeriksaan perangkat. Batch 3A-3F juga
  selesai pada schema, migrasi, state machine, hitungan kas, otorisasi, audit,
  pengaturan shift Manager, pembukaan shift Manager/Kasir, mutasi kas,
  penutupan kas, pengaitan checkout, riwayat dan rekonsiliasi shift, identitas
  shift pada riwayat/struk, serta bukti ringkasan tutup kasir. Nama shift pada
  struk juga dapat diaktifkan atau dinonaktifkan oleh Manager; gerbang visual
  terakhir Batch 3F lulus pada 19 September 2026. Mesin diskon, promo beli X
  dapat Y, persistence/otorisasi/audit, serta integrasi checkout atomik Batch
  4A-4D juga telah selesai. Batch 4E, 4F-A, dan 4F-B beserta gerbang manual
  pembatalan promo telah lulus; Batch 4 resmi selesai. Tahap 20C Batch 5A
  fondasi nomor meja opsional telah lulus manual. Batch 5B sudah menghubungkan
  input meja ke checkout, snapshot, riwayat, pencarian, struk, dan backup;
  penyelarasan UI portrait/landscape telah lulus manual, sedangkan gerbang alur
  penuh Batch 5B masih menunggu. Batch 6A telah mengunci urutan formula dan
  mesin hitung integer untuk biaya layanan, pajak, serta pembulatan. Batch 6B
  telah menambahkan persistence schema v18, audit atomik, dan pengaturan khusus
  Manager serta lulus gerbang manual. Batch 6C telah menghubungkan formula ke
  preview dan checkout atomik, menaikkan schema ke v19, menyimpan snapshot biaya
  transaksi, dan lulus gerbang manual. Batch 6D telah menyelaraskan snapshot
  tersebut pada riwayat, preview/data printer, serta agregat laporan. Batch 6E
  telah mengoreksi urutan satuan-jumlah pada Pesanan Suara dan jumlah hadiah
  Buy X Get Y pada ringkasan suara Keranjang; verifikasi otomatis, build, serta
  smoke test perangkat lulus dan gerbang dengar nyata menunggu pengguna. Batch
  6F-A telah memperjelas rincian promo pada Keranjang dengan bahasa operasional,
  target produk/satuan aktual, jumlah beli/hadiah, status Otomatis/Opsional,
  serta nilai hemat. Batch 6F-B sudah memisahkan barang berbayar dan hadiah
  gratis pada Preview Struk/ESC-POS tanpa mengubah nilai transaksi; gerbang
  visual struk dinyatakan lulus oleh pengguna pada 24 September 2026. Batch 7A
  sudah mengunci aturan customer/member, menambahkan schema/repository member,
  otorisasi Manager/Kasir, audit, serta migrasi v20. Batch 7B sudah menambahkan
  UI pengelolaan member Manager serta pemilihan member/nama nonmember di
  Keranjang. Penyempurnaan Batch 7B membuat program member opsional per toko,
  default aktif, hanya dapat diubah Manager, dan tidak menghapus data saat
  dinonaktifkan melalui migrasi v21. Integrasi diskon, persistence, dan snapshot
  checkout dilanjutkan terpisah pada Batch 7C.
- Batch 7C sudah menghubungkan pilihan customer/member ke preview dan checkout,
  menerapkan aturan diskon tanpa stacking, menyimpan snapshot transaksi melalui
  schema v22, serta menyelaraskan struk, riwayat, laporan, dan backup. Gerbang
  otomatis lulus; pemeriksaan transaksi nyata pada perangkat masih menunggu.
- Batch 7D sudah mengganti pembulatan Rp1.000 menjadi Rp100 terdekat khusus
  tunai dan menambahkan donasi kembalian opsional dengan persetujuan pembeli.
  Schema v23, migrasi, checkout, struk, riwayat, laporan, shift, backup, full
  regression, build, dan pemasangan perangkat lulus; gerbang transaksi nyata
  dinyatakan lulus oleh pengguna pada 29 September 2026.
- Batch 8A sudah menambahkan pengelolaan gambar QRIS khusus Manager, schema v24,
  audit aman, lifecycle aset, resolusi path lintas perangkat, cleanup orphan,
  serta backup/restore. Analyzer, 685 test, build, checksum APK, smoke startup,
  dan gerbang visual pengaturan QRIS pada perangkat sudah lulus.
- Batch 8B sudah menampilkan QRIS tervalidasi pada dialog pembayaran, menyediakan
  perbesar, dan mewajibkan konfirmasi penerimaan manual. File kosong/hilang/rusak
  serta checkout QRIS tanpa konfirmasi ditolak sebelum transaksi ditulis.
  Hotfix landscape HP membuat dialog pembayaran, pilihan satuan, dan transaksi
  berhasil ringkas/dapat digulir tanpa menutupi isi atau tombol. Analyzer, 693
  test, build, checksum, instalasi, dan smoke startup lulus; gerbang scan QR nyata
  serta pembayaran QRIS perangkat masih menunggu.
- Tahap 21-25: belum selesai.

Perkiraan saat ini tersisa **5-8 batch** sampai production/publish:

- gerbang manual yang masih terbuka serta Batch 8-10 Tahap 20C untuk QRIS,
  Excel, disaster recovery, dan rekonsiliasi terpadu;
- 1-2 batch Android hardening, versioning, build, signing, dan CI;
- 4-6 batch QA/UAT, dokumentasi, release candidate, pilot, dan rollout.

Rentang ini akan dikoreksi berdasarkan hasil audit dan pengujian perangkat;
temuan release blocker dapat menambah batch, sedangkan bagian yang sudah lengkap
tidak akan dikerjakan ulang.

## Urutan eksekusi

### 0. Gerbang rekonsiliasi source dan histori

- Inventaris setiap klaim Tahap 8-16 terhadap source dan test aktual.
- Pulihkan session/actor UID, authorization Manager, penghapusan Admin legacy,
  account lifecycle, login hardening, audit, backup, maintenance, dan printer
  hanya pada bagian yang benar-benar hilang.
- Cocokkan migration/generated schema tanpa mengedit generated file manual.
- Inventaris ulang jumlah test; angka histori 319 tidak boleh dipakai untuk
  source aktif yang saat ini menjalankan 291 test.

Checkpoint yang sudah lulus:

- Batch 1 — identitas aktor dan stok: runtime hanya mengenal Manager/Kasir;
  sesi tanpa UID, Kasir, Manager nonaktif, UID asing, dan toko berbeda ditolak
  tanpa mutasi stok, movement, atau audit.
- Audit checkout membuktikan `cashierUserUid`; audit stok, backup, dan
  pembatalan membuktikan `actorUserUid` dari akun database.
- Targeted test 31/31, backup+migration 46/46, analyzer bersih, dan full suite
  final 265/265.
- Batch 2 — katalog: sesi Manager diteruskan dari shell ke ViewModel dan
  repository; create/update/activate/deactivate kategori/produk serta perubahan
  gambar diaudit dalam transaksi yang sama.
- Kasir, sesi Manager tanpa identitas, dan entity toko lain ditolak tanpa
  mutasi/audit; status sama menjadi no-op dan path gambar tidak masuk audit.
- Targeted katalog 11/11, regression lintas modul 47/47, analyzer bersih, dan
  full suite final 272/272.
- Batch 3 — fondasi akun: `createInitialManager` dipisahkan dari
  `createCashier`; daftar akun, create/status/reset PIN Kasir wajib memakai sesi
  Manager persisten dan revalidasi database.
- Manager kedua, Kasir, sesi legacy, UID palsu, toko lain, dan Manager nonaktif
  ditolak tanpa mutasi; audit create/status/reset bebas PIN/hash/salt dan
  kegagalan audit me-roll back perubahan.
- Targeted akun 28/28, regression setup/recovery/login 43/43, analyzer bersih,
  dan full suite final 277/277.
- Batch 4 - rename Kasir: Manager aktif dapat mengganti nama Kasir satu toko;
  nama di-trim/dibatasi, target Manager/toko lain ditolak, nama identik menjadi
  no-op, dan audit `user_renamed` atomik tanpa PIN/hash/salt.
- Snapshot transaksi lama tetap memakai nama lama dan transaksi baru mengambil
  nama terbaru dari database; targeted 22/22, regression 18/18, migration
  38/38, analyzer bersih, dan full suite final 282/282.
- Batch 5 - self-service Manager: target selalu UID sesi sendiri; rename
  memakai batas nama/no-op, sedangkan perubahan PIN memverifikasi PIN lama dan
  mewajibkan PIN baru berbeda serta unik terhadap seluruh akun toko.
- Audit `user_renamed`/`user_pin_changed` atomik dan bebas secret; sesi aktif
  tetap valid, login lama ditolak setelah ganti PIN, dan login baru memuat nama
  terbaru. Targeted 23/23, regression 27/27, migration 38/38, analyzer bersih,
  dan full suite final 287/287.
- Batch 6 - attempt limiter login: kegagalan ke-5 dan seterusnya ditunda
  2/4/8/16 hingga maksimum 30 detik tanpa blocking UI; format invalid dan
  percobaan selama delay tidak menambah counter, login benar mereset state.
- PIN salah/akun nonaktif memakai pesan generik dan error internal tidak bocor;
  targeted 12/12, regression 36/36, migration 38/38, analyzer bersih, dan full
  suite final 291/291.
- Batch 7 - lifecycle auto-lock dan proteksi PIN: background kurang dari 5 menit
  mempertahankan sesi/keranjang; mulai 5 menit mengunci langsung ke Login dan
  membersihkan keranjang tanpa mengulang setup/lisensi.
- Logout manual memakai jalur pembersihan yang sama; PIN login dibersihkan dan
  kembali tersembunyi saat background, sedangkan autocorrect, suggestion, dan
  autofill dimatikan. Targeted 20/20, regression 33/33, migration 38/38,
  analyzer bersih, dan full suite final 296/296.
- Smoke test Android Batch 7 dikonfirmasi lulus pada perangkat target.
- Batch 8 - konfigurasi printer/desain struk: repository mewajibkan sesi
  Manager aktif dan menulis audit `printer_settings_updated` atau
  `receipt_design_updated` dalam transaksi yang sama dengan update.
- Audit tidak memuat nama/alamat printer maupun path logo; no-op tidak menambah
  audit, Kasir ditolak tanpa mutasi, dan fungsi cetak tetap tersedia. Targeted
  23/23, regression 33/33, migration 38/38, analyzer bersih, dan full suite
  final 301/301.
- Batch 9 - penyimpanan tema: sesi Manager diteruskan eksplisit dari shell dan
  direvalidasi di repository; update tema serta audit `theme_settings_updated`
  atomik, nilai identik no-op, dan kegagalan persist menjadi error generik.
- Kasir/Manager nonaktif ditolak tanpa mutasi; targeted 18/18, regression
  32/32, migration 38/38, analyzer bersih, dan full suite final 303/303.
- Batch 10 - penggantian lisensi: Manager aktif direvalidasi sebelum token
  diproses; token baru divalidasi sebelum secure storage diubah dan audit
  `license_replaced` hanya menyimpan metadata lisensi non-secret.
- Token identik no-op, token invalid mempertahankan token lama, dan kegagalan
  audit memicu rollback kompensasi secure storage. Targeted 12/12, regression
  lisensi 55/55, regression lintas domain 23/23, migration 38/38, analyzer
  bersih, dan full suite final 307/307.
- Batch 11 - penutup rekonsiliasi: provider produksi backup, restore, tujuan
  Download, dan diagnostik kembali tersedia; cleanup aset yatim kembali
  berjalan saat startup hanya pada dua folder aset aplikasi.
- Printer kembali membedakan izin ditolak/permanen, menawarkan Pengaturan
  Aplikasi, memberi timeout seluruh operasi gateway, menahan write timeout
  sampai native selesai, mencegah duplicate print, menyanitasi error plugin,
  dan melewati logo korup.
- Targeted audit/backup/restore/maintenance/printer 59/59, analyzer bersih,
  full suite final 316/316, dan APK debug berhasil dibangun.

Checkpoint berikutnya:

- Tahap 18 Batch 1 lulus: seluruh halaman shell Manager ikut dilayout pada
  portrait/landscape, breakpoint 899/900/1199/1200 px, keyboard inset 320 px,
  dan text scale 1,3-2,0 tanpa overflow setelah empat akar masalah diperbaiki.
  Targeted/regression 25/25, analyzer bersih, dan full suite 324/324.
- Tahap 18 Batch 2 lulus: state error/retry backup dan restore, kegagalan
  permission/storage, semantic progress/live region, serta dialog konfirmasi
  berisiko telah ditutup. Dialog pembatalan transaksi dan nonaktif akun yang
  sudah jelas dipertahankan; restore, reset PIN, hapus foto, dan lisensi
  diperjelas. Targeted 17/17, analyzer bersih, dan full suite 327/327.
- Tahap 19 Batch 1 lulus: backup registry mendukung round-trip kosong, renewal,
  500 toko/lisensi, schema lama, serta file atomik. Checksum korup, duplikat,
  relasi rusak, dan overwrite implisit ditolak sebelum mutasi; kegagalan insert
  paksa me-roll back penghapusan dan insert. Targeted 9/9, License Manager
  109/109, aplikasi Kasir 327/327, dan analyzer bersih.
- Tahap 19 Batch 2 gerbang otomatis lulus: backup memakai dialog lokasi sistem
  dan nama `.klrbackup` bertimestamp; hasil menampilkan lokasi yang dipilih.
  Restore memvalidasi file sebelum dialog, menyebut nama/jumlah/dampak, meminta
  overwrite eksplisit, mempertahankan signing key, lalu memuat ulang registry.
  Targeted service+UI 14/14, License Manager 114/114, aplikasi Kasir 327/327,
  analyzer bersih, `git diff --check` bersih, dan APK debug berhasil dibangun.
  Gerbang manual native pada V2322 Android 15 juga lulus: backup disimpan ke
  Download, file dipilih kembali, dan restore menampilkan hasil berhasil.
- Tahap 19 Batch 3 lulus: dokumen signing key dienkripsi dengan AES-256-GCM dan
  key dari Argon2id 19 MiB/2 iterasi/parallelism 1; salt, nonce, metadata, MAC,
  fingerprint, batas ukuran, passphrase, struktur, serta parameter KDF divalidasi.
  Targeted 8/8, License Manager 122/122, aplikasi Kasir 327/327, analyzer dan
  whitespace/no-secret scan bersih. Rehearsal hanya membuat Ed25519 test key saat
  runtime, memulihkannya ke vault kosong, lalu membuktikan sign/verify berhasil.
- Pengukuran jank/frame memakai alat Flutter karena `dumpsys gfxinfo` tidak
  menghitung frame `SurfaceView` setelah reset pada perangkat ini; verifikasi
  tersebut dijadwalkan kembali dalam device UAT sebelum release candidate.

Exit gate: source, test, schema, dan `fitur yg sudah diterapkan.md` kembali
konsisten; analyzer dan full test hijau.

### 1. Tutup Tahap 17 - Performance dan memory

- Pastikan agregasi laporan memakai batas hari timezone toko dan rentang akhir
  eksklusif.
- Uji batas tengah malam, transaksi batal, ordering, dan hasil agregasi.
- Benchmark ulang dataset 100.000 transaksi dan periksa query plan/integritas.
- Profile katalog 10.000 produk, laporan besar, cache gambar, daftar panjang,
  controller/provider/dialog disposal, serta login/logout berulang.
- Jalankan profile di tablet target untuk startup, login, katalog, pencarian,
  checkout, history, laporan, backup, dan memory.

Exit gate: benchmark tercatat, tidak ada regresi hasil, analyzer dan full test
hijau, serta device profile selesai.

Status: **lulus pada 3 September 2026**. Dataset besar, query laporan,
pagination riwayat, memory, startup, alur utama, dan integritas database produksi
telah diverifikasi. Pengukuran frame Flutter tetap menjadi cek UAT perangkat,
bukan alasan menahan pekerjaan UX Tahap 18.

### 2. Tahap 18 - UX, responsive, dan accessibility

- Landscape, portrait, batas 900/1200 px, keyboard terbuka, dan text scale
  1.3-2.0.
- Semantic label, tooltip, tap target, focus, contrast, dan status nonwarna.
- Loading, empty, error, retry, permission/storage failure.
- Konfirmasi cancel transaksi, nonaktif akun, restore, reset, gambar, dan
  penggantian lisensi menyebut target serta dampaknya.

Exit gate: widget size/semantics/focus/dialog tests dan full regression hijau.

Status: **lulus pada 3 September 2026**. Dua batch menutup matriks layout,
semantics, fokus, target sentuh, state error/retry, kegagalan penyimpanan, dan
dialog berisiko dengan analyzer bersih serta full suite 327/327.

### 3. Tahap 19 - Operasional License Manager

- Backup/restore registry beserta checksum, relasi, validasi, dan rollback.
- Backup private key terenkripsi dengan autentikasi, strong KDF, integrity,
  fingerprint, dan overwrite protection.
- Recovery rehearsal hanya memakai generated test key.
- Device replacement workflow dan hubungan lisensi lama/baru.
- Reminder/filter/export metadata tanpa token.
- Audit issuance, renewal, replacement, key, backup, dan restore.
- App lock PIN, lifecycle lock, secure screen, serta clipboard protection.
- Biometric unlock opsional dengan fallback PIN.

Exit gate: registry round-trip dan private-key recovery rehearsal lulus tanpa
secret pada log/artifact.

Status: **Batch 1-9 lulus dan Tahap 19 selesai**. License Manager selalu melewati
app lock sebelum membuka shell. Biometrik yang sudah terdaftar dapat dipakai
melalui dialog native `biometricOnly`; cancel, perangkat tanpa biometrik, dan
exception platform tetap terkunci serta mempertahankan PIN aplikasi sebagai
fallback. Permintaan bersamaan hanya dapat membuka satu dialog. Targeted 14/14,
License Manager 168/168, Kasir 327/327, kedua analyzer, build, install, launch,
unlock biometrik, dan fallback PIN pada V2322 Android 15 lulus. Pekerjaan
berikutnya adalah Tahap 20 Batch 1: matriks edge case validasi lisensi tanpa
mengubah kebijakan expiry secara diam-diam.

### 4. Tahap 20 - License validation edge cases

- Signature, app ID, installation ID, schema, waktu berlaku, dan expiry.
- Jam perangkat mundur/maju, perubahan timezone, secure storage rusak/hilang,
  token terpotong/ber-spasi, update aplikasi, dan restore database.
- Tetapkan kebijakan expiry, backup saat expired, dan emergency read-only.

Exit gate: protocol dan release matrix kedua aplikasi hijau.

Status: **Batch 1-3 lulus dan Tahap 20 selesai**. Lima test boundary Batch 1 membuktikan skew tepat 10
menit masih diterima dan setelahnya `notYetValid`; lisensi valid sampai sesaat
sebelum expiry dan ditolak tepat pada expiry; jam mundur/maju menghasilkan status
aman; perubahan timezone tidak mengubah instant; whitespace token diterima dan
token terpotong ditolak sebagai malformed. Targeted Kasir 19/19, protocol License
Manager 12/12, full Kasir 332/332, full License Manager 168/168, dan kedua
analyzer bersih. Tidak ada kebijakan grace period atau akses expired yang diubah.

Batch 2 menormalkan exception secure storage menjadi `LicenseException` aman
tanpa fallback penyimpanan lain. Startup tetap terkunci dan menyediakan retry
tanpa membocorkan detail native. Identity yang hilang dibuat ulang sehingga token
lama ditolak sebagai `wrongInstallation`; token tidak dihapus diam-diam. Instance
service baru tetap membaca identity dan token yang sama. Backup hanya membawa hash
identity; persiapan restore pada perangkat berbeda tidak mengimpor identity maupun
token sumber. Targeted Kasir 54/54, full Kasir 337/337, full License Manager
168/168, dan kedua analyzer bersih. APK debug juga berhasil dipasang sebagai
update pada V2322 Android 15; verifikasi layar otomatis menunjukkan aplikasi
langsung ke Login, bukan Aktivasi, sehingga secure identity/token dan database
pengguna terbukti bertahan.

Batch 3 menetapkan kebijakan produksi tanpa grace period: lisensi valid sampai
sesaat sebelum `expiresAt` dan hard-stop tepat pada `expiresAt`. Shell memeriksa
lisensi sebelum data ditampilkan, menjadwalkan pemeriksaan tepat pada expiry,
serta memeriksa ulang saat aplikasi resume. Lisensi invalid atau storage gagal
menutup seluruh shell, membersihkan keranjang, dan kembali ke aktivasi. Backup
tidak tersedia ketika expired; emergency read-only tidak dibuka pada versi 1
karena belum ada autentikasi dan shell terbatas yang aman untuk jalur tersebut.
Pesan expiry mengarahkan operator memasukkan lisensi baru. Targeted 53/53, full
Kasir 338/338, full License Manager 168/168, dan kedua analyzer bersih.

Batch berikutnya adalah Tahap 20A Batch 1: audit kemampuan speech-to-text native
dan proof-of-concept terukur pada perangkat Android target.

### 5. Tahap 20A - Pesanan suara gratis dan opsional

Tahap ini disisipkan tanpa mengubah nomor Tahap 21-25 yang sudah menjadi acuan.
Tujuannya adalah memperoleh hasil terbaik tanpa biaya API, backend suara, atau
akun cloud berbayar. Mesin pengenal suara memakai layanan native perangkat
melalui package Flutter `speech_to_text`; parsing, pencocokan katalog, draf, dan
konfirmasi tetap diproses lokal di aplikasi.

Ketentuan tetap:

- Kasir manual dan seluruh transaksi inti tetap bekerja penuh tanpa fitur suara.
- Fitur hanya aktif jika izin mikrofon, locale Bahasa Indonesia, dan recognition
  service tersedia saat runtime; kegagalan suara tidak boleh menghambat kasir.
- Audio tidak disimpan ke file/database, tidak masuk log/audit, dan tidak
  dikirim oleh aplikasi ke backend milik kita.
- Hasil suara tidak pernah langsung mengubah keranjang. Semua hasil masuk ke
  draf dan baru diterapkan setelah konfirmasi pengguna.
- Tidak menambah Huawei HMS, model AI lokal besar, backend, atau provider cloud
  lain sebelum hasil pengujian membuktikan solusi native gratis tidak memadai.
- Dukungan produksi hanya boleh diklaim pada kombinasi perangkat, OS, locale,
  dan recognition service yang benar-benar telah diuji.
- Batas awal yang harus divalidasi adalah 30 detik per sesi ucapan dan 10 menit
  untuk satu mode pesanan suara. Batas aktual dapat lebih pendek karena layanan
  perangkat; seluruh hasil final tetap digabung ke satu draf sampai `Selesai`.
- Pengenal native Android dapat menutup satu sesi setelah jeda singkat dan tidak
  menjamin perekaman kontinu. Pesanan panjang karena itu dibagi menjadi beberapa
  sesi pendek; hasil yang sudah final wajib dipertahankan dan digabung, sedangkan
  pengguna mendapat aksi `Lanjut Bicara` sampai menekan `Selesai`.

#### Batch 1 - Audit kemampuan dan proof-of-concept native

- Audit dependency, minimum SDK, permission mikrofon, privacy declaration, dan
  lifecycle aplikasi sebelum menambah package.
- Integrasikan adapter terkecil untuk inisialisasi, daftar locale, mulai,
  partial result, final result, stop, cancel, status, dan error yang aman.
- Pilih locale Indonesia dari locale yang benar-benar dilaporkan perangkat;
  jangan mengasumsikan `id-ID` selalu tersedia.
- Batasi satu sesi pengenalan awal pada 30 detik. Jika pengguna masih berada di
  mode suara, sesi berikutnya boleh dimulai terkontrol setelah sesi sebelumnya
  benar-benar selesai; tidak boleh ada listener atau dialog ganda.
- Buat layar/harness internal sementara untuk mengukur transkrip tanpa menyentuh
  katalog atau keranjang.
- Uji unit adapter dengan gateway palsu serta uji nyata pada V2322 untuk izin
  diterima/ditolak, tersedia/tidak tersedia, online/offline, diam, cancel, dan
  dua sesi berurutan.

Exit gate: tidak ada crash/hang/listener bocor; transkrip Bahasa Indonesia dapat
dikumpulkan pada perangkat target dan seluruh kasir manual tetap hijau.

#### Batch 2 - Parser pesanan lokal yang deterministik

- Tetapkan format ucapan yang mudah: satu atau beberapa pasangan
  `<jumlah> <nama produk>`, misalnya `dua Aqua 350 ml, satu mi cup rawit`.
- Normalisasi huruf, spasi, tanda baca, angka Indonesia, satuan, dan kata pengisi
  tanpa mengubah nama produk di database.
- Cocokkan nama aktif menggunakan nama resmi dan alias lokal yang eksplisit;
  gunakan pencarian mirip hanya untuk menghasilkan kandidat, bukan memilih
  produk ambigu secara diam-diam.
- Bedakan hasil cocok, ambigu, tidak ditemukan, jumlah tidak valid, stok kurang,
  dan produk nonaktif. Tetapkan batas jumlah yang aman.
- Parser bersifat pure Dart dan tidak bergantung plugin/platform agar dapat diuji
  lengkap dengan katalog serta transkrip yang sama setiap kali.
- Uji nama mirip, nama yang merupakan bagian nama lain, angka kata/digit,
  pengulangan produk, koreksi kuantitas, stok, produk nonaktif, dan input kosong.

Exit gate: seluruh fixture parser dan kasus negatif hijau; tidak ada hasil
ambigu atau tidak dikenal yang berubah menjadi item pesanan otomatis.

#### Batch 3 - Pengelola sesi pesanan suara

- Bentuk state eksplisit: idle, meminta izin, mendengarkan, memproses, berhenti,
  gagal, dan selesai.
- Gabungkan final transcript dari beberapa sesi pendek sampai pengguna menekan
  `Selesai`; partial transcript hanya untuk tampilan dan tidak boleh diduplikasi.
- Tangani silence timeout, network/service unavailable, permission denied,
  lifecycle pause/resume, logout, auto-lock, rotasi, dan tombol ditekan berulang.
- Pertahankan transkrip/draf yang sudah valid ketika satu sesi gagal dan sediakan
  retry atau lanjut manual.
- Uji state transition, race stop/cancel/result, disposal, auto-lock, logout,
  dan pemulihan error dengan fake recognizer serta fake clock bila diperlukan.

Exit gate: tidak ada rekaman setelah keluar/terkunci, tidak ada hasil ganda, dan
state dapat selalu kembali ke idle secara aman.

#### Batch 4 - UI draf, koreksi, dan konfirmasi keranjang

- Tambahkan tombol suara opsional pada halaman Kasir dengan status dukungan
  layanan perangkat yang jelas, tanpa memenuhi toolbar atau area produk.
- Tampilkan indikator mikrofon aktif, transkrip sementara, durasi/status, tombol
  berhenti, selesai, batal, dan retry dengan semantic label yang benar.
- Setelah selesai, tampilkan draf berisi produk, jumlah, subtotal, kandidat
  ambigu, item tidak ditemukan, serta alasan item tidak dapat dipakai.
- Pengguna dapat memilih kandidat, mengubah jumlah, menghapus item, atau kembali
  berbicara sebelum menekan `Tambahkan ke Keranjang`.
- Saat konfirmasi, baca ulang produk dan stok aktual; perubahan keranjang
  diterapkan satu kali dan tetap mengikuti aturan keranjang yang sudah ada.
- Uji widget pada portrait/landscape, text scale, keyboard, empty/error/loading,
  fokus, semantics, rotasi, serta regresi halaman produk dan keranjang.

Exit gate: tidak ada overflow; seluruh perubahan keranjang eksplisit, dapat
ditinjau, dan tidak dapat diterapkan dua kali.

#### Batch 5 - Akurasi katalog, privasi, dan hardening gratis

- Batch 5A mengirim istilah Kamus toko, nama produk aktif, dan alias katalog
  sebagai contextual bias ke recognizer Android 13+ sebelum setiap sesi. Daftar
  dibersihkan, dideduplikasi, dan dibatasi 200 frasa agar aman untuk Intent.
  Kemampuan ini bersifat advisory: recognizer/OS yang tidak mendukung atau
  mengabaikannya wajib tetap memakai alur lama tanpa menggagalkan sesi.
- Alur pengguna, parser, pengelola lifecycle anti-stuck, dan aturan konfirmasi
  keranjang tidak berubah pada Batch 5A.
- Uji nyata Batch 5A menunjukkan contextual bias efektif saat perangkat dekat:
  hampir seluruh nama, ukuran, dan jumlah berhasil dipertahankan. Pada jarak jauh
  recognizer dapat kehilangan kata serta angka sebelum hasil mencapai parser;
  parser dilarang menebak kembali informasi yang sudah hilang.
- Batch 5B menambahkan penjaga hasil berisiko tanpa langkah baru bagi kasir.
  Confidence final yang rendah atau hasil selesai tanpa satu pun item katalog
  siap memunculkan peringatan untuk mengulang lebih dekat. Draf bermasalah tetap
  dapat diperiksa/koreksi dan tidak pernah dipilih diam-diam.
- Batch 5C menampilkan indikator kejernihan sinyal selama mikrofon aktif dengan
  callback level suara native yang sudah tersedia. Rentang sinyal sesi dipakai
  untuk memberi label `lemah`, `cukup`, atau `jelas`; sinyal lemah ikut menandai
  hasil untuk diulang lebih dekat. Batas awal wajib dikalibrasi di V2322 dan
  callback terminal/sesi lama tidak boleh memperbarui indikator.
- Percobaan melepas `SpeechRecognizer` setelah setiap sesi ditarik kembali karena
  memutus hasil final dan merusak state tombol pada uji perangkat. Hotfix revisi
  mempertahankan recognizer pada sesi sukses, mendaur ulangnya hanya saat Android
  melaporkan `recognizer busy`, dan mencoba ulang satu kali bila start native
  ditolak. Penutupan hotfix menunggu uji transkrip/tombol 3-5 sesi lalu soak test
  minimal 20 sesi pada V2322.

- Sediakan alias produk yang dikelola lokal hanya jika hasil benchmark nyata
  membutuhkannya; nama resmi tetap menjadi sumber utama.
- Urutkan kandidat secara deterministik dan tampilkan perbedaan nama agar pilihan
  tidak menyesatkan. Jangan menampilkan persentase keyakinan palsu bila platform
  tidak memberikannya secara konsisten.
- Pastikan permission diminta saat fitur dipakai, bukan saat startup, disertai
  penjelasan penggunaan mikrofon dan kemungkinan pemrosesan oleh layanan sistem.
- Audit source, log, database, backup, dan crash path untuk memastikan tidak ada
  audio maupun transkrip sensitif yang tersimpan tanpa keputusan eksplisit.
- Tambahkan rate/debounce lokal, batas durasi sesi, batas jumlah item, dan pesan
  aman untuk error platform.
- Jalankan targeted, regression Kasir, analyzer, full suite, dan build Android.

Exit gate: privacy/error/lifecycle tests hijau, tidak ada data suara persisten,
dan aplikasi tetap dapat digunakan sepenuhnya ketika fitur dimatikan.

#### Batch 6 - Benchmark perangkat dan keputusan dukungan produksi

- Siapkan sedikitnya 50 skenario pesanan representatif: suasana tenang dan bising,
  nama pendek/mirip, merek, ukuran, jumlah kata/digit, pengulangan, koreksi, dan
  produk yang tidak ada.
- Catat perangkat, OS, locale, recognition service, kondisi jaringan, ucapan,
  transkrip, hasil parser, koreksi pengguna, waktu respons, dan pass/fail tanpa
  menyimpan rekaman audio.
- Gerbang keselamatan mutlak: nol perubahan keranjang tanpa konfirmasi dan nol
  item ambigu yang dipilih diam-diam.
- Target penerimaan awal: minimal 90% baris pesanan cocok pada percobaan pertama
  di kondisi toko normal dan minimal 95% dapat diselesaikan melalui kandidat
  atau koreksi draf tanpa kembali ke pencarian manual.
- Uji wajib V2322 Android. Uji iPhone hanya dilakukan jika rilis iOS masuk scope
  dan tersedia Mac/signing/perangkat nyata. Huawei tanpa GMS hanya dinyatakan
  didukung jika runtime menyediakan recognizer Bahasa Indonesia dan perangkat
  nyata lulus matriks yang sama; selain itu UI memberi fallback manual.
- Jika target akurasi tidak tercapai, perbaiki alias/parser/UX lalu ulangi
  benchmark. Jangan menambah layanan berbayar secara diam-diam. Jika tetap gagal,
  fitur berstatus eksperimen atau dimatikan default dan tidak menahan rilis POS.

Exit gate: hasil benchmark dan batas dukungan terdokumentasi, build perangkat
lulus, serta keputusan GO/NO-GO fitur suara dibuat tanpa mengubah GO/NO-GO kasir
manual.

Status: **Batch 1 dan Batch 2 selesai pada 6 September 2026; Batch 3 selesai
pada 7 September 2026 setelah uji lifecycle perangkat lulus; Batch 4 selesai
pada 8 September 2026; Batch 5A lulus gate otomatis/build/perangkat dan uji
jarak dekat pada 9 September 2026; Batch 5B lulus gate otomatis/build/perangkat
dan menunggu verifikasi peringatan; Batch 5C lulus gate otomatis/build/perangkat
dan menunggu kalibrasi indikator pada uji suara dekat/jauh nyata**.
Potongan partial yang berbeda tidak lagi ditimpa hasil final pendek; final yang
hanya mengulang ekor partial juga tidak diduplikasi, termasuk ketika hasil tersebut
datang setelah status sesi selesai. Hasil native lama ditolak setelah transkrip
dihapus, sedangkan beberapa sesi yang sah tetap digabung.
Harness membaca produk aktif dari database produksi secara read-only. Parser menerima
format `nama produk + jumlah` tanpa satuan wajib, menormalkan variasi `mili`, dapat
memisahkan tiga produk berurutan walau tanda baca dihapus recognizer, dan mencocokkan
dua kata pembeda jika hasil katalog unik; hasil ambigu tidak dipilih diam-diam. Draf
menyamakan angka ucapan dengan digit saat membandingkan revisi recognizer. Kata jeda
seperti `hemm` dibuang, sedangkan kata percakapan seperti `sama` dan `deh` hanya
dibersihkan pada batas nama agar tidak merusak nama produk sah. Produk yang memang
tidak ada di katalog tetap tidak boleh ditebak menjadi produk lain. Draf
memilih alternatif hasil Android yang menghasilkan kecocokan katalog paling lengkap
dan menerima salah eja dekat pada potongan unik minimal dua kata, termasuk variasi
`rawit bintik` untuk `Rawit Bingit`; hasil yang jauh tetap tidak dipaksakan cocok. Draf
tetap menandai nama tidak ditemukan, jumlah, stok, produk nonaktif, serta
ketidakcocokan satuan tanpa mengubah keranjang. Harness kini menyediakan aksi
`Koreksi` untuk hasil tidak dikenal: pengguna memilih produk aktif yang benar, konflik
ditolak, daftar pilihan menampilkan harga jual dan satuan katalog, lalu variasi
ucapan dipakai sebagai alias selama sesi uji dan draf langsung
dihitung ulang. Hasil ambigu kini memiliki tombol `Pilih`; dialog hanya menampilkan
kandidat terkait beserta harga dan satuannya, lalu pilihan langsung menyelesaikan
draf sekaligus dipakai kembali selama sesi yang sama. Setiap draf yang sudah cocok
menampilkan jumlah, harga satuan, dan subtotal sementara tanpa mengubah keranjang;
status stok/satuan/nonaktif juga tetap memperlihatkan harga referensi. Persistensi alias, audit,
backup, dan pemilih katalog skala besar baru
boleh ditambahkan setelah perilaku ini lulus pada perangkat. Katalog uji perangkat
ditambah secara idempoten sebanyak 122 produk: nama gabungan dipisah agar dapat
diuji sebagai produk berbeda, harga rentang memakai nilai tengah, dan seluruh produk
baru aktif dengan pelacakan stok dimatikan. Pengguna menyatakan alur ucapan,
katalog, kandidat, koreksi, harga, dan subtotal draf lulus pada V2322; Batch 1–2
sudah dicatat pada `fitur yg sudah diterapkan.md`.

Implementasi Batch 3 menambahkan state eksplisit idle, meminta izin, siap,
mendengarkan, memproses, berhenti, gagal, dan selesai. Aksi `Selesai Pesanan`
mengunci draf sampai dibersihkan; error tetap mempertahankan hasil yang sudah
terbaca. Saat aplikasi inactive/hidden/paused/detached, mikrofon dibatalkan satu
kali, partial disimpan, dan hasil native terlambat ditolak. Targeted voice 44/44,
analyzer bersih, full suite Kasir 386/386, build debug lulus, dan uji lifecycle
nyata pada V2322 Android 15 dinyatakan lulus oleh pengguna. Batch 3 ditutup;
enam batch tersisa tetap dikerjakan satu per satu.

Batch 4 mengintegrasikan halaman pesanan suara ke Kasir melalui tombol mikrofon
opsional. Draf dapat mengubah jumlah dan menghapus item sebelum konfirmasi.
Konfirmasi hanya tersedia setelah pesanan diselesaikan, menggabungkan produk yang
sama, membaca ulang katalog aktif dan stok terbaru, serta menerapkan seluruh draf
secara atomik dengan proteksi klik ganda. Batch ini belum ditutup sebelum seluruh
gerbang otomatis dan uji perangkat yang relevan lulus.

Perbaikan lanjutan Batch 4 membuat status `notListening` segera membuka sesi
berikutnya dan menambahkan normalisasi morfologi Indonesia yang dibatasi konteks
kata perintah serta token katalog. Regresi terkait 39/39, full suite 394/394,
analyzer, build, dan pemasangan update V2322 lulus; uji suara nyata masih ditunggu.

Parser Batch 4 juga membedakan angka nama produk dari kuantitas dan memulihkan
gabungan recognizer yang unik, misalnya `Nabati 2004` menjadi `Nabati 2000 x 4`.
Kasus ambigu tidak dipilih otomatis. Targeted voice 51/51, full suite 397/397,
analyzer, build, dan pemasangan update V2322 lulus; uji nyata masih ditunggu.

Varian produk pecahan pada Batch 4 kini dinormalisasi berdasarkan katalog.
Bentuk `1/2`, `1/4`, `2/3`, simbol pecahan, serta ucapan seperti `setengah`,
`separuh`, `seperempat`, dan `tiga per empat` diperlakukan sebagai bagian nama
produk; angka setelah varian tetap menjadi kuantitas. Parser 25/25, targeted
voice 54/54, full suite 400/400, analyzer, build, pemasangan, dan pembukaan pada
V2322 lulus. Uji suara pecahan nyata masih ditunggu sebelum Batch 4 ditutup.

Kamus istilah operasional per toko kini tersedia pada tab `Kamus`. Manager dapat
menambah, mengubah, dan menghapus frasa seperti `sekilo -> 1 kg` atau
`dua setengah -> 2500`; Kasir hanya dapat melihat dan menggunakan aturan aktif.
Aturan bawaan pecahan/satuan ditampilkan sebagai referensi tetapi tidak dapat
ditimpa. Penyimpanan langsung aktif dan tombol `Terapkan Ulang Kamus` memuat ulang
aturan tanpa melatih mesin, membangun APK, atau menghubungkan laptop. Substitusi
dilakukan satu kali dengan frasa terpanjang lebih dahulu agar hasil pengganti
tidak ditafsirkan ulang oleh aturan lain. Semua mutasi divalidasi ulang sebagai
Manager aktif, dibatasi ke toko sesi, dan diaudit secara atomik.

Data kamus disimpan pada schema Kasir versi 10 sehingga ikut terlindungi oleh
backup/restore database yang sudah ada. Migrasi seluruh jalur versi 1-9 ke versi
10, targeted voice 61/61, targeted database/migrasi/backup 75/75, seluruh test
Kasir 416/416, analyzer, dan `git diff --check` lulus. Build debug, pemasangan
update, migrasi database, dan pembukaan aplikasi pada V2322 juga berhasil tanpa
crash. Tidak ada dependency baru atau perubahan format token/protocol. Uji
persistence aturan dan hak akses Manager/Kasir masih menjadi gerbang manual
sebelum Batch 4 ditutup.

Perbaikan terbaru Batch 4 menghubungkan kamus toko dengan koreksi transkripsi
yang sangat mirip, bukan hanya substitusi ejaan persis. Koreksi hanya dilakukan
bila kandidat unik dan aman; frasa pendek, aturan bernomor, serta kandidat ambigu
tidak ditebak. Transkrip terkoreksi ditampilkan sebelum konfirmasi. Lifecycle
native juga memakai satu instance pengenal sepanjang umur aplikasi dan memindahkan
listener ke halaman aktif, sehingga pemakaian berulang atau buka-tutup halaman
tidak tertambat ke callback lama. Status `notListening` segera menyimpan segmen dan
membuka `Lanjut Bicara`. Regresi voice 66/66, seluruh test Kasir 421/421, analyzer,
`git diff --check`, build debug, pemasangan, serta pembukaan pada V2322 lulus.
Tidak ada perubahan schema atau dependency. Enam siklus suara nyata dan koreksi
istilah toko masih menjadi gerbang manual sebelum perbaikan ini dinyatakan lulus.

Uji perangkat 8 September menemukan callback final/partial Android dapat datang
setelah `notListening/done` dan sebelumnya mengubah status kembali ke memproses.
Batch 4 sekarang menolak partial terlambat, menerima final terakhir tanpa
mengaktifkan ulang sesi, dan mengunci draf setelah `Selesai Pesanan`. Regresi UI
dan native 22/22, seluruh voice 66/66, seluruh test Kasir 421/421, analyzer,
`git diff --check`, build debug, serta pemasangan V2322 lulus. Verifikasi suara
nyata bahwa status tidak kembali memproses menjadi gerbang terakhir perbaikan ini.

Audit ulang log native V2322 pada 8 September mengoreksi asumsi lifecycle di
atas: `notListening` belum berarti sesi Android sudah dilepas. Pembukaan sesi
terlalu awal terbukti memicu `already in session`, lalu `service's capacity is
full`. Implementasi terbaru menunggu final/terminal, melakukan cleanup native
secara terserialisasi, memberi cooldown 750 ms, menyediakan fallback cleanup tiga
detik, dan membatalkan start tertunda dengan generasi operasi. Perlindungan
callback terlambat dan satu instance pengenal tetap dipertahankan. Regresi voice
68/68, seluruh test Kasir 423/423, analyzer, `git diff --check`, build debug, dan
pemasangan V2322 lulus. Sebelum Batch 4 ditutup, wajib dilakukan 8-10 sesi suara
nyata dan log perangkat harus bebas dari `already in session` serta `capacity is
full`; catatan lama bahwa `notListening` langsung mengaktifkan `Lanjut Bicara`
tidak lagi menjadi perilaku yang benar.

Gerbang lifecycle tersebut kemudian lulus pada V2322: lebih dari dua puluh sesi
`onStartListening` tercatat tanpa `already in session` atau `capacity is full`.
Cleanup terserialisasi, cooldown, fallback terminal, dan generasi operasi menjadi
regresi wajib yang tidak boleh dihapus. Peningkatan sesudahnya menambahkan state
`Menyiapkan mikrofon` setelah tombol ditekan dan baru menampilkan `Mikrofon aktif`
serta mengirim bunyi/getar ketika callback native `listening` diterima. Countdown
tetap sengaja tidak digunakan karena waktu kesiapan berbeda antarperangkat.
Targeted 25/25, seluruh voice 69/69, seluruh test Kasir 424/424, analyzer,
`git diff --check`, build debug, dan pemasangan V2322 lulus. Kejelasan cue serta
masuknya kata produk pertama masih menunggu verifikasi singkat di perangkat.

Fondasi penyelarasan draf berikutnya mempertahankan batas keras antarsesi suara.
`VoiceTranscriptDraft` tidak lagi meratakan sesi selesai dan sesi baru menjadi
satu kalimat; parser menerima pemisah `;` yang sudah menjadi batas klausa.
Regresi `beras sekitar` lalu `beras 1 kg 2` sekarang menjaga bagian gagal tetap
terpisah dan mencocokkan sesi kedua sebagai `Beras 1 kg` berjumlah 2. Targeted
50/50, seluruh voice 70/70, seluruh test Kasir 425/425, analyzer,
`git diff --check`, build debug, dan pemasangan V2322 lulus. Verifikasi dua sesi
nyata kemudian dinyatakan lulus oleh pengguna pada 8 September 2026.

Penyelarasan UI berikutnya memisahkan hasil menjadi `Siap dimasukkan`, `Perlu
konfirmasi`, dan `Tidak dikenali`. Hanya status cocok katalog yang dianggap
siap. Teks tanpa pasangan produk-jumlah kini memblokir konfirmasi sampai kasir
memilih `Abaikan` secara eksplisit; ucapan baru mereset keputusan tersebut.
Widget halaman 11/11, seluruh voice 72/72, seluruh test Kasir 426/426, analyzer,
`git diff --check`, build debug, pemasangan, dan pembukaan pada V2322 lulus.
Uji keterbacaan ketiga kelompok dan pengaman `Abaikan` pada perangkat menjadi
gerbang terakhir sebelum Batch 4 ditutup dan Batch 5 dimulai.

Hotfix lifecycle Batch 5C tanggal 9 September kemudian mengoreksi kegagalan
berulang yang masih muncul walaupun APK terbaru sudah terpasang. Timer Dart dari
sesi terminal sekarang selalu dibersihkan agar tidak menghentikan sesi baru;
callback error Android dijamin tiba sebelum status terminal; dan sesi tanpa hasil
menampilkan kegagalan yang dapat ditindaklanjuti, bukan transkrip kosong tanpa
alasan. Retry start tetap hanya satu kali dan recognizer hanya didaur ulang pada
`ERROR_RECOGNIZER_BUSY`. Targeted 35/35, seluruh test Kasir 435/435, analyzer,
`git diff --check`, build, kecocokan SHA-256 APK lokal/perangkat, pemasangan, dan
pembukaan V2322 lulus. Gerbang yang masih terbuka sebelum melanjutkan roadmap
adalah 5 sesi suara singkat lalu minimal 20 sesi berurutan pada perangkat; setiap
pesan error harus dicatat karena sekarang tidak lagi tersembunyi.

Gerbang hotfix lifecycle tersebut kemudian lulus pada 21 sesi suara berurutan di
V2322. Batch 5D menambahkan banner countdown fixed pada tiga detik terakhir yang
dihitung sejak callback native `listening`: `3`, `2`, `1`, lalu status
memproses. Banner tetap terlihat ketika halaman digulir, memberi satu getaran
tanpa bunyi, tidak menghalangi sentuhan, dan selalu dibersihkan pada terminal,
cancel, error, background, atau sesi baru. Parser dan lifecycle recognizer yang
sudah lulus 21 sesi tidak diubah. Targeted 36/36, seluruh test Kasir 436/436,
analyzer, `git diff --check`, build, pemasangan, dan kecocokan SHA-256 APK
lokal/perangkat lulus. Gerbang manual Batch 5D adalah 3-5 sesi untuk memeriksa
posisi fixed, ketepatan countdown, getaran, dan cleanup banner sebelum tahap
berikutnya.

Uji perangkat pertama Batch 5D menemukan countdown tidak muncul karena timer
internal `speech_to_text` mengurangi waktu berjalan lebih dari sekali setiap
hasil parsial. Koreksi mempertahankan durasi awal sesi dan hanya menghitung sisa
waktu satu kali, tanpa mengubah batas jeda lima detik maupun alur recognizer yang
sudah lulus 21 sesi. Regresi baru mensimulasikan ucapan kontinu hingga detik
ke-27 dan memverifikasi sesi tidak berhenti dini serta banner fixed `3-2-1`.
Setelah koreksi: tes suara 82/82, seluruh tes 436/436, analyzer dan diff bersih,
serta APK lokal/perangkat identik dengan SHA-256
`87e87d4af6e5e066cc884c936058246bf3d0f9d6aa0a0ccd29b97474786a5d40`.
Gerbang manual yang tersisa adalah bicara kontinu mendekati 30 detik; bila kasir
diam lima detik, sesi berakhir melalui batas jeda sehingga countdown batas
maksimum memang tidak tampil.

Penyesuaian perangkat berikutnya memperpanjang banner peringatan menjadi lima
detik: `5-4-3-2-1`, lalu memproses. Tidak ada perubahan pada batas sesi, batas
jeda, parser, maupun lifecycle recognizer. Tes suara 82/82, seluruh tes 436/436,
analyzer dan diff lulus; APK lokal/perangkat identik dengan SHA-256
`a30acf3749c25f102ab39d118a233871ce30d3c599e8ceb012e8d6ccdb1d6584`.

Batch 5E memperbaiki pemilihan produk bernama sama berdasarkan satuan yang
diucapkan. Varian Botol dan Dus kini dapat dipilih langsung dari ucapan, termasuk
bentuk `aqua nya` dan `aquanya`; tanpa satuan atau jika beberapa kandidat tetap
sama kuat, konfirmasi masih diwajibkan. Tes parser 31/31, seluruh tes suara
84/84, seluruh tes aplikasi 438/438, analyzer dan diff lulus. APK
lokal/perangkat identik dengan SHA-256
`0bd9e26ccca3f9c29b5d9d4f83020a2d5491e8958c5a9273d450f6b79a41507f`.
Gerbang manual tersisa adalah verifikasi satu ucapan varian Botol dan satu varian
Dus pada katalog nyata.

Batch 5F menambahkan pencocokan fonetik aman untuk nama katalog majemuk yang
dilebur layanan suara. Pembandingan mengabaikan spasi dan memperlakukan `x`
sebagai bunyi `ks`, sehingga `milaxa` dan `mielaksa` dapat diarahkan ke
`Mie Laksa`; nama serta transkrip asli tidak diubah. Aturan hanya berlaku untuk
nama multi-kata dengan panjang minimum, tetap memakai ambang kemiripan, dan
tetap meminta konfirmasi bila beberapa kandidat sama kuat. Tes parser 32/32,
tes suara 85/85, seluruh tes aplikasi 439/439, analyzer dan diff lulus. APK
lokal/perangkat identik dengan SHA-256
`1ebd462396d4c9ce7f4baa7221714320f9e2709325103f93c768bde3cf38d2cb`.
Gerbang manual tersisa adalah pengulangan ucapan `Mie Laksa 2` pada perangkat.

Batch 5G mengaudit kegagalan `recognizer busy`, hasil kosong, dan error generik
yang kembali muncul pada suara dekat. Tanda siap kini menunggu callback Android
`onReadyForSpeech`, hasil final ditutup oleh terminal native tanpa cancel paksa,
cleanup error hanya mempunyai satu pemilik, error sesi lama diabaikan, dan
destroy recognizer busy tidak dapat mengenai instance baru. Tes service/halaman
39/39, seluruh tes suara 87/87, seluruh tes aplikasi 441/441, analyzer, diff,
serta build Kotlin lulus. APK debug dipasang dan dibuka pada V2322; SHA-256 lokal
dan perangkat identik:
`5a6bb16ea98f7b9fa59134858992bbd4631eaa890d27ca2dc2e417795fe4096a`.
Gerbang manual wajib mengulang minimal 20 sesi nyata, termasuk lanjut bicara,
hapus transkrip, dan cancel, sebelum hardening lifecycle ini dinyatakan lulus.

Uji perangkat berikutnya dinyatakan berjalan lancar oleh pengguna. Batch 5H-1
kemudian menambahkan pembentuk ringkasan domain yang hanya mengambil produk Siap
dimasukkan, memakai koreksi jumlah, menggabungkan produk sama, dan membacakan
subtotal per barang tanpa harga satuan maupun total draft. Angka, unit umum, dan
pecahan dinormalisasi menjadi ucapan Indonesia; setiap item dibagi menjadi nama,
jumlah/satuan, dan harga agar TTS dapat memberi jeda eksplisit. Test pembentuk
5/5, targeted 44/44, seluruh suara 92/92, seluruh aplikasi 446/446, analyzer,
serta diff lulus. Batch murni ini tidak memerlukan build perangkat. Batch 5H-2
akan menambahkan TTS native, kontrol baca/berhenti, kecepatan sedang, jeda nyata,
dan eksklusi audio dua arah dengan mikrofon.

Batch 5H-2 kemudian menghubungkan ringkasan tersebut ke `flutter_tts 4.2.5` dan
engine Text-to-Speech bawaan perangkat. Tombol **Dengarkan Ringkasan** hanya
membaca produk Siap dimasukkan; **Berhenti Membaca** menghentikan engine secara
eksplisit. Kecepatan dikunci sedang-cepat `0.55`; jeda nama ke jumlah 100 ms,
jumlah ke harga 50 ms, dan antarbarang 200 ms. Pilihan hanya memuat voice
ber-locale Indonesia,
mendahulukan voice offline, menandai voice yang membutuhkan jaringan, dan hanya
menampilkan gender bila metadata OS tersedia. Mode otomatis tetap menjadi
pilihan awal.

Mikrofon, koreksi draf, penyelesaian, hapus transkrip, dan konfirmasi keranjang
dikunci selama pembacaan. Status **Menghentikan Suara** mempertahankan kunci
tersebut sampai callback `stop` native benar-benar selesai; lifecycle background
juga menghentikan audio. Service TTS 4/4, targeted gabungan STT/TTS/UI 49/49,
seluruh suara 97/97, seluruh aplikasi 451/451, analyzer, dan diff lulus. APK debug
berhasil dibangun, dipasang sebagai update, dan dibuka pada V2322 Android 15;
Google TTS terdeteksi dan SHA-256 APK lokal/perangkat identik:
`f778819a06b66d547150a4e81b2ee302b9bef8c9c8ace11296c18c6565531c2f`.
Uji perangkat awal dinyatakan berjalan sangat bagus: subtotal terbaca tanpa total
transaksi dan lifecycle kontrol pembacaan benar. Atas masukan operasional,
tambahan jeda lama 350/650 ms kemudian dipadatkan menjadi 180 ms dari nama ke
jumlah, 100 ms dari jumlah ke harga, dan 320 ms antarbarang tanpa mengubah
kecepatan suara maupun alur STT. Targeted 49/49, seluruh suara 97/97, seluruh
aplikasi 451/451, analyzer, dan diff lulus. APK debug dipasang ulang serta dibuka
pada V2322; SHA-256 lokal/perangkat identik:
`2cccbb815ce52c81c2d323d286bd185d181baf3d249981993057fb1d5b6080aa`.
Gerbang manual terakhir hanya membandingkan tempo ringkasan minimal lima barang.

Penyesuaian tempo kedua menaikkan kecepatan secara konservatif dari `0.5` ke
`0.55` dan memadatkan jeda tambahan 180/100/320 ms menjadi 100/50/200 ms.
Pemisahan utterance nama, jumlah, harga, dan barang berikutnya tetap
dipertahankan. Targeted 49/49, seluruh aplikasi 451/451, analyzer, dan diff
lulus. APK debug dipasang ulang serta dibuka pada V2322; SHA-256 lokal/perangkat
identik:
`0c5f89106f7d4d85bf5a2d878d90e287d9906cc6b9d06677abf476ab6255d008`.
Gerbang manual hanya menilai kenyamanan tempo pada minimal lima barang.

Batch 5I menambahkan **Tambah Produk Manual** di tab Pesanan Suara. Kasir dapat
mencari katalog aktif dengan informasi harga/satuan/stok, memilih produk, dan
memasukkan jumlah 1-999. Validasi stok dilakukan sebelum produk menjadi baris
**Siap dimasukkan**. Pilihan manual disimpan sebagai baris draft berindeks stabil,
tidak ditebak ulang oleh parser, dan digabung dengan hasil suara pada koreksi
jumlah, TTS, serta konfirmasi keranjang yang sama. Targeted 23/23, seluruh suara
99/99, seluruh aplikasi 453/453, analyzer, dan diff lulus. APK debug dipasang dan
dibuka pada V2322; SHA-256 lokal/perangkat identik:
`4900dc831a3cf2e1df7d7ae960c9c9e6135eb745c573e60c70f67e46eefd089c`.
Gerbang perangkat cari→pilih jumlah→edit draft→konfirmasi telah **lulus pada
10 September 2026**. Pengujian ulang memakai APK profile juga memastikan
pencarian produk dan input jumlah berjalan lancar tanpa patah-patah yang terlihat
pada APK debug. Batch 5I selesai.

Batch 6 menambahkan harness benchmark profile yang terpisah dari navigasi
produksi. Matriks berisi 50 skenario tenang/berisik dan dekat/jauh, termasuk
nama/ukuran mirip, beberapa produk, angka kata/digit, serta satu produk yang
tidak ada. Harness membaca katalog aktif dan Kamus toko secara read-only,
mengukur latensi, tidak mengubah keranjang, dan tidak menyimpan audio maupun
hasil ke database. Keputusan GO memerlukan tepat 50 hasil, minimal 90% lulus
pertama, minimal 95% dapat selesai melalui draf, dan nol produk salah yang
dipilih otomatis. Hasil yang kehilangan baris atau kuantitas tidak dapat
ditandai selesai lewat koreksi draf.

Percobaan perangkat pertama mencapai skenario 26 tetapi dibatalkan sebagai data
keputusan: audit menemukan harness saat itu hanya mengirim nama produk sebagai
konteks recognizer, belum istilah Kamus dan alias seperti halaman produksi, serta
belum memberi jeda setelah banyak panggilan beruntun. Harness koreksi kini memakai
konteks yang sama dengan produksi, memberi pendinginan 30 detik setiap 10 hasil
dan setelah status layanan sibuk, serta dapat menyalin laporan sementara tanpa
persistensi database.

Unit/widget benchmark koreksi lulus **7/7**, seluruh aplikasi **460/460**,
analyzer bersih, dan APK profile koreksi berhasil dipasang serta dibuka ulang
pada V2322. SHA-256 lokal dan perangkat identik:
`51746e2878933b525629b78eb829eb86fb0c576ca461c9f42bfb6d78f52655ed`.
Tahap 20A belum ditutup sampai pengujian ulang 50 skenario perangkat disalin dan
dinilai.

Uji ulang berikutnya menemukan kegagalan transkrip kosong sejak skenario 1.
Log perangkat memastikan mikrofon dan izin bekerja, sedangkan Google
RecognitionService menolak sesi karena listener lama masih aktif dan kapasitas
layanan penuh. Plugin lokal telah diperbaiki agar `ERROR_CLIENT`, seperti
`ERROR_RECOGNIZER_BUSY`, menghancurkan recognizer yang tertahan; lapisan Dart
menandainya sebagai kondisi sibuk yang dapat dipulihkan dan harness memberi
pendinginan 30 detik. Speech Services perangkat direset satu kali tanpa
menghapus data. Targeted recognizer/benchmark **31/31**, full suite **460/460**,
analyzer bersih, APK profile terpasang, dan hash lokal/perangkat identik:
`011d9c9d7cac3d22d766a7ea8c584f442bee97ba05d27469afd20d941bb60fbc`.
Gerbang manual dimulai kembali setelah pengguna memastikan skenario 1 kembali
menghasilkan transkrip.

Uji berikutnya menemukan hasil sementara `beras medium 5 kg jika` ditimpa hasil
final yang lebih pendek menjadi `beras medium 5 kg`, sehingga jumlah hilang.
Pada 11 September 2026 harness diselaraskan dengan aplikasi utama memakai
`VoiceTranscriptDraft`. Kandidat homofon `jika` hanya dikoreksi menjadi `tiga`
bila penilaian parser terhadap katalog menjadi lebih valid; tidak ada koreksi
global dan tidak ada penggunaan jawaban skenario sebagai jalan pintas. Targeted
parser/transcript/benchmark **47/47**, full suite **462/462**, analyzer bersih,
dan APK profile telah terpasang di V2322 dengan SHA-256 lokal/perangkat identik:
`3e59050890e30301603aeb19f7be57af23baf9d056a8d2f3a2870cb62811fb29`.
Validasi manual skenario 1-2 masih menjadi gerbang sebelum pengumpulan ulang 50
skenario diteruskan.

Laporan lengkap kemudian diaudit dan dipertahankan sebagai data baseline.
Preflight lama hanya memuat 14 produk, sedangkan matriks membutuhkan 69 produk
unik. Benchmark kini mengunci perekaman serta menampilkan daftar bila produk
wajib hilang/tidak aktif; evaluator juga menolak `resolvedInDraft` bila target
tidak tersedia. Pencocokan potongan tengah nama diperketat agar produk asing
tidak otomatis diarahkan ke produk katalog lain.

Replay seluruh transkrip lama terhadap katalog lengkap menghasilkan **43/50
(86%)** first-attempt, **46/50 (92%)** dapat diselesaikan, dan **0** pelanggaran
keselamatan. Hanya VF-01, VF-02, VF-04, dan BF-09 yang perlu diucapkan ulang;
VN-15, BF-06, dan BF-07 cukup diselesaikan melalui koreksi draf. Seed perangkat
menambahkan 122 produk tanpa menimpa data lama.

Mode retest terarah kemudian ditambahkan melalui build-time define sehingga
hanya ID skenario gagal yang ditampilkan, tetap dalam urutan matriks; ID tidak
dikenal ditolak. Targeted benchmark **11/11**, full suite **466/466**, dan
analyzer bersih. APK profile VF-01/VF-02/VF-04/BF-09 terpasang di V2322,
terverifikasi menampilkan **Skenario 1 dari 4** dengan tombol rekam aktif, serta
hash lokal/perangkat identik:
`a5475124ea2432a7101595fbfb9628ca7b510e8e233881805b65cc65b59caa1c`.
Uji manual terarah lulus **4/4 first-attempt**, termasuk BF-09 pada kondisi
berisik dan jauh. Setelah hasil baru menggantikan empat kegagalan baseline,
hasil final 50 skenario menjadi **47/50 (94%) first-attempt**, **50/50 (100%)
resolved**, **0** pelanggaran keselamatan, dan rata-rata latensi sekitar
**13.185 ms**. Seluruh exit gate terpenuhi; keputusan final Batch 6 adalah
**GO** tanpa mengulang 50 skenario. APK profile aplikasi Kasir utama telah
dikembalikan ke perangkat sebagai update dan terbuka pada Dashboard tanpa
menghapus data. SHA-256 lokal/perangkat identik:
`6f7dd8f35b01c1b0c024e5a353e64466dea2553316080d327a1e97e905c336cf`.

### 5B. Tahap 20B - Penyempurnaan operasional Kasir utama

Tahap ini menutup fitur harian prioritas utama yang dapat memakai fondasi
sekarang tanpa lebih dahulu mengubah rumus transaksi. Tahap 21 security
hardening baru dimulai setelah Tahap 20B dan 20C selesai agar fitur tidak
berubah lagi setelah security freeze.

#### Audit 10 usulan fitur per 11 September 2026

| No. | Fitur | Status source saat audit | Penempatan |
| --- | --- | --- | --- |
| 1 | Tambah item manual ke draf suara | **Sudah selesai** dan lulus perangkat | Tahap 20A Batch 5I; tidak dikerjakan ulang |
| 2 | Scanner barcode | **Implementasi tersedia**: kamera dan HID/Keyboard sudah ada; scan kamera lulus, sedangkan gate scanner fisik nyata tertunda sampai alat/OTG tersedia | Tahap 20B Batch 2 (**utama**) |
| 3 | Bacakan isi keranjang tanpa total keseluruhan | **Selesai**: implementasi, test otomatis, dan uji dengar perangkat lulus | Tahap 20B Batch 3 (**utama**) |
| 4 | Export Excel | **Belum ada**; baru ada preferensi tema Excel tanpa generator/export | Tahap 20C Batch 9 (**utama**) |
| 5 | Customer member dan diskon dari Manager | **Belum ada** | Tahap 20C Batch 7 |
| 6 | Produk terlaris di Dashboard Manager | **Selesai**: tab Manager, pintu masuk Dashboard, periode harian/mingguan/bulanan/tahunan, role guard, build, dan pemeriksaan UI perangkat lulus | Tahap 20B Batch 5 (**utama**) |
| 7 | Audit log agar Manager tahu siapa melakukan apa | **Selesai**: halaman Manager, filter, pagination, waktu toko, sanitasi tampilan, audit transaksi selesai, dan pemeriksaan perangkat telah lulus | Tahap 20B Batch 4 (**utama**) |
| 8 | Pencarian produk langsung saat mengetik dan urutan relevansi | **Selesai**: tab Produk memakai debounce dan kedua katalog memakai ranking yang sama | Tahap 20B Batch 1 |
| 9 | Nomor meja opsional | **Implementasi selesai dan terpasang**; gerbang manual transaksi/riwayat/struk menunggu | Tahap 20C Batch 5 |
| 10 | On/off pajak dan biaya layanan | **Implementasi selesai** pada pengaturan, checkout, snapshot, riwayat, struk, dan agregat laporan; gerbang visual Batch 6D menunggu | Tahap 20C Batch 6 |

#### Audit 9 usulan fitur tambahan per 12 September 2026

| No. | Fitur | Prioritas | Penempatan final |
| --- | --- | --- | --- |
| 1 | Satu produk memiliki beberapa satuan, konversi stok, dan harga berbeda | **Utama** | Tahap 20C Batch 1 |
| 2 | Pembulatan otomatis total akhir | **Utama** | Tahap 20C Batch 6; kebijakan tunai Rp100 dan donasi disempurnakan pada Batch 7D |
| 3 | Shift kasir, uang awal/akhir, dan tutup kasir sebelum ganti shift | **Utama** | Tahap 20C Batch 3 |
| 4 | Notifikasi produk masuk melalui ketukan katalog | Penting | Tahap 20C Batch 2 |
| 5 | Notifikasi produk masuk melalui scanner | Penting | Tahap 20C Batch 2 |
| 6 | Diskon transaksi, diskon produk, dan promo beli X dapat Y | Penting | Tahap 20C Batch 4 |
| 7 | Pilih customer/member atau nama customer manual untuk struk | Opsional | Tahap 20C Batch 7 |
| 8 | Tab produk terlaris harian, mingguan, bulanan, dan tahunan | **Utama** | Tahap 20B Batch 5 |
| 9 | Manager memasang gambar QRIS yang ditampilkan saat pembayaran | **Utama** | Tahap 20C Batch 8 |

#### Batch 1 - Pencarian produk langsung dan urutan relevansi

- Tab Produk Manager memperbarui hasil selama pengguna mengetik tanpa Enter.
- Gunakan debounce singkat agar database tidak menerima query beruntun yang
  tidak perlu; perubahan kategori dan status aktif tetap langsung berlaku.
- Urutkan hasil dengan prioritas: kecocokan barcode/kode tepat, nama diawali
  kata pencarian, lalu nama/kode yang hanya mengandung kata tersebut; urutan
  alfabetis menjadi penentu terakhir yang stabil.
- Terapkan urutan relevansi yang sama pada katalog Kasir tanpa merusak filter
  kategori, stok, produk aktif, atau performa katalog besar.

Exit gate: targeted repository/ViewModel/widget lulus, pencarian katalog 10.000
produk tetap responsif, analyzer dan full suite hijau.

Status: **selesai 11 September 2026**. Tab Produk mencari otomatis setelah
debounce 250 ms dan menolak hasil request lama. Repository Produk serta katalog
Kasir memakai comparator yang sama: kode/barcode tepat, nama berawalan, lalu
kecocokan kandungan dengan nama alfabetis dan ID sebagai urutan stabil. Targeted
**23/23**, full suite **469/469**, dan analyzer bersih. Benchmark database
terisolasi 10.000 produk mencatat pencarian spesifik **35 ms** serta pencarian
luas yang mengurutkan 10.000 hasil **426 ms**; integrity check `ok` dan foreign
key violation 0. Schema tetap versi 10 dan test manual tidak diperlukan.

#### Batch 2 - Scanner barcode utama

- Tambahkan pemindai kamera dari halaman Kasir dengan penghormatan pada
  pengaturan `barcodeEnabled`; tetap dukung scanner fisik yang bertindak sebagai
  input keyboard dan pencarian barcode manual.
- Minta izin kamera hanya ketika scanner dibuka, sediakan fallback pencarian
  manual, dan tampilkan pesan aman untuk izin ditolak/permanen.
- Barcode harus cocok tepat dengan satu produk aktif sebelum masuk keranjang;
  produk tidak ditemukan, nonaktif, stok habis, atau scan berulang cepat tidak
  boleh menambah item secara diam-diam.
- Audit dependency, lifecycle kamera, rotasi, pause/resume, dan pelepasan resource.

Exit gate: unit/widget/lifecycle hijau serta scan barcode nyata lulus pada
perangkat target sebelum batch ditutup.

Status: **berjalan per 12 September 2026**. Pemindai kamera, izin hanya saat
halaman scanner dibuka, fallback input manual, pencocokan barcode tepat,
validasi produk aktif/stok, dan pengunci proses ganda sudah selesai. Uji kamera
nyata pada V2322 dinyatakan lancar. Menu Manager **Perangkat** juga sudah
tersedia sebagai pusat uji scanner kamera/HID dan akses pengaturan printer;
pengujian barcode di halaman ini tidak mengubah keranjang. Penangkapan HID
langsung tanpa fokus kolom kini sudah tersedia hanya ketika halaman Kasir aktif;
input tidak direbut ketika pengguna sedang mengetik di form/dialog, Enter/Tab
mengakhiri barcode, dan indikator scanner fisik siap tampil pada toolbar Kasir.
Kolom pencarian memiliki fokus eksplisit agar input manusia/keyboard layar tidak
pernah direbut listener HID. Perubahan inset keyboard portrait juga tidak lagi
mengganti struktur toolbar dan memutus input connection. Analyzer bersih, tes
HID serta regresi pencarian/keyboard portrait terarah lulus, dan full suite
terakhir **480/480**. APK profile perbaikan sudah terpasang pada V2322 dengan
SHA-256
`fa6a3774b8e59c84e7ce61dbe4755ec62109feecace08f159cd23ba58226909a`.
Sisa Batch 2 adalah uji scanner fisik nyata serta opsi operasional scanner yang
memang dibutuhkan hasil uji sebelum Batch 2 ditutup. Schema tetap versi 10.

Pengujian manual berikutnya memastikan pencarian produk sudah lancar pada HP
portrait maupun landscape. Karena scanner fisik dan OTG belum tersedia, gate
HID nyata dicatat **tertunda karena perangkat**, bukan dianggap gagal; pekerjaan
dapat berlanjut ke Batch 3 tanpa menutup gate tersebut.

#### Batch 3 - Pembacaan isi keranjang

- Gunakan kembali pembentuk ringkasan dan service TTS Tahap 20A, bukan membuat
  mesin suara kedua.
- Baca nama produk, jumlah/satuan, dan subtotal setiap baris keranjang dengan
  tempo ringkas; **jangan membaca total keseluruhan keranjang**.
- Pertahankan pilihan suara laki-laki/perempuan yang tersedia, tombol berhenti,
  pencegahan TTS dan mikrofon berjalan bersamaan, serta fallback ketika engine
  suara tidak tersedia.
- Isi yang dibaca harus berupa snapshot keranjang saat tombol ditekan dan tidak
  mengubah kuantitas maupun transaksi.

Exit gate: pembentuk teks deterministik, kontrol lifecycle TTS, targeted/full
test hijau, dan uji dengar minimal 10 barang nyaman pada perangkat.

Status: **selesai 12 September 2026**. Kontrol dengarkan/berhenti dan pilihan suara ditempatkan di header
Keranjang agar tidak mengambil ruang daftar. Pembentuk ringkasan serta service
TTS Tahap 20A digunakan kembali; ucapan berisi nama, jumlah/satuan, dan subtotal
setiap baris dari snapshot saat tombol ditekan, tanpa total keseluruhan dan
tanpa mutasi keranjang. TTS dihentikan sebelum membuka mikrofon Pesanan Suara,
scanner, pembayaran, saat aplikasi masuk latar, dan ketika halaman dilepas.
Fallback engine tidak tersedia sudah aman. Analyzer penuh bersih dan seluruh
test aplikasi **481/481 lulus**. APK profile sudah dipasang sebagai update pada
V2322 dengan hash lokal/perangkat identik
`28ea6239785abb2b68e93e66e9c24668be33d16d04a437d337ee85afbb747fa9`.
Uji dengar manual pada perangkat dinyatakan lancar; tempo dan jeda sama dengan
ringkasan Pesanan Suara.

#### Batch 4 - Halaman Audit Log untuk Manager

- Hubungkan `AuditLogRepository` yang sudah ada ke tab khusus Manager; Kasir
  tidak dapat membuka atau membaca datanya.
- Tampilkan waktu toko, nama/role pelaku, aksi, objek, dan ringkasan aman dengan
  pagination. Sediakan filter tanggal, pelaku, dan jenis aksi yang sudah
  didukung repository.
- Detail tidak boleh membocorkan PIN, hash, salt, token lisensi, private key,
  passphrase, atau data rahasia lain.
- Audit seluruh mutasi saat ini dan setiap batch baru; aksi yang mengubah data
  penting wajib mencatat aktor persisten dan berhasil secara atomik.

Exit gate: authorization negatif, pagination/filter, sanitasi rahasia,
kelengkapan aksi utama, analyzer, dan full suite lulus.

Status 12 September 2026: implementasi selesai. Menu hanya tersedia untuk
Manager dengan identitas persisten. Halaman memuat 50 log per halaman dalam
urutan terbaru, memakai waktu toko, serta memiliki filter aksi, pelaku, dan
periode. Tampilan hanya memakai ringkasan aman dan tidak pernah merender
`detailsJson`. Checkout kini menulis `sale_completed` secara atomik agar aktivitas
Kasir juga terlihat, sementara jejak pembatalan tetap terpisah. Targeted **17/17**,
regresi pembatalan **3/3**, seluruh test aplikasi **485/485**, analyzer penuh,
`git diff --check`, build profile, pemasangan, dan pencocokan hash APK lulus.
Pemeriksaan UI/filter pada akun Manager di V2322 kemudian dinyatakan berjalan
lancar oleh pengguna pada 12 September 2026. Batch 4 selesai; perlindungan akun
Kasir tetap menjadi regression test wajib pada batch berikutnya.

#### Batch 5 - Tab Produk Terlaris Manager

- Gunakan agregasi `topProducts` yang sudah teruji sebagai sumber satu tab
  khusus Manager; Dashboard cukup menampilkan ringkasan/pintu masuk sehingga
  tidak membuat query agregasi kedua.
- Sediakan periode harian, mingguan, bulanan, dan tahunan berdasarkan timezone
  toko. Tampilkan peringkat, nama produk, jumlah terjual, dan omzet produk;
  transaksi batal tidak dihitung.
- Sediakan empty/error/loading state dan layout portrait, tablet, serta
  landscape tanpa memperlambat Dashboard atau laporan.

Exit gate: hasil Dashboard sama dengan laporan, transaksi batal dikecualikan,
semua periode tepat pada batas waktu toko, responsive/accessibility, query plan,
analyzer, full suite, dan pemeriksaan perangkat lulus.

Status 12 September 2026: implementasi selesai dan gerbang otomatis/build lulus.
Tab khusus Manager memakai agregasi `topProducts` yang sama dengan laporan;
halaman hanya dibuat saat dibuka agar Dashboard tidak menjalankan query tambahan.
Periode harian, minggu Senin-Minggu, bulan kalender, dan tahun kalender mengikuti
timezone toko. Daftar menampilkan peringkat, nama/kode, jumlah beserta satuan,
dan omzet; transaksi batal tidak dihitung. Kasir ditolak oleh navigation policy
dan revalidasi repository. Targeted **17/17**, regresi drawer landscape **1/1**,
seluruh test aplikasi **490/490**, analyzer penuh, `git diff --check`, build
profile, pemasangan update, dan pencocokan hash APK lulus. Pemeriksaan UI empat
periode pada V2322 kemudian dinyatakan berjalan baik oleh pengguna. Batch 5
selesai pada 12 September 2026.

### 5C. Tahap 20C - Ekstensi transaksi, shift, promo, dan pelaporan

Tahap ini wajib dikerjakan berurutan. Model satuan dan stok menjadi fondasi
keranjang; promo diselesaikan sebelum pajak dan pembulatan; seluruh nilai yang
memengaruhi transaksi harus disimpan sebagai snapshot. QRIS serta Excel baru
dikerjakan setelah rumus transaksi stabil agar tidak dirombak dua kali.

#### Batch 1 - Satuan, konversi stok, harga, dan barcode bertingkat

- Satu produk memiliki satuan dasar dan beberapa varian penjualan, misalnya
  `Aqua 350 ml`: Botol, Dus, atau Karton, masing-masing dengan harga sendiri.
- Manager menentukan satuan default, faktor konversi terhadap stok dasar, harga,
  status aktif, urutan tampilan, dan barcode opsional yang tetap unik per varian.
- Saat produk diketuk, Kasir memilih satuan lalu jumlah; satuan default boleh
  langsung dipakai jika hanya ada satu pilihan. Keranjang menyimpan identitas
  varian, harga snapshot, faktor konversi, dan kuantitas stok yang dikonsumsi.
- Scanner, Pesanan Suara, pencarian, stok, struk, riwayat, pembatalan, laporan,
  audit, dan backup/restore harus memahami varian yang sama tanpa menggandakan
  produk katalog.
- Sebelum coding, kunci keputusan stok: seluruh varian mengurangi satu stok dasar
  dengan konversi bilangan bulat; varian tanpa konversi tidak boleh dipaksakan.

Exit gate: tabel konversi dan pembulatan stok, barcode unik, harga snapshot,
checkout/pembatalan atomik, migrasi, backup/restore, serta seluruh jalur input
manual/scanner/suara lulus.

Status Batch 1A, 12 September 2026: fondasi data selesai. Schema naik aman dari
10 ke 11 dengan tabel `product_unit_variants`; kolom produk lama tetap menjadi
satuan dasar agar transaksi dan katalog yang sudah berjalan tidak berubah.
Setiap satuan turunan menyimpan nama, faktor konversi bilangan bulat terhadap
stok dasar, harga jual, barcode opsional, status aktif, dan urutan. Repository
Manager menyediakan create/update/aktif-nonaktif secara atomik dengan audit,
memblokir nama satuan ambigu serta barcode ganda antara produk dasar dan varian,
dan menolak Kasir tanpa mutasi. Produk v10 terbukti tetap utuh setelah migrasi;
matriks migrasi **56/56**, regresi katalog/health/backup/recovery **30/30**,
seluruh test aplikasi **503/503**, analyzer penuh, dan `git diff --check` lulus.
Tidak ada dependency baru dan uji perangkat belum diperlukan karena belum ada
UI baru. Batch 1 berikutnya: UI Manager untuk mengelola satuan turunan, kemudian
pilihan satuan dan snapshot pada keranjang/checkout.

Status Batch 1B, 12 September 2026: UI pengelolaan satuan turunan tersedia dari
aksi setiap produk pada tab Produk Manager. Satuan dasar ditampilkan sebagai
referensi stok yang terkunci, sedangkan Manager dapat menambah, mengubah,
mengaktifkan, dan menonaktifkan satuan turunan beserta faktor konversi, harga
jual, barcode opsional, serta urutan tampil. Dialog dibuat responsif termasuk
landscape pendek; seluruh mutasi tetap melewati repository, otorisasi Manager,
validasi, transaksi atomik, dan Audit Log dari Batch 1A. Jalur UI tambah,
ubah, nonaktifkan, aktifkan kembali, dan landscape pendek telah diuji. Targeted
test **22/22**, seluruh test aplikasi **507/507**, analyzer penuh **No issues
found**, `git diff --check`, build profile, pemasangan update tanpa menghapus
data, pembukaan aplikasi, serta pencocokan SHA-256 APK lokal/perangkat lulus.
Hash APK: `24ede6668247591e4b2a72b6157a7d02e16d1679f8f22e5b388ee097fd07feae`.
Pemeriksaan visual/interaksi pada V2322 dinyatakan lulus oleh pengguna. Teks
bantuan konversi juga dibuat netral terhadap jenis satuan: jumlah selalu
dijelaskan sebagai isi satuan dasar, sehingga produk berbasis Kg tidak lagi
menampilkan contoh Dus yang menyesatkan.

Status Batch 1C, 12 September 2026: integrasi inti Kasir dan transaksi selesai.
Kasir kini memilih satuan aktif dan jumlah saat produk mempunyai beberapa
pilihan; produk dengan satu satuan tetap memakai alur cepat. Keranjang dapat
menyimpan satu produk sebagai beberapa baris satuan, memakai harga masing-masing,
dan menampilkan nama satuan pada baris, notifikasi scanner, serta ringkasan TTS.
Barcode satuan turunan langsung memilih varian yang benar. Validasi stok
menjumlahkan seluruh kebutuhan satuan ke stok dasar agar kombinasi Kg/Karung,
Botol/Dus, dan pola serupa tidak dapat menjual melebihi stok.

Schema naik kompatibel dari 11 ke 12 dengan snapshot UID varian, faktor
konversi, dan kuantitas stok pada item penjualan. Checkout menyimpan snapshot
harga/satuan, mengurangi stok dasar secara atomik, dan pembatalan mengembalikan
stok dasar dari pergerakan stok yang sama. Data transaksi lama tetap dapat
dibaca karena kolom baru nullable. Katalog Kasir dan Produk Manager juga
menampilkan rentang harga termurah hingga termahal dari satuan aktif; bila hanya
ada satu harga, tampilan tetap berupa satu harga. Perhitungan rentang memakai
satu query untuk seluruh katalog dan mengabaikan satuan nonaktif. Pemilih satuan
Kasir serta halaman Satuan & Harga menampilkan stok ekuivalen tiap satuan dan
sisa satuan dasar, termasuk sisa stok setelah barang yang belum dibayar masuk
ke keranjang. Kartu produk Kasir juga langsung menampilkan stok dasar tersisa
setelah isi keranjang diperhitungkan tanpa menunggu checkout; misalnya stok 50
Botol menjadi 26 Botol segera setelah 1 Dus berisi 24 Botol ditambahkan. Saat
jumlah dikurangi atau baris dihapus, angka stok kembali otomatis. Targeted
core/migrasi/UI **118/118 lulus**, targeted stok dinamis/cart/UI **41/41 lulus**,
seluruh test aplikasi **527/527 lulus**,
analyzer penuh **No issues found**,
`git diff --check`, build profile, pemasangan update tanpa menghapus data,
pembukaan aplikasi, dan pencocokan SHA-256 APK lokal/perangkat lulus. Hash APK:
`2edad95fac063ae3d263f9bd3ddaeea80f4087aaf6d47858763a3c2a56c58626`.
Gerbang perangkat Batch 1C masih perlu memastikan pilihan satuan, kombinasi dua
satuan dalam keranjang, konversi stok setelah checkout/pembatalan, dan layout
portrait/landscape. Jalur Pesanan Suara akan diselaraskan pada Batch 1D sebelum
seluruh Batch 1 ditutup.

Keputusan stok telah dikunci: model memakai satu stok fisik dasar bersama agar
Botol/Dus atau Kg/Karung tidak dihitung ganda. Ketersediaan satuan turunan
dihitung otomatis dengan pembagian bulat dan menampilkan sisa stok dasar,
misalnya 50 Botol menjadi 2 Dus dengan sisa 2 Botol. Stok terpisah dan mekanisme
transfer/repacking tidak diperlukan untuk alur toko saat ini.

Penyempurnaan input angka 13 September 2026 juga telah diterapkan. Harga beli,
harga jual, uang diterima, stok, faktor isi satuan, dan urutan tampilan kini
menambahkan titik pemisah ribuan secara langsung saat diketik maupun ditempel.
Nilai bertitik dibersihkan kembali menjadi integer sebelum validasi dan
penyimpanan, sehingga tampilan `1.250.000` tetap dihitung sebagai `1250000`.
PIN dan barcode sengaja tidak diformat karena merupakan identifier. Targeted
formatter/form **10/10 lulus**, seluruh test aplikasi **530/530 lulus**, analyzer
penuh **No issues found**, `git diff --check` bersih, dan tidak ada dependency
atau perubahan schema. APK profile dipasang sebagai update dan dibuka pada
V2322; SHA-256 lokal/perangkat identik:
`334984db782c3da66efaf7d057c969025544a5c77f2630e994dee54b381e408a`.

Status Tahap 20C Batch 1D-A, 13 September 2026: audit scanner multi-satuan
selesai. Scanner Kasir melalui kamera, input manual, dan HID sudah memakai
resolver `ProductSaleUnit`. Barcode cukup disimpan pada produk dasar; ketika
produk mempunyai beberapa satuan aktif, scan barcode dasar membuka pemilih
satuan dan jumlah. Produk dengan satu satuan tetap langsung masuk, sedangkan
barcode khusus Dus/Karung tetap opsional dan jika diisi langsung memilih varian
serta harganya. Satu barcode tidak diduplikasi ke dua satuan karena hasil scan
akan ambigu. Proteksi satu proses scan juga mencakup dialog agar callback
kamera/HID ganda tidak membuka pilihan atau menambah barang dua kali. Celah pada
halaman Perangkat telah ditutup: Uji Scanner kini memakai resolver yang sama dan
menampilkan
barcode, harga, serta satuan varian tanpa menambah keranjang. Targeted UI
Kasir/ViewModel **43/43 lulus**, seluruh test aplikasi **532/532 lulus**,
analyzer penuh **No issues found**, dan `git diff --check` bersih. APK profile
dipasang sebagai update dan dibuka pada V2322; SHA-256 lokal/perangkat identik:
`567e02d972467b9f6e69688c8d63bb871f2d0bc093890b6e85ee79e5f1e92fc7`.
Status Tahap 20C Batch 1D-B, 13 September 2026: Pesanan Suara telah memakai
seluruh satuan aktif dari katalog. Nama produk yang sama dipisahkan memakai
identitas satuan persisten; ucapan yang menyertakan `botol`, `dus`, `karung`,
atau nama satuan kustom memilih varian serta harga yang tepat. Ucapan tanpa
satuan tidak ditebak ketika produknya memiliki beberapa satuan: pengguna wajib
memilih kandidat yang menampilkan harga dan satuannya. Bias pengenal suara juga
mencakup kombinasi nama produk dan satuan aktif.

Draf, koreksi kandidat, tambah produk manual, ringkasan suara, dan konfirmasi ke
keranjang kini mempertahankan identitas satuan. Dua satuan dari produk yang sama
tetap menjadi dua baris, sedangkan pengulangan satuan yang sama digabung. Batas
stok dihitung atomik sebagai total stok dasar lintas seluruh satuan; misalnya 1
Botol + 2 Dus berisi 2 Botol membutuhkan 5 stok dasar. Siklus mikrofon tidak
diubah agar kestabilan rekaman sebelumnya tetap terjaga. Targeted parser,
ringkasan, UI suara, dan ViewModel **81/81 lulus**; seluruh test aplikasi
**538/538 lulus**; analyzer penuh **No issues found**; dan `git diff --check`
bersih. Tidak ada dependency atau perubahan schema. APK profile dipasang sebagai
update dan dibuka pada V2322; SHA-256 lokal/perangkat identik:
`9a12b9e55627abaa0b3550c6349112fe0851cc19138ee54196a9b4b19b95c299`.
Pada saat implementasi, gerbang akhir Batch 1D-B menunggu uji suara nyata untuk
produk yang sama dalam satuan berbeda.

Gerbang tersebut dinyatakan **lulus oleh pengguna pada 13 September 2026**:
produk dengan satuan berbeda terbaca, masuk ke draf, dan diteruskan ke keranjang
dengan lancar. Tahap 20C Batch 1 selesai.

#### Batch 2 - Feedback produk masuk ke keranjang

- Ketukan produk dan hasil scan yang sukses menampilkan feedback singkat berisi
  nama, satuan, kuantitas terbaru, dan status `Masuk ke keranjang` tanpa dialog
  yang memperlambat operasional.
- Scan sukses dapat memakai bunyi/haptic singkat yang dapat dimatikan; kegagalan
  memakai pola berbeda dan alasan jelas. Notifikasi visual tetap tersedia untuk
  perangkat tanpa suara atau kebutuhan accessibility.
- Scan berulang harus memperbarui jumlah dan feedback secara deterministik tanpa
  menambah dua kali karena callback kamera/HID ganda.

Exit gate: tap, kamera, HID, stok habis, produk tidak dikenal, callback ganda,
accessibility, dan lifecycle background lulus tanpa mengubah perilaku checkout.

Status 13 September 2026: implementasi Batch 2 selesai. Ketukan kartu produk dan
scanner melalui kamera, input barcode, atau HID memakai satu feedback visual
ringkas yang menampilkan status **Masuk ke keranjang**, nama produk, jumlah
terbaru, dan satuan. Feedback lama langsung diganti agar scan cepat tidak
menumpuk antrean; scan berulang menampilkan jumlah terbaru secara deterministik.
Ikon membedakan ketukan dan scanner. Kegagalan stok/barcode memakai warna, ikon,
dan pesan berbeda. Feedback sukses maupun gagal merupakan live region dengan
label eksplisit untuk pembaca layar. Bunyi/getar tidak ditambahkan agar kasir
tidak bising dan tidak memerlukan pengaturan baru; notifikasi visual tetap selalu
tersedia.

Verifikasi: widget Kasir **27/27 lulus**, targeted Kasir/ViewModel **47/47
lulus**, seluruh test aplikasi **540/540 lulus**, analyzer penuh **No issues
found**, serta `git diff --check` bersih. Tidak ada dependency atau perubahan
schema. APK profile dipasang sebagai update dan dibuka pada V2322; SHA-256
lokal/perangkat identik:
`13781ffc88becfa1d5d666a8f15501655d0539219001c0c2f8231c22975bea23`.
Pemeriksaan visual notifikasi tap dan scanner kamera dinyatakan **lulus oleh
pengguna pada 13 September 2026**. Scanner fisik tetap dicakup otomatis sampai
alat/OTG tersedia. Tahap 20C Batch 2 selesai.

#### Batch 3 - Shift dan tutup kasir

- Manager mengatur daftar shift; satu perangkat hanya boleh memiliki satu shift
  aktif. Kasir harus membuka/memilih shift sebelum transaksi dan shift berikutnya
  tidak dapat dimulai sebelum shift lama ditutup.
- Pembukaan menyimpan operator dan uang awal. Penutupan meminta uang tunai fisik
  terakhir, menghitung kas seharusnya, selisih, serta alasan bila selisih melewati
  batas; mutasi kas masuk/keluar dicatat terpisah.
- `Tutup Kasir` menghasilkan ringkasan shift, mengunci transaksi baru secara
  atomik, menyimpan snapshot operator/waktu toko, dan mencatat audit. Crash,
  auto-lock, logout, serta restart tidak boleh membuat dua shift aktif.
- Riwayat, laporan, struk, backup/restore, dan Excel memakai identitas shift yang
  sama. Nama shift pada struk penjualan dapat diaktifkan/nonaktifkan Manager
  tanpa menghapus snapshot transaksi; ringkasan tutup kasir tetap wajib
  mengidentifikasi shift. Aturan siapa yang boleh menutup/membuka kembali
  dikunci sebelum coding.

Exit gate: state machine shift, hitungan kas, pergantian operator, crash recovery,
authorization, timezone, audit, migrasi, dan soak checkout lulus.

Status Batch 3A, 13 September 2026: fondasi data dan state machine selesai.
Schema naik kompatibel dari v12 ke v13 dengan tabel definisi shift, sesi shift,
dan pergerakan kas; transaksi lama tetap utuh dan memperoleh snapshot shift
nullable. Database menjamin hanya satu shift aktif per toko/perangkat. Manager
mengelola definisi shift; akun Manager atau Kasir aktif dapat membuka shift;
shift hanya dapat ditutup oleh pembukanya atau Manager. Logout, auto-lock, dan
restart tidak menyimpan state shift di memori sehingga shift aktif dipulihkan
dari database. Uang seharusnya dihitung dari uang awal + transaksi tunai selesai
+ kas masuk - kas keluar; transaksi batal dan non-tunai tidak dihitung. Selisih
berapa pun wajib memiliki catatan. Seluruh pembukaan, penutupan, definisi, dan
pergerakan kas masuk Audit Log.

Verifikasi Batch 3A: repository/state machine **6/6 lulus**, seluruh matriks dan
kasus data migrasi v1-v13 **81/81 lulus**, regresi health/recovery/backup
**15/15 lulus**, seluruh test aplikasi **559/559 lulus**, Flutter analyzer **No
issues found**, dan `git diff --check` bersih. Tidak ada dependency baru. UI
belum ditambahkan dan checkout belum diwajibkan memiliki shift pada 3A agar
aplikasi tidak terkunci sebelum layar buka/tutup shift tersedia. Sub-batch
berikutnya adalah UI pengaturan dan pembukaan shift, kemudian pengaitan checkout
serta penutupan kas.

Status Batch 3B, 13 September 2026: menu **Shift** tersedia untuk Manager dan
Kasir dengan sumber status langsung dari database. Manager dapat menambah,
mengubah, mengurutkan, mengaktifkan, dan menonaktifkan definisi shift. Manager
dan Kasir dapat memilih shift aktif serta memasukkan uang awal dengan pemisah
ribuan. Kartu status menampilkan shift aktif, pembuka, waktu buka, dan uang awal;
status tetap pulih ketika halaman/repository dibuat ulang. Kasir tidak memperoleh
kontrol pengaturan definisi. Halaman dan dialog dapat digulir serta telah diuji
pada landscape pendek 800x360.

Verifikasi Batch 3B: widget UI Shift **3/3 lulus**, integrasi repository, role,
login/shell, Kasir, scanner, dan responsive **42/42 lulus**, seluruh test
aplikasi **562/562 lulus**, Flutter analyzer **No issues found**, serta
`git diff --check` bersih. Tidak ada dependency atau perubahan schema baru;
schema tetap v13. Checkout belum dikunci dan uji perangkat ditunda sampai kontrol
kas masuk/keluar serta penutupan tersedia, agar pengujian tidak meninggalkan
shift aktif yang belum dapat ditutup melalui UI.

Status Batch 3C, 14 September 2026: pembuka shift atau Manager dapat mencatat
kas masuk/keluar dengan nominal dan catatan, melihat riwayat operator/waktu,
serta melihat kas seharusnya yang dihitung ulang dari database. Kasir lain hanya
mendapat tampilan baca dan tidak dapat mengubah ataupun menutup shift. Dialog
penutupan menampilkan uang fisik, kas seharusnya, dan selisih secara langsung;
selisih berapa pun wajib diberi alasan sebelum penutupan atomik diterima.

Verifikasi Batch 3C: targeted repository/UI **13/13 lulus**, regresi shell,
Kasir, scanner, dan responsive **46/46 lulus**, seluruh test aplikasi **566/566
lulus**, Flutter analyzer **No issues found**, dan `git diff --check` bersih.
APK profile berhasil dibangun serta dipasang sebagai update tanpa menghapus data
pada V2322; SHA-256
`2a8b8d33c6ee4f17c8fa60f312f5c5247fc05ee18dc55117ef7b31dc0deb848d`.
Gerbang manual uang awal Rp100.000, kas masuk Rp20.000, kas keluar Rp5.000,
kas seharusnya Rp115.000, validasi alasan selisih, penutupan, dan pembukaan
shift berikutnya dinyatakan lulus oleh pengguna.

Status Batch 3D, 14 September 2026: checkout hanya dapat diselesaikan ketika
terdapat shift aktif. Repository melakukan pemeriksaan ulang secara atomik agar
perubahan kondisi di antara layar pembayaran dan penyimpanan tidak menghasilkan
transaksi tanpa shift. Pembuka shift dan Manager dapat bertransaksi; Kasir lain
ditolak dengan pesan yang jelas. Penjualan menyimpan snapshot UID dan nama shift,
Audit Log menyimpan konteks shift, serta keranjang tetap utuh apabila checkout
ditolak. Pembayaran tunai transaksi selesai ikut menambah kas seharusnya,
sedangkan non-tunai dan transaksi batal tidak ikut kas fisik.

Verifikasi Batch 3D: targeted checkout/ViewModel **37/37 lulus**, regresi terkait
**42/42 lulus**, seluruh test aplikasi **571/571 lulus**, Flutter analyzer **No
issues found**, dan `git diff --check` bersih. APK profile awal Batch 3D memiliki
SHA-256
`2ad628044868bb2c4c5d8a390f0b95a5da5e0456546c69e2a5f94a826bad0d1a`.

Status Batch 3E, 14 September 2026: halaman Shift menampilkan hingga 20 shift
tertutup terbaru. Manager dapat melihat seluruh operator, sedangkan Kasir hanya
melihat shift yang dibukanya sendiri. Setiap detail memuat waktu, operator, uang
awal, penjualan tunai/non-tunai, kas masuk/keluar, kas seharusnya, uang fisik,
selisih, alasan penutupan, daftar transaksi, dan mutasi kas. Tampilan detail dapat
digulir dan tetap aman pada landscape pendek.

Verifikasi Batch 3E: targeted repository/UI **24/24 lulus**, seluruh test
aplikasi **573/573 lulus**, Flutter analyzer **No issues found**, dan
`git diff --check` bersih. APK profile gabungan Batch 3D-3E dipasang sebagai
update tanpa menghapus data pada V2322; SHA-256
`27a452cd2c66a7b1d84869f48b2c4996b8587cde951018a7d477a9fc5b27e3db`.
Gerbang manual checkout tunai/non-tunai, rekonsiliasi kas, penutupan, riwayat,
detail transaksi, akses role, dan landscape dinyatakan **lulus oleh pengguna**
pada 14 September 2026. Sub-batch 3F menyelaraskan identitas shift pada riwayat
transaksi dan struk, serta menyediakan bukti ringkasan tutup kasir.

Status implementasi Batch 3F, 14 September 2026: daftar dan detail riwayat
transaksi menampilkan snapshot nama shift. Transaksi lama yang memang belum
memiliki snapshot dijelaskan sebagai data lama, bukan ditebak ke shift baru.
Struk penjualan mencetak identitas shift apabila tersedia. Detail riwayat shift
memiliki tombol **Cetak Ringkasan Tutup Kasir** yang memakai printer dan ukuran
kertas toko saat ini. Ringkasan bergaya laporan penutupan singkat memuat waktu,
operator pembuka/penutup, jumlah transaksi selesai/batal, penjualan
tunai/non-tunai, uang awal, kas masuk/keluar, kas seharusnya, uang fisik,
selisih, dan catatan penutupan. Kegagalan cetak dicatat dan tidak mengubah data
shift maupun transaksi.

Verifikasi otomatis Batch 3F: targeted printer/riwayat/UI **27/27 lulus**,
seluruh test aplikasi **575/575 lulus**, Flutter analyzer **No issues found**,
dan `git diff --check` bersih. Tidak ada perubahan schema atau dependency. APK
profile berhasil dibangun, dipasang sebagai update tanpa menghapus data, dan
dibuka pada V2322. SHA-256 APK lokal/perangkat identik:
`eaf2f0f0091338e422bd152356531656eef1e54f290ff8d8ed8ad09cbfc3c0f2`.

Penyempurnaan Batch 3F, 14 September 2026: Manager memperoleh sakelar
**Tampilkan nama shift** pada Branding & Desain Struk. Nilai awal aktif menjaga
kompatibilitas tampilan lama. Jika dimatikan, hanya struk penjualan dan
pratinjaunya yang menyembunyikan nama shift; snapshot transaksi, riwayat,
rekonsiliasi, Audit Log, dan ringkasan tutup kasir tetap utuh. Pengaturan
disimpan per toko dan perubahannya diaudit melalui sistem desain struk yang
sudah ada.

Schema naik kompatibel dari v13 ke v14. Migrasi langsung seluruh snapshot
v1-v13, preservasi data pengaturan v13, database health/recovery, dan kontrak
backup/restore telah diuji. Targeted desain struk/printer/migrasi **103/103
lulus**, seluruh test aplikasi **578/578 lulus**, Flutter analyzer **No issues
found**, serta `git diff --check` bersih. Tidak ada dependency baru. APK profile
dipasang sebagai update dan dibuka pada V2322; SHA-256 lokal/perangkat identik:
`648905256b40144416b575a5858796ca00fa140b2af30aca932da43a3bc3ac9a`.
Gerbang visual sakelar dan pratinjau aktif/nonaktif dinyatakan lulus oleh
pengguna pada 19 September 2026. Gerbang printer fisik boleh ditunda sampai
printer tersedia. Batch 3 selesai.

#### Batch 4 - Mesin promo dan diskon

- Manager dapat membuat diskon seluruh transaksi, diskon per produk/varian, dan
  promo beli X dapat Y dengan periode aktif serta batas pemakaian yang jelas.
- Sebelum coding, sepakati diskon nominal/persentase, produk Y gratis atau
  potongan, kelipatan promo, prioritas, stacking, pembulatan, dan konflik antar
  promo. Hasil harus deterministik dan tidak boleh membuat nilai negatif.
- Kasir melihat promo yang diterapkan dan dapat membatalkannya hanya jika aturan
  Manager mengizinkan. Checkout menyimpan snapshot aturan, dasar hitung, dan
  nominal diskon agar transaksi lama tidak berubah.
- Terapkan pada keranjang, Pesanan Suara/manual, struk, riwayat, pembatalan,
  laporan, audit, backup/restore, dan export berikutnya.

Exit gate: matriks promo/stacking, periode timezone toko, harga varian, snapshot,
authorization, performa keranjang besar, migrasi, dan full regression lulus.

Status Batch 4A, 19 September 2026: kontrak mesin diskon deterministik selesai.
Diskon nominal produk dihitung per unit; diskon persentase memakai basis point
dan integer Rupiah dengan pembulatan turun. Satu diskon produk terbaik dipilih
per baris, lalu satu diskon transaksi terbaik dihitung dari subtotal bersih.
Konflik manfaat sama memakai prioritas lalu UID, target varian tidak bocor ke
satuan lain, dan seluruh diskon dibatasi agar total tidak negatif. Hasil mesin
menyediakan snapshot aturan, dasar hitung, nominal, serta target untuk integrasi
checkout berikutnya.

Matriks mesin **8/8 lulus**, seluruh test aplikasi **586/586 lulus**, Flutter
analyzer **No issues found**, dan `git diff --check` bersih. Tidak ada dependency,
schema, UI, atau perubahan checkout pada 4A sehingga build/uji perangkat belum
diperlukan. Batch 4B selanjutnya menambahkan kontrak promo beli X dapat Y dan
menetapkan interaksinya dengan diskon produk sebelum persistence/UI dibuat.

Status Batch 4B, 19 September 2026: mesin yang sama mendukung beli X dapat Y
untuk produk sama/berbeda serta satuan dasar/varian tertentu. Hadiah wajib sudah
ada di keranjang dan tidak ditambahkan diam-diam. Promo sama menghitung kelipatan
dari jumlah beli + hadiah; promo berbeda dibatasi jumlah kedua produk. Hadiah
dapat gratis, dipotong persentase, atau dipotong nominal per unit, selalu dibatasi
harga hadiah.

Konflik diproses berdasarkan prioritas lalu UID, dan unit yang telah dipakai satu
promo tidak dapat digunakan promo lain. Unit hadiah tidak memperoleh diskon
produk kedua; diskon produk hanya untuk unit berbayar dan diskon transaksi
dihitung terakhir. Snapshot hasil mencakup kelipatan, jumlah hadiah, aturan,
dasar hitung, nominal, dan baris hadiah.

Matriks gabungan **16/16 lulus**, seluruh test aplikasi **594/594 lulus**,
Flutter analyzer **No issues found**, serta `git diff --check` bersih. Tidak ada
dependency, schema, UI, atau perubahan checkout sehingga build/uji perangkat
belum diperlukan. Batch 4C berikutnya membangun persistence aturan, periode,
batas pemakaian, otorisasi Manager, dan Audit Log sebelum integrasi Kasir.

Status Batch 4C, 19 September 2026: persistence aturan promo selesai pada schema
database v15. Satu tabel aturan menyimpan diskon transaksi, diskon produk/varian,
dan beli X dapat Y, termasuk prioritas, periode aktif, batas dan penghitung
pemakaian, izin override Kasir, status aktif, serta referensi produk/satuan yang
tervalidasi per toko. Periode memakai awal inklusif dan akhir eksklusif; aturan
yang belum mulai, sudah berakhir, kehabisan batas, atau nonaktif tidak dianggap
eligible.

Create/update/aktivasi hanya dapat dilakukan Manager aktif yang identitas dan
tokonya diverifikasi kembali dari database. Nama unik tanpa beda kapital,
periode/nilai/jumlah wajib valid, target lintas toko atau varian yang tidak cocok
ditolak atomik, dan no-op tidak membuat audit palsu. Seluruh mutasi berhasil
dicatat pada Audit Log. Model tersimpan dapat dipetakan langsung ke mesin diskon
Batch 4A/4B tanpa membuat mesin kedua.

Test repository **5/5 lulus**, seluruh test aplikasi **599/599 lulus**, Flutter
analyzer **No issues found**, dan `git diff --check` bersih. Seluruh snapshot
database lama dapat dibuka langsung ke schema v15; database health dan
backup/restore sudah memakai versi baru dan backup schema v16 ditolak sebagai
versi masa depan. APK profile bersih berhasil dibangun, dipasang memakai update
yang mempertahankan data, dibuka pada V2322, dan hash lokal/perangkat identik:
`79e9a39c8abce0591076620d485a4c7a626fdf7240fbb2629a1070b89f6a1a49`.
Batch berikutnya mengintegrasikan aturan eligible ke keranjang/checkout beserta
snapshot transaksi dan konsumsi batas pemakaian secara atomik, lalu UI Manager
dan tampilan promo Kasir.

Status Batch 4D, 19 September 2026: checkout produksi sekarang mengambil hanya
aturan promo eligible dan menghitungnya dengan mesin deterministik Batch 4A/4B.
Diskon transaksi, diskon produk/satuan, serta beli X dapat Y memengaruhi total,
pembayaran, dan kembalian tanpa mengubah subtotal bruto maupun stok barang.

Schema database naik kompatibel dari v15 ke v16 dengan snapshot manfaat promo
per transaksi. Snapshot menyimpan UID/nama/jenis/nilai aturan, dasar hitung,
nominal diskon, kaitan item bila ada, serta rincian kelipatan dan jumlah hadiah,
sehingga perubahan aturan di masa depan tidak mengubah transaksi lama. Setiap
promo yang benar-benar terpakai menaikkan penghitung tepat sekali per checkout;
batas yang habis tidak diterapkan pada transaksi berikutnya.

Penyimpanan sale, item, stok, pembayaran, snapshot, penghitung promo, dan Audit
Log berada dalam transaksi database yang sama. Simulasi kegagalan pada langkah
akhir membuktikan seluruh mutasi di-rollback. Promo target varian juga diuji
tidak bocor ke satuan dasar.

Targeted promo/checkout/migrasi/backup **131/131 lulus**, seluruh test aplikasi
**603/603 lulus**, Flutter analyzer penuh **No issues found**, dan `git diff
--check` bersih. APK profile dibangun, dipasang sebagai update yang
mempertahankan data, serta dibuka tanpa exception pada V2322. Database dan aset
produk perangkat tetap ada; SHA-256 APK lokal/perangkat identik:
`74774a521dce44ac7f49b4a9ff5a7366d45efcb168fcd9ea56d90f8d26d8a473`.
Batch 4E berikutnya membuat UI pengelolaan promo Manager dan penjelasan promo
yang transparan bagi Kasir, termasuk aturan override.

Status Batch 4E, 20 September 2026: Manager kini memiliki menu khusus **Promo &
Diskon** untuk membuat, mengubah, mengaktifkan, dan menonaktifkan diskon
transaksi, diskon produk/satuan, serta beli X dapat Y. Form mendukung nominal
Rupiah/persentase, prioritas, jadwal, batas penggunaan, dan keputusan apakah
promo boleh dimatikan Kasir. Seluruh mutasi tetap melalui repository Manager dan
Audit Log yang sudah dibangun Batch 4C; tidak dibuat dependency atau mesin
diskon kedua.

Keranjang Kasir menampilkan nama, jenis, nominal manfaat, subtotal bruto,
diskon, dan total bersih dari preview mesin yang sama dengan checkout. Promo
wajib diberi tanda kunci. Promo opsional dapat dimatikan hanya ketika Manager
memberi izin; checkout memvalidasi ulang izin tersebut di repository sehingga
permintaan yang mencoba membatalkan promo wajib ditolak atomik. Preview
asinkron memakai generation guard dan lifecycle guard agar perubahan keranjang
cepat maupun penutupan halaman tidak menghasilkan hasil lama atau notifier
setelah dispose.

Tes repository baru membuktikan preview wajib/opsional, pengecualian promo
opsional tanpa snapshot/pemakaian, serta penolakan promo wajib tanpa sale/item
atau perubahan stok. Tes ViewModel membuktikan total mengikuti promo dan kembali
ke harga bruto setelah promo opsional dimatikan. Tes halaman membuktikan Manager
dapat membuat/menonaktifkan promo dan Kasir ditolak. Seluruh test aplikasi
**609/609 lulus** dan Flutter analyzer penuh **No issues found**. APK profile
berhasil dibangun dengan SHA-256
`3a88771b9037cbcf89dc7745a44b0d15022393b2511958d0bec0d65ab928fa75`;
APK kemudian dipasang sebagai update yang mempertahankan data, dibuka tanpa
exception pada V2322, dan hash APK lokal/perangkat terbukti identik. Uji alur
promo singkat oleh pengguna menjadi gerbang terakhir sebelum lanjut Batch 4F.

Koreksi operasional Buy X Get Y pada 20 September 2026 kini membuat Kasir cukup
memasukkan barang yang dibeli. Saat jumlah dan satuan tepat memenuhi syarat,
barang hadiah otomatis muncul di keranjang dengan jumlah dan label promo. Bonus
dihitung ulang dari jumlah pembelian asli sehingga tidak menggandakan diri,
hilang ketika syarat tidak lagi terpenuhi, serta mengikuti on/off promo opsional.
Checkout menghitung ulang bonus secara independen, memasukkannya ke item
transaksi, dan memvalidasi/mengurangi stok hadiah secara atomik. Form Manager
menampilkan satuan tepat serta otomatis memakai potongan 100% ketika tipe Buy X
Get Y dipilih (masih dapat diubah Manager).

Test meliputi hadiah produk berbeda, produk sama, batas penggunaan, perubahan
jumlah keranjang, serta menonaktifkan dan mengaktifkan kembali promo opsional.
Seluruh test aplikasi **614/614 lulus**, Flutter analyzer penuh **No issues
found**, dan `git diff --check` bersih. APK dipasang sebagai update tanpa
menghapus data dan dibuka tanpa crash pada V2322; SHA-256 lokal/perangkat
identik:
`c233336ecbf8d2a01773419202d2ccabba96f41eb93ede7d6f5ee4769e3d726f`.
Uji manual alur hadiah otomatis Buy X Get Y dinyatakan lulus oleh pengguna.
Penyempurnaan lanjutan menambahkan penghapusan promo oleh Manager dengan dialog
konfirmasi dan Audit Log `Promo dihapus`; snapshot promo pada transaksi lama
tetap tidak berubah. Baris hadiah di keranjang juga menjelaskan sumber bonus,
misalnya **Karena beli 2 Air Mineral Botol**, sehingga Kasir dapat mengetahui
aturan Buy X yang menghasilkan Get Y.

Test terarah repository, halaman promo, ViewModel Kasir, dan checkout **52/52
lulus**; seluruh test aplikasi **615/615 lulus** dan Flutter analyzer penuh **No
issues found**. APK profile dipasang sebagai update tanpa menghapus data, dibuka
tanpa exception pada V2322, dan SHA-256 APK lokal/perangkat identik:
`121cff733cd60932b3ce60030775c686b82c3a59775c39401cb316aacade8819`.
Uji manual tombol hapus promo dan keterangan sumber hadiah dinyatakan **lulus
oleh pengguna pada 20 September 2026**. Batch 4E selesai.

Status Batch 4F-A, 20 September 2026: detail riwayat dan struk sekarang membaca
snapshot promo milik transaksi, bukan aturan promo aktif. Rincian menampilkan
nama, jenis, dan nominal manfaat promo; beberapa snapshot dari aturan yang sama
digabung secara deterministik. Snapshot tetap tampil setelah promo diedit atau
dihapus, sedangkan transaksi lama tanpa snapshot tetap memakai total diskon lama
tanpa menebak nama promo.

Test repository riwayat dan printer **22/22 lulus**, seluruh test aplikasi
**617/617 lulus**, dan Flutter analyzer penuh **No issues found**. Tidak ada
perubahan schema atau dependency. APK profile dipasang sebagai update tanpa
menghapus data, dibuka tanpa exception pada V2322, dan SHA-256 APK
lokal/perangkat identik:
`cd8dc935c18dd7c77142eb4a4876b263abe9b1efec34157d60049c4a9293c7f6`.
Pemeriksaan visual rincian promo pada detail riwayat dan Preview Struk kemudian
dinyatakan **lulus oleh pengguna pada 20 September 2026**. Batch 4F-A selesai.

Status Batch 4F-B, 20 September 2026: pembatalan transaksi promo kini
mengembalikan `usageCount` setiap aturan yang terpakai tepat sekali, dalam
transaksi database yang sama dengan pengembalian stok, perubahan status, dan
Audit Log. Snapshot historis tidak dihapus. Audit pembatalan menyimpan nominal
diskon serta UID/nama promo tanpa data sensitif.

Laporan merinci diskon per snapshot promo untuk transaksi selesai dan tidak
menghitung transaksi batal. Query memakai indeks transaksi dan snapshot yang
sudah ada. Backup dan staging restore terbukti mempertahankan konfigurasi,
penghitung pemakaian, serta snapshot promo tanpa perubahan schema.

Test terarah pembatalan/laporan/backup **27/27 lulus**, test query laporan
**12/12 lulus**, seluruh test aplikasi **618/618 lulus**, Flutter analyzer penuh
**No issues found**, dan `git diff --check` bersih. APK profile dipasang sebagai
update tanpa menghapus data, dibuka tanpa exception pada V2322, dan SHA-256 APK
lokal/perangkat identik:
`271ab7779383aec9fe1327191662c2fcc9a43bb4dac9962671b1f87d7be2d52b`.
Uji manual satu transaksi promo sampai pembatalan dinyatakan lulus oleh
pengguna pada 20 September 2026. Batch 4 resmi selesai; laporan dan
backup/restore juga telah dicakup otomatis.

Urutan lanjutan Batch 4 dikunci agar perubahan tetap kecil dan teruji:

- Batch 4D (**selesai 19 September 2026**): integrasi aturan eligible ke
  keranjang/checkout, snapshot transaksi, serta konsumsi batas pemakaian dalam
  transaksi database yang sama;
- Batch 4E (**selesai dan lulus manual 20 September 2026**): UI pengelolaan promo
  Manager dan penjelasan promo yang transparan bagi Kasir, termasuk izin
  override;
- Batch 4F-A (**selesai dan lulus manual 20 September 2026**): rincian snapshot
  promo pada struk dan riwayat;
- Batch 4F-B (**selesai dan lulus manual 20 September 2026**): pembatalan,
  laporan, audit, backup/restore, performa query, dan regresi penuh. Batch 4
  selesai.

#### Batch 5 - Nomor meja opsional

- Tambahkan pengaturan on/off nomor meja oleh Manager.
- Saat aktif, Kasir/Manager dapat mengisi nomor atau nama meja sebelum checkout;
  aturan wajib atau boleh kosong dikunci melalui keputusan bisnis pada awal
  batch.
- Simpan snapshot meja pada transaksi serta tampilkan di keranjang, struk,
  riwayat, pencarian, backup/restore, dan export berikutnya.
- Transaksi retail tetap berjalan seperti sekarang ketika fitur dimatikan.

Keputusan bisnis dikunci: saat fitur aktif, nomor/nama meja tetap **boleh
kosong**. Batas dan sanitasi isi akan dikunci pada Batch 5B sebelum checkout
diubah.

Urutan implementasi:

- Batch 5A (**selesai dan lulus manual 20 September 2026**): schema v17,
  default mati, pengaturan on/off khusus Manager, audit atomik, role guard,
  kolom snapshot transaksi nullable, migrasi lama, backup, dan UI pengaturan;
- Batch 5B (**implementasi, test, build, dan pemasangan selesai 20 September
  2026; menunggu gerbang manual**): input meja pada keranjang/checkout,
  validasi, snapshot transaksi, riwayat, pencarian, struk, backup/restore, serta
  regresi alur retail saat fitur mati. Perbaikan responsif 21 September menjaga
  input meja tetap terlihat di atas keyboard pada portrait maupun landscape;
  **630/630 test**, analyzer, build, pemasangan, dan smoke test lulus, sedangkan
  pemeriksaan visual perangkat masih menunggu. Koreksi lifecycle berikutnya
  memastikan total dan tombol pembayaran kembali setelah keyboard ditutup;
  regresi buka/tutup keyboard, seluruh **630/630 test**, analyzer, build,
  pemasangan, dan smoke test kembali lulus pada 21 September 2026. Penyelarasan
  UI Keranjang HP Batch 1 kemudian merangkum promo dan menyejajarkan aksi pada
  portrait agar daftar produk lebih luas tanpa mengecilkan target sentuh;
  **631/631 test**, analyzer, build, pemasangan, dan smoke test lulus, menunggu
  penilaian visual perangkat sebelum pemadatan baris produk Batch 2. Batch 2
  kemudian membuat kontrol item adaptif: portrait menjaga lebar teks, landscape
  menyatukan aksi jumlah/hapus sehingga baris lebih pendek, dan divider keduanya
  dipadatkan. Regresi UI/keyboard **3/3**, seluruh **631/631 test**, analyzer,
  build, pemasangan, hash, serta smoke test lulus. Penilaian visual portrait dan
  landscape dinyatakan nyaman dan **lulus manual pada 21 September 2026**.

Exit gate: migrasi lama, on/off, validasi input, snapshot historis, struk,
backup/restore, dan role test lulus.

#### Batch 6 - Pajak, biaya layanan, dan pembulatan total opsional

- Manager dapat mengaktifkan pajak, biaya layanan, dan pembulatan secara
  independen, menentukan nama/rate biaya, serta memilih aturan pembulatan yang
  telah disepakati.
- Urutan awal untuk dikunci lewat tabel contoh: subtotal dikurangi promo/member,
  kemudian biaya layanan dan pajak, lalu satu penyesuaian pembulatan pada total
  akhir. Seluruh perhitungan memakai integer Rupiah, bukan floating-point.
- Permintaan `kurang dari Rp490` belum dianggap rumus final: sebelum coding harus
  dipastikan apakah pecahan `0-490` dibulatkan turun dan `491-999` naik ke ribuan,
  bagaimana tepat Rp490/Rp500 diperlakukan, serta perilaku total negatif/refund.
- Tampilkan nilai sebelum pembulatan dan penyesuaiannya; simpan nama, rate, dasar
  hitung, nominal biaya, aturan, dan nominal rounding sebagai snapshot transaksi.
- Saat semua opsi mati, total harus identik dengan perilaku sekarang. Sertakan
  struk, riwayat, laporan, pembatalan, audit, backup/restore, dan Excel.

Exit gate: tabel nominal/batas pembulatan, urutan formula, checkout atomik,
snapshot, role, migrasi, backup/restore, analyzer, dan full suite lulus.

Status Batch 6A, 21 September 2026: keputusan bisnis saat itu telah disetujui dan mesin
hitung murni selesai. Urutannya adalah subtotal dikurangi promo/diskon, biaya
layanan dihitung dari nilai bersih tersebut, pajak dihitung dari nilai bersih
ditambah biaya layanan, lalu total akhir dibulatkan ke Rp1.000 terdekat. Sisa
Rp0-Rp499 turun dan Rp500-Rp999 naik; Rp490 turun sedangkan Rp500 naik.
Persentase disimpan sebagai basis point dan setiap nominal antara memakai
integer Rupiah dengan pembulatan turun, bukan floating-point. Semua opsi mati
mempertahankan total lama secara identik; input negatif, diskon melebihi
subtotal, rate di luar 0-100%, dan nama biaya aktif yang kosong ditolak. Uji
khusus **10/10**, seluruh test aplikasi **641/641**, dan analyzer lulus. Mesin
belum dihubungkan ke database, UI, atau checkout pada sub-batch ini sehingga
schema tetap versi 17 dan tidak memerlukan uji perangkat.

Catatan kebijakan: pembulatan Rp1.000 di atas kemudian diganti oleh Batch 7D
menjadi Rp100 terdekat dan hanya berlaku untuk pembayaran tunai. Snapshot
transaksi lama tetap dipertahankan agar riwayat historis tidak berubah.

Status Batch 6B, 21 September 2026: schema naik kompatibel dari v17 ke v18
dengan pengaturan nama/rate biaya layanan, nama/rate pajak, dan mode pembulatan.
Semua opsi default mati; migrasi tidak mengubah transaksi atau total lama.
Manager dapat mengatur ketiganya dari Pengaturan Transaksi dan penyimpanan
ditulis atomik bersama audit `transaction_charges_setting_updated`. Kasir,
rate/nama tidak valid, dan kegagalan audit tidak dapat meninggalkan perubahan
sebagian. Uji kalkulator/repository/widget/migrasi/backup dan seluruh **646/646
test** lulus, analyzer bersih, build profile serta pemasangan update pada V2322
berhasil, startup bebas crash, dan hash APK lokal/perangkat identik:
`8134d75df964cd15ffee1267adf35760c0f9672fc20f393fc8969102f430655e`.
Pengaturan belum memengaruhi checkout sampai snapshot transaksi Batch 6C siap.
Gerbang manual tampilan dan persistensi dinyatakan **lulus oleh pengguna pada
21 September 2026**.

Status Batch 6C, 21 September 2026: preview keranjang dan checkout kini memakai
mesin hitung Batch 6A yang sama. Total pembayaran dihitung dari subtotal setelah
promo, biaya layanan, pajak, lalu pembulatan; validasi uang tunai, pembayaran,
Audit Log, dan nilai transaksi memakai total akhir yang sama dalam satu transaksi
database. Schema naik kompatibel dari v18 ke v19 dan menyimpan snapshot nama,
rate, dasar hitung, nominal biaya/pajak, total sebelum pembulatan, mode, serta
penyesuaian pembulatan. Transaksi lama tetap terbaca dengan snapshot nullable,
sedangkan semua opsi mati menghasilkan total lama secara identik. Ringkasan
keranjang dan dialog transaksi berhasil juga menampilkan komponen yang aktif.
Uji checkout/ViewModel/migrasi/backup/database terarah lulus, seluruh **649/649
test** lulus, dan analyzer bersih. APK profile berhasil dipasang sebagai update
pada V2322 tanggal 23 September 2026 tanpa menghapus data; proses hidup, startup
bebas error AndroidRuntime/Flutter, dan hash lokal/perangkat identik:
`fae99c3091c55c8f763a02d123119812cacf46286042e750154af5bb35f8f343`.
Gerbang transaksi manual dinyatakan **lulus oleh pengguna pada 24 September
2026**. Rincian snapshot pada riwayat, struk, dan laporan dilanjutkan pada Batch
6D.

Status Batch 6D, 24 September 2026: detail riwayat dan struk kini menampilkan
nama serta persentase biaya layanan/pajak, nominal setiap komponen, total sebelum
pembulatan, dan penyesuaian pembulatan bertanda. Semua nilai dibaca dari snapshot
transaksi sehingga perubahan pengaturan toko tidak mengubah transaksi lama.
Preview struk dan byte ESC/POS memakai sumber yang sama; label panjang tetap
dipenggal aman mengikuti lebar kertas 58/80 mm. Repository laporan menambahkan
agregat biaya layanan, pajak, dan pembulatan agar omzet dapat direkonsiliasi:
subtotal kotor dikurangi diskon, ditambah biaya dan pajak, lalu ditambah
penyesuaian pembulatan. Transaksi lama tetap bernilai nol untuk komponen yang
belum memiliki snapshot dan transaksi batal tidak ikut agregat.

Test terarah riwayat/struk/laporan **38/38**, seluruh test aplikasi **652/652**,
Flutter analyzer **No issues found**, dan `git diff --check` lulus. Tidak ada
dependency atau perubahan schema baru. APK profile berhasil dibangun dan
dipasang sebagai update pada V2322 tanpa menghapus data; proses hidup, startup
bebas error AndroidRuntime/Flutter, dan hash APK lokal/perangkat identik:
`c6999bf5c5649039e132a0df8de90d5cb9dc63150ebe3a39b4d25f403c26e28e`.
Gerbang visual riwayat dan preview struk dinyatakan **lulus oleh pengguna pada
24 September 2026**.

Koreksi UI setelah Batch 6D, 24 September 2026: tombol jumlah `+/-` sebelumnya
mengganti ringkasan Keranjang dengan progress bar dan menonaktifkan tombol Bayar
setiap kali promo/biaya dihitung ulang, sehingga layout terlihat berkedip.
Ringkasan stabil kini dipertahankan sampai hasil baru siap dan nilainya berubah
satu kali; pembayaran tetap menghitung ulang serta memvalidasi hasil terbaru
sebelum dialog dibuka. Test regresi khusus **1/1**, seluruh test aplikasi
**653/653**, analyzer, `git diff --check`, build, pemasangan update, hash, dan
smoke test lulus. SHA-256 lokal/perangkat:
`b87ba7f15b85f59336c338768623e7689734a4090b75d032ab3febaabb6f4822`.
Gerbang visual tombol `+/-` pada perangkat masih menunggu pemeriksaan pengguna.

#### Batch 6E - Rekonsiliasi suara multi-satuan dan hadiah promo

Status: **implementasi dan verifikasi otomatis selesai; gerbang perangkat
menunggu pemeriksaan pengguna sebelum Batch 7**.

1. Parser Pesanan Suara harus menerima kedua susunan ucapan yang lazim tanpa
   mengubah arti: `Aqua 1 botol` dan `Aqua botol 1`. Aturan yang sama wajib
   berlaku untuk satuan dasar, satuan turunan, serta satuan kustom toko—bukan
   hanya kata `botol`. Beberapa produk dalam satu ucapan, nama produk yang juga
   mengandung angka, dan kandidat ambigu tetap harus aman serta tidak boleh
   dipilih diam-diam.
2. Ringkasan suara Keranjang harus membacakan jumlah barang yang benar-benar ada
   pada baris, termasuk hadiah otomatis Buy X Get Y. Baris hadiah murni dengan
   `quantity = 0` dan `promotionQuantity = 1` harus disebut sebagai `1 Pcs`,
   bukan `0 Pcs`; harga/subtotal hadiah tetap nol. Baris yang berisi pembelian
   sekaligus hadiah harus memakai jumlah tampil yang sama dengan Keranjang tanpa
   menggandakan hadiah.

Rencana verifikasi:

- matriks parser untuk `nama + jumlah + satuan` dan `nama + satuan + jumlah`
  pada Botol, Dus, Karung, Pcs, serta satuan kustom;
- ucapan beberapa produk dan produk bernama/berukuran angka tidak mengalami
  regresi;
- unit test pembentuk ringkasan untuk hadiah murni, hadiah produk sama, hadiah
  produk berbeda, kelipatan promo, promo dinonaktifkan, serta baris normal;
- widget/regresi Kasir memastikan teks Keranjang dan kalimat TTS memakai jumlah
  yang sama, harga hadiah tetap nol, dan checkout tidak berubah;
- analyzer, seluruh test, build profile, lalu uji dengar nyata pada perangkat.

Exit gate: kedua urutan ucapan menghasilkan produk/satuan/jumlah identik; hadiah
Buy X Get Y dibacakan dengan jumlah tampil yang benar dan harga nol; tidak ada
regresi lifecycle rekaman, parser katalog, multi-satuan, promo, atau checkout.
Batch ini tidak direncanakan menambah schema maupun dependency.

Hasil implementasi 24 September 2026:

- parser menerima satuan katalog di depan maupun di belakang jumlah, termasuk
  satuan kustom yang terdiri dari beberapa kata;
- deteksi satuan di depan jumlah dibatasi pada satuan yang benar-benar ada di
  katalog aktif. Karena itu kata seperti `cup` pada nama `Mie Cup` tidak
  dipotong atau dianggap sebagai satuan secara keliru;
- ringkasan Keranjang memisahkan jumlah yang dibacakan dari harga baris. Jumlah
  memakai `quantity + promotionQuantity`, sedangkan harga tetap memakai bagian
  yang dibayar. Hadiah murni satu Pcs kini dibaca `satu pcs, harga nol rupiah`;
- tidak ada perubahan schema, dependency, kalkulasi promo, maupun checkout.

Verifikasi: test parser/ringkasan terarah **46/46 lulus**, widget integrasi TTS
promo lulus, seluruh test aplikasi **656/656 lulus**, Flutter analyzer **No
issues found**, dan `git diff --check` bersih. APK profile terpasang sebagai
update pada V2322 tanpa menghapus data; proses aplikasi hidup dan startup bebas
error AndroidRuntime/Flutter. SHA-256 APK lokal/perangkat identik:
`7fc51d9d777a35384d1a85775619d7682e9912d8bcbbdb61efe6e74b3cc32e81`.

Gerbang manual: ucapkan satu produk dalam dua urutan (`Aqua 1 botol` dan
`Aqua botol 1`), satu contoh satuan kustom, serta beberapa produk sekaligus.
Kemudian aktifkan Buy X Get Y, buka pembacaan Keranjang, dan pastikan hadiah
disebut sesuai jumlah tampil dengan harga nol.

Hotfix UI Keranjang landscape, 24 September 2026: panel produk di kiri sejak
awal dapat digulir, tetapi panel ringkasan di kanan masih berupa kolom tetap.
Ketika promo, biaya layanan, pajak, atau pembulatan menambah tinggi ringkasan,
tombol Bayar dan Kosongkan dapat terpotong di bawah layar. Panel kanan kini
memiliki scroll vertikal mandiri sehingga seluruh rincian dan kedua tombol
tetap dapat dijangkau tanpa mengubah layout portrait atau rumus transaksi.

Verifikasi: test widget khusus landscape **1/1 lulus**, seluruh test aplikasi
**656/656 lulus**, Flutter analyzer **No issues found**, dan `git diff --check`
bersih. APK profile terpasang sebagai update pada V2322 tanpa menghapus data;
proses hidup dan startup bebas error AndroidRuntime/Flutter. SHA-256 lokal dan
perangkat identik:
`0176f46db7c5f4a4486e5b5ff359f3a28315ddc495157658c157df189e304694`.
Gerbang visual scroll panel kanan masih menunggu pemeriksaan pengguna.

#### Batch 6F - Rekonsiliasi presentasi promo Keranjang dan struk

Status: **Batch 6F-A lulus gerbang visual pengguna dan audit kestabilan refresh
UI telah terpasang. Batch 6F-B selesai implementasi, verifikasi otomatis,
build, serta smoke test perangkat; gerbang visual struk menunggu pengguna**.

Batch 6F-A, rincian promo Keranjang:

- label teknis seperti `Wajib` diganti menjadi bahasa operasional `Otomatis`,
  `Opsional aktif`, atau `Opsional nonaktif`;
- diskon produk menyebut target aktual, misalnya `Untuk pembelian Air Mineral
  semua satuan`, bukan hanya jenis aturan generik;
- Buy X Get Y menyebut jumlah, produk, dan satuan aktual dari hasil kalkulasi,
  misalnya `Beli 1 Air Mineral Botol, bonus 1 Pop Mie Promo Pcs`;
- promo gratis dibedakan dari potongan sebagian: kata `bonus` hanya dipakai
  ketika nilai hadiah dipotong penuh, sedangkan potongan sebagian memakai
  `dapat`;
- promo opsional yang dimatikan tetap mempertahankan rincian produk dan jumlah;
  nilai potensi hanya ditampilkan bila dapat dihitung dari keranjang aktif,
  tanpa memalsukan Rp0. Rumus, checkout, schema, dan dependency tidak berubah.

Verifikasi Batch 6F-A: test model Buy X Get Y dan widget diskon produk/Buy X
Get Y lulus, seluruh test aplikasi **656/656 lulus**, Flutter analyzer **No
issues found**, build APK profile berhasil, pemasangan update ke V2322 tidak
menghapus data, proses aplikasi hidup, dan log startup bebas error
AndroidRuntime/Flutter. SHA-256 APK lokal/perangkat identik:
`c9fc5cc6e3c1536a2e78c26d65d2d42661df360919a6fe7eff8996503870be3a`.

Gerbang visual utama Batch 6F-A dinyatakan **lulus oleh pengguna pada 24
September 2026**. Pemeriksaan lanjutan menemukan sakelar promo opsional sempat
membaca hasil kalkulasi lama ketika ditekan, sehingga tampil memantul atau
terlambat sebelum menetap. Hotfix membuat posisi dan label sakelar langsung
mengikuti pilihan Kasir pada frame pertama; perhitungan promo tetap berjalan
asinkron dan menjadi sumber nilai akhir.

Verifikasi hotfix: test state dan widget frame pertama **2/2 lulus**, seluruh
test aplikasi **656/656 lulus**, Flutter analyzer **No issues found**, build
APK profile serta pemasangan update ke V2322 berhasil, proses aplikasi hidup,
dan log startup bebas error AndroidRuntime/Flutter. SHA-256 APK lokal/perangkat
identik:
`9e762c759dd630e8eea900474c9e0219f7c6e48ac00b988da488ac16070f7178`.
Gerbang visual hotfix on/off promo menunggu konfirmasi pengguna.

Status Batch 6F-B, 24 September 2026: kejelasan struk selesai
diimplementasikan.

- Jumlah yang dibayar dipisahkan dari hadiah gratis. Contoh satu Pop Mie
  berbayar ditambah dua hadiah kini menampilkan satu Pop Mie pada Detail
  Belanja dan `Bonus 2 Pop Mie Pcs` bernilai Rp0 pada Promo & Diskon.
- Rincian bonus menyebut hubungan penyebab dari snapshot checkout, misalnya
  `Karena beli 1 Air Mineral Botol`. Transaksi lama yang belum memiliki konteks
  tersebut memakai keterangan aman `Hadiah dari promo ini`, bukan menebak data.
- Hanya Buy X Get Y dengan potongan hadiah penuh yang dipisahkan sebagai bonus
  Rp0. Potongan hadiah sebagian tetap menjadi barang berharga normal dan
  ditampilkan sebagai `Beli X dapat Y`, sehingga tidak disebut gratis.
- Preview Struk dan byte ESC/POS memakai sumber item berbayar/rincian bonus yang
  sama. Subtotal diberi label `Subtotal sebelum promo` ketika ada hadiah gratis;
  nilai subtotal, diskon, total, item fisik, stok, dan snapshot transaksi tidak
  berubah. Tidak ada perubahan schema atau dependency.

Verifikasi Batch 6F-B: test struk **19/19 lulus**, test terarah gabungan
printer/riwayat/mesin promo **42/42 lulus**, seluruh test aplikasi **658/658
lulus**, Flutter analyzer **No issues found**, dan `git diff --check` bersih.
APK profile dipasang sebagai update pada V2322 tanpa menghapus data; proses
aplikasi hidup dan log startup bebas error AndroidRuntime/Flutter. SHA-256 APK
lokal/perangkat identik:
`875703744d2396ef5277ad721b48fbfbd26652b50b6f12caa3af6ec3b632776b`.
Gerbang visual Preview Struk transaksi baru dan cetak printer fisik bila alat
tersedia masih menunggu pengguna.

Exit gate Batch 6F: rincian Keranjang dinyatakan mudah dipahami pada perangkat;
struk tidak membuat barang gratis terlihat ditagihkan; nilai finansial preview,
hasil cetak, riwayat, dan checkout tetap identik.

#### Batch 7 - Customer, member, dan diskon pelanggan

- **Batch 7A - fondasi member dan aturan bisnis (selesai):** data minimum hanya
  kode unik, nama, status aktif, jenis diskon, dan nilai diskon; alamat, tanggal
  lahir, nomor identitas, dan data pribadi lain tidak dikumpulkan. CRUD/status
  hanya untuk Manager aktif, sedangkan Kasir hanya dapat mencari member aktif.
  Seluruh mutasi bersifat atomik dan diaudit. Schema v20 serta migrasi seluruh
  snapshot lama sudah diverifikasi tanpa kehilangan data.
- Aturan formula dikunci: promo produk dan Buy X Get Y dihitung lebih dulu,
  kemudian diskon transaksi. Jika promo transaksi dan diskon member sama-sama
  tersedia, hanya nominal transaksi yang lebih besar dipakai; keduanya tidak
  ditumpuk. Biaya layanan, pajak, dan pembulatan tetap dihitung sesudah diskon.
- **Batch 7B - UI dan pemilihan pelanggan (selesai otomatis; gerbang visual
  perangkat menunggu pengguna):** halaman pengelolaan member hanya tersedia
  untuk Manager aktif dan mendukung tambah, cari, ubah, aktif/nonaktif, serta
  hapus. Keranjang menyediakan pencarian/pemilihan member aktif dan nama
  nonmember manual yang tidak membuat profil baru. Tata letak sudah diuji pada
  HP portrait/landscape, keyboard terbuka, landscape pendek, dan tablet tanpa
  mengembalikan overflow lama. Pilihan pelanggan masih berupa draf UI dan belum
  memengaruhi total maupun tersimpan ke transaksi; integrasinya sengaja
  diselesaikan atomik pada Batch 7C.
- Program member bersifat opsional per toko dan default aktif agar perilaku toko
  lama tidak berubah. Manager dapat menonaktifkannya dari Pengaturan Transaksi;
  Kasir tidak lagi melihat pemilih member, tetapi input nama nonmember tetap
  tersedia. Seluruh data member dipertahankan untuk aktivasi ulang dan perubahan
  pengaturan dicatat di Audit Log. Schema v21 menambahkan pengaturan ini dengan
  migrasi aman dari v20.
- **Batch 7C - checkout dan snapshot lintas fitur (selesai otomatis; gerbang
  transaksi perangkat menunggu pengguna):** pilihan member kini memengaruhi
  preview dan checkout. Promo produk/Buy X Get Y dihitung lebih dahulu; antara
  promo transaksi dan diskon member hanya nominal terbesar yang dipakai tanpa
  stacking. Nama nonmember manual hanya disimpan pada transaksi dan tidak
  membuat profil baru.
- Schema v22 menyimpan snapshot nama customer, UID/kode member, jenis/nilai
  aturan, dasar hitung, dan nominal diskon member. Perubahan atau penghapusan
  profil berikutnya tidak mengubah transaksi lama; transaksi lama tetap valid
  dengan snapshot nullable.
- Preview Keranjang menjelaskan diskon member yang diterapkan atau kalah oleh
  promo transaksi yang lebih besar. Struk dan Riwayat menampilkan customer,
  kode member, serta potongan member; agregat diskon/total Laporan otomatis
  memakai nilai transaksi final yang sama. Database dan snapshot tersebut ikut
  backup/restore, serta menjadi kontrak data member/customer untuk Excel Batch
  9 tanpa membuat exporter lebih awal.
- Test repository checkout **27/27** dan seluruh test aplikasi **676/676**
  lulus; analyzer serta `git diff --check` bersih. APK profile berhasil dipasang
  sebagai update pada V2322, migrasi v21 ke v22 membuka aplikasi tanpa error,
  dan SHA-256 APK lokal/perangkat identik:
  `3344c55fc330f1868a6105f5c2ca5505939be81d4076605cef58dc018cba815a`.

- **Batch 7D - pembulatan tunai Rp100 dan donasi kembalian (selesai otomatis;
  gerbang transaksi perangkat menunggu pengguna):** total pada Keranjang tetap
  presisi. Pembulatan ke Rp100 terdekat baru diterapkan setelah Kasir memilih
  metode tunai; selisih maksimum Rp50. QRIS dan metode nontunai lain tetap
  memakai nominal persis tanpa pembulatan.
- Donasi kembalian hanya tersedia untuk tunai, tidak boleh negatif atau melebihi
  kembalian tersedia, dan nilai positif wajib disertai persetujuan pembeli yang
  dicentang Kasir. Donasi mengurangi kembalian, tetapi tidak menambah omzet atau
  nilai penjualan/pembayaran.
- Schema v23 menyimpan donasi sebagai nilai transaksi terpisah. Migrasi mengubah
  pengaturan aktif lama `nearestThousand` menjadi `nearestHundred`, sementara
  snapshot pembulatan transaksi lama tetap dibaca apa adanya. Kolom nullable
  menjaga seluruh snapshot database lama tetap dapat dibuka langsung.
- Struk dan Riwayat menampilkan donasi di antara uang diterima dan kembalian.
  Laporan mengagregasikan donasi terpisah dari omzet. Expected cash dan
  rekonsiliasi Shift menambahkan donasi ke kas fisik, tetapi `cashSalesTotal`
  tetap hanya berisi pendapatan penjualan.
- Validasi repository menolak donasi tanpa persetujuan, melebihi kembalian, atau
  pada metode nontunai. Backup/restore dan health check sudah diselaraskan dengan
  schema v23.
- Seluruh test aplikasi **677/677** lulus; Flutter analyzer **No issues found**
  dan `git diff --check` bersih. APK profile berhasil dibangun dan dipasang
  sebagai update pada V2322 tanpa menghapus data; proses aplikasi hidup dan
  `MainActivity` aktif. SHA-256 APK lokal/perangkat identik:
  `fcb46e0ccfb1dea3706c3b2ac009d7274120de1daa0c2ca528f93c50d5f31f55`.

Exit gate otomatis Batch 7C-7D untuk authorization, uniqueness, pencarian,
customer manual, formula diskon, pembulatan tunai, donasi, privacy, snapshot,
migrasi, backup/restore, dan full regression lulus. Gerbang terakhir adalah
transaksi nyata member/nonmember serta pembulatan/donasi tunai beserta preview
struk dan Riwayat pada perangkat.

#### Batch 8 - Tampilan QRIS pembayaran

- Manager dapat memasang, mengganti, menghapus, dan melihat pratinjau gambar QRIS
  toko dari pengaturan pembayaran; gambar disalin ke storage aplikasi dan ikut
  backup/restore serta pembersihan asset yatim.
- Ketika metode QRIS dipilih, tampilkan QR dengan ukuran/kontras yang mudah
  dipindai dan aksi perbesar. Kasir tetap wajib mengonfirmasi pembayaran karena
  QRIS statis/offline tidak dapat membuktikan uang sudah diterima.
- Kegagalan file, gambar rusak, atau QR belum dikonfigurasi tidak boleh membuat
  transaksi dianggap lunas. Penggantian gambar dicatat audit tanpa menyimpan
  isi file atau data sensitif dalam log.

Checkpoint Batch 8A lulus otomatis pada 29 September 2026:

- Manager dapat memilih, mengganti, menghapus, dan memperbesar gambar QRIS dari
  Pengaturan Transaksi. File dinormalisasi ke PNG dan disimpan di
  `payment_assets`; path perangkat lama dipetakan ke aset hasil restore pada
  perangkat aktif.
- Schema naik ke v24. Perubahan gambar dan audit
  `qris_settings_updated` berjalan atomik; audit hanya mencatat status ada/tidak
  ada gambar tanpa path atau isi file. Kasir ditolak tanpa mutasi.
- `payment_assets` sudah masuk manifest, pembuatan backup, restore commit,
  rollback, penghapusan aset aktif lama, dan startup orphan cleanup.
- Test terarah 132/132 dan seluruh test 685/685 lulus; analyzer serta
  `git diff --check` bersih. APK profile terpasang pada V2322, checksum lokal dan
  perangkat identik, proses hidup, dan log startup bersih. Gerbang visual
  pilih/ganti/perbesar/hapus pada portrait-landscape masih menunggu pengguna.
- Gerbang visual pilih/ganti/perbesar/hapus pada portrait-landscape dinyatakan
  lulus oleh pengguna. Tampilan QRIS pada dialog pembayaran, konfirmasi manual
  Kasir, dan perlindungan file hilang/rusak dilanjutkan pada Batch 8B.

Checkpoint Batch 8B lulus otomatis pada 30 September 2026:

- Saat metode QRIS dipilih, dialog menampilkan gambar dalam bidang putih dengan
  ukuran terbaca, aksi perbesar/zoom, penjelasan bahwa QRIS statis tidak dapat
  diverifikasi otomatis, serta kolom referensi opsional.
- Gambar di-resolve ulang dari pengaturan terbaru setiap dialog pembayaran akan
  dibuka, lalu isi file divalidasi sebelum dirender. Perubahan QRIS Manager tetap
  terbaca walaupun halaman Kasir sebelumnya sudah aktif.
- Kasir wajib mencentang bahwa dana sudah diterima setelah memeriksa notifikasi
  atau mutasi. Dialog dan repository sama-sama menolak QRIS tanpa konfigurasi
  atau tanpa konfirmasi; batal/gagal tidak membuat sale, payment, atau movement.
- UI portrait dan landscape pendek dicakup widget test. Test terarah final
  **67/67**, regresi checkout/struk/riwayat **83/83**, seluruh test aplikasi
  **692/692**, analyzer **No issues found**, dan `git diff --check` bersih.
- APK profile terpasang sebagai update pada V2322 tanpa menghapus data. SHA-256
  lokal/perangkat identik:
  `90c2c40daa95c20384b5c160e0242ce9f9389506d3f2ffb182f569f20062cd88`.
  Proses aplikasi hidup dan log startup AndroidRuntime/Flutter bersih.
- Hotfix landscape HP membuat header/total lebih ringkas, menempatkan total dan
  metode pembayaran berdampingan, menjaga isi panjang dapat digulir, serta
  memastikan tombol dialog tetap terpisah dari isi. Dialog pilihan satuan juga
  memakai lebar landscape dan daftar satuan yang dapat digulir; dialog transaksi
  berhasil memakai ringkasan ringkas yang dapat digulir. Test pembayaran 7/7,
  widget Kasir 34/34, seluruh test **693/693**, analyzer bersih, dan APK terbaru
  terpasang dengan SHA-256 lokal/perangkat identik:
  `ba86e13106b6f44ecb44e7d73f06908658c33a0324600c1a2b32065a4f3a347d`.

Gerbang manual Batch 8B dinyatakan **lulus oleh pengguna pada 30 September
2026**: fitur QRIS dan perubahan UI dialog landscape berjalan baik pada
perangkat. QRIS statis tetap tidak diklaim memverifikasi pembayaran secara
otomatis; konfirmasi Kasir tetap menjadi pengaman operasional.

Exit gate: authorization, lifecycle asset, backup/restore, tampilan berbagai
orientasi, scan QR nyata, pembayaran gagal/batal, audit, dan full suite lulus.

#### Batch 9 - Export laporan Excel utama

- Export `.xlsx` offline berdasarkan rentang tanggal dengan dialog lokasi file
  sistem agar hasil mudah ditemukan; tidak memerlukan akun atau internet.
- Isi minimal: ringkasan, transaksi, item/varian satuan, shift, pembayaran,
  promo/diskon/member, customer/meja, pajak/biaya/pembulatan, dan produk terlaris.
- Gunakan preferensi `useThemeInExcel` yang sudah ada, format Rupiah/tanggal yang
  benar, nama sheet/file aman, freeze header, filter, dan lebar kolom terbaca.
- Export memakai snapshot database yang konsisten, tidak menahan UI, memberi
  progress/cancel/error yang aman, dan menangani data kosong maupun data besar.

Exit gate: workbook dibaca ulang secara otomatis, total sama dengan repository
laporan, file nyata dapat dibuka, analyzer/full suite, dan uji perangkat lulus.

Checkpoint implementasi Batch 9 pada 30 September 2026:

- Menu Manager baru **Laporan Excel** menyediakan rentang tanggal responsif dan
  dialog simpan sistem. Kasir tidak dapat membuka menu maupun memanggil snapshot
  export secara langsung.
- Workbook `.xlsx` dibuat offline tanpa dependency baru. Tujuh sheet mencakup
  Ringkasan, Transaksi, Item beserta varian satuan, Pembayaran, Promo, Shift,
  dan Produk Terlaris. Snapshot transaksi turut memuat customer/member/meja,
  pajak, biaya layanan, pembulatan, donasi, serta status/alasan pembatalan.
- Data ringkasan dan seluruh detail dibaca dalam satu transaksi database agar
  konsisten. Penyusunan ZIP/OOXML berjalan di isolate sehingga UI tetap aktif;
  tersedia status proses, pembatalan aman, pesan error generik, dan penanganan
  data kosong.
- Header memakai preferensi `useThemeInExcel`, dikunci, diberi filter, lebar
  kolom terbaca, nominal memakai format Rupiah, waktu mengikuti timezone toko,
  serta nama file/sheet diamankan.
- Workbook berhasil dibaca ulang otomatis; tujuh sheet/relasi OOXML, escaping,
  style, freeze/filter, dan total repository telah diverifikasi. Delapan test
  baru lulus, regresi terarah 64/64, seluruh test aplikasi **701/701**, analyzer
  **No issues found**, dan `git diff --check` bersih. Koreksi akhir agar jumlah
  transaksi memakai format angka biasa (bukan Rupiah) lulus test workbook 4/4
  dan analyzer terarah.
- APK profile terpasang sebagai update pada V2322 tanpa menghapus data. Proses
  aplikasi hidup, log startup bersih, dan SHA-256 lokal/perangkat identik:
  `7923b7c9b6b3249bb2c8014d9b9b230e038b4a762701470b785f2c219a6a93fe`.
- Audit file nyata dari V2322 membuktikan seluruh sheet berisi data: Ringkasan
  16 baris data, Transaksi 33, Item 55, Pembayaran 33, Promo 46, Shift 1, dan
  Produk Terlaris 2. Viewer spreadsheet HP hanya menampilkan dua sheet awal;
  hotfix kompatibilitas menambahkan metadata batas data, tinggi baris standar,
  dan posisi sel aktif pada setiap worksheet. Test export/UI lulus 6/6 dan
  analyzer bersih. APK hotfix terpasang sebagai update dengan SHA-256
  lokal/perangkat identik:
  `91f75b72b7690fdcfcfefbbd4d2fd3a8f1a5253ab5b0bb13775a6b8521f4f1eb`.

Gerbang manual Batch 9 yang masih terbuka: sebagai Manager buka **Laporan
Excel**, pilih rentang yang berisi transaksi, tekan **Buat & Simpan Excel**,
simpan sebagai **file baru** melalui dialog sistem, lalu buka file baru tersebut
di aplikasi spreadsheet pada HP (file lama tidak berubah setelah hotfix).
Bandingkan omzet/jumlah transaksi dengan Dashboard atau Riwayat dan periksa
sheet Transaksi, Item, Pembayaran, Promo, serta Shift. Setelah file nyata dapat
dibuka dan angka cocok, Batch 9 dinyatakan selesai dan lanjut Batch 10A.

#### Batch 10 - Rekonsiliasi schema dan regresi operasional

- **Batch 10A - Freeze schema dan migrasi:** bekukan schema fitur Tahap 20B-20C,
  regenerasi Drift/migration snapshot, dan pastikan upgrade seluruh versi lama ke
  versi final menjaga data.
- **Batch 10B - Backup otomatis tahan uninstall:** Manager memilih folder publik
  melalui dialog folder sistem. Aplikasi membuat backup atomik setelah tutup
  shift dan paling banyak sekali per hari, menyimpan beberapa generasi (target
  awal 7 harian dan 4 mingguan), serta menampilkan waktu/status backup terakhir
  dan peringatan jika backup terlambat. Backup tidak boleh hanya berada di
  penyimpanan private atau `Android/data` yang ikut terhapus saat uninstall.
- **Batch 10C - Disaster recovery/fresh install:** setelah aktivasi lisensi,
  onboarding menyediakan pilihan **Pulihkan Toko dari Backup** sebelum membuat
  setup/Manager baru. Restore memverifikasi checksum, versi schema, ruang kosong,
  dan rollback; identitas/lisensi perangkat tidak dipulihkan dari backup toko.
  Uji wajib mencakup uninstall/fresh install nyata, memilih `.kbackup` dari folder
  publik, memulihkan toko, login, transaksi, aset, serta riwayat secara utuh.
- **Batch 10D - Rekonsiliasi dan regresi operasional:** pastikan backup/restore,
  printer, laporan, audit, role, pencarian, scanner, TTS, satuan, shift, promo,
  meja, biaya/pembulatan, customer/member, QRIS, dan Excel bekerja bersama.
  Jalankan load test katalog besar, soak checkout/shift, analyzer, seluruh test,
  build profile, serta gerbang manual perangkat yang benar-benar membutuhkan
  hardware.

Exit gate: backup otomatis dapat ditemukan setelah uninstall, fresh-install
restore lulus tanpa kehilangan data, tidak ada regresi/data loss, dan source siap
masuk Tahap 21 security hardening tanpa perubahan fitur V1 yang belum terjadwal.

### 6. Tahap 21 - Android security hardening

- Audit manifest, exported component, permission, backup, FileProvider,
  cleartext, dan package visibility.
- Audit log/source/artifact untuk token, PIN, hash, salt, dan private key.
- Pastikan secure storage tidak memiliki fallback plaintext.
- Buat release keystore terpisah, backup terenkripsi, dan catat fingerprint.

Exit gate: no-secret audit bersih dan kedua release build tidak memakai debug
signing.

### 7. Tahap 22 - Build, versioning, dan CI

- Audit/upgrade `file_picker`, `print_bluetooth_thermal`, dan `speech_to_text`
  saat dependency freeze agar peringatan transisi Kotlin Gradle Plugin ke
  Built-in Kotlin tidak menjadi blocker pada Flutter versi mendatang; build
  dengan toolchain saat ini sudah lulus.

- Tetapkan app version/versionCode; pisahkan dari schema database, protocol
  lisensi, dan format backup.
- Clean generation, format check, analyze, full tests, dan build release.
- Periksa generated/migration consistency.
- Siapkan CI tanpa private signing key produksi.
- Verifikasi signature, nama artifact, dan SHA-256.
- Tentukan POS diterbitkan sebagai signed APK internal atau AAB Play Store.
  License Manager tetap distribusi internal.

Exit gate: artifact release reproducible, signed, terverifikasi, dan dapat
dipasang.

### 8. Tahap 23 - Full QA/UAT

- Fresh install dan alur POS lengkap.
- Upgrade data dari schema 7, 8, 9, dan versi final-1.
- Role Manager/Kasir dan seluruh jalur negatif utama.
- Printer, backup/restore, storage penuh, database locked, app killed, dan
  background/resume.
- Soak test: 100 checkout, login/logout, dan printer retry berulang.
- Catat build, device, tester, expected/actual, evidence, dan pass/fail.

Exit gate: UAT sign-off tanpa blocker data loss, migration, license, atau role.

### 9. Tahap 24 - Dokumentasi operasional

- Manual toko dan manual Super Admin.
- SOP backup/restore, private key, release keystore, recovery, dan incident.
- Privacy/data notice, release notes, upgrade steps, dan rollback plan.

Exit gate: operator dapat menjalankan activation, backup, recovery, update,
dan troubleshooting tanpa pengetahuan developer.

### 10. Tahap 25 - Release Candidate dan production

- Freeze schema dan fitur V1.
- Jalankan seluruh analyzer/test/build dari kondisi bersih.
- Pasang artifact release, bukan debug build atau `flutter run`.
- Verifikasi signature dan SHA-256, lalu smoke test kedua aplikasi.
- Pilot rollout pada satu perangkat/toko dan pantau transaksi, printer,
  backup, serta lisensi.
- Jika pilot stabil, lanjutkan publish/production rollout.
- Arsipkan artifact, checksum, release notes, migration snapshot, dan backup
  signing secara aman.

## GO/NO-GO production

GO hanya jika migration lama ke final, license persistence, role guard,
backup/recovery, private-key recovery, release signing, full tests, device UAT,
printer, dokumentasi, dan rollback plan semuanya lulus.

NO-GO jika ada full test merah, secret ditemukan, debug signing masih aktif,
upgrade dapat menghilangkan data/lisensi, private key hanya memiliki satu copy,
atau backup/restore belum memiliki keputusan risiko yang eksplisit.

## Ditunda setelah V1

- Multi-Manager.
- Cloud sync dan online revocation.
- Advanced analytics di luar daftar produk terlaris V1.
- Biometric POS.
- Golden test luas, signed CI, dan obfuscation jika belum dibutuhkan.
- Dukungan printer tambahan di luar perangkat target rilis.

## Format pencatatan setiap batch

Setiap entri baru di `fitur yg sudah diterapkan.md` memuat:

1. tahap dan batch;
2. perubahan yang berhasil diterapkan;
3. file/domain yang terdampak;
4. targeted dan regression test;
5. analyzer dan full test;
6. build/device test bila relevan;
7. schema/version;
8. pekerjaan atau risiko yang masih tersisa.
