# Tema 3 — Polimorfismo em Java

## 1. Ideia central do tema

O Tema 1 te deu a base de classe/objeto.
O Tema 2 aprofundou herança.
O **Tema 3 mostra como usar herança e interfaces para criar código flexível**.

A palavra-chave aqui é:

```text
Contrato
```

Ou seja: você define um comportamento esperado e deixa as classes concretas implementarem esse comportamento do jeito delas.

Exemplo mental:

```java
Animal a = new Cachorro();
Animal b = new Gato();

a.emitirSom(); // Latir
b.emitirSom(); // Miar
```

A variável é do tipo geral `Animal`, mas o comportamento executado é o da classe real: `Cachorro` ou `Gato`.

Isso é polimorfismo.

---

# 2. Polimorfismo

Polimorfismo significa “muitas formas”.

Na prática, em Java, significa que uma referência de tipo mais geral pode apontar para objetos de tipos mais específicos.

Exemplo:

```java
Pessoa p1 = new Fisica();
Pessoa p2 = new Juridica();
```

`p1` é declarado como `Pessoa`, mas o objeto real é `Fisica`.
`p2` é declarado como `Pessoa`, mas o objeto real é `Juridica`.

Quando você chama um método sobrescrito:

```java
p1.atualizarID("12345678901");
p2.atualizarID("12345678000199");
```

Java decide em tempo de execução qual método usar. O PDF chama isso de **vinculação dinâmica**, quando o sistema identifica o tipo real do objeto e executa o método adequado. 

---

# 3. Tipo estático e tipo dinâmico

Esse ponto é essencial.

```java
Pessoa p = new Aluno();
```

Aqui:

```text
Tipo estático: Pessoa
Tipo dinâmico: Aluno
```

O **tipo estático** é o tipo da variável:

```java
Pessoa p
```

O **tipo dinâmico** é o tipo real do objeto:

```java
new Aluno()
```

O compilador olha para o tipo estático para saber o que você pode chamar.

Exemplo:

```java
Pessoa p = new Aluno();

p.apresentar(); // pode, se Pessoa tiver apresentar()
p.getMatricula(); // erro, se getMatricula() só existir em Aluno
```

Mesmo que o objeto real seja `Aluno`, a variável `p` é vista como `Pessoa`.

---

# 4. Classe abstrata

Uma **classe abstrata** é uma classe que **não pode ser instanciada diretamente**.

Exemplo:

```java
public abstract class Pessoa {
}
```

Você não pode fazer:

```java
Pessoa p = new Pessoa(); // erro
```

A classe abstrata serve como base/modelo para outras classes.

O PDF explica que uma classe abstrata existe para fornecer uma interface comum e comportamentos comuns para as subclasses. Ela impede que você instancie uma classe genérica demais, como `Pessoa`, quando no sistema real você deveria usar algo mais específico, como `Fisica` ou `Juridica`. 

---

# 5. Método abstrato

Um **método abstrato** é um método sem corpo.

Exemplo:

```java
public abstract void atualizarID(String identificador);
```

Ele termina com `;`, não tem `{ }`.

Errado:

```java
public abstract void atualizarID(String identificador) {
}
```

Certo:

```java
public abstract void atualizarID(String identificador);
```

Se uma classe tem pelo menos um método abstrato, a classe também precisa ser abstrata:

```java
public abstract class Pessoa {
    public abstract void atualizarID(String identificador);
}
```

O PDF reforça que um método abstrato não possui implementação e sua implementação fica “postergada” para uma subclasse concreta. Se a subclasse não implementar o método abstrato, ela também continua abstrata. 

---

# 6. Método concreto

Método concreto é o método normal, com corpo.

Exemplo:

```java
public String recuperarID() {
    return identificador;
}
```

Uma classe abstrata pode ter:

```text
atributos
construtores
métodos concretos
métodos abstratos
```

Exemplo:

```java
public abstract class Pessoa {
    protected String identificador;

    public abstract void atualizarID(String identificador);

    public String recuperarID() {
        return identificador;
    }
}
```

