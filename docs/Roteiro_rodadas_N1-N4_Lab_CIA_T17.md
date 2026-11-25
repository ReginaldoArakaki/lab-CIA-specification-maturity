# Roteiro das rodadas N1→N4 · Laboratório CIA T17

24/11/2026 · Reginaldo Arakaki

Cada grupo PBL sobe a mesma arquitetura de N1 até N3 em rodadas curtas, coletando evidências a cada degrau. Na rodada final, o grupo leva o sistema a N4 e defende as decisões com ADRs que deixam explícito o que cada mecanismo custa.

## Objetivo e regras do jogo

**Pergunta da dinâmica:** que decisões tornam o nosso sistema mais resiliente em C, I e A, e o que pagamos por cada uma?

**Entregáveis do grupo**

1.  Planilha de evidências preenchida nas 4 rodadas (seção Planilha de coleta).

2.  Um print por rodada da aba **4 · Simulador**: indicadores + gráfico + linha do tempo, na mesma tela.

3.  De 3 a 5 ADRs na rodada N4, cada um com o trade-off declarado.

4.  A Ficha (aba **5 · Ficha**) com um cenário para C, I e A, nível atual → alvo.

**Regras**

- **Fixe o experimento antes da Rodada 1.** Parâmetros de demanda e incidentes ficam iguais nas 4 rodadas. Só mudam as táticas (aba 2) e o nível de privacidade (aba 3). Assim a diferença entre rodadas é efeito da arquitetura, não do cenário.

- **Uma variável por vez dentro da rodada.** Se quiserem testar uma tática isolada, marquem, anotem o efeito e desmarquem.

- **Sem print, sem rodada.** O número que entra na planilha precisa aparecer no print.

- **O nível que vale é o efetivo**, calculado pelo site. Declarar N4 sem as táticas não conta.

- **Papéis rotativos por rodada:** operador do site, anotador da planilha, cronometrista e advogado do diabo (pergunta "e quanto custa?").

## As 4 rodadas em uma vista

São 45 minutos no total: três rodadas curtas de observação e uma rodada longa de decisão.

| Rodada  | Tempo  | Arquitetura (aba 2)                                 | Privacidade (aba 3)             | Pergunta central                                                     |
|---------|--------|-----------------------------------------------------|---------------------------------|----------------------------------------------------------------------|
| R1 · N1 | 7 min  | Nenhuma tática marcada                              | P0 · dados em aberto            | O que acontece quando ninguém controla?                              |
| R2 · N2 | 8 min  | Todas as táticas de N2 nas 3 camadas                | P1 · TLS em trânsito            | "Infra forte" e suporte resolvem?                                    |
| R3 · N3 | 10 min | N2 + todas as de N3                                 | P2 · cifra simétrica em repouso | Quanto melhora com alerta e operador? O que ainda depende de humano? |
| R4 · N4 | 20 min | O grupo escolhe o que levar a N4, camada por camada | P3 ou P4, justificado           | Quais decisões fecham a malha, e a que custo?                        |

Nas rodadas 1 a 3 o grupo **observa e coleta**. Na rodada 4 o grupo **decide**. Subir tudo para N4 não é obrigatório: um ADR que mantém uma camada em N3 com bom argumento vale tanto quanto um que a leva a N4.

## Rodada 1 · N1, programação sem controle (7 min)

A linha de base mostra o sistema "só RF": funciona até o primeiro pico e é o cliente quem descobre a falha.

**No site**

1.  Aba **1 · Caso**: leiam o desafio e a meta de SLA do grupo.

2.  Aba **4 · Simulador**: cliquem em "Restaurar valores do caso" e liguem os incidentes do grupo (seção Rota de cada grupo). Não mexam mais nesses controles até o fim.

3.  Aba **2 · Arquitetura**: nenhuma tática marcada. Aba **3 · Privacidade**: P0.

4.  Tirem o print do simulador.

**O que observar**

- Em que minuto aparece a mensagem de "efeito avalanche" e o que ela significa.

- Quem detectou cada incidente na linha do tempo, e quanto tempo levou.

- Quantos minutos o sistema ficou fora em comparação com o orçamento do mês inteiro.

- Na aba 3: em quantos trechos do fluxo o CPF e o cartão aparecem legíveis.

