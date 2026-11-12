# Guia completo para apresentar o CME Watch

## 1. O que é pedido no PDF e o que foi pedido aqui

O PDF anexado é o enunciado da atividade avaliativa. Ele define o que o grupo precisa entregar e como o seminário será avaliado. A solicitação feita nesta conversa é diferente: produzir uma explicação completa do projeto, do código, da arquitetura e da API, além de preparar os slides para a apresentação.

O enunciado exige um pipeline completo de dados: coletar dados de uma API aberta da NASA, automatizar a coleta com GitHub Actions, armazenar os registros em PostgreSQL no Supabase, publicar um painel no GitHub Pages e explicar a solução em um seminário de até 20 minutos. Para o grupo 03, a API escolhida é DONKI/CME. O PDF também sugere 6 minutos para a API, 4 para a arquitetura, 8 para a demonstração e 2 para o uso de IA, seguidos de até 10 minutos de perguntas.

Antes da apresentação, o grupo ainda precisa comprovar no ambiente real os itens que dependem de GitHub e Supabase: execução verde do workflow, dados no banco, API REST retornando JSON, painel publicado, permissões do professor, região do projeto Supabase e links finais. O código local mostra como o sistema foi construído, mas a existência do código não prova sozinha que cada configuração externa está correta.

## 2. Resposta de 30 segundos

O CME Watch é um pipeline de dados científicos que acompanha ejeções de massa coronal do Sol. O GitHub Actions executa diariamente um script Python. O script consulta a API DONKI do CCMC, normaliza os registros, remove duplicidades e faz upsert em lotes no Supabase. O Supabase armazena os eventos em PostgreSQL e publica uma API REST protegida por RLS. O painel estático hospedado no GitHub Pages consulta essa API usando apenas a chave pública anon e apresenta filtros, estatísticas, gráfico, ordenação e detalhes dos eventos.

## 3. O problema que o projeto resolve

Dados científicos perdem valor quando precisam ser coletados manualmente, ficam espalhados em respostas JSON ou não podem ser consultados por pessoas que não conhecem a API. O projeto transforma uma fonte técnica em um fluxo repetível:

1. A API fornece os eventos brutos.
2. O pipeline executa sem depender de um computador do grupo ligado.
3. O banco cria uma cópia normalizada e consultável.
4. O painel mostra os dados de forma compreensível.
5. A tabela `execucoes` deixa rastreável quando a coleta ocorreu e se ela terminou com sucesso.

O objetivo não é prever sozinho uma tempestade geomagnética. O sistema organiza e apresenta observações de CMEs. A previsão científica exigiria modelos físicos, propagação, dados de vento solar e outras fontes.

## 4. A API DONKI/CME

DONKI é uma base da NASA com notificações e informações sobre eventos de clima espacial. CME significa Coronal Mass Ejection, ou ejeção de massa coronal: uma nuvem de plasma e campo magnético expelida pelo Sol.

O pipeline atual usa:

```text
https://ccmc.gsfc.nasa.gov/DONKI-API/get/CME
```

e envia `startDate` e `endDate` para consultar uma janela de tempo. O script busca os últimos 30 dias em cada execução.

O repositório também documenta uma migração importante. O endpoint antigo em `api.nasa.gov/DONKI/CME` passou a falhar com HTTP 503 no período registrado no diagnóstico. O novo endpoint CCMC respondeu HTTP 200 sem exigir `api_key`, mantendo os campos que o normalizador utiliza. Esse é um bom exemplo de manutenção: o código precisou mudar porque uma dependência externa mudou.

### Campos relevantes da resposta

- `activityID`: identificador natural do evento e chave de deduplicação.
- `startTime`: momento de início do evento.
- `sourceLocation`: localização de origem no Sol.
- `instruments`: instrumentos que observaram o evento.
- `cmeAnalyses`: uma lista com análises do evento.
- `linkedEvents`: eventos relacionados.
- `link`: referência para a fonte do evento.

Uma resposta pode ter mais de uma análise. O pipeline escolhe a que tem `isMostAccurate`; se nenhuma estiver marcada assim, usa a última análise da lista.

## 5. Arquitetura ponta a ponta

```text
NASA/CCMC DONKI
      |
      | JSON de CMEs
      v
GitHub Actions
      |
      | executa Python com secrets
      v
scripts/fetch_nasa.py
      |
      | normalização, deduplicação, upsert e log
      v
Supabase / PostgreSQL
      |
      | PostgREST com leitura pública controlada por RLS
      v
GitHub Pages
      |
      | index.html + style.css + script.js
      v
Painel CME Watch
```

