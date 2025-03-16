# Model Warna pada Citra

**Soal :**
buatlah progam untuk mengubah gambar asli dengan 6 metode dibawah.

jelaskan kondisi input dan output gambar ketika menggunakan metode.
kerjakan di google collab/editor lain. dan upload pada github.  
1. citra negative
2. ctransformasi log
3. transformasi power law
4. histogram equalization
5. histogram normalization
6. konversi rgb to hsi ? jelaskan cara menentukan tresholding (opsional)

 # Jawaban

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt
import io
import base64
from google.colab import files
from skimage import exposure, color

# Upload file
def upload_file():
    uploaded = files.upload()
    for filename in uploaded.keys():
        return filename

# Load image
def load_image(filename):
    image = cv2.imread(filename)
    return cv2.cvtColor(image, cv2.COLOR_BGR2RGB)

# Citra negatif
def negative_image(image):
    return 255 - image

# Transformasi log
def log_transform(image):
    c = 255 / np.log(1 + np.max(image))
    return np.array(c * np.log(1 + image), dtype=np.uint8)

# Transformasi power law (gamma correction)
def power_law_transform(image, gamma=1.2):
    c = 255 / (np.max(image) ** gamma)
    return np.array(c * (image ** gamma), dtype=np.uint8)

# Histogram equalization
def histogram_equalization(image):
    if len(image.shape) == 3:
        image_yuv = cv2.cvtColor(image, cv2.COLOR_RGB2YUV)
        image_yuv[:, :, 0] = cv2.equalizeHist(image_yuv[:, :, 0])
        return cv2.cvtColor(image_yuv, cv2.COLOR_YUV2RGB)
    else:
        return cv2.equalizeHist(image)

# Histogram normalization
def histogram_normalization(image):
    return exposure.rescale_intensity(image, in_range='image', out_range=(0, 255)).astype(np.uint8)

# Konversi RGB ke HSI
def rgb_to_hsi(image):
    return color.rgb2hsv(image)

# Menampilkan hasil
def show_results(image):
    transformations = {
        'Original': image,
        'Negative': negative_image(image),
        'Log Transform': log_transform(image),
        'Power Law Transform': power_law_transform(image),
        'Histogram Equalization': histogram_equalization(image),
        'Histogram Normalization': histogram_normalization(image),
        'RGB to HSI': rgb_to_hsi(image)
    }
    
    plt.figure(figsize=(10, 10))
    for i, (title, img) in enumerate(transformations.items()):
        plt.subplot(3, 3, i+1)
        if title == 'RGB to HSI':
            plt.imshow(img)
        else:
            plt.imshow(img, cmap='gray' if len(img.shape) == 2 else None)
        plt.title(title)
        plt.axis('off')
    plt.show()

# Main program
filename = upload_file()
image = load_image(filename)
show_results(image)

```
**Output :** <br>
![download](https://github.com/user-attachments/assets/7e97126d-86fe-4037-a2c1-ac98ffeb778b)

**Penjelasan :** <br>
- Citra negatif membalik warna dan intensitas gambar, membuat area terang menjadi gelap dan sebaliknya. 
- Transformasi log meningkatkan detail di area gelap dengan memperluas rentang intensitas. 
- Transformasi power law (gamma correction) menyesuaikan pencahayaan gambar; gamma > 1 membuat gambar lebih gelap, sedangkan gamma < 1 membuatnya lebih terang.
- Histogram equalization dan histogram normalization meningkatkan kontras dengan mendistribusikan intensitas lebih merata, membuat detail lebih terlihat.
- Konversi RGB ke HSI mengubah format warna agar lebih sesuai untuk analisis berbasis warna.
  
Untuk thresholding, gambar dikonversi ke grayscale, lalu ditentukan nilai ambang (misalnya 128). Piksel di atas threshold menjadi putih, sementara yang di bawahnya menjadi hitam, menghasilkan gambar biner yang berguna dalam segmentasi dan deteksi objek.

