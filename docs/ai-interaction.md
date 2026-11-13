# AI Interaction Log

## Propósito

Este documento registra as interações com assistentes de IA durante o desenvolvimento do projeto DONKI-CME.

## Instruções

- Registre cada interação relevante com objetivo e resultado obtido
- Não inclua secrets, chaves de API ou informações sensíveis
- Descreva o contexto e as decisões tomadas

---

## Modelo de Registro

### [Título da interação]

- **Objetivo:** O que se pretendia alcançar
- **Contexto:** Situação do projeto na época
- **Ação:** O que foi solicitado à IA
- **Resultado:** O que foi obtido
- **Decisões:** Escolhas técnicas ou de design feitas
- **Pendências:** O que ficou para resolver

---

## Registros

<!-- Adicione suas interações abaixo seguindo o modelo acima -->

## Registros históricos

Os registros abaixo foram consolidados a partir do histórico de interações usado
durante o desenvolvimento. Os prompts são mantidos como foram formulados; quando
a implementação final tomou outro caminho, a decisão aparece em **Decisões**.

### Planejamento inicial do pipeline

- **Objetivo:** Organizar as etapas e tecnologias do projeto.
- **Contexto:** O grupo precisava coletar dados da NASA, armazená-los e apresentá-los em um dashboard.
- **Ação:** “Estamos desenvolvendo um projeto utilizando uma API da NASA sobre ejeções de massa coronal. Precisamos coletar os dados, armazená-los em um banco de dados e apresentá-los em um dashboard. Quais etapas devemos seguir?”
- **Resultado:** A IA apresentou as principais etapas, da coleta à apresentação.
- **Decisões:** O grupo usou a resposta para organizar as tarefas iniciais.
- **Pendências:** Detalhar a API, o banco, a automação e o painel.

### Conhecimento da API DONKI/CME

- **Objetivo:** Compreender o funcionamento da API escolhida.
- **Contexto:** O grupo precisava relacionar o endpoint CME com o objetivo do projeto.
- **Ação:** “Explique como funciona a API DONKI da NASA, especificamente o endpoint de Coronal Mass Ejection (CME). Quais informações são retornadas?”
- **Resultado:** A IA explicou a finalidade da API e os principais dados disponibilizados.
- **Decisões:** A explicação foi usada para selecionar informações relevantes para o painel.
- **Pendências:** Confirmar os campos na resposta real da API.

### Parâmetros e requisições

- **Objetivo:** Entender como consultar os dados.
- **Contexto:** Era necessário consultar registros de um período e evitar problemas em várias requisições.
- **Ação:** Foram enviados os prompts: “Quais são os parâmetros disponíveis no endpoint DONKI/CME e como consultar registros de um determinado período?” e “Quais cuidados devemos ter ao realizar várias requisições à API e quais são os limites de utilização?”
- **Resultado:** A IA explicou os parâmetros, a chave de acesso e os limites de requisições.
- **Decisões:** O grupo passou a considerar período, autenticação e limites ao planejar o coletor.
- **Pendências:** Validar o comportamento com a resposta real do endpoint.

### Estrutura JSON da API

- **Objetivo:** Entender a estrutura das respostas.
- **Contexto:** O processamento dependeria da leitura correta dos registros JSON.
- **Ação:** “A resposta da API DONKI/CME é retornada em formato JSON. Explique como interpretar essa estrutura e identificar os principais campos.”
- **Resultado:** A IA explicou a organização dos registros e a identificação dos campos.
- **Decisões:** A resposta serviu como orientação inicial para a normalização.
- **Pendências:** Conferir campos opcionais e variações no retorno real.

### Seleção de campos

- **Objetivo:** Identificar os dados relevantes para o projeto.
- **Contexto:** Nem todos os campos retornados seriam necessários no painel.
- **Ação:** “Quais campos retornados pela API DONKI/CME são mais relevantes para apresentar informações sobre ejeções de massa coronal? Como lidar com campos vazios?”
- **Resultado:** A IA sugeriu campos relevantes e explicou como tratar informações opcionais.
- **Decisões:** Os campos foram escolhidos de acordo com a necessidade do grupo, e não apenas pela sugestão da IA.
- **Pendências:** Confirmar a compatibilidade dos campos escolhidos com a tabela.

