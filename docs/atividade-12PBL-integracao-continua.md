# Integração Contínua, Qualidade Automatizada, Métricas e Gestão de Defeitos – LocalEats

**Equipe:** Tech Quality

**Integrante:** Gabriel Piske

**Sistema:** Local Eats (https://local-eats-unisenac.vercel.app/static/index.html)

---

# 1. Repositório da Atividade

| Item | Descrição |
|------|-----------|
| Nome do repositório | localeats-integracao-continua |
| Link do repositório | https://github.com/GabriePiske/localeats-integracao-continua |

### Estrutura de diretórios

```text
localeats-integracao-continua/
│
├── .github/
│   └── workflows/
│       └── quality.yml
│
├── tests/
│   └── test_order.py
│
├── app.py
├── requirements.txt
└── README.md
```

---

# 2. Planejamento da Funcionalidade

| Item | Descrição |
|------|-----------|
| Título da Issue | Implement calculate_total function |
| Objetivo da funcionalidade | Implementar uma função responsável por calcular o valor total de um pedido com base no preço e na quantidade. |
| Link da Issue | https://github.com/GabriePiske/localeats-integracao-continua/issues/1 |

---

# 3. Teste Automatizado

| Item | Descrição |
|------|-----------|
| Tipo de teste | Unitário |
| Objetivo do teste | Verificar se a função `calculate_total` retorna corretamente o valor total de um pedido. |
| Link para o arquivo do teste | https://github.com/GabriePiske/localeats-integracao-continua/blob/main/tests/test_order.py |

### Código do teste

```python
import sys
import os

sys.path.append(os.path.dirname(os.path.dirname(os.path.abspath(__file__))))

from app import calculate_total


def test_calculate_total():
    assert calculate_total(20, 3) == 60
```

---

# 4. Pipeline de Integração Contínua

| Item | Descrição |
|------|-----------|
| Nome do workflow | Quality Pipeline |
| Evento que dispara a execução | Push e Pull Request para a branch `main` |
| Link para o arquivo do workflow | https://github.com/GabriePiske/localeats-integracao-continua/blob/main/.github/workflows/quality.yml |
| Link de uma execução do workflow | https://github.com/GabriePiske/localeats-integracao-continua/actions |

### Código do workflow

```yaml
name: Quality Pipeline

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  tests:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Run tests
        run: python -m pytest
```

---

# 5. Indicadores de Qualidade

| Indicador | Valor |
|-----------|-------|
| Quantidade de testes executados | 1 |
| Quantidade de testes aprovados | 1 |
| Quantidade de testes com falha | 0 |
| Status final do pipeline | Sucesso |

---

# 6. Registro de Defeito

| Item | Descrição |
|------|-----------|
| Título do defeito | Import error in automated test |
| Severidade | Média |
| Link da Issue | https://github.com/GabriePiske/localeats-integracao-continua/issues/2 |

### Descrição

Durante a execução do pipeline de Integração Contínua, o teste automatizado não conseguiu localizar o módulo `app`, ocasionando falha na execução. O problema foi identificado pelo GitHub Actions e corrigido ajustando o caminho de importação no arquivo `test_order.py`.

---

# Conclusão

A atividade permitiu aplicar um fluxo completo de qualidade utilizando GitHub, testes automatizados e Integração Contínua. Foi possível criar Issues para gerenciamento de funcionalidades e defeitos, implementar testes unitários, configurar um pipeline no GitHub Actions e acompanhar indicadores de qualidade. A utilização dessas práticas contribui para reduzir falhas, automatizar verificações e aumentar a confiabilidade do processo de desenvolvimento do projeto LocalEats.
