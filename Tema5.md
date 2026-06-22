# Tema 5 — Programação paralela em Java: threads

## 1. Ideia central do tema

Programação paralela é quando um programa consegue executar **mais de uma tarefa ao mesmo tempo**, ou pelo menos aparentar isso.

Exemplo simples:

```text
Tarefa 1: baixar arquivo
Tarefa 2: atualizar barra de progresso
Tarefa 3: permitir o usuário clicar em botões
```

Sem threads, uma tarefa pode bloquear a outra.

Com threads, você consegue ter várias linhas de execução dentro do mesmo programa.

O PDF define thread como uma **linha de execução dentro de um processo**, também chamada de processo leve. 

---

## 2. Processo versus thread

Um **processo** é um programa em execução.

Exemplo:

```text
Chrome aberto
IntelliJ aberto
Spotify aberto
```

Cada processo tem seu próprio espaço de memória.

Uma **thread** é uma linha de execução dentro de um processo.

Exemplo:

```text
Processo: navegador
  Thread 1: interface
  Thread 2: carregamento de página
  Thread 3: áudio/vídeo
  Thread 4: download
```

Resumo:

```text
Processo = programa em execução, com memória própria.
Thread = linha de execução dentro de um processo.
```

Diferença importante:

```text
Processos são mais isolados.
Threads compartilham memória dentro do mesmo processo.
```

Isso é bom para desempenho, mas perigoso para dados compartilhados.

---

## 3. CPU de um núcleo versus CPU multinúcleo

O PDF usa a ideia de CPU de núcleo único e CPU multinúcleo.

Em uma CPU de **núcleo único**, duas tarefas não executam literalmente ao mesmo tempo. O sistema operacional alterna rapidamente entre elas. Isso cria a ilusão de simultaneidade.

Esse processo é feito pelo **escalonador**, que decide qual processo/thread vai usar a CPU por um período de tempo. Quando esse tempo acaba, o estado da CPU é salvo e outro processo/thread entra em execução. Isso é chamado de **troca de contexto**. 

Em uma CPU **multinúcleo**, várias threads podem realmente executar ao mesmo tempo, cada uma em um núcleo diferente.

Resumo:

```text
Um núcleo → alternância rápida, ilusão de paralelismo.
Vários núcleos → execução realmente paralela pode acontecer.
```

---

## 4. Multitarefa preemptiva

O sistema operacional moderno usa **multitarefa preemptiva**.

Isso significa que o sistema pode interromper uma tarefa para executar outra.

Exemplo mental:

```text
Thread A começa
Sistema interrompe A
Thread B executa
Sistema interrompe B
Thread A continua
```

Você, programador, não controla exatamente essa ordem.

Por isso, com threads, a saída pode variar:

```java
Thread 1 executou
Thread 2 executou
```

ou:

```java
Thread 2 executou
Thread 1 executou
```

E as duas saídas podem estar corretas.

---

## 5. Criando thread em Java

Existem dois jeitos principais.

## Jeito 1 — estender `Thread`

```java
public class MinhaThread extends Thread {

    @Override
    public void run() {
        System.out.println("Thread executando...");
    }
}
```

Uso:

```java
public class Principal {
    public static void main(String[] args) {
        MinhaThread t1 = new MinhaThread();
        t1.start();
    }
}
```

O método importante é:

```java
start()
```

Ele inicia a thread e faz o Java chamar o método:

```java
run()
```

Cuidado: se você chamar `run()` diretamente, não cria uma nova thread.

Errado para criar paralelismo:

```java
t1.run();
```

Certo:

```java
t1.start();
```

O PDF destaca que `start()` inicia a execução da thread e passa a executar `run()`, enquanto `run()` modela o comportamento realizado pela thread. 

---

## 6. Criando thread com `Runnable`

Esse é o jeito mais flexível.

```java
public class MinhaTarefa implements Runnable {

    @Override
    public void run() {
        System.out.println("Tarefa executando...");
    }
}
```

Uso:

```java
public class Principal {
    public static void main(String[] args) {
        MinhaTarefa tarefa = new MinhaTarefa();

        Thread t1 = new Thread(tarefa);
        t1.start();
    }
}
```

Aqui você separa:

