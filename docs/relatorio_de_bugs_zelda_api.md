# 🐛 Relatório de Inconsistências e Bugs - Zelda API

Este documento reúne os achados de testes funcionais, exploratórios e de contrato realizados na [**Zelda API**](https://docs.zelda.fanapis.com/docs/). Os cenários mapeados evidenciam desvios do padrão RESTful (RFC 7231) e falhas na validação de entradas da API.

## 📋 Sumário de Bugs Mapeados

| ID          | Título                                                                               | Tipo                     | Gravidade | Status Esperado          | Status Atual      |
| ----------- | ------------------------------------------------------------------------------------ | ------------------------ | --------- | ------------------------ | ----------------- |
| **BUG-001** | Status Code 400 retornado para recursos inexistentes                                 | Divergência de Protocolo | Baixa     | `404 Not Found`          | `400 Bad Request` |
| **BUG-002** | Métodos HTTP não permitidos retornam status semântico incorreto sem header `Allow`   | Divergência de Protocolo | Baixa     | `405 Method Not Allowed` | `400 Bad Request` |
| **BUG-003** | Falta de validação para parâmetros de paginação negativos (`limit` / `page`)         | Validação de Entrada     | Média     | `400 Bad Request`        | `200 OK`          |
| **BUG-004** | Trata silenciosamente tipos de dados inválidos em parâmetros de query (`limit=abcd`) | Validação de Entrada     | Média     | `400 Bad Request`        | `200 OK`          |

## 🔍 Detalhamento dos issues

### `[BUG-001] Status Code 400 retornado para ID inexistente em vez de 404`

- **Componente:** `GET /<resource>/{id}` (ex: `/games/{id}`)

- **Gravidade:** Baixa (Low / Semântica HTTP)

- **Passos para Reproduzir:**
  1.  Enviar requisição `GET` para `https://zelda.fanapis.com/api/games/600000000000000000000000`
  2.  Verificar o status code e payload de resposta.

<span style="background-color: #e74c3c; color: white; padding: 1px 100px;">
  Resultado Atual
</span>

- Ao realizar a busca de um recurso específico utilizando um ID sintaticamente válido (formato ObjectID de 24 caracteres), porém inexistente na base de dados, a API responde com HTTP `400 Bad Request`.

- **Status Code:** `400 Bad Request`

![Pasted image](/evidencias/e_ID.png)

<span style="background-color: #2ecc71; color: white; padding: 1px 100px;">
  Resultado Esperado
</span>

- **Justificativa:** O status `400 Bad Request` indica que o cliente enviou uma requisição sintaticamente incorreta. Se a sintaxe do ID está adequada e a entidade apenas não consta no banco, a resposta semântica correta deve ser `404 Not Found`.

- **Status Code:** `404 Not Found` (Segundo a especificação RFC 7231 / Arquitetura REST).

- **Response body:**

```json
{
  "success": false,
  "message": "No item found with that ID"
}
```

---

### `[BUG-002] Métodos HTTP não suportados não retornam status 405 nem cabeçalho 'Allow'`

- **Componente:** Endpoints de leitura de recursos (ex: `POST /games`, `PUT /games/{id}`, `DELETE /games/{id}`)

- **Gravidade:** Baixa (Low / Padrão de Segurança e Protocolo)

- **Passos para Reproduzir:**
  1. Enviar requisição `PUT`para `https://zelda.fanapis.com/api/games` com um payload vazio ou qualquer JSON.

  2. Verificar o cabeçalho e o status code retornado.

<span style="background-color: #e74c3c; color: white; padding: 1px 100px;">
  Resultado Atual
</span>

- Como a Zelda API é uma API exclusivamente de leitura (_read-only_), o envio de métodos de escrita (`POST`, `PUT`, `DELETE`) deve ser bloqueado com a indicação dos métodos permitidos. A API bloqueia a alteração, porém responde com `400 Bad Request`.

- **Status Code:** `400 Bad Request`

![Pasted image](/evidencias/e_PUT.png)

<span style="background-color: #2ecc71; color: white; padding: 1px 100px;">
  Resultado Esperado
</span>

- **Status Code:** `405 Method Not Allowed`

- **Headers:** Deve conter `Allow: GET` (informando ao cliente quais métodos HTTP são aceitos naquela rota).

---

### `[BUG-003] Aceite de números negativos em parâmetros de paginação (limit e page)`

- **Componente:** Endpoints de listagem (`GET /games`)

- **Gravidade:** Média (Medium / Regra de Negócio)

- **Passos para Reproduzir:**
  1. Enviar requisição `GET` para `https://zelda.fanapis.com/api/games?limit=-10`

  2. Enviar requisição `GET` para `https://zelda.fanapis.com/api/games?page=-1`

<span style="background-color: #e74c3c; color: white; padding: 1px 100px;">
  Resultado Atual
</span>

- A API não possui validação de intervalo (range validation) para os parâmetros de paginação `limit` e `page`. Ao enviar um valor negativo, a API processa a requisição com sucesso (`200 OK`) aplicável por um tratamento interno silencioso.

- **Status Code:** `200 OK`

- **Response Body:** Retorna lista padrão de objetos.

![Pasted image](/evidencias/e_Neg.png)

<span style="background-color: #2ecc71; color: white; padding: 1px 100px;">
  Resultado Esperado
</span>

- **Status Code:** `400 Bad Request`

- **Response Body:** Mensagem de erro amigável
  - ex:

    ```json
    {
      "success": false,
      "message": "limit must be a positive integer"
    }
    ```

---

### `[BUG-004] Fallback silencioso ao enviar tipos de dados incompatíveis no filtro limit`

- **Componente:** Endpoints de listagem (`GET /items`)

- **Gravidade:** Média (Medium / Validação de Tipagem)

- **Passos para Reproduzir:**
  1. Enviar requisição `GET` para `https://zelda.fanapis.com/api/games?limit=abcd`

  2. Observar o número de itens retornados na lista e o status de resposta.

<span style="background-color: #e74c3c; color: white; padding: 1px 100px;">
  Resultado Atual
</span>

- Ao enviar uma string alfabética para um parâmetro numérico de paginação (ex: `limit=abcd`), o parser do servidor ignora o valor inválido e assume silenciosamente o valor _default_ da aplicação (20 itens), respondendo com `200 OK`.

- **Status Code:** `200 OK`

- **Response Body:** Retorna array `data` preenchido com o limite _default_ de 20 registros.

![Pasted image](/evidencias/e_abc.png)

<span style="background-color: #2ecc71; color: white; padding: 1px 100px;">
  Resultado Esperado
</span>

- **Status Code:** `400 Bad Request`

- **Response Body:** Mensagem informando erro de tipagem no parâmetro
  - ex:

    ```json
    {
      "success": false,
      "message": "limit parameter must be a number""
    }
    ```

- **Impacto:** O mascaramento de falhas de tipagem impede que clientes integradores identifiquem erros de parâmetros enviados em suas requisições.
