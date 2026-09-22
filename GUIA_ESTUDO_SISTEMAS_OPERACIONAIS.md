# 🖥️ GUIA DEFINITIVO DE REVISÃO: SISTEMAS OPERACIONAIS (ITPSTOP)
**Professor:** Prof. Dr. Ramiro Tadeu Wisnieski | **Curso:** Tecnologia em Sistemas para Internet — IFSP Itapetininga  
**Material Consolidado:** Aulas 01, 02, 03, 04, 05, Listex 2, Listex 5, Atividade de Virtualização e Questões de Revisão.

---

## 🧭 MAPA MENTAL RÁPIDO PARA A PROVA
* **Função Básica do SO:** Máquina estendida (abstração de hardware) e gerenciador de recursos (CPU, memória, dispositivos de E/S, arquivos).
* **Tipos de SO:**
  * **Monoprogramável / Monotarefa:** 1 programa por vez na memória; CPU ociosa durante E/S (ex: MS-DOS).
  * **Multiprogramável / Multitarefa:** Múltiplos programas na memória compartilhando recursos.
    * **Batch (Lote):** Sem interação do usuário; execução de jobs em fila via disco/fita/scripts `.bat`/`.sh`.
    * **Tempo Compartilhado (Time-Sharing):** Divisão da CPU em fatias de tempo (*time-slice* / quantum); ambiente interativo com terminais; ilusão de sistema dedicado.
    * **Tempo Real (RTOS):** Foco em **previsibilidade temporal** e cumprimento estrito de prazos (*deadlines*).
      * **Hard Deadline:** Falha no prazo gera catástrofe total (ex: controle de voo, robôs cirúrgicos, airbags).
      * **Soft Deadline:** Atraso reduz utilidade, mas sem catástrofe (ex: streaming de vídeo).
  * **Múltiplos Processadores:**
    * **Fortemente Acoplados (SMP):** Processadores compartilham a **mesma memória física principal** gerenciada por um único SO.
    * **Fracamente Acoplados (Clusters / Distribuídos):** Sistemas autônomos com seu próprio SO e memória, conectados via rede de alta velocidade (ex: Cluster Beowulf).
* **Virtualização:**
  * **Hypervisor Tipo 1 (Bare-Metal):** Roda direto no hardware físico (ex: VMware ESXi, Hyper-V, KVM, Xen). Alta performance.
  * **Hypervisor Tipo 2 (Hosted):** Roda como aplicação sobre um SO hospedeiro (ex: VirtualBox, VMware Workstation).
  * **Containers (Docker):** Compartilha o kernel do SO hospedeiro; mais leve e rápido que VMs completas.
* **Processos (Programa em Execução):**
  * **Espaço de Endereçamento:** `Text` (código), `Data` (globais/estáticas), `Heap` (alocação dinâmica, cresce para cima), `Stack` (variáveis locais/funções, cresce para baixo).
  * **PCB (Bloco de Controle do Processo):** Estrutura no kernel com PID, Estado, PC (Program Counter), Registradores, Prioridade, Limites de Memória e Arquivos Abertos.
  * **Ciclo de Vida (5 Estados):** Novo $\\rightarrow$ Pronto $\\rightleftharpoons$ Executando $\\rightarrow$ Bloqueado $\\rightarrow$ Pronto, e Executando $\\rightarrow$ Terminado.
  * **Troca de Contexto (*Context Switch*):** Salva o PCB do processo atual e carrega o PCB do próximo. Puro *overhead*.
* **Threads (Processos Leves - LWP):**
  * **Compartilham:** Espaço de memória, código (`Text`), dados globais (`Data`), `Heap` e arquivos abertos.
  * **Possuem Exclusivo:** Thread ID (TID), Contador de Programa (PC), Registradores e **Pilha Própria (Stack)**.
  * **Vantagens:** Responsividade, compartilhamento direto de memória, criação/troca muito mais rápida que processos, suporte a paralelismo real em multicore.
  * **Problemas:** Condição de corrida (*Race Condition*), necessitando de **Mutex** ou **Semáforos** na seção crítica.

---

## 1. TIPOS DE SISTEMAS OPERACIONAIS (AULA 02 & LISTEX 2)

### 📌 Comparativo dos Tipos de SO:
| Tipo de SO | Interação do Usuário | Foco Principal | Exemplo / Aplicação |
| :--- | :---: | :--- | :--- |
| **Monoprogramável** | Baixa / Direta | Simplicidade; 1 tarefa por vez. | MS-DOS |
| **Batch (Lote)** | **Nenhuma** | Alto throughput de CPU em processamento de grandes volumes. | Processamento de folha de pagamento, rotinas de backup noturno, scripts `.bat` |
| **Tempo Compartilhado** | **Alta (Tempo Real)** | Resposta rápida para múltiplos usuários via *time-slice*. | Linux, Windows, macOS, Servidores Web |
| **Tempo Real (RTOS)** | Média / Sensores | **Previsibilidade temporal** e cumprimento de *deadlines*. | Hard: Usinas, Robôs cirúrgicos. Soft: Streaming, Jogos |

