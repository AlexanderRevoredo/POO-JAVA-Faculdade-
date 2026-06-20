# Tema 1 — Introdução à programação OO em Java

## 1. Ideia central da POO

POO significa **Programação Orientada a Objetos**. A ideia é organizar o programa em “coisas” que têm:

**Estado** → atributos.
**Comportamento** → métodos.
**Identidade** → cada objeto criado na memória é uma instância própria.

Exemplo mental:

```java
Aluno aluno1 = new Aluno("Alex", 25);
```

`Aluno` é a classe.
`aluno1` é uma referência para um objeto.
`new Aluno(...)` cria o objeto na memória.

O PDF explica que a POO surgiu para melhorar **manutenibilidade**, **reaproveitamento de código** e reduzir a complexidade que crescia nos programas estruturados. 

---

## 2. Classe

Uma **classe** é um modelo para criar objetos. Ela define dados, métodos e o mecanismo de instanciação dos objetos. O material mostra a classe como uma estrutura formada por **dados**, **métodos que operam sobre esses dados** e **mecanismo de instanciação**. 

Exemplo:

```java
public class Aluno {
    private String nome;

    public void inserirNome(String nn) {
        nome = nn;
    }

    public String recuperarNome() {
        return nome;
    }
}
```

Aqui:

`Aluno` = classe.
`nome` = atributo.
`inserirNome` = método que altera o estado.
`recuperarNome` = método que retorna o estado.
`private` = só a classe acessa diretamente.
`public` = qualquer parte do programa pode acessar.

Também tem um detalhe importante: **em Java, uma classe pública deve ficar em um arquivo com o mesmo nome da classe**, por exemplo `Aluno.java`. 

---

## 3. Declaração de classe e modificadores

A estrutura geral apresentada no PDF é mais ou menos:

```java
[Modificador] class NomeDaClasse [extends Superclasse] [implements Interface] {
    // corpo da classe
}
```

A forma mais simples possível é:

```java
class ClasseSimples {
}
```

Os modificadores citados no tema são:

```java
public
protected
private
abstract
static
final
strictfp
```

E também aparecem as **annotations**, que são aquelas marcações com `@`, como:

```java
@Override
@Deprecated
@SuppressWarnings
```

Resumo prático:

`public` → visível para fora.
`private` → visível só dentro da própria classe.
`protected` → visível para herança/pacote.
`abstract` → classe ou método incompleto, usado para especialização depois.
`final` → impede alteração/herança dependendo do contexto.
`static` → pertence à classe, não a um objeto específico.
`strictfp` → relacionado a cálculos de ponto flutuante independentes da plataforma.
`@Annotation` → metadado/etiqueta que o compilador ou framework interpreta. 

Coisa de prova: Java é **case sensitive**. Então:

```java
class
```

é diferente de:

```java
Class
```

Por isso `public class Aluno {}` é válido, mas `Public class Aluno {}` não é. 

---

## 4. Objeto e instanciação

A classe é o molde. O objeto é o produto criado a partir do molde.

Exemplo:

```java
Aluno objetoAluno = new Aluno();
```

Isso pode ser separado em duas partes:

```java
Aluno objetoAluno;
objetoAluno = new Aluno();
```

O PDF destaca que a criação do objeto envolve reservar espaço em memória e executar um método especial chamado **construtor**. 

---

## 5. Construtor

O **construtor** é um método especial que roda quando o objeto nasce.

Regras principais:

O construtor tem o **mesmo nome da classe**.
Construtor **não tem tipo de retorno**, nem `void`.
Ele é chamado com `new`.
Ele inicializa o objeto.

Exemplo:

```java
public class Aluno {
    private String nome;
    private int idade;

    public Aluno(String nome, int idade) {
        this.nome = nome;
        this.idade = idade;
    }
}
```

Aqui:

```java
this.nome = nome;
```

significa: o atributo `nome` do objeto recebe o parâmetro `nome`.