Aqui:

```java
atualizarID()
```

é abstrato.

```java
recuperarID()
```

é concreto.

---

# 7. Classe concreta

Uma classe concreta é uma classe que pode ser instanciada.

Exemplo:

```java
public class Fisica extends Pessoa {
    @Override
    public void atualizarID(String cpf) {
        this.identificador = cpf;
    }
}
```

Agora pode:

```java
Pessoa p = new Fisica();
```

Não porque `Pessoa` foi instanciada, mas porque `Fisica` foi instanciada e guardada em uma referência do tipo `Pessoa`.

---

# 8. Exemplo prático com classe abstrata

```java
public abstract class Animal {
    public abstract void emitirSom();

    public void dormir() {
        System.out.println("Zzzz...");
    }
}
```

```java
public class Cachorro extends Animal {
    @Override
    public void emitirSom() {
        System.out.println("Latir!");
    }
}
```

```java
public class Gato extends Animal {
    @Override
    public void emitirSom() {
        System.out.println("Miar!");
    }
}
```

Uso:

```java
public class Main {
    public static void main(String[] args) {
        Animal cachorro = new Cachorro();
        Animal gato = new Gato();

        cachorro.emitirSom();
        cachorro.dormir();

        gato.emitirSom();
        gato.dormir();
    }
}
```

Saída:

```text
Latir!
Zzzz...
Miar!
Zzzz...
```

Esse exemplo mistura:

```text
herança
polimorfismo
classe abstrata
método abstrato
método concreto
@Override
```

---

# 9. `abstract` versus `final`

Esses dois são quase opostos.

## `abstract`

Significa:

```text
Ainda precisa ser completado por uma subclasse.
```

Exemplo:

```java
public abstract void emitirSom();
```

## `final`

Significa:

```text
Não pode ser alterado/sobrescrito/herdado, dependendo do caso.
```

O PDF explica que um método `final` não pode ser redefinido nas subclasses. Também explica que uma classe `final` não pode ter subclasses, e uma variável `final` não pode receber outro valor depois de inicializada. 

---

# 10. `final` em variável

Exemplo:

```java
final int DIAS_LETIVOS = 200;
```

Depois disso, não pode:

```java
DIAS_LETIVOS = 180; // erro
```

Com objeto, cuidado:

```java
final Escola escola = new Escola();
```

Isso significa que a referência `escola` não pode apontar para outro objeto.

Não pode:

```java
escola = new Escola(); // erro
```

Mas o objeto ainda pode mudar internamente, se tiver métodos para isso:

```java
escola.setNome("Nova Escola"); // pode, dependendo da classe
```

O PDF chama atenção justamente para isso: se a variável de referência é `final`, a referência não muda, mas isso não significa necessariamente que o objeto virou imutável. 

---

# 11. `final` em método

```java
public final String recuperarID() {
    return identificador;
}
```

Uma subclasse não pode fazer:

```java
@Override
public String recuperarID() {
    return "outro valor";
}
```

Isso dá erro.

Use `final` quando você quer proteger um comportamento da classe pai para que ninguém sobrescreva depois.

---

# 12. `final` em classe

```java
public final class Utilitario {
}
```

Agora ninguém pode fazer:

```java
public class MinhaClasse extends Utilitario {
}
```

Dá erro.

Classe `final` não pode ser herdada.

---

# 13. Upcasting

**Upcasting** é converter uma referência de subclasse para superclasse.

Exemplo:

```java
Aluno aluno = new Aluno();
Pessoa pessoa = aluno;
```

Ou direto:

```java
Pessoa pessoa = new Aluno();
```

Isso é seguro porque:

```text
Aluno é uma Pessoa.
```

Então todo `Aluno` pode ser tratado como `Pessoa`.

---

# 14. Downcasting

**Downcasting** é o caminho contrário: pegar uma referência de superclasse e tentar tratá-la como subclasse.

Exemplo:

```java
Pessoa pessoa = new Aluno();

Aluno aluno = (Aluno) pessoa;
```

