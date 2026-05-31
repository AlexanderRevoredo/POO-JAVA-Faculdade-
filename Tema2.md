Vou fazer um resumo completo do conteúdo e listar os desafios práticos mencionados!

---

## Herança em Java — Resumo Completo

---

### 1. Hierarquia de Herança

**O que é herança?** É a capacidade de uma classe transmitir seus métodos e atributos para classes descendentes. Em Java, usa-se a palavra-chave `extends`.

O relacionamento de herança é **vertical** e introduz a ideia de **"é um tipo de"**, diferente da associação (horizontal), que representa **"contém/possui"**.

- **Superclasse** → classe mais genérica (ex: `Pessoa`)
- **Subclasse** → classe mais especializada (ex: `Aluno extends Pessoa`)

A relação de herança se **propaga indefinidamente**: se `Aluno` deriva de `Fisica`, que deriva de `Pessoa`, então `Aluno` é também do tipo `Fisica` e do tipo `Pessoa`.

**Especialização** ocorre quando a subclasse particulariza comportamentos da superclasse, sobrescrevendo métodos.

---

### 2. Herança Múltipla e o "Diamante da Morte"

Java **não permite herança múltipla** de classes. O motivo é o chamado **problema do diamante da morte**: se uma classe `ProfessorJuridico` herdasse de `Fisica` e `Juridica` ao mesmo tempo, e ambas tivessem versões diferentes de `atualizarID()`, o compilador não saberia qual chamar — gerando ambiguidade irresolvível.

Em linguagens como C++, o programador resolve isso manualmente via cast explícito, aumentando o risco de erro. Java preferiu proibir isso.

---

### 3. Modificadores de Acesso

| Modificador | Na classe | No pacote | Subclasse fora do pacote | Fora do pacote |
|---|---|---|---|---|
| `private` | ✅ | ❌ | ❌ | ❌ |
| `default` | ✅ | ✅ | ❌ | ❌ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| `public` | ✅ | ✅ | ✅ | ✅ |

> `private` e `protected` só podem ser aplicados a classes **aninhadas** (inner classes).

---

### 4. Inner Classes (Classes Internas)

Java permite **aninhar classes** dentro de outras. As regras de herança se aplicam igualmente:

- Uma inner class pode ser estendida por outra inner class
- Uma inner class pode ser estendida por uma classe fora do aninhamento
- Os modificadores de acesso regulam a visibilidade dentro do aninhamento

Para instanciar uma inner class, é obrigatório primeiro instanciar a classe que a engloba:
```java
new Externa().new Interna()
```

---

### 5. Princípio da Substituição de Liskov (LSP)

> *"Se D deriva de B e D é subtipo de B, qualquer objeto do tipo B pode ser substituído por um do tipo D sem prejuízo ao programa."*

**Atenção:** subclasse e subtipo são conceitos distintos:
- **Subclasse** = derivação estrutural
- **Subtipo** = derivação que **mantém todas as propriedades** da superclasse

Quando uma subclasse altera o **contrato** da superclasse (ex: muda a assinatura ou o comportamento esperado de um método), ela não é um subtipo, violando o LSP.

O LSP faz parte dos **princípios SOLID** (Single responsibility, Open-closed, **Liskov substitution**, Interface segregation, Dependency inversion).

---

### 6. Java Collections Framework

Framework que oferece estruturas de dados prontas e testadas. Possui **duas hierarquias**:

**Hierarquia Collection:**
- `Iterable` → `Collection` → `Set`, `List`, `Queue`, `Deque`
- Implementações concretas: `TreeSet`, `ArrayList`, `LinkedList`, `PriorityQueue`, `Stack`...

**Hierarquia Map:**
- `Map` → `AbstractMap`, `SortedMap` → `HashMap`, `TreeMap`, `LinkedHashMap`...

**Diferenças entre as interfaces:**

| Interface | Característica |
|---|---|
| `Set` | Sem duplicatas |
| `List` | Ordenada, com duplicatas, acesso direto por índice |
| `Queue` | Fila (FIFO) ou lista de prioridade |
| `Deque` | Inserção/remoção em ambas as extremidades (FIFO ou LIFO) |

A `Collection` possui 15 métodos genéricos, como `add()`, `remove()`, `contains()`, `size()`, `isEmpty()`, `equals()`, etc.

---

### 7. Tipagem Estática vs. Dinâmica

| | Estática | Dinâmica |
|---|---|---|
| Quando o tipo é definido | Compilação | Execução |
| Exemplo | Java | JavaScript |

**Java é estaticamente tipada.** O `var` (introduzido no Java 10) **não é tipagem dinâmica** — o compilador ainda infere o tipo em tempo de compilação, e ele não pode mudar depois.

**Vinculação dinâmica (dynamic binding)** — quando um método é selecionado em tempo de execução com base no tipo real do objeto — **não é o mesmo que tipagem dinâmica**. É um mecanismo de polimorfismo.

