# Pillow

O Pillow é uma biblioteca Python usada para trabalhar com imagens.

## Redimensionamento de imagens

```python
from pathlib import Path
from PIL import Image, ImageOps

ROOT_FOLDER = Path(__file__).parent
ORIGINAL_IMAGE = ROOT_FOLDER / "original.jpg"
NEW_IMAGE = ROOT_FOLDER / "new.jpg"

with Image.open(ORIGINAL_IMAGE) as source_image:
    # Aplica a orientação EXIF aos pixels e remove essa marca dos metadados.
    oriented_image = ImageOps.exif_transpose(source_image)
    exif = oriented_image.getexif()

    # Recorta sem distorcer para produzir uma imagem de 256 por 256 pixels.
    resized_image = ImageOps.fit(
        oriented_image.convert("RGB"),
        (256, 256),
        method=Image.Resampling.LANCZOS,
    )

    resized_image.save(
        NEW_IMAGE,
        exif=exif,
        optimize=True,
        quality=85,
    )
```

`ImageOps.fit()` preserva a proporção da imagem e recorta o excesso para preencher exatamente o tamanho solicitado. Para apenas limitar as dimensões sem recortar, podemos usar `ImageOps.contain()` ou `thumbnail()`.
