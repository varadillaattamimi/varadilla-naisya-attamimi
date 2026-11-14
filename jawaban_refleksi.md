# Jawaban Refleksi — Latihan Blockchain

**Nama:**
**NIM:**
**Kelas:**

> Salin output `python cek_nilai.py` Anda di bawah ini:

```
==============================================================
Level Materi                           Lulus      Status
--------------------------------------------------------------
1     Fungsi hash SHA-256                7/7     SELESAI
2     Blockchain dasar (GfG)             8/8     SELESAI
3     Eksperimen tampering               9/9     SELESAI
4     Transaksi dalam blok             13/13     SELESAI
5     PoW dengan nonce                 10/10     SELESAI
6     Eksperimen difficulty            13/13     SELESAI
7     Tanda tangan digital             10/10     SELESAI
8     Jaringan & konsensus               7/7     SELESAI
9     Reward & saldo                   13/13     SELESAI
--------------------------------------------------------------
TOTAL                                  90/90        100%
============================================================== 
```

---

## Level 1 — Fungsi Hash
1.Hash disebut sidik jari digital karena menghasilkan nilai unik yang mewakili suatu data. Tiga sifat fungsi hash kriptografis adalah deterministik (input sama menghasilkan hash sama), memiliki efek avalanche (perubahan kecil pada input menghasilkan perubahan besar pada hash), dan sulit menemukan data asli hanya dari hash. Dalam percobaan saya, hash nama menghasilkan 8fc3e6a3....
2.Ketika satu huruf pada teks diubah, hasil hash SHA-256 dapat berubah secara signifikan. Pada percobaan data tanggal lahir saya, perubahan satu hari menghasilkan perubahan sebesar 51,17% bit. Hal ini menunjukkan sifat avalanche effect. Untuk tiga pasangan teks berbeda, persentase perubahan dapat berbeda-beda karena hasil hash bergantung pada keseluruhan input.
3.Jika hash_dict tidak menggunakan sort_keys=True, urutan key dapat memengaruhi hasil serialisasi dan menghasilkan hash yang berbeda meskipun isi datanya sama. Hal ini berbahaya untuk blockchain karena data yang sebenarnya sama dapat memiliki hash berbeda sehingga proses validasi menjadi tidak konsisten.

## Level 2 — Blockchain Dasar
1.previous_hash pada blok genesis digunakan sebagai penanda bahwa blok tersebut merupakan blok pertama dalam blockchain. Nilainya menggunakan "0" karena belum ada blok sebelumnya yang menjadi acuan. Pada blockchain saya, proof genesis menggunakan angka 2012 yang berasal dari empat digit terakhir NIM.
2.Hasil proof blok saya adalah 2222, 47312, dan 26086. Nilai proof merupakan hasil pencarian angka yang memenuhi ketentuan Proof of Work. Semakin tinggi difficulty, semakin sulit menemukan proof yang memenuhi target sehingga proses mining membutuhkan lebih banyak percobaan.

## Level 3 — Tampering
1.Ketika data pada blok posisi k diubah, hash blok tersebut ikut berubah. Blok berikutnya masih menyimpan previous_hash yang lama sehingga tautannya tidak cocok. Karena itu, cari_tautan_putus melaporkan posisi k+1. Pada percobaan saya, perubahan di posisi 1 terdeteksi pada indeks 2.
2.Perubahan pada blok terakhir tidak terdeteksi oleh cari_tautan_putus karena tidak ada blok berikutnya yang menyimpan hash blok terakhir sebagai previous_hash. Meskipun demikian, validasi hash blok terakhir tetap dapat mendeteksi perubahan apabila hash yang diharapkan diperiksa.
3.Setelah sambung_ulang, rantai palsu dapat dianggap valid karena desain PoW pada artikel GfG belum mengikat data blok dengan mekanisme Proof of Work yang harus dihitung ulang secara kuat. Akibatnya, tautan hash dapat dibuat kembali tanpa proses mining yang benar-benar melindungi rantai.

## Level 4 — Transaksi
1.Transaksi tidak langsung masuk ke blok karena transaksi perlu menunggu di pending_transactions sebelum dimasukkan ketika blok baru dibuat. Dengan cara ini, beberapa transaksi dapat dikumpulkan dan dicatat bersama dalam satu blok.
2.create_block harus menggunakan salinan list(self.pending_transactions) agar daftar transaksi yang dimasukkan ke blok tidak berubah ketika daftar transaksi pending dibersihkan. Jika tidak menggunakan salinan, transaksi yang sudah dimasukkan ke blok berisiko ikut terhapus atau mengalami perubahan ketika daftar pending diperbarui.

## Level 5 — PoW dengan Nonce
1.Pada Level 3, rantai palsu masih dapat dianggap valid setelah tautan disambungkan ulang. Namun, pada Level 5, serangan tersebut menjadi lebih sulit karena hash blok mencakup data blok dan nonce. Jika data diubah, hash berubah sehingga Proof of Work harus dihitung ulang untuk blok tersebut dan blok berikutnya.
2.Pada percobaan saya, penyerang membutuhkan 294.702 percobaan hash untuk menambang ulang blok 1 sampai 3 setelah pemalsuan. Jika rantai berisi 1.000 blok dengan difficulty 6, proses penambangan ulang akan membutuhkan usaha komputasi yang jauh lebih besar karena setiap blok yang terpengaruh harus dihitung kembali.

