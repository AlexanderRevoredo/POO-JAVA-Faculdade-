# Tema 2 — Herança em Java

## 1. Ideia central de herança

Herança é quando uma classe transmite atributos e métodos para outra.

Exemplo:

```java
public class Pessoa {
    protected String nome;
}

public class Aluno extends Pessoa {
    private String matricula;
}
```

Aqui:

```text
Pessoa = superclasse / classe pai / classe base
Aluno = subclasse / classe filha / classe derivada
```

A frase mais importante é:

```text
Aluno é uma Pessoa.
```

O PDF reforça isso: a herança cria uma relação vertical do tipo **“é um”** ou **“é tipo de”**, diferente de associação/agregação/composição, que são relações mais horizontais do tipo “possui” ou “contém”. 

---

## 2. Hierarquia de classes

Uma hierarquia de classes é uma cadeia de herança.

Exemplo:

```text
Pessoa
  └── Fisica
        └── Aluno
```

Se `Aluno` herda de `Fisica`, e `Fisica` herda de `Pessoa`, então:

```text
Aluno é Aluno.
Aluno é Fisica.
Aluno é Pessoa.
Aluno também é Object.
```

Esse ponto é muito importante para prova: **a herança se propaga em vários níveis**. O PDF diz que um objeto da classe `Aluno` também é objeto das classes acima na hierarquia. 

Exemplo:

```java
Aluno aluno = new Aluno();
Pessoa pessoa = aluno;
```

Isso funciona porque `Aluno` é um tipo de `Pessoa`.

---

## 3. `extends`

Em Java, uma classe herda de outra usando `extends`.

```java
public class Aluno extends Pessoa {
}
```

Isso significa:

```text
Aluno herda características de Pessoa.
```

Mas Java só permite **uma superclasse direta**:

```java
public class Aluno extends Pessoa {
}
```

Não pode:

```java
public class Aluno extends Pessoa, Fisica {
}
```

Isso é herança múltipla de classes, e Java não permite.

---

## 4. `super`

Quando a classe pai tem construtor com parâmetros, a classe filha precisa chamar esse construtor usando `super`.

Exemplo:

```java
public class Pessoa {
    protected String nome;
    protected String nacionalidade;

    public Pessoa(String nome, String nacionalidade) {
        this.nome = nome;
        this.nacionalidade = nacionalidade;
    }
}
```

Classe filha:

```java
public class Aluno extends Pessoa {
    private String matricula;

    public Aluno(String nome, String nacionalidade, String matricula) {
        super(nome, nacionalidade);
        this.matricula = matricula;
    }
}
```

Aqui:

```java
super(nome, nacionalidade);
```

chama o construtor da classe `Pessoa`.

Sem isso, se a superclasse não tiver construtor vazio, o código dá erro.

---

## 5. Generalização e especialização

Na hierarquia:

```text
Pessoa
  ├── Fisica
  ├── Juridica
  └── Aluno
```

`Pessoa` é mais genérica.
`Aluno`, `Fisica` e `Juridica` são mais específicas.

O PDF explica que as classes acima generalizam, e as classes abaixo especializam. A especialização acontece quando a subclasse adiciona atributos, métodos ou muda comportamentos herdados. 

Exemplo:

```java
public class Pessoa {
    protected String identificador;

    protected void atualizarID(String identificador) {
        this.identificador = identificador;
    }
}
```

Classe `Fisica` especializa:

```java
public class Fisica extends Pessoa {
    protected void atualizarID(String cpf) {
        if (validaCPF(cpf)) {
            this.identificador = cpf;
        }
    }

    private boolean validaCPF(String cpf) {
        return cpf.length() == 11;
    }
}
```

Classe `Juridica` especializa de outro jeito:

```java
public class Juridica extends Pessoa {
    protected void atualizarID(String cnpj) {
        if (validaCNPJ(cnpj)) {
            this.identificador = cnpj;
        }
    }

    private boolean validaCNPJ(String cnpj) {
        return cnpj.length() == 14;
    }
}
```

Mesmo método conceitual: `atualizarID`.
Comportamentos diferentes: CPF, CNPJ, matrícula etc.

---

## 6. Sobrescrita de métodos

Sobrescrita é quando a classe filha redefine um método herdado da classe pai.

