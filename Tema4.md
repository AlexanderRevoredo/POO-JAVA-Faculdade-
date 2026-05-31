# Tratamento de Exceções em Java

## 1. O que é uma exceção?

Uma exceção é um **evento em tempo de execução** que interrompe o fluxo normal do programa. Em Java, ao ocorrer uma exceção, é criado um *exception object* que é entregue à JVM.

### Fluxo do mecanismo

1. Exceção ocorre → objeto criado
2. Objeto lançado à JVM (*lançamento de exceção*)
3. JVM busca um *exception handler* na call stack
4. Handler adequado encontrado → exceção capturada
5. Nenhum handler encontrado → programa encerrado

### Vantagens do mecanismo

- Separa o código de tratamento de erros do código funcional
- Propaga o erro diretamente ao método interessado na pilha de chamadas
- Agrupa e diferencia tipos de erros via hierarquia de classes

---

## 2. Hierarquia de classes

```
Object
  └── Throwable
        ├── Error
        │     ├── AWTError
        │     ├── VirtualMachineError
        │     │     ├── OutOfMemoryError
        │     │     ├── StackOverflowError
        │     │     └── UnknownError
        │     └── ThreadDeath
        └── Exception
              ├── IOException
              │     └── FileNotFoundException
              └── RuntimeException
                    ├── ArithmeticException
                    ├── ArrayIndexOutOfBoundsException
                    ├── ClassCastException
                    ├── InputMismatchException
                    └── NullPointerException
```

### Error

Agrupa exceções que, em situações normais, **não precisam ser capturadas** pelo programa. São erros graves relacionados ao ambiente de execução da JVM. Exemplos: `StackOverflowError`, `OutOfMemoryError`.

### Exception

Agrupa exceções que os programas **devem capturar**. Permite extensão pelo programador para criar exceções próprias. Subclasse importante: `RuntimeException`, que cobre divisão por zero, acesso a índice inválido de array, etc.

---

## 3. Exceções implícitas vs explícitas

### Implícitas (unchecked)

- Definidas em `Error` e `RuntimeException` e suas subclasses
- Lançadas **automaticamente pela JVM**
- **Não é obrigatório** capturá-las ou declará-las com `throws`
- São chamadas *contornáveis* — mas ignorá-las pode causar saída anormal
- Exemplo: `ArithmeticException` ao dividir por zero

### Explícitas (checked)

- Tudo que **não é implícita**
- São *incontornáveis*: o método deve capturá-las com `try-catch` **ou** declará-las com `throws` na assinatura
- Não listar exceções explícitas não tratadas gera **erro de compilação**
- Exemplos: `IOException`, `IllegalAccessException`

---

## 4. Estrutura try-catch-finally

```java
try {
    // bloco monitorado
} catch (ExcecaoTipo1 e) {
    // trata tipo 1
} catch (ExcecaoTipo2 e) {
    // trata tipo 2
    // ATENÇÃO: subclasses SEMPRE antes de superclasses!
} finally {
    // SEMPRE executado — ideal para liberar recursos
}
```

> ⚠️ **Regra importante:** subclasses devem vir antes da superclasse nos blocos `catch`. Um `catch (Exception e)` no topo captura tudo e os blocos abaixo nunca serão alcançados.

### Comportamento do `finally`

| Situação | `catch` executado? | `finally` executado? |
|---|---|---|
| Nenhuma exceção lançada | Não | ✅ Sim |
| Exceção lançada e capturada | ✅ Sim | ✅ Sim |
| Exceção lançada e não capturada | Não | ✅ Sim |
| `System.exit()` chamado | Não | ❌ Não |

---

## 5. Comandos principais

### `throw`

Lança uma exceção explicitamente. O objeto deve ser `Throwable` ou subclasse. Causa desvio imediato — nenhuma instrução seguinte é executada.

```java
if (divisor == 0)
    throw new ArithmeticException("Divisor nulo.");
```

### `throws`

Declara na **assinatura do método** quais exceções explícitas ele pode lançar sem tratar. Obriga o chamador a lidar com elas. Não é necessário para exceções implícitas.

```java
public int metodo(int arg) throws IOException, IllegalAccessException {
    // ...
}
```

### `finally`

Executado **sempre** após o bloco `try`, mesmo com `return`, `break` ou `continue`. Ideal para liberar recursos e evitar vazamentos (conexões abertas, arquivos não fechados, dispositivos não liberados).

```java
Scanner s = new Scanner(System.in);
try {
    // operações
} catch (Exception e) {
    System.out.println("Erro: " + e.getMessage());
} finally {
    s.close(); // sempre fechará o scanner
}
```

---

## 6. Criando exceções personalizadas

