# 🎬 deisIMDB — Base de Dados de Cinema

[![Java](https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)](https://www.java.com/)
[![MSSQL](https://img.shields.io/badge/MS_SQL_Server-CC2927?style=for-the-badge&logo=microsoftsqlserver&logoColor=white)](https://www.microsoft.com/sql-server)
[![JDBC](https://img.shields.io/badge/JDBC-007396?style=for-the-badge&logo=java&logoColor=white)](#-arquitetura)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)

**Aplicação Java com ligação a base de dados MS-SQL para gestão de informação cinematográfica (atores, filmes, géneros e votos)**

[Descrição](#-descrição) •
[Funcionalidades](#-funcionalidades) •
[Arquitetura](#-arquitetura) •
[Instalação](#-instalação) •
[Utilização](#-utilização) •
[API de Classes](#-api-de-classes)

---

## 📋 Descrição

O **deisIMDB** é um projeto académico que demonstra a integração entre **Java** e **bases de dados relacionais** (MS-SQL Server) através de JDBC. A aplicação permite gerir informação cinematográfica — atores, realizadores, filmes, géneros e votos — utilizando operações **CRUD**, chamadas a **Stored Procedures** e **funções escalares/vetoriais**.

O projeto serve como exemplo prático dos conceitos estudados nas unidades curriculares de **Algoritmos e Estruturas de Dados** e **Bases de Dados**, aplicando padrões como o **DAO (Data Access Object)** para separar a lógica de acesso a dados da lógica de negócio.

---

## ✨ Funcionalidades

| Funcionalidade | Descrição |
|---|---|
| 📥 **Importação de Dados** | Carregamento em massa via CSV com `bcp` (bulk copy) |
| ➕ **Inserir Ator** | Criação de novo ator com suporte a *undelete* (reativação) |
| 📋 **Listar Atores** | Listagem de todos os atores ativos na base de dados |
| ✏️ **Atualizar Ator** | Edição dos dados de um ator existente |
| 🗑️ **Eliminar Ator** | Eliminação lógica (*soft delete*) via trigger na base de dados |
| 🔍 **Verificar Existência** | Chamada a função escalar para validar IDs |
| 📊 **Funções Vetoriais** | Consultas que devolvem tabelas completas |
| 🔄 **Undelete** | Reativação de registos eliminados através de Stored Procedure |

---

## 🏗️ Arquitetura

O projeto segue o padrão **DAO** para isolar o acesso a dados, com uma classe centralizada de ligação à base de dados.

```
┌─────────────────────────────────────────────────────────┐
│                        Main.java                         │
│                                                          │
│   Interface de utilizador · Menus · Execução de funções  │
└────────────┬────────────────────────┬────────────────────┘
             │                        │
             ▼                        ▼
┌────────────────────┐    ┌────────────────────┐
│     Actor.java     │    │    Result.java     │
│                    │    │                    │
│ • CRUD completo    │    │ • Apresentação de  │
│ • insertDB()       │    │   resultados       │
│ • updateDB()       │    │                    │
│ • deleteDB()       │    └────────────────────┘
│ • Construtores     │
│   com SELECT       │
└────────┬───────────┘
         │
         ▼
┌────────────────────┐       ┌─────────────────────────────┐
│     Dao.java       │──────►│      MS-SQL Server           │
│                    │ JDBC  │                               │
│ • getConnection()  │       │ • Tabelas (actors, movies…)  │
│ • Credenciais      │       │ • Stored Procedures          │
│ • Config servidor  │       │ • Triggers (soft delete)     │
│                    │       │ • Funções escalares/vetoriais │
└────────────────────┘       └─────────────────────────────┘
```

### Decisões de Design

- **Classe `Dao`** centraliza credenciais e configuração de ligação, evitando repetição e reduzindo erros
- **Classe `Actor`** encapsula dados e operações CRUD — cada método acede à BD através do `Dao` instanciado no construtor
- **Prepared Statements** com *placeholders* (`?`) para prevenir SQL injection e garantir tipagem segura
- **Soft delete** via trigger SQL — atores com filmes associados são desativados em vez de eliminados
- **Undelete** via Stored Procedure — reinserção de um ator inativo reativa o registo original

---

## 📁 Estrutura do Projeto

```
deisIMDB/
├── README.md
├── DEISI_DB_2025.code-workspace
│
├── images/                          # Imagens de suporte ao README
│
├── lib/                             # Bibliotecas externas
│   ├── mssql-jdbc-13.2.1.jre11.jar
│   ├── mssql-jdbc-13.2.1.jre11-sources.jar
│   └── mssql-jdbc-13.2.1.jre11-javadoc.jar
│
├── sql/                             # Scripts SQL
│   ├── DDL.sql                      # Criação de tabelas + comandos de importação
│   └── deisIMDB.sql                 # Stored Procedures e funções
│
├── src/pt/ulusofona/aed/deisimdb/   # Código fonte
│   ├── Main.java                    # Interface de utilizador e menus
│   ├── Dao.java                     # Ligação à base de dados (DAO pattern)
│   ├── Actor.java                   # Modelo + operações CRUD
│   ├── Result.java                  # Apresentação de resultados
│   └── TipoEntidade.java           # Enumerado de tipos de entidade
│
└── test-files/demoFiles/            # Ficheiros CSV para importação
    ├── actors.csv
    ├── directors.csv
    ├── genres.csv
    ├── genres_movies.csv
    ├── movies.csv
    └── movie_votes.csv
```

---

## 💻 Instalação

### Pré-requisitos

| Requisito | Descrição |
|---|---|
| **Java JDK 11+** | Runtime e compilação |
| **Docker** | Para o servidor MS-SQL (ou servidor alternativo) |
| **IDE** | VSCode (recomendado), IntelliJ, Eclipse, etc. |
| **JDBC Driver** | Incluído em `lib/` (mssql-jdbc-13.2.1) |

### 1. Clonar o Repositório

```bash
git clone https://github.com/goncaloalegria/deisIMDB.git
cd deisIMDB
```

### 2. Configurar o IDE (VSCode)

Instalar as seguintes extensões:

| Extensão | Autor | Obrigatória |
|---|---|---|
| **Extension Pack for Java** | Microsoft | Sim |
| **Language Support for Java** | Red Hat | Sim |
| **Java** | Oracle | Opcional (melhora IntelliSense) |

> Para outros IDEs, garantir que as bibliotecas em `lib/` estão registadas no classpath e que é possível executar comandos SQL sobre MS-SQL.

### 3. Criar a Base de Dados

```sql
-- 1. Abrir ligação ao servidor SQL com credenciais adequadas
-- 2. Executar os comandos do ficheiro DDL.sql
-- 3. Verificar criação:
SELECT * FROM INFORMATION_SCHEMA.TABLES
```

### 4. Importar Dados CSV

Os comandos de importação são executados em **terminal** (PowerShell / Terminal / Bash), **não em cliente SQL**. Navegar até `test-files/demoFiles/` e executar:

```bash
docker run --rm -v ${PWD}:/work \
  --entrypoint /opt/mssql-tools18/bin/bcp \
  mcr.microsoft.com/mssql/server:2022-latest \
  deisIMDB.dbo.movies in /work/movies.csv \
  -S host.docker.internal,1433 \
  -U sa -P "YourStrong!Passw0rd" \
  -u -c -t "," -r "\n\r" -F 1
```

> Repetir para cada ficheiro CSV (`actors.csv`, `directors.csv`, `genres.csv`, etc.), ajustando o nome da tabela e do ficheiro.

### 5. Criar Objetos SQL

Executar o script `sql/deisIMDB.sql` para criar as Stored Procedures e funções utilizadas pela aplicação.

---

## 🔧 Resolução de Problemas na Importação

| Problema | Solução |
|---|---|
| Erro de carácter não reconhecido | Alterar encoding do CSV no VSCode (canto inferior direito) |
| Encoding **CRLF** | Usar parâmetro `-r "\n\r"` |
| Encoding **LF** | Usar parâmetro `-r "\n"` |
| Erros genéricos de importação | Adicionar `-m 1 -e /work/bcp_errors.log` para gerar log detalhado |
| Dados não aparecem após importação | Verificar com `SELECT COUNT(*) FROM [tabela]` |

---

## 📱 Utilização

### Fluxo Recomendado de Teste

A aplicação apresenta um menu de opções no arranque. Para validar o funcionamento completo, seguir esta sequência:

| Passo | Comando | Resultado Esperado |
|---|---|---|
| 1 | `LISTAR_ACTORES` | Lista com os atores importados do CSV |
| 2 | `INSERT_ACTOR id;name;gender;movie-id` | Confirmação + dados do novo ator |
| 3 | `INSERT_ACTOR` (ID duplicado) | Aviso de existência + dados originais da BD |
| 4 | `LISTAR_ACTORES` | Novos atores na lista, duplicado inalterado |
| 5 | `UPDATE_ACTOR id;name;gender;movie-id` | Confirmação + dados atualizados |
| 6 | `UPDATE_ACTOR` (ID inexistente) | Aviso de ator inexistente |
| 7 | `APAGAR_ACTOR id` | Confirmação de eliminação |
| 8 | `LISTAR_ACTORES` | Atores eliminados não aparecem |
| 9 | *(SQL direto)* `SELECT * FROM actors` | Ator com filme: `isActive = 0`. Ator sem filme: removido |
| 10 | `INSERT_ACTOR` (ator eliminado) | Reativação automática (*undelete*) |
| 11 | `LISTAR_ACTORES` | Ator reativado visível na lista |

> Opções com prefixo `*` no menu ainda não estão implementadas.

---

## 🔌 API de Classes

### `Dao`

Centraliza a configuração de ligação à base de dados.

| Atributo | Descrição |
|---|---|
| `sqlserver` | Endereço do servidor |
| `databaseName` | Nome da base de dados |
| `user` / `password` | Credenciais de acesso |
| `encrypt` | Encriptação da ligação |
| `trustServerCertificate` | Confiança no certificado |

| Método | Descrição |
|---|---|
| `getConnection()` | Devolve um objeto `Connection` JDBC configurado |

### `Actor`

Modelo de dados com operações CRUD integradas.

| Método | Operação CRUD | Descrição |
|---|---|---|
| `Actor(id, name, gender, movieId)` | — | Construtor com dados do utilizador |
| `Actor(id)` | **READ** | Carrega ator da BD por ID |
| `insertDB()` | **CREATE** | Insere ator (ou reativa via Stored Procedure) |
| `updateDB()` | **UPDATE** | Atualiza todos os atributos na BD |
| `deleteDB()` | **DELETE** | Soft delete via trigger (desativa se tem filmes) |

### Padrão de Acesso à BD

Todos os métodos CRUD seguem a mesma estrutura:

```
1. Instanciar Dao
2. Definir String SQL com placeholders (?)
3. Obter Connection via dao.getConnection()
4. Criar PreparedStatement
5. Definir parâmetros → statement.set[Tipo](ordem, valor)
6. Executar → statement.execute[Ação]()
7. (Opcional) Ler resultados via ResultSet
```

---

## 📊 Exemplos de SQL em Java

| Tipo | Método | Características |
|---|---|---|
| **Função Escalar** | `actorExists(id)` | Placeholder para resultado, `registerOutParameter` em vez de `set[Tipo]` |
| **Função Vetorial** | `listaActores()` | Estrutura igual a SELECT, iteração com `while(resultSet.next())` |
| **Stored Procedure** | `insertDB()` | Chamada com `CALL`, lida com inserção e reativação |
| **Tratamento de Erros** | Todos os métodos | Estrutura `try...catch`; atenção à independência entre exceções Java e SQL |

---

## ⚠️ Notas Importantes

- **Java e SQL executam em âmbitos independentes** — falhas na comunicação de eventos podem gerar comportamentos indesejados
- **Exceções de regras de negócio** (geradas programaticamente) têm tratamento diferente das exceções de execução
- Os parâmetros de ligação no `Dao` correspondem à instalação base das aulas; criar construtor em *overload* para configurações diferentes
- Antes de executar a aplicação, os dados devem estar carregados na BD (preferencialmente apenas os dados de demonstração)

---

## 👤 Autor

- **Gonçalo Alegria** — [@goncaloalegria](https://github.com/goncaloalegria)

---

## 🙏 Agradecimentos

- [Universidade Lusófona](https://www.ulusofona.pt/) — Instituição de ensino
- [Microsoft SQL Server](https://www.microsoft.com/sql-server) — Motor de base de dados
- [Docker](https://www.docker.com/) — Containerização do ambiente de desenvolvimento
