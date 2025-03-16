![download (1)](https://github.com/user-attachments/assets/b3497a3a-41b4-4e6b-8afd-688678c1226b)# Model Warna pada Citra

**Soal :**<br>
- buatlah progam untuk mengubah gambar asli dengan 6 metode dibawah.
- jelaskan kondisi input dan output gambar ketika menggunakan metode.
- kerjakan di google collab/editor lain. dan upload pada github.  

1. citra negative
2. ctransformasi log
3. transformasi power law
4. histogram equalization
5. histogram normalization
6. konversi rgb to hsi ? jelaskan cara menentukan tresholding (opsional)

 # Jawaban

```python
from google.colab import files
import cv2
import numpy as np
import matplotlib.pyplot as plt

# Upload image file
uploaded = files.upload()

# Membaca gambar yang diunggah
image_path = list(uploaded.keys())[0]
image = cv2.imread(image_path)

# Cek apakah gambar berwarna atau grayscale
if len(image.shape) == 3:
    image_gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
else:
    image_gray = image

# Fungsi untuk menampilkan beberapa gambar dalam grid 2x4
def show_grid_images(images, titles, cmap_options):
    plt.figure(figsize=(12, 8))  # Atur ukuran lebih besar agar lebih rapi
    
    for i in range(len(images)):
        plt.subplot(2, 4, i + 1)  # 2 baris, 4 kolom
        plt.imshow(images[i], cmap=cmap_options[i])
        plt.title(titles[i], fontsize=10)
        plt.axis('off')

    plt.tight_layout()  # Atur agar gambar tidak saling tumpang tindih
    plt.show()

# Transformasi Citra
negative_image = 255 - image_gray
log_image = np.uint8(255 / np.log(1 + np.max(image_gray)) * np.log(1 + image_gray.astype(np.float32)))
power_law_image = np.array(255 * (image_gray / 255) ** 0.5, dtype='uint8')
equalized_image = cv2.equalizeHist(image_gray)
normalized_image = cv2.normalize(image_gray, None, 0, 255, cv2.NORM_MINMAX)
image_hsi = cv2.cvtColor(cv2.cvtColor(image, cv2.COLOR_BGR2RGB), cv2.COLOR_RGB2HSV)

# Thresholding (Otsu)
_, threshold_image = cv2.threshold(image_hsi[:, :, 2], 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)

# Menampilkan dalam grid 2x4
show_grid_images(
    [cv2.cvtColor(image, cv2.COLOR_BGR2RGB), image_gray, negative_image, log_image, 
     power_law_image, equalized_image, normalized_image, threshold_image], 
    ['Gambar Asli (RGB)', 'Gambar Asli (Grayscale)', 'Citra Negative', 'Transformasi Log', 
     'Transformasi Power Law', 'Histogram Equalization', 'Histogram Normalization', 'Thresholding Otsu'],
    [None, 'gray', 'gray', 'gray', 'gray', 'gray', 'gray', 'gray']
)

```
**Output :** <br>

![download (1)](https://github.com/user-attachments/assets/b0ee4866-511e-4602-b8d4-041046e07d10)

**Penjelasan :** <br>
1. Citra Negatif
Input: Gambar grayscale dengan rentang intensitas 0-255.
Output: Inversi nilai intensitas, sehingga bagian terang menjadi gelap dan sebaliknya. Cocok untuk analisis kontras tinggi, seperti mendeteksi tepi objek.

2. Transformasi Log
Input: Gambar grayscale dengan variasi intensitas yang tinggi.
Output: Peningkatan detail pada area yang lebih gelap, berguna untuk menyoroti bagian yang sebelumnya tidak terlihat jelas.

3. Transformasi Power Law (Gamma Correction)
Input: Gambar grayscale yang terlalu terang atau terlalu gelap.
Output: Penyesuaian kontras dengan faktor gamma, di mana nilai gamma <1 mencerahkan dan >1 menggelapkan gambar.

4. Histogram Equalization
Input: Gambar dengan distribusi intensitas yang kurang merata.
Output: Distribusi intensitas yang lebih seragam, meningkatkan detail di area terang dan gelap secara otomatis.

5. Histogram Normalization
Input: Gambar dengan rentang intensitas yang terbatas.
Output: Skala intensitas dinormalisasi dalam rentang 0-255 untuk meningkatkan keterbacaan visual.

6. Konversi RGB ke HSI (Hue Component)
Input: Gambar RGB.
Output: Komponen Hue dari warna, yang berguna untuk analisis berbasis warna tanpa dipengaruhi intensitas cahaya.
