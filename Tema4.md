# Tema 4 — Implementação de tratamento de exceções em Java

## 1. O que é uma exceção?

Uma **exceção** é um problema que acontece durante a execução do programa e interrompe o fluxo normal.

Exemplo clássico:

```java
int resultado = 10 / 0;
```

Isso gera:

```text
ArithmeticException: / by zero
```

Ou seja: o código compilou, mas deu problema enquanto rodava.

O PDF diferencia bem isso: erro de sintaxe é erro de compilação; exceção é erro em tempo de execução. 

---

## 2. Como o Java trata uma exceção?

Quando uma exceção acontece, o Java cria um **objeto de exceção**. Esse objeto guarda informações sobre o erro: tipo, mensagem e estado do programa.

Depois, a JVM procura na **pilha de chamadas** algum bloco capaz de tratar aquela exceção. Esse bloco é chamado de **exception handler**, ou tratador de exceção. 

Exemplo:

```java
try {
    int resultado = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Erro: divisão por zero.");
}
```

Aqui:

```java
try
```

é o bloco monitorado.

```java
catch
```

é o bloco que captura e trata a exceção.

---

## 3. Estrutura `try/catch/finally`

A estrutura geral é:

```java
try {
    // código que pode gerar exceção
} catch (TipoDaExcecao e) {
    // tratamento da exceção
} finally {
    // código que roda ao final
}
```

Exemplo:

```java
try {
    int resultado = 10 / 0;
    System.out.println(resultado);
} catch (ArithmeticException e) {
    System.out.println("Não é possível dividir por zero.");
} finally {
    System.out.println("Fim da tentativa.");
}
```

Saída:

```text
Não é possível dividir por zero.
Fim da tentativa.
```

O `finally` é usado para código que deve executar no final, como fechar arquivo, fechar conexão, liberar recurso etc. O PDF mostra esse uso com um exemplo de divisão e impressão de `"Bloco finally."`. 

---

## 4. Múltiplos `catch`

Você pode ter mais de um `catch`.

```java
try {
    int[] numeros = {1, 2, 3};
    System.out.println(numeros[10]);
} catch (ArithmeticException e) {
    System.out.println("Erro aritmético.");
} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println("Índice inválido.");
} catch (Exception e) {
    System.out.println("Erro genérico.");
}
```

A ordem importa.

Você deve colocar as exceções **mais específicas primeiro** e as mais genéricas depois.

Certo:

```java
catch (ArrayIndexOutOfBoundsException e) {
}
catch (Exception e) {
}
```

Errado:

```java
catch (Exception e) {
}
catch (ArrayIndexOutOfBoundsException e) {
}
```

Porque `Exception` captura quase tudo. Se ela vier antes, o segundo `catch` nunca será alcançado.

O PDF mostra essa ideia quando explica que, se uma exceção mais geral vier antes, ela captura antes de chegar no bloco mais específico. 

---

## 5. Hierarquia das exceções

Em Java, as exceções seguem uma hierarquia.

Simplificando:

```text
Object
 └── Throwable
      ├── Error
      └── Exception
            └── RuntimeException
```

Os tipos principais são:

```text
Throwable
Error
Exception
RuntimeException
```

## `Throwable`

É a classe base para tudo que pode ser “lançado” como problema.

## `Error`

Representa erros mais graves, geralmente relacionados ao ambiente da JVM.

Exemplos:

```java
StackOverflowError
OutOfMemoryError
UnknownError
```

Normalmente você **não deve tentar tratar `Error` como regra principal**. Ele indica problemas mais sérios.

## `Exception`

Representa exceções que o programa pode tratar.

Exemplos:

```java
IOException
IllegalAccessException
SQLException
```

## `RuntimeException`

São exceções que acontecem em tempo de execução e geralmente indicam erro de lógica.

Exemplos:

```java
ArithmeticException
NullPointerException
ArrayIndexOutOfBoundsException
IllegalArgumentException
```

O PDF classifica os tipos de exceção a partir de `Throwable`, separando principalmente `Error`, `Exception` e `RuntimeException`. 

---

## 6. Exceções implícitas e explícitas

O material usa dois termos importantes:

```text
Exceções implícitas
Exceções explícitas
```

## Exceções implícitas

São exceções ligadas a `Error`, `RuntimeException` e suas subclasses.

Exemplos:

```java
ArithmeticException
NullPointerException
ArrayIndexOutOfBoundsException
StackOverflowError
UnknownError
```

Elas podem acontecer automaticamente durante a execução.

Exemplo:

```java
int resultado = 10 / 0;
```

