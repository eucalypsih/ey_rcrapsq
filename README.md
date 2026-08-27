

```bash
-- Detecting CXX compile features - done
[ 11%] Creating directories for 'fmt-populate'
[ 22%] Performing download step (git clone) for 'fmt-populate'
Cloning into 'fmt-src'...
error: RPC failed; curl 92 HTTP/2 stream 5 reset by server (error 0x8 CANCEL)
error: 3305 bytes of body are still expected
fetch-pack: unexpected disconnect while reading sideband packet
fatal: early EOF
fatal: fetch-pack: invalid index-pack output
Cloning into 'fmt-src'...
error: RPC failed; curl 92 HTTP/2 stream 5 reset by server (error 0x8 CANCEL)
error: 11647 bytes of body are still expected
fetch-pack: unexpected disconnect while reading sideband packet
fatal: early EOF
fatal: fetch-pack: invalid index-pack output
Cloning into 'fmt-src'...
error: RPC failed; curl 56 Recv failure: Connection reset by peer
error: 8787 bytes of body are still expected
fetch-pack: unexpected disconnect while reading sideband packet
fatal: early EOF
fatal: fetch-pack: invalid index-pack output
Had to git clone more than once: 3 times.
CMake Error at fmt-subbuild/fmt-populate-prefix/tmp/fmt-populate-gitclone.cmake:55 (message):
  Failed to clone repository:

    'https://github.com/fmtlib/fmt'


make[2]: *** [CMakeFiles/fmt-populate.dir/build.make:102: fmt-populate-prefix/src/fmt-populate-stamp/fmt-populate-download] Error 1
make[1]: *** [CMakeFiles/Makefile2:87: CMakeFiles/fmt-populate.dir/all] Error 2
make: *** [Makefile:91: all] Error 2

CMake Error at /data/data/com.termux/files/usr/share/cmake-4.4/Modules/FetchContent.cmake:1933 (message):
  Build step for fmt failed: 2
Call Stack (most recent call first):
  /data/data/com.termux/files/usr/share/cmake-4.4/Modules/FetchContent.cmake:1622 (__FetchContent_populateSubbuild)
  /data/data/com.termux/files/usr/share/cmake-4.4/Modules/FetchContent.cmake:2160:EVAL:2 (__FetchContent_doPopulation)
  /data/data/com.termux/files/usr/share/cmake-4.4/Modules/FetchContent.cmake:2160 (cmake_language)
  /data/data/com.termux/files/usr/share/cmake-4.4/Modules/FetchContent.cmake:2401 (__FetchContent_Populate)
  CMakeLists.txt:38 (FetchContent_MakeAvailable)


-- Configuring incomplete, errors occurred!

```

<br>

a-ai:
Error ini terjadi karena **koneksi internet tidak stabil** atau ukuran buffer Git terlalu kecil, sehingga server GitHub memutus sambungan (`Connection reset by peer` / `HTTP/2 stream 5 reset`) saat CMake mencoba mengunduh *library* fmt via `FetchContent`.

Berikut adalah beberapa cara untuk mengatasi masalah ini di Termux:

1. Ubah Konfigurasi Buffer Git (Paling Sering Berhasil)
Perbesar ukuran buffer Git dan matikan kompresi yang terlalu berat agar koneksi tidak mudah terputus. Jalankan perintah ini di terminal Termux Anda sebelum melakukan *build*:
```bash
git config --global http.postBuffer 524288000
git config --global http.maxRequestSize 524288000
git config --global core.compression 0

```
2. Paksa Git Menggunakan HTTP/1.1
Terkadang protokol HTTP/2 bermasalah pada jaringan tertentu di Termux. Anda bisa memaksa Git menggunakan HTTP/1.1 dengan perintah:
```bash
git config --global http.version HTTP/1.1

```
3. Ubah Kedalaman Clone (Shallow Clone)
Jika Anda memiliki akses untuk mengedit file `CMakeLists.txt` proyek tersebut, cari bagian `FetchContent_Declare(fmt ...)` dan tambahkan opsi `GIT_SHALLOW TRUE` untuk memperkecil ukuran unduhan (hanya mengambil *commit* terakhir).

Contoh perubahan:
```cmake
FetchContent_Declare(
  fmt
  GIT_REPOSITORY https://github.com/fmtlib/fmt
  GIT_TAG        xxxxxxxx # Versi tag/commit
  GIT_SHALLOW    TRUE     # Tambahkan baris ini
)

```
4. Bersihkan Cache CMake Sebelum Mencoba Lagi
Setelah melakukan salah satu langkah di atas, hapus folder *build* lama Anda agar CMake mengulangi proses unduhan dari awal tanpa membaca *cache* yang rusak:
```bash
rm -rf build/
mkdir build && cd build
cmake ..

```


<br>
