# **README \- Watermark Tools-nya Waterverri**

## **1\. Pendahuluan**

Watermark Tools-nya Waterverri adalah aplikasi berbasis HTML yang dirancang untuk membantu penulis dan pembuat konten mencegah plagiarisme, khususnya pada platform seperti forum phpBB yang menggunakan BBCode. Alat ini menerapkan berbagai teknik penyamaran pada teks, menyematkan "watermark" tak terlihat dan mengubah struktur teks yang terlihat untuk mempersulit penyalinan otomatis dan penggunaan ulang tanpa izin.

Tujuannya bukanlah untuk membuat konten sepenuhnya anti-pencurian, melainkan untuk secara signifikan meningkatkan upaya yang diperlukan untuk membersihkan dan menerbitkan ulang konten yang dicuri. Skrip ini menerima input BBCode, memproses konten teksnya dengan teknik watermarking, dan menghasilkan output BBCode yang dimodifikasi dengan tetap mempertahankan tag format asli sambil menyertakan penyamaran.

## **2\. Fitur-fitur**

* Input & Output BBCode: Menerima teks yang diformat dengan tag BBCode umum (misalnya, \[b\], \[i\], \[color\]) dan menghasilkan teks yang diberi watermark.  
* Pelestarian Tag Asli: Skrip ini secara cerdas mendeteksi teks yang sudah ada di dalam tag seperti \[b\], \[i\], \[url\], \[color\], atau \[size\] dan tidak menerapkan pewarnaan tambahan pada konten ini, sehingga format asli Anda tetap terjaga.  
* Pratinjau HTML: Menyediakan pratinjau langsung berbasis HTML tentang bagaimana konten teks (tanpa rendering BBCode) terlihat setelah watermark diterapkan.  
* Frasa Tak Terlihat yang Dapat Disesuaikan: Pengguna dapat memasukkan daftar frasa mereka sendiri untuk digunakan sebagai konten watermark tak terlihat yang lebih besar.  
* Penyimpanan LocalStorage: Frasa watermark khusus secara otomatis disimpan di penyimpanan lokal browser Anda dan dimuat kembali pada kunjungan berikutnya.  
* Teknik Penyamaran Tingkat Lanjut:  
  * Pewarnaan Dinamis Tingkat Kalimat: Mengidentifikasi kalimat dalam teks biasa dan membungkus masing-masing kalimat dalam tag \[color\] dengan warna mendekati hitam yang sedikit berbeda, sehingga lebih sulit untuk memilih teks secara berkelanjutan.  
  * Penggantian Karakter Spasi Tak Terlihat: Secara acak mengganti karakter spasi dengan satu karakter alfabet "tak terlihat", yang diberi gaya dengan warna yang sangat cocok dengan latar belakang halaman.  
  * Paragraf Tak Terlihat (untuk Padding & Penyamaran): Menyisipkan blok besar teks tak terlihat (menggunakan frasa khusus Anda atau frasa default) pada beberapa baris baru dan untuk mengisi (padding) baris yang lebih pendek. Paragraf ini menggunakan ukuran font default forum tetapi dibuat tidak terlihat dengan mewarnai teks agar sesuai dengan latar belakang. Konten teks dari paragraf tak terlihat ini *tidak* dipecah-pecah.  
  * Penyetaraan Panjang Baris Rata-rata: Skrip menghitung rata-rata panjang konten teks dari baris input Anda yang tidak kosong. Kemudian, skrip akan mengisi baris yang lebih pendek (termasuk baris kosong) dengan paragraf tak terlihat agar semua baris tampak memiliki kepadatan karakter yang serupa, yang selanjutnya membingungkan bot plagiarisme.

## **3\. Cara Menggunakan**

1. Simpan Kode: Salin seluruh kode HTML, CSS, dan JavaScript ke dalam satu berkas bernama watermark\_tool.html (atau nama .html lainnya).  
2. Buka di Browser: Buka berkas watermark\_tool.html ini di browser web modern.  
3. Masukkan Teks Asli: Tempel teks Anda, termasuk format BBCode apa pun (misalnya, \[b\]teks Anda\[/b\]), ke dalam area teks "Original Text (with BBCode)".  
4. Masukkan Frasa Watermark Khusus (Opsional):  
   Di area teks "Custom Invisible Paragraph/Sentence Phrases (one per line)", Anda dapat memasukkan frasa Anda sendiri. Setiap baris akan diperlakukan sebagai frasa terpisah yang dapat dipilih secara acak untuk paragraf/kalimat tak terlihat.  
   Frasa ini akan disimpan ke penyimpanan lokal browser Anda secara otomatis saat Anda mengetik (dengan sedikit penundaan).  
   Jika dibiarkan kosong, frasa default dari konfigurasi skrip akan digunakan.  
