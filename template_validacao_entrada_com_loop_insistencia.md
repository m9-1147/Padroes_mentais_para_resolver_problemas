# Template: Validação de Entrada com Loop de Insistência

> **Padrão mental:** Ler como texto → validar cada campo → converter só o que passou → repetir até ser válido.

## Resumo em 1 frase
Trate toda entrada como texto suspeito: valide cada pedaço pela sua regra, converta só depois e insista até o usuário acertar.

---

## Os 3 Princípios Fundamentais
1. String primeiro, número depois int("5.30") quebra o programa (ValueError). "5.30".isdigit() apenas devolve False, sem travar. ➜ Validar texto é sempre seguro.
2. Uma regra por bloco if Legível, fácil de depurar, fácil de adicionar/remover regras. Cada continue é uma "porta" que rejeita entrada ruim.
3. Função reutilizável = molde de bolo Escreva o molde uma vez, use N vezes. Linhas com mesmo formato = mesma função chamada repetidamente.

## Quando USAR
* Exercícios que exigem formato rígido de entrada.
* Programas interativos onde o usuário pode errar.
* Campos heterogêneos (tipos/formatos diferentes na mesma linha).

## As 4 Fases Universais

| Fase | O que faz | Detalhe crítico |
|------|-----------|-----------------|
| **1. CAPTURAR** | `input().split()` devolve lista de **strings** | NÃO converta ainda! |
| **2. VALIDAR** | Testa cada regra; se falhar → `continue` | Uma regra por bloco `if` |
| **3. CONVERTER** | Passou? Agora sim: `int()`, `float()`... | Só converte o que é seguro |
| **4. RETORNAR** | Devolve os valores prontos para uso | Sai do loop automaticamente |

Tudo embrulhado em `while True:` — o **loop de insistência**.

---

## Esqueleto Genérico (silencioso)

```python
def ler_entrada():
    while True:                              # Fase 0: insistir
        partes = input().split()             # Fase 1: capturar texto

        # Fase 2: validar (uma regra por bloco)
        if len(partes) != QUANTIDADE:        # quantidade correta?
            continue
        if not (partes[0].isdigit()):        # formato do campo?
            continue
        if not regra_especial(partes[2]):    # validação complexa?
            continue

        # Fase 3 + 4: converter e devolver
        return int(partes[0]), int(partes[1]), float(partes[2])

```

## Versão com Mensagens de Erro Amigáveis

```python
def ler_entrada():
    """Lê uma linha com: 2 inteiros e 1 float de 2 casas decimais.
       Insiste até receber uma entrada válida."""
    while True:
        partes = input().split()

        # Regra 1: quantidade exata de campos
        if len(partes) != 3:
            print("⚠️  Erro: digite EXATAMENTE 3 valores separados por espaço.")
            continue

        cod, num, valor = partes

        # Regra 2: os dois primeiros devem ser inteiros
        if not cod.isdigit():
            print("⚠️  Erro: o 1º valor (código) deve ser um inteiro.")
            continue
        if not num.isdigit():
            print("⚠️  Erro: o 2º valor (quantidade) deve ser um inteiro.")
            continue

        # Regra 3: o terceiro deve ser float com EXATAMENTE 2 casas decimais
        if not eh_float_2_casas(valor):
            print("⚠️  Erro: o 3º valor deve ser um float com 2 casas (ex: 5.30).")
            continue

        # Tudo válido → converte e retorna
        return int(cod), int(num), float(valor)


def eh_float_2_casas(s):
    """Valida se a string representa um float com exatamente 2 casas decimais."""
    if s.count('.') != 1:          # precisa ter UM ponto
        return False
    inteira, decimal = s.split('.')
    return (inteira.isdigit()       # parte inteira é dígito
            and decimal.isdigit()   # parte decimal é dígito
            and len(decimal) == 2)  # exatamente 2 casas


# ===== USO =====
cod1, num1, valor1 = ler_entrada()   # 1ª linha
cod2, num2, valor2 = ler_entrada()   # 2ª linha

total = num1 * valor1 + num2 * valor2
print(f"VALOR A PAGAR: R$ {total:.2f}")

```

## Regras de Validação:

| Regra | Código | Exemplo |
| --- | --- | --- |
| Inteiro positivo | s.isdigit() | "123" → True ; "-1" → False |
| Inteiro com sinal | s.lstrip('-').isdigit() | "-12" → True |
| Quantidade de campos | len(partes) == N | controla nº de valores na linha |
| Dentro de intervalo | 1 <= int(s) <= 4 | valida faixa de valores |
| Float 2 casas | eh_float_2_casas(s) | "5.30" → True ; "5.3" → False |
| Conjunto fechado | s in {"S", "N"} | aceita só opções específicas |
| Float genérico (seguro) | try: float(s) ... except: ... | aceita qualquer float válido |

