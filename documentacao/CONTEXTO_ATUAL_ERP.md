# ERP Winservice — Contexto atual e índice operacional

**Documento:** índice operacional resumido  
**Data de referência:** 2026-11-08  
**Branch de referência:** `main`  
**Repositório:** [lucasfaborges94/erp-winservice-dev-docs](https://github.com/lucasfaborges94/erp-winservice-dev-docs)

> Este arquivo acelera a recuperação de contexto. Não é fonte normativa e não substitui a Fonte da Verdade, ADRs, especificações, código, banco, migrações ou evidências. Antes de cada resposta sobre o ERP, consulte novamente o repositório e valide as referências abaixo.

## 1. Protocolo obrigatório de consulta

1. Ler [00_FONTE_DA_VERDADE.md](../00_FONTE_DA_VERDADE.md).
2. Ler [PROTOCOLO_DE_ATUACAO_IA_GITHUB.md](PROTOCOLO_DE_ATUACAO_IA_GITHUB.md).
3. Consultar este índice e [08_CONTINUIDADE_E_TRANSFERENCIA.md](08_CONTINUIDADE_E_TRANSFERENCIA.md).
4. Verificar HEAD/commits atuais e mudanças posteriores ao último registro.
5. Consultar documentos especializados e ADRs individuais relacionados à mensagem atual.
6. Responder somente após validar as fontes.
7. Registrar incrementalmente pontos-chave e sessões relevantes.

A consulta é obrigatória antes de cada resposta, não apenas no início de uma conversa. Se o GitHub estiver inacessível, informar a limitação e não usar a conversa como substituta da documentação vigente.

## 2. Estado documental conhecido

- O repositório é a memória documental oficial e persistente do projeto.
- A consulta por mensagem e o registro incremental de sessões foram aprovados em 2026-11-08.
- A etapa do projeto permanece em consolidação de arquitetura, fundação multiempresa, autorização e especificação operacional. Não presumir que a implementação esteja concluída.
- A prioridade registrada do produto é Operação primeiro, com evolução modular.
- O status detalhado e as limitações devem ser confirmados em [06_STATUS_E_RELATORIOS.md](06_STATUS_E_RELATORIOS.md).

## 3. Decisões e referências críticas

- [02_DECISOES_ARQUITETURA.md](02_DECISOES_ARQUITETURA.md) é somente o índice; consultar fichas individuais.
- [ADR-0017](decisoes/ADR-0017-*) — consultar a ficha individual para tenancy e autorização multiempresa.
- [ADR-0018](decisoes/ADR-0018-company-user-roles-fonte-unica-de-papeis.md) — aprovada: `company_user_roles` é a fonte única de atribuição de papéis por vínculo usuário–empresa; `company_users.role_identifier` e seu valor legado ficam fora do ERP, sem reconciliação ou migração; remoção física depende do tratamento de dependências.
- [MODELO_FUNCIONAL_OPERACIONAL_MVP.md](MODELO_FUNCIONAL_OPERACIONAL_MVP.md) — modelo funcional da Operação; conferir versão atual.
- [MODELO_LOGICO_DADOS_OPERACAO.md](MODELO_LOGICO_DADOS_OPERACAO.md) — proposta lógica; não presumir aprovação como modelo físico.
- [BASE_CONHECIMENTO_ERP.md](BASE_CONHECIMENTO_ERP.md) — arquitetura conceitual aprovada da Base de Conhecimento; conferir escopo e pendências atuais.

## 4. Pendências técnicas documentadas na última continuidade consultada

A última parte da continuidade consultada registra como próxima frente preparar a especificação executável e o pacote de migração do catálogo de permissões, após:
- inventariar dependências de `permissions`, `role_permissions` e `user_permissions`;
- validar o registro legado;
- definir códigos estáveis;
- identificar repositório e baseline oficiais de migrations;
- resolver pontos técnicos ainda abertos na proposta física.

Também permanecem frentes registradas no histórico de continuidade, incluindo a fundação multiempresa e dependências da ADR-0018. Revalidar tudo contra o HEAD e os documentos específicos antes de agir; esta lista é um atalho, não uma declaração de status atual.

## 5. Decisão mais recente — códigos de permissão

Em 2026-11-08, foi aprovada a convenção semântica `entidade.ação` para `permission_code`, em minúsculas e independente dos nomes físicos das tabelas. O registro legado `customer/view` deverá ser mapeado para `customer.view`, sujeito à validação no plano de migração. A aprovação não define a matriz completa de permissões nem autoriza alterações no Supabase. Especificação: [proposta física do catálogo de permissões v0.4.0](relatorios/PROPOSTA_MODELO_FISICO_CATALOGO_PERMISSOES_MVP.md).

## 6. Repositórios do projeto

- Repositório documental oficial: `lucasfaborges94/erp-winservice-dev-docs`.
- Em 2026-11-08, o responsável aprovou a criação de um repositório separado de código-fonte com o nome `lucasfaborges94/erp-winservice-app`, visibilidade privada e branch principal `main`.
- **Estado verificado em 2026-11-08/09:** repositório criado e acessível pela integração GitHub; privado, branch padrão `main`, proprietário `lucasfaborges94`, permissão da integração `admin`.
- O repositório deixou de estar vazio. HEAD verificado: `0a6f7ac84a56b092a949817345b5d278abfbfdf7` (`docs(supabase): document initial advisor findings`). Há quatro commits e artefatos em `supabase/README.md` e `supabase/baseline/` (inventário estrutural e relatório de advisors, datados de 2026-11-08/09).
- Não há evidência de API implementada, aplicação web/mobile, migrations executáveis ou baseline SQL restaurável. O inventário é estrutural e não substitui dump restaurável nem migration.
- Próximo passo: definir a estrutura de aplicação e o contrato/forma da API sem presumir framework; reconciliar o estado real do Supabase antes de criar baseline de migrations.

## 7. Índice por assunto

| Assunto | Fonte a consultar |
|---|---|
| Objetivo, escopo e limites | [01_PROJECT_CHARTER.md](01_PROJECT_CHARTER.md), [04_MODULOS_ESCOPO.md](04_MODULOS_ESCOPO.md) |
| Decisões arquiteturais | [02_DECISOES_ARQUITETURA.md](02_DECISOES_ARQUITETURA.md) e ADR individual pertinente |
| Roadmap e prioridades | [03_ROADMAP_ETAPAS.md](03_ROADMAP_ETAPAS.md) |
| Stack e ambientes | [05_STACK_RECURSOS.md](05_STACK_RECURSOS.md) |
| Status e relatórios | [06_STATUS_E_RELATORIOS.md](06_STATUS_E_RELATORIOS.md) |
| Aprovação, versionamento e governança | [07_GOVERNANCA_DOCUMENTAL.md](07_GOVERNANCA_DOCUMENTAL.md) |
| Sessões e retomada | [08_CONTINUIDADE_E_TRANSFERENCIA.md](08_CONTINUIDADE_E_TRANSFERENCIA.md) |
| Autorização e isolamento multiempresa | [MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md](MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md) |
| Operação — requisitos | [MODELO_FUNCIONAL_OPERACIONAL_MVP.md](MODELO_FUNCIONAL_OPERACIONAL_MVP.md) |
| Operação — dados | [MODELO_LOGICO_DADOS_OPERACAO.md](MODELO_LOGICO_DADOS_OPERACAO.md) |
| Base de Conhecimento | [BASE_CONHECIMENTO_ERP.md](BASE_CONHECIMENTO_ERP.md) |
| Migração multiempresa | [relatorios/PLANO_MIGRACAO_FUNDACAO_MULTIEMPRESA_PARTE_A_MVP.md](relatorios/PLANO_MIGRACAO_FUNDACAO_MULTIEMPRESA_PARTE_A_MVP.md) |
| Catálogo de permissões | [relatorios/PROPOSTA_MODELO_FISICO_CATALOGO_PERMISSOES_MVP.md](relatorios/PROPOSTA_MODELO_FISICO_CATALOGO_PERMISSOES_MVP.md) |

## 8. Registro incremental de sessões

- Registrar aprovações, decisões, requisitos, correções, descobertas, mudanças de estado, artefatos, testes, riscos, bloqueios e próximos passos assim que forem confirmados.
- Uma sessão pode abranger várias mensagens correlatas; atualizar seu registro em vez de criar um arquivo por mensagem.
- Para marcos independentes ou sessões extensas, usar `documentacao/sessoes/AAAA-MM-DD-titulo-curto.md` e manter o índice em [08_CONTINUIDADE_E_TRANSFERENCIA.md](08_CONTINUIDADE_E_TRANSFERENCIA.md).
- Não registrar como implementado algo que foi apenas discutido, especificado ou aprovado.
- Não gerar commits artificiais quando não houver mudança relevante.

## 9. Última atualização

Atualizado em 2026-11-08 para registrar a aprovação do repositório privado de código-fonte `erp-winservice-app`. A criação permanece pendente de confirmação; o HEAD exato deve ser verificado no GitHub a cada consulta.


### Verificação de acesso ao repositório de aplicação — 2026-11-08

O responsável compartilhou a URL `https://github.com/lucasfaborges94/erp-winservice-app`. A consulta direta de metadados pela integração GitHub retornou HTTP 404; a listagem de repositórios acessíveis para o proprietário retornou somente `erp-winservice-dev-docs`. Portanto, o repositório de aplicação **não está visível/acessível à integração nesta verificação**. Isso não permite distinguir entre repositório inexistente e repositório privado sem acesso concedido à integração. Permanecem não verificados existência, visibilidade, branch padrão e permissões. Não iniciar estruturação nem versionamento de migrations até confirmar acesso e metadados.


### Confirmação de acesso ao repositório de aplicação — 2026-11-08

A integração GitHub passou a acessar `https://github.com/lucasfaborges94/erp-winservice-app`. Metadados confirmados: ID `1450814205`, proprietário `lucasfaborges94`, visibilidade `private`, branch padrão `main`, repositório não arquivado e permissão `admin` para a integração. O tamanho informado é zero. A consulta de conteúdo da branch `main` retornou HTTP 404 com a mensagem `This repository is empty`; a listagem de commits retornou HTTP 409 (`Git Repository is empty`). Portanto, a existência, privacidade, branch e permissão estão confirmadas, mas ainda não há arquivos, commits, código, workflow ou baseline de migrations. Nenhum arquivo foi criado e nenhuma alteração foi feita no Supabase.


### Delegação operacional do Supabase e baseline estrutural — 2026-11-08

O responsável determinou que o agente atual será o operador exclusivo para executar alterações no projeto Supabase do ERP. A delegação define quem executa as operações; não elimina os gates de segurança, revisão, preflight, backup restaurável, autorização operacional específica e verificação posterior já exigidos pelos documentos e ADRs. Não interpretar a delegação como autorização irrestrita para qualquer DDL/DML.

O repositório de aplicação `lucasfaborges94/erp-winservice-app` foi inicializado com:
- `supabase/README.md`;
- `supabase/baseline/2026-11-08-schema-inventory.json`;
- `supabase/baseline/2026-11-08-advisors.json`.

O inventário foi capturado em modo somente leitura no projeto `voaovwphncwbrwtcqnsc`, PostgreSQL `17.6.1.141`, região `sa-east-1`. Ele registra metadados do schema `public`, sem dados de linhas ou segredos: 310 tabelas, 461 colunas, 206 constraints, 110 índices, 11 funções, 18 triggers, 23 policies, 34 sequences, 8 extensões instaladas e 1.064 concessões de privilégios de tabela.

Contagens pontuais confirmadas por SQL somente leitura: 26 visitas, 103 clientes, 99 endereços, 1 empresa, 1 vínculo em `company_users`, 2 associações em `company_user_roles`, 3 papéis, 1 permissão, zero linhas em `role_permissions` e `user_permissions`. A coluna legada `company_users.role_identifier`, sua FK e a função `public.can_view_expense(uuid)` ainda existem; nenhuma foi alterada.

O Supabase retornou zero migrations e a relação `supabase_migrations.schema_migrations` não existe. Portanto, não há baseline executável de migrations. O arquivo JSON é um inventário estrutural datado, **não é um dump restaurável**. A integração disponível não oferece `pg_dump` nem Supabase CLI nesta sessão; gerar o baseline SQL completo continua pendente.

Os advisors foram registrados separadamente. Principais achados:
- Segurança: 26 tabelas com RLS habilitado sem policy; 7 funções com `search_path` mutável; 2 funções `SECURITY DEFINER` executáveis por `anon` e `authenticated`; proteção contra senhas vazadas desabilitada.
- Performance: 89 FKs sem índice de cobertura; 2 pares de índices duplicados; 12 índices sem uso observado; 1 policy com reavaliação desnecessária de função Auth por linha.

Nenhuma alteração foi feita no banco. O inventário é a referência estrutural conhecida desta captura; confirmar drift no Supabase real antes de cada alteração e atualizar o snapshot após mudanças controladas.


## Triagem de segurança Supabase — 2026-11-08

Foi realizada nova inspeção somente leitura dos Security Advisors, definições de funções e policies do schema public. O relatório detalhado está em [AUDITORIA_SEGURANCA_SUPABASE_2026-11-08.md](auditorias/AUDITORIA_SEGURANCA_SUPABASE_2026-11-08.md).

Achados prioritários: (1) `public.authorize_integration_sync_event(...)` é SECURITY DEFINER, executável por anon/authenticated, e o corpo consultado não apresentou validação de assinatura/segredo do webhook antes de inserir payload em `integration_sync_queue`; (2) policies permissivas com `USING (true)`/ `WITH CHECK (true)` em tabelas operacionais como customers, addresses, customer_contacts e visits não implementam o isolamento por empresa exigido pela ADR-0017; (3) `can_view_expense` permanece legado e fora do MVP conforme ADR-0018; (4) 26 tabelas com RLS sem policy exigem triagem individual, não sendo automaticamente exposição; (5) sete funções apresentam search_path mutável; (6) proteção contra senhas vazadas está desabilitada.

Nenhuma chamada RPC de negócio, teste de exploração ou leitura de payload/segredo foi executada. Nenhuma policy, função, grant ou configuração foi alterada. Próximo passo: revisar contrato de autenticação do webhook e produzir matriz de classificação/acesso por tabela, mantendo o dump restaurável como gate obrigatório antes de correções/migrations.


## Matriz inicial de RLS/grants/tenant — 2026-11-08

Foi consultado o catálogo PostgreSQL das 310 tabelas de public. RLS está habilitado em 38/38; FORCE RLS está desabilitado em 38/38; grants explícitos para anon e authenticated foram observados em 38/38; 26 tabelas não possuem policies; 10 tabelas têm policy com predicado literal true (em users, apenas INSERT). 23 tabelas não possuem company_identifier na captura, incluindo visits, work_orders, equipments, quotations e company_user_roles. A matriz por tabela e limitações está em [MATRIZ_RLS_GRANTS_TENANT_2026-11-08.md](auditorias/MATRIZ_RLS_GRANTS_TENANT_2026-11-08.md).

O grant amplo não ignora RLS por si só; tabelas sem policy tendem a negar acesso para papéis sujeitos a RLS. O risco mais direto está nas policies permissivas sem filtro de empresa e nas funções privilegiadas identificadas no relatório de segurança. Nenhuma alteração foi feita no banco.


## Deliberação — integrações fora do MVP (2026-11-08)

O responsável determinou que integrações externas não façam parte do MVP. Auvo, conectores, webhooks, sincronizações, filas e infraestrutura específica de integração não devem ser implementados nem constituir dependência dos fluxos operacionais desta entrega. Os problemas técnicos próprios dessa frente serão tratados quando o desenvolvimento das integrações for retomado.

Essa exclusão não elimina automaticamente a necessidade de uma API interna para servir as aplicações e os módulos do ERP. O protocolo/API interna continua como questão arquitetural em aberto e deve ser definido separadamente. A classificação de integrações como fora do MVP também não autoriza remover objetos já existentes no Supabase.

A auditoria anterior identificou a RPC `public.authorize_integration_sync_event(...)` como SECURITY DEFINER e executável por `anon`/`authenticated`, sem validação de autenticidade visível no corpo inspecionado. O conector está fora do escopo de implementação do MVP; contudo, a existência e a exposição atuais desse objeto permanecem um risco de segurança do ambiente a ser isolado/avaliado antes de qualquer disponibilização pública, sem presumir que a exclusão funcional por si só o desative. Nenhuma alteração no banco foi executada nesta deliberação.


### Arquitetura da API interna — aprovada em 2026-11-08

**Estado:** APROVADA pelo responsável e formalizada na [ADR-0019](decisoes/ADR-0019-arquitetura-api-interna.md).

**Decisão aprovada:** monólito modular com API própria em TypeScript/NestJS, hospedada inicialmente na VPS existente via Easypanel, mantendo o Supabase como PostgreSQL/fonte de verdade e Supabase Auth como provedor de identidade. WeWeb e FlutterFlow consumiriam a API ERP para operações de negócio. Não adotar microsserviços no MVP.

**Responsabilidades aprovadas pela ADR-0019:**
- API: casos de uso, regras de negócio, validação de JWT, seleção/validação de empresa ativa, autorização por vínculo e papéis em `company_user_roles`, auditoria, idempotência e sincronização offline.
- Supabase/PostgreSQL: persistência, constraints, integridade referencial, RLS e funções de banco estritamente necessárias.
- Clientes: apresentação, captura de dados e armazenamento/fila local para operação offline; sem autoridade para conceder acesso ou decidir tenant.
- Supabase Auth: identidade e ciclo de sessão.

**Segurança ainda a especificar:** contrato confiável de contexto transacional da empresa ativa e sua propagação até RLS; estratégia de conexão da API ao Supabase sem uso indiscriminado de `service_role`; segregação de operações privilegiadas; testes negativos multiempresa. A arquitetura não deve ser implementada antes de resolver esses pontos e reconciliar as policies atuais.

**MVP:** integrações externas, webhooks, sincronizações com terceiros, filas e infraestrutura dedicada de integração permanecem fora do escopo conforme deliberação do responsável. A sincronização offline do próprio app técnico é requisito operacional e não deve ser confundida com integração externa.

**Estrutura de código prevista, ainda não criada:** `apps/api` com módulos por domínio (auth, companies, users, permissions, customers, equipments, work-orders, visits, tasks e audit), além de contratos compartilhados quando necessário. O framework NestJS está aprovado pela ADR-0019; a estrutura de código ainda não foi criada.

**Justificativa:** centraliza regras e autorização, preserva separação por domínio e permite evolução sem custo operacional prematuro de microsserviços. A escolha de NestJS integra a direção aprovada pela ADR-0019.

**Próximos passos:** especificar e testar autenticação, empresa ativa, contexto RLS e acesso ao banco; definir contratos REST e fronteiras dos módulos; reconciliar policies e baseline de migrations; só então criar estrutura, testes e pipeline no repositório de aplicação. A aprovação arquitetural não autoriza alterações no Supabase nem implantação.


## Especificação técnica de autenticação e contexto RLS — 2026-11-09

Foi criada a proposta `documentacao/ESPECIFICACAO_TECNICA_AUTENTICACAO_CONTEXTO_RLS.md`, ainda **PENDENTE DE APROVAÇÃO**. Ela detalha validação do JWT Supabase, mapeamento `auth.users.id → users.auth_user_identifier → users.identifier`, seleção/validação da empresa ativa, conexão PostgreSQL com role de runtime sem BYPASSRLS e contexto estritamente transaction-local, autorização e testes negativos.

**Direção recomendada na proposta:** conexão PostgreSQL da API em transação única, role dedicada sem superuser/BYPASSRLS/ownership, contexto Auth e empresa aplicado localmente à transação, vínculo revalidado no mesmo envelope e RLS como enforcement complementar. A viabilidade depende de prova técnica dos roles/grants reais, comportamento de `auth.uid()`, pooler e policies; não é decisão aprovada nem implementação.

**Achado que bloqueia implementação:** a auditoria estrutural anterior registrou divergência potencial entre `users.identifier`, `users.auth_user_identifier`, `company_users.user_identifier` e policies que comparam `auth.uid()`. A relação física e o fluxo de provisionamento devem ser confirmados no Supabase atual.

**Próxima ação:** responsável revisar/aprovar ou ajustar a proposta. Após aprovação, executar G0/G1 em modo somente leitura e prova técnica não produtiva; não alterar produção nem executar migrations sem backup restaurável, ensaio, revisão e autorização operacional específica.


## Aprovação da especificação técnica e reconciliação G0/G1 — 2026-11-09

O responsável aprovou a `ESPECIFICACAO_TECNICA_AUTENTICACAO_CONTEXTO_RLS.md` como direção técnica para a prova de conceito, incluindo os seis pontos da seção 12. A hospedagem inicial na VPS existente via Easypanel permanece aprovada conforme ADR-0019; o mecanismo automatizado e restrito de deploy ainda será especificado. A aprovação não autoriza DDL/DML, migrations, criação de roles, mudanças de policies ou deploy em produção.

Consulta somente leitura ao projeto Supabase `voaovwphncwbrwtcqnsc` (Winservice), PostgreSQL `17.6.1.141`, `ACTIVE_HEALTHY`, região `sa-east-1`:
- 5 linhas em `auth.users` e 5 em `public.users`; 5/5 correspondências únicas por `users.auth_user_identifier = auth.users.id`, sem nulos/duplicidades; FK `users_auth_user_identifier_fkey` validada.
- `auth.uid()` lê `request.jwt.claim.sub` ou `request.jwt.claims.sub`.
- `anon` e `authenticated` sem BYPASSRLS; `service_role` e `postgres` com BYPASSRLS; ainda não existe prova de role dedicada de runtime.
- 38/38 tabelas públicas com RLS; 0/38 com FORCE RLS; 23 policies. Permanecem policies permissivas com predicados `true`, inclusive em tabelas operacionais.
- Policy SELECT de `users` compara `auth.uid()` a `users.identifier`; policy SELECT de `company_users` compara `auth.uid()` a `company_users.user_identifier`. Ambas precisam ser avaliadas frente ao mapeamento Auth canônico e não são consideradas corretas por presunção.
- `company_user_roles` sem policy observada e grants amplos para anon/authenticated/service_role em `users`, `company_users` e `company_user_roles`.
- `company_users.role_identifier` permanece no schema e não pode ser usado, conforme ADR-0018. `public.can_view_expense` ainda aparece como SECURITY DEFINER e dependência a tratar.
- `public.authorize_integration_sync_event` também permanece SECURITY DEFINER e executável por anon/authenticated, embora integrações estejam fora do MVP; exposição deve ser isolada como risco.
- zero migrations registradas; `supabase_migrations.schema_migrations` inexistente; zero branches e zero Edge Functions.

**G0 parcial, não concluído:** falta backup restaurável, baseline SQL reconciliado, detalhes do pooler e ensaio de restauração. **G1 parcial, não concluído:** mapeamento estrutural confirmado, mas falta JWT ponta a ponta, provisionamento e prova do claim sob role não privilegiada. G2–G5 não iniciados.

Nenhuma alteração foi feita no Supabase. Os resultados detalhados e limitações foram adicionados à especificação técnica e à continuidade.

## Pesquisa do procedimento de backup e baseline — 2026-11-09

Foi criado o procedimento proposto [PROCEDIMENTO_BACKUP_BASELINE_SUPABASE.md](PROCEDIMENTO_BACKUP_BASELINE_SUPABASE.md), baseado na documentação oficial atual do Supabase. A documentação confirma duas rotas distintas: dump lógico via CLI (roles/schema/data, com exclusão de schemas gerenciados como auth e storage) e restauração física para novo projeto (recurso beta, mais abrangente, inclui dados Auth, exige plano pago/backups físicos e pode gerar custo adicional). O dump lógico não é cópia integral do projeto. Dados reais do arquivo data.sql devem permanecer criptografados e fora do Git/chat. Nenhuma operação Supabase foi executada nesta etapa. G0 permanece parcial até existir baseline revisado e ensaio de restauração com escopo comprovado. Próxima ação: captura lógica local e revisão apenas dos arquivos estruturais sanitizados; avaliar clone físico separado se for necessária prova completa de Auth.



## Classificação dos dados existentes — declaração do responsável (2026-11-09)

O responsável esclareceu que os registros atualmente existentes no Supabase são dados de mockup inseridos para testes e mantidos como referência para compreender a lógica/modelagem em construção. Para análise do domínio, os dados de linhas podem ser tratados como dados de teste conforme essa declaração. Isso não comprova ausência de segredos, hashes, tokens ou credenciais nos dumps; tais conteúdos ainda devem ser inspecionados e removidos antes de compartilhamento. O procedimento de backup foi atualizado para permitir análise privada de data.sql após triagem, sem versioná-lo no Git por padrão. A classificação não dispensa backup restaurável nem ensaio de restauração.