```text
Runnable = a tarefa
Thread = quem executa a tarefa
```

Isso é melhor porque Java só permite herdar de uma classe, mas permite implementar várias interfaces.

Então, se sua classe já herda de outra, você ainda pode fazer:

```java
implements Runnable
```

---

## 7. `run()` versus `start()`

Esse ponto cai fácil.

```java
t.run();
```

Executa o método como um método comum, na thread atual.

```java
t.start();
```

Cria uma nova linha de execução e chama `run()` internamente.

Exemplo:

```java
public class MinhaThread extends Thread {
    @Override
    public void run() {
        System.out.println("Executando: " + Thread.currentThread().getName());
    }
}
```

```java
public class Principal {
    public static void main(String[] args) {
        MinhaThread t = new MinhaThread();

        t.run();
        t.start();
    }
}
```

A primeira chamada roda na thread principal.
A segunda roda em uma nova thread.

---

## 8. `Thread.currentThread()`

Esse método retorna a thread que está executando naquele momento.

```java
System.out.println(Thread.currentThread().getName());
```

Exemplo:

```java
public class Principal {
    public static void main(String[] args) {
        System.out.println("Thread atual: " + Thread.currentThread().getName());
    }
}
```

Geralmente imprime:

```text
Thread atual: main
```

A thread `main` é a primeira thread do programa Java.

---

## 9. Nomeando threads

Você pode dar nome para uma thread:

```java
Thread t1 = new Thread(tarefa);
t1.setName("Minha Thread 1");
t1.start();
```

Ou no construtor:

```java
Thread t1 = new Thread(tarefa, "Minha Thread 1");
```

Dentro do `run()`:

```java
System.out.println(Thread.currentThread().getName());
```

O PDF usa exemplos em que a thread recebe nome com `setName()` e também usa `getId()` para listar identificadores de threads executadas. 

---

## 10. `sleep()`

O método `sleep()` pausa temporariamente a thread atual.

```java
Thread.sleep(1000);
```

Isso pausa por aproximadamente 1000 milissegundos, ou seja, 1 segundo.

Exemplo:

```java
public class MinhaTarefa implements Runnable {

    @Override
    public void run() {
        try {
            System.out.println("Começou");
            Thread.sleep(2000);
            System.out.println("Terminou");
        } catch (InterruptedException e) {
            System.out.println("Thread interrompida.");
        }
    }
}
```

`sleep()` pode lançar `InterruptedException`, então precisa de `try/catch` ou `throws`.

O PDF explica que `sleep(long millis)` suspende temporariamente a execução da thread pelo tempo informado, mas o tempo real pode variar por causa do escalonador e da resolução dos temporizadores. 

---

## 11. `join()`

`join()` faz uma thread esperar outra terminar.

Exemplo:

```java
Thread t1 = new Thread(() -> {
    System.out.println("Tarefa 1 começou");
    try {
        Thread.sleep(2000);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    System.out.println("Tarefa 1 terminou");
});

t1.start();

t1.join();

System.out.println("Agora a main continua");
```

Sem `join()`, a `main` poderia continuar antes da `t1` terminar.

Com `join()`, a `main` espera.

O PDF explica justamente esse caso: se a Thread A precisa aguardar a Thread B finalizar, a chamada `B.join()` faz A esperar até B terminar; também existem versões com timeout, como `join(long millis)`. 

---

## 12. `setDaemon()`

Uma thread daemon é uma thread de apoio, que não impede o programa de encerrar.

Exemplo:

```java
Thread t = new Thread(tarefa);
t.setDaemon(true);
t.start();
```

Cuidado: `setDaemon(true)` precisa ser chamado **antes** do `start()`.

O PDF diz que `setDaemon(true)` marca a thread como daemon, `false` marca como thread de usuário, e isso precisa ser feito antes da thread iniciar. 

---

## 13. `yield()`

`yield()` sugere ao escalonador:

```text
Pode dar chance para outra thread executar agora.
```

Exemplo:

```java
Thread.yield();
```

Mas é só uma sugestão. Você não deve depender dele para lógica importante.

---

## 14. `stop()` é depreciado

Existe o método:

```java
stop()
```

Mas ele é inseguro e deve ser evitado.

