# Aula 06 – Planejamento e Execução de Testes

**Sistema:** LocalEats  
**Disciplina:** Qualidade de Software  
**Metodologia:** PBL  
**Integrante:** Gabriel Piske 
**Data:** Abril/2026


## 1. Plano de Testes

### 1.1 Objetivo

Validar as principais funcionalidades do sistema LocalEats, verificando se o comportamento real da aplicação está de acordo com o comportamento esperado. O foco é identificar falhas funcionais, inconsistências de interface e comportamentos inesperados que impactem a experiência do usuário.

### 1.2 Escopo

**O que será testado:**

- Carregamento e exibição da listagem de restaurantes (página Explorar)
- Filtragem de restaurantes por categoria (Italiana, Japonesa, Brasileira, Mexicana)
- Busca de restaurantes por nome ou termo
- Exibição da página "Meus Favoritos"
- Exibição da página "Meus Pedidos" (Histórico de Transações)

**O que NÃO será testado:**

- Integração com backend/API externas (caixa-preta, sem acesso ao código)
- Fluxo de autenticação (o sistema não apresenta tela de login)
- Processo de pagamento (funcionalidade não identificada no sistema)
- Testes de performance e carga
- Testes em navegadores mobile nativos

### 1.3 Funcionalidades Selecionadas

| # | Funcionalidade | Justificativa |
|---|---|---|
| 1 | Listagem de restaurantes | Função central do sistema |
| 2 | Filtro por categoria | Funcionalidade de navegação principal |
| 3 | Busca por termo | Funcionalidade de descoberta de restaurantes |
| 4 | Página Meus Favoritos | Funcionalidade de personalização do usuário |
| 5 | Página Meus Pedidos | Funcionalidade de histórico do usuário |

### 1.4 Estratégia de Testes

**Tipos de teste utilizados:**

- **Testes Funcionais (Caixa-Preta):** verificação do comportamento da interface a partir das entradas e saídas visíveis, sem análise do código-fonte.

**Abordagem adotada:**

Os testes serão executados manualmente no navegador Google Chrome (versão desktop), acessando diretamente a URL `https://local-eats-unisenac.vercel.app/`. Para cada caso de teste, serão registrados o resultado obtido e uma evidência descritiva. Casos onde o sistema não retornar resposta válida serão classificados como **Falhou**.

### 1.5 Responsáveis

| Responsável | Atividade |

| Gabriel | Elaboração do plano, especificação e execução dos casos de teste, registro de resultados e análise |


## 2. Casos de Teste

> Linguagem utilizada: **Gherkin**


### CT01 – Carregamento da listagem de restaurantes

**Tipo:** Cenário de Sucesso (Happy Path)  
**Pré-condição:** Navegador aberto, sem cache. Acesso à internet disponível.

```gherkin
Dado que acesso a URL https://local-eats-unisenac.vercel.app/
Quando a página terminar de carregar
Então o sistema deve exibir uma lista de restaurantes
E cada restaurante deve apresentar nome, imagem e categoria
```

**Dados de entrada:** Nenhum (acesso direto à URL)  
**Resultado esperado:** Lista de restaurantes exibida com pelo menos um item visível, sem erros de carregamento.


### CT02 – Filtro por categoria "Italiana"

**Tipo:** Cenário de Sucesso (Happy Path)  
**Pré-condição:** Página inicial carregada com restaurantes visíveis.

```gherkin
Dado que estou na página inicial do LocalEats
E que a listagem de restaurantes está carregada
Quando eu clicar no filtro "Italiana"
Então o sistema deve exibir somente restaurantes da categoria Italiana
E restaurantes de outras categorias não devem aparecer na listagem
```

**Dados de entrada:** Clique no botão "Italiana"  
**Resultado esperado:** Listagem filtrada exibindo apenas restaurantes italianos.


### CT03 – Busca por termo existente

**Tipo:** Cenário de Sucesso (Happy Path)  
**Pré-condição:** Página inicial carregada.

```gherkin
Dado que estou na página inicial do LocalEats
E que a listagem de restaurantes está carregada
Quando eu digitar um termo correspondente a um restaurante existente no campo de busca
Então o sistema deve exibir somente os restaurantes cujo nome contém o termo digitado
```