Você não escreveu `throw`, mas a exceção acontece.

O PDF diz que exceções implícitas são as definidas nas classes `Error`, `RuntimeException` e derivadas; inclusive cita `UnknownError` como subclasse de `Error`. 

## Exceções explícitas

São exceções que o programador lança manualmente com `throw`, principalmente quando não são `RuntimeException` ou `Error`.

Exemplo:

```java
throw new IllegalAccessException();
```

O PDF usa `IllegalAccessException` como exemplo de exceção explícita. Como ela não é `RuntimeException` nem `Error`, ela precisa ser tratada ou declarada com `throws`. 

---

## 7. `throw`

`throw` serve para **lançar uma exceção**.

Exemplo:

```java
public void sacar(double valor) {
    if (valor <= 0) {
        throw new IllegalArgumentException("Valor inválido.");
    }

    System.out.println("Saque realizado.");
}
```

Aqui você está dizendo:

```text
Se o valor for inválido, lance uma exceção.
```

Outro exemplo:

```java
public int dividir(int a, int b) {
    if (b == 0) {
        throw new ArithmeticException("Divisor nulo.");
    }

    return a / b;
}
```

`throw` lança o objeto de exceção.

---

## 8. `throws`

`throws` é colocado na assinatura do método para avisar:

```text
Este método pode lançar tal exceção.
```

Exemplo:

```java
public int getElemento(int i) throws IllegalAccessException {
    if (i < 0 || i > 3) {
        throw new IllegalAccessException("Índice inválido.");
    }

    return vetor[i];
}
```

Aqui:

```java
throw new IllegalAccessException(...)
```

lança a exceção.

```java
throws IllegalAccessException
```

avisa quem chamar o método que essa exceção pode acontecer.

O PDF explica que, se uma exceção explícita não for tratada localmente, o Java obriga o uso de `throws` na assinatura do método, e os chamadores precisam tratar ou propagar também. 

Resumo:

```text
throw  = lança a exceção.
throws = declara que o método pode lançar exceção.
```

---

## 9. Tratando localmente ou propagando

Você tem duas escolhas quando uma exceção pode acontecer.

## Tratar no próprio método

```java
public int dividir(int a, int b) {
    try {
        return a / b;
    } catch (ArithmeticException e) {
        System.out.println("Erro: divisão por zero.");
        return 0;
    }
}
```

## Propagar para quem chamou

```java
public int dividir(int a, int b) throws ArithmeticException {
    if (b == 0) {
        throw new ArithmeticException("Divisor nulo.");
    }

    return a / b;
}
```

Aí quem chama precisa lidar com isso:

```java
try {
    int resultado = dividir(10, 0);
} catch (ArithmeticException e) {
    System.out.println(e.getMessage());
}
```

O PDF explica que a exceção pode ser propagada pela cadeia de chamadores até encontrar um `catch` adequado; se não encontrar, o tratador padrão do Java encerra o programa. 

---

## 10. Relançamento de exceção

Relançar é capturar uma exceção e lançar ela de novo.

Exemplo:

```java
public int divisao(int dividendo, int divisor) throws ArithmeticException {
    try {
        if (divisor == 0) {
            throw new ArithmeticException("Divisor nulo.");
        }

        return dividendo / divisor;

    } catch (ArithmeticException e) {
        System.out.println("Erro tratado parcialmente: " + e.getMessage());
        throw e;
    }
}
```

Aqui:

```java
catch (ArithmeticException e)
```

captura.

```java
throw e;
```

relança.

Isso é útil quando o método quer registrar/logar/tratar parcialmente, mas ainda quer deixar outro lugar decidir o tratamento final.

O PDF explica exatamente isso: uma exceção capturada pode ser tratada, parcialmente tratada ou não tratada; nos dois últimos casos, o processo pode continuar com o relançamento usando `throw e`. 

---

## 11. `finally` não tem acesso direto à exceção

Dentro do `catch`, você tem a variável da exceção:

```java
catch (Exception e) {
    System.out.println(e.getMessage());
}
```

Mas no `finally`, essa variável não existe diretamente:

```java
finally {
    // aqui não existe "e", a menos que você tenha criado fora
}
```

O PDF reforça que exceções não podem ser relançadas diretamente de um `finally` usando aquela referência do `catch`, porque o `finally` não recebe a referência da exceção; ela é variável local do `catch`. 

---

## 12. Pré-condições e pós-condições

O PDF também comenta que exceções podem ser usadas para validar condições do método. 

## Pré-condição

É algo que precisa ser verdadeiro **antes** do método executar.

