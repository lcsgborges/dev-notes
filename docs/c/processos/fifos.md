# FIFOs

Também chamada de **Named Pipes**. Quando dois processos se comunicam via pai-filho, o **pipe** basta, mas quando dois programas que não têm parentesco precisam se comunicar, usamos a FIFO (First In First Out). A FIFO resolve esse problema criando um nome no sistema de arquivos:

```bash
/tmp/my_fifo
```

Agora qualquer processo que tenha permissão pode abrir `/tmp/my_fifo` para ler/escrever.

A FIFO não é um arquivo comum. Ela aparece no sistema de arquivos, mas não armazena dados como um arquivo normal. Exemplo:

```bash
mkfifo canal

ls -l canal
```

A saída gerada do comando acima será algo parecido com `prw-rw-r-- 1 lcsgborges lcsgborges 0 Jun 28 13:34 canal`. Esse `p` indica `pipe`. A FIFO é um ponto de encontro, os dados passam por ela, mas não ficam gravados nela.

Teste rápido terminal:

```bash
mkfifo canal

cat canal
```

O `cat` fica parado esperando alguém escrever. Em outro terminal:

```bash
echo "Olá FIFO" > canal
```

Isso já é **IPC**: dois processos independentes se comunicaram usando a FIFO.

## Diferença entre Pipe e FIFO

### Pipe

- Criado com `pipe()`
- Não tem nome no filesystem
- Normalmente usado entre pai e filho
- Desaparece quando os descritores são fechados

### FIFO

- Criada com `mkfifo` (shell) ou `mkfifo()` (lib C)
- Tem nome no filesystem
- Pode ser aberta por processos independentes
- Continua existindo até ser removida com `rm` ou `unlink`

A mecânica depois que abre é parecida: `read()`, `write()`, `close()`

## Criando FIFO pelo terminal

```bash
mkfifo /tmp/minha_fifo

# Verificando se deu certo:

ls -l /tmp/minha_fifo

# Removendo:

rm /tmp/minha_fifo
```

## Criando FIFO em C

A função POSIX é:

```c
mkfifo("canal", 0666);
```

Aqui vale um detalhe importante sobre a base de um literal numérico em C:

| Prefixo | Base | Exemplo |
| :-----: | :--: | :-----: |
| (nenhum) | decimal | 666 |
| 0 | octal | 0666 |
| 0x ou 0X | hexadecimal | 0x1B7 |
| 0b ou 0B (C23/GNU) | binário | 0b001010110 |

Ou seja, quando passamos `0666`, estamos dizendo que é octal e com isso as permissões passam a ser: `rw-rw-rw-`, ou seja, é um arquivo apenas de escrita e leitura.

Exemplo completo:

```c
#include <stdio.h>
#include <sys/stat.h>

int main() {
    if (mkfifo("canal", 0666) == -1) {
        perror("mkfifo");
        return 1;
    }
    printf("FIFO criada: canal\n");
    return 0;
}
```

### Programa Leitor

Vamos fazer agora um programa que vai ler os dados da FIFO, `leitor.c`:

```c
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>
#include <sys/stat.h>
#include <errno.h>

int main() {
    const char *fifo_path = "canal";

    if (mkfifo(fifo_path, 0666) == -1 && errno != EEXIST) {
        perror("mkfifo");
        return 1;
    }

    printf("Leitor: aguardando escritor...\n");

    int fd = open(fifo_path, O_RDONLY);

    if (fd == -1) {
        perror("open");
        return 1;
    }

    printf("Leitor: escritor conectado.\n");

    char buffer[128];
    ssize_t n;

    while ((n = read(fd, buffer, sizeof(buffer))) > 0) {
        write(STDOUT_FILENO, buffer, n);
    }

    if (n == -1) {
        perror("read");
        return 1;
    } else {
        printf("\nLeitor: escritor fechou a FIFO\n");
    }
    
    close(fd);
    return 0;
}
```

### Programa Escritor

Vamos fazer agora um programa que vai escrever os dados na FIFO, `escritor.c`:

```c
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>
#include <string.h>

int main() {
    const char *fifo_path = "canal";

    printf("Escritor: aguardando leitor...\n");

    int fd = open(fifo_path, O_WRONLY);

    if (fd == -1) {
        perror("open");
        return 1;
    }

    printf("Escritor: leitor conectado\n");

    const char *msg = "Mensagem enviada pela FIFO\n";

    if (write(fd, msg, strlen(msg)) == -1) {
        perror("write");
        close(fd);
        return 1;
    }

    close(fd);
    return 0;
}
```

### Comportamento de bloqueio

Abrir FIFO para leitura **bloqueia** (`open("canal", O_RDONLY);`). Se não houver nenhum escritor, o processo fica bloqueado esperando. Se abrir a FIFO para escrita, **também bloqueia** (`open("canal", O_WRONLY);`). Se não houver nenhum leitor, o processo fica bloqueado esperando.

## Exemplo real: servidor de comandos por FIFO

```c
// servidor_fifo.c

#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>
#include <sys/stat.h>
#include <string.h>
#include <errno.h>

int main() {
    const char *fifo_path = "/tmp/app_comandos";

    if (mkfifo(fifo_path, 0666) == -1 && errno != EEXIST) {
        perror("mkfifo");
        return 1;
    }

    printf("Servidor iniciado. FIFO %s\n", fifo_path);
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
                printf("Comando desconhecido: %s\n", buffer);
            }
        }
        fclose(fifo);
    }
    return 0;
}
```

> A FIFO é melhor na prática unidirecional, se queremos comunicação bidirecional é melhor usar **Unix Domain Socket**.

## Quando usar FIFO

Use FIFO quando:

- Os processos estão na mesma máquina
- A comunicação é simples
- Os processos não são pai e filho
- A comunicação é basicamente texto ou fluxo simples
- Queremos testar IPC pelo terminal facilmente

Exemplos bons:

- Canal de comandos local
- Script enviando comando para daemon simples
- Pipeline manual entre processos independentes
- Logs ou eventos simples
- Protótipos de IPC

**Não** use FIFO quando:

- Precisa de comunicação complexa bidirecional
- Precisa autenticação forte
- Precisa múltiplos clientes organizados
- Precisa comunicação pela rede
- Precisa mensagens estruturadas com resposta

Resumindo, FIFO é útil quando dois processos independentes precisam trocar dados simples na mesma máquina usando um nome conhecido no sistema de arquivos (`filesystem`).
