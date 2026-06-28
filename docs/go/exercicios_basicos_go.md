# Exercícios de Go — primeiros passos

Conteúdos cobertos:

- Pacotes.
- Nomes exportados e não exportados.
- Funções.
- Variáveis.
- Sistema de tipos e constantes.
- *Arrays*.
- Laços de repetição.
- Condicionais (`if`).
- Estruturas `switch`.
- `defer`.

## 1. Pacotes

### Exercício 1 — usando pacotes básicos

Crie um programa que importe os pacotes `fmt` e `strings`.

O programa deve:

1. Criar uma variável com seu nome completo em letras minúsculas.
2. Transformar o nome para letras maiúsculas usando `strings.ToUpper`.
3. Exibir no terminal:

```txt
Nome original: lucas guimaraes
Nome formatado: LUCAS GUIMARAES
```

### Exercício 2 — usando o pacote `math`

Crie um programa que calcule a raiz quadrada de um número.

Regras:

1. Importe `fmt` e `math`.
2. Crie uma variável `numero` com valor `64.0`.
3. Use `math.Sqrt`.
4. Exiba:

```txt
A raiz quadrada de 64 é 8
```

---

## 2. Nomes exportados e não exportados

Em Go, identificadores iniciados por uma letra maiúscula Unicode são **exportados**. Os demais ficam restritos ao próprio pacote.

### Exercício 3 — criando um pacote próprio

Crie esta estrutura:

```txt
ex03/
  main.go
  mensagens/
    mensagens.go
```

Inicialize um módulo dentro de `ex03`, por exemplo, com `go mod init example.com/ex03`. Esse caminho será usado para importar o pacote `mensagens` em `main.go`.

No arquivo `mensagens/mensagens.go`, crie:

```go
package mensagens
```

Dentro desse pacote:

1. Crie uma função pública chamada `Saudacao`.
2. Ela deve receber um nome.
3. Ela deve chamar uma função privada chamada `formatarNome`.
4. A função privada deve colocar `"Olá, "` antes do nome.
5. No `main.go`, chame apenas a função pública `Saudacao`.

Resultado esperado:

```txt
Olá, Lucas
```

Atenção: tente chamar `formatarNome` diretamente no `main.go` e veja o erro que o Go mostra.

---

## 3. Funções

### Exercício 4 — função simples

Crie uma função chamada `somar`.

Ela deve:

1. Receber dois números inteiros.
2. Retornar a soma deles.
3. Ser chamada dentro da função `main`.

Exemplo:

```txt
Resultado: 15
```

### Exercício 5 — função com múltiplos retornos

Crie uma função chamada `calcular`.

Ela deve receber dois números inteiros e retornar:

1. Soma.
2. Subtração.
3. Multiplicação.
4. Divisão.

Exemplo:

```txt
Soma: 20
Subtração: 10
Multiplicação: 75
Divisão: 3
```

Use os números `15` e `5`.

### Exercício 6 — função de validação

Crie uma função chamada `maiorDeIdade`.

Ela deve:

1. Receber uma idade.
2. Retornar `true` se a idade for maior ou igual a `18`.
3. Retornar `false` caso contrário.

Depois exiba:

```txt
É maior de idade? true
```

---

## 4. Variáveis

### Exercício 7 — declaração de variáveis

Crie um programa usando três formas diferentes de declarar variáveis:

```go
var nome string
var idade int = 25
cidade := "Brasília"
```

Depois exiba uma frase como:

```txt
José tem 25 anos e mora em Brasília
```

### Exercício 8 — valores zero

Crie variáveis sem atribuir valor inicial:

```go
var nome string
var idade int
var ativo bool
var salario float64
```

Exiba todas no terminal e observe os valores padrão.

Resultado esperado semelhante a:

```txt
Nome:
Idade: 0
Ativo: false
Salário: 0
```

### Exercício 9 — troca de valores

Crie duas variáveis:

```go
a := 10
b := 20
```

Troque os valores entre elas sem criar uma terceira variável.

Resultado esperado:

```txt
Antes:
a = 10
b = 20

Depois:
a = 20
b = 10
```

---

## 5. Sistema de tipos e constantes

### Exercício 10 — tipos básicos

Crie variáveis dos seguintes tipos:

1. `string`.
2. `int`.
3. `float64`.
4. `bool`.

Depois exiba o valor e o tipo de cada uma usando `fmt.Printf` com `%T`.

Exemplo:

```txt
Lucas é do tipo string
20 é do tipo int
1.75 é do tipo float64
true é do tipo bool
```

