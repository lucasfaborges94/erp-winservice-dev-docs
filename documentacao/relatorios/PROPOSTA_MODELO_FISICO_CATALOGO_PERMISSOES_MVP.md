# Proposta de modelo físico do catálogo de permissões — MVP

**Projeto:** ERP Winservice  
**Data:** 2026-11-08  
**Versão:** 0.4.0  
**Estado:** APROVADA COMO MODELO FÍSICO — NÃO IMPLEMENTADA  
**Base arquitetural:** ADR-0011, ADR-0017 e modelo de autorização multiempresa  
**Banco avaliado:** Supabase PostgreSQL 17.6 — projeto `voaovwphncwbrwtcqnsc`

> Esta especificação detalha a modelagem física da ADR-0011 e foi aprovada pelo responsável em 2026-11-08. A aprovação valida o desenho descrito neste documento, mas não autoriza DDL/DML, migrations, alteração de dados, RLS, grants ou qualquer alteração no Supabase. Itens listados como decisões não tomadas permanecem pendentes.

---

## 1. Objetivo

Definir uma estrutura física que suporte simultaneamente:

1. catálogo padrão global mantido pela plataforma;
2. versões globais publicadas e imutáveis;
3. catálogo efetivo próprio para cada empresa;
4. adesão voluntária de cada empresa a uma versão global;
5. personalizações locais preservadas durante novas adesões;
6. permissões exclusivas da empresa, não existentes no catálogo global;
7. histórico auditável e restauração de valores padrão;
8. atribuição de permissões a papéis e exceções individuais por vínculo usuário–empresa;
9. isolamento por `company_identifier` e FKs compostas.

A proposta mantém a semântica de autorização registrada no modelo de autorização: união das permissões dos papéis, exceção individual explícita e negação por padrão.

## 2. Decisão de modelagem proposta

Adotar **catálogo global versionado + catálogo efetivo materializado por empresa (copy-on-adoption) + histórico de alterações**.

O catálogo global é a referência publicada pela plataforma. Cada empresa mantém uma cópia efetiva tenant-scoped da versão que adotou. A cópia permite customização independente, sem alterar a definição global ou afetar outras empresas.

Não usar uma única tabela `permissions` com `company_identifier` nullable para misturar registros globais e locais. A separação física entre catálogo global e catálogo da empresa deve ser explícita.

## 3. Entidades propostas

### 3.1 Catálogo global

#### `global_permission_catalog_versions`

Uma linha por versão publicada do catálogo padrão.

Campos mínimos:
- `identifier uuid PK`;
- `version text NOT NULL`;
- `status text NOT NULL` — no mínimo `draft`, `published`, `retired`;
- `created_at timestamptz NOT NULL`;
- `published_at timestamptz NULL`;
- `created_by uuid NULL`.

Restrições:
- `UNIQUE(version)`;
- versões publicadas não podem ser editadas; uma alteração gera nova versão;
- versão publicada não pode ser excluída enquanto houver adoções ou histórico que a referencie.

#### `global_permissions`

Uma definição de permissão dentro de uma versão global.

Campos mínimos:
- `identifier uuid PK`;
- `catalog_version_identifier uuid NOT NULL`;
- `permission_code text NOT NULL` — código semântico estável entre versões;
- `entity text NOT NULL`;
- `field text NULL`;
- `action text NOT NULL`;
- `description text NULL`;
- `is_active boolean NOT NULL`.

Restrições:
- `UNIQUE(catalog_version_identifier, permission_code)`;
- FK para a versão global;
- o código é estável quando a mesma permissão evolui entre versões;
- mudança semântica incompatível exige novo código, não reaproveitamento silencioso.

Essas duas tabelas são globais e não recebem `company_identifier`.

### 3.2 Catálogo efetivo da empresa

#### `company_permission_catalogs`

Uma linha por empresa, representando o estado de adoção do catálogo.

Campos mínimos:
- `identifier uuid PK`;
- `company_identifier uuid NOT NULL UNIQUE`;
- `adopted_global_version_identifier uuid NOT NULL`;
- `revision_number bigint NOT NULL`;
- `updated_at timestamptz NOT NULL`;
- `updated_by uuid NULL`.

Restrições:
- FK de `company_identifier` para `companies(identifier)`;
- FK para a versão global adotada;
- `UNIQUE(company_identifier, identifier)`;
- `revision_number` cresce a cada alteração efetiva do catálogo local.

#### `company_permissions`

Catálogo efetivo materializado de cada empresa. É a fonte consultada por `role_permissions` e `user_permissions`.

