# Template: Validação por Intervalo com try/except

> **Padrão mental:** Tente converter → verifique se está no intervalo permitido → só saia do loop quando o valor for válido.

## Resumo em 1 frase
Converta de imediato dentro de uma "rede de segurança" (try), aceite apenas se cair no intervalo esperado e insista enquanto não cair.

---

## Os 3 Princípios Fundamentais
1. **Converter sob proteção** — `float(input(...))` pode quebrar (ValueError) se vier texto. O `try/except` é a rede que impede o crash e devolve o controle ao loop.
2. **Intervalo é a regra-mestra** — `0.0 <= A <= 10.0` em uma única linha valida limite inferior e superior de uma vez. Só esse `if` decide se o valor é aceito.
3. **break é a porta de saída** — o loop só termina quando a condição é satisfeita (ou quando a entrada acaba, via EOFError). Sem `break`, o programa insiste para sempre.

## Quando USAR
* Quando o valor precisa cair dentro de uma **faixa numérica** (notas, idades, percentuais, índices).
* Quando você quer **conversão imediata** para número (não precisa validar formato textual rígido).
* Leitura interativa onde o usuário **pode digitar lixo** ou pressionar Ctrl+D/Ctrl+Z (EOF).

## As 4 Fases Universais

| Fase | O que faz | Detalhe crítico |
|------|-----------|-----------------|
| **1. TENTAR** | `try:` abre a rede de segurança | Tudo que pode quebrar fica aqui |
| **2. CONVERTER** | `float(input(...))` já transforma em número | Diferente do Caso 1: converte cedo |
| **3. VALIDAR INTERVALO** | `if limite_inf <= x <= limite_sup` | Uma linha cobre os dois limites |
| **4. SAIR / INSISTIR** | `break` se válido; loop repete se não | `except` captura erros sem crashar |

Tudo embrulhado em `while True:` — o **loop de insistência**.

---

## Esqueleto Genérico (silencioso)

```python
while True:
    try:
        x = float(input())                  # Fase 1+2: tentar e converter
        if LIMITE_INF <= x <= LIMITE_SUP:   # Fase 3: validar intervalo
            break                           # Fase 4: sair (válido)
    except ValueError:                      # entrada não numérica
        continue                            # insiste
    except EOFError:                        # fim da entrada (Ctrl+D/Z)
        break                               # encerra com segurança
```

## Versão com Mensagens de Erro Amigáveis

```python
def ler_nota(rotulo="nota"):
    """Lê uma nota de 0.0 a 10.0. Insiste até receber valor válido.
       Encerra silenciosamente se a entrada acabar (EOF)."""
    while True:
        try:
            valor = float(input(f"Digite a {rotulo} (0.0 a 10.0): "))

            # Regra única: precisa estar no intervalo permitido
            if 0.0 <= valor <= 10.0:
                return valor

            print("⚠️  Erro: a nota deve estar entre 0.0 e 10.0.")

        except ValueError:
            print("⚠️  Erro: digite um número válido (ex: 7.5).")
        except EOFError:
            print("\nEntrada encerrada.")
            return None


# ===== USO =====
A = ler_nota("primeira nota")
B = ler_nota("segunda nota")

if A is not None and B is not None:
    media = (A + B) / 2
    print(f"MEDIA: {media:.1f}")

```

## Regras de Validação:

| Regra | Código | Exemplo |
| --- | --- | --- |
| Conversão segura | try: float(s) ... except ValueError | impede crash com texto |
| Fim de entrada | except EOFError | trata Ctrl+D (Linux) / Ctrl+Z (Windows) |
| Intervalo fechado | LIMITE_INF <= x <= LIMITE_SUP | 0.0 <= A <= 10.0 |
| Intervalo aberto | LIMITE_INF < x < LIMITE_SUP | 0 < x < 100 (exclui as pontas) |
| Apenas mínimo | x >= LIMITE_INF | idade >= 0 |
| Apenas máximo | x <= LIMITE_SUP | desconto <= 100 |
| Inteiro em faixa | int(s) and 1 <= int(s) <= 7 | dia da semana 1..7 |
