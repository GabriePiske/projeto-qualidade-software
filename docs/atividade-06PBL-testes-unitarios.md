# Testes Unitários Automatizados e TDD – LocalEats

**Aluno:** Gabriel  
**Curso:** Análise e Desenvolvimento de Sistemas – Senac Pelotas  
**Disciplina:** Qualidade de Software  
**Sistema:** LocalEats  

---

## 🔹 1. Funcionalidade Escolhida

### Cálculo de Taxa de Entrega

**O que faz:**  
Calcula o valor da taxa de entrega com base na distância em quilômetros informada pelo sistema.

**Problema que resolve:**  
Padroniza a cobrança de entrega, evitando cobranças inconsistentes e garantindo que regras de negócio sejam aplicadas de forma automática e confiável.

**Importância:**  
A taxa de entrega impacta diretamente o custo final do pedido para o cliente e a receita do restaurante. Erros nessa lógica podem gerar insatisfação, disputas e prejuízo financeiro.

**Regras de negócio:**

| Condição | Comportamento |
|---|---|
| Distância ≤ 0 km | Erro: distância inválida |
| Distância até 3 km | Taxa fixa de R$ 5,00 |
| Distância acima de 3 km | R$ 5,00 + R$ 2,00 por km excedente |

**Implementação base (Python):**

```python
def calcular_taxa_entrega(distancia_km: float) -> float:
    """
    Calcula a taxa de entrega com base na distância em km.
    
    - Distância <= 0: inválida (levanta ValueError)
    - Distância até 3 km: taxa fixa de R$ 5,00
    - Acima de 3 km: R$ 5,00 + R$ 2,00 por km excedente
    """
    if distancia_km <= 0:
        raise ValueError("Distância deve ser maior que zero.")
    
    TAXA_FIXA = 5.00
    TAXA_POR_KM_EXCEDENTE = 2.00
    LIMITE_TAXA_FIXA = 3.0

    if distancia_km <= LIMITE_TAXA_FIXA:
        return TAXA_FIXA
    
    km_excedentes = distancia_km - LIMITE_TAXA_FIXA
    return TAXA_FIXA + (km_excedentes * TAXA_POR_KM_EXCEDENTE)
```

---

## 🔹 2. Testes Unitários

### Configuração do ambiente

```bash
pip install pytest
```

**Arquivo:** `test_taxa_entrega.py`

---

### Teste 1 — Distância dentro da faixa de taxa fixa

**Nome descritivo:** `test_deve_retornar_taxa_fixa_para_distancia_ate_3km`

**Cenário testado:**  
Valida que pedidos dentro do raio de 3 km pagam exatamente R$ 5,00, independentemente do valor exato da distância dentro desse intervalo.

**Dados de entrada:** `distancia_km = 2.0`  
**Resultado esperado:** `5.00`

```python
def test_deve_retornar_taxa_fixa_para_distancia_ate_3km():
    # Arrange
    distancia_km = 2.0

    # Act
    resultado = calcular_taxa_entrega(distancia_km)

    # Assert
    assert resultado == 5.00
```

---

### Teste 2 — Distância exatamente no limite da taxa fixa (valor de contorno)

**Nome descritivo:** `test_deve_retornar_taxa_fixa_para_distancia_exatamente_3km`

**Cenário testado:**  
Valida o comportamento exato no limite de 3 km — valor de contorno crítico onde a regra de taxa fixa ainda deve ser aplicada.

**Dados de entrada:** `distancia_km = 3.0`  
**Resultado esperado:** `5.00`

```python
def test_deve_retornar_taxa_fixa_para_distancia_exatamente_3km():
    # Arrange
    distancia_km = 3.0

    # Act
    resultado = calcular_taxa_entrega(distancia_km)

    # Assert
    assert resultado == 5.00
```

---

### Teste 3 — Distância acima de 3 km com taxa proporcional

**Nome descritivo:** `test_deve_calcular_taxa_proporcional_para_distancia_acima_de_3km`

**Cenário testado:**  
Valida que para distâncias acima de 3 km, o sistema aplica corretamente a taxa fixa + valor proporcional aos quilômetros excedentes.

**Dados de entrada:** `distancia_km = 5.0`  
**Resultado esperado:** `9.00` (R$5,00 fixo + 2 km × R$2,00)

```python
def test_deve_calcular_taxa_proporcional_para_distancia_acima_de_3km():
    # Arrange
    distancia_km = 5.0  # 2 km excedentes

    # Act
    resultado = calcular_taxa_entrega(distancia_km)

    # Assert
    assert resultado == 9.00  # 5.00 + (2 * 2.00)
```

