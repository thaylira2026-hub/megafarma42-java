# 💊 MegaFarma API

API RESTful para cadastro de remédios, desenvolvida em **Java com Spring Boot** e banco de dados **Oracle**, seguindo a arquitetura em camadas **Resource → BO → DAO → TO**.

Projeto desenvolvido nas aulas 41 e 42 da disciplina **Domain Driven Design Using Java** da **FIAP**.

---

## 📋 Funcionalidades

- Listar todos os remédios cadastrados
- Buscar um remédio pelo código
- Cadastrar um novo remédio, com validação dos dados (Bean Validation)
- Excluir um remédio pelo código

## 🛠️ Tecnologias

| Tecnologia | Uso |
|---|---|
| Java 21 | Linguagem |
| Spring Boot 4 (Web MVC) | Criação da API REST |
| Spring Boot Validation | Bean Validation (Hibernate Validator) |
| JDBC + Oracle (ojdbc17) | Acesso ao banco de dados |
| Maven | Gerenciamento de dependências |
| Insomnia | Testes das requisições |

## 🏗️ Arquitetura

```
src/main/java/br/com/fiap
├── MegafarmaApplication.java   → classe principal (sobe a API)
├── resource/RemedioResource    → recebe as requisições HTTP (endpoints)
├── bo/RemedioBO                → regras de negócio
├── dao/RemedioDAO              → comandos SQL no banco
├── dao/ConnectionFactory       → conexão com o Oracle
└── to/RemedioTO                → objeto de transferência (com as validações)
```

Uma requisição percorre as camadas nesta ordem:
**Cliente → Resource → BO → DAO → Banco**, e a resposta volta pelo mesmo caminho.

## 🔗 Endpoints

URL base: `http://localhost:8080/megafarma`

| Verbo | Endpoint | Descrição | Respostas |
|---|---|---|---|
| `GET` | `/megafarma` | Lista todos os remédios | `200 OK` |
| `GET` | `/megafarma/{codigo}` | Busca um remédio pelo código | `200 OK` / `404 Not Found` |
| `POST` | `/megafarma` | Cadastra um remédio | `201 Created` / `400 Bad Request` |
| `DELETE` | `/megafarma/{codigo}` | Exclui um remédio | `204 No Content` / `404 Not Found` |

### Exemplo de corpo (POST)

```json
{
  "nome": "Dipirona",
  "preco": 33.5,
  "dataDeFabricacao": "2026-01-10",
  "dataDeValidade": "2027-01-10"
}
```

## ✅ Validações (Bean Validation)

| Campo | Regra | Anotação |
|---|---|---|
| `nome` | obrigatório, não pode ser vazio | `@NotBlank` |
| `preco` | obrigatório, zero ou positivo | `@NotNull` `@PositiveOrZero` |
| `dataDeFabricacao` | hoje ou no passado | `@PastOrPresent` |
| `dataDeValidade` | hoje ou no futuro (remédio não pode estar vencido) | `@FutureOrPresent` |

Se algum campo não respeitar a regra, a API responde `400 Bad Request` e o remédio não é salvo.

## 🗄️ Banco de dados

Crie a tabela no Oracle antes de rodar a API:

```sql
CREATE TABLE ddd_remedios (
    codigo             NUMBER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    nome               VARCHAR2(100) NOT NULL,
    preco              NUMBER(10,2)  NOT NULL,
    data_de_fabricacao DATE          NOT NULL,
    data_de_validade   DATE          NOT NULL
);
```

## ▶️ Como rodar

1. Clone o repositório e abra a pasta do projeto (a que tem o `pom.xml`) no IntelliJ.
2. Clique em **Sync Maven Changes** para baixar as dependências.
3. Em `dao/ConnectionFactory.java`, coloque a URL, o usuário e a senha do seu banco Oracle.
4. Rode a classe `MegafarmaApplication`.
5. A API fica disponível em `http://localhost:8080/megafarma`.

## 🧪 Testando com o Insomnia

O arquivo `insomnia_megafarma.json` tem as requisições prontas (findAll, findByCodigo, save e delete).
No Insomnia, vá em **Import**, escolha o arquivo e teste.

---

👨‍💻 Desenvolvido por **Thays Lira** · FIAP · Domain Driven Design Using Java