## Level 6 — Difficulty
1. Hasil percobaan mining saya adalah:

Difficulty  | Percobaan | Teori (16^d)
-------------|-----------|-------------
1            | 16        | 16
2            | 590       | 256
3            | 3.290     | 4.096
4            | 71.861    | 65.536
Hasil percobaan tidak selalu sama dengan nilai teori karena nilai teori merupakan perkiraan rata-rata matematis. Angka 16 digunakan karena setiap karakter heksadesimal memiliki 16 kemungkinan nilai, yaitu 0 sampai 9 dan a sampai f. Semakin banyak karakter nol yang ditargetkan pada awal hash, semakin besar jumlah percobaan yang diharapkan.
2.Waktu blok perlu dijaga stabil agar proses penambahan blok dan penerbitan koin baru berlangsung secara teratur. Jika waktu blok terlalu cepat, koin baru dapat dihasilkan lebih cepat dari yang direncanakan. Sebaliknya, jika terlalu lambat, transaksi membutuhkan waktu lebih lama untuk dikonfirmasi. Bitcoin menyesuaikan difficulty setiap 2.016 blok agar waktu rata-rata pembentukan blok mendekati 10 menit.

## Level 7 — Tanda Tangan Digital
1.Kunci privat digunakan untuk membuat tanda tangan digital dan harus dijaga kerahasiaannya. Kunci publik digunakan untuk memverifikasi tanda tangan, sedangkan alamat merupakan identitas yang berasal dari informasi kunci publik dan digunakan dalam transaksi.
2.Tanda tangan penipu dapat valid karena dibuat menggunakan kunci privat milik penipu sendiri. Namun, transaksinya tidak sah karena alamat pengirim yang digunakan adalah alamat saya, sedangkan kunci publik penipu menghasilkan alamat yang berbeda. Pada hasil percobaan saya, tanda tangan penipu valid tetapi transaksi tetap ditolak.
3.Jika kunci privat hilang, pemilik dapat kehilangan kemampuan untuk mengakses dan menggunakan dana yang terkait dengan kunci tersebut. Jika kunci privat dicuri, pihak lain dapat menggunakannya untuk membuat tanda tangan transaksi dan memindahkan dana tanpa izin pemilik.

## Level 8 — Jaringan & Konsensus
1.Jaringan saya memiliki 5 node dengan panjang rantai saat terjadi fork sebesar 5, 3, 4, 5, dan 4 blok. Setelah resolve_conflicts, seluruh node memiliki panjang rantai 5 blok, tetapi belum mencapai kesepakatan karena isi dan hash rantainya masih berbeda.
2.Setelah konsensus, transaksi fork-B yang hanya terdapat pada cabang yang kalah tidak menjadi bagian dari rantai utama. Transaksi tersebut dapat perlu dimasukkan kembali ke dalam transaksi pending jika masih valid. Hal ini berkaitan dengan anjuran menunggu 6 konfirmasi di Bitcoin, yaitu untuk mengurangi risiko transaksi dibatalkan akibat terjadinya reorganisasi rantai. Semakin banyak konfirmasi, semakin sulit transaksi dikeluarkan dari rantai utama.
3.Pada hasil tugas personal saya, setelah blok pemecah seri ditambahkan, seluruh node memiliki rantai sepanjang 6 blok dan mencapai kesepakatan. Node VARADILLA-1 tercatat sebagai pemenang fork dan terdapat 8 orphan blocks dari cabang yang kalah. Laporan tidak menjelaskan secara rinci aturan teknis pemilihan VARADILLA-1 ketika terjadi seri.

## Level 9 — Reward & Saldo
1.Koin pertama berasal dari hadiah mining atau transaksi coinbase yang diberikan kepada penambang ketika berhasil menghasilkan blok. Dengan demikian, koin dapat mulai beredar meskipun semua pengguna memulai dengan saldo 0.
2.validasi_ekonomi diperlukan karena chain_valid hanya memeriksa integritas rantai berdasarkan hash, bukan kebenaran jumlah saldo dan hadiah koin. Validasi ekonomi diperlukan untuk memastikan transaksi tidak melebihi saldo, hadiah mining sesuai aturan, dan tidak terjadi penciptaan koin secara tidak sah. Pada percobaan saya, total suplai tercatat sebesar 110 koin dan validasi ekonomi berhasil. Setelah hadiah blok pertama dicurangi, validasi menjadi False.
3.Bitcoin menggunakan model UTXO, yaitu dana tersimpan dalam bentuk output transaksi yang belum digunakan. Berbeda dengan model saldo akun pada latihan saya, model UTXO mencatat setiap output yang dapat digunakan sebagai input transaksi berikutnya. Dengan demikian, transaksi Bitcoin menggunakan output yang belum dibelanjakan untuk membentuk pembayaran baru.
