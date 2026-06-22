# Tema 6 — Integração com banco de dados em Java

## 1. Ideia central do tema

Até agora você estava trabalhando com objetos só na memória.

Exemplo:

```java
Aluno aluno = new Aluno("A001", "Alex", 2026);
```

Mas quando o programa fecha, esse objeto some.

Banco de dados serve para **persistir dados**, ou seja, guardar informações mesmo depois que o programa encerra.

Exemplo:

```text
Aluno
Matrícula: A001
Nome: Alex
Entrada: 2026
```

Esse aluno pode ser salvo em uma tabela e recuperado depois.

O Tema 6 mostra duas formas principais de fazer isso:

```text
JDBC → acesso direto ao banco usando SQL no Java.
JPA → acesso mais orientado a objetos, usando entidades e anotações.
```

---

## 2. Front-end, back-end e middleware

No material, o Java aparece como **front-end** da aplicação, no sentido de ser a parte que interage com o usuário e executa a lógica do programa.

O **back-end** é o recurso externo que fornece dados ou serviços, como banco de dados, mensageria etc.

Entre os dois entra o **middleware**.

```text
Java → Middleware → Banco de dados
```

O middleware é a camada que permite comunicação entre tecnologias diferentes. No caso do Java com banco de dados, o middleware principal é o **JDBC**. O PDF define middleware como tecnologia que permite integração transparente e mudança de fornecedor com pouca ou nenhuma alteração no código. 

---

## 3. JDBC

**JDBC** significa:

```text
Java Database Connectivity
```

Ele é o middleware do Java para acesso a banco de dados.

Com JDBC, seu código Java consegue se conectar a bancos como:

```text
Derby
Oracle
MySQL
PostgreSQL
SQL Server
DB2
```

A ideia é que você use classes Java para executar comandos SQL.

Exemplo mental:

```java
INSERT INTO ALUNO VALUES (...)
SELECT * FROM ALUNO
DELETE FROM ALUNO WHERE ...
```

O PDF destaca que o JDBC permite usar bancos de diversos fornecedores com pouca modificação no código, e recomenda evitar comandos específicos demais de um banco, preferindo SQL padronizado/ANSI. 

---

## 4. Banco Derby / Java DB

O material usa o banco **Derby**, também chamado de **Java DB**.

Ele é um banco relacional feito em Java. No exemplo do PDF, o banco criado se chama:

```text
escola
```

Usuário:

```text
escola
```

Senha:

```text
escola
```

A conexão fica parecida com:

```text
jdbc:derby://localhost:1527/escola
```

A tabela principal do exemplo é `ALUNO`, com campos:

```text
MATRICULA → VARCHAR tamanho 10, chave primária
NOME      → VARCHAR tamanho 40
ENTRADA   → INTEGER
```

O PDF mostra essa tabela sendo criada no NetBeans com a aba Services/Databases. 

---

## 5. Componentes principais do JDBC

O pacote usado é:

```java
import java.sql.*;
```

Os componentes principais são:

```text
DriverManager
Connection
Statement
PreparedStatement
ResultSet
```

### `DriverManager`

Gerencia a conexão com o banco.

```java
DriverManager.getConnection(...)
```

### `Connection`

Representa a conexão aberta com o banco.

```java
Connection c1 = DriverManager.getConnection(...);
```

### `Statement`

Executa comandos SQL simples.

```java
Statement st = c1.createStatement();
```

### `PreparedStatement`

Executa SQL com parâmetros.

```java
PreparedStatement ps = c1.prepareStatement(
    "DELETE FROM ALUNO WHERE ENTRADA = ?"
);
```

### `ResultSet`

Guarda o resultado de uma consulta `SELECT`.

```java
ResultSet r1 = st.executeQuery("SELECT * FROM ALUNO");
```

---

## 6. Passos básicos do JDBC

O PDF apresenta quatro passos para usar JDBC: carregar o driver, obter conexão, criar executor SQL e executar comandos DML. 

Exemplo:

```java
Class.forName("org.apache.derby.jdbc.ClientDriver");

Connection c1 = DriverManager.getConnection(
    "jdbc:derby://localhost:1527/escola",
    "escola",
    "escola"
);

Statement st = c1.createStatement();

st.executeUpdate(
    "INSERT INTO ALUNO VALUES('E2018.5004','DENIS',2018)"
);

st.close();
c1.close();
```

