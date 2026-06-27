# OpenMP

## Por que utilizar programação paralela?

Os dois principais motivos são:

- **Reduzir o tempo** necessário para solucionar um problema.
- **Resolver problemas mais complexos** e de maior dimensão.

## Modelos de programação paralela

### Programação em memória compartilhada (OpenMP, Cilk, CUDA)

- Programação usando processos ou threads.
- Decomposição do domínio ou funcional com granularidade fina, média ou grossa.
- Comunicação através de uma **memória compartilhada**.
- Sincronização através de mecanismos de exclusão mútua.

### Programação em memória distribuída (MPI)

- Programação usando processos distribuídos.
- Decomposição do domínio com granularidade grossa.
- Comunicação e sincronização por **troca de mensagens**.

## Introdução ao OpenMP

Tipos e protótipos de funções no arquivo:

```c
#include <omp.h>
```

A maioria das construções do OpenMP consiste em diretivas de compilação:

```c
# pragma omp construct [clause[clause]...]

// Exemplo:

# pragma omp parallel private(var1, var2) shared(var3, var4)
{

}
```

> Em C, `#pragma` é uma diretiva de pré-processamento que modifica ou controla comportamentos do compilador que não fazem parte da linguagem em si. De forma resumida, ela passa comandos especiais ao compilador.

Notas de compilação com **GCC**:

```bash
gcc code.c -o code.out -fopenmp
```

### Funções OpenMP

Algumas funções comuns do OpenMP:

```c
// Arquivo de interface da biblioteca OpenMP
#include <omp.h>

// Retorna o identificador da thread (ID)
int omp_get_thread_num();

// Indica o número de threads a executar na região paralela
void omp_set_num_threads(int num_threads);

// Retorna o número de threads da equipe que executa a região paralela

// Podemos alterar esse padrão usando: export OMP_NUM_THREADS=4 (por exemplo)
int omp_get_num_threads();
```

### Diretivas OpenMP

Algumas diretivas comuns do OpenMP:

```c
// Cria uma região paralela. Define variáveis privadas e compartilhadas entre
// as threads

// Cada thread possui sua própria cópia da variável privada
#pragma omp parallel private(...) shared(...)
{ // Precisa do bloco na linha abaixo

    // No caso abaixo, apenas uma thread executa o bloco
    #pragma omp single
}
```

#### Cláusula shared

Especifica um conjunto de variáveis que são compartilhadas entre as threads (por padrão, as variáveis são shared ao entrar na região paralela).

#### Cláusula private

Especifica um conjunto de variáveis privadas. Os valores no início da região paralela são indefinidos. Ao final da região, as cópias privadas são descartadas; portanto, os valores das variáveis originais não são atualizados por essas cópias.

### Exercício 01: Hello World em OpenMP

```c
#include <stdio.h>
#include <omp.h>

int main() {
    int thread_id, num_threads;
    #pragma omp parallel private(thread_id) shared(num_threads)
    {
        thread_id = omp_get_thread_num();
        #pragma omp single
        num_threads = omp_get_num_threads();
        printf("%d of %d - hello world!\n", thread_id, num_threads);
    }
    return 0;
}

// Para compilar: gcc code01.c -o code.out -fopenmp
```

No OpenMP, podemos usar `#pragma omp for`, que permite dividir as iterações de um laço entre as threads sem definir manualmente um chunk, um início e um fim para cada uma, como seria necessário em uma distribuição manual com **MPI**.

### Interações das threads

OpenMP é um modelo de _multithreading_ de memória compartilhada; portanto, as threads se comunicam por meio de variáveis compartilhadas.

- O compartilhamento não intencional de dados causa **condições de corrida** (quando a saída do programa muda conforme as threads são escalonadas)
- O problema ocorre quando duas ou mais threads tentam acessar ou alterar o conteúdo da mesma estrutura simultaneamente
- Precisamos planejar o acesso aos dados para reduzir a necessidade de sincronização, pois ela tem custo de desempenho

### Sincronização

Assegura que uma ou mais threads estejam em um estado bem definido em um ponto conhecido da execução. As duas formas mais comuns são:

- **Barreira**: cada thread espera na barreira até a chegada de todas as demais
- **Exclusão mútua**: define um bloco de código que apenas uma thread pode executar por vez

#### Barrier

```c
#pragma omp parallel
{
    int id = omp_get_thread_num();
    A[id] = big_calc(id);

    #pragma omp barrier
    B[id] = big_calc2(id, A);
}
```

#### Exclusão mútua (Critical)

- **Critical**: apenas uma thread pode entrar por vez

```c
#pragma omp parallel
{
    float b;
    int i, id, nthreads, N;
    id = omp_get_thread_num();
    nthreads = omp_get_num_threads();

    for (i = id; i < N; i += nthreads) {
        b = big_job(i);
        #pragma omp critical
        res += consume(b);
    }
}
```

- **Atomic**: provê exclusão mútua para operações específicas (atribuições, incrementos e decrementos)

```c
#pragma omp parallel
{
    double tmp, b, x = 0;
    b = do_it();
    tmp = big_ugly(b);
    #pragma omp atomic
    x += tmp;
}
```

### Exercício 02: Vector SUM

```c
#include <stdio.h>
#include <omp.h>

#define TAM 1000

int main() {

    int id, nthreads, v[TAM];
    int sum = 0;
    int sum_local = 0;

    for (int i = 0; i < TAM; i++)
        v[i] = 1;

    #pragma omp parallel private(id) shared(nthreads, sum)
    {
        #pragma omp single
        nthreads = omp_get_num_threads();

        #pragma omp for
        for (int i = 0; i < TAM; i++)
            sum_local += v[i];

        #pragma omp atomic
        sum += sum_local;
    }
    printf("SUM = %d\n", sum);
    return 0;
}
```

### Redução

A combinação de variáveis locais das threads em uma única variável é chamada de **redução**.

Diretiva reduction: `reduction(op: list_vars)`

Dentro de uma região paralela ou divisão de trabalho:

- Será feita uma cópia local de cada variável da lista
- Cada cópia será inicializada de acordo com a operação (por exemplo, 0 para `+` e 1 para `*`)
- As atualizações acontecerão na cópia local
- As cópias locais serão "reduzidas" para a variável original

```c
// Exemplo:
#pragma omp for reduction(+: sum)
```

Solução do **Vector SUM (exercício 02)** com redução:

```c
#include <stdio.h>
#include <omp.h>

#define TAM 100000

int main() {
    int sum = 0, v[TAM];
    for (int i = 0; i < TAM; i++)
        v[i] = 1;

    #pragma omp parallel for reduction(+: sum)
    for (int i = 0; i < TAM; i++)
        sum += v[i];

    printf("SUM = %d\n", sum);
    return 0;
}
```

### Sections

No contexto de programação paralela com OpenMP, `#pragma omp section` é uma diretiva de "compartilhamento de trabalho" (worksharing) usada dentro de um bloco `#pragma omp sections`. Ela divide tarefas distintas e independentes entre as threads, permitindo que cada bloco de código seja executado apenas uma vez por uma única thread, paralelizando funções diferentes:

```c
#include <stdio.h>
#include <omp.h>

int main() {
    #pragma omp parallel
    // Também poderia ser: #pragma omp parallel sections
    {
        #pragma omp sections
        {
            #pragma omp section
            {
                // Apenas uma thread executa essa função
                printf("Função 1\n");
            }
            #pragma omp section
            {
                // Apenas uma thread executa essa função
                printf("Função 2\n");
            }
        }
        // Todas as threads executam essa função
        printf("Função executada por todas as threads!\n");
    }
    return 0;
}
```
