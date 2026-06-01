# 🧪 PBL – Aula 10: Testes Funcionais Automatizados – LocalEats

**Aluno:** Gabriel  
**Curso:** Análise e Desenvolvimento de Sistemas – Senac Pelotas  
**Disciplina:** Qualidade de Software  
**Sistema:** LocalEats — https://local-eats-unisenac.vercel.app/static/index.html  

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
| 1 | Página inicial carrega com sucesso | Título correto visível no navegador |
| 2 | Lista de restaurantes é exibida | Pelo menos um card de restaurante presente após carregamento dinâmico |
| 3 | Filtro de categoria funciona | Clicar em uma categoria filtra a listagem |

---

## 🔹 2. Teste Automatizado com Codegen

### Comando utilizado

```bash
playwright codegen https://local-eats-unisenac.vercel.app/static/index.html
```

### Código gerado automaticamente pelo Codegen

```python
from playwright.sync_api import Playwright, sync_playwright, expect


def run(playwright: Playwright) -> None:
    browser = playwright.chromium.launch(headless=False)
    context = browser.new_context()
    page = context.new_page()
    page.goto("https://local-eats-unisenac.vercel.app/static/index.html")
    page.locator(".filter-btn").first.click()
    page.locator(".restaurant-card").first.click()
    context.close()
    browser.close()


with sync_playwright() as playwright:
    run(playwright)
```

### Observações iniciais

O site é uma aplicação em **HTML estático com carregamento dinâmico de dados via JavaScript**. O Codegen capturou a interação de forma funcional, mas com algumas limitações:

**O que o Codegen fez bem:**
- Identificou corretamente os seletores de classe (`.filter-btn`, `.restaurant-card`) presentes no HTML real
- Capturou o fluxo de clique na listagem sem dificuldades
- Gerou código funcional como ponto de partida

**O que gerou código desnecessário ou frágil:**
- Não incluiu nenhum `wait` para o carregamento dinâmico dos restaurantes — o JS busca os dados de uma API e popula os cards depois do `DOMContentLoaded`, então sem espera o teste falha intermitentemente
- A estrutura `run(playwright)` com `sync_playwright` não é compatível com Pytest sem adaptação
- Sem nenhuma `assertion` — o Codegen gravou ações, mas não verificações de resultado
- Seletores por classe CSS (`.restaurant-card`) são frágeis se o nome da classe mudar

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

### Instalação

```bash
pip install -r requirements.txt
playwright install chromium
```

### Teste inicial (pós-Codegen, pré-POM)

```python
# tests/test_navegacao_restaurantes.py

from playwright.sync_api import Page, expect

BASE_URL = "https://local-eats-unisenac.vercel.app/static/index.html"


def test_pagina_inicial_carrega(page: Page):
    """Verifica se a página inicial do LocalEats carrega com o título correto."""
    page.goto(BASE_URL)

    expect(page).to_have_title("Local Eats | Descubra a Gastronomia Local")


def test_lista_de_restaurantes_e_exibida(page: Page):
    """Verifica se os cards de restaurante aparecem após o carregamento dinâmico."""
    page.goto(BASE_URL)

    # Aguarda carregamento dinâmico dos cards via JS
    page.wait_for_selector(".restaurant-card")

    cards = page.locator(".restaurant-card")
    expect(cards.first).to_be_visible()


def test_filtro_de_categoria_e_clicavel(page: Page):
    """Verifica se os botões de filtro de categoria estão visíveis e clicáveis."""
    page.goto(BASE_URL)

    filtro = page.locator(".filter-btn").first
    expect(filtro).to_be_visible()
    filtro.click()
```

---

## 🔹 4. Refatoração com Page Object Model (POM)

O POM separa a lógica de interação com a interface dos testes em si. O resultado são testes mais legíveis, fáceis de manter e resilientes a mudanças na interface.

### `pages/restaurantes_page.py`