### 📌 Múltiplos Processadores: Fortemente vs Fracamente Acoplados:
* **Fortemente Acoplados (Tightly Coupled / SMP):**
  * CPUs compartilham uma **única memória física comum** (*Shared Memory*) e barramento.
  * Gerenciados por um **único Sistema Operacional**.
  * Comunicação ultrarrápida via memória.
* **Fracamente Acoplados (Loosely Coupled / Clusters / Sistemas Distribuídos):**
  * Dois ou mais nós computacionais independentes, cada um com **seu próprio SO e sua própria memória**.
  * Comunicação ocorre via **rede / linhas de comunicação**.
  * *Cluster Beowulf:* Rede de computadores comuns com Linux configurados para processamento paralelo científico de alto desempenho.

---

## 2. VIRTUALIZAÇÃO (AULA 03 & ATIVIDADE 3)

A virtualização desacopla o sistema operacional e as aplicações do hardware físico subjacente através do **Hypervisor** (ou VMM - Virtual Machine Monitor).

### 📌 Hypervisor Tipo 1 vs Tipo 2:
```
   HYPERVISOR TIPO 1 (Bare-Metal)           HYPERVISOR TIPO 2 (Hosted)
+---------------------------------+     +---------------------------------+
|  VM 1 (Linux)  |  VM 2 (Windows)|     |  VM 1 (Linux)  |  VM 2 (Windows)|
+---------------------------------+     +---------------------------------+
|   HYPERVISOR (ESXi, KVM, Xen)   |     |   HYPERVISOR (VirtualBox, etc.) |
+---------------------------------+     +---------------------------------+
|         HARDWARE FÍSICO         |     |      SO HOSPEDEIRO (Host OS)    |
+---------------------------------+     +---------------------------------+
                                        |         HARDWARE FÍSICO         |
                                        +---------------------------------+
```

| Característica | Hypervisor Tipo 1 (Nativo / Bare-Metal) | Hypervisor Tipo 2 (Hospedado / Hosted) |
| :--- | :--- | :--- |
| **Onde roda** | Diretamente sobre o hardware físico. | Como aplicação sobre um SO hospedeiro existente. |
| **Desempenho** | **Excelente (baixo overhead)**. | Médio (overhead do SO hospedeiro). |
| **Uso Típico** | Data Centers, Servidores de Nuvem (AWS, Azure). | Desktops, testes, ambiente de desenvolvimento. |
| **Exemplos** | VMware ESXi, Microsoft Hyper-V, KVM, Xen. | Oracle VirtualBox, VMware Workstation. |

### 📌 Containers vs Máquinas Virtuais (VMs):
* **Máquina Virtual:** Virtualiza o **hardware completo**. Cada VM carrega seu próprio kernel e SO convidado completo (pesado, consome GBs de RAM e minutos para iniciar).
* **Container (Docker):** Virtualiza o **Sistema Operacional**. Compartilha o **mesmo kernel do host**, isolando processos via *namespaces* e *cgroups* (leve, consome MBs e inicia em milissegundos).

---

## 3. PROCESSO: CONCEITO, ESTRUTURA E ESTADOS (AULA 04)

### 📌 Definição Formal:
Um **Processo** é um **programa em execução**. Enquanto o programa é uma entidade passiva armazenada em disco, o processo é uma entidade ativa com contexto de hardware e memória.

### 📌 Estrutura do Processo na Memória:
```
+------------------------------------+  Endereço Alto (0xFFFFFFFF)
|               STACK                |  (Cresce para BAIXO: variáveis locais, funções)
|                 |                  |
|                 v                  |
|                                    |
|                 ^                  |
|                 |                  |
|               HEAP                 |  (Cresce para CIMA: alocação dinâmica malloc/new)
+------------------------------------+
|               DATA                 |  (Variáveis globais e estáticas)
+------------------------------------+
|               TEXT                 |  (Código binário / Instruções do programa)
+------------------------------------+  Endereço Baixo (0x00000000)
```

### 📌 Bloco de Controle do Processo (PCB - Process Control Block):
Estrutura de dados essencial mantida pelo kernel para cada processo ativo:
1. **Identificador do Processo (PID):** Número único do processo.
2. **Estado do Processo:** Novo, Pronto, Executando, Bloqueado ou Terminado.
3. **Contador de Programa (PC - Program Counter):** Endereço da próxima instrução a executar.
4. **Registradores da CPU:** Acumuladores, ponteiros de pilha e registradores de dados (salvos na troca de contexto).
5. **Prioridade e Informações de Escalonamento.**
6. **Gerência de Memória:** Registradores base/limite, tabelas de páginas/segmentos.
7. **Contabilidade e Status de E/S:** Tempo de CPU consumido, lista de arquivos e dispositivos abertos.