Aqui funciona porque o objeto real realmente é `Aluno`.

Mas isso aqui é perigoso:

```java
Pessoa pessoa = new PessoaFisica();

Aluno aluno = (Aluno) pessoa; // pode dar erro em tempo de execução
```

Antes de fazer downcasting, é bom verificar:

```java
if (pessoa instanceof Aluno) {
    Aluno aluno = (Aluno) pessoa;
}
```

O PDF explica que downcasting faz o compilador reinterpretar uma referência da superclasse como sendo da subclasse; já o upcasting reinterpreta uma referência da subclasse como superclasse. 

---

# 15. Interface

Interface é um **contrato**.

Ela diz:

```text
Quem implementar essa interface precisa ter estes métodos.
```

Exemplo:

```java
public interface Identificavel {
    void atualizarID(String identificador);
    String recuperarID();
}
```

Uma classe implementa interface com `implements`:

```java
public class Pessoa implements Identificavel {
    private String identificador;

    @Override
    public void atualizarID(String identificador) {
        this.identificador = identificador;
    }

    @Override
    public String recuperarID() {
        return identificador;
    }
}
```

O PDF reforça que, quando uma classe implementa uma ou mais interfaces, ela deve implementar todos os métodos abstratos dessas interfaces. Também destaca que os métodos da interface são públicos, mesmo quando você omite o `public`. 

---

# 16. Interface versus classe abstrata

Essa comparação cai fácil.

## Classe abstrata

Pode ter:

```text
atributos de instância
construtor
métodos concretos
métodos abstratos
estado interno
```

Exemplo:

```java
public abstract class Pessoa {
    protected String nome;

    public Pessoa(String nome) {
        this.nome = nome;
    }

    public abstract void apresentar();
}
```

## Interface

É mais focada em contrato.

Exemplo:

```java
public interface Autenticavel {
    boolean autenticar(String senha);
}
```

Uma classe pode herdar só de uma classe:

```java
class Aluno extends Pessoa
```

Mas pode implementar várias interfaces:

```java
class Aluno extends Pessoa implements Autenticavel, Identificavel
```

Resumo:

```text
abstract class = base com possível estado e comportamento comum
interface = contrato de comportamento
```

---

# 17. Implementando múltiplas interfaces

Java não permite herança múltipla de classes:

```java
class C extends A, B // erro
```

Mas permite múltiplas interfaces:

```java
public class Pessoa implements Identificavel, Autenticavel {
}
```

Exemplo:

```java
public interface Identificavel {
    String getIdentificador();
}
```

```java
public interface Autenticavel {
    boolean autenticar(String senha);
}
```

```java
public class Usuario implements Identificavel, Autenticavel {
    private String id;
    private String senha;

    public Usuario(String id, String senha) {
        this.id = id;
        this.senha = senha;
    }

    @Override
    public String getIdentificador() {
        return id;
    }

    @Override
    public boolean autenticar(String senha) {
        return this.senha.equals(senha);
    }
}
```

---

# 18. Interface com método sem corpo

No estilo básico, método de interface fica assim:

```java
public interface Teste {
    void executar();
}
```

Não assim:

```java
public interface Teste {
    void executar() {
    }
}
```

Esse segundo exemplo dá erro se você estiver tentando declarar um método abstrato comum. O PDF mostra uma questão em que a correção é remover o corpo `{ }` do método da interface e trocar por `;`. 

Observação importante: em Java moderno existem métodos `default`, `static` e `private` em interfaces, que podem ter corpo. Mas para o básico da prova, guarde:

```text
método abstrato de interface termina com ;
```

---

# 19. Interface e Collections

Um exemplo real de interface em Java é:

```java
List
```

`List` é uma interface.
`ArrayList` é uma classe concreta que implementa `List`.

Exemplo:

```java
List<String> nomes = new ArrayList<>();
```

Aqui você programa contra a interface `List`, não contra a implementação concreta `ArrayList`.

Isso deixa o código mais flexível.

Depois você poderia trocar:

```java
List<String> nomes = new LinkedList<>();
```

sem mudar o resto do código que usa `nomes.add()`, `nomes.get()`, `nomes.remove()` etc.

O PDF usa justamente o Framework Collections como exemplo real de interface, destacando `List` como interface e `ArrayList` como classe concreta que implementa seus métodos. 

---

# 20. Interface funcional

Uma **interface funcional** é uma interface que possui **apenas um método abstrato**.

Ela também é chamada de interface **SAM**:

```text
Single Abstract Method
```

Exemplo:

```java
@FunctionalInterface
public interface Operacao {
    int executar(int x);
}
```

Tem só um método abstrato:

```java
int executar(int x);
```

Por isso pode ser usada com lambda.

O PDF explica que uma interface com apenas um método abstrato é uma interface funcional/SAM, e cita `Comparator` e `Runnable` como exemplos comuns na API Java. 

---

# 21. `@FunctionalInterface`

Essa annotation não é obrigatória, mas é útil.

```java
@FunctionalInterface
public interface Operacao {
    int executar(int x);
}
```

Ela diz ao compilador:

```text
Verifique se essa interface realmente tem só um método abstrato.
```

Se você tentar colocar dois métodos abstratos:

```java
@FunctionalInterface
public interface Operacao {
    int executar(int x);
    int calcular(int y);
}
```

Dá erro.

O PDF explica que `@FunctionalInterface` instrui o compilador a verificar o critério SAM e também comunica ao programador que aquela interface foi criada intencionalmente para ter um único método abstrato. 

---

# 22. Lambda

Lambda é uma forma curta de implementar uma interface funcional.

Exemplo sem lambda:

```java
Operacao dobro = new Operacao() {
    @Override
    public int executar(int x) {
        return x * 2;
    }
};
```

Com lambda:

```java
Operacao dobro = x -> x * 2;
```

Uso:

```java
System.out.println(dobro.executar(5));
```

Saída:

```text
10
```

Outro exemplo:

```java
@FunctionalInterface
interface Conversor {
    double converter(double valor);
}
```

```java
Conversor celsiusParaFahrenheit = c -> (c * 9 / 5) + 32;

System.out.println(celsiusParaFahrenheit.converter(30));
```

Saída:

```text
86.0
```

O PDF usa um exemplo de programação funcional com conversão de Celsius para Fahrenheit e destaca a expressão lambda no código. 

---

# 23. `Comparator` como interface funcional

`Comparator` é uma interface funcional muito usada para ordenar.

Exemplo:

```java
List<String> nomes = new ArrayList<>();

nomes.add("Carlos");
nomes.add("Ana");
nomes.add("Bruno");

nomes.sort((a, b) -> a.compareToIgnoreCase(b));

System.out.println(nomes);
```

Saída:

```text
[Ana, Bruno, Carlos]
```

Aqui:

```java
(a, b) -> a.compareToIgnoreCase(b)
```

é uma lambda que implementa o método de comparação.

---

# 24. `Runnable` como interface funcional

`Runnable` também é interface funcional.

Exemplo:

```java
Runnable tarefa = () -> {
    System.out.println("Executando tarefa...");
};

tarefa.run();
```

Saída:

```text
Executando tarefa...
```

Isso vai voltar com força no Tema 5, quando você estudar threads.

---

# 25. Interface aninhada

O material também toca em interfaces como membros internos/aninhados.

Exemplo simples:

```java
public class Sistema {
    interface Imprimivel {
        void imprimir();
    }
}
```

Isso é uma interface declarada dentro de uma classe.

Dependendo do modificador de acesso (`public`, `private`, `protected`, default), ela pode ou não ser acessada fora daquela classe/pacote/hierarquia.

Esse ponto é mais avançado, mas a ideia é: interfaces também podem ser membros de outras estruturas, e os modificadores de acesso afetam sua visibilidade.

---

# 26. O que você precisa dominar para a prova

Se você souber isso, está bem no Tema 3:

