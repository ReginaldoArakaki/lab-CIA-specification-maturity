# Guia Orientativo de Uso e Exploração do Laboratório de Simulador de Maturidades Arquiteturais (CIA)

O presente documento constitui o guia prático de orientação e exploração pedagógica para alunos, monitores e professores na utilização do **Laboratório de Simulação de Maturidades Arquiteturais**. O objetivo central deste laboratório é permitir a vivência experimental das Decisões de Arquitetura de Software (ADRs) direcionadas aos Requisitos Não Funcionais (RNFs) de **Confidencialidade, Integridade e Disponibilidade (Tríade CIA)**.

A proposta pedagógica do laboratório é fundamentada no ciclo de simulação: em vez de apenas discutir conceitos abstratos, a turma vivencia ativamente o impacto de manter um sistema em **baixas maturidades (N1 e N2)** — sentindo a dor da indisponibilidade, perda de vendas e vazamento de dados — para então projetar e validar a evolução gradual até **altas maturidades (N3 e N4)** por meio de táticas bem fundamentadas.

---

## 1. Fundamentos Arquiteturais de Referência

Para explorar o simulador com rigor técnico, é essencial compreender o modelo teórico que sustenta todas as métricas e componentes da ferramenta.

### O Pattern de Referência 3-Tier

Conforme estabelecido na arquitetura de referência do curso, a discussão de segurança e resiliência não deve ser dividida de forma rasa entre "front-end e back-end". A fronteira de responsabilidade do sistema organiza-se estritamente em **Três Camadas (3-Tier)**:

*   **Camada 1 — IHC (Interação Humano-Computador e Coisas Conectadas)**: Engloba a interface com o usuário, dispositivos IoT, portas de entrada de APIs e validação inicial. É a primeira linha de defesa para Confidencialidade e Disponibilidade.
*   **Camada 2 — LN (Lógica de Negócio)**: Onde residem as regras de negócio, os parâmetros da aplicação, a orquestração de workflows e os atuadores de negócio (como *throttling* e *degradação graciosa* em tempo de execução).
*   **Camada 3 — Acesso & Integração**: Responsável pela persistência e consistência transacional dos dados, integrações internas e externas, réplicas, backups e integridade de longo prazo.

### A Escala de Maturidade de ADRs (N1 a N4)

A maturidade de uma arquitetura é medida pela resposta à seguinte pergunta-guia: **"Quem controla a operação no seu sistema hoje?"**

*   **N1 (Programação Sem Controle / "Só RF")**: Código focado exclusivamente em Requisitos Funcionais. APIs síncronas ponto a ponto, credenciais fixas e ausência de logs ou táticas de resiliência. *Quem controla: Ninguém.*
*   **N2 (Processos Manuais)**: Sistema rodando em infraestrutura gerenciada na nuvem. A monitoração rastreia métricas genéricas da infraestrutura (CPU, memória), mas os incidentes são descobertos quando **o cliente reclama nas redes sociais** ou abre chamados no suporte. *Quem controla: O suporte, após a reclamação.*
*   **N3 (Malha Aberta Cibernética)**: Introdução de mensageria assíncrona, filas, *circuit breakers*, limites de taxa (*rate limiting*) e observabilidade com alerta. A ferramenta de monitoramento detecta a falha e avisa o operador humano, que executa *runbooks* ou rotinas manuais de recuperação. *Quem controla: O operador alertado pela ferramenta.*
*   **N4 (Malha Fechada Cibernética — Meta a Buscar)**: Arquitetura autorregulada. Sensores de negócio (como quedas na taxa de conversão) alimentam controladores cibernéticos que acionam atuadores automáticos (promoção de réplica ativa, isolamento de nós, degradação graciosa e predizer picos). O sistema reage ou antecipa o incidente antes que o usuário seja afetado. *Quem controla: A própria arquitetura.*

### As 5 Janelas Cibernéticas e as Táticas de Segurança

A proteção do sistema é distribuída em cinco janelas operacionais: **Monitorar**, **Resistir**, **Detectar**, **Recuperar** e **Predizer**. Elas se articulam com as três táticas fundamentais de segurança:

