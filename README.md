# USK Coffee

Aplikasi Streamlit untuk mendeteksi kelas biji kopi pada gambar menggunakan **YOLOv8n** dan **YOLOv11n**. Aplikasi menampilkan gambar masukan, hasil deteksi masing-masing model, serta tabel perbandingan hasilnya.

## Fitur

- Unggah gambar JPG, JPEG, PNG, BMP, atau WebP.
- Atur ambang confidence dari 0,25 hingga 1,00, dengan nilai awal 0,40.
- Tampilkan bounding box dan label hasil deteksi dari kedua model.
- Bandingkan kelas yang terdeteksi, rata-rata confidence, dan durasi deteksi.
- Gunakan CUDA jika tersedia, atau CPU jika CUDA tidak tersedia.

Kedua model menggunakan empat kelas berikut:

| ID | Kelas |
| --- | --- |
| 0 | `defect` |
| 1 | `longberry` |
| 2 | `peaberry` |
| 3 | `premium` |

## Struktur proyek

```text
usk-coffe/
├── .devcontainer/
│   └── devcontainer.json    # Konfigurasi lingkungan pengembangan
├── images/
│   ├── default_image.jpg
│   ├── gambar_yang_terdeteksi.jpg
│   ├── gambar_yang_terdeteksi1.jpg
│   └── gambar_yang_terdeteksi2.jpg
├── model/
│   ├── yolov8n.pt           # Bobot model deteksi biji kopi YOLOv8n
│   └── yolov11n.pt          # Bobot model deteksi biji kopi YOLOv11n
├── app.py                  # Antarmuka, pemuatan model, dan inferensi
├── settings.py             # Konfigurasi lokasi aset dan sumber masukan
├── style.css               # Gaya tampilan aplikasi
├── requirements.txt        # Dependensi Python
├── Petunjuk.txt            # Catatan penggunaan awal
└── README.md
```

## Instalasi dan menjalankan aplikasi

Gunakan Python 3.11 agar sesuai dengan konfigurasi dev container yang disertakan. Jalankan perintah berikut dari direktori proyek.

1. Buat virtual environment:

   ```bash
   python -m venv .venv
   ```

2. Aktifkan virtual environment.

   macOS / Linux:

   ```bash
   source .venv/bin/activate
   ```

   Windows PowerShell:

   ```powershell
   .\.venv\Scripts\Activate.ps1
   ```

3. Instal dependensi:

   ```bash
   python -m pip install -r requirements.txt
   ```

4. Jalankan aplikasi:

   ```bash
   python -m streamlit run app.py
   ```

5. Buka alamat yang ditampilkan Streamlit di terminal, biasanya `http://localhost:8501`.

Bobot kedua model tersedia di folder `model/`. GPU bersifat opsional; penggunaan CUDA memerlukan lingkungan PyTorch dan driver GPU yang sesuai.

## Cara menggunakan

1. Atur **Model Confidence Level** pada sidebar.
2. Pilih sumber **Image** dan unggah gambar melalui **Select Image File**.
3. Klik **Process Detection**.
4. Periksa gambar masukan dan hasil deteksi YOLOv8 serta YOLOv11.
5. Bandingkan ringkasan pada tabel **Statistical Analysis of Detection Results**.

Gambar masukan disesuaikan menjadi lebar 640 piksel dengan rasio aspek dipertahankan sebelum inferensi. Tabel menampilkan kelas unik yang terdeteksi, rata-rata confidence objek, dan durasi pemanggilan prediksi. Jika tidak ada objek terdeteksi, kelas ditampilkan sebagai `None`, sedangkan confidence dan durasi ditampilkan sebagai `-`.

Confidence dan durasi tersebut belum cukup untuk menyimpulkan model mana yang lebih akurat. Evaluasi akurasi memerlukan dataset uji berlabel dan metrik seperti precision, recall, serta mAP. Repository ini belum menyertakan skrip pelatihan atau evaluasi dataset.

## Keterbatasan implementasi saat ini

- **Lokasi log masih tetap:** `app.py` menggunakan `d:/GitHub/Mini-Experiment/log`. Sesuaikan lokasi ini dengan direktori yang dapat ditulis pada lingkungan Anda. Pada macOS/Linux, jalur tersebut dapat diperlakukan sebagai direktori relatif bernama `d:`. File log lama juga dikosongkan setiap eksekusi ulang skrip.
- **Model dimuat ulang saat skrip dijalankan ulang:** pemuatan model belum menggunakan cache Streamlit, sehingga perubahan widget dapat menambah waktu tunggu.
- **Hasil belum disimpan dalam session state:** hasil deteksi hanya tampil pada eksekusi ketika tombol proses diklik dan dapat hilang setelah interaksi berikutnya.
- **Penanganan kegagalan belum lengkap:** kegagalan memuat model ditampilkan sebagai pesan, tetapi pemanggilan prediksi belum dicegah. Pembacaan gambar unggahan dan inferensi juga belum memiliki penanganan error khusus.
- **Konfigurasi gambar default belum selaras:** `settings.py` merujuk `Default_Image.jpg`, sedangkan file yang tersedia adalah `default_image.jpg`. File `Detected_Image.jpg` yang dikonfigurasi juga belum tersedia. Alur deteksi saat ini tetap mensyaratkan gambar unggahan.
- **Versi dependensi belum dikunci:** instalasi pada waktu atau lingkungan berbeda dapat menghasilkan kombinasi versi yang berbeda.
- **Konfigurasi dev container menonaktifkan CORS dan proteksi XSRF:** tinjau kembali opsi tersebut sebelum menggunakan konfigurasi ini untuk akses publik.

## Pemeriksaan awal jika terjadi masalah

- Pastikan virtual environment aktif dan dependensi berhasil diinstal.
- Pastikan `model/yolov8n.pt` dan `model/yolov11n.pt` tersedia serta dapat dibaca.
- Jika aplikasi gagal menulis log, periksa lokasi `log_directory` di `app.py` dan izin direktori tujuan.
- Jika pembacaan gambar gagal, coba gambar JPG atau PNG lain yang valid.
- Jika GPU tidak terdeteksi, aplikasi menggunakan CPU; periksa instalasi PyTorch dan dukungan CUDA bila ingin menggunakan GPU.