O PDF comenta que `stop()` está depreciado desde a versão 1.2, porque seu uso pode causar problemas com monitores e travas. 

Na prática, para parar uma thread, o ideal é usar uma variável de controle ou interrupção.

Exemplo simples:

```java
class Tarefa implements Runnable {
    private boolean executando = true;

    public void parar() {
        executando = false;
    }

    @Override
    public void run() {
        while (executando) {
            System.out.println("Rodando...");
        }
    }
}
```

---

# 15. Ciclo de vida de uma thread

Estados importantes:

```text
New
Runnable
Blocked
Waiting
Timed Waiting
Terminated
```

O PDF destaca uma questão importante: o estado **Runnable** significa que a thread está pronta para ser executada pelo escalonador, mas ainda não necessariamente está executando naquele exato instante. 

## `New`

A thread foi criada, mas ainda não começou.

```java
Thread t = new Thread(tarefa);
```

Aqui está em `New`.

## `Runnable`

Depois de:

```java
t.start();
```

ela fica pronta para executar.

## `Blocked`

A thread está bloqueada esperando acesso a uma região sincronizada.

## `Waiting`

A thread está esperando indefinidamente, por exemplo com:

```java
wait()
join()
```

## `Timed Waiting`

A thread está esperando por tempo limitado, por exemplo:

```java
Thread.sleep(1000);
join(1000);
```

## `Terminated`

A thread terminou sua execução.

---

# 16. O maior perigo: dados compartilhados

Threads compartilham memória.

Isso significa que duas threads podem tentar mexer na mesma variável ao mesmo tempo.

Exemplo perigoso:

```java
public class Contador {
    private int valor = 0;

    public void incrementar() {
        valor++;
    }

    public int getValor() {
        return valor;
    }
}
```

Parece simples, mas:

```java
valor++;
```

não é uma operação única. Ela envolve algo como:

```text
ler valor
somar 1
salvar valor
```

Se duas threads fizerem isso ao mesmo tempo, pode dar problema.

---

# 17. Condição de corrida

**Condição de corrida** acontece quando o resultado depende da ordem em que as threads executam.

Exemplo:

```java
contador++;
```

Duas threads podem ler o mesmo valor antes de uma salvar o incremento.

Imagine:

```text
contador = 0

Thread A lê 0
Thread B lê 0
Thread A soma 1 e salva 1
Thread B soma 1 e salva 1
```

Resultado esperado:

```text
2
```

Resultado real:

```text
1
```

Esse é o tipo de erro clássico de concorrência.

O PDF explica que chamadas concorrentes podem levar a condição de corrida, e uma forma de impedir é usar `synchronized`, garantindo que apenas uma execução ocorra ao mesmo tempo. 

---

# 18. Região crítica

Região crítica é a parte do código que acessa recurso compartilhado.

Exemplo:

```java
contador++;
```

ou:

```java
saldo -= valor;
```

ou:

```java
lista.add(item);
```

Se várias threads acessam isso ao mesmo tempo, pode dar inconsistência.

A solução é proteger a região crítica.

---

# 19. `synchronized`

`synchronized` cria exclusão mútua.

Ou seja:

```text
Só uma thread por vez pode executar aquele trecho.
```

Exemplo:

```java
public class Contador {
    private int contador = 0;

    public synchronized void incrementar() {
        contador++;
    }

    public synchronized int getContador() {
        return contador;
    }
}
```

Agora, se duas threads chamarem `incrementar()`, uma espera a outra terminar.

O PDF explica que Java implementa o conceito de monitor com a palavra reservada `synchronized`; ela pode ser usada em método ou em bloco de código, e cada objeto tem um monitor associado que pode ser travado/destravado por uma thread. 

---

# 20. Método sincronizado

Exemplo:

```java
public synchronized void incrementar() {
    contador++;
}
```

Isso trava o monitor do objeto atual.

É como se fosse:

```java
public void incrementar() {
    synchronized (this) {
        contador++;
    }
}
```

Se o método for `static synchronized`, o lock é na classe, não em uma instância específica.

O PDF destaca essa diferença: método de instância trava o monitor da instância; método `static` trava o monitor associado ao objeto `Class` da classe. 

