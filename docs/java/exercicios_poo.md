# Lista de Exercícios - Java POO

## Objetivo

Esta lista reúne exercícios somente sobre Programação Orientada a Objetos em Java:

- Classes e objetos
- Atributos e métodos
- Construtores
- Palavra `this`
- Sobrecarga
- Encapsulamento
- Modificadores de acesso
- Método `toString()`
- Membros estáticos
- Tipos por valor e tipos por referência
- Objetos relacionados por composição simples

## Regras gerais

- Não use arrays.
- Não use listas.
- Não use matrizes.
- Não use `ArrayList`, `List`, `Map`, `Set` ou qualquer outra coleção.
- Não use `for each`.
- Quando precisar testar mais de um objeto, crie variáveis separadas, como `produto1`, `produto2`, `conta1` e `conta2`.

---

## 1. Classe `Triangulo`

Crie uma classe chamada `Triangulo` com três atributos:

```java
private double a;
private double b;
private double c;
```

Crie um construtor que receba os três lados do triângulo.

Crie os métodos:

```java
double area()
String maiorArea(Triangulo outroTriangulo)
```

No método `area()`, use a fórmula de Heron:

```text
p = (a + b + c) / 2
area = raizQuadrada(p * (p - a) * (p - b) * (p - c))
```

No `main`, crie dois triângulos usando variáveis separadas, calcule a área de cada um e mostre qual possui a maior área.

---

## 2. Classe `Produto`

Crie uma classe `Produto` com os atributos:

```java
private String nome;
private double preco;
private int quantidade;
```

Crie um construtor que receba todos os dados do produto.

Crie métodos para:

```java
double valorTotalEmEstoque()
void adicionarProdutos(int quantidade)
void removerProdutos(int quantidade)
```

Regras:

- Não permita preço menor ou igual a zero.
- Não permita quantidade negativa.
- Não permita remover mais produtos do que existe em estoque.

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

## 4. Construtor simples

Crie uma classe `Pessoa` com:

```java
private String nome;
private int idade;
private double altura;
```

Crie um construtor que receba todos os dados da pessoa.

Crie os métodos:

```java
String getNome()
int getIdade()
double getAltura()
void fazerAniversario()
boolean maiorDeIdade()
```

Depois, no `main`, instancie três pessoas diferentes usando variáveis separadas e mostre os dados de cada uma.

---

## 5. Sobrecarga de construtores

Crie uma classe `Aluno` com:

```java
private String nome;
private double nota1;
private double nota2;
private double nota3;
```

Crie dois construtores:

```java
Aluno(String nome)
Aluno(String nome, double nota1, double nota2, double nota3)
```

Crie também os métodos:

```java
double media()
boolean aprovado()
```

Regras:

- A média deve considerar as três notas.
- O aluno é aprovado se a média for maior ou igual a `7.0`.

No `main`, teste os dois construtores.

---

## 6. Palavra `this`

Crie uma classe `Carro` com os atributos:

```java
private String modelo;
private String marca;
private int ano;
```

Crie um construtor usando `this` para diferenciar os atributos dos parâmetros:

```java
public Carro(String modelo, String marca, int ano) {
    this.modelo = modelo;
    this.marca = marca;
    this.ano = ano;
}
```

Crie também o método:

```java
void atualizarAno(int ano)
```

Regra:

- O ano só deve ser atualizado se for maior que o ano atual do carro.

Depois, crie dois carros no `main` usando variáveis separadas e imprima os dados.

---

## 7. Encapsulamento com `private`

Crie uma classe `Funcionario` com atributos privados:

```java
private String nome;
private double salario;
```

Crie um construtor que receba `nome` e `salario`.

Crie os métodos:

```java
String getNome()
void setNome(String nome)
double getSalario()
void aumentarSalario(double percentual)
```

Regras:

- Não crie `setSalario`.
- O salário só pode mudar pelo método `aumentarSalario`.
- O percentual deve ser maior que zero.

No `main`, crie um funcionário, aumente o salário e mostre o resultado.

---

## 8. Conta bancária

Crie uma classe `ContaBancaria` com:

```java
private int numero;
private String titular;
private double saldo;
```

Crie dois construtores:

```java
ContaBancaria(int numero, String titular)
ContaBancaria(int numero, String titular, double depositoInicial)
```

Crie métodos:

```java
void depositar(double valor)
void sacar(double valor)
double getSaldo()
```

Regras:

- O saldo não pode ser alterado diretamente.
- O saque deve cobrar uma taxa de `R$ 5.00`.
- Não permita depósito menor ou igual a zero.
- Não permita saque se o saldo for insuficiente.

No `main`, crie uma conta, faça um depósito, faça um saque e mostre o saldo final.

---

## 9. Modificadores de acesso

Crie uma classe `Livro` usando:

```java
private String titulo;
private String autor;
private int paginas;
```

Crie um construtor que receba todos os dados.

Crie os métodos:

```java
String getTitulo()
String getAutor()
int getPaginas()
void setTitulo(String titulo)
void setAutor(String autor)
void setPaginas(int paginas)
```

Regras:

- O título não pode ser vazio.
- O autor não pode ser vazio.
- A quantidade de páginas deve ser maior que zero.

Depois tente acessar um atributo diretamente no `main`:

```java
livro.titulo = "Java";
```

Observe o erro e corrija usando os métodos públicos.

---

## 10. Tipos por valor e tipos por referência

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
Produto produto1 = new Produto("TV", 900.0, 5);
Produto produto2 = produto1;
produto2.adicionarProdutos(3);
```

Mostre os dados de `produto1` e `produto2`.

Explique em um comentário no código por que os dois produtos ficaram com o mesmo estoque.

---

## 11. Membros estáticos

Crie uma classe `ConversorMoeda` com:

```java
public static final double IOF = 0.06;
```

Crie o método estático:

```java
public static double dolarParaReal(double cotacaoDolar, double quantidadeDolar)
```

Regra:

- O valor final deve incluir `6%` de IOF.

No `main`, leia a cotação do dólar, leia a quantidade de dólares e mostre o valor em reais.

---

## 12. Objeto usando outro objeto

Crie uma classe `Endereco` com os atributos:

```java
private String rua;
private String cidade;
private String estado;
```

Crie uma classe `Cliente` com os atributos:

```java
private String nome;
private String email;
private Endereco endereco;
```

Crie construtores para as duas classes.

Na classe `Cliente`, crie o método:

```java
void alterarEndereco(Endereco endereco)
```

No `main`, crie um endereço, crie um cliente usando esse endereço e depois altere o endereço do cliente para outro objeto `Endereco`.

Não use arrays, listas ou matrizes para armazenar os endereços.
