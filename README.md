
# Report On The Investigation Into<br>Russian Interference In The<br>2016 Presidential Election

## Volume 1 to 11

### Special Counsel Robert S. Mueller, III

### 2019

This document was generated with the [Tesseract Optical Character Recognition engine](https://github.com/tesseract-ocr). Because of the poor quality of the original pdf and the numerous redacted sections, the text contains many errors and should not be regarded as the definitive text. This document should be used by those who wish to process the text at scale. The difficulties associated with the OCR process also signals the failure of the Justice Department to accommodate citizens using screen readers to access this document.


```python
from wand.image import Image
from PIL import Image as PI
import pyocr
import pyocr.builders
import io
import os
from PyPDF2 import PdfFileWriter, PdfFileReader
```


```python
#splits full report into pages
inputpdf = PdfFileReader(open("report.pdf", "rb"))
for i in range(inputpdf.numPages):
    output = PdfFileWriter()
    output.addPage(inputpdf.getPage(i))
    with open(f"pages/report-page{i:03}.pdf", "wb") as outputStream:
        output.write(outputStream)
```


```python
#reports list of images
file_list = []
for path, subdirs, files in os.walk("pages"):  # change depending on system
    for file in files:
        a = os.path.join(file)
        file_list.append(a)
file_list = sorted(file_list)
```


```python
tool = pyocr.get_available_tools()[0]
lang = tool.get_available_languages()[0]
```


```python
image = []
text = []
```


```python
for file in file_list:
    pdf = Image(filename="pages/"+file, resolution=300)
    jpg = pdf.convert('jpeg')
    img_page = Image(image=jpg)
    image.append(img_page.make_blob('jpeg'))
```


```python
for img in image: 
    txt = tool.image_to_string(
        PI.open(io.BytesIO(img)),
        lang=lang,
        builder=pyocr.builders.TextBuilder())
    text.append(txt)
```


```python
with open("report.txt", "w") as file:
    for page in text:
        file.write(page)
```