Para criar uma exceção customizada, estenda uma classe de exceção existente. Isso lega à nova classe o mecanismo de manipulação de exceções.

### Os 4 construtores padrão

1. Sem argumentos — passa mensagem padrão para a superclasse
2. Recebe `String` com mensagem personalizada
3. Recebe `String` + objeto `Throwable` (para encadeamento)
4. Recebe apenas `Throwable` (para encadeamento)

### Exemplo

```java
public class ErroValidacaoCNPJ extends Throwable {
    private String msg_erro;

    ErroValidacaoCNPJ(String msg_erro) {
        this.msg_erro = msg_erro;
    }

    @Override
    public String toString() {
        return "ErroValidacaoCNPJ: " + msg_erro;
    }
}
```

> 💡 **Boa prática:** ao criar uma classe de exceção, considere sempre sobrescrever o método `toString()` para melhorar a legibilidade das mensagens de erro.

---

## 7. Encadeamento de exceções

Ocorre quando **uma exceção causa outra**. Preservar essa relação é essencial para depuração — sem ela, o contexto original do erro se perde.

### Como fazer

```java
// Pelo construtor (recomendado)
ErroValidacao erro = new ErroValidacao("Mensagem", excecaoOriginal);

// Ou após a criação
ErroValidacao erro = new ErroValidacao("Mensagem");
erro.initCause(excecaoOriginal);

// Para recuperar a causa
Throwable causa = erro.getCause();
```

### Métodos da classe `Throwable`

| Método | Descrição |
|---|---|
| `getCause()` | Retorna a exceção que originou esta, ou `null` |
| `initCause(Throwable)` | Associa uma exceção causadora após a criação |
| `getMessage()` | Retorna a mensagem de erro |
| `printStackTrace()` | Imprime o rastro completo da pilha de execução |

> ⚠️ Uma vez estabelecida a associação (via construtor ou `initCause`), ela **não pode ser modificada**. Não chame `initCause` duas vezes nem após usar o construtor com `Throwable`.

---

## 8. Notificação, lançamento e relançamento

### Notificação

Informar o chamador via `throws` na assinatura do método. Obrigatória para explícitas; opcional para implícitas.

```java
public void metodo() throws MinhaExcecao {
    // ...
}
```

### Lançamento

Pode ser **implícito** (pela JVM) ou **explícito** (com `throw`). Ao usar `throw` no fluxo principal do método sem um `if` ou estrutura de desvio, todo código abaixo se torna inatingível — erro de compilação.

```java
// CORRETO — lançamento em fluxo alternativo
if (condicao)
    throw new MinhaExcecao("msg");

// ERRADO — código após throw é inatingível
throw new MinhaExcecao("msg");
int x = 1; // erro de compilação!
```

### Relançamento

Dentro de um `catch`, usa `throw e` para repassar a exceção capturada a um bloco mais externo. Permite tratamento parcial ou postergado.

```java
catch (Exception e) {
    System.out.println("Tratamento parcial...");
    throw e; // relança para o chamador
}
```

> ⚠️ Não é possível relançar de um bloco `finally`, pois a referência ao objeto exceção não está disponível nesse escopo.

---

## 9. Exemplo completo

```java
public class Calculadora {

    public int divisao(int dividendo, int divisor) throws ArithmeticException {
        if (divisor == 0)
            throw new ArithmeticException("Divisor nulo.");
        return dividendo / divisor;
    }
}

public class Principal {

    public static void main(String[] args) {
        Calculadora calc = new Calculadora();
        Scanner s = new Scanner(System.in);

        try {
            int resultado = calc.divisao(10, 0);
            System.out.println("Resultado: " + resultado);
        } catch (ArithmeticException e) {
            System.out.println("ERRO: " + e.getMessage());
        } finally {
            s.close();
            System.out.println("Recursos liberados.");
        }
    }
}
```

---

## 10. Resumo rápido

| Conceito | Resumo |
|---|---|
| Exceção | Evento em tempo de execução que interrompe o fluxo normal |
| `Error` | Erros graves da JVM — normalmente não capturados |
| `Exception` | Erros que o programa deve tratar |
| Implícita | `Error` + `RuntimeException` — não obriga tratamento |
| Explícita | Demais exceções — obriga `try-catch` ou `throws` |
| `try` | Delimita o bloco monitorado |
| `catch` | Captura e trata a exceção |
| `finally` | Sempre executado — libera recursos |
| `throw` | Lança uma exceção manualmente |
| `throws` | Declara exceções não tratadas na assinatura do método |
| Encadeamento | Associa uma exceção à que a causou |
| Relançamento | `throw e` dentro de `catch` para repassar a exceção |

---

*Referências: Deitel & Deitel, Java How to Program, 11ª ed. | Oracle Java Documentation*