Há dois fluxos de credencial:

- O pipeline usa `SUPABASE_SERVICE_KEY` somente dentro do GitHub Actions. Essa chave possui privilégio de serviço e nunca pode aparecer no frontend, em commits, prints ou documentação.
- O painel usa `SUPABASE_ANON_KEY`, que é uma chave pública de leitura. Ela pode aparecer no navegador, mas suas permissões precisam ser limitadas por RLS.

## 6. Estrutura do repositório

### `index.html`

É o shell do painel. Declara os campos de data, os botões de período, os cards de estatísticas, o canvas do gráfico, a lista de eventos, o modal de detalhes e a seção educativa sobre CMEs. Também contém o pequeno cálculo de tempo estimado de viagem usando a distância Sol-Terra e a velocidade escolhida.

### `style.css`

Define o layout responsivo, cores, tipografia, cards, estados de carregamento, modal e o tema visual solar. O arquivo tem uma primeira camada de estilo e uma camada final de sobrescritas do tema `SOLAR`. Em uma manutenção futura, vale consolidar as duas camadas para reduzir duplicidade.

### `script.js`

É o frontend vanilla JavaScript. Não existe framework nem etapa de build. Ele consulta o PostgREST do Supabase, transforma os registros em objetos de apresentação e atualiza o DOM.

### `scripts/fetch_nasa.py`

É o pipeline executado pelo Actions. Faz a coleta, a seleção da melhor análise, a normalização, a deduplicação, o upsert em lotes e o registro da execução.

### `sql/setup.sql`

Cria tabelas, índices, trigger, RLS, policies e GRANTs. É o contrato de infraestrutura do banco e deve ser executado no projeto Supabase correto.

### `.github/workflows/update-data.yml`

Define quando e em qual ambiente o pipeline roda. O arquivo não contém as chaves. Ele injeta secrets do GitHub como variáveis de ambiente no passo que executa o Python.

### `docs/`

Contém tutorial, histórico de interações com IA, reflexão, diagnóstico da migração da API e a apresentação. O PDF de apresentação antigo ainda cita o endpoint antigo e links provisórios. Por isso, os slides atualizados desta entrega devem ser usados no seminário.

## 7. Pipeline Python em detalhe

### 7.1 Configuração e segurança

`get_env_var()` lê variáveis de ambiente e encerra o processo se uma variável obrigatória não existir. O pipeline exige `SUPABASE_URL` e `SUPABASE_SERVICE_KEY`.

`sanitize_error_message()` remove padrões de `api_key` e tokens JWT das mensagens de erro. A intenção é manter logs úteis sem vazar credenciais.

### 7.2 Coleta

`fetch_nasa_data(start_date, end_date)` monta a chamada ao endpoint CCMC, define timeout de 80 segundos, tenta até três vezes e chama `raise_for_status()`. Um HTTP 4xx/5xx ou erro de rede não é tratado como lista vazia: depois da última tentativa, a exceção sobe para o pipeline e gera `erro_critico`.

### 7.3 Normalização

`normalize_event(raw)` converte o JSON da API em um formato estável para o banco. O registro contém `activity_id`, data, origem, nota, instrumentos, velocidade, tipo, latitude, longitude, meio-ângulo, link, eventos relacionados e horário da coleta.

Essa etapa reduz o acoplamento do frontend ao JSON original da NASA. O painel passa a depender do contrato da tabela `cme_events`, não da estrutura inteira da API externa.

### 7.4 Heurística de direcionamento à Terra

`is_earth_directed()` marca o registro quando latitude e longitude da análise estão ambas dentro de 30 graus do centro. Isso é uma heurística de triagem criada para o painel. Não deve ser apresentada como confirmação de trajetória ou previsão de impacto. O campo é útil para destacar eventos, mas a banca pode perguntar sobre essa limitação.

### 7.5 Deduplicação e upsert

`deduplicate_records()` usa um conjunto de `activity_id` para remover repetições antes de enviar os registros. Essa etapa evita o problema de múltiplos objetos com a mesma chave natural no mesmo upsert.

`upsert_batch()` envia cada lote com `on_conflict="activity_id"`. Se o evento já existe, ele é atualizado; se é novo, é inserido. O tamanho do lote é 50.

### 7.6 Registro e código de saída

`register_execution()` grava métricas na tabela `execucoes`: quantidade processada, número de lotes, número de erros, status e detalhes.

- `concluido`: todos os lotes terminaram.
- `erro_parcial`: pelo menos um lote foi gravado, mas outro falhou.
- `erro_critico`: a coleta falhou ou nenhum lote foi gravado.

