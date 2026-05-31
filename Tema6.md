# Integração com Banco de Dados em Java — Resumo Completo

## 1. Conceitos fundamentais: front-end, back-end e middleware

- **Front-end**: camada de interface da aplicação (o app Java).
- **Back-end**: banco de dados ou mensageria (ex: Derby, Oracle, SQL Server).
- **Middleware**: camada intermediária que conecta front-end e back-end de forma transparente, permitindo troca de fornecedor com pouca ou nenhuma alteração de código.
- No Java, o middleware para bancos de dados é o **JDBC**.

---

## 2. Banco de dados Derby

- Apache Derby (Java DB) é um banco relacional 100% Java, embutido no JDK.
- Gerenciado diretamente pelo NetBeans via aba **Services → Databases → Java DB**.
- Para criar: clique direito no driver Java DB → **Create Database**.
- Connection String padrão: `jdbc:derby://localhost:1527/nome_banco`

**Estrutura da tabela de exemplo (ALUNO):**

| Campo | Tipo | Complemento |
|---|---|---|
| MATRÍCULA | VARCHAR | Tamanho 10, chave primária |
| NOME | VARCHAR | Tamanho 40 |
| ENTRADA | INTEGER | Sem atributos complementares |

---

## 3. JDBC (Java Database Connectivity)

### Tipos de driver

| Tipo | Descrição |
|---|---|
| JDBC-ODBC Bridge | Conecta via Open Database Connectivity (ODBC) |
| JDBC-Native API | Usa o cliente do banco para a conexão |
| JDBC-Net | Acessa servidores de middleware via Sockets (3 camadas) |
| Pure Java (Thin Driver) | Implementação completa em Java, sem cliente instalado |

### Fluxo básico de uso (4 passos)

```java
// Passo 1 — carregar o driver
Class.forName("org.apache.derby.jdbc.ClientDriver");

// Passo 2 — obter a conexão
Connection c1 = DriverManager.getConnection(
    "jdbc:derby://localhost:1527/escola",
    "escola", "escola");

// Passo 3 — criar o executor de SQL
Statement st = c1.createStatement();

// Passo 4 — executar o comando SQL
st.executeUpdate("INSERT INTO ALUNO VALUES('E2018.5004','DENIS',2018)");
st.close();
c1.close();
```

> **Atenção:** use `executeQuery` para SELECT e `executeUpdate` para INSERT, UPDATE e DELETE. Feche os componentes na ordem inversa de criação.

### Consulta com ResultSet

```java
ResultSet r1 = st.executeQuery("SELECT * FROM ALUNO");
while (r1.next())
    System.out.println(r1.getString("NOME") + " - " + r1.getInt("ENTRADA"));
r1.close();
st.close();
c1.close();
```

O `ResultSet` inicia na posição BOF (beginning of file). Cada chamada a `next()` avança um registro até o EOF.

### PreparedStatement (parâmetros e proteção contra SQL Injection)

```java
PreparedStatement ps = c1.prepareStatement(
    "DELETE FROM ALUNO WHERE ENTRADA = ?");
ps.setInt(1, 2018);
ps.executeUpdate();
ps.close();
c1.close();
```

- Parâmetros são representados por `?` e indexados a partir de **1**.
- Use `setInt`, `setString`, etc., de acordo com o tipo do campo.

---

## 4. Mapeamento objeto-relacional

Cada tabela é representada por uma **classe de entidade** Java:

```java
public class Aluno {
    public String matricula;
    public String nome;
    public int ano;

    public Aluno() {}

    public Aluno(String matricula, String nome, int ano) {
        this.matricula = matricula;
        this.nome = nome;
        this.ano = ano;
    }
}
```

Populando uma coleção a partir do banco:

```java
List<Aluno> lista = new ArrayList<>();
ResultSet r1 = st.executeQuery("SELECT * FROM ALUNO");
while (r1.next())
    lista.add(new Aluno(
        r1.getString("MATRICULA"),
        r1.getString("NOME"),
        r1.getInt("ENTRADA")));
```

Listando com for-each:

```java
for (Aluno aluno : lista)
    System.out.println(aluno.nome + " (" + aluno.matricula + ") - " + aluno.ano);
```

> A conversão de tabelas e registros em coleções de entidades é chamada de **mapeamento objeto-relacional**.

---

## 5. Padrão DAO (Data Access Object)

Concentra todos os comandos SQL em uma única classe, melhorando organização, reuso e manutenção.

### Classe base genérica

```java
public abstract class GenericDAO<E, K> {
    protected Connection getConnection() throws Exception {
        Class.forName("org.apache.derby.jdbc.ClientDriver");
        return DriverManager.getConnection(
            "jdbc:derby://localhost:1527/escola",
            "escola", "escola");
    }
    protected Statement getStatement() throws Exception {
        return getConnection().createStatement();
    }
    protected void closeStatement(Statement st) throws Exception {
        st.getConnection().close();
    }

    public abstract void incluir(E entidade);
    public abstract void excluir(K chave);
    public abstract void alterar(E entidade);
    public abstract E obter(K chave);
    public abstract List<E> obterTodos();
}
```

> `E` = classe da entidade | `K` = classe da chave primária

### AlunoDAO — métodos principais

