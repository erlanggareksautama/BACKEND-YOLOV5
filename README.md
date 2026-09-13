# 🚏 BACKEND-YOLOV5 — Deteksi Kondisi Halte Berbasis Laporan Warga



## 📌 Skenario Penggunaan

Model **YOLOv5** ini dikembangkan untuk mendukung sistem pelaporan warga terkait **kondisi fasilitas halte** di lingkungan perkotaan. Ketika seorang warga mengambil foto kondisi halte melalui aplikasi **WebGIS**, sistem akan secara otomatis mendeteksi dan memverifikasi keberadaan serta kondisi dari **4 parameter utama** kelengkapan halte, yaitu:

1. **Sidewalk / Guiding Block** — trotoar dan ubin pemandu (jalur difabel) di sekitar halte.
2. **Shelter / Kanopi Halte** — bangunan peneduh/atap halte tempat menunggu.
3. **Sign** — papan penanda/rambu bus stop.
4. **Street Light** — penerangan jalan di area halte.

Model dilatih menggunakan **2.000 dataset foto** yang dikumpulkan dan dianotasi melalui platform **Roboflow**. Hasil deteksi ini digunakan sebagai **lapisan validasi otomatis**: sebelum laporan warga diteruskan ke **Dinas Perhubungan (Dishub)**, sistem akan mengecek apakah objek yang dilaporkan (misalnya "shelter rusak" atau "papan sign hilang") benar-benar terdeteksi pada foto yang diunggah. Dengan begitu, proses verifikasi laporan menjadi lebih cepat, objektif, dan mengurangi laporan yang tidak valid sebelum ditindaklanjuti oleh petugas Dishub.

## 🧠 Tentang Proyek

Repository ini berisi **backend YOLOv5** yang digunakan untuk melakukan *object detection* pada foto kondisi halte, sebagai bagian dari sistem WebGIS pelaporan infrastruktur transportasi publik.

## 🗂️ Dataset

| Detail | Keterangan |
| Sumber | Roboflow (custom dataset) |
| Jumlah data | ± 2.000 foto |
| Kelas anotasi | `crosswalk`, `horizontal-directional-tile`, `road`, `shelter`, `sidewalk`, `sign`, `street_light`, `vertical-directional-tile`, `warning-tile` |
| Format | YOLOv5 (`.txt` label + `.yaml` config) |

> Catatan: kelas `crosswalk`, `horizontal/vertical-directional-tile`, `road`, dan `warning-tile` merupakan turunan/pendukung dari parameter **sidewalk & guiding block**, sedangkan `shelter` dan `sign` merepresentasikan parameter kanopi dan papan tanda halte.

## 📊 Evaluasi Model



**Contoh hasil inferensi:**
Pada gambar uji lapangan, model berhasil mendeteksi kelas `shelter` pada sebuah halte dengan tingkat keyakinan **0.51**. Confidence yang tergolong sedang ini — meski recall kelas `shelter` tinggi (0.95) pada evaluasi keseluruhan — mengindikasikan bahwa kondisi pencahayaan, sudut pengambilan gambar, atau oklusi oleh objek lain (pohon, tiang listrik) di lapangan masih dapat menurunkan skor keyakinan deteksi.

### Ringkasan Temuan
- Kelas dengan struktur visual khas dan kontras tinggi (`shelter`, `warning-tile`, `vertical-directional-tile`, `horizontal-directional-tile`) memiliki performa deteksi terbaik.
- Kelas `sign` dan `road` masih memerlukan penambahan data latih dan variasi sudut pandang.
- Kelas `crosswalk` dan `street_light` **belum berfungsi dengan baik** — kemungkinan disebabkan oleh jumlah sampel yang minim, kualitas anotasi, atau kemiripan visual dengan objek latar (background).

### Rekomendasi Perbaikan
1. Menambah jumlah dan variasi data latih untuk kelas `crosswalk`, `sign`, dan `street_light`.
2. Melakukan augmentasi data (perubahan pencahayaan, rotasi, oklusi) untuk memperkuat generalisasi model.
3. Meninjau ulang kualitas anotasi (bounding box) pada kelas dengan recall rendah.
4. Menurunkan/menyesuaikan confidence threshold khusus untuk kelas `shelter` agar deteksi pada kondisi lapangan yang kurang ideal tetap tertangkap.

## 🌐 Integrasi dengan WebGIS

Output deteksi (kelas objek + skor keyakinan) dikirim melalui API backend ke sistem WebGIS pelaporan warga, untuk kemudian:
1. Mencocokkan hasil deteksi dengan jenis laporan yang diajukan warga.
2. Menandai laporan sebagai **tervalidasi** apabila objek yang dilaporkan terdeteksi sesuai kelasnya.
3. Meneruskan laporan yang telah tervalidasi ke **Dinas Perhubungan (Dishub)** untuk ditindaklanjuti.

## 📁 Struktur Direktori (ringkas)

BACKEND-YOLOV5/
├── data/              # konfigurasi dataset (.yaml)
├── weights/           # model hasil training (best.pt)
├── detect.py          # script inferensi
├── train.py           # script training
├── requirements.txt
└── README.md

## 📚 Referensi
- Ultralytics YOLOv5 — https://github.com/ultralytics/yolov5
- Roboflow — https://roboflow.com

## 📄 Lisensi
Proyek ini mengikuti lisensi dasar YOLOv5 (AGPL-3.0), kecuali dinyatakan lain oleh pemilik repository.# BACKEND-YOLOV5
