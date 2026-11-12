# Próximos passos --- CME Watch / DONKI-CME

Este documento organiza o que falta para finalizar o projeto. Marque
cada tarefa conforme for concluída e valide os resultados no ambiente
real --- o código estar preparado não garante que o deploy, o GitHub
Actions ou o Supabase já estejam funcionando.

## 1. Prioridade alta --- colocar o fluxo completo para funcionar

### 1.1 Conferir as configurações do GitHub

-   [ ] Confirmar que o repositório correto está na organização/grupo
    esperado.
-   [ ] Confirmar que o professor tem acesso ao repositório, conforme as
    orientações do trabalho.
-   [ ] Abrir **Settings → Secrets and variables → Actions** e verificar
    se existem os secrets:
    -   `SUPABASE_URL`
    -   `SUPABASE_SERVICE_KEY`
    -   `NASA_API_KEY`
-   [ ] Confirmar que `SUPABASE_SERVICE_KEY` é a chave de serviço do
    projeto Supabase e não foi colocada no código do front-end.
-   [ ] Conferir se o workflow `.github/workflows/update-data.yml` está
    habilitado.

### 1.2 Executar e validar a coleta de dados

-   [ ] Abrir **Actions** no GitHub.
-   [ ] Executar manualmente o workflow de atualização de dados.
-   [ ] Abrir a execução e conferir se todas as etapas terminaram sem
    erro.
-   [ ] Se falhar, ler o log, corrigir o problema e executar novamente.
-   [ ] No Supabase, conferir se a tabela `cme_events` recebeu
    registros.
-   [ ] Conferir se a tabela `execucoes` recebeu um registro da execução
    com status `concluido`.
-   [ ] Confirmar que a execução agendada também está configurada. O
    cron atual está definido para `06:00 UTC` diariamente; converter
    para o horário local ao documentar o agendamento.

**Critério de conclusão:** o workflow termina com sucesso e os dados e o
registro de execução aparecem no Supabase.

### 1.3 Publicar e testar o dashboard

-   [ ] Ativar ou conferir o GitHub Pages para o repositório.
-   [ ] Substituir no `README.md` a URL de demonstração provisória pela
    URL publicada.
-   [ ] Abrir o dashboard publicado em uma janela anônima ou em outro
    navegador.
-   [ ] Confirmar que os eventos são carregados do Supabase.
-   [ ] Testar os filtros de período: 7, 30, 90 e 365 dias.
-   [ ] Testar ordenação, estatísticas, gráfico, detalhes do evento e
    links externos.
-   [ ] Testar os estados de carregamento, lista vazia e erro de
    conexão, se disponíveis.
-   [ ] Conferir o console do navegador para erros.

**Critério de conclusão:** o dashboard público abre e suas funções
principais funcionam com dados reais.

## 2. Prioridade alta --- verificar banco, segurança e API

-   [ ] Conferir se as tabelas `cme_events` e `execucoes` existem no
    projeto Supabase correto.
-   [ ] Conferir se as políticas de RLS permitem apenas as operações
    previstas.
-   [ ] Testar a leitura pública pela chave `anon` e confirmar que ela
    só permite os dados que devem ser públicos.
-   [ ] Nunca colocar a chave `service_role` no `index.html`,
    `script.js`, README ou em outro arquivo público.
-   [ ] Testar o endpoint REST do Supabase para `cme_events` e confirmar
    que retorna JSON.
-   [ ] Registrar no material de entrega a URL do endpoint utilizado,
    sem incluir chaves secretas ou tokens.
-   [ ] Confirmar que a tabela `execucoes` mostra a última atualização e
    que a interface exibe essa informação corretamente.

**Critério de conclusão:** leitura pública funciona conforme planejado,
as permissões estão revisadas e nenhuma chave privilegiada está exposta.

## 3. Corrigir inconsistências e fazer uma revisão técnica

-   [x] Revisar o `README.md`: o gráfico é desenhado com Canvas e a
    documentação não depende de uma biblioteca externa de gráficos.
