# 🐒 RandomForest — Monkey Pose Detector

Aplikasi deteksi pose tubuh manusia secara **real-time** menggunakan **MediaPipe Pose** untuk ekstraksi landmark tubuh dan **Random Forest Classifier** untuk klasifikasi pose. Hasil deteksi ditampilkan lewat GUI (Tkinter) berupa reaksi gambar "monyet" beserta efek suara, sesuai pose yang terdeteksi dari webcam.

Project ini merupakan tugas kelompok mata kuliah/Machine Learning (lihat `PPT/Presentasi Kelompok 5.pdf`) dengan studi kasus klasifikasi pose tubuh menggunakan algoritma **Random Forest**.

---

## ✨ Fitur

- Deteksi pose tubuh secara real-time dari webcam menggunakan **MediaPipe Pose**.
- Deteksi landmark tangan (**MediaPipe Hands**) sebagai visualisasi tambahan di frame kamera.
- Klasifikasi pose menggunakan model **Random Forest** (scikit-learn) yang dilatih dari dataset gambar.
- GUI desktop interaktif (Tkinter) yang menampilkan:
  - Feed kamera langsung beserta gambar landmark pose/tangan.
  - Gambar reaksi "monyet" sesuai kelas pose yang terdeteksi.
  - Label status & tingkat keyakinan (*confidence score*) prediksi.
  - Efek suara untuk pose tertentu (misalnya `hidup_jokowi`).
- Script terpisah untuk ekstraksi fitur (`pose_extractor.py`) dan training model (`train_model.py`), sehingga model dapat dilatih ulang dengan dataset sendiri.

---

## 🗂️ Struktur Project

```
RandomForest-main/
├── images/                    
│   ├── thinking/            
│   ├── idle/                  
│   ├── idea/                
│   ├── shocked/               
│   ├── hidup_jokowi/         
│   ├── monkey_fuck/          
│   └── other/                
├── audio/
│   └── hidup_jokowi.mp3      
├── PPT/
│   ├── Presentasi Kelompok 5.pdf
│   ├── kerangka berfikir random forest.png
│   └── Generate Video.mp4
├── monkey_thinking.png         # aset gambar reaksi monyet (per kelas)
├── monkey_idle.jpg
├── monkey_idea.png
├── monkey_shocked.png
├── monkey_scared.png
├── monkey_clinge.png
├── monkey_fuck.png
├── hidup_jokowi.png
├── pose_extractor.py           
├── train_model.py          
├── realtime_app.py             
└── pose_classifier.pkl
```

---

## 🧠 Cara Kerja

1. **Ekstraksi fitur** (`pose_extractor.py`)
   Setiap gambar diproses dengan **MediaPipe Pose** untuk mendapatkan 33 titik landmark tubuh (`x`, `y`, `visibility`), lalu diratakan (*flatten*) menjadi satu vektor fitur numerik.

2. **Training model** (`train_model.py`)
   Seluruh gambar pada folder `images/` diekstrak fiturnya, diberi label sesuai nama foldernya, kemudian dibagi menjadi data latih dan data uji (80:20). Model **`RandomForestClassifier`** (100 trees) dilatih dari data tersebut, dievaluasi dengan `classification_report`, lalu disimpan sebagai `pose_classifier.pkl`.

   | Folder gambar   | Label |
   |-----------------|:-----:|
   | `other`         | 0     |
   | `thinking`      | 1     |
   | `idle`          | 2     |
   | `idea`          | 3     |
   | `shocked`       | 4     |
   | `hidup_jokowi`  | 5     |
   | `monkey_fuck`   | 6     |

3. **Deteksi real-time** (`realtime_app.py`)
   Frame dari webcam diproses secara live, fitur pose diekstrak dengan cara yang sama seperti saat training, lalu diprediksi menggunakan model yang sudah dilatih. Hasil prediksi ditampilkan di jendela GUI berupa teks status, tingkat keyakinan, gambar reaksi monyet, serta efek suara (jika ada).

---

## ⚙️ Instalasi

### Prasyarat
- Python 3.9 – 3.11 (MediaPipe belum tentu kompatibel dengan versi Python terbaru)
- Webcam aktif untuk menjalankan `realtime_app.py`

### Langkah instalasi

```bash
# 1. Clone repository
git clone https://github.com/Sikon-hash/RandomForest.git
cd RandomForest

# 2. (Opsional tapi disarankan) buat virtual environment
python -m venv venv
source venv/bin/activate      # Linux/Mac
venv\Scripts\activate         # Windows

# 3. Install dependencies
pip install opencv-python mediapipe numpy scikit-learn joblib pillow pygame
```

> 💡 Kamu juga bisa menyimpan daftar dependency di atas ke dalam `requirements.txt` agar instalasi lebih mudah:
> ```bash
> pip freeze > requirements.txt
> ```

---

## ▶️ Cara Menjalankan

### 1. Melatih ulang model (opsional, model `pose_classifier.pkl` sudah tersedia)

```bash
python train_model.py
```

Script ini akan membaca gambar dari folder `images/`, melatih Random Forest Classifier, menampilkan akurasi dan classification report, lalu menyimpan model ke `pose_classifier.pkl`.

### 2. Menjalankan aplikasi deteksi real-time

```bash
python realtime_app.py
```

Jendela aplikasi akan terbuka menampilkan feed webcam dan reaksi "monyet" sesuai pose yang terdeteksi. Tekan tombol close pada jendela atau `Ctrl+C` di terminal untuk menghentikan aplikasi.

---

## 📊 Dataset

Dataset berupa kumpulan gambar pose yang dikelompokkan per folder kelas di dalam `images/`:

| Kelas         | Jumlah gambar |
|---------------|:-------------:|
| `idea`        | 30            |
| `other`       | 19            |
| `idle`        | 17            |
| `thinking`    | 14            |
| `shocked`     | 10            |
| `monkey_fuck` | 8             |
| `hidup_jokowi`| 4             |

Untuk menambah kelas pose baru:
1. Buat folder baru di dalam `images/` (misalnya `images/nama_pose_baru/`).
2. Isi dengan gambar-gambar pose yang sesuai (format `.jpg` / `.png`).
3. Tambahkan mapping label baru di `train_model.py`.
4. Tambahkan gambar reaksi & logika tampilannya di `realtime_app.py`.
5. Jalankan ulang `train_model.py` untuk melatih model dengan kelas baru.

---

## 🛠️ Teknologi yang Digunakan

- [Python](https://www.python.org/)
- [OpenCV](https://opencv.org/) — pengambilan & pemrosesan frame kamera
- [MediaPipe](https://developers.google.com/mediapipe) — deteksi landmark pose & tangan
- [scikit-learn](https://scikit-learn.org/) — model Random Forest Classifier
- [Tkinter](https://docs.python.org/3/library/tkinter.html) — antarmuka GUI desktop
- [Pygame](https://www.pygame.org/) — pemutaran efek suara
- [Pillow (PIL)](https://python-pillow.org/) — pemrosesan gambar untuk ditampilkan di GUI

---

## 📌 Catatan

- Kelas `monkey_fuck` (label 6) sudah didaftarkan pada proses training, namun logika tampilannya di `realtime_app.py` saat ini dinonaktifkan (dikomentari).
- Materi presentasi kelompok dan penjelasan konsep Random Forest tersedia di folder `PPT/`.

---

## 📄 Lisensi

Belum ada lisensi resmi yang ditentukan untuk project ini. Silakan tambahkan file `LICENSE` (misalnya MIT License) jika ingin membuka project ini untuk digunakan secara bebas oleh orang lain.