Exemplo:

```java
public void setIdade(int idade) {
    if (idade < 0) {
        throw new IllegalArgumentException("Idade não pode ser negativa.");
    }

    this.idade = idade;
}
```

Pré-condição:

```text
idade precisa ser maior ou igual a zero.
```

## Pós-condição

É algo que precisa ser verdadeiro **depois** que o método executa.

Exemplo mental:

```text
Depois de sacar, o saldo não pode ficar negativo.
```

Se ficar, tem erro na lógica.

---

## 13. Exceções personalizadas

Você pode criar suas próprias exceções.

Exemplo simples:

```java
public class SaldoInsuficienteException extends Exception {
    public SaldoInsuficienteException(String mensagem) {
        super(mensagem);
    }
}
```

Uso:

```java
public void sacar(double valor) throws SaldoInsuficienteException {
    if (valor > saldo) {
        throw new SaldoInsuficienteException("Saldo insuficiente.");
    }

    saldo -= valor;
}
```

Como ela herda de `Exception`, é uma exceção que precisa ser tratada ou declarada.

---

## 14. Exceção personalizada unchecked

Você também pode criar uma exceção herdando de `RuntimeException`.

```java
public class IdadeInvalidaException extends RuntimeException {
    public IdadeInvalidaException(String mensagem) {
        super(mensagem);
    }
}
```

Uso:

```java
public void setIdade(int idade) {
    if (idade < 0) {
        throw new IdadeInvalidaException("Idade inválida.");
    }

    this.idade = idade;
}
```

Aqui não é obrigatório declarar `throws`, porque `RuntimeException` é uma exceção implícita/unchecked.

---

## 15. Encadeamento de exceções

Encadear exceção é criar uma exceção nova dizendo qual foi a causa original.

Exemplo:

```java
try {
    int[] numeros = {1, 2, 3};
    System.out.println(numeros[10]);
} catch (ArrayIndexOutOfBoundsException e) {
    throw new RuntimeException("Erro ao acessar o array.", e);
}
```

Aqui:

```java
e
```

é a causa original.

Você pode recuperar com:

```java
getCause()
```

Exemplo:

```java
catch (RuntimeException e) {
    System.out.println(e.getMessage());
    System.out.println(e.getCause());
}
```

O PDF trabalha esse mecanismo de encadeamento e mostra exemplos com `getCause()`, cast explícito e exceções personalizadas. 

---

## 16. `getMessage()`

`getMessage()` pega a mensagem da exceção.

Exemplo:

```java
try {
    throw new ArithmeticException("Divisor nulo.");
} catch (ArithmeticException e) {
    System.out.println(e.getMessage());
}
```

Saída:

```text
Divisor nulo.
```

O material usa isso nos exemplos:

```java
e.getMessage()
```

para imprimir mensagens como erro de divisão. 

---

## 17. `printStackTrace()`

`printStackTrace()` imprime a pilha do erro.

Exemplo:

```java
try {
    int resultado = 10 / 0;
} catch (ArithmeticException e) {
    e.printStackTrace();
}
```

Ele mostra onde o erro aconteceu.

É útil para debug, mas em programa final normalmente você trata melhor a mensagem.

---

## 18. Exemplo completo: divisão segura

```java
public class Calculadora {

    public int dividir(int dividendo, int divisor) {
        try {
            if (divisor == 0) {
                throw new ArithmeticException("Divisor não pode ser zero.");
            }

            return dividendo / divisor;

        } catch (ArithmeticException e) {
            System.out.println("Erro: " + e.getMessage());
            return 0;

        } finally {
            System.out.println("Operação finalizada.");
        }
    }
}
```

Uso:

```java
public class Principal {
    public static void main(String[] args) {
        Calculadora calc = new Calculadora();

        int resultado = calc.dividir(10, 0);

        System.out.println("Resultado: " + resultado);
    }
}
```

Saída:

```text
Erro: Divisor não pode ser zero.
Operação finalizada.
Resultado: 0
```

---

# O que você precisa dominar para a prova

Se você souber isso, está bem no Tema 4:

```text
1. O que é exceção.
2. Diferença entre erro de compilação e erro em tempo de execução.
3. Estrutura try/catch/finally.
4. O que é exception handler.
5. Como múltiplos catch funcionam.
6. Por que catch genérico deve vir depois do específico.
7. Hierarquia Throwable, Error, Exception e RuntimeException.
8. Diferença entre exceções implícitas e explícitas.
9. Usar throw.
10. Usar throws.
11. Diferença entre throw e throws.
12. Propagar exceção.
13. Relançar exceção com throw e.
14. Entender que finally não recebe a variável do catch.
15. Criar exceção personalizada.
16. Usar getMessage().
17. Usar getCause().
18. Entender encadeamento de exceções.
19. Saber quando usar RuntimeException ou Exception.
20. Saber validar pré-condições com exceções.
```

