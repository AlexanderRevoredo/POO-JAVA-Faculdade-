---

# Resumo — Polimorfismo em Java

---

## Contexto histórico

O conteúdo parte da crise do software dos anos 1960–70, quando sistemas estruturados se tornaram difíceis de manter. A resposta foi a Programação Orientada a Objetos (POO), centrada em reuso e manutenibilidade — contexto onde polimorfismo e interfaces ganham protagonismo.

---

## Módulo 1 — Polimorfismo em Java

### Classes e Métodos Abstratos

Uma **classe abstrata** não pode ser instanciada; seu papel é fornecer uma interface e comportamentos comuns para as subclasses. Em Java, usa-se o modificador `abstract`:

```java
public abstract class Pessoa { ... }
```

Um **método abstrato** não possui corpo e força a classe a ser abstrata. A implementação fica a cargo das subclasses:

```java
protected abstract void atualizarID(String identificador);
```

Pontos importantes:
- A abstração de um método se propaga pela hierarquia — uma subclasse que não implementar o método abstrato também será abstrata.
- Uma classe abstrata **pode** ter atributos, construtores e métodos concretos.
- Uma classe abstrata **pode** estender uma classe concreta, e vice-versa.

**Exemplo prático:** a classe `Pessoa` é abstrata, com o método `atualizarID` abstrato. As classes `Fisica` e `Juridica` o implementam cada uma com suas validações (CPF e CNPJ). Um vetor de referências do tipo `Pessoa` armazena objetos de ambas as subclasses — o sistema resolve em tempo de execução qual versão do método invocar. Isso é **polimorfismo**.

---

### 🧩 Desafio 1
> Uma classe abstrata em Java é declarada pelo uso do modificador `abstract`. Qual afirmação está correta?

**Resposta correta:** Uma classe abstrata não permite instanciação, mas não altera as regras de herança — ela pode estender uma classe concreta e ser estendida por uma.

---

### Métodos e Classes "final"

O modificador `final` restringe modificações:

- **Método `final`:** não pode ser redefinido (sobrescrito) nas subclasses.
- **Classe `final`:** não pode ter subclasses; todos os seus métodos são implicitamente `final`.
- **Variável `final`:** deve ser inicializada (na declaração ou no construtor) e não pode ter seu valor alterado depois.
- Métodos `static` e `private` são implicitamente `final`.
- O uso de `final` permite ao compilador realizar **otimizações de desempenho**.

---

### 🧩 Desafio 2
> Considere uma variável de referência `ref` declarada como `final`. O que ocorre ao tentar reatribuí-la?

**Resposta correta:** A variável `ref` é `final`, não o objeto. Ela pode ser usada para chamar métodos e alterar o estado do objeto, mas **não pode referenciar outra instância** — a tentativa gera erro de compilação.

---

### Upcasting e Downcasting

Operações que reinterpretam referências dentro de uma hierarquia de herança:

- **Upcasting:** referência da subclasse é reinterpretada como superclasse (implícito).
- **Downcasting:** referência da superclasse é reinterpretada como subclasse (explícito).

Essas operações **não mudam o objeto**, apenas a forma como o compilador o interpreta. O que é `private` continua inacessível independentemente do casting. Downcasting e upcasting só fazem sentido dentro da hierarquia — fora dela, trata-se de casting comum.

Sobre variáveis protegidas: uma variável `protected` compartilhada na hierarquia ocupa o **mesmo espaço de memória**, portanto alterações em uma subclasse refletem na superclasse.

---

### 🧩 Desafio 3
> Julgue: (I) Downcasting muda o tipo do objeto. (II) Pode-se fazer casting fora da hierarquia. (III) Upcasting não permite invocar métodos `private` da superclasse.

**Resposta correta:** Apenas **III** está correta. O casting não muda o objeto; casting fora da hierarquia não é down/upcasting; e restrições de acesso permanecem independente do casting.

---

### Polimorfismo em Prática

O polimorfismo ocorre quando um método é invocado por uma referência da superclasse e a versão especializada da subclasse é executada, com resolução em **tempo de execução** (vinculação dinâmica).

No exemplo com `Pessoa`, `Fisica` e `Juridica`:
- Um vetor de `Pessoa[]` armazena objetos de `Fisica` e `Juridica`.
- Ao iterar e chamar `recuperarNome()` e `recuperarID()`, cada objeto executa sua própria implementação automaticamente.

Benefício principal: **manutenibilidade** — adicionar uma nova subclasse não exige alterar o código que a consome.

---

### 🧩 Desafio 4
> Para que o comportamento polimórfico ocorra com `Derivada1` e `Derivada2`, como deve ser declarado o vetor na classe `Principal`?

**Resposta correta:** O vetor deve ser do tipo **`Base`** — referências da superclasse permitem o polimorfismo, pois o sistema resolve em tempo de execução qual versão do método invocar.

---

## Módulo 2 — A Entidade Interface de Java

### Conceito de Interface

Uma interface define um **contrato** de interação: especifica o que um objeto deve ser capaz de fazer, sem definir como. Analogia física: um mouse define quais interações são possíveis (botões, roda, movimento), mas cada modelo implementa isso à sua maneira.

Em POO, a interface garante certas propriedades de um objeto, isolando os detalhes de implementação do mundo exterior.

---

### Particularidades da Interface em Java

Declaração:
```java
public interface NomeDaInterface { ... }
```

