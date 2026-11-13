# Reflexão do Grupo

## Propósito

Este documento é um espaço para o grupo registrar reflexões sobre o desenvolvimento do projeto DONKI-CME.

## Instruções

- Registre reflexões sobre desafios enfrentados e soluções encontradas
- Documente aprendizados técnicos e de trabalho em equipe
- Não inclua secrets, chaves de API ou informações sensíveis

---

## Modelo de Registro

### [Título da reflexão]

- **Tema:** Assunto da reflexão
- **Contexto:** Situação que motivou a reflexão
- **Discussão:** Pontos levantados pelo grupo
- **Conclusão:** Aprendizados e decisões tomadas

---

## Reflexões

### Uso crítico da IA no desenvolvimento do DONKI-CME

- **Tema:** Como o grupo utilizou a IA para compreender tecnologias, investigar
  falhas e tomar decisões de implementação.
- **Contexto:** Durante o desenvolvimento, o grupo consultou a IA sobre a API
  DONKI/CME, JSON, Python, Supabase, GitHub Actions, segurança, dashboard,
  gráficos e documentação. Também foi necessário investigar falhas reais do
  endpoint antigo da NASA.
- **Discussão:** A IA ajudou a organizar as etapas, explicar conceitos,
  sugerir indicadores e localizar pontos de falha. Porém, várias orientações
  eram gerais e precisaram ser comparadas com os arquivos, os dados e as
  configurações reais do projeto. Um exemplo foi a falha HTTP 503 do endpoint
  antigo, cuja causa não pôde ser confirmada apenas pela primeira leitura dos
  logs. O grupo precisou comparar execuções, testar endpoints e consultar a
  documentação do CCMC antes de migrar o pipeline.

  Também houve uma decisão de implementação relacionada aos gráficos. Murilo
  planejou inicialmente utilizar Chart.js, mas o grupo decidiu usar Canvas na
  versão final. A escolha reduziu dependências externas e manteve o desenho do
  gráfico sob controle do próprio frontend. Portanto, Chart.js aparece apenas
  como uma alternativa considerada durante o planejamento, não como tecnologia
  utilizada no produto final.

  O grupo também avaliou as sugestões de segurança, deduplicação, upsert,
  tratamento de erros, registro das execuções e proteção das credenciais. As
  respostas da IA não foram aceitas automaticamente: foram testadas, adaptadas
  e revisadas pelos integrantes.

  Em relação ao Git, todos os commits feitos pelo Murilo utilizaram Git. Alguns
  foram feitos manualmente por ele, escrevendo e executando `git add`, `git
  commit` e `git push`. Na maioria dos casos, porém, ele pediu à própria IA para
  fazer o commit e o push das alterações seguindo as regras de commit criadas
  para o projeto.
  Essa escolha evitou o trabalho repetitivo de escrever `git add` e listar todos
  os arquivos alterados, o que poderia consumir tempo sem contribuir diretamente
  para o desenvolvimento. Mesmo com esse apoio, Murilo permaneceu responsável
  por revisar as alterações e seguir as regras definidas para os commits.
  Esse mesmo processo foi usado na criação dos arquivos do projeto. Os arquivos
  foram gerados com auxílio da inteligência artificial, mas o grupo sempre pediu
  explicações sobre o que havia sido feito antes de aceitar o resultado. No caso
  do pipeline, Murilo apresentou à IA o repositório da atividade do semestre
  anterior para que ela entendesse o contexto e os requisitos do novo projeto.
  Depois, pediu explicações sobre cada linha do código e sobre a forma como o
  pipeline havia sido construído. A partir dessas explicações, o grupo identificou
  mudanças necessárias, corrigiu trechos e adaptou o código à realidade do
  projeto. Assim, a IA foi utilizada como apoio à geração e ao aprendizado, e
  não como substituta da análise dos integrantes.
  Também houve uma limitação no registro do histórico. Alguns prompts de criação
  de arquivos foram feitos na conta do Claude, mas a conta foi suspensa e o grupo
  perdeu o acesso a essas conversas. Por isso, não foi possível recuperar todos
  os prompts na íntegra. Mesmo assim, registramos tudo o que conseguimos recuperar
  e confirmar a partir dos arquivos, das conversas disponíveis e das alterações
  realizadas no projeto. Os trechos que não puderam ser confirmados não foram
  inventados.
  A investigação e a documentação da migração da API DONKI também foram feitas
  com auxílio do GitHub Copilot. O Copilot ajudou a organizar o diagnóstico do
  endpoint antigo, a separação entre a indisponibilidade do serviço e o erro de
  configuração da chave, a pesquisa sobre o CCMC e o registro dos commits. Após
  a migração, o novo endpoint retornou os registros esperados, o pipeline foi
  executado com sucesso e os dados foram gravados no Supabase. O grupo conferiu
  esse resultado e registrou a solução nos arquivos do projeto.
- **Conclusão:** A IA foi útil como ferramenta de apoio ao aprendizado e à
  investigação, mas não substituiu as decisões do grupo. O principal
  aprendizado foi formular perguntas mais específicas, fornecer contexto
  suficiente, conferir as evidências e testar as soluções antes de aplicá-las.
  Em uma próxima versão, o grupo melhoraria os testes automatizados para
  timeout, HTTP 503, JSON inválido, normalização e classificação de eventos
  direcionados à Terra.