Campos mínimos:
- `identifier uuid PK`;
- `company_identifier uuid NOT NULL`;
- `permission_code text NOT NULL`;
- `entity text NOT NULL`;
- `field text NULL`;
- `action text NOT NULL`;
- `description text NULL`;
- `source_global_permission_identifier uuid NULL`;
- `origin text NOT NULL` — `global_copy` ou `company_custom`;
- `is_customized boolean NOT NULL`;
- `is_active boolean NOT NULL`;
- `created_at timestamptz NOT NULL`;
- `updated_at timestamptz NOT NULL`.

Restrições:
- `UNIQUE(company_identifier, identifier)`;
- `UNIQUE(company_identifier, permission_code)`;
- FK de `company_identifier` para `company_permission_catalogs(company_identifier)`, garantindo que toda permissão efetiva pertença a um catálogo criado para a empresa.
- quando `origin = 'global_copy'`, `source_global_permission_identifier` é obrigatório e referencia uma definição global;
- quando `origin = 'company_custom'`, `source_global_permission_identifier` é nulo;
- CHECK para coerência entre `origin` e `source_global_permission_identifier`.

Uma permissão copiada do catálogo global mantém o mesmo `permission_code`. Uma permissão criada exclusivamente pela empresa usa um código namespaceado, por exemplo `company.<codigo>`, sem colisão com os códigos globais.

A cópia local é a definição efetiva. A empresa pode personalizar descrição, ativação e atributos funcionais que forem liberados pela plataforma. A empresa não pode transformar uma definição em capacidade que a plataforma não disponibilizou, nem contornar limites globais de segurança ou de módulos.

### 3.3 Histórico e restauração

#### `company_permission_catalog_events`

Trilha append-only de operações de adoção, personalização, criação, desativação e restauração.

Campos mínimos:
- `identifier uuid PK`;
- `company_identifier uuid NOT NULL`;
- `catalog_identifier uuid NOT NULL`;
- `revision_number bigint NOT NULL`;
- `event_type text NOT NULL`;
- `from_global_version_identifier uuid NULL`;
- `to_global_version_identifier uuid NULL`;
- `actor_user_identifier uuid NULL`;
- `occurred_at timestamptz NOT NULL`;
- `details jsonb NOT NULL` — resumo estruturado da operação, sem substituir as definições relacionais.

Restrições:
- FKs compostas para o catálogo da mesma empresa;
- `UNIQUE(company_identifier, revision_number)`;
- UPDATE/DELETE bloqueados no fluxo operacional, conforme diretriz append-only da ADR-0016.

O histórico deverá registrar quais permissões foram criadas, alteradas, preservadas, desativadas ou restauradas em cada revisão. Para reconstrução integral, o evento deverá conter snapshots antes/depois das definições afetadas ou referências a revisões imutáveis equivalentes. O JSONB é trilha/snapshot, não fonte primária das permissões efetivas.

### 3.4 Atribuição a papéis e usuários

Manter:
- `roles`: papéis próprios de cada empresa;
- `role_permissions`: associação de papel a permissão efetiva da empresa;
- `user_permissions`: exceções por vínculo usuário–empresa.

Ajustes conceituais:
- `role_permissions.permission_identifier` referencia `company_permissions.identifier`;
- `user_permissions.permission_identifier` referencia `company_permissions.identifier`;
- ambas as tabelas mantêm `company_identifier NOT NULL`;
- FKs compostas devem garantir que papel, vínculo e permissão pertençam à mesma empresa;
- unicidade: `(company_identifier, role_identifier, permission_identifier)` e `(company_identifier, company_user_identifier, permission_identifier)`;
- `user_permissions.allow` deve ser `boolean NOT NULL`.

Não apontar essas associações diretamente para `global_permissions`: a autorização deve usar a permissão efetiva adotada/customizada pela empresa.

## 4. Fluxo de adoção e atualização

### Primeira adoção

1. Criar `company_permission_catalogs` para a empresa.
2. Registrar a versão global adotada.
3. Copiar todas as definições ativas daquela versão para `company_permissions`.
4. Registrar evento de adoção e revisão inicial.
5. Só então permitir associação de permissões aos papéis ou usuários.

### Adoção de nova versão global

Executar uma operação transacional e auditável:

1. comparar a versão atualmente adotada com a nova versão escolhida;
2. localizar permissões por `permission_code`;
3. inserir novas permissões globais;
4. atualizar cópias locais que não foram personalizadas;
5. preservar definições marcadas como personalizadas;
6. marcar como inativas/depreciadas as permissões removidas do padrão, sem apagar referências históricas;
7. preservar permissões exclusivas da empresa;
8. atualizar a versão adotada e incrementar `revision_number`;
9. registrar evento com resumo e snapshots das alterações.

