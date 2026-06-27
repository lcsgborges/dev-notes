# Programa, Processo e Kernel

## Programa

Programa é um arquivo executável parado no disco.

Exemplo: `hello_world.c` no disco. Enquanto ele está apenas parado no disco, ele não faz nada, é apenas um conjunto de instruções, dados e metadados.

## Processo

Um processo é um programa em execução. Quando fazemos `./hello_world`, o sistema operacional pega esse programa, carrega ele na memória e cria um processo.

```mermaid 
flowchart LR
    A[Programa no disco]
    B[Processo na memória]
    A --> B
```

Para mostrar processos ativos, basta utilizar o comando:

```bash
ps
# Ou com mais detalhes:
ps aux
```

Um processo contém várias coisas também:

- Código do programa
- Variáveis globais
- Heap
- Stack
- Registradores da CPU
- File descriptors
- PID
- Estado do processo
- Informações de permissões

### Estados do Processo

- **READY**: pronto para rodar
- **RUNNING**: rodando na CPU
- **BLOCKED**: esperando alguma coisa
- **TERMINATED**: terminou

Exemplo:

Se não houver dados para ler:

```mermaid
    flowchart LR
    A[Processo chama `read()`]
    B[Não há dados disponíveis]
    C[Kernel coloca processo em **BLOCKED**]
    A --> B --> C
```

Quando os dados chegam:

```mermaid
    flowchart LR
    A[Dados chegaram]
    B[Kernel acorda o processo]
    C[Kernel volta para **READY**]
    A --> B --> C
```

### Kernel

O responsável por controlar os processos é o **Kernel**. Ele é responsável por controlar a CPU, memória, disco e rede.

### PID 

PID significa *Process ID*. É o identificador único de um processo enquanto ele está ativo.

Exemplo em C:

```c
#include <stdio.h>
#include <unistd.h>

int main() {
    printf("My PID: %d\n", getpid());
    printf("Parent PID: %d\n", getppid());

    while (1) {
        sleep(1);
    }
    
    return 0;
}
```

Agora compilando e verificado o PID:

```bash
gcc my_pid.c -o my_pid

./my_pid

ps aux | grep my_pid
```

### Exemplo mental

```mermaid
    flowchart TD
    A[Shell recebe o comando]
    B[Shell pede ao kernel para criar o processo]
    C[Kernel carrega o programa na memória]
    D[Kernel cria estrutura do processo]
    E[Kernel entrega CPU ao processo]
    F[Programa começa a executar `main()`]
    A --> B --> C --> D --> E --> F
```

## Resumo

- **Programa**: arquivo executável parado no disco
- **Processo**: programa em execução
- **PID**: identificador único do processo
- **Kernel**: núcleo do sistema operacional
- **System call**: pedido do processo ao kernel
