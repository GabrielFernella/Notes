# Sistemas Distribuídos

Objetivos SD

- Fazer link entre usuários e recursos

- transparência 

- flexibilidade

- escalabilidade


Middlewares

- Interface entre o SO e o Software

Características dos Algoritmos descentralizados

- Nenhuma máquina tem informações completa do estado do sistema
- as máquinas tomam decisões com as informações locais
- a falha no algoritmo não 'quebra' todo o sistema
- não existe uma suposição de um relógio digital 

Técnica de Escalabilidade

- aumentar a disponibilidade dos recursos

- Fazer balanceamento de carga

- Oculta a latência de comunicação casa haja grande dispersão geográfica

  

---

## Tipos de Sistemas Distribuidos

### Tipos de SD

- Computação em Cluster
- Computação em Grade
- Computação em Nuvem
- Sistemas de informações Distribuídos
- Sistemas Distribuídos Pervasivos

### Em Cluster

- Caracteristica Homogenea
- conjunto de máquinas semelhantes
- Conexão entre uma rede local(LAN)
- Software
    - Mesmo SO nas Máquinas
    - Geralmente único programa executado
- Normalmente usado para computação paralela
- Forte acoplamento entre Nós
    Nó mestre - alocar tarefas em nó, organizar a fila de tarefas e interface com usuários
    Provê transparência de localização e migração

### Em Grade

- Característica Heterogenea 
- Exemplo: planetLab
- Hardware de diferentes organizações são reunidos e dispersos entre eles
- Uma rede de serviço mundial para criar novos serviços de rede
- a RNP faz parte dela

### Em Nuvem

- Uso de recursos de computação
  - VM
  - cloud storage
  - aplicação
- Serviços terceirizados 

### Sistema de Informação distribuidos

- Sistemas corporativos para integrar diversas aplicações onde a interoperabilidade se mostrou 'penosa':
  - Sistema de processamento de transações
  - Integração de aplicações empresariais

### Sistema de Informação: Integração de aplicações corporativas

- Necessidade das aplicações se comunicarem
- não apenas transações com requisição/resposta em formato de transação com os BDs
- Middleware
  - Chamada remota de procedimento (RPC)
  - Invocação de método remoto(RMI)
  - Middleware orientando a mensagem  (MOM)
- Vários métodos 
- promove a reusabilidade

### Sistemas Perbasivos

- Instabilidade é o comportamento esperado desses sistemas
- Dispositivos de computação móveis e embarcados pequenos
  - alimentação por bateria
  - mobilidade
  - conexão sem fio

---

Estilos arquitetônicos

Um uso arquitetônico é determiando através dos: 

- componentes: Unidade modular com interfaces requeridas e fornecidas bem definidas que é substituível dentro de seu ambiente
- Conexões: o modo como os componentes estão ligados 
- Dados Intercambiados: forma da troca dos dados entre componentes(repositório compartilhado)
- Formas de Configuração: maneiras como os componentes configurados (em tempo de execução?)

Arquitetura de Sistemas

- Arquitetura centralizadas - Cliente servidor
  Cliente servidor: Netflix, site de noticias, online bank 
  Arquiteturas de duas divisões: fat clients/thin clients - divisão de processos do sistema
  Arquitetura de três divisões: Cliente, servidor, Servidor que pode agir como cliente (banco de dados)
- Arquiteturas descentralizadas
  Sistemas Peer-to-Peer, como o chord
  - Vários servidores heterogêneos que juntos processam um conjunto de dados
  - balanceamento de carga horizontal / Balanceamento Horizontal, onde cada servidor tem suas devidas responsabilidades
- Arquiteturas Hibridas
  Sistemas Peer-to-peer, bitTorrent, whatsApp, skype

---