Nenhuma adesão será automática. A empresa escolhe quando atualizar, conforme ADR-0011.

### Restauração

A empresa poderá restaurar uma permissão personalizada ao valor de uma versão global selecionada. A restauração:
- cria novo evento/revisão;
- não apaga o histórico da personalização;
- não altera versões globais;
- não altera outras empresas.

## 5. Semântica de autorização preservada

A avaliação efetiva de uma permissão seguirá:

1. vínculo usuário–empresa ativo e contexto de empresa validado;
2. módulo global disponível e módulo ativado para a empresa, quando aplicável;
3. se o usuário possuir papel `superuser`, aplicar as regras já aprovadas para esse papel, respeitando limites globais;
4. para usuário comum, uma permissão concedida por qualquer papel atribuído ao vínculo é suficiente (união);
5. `user_permissions.allow = false` nega a permissão e prevalece sobre concessões de papéis;
6. `user_permissions.allow = true` concede explicitamente;
7. sem exceção individual e sem concessão por papel, negar;
8. agentes nunca podem receber ou exercer `superuser`.

A implementação deverá evitar que uma permissão customizada conceda ações ou capacidades que a plataforma não disponibilizou globalmente.

## 6. Destino da tabela atual `permissions`

A tabela atual não materializa a separação exigida pela ADR-0011: possui `company_identifier NOT NULL` e campos `entity`, `field` e `action`, sem estrutura documentada de versões globais e adoção local.

Proposta:
- não tentar converter a tabela atual diretamente em catálogo global;
- inventariar seu registro e dependências;
- mapear registros para códigos estáveis;
- migrar para as novas estruturas somente após aprovação do desenho e validação dos vínculos;
- manter a tabela antiga durante a transição;
- removê-la apenas em etapa posterior, depois de comprovar ausência de dependências e concluir testes.

Na inspeção somente leitura de 2026-11-08:
- `permissions`: 1 registro;
- `role_permissions`: 0 registros;
- `user_permissions`: 0 registros;
- `roles`: 3 registros.

Esses números são baseline observado, não autorização para excluir ou reclassificar dados.

## 7. Regras multiempresa e índices

Todas as tabelas de catálogo local e histórico recebem `company_identifier NOT NULL`. Exceção: as tabelas do catálogo global não pertencem a uma empresa.

Índices e constraints mínimos:
- `company_permission_catalogs(company_identifier)` UNIQUE;
- `company_permissions(company_identifier, permission_code)` UNIQUE;
- `company_permissions(company_identifier, identifier)` UNIQUE;
- `company_permission_catalog_events(company_identifier, revision_number)` UNIQUE;
- FKs compostas de `role_permissions` e `user_permissions` para `company_permissions`;
- FKs compostas das associações para `roles` e `company_users`;
- índices de consulta por empresa, código, papel e vínculo.

RLS deverá ser especificada na Parte B, com negação por padrão. Esta proposta não define policies nem contrato de contexto transacional.

## 8. Migração e gates

Antes de migration:
1. aprovar esta proposta;
2. inventariar constraints, índices, triggers, views, policies e funções que referenciam `permissions`;
3. verificar dependências de `role_permissions` e `user_permissions`;
4. definir códigos estáveis para as permissões;
5. validar a origem e a semântica do registro atual;
6. criar baseline e migrations versionadas no repositório de implementação;
7. testar em ambiente restaurável;
8. validar união, negação individual, concessão individual, superuser, customização, adoção e restauração;
9. só depois autorizar execução específica no Supabase.

A aprovação desta proposta não autoriza DDL, DML, alteração de dados, RLS, grants ou remoção de tabelas.

## 9. Decisões não tomadas por esta proposta

- Matriz completa de permissões por módulo.
- Matriz completa de permissões por módulo e inventário dos códigos necessários além da convenção já aprovada.
- Quais atributos de uma permissão a empresa pode personalizar.
- Política detalhada para permissões removidas do catálogo global.
- Interface e fluxo de aprovação de adoção/restauração.
- Implementação da resolução de permissões no backend/API.
- Contrato de contexto transacional e policies RLS.

Esses itens não devem ser presumidos como aprovados.

### 9.1 Convenção aprovada para códigos estáveis

