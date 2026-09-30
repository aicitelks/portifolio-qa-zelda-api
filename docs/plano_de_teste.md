# 📋 Plano de Teste - Zelda API (Endpoint /games)

## 1. Introdução

Este documento especifica a estratégia e o planejamento de testes para a verificação e validação da [**Zelda API**](https://docs.zelda.fanapis.com/docs/), com foco inicial no recurso `/games`. O objetivo principal é garantir a confiabilidade, integridade de dados e aderência às especificações do protocolo HTTP/REST.

## 2. Escopo dos Testes

### 2.1 Dentro do Escopo (In-Scope)

* **Funcionalidade / Caminho Feliz (Positive Testing):**

  * Listagem geral de jogos (`GET /games`).

  * Consulta por ID específico (`GET /games/:id`).

  * Validação de schema e propriedades obrigatórias (`name`, `description`, `developer`, `publisher`, `released_date`, `id`).

  * Paginação e limites de exibição (`limit`, `page`).

  * Garantia de não sobreposição de dados entre páginas distintas.

  * Busca pelo nome do jogo de forma parcial no campo correspondente (`name`).

* **Tratamento de Erros (Negative Testing):**

  * Consulta com IDs inexistentes (formatos válidos e inválidos).

  * Comportamento da paginação ao solicitar páginas sem registros.

* **Validação de Robustez e Limites (Improvements / Edge Cases):**

  * Envio de parâmetros de paginação negativos.

  * Envio de tipos de dados inválidos em parâmetros de query (`limit=abcd`).

  * Envio de métodos HTTP não suportados/bloqueados (`PUT`).

### 2.2 Fora do Escopo (Out-of-Scope)

* Testes de carga, estresse ou performance avançados.

* Testes de autenticação e autorização (a API é pública e *read-only*).

* Validação de outros endpoints da API (Staff, Characters, Monsters, Dungeons, Places, Bosses, Items) nesta fase.

## 3. Tipos de Teste Aplicados

| Tipo de Teste | Descrição / Objetivo | 
 | ----- | ----- | 
| **Testes Funcionais** | Garantir que as consultas retornem os dados corretos de acordo com os parâmetros informados. | 
| **Testes de Contrato / Schema** | Validar se os tipos de dados e os campos obrigatórios retornados estão de acordo com o padrão esperado. | 
| **Testes Negativos** | Validar a resiliência e o tratamento de mensagens de erro frente a dados malformados ou inexistentes. | 
| **Testes Exploratórios / Edge Cases** | Identificar comportamentos inconsistentes de parâmetros de query e métodos HTTP não permitidos. | 

## 4. Ambiente de Teste e Ferramentas

* **Base URL:** `https://zelda.fanapis.com/api` (Gerida via variável de ambiente `{{baseURL}}`).

* **Ferramenta de Automação:** Postman (v10+).

* **Executor CLI:** Newman.

* **Relatórios de Execução:** Newman HTML Reporter / Allure Reports.

* **Linguagem de Assertion:** JavaScript (Chai.js / Postman BDD API).

## 5. Critérios de Aceitação e Qualidade

* **Critério de Sucesso:** 100% dos cenários de *Positive Testing* devem passar com status `200 OK` e dados compatíveis.

* **Critério de Falha / Bug:**

  * Retorno de dados inconsistentes ou incorretos.

  * Erros sintáticos no schema retornado.

  * Mascaramento de erros de cliente (`4xx`) respondidos com status de sucesso (`200 OK`).

## 6. Riscos Mapeados

| Risco | Impacto | Mitigação | 
 | ----- | ----- | ----- | 
| Instabilidade ou indisponibilidade da API pública externa | Alto | Uso de mocks ou adição de retries nos scripts do Newman em integrações futuras. | 
| Divergências nos códigos de status HTTP em relação ao padrão REST | Médio | Asserções flexíveis com `oneOf` e documentação técnica dos desvios em relatórios de bugs. | 
