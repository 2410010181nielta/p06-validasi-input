# Tugas 6 - Validasi Input dan Kegunaan

Proyek `p06-validasi-input` berisi kelas validasi dan dua JFrame Form dengan tema FlatLaf 3.5.4:

| Berkas | Keterangan |
|--------|------------|
| `Validator` | Aturan validasi tanpa GUI (NPM, nama, email, nomor HP) |
| `UjiValidator` | Pengujian aturan `Validator` dengan contoh masukan (Praktikum 2) |
| `FormAnggota` | Form anggota dengan validasi, sesuai Praktikum 3 dan 4 |
| `FormAnggotaPerpustakaan` | Form anggota dari Pertemuan 5 yang dikembangkan sesuai latihan mandiri |

Setiap form memiliki berkas `.java` dan `.form` di `src/main/java/id/ac/uniska/pbo2/p06/`.

## Cara Menjalankan

Buka proyek di Apache NetBeans (JDK 21), lalu klik kanan file dan pilih **Run File** (Shift+F6).
Dengan Maven di terminal:

```bash
mvn compile exec:java -Dexec.mainClass=id.ac.uniska.pbo2.p06.FormAnggotaPerpustakaan
mvn compile exec:java -Dexec.mainClass=id.ac.uniska.pbo2.p06.UjiValidator
```

## Keluaran `UjiValidator`

```
SESUAI NPM kosong: NPM wajib diisi
SESUAI NPM 5 digit: NPM harus berisi 10 digit angka
SESUAI NPM berisi huruf: NPM harus berisi 10 digit angka
SESUAI NPM benar: valid
SESUAI Nama terlalu pendek: Nama minimal 3 huruf
SESUAI Nama berisi angka: Nama tidak boleh berisi angka
SESUAI Nama benar: valid
SESUAI Email tanpa domain: Format email belum benar, contoh: nama@contoh.com
SESUAI Email benar: valid
SESUAI Nomor HP terlalu pendek: Nomor HP diawali 08 dan berisi 10 sampai 13 digit
SESUAI Nomor HP diawali 62: Nomor HP diawali 08 dan berisi 10 sampai 13 digit
SESUAI Nomor HP 12 digit: valid

Semua aturan bekerja sesuai harapan
```

Baris "Nama berisi angka" adalah aturan baru dari latihan mandiri.

## Penerapan Ketentuan Latihan Mandiri (`FormAnggotaPerpustakaan`)

| No | Ketentuan | Penerapan | Heuristik |
|----|-----------|-----------|-----------|
| 1 | Aturan validasi di kelas `Validator` | `periksaNpm()`, `periksaNama()`, `periksaEmail()`, `periksaHp()` memanggil `Validator` | Konsistensi |
| 2 | Nama tidak boleh berisi angka | Aturan baru di `Validator.nama` dan diuji di `UjiValidator` | Pencegahan kesalahan |
| 3 | Garis merah dan pesan di label bawah isian | Method `tandai()` mengatur `JComponent.outline` dan teks label pesan | Membantu memperbaiki kesalahan |
| 4 | NPM dan HP hanya menerima angka lewat `keyTyped` | `npmFieldKeyTyped` dan `hpFieldKeyTyped` memanggil `hanyaAngka()` yang memakai `evt.consume()` | Pencegahan kesalahan |
| 5 | Ringkasan jumlah kesalahan dan fokus ke kesalahan pertama | `daftar()` menampilkan "n isian perlu diperbaiki" di `statusLabel` dan memanggil `requestFocusInWindow()` | Visibilitas status sistem |
| 6 | Konfirmasi sebelum mengosongkan form | `batal()` menampilkan `showConfirmDialog` jika `adaIsian()` | Kendali dan kebebasan pengguna |
| 7 | `labelFor`, `displayedMnemonic`, `mnemonic`, `toolTipText` | Diatur di jendela Properties: P, N, R, J, E, H, L untuk label; B, T, M, D, U untuk tombol dan pilihan; tooltip pada NPM, nama, dan HP | Fleksibilitas dan bantuan |

Event dari Pertemuan 5 tetap ada: Daftar aktif setelah persetujuan dicentang, jumlah digit NPM
("8 dari 10 digit"), dan konfirmasi saat jendela ditutup.

Keterbatasan yang diketahui: menempel teks dengan Ctrl+V tidak melewati `keyTyped`, jadi karakter bukan angka
dapat masuk ke NPM atau HP lewat tempel. Isian tetap diperiksa saat pindah isian dan saat Daftar diklik.

## Tangkapan Layar

Form dengan pesan kesalahan:

![Form dengan pesan kesalahan](screenshots/form-pesan-kesalahan.png)

