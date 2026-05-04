# Planejamento e Execução de Testes

**Disciplina:** Qualidade de Software  
**Projeto:** LocalEats  
**Integrantes do grupo:**

* Gabriel Piske


## 1. Plano de Testes

### 1.1 Objetivo

Validar as principais funcionalidades do sistema LocalEats, verificando se o comportamento real da aplicação está de acordo com o comportamento esperado. O foco é identificar falhas funcionais, inconsistências de interface e comportamentos inesperados que impactem a experiência do usuário.

### 1.2 Escopo

**O que será testado:**

* Carregamento e exibição da listagem de restaurantes (página Explorar)
* Filtragem de restaurantes por categoria (Italiana, Japonesa, Brasileira, Mexicana)
* Busca de restaurantes por nome ou termo
* Exibição da página "Meus Favoritos"
* Exibição da página "Meus Pedidos" (Histórico de Transações)
* Navegação interna da página de detalhes do restaurante (abas Cardápio e Avaliações)

**O que NÃO será testado:**

* Integração com backend/APIs externas (caixa-preta, sem acesso ao código)
* Fluxo de autenticação (o sistema não apresenta tela de login)
* Processo de pagamento (funcionalidade não identificada no sistema)
* Testes de performance e carga
* Testes em navegadores mobile nativos

### 1.3 Funcionalidades Selecionadas

* Listagem de restaurantes
* Filtro por categoria
* Busca por termo
* Página Meus Favoritos
* Página Meus Pedidos
* Aba "Cardápio" na página do restaurante
* Aba "Avaliações" na página do restaurante

### 1.4 Estratégia de Testes

* **Tipos de teste:**
  * (x) Funcional
  * ( ) Usabilidade
  * ( ) Outros

* **Abordagem:**  
Testes manuais baseados em cenários definidos previamente, executados no navegador Google Chrome (versão desktop), acessando diretamente a URL `https://local-eats-unisenac.vercel.app/`. Para cada caso de teste, serão registrados o resultado obtido e uma evidência descritiva. Casos onde o sistema não retornar resposta válida serão classificados como **Falhou**.

### 1.5 Responsáveis

| Nome | Responsabilidade |

| Gabriel | Elaboração do plano, especificação e execução dos casos de teste, registro de resultados e análise |


## 2. Casos de Teste

### CT-01 – Carregamento da listagem de restaurantes

**Pré-condição:** Navegador aberto, sem cache. Acesso à internet disponível.

**Passos:**
1. Acessar a URL `https://local-eats-unisenac.vercel.app/`
2. Aguardar o carregamento completo da página
3. Verificar se a listagem de restaurantes é exibida

**Dados de entrada (se aplicável):** Nenhum (acesso direto à URL)

**Resultado esperado:** Lista de restaurantes exibida com pelo menos um item visível, apresentando nome, imagem e categoria de cada estabelecimento, sem erros de carregamento.


### CT-02 – Filtro por categoria "Italiana"

**Pré-condição:** Página inicial carregada com restaurantes visíveis.

**Passos:**
1. Acessar a página inicial do LocalEats
2. Confirmar que a listagem de restaurantes está carregada
3. Clicar no botão de filtro "Italiana"
4. Observar os resultados exibidos na listagem

**Dados de entrada (se aplicável):** Clique no botão "Italiana"

**Resultado esperado:** Listagem filtrada exibindo apenas restaurantes da categoria Italiana. Restaurantes de outras categorias não devem aparecer.


### CT-03 – Busca por termo existente

**Pré-condição:** Página inicial carregada com restaurantes visíveis.

**Passos:**
1. Acessar a página inicial do LocalEats
2. Localizar o campo de busca
3. Digitar o nome parcial ou completo de um restaurante visível na listagem
4. Observar os resultados retornados

**Dados de entrada (se aplicável):** Nome parcial ou completo de um restaurante existente na listagem

**Resultado esperado:** Listagem filtrada exibindo somente os restaurantes cujo nome contém o termo digitado.


### CT-04 – Acesso à página "Meus Favoritos"

**Pré-condição:** Página inicial carregada.