Em 2026-11-08, o responsável aprovou a convenção semântica `entidade.ação` para `permission_code`, em minúsculas e com identificadores estáveis independentes dos nomes físicos das tabelas. Exemplos: `customer.view`, `customer.create`, `customer.update`, `visit.view`, `visit.create` e `visit.cancel`.

O registro legado atual `entity=customer`, `field=NULL`, `action=view` deverá ser mapeado para `customer.view`, sujeito à validação no plano de migração. A convenção não define, por si só, a matriz completa de permissões nem aprova a criação de todos os exemplos. Mudança semântica incompatível exige novo código; alterações de descrição ou de implementação física não devem renomear um código estável.

## 10. Referências

- `documentacao/decisoes/ADR-0011-catalogo-permissoes-global-versionado.md`
- `documentacao/decisoes/ADR-0017-tenancy-compartilhada-autorizacao-multiempresa.md`
- `documentacao/MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md`
- `documentacao/ESPECIFICACAO_FISICA_FUNDACAO_MULTIEMPRESA_MVP.md`
- `documentacao/relatorios/PLANO_MIGRACAO_FUNDACAO_MULTIEMPRESA_PARTE_A_MVP.md`

## 11. Auditoria somente leitura do schema atual — 2026-11-08

A inspeção do projeto Supabase `voaovwphncwbrwtcqnsc` (PostgreSQL 17.6), limitada às tabelas `permissions`, `role_permissions` e `user_permissions`, confirmou:

- Contagens: `permissions` = 1; `role_permissions` = 0; `user_permissions` = 0.
- O único registro de `permissions` pertence à Winservice e representa `entity=customer`, `field=NULL`, `action=view`, descrição “Permissão para visualizar todos os dados de clientes.”. Ainda não possui `permission_code`; sua classificação/mapeamento para código estável permanece pendente.
- `role_permissions.permission_identifier` e `user_permissions.permission_identifier` possuem FKs simples para `permissions(identifier)`, sem garantia composta de tenant.
- `role_permissions.role_identifier` referencia `roles(identifier)` por FK simples; `user_permissions.company_user_identifier` referencia `company_users(identifier)` por FK simples.
- As três tabelas possuem `company_identifier NOT NULL`, mas suas FKs atuais para `companies(identifier)` são simples. A auditoria não identificou FKs compostas por empresa nesses relacionamentos.
- `user_permissions.allow` permite NULL, divergindo do modelo aprovado que exige `boolean NOT NULL`.
- RLS está habilitado nas três tabelas, `FORCE ROW LEVEL SECURITY` está desabilitado e a consulta a `pg_policies` não retornou policies para essas tabelas. Portanto, não há policies próprias identificadas nesta inspeção.
- Não foram encontrados triggers não internos nas três tabelas. A busca textual em funções `public` não retornou funções que mencionem esses nomes; isso não substitui busca no código da aplicação, workflows ou integrações.
- Constraints e índices atuais são majoritariamente PK/UNIQUE por `identifier` e FKs individuais. Não há unicidade tenant-scoped de associação documentada no schema consultado.

Esta auditoria é somente leitura. Não foram alterados dados, constraints, policies ou estruturas. Os resultados são um retrato do ambiente consultado em 2026-11-08 e deverão ser revalidados antes de qualquer migration.

## 12. Aprovação e limites

Em 2026-11-08, o responsável aprovou o modelo físico descrito nesta proposta, incluindo catálogo global versionado, catálogo efetivo materializado por empresa, adoção voluntária, preservação de customizações, histórico/restauração e referências das permissões efetivas por `role_permissions` e `user_permissions`.

A aprovação do modelo físico não autoriza implementação. A convenção `entidade.ação` para códigos estáveis foi aprovada separadamente em 2026-11-08 e está registrada na seção 9.1. Permanecem obrigatórios o inventário de dependências, o mapeamento validado dos registros existentes, a identificação do repositório/baseline de migrations e autorização operacional específica antes de qualquer alteração no Supabase.

## Histórico de alterações

- 2026-11-08 — v0.4.0: registro da aprovação da convenção estável `entidade.ação`; mapeamento proposto do registro legado `customer/view` para `customer.view`, condicionado à validação da migration.
- 2026-11-08 — v0.3.0: inclusão da auditoria somente leitura das tabelas e dependências atuais de permissões; aprovação do modelo preservada.
- 2026-11-08 — v0.2.0: modelo físico aprovado pelo responsável; limites de implementação e decisões ainda pendentes preservados.
- 2026-11-08 — v0.1.0: proposta inicial do modelo físico do catálogo global versionado e catálogo efetivo customizável por empresa.