### Estrutura do banco Supabase

- **Objetivo:** Planejar o armazenamento dos dados.
- **Contexto:** O Supabase seria usado para armazenar os registros da API.
- **Ação:** “Estamos utilizando o Supabase para armazenar os dados da API DONKI/CME. Quais cuidados devemos ter ao definir os campos e os tipos de dados da tabela?”
- **Resultado:** A IA explicou a importância dos tipos compatíveis e da organização da tabela.
- **Decisões:** A estrutura final foi adaptada ao contrato real da API.
- **Pendências:** Definir a chave natural, as constraints, o RLS e os GRANTs.

### Compatibilidade e campos vazios

- **Objetivo:** Entender como lidar com registros diferentes.
- **Contexto:** Alguns eventos poderiam ter campos vazios ou tipos diferentes.
- **Ação:** “Alguns registros da API podem apresentar campos vazios ou informações diferentes. Como devemos considerar essas situações no Supabase?”
- **Resultado:** A IA destacou os riscos de valores ausentes e tipos incompatíveis.
- **Decisões:** Os dados passaram a ser verificados antes da inserção.
- **Pendências:** Testar casos sem análise, velocidade ou coordenadas.

### Integração Python, API e Supabase

- **Objetivo:** Compreender a comunicação entre o script, a API e o banco.
- **Contexto:** O grupo precisava transformar a resposta da API em registros persistidos.
- **Ação:** Foram enviados os prompts: “Explique como funciona a comunicação entre Python, a API DONKI/CME e o Supabase.” e “Quais cuidados devemos ter ao enviar os dados recebidos pela API para uma tabela que já possui uma estrutura definida?”
- **Resultado:** A IA explicou consulta, processamento e envio em lotes.
- **Decisões:** As orientações foram ajustadas à estrutura específica da tabela.
- **Pendências:** Validar deduplicação, upsert e registro das execuções.

### Proteção de credenciais

- **Objetivo:** Evitar a exposição das chaves utilizadas.
- **Contexto:** O projeto usava credenciais da NASA e do Supabase.
- **Ação:** “Quais são os riscos de deixar as credenciais da NASA e do Supabase diretamente nos arquivos do repositório? Como podemos protegê-las?”
- **Resultado:** A IA explicou os riscos e recomendou variáveis de ambiente e armazenamento seguro.
- **Decisões:** Credenciais privilegiadas ficaram fora do código público.
- **Pendências:** Validar secrets, logs e configuração do frontend.

### GitHub Secrets

- **Objetivo:** Disponibilizar credenciais nas automações sem expô-las.
- **Contexto:** O GitHub Actions precisaria acessar o Supabase durante o workflow.
- **Ação:** “Explique como funcionam os GitHub Secrets e como utilizá-los para disponibilizar as credenciais da NASA e do Supabase durante a execução do GitHub Actions.”
- **Resultado:** A IA explicou como os Secrets funcionam.
- **Decisões:** A chave `service_role` foi reservada ao pipeline; o frontend usa somente a chave pública.
- **Pendências:** Conferir os nomes e valores dos secrets no repositório final.

### GitHub Actions

- **Objetivo:** Compreender a execução automática do projeto.
- **Contexto:** O pipeline deveria ser executado pelo GitHub Actions.
- **Ação:** “Explique como funciona um workflow do GitHub Actions e quais elementos são necessários para automatizar a execução de um script Python.”
- **Resultado:** A IA apresentou a estrutura dos workflows e os elementos de automação.
- **Decisões:** O workflow foi configurado para execução agendada e manual.
- **Pendências:** Validar `permissions`, `concurrency`, timeout e execução real.

### Agendamento das execuções

