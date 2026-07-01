# Unix Domain Sockets

**Unix Domain Sockets** é uma forma de comunicação entre processos que estão na mesma máquina, resumindo, é um tipo de **IPC**.

Ele funciona como um canal de comunicação **bidirecional**, controlado pelo kernel, representado por *file descriptors*.

## O que é um socket

É uma ponta de comunicação (traduzido do inglês como *tomada*). Cada processo tem uma ponta (tomada), criadas e controladas pelo kernel. Em **C**, um socket vira um *file descriptor*: `int fd = socket(...)`.

## Diferença de pipe e socket

Um **pipe** é um canal de comunicação unidirecional (só tem uma direção). Já o **Unix Domain Socket** é ida e volta no mesmo canal, então ele é melhor para conexões do tipo:

- cliente pergunta e servidor responde
- cliente envia comando e servidor confirma
- processo A manda dados e processo B devolve resultado

## Socketpair

Existe uma função chamada `socketpair()`. Ele já cria dois sockets conectados, como se o kernel entregasse dois telefones já em ligação.

```c
int sv[2];
socketpair(AF_UNIX, SOCK_STREAM, 0, sv);
```

Depois disso, temos que:

- `sv[0]`: uma ponta
- `sv[1]`: outra ponta

Exemplo:

```c
#include <stdio.h>
#include <unistd.h>
#include <string.h>
#include <sys/socket.h>

int main() {
    int sv[2];

    if (socketpair(AF_UNIX, SOCK_STREAM, 0, sv) == -1) {
        perror("socketpair");
        return 1;
    }

    const char *msg = "Olá pelo Unix Domain Socket";
    write(sv[0], msg, strlen(msg));

    char buffer[100];

    ssize_t n = read(sv[1], buffer, sizeof(buffer) -1);

    if (n == -1) {
        perror("read");
        return 1;
    }

    buffer[n] = '\0';

    printf("Recebido: %s\n", buffer);

    close(sv[0]);
    close(sv[1]);
    
    return 0;
}
```

Agora com `fork()`, pai e filho conversando:

```c
#include <stdio.h>
#include <unistd.h>
#include <string.h>
#include <sys/socket.h>
#include <sys/wait.h>

int main() {
    int sv[2];

    if(socketpair(AF_UNIX, SOCK_STREAM, 0, sv) == -1) {
        perror("socketpair");
        return 1;
    }

    pid_t pid = fork();

    if (pid == -1) {
        perror("fork");
        return 1;
    }

    if (pid == 0) {
        close(sv[0]);
        char buffer[100];
        ssize_t n = read(sv[1], buffer, sizeof(buffer) -1);

        if (n == -1) {
            perror("read");
            close(sv[1]);
            return 1;
        }

        buffer[n] = '\0';
        printf("Filho recebeu = %s\n", buffer);

        const char *resp = "Oi pai, tudo bem. Recebi sua mensagem.";
        write(sv[1], resp, strlen(resp));

        close(sv[1]);
        return 0;
    } else {
        close(sv[1]);

        const char *msg = "Oi filho, tudo bem?";
        write(sv[0], msg, strlen(msg));

        char buffer[100];

        ssize_t n = read(sv[0], buffer, sizeof(buffer)-1);

        if (n == -1) {
            perror("read");
            close(sv[0]);
            return 1;
        }

        buffer[n] = '\0';

        printf("Pai recebeu = %s\n", buffer);
        close(sv[0]);
        wait(NULL);
    }
    return 0;
}
```

O `socketpair()` funciona muito bem quando os processos tem relação, exemplo pai e filho. Mas quando tem dois processos diferentes, já não funciona, por isso precisamos criar um arquivo `.sock` (um **nome local** para conectar processos).

## Modelo cliente-servidor

Agora aparece um modelo novo: um processo fica esperando conexões (**servidor**) e outro processo se conecta nele (**cliente**). Fluxo:

1. Servidor cria socket
2. Servidor dá uma nome para ele, exemplo: `/tmp/app.sock`
3. Servidor fica esperando cliente
4. Cliente cria socket
5. Cliente conecta em `/tmp/app.sock`
6. Servidor aceita a conexão
7. Os dois conversam

### Funções do servidor

Servidor Unix Domain Socket usa:

- `socket()`: cria uma ponta de comunicação (tomada)
- `bind()`: dá um nome local para a ponta, exemplo `/tmp/app.sock`
- `listen()`: diz "agora estou aceitando conexões"
- `accept()`: espera um cliente conectar e cria uma conexão com ele
- `read()`: escreve no arquivo
- `write()`: lê do arquivo
- `close()`: fecha descritores
- `unlink()`: remove o arquivo `.sock` do sistema de arquivos (*filesystem*)

### Socket de escuta vs Socket conectado

No servidor existem dois tipos de file descriptors:

- `server_fd`: socket que fica esperando conexões
- `client_fd`: socket conectado com um cliente específico

O servidor não conversa com o cliente usando `server_fd`. Ele conversa usando o `client_fd`, que vem do `accept()`.

### Servidor Unix Domain Socket

Arquivo: `server.c`

```c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <sys/socket.h>
#include <sys/un.h>

#define SOCKET_PATH "/tmp/my_socket.sock"
#define BUFFER_SIZE 1024

int main() {
    int server_fd = socket(AF_UNIX, SOCK_STREAM, 0);

    if (server_fd == -1) {
        perror("socket");
        return 1;
    }

    unlink(SOCKET_PATH);

    struct sockaddr_un addr;
    memset(&addr, 0, sizeof(addr));

    addr.sun_family = AF_UNIX;
    strncpy(addr.sun_path, SOCKET_PATH, sizeof(addr.sun_path) -1);

    if (bind(server_fd, (struct sockaddr *) &addr, sizeof(addr)) == -1) {
        perror("bind");
        close(server_fd);
        return 1;
    }

    if (listen(server_fd, 5) == -1) {
        perror("listen");
        close(server_fd);
        unlink(SOCKET_PATH);
        return 1;
    }

    printf("Servidor esperando em: %s\n", SOCKET_PATH);

    int client_fd = accept(server_fd, NULL, NULL);

    if (client_fd == -1) {
        perror("accept");
        close(server_fd);
        unlink(SOCKET_PATH);
        return 1;
    }

    char buffer[BUFFER_SIZE];

    ssize_t n = read(client_fd, buffer, sizeof(buffer) -1);

    if (n == -1) {
        perror("read");
        close(server_fd);
        close(client_fd);
        unlink(SOCKET_PATH);
        return 1;
    }

    buffer[n] = '\0';

    printf("Cliente disse = %s\n", buffer);

    const char *resp = "Servidor recebeu sua mensagem\n";

    write(client_fd, resp, strlen(resp));

    close(client_fd);
    close(server_fd);

    unlink(SOCKET_PATH);
    
    return 0;
}
```