1.  **Resistir**: Impede ou dificulta o ataque (MFA, TLS, criptografia simétrica AES-256 no KMS, criptografia assimétrica RSA, hashes e validação de entrada).
2.  **Detectar**: Identifica o incidente em andamento (análise de padrões anômalos de acesso, divergência de checksums, violação de SLOs e logs auditáveis).
3.  **Recuperar**: Restaura o estado operacional e a integridade (réplicas ativas/passivas, backups imutáveis com restauração *point-in-time*, rotação de segredos e isolamento de nós).

---

## 2. Estrutura e Navegação pelas Bancadas do Laboratório

A aplicação web interativa é organizada em uma barra superior com identificação do grupo (G1 a G5) e oito bancadas de trabalho principais:

| Aba / Bancada | Função Pedagógica | Ação Principal dos Alunos |
| :--- | :--- | :--- |
| **Aula** | Cronograma da sessão (90 a 110 min) e roteiro das dinâmicas | Leitura da pergunta-guia e alocação do foco do grupo |
| **1 · Caso** | Contextualização do problema real e métricas de negócio | Análise do desafio específico e parâmetros de carga/SLA |
| **2 · Arquitetura** | Matriz 3-Tier x Maturidade para seleção de táticas | Marcação de táticas reais e apuração do Nível Efetivo |
| **3 · Privacidade** | Régua de exposição e cifra de dados (P0 a P4) | Teste de transitividade dos dados legíveis vs. cifrados/tokenizados |
| **4 · Simulador** | Engine de simulação de carga e injeção de incidentes | Execução dos testes de estresse, visualização do gráfico e medição de KPIs |
| **5 · Ficha** | Gerador do Cenário de Qualidade no modelo de 6 partes | Documentação formal das táticas acionadas e formulação dos ADRs |
| **6 · Supervisor** | Simulador de *role-play* corporativo | Treinamento de abordagem de comunicação e publicação no mural |
| **Painel** | Dashboard consolidador da turma em tempo real | Comparação de custos, disponibilidade e apresentações de *pitch* |

---

## 3. Guia Experimental 1: A Experiência em Baixas Maturidades ("Sofrer em N2")

A primeira etapa prática do laboratório exige que os estudantes vivenciem o colapso de uma aplicação configurada sem táticas de resiliência e segurança.

### Passo a Passo para Configurar o Estágio N2

1.  Acesse a aba **2 · Arquitetura**.
2.  Desmarque todas as táticas de nível N3 e N4 nas três camadas (IHC, Lógica de Negócio e Acesso). Marque apenas as táticas básicas de N2:
    *   *IHC*: Monitoração básica de infraestrutura do provedor (CPU, rede) e canal de suporte.
    *   *Lógica de Negócio*: Logs de aplicação sem contexto de negócio e parâmetros em arquivo editados manualmente.
    *   *Acesso*: Backup diário com restauração manual e réplica básica do provedor.
3.  O painel indicará **Nível Efetivo: N2 (Processos manuais)**.
4.  Acesse a aba **3 · Privacidade** e selecione o nível **P0 (Dados em aberto)**, onde HTTP roda sem TLS, o banco armazena senhas e dados em texto claro e os logs expõem CPFs e números de cartão.
5.  Acesse a aba **4 · Simulador** e mantenha acionados os incidentes padrões (ex: *Queda do banco primário* e *Lentidão na lógica de negócio*).

### O Que Observar Durante a Simulação N2

Ao executar a simulação em N2, os alunos devem registrar os seguintes fenômenos nos gráficos e indicadores:

*   **Efeito Avalanche e Colapso de Capacidade**: Sem *circuit breaker* ou filas na IHC, o excesso de conexões prende as *threads* da aplicação. A capacidade do sistema cai pela metade durante o pico devido a *deadlocks* e contenção de banco.
*   **Estouro Severo do Orçamento de Indisponibilidade**: O tempo de detecção (MTTD) e o tempo de recuperação (MTTR) sobem drasticamente. Um incidente no banco primário leva 40 minutos para ser percebido (quando o cliente aciona o suporte) e 120 minutos para restauração manual, ultrapassando os 43 minutos permitidos pelo SLA mensal de 99,9%.
*   **Prejuízo Financeiro e Perda de Vendas**: O gráfico exibe uma grande área vermelha indicando milhares de pedidos perdidos por falhas de timeout e recusas da aplicação.
*   **Vazamento de Registros Pessoais**: Em P0, qualquer incidente de malware ou interceptação resulta na exfiltração integral da base de cadastros em texto claro.