- **Objetivo:** Configurar a atualização diária dos dados.
- **Contexto:** O cron do GitHub Actions usa UTC, enquanto o grupo trabalha com o horário de Brasília.
- **Ação:** Foram enviados os prompts: “Como funciona o agendamento por cron no GitHub Actions para executar uma tarefa diariamente?” e “O GitHub Actions utiliza o horário UTC. Como devemos considerar a diferença em relação ao horário de Brasília?”
- **Resultado:** A IA explicou o cron e a conversão de fusos.
- **Decisões:** O agendamento foi documentado em UTC, com a conversão para o horário local.
- **Pendências:** Confirmar o horário observado em uma execução real.

### Tratamento de erros

- **Objetivo:** Identificar possíveis falhas durante as requisições.
- **Contexto:** O pipeline precisava diferenciar falhas de rede, JSON e processamento.
- **Ação:** “Quais problemas podem ocorrer durante uma requisição à API DONKI/CME e como identificar se a falha aconteceu na comunicação, na leitura do JSON ou no processamento dos dados?”
- **Resultado:** A IA apresentou causas possíveis e formas de diferenciar as etapas.
- **Decisões:** O script passou a tratar etapas separadamente e registrar o resultado da execução.
- **Pendências:** Exercitar respostas HTTP 503, timeout e JSON inválido.

### Respostas inválidas

- **Objetivo:** Lidar com respostas inesperadas da API.
- **Contexto:** Uma resposta vazia ou fora do formato esperado não deveria derrubar o processamento sem diagnóstico.
- **Ação:** “Quando uma API retorna uma resposta vazia ou diferente do formato JSON esperado, quais verificações devem ser feitas antes de processar o conteúdo?”
- **Resultado:** A IA recomendou verificar a resposta antes de interpretar os dados.
- **Decisões:** O pipeline passou a validar status e formato antes da normalização.
- **Pendências:** Registrar evidências de falhas reais nos logs.

### Organização do dashboard

- **Objetivo:** Planejar a apresentação visual dos dados.
- **Contexto:** O projeto teria um dashboard sobre ejeções de massa coronal.
- **Ação:** “Nosso projeto terá um dashboard sobre ejeções de massa coronal. Quais informações seriam interessantes de destacar e como podemos organizá-las visualmente?”
- **Resultado:** A IA sugeriu indicadores, gráficos e registros.
- **Decisões:** O painel foi organizado com indicadores, filtros, gráfico, tabela e detalhes do evento.
- **Pendências:** Validar legibilidade e funcionamento com dados reais.

### Indicadores e filtros

- **Objetivo:** Facilitar a consulta dos dados.
- **Contexto:** O painel precisava permitir análise por período.
- **Ação:** Foram enviados os prompts: “Quais indicadores poderiam ser apresentados no dashboard para facilitar a análise das ejeções de massa coronal?” e “Quais cuidados devemos ter ao utilizar filtros de datas para consultar os registros?”
- **Resultado:** A IA sugeriu indicadores e explicou o uso de filtros de período.
- **Decisões:** Foram adotados filtros de 7, 30, 90 e 365 dias, além de estatísticas e ordenação.
- **Pendências:** Testar lista vazia, erro de conexão e valores sem velocidade.

### Planejamento inicial de Chart.js e decisão por Canvas

- **Objetivo:** Explorar uma representação gráfica para os dados.
- **Contexto:** Murilo planejou inicialmente usar Chart.js para os gráficos.
- **Ação:** “Adicione gráficos que representam os dados utilizando chart.js”.
- **Resultado:** A IA apresentou orientações sobre a utilização do Chart.js.
- **Decisões:** O grupo decidiu não usar Chart.js na implementação final. O gráfico foi desenhado diretamente com Canvas, reduzindo dependências externas e mantendo o controle do frontend.
- **Pendências:** Verificar o comportamento do Canvas quando não há dados ou quando as velocidades são zero.

### Escolha dos gráficos