### Exercício 11 — conversão de tipos

Crie:

```go
idade := 25
altura := 1.75
```

Depois converta `idade` para `float64` e some com `altura`.

Exiba:

```txt
Resultado: 26.75
```

Atenção: Go não soma `int` com `float64` diretamente.

### Exercício 12 — constantes

Crie uma constante chamada `pi` com valor `3.14`.

Depois crie uma variável `raio := 5.0`.

Calcule a área do círculo:

```txt
área = pi * raio * raio
```

Resultado esperado:

```txt
Área do círculo: 78.5
```

### Exercício 13 — constantes para estados

Crie constantes para representar status de pedido:

```go
const (
    StatusPendente = "PENDENTE"
    StatusPago = "PAGO"
    StatusCancelado = "CANCELADO"
)
```

Crie uma variável `statusAtual` e exiba uma mensagem dependendo do status.

Exemplo:

```txt
Pedido aguardando pagamento
```

---

## 6. Arrays

### Exercício 14 — *array* de números

Crie um *array* com cinco números inteiros.

Depois:

1. Exiba o array inteiro.
2. Exiba o primeiro elemento.
3. Exiba o último elemento.
4. Altere o segundo elemento.
5. Exiba o array atualizado.

Exemplo:

```txt
Array original: [10 20 30 40 50]
Primeiro: 10
Último: 50
Array atualizado: [10 99 30 40 50]
```

### Exercício 15 — média de notas com *array*

Crie um *array* com quatro notas:

```go
notas := [4]float64{8.5, 7.0, 9.2, 6.8}
```

Calcule a média das notas usando um laço.

Resultado esperado:

```txt
Média: 7.875
```

Depois, melhore a exibição para mostrar apenas duas casas decimais:

```txt
Média: 7.88
```

---

## 7. Laços de repetição

### Exercício 16 — contador simples

Use um `for` para exibir os números de `1` até `10`.

Resultado:

```txt
1
2
3
4
5
6
7
8
9
10
```

### Exercício 17 — soma de números

Crie um programa que some todos os números de `1` até `100`.

Resultado esperado:

```txt
Soma total: 5050
```

### Exercício 18 — percorrendo um *array* com `range`

Crie um *array* de nomes:

```go
nomes := [4]string{"Lucas", "Ana", "Carlos", "Marina"}
```

Use `range` para exibir:

```txt
Índice 0: Lucas
Índice 1: Ana
Índice 2: Carlos
Índice 3: Marina
```

### Exercício 19 — números pares

Use um laço para exibir apenas os números pares de `1` até `50`.

Resultado esperado:

```txt
2
4
6
...
50
```

---

## 8. Condicionais (`if`)

### Exercício 20 — verificador de nota

Crie uma variável:

```go
nota := 7.5
```

Regras:

- Se a nota for maior ou igual a `7`, exiba `"Aprovado"`.
- Se for maior ou igual a `5` e menor que `7`, exiba `"Recuperação"`.
- Se for menor que `5`, exiba `"Reprovado"`.

### Exercício 21 — *login* simples

Crie duas constantes:

```go
const usuarioCorreto = "admin"
const senhaCorreta = "123456"
```

Depois crie duas variáveis:

```go
usuario := "admin"
senha := "123456"
```

Se o usuário e a senha estiverem corretos, exiba:

```txt
Login realizado com sucesso
```

Caso contrário:

```txt
Usuário ou senha inválidos
```

### Exercício 22 — par ou ímpar

Crie uma variável `numero`.

Use `if` para verificar se o número é par ou ímpar.

Exemplo:

```txt
O número 10 é par
```

---

## 9. Estruturas `switch`

### Exercício 23 — dia da semana

Crie uma variável:

```go
dia := 3
```

Use `switch` para exibir o dia correspondente:

```txt
1 - Domingo
2 - Segunda
3 - Terça
4 - Quarta
5 - Quinta
6 - Sexta
7 - Sábado
```

Resultado esperado:

```txt
Terça
```

### Exercício 24 — menu de opções

Crie uma variável:

```go
opcao := 2
```

Use `switch` para simular um menu:

```txt
1 - Cadastrar usuário
2 - Buscar usuário
3 - Atualizar usuário
4 - Deletar usuário
```

Se `opcao` for `2`, exiba:

```txt
Buscando usuário...
```

Se a opção não existir, exiba:

```txt
Opção inválida
```

### Exercício 25 — `switch` sem expressão

Crie uma variável:

```go
idade := 20
```

Use um `switch` sem expressão, assim:

```go
switch {
case idade < 12:
    // ...
case idade < 18:
    // ...
default:
    // ...
}
```

Regras:

- Menor que 12: `"Criança"`.
- Menor que 18: `"Adolescente"`.
- Caso contrário: `"Adulto"`.

---

## 10. `defer`

### Exercício 26 — entendendo a ordem de `defer`

Crie um programa com:

```go
fmt.Println("Início")
defer fmt.Println("Primeiro defer")
defer fmt.Println("Segundo defer")
fmt.Println("Fim")
```

Antes de rodar, tente responder mentalmente qual será a ordem da saída.

Depois, execute o programa e veja o resultado.

### Exercício 27 — simulando a abertura e o fechamento de um recurso

Crie uma função chamada `processarArquivo`.

Dentro dela:

1. Exiba `"Abrindo arquivo..."`.
2. Use `defer` para exibir `"Fechando arquivo..."`.
3. Exiba `"Processando arquivo..."`.

Resultado esperado:

```txt
Abrindo arquivo...
Processando arquivo...
Fechando arquivo...
```

### Exercício 28 — `defer` dentro de uma função

Crie duas funções:

```go
func primeira() {
    defer fmt.Println("Fim da primeira")
    fmt.Println("Início da primeira")
}

func segunda() {
    defer fmt.Println("Fim da segunda")
    fmt.Println("Início da segunda")
}
```

Chame as duas dentro do `main`.

Observe que a chamada adiada por `defer` é executada quando a função em que foi declarada termina, não apenas no final do programa.

---

## Desafios integrando vários assuntos

### Desafio 1 — calculadora simples

Crie uma calculadora usando:

- Variáveis.
- Funções.
- `switch`.
- Tipos numéricos.

Regras:

1. Crie duas variáveis `a` e `b`.
2. Crie uma variável `operacao`.
3. Use `switch` para decidir a operação.
4. Crie funções para:
   - Somar.
   - Subtrair.
   - Multiplicar.
   - Dividir, tratando o divisor igual a zero.

Exemplo:

```go
a := 10.0
b := 5.0
operacao := "+"
```

Resultado:

```txt
Resultado: 15
```

### Desafio 2 — sistema de notas

Crie um programa que tenha:

- *Array* de notas.
- Laço de repetição.
- Função para calcular a média.
- `if` para verificar a situação.

Regras:

1. Crie um array com 4 notas.
2. Crie uma função `calcularMedia`.
3. Se a média for maior ou igual a 7: aprovado.
4. Se a média for maior ou igual a 5: recuperação.
5. Caso contrário: reprovado.

Exemplo:

```txt
Média: 7.88
Situação: Aprovado
```

### Desafio 3 — simulador de pedido

Crie constantes:

```go
const (
    StatusCriado = "CRIADO"
    StatusPago = "PAGO"
    StatusEnviado = "ENVIADO"
    StatusEntregue = "ENTREGUE"
    StatusCancelado = "CANCELADO"
)
```

Crie uma variável `status`.

Use `switch` para exibir a mensagem correspondente:

```txt
Pedido criado, aguardando pagamento
Pagamento confirmado
Pedido enviado
Pedido entregue
Pedido cancelado
Status desconhecido
```

### Desafio 4 — relatório de produtos

Crie *arrays* separados:

```go
produtos := [3]string{"Mouse", "Teclado", "Monitor"}
precos := [3]float64{50.0, 120.0, 900.0}
```

Use um laço para exibir:

```txt
Produto: Mouse - Preço: R$ 50.00
Produto: Teclado - Preço: R$ 120.00
Produto: Monitor - Preço: R$ 900.00
```

Depois calcule o total dos preços.

Resultado esperado:

```txt
Total: R$ 1070.00
```

### Desafio 5 — minissistema bancário

Use tudo que aprendeu até aqui.

Crie:

```go
saldo := 1000.0
operacao := "saque"
valor := 250.0
```

Regras:

- Se a operação for `"deposito"`, adicione ao saldo.
- Se for `"saque"`, verifique se há saldo suficiente.
- Se tiver saldo, subtraia.
- Se não tiver, exiba `"Saldo insuficiente"`.
- Use `switch` para escolher a operação.
- Use `defer` para exibir `"Operação finalizada"` no fim da função.

Exemplo:

```txt
Saldo anterior: R$ 1000.00
Saque realizado: R$ 250.00
Saldo atual: R$ 750.00
Operação finalizada
```