| Método DAO | Comando SQL |
|---|---|
| `obterTodos()` | SELECT * FROM ALUNO |
| `obter(chave)` | SELECT * FROM ALUNO WHERE MATRÍCULA = ? |
| `incluir(entidade)` | INSERT INTO ALUNO VALUES (?, ?, ?) |
| `alterar(entidade)` | UPDATE ALUNO SET NOME = ?, ENTRADA = ? WHERE MATRÍCULA = ? |
| `excluir(chave)` | DELETE FROM ALUNO WHERE MATRÍCULA = ? |

> A chave primária **nunca deve ser alterada** — regra seguida por qualquer framework de ORM.

Usando o DAO:

```java
AlunoDAO dao = new AlunoDAO();
dao.obterTodos().forEach(aluno -> System.out.println(aluno.nome));
```

---

## 6. JPA (Java Persistence Architecture)

Framework de persistência baseado em **anotações**, que automatiza o mapeamento objeto-relacional.

### Anotações principais

```java
@Entity
@Table(name = "ALUNO")
@NamedQueries({
    @NamedQuery(name = "Aluno.findAll", query = "SELECT a FROM Aluno a")
})
public class Aluno implements Serializable {
    @Id
    @Basic(optional = false)
    @Column(name = "MATRICULA")
    private String matricula;

    @Column(name = "NOME")
    private String nome;

    @Column(name = "ENTRADA")
    private Integer ano;

    // getters e setters
}
```

### Configuração — persistence.xml

Arquivo XML (em META-INF) que define o driver de persistência, a conexão e as entidades do esquema. Deve conter: `url`, `user`, `password`, `driver` e o provedor (ex: EclipseLink, Hibernate).

### Usando o EntityManager

```java
EntityManagerFactory emf =
    Persistence.createEntityManagerFactory("ExemploJavaDB01PU");
EntityManager em = emf.createEntityManager();
Query query = em.createNamedQuery("Aluno.findAll", Aluno.class);
List<Aluno> lista = query.getResultList();
lista.forEach(aluno -> System.out.println(aluno.getNome()));
```

### Equivalência de operações

| SQL | DAO | JpaController | EntityManager |
|---|---|---|---|
| INSERT | incluir | create | persist |
| UPDATE | alterar | edit | merge |
| DELETE | excluir | destroy | remove |
| SELECT | obterTodos / obter | findAlunoEntities | createNamedQuery |

---

## 7. Sistema cadastral e controle transacional

### Classe SistemaEscola (estrutura)

```java
public class SistemaEscola {
    private final AlunoDAO dao = new AlunoDAO();
    private static final BufferedReader entrada =
        new BufferedReader(new InputStreamReader(System.in));

    public void exibirTodos() {
        dao.obterTodos().forEach(aluno -> System.out.println(aluno.nome));
    }

    public void inserirAluno() throws IOException {
        Aluno aluno = new Aluno();
        System.out.println("Nome:"); aluno.nome = entrada.readLine();
        System.out.println("Matrícula:"); aluno.matricula = entrada.readLine();
        System.out.println("Entrada:"); aluno.ano = Integer.parseInt(entrada.readLine());
        dao.incluir(aluno);
    }

    public void excluirAluno() throws IOException {
        System.out.println("Matrícula:");
        dao.excluir(entrada.readLine());
    }
}
```

### Controle transacional — JDBC

```java
c1.setAutoCommit(false);
// ... operações SQL ...
c1.commit();
// em caso de erro:
c1.rollback();
```

### Controle transacional — JPA

```java
em.getTransaction().begin();
try {
    em.persist(aluno);
    em.getTransaction().commit();
} catch (Exception e) {
    em.getTransaction().rollback();
} finally {
    em.close();
}
```

### Geração automática no NetBeans

1. **Entity Classes from Database** → gera a entidade JPA anotada + `persistence.xml`
2. **JPA Controller Classes for Entity Classes** → gera o `AlunoJpaController` com todos os métodos CRUD
3. Adicionar manualmente a biblioteca **Java DB Driver** ao projeto

---

## Desafios práticos

### Roteiro 1 — JDBC básico

- [ ] Criar o banco Derby chamado "escola"
- [ ] Criar a tabela ALUNO (MATRÍCULA, NOME, ENTRADA)
- [ ] Incluir ao menos um aluno via código Java
- [ ] Realizar uma consulta SELECT e exibir os resultados no console
- [ ] Adicionar a biblioteca Java DB Driver ao projeto
- [ ] Implementar uma operação com `PreparedStatement`

### Roteiro 2 — Padrão DAO

- [ ] Criar a classe entidade `Aluno`
- [ ] Realizar leitura da tabela e popular um `List<Aluno>`
- [ ] Listar o conteúdo da coleção com for-each
- [ ] Criar a classe abstrata `GenericDAO<E, K>`
- [ ] Implementar a classe `AlunoDAO` com os métodos `obterTodos`, `obter`, `incluir`, `excluir` e `alterar`
- [ ] Testar todas as operações CRUD

### Roteiro 3 — Sistema completo com JPA

- [ ] Criar o projeto `ExemploEntidadeJPA` no NetBeans
- [ ] Gerar entidades JPA a partir do banco via "Entity Classes from Database"
- [ ] Gerar o `AlunoJpaController` via "JPA Controller Classes for Entity Classes"
- [ ] Criar a classe `SistemaEscola` adaptada para usar `AlunoJpaController`
- [ ] Adicionar manualmente a biblioteca Java DB Driver
- [ ] Executar e testar o sistema (listar, inserir, excluir)
- [ ] Incluir controle transacional com `commit` e `rollback`

---

*Referência: Integração com banco de dados em Java — Prof. Tomás de Aquino*
