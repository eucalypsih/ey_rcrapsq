# ey_rcrapsqc

qc:
Saya ingin mengetahui cara **cache lokal FetchContent** agar CMake tidak selalu mengunduh ulang pustaka dari internet saat konfigurasi ulang?

<br>

a-ai:
Secara default, CMake akan menyimpan pustaka hasil `FetchContent` di dalam folder `build` proyek Anda (`${CMAKE_BINARY_DIR}/_deps`). Masalahnya, jika Anda menghapus folder `build` atau melakukan konfigurasi ulang dari awal (`cmake --fresh`), CMake terpaksa mengunduh ulang pustaka tersebut dari internet.

Ada 3 cara untuk mengaktifkan **cache lokal permanen** agar CMake tetap bisa bekerja secara offline atau tidak mengunduh ulang file saat dikonfigurasi ulang:

---

Cara 1: Menggunakan Fitur Cache Bawaan `FETCHCONTENT_BASE_DIR` (Paling Direkomendasikan)
Sejak CMake versi 3.24+, CMake memperkenalkan mekanisme internal agar unduhan arsip tetap bertahan meskipun Anda melakukan `cmake --fresh`. Anda dapat memindahkan seluruh folder dasar unduhan dependensi ke direktori global di luar folder `build` menggunakan variabel `FETCHCONTENT_BASE_DIR`.

Cukup tambahkan baris berikut di file `CMakeLists.txt` utama Anda, **sebelum** melakukan `include(FetchContent)`:
```cmake
# Tentukan folder cache global (contoh: di home directory pengguna)
if(CMAKE_HOST_WIN32)
    set(FETCHCONTENT_BASE_DIR "$ENV{USERPROFILE}/.cmake_fetchcontent_cache" CACHE PATH "")
else()
    set(FETCHCONTENT_BASE_DIR "$ENV{HOME}/.cmake_fetchcontent_cache" CACHE PATH "")
endif()

include(FetchContent)
# ... Deklarasi FetchContent_Declare Anda di bawahnya ...

```
- Kelebihan: Sangat bersih, otomatis memisahkan dependensi antar proyek, dan aman dari proses penghapusan folder build.

---

Cara 2: Menggunakan Mode Offline `FETCHCONTENT_FULLY_DISCONNECTED`
Jika Anda sudah pernah sukses mengunduh pustaka tersebut minimal satu kali, skrip sumbernya sebenarnya sudah tersimpan di lokal build Anda. Anda dapat memaksa CMake untuk berhenti mengecek repositori Git atau mengunduh ulang dengan mengaktifkan mode pemutus koneksi.

Jalankan perintah konfigurasi CMake Anda melalui terminal dengan menyertakan flag berikut:
```bash
cmake -B build -DFETCHCONTENT_FULLY_DISCONNECTED=ON

```
Atau jika Anda menggunakan **CMake Presets** (`CMakePresets.json`), tambahkan ke dalam konfigurasi Anda:
```json
"cacheVariables": {
  "FETCHCONTENT_FULLY_DISCONNECTED": "ON"
}

```
- Catatan: Aturan baru pada CMake 3.30+ mewajibkan source sudah harus diunduh (terpopulasi) terlebih dahulu sebelum Anda bisa menyalakan flag ini. Jika folder build Anda dihapus total, flag ini akan memicu error karena pustaka belum tersedia secara lokal.

---

Cara 3: Mengalihkan Jalur ke Folder Lokal (`FETCHCONTENT_SOURCE_DIR_<nama>`)
Jika Anda sudah mengkloning repositori `{fmt}` secara manual ke dalam hardisk komputer Anda (misal di folder `/pustaka/lokal/fmt`), Anda dapat memerintahkan `FetchContent` untuk langsung membaca folder lokal tersebut tanpa menyentuh jaringan sama sekali.

CMake menyediakan variabel bawaan dengan format `FETCHCONTENT_SOURCE_DIR_<UPPERCASE_NAME>`. Tanpa perlu mengubah isi berkas `CMakeLists.txt`, Anda cukup mengetikkan perintah ini di terminal saat konfigurasi:
```bash
# Untuk pustaka fmt, ubah nama target menjadi UPPERCASE (FMT)
cmake -B build -DFETCHCONTENT_SOURCE_DIR_FMT="/jalur/ke/folder/lokal/fmt"

```
- Kelebihan: Sangat cocok untuk pengembangan lokal, lingkungan korporat/kantor yang tidak memiliki akses internet (air-gapped), atau saat Anda ingin memodifikasi source code pustaka tersebut secara *real-time*.

---

Kesimpulan Penggunaan
- Gunakan **Cara 1** jika Anda ingin semua dependensi otomatis masuk ke satu folder cache pusat komputer Anda tanpa ribet.
- Gunakan **Cara 2** untuk mempercepat proses kompilasi ulang harian agar CMake tidak membuang waktu melakukan handshake koneksi Git.

<br>

qai:
Jika Anda ingin mencoba salah satu metode di atas, beri tahu saya:
- Apakah Anda menggunakan **sistem operasi Windows, Linux, atau macOS** untuk pengembangan ini?
- Apakah Anda mengonfigurasi CMake menggunakan **Terminal/CLI** atau lewat **IDE (seperti VS Code / CLion)**?



<br>