Exemplo:

```java
public class Empregado {
    public void trabalhar() {
        System.out.println("Empregado trabalhando");
    }
}
```

```java
public class Professor extends Empregado {
    @Override
    public void trabalhar() {
        System.out.println("Professor dando aula");
    }
}
```

O `@Override` avisa ao compilador:

```text
Esse método está sobrescrevendo um método da superclasse.
```

Se você errar a assinatura, o Java avisa.

---

## 7. Herança múltipla e “diamante da morte”

Java **não permite herança múltipla de classes**.

O motivo principal é evitar ambiguidade.

Imagine:

```text
Pessoa
 ├── Fisica
 └── Juridica
      ...
ProfessorJuridico herda de Fisica e Juridica
```

Se `Fisica` e `Juridica` tiverem versões diferentes de `atualizarID()`, qual delas `ProfessorJuridico` deveria usar?

Esse problema é chamado no PDF de **diamante da morte**. O material mostra justamente essa ambiguidade em uma imagem: `Pessoa` no topo, `Fisica` e `Juridica` no meio e `ProfessorJuridico` embaixo recebendo dois caminhos possíveis de herança. 

Por isso Java permite:

```java
class Aluno extends Pessoa {
}
```

Mas não permite:

```java
class ProfessorJuridico extends Fisica, Juridica {
}
```

Para contornar isso, Java usa **interfaces**, que aparecem mais forte no Tema 3.

---

## 8. Modificadores de acesso na herança

Esse é um dos pontos mais importantes do Tema 2.

### `private`

Só a própria classe acessa.

```java
private String cpf;
```

A subclasse **herda a estrutura**, mas não consegue acessar diretamente o atributo `private`.

Exemplo:

```java
public class Pessoa {
    private String nome;
}

public class Aluno extends Pessoa {
    public void teste() {
        // System.out.println(nome); // erro
    }
}
```

### default

Quando você não escreve nada:

```java
class Pessoa {
}
```

Ela fica visível apenas dentro do mesmo pacote.

O PDF mostra um exemplo em que uma classe sem `public` não pode ser usada por outra classe em pacote diferente, mesmo com `import`, porque o modificador default restringe a visibilidade ao pacote. 

### `protected`

Visível:

```text
na própria classe
no mesmo pacote
nas subclasses, mesmo em outro pacote
```

Exemplo:

```java
protected String nome;
```

Uma subclasse pode acessar:

```java
public class Aluno extends Pessoa {
    public void mostrarNome() {
        System.out.println(nome);
    }
}
```

O PDF reforça que atributos e métodos `protected` são herdados e acessíveis pela subclasse, mas a herança funciona de cima para baixo: a superclasse não acessa automaticamente membros criados na subclasse. 

### `public`

Visível em qualquer lugar.

```java
public String getNome() {
    return nome;
}
```

Resumo:

```text
private   → só a própria classe
default   → própria classe + pacote
protected → própria classe + pacote + subclasses
public    → qualquer lugar
```

---

## 9. Classes internas

Classe interna é uma classe declarada dentro de outra.

Exemplo:

```java
class Externa {

    class Interna {
        void mostrar() {
            System.out.println("Classe interna");
        }
    }
}
```

Para instanciar:

```java
Externa.Interna obj = new Externa().new Interna();
```

O PDF mostra que classes internas também podem participar de herança, e que os modificadores de acesso afetam a acessibilidade dessas classes. Ele inclusive mostra exemplos de classe interna sendo estendida por outra classe interna e por classe fora do aninhamento. 

Exemplo simplificado:

```java
class Externa {
    class Interna {
        void mensagem() {
            System.out.println("Interna");
        }
    }
}

class Filha extends Externa.Interna {
    public Filha() {
        new Externa().super();
    }
}
```

Esse tipo de código é mais avançado e provavelmente cai mais em questão conceitual do que em prática simples.

---

## 10. Subclasse versus subtipo

Esse é um ponto mais teórico, mas importante.

Toda classe filha é uma subclasse.

Mas para ser um **subtipo correto**, ela precisa poder substituir a classe pai sem quebrar o comportamento esperado.

Exemplo simples:

```java
Pessoa p = new Fisica();
```

