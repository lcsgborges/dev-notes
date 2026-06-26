# Arquivos em C

- **Texto**: os bytes do arquivo devem ser interpretados por uma tabela de caracteres (geralmente ASCII)
- **Binário**: bytes interpretados por outros programas

## `stdio.h`

Biblioteca padrão C de entrada e saída

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
