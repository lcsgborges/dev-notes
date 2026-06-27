# Alembic

- Pode trabalhar em toda a camada de DDL (Data Definition Language)
- Fornece scripts de migração de schemas para upgrades e downgrades
- Suporte à geração de SQL (offline)
- API minimalista

## Instalação

```bash
pip install alembic
```

## Inicializando o Alembic

```bash
alembic init <nome> # por padrão migrations: alembic init migrations
```

## Configurações gerais

O arquivo `alembic.ini` contém as configurações do diretório em que as migrações serão salvas, o caminho dos scripts e a URI do banco de dados.

O arquivo `env.py` contém a configuração do ambiente usado para gerar e executar as migrações.

### Gerando uma migração

```bash
alembic revision -m "mensagem de migração"
# A mensagem identificará o arquivo de migração, como em um commit
```

No arquivo gerado para a migração, teremos duas funções principais que são "inversas": `upgrade()` e `downgrade()`.

### API de operações

A API de operações funciona para os comandos DDL:

- Criação de tabelas (`CREATE TABLE`)
- Alteração de tabelas (`ALTER TABLE`)
- Exclusão de tabelas (`DROP TABLE`)

Essas operações serão feitas dentro das funções `upgrade()` e `downgrade()`.

### Configuração do banco de dados no Alembic

Antes de aplicar uma migração, devemos informar ao Alembic onde está o banco de dados. Para isso, precisamos definir a URI do banco no `alembic.ini`.

```toml
[alembic]
sqlalchemy.url = sqlite:///database.db
```

Entretanto, podemos configurar o banco de dados diretamente no `env.py`:

```python
from alembic import context
from api.settings import get_settings

settings = get_settings()

config = context.config
config.set_main_option("sqlalchemy.url", settings.DATABASE_URL)
```

Para aplicar as migrações no banco de dados, basta usarmos o seguinte comando:

```bash
alembic upgrade head
```

Se quisermos reverter a última migração, usamos o seguinte comando:

```bash
alembic downgrade -1
```

### Desfazendo e aplicando migrações

O Alembic permite consultar o histórico e avançar ou retroceder entre as migrações:

```bash
alembic history
alembic history --indicate-current # ou só -i (mostra onde estou)

alembic upgrade <ID>
alembic upgrade head
alembic upgrade +1

alembic downgrade <ID>
alembic downgrade base
alembic downgrade -1
```

## SQLA CodeGen

Um cenário comum é termos um banco existente ou um projeto que começou sem modelos versionados. Nesse caso, podemos fazer a reflexão (_reflection_) do banco de dados para gerar código.

- Instalação: `pip install sqlacodegen`

Usando a URI do banco, o gerador construirá os modelos com o SQLAlchemy ORM:

```bash
# sqlacodegen <DB_URI>
sqlacodegen sqlite:///database.db
```

## Migrações automáticas

No arquivo `env.py`, precisamos atribuir `Base.metadata` a `target_metadata`:

```python
from core.database import Base

target_metadata = Base.metadata
```

Caso tenhamos outros modelos, também precisamos importá-los para que sejam registrados em `Base.metadata`:

```python
from core.database import Base
from modules.users.models import User
from modules.posts.models import Post

target_metadata = Base.metadata

# Podemos criar um arquivo que reúna todos os modelos e importá-los de uma
# só vez. Exemplo: registry.py com todos os imports dos modelos.
```

Agora, podemos gerar uma migração automática com o Alembic:

```bash
alembic revision --autogenerate -m "tabelas users e posts"
```

## Alguns problemas que podemos ter

### Sem acesso ao banco de produção

Por questões de segurança, às vezes não podemos aplicar uma migração no banco de produção. Nesse caso, podemos usar o modo offline para gerar o código SQL:

```bash
alembic upgrade +1 --sql
```

Para downgrades no modo offline, precisamos informar o **ID** da versão atual e o da versão para a qual queremos voltar:

```bash
alembic downgrade <id_current>:<id_past> --sql
# Também podemos usar downgrade head:-1. Exemplo:
# alembic downgrade head:-1 --sql
```
