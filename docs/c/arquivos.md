# Arquivos em C

- **Texto**: os bytes do arquivo representam caracteres em determinada codificação, como UTF-8 ou ASCII.
- **Binário**: os bytes seguem um formato específico e não devem ser interpretados diretamente como texto.

## Principais recursos da biblioteca padrão de C

- `FILE *`.
- `fopen()`.
- `fclose()`.
- `fread()`.
- `fwrite()`.
- `fprintf()`.
- `fscanf()`.
- `fgets()`.
- `fputs()`.
- `fseek()`.
- `ftell()`.

Esses recursos são diferentes da camada POSIX, que oferece:

- `int fd`.
- `open()`.
- `read()`.
- `write()`.
- `close()`.

Para um programa C, um arquivo é uma fonte ou destino de bytes. Em C padrão, trabalhamos com arquivos usando um ponteiro `FILE *arquivo`.

Esse `FILE *` não é o arquivo em si. Ele aponta para uma estrutura da biblioteca padrão que representa um **fluxo** (*stream*) de entrada ou saída.

Exemplo:

```c
FILE *f = fopen("arquivo.txt", "r");
```

No exemplo acima, `arquivo.txt` é o arquivo, enquanto `f` representa o fluxo usado pelo programa para acessá-lo.

O `FILE *` é usado com as funções da biblioteca C padrão e tem recursos como:

- *Buffer* interno.
- Formatação.
- Leitura por linha.
- Escrita formatada.
- Portabilidade.

Já o **descritor de arquivo** (*file descriptor*) é uma abstração POSIX de mais baixo nível, representada por um número inteiro.

## Abrindo um arquivo

Para abrir um arquivo, usamos o `fopen()`: `FILE *fopen(const char *filename, const char *mode)`.

Exemplo:

```c
FILE *f = fopen("arquivo.txt", "r");
```

Os principais modos de abertura são:

- `r`: abre para leitura. O arquivo precisa existir.
- `w`: abre para escrita. Se o arquivo existir, apaga seu conteúdo; caso contrário, cria o arquivo.
- `a`: abre para escrita no final. Se não existir, cria.
- `r+`: abre para leitura e escrita. O arquivo precisa existir.
- `w+`: abre para leitura e escrita. Apaga o conteúdo se existir.
- `a+`: abre para leitura e escrita, escrevendo no final.

Para arquivos binários, acrescentamos `b` ao modo de abertura. Exemplos: `rb`, `wb`, `ab`, `rb+` e `wb+`.

Sempre devemos verificar se a abertura foi bem-sucedida. A função `fopen()` retorna `NULL` quando não consegue abrir o arquivo.

```c
#include <stdio.h>

int main(void) {
    FILE *f = fopen("arquivo.txt", "r");

    if (f == NULL) {
        perror("Erro ao abrir arquivo");
        return 1;
    }

    fclose(f);
    return 0;
}
```

> `perror()` mostra uma mensagem associada ao valor atual de `errno`. Exemplo: `Erro ao abrir arquivo: No such file or directory`.

## Escrevendo texto em arquivo

```c
#include <stdio.h>

int main(void) {
    FILE *f = fopen("saida.txt", "w");

    if (f == NULL) {
        perror("Erro ao abrir arquivo");
        return 1;
    }

    fprintf(f, "Olá, arquivo!\n");
    fprintf(f, "Número: %d\n", 42);

    fclose(f);
    return 0;
}
```

O `fprintf()` escreve no fluxo informado. A chamada `printf("Oi\n")`, por exemplo, equivale a `fprintf(stdout, "Oi\n")`.

## Lendo texto com `fgets()`

Para ler arquivos de texto, geralmente usamos `fgets()`:

```c
#include <stdio.h>

int main(void) {
    FILE *f = fopen("nomes.txt", "r");

    if (f == NULL) {
        perror("Erro ao abrir arquivo");
        return 1;
    }

    char linha[100];

    while (fgets(linha, sizeof(linha), f) != NULL) {
        printf("Li: %s", linha);
    }

    fclose(f);
    return 0;
}
```

Para ler linhas, `fgets()` costuma ser mais adequada que `scanf()`, pois recebe o tamanho do *buffer* e pode preservar os espaços. Uma conversão `%s` de `scanf()` sem largura máxima pode ultrapassar o *buffer* e, mesmo quando limitada, termina no primeiro espaço. Assim, para a entrada `Lucas Borges`, `%s` lê apenas `Lucas`.

O `fgets()` mantém o `\n` quando ele cabe no *buffer*. Por exemplo, uma linha pode conter `"Lucas\n"`. Podemos remover esse caractere da seguinte forma:

```c
#include <stdio.h>
#include <string.h>

int main(void) {
    FILE *f = fopen("nomes.txt", "r");

    if (f == NULL) {
        perror("fopen");
        return 1;
    }

    char linha[100];

    while (fgets(linha, sizeof(linha), f) != NULL) {
        linha[strcspn(linha, "\n")] = '\0';

        printf("Nome: [%s]\n", linha);
    }

    fclose(f);
    return 0;
}
```

A função `strcspn(const char *s, const char *reject)` retorna o tamanho do trecho inicial de `s` que não contém caracteres de `reject`. Nesse exemplo, ela localiza o primeiro `\n`; se ele não existir, retorna a posição do terminador `\0`.