O PDF usa um exemplo com `Aluno`, `nome`, `idade`, `codigo_identificador` e `Random`, mostrando que o construtor pode receber parâmetros para definir o estado inicial do objeto. 

---

## 6. Escopo de variável

Esse ponto é bem importante.

Se você declara uma variável como atributo da classe:

```java
private Random aleatorio;
```

ela existe no objeto inteiro.

Mas se você declara dentro do construtor:

```java
Random aleatorio = new Random();
```

ela só existe dentro daquele construtor. Depois que o construtor termina, você não consegue mais usar essa variável em outros métodos.

Então:

**atributo** = pertence ao objeto.
**variável local** = pertence ao método/bloco onde foi criada.

O PDF chama atenção para isso usando o exemplo da variável `aleatorio`. 

---

## 7. Estado e comportamento

O **estado** do objeto é definido pelos atributos.

Exemplo:

```java
private String nome;
private int idade;
```

O **comportamento** é definido pelos métodos.

Exemplo:

```java
public void definirNome(String nome) {
    this.nome = nome;
}
```

Então, em:

```java
Aluno novoAluno = new Aluno("teste", 50);
```

o estado do objeto é o conjunto de valores dos atributos, e o comportamento são os métodos que ele pode executar. 

---

## 8. Garbage Collector

Em Java, você **não destrói objetos manualmente**.

Quando um objeto não tem mais nenhuma referência apontando para ele, a JVM pode removê-lo da memória usando o **coletor de lixo**, ou **Garbage Collector**.

Você pode pedir:

```java
System.gc();
```

Mas isso é só uma **solicitação**, não uma ordem. A JVM decide quando realmente limpar. O PDF reforça que o programador não controla diretamente a destruição dos objetos. 

---

## 9. Encapsulamento

Encapsulamento é proteger os dados internos da classe.

A ideia é:

Atributos ficam `private`.
Acesso acontece por métodos `public`.
A classe controla como seus dados são lidos e alterados.

Exemplo:

```java
public class Pessoa {
    private String nome;

    public Pessoa(String nome) {
        this.nome = nome;
    }

    public String getNome() {
        return nome;
    }
}
```

O PDF explica que encapsulamento serve para **ocultar dados**, controlar leitura/escrita por métodos públicos e oferecer uma interface simples para usar a classe sem conhecer seus detalhes internos. 

Exemplo com validação:

```java
public void setIdade(int idade) {
    if (idade >= 0) {
        this.idade = idade;
    }
}
```

Isso impede:

```java
pessoa.idade = -500;
```

Se `idade` estiver `private`, ninguém altera direto de fora.

---

## 10. Getters e setters

Getter = método para pegar valor.
Setter = método para alterar valor.

Exemplo:

```java
public String getNome() {
    return nome;
}

public void setNome(String nome) {
    this.nome = nome;
}
```

Mas nem todo atributo precisa ter setter. Se o dado for sensível ou não puder mudar, você pode ter só getter.

Exemplo:

```java
private final double codigoIdentificador;

public double getCodigoIdentificador() {
    return codigoIdentificador;
}
```

---

## 11. Relações entre objetos

O tema também explica três relações importantes: **associação**, **agregação** e **composição**. O PDF mostra que associação é a mais ampla, agregação é um caso mais específico, e composição é a relação mais forte/restritiva. 

### Associação

Um objeto usa outro, mas sem posse forte.

Exemplo:

```java
class Professor {
    public void ensinar(Aluno aluno) {
        System.out.println("Ensinando " + aluno.getNome());
    }
}
```

O professor usa o aluno, mas não “possui” o aluno.

### Agregação

Um objeto contém outro, mas o filho pode existir sem o pai.

Exemplo:

```java
class Escola {
    private Aluno[] alunos;
}
```

Se a escola fechar, os alunos continuam existindo.

### Composição

