# Template: Validação por Padrão de Texto (Regex)

> **Padrão mental:** Limpe a entrada → confronte com um molde de texto (regex) → só aceite se casar 100% com o molde → insista até casar.

## Resumo em 1 frase
Descreva o formato exato que você espera como uma "impressão digital" (regex) e só deixe passar a entrada que bater perfeitamente com ela.

---

## Os 3 Princípios Fundamentais
1. **Limpar antes de validar** — `.strip()` remove espaços e quebras de linha invisíveis nas pontas. Sem isso, `" ABC "` ou `"ABC\n"` poderiam falhar/passar de forma inesperada.
2. **Âncoras são obrigatórias** — `^` (início) e `$` (fim) garantem que a regra vale para a string **inteira**, não só para um pedaço. Sem âncoras, `re.match` aceitaria `"ABC123"` por casar só o começo.
3. **Regex descreve formato, não valor** — ela valida a *forma* ("só maiúsculas", "CEP", "e-mail"), enquanto `if/intervalo` valida a *grandeza*. Use regex quando o que importa é o **padrão dos caracteres**.

## Quando USAR
* Quando a entrada deve seguir um **formato textual rígido** (códigos, siglas, placas, CEP, e-mail, nomes só com letras).
* Quando as regras são muitas/complexas e ficariam feias com vários `if` encadeados.
* Quando você precisa aceitar/rejeitar com base na **composição dos caracteres** (maiúsculas, dígitos, símbolos, comprimento).

## As 4 Fases Universais

| Fase | O que faz | Detalhe crítico |
|------|-----------|-----------------|
| **1. CAPTURAR + LIMPAR** | `input().strip()` remove ruído nas pontas | Sempre limpe antes de testar |
| **2. VALIDAR (REGEX)** | `re.match(r'^padrão$', s)` confronta com o molde | Use `^` e `$` para casar a string toda |
| **3. CONVERTER/USAR** | Atribui o valor já validado | `str()` aqui é só documental (já é string) |
| **4. SAIR / INSISTIR** | `break` se casou; loop repete se não | `except EOFError` encerra com segurança |

Tudo embrulhado em `while True:` — o **loop de insistência**.

---

## Esqueleto Genérico (silencioso)

```python
import re

while True:
    try:
        valor = input().strip()             # Fase 1: capturar + limpar
        if re.match(r'^PADRAO$', valor):     # Fase 2: validar contra o molde
            resultado = valor                # Fase 3: usar valor válido
            break                            # Fase 4: sair
    except EOFError:                         # fim da entrada
        break


```

## Versão com Mensagens de Erro Amigáveis

```python
import re

def ler_codigo_maiusculas(rotulo="código"):
    """Lê uma string composta APENAS por letras maiúsculas (A-Z).
       Insiste até receber valor válido. Encerra em EOF."""
    PADRAO = r'^[A-Z]+$'   # ^ início | [A-Z]+ uma ou mais maiúsculas | $ fim

    while True:
        try:
            valor = input(f"Digite o {rotulo} (só letras MAIÚSCULAS): ").strip()

            # Regra única: casar 100% com o padrão
            if re.match(PADRAO, valor):
                return valor

            print("⚠️  Erro: use apenas letras maiúsculas, sem espaços ou números (ex: ABC).")

        except EOFError:
            print("\nEntrada encerrada.")
            return None


# ===== USO =====
vendedor = ler_codigo_maiusculas("vendedor")

if vendedor is not None:
    print(f"VENDEDOR REGISTRADO: {vendedor}")


```

## Regras de Validação:

| Regra | Código (regex) | Exemplo |
| --- | --- | --- |
| Só letras maiúsculas | r'^[A-Z]+$' | "ABC" → ok ; "AbC" → falha |
| Só letras minúsculas | r'^[a-z]+$' | "abc" → ok |
| Letras (qualquer caixa) | r'^[A-Za-z]+$' | "Abc" → ok ; "Ab1" → falha |
| Só dígitos | r'^[0-9]+$' | "12345" → ok |
| Letras + dígitos | r'^[A-Za-z0-9]+$' | "Abc123" → ok |
| Comprimento fixo (3) | r'^[A-Z]{3}$' | "ABC" → ok ; "AB" → falha |
| Faixa de comprimento | r'^[A-Z]{2,5}$' | 2 a 5 maiúsculas |
| Placa Mercosul (BR) | r'^[A-Z]{3}\\d[A-Z]\\d{2}$' | "ABC1D23" → ok |
| CEP brasileiro | r'^\\d{5}-?\\d{3}$' | "35000-000" → ok |
| E-mail simples | r'^[\\w.]+@[\\w.]+.\\w+$' | "a@b.com" → ok |


## Bússula de Metacaracteres

| Símbolo | Significado |
| --- | --- |
| ^ | início da string |
| $ | fim da string |
| [A-Z] | qualquer letra maiúscula |
| \\d | qualquer dígito (0-9) |
| \\w | letra, dígito ou _ |
| + | uma ou mais repetições |
| * | zero ou mais repetições |
| ? | zero ou uma (opcional) |
| {n} | exatamente n repetições |
| {n,m} | de n a m repetições |

## Armadilhas a evitar:
1. re.match vs re.fullmatch — match ancora só no início. Por isso o $ no fim é essencial. Alternativa mais segura: re.fullmatch(r'[A-Z]+', s) dispensa as âncoras.
2. str(valor_str0) é redundante — input() já devolve str. A conversão não faz mal, mas é decorativa. Mantenha por clareza ou remova por economia.
3. Esquecer o import re — o padrão depende do módulo re. Sem ele → NameError.
4. "+" exige pelo menos 1 caractere — string vazia "" falha em ^[A-Z]+$ (o que geralmente é o desejado). Se quisesse aceitar vazio, usaria "*".

