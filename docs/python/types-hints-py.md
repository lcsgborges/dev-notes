# Anotações de tipo em Python

> Exemplos compatíveis com Python 3.13 ou superior.

O interpretador não usa as anotações para impor os tipos de argumentos e variáveis. Elas servem principalmente a ferramentas de análise estática, editores e leitores do código:

- Permitir que ferramentas como `mypy` e Pyright encontrem incompatibilidades antes da execução.
- Melhorar o preenchimento automático e a documentação exibida pelos editores.
- Registrar as expectativas da interface do código.
- Detectar erros simples, como passar um `int` quando a função espera uma `str`.

## Tipos básicos

```python
name: str = "Lucas Borges"
x: int = 10
y: float = 20.5
is_valid: bool = False
data: bytes = b"weather"
```

## Constantes

Python não impede a reatribuição de uma variável apenas porque seu nome está em letras maiúsculas; essa escrita é uma convenção. Quando a anotação explícita não acrescenta informação, podemos deixar que a ferramenta infira o tipo:

```python
CONSTANT = "qualquer valor"
```

## Coleções

Geralmente, são tipos que contêm mais de um valor, como listas, tuplas e dicionários:

```python
list_nums: list[int] = [1, 2, 3]
tuple_two_values: tuple[str, int] = ("value", 123)
tuple_many_values: tuple[str, ...] = "a", "b", "c", "d", "..."
any_set: set[int] = {1, 2, 3, 4}
frozen_set: frozenset[int] = frozenset([5, 6, 7])
any_dict: dict[str, str] = {"key1": "value1", "key2": "value2"}
any_range: range = range(5)
```

## Outros tipos

```python
nothing: None = None
anything: object = "123"
any_type: type = int
```

## Constantes com `Final`

`Final` informa aos verificadores de tipo que o nome não deve ser reatribuído. Essa restrição não é aplicada durante a execução:

```python
from typing import Final

CONSTANT_FINAL: Final[list[str]] = ["a", "b", "c"]
```

`Final` impede apenas a reatribuição do nome para o verificador estático; ele não torna imutável uma coleção mutável. Nesse exemplo, ainda é possível alterar o conteúdo da lista durante a execução.
