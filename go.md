# Golang

> Documentação: https://go.dev/doc/

---

## Instalações de ferramentas úteis

```bash
go install golang.org/x/tools/gopls@latest # LSP
go install honnef.co/go/tools/cmd/staticcheck@latest # Linter
```

---

## Estrutura lógica da linguagem Go

### Módulos

Em Go, um módulo representa o projeto ou biblioteca como um todo, definido pelo arquivo `go.mod`, contendo um ou mais pacotes e suas dependências.

Ele agrupa os pacotes do projeto, controla o caminho de importação, gerencia as dependências externas e define a versão do Go utilizada no projeto.

Uma boa prática para nomear módulos na linguagem, é utilizando o caminho do repositório de onde o projeto ou biblioteca está, por exemplo: `go mod init github.com/lcsgborges/projectGo`

### Pacotes

Em Go, um pacote representa um conjunto de arquivos `.go` em uma mesma pasta, com o mesmo nome de `package`, que agrupam código relacionado.

Cada pasta representa um pacote. Por isso, todos os arquivos `.go` dentro da mesma pasta normalmente devem declarar o mesmo `package`.

Existe um pacote especial chamado `package main`. Ele indica que aquele pacote será compilado como um executável.

Para que um `package main` seja executável, ele precisa possuir uma função `main`:

```go
func main() {
    // entrypoint da aplicação
}
```

A função `main` é o ponto de entrada da aplicação, ou seja, é onde a execução do programa começa.

---

## Hello World em Go

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello, World!")
}
```

---

## Compilação em Go

Go é uma linguagem compilada, ou seja, precisamos compilar nosso arquivo para que ele vire um executável e possa rodar na nossa máquina, para isso usamos o comando `go build`, por exemplo:

```bash
go build main.go -o main.exe
```

Nesse caso, passamos a Flag `-o` que significa _output_ e nosso arquivo executável passa a se chamar `main.exe` e para rodar ele, basta executar: `./main.exe`.

Além disso, em Go, conseguimos fazer Cross-Compilation, ou seja, podemos compilar um arquivo `.go` para outro sistema operacional diferente do nosso, passando o sistema operacional em que será executado e a arquitetura do processador, por exemplo:

```bash
# Vamos supor que estamos em uma máquina Linux e queremos compilar nosso código para Windows,
# podemos simplesmente passar o seguinte comando:
GOOS=windows GOARCH=amd64 go build main.go -o main.exe
```

Se quisermos testar o código rapidamente, podemos executar o comando `go run` para executar nosso código, mas o que ele faz por debaixo do pano é o seguinte: `Builda -> Executa o código -> Deleta o executável`. Para rodar, basta passar o comando e o nome do arquivo, exemplo: `go run main.go`

---

## Nomes públicos e privados

Em Go, podemos ter nomes públicos e privados, isso quer dizer que podemos ter variáveis e funções que são exportáveis ou não. A regra é a seguinte: se uma variável ou função começar com letra **minúscula**, ela é **privada**, se começar com letra maiúscula, vai ser **pública** e poderá ser importada por outros arquivos fora do `package` dela. Uma variável e função privada só podem ser usada dentro do mesmo `package`.

Ainda em `package`, podemos renomear a forma que nosso pacote será importado, por exemplo:

```go
package main

import io "fmt"

func main(
	io.Println("Hello")
)
```

No exemplo acima, renomeamos o nome do pacote "fmt", similar ao `as` usado em Python.

Podemos importar pacotes com `.` na frente, integrando ele ao nosso pacote principal, por exemplo, `import . "fmt"`, dessa forma, podemos usar `Println` sem precisar usar `fmt.Println`, mas não é recomendado.

Além disso, podemos usar o `_` para importar um pacote, serve para inicializar esse pacote, mas sem trazer as variáveis e funções pra dentro do pacote que chamou ele.

## Funções em Go

Uma função em Go é similar as outras linguagens, recebe um argumento, processa alguma coisa e retorna um resultado:

```go
func multi(a int, b int) int {
	return a * b
}
```

Podemos retornar mais de um valor, `func swap (a, b int) (int, int) {}` e também podemos nomear esses retornos, por exemplo,
`func div(a, b int) (result int, reminder int) {}`, e com isso nomeando o nome do retorno, podemos usar o padrão _Naked result_, em que podemos apenas usar `return` sem passar o nome do que será retornado, mas não é uma boa prática.

Podemos ter funções que retornam funções, chamadas de `Higher Order Function`, por exemplo:

```go
package main

