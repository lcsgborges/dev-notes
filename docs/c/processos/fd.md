# File Descriptors

**Tudo em Unix é um arquivo!** Sabendo disso, temos que um processo não manipula recursos externos como arquivos, terminal, pipes e sockets. Ele recebe um **número inteiro** e esse número inteiro é o **File Descriptor (fd)** e como ele podemos acessar esses recursos (arquivos).

Um file descriptor é um número inteiro usado por um processo para se referir a um recurso aberto no kernel.

Logo, temos:

```c
int fd = open("arquivo.txt", O_RONLY);
```

Suponha que o sistema retorne `fd = 10`. Isso significa que dentro da tabela de descritores deste processo, a posição 10 aponta para um recurso aberto controlado pelo kernel. O `10` não é o arquivo, o `10` é uma **referência**.

## A ideia histórica: "Everything is a file"

No Unix, uma das grandes ideias foi criar uma interface uniforme para muitos recursos. Em vez de cada coisa ter uma API completamente diferente, muitas operações são feitas com `read()`, `write()` e `close()`. Por exemplo, podemos usar `read()` para:

- ler de um arquivo
- ler do teclado
- ler de um pipe
- ler de um socket

E podemos usar o `write()` para:

- escrever em um arquivo
- escrever no terminal
- escrever em um pipe
- escrever em um socket

Todo processo já nasce com 3 file descriptors:

- 0 (stdin): entrada padrão (teclado)
- 1 (stdout): saída padrão (terminal)
- 2 (stderr): erro padrão (terminal)

Dessa forma, quando fazemos um `printf("Olá\n")`, no fundo, essa saída acaba indo para o file descriptor 1, algo conceitualmente parecido com: `write(1, "Olá\n", 4)`.

`printf()` é uma função da biblioteca padrão C, já `write()` é uma system call. `printf()` é mais confortável, formatado e usa buffer, `write()` é mais baixo nível, trabalha diretamente com bytes e file descriptors.

## POSIX

**POSIX** (Portable Operating System Interface) é um padrão que define uma interface de programação para sistemas Unix-like. Ele especifica funções como:

- `open()`
- `read()`
- `write()`
- `close()`
- `pipe()`
- `fork()`
- `exec()`

### stdio.h vs POSIX

| Operações sobre arquivos | stdio.h | POSIX |
| :----------------------: | :-----: | :---: |
| Abrir | fopen() | open() |
| Fechar | fclose() | close() |
| Ler | fread(), fscanf(), getc() | read() |
| Escrever | fwrite(), fprintf(), putc() | write() |
| Outros | fseek(), rewind() | mmap(), lseek(), poll() |
| Vantagens | Bufferizado, poderoso, sempre disponível | Simples, rápido, baixo nível |
| Desvantagens | Buffering pode ser confuso, pois adiciona overheads | Somente para UNIX |

### Modos POSIX

| Modo | Significado |
| :--: | :---------: |
| O_RDONLY | Abre o arquivo somente para leitura |
| O_WRONLY | Abre o arquivo somente para escrita |
| O_RDWR | Abre o arquivo para leitura e escrita |
| O_APPEND | Abre o arquivo para escrita ao final do arquivo |
| O_CREAT | Crie o arquivo se ele não existe |
| Outros | [https://man7.org/linux/man-pages/man2/open.2.html](https://man7.org/linux/man-pages/man2/open.2.html) |

## Arquivos

Abrindo um arquivo de verdade:

```c
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>

int main() {
    // O terceiro argumento são as permissões do arquivo
    int fd = open("teste.txt", O_CREAT | O_WRONLY | O_TRUNC, 0644);

    if (fd == -1) {
        perror("open");
        return 1;
    }
    printf("File descriptor = %d\n", fd);
    close(fd);
    return 0;
}
```

### O offset do arquivo

Qual lemos ou escrevemos em um arquivo, existe uma posição atual, exemplo:

```text
arquivo: ABCDEFGHIJ
offset: 0
```

Se fizermos `read(fd, buffer, 3)`, lê `ABC` e o *offset* avança para 3:

```text
arquivo: ABCDEFGHIJ
offset: 3
```

Se ler mais 2, teremos `DE` e o *offset* vira 5. Esse *offset* fica na **open file description**, dentro do kernel.

Podemos mudar o offset manualmente com `lseek()`:

```c
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>

int main() {
    int fd = open("dados.txt", O_RDONLY);

    if (fd == -1) {
        perror("open");
        return 1;
    }

    char c;

    read(fd, &c, 1);
    printf("Primeiro char: %c\n", c);

    lseek(fd, 0, SEEK_SET);

    read(fd, &c, 1);
    printf("Depois do lseek: %c\n", c);

    close(fd);
    return 0;
}
```

Arquivos de textos comuns aceitam `lseek()`, mas pipe e socket geralmente não aceitam, pois arquivo tem posição e pipe e sockets são fluxos (bytes chegando ao longo do tempo).

> obs: Quando fazemos `close(fd)`, removemos essa entrada da tabela de descritores do processo, mas o file descriptor (int) ainda existe, ele só é liberado quando não existe mais nenhuma referência para ele.

Se não fecharmos as referências aos files descriptors, ele pode estourar um erro `Too many open files`. Geralmente o limite da tabela de descritores é 1024 (`ulimit -n`).

### EOF

A chamada `read()` retorna quantidade de bytes lidos:

- > 0: quantidade de bytes lidos
- = 0: fim do arquivo, EOF
- -1: erro

Exemplo de código lendo até o fim do arquivo:

```c
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>

int main() {
    int fd = open("dados.txt", O_RDONLY);

    if (fd == -1) {
        perror("open");
        return 1;
    }

    char buffer[1024];
    ssize_t n;

    while((n = read(fd, buffer, sizeof(buffer))) > 0) {
        // Escrever no terminal (fd = 1)
        if (write(1, buffer, n) == -1) {
            perror("write");
            close(fd);
            return 1;
        }
    }

    // Caso não consiga ler o arquivo
    if (n == -1) {
        perror("read");
        close(fd);
        return 1;
    }

    close(fd);
    return 0;
}
```

Esse programa é uma versão mínima do comportamento do `cat`.
