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
    A[Processo chama read()]
    B[Não há dados disponíveis]
    C[Kernel coloca processo em BLOCKED]
    A --> B --> C
```

Quando os dados chegam:

```mermaid
    flowchart LR
    A[Dados chegaram]
    B[Kernel acorda o processo]
    C[Kernel volta para READY]
    A --> B --> C
```

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

Agora compilando e verificando o PID:

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
    F[Programa começa a executar main()]
    A --> B --> C --> D --> E --> F
```

## Kernel

O responsável por controlar os processos é o **Kernel** (núcleo do Sistema Operacional). Ele é responsável por controlar a CPU, memória, disco e rede. É responsável por conversar diretamente com o hardware e controlar praticamente tudo:

- CPU
- Memória
- Disco
- Rede
- USB
- Mouse
- Teclado
- Entre outros...

### Rings

A maioria dos processadores x86 possui níveis de privilégio chamados **Rings**. Os dois mais importantes são:

- Ring 0
- Ring 3

#### Ring 0

É onde o Kernel executa. Aqui ele pode fazer tudo, como: acessar qualquer memória, controlar a CPU, acessar dispositivos, interromper processos, criar processos, configurar a rede. Resumindo, possui **privilégio máximo**.

#### Ring 3

É onde nosso programa roda, por exemplo: Chrome, VSCode, Firefox, um programa nosso em C. Nesse caso eles possuem várias restrições e não podem sair acessando qualquer coisa.

O Ring 3 é chamado de **User Space**. É o espaço onde ficam os programas do usuário.

### System Call

Quando escrevemos um programa e usamos o comando `printf()`, não temos acesso a tela, por isso chamamos o Kernel e pedimos algo para ele usando **system call**. Uma **system call** é uma porta oficial de entrada no Kernel, é um pedido.

Exemplo ao abrir um arquivo em C:

```mermaid
    flowchart TD
    A[fopen()]
    B[glibc]
    C[open()]
    D[System call]
    E[Kernel]
    F[Sistem de Arquivos]
    G[Disco]
    A --> B --> C --> D --> E --> F --> G
```

Essa troca de contexto, quando precisamos acessar ou interagir com algo controlado pelo kernel se chama **Mode Switch** ou **Kernel Transition**.

#### Por que System Calls são caras?

Uma função comum `int soma(a, b) { return a + b; }` executa inteiramente no *User Space*. Já uma *System Call* precisa:

1. Mudar para modo kernel
2. Salvar registradores
3. Validar argumentos
4. Verificar permissões
5. Executar o kernel
6. Voltar ao user space

Isso custa muito mais ciclos da CPU. Por isso bibliotecas como a `glibc` tentam evitar chamadas desnecessárias ao Kernel.

### glibc

Raramente chamamos system calls diretamente. Normalmente usamos bibliotecas, exemplo: `printf()`, `malloc()`, `fopen()`. Essas funções pertencem, em geral, à glibc. Ela atua como uma camada entre o programa e o kernel.

Podemos verificar, "enxergar" a conversa entre um programa e o Kernel:

```bash
strace program

# Exemplo:

strace ls
```

## Principais conceitos

- **Programa**: arquivo executável parado no disco
- **Processo**: programa em execução
- **PID**: identificador único do processo
- **Kernel**: núcleo do sistema operacional
- **System call**: pedido do processo ao kernel
