# 🧠 Padrões Mentais de Validação de Entrada em Python

> Coleção de **templates reutilizáveis** para validar entrada de dados em Python, organizados como *padrões mentais* — moldes que você escreve uma vez e aplica em qualquer exercício, prova ou sistema interativo.

[![Python](https://img.shields.io/badge/Python-3.x-blue.svg)](https://www.python.org/)

---

## 📖 Sobre o Projeto

Toda entrada de usuário é **texto suspeito** até prova em contrário. Este repositório reúne
padrões testados para transformar entrada caótica em dados confiáveis, seguindo uma filosofia única:

> **Ler como texto → validar cada regra → converter só o que passou → insistir até acertar.**

Cada padrão é um **molde de bolo**: documentado com princípios, fases universais, esqueleto
genérico, versão com mensagens amigáveis, tabela de regras e armadilhas a evitar.

---

## 🎯 A Filosofia Central

| Princípio | Por quê |
|-----------|---------|
| **String primeiro, número depois** | `int("5.30")` quebra; `"5.30".isdigit()` apenas devolve `False`. Validar texto é sempre seguro. |
| **Uma regra por bloco `if`** | Legível, fácil de depurar, fácil de adicionar/remover regras. |
| **Função reutilizável = molde de bolo** | Escreva uma vez, use N vezes. |
| **Loop de insistência (`while True`)** | O programa só avança quando a entrada é válida. |

---

## 🗂️ Catálogo de Padrões

| Caso | Padrão | Foco | Quando usar |
|---|--------|------|-------------|
| **01** | [Validação Campo a Campo](./template_validacao_entrada_com_loop_insistencia.md) | Validar e converter cada campo individualmente | Campos **heterogêneos** (tipos/formatos diferentes na mesma linha) |
| **02** | [Try/Except + Intervalo](./template_validacao_intervalo_com_try-except.md) | Conversão segura + faixa de valores | Um número que deve estar **entre X e Y** |
| **03** | [Validação por Regex (texto)](./template_validacao_regex_padrao-texto.md) | Confrontar com molde textual | Códigos, siglas, placas — **formato de caracteres** |
| **04** | [Decimal Fixo (Regex) + float](./template_validacao_decimal_regex.md) | Validar formato `D.DD` antes de converter | Valores monetários com **2 casas obrigatórias** |
| **05** | [Validação em Lote (map + all)](./template_validacao_lote_map_all.md) | Converter e validar N itens de uma vez | Campos **homogêneos**, mesma regra para todos |

---

## 🧭 Qual padrão usar? (Árvore de Decisão)

```text
A entrada tem vários campos na mesma linha?
├── SIM → Os campos são do MESMO tipo e mesma regra?
│        ├── SIM → 🟦 CASO 05 (map + all)
│        └── NÃO → 🟦 CASO 01 (campo a campo)
└── NÃO → O que importa é o FORMATO do texto?
         ├── SIM → É um número decimal fixo?
         │        ├── SIM → 🟦 CASO 04 (regex decimal)
         │        └── NÃO → 🟦 CASO 03 (regex texto)
         └── NÃO → É um número numa faixa?
                  └── SIM → 🟦 CASO 02 (try/except + intervalo)
```

---

## 🏗️ As 4 Fases Universais

Todo padrão segue a mesma anatomia, embrulhada em `while True:`

| Fase | O que faz | Detalhe crítico |
|------|-----------|-----------------|
| **1. CAPTURAR** | Lê a entrada como **string** | Não converta ainda (exceto lotes homogêneos) |
| **2. VALIDAR** | Testa cada regra; falhou → `continue` | Uma regra por bloco |
| **3. CONVERTER** | Passou? `int()`, `float()`... | Só converte o que é seguro |
| **4. RETORNAR** | Devolve os valores prontos | Sai do loop automaticamente |

---

## 📋 Tabela-Mestra de Regras de Validação

| Regra | Código | Exemplo |
|-------|--------|---------|
| Inteiro positivo | `s.isdigit()` | `"123"` → True |
| Inteiro com sinal | `s.lstrip('-').isdigit()` | `"-12"` → True |
| Quantidade de campos | `len(partes) == N` | controla nº de valores |
| Dentro de intervalo | `1 <= int(s) <= 4` | valida faixa |
| Float 2 casas | `re.match(r'^\d+\.\d{2}$', s)` | `"5.30"` → True ; `"5.3"` → False |
| Só maiúsculas | `re.match(r'^[A-Z]+$', s)` | `"ABC"` → True |
| Lote no intervalo | `all(1 <= v <= 4 for v in valores)` | reprova se 1 estiver fora |
| Conjunto fechado | `s in {"S", "N"}` | só opções específicas |
| Float genérico | `try: float(s) ... except: ...` | qualquer float válido |

---

## 🚀 Exemplo Rápido

```python
def ler_entrada():
    """Lê 2 inteiros e 1 float de 2 casas. Insiste até validar."""
    while True:
        partes = input().split()
        if len(partes) != 3:
            continue
        cod, num, valor = partes
        if not (cod.isdigit() and num.isdigit()):
            continue
        if not re.match(r'^\d+\.\d{2}$', valor):
            continue
        return int(cod), int(num), float(valor)
```

## Estrutura do Repositório

```
├── README.md
├── template_validacao_entrada_com_loop_insistencia.md   # Validação campo a campo
├── template_validacao_intervalo_com_try-except.md   # Try/except + intervalo
├── template_validacao_regex_padrao-texto.md   # Regex (texto)
├── template_validacao_decimal_regex.md   # Regex decimal + float
└── template_validacao_lote_map_all.md   # Lote (map + all)
```

## ⚠️ Armadilhas Recorrentes

- **`all([])` é `True`** → sempre combine com `len(valores) == N`.
- **Ponto não escapado em regex** → use `\.`, nunca `.` solto.
- **Formato brasileiro (vírgula)** → `float()` não entende `,`; faça `.replace(',', '.')`.
- **`re.match` ancora só no início** → use `^...$` ou `re.fullmatch`.
- **Validar quantidade ANTES de desempacotar** → ou o `A, B, C = ...` estoura.

---

## 🗺️ Próximos Padrões

- [ ] Caso 06 — Validação de datas (`datetime`)
- [ ] Caso 07 — Entrada com múltiplas linhas (`sys.stdin`)
- [ ] Caso 08 — Validação de e-mail / formatos compostos
