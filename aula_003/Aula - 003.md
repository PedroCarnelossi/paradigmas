# Derivação de um Código a partir da Gramática da Linguagem Python

## 1. Introdução

As linguagens de programação seguem regras que determinam como um código deve ser escrito. Essas regras formam uma **gramática formal**, utilizada para identificar se determinada instrução é sintaticamente válida.

Nesta atividade foi escolhida a linguagem **Python**, utilizando parte de sua gramática oficial para demonstrar a derivação do código:

```python
x = 1 + 2
```

O exemplo envolve uma atribuição e uma expressão aritmética, permitindo demonstrar de forma simples o funcionamento das regras gramaticais.

## 2. Linguagem e fonte da gramática

A linguagem escolhida foi **Python**.

A gramática foi consultada na documentação oficial:

**Python Software Foundation. Full Grammar Specification.**  
[https://docs.python.org/3/reference/grammar.html](https://docs.python.org/3/reference/grammar.html)

A gramática atual do Python utiliza uma notação baseada em **PEG, Parsing Expression Grammar**.

Alguns símbolos importantes da notação são:

- `|`: indica alternativas;
    
- `*`: zero ou mais ocorrências;
    
- `+`: uma ou mais ocorrências;
    
- `[ ]`: elemento opcional;
    
- `NAME` e `NUMBER`: representam tokens da linguagem.
    

## 3. Código escolhido

O código que será derivado é:

```python
x = 1 + 2
```

Nesse código:

- `x` é a variável;
    
- `=` representa a atribuição;
    
- `1 + 2` é a expressão aritmética.
    

O resultado da soma é `3`, que será associado à variável `x`.

## 4. Principais regras de produção utilizadas

Foram selecionadas somente as regras necessárias para representar o código escolhido, conforme permitido pelo enunciado da atividade.

As principais regras são:

```text
file:
    | [statements] ENDMARKER

statements:
    | statement+

statement:
    | simple_stmts

simple_stmts:
    | simple_stmt !';' NEWLINE

simple_stmt:
    | assignment
```

Essas regras mostram que um programa possui instruções e que uma instrução simples pode ser uma atribuição.

A regra utilizada para a atribuição é:

```text
assignment:
    | (star_targets '=')+ annotated_rhs !'=' [TYPE_COMMENT]
```

De forma simplificada:

```text
alvo = expressão
```

No exemplo:

```text
x = 1 + 2
```

Para reconhecer o `x`, são utilizadas regras como:

```text
star_targets
→ star_target
→ target_with_star_atom
→ star_atom
→ NAME
```

Assim, `x` é reconhecido como um token `NAME`.

Para a expressão `1 + 2`, a gramática passa por diferentes níveis de expressão até chegar à regra de soma:

```text
expression
→ disjunction
→ conjunction
→ inversion
→ comparison
→ bitwise_or
→ bitwise_xor
→ bitwise_and
→ shift_expr
→ sum
```

A regra principal da soma é:

```text
sum:
    | sum '+' term
    | sum '-' term
    | term
```

Os números são reconhecidos seguindo:

```text
term
→ factor
→ power
→ await_primary
→ primary
→ atom
→ NUMBER
```

Portanto, `1` e `2` são reconhecidos como tokens `NUMBER`.

## 5. Derivação passo a passo

A derivação deve iniciar pelo símbolo inicial e aplicar sucessivamente as produções até chegar ao código escolhido, como solicitado no enunciado.

Começando pelo símbolo inicial:

```text
file
⇒ statements ENDMARKER
⇒ statement ENDMARKER
⇒ simple_stmts ENDMARKER
⇒ simple_stmt NEWLINE ENDMARKER
⇒ assignment NEWLINE ENDMARKER
```

Aplicando a regra de atribuição:

```text
⇒ star_targets "=" annotated_rhs
```

Derivando o lado esquerdo:

```text
star_targets
⇒ star_target
⇒ target_with_star_atom
⇒ star_atom
⇒ NAME
```

Assim:

```text
⇒ NAME "=" annotated_rhs
```

Derivando o lado direito:

```text
annotated_rhs
⇒ star_expressions
⇒ star_expression
⇒ expression
```

Percorrendo as regras de expressão:

```text
expression
⇒ disjunction
⇒ conjunction
⇒ inversion
⇒ comparison
⇒ bitwise_or
⇒ bitwise_xor
⇒ bitwise_and
⇒ shift_expr
⇒ sum
```

Aplicando a regra de soma:

```text
sum
⇒ sum "+" term
⇒ term "+" term
```

Cada termo é transformado em número:

```text
term
⇒ factor
⇒ power
⇒ await_primary
⇒ primary
⇒ atom
⇒ NUMBER
```

Portanto:

```text
NAME "=" NUMBER "+" NUMBER
```

Substituindo os tokens:

```text
NAME   → x
NUMBER → 1
NUMBER → 2
```

Resultado:

```python
x = 1 + 2
```

## 6. Símbolos terminais e não terminais

### Terminais

Os terminais correspondem aos elementos finais reconhecidos no código.

Nesta derivação, os principais são:

```text
NAME
NUMBER
=
+
NEWLINE
ENDMARKER
```

No código:

- `x` é reconhecido como `NAME`;
    
- `1` e `2` são reconhecidos como `NUMBER`;
    
- `=` é o operador de atribuição;
    
- `+` é o operador de soma.
    

### Não terminais

Os símbolos não terminais representam estruturas que ainda precisam ser derivadas.

Entre os principais utilizados estão:

```text
file
statements
statement
simple_stmts
simple_stmt
assignment
star_targets
star_target
annotated_rhs
star_expressions
expression
sum
term
factor
power
primary
atom
```

Esses elementos são substituídos progressivamente até chegar aos tokens que formam o código final.

## 7. Resultado e explicação

O código gerado pela derivação foi:

```python
x = 1 + 2
```

A gramática inicialmente reconhece o código como uma instrução simples. Depois identifica que essa instrução é uma atribuição.

O lado esquerdo é reconhecido como um `NAME`, representado por `x`. O lado direito é reconhecido como uma expressão e, posteriormente, como uma soma formada por dois números.

De maneira simplificada, a estrutura é:

```text
Atribuição
├── NAME → x
├── =
└── Soma
    ├── NUMBER → 1
    ├── +
    └── NUMBER → 2
```

Assim, as regras da gramática permitem demonstrar formalmente por que `x = 1 + 2` é uma instrução sintaticamente válida em Python.

## 8. Conclusão

A derivação demonstra que mesmo uma instrução simples possui diversas regras gramaticais por trás de sua construção.

A gramática permite que o analisador sintático identifique que `x` pode receber um valor, que o símbolo `=` representa uma atribuição e que `1 + 2` forma uma expressão aritmética válida.

Também é possível compreender a diferença entre **símbolos não terminais**, que representam estruturas que ainda serão desenvolvidas, e **símbolos terminais ou tokens**, que formam o código final.

Dessa forma, utilizando apenas uma pequena parte da gramática oficial do Python, foi possível realizar a derivação completa de um código válido, atendendo aos itens solicitados na atividade.

## Referência

PYTHON SOFTWARE FOUNDATION. **Full Grammar Specification: Python Documentation**. Disponível em: https://docs.python.org/3/reference/grammar.html