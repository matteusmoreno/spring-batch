# Spring Batch - Importação de Dados (Code Tickets)

Este projeto é uma aplicação Spring Batch desenvolvida no curso da Alura, focada na importação eficiente de dados de arquivos CSV para um banco de dados relacional. Ele simula o processo de geração e gerenciamento de tickets, lendo informações de clientes e eventos a partir de um arquivo CSV e persistindo-as em uma tabela.

## Descrição do Projeto

O objetivo principal deste projeto é demonstrar as capacidades do Spring Batch para processamento em lote (batch processing). Ele configura um pipeline completo para:
* Ler dados de um arquivo CSV.
* Mapear esses dados para um objeto Java (Entity).
* Escrever os dados processados em um banco de dados.

A aplicação é robusta para lidar com grandes volumes de dados e garante a integridade durante o processo de importação.

## Funcionalidades

* **Leitura de CSV:** Utiliza `FlatFileItemReader` para ler linhas de um arquivo CSV.
* **Mapeamento de Dados:** Transforma cada linha do CSV em um objeto `Importacao` Java usando um `FieldSetMapper` personalizado (`ImportacaoMapper`).
* **Persistência em Banco de Dados:** Escreve os objetos processados em uma tabela de banco de dados (`importacao`) usando `JdbcBatchItemWriter`.
* **Timestamps Automáticos:** Registra a hora da importação de cada registro com um timestamp (`horaImportacao`).
* **Gerenciamento de Transações:** Configuração de transações para garantir a atomicidade das operações em lote.
* **Pool de Conexões:** Utiliza HikariCP para gerenciamento eficiente de conexões com o banco de dados.

## Tecnologias Utilizadas

* **Java 21.0.7**
* **Spring Boot 3.5.0**
* **Spring Batch 5.2.2**
* **Spring Data JPA**
* **Hibernate 6.6.15.Final**
* **PostgreSQL 16.9** (Banco de Dados)
* **HikariCP** (Pool de Conexões)
* **Lombok** (`@Data` para boilerplate code)
* **FlywayDB** (Gerenciamento de Migrações de Banco de Dados)
* **Maven** (Gerenciador de Dependências)

## Configuração e Como Rodar

### Pré-requisitos

* JDK 21 ou superior instalado.
* PostgreSQL 16.9 ou superior instalado e rodando.
* Maven instalado.

### 1. Configuração do Banco de Dados

1.  Certifique-se de que seu servidor PostgreSQL está acessível.
2.  O aplicativo tentará criar o banco de dados `forum_hub` se ele não existir, conforme `spring.datasource.url`.
3.  O FlywayDB cuidará das migrações do banco de dados (criação da tabela `importacao`).

#### Esquema da Tabela `importacao`

A tabela `importacao` no banco de dados armazena os seguintes campos:

| Campo           | Tipo         | Descrição                                 |
| :-------------- | :----------- | :---------------------------------------- |
| `id`            | `BIGINT`     | ID único do registro (gerado automaticamente) |
| `cpf`           | `VARCHAR`    | CPF do cliente                            |
| `cliente`       | `VARCHAR`    | Nome do cliente                           |
| `nascimento`    | `DATE`       | Data de nascimento do cliente             |
| `evento`        | `VARCHAR`    | Nome do evento                            |
| `data`          | `DATE`       | Data do evento                            |
| `tipo_ingresso` | `VARCHAR`    | Tipo do ingresso (ex: Pista, Camarote)    |
| `valor`         | `DOUBLE`     | Valor do ingresso                         |
| `hora_importacao` | `TIMESTAMP`  | Timestamp da importação do registro       |

### 2. Arquivo CSV de Entrada (`dados.csv`)

O projeto espera um arquivo CSV chamado `dados.csv` na pasta `files/` dentro do diretório raiz do projeto. O formato das colunas e o delimitador devem seguir o padrão:

* **Delimitador:** `;` (ponto e vírgula)
* **Comentários:** Linhas que começam com `--` são ignoradas.
* **Ordem das Colunas:** `cpf`, `cliente`, `nascimento`, `evento`, `data`, `tipoIngresso`, `valor`.
* **Formato das Datas:** `yyyy-MM-dd` (ex: `1985-04-23`).
* **Formato do Valor:** Deve ser um número decimal válido (ex: `120.50`).

**Exemplo de linha no `dados.csv`:**
```csv
123.456.789-00;João Silva;1985-04-23;Roberto Carlos;2024-09-15;Pista;120.50
```

### 3. Configuração da Aplicação

Certifique-se de que o arquivo `application.properties` está configurado corretamente com as credenciais do seu banco de dados PostgreSQL:

```properties
spring.application.name=code_tickets

spring.datasource.url=jdbc:postgresql://localhost:5432/forum_hub?createDatabaseIfNotExist=true
spring.datasource.username=postgres
spring.datasource.password=sua_senha_do_postgres

# ... outras configurações ...
```

Substitua `sua_senha_do_postgres` pela senha do seu usuário `postgres`.

### 4. Como Compilar e Rodar

1.  **Clone o repositório:**
    ```bash
    git clone https://github.com/matteusmoreno/spring-batch.git
    cd spring-batch
    ```
2.  **Crie a pasta `files` e adicione o `dados.csv`:**
    ```bash
    mkdir files
    # Crie o arquivo dados.csv dentro da pasta 'files' com os dados conforme o exemplo acima.
    ```
3.  **Compile o projeto:**
    ```bash
    mvn clean install
    ```
4.  **Execute a aplicação Spring Boot:**
    ```bash
    mvn spring-boot:run
    ```
    O Spring Batch Job (`geracao-tickets`) será executado automaticamente na inicialização da aplicação.

## Estrutura do Projeto

* `src/main/java/br/com/alura/code_tickets/CodeTicketsApplication.java`: Classe principal da aplicação Spring Boot.
* `src/main/java/br/com/alura/code_tickets/DataSourceConfig.java`: Configuração do DataSource e do Transaction Manager.
* `src/main/java/br/com/alura/code_tickets/Importacao.java`: Entidade JPA que representa um registro de importação.
* `src/main/java/br/com/alura/code_tickets/ImportacaoJobConfiguration.java`: Configuração do Job e dos Steps do Spring Batch.
* `src/main/java/br/com/alura/code_tickets/ImportacaoMapper.java`: Mapper personalizado para transformar `FieldSet` de CSV em `Importacao`.
* `src/main/resources/application.properties`: Arquivo de configuração da aplicação.
* `files/dados.csv`: Arquivo CSV de entrada para o Job.

## Contribuições

Contribuições são bem-vindas! Sinta-se à vontade para abrir issues ou pull requests.

## Licença

Este projeto está sob a licença MIT.