Características:
- **Não pode ser instanciada** diretamente.
- Não admite atributos de instância — apenas **constantes** (`static final`).
- Pode conter: assinaturas de métodos abstratos, métodos `default`, métodos `static` e tipos aninhados.
- Apenas métodos `default` e `static` podem ter implementação.
- Todos os métodos são implicitamente **públicos**.
- Admite **herança múltipla** (uma interface pode estender mais de uma superinterface).
- Uma classe implementa uma interface com `implements` e pode implementar várias ao mesmo tempo.
- A classe que implementa deve implementar **todos** os métodos abstratos e não pode reduzir a visibilidade deles.

---

### 🧩 Desafio 5
> Sobre a entidade Interface de Java, qual afirmação está correta?

**Resposta correta:** Interfaces **admitem herança múltipla** — diferente das classes em Java, que só herdam de uma superclasse.

---

### Diferença entre Classe Abstrata e Interface

| | Classe Abstrata | Interface |
|---|---|---|
| Instanciação | Não | Não |
| Atributos de instância | Sim | Não (só constantes) |
| Membros privados/protegidos | Sim | Não |
| Herança múltipla | Não | Sim |
| Propósito | Define um tipo abstrato | Define um contrato/capacidades |

**Regra prática:** use interface para especificar capacidades; use classe abstrata para generalizar comportamentos e compartilhar código. Os dois conceitos são **complementares**, como demonstrado na API Java Collections.

---

### 🧩 Desafio 6
> Sobre interfaces em Java, qual afirmação está correta?

**Resposta correta:** Interfaces permitem a **declaração de funcionalidades** (métodos), e estas devem ser definidas (implementadas) nas classes que a implementam.

---

### 🧩 Desafio 7
> Um código com interface `iTeste` apresenta erro de compilação. Qual a correção?

**Resposta correta:** Substituir o corpo `{ }` do método na interface por `;` — interfaces não admitem definição de métodos comuns, mesmo que o corpo seja vazio.

---

## Módulo 3 — Interface Avançada em Java

### Aninhamento de Interfaces e Herança

Uma interface pode ser declarada dentro de outra (**interface aninhada**). Características:

- Interfaces aninhadas são implicitamente **estáticas**.
- Quando declarada dentro de outra interface, só pode ser **pública**.
- Quando declarada dentro de uma classe, pode ter qualquer modificador de acesso.
- **Não existe herança entre interface externa e interna** — ser membro não implica herdar métodos.
- O compilador gera um arquivo `.class` separado com o nome `Externa$Interna.class`.

Uma classe pode implementar uma interface aninhada de outro pacote desde que use a referência completa (`Externa.Interna`). Se a interface aninhada pertencer a uma **classe** (não a uma interface), a classe implementadora precisa **estender** a classe externa para ter acesso à interface membro.

---

### 🧩 Desafio 8
> Julgue: (I) Classe só implementa interface aninhada se implementar a externa. (II) Interface que estende aninhada também estende a externa. (III) Referência completa é necessária mesmo no mesmo pacote.

**Resposta correta:** Apenas **III** está correta — a interface aninhada só é acessível via referência à interface/classe externa, pois é membro dela.

---

### Programação Funcional e Interface Funcional

A partir do **Java 8**, foi introduzado suporte a expressões **lambda**, permitindo técnicas de programação funcional.

Uma **expressão lambda** é uma função anônima com sintaxe:
```java
(parâmetros) -> corpo
// Exemplos:
() -> 17;
x -> x / 10;
```

Uma **Interface Funcional** (ou SAM — *Single Abstract Method*) é uma interface com **exatamente um método abstrato**, incluindo os herdados. Pode ser anotada com `@FunctionalInterface` para validação em tempo de compilação.

Exemplos da API Java: `Comparator`, `Runnable`.

Numa hierarquia, se a superinterface é funcional, as subinterfaces também são — **até que uma delas declare um novo método abstrato**, a partir de onde violam o critério SAM e deixam de ser funcionais, assim como suas derivadas.

---

### 🧩 Desafio 9
> Julgue afirmativas sobre Interface Funcional.

**Resposta correta:** A condição para ser funcional é possuir apenas **um método abstrato** (incluindo herdados). Numa hierarquia, o método herdado da superinterface mantém as subinterfaces funcionais — até que uma delas adicione outro método abstrato, quebrando o critério SAM para ela e suas derivadas.

---

### 🧩 Desafio 10
> Um código com classe `IdUnico` (que contém interface privada aninhada `iCodigo`) e classe `Concreta` (que a estende) não compila. Qual a solução?

**Resposta correta:** É suficiente alterar o modificador da variável `seed` para **`protected`**. A interface `iCodigo` sendo privada aplica restrição apenas a ela, mas seu método `imprimeCod()` é público e pode ser implementado. O problema de compilação é o acesso à variável `seed` na subclasse.

---

## Resumo dos Tópicos Principais

1. **Classes abstratas** definem contratos e comportamentos comuns sem permitir instanciação.
2. **Métodos abstratos** adiam a implementação para as subclasses.
3. **`final`** impede redefinição de métodos, herança de classes e reatribuição de variáveis.
4. **Upcasting/Downcasting** reinterpretam referências dentro da hierarquia sem alterar o objeto.
5. **Polimorfismo** resolve em tempo de execução qual versão de um método invocar.
6. **Interfaces** definem contratos com herança múltipla, sem estado e sem implementação (exceto `default`/`static`).
7. **Interfaces aninhadas** são membros estáticos públicos da entidade externa — sem herança entre externa e interna.
8. **Programação funcional** é suportada via lambdas a partir do Java 8.
9. **Interface funcional (SAM)** possui exatamente um método abstrato e integra-se com lambdas.