**Pergunta para a planilha:** em N1, qual dos três atributos (C, I ou A) falhou primeiro, e quem pagou a conta?

## Rodada 2 · N2, processos manuais (8 min)

A rodada mostra que "infra forte" do provedor e um suporte atento reduzem o tempo de detecção, mas não fazem o sistema escalar.

**No site**

1.  Aba 2: marquem as 6 táticas de N2 (2 em cada camada). Confiram que as três camadas mostram N2.

2.  Aba 3: P1 (TLS em trânsito).

3.  Print do simulador.

**O que observar**

- Quanto o MTTD caiu em relação a N1, e por quê: agora quem avisa é o suporte, não as redes sociais.

- Se os minutos fora caíram na mesma proporção. Na Black Friday, quase não caem: a capacidade fixa é o gargalo.

- Em quais trechos da aba 3 o dado continua legível mesmo com TLS (banco, logs e backups).

- Se houver incidente de pagamento (G2): se as duplicidades subiram ou desceram, e por quê.

**Pergunta para a planilha:** o que N2 melhorou no processo de suporte que não melhorou na arquitetura?

## Rodada 3 · N3, malha aberta cibernética (10 min)

N3 traz o maior salto da dinâmica: fila, assíncrono e alertas fazem o sistema escalar. Mesmo assim, cada incidente ainda espera um operador agir.

**No site**

1.  Aba 2: além das de N2, marquem as 12 táticas de N3 (4 em cada camada). As três camadas devem mostrar N3.

2.  Aba 3: P2 (cifra simétrica em repouso, Autoestudo 3).

3.  Print do simulador.

4.  **Experimento de isolamento** (opcional, 3 min): desmarquem uma única tática de N3 (por exemplo, a fila virtual ou a idempotência), anotem o efeito e marquem de novo.

**O que observar**

- Na linha do tempo, a frase "um alerta da ferramenta → operador": o tempo entre detectar e recuperar é o tempo humano.

- No gráfico, a linha azul (capacidade) chega **depois** da demanda: o autoscaling reage com atraso.

- Pedidos perdidos: a fila evita erro, mas parte das pessoas abandona a espera.

- G5 (ingressos): o sistema agora fica de pé e vende muito mais, inclusive para bots. Disponibilidade sem segurança amplia o cambismo.

- G4 e G5: o ransomware continua parando o sistema, mas a cópia do banco não expõe mais ninguém, porque os dados estão cifrados.

**Pergunta para a planilha:** listem os pontos em que um humano ainda precisa agir. Cada um deles é candidato a ADR na Rodada 4.

## Rodada 4 · N4, decidir e fechar a malha (20 min)

Na rodada final o grupo não marca tudo. Ele escolhe, camada por camada, os mecanismos que fecham a malha onde ela mais dói, e registra cada escolha em um ADR com o trade-off.

**Passo a passo**

1.  **Diagnóstico (4 min).** Com a planilha das rodadas 1 a 3, escolham as 2 ou 3 métricas que mais violam a meta do grupo: minutos fora, inconsistências, registros expostos, bots.

2.  **Hipóteses (4 min).** Para cada métrica, qual tática de N4 a resolve? Usem o Catálogo de mecanismos.

3.  **Experimento (6 min).** Marquem uma tática de N4 por vez e anotem o efeito. Uma camada só chega a N4 quando todas as suas táticas de N4 estão marcadas: o site mostra o que falta.

4.  **Privacidade (2 min).** Decidam entre P3 e P4 e anotem o argumento.

5.  **ADRs (4 min).** Escrevam de 3 a 5 ADRs no modelo da seção seguinte. Tirem o print final.

**Regras da conclusão**

- Todo ADR tem uma linha "o que pagamos": custo, complexidade, latência, dependência ou risco novo.

- O indicador "custo relativo das táticas" do simulador entra na justificativa. Com todas as táticas marcadas, ele vai de 34 (N3 com P2) a 68 (N4 com P4).

- Uma camada pode ficar em N3 de propósito. Nesse caso o ADR diz por que o ganho não paga o custo.

- O grupo responde à provocação do Painel: dos minutos que sobraram, quantos o SLA excluiria por serem incidente de segurança? Isso é justo com o cliente?

