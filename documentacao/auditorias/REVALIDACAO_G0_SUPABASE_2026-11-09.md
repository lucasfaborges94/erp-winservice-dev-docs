# Revalidação G0 — estado atual do Supabase — 2026-11-09

**Projeto:** Winservice (`voaovwphncwbrwtcqnsc`)  
**Região:** `sa-east-1`  
**Estado do projeto:** `ACTIVE_HEALTHY`  
**PostgreSQL:** `17.6.1.141`  
**Tipo:** inspeção somente leitura  
**Referências:** ADR-0017, ADR-0018, ADR-0019 e `ESPECIFICACAO_TECNICA_AUTENTICACAO_CONTEXTO_RLS.md`.

## 1. Escopo executado

Foram consultados os metadados do projeto, a lista de migrations, as tabelas do schema `public` e os Supabase Security Advisors. Não foram executados DDL/DML, migrations, alterações de grants/policies/funções, chamadas RPC de negócio, leituras de payloads ou operações de deploy.

## 2. Evidências atuais

- Projeto `Winservice` em estado `ACTIVE_HEALTHY`, região `sa-east-1`, PostgreSQL `17.6.1.141`.
- A integração retornou **zero migrations** registradas.
- A listagem retornou **38 tabelas** no schema `public`; todas aparecem com RLS habilitado.
- O Security Advisor identificou **26 tabelas com RLS habilitado e nenhuma policy**. Ausência de policy normalmente resulta em negação por padrão para roles sujeitas a RLS; isso não deve ser classificado automaticamente como vazamento. É necessário avaliar grants, funções privilegiadas, views e caminhos de serviço por tabela.
- O Advisor sinalizou **7 funções com search_path mutável**: `set_updated_fields`, `set_created_by`, `ensure_primary_address`, `set_created_fields`, `set_visit_address_snapshot`, `update_updated_at` e `prevent_changes`.
- O Advisor sinalizou **2 funções SECURITY DEFINER executáveis por anon e authenticated**: `public.authorize_integration_sync_event(...)` e `public.can_view_expense(uuid)`. A primeira está relacionada a integração externa, fora do MVP; a segunda é legada e depende de `company_users.role_identifier`, campo que a ADR-0018 proíbe usar como fonte de papéis. Ambas permanecem achados para tratamento coordenado; nenhuma permissão foi alterada.
- O Auth Advisor sinalizou **proteção contra senhas vazadas desabilitada**. Nenhuma configuração foi alterada.

## 3. Interpretação e limitações

1. A fotografia confirma que não há migrations registradas pela integração, mas não produz por si só um baseline SQL restaurável.
2. RLS habilitado não prova isolamento correto. As 26 tabelas sem policy podem estar efetivamente bloqueadas para roles comuns, mas grants e funções privilegiadas precisam ser avaliados em conjunto.
3. Os achados do Advisor são triagem automatizada; não foram executados testes de exploração, simulação multiempresa ou chamadas às funções sinalizadas.
4. A configuração/comportamento do pooler, os parâmetros seguros de conexão, a existência de backup restaurável e o ensaio de restauração não foram verificados nesta revalidação.
5. Nenhuma alteração foi feita no banco, Auth, policies, grants, funções, dados ou configuração do projeto.

## 4. Estado dos gates

- **G0 — PARCIAL / NÃO CONCLUÍDO:** estado do projeto, inventário resumido de tabelas, RLS, migrations e Advisors foram reconsultados. Permanecem pendentes backup restaurável, baseline SQL reconciliada, evidência de configuração/compatibilidade do pooler e ensaio de restauração em ambiente não produtivo.
- **G1 — PARCIAL / NÃO CONCLUÍDO:** a especificação técnica registra correspondência estrutural Auth→ERP, mas a validação JWT ponta a ponta na API e o fluxo de provisionamento ainda não foram comprovados.
- **G2–G5 — NÃO INICIADOS:** dependem dos gates anteriores e da prova técnica descrita na especificação.

## 5. Próxima ação

Obter um backup SQL restaurável do projeto e realizar ensaio de restauração em ambiente isolado, sem alterar o projeto principal. Em paralelo, preparar a baseline versionada reconciliada com o schema real e documentar a configuração do pooler sem registrar credenciais. Somente depois de evidenciar G0, avançar para a prova G1 no backend.

**Este relatório não autoriza DDL/DML, migrations, grants, policies, funções, alterações de Auth ou deploy.**
