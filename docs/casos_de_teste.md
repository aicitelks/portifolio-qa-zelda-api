# 🧪 Casos de Teste Detalhados - Zelda API (`/games`)

Este documento apresenta o detalhamento operacional de todos os casos de teste mapeados e automatizados na coleção Postman da **Zelda API**.

---

## 📁 1. Positive Testing (Testes Funcionais e de Contrato)

### **CT01 - Get All games - Listar games com sucesso**
* **Objetivo:** Validar o retorno da listagem completa de jogos.
* **Método HTTP:** `GET`
* **URL:** `{{baseURL}}/games`
* **Resultado Esperado:** 
  * Status code `200 OK`.
  * `success` igual a `true`.
  * `data` deve ser um Array.
  * `count` deve ser um valor do tipo Number.

---

### **CT02 - Get game by ID - Retornar um game a partir do seu ID**
* **Objetivo:** Consultar um jogo específico através do seu identificador único.
* **Método HTTP:** `GET`
* **URL:** `{{baseURL}}/games/:game_id`
* **Parâmetros de Path:** `game_id = 5f6ce9d805615a85623ec2b8`
* **Resultado Esperado:**
  * Status code `200 OK`.
  * O campo `id` do retorno deve ser exatamente igual ao `game_id` informado.
  * O campo `name` deve ser `"The Legend of Zelda: A Link to the Past"`.

---

### **CT03 - Validar os campos obrigatórios da lista de games**
* **Objetivo:** Garantir que todos os objetos do contrato de jogos possuem as propriedades fundamentais.
* **Método HTTP:** `GET`
* **URL:** `{{baseURL}}/games`
* **Resultado Esperado:**
  * Status code `200 OK`.
  * Cada objeto retornado dentro de `data` deve conter as propriedades: `name`, `description`, `developer`, `publisher`, `released_date` e `id`.

---

### **CT04 - Validar a quantidade padrão de itens retornados na lista**
* **Objetivo:** Validar o limite de paginação default da API quando nenhum parâmetro é enviado.
* **Método HTTP:** `GET`
* **URL:** `{{baseURL}}/games`
* **Resultado Esperado:**
  * Status code `200 OK`.
  * O valor da propriedade `count` deve ser exatamente `20`.
  * O array `data` deve conter exatamente 20 elementos.

---

### **CT05 - Validar a quantidade de itens retornados com parâmetro LIMIT**
* **Objetivo:** Validar se a API respeita o filtro de quantidade solicitada pelo cliente.
* **Método HTTP:** `GET`
* **URL:** `{{baseURL}}/games?limit=3`
* **Query Params:** `limit = 3`
* **Resultado Esperado:**
  * Status code `200 OK`.
  * O array `data` e o atributo `count` devem possuir tamanho igual a `3`.

---

### **CT06 - Validar se a paginação é retornada corretamente**
* **Objetivo:** Garantir que o deslocamento de páginas via parâmetro `page` retorne a estrutura esperada.
* **Método HTTP:** `GET`
* **URL:** `{{baseURL}}/games?limit=3&page=10`
* **Query Params:** `limit = 3`, `page = 10`
* **Resultado Esperado:**
  * Status code `200 OK`.
  * Propriedades `success`, `count` e `data` presentes no corpo da resposta.
  * O tamanho da lista `data` deve ser de no máximo 3 itens.

---

### **CT07 - Validar que páginas diferentes retornam itens diferentes**
* **Objetivo:** Garantir a unicidade dos dados entre diferentes páginas de consulta (prevenção de dados duplicados).
* **Método HTTP:** `GET`
* **URL:** `{{baseURL}}/games?limit=3&page=9`
* **Pre-request Script:** Realiza requisição para a página 10 e armazena os IDs retornados em variável temporária (`page10Data`).
* **Resultado Esperado:**
  * Status code `200 OK`.
  * Nenhum ID retornado na página 9 deve estar presente no array de IDs da página 10 (`hasOverlap = false`).

---

### **CT08 - Get game by NAME - Retornar um game por correspondência de nome**
* **Objetivo:** Validar a funcionalidade de busca e filtragem por nome de jogo.
* **Método HTTP:** `GET`
* **URL:** `{{baseURL}}/games/?name=Link`
* **Query Params:** `name = Link`
* **Resultado Esperado:**
  * Status code `200 OK`.
  * O atributo `count` deve ser maior que 0.
  * Todos os itens dentro do array `data` devem conter o termo `"Link"` na propriedade `name`.

---

## 📁 2. Negative Testing (Tratamento de Exceções)

### **CT01 - Validar ID inexistente com formato válido**
* **Objetivo:** Avaliar o comportamento da API ao buscar um registro que não existe na base, porém com formato sintático correto (ObjectID MongoDB 24 chars).
* **Método HTTP:** `GET`
* **URL:** `{{baseURL}}/games/:invalid-id`
* **Parâmetros de Path:** `invalid-id = 600000000000000000000000`
* **Resultado Esperado (Validação do Teste):**
  * Status Code retornado deve ser de erro (`400 Bad Request` ou `404 Not Found`).
  * Nota de QA: Espera-se `404 Not Found` no padrão REST.
  * O atributo `success` no JSON deve ser `false`.

---

### **CT02 - Validar ID inexistente com formato inválido**
* **Objetivo:** Avaliar a resposta da API ao receber um ID com sintaxe completamente incorreta.
* **Método HTTP:** `GET`
* **URL:** `{{baseURL}}/games/:invalid-id`
* **Parâmetros de Path:** `invalid-id = 1234`
* **Resultado Esperado:**
  * Status Code: `400 Bad Request`.
  * O atributo `success` deve ser `false`.

---

### **CT03 - Validar se a paginação é retornada corretamente quando não há itens**
* **Objetivo:** Validar a integridade da resposta ao consultar uma página além do total existente na base.
* **Método HTTP:** `GET`
* **URL:** `{{baseURL}}/games?limit=3&page=100`
* **Query Params:** `limit = 3`, `page = 100`
* **Resultado Esperado:**
  * Status Code `200 OK`.
  * Atributo `success` como `true`.
  * Array `data` retornado como vazio (`[]`).
  * Atributo `count` igual a `0`.

---

## 📁 3. Improvements (Edge Cases & Falhas de Validação)

### **CT01 - Valor negativo em paginação não deveria retornar nada**
* **Objetivo:** Verificar se a API rejeita parâmetros inválidos de paginação (valores negativos).
* **Método HTTP:** `GET`
* **URL:** `{{baseURL}}/games?page=-10`
* **Query Params:** `page = -10`
* **Resultado Esperado:**
  * Status Code esperado pela especificação: `400 Bad Request`.

---

### **CT02 - Validar tipo de dado incorreto em query param limit**
* **Objetivo:** Verificar a validação de tipagem nos parâmetros de query.
* **Método HTTP:** `GET`
* **URL:** `{{baseURL}}/games?limit=abcd`
* **Query Params:** `limit = abcd`
* **Resultado Esperado:**
  * Status Code esperado pela especificação: `400 Bad Request`.

---

### **CT03 - Validar método HTTP não suportado**
* **Objetivo:** Testar se a API bloqueia a execução de operações de escrita em um endpoint *read-only*.
* **Método HTTP:** `PUT`
* **URL:** `{{baseURL}}/games/{{aLinkToThePastID}}`
* **Body:** JSON estruturado contendo dados fictícios de jogo.
* **Resultado Esperado:**
  * Status code deve indicar erro de cliente (`400`, `403`, `404` ou `405`).
  * Padrão REST ideal: `405 Method Not Allowed`.