A ordem é:

```text
1. Carregar driver.
2. Abrir conexão.
3. Criar Statement.
4. Executar SQL.
5. Fechar recursos.
```

---

## 7. `executeUpdate()` versus `executeQuery()`

Essa diferença é muito importante.

### `executeUpdate()`

Usado para comandos que **alteram dados**:

```text
INSERT
UPDATE
DELETE
```

Exemplo:

```java
st.executeUpdate("INSERT INTO ALUNO VALUES('A001','ALEX',2026)");
```

### `executeQuery()`

Usado para comandos que **consultam dados**:

```text
SELECT
```

Exemplo:

```java
ResultSet r1 = st.executeQuery("SELECT * FROM ALUNO");
```

O PDF destaca justamente essa diferença: consultas usam `executeQuery`, enquanto comandos de manipulação usam `executeUpdate`. 

---

## 8. `ResultSet`

Quando você faz um `SELECT`, o resultado vem em um `ResultSet`.

Exemplo:

```java
ResultSet r1 = st.executeQuery("SELECT * FROM ALUNO");

while (r1.next()) {
    System.out.println(
        "Aluno: " + r1.getString("NOME") +
        " - " + r1.getInt("ENTRADA")
    );
}
```

O `ResultSet` começa antes do primeiro registro. Por isso você usa:

```java
r1.next()
```

para avançar.

Métodos comuns:

```java
getString("NOME")
getInt("ENTRADA")
getDouble("SALARIO")
```

O PDF explica que o `ResultSet` começa em BOF, antes do primeiro registro, e vai avançando com `next()` até EOF. 

---

## 9. Fechando recursos

Você precisa fechar os recursos depois de usar:

```java
r1.close();
st.close();
c1.close();
```

O material recomenda fechar os componentes JDBC em ordem inversa à criação, porque existe dependência entre eles. 

Ordem comum:

```text
ResultSet
Statement / PreparedStatement
Connection
```

---

## 10. Tipos de drivers JDBC

O PDF cita quatro tipos de driver JDBC. 

```text
JDBC-ODBC Bridge
JDBC-Native API
JDBC-Net
Pure Java
```

### JDBC-ODBC Bridge

Faz ponte usando ODBC.

### JDBC-Native API

Usa cliente nativo do banco instalado na máquina.

### JDBC-Net

Usa middleware via rede/sockets, arquitetura em três camadas.

### Pure Java

Driver totalmente feito em Java. Também chamado de **Thin Driver**.

Na prática, para prova:

```text
Pure Java / Thin Driver → não precisa de cliente nativo instalado.
JDBC-Native API → usa cliente do banco.
JDBC-Net → usa middleware via sockets.
JDBC-ODBC Bridge → usa ponte ODBC.
```

---

## 11. `PreparedStatement`

`PreparedStatement` é uma versão melhor e mais segura do `Statement`.

Em vez de montar SQL na mão assim:

```java
"DELETE FROM ALUNO WHERE ENTRADA = " + ano
```

você usa parâmetros:

```java
PreparedStatement ps = c1.prepareStatement(
    "DELETE FROM ALUNO WHERE ENTRADA = ?"
);

ps.setInt(1, 2018);
ps.executeUpdate();
```

O `?` é o parâmetro.

Importante:

```text
O índice dos parâmetros começa em 1, não em 0.
```

Exemplo:

```java
ps.setString(1, "A001");
ps.setInt(2, 2026);
```

O PDF destaca que `PreparedStatement` facilita comandos parametrizados, ajuda com conversões de tipos como datas e oferece proteção contra SQL Injection. 

---

## 12. SQL Injection

SQL Injection acontece quando o usuário consegue injetar comandos SQL dentro da entrada.

Exemplo ruim:

```java
String sql = "SELECT * FROM USUARIO WHERE LOGIN = '" + login + "'";
```

Se o usuário digitar algo malicioso, pode alterar o SQL final.

Com `PreparedStatement`, você evita isso:

```java
PreparedStatement ps = c1.prepareStatement(
    "SELECT * FROM USUARIO WHERE LOGIN = ?"
);

ps.setString(1, login);
```

Então, para prova:

```text
PreparedStatement é mais seguro que Statement para dados vindos do usuário.
```

