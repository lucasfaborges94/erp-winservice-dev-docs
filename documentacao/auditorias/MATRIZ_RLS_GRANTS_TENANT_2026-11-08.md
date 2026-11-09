# Matriz inicial de RLS, grants e tenant — Supabase — 2026-11-08

**Projeto:** `voaovwphncwbrwtcqnsc`  
**Schema:** `public`  
**Tipo:** inspeção somente leitura de catálogos PostgreSQL  
**Estado:** fotografia inicial; requer revisão semântica por entidade.

## 1. Resumo

- 310 tabelas base no schema public.
- RLS habilitado em 38/38; FORCE ROW LEVEL SECURITY desabilitado em 38/38.
- Grants explícitos para `anon` e `authenticated` foram observados em 38/310 tabelas. O conjunto de privilégios concedidos inclui DELETE, INSERT, REFERENCES, SELECT, TRIGGER, TRUNCATE e UPDATE.
- 26 tabelas sem policy.
- 12 tabelas com uma ou mais policies.
- 10 tabelas com pelo menos uma policy contendo predicado literal `true` em USING ou WITH CHECK; em `users`, o predicado true pertence à policy de INSERT, enquanto SELECT permanece limitado ao próprio usuário.
- 23 tabelas sem a coluna `company_identifier` na captura atual; isso inclui entidades operacionais importantes como `visits`, `work_orders`, `equipments` e `quotations`, cuja tenancy deverá ser resolvida conforme o plano Parte A antes da liberação multiempresa.
- `company_user_roles` não possui atualmente `company_identifier`, embora a ADR-0018 exija tenant e FKs compostas na migração.

**Interpretação:** grants permitem que os papéis alcancem os objetos pela Data API, mas RLS determina quais linhas podem ser acessadas. O grant amplo não significa, sozinho, que RLS esteja sendo ignorado. Entretanto, policies permissivas com `true` não fornecem isolamento por tenant; tabelas sem policy tendem a negar acesso para papéis sujeitos a RLS. Funções SECURITY DEFINER e owners/bypass RLS precisam ser avaliados separadamente.

## 2. Matriz por tabela

Todas as linhas abaixo têm RLS habilitado, FORCE RLS desabilitado e grants explícitos para anon/authenticated com o conjunto de privilégios listado no resumo.

| Tabela | company_identifier | Policies | Comandos cobertos | Predicado literal true |
|---|---:|---:|---|---:|
| `a_blank_template` | sim | 0 | — | não |
| `addresses` | sim | 3 | INSERT, SELECT, UPDATE | sim |
| `approval_events` | sim | 0 | — | não |
| `companies` | não | 1 | ALL | sim |
| `company_modules` | sim | 0 | — | não |
| `company_user_roles` | não | 0 | — | não |
| `company_users` | sim | 1 | SELECT | não |
| `contact_channels` | não | 0 | — | não |
| `customer_contacts` | sim | 2 | INSERT, SELECT | sim |
| `customers` | sim | 2 | INSERT, SELECT | sim |
| `documents` | não | 0 | — | não |
| `entity_status` | sim | 3 | INSERT, SELECT, UPDATE | sim |
| `entity_types` | não | 3 | INSERT, SELECT, UPDATE | sim |
| `equipments` | não | 0 | — | não |
| `expenses` | sim | 1 | SELECT | não |
| `integration_sync_queue` | sim | 0 | — | não |
| `integration_webhooks` | não | 0 | — | não |
| `integrations` | sim | 0 | — | não |
| `modules` | não | 1 | SELECT | sim |
| `permissions` | sim | 0 | — | não |
| `phonenumbers` | não | 0 | — | não |
| `quotations` | não | 0 | — | não |
| `role_modules` | não | 0 | — | não |
| `role_permissions` | sim | 0 | — | não |
| `role_screens` | não | 0 | — | não |
| `roles` | sim | 0 | — | não |
| `screens` | não | 1 | SELECT | sim |
| `task_types` | não | 0 | — | não |
| `user_modules` | não | 0 | — | não |
| `user_permissions` | sim | 0 | — | não |
| `user_screens` | não | 0 | — | não |
| `users` | não | 2 | INSERT, SELECT | sim* |
| `visit_requirements` | não | 0 | — | não |
| `visit_type_requirement` | não | 0 | — | não |
| `visit_types` | não | 0 | — | não |
| `visits` | não | 3 | INSERT, SELECT, UPDATE | sim |
| `work_order_equipment` | não | 0 | — | não |
| `work_orders` | não | 0 | — | não |

`users` possui predicado literal true na policy de INSERT, mas sua policy SELECT observada restringe a leitura ao próprio identificador autenticado. Não interpretar o indicador como SELECT irrestrito nessa tabela.

## 3. Prioridades para o desenho de autorização

1. Corrigir o desenho das policies de tabelas tenant-scoped com predicates true, começando por clientes, contatos, endereços, visitas e empresas.
2. Definir classificação global/tenant/infraestrutura para tabelas sem `company_identifier`; ausência da coluna não significa automaticamente erro, pois catálogos globais podem ser intencionais.
3. Aplicar o plano de fundação multiempresa: backfill, FKs compostas e constraints tenant-scoped antes de implementar policies definitivas.
4. Definir grants mínimos por papel e objeto; retirar privilégios não necessários somente após validar consumidores existentes.
5. Projetar policies para `company_user_roles`, `roles`, `permissions`, `role_permissions` e `user_permissions` coerentes com ADR-0017/0018 e o catálogo aprovado.
6. Testar acesso cruzado com identidades e vínculos de duas empresas em ambiente restaurável.
7. Não criar policy genérica para todas as tabelas: permissões de leitura/escrita e classificação variam por domínio.

## 4. Limitações

- A matriz descreve catálogos de RLS, grants e presença de coluna; não testa comportamento por sessão.
- Não consultou dados de linhas, payloads, segredos ou conteúdo de documentos.
- Não valida a semântica funcional de cada tabela nem os consumidores externos.
- Não substitui dump SQL restaurável nem ensaio de restauração.
- Snapshot realizado em 2026-11-09T01:44Z; consultar novamente antes de qualquer alteração.

**Limites:** nenhuma policy, grant, coluna ou dado foi alterado. Esta matriz não autoriza DDL/DML.
