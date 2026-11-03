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