---

# 21. Bloco sincronizado

Você também pode sincronizar só uma parte do código.

```java
public void incrementar() {
    synchronized (this) {
        contador++;
    }
}
```

Ou usando outro objeto como trava:

```java
private final Object lock = new Object();

public void incrementar() {
    synchronized (lock) {
        contador++;
    }
}
```

Isso é útil quando você não quer travar o método inteiro, só um trecho crítico.

---

# 22. Monitor

Monitor é o mecanismo que controla a entrada em uma região crítica.

Pensa assim:

```text
Objeto = sala
Monitor = chave da sala
Thread = pessoa tentando entrar
```

Se uma thread pegou a chave, outra precisa esperar.

Quando a thread sai, libera a chave.

Em Java, `synchronized` usa esse mecanismo por trás.

---

# 23. `wait()`, `notify()` e `notifyAll()`

Esses métodos são usados para cooperação entre threads.

Eles pertencem a `Object`, então todo objeto tem.

## `wait()`

A thread libera a trava e fica esperando.

```java
wait();
```

## `notify()`

Acorda uma thread que está esperando.

```java
notify();
```

## `notifyAll()`

Acorda todas as threads que estão esperando.

```java
notifyAll();
```

O PDF explica que todo objeto possui um **wait-set**, usado para cooperação entre threads; `wait()` adiciona a thread ao conjunto de espera, `notify()` acorda uma thread, e `notifyAll()` acorda todas, embora só uma consiga obter o monitor por vez. 

Importante: esses métodos precisam ser chamados dentro de contexto sincronizado.

Exemplo:

```java
synchronized (objeto) {
    objeto.wait();
}
```

---

# 24. Deadlock

Deadlock é quando duas ou mais threads ficam travadas para sempre esperando recursos umas das outras.

Exemplo mental:

```text
Thread A pegou Recurso 1 e espera Recurso 2.
Thread B pegou Recurso 2 e espera Recurso 1.
```

Nenhuma das duas anda.

O PDF define deadlock como uma situação em que duas ou mais threads ficam bloqueadas indefinidamente, aguardando recursos exclusivos que estão com outra thread. 

Exemplo conceitual:

```java
synchronized (lock1) {
    synchronized (lock2) {
        // ...
    }
}
```

Em outra thread:

```java
synchronized (lock2) {
    synchronized (lock1) {
        // ...
    }
}
```

Isso pode gerar deadlock.

Para evitar:

```text
sempre pegar locks na mesma ordem
evitar locks aninhados desnecessários
usar timeout quando possível
reduzir região crítica
```

---

# 25. Semáforo

`Semaphore` controla quantas threads podem acessar um recurso ao mesmo tempo.

Import:

```java
import java.util.concurrent.Semaphore;
```

Exemplo:

```java
Semaphore semaforo = new Semaphore(3);
```

Isso permite até 3 acessos simultâneos.

Uso:

```java
semaforo.acquire();

try {
    // região crítica
} finally {
    semaforo.release();
}
```

`acquire()` pega uma permissão.
`release()` libera uma permissão.

O PDF explica que `Semaphore` existe desde Java 5; `acquire()` solicita acesso a um recurso/região crítica e bloqueia até uma permissão estar disponível, enquanto `release()` libera o recurso pela thread. 

Exemplo completo:

```java
import java.util.concurrent.Semaphore;

public class ExemploSemaforo {

    private static final Semaphore semaforo = new Semaphore(2);

    public static void main(String[] args) {
        Runnable tarefa = () -> {
            try {
                semaforo.acquire();

                System.out.println(Thread.currentThread().getName() + " entrou");
                Thread.sleep(2000);
                System.out.println(Thread.currentThread().getName() + " saiu");

            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                semaforo.release();
            }
        };

        for (int i = 1; i <= 5; i++) {
            new Thread(tarefa, "Thread " + i).start();
        }
    }
}
```

Aqui só 2 threads entram por vez.

---

# 26. Semáforo com `fair`

Você pode criar um semáforo com fila justa:

```java
Semaphore sem = new Semaphore(50, true);
```

O `true` indica que ele usa uma fila mais justa, geralmente FIFO.