---

## 4. Guia Experimental 2: Aprimoramentos Arquiteturais Passo a Passo (N2 → N3 → N4)

Após constatar o fracasso operacional do sistema em N2, os grupos devem projetar melhorias arquiteturais justificadas por ADRs.

### Matriz de Transição e Escolha de Táticas

Para elevar o Nível Efetivo de uma camada, **todas** as táticas do nível correspondente (e dos níveis anteriores) devem ser implementadas:

```text
[ N2: Processos Manuais ] ──(ADR-001)──> [ N3: Malha Aberta ] ──(ADR-002)──> [ N4: Malha Fechada ]
   - Logs fracos                             - Fila virtual / Rate Limit        - Telemetria de negócio
   - Suporte reativo                         - Workers assíncronos              - Degradação graciosa autônoma
   - Backup manual                           - Outbox / Circuit Breaker         - Réplica ativa / Failover autônomo
```

#### 1. Elevação da Camada IHC (Interação)
*   **Para atingir N3**: Marcar *Fila Virtual (Sala de Espera)*, *Rate Limit no API Gateway*, *Monitoração de experiência com alertas* e *Timeout/Retry no cliente*.
*   **Para atingir N4**: Adicionar *Degradação Graciosa Automática* (desliga serviços secundários para preservar o checkout), *Detecção Comportamental de Bots com MFA Step-Up* e *Telemetria integrada ao controlador CIA*.

#### 2. Elevação da Camada Lógica de Negócio (LN)
*   **Para atingir N3**: Implementar *Workers Assíncronos*, *Idempotência em pedidos e pagamentos*, *Circuit Breaker nas dependências externas* e *Autoscaling*.
*   **Para atingir N4**: Adicionar *Business Drivers como SLO (vendas por minuto)*, *Feature Flags/Throttling em runtime* e *Predição de Carga com provisionamento antecipado*.

#### 3. Elevação da Camada Acesso & Integração
*   **Para atingir N3**: Configurar *Redundância Passiva (Standby com checkpoint e ressincronismo)*, *Padrão Outbox para mensageria* e *Retry com backoff*.
*   **Para atingir N4**: Adicionar *Redundância Ativa (Ativo-Ativo com votação)*, *Verificação contínua de integridade (checksums)* e *Backup imutável com restauração point-in-time*.

### A Régua de Privacidade (P0 a P4)

Os alunos devem evoluir a proteção dos dados sensíveis selecionando os rungs da régua na aba 3:

*   **P1 (TLS em trânsito)**: Protege a comunicação cliente-servidor contra escuta em rede pública (Wi-Fi), mas mantém banco e logs legíveis.
*   **P2 (Cifra Simétrica em Repouso — AES-256)**: Aplica AES-256 no banco e nos backups com chaves gerenciadas em KMS. Se um malware exfiltrar a base, os dados permanecem ilegíveis.
*   **P3 (Assimétrica e Assinatura — RSA/mTLS)**: Certificados mútuos entre microserviços, tokens JWT assinados digitalmente e envelopes criptográficos.
*   **P4 (Tokenização e Privacidade por Design - LGPD)**: Dados de cartão capturados diretamente via *iframe* do gateway de pagamento (gerando tokens); CPFs pseudonimizados e logs mascarados com expurgo programado.

### A Regra de Ouro do ADR

Toda mudança na matriz ou na régua de privacidade exige a redação de uma Decisão de Arquitetura em uma linha:
> *"Decidimos implementar [Tática/Componente] na camada [IHC/LN/Acesso] para resolver [Problema/Estímulo]; o custo operacional/financeiro assumido é [Custo/Complexidade]."*