### 📌 Diagrama dos 5 Estados de um Processo e Transições:
```
           +-----------+
           |   NOVO    |
           +-----+-----+
                 | (Admissão)
                 v
           +-----------+   Despacho (Dispatch)   +-------------+
    +----->|  PRONTO   | ----------------------> | EXECUTANDO  | -----> [ TERMINADO ]
    |      +-----------+ <---------------------- +------+------+  (exit)
    |            ^         Fim da Fatia de Tempo        |
    |            |           (Time-slice)               |
    |            |                                      | Espera de E/S
    |      +-----+-----+                                | ou Evento
    +------+ BLOQUEADO | <------------------------------+
    (E/S   +-----------+
    concluída)
```

### 📌 As 4 Transições Fundamentais de Estado:
1. **Pronto $\\rightarrow$ Executando (Despacho / *Dispatch*):** O escalonador seleciona o processo da fila de prontos e entrega a CPU a ele.
2. **Executando $\\rightarrow$ Pronto (Preempção / Interrupção por *Time-slice*):** O processo usou todo o seu tempo de CPU permitido (quantum) ou um processo de maior prioridade ficou pronto.
3. **Executando $\\rightarrow$ Bloqueado (Espera de Evento / E/S):** O processo solicita uma operação de E/S (leitura de disco/teclado) e não pode continuar até que ela termine.
4. **Bloqueado $\\rightarrow$ Pronto (Conclusão de Evento):** O dispositivo de E/S encerra a operação e gera uma interrupção, colocando o processo de volta na fila de prontos.

### 📌 Troca de Contexto (*Context Switch*):
* Ação de **salvar o estado/registradores do processo atual em seu PCB** e **carregar o estado de outro processo a partir do PCB dele** na CPU.
* **Overhead Puro:** Durante a troca de contexto, a CPU não executa nenhuma instrução útil do usuário.

---

## 4. THREADS (AULA 05 & LISTEX 5)

Uma **Thread** (ou Linha de Execução / Processo Leve - LWP) é a menor unidade de processamento que pode ser escalonada pelo SO.

### 📌 Comparativo: O que é Compartilhado vs O que é Exclusivo da Thread:
| Compartilhado entre Threads do Mesmo Processo | Exclusivo de CADA Thread (Contexto Próprio) |
| :--- | :--- |
| • Espaço de Endereçamento de Memória | • **Identificador de Thread (TID)** |
| • Seção de Código (`Text`) | • **Contador de Programa Próprio (PC)** |
| • Seção de Dados Globais (`Data`) | • **Conjunto de Registradores Próprio** |
| • Heap (Memória Dinâmica) | • **Pilha de Execução Própria (`Stack`)** |
| • Arquivos e Sockets Abertos | • Estado e Prioridade da Thread |

### 📌 Por que usar Threads em vez de Processos? (LISTEX 5):
1. **Responsividade:** Em interfaces gráficas ou servidores web, se uma thread bloquear aguardando disco ou rede, as outras continuam atendendo o usuário sem travar a aplicação.
2. **Compartilhamento de Recursos Simplificado:** Threads leem e escrevem nas mesmas variáveis globais diretamente, dispensando chamadas pesadas de IPC (memória compartilhada / pipes).
3. **Economia de Recursos e Velocidade:** Criar uma thread é até **100 vezes mais rápido** e consome fração mínima de memória em relação a criar um processo com `fork()`. A troca de contexto entre threads do mesmo processo não invalida a tabela de páginas da MMU.
4. **Escalabilidade em Multicore:** Threads podem executar em **paralelismo real** em núcleos distintos da CPU.

### 📌 Modelos de Threads:
* **Muitos para Um (N:1 - Espaço de Usuário / ULT):** Gerenciadas por biblioteca; kernel vê apenas 1 processo. Se uma thread bloquear em E/S, **o processo inteiro é bloqueado**! Não aproveita múltiplos núcleos.
* **Um para Um (1:1 - Espaço de Kernel / KLT):** Cada thread de usuário mapeia diretamente para uma thread do kernel (padrão do Linux `pthread` e Windows). Permite paralelismo real em multicore.
* **Muitos para Muitos (M:N - Modelo Híbrido):** Multiplexa $M$ threads de usuário em $N$ threads de kernel.

### 📌 Concorrência vs Paralelismo:
* **Concorrência:** Intercalar múltiplas tarefas no tempo através de chaveamento rápido (acontece mesmo em processadores com 1 único núcleo).
* **Paralelismo:** Execução **simultânea real** de instruções em múltiplos núcleos/CPUs ao mesmo instante físico.

### 📌 Problemas de Concorrência e Sincronização:
* **Condição de Corrida (*Race Condition*):** Ocorre quando duas ou mais threads tentam alterar uma variável compartilhada ao mesmo tempo, gerando resultados incorretos e imprevisíveis.
* **Seção Crítica (*Critical Section*):** Trecho de código que acessa a memória compartilhada.
* **Solução:** Mecanismos de exclusão mútua (**Mutex**) e **Semáforos** para garantir que apenas uma thread acesse a seção crítica por vez.