---

### 8. Métodos da Classe Object

Em Java, **todas as classes herdam de `Object`**. Os principais métodos são:

#### `toString()`
Retorna uma representação textual do objeto no formato:
```
NomeQualificadoDaClasse@hashHexadecimal
```
É recomendado sobrescrever para dar um formato mais útil.

---

#### `equals(Object obj)`
Compara dois objetos. Por padrão, retorna `true` apenas se forem **o mesmo objeto** (`this == obj`).

Deve ser sobrescrito quando se quer comparar **conteúdo** dos objetos. Propriedades que devem ser mantidas:
- **Reflexividade**: `x.equals(x)` → sempre `true`
- **Simetria**: se `x.equals(y)` então `y.equals(x)`
- **Transitividade**: se `x.equals(y)` e `y.equals(z)` então `x.equals(z)`
- **Consistência**: resultados estáveis enquanto o objeto não muda
- `x.equals(null)` → sempre `false`

---

#### `hashCode()`
Retorna um inteiro que representa o objeto. Propriedades importantes:
1. Chamadas repetidas no mesmo objeto retornam o mesmo valor
2. **Objetos iguais devem ter o mesmo hash**
3. Objetos diferentes podem ter hashes iguais (mas é melhor evitar)

> ⚠️ Se você sobrescrever `equals()`, **obrigatoriamente** sobrescreva `hashCode()` também.

---

#### Operador `instanceof`
Verifica se um objeto é instância de um tipo específico:
```java
p1 instanceof Pessoa // true mesmo se p1 for do tipo Fisica
```
Retorna `true` também para instâncias de subclasses.

---

### 9. Acesso Protegido em Detalhe

Uma subclasse acessa métodos `protected` da superclasse **diretamente** (via herança), mesmo em pacotes diferentes. Porém, **não pode acessar** esses mesmos métodos **através de um objeto** da superclasse se estiver em pacote diferente. Esse é um ponto sutil e importante:

```java
// ✅ Funciona (acesso via herança):
CR = calcularCoeficienteRendimento();

// ❌ Erro (acesso via objeto de outro pacote):
CR = nota.calcularCoeficienteRendimento();
```

Além disso, métodos herdados com acesso `protected` **não ficam acessíveis** a outras classes do mesmo pacote da subclasse — apenas à própria subclasse e seus descendentes.

---

## Desafios Práticos

O material propõe vários desafios. Aqui estão todos eles:

---

**Desafio 1 — Modificador Default**
> Mova a classe `Pessoa` para um pacote diferente e remova o modificador `public`. Tente compilar o Código 8 (classe `Derivada` estendendo `Pessoa`). Observe o erro. Depois reinsira o `public` e compile novamente. Explique o que aconteceu e por quê.

---

**Desafio 2 — Inner Class com `protected`**
> Modifique a classe `Interna` (Código 12) adicionando o modificador `protected`. Também faça a classe `Execucao` estender `Externa`. Tente compilar. O que aconteceu? Por quê?

---

**Desafio 3 — Tornando `Externa` pública**
> Após o desafio anterior, mova `Externa` para um arquivo próprio e declare-a como `public`. Aplique as modificações do Código 15 e 16. Execute e analise a saída. Você consegue explicar cada linha impressa?

---

**Desafio 4 — Gerenciador de Registros com Collections**
> Expanda o exemplo com `TreeSet`, `PessoaFisica` e `Aluno` para criar um gerenciador de registros real. Adicione suporte a **pessoas jurídicas** também. Experimente trocar o `TreeSet` por outras estruturas (`ArrayList`, `LinkedList`, `HashMap`) e observe as diferenças de comportamento (ordenação, duplicatas, busca).

---

**Desafio 5 — Comportamento de `equals` cruzado**
> No exemplo do Código 42 (classe `Principal`), o material sugere: *"O que aconteceria se fizéssemos `A1.equals(PF1)`?"* Implemente e analise. Vai funcionar? Vai lançar erro? O resultado será `true` ou `false`? Explique o porquê.

---

**Desafio 6 — Validação completa de CPF**
> O método `validarCPF()` na classe `PessoaFisica` só verifica tamanho e dígitos repetidos. Implemente uma **validação real de CPF** com os dígitos verificadores. Você pode buscar o algoritmo na internet e adaptá-lo para o código existente.

---

**Desafio 7 — Exploração livre do código**
> O material encoraja modificar todos os exemplos e testar variações. Sugestões específicas:
> - Trocar `TreeSet` por `ArrayList` e observar que a ordenação some e duplicatas são permitidas
> - Adicionar novos atributos ao critério de comparação em `equals()` e `hashCode()` e verificar se o contrato se mantém
> - Criar uma nova subclasse (ex: `Professor`) e sobrescrever `toString()`, `equals()` e `hashCode()` de forma consistente

---

Se quiser, posso te ajudar a implementar qualquer um desses desafios!
