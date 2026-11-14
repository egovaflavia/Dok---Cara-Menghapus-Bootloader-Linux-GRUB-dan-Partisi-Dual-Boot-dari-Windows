# Cara Menghapus Bootloader Linux (Dual Boot) dari Windows

Panduan ini menjelaskan langkah-langkah untuk menghapus sisa *bootloader* Linux (GRUB) dan partisinya dari Windows agar sistem tidak tertahan di layar error *GRUB rescue* saat komputer dinyalakan.

---

## Tahap 1: Hapus Bootloader Linux dari Partisi EFI (UEFI)

Metode ini dilakukan melalui Command Prompt dengan hak administrator menggunakan utilitas `diskpart`.

1. Klik tombol **Start**, cari **cmd**, lalu pilih **Run as administrator**.
2. Masuk ke utilitas partisi dengan mengetik:
   ```cmd
   diskpart
   ```
3. Tampilkan seluruh volume disk yang terdeteksi:
   ```cmd
   list volume
   ```
4. Perhatikan daftar volume yang muncul:
   * Cari volume dengan format **FAT32** berukuran sekitar **100 MB – 500 MB** (biasanya berlabel *System* atau tanpa label). Volume ini adalah partisi EFI.
   * Catat nomor volumenya (misalnya: `Volume 2`).
5. Pilih volume EFI tersebut:
   ```cmd
   select volume 2
   ```
   *(Sesuaikan angka `2` dengan nomor volume EFI pada sistem Anda)*
6. Berikan *drive letter* sementara (misalnya huruf `Z`) agar partisi dapat diakses:
   ```cmd
   assign letter=Z
   ```
7. Keluar dari Diskpart:
   ```cmd
   exit
   ```
8. Berpindah ke drive `Z:` dan periksa isi folder `EFI`:
   ```cmd
   Z:
   cd EFI
   dir
   ```
   Anda akan melihat daftar folder sistem operasi, seperti `Microsoft`, `Boot`, serta folder distro Linux seperti `ubuntu`, `fedora`, `kali`, `arch`, atau `debian`.
9. Hapus folder distro Linux tersebut beserta seluruh isinya:
   ```cmd
   rmdir /S /Q ubuntu
   ```
   *(Ganti `ubuntu` sesuai dengan nama folder distro Linux yang tertera di layar)*
10. Lepaskan kembali *drive letter* `Z:` agar partisi EFI tersembunyi kembali:
    ```cmd
    diskpart
    select volume 2
    remove letter=Z
    exit
    ```

> **Verifikasi:** Coba masuk kembali ke `Z:` di Command Prompt. Sistem akan menampilkan pesan `The system cannot find the drive specified`, yang menandakan *drive letter* sudah dilepas dan folder GRUB telah bersih.

---

## Tahap 2: Hapus Partisi Linux di Disk Management

Setelah *bootloader* GRUB dibersihkan, ruang penyimpanan yang sebelumnya digunakan oleh Linux dapat dikembalikan ke partisi Windows.

1. Tekan kombinasi tombol **Win + X**, lalu pilih **Disk Management**.
2. Cari partisi instalasi Linux:
   * Partisi Linux biasanya tidak memiliki *file system* yang dikenali Windows (berstatus **Healthy**, tetapi tanpa format NTFS/FAT32, atau tertera *RAW* / tanpa huruf drive).
   * **Peringatan:** Pastikan tidak menghapus partisi berlabel **C:**, partisi **EFI System** (100–500 MB), atau partisi **Recovery**.
3. Klik kanan pada partisi Linux tersebut, lalu pilih **Delete Volume**. Status ruangnya akan berubah menjadi **Unallocated space** (berwarna hitam).
4. Gabungkan kembali ruang kosong tersebut:
   * Klik kanan pada partisi utama Windows (biasanya Drive **C:** atau partisi data di sebelah kirinya).
   * Pilih **Extend Volume**, lalu ikuti panduan *Next* hingga selesai.

> **Verifikasi:** Restart komputer. Sistem akan langsung melakukan *booting* ke Windows tanpa menampilkan pilihan menu GRUB atau layar *error*.
