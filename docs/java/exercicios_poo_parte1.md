# Lista de Exercícios — Java POO (Parte I)

## Objetivo

Esta lista reúne exercícios sobre os seguintes assuntos:

- Introdução à Programação Orientada a Objetos
- Classes e objetos
- Atributos e métodos
- Reaproveitamento e delegação
- `toString()`
- Membros estáticos
- Construtores
- Palavra `this`
- Sobrecarga
- Encapsulamento
- Modificadores de acesso
- Tipos por valor e tipos por referência
- Garbage Collector e escopo local
- Vetores
- Boxing, unboxing e wrapper classes
- Laço `for each`
- Listas
- Matrizes

---

## 1. Classe `Triangulo`

Crie uma classe chamada `Triangulo` com três atributos:

```java
double a;
double b;
double c;
```

No programa principal, crie dois triângulos, leia os lados de cada um e calcule a área usando a fórmula de Heron:

```text
p = (a + b + c) / 2
area = √(p * (p - a) * (p - b) * (p - c))
```

Mostre qual triângulo possui a maior área.

---

## 2. Classe `Produto`

Crie uma classe `Produto` com os atributos:

```java
String nome;
double preco;
int quantidade;
```

Crie métodos para:

```java
double valorTotalEmEstoque()
void adicionarProdutos(int quantidade)
void removerProdutos(int quantidade)
```

No `main`, crie um produto, adicione unidades, remova unidades e mostre o valor total em estoque.

---

## 3. Método `toString()`

Aproveite a classe `Produto` do exercício anterior e sobrescreva o método `toString()` para exibir os dados do produto no seguinte formato:

```text
Produto: TV, R$ 900.00, 10 unidades, Total: R$ 9000.00
```

Depois, no `main`, imprima diretamente o objeto:

```java
System.out.println(produto);
```

---

## 4. Construtor Simples

Crie uma classe `Pessoa` com:

```java
String nome;
int idade;
double altura;
```

Crie um construtor que receba todos os dados da pessoa.

Depois, no `main`, instancie três pessoas diferentes e mostre seus dados.

---

## 5. Sobrecarga de Construtores

Crie uma classe `Aluno` com:

```java
String nome;
double nota1;
double nota2;
double nota3;
```

Crie dois construtores:

```java
Aluno(String nome)
Aluno(String nome, double nota1, double nota2, double nota3)
```

Crie também um método:

```java
double media()
```

No `main`, teste os dois construtores.

---

## 6. Palavra `this`

Crie uma classe `Carro` com os atributos:

```java
String modelo;
String marca;
int ano;
```

Crie um construtor usando `this` para diferenciar os atributos dos parâmetros.

Exemplo:

```java
public Carro(String modelo, String marca, int ano) {
    this.modelo = modelo;
    this.marca = marca;
    this.ano = ano;
}
```

Depois, crie alguns carros no `main` e imprima os dados.

---

## 7. Encapsulamento com `private`

Crie uma classe `Funcionario` com atributos privados:

```java
private String nome;
private double salario;
```

Crie os métodos:

```java
getNome()
setNome()
getSalario()
aumentarSalario(double percentual)
```

Não permita alterar o salário diretamente com `setSalario`.

No `main`, crie um funcionário, aumente o salário e mostre o resultado.

---

## 8. Conta Bancária

Crie uma classe `ContaBancaria` com:

```java
private int numero;
private String titular;
private double saldo;
```

Crie métodos:

```java
depositar(double valor)
sacar(double valor)
getSaldo()
```

Regras:

```text
- O saldo não pode ser alterado diretamente.
- O saque deve cobrar uma taxa de R$ 5.00.
- Não permita saque se o saldo for insuficiente.
```

---

## 9. Modificadores de Acesso

Crie uma classe `Produto` usando:

```java
private String nome;
private double preco;
private int quantidade;
```

Depois tente acessar os atributos diretamente no `main`:

```java
produto.preco = 1000.0;
```

Observe o erro.

Depois corrija usando métodos públicos:

```java
getPreco()
setPreco()
```

No `setPreco`, não permita preço menor ou igual a zero.

---

## 10. Tipos por Valor e Tipos por Referência