5. Terapkan Watermark: Klik tombol "Apply Watermark".  
6. Tinjau Pratinjau HTML: Area "Watermarked HTML Preview" akan menampilkan teks Anda dengan efek watermarking yang terlihat diterapkan pada konten teks biasa. Perhatikan bahwa pratinjau ini merender HTML secara langsung dan mungkin tidak sepenuhnya mencerminkan spasi atau tampilan akhir di forum BBCode.  
7. Gunakan Output BBCode: Area teks "Watermarked BBCode Output (Forums)" akan berisi BBCode akhir yang telah diproses. Salin ini dan tempel ke editor posting forum phpBB Anda.

## **4\. Penjelasan Teknik Watermarking**

Skrip ini menggunakan beberapa lapisan penyamaran:

* Pewarnaan Dinamis Tingkat Kalimat:  
  * Teks biasa (yang tidak sudah ada di dalam tag BBCode seperti \[b\] atau \[color\]) dipecah menjadi segmen-segmen kalimat.  
  * Setiap segmen kalimat kemudian dibungkus dalam tag \[color=WARNA\_HAMPIR\_HITAM\_ACAK\] sendiri. Ini menghasilkan variasi warna yang halus antar kalimat.  
* Penggantian Spasi:  
  * Dengan probabilitas tertentu (CONFIG.PROB\_SPACE\_TO\_INVISIBLE\_CHAR), karakter spasi diganti dengan satu karakter alfabet (misalnya, 'a', 'b', 'c') dari CONFIG.MICRO\_INVISIBLE\_CHARS.  
  * Karakter pengganti ini dibungkus dalam BBCode sebagai \[color=WARNA\_DEKAT\_LATAR\_BELAKANG\]karakter\[/color\].  
* Paragraf & Kalimat Tak Terlihat:  
  * Penempatan: Disisipkan dengan probabilitas (CONFIG.PROB\_NEWLINE\_TO\_INVISIBLE\_PARA) ketika karakter baris baru (\\n) diproses (tetapi tidak di antara dua baris baru berturut-turut), atau di akhir seluruh input teks jika tidak diakhiri dengan baris baru (menggunakan CONFIG.PROB\_ADD\_END\_PARA\_INVISIBLE\_SENTENCE). Selain itu, digunakan untuk mengisi baris hingga mencapai panjang rata-rata target.  
  * Konten: Menggunakan frasa dari input khusus pengguna atau CONFIG.DEFAULT\_INVISIBLE\_PARAGRAPH\_CONTENT.  
  * Struktur HTML (untuk Pratinjau): Wadah luar \<span\> (misalnya, \<span class="invisible-paragraph-container" style="display:block; line-height:0.1px; height:0.1px; overflow:hidden;"\>...\</span\>) membuatnya mengambil ruang visual minimal dalam pratinjau HTML. Konten di dalamnya (seluruh frasa) dibungkus dalam satu \<span\> lagi dengan kelas invisible-fragment dan diberi warna mendekati latar belakang.  
  * Struktur BBCode: Seluruh blok *tidak* menggunakan tag \[size=1\]. Hanya tag \[color=WARNA\_DEKAT\_LATAR\_BELAKANG\] yang diterapkan pada keseluruhan frasa tak terlihat.  
* Pewarnaan Dinamis (Umum):  
  * Fungsi getVisibleTextColor() dan getInvisibleTextColor() menghasilkan warna RGB yang sedikit bervariasi dari warna dasar (mendekati hitam untuk yang terlihat, mendekati warna latar belakang halaman untuk yang tak terlihat). Ini mempersulit penghapusan watermark hanya dengan menghilangkan satu warna tetap. Warna-warna ini dikonversi ke HEX untuk BBCode.  
* Penyetaraan Panjang Baris Rata-rata:  
  * Target panjang baris ditentukan oleh panjang teks rata-rata dari baris-baris yang tidak kosong pada input asli Anda.  
  * Setiap baris output yang lebih pendek dari target ini akan ditambahkan dengan paragraf/kalimat tak terlihat (yang memiliki ukuran font normal tetapi berwarna seperti latar belakang) hingga mendekati panjang target. Ini berlaku juga untuk baris yang awalnya kosong.  