Jendela Navigator yang memperlihatkan label pesan:

![Navigator label pesan](screenshots/navigator-label-pesan.png)

## Peer Review

Lembar peer review yang saya terima dari teman:

![Lembar peer review](screenshots/peer-review.jpg)

Peninjau: _(nama teman)_

| No | Temuan dari teman (heuristik dan skor) | Perbaikan yang saya lakukan | Commit |
|----|----------------------------------------|-----------------------------|--------|
| 1 |  |  |  |
| 2 | |  |  |
| 3 |  |  | |

Temuan dengan skor 3 dan 4 wajib diperbaiki dan dapat ditelusuri ke commit.

## Jawaban Eksperimen Praktikum 4

1. **Mengetik huruf pada isian NPM.** Huruf tidak muncul di isian. Pada `npmFieldKeyTyped`, kondisi
   `!Character.isDigit(evt.getKeyChar())` bernilai benar sehingga `evt.consume()` dijalankan dan karakter
   tidak dimasukkan. Angka baru ke-11 juga ditolak karena `penuh` bernilai benar.

2. **Email `siti@` lalu Tab.** Pesan "Format email belum benar, contoh: nama@contoh.com" muncul **segera
   saat fokus meninggalkan isian**, yaitu saat event `focusLost` berjalan. Isian ikut diberi garis merah.

3. **Klik isian Nama tanpa mengetik, lalu Tab.** Tidak ada pesan karena `namaFieldFocusLost` hanya memeriksa
   jika isian tidak kosong (`!isBlank()`). Isian yang hanya dilewati tidak langsung dianggap salah,
   sehingga pengguna tidak dimarahi sebelum sempat mengisi. Isian kosong baru diperiksa saat Daftar diklik.

4. **Centang persetujuan, lalu klik Daftar dengan form kosong.** Muncul "5 isian perlu diperbaiki" di
   `statusLabel` dan kelima isian (NPM, Nama, Program studi, Email, Nomor HP) bergaris merah dengan pesan masing-masing.
   Fokus diarahkan ke **isian NPM**, kesalahan pertama pada daftar `salah`, melalui `requestFocusInWindow()`.

5. **Alt+N.** Fokus pindah ke isian **Nama lengkap**. Properti `displayedMnemonic` (N) pada `namaLabel` menentukan
   tombol pintasnya, dan properti `labelFor` menghubungkannya ke `namaField`.

6. **Klik Batal, lalu pilih No.** Data **tetap ada**. Pada `batal()`, jawaban selain `YES_OPTION` memicu
   `return` sebelum `kosongkan()` dipanggil.
   

## Jawaban Pertanyaan Refleksi

**1. Mengapa aturan validasi dipisahkan ke kelas `Validator`?**
Aturan yang terpisah dapat dipakai oleh banyak event (focusLost dan Daftar) tanpa menyalin kode, mudah
diubah di satu tempat, dan dapat diuji tanpa membuka jendela, seperti yang dilakukan `UjiValidator`
dan nanti dengan JUnit. Event handler cukup mengurus tampilan.

**2. Mengapa isian kosong tidak langsung ditandai merah saat hanya dilewati?**
Pengguna mungkin belum sempat mengisi atau hanya berpindah isian. Pesan merah pada isian yang belum disentuh
terasa seperti teguran dan mengganggu. Karena itu isian kosong baru diperiksa saat Daftar diklik, sedangkan
isian yang sudah diisi segera diperiksa saat fokus berpindah.

**3. Tiga heuristik Nielsen yang diterapkan beserta buktinya.**
- *Pencegahan kesalahan*: NPM dan HP hanya menerima angka lewat `evt.consume()`, dan tombol Daftar nonaktif sampai persetujuan dicentang.
- *Membantu memperbaiki kesalahan*: pesan menyebut isian dan caranya, misalnya "Format email belum benar, contoh: nama@contoh.com", tampil tepat di bawah isian.
- *Kendali dan kebebasan pengguna*: tombol Batal meminta konfirmasi sebelum menghapus data, dan penutupan jendela juga meminta konfirmasi.

**4. Mengapa pesan kesalahan tidak boleh hanya berupa warna merah?**
Sebagian pengguna buta warna sehingga tidak dapat membedakan merah dari warna lain, dan pembaca layar
tidak membacakan warna. Karena itu setiap kesalahan juga disertai teks yang jelas dan label pesan di bawah isian.

**5. Temuan peer review yang paling bermanfaat.**


## Deklarasi Penggunaan AI

saya menggunakan claude ai untuk membantu menyelesaikan tugas karna saya kurang paham jika tidak ada bantuan 
