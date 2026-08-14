# Basic-Image-Processing
Used publicly available CT scan files from The Cancer Imaging Archive (https://www.cancerimagingarchive.net/access-data/) to practice converting standard medical imaging files (DICOM) into mutable arrays to enhance features on image such as bone 

```
import pydicom as dicom
from matplotlib.pyplot import gray
from pydicom import dcmread
import numpy as np

import matplotlib.pyplot as plt

file_path = r"C:\Users\zhaid\OneDrive\Pycharm Projects\DICOM\CT Data Files\stageii_colorectal_ct\StageII-Colorectal-CT-001\12095\8d622732-746b-47dd-b5b8-41b04c5bb0eb.dcm"

img_data = dicom.dcmread(file_path)

img_array = img_data.pixel_array

slope = getattr(img_data, "RescaleSlope")
intercept = getattr(img_data, "RescaleIntercept")
hounsfield_array = (img_array * slope) + intercept

def IsolateBone(hf_array):

    arr = hf_array.copy()
    hf_max = hf_array.max()
    for row in range(arr.shape[0]):
        for col in range(arr.shape[1]):
            hu_unit = arr[row, col]
```
## 
            if (hu_unit < 1000) and (hu_unit > 300): 
                 hf_array[row,col] += 150
                 if (arr[row, col] < hf_max):
                     arr[row, col] = hf_max
            else:
                arr[row,col] = 0

    pix_array = (arr- intercept) / slope
    normalize_arr = (pix_array - pix_array.min()) / (pix_array.max() - pix_array.min())
    final_array = np.uint8(normalize_arr * 255)
    plt.figure(1)
    plt.imshow(final_array, cmap="gray")
    plt.title("Isolating Bone")


plt.figure(2)
plt.imshow(img_array, cmap="gray")
plt.title("Original Image")

print(IsolateBone(hounsfield_array))

plt.show()
