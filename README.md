# Konstan

Dashboard web do **Clube de Investimento Konstan**. Acompanha a evolução da cota do clube e compara o desempenho contra os principais benchmarks (CDI, Ibovespa e S&P 500 em BRL), com métricas de risco e retorno, controle de aportes e exportação de relatório em PDF.

É uma aplicação estática de página única (`index.html`) publicada no **GitHub Pages**, que consome os dados de um bucket público do **Supabase Storage**.

## Funcionalidades

- **Evolução da cota** — série normalizada (base 1,0 no início) da cota Konstan vs. CDI, Ibovespa e S&P 500 (em BRL), com toggles para ligar/desligar cada benchmark e o retorno acumulado do período.
- **Performance** — retornos anuais e mensais, tabela de cotas mensais e comparativo de retorno acumulado.
- **Indicadores de risco/retorno** — Índice de Sharpe, Índice de Sortino, drawdown máximo, downside deviation, dispersão risco × retorno, distribuição (histograma) dos retornos diários, curva de drawdown, PL vs. aportes acumulados e **excess return** (excedente em R$ frente a CDI, Ibovespa e S&P 500).
- **Investidores** — área protegida por login (autenticação via Supabase) com a posição por cotista.
- **Relatório** — exportação de um PDF com os principais gráficos e métricas do período.

## Estrutura

O projeto é intencionalmente simples:

```
.
├── index.html                   # aplicação completa (HTML + CSS + JS inline)
├── README.md
└── .github/workflows/deploy.yml # publicação automática no GitHub Pages
```

Bibliotecas carregadas via CDN: **Chart.js** (gráficos), **Supabase JS** (dados e autenticação) e bibliotecas de geração de PDF.

## Dados

Os dados são lidos de um arquivo JSON no Supabase Storage (bucket `dados`, arquivo `alldata.json`), que deve ser um array de registros diários no formato:

| Campo | Descrição |
|-------|-----------|
| `d`   | data (`YYYY-MM-DD`) |
| `k`   | cota Konstan (base 1,0 no início) |
| `c`   | índice CDI (base 1,0) |
| `i`   | índice Ibovespa (base 1,0) |
| `s`   | índice S&P 500 em BRL (base 1,0) |
| `q`   | total de cotas emitidas na data |
| `a`   | aportes acumulados (R$) |

O PL do clube em qualquer data é `q × k`; o lucro bruto é `q × k − a`. Novos aportes aumentam `q` e `a` na data em que ocorrem.

## Como rodar localmente

Por ser um site estático, basta servir a raiz do projeto:

```bash
python3 -m http.server 8000
# abra http://localhost:8000
```

O bucket do Supabase precisa estar público (ou acessível) para que o `alldata.json` seja carregado.

## Deploy

O deploy é automático via GitHub Actions (`.github/workflows/deploy.yml`): cada push na branch `main` publica a raiz do repositório no GitHub Pages. Requer **Settings → Pages → Source: "GitHub Actions"**.