**Passos:**
1. Estar em qualquer página do LocalEats
2. Localizar o link "Meus Favoritos" no menu de navegação
3. Clicar no link
4. Verificar o carregamento e o conteúdo da página

**Dados de entrada (se aplicável):** Clique no link "Meus Favoritos"

**Resultado esperado:** Redirecionamento correto para a página de favoritos, com título "Restaurantes Favoritos" visível e sem erros de renderização.


### CT-05 – Acesso à página "Meus Pedidos"

**Pré-condição:** Página inicial carregada.

**Passos:**
1. Estar em qualquer página do LocalEats
2. Localizar o link "Meus Pedidos" no menu de navegação
3. Clicar no link
4. Verificar o carregamento e o conteúdo da página

**Dados de entrada (se aplicável):** Clique no link "Meus Pedidos"

**Resultado esperado:** Redirecionamento correto para a página de histórico, com título "Histórico de Transações" visível e sem erros de renderização.


### CT-06 – Navegação para aba "Cardápio" dentro do restaurante

**Pré-condição:** Página de detalhes de um restaurante aberta. Produtos do restaurante visíveis na tela.

**Passos:**
1. Acessar a página inicial e selecionar qualquer restaurante disponível
2. Confirmar que os produtos do restaurante estão sendo exibidos
3. Localizar o botão "Cardápio" na página
4. Clicar no botão e observar o comportamento da interface

**Dados de entrada (se aplicável):** Clique no botão "Cardápio"

**Resultado esperado:** Exibição do conteúdo da aba "Cardápio" com alguma resposta visual ao clique (troca de aba, destaque do botão ativo, ou atualização do conteúdo). Nenhum erro deve ocorrer na interface.


### CT-07 – Navegação para aba "Avaliações" dentro do restaurante

**Pré-condição:** Página de detalhes de um restaurante aberta. Avaliações do restaurante visíveis na tela.

**Passos:**
1. Acessar a página inicial e selecionar qualquer restaurante disponível
2. Confirmar que as avaliações do restaurante estão visíveis
3. Localizar o botão "Avaliações" na página
4. Clicar no botão e observar o comportamento da interface

**Dados de entrada (se aplicável):** Clique no botão "Avaliações"

**Resultado esperado:** Exibição do conteúdo da aba "Avaliações" com alguma resposta visual ao clique. Nenhum erro deve ocorrer na interface.


## 3. Execução dos Testes

| ID | Resultado (Passou/Falhou) | Evidência (descrição ou print) |
|---|---|---|
| CT-01 | **Passou** | A página carregou corretamente exibindo a lista de restaurantes com nome, imagem e categoria de cada estabelecimento. |
| CT-02 | **Passou** | Ao clicar no filtro "Italiana", a listagem foi atualizada exibindo somente restaurantes dessa categoria. Restaurantes de outras categorias deixaram de aparecer. |
| CT-03 | **Passou** | Ao digitar o nome de um restaurante existente, a listagem foi filtrada corretamente, exibindo apenas o resultado correspondente ao termo digitado. |
| CT-04 | **Passou** | O clique em "Meus Favoritos" navegou corretamente para `/static/profile.html`. A página carregou com o título "Restaurantes Favoritos" visível, sem erros. |
| CT-05 | **Passou** | O clique em "Meus Pedidos" navegou corretamente para `/static/orders.html`. A página carregou com o título "Histórico de Transações" visível, sem erros. |
| CT-06 | **Falhou** | Os produtos do restaurante são exibidos corretamente. Porém, ao clicar no botão "Cardápio", nenhuma ação ocorre — não há resposta visual, troca de conteúdo ou feedback ao usuário. O botão existe na interface mas não possui comportamento funcional. |
| CT-07 | **Falhou** | As avaliações do restaurante estão visíveis na página. Ao clicar no botão "Avaliações", nenhuma ação ocorre — o sistema não responde ao clique, sem troca de aba, redirecionamento ou qualquer feedback visual. O botão aparenta estar inativo. |


## 4. Análise dos Resultados

* **Quantidade de testes executados:** 7
* **Quantidade de testes que passaram:** 5
* **Quantidade de testes que falharam:** 2