O PDF explica que o construtor sobrecarregado permite definir a “justeza” ou `fair` do semáforo; se for `true`, usa fila FIFO para threads em espera. 

---

# 27. `CountDownLatch`

`CountDownLatch` serve para uma thread esperar várias operações terminarem.

Import:

```java
import java.util.concurrent.CountDownLatch;
```

Exemplo:

```java
CountDownLatch latch = new CountDownLatch(3);
```

Isso significa:

```text
Espere 3 contagens terminarem.
```

Cada thread chama:

```java
latch.countDown();
```

A thread principal pode chamar:

```java
latch.await();
```

Exemplo:

```java
import java.util.concurrent.CountDownLatch;

public class ExemploLatch {
    public static void main(String[] args) throws InterruptedException {

        CountDownLatch latch = new CountDownLatch(3);

        Runnable tarefa = () -> {
            try {
                System.out.println(Thread.currentThread().getName() + " trabalhando");
                Thread.sleep(1000);
                System.out.println(Thread.currentThread().getName() + " terminou");
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                latch.countDown();
            }
        };

        new Thread(tarefa, "Thread 1").start();
        new Thread(tarefa, "Thread 2").start();
        new Thread(tarefa, "Thread 3").start();

        latch.await();

        System.out.println("Todas terminaram.");
    }
}
```

No PDF, `CountDownLatch` aparece no exemplo da empacotadora, junto com `Semaphore`, para controlar conclusão de tarefas e liberação de recursos. 

---

# 28. Exemplo do PDF: empacotadora

O PDF traz um exemplo mais completo de uma empresa/empacotadora.

A ideia geral é:

```text
Empresa tem produtos para empacotar.
Equipes trabalham em paralelo.
Empacotadores executam tarefas.
Fitas são recursos limitados.
Semáforo controla acesso às fitas.
Latch ajuda a esperar empacotadores terminarem.
Contadores precisam ser sincronizados.
```

A classe `Empacotador` implementa `Runnable`, tem um método `run()`, usa `Thread.sleep()` para simular trabalho e chama métodos da equipe ao terminar. O PDF também mostra uma classe `ContadorSinc` com métodos `synchronized`, como `incrementar()`, `decrementar()` e `getContador()`, para evitar problemas de concorrência. 

Esse exemplo é importante porque junta:

```text
Runnable
Thread
sleep
synchronized
Semaphore
CountDownLatch
recurso compartilhado
condição de corrida
```

---

# 29. `InterruptedException`

Vários métodos de thread podem lançar `InterruptedException`, por exemplo:

```java
Thread.sleep(1000);
thread.join();
semaforo.acquire();
latch.await();
```

Por isso aparece muito:

```java
try {
    Thread.sleep(1000);
} catch (InterruptedException e) {
    e.printStackTrace();
}
```

Interrupção é uma forma de sinalizar que uma thread deve parar de esperar ou encerrar alguma tarefa.

---

# 30. Ordem de execução não é garantida

Esse ponto é essencial.

Código:

```java
new Thread(() -> System.out.println("A")).start();
new Thread(() -> System.out.println("B")).start();
new Thread(() -> System.out.println("C")).start();
```

Pode imprimir:

```text
A
B
C
```

Mas também pode imprimir:

```text
C
A
B
```

Ou qualquer outra ordem.

O escalonador decide.

Com thread, você não deve depender da ordem, a menos que use mecanismos como:

```text
join
synchronized
wait/notify
Semaphore
CountDownLatch
```

---

# 31. Concorrência versus paralelismo

Essas palavras aparecem muito.

## Concorrência

Várias tarefas estão em andamento no mesmo período, mas não necessariamente executando no mesmo instante.

Exemplo:

```text
Um atendente alterna entre vários clientes.
```

## Paralelismo

Várias tarefas executam realmente ao mesmo tempo.

Exemplo:

```text
Vários atendentes atendem vários clientes ao mesmo tempo.
```

No computador:

```text
Concorrência pode ocorrer com um núcleo.
Paralelismo real precisa de múltiplos núcleos.
```

---

# 32. Quando usar threads?

Bons casos:

```text
interface gráfica que não pode travar
servidores atendendo vários clientes
processamento pesado dividido em partes
downloads simultâneos
jogos
processamento de imagem
simulações
```

