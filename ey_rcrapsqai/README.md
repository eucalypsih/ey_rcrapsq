# ey_rcrapsqai


Apakah Anda ingin mengetahui cara **cache lokal FetchContent** agar CMake tidak selalu mengunduh ulang pustaka dari internet saat konfigurasi ulang?

Saya ingin mengetahui cara **cache lokal FetchContent** agar CMake tidak selalu mengunduh ulang pustaka dari internet saat konfigurasi ulang?

Jika Anda ingin menguji ketergantungan *run-time* yang sesungguhnya, apakah Anda tertarik untuk mencoba mengubah konfigurasi proyek Anda menjadi **Shared Library (`.so` atau `.dylib`)** agar bisa melihat kapan program akan error saat file pustakanya dipindahkan?

Jika proyek Anda ditujukan untuk didistribusikan ke komputer lain, apakah Anda tertarik untuk mengubah `add_library(myp_lib OBJECT ...)` menjadi `STATIC` atau `SHARED` library agar file distribusinya menjadi pustaka standar (`.a `/ `.so`) yang universal?

Apakah Anda ingin tahu bagaimana cara menggabungkan biner `fmt` dan `myp_lib` menjadi **satu file `.a` yang benar-benar utuh** agar teknik `$<LINK_ONLY:...>` tidak diperlukan lagi?

<br>
