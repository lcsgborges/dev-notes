# Java - Orientação a Objetos

Uma classe é uma representação ou um molde de algo. Ela pode conter atributos, que representam características e propriedades, e métodos, que definem comportamentos.

Exemplos comuns de classe em Java:

- **Entidades**: `Product`, `Client`, `Triangle`
- **Serviços**: `ProductService`, `ClientService`, `EmailService`
- **Controladores**: `ProductController`, `ClientController`
- **Utilitários**: `Calculator`
- **Repositórios**: `ProductRepository`, `ClientRepository`

Além disso, podemos agrupar as classes dentro de pacotes (`packages`). Exemplo: `users`, com `controllers`, `services` e `repositories`.

## Modificadores de acesso

Os atributos e métodos possuem os seguintes modificadores de acesso:

- **private**: elementos só podem ser acessados dentro da própria classe onde foram declarados
- **default (package-private)**: aplicado automaticamente quando nenhum modificador é declarado. O elemento fica visível apenas para classes pertencentes ao mesmo pacote
- **protected**: permite o acesso dentro do próprio pacote e também por classes filhas (subclasses) que estejam em outros pacotes
- **public**: sem restrições. Pode ser acessado de qualquer lugar, por qualquer classe e em qualquer pacote

## Membros estáticos

Membros estáticos pertencem à própria classe e podem ser acessados sem a necessidade de instanciar um objeto.

Exemplo de uso:

```java
double n = Math.sqrt(64.0);
```

## Classe Object

Toda classe em Java herda de `Object`. Entre seus métodos estão:

- `getClass()`: retorna o tipo do objeto
- `equals()`: compara se o objeto é igual a outro
- `hashCode()`: retorna um código hash do objeto
- `toString()`: converte o objeto em uma representação textual

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

> Em geral, os objetos são alocados na heap, e a variável armazena uma referência ao objeto.

### Palavra `this`

Usamos a palavra `this` para nos referirmos ao próprio objeto dentro da classe, de forma semelhante ao `self` usado em Python.

## Sobrecarga

A sobrecarga em Java permite declarar métodos com o mesmo nome e diferentes listas de parâmetros. Esse recurso é muito usado em construtores:

```java
public class Product {
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

Encapsulamento consiste em proteger os dados internos da classe. Ocultamos os detalhes de implementação e restringimos o acesso direto aos atributos, permitindo que sejam acessados e modificados de outras formas.

### Getters e Setters

Para acessar ou modificar um atributo, geralmente utilizamos `getters` e `setters`:

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
System.out.print(p.getName());
p.setPrice(2200);
```