Casos ruins:

```text
programa simples demais
tarefa rápida
quando o custo de criar/controlar threads é maior que o ganho
quando você não sabe proteger dados compartilhados
```

Threads podem melhorar desempenho, mas também podem aumentar complexidade.

O próprio PDF reforça que programação paralela é desafiadora, porque pensar sequencialmente é fácil, mas múltiplas linhas de execução criam problemas inexistentes em um programa de uma única linha. 

---

# 33. Código base para estudar threads

Esse aqui é um bom exemplo para você decorar:

```java
public class MinhaTarefa implements Runnable {

    @Override
    public void run() {
        System.out.println("Executando: " + Thread.currentThread().getName());
    }
}
```

```java
public class Principal {
    public static void main(String[] args) {

        Thread t1 = new Thread(new MinhaTarefa(), "Thread 1");
        Thread t2 = new Thread(new MinhaTarefa(), "Thread 2");

        t1.start();
        t2.start();

        System.out.println("Fim da main");
    }
}
```

Saída possível:

```text
Fim da main
Executando: Thread 1
Executando: Thread 2
```

ou:

```text
Executando: Thread 2
Fim da main
Executando: Thread 1
```

Isso é normal.

---

# 34. Código com contador inseguro

```java
public class Contador {
    private int valor = 0;

    public void incrementar() {
        valor++;
    }

    public int getValor() {
        return valor;
    }
}
```

Teste:

```java
public class Principal {
    public static void main(String[] args) throws InterruptedException {

        Contador contador = new Contador();

        Runnable tarefa = () -> {
            for (int i = 0; i < 1000; i++) {
                contador.incrementar();
            }
        };

        Thread t1 = new Thread(tarefa);
        Thread t2 = new Thread(tarefa);

        t1.start();
        t2.start();

        t1.join();
        t2.join();

        System.out.println(contador.getValor());
    }
}
```

Você espera:

```text
2000
```

Mas pode vir menos.

---

# 35. Código com contador seguro

```java
public class Contador {
    private int valor = 0;

    public synchronized void incrementar() {
        valor++;
    }

    public synchronized int getValor() {
        return valor;
    }
}
```

Agora o resultado tende a ser correto:

```text
2000
```

O PDF usa exatamente essa ideia: para corrigir condição de corrida no contador, o método `incrementar()` pode ser declarado como `synchronized`, garantindo que apenas uma thread por vez execute aquele método para a mesma instância. 

---

# O que você precisa dominar para a prova

Se você souber isso, está bem no Tema 5:

```text
1. O que é thread.
2. Diferença entre processo e thread.
3. O que é programação paralela.
4. O que é concorrência.
5. O que é escalonador.
6. O que é troca de contexto.
7. Diferença entre CPU núcleo único e multinúcleo.
8. Criar thread estendendo Thread.
9. Criar thread implementando Runnable.
10. Diferença entre start() e run().
11. Usar sleep().
12. Usar join().
13. Saber que stop() é depreciado.
14. Entender setDaemon().
15. Entender estados: New, Runnable, Blocked, Waiting, Timed Waiting, Terminated.
16. Entender condição de corrida.
17. Entender região crítica.
18. Usar synchronized em método.
19. Usar synchronized em bloco.
20. Entender monitor.
21. Entender wait(), notify() e notifyAll().
22. Entender deadlock.
23. Usar Semaphore com acquire() e release().
24. Entender CountDownLatch.
25. Saber que ordem de execução de threads não é garantida.
26. Saber que dados compartilhados precisam de cuidado.
```

---

# Desafios para você resolver

## Desafio 1 — Primeira thread

Crie uma classe:

```text
MinhaThread
```

Ela deve estender `Thread`.

No método `run()`, imprima:

```text
Thread rodando: <nome da thread>
```

No `main`, crie 3 threads e chame `start()` em todas.

Objetivo:

```text
Entender Thread, run() e start().
```

---

## Desafio 2 — Runnable

Crie uma classe:

```text
MinhaTarefa
```

Ela deve implementar `Runnable`.

No `run()`, imprima:

```text
Executando tarefa em: <nome da thread>
```

No `main`, crie:

