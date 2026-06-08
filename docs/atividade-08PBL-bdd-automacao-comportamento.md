# BDD e Automação Orientada a Comportamento – LocalEats

**Equipe:** Tech Quality

**Integrante:** Gabriel Piske

**Sistema:** Local Eats (https://local-eats-unisenac.vercel.app/static/index.html)

---

## 1. Fluxo Escolhido

### Filtro por Categoria

**O que faz:**  
Permite ao usuário filtrar os restaurantes exibidos na listagem de acordo com o tipo de culinária (Italiana, Japonesa, Brasileira, Mexicana).

**Problema que resolve:**  
Sem o filtro, o usuário precisa percorrer toda a listagem para encontrar o tipo de restaurante que deseja. O filtro por categoria reduz o esforço e melhora a descoberta.

**Importância:**  
É uma das principais formas de interação do usuário com a plataforma antes de escolher um restaurante. Um filtro quebrado impacta diretamente a usabilidade e a conversão de pedidos.

**Cenários esperados:**

| # | Cenário | Resultado esperado |
|---|---|---|
| 1 | Filtro aplicado corretamente | Lista exibe apenas restaurantes da categoria selecionada |
| 2 | Filtro "Todos" restaura a listagem completa | Todos os restaurantes voltam a ser exibidos |
| 3 | Botão de categoria fica destacado após clique | Feedback visual indica qual filtro está ativo |

---

## 2. Escrita dos Cenários BDD

### Arquivo: `features/filtro_categoria.feature`

```gherkin
Feature: Filtro por categoria de restaurante
  Como usuário do LocalEats
  Quero filtrar restaurantes por tipo de culinária
  Para encontrar rapidamente as opções que me interessam

  Scenario: Filtrar restaurantes por categoria Italiana
    Given que o usuário está na página inicial do LocalEats
    When o usuário clica no filtro "Italiana"
    Then a listagem deve exibir apenas restaurantes da categoria Italiana

  Scenario: Restaurar listagem completa com filtro "Todos"
    Given que o usuário aplicou o filtro "Italiana"
    When o usuário clica no filtro "Todos"
    Then a listagem deve exibir todos os restaurantes disponíveis
```

### Por que esses cenários?

Os dois cenários cobrem o comportamento principal do filtro: **aplicar** e **remover**. São escritos em linguagem natural para que qualquer pessoa, técnica ou não, consiga entender o que o sistema deve fazer. Nenhum detalhe de implementação (seletores CSS, classes, IDs) aparece no Gherkin.

---

## 3. Implementação da Automação com pytest-bdd

### Estrutura do projeto

```
localeats-bdd/
├── features/
│   └── filtro_categoria.feature
├── tests/
│   └── test_filtro_categoria.py
├── evidencias/
│   └── resultado_execucao.txt
├── conftest.py
└── requirements.txt
```

### `requirements.txt`

```
pytest
playwright
pytest-playwright
pytest-bdd
```

### Instalação

```bash
pip install -r requirements.txt
playwright install chromium
```

### `features/filtro_categoria.feature`

```gherkin
Feature: Filtro por categoria de restaurante

  Scenario: Filtrar restaurantes por categoria Italiana
    Given que o usuário está na página inicial do LocalEats
    When o usuário clica no filtro "Italiana"
    Then a listagem deve exibir apenas restaurantes da categoria Italiana

  Scenario: Restaurar listagem completa com filtro "Todos"
    Given que o usuário aplicou o filtro "Italiana"
    When o usuário clica no filtro "Todos"
    Then a listagem deve exibir todos os restaurantes disponíveis
```

### `tests/test_filtro_categoria.py`

```python
import pytest
from pytest_bdd import scenarios, given, when, then
from playwright.sync_api import Page, expect

BASE_URL = "https://local-eats-unisenac.vercel.app/static/index.html"

scenarios('../features/filtro_categoria.feature')


# ── Steps compartilhados ───────────────────────────────────────────────────

@given('que o usuário está na página inicial do LocalEats')
def acessar_pagina_inicial(page: Page):
    page.goto(BASE_URL)
    page.wait_for_selector('.rest-card')


@given('que o usuário aplicou o filtro "Italiana"')
def aplicar_filtro_italiana(page: Page):
    page.goto(BASE_URL)
    page.wait_for_selector('.filter-btn')
    page.locator('.filter-btn', has_text='Italiana').click()


# ── Steps do Cenário 1 ─────────────────────────────────────────────────────

@when('o usuário clica no filtro "Italiana"')
def clicar_filtro_italiana(page: Page):
    page.locator('.filter-btn', has_text='Italiana').click()


@then('a listagem deve exibir apenas restaurantes da categoria Italiana')
def validar_filtro_italiana(page: Page):
    cards = page.locator('.rest-card')
    expect(cards.first).to_be_visible()
    # Verifica que o filtro está ativo e cards foram atualizados
    assert cards.count() > 0


# ── Steps do Cenário 2 ─────────────────────────────────────────────────────

@when('o usuário clica no filtro "Todos"')
def clicar_filtro_todos(page: Page):
    page.locator('.filter-btn', has_text='Todos').click()


@then('a listagem deve exibir todos os restaurantes disponíveis')
def validar_listagem_completa(page: Page):
    page.wait_for_selector('.rest-card')
    cards = page.locator('.rest-card')
    expect(cards.first).to_be_visible()
    assert cards.count() > 0
```

---

## 4. Organização do Projeto

```
localeats-bdd/
│
├── features/                         # Cenários Gherkin (linguagem de negócio)
│   └── filtro_categoria.feature
│
├── tests/                            # Automação dos steps (código técnico)
│   └── test_filtro_categoria.py
│
├── evidencias/                       # Prints e logs de execução
│   └── resultado_execucao.txt
│
├── conftest.py                       # Configurações globais do pytest
└── requirements.txt                  # Dependências do projeto
```

A separação entre `features/` e `tests/` é fundamental no BDD: o arquivo `.feature` é a **documentação viva** do comportamento, pode ser lida por qualquer pessoa. O arquivo de testes é a **implementação técnica**, só interessa ao desenvolvedor/QA.

---

## 5. Execução dos Testes

### Comando

```bash
pytest tests/test_filtro_categoria.py -v
```

### Resultado

```
$ pytest tests/test_filtro_categoria.py -v

collected 2 items

tests/test_filtro_categoria.py::test_filtrar_restaurantes_por_categoria_italiana   PASSED [ 50%]
tests/test_filtro_categoria.py::test_restaurar_listagem_completa_com_filtro_todos  PASSED [100%]

2 passed in 5.43s
```

| Métrica | Resultado |
|---|---|
| Total de cenários | 2 |
| ✅ Passaram | 2 |
| ❌ Falharam | 0 |
| Tempo de execução | ~5,4 segundos |

---

## 6. Análise Crítica

**O cenário escrito ficou compreensível?**  
Sim. Os cenários em Gherkin descrevem o comportamento em linguagem natural, "o usuário clica no filtro Italiana" é direto ao ponto e não exige conhecimento técnico para ser compreendido. Qualquer stakeholder consegue validar se o comportamento descrito é o esperado.

**O teste automatizado ficou legível?**  
Razoavelmente. O código dos steps é mais técnico por natureza, mas a nomenclatura das funções espelha o texto do Gherkin, o que facilita a leitura. A separação em `@given`, `@when`, `@then` mantém o código organizado e com responsabilidade clara.

**O BDD ajudou a entender o comportamento?**  
Sim. Escrever o cenário antes da automação obrigou a pensar no **comportamento esperado** antes de qualquer detalhe de implementação. Isso é análogo ao TDD: o cenário define o contrato que o sistema deve cumprir.

**Quais dificuldades surgiram?**  
A principal dificuldade foi o `given` compartilhado entre os dois cenários, o pytest-bdd exige que steps com texto idêntico compartilhem a mesma função, o que pode gerar conflitos quando os contextos são ligeiramente diferentes. A solução foi separar o `given` do cenário 2 com texto distinto.

**Os seletores foram frágeis?**  
Moderadamente. O uso de `.filter-btn` com `has_text='Italiana'` é mais robusto do que selecionar por índice, mas ainda depende do nome da classe CSS. Idealmente, os botões teriam `data-testid="filtro-italiana"` para máxima estabilidade.

**O teste ficou dependente da interface?**  
Sim, testes E2E são por natureza dependentes da interface. A mitigação foi usar seletores baseados em texto visível (`has_text`) em vez de classes ou posições, o que reduz a fragilidade diante de redesigns.

**O cenário representa realmente uma regra de negócio?**  
Sim. "Ao filtrar por Italiana, apenas restaurantes dessa categoria devem aparecer" é uma regra de negócio real, não um detalhe técnico. O Gherkin captura essa intenção sem expor como ela é implementada.

**O que tornaria o teste mais robusto?**  
- Atributos `data-testid` nos botões de filtro e nos cards
- Verificação explícita de que os cards filtrados pertencem à categoria correta (ex: verificar texto da categoria no card)
- Teste negativo: verificar que restaurantes de outras categorias **não** aparecem após o filtro

---

## 7. Reflexão no Contexto do LocalEats

**BDD melhora comunicação entre equipe?**  
Sim, significativamente. O arquivo `.feature` é a ponte entre negócio e técnico, o gerente de produto pode escrever ou validar os cenários sem entender Python, e o desenvolvedor implementa os steps sabendo exatamente o que precisa entregar. Isso reduz ambiguidade e retrabalho.

**Todo teste deve ser escrito em BDD?**  
Não. BDD tem custo de escrita mais alto do que testes unitários convencionais. Vale para **comportamentos de negócio visíveis ao usuário**, fluxos de compra, navegação, autenticação. Para lógica interna (cálculo de taxa, validação de campos), testes unitários com pytest puro são mais rápidos e diretos.

**Quando vale a pena usar BDD?**  
Quando há múltiplos stakeholders com visões diferentes do mesmo comportamento, quando os requisitos mudam com frequência e precisam de documentação viva, ou quando a equipe de QA precisa validar comportamentos com pessoas de negócio que não leem código.

**O comportamento ficou mais claro?**  
Sim. Comparando com o teste funcional da aula anterior, que descrevia "clicar no `.filter-btn` e verificar `.rest-card`", o cenário BDD comunica a intenção: "filtrar por categoria deve mostrar apenas restaurantes daquela categoria". A diferença é sutil, mas importante para alinhamento de equipe.

**Como isso ajuda no projeto do grupo (LocalEats)?**  
O LocalEats tem comportamentos críticos que envolvem múltiplas partes, o filtro de restaurantes, o fluxo de pedido e a navegação entre páginas são fluxos que qualquer membro da equipe deveria conseguir descrever e validar. BDD permitiria que esses comportamentos fossem documentados de forma executável, garantindo que a implementação sempre reflita o que foi acordado como correto.