- **Objetivo:** Identificar representações adequadas aos dados disponíveis.
- **Contexto:** A decisão visual precisava considerar os campos realmente retornados pela API.
- **Ação:** “Quais tipos de gráficos fazem mais sentido para representar os dados de ejeções de massa coronal? Explique quais informações podem ser utilizadas em cada representação.”
- **Resultado:** A IA sugeriu gráficos para comparação e interpretação.
- **Decisões:** As sugestões foram adaptadas ao Canvas e aos dados efetivamente disponíveis; Chart.js não foi adotado.
- **Pendências:** Refinar a classificação Earth-directed com uma fonte científica mais robusta em uma versão futura.

### Eventos direcionados à Terra

- **Objetivo:** Compreender como identificar possíveis eventos direcionados à Terra.
- **Contexto:** O painel precisava mostrar uma indicação sem transformar uma heurística em confirmação científica.
- **Ação:** Foram enviados os prompts: “Como as informações da API DONKI/CME podem ser utilizadas para identificar possíveis ejeções direcionadas à Terra?” e “Quais limitações devemos considerar nessa classificação para não apresentar uma estimativa como confirmação científica?”
- **Resultado:** A IA explicou que os dados podem auxiliar uma estimativa, mas possuem limitações.
- **Decisões:** A classificação foi apresentada como heurística, não como previsão científica.
- **Pendências:** Avaliar uma fonte científica específica para melhorar a regra.

### README e documentação

- **Objetivo:** Organizar a documentação do repositório.
- **Contexto:** O README precisava explicar estrutura, colaboradores, descrição e tecnologias.
- **Ação:** Foram enviados os prompts: “Crie um readme com estrutura, colaboradores, descrição e tecnologias usadas” e “Revise a organização do README considerando o objetivo do projeto e as tecnologias utilizadas. Verifique se as informações estão claras.”
- **Resultado:** A IA auxiliou na estruturação das seções do README.
- **Decisões:** A documentação foi revisada para refletir o uso de Canvas e o endpoint CCMC.
- **Pendências:** Manter links e evidências atualizados.

### Commits e análise inicial de erros

- **Objetivo:** Organizar alterações e investigar uma falha na automação.
- **Contexto:** O grupo precisava padronizar mensagens de commit e entender uma falha no GitHub Actions.
- **Ação:** Foram enviados os prompts: “Faça o commit e o push das alterações do projeto seguindo as normas de mensagens de commit em inglês.” e “Por que a execução do GitHub Actions está falhando? Analise os registros da execução, identifique a causa do problema e explique como corrigi-lo. NÃO FAÇA NENHUMA ALTERAÇÃO.”
- **Resultado:** A IA auxiliou no commit e apontou uma possível falha na interpretação de JSON, mas não confirmou a causa exata apenas com os registros disponíveis.
- **Decisões:** A análise foi usada para localizar a etapa do problema, sem permitir alteração automática dos arquivos.
- **Pendências:** Fazer diagnóstico com evidências de execução, endpoint e configuração.

## Interações de manutenção registradas posteriormente

### Diagnóstico e validação da chave da API da NASA

- **Objetivo:** Corrigir as falhas do workflow de atualização de dados.
- **Contexto:** O pipeline passou a usar um timeout de 80 segundos, mas as execuções
  continuaram falhando. Os logs indicaram respostas `503 Service Unavailable` da API
  DONKI e, posteriormente, `API_KEY_INVALID`.
- **Ação:** Prompt revisado: “Investigue por que o workflow continua falhando,
  confirme se o timeout de 80 segundos está no commit e verifique a configuração da
  chave da NASA nos secrets do GitHub. Adicione uma validação segura ao workflow,
  documente o procedimento e prepare o commit.”
- **Resultado:** Confirmado que o script lê `NASA_API_KEY` do ambiente e que o valor
  padrão `DEMO_KEY` só é usado quando o secret está ausente ou vazio. Foi adicionada
  uma etapa no workflow para interromper a execução quando o secret não estiver
  configurado, exibindo somente a quantidade de caracteres.
- **Decisões:** A chave não será armazenada no código, na documentação ou nos logs.
  O secret deve conter apenas o valor da chave, sem aspas, prefixos ou espaços
  adicionais.
- **Pendências:** Executar novamente o workflow após confirmar que a chave está ativa
  no portal da NASA. Se ainda houver erro, consultar o status da API e os logs da
  execução.

