### Estado mais recente — proposta do SaaS de atendimento (2026-11-14)

A ADR-0020 aprovada permanece na `main` (`0525fe67a5113cbb8527c29ff2e434589e99ae84`). A especificação de atendimento segue isolada na branch `spec/atendimento-saas-mvp`, em estado PROPOSTA e pendente de aprovação explícita.

Deliberações registradas para a proposta: adaptador WhatsApp independente do fornecedor; Chatwoot transitório informado como disponível; telefone sozinho não identifica cliente; identidade de serviço própria n8n→API; piloto com workflows e credenciais separados por perfil; perfil write-only sem criação de cadastro de cliente.

Modelo opaco de credencial de serviço aprovado para a proposta: token de alta entropia emitido pela API ERP, armazenado como hash e vinculado a perfil/escopos/tenant no servidor, com expiração, rotação e revogação. O prazo de validade, autoridade de emissão e escopos finais permanecem pendentes; até 90 dias é apenas sugestão. Nenhum token, chave Supabase, workflow, alteração de código/banco ou deploy foi criado/realizado. O ERP ainda não possui MCP; REST é o caminho inicial proposto. Gates G0/G1 continuam parciais segundo evidência de 2026-11-09 e precisam de revalidação.

---



### Revisão dos arquivos do scaffold após remoção de Mau — 2026-11-09

O responsável compartilhou a leitura local de `apps/api/AGENTS.md`, `README.md` e `package.json`. A inspeção confirma que `@nestjs/mau` não aparece mais nas dependências do `package.json`, mas o script `"deploy": "nest deploy"` permanece e deve ser removido por não corresponder ao destino aprovado VPS/Easypanel. O `package-lock.json` não foi compartilhado, portanto sua consistência ainda não foi verificada.

O `AGENTS.md` mantém uma seção de telemetria e instruções operacionais do NestJS Observe, incompatíveis com a decisão de não adotar essa ferramenta na fundação. O `README.md` continua sendo o template padrão do NestJS, com conteúdo de Mau/AWS, Observe e licença MIT do framework; deve ser substituído por documentação própria da API, sem confundir a licença do framework com a licença `UNLICENSED` da aplicação.

**Estado:** revisão dos três arquivos concluída; ainda não houve alteração local confirmada, commit ou push. Permanecem pendentes a adequação de AGENTS.md e README.md, remoção do script deploy, inspeção do package-lock.json e repetição de build, testes unitários, e2e e lint após os ajustes. O scaffold continua sem módulos de negócio; os gates da ADR-0019 permanecem aplicáveis.


### Validação automatizada após remoção de Mau e do script deploy — 2026-11-09

O responsável reportou a execução local, dentro de `apps/api`, de `npm.cmd pkg delete scripts.deploy`, seguida de build, testes unitários, testes e2e e lint. Todos os comandos terminaram sem erro: build NestJS aprovado; Vitest unitário com 1 arquivo/1 teste aprovado; Vitest e2e com 1 arquivo/1 teste aprovado; Oxlint com 0 warnings e 0 errors. A remoção do script foi executada, mas o conteúdo atualizado do manifesto ainda não foi inspecionado diretamente. O lockfile também permanece não verificado.

**Próximo passo:** receber/revisar AGENTS.md, README.md, package.json e package-lock.json atualizados; conferir consistência e diff/status local antes de qualquer commit. Nenhum commit/push do scaffold foi confirmado.

### Revisão local dos arquivos após remoção do script deploy — 2026-11-09

O responsável compartilhou a saída local atualizada de `AGENTS.md`, `README.md`, `package.json` e as primeiras 40 linhas de `package-lock.json`.

**Constatações:**
- `package.json` não contém o script `deploy` e não lista `@nestjs/mau`; a licença da aplicação permanece `UNLICENSED`. As dependências diretas NestJS, reflect-metadata e rxjs, além das ferramentas de desenvolvimento, aparecem no manifesto.
- O trecho inicial do `package-lock.json` mostra `lockfileVersion: 3` e o manifesto raiz com as mesmas dependências diretas e devDependencies apresentadas no `package.json`. Como só foram fornecidas 40 linhas, não foi possível auditar integralmente os pacotes resolvidos nem pesquisar referências residuais a Mau.
- `AGENTS.md` ainda contém a seção “Production data: NestJS Observe” e instruções para instalar/vincular Observe. Essa orientação conflita com a decisão de não adotar a ferramenta na fundação e deve ser removida ou substituída por instruções neutras de diagnóstico.
- `README.md` continua sendo o template NestJS, com conteúdo de Mau/AWS, Observe e licença MIT do framework. Deve ser substituído por documentação própria da API; a licença do app permanece `UNLICENSED`.

