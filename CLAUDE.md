# Dashboard Laser&Co Porto Alegre

Dashboard HTML único para apresentação de resultados de campanhas Meta Ads das 3 unidades da rede L&C Porto Alegre.

## Arquivo principal

`dashboard.html` — arquivo único, sem dependências externas além de CDN (Chart.js + Google Fonts). Funciona offline com duplo clique.

## Estrutura do projeto

```
Dashboard Leads ADS/
└── dashboard.html   # dashboard completo com dados embutidos
```

## As 3 unidades

| Unidade | ID no código | Cor | Campanha principal |
|---|---|---|---|
| L&C Iguatemi | `iguatemi` | `#4f6af0` | [INAUGURAÇÃO] - IGUATEMI - CBO |
| L&C Pátio 24 | `patio24` | `#7c3aed` | [ANIVERSAIRO] - PATIO 24 - CBO |
| L&C Praia de Belas | `praiadb` | `#f97316` | [ANIVERSAIRO] - PRAIA BELA - CBO |

## Fontes de dados

Dois arquivos por unidade, exportados do Meta Ads Manager:

### 1. Relatório de campanhas (XLSX com detalhamento por dia)
- Ads Manager → Campanhas → selecionar período → **Detalhamentos → Tempo → Dia** → Exportar
- Colunas usadas: `Início dos relatórios` (col 1), `Nome da campanha` (col 3), `Conversas por mensagem iniciadas` (col 4), `Valor usado` (col 6)
- Filtrar apenas a campanha principal de cada unidade (ignorar campanhas CAMP ENGAJA inativas)

### 2. Extrato financeiro (Invoice Summary CSV)
- Ads Manager → Faturamento → Histórico de pagamentos → Exportar CSV
- Colunas usadas: `Total de fundos adicionados`, `Valor total cobrado`

## Como atualizar o dashboard

1. Baixar os dois arquivos de cada unidade (campanha XLSX + invoice CSV) **no mesmo momento**, preferencialmente ao final do dia
2. Ler com PowerShell:
   ```powershell
   $excel = New-Object -ComObject Excel.Application
   $excel.Visible = $false; $excel.DisplayAlerts = $false
   $wb = $excel.Workbooks.Open("C:\caminho\arquivo.xlsx")
   $ws = $wb.Sheets.Item(1)
   # ler linhas filtrando pelo nome da campanha...
   $wb.Close($false); $excel.Quit()
   ```
3. Atualizar o array `FRANQUIAS` no `dashboard.html` com os novos dados
4. Calcular saldo: `Total adicionado (invoice) − Total cobrado (invoice) − Gasto do dia atual (XLSX)`

## Cálculo do saldo

O invoice sempre fica 1 dia atrasado. O gasto do dia corrente aparece no XLSX mas não no invoice.

```
saldo = totalAdicionado(invoice) − totalCobrado(invoice) − gastoHoje(XLSX, todas campanhas)
```

**Regra crítica — campanha ativa vs pausada:**
- **Campanha PAUSADA/ENCERRADA** → fórmula é exata (sem gasto após o invoice). Usar direto.
- **Campanha ATIVA** → invoice fica D-1 e o XLSX baixado cedo captura só gasto parcial do dia → fórmula dá valor ACIMA do real. **Sempre pedir o saldo real** exibido em "Saldo pré-pago" ou "Saldo disponível" no Gerenciador de Anúncios.

Verificar a coluna "Veiculação da campanha" no XLSX: `active` = pedir saldo; `inactive` = calcular pelo invoice.

## Métrica principal

Estas são campanhas de **conversas via WhatsApp** (não Lead Forms). O indicador de resultado é `actions:onsite_conversion.messaging_conversation_started_7d`. O dashboard exibe "Conversas" em vez de "Leads".

## WhatsApp do gestor

Número: `5545998247530` (embutido no botão do dashboard)

## Hospedagem

Netlify Drop: arrastar `dashboard.html` em `app.netlify.com/drop`
GitHub Pages: upload do arquivo em repositório público → Settings → Pages → branch main