## Catálogo de mecanismos e seus trade-offs

Nenhum mecanismo é de graça: cada um ganha em um atributo e paga em custo, complexidade, latência ou em um risco novo. Os nomes seguem as táticas das abas 2 e 3 do site.

| Mecanismo                                      | Camada · nível | O que ganha                                 | O que paga                                                    | Quando não vale                        |
|------------------------------------------------|----------------|---------------------------------------------|---------------------------------------------------------------|----------------------------------------|
| Fila virtual / sala de espera                  | IHC · N3       | A: absorve rajadas sem erro                 | Espera e abandono do usuário; mais um serviço para operar     | Carga estável, sem picos               |
| Rate limit no gateway                          | IHC · N3       | A e C: corta abuso e bots simples           | Bloqueia usuários legítimos atrás do mesmo IP                 | Uso interno, poucos clientes           |
| Detecção de bots + MFA step-up                 | IHC · N4       | C: barra cambistas e contas invadidas       | Atrito no login; falsos positivos; custo do serviço           | Baixo valor por conta                  |
| Degradação graciosa automática                 | IHC · N4       | A: protege o checkout no pico               | Recursos desligados irritam; precisa definir o que é crítico  | Sistema com uma única função           |
| Workers assíncronos                            | LN · N3        | A: escala e isola a lentidão                | Consistência eventual; depuração mais difícil                 | Operações que exigem resposta imediata |
| Idempotência                                   | LN · N3        | I: retry sem cobrança ou assento duplicado  | Chave de idempotência armazenada; disciplina em todo endpoint | Operações só de leitura                |
| Circuit breaker                                | LN · N3        | A: uma dependência lenta não derruba tudo   | Respostas degradadas; limiares para calibrar                  | Dependências sem alternativa           |
| Autoscaling por métrica                        | LN · N3        | A: capacidade elástica                      | Reage com atraso; custo variável na fatura                    | Rajadas de segundos (chega tarde)      |
| SLO de negócio + atuadores (flags, throttling) | LN · N4        | A e I: o controlador age pelo negócio       | Instrumentação; risco de automação errada em cascata          | Sem métricas de negócio confiáveis     |
| Predição de carga                              | LN · N4        | A: capacidade antes do pico                 | Modelo erra; capacidade paga sem uso                          | Sem histórico ou sazonalidade          |
| Redundância passiva                            | Acesso · N3    | A: standby assume em minutos                | Custo de um nó parado; RTO com ressincronismo                 | Dados que podem esperar horas          |
| Redundância ativa com votação                  | Acesso · N4    | A e I: troca quase instantânea              | 2 a 3 vezes o custo; consistência entre réplicas              | Orçamento de indisponibilidade folgado |
| Outbox / mensageria                            | Acesso · N3    | I: nenhum evento perdido com o externo fora | Infraestrutura de mensageria; entrega atrasada                | Integrações síncronas e raras          |
| Verificação contínua de integridade            | Acesso · N4    | I: divergência corrigida sozinha            | Processamento extra; regras de reconciliação                  | Dados sem invariantes claros           |
| Backup imutável + point-in-time                | Acesso · N4    | I e A: ransomware sem perda (RPO zero)      | Armazenamento; testes de restauração periódicos               | — (pouco caso para não ter)            |
| TLS em trânsito                                | P1             | C: interceptação inútil                     | Certificados para renovar                                     | — (obrigatório)                        |
| Cifra simétrica em repouso + KMS               | P2             | C: cópia do banco ilegível                  | Gestão e rotação de chaves; um pouco de latência              | — (padrão para dado pessoal)           |
| mTLS, JWT assinado, envelope encryption        | P3             | C e I: autenticidade entre serviços         | PKI para operar; complexidade de rotação                      | Monolito sem chamadas entre serviços   |
| Tokenização e pseudonimização                  | P4             | C e LGPD: o dado nem existe no sistema      | Dependência do provedor de tokens; relatórios mais difíceis   | Sistema sem dado pessoal sensível      |

## Modelo de ADR

Um ADR por decisão, curto o suficiente para caber em meia página. A evidência vem da planilha e dos prints, não da opinião.

