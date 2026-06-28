# Java — orientação a objetos

Uma classe é uma representação ou um molde de algo. Ela pode conter atributos, que representam características e propriedades, e métodos, que definem comportamentos.

Exemplos comuns de classe em Java:

- **Entidades**: `Product`, `Client` e `Triangle`.
- **Serviços**: `ProductService`, `ClientService` e `EmailService`.
- **Controladores**: `ProductController` e `ClientController`.
- **Utilitários**: `Calculator`.
- **Repositórios**: `ProductRepository` e `ClientRepository`.

Além disso, podemos agrupar as classes dentro de pacotes (`packages`). Exemplo: `users`, com `controllers`, `services` e `repositories`.

## Modificadores de acesso

Os atributos e métodos possuem os seguintes modificadores de acesso:

- **`private`**: permite acesso somente dentro da classe que declarou o elemento.
- **Acesso de pacote** (*package-private*): ocorre quando nenhum modificador é declarado e permite acesso por código do mesmo pacote.
- **`protected`**: permite acesso no mesmo pacote e, sob as regras de herança, por subclasses em outros pacotes.
- **`public`**: permite acesso por outros tipos, respeitando também os limites definidos pelo sistema de módulos.

## Membros estáticos

Membros estáticos pertencem à própria classe e podem ser acessados sem a necessidade de instanciar um objeto.

Exemplo de uso:

```java
double n = Math.sqrt(64.0);
```

## Classe `Object`

Toda classe em Java herda de `Object`. Entre seus métodos estão:

- `getClass()`: retorna o objeto `Class` que representa a classe de execução.
- `equals()`: testa igualdade; a implementação de `Object` compara as referências.
- `hashCode()`: retorna um código *hash* compatível com o contrato de `equals()`.
- `toString()`: retorna uma representação textual do objeto.

Dessa forma, podemos sobrescrever `toString()` em nossa classe para representar seus objetos da maneira mais adequada.

## Construtores

Quando criamos classes, é comum usarmos construtores, que são responsáveis por inicializar um objeto com os valores informados. Por exemplo:

```java
public class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

O exemplo acima cria uma classe `Person` com dois atributos (`name` e `age`). O construtor recebe um nome e uma idade para inicializar o objeto. Portanto, ao instanciar essa classe, precisamos passar esses dados como argumentos:

```java
Person person = new Person("João", 30);
```

O operador `new` indica que estamos criando um novo objeto em Java.

> Conceitualmente, os objetos são alocados no *heap*, e a variável armazena uma referência. A JVM pode aplicar otimizações que alteram a alocação física.

### Palavra `this`

Usamos a palavra `this` para nos referirmos ao próprio objeto dentro da classe, de forma semelhante ao `self` usado em Python.

## Sobrecarga

A sobrecarga em Java permite declarar métodos com o mesmo nome e diferentes listas de parâmetros. Esse recurso é muito usado em construtores:

```java
class Product {
    private String name;
    private double price;
    private int quantity;

    public Product(String name, double price, int quantity) {
        this.name = name;
        this.price = price;
        this.quantity = quantity;
    }

    public Product(String name, double price) {
        this.name = name;
        this.price = price;
    }
}

public class Main {
    public static void main(String[] args) {
        Product p1 = new Product("TV", 2000, 2);
        Product p2 = new Product("PS5", 3500);
    }
}
```

No caso acima, estamos criando dois objetos `Product` usando duas versões sobrecarregadas do construtor.

## Encapsulamento

Encapsulamento consiste em ocultar detalhes internos e controlar as operações permitidas sobre o estado de um objeto. Isso não exige expor um método de leitura ou escrita para cada atributo.

### *Getters* e *setters*

Quando a interface da classe exige leitura ou alteração de um atributo, podemos fornecer *getters* e *setters*. Esses métodos também podem validar os novos valores:

```java
public class Product {
    private String name;
    private double price;

    public Product(String name, double price) {
        this.name = name;
        this.price = price;
    }

    public String getName() {
        return this.name;
    }

    public double getPrice() {
        return this.price;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setPrice(double price) {
        this.price = price;
    }
}
```

Agora, para acessar ou modificar um atributo, podemos usar:

```java
Product p = new Product("TV", 2000);
System.out.println(p.getName());
p.setPrice(2200);
```
