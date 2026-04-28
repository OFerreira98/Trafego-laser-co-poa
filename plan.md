# Plano: Dashboard L&C Porto Alegre

## Contexto

Dashboard HTML para apresentar resultados de campanhas Meta Ads das 3 unidades franqueadas da Laser&Co em Porto Alegre. Baseado na skill `dashboard-forms-ads`, adaptado de Lead Forms para campanhas de conversas via WhatsApp, e de agrupamento por produto para agrupamento por unidade franqueada.

## Adaptações em relação à skill original

| Aspecto | Skill original | Este projeto |
|---|---|---|
| Entidade | Máquinas/produtos | Unidades (franquias) |
| Métrica | Leads de formulário | Conversas via WhatsApp |
| Fonte de leads | Google Sheets via URL | XLSX exportado do Ads Manager |
| Agrupamento | Grupos (Máquinas, Franquias) | Cada unidade é seu próprio grupo |
| Saldo | Orçamento global compartilhado | Saldo por conta de anúncios |

## Estrutura de dados

```js
const FRANQUIAS = [
  {
    id: string,          // identificador único (iguatemi, patio24, praiadb)
    nome: string,        // exibido nas tabs e legenda
    cor: string,         // hex — paleta: azul, roxo, laranja
    investimento: float, // gasto total na campanha principal (do XLSX)
    saldoConta: float,   // calculado: adicionado - cobrado(invoice) - hoje(XLSX)
    leads: [             // conversas por dia
      { data: "YYYY-MM-DD", count: int }
    ]
  }
]
```

## Cálculo de saldo

```
saldo = Total adicionado (invoice)
      − Total cobrado (invoice)       ← cobre até D-1
      − Gasto de hoje em todas as campanhas (XLSX)
```

O invoice fecha com 1 dia de atraso. O XLSX captura o gasto parcial do dia corrente.

## Decisões tomadas

**Campanhas incluídas por unidade**
- Iguatemi: somente `[INAUGURAÇÃO] - IGUATEMI - CBO` (ativa). Campanhas CAMP ENGAJA ignoradas (inativas, não são o foco).
- Pátio 24: somente `[ANIVERSAIRO] - PATIO 24 - CBO` (campanha atual). CAMP ENGAJA ignorada (rodou de 03/04 a 10/04, antes da inauguração).
- Praia de Belas: somente `[ANIVERSAIRO] - PRAIA BELA - CBO`.

**Invoices mapeados**
- Iguatemi → Invoice original (conta 1212149340997467, LASER COMPANY PRAIA DE BELAS LTDA): R$4.000 / R$3.234,35
- Pátio 24 → Invoice (3) (conta 2161966114622847): R$3.000 / R$2.346,38
- Praia de Belas → Invoice (2) (conta 860865039565964): R$2.500 / R$1.542,41

**Saldo Iguatemi**
Invoice cobre até 27/04; XLSX baixado às 05:44 de 28/04 (R$4,30 parcial). Saldo confirmado diretamente pelo gerenciador: R$630,26. Para os próximos, usar o valor real do gerenciador se houver divergência.

## Período dos dados

| Unidade | Início campanha | Último dia com conversas |
|---|---|---|
| Iguatemi | 11/04/2026 | 28/04/2026 (em andamento) |
| Pátio 24 | 11/04/2026 | 27/04/2026 (em andamento) |
| Praia de Belas | 11/04/2026 | 24/04/2026 (pausada) |

## Resultados (Abril 2026)

| Unidade | Conversas | Investido | CPL | Saldo |
|---|---|---|---|---|
| Iguatemi | 221 | R$1.703,74 | R$7,71 | R$630,26 |
| Pátio 24 | 223 | R$1.332,24 | R$5,98 | R$651,87 |
| Praia de Belas | 399 | R$1.355,02 | R$3,40 | R$957,59 |
| **Total** | **843** | **R$4.391,00** | **R$5,21** | |

## Próximos passos

- [ ] Hospedar em GitHub Pages ou Netlify para compartilhar com o cliente
- [ ] Atualizar mensalmente com novos exports (campanha XLSX + invoice CSV por unidade)
- [ ] Verificar se campanha Praia de Belas foi reativada ao gerar o próximo relatório
