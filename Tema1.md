# Resumo: Introdução à Programação OO em Java

## 1. Classes e Objetos

**O que é uma classe?** É uma estrutura que define dados, métodos e o mecanismo de instanciação de objetos. Em Java, cada classe pública deve estar em um arquivo com o mesmo nome e extensão `.java`.

**Sintaxe básica de declaração:**
```java
[Modificador] class Identificador [Superclasse] [Superinterface] { [Corpo] }
```

**Modificadores disponíveis:** `public`, `protected`, `private`, `abstract`, `static`, `final`, `strictfp` e anotações.

**Instanciação (criação de objetos):** Feita com a palavra-chave `new`, que chama o método construtor — obrigatoriamente com o mesmo nome da classe e sem tipo de retorno.
```java
Aluno objetoAluno = new Aluno();
```

**Ciclo de vida do objeto:**
- Criação → alocação de memória + execução do construtor
- Uso → interação via métodos
- Destruição → feita automaticamente pelo **coletor de lixo (Garbage Collector)** da JVM; o programador não controla esse momento

**Estado e comportamento:** O estado de um objeto é definido por seus atributos; o comportamento, pelos seus métodos.

---

**Encapsulamento:** Ocultação dos dados internos da classe, expondo apenas o necessário via métodos públicos (getters e setters). Serve para:
- **Ocultação de dados** — atributos `private`, acesso controlado por métodos
- **Abstração** — interface simplificada para o usuário da classe

**Modificadores de visibilidade:**

| Modificador | Visibilidade |
|---|---|
| `private` | Somente dentro da própria classe |
| `default` (sem modificador) | Dentro do mesmo pacote |
| `protected` | Pacote + subclasses |
| `public` | Acesso global |

---

**Tipos de relações entre objetos:**
- **Associação** — relação mais fraca; objetos usam serviços uns dos outros com existência independente
- **Agregação** — um objeto contém outros, mas os filhos sobrevivem ao pai (ex: Escola e Aluno)
- **Composição** — relação mais forte; filhos não existem sem o pai (ex: Escola e Departamentos)

**Referências em Java:** Variáveis de classe são referências, não cópias. Ao atribuir `a2 = a1`, ambas apontam para o mesmo objeto. Objetos passados como parâmetro também são passados por referência; tipos primitivos são passados por valor.

---

## 2. Herança e Polimorfismo

**Herança** é a relação hierárquica entre classes. A classe acima na hierarquia é a **superclasse (pai/base)**; a de baixo é a **subclasse (filha/derivada)**.

**Vantagens:**
- Reutilização de código
- Manutenibilidade (alteração na superclasse se propaga automaticamente)

**Declaração em Java:**
```java
public class Professor extends Empregado { }
```

Java **não suporta herança múltipla entre classes**, mas permite que uma classe implemente múltiplas interfaces:
```java
public class MinhaClasse implements InterfaceA, InterfaceB { }
```

Toda classe em Java herda, direta ou indiretamente, da classe `Object`.

---

**Visibilidade na herança:**
- Métodos `public` na superclasse devem ser `public` na subclasse
- Métodos `protected` podem ser `protected` ou `public` nas subclasses
- Membros `private` **não são acessíveis** às subclasses

---

**Polimorfismo** é a capacidade de um mesmo método se comportar de formas diferentes. Manifesta-se de duas formas:

- **Sobrescrita (override):** a subclasse reescreve um método da superclasse com a mesma assinatura
- **Sobrecarga (overload):** métodos com o mesmo nome mas parâmetros diferentes na mesma classe

**Classes abstratas:** Não podem ser instanciadas diretamente. Servem como base para subclasses. Podem conter métodos abstratos (sem implementação) e métodos concretos (com implementação).
```java
abstract class Animal {
    public abstract void emitirSom(); // abstrato
    public void dormir() { System.out.println("Zzzz..."); } // concreto
}
```

---

## 3. Agrupamento de Objetos

**Objetivo:** Organizar objetos em grupos com base em um critério (chave de particionamento).

**Estruturas usadas:**
- `List` → lista ordenada, aceita duplicatas, expansível dinamicamente
- `Map` → mapeia chave → valor; não permite chaves duplicadas

**Classe `Collectors` com `groupingBy`:** Permite agrupar objetos de forma elegante usando streams:
```java
Map<String, List<Aluno>> agrupamento =
    discentes.stream().collect(Collectors.groupingBy(Aluno::recuperarNaturalidade));
```

