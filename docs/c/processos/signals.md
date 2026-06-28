# Signals

Signal é uma notificação assíncrona enviada a um processo para informar que algum evento aconteceu. Ele carrega basicamente um número que traduz isso em uma ação, ele não é ideal para enviar dados.

## Principais Signals

- SIGINT: interrupção, geralmente `Ctrl + C`
- SIGTERM: pedido educado de término
- SIGKILL: mata imediatamente
- SIGSTOP: pausa imediatamente
- SIGCONT: continua processo pausado
- SIGCHLD: filho terminou ou mudou de estado
- SIGSEGV: acesso inválido à memória
- SIGFPE: erro aritmético, como divisão por zero
- SIGPIPE: escrita em pipe/socket sem leitor
- SIGHUP: terminal desconectou; usado também para recarregar config
- SIGALRM: alarme/temporizador
- SIGUSR1: signal livre para uso da aplicação
- SIGUSR2: outro signal livre para uso da aplicação

Cada Signal tem um número associado, exemplo:

- SIGINT = 2
- SIGKILL = 9
- SIGTERM = 15

Mas é bom evitar depender diretamente dos números no código.

## O que os processos fazem com o signal

Para muitos signals, o processo pode:

1. Aceitar a ação padrão do signal
2. Ignorar o signal
3. Capturar o signal com uma função handler

Exemplo:

```text
SIGINT chegou

Opção 1: encerrar
Opção 2: ignorar
Opção 3: executar uma função handler()
```

O `SIGKILL` e o `SIGSTOP` não podem ser ignorados e nem capturados, pois o sistema precisa ter uma forma definitiva de matar ou parar um processo.

Então para matar um processo imediatamente:

```bash
kill -9 PID
```

O nome `kill` é enganoso, ele não serve apenas para matar processos, ele serve para **enviar signals**. Exemplo:

```bash
kill -USR1 1234
kill -HUP 1234
kill -STOP 1234
kill -CONT 1234
```

Exemplo capturando um `Ctrl + C`:

```c
#include <stdio.h>
#include <signal.h>
#include <unistd.h>

void sigint_handler(int signal) {
    printf("\nReceive SIGINT. Signal = %d\n", signal);
}

int main() {
    signal(SIGINT, sigint_handler);

    while (1) {
        printf("Running... Press Ctrl+C\n");
        sleep(1);
    }

    return 0;
}
```

Para ignorar um signal, podemos usar `signal(SIGNAL, SIG_IGN)`, exemplo: `signal(SIGINT, SIG_IGN)`.

Embora simples, `signal()` tem problemas históricos de portabilidade e comportamento. Por isso, surgiu o `sigaction()`.

Um processo pode enviar signal para si mesmo usando o `raise()`:

```c
#include <stdio.h>
#include <signal.h>

int handler(int sig) {
    printf("Signal = %d\n", sig);
}

int main() {
    signal(SIGUSR1, handler);
    raise(SIGUSR1);
    return 0;
}
```

Com `pause()` um processo "dorme" até que receba um signal:

```c
while(1) {
    pause();
}
```

Isso evita consumo de CPU desnecessário.

Com o `alarm()`, podemos pedir para o kernel enviar um `SIGALRM` após x segundos:

```c
#include <signal.h>
#include <unistd.h>

void sigalrm_handler(int sig) {
    write(1, "Tempo esgotado\n", 15);
}

int main() {
    signal(SIGALRM, sigalrm_handler);
    alarm(5);

    // Quando chega um signal, a função sai do pause() 
    // e continua o restante do código
    
    pause();
    return 0;
}
```