**Validação automatizada reportada na sessão:** build, unitários (1/1), e2e (1/1) e lint (0 warnings/0 errors) aprovados após a limpeza.

**Estado:** a revisão textual está concluída, mas AGENTS.md e README.md ainda precisam de ajuste. Não há commit/push do scaffold confirmado; o repositório remoto de aplicação continua no HEAD baseline anteriormente consultado, sem o scaffold. Próximo passo: adequar os dois documentos, compartilhar seu conteúdo atualizado e a saída integral de conferência do lockfile/status/diff; só então revisar o conjunto para o primeiro commit.

### Publicação e revisão remota do scaffold da API — 2026-11-09

O responsável confirmou o commit local `01d00ea` (`chore(api): add NestJS API scaffold`) e o push da branch `review/api-scaffold-2026-11-09` no repositório privado `lucasfaborges94/erp-winservice-app`. A integração GitHub confirmou a existência da branch e a leitura dos 19 arquivos publicados. O scaffold contém NestJS/TypeScript, ESM, Express, Vitest, Oxlint e configurações; o código funcional ainda é somente o Hello World de exemplo, sem módulos ou endpoints de negócio.

**Revisão remota:** `apps/api/package.json` não contém `@nestjs/mau` nem script `deploy`; licença `UNLICENSED`. O `package-lock.json` usa lockfile v3, corresponde ao manifesto raiz quanto às dependências e devDependencies e não contém referências a Mau. `AGENTS.md` e `README.md` foram corrigidos diretamente na branch de revisão para remover instruções de NestJS Observe/Mau e documentar a arquitetura/gates da ADR-0019. As gravações foram confirmadas pela integração; os arquivos devem ser relidos e validados após a gravação.

**Ponto local pendente:** a saída do commit mostrou `package.json` e `package-lock.json` na raiz como não rastreados (`??`), fora dos 19 arquivos commitados. A origem e necessidade desses dois arquivos ainda não foram verificadas; não adicionar nem excluir sem inspeção. Os avisos LF/CRLF não impediram o commit/push.

**Testes:** build, unitários (1/1), e2e (1/1) e lint (0 warnings/0 errors) haviam sido reportados pelo responsável antes do push. Esta revisão remota não executou os testes. Como README/AGENTS foram ajustados remotamente após o push, será necessária nova validação local depois de sincronizar a branch.

**Estado:** scaffold publicado em branch de revisão, não integrado à main. Nenhuma operação Supabase ou deploy foi realizada. Próximo passo: concluir a releitura remota das correções, solicitar inspeção dos dois manifests não rastreados da raiz e então pedir sincronização da branch e repetição dos testes locais; não abrir/mesclar PR antes de resolver os pontos de revisão.


### Validação local após sincronização da revisão — 2026-11-09

O responsável sincronizou a branch `review/api-scaffold-2026-11-09` com `git pull --ff-only`; o pull avançou de `01d00ea` para `970dc9c`, trazendo as correções remotas de `apps/api/AGENTS.md` e `apps/api/README.md`. Em seguida, executou localmente, dentro de `apps/api`:

- `npm.cmd run build`: concluído sem erro;
- `npm.cmd test`: 1 arquivo e 1 teste aprovados;
- `npm.cmd run test:e2e`: 1 arquivo e 1 teste aprovados;
- `npm.cmd run lint`: 0 warnings e 0 errors.

O `git status --short` final mostrou somente `?? package-lock.json` e `?? package.json` na raiz. O responsável inspecionou seus conteúdos: o `package.json` contém apenas `{}`; o `package-lock.json` contém lockfile v3 com `packages: {}`. São manifests vazios, fora de `apps/api`, sem configuração de workspace ou dependências; não fazem parte do scaffold versionado. A remoção local ainda não foi executada/confirmada.

**Estado:** os gates automatizados locais passaram após a sincronização das correções de documentação. Isso valida build, testes de exemplo e lint do scaffold; não valida módulos de negócio, segurança multiempresa, integração com Supabase ou deploy, que ainda não existem. A branch continua isolada para revisão e não foi integrada à main. Próximo passo: remover os dois manifests vazios não rastreados da raiz, confirmar `git status --short` limpo e então concluir a revisão da branch/decidir abertura de PR. Nenhuma operação Supabase foi realizada.


### Remoção dos manifests vazios da raiz — 2026-11-09

