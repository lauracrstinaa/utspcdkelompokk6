# UTS PCD Kelompok 6

Proyek UTS Pengolahan Citra Digital (PCD) - Kelompok 6.

## Anggota
- lauracrstinaa
- victoriaa20
- gwediaa
- Shaazrn

# UTS Pengolahan Citra Digital — Topik 3: Binerisasi Dokumen Berpencahayaan Tidak Merata

Pipeline pengolahan citra klasik (tanpa deep learning) untuk mengubah foto dokumen yang berpencahayaan tidak merata menjadi citra biner yang terbaca (teks hitam, latar putih). Seluruh pipeline dijalankan oleh satu notebook, `UTS_PCD_FIX.ipynb`, dan diakhiri dengan tabel metrik evaluasi.

## Alur pipeline

1. **Pra-proses**: grayscale dan Gaussian blur (buatan sendiri).
2. **Koreksi iluminasi**: Black Hat berkernel elips besar dan CLAHE.
3. **Binerisasi**: Global Otsu (buatan sendiri), Adaptive Mean, dan Adaptive Gaussian dibandingkan berdasarkan F1.
4. **Pasca-proses morfologi**: opening, closing, dan penyaringan komponen terhubung kecil.
5. **Evaluasi**: precision, recall, F1, dan IoU tingkat piksel terhadap mask ground truth.

Dua operasi yang ditulis dari nol hanya dengan NumPy: **konvolusi 2D** (beserta Gaussian blur) dan **thresholding Otsu**. Keduanya diverifikasi terhadap OpenCV pada 10 citra.

## Unduh berkas