**Dados de entrada:** Termo de busca: nome parcial ou completo de um restaurante visível na listagem  
**Resultado esperado:** Resultado filtrado contendo o restaurante pesquisado.


### CT04 – Acesso à página "Meus Favoritos"

**Tipo:** Cenário de Sucesso (Happy Path)  
**Pré-condição:** Página inicial carregada.

```gherkin
Dado que estou em qualquer página do LocalEats
Quando eu clicar no link "Meus Favoritos" no menu de navegação
Então o sistema deve redirecionar para a página de favoritos
E a página deve exibir um título ou indicação de conteúdo (favoritos do usuário ou mensagem de lista vazia)
```

**Dados de entrada:** Clique no link "Meus Favoritos"  
**Resultado esperado:** Página carregada com título "Restaurantes Favoritos" visível, sem erros.


### CT05 – Acesso à página "Meus Pedidos"

**Tipo:** Cenário de Sucesso (Happy Path)  
**Pré-condição:** Página inicial carregada.

```gherkin
Dado que estou em qualquer página do LocalEats
Quando eu clicar no link "Meus Pedidos" no menu de navegação
Então o sistema deve redirecionar para a página de histórico de pedidos
E a página deve exibir o título "Histórico de Transações" ou uma mensagem de lista vazia
```

**Dados de entrada:** Clique no link "Meus Pedidos"  
**Resultado esperado:** Página carregada corretamente, sem erros de renderização.


### CT06 – Busca por termo inexistente

**Tipo:** Cenário de Erro  
**Pré-condição:** Página inicial carregada com restaurantes visíveis.

```gherkin
Dado que estou na página inicial do LocalEats
E que a listagem de restaurantes está carregada
Quando eu digitar um termo que não corresponde a nenhum restaurante no campo de busca
Então o sistema deve exibir uma mensagem informando que nenhum resultado foi encontrado
E a listagem deve aparecer vazia, sem exibir restaurantes incorretos
```

**Dados de entrada:** Termo de busca: `"xyzrestauranteinexistente123"`  
**Resultado esperado:** Listagem vazia com mensagem informativa ao usuário (ex: "Nenhum restaurante encontrado").


### CT07 – Filtro por categoria sem resultados ou com falha de resposta

**Tipo:** Cenário de Erro  
**Pré-condição:** Página inicial carregada.

```gherkin
Dado que estou na página inicial do LocalEats
Quando eu clicar em um filtro de categoria
E o sistema não retornar nenhum restaurante para aquela categoria
Então o sistema deve exibir uma mensagem informativa indicando ausência de resultados
E não deve travar, exibir erro genérico ou ficar em estado de carregamento indefinido
```

**Dados de entrada:** Clique em qualquer filtro de categoria (ex: "Mexicana")  
**Resultado esperado:** Comportamento claro: lista com resultados ou mensagem de "nenhum resultado encontrado". Sem estado de carregamento infinito.


## 3. Execução dos Testes

| ID | Título | Resultado | Evidência |

| CT01 | Carregamento da listagem de restaurantes | **Falhou** | A página exibe a mensagem "Carregando os melhores restaurantes..." indefinidamente, sem apresentar nenhum restaurante. A listagem nunca é renderizada. |
| CT02 | Filtro por categoria "Italiana" | **Falhou** | Como a listagem não carrega (CT01), os botões de filtro existem na interface, mas a ação não produz resultado visível — nenhum restaurante é exibido após o clique. |
| CT03 | Busca por termo existente | **Falhou** | O campo de busca está presente na interface, porém como a listagem não carrega, nenhuma busca retorna resultado. O sistema permanece em estado de "carregamento". |
| CT04 | Acesso à página "Meus Favoritos" | **Passou** | O clique em "Meus Favoritos" navega corretamente para `/static/profile.html`. A página carrega com o título "Restaurantes Favoritos" visível. O conteúdo exibe "Carregando seus locais do coração...", mas a estrutura da página funciona. |
| CT05 | Acesso à página "Meus Pedidos" | **Passou** | O clique em "Meus Pedidos" navega corretamente para `/static/orders.html`. A página carrega com o título "Histórico de Transações" visível. Estrutura funcional, mesmo com conteúdo em carregamento. |
| CT06 | Busca por termo inexistente | **Falhou** | Não foi possível validar o comportamento de erro, pois o sistema não carrega a listagem. Nenhuma mensagem de "resultado não encontrado" é exibida — o sistema permanece em estado de carregamento. |
| CT07 | Filtro sem resultados ou com falha | **Falhou** | O sistema permanece em estado de "carregando" após qualquer interação com filtros. Não exibe mensagem de erro nem de ausência de resultados. Comportamento indefinido. |


