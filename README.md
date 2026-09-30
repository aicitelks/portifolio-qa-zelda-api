# 🗡️ Zelda API - QA Automation & Manual Testing Portfolio

![Postman](https://img.shields.io/badge/Postman-FF6C37?style=for-the-badge&logo=postman&logoColor=white)
![Newman](https://img.shields.io/badge/Newman-FF6C37?style=for-the-badge&logo=postman&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)
![NodeJS](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white)
![WSL](https://img.shields.io/badge/WSL2-0078D6?style=for-the-badge&logo=windows&logoColor=white)
![License MIT](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

> Projeto prático de QA focado no mapeamento, documentação e automação de testes de API REST (somente leitura) para o ecossistema da **Zelda API**.

---

## 📌 Sobre o Projeto

Este repositório faz parte do meu portfólio de Engenharia de Qualidade de Software (QA). O objetivo principal é demonstrar a aplicação de cenários de **Testes Funcionais**, **Testes de Contrato**, **Testes Negativos** e **Validação de Parâmetros**, além de relatar inconsistências de protocolo e regras de negócio.

A API utilizada como objeto de estudo é a <a href="https://docs.zelda.fanapis.com/docs/">Zelda FanAPI </a>, com foco no recurso principal `/games`.

---

## 🎯 Tipos de Testes Cobertos

A coleção do Postman foi estruturada nas seguintes categorias:

* 🟢 **Positive Testing:**
  * Listagem geral de jogos com verificação de contrato e tipos de dados.
  * Consulta por ID específico com asserção de propriedades obrigatórias (`name`, `developer`, `publisher`, etc.).
  * Validação de filtros por nome e paginação (`limit` e `page`).
  * Teste de paginação encadeada (garantindo que páginas distintas retornam registros diferentes sem sobreposição).

* 🔴 **Negative Testing:**
  * Consulta com IDs inexistentes (formatos válidos e inválidos).
  * Comportamento da API em páginas fora do limite da base de dados (retorno de listas vazias).

* ⚠️ **Edge Cases:**
  * Envio de parâmetros de paginação negativos (`page=-10`).
  * Envio de tipos de dados inválidos em query parameters (`limit=abcd`).
  * Bloqueio de métodos HTTP não suportados (`PUT`, `POST`, `DELETE`) em endpoints de leitura.

---

## 📁 Estrutura da Documentação

A documentação técnica do projeto está organizada na pasta `docs/`:

| Documento | Descrição | Link |
| :--- | :--- | :--- |
| 📋 **Plano de Teste** | Estratégia, escopo, ferramentas e gestão de riscos. | [Acessar](./docs/plano_de_teste.md) |
| 🧪 **Casos de Teste** | Mapeamento detalhado dos cenários e asserções. | [Acessar](./docs/casos_de_teste.md) |
| 🐛 **Relatório de Bugs** | Reporte de divergências de status code e validações. | [Acessar](./docs/relatorio_de_bugs_zelda_api.md) |

---

## 📊 Relatórios de Execução com Newman & HTML Extra

Os testes foram automatizados e executados via linha de comando com o **Newman** no ambiente **WSL2 (Ubuntu)**, gerando relatórios gráficos e interativos através do `newman-reporter-htmlextra`. 

- Para visualizar **o relatório completo** basta acessar [aqui](./evidencias/Zelda%20API-report.html) e baixar o arquivo.

<img width="1126" height="582" alt="Zelda API" src="https://github.com/user-attachments/assets/c39a0875-f383-48b7-b739-7198c0f1347a" />


#### 🚀 Como executar na sua máquina local
Passos a serem executados para executar a coleção localmente e obter o relatório Newman:

1. **Pré-requisitos:**
   * Node.js (v18 ou superior)
   * NPM ou NVM instalado

2. **Instalar o Newman e o Reporter:**
   ```bash
   npm install -g newman newman-reporter-htmlextra

3. **Clonar o Repositório:**
    ```bash
    git clone [https://github.com/SEU_USUARIO/portifolio-qa-zelda-api.git](https://github.com/SEU_USUARIO/portifolio-qa-zelda-api.git)
    cd portifolio-qa-zelda-api
   
4. **Executar a Coleção e Gerar o Dashboard HTML:**
    ```bash
    newman run "Zelda API.postman_collection.json" -e "Zelda-API-Dev.postman_environment.json" -r cli,htmlextra

5. **Visualizar o Relatório:**
   Acesse a pasta /newman gerada na raiz do projeto e abra o arquivo .html no seu navegador!

---

### 🛠️ Tecnologias e Ferramentas Utilizadas
- Postman: Criação das requisições, variáveis de ambiente e scripts de validação em JavaScript.
- Newman: Execution Runner de testes via CLI.
- HTML Extra Reporter: Gerador de relatórios visuais de qualidade.
- WSL2 / Ubuntu: Ambiente de execução no Windows.
- VS Code: Editor de código e gestão do repositório.

---
<div align="center">

  Desenvolvido com 💚 e foco em qualidade por **Le Castro** 👩‍💻✨

  [![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/leticiacastro87)
  [![GitHub](https://img.shields.io/badge/GitHub-100000?style=for-the-badge&logo=github&logoColor=white)](https://github.com/aicitelks)

</div>
