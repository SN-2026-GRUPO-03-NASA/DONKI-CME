# ☀️ CME Watch

Painel que acompanha as **Ejeções de Massa Coronal (CME)** do Sol usando dados da API DONKI da NASA. Os dados são coletados todo dia de forma automática, guardados no Supabase e mostrados em uma página publicada no GitHub Pages.

![Python](https://img.shields.io/badge/Python-3.11-blue)
![GitHub Actions](https://img.shields.io/badge/GitHub-Actions-2088FF)
![Supabase](https://img.shields.io/badge/Supabase-PostgreSQL-3ECF8E)
![GitHub Pages](https://img.shields.io/badge/GitHub-Pages-222222)

🔗 **Painel online:** https://sn-2026-grupo-03-nasa.github.io/DONKI-CME/

---

## 🌞 O que são CMEs?

São grandes explosões de plasma e campo magnético que saem do Sol. Quando uma delas vem na direção da Terra, pode causar tempestades geomagnéticas e atrapalhar satélites, GPS, redes elétricas e rádio. Acompanhar esses eventos é importante para a previsão de clima espacial.

## ✨ O que o projeto faz

- Coleta os dados de CME dos últimos 30 dias todos os dias, sem ninguém precisar rodar nada
- Guarda os eventos no banco sem repetir registros
- Mostra um painel com cards, gráfico de velocidade e tabela ordenável
- Filtros por período: **7D, 30D, 90D e 1A**
- Janela de detalhes ao clicar em um evento
- Mostra quando foi a última atualização do pipeline

## 🧩 Como funciona

```
NASA DONKI (API CCMC)  →  GitHub Actions (Python)  →  Supabase (PostgreSQL)  →  GitHub Pages (painel)
```

1. **NASA DONKI:** fonte dos dados em JSON.
2. **GitHub Actions:** roda o script `fetch_nasa.py` todo dia às 06:00 UTC, trata os dados e envia ao banco.
3. **Supabase:** guarda os eventos (`cme_events`) e o log de execuções (`execucoes`).
4. **GitHub Pages:** hospeda o HTML, CSS e JS que leem o Supabase e montam o painel.

## 📁 Estrutura

```
DONKI-CME/
├── index.html                       # painel
├── script.js                        # lógica do painel e conexão com o Supabase
├── style.css                        # estilos
├── scripts/
│   └── fetch_nasa.py                # coleta e envio ao banco
├── sql/
│   └── setup.sql                    # tabelas, RLS e GRANTs
├── .github/workflows/
│   └── update-data.yml              # agendamento automático
└── docs/
    └── tutorial.pdf                 # passo a passo para replicar
```

## 🚀 Como replicar

O passo a passo completo está em [`docs/tutorial.pdf`](docs/tutorial.pdf). Resumo:

1. Crie o repositório no GitHub e ative o **Pages** (branch `main`, pasta `/ (root)`).
2. Crie um projeto no **Supabase** e rode o `sql/setup.sql` no SQL Editor.
3. No GitHub, em *Settings → Secrets and variables → Actions*, crie:

   | Secret | Valor |
   |---|---|
   | `SUPABASE_URL` | URL do projeto Supabase |
   | `SUPABASE_SERVICE_KEY` | chave `service_role` |

4. No `script.js`, coloque a URL e a chave **anon** (pública) do Supabase.
5. Em *Actions → Update Data → Run workflow*, rode uma vez na mão para popular o banco.

> ⚠️ **Nunca** coloque a chave `service_role` no código, em commits ou prints. Só a chave `anon` pode ficar no frontend.

## 🧪 Rodando o script no computador (opcional)

```bash
pip install requests supabase

# PowerShell
$env:SUPABASE_URL = "https://seu-projeto.supabase.co"
$env:SUPABASE_SERVICE_KEY = "sua-service-key"

python scripts/fetch_nasa.py
```

## 🔐 Segurança

- Chave `service_role` só como Secret do GitHub
- RLS ligado nas duas tabelas: o painel (chave `anon`) só consegue ler
- Todo dado externo passa por `escapeHtml()` antes de entrar no HTML (proteção contra XSS)
- O workflow usa `permissions: contents: read`

## 🛠️ Tecnologias

Python · Supabase (PostgreSQL) · GitHub Actions · GitHub Pages · JavaScript · Canvas · HTML/CSS

## 📡 Fonte dos dados

[NASA DONKI](https://ccmc.gsfc.nasa.gov/tools/DONKI/) (CCMC). Usamos o endpoint:

```
https://ccmc.gsfc.nasa.gov/DONKI-API/get/CME?startDate=AAAA-MM-DD&endDate=AAAA-MM-DD
```

O endpoint antigo `api.nasa.gov/DONKI/CME` começou a retornar HTTP 503 em 30/09/2026, por isso migramos para o do CCMC, que não precisa de chave de API.

## 👥 Grupo 03

- Murilo de Souza Cândido
- Guilherme
- Yasmin C.

Sistemas de Informação · IFPR · 3º Trimestre de 2026
