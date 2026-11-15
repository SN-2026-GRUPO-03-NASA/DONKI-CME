# Roteiro de slides - CME Watch

## Objetivo da apresentação

Apresentar o CME Watch como um pipeline de dados científicos que coleta eventos
de Ejeção de Massa Coronal (CME), armazena os dados de forma segura e os mostra
em um painel web. O grupo deve demonstrar o fluxo real do sistema e deixar
claros seus limites científicos.

**Duração prevista:** 19 a 20 minutos.

## Regras para os slides

- Use pouco texto: título, uma ideia principal e apoio visual.
- Use prints reais do painel, GitHub Actions e Supabase. Não invente dados,
  números ou resultados.
- Não exiba `SUPABASE_SERVICE_KEY`, tokens, URLs privadas ou telas com secrets.
- Diga que `Earth-directed` é uma heurística de triagem, não uma confirmação de
  que a CME atingirá a Terra.
- Mostre o horário do workflow como `06:00 UTC`. Em Brasília, normalmente isso
  corresponde a 03:00, considerando UTC-3.

---

## Slide 1 - Capa

**Tempo:** 40 segundos
**Responsável sugerido:** Murilo

### Colocar no slide

- Título: `CME Watch`
- Subtítulo: `Monitor de Ejeções de Massa Coronal com dados da NASA/CCMC`
- Nomes do Grupo 03, curso, instituição e ano.
- Uma imagem do Sol ou uma captura bonita da tela inicial do painel.

### Fala sugerida

> Nós somos o Grupo 03 e desenvolvemos o CME Watch. Ele transforma dados sobre
> ejeções de massa coronal, publicados pela NASA/CCMC, em um painel atualizado
> automaticamente e mais fácil de consultar.

---

## Slide 2 - Problema

**Tempo:** 1 minuto e 20 segundos
**Responsável sugerido:** Guilherme

### Colocar no slide

- Título: `O problema`
- De um lado: resposta JSON técnica da API.
- Do outro: necessidade de filtros, histórico e visualização simples.
- Frase curta: `Dados científicos precisam ser coletados e interpretados com rapidez.`

### Fala sugerida

> A API disponibiliza informações científicas úteis, mas o retorno vem em JSON
> e exige conhecimento técnico para ser consultado. Se a coleta fosse manual,
> os dados poderiam ficar espalhados e desatualizados. O CME Watch automatiza
> esse processo e apresenta os registros em uma interface acessível.

---

## Slide 3 - O que é uma CME?

**Tempo:** 1 minuto e 30 segundos
**Responsável sugerido:** Yasmin

### Colocar no slide

- Título: `Ejeção de Massa Coronal`
- Ilustração do Sol com uma ejeção.
- Três efeitos possíveis: satélites, GPS e comunicação/radiocomunicação.
- Observação: CME e flare solar não são o mesmo fenômeno.

### Fala sugerida

> Uma CME é uma grande expulsão de plasma e campo magnético da coroa solar.
> Quando as condições são favoráveis, ela pode contribuir para tempestades
> geomagnéticas e afetar tecnologias como satélites, GPS, rádio e redes
> elétricas. O projeto acompanha os registros, mas não faz uma previsão física
> completa de impacto.

---

## Slide 4 - Objetivo e escopo

**Tempo:** 1 minuto
**Responsável sugerido:** Yasmin

### Colocar no slide

- Título: `O que o CME Watch entrega`
- Coletar eventos automaticamente.
- Guardar dados com histórico e sem duplicação.
- Filtrar, comparar e visualizar eventos no painel.
- Limite: `Não confirma impacto na Terra.`

### Fala sugerida

> Nosso objetivo é organizar e disponibilizar os registros de CME. O sistema
> não tenta substituir modelos científicos de propagação ou afirmar que uma CME
> atingirá a Terra. Ele oferece uma camada de consulta e visualização sobre
> dados publicados pela fonte científica.

---