O script encerra com código 1 em erro parcial ou crítico. Assim o GitHub Actions mostra a execução como falha, mesmo que o banco tenha recebido parte dos dados. Em sucesso, encerra com código 0.

## 8. Banco de dados e API REST

### `cme_events`

É a tabela principal. `activity_id TEXT UNIQUE NOT NULL` é a chave natural. Os campos de lista, como instrumentos e eventos ligados, usam JSONB. Há índices em `start_time`, `speed` e `is_earth_directed` para acelerar filtros e ordenações.

### `execucoes`

É a tabela de observabilidade do pipeline. Além de servir para auditoria, ela permite que o painel mostre a última atualização.

### Trigger

O trigger `update_cme_events_updated_at` atualiza `updated_at` antes de cada alteração. Isso separa a hora da criação da hora da última atualização do registro.

### RLS e GRANTs

As duas tabelas ativam Row Level Security. `anon` e `authenticated` recebem SELECT. `service_role` recebe acesso completo para que o pipeline consiga inserir e atualizar. Os GRANTs explícitos complementam as policies.

Uma resposta boa para a banca é: “O frontend não precisa escrever no banco. Ele só lê. A escrita fica concentrada no pipeline, usando a chave de serviço guardada como secret.”

O endpoint REST do Supabase segue este formato:

```text
https://<projeto>.supabase.co/rest/v1/cme_events
https://<projeto>.supabase.co/rest/v1/execucoes
```

O PostgREST interpreta parâmetros de filtro, ordenação e limite. O painel usa, por exemplo, `start_time.gte`, `start_time.lte`, `order=start_time.desc` e `limit=1000`.

## 9. Workflow do GitHub Actions

O workflow possui três gatilhos:

- `schedule`: todos os dias às 06:00 UTC.
- `workflow_dispatch`: execução manual para testes e recuperação.
- `push` na branch `main`: atualiza após uma mudança publicada.

Ele usa Ubuntu, Python 3.11, instala `requests` e `supabase`, injeta os dois secrets do Supabase e executa `python scripts/fetch_nasa.py`.

As boas práticas pedidas pelo enunciado estão presentes: `permissions: contents: read`, `concurrency` com cancelamento de execução concorrente e `timeout-minutes: 10`.

O cron 06:00 UTC corresponde a 03:00 no horário de Brasília quando o fuso está em UTC-3. A apresentação deve mencionar UTC para não confundir o horário do Actions com o horário local.

## 10. Frontend em detalhe

1. Ao carregar, o script define a janela padrão dos últimos 30 dias.
2. `fetchCMEData()` valida datas, monta a URL PostgREST e envia `apikey` e `Authorization` com a chave anon.
3. `normalizeEvent()` converte nomes do banco para nomes usados pela interface.
4. `renderStats()` calcula total, velocidade média, velocidade máxima e quantidade marcada como Earth-directed.
5. `renderList()` ordena por data ou velocidade e cria os cards clicáveis.
6. `openModal()` mostra todos os metadados importantes e valida o link externo para aceitar apenas `http` e `https`.
7. `renderChart()` desenha diretamente no elemento Canvas. Ele mostra grade, área preenchida, linha, pontos e uma linha de referência em 1000 km/s quando aplicável.
8. `fetchLastUpdate()` consulta a execução mais recente e exibe `executed_at`.
9. `escapeHtml()` converte texto externo em conteúdo seguro antes de inseri-lo com `innerHTML`. Essa proteção é importante porque os dados vêm de uma fonte externa.

## 11. O que demonstrar ao vivo

Roteiro de demonstração:

1. Abrir o painel publicado e explicar que ele é um site estático.
2. Mostrar o filtro 30D e clicar em “Atualizar dados”.
3. Mostrar os quatro cards de estatística.
4. Alterar para 7D ou 90D e explicar que o frontend muda o intervalo enviado ao Supabase.
5. Ordenar a lista por velocidade.
6. Abrir um card e mostrar o modal com origem, velocidade, análise, instrumentos e link.
7. Voltar ao GitHub, abrir Actions e mostrar uma execução verde.
8. Abrir o Table Editor do Supabase e mostrar `cme_events` e `execucoes`.
9. Abrir o endpoint REST com a chave pública e mostrar o JSON.

Mantenha prints ou vídeo local do painel, do Actions e do Supabase. O próprio enunciado recomenda um backup porque a API, o GitHub Pages ou a rede podem falhar durante o seminário.

## 12. Perguntas prováveis da banca

### Por que usar `activity_id` como chave?

