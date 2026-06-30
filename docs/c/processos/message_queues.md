# Message Queues

Uma **Message Queue** é um mecanismo de IPC onde processos trocam dados através de uma **fila de mensagens mantida pelo kernel**. A diferença principal para `pipe` é que `pipe` mantém **bytes contínuos**, já `message queues` mantém mensagens separadas, ou seja, em `pipe` precisamos criar um protocolo/mecanismo para separar mensagens, já em `message queues`, **cada envio já é uma mensagem separada**.

Dessa forma, um **produtor** envia mensagens e um **consumidor** recebe mensagens. Isso é util quando queremos:

- tarefas discretas
- comandos
- eventos
- mensagens independentes
- produtor e consumidor desacoplados

## Message Queue vs Pipe

### Pipe

- fluxo de bytes
- não preserva mensagens
- normalmente lido na ordem dos bytes
- bom para streaming

### Message Queue

- fila de mensagens
- preserva fronteira das mensagens
- pode ter prioridade
- bom para tarefas/eventos/comandos

Exemplo:

```text
PIPE:
"ADD 2 3\nSUB 10 4\n"

MESSAGE QUEUE:
Mensagem 1: "ADD 2 3"
Mensagem 2: "SUB 10 4"
```

## POSIX Message Queues

API mais morderna e mais limpa:

- `mq_open()`
- `mq_send()`
- `mq_receive()`
- `mq_close()`
- `mq_unlink()`

As filas POSIX são identificadas por nomes do tipo `/algum_nome`, e dois processos usam a mesma fila abrindo o mesmo nome com `mq_open()`.

Mensagens são enviadas e recebidas com `mq_send()` e `mq_receive()`.

A fila é fechada/removida com `mq_close()` e `mq_unlink()`.

## System V Message Queues

API mais antiga:

- `msgget()`
- `msgsnd()`
- `msgrcv()`
- `msgctl()`

A API System V usa `msgget()` para criar ou obter uma fila, `msgsnd()` para adicionar mensagens e `msgrcv()` para receber mensagens.

## Fluxo Básico POSIX

Um processo **produtor** faz:

1. `mq_open()`
2. `mq_send()`
3. `mq_close()`

Um processo **consumidor** faz:

1. `mq_open()`
2. `mq_receive()`
3. `mq_close()`
4. `mq_unlink()`

## Criando uma fila

Em POSIX, a fila tem nome, exemplo `/minha_fila`. Regras importantes:

- começa com `/`.
- não usa barras adicionais
- não é `/tmp/fila`

A fila POSIX não é um arquivo comum no seu diretório. Ela é um objeto IPC do sistema.

### Estrutura de atributos

Quando criamos a fila, podemos definir atributos:

```c
struct mq_attr attr;
```

Campos importantes:

```c
attr.mq_maxmsg; // máximo de mensagens na fila

attr.mq_msgsize; // tamanho máximo de cada mensagem
```

Exemplo:

```c
struct mq_attr attr;

attr.mq_flags = 0;
attr.mq_maxmsg = 10;
attr.mq_msgsize = 256;
attr.mq_curmsgs = 0;
```

O exemplo acima diz que a fila pode ter até 10 mensagens e cada mensagem pode ter até 256 bytes.

## Produtor: enviando mensagens

Exemplo `produtor.c`:

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <mqueue.h>
#include <fcntl.h>
#include <sys/stat.h>

#define QUEUE_NAME "/fila_tarefas"
#define MAX_MSG 10
#define MSG_SIZE 256

int main(void) {
    struct mq_attr attr;

    attr.mq_flags = 0;
    attr.mq_maxmsg = MAX_MSG;
    attr.mq_msgsize = MSG_SIZE;
    attr.mq_curmsgs = 0;

    mqd_t fila = mq_open(QUEUE_NAME, O_CREAT | O_WRONLY, 0644, &attr);

    if (fila == (mqd_t) -1) {
        perror("mq_open");
        return 1;
    }

    const char *mensagens[] = {
        "tarefa 1: gerar relatório",
        "tarefa 2: enviar email",
        "tarefa 3: processar pagamento"
    };

    for (int i = 0; i < 3; i++) {
        if (mq_send(fila, mensagens[i], strlen(mensagens[i]) + 1, 0) == -1) {
            perror("mq_send");
            mq_close(fila);
            return 1;
        }
        printf("Enviado: %s\n", mensagens[i]);
    }
    mq_close(fila);
    return 0;
}
```

## Consumidor: recebendo mensagens

Exemplo `consumidor.c`:

```c
#include <stdio.h>
#include <stdlib.h>
#include <mqueue.h>
#include <fcntl.h>
#include <sys/stat.h>