## Slide 5 - Arquitetura

**Tempo:** 2 minutos
**Responsável sugerido:** Murilo

### Colocar no slide

```text
NASA/CCMC -> GitHub Actions -> Python -> Supabase -> GitHub Pages -> Navegador
```

- Uma seta por etapa.
- Para cada etapa, apenas uma frase curta.

### Fala sugerida

> A NASA/CCMC fornece os eventos em JSON. O GitHub Actions executa o pipeline
> automaticamente. O script Python trata os dados e os grava no Supabase. O
> GitHub Pages hospeda o frontend e o navegador consulta somente a API REST do
> Supabase para montar o painel.

> Essa separação é importante: o pipeline escreve no banco com uma credencial
> de serviço guardada como secret; o visitante apenas lê os dados públicos
> planejados pela aplicação.

---

## Slide 6 - Pipeline Python

**Tempo:** 2 minutos
**Responsável sugerido:** Guilherme

### Colocar no slide

```text
Buscar API -> Normalizar -> Deduplicar -> Upsert em lotes -> Registrar execução
```

- Mostrar `scripts/fetch_nasa.py` apenas com trechos curtos ou pseudocódigo.
- Mostrar que a coleta usa timeout e até três tentativas.

### Fala sugerida

> O pipeline busca os últimos 30 dias no endpoint atual do CCMC. Ele escolhe a
> análise marcada como mais precisa, quando disponível, e converte o JSON da
> NASA para o formato controlado pela nossa tabela. Se a API falhar, o script
> tenta novamente até três vezes e registra a falha em vez de tratá-la como se
> não houvesse eventos.

---

## Slide 7 - Deduplicação e upsert

**Tempo:** 2 minutos
**Responsável sugerido:** Yasmin

### Colocar no slide

| Conceito | Aplicação no projeto |
|---|---|
| `activity_id` | Identificador natural fornecido pela NASA |
| `UNIQUE` | Impede duas linhas para o mesmo evento |
| Deduplicação | Remove repetidos dentro da resposta atual |
| Upsert | Insere evento novo ou atualiza um já existente |

### Fala sugerida

> Como a busca diária cobre novamente os últimos 30 dias, um mesmo evento pode
> reaparecer em execuções diferentes. A deduplicação remove repetições no lote
> recebido agora. A regra `UNIQUE` protege o banco. O upsert usa `activity_id`
> como chave de conflito para inserir uma CME nova ou atualizar uma análise que
> a NASA revisou.

---

## Slide 8 - Banco de dados e segurança

**Tempo:** 2 minutos
**Responsável sugerido:** Murilo

### Colocar no slide

- Tabela `cme_events`: eventos normalizados.
- Tabela `execucoes`: data, status, lotes e erros do pipeline.
- Um diagrama simples de permissões:

```text
Navegador + anon key        -> SELECT
GitHub Actions + service_role -> escrita do pipeline
```

### Fala sugerida

> `cme_events` guarda as CMEs e `execucoes` registra a saúde do pipeline. O
> painel usa a chave pública `anon`, que tem apenas permissão de leitura. A
> `service_role` fica somente nos Secrets do GitHub Actions, porque possui
> privilégios de escrita. A segurança real vem das policies de RLS e dos grants
> definidos no PostgreSQL.

---

## Slide 9 - GitHub Actions e observabilidade

**Tempo:** 1 minuto e 30 segundos
**Responsável sugerido:** Guilherme

### Colocar no slide

- `[INSERIR PRINT REAL DO WORKFLOW VERDE]`
- Gatilhos: agendamento diário, execução manual e `push` na `main`.
- Status: `concluido`, `erro_parcial` e `erro_critico`.

### Fala sugerida

> O GitHub Actions executa o pipeline diariamente às 06:00 UTC. Também pode ser
> acionado manualmente para teste ou recuperação. A tabela `execucoes` registra
> quantos eventos foram processados, quantos lotes foram enviados e se ocorreu
> sucesso, erro parcial ou erro crítico. Isso permite investigar problemas sem
> depender apenas do painel.

