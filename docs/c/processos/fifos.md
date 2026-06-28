# FIFOs

Uma FIFO (*first in, first out*, ou “primeiro a entrar, primeiro a sair”) também é chamada de **named pipe** (*pipe* nomeado). Um *pipe* anônimo costuma ser suficiente para a comunicação entre processos relacionados, como pai e filho. Quando programas independentes precisam se comunicar, podemos usar uma FIFO, que possui um nome no sistema de arquivos:

```text
/tmp/my_fifo
```

Qualquer processo que tenha permissão pode abrir `/tmp/my_fifo` para leitura ou escrita.

A FIFO não é um arquivo comum. Ela aparece no sistema de arquivos, mas não armazena os dados de forma persistente. Exemplo:

```bash
mkfifo canal

ls -l canal
```

A saída do comando acima será semelhante a `prw-rw-r-- 1 lcsgborges lcsgborges 0 Jun 28 13:34 canal`. O `p` no início indica que esse arquivo especial é um *pipe*. A FIFO funciona como um ponto de encontro: os dados passam por ela, mas não ficam armazenados nela.

Para fazer um teste rápido, execute em um terminal:

```bash
mkfifo canal

cat canal
```

O `cat` fica bloqueado enquanto espera que outro processo escreva. Em outro terminal, execute:

```bash
echo "Olá, FIFO!" > canal
```

Isso já é **IPC** (*interprocess communication*, ou comunicação entre processos): dois processos independentes se comunicaram por meio da FIFO.

## Diferença entre *pipe* e FIFO

### *Pipe*

- É criado com `pipe()`.
- Não tem nome no sistema de arquivos.
- Normalmente é usado entre processos relacionados, como pai e filho.
- Deixa de existir quando todos os descritores associados a ele são fechados.

### FIFO

- É criada com o comando `mkfifo` ou com a função C `mkfifo()`.
- Tem nome no sistema de arquivos.
- Pode ser aberta por processos independentes.
- Sua entrada no sistema de arquivos continua existindo até ser removida com `rm` ou `unlink()`.

Depois que a FIFO é aberta, sua utilização é semelhante à de um *pipe* anônimo: usamos `read()`, `write()` e `close()`.

## Criando uma FIFO pelo terminal

```bash
mkfifo /tmp/minha_fifo

# Verifica se a FIFO foi criada.

ls -l /tmp/minha_fifo

# Remove a FIFO.

rm /tmp/minha_fifo
```

## Criando uma FIFO em C

A função POSIX usada para criar uma FIFO é `mkfifo()`. Exemplo:

```c
mkfifo("canal", 0666);
```

Aqui vale um detalhe importante sobre a base de um literal numérico em C:

| Prefixo | Base | Valor equivalente a `0666` |
| :-----: | :--: | :-------------------------: |
| nenhum | decimal | `438` |
| `0` | octal | `0666` |
| `0x` ou `0X` | hexadecimal | `0x1B6` |
| `0b` ou `0B` (C23 ou extensão GNU) | binário | `0b110110110` |

Quando passamos `0666`, usamos um valor octal para solicitar permissão de leitura e escrita para o proprietário, o grupo e os demais usuários (`rw-rw-rw-`). A `umask` do processo pode remover algumas dessas permissões. Por exemplo, com `umask 0002`, o modo efetivo será `0664` (`rw-rw-r--`).

Exemplo completo:

```c
#include <stdio.h>
#include <sys/stat.h>

int main(void) {
    if (mkfifo("canal", 0666) == -1) {
        perror("mkfifo");
        return 1;
    }
    printf("FIFO criada: canal.\n");
    return 0;
}
```

### Programa leitor

O programa `leitor.c` cria a FIFO, caso ela ainda não exista, e lê os dados recebidos:

```c
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>
#include <sys/stat.h>
#include <errno.h>

int main(void) {
    const char *fifo_path = "canal";

    if (mkfifo(fifo_path, 0666) == -1 && errno != EEXIST) {
        perror("mkfifo");
        return 1;
    }

    printf("Leitor: aguardando escritor...\n");
    fflush(stdout);

    int fd = open(fifo_path, O_RDONLY);

    if (fd == -1) {
        perror("open");
        return 1;
    }

    printf("Leitor: escritor conectado.\n");
    fflush(stdout);

    char buffer[128];
    ssize_t n;

    while ((n = read(fd, buffer, sizeof(buffer))) > 0) {
        size_t total = 0;

        while (total < (size_t)n) {
            ssize_t written = write(
                STDOUT_FILENO,
                buffer + total,
                (size_t)n - total
            );

            if (written == -1) {
                perror("write");
                close(fd);
                return 1;
            }

            total += (size_t)written;
        }
    }

    if (n == -1) {
        perror("read");
        close(fd);
        return 1;
    } else {
        printf("\nLeitor: escritor fechou a FIFO.\n");
    }

    close(fd);
    return 0;
}
```