---

## 13. Modelo relacional versus modelo orientado a objetos

Banco relacional trabalha com:

```text
tabelas
linhas/registros
colunas/campos
chaves primárias
relacionamentos
```

Java trabalha com:

```text
classes
objetos
atributos
métodos
referências
coleções
```

Existe uma diferença natural entre esses dois mundos.

Tabela:

```text
ALUNO
MATRICULA | NOME | ENTRADA
```

Classe Java:

```java
public class Aluno {
    public String matricula;
    public String nome;
    public int ano;
}
```

O PDF explica que, para converter entre os dois modelos, criamos uma classe de entidade para representar cada tabela; os atributos da classe são associados aos campos da tabela, e cada registro pode virar uma instância da classe. 

---

## 14. Mapeamento objeto-relacional

Mapeamento objeto-relacional é transformar dados da tabela em objetos Java.

Exemplo:

```java
List<Aluno> lista = new ArrayList<>();

ResultSet r1 = st.executeQuery("SELECT * FROM ALUNO");

while (r1.next()) {
    lista.add(new Aluno(
        r1.getString("MATRICULA"),
        r1.getString("NOME"),
        r1.getInt("ENTRADA")
    ));
}
```

Agora você não precisa trabalhar diretamente com tabela. Você trabalha com objetos:

```java
for (Aluno aluno : lista) {
    System.out.println(aluno.nome);
}
```

O PDF chama essa técnica de conversão de tabelas e registros em coleções de entidades de **mapeamento objeto-relacional**. 

---

## 15. Classe entidade

Uma entidade representa uma tabela.

Exemplo simples:

```java
public class Aluno {
    public String matricula;
    public String nome;
    public int ano;

    public Aluno() {
    }

    public Aluno(String matricula, String nome, int ano) {
        this.matricula = matricula;
        this.nome = nome;
        this.ano = ano;
    }
}
```

Aqui:

```text
Classe Aluno → tabela ALUNO
matricula    → campo MATRICULA
nome         → campo NOME
ano          → campo ENTRADA
```

---

## 16. DAO

**DAO** significa:

```text
Data Access Object
```

É um padrão para concentrar o acesso ao banco em classes específicas.

Sem DAO, você pode acabar espalhando SQL por todo o sistema:

```text
SQL na tela de cadastro
SQL na tela de relatório
SQL na classe principal
SQL no botão
SQL no menu
```

Com DAO, você coloca o SQL em uma classe própria:

```text
AlunoDAO
```

O PDF explica que o objetivo do DAO é concentrar instruções SQL em um único tipo de classe, agrupando e reutilizando comandos relacionados ao banco. 

---

## 17. `GenericDAO`

O material apresenta uma classe abstrata genérica:

```java
public abstract class GenericDAO<E, K> {

    protected Connection getConnection() throws Exception {
        Class.forName("org.apache.derby.jdbc.ClientDriver");

        return DriverManager.getConnection(
            "jdbc:derby://localhost:1527/escola",
            "escola",
            "escola"
        );
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

Aqui:

```text
E = classe da entidade
K = classe da chave primária
```

Exemplo:

```java
AlunoDAO extends GenericDAO<Aluno, String>
```

Significa:

```text
Entidade: Aluno
Chave: String
```

O PDF reforça que `GenericDAO` é abstrata e define assinaturas genéricas para operações de banco, enquanto as classes filhas implementam os detalhes específicos da entidade. 

---

## 18. `AlunoDAO`

`AlunoDAO` é a classe que acessa a tabela `ALUNO`.

Ela deve ter métodos como:

```java
incluir(Aluno aluno)
excluir(String matricula)
alterar(Aluno aluno)
obter(String matricula)
obterTodos()
```

Relação com SQL:

```text
obterTodos() → SELECT
obter()      → SELECT com WHERE
incluir()    → INSERT
alterar()    → UPDATE
excluir()    → DELETE
```

O PDF mostra que `obterTodos` executa `SELECT * FROM ALUNO` e monta uma lista de objetos `Aluno`; já `obter` usa `PreparedStatement` com `WHERE MATRICULA = ?`. 

Exemplo mental:

```java
AlunoDAO dao = new AlunoDAO();

List<Aluno> alunos = dao.obterTodos();

Aluno aluno = dao.obter("A001");

