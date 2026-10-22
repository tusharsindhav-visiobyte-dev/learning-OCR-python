Optical Character Recognition (OCR) in Python lets you extract editable, searchable text from images, scanned documents, and PDFs.

The two main tools to know are:

1. **Tesseract (`pytesseract`)**: Best for standard text, offline processing, and quick CPU-based extraction.
2. **EasyOCR**: Best for complex backgrounds, multi-language text, scene text (signs, license plates), and GPU acceleration.

---

## 1. Setup & Installation

Before running Python code, Tesseract requires installing its system binary.

### Installing Dependencies

```bash
pip install pytesseract easyocr pillow opencv-python

```

### System Binary Requirement for Tesseract

* **Windows**: Download and run the installer from the [UB-Mannheim Tesseract Wiki](https://www.google.com/search?q=https://github.com/UB-Mannheim/tesseract/wiki). Add `C:\Program Files\Tesseract-OCR` to your system PATH.
* **macOS**: `brew install tesseract`
* **Linux (Ubuntu/Debian)**: `sudo apt-get install tesseract-ocr`

---

## 2. Basic OCR with `pytesseract`

`pytesseract` acts as a Python wrapper around the Tesseract engine.

```python
import pytesseract
from PIL import Image

# If Tesseract isn't in your PATH (common on Windows), specify executable location:
# pytesseract.pytesseract.tesseract_cmd = r'C:\Program Files\Tesseract-OCR\tesseract.exe'

# Load image
img = Image.open("sample_text.jpg")

# Extract text
extracted_text = pytesseract.image_to_string(img)

print("--- Extracted Text ---")
print(extracted_text)

```

---

## 3. Advanced OCR with EasyOCR

EasyOCR relies on PyTorch under the hood. It works out of the box without needing external C++ binaries and delivers higher accuracy on styled or low-contrast text.

```python
import easyocr

# Initialize reader (specify target languages, e.g., 'en' for English)
reader = easyocr.Reader(['en'], gpu=False)  # Set gpu=True if CUDA is available

# Read text (returns bounding box coordinates, text, and confidence score)
results = reader.readtext('sample_text.jpg')

for bbox, text, prob in results:
    print(f"Detected: '{text}' (Confidence: {prob:.2f})")

```

---

## 4. Improving Accuracy via Image Preprocessing

Raw images often contain noise, uneven lighting, or low contrast. Using OpenCV to clean up images before feeding them to OCR significantly improves accuracy.

1. **Convert to Grayscale:** Removes unnecessary color noise.
Simplifies the image by converting 3-channel RGB to 1-channel grayscale.

```python
import cv2

image = cv2.imread('noisy_image.jpg')
gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)

```


2. **Apply Thresholding (Binarization):** Separates text from background.
Converts pixels to either pure black or pure white using Otsu's thresholding.

```python
_, binary_img = cv2.threshold(gray, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)
cv2.imwrite('clean_image.png', binary_img)

```


3. **Pass Clean Image to OCR:** Extract with higher precision.
```python
text = pytesseract.image_to_string(binary_img)
print(text)

```


---

## Direct Comparison

| Feature | `pytesseract` | EasyOCR |
| --- | --- | --- |
| **Setup** | Requires separate system binary installation | `pip install` only (auto-downloads models) |
| **Speed** | Extremely fast on CPU | Slower on CPU, fast on GPU |
| **Accuracy** | High on clean, scanned documents | High on natural scene text, rotated/curved text |
| **Languages** | 100+ (requires downloading `.traineddata` files) | 80+ supported out of the box |