-   [ ] Substituir todos os links e textos provisórios, como
    `SEU-USUARIO`, `SEU_PROJETO` e URLs de exemplo.
-   [ ] Adicionar capturas de tela reais do dashboard, da execução
    bem-sucedida do workflow e dos dados no Supabase.
-   [ ] Conferir se a heurística que classifica eventos como
    direcionados à Terra está documentada como estimativa, não como
    confirmação científica.
-   [ ] Revisar o tratamento de falhas e confirmar que eventos sem
    `activity_id` válido não sejam enviados para o banco.
-   [ ] Verificar o comportamento do gráfico quando não há eventos ou
    quando todos os valores de velocidade são zero.
-   [ ] Fazer uma última revisão do console do navegador e dos logs do
    GitHub Actions.

## 4. Completar a documentação do projeto

### 4.1 Registro de uso de IA

Arquivo: `docs/ai-interaction.md`

-   [ ] Preencher o modelo com interações reais de IA utilizadas durante
    o projeto.
-   [ ] Registrar ferramenta, objetivo, resumo do pedido, resultado
    aproveitado e revisão humana.
-   [ ] Não inventar interações; documentar somente o que realmente
    aconteceu.

### 4.2 Reflexão individual ou do grupo

Arquivo: `docs/reflexao.md`

-   [ ] Preencher a reflexão com aprendizados reais.
-   [ ] Descrever dificuldades encontradas e como foram resolvidas.
-   [ ] Explicar limitações conhecidas e o que seria melhorado numa
    próxima versão.
-   [ ] Conferir se o formato atende às instruções do professor.

### 4.3 Tutorial em PDF

-   [ ] Criar o tutorial solicitado, caso seja exigido pela atividade.
-   [ ] Explicar como acessar o dashboard, filtrar eventos, abrir
    detalhes e interpretar as informações.
-   [ ] Incluir capturas de tela legíveis.
-   [ ] Conferir se o arquivo atende ao número de páginas e demais
    requisitos da atividade.

### 4.4 Apresentação em PDF

-   [ ] Criar a apresentação solicitada, caso seja exigida.
-   [ ] Apresentar problema, objetivo, arquitetura, tecnologias,
    funcionamento, resultados e limitações.
-   [ ] Incluir imagens reais do sistema.
-   [ ] Revisar ortografia, legibilidade e links.

## 5. Preparar a entrega

-   [ ] Confirmar que o README tem os links finais para repositório e
    dashboard publicado.
-   [ ] Confirmar que os documentos exigidos estão no repositório ou na
    pasta de entrega correta.
-   [ ] Conferir se o professor tem acesso aos links necessários.
-   [ ] Preparar um PDF ou documento final com os links solicitados pela
    atividade.
-   [ ] Incluir evidência de uma execução bem-sucedida do GitHub
    Actions.
-   [ ] Incluir evidência dos dados no Supabase e do endpoint REST
    retornando JSON, se solicitado.
-   [ ] Conferir a planilha de acompanhamento/reservas de atualização
    mencionada nas instruções da atividade, se aplicável.
-   [ ] Abrir cada link em uma janela anônima para verificar acesso.
-   [ ] Fazer uma última revisão dos requisitos oficiais da atividade
    antes de enviar.

## Ordem recomendada

1.  Executar o workflow e confirmar os dados no Supabase.
2.  Publicar o dashboard e testar suas funções.
3.  Conferir permissões, RLS, secrets e endpoint REST.
4.  Corrigir o README e adicionar capturas de tela.
5.  Completar os documentos e PDFs exigidos.
6.  Montar a entrega final e conferir todos os links.

## Observações

-   Esta lista é baseada na estrutura do projeto analisada. Requisitos
    específicos, como número de páginas, formato dos PDFs e conteúdo
    obrigatório da entrega, devem ser conferidos nas instruções oficiais
    do professor.
-   Não marque uma tarefa como concluída apenas porque existe código
    para ela: confirme o funcionamento no GitHub, no Supabase ou no
    dashboard publicado.