Se `Fisica` respeita o contrato de `Pessoa`, então `Fisica` é um subtipo adequado de `Pessoa`.

O PDF diz que subclasse e subtipo são conceitos diferentes: subclasse é derivação por herança; subtipo exige que as propriedades/contratos da superclasse continuem válidos na subclasse. 

---

## 11. Princípio da substituição de Liskov

Esse princípio diz, em linguagem simples:

```text
Se uma classe filha herda de uma classe pai, eu deveria conseguir usar a filha no lugar da pai sem quebrar o programa.
```

Exemplo bom:

```java
public class Pessoa {
    public void atualizarID(String id) {
        // atualiza identificador
    }
}
```

```java
public class Fisica extends Pessoa {
    @Override
    public void atualizarID(String cpf) {
        // valida CPF e atualiza
    }
}
```

Continua recebendo `String`. Continua atualizando o identificador. O contrato geral foi mantido.

Exemplo ruim:

```java
public class Aluno extends Pessoa {
    public void atualizarID() {
        // gera matrícula sozinho
    }
}
```

Aqui mudou o contrato: antes precisava passar uma `String`; agora não recebe nada. Um código que espera usar `Pessoa` pode quebrar ao receber `Aluno`.

O PDF liga esse assunto ao princípio de Liskov e aos princípios SOLID, dizendo que um objeto da classe base deve poder ser substituído por objeto da derivada sem prejuízo para o funcionamento do software. 

---

## 12. Tipo estático e tipo dinâmico

Esse ponto é muito importante para entender polimorfismo.

Exemplo:

```java
Pessoa pessoa = new Aluno();
```

Aqui:

```text
Tipo estático = Pessoa
Tipo dinâmico = Aluno
```

O tipo estático é o tipo da variável, definido no código.

```java
Pessoa pessoa
```

O tipo dinâmico é o tipo real do objeto criado em tempo de execução.

```java
new Aluno()
```

Consequência:

```java
pessoa.metodoDePessoa();
```

Você só consegue chamar diretamente métodos que existem em `Pessoa`.

Mas se `Aluno` sobrescrever um método de `Pessoa`, a versão executada será a de `Aluno`.

Exemplo:

```java
class Pessoa {
    public void apresentar() {
        System.out.println("Sou uma pessoa");
    }
}

class Aluno extends Pessoa {
    @Override
    public void apresentar() {
        System.out.println("Sou um aluno");
    }
}
```

```java
Pessoa p = new Aluno();
p.apresentar();
```

Saída:

```text
Sou um aluno
```

---

## 13. Java Collections Framework e herança

O Tema 2 também mostra que herança não é só teoria: ela aparece muito nas bibliotecas do Java, especialmente no **Java Collections Framework**.

O PDF fala de hierarquias como `Collection`, `Set`, `SortedSet`, `Map` e `SortedMap`. `SortedSet` e `SortedMap` são especializações de `Set` e `Map`, incorporando ordenação. 

Exemplo de ideia:

```text
Collection
 ├── List
 ├── Set
 │    └── SortedSet
 └── Queue

Map
 └── SortedMap
```

### `Collection`

É uma interface mais genérica para grupos de objetos.

Métodos importantes citados no PDF:

```java
add()
addAll()
clear()
contains()
containsAll()
equals()
hashCode()
isEmpty()
iterator()
remove()
removeAll()
retainAll()
size()
toArray()
```

Esses métodos são comportamentos comuns que várias coleções herdam ou implementam. 

### `List`

Lista ordenada por posição e aceita repetidos.

Exemplo:

```java
List<String> nomes = new ArrayList<>();

nomes.add("Alex");
nomes.add("Revo");
nomes.add("Alex");
```

### `Set`

Conjunto, normalmente não aceita repetidos.

```java
Set<String> nomes = new HashSet<>();

nomes.add("Alex");
nomes.add("Alex");
```

Só fica um `"Alex"`.

### `Map`

Estrutura de chave e valor.

```java
Map<String, String> alunos = new HashMap<>();

alunos.put("001", "Alex");
alunos.put("002", "Revo");
```

### `SortedSet` e `SortedMap`

São versões ordenadas.

```java
SortedSet<String> nomes = new TreeSet<>();
```

---

## 14. ArrayList não ordena automaticamente

