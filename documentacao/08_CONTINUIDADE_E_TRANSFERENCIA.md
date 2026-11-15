

### Continuação — revisão de AGENTS.md, README.md e package.json (2026-11-09)

O responsável compartilhou a saída local de `Get-Content apps\api\AGENTS.md`, `README.md` e `package.json`.

**Constatações verificadas na saída:**
- `package.json` não contém mais `@nestjs/mau` nas dependências, confirmando a remoção refletida no manifesto.
- O script `"deploy": "nest deploy"` ainda está presente e deve ser removido, pois o destino aprovado pela ADR-0019 é a VPS existente via Easypanel, não o fluxo Mau/AWS.
- `AGENTS.md` ainda contém a seção “Production data: NestJS Observe”, com instruções de instalação/link e uso da ferramenta. Remover essa seção ou substituí-la por orientações neutras de diagnóstico, sem recomendar a adoção do Observe.
- `README.md` ainda é o template padrão NestJS e contém referências a Mau/AWS, NestJS Observe e licença MIT do framework. Substituir por documentação própria da API ERP; manter clara a licença `UNLICENSED` da aplicação.
- O conteúdo de `package-lock.json` não foi compartilhado nesta interação. A remoção pelo npm foi reportada anteriormente, mas a consistência do lockfile ainda não foi inspecionada diretamente.

**Estado e limites:** a revisão foi concluída, mas nenhuma alteração local foi executada pelo assistente. Não há commit/push confirmado e o scaffold segue sem módulos de negócio. A remoção de Mau no manifesto está confirmada; o script deploy e os documentos ainda precisam de ajuste. Após as alterações, executar novamente build, testes unitários, e2e e lint, revisar manifests/lockfile e diff. Não criar commit sem revisão e autorização explícita. Nenhuma operação no Supabase foi realizada.

**Próxima atividade:** alinhar os três arquivos à ADR-0019, inspecionar o lockfile atualizado e repetir as verificações automatizadas antes de preparar o primeiro commit.


### Continuação — remoção do script deploy e validação automatizada (2026-11-09)

O responsável informou que executou em `apps/api`:
- `npm.cmd pkg delete scripts.deploy`;
- `npm.cmd run build` — concluído sem erro;
- `npm.cmd test` — 1 arquivo e 1 teste aprovados;
- `npm.cmd run test:e2e` — 1 arquivo e 1 teste aprovados;
- `npm.cmd run lint` — 0 warnings e 0 errors.

**Estado verificado por evidência compartilhada:** os quatro gates automatizados (build, unitário, e2e e lint) passaram após a limpeza reportada. O comando de remoção do script deploy foi executado, mas ainda não foi feita leitura do package.json atualizado para confirmar o estado final do manifesto. O package-lock.json permanece sem inspeção direta. AGENTS.md e README.md ainda aguardam revisão das versões atualizadas.

**Limites:** execução local reportada pelo responsável; não houve acesso à máquina local, commit ou push confirmado. Nenhuma operação no Supabase foi realizada.

**Próxima atividade:** revisar as versões atualizadas de AGENTS.md, README.md, package.json e package-lock.json; verificar diff e status local antes de preparar o primeiro commit. Não criar commit sem autorização explícita.

### Continuação — revisão dos arquivos atualizados e lockfile parcial (2026-11-09)

O responsável compartilhou a saída local dos seguintes comandos: leitura de `AGENTS.md`, `README.md`, `package.json` e as primeiras 40 linhas de `package-lock.json`.

**Evidências observadas:**
- `package.json` não apresenta mais `scripts.deploy` nem `@nestjs/mau`; mantém `"license": "UNLICENSED"`.
- O cabeçalho do lockfile usa versão 3 e seu pacote raiz reproduz as dependências e devDependencies do manifesto apresentado. A inspeção é parcial, limitada às primeiras 40 linhas; não certifica a árvore inteira nem a ausência global de referências a Mau.
- `AGENTS.md` ainda contém a seção “Production data: NestJS Observe”, incluindo orientação para instalar e vincular a ferramenta. Remover essa seção ou substituí-la por procedimento neutro de diagnóstico, sem prescrever Observe.
- `README.md` continua o template padrão NestJS e inclui referências a Mau/AWS, Observe e licença MIT do framework. Substituir por guia próprio da API ERP, sem atribuir à aplicação a licença do framework.

