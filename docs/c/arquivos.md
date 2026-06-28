# Arquivos em C

- **Texto**: os bytes do arquivo devem ser interpretados segundo uma codificação de caracteres (por exemplo, ASCII)
- **Binário**: os bytes são interpretados por outros programas

## Principais comandos da biblioteca padrão C

- `FILE *`
- `fopen()`
- `fclose()`
- `fread()`
- `fwrite()`
- `fprintf()`
- `fscanf()`
- `fgets()`
- `fputs()`
- `fseek()`
- `ftell()`

Isso é diferente da camanda POSIX/Linux que usa:

- `int fd`
- `open()`
- `read()`
- `write()`
- `close()`

Para um programa C, um arquivo é uma fonte ou destino de bytes. Em C padrão, trabalhamos com arquivos usando um ponteiro `FILE *arquivo`.

Esse `FILE *` não é o arquivo em si. Ele é uma estrutura da biblioteca C que representa um **stream**, ou seja, um fluxo de entrada e saída.

Exemplo:

```c
FILE *f = fopen("arquivo.txt", "r");
```

No exemplo acima, `dados.txt` é o arquivo em si, já o `f` é o stream usado pelo programa para acessar esse arquivo.

O `FILE *` é usado com as funções da biblioteca C padrão e tem recursos como:

- buffer interno
- formatação
- leitura por linha
- escrita formatada
- portabilidade

Já o **file descriptor** é mais baixo nível e é apenas um número inteiro.

## Abrindo um arquivo

Para abrir um arquivo, usamos o `fopen()`: `FILE *fopen(const char *filename, const char *mode)`.

Exemplo:

```c
FILE *f = fopen("arquivo.txt", "r");
```

Os principais modos de abertura são:

- `r`: abre para leitura. O arquivo precisa existir.
- `w`: abre para escrita. Se o arquivo existir, apaga o conteúdo dele, se não existir, cria.
- `a`: abre para escrita no final. Se não existir, cria.
- `r+`: abre para leitura e escrita. O arquivo precisa existir.
- `w+`: abre para leitura e escrita. Apaga o conteúdo se existir.
- `a+`: abre para leitura e escrita, escrevendo no final.

Para arquivo binários, usamos `b` ao final, exemplo: `rb`, `wb`, `ab`, `rb+`, `wb+`.

É sempre bom verificar se a abertura deu certo. Se o arquivo não existir, `fopen()` retorna `NULL`.

```c
#include <stdio.h>

int main() {
    FILE *f = fopen("arquivo.txt", "r");

    if (f == NULL) {
        perror("Erro ao abrir arquivo");
        return 1;
    }
    
    fclose(f);
    return 0;
}
```

> `perror()` mostra o motivo do erro, exemplo: `Erro ao abrir arquivo: No such file or directory`.

## Escrevendo texto em arquivo

```c
#include <stdio.h>

int main() {
    FILE *f = fopen("saida.txt", "w");

    if (f == NULL) {
        perror("Erro ao abrir arquivo");
        return 1;
    }

    fprintf(f, "Olá, arquivo\n");
    fprintf(f, "Número: %d\n", 42);

    fclose(f);
    return 0;
}
```

O `fprintf()` escreve no arquivo em vez de escrever no terminal. O `printf()` é parecido com `fprintf(stdout, "Oi\n")`.

## Lendo texto com `fgets()`

Para ler arquivos de texto, geralmente usamos `fgets()`:

```c
#include <stdio.h>

int main() {
    FILE *f = fopen("nomes.txt", "r");

    if (f == NULL) {
        perror("Erro ao abrir arquivo");
        return 1;
    }

    char linha[100];

    while (fgets(linha, sizeof(linha), f) != NULL) {
        printf("Li: %s\n", linha);
    }

    fclose(f);
    return 0;
}
```

O `fgets()` é melhor que o `scanf()` porque lê uma linha inteira com limite de tamanho. Ele evita passar do tamanho do buffer. Já funções como `scanf()` podem ser perigosas e também para no primeiro espaço, se tiver "Lucas Borges", `%s` só lê "Lucas".

O `fgets()` mantém o `\n` se houver espaço no buffer. Exemplo: "Lucas\n". Para remover:

```c
#include <stdio.h>

int main() {
    FILE *f = fopen("nomes.txt", "r");

    if (f == NULL) {
        perror("fopen");
        return 1;
    }

    char linha[100];

    while(fgets(linha, sizeof(linha), f) != NULL) {
        linha[strcspn(linha, "\n")] = '\0';

        printf("Nome: [%s]\n", linha);
    }

    fclose(f);
    return 0;
}
```

O `strcspn(const char *s, const char *reject)` conta quantos caracteres iniciais de `s` retorna a primeira posição que aparece `reject`.