Cuidado com uma pegadinha.

`ArrayList` mantém a **ordem de inserção**, mas ele não ordena sozinho alfabeticamente ou numericamente.

Exemplo:

```java
ArrayList<String> cores = new ArrayList<>();

cores.add("Vermelho");
cores.add("Azul");
cores.add("Branco");

System.out.println(cores);
```

Saída:

```text
[Vermelho, Azul, Branco]
```

Ele imprime na ordem em que você inseriu.

O PDF mostra uma questão em que um método `compare` não muda a ordem de inserção do `ArrayList`, porque `ArrayList` não usa automaticamente esse comparador para ordenar os elementos. 

Para ordenar, você teria que fazer algo como:

```java
Collections.sort(cores);
```

ou:

```java
cores.sort(Comparator.naturalOrder());
```

---

## 15. Classe `Object`

Em Java, toda classe herda direta ou indiretamente de `Object`.

Mesmo que você escreva:

```java
public class Pessoa {
}
```

Na prática, ela é como se fosse:

```java
public class Pessoa extends Object {
}
```

Por isso, toda classe Java possui métodos herdados de `Object`.

O Tema 2 destaca principalmente:

```java
toString()
equals()
hashCode()
getClass()
```

O PDF afirma que todas as classes descendem direta ou indiretamente de `Object`, e que esses métodos ficam disponíveis para as subclasses. 

---

## 16. `toString()`

O método `toString()` retorna uma representação textual do objeto.

Se você não sobrescrever, a saída costuma ser algo como:

```text
com.meuprojeto.Pessoa@72ea2f77
```

O PDF explica que essa forma padrão contém o nome qualificado da classe, o símbolo `@` e o hash do objeto. 

Exemplo sobrescrevendo:

```java
public class Pessoa {
    private String nome;

    public Pessoa(String nome) {
        this.nome = nome;
    }

    @Override
    public String toString() {
        return "Pessoa{nome='" + nome + "'}";
    }
}
```

Agora:

```java
Pessoa p = new Pessoa("Alex");
System.out.println(p);
```

Saída:

```text
Pessoa{nome='Alex'}
```

Sem `toString()`, imprimir objeto fica feio.
Com `toString()`, você controla como ele aparece.

---

## 17. `equals()`

O operador `==` compara referências.

```java
Pessoa p1 = new Pessoa("Alex", "123");
Pessoa p2 = new Pessoa("Alex", "123");

System.out.println(p1 == p2);
```

Mesmo com os mesmos dados, pode dar:

```text
false
```

Porque são dois objetos diferentes na memória.

Já `equals()` serve para comparar conteúdo, se você sobrescrever corretamente.

Exemplo:

```java
@Override
public boolean equals(Object obj) {
    Pessoa outra = (Pessoa) obj;
    return this.cpf.equals(outra.cpf);
}
```

Aí:

```java
p1.equals(p2)
```

pode dar `true` se os CPFs forem iguais.

O PDF trabalha justamente a diferença entre `==` e `equals()`, usando exemplos com `PessoaFisica` e `Aluno`, além de sobrescrita de `equals`, `hashCode` e `toString`. 

---

## 18. `hashCode()`

O `hashCode()` retorna um número usado por estruturas como:

```java
HashSet
HashMap
Hashtable
```

Regra prática:

Se você sobrescreve `equals()`, normalmente também deve sobrescrever `hashCode()`.

Exemplo:

```java
@Override
public int hashCode() {
    return cpf.hashCode();
}
```

Por quê?

Porque estruturas baseadas em hash usam `hashCode()` para organizar os objetos. Se `equals()` diz que dois objetos são iguais, o `hashCode()` deles deve ser compatível.

---

## 19. `getClass()`

O método `getClass()` retorna a classe real do objeto.

Exemplo:

```java
Pessoa p = new Aluno();

System.out.println(p.getClass());
```

Pode imprimir algo como:

```text
class Aluno
```

Mesmo que o tipo estático seja `Pessoa`, o objeto real é `Aluno`.

---

# O que você precisa dominar para a prova

Se você souber isso, está bem no Tema 2:

