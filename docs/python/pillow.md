# Pillow

O Pillow é uma biblioteca Python usada para trabalhar com imagens.

## Redimensionamento de imagens

```python
from pathlib import Path
from PIL import Image

ROOT_FOLDER = Path(__file__).parent
ORIGINAL_IMAGE = ROOT_FOLDER / 'original.jpg'
NEW_IMAGE = ROOT_FOLDER / 'new.jpg'

# pil_img = Image.open(ORIGINAL_IMAGE) ou com gerenciador de contexto:
with Image.open(ORIGINAL_IMAGE) as pil_img:
    width, height = pil_img.size
    exif = pil_img.info.get('exif')  # Metadados EXIF da imagem

    # Nova imagem = 256 x 256
    new_width = 256
    new_height = 256

    new_img = pil_img.resize(size=(new_width, new_height))
    new_img.save(NEW_IMAGE, optimize=True, quality=75)
```