import "fmt"

func main() {
	fmt.Printf("Result = %d\n", mult2(5))
}

func mult2 (x int) func() int {
	return func() int {
		return x * 2
	}
}
```

No exemplo acima, temos uma função `mult2` que recebe um número e seu retorno é uma função que pega esse número, multiplica por 2 e retorna o resultado. 

Essa função que consegue enxergar o valor acima, por exemplo `func() {return x * 2}`, ele enxerga o x da função `mult2`. Isso é chamado de `Closure Function`.

Ainda no exemplo de cima, temos uma função anônima dentro da `mult2`. Percebe-se que toda `closure function` é uma `anonymous function`, mas o contrário nem sempre é válido.

---

## Variáveis em Go

Em Go, as variáveis podem ser declaradas em **escopo de função** ou **escopo de pacote**. As funções em escopo de função são vistas apenas dentro daquela função e as de escpo de pacote são consideradas **variáveis globais** no pacote.

Ainda podemos declarar variáveis de algumas formas:

```go
var x int = 10 // Jeito tradicional
var x = 10 // Com inferência de tipo, o compilador sabe o tipo da variável ao compilar
x := 10 // Short syntax (apenas em escopo de função, em escopo de pacote não vai dar certo)

var name, lastname string // Variáveis do mesmo tipo 'string' sem iniciar os valores
```

Não podemos declarar apenas como `var name`, pois nesse caso, o compilador não sabe o tipo da variável. Na mesma linha, podemos declarar variáveis de tipos diferentes, caso elas já sejam inicializadas com valor: `var name, age = "João", 10`.

---

## Tipos em Go

Em Go, temos alguns tipos bem comuns:

### Inteiros com sinal

- `int`: é o padrão e depende da arquitetura, podendo ser int32 ou int64
- `int8`
- `int16`
- `int32` = `rune`
- `int64`

### Inteiros sem sinal (unsigned, ou seja, positivos apenas)

- `uint`: segue o mesmo padrão da arquitetura do int
- `uint8` = `byte`
- `uint16`
- `uint32`
- `uint64`

### Números decimais

- `float32`
- `float64`

### Números complexos

- `complex64`
- `complex128`

### Strings

- `string`

### Booleanos

- `bool`

### Casting de tipos

Podemos ainda transformar tipos em outros, caso seja possível, exemplo:

```go
func main() {
    var x = 10
    var y = float32(10)
}
```

No exemplo acima, estamos pegando um tipo `int` e transformando ele em `float32`

Com strings, pegamos o `int` e pegamos o código dele em ASCII, exemplo: `string(65) = "A"`

---

## Constantes em Go

Em Go, as constantes não podem ter seus valores alterados depois de inicializadas e não podem ser usadas com **short syntax**: `const x int = 10`

Apenas alguns tipos podem ser constantes, geralmente valores numéricos, strings e booleanos.

---

## Arrays em Go

Em Go, arrays são estruturas sequencias em memória. Os arrays precisam ter tamanho fixo e não podem ter seu tamanho alterado ao decorrer do código. Além disso, o valor do tamanho do array precisa ser uma `literal constant, e.g: 10` ou declarado com `const`, exemplo: 

```go
func main() {
	const x = 10
	arr := [x]int{}
}
```

Dessa forma é válida, pois x é uma constante. Outra coisa, os valores podem ou não ser inicializados na definição do array, sendo possível alterar esses valores depois. Podemos também passar apenas um valor específico pra uma posição especifíca, exemplo: `arr := [10]int{5: 200}`, nesse caso, `arr[5] = 200`

---