#define QUEUE_NAME "/fila_tarefas"
#define MSG_SIZE 256

int main(void) {
    mqd_t fila = mq_open(QUEUE_NAME, O_RDONLY);

    if (fila == (mqd_t) -1) {
        perror("mq_open");
        return 1;
    }

    char buffer[MSG_SIZE];
    unsigned int prioridade;

    for (int i = 0; i < 3; i++) {
        ssize_t n = mq_receive(fila, buffer, sizeof(buffer), &prioridade);

        if (n == -1) {
            perror("mq_receive");
            mq_close(fila);
            return 1;
        }

        printf("Recebido: %s | prioridade %u\n", buffer, prioridade);
    }

    mq_close(fila);

    /*
     * Remove a fila do sistema.
     * Faça isso quando ela não for mais necessária.
     */
    mq_unlink(fila);
    return 0;
}
```

Compilando:

```bash
gcc produtor.c -o produtor -lrt
gcc consumidor.c -o consumidor -lrt
```

## O que é `mqd_t`

Quando fazemos `mqd_t fila = mq_open(...)`, estamos criando um **descritor da message queue**. Pense nele como o equivalente de um *file descriptor*, mas específico para *POSIX message queue*.

## Prioridade das mensagens

POSIX message queues suportam prioridade:

```c
mq_send(fila, mensagem, tamanho, prioridade);
```

Ao receber, a fila **entrega primeiro a mensagem de maior prioridade**. Mensagens com a mesma prioridade preservam a ordem de chegada. A documentação do `mq_send()` descreve que mensagens são posicionadas em ordem decrescente de prioridade, mantendo a ordem entre as mensagens de mesma prioridade.

Exemplo `prioridade.c`:

```c
#include <stdio.h>
#include <string.h>
#include <mqueue.h>
#include <fcntl.h>
#include <sys/stat.h>

#define QUEUE_NAME "/fila_prioridade"
#define MSG_SIZE 128
#define MAX_MSG 10

int main() {
    struct mq_attr attr = {0};
    attr.mq_maxmsg = MAX_MSG;
    attr.mq_msgsize = MSG_SIZE;

    mq_unlink(QUEUE_NAME);

    mqd_t fila = mq_open(QUEUE_NAME, O_CREAT | O_RDWR, 0664, &attr);

    if (fila == (mqd_t) -1) {
        perror("mq_open");
        return 1;
    }
     
    // strlen() só retorna o tamanho vísivel da string, precisamos somar +1 para incluir o '\0'
    mq_send(fila, "mensagem baixa", strlen("mensagem baixa") + 1, 1);
    mq_send(fila, "mensagem alta", strlen("mensagem alta") + 1, 10);
    mq_send(fila, "mesagem média", strlen("mensagem média") + 1, 5);

    char buffer[MSG_SIZE];
    unsigned int prio;

    for (int i = 0; i < 3; i++) {
        if (mq_receive(fila, buffer, sizeof(buffer), &prio) == -1) {
            perror("mq_receive");
            break;
        }

        printf("Recebido: %s | prioridade = %u\n", buffer, prio);
    }
    mq_close(fila);
    mq_unlink(QUEUE_NAME);
    return 0;
}
```

Saída esperada:

```text
Recebido: mensagem alta | prioridade = 10
Recebido: mensagem média | prioridade = 5
Recebido: mensagem baixa | prioridade = 1
```

## Bloqueio

Por padrão:

- `mq_receive()` bloqueia se a fila estiver cheia
- `mq_send()` bloqueia se a fila estiver cheia

Isso é útil porque o consumidor pode simplesmente esperar trabalho. Exemplo:

```c
mq_receive(fila, buffer, sizeof(buffer), NULL);
```

Se não houver mensagem, o processo dorme. O kernel acorda quando alguém enviar uma mensagem.

Podemos abrir fila com `O_NONBLOCK` (**modo não bloqueante**). Exemplo:

```c
mqd_t fila = mq_open(QUEUE_NAME, O_RDONLY | O_NONBLOCK);
```

Agora, se a fila estiver vazia, `mq_receive()` não fica travado. Ele falha imediatamente, exemplo:

```c
#include <stdio.h>
#include <errno.h>
#include <mqueue.h>
#include <fcntl.h>

#define QUEUE_NAME "/fila_tarefas"
#define MSG_SIZE 256