---

## Slide 10 - Demonstração do painel

**Tempo:** 3 minutos
**Responsáveis sugeridos:** todos

### Demonstrar ao vivo

1. Abrir o painel publicado.
2. Alterar o filtro para 7D ou 90D e clicar em `Atualizar dados`.
3. Mostrar total de eventos, média, máximo e Earth-directed.
4. Ordenar por velocidade.
5. Abrir um evento e mostrar o modal com origem, instrumentos e link da fonte.
6. Mostrar a data de última atualização do pipeline.

### Fala sugerida

> O painel consulta o Supabase com filtros de data. A mesma lista de eventos,
> chamada `currentEvents`, alimenta as estatísticas, a lista, o gráfico e o
> modal. Assim, todos os elementos da tela representam o mesmo período
> selecionado.

> O gráfico usa Canvas e destaca visualmente eventos rápidos a partir de
> 1000 km/s. Esse valor serve como referência visual da interface e não como
> confirmação de que haverá impacto na Terra.

---

## Slide 11 - Migração, limitações e melhorias

**Tempo:** 1 minuto e 40 segundos
**Responsável sugerido:** Yasmin

### Colocar no slide

- Endpoint antigo apresentou HTTP 503 no período investigado.
- Endpoint atual: CCMC, compatível com os campos necessários e sem `api_key`.
- Limitações e melhorias:
  - heurística Earth-directed;
  - testes para timeout, HTTP 503 e JSON inválido;
  - validação explícita de `activity_id`;
  - backoff exponencial com jitter;
  - teste do gráfico com velocidades zero.

### Fala sugerida

> O projeto precisou se adaptar a uma mudança externa: o endpoint antigo passou
> a falhar com HTTP 503. O grupo investigou o problema, validou o endpoint do
> CCMC e conferiu que os campos necessários continuavam disponíveis. Entre as
> melhorias futuras estão mais testes automatizados e uma classificação
> Earth-directed apoiada por uma fonte científica mais completa.

---

## Slide 12 - Conclusão

**Tempo:** 1 minuto e 20 segundos
**Responsável sugerido:** Murilo

### Colocar no slide

- Pipeline automatizado.
- Dados persistidos e protegidos.
- Painel público e consultável.
- Observabilidade por meio de `execucoes`.
- QR Code ou link para o painel publicado, se disponível.

### Fala sugerida

> O CME Watch conecta uma fonte científica, uma automação diária, um banco com
> regras de segurança e uma interface pública. O resultado é um fluxo
> reproduzível para consultar CMEs com filtros, histórico e transparência sobre
> a última atualização. Obrigado. Estamos prontos para as perguntas.

---

## Perguntas que provavelmente aparecerão após a apresentação

1. Por que usar upsert em vez de insert?
2. Qual a diferença entre deduplicação, `UNIQUE` e upsert?
3. Por que a chave `anon` pode aparecer no JavaScript?
4. Onde está a `service_role` e por que ela não pode ir para o frontend?
5. O que acontece se a NASA/CCMC estiver indisponível?
6. O que significa `erro_parcial`?
7. Por que `Earth-directed` não é previsão científica?
8. Para que serve a tabela `execucoes`?
9. Por que o navegador não consulta a NASA diretamente?
10. Quais são as limitações e melhorias futuras do sistema?

## Frase de defesa em 30 segundos

> O CME Watch transforma dados de CME da NASA/CCMC em um fluxo automatizado e
> consultável. O GitHub Actions executa o Python diariamente, o pipeline
> normaliza, deduplica e atualiza os eventos no Supabase, e o painel estático
> apresenta filtros, estatísticas, gráfico, detalhes e a saúde das execuções.
> O sistema deixa claro que Earth-directed é uma heurística de triagem, não uma
> previsão científica de impacto.
