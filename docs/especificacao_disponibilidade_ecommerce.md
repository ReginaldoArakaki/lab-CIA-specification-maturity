# Especificação de Disponibilidade - Plataforma de E-commerce

## 1. Objetivo

Garantir a disponibilidade da plataforma de e-commerce para clientes finais, assegurando a continuidade das operações de navegação, pesquisa de produtos, carrinho de compras e finalização de pedidos.

## 2. Meta de Disponibilidade

A plataforma deverá manter uma disponibilidade mensal mínima de **99,9% (noventa e nove vírgula nove por cento)**.

### Disponibilidade permitida

| Disponibilidade | Indisponibilidade máxima por mês |
|---------------|-----------------------------|
| 99,0% | ~7,2 horas |
| 99,9% | ~43 minutos |
| 99,99% | ~4,3 minutos |

## 3. Definição de Disponibilidade

O serviço será considerado disponível quando:

- O site responder requisições HTTPS com sucesso;
- As páginas principais estiverem acessíveis;
- O carrinho de compras estiver funcional;
- O checkout puder ser concluído;
- A API de autenticação estiver operacional.

## 4. Método de Medição

A disponibilidade será medida por monitoramento automático externo executado a cada 1 minuto a partir de, no mínimo, três localidades distintas.

### Fórmula

```text
Disponibilidade (%) =
(Total de minutos do período - Minutos de indisponibilidade)
÷ Total de minutos do período × 100
```

## 5. Exclusões

Não serão considerados como indisponibilidade:

- Janelas de manutenção programada previamente comunicadas;
- Eventos de força maior;
- Falhas de provedores terceiros fora do controle do contratante;
- Ataques DDoS ou incidentes de segurança de grande escala;
- Problemas de conectividade do usuário final.

## 6. Janelas de Manutenção

- Manutenções programadas: domingos entre 00h00 e 04h00.
- Comunicação mínima: 72 horas de antecedência.
- O tempo de manutenção programada não será contabilizado no cálculo do SLA.

## 7. Tempo de Recuperação (RTO)

| Severidade | Tempo máximo de recuperação |
|------------|---------------------------|
| Crítica (checkout indisponível) | 1 hora |
| Alta (site acessível com degradação) | 4 horas |
| Média | 8 horas |
| Baixa | 24 horas |

## 8. Penalidades

Caso a meta mensal de 99,9% não seja atingida:

| Disponibilidade Mensal | Crédito |
|------------------------|----------|
| 99,0% a 99,89% | 5% |
| 95,0% a 98,99% | 10% |
| Abaixo de 95% | 20% |

O crédito será aplicado na próxima fatura do serviço.

## Exemplo para um e-commerce médio

- Disponibilidade alvo: 99,9%
- Monitoramento: 24x7
- RTO: 1 hora para falhas críticas
- Janela de manutenção: Domingo 00h às 04h
- Tempo máximo de indisponibilidade mensal: aproximadamente 43 minutos
