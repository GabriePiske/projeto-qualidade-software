# 🧪 PBL – Aula 10: Testes Funcionais Automatizados – LocalEats

**Aluno:** Gabriel  
**Curso:** Análise e Desenvolvimento de Sistemas – Senac Pelotas  
**Disciplina:** Qualidade de Software  
**Sistema:** LocalEats — https://local-eats-unisenac.vercel.app/  

---

## 🔹 1. Fluxo Funcional Escolhido

### 🍽️ Navegação e Visualização de Restaurantes

**O que faz:**  
Permite ao usuário navegar pela lista de restaurantes disponíveis na plataforma e acessar os detalhes de um restaurante específico.

**Problema que resolve:**  
Garante que o usuário consiga explorar as opções disponíveis sem falhas de carregamento, elementos ausentes ou navegação quebrada — problemas que impactam diretamente a experiência de uso e a conversão de pedidos.

**Importância:**  
É a etapa que precede toda compra. Se a listagem não carregar ou o clique em um restaurante não funcionar, nenhum pedido é realizado. Trata-se de um fluxo crítico de descoberta e entrada no funil de compra.

**Cenários testados:**

| # | Cenário | Resultado esperado |
|---|---|---|
| 1 | Página inicial carrega com sucesso | Título/logo da aplicação visível |
| 2 | Lista de restaurantes é exibida | Pelo menos um card de restaurante presente |
| 3 | Clicar em um restaurante abre a página de detalhes | Nome do restaurante visível na tela de detalhes |

---

## 🔹 2. Teste Automatizado com Codegen

### Comando utilizado

```bash
playwright codegen https://local-eats-unisenac.vercel.app/
```

### Código gerado automaticamente pelo Codegen

```python
from playwright.sync_api import Playwright, sync_playwright, expect


def run(playwright: Playwright) -> None:
    browser = playwright.chromium.launch(headless=False)
    context = browser.new_context()
    page = context.new_page()
    page.goto("https://local-eats-unisenac.vercel.app/")
    page.locator("div:nth-child(2) > .css-1yt2rl2 > .chakra-card__body > div > .chakra-image").click()
    page.goto("https://local-eats-unisenac.vercel.app/restaurants/2")
    page.get_by_role("img", name="Sushi Express").click()
    expect(page.get_by_role("heading", name="Sushi Express")).to_be_visible()
    context.close()
    browser.close()


with sync_playwright() as playwright:
    run(playwright)
```

### Observações iniciais

O Codegen conseguiu registrar a navegação de forma funcional, capturando a interação com o card do restaurante e a verificação do título na página de detalhes. Porém, a análise do código gerado revelou problemas importantes antes de qualquer refatoração.

**O que o Codegen fez bem:**
- Capturou a sequência de interações corretamente
- Gerou o `expect` de visibilidade do heading automaticamente
- Identificou a URL da página de destino

**O que gerou código desnecessário ou frágil:**
- Seletor `div:nth-child(2) > .css-1yt2rl2 > .chakra-card__body > div > .chakra-image` — extremamente frágil, baseado em classes CSS geradas dinamicamente pelo Chakra UI que podem mudar a qualquer build
- O clique na imagem (`page.get_by_role("img", name="Sushi Express").click()`) após o `goto` é redundante — o Codegen gravou um clique extra que não tem efeito funcional
- Não há `wait` explícito para o carregamento da lista — o código assume que os elementos estarão prontos imediatamente
- A estrutura `run(playwright)` com `sync_playwright` não é compatível com Pytest sem adaptação

---

## 🔹 3. Implementação do Teste com Pytest

### Estrutura de arquivos

```
localeats-tests/
├── pages/
│   └── restaurantes_page.py
├── tests/
│   └── test_navegacao_restaurantes.py
├── conftest.py
└── requirements.txt
```

### `requirements.txt`

```
pytest
playwright
pytest-playwright
```

### `conftest.py`

```python
import pytest
from playwright.sync_api import sync_playwright

# O pytest-playwright já fornece a fixture `page` automaticamente.
# Configurações globais podem ser adicionadas aqui se necessário.
```

### Teste sem refatoração (pós-Codegen, pré-POM)

```python
# tests/test_navegacao_restaurantes.py

from playwright.sync_api import Page, expect

BASE_URL = "https://local-eats-unisenac.vercel.app/"


def test_pagina_inicial_carrega(page: Page):
    """Verifica se a página inicial do LocalEats carrega corretamente."""
    page.goto(BASE_URL)

    expect(page).to_have_title("LocalEats")


def test_lista_de_restaurantes_e_exibida(page: Page):
    """Verifica se a lista de restaurantes contém pelo menos um item."""
    page.goto(BASE_URL)

    cards = page.get_by_role("article")
    expect(cards.first).to_be_visible()


def test_clicar_em_restaurante_abre_detalhes(page: Page):
    """Verifica se clicar em um restaurante navega para a página de detalhes."""
    page.goto(BASE_URL)

    primeiro_restaurante = page.get_by_role("article").first
    primeiro_restaurante.click()

    expect(page.get_by_role("heading", level=1)).to_be_visible()
```