---

## 5. Experimentos com os Estudos de Caso Práticos

O laboratório disponibiliza dois cenários reais de alta complexidade para testes comparativos:

### Caso 1: Venda de Ingressos — "Fam Show Ticket" (Pico de Rajada)

*   **Características do Desafio**: Oferta física de 450 assentos disputada por 5.000 compradores (com picos de até 400.000 acessos simulados) no momento da abertura das vendas.
*   **Métricas e Regras Críticas**: RN001 (Estoque máximo de 450), RN002 (Venda única / sem overbooking), RN003 (Limite de 2 ingressos por CPF) e RN004 (Reserva temporária de 10 minutos).
*   **Experimento Guiado**:
    1.  No simulador, selecione a forma de pico em **Rajada**.
    2.  Sem a tática de *Fila Virtual (ADR-001)* em N2, observe o colapso por contenção de banco e a ocorrência de assentos vendidos em duplicidade.
    3.  Eleve a IHC para N3 ativando a Fila Virtual. Observe como a taxa de atendimento se estabiliza, o *overselling* cai para zero e o tempo de resposta do checkout é preservado.
    4.  Injete o ataque de *Bots Cambistas e Credential Stuffing*. Em N3 (Rate Limit), parte dos bots ainda consome estoque. Em N4 (Detecção Comportamental + MFA Step-up), o sistema bloqueia os bots preservando os compradores legítimos.

### Caso 2: E-Commerce — Pico Sustentado na Black Friday (Platô)

*   **Características do Desafio**: Platô de demanda 8× acima do normal sustentado por 3 horas seguidas (0h às 3h).
*   **Métricas e Regras Críticas**: Meta de SLA de 99,9% (máximo de 43 minutos de indisponibilidade mensal), RTO de 1 hora para falhas críticas de checkout.
*   **Experimento Guiado**:
    1.  Configure a forma de pico em **Platô**.
    2.  Simule a falha da *Integração de Pagamento Externa*. Em N2, as chamadas síncronas travam o checkout e geram cobranças duplicadas.
    3.  Eleve a Lógica de Negócio para N3 ativando o padrão *Outbox* com *retry e backoff* e *Idempotência*. Os pagamentos ficam retidos com segurança na fila e são reprocessados sem duplicação quando o gateway restabelece o serviço.

### Experimento Adicional: Simulação de Ataques em MQTT

Para atividades focadas em redes de sensores e IoT, os alunos utilizam a simulação do protocolo MQTT para demonstrar violações da Tríade CIA:

| Atributo CIA | Mecanismo do Ataque | Impacto Observado |
| :--- | :--- | :--- |
| **Disponibilidade** | Duplicação de Client ID (Hijack) | Desconexão contínua em loop (*flapping*) |
| **Confidencialidade** | Eavesdropping em Tópicos Abertos | Captura de dados sensíveis em texto claro |
| **Integridade** | Injeção / Adulteração de Payload | Comandos e telemetrias falsas no Broker |

*   **Ataque de Client ID (Disponibilidade)**: O aluno conecta um segundo cliente MQTT usando exatamente o mesmo `Client ID` de um sensor em execução. O *broker* desconecta forçadamente a primeira conexão. Quando o cliente legítimo tenta reconectar, gera-se um loop contínuo de desconexões (*flapping*), derrubando a disponibilidade do serviço.
*   **Eavesdropping (Confidencialidade)**: Subscrição com *wildcards* em brokers públicos sem autenticação/TLS, capturando *payloads* em texto claro.
*   **Injeção de Mensagens (Integridade)**: Publicação de comandos não autorizados em tópicos de atuadores sem verificação de assinatura de payload.

---

## 6. Especificação Formal de Cenários de Qualidade (6 Partes)

Na aba **5 · Ficha**, os alunos transformam as descobertas da simulação em especificações formais de requisitos não funcionais, utilizando o modelo padronizado de 6 partes:

1.  **Fonte do Estímulo**: Quem ou o que gera o evento (ex: *Atacante externo com botnet*, *Falha de hardware no nó primário do banco*).
2.  **Estímulo**: O evento em si (ex: *Pico de 500.000 requisições/min*, *Nó deixa de responder ao heartbeat*).
3.  **Artefato**: A camada e o componente afetado (ex: *Camada de Acesso — Base transacional de pedidos*).
4.  **Ambiente**: A condição operacional no momento da falha (ex: *Produção, durante a abertura da pré-venda*).
5.  **Resposta**: As táticas arquiteturais acionadas em sequência (ex: *Detectar por heartbeat → Failover automático para réplica ativa → Isolamento do nó falho → Alerta à operação*).
6.  **Medida de Resposta**: A métrica de verificação objetiva (ex: *MTTD ≤ 5s, RTO ≤ 30s, RPO = 0, Indisponibilidade na janela ≤ 2 min*).

O sistema disponibiliza o botão **"Preencher com a simulação"**, que lê os dados em tempo real da engine e popula os campos para revisão do estudante, permitindo a exportação final do relatório em formato Markdown (`.md`).

---

## 7. Governança, Role-Play e Avaliação da Turma

O aprendizado de arquitetura completa-se quando o estudante aprende a **vender e defender a Decisão de Arquitetura** para a gestão.

### A Dinâmica do Supervisor (Aba 6)

Na aba **6 · Supervisor**, os alunos realizam um *role-play* em duplas cruzadas treinando a comunicação técnica com supervisores ocupados:

#### Regras de Ouro da Comunicação Arquitetural
1.  **Pergunte, não diagnostique**: Evite dizer "nossos sistemas são amadores em N1". Faça perguntas reflexivas (ex: *"Se o banco primário cair agora, quanto tempo levamos para voltar?"*).
2.  **Traga dados concretos**: Use as métricas coletadas no simulador (pedidos perdidos, minutos fora, cadastros expostos).
3.  **Peça um compromisso pequeno**: Não solicite orçamento para reescrever o sistema inteiro. Peça 15 minutos de reunião, um estudo de um incidente passado ou a criação de uma página de documentação.
4.  **Ofereça trabalho, não crítica**: Mostre-se disposto a desenhar a proposta do ADR.

#### Matriz de Objeções e Respostas

| Objeção do Supervisor | Resposta Estratégica do Aluno |
| :--- | :--- |
| *“Não temos tempo para isso agora.”* | *“Entendo a correria. Consigo trazer em 15 minutos um exemplo do nosso próprio sistema mostrando como evitar indisponibilidade no próximo pico. Quando seria melhor?”* |
| *“Isso é assunto da equipe de infraestrutura.”* | *“Parte da infraestrutura resolve, mas a outra parte depende da aplicação — como idempotência e filas. Queria entender onde posso ajudar do lado do código.”* |
| *“Sempre funcionou assim sem problemas.”* | *“Funcionou muito bem até nosso volume atual. Fiquei curioso em como o checkout se comportaria sob um pico de 10×. Temos dados do último evento?”* |
| *“Foque apenas na sua tarefa atual.”* | *“Com certeza. Posso apenas registrar em uma página as oportunidades de resiliência que observei e você decide se vale aprofundar no futuro.”* |

### Consolidação no Painel da Turma (Aba Painel)

A sessão encerra-se com a projeção do **Painel da Turma**, que consolida em tempo real os resultados de todos os grupos:

*   **Comparativo de Desempenho**: Apresenta a disponibilidade acumulada de cada grupo, o custo relativo das táticas adotadas, os minutos fora do ar e o cumprimento do orçamento de indisponibilidade.
*   **A Provocação Final do SLA**: O painel destaca quantos minutos de indisponibilidade foram causados por ataques de segurança ou falhas em provedores terceiros.
*   **O Questionamento do Prof. Reginaldo Arakaki**: A especificação padrão de e-commerce isenta o fornecedor do SLA em casos de "ataques DDoS de grande escala ou falha de terceiros". *A provocação para o encerramento é: O seu SLA foi escrito para proteger a experiência do cliente final ou para isentar de responsabilidade o fornecedor de TI? Em uma maturidade N4, a arquitetura assume a resiliência em vez de se esconder nas cláusulas de isenção.*