Crie um programa com duas partes.

Parte 1:

```java
int x = 10;
int y = x;
y = 20;
```

Mostre `x` e `y`.

Parte 2:

```java
Produto p1 = new Produto("TV", 900.0);
Produto p2 = p1;
p2.setPreco(1200.0);
```

Mostre o preço de `p1` e `p2`.

Explique com comentário no código por que o comportamento é diferente.

---

## 11. Vetor de Números

Leia um número `N` e depois leia `N` números inteiros.

Armazene os valores em um vetor:

```java
int[] numeros = new int[N];
```

Depois mostre:

```text
- Todos os números
- A soma
- A média
- O maior número
- O menor número
```

---

## 12. Vetor de Objetos

Crie uma classe `Produto` com:

```java
private String nome;
private double preco;
```

No `main`, leia `N` produtos e armazene em:

```java
Produto[] produtos = new Produto[N];
```

Depois calcule e mostre o preço médio dos produtos.

---

## 13. Boxing, Unboxing e Wrapper Classes

Crie um programa que demonstre:

```java
int x = 20;
Integer obj = x; // boxing
int y = obj;     // unboxing
```

Depois crie uma lista:

```java
List<Integer> numeros = new ArrayList<>();
```

Adicione números à lista e mostre a soma dos valores.

Explique em comentário por que usamos `Integer` e não `int` dentro de `List`.

---

## 14. Laço `for each`

Crie uma lista de nomes:

```java
List<String> nomes = new ArrayList<>();
```

Adicione pelo menos cinco nomes.

Depois percorra a lista usando:

```java
for (String nome : nomes) {
    System.out.println(nome);
}
```

Em seguida, mostre apenas os nomes que possuem mais de cinco letras.

---

## 15. Listas — Parte 1

Crie uma lista de números inteiros:

```java
List<Integer> numeros = new ArrayList<>();
```

Faça as seguintes operações:

```text
- Adicionar 10 números
- Remover um número pelo índice
- Remover um número pelo valor
- Mostrar o tamanho da lista
- Verificar se um número existe na lista
- Mostrar todos os números
```

---

## 16. Listas — Parte 2 com Objetos

Crie uma classe `Pessoa` com:

```java
private String nome;
private int idade;
```

No `main`, crie:

```java
List<Pessoa> pessoas = new ArrayList<>();
```

Adicione várias pessoas.

Depois mostre:

```text
- Todas as pessoas
- Apenas pessoas maiores de idade
- A pessoa mais velha
- A média das idades
```

---

## 17. Remoção Condicional em Lista

Crie uma lista de funcionários com:

```java
String nome;
double salario;
```

Remova da lista todos os funcionários que ganham menos que R$ 2000.00.

Use:

```java
removeIf(...)
```

Depois mostre a lista atualizada.

---

## 18. Matriz de Inteiros

Leia dois valores:

```java
int linhas;
int colunas;
```

Crie uma matriz:

```java
int[][] matriz = new int[linhas][colunas];
```

Leia os valores da matriz e depois mostre:

```text
- A matriz completa
- A soma de todos os elementos
- A diagonal principal, se a matriz for quadrada
- Os números negativos
```

---

## 19. Matriz de Notas

Crie uma matriz para armazenar notas de alunos.

Cada linha representa um aluno e cada coluna representa uma nota.

Exemplo:

```java
double[][] notas = new double[3][4];
```

Depois calcule:

```text
- Média de cada aluno
- Maior nota de cada aluno
- Média geral da turma
```

---

## 20. Sistema Simples de Cadastro

Crie um pequeno sistema de cadastro de produtos usando `List<Produto>`.

A classe `Produto` deve ter:

```java
private String nome;
private double preco;
private int quantidade;
```

O sistema deve permitir:

```text
1. Cadastrar produto
2. Listar produtos
3. Buscar produto pelo nome
4. Adicionar estoque
5. Remover estoque
6. Mostrar valor total em estoque
7. Sair
```

Use:

```java
Scanner
ArrayList
Construtores
Getters e setters
Encapsulamento
toString()
```

Esse exercício junta praticamente todos os assuntos das seções.