* Pelestarian Tag BBCode Asli:  
  * Skrip ini memeriksa apakah segmen teks berada tepat di antara tag BBCode pembuka dan penutup yang dikenali (misalnya, \[b\]teks\[/b\], \[color=red\]teks\[/color\]).  
  * Jika demikian, teks di dalam tag tersebut tidak akan diproses lebih lanjut untuk pewarnaan tingkat kalimat, sehingga mempertahankan gaya asli yang Anda terapkan.

## **5\. Konfigurasi (melalui objek** CONFIG **JavaScript)**

Perilaku skrip dapat disesuaikan dengan mengedit objek CONFIG di awal tag \<script\>. Opsi utama meliputi:

* BACKGROUND\_COLOR, TEXT\_COLOR\_BASE: Warna dasar untuk teks tak terlihat dan terlihat. BACKGROUND\_COLOR harus cocok dengan warna latar belakang forum Anda (saat ini diatur ke \#fffccc atau rgb(255, 252, 204\)).  
* COLOR\_VARIATION, TEXT\_COLOR\_VARIATION: Rentang pengacakan untuk warna.  
* PROB\_SPACE\_TO\_INVISIBLE\_CHAR: Probabilitas mengganti spasi dengan karakter tak terlihat.  
* PROB\_NEWLINE\_TO\_INVISIBLE\_PARA: Probabilitas menyisipkan paragraf tak terlihat pada baris baru.  
* PROB\_ADD\_END\_PARA\_INVISIBLE\_SENTENCE: Probabilitas menambahkan kalimat tak terlihat di akhir input (jika tidak diakhiri baris baru).  
* DEFAULT\_INVISIBLE\_PARAGRAPH\_CONTENT: Frasa default yang digunakan jika area input khusus kosong.  
* MICRO\_INVISIBLE\_CHARS: Kumpulan karakter alfabet yang digunakan untuk penggantian spasi satu karakter.  
* LOCALSTORAGE\_KEY\_CUSTOM\_PHRASES: Kunci yang digunakan untuk menyimpan/mengambil frasa khusus.

## **6\. Debugging**

Di dalam bagian \<style\> HTML, terdapat aturan CSS yang dikomentari. Anda dapat menghilangkan komentar pada aturan ini untuk membuat elemen "tak terlihat" menjadi berbeda secara visual (misalnya, dengan latar belakang atau garis tepi berwarna) di area "Watermarked HTML Preview". Ini sangat membantu untuk memahami di mana dan bagaimana watermark diterapkan.

## **7\. Batasan & Pertimbangan**

* Perbedaan Pratinjau HTML: Pratinjau HTML menunjukkan bagaimana konten dimodifikasi tetapi tidak sepenuhnya mensimulasikan mesin rendering phpBB. Spasi dan tata letak visual yang tepat dari posting BBCode akhir mungkin berbeda.  
* Varian BBCode: BBCode yang dihasilkan menggunakan tag umum (\[color\]). Meskipun didukung secara luas, rendering yang tepat dapat sedikit berbeda antara versi perangkat lunak forum yang berbeda atau tema khusus.  
* Tidak Sepenuhnya Aman: Alat ini bertujuan sebagai pencegah. Individu yang bertekad dengan keterampilan teknis masih dapat mencoba membersihkan teks, meskipun tujuannya adalah membuat proses ini membosankan dan rawan kesalahan.  
* Aksesibilitas: Penggunaan konten tak terlihat yang berlebihan dapat menimbulkan tantangan bagi pengguna yang mengandalkan pembaca layar atau teknologi bantu lainnya. Gunakan dengan pertimbangan.  
* Kinerja: Teks yang sangat panjang dengan probabilitas tinggi untuk penyisipan mungkin memerlukan waktu beberapa saat untuk diproses di browser.  
* Batas Karakter Forum: Perhatikan batas karakter maksimum per posting di forum Anda. Penambahan watermark yang ekstensif, terutama padding baris, dapat meningkatkan jumlah karakter secara signifikan.

## **8\. Potensi Peningkatan di Masa Depan**

* Antarmuka pengguna untuk menyesuaikan probabilitas CONFIG tanpa mengedit skrip.  
* Parsing BBCode yang lebih canggih untuk menangani struktur BBCode yang sangat kompleks atau non-standar.