```text
1. Explicar herança como relação “é um”.
2. Diferenciar superclasse e subclasse.
3. Usar extends.
4. Usar super no construtor.
5. Explicar generalização e especialização.
6. Sobrescrever métodos com @Override.
7. Entender por que Java não permite herança múltipla de classes.
8. Explicar o diamante da morte.
9. Diferenciar private, default, protected e public.
10. Entender classe interna.
11. Diferenciar subclasse e subtipo.
12. Explicar Liskov de forma simples.
13. Diferenciar tipo estático e tipo dinâmico.
14. Entender a hierarquia básica de Collections.
15. Saber que ArrayList não ordena automaticamente.
16. Saber usar List, Set e Map em nível básico.
17. Explicar que toda classe herda de Object.
18. Sobrescrever toString().
19. Sobrescrever equals().
20. Sobrescrever hashCode().
21. Diferenciar == e equals().
```

---

# Desafios para você resolver

## Desafio 1 — Herança básica

Crie as classes:

```text
Pessoa
Aluno
Professor
```

`Pessoa` deve ter:

```text
nome
nacionalidade
naturalidade
```

`Aluno` herda de `Pessoa` e adiciona:

```text
matricula
```

`Professor` herda de `Pessoa` e adiciona:

```text
disciplina
```

Regras:

```text
Use private ou protected corretamente.
Use construtor com super.
Crie método apresentar().
Sobrescreva apresentar() em Aluno e Professor.
```

---

## Desafio 2 — Generalização e especialização

Crie:

```text
Pessoa
Fisica
Juridica
Aluno
```

`Pessoa` tem:

```text
identificador
atualizarID(String identificador)
```

`Fisica` sobrescreve `atualizarID` para validar CPF com 11 dígitos.

`Juridica` sobrescreve `atualizarID` para validar CNPJ com 14 dígitos.

`Aluno` gera uma matrícula automática.

Depois responda em comentário:

```java
// Aluno respeita o mesmo contrato de Pessoa?
// Por quê?
```

---

## Desafio 3 — `protected` na prática

Crie:

```java
public class Pessoa {
    protected String nome;
    private String cpf;
}
```

Depois crie:

```java
public class Aluno extends Pessoa {
}
```

Dentro de `Aluno`, tente acessar:

```java
nome
cpf
```

Antes de compilar, responda:

```java
// Qual vai funcionar?
// Qual vai dar erro?
// Por quê?
```

---

## Desafio 4 — default e pacotes

Crie dois pacotes:

```text
pctalpha
pctbravo
```

Em `pctbravo`, crie:

```java
class Pessoa {
}
```

Sem `public`.

Em `pctalpha`, tente:

```java
import pctbravo.Pessoa;

public class Aluno extends Pessoa {
}
```

Veja o erro.

Depois mude `Pessoa` para:

```java
public class Pessoa {
}
```

E teste de novo.

Objetivo: entender o modificador **default**.

---

## Desafio 5 — Diamante da morte conceitual

Crie um comentário explicando o problema:

```text
Pessoa tem atualizarID().
Fisica sobrescreve atualizarID() para CPF.
Juridica sobrescreve atualizarID() para CNPJ.
ProfessorJuridico precisaria herdar de Fisica e Juridica.
```

Responda:

```java
// Se Java permitisse isso, qual atualizarID() deveria ser chamado?
// Por que isso é ambíguo?
// Como Java evita esse problema?
```

---

## Desafio 6 — Tipo estático e dinâmico

Crie:

```java
Pessoa p = new Aluno();
```

`Pessoa` tem:

```java
public void apresentar()
```

`Aluno` sobrescreve:

```java
@Override
public void apresentar()
```

Depois execute:

```java
p.apresentar();
```

Responda:

```java
// O tipo estático de p é qual?
// O tipo dinâmico de p é qual?
// Qual método foi executado?
```

---

## Desafio 7 — Liskov

Crie uma classe `Ave` com:

```java
public void voar()
```

Crie:

```java
class Pardal extends Ave
class Pinguim extends Ave
```

Agora pense:

```java
Ave a = new Pinguim();
a.voar();
```

Pinguim deveria voar?

Responda:

```java
// Essa herança respeita Liskov?
// Como você remodelaria isso?
```

Dica: talvez `Ave` não devesse ter `voar()`. Talvez o melhor seja uma interface `Voador`.

---