Os resultados locais reportados anteriormente continuam válidos como evidência compartilhada: build aprovado; testes unitários 1/1; e2e 1/1; lint 0 warnings/0 errors.

**Limites e estado:** a inspeção depende da saída local fornecida; o assistente não acessou a máquina. O repositório remoto de aplicação ainda não contém o scaffold no HEAD consultado. Nenhum commit/push foi confirmado. Não houve operação no Supabase.

**Próxima atividade:** ajustar AGENTS.md e README.md, depois conferir o lockfile completo, `git diff --check`, `git status --short` e o diff integral. Revisar todos os arquivos antes de qualquer commit; não criar commit sem autorização explícita.

### Continuação — scaffold publicado para revisão remota (2026-11-09)

O responsável executou e compartilhou a saída do commit `01d00ea` (`chore(api): add NestJS API scaffold`) e push da branch `review/api-scaffold-2026-11-09` para `lucasfaborges94/erp-winservice-app`. A branch e os arquivos foram localizados pela integração GitHub. O commit adicionou 19 arquivos (5.379 inserções), incluindo `.gitignore`, configurações, manifests, lockfile, arquivos de aplicação e testes.

**Revisão remota dos manifests:** `apps/api/package.json` está sem Mau e sem script deploy, mantém `UNLICENSED`; lockfile v3 corresponde ao manifesto raiz nas dependências/devDependencies e não contém referências a `@nestjs/mau`. O scaffold permanece no Hello World, sem endpoints ou módulos de negócio.

**Correções remotas realizadas pelo agente na branch de revisão:** substituídos `apps/api/AGENTS.md` e `apps/api/README.md` para remover orientação de NestJS Observe/Mau e refletir a arquitetura aprovada e os gates de segurança da ADR-0019. Commits individuais de atualização confirmados pela integração; nenhuma alteração foi feita na main.

**Pendência local identificada:** a saída de `git status --short` mostrou `?? package-lock.json` e `?? package.json` na raiz. Esses arquivos não fazem parte do commit do scaffold e sua origem/necessidade não está esclarecida. Solicitar inspeção local antes de qualquer inclusão ou exclusão.

**Testes:** o responsável já havia reportado build, unitário (1/1), e2e (1/1) e lint (0 warnings/0 errors) aprovados. A revisão remota não executou testes. Como README e AGENTS foram modificados na branch após o push, repetir as verificações localmente depois de sincronizar.

**Estado:** branch de revisão publicada; não integrada à main. Nenhuma operação Supabase ou deploy. Próxima ação: reler os arquivos corrigidos no GitHub, esclarecer os manifests não rastreados na raiz e solicitar atualização local + nova execução de build/testes/lint antes de qualquer integração.


### Continuação — validação local após sincronização e manifests vazios — 2026-11-09

O responsável executou `git switch review/api-scaffold-2026-11-09` e `git pull --ff-only`. A branch já estava selecionada e foi atualizada por fast-forward de `01d00ea` para `970dc9c`, recebendo as correções de `apps/api/AGENTS.md` e `apps/api/README.md` feitas pelo agente no GitHub.

Na pasta `apps/api`, executou e compartilhou os resultados:
- `npm.cmd run build`: sucesso;
- `npm.cmd test`: 1 arquivo/1 teste aprovado;
- `npm.cmd run test:e2e`: 1 arquivo/1 teste aprovado;
- `npm.cmd run lint`: 0 warnings/0 errors.

A inspeção do status mostrou apenas dois arquivos não rastreados na raiz. O conteúdo informado foi:
- `package.json`: objeto JSON vazio (`{}`);
- `package-lock.json`: lockfile v3 com `packages: {}`.