```text
1. Explicar polimorfismo.
2. Diferenciar tipo estático e tipo dinâmico.
3. Entender vinculação dinâmica.
4. Criar classe abstrata com abstract.
5. Saber que classe abstrata não pode ser instanciada.
6. Criar método abstrato sem corpo.
7. Saber que subclasse concreta deve implementar método abstrato.
8. Diferenciar método abstrato e método concreto.
9. Usar @Override.
10. Entender final em variável, método e classe.
11. Fazer upcasting.
12. Fazer downcasting com instanceof.
13. Criar interface.
14. Implementar interface com implements.
15. Saber que interface é contrato.
16. Diferenciar interface de classe abstrata.
17. Implementar múltiplas interfaces.
18. Saber que método abstrato de interface termina com ;
19. Entender List como interface e ArrayList como implementação.
20. Entender interface funcional/SAM.
21. Usar @FunctionalInterface.
22. Fazer lambda simples.
23. Reconhecer Comparator e Runnable como interfaces funcionais.
```

---

# Desafios para você resolver

## Desafio 1 — Classe abstrata básica

Crie uma classe abstrata:

```text
Animal
```

Com:

```java
public abstract void emitirSom();
public void dormir()
```

Depois crie:

```text
Cachorro
Gato
Leao
```

Regras:

`Cachorro` imprime `"Latir!"`.
`Gato` imprime `"Miar!"`.
`Leao` imprime `"Rugir!"`.
Todos devem herdar de `Animal`.
No `main`, crie:

```java
Animal a1 = new Cachorro();
Animal a2 = new Gato();
Animal a3 = new Leao();
```

E chame:

```java
emitirSom()
dormir()
```

---

## Desafio 2 — Polimorfismo com array

Usando o desafio anterior, crie:

```java
Animal[] animais = new Animal[3];
```

Coloque dentro:

```java
new Cachorro()
new Gato()
new Leao()
```

Depois faça um `for`:

```java
for (Animal animal : animais) {
    animal.emitirSom();
}
```

Objetivo: ver polimorfismo acontecendo em sequência.

---

## Desafio 3 — Classe abstrata Pessoa

Crie:

```text
Pessoa
Fisica
Juridica
```

`Pessoa` deve ser abstrata e ter:

```text
nome
identificador
```

Métodos:

```java
public abstract void atualizarID(String identificador);
public String recuperarID()
public String getNome()
```

`Fisica` valida CPF com 11 dígitos.
`Juridica` valida CNPJ com 14 dígitos.

No `main`:

```java
Pessoa p1 = new Fisica("Alex");
Pessoa p2 = new Juridica("Empresa XPTO");
```

Chame:

```java
p1.atualizarID("12345678901");
p2.atualizarID("12345678000199");
```

---

## Desafio 4 — `final`

Crie uma classe `Escola` com:

```java
private final String codigo;
private String nome;
```

Regras:

`codigo` é definido no construtor e nunca muda.
`nome` pode mudar com setter.
Tente alterar `codigo` depois e veja o erro.

Depois crie um método:

```java
public final String getCodigo()
```

Tente sobrescrever esse método em uma subclasse e veja o erro.

---

## Desafio 5 — Upcasting e downcasting

Crie:

```text
Pessoa
Aluno
Professor
```

Depois faça:

```java
Pessoa p = new Aluno("Alex", "A001");
```

Tente chamar:

```java
p.getMatricula();
```

Veja o erro.

Depois faça:

```java
if (p instanceof Aluno) {
    Aluno aluno = (Aluno) p;
    System.out.println(aluno.getMatricula());
}
```

Explique em comentário:

```java
// Por que precisei fazer downcasting?
```

---

## Desafio 6 — Interface simples

Crie a interface:

```java
public interface Autenticavel {
    boolean autenticar(String senha);
}
```

Crie as classes:

```text
Usuario
Administrador
```

As duas implementam `Autenticavel`.

Regras:

`Usuario` autentica com uma senha simples.
`Administrador` autentica com senha + código extra.

No `main`:

```java
Autenticavel a1 = new Usuario(...);
Autenticavel a2 = new Administrador(...);
```

Chame:

```java
a1.autenticar("123");
a2.autenticar("123");
```

---

## Desafio 7 — Múltiplas interfaces

Crie as interfaces:

```java
interface Identificavel {
    String getIdentificador();
}
```

```java
interface Imprimivel {
    void imprimir();
}
```

Crie a classe:

```java
class Produto implements Identificavel, Imprimivel
```

`Produto` deve ter:

```text
codigo
nome
preco
```

Implemente os dois contratos.

---

## Desafio 8 — Interface versus classe abstrata

Crie:

```text
Funcionario
Professor
Diretor
```

`Funcionario` deve ser uma classe abstrata com:

```text
nome
salario
```

E método abstrato:

```java
public abstract double calcularBonus();
```

Agora crie uma interface:

```java
interface Autenticavel {
    boolean autenticar(String senha);
}
```

`Diretor` deve herdar de `Funcionario` e implementar `Autenticavel`.

`Professor` só herda de `Funcionario`.

Objetivo: treinar:

```text
extends
implements
abstract
@Override
polimorfismo
```

---

## Desafio 9 — List e ArrayList

Faça:

```java
List<String> nomes = new ArrayList<>();
```

Adicione nomes, remova nomes e imprima.

Depois troque:

```java
ArrayList<String> nomes = new ArrayList<>();
```

por:

```java
List<String> nomes = new ArrayList<>();
```

Responda:

```java
// Por que é melhor declarar como List?
```

---

## Desafio 10 — Interface funcional

Crie:

```java
@FunctionalInterface
interface Operacao {
    int executar(int x);
}
```

No `main`, crie lambdas:

```java
Operacao dobrar = x -> x * 2;
Operacao quadrado = x -> x * x;
Operacao triplo = x -> x * 3;
```

Teste:

```java
System.out.println(dobrar.executar(5));
System.out.println(quadrado.executar(5));
System.out.println(triplo.executar(5));
```

---

## Desafio 11 — Conversor com lambda

Crie:

```java
@FunctionalInterface
interface Conversor {
    double converter(double valor);
}
```

Depois crie:

```java
Conversor celsiusParaFahrenheit = c -> (c * 9 / 5) + 32;
Conversor kmParaMilhas = km -> km * 0.621371;
```

Teste os dois.

---

## Desafio 12 — Comparator com lambda

Crie uma classe:

```text
Aluno
```

Com:

```text
nome
nota
```

Crie uma lista de alunos.

Ordene por nome:

```java
alunos.sort((a, b) -> a.getNome().compareToIgnoreCase(b.getNome()));
```

Depois ordene por nota:

```java
alunos.sort((a, b) -> Double.compare(a.getNota(), b.getNota()));
```

Objetivo: entender `Comparator` como interface funcional.

---

## Desafio 13 — Projeto chefão do Tema 3

Crie um projeto:

```text
SistemaPolimorfismoEscola
```

Classes/interfaces obrigatórias:

```text
Pessoa
Aluno
Funcionario
Professor
Diretor
Autenticavel
Identificavel
Imprimivel
Escola
Principal
```

Regras:

`Pessoa` deve ser abstrata.
`Pessoa` tem `nome` e `identificador`.
`Pessoa` tem método abstrato `apresentar()`.
`Aluno` herda de `Pessoa`.
`Funcionario` herda de `Pessoa` e é abstrata.
`Professor` herda de `Funcionario`.
`Diretor` herda de `Funcionario` e implementa `Autenticavel`.
`Identificavel` exige `getIdentificador()`.
`Imprimivel` exige `imprimir()`.
`Escola` guarda `List<Pessoa> pessoas`.
No `main`, crie uma lista com `Aluno`, `Professor` e `Diretor`.
Percorra a lista chamando `apresentar()`.

Obrigatório usar:

```text
abstract
extends
implements
@Override
final
upcasting
downcasting
interface
List
ArrayList
lambda ou Comparator
```
