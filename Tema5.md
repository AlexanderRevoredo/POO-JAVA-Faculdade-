*# Resumo completo: Programação paralela em Java com threads

### 1. Threads e processamento paralelo

Uma **thread** é uma unidade independente de execução dentro de um processo. Em sistemas com múltiplos núcleos, diferentes threads podem rodar em paralelo em núcleos distintos, aumentando o desempenho. Sem threads, o programa executa sequencialmente em um único núcleo.

O sistema operacional usa um **escalonador de processos** que gerencia o tempo de CPU de cada processo/thread. Em CPUs de núcleo único, a simultaneidade é uma ilusão criada pela preempção (troca rápida de contexto). Em CPUs multinúcleo, o paralelismo é real.

Diferença importante entre processos e threads:
- **Processos**: têm espaço de memória próprio, isolados entre si.
- **Threads**: menores unidades de execução, compartilham o mesmo espaço de memória do processo.

Há dois tipos de threads em Java: **daemon** (rodam em segundo plano, são encerradas quando todas as user threads terminam — ex.: Garbage Collector) e **user threads** (criadas pela aplicação, a JVM aguarda seu término).

---

### 2. Ciclo de vida de uma thread

Como mostrado no diagrama acima, uma thread passa por 6 estados:

- **NEW**: criada, ainda não agendada.
- **RUNNABLE**: pronta para execução ou em execução na CPU.
- **BLOCKED**: suspensa aguardando um lock (trava).
- **TIMED_WAITING**: suspensa por tempo determinado via `sleep()`, `wait(timeout)` ou `join(timeout)`.
- **WAITING**: suspensa indefinidamente via `wait()`, `join()` ou `park()`.
- **TERMINATED**: concluiu a execução.

Existem duas formas de criar threads em Java: estendendo a classe `Thread` ou implementando a interface `Runnable` (geralmente preferida por respeitar os princípios de orientação a objetos e evitar problemas com herança múltipla).

---

### 3. Sincronização entre threads

Como threads compartilham memória, acessos concorrentes a um mesmo recurso podem causar a **condição de corrida** (race condition): duas threads leem e escrevem a mesma variável ao mesmo tempo, gerando resultados incorretos.

Para resolver isso, Java oferece dois mecanismos principais:

**Semáforos (`Semaphore`)**: controlam quantas threads podem acessar um recurso simultaneamente. Funcionam com `acquire()` (solicita acesso) e `release()` (libera acesso). Também são usados para sinalização entre threads.

**Monitores (`synchronized`)**: garantem que apenas uma thread por vez execute um bloco crítico. Usam `synchronized` em métodos ou blocos. O monitor em Java inclui o `wait-set` com os métodos `wait()`, `notify()` e `notifyAll()` para cooperação entre threads.

**Objetos imutáveis** também são uma estratégia: se o estado de um objeto não pode ser alterado após a criação, não há risco de condição de corrida. Em Java, objetos imutáveis devem ter todos os campos `private` e `final`, sem métodos `set`.

---

### 4. Implementação de threads na prática

A classe `Thread` oferece métodos importantes como `start()`, `run()`, `sleep()`, `join()`, `getPriority()`, `setPriority()`, `getState()`, `setDaemon()`, `yield()`, entre outros.

Problemas comuns a evitar:
- **Condição de corrida**: acesso concorrente não sincronizado a recursos compartilhados.
- **Deadlock**: duas ou mais threads bloqueadas esperando uma pela outra indefinidamente.
- **Starvation**: threads de baixa prioridade que nunca recebem tempo de CPU.

Boas práticas: usar IDEs com suporte a depuração de threads (como NetBeans), elaborar diagramas de sequência UML, ter cuidado com tipos não primitivos passados por referência (que podem vazar mutabilidade mesmo em classes declaradas `final`).

---

### Desafios do conteúdo

O material apresenta 4 atividades por módulo. Aqui estão os desafios principais para você praticar:

**Desafio 1 — Conceitual:** Qual a principal vantagem do processamento paralelo com threads em Java?
*(Resposta: aproveitar múltiplos núcleos do processador para executar tarefas em paralelo, reduzindo o tempo total de execução.)*

**Desafio 2 — Ciclo de vida:** Uma thread está aguardando que outra finalize antes de continuar. Em qual estado ela se encontra, e qual método foi chamado?

**Desafio 3 — Código:** Implemente uma classe `ContadorSeguro` com um método `incrementar()` que funcione corretamente com múltiplas threads simultâneas. Use `synchronized` ou `Semaphore`.

**Desafio 4 — Análise:** No exemplo PingPong do material, por que a ordem de impressão das linhas "Thread A iniciada" e "Thread B iniciada" pode variar entre execuções? O que garante que PING e PONG sempre se alternem corretamente?

**Desafio 5 — Projeto:** Baseando-se no exemplo da empresa ACME, projete um sistema com 3 threads que compartilham um contador de produtos. Use monitores para garantir que o total final seja sempre correto, independentemente da ordem de execução.
