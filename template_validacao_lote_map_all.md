# Template: Validação em Lote (map + all) com Intervalo Coletivo

> **Padrão mental:** Converta a linha inteira de uma vez (map) → valide quantidade E intervalo de TODOS de uma vez (all) → só aceite se o conjunto inteiro passar.

## Resumo em 1 frase
Trate a linha como um lote homogêneo: converta tudo de uma vez com `map`, e use `all` para exigir que cada item respeite a mesma regra antes de aceitar.

---

## Os 3 Princípios Fundamentais
1. **`map` converte o lote inteiro** — `list(map(int, input().split()))` transforma todas as strings em inteiros numa tacada. Ideal quando todos os campos têm o **mesmo tipo**.
2. **`all` valida o coletivo numa expressão** — `all(1 <= v <= 4 for v in valores)` substitui um loop de `if`s. Só retorna `True` se **todos** passarem; um único intruso reprova a linha inteira.
3. **Quantidade + conteúdo são duas guardas** — `len(valores) == 5` garante o **número certo** de campos; `all(...)` garante o **valor certo** de cada um. As duas juntas blindam a entrada.

## Quando USAR
* Linhas com **N valores homogêneos** (todos inteiros, todos na mesma faixa).
* Quando todos os campos seguem **a mesma regra** (ex: notas, respostas de prova, coordenadas).
* Gabaritos, vetores, listas de respostas — qualquer caso de "todos devem obedecer X".

## As 4 Fases Universais

| Fase | O que faz | Detalhe crítico |
|------|-----------|-----------------|
| **1. CAPTURAR + CONVERTER** | `list(map(int, input().split()))` | ⚠️ Converte cedo — exige campos homogêneos |
| **2. VALIDAR QUANTIDADE** | `len(valores) == N` | confere o número de campos |
| **3. VALIDAR COLETIVO** | `all(regra for v in valores)` | uma expressão valida todos |
| **4. RETORNAR** | `return valores` sai do loop | desempacota depois: `A, B, ... = ...` |

Tudo embrulhado em `while True:` — o **loop de insistência**.

---

## Esqueleto Genérico (silencioso)

```python
def ler_linha():
    while True:                                       # Fase 0: insistir
        valores = list(map(int, input().split()))     # Fase 1: capturar + converter
        if (len(valores) == QUANTIDADE                 # Fase 2: quantidade
                and all(MIN <= v <= MAX for v in valores)):  # Fase 3: coletivo
            return valores                             # Fase 4: retornar


```

## Versão com Mensagens de Erro Amigáveis

```python
def ler_inteiro_em_faixa(rotulo, minimo, maximo):
    """Lê um único inteiro dentro de [minimo, maximo]. Insiste até validar."""
    while True:
        try:
            x = int(input(f"Digite {rotulo} ({minimo} a {maximo}): "))
            if minimo <= x <= maximo:
                return x
            print(f"⚠️  Erro: valor deve estar entre {minimo} e {maximo}.")
        except ValueError:
            print("⚠️  Erro: digite um número inteiro válido.")


def ler_linha(qtd, minimo, maximo):
    """Lê uma linha com 'qtd' inteiros, todos em [minimo, maximo].
       Insiste até a linha inteira ser válida."""
    while True:
        try:
            valores = list(map(int, input(
                f"Digite {qtd} números ({minimo}-{maximo}) separados por espaço: "
            ).split()))
        except ValueError:
            print("⚠️  Erro: use apenas números inteiros separados por espaço.")
            continue

        # Regra 1: quantidade exata
        if len(valores) != qtd:
            print(f"⚠️  Erro: digite EXATAMENTE {qtd} valores.")
            continue

        # Regra 2: todos dentro do intervalo
        if not all(minimo <= v <= maximo for v in valores):
            print(f"⚠️  Erro: todos os valores devem estar entre {minimo} e {maximo}.")
            continue

        return valores


# ===== USO =====
T = ler_inteiro_em_faixa("o gabarito (alternativa correta)", 1, 4)
A, B, C, D, E = ler_linha(qtd=5, minimo=1, maximo=4)

# Conta quantas respostas batem com o gabarito
acertaram = sum(1 for resposta in [A, B, C, D, E] if resposta == T)
print(f"QUANTOS ACERTARAM: {acertaram}")


```

## Regras de Validação:

| Regra | Código | Exemplo |
| --- | --- | --- |
| Converter lote (int) | list(map(int, s.split())) | "1 2 3" → [1, 2, 3] |
| Converter lote (float) | list(map(float, s.split())) | "1.5 2.0" → [1.5, 2.0] |
| Quantidade exata | len(valores) == N | controla nº de itens |
| Todos no intervalo | all(MIN <= v <= MAX for v in valores) | reprova se 1 estiver fora |
| Pelo menos um atende | any(v == ALVO for v in valores) | existência de um caso |
| Nenhum repetido | len(valores) == len(set(valores)) | exige itens distintos |
| Conjunto fechado | all(v in {1,2,3,4} for v in valores) | só valores permitidos |
| Soma esperada | sum(valores) == ALVO | total deve bater |


## Regras de Validação:

| Função | Lê-se como | Retorna True quando... |
| --- | --- | --- |
| all(...) | "TODOS obedecem?" | nenhum item viola a regra |
| any(...) | "ALGUM obedece?" | pelo menos um item satisfaz |
| sum(1 for ... if ...) | "QUANTOS obedecem?" | (conta, não é booleano) |


## Armadilhas a evitar:

1. "map(int, ...)" quebra com texto — se vier "1 dois 3", o int() lança ValueError e o esqueleto silencioso crasha. Por isso a versão amigável envolve a conversão em try/except.
2. Validar quantidade ANTES de acessar índices — só desempacote A, B, C, D, E = ... depois de garantir len == 5, ou o desempacotamento estoura.
3. "all([])" é True! — uma lista vazia passa no all(...). É a guarda len(valores) == N que impede esse falso positivo. Nunca confie só no all.
4. "T" sem try/except — no código original, int(input()) para ler T pode quebrar com entrada não numérica. A versão amigável protege com ler_inteiro_em_faixa.


## Melhor padrão para cada cenário:

| Cenário | Melhor padrão |
| --- | --- |
| Campos de tipos diferentes na mesma linha | Caso 1 (validar um a um) |
| N campos iguais, mesma regra | Caso 5 (map + all) ✅ |
| Um único número numa faixa | Caso 2 (try + intervalo) |
| Formato textual rígido | Caso 3 / 4 (regex) |