### Programa escritor

O programa `escritor.c` também tenta criar a FIFO. Assim, qualquer um dos dois programas pode ser iniciado primeiro:

```c
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>
#include <string.h>
#include <sys/stat.h>
#include <errno.h>

int main(void) {
    const char *fifo_path = "canal";

    if (mkfifo(fifo_path, 0666) == -1 && errno != EEXIST) {
        perror("mkfifo");
        return 1;
    }

    printf("Escritor: aguardando leitor...\n");
    fflush(stdout);

    int fd = open(fifo_path, O_WRONLY);

    if (fd == -1) {
        perror("open");
        return 1;
    }

    printf("Escritor: leitor conectado.\n");

    const char *msg = "Mensagem enviada pela FIFO.\n";

    if (write(fd, msg, strlen(msg)) == -1) {
        perror("write");
        close(fd);
        return 1;
    }

    close(fd);
    return 0;
}
```

Os exemplos tratam `EEXIST` como indicação de que a FIFO já foi criada. Em código de produção, devemos verificar com `lstat()` e `S_ISFIFO()` se o caminho existente realmente representa uma FIFO antes de abri-lo, principalmente em diretórios compartilhados como `/tmp`.

### Comportamento de bloqueio

Por padrão, abrir uma FIFO somente para leitura com `open("canal", O_RDONLY)` bloqueia a execução até que um escritor a abra. Da mesma forma, `open("canal", O_WRONLY)` bloqueia a execução até que um leitor abra a FIFO. Com a opção `O_NONBLOCK`, a abertura para leitura retorna imediatamente, mesmo sem escritores, enquanto a abertura para escrita falha com `ENXIO` quando não há leitores.

## Exemplo real: servidor de comandos por FIFO

```c
// servidor_fifo.c

#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>
#include <sys/stat.h>
#include <string.h>
#include <errno.h>

int main(void) {
    const char *fifo_path = "/tmp/app_comandos";

    if (mkfifo(fifo_path, 0666) == -1 && errno != EEXIST) {
        perror("mkfifo");
        return 1;
    }

    printf("Servidor iniciado. FIFO: %s.\n", fifo_path);
    printf("Envie comandos com: echo status > %s\n", fifo_path);

    while (1) {
        FILE *fifo = fopen(fifo_path, "r");

        if (fifo == NULL) {
            perror("fopen");
            return 1;
        }

        char buffer[128];

        while (fgets(buffer, sizeof(buffer), fifo) != NULL) {
            buffer[strcspn(buffer, "\n")] = '\0';

            if (strcmp(buffer, "status") == 0) {
                printf("STATUS: servidor rodando.\n");
            } else if (strcmp(buffer, "reload") == 0) {
                printf("RELOAD: recarregando configuração.\n");
            } else if (strcmp(buffer, "stop") == 0) {
                printf("STOP: encerrando servidor.\n");
                fclose(fifo);
                unlink(fifo_path);
                return 0;
            } else {
                printf("Comando desconhecido: %s.\n", buffer);
            }
        }
        fclose(fifo);
    }
}
```

> Uma FIFO normalmente é usada como um canal unidirecional. Para uma comunicação bidirecional, podemos usar duas FIFOs, uma para cada direção, ou um **soquete de domínio Unix** (*Unix domain socket*).

## Quando usar FIFO

Use uma FIFO quando:

- Os processos estão na mesma máquina.
- A comunicação é simples.
- Os processos não são pai e filho.
- A comunicação consiste basicamente em texto ou em um fluxo simples de bytes.
- Queremos testar IPC facilmente pelo terminal.

Bons exemplos de uso:

- Um canal local de comandos.
- Um *script* que envia comandos a um serviço simples.
- Um *pipeline* manual entre processos independentes.
- A transmissão de *logs* ou eventos simples.
- Um protótipo de IPC.

**Não** use uma FIFO quando:

- A aplicação exige comunicação bidirecional complexa.
- É necessária uma autenticação forte.
- É necessário coordenar vários clientes.
- A comunicação precisa ocorrer pela rede.
- A aplicação exige mensagens estruturadas com respostas.

Em resumo, uma FIFO é útil quando processos independentes precisam trocar dados simples na mesma máquina usando um canal identificado por um nome conhecido no sistema de arquivos.