Um objeto contém outro e o filho depende do pai para existir.

Exemplo:

```java
class Escola {
    private Departamento[] departamentos;
}
```

Se a escola deixar de existir, os departamentos também deixam.

O PDF usa justamente exemplos como `Escola`, `Aluno`, `Departamento` e `Endereco` para mostrar essas relações. 

---

## 12. Referências de objetos

Esse é um ponto que confunde muita gente.

Em Java, variáveis de classe/objeto guardam **referências**, não o objeto inteiro.

Exemplo:

```java
Aluno a1 = new Aluno("Carlos", 20);
Aluno a2 = new Aluno("Ana", 23);

a2 = a1;
a2.definirNome("Flávia");

System.out.println(a1.recuperarNome());
```

Vai imprimir:

```text
Flávia
```

Porque agora `a1` e `a2` apontam para o mesmo objeto.

O PDF mostra exatamente essa ideia: objetos são manipulados por referência, e passar um objeto como parâmetro também passa a referência para o mesmo objeto. Já tipos primitivos, como `int`, `double`, `boolean`, são passados por valor. 

Resumo:

```java
int x = 10;
```

`x` guarda valor.

```java
Aluno a1 = new Aluno();
```

`a1` guarda referência.

---

## 13. Herança

Herança é quando uma classe filha herda atributos e métodos de uma classe pai.

Exemplo:

```java
public class Pessoa {
    protected String nome;
}

public class Aluno extends Pessoa {
    private String matricula;
}
```

`Aluno` é uma `Pessoa`.

O PDF explica que uma classe acima na hierarquia é chamada de **superclasse**, e as classes abaixo são chamadas de **subclasses**. Também destaca que herança evita repetição de código e melhora manutenção, porque atributos/métodos comuns podem ficar na classe pai. 

Termos:

Superclasse = classe pai/base.
Subclasse = classe filha/derivada.
`extends` = usado para herdar de uma classe.
`super` = usado para chamar algo da classe pai.

Exemplo:

```java
public class Empregado {
    protected String nome;

    public Empregado(String nome) {
        this.nome = nome;
    }
}

public class Professor extends Empregado {
    public Professor(String nome) {
        super(nome);
    }
}
```

---

## 14. Generalização e especialização

Quando você sobe na hierarquia, fica mais genérico.

Exemplo:

```text
Pessoa
  └── Empregado
        └── Professor
```

`Pessoa` é mais geral.
`Professor` é mais específico.

O PDF destaca essa ideia: classes mais altas representam generalizações, enquanto classes mais baixas representam especializações. 

---

## 15. Herança múltipla

Java **não permite herança múltipla de classes**.

Você não pode fazer:

```java
class C extends A, B {
}
```

Mas Java permite implementar múltiplas interfaces:

```java
class C implements InterfaceA, InterfaceB {
}
```

O Tema 1 já introduz essa diferença: Java não suporta múltiplas superclasses, mas aceita múltiplas interfaces. 

---

## 16. Polimorfismo

Polimorfismo é quando uma referência de tipo mais geral pode apontar para objetos de tipos mais específicos.

Exemplo:

```java
Pessoa p = new Aluno();
```

Ou:

```java
Empregado e = new Professor();
```

A ideia prática é: você trabalha com o tipo geral, mas em tempo de execução o comportamento pode ser o da classe específica.

Exemplo:

```java
class Empregado {
    public void trabalhar() {
        System.out.println("Empregado trabalhando");
    }
}

class Professor extends Empregado {
    @Override
    public void trabalhar() {
        System.out.println("Professor dando aula");
    }
}
```

Se fizer:

```java
Empregado e = new Professor();
e.trabalhar();
```

a saída será:

```text
Professor dando aula
```

Isso é polimorfismo.

---

## 17. Agrupamento de objetos e coleções

O Tema 1 também entra em agrupamento de objetos. Isso aparece em estruturas como `ArrayList`, `List`, `Set`, `Map`, `TreeMap`, `Collectors.groupingBy` e uso de `stream`.