---

### Teste 4 — Distância zero (cenário de erro/borda)

**Nome descritivo:** `test_deve_lancar_erro_para_distancia_zero`

**Cenário testado:**  
Valida que o sistema rejeita corretamente uma distância igual a zero, que representa um estado inválido no contexto de entrega.

**Dados de entrada:** `distancia_km = 0`  
**Resultado esperado:** `ValueError` com mensagem de distância inválida

```python
import pytest

def test_deve_lancar_erro_para_distancia_zero():
    # Arrange
    distancia_km = 0

    # Act & Assert
    with pytest.raises(ValueError, match="Distância deve ser maior que zero."):
        calcular_taxa_entrega(distancia_km)
```

---

### Teste 5 — Distância negativa (cenário de erro/borda)

**Nome descritivo:** `test_deve_lancar_erro_para_distancia_negativa`

**Cenário testado:**  
Valida que o sistema rejeita distâncias negativas, que seriam logicamente impossíveis e indicam erro de entrada de dados.

**Dados de entrada:** `distancia_km = -5.0`  
**Resultado esperado:** `ValueError`

```python
def test_deve_lancar_erro_para_distancia_negativa():
    # Arrange
    distancia_km = -5.0

    # Act & Assert
    with pytest.raises(ValueError, match="Distância deve ser maior que zero."):
        calcular_taxa_entrega(distancia_km)
```

---

## 🔹 3. Aplicação do TDD

### Funcionalidade aplicada: Cálculo de Taxa de Entrega

O ciclo TDD foi aplicado de forma completa na função `calcular_taxa_entrega`.

---

### 🔴 Red — Escrever o teste antes da implementação

Antes de escrever qualquer código de produção, o teste foi criado:

```python
# test_taxa_entrega.py — escrito ANTES da implementação

def test_deve_retornar_taxa_fixa_para_distancia_ate_3km():
    distancia_km = 2.0
    resultado = calcular_taxa_entrega(distancia_km)
    assert resultado == 5.00
```

**Resultado esperado da execução:** ❌ FALHA — `NameError: name 'calcular_taxa_entrega' is not defined`

```
FAILED test_taxa_entrega.py::test_deve_retornar_taxa_fixa_para_distancia_ate_3km
NameError: name 'calcular_taxa_entrega' is not defined
```

A falha confirma que o teste está correto e aguarda implementação — esse é o comportamento esperado no Red.

---

### 🟢 Green — Implementação mínima para passar no teste

```python
# taxa_entrega.py — implementação mínima (sem refatoração)

def calcular_taxa_entrega(distancia_km):
    if distancia_km <= 0:
        raise ValueError("Distância deve ser maior que zero.")
    if distancia_km <= 3:
        return 5.00
    km_excedentes = distancia_km - 3
    return 5.00 + (km_excedentes * 2.00)
```

**Resultado após implementação:** PASSOU

```
PASSED test_taxa_entrega.py::test_deve_retornar_taxa_fixa_para_distancia_ate_3km
```

---

### 🔵 Refactor — Melhorar sem quebrar os testes

Após todos os testes passarem, o código foi refatorado para maior clareza e manutenibilidade:

```python
# taxa_entrega.py — após refatoração

def calcular_taxa_entrega(distancia_km: float) -> float:
    """
    Calcula a taxa de entrega com base na distância em km.

    Regras:
      - Distância <= 0: inválida (ValueError)
      - Até 3 km: taxa fixa de R$ 5,00
      - Acima de 3 km: R$ 5,00 + R$ 2,00/km excedente
    """
    _validar_distancia(distancia_km)

    TAXA_FIXA = 5.00
    LIMITE_TAXA_FIXA = 3.0
    TAXA_POR_KM_EXCEDENTE = 2.00

    if distancia_km <= LIMITE_TAXA_FIXA:
        return TAXA_FIXA

    km_excedentes = distancia_km - LIMITE_TAXA_FIXA
    return TAXA_FIXA + (km_excedentes * TAXA_POR_KM_EXCEDENTE)


def _validar_distancia(distancia_km: float) -> None:
    if distancia_km <= 0:
        raise ValueError("Distância deve ser maior que zero.")
```

**Resultado após refatoração:** Todos os testes continuam passando.

---

## 🔹 4. Refatoração

### Melhorias realizadas