```python
from playwright.sync_api import Page, Locator

BASE_URL = "https://local-eats-unisenac.vercel.app/static/index.html"


class RestaurantesPage:
    """
    Page Object para o fluxo de navegação de restaurantes do LocalEats.
    Encapsula todos os seletores e ações relacionados à listagem.
    """

    def __init__(self, page: Page):
        self.page = page

    # ── Navegação ──────────────────────────────────────────────────────────

    def acessar(self) -> None:
        """Navega para a página inicial do LocalEats."""
        self.page.goto(BASE_URL)

    # ── Seletores ──────────────────────────────────────────────────────────

    def titulo_pagina(self) -> str:
        """Retorna o título atual da página."""
        return self.page.title()

    def cards_de_restaurante(self) -> Locator:
        """Aguarda e retorna o locator para todos os cards de restaurante."""
        self.page.wait_for_selector(".restaurant-card")
        return self.page.locator(".restaurant-card")

    def primeiro_card(self) -> Locator:
        """Retorna o locator para o primeiro card de restaurante."""
        return self.cards_de_restaurante().first

    def botoes_filtro(self) -> Locator:
        """Retorna o locator para os botões de filtro de categoria."""
        return self.page.locator(".filter-btn")

    def campo_busca(self) -> Locator:
        """Retorna o locator para o campo de busca."""
        return self.page.get_by_placeholder("Buscar")

    # ── Ações ──────────────────────────────────────────────────────────────

    def clicar_filtro(self, indice: int = 0) -> None:
        """Clica no botão de filtro pelo índice."""
        self.botoes_filtro().nth(indice).click()

    def buscar_restaurante(self, termo: str) -> None:
        """Digita um termo no campo de busca."""
        self.campo_busca().fill(termo)
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

    assert restaurantes.titulo_pagina() == "Local Eats | Descubra a Gastronomia Local"


def test_lista_de_restaurantes_exibe_pelo_menos_um_card(page: Page):
    """
    Verifica se a listagem de restaurantes contém pelo menos
    um item visível após o carregamento dinâmico via JavaScript.
    """
    restaurantes = RestaurantesPage(page)
    restaurantes.acessar()

    expect(restaurantes.primeiro_card()).to_be_visible()


def test_botoes_de_filtro_estao_disponiveis(page: Page):
    """
    Verifica se os botões de filtro por categoria estão
    presentes e clicáveis na página inicial.
    """
    restaurantes = RestaurantesPage(page)
    restaurantes.acessar()
    restaurantes.clicar_filtro(0)

    expect(restaurantes.botoes_filtro().first).to_be_visible()
```

### Por que o POM melhora o código?

| Problema no código bruto | Solução com POM |
|---|---|
| Seletores `.restaurant-card` repetidos em vários testes | Centralizados na classe — mudar em um lugar atualiza todos |
| `wait_for_selector` espalhado nos testes | Encapsulado no método `cards_de_restaurante()` |
| Testes misturando "como interagir" com "o que verificar" | Page Objects cuidam do "como"; testes descrevem o "o quê" |
| Difícil de ler o fluxo testado | Testes leem-se como documentação do comportamento esperado |

---

## 🔹 5. Execução dos Testes

### Comando de execução

```bash
# Modo headless (CI/CD)
pytest tests/ -v

# Modo com navegador visível (debug)
pytest tests/ -v --headed
```

### Resultado da execução

```
$ pytest tests/test_navegacao_restaurantes.py -v

======================================================= test session starts ========================================================
platform linux -- Python 3.11.0, pytest-7.4.0 -- /usr/bin/python3
cachedir: .pytest_cache
Using base URL: https://local-eats-unisenac.vercel.app/static/index.html
collected 3 items

tests/test_navegacao_restaurantes.py::test_pagina_inicial_carrega_com_titulo_correto          PASSED [ 33%]
tests/test_navegacao_restaurantes.py::test_lista_de_restaurantes_exibe_pelo_menos_um_card     PASSED [ 66%]
tests/test_navegacao_restaurantes.py::test_botoes_de_filtro_estao_disponiveis                 PASSED [100%]

======================================================== 3 passed in 6.21s =========================================================
```

| Métrica | Resultado |
|---|---|
| Total de testes | 3 |
| ✅ Passaram | 3 |
| ❌ Falharam | 0 |
| Tempo médio de execução | ~6,2 segundos |

---

## 🔹 6. Análise Crítica dos Testes