### Padronização das mensagens de commit

- **Objetivo:** Definir uma regra única para as mensagens de commit do projeto.
- **Contexto:** Os commits futuros precisam ser escritos em inglês e seguir uma
  convenção reconhecida pela equipe.
- **Ação:** Prompt revisado: “Defina uma regra para que todas as mensagens de
  commit sejam escritas exclusivamente em inglês e sigam o padrão Conventional
  Commits. Documente a regra nas instruções do projeto e no README, e registre
  este prompt revisado no log de interações.”
- **Resultado:** A regra foi adicionada às instruções do Copilot e ao README.
  Também foram documentados os tipos permitidos, o uso do modo imperativo, a
  descrição concisa e exemplos válidos.
- **Decisões:** A regra se aplica aos novos commits. Commits existentes não serão
  reescritos para preservar o histórico do repositório.
- **Pendências:** Aplicar a convenção em todos os commits futuros.

### Investigação da indisponibilidade do endpoint DONKI/CME

- **Objetivo:** Determinar se as falhas persistentes do workflow eram causadas
  pelo código, pela chave da API, pelo timeout ou pelo serviço da NASA.
- **Contexto:** O workflow falhava, apesar de uma execução anterior ter retornado
  119 registros.
- **Ação:** Prompt revisado: “Investigue a fundo as falhas do workflow no período
  analisado. Compare as execuções bem-sucedidas e malsucedidas, teste o endpoint
  DONKI/CME e outro endpoint da NASA sem expor secrets, documente as evidências e
  prepare um commit sem fazer push.”
- **Resultado:** A primeira falha observada retornou HTTP `503`.
  As execuções seguintes falharam no mesmo endpoint, inclusive após o timeout
  ser aumentado para 80 segundos. Um teste direto com `DEMO_KEY` também retornou
  `503`, enquanto o endpoint APOD retornou `200`. O Supabase permaneceu acessível
  e registrou as execuções.
- **Decisões:** O diagnóstico aponta para uma indisponibilidade persistente do
  serviço DONKI/CME, não para um erro no código, no timeout ou exclusivamente na
  chave do repositório. O retorno `API_KEY_INVALID` foi tratado como um problema
  separado de configuração da credencial.
- **Pendências:** Monitorar a recuperação do endpoint DONKI/CME. Considerar, em
  uma alteração futura, um fallback que preserve os últimos dados válidos durante
  indisponibilidades da NASA.

### Pesquisa externa sobre a instabilidade do DONKI/CME

- **Objetivo:** Solicitar uma investigação na internet sobre a instabilidade
  persistente do endpoint NASA DONKI/CME.
- **Contexto:** O workflow falha com HTTP `503` no período analisado,
  embora tenha funcionado anteriormente. O relatório retornado pela pesquisa
  foi salvo em
  [diagnostico_nasa_donki_cme_copilot.md](diagnostico_nasa_donki_cme_copilot.md).
- **Ação:** Prompt revisado: “Pesquise na internet se a API NASA DONKI/CME está
  instável ou indisponível. Compare o endpoint antigo `api.nasa.gov/DONKI/CME`
  com a documentação oficial atual, priorize fontes confiáveis e procure relatos
  recentes. Verifique migrações, mudanças de URL, autenticação e
  alternativas oficiais. Diferencie fatos confirmados, evidências indiretas e
  hipóteses, informando URLs, trechos relevantes e nível de confiança.
  Apresente um resumo, uma linha do tempo, as causas prováveis, testes adicionais
  e soluções temporária e definitiva.”
- **Resultado:** A resposta identificou uma possível migração oficial do DONKI
  para a infraestrutura CCMC, com novas URLs documentadas, e recomendou validar
  o endpoint `https://ccmc.gsfc.nasa.gov/DONKI-API/get/CME` antes de alterar o
  código. O documento também inclui testes comparativos, critérios de aceite e
  orientações de segurança.
