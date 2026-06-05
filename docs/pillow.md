# Pillow

O Pillow é uma biblioteca Python feita para trabalhar com imagens

## Redimensionamento de imagens

```python
from pathlib import Path
from PIL import Image

ROOT_FOLDER = Path(__file__).parent
ORIGINAL_IMAGE = ROOT_FOLDER / 'original.jpg'
NEW_IMAGE = ROOT_FOLDER / 'new.jpg'

# pil_img = Image.open(ORIGINAL_IMAGE) -> Podemos usar gerenciador de contexto aqui:
with Image.open(ORIGINAL_NAME) as pil_img:
    widht, height = pil_img.size
    exif = pil_img.info['exif'] # Caso precisamos das informações (se não precisamos disso, podemos ignorar)
    
    # nova imagem = 512 x 512
    new_widht = 256
    new_height = 256

    new_img = pil_img.resize(size=(new_widht, new_height))
    new_img.save(NEW_IMAGE, optimize=True, quality=75)
```
