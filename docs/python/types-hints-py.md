# Type hints em Python (dicas de tipos ou anotações de tipo)

> Ambiente com `python>=3.13`

Por padrão, os _type hints_ não alteram o comportamento do interpretador do Python. Entretanto, são úteis para:

- Permitir que ferramentas como `mypy` ou `Pyright` encontrem erros antes da execução
- Ativar o preenchimento automático inteligente e exibir documentação nos editores
- Ajudar a entender o código
- Evitar erros simples (por exemplo, passar `int` onde era esperado `str`)
- Melhorar a legibilidade do código, como se fosse uma documentação automática

## Tipos básicos

```python
name: str = "Lucas Borges"
x: int = 10
y: float = 20.5
is_valid: bool = False
data: bytes = b"weather"
```

## Constantes

Constantes não costumam ser reatribuídas, então a anotação explícita de tipo pode ser redundante. O próprio valor já deixa claro o tipo:

```python
CONSTANT = "any value"  # CONSTANTE: Literal["any value"]
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

## Constantes com Final

`Final` informa aos verificadores de tipo que a variável ou constante não deve ser reatribuída:

```python
from typing import Final

CONSTANT_FINAL: Final[list[str]] = ["a", "b", "c"]
```