## 4. Análise dos Resultados

| Métrica | Valor |

| Total de testes executados | 7 |
| Testes que passaram | 2 |
| Testes que falharam | 5 |
| Taxa de sucesso | 28,6% |

### Principais problemas encontrados

**1. Listagem de restaurantes não carrega**  
A falha mais crítica do sistema. A página principal exibe indefinidamente a mensagem "Carregando os melhores restaurantes...", sem nunca renderizar os dados. Esse problema é **bloqueante**, pois impede a validação de todas as funcionalidades dependentes da listagem (busca, filtros e, possivelmente, favoritos e pedidos).

**Hipótese técnica:** A falha provavelmente está relacionada a uma chamada de API ou endpoint externo que não retorna dados (erro de CORS, endpoint indisponível, dados inexistentes no banco ou falha de integração com o backend).

**2. Filtros e busca inutilizados pela falha de carregamento**  
Como consequência direta do problema anterior, os filtros por categoria e o campo de busca existem na interface, mas não produzem nenhum efeito observável. Não é possível distinguir se são bugs independentes ou se dependem exclusivamente do carregamento da listagem.

**3. Ausência de feedback de erro para o usuário**  
Em nenhum momento o sistema apresenta uma mensagem de erro ao usuário (ex: "Não foi possível carregar os restaurantes"). O estado de carregamento infinito é uma experiência ruim e não comunica o problema adequadamente.

**4. Páginas de navegação funcionais, porém com conteúdo em espera**  
As páginas "Meus Favoritos" e "Meus Pedidos" carregam corretamente do ponto de vista de estrutura e navegação, mas seus conteúdos também ficam presos em estado de carregamento, sugerindo que a mesma causa raiz afeta todo o sistema.


## 5. Reflexão no Contexto do LocalEats

**O plano de testes ajudou a organizar melhor os testes?**

Sim. Definir o escopo antecipadamente foi essencial para não perder tempo testando funcionalidades fora do alcance (como autenticação, que não existe). A separação entre cenários de sucesso e de erro também permitiu identificar que o problema do sistema é estrutural — não é uma falha isolada em um cenário específico, mas uma falha que impacta a maioria dos fluxos de forma encadeada.

**Algum problema só foi percebido durante a execução?**

Sim. Antes da execução, a hipótese era que os filtros e a busca poderiam ter bugs isolados. Somente ao executar os testes ficou evidente que todos esses problemas derivam de uma única causa raiz: a listagem de restaurantes não carrega. Isso mudou completamente a análise — em vez de múltiplos bugs independentes, temos um único ponto de falha crítico.

Outro ponto percebido durante a execução foi a ausência total de tratamento de erro na interface. Não há nenhuma mensagem informativa ao usuário quando o carregamento falha, o que é um problema de UX relevante, independentemente da causa técnica.

**O que melhorariam no processo de testes?**

- **Acesso ao código-fonte ou à documentação da API:** com isso, seria possível identificar com exatidão se a falha está no frontend (fetch incorreto, URL errada) ou no backend (endpoint fora do ar, banco vazio). Testes de caixa-branca seriam muito mais eficazes aqui.
- **Testes em múltiplos navegadores:** os testes foram realizados somente no Chrome desktop. Diferentes comportamentos poderiam surgir no Firefox ou em dispositivos móveis.
- **Automação com Cypress ou Playwright:** para um sistema com esse nível de falha de carregamento assíncrono, ferramentas de automação com suporte a `waitForSelector` e interceptação de requisições de rede seriam ideais para diagnosticar com precisão onde o carregamento falha.
- **Inclusão de testes de API (Postman/Insomnia):** verificar diretamente se os endpoints que alimentam a listagem estão retornando dados seria o próximo passo natural na investigação.