Existem 3 versões do `groupingBy`:
1. Agrupa em `List` (padrão)
2. Permite escolher outro tipo de coleção (`Set`, por exemplo)
3. Permite escolher também o tipo de `Map` (`TreeMap` mantém ordem alfabética)

---

**Principais tipos de coleções em Java:**

| Coleção | Característica |
|---|---|
| `Set` | Conjuntos sem duplicatas |
| `List` | Lista ordenada, aceita duplicatas |
| `Queue` | Fila genérica; pode ser FIFO ou lista de prioridade |
| `Deque` | Inserção/remoção nas duas extremidades; implementa pilha (LIFO) ou fila (FIFO) |

---

## 4. Ambientes de Desenvolvimento

**Java vs C/C++:**
- C++ tem checagem estática fraca e maior performance, mas menos segurança
- Java tem foco em segurança e portabilidade via JVM
- Java **não é linguagem independente de plataforma — é uma plataforma** (roda na JVM)
- Linguagem C é estruturada, sem classes/objetos; Java é puramente OO

**Componentes do ambiente Java:**
- **JVM** — Máquina Virtual Java; executa bytecodes, abstrai o hardware
- **JRE** — JVM + bibliotecas; permite executar programas Java
- **JDK** — JRE + ferramentas de desenvolvimento (`javac`, `java`, `jar`, `javadoc`)

**IDEs populares:** Eclipse e NetBeans (ambos open source, suportam plugins e múltiplas linguagens)

---

**Estrutura de um programa Java:**
- Ponto de entrada obrigatório para execução: `public static void main(String args[])`
- Métodos de acesso: `get` (recupera valor) e `set` (define valor)

**Comandos principais:**

| Comando | Uso |
|---|---|
| `switch-case` | Seleção entre múltiplas alternativas |
| `while` | Repete enquanto condição for verdadeira (testa antes) |
| `do-while` | Executa ao menos uma vez (testa depois) |
| `for` | Repetição com inicialização, condição e iteração |
| `for-each` | Iteração sobre coleções |
| `break` | Interrompe o laço ou switch atual |

---

## Desafios para Praticar

Aqui estão todos os desafios presentes no conteúdo, para você testar seus conhecimentos:

---

**Desafio 1 — Classe Pessoa (Encapsulamento)**
Modifique o programa da classe `Pessoa` para que ele exiba o seguinte resultado:
```
[Pessoa 1] nome: Teste A, Código Identificador: <valor numérico>
[Pessoa 2] nome: Teste B, Código Identificador: <valor numérico>
```

---

**Desafio 2 — Classe Endereço (Relações entre objetos)**
Com base no código da classe `Escola`, implemente a classe `Endereco` com os atributos `nome_rua` e `numero`, com seus devidos getters e setters, e teste sua criação com um `main`.

---

**Desafio 3 — Referências de objetos**
No código da classe `Referencia`, substitua a linha `a2 = a1` por `a1 = a2`, execute o programa e responda: houve diferença no resultado? Por quê?

---

**Desafio 4 — Herança: novo aluno**
Modifique a classe `Principal` para adicionar um segundo aluno com os seguintes dados:
- Nome: **Maria**
- Data de nascimento: **07/07/2007**
- Endereço: Brasil, Ceará, rua Canuto de Aguiar, número 176, CEP 20252-060, complemento: apto 307
- CPF: 123456877-00

---

**Desafio 5 — Polimorfismo com sobrecarga**
Implemente uma classe `Principal` com dois métodos sobrecarregados chamados `maiorElem`:
- Um que recebe **2 inteiros** e retorna o maior
- Outro que recebe **3 inteiros** e retorna o maior (pode reutilizar o de 2)

---

**Desafio 6 — Classes abstratas: classe Leão**
Adicione ao exemplo da classe abstrata `Animal` uma nova subclasse chamada `Leao` que:
- Implementa `emitirSom()` imprimindo `"Rugir!"`
- Possui um método adicional `tipoDeAnimal()` que imprime `"É um animal selvagem"`

---

**Desafio 7 — Collectors e mapeamento**
Complete o trecho de código abaixo substituindo `???` por uma expressão lambda que calcule o quadrado de cada número:
```java
List<Integer> quadradoNumeros = map(numeros, ???);
```
O resultado esperado é:
```
Números Originais: [1, 2, 3, 4, 5]
Números ao Quadrado: [1, 4, 9, 16, 25]
```

---

Bons estudos! Se quiser que eu resolva algum dos desafios com você ou explique qualquer parte com mais detalhes, é só pedir.
