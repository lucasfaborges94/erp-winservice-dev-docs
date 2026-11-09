# Auditoria de segurança Supabase — triagem inicial — 2026-11-08

**Projeto:** `voaovwphncwbrwtcqnsc` (Winservice)  
**Schema:** `public`  
**PostgreSQL observado:** 17.6.1.141  
**Tipo:** inspeção somente leitura  
**Estado:** triagem inicial; não é pentest nem comprovação de exploração.

## 1. Escopo e método

Foram consultados novamente os Supabase Security Advisors, definições de funções no catálogo PostgreSQL e policies do schema `public`. As consultas não executaram DDL/DML, não chamaram RPCs de negócio e não leram payloads da fila ou segredos de integrações.

Referências arquiteturais: ADR-0017 (tenancy compartilhada e RLS/backend complementares), ADR-0018 (fonte única de papéis) e modelo de autorização multiempresa.

## 2. Achados prioritários

### SEC-01 — RPC SECURITY DEFINER executável por anon e authenticated

**Objeto:** `public.authorize_integration_sync_event(uuid,text,text,text,jsonb,text)`  
**Severidade de triagem:** ALTA — requer validação urgente do contrato do webhook.

Evidências observadas:
- função `SECURITY DEFINER`, proprietária `postgres`;
- EXECUTE efetivo para `anon`, `authenticated`, `service_role` e `postgres`;
- publicada no schema `public`, portanto exposta como RPC pela Data API;
- aceita `p_integration_identifier`, tipo de entidade, operação, identificador externo, payload JSON e direção;
- valida existência da integração, status `active` e provedor `auvo`;
- não foi identificada no corpo consultado validação de assinatura HMAC, segredo do webhook, token ou identidade autenticada do chamador;
- insere o payload recebido em `integration_sync_queue` e retorna identificadores, empresa e chave de idempotência.

**Risco:** se um terceiro obtiver/conseguir inferir o UUID de uma integração ativa, poderá potencialmente enfileirar payloads arbitrários no contexto daquela integração, contornando RLS pela execução como proprietário. A consulta não testou a chamada, não leu UUIDs de integração e não comprova exploração; o risco decorre do contrato visível da função e dos privilégios.

**Ação recomendada para especificação:** confirmar o contrato do webhook Auvo e o mecanismo de autenticação esperado. Definir se a função deve ser privada/invocável apenas por backend confiável ou se deve validar criptograficamente o webhook antes de aceitar o evento. Revisar retorno de metadados, rate limiting, replay/idempotência e grants. Não revogar EXECUTE nem alterar o endpoint automaticamente, pois isso pode interromper integração operacional.

### SEC-02 — Policies RLS não implementam isolamento multiempresa

Foram observadas policies permissivas para `authenticated` com predicados amplos `USING (true)` e/ou `WITH CHECK (true)`, incluindo:
- `customers`: SELECT e INSERT;
- `addresses`: SELECT, INSERT e UPDATE;
- `customer_contacts`: SELECT e INSERT;
- `visits`: SELECT, INSERT e UPDATE;
- `companies`: policy `ALL` com `USING (true)`;
- `entity_status` e `entity_types`: SELECT, INSERT e UPDATE;
- `modules` e `screens`: SELECT irrestrito para authenticated.

Essas policies não verificam vínculo ativo, empresa selecionada, papel ou permissão. Para dados tenant-scoped, isso diverge diretamente dos requisitos da ADR-0017. A inspeção não simulou sessão de usuário nem fez leitura de linhas via Data API; portanto o achado é sobre a definição das policies, não uma medição de dados efetivamente extraídos.

**Risco:** usuários autenticados podem receber acesso entre empresas ou operações não autorizadas, conforme grants e exposição das tabelas. A policy `ALL` em `companies` é especialmente ampla.

**Ação recomendada:** classificar cada tabela como global, tenant-scoped ou infraestrutura; definir contexto de empresa validado no backend; projetar policies com vínculo ativo e autorização, além de FKs compostas. Não substituir todas as policies por um template genérico.

### SEC-03 — função legada de despesas

`public.can_view_expense(uuid)` permanece SECURITY DEFINER, executável por `anon` e `authenticated`, e consulta `company_users.role_identifier`. A ADR-0018 e a auditoria vigente classificam a função como obsoleta e fora do MVP, reservada ao futuro Financeiro. Não deve ser reutilizada como modelo de autorização. Sua exposição e dependência física permanecem registradas para tratamento no momento apropriado, sem bloquear o desenho geral atual.

## 3. Outros achados

### SEC-04 — 26 tabelas com RLS habilitado e sem policy

O Advisor retornou 26 tabelas nessa condição. Para papéis sujeitos a RLS, ausência de policy normalmente resulta em negação por padrão; não classificar automaticamente como vazamento. Verificar caso a caso grants, funções SECURITY DEFINER, views e caminhos de serviço.

Lista observada: `a_blank_template`, `approval_events`, `company_modules`, `company_user_roles`, `contact_channels`, `documents`, `equipments`, `integration_sync_queue`, `integration_webhooks`, `integrations`, `permissions`, `phonenumbers`, `quotations`, `role_modules`, `role_permissions`, `role_screens`, `roles`, `task_types`, `user_modules`, `user_permissions`, `user_screens`, `visit_requirements`, `visit_type_requirement`, `visit_types`, `work_order_equipment`, `work_orders`.

### SEC-05 — sete funções com search_path mutável

O Advisor sinalizou `set_updated_fields`, `set_created_by`, `ensure_primary_address`, `set_created_fields`, `set_visit_address_snapshot`, `update_updated_at` e `prevent_changes`. O catálogo confirmou ausência de configuração fixa de search_path nessas funções. A função `authorize_integration_sync_event` tem configuração explícita; `can_view_expense` também tem configuração explícita.

A correção deve ser feita com análise de resolução de objetos e qualificação de referências, principalmente em funções acionadas por triggers. Não aplicar alteração em lote sem teste.

### SEC-06 — proteção contra senhas vazadas desabilitada

O Auth Advisor confirmou a configuração desabilitada. Habilitação é recomendada, mas depende de avaliar impacto operacional e política de autenticação; não foi alterada.

## 4. Limitações

- Nenhuma tentativa de exploração ou chamada RPC foi executada.
- Nenhuma linha de clientes, visitas, payloads de integração ou segredo foi consultada nesta triagem.
- Não foram testadas sessões reais de usuários de empresas distintas.
- Não foi obtido dump SQL restaurável; o inventário JSON versionado continua sendo apenas fotografia estrutural.
- O resultado dos Advisors é fotografia da execução em 2026-11-09T01:41:32Z; reexecutar antes de qualquer correção.

## 5. Próximas ações propostas

1. Priorizar revisão do contrato de autenticação do webhook/RPC `authorize_integration_sync_event`.
2. Produzir matriz tabela × classificação (global/tenant/infraestrutura) × operação × ator.
3. Especificar o contrato de contexto da empresa e as policies alinhadas à ADR-0017.
4. Definir estratégia de grants mínimos e de funções privilegiadas, considerando que RLS não controla EXECUTE de funções.
5. Avaliar a configuração de proteção contra senhas vazadas.
6. Corrigir search_path individualmente após análise de dependências.
7. Obter dump SQL restaurável e ambiente de ensaio antes de qualquer migration/correção.

**Limites:** nenhuma policy, função, grant, tabela, dado ou configuração do Supabase foi alterada. Este relatório registra evidências e recomendações, não autoriza execução.
