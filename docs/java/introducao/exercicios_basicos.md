# Lista de exercícios Java — primeiros passos

## Exercício 1 — Cadastro simples de usuário

Crie um programa que leia:

- nome;
- idade;
- cidade;
- profissão.

Depois, exiba uma mensagem formatada no seguinte modelo:

```text
Olá, João!
Você tem 25 anos, mora em Brasília e trabalha como programador.
```

### Requisitos

- Use variáveis do tipo `String` e `int`.
- Use estrutura sequencial.
- Crie uma função chamada `exibirCadastro`.

### Exemplo de função

```java
public static void exibirCadastro(String nome, int idade, String cidade, String profissao) {
    // Sua lógica aqui.
}
```

---

## Exercício 2 — Conversor de temperatura

Crie um programa que leia uma temperatura em Celsius e converta para Fahrenheit.

Fórmula:

```text
fahrenheit = (celsius * 9 / 5) + 32
```

### Requisitos

- Use `double`.
- Crie uma função chamada `converterParaFahrenheit`.
- Exiba o resultado com uma mensagem clara.

### Exemplo

```text
Digite a temperatura em Celsius: 30
30.0°C equivalem a 86.0°F
```

---

## Exercício 3 — Verificador de maioridade

Crie um programa que leia o nome e a idade de uma pessoa.

O programa deve informar:

- se a pessoa é menor de idade;
- se a pessoa é maior de idade;
- se a pessoa é idosa, considerando idade maior ou igual a 60.

### Requisitos

- Use `if`, `else if` e `else`.
- Crie uma função chamada `classificarIdade`.
- A função deve receber a idade e retornar um texto.

### Exemplo

```java
public static String classificarIdade(int idade) {
    // Sua lógica aqui.
}
```

---

## Exercício 4 — Sistema de notas

Crie um programa que leia três notas de um aluno e calcule a média.

Depois, informe a situação do aluno:

- média maior ou igual a 7: aprovado;
- média maior ou igual a 5 e menor que 7: recuperação;
- média menor que 5: reprovado.

### Requisitos

- Crie uma função `calcularMedia`.
- Crie uma função `verificarSituacao`.
- Use estrutura condicional.
- Exiba a média e a situação final.

### Exemplo

```text
Média: 6.5
Situação: Recuperação
```

---

## Exercício 5 — Normalizador de nomes

Crie um programa que leia o nome completo de uma pessoa.

O programa deve exibir:

- o nome original;
- o nome em letras maiúsculas;
- o nome em letras minúsculas;
- a quantidade de caracteres;
- o nome sem espaços no início e no fim.

### Requisitos

Use os métodos:

```java
toUpperCase()
toLowerCase()
length()
trim()
```

### Desafio extra

Crie uma função chamada `normalizarNome` que receba uma `String` e retorne o nome sem espaços no início e no fim.

---

## Exercício 6 — Verificador de palavra-chave

Crie um programa que leia uma frase e uma palavra-chave.

O programa deve informar se a palavra-chave aparece dentro da frase.

### Requisitos

- Use `toLowerCase(Locale.ROOT)` nas duas entradas para evitar diferenças de capitalização dependentes da configuração regional.
- Importe `java.util.Locale`.
- Use `contains()`.
- Crie uma função chamada `contemPalavra`.

### Exemplo

```text
Digite uma frase: Java é uma linguagem poderosa
Digite a palavra-chave: java
A palavra foi encontrada.
```

### Exemplo de função

```java
public static boolean contemPalavra(String frase, String palavra) {
    // Sua lógica aqui.
}
```

---

## Exercício 7 — Login simples

Crie um programa que simule um login.

O usuário deve digitar:

- e-mail;
- senha.

Considere que o login correto seja:

```text
email: admin@email.com
senha: 123456
```

### Requisitos

