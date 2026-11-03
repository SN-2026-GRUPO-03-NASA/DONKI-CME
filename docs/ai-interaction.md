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