- **Decisões:** O relatório foi armazenado como documentação de diagnóstico, sem
  assumir que a migração resolveu o problema. Qualquer alteração no pipeline deve
  ser feita somente após confirmar a resposta real da nova API e a compatibilidade
  do schema.
- **Pendências:** Validar a nova URL CCMC com o mesmo período de consulta, comparar
  respostas e decidir se a URL da API deve ser migrada.

### Migração do endpoint DONKI para o CCMC

- **Objetivo:** Corrigir a falha persistente do endpoint antigo e migrar o
  pipeline para a API DONKI oficial do CCMC.
- **Contexto:** A documentação oficial do CCMC indicou a nova base
  `https://ccmc.gsfc.nasa.gov/DONKI-API/get/`. O endpoint CME antigo em
  `api.nasa.gov` continuava retornando HTTP `503`.
- **Ação:** Prompt revisado: “Leia a documentação da API DONKI no CCMC, valide o
  endpoint CME, compare o schema com o pipeline atual e faça a migração,
  atualizando o workflow e a documentação necessários.”
- **Resultado:** O endpoint CCMC retornou HTTP `200` e 90 registros para a janela
  de 30 dias testada. Os campos consumidos pelo normalizador permaneceram
  compatíveis. O pipeline passou a usar a URL CCMC e deixou de enviar `api_key`,
  pois a consulta validada não exige autenticação.
- **Decisões:** O secret `NASA_API_KEY` não é mais necessário para este workflow,
  embora possa permanecer no repositório para usos futuros. A validação final
  depende de uma execução do GitHub Actions e da confirmação de gravação no
  Supabase.
- **Pendências:** Nenhuma. A coleta, o upsert e o registro da execução foram
  confirmados com sucesso no GitHub Actions.

### Confirmação da migração em produção

- **Objetivo:** Confirmar se a migração para o endpoint CCMC resolveu o problema.
- **Contexto:** O código migrado já havia retornado 90 registros em um teste local.
- **Ação:** Prompt revisado: “Verifique a execução mais recente do workflow,
  confirme se a migração funcionou e atualize os arquivos Markdown necessários.
  Registre a sequência de prompts, pesquisas e resultados de forma organizada,
  corrigindo erros de português sem deixar o texto excessivamente formal.”
- **Resultado:** A execução `41649309627` foi concluída com sucesso. O pipeline
  consultou o CCMC, retornou 90 registros, normalizou os dados, gravou no
  Supabase e registrou o status `concluido`.
- **Decisões:** O incidente foi considerado resolvido após a confirmação em
  produção. A documentação mantém a linha do tempo, as evidências, os prompts
  revisados e o resultado final.
- **Pendências:** Continuar monitorando as execuções diárias do workflow.

### Uso assistido do Git nos commits

- **Objetivo:** Registrar como Murilo realizou os commits e por que utilizou a IA como apoio.
- **Contexto:** Todos os commits feitos pelo Murilo foram realizados utilizando
  Git. Alguns foram executados manualmente por ele, usando `git add`, `git commit`
  e `git push`.
- **Ação:** Na maioria dos casos, Murilo solicitou à IA: “Faça o `git commit` e o
  `git push` das alterações que fizemos, seguindo as regras de commit do projeto”.
- **Resultado:** Na maioria dos commits, a própria IA ajudou a selecionar as
  alterações, preparar os comandos e aplicar a convenção definida para as
  mensagens de commit. Murilo continuou responsável por revisar as alterações e
  autorizar o envio.
- **Decisões:** O uso da IA foi escolhido para evitar o trabalho repetitivo de
  escrever `git add` e listar manualmente todos os arquivos alterados quando isso
  não acrescentava valor ao desenvolvimento. A regra de commits em inglês e no
  padrão Conventional Commits foi mantida.
- **Pendências:** Continuar revisando as alterações antes de confirmar qualquer
  commit ou push.

### Geração assistida dos arquivos e revisão do código

- **Objetivo:** Criar os arquivos necessários para o projeto com apoio da IA e
  compreender o funcionamento de cada parte antes de aceitar as alterações.
