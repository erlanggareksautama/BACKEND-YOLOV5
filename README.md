# 🚏 BACKEND-YOLOV5 — Deteksi Kondisi Halte Berbasis Laporan Warga

## Confusson Matrix YOLOV5 (Tingkat Kepercayaan)
<img width="3000" height="2250" alt="image" src="https://github.com/user-attachments/assets/e4aa062b-4399-4c7e-befb-a6f629a5bf62" />

## 📌 Skenario Penggunaan

Model **YOLOv5** ini dikembangkan untuk mendukung proses verifikasi laporan warga terkait **kondisi fasilitas halte** di lingkungan perkotaan. Warga dapat mengirimkan laporan beserta foto kondisi halte melalui aplikasi pelaporan. Foto yang dikirimkan warga kemudian diproses oleh sistem **object detection**, dan hasilnya ditampilkan pada **dashboard milik Dinas Perhubungan (Dishub)**.

Melalui dashboard tersebut, petugas Dishub dapat langsung melihat di WebGIS TransConnect apakah objek yang dilaporkan warga benar-benar terdeteksi pada foto, berdasarkan **4 parameter utama** kelengkapan halte:

1. **Sidewalk / Guiding Block** — trotoar dan ubin pemandu (jalur difabel) di sekitar halte.
2. **Shelter / Kanopi Halte** — bangunan peneduh/atap halte tempat menunggu.
3. **Sign** — papan penanda/rambu bus stop.
4. **Street Light** — penerangan jalan di area halte.

Model dilatih menggunakan **2.000 dataset foto** yang dikumpulkan dan dianotasi melalui platform **Roboflow**. Fitur deteksi objek ini **hanya dapat diakses/dilihat oleh petugas Dishub** melalui dashboard internal, sebagai alat bantu untuk mengecek keberadaan objek yang dilaporkan sebelum petugas menindaklanjuti laporan tersebut secara langsung di lapangan. Dengan begitu, proses pengecekan awal menjadi lebih cepat dan objektif, tanpa mengandalkan penilaian manual semata terhadap foto laporan.

---

## 🧠 Tentang Proyek

Repository ini berisi **backend YOLOv5** yang digunakan untuk melakukan *object detection* pada foto kondisi halte yang dikirim warga, dan hasilnya ditampilkan pada dashboard Dinas Perhubungan (Dishub) sebagai alat bantu verifikasi laporan.

## 🗂️ Dataset

| Detail | Keterangan |
|---|---|
| Sumber | Roboflow (custom dataset) |
| Jumlah data | ± 2.000 foto |
| Kelas anotasi | `crosswalk`, `horizontal-directional-tile`, `road`, `shelter`, `sidewalk`, `sign`, `street_light`, `vertical-directional-tile`, `warning-tile` |
| Format | YOLOv5 (`.txt` label + `.yaml` config) |

> Catatan: kelas `crosswalk`, `horizontal/vertical-directional-tile`, `road`, dan `warning-tile` merupakan turunan/pendukung dari parameter **sidewalk & guiding block**, sedangkan `shelter` dan `sign` merepresentasikan parameter kanopi dan papan tanda halte.

## 📊 Evaluasi Model

<img width="720" height="1280" alt="image" src="https://github.com/user-attachments/assets/361b22fb-8fa3-4851-8937-f6e775fad814" />

**Contoh hasil inferensi:**
Pada gambar uji lapangan, model berhasil mendeteksi kelas `shelter` pada sebuah halte dengan tingkat keyakinan **0.51**. Confidence yang tergolong sedang ini — meski recall kelas `shelter` tinggi (0.95) pada evaluasi keseluruhan — mengindikasikan bahwa kondisi pencahayaan, sudut pengambilan gambar, atau oklusi oleh objek lain (pohon, tiang listrik) di lapangan masih dapat menurunkan skor keyakinan deteksi. Pada dashboard Dishub, hasil semacam ini tetap ditampilkan sebagai indikasi objek "shelter terdeteksi", namun dengan skor keyakinan yang perlu dipertimbangkan petugas saat verifikasi.

### Rekomendasi Perbaikan
1. Menambah jumlah dan variasi data latih untuk kelas `crosswalk`, `sign`, dan `street_light`.
2. Melakukan augmentasi data (perubahan pencahayaan, rotasi, oklusi) untuk memperkuat generalisasi model.
3. Meninjau ulang kualitas anotasi (bounding box) pada kelas dengan recall rendah.
4. Menurunkan/menyesuaikan confidence threshold khusus untuk kelas `shelter` agar deteksi pada kondisi lapangan yang kurang ideal tetap tertangkap dan ditampilkan di dashboard.

---

## ⚙️ Instalasi

```bash
git clone https://github.com/erlanggareksautama/BACKEND-YOLOV5.git
cd BACKEND-YOLOV5
pip install -r requirements.txt

```

## 🖥️ Integrasi dengan Dashboard Dishub

Output deteksi (kelas objek + skor keyakinan) dikirim melalui API backend ke **dashboard internal Dinas Perhubungan (Dishub)**, dengan alur sebagai berikut:
1. Warga mengirimkan laporan beserta foto kondisi halte melalui aplikasi pelaporan.
2. Foto tersebut diproses oleh model YOLOv5 untuk mendeteksi keberadaan objek (sidewalk/guiding block, shelter, sign, street light).
3. Hasil deteksi (bounding box, label kelas, dan confidence score) ditampilkan **hanya pada dashboard Dishub**, sebagai alat bantu bagi petugas untuk mengecek kesesuaian laporan sebelum ditindaklanjuti.
4. Warga **tidak melihat** hasil deteksi ini secara langsung — fitur object detection sepenuhnya merupakan alat bantu internal bagi petugas Dishub.

## 📁 Struktur Direktori (ringkas)

```
BACKEND-YOLOV5/
├── data/              # konfigurasi dataset (.yaml)
├── weights/           # model hasil training (best.pt)
├── detect.py          # script inferensi
├── train.py           # script training
├── requirements.txt
└── README.md
```

## 📚 Referensi
- Ultralytics YOLOv5 — https://github.com/ultralytics/yolov5
- Roboflow — https://roboflow.com

## 📄 Lisensi
Proyek ini mengikuti lisensi dasar YOLOv5 (AGPL-3.0), kecuali dinyatakan lain oleh pemilik repository.