```markdown
# ADR-0X · <decisão em uma linha>

Camada: IHC | LN | Acesso      Atributo: C | I | A      Nível: N3 → N4

## Contexto
O que a evidência mostrou (rodada, métrica, print).

## Decisão
O mecanismo adotado e onde ele fica no 3-tier.

## Alternativas consideradas
Ao menos uma, e por que foi descartada.

## Consequências
Ganho: a métrica antes → depois (do simulador).
O que pagamos: custo, complexidade, latência, dependência ou risco novo.
Janela cibernética: monitorar | resistir | detectar | recuperar | predizer.

## Como verificar
A medida do cenário de qualidade (card 7): MTTD, RTO, RPO, registros expostos.
```

**Exemplo preenchido (G3, Black Friday)**

```markdown
# ADR-03 · Redundância ativa no banco de pedidos

Camada: Acesso      Atributo: A      Nível: N3 → N4

## Contexto
Na R3, a queda do banco primário levou 4 min para ser detectada e
15 min para o operador promover o standby. Somados, 19 min dos 43 min
que a meta de 99,9% permite no mês inteiro (44% do orçamento em uma noite).

## Decisão
Banco em ativo-ativo com 3 réplicas e votação; o controlador descarta
o nó que diverge, sem esperar o operador.

## Alternativas consideradas
Manter a redundância passiva e automatizar a promoção do standby.
Descartada: o ressincronismo continua custando minutos no pico.

## Consequências
Ganho: indisponibilidade 19 min → 0 min na simulação da R4.
O que pagamos: cerca de 3× o custo do banco; latência de escrita maior;
precisamos tratar consistência entre réplicas.
Janela cibernética: detectar + recuperar sem humano.

## Como verificar
MTTD ≤ 1 min, RTO ≤ 2 min, RPO = 0, em teste de falha mensal.
```

## Rota de cada grupo

Todos os grupos passam pelas 4 rodadas, mas cada um persegue uma métrica diferente. Os incidentes abaixo já vêm ligados quando o grupo clica em "Restaurar valores do caso".

| Grupo | Caso                  | Incidentes ligados                                               | Métrica que o grupo persegue                                    | ADR provável na R4                                 | Armadilha a evitar                                                  |
|-------|-----------------------|------------------------------------------------------------------|-----------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| G1    | Ingressos · foco A    | Queda do banco; deadlock na LN                                   | Minutos fora e pedidos perdidos na abertura                     | Predição de carga + degradação graciosa            | Achar que a fila resolve tudo: ela troca erro por abandono          |
| G2    | Ingressos · foco I    | Os do G1 + falha do pagamento externo                            | Assentos vendidos em duplicidade                                | Verificação contínua de integridade + idempotência | Medir só disponibilidade; em N2 as duplicidades sobem               |
| G3    | Black Friday · foco A | Queda do banco; deadlock na LN                                   | Orçamento de 43 min do mês                                      | Redundância ativa no banco                         | N2 quase não melhora: o problema é a capacidade fixa                |
| G4    | Black Friday · foco C | Os do G3 + ransomware + interceptação Wi-Fi                      | Registros expostos e RPO                                        | Backup imutável + tokenização (P4)                 | Confundir TLS (trânsito) com cifra em repouso                       |
| G5    | À escolha · ataque    | Queda do banco; deadlock; bots + credential stuffing; ransomware | Contas invadidas, ingressos para bots, minutos excluídos do SLA | Detecção de bots + MFA step-up                     | Em N3 o sistema vende mais para bots: disponibilidade sem segurança |

O G5 pode rodar os dois casos e comparar: o mesmo ataque custa diferente em ingressos (cambismo) e na Black Friday (contas invadidas).

## Planilha de coleta de evidências

Cada grupo copia esta tabela e preenche uma linha por rodada, com os números do print. O nome do arquivo do print vai na última coluna (ex.: G3-R2.png).