O responsável executou a remoção local de `package.json` e `package-lock.json` vazios que estavam fora de `apps/api`. A mensagem do terminal terminou imediatamente após `git status --short`, sem listar arquivos; isso indica working tree limpa no momento da verificação compartilhada.

A branch `review/api-scaffold-2026-11-09` foi consultada no GitHub. A comparação com `main` informa 3 commits à frente, 0 atrás, e 19 arquivos adicionados, todos concentrados no scaffold em `apps/api` e configurações relacionadas. A revisão dos manifests e documentos confirma `UNLICENSED`, ausência de Mau e do script `deploy`, e documentação alinhada à ADR-0019.

**Estado:** scaffold revisado e gates locais reportados como aprovados após sincronização. Nenhum endpoint de negócio, autenticação, tenancy, integração Supabase ou deploy foi implementado. Branch ainda não integrada à `main`. Próxima decisão: autorização explícita para abrir PR da branch de revisão contra `main`; não realizar merge sem aprovação específica.


### Pull Request aberto para integração do scaffold — 2026-11-09

Após autorização explícita do responsável, foi criado o [PR #1 — chore(api): add NestJS scaffold for ERP](https://github.com/lucasfaborges94/erp-winservice-app/pull/1), de `review/api-scaffold-2026-11-09` para `main` no repositório de aplicação. O GitHub confirmou estado `open`, não draft, 3 commits, 19 arquivos alterados (5.357 adições, 0 remoções), head `970dc9c1658fdc007797283aec669fbc99b50d20` e base `0a6f7ac84a56b092a949817345b5d278abfbfdf7`. A resposta inicial da API retornou `mergeable: false`; isso foi registrado como estado retornado pelo GitHub, sem inferir a causa nem afirmar conflito confirmado.

**Estado:** PR aberto e aguardando revisão; nenhum merge foi executado. Não há autorização para deploy, alterações no Supabase ou implementação de endpoints de negócio. Próximo passo: revisar PR #1 e esclarecer o estado de mergeabilidade antes de qualquer integração.


### Rechecagem do PR #1 — mergeabilidade confirmada — 2026-11-09

Na retomada, o HEAD de documentação consultado foi `306d792e520a716825f0b3317dba7144110e24a3`. O PR #1 (`chore(api): add NestJS scaffold for ERP`) permanece aberto, não draft e não merged. A consulta atual ao GitHub retornou `mergeable: true`, diferente do valor `false` retornado na criação; portanto, o estado anterior foi superado e não há indicação atual de conflito de merge.

A branch `review/api-scaffold-2026-11-09` permanece no commit `970dc9c1658fdc007797283aec669fbc99b50d20`, 3 commits à frente e 0 atrás de `main` (`0a6f7ac84a56b092a949817345b5d278abfbfdf7`). O PR tem 19 arquivos alterados, 5.357 adições e 0 remoções. A consulta encontrou 0 reviews, 0 comentários de conversa e 0 check runs/statuses associados ao commit consultado; isso registra ausência de evidência de execução de checks, não aprovação automatizada.

A revisão do patch e da ADR-0019 confirma que o conteúdo segue limitado ao scaffold NestJS/Hello World, configurações, documentação e testes de exemplo. Não há módulos ou endpoints de negócio, autenticação, tenancy, autorização, integração Supabase ou deploy. Os gates da seção 7 da ADR-0019 continuam obrigatórios antes de conectar clientes ou implementar endpoints de negócio.

**Estado:** PR aberto e atualmente mergeable, aguardando decisão/revisão humana. Nenhum merge foi executado. Nenhuma alteração no Supabase ou deploy foi realizada. Merge continua dependendo de autorização explícita específica do responsável.


### Integração do scaffold da API na main — 2026-11-09

Após autorização explícita do responsável nesta sessão, foi executado o merge do PR #1 — [chore(api): add NestJS scaffold for ERP](https://github.com/lucasfaborges94/erp-winservice-app/pull/1) — usando a estratégia de merge commit e protegendo a operação com o SHA esperado da branch (`970dc9c1658fdc007797283aec669fbc99b50d20`). O GitHub retornou `merged: true`.

**Evidência pós-operação:** PR #1 está `closed` e `merged: true`, com `merged_at: 2026-11-09T13:25:11Z`. O commit de merge é `2e73ce3f2377a8403992ee5b5b46fe4657b2eb56`; a referência `main` do repositório `lucasfaborges94/erp-winservice-app` foi consultada e aponta exatamente para esse SHA. O commit possui os pais esperados: base anterior `0a6f7ac84a56b092a949817345b5d278abfbfdf7` e head do PR `970dc9c1658fdc007797283aec669fbc99b50d20`. A assinatura do commit aparece como verificada pelo GitHub.

**Escopo integrado:** 19 arquivos, 5.357 adições e 0 remoções. O código continua limitado ao scaffold NestJS/Hello World, configurações, documentação e testes de exemplo. Build, testes unitários (1/1), e2e (1/1) e lint (0 warnings/0 errors) foram reportados anteriormente como executados localmente pelo responsável; não foram executados novamente pelo merge. Não há implementação de autenticação, tenancy, autorização, RLS, módulos/endpoints de negócio, integração Supabase ou deploy.

**Estado e limites:** scaffold integrado à `main`. Nenhuma operação Supabase, migration ou deploy foi executada. Os gates da seção 7 da ADR-0019 continuam obrigatórios antes de endpoints de negócio ou conexão de clientes. A branch local do responsável não foi alterada por esta operação remota.


### Estrutura isolada para hardening do Supabase — 2026-11-09

Foi criada no repositório privado `lucasfaborges94/erp-winservice-app` a branch Git `work/supabase-hardening`, baseada na `main` após o merge do PR #1, commit-base `2e73ce3f2377a8403992ee5b5b46fe4657b2eb56`. A branch contém atualização de `supabase/README.md` documentando a organização prevista de migrations, baseline, testes e regras de segurança. A branch não altera a `main` nem o banco principal.

O projeto Supabase de produção identificado é `Winservice` (`voaovwphncwbrwtcqnsc`), região `sa-east-1`, PostgreSQL 17.6.1.141. A consulta de branches retornou lista vazia. O custo cotado para uma branch Supabase é **US$ 0,01344 por hora**; a criação não foi executada porque requer confirmação explícita do responsável após ciência do custo.

**Limitação crítica:** a ferramenta informa que branches Supabase recebem migrations do projeto principal, mas não copiam dados de produção. O inventário estrutural versionado em `supabase/baseline/` não é dump restaurável; a consulta anterior registrou zero migrations e ausência de histórico `supabase_migrations.schema_migrations`. Portanto, criar uma branch Supabase neste momento não produziria réplica do schema real. Primeiro é necessário estabelecer e validar uma baseline de migrations/restauração, ou o responsável deve decidir conscientemente criar um ambiente vazio temporário.

Nenhuma tabela, policy RLS, função, trigger, dado ou configuração do projeto principal foi alterada. Nenhum dado de produção foi copiado. Nenhum deploy foi realizado.


### Retomada da fundação de autenticação e RLS — 2026-11-09 — revalidação somente leitura (Supabase MCP; horário observado pelos Advisors: 2026-11-09T13:37:36.793Z).

A especificação técnica de autenticação, empresa ativa, autorização, contexto transacional e RLS já está elaborada e aprovada, conforme `documentacao/ESPECIFICACAO_TECNICA_AUTENTICACAO_CONTEXTO_RLS.md`. Não é necessário reabrir sua definição; a próxima sequência é completar os gates de prova.

**Estado da aplicação:** repositório `lucasfaborges94/erp-winservice-app`, branch `main`, commit `2e73ce3f2377a8403992ee5b5b46fe4657b2eb56`. A API continua somente com scaffold NestJS/Hello World. O manifesto não contém biblioteca de JWT, conexão PostgreSQL ou módulos de autenticação; não há implementação de negócio.

**Revalidação somente leitura do Supabase:** projeto Winservice `voaovwphncwbrwtcqnsc`, `ACTIVE_HEALTHY`, PostgreSQL `17.6.1.141`; zero migrations retornadas; 38 tabelas em `public`, todas com RLS habilitado. Advisors: 26 tabelas sem policy, 7 funções com search_path mutável, 2 funções SECURITY DEFINER executáveis por anon/authenticated e proteção contra senhas vazadas desabilitada. Detalhes, limitações e interpretação em `documentacao/auditorias/REVALIDACAO_G0_SUPABASE_2026-11-09.md`.

**Gates:** G0 permanece parcial; faltam backup restaurável, baseline SQL reconciliada, verificação de pooler/conexão e ensaio de restauração. G1 permanece parcial; validação JWT ponta a ponta e provisionamento ainda não comprovados. G2–G5 não iniciados.

**Próxima ação:** obter backup restaurável e realizar ensaio de restauração isolado; preparar baseline SQL reconciliada e registrar parâmetros não secretos do pooler. Não executar DDL/DML, migrations, ajustes de segurança ou deploy sem autorização operacional específica e gates concluídos.