## Desafio 8 — `toString()`

Crie uma classe `Produto` com:

```text
nome
preco
codigo
```

Sem sobrescrever `toString()`, faça:

```java
System.out.println(produto);
```

Depois sobrescreva:

```java
@Override
public String toString() {
    return "Produto: " + nome + " | Preço: " + preco + " | Código: " + codigo;
}
```

Compare as duas saídas.

---

## Desafio 9 — `equals()` e `hashCode()`

Crie uma classe `PessoaFisica` com:

```text
nome
cpf
```

Crie dois objetos:

```java
PessoaFisica p1 = new PessoaFisica("Alex", "123");
PessoaFisica p2 = new PessoaFisica("Revo", "123");
```

Faça:

```java
System.out.println(p1 == p2);
System.out.println(p1.equals(p2));
```

Depois sobrescreva `equals()` para comparar pelo CPF.

Depois sobrescreva `hashCode()` usando o CPF.

Objetivo:

```text
== compara referência.
equals compara conteúdo, se você programar assim.
hashCode deve acompanhar equals.
```

---

## Desafio 10 — Collections e herança

Crie uma classe `Empregado` com:

```text
nome
salario
```

Crie subclasses:

```text
Professor
Diretor
Secretario
```

Crie:

```java
ArrayList<Empregado> empregados = new ArrayList<>();
```

Adicione objetos de todos os tipos:

```java
empregados.add(new Professor(...));
empregados.add(new Diretor(...));
empregados.add(new Secretario(...));
```

Depois faça:

```java
for (Empregado e : empregados) {
    System.out.println(e);
}
```

Obrigatório:

```text
Usar herança.
Usar polimorfismo.
Sobrescrever toString().
```

---

## Desafio 11 — `List`, `Set` e `Map`

Crie três estruturas:

```java
List<String> lista = new ArrayList<>();
Set<String> conjunto = new HashSet<>();
Map<String, String> mapa = new HashMap<>();
```

Adicione nomes repetidos na `List` e no `Set`.

Veja a diferença:

```text
List aceita repetidos.
Set remove repetidos.
Map trabalha com chave e valor.
```

Depois crie um `Map` assim:

```text
matricula -> nome
```

Exemplo:

```java
001 -> Alex
002 -> Revo
003 -> Valhalla
```

---

## Desafio 12 — ArrayList não ordena sozinho

Crie:

```java
ArrayList<String> cores = new ArrayList<>();
```

Adicione:

```text
Vermelho
Azul
Branco
Preto
```

Imprima.

Depois use:

```java
Collections.sort(cores);
```

Imprima de novo.

Responda:

```java
// O ArrayList ordenou sozinho?
// O que mudou depois do sort?
```

---

## Desafio 13 — Classe interna

Crie:

```java
class Externa {
    class Interna {
        void mensagem() {
            System.out.println("Sou interna");
        }
    }
}
```

No `main`, instancie:

```java
Externa.Interna obj = new Externa().new Interna();
obj.mensagem();
```

Depois tente mudar a classe interna para `private` e veja o que acontece.

---

## Desafio 14 — Projeto chefão do Tema 2

Crie um projeto chamado:

```text
SistemaHerancaEscola
```

Classes:

```text
Pessoa
PessoaFisica
PessoaJuridica
Aluno
Empregado
Professor
Diretor
Secretario
Escola
Principal
```

Obrigatório ter:

```text
extends
super
protected
private
@Override
toString()
equals()
hashCode()
ArrayList
List
Set
Map
tipo estático e tipo dinâmico
```

Regras:

`Pessoa` tem `nome` e `identificador`.
`PessoaFisica` valida CPF.
`PessoaJuridica` valida CNPJ.
`Aluno` tem matrícula.
`Empregado` tem salário.
`Professor`, `Diretor` e `Secretario` herdam de `Empregado`.
`Escola` guarda listas de alunos e empregados.
`equals()` deve comparar pessoas pelo identificador.
`hashCode()` deve usar o identificador.
`toString()` deve mostrar os dados bonitos.
No `main`, use:

```java
Pessoa p1 = new Aluno(...);
Empregado e1 = new Professor(...);
```

E explique em comentários:

```java
// tipo estático:
// tipo dinâmico:
// método executado:
```
