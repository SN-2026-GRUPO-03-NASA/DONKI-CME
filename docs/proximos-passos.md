# Próximos Passos — DONKI-CME (Grupo 03)

Documento de acompanhamento para conclusão da Atividade Avaliativa (3º Trimestre).
Última atualização: 30/10/2026.

## 1. Nomenclatura (crítico para avaliação)

- [ ] Renomear/transferir a organização GitHub para `SN-2026-GRUPO-03-NASA`.
- [ ] Renomear o repositório para `DONKI-CME`.
- [ ] Criar/renomear o projeto Supabase para `DONKI-CME-DB` (região South America — São Paulo).
- [ ] Atualizar README, planilha de reserva e links internos com os nomes definitivos.

## 2. Acessos e convites

- [ ] Convidar `GilFerques` para a organização GitHub (People → Invite member).
- [ ] Convidar `rafael.ferques@ifpr.edu.br` no Supabase com permissão Developer (Settings → Team → Invite).

## 3. Secrets do GitHub Actions

- [ ] Configurar `SUPABASE_URL`.
- [ ] Configurar `SUPABASE_SERVICE_KEY` (service_role — nunca no código).
- [ ] Configurar `NASA_API_KEY` (chave pessoal gratuita de api.nasa.gov).
- [ ] Verificar que nenhuma chave privada aparece no histórico de commits ou no `index.html`.

## 4. GitHub Pages

- [ ] Ativar GitHub Pages: Settings → Pages → Source: Deploy from a branch → `main` / root.
- [ ] Confirmar que o painel carrega dados reais do Supabase.
- [ ] Testar filtro por data, gráfico, modal e "última atualização".

## 5. Banco de dados (Supabase)

- [ ] Executar `sql/setup.sql` no SQL Editor (se ainda não executado).
- [ ] Confirmar RLS ativo em `cme_events` e `execucoes`.
- [ ] Confirmar GRANTs: SELECT para `anon`/`authenticated`, ALL para `service_role`.
- [ ] Confirmar constraint UNIQUE em `activity_id`.

## 6. Pipeline e workflow

- [ ] Resolver as falhas recentes do workflow (verificar logs com conta admin).
- [ ] Garantir que a NASA API DONKI responda (testar endpoint com a chave).
- [ ] Executar o workflow manualmente (`workflow_dispatch`) e obter status verde.
- [ ] Confirmar dados reais gravados em `cme_events` e registro em `execucoes`.
- [ ] Manter backup (prints/vídeo) do workflow e do painel funcionando.

## 7. Documentação

- [ ] Preencher `docs/ai-interaction.md` com todos os prompts utilizados (objetivo, resposta, erros, acertos, ajustes).
- [ ] Preencher `docs/reflexao.md` com análise crítica do uso de IA.
- [ ] Revisar/complementar `docs/tutorial.pdf` e `docs/apresentacao.pdf`.
- [ ] Atualizar README com links definitivos e integrantes do grupo.

## 8. Entrega (via Class)

- [ ] Link da organização e do repositório no GitHub.
- [ ] Link do painel publicado no GitHub Pages.
- [ ] Print do workflow com status verde.
- [ ] Print do Table Editor do Supabase com dados inseridos.
- [ ] URL da API REST do Supabase retornando JSON (com publishable key).
- [ ] Atualizar colunas G e H da planilha de reserva da API.
- [ ] Enviar arquivo único em PDF via Google Class.

## 9. Seminário (16/11 e 23/11)

- [ ] Preparar apresentação (até 20 min + 10 min de perguntas).
- [ ] Garantir participação de todos os integrantes.
- [ ] Backup local com prints/vídeo do painel e do workflow.