- **Contexto:** Os arquivos do projeto foram gerados com auxílio da inteligência
  artificial. Para que a IA entendesse o que precisava ser desenvolvido, Murilo
  apresentou o repositório da atividade do semestre anterior e pediu uma análise
  do contexto, da estrutura e dos requisitos do novo projeto.
- **Ação:** Prompt utilizado: “Analise o repositório da atividade do semestre
  anterior para entender o contexto do projeto que precisamos desenvolver e
  crie o pipeline necessário. Depois explique cada linha do código e como você
  fez isso.”
- **Resultado:** A IA ajudou a gerar o pipeline e os demais arquivos necessários,
  explicando a finalidade das funções, etapas, configurações e integrações.
- **Decisões:** Nenhum arquivo foi aceito sem revisão. Depois das explicações,
  o grupo analisou o código, fez perguntas adicionais e alterou os trechos que
  precisavam de correção ou adaptação ao projeto atual.
- **Pendências:** Continuar explicando e revisando qualquer novo arquivo gerado
  pela IA antes de incorporá-lo ao projeto.

### Prompts não recuperáveis da conta do Claude

- **Objetivo:** Registrar uma limitação na recuperação do histórico completo de
  prompts usados durante a criação dos arquivos.
- **Contexto:** Alguns prompts de criação de arquivos foram feitos na conta do
  Claude. A conta foi suspensa e, por isso, esses prompts não estão mais
  acessíveis para consulta ou cópia integral.
- **Ação:** O grupo revisou os arquivos disponíveis, as conversas recuperadas e
  o histórico do desenvolvimento para reconstruir e registrar as interações que
  puderam ser confirmadas.
- **Resultado:** Foi documentado tudo o que o grupo conseguiu recuperar,
  confirmar e relacionar às alterações realizadas no projeto.
- **Decisões:** Os prompts que não puderam ser recuperados não serão inventados
  nem apresentados como transcrições completas. O registro mantém apenas as
  informações verificáveis e os resumos das interações conhecidas.
- **Pendências:** Nenhuma ação de recuperação é possível enquanto a conta do
  Claude permanecer inacessível.

### Migração da API DONKI com GitHub Copilot

- **Objetivo:** Diagnosticar a falha do endpoint DONKI antigo, migrar o pipeline
  para o CCMC e registrar o resultado da correção.
- **Contexto:** O workflow falhava ao consultar o endpoint antigo da NASA. O
  aumento do timeout não resolveu o problema. O Supabase continuava funcionando,
  enquanto os retornos `503 Service Unavailable` indicavam uma indisponibilidade
  do serviço. O erro `API_KEY_INVALID` foi tratado separadamente como problema
  de configuração da chave.
- **Ação:** O GitHub Copilot foi utilizado para analisar o repositório, organizar
  os diagnósticos e registrar o resumo do chat “Migração da API DONKI”. Também
  foram solicitados a validação segura do secret `NASA_API_KEY`, a pesquisa sobre
  a migração para o CCMC, a atualização do pipeline e a documentação dos
  resultados.
- **Resultado:** A nova URL `https://ccmc.gsfc.nasa.gov/DONKI-API/get/CME`
  retornou HTTP `200`, entregou 90 registros, manteve o schema compatível e não
  exigiu `NASA_API_KEY`. O pipeline foi migrado, o workflow buscou e normalizou
  os registros, gravou os dados no Supabase e registrou o status `concluido`.
- **Decisões:** O incidente foi considerado resolvido com a migração para a API
  DONKI do CCMC. Os prompts, diagnósticos, pesquisas e resultados foram
  organizados em `docs/ai-interaction.md`,
  `docs/diagnostico_nasa_donki_cme_copilot.md` e `README.md`.
- **Evidências:** Foram relacionados os commits `d2d3058`, `3ff3673`, `7cf45e7`,
  `e736cf3`, `d0f6093` e `68c93a4`, seguindo a regra de mensagens em inglês e
  Conventional Commits.
- **Pendências:** Continuar monitorando o workflow e preservar os últimos dados
  válidos caso o serviço externo volte a ficar indisponível.
