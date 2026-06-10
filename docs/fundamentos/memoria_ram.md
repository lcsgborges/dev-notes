# Memória RAM

## Stack

A **stack** é uma área de memória usada para armazenar informações temporárias durante a execução de funções.

Ela normalmente contém:

- Variáveis locais simples
- Parâmetros de funções
- Endereço de retorno da função
- Informação da chamada (call stack)

Exemplo em C:

```c
void example() {
    int age = 10;
}
```

A variável `age` geralmente é armazenada na **stack**.

### Características

- Muito rápida
- Tamanho limitado (geralmente MBs)
- Gerenciada automaticamente
- Os dados desaparecem quando a função termina

## Heap

A **heap** é uma área de memória usada para armazenar dados que precisam viver por mais tempo ou cujo tamanho é conhecido apenas em tempo de execução.

Exemplo:

```c
int *age = malloc(sizeof(int));
*age = 10;
```

No exemplo acima, o ponteiro `age` fica na stack. O valor **10** fica na heap.

```mermaid
flowchart LR
    A[age]-.->B[10];
```

### Características

- Maior que a stack
- Mais lenta para a alocação
- Permite tamanhos dinâmicos
- Precisa ser liberada manualmente em algumas linguagens (como C)

```c
free(age);
```

Em linguagens como Java, Go, C#, a liberação é normalmente feita pelo **Garbage Collector**

## Ponteiros

Um ponteiro é uma variável que guarda o endereço de memória de outra variável

```c
int age = 10;
int *p = &age;
```

Temos que:

```text
age = 10

p = endereço de age
```

Visualmente:

```text
0x1000 -> age = 10

p = 0x1000
```

O operador `&`  significa: "Qual é o endereço de memória de `age`?"

O operador `*` significa "acesse o valor armazenado naquele endereço"

Exemplo:

```c
printf("%d", *p);

// Saída: 30
```

## Relação entre Ponteiros, Stack e Heap

Um caso clássico:

```c
int *p = malloc(sizeof(int));
*p = 50;
```

O que acontece:

1. A variável `p` é criada na stack
2. `malloc` reserva espaço na heap do tamanho de `int` (4 bytes = 32 bits)
3. O endereço desse espaço é reservado em `p`
4. `*p = 50` grava o valor 50 naquele endereço

## Resumo Prático

- **Pergunta**: O dado existe apenas durante a execução da função?

Provavelmente está na **stack**

- **Pergunta**: O dado precisa sobreviver ao término da função ou tem tamanho dinâmico?

Provavelmente está na **heap**

- **Pergunta**: Como encontrar esse dado na memória?

Através de um **ponteiro** (ou referência, em linguagens de mais alto nível)

- Resumo: **_Stack_ guarda variáveis locais e contexto da execução. _Heap_ guarda objetos e dados alocados dinamicamente. _Ponteiros_ são endereços que permitem acessar esses dados na memória**