**O teste quebrou em algum momento? Por quê?**  
Sim — na primeira versão sem o `wait_for_selector(".restaurant-card")`, o teste de listagem falhava intermitentemente com `TimeoutError`. O site carrega os restaurantes via JavaScript de forma assíncrona depois do HTML inicial. O Codegen não percebeu esse comportamento porque gravou a interação depois que o JS já havia terminado no navegador do desenvolvedor. Em um ambiente mais lento ou em CI, isso falha facilmente.

**Quais seletores foram mais difíceis?**  
O campo de busca foi o mais delicado — o placeholder "Buscar" identificado via `get_by_placeholder` é uma boa prática, mas dependente do texto exato do atributo. Se o placeholder mudar para "Pesquisar restaurantes", o seletor quebra. O ideal seria um `data-testid` no elemento.

**O Codegen ajudou ou gerou problemas?**  
Ajudou como ponto de partida para mapear as classes CSS reais do site (`.restaurant-card`, `.filter-btn`). Mas o código bruto não era entregável: sem `assertions`, sem espera pelo carregamento dinâmico e estrutura incompatível com Pytest. O Codegen é uma ferramenta de rascunho, não de entrega.

**O teste é confiável? Por quê?**  
Razoavelmente. O `wait_for_selector` resolve a principal fragilidade de timing. Porém, os seletores baseados em classes CSS (`.restaurant-card`) são frágeis — uma refatoração de CSS ou troca de nome de classe quebra todos os testes sem que haja mudança de comportamento funcional. O uso de `data-testid` nos elementos do HTML resolveria isso definitivamente.

**O que tornaria o teste mais robusto?**  
- Adicionar atributos `data-testid` no HTML do site para seletores estáveis
- Usar `expect(...).to_be_visible(timeout=10000)` explícito para ambientes com rede lenta
- Criar um teste negativo: buscar por um restaurante inexistente e verificar mensagem de "nenhum resultado"
- Separar testes que dependem de dados externos (API de restaurantes) com mocks

**Quais são os riscos de manutenção?**  
- Qualquer renomeação de classe CSS (`.restaurant-card` → `.card-restaurante`) quebra todos os testes
- O conteúdo dinâmico depende de uma API externa — se a API retornar lista vazia, o teste falha sem ser um bug de interface
- Testes E2E são lentos (~6s por teste) — uma suíte grande pode tornar o pipeline de CI lento sem paralelização

---

## 🔹 7. Reflexão no Contexto do LocalEats

**Testes automatizados substituem testes manuais?**  
Não completamente. Testes automatizados são excelentes para regressão — garantir que o que funcionava continua funcionando. Mas testes exploratórios manuais continuam sendo essenciais para descobrir problemas de usabilidade, fluxos não mapeados e comportamentos inesperados que um script não saberia verificar.

**Vale a pena automatizar todos os fluxos?**  
Não. O custo de criação e manutenção de testes E2E é alto. Vale automatizar os fluxos críticos de negócio — navegação de restaurantes, adição ao carrinho e finalização de pedido. Fluxos secundários têm melhor custo-benefício sendo testados manualmente ou cobertos por testes unitários da lógica subjacente.

**Qual tipo de teste deve ser priorizado?**  
A pirâmide de testes continua sendo a referência: a base deve ser de testes unitários (rápidos, baratos, muitos), o meio de testes de integração, e o topo de testes E2E (lentos, caros, poucos, cobrindo os fluxos mais críticos). No LocalEats, as regras de negócio (cálculo de taxa, validação de pedido) ficam nos unitários; os fluxos de usuário ficam no E2E.

**Como isso ajuda no projeto do grupo (LocalEats)?**  
O LocalEats tem fluxos críticos como navegação de restaurantes, adição de itens ao carrinho e finalização de pedido. Automatizar esses fluxos com Playwright garantiria que nenhum deploy quebre a experiência principal do usuário. Qualquer mudança no frontend — redesign de componentes, atualização de rotas ou alteração na API de pedidos — seria detectada automaticamente pelos testes E2E antes de chegar aos usuários.

---

*Documento elaborado para a disciplina de Qualidade de Software – Senac Pelotas, 2026.*
