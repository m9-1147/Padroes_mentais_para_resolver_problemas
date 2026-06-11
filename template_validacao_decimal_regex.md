# Template: Validação de Decimal Fixo (Regex) + Conversão

> **Padrão mental:** Confira o formato textual do número (regex) ANTES de converter → só transforme em float depois que o molde casar → insista até casar.

## Resumo em 1 frase
Valide a "aparência" do número como texto (dígitos + ponto + 2 casas) e só então converta para float, garantindo formato e segurança ao mesmo tempo.

---

## Os 3 Princípios Fundamentais
1. **Validar o texto blinda a conversão** — diferente do `try/except` (Caso 2), aqui a regex já garante que `float()` nunca vai quebrar. A regex é a "porta blindada"; o `float()` entra tranquilo.
2. **Regex fixa a precisão que o `float` não fixa** — `float("5.5")` e `float("5.50")` viram o mesmo número. Só validando o **texto** com `\.\d{2}` você garante que o usuário digitou exatamente 2 casas decimais.
3. **Âncoras `^...$` impedem lixo nas pontas** — sem elas, `re.match` aceitaria `"12.34abc"`. O molde precisa cobrir a string **inteira**.

## Quando USAR
* Valores monetários ou tarifas onde **as 2 casas decimais são obrigatórias** (R$, preço/hora, taxas).
* Quando você quer **rejeitar** `"5"`, `"5.5"`, `"5.555"` e aceitar **apenas** `"5.50"`.
* Quando precisa de **formato rígido + número usável** na mesma operação.

## As 4 Fases Universais

| Fase | O que faz | Detalhe crítico |
|------|-----------|-----------------|
| **1. CAPTURAR + LIMPAR** | `input().strip()` remove ruído nas pontas | Sempre limpe antes de testar |
| **2. VALIDAR (REGEX)** | `re.match(r'^\d+\.\d{2}$', s)` confere o formato | `\d+` (magnitude) + `\.` (ponto) + `\d{2}` (2 casas) |
| **3. CONVERTER** | `float(s)` — agora é 100% seguro | A regex já garantiu que não quebra |
| **4. SAIR / INSISTIR** | `break` se casou; loop repete se não | `except EOFError` encerra com segurança |

Tudo embrulhado em `while True:` — o **loop de insistência**.

---

## Esqueleto Genérico (silencioso)

```python
import re

while True:
    try:
        valor = input().strip()                 # Fase 1: capturar + limpar
        if re.match(r'^\d+\.\d{2}$', valor):     # Fase 2: validar formato decimal
            numero = float(valor)                # Fase 3: converter com segurança
            break                                # Fase 4: sair
    except EOFError:                             # fim da entrada
        break


```

## Versão com Mensagens de Erro Amigáveis

```python
import re

def ler_valor_2_casas(rotulo="valor"):
    """Lê um número no formato D+.DD (ex: 12.50, 7.00, 1000.99).
       Exige EXATAMENTE 2 casas decimais. Insiste até validar. Encerra em EOF."""
    PADRAO = r'^\d+\.\d{2}$'   # \d+ inteiro | \. ponto literal | \d{2} duas casas

    while True:
        try:
            texto = input(f"Digite o {rotulo} (ex: 12.50): ").strip()

            # Regra única: casar com o formato decimal de 2 casas
            if re.match(PADRAO, texto):
                return float(texto)

            print("⚠️  Erro: use o formato D.DD com 2 casas decimais (ex: 5.30).")

        except EOFError:
            print("\nEntrada encerrada.")
            return None


# ===== USO =====
VALOR_HORA = ler_valor_2_casas("valor da hora")

if VALOR_HORA is not None:
    horas = 40
    print(f"SALARIO: R$ {VALOR_HORA * horas:.2f}")

```

## Regras de Validação:

| Regra | Código (regex) | Exemplo |
| --- | --- | --- |
| Decimal com 2 casas | r'^\\d+.\\d{2}$' | "5.30" → ok ; "5.3" → falha |
| Decimal com 1 casa | r'^\\d+.\\d$' | "5.3" → ok |
| Decimal N casas | r'^\\d+.\\d{N}$' | ajuste {N} conforme precisão |
| Decimal opcional | r'^\\d+(.\\d{2})?$' | "5" e "5.30" → ambos ok |
| Inteiro OU decimal | r'^\\d+(.\\d+)?$' | "5" ou "5.5" ou "5.55" |
| Aceita sinal negativo | r'^-?\\d+.\\d{2}$' | "-5.30" → ok |
| Aceita vírgula (BR) | r'^\\d+,\\d{2}$' | "5,30" → ok (trocar ,→. antes do float!) |
| Milhar + decimal | r'^\\d{1,3}(.\\d{3})*,\\d{2}$' | "1.234,56" formato BR |


## Anatomia do Padrão "*^\d+\\.\d{2}$*"

| Trecho | Lê-se como | Por quê |
| --- | --- | --- |
| ^ | "começa aqui" | impede prefixo indevido |
| \\d+ | "1 ou mais dígitos" | parte inteira de qualquer magnitude |
| \\. | "um ponto literal" | a barra escapa o . (que sozinho = "qualquer char") |
| \\d{2} | "exatamente 2 dígitos" | força as 2 casas decimais |
| $ | "termina aqui" | impede sufixo indevido |


## Armadilhas a evitar

1. Ponto não escapado — r'^\d+.\d{2}$' (sem \) aceita "5x30". Sempre escape: \..
2. Formato brasileiro (vírgula) — se o usuário digita "5,30", a regex de ponto falha. Solução: aceite vírgula e troque antes de converter: float(texto.replace(',', '.')). O float() do Python não entende vírgula.
3. Mais de 2 casas rejeitadas — "5.999" falha (correto, se a regra exige 2). Se quiser arredondar em vez de rejeitar, valide com \d+(\.\d+)? e formate na saída com :.2f.
4. re.fullmatch como alternativa — re.fullmatch(r'\d+\.\d{2}', s) dispensa ^ e $ e é mais à prova de erro.


## Comparação rápida com "*try/except + intervalo*"

| Aspecto | Caso 2 (intervalo) | Caso 4 (regex decimal) |
| --- | --- | --- |
| Foco | grandeza (faixa de valor) | formato (aparência do texto) |
| Garante 2 casas? | ❌ Não | ✅ Sim |
| Protege o float()? | via try/except | via regex prévia |
| Use quando... | "entre 0 e 10" | "exatamente D.DD" |


## Combo poderoso: 
valide com regex (formato) e depois cheque o intervalo "(if 0 <= numero <= X)". Formato + grandeza = validação completa.
