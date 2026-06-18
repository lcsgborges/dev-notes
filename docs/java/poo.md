# Java - Orientação a Objetos

Basicamente, uma classe é uma representação de algo, um molde. Pode conter atributos (características e propriedades) e métodos (são funções).

Exemplos comuns de classe em Java:

- **Entidades**: `Product`, `Client`, `Triangle`
- **Serviços**: `ProductService`, `ClientService`, `EmailService`
- **Controladores**: `ProductController`, `ClientController`
- **Utilitários**: `Calculator`
- **Repositórios**: `ProductRepository`, `ClientRepository`

Além disso, podemos agrupar as classes dentros de pacotes (`packages`). Exemplo: `users`, com `controllers`, `services`, `repositories`, ...

## Modificadores de acesso

Os atributos e métodos possuem modificadores de acesso e são eles:

- **private**: elementos só podem ser acessados dentro da própria classe onde foram declarados
- **default (package-private)**: aplicado automaticamente quando nenhum modificador é declarado. O elemento fica visível apenas para classes pertencentes ao mesmo pacote
- **protected**: permite o acesso dentro do próprio pacote e também por classes filhas (subclasses) que estejam em outros pacotes
- **public**: sem restrições. Pode ser acessado de qualquer lugar, por qualquer classe e em qualquer pacote

## Membros estáticos

Membros estáticos são aqueles que são acessados pela própria classe sem a necessidade de instanciar um objeto para usar o atributo ou método

Exemplo de uso:

```java
double n = Math.sqrt(64.0);
```

## Classe Object

Toda classe em Java herda de Object. Ele possui os seguintes métodos:

- `getClass`: retorna o tipo do objeto
- `equals`: compara se o objeto é igual a outro
- `hashCode`: retorna um código hash do objeto
- `toString`: converte o objeto para String

Dessa forma, podemos implementar nosso próprio toString na nossa classe de modo a representá-la da melhor forma

## Construtores

Quando criamos novas classes, é comum usarmos métodos construtores (são responsáveis por instanciar um objeto com os valores passados), por exemplo:

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

O exemplo acima cria uma classe `Person` que possui 2 atributos (`name` e `age`) e o método construtor pede um nome e uma idade para poder criar o objeto, dessa forma, quando vamos instanciar essa classe, precisamos passar esses dados como argumentos:

```java
Person person = new Person("João", 30);
```

O `new` é a forma que declaramos que estamos criando um novo objeto em java (dentro da `Heap`)

> Por padrão, objetos são criados na Heap em Java e a variável falamos que é do tipo referência (um ponteiro para Heap), é basicamente pensarmos em C.

### Palavra `this`

Usamos a palavra `this` para se referir ao próprio objeto em si dentro da classe, semelhante ao `self` que usamos em Python

## Sobrecarga

A sobrecarga em Java é um mesmo método poder receber diferentes argumentos, muito usado em construtores:

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

No caso acima, estamos criando dois objetos `Product`, mas com sobrecarga no método construtor

## Encapsulamento

Encapsulamento é você proteger os dados internos da classe. Ocultamos o detalhe de implementação e retringimos o acesso direto aos atributos, premitindo que eles sejam acessados e modificados de outras formas:

### Getters e Setters

Para modificar/acessar um atributo, utilizamos geralmente `getters` e `setters`:

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

Agora ao acessar um atributo, podemos usar:

```java
Product p = new Product("TV", 2000);
System.out.print(p.getName());
p.setPrice(2200);
```