**Principais problemas encontrados:**

* **Botão "Cardápio" sem funcionamento:** o botão existe visualmente na página de detalhes do restaurante, mas não responde ao clique. Nenhuma troca de conteúdo, navegação ou feedback visual ocorre. Hipótese: event listener ausente ou handler de clique não implementado.
* **Botão "Avaliações" sem funcionamento:** comportamento idêntico ao botão "Cardápio". Ambos aparentam ser elementos de navegação por abas (tab navigation) com a lógica de troca de conteúdo incompleta.
* **Ausência de feedback visual de estado:** nenhum dos botões de aba indica qual está ativo ou selecionado, tornando a experiência confusa para o usuário mesmo quando o conteúdo está presente na tela.


## 5. Reflexão

**O plano de testes ajudou a organizar melhor o processo? Por quê?**

Sim. Definir o escopo antecipadamente foi essencial para não perder tempo testando funcionalidades fora do alcance, como autenticação, que não existe no sistema. A inclusão das abas "Cardápio" e "Avaliações" no escopo, identificada durante a exploração inicial da aplicação, demonstrou que o plano deve ser atualizado conforme o conhecimento sobre o sistema aumenta.

**Algum problema só foi identificado durante a execução? Explique.**

Sim. A falha nos botões "Cardápio" e "Avaliações" só foi identificada durante a execução real dos testes. Na fase de planejamento, a hipótese era que a navegação interna das abas funcionaria normalmente, já que o conteúdo estava visível na tela. A execução revelou que os botões existem visualmente mas não possuem comportamento funcional associado, um tipo de bug sutil que dificilmente seria detectado sem interação direta com o sistema.

**O que o grupo melhoraria no processo de testes?**

* **Exploração inicial mais ampla antes do planejamento:** percorrer todas as telas do sistema antes de definir os casos de teste teria permitido identificar as abas da página do restaurante já na fase de planejamento, evitando ajustes posteriores no escopo.
* **Testes em múltiplos navegadores:** os testes foram realizados somente no Chrome desktop. Diferentes comportamentos poderiam surgir no Firefox ou em dispositivos móveis.
* **Automação com Cypress ou Playwright:** para validar interações de clique e troca de abas de forma confiável e reproduzível, ferramentas de automação com suporte a verificação de estado de elementos seriam ideais.
* **Inspeção do console do navegador:** verificar erros JavaScript durante os cliques nos botões seria o próximo passo para diagnosticar com precisão se há um erro de execução silencioso nos handlers.

**Conclusão**

O comportamento geral do sistema LocalEats foi considerado parcialmente aceitável. As funcionalidades de navegação principal (listagem, filtros, busca e páginas de perfil) funcionam conforme o esperado. Porém, as abas "Cardápio" e "Avaliações" dentro da página do restaurante apresentam falhas que comprometem a experiência do usuário, pois os botões existem na interface sem possuir comportamento funcional implementado. Para que o sistema seja considerado totalmente aceitável, essas interações precisam ser corrigidas.


## 6. Conclusão Geral

**Qualidade geral do sistema testado:**  
O sistema LocalEats apresenta qualidade parcial. A estrutura de navegação principal funciona adequadamente, mas funcionalidades internas da página de detalhes do restaurante estão incompletas, o que reduz a qualidade geral da aplicação.

**Principais pontos positivos:**

* Listagem de restaurantes carrega corretamente com nome, imagem e categoria
* Filtros por categoria funcionam conforme esperado
* Campo de busca retorna resultados corretos
* Navegação entre páginas (Favoritos e Pedidos) funciona sem erros

**Principais problemas identificados:**

* Botão "Cardápio" na página do restaurante não responde ao clique
* Botão "Avaliações" na página do restaurante não responde ao clique
* Ausência de feedback visual de estado nos botões de aba, tornando a experiência confusa

**Impressão geral sobre o processo de testes:**

O processo de planejamento e execução de testes se mostrou eficaz para identificar problemas que não seriam evidentes em uma análise superficial da interface. A estrutura de casos de teste facilitou a descrição clara dos cenários e a comparação entre o resultado esperado e o obtido.