```java
Thread t1 = new Thread(new MinhaTarefa(), "Tarefa 1");
Thread t2 = new Thread(new MinhaTarefa(), "Tarefa 2");
```

E chame `start()`.

Objetivo:

```text
Entender Runnable como tarefa.
```

---

## Desafio 3 — `run()` versus `start()`

Crie uma thread e teste:

```java
t.run();
t.start();
```

Imprima:

```java
Thread.currentThread().getName()
```

Responda em comentário:

```java
// Qual rodou na main?
// Qual criou uma nova thread?
```

---

## Desafio 4 — `sleep()`

Crie uma thread que imprima:

```text
Começou
```

Espere 2 segundos com:

```java
Thread.sleep(2000);
```

Depois imprima:

```text
Terminou
```

Obrigatório tratar `InterruptedException`.

---

## Desafio 5 — `join()`

Crie duas threads.

A primeira demora 2 segundos.

A segunda demora 1 segundo.

No `main`, use:

```java
t1.join();
t2.join();
```

Depois imprima:

```text
Todas as threads terminaram.
```

Teste sem `join()` e com `join()` para ver a diferença.

---

## Desafio 6 — Contador sem sincronização

Crie uma classe `Contador`:

```java
private int valor = 0;

public void incrementar() {
    valor++;
}
```

Crie duas threads, cada uma incrementando 100000 vezes.

No final, imprima o valor.

Resultado esperado:

```text
200000
```

Veja se sempre chega nisso.

Objetivo:

```text
Perceber condição de corrida.
```

---

## Desafio 7 — Contador com `synchronized`

Pegue o desafio anterior e altere:

```java
public synchronized void incrementar()
```

Teste de novo.

Objetivo:

```text
Resolver condição de corrida.
```

---

## Desafio 8 — Bloco sincronizado

Em vez de sincronizar o método inteiro, use:

```java
private final Object lock = new Object();

public void incrementar() {
    synchronized (lock) {
        valor++;
    }
}
```

Compare com o desafio anterior.

---

## Desafio 9 — Semáforo

Crie um estacionamento com 3 vagas.

Use:

```java
Semaphore vagas = new Semaphore(3);
```

Crie 10 carros como threads.

Cada carro deve:

```text
tentar entrar
acquire()
ficar estacionado por 2 segundos
release()
sair
```

Objetivo:

```text
Entender acquire() e release().
```

---

## Desafio 10 — `CountDownLatch`

Crie 3 threads chamadas:

```text
Aluno 1
Aluno 2
Aluno 3
```

Cada uma deve dormir por um tempo aleatório e depois imprimir:

```text
Aluno X terminou a prova.
```

A thread principal deve esperar todas terminarem com:

```java
latch.await();
```

Depois imprimir:

```text
Todos terminaram. Professor pode fechar a sala.
```

---

## Desafio 11 — Deadlock conceitual

Crie dois objetos:

```java
Object lock1 = new Object();
Object lock2 = new Object();
```

Thread A:

```java
synchronized (lock1) {
    synchronized (lock2) {
    }
}
```

Thread B:

```java
synchronized (lock2) {
    synchronized (lock1) {
    }
}
```

Responda em comentário:

```java
// Por que isso pode travar?
// Como evitar?
```

Não precisa forçar o deadlock se não quiser; entender o conceito já ajuda muito.

---

## Desafio 12 — Projeto chefão do Tema 5

Crie um mini sistema chamado:

```text
SistemaEmpacotadora
```

Classes:

```text
Empresa
Equipe
Empacotador
ContadorSinc
PoolProdutos
Principal
```

Regras:

`Principal` cria uma empresa com quantidade de produtos.
`Empresa` cria equipes.
`Equipe` cria empacotadores.
`Empacotador` implementa `Runnable`.
`ContadorSinc` tem métodos `synchronized`.
`PoolProdutos` controla quantos produtos ainda existem.
Use `Semaphore` para limitar o número de fitas disponíveis.
Use `CountDownLatch` para esperar empacotadores terminarem.

Obrigatório usar:

```text
Thread
Runnable
run()
start()
sleep()
join() ou CountDownLatch
synchronized
Semaphore
acquire()
release()
tratamento de InterruptedException
```
