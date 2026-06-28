# Alembic

- Permite criar e executar operações DDL (*Data Definition Language*).
- Mantém scripts versionados para aplicar e reverter alterações de esquema.
- Pode gerar SQL sem executar a migração no banco de dados, no modo *offline*.
- Integra-se aos metadados do SQLAlchemy para sugerir migrações.

## Instalação

```bash
pip install alembic
```

## Inicializando o Alembic

```bash
# Cria a estrutura no diretório migrations.
alembic init migrations
```

## Configurações gerais

O arquivo `alembic.ini` contém as configurações do diretório em que as migrações serão salvas, o caminho dos scripts e a URI do banco de dados.

O arquivo `env.py` contém a configuração do ambiente usado para gerar e executar as migrações.

### Gerando uma migração

```bash
alembic revision -m "mensagem de migração"
# A mensagem ajuda a identificar a migração no histórico.
```

O arquivo gerado possui duas funções principais: `upgrade()`, que aplica a alteração, e `downgrade()`, que deve revertê-la quando a reversão for suportada.

### API de operações

A API de operações funciona para os comandos DDL:

- Criação de tabelas (`CREATE TABLE`).
- Alteração de tabelas (`ALTER TABLE`).
- Exclusão de tabelas (`DROP TABLE`).

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
database_url = str(settings.DATABASE_URL).replace("%", "%%")
config.set_main_option("sqlalchemy.url", database_url)
```

O escape de `%` evita que uma URL com esse caractere seja interpretada pela interpolação usada na configuração. Credenciais não devem ser gravadas diretamente em arquivos versionados.

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
alembic current
alembic history --verbose

alembic upgrade <ID>
alembic upgrade head
alembic upgrade +1

alembic downgrade <ID>
alembic downgrade base
alembic downgrade -1
```

## `sqlacodegen`

Um cenário comum é termos um banco existente ou um projeto que começou sem modelos versionados. Nesse caso, podemos fazer a reflexão (_reflection_) do banco de dados para gerar código.

Instalação: `pip install sqlacodegen`.

Usando a URI do banco, o gerador construirá os modelos com o SQLAlchemy ORM:

```bash
# Substitua DATABASE_URL pela URL de conexão.
sqlacodegen sqlite:///database.db
```

## Autogeração de migrações

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

# Um módulo de registro pode reunir essas importações.
```

Agora, podemos pedir que o Alembic compare os metadados com o banco e gere uma migração candidata:

```bash
alembic revision --autogenerate -m "tabelas users e posts"
```

A autogeração não detecta todas as alterações nem garante que a operação sugerida seja segura. O arquivo gerado deve ser revisado antes da aplicação.

## Alguns problemas que podemos ter

### Sem acesso ao banco de produção

Quando não podemos aplicar uma migração diretamente no banco de produção, usamos o modo *offline* para gerar o SQL. Como esse modo não consulta o banco, devemos informar explicitamente o intervalo de revisões:

```bash
alembic upgrade REVISAO_ATUAL:REVISAO_DESTINO --sql
```

Para downgrades no modo offline, precisamos informar o **ID** da versão atual e o da versão para a qual queremos voltar:

```bash
alembic downgrade REVISAO_ATUAL:REVISAO_ANTERIOR --sql

# Para gerar a reversão de uma revisão a partir de head:
alembic downgrade head:-1 --sql
```