dao.incluir(new Aluno("A002", "Revo", 2026));

dao.excluir("A002");
```

---

## 19. Vantagem do DAO

A grande vantagem é manutenção.

Se mudar o banco de Derby para Oracle, você altera principalmente o método de conexão:

```java
getConnection()
```

Não precisa sair procurando SQL em todo o sistema.

O PDF comenta que essa estratégia facilita mudar o fornecedor do banco, porque o processo de conexão fica concentrado em apenas um método reutilizado pelo restante do código. 

---

## 20. Sistema cadastral simples

Depois de criar `AlunoDAO`, o PDF monta um sistema chamado:

```text
SistemaEscola
```

Ele usa:

```java
private final AlunoDAO dao = new AlunoDAO();
```

E entrada de dados com:

```java
BufferedReader entrada = new BufferedReader(
    new InputStreamReader(System.in)
);
```

Exemplo de método:

```java
public void inserirAluno() throws IOException {
    Aluno aluno = new Aluno();

    System.out.println("Nome:");
    aluno.nome = entrada.readLine();

    System.out.println("Matricula:");
    aluno.matricula = entrada.readLine();

    System.out.println("Entrada:");
    aluno.ano = Integer.parseInt(entrada.readLine());

    dao.incluir(aluno);
}
```

Também aparece `exibirTodos()` usando lambda:

```java
dao.obterTodos().forEach(aluno -> exibir(aluno));
```

O PDF mostra essa estrutura em modo texto, onde o sistema se preocupa com entrada/saída, enquanto o DAO cuida das operações no banco. 

---

## 21. Transações

Transação é um bloco de operações que deve ser tratado como uma unidade.

Exemplo: transferência bancária.

```text
1. Tirar dinheiro da conta A.
2. Colocar dinheiro na conta B.
```

Se a etapa 1 funcionar e a etapa 2 falhar, não pode deixar o sistema pela metade.

Então usamos:

```text
commit   → confirma
rollback → desfaz
```

O PDF explica que uma transação é iniciada, as operações são executadas, e ao final ocorre `commit` ou `rollback`. 

Exemplo JDBC:

```java
Connection c1 = null;