- [Unduh FIX.zip](https://github.com/lauracrstinaa/utspcdkelompokk6/releases/download/v1.0/FIX.zip)
- [Halaman release v1.0](https://github.com/lauracrstinaa/utspcdkelompokk6/releases/tag/v1.0)

## Persyaratan

- Python 3.9 atau lebih baru
- Pustaka pada `requirements.txt`: NumPy, pandas, OpenCV, Matplotlib, dan Jupyter Notebook

```bash
pip install -r requirements.txt
```

## Struktur folder

```
.
├── UTS_PCD_FIX.ipynb
├── requirements.txt
├── README.md
├── data/
│   ├── raw/             # foto dokumen: <lembar>_<kondisi>.jpg
│   └── gt_masks/        # mask ground truth PNG: <lembar>_<kondisi>.png
└── hasil/               # dibuat otomatis saat notebook dijalankan
```

Aturan penamaan data:

- Nama foto berformat `<lembar>_<kondisi>.jpg`, misalnya `A_K3.jpg` (lembar dokumen A, kondisi K3). Ekstensi yang didukung: `.jpg`, `.jpeg`, `.png`.
- Mask ground truth memakai nama yang sama dengan fotonya berekstensi `.png`, misalnya `A_K3.png`. Piksel tinta bernilai putih (di atas 127), latar hitam.
- Pembagian data dilakukan **per lembar dokumen**, sehingga dokumen yang sama tidak muncul di citra pengembangan (sekitar 60%) dan citra uji (sekitar 40%) sekaligus.

Kondisi pengambilan citra:

| Kode | Kondisi | Kode | Kondisi |
|---|---|---|---|
| K1 | Terang merata (jendela) | K6 | Lampu dari kanan |
| K2 | Terang merata (lampu kamar) | K7 | Redup |
| K3 | Bayangan tangan | K8 | Miring |
| K4 | Bayangan HP | K9 | Jauh / latar beda |
| K5 | Lampu dari kiri | K10 | Melengkung / kusut |

## Cara menjalankan

### Opsi 1: Google Colab

1. Unggah `UTS_PCD_FIX.ipynb` ke Google Colab.
2. Siapkan dua berkas zip: `foto.zip` berisi foto dokumen dan `mask.zip` berisi mask ground truth.
3. Pilih **Runtime > Run all**. Saat diminta, unggah `foto.zip` lalu `mask.zip`.
4. Setelah selesai, `hasil_uts.zip` terunduh otomatis.

### Opsi 2: Lokal

1. Letakkan foto di `data/raw/` dan mask di `data/gt_masks/`.
2. Jalankan Jupyter di folder yang sama dengan notebook:
   ```bash
   jupyter notebook UTS_PCD_FIX.ipynb
   ```
3. Pilih **Kernel > Restart & Run All**.

Seluruh hasil tersimpan di folder `hasil/`.

### Uji coba dengan data sintetis

Untuk memastikan kode berjalan tanpa data asli, ubah `DEMO = False` menjadi `DEMO = True` pada sel Langkah 1. Notebook akan membuat dokumen sintetis di `data_demo/`. Hasil dari mode ini **tidak boleh dilaporkan** sebagai hasil penelitian.

## Isi notebook

| Bagian | Isi |
|---|---|
| Modul 1 | Manajemen dataset: pemuatan, pembagian dev/uji, pemeriksaan ketentuan |
| Modul 2 | Operasi dari nol: konvolusi 2D, Gaussian blur, Otsu |
| Modul 3 | Pipeline empat tahap |
| Modul 4 | Verifikasi terhadap OpenCV |
| Modul 5 | Metrik evaluasi |
| Modul 6 | Perbandingan metode dan penyetelan parameter |
| Modul 7 | Analisis kegagalan |
| Modul 8 | Data sintetis untuk uji coba |
| Langkah 1-7 | Menjalankan seluruh pipeline pada data dan menyimpan hasil |

## Keluaran di folder `hasil/`

| Berkas | Isi |
|---|---|
| `verifikasi.csv`, `verifikasi_ringkasan.csv` | Selisih piksel, MAE, dan waktu: buatan sendiri vs OpenCV |
| `verifikasi_konvolusi.csv` | Verifikasi konvolusi 2D dengan kernel asimetris |
| `verifikasi_separable.csv` | Gaussian 2D vs separabel vs OpenCV |
| `tuning_dev.csv` | Hasil grid penyetelan pada citra dev |
| `alasan_parameter.csv` | Nilai parameter terpilih beserta bukti percobaannya |
| `konfigurasi_terpilih.json` | Konfigurasi akhir pipeline |
| `bukti_pergeseran_morfologi.csv` | Bukti pergeseran morfologi pada kernel genap |
| `perbandingan_metode.csv` | Otsu vs Adaptive Mean vs Adaptive Gaussian |
| `metrik_uji_per_citra.csv` | Metrik setiap citra uji |
| `metrik_uji_per_kondisi.csv`, `metrik_uji_per_kondisi_detail.csv` | F1 per kondisi pengambilan |
| `ci_bootstrap.csv` | Selang kepercayaan 95% (bootstrap) |
| `f1_per_kondisi.png` | Grafik F1 per kondisi |
| `gagal_*.png` | Tiga citra uji terburuk untuk analisis kegagalan |
| `biner/` | Citra biner hasil pipeline untuk seluruh citra |

## Reproduksibilitas

- Seed acak tetap (`SEED = 42`).
- Pembagian dev/uji disimpan di `data/split.csv` dan dibaca ulang pada eksekusi berikutnya.
- Seluruh parameter dipilih dari F1 pada citra dev; angka akhir dilaporkan hanya dari citra uji.

## Dataset dan lisensi

Foto dipotret sendiri oleh kelompok. Jika dataset publik dipakai, isi sumber dan lisensinya pada `SUMBER_PUBLIK` di sel pengaturan notebook, lalu cantumkan juga di bagian ini.

## Anggota kelompok

Gizsha Anggraini (2430105030016) Ketua dan Integrator 
Laura Cristina (2430105030013) Pengembang Algoritma, Penguji/Penulis, Integrator
Kartika Maulidia Zyamzia (2430105030029) Penanggung Jawab Data, Pengembang Algoritma, Integrator
Victoria (2430205030003) Penanggung Jawab Data, Penguji/Penulis, Integrator