Porque é o identificador natural fornecido pela própria API para o evento. Ele é mais estável para deduplicação do que a posição do registro na resposta ou a hora da coleta.

### Por que usar upsert em vez de insert?

Porque a janela de 30 dias se repete a cada execução. O upsert permite atualizar eventos já conhecidos e inserir eventos novos sem criar duplicatas.

### O frontend está seguro com uma chave no JavaScript?

A chave usada no frontend é a anon, que é pública por definição. A chave service_role fica somente nos Secrets do GitHub. A segurança real vem da separação de credenciais, dos GRANTs e do RLS. A anon deve conseguir apenas leitura.

### O que acontece se a NASA estiver fora do ar?

O script tenta novamente até três vezes. Se a coleta continuar falhando, registra `erro_critico` e encerra com código 1. O Actions fica vermelho e o grupo consegue diagnosticar. O painel mantém os últimos dados que já estavam no banco. O código não deve transformar erro de API em lista vazia.

### Por que registrar `execucoes`?

Para observabilidade. A equipe consegue saber quando o pipeline rodou, quantos registros processou, quantos lotes usou e se houve erro. O painel também usa essa tabela para mostrar a última atualização.

### O que significa Earth-directed?

No projeto, significa que uma heurística encontrou latitude e longitude próximas do centro solar. É uma indicação para triagem, não uma previsão científica de que a CME atingirá a Terra.

### O painel consulta a NASA diretamente?

Não. O pipeline consulta a NASA/CCMC e grava no Supabase. O navegador consulta o Supabase. Essa separação reduz a exposição de credenciais e evita que cada visitante dispare chamadas para a API científica.

### Por que o gráfico usa Canvas?

O código atual desenha diretamente no Canvas, sem depender de uma biblioteca externa. Isso reduz dependências e mantém o gráfico sob controle do próprio frontend.

### O que melhoraria em uma próxima versão?

Testes automatizados para HTTP 503, timeout, JSON inválido e normalização; validação explícita de `activity_id`; backoff exponencial com jitter; consolidação do CSS duplicado; uma classificação Earth-directed baseada em fonte científica; e uma política de retenção ou histórico mais longo.

## 13. Divisão de fala para 20 minutos

### Bloco 1: API, 6 minutos

Explique o que é CME, o que é DONKI, quais campos são utilizados e por que os dados são relevantes para clima espacial. Comente a migração do endpoint antigo para o CCMC e a validação registrada.

### Bloco 2: arquitetura, 4 minutos

Siga o caminho NASA/CCMC → Actions → Python → Supabase → Pages. Explique a chave natural, as duas tabelas e a separação entre service key e anon key.

### Bloco 3: demonstração, 8 minutos

Mostre o painel, os filtros, o gráfico, a ordenação, o modal, o workflow, o banco e o JSON REST. Não tente explicar cada linha durante a demonstração; use o fluxo para provar que as partes se conectam.

### Bloco 4: uso de IA, 2 minutos

Diga onde a IA ajudou, mas também cite correções humanas reais: o diagnóstico do 503, a migração para CCMC, segurança das chaves, deduplicação, tratamento de falhas e revisão da documentação. O arquivo `docs/ai-interaction.md` contém registros. O arquivo `docs/reflexao.md` ainda precisa receber a reflexão real do grupo.

## 14. Checklist final de estudo

- Saber explicar cada seta da arquitetura.
- Saber dizer onde ficam as duas chaves e qual delas pode aparecer no navegador.
- Saber explicar `activity_id`, UNIQUE, deduplicação e upsert.
- Saber diferenciar `concluido`, `erro_parcial` e `erro_critico`.
- Saber explicar o que o RLS permite para `anon` e `service_role`.
- Saber mostrar que a última atualização vem de `execucoes`.
- Saber declarar que `Earth-directed` é heurística.
- Saber dizer que o gráfico é desenhado diretamente no Canvas.
- Levar evidências locais do painel, Actions e Supabase.
- Conferir os links e o projeto Supabase final antes de apresentar.

## Fontes usadas

- Enunciado da atividade: `Atividade Avaliativa 3 Trim.pdf`.
- Código do painel: `index.html`, `script.js`, `style.css`.
- Pipeline: `scripts/fetch_nasa.py`.
- Banco: `sql/setup.sql`.
- Automação: `.github/workflows/update-data.yml`.
- Documentação e diagnóstico: `README.md`, `docs/diagnostico_nasa_donki_cme_copilot.md`, `ORIENTACOES_GUILHERME_YASMIN.md` e `proximos_passos_CME_Watch.md`.