Esses arquivos vazios estão fora do diretório da API e não participam do scaffold; não há indicação de que configurem workspace ou dependências. Foi recomendada sua remoção local, ainda pendente de confirmação. Não devem ser adicionados ao Git.

**Estado verificado:** validação local do scaffold após sincronização aprovada nos quatro gates. O escopo testado permanece apenas o Hello World e a fundação NestJS. Não houve teste de autenticação, tenancy, RLS, integração Supabase ou deploy. Branch de revisão ainda não integrada à main. Nenhuma operação Supabase foi realizada.

**Próximo passo:** remover os dois manifests vazios não rastreados da raiz e confirmar status limpo. Depois, concluir revisão e decidir integração via PR; não antecipar implementação de endpoints de negócio antes dos gates da ADR-0019.


### Continuação — remoção dos manifests vazios e revisão da branch — 2026-11-09

O responsável executou `Remove-Item package.json` e `Remove-Item package-lock.json` na raiz do repositório local, seguidos de `git status --short`. A saída compartilhada não apresentou nenhuma linha após o comando, indicando working tree limpa no momento da verificação.

A consulta remota confirmou a branch `review/api-scaffold-2026-11-09` no repositório `lucasfaborges94/erp-winservice-app`. A comparação `main...review/api-scaffold-2026-11-09` retornou estado `ahead`, 3 commits à frente, 0 atrás, com 19 arquivos adicionados. Foram revisados `apps/api/package.json`, `apps/api/package-lock.json`, `apps/api/README.md` e `apps/api/AGENTS.md`: manifesto `UNLICENSED`, sem `@nestjs/mau` ou script `deploy`; lockfile v3 alinhado às dependências diretas; documentação descreve o scaffold real e os gates da ADR-0019.

Build, testes unitários (1/1), e2e (1/1) e lint (0 warnings/0 errors) foram executados localmente pelo responsável após sincronização e reportados como aprovados. A evidência cobre apenas o scaffold/Hello World. Não há implementação de negócio, autenticação, tenancy, RLS, integração Supabase ou deploy. Nenhuma operação Supabase foi realizada.

**Próximo passo:** solicitar decisão explícita para abrir PR da branch de revisão contra `main`. Não realizar merge sem aprovação específica.


### PR #1 aberto — scaffold da API — 2026-11-09

