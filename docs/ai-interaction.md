# AI Interaction Log

## Propósito

Este documento registra as interações com assistentes de IA durante o desenvolvimento do projeto DONKI-CME.

## Instruções

- Registre cada interação relevante com data, objetivo e resultado obtido
- Não inclua secrets, chaves de API ou informações sensíveis
- Descreva o contexto e as decisões tomadas

---

## Modelo de Registro

### YYYY-MM-DD: [Título da interação]

- **Objetivo:** O que se pretendia alcançar
- **Contexto:** Situação do projeto na época
- **Ação:** O que foi solicitado à IA
- **Resultado:** O que foi obtido
- **Decisões:** Escolhas técnicas ou de design feitas
- **Pendências:** O que ficou para resolver

---

## Registros

<!-- Adicione suas interações abaixo seguindo o modelo acima -->

### 2026-11-03: Diagnóstico e validação da chave da API da NASA

- **Objetivo:** Corrigir as falhas do workflow de atualização de dados.
- **Contexto:** O pipeline passou a usar um timeout de 80 segundos, mas as execuções
  continuaram falhando. Os logs indicaram respostas `503 Service Unavailable` da API
  DONKI e, posteriormente, `API_KEY_INVALID`.
- **Ação:** Solicitação revisada: “Investigue por que o workflow continua falhando,
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

### 2026-11-03: Padronização das mensagens de commit

- **Objetivo:** Definir uma regra única para as mensagens de commit do projeto.
- **Contexto:** Os commits futuros precisam ser escritos em inglês e seguir uma
  convenção reconhecida pela equipe.
- **Ação:** Solicitação revisada: “Estabeleça uma regra para que todas as mensagens
  de commit sejam escritas exclusivamente em inglês e sigam o padrão Conventional
  Commits. Documente a regra nas instruções do projeto e no README, e registre este
  prompt revisado no log de interações.”
- **Resultado:** A regra foi adicionada às instruções do Copilot e ao README.
  Também foram documentados os tipos permitidos, o uso do modo imperativo, a
  descrição concisa e exemplos válidos.
- **Decisões:** A regra se aplica aos novos commits. Commits existentes não serão
  reescritos para preservar o histórico do repositório.
- **Pendências:** Aplicar a convenção em todos os commits futuros.

### 2026-11-03: Investigação da indisponibilidade do endpoint DONKI/CME

- **Objetivo:** Determinar se as falhas persistentes do workflow eram causadas
  pelo código, pela chave da API, pelo timeout ou pelo serviço da NASA.
- **Contexto:** O workflow falhava desde 30 de setembro, apesar de a última
  execução bem-sucedida, em 29 de setembro, ter retornado 119 registros.
- **Ação:** Solicitação revisada: “Investigue profundamente as falhas do workflow
  desde 28 de setembro. Compare as execuções bem-sucedidas e malsucedidas,
  teste o endpoint DONKI/CME e um endpoint alternativo da NASA sem expor secrets,
  documente as evidências e prepare um commit sem fazer push.”
- **Resultado:** A primeira falha ocorreu em 30 de setembro e retornou HTTP `503`.
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

### 2026-11-03: Pesquisa externa sobre a instabilidade do DONKI/CME

- **Objetivo:** Solicitar uma investigação na internet sobre a instabilidade
  persistente do endpoint NASA DONKI/CME.
- **Contexto:** O workflow falha com HTTP `503` desde 30 de setembro de 2026,
  embora tenha funcionado até 29 de setembro. O relatório retornado pela pesquisa
  foi salvo em
  [diagnostico_nasa_donki_cme_copilot.md](diagnostico_nasa_donki_cme_copilot.md).
- **Ação:** Prompt revisado fornecido: “Investigue na internet se a API NASA
  DONKI/CME está enfrentando instabilidade ou indisponibilidade. Compare o
  endpoint antigo `api.nasa.gov/DONKI/CME` com a documentação oficial atual,
  priorize fontes confiáveis, procure relatos desde 30/09/2026, verifique
  migrações, mudanças de URL, autenticação e alternativas oficiais. Diferencie
  fatos confirmados, evidências indiretas e hipóteses; informe URLs, datas,
  trechos relevantes e nível de confiança. Apresente resumo executivo, linha do
  tempo, causas prováveis, testes adicionais e soluções temporária e definitiva.”
- **Resultado:** A resposta identificou uma possível migração oficial do DONKI
  para a infraestrutura CCMC, com novas URLs documentadas, e recomendou validar
  o endpoint `https://ccmc.gsfc.nasa.gov/DONKI-API/get/CME` antes de alterar o
  código. O documento também inclui testes comparativos, critérios de aceite e
  orientações de segurança.
- **Decisões:** O relatório foi armazenado como documentação de diagnóstico, sem
  assumir que a migração resolveu o problema. Qualquer alteração no pipeline deve
  ser feita somente após confirmar a resposta real da nova API e a compatibilidade
  do schema.
- **Pendências:** Validar a nova URL CCMC com a mesma janela de datas, comparar
  respostas e decidir se a URL da API deve ser migrada.

### 2026-11-03: Migração do endpoint DONKI para o CCMC

- **Objetivo:** Corrigir a falha persistente do endpoint antigo e migrar o
  pipeline para a API DONKI oficial do CCMC.
- **Contexto:** A documentação oficial do CCMC indicou a nova base
  `https://ccmc.gsfc.nasa.gov/DONKI-API/get/`. O endpoint CME antigo em
  `api.nasa.gov` continuava retornando HTTP `503`.
- **Ação:** Solicitação revisada: “Leia a documentação completa da API DONKI no
  CCMC, valide o endpoint CME, compare o schema com o pipeline atual e faça a
  migração com as atualizações necessárias de workflow e documentação.”
- **Resultado:** O endpoint CCMC retornou HTTP `200` e 90 registros para a janela
  de 30 dias testada. Os campos consumidos pelo normalizador permaneceram
  compatíveis. O pipeline passou a usar a URL CCMC e deixou de enviar `api_key`,
  pois a consulta validada não exige autenticação.
- **Decisões:** O secret `NASA_API_KEY` não é mais necessário para este workflow,
  embora possa permanecer no repositório para usos futuros. A validação final
  depende de uma execução do GitHub Actions e da confirmação de gravação no
  Supabase.
- **Pendências:** Executar o workflow migrado e verificar se a coleta e o upsert
  terminam com sucesso.
