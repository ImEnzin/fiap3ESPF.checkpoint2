# Checkpoint 1 - Orders API - Spring Boot

API REST completa para gerenciamento de pedidos com CRUD completo e banco de dados persistente.

*(Opcional: contém uma interface web simples para facilitar testes da API)*

---

## Integrantes

| Nome | RM |
|---|---|
| Vinicius Henrique | RM556908 |
| Enzo Dias | RM558225 |
| Gustavo Pierre | RM558928 |
| Gabriel Belo | RM551669 |
| Laura Souza | RM556320 |

---

## O que o sistema faz?

Este sistema é uma API de gerenciamento de pedidos. Ele permite:

- **Criar** um novo pedido informando o nome do cliente e o valor
- **Listar** todos os pedidos cadastrados
- **Buscar** um pedido específico pelo ID
- **Atualizar** os dados de um pedido existente
- **Deletar** um pedido do sistema

Todos os dados ficam salvos em banco de dados — mesmo reiniciando a aplicação, os pedidos não somem.

---

## Tecnologias utilizadas

| Tecnologia | Para que serve |
|---|---|
| Java 17 | Linguagem principal |
| Spring Boot 3.3.5 | Framework que facilita criar APIs |
| Spring Web | Criação dos endpoints HTTP (REST) |
| Spring Data JPA | Comunicação com o banco de dados |
| Jakarta Validation | Validação dos dados recebidos |
| Lombok | Elimina código repetitivo (getters, setters) |
| H2 Database | Banco de dados embutido, salvo em arquivo |
| Maven | Gerenciador de dependências e build |

---

## Estrutura do projeto

```
cp2/
├── pom.xml                          ← dependências do projeto
└── src/
    └── main/
        ├── java/br/com/fiap/checkpoint1/
        │   ├── Checkpoint1Application.java   ← ponto de entrada da aplicação
        │   ├── controller/
        │   │   └── OrderController.java    ← recebe as requisições HTTP
        │   ├── model/
        │   │   └── OrderModel.java              ← representa o pedido (entidade do banco)
        │   ├── repository/
        │   │   └── OrderRepository.java    ← acesso ao banco de dados
        │   └── service/
        │       └── OrderService.java       ← regras de negócio
        └── resources/
            ├── application.properties       ← configurações da aplicação
            └── static/
                └── index.html               ← interface web
```

### Como cada camada funciona

```
Navegador / Cliente HTTP
        ↓
  Controller  → recebe a requisição e valida os dados de entrada
        ↓
  Service     → aplica as regras de negócio
        ↓
  Repository  → executa as operações no banco de dados
        ↓
  Banco H2    → armazena os dados em arquivo local (pasta /data)
```

---

## Como rodar o projeto

### Pré-requisitos

- Java 17 instalado
- Maven instalado

### Passo a passo

**1. Clonar ou abrir o projeto no VS Code**

**2. Compilar o projeto:**
```bash
mvn clean package
```

**3. Iniciar a aplicação:**
```bash
mvn spring-boot:run
```

**4. Acessar no navegador:**
- Interface web: http://localhost:8085
- API direta: http://localhost:8085/pedidos
- Console do banco: http://localhost:8085/h2-console

> A aplicação roda na porta **8085** conforme definido em `application.properties`.

---

## Interface Web

Ao acessar http://localhost:8085 você encontra uma interface visual completa onde é possível:

- Preencher um formulário para criar pedidos
- Ver todos os pedidos em uma tabela
- Editar qualquer pedido clicando em "Editar"
- Excluir pedidos com confirmação

---

## Endpoints da API

Base URL: `http://localhost:8085`

### Criar pedido — POST `/orders`

Cadastra um novo pedido. A data é preenchida automaticamente.

```http
POST http://localhost:8085/orders
Content-Type: application/json

{
  "clientName": "João Silva",
  "totalValue": 150.00
}
```

Resposta (201 Created):
```json
{
  "id": 1,
  "clientName": "João Silva",
  "orderDate": "2026-04-25",
  "totalValue": 150.00
}
```

---

### Listar todos os pedidos — GET `/orders`

```http
GET http://localhost:8085/orders
```

Resposta (200 OK):
```json
[
  {
    "id": 1,
    "clientName": "João Silva",
    "orderDate": "2026-04-25",
    "totalValue": 150.00
  }
]
```

---

### Buscar pedido por ID — GET `/orders/{code}`

```http
GET http://localhost:8085/orders/1
```

Resposta (200 OK):
```json
{
  "id": 1,
  "clientName": "João Silva",
  "orderDate": "2026-04-25",
  "totalValue": 150.00
}
```

Se o ID não existir, retorna `404 Not Found`.

---

### Atualizar pedido — PUT `/orders/{code}`

```http
PUT http://localhost:8085/orders/1
Content-Type: application/json

{
  "clientName": "João Silva Atualizado",
  "totalValue": 200.00
}
```

Resposta (200 OK):
```json
{
  "id": 1,
  "clientName": "João Silva Atualizado",
  "orderDate": "2026-04-25",
  "totalValue": 200.00
}
```

---

### Deletar pedido — DELETE `/orders/{code}`

```http
DELETE http://localhost:8085/orders/1
```

Resposta: `204 No Content` (sem corpo)

---

## Dados do pedido (Model)

| Campo | Tipo | Obrigatório | Regra |
|---|---|---|---|
| `id` | Long | — | Gerado automaticamente |
| `clientName` | String | ✅ Sim | Não pode ser vazio |
| `totalValue` | BigDecimal | ✅ Sim | Deve ser maior que zero |
| `orderDate` | LocalDate | ❌ Não | Preenchida automaticamente com a data atual |

---

## Banco de Dados H2

O banco de dados é o H2, salvo em arquivo local para persistência.

- **Console web:** http://localhost:8085/h2-console
- **JDBC URL:** `jdbc:h2:file:~/testdb`
- **Usuário:** `sa`
- **Senha:** `password`

Os dados ficam salvos na pasta `data/` dentro do projeto.

---

## Validações

A API valida os dados recebidos e retorna erros descritivos:

- `clientName` vazio → `400 Bad Request`
- `totalValue` negativo ou zero → `400 Bad Request`
- ID não encontrado → `404 Not Found`

---

## .gitignore

O projeto contém um arquivo `.gitignore` que impede o envio de arquivos desnecessários ao repositório Git.

| Pasta/Arquivo ignorado | Motivo |
|---|---|
| `target/` | Contém o JAR compilado e classes geradas pelo Maven. É grande e pode ser recriado com `mvn package` |
| `data/` | Contém o arquivo do banco H2 gerado localmente. Cada desenvolvedor terá o seu próprio banco ao rodar |
| `.idea/`, `*.iml` | Configurações pessoais do IntelliJ IDEA — não devem ser compartilhadas |
| `.vscode/` | Configurações pessoais do VS Code |
| `.classpath`, `.project`, `.settings/` | Arquivos do Eclipse — gerados automaticamente por cada IDE |
| `*.log`, `logs/` | Arquivos de log gerados em tempo de execução |
| `.env` | Variáveis de ambiente sensíveis (senhas, tokens) — nunca devem ir para o repositório |
| `.DS_Store`, `Thumbs.db` | Arquivos ocultos gerados pelo macOS e Windows respectivamente |

> **Resumo:** apenas o código-fonte (`src/`), o `pom.xml` e o `README.md` são enviados ao GitHub. Tudo que é gerado automaticamente ou é pessoal de cada máquina fica de fora.