| Rodada  | Níveis efetivos IHC / LN / Acesso | Privacidade | Minutos fora (% do orçamento) | Pedidos perdidos | Inconsistências | Registros expostos / contas invadidas | MTTD / MTTR (min) | Quem detectou | Custo relativo | Print |
|---------|-----------------------------------|-------------|-------------------------------|------------------|-----------------|---------------------------------------|-------------------|---------------|----------------|-------|
| R1 · N1 |                                   | P0          |                               |                  |                 |                                       |                   |               |                |       |
| R2 · N2 |                                   | P1          |                               |                  |                 |                                       |                   |               |                |       |
| R3 · N3 |                                   | P2          |                               |                  |                 |                                       |                   |               |                |       |
| R4 · N4 |                                   |             |                               |                  |                 |                                       |                   |               |                |       |

Abaixo da tabela, uma frase por rodada: **o que mudou e por quê**. Essas frases viram o "Contexto" dos ADRs.

## Valores de referência para o professor

Com os valores padrão do site, N3 é o grande salto: quatro dos cinco grupos passam a caber na meta do mês. O G4 só cabe em N4, porque o ransomware ainda para o sistema por 60 minutos em N3. Configuração usada: R1 = N1 + P0, R2 = N2 + P1, R3 = N3 + P2, R4 = todas as táticas de N4 + P4.

*[Gráfico: minutos fora do ar por rodada, como % do orçamento mensal, grupos G1–G5 — disponível na versão .docx e no documento online.]*

Simulador do Laboratório CIA, valores padrão de cada caso · G5 no caso Ingressos · R4 com todas as táticas de N4

| Grupo          | Pedidos perdidos R1 → R4                 | Inconsistências R1 → R4                          | Registros expostos R1 → R4    | Outros sinais                                               |
|----------------|------------------------------------------|--------------------------------------------------|-------------------------------|-------------------------------------------------------------|
| G1             | 288.063 → 245.392 → 66.461 → 5           | 990 → 559 → 71 → 0 (5 corrigidas sozinhas)       | 0 em todas                    | MTTD 45 → 0,8 min                                           |
| G2             | 288.063 → 247.392 → 66.461 → 5           | 990 → 1.059 → 71 → 0                             | 0 em todas                    | Em N2 as duplicidades **sobem**: retry sem idempotência     |
| G3             | 2.068.099 → 1.876.687 → 212.663 → 0      | 1.624 → 2.570 → 408 → 0 (43 corrigidas sozinhas) | 0 em todas                    | N2 quase igual a N1: capacidade fixa                        |
| G4             | 2.087.599 → 2.027.774 → 774.681 → 85.500 | 1.258 → 1.327 → 284 → 0                          | 2.000.975 → 2.000.000 → 0 → 0 | RPO: tudo → 24 h → 1 h → zero; 70 → 12 min excluídos do SLA |
| G5 (ingressos) | 300.585 → 282.087 → 99.361 → 4.612       | 958 → 503 → 62 → 0                               | 201.000 → 200.800 → 200 → 0   | Ingressos para bots: 2.261 → 4.348 → 32.507 → 3.528         |

O custo relativo das táticas é o mesmo para todos os grupos nesta configuração: 0 → 8 → 34 → 68. O G5 no caso Black Friday estoura o orçamento até N3 (206%) e fica em 28% em N4, como o G4.

## Barema de avaliação

São 10 pontos. O peso maior está na qualidade dos trade-offs, não no nível alcançado.

| Critério                      | Peso | Atende plenamente quando                                                                                         |
|-------------------------------|------|------------------------------------------------------------------------------------------------------------------|
| Evidências das rodadas 1 a 3  | 2    | 3 prints legíveis e planilha completa, com a frase "o que mudou e por quê" em cada rodada                        |
| Rigor do experimento          | 1    | Parâmetros e incidentes iguais nas 4 rodadas; ao menos um teste de tática isolada                                |
| Diagnóstico                   | 1,5  | As 2 ou 3 métricas críticas apontadas com números e com o atributo C, I ou A afetado                             |
| ADRs e trade-offs             | 3    | De 3 a 5 ADRs completos; cada um com alternativa descartada, "o que pagamos" concreto e a métrica antes → depois |
| Coerência 3-tier e maturidade | 1    | Cada ADR situado na camada certa; nível declarado igual ao efetivo                                               |
| Privacidade                   | 0,5  | Escolha entre P3 e P4 justificada pelo dado que o caso trata                                                     |
| Provocação do SLA             | 1    | Posição do grupo sobre os minutos excluídos, com argumento                                                       |