try {
    c1 = getConnection();
    c1.setAutoCommit(false);

    PreparedStatement ps = c1.prepareStatement(
        "DELETE FROM ALUNO WHERE MATRICULA = ?"
    );

    ps.setString(1, chave);
    ps.executeUpdate();

    c1.commit();

} catch (Exception e) {
    if (c1 != null) {
        c1.rollback();
    }
}
```

Por padrão, o banco pode confirmar automaticamente cada comando. Com:

```java
c1.setAutoCommit(false);
```

você desliga isso e passa a controlar manualmente com:

```java
commit()
rollback()
```

---

## 22. JPA

**JPA** significa:

```text
Java Persistence Architecture
```

Ela é uma arquitetura de persistência que usa **mapeamento objeto-relacional** com anotações.

Enquanto JDBC é mais manual, JPA é mais automático.

Com JDBC:

```java
SELECT * FROM ALUNO
ResultSet
getString
getInt
new Aluno(...)
```

Com JPA, você trabalha mais com objetos:

```java
em.persist(aluno);
em.remove(aluno);
em.merge(aluno);
```

O PDF diz que o JPA usa modelo de programação anotado para simplificar o mapeamento entre objetos Java e registros do banco. 

---

## 23. Entidade JPA

Exemplo de entidade JPA:

```java
@Entity
@Table(name = "ALUNO")
@NamedQueries({
    @NamedQuery(
        name = "Aluno.findAll",
        query = "SELECT a FROM Aluno a"
    )
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

    public Aluno() {
    }

    public Aluno(String matricula) {
        this.matricula = matricula;
    }

    // getters e setters
}
```

Principais anotações:

```java
@Entity
```

Transforma a classe em entidade persistente.

```java
@Table(name = "ALUNO")
```

Liga a classe à tabela `ALUNO`.

```java
@Id
```

Define a chave primária.

```java
@Column(name = "MATRICULA")
```

Liga o atributo a uma coluna da tabela.

```java
@Basic(optional = false)
```

Define obrigatoriedade do campo.

```java
@NamedQuery
```

Define uma consulta nomeada.

O PDF explica que `Entity` transforma a classe em entidade, `Table` escolhe a tabela, `Column` associa atributo ao campo, `Id` define a chave primária e `Basic` permite mapear obrigatoriedade. 

---

## 24. JPQL

JPQL é a linguagem de consulta do JPA.

Ela parece SQL, mas trabalha com **objetos**, não diretamente com tabelas.

SQL:

```sql
SELECT * FROM ALUNO
```

JPQL:

```java
SELECT a FROM Aluno a
```

O PDF destaca que JPQL retorna objetos em vez de registros e pode ser usado no código ou associado à classe por `NamedQuery`. 

---

## 25. `persistence.xml` e Persistence Unit

O JPA precisa de um arquivo de configuração:

```text
persistence.xml
```

Ele define a **Persistence Unit**, que guarda informações como:

```text
nome da unidade de persistência
driver
url do banco
usuário
senha
provedor de persistência
classes de entidade
```

Exemplo mental:

```text
Persistence Unit: ExemploJavaDB01PU
Driver: Derby
URL: jdbc:derby://localhost:1527/escola
User: escola
Password: escola
Provider: EclipseLink
Entity: Aluno
```

O PDF explica que a configuração da conexão é feita em arquivo XML chamado `persistence`, contendo propriedades equivalentes às usadas no JDBC, além do provedor de persistência e entidades. 

---

## 26. `EntityManagerFactory` e `EntityManager`

No JPA, os objetos centrais são:

```text
EntityManagerFactory
EntityManager
Query
```

### `EntityManagerFactory`

Cria `EntityManager`.

```java
EntityManagerFactory emf =
    Persistence.createEntityManagerFactory("ExemploJavaDB01PU");
```

### `EntityManager`

É o objeto central para operações de persistência.

```java
EntityManager em = emf.createEntityManager();
```

### `Query`

Representa uma consulta.

```java
Query query = em.createNamedQuery("Aluno.findAll", Aluno.class);
List lista = query.getResultList();
```

Exemplo completo:

```java
EntityManagerFactory emf =
    Persistence.createEntityManagerFactory("ExemploJavaDB01PU");

EntityManager em = emf.createEntityManager();

Query query = em.createNamedQuery("Aluno.findAll", Aluno.class);

List<Aluno> lista = query.getResultList();

lista.forEach(aluno -> {
    System.out.println(aluno.getNome());
});
```

O PDF mostra esse fluxo para listar nomes dos alunos usando `EntityManagerFactory`, `EntityManager`, `createNamedQuery` e `getResultList`. 

---

## 27. Transação com JPA

Com JPA, o controle transacional fica mais limpo:

```java
public void incluir(Aluno aluno) {
    EntityManagerFactory emf =
        Persistence.createEntityManagerFactory("ExemploJavaDB01PU");

    EntityManager em = emf.createEntityManager();

    em.getTransaction().begin();

    try {
        em.persist(aluno);
        em.getTransaction().commit();

    } catch (Exception e) {
        em.getTransaction().rollback();

    } finally {
        em.close();
    }
}
```

Fluxo:

```text
begin()
persist()
commit()
rollback() se der erro
close()
```

O PDF compara esse fluxo com JDBC e mostra que os princípios são parecidos, mas o código com JPA tende a ser mais simples. 

---

## 28. Operações JDBC, DAO, JpaController e EntityManager

O PDF apresenta uma relação importante: 

```text
SQL     → DAO      → JpaController → EntityManager

INSERT  → incluir  → create        → persist
UPDATE  → alterar  → edit          → merge
DELETE  → excluir  → destroy       → remove
SELECT  → obter    → find/query    → find/createQuery
```

Guarda isso porque é bem cara de questão.

---

## 29. NetBeans e geração automática JPA

O material mostra que o NetBeans consegue gerar muita coisa automaticamente.

Fluxo:

```text
New File
Entity Classes from Database
Selecionar conexão JDBC
Selecionar tabela ALUNO
Escolher pacote model
Criar persistence unit
Definir coleção como List
```

Depois:

```text
New File
JPA Controller Classes for Entity Classes
Selecionar entidade Aluno
Escolher pacote manager
Gerar AlunoJpaController
```

O PDF diz que o NetBeans possui gerador automático de entidades JPA e que basta conhecer a conexão JDBC com o banco para recuperar as tabelas e gerar as entidades. 

---

## 30. JDBC versus DAO versus JPA

Resumo bem direto:

```text
JDBC:
Você escreve SQL diretamente no código Java.

DAO:
Você ainda usa JDBC/SQL, mas organiza tudo em classes próprias.

JPA:
Você usa entidades/anotações e deixa boa parte do mapeamento para o framework.
```

Comparando:

```text
JDBC puro → mais manual, mais controle, mais código repetido.
DAO       → organiza o JDBC e centraliza SQL.
JPA       → mais automático, mais orientado a objetos.
```

O PDF conclui que JDBC é o middleware de acesso a banco do Java, enquanto JPA é arquitetura de persistência baseada em anotações; o uso de DAO surge para organizar os comandos SQL espalhados e preparar o caminho para ferramentas como JPA. 

---

# O que você precisa dominar para a prova

Se você souber isso, está bem no Tema 6:

```text
1. O que é persistência de dados.
2. Diferença entre front-end, back-end e middleware.
3. O que é JDBC.
4. O que é Derby / Java DB.
5. O que é Connection String.
6. Criar mentalmente a tabela ALUNO.
7. Saber os componentes DriverManager, Connection, Statement, PreparedStatement e ResultSet.
8. Saber o fluxo básico do JDBC.
9. Diferenciar executeQuery e executeUpdate.
10. Saber percorrer ResultSet com next().
11. Saber usar getString e getInt.
12. Saber fechar ResultSet, Statement e Connection.
13. Saber os quatro tipos de drivers JDBC.
14. Saber por que PreparedStatement é melhor que Statement.
15. Entender SQL Injection.
16. Entender mapeamento objeto-relacional.
17. Criar uma classe entidade Aluno.
18. Entender List<Aluno> preenchida a partir de ResultSet.
19. Entender o padrão DAO.
20. Saber para que serve GenericDAO.
21. Saber que E representa entidade e K representa chave.
22. Saber que AlunoDAO concentra SQL da tabela ALUNO.
23. Relacionar SELECT, INSERT, UPDATE e DELETE com obter, incluir, alterar e excluir.
24. Entender transações: commit e rollback.
25. Entender JPA.
26. Saber o papel de @Entity, @Table, @Id, @Column e @NamedQuery.
27. Entender persistence.xml / Persistence Unit.
28. Saber o que são EntityManagerFactory e EntityManager.
29. Saber que EntityManager faz persist, merge e remove.
30. Saber que NetBeans pode gerar entidades JPA e JpaController.
```

---

# Desafios para você resolver

## Desafio 1 — Classe entidade `Aluno`

Crie a classe:

```text
Aluno
```

Atributos:

```text
matricula
nome
entrada
```

Regras:

```text
Use private.
Crie construtor vazio.
Crie construtor com todos os atributos.
Crie getters e setters.
Crie toString().
```

---

## Desafio 2 — Simular tabela em memória

Crie:

```java
List<Aluno> alunos = new ArrayList<>();
```

Adicione 3 alunos.

Depois liste todos com:

```java
for (Aluno aluno : alunos) {
    System.out.println(aluno);
}
```

Objetivo: entender a ideia de tabela virando coleção de objetos.

---

## Desafio 3 — JDBC básico: conexão

Crie uma classe `ConexaoBD`.

Ela deve ter um método:

```java
public Connection getConnection() throws Exception
```

Dentro dele:

```java
Class.forName("org.apache.derby.jdbc.ClientDriver");

return DriverManager.getConnection(
    "jdbc:derby://localhost:1527/escola",
    "escola",
    "escola"
);
```

Objetivo: treinar `Class.forName`, `DriverManager` e `Connection`.

---

## Desafio 4 — Inserir aluno com `Statement`

Crie um método:

```java
public void inserirAluno()
```

Execute:

```sql
INSERT INTO ALUNO VALUES('A001','ALEX',2026)
```

Usando:

```java
Statement
executeUpdate()
```

Depois feche:

```java
st.close();
connection.close();
```

---

## Desafio 5 — Consultar alunos com `ResultSet`

Crie um método:

```java
public void listarAlunos()
```

Execute:

```sql
SELECT * FROM ALUNO
```

Obrigatório usar:

```java
executeQuery()
ResultSet
while (rs.next())
getString("NOME")
getInt("ENTRADA")
```

---

## Desafio 6 — `PreparedStatement`

Crie um método:

```java
public void excluirPorEntrada(int ano)
```

SQL:

```sql
DELETE FROM ALUNO WHERE ENTRADA = ?
```

Obrigatório usar:

```java
PreparedStatement
setInt(1, ano)
executeUpdate()
```

Depois responda em comentário:

```java
// Por que PreparedStatement é melhor que concatenar String?
```

---

## Desafio 7 — DAO simples

Crie:

```text
AlunoDAO
```

Métodos:

```java
void incluir(Aluno aluno)
void excluir(String matricula)
Aluno obter(String matricula)
List<Aluno> obterTodos()
```

Obrigatório:

```text
SQL só pode ficar dentro do AlunoDAO.
A classe Principal não pode ter SQL.
```

Objetivo: entender DAO.

---

## Desafio 8 — `GenericDAO`

Crie:

```java
public abstract class GenericDAO<E, K>
```

Com métodos abstratos:

```java
public abstract void incluir(E entidade);
public abstract void excluir(K chave);
public abstract void alterar(E entidade);
public abstract E obter(K chave);
public abstract List<E> obterTodos();
```

Depois faça:

```java
public class AlunoDAO extends GenericDAO<Aluno, String>
```

Objetivo: treinar generics + DAO + herança + classe abstrata.

---

## Desafio 9 — SistemaEscola modo texto

Crie:

```text
SistemaEscola
Principal
Aluno
AlunoDAO
```

O menu deve ter:

```text
1 - Inserir aluno
2 - Excluir aluno
3 - Buscar aluno
4 - Listar todos
0 - Sair
```

Use:

```java
BufferedReader
InputStreamReader
switch
AlunoDAO
```

---

## Desafio 10 — Transação JDBC

No método `excluir`, use transação:

```java
connection.setAutoCommit(false);
```

Se der certo:

```java
connection.commit();
```

Se der erro:

```java
connection.rollback();
```

Obrigatório usar:

```java
try
catch
finally
commit
rollback
```

---

## Desafio 11 — Entidade JPA

Transforme sua classe `Aluno` em entidade JPA:

```java
@Entity
@Table(name = "ALUNO")
public class Aluno implements Serializable {
}
```

Use:

```java
@Id
@Column(name = "MATRICULA")
private String matricula;
```

E:

```java
@Column(name = "NOME")
private String nome;

@Column(name = "ENTRADA")
private Integer entrada;
```

---

## Desafio 12 — Consulta JPQL

Crie uma `NamedQuery`:

```java
@NamedQuery(
    name = "Aluno.findAll",
    query = "SELECT a FROM Aluno a"
)
```

Depois, no código:

```java
Query query = em.createNamedQuery("Aluno.findAll", Aluno.class);
List<Aluno> alunos = query.getResultList();
```

Liste os nomes.

---

## Desafio 13 — Operações com `EntityManager`

Crie métodos JPA:

```java
void incluir(Aluno aluno)
void alterar(Aluno aluno)
void excluir(Aluno aluno)
```

Use:

```java
em.persist(aluno);
em.merge(aluno);
em.remove(aluno);
```

Com transação:

```java
em.getTransaction().begin();
em.getTransaction().commit();
em.getTransaction().rollback();
```

---

## Desafio 14 — Tabela de comparação

Faça uma tabela em comentário no código:

```text
SQL     DAO       JPA Controller    EntityManager
INSERT  incluir   create            persist
UPDATE  alterar   edit              merge
DELETE  excluir   destroy           remove
SELECT  obter     find              find/query
```

Objetivo: fixar o mapa de comandos.

---

## Desafio 15 — Projeto chefão do Tema 6

Crie um mini sistema:

```text
SistemaEscolaBanco
```

Classes:

```text
Aluno
GenericDAO
AlunoDAO
SistemaEscola
Principal
```

Obrigatório ter:

```text
Connection
DriverManager
Statement
PreparedStatement
ResultSet
executeQuery
executeUpdate
List<Aluno>
DAO
GenericDAO<E, K>
commit
rollback
try/catch/finally
```

Funções:

```text
Cadastrar aluno
Buscar por matrícula
Listar todos
Alterar nome/ano
Excluir aluno
```
