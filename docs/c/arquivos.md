# Arquivos em C

- **Texto**: os bytes do arquivo devem ser interpretados segundo uma codificação de caracteres (por exemplo, ASCII)
- **Binário**: os bytes são interpretados por outros programas

## `stdio.h`

Biblioteca padrão de entrada e saída da linguagem C.

```c
#include <stdio.h>

int main(int argc, char *argv[]) {
    FILE *fp;
    fp = fopen("exemplo.bin", "wb");
    if (fp == NULL)
        printf("Erro na abertura do arquivo.");
    fclose(fp);
    return 0;
}
```