---

# Desafios para você resolver

## Desafio 1 — Divisão segura

Crie uma classe `Calculadora`.

Método:

```java
public int dividir(int a, int b)
```

Regras:

Se `b == 0`, lance `ArithmeticException`.

Capture a exceção com `try/catch`.

Mostre:

```text
Erro: divisão por zero.
```

No `finally`, mostre:

```text
Operação finalizada.
```

---

## Desafio 2 — `throw` na prática

Crie uma classe `Pessoa` com:

```text
nome
idade
```

No setter de idade:

```java
public void setIdade(int idade)
```

Se idade for negativa, lance:

```java
throw new IllegalArgumentException("Idade inválida.");
```

Teste no `main`.

---

## Desafio 3 — `throws`

Crie uma classe `Arranjo` com um vetor:

```java
int[] vetor = {1, 2, 3, 4};
```

Crie o método:

```java
public int getElemento(int indice) throws IllegalAccessException
```

Se o índice for menor que `0` ou maior que `3`, lance:

```java
throw new IllegalAccessException("Índice inválido.");
```

No `main`, chame esse método dentro de um `try/catch`.

---

## Desafio 4 — Múltiplos `catch`

Crie um programa que possa gerar:

```text
ArithmeticException
ArrayIndexOutOfBoundsException
NullPointerException
```

Use três `catch`, um para cada tipo.

Depois adicione um quarto:

```java
catch (Exception e)
```

No final.

Teste a ordem.

---

## Desafio 5 — Exceção personalizada

Crie:

```java
public class SaldoInsuficienteException extends Exception
```

Com construtor:

```java
public SaldoInsuficienteException(String mensagem) {
    super(mensagem);
}
```

Depois crie uma classe `ContaBancaria` com:

```text
titular
saldo
```

Método:

```java
public void sacar(double valor) throws SaldoInsuficienteException
```

Se o valor for maior que o saldo, lance sua exceção personalizada.

---

## Desafio 6 — Exceção unchecked personalizada

Crie:

```java
public class ProdutoInvalidoException extends RuntimeException
```

Depois crie uma classe `Produto` com:

```text
nome
preco
```

Se `preco <= 0`, lance `ProdutoInvalidoException`.

Observe que, por herdar de `RuntimeException`, você não é obrigado a usar `throws`.

---

## Desafio 7 — Relançamento

Crie uma classe `Calculadora`.

Método:

```java
public int dividir(int a, int b) throws ArithmeticException
```

Dentro do método:

1. Faça `try/catch`.
2. Se `b == 0`, lance `ArithmeticException`.
3. No `catch`, mostre uma mensagem.
4. Depois faça:

```java
throw e;
```

No `main`, trate essa exceção de novo.

Objetivo: entender relançamento.

---

## Desafio 8 — Encadeamento de exceções

Crie um método que tente acessar uma posição inválida de array.

Capture:

```java
ArrayIndexOutOfBoundsException
```

Depois lance:

```java
throw new RuntimeException("Erro ao processar array.", e);
```

No `main`, capture `RuntimeException` e imprima:

```java
e.getMessage()
e.getCause()
```

---

## Desafio 9 — `finally` e fechamento de recurso

Crie um programa com `Scanner`.

Use:

```java
try
catch
finally
```

No `finally`, feche o scanner:

```java
scanner.close();
```

Objetivo: entender que `finally` é usado para liberar recursos.

---

## Desafio 10 — Projeto chefão do Tema 4

Crie um mini sistema chamado:

```text
SistemaBancoComExcecoes
```

Classes:

```text
ContaBancaria
SaldoInsuficienteException
ValorInvalidoException
Principal
```

Regras:

`ContaBancaria` tem `titular`, `saldo` e `numero`.

Métodos:

```java
depositar(double valor)
sacar(double valor)
transferir(ContaBancaria destino, double valor)
exibirDados()
```

Validações:

```text
Não permitir depósito negativo.
Não permitir saque negativo.
Não permitir saque maior que o saldo.
Não permitir transferência para conta nula.
```

Obrigatório usar:

```text
try
catch
finally
throw
throws
exceção personalizada checked
exceção personalizada unchecked
getMessage()
relançamento em pelo menos um método
```