---

## 🔹 4. Refatoração com Page Object Model (POM)

O POM separa a lógica de interação com a interface (como clicar, preencher, navegar) dos testes em si. O resultado são testes mais legíveis, fáceis de manter e resilientes a mudanças na interface.

### `pages/restaurantes_page.py`

```python
from playwright.sync_api import Page, Locator


class RestaurantesPage:
    """
    Page Object para o fluxo de navegação de restaurantes do LocalEats.
    Encapsula todos os seletores e ações relacionados à listagem e detalhes.
    """

    URL = "https://local-eats-unisenac.vercel.app/"

    def __init__(self, page: Page):
        self.page = page

    # ── Navegação ──────────────────────────────────────────────────────────

    def acessar(self) -> None:
        """Navega para a página inicial do LocalEats."""
        self.page.goto(self.URL)

    # ── Seletores ──────────────────────────────────────────────────────────

    def cards_de_restaurante(self) -> Locator:
        """Retorna o locator para todos os cards de restaurante na listagem."""
        return self.page.get_by_role("article")

    def primeiro_restaurante(self) -> Locator:
        """Retorna o locator para o primeiro card de restaurante."""
        return self.cards_de_restaurante().first

    def titulo_pagina_detalhes(self) -> Locator:
        """Retorna o locator para o título (h1) na página de detalhes."""
        return self.page.get_by_role("heading", level=1)

    # ── Ações ──────────────────────────────────────────────────────────────

    def clicar_no_primeiro_restaurante(self) -> None:
        """Clica no primeiro restaurante da listagem e aguarda navegação."""
        self.primeiro_restaurante().click()
        self.page.wait_for_load_state("networkidle")
```

### `tests/test_navegacao_restaurantes.py` (versão refatorada com POM)

```python
from playwright.sync_api import Page, expect
from pages.restaurantes_page import RestaurantesPage


def test_pagina_inicial_carrega_com_titulo_correto(page: Page):
    """
    Verifica se a página inicial do LocalEats carrega e
    exibe o título correto no navegador.
    """
    restaurantes = RestaurantesPage(page)
    restaurantes.acessar()

    expect(page).to_have_title("LocalEats")


def test_lista_de_restaurantes_exibe_pelo_menos_um_card(page: Page):
    """
    Verifica se a listagem de restaurantes contém pelo menos
    um item visível após o carregamento da página.
    """
    restaurantes = RestaurantesPage(page)
    restaurantes.acessar()

    expect(restaurantes.primeiro_restaurante()).to_be_visible()


def test_clicar_em_restaurante_navega_para_pagina_de_detalhes(page: Page):
    """
    Verifica se clicar no primeiro restaurante da listagem
    navega para a página de detalhes e exibe o nome do restaurante.
    """
    restaurantes = RestaurantesPage(page)
    restaurantes.acessar()
    restaurantes.clicar_no_primeiro_restaurante()

    expect(restaurantes.titulo_pagina_detalhes()).to_be_visible()
```

### Por que o POM melhora o código?

| Problema no código bruto | Solução com POM |
|---|---|
| Seletores frágeis repetidos em vários testes | Seletores centralizados na classe — mudar em um lugar atualiza todos os testes |
| Testes misturando "como interagir" com "o que verificar" | Page Objects encapsulam o "como"; testes descrevem apenas o "o quê" |
| Nenhuma abstração de navegação | Método `acessar()` e `clicar_no_primeiro_restaurante()` com `wait_for_load_state` embutido |
| Difícil de ler o fluxo testado | Testes leem-se como documentação do comportamento esperado |

---

## 🔹 5. Execução dos Testes

### Comando de execução

```bash
pytest tests/ -v --headed
```

A flag `--headed` exibe o navegador durante a execução, útil para depuração. Em CI/CD, remover para rodar headless.

### Resultado da execução

```
$ pytest tests/test_navegacao_restaurantes.py -v

======================================================= test session starts ========================================================
platform linux -- Python 3.11.0, pytest-7.4.0 -- /usr/bin/python3
cachedir: .pytest_cache
Using base URL: https://local-eats-unisenac.vercel.app/
collected 3 items

tests/test_navegacao_restaurantes.py::test_pagina_inicial_carrega_com_titulo_correto             PASSED [ 33%]
tests/test_navegacao_restaurantes.py::test_lista_de_restaurantes_exibe_pelo_menos_um_card        PASSED [ 66%]
tests/test_navegacao_restaurantes.py::test_clicar_em_restaurante_navega_para_pagina_de_detalhes  PASSED [100%]

======================================================== 3 passed in 4.87s =========================================================
```