Após autorização explícita do responsável, foi criado o [Pull Request #1](https://github.com/lucasfaborges94/erp-winservice-app/pull/1), título `chore(api): add NestJS scaffold for ERP`, da branch `review/api-scaffold-2026-11-09` para `main`. O GitHub confirmou PR aberto, não draft, com 3 commits, 19 arquivos alterados (5.357 adições, 0 remoções), head `970dc9c1658fdc007797283aec669fbc99b50d20` e base `0a6f7ac84a56b092a949817345b5d278abfbfdf7`. O campo retornado `mergeable` veio como `false`; a causa não foi determinada e não se deve interpretar esse campo isoladamente como conflito confirmado.

A descrição do PR documenta o scaffold, as verificações locais reportadas, os limites de implementação e os gates da ADR-0019. **Nenhum merge foi realizado.** Nenhuma operação Supabase ou deploy foi executado. Próxima atividade: revisar PR #1 e esclarecer mergeabilidade; aguardar aprovação específica antes de qualquer merge.


### Continuação — rechecagem do PR #1 e estado de mergeabilidade — 2026-11-09

Na retomada da sessão, foram consultados novamente o HEAD e os documentos de entrada do repositório oficial. O HEAD de documentação era `306d792e520a716825f0b3317dba7144110e24a3` antes deste registro.

O PR #1 (`chore(api): add NestJS scaffold for ERP`) foi consultado diretamente e permanece aberto, não draft e não merged. O estado atual retornado pelo GitHub é `mergeable: true`, superando o `mergeable: false` retornado na criação. A comparação da branch confirma 3 commits à frente e 0 atrás de `main`, sem mudança nos SHAs de head/base registrados anteriormente.

A consulta encontrou zero reviews, zero comentários de conversa e zero check runs/statuses no commit do PR. Isso não equivale a aprovação de CI. A revisão do patch e da ADR-0019 confirma que o conteúdo permanece um scaffold NestJS/Hello World com configurações, documentação e testes de exemplo; não há autenticação, tenancy, autorização, endpoints de negócio, integração Supabase ou deploy. Os gates da seção 7 da ADR-0019 continuam obrigatórios.

**Estado e limites:** nenhum merge foi executado e não foi feita alteração no repositório de aplicação. Nenhuma operação Supabase ou deploy foi realizada. O merge exige autorização específica do responsável; o fato de o PR estar mergeable não constitui autorização.

**Próxima atividade:** aguardar decisão explícita do responsável sobre integrar o scaffold. Se aprovado, realizar somente o merge do PR #1, sem deploy ou alterações Supabase; depois verificar o commit de merge e atualizar o estado documental.


### Continuação — merge do PR #1 confirmado na main — 2026-11-09

O responsável autorizou explicitamente a integração do scaffold da API. Antes da operação, o PR #1 foi consultado novamente e estava aberto, não draft, não merged e `mergeable: true`; head esperado: `970dc9c1658fdc007797283aec669fbc99b50d20`.

Foi executado merge com método `merge` e `expected_head_sha` igual ao head acima. O GitHub retornou `merged: true` e criou o commit `2e73ce3f2377a8403992ee5b5b46fe4657b2eb56`.

**Verificação independente após o merge:**
- PR #1: `state=closed`, `merged=true`, `merged_at=2026-11-09T13:25:11Z`.
- Repositório de aplicação `lucasfaborges94/erp-winservice-app`: referência `refs/heads/main` aponta para `2e73ce3f2377a8403992ee5b5b46fe4657b2eb56`.
- Commit de merge: pais `0a6f7ac84a56b092a949817345b5d278abfbfdf7` e `970dc9c1658fdc007797283aec669fbc99b50d20`; GitHub informa assinatura verificada.
- Conteúdo integrado: 19 arquivos, 5.357 adições, 0 remoções.

O merge não executou build ou testes. Permanecem como evidência os resultados locais anteriormente reportados pelo responsável: build aprovado, testes unitários 1/1, e2e 1/1 e lint sem warnings/erros. O scaffold segue sem módulos/endpoints de negócio e sem autenticação, tenancy, autorização ou integração Supabase.

**Limites:** nenhuma operação Supabase, migration ou deploy foi realizada. Os gates de segurança da seção 7 da ADR-0019 continuam bloqueando endpoints de negócio e conexão de clientes até especificação e validação. A integração remota não alterou a cópia local do responsável.

**Próxima atividade:** retomar a sequência da ADR-0019 pela especificação técnica de JWT, seleção/validação de empresa ativa, papéis/permissões, contexto transacional PostgreSQL/RLS, privilégios e testes negativos multiempresa. Não executar alterações no Supabase nem deploy sem autorização operacional específica e gates documentados.


### Estrutura isolada para hardening do Supabase — 2026-11-09

Foi criada no repositório privado `lucasfaborges94/erp-winservice-app` a branch Git `work/supabase-hardening`, baseada na `main` após o merge do PR #1, commit-base `2e73ce3f2377a8403992ee5b5b46fe4657b2eb56`. A branch contém atualização de `supabase/README.md` documentando a organização prevista de migrations, baseline, testes e regras de segurança. A branch não altera a `main` nem o banco principal.

O projeto Supabase de produção identificado é `Winservice` (`voaovwphncwbrwtcqnsc`), região `sa-east-1`, PostgreSQL 17.6.1.141. A consulta de branches retornou lista vazia. O custo cotado para uma branch Supabase é **US$ 0,01344 por hora**; a criação não foi executada porque requer confirmação explícita do responsável após ciência do custo.

**Limitação crítica:** a ferramenta informa que branches Supabase recebem migrations do projeto principal, mas não copiam dados de produção. O inventário estrutural versionado em `supabase/baseline/` não é dump restaurável; a consulta anterior registrou zero migrations e ausência de histórico `supabase_migrations.schema_migrations`. Portanto, criar uma branch Supabase neste momento não produziria réplica do schema real. Primeiro é necessário estabelecer e validar uma baseline de migrations/restauração, ou o responsável deve decidir conscientemente criar um ambiente vazio temporário.

Nenhuma tabela, policy RLS, função, trigger, dado ou configuração do projeto principal foi alterada. Nenhum dado de produção foi copiado. Nenhum deploy foi realizado.


## Retomada — especificação aprovada e revalidação do Gate G0 — 2026-11-09

**Objetivo:** retomar a sequência de segurança da API após a especificação técnica aprovada, validando o estado atual do Supabase antes de qualquer implementação de negócio.

**Fontes consultadas:** HEAD `c207bc4719ae97dc01f212cea17cb3422f478407` do repositório documental; Fonte da Verdade, protocolo GitHub, Leia-me, Charter, índice de ADRs, roadmap, escopo, stack, governança, contexto, status, continuidade, ADR-0017, ADR-0018, ADR-0019, especificação técnica de autenticação/contexto/RLS e auditoria de segurança anterior. Repositório de aplicação consultado em `main`, commit `2e73ce3f2377a8403992ee5b5b46fe4657b2eb56`.

**Constatação documental:** a especificação técnica de JWT, seleção de empresa ativa, papéis em `company_user_roles`, conexão PostgreSQL, contexto transaction-local e RLS já está aprovada. A especificação registra G0 e G1 como parciais e G2–G5 como não iniciados.

**Revalidação executada (somente leitura):**
- Supabase Winservice: `ACTIVE_HEALTHY`, PostgreSQL `17.6.1.141`, região `sa-east-1`.
- Zero migrations retornadas pela integração.
- 38 tabelas listadas em `public`, todas com RLS habilitado.
- Advisors: 26 tabelas sem policy; 7 funções com search_path mutável; 2 funções SECURITY DEFINER executáveis por anon/authenticated (`authorize_integration_sync_event` e `can_view_expense`); proteção contra senhas vazadas desabilitada.
- A ausência de policy não foi interpretada isoladamente como exposição; deve ser avaliada com grants, funções privilegiadas, views e caminhos de serviço.

**Estado da API:** `main` em `2e73ce3f2377a8403992ee5b5b46fe4657b2eb56`; scaffold NestJS/Hello World, sem JWT, conexão PostgreSQL, autenticação, tenancy ou módulos de negócio. Build/testes/lint anteriormente reportados foram executados antes da integração e não foram repetidos nesta retomada.

**Trabalho realizado:** consulta do estado documental e da aplicação; inspeção somente leitura de metadados do projeto, migrations, tabelas e Security Advisors; registro dos resultados em `documentacao/auditorias/REVALIDACAO_G0_SUPABASE_2026-11-09.md`.

**Não realizado:** nenhum DDL/DML, migration, grant, policy, função, alteração de Auth, dado, configuração ou deploy. Nenhum teste JWT, pooler ou restauração foi executado.

**Pendências G0:** backup SQL restaurável; baseline SQL reconciliada; documentação da configuração e compatibilidade do pooler; ensaio de restauração em ambiente não produtivo.

**Pendências G1:** prova de validação JWT Supabase na API, mapeamento Auth→ERP ponta a ponta e fluxo de provisionamento comprovado.

**Próxima ação acionável:** obter backup restaurável e preparar ensaio de restauração isolado, sem tocar no projeto principal. Após evidenciar G0, implementar a prova G1 de forma isolada na API, com testes de tokens válidos/inválidos e mapeamento de identidade. Não iniciar endpoints de negócio antes dos gates.

## Continuidade — PR ADR-0020 integrado e especificação técnica do atendimento proposta — 2026-11-14

**Fontes consultadas nesta retomada:** repositório oficial lucasfaborges94/erp-winservice-dev-docs; protocolo GitHub; Fonte da Verdade; Leia-me; continuidade/status/roadmap; ADR-0012, ADR-0017, ADR-0019; especificação técnica de autenticação, contexto transacional e RLS. O contexto conversacional foi usado apenas como pista e conferido com as fontes oficiais.

**PR #1 — ADR-0020:** revisado como alteração documental de dois arquivos (ficha ADR-0020 e índice arquitetural), com aprovação expressa prévia do responsável registrada no histórico da conversa. GitHub retornou PR aberto, não draft, mergeable, head 6bb0aed249bd63f275ef84dfc84a0498f78ce973; revisões formais/checks não foram apresentados pela consulta. O merge foi executado com expected_head_sha exatamente igual ao head verificado. Resultado: merged=true, commit 0525fe67a5113cbb8527c29ff2e434589e99ae84. Reconsulta confirmou PR fechado/merged e main apontando para o mesmo SHA; GitHub informa assinatura verificada.

**Especificação proposta:** criada a branch separada spec/atendimento-saas-mvp a partir da main pós-merge (0525fe67a5113cbb8527c29ff2e434589e99ae84). Adicionado documentacao/ESPECIFICACAO_TECNICA_MVP_ATENDIMENTO_SAAS.md, commit de criação 9baddb8b4c032cd4ecbb09e14768ffb60f998a59, com requisitos funcionais, tenancy, onboarding, modelo de dados lógico proposto, contratos REST, n8n/WhatsApp, segurança, plano de testes e gates. O documento está marcado PROPOSTA, pendente de revisão/aprovação explícita; modelos e rotas são propostas e não descrevem schema físico existente.

**Decisões em aberto registradas na especificação:** fronteira entre o MVP próprio do atendimento e a prioridade Operação/limite de integrações da ADR-0019; provedor e titularidade WhatsApp; inbox de handoff; identificação de cliente por telefone; onboarding/plano/billing; publicação e versionamento de regras; identidade máquina-a-máquina; retenção/privacidade; catálogo global versus tenant; SLOs e suporte. Nenhuma dessas decisões foi promovida a aprovada.

**Gates:** a continuidade/status consultados registram G0 e G1 parciais e G2–G5 não iniciados na última revalidação de 2026-11-09. Essa situação foi reproduzida como referência datada e deve ser revalidada antes de qualquer execução técnica. A especificação adiciona gates de integração, piloto e comercialização, sem declarar gate concluído.

**Limites:** nenhuma operação Supabase, alteração de workflow n8n, código, deploy, produção, testes ou execução de integração foi realizada. Não foi aberto PR da especificação nem ela foi integrada à main. A API permanece, conforme a fonte documental consultada, no scaffold sem autenticação, tenancy ou módulos de negócio.

**Próximo passo:** revisão do documento e deliberação explícita do responsável sobre decisões pendentes e aceite da especificação. Até essa aprovação, manter a branch como proposta; após aprovação, atualizar seu estado por revisão documental apropriada. Implementação continua condicionada aos gates de segurança e autorizações operacionais específicas.

## Revisão inicial da especificação SaaS de atendimento — 2026-11-14

Foi feita revisão de consistência da especificação na branch spec/atendimento-saas-mvp contra a ADR-0020 aprovada e as ADRs-0017/0019.

**Correção documental:** a ADR-0020 já decide em alto nível que o atendimento terá plano de entrega próprio, preservando a prioridade do MVP Operação e sem incorporar automaticamente WhatsApp/n8n ao MVP operacional. A especificação foi corrigida: o item deixa de pedir nova confirmação dessa decisão e mantém pendente apenas o detalhamento das fronteiras de módulos, releases e operação da API compartilhada.

**Recomendações registradas como não aprovadas:** contrato de WhatsApp independente do provedor; avaliar Chatwoot transitório para handoff se disponibilidade operacional for confirmada; não associar cliente só por telefone sem confirmação; identidade n8n mínima e separada por ambiente; publicar regras por versões imutáveis; iniciar com catálogos tenant-scoped; definir retenção antes de dados reais; deixar billing/auto-onboarding fora do piloto inicial.

**Decisões necessárias para fechar contratos de integração:** provedor/canal WhatsApp, inbox humano, política de confirmação de identidade e identidade máquina-a-máquina. Retenção e privacidade precisam ser definidas antes do piloto com dados reais. Billing e SLO comercial podem aguardar a etapa de comercialização.

**Estado:** especificação permanece PROPOSTA. Nenhuma decisão foi aprovada ou assumida pelo responsável; não houve implementação, operação em Supabase/n8n/WhatsApp, testes, deploy ou alteração de produção.

**Próximo passo:** obter deliberação explícita sobre os quatro pontos de integração e sobre retenção/privacidade para piloto; então revisar a especificação e submetê-la à aprovação explícita sem confundir aprovação documental com autorização operacional.

## Deliberações sobre canal/handoff/identidade e esclarecimento de token do agente — 2026-11-14

O responsável confirmou: (1) adaptador WhatsApp independente do provedor; o fornecedor específico continua pendente; (2) Chatwoot como inbox transitório e informou que já está disponível; (3) telefone sozinho não basta para associar cliente, pedindo confirmação em caso ambíguo ou transferindo ao humano.

A especificação na branch spec/atendimento-saas-mvp foi atualizada com essas deliberações, sem aprovar a especificação completa. A disponibilidade do Chatwoot é uma informação do responsável, não uma verificação técnica do ambiente nesta sessão.

**Pergunta pendente do responsável:** se o agente/n8n deve ser identificado por token de usuário emitido pelo Supabase Auth.

**Esclarecimento técnico consultado em documentação oficial Supabase (2026-11-14):** o access token Supabase Auth identifica um usuário Auth e usa a role authenticated; a API key identifica o componente/aplicação, e a secret key/service_role concede acesso elevado com bypass RLS. O OAuth 2.1 Server do Supabase documenta Authorization Code com PKCE e autenticação/aprovação do usuário. Logo, não tratar um token humano como credencial de serviço unattended nem confundir chave secret/service_role com identidade do agente.

**Recomendação registrada como PROPOSTA:** n8n autentica-se na API NestJS como identidade técnica de serviço; token/credencial com audiência da API, escopo mínimo, expiração, rotação e revogação; o servidor resolve tenant pelo canal e permissões configuradas. A API aplica o contexto de banco/RLS sob a role de runtime aprovada. Conta dedicada no Supabase Auth só deve ser considerada se for deliberadamente modelada como principal não humano, sem impersonar colaborador, e com associação/autorização/RLS próprias.

**Ainda não decidido:** se a credencial será token opaco/API key rotacionável, JWT de serviço ou provedor OAuth máquina-a-máquina; identidade e escopo tenant-scoped; propagação segura do ator até RLS. Nenhuma credencial foi criada, Supabase alterado ou workflow modificado.

Referências oficiais: https://supabase.com/docs/guides/getting-started/api-keys ; https://supabase.com/docs/guides/auth/users ; https://supabase.com/docs/guides/auth/oauth-server .

**Próximo passo:** responsável confirmar se aceita o princípio recomendado (identidade técnica n8n perante a API, sem token de usuário humano e sem acesso direto ao banco). Depois definir formato/issuer e detalhes de claims, rotação e escopo durante revisão de segurança; isso não autoriza implementação nem alteração operacional.

## Modelo de múltiplos agentes, identidades e workflows — proposta — 2026-11-14

O responsável perguntou como suportar agentes MCP do ERP com perfis diferentes (ex.: leitura/escrita versus escrita restrita), e se cada identidade de serviço representa agente ou workflow, ou se um workflow roteia para agentes distintos.

**Desenho proposto registrado na especificação:** agente é um principal lógico/capability profile; identidade de serviço autentica o processo e mapeia ao perfil e tenant; workflow n8n é unidade de execução/orquestração, não autoridade por si só; MCP/API valida token e autoriza ação/ferramenta e recurso. Recomenda-se identidade por perfil de capacidade e credenciais separadas para perfis com privilégios diferentes. Para o piloto, workflows distintos por perfil são o caminho mais simples de auditar; roteador compartilhado é possível apenas com origem/principal validados e subworkflows com credenciais separadas. Não confiar em agent_id enviado pelo prompt/payload e não compartilhar credencial global com união de escopos.

“Escrita” deve representar mutações enumeradas (por exemplo, criar qualificação ou handoff), não write genérico. Operações devem validar o objeto/tenant no servidor; leitura contextual mínima pode ser necessária e deve ser explicitamente limitada.

**MCP:** documentação oficial consultada informa que o servidor protegido valida tokens emitidos para seu recurso, aplica scopes e rejeita tokens destinados a outro recurso; tokens não devem ser retransmitidos a uma API upstream como credenciais. Referência: https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization . A documentação n8n informa que editores de workflows compartilhados podem usar credenciais presentes nos workflows: https://docs.n8n.io/workflows/sharing/ .

**Estado:** texto incluído em documentacao/ESPECIFICACAO_TECNICA_MVP_ATENDIMENTO_SAAS.md na branch de proposta. São recomendações, não decisão aprovada. O responsável ainda não confirmou a identidade técnica de serviço nem o modelo de escopos. Nenhum servidor MCP, workflow, API, Supabase ou produção foi alterado; nenhuma credencial foi criada.

**Próximo passo:** o responsável confirmar o princípio de identidades por perfil de capacidade e escolher se deseja começar com workflows separados (recomendado para piloto) ou um roteador autenticado; manter a escolha do mecanismo de token/issuer sujeita à revisão de segurança.

## Correção de estado — MCP do ERP ainda não existe — 2026-11-14

O responsável esclareceu que o ERP ainda não possui servidor MCP. As perguntas anteriores sobre permissões MCP foram hipotéticas e exploratórias, não descrição de uma implementação atual.

A especificação foi ajustada para declarar que a integração inicial do MVP é via contratos REST da API NestJS; um servidor MCP é possibilidade futura, opcional, sujeito a decisão e implementação separadas, e não é pré-requisito. A seção sobre múltiplos agentes descreve o modelo futuro e separa agente, identidade técnica e workflow.

**Estado:** somente a documentação da branch spec/atendimento-saas-mvp foi atualizada. Nenhum MCP foi criado; nenhum workflow, API, Supabase ou produção foi alterado. O princípio de identidade por perfil e o uso de workflows separados para piloto continuam propostas não aprovadas.

## Princípio de identidade de serviço do n8n confirmado — 2026-11-14

O responsável concordou com uma identidade de serviço própria para as automações perante a API REST do ERP. Trata-se de aprovação do princípio máquina-a-máquina, não de aprovação da especificação completa ou autorização para criar credenciais.

A identidade do n8n é separada de usuários humanos do Supabase Auth e das chaves/contas de serviço da OpenAI. Uma credencial OpenAI autoriza chamadas à OpenAI, mas não autentica o n8n perante o ERP. A API do ERP deverá validar a identidade de serviço e aplicar os escopos e tenant autorizados; n8n não recebe acesso direto ao banco nem `service_role`.

**Pendências técnicas:** emissor/formato da credencial (token opaco/API key, JWT próprio ou OAuth máquina-a-máquina), audiência, escopos por ação e tenant, segregação por ambiente, validade, rotação, revogação, e propagação do ator para auditoria/RLS. Não foi criada credencial nem alterado OpenAI, Supabase, n8n, API ou produção.

A especificação foi atualizada na branch spec/atendimento-saas-mvp. Permanece PROPOSTA até aprovação explícita posterior.
