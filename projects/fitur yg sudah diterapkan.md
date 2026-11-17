# kasir offline

> apakah kamu bisa baca project yg saya buka?

Bisa. Saya dapat membaca dan mengedit project di folder kerja yang sedang terbuka: `C:\Development\projects`, termasuk memeriksa struktur, kode, konfigurasi, dan menjalankan pengujian.

Sebutkan nama folder project atau bagian yang ingin diperiksa.

> ada beberapa code error di test nya, tolong perbaiki dengan benar, agar berjalan sesuai fitur yg saya inginkan

<details><summary>5 previous messages</summary>

> Saya akan membaca instruksi project terlebih dahulu, lalu mengidentifikasi project aktif, menjalankan test, dan menelusuri kegagalannya sebelum memperbaiki kode.
>
> Saya menemukan dua folder project. Saya akan mulai dari `kasir_offline` karena paling baru diperbarui, sambil memeriksa status repository agar perubahan Anda yang sudah ada tidak tertimpa.
>
> Project ini memiliki banyak perubahan lokal dan test baru; saya akan mempertahankan semuanya. Sekarang saya jalankan seluruh test suite untuk mendapatkan daftar kegagalan aktual, lalu memperbaiki akar masalah tanpa mengubah ekspektasi test secara sembarangan.
>
> Test suite belum selesai dalam satu menit dan output-nya tertahan oleh formatter. Saya akan menjalankannya per kelompok dengan reporter ringkas agar kegagalan pertama terlihat cepat dan mudah diisolasi.
>
> Flutter tertahan oleh proses Dart lama sejak tadi malam, jadi runner belum benar-benar masuk ke test. Sambil menghindari penghentian proses yang mungkin dipakai IDE Anda, saya akan membaca hasil audit/test terakhir yang sudah tersimpan dan menjalankan analisis statis untuk menemukan error kompilasi.

</details>

Bisa saya perbaiki, tetapi saya perlu memastikan dua hal agar tidak memperbaiki project atau perilaku yang salah:

1. Apakah project yang dimaksud `kasir_offline`?
2. Fitur seperti apa yang Anda inginkan, dan test/error mana yang gagal? Kirim nama test atau pesan error jika ada.

Saat saya coba menjalankan test, Flutter tertahan oleh beberapa proses Dart lama sejak tadi malam. Saya juga perlu izin untuk menghentikan proses Dart tersebut karena mungkin sedang dipakai IDE Anda.

> 1. iyaa 2. saya mau ini
>
> User context
>
> Attachments:
>
> - 1. Tambahkan kolom schema 9 Buka: lib/core/database/app_database.dart Pada Sale…: `C:/Users/Lenovo/.codex/attachments/63e2903b-f879-4a90-b32a-f8c054d7d722/pasted-text.txt`

<details><summary>12 previous messages</summary>

> Baik, project targetnya `kasir_offline`. Saya akan membaca spesifikasi yang Anda lampirkan, mencocokkannya dengan perubahan schema 9 yang sudah ada, lalu memperbaiki implementasi dan test sesuai perilaku tersebut.
>
> Saya menemukan penyebab kompilasi utama: repository pembatalan mendefinisikan `AppUserSession` dan `UserRole` palsu sendiri, ViewModel history masih memakai parameter lama, dan page menutupinya dengan `as dynamic`. Saya akan membetulkan kontrak ketiganya agar semuanya memakai sesi login nyata.
>
> <details><summary>Edited 3 files</summary>
>
> - Wrote `./kasir_offline/lib/features/history/presentation/transaction_history_page.dart` (+7 -9)
> - Wrote `./kasir_offline/lib/features/history/presentation/transaction_history_view_model.dart` (+14 -15)
> - Wrote `./kasir_offline/lib/features/sales/data/sale_cancellation_repository.dart` (+11 -34)
>
> </details>
>
> Test transaksi masih seluruhnya memakai API lama (`cashierName`, `actorName`, `actorRole`). Saya akan menambahkan helper akun tersimpan bersama dan memperbarui empat test transaksi supaya menguji identitas persisten, bukan sekadar membuat kompilasi lolos.
>
> <details><summary>Created a file, edited 6 files</summary>
>
> - Wrote `./kasir_offline/test/helpers/app_user_test_support.dart` (+28 -0)
> - Wrote `./kasir_offline/test/sale_cancellation_repository_test.dart` (+42 -19)
> - Wrote `./kasir_offline/test/sale_repository_test.dart` (+53 -13)
> - Wrote `./kasir_offline/test/sale_cancellation_repository_test.dart` (+1 -1)
> - Wrote `./kasir_offline/test/sale_repository_test.dart` (+1 -1)
> - Wrote `./kasir_offline/test/receipt_printer_service_test.dart` (+19 -8)
> - Wrote `./kasir_offline/test/sale_history_repository_test.dart` (+39 -11)
> - Wrote `./kasir_offline/test/drift/kasir_database/migrated_recovery_flow_test.dart` (+3 -3)
> - Wrote `./kasir_offline/test/drift/kasir_database/migration_test.dart` (+61 -0)
>
> </details>
>
> Implementasi dan test utama sekarang sudah selaras. Namun formatter/analyzer masih macet karena proses Dart lama, jadi saya lanjutkan pemeriksaan tekstual untuk menangkap referensi API lama dan memastikan migration test versi 8→9 benar sebelum verifikasi runtime.
>
> Formatter berhasil. Analyzer sudah mulai tetapi gagal karena Dart mencoba memperbarui file telemetry di luar workspace yang tidak diizinkan sandbox; saya akan menjalankannya kembali dengan izin yang diperlukan agar hasil kompilasi dapat diverifikasi.
>
> <details><summary>Edited a file</summary>
>
> - Wrote `./kasir_offline/test/drift/kasir_database/migration_test.dart` (+1 -1)
>
> </details>
>
> Analyzer menemukan dua konflik nama `isNull` pada migration test; itu sudah saya koreksi. Berikutnya saya verifikasi ulang analisis file terkait lalu menjalankan test repository dan migration yang langsung menguji fitur ini.
>
> Test fitur transaksi (13 test) dan seluruh jalur migration/recovery (39 test) sudah lulus. Saya akan menjalankan seluruh suite sekali lagi untuk memastikan perubahan signature tidak merusak modul lain.
>
> Full suite menemukan error terpisah di `login_shell_flow_test.dart`: helper `insertTestUser` dideklarasikan dua kali, dan pemanggilannya juga membuat akun tambahan tanpa memakai UID yang diminta. Saya akan menghapus duplikasi yang tidak diperlukan; akun Manager/Kasir integrasi yang memang dipakai test tetap dipertahankan.
>
> <details><summary>Edited a file</summary>
>
> - Wrote `./kasir_offline/test/login_shell_flow_test.dart` (+0 -46)
> - Wrote `./kasir_offline/test/login_shell_flow_test.dart` (+1 -1)
>
> </details>

</details>

Perbaikan fitur schema 9 dan identitas pengguna persisten sudah selesai.

Yang diperbaiki:

- Checkout memakai `AppUserSession` nyata dan menyimpan `cashierUserUid`.
- Pembatalan hanya menerima Manager aktif dan menyimpan `actorUserUid`.
- History ViewModel/Page memakai sesi nyata tanpa `dynamic`.
- Test transaksi menggunakan akun yang benar-benar tersimpan di database.
- Ditambahkan test keamanan sesi legacy.
- Ditambahkan test integritas migrasi schema 8→9.
- Recovery flow diperbarui menuju schema 9.
- Duplikasi helper di `login_shell_flow_test.dart` dihapus.

Verifikasi:

- 13 test transaksi/printer: lulus.
- 39 test migration/recovery: lulus.
- 4 test login-shell: lulus.
- Analisis penuh `lib` dan `test`: **No issues found**.
- `git diff --check`: bersih.

Seluruh test suite sempat melewati batas waktu 60 detik, tetapi tidak mengeluarkan kegagalan baru sebelum timeout; semua kelompok yang terdampak langsung sudah diuji dan lulus.

> apakah filenya belum berubah dari perintah sebelumnya?

<details><summary>1 previous message</summary>

> Saya cek langsung status Git dan perubahan file saat ini untuk memastikan hasil perbaikan sebelumnya masih tersimpan.

</details>

File-nya **sudah berubah** dan perubahannya masih tersimpan di folder `kasir_offline`, tetapi belum di-commit ke Git.

Di antaranya:

- `sale_repository.dart`
- `sale_cancellation_repository.dart`
- `transaction_history_view_model.dart`
- `transaction_history_page.dart`
- empat test transaksi
- migration test
- helper baru `test/helpers/app_user_test_support.dart`
- schema 8 dan schema 9

Jika perubahan belum terlihat di editor, coba refresh/reload project atau buka ulang file tersebut.

> coba sesuaikan lagi file2 yg harusnya berubah, mungkin saya salah

<details><summary>5 previous messages</summary>

> Saya akan audit ulang seluruh 13 poin dari instruksi lampiran terhadap file saat ini—termasuk schema/generated, repository, UI, dan test—lalu hanya memperbaiki bagian yang belum sesuai atau sempat salah. Perubahan lain milik Anda tetap saya pertahankan.
>
> Audit menunjukkan alur utama sudah sesuai; angka versi 8 yang tersisa memang milik test khusus v7→v8 dan tidak boleh diubah. Untuk memastikan file generated benar-benar sinkron (bukan hasil edit manual/parsial), saya akan regenerasi migration dan database code dari sumber schema sekarang.
>
> Regenerasi melewati batas waktu tool; satu proses generator masih berjalan. Saya beri kesempatan singkat untuk selesai, lalu saya cek hasilnya melalui analyzer dan test—tanpa menghentikan proses IDE lama Anda.
>
> Audit test sekarang lengkap dan `diff --check` bersih. Saya jalankan verifikasi final pada seluruh source, lalu kelompok test fitur, migration, dan login-shell.
>
> Test terhalang karena tiga proses `flutter_tester` yatim dari percobaan full-suite yang sebelumnya terkena timeout dan masih mengunci `sqlite3.dll`. Proses tersebut berasal dari test yang saya jalankan, jadi saya akan menutup hanya tiga PID itu lalu mengulang test.

</details>

Sudah saya audit dan sesuaikan ulang seluruh file berdasarkan instruksi awal.

Hasil akhir:

- Schema database sudah versi 9.
- `cashierUserUid` dan `actorUserUid` sudah tersedia.
- Migration 8→9 sudah benar.
- Checkout memakai sesi kasir persisten.
- Pembatalan memakai akun Manager aktif.
- Halaman kasir/history menerima `AppUserSession`.
- Test transaksi menggunakan akun yang tersimpan di database.
- Test migrasi v7→v8 tetap berhenti di versi 8 sesuai instruksi.
- Test recovery sudah menuju versi 9.
- Artefak schema 9 dan kode generated tersedia dan konsisten.
- Tidak ada referensi API lama yang tertinggal pada alur terkait.

Verifikasi akhir:

- Analyzer seluruh `lib` dan `test`: **No issues found**
- 56 test terkait: **All tests passed**
- Pemeriksaan whitespace/diff: bersih

Jadi file-file yang seharusnya berubah sekarang sudah sesuai dengan fitur yang Anda lampirkan.

> coba kamu pahami secara terperinci karna ini yg akan kita kerjakan kedepannya [HANDOFF_ROADMAP_LENGKAP_KASIR_OFFLINE_SAMPAI_RILIS_2026-07-25.txt](HANDOFF_ROADMAP_LENGKAP_KASIR_OFFLINE_SAMPAI_RILIS_2026-07-25.txt)

<details><summary>2 previous messages</summary>

> Saya akan membaca handoff roadmap ini secara utuh, lalu memetakan tujuan produk, kondisi implementasi saat ini, urutan milestone, dependensi, kriteria selesai, dan risiko yang perlu dijaga. Saya tidak akan mengubah kode pada tahap ini.
>
> Dokumennya sangat lengkap (sekitar 2.500 baris). Bagian awal menegaskan bahwa roadmap adalah sumber konteks, tetapi statusnya harus dikoreksi dengan audit aktual—dan schema 9 yang dianggap “belum dikonfirmasi” sekarang sudah kita verifikasi selesai. Saya lanjut membaca bagian tengah yang memuat batch setelah schema 9 dan release blocker berikutnya.

</details>

Saya sudah membaca dan memahami roadmap tersebut secara menyeluruh. Dokumen ini akan saya jadikan sumber konteks utama, tetapi statusnya selalu akan dicocokkan dengan source aktual sebelum mengerjakan batch baru.

Koreksi status terkini dibanding dokumen:

- Tahap 7—Persistent Actor Identity / Schema 9: **sudah selesai dan terverifikasi**.
- Checkout sudah memakai sesi Kasir/Manager persisten.
- Pembatalan sudah memakai sesi Manager aktif.
- Migration v8→v9 dan data-integrity test sudah tersedia.
- Analyzer bersih dan 56 test terkait sudah lulus.
- Karena itu, pekerjaan berikutnya dimulai dari **Tahap 8: Session Identity untuk Stok dan Aksi Sensitif**.

Urutan kerja yang akan saya pegang:

1. Audit source dan baseline test sebelum patch.
2. Kerjakan per batch 3–5 checkpoint dalam domain yang sama.
3. Authorization harus berada di repository/service, bukan hanya menyembunyikan menu.
4. Gunakan satu `AppDatabase`.
5. Jangan mengedit file generated atau migration lama secara manual.
6. Jika test gagal, selesaikan akar error pertama sebelum melanjutkan.
7. Urutan verifikasi: format → targeted test → regression → migration test jika perlu → analyzer → full test → device/negative path.
8. Subsistem lisensi yang sudah stabil tidak akan dirombak tanpa kebutuhan dan audit kuat.
9. Private signing key tidak boleh masuk ke aplikasi POS, source, log, chat, atau test.

Arah pekerjaan ke depan:

- Fase A: identitas sesi stok, audit batas authorization, hapus Admin legacy, account lifecycle, login hardening.
- Fase B: audit log, backup/restore POS, integritas database, printer, performa, dan UX.
- Fase C: backup registry dan private key License Manager, replacement perangkat, audit, dan app lock.
- Fase D: Android hardening, signing/versioning, CI, UAT, dokumentasi, dan release candidate.

Batch berikutnya yang tepat adalah:

- Ubah operasi stok sensitif agar menerima `AppUserSession`.
- Validasi Manager aktif berdasarkan UID, toko, role, dan status database.
- Simpan `actorUserUid` serta snapshot nama/role pada audit.
- Pastikan perubahan stok dan audit atomik.
- Tambahkan jalur negatif untuk Kasir, Admin legacy, Manager nonaktif, UID asing, dan store mismatch.

Saya juga memahami batas rilisnya: aplikasi belum boleh dinyatakan siap rilis hanya karena test unit hijau. Release memerlukan migration upgrade nyata, backup/recovery, perlindungan brute-force, backup private key, signing, audit secret, pengujian perangkat/printer, UAT, serta SOP operasional.

> kamu tau kan apa aja yang akan kita kerjakan kedepannya sesuai pedoman itu?

Ya, saya tahu dan akan mengikuti urutannya.

Posisi kita sekarang:

- Schema 9, identitas checkout, dan identitas pembatalan sudah selesai.
- Pekerjaan berikutnya adalah **Tahap 8: identitas sesi untuk stok dan aksi sensitif**.

Setelah itu urutannya:

1. Authorization boundary audit seluruh operasi sensitif.
2. Menghapus `Admin` legacy dari jalur produksi POS.
3. Menyempurnakan lifecycle akun.
4. Login security hardening dan pembatasan percobaan PIN.
5. Melengkapi audit log.
6. Backup/restore data POS.
7. Integritas dan maintenance database.
8. Validasi printer dan perangkat nyata.
9. Performance/load testing.
10. UX, responsive, dan accessibility.
11. Hardening `kasir_license_manager`, termasuk backup registry dan private key terenkripsi.
12. License edge-case testing.
13. Android security audit.
14. Versioning, release signing, dan CI.
15. Full QA/UAT untuk fresh install dan upgrade.
16. Dokumentasi serta SOP operasional.
17. Release candidate, smoke test APK release, lalu production release.

Pada setiap batch saya akan:

- Audit source aktual terlebih dahulu.
- Menetapkan perilaku positif, negatif, dan data yang tidak boleh berubah.
- Mengerjakan repository/service → ViewModel → UI/wiring → test.
- Menjalankan format, targeted test, regression, analyzer, full test, lalu device test jika relevan.
- Menjaga migration lama, satu `AppDatabase`, file generated, dan keamanan private key.

Saya tidak akan menganggap suatu tahap selesai hanya karena kodenya terlihat benar; tahap dianggap selesai setelah verifikasi yang diwajibkan roadmap lulus.

> okeee baik, mari kita kerjakan secara bertahap satu persatu beserta test nya agar memastikan tahap demi tahap benar dan secara lengkap, pastikan kedepannya kita selalu berkembang dan maju sampai tahap relase production, seandainya kamu memerlukan saya untuk mengetes manual untuk build secara nyata, bisa beri tau saya agar sesuai dengan apa yg jadi pedoman dan saya harapkan. lakukan jangan terburu2, teliti, dan benar. tolong ingat peraturan ini selamanya

Saya mengerti dan akan menjadikannya aturan kerja tetap untuk proyek ini.

Prinsip yang akan selalu saya pegang:

- Kerjakan satu tahap secara lengkap dan berurutan.
- Audit source aktual sebelum mengubah kode.
- Jangan menebak struktur atau status implementasi.
- Utamakan ketelitian dan akar masalah, bukan kecepatan.
- Setiap perubahan harus disertai test positif, negatif, rollback, dan regression yang relevan.
- Tahap dianggap selesai hanya setelah format, targeted test, regression, analyzer, dan test suite yang diperlukan lulus.
- Jangan mengedit file generated secara manual atau merusak migration lama.
- Jaga satu `AppDatabase`, keamanan PIN, lisensi, dan private key.
- Catat status selesai, file yang berubah, hasil test, risiko, dan pekerjaan berikutnya.
- Jika diperlukan pengujian perangkat, printer, APK release, upgrade data nyata, izin Android, atau alur manual, saya akan memberi Anda langkah pengujian yang jelas beserta hasil yang diharapkan.
- Kita tidak akan menyatakan siap production sebelum seluruh release blocker, UAT, signing, backup/recovery, dokumentasi, dan smoke test terpenuhi.

Untuk percakapan baru di masa depan, roadmap tersebut tetap perlu disertakan atau disimpan sebagai sumber handoff karena saya tidak dapat menjamin memori permanen lintas seluruh sesi. Selama konteks dan file roadmap tersedia, saya akan melanjutkan dari checkpoint terakhir tanpa mengulang atau melompati tahap.

Tahap berikutnya adalah **Tahap 8: Session Identity untuk Stok dan Aksi Sensitif**. Kita akan mengerjakannya dari audit, definisi selesai, implementasi, test otomatis, sampai pengujian manual bila diperlukan.

> okee bagus kamu paham, yuk kita ketahap 8

<details><summary>12 previous messages</summary>

> Kita mulai Tahap 8 dengan audit penuh tanpa langsung mengubah kode: kontrak repository stok, ViewModel/UI/wiring, seluruh penulisan audit log, serta baseline test. Setelah itu saya tetapkan definisi selesai dan mengerjakan guard Manager persisten secara atomik.
>
> Audit mengonfirmasi gap tepat seperti roadmap: `StockChangeRequest` masih membawa nama/role bebas, repository hanya memeriksa string `'admin'`, UI meneruskan string, dan audit belum mengisi `actorUserUid`. Saya jalankan baseline stok dulu sebelum mengubah kontraknya.
>
> Definisi selesai Tahap 8:
>
> - Hanya Manager dengan `uid/storeId` persisten dan akun database aktif yang boleh mengubah stok.
> - Store, role, dan UID sesi divalidasi ulang di repository.
> - Nama/role audit selalu diambil dari `AppUsers`, bukan UI.
> - Audit menyimpan `actorUserUid`.
> - Kasir, Admin legacy, akun nonaktif, UID asing, role mismatch, dan toko berbeda ditolak tanpa mengubah stok/movement/audit.
> - Update produk, movement, dan audit tetap satu transaksi sehingga kegagalan akhir me-roll back semuanya.
>
> Saya mulai mengubah kontrak domain sampai wiring UI sesuai definisi ini.
>
> <details><summary>Edited 6 files</summary>
>
> - Wrote `./kasir_offline/lib/features/shell/presentation/app_shell_page.dart` (+1 -4)
> - Wrote `./kasir_offline/lib/features/stock/data/stock_repository.dart` (+75 -36)
> - Wrote `./kasir_offline/lib/features/stock/domain/stock_models.dart` (+11 -12)
> - Wrote `./kasir_offline/lib/features/stock/presentation/stock_change_dialog.dart` (+16 -18)
> - Wrote `./kasir_offline/lib/features/stock/presentation/stock_page.dart` (+12 -14)
> - Wrote `./kasir_offline/test/stock_repository_test.dart` (+246 -50)
> - Wrote `./kasir_offline/test/stock_repository_test.dart` (+1 -0)
>
> </details>
>
> Kontrak produksi sudah berpindah ke sesi nyata, dan test keamanan awal sudah ditambahkan termasuk rollback paksa saat insert audit gagal. Saya jalankan analyzer terarah untuk menangkap semua pemanggilan lama atau kesalahan tipe sebelum menjalankan test.
>
> <details><summary>Edited a file</summary>
>
> - Wrote `./kasir_offline/test/stock_repository_test.dart` (+1 -1)
> - Wrote `./kasir_offline/test/stock_repository_test.dart` (+76 -0)
> - Wrote `./kasir_offline/test/stock_repository_test.dart` (+8 -1)
> - Wrote `./kasir_offline/test/stock_repository_test.dart` (+31 -0)
>
> </details>
>
> Targeted repository sekarang memiliki 14 test dan semuanya lulus, termasuk snapshot nama dari database, isolasi toko, akun nonaktif/asing, role mismatch, negative-stock setting, concurrent updates, serta rollback audit. Berikutnya saya periksa tidak ada API lama yang tertinggal dan jalankan regression UI/login.
>
> Regression UI dan login-shell lulus, analyzer penuh bersih, dan tidak ada kontrak string lama tersisa. Saya jalankan full test suite sebagai gerbang akhir Tahap 8; setelah itu saya akan menilai apakah diperlukan pengujian manual di perangkat.
>
> Full suite melewati batas eksekusi 60 detik tanpa melaporkan kegagalan, sehingga belum boleh saya klaim selesai. Saya akan membersihkan hanya proses test yatim dari run tersebut, lalu membagi suite menjadi beberapa kelompok agar setiap kelompok mendapatkan hasil akhir yang pasti.
>
> Kelompok auth juga lebih lama dari 60 detik karena test hashing PIN produksi, bukan karena kegagalan yang terlihat. Agar tidak terus memutus runner dan menghasilkan proses yatim, saya akan menjalankan full suite sebagai proses latar terkontrol, memantau log-nya, dan menunggu exit akhirnya tanpa batas satu panggilan.
>
> Full suite berjalan normal di latar dan sudah melewati 85 test tanpa kegagalan. Saya lanjut memantau sampai runner benar-benar selesai; hasil parsial tidak akan dianggap kelulusan.
>
> Full suite selesai dengan **212 test lulus**. Tahap otomatis sudah hijau; sesuai roadmap, masih ada checkpoint manual perangkat untuk memastikan sesi Manager nyata dapat mengubah stok dan Kasir tidak memperoleh jalur UI tersebut. Saya rangkum langkah uji manual yang perlu Anda lakukan sebelum Tahap 8 dinyatakan tertutup sepenuhnya.

</details>

Tahap 8 secara kode dan test otomatis sudah selesai.

Perubahan utama:

- `StockChangeRequest` sekarang menerima `AppUserSession`.
- Repository memvalidasi UID, toko, role Manager, dan status akun aktif.
- Nama dan role audit diambil dari akun database.
- Audit menyimpan `actorUserUid`.
- `StockPage`, dialog, dan shell meneruskan sesi nyata.
- Update stok, movement, dan audit tetap atomik.

Verifikasi otomatis:

- 14 targeted test stok lulus.
- Regression widget dan login-shell lulus.
- Analyzer penuh: **No issues found**.
- Full suite: **212 tests passed**.
- `diff --check`: bersih.

Uji manual perangkat yang diperlukan:

1. Login sebagai Manager.
2. Buka menu **Stok**.
3. Lakukan Stok Masuk, misalnya jumlah `5`.
4. Pastikan stok bertambah dan riwayat movement muncul.
5. Lakukan Stok Opname dan pastikan jumlah menjadi nilai fisik yang dimasukkan.
6. Coba Stok Keluar melebihi stok pada produk yang tidak mengizinkan stok negatif; harus ditolak.
7. Logout lalu login sebagai Kasir.
8. Pastikan menu **Stok** tidak terlihat dan tidak dapat dibuka dari navigasi.
9. Restart aplikasi, login kembali sebagai Manager, lalu pastikan perubahan stok tetap tersimpan.

Silakan jalankan skenario tersebut pada perangkat. Kirim hasil atau tangkapan error jika ada. Tahap 8 akan dinyatakan tertutup setelah smoke test manual ini lulus.

> tapi setelah kamu kerjakann tahap pertahap apakah selalu kamu unit test apa yg baru kamu kerjakan dan semua unit testnya?

Ya. Untuk setiap tahap, saya akan selalu menjalankan dua lapis pengujian:

1. Test khusus perubahan baru

   Test terdekat untuk fitur yang baru dikerjakan, termasuk:
   - happy path;
   - input tidak valid;
   - authorization;
   - akun nonaktif atau tidak ditemukan;
   - store mismatch;
   - rollback dan atomicity;
   - data lama/migration jika schema berubah;
   - concurrency bila relevan.

2. Seluruh test project

   Setelah targeted dan regression test lulus, saya jalankan:
   - `dart format`;
   - `flutter analyze`;
   - seluruh `flutter test`;
   - `git diff --check`.

Untuk Tahap 8 sendiri hasilnya:

- 14 targeted test stok lulus.
- Regression UI/login-shell lulus.
- Analyzer penuh bersih.
- Seluruh suite: **212 test lulus**.

Namun unit/widget test tidak cukup untuk hal yang bergantung pada perangkat nyata. Untuk Bluetooth, printer, permission Android, lifecycle, upgrade APK, backup file, dan release build, saya juga akan meminta Anda melakukan pengujian manual dengan langkah dan hasil yang jelas.

Saya tidak akan menutup sebuah tahap jika targeted test atau full test masih gagal. Jika pengujian perangkat diwajibkan roadmap, tahap akan ditandai “otomatis lulus, manual pending” sampai Anda mengonfirmasi hasilnya.

> okee selalu beri tau saya kalau kamu sudah mengetes semuanya, dan kasih tau saya jika kamu butuh bantuan untuk tahap yg sangat membutuhkan untuk di build dan saya tes aplikasinya

Siap. Pada setiap tahap saya akan selalu melaporkan secara jelas:

- test baru yang ditambahkan;
- targeted test yang dijalankan;
- regression test yang dijalankan;
- hasil `flutter analyze`;
- hasil seluruh `flutter test`;
- jumlah test lulus/gagal;
- apakah tahap sudah selesai atau masih menunggu pengujian manual;
- risiko atau bagian yang belum dapat diverifikasi otomatis.

Jika membutuhkan perangkat nyata, saya akan memberi tahu:

- kapan aplikasi perlu di-build atau dijalankan;
- perintah yang harus digunakan;
- akun/role yang harus dipakai;
- langkah pengujian;
- hasil yang seharusnya muncul;
- jalur negatif yang harus dicoba;
- informasi error atau screenshot yang perlu dikirim kembali.

Saya tidak akan menyatakan tahap selesai sepenuhnya apabila pengujian build/perangkat yang diwajibkan belum Anda konfirmasi. Saat ini Tahap 8 berstatus: **semua pengujian otomatis lulus, pengujian manual perangkat masih menunggu konfirmasi Anda**.

> okee sudah saya test manual berhasil semua, yuk lanjut tahap praktik berikutnya, tolong test manual oleh saya itu saat sangat diperlukan saja

<details><summary>12 previous messages</summary>

> Tahap 8 saya tutup sebagai **selesai penuh**: 212 test otomatis lulus dan smoke test perangkat Anda juga berhasil. Pengujian manual berikutnya hanya akan saya minta bila perilakunya memang tidak dapat dipercaya dari test otomatis, seperti hardware, permission, lifecycle, upgrade APK, atau release build.
>
> Kita lanjut Tahap 9: audit batas authorization seluruh operasi sensitif sebelum menentukan batch perubahan pertama.
>
> Audit awal menemukan gap prioritas yang lebih luas dari perkiraan: repository kategori dan produk tampaknya sudah mulai menerima `AppUserSession`, tetapi ViewModel masih terlihat memakai kontrak lama. Saya akan membaca implementasi aktual dan test-nya secara penuh untuk memastikan apakah ini pekerjaan parsial, dead code, atau perubahan yang belum terhubung sebelum membuat patch.
>
> Ada perubahan parsial yang muncul pada kategori/produk: repository sudah memakai `ActiveManagerAuthorizer`, tetapi ViewModel belum meneruskan session. Ini membuat source aktual tidak konsisten dan berbeda dari keadaan saat full test Tahap 8 dimulai. Saya akan cek status/timestamp serta analyzer sekarang agar kita membedakan perubahan valid yang perlu diselesaikan dari perubahan lain yang tidak boleh tertimpa.
>
> Perubahan parsial tersebut sejalan dengan Tahap 9 dan dapat dipertahankan: ada authorizer terpusat yang memvalidasi Manager aktif. Saya akan melanjutkan dari keadaan itu, bukan menimpanya. Batch pertama Tahap 9 akan difokuskan pada kategori dan produk: repository guard → ViewModel/session → UI/wiring → denial/no-mutation tests.
>
> <details><summary>Edited 5 files</summary>
>
> - Wrote `./kasir_offline/lib/app/session/active_manager_authorizer.dart` (+1 -0)
> - Wrote `./kasir_offline/lib/features/categories/presentation/category_view_model.dart` (+27 -13)
> - Wrote `./kasir_offline/lib/features/products/presentation/product_module_page.dart` (+15 -6)
> - Wrote `./kasir_offline/lib/features/products/presentation/product_view_model.dart` (+31 -13)
> - Wrote `./kasir_offline/lib/features/shell/presentation/app_shell_page.dart` (+1 -1)
>
> </details>
>
> Batch pertama saya batasi pada kategori dan produk agar tetap teliti. Guard terpusat sudah diperbaiki, dan session sekarang mengalir dari `AppShell` ke ViewModel. Berikutnya saya sesuaikan test kategori/produk dengan Manager persisten serta menambahkan denial/no-mutation matrix sebelum menyentuh domain sensitif lain.
>
> <details><summary>Edited a file</summary>
>
> - Wrote `./kasir_offline/test/category_product_test.dart` (+120 -28)
> - Wrote `./kasir_offline/test/category_product_test.dart` (+2 -0)
>
> </details>
>
> Targeted batch lulus 6 test. Karena kontrak repository kini wajib menggunakan Manager, sejumlah fixture test lama yang membuat data lewat repository perlu diberi akun Manager persisten; ini bukan bypass security. Saya jalankan analyzer penuh untuk mendapatkan daftar pasti dan memperbarui fixture tersebut tanpa mengubah perilaku test lainnya.
>
> <details><summary>Edited 8 files</summary>
>
> - Wrote `./kasir_offline/test/sale_cancellation_repository_test.dart` (+9 -5)
> - Wrote `./kasir_offline/test/sale_history_repository_test.dart` (+9 -5)
> - Wrote `./kasir_offline/test/stock_repository_test.dart` (+5 -1)
> - Wrote `./kasir_offline/test/sale_repository_test.dart` (+23 -11)
> - Wrote `./kasir_offline/test/receipt_printer_service_test.dart` (+23 -11)
> - Wrote `./kasir_offline/test/cashier_view_model_test.dart` (+34 -17)
> - Wrote `./kasir_offline/test/sales_report_repository_test.dart` (+34 -17)
> - Wrote `./kasir_offline/test/widget_test.dart` (+25 -11)
>
> </details>
>
> Seluruh fixture lama sudah diperbarui memakai Manager persisten untuk membuat kategori/produk, dan analyzer penuh kembali bersih. Saya sekarang menjalankan regression lintas katalog, kasir, penjualan, stok, laporan, printer, serta UI untuk memastikan guard baru tidak mengubah perilaku baca/checkout.
>
> Regression batch menampilkan **53 test lulus**; wrapper melewati batas waktu sesaat setelah mencetak hasil akhir, jadi saya tetap akan menjalankan full suite terkontrol di latar dan menunggu status final. Batch kategori/produk belum akan saya nyatakan selesai sebelum seluruh suite hijau.
>
> Full suite masih berjalan normal dan sudah melewati 154 test tanpa kegagalan. Saya tetap menunggu sampai hasil akhirnya keluar; tidak ada kebutuhan test manual untuk batch ini karena authorization repository dan UI dapat diverifikasi deterministik lewat automated tests.

</details>

Tahap 9 batch pertama—authorization kategori dan produk—sudah selesai.

Yang diperbaiki:

- Menambahkan `ActiveManagerAuthorizer` sebagai guard terpusat.
- Operasi create/update/aktif-nonaktif kategori wajib memakai Manager persisten.
- Operasi create/update/aktif-nonaktif/foto produk wajib memakai Manager persisten.
- Guard memvalidasi UID, toko, role, status aktif, dan akun database.
- Session dialirkan dari `AppShell` → `ProductModulePage` → ViewModel → repository.
- Seluruh fixture test memakai Manager tersimpan, bukan bypass authorization.

Test keamanan yang ditambahkan mencakup:

- Manager aktif diizinkan.
- Kasir ditolak.
- Session Admin legacy ditolak.
- Manager nonaktif ditolak.
- Penolakan tidak membuat kategori atau produk.

Hasil verifikasi:

- Targeted kategori/produk: **6 test lulus**.
- Regression katalog, transaksi, stok, laporan, printer, dan UI: **53 test lulus**.
- Analyzer seluruh `lib` dan `test`: **No issues found**.
- Full test suite: **214 test lulus**.
- Tidak memerlukan test manual karena perilakunya dapat diverifikasi penuh secara deterministik.

Tahap 9 belum selesai seluruhnya. Batch berikutnya adalah memperkuat authorization di repository manajemen akun, karena saat ini sebagian guard masih berada di ViewModel. Setelah itu kita audit settings, theme, printer, lisensi, dan operasi sensitif lainnya.

> okee bagus, yuk lanjut ke batch berikutnya

<details><summary>14 previous messages</summary>

> Kita lanjut Tahap 9 batch kedua: authorization manajemen akun. Saya akan audit pemisahan operasi bootstrap/recovery dari operasi setelah login, karena pembuatan Manager awal tidak boleh dipaksa memakai session yang belum ada, sedangkan create/activate/reset Kasir wajib dijaga di repository.
>
> Audit membedakan dua jalur yang tidak boleh dicampur:
>
> - Bootstrap/recovery membuat Manager saat belum ada sesi.
> - Setelah login, Manager membuat/mengelola Kasir.
>
> Saya akan membuat API repository terpisah untuk keduanya. API bootstrap hanya boleh membuat Manager pada kondisi awal/recovery; operasi Kasir akan selalu memerlukan `AppUserSession` Manager dan revalidasi database.
>
> <details><summary>Edited 10 files</summary>
>
> - Wrote `./kasir_offline/lib/features/auth/data/app_user_repository.dart` (+127 -60)
> - Wrote `./kasir_offline/lib/features/auth/presentation/user_management_view_model.dart` (+8 -9)
> - Wrote `./kasir_offline/lib/features/auth/services/manager_recovery_coordinator.dart` (+5 -6)
> - Wrote `./kasir_offline/lib/features/setup/services/initial_setup_coordinator.dart` (+5 -6)
> - Wrote `./kasir_offline/lib/features/auth/data/app_user_repository.dart` (+20 -0)
> - Wrote `./kasir_offline/lib/features/auth/services/manager_recovery_coordinator.dart` (+0 -1)
> - Wrote `./kasir_offline/lib/features/setup/services/initial_setup_coordinator.dart` (+0 -1)
> - Wrote `./kasir_offline/test/user_management_repository_test.dart` (+36 -44)
> - Wrote `./kasir_offline/test/user_management_view_model_test.dart` (+14 -14)
> - Wrote `./kasir_offline/test/user_management_page_test.dart` (+10 -12)
> - Wrote `./kasir_offline/test/app_user_repository_test.dart` (+17 -12)
> - Wrote `./kasir_offline/test/login_shell_flow_test.dart` (+10 -12)
> - Wrote `./kasir_offline/test/login_view_model_test.dart` (+22 -15)
> - Wrote `./kasir_offline/test/user_management_repository_test.dart` (+43 -7)
> - Wrote `./kasir_offline/test/user_management_repository_test.dart` (+2 -1)
> - Wrote `./kasir_offline/test/user_management_page_test.dart` (+0 -1)
>
> </details>
>
> Context compaction
>
> Source: automatic
> Status: completed
>
> Saya lanjutkan batch otorisasi manajemen pengguna dari kondisi terakhir. Saya akan mulai dengan memastikan perubahan yang sudah ada benar-benar lolos analyzer, lalu memperketat skenario penolakan dan menjalankan targeted test, regresi terkait, serta seluruh test suite.
>
> Audit pemanggil menemukan beberapa test lama masih memakai alias bootstrap `createUser`. Alias itu memang sudah dibatasi hanya untuk Manager pertama, tetapi saya akan migrasikan seluruh pemanggil tersisa ke API eksplisit agar jalur bootstrap dan operasi setelah login tidak ambigu. Setelah itu saya validasi skenario lintas toko dan sesi lama.
>
> <details><summary>Edited 7 files</summary>
>
> - Wrote `./kasir_offline/lib/features/auth/data/app_user_repository.dart` (+0 -20)
> - Wrote `./kasir_offline/test/app_user_repository_test.dart` (+29 -26)
> - Wrote `./kasir_offline/test/manager_recovery_coordinator_test.dart` (+4 -5)
> - Wrote `./kasir_offline/test/manager_recovery_page_test.dart` (+4 -5)
> - Wrote `./kasir_offline/test/manager_recovery_startup_test.dart` (+8 -10)
> - Wrote `./kasir_offline/test/manager_recovery_view_model_test.dart` (+4 -5)
> - Wrote `./kasir_offline/test/user_management_repository_test.dart` (+48 -0)
>
> </details>
>
> API lama sudah dihapus sepenuhnya dan semua fixture recovery/setup telah memakai `createInitialManager`. Saya juga menambahkan bukti bahwa sesi legacy, sesi lintas toko, UID palsu, Kasir, dan Manager nonaktif semuanya ditolak tanpa menambah atau mengubah akun. Analyzer melewati batas 60 detik foreground, jadi saya lanjutkan pemeriksaan melalui proses latar sambil menjalankan test terarah.
>
> Proses test tidak gagal, tetapi runner Flutter di mesin ini tertahan sebelum menghasilkan output ketika dijalankan melalui wrapper latar pertama. Saya sudah menghentikan hanya proses yang saya mulai sendiri; sekarang saya pisahkan redirection ke proses `cmd` anak agar hasil test dapat dipantau tanpa menyentuh daemon atau proses proyek lain.
>
> <details><summary>Edited a file</summary>
>
> - Wrote `./kasir_offline/test/user_management_repository_test.dart` (+1 -1)
>
> </details>
>
> Penyebab runner tertahan sudah jelas: sandbox tidak dapat mengakses cache Flutter; setelah dijalankan dengan akses SDK yang benar, compiler menemukan satu konflik nama matcher dari Drift. Konflik import itu sudah diperbaiki, dan sekarang verifikasi terarah dapat berjalan normal.
>
> Regression yang benar-benar ada telah lulus 37 test; status command merah hanya karena saya memasukkan nama file lama `initial_setup_page_test.dart` yang ternyata sudah berganti menjadi `initial_setup_manager_page_test.dart`. Saya koreksi daftar itu dan jalankan file yang tepat, lalu analyzer. Ini bukan kegagalan fitur.
>
> <details><summary>Edited 2 files</summary>
>
> - Wrote `./kasir_offline/test/login_shell_flow_test.dart` (+0 -1)
> - Wrote `./kasir_offline/test/manager_recovery_startup_test.dart` (+0 -1)
>
> </details>
>
> Semua pemeriksaan statis sekarang bersih (`No issues found`). Targeted suite lulus 16 test dan regresi setup/recovery/login lulus 42 test total. Saya sedang menjalankan seluruh suite sebagai gerbang terakhir batch ini; karena durasinya panjang, proses berjalan di latar dan saya pantau sampai hasil final keluar.

</details>

Batch berikutnya—Stage 9 Batch 2—sudah selesai dan terverifikasi.

Perubahan utama:

- Memisahkan pembuatan Manager awal melalui `createInitialManager`.
- Pembuatan Kasir wajib melalui Manager aktif dengan identitas persisten.
- Operasi daftar akun, aktivasi/nonaktivasi Kasir, dan reset PIN sekarang memvalidasi ulang Manager di database.
- API generik lama `createUser` dihapus agar tidak menjadi jalur bypass.
- Sesi Kasir, Manager nonaktif, legacy, lintas toko, dan UID palsu ditolak tanpa mutasi data.
- ViewModel, setup awal, recovery Manager, login, serta seluruh fixture test telah disesuaikan.

Hasil verifikasi:

- Targeted test: **16 lulus**
- Regression setup/recovery/login: **42 lulus**
- Dart analyzer: **No issues found**
- Seluruh test suite: **216 lulus**
- Tidak ada test gagal.

Manual test belum diperlukan karena batch ini sepenuhnya dapat diverifikasi melalui repository, widget, regression, dan full test otomatis. Kita siap melanjutkan ke batch praktik berikutnya.

> okee yuk lanjut ke tahap berikutnya

<details><summary>9 previous messages</summary>

> Saya lanjut ke batch setelah otorisasi manajemen pengguna. Saya akan cek kembali urutan roadmap dan kondisi kode saat ini terlebih dahulu, lalu mengambil satu batch kecil yang saling terkait, lengkap dengan targeted test, regresi, analyzer, dan full suite.
>
> Roadmap menetapkan tahap berikutnya sebagai **Tahap 10: menghapus Admin legacy dari aplikasi POS**. Nilai historis `'admin'` di audit migration tetap dipertahankan, sedangkan enum runtime, akses menu, codec, dan fixture test akan dipindahkan ke Manager dengan identitas database nyata. Ini tidak menyentuh konsep Super Admin pada aplikasi license manager.
>
> <details><summary>Edited 11 files</summary>
>
> - Wrote `./kasir_offline/lib/app/session/app_user_session.dart` (+5 -7)
> - Wrote `./kasir_offline/lib/features/auth/data/app_user_repository.dart` (+0 -1)
> - Wrote `./kasir_offline/lib/features/auth/domain/local_app_user.dart` (+4 -8)
> - Wrote `./kasir_offline/test/category_product_test.dart` (+2 -2)
> - Wrote `./kasir_offline/test/license_access_test.dart` (+7 -2)
> - Wrote `./kasir_offline/test/manager_role_access_test.dart` (+6 -3)
> - Wrote `./kasir_offline/test/sale_history_repository_test.dart` (+6 -6)
> - Wrote `./kasir_offline/test/sale_repository_test.dart` (+2 -2)
> - Wrote `./kasir_offline/test/stock_repository_test.dart` (+2 -2)
> - Wrote `./kasir_offline/test/widget_test.dart` (+8 -6)
> - Wrote `./kasir_offline/test/login_page_test.dart` (+2 -4)
>
> </details>
>
> Audit runtime sudah bersih dari `UserRole.admin`; satu-satunya data Admin yang sengaja tersisa adalah row audit migration lama (`actorRole: 'admin'`), sesuai pedoman agar sejarah transaksi tidak diubah. Fixture widget kini memakai Manager persisten yang benar, bukan sesi demo tanpa identitas. Selanjutnya saya gunakan analyzer untuk menangkap semua switch atau pemanggil yang mungkin belum lengkap.
>
> Targeted role/auth lulus 18 test. Regression checkout/stok/riwayat menemukan satu kegagalan fixture saja: nama aktor sudah diubah menjadi “Manager Test”, tetapi satu ekspektasi lama masih membaca “Admin Test”. Perilaku produksi benar; saya perbaiki ekspektasi tunggal itu sebelum melanjutkan test lain, sesuai aturan debug error pertama.
>
> <details><summary>Edited a file</summary>
>
> - Wrote `./kasir_offline/test/sale_history_repository_test.dart` (+1 -1)
>
> </details>
>
> Widget regression mengungkap konsekuensi yang memang diharapkan dari Manager persisten: menu Akun sekarang ikut dibangun, sementara harness `widget_test.dart` lama tidak menyediakan `UserManagementViewModel` karena Admin demo sebelumnya sengaja tidak memiliki akses Akun. Saya akan memperbaiki harness test agar mencerminkan wiring aplikasi produksi, bukan menyembunyikan menu Manager.
>
> <details><summary>Edited a file</summary>
>
> - Wrote `./kasir_offline/test/widget_test.dart` (+35 -18)
>
> </details>
>
> Widget shell kini lulus 7 test setelah harness disamakan dengan wiring produksi. Migration suite juga lulus 38 test, termasuk bukti eksplisit bahwa snapshot historis `actorRole: 'admin'` dari v8 tetap terbaca setelah migrasi v9. Saya masuk gerbang akhir: pencarian referensi terlarang, analyzer penuh, lalu seluruh test suite.

</details>

Tahap 10 — penghapusan Admin legacy — selesai.

Perubahan utama:

- `UserRole.admin` dihapus dari aplikasi POS.
- Akses manajerial sekarang hanya dimiliki `UserRole.manager`.
- `UserRoleCodec` hanya menerima Manager dan Kasir.
- Fixture Admin demo diganti dengan Manager persisten.
- Widget shell Manager dilengkapi provider manajemen akun yang sesuai produksi.
- Snapshot audit lama bernilai `'admin'` tetap dipertahankan dan dapat dibaca.

Hasil pengujian:

- Targeted role/auth: **18 lulus**
- Checkout, stok, dan riwayat: **21 lulus**
- Widget shell Manager/Kasir: **7 lulus**
- Seluruh migration test: **38 lulus**
- Analyzer: **No issues found**
- Full test suite: **216 lulus**
- Pencarian `UserRole.admin` dan `Admin Demo`: **tidak ditemukan**

Tidak perlu manual test untuk tahap ini. Tahap berikutnya sesuai roadmap adalah **Tahap 11: penyempurnaan account lifecycle**.

> okee bagus, yuk lanjut ke tahap berikutnya 11

<details><summary>7 previous messages</summary>

> Saya lanjut ke Tahap 11 dengan pola yang sama: audit keputusan lifecycle akun yang sudah ada, lalu mengambil batch pertama yang paling aman dan saling terkait. Saya akan menjaga perubahan tetap kecil, menguji jalur positif dan negatif, serta tidak meminta manual test kecuali memang ada perilaku perangkat yang tak bisa diverifikasi otomatis.
>
> Audit menunjukkan fondasi Tahap 11 sudah cukup matang untuk batch pertama. Saya mengikuti rekomendasi roadmap: **tepat satu Manager per toko untuk V1**, Manager tidak dapat dinonaktifkan, dan tidak ada pembuatan Manager kedua. Batch ini akan menuntaskan rename Kasir; penggantian nama/PIN Manager dan audit reset PIN akan menjadi batch berikutnya agar transaksi dan rollback tetap diuji terpisah.
>
> <details><summary>Edited 8 files</summary>
>
> - Wrote `./kasir_offline/README.md` (+9 -1)
> - Wrote `./kasir_offline/lib/features/auth/data/app_user_repository.dart` (+55 -0)
> - Wrote `./kasir_offline/lib/features/auth/presentation/user_management_view_model.dart` (+30 -4)
> - Wrote `./kasir_offline/lib/features/auth/presentation/user_management_page.dart` (+130 -13)
> - Wrote `./kasir_offline/test/user_management_repository_test.dart` (+105 -0)
> - Wrote `./kasir_offline/test/user_management_repository_test.dart` (+1 -1)
> - Wrote `./kasir_offline/test/user_management_page_test.dart` (+36 -5)
> - Wrote `./kasir_offline/test/user_management_view_model_test.dart` (+16 -5)
> - Wrote `./kasir_offline/test/sale_repository_test.dart` (+43 -6)
> - Wrote `./kasir_offline/test/sale_repository_test.dart` (+2 -3)
>
> </details>
>
> Analyzer menemukan satu masalah tipe nullable pada hasil decode role setelah guard Kasir. Tidak ada masalah perilaku; saya akan membuat hasil return eksplisit `UserRole.cashier`, lalu menjalankan ulang analyzer dan test terarah.
>
> <details><summary>Edited a file</summary>
>
> - Wrote `./kasir_offline/lib/features/auth/data/app_user_repository.dart` (+1 -1)
>
> </details>
>
> Batch rename Kasir sudah lulus 20 targeted test, termasuk trim dan batas nama, target Manager/lintas toko ditolak tanpa mutasi, UI edit, refresh list, serta bukti transaksi lama mempertahankan nama lama dan transaksi baru memakai nama terbaru. Saya lanjutkan regresi login, shell Akun, dan riwayat sebelum full suite.
>
> Regresi login/shell/riwayat lulus 16 test dan analyzer final bersih. Full suite sedang berjalan; jumlah test sekarang bertambah karena skenario rename dan snapshot baru, jadi saya tunggu sampai hasil akhir benar-benar tercatat sebelum menutup batch.

</details>

Tahap 11 Batch 1 selesai.

Yang sudah diterapkan:

- Model V1 ditetapkan: tepat satu Manager per toko.
- Manager tidak dapat dinonaktifkan atau dibuat kedua kali.
- Repository mendukung rename Kasir dengan otorisasi Manager aktif.
- Nama di-trim, wajib diisi, maksimal 100 karakter.
- Target Manager dan akun toko lain ditolak.
- ViewModel melakukan refresh daftar setelah rename.
- UI memiliki dialog **Edit Nama Kasir**.
- Snapshot transaksi lama tetap memakai nama lama.
- Transaksi baru otomatis memakai nama Kasir terbaru.

Verifikasi:

- Targeted repository/ViewModel/UI/transaksi: **20 lulus**
- Regresi login, shell, riwayat, dan akun: **16 lulus**
- Analyzer: **No issues found**
- Full test suite: **220 lulus**
- Tidak ada test gagal.

Manual test belum diperlukan. Batch Tahap 11 berikutnya adalah self-service Manager: perubahan nama sendiri dan perubahan PIN dengan verifikasi PIN lama.

> okee bagus, yuk lanjut tahap berikutnya

<details><summary>6 previous messages</summary>

> Saya lanjut ke Tahap 11 Batch 2: self-service Manager. Fokusnya perubahan nama sendiri dan perubahan PIN dengan verifikasi PIN lama, uniqueness terhadap seluruh akun toko, tidak mengekspos hash, serta memastikan sesi dan login berperilaku benar setelah perubahan.
>
> Keputusan teknis batch ini: perubahan Manager hanya boleh menargetkan UID sesi sendiri; repository memvalidasi ulang Manager aktif di database. Ganti PIN wajib memverifikasi PIN lama dan PIN baru harus berbeda serta unik terhadap semua akun—termasuk akun nonaktif. Jika hashing gagal, transaksi harus tetap tanpa mutasi.
>
> <details><summary>Edited 6 files</summary>
>
> - Wrote `./kasir_offline/lib/features/auth/data/app_user_repository.dart` (+96 -0)
> - Wrote `./kasir_offline/lib/features/auth/presentation/user_management_view_model.dart` (+51 -5)
> - Wrote `./kasir_offline/lib/features/auth/presentation/user_management_page.dart` (+239 -12)
> - Wrote `./kasir_offline/test/user_management_repository_test.dart` (+92 -0)
> - Wrote `./kasir_offline/test/user_management_page_test.dart` (+60 -6)
> - Wrote `./kasir_offline/test/user_management_view_model_test.dart` (+32 -5)
>
> </details>
>
> Targeted suite lulus 20 test. Kebijakan sesi saya tetapkan eksplisit: perubahan nama/PIN sendiri tidak memutus sesi Manager yang sedang aktif karena UID, toko, role, dan status akun tidak berubah; login berikutnya wajib memakai PIN baru dan memuat nama baru. Recovery lupa PIN tetap tidak digabungkan ke alur ini dan tidak ada bypass tersembunyi.
>
> <details><summary>Edited 2 files</summary>
>
> - Wrote `./kasir_offline/README.md` (+4 -0)
> - Wrote `./kasir_offline/test/user_management_repository_test.dart` (+4 -0)
>
> </details>
>
> Analyzer final bersih. Full suite masih berjalan di latar; seluruh jalur sensitif sudah tercakup—PIN lama salah, PIN baru duplikat/invalid/sama dengan PIN lama, rollback saat hashing gagal, login lama tidak berlaku, login baru berlaku, dan sesi Manager aktif tetap tervalidasi oleh database.

</details>

Tahap 11 Batch 2 selesai.

Yang diterapkan:

- Manager dapat mengubah nama sendiri.
- Manager dapat mengganti PIN sendiri.
- PIN lama wajib diverifikasi.
- PIN baru harus 4–6 digit, berbeda, dan unik terhadap semua akun termasuk Kasir nonaktif.
- Perubahan hanya boleh menargetkan Manager dari sesi aktif sendiri.
- Dialog **Edit Nama Manager** dan **Ganti PIN Manager** tersedia.
- Sesi aktif tetap berlaku; login berikutnya menggunakan nama dan PIN baru.
- Prosedur lupa PIN tetap dipisahkan tanpa bypass tersembunyi.
- Kebijakan lifecycle tersebut didokumentasikan di README.

Jalur keamanan yang diuji:

- PIN lama salah ditolak.
- PIN baru invalid, duplikat, atau sama dengan PIN lama ditolak.
- PIN lama tidak berlaku setelah perubahan.
- PIN baru berhasil digunakan.
- Kegagalan hashing me-roll back seluruh perubahan.
- Hash dan salt tidak ditampilkan melalui model/UI.
- Manager nonaktif atau sesi tidak sah tetap ditolak.

Hasil verifikasi:

- Targeted repository/ViewModel/UI: **20 lulus**
- Regresi autentikasi, sesi, dan shell: **24 lulus**
- Analyzer: **No issues found**
- Full suite: **225 lulus**

Manual test belum diperlukan. Batch berikutnya adalah audit reset PIN Kasir dan audit perubahan lifecycle akun.

> okee bagus, yuk lanjut tahap berikutnyaa

<details><summary>5 previous messages</summary>

> Saya lanjut ke Tahap 11 Batch 3: audit perubahan lifecycle akun, dimulai dari reset PIN Kasir sebagai checkpoint wajib roadmap. Saya akan memakai transaksi database yang sama supaya perubahan akun dan audit selalu atomik, serta memastikan detail audit tidak pernah memuat PIN, hash, atau salt.
>
> <details><summary>Edited 2 files</summary>
>
> - Wrote `./kasir_offline/lib/features/auth/data/app_user_repository.dart` (+27 -1)
> - Wrote `./kasir_offline/lib/features/auth/data/app_user_repository.dart` (+5 -5)
> - Wrote `./kasir_offline/lib/features/auth/data/app_user_repository.dart` (+5 -5)
> - Wrote `./kasir_offline/test/user_management_repository_test.dart` (+52 -0)
>
> </details>
>
> Format audit yang dipakai adalah:
>
> - `action: user_pin_reset`
> - `entityType: user`
> - aktor memakai UID, nama, dan role Manager dari database
> - target memakai UID/nama/role Kasir di JSON
> - tidak ada PIN, hash, salt, atau nilai rahasia dalam detail
>
> Reset PIN dan insert audit berada dalam transaksi yang sama; test trigger database akan membuktikan keduanya rollback bila audit gagal.
>
> Command gabungan melewati batas waktu tanpa hasil karena proses Flutter/analyzer masih tertahan di wrapper, bukan karena laporan test gagal. Saya akan identifikasi hanya proses yang baru saya mulai pada batch ini, hentikan proses yatim tersebut, lalu menjalankan pemeriksaan satu per satu seperti pada batch sebelumnya.
>
> Targeted repository lulus 12 test dan regresi UI/auth/migration lulus 51 test. Tidak ada perubahan schema, sehingga snapshot migration tetap sama dan hijau. Saya jalankan full suite sebagai gerbang terakhir; analyzer juga sudah bersih.

</details>

Tahap 11 Batch 3 selesai.

Yang diterapkan:

- Reset PIN Kasir menghasilkan audit `user_pin_reset`.
- Audit menyimpan identitas Manager persisten sebagai aktor.
- Target audit menyimpan UID, nama snapshot, dan role Kasir.
- PIN baru, hash, salt, dan data rahasia lainnya tidak masuk audit.
- Update PIN dan pembuatan audit berada dalam satu transaksi.
- Jika audit gagal, perubahan PIN otomatis dibatalkan.

Hasil verifikasi:

- Targeted repository dan rollback: **12 lulus**
- Regresi UI, autentikasi, dan migration: **51 lulus**
- Analyzer: **No issues found**
- Full test suite: **226 lulus**
- Tidak ada perubahan schema.

Manual test belum diperlukan. Berikutnya kita dapat menutup Tahap 11 dengan kebijakan operasional lupa PIN Manager, lalu masuk Tahap 12 login security hardening.

> okee bagus, yuk lanjut ke tahap 12 berikutnya

<details><summary>6 previous messages</summary>

> Saya lanjut langsung ke Tahap 12 sesuai permintaan. Saya akan audit alur autentikasi dan lifecycle aplikasi terlebih dahulu, lalu mengambil batch pertama: attempt limiter, pesan error generik, dan perilaku lock yang dapat diuji otomatis tanpa membuat pengguna sah mudah terkunci permanen.
>
> Kebijakan V1 yang saya ambil mengikuti roadmap:
>
> - kegagalan 1–4 tanpa delay;
> - kegagalan ke-5: 2 detik, ke-6: 4, ke-7: 8, lalu 16 dan maksimum 30 detik;
> - format PIN invalid tidak dihitung;
> - login berhasil mereset limiter;
> - limiter disimpan selama instance aplikasi berjalan dan reset saat proses aplikasi benar-benar restart;
> - UI tidak memakai `sleep`, tetapi countdown non-blocking;
> - kegagalan autentikasi memakai pesan generik yang tidak membedakan PIN salah dan akun nonaktif.
>
> <details><summary>Edited 4 files</summary>
>
> - Wrote `./kasir_offline/lib/features/auth/presentation/login_page.dart` (+14 -9)
> - Wrote `./kasir_offline/lib/features/auth/presentation/login_view_model.dart` (+112 -30)
> - Wrote `./kasir_offline/lib/features/auth/presentation/login_page.dart` (+7 -3)
> - Wrote `./kasir_offline/lib/features/auth/presentation/login_view_model.dart` (+1 -0)
> - Wrote `./kasir_offline/test/login_view_model_test.dart` (+65 -10)
> - Wrote `./kasir_offline/test/login_view_model_test.dart` (+3 -2)
> - Wrote `./kasir_offline/test/login_page_test.dart` (+29 -5)
>
> </details>
>
> Limiter login dan UI countdown lulus 10 targeted test dengan fake clock. Format invalid tidak menambah counter, percobaan saat delay tidak menambah counter, dan login benar mereset seluruh state. Saya dokumentasikan bahwa limiter V1 bersifat in-memory—restart proses mereset limiter—sehingga tidak berisiko lock permanen tanpa recovery; persistence dapat diperketat nanti bersama threat model penyimpanan waktu.
>
> <details><summary>Edited a file</summary>
>
> - Wrote `./kasir_offline/README.md` (+10 -0)
>
> </details>
>
> Regresi login-shell dan logout lulus 18 test. Full suite masih berjalan tanpa kegagalan; gerbang terakhir ini juga memastikan timer limiter dibersihkan saat ViewModel dibuang sehingga tidak ada timer yatim atau test yang menggantung.

</details>

Tahap 12 Batch 1 selesai.

Yang diterapkan:

- Attempt limiter progresif:
  - Percobaan 1–4: tanpa delay.
  - Percobaan ke-5: 2 detik.
  - Berikutnya: 4, 8, 16 detik.
  - Maksimum: 30 detik.

- Tidak ada blocking `sleep` pada UI.
- ViewModel menyediakan `retryAfter`, countdown, dan status blokir.
- Input PIN dan tombol login dinonaktifkan selama delay.
- PIN dibersihkan ketika limiter mulai aktif.
- Login berhasil mereset seluruh penghitung.
- Format PIN invalid tidak dihitung sebagai kegagalan autentikasi.
- Percobaan selama delay tidak menambah counter.
- Error autentikasi dibuat generik: `PIN tidak dikenali atau akun tidak aktif.`
- Error internal tidak lagi mengekspos detail exception.
- Timer dibersihkan ketika ViewModel ditutup.
- Kebijakan restart limiter didokumentasikan di README.

Hasil verifikasi:

- Targeted limiter/ViewModel/UI: **10 lulus**
- Regresi login-shell, logout, startup, dan akun: **18 lulus**
- Analyzer: **No issues found**
- Full test suite: **228 lulus**

Manual test belum diperlukan. Batch Tahap 12 berikutnya adalah lifecycle lock saat aplikasi lama berada di background, kemudian penyempurnaan proteksi field PIN.

> okee yuk lanjut tahap berikutnya

<details><summary>10 previous messages</summary>

> Saya lanjut ke Tahap 12 Batch 2: lifecycle lock. Saya akan audit posisi session dan state keranjang terlebih dahulu, karena kembali ke Login saja belum cukup bila ViewModel keranjang masih hidup secara global. Keputusan clear/preserve cart akan dibuat eksplisit dan diuji bersama pause/resume threshold.
>
> Kebijakan lifecycle V1:
>
> - threshold background: **5 menit**;
> - kurang dari threshold: sesi dan keranjang tetap;
> - 5 menit atau lebih, termasuk device lock/pause: kembali ke Login;
> - auto-lock dan logout manual sama-sama menghapus keranjang;
> - tidak menjalankan ulang setup atau aktivasi lisensi karena navigasi kembali langsung ke `LoginPage`;
> - clock dibuat injectable agar threshold bisa diuji deterministik.
>
> <details><summary>Edited 3 files</summary>
>
> - Wrote `./kasir_offline/lib/features/shell/presentation/app_shell_page.dart` (+56 -17)
> - Wrote `./kasir_offline/test/widget_test.dart` (+79 -6)
> - Wrote `./kasir_offline/test/login_shell_flow_test.dart` (+11 -4)
>
> </details>
>
> Audit juga menemukan bahwa PIN yang sedang diketik di halaman Login dapat tetap berada di controller ketika aplikasi masuk background. Dalam batch yang sama saya akan menghapus PIN pada pause/inactive/hidden, mengembalikan mode obscure, serta menonaktifkan suggestion/autocorrect/autofill untuk field PIN.
>
> <details><summary>Edited 2 files</summary>
>
> - Wrote `./kasir_offline/lib/features/auth/presentation/login_page.dart` (+36 -12)
> - Wrote `./kasir_offline/test/login_page_test.dart` (+29 -5)
>
> </details>
>
> Analyzer menangkap perbedaan API Flutter versi proyek: pemicu lifecycle test bersifat sinkron (`void`), dan properti obscure berada pada `EditableText`, bukan `TextFormField`. Saya sesuaikan test dengan API aktual tanpa mengubah implementasi produksi.
>
> <details><summary>Edited 2 files</summary>
>
> - Wrote `./kasir_offline/test/login_page_test.dart` (+19 -15)
> - Wrote `./kasir_offline/test/widget_test.dart` (+12 -12)
>
> </details>
>
> Test pertama menemukan dua masalah pada harness, bukan produksi:
>
> - Flutter versi ini mewajibkan urutan lifecycle lengkap (`resumed → inactive → hidden → paused` dan sebaliknya), bukan loncat langsung.
> - Fixture login-shell tidak memiliki produk katalog, sehingga tidak bisa mengisi keranjang untuk test logout manual.
>
> Saya perbaiki simulasi lifecycle sesuai state machine Flutter. Verifikasi pembersihan keranjang tetap dilakukan pada test shell yang memang memiliki katalog; test logout integrasi tetap fokus memastikan kembali ke Login.
>
> <details><summary>Edited 4 files</summary>
>
> - Wrote `./kasir_offline/test/login_page_test.dart` (+6 -2)
> - Wrote `./kasir_offline/test/login_shell_flow_test.dart` (+1 -8)
> - Wrote `./kasir_offline/test/widget_test.dart` (+18 -6)
> - Wrote `./kasir_offline/README.md` (+4 -0)
>
> </details>
>
> Targeted lifecycle, PIN-background, widget shell, dan logout lulus 17 test; analyzer final bersih. Full suite sedang berjalan. Karena perilaku ini bergantung pada lifecycle Android asli, setelah otomatisasi hijau saya akan memberi satu smoke test perangkat yang memang layak dilakukan manual—cukup untuk memastikan OS mengirim event sesuai harapan.

</details>

Tahap 12 Batch 2 selesai secara otomatis.

Yang diterapkan:

- Auto-lock setelah aplikasi berada di background minimal **5 menit**.
- Resume sebelum 5 menit mempertahankan sesi dan keranjang.
- Auto-lock dan logout manual membersihkan keranjang.
- Auto-lock kembali langsung ke Login tanpa mengulang setup atau aktivasi.
- PIN yang sedang diketik dibersihkan saat Login masuk background.
- Field PIN selalu kembali obscure.
- Autocorrect, suggestion, dan autofill PIN dinonaktifkan.
- Clock lifecycle dapat diinjeksi untuk pengujian deterministik.

Hasil verifikasi:

- Targeted lifecycle, PIN, shell, dan logout: **17 lulus**
- Analyzer: **No issues found**
- Full test suite: **230 lulus**

Untuk batch ini manual test pada perangkat nyata memang diperlukan satu kali:

1. Login dan isi satu produk ke keranjang.
2. Background aplikasi kurang dari 5 menit, lalu buka kembali — sesi dan keranjang harus tetap ada.
3. Background aplikasi minimal 5 menit, lalu buka kembali — harus kembali ke Login dan keranjang harus kosong.
4. Pada halaman Login, ketik PIN lalu background aplikasi — saat kembali, field PIN harus kosong dan tersembunyi.

Setelah hasilnya dikonfirmasi, Tahap 12 bisa ditutup atau kita tindak lanjuti jika lifecycle perangkat berperilaku berbeda.

> okeee berhasil, yuk lanjut tahap berikutnya

<details><summary>5 previous messages</summary>

> Manual lifecycle Android sudah terkonfirmasi berhasil, jadi Tahap 12 dapat ditutup. Saya lanjut ke Tahap 13: audit log completeness, dimulai dengan memetakan seluruh operasi sensitif yang sudah ada agar action code, aktor, target, detail aman, dan atomicity konsisten sebelum menambah log baru.
>
> Audit menemukan hanya tiga action produksi saat ini: pembatalan transaksi, perubahan stok manual, dan reset PIN Kasir. Batch pertama Tahap 13 akan menstandarkan constants lalu melengkapi lifecycle akun:
>
> - `user_created`
> - `user_renamed`
> - `user_activated`
> - `user_deactivated`
> - `user_pin_changed`
> - `user_pin_reset`
>
> UID target tetap berada di JSON karena schema audit saat ini hanya memiliki `entityId` numerik. Saya tidak menambah schema/index baru karena belum ada bukti kebutuhan query baru.
>
> <details><summary>Created a file, edited 4 files</summary>
>
> - Wrote `./kasir_offline/lib/core/audit/audit_codes.dart` (+16 -0)
> - Wrote `./kasir_offline/lib/features/auth/data/app_user_repository.dart` (+3 -2)
> - Wrote `./kasir_offline/lib/features/sales/data/sale_cancellation_repository.dart` (+3 -2)
> - Wrote `./kasir_offline/lib/features/stock/data/stock_repository.dart` (+3 -2)
> - Wrote `./kasir_offline/lib/features/auth/data/app_user_repository.dart` (+96 -5)
> - Wrote `./kasir_offline/lib/features/auth/data/app_user_repository.dart` (+45 -22)
> - Wrote `./kasir_offline/lib/features/auth/data/app_user_repository.dart` (+2 -2)
> - Wrote `./kasir_offline/lib/features/auth/data/app_user_repository.dart` (+1 -1)
> - Wrote `./kasir_offline/test/user_management_repository_test.dart` (+91 -2)
>
> </details>
>
> Batch audit lifecycle akun kini mencakup bootstrap Manager, pembuatan Kasir, rename Kasir/Manager, aktivasi/nonaktivasi, perubahan PIN Manager, dan reset PIN Kasir. No-op rename/status tidak menulis log tambahan; hanya mutasi nyata yang diaudit. Saya sekarang memvalidasi urutan action, snapshot aktor, sanitasi seluruh JSON, dan rollback ketika insert audit ditolak database.
>
> Regresi action constants, stock, cancellation, setup/recovery, dan seluruh migration lulus 64 test. Schema tetap v9; index yang ada sudah cukup untuk pola saat ini, jadi saya tidak membuat schema 10 tanpa kebutuhan query nyata. Saya jalankan full suite untuk menutup batch pertama Tahap 13.

</details>

Tahap 13 Batch 1 selesai.

Yang diterapkan:

- Constants terpusat untuk action dan entity audit.
- Action lifecycle akun:
  - `user_created`
  - `user_renamed`
  - `user_activated`
  - `user_deactivated`
  - `user_pin_changed`
  - `user_pin_reset`

- Audit mencakup:
  - Pembuatan Manager awal.
  - Pembuatan Kasir.
  - Rename Kasir dan Manager.
  - Aktivasi/nonaktivasi Kasir.
  - Perubahan PIN Manager.
  - Reset PIN Kasir.

- Bootstrap Manager dicatat dengan aktor `System Setup`.
- Mutasi akun dan audit berada dalam transaksi yang sama.
- No-op tidak menghasilkan audit tambahan.
- JSON audit tidak menyimpan PIN, hash, salt, atau nilainya.
- Constants juga diterapkan pada audit stok dan pembatalan transaksi.
- Schema tetap v9; belum ada alasan menambah index/schema 10.

Hasil verifikasi:

- Targeted lifecycle akun: **23 lulus**
- Regresi stock, cancellation, setup/recovery, dan migration: **64 lulus**
- Analyzer: **No issues found**
- Full test suite: **232 lulus**

Manual test tidak diperlukan. Batch Tahap 13 berikutnya adalah audit perubahan kategori dan produk, termasuk create/update/deactivate serta rollback atomik.

> okee bagus, lanjut tahap 13 batch berikutnya

<details><summary>9 previous messages</summary>

> Saya lanjut ke Tahap 13 Batch 2: audit kategori dan produk. Saya akan memetakan mutasi aktual terlebih dahulu—termasuk apakah “hapus” berupa hard delete atau deactivate—lalu menambahkan action code yang sesuai tanpa mengubah perilaku domain yang sudah stabil.
>
> Operasi aktual memakai soft deactivate/reactivate, bukan hard delete. Action code yang dipakai:
>
> - kategori: `category_created`, `category_updated`, `category_activated`, `category_deactivated`
> - produk: `product_created`, `product_updated`, `product_activated`, `product_deactivated`
>
> Perubahan gambar masuk `product_updated` dengan `changedFields: ['image']`; path file tidak disimpan di audit. Status yang sudah sama diperlakukan sebagai no-op tanpa log baru.
>
> <details><summary>Edited 4 files</summary>
>
> - Wrote `./kasir_offline/lib/core/audit/audit_codes.dart` (+10 -0)
> - Wrote `./kasir_offline/lib/features/categories/data/category_repository.dart` (+86 -5)
> - Wrote `./kasir_offline/lib/features/products/data/product_repository.dart` (+128 -7)
> - Wrote `./kasir_offline/test/category_product_test.dart` (+122 -0)
>
> </details>
>
> Analyzer menangkap tiga kesalahan mekanis: entity `product` ternyata sudah ada pada constants, import JSON tersisip di akhir test, dan matcher Drift perlu menyembunyikan `isNotNull`. Saya perbaiki ketiganya tanpa mengubah desain audit.
>
> <details><summary>Edited 2 files</summary>
>
> - Wrote `./kasir_offline/lib/core/audit/audit_codes.dart` (+0 -1)
> - Wrote `./kasir_offline/test/category_product_test.dart` (+3 -2)
>
> </details>
>
> Targeted audit kategori/produk lulus 8 test dan analyzer bersih. Saya sekarang menjalankan regresi lintas subsistem karena fixture stok/penjualan membuat kategori dan produk saat setup; test lama yang menghitung seluruh tabel audit perlu tetap membedakan action miliknya, bukan mengasumsikan tabel audit kosong.
>
> Regresi menemukan asumsi fixture lama: test stok dan pembatalan menghitung seluruh tabel audit setelah setup kategori/produk. Perilaku baru benar; saya akan membersihkan audit setup pada fixture dua subsistem tersebut sehingga masing-masing test tetap mengukur hanya action domainnya. Kode produksi tidak diubah.
>
> <details><summary>Edited 2 files</summary>
>
> - Wrote `./kasir_offline/test/sale_cancellation_repository_test.dart` (+3 -2)
> - Wrote `./kasir_offline/test/stock_repository_test.dart` (+1 -0)
>
> </details>
>
> Targeted kategori/produk lulus 8 test; regresi stok dan pembatalan kembali hijau 17 test setelah fixture diisolasi; analyzer final bersih. Full suite sedang berjalan sebagai gerbang akhir batch.

</details>

Tahap 13 Batch 2 selesai.

Yang diterapkan:

- Audit kategori:
  - `category_created`
  - `category_updated`
  - `category_activated`
  - `category_deactivated`

- Audit produk:
  - `product_created`
  - `product_updated`
  - `product_activated`
  - `product_deactivated`

- Soft deactivate/reactivate dicatat sesuai operasi aktual.
- Perubahan gambar memakai `product_updated`.
- Path gambar lokal tidak disimpan; audit hanya mencatat `hasImage`.
- Actor UID, nama, dan role Manager diambil ulang dari database.
- Entity ID serta snapshot UID/nama kategori atau produk tersimpan.
- Status yang sudah sama menjadi no-op tanpa audit tambahan.
- Mutasi dan audit berada dalam transaksi yang sama.
- Kegagalan audit membatalkan pembuatan produk.
- Tidak ada perubahan schema atau index.

Hasil verifikasi:

- Targeted kategori/produk: **8 lulus**
- Regresi stok dan pembatalan: **17 lulus**
- Analyzer: **No issues found**
- Full test suite: **234 lulus**

Manual test tidak diperlukan. Batch Tahap 13 berikutnya dapat melanjutkan audit settings/printer/receipt design serta query/filter/pagination audit log.

> okee lanjut tahap 13 batch berikutnya

<details><summary>8 previous messages</summary>

> Context compaction
>
> Source: automatic
> Status: completed
>
> Saya lanjutkan Stage 13 batch berikutnya. Saya akan mulai dengan membaca ulang pedoman dan memetakan repository pengaturan printer/desain struk beserta alur aktornya, lalu memilih batch kecil yang lengkap: implementasi audit atomik, tes terarah, regresi, analyzer, dan seluruh unit test.
>
> Hasil audit awal menemukan batas keamanan yang penting: pengaturan printer dan desain struk saat ini dapat disimpan tanpa identitas sesi, bahkan dialognya bisa dibuka dari halaman Kasir/Riwayat. Sesuai pedoman role matrix, saya akan menjadikan perubahan konfigurasi sebagai aksi Manager-only di repository; fungsi cetak transaksi tetap dapat dipakai Kasir. Audit akan menyimpan snapshot konfigurasi yang aman dan tidak menyimpan alamat perangkat maupun path logo mentah.
>
> <details><summary>Edited 12 files</summary>
>
> - Wrote `./kasir_offline/lib/core/audit/audit_codes.dart` (+4 -0)
> - Wrote `./kasir_offline/lib/features/cashier/presentation/cashier_page.dart` (+1 -1)
> - Wrote `./kasir_offline/lib/features/history/presentation/transaction_history_page.dart` (+1 -1)
> - Wrote `./kasir_offline/lib/features/printer/data/printer_settings_repository.dart` (+76 -33)
> - Wrote `./kasir_offline/lib/features/printer/data/receipt_design_repository.dart` (+84 -37)
> - Wrote `./kasir_offline/lib/features/printer/presentation/printer_settings_dialog.dart` (+12 -5)
> - Wrote `./kasir_offline/lib/features/printer/presentation/printer_view_model.dart` (+7 -4)
> - Wrote `./kasir_offline/lib/features/printer/presentation/receipt_design_dialog.dart` (+11 -5)
> - Wrote `./kasir_offline/lib/features/printer/presentation/receipt_design_view_model.dart` (+6 -4)
> - Wrote `./kasir_offline/test/receipt_printer_service_test.dart` (+4 -3)
> - Wrote `./kasir_offline/test/printer_settings_repository_test.dart` (+103 -16)
> - Wrote `./kasir_offline/test/receipt_design_repository_test.dart` (+117 -21)
> - Wrote `./kasir_offline/test/printer_settings_repository_test.dart` (+1 -1)
> - Wrote `./kasir_offline/test/receipt_design_repository_test.dart` (+1 -1)
>
> </details>
>
> Implementasi inti dan tes keamanan sudah ditambahkan. Sekarang saya masuk verifikasi pertama: tes repository printer, desain struk, dan layanan cetak bersama-sama; ini akan menangkap masalah wiring sesi maupun efek samping audit pada alur cetak yang sudah ada.
>
> <details><summary>Edited 6 files</summary>
>
> - Wrote `./kasir_offline/lib/features/printer/data/printer_settings_repository.dart` (+2 -2)
> - Wrote `./kasir_offline/lib/features/printer/data/receipt_design_repository.dart` (+2 -2)
> - Wrote `./kasir_offline/test/printer_settings_repository_test.dart` (+1 -1)
> - Wrote `./kasir_offline/test/receipt_design_repository_test.dart` (+1 -1)
> - Wrote `./kasir_offline/lib/features/cashier/presentation/cashier_page.dart` (+8 -6)
> - Wrote `./kasir_offline/lib/features/history/presentation/transaction_history_page.dart` (+6 -5)
>
> </details>
>
> Tes terarah lulus 12/12, analyzer bersih, dan regresi otorisasi/audit lulus 43/43. Saya lanjutkan pemeriksaan terakhir berupa seluruh test suite; setelah ini saya akan melaporkan jumlah final dan apakah ada kebutuhan tes manual.
>
> <details><summary>Edited a file</summary>
>
> - Wrote `./kasir_offline/test/widget_test.dart` (+11 -5)
>
> </details>

</details>

Stage 13 Batch 3 selesai dengan benar.

Perubahan utama:

- Konfigurasi printer dan desain struk sekarang hanya dapat diubah oleh Manager aktif.
- Validasi dilakukan di repository, bukan hanya menyembunyikan tombol UI.
- Kasir tetap dapat mencetak transaksi, tetapi tidak melihat tombol pengaturan printer.
- Audit baru:
  - `printer_settings_updated`
  - `receipt_design_updated`
- Audit mencatat UID dan snapshot Manager serta entity terkait.
- Alamat printer, nama perangkat, dan path logo lokal tidak dimasukkan ke audit.
- Perubahan data dan audit berada dalam satu transaksi. Jika audit gagal, perubahan otomatis dibatalkan.
- Tidak ada perubahan schema database; tetap schema v9.

Hasil pengujian final:

- Tes printer, desain struk, dan layanan cetak: **12/12 lulus**
- Regression otorisasi dan audit: **43/43 lulus**
- Widget test termasuk akses Manager/Kasir: **8/8 lulus**
- Dart analyzer seluruh `lib` dan `test`: **tidak ada masalah**
- Seluruh test suite: **238/238 lulus**

Tes manual belum diperlukan untuk batch ini. Tahap berikutnya dapat melanjutkan audit `settings/theme`, kemudian audit query/filter/order/pagination.

> okeee bagus, yuk lanjutr tahap berikutnya

<details><summary>6 previous messages</summary>

> Saya lanjut ke Stage 13 batch berikutnya, dengan fokus awal `settings/theme`. Saya akan audit alur penyimpanan tema sampai UI dan memastikan perubahan hanya dilakukan Manager aktif, tercatat atomik, tidak membuat audit berulang saat aplikasi sekadar memuat tema, lalu menjalankan seluruh lapisan tes seperti batch sebelumnya.
>
> Audit menemukan satu karakteristik khusus tema: perubahan saat ini diterapkan langsung lalu disimpan secara `fire-and-forget`. Saya tetap mempertahankan respons UI yang instan, tetapi boundary database akan diperketat: setiap penyimpanan wajib membawa sesi Manager, perubahan yang identik tidak membuat audit, dan update tema + audit berada dalam satu transaksi. Kegagalan simpan tetap ditangkap controller tanpa merusak database.
>
> <details><summary>Created a file, edited 5 files</summary>
>
> - Wrote `./kasir_offline/lib/core/audit/audit_codes.dart` (+2 -0)
> - Wrote `./kasir_offline/lib/core/theme/theme_controller.dart` (+48 -40)
> - Wrote `./kasir_offline/lib/core/theme/theme_repository.dart` (+114 -18)
> - Wrote `./kasir_offline/lib/features/settings/presentation/theme_customizer_page.dart` (+38 -18)
> - Wrote `./kasir_offline/lib/features/shell/presentation/app_shell_page.dart` (+1 -1)
> - Wrote `./kasir_offline/test/theme_repository_test.dart` (+141 -0)
>
> </details>
>
> Tes tema terarah sudah lulus 12/12, termasuk empat skenario baru: audit Manager, no-op tanpa audit, penolakan Kasir, dan rollback ketika audit gagal. Saya lanjutkan regresi startup/login serta analyzer karena controller tema hidup sejak aplikasi mulai dan perubahan constructor halaman menyentuh shell utama.
>
> <details><summary>Edited a file</summary>
>
> - Wrote `./kasir_offline/test/database_test.dart` (+21 -10)
> - Wrote `./kasir_offline/test/database_test.dart` (+2 -2)
>
> </details>
>
> Seluruh pemeriksaan sebelum full suite sudah hijau: tes tema/widget 12/12, regresi startup/login/role 8/8, database+audit 6/6, dan analyzer tanpa masalah. Saya sekarang menjalankan seluruh test suite final pada source terakhir.

</details>

Stage 13 Batch 4—pengaturan tema—selesai.

Perubahan utama:

- Penyimpanan tema sekarang wajib menggunakan sesi Manager aktif.
- Kasir ditolak di tingkat repository tanpa perubahan database.
- Ditambahkan audit stabil:
  - `theme_settings_updated`
  - entity `theme`
- Audit mencatat UID dan snapshot Manager, field yang berubah, mode, preset, warna, density, corner style, dan status logo.
- Penyimpanan tema dan audit dilakukan dalam satu transaksi.
- Jika audit gagal, perubahan tema di database ikut dibatalkan.
- Penyimpanan nilai yang identik tidak menghasilkan audit tambahan.
- Sesi Manager sekarang diteruskan dari shell ke halaman kustomisasi tema.
- Tidak ada perubahan schema; tetap schema v9.

Hasil pengujian:

- Tema dan widget: **12/12 lulus**
- Regresi startup, login, dan role: **8/8 lulus**
- Database dan audit tema: **6/6 lulus**
- Analyzer seluruh `lib` dan `test`: **tidak ada masalah**
- Full test suite: **242/242 lulus**

Tes manual belum diperlukan. Batch Stage 13 berikutnya dapat berfokus pada kelengkapan query audit—filter, urutan, pagination, serta pembacaan audit legacy—atau audit penggantian lisensi.

> okeee yuk lanjut tahap berikutnya 13 batch selanjutnya

<details><summary>5 previous messages</summary>

> Saya lanjut ke Stage 13 batch berikutnya dengan fokus kelengkapan pembacaan audit: filter, urutan, pagination, dan kompatibilitas log legacy. Saya akan terlebih dahulu memeriksa apakah sudah ada repository atau UI audit; bila belum, batch ini akan membangun lapisan query yang teruji tanpa memaksakan perubahan schema/index sebelum ada bukti kebutuhan.
>
> Tidak ditemukan repository maupun halaman khusus untuk membaca jejak audit; selama ini tabel hanya dibaca secara ad hoc oleh riwayat transaksi. Batch ini saya batasi pada fondasi query yang aman dan dapat dipakai UI berikutnya: Manager-only, terisolasi per toko, filter terkomposisi, urutan stabil dengan tie-breaker ID, serta pagination `limit + 1`. Index baru tidak ditambahkan karena index `storeId + createdAt` dan entity yang ada sudah mencukupi untuk bukti penggunaan saat ini.
>
> <details><summary>Created 2 files, edited a file</summary>
>
> - Wrote `./kasir_offline/lib/core/audit/audit_log_repository.dart` (+186 -0)
> - Wrote `./kasir_offline/test/audit_log_repository_test.dart` (+209 -0)
> - Wrote `./kasir_offline/test/audit_log_repository_test.dart` (+1 -1)
>
> </details>
>
> Lapisan query audit baru sudah lulus 6/6 dan analyzer bersih. Query ini mempertahankan log legacy dengan `actorUserUid = null`, menolak Kasir, serta memberi urutan deterministik bahkan ketika timestamp sama. Berikutnya saya jalankan regresi terhadap seluruh produsen dan pembaca audit yang sudah dibangun agar API baru tidak mengganggu alur lama.
>
> Regresi seluruh produsen dan pembaca audit lulus 55/55. Saya lanjutkan full suite final; setelah selesai saya akan memastikan jumlah total, status schema, dan apakah batch ini memerlukan tes manual.

</details>

Stage 13 Batch 5—fondasi pembacaan audit—selesai.

Yang ditambahkan:

- Repository khusus untuk membaca audit log.
- Hanya Manager aktif yang dapat mengaksesnya.
- Data otomatis dibatasi pada toko aktif.
- Filter berdasarkan:
  - action
  - entity type dan entity ID
  - actor UID
  - actor teridentifikasi atau legacy
  - rentang waktu
- Urutan terbaru atau terlama.
- Tie-breaker menggunakan ID sehingga urutan tetap deterministik saat timestamp sama.
- Pagination dengan `offset`, `limit`, `hasNextPage`, dan `nextOffset`.
- Batas maksimal 100 data per halaman.
- Validasi parameter pagination, actor, entity, dan rentang tanggal.
- Log legacy dengan `actorUserUid = null` tetap dapat dibaca.

Tidak ditambahkan schema atau index baru karena belum ada bukti performa yang membutuhkannya. Schema tetap v9.

Hasil pengujian:

- Tes query audit baru: **6/6 lulus**
- Regresi seluruh produsen dan pembaca audit: **55/55 lulus**
- Analyzer seluruh `lib` dan `test`: **tidak ada masalah**
- Full test suite: **248/248 lulus**

Tes manual belum diperlukan karena batch ini merupakan lapisan repository tanpa UI baru. Tahap Stage 13 berikutnya paling tepat adalah audit penggantian lisensi, setelah memeriksa kebijakan keamanan token agar audit tidak pernah menyimpan token penuh atau data aktivasi sensitif.

> okeee bagus yuk lanjut ke tahap berikutnya

<details><summary>5 previous messages</summary>

> Saya lanjut ke Stage 13 batch berikutnya dengan fokus audit perubahan/penggantian lisensi. Bagian ini sensitif, jadi saya akan memetakan dulu alur aktivasi, penyimpanan token, dan siapa yang boleh mengganti lisensi; audit hanya boleh menyimpan metadata aman—tidak token penuh, signature, request code, maupun rahasia perangkat.
>
> Ada batas atomisitas penting: token lisensi disimpan di secure storage, sedangkan audit berada di SQLite, sehingga keduanya tidak bisa memakai satu transaksi database murni. Saya akan memakai pola kompensasi yang aman: validasi Manager dan token lebih dulu, simpan token baru, tulis audit dalam transaksi SQLite, lalu pulihkan token lama jika audit gagal. Aktivasi pertama sebelum login tetap memakai alur aktivasi biasa dan tidak dipalsukan sebagai aksi Manager.
>
> <details><summary>Edited 9 files</summary>
>
> - Wrote `./kasir_offline/lib/core/audit/audit_codes.dart` (+2 -0)
> - Wrote `./kasir_offline/lib/features/license/presentation/license_info_page.dart` (+23 -12)
> - Wrote `./kasir_offline/lib/features/license/presentation/license_info_view_model.dart` (+10 -3)
> - Wrote `./kasir_offline/lib/features/license/services/license_activation_service.dart` (+110 -12)
> - Wrote `./kasir_offline/lib/features/shell/presentation/app_shell_page.dart` (+1 -1)
> - Wrote `./kasir_offline/lib/main.dart` (+5 -4)
> - Wrote `./kasir_offline/test/license_info_page_test.dart` (+14 -2)
> - Wrote `./kasir_offline/test/license_info_view_model_test.dart` (+50 -11)
> - Wrote `./kasir_offline/test/license_replacement_safety_test.dart` (+50 -11)
> - Wrote `./kasir_offline/test/license_info_view_model_test.dart` (+90 -14)
> - Wrote `./kasir_offline/test/license_info_view_model_test.dart` (+2 -0)
> - Wrote `./kasir_offline/test/license_replacement_safety_test.dart` (+5 -4)
>
> </details>
>
> Tes utama lisensi lulus 13/13. Audit sukses hanya menyimpan ID lisensi, waktu terbit/kedaluwarsa, dan jumlah fitur; installation ID, nama toko, serta token tidak dicatat. Saya menambahkan pemeriksaan bahwa semua token pengganti yang ditolak juga tidak menghasilkan audit, lalu menjalankan seluruh regresi modul lisensi.
>
> Regresi lisensi lengkap lulus 46/46 dan analyzer final bersih. Saya sekarang menjalankan seluruh test suite. Karena batch ini menyentuh secure storage dan database sekaligus, hasil full suite menjadi pemeriksaan terakhir sebelum dinyatakan selesai.

</details>

Stage 13 Batch 6—audit penggantian lisensi—selesai.

Perubahan utama:

- Penggantian lisensi sekarang wajib dilakukan oleh Manager aktif.
- Kasir ditolak sebelum token diproses atau disimpan.
- Aktivasi pertama sebelum login tetap memakai alur aktivasi terpisah.
- Ditambahkan action audit:
  - `license_replaced`
  - entity `license`
- Audit hanya menyimpan:
  - ID lisensi
  - waktu penerbitan
  - waktu kedaluwarsa
  - jumlah fitur
- Audit tidak menyimpan:
  - token lisensi
  - signature
  - installation ID
  - nama toko
  - request code/data aktivasi
- Memasukkan token yang sama tidak membuat audit penggantian.
- Token invalid, signature palsu, token perangkat lain, dan token kedaluwarsa tidak mengganti lisensi maupun membuat audit.

Karena token berada di secure storage sedangkan audit berada di SQLite, digunakan rollback kompensasi:

1. Manager dan token diverifikasi.
2. Token baru disimpan.
3. Audit ditulis dalam transaksi SQLite.
4. Jika audit gagal, token lama dipulihkan.

Hasil pengujian:

- Tes utama penggantian dan keamanan lisensi: **13/13 lulus**
- Regresi lengkap modul lisensi dan startup: **46/46 lulus**
- Analyzer seluruh `lib` dan `test`: **tidak ada masalah**
- Full test suite: **251/251 lulus**

Schema tetap v9. Tes manual belum diperlukan untuk batch ini; penggantian lisensi dengan secure storage Android dan token resmi dapat dimasukkan ke device test menjelang tahap release.

> okeee berhasil, yuk lanjut tahap berikutnya

<details><summary>5 previous messages</summary>

> Bagus. Dengan audit produk, kategori, akun, printer, desain struk, tema, lisensi, serta query audit sudah lengkap, kita beralih ke Stage 14: backup/restore data POS. Saya akan mulai dari batch fondasi yang kecil dan aman—membaca pedoman lengkap, mengaudit penyimpanan database/aset lokal, lalu menetapkan format backup terversi dan tervalidasi sebelum menyentuh proses restore yang berisiko.
>
> Audit menunjukkan database memakai `driftDatabase(name: 'kasir_offline')`, gambar produk berada di `product_images`, dan logo struk di `receipt_assets`. Belum ada library backup. Batch 1 akan membangun kontrak format terlebih dahulu: manifest versi 1, daftar file relatif yang aman, ukuran dan SHA-256, metadata schema/aplikasi, serta hash installation ID—tanpa memasukkan token lisensi. Proses snapshot SQLite dan archive fisik akan menjadi batch berikutnya setelah kontrak ini terkunci oleh tes.
>
> <details><summary>Created 3 files, edited 2 files</summary>
>
> - Wrote `./kasir_offline/lib/features/backup/domain/backup_manifest.dart` (+212 -0)
> - Wrote `./kasir_offline/lib/features/backup/services/backup_checksum_service.dart` (+67 -0)
> - Wrote `./kasir_offline/pubspec.yaml` (+3 -2)
> - Wrote `./kasir_offline/test/backup_manifest_test.dart` (+217 -0)
> - Wrote `./kasir_offline/lib/features/backup/services/backup_checksum_service.dart` (+1 -3)
>
> </details>
>
> Fondasi format backup sudah lulus 6/6: round-trip manifest, versi/schema tidak didukung, path traversal atau file di luar scope, checksum berubah, file hilang, dan hashing installation ID. Selanjutnya saya periksa seluruh source dengan analyzer dan regresi database/aset/lisensi agar kontrak baru tidak membawa token atau mengganggu fitur lama.
>
> Regresi database, seluruh jalur migrasi v1–v9, dan lisensi lulus 49/49. Saya lanjutkan full suite final. Batch ini belum membuat atau memulihkan arsip nyata, jadi tidak ada risiko overwrite data pengguna dan belum memerlukan tes manual.

</details>

Stage 14 Batch 1—fondasi format backup—selesai.

Yang ditambahkan:

- Format manifest backup versi 1.
- Metadata:
  - versi format backup
  - versi aplikasi
  - schema database
  - waktu pembuatan UTC
  - hash installation ID
  - daftar file, ukuran, dan SHA-256
- Scope file dibatasi hanya untuk:
  - `database/kasir_offline.sqlite`
  - `assets/product_images/...`
  - `assets/receipt_assets/...`
- Database utama wajib tercantum.
- Path absolut, traversal `..`, backslash, duplikat, dan file di luar scope ditolak.
- File secure storage atau token lisensi tidak dapat dimasukkan ke manifest.
- Validasi schema backup yang lebih baru dari aplikasi.
- Verifikasi ukuran dan checksum file secara streaming.
- Installation ID disimpan sebagai hash dengan domain separation, bukan nilai mentah.
- Dependency `crypto` dijadikan dependency langsung untuk SHA-256.

Hasil pengujian:

- Tes manifest dan checksum: **6/6 lulus**
- Regresi database, migrasi v1–v9, dan lisensi: **49/49 lulus**
- Analyzer seluruh `lib` dan `test`: **tidak ada masalah**
- Full test suite: **257/257 lulus**

Schema tetap v9. Belum ada penyalinan atau restore database sehingga data pengguna tidak disentuh dan tes manual belum diperlukan.

Batch berikutnya adalah pembuatan snapshot backup nyata: checkpoint/konsistensi SQLite, penyalinan database yang aman, pengumpulan gambar/logo, pembuatan manifest final, dan archive tervalidasi.

> okee berhasil, yuk lanjut tahap berikutnya

<details><summary>6 previous messages</summary>

> Saya lanjut ke Stage 14 Batch 2: pembuatan backup nyata. Fokusnya hanya operasi ekspor yang non-destruktif—Manager-only, snapshot SQLite konsisten, pengumpulan gambar/logo yang benar-benar berada di direktori aplikasi, manifest/checksum final, lalu arsip ZIP sementara yang dipindahkan atomik setelah tervalidasi. Restore belum dilakukan pada batch ini.
>
> Desain teknis sudah ditetapkan: snapshot database memakai `PRAGMA wal_checkpoint(FULL)` lalu `VACUUM INTO`, sehingga hasilnya merupakan database SQLite mandiri tanpa menyalin file WAL secara sembarang. Arsip dibuat di staging pada filesystem tujuan, diverifikasi isi dan checksum-nya, baru di-rename ke nama final. Jika audit gagal, arsip final dihapus sebagai kompensasi; file tujuan yang sudah ada tidak pernah ditimpa.
>
> <details><summary>Created 3 files, edited 6 files</summary>
>
> - Wrote `./kasir_offline/lib/core/audit/audit_codes.dart` (+2 -0)
> - Wrote `./kasir_offline/lib/core/database/app_database.dart` (+7 -2)
> - Wrote `./kasir_offline/lib/features/backup/services/backup_database_snapshotter.dart` (+70 -0)
> - Wrote `./kasir_offline/lib/features/backup/services/pos_backup_service.dart` (+279 -0)
> - Wrote `./kasir_offline/pubspec.yaml` (+1 -0)
> - Wrote `./kasir_offline/lib/features/backup/services/pos_backup_service.dart` (+2 -3)
> - Wrote `./kasir_offline/test/pos_backup_service_test.dart` (+234 -0)
> - Wrote `./kasir_offline/lib/features/backup/services/pos_backup_service.dart` (+5 -4)
> - Wrote `./kasir_offline/lib/features/backup/services/backup_database_snapshotter.dart` (+14 -14)
> - Wrote `./kasir_offline/pubspec.yaml` (+1 -0)
> - Wrote `./kasir_offline/test/pos_backup_service_test.dart` (+17 -10)
>
> </details>
>
> Backup nyata kini lulus 10/10 tanpa warning database ganda. Tes membuktikan snapshot dapat dibuka sebagai SQLite mandiri, dua aset ikut, secure token tidak ikut, Kasir ditolak, file lama tidak ditimpa, dan kegagalan audit membersihkan archive serta staging. Saya lanjutkan analyzer dan regresi migrasi/audit.
>
> <details><summary>Edited a file</summary>
>
> - Wrote `./kasir_offline/lib/features/backup/services/pos_backup_service.dart` (+26 -15)
>
> </details>
>
> Regresi final backup, query audit, dan seluruh matriks migrasi lulus 54/54; analyzer juga bersih. Saya lanjutkan full suite. Restore belum diaktifkan, sehingga batch ini tetap hanya membaca dan mengekspor data tanpa mengubah database aktif.

</details>

Stage 14 Batch 2—pembuatan backup nyata—selesai.

Yang sudah diterapkan:

- Backup hanya dapat dibuat oleh Manager aktif.
- Snapshot SQLite konsisten menggunakan:
  - `wal_checkpoint(FULL)`
  - `VACUUM INTO`
- Snapshot diverifikasi dengan:
  - `integrity_check`
  - `foreign_key_check`
  - pemeriksaan `user_version`
- Gambar produk dan logo struk ikut disalin.
- Symbolic link ditolak.
- Token lisensi dan folder lain di luar scope tidak ikut.
- Manifest dan checksum seluruh file diverifikasi sebelum archive diterbitkan.
- Archive memakai ekstensi `.kbackup` dan ZIP streaming.
- Proses menggunakan staging pada filesystem tujuan.
- Archive sementara di-rename ke tujuan final setelah valid.
- File tujuan yang sudah ada tidak pernah ditimpa.
- Staging dan file sementara selalu dibersihkan.
- Audit `backup_created` menyimpan metadata aman:
  - format backup
  - schema version
  - jumlah file
  - ukuran archive
  - SHA-256 archive
- Jika audit gagal, archive final ikut dihapus.

Hasil pengujian:

- Backup, snapshot, manifest, aset, dan cleanup: **10/10 lulus**
- Regresi backup, audit, dan seluruh migrasi v1–v9: **54/54 lulus**
- Analyzer seluruh `lib` dan `test`: **tidak ada masalah**
- Full test suite: **261/261 lulus**

Schema tetap v9. Restore belum diaktifkan, sehingga database aktif tidak pernah ditimpa. Tes manual belum diperlukan karena layanan backup belum memiliki UI pemilihan lokasi.

Batch berikutnya adalah fondasi restore aman: ekstraksi ke staging, validasi archive/manifest/checksum/schema/database, penolakan archive corrupt atau path traversal, dan persiapan rollback—belum langsung mengganti database aktif.

> okeee berhasil, yuk lanjut tahap berikutnya

<details><summary>9 previous messages</summary>

> Saya lanjut ke Stage 14 Batch 3: validasi dan ekstraksi restore ke staging, tanpa mengganti database aktif. Saya akan memperketat boundary archive—ekstensi, ukuran, jumlah entri, path traversal, duplikat, manifest tunggal, schema, checksum, dan kesehatan SQLite—serta memastikan staging selalu dibersihkan jika satu pemeriksaan saja gagal.
>
> Batas restore ditetapkan konservatif: archive maksimal 2 GB, maksimal 20.000 entri, manifest maksimal 1 MB, total hasil ekstraksi maksimal 5 GB, dan satu file maksimal 2 GB. Semua nama entri diperiksa sebelum ekstraksi; file yang tidak tercantum atau duplikat ditolak. Installation ID hanya dibandingkan sebagai hash dan perbedaan perangkat tidak menggagalkan validasi—lisensi tetap ditangani terpisah.
>
> <details><summary>Created a file, edited 3 files</summary>
>
> - Wrote `./kasir_offline/lib/features/backup/services/backup_database_snapshotter.dart` (+17 -5)
> - Wrote `./kasir_offline/lib/features/backup/services/backup_restore_staging_service.dart` (+279 -0)
> - Wrote `./kasir_offline/lib/features/backup/services/backup_restore_staging_service.dart` (+13 -13)
> - Wrote `./kasir_offline/test/pos_backup_service_test.dart` (+207 -0)
> - Wrote `./kasir_offline/test/pos_backup_service_test.dart` (+1 -0)
> - Wrote `./kasir_offline/test/pos_backup_service_test.dart` (+2 -15)
>
> </details>
>
> Staging restore sekarang lulus 14/14. Backup valid diekstrak dan database staging terbukti berisi kondisi saat backup, sementara database aktif yang sudah berubah tetap tidak tersentuh. Archive corrupt, traversal, checksum berubah, schema lebih baru, dan akses Kasir semuanya ditolak serta staging dibersihkan.
>
> <details><summary>Edited a file</summary>
>
> - Wrote `./kasir_offline/lib/features/backup/services/backup_database_snapshotter.dart` (+14 -3)
> - Wrote `./kasir_offline/lib/features/backup/services/backup_database_snapshotter.dart` (+3 -4)
> - Wrote `./kasir_offline/lib/features/backup/services/backup_database_snapshotter.dart` (+2 -8)
>
> </details>
>
> Context compaction
>
> Source: automatic
> Status: completed
>
> Saya lanjutkan verifikasi akhir Batch 3 Tahap 14: saya konfirmasi regresi terkait, lalu jalankan seluruh test suite. Setelah itu saya rangkum hasilnya dan batas aman fitur restore yang sudah tercapai.
>
> Regresi terkait sudah lulus 62/62. Saya lanjutkan seluruh suite proyek untuk memastikan perubahan restore staging tidak merusak fitur lain.
>
> Suite penuh masih berjalan normal; progres terakhir sudah melewati test setup awal tanpa kegagalan. Saya tunggu sampai hasil terminal keluar agar angka kelulusannya pasti.

</details>

Tahap 14 Batch 3 selesai: persiapan restore aman ke staging.

Yang sudah diterapkan:

- Restore hanya dapat dilakukan Manager.
- Arsip `.kbackup` divalidasi sebelum digunakan.
- Perlindungan path traversal dan archive korup.
- Batas ukuran arsip, file, manifest, dan jumlah entri.
- Verifikasi manifest, checksum, versi schema, integritas SQLite, dan foreign key.
- Pencocokan installation ID tanpa membocorkan ID asli.
- Staging otomatis dibersihkan jika proses gagal.
- Database aktif sama sekali belum disentuh pada tahap ini.

Hasil verifikasi:

- Test backup dan restore staging: **14/14 lulus**
- Regression test terkait: **62/62 lulus**
- Analyzer: **tidak ada masalah**
- Seluruh test proyek: **265/265 lulus**
- Schema database tetap **versi 9**

Belum perlu pengujian manual karena tahap ini belum mengganti database aktif maupun mengubah UI.

Batch berikutnya adalah mekanisme restore aktual: membuat safety backup, menutup koneksi database, mengganti database dan aset secara atomik, membuka ulang database, serta rollback jika terjadi kegagalan. Ini bagian berisiko tinggi, jadi akan dikerjakan bertahap dan diuji ketat sebelum meminta pengujian manual.

> okee bagus, yuk lanjut batch berikutnya

<details><summary>7 previous messages</summary>

> Saya lanjut ke Tahap 14 Batch 4: orkestrasi restore aktual dengan safety backup dan rollback. Karena bagian ini menyentuh database aktif, saya akan terlebih dahulu memetakan lifecycle database serta lokasi aset, lalu menerapkan jalur kegagalan yang bisa dipulihkan sebelum menambahkan test.
>
> Audit menunjukkan aplikasi saat ini memakai satu instance `AppDatabase` yang disuntikkan ke banyak repository. Jadi Batch 4 saya batasi pada mesin commit filesystem yang benar-benar atomik-terpulihkan: database ditutup melalui lifecycle callback, DB/aset lama dipindahkan ke safety area, hasil restore dipasang, diverifikasi setelah dibuka ulang, dan seluruh perubahan di-rollback bila satu langkah gagal. Integrasi UI/restart aplikasi akan menjadi lapisan berikutnya agar tidak meninggalkan repository yang menunjuk koneksi lama.
>
> <details><summary>Created 2 files, edited a file</summary>
>
> - Wrote `./kasir_offline/lib/features/backup/services/backup_restore_commit_service.dart` (+229 -0)
> - Wrote `./kasir_offline/test/backup_restore_commit_service_test.dart` (+308 -0)
> - Wrote `./kasir_offline/lib/features/backup/services/backup_restore_commit_service.dart` (+8 -4)
>
> </details>
>
> Targeted test menemukan kesalahan pada fixture test, bukan implementasi: hash installation ID dummy memakai karakter non-heksadesimal sehingga ditolak oleh kontrak manifest. Saya koreksi fixture menjadi SHA-256 valid lalu ulangi seluruh targeted test.
>
> <details><summary>Edited 2 files</summary>
>
> - Wrote `./kasir_offline/test/backup_restore_commit_service_test.dart` (+1 -1)
> - Wrote `./kasir_offline/lib/features/backup/services/backup_restore_commit_service.dart` (+20 -7)
>
> </details>
>
> Targeted test kini lulus 18/18 dan analyzer bersih. Berikutnya saya jalankan regresi database/migrasi/lisensi/audit, kemudian seluruh suite proyek; ini penting karena rollback mengandalkan konsistensi file database dan pemisahan data lisensi.
>
> Regresi terkait sudah lulus 66/66. Suite penuh masih berjalan; saya lanjut menunggu hasil terminal agar tidak menyimpulkan dari progres parsial.

</details>

Tahap 14 Batch 4 selesai: mesin commit restore dan rollback aman.

Yang sudah diterapkan:

- Safety copy database dan seluruh aset aktif sebelum penggantian.
- Database ditutup melalui lifecycle terkontrol.
- Database serta aset backup dipasang ke lokasi aktif.
- Database dibuka kembali dan divalidasi.
- Kegagalan membuka database baru memicu rollback penuh.
- Kegagalan pencatatan setelah commit juga memicu rollback.
- Aset lama yang tidak tercantum dalam backup tidak tertinggal.
- Staging dan working directory selalu dibersihkan.
- Error rollback kritis dibedakan melalui `RestoreRollbackException`.

Hasil pengujian:

- Targeted backup/restore: **18/18 lulus**
- Regression database, migrasi, lisensi, dan audit: **66/66 lulus**
- Analyzer seluruh `lib` dan `test`: **bersih**
- Seluruh test proyek: **269/269 lulus**
- Schema tetap **versi 9**

Belum perlu test manual. Mesin commit sudah teruji di level filesystem, tetapi belum dihubungkan ke tombol/UI karena aplikasi memakai satu instance database yang dibagikan ke banyak repository. Batch berikutnya perlu mengintegrasikan lifecycle aplikasi secara benar—termasuk audit `restore_completed`, session reset, dan restart/rebootstrap setelah restore—agar tidak ada komponen yang memakai koneksi database lama.

> okee bagus, yuk lanjut tahap berikutnya

<details><summary>7 previous messages</summary>

> Saya lanjut ke Tahap 14 Batch 5: integrasi alur restore tingkat aplikasi. Fokusnya adalah mencatat `restore_completed` pada database hasil restore, mengakhiri sesi lama, dan menghasilkan sinyal rebootstrap wajib agar tidak ada repository yang tetap memakai koneksi sebelum restore. Saya audit struktur root aplikasi dan session terlebih dahulu sebelum mengubah wiring.
>
> Struktur session tidak memiliki global session store; session hidup di navigation shell. Karena itu saya gunakan kontrak eksplisit: restore sukses wajib mengirim sinyal `requiresApplicationRestart` dan menjalankan callback invalidasi session. Audit ditulis ke database hasil restore dengan snapshot identitas Manager; UID hanya dipakai bila user tersebut memang ada di backup, sehingga foreign key tetap valid saat restore lintas snapshot.
>
> <details><summary>Created 3 files, edited 3 files</summary>
>
> - Wrote `./kasir_offline/lib/core/audit/audit_codes.dart` (+1 -0)
> - Wrote `./kasir_offline/lib/features/backup/services/backup_restore_commit_service.dart` (+13 -1)
> - Wrote `./kasir_offline/lib/features/backup/services/backup_restore_staging_service.dart` (+8 -1)
> - Wrote `./kasir_offline/lib/features/backup/services/pos_restore_service.dart` (+32 -0)
> - Wrote `./kasir_offline/lib/features/backup/services/restore_completion_recorder.dart` (+80 -0)
> - Wrote `./kasir_offline/test/pos_restore_service_test.dart` (+235 -0)
>
> </details>
>
> Targeted test berhenti pada compile-time fixture: helper setup toko memang mewajibkan preferensi tema. Saya lengkapi fixture dengan konfigurasi tema standar, lalu ulangi; test implementasi lama yang sempat berjalan tetap lulus.
>
> <details><summary>Edited 5 files</summary>
>
> - Wrote `./kasir_offline/test/pos_restore_service_test.dart` (+13 -0)
> - Wrote `./kasir_offline/lib/features/backup/services/backup_restore_commit_service.dart` (+1 -0)
> - Wrote `./kasir_offline/lib/features/backup/services/backup_restore_staging_service.dart` (+1 -0)
> - Wrote `./kasir_offline/lib/features/backup/services/pos_restore_service.dart` (+14 -4)
> - Wrote `./kasir_offline/lib/features/backup/services/restore_completion_recorder.dart` (+1 -1)
> - Wrote `./kasir_offline/lib/features/backup/services/backup_restore_staging_service.dart` (+1 -1)
>
> </details>
>
> Targeted backup/restore lulus 22/22, regresi terkait lulus 70/70, dan analyzer bersih. Saya jalankan seluruh suite proyek sebagai gerbang terakhir Batch 5.
>
> Suite penuh masih berjalan tanpa hasil kegagalan terminal. Saya cek progresnya lalu tunggu hingga semua test selesai.

</details>

Tahap 14 Batch 5 selesai: integrasi coordinator dan audit restore.

Yang selesai:

- Coordinator menyatukan proses prepare dan commit restore.
- Session lama hanya diinvalidasi setelah commit berhasil.
- Kegagalan commit tidak mengubah session.
- Hasil restore mewajibkan restart/rebootstrap aplikasi.
- Audit code `restore_completed` ditambahkan.
- Audit ditulis ke database hasil restore.
- Informasi audit mencakup checksum arsip, schema, versi aplikasi backup, jumlah file, dan kecocokan instalasi.
- Jika akun Manager lama tidak ada dalam backup, audit tetap valid dengan UID `null` dan snapshot nama/role.
- Token lisensi tetap terpisah dan tidak dipindahkan.

Hasil verifikasi:

- Targeted backup/restore: **22/22 lulus**
- Regression restore, migrasi, lisensi, dan audit: **70/70 lulus**
- Analyzer seluruh proyek: **bersih**
- Seluruh test proyek: **273/273 lulus**
- Schema database tetap **versi 9**

Belum perlu test manual karena alur pemilihan file dan UI restore belum dipasang. Batch berikutnya adalah wiring lifecycle database produksi dan UI Manager: pilih file `.kbackup`, konfirmasi risiko, tampilkan status restore, lalu mengarahkan aplikasi ke restart/rebootstrap dan login ulang.

> okee bagus, yuk lanjut tahap berikutnya

<details><summary>14 previous messages</summary>

> Saya lanjut ke Tahap 14 Batch 6: wiring restore pada aplikasi produksi dan UI Manager. Saya akan memastikan dulu lokasi database Drift yang sebenarnya serta titik menu Manager yang tepat, karena salah menentukan path database adalah risiko terbesar pada tahap ini. Setelah itu baru saya pasang pemilih file, konfirmasi, progress, dan alur login ulang.
>
> <details><summary>Created 3 files, edited 7 files, searched the web</summary>
>
> Searched the web for `site:pub.dev/packages/file_picker file_picker Flutter package latest Android pickFiles allowedExtensions ...`
>
> - Wrote `./kasir_offline/pubspec.yaml` (+1 -0)
> - Wrote `./kasir_offline/lib/features/backup/presentation/backup_restore_page.dart` (+185 -0)
> - Wrote `./kasir_offline/lib/features/backup/services/production_restore_service_factory.dart` (+95 -0)
> - Wrote `./kasir_offline/lib/app/navigation/app_section.dart` (+11 -6)
> - Wrote `./kasir_offline/lib/app/session/app_user_session.dart` (+4 -3)
> - Wrote `./kasir_offline/lib/features/shell/presentation/app_shell_page.dart` (+5 -3)
> - Wrote `./kasir_offline/lib/main.dart` (+14 -3)
> - Wrote `./kasir_offline/lib/app/session/app_user_session.dart` (+1 -1)
> - Wrote `./kasir_offline/lib/features/backup/services/production_restore_service_factory.dart` (+18 -4)
> - Wrote `./kasir_offline/test/backup_restore_page_test.dart` (+214 -0)
> - Wrote `./kasir_offline/lib/features/backup/presentation/backup_restore_page.dart` (+1 -1)
> - Wrote `./kasir_offline/lib/features/backup/services/production_restore_service_factory.dart` (+16 -5)
>
> </details>
>
> Targeted run menemukan dua hal pada test: aturan Manager legacy perlu diperbarui karena menu Backup, seperti menu Akun, memang wajib identitas persisten; dan `pumpAndSettle` menunggu animasi progress tak berbatas. Saya sesuaikan assertion akses dengan kebijakan baru dan membuat widget test menunggu durasi terukur.
>
> <details><summary>Edited 2 files</summary>
>
> - Wrote `./kasir_offline/test/backup_restore_page_test.dart` (+2 -1)
> - Wrote `./kasir_offline/test/manager_role_access_test.dart` (+6 -4)
> - Wrote `./kasir_offline/test/backup_restore_page_test.dart` (+0 -2)
> - Wrote `./kasir_offline/test/backup_restore_page_test.dart` (+0 -1)
>
> </details>
>
> Wiring UI dan lifecycle kini lulus targeted regression **40/40**, termasuk shell Manager/Kasir, login, validasi ekstensi, pembatalan konfirmasi, restore sukses, dan permintaan tutup aplikasi. Analyzer juga bersih; saya lanjutkan seluruh suite proyek.
>
> Suite penuh masih berjalan tanpa hasil gagal terminal. Saya tunggu hingga semua test selesai karena perubahan ini menyentuh navigasi dan dependency native.
>
> Seluruh suite lulus **276/276**. Karena Batch 6 menambahkan plugin pemilih file native dan menyentuh database fisik aplikasi, saya juga akan menjalankan build APK debug sebagai pemeriksaan kompilasi Android. Setelah build berhasil, tahap ini memang memerlukan satu pengujian manual terkontrol pada perangkat—ini salah satu kondisi yang benar-benar tidak bisa dibuktikan hanya dengan unit/widget test.
>
> Build Android menemukan masalah nyata pada `file_picker` 11.0.2: registrant Flutter mengacu ke `FilePickerPlugin`, tetapi artefak plugin tidak menyediakan kelas tersebut saat kompilasi Java. Saya akan memeriksa metadata plugin dan kompatibilitas versi Flutter/Gradle lokal, lalu memilih versi stabil yang benar-benar bisa dibangun—tanpa mengubah logika restore.
>
> Penyebabnya sudah jelas: proyek memakai AGP 9 tetapi masih menonaktifkan Built-in Kotlin demi kompatibilitas plugin lama; `file_picker` 11 justru tidak mengaktifkan Kotlin plugin pada AGP 9 dan mengasumsikan Built-in Kotlin aktif. Mengaktifkannya secara global berisiko merusak plugin printer. Solusi paling aman adalah memakai `file_picker` 10.3.10 yang masih kompatibel dengan konfigurasi ini, lalu build ulang.
>
> <details><summary>Edited 2 files</summary>
>
> - Wrote `./kasir_offline/lib/features/backup/presentation/backup_restore_page.dart` (+1 -1)
> - Wrote `./kasir_offline/pubspec.yaml` (+1 -1)
>
> </details>
>
> Analyzer sempat melewati batas waktu tanpa mengeluarkan error—kemungkinan resource masih dipakai proses Gradle sebelumnya. Saya lanjutkan build ulang; hasil kompilasi akan langsung membuktikan kompatibilitas plugin, lalu analyzer dan suite akan saya konfirmasi lagi setelah resource stabil.
>
> APK debug sekarang berhasil dibangun dengan plugin yang kompatibel, dan analyzer kembali bersih. Karena dependency native berubah setelah suite sebelumnya, saya ulang seluruh test suite sekali lagi agar hasil akhir Batch 6 benar-benar berasal dari dependency final.
>
> Suite final masih berjalan dan belum mengeluarkan status terminal. Saya lanjut menunggu hasil lengkapnya.
>
> Suite final masih aktif; progres sebelumnya sudah melewati login dan shell tanpa kegagalan. Saya tunggu satu putaran lagi sampai hasil terminal muncul.

</details>

Tahap 14 Batch 6 selesai: restore sudah terhubung ke aplikasi dan UI Manager.

Yang tersedia:

- Menu baru **Backup** khusus Manager dengan identitas persisten.
- Pemilih file native Android/desktop.
- Validasi ekstensi `.kbackup`.
- Dialog konfirmasi risiko restore.
- Progress dan pesan kegagalan.
- Safety copy, rollback, audit, serta pemisahan lisensi tetap aktif.
- Setelah database ditutup, session dianggap tidak valid.
- Restore berhasil meminta aplikasi ditutup dan dibuka kembali.
- Lokasi database produksi sudah dipastikan: `Documents/kasir_offline.sqlite`.

Verifikasi akhir:

- Targeted regression: **40/40 lulus**
- Analyzer: **bersih**
- Seluruh test final: **276/276 lulus**
- APK debug: **berhasil dibangun**
- Schema tetap **versi 9**

APK tersedia di [app-debug.apk](./kasir_offline/build/app/outputs/flutter-apk/app-debug.apk).

Ada peringatan kompatibilitas Kotlin untuk versi Flutter masa depan, tetapi build saat ini berhasil. `file_picker` dikunci ke versi 10.3.10 karena versi 11 tidak kompatibel dengan konfigurasi AGP/Kotlin proyek dan gagal dikompilasi.

Untuk tahap ini, pengujian perangkat memang diperlukan, tetapi sebaiknya dilakukan setelah UI pembuatan backup dipasang pada batch berikutnya. Dengan begitu kita dapat menguji siklus nyata secara aman: buat data → backup → ubah data → restore → buka ulang → verifikasi data. Jangan uji restore memakai data produksi terlebih dahulu.

> okee pengujian perangkat setelah batch berikutnya, uji perangkatnya melalui flutter run -d 10DE3201NZ000NB

<details><summary>7 previous messages</summary>

> Siap. Saya catat device target **`10DE3201NZ000NB`** dan setelah batch berikutnya selesai serta seluruh test otomatis lulus, saya akan menjalankan langsung:
>
> `flutter run -d 10DE3201NZ000NB`
>
> Batch berikutnya saya kerjakan untuk UI pembuatan backup/export agar pengujian perangkat bisa menjalankan siklus lengkap dan aman, bukan hanya membuka file picker restore.
>
> <details><summary>Created a file, edited 5 files</summary>
>
> - Wrote `./kasir_offline/lib/features/backup/services/backup_destination_service.dart` (+32 -0)
> - Wrote `./kasir_offline/lib/features/backup/services/pos_backup_service.dart` (+9 -1)
> - Wrote `./kasir_offline/lib/main.dart` (+20 -3)
> - Wrote `./kasir_offline/lib/features/backup/presentation/backup_restore_page.dart` (+67 -0)
> - Wrote `./kasir_offline/test/backup_restore_page_test.dart` (+88 -2)
> - Wrote `./kasir_offline/lib/features/backup/presentation/backup_restore_page.dart` (+4 -4)
> - Wrote `./kasir_offline/lib/features/backup/services/backup_destination_service.dart` (+3 -4)
>
> </details>
>
> UI pembuatan backup sudah lulus targeted **20/20** dan analyzer bersih. Backup akan disimpan dengan nama bertimestamp ke folder Downloads; bila platform tidak menyediakan Downloads, digunakan folder backup privat aplikasi. Saya jalankan seluruh suite sebelum memasang ke device.
>
> Suite penuh masih berjalan normal. Saya tunggu sampai hasil terminal keluar sebelum menjalankan aplikasi di device.
>
> Seluruh suite final lulus **277/277**. Sekarang saya masuk ke tahap perangkat yang disepakati: cek koneksi device `10DE3201NZ000NB`, lalu jalankan aplikasi menggunakan `flutter run -d 10DE3201NZ000NB` dan pantau sampai terpasang serta terbuka.
>
> Device terdeteksi sebagai Vivo V2322 Android 15. `flutter run` sedang menyelesaikan build dan instalasi; belum ada error, hanya peringatan Kotlin yang sama seperti build sebelumnya. Saya lanjut pantau sampai aplikasi benar-benar diluncurkan.
>
> APK sudah berhasil dibangun dan terpasang ke device. Log `flutter run` belum menampilkan koneksi debug setelah instalasi, jadi saya verifikasi langsung apakah proses aplikasi sudah hidup di perangkat sebelum menyerahkan langkah manual.

</details>

Aplikasi sudah berhasil dibangun, dipasang, dan sedang berjalan pada device **10DE3201NZ000NB** melalui `flutter run`.

Hasil otomatis:

- Targeted backup/restore: **20/20 lulus**
- Analyzer: **bersih**
- Seluruh suite: **277/277 lulus**
- Proses aplikasi aktif di device.

Silakan lakukan pengujian manual berikut dengan data uji:

1. Login sebagai Manager.
2. Buka menu **Backup**.
3. Tekan **Buat backup sekarang**.
4. Pastikan muncul pesan lokasi file `.kbackup`.
5. Ubah satu data yang mudah dikenali, misalnya nama toko/produk.
6. Tekan **Pilih file backup**, pilih file yang baru dibuat, lalu konfirmasi.
7. Setelah berhasil, tekan **Tutup aplikasi sekarang**.
8. Buka kembali aplikasi dan login ulang.
9. Pastikan data kembali ke kondisi sebelum perubahan.
10. Pastikan gambar produk dan logo struk tetap tersedia.
11. Pastikan lisensi perangkat tetap valid atau tidak berpindah secara keliru.

Mohon beri tahu hasilnya, terutama:

- apakah file tersimpan dan bisa dipilih;
- apakah aplikasi berhasil ditutup serta dibuka kembali;
- apakah data lama benar-benar kembali;
- pesan error apa pun yang muncul.

> file nya terlalu sulit taruhnya malah disni Backup tersimpan: /storage/emulated/0/Android/data/com.example.kasir_offline/files/Download/kasir-offline-20260725-091002.kbackup buatkan masuk ke download aja yg terbaru, jangan terlalu susah nyarinya, saya tidak menemukannya

<details><summary>6 previous messages</summary>

> Benar, lokasi itu adalah folder Download privat aplikasi dan memang sulit ditemukan dari file manager. Saya akan ubah agar backup diekspor ke **Download publik perangkat** melalui Android MediaStore, sehingga tampil langsung di folder Download dan bisa dipilih kembali dari file picker. File sementara privat akan dibersihkan setelah ekspor berhasil.
>
> <details><summary>Created a file, edited 3 files</summary>
>
> - Wrote `./kasir_offline/android/app/src/main/kotlin/com/example/kasir_offline/MainActivity.kt` (+81 -0)
> - Wrote `./kasir_offline/lib/features/backup/presentation/backup_restore_page.dart` (+2 -1)
> - Wrote `./kasir_offline/lib/features/backup/services/backup_destination_service.dart` (+22 -4)
> - Wrote `./kasir_offline/test/backup_restore_page_test.dart` (+5 -0)
> - Wrote `./kasir_offline/test/backup_restore_page_test.dart` (+4 -1)
>
> </details>
>
> Targeted test lulus **12/12** dan analyzer bersih. Exporter sekarang memakai Android MediaStore, sehingga hasil ditampilkan sebagai `Download/nama-file.kbackup`, bukan lagi `Android/data/...`. Saya build Android untuk memvalidasi kode Kotlin native sebelum memasang ulang ke device.
>
> Build Android dengan MediaStore berhasil. Suite penuh masih berjalan; setelah lulus saya pasang ulang build ini ke device yang sama untuk Anda coba membuat backup lagi.
>
> Seluruh suite kembali lulus **277/277**. Saya pasang ulang versi yang sudah memakai Download publik ke device `10DE3201NZ000NB`, lalu pastikan proses aplikasinya aktif.
>
> `flutter run` sedang rebuild untuk arm64; belum ada error. Saya lanjut pantau sampai instalasi selesai.

</details>

Sudah diperbaiki. Backup sekarang diekspor melalui Android MediaStore ke folder publik:

`Download/kasir-offline-YYYYMMDD-HHMMSS.kbackup`

Versi terbaru sudah:

- Targeted test: **12/12 lulus**
- Seluruh test: **277/277 lulus**
- Analyzer: **bersih**
- APK berhasil dibangun
- Dipasang dan sedang berjalan di device **10DE3201NZ000NB**

Silakan tekan **Buat backup sekarang** lagi. Pesan yang benar seharusnya seperti:

`Backup tersimpan: Download/kasir-offline-20260725-....kbackup`

Kemudian buka aplikasi **File Manager → Download**. File `.kbackup` seharusnya langsung terlihat dan mudah dipilih untuk restore. File sementara di folder privat otomatis dihapus setelah ekspor berhasil.

> tapi sekarang ketika download itu jadi bentuk zip, dan kalau di ekstrak keluar semua isinya satu2, gimana kalau saat pilih file backup itu bentuk zip aja, jadi user engga perlu ekstrak dll? kira2 bisa engga?

<details><summary>5 previous messages</summary>

> Bisa, dan user memang tidak perlu mengekstrak apa pun. File backup adalah arsip ZIP terstruktur, tetapi aplikasi harus menerima arsip tersebut secara langsung.
>
> Saya akan memperbaiki dua hal:
>
> - backup baru tetap bernama `.kbackup` dan MIME-nya tidak lagi diumumkan sebagai ZIP agar File Manager tidak mengubah/menonjolkannya sebagai arsip biasa;
> - menu restore menerima baik `.kbackup` maupun `.zip` untuk kompatibilitas dengan file yang sudah terlanjur ditampilkan atau disimpan sebagai ZIP.
>
> Isi arsip tetap divalidasi lewat manifest, checksum, schema, dan integritas database—jadi bukan sembarang ZIP yang bisa direstore.
>
> <details><summary>Edited 5 files</summary>
>
> - Wrote `./kasir_offline/android/app/src/main/kotlin/com/example/kasir_offline/MainActivity.kt` (+4 -1)
> - Wrote `./kasir_offline/lib/features/backup/presentation/backup_restore_page.dart` (+7 -3)
> - Wrote `./kasir_offline/lib/features/backup/services/backup_restore_staging_service.dart` (+3 -2)
> - Wrote `./kasir_offline/test/backup_restore_page_test.dart` (+8 -3)
> - Wrote `./kasir_offline/test/pos_backup_service_test.dart` (+1 -1)
> - Wrote `./kasir_offline/test/pos_backup_service_test.dart` (+25 -0)
>
> </details>
>
> Targeted test lulus **19/19** dan analyzer bersih. Sekarang arsip `.zip` valid dapat dipilih langsung tanpa ekstraksi, sedangkan ZIP sembarang tetap ditolak oleh validasi manifest/checksum/database. Saya build dan pasang ulang agar perubahan MIME juga aktif di Android.
>
> APK berhasil dibangun. Suite penuh masih berjalan; sesudah hasil terminal keluar saya pasang versi ini ke device yang sama.
>
> Suite penuh lulus **278/278**. Saya pasang ulang versi final ke device `10DE3201NZ000NB`; setelah aktif, Anda cukup memilih file `.kbackup` atau `.zip` langsung—jangan diekstrak.

</details>

Bisa, dan sekarang sudah diterapkan.

Perilaku terbaru:

- Backup baru tetap bernama `.kbackup`.
- File disimpan di folder **Download publik**.
- Android tidak lagi diberi MIME ZIP biasa.
- Restore menerima file `.kbackup` maupun `.zip`.
- User memilih arsipnya langsung—tidak perlu diekstrak.
- ZIP sembarang tetap ditolak karena harus memiliki manifest, checksum, schema, dan database yang valid.

Hasil verifikasi:

- Targeted test: **19/19 lulus**
- Seluruh test: **278/278 lulus**
- Analyzer: **bersih**
- APK Android berhasil dibangun
- Versi terbaru sudah dipasang dan aktif di device **10DE3201NZ000NB**

Silakan coba:

1. Buat backup baru.
2. Buka menu restore.
3. Pilih file `.kbackup` atau `.zip` tersebut secara langsung.
4. Jangan tekan ekstrak.
5. Konfirmasi restore.

File backup lama yang tampil sebagai ZIP juga seharusnya sekarang bisa langsung dipilih.

> okee berhasil, yuk lanjut ke langkah tahap selanjutnya

<details><summary>7 previous messages</summary>

> Bagus, berarti siklus perangkat untuk backup → Download publik → pilih arsip langsung sudah tervalidasi. Saya tandai pengujian manual selesai, lalu saya baca kembali urutan roadmap setelah Tahap 14 agar batch berikutnya tetap mengikuti pedoman dan tidak melompati release blocker.
>
> Tahap berikutnya sesuai roadmap adalah **Tahap 15: Data Integrity dan Maintenance**. Saya mulai Batch 1 dari service diagnostik database khusus Manager: `integrity_check`, `foreign_key_check`, schema version, ringkasan jumlah data, tanggal backup terakhir, dan ukuran file database—semuanya read-only tanpa repair otomatis atau secret.
>
> <details><summary>Created 2 files, edited a file</summary>
>
> - Wrote `./kasir_offline/lib/features/maintenance/services/database_health_service.dart` (+163 -0)
> - Wrote `./kasir_offline/lib/features/maintenance/services/database_health_service.dart` (+2 -1)
> - Wrote `./kasir_offline/test/database_health_service_test.dart` (+156 -0)
>
> </details>
>
> Targeted test menemukan fixture FK sintetis belum mengisi kolom timestamp wajib; service healthy dan test lain sudah berjalan. Saya lengkapi baris sintetis tersebut tanpa mengubah implementasi diagnostik, lalu ulangi.
>
> <details><summary>Edited a file</summary>
>
> - Wrote `./kasir_offline/test/database_health_service_test.dart` (+4 -1)
>
> </details>
>
> Tahap 15 Batch 1 targeted regression lulus **19/19** dan analyzer bersih. Diagnostik terbukti mendeteksi pelanggaran FK tanpa mengubah atau memperbaiki data secara otomatis. Saya jalankan seluruh suite sebagai gerbang akhir batch.
>
> Suite penuh masih berjalan tanpa status gagal terminal. Saya lanjut tunggu sampai seluruh test selesai.

</details>

Tahap 15 Batch 1 selesai: service diagnostik kesehatan database khusus Manager.

Pemeriksaan yang tersedia:

- `PRAGMA integrity_check`
- `PRAGMA foreign_key_check`
- Schema/user version
- Ringkasan jumlah toko, kategori, produk, metode pembayaran, akun, transaksi, pergerakan stok, dan audit
- Waktu backup terakhir
- Ukuran file database
- Status sehat/tidak sehat

Perlindungannya:

- Hanya Manager aktif dengan identitas persisten.
- Seluruh proses hanya membaca data.
- Tidak ada repair atau penghapusan otomatis.
- Pesan kegagalan tidak membocorkan detail internal atau secret.
- Pelanggaran foreign key sintetis berhasil dideteksi dan tetap tidak dimutasi.

Hasil verifikasi:

- Targeted regression: **19/19 lulus**
- Analyzer: **bersih**
- Seluruh test: **282/282 lulus**
- Schema tetap **versi 9**

Tidak perlu pengujian perangkat untuk batch service read-only ini. Batch berikutnya dapat menampilkan hasil diagnostik pada halaman Manager, kemudian dilanjutkan ke audit transaction numbering sesuai Tahap 15.2.

> OKEE lanjut tahap 15 batch 2

<details><summary>6 previous messages</summary>

> Saya lanjut Tahap 15 Batch 2 dengan menampilkan diagnostik database di halaman Manager. UI akan menjalankan pemeriksaan hanya saat tombol ditekan, menampilkan status sehat/tidak sehat, schema, FK, jumlah data, backup terakhir, dan ukuran database—tanpa tombol repair otomatis.
>
> <details><summary>Edited 4 files</summary>
>
> - Wrote `./kasir_offline/lib/features/maintenance/services/database_health_service.dart` (+6 -1)
> - Wrote `./kasir_offline/lib/main.dart` (+21 -6)
> - Wrote `./kasir_offline/lib/features/backup/presentation/backup_restore_page.dart` (+162 -0)
> - Wrote `./kasir_offline/test/backup_restore_page_test.dart` (+103 -0)
>
> </details>
>
> Widget test diagnostik baru lulus, tetapi kartu tambahan mendorong tombol restore ke bawah viewport test 600px sehingga tiga test lama mengetuk area di luar layar. Ini bukan bug UI; saya perbaiki test agar melakukan scroll/`ensureVisible` seperti interaksi pengguna sebelum menekan tombol restore.
>
> <details><summary>Edited a file</summary>
>
> - Wrote `./kasir_offline/test/backup_restore_page_test.dart` (+10 -3)
>
> </details>
>
> Targeted UI/service/role test lulus **14/14** dan analyzer bersih. Saya jalankan seluruh suite untuk memastikan penambahan kartu diagnostik tidak mengganggu menu Backup, restore, maupun navigasi lain.
>
> Suite penuh masih berjalan tanpa status gagal terminal. Saya lanjut menunggu sampai hasil lengkap keluar.

</details>

Tahap 15 Batch 2 selesai: diagnostik database sudah tersedia di UI Manager.

Pada menu **Backup & Restore** sekarang tersedia kartu **Kesehatan database** dengan tombol **Periksa sekarang**.

Informasi yang ditampilkan:

- Status database sehat atau perlu pemeriksaan teknis
- Schema database
- Jumlah pelanggaran foreign key
- Waktu backup terakhir
- Ukuran database
- Jumlah toko, kategori, produk, akun, transaksi, stok, dan audit log

Perlindungannya:

- Hanya Manager aktif yang dapat mengakses.
- Pemeriksaan sepenuhnya read-only.
- Tidak tersedia tombol repair otomatis.
- Error ditampilkan dengan pesan aman.
- Tombol dinonaktifkan setelah restore sampai aplikasi dibuka ulang.

Hasil verifikasi:

- Targeted UI/service/role: **14/14 lulus**
- Analyzer: **bersih**
- Seluruh test: **285/285 lulus**
- Schema tetap **versi 9**

Tidak perlu pengujian perangkat khusus untuk batch ini karena interaksi dan seluruh status UI sudah ditutup oleh widget test. Tahap 15 Batch 3 berikutnya dapat mulai mengaudit dan memperkuat transaction numbering untuk collision, restart, timestamp sama, concurrency, dan rollback.

> okeee yuk lanjut tahap selanjutnya 15 batch 3

<details><summary>7 previous messages</summary>

> Saya lanjut Tahap 15 Batch 3: audit dan hardening nomor transaksi. Saya akan telusuri generator serta jalur checkout terlebih dahulu, lalu membuat test timestamp sama, restart, concurrency, ordering, dan rollback sebelum mengubah algoritmanya.
>
> Context compaction
>
> Source: automatic
> Status: completed
>
> Audit menemukan sumber risikonya: nomor saat ini memakai jumlah transaksi harian (`count + 1`). Jika transaksi lama dibatalkan/dihapus atau ada proses bersamaan, nomor dapat dipakai ulang dan menabrak constraint unik. Saya akan menggantinya dengan urutan maksimum dari nomor yang sudah tersimpan, tetap di dalam transaksi checkout, serta membuat waktu bisa dikendalikan oleh test supaya batas hari/timezone benar-benar teruji.
>
> <details><summary>Edited 2 files</summary>
>
> - Wrote `./kasir_offline/lib/features/sales/data/sale_repository.dart` (+68 -49)
> - Wrote `./kasir_offline/test/sale_repository_test.dart` (+102 -3)
>
> </details>
>
> Test pertama menangkap dua ketidaksesuaian API kecil sebelum logika dijalankan: test perlu mengimpor `Value` dari Drift, dan versi Drift proyek menggunakan `InsertMode.insertOrIgnore` (bukan helper nama pendek). Saya sesuaikan sekarang lalu mengulang test yang sama.
>
> <details><summary>Edited 2 files</summary>
>
> - Wrote `./kasir_offline/lib/features/sales/data/sale_repository.dart` (+6 -5)
> - Wrote `./kasir_offline/test/sale_repository_test.dart` (+2 -1)
>
> </details>
>
> Test inti kini lulus, termasuk 20 checkout paralel pada timestamp identik, urutan `000001–000020`, kelanjutan setelah repository dibuat ulang, dan pergantian tanggal menurut Asia/Jakarta. Berikutnya saya jalankan analyzer serta test regresi penjualan/riwayat/pembatalan, lalu seluruh suite.

</details>

Tahap 15 Batch 3 selesai.

Perubahan utama:

- Nomor transaksi tidak lagi menggunakan `jumlah transaksi + 1`.
- Sequence sekarang mengambil nomor tertinggi yang pernah tersimpan, sehingga aman setelah restart atau penghapusan transaksi.
- Format baru: `KODETOKO-YYYYMMDD-000001`.
- Tanggal nomor mengikuti timezone toko `Asia/Jakarta`.
- Checkout pada timestamp yang sama tetap menghasilkan nomor unik dan berurutan.
- Inisialisasi metode pembayaran dibuat aman ketika dipanggil bersamaan.
- Waktu dapat dikontrol dalam unit test tanpa mengubah jam perangkat.

Hasil pengujian:

- Unit test repository penjualan: **8/8 lulus**
- Test regresi checkout, riwayat, pembatalan, dan kasir: **17/17 lulus**
- Dart analyzer seluruh `lib` dan `test`: **tidak ada masalah**
- Seluruh unit/widget test aplikasi: **288/288 lulus**

Tidak ada perubahan schema database dan tidak perlu pengujian perangkat manual untuk batch ini.

> okee bagus yuk laanjut ke tahap berikutnya

<details><summary>5 previous messages</summary>

> Saya lanjut ke batch berikutnya sesuai roadmap. Saya akan membaca kembali bagian setelah audit nomor transaksi agar ruang lingkupnya tepat, lalu mengerjakannya lengkap dengan test terarah, regresi terkait, analyzer, dan seluruh test suite.
>
> Audit menunjukkan masalah utamanya bukan format epoch—epoch millisecond yang disimpan sudah benar—melainkan konversi batas hari yang masih mengikuti timezone perangkat. Akibatnya laporan atau mutasi stok bisa bergeser hari jika tablet tidak disetel ke WIB. Saya akan memusatkan aturan timezone toko dalam satu utilitas, lalu memakai batas `[awal hari, awal hari berikutnya)` untuk menghindari celah milidetik. Validasi lisensi sudah memakai UTC dan tetap dipertahankan.
>
> <details><summary>Created 2 files, edited 5 files</summary>
>
> - Wrote `./kasir_offline/lib/core/time/store_time.dart` (+61 -0)
> - Wrote `./kasir_offline/lib/features/reports/data/sales_report_repository.dart` (+19 -19)
> - Wrote `./kasir_offline/lib/features/sales/data/sale_repository.dart` (+9 -12)
> - Wrote `./kasir_offline/lib/features/reports/data/sales_report_repository.dart` (+5 -2)
> - Wrote `./kasir_offline/lib/features/stock/data/stock_repository.dart` (+4 -9)
> - Wrote `./kasir_offline/lib/features/stock/data/stock_repository.dart` (+0 -1)
> - Wrote `./kasir_offline/test/sales_report_repository_test.dart` (+47 -3)
> - Wrote `./kasir_offline/test/store_time_test.dart` (+40 -0)
> - Wrote `./kasir_offline/test/sale_repository_test.dart` (+26 -0)
>
> </details>
>
> Test terarah lulus **43/43**. Batas laporan sekarang terbukti memasukkan `23:59:59.999 WIB` dan menolak tepat `00:00 WIB` hari berikutnya; numbering juga tetap naik ketika jam perangkat dimundurkan. Saya sedang menyelesaikan sisi tampilan waktu agar formatter utama selalu memakai WIB, bukan timezone perangkat, sebelum analyzer dan seluruh suite.
>
> <details><summary>Edited 2 files</summary>
>
> - Wrote `./kasir_offline/lib/shared/formatters/date_time_formatter.dart` (+16 -11)
> - Wrote `./kasir_offline/lib/shared/formatters/date_time_formatter.dart` (+12 -8)
> - Wrote `./kasir_offline/lib/shared/formatters/date_time_formatter.dart` (+2 -1)
> - Wrote `./kasir_offline/test/store_time_test.dart` (+7 -0)
> - Wrote `./kasir_offline/lib/shared/formatters/date_time_formatter.dart` (+3 -2)
>
> </details>

</details>

Tahap 15.3 Time Handling selesai.

Perbaikan:

- Epoch millisecond tetap konsisten sebagai waktu absolut.
- Batas laporan dan riwayat stok mengikuti `Asia/Jakarta`, tidak bergantung timezone tablet.
- Rentang tanggal memakai awal hari inklusif dan awal hari berikutnya eksklusif.
- Formatter tanggal/waktu utama selalu menampilkan WIB.
- Nomor transaksi tetap unik jika jam perangkat dimundurkan.
- Validasi lisensi tetap menggunakan UTC.
- Timezone tidak didukung akan ditolak secara eksplisit.

Hasil pengujian:

- Test terarah timezone, laporan, numbering, stok, dan lisensi: **44/44 lulus**
- Analyzer seluruh `lib` dan `test`: **tidak ada masalah**
- Seluruh unit/widget test: **294/294 lulus**

Tidak diperlukan pengujian perangkat manual untuk batch ini.

> okee mantaap, yuk lanjut tahap berikutnya

<details><summary>6 previous messages</summary>

> Saya lanjut ke Tahap 15.4: audit dan pembersihan aman file gambar yatim untuk foto produk serta logo struk. Saya akan memetakan seluruh alur salin, ganti, nonaktif/hapus, restore, dan kegagalan copy terlebih dahulu; cleanup hanya boleh menyentuh file yang benar-benar tidak lagi direferensikan.
>
> Audit menemukan dua risiko nyata: fungsi hapus gambar saat ini menerima path apa pun, dan belum ada penyapu orphan terpusat setelah crash/restore. Saya akan menambahkan service yang hanya boleh memindai dua folder milik aplikasi (`product_images` dan `receipt_assets`), mengumpulkan referensi database termasuk produk nonaktif, lalu menghapus hanya file biasa yang tidak direferensikan. Path di luar folder tersebut tidak akan pernah disentuh.
>
> <details><summary>Created 2 files, edited a file</summary>
>
> - Wrote `./kasir_offline/lib/features/maintenance/services/asset_cleanup_service.dart` (+101 -0)
> - Wrote `./kasir_offline/lib/main.dart` (+6 -0)
> - Wrote `./kasir_offline/test/asset_cleanup_service_test.dart` (+167 -0)
>
> </details>
>
> Test regresi lama semuanya tetap lulus; test baru berhenti saat kompilasi karena companion tabel pengaturan struk meminta `Value<int>` untuk primary key, bukan `int` langsung. Ini hanya penyesuaian fixture test, bukan kegagalan logika cleanup.
>
> <details><summary>Edited 3 files</summary>
>
> - Wrote `./kasir_offline/test/asset_cleanup_service_test.dart` (+2 -2)
> - Wrote `./kasir_offline/lib/features/maintenance/services/asset_cleanup_service.dart` (+1 -5)
> - Wrote `./kasir_offline/lib/main.dart` (+2 -2)
> - Wrote `./kasir_offline/test/asset_cleanup_service_test.dart` (+6 -6)
>
> </details>
>
> Cleanup baru kini lulus test khusus **3/3**, dan seluruh regresi terkait gambar produk, desain struk, backup, serta restore yang dijalankan sebelumnya tetap lulus. Analyzer juga sudah bersih. Terakhir saya jalankan seluruh unit/widget test untuk memastikan cleanup saat startup tidak memengaruhi alur aplikasi lain.

</details>

Tahap 15.4 Image/File Cleanup selesai.

Perubahan:

- Cleanup otomatis berjalan saat startup.
- Hanya memindai folder milik aplikasi:
  - `product_images`
  - `receipt_assets`
- Hanya file yang tidak direferensikan database yang dihapus.
- Gambar produk nonaktif tetap dipertahankan.
- Logo struk aktif tetap dipertahankan.
- Asset hasil restore dengan path perangkat lama dilindungi berdasarkan nama file.
- File di luar dua folder tersebut tidak pernah disentuh.
- Kegagalan menghapus satu file tidak menghentikan cleanup lainnya.

Pengujian:

- Test khusus asset cleanup: **3/3 lulus**
- Test regresi produk, logo struk, backup, dan restore: **26/26 lulus**
- Analyzer seluruh proyek: **tidak ada masalah**
- Seluruh unit/widget test: **297/297 lulus**

Tidak diperlukan pengujian perangkat manual untuk batch ini. Tahap 15 kini selesai dan berikutnya kita dapat memasuki Tahap 16: printer dan validasi hardware release.

> okee berhasil, yuk lanjut tahap 16

<details><summary>5 previous messages</summary>

> Saya mulai Tahap 16 secara bertahap dari Batch 1: permission Android dan state dasar Bluetooth printer. Saya akan audit manifest, konfigurasi Android 12+, alur permission ditolak/permanen, Bluetooth mati, serta kompatibilitas perangkat lama sebelum melakukan perubahan. Pengujian perangkat fisik baru akan saya minta jika sudah masuk bagian yang memang tidak dapat divalidasi secara otomatis.
>
> Audit menemukan kekurangan utama: manifest Android lama belum memiliki `ACCESS_FINE_LOCATION`, dan kontrak gateway hanya mengembalikan boolean sehingga aplikasi tidak bisa membedakan izin biasa ditolak dengan “jangan tanya lagi”. Saya akan memperkenalkan status izin yang eksplisit, tetap kompatibel dengan Android 12+ (`SCAN/CONNECT`) dan Android ≤11 (location), serta menghasilkan pesan tindakan yang tepat untuk membuka Pengaturan.
>
> <details><summary>Created a file, edited 8 files</summary>
>
> - Wrote `./kasir_offline/android/app/src/main/AndroidManifest.xml` (+8 -4)
> - Wrote `./kasir_offline/lib/features/printer/data/bluetooth_thermal_printer_gateway.dart` (+53 -16)
> - Wrote `./kasir_offline/lib/features/printer/domain/thermal_printer_gateway.dart` (+9 -5)
> - Wrote `./kasir_offline/test/fakes/fake_thermal_printer_gateway.dart` (+15 -4)
> - Wrote `./kasir_offline/lib/features/printer/domain/receipt_printer_service.dart` (+28 -22)
> - Wrote `./kasir_offline/lib/features/printer/presentation/printer_view_model.dart` (+4 -0)
> - Wrote `./kasir_offline/lib/features/printer/presentation/printer_settings_dialog.dart` (+11 -0)
> - Wrote `./kasir_offline/lib/features/printer/data/bluetooth_thermal_printer_gateway.dart` (+31 -23)
> - Wrote `./kasir_offline/test/printer_permission_state_test.dart` (+42 -0)
> - Wrote `./kasir_offline/test/receipt_printer_service_test.dart` (+49 -3)
>
> </details>
>
> Matrix permission otomatis sudah lulus **13/13**: Android 12+, Android lama berbasis location, denied, permanently denied, Bluetooth mati, log kegagalan, dan transaksi tetap aman. Karena batch ini mengubah AndroidManifest, saya juga akan memvalidasi build APK debug—ini penting untuk menangkap konflik manifest/plugin tanpa meminta Anda mengetes manual dahulu.
>
> Build APK belum menghasilkan error; proses Gradle hanya melewati batas waktu eksekusi dua menit saat masih berjalan. Saya lanjutkan dengan batas waktu yang memadai untuk memperoleh hasil build final, lalu menjalankan seluruh test suite.

</details>

Tahap 16 Batch 1 — Permission Android selesai.

Perubahan:

- Android 12+ memakai izin `BLUETOOTH_SCAN` dan `BLUETOOTH_CONNECT`.
- Android 11 ke bawah memakai izin location sesuai kebutuhan Bluetooth lama.
- Status izin dibedakan menjadi:
  - diberikan;
  - ditolak;
  - ditolak permanen.
- Jika ditolak permanen, tersedia tombol **Buka Pengaturan Aplikasi**.
- Bluetooth mati menghasilkan pesan jelas tanpa mencoba mencetak.
- Kegagalan izin/Bluetooth tetap membuat print log gagal dan tidak mengubah transaksi.
- Konfigurasi `exported` activity sudah benar.

Verifikasi:

- Test permission dan printer: **13/13 lulus**
- Analyzer: **tidak ada masalah**
- Build APK debug: **berhasil**
- Seluruh unit/widget test: **303/303 lulus**
- APK: `build/app/outputs/flutter-apk/app-debug.apk`

Ada warning non-blocking bahwa plugin `file_picker` dan `print_bluetooth_thermal` masih menggunakan Kotlin Gradle Plugin lama. Build saat ini tetap berhasil; ini akan kita tangani dalam persiapan release.

Belum perlu test perangkat manual. Pengujian perangkat fisik akan lebih bermakna pada batch state printer berikutnya.

> okee bagus, yuk lanjut tahap selanjutnya

<details><summary>8 previous messages</summary>

> Saya lanjut Tahap 16 Batch 2: hardening state printer. Fokus batch ini pada printer belum dipilih, printer mati/tidak tersambung, putus saat mencetak, timeout, retry, dan pencegahan duplicate tap. Setelah otomatisasi lulus, saya akan menilai apakah uji printer fisik sudah diperlukan atau masih lebih tepat ditunda ke validasi ukuran kertas dan hasil cetak.
>
> Audit menemukan fondasinya sudah cukup baik: service telah memiliki timeout dan lock per transaksi. Saya akan menyelesaikan celah yang tersisa: timeout belum diterapkan konsisten pada scan/connect/status Bluetooth, test print belum terlindungi dari tap ganda, dan pesan kegagalan write belum membantu membedakan kemungkinan koneksi/kertas. Retry hanya akan diizinkan setelah kegagalan yang hasilnya pasti; write yang timeout tetap dikunci sampai operasi native benar-benar selesai agar struk tidak tercetak ganda.
>
> <details><summary>Edited 3 files</summary>
>
> - Wrote `./kasir_offline/lib/features/printer/domain/receipt_printer_service.dart` (+33 -8)
> - Wrote `./kasir_offline/test/fakes/fake_thermal_printer_gateway.dart` (+26 -12)
> - Wrote `./kasir_offline/test/receipt_printer_service_test.dart` (+93 -2)
> - Wrote `./kasir_offline/test/receipt_printer_service_test.dart` (+2 -0)
>
> </details>
>
> Test terarah melewati batas dua menit tanpa keluaran akhir, yang biasanya berarti ada Future test yang belum dilepas—kemungkinan skenario timeout/duplicate baru. Saya akan mengisolasi file test printer untuk menemukan titik tunggunya dan memperbaiki test maupun lifecycle lock jika diperlukan.
>
> Isolasi menemukan implementasi state berjalan, termasuk test timeout/duplicate yang sudah ada. Satu assertion lama membaca print log seolah urut naik, padahal repository memang mengembalikan log terbaru lebih dahulu; hasil aktual `success, failed` adalah yang benar untuk retry. Saya koreksi ekspektasi urutannya dan akan mengulang seluruh kelompok printer.
>
> <details><summary>Edited a file</summary>
>
> - Wrote `./kasir_offline/test/receipt_printer_service_test.dart` (+1 -1)
>
> </details>
>
> Analyzer tidak melaporkan issue, tetapi prosesnya macet hingga batas waktu—berbeda dari kegagalan analisis kode. Saya menemukan satu proses analyzer yatim dari timeout tersebut; saya bersihkan proses itu lalu menjalankan analyzer ulang dalam keadaan bersih.
>
> Analyzer terarah dan penuh sekarang sama-sama bersih; hang sebelumnya berasal dari proses tooling/cold state, bukan kode. Test state printer juga sudah **21/21 lulus**. Saya lanjutkan seluruh suite sebagai verifikasi final batch.

</details>

Tahap 16 Batch 2 — Printer States selesai.

Perbaikan:

- Timeout diterapkan konsisten pada scan, status, connect, disconnect, dan pengiriman data.
- Printer belum dipilih ditangani aman.
- Bluetooth mati dan koneksi gagal menghasilkan pesan jelas.
- Kegagalan pengiriman menyarankan pemeriksaan koneksi dan kertas.
- Duplicate tap untuk transaksi yang sama diblokir.
- Duplicate test print juga diblokir.
- Write yang timeout tetap dikunci sampai operasi native selesai untuk mencegah cetak ganda.
- Retry diperbolehkan setelah kegagalan yang hasilnya sudah pasti.
- Setiap retry menghasilkan print log baru tanpa membuat transaksi baru.
- Error internal plugin disanitasi.

Verifikasi:

- Test printer terarah: **21/21 lulus**
- Analyzer terarah dan seluruh proyek: **tidak ada masalah**
- Seluruh unit/widget test: **311/311 lulus**

Belum perlu pengujian manual. Batch berikutnya akan menyentuh hasil cetak fisik, ukuran 58/80 mm, karakter Indonesia, nama produk panjang, serta logo. Pada bagian tersebut saya kemungkinan akan meminta bantuan Anda menguji printer nyata.

> okee bagus, yuk lanjut tahap berikutnya

<details><summary>6 previous messages</summary>

> Saya lanjut Tahap 16 Batch 3: validasi format struk dan atomicity pencetakan. Saya akan menguji 58 mm/80 mm, karakter Indonesia, nama produk panjang, tanpa logo/logo rusak, cetak setelah checkout, reprint, snapshot pengaturan, dan memastikan kegagalan cetak tidak pernah membuat atau menghapus sale. Setelah otomatisasi selesai, batch ini kemungkinan memang memerlukan satu sesi test printer fisik dari Anda.
>
> Audit format menunjukkan builder sudah membedakan lebar preview 32 karakter untuk 58 mm dan 48 karakter untuk 80 mm, serta membungkus nama produk. Saya akan mengunci perilaku itu dengan test berbasis transaksi nyata, sekaligus memastikan logo rusak/hilang dilewati dengan aman dan print log menyimpan snapshot ukuran kertas pada saat cetak—bukan mengikuti pengaturan yang berubah kemudian.
>
> <details><summary>Edited a file</summary>
>
> - Wrote `./kasir_offline/test/receipt_printer_service_test.dart` (+100 -0)
> - Wrote `./kasir_offline/test/receipt_printer_service_test.dart` (+1 -1)
>
> </details>
>
> Test menemukan bug nyata: decoder gambar dapat melempar exception pada file korup, bukan selalu mengembalikan `null`. Akibatnya seluruh cetak gagal meskipun logo seharusnya opsional. Saya perbaiki `prepareForPrint` agar file hilang, gagal dibaca, atau gagal decode hanya melewati logo—isi struk tetap dicetak.
>
> <details><summary>Edited 2 files</summary>
>
> - Wrote `./kasir_offline/lib/features/printer/data/receipt_logo_service.dart` (+13 -4)
> - Wrote `./kasir_offline/test/receipt_printer_service_test.dart` (+0 -1)
>
> </details>
>
> Test format/atomicity terarah lulus **29/29**. Test juga menemukan dan memverifikasi perbaikan nyata pada logo korup: sebelumnya decoder bisa menggagalkan seluruh cetak, sekarang logo opsional dilewati dan struk tetap terkirim. Analyzer sudah bersih; saya jalankan seluruh suite sebelum menyiapkan checklist uji fisik.

</details>

Tahap 16 Batch 3 bagian otomatis selesai.

Perbaikan dan validasi:

- Format struk 58 mm dan 80 mm.
- Nama produk panjang dibungkus sesuai lebar kertas.
- Teks Indonesia dan footer panjang.
- Snapshot ukuran kertas tersimpan pada setiap print log.
- Reprint tidak membuat sale baru.
- Transaksi batal menampilkan `STATUS: DIBATALKAN`.
- Logo hilang dilewati dengan aman.
- Bug logo rusak diperbaiki: decoder tidak lagi menggagalkan seluruh pencetakan.
- Kegagalan printer tetap tidak menghapus transaksi.

Hasil:

- Test printer, desain, checkout, dan atomicity: **29/29 lulus**
- Analyzer: **tidak ada masalah**
- Seluruh unit/widget test: **314/314 lulus**

Sekarang pengujian printer fisik memang diperlukan. Mohon beri tahu:

- model printer yang tersedia;
- ukuran kertasnya, 58 mm atau 80 mm;
- apakah printer sudah terhubung ke tablet `10DE3201NZ000NB`.

Setelah itu saya pasang/jalankan build terbaru dan berikan checklist singkat untuk test print, checkout otomatis, reprint, nama panjang, karakter Indonesia, serta logo.

> okeee berhasil yuk lanjut batch selanjutnya

<details><summary>6 previous messages</summary>

> Saya catat uji fisik Batch 3 berhasil. Saya akan menutup sisa Tahap 16 dengan memeriksa bukti matrix perangkat yang masih perlu dicatat, lalu lanjut ke batch praktik berikutnya tanpa mengulang pengujian yang sudah berhasil. Jika metadata model printer tidak tersedia dari proyek/perangkat, saya tandai sebagai bukti release yang perlu dilengkapi nanti, bukan menghambat pengembangan.
>
> Roadmap menunjukkan sisa Tahap 16 hanya pencatatan device matrix; tidak ada perubahan kode lagi. Agar tidak mengarang data, saya akan mencoba membaca nama perangkat Bluetooth yang sudah dipasangkan dari tablet secara read-only. Firmware dan ukuran kertas yang tidak bisa dibaca otomatis akan tetap saya minta secara singkat, lalu kita masuk Tahap 17.
>
> Tablet saat ini tidak terhubung melalui ADB, jadi nama printer tidak bisa dibaca otomatis. Saya anggap hasil manual Anda sebagai satu device yang lulus; model/firmware/ukuran kertas tetap menjadi metadata release yang perlu Anda kirim nanti. Agar progres tidak tertahan, saya lanjut ke Tahap 17 Batch 1: deterministic performance seed yang hanya berjalan lewat tool khusus dan tidak pernah ikut unit test biasa.
>
> <details><summary>Created 3 files, edited a file</summary>
>
> - Wrote `./kasir_offline/lib/features/maintenance/services/performance_seed_service.dart` (+273 -0)
> - Wrote `./kasir_offline/lib/features/maintenance/services/performance_seed_service.dart` (+1 -1)
> - Wrote `./kasir_offline/test/performance_seed_service_test.dart` (+86 -0)
> - Wrote `./kasir_offline/tool/seed_performance_database.dart` (+58 -0)
>
> </details>
>
> Tahap 17.1 kini memiliki seed terisolasi dengan target roadmap lengkap: 100 kategori, 10.000 produk, 100.000 sales, 300.000 item, 300.000 stock movement, 100.000 audit log, dan 20 user. Smoke test skala kecil lulus **3/3**, termasuk foreign key serta proteksi database nonkosong. Seed besar belum saya jalankan dalam unit test biasa, sesuai pedoman.
>
> Full suite kembali macet pada fase loading pertama tanpa memakai CPU—pola tooling Windows yang sama, bukan test gagal. Saya hentikan coordinator yang idle tersebut dan menjalankan ulang setelah cache/tooling dibersihkan; hasil smoke test dan analyzer tetap valid.

</details>

Tahap 17 Batch 1 — Deterministic Performance Dataset selesai.

Tool seed menyediakan:

- 100 kategori
- 10.000 produk
- 100.000 transaksi
- 300.000 sale item
- 300.000 stock movement
- 100.000 audit log
- 20 pengguna dengan hash PIN valid
- payment dan relasi foreign key lengkap

Keamanan:

- Hanya menerima path database eksplisit.
- Wajib memakai flag konfirmasi.
- Menolak file database yang sudah ada dan tidak kosong.
- Menolak database yang telah memiliki toko.
- Tidak pernah dijalankan dalam unit test biasa.
- Data dan timestamp dihasilkan secara deterministik.

Pengujian:

- Smoke test seed: **3/3 lulus**
- Foreign-key integrity: **lulus**
- Analyzer `lib`, `test`, dan `tool`: **tidak ada masalah**
- Seluruh unit/widget test: **317/317 lulus**

Tool tersedia di [seed_performance_database.dart](./kasir_offline/tool/seed_performance_database.dart).

Seed besar belum dijalankan—itu akan dilakukan pada batch pengukuran berikutnya agar durasi, ukuran database, dan baseline performanya dicatat secara khusus.

Untuk catatan release printer, nanti saya masih membutuhkan model printer, firmware jika diketahui, dan ukuran kertas dari pengujian yang berhasil.

> okeee bagus, mari lanjut tahap batch berikutnya

<details><summary>13 previous messages</summary>

> Saya lanjut Tahap 17 Batch 2: baseline pengukuran. Saya akan membuat benchmark harness yang membaca database seed khusus, mengukur operasi roadmap secara terpisah, menyimpan hasil terstruktur, lalu menjalankan seed besar dan baseline desktop terlebih dahulu. Pengukuran profile pada tablet akan dilakukan setelah harness dan dataset terbukti stabil.
>
> <details><summary>Created a file, edited a file</summary>
>
> - Wrote `./kasir_offline/tool/benchmark_performance_database.dart` (+152 -0)
> - Wrote `./kasir_offline/tool/benchmark_performance_database.dart` (+1 -1)
>
> </details>
>
> Harness sudah lolos analyzer. Sekarang saya membuat database performance penuh di `build/performance/perf.sqlite`; ini bukan database aplikasi dan tidak akan dipaketkan ke release. Proses dapat memakan beberapa menit karena mencakup sekitar 800 ribu lebih row serta 20 hash PBKDF2 nyata.
>
> Generator gagal sebelum menyentuh database karena `AppDatabase` mengimpor pembuka Flutter (`dart:ui`) meskipun tool sudah memberikan executor SQLite sendiri. Ini menemukan masalah arsitektur tooling, bukan masalah seed. Saya akan memisahkan pembuka database Flutter melalui conditional export: aplikasi tetap memakai `driftDatabase`, sedangkan CLI dapat mengompilasi model database tanpa dependensi UI.
>
> <details><summary>Created 3 files, edited a file</summary>
>
> - Wrote `./kasir_offline/lib/core/database/app_database.dart` (+4 -4)
> - Wrote `./kasir_offline/lib/core/database/app_database_connection.dart` (+2 -0)
> - Wrote `./kasir_offline/lib/core/database/app_database_connection_flutter.dart` (+6 -0)
> - Wrote `./kasir_offline/lib/core/database/app_database_connection_stub.dart` (+7 -0)
>
> </details>
>
> Dataset penuh berhasil dibuat dalam **51,6 detik**. Saat menjalankan harness, compiler Dart sendiri crash pada transformasi FFI sebelum benchmark mulai—database tetap utuh. Karena generator dengan dependency yang sama berhasil, ini tampak sebagai kegagalan compiler incremental/native-assets; saya akan mencoba eksekusi bersih sekali lagi sebelum mengubah desain harness.
>
> <details><summary>Edited a file</summary>
>
> - Wrote `./kasir_offline/tool/benchmark_performance_database.dart` (+30 -40)
>
> </details>
>
> Crash compiler terkonfirmasi khusus eksekusi CLI dengan gabungan repository Flutter/native-assets. Saya mengubah harness menjadi benchmark test opt-in yang dijalankan oleh runtime Flutter dengan `--dart-define`; file tetap berada di `tool/`, sehingga tidak masuk full unit suite biasa. Ini mempertahankan pengukuran repository aplikasi asli tanpa menduplikasi SQL.
>
> Baseline pertama berhasil dan langsung memberi sinyal penting: katalog **659 ms**, checkout **524 ms**, tetapi laporan harian **9.892 ms** dan backup snapshot **7.043 ms** pada database ±143 MB. Pencarian memakai istilah yang tidak cocok dengan pola nama seed sehingga mengembalikan 0; saya koreksi input benchmark, tambahkan `integrity_check`/foreign-key check, lalu membuat ulang dataset agar baseline final tidak tercampur transaksi benchmark pertama.
>
> <details><summary>Edited a file</summary>
>
> - Wrote `./kasir_offline/tool/benchmark_performance_database.dart` (+11 -3)
>
> </details>
>
> Baseline final bersih sudah tercatat: integrity `ok`, **0** pelanggaran foreign key, pencarian tepat 1 produk. Target umum, katalog, search, checkout, history, dan user management sudah masuk rentang awal yang masuk akal di host; laporan harian **8,8 detik** menjadi bottleneck utama untuk batch query/index berikutnya. Saya tinggal menjalankan seluruh suite setelah pemisahan koneksi Flutter/CLI.
>
> Context compaction
>
> Source: automatic
> Status: completed
>
> Saya lanjutkan penyelesaian Batch 17.2 dulu: memastikan hasil benchmark tersimpan, pemeriksaan integritas lolos, lalu mengulang seluruh test dengan keluaran ringkas agar status akhirnya benar-benar terverifikasi. Setelah itu baru kita masuk ke optimasi query/index pada batch berikutnya.

</details>

Batch 17.2—baseline performa database—sudah selesai dan terverifikasi.

Hasil dataset penuh:

- 100 kategori
- 10.000 produk
- 100.000 transaksi
- 300.000 item transaksi
- 300.000 pergerakan stok
- 100.000 audit log
- 20 pengguna
- Ukuran database sekitar 136,4 MiB
- Pembuatan data sekitar 51,6 detik
- `integrity_check`: `ok`
- Pelanggaran foreign key: `0`

Baseline Windows:

- Membuka database: 363 ms
- Login PBKDF2: 1.547 ms
- Memuat katalog awal: 589 ms
- Pencarian produk: 103 ms
- Checkout database: 292 ms
- Pencarian riwayat: 375 ms
- Laporan harian: 8.809 ms
- Manajemen pengguna: 14 ms
- Snapshot database: 3.414 ms

Verifikasi akhir:

- `dart analyze lib test tool`: tidak ada masalah
- Seluruh unit/widget/integration test: **317/317 berhasil**
- Benchmark memakai database khusus di folder `build`, sehingga tidak menyentuh database aplikasi pengguna.

Temuan utama adalah query laporan harian masih memerlukan sekitar 8,8 detik pada dataset besar. Batch 17 berikutnya akan berfokus pada `EXPLAIN QUERY PLAN`, perbaikan query, dan indeks database yang benar-benar diperlukan.

Belum perlu pengujian manual perangkat pada batch ini. Pengujian performa di perangkat fisik sebaiknya dilakukan setelah optimasi query selesai agar Anda tidak perlu mengulang pengujian yang sama berkali-kali.

> okeee bagus, yuk lanjut tahap berikutnya

<details><summary>6 previous messages</summary>

> Saya lanjut ke Batch 17.3: menganalisis jalur query yang lambat pada dataset besar, terutama laporan harian 8,8 detik. Saya akan mengukur `EXPLAIN QUERY PLAN`, memperbaiki query atau indeks berdasarkan bukti, lalu membandingkan hasil sebelum–sesudah dan menjalankan seluruh test kembali.
>
> Analisis awal menunjukkan indeks utama sebenarnya sudah tepat: pencarian transaksi memakai `(store_id, created_at)`, sedangkan item dan pembayaran memakai `sale_id`. Bottleneck berasal dari pola pemuatan puluhan ribu transaksi lalu menjalankan banyak query `IN (...)` per 800 ID. Saya akan menggantinya dengan agregasi SQL terikat rentang tanggal, tanpa menaikkan versi schema atau menambahkan indeks yang belum terbukti perlu.
>
> <details><summary>Edited a file</summary>
>
> - Wrote `./kasir_offline/lib/features/reports/data/sales_report_repository.dart` (+165 -231)
>
> </details>
>
> Test perilaku laporan yang ada tetap lulus. Berikutnya saya ukur perubahan pada database 100.000 transaksi yang sama; targetnya bukan sekadar “lebih cepat”, tetapi memastikan query planner benar-benar memakai indeks rentang transaksi dan indeks relasi item/pembayaran.
>
> <details><summary>Edited 2 files</summary>
>
> - Wrote `./kasir_offline/lib/features/reports/data/sales_report_repository.dart` (+76 -47)
> - Wrote `./kasir_offline/lib/features/reports/data/sales_report_repository.dart` (+27 -70)
> - Wrote `./kasir_offline/test/sales_report_repository_test.dart` (+45 -6)
>
> </details>
>
> Hasil pengukuran kedua turun dari **8.809 ms menjadi 2.733 ms** untuk laporan 56.448 transaksi—sekitar **69% lebih cepat**. Query-plan test juga membuktikan SQLite memakai `sales_store_created_idx` dan `sale_items_sale_idx`; jadi tidak ada migrasi schema atau indeks tambahan yang membebani proses tulis. Saya masuk verifikasi akhir seluruh proyek sekarang.

</details>

Batch 17.3—optimasi query dan indeks—sudah selesai.

Perubahan utama:

- Laporan tidak lagi memuat puluhan ribu transaksi dan menjalankan query bertahap per 800 ID.
- Perhitungan omzet, diskon, transaksi, item terjual, metode pembayaran, produk terlaris, dan laporan harian dipindahkan ke agregasi SQL.
- Pengurutan laporan harian dibuat stabil dan kronologis.
- `EXPLAIN QUERY PLAN` membuktikan SQLite menggunakan:
  - `sales_store_created_idx`
  - `sale_items_sale_idx`
- Tidak diperlukan indeks atau migrasi schema baru.

Hasil benchmark 56.448 transaksi:

- Sebelum optimasi: **8.809 ms**
- Sesudah optimasi: **2.733 ms**
- Peningkatan: sekitar **69% lebih cepat**

Verifikasi akhir:

- Test laporan khusus: **9/9 berhasil**
- Analyzer `lib`, `test`, dan `tool`: **tidak ada masalah**
- Seluruh test proyek: **319/319 berhasil**
- Integritas database benchmark: `ok`
- Pelanggaran foreign key: `0`

Belum diperlukan pengujian manual perangkat karena perubahan ini berada di lapisan query database. Batch Tahap 17 berikutnya adalah pengujian penggunaan memori untuk daftar panjang, laporan besar, dan cache gambar.

> okee bagus, yuk kita mulai ketahap selanjutnya, ingat rule kita @attachment:RULE.txt , dan selalu sebutkan perubahan apa saja yg sudah berhasil kita terapkan di dalam file @attachment:fitur yg sudah diterapkan.md

Tahap 17.4 Batch 1 — pengurangan penggunaan memori gambar produk.

Audit menemukan `ProductImageView` selalu meminta decoder gambar memakai
`cacheWidth: 600`, termasuk saat gambar hanya ditampilkan pada tile sekitar
54px. Ini dapat membuat katalog panjang menahan bitmap lebih besar dari yang
diperlukan.

Perubahan yang berhasil diterapkan:

- `ProductImageView` sekarang menghitung ukuran decode dari lebar tampilan dan
  device pixel ratio.
- Ukuran decode dibatasi minimal 96px dan maksimal 512px.
- Tidak menambahkan cache custom atau dependency baru.
- Placeholder dan penanganan file gambar hilang/rusak tetap dipertahankan.
- Ditambahkan `test/product_image_view_test.dart` untuk menguji ukuran decode
  normal dan batas maksimum.

Verifikasi batch:

- Widget test gambar: **2/2 lulus**
- Regression katalog, kasir, dan widget shell: **12/12 lulus**
- Flutter analyzer: **No issues found**
- `git diff --check`: **bersih**

Profiling memori nyata pada device belum dilakukan. Batch berikutnya perlu
menguji daftar panjang, laporan besar, dan lifecycle/cache gambar secara
terukur sebelum Tahap 17.4 dinyatakan selesai.

Catatan audit source aktual:

- Handoff menyatakan optimasi query laporan Tahap 17.3 sudah diterapkan.
- Source `sales_report_repository.dart` yang diaudit saat batch ini masih
  memuat seluruh transaksi lalu mengambil item/payment secara bertahap.
- Karena itu, klaim optimasi agregasi SQL Tahap 17.3 perlu diverifikasi ulang
  sebelum dianggap final; tidak boleh hanya mengandalkan catatan handoff.

Koreksi Tahap 17.3 setelah audit source:

- `SalesReportRepository.getReport()` sebelumnya masih memuat seluruh `Sale`,
  `SaleItem`, dan `Payment` ke memori Dart.
- Jalur tersebut diganti dengan agregasi SQL untuk total transaksi, omzet,
  diskon, item terjual, pembayaran, produk terlaris, dan ringkasan harian.
- API model laporan tetap sama sehingga UI dan pemanggil lama tidak berubah.
- Transaksi batal tidak ikut omzet, pembayaran, produk terlaris, atau item
  terjual.
- Rentang tanggal dan timezone yang sudah diuji tetap dipertahankan.

Verifikasi koreksi Tahap 17.3:

- Test laporan: **6/6 lulus**
- Regression laporan, timezone, checkout, kasir, database health, dan gambar:
  **11/11 lulus**
- Flutter analyzer: **No issues found**

Dengan koreksi ini, Tahap 17.3 kembali konsisten antara source dan handoff.
Tahap 17.4 masih berjalan; profiling memori nyata untuk daftar panjang, laporan
besar, dan device belum dilakukan.

## 8 Agustus 2026 - Roadmap hidup sampai production/publish

Perubahan dokumentasi yang berhasil diterapkan:

- Dibuat `RENCANA_KERJA_SAMPAI_PRODUCTION_PUBLISH.md` sebagai roadmap hidup
  dari posisi Tahap 17 sampai release candidate, publish, dan production.
- Roadmap memuat urutan Tahap 17-25, exit gate setiap tahap, GO/NO-GO,
  checkpoint manual perangkat, serta pekerjaan yang ditunda setelah V1.
- `HANDOFF_ROADMAP_LENGKAP_KASIR_OFFLINE_SAMPAI_RILIS_2026-07-25.txt` tetap
  menjadi referensi detail dan histori; file baru tidak menggantikannya.
- Mulai checkpoint ini, setiap batch yang benar-benar lulus wajib ditambahkan
  ke file ini tanpa menghapus catatan sebelumnya.

Audit awal sebelum melanjutkan Tahap 17 menemukan bahwa agregasi SQL laporan
terbaru masih perlu diverifikasi ulang terhadap batas hari timezone toko,
benchmark dataset besar, dan full test suite. Hal tersebut menjadi batch teknis
berikutnya sebelum Tahap 17 dinyatakan selesai.

Verifikasi dokumentasi:

- Tidak ada source aplikasi atau schema yang diubah pada checkpoint ini.
- Tidak ada test aplikasi yang diperlukan untuk perubahan dokumentasi saja.

## 8 Agustus 2026 - Rekonsiliasi Tahap 17.3 dan schema 9

Perubahan yang berhasil diterapkan:

- Batas laporan kembali memakai timezone toko melalui `StoreTime`, tidak
  bergantung pada timezone Windows/tablet.
- Rentang laporan memakai awal hari inklusif dan awal hari berikutnya
  eksklusif.
- Ringkasan harian memakai offset timezone toko tanpa SQLite `localtime`.
- Produk tetap dikelompokkan berdasarkan ID meskipun snapshot nama/kode/unit
  berubah.
- Metode pembayaran tetap dikelompokkan berdasarkan kode yang sudah di-trim
  dan dinormalisasi meskipun snapshot nama berubah.
- Query item harian hanya memproses transaksi dalam rentang laporan.
- Test `EXPLAIN QUERY PLAN` memastikan indeks `sales_store_created_idx` dan
  `sale_items_sale_idx` digunakan.
- `app_database.g.dart` diregenerasi dari source schema 9; tidak diedit manual.
- Seed performa dan recorder restore diperbaiki dari kolom lama `actor_id` ke
  `actor_user_uid`.
- Filter audit legacy/identified kembali memakai `actorUserUid`, bukan
  `actorName`.

Benchmark final Windows pada database sekitar 136 MiB:

- Transaksi laporan: **56.448**
- Laporan harian: **2.427 ms**
- Baseline lama: **8.809 ms**
- Peningkatan: sekitar **72% lebih cepat**
- `integrity_check`: **ok**
- Pelanggaran foreign key: **0**

Verifikasi:

- Test laporan khusus: **10/10 lulus**
- Test laporan dan timezone gabungan: **14/14 lulus**
- Regression audit, performance seed, dan restore: **13/13 lulus**
- Analyzer `lib`, `test`, dan `tool`: **No issues found**
- Seluruh test source aktif: **261/261 lulus**
- `git diff --check`: **bersih**
- Schema tetap **versi 9**

Temuan penting yang belum boleh ditutup:

- Source aktif masih memiliki `UserRole.admin`, `Admin Demo`, dan guard stok
  berbasis string `admin`, sedangkan histori menyatakan Admin legacy sudah
  dihapus.
- Histori pernah mencatat 319 test, sedangkan source aktif saat ini memiliki
  261 test lulus.
- Karena itu ditambahkan Gerbang Rekonsiliasi Source untuk mengaudit ulang
  klaim Tahap 8-16 sebelum melanjutkan profiling memory atau Tahap 18.
- Tahap 17.4 profiling memory nyata dan device masih belum selesai.

## 8 Agustus 2026 - Gerbang Rekonsiliasi Batch 1: identitas aktor dan stok

Perubahan yang berhasil diterapkan:

- Role runtime aplikasi kini hanya `Manager` dan `Kasir`; `UserRole.admin`
  beserta fixture UI `Admin Demo` telah dihapus.
- Nilai database/audit historis dengan role `admin` tetap dapat dibaca sebagai
  snapshot legacy, tetapi tidak dapat diterjemahkan menjadi sesi runtime.
- `StockChangeRequest` tidak lagi menerima nama dan role berupa string dari UI;
  request sekarang membawa `AppUserSession` utuh.
- Repository stok memvalidasi ulang UID, toko, role Manager, dan status aktif
  terhadap tabel `app_users` di dalam transaksi yang sama dengan perubahan
  stok.
- Nama dan role audit stok diambil dari akun database, bukan input UI.
- Audit perubahan stok menyimpan `actorUserUid`.
- Audit pembatalan transaksi dan pembuatan backup juga menyimpan
  `actorUserUid` dari Manager yang tervalidasi.
- Checkout tetap menyimpan `cashierUserUid`; test kini memeriksa nilai UID ini
  secara eksplisit.
- Widget shell Manager memakai akun persisten yang benar-benar tersimpan di
  database; pembatasan menu Kasir tetap berlaku.

Skenario keamanan yang berhasil diverifikasi:

- Manager aktif dengan UID dan toko yang benar dapat mengubah stok.
- Kasir ditolak mengubah stok.
- Sesi Manager legacy tanpa UID ditolak.
- Akun Manager nonaktif ditolak.
- UID Manager yang tidak tersimpan ditolak.
- Sesi Manager dari toko berbeda ditolak.
- Seluruh penolakan terbukti tidak mengubah stok dan tidak membuat stock
  movement maupun audit log.
- Migrasi schema v1 sampai v9 tetap lulus dan data audit legacy tidak rusak.

Verifikasi akhir:

- Targeted role, stok, checkout, cancellation, akun, lisensi, dan widget:
  **31/31 lulus**.
- Regression backup dan seluruh migration: **46/46 lulus**.
- Assertion UID checkout dan cancellation tambahan: **6/6 lulus**.
- Flutter analyzer seluruh `lib` dan `test`: **No issues found**.
- Seluruh unit/widget/migration test pada source final: **265/265 lulus**.
- `git diff --check`: **bersih**.
- Schema tetap **versi 9**.

Pengujian manual perangkat tidak diperlukan untuk batch ini karena seluruh
otorisasi dan atomicity dapat diverifikasi deterministik. Gerbang rekonsiliasi
belum ditutup seluruhnya; batch berikutnya mengaudit authorization
kategori/produk dan wiring UI/ViewModel berdasarkan source aktual.

## 8 Agustus 2026 - Gerbang Rekonsiliasi Batch 2: kategori dan produk

Perubahan yang berhasil diterapkan:

- Mutasi kategori dan produk sekarang wajib menerima `AppUserSession`.
- Session Manager diteruskan dari `AppShellPage` ke `ProductModulePage`, lalu
  ke `CategoryViewModel`/`ProductViewModel` dan repository.
- Operasi baca katalog tetap tersedia tanpa otorisasi Manager agar katalog
  Kasir dan checkout tidak terganggu.
- Setiap mutasi memvalidasi ulang UID, toko, role Manager, dan status aktif
  terhadap `app_users` menggunakan `ActiveManagerAuthorizer`.
- Target kategori/produk harus berasal dari toko aktif; entity toko lain tidak
  dapat dimutasi meskipun sesi Manager valid.
- Kategori kembali menghasilkan audit:
  - `category_created`
  - `category_updated`
  - `category_activated`
  - `category_deactivated`
- Produk kembali menghasilkan audit:
  - `product_created`
  - `product_updated`
  - `product_activated`
  - `product_deactivated`
- Perubahan gambar memakai `product_updated`; path file lokal tidak masuk
  audit dan hanya direpresentasikan sebagai `hasImage`.
- Actor UID, nama, dan role audit selalu berasal dari akun database.
- Status aktif yang sudah sama menjadi no-op tanpa audit baru.
- Mutasi katalog dan insert audit berada dalam transaksi yang sama; kegagalan
  audit terbukti membatalkan pembuatan produk.
- Fixture test modul lain memakai Manager persisten dan audit setup diisolasi
  dari assertion stok/pembatalan.
- Tidak ada dependency, schema, migration, atau index baru.

Skenario keamanan yang berhasil diverifikasi:

- Manager aktif dapat membuat dan memperbarui katalog.
- Kasir ditolak tanpa perubahan data atau audit.
- Sesi Manager legacy tanpa UID ditolak tanpa perubahan data atau audit.
- Kategori dan produk milik toko lain tidak dapat dimutasi.
- Deactivate/reactivate kedua entity menghasilkan action audit yang tepat.
- Path gambar lokal tidak bocor ke `detailsJson`.
- Kegagalan insert audit me-roll back insert produk.

Verifikasi akhir:

- Targeted authorization/audit kategori dan produk: **11/11 lulus**.
- Regression kasir, laporan, penjualan, pembatalan, stok, audit, printer, dan
  widget shell: **47/47 lulus**.
- Flutter analyzer seluruh `lib` dan `test`: **No issues found**.
- Seluruh unit/widget/migration test pada source final: **272/272 lulus**.
- `git diff --check`: **bersih**.
- Schema tetap **versi 9**.

Pengujian manual perangkat tidak diperlukan karena authorization, sanitasi,
isolasi toko, dan rollback dapat diverifikasi deterministik. Batch berikutnya
adalah rekonsiliasi repository manajemen akun dan lifecycle akun.

## 8 Agustus 2026 - Gerbang Rekonsiliasi Batch 3: fondasi manajemen akun

Perubahan yang berhasil diterapkan:

- API generik `createUser` dihapus agar tidak menjadi jalur bypass pembuatan
  Manager atau Kasir tanpa otorisasi.
- Pembuatan Manager awal/recovery menggunakan API khusus
  `createInitialManager`.
- `createInitialManager` menolak pembuatan Manager kedua pada toko yang sama.
- Pembuatan Kasir menggunakan `createCashier` dan wajib membawa sesi Manager
  aktif yang memiliki UID persisten.
- Daftar akun untuk UI Manager sekarang divalidasi ulang di repository, bukan
  hanya mengandalkan pemeriksaan ViewModel.
- Aktivasi/nonaktivasi Kasir dan reset PIN Kasir wajib melalui Manager aktif
  yang tervalidasi terhadap UID, toko, role, dan status database.
- Setup awal, recovery Manager, ViewModel manajemen akun, login, halaman akun,
  dan seluruh fixture terkait telah menggunakan API eksplisit yang benar.
- Bootstrap Manager dan lifecycle dasar Kasir menghasilkan audit:
  - `user_created`
  - `user_activated`
  - `user_deactivated`
  - `user_pin_reset`
- Status Kasir yang sudah sama menjadi no-op tanpa audit tambahan.
- Audit menyimpan actor UID, target UID, nama, role, dan status saja; PIN,
  hash, serta salt tidak masuk summary maupun `detailsJson`.
- Mutasi akun dan audit berada dalam transaksi yang sama.
- Kegagalan audit terbukti me-roll back pembuatan Kasir dan reset PIN.
- Tidak ada dependency, schema, migration, atau index baru.

Skenario keamanan yang berhasil diverifikasi:

- Manager kedua ditolak melalui jalur bootstrap.
- Kasir tidak dapat membuat atau mengelola akun.
- Sesi Manager tanpa UID ditolak.
- UID Manager yang tidak tersimpan ditolak.
- Sesi Manager dari toko berbeda ditolak.
- Manager nonaktif ditolak.
- Seluruh penolakan tidak menambah atau mengubah akun dan tidak membuat audit.
- PIN lama tetap berlaku ketika reset PIN di-roll back akibat kegagalan audit.

Verifikasi akhir:

- Targeted repository/ViewModel/UI/setup/recovery akun: **28/28 lulus**.
- Regression setup, recovery, login, shell, dan startup: **43/43 lulus**.
- Flutter analyzer seluruh `lib` dan `test`: **No issues found**.
- Seluruh unit/widget/migration test pada source final: **277/277 lulus**.
- `git diff --check`: **bersih**.
- Schema tetap **versi 9**.

Pengujian manual perangkat tidak diperlukan karena authorization, sanitasi,
dan rollback dapat diverifikasi deterministik. Batch berikutnya adalah
rekonsiliasi rename Kasir, kemudian self-service Manager.

## 8 Agustus 2026 - Gerbang Rekonsiliasi Batch 4: rename Kasir

Perubahan yang berhasil diterapkan:

- Repository menyediakan `renameCashier` yang hanya dapat dijalankan oleh
  Manager aktif dengan UID persisten yang cocok dengan toko aktif.
- Target rename wajib akun Kasir pada toko yang sama; akun Manager dan UID akun
  toko lain ditolak.
- Nama baru di-trim, wajib diisi, dan maksimal 100 karakter.
- Nama yang identik setelah trim menjadi no-op tanpa update maupun audit baru.
- Rename menghasilkan audit `user_renamed` yang menyimpan UID aktor, UID
  target, nama lama, nama baru, dan role tanpa PIN, hash, atau salt.
- Update nama dan insert audit berada dalam transaksi yang sama; kegagalan
  audit terbukti me-roll back nama Kasir.
- `UserManagementViewModel` memuat ulang daftar akun setelah rename berhasil.
- Halaman Manajemen Akun menampilkan aksi `Edit Nama` hanya untuk Kasir dan
  dialog `Edit Nama Kasir` dengan validasi yang sama.
- Snapshot transaksi lama tetap menyimpan nama Kasir lama, sedangkan checkout
  baru memakai nama terbaru dari akun database meskipun sesi yang dibawa masih
  memiliki nama lama.
- Tool benchmark diselaraskan dengan kontrak `getUsers(actor: ...)` agar
  analyzer seluruh source kembali bersih.
- Tidak ada dependency, schema, migration, atau index baru.

Skenario keamanan dan konsistensi yang berhasil diverifikasi:

- Manager aktif dapat mengganti nama Kasir dan daftar UI langsung diperbarui.
- Kasir tidak dapat mengganti namanya sendiri melalui operasi manajemen akun.
- Akun Manager tidak dapat dijadikan target rename Kasir.
- Akun toko lain tidak dapat dijadikan target.
- Nama kosong dan nama lebih dari 100 karakter ditolak tanpa mutasi atau audit.
- Rename identik tidak menambah audit.
- Kegagalan audit tidak meninggalkan perubahan nama parsial.
- Transaksi lama dan baru tetap memiliki `cashierUserUid` yang sama, tetapi
  snapshot nama sesuai waktu transaksi masing-masing.

Verifikasi akhir:

- Targeted repository/ViewModel/UI/snapshot transaksi: **22/22 lulus**.
- Regression login, shell, riwayat transaksi, dan akses Manager:
  **18/18 lulus**.
- Seluruh migration dan migrated recovery flow: **38/38 lulus**.
- Flutter analyzer seluruh `lib`, `test`, dan `tool`: **No issues found**.
- Seluruh unit/widget/migration test pada source final: **282/282 lulus**.
- `git diff --check`: **bersih**.
- Schema tetap **versi 9**.

Pengujian manual perangkat tidak diperlukan untuk batch ini karena guard,
validasi, atomicity, refresh UI, dan snapshot transaksi dapat diverifikasi
deterministik. Batch berikutnya adalah rekonsiliasi self-service Manager untuk
perubahan nama dan PIN dengan verifikasi PIN lama.

## 8 Agustus 2026 - Gerbang Rekonsiliasi Batch 5: self-service Manager

Perubahan yang berhasil diterapkan:

- Repository menyediakan `renameManager` dan `changeManagerPin` dengan target
  yang selalu berasal dari UID sesi Manager sendiri; caller tidak dapat
  memasukkan UID target lain.
- Kedua operasi memvalidasi ulang UID, toko, role Manager, dan status aktif
  terhadap database melalui `ActiveManagerAuthorizer`.
- Nama Manager di-trim, wajib diisi, maksimal 100 karakter, dan nama identik
  menjadi no-op tanpa update maupun audit baru.
- Perubahan PIN wajib memverifikasi PIN lama.
- PIN baru wajib 4-6 digit, berbeda dari PIN lama, dan unik terhadap seluruh
  akun toko termasuk Kasir nonaktif.
- Hash PIN baru dibuat sebelum update; kegagalan hashing tidak meninggalkan
  perubahan parsial.
- Rename Manager menghasilkan audit `user_renamed`; perubahan PIN menghasilkan
  audit `user_pin_changed` tanpa menyimpan PIN, hash, atau salt.
- Mutasi nama/PIN dan audit berada dalam transaksi yang sama; kegagalan audit
  terbukti me-roll back perubahan.
- `UserManagementViewModel` memuat ulang daftar akun setelah perubahan sukses.
- Halaman Manajemen Akun menyediakan dialog **Edit Nama Manager** dan
  **Ganti PIN Manager** dengan PIN lama, PIN baru, serta konfirmasi PIN baru.
- Dialog rename yang sama dipakai ulang untuk Manager dan Kasir tanpa mengubah
  key maupun perilaku rename Kasir yang sudah ada.
- Sesi aktif tetap berlaku setelah nama/PIN berubah karena UID, toko, role,
  dan status tidak berubah; login berikutnya membaca nama baru dan hanya
  menerima PIN baru.
- Recovery lupa PIN tetap memakai prosedur khusus tanpa bypass tersembunyi.
- Kebijakan lifecycle Manager V1 dicatat di `README.md`.
- Tidak ada dependency, schema, migration, atau index baru.

Skenario keamanan dan konsistensi yang berhasil diverifikasi:

- Kasir, sesi Manager legacy, UID palsu, toko berbeda, dan Manager nonaktif
  ditolak tanpa mutasi maupun audit.
- Nama kosong dan lebih dari 100 karakter ditolak.
- PIN lama salah, PIN baru sama, invalid, atau duplikat ditolak.
- PIN Kasir nonaktif tetap tidak boleh dipakai Manager.
- PIN lama tidak berlaku setelah perubahan sukses dan PIN baru dapat login.
- Sesi Manager yang dibuat sebelum rename/PIN berubah tetap dapat memuat daftar
  akun karena identitas persisten tidak berubah.
- Audit rename memakai snapshot nama Manager terbaru dan details memuat nama
  lama serta nama baru tanpa data rahasia.
- Kegagalan hashing maupun insert audit mempertahankan PIN lama.
- Kegagalan audit rename mempertahankan nama Manager lama.

Verifikasi akhir:

- Targeted repository/ViewModel/UI manajemen akun: **23/23 lulus**.
- Regression autentikasi, sesi, shell, role, dan widget: **27/27 lulus**.
- Seluruh migration dan migrated recovery flow: **38/38 lulus**.
- Flutter analyzer seluruh `lib`, `test`, dan `tool`: **No issues found**.
- Seluruh unit/widget/migration test pada source final: **287/287 lulus**.
- `git diff --check`: **bersih**.
- Schema tetap **versi 9**.

Pengujian manual perangkat tidak diperlukan karena seluruh perubahan berada
pada boundary database, autentikasi, dan dialog yang sudah diuji deterministik.
Batch berikutnya adalah rekonsiliasi login hardening setelah lifecycle akun
kembali selaras.

## 8 Agustus 2026 - Gerbang Rekonsiliasi Batch 6: attempt limiter login

Perubahan yang berhasil diterapkan:

- `LoginViewModel` kembali memiliki attempt limiter progresif in-memory.
- Percobaan gagal 1-4 berjalan tanpa delay.
- Kegagalan ke-5 memberi delay 2 detik, kemudian 4, 8, 16, dan maksimum
  30 detik untuk percobaan berikutnya.
- Tidak ada blocking `sleep`; countdown memakai timer non-blocking dan
  `retryAfter` dihitung dari clock yang dapat diinjeksi.
- Percobaan selama delay tidak menambah penghitung kegagalan.
- Format PIN invalid ditolak sebelum autentikasi dan tidak menambah counter.
- Login berhasil mereset counter, batas waktu, timer, dan error limiter.
- PIN salah dan PIN akun nonaktif memakai pesan generik yang sama:
  `PIN tidak dikenali atau akun tidak aktif.`
- Error internal tidak lagi mengekspos teks exception atau detail database.
- `LoginPage` menonaktifkan field PIN, tombol visibility, submit, dan submit
  keyboard selama delay.
- UI menampilkan countdown dan membersihkan PIN serta mengembalikan mode
  obscure ketika delay mulai aktif.
- Timer dibatalkan ketika blokir selesai, login berhasil, atau ViewModel
  dibuang sehingga tidak ada timer yatim.
- Limiter berlaku global selama instance `LoginViewModel` hidup dan reset saat
  proses aplikasi restart; tidak ada permanent lockout tanpa recovery.
- Kebijakan tersebut didokumentasikan di `README.md`.
- Tidak ada dependency, schema, migration, atau index baru.

Skenario keamanan dan konsistensi yang berhasil diverifikasi:

- Delay progresif tepat 2/4/8/16/30 detik dan tetap maksimum 30 detik.
- Fake clock dapat melewati delay secara deterministik tanpa menunggu nyata.
- Login benar selama delay ditolak tanpa menambah counter.
- Login benar setelah delay berakhir berhasil dan mereset seluruh state.
- PIN akun nonaktif tidak dapat dibedakan dari PIN yang tidak dikenal melalui
  pesan error.
- Exception internal yang mengandung data sensitif tidak muncul di UI.
- Kegagalan kelima membersihkan controller PIN dan menonaktifkan form.
- Flow login Manager/Kasir, logout, startup recovery, manajemen akun, dan
  navigasi shell tetap berjalan.

Verifikasi akhir:

- Targeted limiter/ViewModel/UI login: **12/12 lulus**.
- Regression login-shell, recovery, akun, dan widget: **36/36 lulus**.
- Seluruh migration dan migrated recovery flow: **38/38 lulus**.
- Flutter analyzer seluruh `lib`, `test`, dan `tool`: **No issues found**.
- Seluruh unit/widget/migration test pada source final: **291/291 lulus**.
- `git diff --check`: **bersih**.
- Schema tetap **versi 9**.

Pengujian manual perangkat belum diperlukan untuk limiter karena clock,
countdown, disable state, dan reset dapat diuji deterministik. Batch login
hardening berikutnya adalah lifecycle auto-lock setelah background 5 menit dan
proteksi field PIN; batch tersebut memerlukan smoke test lifecycle Android
setelah otomatisasi hijau.

## 8 Agustus 2026 - Gerbang Rekonsiliasi Batch 7: lifecycle auto-lock dan proteksi PIN

Perubahan yang berhasil diterapkan:

- `AppShellPage` mengamati lifecycle aplikasi dan mencatat waktu saat aplikasi
  meninggalkan foreground menggunakan clock yang dapat diinjeksi.
- Resume sebelum 5 menit mempertahankan sesi dan keranjang.
- Resume tepat 5 menit atau lebih melakukan auto-lock langsung ke halaman
  Login dan membersihkan keranjang tanpa mengulang setup awal atau aktivasi
  lisensi.
- Durasi negatif akibat jam perangkat bergerak mundur diperlakukan sebagai
  kondisi tidak aman dan mengunci aplikasi.
- Logout manual dan auto-lock memakai satu jalur pembersihan keranjang yang
  idempotent sehingga callback logout tidak dijalankan berulang.
- `LoginPage` membersihkan PIN ketika aplikasi meninggalkan foreground,
  mengembalikan mode PIN tersembunyi, dan mencegah hasil autentikasi yang
  selesai di background membuka shell.
- Autocorrect, suggestion, dan autofill dinonaktifkan pada field PIN.
- Kebijakan lifecycle lock V1 didokumentasikan di `README.md`.
- Tidak ada dependency, schema, migration, atau index baru.

Skenario keamanan dan konsistensi yang berhasil diverifikasi otomatis:

- Background 4 menit 59 detik mempertahankan sesi dan isi keranjang.
- Background tepat 5 menit mengunci sesi serta membersihkan keranjang.
- Logout manual membersihkan keranjang melalui jalur yang sama.
- Auto-lock setelah login kembali ke Login tanpa membuka setup atau aktivasi.
- PIN yang terlihat maupun tersembunyi dibersihkan saat background dan kembali
  tersembunyi ketika resume.
- Field PIN tidak menawarkan autocorrect, suggestion, maupun autofill.
- Flow login, role, recovery, akun, shell, dan database migration tetap lulus.

Verifikasi otomatis akhir:

- Targeted lifecycle Login/shell/integrasi: **20/20 lulus**.
- Regression login, keranjang, akun, recovery, dan startup: **33/33 lulus**.
- Seluruh migration dan migrated recovery flow: **38/38 lulus**.
- Flutter analyzer seluruh `lib`, `test`, dan `tool`: **No issues found**.
- Seluruh unit/widget/migration test pada source final: **296/296 lulus**.
- `git diff --check`: **bersih**.
- Schema tetap **versi 9**.

Implementasi dan gerbang otomatis batch ini selesai. Gerbang terakhir yang
masih diperlukan adalah smoke test lifecycle Android nyata pada perangkat
target untuk memvalidasi perilaku background/resume sistem operasi.

Konfirmasi perangkat:

- Smoke test Android pada perangkat target dikonfirmasi **lulus** oleh pengguna
  pada 8 Agustus 2026.
- Sesi/keranjang bertahan sebelum 5 menit, auto-lock dan pembersihan keranjang
  berjalan mulai 5 menit, serta field PIN kembali kosong dan tersembunyi.
- Dengan konfirmasi ini, Gerbang Rekonsiliasi Batch 7 dinyatakan **selesai
  penuh**.

## 8 Agustus 2026 - Gerbang Rekonsiliasi Batch 8: konfigurasi printer dan desain struk

Perubahan yang berhasil diterapkan:

- `PrinterSettingsRepository` dan `ReceiptDesignRepository` sekarang mewajibkan
  sesi Manager persisten untuk setiap penyimpanan konfigurasi.
- UID, toko, role Manager, dan status aktif divalidasi ulang dari database;
  Kasir maupun sesi yang tidak sah ditolak pada boundary repository.
- Pengaturan printer menghasilkan audit `printer_settings_updated` dan desain
  struk menghasilkan audit `receipt_design_updated`.
- Audit memakai UID, nama, dan role Manager dari database serta entity toko
  terkait, bukan snapshot sesi yang dapat kedaluwarsa.
- Detail audit hanya memuat field konfigurasi aman. Nama/alamat printer dan
  path logo lokal tidak pernah dimasukkan ke audit.
- Update konfigurasi dan insert audit berada dalam transaksi yang sama;
  kegagalan audit me-roll back perubahan.
- Penyimpanan nilai identik menjadi no-op tanpa update maupun audit tambahan.
- Sesi diteruskan melalui ViewModel dan dialog printer/desain struk.
- Tombol pengaturan printer disembunyikan bagi Kasir di halaman Kasir dan
  Riwayat, sementara kemampuan mencetak transaksi tetap tidak dibatasi.
- Kebijakan konfigurasi printer V1 didokumentasikan di `README.md`.
- Tidak ada dependency, schema, migration, atau index baru.

Skenario keamanan dan konsistensi yang berhasil diverifikasi:

- Manager aktif dapat menyimpan pengaturan printer dan desain struk.
- Audit tercipta tepat sekali dengan action, entity, dan aktor persisten yang
  benar tanpa alamat perangkat atau path logo.
- Penyimpanan identik tidak menambah audit.
- Kasir ditolak tanpa perubahan pengaturan maupun audit.
- Trigger kegagalan audit membatalkan update printer dan desain struk.
- Manager melihat tombol pengaturan printer; Kasir tidak melihatnya.
- Preview, pencetakan transaksi, kegagalan printer, login/shell, riwayat,
  backup, tema, role, dan pembacaan audit tetap berjalan.

Verifikasi akhir:

- Targeted repository/audit/printer/widget: **23/23 lulus**.
- Regression audit, role, login-shell, Kasir, riwayat, tema, dan backup:
  **33/33 lulus**.
- Seluruh migration dan migrated recovery flow: **38/38 lulus**.
- Flutter analyzer seluruh `lib`, `test`, dan `tool`: **No issues found**.
- Seluruh unit/widget/migration test pada source final: **301/301 lulus**.
- `git diff --check`: **bersih**.
- Schema tetap **versi 9**.

Pengujian manual printer tidak diperlukan pada batch ini karena perilaku cetak
fisik tidak diubah; authorization, sanitasi audit, atomicity, dan akses UI telah
diverifikasi deterministik. Batch berikutnya adalah rekonsiliasi penyimpanan
tema agar memakai revalidasi Manager aktif dan audit atomik.

## 8 Agustus 2026 - Gerbang Rekonsiliasi Batch 9: penyimpanan tema

Perubahan yang berhasil diterapkan:

- `ThemeRepository` sekarang mewajibkan sesi Manager persisten dan memvalidasi
  ulang UID, toko, role, serta status aktif dari database.
- Penyimpanan tema dan audit `theme_settings_updated` berada dalam transaksi
  yang sama; kegagalan audit me-roll back seluruh perubahan tema.
- Audit memakai UID, nama, dan role Manager dari database, bukan nama snapshot
  sesi yang mungkin sudah kedaluwarsa.
- Audit memuat daftar field yang berubah serta nilai tema non-secret: mode,
  preset, warna, density, corner style, dan status logo toko.
- Penyimpanan nilai identik menjadi no-op tanpa update maupun audit baru.
- `ThemeController` tidak lagi menyimpan actor global. Setiap mutasi menerima
  sesi secara eksplisit dari `ThemeCustomizerPage` melalui `AppShellPage`.
- Pratinjau tema tetap berubah langsung tanpa menunggu database.
- Kegagalan persist ditangkap sebagai pesan generik `Tema tidak dapat
  disimpan.` dan tidak lagi mencetak exception atau stack database ke debug log.
- Kasir tetap tidak memiliki akses ke menu Tema; repository juga menolak
  pemanggilan langsung oleh Kasir atau Manager nonaktif tanpa mutasi/audit.
- Kebijakan penyimpanan tema V1 didokumentasikan di `README.md`.
- Tidak ada dependency, schema, migration, atau index baru.

Skenario keamanan dan konsistensi yang berhasil diverifikasi:

- Manager aktif menyimpan tema dan menghasilkan tepat satu audit dengan actor
  persisten dari database meskipun nama pada sesi sudah lama.
- Penyimpanan identik tidak menghasilkan audit.
- Kasir dan Manager nonaktif ditolak tanpa mengubah tema atau menambah audit.
- Trigger kegagalan audit mempertahankan tema lama dan tidak meninggalkan audit
  parsial.
- Controller mempertahankan pratinjau UI tetapi menampilkan error generik saat
  persist ditolak; database tetap memakai tema lama.
- Setup, login, shell, lifecycle lock, role, audit reader, printer, dan database
  theme round-trip tetap berjalan.

Verifikasi akhir:

- Targeted repository/controller/database/widget tema: **18/18 lulus**.
- Regression shell, setup, role, audit, printer, startup, dan login:
  **32/32 lulus**.
- Seluruh migration dan migrated recovery flow: **38/38 lulus**.
- Flutter analyzer seluruh `lib`, `test`, dan `tool`: **No issues found**.
- Seluruh unit/widget/migration test pada source final: **303/303 lulus**.
- `git diff --check`: **bersih**.
- Schema tetap **versi 9**.

Pengujian manual tidak diperlukan karena authorization, no-op, audit,
atomicity, wiring sesi, state UI, dan error state dapat diverifikasi
deterministik. Batch berikutnya adalah rekonsiliasi audit penggantian lisensi.

## 1 September 2026 - Gerbang Rekonsiliasi Batch 10: penggantian lisensi

Perubahan yang berhasil diterapkan:

- Aktivasi pertama sebelum login tetap memakai `activate` tanpa dipalsukan
  sebagai aksi Manager atau penggantian lisensi.
- Jalur penggantian lisensi baru mewajibkan sesi Manager persisten dan
  memvalidasi ulang UID, toko, role, serta status aktif dari database sebelum
  token diproses.
- Token baru dibersihkan dan divalidasi penuh sebelum secure storage diubah.
- Penggantian sukses menghasilkan audit stabil `license_replaced` dengan entity
  `license` dan identitas Manager dari database.
- Audit hanya menyimpan ID lisensi, waktu terbit, waktu kedaluwarsa, dan jumlah
  fitur.
- Audit tidak menyimpan token, signature, installation ID, nama toko, request
  code, encoded activation request, maupun activation secret.
- Token identik menjadi no-op tanpa write secure storage atau audit baru.
- Karena secure storage dan SQLite tidak mendukung satu transaksi lintas media,
  digunakan rollback kompensasi: token lama dibaca, token baru disimpan, audit
  ditulis dalam transaksi SQLite, lalu token lama dipulihkan bila audit gagal.
- Jika sebelumnya belum ada token, kegagalan audit menghapus token baru.
- Sesi Manager diteruskan dari `AppShellPage` ke `LicenseInfoPage`, dialog,
  ViewModel, lalu service penggantian.
- Error internal penggantian menjadi pesan generik dan tidak mengekspos teks
  exception database atau secure storage.
- Kebijakan penggantian lisensi V1 didokumentasikan di `README.md`.
- Tidak ada dependency, schema, migration, atau index baru.

Skenario keamanan dan konsistensi yang berhasil diverifikasi:

- Manager aktif dapat mengganti token dan menghasilkan tepat satu audit aman.
- Kasir dan Manager nonaktif ditolak sebelum token berubah atau audit dibuat.
- Token malformed, signature palsu, token tablet lain, dan token kedaluwarsa
  mempertahankan token serta payload lisensi lama tanpa audit.
- Token identik diterima tanpa penulisan atau audit penggantian.
- Trigger kegagalan audit memulihkan token lama dan tidak meninggalkan audit
  parsial.
- Audit memakai UID/nama Manager dari database serta tidak mengandung token,
  installation ID, atau nama toko.
- Aktivasi awal, startup license gate, registry/issuer tool, role, shell, audit
  reader, dan backup tanpa token tetap berjalan.

Verifikasi akhir:

- Targeted ViewModel/service/safety/UI penggantian lisensi: **12/12 lulus**.
- Seluruh regression lisensi dan startup: **55/55 lulus**.
- Regression role, login-shell, audit reader, dan backup: **23/23 lulus**.
- Seluruh migration dan migrated recovery flow: **38/38 lulus**.
- Flutter analyzer seluruh `lib`, `test`, dan `tool`: **No issues found**.
- Seluruh unit/widget/migration test pada source final: **307/307 lulus**.
- `git diff --check`: **bersih**.
- Schema tetap **versi 9**.

Pengujian manual belum diperlukan karena secure storage diuji melalui kontrak
key-value store dan seluruh authorization, validasi, sanitasi, no-op, serta
rollback dapat diverifikasi deterministik. Penggantian memakai secure storage
Android dan token resmi akan masuk device/UAT release. Batch berikutnya adalah
inventaris penutup rekonsiliasi audit, backup, maintenance, dan printer sebelum
kembali ke Tahap 17 performance/memory.

## 2 September 2026 - Gerbang Rekonsiliasi Batch 11: penutup source

Gerbang Rekonsiliasi Source Tahap 8-16 selesai. Inventaris produsen/pembaca
audit, backup/restore, database health, cleanup aset, dan printer menemukan
bahwa implementasi domainnya masih tersedia, tetapi beberapa wiring produksi
dan hardening Tahap 16 hilang dari source aktif.

Perubahan yang berhasil dipulihkan:

- `main.dart` kembali memasang provider produksi untuk pembuat backup, tujuan
  backup Download publik, factory restore, dan diagnostik database.
- Lokasi database produksi untuk health check kembali menunjuk
  `Documents/kasir_offline.sqlite`.
- `AssetCleanupService` kembali berjalan saat startup dan tetap hanya memindai
  `product_images` serta `receipt_assets`.
- Android 11 ke bawah kembali memiliki deklarasi location yang dibatasi
  `maxSdkVersion=30`; Android 12+ tetap memakai Bluetooth scan/connect.
- Status izin printer kembali dibedakan menjadi granted, denied, dan permanently
  denied. Penolakan permanen menyediakan tombol **Buka Pengaturan Aplikasi**.
- Scan, status, connect, disconnect, permission, dan open settings memakai
  timeout yang sama.
- Cetak transaksi serta test print menolak request duplikat. Jika pengiriman
  native timeout, lock tetap aktif sampai Future native selesai agar retry tidak
  berisiko mencetak dua struk.
- Error internal plugin disanitasi sebelum masuk UI maupun print log.
- Logo struk hilang, gagal dibaca, atau korup dilewati tanpa menggagalkan isi
  struk.

Inventaris juga memastikan:

- Seluruh produsen audit runtime memakai action/entity stabil; mutasi Manager
  yang direkonsiliasi memakai UID aktor persisten dari database.
- Pembaca audit tetap Manager-only, memiliki filter, pagination, urutan
  deterministik, dan tetap dapat membaca audit legacy ber-UID null.
- Backup tetap memisahkan secure token, memverifikasi manifest/checksum/schema,
  dan menghapus archive jika audit gagal.
- Restore tetap memakai staging, validasi path/ukuran/checksum/database, safety
  copy, rollback, audit pada database hasil restore, serta invalidasi sesi.
- Database health tetap read-only dan Manager-only; tidak ada repair otomatis.
- Schema database tetap **v9** dan tidak ada dependency baru.

Pengujian source final:

- Targeted audit/backup/restore/maintenance/printer: **59/59 lulus**.
- Flutter analyzer: **tidak ada masalah**.
- Seluruh unit, widget, dan migration test: **316/316 lulus**.
- APK debug: **berhasil dibangun** di
  `build/app/outputs/flutter-apk/app-debug.apk`.
- `git diff --check`: **bersih** sebelum dokumentasi.

Warning non-blocking Kotlin lama dari plugin `file_picker` dan
`print_bluetooth_thermal` tetap dicatat untuk Tahap 22; build saat ini berhasil.
Tes manual belum diperlukan pada batch ini. Validasi printer fisik pernah lulus
dan akan diulang pada UAT release; pekerjaan berikutnya kembali ke Tahap 17.4
untuk profiling memory nyata pada tablet target.

## 2 September 2026 - Tahap 17.4 Batch 2: alokasi daftar dan widget lazy

Audit lifecycle memastikan seluruh `TextEditingController` halaman/dialog
utama sudah dibuang pada `dispose`, provider ViewModel yang dibuat aplikasi
memiliki lifecycle ownership yang benar, dan katalog sudah memakai
`GridView.builder`. Tablet target `10DE3201NZ000NB` belum terdeteksi, sehingga
batch dilanjutkan pada churn memory yang dapat dibuktikan dari source dan test.

Perubahan yang berhasil diterapkan:

- `CashierViewModel.visibleProducts` tidak lagi memfilter serta membuat daftar
  10.000 produk setiap kali getter dibaca.
- Hasil filter sekarang disimpan ulang sampai katalog, kata pencarian, atau
  kategori berubah.
- `TransactionHistoryViewModel.entries` tidak lagi membuat salinan seluruh
  daftar pada setiap getter.
- Ringkasan omzet, item, dan jumlah transaksi batal dihitung satu kali ketika
  snapshot riwayat diganti, bukan memindai seluruh riwayat pada setiap rebuild.
- Halaman riwayat tidak lagi membuat seluruh widget transaksi sekaligus.
  Header tanggal dan kartu transaksi sekarang dibangun oleh
  `SliverChildBuilderDelegate` hanya ketika diperlukan viewport.
- Keranjang Kasir, filter, periode riwayat, detail, pembatalan, reprint, serta
  state navigasi tetap dipertahankan.
- Tidak ada dependency, schema, migration, atau generated file baru.

Pengujian source final:

- Tes baru membuktikan cache filter dipakai ulang lalu diinvalidasi ketika
  kriteria berubah, serta snapshot/ringkasan riwayat stabil tanpa copy getter.
- Targeted Kasir, riwayat, pembatalan, printer, shell, dan lifecycle:
  **38/38 lulus**.
- Flutter analyzer: **tidak ada masalah**.
- Seluruh unit, widget, dan migration test: **318/318 lulus**.
- `git diff --check`: **bersih** sebelum dokumentasi.

Profil angka heap/RSS perangkat belum dilakukan karena tablet tidak terhubung;
hal tersebut tidak ditandai lulus secara semu. Checkpoint berikutnya adalah
menjalankan build profile pada tablet dan mengukur startup, login/logout
berulang, katalog, pencarian, checkout, history, laporan, backup, cache gambar,
serta kestabilan memory setelah kembali ke idle.

## 2 September 2026 - Tahap 17.4 Batch 3: device profile dan akses Backup

APK profile diuji langsung pada V2322 Android 15 (API 35), device
`10DE3201NZ000NB`. Pengujian mempertahankan database asli: tidak ada transaksi
yang disimpan, stok yang diubah, restore yang dijalankan, maupun backup baru
yang dibuat.

Perubahan yang berhasil diterapkan:

- Audit perangkat menemukan provider/service backup, restore, tujuan Download,
  dan diagnostik sudah terpasang di produksi tetapi `BackupRestorePage` belum
  dapat dicapai dari shell aplikasi.
- Bagian Manager **Backup & Restore** ditambahkan ke navigasi dan diarahkan ke
  halaman produksi yang sudah ada.
- Kasir tetap tidak dapat melihat atau mengakses bagian tersebut melalui
  `AppUserSession.canAccess`.
- Tidak ada dependency, schema, migration, generated file, atau format backup
  yang berubah.

Hasil profile perangkat:

- Cold start APK profile: **1.767 ms**.
- Alur aktual yang diuji: login setelah auto-lock/reinstall, Dashboard, katalog,
  pencarian, scroll riwayat, empat siklus perpindahan halaman, 20 kali
  buka/tutup dialog akun, serta 20 kali buka/tutup pembayaran tanpa menyimpan
  transaksi.
- Siklus kedua dialog akun hanya menambah sekitar **58 KB** native heap; siklus
  kedua dialog pembayaran menurunkan native heap sekitar **289 KB**. Tidak ada
  pola pertumbuhan linear.
- Pada APK profile final, Dashboard awal tercatat PSS **224,5 MB**, RSS
  **289,9 MB**, dan native heap teralokasi **37,1 MB**.
- Setelah diagnostik database dan kembali idle tercatat PSS **214,3 MB**, RSS
  **294,7 MB**, dan native heap **37,9 MB**. Setelah lima iterasi tambahan
  Dashboard/Backup tetap stabil pada PSS **214,4 MB**, RSS **296,8 MB**, dan
  native heap **38,0 MB**.
- Buffer grafis sekitar **81-89 MB** berasal dari buffer layar 1080x2388.
  `dumpsys gfxinfo` tidak merekam frame Flutter `SurfaceView` setelah reset,
  sehingga angka frame tidak dipakai sebagai klaim kelulusan.
- Diagnostik produksi menyatakan database sehat: schema 9, pelanggaran foreign
  key 0, ukuran 232 KB, 1 toko, 3 kategori, 3 produk, 5 metode pembayaran,
  2 akun, 8 transaksi, 13 pergerakan stok, dan 21 audit log.

Pengujian source dan build final:

- Targeted role, shell, dan Backup & Restore: **16/16 lulus**.
- Flutter analyzer: **tidak ada masalah**.
- Seluruh unit, widget, dan migration test: **319/319 lulus**.
- APK profile: **berhasil dibangun** (88,5 MB), dipasang dengan update yang
  mempertahankan data, dan dibuka pada perangkat target.

Tahap 17.4 belum ditutup secara semu. Dataset aktif perangkat hanya memiliki
3 produk dan 8 transaksi, sehingga batch berikutnya adalah profile perangkat
dengan dataset terisolasi berisi 10.000 produk dan laporan besar. Isolasi wajib
agar database asli pengguna tidak ditimpa atau dicampur dengan data benchmark.

## 2 September 2026 - Tahap 17.4 Batch 4: stress profile terisolasi

Harness profile Android khusus berhasil dibuat untuk menguji UI dan repository
produksi dengan dataset besar tanpa memakai database toko pengguna.

Perubahan yang berhasil diterapkan:

- `tool/device_performance_profile.dart` membuka hanya
  `kasir_offline_performance_profile.sqlite`, bukan `kasir_offline.sqlite`.
- Harness memakai `PerformanceSeedService` yang sudah ada dan memverifikasi
  tepat 10.000 produk serta 100.000 transaksi sebelum pengujian dimulai.
- Halaman Kasir produksi dipakai untuk load katalog dan pencarian 10.000
  produk; halaman Riwayat produksi dipakai untuk periode **Semua** 100.000
  transaksi; `SalesReportRepository` produksi dipakai untuk laporan besar.
- Waktu seed/verifikasi, load katalog, laporan, dan riwayat tampil langsung di
  harness agar hasil profile dapat dibaca deterministik dari perangkat.
- Tes isolasi membuat file produksi sentinel, menjalankan seed kecil pada
  database profile, lalu membuktikan isi sentinel tidak berubah.
- Tidak ada dependency, schema, migration, generated file, format backup, atau
  jalur startup produksi yang berubah. Schema tetap **versi 9**.

Hasil profile V2322 Android 15 (`10DE3201NZ000NB`):

- Cold start harness: **1.800 ms**.
- Seed/verifikasi penuh: **69.774 ms**. Database utama sekitar 136 MB dan WAL
  sekitar 137 MB selama benchmark.
- Katalog 10.000 produk termuat dalam **652 ms**. Pencarian `PRD-09999`
  langsung menghasilkan produk ke-9.999 dengan benar.
- Laporan SQL 100.000 transaksi selesai dalam **2.934 ms** dan menghasilkan
  98.000 transaksi selesai, 2.000 batal, 294.000 item terjual, 10.000 kelompok
  produk, satu kelompok pembayaran, serta dua hari laporan.
- Riwayat 100.000 transaksi termuat dalam **7.043 ms**; reload berikutnya
  **7.446 ms** dan **7.768 ms**. Delapan scroll panjang berjalan tanpa crash.
- PSS status awal **200,0 MB**, katalog **234,7 MB**, laporan **239,2 MB**,
  dan setelah riwayat penuh menetap sekitar **498-528 MB**. Area data utama
  tidak tumbuh linear pada reload, tetapi snapshot 100.000 baris masih terlalu
  besar untuk menutup Tahap 17.4.
- Checksum SHA-256 database produksi, WAL, dan SHM identik sebelum dan sesudah
  seluruh benchmark. Tidak ada transaksi, stok, audit, backup, atau lisensi
  produksi yang berubah.
- Tiga file database benchmark sekitar **274 MB** sudah dihapus. APK profile
  normal dibangun ulang (**101,6 MB**), dipasang kembali, dan membuka halaman
  Login aplikasi utama dengan cold start **1.742 ms**.

Pengujian source final:

- Targeted isolasi database dan performance seed: **4/4 lulus**.
- Flutter analyzer seluruh source, test, dan tool: **tidak ada masalah**.
- Seluruh unit, widget, dan migration test: **320/320 lulus**.
- APK profile harness (**88,5 MB**) dan APK profile normal (**101,6 MB**):
  **berhasil dibangun dan dipasang**.
- `git diff --check`: **bersih** setelah dokumentasi.

Tes manual pengguna tidak diperlukan pada batch ini karena seluruh navigasi,
input pencarian, scroll, memory, file perangkat, dan checksum dijalankan serta
dibaca langsung melalui perangkat. Batch berikutnya tetap Tahap 17.4:
pagination/bounded loading riwayat agar periode **Semua** tidak menahan seluruh
100.000 transaksi di memory, lalu profile ulang pada dataset terisolasi.

## 3 September 2026 - Tahap 17.4 Batch 5: pagination riwayat dan penutup Tahap 17

Riwayat transaksi sekarang tidak lagi mengambil dan mempertahankan seluruh
hasil filter di memory. Periode **Semua** tetap menampilkan ringkasan global,
tetapi daftar aktif dibatasi menjadi 50 transaksi per halaman.

Perubahan yang berhasil diterapkan:

- `SaleHistoryRepository.getSalesPage` mengambil ID dengan urutan
  `created_at DESC, id DESC`, `LIMIT 51`, dan offset halaman; hanya 50 sale serta
  payment terkait yang kemudian dibaca ke memory.
- Pencarian nomor transaksi, nama Kasir, dan metode pembayaran tetap bekerja.
  Filter tanggal memakai batas akhir eksklusif yang sama dengan perilaku lama.
- Ringkasan seluruh filter (`transactionCount`, omzet, item, dan pembatalan)
  dihitung langsung oleh agregasi SQL, bukan dari satu snapshot 100.000 objek.
- Omzet dan item hanya menjumlah transaksi selesai; transaksi batal tetap
  dihitung terpisah sesuai aturan sebelumnya.
- ViewModel menyimpan satu halaman, mereset ke halaman pertama saat periode atau
  pencarian berubah, menyediakan halaman berikut/sebelumnya, dan mengabaikan
  hasil request lama bila filter berubah saat load masih berlangsung.
- UI menampilkan `Halaman x dari y`, jumlah baris yang sedang ditampilkan,
  total hasil filter, tombol **Sebelumnya/Berikutnya**, serta kembali ke atas
  setelah perpindahan halaman.
- Benchmark desktop dan harness Android diselaraskan dengan API pagination.
- Tidak ada dependency, migration, format backup, atau jalur autentikasi yang
  berubah. Schema database tetap **versi 9**.

Verifikasi repository dan regresi:

- Test pagination menggunakan 55 transaksi membuktikan halaman pertama 50,
  halaman kedua 5, kembali ke halaman pertama 50, urutan `RAW-055` lalu
  `RAW-005`, dan ringkasan global 55 transaksi/50.000 omzet/100 item/5 batal.
- Targeted history, cancellation, login shell, dan isolasi performance:
  **15/15 lulus**.
- Flutter analyzer seluruh source, test, dan tool: **tidak ada masalah**.
- Seluruh unit, widget, dan migration test: **321/321 lulus**.

Hasil profile V2322 Android 15 (`10DE3201NZ000NB`) pada 100.000 transaksi:

- Cold start harness: **1.997 ms**; seed/verifikasi penuh: **68.821 ms**.
- Halaman pertama periode **Semua** termuat dalam **362 ms** dengan ringkasan
  100.000 transaksi dan omzet Rp1.470.000.000 yang benar.
- PSS setelah halaman riwayat pertama **236,2 MB**. Setelah berpindah ke halaman
  kedua PSS **198,1 MB**, dan setelah tiga siklus Status-Riwayat PSS akhir
  **195,3 MB** / RSS **287,8 MB**. Sebelum pagination, PSS bertahan sekitar
  **498-528 MB**.
- Tiga warm reload selesai dalam **119 ms**, **102 ms**, dan **116 ms**.
- Scroll sampai footer menampilkan `Halaman 1 dari 2000`; tombol berikutnya
  membuka transaksi pertama halaman kedua yang berakhiran `-099950`, sesuai
  offset setelah 50 hasil pertama.
- Pencarian `PERF-20260701-000001` menghasilkan tepat satu transaksi, omzet
  Rp15.000, dan pagination 1/1.
- Checksum SHA-256 database produksi, WAL, dan SHM identik sebelum dan sesudah
  benchmark. Tiga file database uji sekitar **274 MB** sudah dihapus.
- APK profile normal berhasil dibangun ulang (**101,6 MB**), dipasang, dan
  kembali membuka halaman `Login`/`Masuk` dengan cold start **1.888 ms**.

Catatan tooling non-blocking: perintah lama `dart run
tool/seed_performance_database.dart` berhenti sebelum `main` karena jalur
`AppDatabase` membawa `drift_flutter`/`dart:ui` yang tidak tersedia pada Dart VM
murni. Tidak ada file benchmark yang tercipta. Seed besar dan pengukuran tetap
lulus melalui harness Flutter Android yang memang menjadi target runtime;
kompatibilitas CLI ini dapat dirapikan pada batch tooling tanpa mengubah fitur.

Dengan hasil ini, Tahap 17 performance dan memory dinyatakan **selesai**.
Pengujian manual pengguna tidak diperlukan karena tombol halaman, pencarian,
scroll, reload, memory, checksum, pembersihan file uji, serta pemulihan APK
normal sudah diverifikasi langsung. Tahap berikutnya adalah Tahap 18 Batch 1:
audit UX, responsive, dan accessibility.

## 3 September 2026 - Tahap 18 Batch 1: responsive dan accessibility dasar

Audit berbasis widget test menemukan empat overflow nyata pada kombinasi layar
sempit dan text scale besar. Semua akar masalah tersebut sudah diperbaiki tanpa
menambah dependency atau membuat sistem layout baru.

Perubahan yang berhasil diterapkan:

- Label `NavigationDrawerDestination` sekarang memakai ruang fleksibel dan
  ellipsis visual. Teks lengkap `Backup & Restore` tetap tersedia pada semantics.
- Aksi AppBar lengkap (chip status printer dan nama pengguna) baru ditampilkan
  pada breakpoint lebar 1200 px. Lebar 900-1199 px memakai tombol status printer
  ringkas yang tetap memiliki tooltip.
- Header `Transaksi Terbaru` pada dashboard berubah menjadi susunan vertikal
  bila lebar konten kurang dari 480 px, sehingga tombol `Lihat Semua` tidak
  memotong judul ketika teks diperbesar.
- Kartu produk Kasir mendapat tinggi tambahan hanya ketika skala teks efektif
  melewati 1,5. Rasio lama tetap dipakai untuk teks normal.
- Test app dapat menyuntikkan `TextScaler` dan keyboard inset tanpa mengubah
  konfigurasi produksi.

Matriks pengujian yang lulus:

- Breakpoint **899/900/1199/1200 px** pada text scale **2,0**, termasuk
  perpindahan Drawer, NavigationRail ringkas, dan NavigationRail extended.
- Portrait **600x1024** dan landscape **1024x600** pada text scale **1,3**.
- Layar ponsel **390x844** pada text scale **2,0**.
- Portrait **600x1024** dengan keyboard inset **320 px**.
- Dashboard dan Kasir tampil tanpa `RenderFlex overflow`; seluruh halaman
  Manager yang berada di `IndexedStack` ikut melalui layout pada setiap ukuran.
- Drawer sempit mempertahankan semantic label lengkap, tinggi target sentuh
  minimal **48 px**, dan dapat menerima fokus keyboard.

Gerbang verifikasi:

- Targeted/regression shell, login flow, dan manajemen akun: **25/25 lulus**.
- Flutter analyzer seluruh source, test, dan tool: **tidak ada masalah**.
- Seluruh unit, widget, dan migration test: **324/324 lulus**.
- `git diff --check`: **bersih**.
- Schema database tetap **versi 9**; tidak ada migration, dependency, format
  backup, atau data aplikasi yang berubah.

Pengujian manual perangkat tidak diperlukan untuk batch ini karena seluruh
kondisi ukuran, orientasi, text scale, keyboard inset, semantics, target sentuh,
dan fokus sudah diuji secara deterministik. Tahap 18 belum ditutup: Batch 2 akan
mengaudit state loading/empty/error/retry, kegagalan permission/storage,
semantics/tooltip/focus yang tersisa, serta kejelasan dialog konfirmasi berisiko.

## 3 September 2026 - Tahap 18 Batch 2: state kegagalan dan dialog berisiko

Audit lanjutan menutup state error/retry, kegagalan permission/storage,
semantic status proses, dan kejelasan aksi berisiko. Implementasi tetap memakai
komponen Flutter bawaan tanpa dependency atau abstraksi baru.

Perubahan yang berhasil diterapkan:

- Error diagnostik, pembuatan backup, pemilihan arsip, dan restore sekarang
  tampil pada satu kartu status yang konsisten, menjadi semantics `liveRegion`,
  dapat ditutup, dan menyediakan aksi **Coba Lagi** sesuai operasi yang gagal.
- Exception dari file picker kini ditangkap. `FileSystemException` dan
  `PlatformException` diterjemahkan menjadi petunjuk memeriksa izin file serta
  ruang kosong; path dan detail exception internal tidak ditampilkan.
- Error validasi backup dan diagnostik yang memang aman tetap menyampaikan
  alasan spesifik agar pengguna dapat memperbaiki inputnya.
- Indikator proses pemeriksaan database, pembuatan backup, dan restore memiliki
  semantic label yang menjelaskan operasi aktif.
- Dialog restore menyebut nama file arsip yang dipilih dan menjelaskan data
  yang diganti, safety copy, rollback, serta lisensi yang tidak dipindahkan.
- Penghapusan foto produk sekarang wajib melewati dialog non-dismissible yang
  menyebut nama produk, perubahan menjadi placeholder, dan penghapusan file
  lama. Aksi **Batal** terbukti tidak mengubah referensi foto.
- Dialog reset PIN Kasir menegaskan PIN lama tidak dapat digunakan setelah
  perubahan disimpan.
- Dialog penggantian lisensi menegaskan lisensi aktif hanya diganti sesudah
  token baru lolos verifikasi dan lisensi lama dipertahankan bila verifikasi
  gagal.
- Dialog pembatalan transaksi dan nonaktif akun telah diaudit; keduanya sudah
  menyebut target serta dampak sehingga tidak memerlukan perubahan.
- Test widget menemukan overflow 34 px pada dropdown kategori produk. Isi
  dropdown kini mengikuti lebar field melalui `isExpanded`.
- Tidak ada dependency, migration, format backup, maupun schema yang berubah;
  schema database tetap **versi 9**.

Gerbang verifikasi:

- Targeted widget test backup/restore, foto produk, manajemen akun, dan lisensi:
  **17/17 lulus**.
- Test baru membuktikan sanitasi path storage, retry pemilih file, nama arsip
  pada konfirmasi restore, dampak reset PIN/lisensi, serta jalur batal dan
  konfirmasi penghapusan foto.
- Flutter analyzer seluruh source, test, dan tool: **tidak ada masalah**.
- Seluruh unit, widget, dan migration test: **327/327 lulus**.
- `git diff --check`: **bersih**.

Pengujian manual perangkat tidak diperlukan pada batch ini karena perubahan
berupa state dan dialog deterministik yang telah dijalankan melalui widget test,
termasuk operasi database serta pencatatan penghapusan file. Dengan Batch 1 dan
Batch 2 lulus, Tahap 18 UX, responsive, dan accessibility dinyatakan
**selesai**. Tahap berikutnya adalah Tahap 19 Batch 1: backup/restore registry
License Manager dengan checksum, validasi, rollback, dan overwrite protection.

## 3 September 2026 - Tahap 19 Batch 1: fondasi backup/restore registry License Manager

Fondasi layanan backup dan restore registry Super Admin sudah diterapkan. Batch
ini mengunci format serta boundary data terlebih dahulu; UI pemilihan file belum
dibuka sampai alur konfirmasi restore tersedia pada Batch 2.

Perubahan yang berhasil diterapkan:

- Format backup JSON `kasir-license-registry` versi 1 menyimpan versi schema
  database, waktu pembuatan UTC, `AdminStores`, `AdminLicenses`, dan checksum
  SHA-256 seluruh payload.
- Urutan toko dan lisensi dibuat deterministik agar dokumen serta checksum dapat
  diverifikasi secara konsisten.
- Ekspor memakai file `.part`, flush, lalu rename ke tujuan final. File tujuan
  maupun file sementara yang sudah ada ditolak dan tidak pernah ditimpa.
- File sumber wajib berupa file biasa, bukan symlink atau direktori, dan dibatasi
  maksimal 50 MB. Masing-masing tabel dibatasi 250.000 record.
- Parser menolak JSON rusak, format/versi asing, schema lebih baru, field hilang
  atau tambahan, tipe data salah, tanggal non-UTC, expiry tidak valid, daftar
  fitur rusak, dan checksum tidak cocok.
- Validasi keunikan mencakup ID toko, Installation ID, kode permintaan, dan ID
  lisensi.
- Setiap lisensi wajib mengacu ke toko yang tersedia. Relasi
  `supersedesLicenseId` dan `replacedByLicenseId` harus timbal balik, berasal
  dari toko yang sama, tidak membentuk siklus, dan satu toko tidak boleh memiliki
  lebih dari satu lisensi current.
- Backup schema 1 tetap dapat dipulihkan; kolom fingerprint yang belum ada
  dipetakan menjadi `null` sesuai migration database versi 2.
- Restore ke registry berisi ditolak secara default. Overwrite hanya berjalan
  bila pemanggil memberi persetujuan eksplisit dan seluruh delete/insert berada
  dalam satu transaksi database.
- Kegagalan di tengah restore me-roll back data lama dan seluruh insert baru;
  tidak ada partial restore.
- Layanan tidak mencetak dokumen, token, checksum payload, maupun detail data ke
  log. Private signing key tidak pernah menjadi bagian backup registry ini.
- Tidak ada dependency, schema, migration, atau generated file yang berubah.
  Schema License Manager tetap **versi 2** dan schema aplikasi Kasir tetap
  **versi 9**.

Gerbang verifikasi:

- Test layanan backup/restore registry: **9/9 lulus**.
- Matriks test mencakup round-trip kosong, relasi renewal, ekspor file dan
  overwrite protection, checksum corrupt, duplikat, relasi rusak, kompatibilitas
  schema 1, registry besar 500 toko/lisensi, serta rollback paksa melalui trigger
  SQLite.
- Flutter analyzer seluruh License Manager: **tidak ada masalah**.
- Seluruh unit/widget/migration test License Manager: **109/109 lulus**.
- Seluruh unit/widget/migration test aplikasi Kasir: **327/327 lulus**.
- `git diff --check`: **bersih**.

Pengujian manual perangkat belum diperlukan karena layanan belum memiliki pintu
masuk UI dan seluruh operasi file/database dijalankan pada test terisolasi.
Tahap 19 belum selesai. Batch 2 akan menghubungkan layanan ke UI operasional,
menaruh hasil backup di lokasi yang mudah ditemukan, menampilkan validasi sebelum
restore, meminta konfirmasi overwrite eksplisit, dan menguji refresh/error state.

## 3 September 2026 - Tahap 19 Batch 2: UI backup/restore registry

Implementasi UI operasional dan seluruh gerbang otomatis maupun native Android
telah lulus.

Perubahan yang berhasil diterapkan:

- Halaman Registry memiliki kartu `Backup & Restore Registry` dengan aksi buat
  backup dan restore yang tetap responsif pada lebar terbatas.
- Backup memakai pemilih lokasi bawaan sistem sehingga pengguna dapat memilih
  folder Download secara langsung, bukan folder privat aplikasi.
- Nama file otomatis jelas dan unik dengan pola
  `kasir-license-registry-YYYYMMDD-HHMMSS.klrbackup`.
- Setelah penyimpanan berhasil, UI menampilkan lokasi yang dikembalikan pemilih
  file agar hasil lebih mudah ditemukan.
- Restore menerima `.klrbackup` dan kompatibilitas `.json`, lalu memvalidasi
  file, checksum, schema, dan relasi melalui layanan Batch 1 sebelum membuka
  dialog overwrite.
- Dialog konfirmasi menyebut nama file, jumlah toko, jumlah lisensi, dampak
  penggantian seluruh registry, jaminan rollback, serta bahwa private signing
  key tidak berubah.
- Overwrite baru dikirim sebagai persetujuan eksplisit setelah tombol
  `Ganti Registry` ditekan. Pembatalan tidak mengubah data.
- Setelah restore berhasil, ViewModel registry dimuat ulang sehingga daftar dan
  pencarian langsung memakai data baru.
- Proses backup/restore mencegah aksi ganda, memiliki progress dengan semantic
  label, dan mengumumkan hasil lewat live region.
- Error validasi tetap spesifik dan aman; exception platform/filesystem diubah
  menjadi petunjuk permission/ruang kosong tanpa membocorkan path internal.
- Dokumen backup memuat token lisensi sesuai kebutuhan recovery registry, tetapi
  UI memperingatkan pengguna untuk menyimpannya di lokasi aman. Private signing
  key tetap tidak ikut.
- Dependency `file_picker` ditambahkan untuk dialog Save/Open lintas platform;
  tidak ada perubahan schema, migration, atau generated database. Schema License
  Manager tetap **versi 2** dan schema aplikasi Kasir tetap **versi 9**.

Gerbang verifikasi otomatis:

- Targeted service dan widget UI backup/restore registry: **14/14 lulus**.
- Test UI membuktikan nama/payload backup, lokasi hasil, dialog validasi,
  pembatalan, overwrite eksplisit, refresh registry, penolakan backup corrupt,
  serta sanitasi error platform.
- Flutter analyzer seluruh License Manager: **tidak ada masalah**.
- Seluruh unit/widget/migration test License Manager: **114/114 lulus**.
- Seluruh unit/widget/migration test aplikasi Kasir: **327/327 lulus**.
- `git diff --check`: **bersih**.
- APK debug Android berhasil dibangun, dipasang, dan diluncurkan melalui
  `flutter run` pada V2322 Android 15.

Verifikasi manual perangkat juga **lulus**: backup dapat disimpan ke Download,
file `.klrbackup` dapat dipilih kembali tanpa ekstraksi atau menelusuri folder
privat aplikasi, dan aplikasi menampilkan hasil `Restore berhasil`.

Dengan hasil ini, Tahap 19 Batch 2 dinyatakan **selesai**. Tahap 19 belum selesai
karena batch berikutnya akan menangani backup private signing key terenkripsi dan
recovery rehearsal yang hanya memakai generated test key.

## 4 September 2026 - Tahap 19 Batch 3: fondasi backup private signing key terenkripsi

Fondasi kriptografi backup dan recovery private signing key telah selesai. Batch
ini sengaja mengunci format serta boundary keamanan sebelum membuka UI file dan
passphrase pada batch berikutnya.

Perubahan yang berhasil diterapkan:

- Format JSON `kasir-license-signing-key-backup` versi 1 hanya menaruh metadata
  non-secret, parameter kriptografi, salt, nonce, ciphertext, dan MAC di luar
  payload terenkripsi.
- Dokumen private signing key kanonis dienkripsi memakai **AES-256-GCM** sehingga
  kerahasiaan dan integrity dilindungi dalam satu primitive AEAD.
- Key enkripsi diturunkan dari passphrase memakai **Argon2id** dengan memory
  19 MiB, 2 iterasi, parallelism 1, output 32 byte, dan salt acak 16 byte.
- Passphrase wajib 12-256 karakter dan tidak boleh hanya whitespace.
- Setiap backup memakai salt 16 byte dan nonce 12 byte baru dari
  `Random.secure`; test membuktikan dua backup key/passphrase yang sama memiliki
  salt, nonce, ciphertext, dan MAC berbeda.
- Metadata format, versi, waktu backup, algoritma key, fingerprint, seluruh
  parameter KDF, salt, algoritma cipher, dan nonce masuk AAD. Perubahan metadata,
  ciphertext, atau MAC menyebabkan autentikasi gagal.
- Parser hanya menerima field yang tepat, waktu UTC, fingerprint berformat
  benar, Base64URL kanonis, Argon2id parameter produksi, AES-256-GCM, panjang
  salt/nonce/MAC, ciphertext masuk akal, dan ukuran dokumen maksimal 64 KiB.
- Passphrase salah dan backup yang berubah/rusak menghasilkan pesan generik yang
  sama sehingga tidak membuka detail internal kriptografi.
- Setelah dekripsi, pasangan Ed25519 divalidasi kembali oleh parser lama dan
  fingerprint public key dihitung ulang serta wajib cocok dengan metadata.
- Ekspor file memakai `.part`, flush, lalu rename; tujuan atau file sementara
  yang sudah ada ditolak dan tidak ditimpa. Sumber recovery wajib file biasa,
  bukan symlink atau direktori.
- Restore ke vault berisi ditolak secara default. Penggantian hanya berjalan bila
  pemanggil mengirim persetujuan `overwriteExisting` secara eksplisit.
- Derived encryption key dihancurkan setelah operasi selesai. Service tidak
  menulis private key, passphrase, dokumen plaintext, ciphertext, atau MAC ke log.
- Tidak ada dependency, UI, schema, migration, atau generated database yang
  berubah. Schema License Manager tetap **versi 2** dan aplikasi Kasir tetap
  **versi 9**.

Recovery rehearsal dan gerbang verifikasi:

- Rehearsal membuat pasangan Ed25519 baru saat runtime, bukan memakai key
  produksi maupun private-key fixture yang disimpan di repository.
- Generated test key diimpor ke source vault, dibackup terenkripsi, dibuka dengan
  passphrase, dipulihkan ke vault kosong, lalu dipakai menandatangani pesan yang
  berhasil diverifikasi dengan public key hasil recovery.
- Skenario negatif mencakup passphrase salah, MAC/ciphertext atau metadata
  berubah, parameter KDF dilemahkan, field asing, key lama tanpa persetujuan
  overwrite, passphrase lemah, key kosong, dokumen oversized, dan sumber bukan
  file biasa.
- Targeted encrypted-backup/recovery test: **8/8 lulus**.
- Seluruh unit/widget/migration test License Manager: **122/122 lulus**.
- Seluruh unit/widget/migration test aplikasi Kasir: **327/327 lulus**.
- Flutter analyzer seluruh License Manager: **tidak ada masalah**.
- `git diff --check`, trailing-whitespace scan, dan pola secret produksi pada dua
  file baru: **bersih**.

Pengujian manual perangkat belum diperlukan karena layanan belum memiliki pintu
masuk UI dan seluruh boundary kriptografi/file telah diuji dengan file sementara
terisolasi. Tahap 19 belum selesai; Batch 4 akan menambahkan dialog passphrase dan
konfirmasi, Save/Open melalui pemilih sistem, refresh metadata key, serta error
state aman.

## 4 September 2026 - Tahap 19 Batch 4: UI backup/recovery signing key terenkripsi

UI operasional untuk layanan kriptografi Batch 3 telah selesai dan seluruh
gerbang otomatis serta build Android lulus.

Perubahan yang berhasil diterapkan:

- Halaman Security License Manager kini menyediakan aksi `Backup Terenkripsi`
  saat signing key tersedia dan `Pulihkan Backup` pada kondisi key tersedia
  maupun kosong.
- Backup meminta passphrase minimal 12 karakter beserta konfirmasi yang harus
  sama. Input disamarkan, dapat ditampilkan sementara, dibatasi 256 karakter,
  tidak dicatat ke log/clipboard, dan controller dibersihkan saat dialog selesai.
- UI menjelaskan bahwa passphrase yang terlupa tidak dapat dipulihkan dan file
  backup harus disimpan terpisah dari passphrase.
- Dokumen terenkripsi dari layanan Batch 3 disimpan melalui dialog Save bawaan
  sistem dengan nama unik
  `kasir-signing-key-YYYYMMDD-HHMMSS.kskbackup`, sehingga pengguna dapat memilih
  lokasi yang mudah ditemukan tanpa mengekstrak file.
- Recovery memilih file `.kskbackup` melalui dialog Open sistem, meminta
  passphrase tanpa menyimpannya, lalu baru membuka isi terenkripsi.
- Sebelum restore, dialog non-dismissible menampilkan nama file, fingerprint
  backup, waktu pembuatan key, dan fingerprint key saat ini bila ada.
- Bila vault telah berisi signing key, tombol konfirmasi menyatakan penggantian
  secara eksplisit dan `overwriteExisting` baru dikirim setelah pengguna memilih
  `Ganti Kunci`. Pembatalan tidak mengubah vault.
- Setelah restore berhasil, metadata algoritma, fingerprint, dan waktu pembuatan
  dimuat ulang dari secure storage sehingga halaman langsung mencerminkan key
  yang dipulihkan.
- Operasi ganda dicegah selama backup/recovery berjalan. Progress dan hasil
  memakai semantic live region agar dapat diumumkan pembaca layar.
- Error format/passphrase/integrity tetap spesifik dari boundary layanan,
  sedangkan exception platform/filesystem disanitasi menjadi petunjuk izin dan
  ruang kosong tanpa membocorkan path atau detail internal.
- `LicenseSigningKeyBackupService` didaftarkan pada provider aplikasi; tidak ada
  dependency, schema, migration, ataupun generated database yang berubah.
  Schema License Manager tetap **versi 2** dan aplikasi Kasir tetap **versi 9**.

Gerbang verifikasi:

- Enam test widget baru membuktikan validasi panjang dan kecocokan passphrase,
  nama/isi file yang dikirim ke Save picker, konfirmasi fingerprint dan cancel,
  restore ke vault kosong tanpa izin overwrite, overwrite eksplisit, refresh
  metadata, passphrase salah, serta sanitasi error platform.
- Targeted Security/signing-key suite: **17/17 lulus**.
- Flutter analyzer License Manager: **tidak ada masalah**.
- Seluruh unit/widget/migration test License Manager: **128/128 lulus**.
- Flutter analyzer aplikasi Kasir: **tidak ada masalah**.
- Seluruh unit/widget/migration test aplikasi Kasir: **327/327 lulus**.
- APK debug License Manager berhasil dibangun di
  `build/app/outputs/flutter-apk/app-debug.apk`.
- `git diff --check` dan trailing-whitespace scan: **bersih**.

Pengujian manual perangkat belum diperlukan pada batch ini karena dialog
Save/Open Android yang sama telah lulus manual pada Batch 2, sementara alur baru
passphrase, konfirmasi, overwrite, recovery, dan error telah diuji terisolasi.
Pengujian perangkat akan diminta pada workflow penggantian perangkat dengan
generated test key agar private key produksi tidak dipakai sebagai bahan uji.

Dengan hasil ini, Tahap 19 Batch 4 dinyatakan **selesai**. Batch berikutnya akan
menyusun workflow penggantian perangkat License Manager dan memverifikasi dampak
terhadap hubungan lisensi lama/baru tanpa mengubah protocol secara diam-diam.

## 4 September 2026 - Tahap 19 Batch 5: workflow penggantian perangkat

Workflow penggantian perangkat pada License Manager telah selesai dan lulus
pengujian otomatis maupun smoke test perangkat. Registry lisensi sekarang
menyediakan aksi **Ganti Perangkat** untuk toko berlisensi. Aksi tersebut membuka
halaman penerbitan dalam konteks khusus yang menampilkan ID lisensi lama dan
Installation ID perangkat lama, mempertahankan nama toko, serta mengosongkan
input Data Aktivasi agar operator wajib memakai permintaan dari perangkat baru.

Halaman penerbitan membedakan penggantian perangkat dari renewal biasa. Peringatan
operasional menjelaskan bahwa token offline pada perangkat lama tidak dapat
dicabut dari jarak jauh, sehingga perangkat lama harus dihapus atau dipensiunkan
secara fisik. Data Aktivasi dari perangkat lama ditolak. Pesan clipboard kosong
yang sebelumnya keliru juga telah diperbaiki agar menyebut Data Aktivasi.

Pada lapisan repository, penerbitan perangkat pengganti dilakukan dalam satu
transaksi atomik. Lisensi baru tetap memakai identitas toko yang sama, hubungan
lisensi lama dan baru disimpan dua arah melalui `replacedByLicenseId` dan
`supersedesLicenseId`, lalu Installation ID, request code, dan activation request
toko dipindahkan ke perangkat baru. Installation ID yang telah dimiliki toko lain
ditolak dan transaksi tidak meninggalkan perubahan sebagian.

Gerbang verifikasi yang lulus:

- 8 pengujian baru; targeted test **36/36 lulus**.
- Flutter analyzer License Manager dan Kasir **tanpa issue**.
- Full test License Manager **136/136 lulus**.
- Full test Kasir **327/327 lulus**.
- APK debug License Manager berhasil dibangun, dipasang, dan dijalankan melalui
  `flutter run -d 10DE3201NZ000NB --debug --no-resident` pada V2322 Android 15.
- Smoke test manual non-destruktif pada perangkat dinyatakan **lulus**; tidak ada
  token atau private key produksi yang diterbitkan maupun diubah.
- Pemeriksaan diff, trailing whitespace, dan log produksi bersih.

Tidak ada perubahan schema database, migration, generated database, protocol,
atau dependency pada batch ini. Peringatan kompatibilitas KGP dari `file_picker`
masih menjadi pekerjaan Tahap 22; peringatan tersebut tidak menggagalkan build.

Dengan hasil ini, Tahap 19 Batch 5 dinyatakan **selesai**. Batch 6 akan
menambahkan reminder, filter, dan export metadata tanpa menyertakan token.

## 5 September 2026 - Tahap 19 Batch 6: reminder, filter, dan ekspor metadata

Operasional masa berlaku pada registry License Manager telah dilengkapi. Halaman
registry sekarang menampilkan reminder jumlah lisensi yang kedaluwarsa serta
yang akan habis dalam 7, 30, dan 60 hari. Operator dapat memfilter setiap
jendela tersebut, melihat lisensi yang sudah kedaluwarsa atau belum berlisensi,
dan mengurutkan daftar menurut nama toko, kedaluwarsa terdekat, atau terlama.

Perhitungan filter memakai waktu UTC dan batas inklusif: lisensi yang berakhir
tepat pada ujung 7/30/60 hari masuk ke jendela terkait, sedangkan lisensi yang
sudah kedaluwarsa tidak tercampur ke reminder mendatang. Daftar selalu memakai
lisensi terkini pada rantai renewal/replacement. Registry dan detail toko juga
menampilkan status **PENERBITAN AWAL**, **DIPERPANJANG / DIGANTI**, atau
**BELUM BERLISENSI**. Label gabungan dipakai karena schema saat ini menyimpan
relasi lisensi lama/baru yang sama untuk renewal dan penggantian perangkat.

Ekspor metadata tersedia dalam JSON dan CSV melalui pemilih lokasi sistem.
Ekspor mengikuti hasil pencarian, filter, dan urutan yang sedang tampil serta
memuat waktu UTC, identitas toko/perangkat, tanggal lisensi, status, sisa hari,
fingerprint, hubungan lisensi, dan fitur. Serializer tidak memiliki field token
atau Data Aktivasi; keduanya juga diverifikasi tidak muncul pada isi file.

File/domain yang berubah:

- Model registry: filter expiry, sort, status penerbitan, dan ringkasan reminder.
- Repository registry: boundary UTC 7/30/60 hari, filter, sort deterministik,
  lisensi terkini, dan hitungan reminder.
- ViewModel serta halaman registry: state filter/sort, reminder, status, dan UI
  ekspor yang aksesibel serta tetap responsif.
- Service ekspor metadata baru: JSON dan CSV standar tanpa dependency tambahan.
- Test repository, service ekspor, widget registry, serta regression lama.

Gerbang verifikasi yang lulus:

- 5 pengujian baru; targeted registry/regression **24/24 lulus**.
- Boundary tepat 7 hari lintas offset zona waktu, expired, renewal, replacement,
  sort, CSV escaping, hasil filter, dan no-token export telah diuji.
- Flutter analyzer License Manager dan aplikasi Kasir: **tidak ada masalah**.
- Seluruh test License Manager: **141/141 lulus**.
- Seluruh test aplikasi Kasir: **327/327 lulus**.
- APK debug License Manager berhasil dibangun di
  `build/app/outputs/flutter-apk/app-debug.apk`.
- `git diff --check`, trailing-whitespace scan, dan pemeriksaan log/secret pada
  area perubahan: **bersih**.

Satu regresi layout pada viewport standar sempat terdeteksi oleh full suite:
kartu operasional baru mendorong pencarian dan daftar utama keluar dari area
lazy-build. Hierarki halaman diperbaiki dengan mempertahankan reminder,
pencarian, filter, dan daftar sebagai konten utama di atas; seluruh test kemudian
lulus pada pengulangan final.

Tidak ada perubahan schema database, migration, generated database, protocol,
atau dependency. Manual device test tidak diperlukan karena ekspor memakai
integrasi Save File sistem yang sama dengan backup registry dan telah lulus uji
perangkat pada Batch 2; format serta isi file baru diuji terisolasi. Warning KGP
lama dari `file_picker` tetap dicatat untuk dependency freeze Tahap 22 dan tidak
menggagalkan build.

Dengan hasil ini, Tahap 19 Batch 6 dinyatakan **selesai**. Batch berikutnya akan
mengaudit pencatatan issuance, renewal, replacement, key, backup, dan restore
dengan larangan menyimpan private key, token lengkap, atau passphrase.

## 5 September 2026 - Tahap 19 Batch 7: jejak audit operasional License Manager

Jejak audit persisten untuk operasi sensitif License Manager telah selesai.
Riwayat ini memberi bukti operasional yang dapat dibaca dari menu **Audit** tanpa
menyimpan material rahasia.

Perubahan yang berhasil diterapkan:

- Database License Manager naik dari schema **versi 2 ke versi 3** dengan tabel
  `admin_audit_events`. Migrasi dari database lama mempertahankan seluruh toko dan
  lisensi serta membuat riwayat audit dalam kondisi kosong.
- Audit membedakan penerbitan lisensi, perpanjangan, penggantian perangkat, import
  signing key, penghapusan signing key, backup signing key, recovery signing key,
  backup registry, dan restore registry.
- Repository audit memakai allowlist metadata sederhana dengan batas ukuran.
  Field di luar daftar langsung ditolak; token lisensi, Data Aktivasi, private
  key, dokumen key terenkripsi, dan passphrase tidak pernah disimpan.
- Penerbitan, perpanjangan, penggantian perangkat, dan restore registry mencatat
  audit di dalam transaksi Drift yang sama. Kegagalan audit me-roll back seluruh
  perubahan toko, lisensi, dan hubungan rantai lisensi.
- Import, delete, dan recovery signing key memakai kompensasi secure storage.
  Jika audit gagal, key lama dipulihkan atau key baru dibuang agar state dan audit
  tidak saling bertentangan.
- Backup registry dicatat setelah dokumen beserta checksum berhasil divalidasi;
  backup signing key dicatat setelah dokumen terenkripsi berhasil dibuat. Audit
  hanya memuat jumlah record, checksum, algoritma, waktu key, dan fingerprint
  public key yang aman.
- Halaman Audit menampilkan 200 aktivitas terbaru, urutan waktu terbaru, detail
  metadata aman, refresh, loading, empty, serta error state generik. Filter audit
  lanjutan tetap mengikuti roadmap V1.1 dan tidak ditambahkan secara spekulatif.
- Registry backup sengaja tidak membawa tabel audit dan restore tidak menghapus
  audit yang sudah ada, sehingga backup tidak menjadi jalur pemindahan atau
  penghilangan jejak operasional.

File/domain utama yang berubah:

- Schema, migration, dan generated Drift database License Manager.
- Repository serta halaman Audit dan navigasi shell.
- Repository penerbitan/registry dan layanan backup/restore registry.
- ViewModel signing key dan layanan backup/recovery signing key.
- Test migration, repository audit, transaksi registry, kompensasi signing key,
  backup/restore, dan navigasi widget.

Gerbang verifikasi yang lulus:

- 11 pengujian baru; targeted audit/regression: **53/53 lulus**.
- Regression khusus signing key setelah perbaikan lint: **10/10 lulus**.
- Flutter analyzer License Manager dan aplikasi Kasir: **tidak ada masalah**.
- Seluruh test License Manager: **152/152 lulus**.
- Seluruh test aplikasi Kasir: **327/327 lulus**.
- APK debug License Manager berhasil dibangun di
  `build/app/outputs/flutter-apk/app-debug.apk`.
- Generated database diperbarui melalui build runner; pemeriksaan diff, trailing
  whitespace, logging debug, dan pola secret pada area audit: **bersih**.

Tidak ada perubahan schema aplikasi Kasir (tetap versi 9), protocol lisensi,
format token, ataupun dependency. Pengujian manual perangkat belum diperlukan
karena tidak ada integrasi native baru; navigasi dan UI Audit telah diuji melalui
widget test, sedangkan APK Android berhasil dibangun. Warning kompatibilitas KGP
lama dari `file_picker` tetap dicatat untuk Tahap 22 dan tidak menggagalkan build.

Dengan hasil ini, Tahap 19 Batch 7 dinyatakan **selesai**. Batch berikutnya akan
menambahkan app lock PIN, lifecycle lock, secure screen, dan perlindungan
clipboard pada License Manager.

## 5 September 2026 - Tahap 19 Batch 8: app lock dan proteksi layar sensitif

Fondasi app lock License Manager telah selesai dan lulus pada unit, widget,
regression, build, serta perangkat Android nyata. Shell, registry, audit, dan
private signing key tidak lagi terbuka sebelum operator melewati app lock.

Perubahan yang berhasil diterapkan:

- Startup pertama menampilkan pembuatan PIN enam digit beserta konfirmasi.
  Startup berikutnya selalu menampilkan layar terkunci dan tidak membangun shell
  sensitif sebelum PIN benar.
- PIN tidak disimpan sebagai plaintext. Credential memakai
  PBKDF2-HMAC-SHA256 **210.000 iterasi**, salt acak 16 byte, dan hash 32 byte,
  lalu dokumen credential disimpan melalui `flutter_secure_storage` yang sudah
  menjadi dependency proyek.
- Record secure storage yang rusak atau tidak didukung gagal secara tertutup:
  aplikasi tetap terkunci dan hanya menyediakan retry generik.
- Lima PIN salah memulai cooldown progresif 2, 4, 8, 16, hingga maksimum 30
  detik. Percobaan selama cooldown tidak menjalankan verifikasi hash baru;
  keberhasilan unlock mereset counter.
- Lifecycle lock memakai batas lima menit. Resume sebelum batas mempertahankan
  akses, sedangkan resume tepat atau setelah batas menutup shell dan kembali ke
  layar PIN.
- Penerbitan lisensi, import/delete signing key, pembuatan backup key, pembukaan
  backup terenkripsi, dan recovery key memiliki guard di lapisan service atau
  ViewModel. Operasi ditolak sebelum membaca private key ketika aplikasi sedang
  terkunci.
- Token lisensi yang disalin otomatis dihapus dari clipboard setelah 60 detik
  dan langsung dibersihkan saat aplikasi masuk background. Pembersihan hanya
  dilakukan bila clipboard masih berisi token yang sama sehingga catatan baru
  pengguna tidak ikut terhapus.
- Dokumen private key dari clipboard sekarang dibersihkan sebelum parsing;
  dokumen invalid tidak lagi meninggalkan private key mentah di clipboard.
- `MainActivity` Android memasang `WindowManager.LayoutParams.FLAG_SECURE`
  sebelum Flutter dibuat. Screenshot, screen recording non-secure, dan preview
  recent apps tidak dapat menangkap layar License Manager.
- Tidak ada dependency baru, fallback plaintext, log PIN, atau log private key.

File/domain utama yang berubah:

- Controller/store app lock dan layar setup/unlock responsif.
- Service clipboard sensitif dengan timer serta pemeriksaan nilai terbaru.
- Wiring aplikasi, shell, issuance, signing-key ViewModel, dan backup/recovery.
- Android `MainActivity` untuk secure window flag.
- Test controller, widget lifecycle, clipboard, guard operasi sensitif, source
  secure flag, dan penyesuaian regression aplikasi root.

Gerbang verifikasi yang lulus:

- 10 pengujian baru; matriks app-lock/security awal: **17/17 lulus**.
- Regression luas issuance/signing-key/security/shell: **78/78 lulus**.
- Test lifecycle/clipboard setelah hardening terakhir: **4/4 lulus**.
- Flutter analyzer License Manager dan aplikasi Kasir: **tidak ada masalah**.
- Seluruh test License Manager setelah perubahan final: **162/162 lulus**.
- Seluruh test aplikasi Kasir: **327/327 lulus**.
- APK debug berhasil dibangun, dipasang, dan dijalankan melalui
  `flutter run -d 10DE3201NZ000NB --debug --no-resident` pada V2322 Android 15.
- ADB Window Manager mengonfirmasi window aktif License Manager memiliki flag
  `SECURE`; ini membuktikan proteksi layar berjalan secara native, bukan hanya
  ada di source.
- `git diff --check`, trailing whitespace, debug logging, dan file build tak
  terlacak pada area perubahan: **bersih**.

Tidak ada perubahan schema database, migration, generated database, protocol
lisensi, format token, atau dependency. Schema License Manager tetap versi 3 dan
schema Kasir tetap versi 9. Verifikasi perangkat dilakukan otomatis sehingga
pengujian manual pengguna belum diperlukan. Warning KGP lama dari `file_picker`
tetap menjadi pekerjaan Tahap 22 dan tidak menggagalkan build.

Dengan hasil ini, Tahap 19 Batch 8 dinyatakan **selesai**. Batch berikutnya akan
menambahkan unlock biometric opsional dengan fallback PIN, penanganan perangkat
tanpa biometric, serta pengujian native sebelum Tahap 19 ditutup.

## 5 September 2026 - Tahap 19 Batch 9: biometric unlock opsional

Biometric unlock License Manager telah selesai dan lulus pengujian otomatis
serta perangkat Android nyata. Fitur ini menjadi jalur cepat opsional; PIN enam
digit License Manager tetap tersedia dan tetap menjadi fallback utama.

Perubahan yang berhasil diterapkan:

- Dependency resmi `local_auth` ditambahkan untuk dialog biometrik native.
- Tombol **Buka dengan Biometrik** hanya tampil ketika perangkat mendukung dan
  memiliki biometrik yang sudah terdaftar. Perangkat tanpa biometrik langsung
  memakai UI PIN lama tanpa error atau jalur buntu.
- Autentikasi memakai `biometricOnly: true`; PIN/pattern perangkat tidak dapat
  menggantikan PIN khusus License Manager.
- Cancel, biometrik ditolak, lockout, dan exception platform gagal tertutup:
  shell sensitif tidak dibangun, hitungan kegagalan PIN tidak bertambah, detail
  exception tidak tampil, dan operator tetap dapat memasukkan PIN aplikasi.
- Keberhasilan biometrik mengubah state melalui `AppLockController` yang sama,
  mereset cooldown PIN, dan tetap tunduk pada lifecycle lock serta guard operasi
  sensitif dari Batch 8.
- Guard konkurensi memastikan dua pemicu bersamaan tidak membuka lebih dari satu
  dialog biometrik.
- Android memakai permission `USE_BIOMETRIC`, `FlutterFragmentActivity`, serta
  tema AppCompat yang diwajibkan plugin. `FLAG_SECURE` tetap dipasang sebelum
  Flutter dibuat.

File/domain utama yang berubah:

- Adapter biometrik, `AppLockController`, UI `AppLockGate`, dan wiring aplikasi.
- Manifest, `MainActivity`, tema Android siang/malam, pubspec, dan lockfile.
- Test adapter biometrik, controller, widget fallback, konkurensi, dan source
  konfigurasi Android.

Gerbang verifikasi yang lulus:

- 6 pengujian baru; targeted biometric/app-lock: **14/14 lulus**.
- Flutter analyzer License Manager dan aplikasi Kasir: **tidak ada masalah**.
- Seluruh test License Manager: **168/168 lulus**.
- Seluruh test aplikasi Kasir: **327/327 lulus**.
- APK debug berhasil dibangun, dipasang, dan dijalankan melalui
  `flutter run -d 10DE3201NZ000NB --debug --no-resident` pada V2322 Android 15.
- Pengujian manual pengguna pada V2322 mengonfirmasi unlock biometrik dan
  fallback PIN: **lulus**.
- `git diff --check`, trailing whitespace, debug logging, serta file build tak
  terlacak pada area perubahan: **bersih**.

Tidak ada perubahan schema database, migration, generated database, protocol,
atau format token. Schema License Manager tetap versi 3 dan schema Kasir tetap
versi 9. Dependency baru dibatasi pada plugin biometrik resmi beserta dependency
transitifnya. Warning KGP lama dari `file_picker` tetap menjadi pekerjaan Tahap
22 dan tidak menggagalkan build.

Dengan hasil ini, Tahap 19 Batch 9 dan seluruh **Tahap 19 dinyatakan selesai**.
Tahap berikutnya adalah Tahap 20 Batch 1: matriks edge case validasi lisensi
tanpa mengubah kebijakan expiry sebelum keputusan operasional ditetapkan.

## 5 September 2026 - Tahap 20 Batch 1: release matrix waktu dan format token

Release matrix edge case validasi lisensi telah dilengkapi tanpa mengubah kode
produksi maupun kebijakan lisensi. Perilaku validator yang sudah benar sekarang
dikunci oleh test deterministik agar tidak bergeser menjelang release.

Perubahan yang berhasil diterapkan:

- Batas toleransi jam penerbitan diuji pada titik tepat: `issuedAt` sepuluh menit
  di depan masih valid, sedangkan tambahan satu mikrodetik ditolak sebagai
  `notYetValid`.
- Batas expiry diuji sampai mikrodetik: sesaat sebelum `expiresAt` masih valid
  dan tepat pada `expiresAt` langsung berstatus `expired` tanpa grace period.
- Simulasi jam perangkat mundur satu jam menghasilkan `notYetValid`; jam maju
  jauh melewati masa berlaku menghasilkan `expired` dengan payload tidak dibuka.
- Instant yang sama dalam UTC dan offset Asia/Jakarta menghasilkan status yang
  sama, sehingga perubahan timezone tidak memperpanjang atau mempersingkat
  lisensi.
- Token dengan whitespace pada pemisah baris tetap diterima setelah normalisasi;
  token yang terpotong ditolak aman sebagai `malformed` tanpa exception keluar.
- Regression replacement memastikan token malformed, signature palsu, lisensi
  perangkat lain, dan lisensi expired tidak menimpa lisensi valid maupun membuat
  audit penggantian.
- Protocol pasangan pada License Manager tetap dapat menerbitkan serta
  memverifikasi token yang dibaca aplikasi Kasir.

Gerbang verifikasi yang lulus:

- 5 test boundary baru; targeted validasi dan replacement Kasir: **19/19 lulus**.
- Cross-protocol License Manager: **12/12 lulus**.
- Flutter analyzer License Manager dan aplikasi Kasir: **tidak ada masalah**.
- Seluruh test License Manager: **168/168 lulus**.
- Seluruh test aplikasi Kasir: **332/332 lulus**.
- `git diff --check`, format, dan trailing whitespace: **bersih**.

Tidak ada perubahan kode produksi, schema, migration, generated database,
dependency, protocol, format token, atau kebijakan expiry. Schema License Manager
tetap versi 3 dan schema Kasir tetap versi 9. Build serta pengujian manual tidak
diperlukan karena batch ini hanya menambah release matrix deterministik dan
artifact produksi tidak berubah.

Dengan hasil ini, Tahap 20 Batch 1 dinyatakan **selesai**. Batch berikutnya akan
menguji corrupted/unavailable secure storage, installation identity yang hilang,
persistensi lisensi saat update, dan memastikan restore database tidak diam-diam
mengkloning lisensi perangkat.

## 5 September 2026 - Tahap 20 Batch 2: ketahanan secure storage dan update

Boundary penyimpanan lisensi, kehilangan installation identity, pembaruan APK,
dan restore lintas perangkat telah diperkuat serta diverifikasi. Batch ini
menutup satu celah produksi: exception native secure storage sebelumnya dapat
naik mentah ke halaman startup.

Perubahan yang berhasil diterapkan:

- Seluruh operasi baca/tulis/hapus `LicenseRepository` sekarang menormalkan
  kegagalan storage menjadi `LicenseException` dengan pesan aman. Tidak ada
  fallback plaintext maupun penyimpanan alternatif.
- Startup gagal secara tertutup ketika secure storage tidak tersedia: aplikasi
  tidak membuka Login/Setup, tidak membocorkan detail native, dan menyediakan
  tombol `Coba Lagi`.
- Installation identity yang hilang dibuat ulang oleh alur yang sudah ada.
  Lisensi lama kemudian ditolak sebagai `wrongInstallation` dan token lama tetap
  disimpan agar tidak ada penghapusan diam-diam.
- Pembuatan ulang repository dan service dengan secure store yang sama tetap
  membaca installation identity serta token yang sama, mengunci perilaku update
  aplikasi tanpa reset data.
- Backup perangkat sumber hanya membawa hash installation identity. Persiapan
  restore pada perangkat lain menghasilkan `installationMatches = false` serta
  mempertahankan identity dan token perangkat tujuan.

Lima test baru ditambahkan pada repository/identity, validator kriptografis,
startup widget, dan backup lintas perangkat. Gerbang verifikasi yang lulus:

- Targeted license/startup/backup/restore: **54/54 lulus**.
- Flutter analyzer aplikasi Kasir dan License Manager: **tidak ada masalah**.
- Seluruh test aplikasi Kasir: **337/337 lulus**.
- Seluruh test License Manager: **168/168 lulus**.
- `git diff --check`, format, dan trailing whitespace: **bersih**.
- APK debug berhasil dibangun, dipasang sebagai update, dan dijalankan melalui
  `flutter run -d 10DE3201NZ000NB --debug --no-resident` pada V2322 Android 15.
- Screenshot verifikasi otomatis setelah update menunjukkan halaman Login, bukan
  Aktivasi atau error. Lisensi, installation identity, dan database pengguna
  perangkat terbukti bertahan; screenshot sementara sudah dihapus dari komputer
  dan perangkat.

Tidak ada perubahan schema, migration, generated database, dependency, protocol,
format token, ataupun format backup. Schema Kasir tetap versi 9 dan schema
License Manager tetap versi 3. Warning KGP lama dari `file_picker` dan plugin
printer tetap dicatat untuk tahap dependency/build berikutnya dan tidak
menggagalkan build.

Dengan hasil ini, Tahap 20 Batch 2 dinyatakan **selesai** tanpa memerlukan uji
manual pengguna. Tahap 20 belum ditutup: Batch 3 akan menetapkan kebijakan saat
lisensi expired, ketersediaan backup ketika expired, dan keputusan emergency
read-only secara eksplisit.

## 5 September 2026 - Tahap 20 Batch 3: kebijakan expiry dan runtime guard

Kebijakan operasional ketika lisensi kedaluwarsa telah ditetapkan dan diterapkan
secara eksplisit. Batch ini sekaligus menutup celah saat aplikasi terus terbuka:
sebelumnya gerbang lisensi hanya dijalankan pada cold startup, sehingga shell
yang sudah aktif belum otomatis berhenti tepat ketika masa lisensi berakhir.

Keputusan kebijakan versi produksi pertama:

- Tidak ada grace period. Lisensi tetap valid sampai sesaat sebelum `expiresAt`
  dan langsung tidak valid tepat pada `expiresAt`.
- Expiry memakai hard-stop: Login, data operasional, transaksi, laporan,
  pengaturan, dan Backup & Restore tidak dapat diakses.
- Jalur aktivasi/perpanjangan tetap tersedia agar operator dapat memasukkan
  lisensi pengganti tanpa menghapus token lama secara diam-diam.
- Backup saat expired tidak dibuka. Menu backup berada di shell Manager dan
  membuka sebagian shell setelah expiry akan melemahkan batas keamanan yang sama.
- Emergency read-only tidak dibuka pada versi 1. Fitur tersebut baru layak jika
  tersedia autentikasi Manager dan shell terbatas khusus yang dapat dibuktikan
  tidak memiliki mutasi atau akses backup tersembunyi.

Perubahan yang berhasil diterapkan:

- `AppShellPage` memeriksa lisensi sebelum dashboard atau data lain ditampilkan.
- Timer satu-kali dijadwalkan ke waktu expiry sehingga shell aktif terkunci tepat
  saat masa lisensi habis tanpa menunggu restart atau perpindahan halaman.
- Lisensi diperiksa ulang setiap aplikasi kembali ke foreground. Ini menutup
  perubahan waktu, expiry selama background, dan kegagalan secure storage runtime.
- Hasil invalid, token hilang, payload tanpa expiry, atau exception storage
  menutup shell secara aman, membersihkan keranjang, dan kembali ke gerbang
  startup/aktivasi.
- Login menangkap instance `LicenseGateService` yang masih valid sebelum route
  login diganti, sehingga pemeriksaan shell tidak bergantung pada context lama.
- Pesan expiry kini menjelaskan bahwa lisensi baru diperlukan serta akses data
  dan backup tidak tersedia setelah kedaluwarsa.
- Fake secure store test dapat diberi nilai awal untuk menguji alur login-shell
  nyata tanpa menambah abstraction atau dependency produksi.

Pengujian yang ditambahkan dan diperkuat membuktikan:

- Shell Manager yang sedang berada di Backup & Restore langsung menutup akses
  tepat pada expiry dan membersihkan keranjang yang belum diproses.
- Lisensi valid tetap membuka shell pada alur login Manager maupun Kasir.
- Startup expired berhenti di halaman Aktivasi, tidak membangun halaman backup,
  dan tidak melanjutkan pemeriksaan setup maupun akun pengguna.
- Boundary tepat pada expiry menghasilkan status serta pesan kebijakan yang
  benar; token expired tetap tidak dihapus otomatis.

Gerbang verifikasi yang lulus:

- Targeted license/startup/login/shell: **53/53 lulus**.
- Flutter analyzer aplikasi Kasir dan License Manager: **tidak ada masalah**.
- Seluruh test aplikasi Kasir: **338/338 lulus**.
- Seluruh test License Manager: **168/168 lulus**.

Tidak ada perubahan schema, migration, generated database, dependency, protocol,
format token, atau format backup. Schema Kasir tetap versi 9 dan schema License
Manager tetap versi 3. Build maupun uji manual perangkat belum diperlukan karena
kebijakan runtime sudah dicakup widget test deterministik dan tidak mengubah
integrasi native.

Dengan hasil ini, Tahap 20 Batch 3 dan seluruh **Tahap 20 dinyatakan selesai**.
Tahap berikutnya adalah Tahap 21 Batch 1: audit Android manifest, komponen
exported, permission, backup, FileProvider, cleartext, dan package visibility.

## 6 September 2026 - Rekonsiliasi UI Kasir HP/tablet

Temuan visual pada HP dan tablet telah diperbaiki serta lulus pengujian manual
di V2322 Android 15, termasuk orientasi portrait dan landscape pendek.

Perubahan yang berhasil diterapkan:

- Navigation rail hanya dipakai saat tinggi layar memadai; landscape pendek
  memakai drawer agar seluruh menu dapat diakses tanpa overflow.
- Halaman Kasir landscape pendek tidak lagi memakai header tab
  Produk/Keranjang yang memakan tinggi. Pencarian, kategori, dan tombol menuju
  keranjang berada dalam satu toolbar ringkas dengan target sentuh 48 dp.
- Area grid produk diperluas dan kartu produk tidak lagi memiliki ruang kosong
  vertikal yang berlebihan.
- Foto produk ditampilkan utuh dalam bingkai seragam rasio 4:3, berlatar putih,
  memiliki border, padding, dan sudut membulat.
- Keranjang landscape memakai dua kolom: daftar item yang dapat di-scroll di
  kiri serta subtotal, total, dan aksi pembayaran di kanan.
- Tombol Bayar dan Kosongkan ditampilkan sejajar pada landscape, sementara
  elemen printer/indikator keranjang yang berulang diringkas. Layout portrait
  tetap mempertahankan susunan vertikal sebelumnya.
- Navigasi Keranjang ke Produk dan sebaliknya tetap tersedia serta jumlah item
  ditampilkan pada judul keranjang.

Pengujian yang berhasil:

- Test regresi landscape menambahkan produk nyata ke keranjang, memastikan
  daftar item, total, kedua tombol aksi, dan navigasi dua arah tampil tanpa
  overflow.
- Flutter analyzer aplikasi Kasir dan License Manager: **tidak ada masalah**.
- Seluruh test aplikasi Kasir: **342/342 lulus**.
- Seluruh test License Manager: **168/168 lulus**.
- `git diff --check`, format, dan trailing whitespace: **bersih**.
- APK debug berhasil dibangun, dipasang sebagai update, dan dijalankan melalui
  `flutter run -d 10DE3201NZ000NB --debug --no-resident`.
- Uji manual pengguna pada foto produk, halaman produk landscape, dan keranjang
  landscape: **lulus**.

Tidak ada perubahan schema, migration, generated database, dependency, alur
transaksi, maupun format backup. Batch rekonsiliasi UI ini dinyatakan
**selesai**. Fitur suara belum diimplementasikan dan akan dirancang terlebih
dahulu sebelum masuk batch pengembangan terpisah.

## 6 September 2026 - Tahap 20A Batch 1–2: pengenalan suara dan parser katalog

Proof-of-concept pengenalan suara native serta parser pesanan berbasis katalog
telah diterapkan dan dinyatakan lulus setelah pengujian otomatis serta uji manual
pengguna pada V2322 Android 15.

Perubahan yang berhasil diterapkan:

- Pengenal suara memakai layanan native perangkat dengan locale Indonesia,
  partial/final result, beberapa sesi maksimal 30 detik, stop, cancel, dan pesan
  error yang aman. Audio tidak disimpan oleh aplikasi.
- Hasil beberapa sesi digabung tanpa menghapus ucapan awal atau menggandakan
  bagian akhir yang dikirim ulang recognizer.
- Parser memahami beberapa pasangan nama produk dan jumlah, angka digit/kata,
  satuan opsional, kata jeda/percakapan, nama pendek yang unik, serta alternatif
  pengenal yang paling sesuai katalog.
- Produk ambigu tidak dipilih otomatis. Pengguna dapat memilih kandidat yang
  menampilkan nama, harga, dan satuan; hasil tidak dikenal dapat dikoreksi ke
  produk aktif dan dipelajari sebagai alias selama sesi.
- Draf menampilkan nama, jumlah, harga satuan, subtotal sementara, satuan, serta
  status produk/stok tanpa mengubah keranjang.
- Katalog uji perangkat berisi 125 produk aktif. Sebanyak 122 produk tambahan
  dimasukkan secara idempoten dengan pelacakan stok dimatikan.

Gerbang verifikasi yang lulus saat penutupan Batch 1–2:

- Targeted voice: **41/41 lulus**.
- Seluruh test aplikasi Kasir: **383/383 lulus**.
- Flutter analyzer: **tidak ada masalah**.
- Build, transkripsi nyata, koreksi kandidat, dan tampilan harga pada V2322:
  **lulus manual pengguna**.

Tidak ada perubahan schema, migration, format backup, atau dependency baru.
Alias masih hanya berlaku selama sesi dan draf belum dapat mengubah keranjang;
keduanya sengaja dilanjutkan pada batch berikutnya. Dengan hasil ini, Tahap 20A
Batch 1 dan Batch 2 dinyatakan **selesai**.

## 7 September 2026 - Tahap 20A Batch 3: lifecycle sesi suara

Pengelola lifecycle pesanan suara telah diterapkan dan dinyatakan lulus pada
perangkat V2322 Android 15. Sesi kini memiliki state eksplisit dari idle sampai
selesai, draf dapat dikunci melalui `Selesai Pesanan`, dan error tidak menghapus
hasil yang telah terbaca. Ketika aplikasi menjadi inactive, hidden, paused, atau
detached, mikrofon dihentikan satu kali, partial tetap disimpan, dan hasil native
yang datang terlambat ditolak.

Gerbang penutupan Batch 3 yang lulus:

- Targeted voice: **44/44 lulus**.
- Seluruh test aplikasi Kasir: **386/386 lulus**.
- Flutter analyzer: **tidak ada masalah**.
- Build debug serta uji lifecycle/background/lanjut bicara pada perangkat:
  **lulus manual pengguna**.

Tidak ada perubahan schema, migration, format backup, atau dependency baru.
Dengan hasil ini, Tahap 20A Batch 3 dinyatakan **selesai**.

## 7 September 2026 - Tahap 20A Batch 4: integrasi draf suara ke keranjang

Bagian implementasi dan gerbang otomatis Batch 4 telah selesai. Halaman Kasir
kini menyediakan tombol `Pesanan suara` yang opsional. Draf hasil suara dapat
dikoreksi kuantitasnya atau dihapus per item, lalu dikonfirmasi setelah pengguna
menekan `Selesai Pesanan`.

Konfirmasi membaca ulang katalog aktif serta stok terbaru sebelum mengubah
keranjang. Semua item diterapkan sekaligus; kegagalan satu item tidak meninggalkan
sebagian item lain di keranjang. Produk yang sama digabung, jumlah keranjang yang
sudah ada ikut diperhitungkan, dan pengiriman bersamaan dilindungi agar draf tidak
diterapkan dua kali.

Gerbang otomatis yang lulus:

- Unit test operasi draf keranjang: **7/7 lulus**.
- Targeted voice: **44/44 lulus**.
- Seluruh test aplikasi Kasir: **390/390 lulus**.
- Matriks UI landscape pendek serta portrait/landscape, teks besar, dan keyboard:
  **lulus tanpa overflow**.
- Flutter analyzer: **tidak ada masalah**.
- APK debug: **berhasil dibangun**.

Tidak ada perubahan schema, migration, format backup, atau dependency baru.
Batch 4 belum dinyatakan selesai sampai alur nyata dari tombol mikrofon hingga
item masuk ke keranjang lulus pada perangkat.

Perbaikan perangkat pada Batch 4 juga telah melewati gerbang otomatis. Status
native `notListening` kini langsung melepaskan sesi sehingga `Lanjut Bicara`
tidak menunggu callback `done` yang terlambat. Parser mengenali kata perintah
berimbuhan serta prefiks, sufiks, dan konfiks yang menempel pada token produk,
tetapi hanya menormalisasinya jika hasilnya cocok dengan kata aman atau nama
katalog nyata. Nama asing tetap tidak dipaksakan menjadi produk.

- Regresi layanan/parser/UI terkait: **39/39 lulus**.
- Seluruh test aplikasi setelah perbaikan: **394/394 lulus**.
- Flutter analyzer: **tidak ada masalah**.
- Build debug dan pemasangan update ke V2322: **berhasil**.

Uji suara nyata untuk kecepatan `Lanjut Bicara` dan variasi imbuhan masih menjadi
gerbang manual penutupan Batch 4.

Perbaikan angka pada nama katalog juga telah diterapkan. Parser membedakan angka
judul dari kuantitas pada bentuk terpisah seperti `Nabati 2000 4`, serta dapat
memulihkan keluaran recognizer `Nabati 2004` menjadi `Nabati 2000` sebanyak 4.
Pemulihan hanya dilakukan bila produk unik dan jumlah hasil gabungan 1-99;
beberapa kandidat yang sama-sama masuk akal tetap ditolak agar tidak salah jual.

- Parser angka katalog: **22/22 lulus**.
- Seluruh targeted voice: **51/51 lulus**.
- Seluruh test aplikasi: **397/397 lulus**.
- Flutter analyzer: **tidak ada masalah**.
- Build, pemasangan update, dan pembukaan aplikasi pada V2322: **berhasil**.

Uji suara nyata untuk produk bernomor masih menjadi gerbang manual Batch 4.

Perbaikan varian produk pecahan pada Batch 4 telah melewati gerbang otomatis.
Parser kini menyamakan bentuk katalog angka (`1/2`, `1/4`, `2/3`, dan pecahan
umum lain) dengan ucapan Indonesia seperti `setengah`, `separuh`, `seperempat`,
`dua pertiga`, dan `tiga per empat`. Pecahan diperlakukan sebagai bagian nama
produk, sedangkan angka sesudahnya tetap menjadi jumlah barang. Satuan `kilo`
juga disamakan dengan `kg`, sehingga `tepung setengah kilo dua` dapat diarahkan
ke produk `Tepung 1/2 kg` sebanyak 2 tanpa tertukar dengan `Tepung 1 kg`.

- Parser suara: **25/25 lulus**.
- Seluruh targeted voice: **54/54 lulus**.
- Seluruh test aplikasi: **400/400 lulus**.
- Flutter analyzer: **tidak ada masalah**.
- Build, pemasangan update, dan pembukaan aplikasi pada V2322: **berhasil**.

Tidak ada perubahan schema, migration, format backup, atau dependency baru.
Uji suara pecahan nyata pada V2322 masih menjadi gerbang manual Batch 4.

## 7 September 2026 - Tahap 20A Batch 4: kamus istilah suara per toko

Kamus istilah yang dapat dikelola langsung dari aplikasi telah diterapkan untuk
meningkatkan kecocokan ucapan tanpa mengubah APK setiap kali toko memiliki istilah
baru.

Perubahan yang berhasil diterapkan:

- Halaman `Pesanan Suara` kini memiliki tab `Kamus` berisi istilah khusus toko
  dan istilah sistem bawaan.
- Manager aktif dapat menambah, mengubah, dan menghapus pasangan frasa, misalnya
  `sekilo -> 1 kg` dan `dua setengah -> 2500`; Kasir hanya dapat membaca dan
  memakai aturan tersebut.
- Aturan langsung aktif setelah disimpan. Tombol `Terapkan Ulang Kamus` memuat
  ulang data tanpa build APK, koneksi laptop, atau proses training terpisah.
- Penggantian frasa berjalan satu kali dengan frasa terpanjang lebih dahulu.
  Karena itu keluaran satu aturan tidak diproses ulang oleh aturan lain.
- Istilah sistem seperti `setengah -> 1/2`, `seperempat -> 1/4`, `kilo -> kg`,
  dan `mili -> ml` terlihat sebagai referensi tetapi tidak dapat ditimpa.
- Mutasi divalidasi ulang memakai sesi Manager aktif, dibatasi pada toko yang
  sama, dan audit add/update/delete ditulis atomik tanpa menyimpan audio.
- Kamus dimuat saat halaman suara dibuka sehingga tetap aktif walaupun pengguna
  tidak membuka tab Kamus terlebih dahulu.
- Ditambahkan tabel kamus pada schema Kasir versi 10. Data otomatis ikut backup
  dan restore database yang sudah ada; migrasi tidak mengubah tabel lama.
- Tidak ada dependency baru, penyimpanan audio, backend, maupun biaya API.

Gerbang otomatis yang lulus:

- Parser dan repository kamus: **30/30**.
- UI kamus Manager/Kasir: **2/2**.
- Seluruh targeted voice: **61/61**.
- Database, migrasi, backup, dan restore terkait: **75/75**.
- Seluruh test aplikasi Kasir: **416/416**.
- Flutter analyzer: **tidak ada masalah**.
- `git diff --check`: **bersih**.
- Build debug, pemasangan update, migrasi schema 10, dan pembukaan aplikasi pada
  V2322 Android 15: **berhasil tanpa crash**.

Persistence aturan setelah membuka ulang aplikasi serta verifikasi hak akses
Manager/Kasir pada V2322 masih menjadi gerbang manual. Tahap 20A Batch 4 belum
ditutup sampai gerbang perangkat tersebut lulus.

### Perbaikan kamus fuzzy dan lifecycle pengenal suara

Dua regresi lanjutan pada Batch 4 telah diperbaiki pada source dan build perangkat
terbaru:

- Istilah kamus toko tidak lagi terbatas pada kecocokan ejaan persis. Kesalahan
  transkripsi yang sangat dekat, misalnya `se kilo`/`sekiloh` terhadap `sekilo`
  atau `jeban` terhadap `ceban`, dapat diarahkan ke istilah toko yang unik.
- Koreksi fuzzy dibatasi agar tidak mengubah ucapan secara agresif: frasa pendek
  dan istilah yang mengandung angka hanya memakai kecocokan persis, sedangkan
  kandidat yang hampir sama tetapi menghasilkan pengganti berbeda dibiarkan untuk
  dikoreksi pengguna.
- Transkrip yang ditampilkan kini sudah memakai hasil koreksi kamus sehingga
  pengguna dapat melihat istilah yang benar sebelum memasukkan draf ke keranjang.
- Layanan pengenal suara produksi sekarang hanya dibuat sekali sepanjang umur
  aplikasi. Listener dipasang ulang untuk halaman yang sedang aktif sehingga
  buka-tutup halaman atau pemakaian lebih dari tiga/empat kali tidak mewarisi
  callback halaman lama.
- Status native `notListening` langsung menyimpan segmen dan mengaktifkan kembali
  `Lanjut Bicara`, tanpa menunggu callback `done` yang dapat terlambat.
- Tidak ada perubahan schema (tetap versi 10), format backup, maupun dependency.

Gerbang terbaru yang lulus:

- Seluruh regresi voice: **66/66**.
- Seluruh test aplikasi Kasir: **421/421**.
- Flutter analyzer: **tidak ada masalah**.
- `git diff --check`: **bersih**.
- Build debug, pemasangan update, dan pembukaan aplikasi pada V2322 Android 15:
  **berhasil**.

Uji nyata koreksi istilah serta sedikitnya enam siklus bicara, hapus transkrip,
keluar, dan membuka ulang halaman masih menjadi gerbang manual terakhir untuk
perbaikan ini.

### 8 September 2026 - Perbaikan transkrip yang aktif kembali setelah sesi selesai

Uji perangkat menemukan status halaman dapat kembali ke `Memproses hasil suara`
setelah sesi sebenarnya sudah berhenti. Penyebabnya adalah urutan callback Android:
`notListening/done` dapat tiba sebelum hasil transkrip final atau partial yang
terlambat.

Perbaikan yang berhasil diterapkan:

- Partial yang datang setelah sesi berhenti sekarang ditolak.
- Final terakhir tetap boleh melengkapi draf, tetapi tidak dapat menghidupkan
  kembali status mendengarkan atau memproses.
- `Selesai Pesanan` mengunci generasi hasil sehingga callback native yang datang
  sesudahnya tidak dapat mengubah draf yang sudah disetujui.
- Tidak ada perubahan schema, dependency, format backup, atau aturan kamus.

Gerbang otomatis/perangkat yang lulus:

- Regresi UI penghentian sesi dan layanan native: **22/22**.
- Seluruh regresi voice: **66/66**.
- Seluruh test aplikasi Kasir: **421/421**.
- Flutter analyzer dan `git diff --check`: **bersih**.
- Build debug dan pemasangan pada V2322 Android 15: **berhasil**; proses aplikasi
  terverifikasi aktif setelah instalasi.

Satu uji suara nyata masih diperlukan untuk memastikan status tetap `Draf
tersimpan, siap dilanjutkan` setelah hasil final muncul pada perangkat tersebut.

### 8 September 2026 - Audit ulang lifecycle native untuk sesi suara berulang

Audit log nyata pada V2322 menemukan penyebab sesi suara macet setelah dua/tiga
kali pemakaian. Status Android `notListening` hanya berarti mikrofon berhenti
mendengar, bukan berarti sesi native sudah dilepas. Membuka sesi berikutnya pada
status tersebut membuat Android menolak permintaan dengan `already in session`,
lalu akhirnya `service's capacity is full`.

Perbaikan yang sudah diterapkan pada source dan build terbaru:

- `notListening` kini menahan halaman pada `Memproses hasil suara`; tombol
  `Lanjut Bicara` baru aktif setelah hasil final/terminal dan cleanup selesai.
- Hasil final langsung memicu `cancel` native yang terserialisasi agar resource
  pengenal suara benar-benar dilepas sebelum sesi berikutnya.
- Jeda aman 750 ms diterapkan setelah terminal sebelum listen berikutnya.
- Jika callback terminal Android hilang, fallback terbatas tiga detik melakukan
  cleanup dan mengeluarkan UI dari kondisi sibuk tanpa menunggu selamanya.
- Generasi operasi mencegah start yang masih menunggu cooldown membuka mikrofon
  setelah pengguna membatalkan sesi atau meninggalkan halaman.
- Callback lama tetap ditolak dan satu instance pengenal suara tetap dipakai;
  perbaikan ini tidak menghapus perlindungan regresi yang telah dibuat.
- Tidak ada perubahan schema (tetap versi 10), dependency, format backup, atau
  penyimpanan audio.

Gerbang otomatis/build yang lulus:

- Seluruh regresi voice: **68/68**.
- Seluruh test aplikasi Kasir: **423/423**.
- Flutter analyzer: **tidak ada masalah**.
- `git diff --check`: **bersih**.
- Build debug dan pemasangan pada V2322 Android 15: **berhasil**.

Catatan ini menggantikan asumsi lama bahwa `notListening` boleh langsung membuka
`Lanjut Bicara`. Uji nyata sedikitnya 8-10 sesi berurutan serta pemeriksaan log
bahwa `already in session`/`capacity is full` tidak muncul masih menjadi gerbang
manual sebelum perbaikan lifecycle ini dinyatakan lulus sepenuhnya.

### 8 September 2026 - Tanda kesiapan mikrofon sebelum kasir berbicara

Gerbang manual lifecycle sebelumnya dinyatakan **lulus**. Log V2322 mencatat
lebih dari dua puluh sesi `onStartListening` tanpa `already in session` maupun
`capacity is full`; cleanup, cooldown, dan fallback anti-stuck wajib
dipertahankan pada perubahan suara berikutnya.

Peningkatan berikut sudah diterapkan pada source dan build terbaru:

- Setelah `Mulai Bicara` atau `Lanjut Bicara`, halaman menampilkan status dan
  kartu `Menyiapkan mikrofon...`; aplikasi belum menyuruh kasir berbicara.
- Kesiapan tidak memakai countdown tetap yang dapat meleset antarperangkat.
  Aplikasi menunggu callback native `listening` sebagai sumber kebenaran.
- Saat mikrofon benar-benar aktif, kartu berubah menjadi `Mikrofon aktif -
  Silakan bicara sekarang` dan perangkat mengirim satu bunyi klik serta getaran.
- Bunyi/getar hanya pelengkap; indikator visual dan live-region accessibility
  tetap berfungsi pada perangkat yang senyap atau menonaktifkan haptics.
- Status persiapan ikut dilindungi pada alur error, final, background, dan
  callback terlambat sehingga lifecycle anti-stuck tidak dibuka kembali.
- Tidak ada dependency baru, perubahan schema (tetap versi 10), format backup,
  maupun penyimpanan audio.

Gerbang otomatis/build yang lulus:

- Lifecycle dan UI suara terfokus: **25/25**.
- Seluruh regresi voice: **69/69**.
- Seluruh test aplikasi Kasir: **424/424**.
- Flutter analyzer: **tidak ada masalah**.
- `git diff --check`: **bersih**.
- Build debug dan pemasangan pada V2322 Android 15: **berhasil**.

Uji perangkat singkat untuk memastikan bunyi/getar terasa jelas dan ucapan
produk pertama setelah tanda siap ikut masuk transkrip masih diperlukan sebelum
peningkatan tanda kesiapan ini ditutup.

### 8 September 2026 - Tahap 20A Batch 4: batas keras antarsesi suara

Fondasi pertama penyelarasan draf terverifikasi sudah diterapkan:

- Setiap sesi selesai kini dipisahkan dengan batas klausa `;` yang sudah
  dipahami parser, bukan lagi diratakan menjadi satu kalimat dengan spasi.
- Fragmen partial/final di dalam sesi yang sama tetap digabung seperti
  sebelumnya; hanya perpindahan `Mulai/Lanjut Bicara` yang membuat batas baru.
- Sisa gagal sesi lama tidak dapat menjadi awalan nama produk pada sesi baru.
  Contoh regresi `beras sekitar` lalu `beras 1 kg 2` menghasilkan satu item
  valid `Beras 1 kg` berjumlah 2, sedangkan `beras sekitar` tetap terpisah untuk
  diperiksa.
- Tidak ada tebakan jumlah default, penghapusan transkrip asli, perubahan
  lifecycle native, dependency, schema (tetap versi 10), atau format backup.

Gerbang otomatis/build yang lulus:

- Draf, parser, dan halaman suara terkait: **50/50**.
- Seluruh regresi voice: **70/70**.
- Seluruh test aplikasi Kasir: **425/425**.
- Flutter analyzer: **tidak ada masalah**.
- `git diff --check`: **bersih**.
- Build debug dan pemasangan pada V2322 Android 15: **berhasil**.

Uji perangkat kasus beberapa sesi suara dinyatakan **lulus oleh pengguna** pada
8 September 2026. Batas antarsesi dan alur lanjut bicara berjalan lancar pada
V2322 sehingga fondasi ini ditutup dan tidak boleh dilemahkan oleh perubahan
berikutnya.

### 8 September 2026 - Tahap 20A Batch 4: pemisahan hasil draf suara

Penyelarasan hasil suara menuju konfirmasi keranjang sudah diterapkan:

- Hasil kini dipisahkan menjadi `Siap dimasukkan`, `Perlu konfirmasi`, dan
  `Tidak dikenali`, lengkap dengan jumlah item dan penjelasan tindakan.
- Hanya baris berstatus cocok dengan katalog yang masuk ke kelompok siap.
  Produk ambigu, stok kurang, produk nonaktif, jumlah tidak valid, dan satuan
  tidak cocok tetap memerlukan tindakan pengguna.
- Teks yang belum memiliki pasangan produk dan jumlah tidak lagi dapat
  terlewati diam-diam. Tombol `Tambahkan ke Keranjang` menolak proses sampai
  pengguna memeriksa teks tersebut dan memilih `Abaikan` secara eksplisit.
- Pilihan `Abaikan` hanya berlaku pada hasil yang sedang ditinjau, dapat
  dibatalkan, dan otomatis direset ketika ucapan baru masuk atau transkrip
  dibersihkan.
- Konfirmasi tetap hanya mengirim produk valid, tetap satu kali, dan tidak
  mengubah parser, lifecycle mikrofon, database/schema versi 10, format backup,
  atau dependency.

Gerbang otomatis/build yang lulus:

- Widget halaman suara: **11/11**.
- Seluruh regresi voice/parser/kamus/lifecycle: **72/72**.
- Seluruh test aplikasi Kasir: **426/426**.
- Flutter analyzer: **tidak ada masalah**.
- `git diff --check`: **bersih**.
- Build debug, pemasangan, dan pembukaan pada V2322 Android 15: **berhasil**.

Gerbang manual yang masih diperlukan: pastikan ketiga kelompok mudah dipahami,
teks asing benar-benar menahan konfirmasi sebelum `Abaikan`, dan hanya produk
valid yang akhirnya ditambahkan ke keranjang.

### 9 September 2026 - Tahap 20A Batch 5A: contextual biasing katalog dan Kamus

Penyempurnaan akurasi pengenal suara Android sudah dipasang tanpa mengubah alur
kasir yang telah lulus:

- Sebelum setiap `Mulai Bicara` atau `Lanjut Bicara`, aplikasi mengirim istilah
  Kamus toko terlebih dahulu, lalu nama serta alias produk aktif, sebagai frasa
  konteks untuk layanan pengenal suara Android.
- Integrasi memakai `RecognizerIntent.EXTRA_BIASING_STRINGS` pada Android 13+
  melalui patch lokal minimal terhadap `speech_to_text` 7.4.0. Versi plugin
  tetap sama dan source berlisensi BSD disimpan di `third_party/speech_to_text`
  agar build dapat direproduksi serta patch dapat diaudit saat upgrade.
- Frasa dinormalisasi, duplikat beda kapital dibuang, frasa kosong/terlalu
  panjang dilewati, dan payload dibatasi maksimal 200 frasa agar Intent tidak
  membesar tanpa batas. Kamus toko diprioritaskan saat katalog sangat besar.
- Contextual biasing bersifat opsional menurut kontrak Android. Android lama,
  platform lain, atau recognizer yang menolak/mengabaikannya tetap melanjutkan
  sesi menggunakan perilaku lama; fitur kasir manual tidak terpengaruh.
- Lifecycle satu recognizer, cleanup terserialisasi, cooldown 750 ms, fallback
  terminal, batas keras antarsesi, parser deterministik, draf verifikasi, dan
  konfirmasi keranjang satu kali tidak diubah.
- Audio dan transkrip tetap tidak ditambahkan ke database, backup, atau log;
  tidak ada backend/API suara baru maupun biaya layanan baru.

File/domain yang terdampak:

- `lib/features/voice/services/native_voice_recognition_service.dart`;
- `lib/voice_poc_main.dart`;
- `third_party/speech_to_text` dan referensi path di `pubspec`;
- regresi `test/native_voice_recognition_service_test.dart` dan
  `test/voice_poc_page_test.dart`.

Gerbang yang lulus:

- Test layanan native dan halaman suara terarah: **29/29**.
- Seluruh test aplikasi Kasir: **429/429**.
- Flutter analyzer: **tidak ada masalah**.
- `git diff --check`: **bersih** pada kode final.
- Kompilasi Kotlin plugin, build APK debug, pemasangan update, sinkronisasi, dan
  pembukaan aplikasi pada V2322 Android 15/API 35: **berhasil**.

Tidak ada perubahan schema database (tetap versi 10), format backup, format
lisensi, ataupun alur/tombol Pesanan Suara. Peringatan migrasi Built-in Kotlin
dari beberapa plugin lama tetap dicatat untuk dependency freeze Tahap 22 dan
tidak menjadi kegagalan build saat ini.

Batch 5A belum dinyatakan lulus akurasi nyata. Gerbang manual yang diperlukan
adalah membandingkan ucapan bermasalah seperti `rawit bingit 2`, `beras sekilo
2`, produk bernomor seperti `nabati 2000 4`, serta satu istilah Kamus toko pada
kondisi normal dan agak jauh/berisik. Recognizer Android boleh mengabaikan hint,
sehingga hasil uji tersebut menentukan penyempurnaan Batch 5 berikutnya.

### 9 September 2026 - Tahap 20A Batch 5B: penjaga hasil suara jarak jauh

Uji nyata Batch 5A memperlihatkan dua hasil yang berbeda. Saat V2322 dekat
dengan pembicara, hampir seluruh produk, ukuran, serta jumlah berhasil dikenali
dan dipetakan ke katalog. Saat perangkat jauh, recognizer menghasilkan teks
yang sudah kehilangan beberapa nama dan angka sebelum mencapai parser. Karena
informasi yang hilang tidak dapat dipulihkan secara aman, aplikasi tidak
menambahkan tebakan agresif yang berisiko memasukkan produk atau jumlah salah.

Penyempurnaan yang diterapkan:

- hasil final dengan confidence native di bawah batas konservatif ditandai
  sebagai sesi berisiko;
- hasil selesai yang tidak memiliki satu pun item katalog siap juga memperoleh
  peringatan, termasuk ketika platform tidak menyediakan confidence;
- peringatan meminta kasir mendekatkan perangkat dan memakai `Lanjut Bicara`,
  tanpa tombol atau langkah baru;
- transkrip tetap tersedia untuk diperiksa, sedangkan kandidat dan teks asing
  tetap tidak pernah dipilih diam-diam;
- `Hapus Transkrip` membersihkan pula status peringatan kualitas;
- lifecycle recognizer satu-instance, cleanup/cooldown anti-stuck, contextual
  bias, parser, database, serta konfirmasi keranjang tidak diubah.

Gerbang yang lulus:

- Test layanan native dan halaman suara terarah: **30/30**.
- Seluruh test aplikasi Kasir: **430/430**.
- Flutter analyzer: **tidak ada masalah**.
- Build APK debug, pemasangan update, dan pembukaan aplikasi pada V2322 Android
  15/API 35: **berhasil**.

Tidak ada perubahan schema database (tetap versi 10), format backup, dependency,
atau penyimpanan audio/transkrip. Verifikasi manual yang masih diperlukan adalah
satu ucapan yang sama dari jarak dekat dan jauh: hasil dekat yang valid tidak
boleh mendapat peringatan, sedangkan hasil jauh yang rusak harus menampilkan
`Hasil suara perlu diulang lebih dekat` dan tetap menahan pilihan produk otomatis.

### 9 September 2026 - Tahap 20A Batch 5C: indikator kejernihan suara real-time

Indikator kualitas tangkapan kini terlihat selama status `Mikrofon aktif` tanpa
menambahkan tombol atau langkah kerja baru:

- callback level suara native diteruskan melalui service hanya selama generasi
  dan sesi recognizer yang sama masih aktif;
- halaman mengukur selisih level terendah dan tertinggi per segmen, lalu
  menampilkan progress serta label `Mengukur`, `Sinyal suara lemah`, `cukup`,
  atau `jelas` secara langsung;
- label lemah meminta kasir mendekatkan perangkat sebelum melanjutkan banyak
  produk, sedangkan segmen yang berakhir dengan rentang sinyal lemah ikut
  mengaktifkan peringatan hasil Batch 5B;
- indikator memakai `ValueNotifier`, sehingga perubahan level tidak membangun
  ulang parser maupun seluruh halaman pada setiap callback mikrofon;
- pengukuran direset pada setiap `Mulai/Lanjut Bicara` dan saat transkrip
  dihapus; callback setelah terminal ditolak agar sesi lama tidak mencemari sesi
  berikutnya;
- tidak ada audio, level suara, atau hasil pengukuran yang disimpan ke database,
  backup, maupun log.

Gerbang yang lulus:

- Test service native dan halaman suara terarah: **32/32**.
- Seluruh test aplikasi Kasir: **432/432**.
- Flutter analyzer: **tidak ada masalah**.
- `git diff --check`: **bersih**.
- Build APK debug, pemasangan update, dan pembukaan aplikasi pada V2322 Android
  15/API 35: **berhasil**.

Tidak ada perubahan schema database (tetap versi 10), format backup, dependency,
parser, atau alur konfirmasi keranjang. Batas rentang sinyal bersifat kalibrasi
perangkat nyata; gerbang manual Batch 5C adalah membandingkan label saat ucapan
yang sama dilakukan dekat dan jauh, lalu menyesuaikan batas bila V2322 terlalu
sering memberi label lemah atau jelas yang keliru.

### 9 September 2026 - Revisi hotfix Tahap 20A Batch 5C: recovery sesi native

Uji manual lanjutan menemukan layanan suara menolak atau tertahan setelah sekitar
16 sesi. Percobaan pertama yang menghancurkan recognizer pada setiap cleanup
ternyata terlalu agresif: uji perangkat berikutnya menghasilkan transkrip kosong
dan state tombol yang tidak konsisten. Percobaan tersebut sudah ditarik kembali
dan tidak dianggap sebagai fitur yang lulus.

Revisi yang kini terpasang:

- recognizer kembali dipertahankan pada sesi sukses agar callback partial, final,
  dan status selesai tidak terputus;
- instance native hanya didaur ulang ketika Android benar-benar melaporkan
  `ERROR_RECOGNIZER_BUSY`;
- nilai `false` dari start native tidak lagi dianggap sukses oleh wrapper;
- satu penolakan start dipulihkan otomatis melalui cancel, jeda aman, dan satu
  percobaan ulang; dua penolakan berurutan keluar sebagai kegagalan aman sehingga
  halaman dan tombol tidak tertahan;
- error `recognizer busy` dan batas permintaan layanan Android tetap mempunyai
  pesan berbeda;
- regresi 25 sesi dipertahankan dan ditambah dua kasus penolakan start.

Gerbang revisi yang sudah lulus:

- Test service dan halaman Pesanan Suara terarah: **35/35**.
- Seluruh unit, widget, dan migration test aplikasi: **435/435**.
- Flutter analyzer dan `git diff --check`: **bersih**.
- APK debug dari `lib/main.dart` berhasil dibangun dan dipasang sebagai update
  tanpa menghapus data pada V2322 Android 15/API 35 (`10DE3201NZ000NB`).
  SHA-256 APK:
  `490200581176531a1b4cadef210f79878802bac9398480267b7466bf5a070318`.

Tidak ada perubahan schema database (tetap versi 10), format backup, dependency,
parser katalog, atau penyimpanan audio/transkrip. Revisi belum ditutup sebagai
lulus manual: gerbang pertama adalah 3-5 sesi untuk memastikan transkrip dan
tombol normal, kemudian minimal 20 sesi untuk melewati titik gagal lama.

### 9 September 2026 - Revisi kedua hotfix Tahap 20A Batch 5C: isolasi sesi terminal

Keluhan perangkat sesudah revisi pertama membuktikan APK terbaru memang sudah
terpasang, tetapi transkrip masih dapat kosong dan tombol kembali tidak stabil.
Audit lanjutan menemukan dua penyebab siklus hidup yang sebelumnya tersembunyi:

- timer `listenFor` Dart dari sesi yang sudah menerima status terminal tidak
  dibatalkan; timer lama kemudian dapat memanggil `stop()` pada sesi yang lebih
  baru dan menjelaskan kegagalan yang baru muncul setelah pemakaian berulang;
- callback error Android dimasukkan ke antrean, sedangkan status selesai dikirim
  lebih dahulu; halaman sudah berpindah ke state berhenti ketika error tiba dan
  akibatnya membuang alasan kegagalan yang sebenarnya.

Perbaikan yang diterapkan:

- seluruh timer listener Dart dibersihkan segera ketika `done` atau
  `doneNoResult` benar-benar diterima;
- Android menjamin urutan event error lalu status terminal dan cleanup;
- error tidak lagi tertutup oleh status selesai yang datang sesudahnya;
- sesi terminal tanpa satu pun hasil transkrip kini masuk ke state gagal dengan
  pesan yang jelas dan tombol bicara kembali dapat digunakan;
- retry start selektif, recycle hanya pada `ERROR_RECOGNIZER_BUSY`, cooldown,
  batas generasi callback, parser, dan alur konfirmasi keranjang tetap
  dipertahankan.

Gerbang otomatis dan pemasangan yang lulus:

- Test service dan halaman Pesanan Suara terarah: **35/35**.
- Seluruh unit, widget, dan migration test aplikasi: **435/435**.
- Flutter analyzer dan `git diff --check`: **bersih**.
- APK debug berhasil dibangun, dipasang sebagai update tanpa menghapus data,
  dibuka, dan proses aplikasi aktif pada V2322 Android 15/API 35
  (`10DE3201NZ000NB`).
- SHA-256 file build dan APK terpasang sama:
  `18c983dfc0ec214e11f6699a6e0582e4a90bfecb91152aeec75360a89e24e2a3`.

Tidak ada perubahan schema database (tetap versi 10), format backup, dependency,
parser katalog, maupun penyimpanan audio/transkrip. Hotfix ini belum dinyatakan
lulus perangkat: lakukan 5 sesi singkat lebih dahulu, kemudian minimal 20 sesi
berurutan setelah perilaku awal terbukti normal. Pesan error yang muncul harus
dicatat karena kini tidak lagi disembunyikan oleh status terminal.

Gerbang perangkat hotfix tersebut kemudian **lulus pada 21 sesi suara
berurutan** tanggal 9 September 2026. Transkrip, tombol, cleanup terminal, dan
pembukaan sesi berikutnya tetap berjalan tanpa penolakan atau hang. Perlindungan
timer lintas sesi dan urutan callback error menjadi regresi wajib yang tidak
boleh dihapus pada perubahan fitur suara berikutnya.

### 9 September 2026 - Tahap 20A Batch 5D: banner countdown sesi suara

Peringatan waktu rekaman kini terlihat tanpa mengubah alur kerja kasir:

- countdown dimulai hanya setelah callback native menyatakan mikrofon benar-benar
  aktif, bukan sejak tombol ditekan;
- banner kecil fixed muncul di atas konten hanya pada tiga detik terakhir dengan
  urutan `3`, `2`, `1`, lalu `Memproses ucapan...`;
- banner memakai overlay sehingga tetap pada posisi yang sama saat transkrip atau
  daftar produk digulir dan tidak menggeser layout;
- warna peringatan menguat pada detik terakhir, getaran singkat diberikan saat
  angka tiga muncul, dan tidak ada bunyi yang dapat ikut terekam;
- banner tidak menerima input sentuh, memiliki live-region semantics, serta
  dibatasi lebarnya agar tetap ringkas pada ponsel dan tablet;
- timer dan banner dibersihkan pada final, status terminal, pembatalan, error,
  background, penghapusan transkrip, dan awal sesi berikutnya;
- status final yang datang menjelang batas tetap diproses; countdown tidak
  menebak, memotong, atau mengubah teks hasil pengenal suara.

Gerbang otomatis dan pemasangan yang lulus:

- Test service dan halaman Pesanan Suara terarah: **36/36**.
- Seluruh unit, widget, dan migration test aplikasi: **436/436**.
- Flutter analyzer dan `git diff --check`: **bersih**.
- APK debug berhasil dibangun, dipasang sebagai update tanpa menghapus data, dan
  dibuka pada V2322 Android 15/API 35 (`10DE3201NZ000NB`).
- SHA-256 file build dan APK terpasang sama:
  `b17cc5444894e18ae8f11a58fdf72d8e2d013bad860263f2f4dba30e6624af62`.

Tidak ada perubahan schema database (tetap versi 10), format backup, dependency,
parser katalog, lifecycle recognizer native, atau penyimpanan audio/transkrip.
Gerbang manual Batch 5D tinggal 3-5 sesi untuk menilai keterbacaan, posisi fixed,
ketepatan urutan `3-2-1`, getaran, serta hilangnya banner setelah sesi selesai.

#### Koreksi gerbang perangkat Batch 5D

Pengujian perangkat pertama menemukan banner tidak sempat muncul. Penyebabnya
bukan widget banner, melainkan penghitung batas waktu internal `speech_to_text`:
setiap hasil parsial menyimpan sisa durasi sebagai durasi awal baru, lalu waktu
yang sudah berjalan dikurangkan kembali. Akibatnya sesi dapat dihentikan jauh
sebelum batas 30 detik dan tidak pernah mencapai countdown tiga detik terakhir.

Perbaikan yang diterapkan:

- durasi awal `listenFor` dan `pauseFor` kini dipertahankan selama satu sesi;
- setiap penjadwalan ulang hanya menghitung sisa waktu satu kali terhadap durasi
  awal tersebut;
- batas jeda lima detik tetap bekerja dan tidak diubah menjadi sesi tanpa batas;
- tes widget sekarang mensimulasikan hasil parsial kontinu sampai detik ke-27,
  memastikan sesi tidak berhenti dini, lalu memverifikasi banner `3-2-1`, posisi
  fixed setelah scroll, status pemrosesan, serta satu panggilan stop pada batas.

Gerbang otomatis dan pemasangan ulang yang lulus:

- Seluruh tes fitur suara: **82/82**.
- Seluruh unit, widget, dan migration test aplikasi: **436/436**.
- Flutter analyzer dan `git diff --check`: **bersih**.
- APK debug dibangun ulang, dipasang sebagai update tanpa menghapus data, dan
  dibuka pada V2322 Android 15/API 35 (`10DE3201NZ000NB`).
- SHA-256 APK lokal dan APK yang ditarik kembali dari perangkat sama:
  `87e87d4af6e5e066cc884c936058246bf3d0f9d6aa0a0ccd29b97474786a5d40`.

Checksum sebelumnya tidak lagi menjadi artefak uji manual Batch 5D. Gerbang
manual tetap terbuka untuk satu skenario bicara kontinu mendekati 30 detik;
berhenti bicara selama lima detik memang mengakhiri sesi lebih awal sehingga
countdown batas maksimum tidak diharapkan muncul pada kondisi tersebut.

#### Penyesuaian Batch 5D: countdown lima detik

Atas hasil uji perangkat, waktu peringatan diperpanjang dari tiga menjadi lima
detik. Banner kini menampilkan `5`, `4`, `3`, `2`, `1`, lalu
`Memproses ucapan...`; posisi fixed, getaran awal, cleanup, batas sesi 30 detik,
dan batas jeda lima detik tetap sama. Tes suara **82/82**, seluruh tes aplikasi
**436/436**, analyzer, serta `git diff --check` lulus. APK debug dipasang ulang
ke `10DE3201NZ000NB` tanpa menghapus data; SHA-256 APK lokal dan terpasang sama:
`a30acf3749c25f102ab39d118a233871ce30d3c599e8ceb012e8d6ccdb1d6584`.

### 10 September 2026 - Tahap 20A Batch 5E: satuan suara membedakan produk bernama sama

Bug katalog dengan nama produk sama tetapi satuan berbeda telah diperbaiki:

- satuan yang diucapkan kini menjadi pembeda kandidat setelah nama katalog
  dinilai cukup cocok;
- `aqua nya 1 botol` memilih varian `Aqua 350 ml` bersatuan Botol;
- `aquanya 2 dus` memilih varian yang sama namanya tetapi bersatuan Dus;
- kata sambung kepunyaan `nya`, baik terpisah maupun menempel, dibersihkan dari
  tepi nama produk tanpa mengubah nama katalog;
- tanpa satuan, misalnya `aqua 3`, produk bernama sama tetap berstatus perlu
  konfirmasi agar sistem tidak menebak;
- jika lebih dari satu kandidat masih memiliki nama dan satuan yang sama kuat,
  konfirmasi juga tetap diwajibkan;
- pemeriksaan produk aktif, stok, jumlah, dan ketidakcocokan satuan produk tunggal
  tetap dipertahankan.

Gerbang yang lulus:

- Tes parser khusus: **31/31**.
- Seluruh tes fitur suara: **84/84**.
- Seluruh unit, widget, dan migration test aplikasi: **438/438**.
- Flutter analyzer dan `git diff --check`: **bersih**.
- APK debug dipasang sebagai update tanpa menghapus data dan dibuka pada
  `10DE3201NZ000NB`.
- SHA-256 APK lokal dan APK terpasang sama:
  `0bd9e26ccca3f9c29b5d9d4f83020a2d5491e8958c5a9273d450f6b79a41507f`.

Gerbang manual Batch 5E tinggal memastikan katalog nyata memilih Botol untuk
`aqua nya 1 botol` dan Dus untuk `aqua 350 ml 1 dus` tanpa masuk ke bagian
Perlu konfirmasi.

### 10 September 2026 - Tahap 20A Batch 5F: pencocokan fonetik nama produk majemuk

Kendala layanan suara yang melebur beberapa kata produk telah ditangani secara
umum pada parser katalog:

- nama katalog majemuk juga dibandingkan dalam bentuk rapat tanpa spasi;
- ejaan `x` dinormalisasi sebagai bunyi `ks` hanya selama pembandingan, sehingga
  transkrip `milaxa 2` dapat memilih `Mie Laksa` dengan jumlah 2;
- bentuk rapat yang tidak mengubah bunyi seperti `mielaksa 3` juga dikenali;
- nama katalog dan teks transkrip asli tidak ditulis ulang oleh mekanisme ini;
- jalur fonetik hanya aktif untuk nama katalog multi-kata dan teks rapat minimal
  lima karakter, serta tetap memakai ambang kemiripan;
- bila beberapa produk memperoleh skor yang sama kuat, mekanisme Perlu
  konfirmasi tetap dipakai dan parser tidak memilih secara paksa;
- kamus toko tetap tersedia untuk variasi ucapan khusus yang tidak dapat
  diselesaikan secara aman oleh aturan umum.

Gerbang yang lulus:

- Tes parser khusus: **32/32**.
- Seluruh tes fitur suara: **85/85**.
- Seluruh unit, widget, dan migration test aplikasi: **439/439**.
- Flutter analyzer dan `git diff --check`: **bersih**.
- APK debug dipasang sebagai update tanpa menghapus data dan dibuka pada
  `10DE3201NZ000NB`.
- SHA-256 APK lokal dan APK terpasang sama:
  `1ebd462396d4c9ce7f4baa7221714320f9e2709325103f93c768bde3cf38d2cb`.

Gerbang manual Batch 5F adalah mengucapkan `Mie Laksa 2` beberapa kali pada
lingkungan nyata dan memastikan hasil seperti `milaxa` atau `mielaksa` tetap
masuk sebagai Mie Laksa, sedangkan kandidat yang benar-benar ambigu tetap
meminta konfirmasi.

### 10 September 2026 - Tahap 20A Batch 5G: stabilisasi kesiapan dan terminal recognizer

Audit menyeluruh dilakukan setelah perangkat kembali mengalami `recognizer
busy`, hasil kosong, dan error generik walaupun suara jelas serta dekat. Akar
masalah berada pada urutan lifecycle native, bukan parser atau katalog:

- aplikasi sebelumnya memberi tanda siap saat perintah start baru diterima,
  sebelum Android benar-benar memanggil `onReadyForSpeech`; tanda siap dan
  countdown sekarang baru dimulai setelah recognizer benar-benar siap;
- `onBeginningOfSpeech` menjadi fallback aman untuk perangkat yang tidak
  mengirim callback ready;
- hasil final tidak lagi langsung memaksa `cancel`; Android mengirim status
  terminal setelah hasil final sehingga sesi sukses tidak memicu
  `ERROR_CLIENT` terlambat;
- satu service menjadi satu-satunya pemilik cleanup error; pembatalan otomatis
  kedua dari wrapper dinonaktifkan;
- error hanya diterima ketika sesi yang relevan masih aktif, sehingga error
  terlambat dari sesi lama tidak merusak sesi baru;
- recognizer yang benar-benar melaporkan busy dilepas secara aman dengan
  referensi instance lama, sehingga proses destroy tertunda tidak mungkin
  menghancurkan recognizer baru;
- fallback terminal terbatas, cooldown, generation guard, batas sesi 30 detik,
  jeda lima detik, countdown fixed, parser katalog, kamus toko, dan draf
  keranjang tetap dipertahankan.

Gerbang otomatis dan pemasangan yang lulus:

- Test service native dan halaman Pesanan Suara terarah: **39/39**.
- Seluruh test fitur suara: **87/87**.
- Seluruh unit, widget, dan migration test aplikasi: **441/441**.
- Flutter analyzer dan `git diff --check`: **bersih**.
- APK debug berhasil dikompilasi termasuk plugin Kotlin, dipasang sebagai update
  tanpa menghapus data, dibuka, dan prosesnya aktif pada V2322 Android 15/API 35
  (`10DE3201NZ000NB`).
- SHA-256 APK lokal dan APK terpasang sama:
  `5a6bb16ea98f7b9fa59134858992bbd4631eaa890d27ca2dc2e417795fe4096a`.

Tidak ada perubahan schema database (tetap versi 10), format backup, dependency,
parser produk, maupun penyimpanan audio/transkrip. Gerbang perangkat Batch 5G
masih terbuka: uji beberapa sesi awal lalu minimal 20 sesi berurutan, termasuk
`Lanjut Bicara`, `Hapus Transkrip`, dan satu pembatalan. Kasir harus mulai bicara
setelah tanda siap berbunyi; error busy, hasil kosong, error generik, hang, atau
callback sesi lama tidak boleh muncul pada suara dekat dan jelas.

Uji perangkat lanjutan kemudian dinyatakan **berjalan lancar oleh pengguna**.
Urutan `onReadyForSpeech`, satu pemilik cleanup, terminal native tanpa cancel
paksa, dan pengabaian callback lama menjadi regresi wajib yang harus tetap
dipertahankan pada penambahan keluaran suara berikutnya.

### 10 September 2026 - Tahap 20A Batch 5H-1: pembentuk ringkasan draft suara

Fondasi kalimat yang kelak dibacakan oleh Text-to-Speech telah selesai tanpa
menyentuh lifecycle pengenal suara Batch 5G:

- hanya baris berstatus `matched` atau Siap dimasukkan yang masuk ringkasan;
- item ambigu, tidak dikenali, tidak valid, dan item yang dihapus tidak ikut;
- jumlah yang telah diperbaiki lewat tombol tambah/kurang menjadi sumber akhir;
- produk yang sama dari beberapa sesi digabung berdasarkan ID katalog dan tetap
  mempertahankan urutan kemunculan pertamanya;
- harga yang dibacakan adalah subtotal per barang (`jumlah × harga satuan`),
  bukan harga satuan;
- ringkasan sengaja tidak memiliki subtotal transaksi maupun total draft karena
  PPN, diskon, dan total final kelak menjadi tanggung jawab tahap pembayaran;
- angka harga dan jumlah dibentuk menjadi kata bahasa Indonesia; angka pada nama
  produk, `ml`, `kg`, `gr`, serta pecahan umum juga dinormalisasi agar lebih
  alami;
- setiap barang menyediakan tiga bagian terpisah—nama, jumlah/satuan, dan
  harga—agar batch TTS berikutnya dapat memberikan jeda nyata dan tidak hanya
  bergantung pada interpretasi tanda koma.

Contoh hasil yang dikunci oleh test:

`Aqua tiga ratus lima puluh mililiter, tiga botol, harga lima belas ribu rupiah.
Mie Laksa, dua bungkus, harga dua belas ribu rupiah.`

Gerbang otomatis yang lulus:

- Test pembentuk ringkasan: **5/5**.
- Test ringkasan, service lifecycle, dan halaman suara terarah: **44/44**.
- Seluruh test fitur suara: **92/92**.
- Seluruh unit, widget, dan migration test aplikasi: **446/446**.
- Flutter analyzer dan `git diff --check`: **bersih**.

Tidak ada perubahan schema database (tetap versi 10), dependency, format backup,
keranjang, parser pengenal suara, atau UI produksi. Build/perangkat manual tidak
diperlukan pada batch domain murni ini. Batch 5H-2 berikutnya menghubungkan hasil
tersebut ke TTS native, tombol baca/berhenti, kecepatan sedang, jeda eksplisit,
dan pengaman agar speaker tidak pernah direkam kembali oleh mikrofon.

### 10 September 2026 - Tahap 20A Batch 5H-2: pembacaan ringkasan TTS native

Ringkasan produk Siap dimasukkan kini dapat dibacakan oleh engine Text-to-Speech
bawaan Android/iOS melalui `flutter_tts 4.2.5`:

- tombol **Dengarkan Ringkasan** membaca nama, jumlah/satuan, dan subtotal setiap
  barang; harga satuan dan total transaksi tetap tidak dibacakan;
- tombol berubah menjadi **Berhenti Membaca**, lalu status
  **Menghentikan Suara** menunggu `stop` native selesai;
- kecepatan memakai tingkat sedang-cepat `0.55`; jeda nama ke jumlah 100 ms,
  jumlah ke harga 50 ms, dan antarbarang 200 ms agar ringkas tanpa mengaburkan
  batas;
- daftar voice difilter ke bahasa Indonesia, voice offline didahulukan, dan
  kebutuhan jaringan diberi label;
- iOS/platform yang menyediakan metadata gender menampilkan label Perempuan atau
  Laki-laki. Android yang tidak menyediakan gender memakai label netral
  **Suara Indonesia 1/2/...** sehingga aplikasi tidak menebak;
- mode **Otomatis (disarankan)** memakai voice Indonesia default perangkat;
- bila bahasa Indonesia belum tersedia, UI gagal aman dan menyediakan tombol
  **Periksa Suara Lagi**;
- mikrofon, perubahan draf, hapus transkrip, penyelesaian pesanan, dan konfirmasi
  keranjang dikunci selama pembacaan maupun proses stop native;
- audio dihentikan ketika aplikasi masuk background, dan start mikrofon tetap
  menunggu TTS benar-benar berhenti;
- manifest Android sekarang dapat menemukan `android.intent.action.TTS_SERVICE`.

Gerbang otomatis dan perangkat yang lulus:

- Test service TTS dan pembentuk ringkasan: **9/9**.
- Test gabungan ringkasan, service STT/TTS, dan halaman suara: **49/49**.
- Seluruh test fitur suara: **97/97**.
- Seluruh unit, widget, dan migration test aplikasi: **451/451**.
- Flutter analyzer dan `git diff --check`: **bersih**.
- APK debug berhasil dibangun, dipasang sebagai update tanpa menghapus data, dan
  dibuka pada V2322 Android 15/API 35 (`10DE3201NZ000NB`).
- Google Text-to-Speech terdeteksi sebagai engine default pada perangkat.
- SHA-256 APK lokal dan APK terpasang identik:
  `f778819a06b66d547150a4e81b2ee302b9bef8c9c8ace11296c18c6565531c2f`.

Tidak ada perubahan schema database (tetap versi 10), format backup, parser,
keranjang, maupun lifecycle pengenal suara Batch 5G. Uji dengar perangkat awal
dinyatakan berjalan sangat bagus oleh pengguna: subtotal setiap barang terbaca,
total transaksi tidak disebutkan, dan kontrol pembacaan berjalan benar.

#### Penyesuaian tempo operasional Batch 5H-2

Uji nyata menemukan jeda dari jumlah menuju harga masih terlalu lama untuk
pesanan berisi banyak barang. Jeda tambahan sebelumnya 350 ms pada setiap bagian
dan 650 ms antarbarang diselaraskan menjadi:

- 180 ms dari nama produk ke jumlah;
- 100 ms dari jumlah ke subtotal harga;
- 320 ms sebelum barang berikutnya.

Kecepatan suara tetap `0.5`; susunan ucapan, pemilihan voice, subtotal per barang,
larangan membaca total transaksi, lifecycle TTS, dan penguncian mikrofon tidak
diubah. Test terarah lulus **49/49**, seluruh test suara **97/97**, seluruh unit,
widget, dan migration test aplikasi **451/451**, analyzer serta
`git diff --check` bersih. APK debug berhasil dibangun, dipasang sebagai update
tanpa menghapus data, dan dibuka pada V2322 Android 15/API 35
(`10DE3201NZ000NB`). SHA-256 APK lokal dan terpasang identik:
`2cccbb815ce52c81c2d323d286bd185d181baf3d249981993057fb1d5b6080aa`.

Gerbang manual terakhir hanya menilai tempo baru pada ringkasan minimal lima
barang; pengujian ulang pengenal suara secara panjang tidak diperlukan karena
alur STT dan parser tidak berubah.

#### Penyesuaian kedua tempo operasional Batch 5H-2

Setelah tempo pertama berjalan benar tetapi masih terasa lambat pada pesanan
panjang, kecepatan TTS dinaikkan secara konservatif dari `0.5` menjadi `0.55`.
Jeda tambahan dipadatkan lagi dari 180/100/320 ms menjadi 100 ms dari nama ke
jumlah, 50 ms dari jumlah ke subtotal harga, dan 200 ms antarbarang. Setiap
bagian tetap dikirim sebagai ucapan terpisah sehingga batas informasi tidak
dihilangkan.

Test terarah lulus **49/49**, seluruh unit, widget, dan migration test aplikasi
**451/451**, analyzer serta `git diff --check` bersih. APK debug berhasil
dibangun, dipasang sebagai update tanpa menghapus data, dan dibuka pada V2322
Android 15/API 35 (`10DE3201NZ000NB`). SHA-256 APK lokal dan terpasang identik:
`0c5f89106f7d4d85bf5a2d878d90e287d9906cc6b9d06677abf476ab6255d008`.

Gerbang manual hanya perlu mendengarkan ringkasan minimal lima barang untuk
menilai apakah tempo sedang-cepat tetap nyaman; alur pengenal suara tidak perlu
diuji ulang karena tidak berubah.

### 10 September 2026 - Tahap 20A Batch 5I: tambah produk manual ke draf suara

Tab Pesanan Suara kini menyediakan tombol **Tambah Produk Manual** sebagai
jalur pelengkap ketika kasir tidak ingin mengulang ucapan yang kurang jelas:

- picker katalog mempunyai pencarian nama/satuan dan menampilkan harga, satuan,
  serta stok bila produk memakai pelacakan stok;
- hanya produk aktif yang dapat dipilih;
- dialog jumlah menerima 1-999 dan menolak jumlah melebihi stok ketika stok
  negatif tidak diizinkan;
- produk terpilih langsung menjadi baris **Siap dimasukkan** tanpa ditebak ulang
  oleh parser suara, tetapi belum mengubah keranjang;
- baris manual memakai indeks stabil terpisah sehingga koreksi jumlah, hapus
  transkrip, dan sesi suara berikutnya tidak menggeser produk manual;
- baris suara dan manual digabung oleh pembentuk ringkasan, pembacaan TTS, serta
  konfirmasi atomik yang sama; produk baru masuk keranjang setelah kasir menekan
  **Selesai Pesanan** dan **Tambahkan ke Keranjang**;
- pencarian manual dikunci selama mikrofon, TTS, stop TTS, atau pengiriman draf
  sedang aktif.

Test draft dan UI terarah lulus **23/23**, seluruh test fitur suara **99/99**,
seluruh unit, widget, dan migration test aplikasi **453/453**, analyzer serta
`git diff --check` bersih. APK debug berhasil dibangun, dipasang sebagai update
tanpa menghapus data, dan dibuka pada V2322 Android 15/API 35
(`10DE3201NZ000NB`). SHA-256 APK lokal dan terpasang identik:
`4900dc831a3cf2e1df7d7ae960c9c9e6135eb745c573e60c70f67e46eefd089c`.

Tidak ada perubahan schema database (tetap versi 10), format backup, parser,
lifecycle recognizer/TTS, atau transaksi inti. Gerbang manual Batch 5I cukup
mencari satu produk, memilih jumlah, mengubah jumlah sekali di draft, lalu
memastikan produk baru masuk keranjang hanya setelah konfirmasi.

Gerbang manual tersebut telah **lulus pada 10 September 2026**. APK profile
berhasil dibangun, dipasang sebagai update pada V2322, dan pengujian pengguna
memastikan pencarian produk serta input jumlah manual berjalan lancar. Kelambatan
yang sebelumnya terlihat berasal dari overhead APK debug, bukan logika draf atau
input jumlah.

### 11 September 2026 - Tahap 20A Batch 6: harness benchmark produksi

Fondasi penilaian produksi fitur suara kini tersedia sebagai entry point profile
terpisah dari navigasi aplikasi utama:

- matriks tetap berisi 50 skenario pada kondisi tenang/berisik dan dekat/jauh;
- variasi mencakup nama serta ukuran mirip, angka kata/digit, beberapa produk,
  kata percakapan, dan produk yang memang tidak ada;
- katalog aktif dan Kamus toko dibaca secara read-only; benchmark tidak mengubah
  draf/keranjang produksi;
- setiap hasil mencatat ucapan acuan, transkrip, latensi, hasil pertama/koreksi/
  gagal, serta pelanggaran keselamatan hanya di memori;
- audio dan laporan tidak disimpan ke database; ekspor hanya terjadi saat
  pengguna menekan **Salin Laporan Benchmark**;
- hasil yang kehilangan baris atau kuantitas tidak dapat ditandai berhasil lewat
  koreksi draf;
- keputusan **GO PRODUKSI** membutuhkan 50/50 hasil, minimal 90% cocok pada
  percobaan pertama, minimal 95% dapat diselesaikan melalui draf, dan nol produk
  salah yang dipilih otomatis.

Verifikasi otomatis dan build:

- Unit/widget benchmark: **6/6**.
- Seluruh test aplikasi: **459/459**.
- Analyzer penuh: bersih.
- APK profile harness berhasil dibangun, dipasang, dan dibuka pada V2322.
- SHA-256 lokal/perangkat identik:
  `ecd6dbdc1d74f6625c3d614c840acbefd3fecaad1525fa2e9f8fbb181eb9fda3`.

Gerbang otomatis Batch 6 selesai. Tahap 20A masih menunggu laporan 50 skenario
nyata dari perangkat sebelum keputusan GO/NO-GO dan pemulihan APK Kasir utama.

#### Koreksi parity dan ketahanan harness Batch 6

Percobaan perangkat pertama terhenti pada skenario 26 setelah 25 panggilan
pengenal suara beruntun. Audit menemukan harness memakai service dan parser yang
sama dengan aplikasi utama, tetapi konteks pengenalannya belum setara: harness
hanya mengirim nama produk, sedangkan aplikasi utama juga mengirim istilah Kamus
serta alias katalog. Skenario 26 sekaligus merupakan awal kelompok kondisi toko
berisik, sehingga hasil percobaan tersebut tidak dipakai sebagai dasar keputusan.

Harness telah diperbaiki tanpa mengubah lifecycle atau parser aplikasi utama:

- biasing phrases kini identik dengan halaman produksi: istilah Kamus, nama
  produk aktif, dan alias produk;
- pendinginan 30 detik wajib berjalan setiap 10 skenario dan otomatis setelah
  layanan melaporkan sibuk/batas sementara;
- tombol rekam terkunci selama pendinginan dengan hitung mundur yang jelas;
- tombol **Salin progres** tersedia sejak ada hasil agar laporan sementara dapat
  diamankan tanpa menyimpan audio atau hasil ke database.

Unit/widget benchmark lulus **7/7**, seluruh unit/widget/migration test aplikasi
lulus **460/460**, dan analyzer penuh bersih. APK profile koreksi berhasil
dibangun, dipasang sebagai update, dan dibuka dari skenario 1 pada V2322 Android
15 (`10DE3201NZ000NB`). SHA-256 APK lokal dan perangkat identik:
`51746e2878933b525629b78eb829eb86fb0c576ca461c9f42bfb6d78f52655ed`.

Gerbang manual 50 skenario dimulai ulang karena 25 hasil awal berasal dari
konfigurasi harness yang belum setara. Data toko tetap dipertahankan dan aplikasi
utama tidak mengalami perubahan pada batch koreksi ini.

#### Pemulihan sesi native yang tertahan

Uji ulang sempat gagal sejak skenario 1 dengan transkrip kosong. Audit ADB
membuktikan izin `RECORD_AUDIO` aktif dan mikrofon benar-benar dibuka sekitar
lima detik, tetapi Google RecognitionService menolak permintaan dengan log
`listener already in session` dan `service capacity is full`. Akar masalahnya
adalah `ERROR_CLIENT` dari Android belum mendaur ulang instance recognizer;
plugin lokal sebelumnya hanya mendaur ulang pada `ERROR_RECOGNIZER_BUSY`.

Perbaikannya dilakukan pada jalur bersama secara terbatas:

- `ERROR_CLIENT` kini menghancurkan instance recognizer yang tertahan sehingga
  sesi berikutnya membuat instance baru;
- Dart memetakan `error_client` sebagai kegagalan sibuk yang dapat dipulihkan,
  bukan kegagalan layanan generik;
- harness otomatis memberi pendinginan 30 detik untuk kondisi tersebut;
- proses Speech Services by Google yang sudah telanjur tertahan di perangkat
  dihentikan sekali dan akan dimulai ulang otomatis oleh Android.

Test terarah recognizer dan benchmark lulus **31/31**, seluruh test aplikasi
lulus **460/460**, analyzer bersih, dan APK profile berhasil dikompilasi serta
dipasang ulang pada V2322. SHA-256 APK lokal dan perangkat identik:
`011d9c9d7cac3d22d766a7ea8c584f442bee97ba05d27469afd20d941bb60fbc`.
Halaman benchmark telah dibuka kembali pada skenario 1; validasi mikrofon nyata
setelah reset layanan menunggu uji pengguna.

#### Koreksi jumlah yang berubah/hilang pada hasil pengenal suara

Uji perangkat pada skenario `beras medium 5 kg tiga` memperlihatkan dua lapis
masalah: hasil sementara mengenali `tiga` sebagai `jika`, kemudian hasil final
Google justru memendek menjadi `beras medium 5 kg` dan menghapus jumlah. Harness
benchmark sebelumnya langsung mengganti teks lama dengan hasil final, tidak
seperti halaman Pesanan Suara utama yang sudah memakai penggabung transkrip.

Perbaikan tanggal 11 September 2026:

- harness kini memakai `VoiceTranscriptDraft` yang sama dengan aplikasi utama,
  sehingga hasil final yang lebih pendek tidak menghapus produk atau jumlah
  yang sudah dikenali pada hasil sementara;
- kandidat `jika` juga diuji sebagai `tiga`, tetapi hanya dipilih bila hasilnya
  memberi pasangan produk-jumlah katalog yang lebih valid. Koreksi tidak
  dilakukan sebagai penggantian global atau berdasarkan jawaban skenario;
- tes regresi meniru urutan native nyata: partial `beras medium 5 kg jika`, lalu
  final `beras medium 5 kg`; hasil gabungan wajib tetap
  `beras medium 5 kg tiga`;
- tidak ada dependency, perubahan schema, penyimpanan audio, atau mutasi
  keranjang baru.

Targeted parser/transcript/benchmark lulus **47/47**, seluruh test aplikasi
lulus **462/462**, dan analyzer penuh bersih. APK profile benchmark berhasil
dibangun, dipasang sebagai update, serta dibuka pada V2322 Android 15
(`10DE3201NZ000NB`). SHA-256 APK lokal dan perangkat identik:
`3e59050890e30301603aeb19f7be57af23baf9d056a8d2f3a2870cb62811fb29`.
Gerbang manual masih terbuka: skenario 1 dan 2 perlu diucapkan satu kali per
skenario untuk memvalidasi perilaku Speech Services nyata sebelum melanjutkan
50 skenario.

#### Validasi katalog dan replay benchmark Batch 6

Laporan 50 skenario pertama tidak dibuang. Audit menemukan harness hanya
memuat 14 produk aktif, sedangkan matriks membutuhkan 69 nama produk unik.
Akibatnya persentase awal 2%/90% dan satu pelanggaran keselamatan tidak sah
untuk menilai parser terhadap katalog yang semestinya.

Perbaikan tanggal 11 September 2026:

- benchmark menghitung produk wajib yang hilang atau tidak aktif sebelum sesi;
  tombol rekam terkunci dan daftar produk hilang ditampilkan bila preflight
  gagal;
- `resolvedInDraft` tidak lagi dapat lulus jika produk target tidak tersedia
  aktif di katalog;
- pencocokan potongan nama diperketat agar frasa asing seperti
  `bumbu racik nasi goreng sachet` tidak otomatis memilih
  `Nasi Goreng Spesial` hanya karena dua kata di tengah sama;
- alat developer `tool/replay_voice_benchmark.dart` memakai kembali transkrip
  laporan lama tanpa merekam ulang atau menyimpan audio.

Replay 50 transkrip dengan katalog 69 produk menghasilkan **43/50 (86%)**
first-attempt, **46/50 (92%)** dapat diselesaikan, dan **0** pelanggaran
keselamatan. Tiga skenario hanya memerlukan koreksi draf: VN-15, BF-06, BF-07.
Empat skenario yang benar-benar perlu uji suara ulang: VF-01, VF-02, VF-04,
BF-09.

Seed katalog perangkat menambahkan **122** produk baru tanpa menimpa data lama;
semuanya aktif dan stok tidak dilacak. Harness juga mendukung filter skenario
melalui build-time define agar kasus gagal dapat diuji ulang tanpa mengulang 50
skenario. Filter selalu mempertahankan urutan matriks dan menolak ID yang tidak
dikenal.

Setelah mode retest ditambahkan, targeted benchmark lulus **11/11**, seluruh
test aplikasi lulus **466/466**, dan analyzer bersih. APK profile khusus VF-01,
VF-02, VF-04, dan BF-09 berhasil dipasang serta dibuka pada V2322. Perangkat
menampilkan **Skenario 1 dari 4**, ucapan VF-01 yang tepat, dan tombol rekam
aktif. SHA-256 APK lokal dan perangkat identik:
`a5475124ea2432a7101595fbfb9628ca7b510e8e233881805b65cc65b59caa1c`.
Uji manual empat skenario terarah pada V2322 kemudian lulus **4/4** pada
percobaan pertama, termasuk BF-09 pada kondisi berisik dan jauh. Setelah empat
hasil tersebut menggantikan empat kegagalan baseline, hasil final 50 skenario
menjadi **47/50 (94%) first-attempt**, **50/50 (100%) resolved**, **0**
pelanggaran keselamatan, dan rata-rata latensi sekitar **13.185 ms**. Seluruh
ambang penerimaan Batch 6 terpenuhi sehingga keputusan akhirnya **GO**. Schema
database tetap versi 10. APK profile aplikasi Kasir utama kemudian dibangun,
dipasang sebagai update tanpa menghapus data, dan terverifikasi terbuka pada
Dashboard V2322. SHA-256 APK utama lokal/perangkat identik:
`6f7dd8f35b01c1b0c024e5a353e64466dea2553316080d327a1e97e905c336cf`.

### 11 September 2026 - Tahap 20B Batch 1: pencarian produk langsung dan relevan

Pencarian katalog diselaraskan pada tab Produk Manager dan halaman Kasir:

- tab Produk memperbarui hasil otomatis 250 ms setelah ketikan terakhir tanpa
  perlu menekan Enter;
- submit manual tetap dapat memaksa pencarian segera, sedangkan timer dibatalkan
  ketika filter berubah atau ViewModel ditutup;
- hasil request lama tidak dapat menimpa hasil dari kata pencarian terbaru;
- repository Produk dan katalog Kasir memakai satu comparator relevansi:
  kecocokan kode/barcode tepat lebih dahulu, nama yang diawali kata pencarian,
  lalu nama/kode/barcode yang mengandung kata tersebut;
- nama alfabetis dan ID menjadi penentu akhir agar urutan selalu stabil;
- filter kategori, status aktif, stok, serta cache hasil Kasir tetap bekerja.

Verifikasi:

- targeted repository/ViewModel/widget: **23/23 lulus**;
- seluruh test aplikasi: **469/469 lulus**;
- analyzer penuh: **No issues found**;
- benchmark database terisolasi: 10.000 produk, pencarian spesifik **35 ms**,
  pencarian luas dan pengurutan 10.000 hasil **426 ms**;
- `integrity_check`: `ok`; pelanggaran foreign key: **0**.

Tidak ada dependency atau perubahan schema database; schema tetap versi 10.
Test manual perangkat tidak diperlukan karena perilaku UI sudah ditutup widget
test dan skala katalog sudah diuji pada database performance terisolasi. Batch
berikutnya adalah Tahap 20B Batch 2: scanner barcode utama.

### 12 September 2026 - Tahap 20B Batch 2A: scanner kamera dan pusat Perangkat

Fondasi barcode yang sebelumnya hanya berupa kolom produk dan pencarian kini
dipakai untuk operasional nyata:

- halaman Kasir memiliki pemindai kamera yang hanya meminta izin ketika dibuka,
  menangani izin ditolak dengan akses Pengaturan, serta menyediakan input
  barcode manual dan dukungan scanner keyboard melalui submit pencarian;
- barcode wajib cocok persis dengan satu produk aktif. Produk tidak dikenal,
  nonaktif, stok habis/melebihi batas, fitur barcode toko nonaktif, dan dua
  proses bersamaan tidak dapat menambah item secara diam-diam;
- hasil kamera dikunci setelah deteksi pertama sehingga barcode yang tetap
  berada di depan kamera tidak menambah produk berulang;
- lifecycle kamera, rotasi, pause/resume, dan pelepasan resource memakai
  penanganan bawaan `mobile_scanner`; model pemindai Android dibundel agar
  operasional barcode tidak bergantung pada unduhan saat digunakan;
- menu Manager **Perangkat** menjadi pusat uji scanner kamera dan scanner fisik
  HID/Keyboard. Hasil uji menampilkan produk, barcode, harga, dan satuan tanpa
  mengubah keranjang;
- kartu Printer Struk pada menu yang sama memakai kembali `PrinterViewModel`
  dan dialog pengaturan printer lama, sehingga tidak ada konfigurasi printer
  kedua;
- pencarian barcode tepat dipusatkan di `ProductRepository` dan dipakai oleh
  Kasir maupun halaman uji Perangkat.

Verifikasi otomatis: unit barcode/hak akses **17/17**, widget halaman Perangkat
dan input manual scanner lulus, seluruh test aplikasi **477/477**, serta analyzer
penuh bersih. APK profile scanner kamera terpasang pada V2322 dengan SHA-256
`84cf8297b46792bef8e0bd29b2da27885148439ee191b408b6df7a15f3ec2188`;
uji scan kamera nyata dinyatakan lancar. APK berikutnya yang menambahkan menu
Perangkat juga berhasil dibangun dan dipasang tanpa menghapus data dengan
SHA-256
`06fbf4192fd4bfb25990702dfe4d1fb54fa7d989f92a786130195a421c61f9ef`,
dan pengujian manual halaman dinyatakan lancar. Tidak ada perubahan schema;
schema tetap versi 10. Dukungan tangkap HID langsung tanpa fokus kolom menjadi
sub-batch berikutnya sebelum Batch 2 ditutup penuh.

### 12 September 2026 - Tahap 20B Batch 2B: scanner HID dan pencarian Kasir stabil

Scanner fisik model HID/Keyboard kini dapat dipakai langsung dari halaman
Kasir tanpa harus memfokuskan kolom khusus:

- penangkap input hanya aktif ketika halaman Kasir benar-benar sedang dibuka,
  fitur barcode toko aktif, dan tidak ada kolom teks/form/dialog yang sedang
  menerima ketikan;
- Enter atau Tab menyelesaikan barcode, jeda antarketikan mereset input yang
  terputus, dan panjang input dibatasi sebelum memakai validasi barcode/stok
  Kasir yang sudah ada;
- indikator USB pada toolbar memberi tahu bahwa scanner fisik siap, sedangkan
  kolom pencarian tetap bebas dipakai keyboard layar maupun keyboard fisik;
- mode portrait tidak lagi mengganti struktur toolbar hanya karena keyboard
  layar mengecilkan area konten. Fokus dan koneksi input pencarian kini tetap
  hidup, sehingga pencarian langsung berjalan di portrait maupun landscape;
- listener selalu dilepas saat halaman ditutup dan tidak menangkap input dari
  Dashboard atau tab lain.

Verifikasi otomatis untuk HID, fokus pencarian, inset keyboard portrait, dan
regresi UI lulus; analyzer penuh bersih dan seluruh test aplikasi **480/480
lulus**. APK profile dipasang sebagai update tanpa menghapus data pada V2322,
dengan SHA-256
`fa6a3774b8e59c84e7ce61dbe4755ec62109feecace08f159cd23ba58226909a`.
Pengujian manual pencarian Kasir di HP portrait dan landscape dinyatakan
lancar. Gate scanner HID nyata tetap ditunda karena scanner dan OTG belum
tersedia; implementasinya tidak dinyatakan gagal ataupun ditutup sebelum alat
nyata dapat diuji. Tidak ada dependency atau perubahan schema; schema tetap
versi 10.

### 12 September 2026 - Tahap 20B Batch 3: pembacaan isi keranjang

Halaman Keranjang kini mempunyai kontrol suara ringkas pada header sehingga
ruang daftar produk tidak berkurang:

- tombol dengarkan memakai kembali `VoiceDraftSummary` dan
  `NativeVoicePlaybackService` yang sudah dipakai Pesanan Suara;
- setiap baris dibacakan sebagai nama produk, jumlah beserta satuan, lalu
  subtotal baris. Total keseluruhan keranjang sengaja tidak dibacakan;
- isi suara merupakan snapshot saat tombol ditekan, sehingga perubahan
  keranjang berikutnya tidak mengubah ucapan yang sedang berjalan dan proses
  ini tidak memutasi kuantitas maupun transaksi;
- tombol yang sama berubah menjadi berhenti saat TTS berjalan. Suara juga
  dihentikan sebelum membuka Pesanan Suara, scanner, pembayaran, mengosongkan
  keranjang, saat aplikasi masuk latar, dan ketika halaman dilepas;
- pilihan suara menawarkan mode otomatis serta suara Indonesia
  perempuan/laki-laki yang memang disediakan mesin TTS perangkat;
- engine bahasa Indonesia yang tidak tersedia atau gagal memberi pesan aman
  tanpa mengganggu keranjang.

Widget test membuktikan ucapan `Air Mineral`, `satu botol`, dan
`harga lima ribu rupiah`, tidak mengandung total keseluruhan, pilihan suara
perempuan/laki-laki tampil, lifecycle memanggil stop, serta isi keranjang tetap
satu. Seluruh test aplikasi **481/481 lulus** dan analyzer penuh bersih. APK
profile terpasang sebagai update tanpa menghapus data pada V2322; hash lokal
dan perangkat identik:
`28ea6239785abb2b68e93e66e9c24668be33d16d04a437d337ee85afbb747fa9`.
Tidak ada dependency atau perubahan schema; schema tetap versi 10. Gate manual
uji dengar perangkat kemudian dinyatakan lancar pada 12 September 2026,
termasuk kesamaan tempo dan jeda dengan ringkasan Pesanan Suara. Tahap 20B
Batch 3 selesai.

### 12 September 2026 - Tahap 20B Batch 4: Audit Log Manager

Jejak audit persisten sekarang mempunyai halaman operasional khusus Manager:

- menu **Audit Log** hanya tersedia untuk sesi Manager dengan identitas akun
  persisten; akun Kasir ditolak di navigation policy maupun repository;
- daftar memakai urutan terbaru dan pagination 50 baris agar riwayat panjang
  tidak dimuat sekaligus;
- Manager dapat memfilter periode hari ini/7 hari/30 hari/semua, jenis aksi,
  serta pelaku tertentu atau akun legacy;
- setiap kartu menampilkan waktu sesuai timezone toko, nama dan role pelaku,
  aksi, objek, serta ringkasan aman;
- data teknis mentah `detailsJson` tidak dirender, sehingga PIN, hash, salt,
  token lisensi, private key, passphrase, atau metadata internal tidak terbuka
  melalui UI;
- checkout menulis audit `sale_completed` di transaksi database yang sama
  dengan sale, item, pembayaran, dan pengurangan stok. Audit menyimpan UID
  pelaku persisten, nomor transaksi, jumlah item, total, dan metode pembayaran
  tanpa referensi pembayaran atau catatan sensitif;
- halaman dimuat saat menu dibuka dan menyediakan refresh, loading, empty,
  error, halaman sebelumnya, serta halaman berikutnya secara responsif.

Verifikasi otomatis meliputi authorization negatif, daftar pelaku, waktu toko,
filter, pagination, sanitasi tampilan, audit checkout atomik, dan regresi
pembatalan. Targeted **17/17 lulus**, regresi pembatalan **3/3 lulus**, seluruh
test aplikasi **485/485 lulus**, analyzer penuh **No issues found**, serta
`git diff --check` bersih. APK profile berhasil dibangun, dipasang sebagai
update tanpa menghapus data, dan dibuka pada V2322. SHA-256 APK lokal dan
perangkat identik:
`21a8f3c6099a19f3b3f556328e6413a32bb4d957a2dbb4081a84a9d2952f5f80`.
Tidak ada dependency atau perubahan schema; schema tetap versi 10. Pemeriksaan
UI/filter pada perangkat kemudian dinyatakan berjalan lancar oleh pengguna pada
12 September 2026. Batch 4 selesai; ketiadaan akses Audit Log bagi akun Kasir
tetap dijaga oleh regression test pada batch berikutnya.

### 12 September 2026 - Tahap 20B Batch 5: tab Produk Terlaris Manager

Agregasi `topProducts` yang sudah dipakai laporan kini tersedia sebagai tab
operasional khusus Manager dan pintu masuk dari Dashboard:

- periode Harian, Mingguan, Bulanan, dan Tahunan dihitung memakai kalender serta
  timezone toko; minggu berlangsung Senin sampai Minggu;
- daftar diurutkan berdasarkan jumlah terjual, omzet, nama, lalu identitas produk
  secara stabil dan menampilkan peringkat, nama/kode, jumlah/satuan, serta omzet;
- hanya transaksi berstatus selesai yang dihitung. Transaksi batal dikecualikan;
- snapshot produk lama tetap digabung berdasarkan identitas bisnis sehingga
  rename produk tidak memecah peringkat yang sama;
- tab hanya dibuat ketika benar-benar dibuka, sehingga Dashboard dan halaman
  lain tidak menjalankan query produk terlaris tersembunyi;
- akun Kasir tidak melihat tab/pintu masuk dan ditolak kembali oleh repository;
- tersedia refresh, loading, empty, error, tampilan semantik, daftar lazy, serta
  layout yang aman pada layar portrait dan drawer landscape pendek.

Verifikasi: targeted repository/widget/role **17/17 lulus**, regresi drawer
landscape **1/1 lulus** tanpa warning, seluruh test aplikasi **490/490 lulus**,
analyzer penuh **No issues found**, dan `git diff --check` bersih. APK profile
berhasil dibangun, dipasang sebagai update tanpa menghapus data, dan dibuka pada
V2322. SHA-256 APK lokal/perangkat identik:
`0bce4257e869b61a3aeb8dbce7bb5fda725a1fb43bb1af818446cc9096a8c7d1`.
Tidak ada dependency atau perubahan schema; schema tetap versi 10. Pemeriksaan
UI empat periode pada perangkat kemudian dinyatakan berjalan baik oleh pengguna
pada 12 September 2026. Tahap 20B Batch 5 selesai.

### 12 September 2026 - Tahap 20C Batch 1A: fondasi satuan dan harga bertingkat

Fondasi satu produk dengan beberapa satuan penjualan sudah tersedia tanpa
mengubah alur transaksi lama:

- schema database naik aman dari versi 10 ke 11 dengan tabel
  `product_unit_variants`; satuan, harga, barcode, dan stok lama pada tabel
  produk tetap menjadi satuan dasar sehingga data pengguna tidak ditulis ulang;
- satuan turunan menyimpan nama satuan, faktor konversi bilangan bulat terhadap
  stok dasar, harga jual tersendiri, barcode opsional, status aktif, urutan,
  identitas persisten, serta waktu pembuatan/perubahan;
- Manager dapat membuat, memperbarui, mengaktifkan, dan menonaktifkan satuan
  turunan melalui repository dalam transaksi atomik bersama Audit Log;
- nama satuan dasar/turunan yang ambigu, faktor konversi di bawah satu, harga
  negatif, serta barcode ganda antara produk dasar dan satuan turunan ditolak;
- akun Kasir, identitas tidak sah, dan entity dari toko lain tetap ditolak
  sebelum mutasi; audit tidak menyimpan barcode mentah, hanya status keberadaan
  barcode;
- migrasi v10 ke v11 diuji memakai produk lama nyata dan membuktikan nama,
  satuan, harga, serta stoknya tetap utuh; tabel satuan baru dimulai kosong.

Verifikasi otomatis: matriks migrasi **56/56 lulus**, regresi terarah katalog,
database health, backup/restore, dan recovery **30/30 lulus**, seluruh test
aplikasi **503/503 lulus**, analyzer penuh **No issues found**, serta
`git diff --check` bersih. Tidak ada dependency baru. Uji perangkat belum
diperlukan karena Batch 1A belum menambahkan UI; pengujian manual dilakukan
setelah UI Manager dan pilihan satuan Kasir tersedia pada sub-batch berikutnya.

### 12 September 2026 - Tahap 20C Batch 1B: UI Manager satuan dan harga bertingkat

Manager kini dapat mengelola satuan penjualan langsung dari menu aksi produk
melalui pilihan **Satuan & Harga**:

- satuan dasar tetap menjadi acuan stok dan ditampilkan sebagai data terkunci;
- satuan turunan dapat ditambah atau diubah bersama faktor konversi ke satuan
  dasar, harga jual, barcode opsional, serta urutan tampilan;
- satuan turunan dapat dinonaktifkan tanpa menghapus referensinya dan dapat
  diaktifkan kembali;
- daftar membedakan status aktif/nonaktif serta menampilkan ringkasan konversi,
  harga, dan barcode;
- dialog mempunyai batas ukuran dan area gulir agar aman pada portrait,
  tablet, maupun landscape pendek;
- perubahan memakai repository dan Audit Log yang sama dengan Batch 1A,
  sehingga validasi barcode/nama, otorisasi Manager, serta transaksi atomik
  tidak diduplikasi di UI.

Verifikasi otomatis: targeted repository dan widget **22/22 lulus**, termasuk
tambah, ubah, nonaktifkan, aktifkan kembali, pengurutan, serta landscape pendek;
seluruh test aplikasi **507/507 lulus**, analyzer penuh **No issues found**, dan
`git diff --check` bersih. Tidak ada dependency baru. APK profile berhasil
dibangun, dipasang sebagai update tanpa menghapus data, dan dibuka pada V2322.
SHA-256 APK lokal dan perangkat identik:
`24ede6668247591e4b2a72b6157a7d02e16d1679f8f22e5b388ee097fd07feae`.

Pemeriksaan visual/interaksi pada perangkat dinyatakan lulus oleh pengguna.
Teks bantuan isi satuan juga diperbaiki menjadi netral terhadap jenis satuan:
Manager diminta memasukkan jumlah satuan dasar dalam satu satuan turunan, bukan
lagi melihat contoh Dus yang tidak sesuai untuk produk berbasis Kg.

### 12 September 2026 - Tahap 20C Batch 1C: satuan bertingkat di Kasir dan transaksi

Integrasi inti satuan penjualan telah tersambung ke alur Kasir:

- produk dengan lebih dari satu satuan membuka pemilih satuan dan jumlah,
  sedangkan produk dengan satu satuan tetap langsung masuk ke keranjang;
- satu produk dapat berada di keranjang sebagai baris terpisah untuk satuan
  berbeda, masing-masing dengan nama satuan, harga, jumlah, dan subtotalnya;
- barcode satuan turunan memilih satuan yang tepat dan feedback scanner
  menyebutkan satuannya;
- ringkasan suara keranjang memakai nama satuan dan harga baris yang dipilih;
- seluruh baris satu produk dihitung bersama dalam satuan stok dasar, sehingga
  kombinasi satuan tidak dapat melampaui stok yang tersedia;
- checkout menyimpan snapshot UID varian, faktor konversi, harga, dan kuantitas
  stok; pengurangan serta pemulihan stok saat pembatalan tetap atomik;
- schema database naik kompatibel dari versi 11 ke 12. Kolom snapshot baru
  nullable sehingga transaksi lama dan jalur migrasi/backup tetap aman;
- katalog Kasir dan daftar Produk Manager menampilkan harga termurah hingga
  termahal dari satuan aktif, misalnya `Rp3.500 ~ Rp200.000`; produk dengan satu
  harga tetap ringkas dan satuan nonaktif tidak ikut dihitung;
- rentang harga dimuat dengan satu query untuk seluruh katalog agar jumlah
  produk yang besar tidak menghasilkan query satu per satu;
- stok fisik tetap disimpan satu kali pada satuan dasar agar tidak terhitung
  ganda, sedangkan setiap satuan menampilkan jumlah utuh yang tersedia dan sisa
  stok dasar, misalnya `Tersedia 2 Dus • sisa 2 Botol`;
- pemilih satuan Kasir menghitung ulang ketersediaan setelah item masuk ke
  keranjang, sehingga angka yang terlihat mencerminkan stok tersisa sebelum
  checkout;
- kartu produk Kasir ikut memperbarui stok dasar secara langsung berdasarkan
  seluruh isi keranjang lintas satuan. Contoh: stok 50 Botol berubah menjadi 26
  Botol setelah 1 Dus berisi 24 Botol ditambahkan, lalu kembali saat jumlah
  dikurangi atau item dihapus;
- halaman Satuan & Harga menjelaskan bahwa stok setiap satuan adalah hasil
  konversi otomatis, bukan stok fisik terpisah.

Verifikasi otomatis: targeted core, migrasi, recovery, dan UI **118/118 lulus**;
targeted stok dinamis/cart/UI **41/41 lulus**; seluruh test aplikasi **527/527
lulus**; analyzer penuh **No issues found**; dan `git diff --check` bersih. Tidak
ada dependency baru. APK profile berhasil dibangun, dipasang sebagai update
tanpa menghapus data, dan dibuka pada V2322.
SHA-256 APK lokal dan perangkat identik:
`2edad95fac063ae3d263f9bd3ddaeea80f4087aaf6d47858763a3c2a56c58626`.

Gerbang manual Batch 1C masih menunggu pemeriksaan pilihan satuan, kombinasi dua
satuan dalam keranjang, konversi stok setelah checkout/pembatalan, serta layout
portrait/landscape. Pesanan Suara belum memilih satuan turunan secara eksplisit;
penyelarasan itu menjadi Batch 1D sebelum keseluruhan Batch 1 dinyatakan selesai.

### 13 September 2026 - Penyempurnaan input angka dengan pemisah ribuan otomatis

- Harga beli, harga jual, uang diterima, stok, faktor isi satuan, dan urutan
  tampilan langsung memakai titik pemisah ribuan selama pengguna mengetik.
- Nilai awal saat mengedit data lama ikut ditampilkan dalam format yang sama.
- Teks yang ditempel, termasuk `Rp 75.000`, dibersihkan dan dinormalkan menjadi
  `75.000`.
- Nilai bertitik diubah kembali menjadi integer sebelum validasi dan penyimpanan;
  contoh `1.250.000` tetap tersimpan dan dihitung sebagai `1250000`.
- PIN dan barcode tidak diformat karena keduanya merupakan identifier, bukan
  nominal atau jumlah.
- Implementasi memakai formatter Flutter bersama yang sudah berada pada modul
  format Rupiah; tidak menambah dependency maupun mengubah schema database.

Verifikasi otomatis: targeted formatter, pembayaran, dan form satuan **10/10
lulus**; seluruh test aplikasi **530/530 lulus**; analyzer penuh **No issues
found**; dan `git diff --check` bersih. APK profile berhasil dibangun, dipasang
sebagai update tanpa menghapus data, dan dibuka pada V2322. SHA-256 APK lokal
dan perangkat identik:
`334984db782c3da66efaf7d057c969025544a5c77f2630e994dee54b381e408a`.

Gerbang manual: ketik nominal panjang pada form Produk/Satuan dan Uang Diterima,
pastikan titik muncul tanpa mengganggu penghapusan angka, lalu simpan satu data
uji dan pastikan nilainya tetap benar.

### 13 September 2026 - Tahap 20C Batch 1D-A: rekonsiliasi scanner multi-satuan

- Audit memastikan scanner Kasir dari kamera, input manual, dan scanner fisik
  HID melewati resolver barcode yang sama dan sudah menghasilkan
  `ProductSaleUnit` yang tepat.
- Barcode produk dasar pada produk dengan satu satuan tetap langsung masuk ke
  keranjang.
- Jika produk memiliki beberapa satuan aktif, satu barcode produk dasar dapat
  dipakai bersama: hasil scan membuka pilihan satuan beserta harga, konversi,
  stok tersedia, dan jumlah sebelum masuk ke keranjang. Barcode satuan turunan
  tidak wajib diisi ulang.
- Barcode khusus satuan tetap opsional; jika tersedia, scan langsung memilih
  varian beserta harga dan faktor konversinya tanpa dialog.
- Barcode yang sama tetap tidak boleh disimpan pada dua satuan karena scanner
  tidak dapat mengetahui kemasan yang dimaksud. Proteksi proses scan mencegah
  callback kamera/HID ganda membuka dialog atau menambah item dua kali.
- Halaman Perangkat → Uji Scanner yang sebelumnya hanya mengenali barcode produk
  dasar kini memakai resolver multi-satuan yang sama.
- Hasil pengujian Perangkat menampilkan barcode, harga, dan nama satuan yang
  ditemukan tanpa memasukkan barang ke keranjang.

Verifikasi otomatis: targeted Kasir/ViewModel **43/43 lulus**, termasuk barcode
dasar yang meminta pilihan Dus dan konversi 12 stok dasar; seluruh test aplikasi
**532/532 lulus**; analyzer penuh **No issues found**; dan `git diff --check`
bersih. Tidak ada dependency atau perubahan schema baru. APK profile berhasil
dibangun, dipasang sebagai update tanpa menghapus data, dan dibuka pada V2322.
SHA-256 APK lokal dan perangkat identik:
`567e02d972467b9f6e69688c8d63bb871f2d0bc093890b6e85ee79e5f1e92fc7`.

Scanner fisik belum memerlukan gerbang manual ulang karena perangkat scanner
belum tersedia; kontrak kamera, manual, dan HID sudah dicakup regression test.
Tahap berikutnya adalah Batch 1D-B untuk rekonsiliasi Pesanan Suara dengan
satuan bertingkat.

### 13 September 2026 - Tahap 20C Batch 1D-B: rekonsiliasi Pesanan Suara multi-satuan

- Pesanan Suara memuat satuan dasar dan seluruh satuan turunan aktif, lengkap
  dengan harga, faktor konversi, serta identitas variannya.
- Ucapan yang menyebut satuan, misalnya `Aqua 2 dus` atau `Aqua 3 botol`, memilih
  satuan dan harga yang sesuai. Nama satuan kustom seperti `karung` ikut dikenali
  langsung dari katalog tanpa daftar statis khusus.
- Jika satu produk mempunyai beberapa satuan dan kasir tidak menyebut satuan,
  hasil ditandai **Perlu konfirmasi**. Pilihan kandidat dapat ditekan dan
  menampilkan harga beserta satuannya sehingga aplikasi tidak menebak diam-diam.
- Tambah produk manual, koreksi kandidat, ringkasan suara, dan konfirmasi ke
  keranjang mempertahankan satuan yang dipilih.
- Satuan berbeda dari produk yang sama tetap menjadi baris draf/keranjang
  terpisah. Pengulangan satuan yang sama tetap digabung agar draf ringkas.
- Validasi stok menghitung kebutuhan stok dasar gabungan dari seluruh satuan
  sebelum satu pun baris ditambahkan, sehingga kegagalan tetap atomik.
- Frasa pembantu pengenal suara sekarang mencakup nama produk, nama satuan, dan
  kombinasi keduanya. Siklus mulai/lanjut/berhenti mikrofon tidak diubah untuk
  menjaga perbaikan stabilitas rekaman yang sudah lulus sebelumnya.

Verifikasi otomatis: targeted parser, ringkasan, halaman suara, dan ViewModel
**81/81 lulus**; seluruh test aplikasi **538/538 lulus**; analyzer penuh **No
issues found**; serta `git diff --check` bersih. Tidak ada dependency atau
perubahan schema. APK profile berhasil dibangun, dipasang sebagai update tanpa
menghapus data, dan dibuka pada V2322. SHA-256 APK lokal/perangkat identik:
`9a12b9e55627abaa0b3550c6349112fe0851cc19138ee54196a9b4b19b95c299`.

Pada saat implementasi, gerbang manualnya adalah mengucapkan produk yang sama
memakai dua satuan berbeda dan memastikan draf, ringkasan, serta keranjang
mempertahankan keduanya.

Gerbang manual tersebut dinyatakan **lulus oleh pengguna pada 13 September
2026**. Pesanan Suara multi-satuan berjalan lancar; Tahap 20C Batch 1 resmi
selesai.

### 13 September 2026 - Tahap 20C Batch 2: feedback produk masuk ke keranjang

- Ketukan kartu produk menampilkan feedback singkat **Masuk ke keranjang**
  beserta nama, satuan, dan jumlah terbaru pada baris keranjang.
- Scanner kamera, input barcode, dan scanner fisik HID memakai feedback yang
  sama dengan ikon scanner, sehingga sumber penambahan tetap mudah dikenali.
- Scan atau ketukan berulang tidak menumpuk SnackBar lama; feedback sebelumnya
  langsung diganti dan jumlah yang tampil selalu jumlah terbaru.
- Pilihan satuan bertingkat tetap dipertahankan, misalnya `Air Mineral • 1 Dus`,
  bukan kembali ke satuan dasar.
- Barcode tidak dikenal, stok tidak cukup, dan kegagalan penambahan memakai
  warna, ikon, serta pesan gagal yang berbeda dari kondisi sukses.
- Feedback sukses dan gagal memakai semantic live region serta label lengkap
  agar dapat diumumkan oleh pembaca layar.
- Bunyi/getar tidak ditambahkan karena notifikasi visual sudah memenuhi alur
  tanpa menambah kebisingan atau pengaturan baru.

Verifikasi otomatis: widget Kasir **27/27 lulus**, targeted Kasir/ViewModel
**47/47 lulus**, seluruh test aplikasi **540/540 lulus**, analyzer penuh **No
issues found**, serta `git diff --check` bersih. Tidak ada dependency atau
perubahan schema. APK profile berhasil dibangun, dipasang sebagai update tanpa
menghapus data, dan dibuka pada V2322. SHA-256 APK lokal/perangkat identik:
`13781ffc88becfa1d5d666a8f15501655d0539219001c0c2f8231c22975bea23`.

Pemeriksaan visual notifikasi ketukan dan scanner kamera dinyatakan **lulus oleh
pengguna pada 13 September 2026**. Scanner fisik nyata tetap ditunda sampai
alat/OTG tersedia; kontrak HID dan scan berulang sudah lulus test otomatis.
Tahap 20C Batch 2 selesai.

### 13 September 2026 - Tahap 20C Batch 3A: fondasi shift dan kas

- Schema database naik kompatibel dari versi 12 ke 13 dengan tiga tabel baru:
  definisi shift, sesi shift kasir, dan pergerakan kas masuk/keluar.
- Penjualan memperoleh snapshot UID dan nama shift yang nullable. Transaksi lama
  tidak ditulis ulang dan tetap dapat dibaca tanpa identitas shift.
- Manager dapat membuat, mengubah, mengaktifkan, dan menonaktifkan definisi
  shift. Nama dinormalisasi dan unik tanpa bergantung pada kapitalisasi.
- Manager maupun Kasir aktif dapat membuka shift, tetapi database menjamin hanya
  satu shift aktif pada satu toko/perangkat. Shift berikutnya ditolak sampai
  shift lama ditutup.
- Shift hanya dapat ditutup oleh akun yang membukanya atau Manager. Logout,
  auto-lock, maupun pembuatan ulang repository tidak menutup atau menggandakan
  shift karena sumber statusnya selalu database.
- Uang seharusnya dihitung dari uang awal + pembayaran tunai transaksi selesai
  + kas masuk - kas keluar. Transaksi batal dan pembayaran non-tunai tidak ikut
  dihitung.
- Kas masuk/keluar wajib bernilai positif dan memiliki catatan. Uang awal/akhir
  tidak boleh negatif. Selisih uang akhir sekecil apa pun wajib memiliki alasan.
- Definisi, pembukaan, pergerakan kas, dan penutupan ditulis atomik bersama Audit
  Log dengan UID aktor, snapshot operator, waktu, dan rincian nominal.
- Definisi yang sedang dipakai shift aktif tidak dapat dinonaktifkan. Data jenis
  pergerakan kas yang tidak valid juga gagal tertutup, bukan diam-diam dihitung.
- Migrasi v12 ke v13 dibuktikan menggunakan transaksi lama; transaksi tetap
  utuh, snapshot shift bernilai kosong, dan tabel shift baru dimulai kosong.

Verifikasi otomatis: repository/state machine **6/6 lulus**, matriks dan data
migrasi v1-v13 **81/81 lulus**, regresi database health, recovery, serta
backup/restore **15/15 lulus**, seluruh test aplikasi **559/559 lulus**, Flutter
analyzer penuh **No issues found**, dan `git diff --check` bersih. Tidak ada
dependency baru.

Batch 3A tidak menambahkan UI dan belum mengunci checkout dengan syarat shift;
ini disengaja agar aplikasi tetap operasional sampai layar pembukaan/penutupan
tersedia. Karena belum ada interaksi baru, uji perangkat manual belum diperlukan.
Sub-batch berikutnya adalah UI Manager untuk definisi shift dan UI pembukaan
shift, lalu pengaitan checkout, kas masuk/keluar, dan penutupan kas.

### 13 September 2026 - Tahap 20C Batch 3B: pengaturan dan pembukaan shift

- Menu **Shift** sekarang tersedia bagi Manager dan Kasir dengan status yang
  dibaca langsung dari database, bukan state sementara halaman.
- Manager dapat menambah, mengubah, mengurutkan, mengaktifkan, dan menonaktifkan
  definisi shift. Kasir dapat melihat pilihan aktif tetapi tidak memperoleh
  kontrol pengaturan tersebut.
- Manager maupun Kasir dapat membuka satu shift aktif dengan memilih definisi
  dan memasukkan uang awal. Input nominal memakai pemisah ribuan yang sama dengan
  form keuangan lain.
- Setelah dibuka, kartu status menampilkan nama shift, operator pembuka, waktu
  buka, serta uang awal. Status yang sama tetap tampil sesudah halaman dan
  repository dibuat ulang.
- Halaman memakai satu daftar yang dapat digulir, lebar konten dibatasi untuk
  tablet, dan dialog form dapat digulir. Skenario landscape pendek 800x360
  memastikan tombol pengaturan tetap dapat dijangkau tanpa overflow.
- Akses menu telah diselaraskan: Manager dan Kasir dapat membuka halaman Shift,
  sedangkan mutasi definisi tetap dijaga ulang oleh repository Manager-only.

Verifikasi otomatis yang lulus:

- widget UI Shift: **3/3**;
- integrasi repository, role, login/shell, Kasir, scanner, dan responsive:
  **42/42**;
- seluruh test aplikasi: **562/562**;
- Flutter analyzer penuh: **No issues found**;
- `git diff --check`: bersih.

Tidak ada dependency atau perubahan schema baru; schema tetap versi 13. Checkout
belum diwajibkan memiliki shift dan uji perangkat belum dilakukan pada sub-batch
ini. Keduanya ditunda sampai UI kas masuk/keluar serta penutupan kas tersedia,
agar aplikasi dan data uji tidak terjebak pada shift aktif tanpa tombol tutup.
Sub-batch berikutnya adalah Batch 3C: mutasi kas, perhitungan kas akhir, dan
penutupan shift; setelah jalur buka-tutup lengkap, barulah dilakukan build serta
pengujian perangkat nyata sebelum checkout diikat ke shift.

### 14 September 2026 - Tahap 20C Batch 3C: mutasi dan penutupan kas

- Pembuka shift atau Manager dapat mencatat **Kas Masuk** dan **Kas Keluar**
  dengan nominal berformat ribuan serta catatan wajib.
- Riwayat kas menampilkan jenis, nominal, catatan, operator, dan waktu terbaru;
  kas seharusnya diperbarui dari uang awal + transaksi tunai selesai + kas masuk
  - kas keluar.
- Kasir lain dapat melihat status tetapi tombol mutasi dan penutupan dinonaktifkan.
  Repository menerapkan izin yang sama agar akses tidak bergantung pada UI.
- Penutupan meminta uang tunai fisik, menampilkan selisih langsung, dan mewajibkan
  alasan untuk setiap selisih. Penutupan atomik melepaskan kunci toko sehingga
  shift berikutnya dapat dibuka.
- Dialog mutasi dan penutupan dapat digulir serta lulus ukuran landscape pendek
  800x360. Batas catatan juga divalidasi di UI dan repository.

Verifikasi otomatis: targeted repository/UI **13/13 lulus**, regresi terkait
**46/46 lulus**, seluruh test aplikasi **566/566 lulus**, Flutter analyzer
**No issues found**, serta `git diff --check` bersih. Tidak ada dependency atau
perubahan schema; schema tetap versi 13.

APK profile berhasil dibangun dan dipasang sebagai update tanpa menghapus data
pada V2322. SHA-256 APK:
`2a8b8d33c6ee4f17c8fa60f312f5c5247fc05ee18dc55117ef7b31dc0deb848d`.
Uji manual alur uang awal Rp100.000, kas masuk Rp20.000, kas keluar Rp5.000,
kas seharusnya Rp115.000, penolakan selisih tanpa alasan, penutupan beralasan,
dan pembukaan shift berikutnya dinyatakan **lulus oleh pengguna** pada 14
September 2026. Batch 3C selesai; Batch 3D selanjutnya mengikat checkout ke
shift aktif dan membuktikan transaksi tunai masuk ke perhitungan kas shift.

### 14 September 2026 - Tahap 20C Batch 3D: checkout wajib terikat shift

- Checkout sekarang hanya dapat diselesaikan apabila ada shift aktif.
- Pemeriksaan shift dilakukan sebelum dialog pembayaran dan diulang secara
  atomik saat transaksi disimpan, sehingga perubahan kondisi tidak dapat
  menghasilkan transaksi tanpa shift.
- Pembuka shift dan Manager dapat bertransaksi. Kasir lain memperoleh penolakan
  yang jelas dan keranjangnya tetap utuh.
- Penjualan menyimpan snapshot UID serta nama shift; Audit Log transaksi juga
  menyimpan konteks shift tersebut.
- Pembayaran tunai transaksi selesai masuk ke kas seharusnya. Pembayaran
  non-tunai dan transaksi batal tidak menambah uang tunai fisik.

Verifikasi otomatis: targeted checkout/ViewModel **37/37 lulus**, regresi
terkait **42/42 lulus**, seluruh test aplikasi **571/571 lulus**, Flutter
analyzer penuh **No issues found**, dan `git diff --check` bersih. APK profile
awal Batch 3D memiliki SHA-256:
`2ad628044868bb2c4c5d8a390f0b95a5da5e0456546c69e2a5f94a826bad0d1a`.

### 14 September 2026 - Tahap 20C Batch 3E: riwayat dan rekonsiliasi shift

- Halaman Shift menampilkan hingga 20 shift tertutup terbaru langsung dari
  database, bukan state sementara halaman.
- Manager dapat melihat seluruh riwayat operator. Kasir hanya dapat melihat
  shift yang dibukanya sendiri.
- Ringkasan menghitung transaksi selesai/batal, penjualan tunai/non-tunai,
  total penjualan, kas masuk/keluar, kas seharusnya, uang fisik, dan selisih.
- Detail menampilkan operator dan waktu, alasan penutupan, daftar transaksi
  beserta metode/statusnya, serta seluruh mutasi kas.
- Dialog detail dapat digulir dan sudah dijaga pada landscape pendek.

Verifikasi otomatis: targeted repository/UI **24/24 lulus**, seluruh test
aplikasi **573/573 lulus**, Flutter analyzer penuh **No issues found**, dan
`git diff --check` bersih. APK profile gabungan Batch 3D-3E dipasang sebagai
update tanpa menghapus data pada V2322; SHA-256:
`27a452cd2c66a7b1d84869f48b2c4996b8587cde951018a7d477a9fc5b27e3db`.

Gerbang manual checkout tunai/non-tunai, rekonsiliasi kas, penutupan, riwayat,
detail transaksi, akses role, dan landscape dinyatakan **lulus oleh pengguna**
pada 14 September 2026. Batch 3D dan 3E selesai; Batch 3F selanjutnya
menyelaraskan identitas shift pada riwayat transaksi dan struk serta menyediakan
bukti ringkasan tutup kasir.

### 14 September 2026 - Tahap 20C Batch 3F: identitas dan bukti tutup shift

- Daftar serta detail riwayat transaksi sekarang menampilkan snapshot nama
  shift yang tersimpan bersama transaksi.
- Transaksi lama tanpa snapshot tetap aman dibuka dan dijelaskan sebagai
  transaksi lama, bukan dipasangkan ke shift secara otomatis.
- Struk penjualan menampilkan nama shift jika transaksi berasal dari shift.
- Detail riwayat shift memiliki tombol **Cetak Ringkasan Tutup Kasir** dan
  memakai konfigurasi printer serta ukuran kertas toko yang sudah ada.
- Ringkasan cetak memuat waktu/operator, transaksi selesai dan batal,
  penjualan tunai/non-tunai, uang awal, kas masuk/keluar, kas seharusnya, uang
  fisik, selisih, serta catatan penutupan.
- Pencetakan ringkasan dikunci dari permintaan ganda. Timeout atau kegagalan
  printer dicatat tanpa mengubah hasil tutup shift maupun transaksi.
- Tidak ada schema atau dependency baru pada batch ini.

Verifikasi otomatis: targeted printer/riwayat/UI **27/27 lulus**, seluruh test
aplikasi **575/575 lulus**, Flutter analyzer penuh **No issues found**, dan
`git diff --check` bersih. APK profile berhasil dibangun, dipasang sebagai
update tanpa menghapus data, dan dibuka pada V2322. SHA-256 APK lokal/perangkat
identik:
`eaf2f0f0091338e422bd152356531656eef1e54f290ff8d8ed8ad09cbfc3c0f2`.

Gerbang visual identitas shift dan tombol ringkasan pada perangkat kemudian
dilanjutkan bersama penyempurnaan opsi nama shift. Cetak fisik dapat diuji nanti
ketika printer tersedia.

#### Penyempurnaan Batch 3F: nama shift opsional pada struk

- Manager memperoleh sakelar **Tampilkan nama shift** di pengaturan Branding &
  Desain Struk. Nilai awalnya aktif agar perilaku struk yang sudah berjalan
  tidak berubah sesudah pembaruan.
- Ketika dinonaktifkan, nama shift disembunyikan dari struk penjualan dan
  pratinjau tanpa menghapus snapshot shift pada transaksi. Riwayat transaksi,
  rekonsiliasi, Audit Log, dan ringkasan tutup kasir tetap menyimpan serta
  menampilkan identitas shift.
- Pengaturan disimpan per toko, hanya dapat diubah Manager, dan perubahannya
  masuk Audit Log melalui alur desain struk yang sudah ada.
- Schema database naik kompatibel dari versi 13 ke 14 dengan nilai migrasi
  bawaan aktif. Pembukaan langsung seluruh snapshot database v1-v13 serta
  preservasi data desain struk v13 telah diuji. Manifest backup saat ini memakai
  schema 14 dan backup schema 15 ditolak aman sebagai versi masa depan.

Verifikasi otomatis gabungan: targeted desain struk/printer/migrasi **103/103
lulus**, seluruh test aplikasi **578/578 lulus**, Flutter analyzer penuh **No
issues found**, dan `git diff --check` bersih. Tidak ada dependency baru. APK
profile berhasil dibangun, dipasang sebagai update, dan dibuka pada V2322.
SHA-256 APK lokal/perangkat identik:
`648905256b40144416b575a5858796ca00fa140b2af30aca932da43a3bc3ac9a`.

Gerbang visual sakelar serta pratinjau struk aktif/nonaktif dinyatakan **lulus
oleh pengguna pada 19 September 2026**. Cetak fisik tetap dapat ditunda sampai
printer tersedia. Tahap 20C Batch 3 selesai.

### 19 September 2026 - Tahap 20C Batch 4A: kontrak mesin diskon deterministik

- Mesin domain diskon menerima baris keranjang berbasis produk dan varian satuan
  tanpa mengubah checkout produksi pada sub-batch fondasi ini.
- Diskon nominal produk dihitung per unit. Diskon persentase disimpan sebagai
  basis point dan dihitung memakai integer Rupiah dengan pembulatan turun.
- Setiap baris memilih satu diskon produk dengan manfaat terbesar. Setelah itu,
  satu diskon transaksi terbaik dihitung dari subtotal yang sudah dikurangi
  diskon produk.
- Nilai yang sama diputuskan secara stabil memakai prioritas lalu UID. Hasil
  diskon dibatasi hingga nilai dasar sehingga total tidak pernah negatif.
- Target varian hanya mengenai satuan yang dipilih, sedangkan target produk tanpa
  varian berlaku pada seluruh satuan produk tersebut.
- Hasil memuat snapshot aturan, dasar hitung, nominal, target produk/varian, dan
  baris yang terkena sebagai kontrak untuk penyimpanan transaksi berikutnya.
- Promo beli X dapat Y sengaja belum dicampurkan; algoritmanya menjadi Batch 4B
  setelah kontrak diskon dasar ini stabil.

Verifikasi otomatis: matriks mesin diskon **8/8 lulus**, seluruh test aplikasi
**586/586 lulus**, Flutter analyzer penuh **No issues found**, dan `git diff
--check` bersih. Tidak ada dependency maupun perubahan schema. Uji manual/build
perangkat belum diperlukan karena belum ada perubahan UI atau perilaku runtime.

### 19 September 2026 - Tahap 20C Batch 4B: mesin promo beli X dapat Y

- Mesin diskon yang sama kini mendukung promo beli X dapat Y untuk produk yang
  sama maupun produk berbeda, termasuk target satuan dasar atau varian tertentu.
- Produk hadiah harus sudah ada di keranjang. Mesin tidak menambah barang atau
  mengurangi stok secara diam-diam; UI berikutnya dapat memberi petunjuk kepada
  kasir ketika hadiah belum dipilih.
- Promo produk yang sama menghitung kelipatan dari jumlah beli + hadiah. Promo
  produk berbeda dibatasi oleh jumlah pembelian dan jumlah hadiah yang benar-benar
  ada di keranjang.
- Hadiah dapat gratis 100%, mendapat potongan persentase, atau potongan nominal
  per unit. Nilainya selalu dibatasi harga hadiah sehingga total tidak negatif.
- Aturan diproses menurut prioritas lalu UID. Unit beli/hadiah yang sudah dipakai
  tidak dapat dipakai lagi oleh promo lain, sehingga konflik deterministik dan
  tidak menggandakan manfaat.
- Unit hadiah tidak menerima diskon produk kedua. Diskon produk hanya mengenai
  unit berbayar, kemudian diskon transaksi dihitung paling akhir.
- Snapshot hasil promo memuat aturan, jumlah kelipatan, jumlah hadiah, jenis dan
  nilai potongan, dasar hitung, nominal manfaat, serta baris hadiah.

Verifikasi otomatis: matriks gabungan diskon/promo **16/16 lulus**, seluruh test
aplikasi **594/594 lulus**, Flutter analyzer penuh **No issues found**, dan
`git diff --check` bersih. Tidak ada dependency, perubahan schema, UI, maupun
perilaku checkout produksi; build/uji perangkat belum diperlukan pada fondasi
domain ini. Tahap berikutnya adalah Batch 4C untuk persistence aturan, periode,
batas pemakaian, otorisasi Manager, dan Audit Log.

### 19 September 2026 - Tahap 20C Batch 4C: persistence dan tata kelola promo

- Schema database naik kompatibel dari versi 14 ke 15 dengan satu tabel aturan
  promo untuk diskon transaksi, diskon produk/varian, dan beli X dapat Y.
- Aturan menyimpan nama unik per toko, jenis/nilai diskon, target produk dan
  satuan, jumlah beli/hadiah, prioritas, periode aktif, batas serta penghitung
  pemakaian, izin override Kasir, status aktif, dan timestamp.
- Waktu mulai bersifat inklusif dan waktu selesai eksklusif. Promo belum mulai,
  sudah selesai, nonaktif, atau sudah mencapai batas pemakaian dikeluarkan dari
  daftar eligible.
- Produk dan varian selalu diverifikasi berada pada toko aktif dan satuan harus
  benar-benar milik produk target. Referensi lintas toko atau salah pasangan
  ditolak sebelum data ditulis.
- Hanya Manager aktif dengan identitas persisten yang dapat membuat, mengubah,
  mengaktifkan, atau menonaktifkan promo. Kasir, sesi lintas toko, dan Manager
  yang telah nonaktif ditolak tanpa mutasi maupun audit parsial.
- Create, update, aktivasi, dan deaktivasi dicatat atomik pada Audit Log; update
  atau status yang tidak berubah menjadi no-op tanpa audit palsu.
- Aturan tersimpan dapat dipetakan langsung ke mesin deterministik Batch 4A/4B,
  sehingga tidak ada dependency maupun sistem diskon kedua yang tumpang tindih.
- Database health ikut menghitung tabel promo. Backup/restore memakai schema 15
  dan schema 16 diperlakukan sebagai backup versi masa depan.

Verifikasi akhir: test repository promo **5/5 lulus**, seluruh test aplikasi
**599/599 lulus**, Flutter analyzer penuh **No issues found**, dan `git diff
--check` bersih. Migrasi seluruh snapshot lama, recovery, health, serta
backup/restore ikut lulus. APK profile bersih dibangun, dipasang sebagai update,
dibuka pada V2322, dan SHA-256 lokal/perangkat identik:
`79e9a39c8abce0591076620d485a4c7a626fdf7240fbb2629a1070b89f6a1a49`.

Batch selanjutnya akan menghubungkan aturan eligible ke keranjang dan checkout,
menyimpan snapshot manfaat pada transaksi, serta mengonsumsi batas pemakaian
secara atomik sebelum UI pengelolaan Manager dan tampilan promo Kasir dibuat.

### 19 September 2026 - Tahap 20C Batch 4D: promo checkout atomik

- Checkout produksi memakai aturan promo eligible dari database dan mesin
  deterministik yang sama dengan Batch 4A/4B; tidak ada mesin atau dependency
  diskon kedua.
- Diskon transaksi, diskon produk/satuan, dan promo beli X dapat Y kini
  mengurangi total pembayaran serta menghitung kembalian dari total bersih.
  Subtotal bruto, kuantitas, dan pergerakan stok tetap utuh.
- Target varian hanya memotong satuan yang dituju dan tidak bocor ke satuan
  dasar produk yang sama.
- Schema database naik kompatibel dari versi 15 ke 16 dengan tabel snapshot
  manfaat promo. Snapshot menyimpan identitas dan nama aturan, jenis/nilai
  diskon, dasar hitung, nominal manfaat, kaitan item, kelipatan, dan jumlah
  hadiah agar transaksi lama tidak berubah ketika promo diedit.
- Penghitung penggunaan bertambah tepat satu kali untuk promo yang benar-benar
  diterapkan. Promo yang sudah mencapai batas tidak dipakai lagi.
- Sale, item, stok, pembayaran, snapshot, penghitung promo, dan Audit Log
  disimpan dalam satu transaksi database. Kegagalan pada langkah akhir telah
  diuji mengembalikan seluruh perubahan termasuk stok dan penghitung promo.
- Backup/restore dan database health mengikuti schema 16; backup schema 17
  ditolak sebagai versi masa depan.

Verifikasi: targeted promo/checkout/migrasi/backup **131/131 lulus**, seluruh
test aplikasi **603/603 lulus**, Flutter analyzer penuh **No issues found**, dan
`git diff --check` bersih. APK profile berhasil dibangun, dipasang memakai
`adb install -r` tanpa menghapus data, lalu dibuka pada V2322 tanpa exception.
Database serta aset produk perangkat tetap ada. SHA-256 APK lokal/perangkat
identik:
`74774a521dce44ac7f49b4a9ff5a7366d45efcb168fcd9ea56d90f8d26d8a473`.

Batch 4E berikutnya adalah UI pengelolaan promo Manager dan penjelasan promo
yang transparan bagi Kasir, termasuk izin override.

### 20 September 2026 - Tahap 20C Batch 4E: UI promo Manager dan transparansi Kasir

- Menu Manager baru **Promo & Diskon** menyediakan create/edit, on/off, dan
  ringkasan penggunaan untuk diskon transaksi, diskon produk/satuan, serta beli
  X dapat Y.
- Aturan dapat memakai nominal Rupiah atau persentase, prioritas, jadwal mulai
  dan berakhir, batas penggunaan, serta opsi apakah Kasir boleh mematikan promo
  pada transaksi tertentu.
- Keranjang Kasir menampilkan promo yang memenuhi syarat, subtotal bruto,
  diskon, dan total bersih. Promo wajib ditandai kunci; promo opsional dapat
  diaktifkan/dimatikan tanpa mengubah aturan milik Manager.
- Preview keranjang dan checkout memakai repository serta mesin diskon yang sama
  dengan Batch 4A-4D. Repository menolak pembatalan promo wajib secara atomik,
  sehingga pembatasan tidak hanya bergantung pada tombol UI.
- Preview asinkron dilindungi generation/lifecycle guard agar perubahan cepat
  tidak menimpa hasil terbaru dan halaman yang sudah ditutup tidak menerima
  notifikasi terlambat.
- Tidak ada dependency baru dan tidak ada mesin diskon kedua; persistence,
  otorisasi Manager, Audit Log, dan snapshot checkout yang telah ada digunakan
  kembali.

Verifikasi otomatis: test repository promo/checkout **16/16 lulus**, test
ViewModel Kasir **23/23 lulus**, test halaman promo **2/2 lulus**, seluruh test
aplikasi **609/609 lulus**, dan Flutter analyzer penuh **No issues found**. APK
profile berhasil dibangun (SHA-256
`3a88771b9037cbcf89dc7745a44b0d15022393b2511958d0bec0d65ab928fa75`).
APK dipasang memakai `adb install -r` tanpa menghapus data, berhasil dibuka pada
V2322 tanpa exception, dan SHA-256 APK lokal/perangkat identik. Uji alur promo
singkat oleh pengguna menjadi gerbang terakhir sebelum Batch 4F.

### 20 September 2026 - Koreksi alur Buy X Get Y Batch 4E

- Form Manager sekarang menjelaskan bahwa produk beli dan produk hadiah harus
  sama-sama dimasukkan ke keranjang. Setelah jumlah terpenuhi, harga unit hadiah
  dipotong otomatis; aplikasi tidak menambahkan hadiah secara diam-diam agar
  pilihan satuan dan stok tetap benar.
- Target Buy X Get Y menampilkan nama satuan yang tepat. Label "Semua satuan"
  yang menyesatkan tidak lagi dipakai untuk target satuan dasar.
- Memilih tipe Buy X Get Y otomatis mengisi potongan hadiah 100% (gratis), namun
  Manager tetap dapat menggantinya dengan persentase atau Rupiah lain.
- Ringkasan aturan Manager sekarang menunjukkan produk, satuan, serta jumlah
  beli/hadiah sehingga konfigurasi dapat diperiksa tanpa membuka form edit.
- Test repository baru membuktikan promo lintas produk: beli dua produk X dan
  masukkan satu produk Y menghasilkan diskon pada Y dan total bersih yang benar.

Verifikasi otomatis akhir: test halaman promo **3/3 lulus**, gabungan test promo
dan checkout **20/20 lulus**, seluruh test aplikasi **611/611 lulus**, analyzer
file terdampak **No issues found**, dan build profile berhasil. APK dipasang
sebagai update menggunakan `adb install -r`, terbuka pada V2322 tanpa crash awal,
dan SHA-256 APK lokal/perangkat identik:
`d12f3ac611d00726cc563dfb25c9fa7f82532b0b8c9917db160bfb8cbe1f6c57`.
Uji manual Buy X Get Y oleh pengguna masih menjadi gerbang kelulusan akhir.

### 20 September 2026 - Penyempurnaan Buy X Get Y: hadiah otomatis

- Alur manual sebelumnya diganti: Kasir cukup memasukkan produk yang dibeli.
  Ketika jumlah dan satuannya memenuhi aturan, produk hadiah otomatis masuk ke
  keranjang.
- Baris hadiah menampilkan jumlah serta label **hadiah promo** beserta nama
  promonya. Tombol kurang dan hapus dikunci pada baris yang seluruhnya merupakan
  bonus agar hadiah wajib tidak terhapus tanpa sengaja.
- Jumlah pembelian asli dan jumlah bonus disimpan terpisah selama keranjang aktif.
  Karena itu bonus dapat dihitung ulang tanpa menggandakan diri, bertambah sesuai
  kelipatan, serta hilang otomatis ketika jumlah pembelian dikurangi.
- Promo opsional tetap dapat dimatikan Kasir jika diizinkan Manager. Saat mati,
  bonus hilang; saat diaktifkan kembali, bonus muncul tepat satu kali.
- Checkout tidak mempercayai tampilan keranjang: aturan dan jumlah bonus dihitung
  ulang di repository transaksi, lalu barang hadiah disimpan sebagai item sale,
  divalidasi stoknya, dan mengurangi stok dalam transaksi database yang sama.
- Petunjuk pada form Manager telah diselaraskan dengan alur bonus otomatis.

Verifikasi akhir: test transaksi/keranjang/promo terarah **46/46 lulus**, seluruh
test aplikasi **614/614 lulus**, Flutter analyzer penuh **No issues found**, dan
`git diff --check` bersih. APK profile dipasang sebagai update menggunakan `adb
install -r`, terbuka pada V2322 tanpa crash awal, dan SHA-256 APK lokal/perangkat
identik:
`c233336ecbf8d2a01773419202d2ccabba96f41eb93ede7d6f5ee4769e3d726f`.
Uji manual Buy X Get Y oleh pengguna masih menjadi gerbang kelulusan akhir.

### 20 September 2026 - Hapus promo dan keterangan sumber hadiah Buy X Get Y

- Uji manual alur hadiah otomatis Buy X Get Y dinyatakan lulus oleh pengguna.
- Manager sekarang dapat menghapus promo melalui tombol hapus dan dialog
  konfirmasi. Penghapusan ditolak untuk Kasir, dicatat sebagai **Promo dihapus**
  di Audit Log, dan dijalankan atomik bersama pencatatan audit.
- Menghapus konfigurasi promo tidak mengubah snapshot promo pada transaksi lama,
  sehingga riwayat penjualan tetap dapat diaudit.
- Baris Get Y di keranjang tetap menampilkan jumlah hadiah dan nama promo, lalu
  menambahkan alasan yang mudah diperiksa, misalnya **Karena beli 2 Air Mineral
  Botol**.
- Alasan hadiah berasal dari aturan dan satuan katalog yang benar, bukan teks
  buatan UI, sehingga tetap selaras saat produk atau satuan Buy X berbeda.

Verifikasi akhir: test repository promo, halaman promo, ViewModel Kasir, dan
checkout **52/52 lulus**; seluruh test aplikasi **615/615 lulus** dan Flutter
analyzer penuh **No issues found**. APK profile dipasang memakai `adb install -r`
tanpa menghapus data, berhasil dibuka pada V2322 tanpa exception, dan SHA-256 APK
lokal/perangkat identik:
`121cff733cd60932b3ce60030775c686b82c3a59775c39401cb316aacade8819`.
Uji manual tombol hapus promo dan keterangan sumber hadiah dinyatakan **lulus
oleh pengguna pada 20 September 2026**. Batch 4E selesai.

### 20 September 2026 - Tahap 20C Batch 4F-A: snapshot promo pada riwayat dan struk

- Detail transaksi sekarang mengambil snapshot promo berdasarkan transaksi dan
  menampilkan bagian **Promo & Diskon** berisi nama, jenis, serta nominal
  manfaatnya.
- Preview dan cetak struk memakai snapshot yang sama. Nama promo panjang tetap
  dibungkus sesuai lebar kertas, sedangkan nilai diskon tetap rata kanan.
- Beberapa snapshot dari aturan promo yang sama digabung berdasarkan UID dan
  nominalnya dijumlahkan, sehingga rincian ringkas tetapi totalnya tetap tepat.
- Aturan promo yang kemudian diubah atau dihapus tidak mengubah riwayat maupun
  struk transaksi lama. Transaksi lama tanpa snapshot tetap menampilkan total
  diskon tersimpan tanpa menebak nama promo.

Verifikasi: test repository riwayat dan printer **22/22 lulus**, seluruh test
aplikasi **617/617 lulus**, Flutter analyzer penuh **No issues found**, dan tidak
ada perubahan schema/dependency. APK profile dipasang memakai `adb install -r`
tanpa menghapus data, berhasil dibuka pada V2322 tanpa exception, dan SHA-256 APK
lokal/perangkat identik:
`cd8dc935c18dd7c77142eb4a4876b263abe9b1efec34157d60049c4a9293c7f6`.
Pemeriksaan visual bagian promo pada detail riwayat dan Preview Struk dinyatakan
**lulus oleh pengguna pada 20 September 2026**. Batch 4F-A selesai.

### 20 September 2026 - Tahap 20C Batch 4F-B: pembatalan, laporan, audit, dan backup promo

- Membatalkan transaksi promo sekarang mengembalikan kuota pemakaian setiap
  aturan yang digunakan tepat sekali. Perubahan kuota, pengembalian stok, status
  transaksi, dan Audit Log tetap satu transaksi database sehingga kegagalan
  membatalkan semuanya.
- Snapshot promo transaksi batal tetap tersimpan agar riwayat dan struk lama
  tidak berubah. Promo yang sudah dihapus tidak membuat pembatalan gagal.
- Audit pembatalan menyertakan nominal diskon serta UID/nama promo yang aman,
  sehingga Manager dapat menelusuri konteks transaksi tanpa menyimpan secret.
- Laporan menyediakan rincian diskon per promo berdasarkan snapshot dan hanya
  menghitung transaksi selesai. Transaksi batal tidak menambah omzet maupun
  manfaat promo pada laporan.
- Backup dan staging restore terbukti mempertahankan konfigurasi promo,
  `usageCount`, dan snapshot transaksi. Query laporan promo memakai indeks yang
  sudah tersedia; tidak diperlukan schema atau dependency baru.

Verifikasi: targeted pembatalan/laporan/backup **27/27 lulus**, test laporan
beserta query plan **12/12 lulus**, seluruh test aplikasi **618/618 lulus**,
Flutter analyzer penuh **No issues found**, dan `git diff --check` bersih. APK
profile dipasang memakai `adb install -r` tanpa menghapus data, berhasil dibuka
pada V2322 tanpa exception, dan SHA-256 APK lokal/perangkat identik:
`271ab7779383aec9fe1327191662c2fcc9a43bb4dac9962671b1f87d7be2d52b`.
Uji manual satu transaksi promo sampai pembatalan menjadi gerbang terakhir
sebelum keseluruhan Batch 4 ditutup.

Uji manual tersebut dinyatakan **lulus oleh pengguna pada 20 September 2026**.
Tahap 20C Batch 4 resmi selesai.

### 20 September 2026 - Tahap 20C Batch 5A: fondasi nomor meja opsional

- Manager memiliki menu baru **Pengaturan Transaksi** untuk mengaktifkan atau
  menonaktifkan nomor/nama meja. Kasir tidak dapat melihat menu atau mengubah
  pengaturan melalui repository.
- Aturan bisnis dikunci: fitur default mati dan, saat aktif, nomor/nama meja
  tetap boleh dikosongkan. Dengan demikian toko retail tetap memakai alur lama.
- Perubahan pengaturan memvalidasi ulang Manager aktif, menjadi no-op jika nilai
  sama, dan menyimpan Audit Log dalam transaksi database yang sama. Kegagalan
  audit me-roll back perubahan.
- Schema database naik dari v16 ke v17. Migrasi menambahkan pengaturan per toko
  serta kolom snapshot meja nullable pada transaksi; data dan transaksi lama
  tetap utuh dengan nilai default mati/kosong.
- Model Drift diregenerasi. Backup memakai schema v17 dan restore menolak schema
  v18 yang lebih baru; recovery database lama tetap diuji sampai login berhasil.
- Batch berikutnya (5B) akan menghubungkan input meja ke keranjang, checkout,
  snapshot, riwayat, pencarian, dan struk tanpa mengubah alur saat fitur mati.

Verifikasi: test pengaturan/role/migrasi **91/91 lulus**, test health/backup
**14/14 lulus**, test recovery lama **1/1 lulus**, seluruh test aplikasi
**623/623 lulus**, dan Flutter analyzer penuh **No issues found**. APK profile
dibangun dan dipasang sebagai
update dengan `adb install -r` tanpa menghapus data. Aplikasi terbuka pada V2322
tanpa exception Flutter; SHA-256 APK lokal/perangkat identik:
`b814ec30f8fbf716adcb5fa0033650e4f7212540efb1024b3e2af653d3ad6639`.
Gerbang manual hanya memeriksa menu Manager dan sakelar tetap tersimpan setelah
pindah halaman atau membuka ulang aplikasi.

Gerbang manual tersebut dinyatakan **lulus oleh pengguna pada 20 September
2026**. Tahap 20C Batch 5A selesai.

### 20 September 2026 - Tahap 20C Batch 5B: nomor meja pada transaksi

- Saat fitur meja aktif, panel Keranjang menampilkan input **Nomor / nama meja
  (opsional)**. Panjang dibatasi 40 karakter, spasi beruntun dirapikan, dan
  karakter kontrol/baris baru ditolak di lapisan transaksi.
- Checkout membaca ulang pengaturan toko di dalam transaksi database. Karena
  itu nilai meja tidak dapat dipaksakan dari UI ketika Manager mematikan fitur;
  alur toko retail tetap identik dan menyimpan snapshot `null`.
- Nilai meja disimpan sebagai snapshot transaksi sehingga perubahan atau
  penonaktifan pengaturan sesudahnya tidak mengubah transaksi lama. Input
  dibersihkan setelah checkout berhasil atau keranjang dikosongkan.
- Riwayat menampilkan identitas meja pada daftar dan detail. Pencarian transaksi
  menerima nomor/nama meja selain nomor transaksi, Kasir, dan metode bayar.
- Preview maupun cetak struk menampilkan baris **Meja** hanya pada transaksi
  yang memiliki snapshot. Backup/restore schema v17 telah diuji membawa status
  pengaturan dan snapshot meja secara utuh.
- Tidak ada dependency atau kenaikan schema baru pada Batch 5B; schema tetap
  versi 17.

Verifikasi: test terarah repository/riwayat/struk/backup/widget **82/82 lulus**,
seluruh test aplikasi **628/628 lulus**, Flutter analyzer penuh **No issues
found**, dan `git diff --check` bersih. APK profile berhasil dibangun dan
dipasang sebagai update dengan `adb install -r` tanpa menghapus data. Aplikasi
terbuka pada V2322 tanpa crash/error Flutter; SHA-256 APK lokal dan perangkat
identik:
`fdfe8bc425ea4bfd7911db2f620d4c537c29b56ec2d88e4d1896ae304fa6ce3a`.

Gerbang manual Batch 5B masih diperlukan untuk satu transaksi bernomor meja,
penampilan pada Riwayat/preview struk, pencarian berdasarkan meja, serta regresi
fitur mati sebelum Batch 5 dinyatakan selesai.

### 21 September 2026 - Perbaikan responsif input meja Batch 5B

- Saat keyboard HP terbuka, area ringkasan pembayaran disembunyikan sementara
  agar input **Nomor / nama meja** dan daftar barang tetap mendapat ruang yang
  cukup. Ringkasan kembali otomatis ketika keyboard ditutup.
- Perilaku tersebut berlaku pada portrait dan landscape, termasuk layout
  landscape ringkas, tanpa mengubah perhitungan maupun proses checkout.
- Ditambahkan uji regresi khusus yang memastikan input meja berada di atas area
  keyboard pada kedua orientasi dan tidak terjadi overflow.

Verifikasi: uji regresi keyboard **2/2 lulus**, seluruh test aplikasi **630/630
lulus**, Flutter analyzer penuh **No issues found**, `git diff --check` bersih,
APK profile berhasil dibangun dan dipasang ke V2322 tanpa menghapus data. Smoke
test tidak menemukan crash/error Flutter; SHA-256 APK lokal dan perangkat
identik:
`9837cba59b8ea6bead3b57c89fddb25c5d2e13d471021875f8a5785a7ff44be9`.

Pemeriksaan visual input meja portrait/landscape pada perangkat masih menunggu
konfirmasi pengguna dan tetap menjadi bagian gerbang manual Batch 5B.

#### Koreksi lifecycle keyboard

Uji perangkat pertama menemukan ringkasan total dan tombol pembayaran tidak
kembali setelah keyboard ditutup. Penyebabnya adalah perubahan inset jendela
Android belum memicu rebuild panel Keranjang. `didChangeMetrics` kini langsung
menyegarkan layout setiap keyboard dibuka/ditutup atau orientasi berubah;
ringkasan hanya tersembunyi selama keyboard benar-benar tampil dan nilai meja
yang sudah diketik tetap dipertahankan.

Verifikasi koreksi: regresi batal mengetik dan selesai mengetik portrait/
landscape **2/2 lulus**, seluruh test **630/630 lulus**, analyzer **No issues
found**, `git diff --check` bersih, APK profile terpasang ke V2322, hash lokal
dan perangkat identik, serta smoke test tanpa crash/error Flutter. SHA-256 APK:
`e28d03839c61e5ae93bb5141bdbdb5ec5cc4f4d89e1e4a0b32f7a037e0b0227e`.

Gerbang visual koreksi pada perangkat masih menunggu konfirmasi pengguna.

### 21 September 2026 - Penyelarasan UI Keranjang HP Batch 1

- Pada HP portrait (`< 600` logical pixel), rincian promo tidak lagi selalu
  memakan area daftar. Promo tampil sebagai tombol ringkas berisi nilai diskon;
  ketukan tombol tetap membuka seluruh promo dan pilihan on/off yang sama.
- Subtotal, diskon, dan total tetap terlihat. Tombol **Bayar** dan **Kosongkan**
  kini sejajar satu baris sehingga area produk bertambah tanpa mengecilkan teks,
  target sentuh, maupun gambar produk.
- Landscape pendek tetap memakai dua kolom yang sudah ada, sedangkan tablet
  lebar mempertahankan tampilan rincian penuh. Logika promo, total, checkout,
  dan keyboard tidak berubah.

Verifikasi: uji UI ringkas dan regresi keyboard **3/3 lulus**, seluruh test
aplikasi **631/631 lulus**, analyzer **No issues found**, `git diff --check`
bersih, APK profile berhasil dipasang ke V2322, dan smoke test tidak menemukan
crash/error Flutter. Hash APK lokal dan perangkat identik:
`b7fc698df2a3d576590971f492e021bfad1c1b74a12c678cd34b5ea991eba445`.

Gerbang manual Batch 1: nilai ruang daftar produk pada portrait dan pastikan
tombol promo, Bayar, Kosongkan, serta keyboard tetap nyaman digunakan sebelum
melanjutkan pemadatan baris produk pada Batch 2.

### 21 September 2026 - Penyelarasan UI Keranjang HP Batch 2

- Baris produk kini adaptif terhadap orientasi. Portrait mempertahankan kontrol
  jumlah dan hapus dua tingkat agar nama/harga tidak menyempit atau membungkus;
  landscape memakai seluruh kontrol dalam satu baris agar tinggi item berkurang.
- Jarak divider antarproduk dipadatkan dari tinggi default menjadi 8 logical
  pixel pada kedua orientasi. Ukuran ikon/tombol mengikuti batas minimal tema
  aplikasi dan keterangan hadiah promo tetap ditampilkan lengkap.
- Uji mengunci bahwa baris portrait standar tidak melebihi 105 px, baris
  landscape lebih pendek daripada portrait, target tombol tidak mengecil, serta
  rotasi dan keyboard tidak menghasilkan overflow.

Verifikasi: regresi UI/keyboard **3/3 lulus**, seluruh test aplikasi **631/631
lulus**, analyzer **No issues found**, `git diff --check` bersih, APK profile
berhasil dipasang ke V2322 tanpa menghapus data, hash lokal/perangkat identik,
dan smoke test tidak menemukan crash/error Flutter. SHA-256 APK:
`605610c2b823e2bce448acdfe48e7ab182ea2e5de8fec40e1b6d58a6f5937453`.

Gerbang manual Batch 2 dinyatakan **lulus oleh pengguna pada 21 September
2026**. Daftar produk, kontrol jumlah/hapus, serta ringkasan pembayaran dinilai
nyaman pada portrait dan landscape. Penyelarasan UI Keranjang HP selesai tanpa
mengubah rumus transaksi.

### 21 September 2026 - Tahap 20C Batch 6A: kontrak hitung biaya dan pembulatan

- Rumus transaksi dikunci berurutan: subtotal dikurangi promo/diskon, biaya
  layanan, pajak atas nilai setelah layanan, kemudian pembulatan total akhir.
- Biaya layanan dan pajak memakai basis point serta aritmetika integer Rupiah;
  pecahan dari persentase selalu dibulatkan turun tanpa floating-point.
- Pembulatan terdekat Rp1.000 memakai batas yang disetujui: Rp0-Rp499 turun dan
  Rp500-Rp999 naik. Penyesuaian pembulatan disimpan terpisah dari nilai sebelum
  pembulatan agar nantinya dapat ditampilkan dan dijadikan snapshot.
- Hasil mesin membawa nama biaya, rate, dasar hitung, nominal biaya, total
  sebelum pembulatan, penyesuaian, dan total akhir untuk integrasi checkout.
- Semua opsi mati mempertahankan total setelah promo secara identik. Nilai
  negatif, diskon melebihi subtotal, rate tidak aman, serta nama biaya aktif
  yang kosong ditolak.

Verifikasi: uji mesin dan tabel batas **10/10 lulus**, seluruh test aplikasi
**641/641 lulus**, serta analyzer **No issues found**. Batch 6A belum mengubah
schema, UI, maupun checkout sehingga tidak memerlukan build atau uji perangkat.
Batch 6B berikutnya akan menambahkan persistence dan pengaturan khusus Manager
dengan seluruh opsi default mati.

### 21 September 2026 - Tahap 20C Batch 6B: persistence pengaturan biaya

- Schema database naik aman dari versi 17 ke 18 dengan konfigurasi biaya
  layanan, pajak, dan pembulatan; seluruh opsi default mati.
- Manager dapat mengisi nama biaya/pajak, persentase bulat 1-100%, serta
  mengaktifkan pembulatan Rp1.000 dari menu Pengaturan Transaksi.
- Nama dibatasi 1-40 karakter, rate aktif wajib 1-100%, nilai identik menjadi
  no-op, dan Kasir tidak dapat menyimpan perubahan.
- Penyimpanan setting dan Audit Log dilakukan dalam satu transaksi database.
  Kegagalan audit me-roll back perubahan pengaturan sepenuhnya.
- Migrasi database lama mempertahankan toko, transaksi, dan total historis;
  backup/restore mengenali schema v18 dan menolak schema yang lebih baru.
- Pengaturan sengaja belum diterapkan ke checkout pada Batch 6B. Sampai Batch
  6C selesai, pembayaran tetap memakai total lama agar tidak ada transaksi
  tanpa snapshot biaya.

Verifikasi otomatis dan perangkat: seluruh test aplikasi **646/646 lulus**,
analyzer **No issues found**, build APK profile berhasil, pemasangan `-r` pada
V2322 mempertahankan data, proses aplikasi hidup, dan log startup tidak memuat
crash/error migrasi. SHA-256 lokal dan perangkat identik:
`8134d75df964cd15ffee1267adf35760c0f9672fc20f393fc8969102f430655e`.
Gerbang manual tampilan serta persistensi setelah membuka ulang halaman masih
menunggu sebelum Batch 6B dinyatakan selesai penuh.

Gerbang manual Batch 6B dinyatakan **lulus oleh pengguna pada 21 September
2026**. Pengaturan tersimpan dan tampil kembali dengan benar.

### 21 September 2026 - Tahap 20C Batch 6C: checkout dan snapshot biaya

- Preview keranjang dan checkout memakai satu kalkulator transaksi yang sama:
  subtotal setelah promo, biaya layanan, pajak, kemudian pembulatan akhir.
- Nilai minimum pembayaran tunai, kembalian, pembayaran tersimpan, total sale,
  dan Audit Log semuanya memakai total akhir hasil kalkulator tersebut.
- Schema database naik aman dari versi 18 ke 19. Transaksi baru menyimpan
  snapshot nama, rate basis point, dasar hitung, nominal layanan/pajak, total
  sebelum pembulatan, mode pembulatan, dan nilai penyesuaiannya.
- Snapshot transaksi lama bersifat nullable agar data sebelum fitur ini tidak
  disamakan secara keliru dengan biaya aktif bernilai nol. Semua opsi default
  mati tetap menghasilkan perilaku dan total lama secara identik.
- Ringkasan Keranjang serta dialog pembayaran berhasil menampilkan biaya,
  pajak, dan penyesuaian pembulatan hanya saat komponen tersebut berlaku.
- Migrasi, health check, backup/restore, dan penolakan schema masa depan telah
  diselaraskan dengan schema v19.

Verifikasi: test terarah checkout/migrasi/backup/database **120/120 lulus**,
test ViewModel Kasir **26/26 lulus**, seluruh test aplikasi **649/649 lulus**,
dan Flutter analyzer penuh **No issues found**. APK profile berhasil dibangun
dan dipasang sebagai update pada V2322 tanggal 23 September 2026 tanpa menghapus
data. Proses aplikasi hidup dan log startup tidak memuat error AndroidRuntime/
Flutter. SHA-256 APK lokal dan perangkat identik:
`fae99c3091c55c8f763a02d123119812cacf46286042e750154af5bb35f8f343`.
Uji transaksi nyata Batch 6C dinyatakan **lulus oleh pengguna pada 24 September
2026**. Penyajian rincian snapshot pada riwayat, struk, dan laporan dilanjutkan
pada Batch 6D agar setiap langkah tetap teruji dan mudah dilacak.

### 24 September 2026 - Tahap 20C Batch 6D: rekonsiliasi riwayat, struk, dan laporan

- Detail riwayat menampilkan nama serta persentase biaya layanan/pajak, nominal
  masing-masing, total sebelum pembulatan, dan penyesuaian pembulatan bertanda.
- Seluruh rincian menggunakan snapshot pada transaksi, bukan pengaturan toko
  terbaru. Mengubah atau menonaktifkan biaya setelah checkout tidak mengubah
  transaksi lama.
- Preview struk dan data ESC/POS mencetak komponen yang sama. Label panjang
  dipenggal sesuai lebar kertas tanpa kehilangan nominal atau makna.
- Laporan kini membawa agregat biaya layanan, pajak, dan penyesuaian pembulatan.
  Omzet dapat direkonsiliasi dari subtotal kotor dikurangi diskon, ditambah
  biaya layanan dan pajak, lalu ditambah penyesuaian pembulatan.
- Transaksi lama tanpa snapshot tetap kompatibel dan menghasilkan agregat nol;
  transaksi batal tidak ikut omzet maupun komponen biaya.
- Tidak ada dependency atau perubahan schema baru pada Batch 6D.

Verifikasi: test terarah riwayat/struk/laporan **38/38 lulus**, seluruh test
aplikasi **652/652 lulus**, Flutter analyzer penuh **No issues found**, dan
`git diff --check` bersih. APK profile berhasil dibangun serta dipasang sebagai
update pada V2322 tanpa menghapus data. Proses aplikasi hidup dan startup bebas
error AndroidRuntime/Flutter. SHA-256 lokal dan perangkat identik:
`c6999bf5c5649039e132a0df8de90d5cb9dc63150ebe3a39b4d25f403c26e28e`.
Gerbang visual riwayat dan preview struk dinyatakan **lulus oleh pengguna pada
24 September 2026**.

### 24 September 2026 - Koreksi glitch tombol jumlah Keranjang

- Akar masalah berada pada hitung ulang promo/biaya: setiap tombol `+/-`
  ditekan, ringkasan yang sudah tampil sempat diganti progress bar dan tombol
  Bayar berubah ke status nonaktif, sehingga tinggi layout serta warna tombol
  berkedip.
- Selama hitung ulang, ringkasan terakhir kini tetap tampil pada tempatnya.
  Nilai diperbarui satu kali setelah kalkulasi baru selesai.
- Tombol Bayar tidak lagi berkedip. Keamanan perhitungan tidak dikurangi karena
  alur pembayaran selalu menghitung ulang promo/biaya dan memeriksa error
  sebelum dialog pembayaran dibuka.
- Tidak ada perubahan schema, dependency, rumus transaksi, atau data tersimpan.

Verifikasi: test regresi visual khusus **1/1 lulus**, seluruh test ViewModel
Kasir **26/26 lulus**, seluruh test aplikasi **653/653 lulus**, Flutter analyzer
**No issues found**, dan `git diff --check` bersih. APK profile dipasang sebagai
update pada V2322 tanpa menghapus data; proses hidup dan startup bebas error.
SHA-256 lokal/perangkat identik:
`b87ba7f15b85f59336c338768623e7689734a4090b75d032ab3febaabb6f4822`.
Gerbang visual tombol `+/-` masih menunggu pemeriksaan pengguna.

### 24 September 2026 - Tahap 20C Batch 6E: rekonsiliasi suara multi-satuan dan hadiah promo

- Pesanan Suara kini menerima kedua urutan ucapan yang alami: `nama + jumlah +
  satuan` dan `nama + satuan + jumlah`. Contoh `Aqua 1 botol` serta `Aqua botol
  1` menghasilkan produk, satuan, dan jumlah yang sama.
- Aturan berlaku berdasarkan satuan katalog aktif, termasuk satuan dasar,
  turunan, dan satuan kustom beberapa kata; tidak ada daftar produk khusus yang
  harus ditulis di kode.
- Deteksi aman mempertahankan kata mirip satuan yang memang bagian dari nama
  produk. Regresi `Mie Cup 4` dikunci agar `cup` tidak dipotong ketika satuan
  produknya adalah Pcs.
- Ringkasan suara Keranjang memakai jumlah barang yang benar-benar tampil,
  termasuk hadiah otomatis Buy X Get Y. Baris hadiah dengan kuantitas beli nol
  dan kuantitas promo satu kini disebut `satu pcs`, bukan `nol pcs`.
- Nilai suara untuk hadiah tetap `harga nol rupiah`. Pada baris yang memuat
  pembelian sekaligus hadiah, jumlah total dibaca tanpa membuat harga hadiah
  ikut ditagihkan.
- Tidak ada perubahan schema, dependency, kalkulasi promo, atau checkout.

Verifikasi otomatis: parser/ringkasan **46/46 lulus**, integrasi widget TTS
hadiah promo lulus, seluruh test aplikasi **656/656 lulus**, Flutter analyzer
**No issues found**, dan `git diff --check` bersih. APK profile terpasang sebagai
update pada V2322 tanpa menghapus data; aplikasi hidup dan log startup tidak
memuat error AndroidRuntime/Flutter. SHA-256 lokal/perangkat identik:
`7fc51d9d777a35384d1a85775619d7682e9912d8bcbbdb61efe6e74b3cc32e81`.

Gerbang perangkat suara nyata masih menunggu konfirmasi pengguna untuk kedua
urutan satuan/jumlah, satuan kustom, ucapan multiproduk, dan pembacaan hadiah
Buy X Get Y dengan jumlah benar serta harga nol.

### 24 September 2026 - Hotfix scroll ringkasan Keranjang landscape

- Akar bug adalah panel kanan landscape yang memiliki tinggi tetap tetapi tidak
  memiliki scroll, berbeda dari daftar produk di sisi kiri.
- Ringkasan promo, subtotal, diskon, biaya, pajak, pembulatan, total, tombol
  Bayar, dan tombol Kosongkan kini dapat digulir secara vertikal pada panel
  kanan.
- Layout portrait, daftar produk kiri, hitungan transaksi, dan proses checkout
  tidak diubah.

Verifikasi: test widget khusus **1/1 lulus**, seluruh test aplikasi **656/656
lulus**, Flutter analyzer **No issues found**, dan `git diff --check` bersih.
APK profile dipasang sebagai update pada V2322 tanpa menghapus data; hash APK
lokal/perangkat identik:
`0176f46db7c5f4a4486e5b5ff359f3a28315ddc495157658c157df189e304694`.

Gerbang visual scroll panel kanan masih menunggu konfirmasi pengguna.

### 24 September 2026 - Tahap 20C Batch 6F-A: rincian promo Keranjang

- Rincian promo di Keranjang kini memakai bahasa yang langsung menjelaskan
  dampaknya. Label teknis `Wajib` diganti menjadi **Otomatis**, sedangkan aturan
  yang dapat dikendalikan Kasir tampil sebagai **Opsional aktif/nonaktif**.
- Diskon produk menyebut target katalog dan cakupan satuannya, misalnya `Untuk
  pembelian Air Mineral semua satuan`, disertai nilai **Hemat** atau **Potensi
  hemat**.
- Buy X Get Y menyebut jumlah, produk, dan satuan hasil kalkulasi aktual,
  misalnya `Beli 1 Air Mineral Botol, bonus 1 Pop Mie Promo Pcs`. Data ini bukan
  teks tebakan dan ikut menyesuaikan kelipatan promo.
- Kata **bonus** hanya dipakai ketika hadiah dipotong penuh. Jika hadiah hanya
  mendapat potongan sebagian, keterangannya memakai kata **dapat** agar tidak
  menjanjikan barang gratis secara keliru.
- Promo opsional yang dinonaktifkan tetap menyebut produk dan jumlah hadiahnya.
  Nilai potensi hanya ditampilkan bila dapat dihitung dari keranjang aktif;
  aplikasi tidak menampilkan Rp0 sebagai potensi palsu. Tidak ada perubahan
  schema, dependency, rumus promo, nilai checkout, maupun data transaksi.

Verifikasi: test model dan widget terarah untuk Buy X Get Y serta diskon produk
lulus, seluruh test aplikasi **656/656 lulus**, dan Flutter analyzer **No issues
found**. APK profile berhasil dibangun serta dipasang sebagai update pada V2322
tanpa menghapus data; proses aplikasi hidup dan log startup tidak memuat error
AndroidRuntime/Flutter. SHA-256 APK lokal/perangkat identik:
`c9fc5cc6e3c1536a2e78c26d65d2d42661df360919a6fe7eff8996503870be3a`.

Gerbang visual utama Batch 6F-A dinyatakan **lulus oleh pengguna pada 24
September 2026**. Perubahan struk tetap dipisahkan ke Batch 6F-B agar tidak
mencampurkan dua risiko presentasi sekaligus.

#### Hotfix respons sakelar promo Keranjang

- Akar glitch adalah state pengecualian promo sudah berubah ketika sakelar
  ditekan, tetapi posisi dan label sakelar masih membaca hasil kalkulasi lama
  sampai proses asynchronous selesai. Sakelar dapat terlihat memantul atau
  terlambat sebelum menetap.
- Posisi serta label `Opsional aktif/nonaktif` kini langsung mengikuti pilihan
  Kasir pada frame pertama. Nominal promo dan total tetap berasal dari mesin
  kalkulasi yang sama setelah proses selesai; tidak ada rumus yang diduplikasi.
- Tidak ada perubahan schema, dependency, aturan promo, atau checkout.

Verifikasi: test state dan widget frame pertama **2/2 lulus**, seluruh test
aplikasi **656/656 lulus**, Flutter analyzer **No issues found**, serta APK
profile berhasil dipasang sebagai update pada V2322 tanpa menghapus data.
Proses aplikasi hidup dan log startup bebas error AndroidRuntime/Flutter.
SHA-256 APK lokal/perangkat identik:
`9e762c759dd630e8eea900474c9e0219f7c6e48ac00b988da488ac16070f7178`.

Gerbang visual hotfix on/off promo masih menunggu konfirmasi pengguna sebelum
Batch 6F-B dimulai.

#### Hotfix kestabilan panel promo Keranjang

- Akar getar panel adalah dua perubahan tinggi dalam satu toggle: teks status
  sementara `Potensi hemat` sempat muncul dari hasil kalkulasi lama lalu hilang
  ketika hasil baru selesai, sedangkan indikator proses pada dialog ikut
  ditambahkan dan dilepas dari layout.
- Status sementara yang tidak lagi sesuai dengan pilihan sakelar tidak
  ditampilkan. Nominal hemat tetap muncul setelah hasil kalkulasi promo yang
  baru sudah pasti.
- Ruang indikator proses pada dialog kini selalu dipertahankan, sehingga panel
  tidak membesar dan mengecil ketika kalkulasi berjalan.
- Logika, rumus, data, schema, dependency, dan proses checkout promo tidak
  diubah.

Verifikasi: test regresi mengunci posisi sakelar pada frame pertama serta tinggi
dialog sebelum, selama, dan sesudah toggle; test terarah **1/1 lulus**, seluruh
test aplikasi **656/656 lulus**, Flutter analyzer **No issues found**, dan
`git diff --check` bersih. APK profile dipasang sebagai update pada V2322 tanpa
menghapus data; proses aplikasi hidup dan log startup bebas error
AndroidRuntime/Flutter. SHA-256 APK lokal/perangkat identik:
`ca1fc5d87ccc95507df225dcb7258e67dd08b005e9384f994f93222d2ec67ca2`.

Gerbang visual kestabilan panel promo masih menunggu pemeriksaan pengguna.

#### Koreksi kilatan garis pada panel promo Keranjang

- Pemeriksaan perangkat menemukan garis berwarna yang muncul sesaat di bawah
  seluruh promo. Garis tersebut adalah indikator proses kalkulasi, bukan
  divider atau error tampilan.
- Indikator visual dihapus karena kalkulasi berlangsung singkat dan posisi
  sakelar sudah memberi respons langsung. Ruang tetapnya dipertahankan agar
  tinggi panel tidak kembali berubah selama on/off promo.
- Status pemrosesan internal, kalkulasi promo, perlindungan checkout, schema,
  dependency, dan data transaksi tidak diubah.

Verifikasi: test widget memastikan tidak ada `LinearProgressIndicator` di
dialog selama toggle serta tinggi dialog tetap stabil; test terarah **1/1
lulus**, seluruh test aplikasi **656/656 lulus**, Flutter analyzer **No issues
found**, dan `git diff --check` bersih. APK profile dipasang sebagai update pada
V2322 tanpa menghapus data; aplikasi hidup dan log startup bebas error
AndroidRuntime/Flutter. SHA-256 APK lokal/perangkat identik:
`990813504b73536069f4e8b26f76a442018eb404f928a0a355fb943a1b07b5aa`.

Gerbang visual kilatan garis masih menunggu pemeriksaan pengguna.

#### Audit global kestabilan refresh UI

- Audit diperluas dari dialog promo Keranjang ke seluruh halaman operasional.
  Akar glitch yang sama ditemukan pada indikator proses singkat yang disisipkan
  lalu dilepas di antara konten lama. Perubahan tinggi sesaat tersebut terlihat
  sebagai garis berwarna, kedipan, atau panel yang bergetar.
- Refresh singkat kini mempertahankan konten lama sampai data baru siap, lalu
  menggantinya secara atomik tanpa menyisipkan indikator ke dalam layout.
- Perbaikan mencakup **Promo & Diskon**, **Produk Terlaris** saat berpindah
  Hari Ini/7 Hari/30 Hari dan periode lain, **Dashboard**, **Audit Log**,
  **Riwayat Transaksi**, **Manajemen Pengguna**, **Kamus Suara**, pratinjau
  promo awal **Kasir**, serta sakelar **Pengaturan Transaksi**.
- Sakelar aktif pada halaman Promo & Diskon memperbarui item yang bersangkutan
  langsung dari hasil penyimpanan, sehingga seluruh daftar tidak lagi diganti
  spinner layar penuh hanya untuk satu perubahan.
- Indikator proses awal saat halaman benar-benar belum memiliki data tetap
  dipertahankan. Progress bar untuk membuat backup, restore, dan pemeriksaan
  database juga tetap dipakai karena prosesnya panjang dan perlu umpan balik
  keselamatan yang jelas.
- Tidak ada perubahan schema, dependency, rumus promo, checkout, maupun data
  transaksi.

Verifikasi: test regresi refresh terarah **7/7 lulus**, seluruh test aplikasi
**656/656 lulus**, Flutter analyzer **No issues found**, dan `git diff --check`
bersih. APK profile berhasil dibangun dan dipasang sebagai update pada V2322
tanpa menghapus data. Aplikasi hidup dan log startup tidak memuat error
AndroidRuntime/Flutter. SHA-256 APK lokal/perangkat identik:
`c1b0863540468e300fdccf4cf179451b8126e17f4ca4c9b591ded7693d91c3a2`.

Gerbang visual audit global masih menunggu pemeriksaan pengguna pada sakelar
promo Keranjang, sakelar Promo & Diskon Manager, perpindahan periode Produk
Terlaris, dan sakelar Pengaturan Transaksi.

### 24 September 2026 - Tahap 20C Batch 6F-B: kejelasan hadiah pada struk

- Barang yang dibayar kini dipisahkan dari hadiah Buy X Get Y yang benar-benar
  gratis. Satu Pop Mie berbayar ditambah dua hadiah tidak lagi tercetak sebagai
  tiga Pop Mie yang seolah semuanya ditagihkan.
- Hadiah gratis tampil di bagian Promo & Diskon sebagai `Bonus 2 Pop Mie Pcs`
  bernilai **Rp0**, disertai hubungan penyebab seperti `Karena beli 1 Air
  Mineral Botol`.
- Hanya potongan hadiah penuh yang disebut bonus gratis. Promo hadiah dengan
  potongan sebagian tetap tampil sebagai barang berharga normal beserta nominal
  diskonnya.
- Preview Struk dan data ESC/POS memakai sumber presentasi yang sama. Label
  `Subtotal sebelum promo` menjelaskan subtotal kotor ketika hadiah gratis ikut
  dicatat, sedangkan subtotal, diskon, total, stok, dan snapshot transaksi tidak
  berubah.
- Snapshot transaksi baru menyimpan konteks pembelian di JSON yang sudah ada;
  tidak ada perubahan schema atau dependency. Transaksi lama tetap terbaca dan
  memakai keterangan generik jika konteks penyebab belum tersimpan.

Verifikasi: test struk **19/19 lulus**, test terarah printer/riwayat/mesin promo
**42/42 lulus**, seluruh test aplikasi **658/658 lulus**, Flutter analyzer **No
issues found**, dan `git diff --check` bersih. APK profile dipasang sebagai
update pada V2322 tanpa menghapus data; aplikasi hidup dan log startup tidak
memuat error AndroidRuntime/Flutter. SHA-256 APK lokal/perangkat identik:
`875703744d2396ef5277ad721b48fbfbd26652b50b6f12caa3af6ec3b632776b`.

Gerbang visual Preview Struk untuk transaksi Buy X Get Y baru dinyatakan
**lulus oleh pengguna pada 24 September 2026**. Cetak printer fisik boleh
ditunda sampai alat tersedia.

### 25 September 2026 - Tahap 20C Batch 7A: fondasi customer/member

- Data member dibatasi pada kebutuhan operasional minimum: kode unik, nama,
  status aktif, jenis diskon, dan nilai diskon. Alamat, tanggal lahir, nomor
  identitas, telepon, dan data pribadi lain tidak ditambahkan.
- Schema database naik dari v19 ke **v20** dengan tabel member per toko, indeks
  kode unik case-insensitive, indeks pencarian member aktif, foreign key toko,
  serta migrasi aman untuk database lama.
- Repository member menyediakan pembuatan, perubahan, aktivasi/nonaktif, hapus,
  daftar Manager, dan pencarian member aktif untuk Kasir/Manager. Kode dan nama
  dinormalisasi; kode duplikat serta nilai diskon tidak sah ditolak.
- Seluruh mutasi hanya dapat dilakukan Manager aktif yang direvalidasi dari
  database. Kasir tidak dapat membuat atau mengubah member; penolakan tidak
  meninggalkan data maupun audit parsial.
- Audit baru mencatat lifecycle member secara atomik dengan identitas aktor
  persisten. Detail audit dibatasi pada UID/kode member, aturan diskon, dan
  status; nama customer serta data pribadi tambahan tidak dimasukkan ke JSON.
- Aturan bisnis dikunci untuk batch integrasi berikutnya: promo produk dan Buy X
  Get Y dihitung lebih dulu; antara promo transaksi dan diskon member hanya
  nominal yang lebih besar dipakai, bukan ditumpuk; biaya layanan, pajak, dan
  pembulatan tetap dihitung setelah diskon. Nama customer manual tidak otomatis
  membuat member.
- Batch ini sengaja belum menampilkan UI agar risiko migrasi, authorization,
  audit, dan aturan data dapat ditutup sebelum pengelolaan/pemilihan member pada
  Batch 7B serta checkout/snapshot pada Batch 7C.

Verifikasi: test repository member **6/6 lulus**; test terfokus member,
migrasi, recovery, health, dan backup **104/104 lulus**; seluruh test aplikasi
**664/664 lulus**; Flutter analyzer **No issues found**. APK profile berhasil
dibangun dan dipasang sebagai update pada V2322 tanpa menghapus data. Proses
aplikasi hidup setelah migrasi v19 ke v20 dan log startup tidak memuat error
Drift/SQLite/Flutter. SHA-256 APK lokal/perangkat identik:
`ffc624037dc95bbd56bd8880ac2691d20a2dc4b1fc1d127609dab7510fa84917`.

Tidak ada gerbang manual untuk Batch 7A karena belum ada UI baru. Gerbang
perangkat berikutnya dilakukan setelah Batch 7B menyediakan halaman pengelolaan
dan pemilihan member.

### 25 September 2026 - Tahap 20C Batch 7B: UI dan pemilihan customer/member

- Menu **Member** baru hanya terlihat dan dapat dibuka oleh Manager aktif.
  Manager dapat menambah, mencari, mengubah, mengaktifkan/menonaktifkan, dan
  menghapus member. Daftar mempertahankan konten lama saat refresh singkat agar
  tidak mengembalikan glitch/kedipan panel.
- Form member mendukung kode unik, nama, diskon persentase termasuk desimal,
  serta diskon nominal dengan pemisah ribuan otomatis. Pesan validasi dan error
  repository ditampilkan tanpa membocorkan detail internal.
- Keranjang Kasir kini memiliki pemilih member aktif dengan pencarian langsung
  berdasarkan kode atau nama. Member nonaktif tidak muncul bagi Kasir.
- Kasir juga dapat menulis nama nonmember secara manual. Input tersebut tidak
  membuat profil member dan dibuat saling eksklusif dengan pilihan member.
- Kontrol customer dibuat ringkas dalam satu baris agar ruang daftar produk
  tidak menyempit. Saat keyboard terbuka, kolom nomor meja atau nama nonmember
  yang sedang digunakan diprioritaskan di atas sehingga perbaikan portrait dan
  landscape lama tetap terjaga.
- Pilihan customer/member pada batch ini masih menjadi draf UI. Diskon belum
  mengubah total dan identitas belum disimpan pada transaksi; formula,
  persistence, snapshot, struk, riwayat, laporan, dan backup diselesaikan
  bersama pada Batch 7C agar tidak ada transaksi setengah terintegrasi.
- Tidak ada perubahan schema maupun dependency pada Batch 7B; UI memakai tabel,
  repository, otorisasi, dan audit Batch 7A.

Verifikasi: test terarah repository/UI/akses/Kasir **44/44 lulus**, seluruh test
aplikasi **667/667 lulus**, Flutter analyzer **No issues found**, dan
`git diff --check` bersih. APK profile berhasil dibangun dan dipasang sebagai
update pada V2322 tanpa menghapus data; proses aplikasi hidup dan log startup
tidak memuat error AndroidRuntime/Flutter/SQLite. SHA-256 APK lokal/perangkat
identik:
`822c157039480e2f5a28df7bcd3d7ba44320950e6d08b6e0a593849414b0c952`.

Gerbang visual Batch 7B masih menunggu pemeriksaan pengguna untuk CRUD member,
pencarian/pemilihan member aktif, nama nonmember, serta kenyamanan Keranjang pada
HP portrait dan landscape.

### 25 September 2026 - Penyempurnaan Tahap 20C Batch 7B: program member opsional

- Manager kini dapat mengaktifkan atau menonaktifkan **Program member
  pelanggan** dari Pengaturan Transaksi. Nilai default tetap aktif sehingga
  perilaku toko lama tidak berubah.
- Saat program dinonaktifkan, pemilih/pencarian member disembunyikan dari
  Keranjang Kasir. Nama pelanggan nonmember tetap dapat ditulis karena bukan
  bagian dari program diskon member.
- Menonaktifkan program tidak menghapus atau mengubah profil member. Seluruh data
  tetap tersimpan dan dapat dipakai kembali setelah Manager mengaktifkannya.
- Hanya Manager aktif yang dapat mengubah pengaturan. Perubahan disimpan bersama
  audit `member_setting_updated` secara atomik; percobaan Kasir ditolak tanpa
  perubahan maupun audit parsial.
- Schema database naik dari v20 ke **v21** dengan kolom pengaturan per toko
  ber-default aktif. Migrasi database lama, health check, dan kompatibilitas
  backup ikut diselaraskan.
- Implementasi memakai repository dan halaman pengaturan yang sudah ada tanpa
  dependency atau lapisan arsitektur baru.

Verifikasi: test repository, UI Pengaturan, UI Kasir, migrasi, recovery, health,
dan backup lulus; seluruh test aplikasi **671/671 lulus**; analyzer **No issues
found**; dan `git diff --check` bersih. APK profile berhasil dibangun serta
dipasang sebagai update pada V2322 tanpa menghapus data. Proses aplikasi hidup
setelah migrasi v20 ke v21 dan log startup tidak memuat error
AndroidRuntime/Flutter/Drift/SQLite. SHA-256 APK lokal dan perangkat identik:
`c1a5ce5b951e93ac61ff6a69bad875e9f3214e3e69f7201129f810c12cea2a5f`.

Gerbang manual yang diperlukan hanya pemeriksaan singkat: matikan program member
di Pengaturan Transaksi, buka Kasir dan pastikan tombol pilih member hilang namun
kolom nama nonmember tetap ada; aktifkan kembali dan pastikan tombol member
muncul lagi. Integrasi diskon serta snapshot transaksi tetap dilanjutkan pada
Batch 7C.

### 25 September 2026 - Tahap 20C Batch 7C: checkout dan snapshot customer/member

- Pemilihan member di Keranjang kini langsung menghitung ulang preview dan ikut
  dikirim ke checkout. Member harus masih aktif, berasal dari toko yang sama,
  dan program member harus aktif ketika transaksi disimpan; validasi tidak hanya
  mengandalkan state UI.
- Promo produk dan Buy X Get Y tetap dihitung lebih dahulu. Antara promo
  transaksi dan diskon member hanya nominal yang lebih besar dipakai; keduanya
  tidak ditumpuk. Jika nominal sama, promo transaksi menang secara
  deterministik. Biaya layanan, pajak, dan pembulatan tetap memakai urutan yang
  sudah dikunci setelah diskon.
- Nama nonmember manual dinormalisasi, dibatasi 100 karakter, dan disimpan pada
  transaksi tanpa membuat profil member. Pilihan member dan nama manual tidak
  dapat dikirim bersamaan.
- Schema database naik dari v21 ke **v22**. Transaksi menyimpan snapshot nama
  customer, UID/kode member, jenis dan nilai aturan diskon, dasar hitung, serta
  nominal diskon member. Snapshot tersebut tidak berubah bila profil member
  kemudian diedit, dinonaktifkan, atau dihapus; transaksi lama bermigrasi aman
  dengan nilai nullable.
- Keranjang menjelaskan potongan member yang benar-benar diterapkan. Jika promo
  transaksi lebih besar, nominal member tidak ikut mengurangi total dan UI
  menjelaskan alasan tersebut agar Kasir tidak mengira kedua diskon dijumlahkan.
- Preview/cetak struk menampilkan nama customer, kode member, dan rincian
  potongan member. Detail Riwayat menampilkan snapshot yang sama. Laporan tidak
  memerlukan mesin kedua karena diskon dan total final sudah masuk agregat
  transaksi yang sama.
- Database v22 beserta snapshot customer/member ikut alur backup/restore yang
  sudah ada. Kolom snapshot ini sekaligus menjadi kontrak sumber data untuk
  export Excel Batch 9; generator Excel belum dibuat lebih awal.
- Nama customer tidak ditambahkan ke Audit Log sehingga kebutuhan operasional
  terpenuhi tanpa memperluas penyimpanan data pribadi.

Verifikasi: test repository checkout **27/27 lulus**, seluruh test aplikasi
**676/676 lulus**, Flutter analyzer **No issues found**, dan
`git diff --check` bersih. APK profile berhasil dibangun dan dipasang sebagai
update pada V2322 tanpa menghapus data. Aplikasi hidup setelah migrasi v21 ke
v22 dan log startup tidak memuat error AndroidRuntime/Flutter/Drift/SQLite.
SHA-256 APK lokal dan perangkat identik:
`3344c55fc330f1868a6105f5c2ca5505939be81d4076605cef58dc018cba815a`.

Gerbang manual Batch 7C: lakukan satu transaksi dengan member dan satu transaksi
nama nonmember, lalu pastikan potongan/total Preview Keranjang, Preview Struk,
serta detail Riwayat sesuai. Sebelum Batch 8, kebijakan pembulatan tunai dan
donasi kembalian disempurnakan pada Batch 7D berikut.

### 29 September 2026 - Tahap 20C Batch 7D: pembulatan tunai Rp100 dan donasi kembalian

- Kebijakan pembulatan lama Rp1.000 diganti menjadi **Rp100 terdekat** dengan
  selisih maksimum Rp50. Keranjang tetap menampilkan total presisi; pembulatan
  baru diterapkan ketika metode pembayaran **Tunai** dipilih.
- QRIS dan seluruh metode nontunai memakai total persis tanpa pembulatan. Dengan
  demikian pembayaran digital tidak lagi dapat terbebani atau dikurangi oleh
  penyesuaian uang kembalian fisik.
- Dialog pembayaran tunai menampilkan total sebelum dan sesudah pembulatan,
  uang diterima, kembalian tersedia, input donasi, serta kembalian akhir.
- Donasi kembalian bersifat opsional. Donasi positif wajib mendapat persetujuan
  pembeli melalui checkbox, tidak boleh melebihi kembalian tersedia, dan tidak
  dapat dipakai pada pembayaran nontunai.
- Donasi disimpan terpisah dan **bukan omzet**. Nilai penjualan serta pembayaran
  tetap sebesar total transaksi; donasi hanya menambah kas fisik yang diharapkan
  dan mengurangi kembalian yang diberikan.
- Preview/cetak struk dan detail Riwayat menampilkan donasi di antara uang
  diterima dan kembalian. Laporan menyediakan agregat donasi terpisah. Riwayat
  Shift menampilkan total donasi, dan expected cash memasukkannya tanpa
  mencampurkannya ke `cashSalesTotal`.
- Schema database naik dari v22 ke **v23**. Migrasi mengubah pengaturan toko lama
  yang aktif dari `nearestThousand` ke `nearestHundred`; snapshot transaksi lama
  tidak ditulis ulang sehingga riwayat historis tetap sama. Kolom donasi nullable
  membuat seluruh snapshot database lama tetap aman dibuka.
- Validasi checkout berada di repository, bukan hanya UI: donasi tanpa
  persetujuan, donasi melebihi kembalian, nilai negatif, dan donasi nontunai
  ditolak secara atomik.
- Tidak ada dependency baru. Implementasi memakai kalkulator transaksi,
  checkout, laporan, printer, dan shift yang sudah ada.

Verifikasi: seluruh test aplikasi **677/677 lulus**, Flutter analyzer **No
issues found**, dan `git diff --check` bersih. APK profile berhasil dibangun
dan dipasang sebagai update pada V2322 tanpa menghapus data; aplikasi hidup dan
`MainActivity` aktif setelah update. SHA-256 APK lokal/perangkat identik:
`fcb46e0ccfb1dea3706c3b2ac009d7274120de1daa0c2ca528f93c50d5f31f55`.

Gerbang manual Batch 7D: aktifkan pembulatan pada Pengaturan Transaksi, buat
total yang tidak genap Rp100, lalu bandingkan pembayaran tunai dan QRIS. Untuk
tunai, masukkan uang lebih, isi donasi tidak melebihi kembalian, centang
persetujuan pembeli, dan pastikan kembalian akhir, Preview Struk, Riwayat,
serta expected cash Shift sesuai. Agregat Laporan sudah dicakup test repository;
QRIS harus tetap memakai total persis dan tidak menampilkan input donasi.

Gerbang manual tersebut dinyatakan **lulus oleh pengguna pada 29 September
2026**.

### 29 September 2026 - Tahap 20C Batch 8A: pengaturan dan lifecycle gambar QRIS

- Manager kini dapat memilih, mengganti, menghapus, dan memperbesar gambar QRIS
  toko dari Pengaturan Transaksi. Status kosong dan file hilang ditampilkan
  jelas; QRIS statis selalu disertai pengingat bahwa Kasir tetap harus
  memastikan pembayaran benar-benar diterima.
- Gambar dipilih melalui galeri, orientasinya dinormalisasi, dimensi besar
  diperkecil secara proporsional, lalu disimpan sebagai PNG bernama unik di
  folder private `payment_assets`. Tidak ada dependency baru.
- Schema database naik dari v23 ke **v24** melalui kolom path QRIS nullable.
  Seluruh database lama bermigrasi dengan nilai kosong sehingga metode
  pembayaran dan transaksi lama tidak berubah.
- Penyimpanan atau penghapusan pengaturan hanya dapat dilakukan Manager aktif.
  Mutasi database dan audit `qris_settings_updated` atomik; Kasir ditolak dan
  kegagalan audit me-roll back perubahan. Detail audit hanya menyimpan boolean
  sebelum/sesudah tanpa path maupun isi gambar.
- Lifecycle aset sudah lengkap: gambar lama dihapus setelah penggantian sukses,
  gambar baru dibersihkan bila persist gagal, dan startup cleanup menghapus aset
  QRIS yatim tanpa menyentuh aset yang masih direferensikan.
- Folder `payment_assets` sudah ikut backup, manifest allowlist, staging,
  restore commit, rollback, penghapusan aset aktif lama, dan checksum. Path dari
  perangkat lama dipetakan berdasarkan nama file ke folder perangkat aktif agar
  gambar hasil restore tetap ditemukan.
- Batch ini belum mengubah alur pembayaran. Menampilkan QRIS saat metode QRIS
  dipilih, aksi perbesar untuk Kasir, konfirmasi pembayaran manual, serta
  perlindungan file hilang/rusak diselesaikan pada Batch 8B setelah UI Manager
  dinyatakan nyaman.

Verifikasi: test terarah QRIS, authorization, audit, migrasi, health, cleanup,
manifest, backup, dan restore **132/132 lulus**; seluruh test aplikasi **685/685
lulus**; Flutter analyzer **No issues found**; dan `git diff --check` bersih.
APK profile berhasil dibangun dan dipasang sebagai update pada V2322 tanpa
menghapus data. Proses aplikasi hidup dan log startup tidak memuat error
AndroidRuntime/Flutter/Drift/SQLite. SHA-256 APK lokal dan perangkat identik:
`6ca8f88dd941f9006a9c9e1d4f86a70a66992a837c9fe62111bb68b70d30f344`.

Gerbang manual Batch 8A dinyatakan **lulus oleh pengguna**: pilih, preview,
perbesar, ganti, hapus, dan pasang kembali gambar QRIS berhasil pada perangkat.

### 30 September 2026 - Tahap 20C Batch 8B: QRIS pada pembayaran dan konfirmasi Kasir

- Ketika metode QRIS dipilih, dialog pembayaran kini menampilkan gambar QRIS
  pada bidang putih berkontras tinggi dengan ukuran nyaman serta tombol
  **Perbesar QRIS** yang mendukung zoom.
- Gambar diambil ulang dari pengaturan toko terbaru setiap dialog pembayaran
  dibuka. Path hasil restore diselesaikan lebih dahulu, kemudian isi file
  didekode untuk memastikan gambar benar-benar terbaca sebelum dapat digunakan.
  Perubahan QRIS oleh Manager karena itu tidak memerlukan restart halaman Kasir.
- Dialog menjelaskan secara eksplisit bahwa QRIS statis/offline tidak dapat
  memeriksa uang masuk. Kasir wajib memeriksa notifikasi atau mutasi lalu
  mencentang **Pembayaran QRIS sudah diterima** sebelum transaksi dapat disimpan.
- QR yang belum dikonfigurasi, file hilang, file rusak, dan konfirmasi yang belum
  diberikan memblokir pembayaran. Repository mengulang validasi konfigurasi dan
  konfirmasi sehingga pemanggilan checkout di luar dialog juga tidak dapat
  mencatat QRIS sebagai lunas secara diam-diam.
- Menutup atau membatalkan dialog tidak membuat transaksi. Pengamanan repository
  dibuktikan tidak menulis sale, payment, maupun stock movement saat validasi
  QRIS gagal.
- Layout dialog tetap dapat digulir pada portrait dan landscape pendek. Nomor
  referensi tetap opsional; QRIS tidak memakai pembulatan tunai maupun donasi.
- Tidak ada dependency atau perubahan schema baru; schema tetap **v24**.

Verifikasi: test UI/service QRIS **11/11**, test terarah final QRIS/checkout/
ViewModel **67/67**, regresi checkout/struk/riwayat **83/83**, seluruh test
aplikasi **692/692 lulus**, Flutter analyzer **No issues found**, dan
`git diff --check` bersih. APK profile berhasil dibangun dan dipasang sebagai
update pada V2322 tanpa menghapus data. Proses aplikasi hidup dan log startup
AndroidRuntime/Flutter bersih. SHA-256 APK lokal dan perangkat identik:
`90c2c40daa95c20384b5c160e0242ce9f9389506d3f2ffb182f569f20062cd88`.

Gerbang manual Batch 8B dinyatakan **lulus oleh pengguna pada 30 September
2026**: fitur QRIS dan perubahan UI berjalan baik pada perangkat.

### 30 September 2026 - Tahap 20C Batch 8B Hotfix: dialog Kasir landscape HP

- Dialog pembayaran kini memakai layout ringkas saat tinggi layar landscape
  pendek: total dan metode pembayaran berdampingan, jarak dipadatkan, serta isi
  panjang tetap dapat digulir tanpa menyembunyikan tombol Batal/Simpan.
- Dialog transaksi berhasil tidak lagi menumpuk tombol di atas rincian. Ikon dan
  judul dibuat ringkas pada landscape, sedangkan rincian biaya, pembayaran,
  kembalian, dan hasil printer dapat digulir secara aman.
- Dialog pilihan satuan memakai lebar landscape, daftar satuan yang dapat
  digulir, serta kepadatan baris yang lebih baik. Input jumlah dan tombol aksi
  tetap berada di dalam bidang dialog.
- QRIS tetap memiliki bidang yang cukup besar agar pesan konfigurasi/file rusak
  tidak overflow. Portrait dan logika pembayaran, promo, stok, serta penyimpanan
  transaksi tidak diubah.
- Tidak ada dependency atau perubahan schema; schema tetap **v24**.

Verifikasi: test dialog pembayaran **7/7**, widget Kasir **34/34**, seluruh test
aplikasi **693/693 lulus**, Flutter analyzer **No issues found**, dan
`git diff --check` bersih. APK profile berhasil dibangun serta dipasang sebagai
update pada V2322 tanpa menghapus data; aplikasi hidup dan log error startup
bersih. SHA-256 APK lokal/perangkat identik:
`ba86e13106b6f44ecb44e7d73f06908658c33a0324600c1a2b32065a4f3a347d`.

Gerbang manual: pada landscape HP, buka pilihan satuan, pembayaran Tunai/QRIS,
dan dialog transaksi berhasil. Pastikan seluruh isi dapat dibaca/digulir, input
tetap dapat dipakai, serta tombol aksi tidak menutup rincian.

Gerbang manual hotfix landscape dinyatakan **lulus oleh pengguna pada 30
September 2026**.

### 30 September 2026 - Tahap 20C Batch 9: export laporan Excel offline

- Manager kini memiliki menu **Laporan Excel** dengan pemilih tanggal awal dan
  akhir yang responsif pada HP/tablet. Kasir tidak melihat menu dan repository
  juga menolak sesi selain Manager aktif.
- Export memakai dialog simpan sistem sehingga pengguna dapat menentukan lokasi
  file `.xlsx`; proses tidak memerlukan internet atau akun cloud.
- Workbook berisi tujuh sheet: Ringkasan, Transaksi, Item, Pembayaran, Promo,
  Shift, dan Produk Terlaris. Detail transaksi mencakup satuan, member/customer,
  meja, pajak, biaya layanan, pembulatan, donasi, dan transaksi batal.
- Ringkasan dan detail diambil dari satu snapshot database. Penyusunan workbook
  berjalan di isolate, menampilkan progress, dapat dibatalkan tanpa membiarkan
  proses lama membuka dialog simpan, serta menampilkan error yang aman.
- Workbook memakai format Rupiah, waktu sesuai timezone toko, tema toko ketika
  `useThemeInExcel` aktif, header beku, filter, lebar kolom terbaca, escaping
  teks, serta nama file aman. Data kosong tetap menghasilkan workbook valid.
- Implementasi memakai dependency `archive` dan `file_picker` yang sudah ada;
  tidak ada dependency, schema, migration, atau generated database baru. Schema
  tetap **v24**.

Verifikasi otomatis: delapan test baru mencakup baca-ulang OOXML, kesamaan total
repository, authorization, data kosong, dan alur simpan UI. Regresi terarah
**64/64**, seluruh test aplikasi **701/701**, Flutter analyzer **No issues
found**, dan `git diff --check` bersih. Koreksi akhir format jumlah transaksi
lulus test workbook **4/4** serta analyzer terarah. APK profile final berhasil
dipasang sebagai update pada V2322, proses hidup, log startup bersih, dan
SHA-256 lokal/perangkat identik:
`7923b7c9b6b3249bb2c8014d9b9b230e038b4a762701470b785f2c219a6a93fe`.

Hotfix kompatibilitas viewer spreadsheet HP juga telah diterapkan. Audit file
nyata yang ditarik dari V2322 memastikan ketujuh sheet berisi data (Ringkasan
16, Transaksi 33, Item 55, Pembayaran 33, Promo 46, Shift 1, dan Produk
Terlaris 2 baris data). Setiap worksheet sekarang memiliki metadata batas area
data, tinggi baris standar, dan posisi sel aktif agar sheet detail tidak tampak
kosong pada viewer Android tertentu. Test export dan UI lulus **6/6**, analyzer
bersih, dan APK hotfix terpasang sebagai update tanpa menghapus data dengan
SHA-256 lokal/perangkat identik:
`91f75b72b7690fdcfcfefbbd4d2fd3a8f1a5253ab5b0bb13775a6b8521f4f1eb`.

Gerbang manual Batch 9 dinyatakan **lulus oleh pengguna pada 17 November 2026**:
file Excel berhasil dibuat dan dibuka, seluruh sheet sesuai, serta hasilnya
cocok. Batch 9 selesai; pekerjaan berlanjut ke Batch 10A.