O PDF mostra operações com `ArrayList`, como `add`, `get`, `remove`, `set`, `contains`, `clear` e `isEmpty`. Também comenta que coleções facilitam manipulação de dados e deixam o código mais legível. 

Exemplo:

```java
ArrayList<Integer> numeros = new ArrayList<>();

numeros.add(10);
numeros.add(20);
numeros.add(30);

System.out.println(numeros.get(0));
numeros.remove(1);
numeros.set(0, 57);
```

Conceitos principais:

`List` → lista, mantém ordem de inserção e aceita repetidos.
`Set` → conjunto, geralmente não aceita repetidos.
`Map` → chave e valor.
`TreeMap` → `Map` ordenado pelas chaves.
`Queue` → fila.
`Deque` → pode funcionar como fila ou pilha.

O PDF também destaca `groupingBy`, que cria uma estrutura `Map`, mapeando uma chave de agrupamento para uma coleção de objetos agrupados. 

Exemplo mental:

```java
Map<String, List<Aluno>> agrupamento =
    alunos.stream()
          .collect(Collectors.groupingBy(Aluno::getNaturalidade));
```

Isso agrupa alunos pela naturalidade.

---

## 18. Lambda e Function

No final da parte de agrupamento/mapeamento, aparece o uso de função/lambda.

Exemplo:

```java
x -> x * x
```

Isso significa: recebe `x` e retorna `x` ao quadrado.

O PDF mostra um exemplo com `List.of(1, 2, 3, 4, 5)` e uma função `map`, onde a expressão correta para gerar os quadrados é `x -> x * x`. 

Exemplo:

```java
List<Integer> numeros = List.of(1, 2, 3, 4, 5);

List<Integer> quadrados = numeros.stream()
        .map(x -> x * x)
        .toList();
```

Resultado:

```text
[1, 4, 9, 16, 25]
```

---

# O que você precisa dominar para a prova

Se você souber fazer isso, você está bem no Tema 1:

```text
1. Criar classe com atributos private.
2. Criar construtor.
3. Usar this.
4. Criar getters e setters.
5. Instanciar objetos com new.
6. Explicar estado e comportamento.
7. Entender referência de objetos.
8. Diferenciar associação, agregação e composição.
9. Criar herança simples com extends e super.
10. Entender public, private e protected.
11. Saber o básico de polimorfismo.
12. Usar ArrayList básico.
13. Entender List, Set, Map e groupingBy.
14. Entender lambda simples: x -> x * x.
```

---

# Desafios para você resolver

Não vou colocar a solução agora, porque a ideia é você treinar. Depois você me manda seu código e eu corrijo.

## Desafio 1 — Classe simples

Crie uma classe `Pessoa` com:

```text
nome
idade
codigoIdentificador
```

Regras:

`nome` deve ser `private`.
`idade` deve ser `private`.
`codigoIdentificador` deve ser `private`.
Crie um construtor que recebe `nome` e `idade`.
Gere o `codigoIdentificador` com `Random`.
Crie getters para os três atributos.
Crie setter para `idade`, mas não permita idade negativa.

No `main`, crie duas pessoas e imprima:

```text
[Pessoa 1] nome: Alex, idade: 25, código: ...
[Pessoa 2] nome: Revo, idade: 20, código: ...
```

---

## Desafio 2 — Encapsulamento

Crie uma classe `ContaBancaria`.

Atributos:

```text
titular
saldo
numeroConta
```

Regras:

Todos os atributos devem ser `private`.
O saldo começa com `0`.
Crie método `depositar(double valor)`.
Crie método `sacar(double valor)`.
Não permita saque maior que o saldo.
Não permita depósito negativo.
Crie método `exibirDados()`.

---

## Desafio 3 — Referência de objetos

Crie uma classe `Aluno` com:

```text
nome
idade
```

Depois, no `main`, faça:

```java
Aluno a1 = new Aluno("Carlos", 20);
Aluno a2 = new Aluno("Ana", 23);

a2 = a1;
a2.setNome("Flávia");

System.out.println(a1.getNome());
System.out.println(a2.getNome());
```

Antes de executar, responda no comentário:

```java
// O que eu acho que vai imprimir?
```

Depois execute e veja se acertou.

---

## Desafio 4 — Associação, agregação e composição

Crie as classes:

```text
Endereco
Aluno
Departamento
Escola
```

Regras:

`Endereco` tem `rua` e `numero`.
`Aluno` tem `nome` e `matricula`.
`Departamento` tem `nome`.
`Escola` tem `nome`, `Endereco`, lista de alunos e lista de departamentos.

Na classe `Escola`:

`Endereco` pode ser recebido pelo construtor.
`Aluno` deve ser adicionado por método `matricularAluno`.
`Departamento` deve ser criado dentro da escola com método `criarDepartamento`.

Depois explique em comentário:

```java
// Endereco é associação, agregação ou composição?
// Aluno é associação, agregação ou composição?
// Departamento é associação, agregação ou composição?
```

---

## Desafio 5 — Herança

Crie:

```text
Empregado
Professor
Diretor
Secretario
```

`Empregado` deve ter:

```text
nome
salario
```

`Professor` deve ter:

```text
disciplina
```

`Diretor` deve ter:

```text
setor
```

`Secretario` deve ter:

```text
turno
```

Todos devem ter método:

```java
exibirDados()
```

Use `extends` e `super`.

---

## Desafio 6 — Polimorfismo

Usando o desafio anterior, faça:

```java
Empregado e1 = new Professor(...);
Empregado e2 = new Diretor(...);
Empregado e3 = new Secretario(...);
```

Coloque todos em um `ArrayList<Empregado>`.

Depois faça um `for` chamando:

```java
e.exibirDados();
```

Cada classe deve mostrar seus próprios dados.

---

## Desafio 7 — ArrayList

Crie um `ArrayList<Integer>` com os números:

```text
10, 20, 30, 40, 50
```

Faça:

1. Imprima todos com `for`.
2. Remova o número da posição 1.
3. Altere o primeiro número para `57`.
4. Verifique se contém `100`.
5. Limpe a lista.
6. Verifique se está vazia.

Esse desafio é muito parecido com a parte do PDF sobre `ArrayList`. 

---

## Desafio 8 — Agrupamento por naturalidade

Crie uma classe `Aluno` com:

```text
nome
naturalidade
```

Crie uma lista com alunos de cidades repetidas, por exemplo:

```text
Alex - São Paulo
Revo - Rio de Janeiro
Marcos - São Paulo
Ana - Curitiba
Bianca - Curitiba
```

Depois agrupe por naturalidade usando:

```java
Collectors.groupingBy(...)
```

A saída esperada deve ser parecida com:

```text
São Paulo = [Alex, Marcos]
Rio de Janeiro = [Revo]
Curitiba = [Ana, Bianca]
```

---

## Desafio 9 — Lambda

Crie uma lista:

```java
List<Integer> numeros = List.of(1, 2, 3, 4, 5);
```

Use `stream().map(...)` para gerar:

```text
[1, 4, 9, 16, 25]
```

Obrigatório usar lambda:

```java
x -> x * x
```

---

## Desafio 10 — Mini sistema completo

Crie um mini sistema chamado `SistemaEscola`.

Precisa ter:

```text
Classe Aluno
Classe Professor
Classe Escola
Classe Principal
```

A escola deve conseguir:

```text
matricular alunos
contratar professores
listar alunos
listar professores
agrupar alunos por cidade
```

Conceitos obrigatórios:

`private`
construtor
getter
setter
`ArrayList`
herança
polimorfismo
`groupingBy`
`toString`