- O programa deve aceitar o e-mail mesmo que ele seja digitado com letras maiúsculas.
- Use `equalsIgnoreCase()` para comparar o e-mail.
- Use `equals()` para comparar a senha.
- Crie uma função chamada `validarLogin`.

### Exemplo

```text
Digite seu e-mail: ADMIN@EMAIL.COM
Digite sua senha: 123456
Login realizado com sucesso.
```

---

## Exercício 8 — Contador com `for`

Crie um programa que leia um número inteiro positivo.

Depois, exiba todos os números de 1 até o número informado.

### Requisitos

- Use laço `for`.
- Crie uma função chamada `contarAte`.
- Se o número for menor ou igual a zero, exiba uma mensagem de erro.

### Exemplo

```text
Digite um número: 5

1
2
3
4
5
```

---

## Exercício 9 — Tabuada com função

Crie um programa que leia um número inteiro e exiba a tabuada desse número de 1 a 10.

### Requisitos

- Use laço de repetição.
- Crie uma função chamada `exibirTabuada`.
- A função deve receber o número como parâmetro.
- Exiba o resultado em formato organizado.

### Exemplo

```text
Digite um número: 7

7 x 1 = 7
7 x 2 = 14
7 x 3 = 21
...
7 x 10 = 70
```

---

## Exercício 10 — Menu interativo de textos

Crie um programa com um menu repetitivo para manipular uma frase digitada pelo usuário.

O programa deve mostrar o seguinte menu:

```text
1 - Mostrar frase em maiúsculas
2 - Mostrar frase em minúsculas
3 - Mostrar quantidade de caracteres
4 - Verificar se contém uma palavra
5 - Substituir uma palavra
0 - Sair
```

### Requisitos

- Use `while` ou `do-while`.
- Use `switch`.
- Use métodos de `String`, como:

```java
toUpperCase()
toLowerCase()
length()
contains()
replace()
trim()
```

- Crie pelo menos 3 funções, por exemplo:

```java
public static String deixarMaiusculo(String texto) {
    return texto.toUpperCase();
}

public static String deixarMinusculo(String texto) {
    return texto.toLowerCase();
}

public static boolean verificarPalavra(String texto, String palavra) {
    String textoNormalizado = texto.toLowerCase(Locale.ROOT);
    String palavraNormalizada = palavra.toLowerCase(Locale.ROOT);
    return textoNormalizado.contains(palavraNormalizada);
}
```

### Desafio extra

Permita que o usuário continue usando o menu até escolher a opção `0`.

---

## Checklist de estudo

Use esta lista para conferir se você praticou os principais pontos.

- [ ] Variáveis e tipos básicos.
- [ ] Entrada de dados com `Scanner`.
- [ ] Saída de dados com `System.out.println()`.
- [ ] Operadores aritméticos.
- [ ] Estruturas `if`, `else if` e `else`.
- [ ] Estrutura `switch`.
- [ ] Laço `for`.
- [ ] Laço `while`.
- [ ] Laço `do-while`.
- [ ] Métodos `static`.
- [ ] Parâmetros e retornos de métodos.
- [ ] Manipulação de `String`.
- [ ] Uso de `toLowerCase()`.
- [ ] Uso de `toUpperCase()`.
- [ ] Uso de `trim()`.
- [ ] Uso de `length()`.
- [ ] Uso de `contains()`.
- [ ] Uso de `equals()`.
- [ ] Uso de `equalsIgnoreCase()`.
- [ ] Uso de `replace()`.

---

## Sugestão de evolução

Depois de resolver os 10 exercícios, tente criar um único programa com menu principal:

```text
1 - Cadastro simples
2 - Conversor de temperatura
3 - Verificador de maioridade
4 - Sistema de notas
5 - Normalizador de nomes
6 - Verificador de palavra-chave
7 - Login simples
8 - Contador
9 - Tabuada
10 - Menu de textos
0 - Sair
```

Esse desafio ajuda a praticar organização de código, funções, condicionais e estruturas repetitivas em um projeto maior.
