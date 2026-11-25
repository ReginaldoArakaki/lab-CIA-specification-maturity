T17 · aula de 25/11/2026 · Especificação para comportamento dinâmico

# A mesma especificação produz arquiteturas diferentes. O que muda é o nível de maturidade das decisões.

Escala de maturidade de ADRs (Arakaki): do código sem tática à malha fechada cibernética. Escolha um degrau. Todo o site (simulador, ADRs e caminho) acompanha o nível selecionado.

Casos: Especificação de Disponibilidade do E-commerce (documental, estática) e Case 02 Fam Show Ticket (Specification as Behavior).

Escala de maturidade de ADRs

[Roteiro 90 min](#aula)[Conceitos](#conceitos)[Dinâmica 1](#diagnostico)[Simulador](#simulador)[ADRs por nível](#adrs)[Dinâmica 2](#din2)[Caminho](#caminho)[Consolidação](#consolidacao)

## Roteiro: 90 minutos, 5 grupos, do documento ao laço de controle

Fio condutor: cada grupo sai da aula com um ADR que sobe pelo menos um degrau, sustentado por uma tática nomeada e por um cenário de aferição que pode ser medido.

### Distribuição de tempo

Proposta de cronometragem. Ajuste a Consolidação se a Dinâmica 2 render mais.

### Entregáveis por grupo

- Dinâmica 1: diagnóstico da spec do E-commerce no seu foco CIA, com o nível atual e um ADR reescrito para o próximo nível.
- Dinâmica 2: para uma tática do Fam Show, a mesma exigência escrita **as Test**, **as Behavior** e **as Code**.
- Consolidação: o artefato posicionado na escada, com justificativa pela régua de 6 perguntas.

## Conceitos: um RNF só vira arquitetura quando tem tática, cenário e medida

### CIA como atributos de qualidade

**Confidencialidade** (autenticar, autorizar, limitar exposição, criptografar), **Integridade** (transações, validação, trilha de auditoria, invariantes de estoque) e **Disponibilidade** (detectar, recuperar e prevenir falhas). Nos dois casos, o "A" aparece especificado e o "C" e o "I" aparecem só como listas.

### Cenário de atributo de qualidade (6 partes)

**Fonte** · **Estímulo** · **Artefato** · **Ambiente** · **Resposta** · **Medida da resposta**. Sem a medida não há aferição, e sem aferição não existe controle. A medida é o que permite passar de N2 para N3.

### Táticas de disponibilidade (Bass et al.)

**Detectar:** ping/echo, heartbeat, monitor, condition monitoring, sanity check. **Recuperar:** redundância ativa/passiva, rollback, retry, degradação, reconfiguração. **Prevenir:** remoção de serviço, transações, modelo preditivo.

### Onde um RNF de disponibilidade aparece nas 5 visões (4+1)

| Visão | E-commerce (checkout 99,9%) | Fam Show (abertura das vendas) |
| --- | --- | --- |

### As formas de uma mesma especificação

| Forma | O que é | Quem executa | Nível típico |
| --- | --- | --- | --- |

## Dinâmica 1: as duas especificações têm números, mas nenhuma fecha o laço

Problematização para os grupos. Os achados abaixo são o gabarito do professor. Antes de mostrá-los, peça a cada grupo que encontre pelo menos dois no seu foco CIA.

### E-commerce: especificação documental estática

### Fam Show: specification as behavior

### Focos dos grupos na Dinâmica 1 (spec do E-commerce)

| Grupo | Foco CIA | Pergunta-guia | Tática a propor |
| --- | --- | --- | --- |

## Simulador: o mesmo incidente, a mesma especificação e quatro desfechos diferentes

Valores ilustrativos para a aula. Os tempos de detecção e recuperação foram arbitrados por nível; a latência do Fam Show usa W = S/(1−ρ) com S = 50 ms, o mesmo modelo M/M/1 da simulação da turma.

### Arquitetura resultante neste nível

### Por que o resultado muda

## ADRs por nível

A mesma decisão de arquitetura, escrita em cada degrau. Use o seletor do topo ou os botões abaixo. Compare o que aparece a cada nível: tática nomeada, cenário com medida, sensor em produção e atuador automático.

### Comparação lado a lado

| Aspecto | N1 | N2 | N3 | N4 |
| --- | --- | --- | --- | --- |

O ADR-001 original do case Fam Show fica entre N1 e N2: tem decisão e consequências, mas não tem tática medida nem cenário. Ele também atribui à fila a prevenção de overselling, o que é papel da reserva atômica.

## Dinâmica 2: escrever a mesma exigência as Test, as Behavior e as Code

Cada grupo recebe uma tática do Fam Show. A régua é a seguinte: o comportamento é especificado com medida, o teste reproduz o estímulo, e o código observa em produção e, no N4, também age.

| Grupo | Tática / regra | as Behavior (Gherkin com medida) | as Test | as Code (sensor/atuador) | Gráfico da simulação |
| --- | --- | --- | --- | --- | --- |

### Exemplo resolvido (G1 · admissão controlada)

as Behavior · Gherkin

```
Funcionalidade: Admissão controlada na abertura das vendas
  Cenário: Flash crowd com degradação do banco
    Dado 5.000 usuários na sala de espera às 10:00
    E a capacidade do checkout cai 30% aos 8 minutos
    Quando a venda é aberta
    Então a latência p95 do checkout fica abaixo de 2 s
      em pelo menos 99% dos minutos da 1ª hora
    E a vazão de admissão é reajustada em até 60 s
      após a degradação
    E vendidos + reservas ativas nunca excede 450
```

as Test · k6

```
export const options = {
  scenarios: { abertura: {
    executor: 'ramping-arrival-rate',
    timeUnit: '1s', preAllocatedVUs: 2000,
    stages: [{ target: 1000, duration: '30s' },
             { target: 1000, duration: '5m' }] } },
  thresholds: {
    'http_req_duration{step:checkout}': ['p(95)<2000'],
    'http_req_failed': ['rate<0.005'],
    'oversold_tickets': ['count==0'] // Counter customizado
  }
};
```

as Code · sensor (Prometheus, N3)

```
groups:
- name: famshow-slo
  rules:
  - alert: CheckoutP95AcimaDoSLO
    expr: histogram_quantile(0.95, sum by (le)
      (rate(http_request_duration_seconds_bucket
      {route="/checkout"}[1m]))) > 2
    for: 1m
    labels: { severity: critical }
  - alert: InvarianteDeEstoqueViolada
    expr: tickets_sold_total + tickets_reserved_active > 450
    labels: { severity: page }
```

as Code · controlador (N4, fecha o laço)

```
# executa a cada 10 s: setpoint ρ = 0,75 (Lei de Little + M/M/1)
mu   = capacidade_medida('checkout')   # sensor: req/s sem erro
p95  = sli('checkout.latencia.p95')    # sensor técnico
conv = sli('negocio.conversao')        # sensor de negócio
if estoque_livre() == 0 and reservas_ativas() == 0:
    fila.encerrar('Esgotado')          # atuador de negócio
lam = 0.75 * mu                        # vazão-alvo
if p95 > 1.5: lam *= 0.8               # margem antes do SLO de 2 s
if conv < 0.5 * baseline_conv(): alerta('anomalia de negócio')
fila.definir_vazao(lam)                # atuador: tática de admissão
```

## O caminho: subir de degrau é aprofundar táticas, cenários de aferição e controle

Para mudar de nível, as três trilhas precisam avançar juntas. Uma tática sem cenário continua em N2. Um cenário medido sem atuador fica em N3. Só se chega a N4 quando a medida aciona a tática sem depender de um humano.

| Trilha | N1 · Sem controle | N2 · Processos manuais | N3 · Malha aberta | N4 · Malha fechada |
| --- | --- | --- | --- | --- |

### Leitura da simulação da turma (Fam Show)

Os quatro gráficos mostram uma malha aberta muito bem dimensionada. A vazão fixa de 37,5 req/s vem da Lei de Little (a posição 2.250 corresponde a cerca de 1 min de espera) e mantém ρ = 75%, com cerca de 200 ms de latência no modelo M/M/1. A gestão atômica de estoque já tem comportamento de N4, porque a expiração automática de reservas (RN004/RN005) devolve estoque sem intervenção humana. A fila ainda está em N3, porque os 37,5 req/s são uma constante de projeto. A fila passa a N4 quando a vazão vira variável controlada, recalculada a partir de μ e do p95 medidos. É exatamente o caso que o simulador acima mostra, quando o banco perde 30% de capacidade.

## Consolidação: régua de 6 perguntas para posicionar qualquer ADR na escada

### Perguntas de fechamento (exit ticket)

1. Por que um RTO de 1 h é incompatível com um orçamento de cerca de 43 min/mês?
2. Qual a diferença entre "monitorar" (N3) e "controlar" (N4) no seu ADR?
3. Qual sinal de negócio do seu caso detecta uma falha antes da CPU subir?
4. Que parte do seu ADR um humano ainda precisa decidir, e por quê?

Material de apoio à aula T17 · PCS/USP · Prof. Reginaldo Arakaki · Escala de maturidade de ADRs conforme manuscrito do professor. Exemplos numéricos do simulador são didáticos e não medições reais.