int main() {
    mqd_t fila = mq_open(QUEUE_NAME, O_RDONLY | O_NONBLOCK);

    if (fila == (mqd_t) -1) {
        perror("mq_open");
        return 1;
    }

    char buffer[MSG_SIZE];

    ssize_t n = mq_receive(fila, buffer, sizeof(buffer), NULL);

    if (n == -1) {
        if (errno == EAGAIN) {
            printf("Fila vazia. Não vou bloquear.\n");
        } else {
            perror("mq_receive");
        }
    }
    mq_close(fila);
    return 0;
}
```

## Descobrindo atributos das filas

Podemos usar `mq_getattr()`:

```c
#include <stdio.h>
#include <mqueue.h>
#include <fcntl.h>

#define QUEUE_NAME "/fila_tarefas"

int main() {
    mqd_t fila = mq_open(QUEUE_NAME, O_RDONLY);

    if (fila == (mqd_t) -1) {
        perror("mq_open");
        return 1;
    }

    struct mq_attr attr;

    if (mq_getattr(fila, &attr) == -1) {
        perror("mq_getattr");
        mq_close(fila);
        return 1;
    }

    printf("Max mensagens = %ld\n", attr.mq_maxmsg);
    printf("Tam mensagens = %ld\n", attr.mq_msgsize);
    printf("Msgs atuais = %ld\n", attr.mq_curmsgs);

    mq_close(fila);
    return 0;
}
```

## Uma fila de jobs

Uma fila onde clientes enviam tarefas e um worker processa.

Mensagem:

```c
typedef struct {
    int id;
    char acao[64];
} Job;
```

### Produtor

```c
#include <stdio.h>
#include <string.h>
#include <mqueue.h>
#include <fcntl.h>
#include <sys/stat.h>

#define QUEUE_NAME "/fila_jobs"

typedef struct {
    int id;
    char acao[64];
} Job;

int main(void) {
    struct mq_attr attr = {0};

    attr.mq_maxmsg = 10;
    attr.mq_msgsize = sizeof(Job);

    mqd_t fila = mq_open(QUEUE_NAME, O_CREAT | O_WRONLY, 0644, &attr);

    if (fila == (mqd_t) -1) {
        perror("mq_open");
        return 1;
    }

    Job jobs[] = {
        {1, "gerar_pdf"},
        {2, "enviar_email"},
        {3, "processar_pagamento"}
    };

    for (int i = 0; i < 3; i++) {
        if (mq_send(fila, (const char *) &jobs[i], sizeof(Job), 0) == -1) {
            perror("mq_send");
            mq_close(fila);
            return 1;
        }
        printf("Job enviado: id=%d acao=%s\n", jobs[i].id, jobs[i].acao);
    }

    mq_close(fila);
    return 0;
}
```

### Worker

```c
#include <stdio.h>
#include <mqueue.h>
#include <fcntl.h>

#define QUEUE_NAME "/fila_jobs"

typedef struct {
    int id;
    char acao[64];
} Job;

int main() {
    mqd_t fila = mq_open(QUEUE_NAME, O_RDONLY);

    if (fila == (mqd_t) -1) {
        perror("mq_open");
        return 1;
    }

    while (1) {
        Job job;

        ssize_t n = mq_receive(fila, (char *) &job, sizeof(Job), NULL);

        if (n == -1) {
            perror("mq_receive");
            mq_close(fila);
            return 1;
        }

        printf("Processando job: id=%d acao=%s\n", job.id, job.acao);

        if (job.id == -1) {
            printf("Worker encerrando\n");
            break;
        }
    }
    mq_close(fila);
    mq_unlink(QUEUE_NAME);
    return 0;
}
```

### Mensagem de parada

```c
Job stop = {-1, "stop"};
mq_send(fila, (const char*) &stop, sizeof(Job), 10);
```

## `mq_close()` vs `mq_unlink()`

O `mq_close()` fecha o descritor da fila naquele processo. (`mq_close(fila)`).

Já o `mq_unlink()` remove o nome da fila do sistema. (`mq_unlink("/fila")`). Se esquecer o `unlink`, pode deixar fila "sobrando" no sistema.

## Filas POSIX no Linux

Em Linux, as filas POSIX geralmente aparecem através de um filesystem especial `mqueue`, frequentemente montado em `/dev/mqueue`. Podemos tentar:

```bash
ls /dev/mqueue
```

## Quando usar Message Queue

Use quando:

- Temos mensagens discretas
- Quer produtor/consumidor
- Quer fila de tarefas local
- Quer Prioridade
- Quer desacoplar envio e processamento
- Quer evitar criar protocolo de separação de mensagens em stream

Bons exemplos:

- Um processo envia jobs para worker
- Um daemon recebe comandos internos
- Múltiplos produtores geram eventos
- Um consumidor processa em ordem/prioridade

Evite quando:

- Precisa de streaming contínuo de dados
- Precisa de comunicação pela rede
- Precisa de muitos clientes complexos com resposta individual
- Precisa alta vazão com dados grandes
