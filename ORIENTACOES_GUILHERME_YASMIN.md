# Orientações para finalizar a atividade

## Objetivo

Concluir a entrega do projeto DONKI-CME com o Supabase na região exigida pela atividade, documentação completa, evidências da execução e apresentação ensaiada.

O GitHub e as configurações do repositório ficam sob responsabilidade de vocês. O projeto atual do Supabase deve ser preservado até a migração ser confirmada.

## 1. Criar o novo projeto Supabase em São Paulo

Criar um novo projeto com:

- Organização: `SN-2026-GRUPO-03-NASA`
- Projeto: `DONKI-CME-DB`
- Região: `South America (São Paulo)`

Não excluir o projeto antigo antes de confirmar que o novo está funcionando.

## 2. Recriar a estrutura do banco

No novo projeto:

1. Abrir o SQL Editor.
2. Executar o arquivo `sql/setup.sql`.
3. Confirmar a criação das tabelas `cme_events` e `execucoes`.
4. Confirmar `UNIQUE` em `activity_id`.
5. Confirmar RLS ativo nas duas tabelas.
6. Confirmar as políticas de leitura para `anon` e `authenticated`.
7. Confirmar acesso completo para `service_role`.
8. Confirmar que a API REST pública retorna JSON.

## 3. Migrar os dados

Depois de criar as tabelas:

1. Exportar os dados válidos do projeto antigo, se necessário.
2. Importar os registros de `cme_events` no novo projeto.
3. Importar os registros de `execucoes` ou registrar uma nova execução válida.
4. Verificar se não existem duplicatas por `activity_id`.
5. Confirmar que os dados aparecem no Table Editor.

Uma alternativa é atualizar os secrets do GitHub para o novo projeto e executar o workflow manualmente. Nesse caso, o pipeline deve repopular `cme_events` e criar um novo registro em `execucoes`.

## 4. Atualizar as credenciais do pipeline e do painel

No GitHub, atualizar os secrets do repositório com os dados do novo projeto:

- `SUPABASE_URL`: URL do novo projeto.
- `SUPABASE_SERVICE_KEY`: nova chave `service_role`.

No `script.js`, atualizar somente os dados públicos:

- URL pública do novo projeto.
- Chave pública `anon` ou publishable key.

Nunca colocar a chave `service_role` no código, nos PDFs, em prints ou nas mensagens.

Depois disso:

1. Executar o workflow manualmente.
2. Confirmar status verde.
3. Confirmar dados reais em `cme_events`.
4. Confirmar registro `concluido` em `execucoes`.
5. Abrir o painel e testar filtros e última atualização.

## 5. Adicionar o professor no novo Supabase

No projeto novo, convidar:

`rafael.ferques@ifpr.edu.br`

Permissão exigida: `Developer`.

Confirmar que o convite foi enviado para o projeto novo, não apenas para o projeto antigo ou para o GitHub.

## 6. Preencher `docs/reflexao.md`

Registrar apenas fatos reais sobre o uso de IA no projeto:

- Onde a IA ajudou.
- Quais erros ou sugestões incorretas apareceram.
- O que o grupo corrigiu manualmente.
- Quais decisões técnicas foram tomadas pelo grupo.
- O que seria feito de forma diferente em uma próxima versão.

Não inventar prompts, resultados ou dificuldades que não aconteceram.

## 7. Atualizar a planilha da atividade

Atualizar as colunas G e H com os links definitivos:

- Organização: `https://github.com/SN-2026-GRUPO-03-NASA`
- Repositório: `https://github.com/SN-2026-GRUPO-03-NASA/DONKI-CME`
- Painel: `https://sn-2026-grupo-03-nasa.github.io/DONKI-CME/`
- Novo projeto Supabase: usar a URL gerada após a migração.

Conferir se os links abrem em uma janela anônima.

## 8. Montar o PDF único da entrega final

O arquivo enviado no Google Classroom deve ser um único PDF contendo:

1. Link da organização do GitHub.
2. Link do repositório.
3. Link do painel publicado.
4. Print do workflow com status verde.
5. Print do Table Editor do novo Supabase com dados inseridos.
6. URL da API REST do novo Supabase retornando JSON.
7. Evidência da planilha com as colunas G e H atualizadas.

Usar apenas prints do projeto novo e dos links finais.

## 9. Ensaiar a apresentação

Tempo máximo: 20 minutos de apresentação e 10 minutos de perguntas.

Sugestão de divisão:

- 6 min: API DONKI/CME e relevância dos dados.
- 4 min: arquitetura NASA/CCMC, Actions, Supabase e Pages.
- 8 min: demonstração do painel, workflow e banco novo.
- 2 min: uso da IA, erros encontrados e correções feitas.

Todos os integrantes devem participar da explicação e das respostas.

Também é recomendável manter prints ou gravações do painel, do workflow e do Supabase para usar como backup durante o seminário.

## Checklist final

- [ ] Novo projeto Supabase criado em São Paulo.
- [ ] `sql/setup.sql` executado.
- [ ] Dados migrados ou repopulados.
- [ ] Secrets apontando para o novo projeto.
- [ ] Painel usando a URL e a chave pública do novo projeto.
- [ ] Workflow executado com sucesso.
- [ ] `execucoes` com status `concluido`.
- [ ] Professor adicionado como Developer no novo Supabase.
- [ ] `docs/reflexao.md` preenchido.
- [ ] Planilha atualizada.
- [ ] PDF único montado.
- [ ] Apresentação ensaiada por todos.
