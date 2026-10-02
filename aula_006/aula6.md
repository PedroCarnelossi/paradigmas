# Exercício em Duplas aula 006

---

 ## 1\. JavaScript

```
console.log(0.1 * 3 === 0.3);

console.log(9007199254740993);
```

 ### Saída

```
false
9007199254740992
```

 ### Explicação

 - O valor `0.1` não tem representação exata em binário (padrão IEEE 754). Multiplicado por 3, o resultado fica `0.30000000000000004`, que é diferente de `0.3`, por isso a comparação estrita retorna `false`.
- Todo número em JavaScript é um `double` de 64 bits, que só garante inteiros exatos até `2^53 - 1` (`Number.MAX_SAFE_INTEGER`, igual a `9007199254740991`). O literal `9007199254740993` passa desse limite e é arredondado para o valor representável mais próximo, `9007199254740992`.

 ### Detecção

 **Nunca.** Não há erro de compilação nem de execução: o programa roda normalmente, mas os valores calculados diferem do resultado matemático exato.

---

 ## 2\. Python

```
p = "maçã"

print(len(p), len(p.encode()))
```

 ### Saída

```
4 6
```

 ### Explicação

 - Em Python 3, `len` sobre uma `str` conta caracteres (pontos de código Unicode): `m`, `a`, `ç` e `ã`, totalizando 4.
- O método `encode()` usa UTF-8 por padrão e devolve `bytes`. Nessa codificação, `m` e `a` ocupam 1 byte cada, enquanto `ç` e `ã` ocupam 2 bytes cada, totalizando 6 bytes.

 ### Detecção

 **Nunca.** Não se trata de um erro: tamanho em caracteres e tamanho em bytes são medidas diferentes e é normal que não coincidam.

---

 ## 3\. Go

```
var b byte = 255

b++

fmt.Println(b)
```

 ### Saída

```
0
```

 ### Explicação

 Em Go, `byte` é um apelido para `uint8`, um inteiro sem sinal de 8 bits que só representa valores de `0` a `255`.

 Somar 1 ao valor máximo faz o número "dar a volta" (aritmética módulo 256):

```
255 + 1 → 0
```

 ### Detecção

 **Nunca.** A especificação de Go define esse comportamento para inteiros sem sinal: a operação é válida e não gera erro nem aviso. (O compilador só recusaria um estouro em uma constante, como `var b byte = 256`.)

---

 ## 4\. Java

```
int[] v = new int[3];

System.out.println(v[0]);

System.out.println(v[3]);
```

 ### Saída

```
0
```

 Em seguida, o programa é interrompido com a exceção:

```
ArrayIndexOutOfBoundsException
```

 ### Explicação

 Em Java, os elementos de um vetor de `int` recém-criado são inicializados automaticamente com `0`. Como a indexação começa em zero, um vetor de 3 posições tem apenas os índices:

```
0  1  2
```

 Por isso `v[0]` imprime `0`, enquanto `v[3]` tenta acessar uma posição que não existe.

 ### Detecção

 **Na execução.**

 A JVM confere os limites a cada acesso ao vetor. Ao encontrar um índice inválido, lança a exceção e encerra o programa, já que ela não é tratada.

---

 ## 5\. Rust

```
let s = String::from("oi");

let t = s;

println!("{} {}", s, t);
```

 ### Saída

 Nenhuma: o programa **não compila**.

 ### Explicação

 Em Rust, cada valor tem um único dono (**ownership**). Como `String` guarda seus dados no heap e não implementa `Copy`, a atribuição abaixo não copia o texto, e sim **move** a posse do valor:

```
let t = s;
```

 A partir dessa linha, `t` é o novo dono e `s` fica inválido. Usar `s` no `println!` caracteriza uso de um valor movido (erro `E0382: borrow of moved value`).

 ### Detecção

 **Na compilação.**

 O verificador de empréstimos (borrow checker) rejeita o código antes de gerar o executável. Para usar as duas variáveis, seria preciso clonar (`s.clone()`) ou emprestar (`&s`).

---

 ## 6\. C

```
union { int i; float f; } u;

u.f = 1.0f;

printf("%d\n", u.i);
```

 ### Saída

 O resultado depende de como a plataforma representa `float` e `int`.

 Em máquinas comuns (IEEE 754 e `int` de 32 bits), aparece:

```
1065353216
```

 ### Explicação

 Todos os membros de uma `union` ocupam o mesmo endereço de memória, então gravar em um deles sobrescreve os bytes dos outros.

 A linha abaixo grava o padrão de bits de `1.0f`, que em IEEE 754 é `0x3F800000`:

```
u.f = 1.0f;
```

 Já a leitura abaixo pega esses mesmos 32 bits e os interpreta como inteiro, e `0x3F800000` em decimal é `1065353216`:

```
u.i
```

 Nenhuma conversão numérica acontece: os bits são reinterpretados, e não convertidos (técnica chamada de *type punning*).

 ### Detecção

 **Nunca.** O compilador aceita o código e a execução não gera erro. O padrão C permite esse tipo de leitura, mas o valor obtido depende da implementação (tamanho dos tipos, formato do ponto flutuante e ordem dos bytes).

---