| Aspecto | Antes | Depois |
|---|---|---|
| **Nomes** | Literais `5.00`, `3`, `2.00` espalhados no código | Constantes nomeadas: `TAXA_FIXA`, `LIMITE_TAXA_FIXA`, `TAXA_POR_KM_EXCEDENTE` |
| **Organização** | Validação misturada com lógica de negócio | Validação extraída para função `_validar_distancia()` |
| **Legibilidade** | Sem docstring nem type hints | Docstring completa + anotações de tipo (`float -> float`) |
| **Duplicação** | Nenhuma — já estava simples | Mantida simplicidade, sem código repetido |

### Justificativa

Extrair a validação para `_validar_distancia()` segue o princípio de responsabilidade única (SRP): a função principal cuida apenas do cálculo, e a validação tem seu próprio escopo. As constantes nomeadas tornam o código autodocumentado — qualquer manutenção futura (ex: alterar a taxa por km) é feita em um único lugar.

---

## 🔹 5. Execução dos Testes

### Arquivo completo: `test_taxa_entrega.py`

```python
import pytest
from taxa_entrega import calcular_taxa_entrega


def test_deve_retornar_taxa_fixa_para_distancia_ate_3km():
    assert calcular_taxa_entrega(2.0) == 5.00


def test_deve_retornar_taxa_fixa_para_distancia_exatamente_3km():
    assert calcular_taxa_entrega(3.0) == 5.00


def test_deve_calcular_taxa_proporcional_para_distancia_acima_de_3km():
    assert calcular_taxa_entrega(5.0) == 9.00


def test_deve_lancar_erro_para_distancia_zero():
    with pytest.raises(ValueError, match="Distância deve ser maior que zero."):
        calcular_taxa_entrega(0)


def test_deve_lancar_erro_para_distancia_negativa():
    with pytest.raises(ValueError, match="Distância deve ser maior que zero."):
        calcular_taxa_entrega(-5.0)
```

### Resultado da execução

```
$ pytest test_taxa_entrega.py -v

======================================================= test session starts ========================================================
platform linux -- Python 3.11.0, pytest-7.4.0
collected 5 items

test_taxa_entrega.py::test_deve_retornar_taxa_fixa_para_distancia_ate_3km          PASSED   [ 20%]
test_taxa_entrega.py::test_deve_retornar_taxa_fixa_para_distancia_exatamente_3km   PASSED   [ 40%]
test_taxa_entrega.py::test_deve_calcular_taxa_proporcional_para_distancia_acima_de_3km PASSED [ 60%]
test_taxa_entrega.py::test_deve_lancar_erro_para_distancia_zero                    PASSED   [ 80%]
test_taxa_entrega.py::test_deve_lancar_erro_para_distancia_negativa                PASSED   [100%]

======================================================== 5 passed in 0.03s =========================================================
```

| Métrica | Resultado |
|---|---|
| Total de testes | 5 |
| ✅ Passaram | 5 |
| ❌ Falharam | 0 |
| Cobertura dos cenários | Sucesso (3), Erro/borda (2) |

---

## 🔹 6. Reflexão no Contexto do LocalEats

**Foi difícil escrever testes antes do código?**  
No início, sim. O instinto natural é implementar primeiro e testar depois. Escrever o teste antes obrigou a pensar com mais cuidado na interface da função — quais parâmetros ela recebe, o que retorna, quais erros deve levantar — antes de se preocupar com como ela funciona internamente. Esse exercício de design foi mais valioso do que parecia.

**O TDD ajudou no desenvolvimento?**  
Sim, de forma clara. O ciclo Red → Green → Refactor criou um ritmo de desenvolvimento seguro: cada etapa tem um objetivo específico, e a refatoração só acontece com a segurança de testes verdes. Isso eliminou a hesitação de melhorar o código por medo de quebrar algo.

**Os testes aumentaram a confiança no código?**  
Muito. Ao refatorar a função (extraindo `_validar_distancia` e nomeando as constantes), foi possível fazer as mudanças sem ansiedade, pois os testes confirmaram imediatamente que o comportamento não mudou.

**O que melhorariam?**  
Adicionaria testes para valores de ponto flutuante com arredondamento (ex: 3.1 km) e testaria distâncias muito grandes para verificar se não há overflow ou comportamento inesperado. Também seria interessante parametrizar os testes com `@pytest.mark.parametrize` para cobrir mais casos com menos código repetido.

**Como isso ajuda no projeto do grupo (NoControle)?**  
O NoControle possui regras de negócio financeiras — cálculo de totais de assinaturas, alertas de vencimento, categorização de gastos. Essas são áreas onde um erro silencioso pode passar despercebido por semanas. TDD garantiria que cada regra fosse validada automaticamente a cada mudança no código, evitando regressões nas funcionalidades mais críticas do sistema.

---

*Documento elaborado para a disciplina de Qualidade de Software – Senac Pelotas, 2026.*