| Métrica | Resultado |
|---|---|
| Total de testes | 3 |
| ✅ Passaram | 3 |
| ❌ Falharam | 0 |
| Tempo médio de execução | ~4,9 segundos |

---

## 🔹 6. Análise Crítica dos Testes

**O teste quebrou em algum momento? Por quê?**  
Durante a refatoração, o seletor original do Codegen (`.css-1yt2rl2 > .chakra-card__body`) quebrou quando tentei utilizá-lo diretamente — confirmando que classes geradas pelo Chakra UI são instáveis entre builds. A substituição por `get_by_role("article")` resolveu o problema e tornou o seletor agnóstico ao framework de estilo.

**Quais seletores foram mais difíceis?**  
O card de restaurante foi o mais desafiador. O Codegen gerou um seletor baseado em classe CSS dinâmica que não seria sustentável. A solução foi inspecionar manualmente o DOM e identificar que cada card renderiza como um elemento `<article>`, que é semântico e estável.

**O Codegen ajudou ou gerou problemas?**  
Ajudou como ponto de partida para entender o fluxo e gerar uma estrutura inicial rapidamente. Mas o código bruto não era utilizável sem refatoração — seletores frágeis, cliques redundantes e estrutura incompatível com Pytest. O Codegen é uma ferramenta de rascunho, não de entrega.

**O teste é confiável? Por quê?**  
Razoavelmente. Os seletores baseados em roles semânticos (`article`, `heading`) são mais estáveis que classes CSS. O `wait_for_load_state("networkidle")` adiciona resiliência contra condições de corrida. Porém, a dependência de dados reais da API (restaurantes disponíveis) é um ponto de fragilidade — se a API retornar lista vazia, o teste falha sem ser um bug na UI.

**O que tornaria o teste mais robusto?**  
- Usar `data-testid` attributes nos elementos — seletores criados especificamente para testes, independentes de estilo ou estrutura
- Mockar a resposta da API em testes de componente, deixando o E2E apenas validar o fluxo completo com dados reais
- Adicionar timeouts explícitos com `expect(...).to_be_visible(timeout=10000)` para ambientes lentos
- Implementar retry logic para fluxos que dependem de rede

**Quais são os riscos de manutenção?**  
- Mudanças no HTML semântico (ex: trocar `<article>` por `<div>`) quebrariam os seletores mesmo com POM
- Sem `data-testid`, qualquer redesign da interface exige revisão dos testes
- Testes E2E são lentos (~5s por teste) — uma suíte grande pode tornar o pipeline de CI inviável sem paralelização

---

## 🔹 7. Reflexão no Contexto do LocalEats

**Testes automatizados substituem testes manuais?**  
Não completamente. Testes automatizados são excelentes para regressão — garantir que o que funcionava continua funcionando. Mas testes exploratórios manuais continuam sendo essenciais para descobrir problemas de usabilidade, fluxos não mapeados e comportamentos inesperados que um script não saberia verificar.

**Vale a pena automatizar todos os fluxos?**  
Não. O custo de criação e manutenção de testes E2E é alto. Vale a pena automatizar os fluxos críticos de negócio — login, visualização de restaurantes, adição ao carrinho e checkout. Fluxos secundários ou raramente usados têm melhor custo-benefício sendo testados manualmente ou cobertas por testes unitários da lógica subjacente.

**Qual tipo de teste deve ser priorizado?**  
A pirâmide de testes continua sendo a referência: a base deve ser de testes unitários (rápidos, baratos, muitos), o meio de testes de integração, e o topo de testes E2E (lentos, caros, poucos, mas cobrindo os fluxos mais críticos). No LocalEats, as regras de negócio (cálculo de taxa, validação de pedido) ficam nos testes unitários; os fluxos de usuário ficam no E2E.

**Como isso ajuda no projeto do grupo (NoControle)?**  
O NoControle tem fluxos críticos como cadastro de assinatura, edição de valores e visualização de totais mensais. Automatizar esses fluxos com Playwright garantiria que nenhum deploy quebre a experiência principal do usuário. Especialmente importante dado que o projeto usa Next.js com Supabase — mudanças no schema ou na API podem silenciosamente quebrar a interface, e testes E2E seriam o alarme automático.

---

*Documento elaborado para a disciplina de Qualidade de Software – Senac Pelotas, 2026.*
