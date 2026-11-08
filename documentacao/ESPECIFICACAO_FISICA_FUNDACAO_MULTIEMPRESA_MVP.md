# Especificação física proposta — Fundação multiempresa e isolamento do MVP

**Versão:** 0.1.0  
**Data:** 2026-11-08  
**Estado:** PROPOSTA TÉCNICA COMPLETA PARA APROVAÇÃO — NÃO APROVADA  
**Escopo:** modelo físico lógico-relacional, tenancy, integridade, autorização, RLS, backend e migração  
**Ambiente auditado:** Supabase Winservice, projeto `voaovwphncwbrwtcqnsc`, schema `public`  
**Natureza:** especificação para deliberação; não é migration nem autorização para executar DDL.

> **Regra de segurança:** nenhum DDL, grant, policy, função, trigger, migration ou dado foi alterado por este documento. A aprovação desta proposta deverá ser explícita e não autoriza automaticamente a execução no Supabase. A execução será apresentada em plano/migrations revisáveis, com backup, validações e janela de implantação.

## 1. Objetivo e resultado esperado

Consolidar em uma única especificação a fundação física necessária para que o MVP opere em banco e schema compartilhados, com isolamento efetivo por empresa, autorização consistente entre backend e PostgreSQL, integridade referencial entre tenants e trilha auditável.

A diretriz expressa do responsável é que **`company_identifier` esteja em todas as tabelas em que fizer sentido**, evitando depender de herança indireta de tenant quando a redundância controlada puder tornar a integridade e as policies mais simples e verificáveis. Esta proposta adota essa diretriz como padrão: toda tabela que contenha dados pertencentes a uma empresa terá `company_identifier NOT NULL`, inclusive tabelas filhas e tabelas de associação. A coluna só será omitida em identidades globais, cadastro de empresas, catálogos globais da plataforma e infraestrutura cuja titularidade seja genuinamente global ou externa ao tenant.

A coluna isoladamente não garante isolamento. A proteção depende conjuntamente de:
- chaves estrangeiras compostas que impeçam relações entre empresas diferentes;
- RLS com negação por padrão e validação de vínculo/contexto;
- backend/API que autentique, resolva usuário e empresa ativa e verifique permissões;
- grants mínimos e funções privilegiadas estritamente controladas;
- testes negativos entre duas empresas e testes de bypass;
- auditoria e monitoramento.

## 2. Autoridade, decisões vigentes e limites

Esta proposta respeita:
- **ADR-0017 (APROVADA):** PostgreSQL e schema compartilhados; isolamento lógico por empresa; usuário pode ter vínculos com várias empresas; seleção explícita de empresa quando houver múltiplos vínculos ativos; RLS e backend como barreiras complementares; integridade entre tenants por constraints e relacionamentos.
- **ADR-0011 (APROVADA):** catálogo padrão global de permissões versionado; empresas adotam voluntariamente versões; personalizações locais preservadas, rastreadas e restauráveis.
- **ADR-0016 (APROVADA):** histórico append-only para alterações relevantes; eventos não são sobrescritos/excluídos no fluxo operacional.
- **ADR-0012:** limites da Isa e de agentes; agentes não recebem privilégios de superuser.
- Modelo de autorização multiempresa v0.3.1: regras conceituais de vínculo, papéis, exceções e negação por padrão, ainda marcado como proposta.
- Modelo lógico Operação v0.7.1: proposta lógica, não modelo físico aprovado.
- Inventário incremental de escopo: itens `FORA_MVP`, `FUTURO`, `OBSOLETO_SUBSTITUÍDO` e `PENDENTE_CLASSIFICAÇÃO` não podem ser reintroduzidos implicitamente.

### 2.1 O que esta proposta não decide

Não altera ADRs. Não define o modelo completo de sessões por aplicação, exclusividade do app técnico, titularidade `account_owner`, papéis de negócio ainda em avaliação, catálogo universal de todos os domínios, nem o desenho de integrações fora do MVP. Esses assuntos permanecem em brainstorm ou documentos especializados.

Não declara que o estado atual do banco esteja seguro. A auditoria de 2026-11-08 registrou policies permissivas, tabelas sem policies, grants amplos, inconsistências de tenant e divergências de identidade. Esses achados são conhecidos e constituem trabalho de remediação, não novidades desta especificação.

## 3. Convenções físicas propostas

### 3.1 Identificadores e colunas comuns

- PKs de entidades de negócio: `identifier uuid PRIMARY KEY DEFAULT gen_random_uuid()`, salvo tabelas de associação cuja PK composta seja mais adequada.
- Tenant: `company_identifier uuid NOT NULL` em toda tabela tenant-scoped.
- Referências entre tabelas tenant-scoped: FK composta `(company_identifier, <fk_identifier>) REFERENCES <tabela>(company_identifier, identifier)`.
- Para suportar FK composta, toda tabela tenant-scoped terá `UNIQUE (company_identifier, identifier)`, mesmo que `identifier` já seja PK global.
- Datas: `created_at timestamptz NOT NULL DEFAULT now()`, `updated_at timestamptz`; timestamps de eventos distinguem tempo do fato e tempo de registro quando aplicável.
- Autoria: `created_by` e `updated_by` referenciam `public.users(identifier)` quando o ator for usuário ERP. Ator de sistema/serviço deverá ser modelado explicitamente, não forçado a um UUID de usuário humano.
- Exclusão: evitar cascatas destrutivas em registros de negócio/histórico. Usar RESTRICT/NO ACTION; cascata apenas para associações puramente dependentes e sem valor histórico, após revisão.
- Status e códigos: NOT NULL quando obrigatórios, com CHECK ou FK para catálogo versionado conforme domínio; não usar tabelas genéricas de status sem contrato específico.
- Soft delete não substitui autorização nem política de retenção.

### 3.2 Quando `company_identifier` não deve existir

| Categoria | Justificativa |
|---|---|
| `auth.users` | Identidade do provedor de autenticação; não pertence a uma empresa única. |
| `public.users` | Perfil global de uma identidade ERP que pode ter vínculos com várias empresas. O tenant é determinado por `company_users`, não pelo perfil. |
| `public.companies` | É a própria raiz do tenant; não pode apontar para outra empresa. |
| Catálogo mestre global de permissões e versões publicadas | São artefatos da plataforma, compartilhados e versionados conforme ADR-0011. |
| Catálogo global de papéis padrão, se vier a existir | Somente se for efetivamente mantido pela plataforma e separado das atribuições locais. |
| Infraestrutura global de autenticação/telemetria sem conteúdo empresarial | Deve possuir escopo técnico próprio e não pode armazenar payload de negócio sem tenant. |

Em todas as outras tabelas que armazenem dados empresariais, relações, eventos ou configurações locais, a proposta é incluir `company_identifier` diretamente, mesmo que seja possível inferi-lo por uma FK.

### 3.3 FKs compostas — regra obrigatória

Exemplo conceitual:
```sql
ALTER TABLE public.work_orders
  ADD CONSTRAINT work_orders_company_identifier_identifier_key
  UNIQUE (company_identifier, identifier);

ALTER TABLE public.visits
  ADD CONSTRAINT visits_work_order_same_company_fk
  FOREIGN KEY (company_identifier, work_order_identifier)
  REFERENCES public.work_orders (company_identifier, identifier);
```

O exemplo é ilustrativo; não deve ser executado antes da aprovação e da reconciliação de dados. FK composta não substitui RLS. Índices devem acompanhar FKs e consultas por tenant.

## 4. Contexto confiável de empresa e identidade

### 4.1 Identidade

Manter a separação aprovada pela ADR-0017:
- `auth.users.id`: identidade do Supabase Auth.
- `public.users.identifier`: ID interno do usuário ERP.
- `public.users.auth_user_identifier`: FK única para `auth.users.id`.

A associação `auth_user_identifier` deverá ser UNIQUE e NOT NULL para usuários humanos autenticáveis, com política explícita para agentes/identidades técnicas que não façam login interativo. Não usar `users.identifier = auth.uid()` como pressuposto.

### 4.2 Empresa ativa

A empresa ativa não será confiada a um `company_identifier` arbitrário enviado pelo cliente nem a uma variável persistente que o cliente possa alterar sem validação.

Fluxo requerido:
1. obter identidade por `auth.uid()`;
2. localizar exatamente um perfil ERP elegível;
3. receber a empresa desejada;
4. validar empresa ativa e vínculo ativo em `company_users`;
5. resolver papéis/permissões do vínculo;
6. executar operação dentro de contexto transacional validado;
7. RLS revalidar a identidade e a associação.

**Mecanismo de transporte do contexto:** proposta preferencial é backend/API validar o vínculo e estabelecer contexto transacional confiável para a operação; não se recomenda confiar em custom claim mutável pelo cliente ou em `SET app.company_id` sem proteção contra spoofing. A escolha final da implementação do contexto SQL (claims assinados pelo servidor, função de contexto ou conexão/role de backend) deve ser validada com a stack real antes do DDL. Em nenhuma hipótese o cliente terá credencial `service_role`.

### 4.3 Usuários com múltiplas empresas

- zero vínculos ativos: negar acesso operacional;
- um vínculo ativo: pode selecionar automaticamente;
- mais de um vínculo ativo: exigir seleção explícita;
- troca de empresa: invalidar caches, queries e autorizações do tenant anterior e validar novamente;
- suspender vínculo: negar novas operações imediatamente após a confirmação transacional.

## 5. Modelo físico proposto — tabelas de identidade e autorização

### 5.1 `public.users` — perfil ERP global

**Tenant:** sem `company_identifier`, pois o mesmo usuário pode pertencer a várias empresas.  
**Estado atual:** existe; auditoria encontrou 5 registros.  
**Campos propostos/reconciliados:**
- `identifier uuid PK`;
- `auth_user_identifier uuid UNIQUE NULL/NOT NULL conforme tipo`, FK para `auth.users(id)`;
- `identity_type text NOT NULL` com valores controlados `human` e `agent`;
- `name`, `email` e demais atributos globais mínimos;
- `status` controlado;
- `created_at`, `updated_at`.
**Restrições:** identidade Auth única; não usar email como chave; agentes não podem possuir papel `superuser`; perfil global não concede acesso a nenhuma empresa. Para humanos com login Supabase, `auth_user_identifier` obrigatório. Para agentes sem login interativo, a forma de autenticação técnica permanece dependente da ADR-0012 e do desenho de backend.
**Migração:** mapear `identifier` e `auth_user_identifier` existentes; validar 1:1; não reescrever IDs nem presumir equivalência.

### 5.2 `public.companies` — raiz do tenant

**Tenant:** sem `company_identifier`; `identifier` é o identificador do tenant.  
**Estado atual:** existe; 1 registro.  
**Campos:** `identifier`, nome/identificação legal, `is_active`, timestamps e campos administrativos estritamente necessários.
**Restrições:** ativação/desativação controlada; não permitir que usuários comuns criem ou alterem parâmetros globais da plataforma. Acesso a dados de uma empresa deve ser validado por vínculo, inclusive para leitura de seu próprio cadastro.
**RLS:** acesso limitado a empresas nas quais a identidade possua vínculo ativo; operações administrativas SaaS separadas e fora do papel `superuser`.

### 5.3 `public.company_users` — vínculo usuário–empresa

**Tenant:** `company_identifier NOT NULL`.  
**Estado atual:** existe; 1 vínculo.  
**Campos:** `identifier`, `company_identifier`, `user_identifier`, `status`, `created_at`, `updated_at`, `created_by`, `suspended_at`, `suspended_by` quando aplicável.
**Restrições:**
- UNIQUE `(company_identifier, user_identifier)` para um vínculo por usuário/empresa;
- UNIQUE `(company_identifier, identifier)`;
- FK composta para `companies(identifier)` (a raiz não precisa da chave tenant composta);
- FK `user_identifier -> users(identifier)`;
- vínculo suspenso/inativo não autoriza operações;
- transição de estado auditável.
**Remover/reconciliar:** `company_admin` não é role nem concede privilégio SaaS; preservar apenas se necessário para designação comercial, com semântica separada e sem uso no autorizador. `role_identifier` legado não pode continuar como fonte de verdade após migração; não apagar antes de reconciliar.
**RLS:** o usuário pode consultar seus vínculos; escrita somente por fluxo de administração autorizado. O cliente não pode criar vínculo próprio.

### 5.4 `public.company_user_roles` — papéis atribuídos ao vínculo

**Tenant:** `company_identifier NOT NULL`.  
**Estado atual:** existe; 2 associações.  
**Campos:** `identifier` ou PK composta, `company_identifier`, `company_user_identifier`, `role_identifier`, `assigned_at`, `assigned_by`.
**Restrições:** UNIQUE `(company_identifier, company_user_identifier, role_identifier)`; FKs compostas para vínculo e papel; impedir associação de papel de outra empresa; agentes não podem receber papel `superuser`.
**RLS:** leitura por membros autorizados do tenant; escrita exige permissão de administração de usuários/papéis.

### 5.5 `public.roles` — papéis da empresa

**Tenant:** `company_identifier NOT NULL` para papéis locais.  
**Estado atual:** existe; 3 registros; `company_identifier` nullable.  
**Proposta:** separar papéis locais da empresa de eventuais templates globais. Para MVP, papéis efetivamente atribuíveis devem ser registros pertencentes à empresa. Se houver templates globais, manter catálogo separado e versionado, sem atribuir diretamente um papel global mutável.
**Campos:** `identifier`, `company_identifier`, `code`, `name`, `description`, `is_system`, timestamps.
**Restrições:** UNIQUE `(company_identifier, code)`; UNIQUE `(company_identifier, identifier)`; códigos protegidos para papéis reservados; não permitir que empresa crie papel `superuser` com privilégios além do contrato global.
**Superuser:** role local atribuída a usuário humano; concede permissões disponíveis na empresa, mas não controles SaaS, não ultrapassa módulos indisponíveis/desativados e não pode ser atribuída a agente. Proteção de último superuser ativo deve ser transacional e impedir remoção/suspensão do último humano com esse papel.

### 5.6 Catálogo global de permissões — proposta compatível com ADR-0011

**Tenant:** catálogo mestre sem `company_identifier`; adoção/customização local com `company_identifier NOT NULL`.  
**Estado atual:** `permissions` existe; 1 registro; atualmente `company_identifier NOT NULL`, o que não representa sozinho o catálogo global versionado.
**Separar conceitualmente/fisicamente:**
1. **Definições globais:** permissão padrão identificada por código estável, domínio/entidade/ação e metadados; sem tenant.
2. **Versões globais publicadas:** conjunto imutável de definições; sem tenant.
3. **Versão adotada pela empresa:** registro tenant-scoped com versão global escolhida e data/autor.
4. **Permissões locais/customizadas:** `company_identifier NOT NULL`, vinculadas à adoção local e com código/nome/descrição e origem rastreável.
5. **Histórico de customização/restauração:** tenant-scoped e append-only/auditável.

Os nomes físicos finais dessas novas tabelas devem ser definidos na migration, mantendo esta separação. Não tratar a tabela atual `permissions` como catálogo global até reconciliar seus dados e dependências.

### 5.7 `public.role_permissions` — permissões concedidas por papel

**Tenant:** `company_identifier NOT NULL`.  
**Estado atual:** existe; vazia.  
**Campos:** `identifier`, `company_identifier`, `role_identifier`, `permission_identifier`, timestamps/autoria.
**Restrições:** UNIQUE `(company_identifier, role_identifier, permission_identifier)`; FKs compostas para role e permissão local/adotada; sem associações cross-tenant.
**Semântica:** concessões de papéis são agregadas por união. Ausência de concessão resulta em negar por padrão.

### 5.8 `public.user_permissions` — exceções por vínculo

**Tenant:** `company_identifier NOT NULL`.  
**Estado atual:** existe; vazia; `allow` nullable.  
**Campos:** `identifier`, `company_identifier`, `company_user_identifier`, `permission_identifier`, `allow boolean NOT NULL`, justificativa, validade opcional, autor e timestamps.
**Restrições:** UNIQUE `(company_identifier, company_user_identifier, permission_identifier)`; FKs compostas; `allow` obrigatório, sem NULL.
**Semântica já registrada no modelo:** `true` concede; `false` nega e prevalece sobre papéis; ausência de exceção usa união dos papéis; ausência de concessão nega. Para `superuser`, a negação individual não se aplica, conforme modelo vigente, sem ultrapassar restrições globais. Agentes não podem ser `superuser`.

### 5.9 Módulos e disponibilidade

**Estado atual:** existem `modules`, `company_modules`, `role_modules`, `user_modules`; sua presença não comprova autorização de dados.
- `modules`: catálogo global de módulos, sem `company_identifier`.
- `company_modules`: `company_identifier NOT NULL`, estado de ativação da empresa, parâmetros e datas.
- `role_modules` e `user_modules`: tenant-scoped, com FKs compostas, somente se forem mantidos como controles complementares.
**Regra:** disponibilidade global e ativação da empresa são gates anteriores à autorização. Se módulo global indisponível ou desativado pela empresa, nenhuma operação nova entra em execução; execuções ativas/aguardando devem seguir a regra de drenagem aprovada na ADR-0015. A permissão de tela/módulo não substitui autorização de entidade/registro.

### 5.10 Telas e navegação

`screens`, `role_screens`, `user_screens` podem controlar navegação/visibilidade, mas não são barreira de segurança. Associações que pertençam à empresa recebem `company_identifier`; catálogo global de telas não. Nenhuma policy de dado deve depender de tela visível.

## 6. Classificação física de tabelas de negócio e dados operacionais

Esta seção cobre tabelas existentes identificadas na auditoria e entidades necessárias ao MVP documentadas no modelo lógico. A inclusão de uma tabela nesta proposta não significa que ela exista hoje nem que seu DDL esteja aprovado.

| Tabela/entidade | Situação observada/documental | `company_identifier` proposto | Relação tenant e regra |
|---|---|---:|---|
| `customers` | Existe, 103 registros; muitos sem tenant | **Sim, NOT NULL** | Cliente pertence diretamente à empresa; UNIQUE composta para FKs filhas. |
| `customer_contacts` | Existe, 61 registros | **Sim, NOT NULL** | Contato pertence à empresa e ao cliente; FK composta para cliente. |
| `addresses` | Existe, 99 registros | **Sim, NOT NULL** | Endereço pertence à empresa e ao cliente quando associado; FK composta para cliente. |
| `equipments` | Existe, vazia | **Sim, NOT NULL** | Equipamento pertence à empresa; FK composta para cliente. |
| `work_orders` | Existe, vazia | **Sim, NOT NULL** | OS pertence à empresa; FK composta para cliente. |
| `visits` | Existe, 26 registros | **Sim, NOT NULL** | Visita pertence à empresa; FK composta para OS, cliente e demais referências. |
| `quotations` | Existe, vazia | **Sim, NOT NULL** | Orçamento pertence à empresa; referências devem ser do mesmo tenant. XOR de vínculo conforme regra comercial a validar no modelo funcional. |
| `task_types` | Existe, 2 registros | **Sim na adoção/localização** | Definição padrão global versionada sem tenant; cópia/adaptação adotada pela empresa com tenant. |
| `visit_types` | Existe, 4 registros | **Sim na adoção/localização** | Mesma separação entre padrão global e versão/customização da empresa. |
| `visit_requirements` | Existe, 5 registros | **Sim na adoção/localização** | Catálogo global de requisitos + adoção/customização tenant-scoped; código não pode ser único apenas global se permitir extensões locais. |
| `visit_type_requirement` | Existe, vazia | **Sim, NOT NULL** | Relação tenant-scoped entre tipo de visita e requisito, FKs compostas e par único. |
| `visit_tasks` | Ausente; necessária ao modelo operacional | **Sim, NOT NULL** | Tarefa pertence à visita e empresa; não possui máquina/status próprio. |
| `visit_events` | Ausente; especificação funcional aprovada | **Sim, NOT NULL** | Eventos append-only pertencem à visita/empresa; FK composta e sem UPDATE/DELETE operacional. |
| `technician_gps_positions` | Ausente no schema auditado | **Sim, NOT NULL** | Posições vinculadas ao usuário/vínculo e empresa; retenção/acesso restritos. |
| `visit_reports` | Ausente | **Sim, NOT NULL** | Relatos gerais da visita, append-only/versionados conforme ADR-0016. |
| `task_reports` | Ausente | **Sim, NOT NULL** | Relatos vinculados à tarefa e visita, mesmo tenant. |
| `requirement_evaluations` | Ausente | **Sim, NOT NULL** | Avaliação histórica da tentativa de encerramento e resultado por visita. |
| `requirement_evaluation_items` | Ausente | **Sim, NOT NULL** | Itens de avaliação pertencem à avaliação/empresa. |
| `requirement_pending_reviews` | Ausente | **Sim, NOT NULL** | Revisão autorizada de requisito opcional pendente, com justificativa e ator. |
| `evidence_records` | Ausente | **Sim, NOT NULL** | Metadados da evidência; binário fica em storage privado com caminho tenant-scoped. |
| `evidence_uploads` | Ausente | **Sim, NOT NULL** | Estado de upload/sincronização; idempotência e vínculo com evidência. |
| `evidence_upload_attempts` | Ausente | **Sim, NOT NULL** | Tentativas append-only ou histórico de tentativas; não guardar URL pública permanente. |
| `acceptance_document_versions` | Ausente | **Sim, NOT NULL** | Conteúdo/versão jurídica da empresa; preservação de versões. |
| `digital_acceptances` | Ausente | **Sim, NOT NULL** | Aceite relacionado à empresa, visita/documento e versão exata apresentada. |
| `acceptance_delivery_events` | Ausente | **Sim, NOT NULL** | Eventos de envio/entrega/abertura/aceite, append-only. |
| `audit_events` | Ausente | **Sim quando o evento descreve operação de tenant** | Eventos globais puramente técnicos podem não ter tenant; qualquer evento que contenha recurso/payload empresarial deve ter. |
| `approval_events` | Existe, vazia | **Sim, NOT NULL** | Evento de aprovação vinculado à empresa e ator ERP; corrigir referência de autoria para `public.users` se confirmada. |
| `documents` | Existe, vazia; inventário PENDENTE_CLASSIFICAÇÃO | **Sim se aprovado como catálogo empresarial** | Finalidade definida como catálogo de documentos da empresa, inclusive evidências; inclusão no MVP ainda pendente. Não implementar por inferência. |
| `contact_channels` | Existe, vazia; inventário PENDENTE_CLASSIFICAÇÃO | **Sim se aprovado para cadastro/atendimento** | Catálogo de canais de contato da empresa; inclusão no MVP pendente. |
| `phonenumbers` | Existe, vazia | **Sim se mantida** | Definir se é redundante com `customer_contacts`; não usar até resolver sobreposição. |
| `expenses` | Existe; FORA_MVP | **Sim se retomada no Financeiro** | Fora do MVP; não alterar nesta etapa. |
| `entity_types`, `entity_status` | FORA_MVP | Não adotar no MVP | Catálogos genéricos não são dependência da fundação. |
| `integrations`, `integration_sync_queue`, `integration_webhooks` | FORA_MVP | **Sim se retomadas** | Infraestrutura futura; não implementar nem depender no MVP. |
| `work_order_equipment` | OBsoleto/substituído no modelo | Não usar | Relação direta OS–equipamento substituída por equipamento associado à visita e visita associada à OS. Não remover fisicamente sem autorização. |
| `a_blank_template` | FORA_MVP, template técnico | Não se aplica | Artefato de desenvolvimento, não dado de negócio. |

### 6.1 Regras específicas de integridade operacional

- `customers`: unicidade de CPF/CNPJ precisa de deliberação explícita: global ou por empresa. Proposta de implementação segura e coerente com tenancy: UNIQUE por empresa, salvo requisito legal/comercial documentado para unicidade global.
- `customer_contacts`: incluir tenant mesmo sendo possível inferi-lo pelo cliente; impedir contato de cliente de outra empresa.
- `addresses`: incluir tenant e cliente; corrigir a função `set_visit_address_snapshot()` que, na fotografia auditada, referencia colunas inexistentes `addresses.address` e `addresses.address_identifier`. Corrigir apenas após teste de compatibilidade e autorização.
- `work_orders`: remover default aleatório de `customer_identifier`; FK deve ser explícita e obrigatória conforme regra de OS.
- `visits`: toda visita deve possuir `company_identifier`; reconstruir relações com OS e cliente antes de impor NOT NULL; não agrupar automaticamente visitas legadas por cliente. Mapear estados legados sem conversão automática.
- `quotations`: entidade vazia; modelar antes de receber dados. Não ativar XOR/constraints de negócio sem validar requisitos vigentes do orçamento.
- `visit_tasks`: não criar status próprio; tarefa acompanha a máquina de estados da visita.
- `visit_events`: eventos append-only; armazenar empresa diretamente e validar que visita pertence à mesma empresa.
- `technician_gps_positions`: dados sensíveis de localização; limitar leitura por permissão e finalidade, minimizar retenção e não expor ao cliente final.
- Evidências: metadados no banco e binários em storage privado; paths e políticas devem incluir tenant; URL assinada de curta duração; backend verifica autorização antes de emitir.
- Aceites: guardar referência à versão exata do documento e os eventos temporais; não sobrescrever aceite anterior.
- Relacionamentos entre avaliação, item, revisão, visita e tarefa sempre com FKs compostas por tenant.

## 7. RLS e políticas de autorização

### 7.1 Regra geral

Ativar RLS em todas as tabelas expostas por API. A política padrão é negar. Não criar policies permissivas `USING (true)` ou `WITH CHECK (true)` em tabelas tenant-scoped.

Para tabela tenant-scoped, cada operação deve validar:
1. identidade Auth válida;
2. usuário ERP vinculado à identidade;
3. empresa ativa;
4. vínculo ativo em `company_users`;
5. contexto da operação igual a `company_identifier` do registro;
6. permissão para a ação;
7. restrições específicas de autoria/estado/módulo.

### 7.2 Funções auxiliares

Proposta de funções SQL pequenas, com nomes e assinaturas finais a definir na migration:
- resolver usuário ERP por `auth.uid()`;
- verificar vínculo ativo `(company_identifier)`;
- resolver contexto de empresa transacional validado;
- verificar permissão `(company_identifier, permission_code, resource_context)`;
- verificar disponibilidade global e ativação do módulo.

Requisitos para funções:
- `SECURITY INVOKER` como padrão;
- `SECURITY DEFINER` somente quando indispensável, com proprietário restrito, `search_path` fixo e schema-qualified references;
- revogar EXECUTE de `PUBLIC`/anon por padrão;
- conceder EXECUTE apenas aos papéis necessários;
- não retornar dados sensíveis além do necessário;
- não aceitar identidade/tenant/role como alegação não validada do cliente;
- não usar função genérica que ignore autorização por conveniência.

### 7.3 Matriz mínima de RLS por grupo

| Grupo | SELECT | INSERT | UPDATE | DELETE |
|---|---|---|---|---|
| `companies` | próprio tenant com vínculo ativo | fluxo controlado de provisionamento | administração autorizada | proibido no fluxo normal |
| `users` | próprio perfil; dados mínimos de membros conforme permissão | provisionamento confiável | próprio perfil em campos permitidos ou administrador | não permitido pelo fluxo comum |
| `company_users` | próprio vínculo e membros autorizados | administrador autorizado | administrador autorizado | revogação lógica; não apagar histórico |
| Papéis/permissões | membros autorizados conforme função | administração de autorização | administração de autorização | restringir; preservar histórico |
| Cadastros/OS/visitas | tenant e permissão de leitura | tenant e permissão de criação | tenant, permissão e regra de estado | negar por padrão; regra explícita |
| Eventos append-only | tenant e permissão de leitura | backend validado e permissão de evento | **negar** | **negar** |
| GPS | tenant e permissão/finalidade | serviço/app autenticado e atribuição válida | preferir append-only | política de retenção controlada |
| Evidências/aceites | tenant e autorização específica | backend validado | versões/eventos novos, não sobrescrever | negar no fluxo operacional |
| Catálogos globais | leitura conforme publicação | somente plataforma | somente plataforma/versionamento | não apagar versões publicadas |

A matriz é o mínimo de desenho; cada tabela terá policy por comando e testes específicos. Permissões de UI/telas não são suficientes.

### 7.4 Grants

- Revogar grants desnecessários de `anon`, `authenticated` e `service_role`.
- Conceder a `authenticated` somente operações que o modelo de acesso direto do cliente realmente exigir.
- Preferir API/backend para operações sensíveis e transacionais.
- Não presumir que RLS restringe `service_role`; esse papel contorna RLS no Supabase e deverá permanecer exclusivamente no servidor.
- Não expor credenciais privilegiadas em web/mobile.
- Revisar grants de funções e objetos por schema, incluindo sequences, views, materialized views e RPCs.

## 8. Backend/API e contrato de autorização

Toda operação protegida deve executar o seguinte pipeline:
1. validar token/sessão;
2. resolver `auth.uid()`;
3. resolver usuário ERP;
4. validar empresa solicitada e vínculo ativo;
5. resolver papéis e exceções;
6. verificar módulo global/empresa;
7. verificar permissão para a ação;
8. validar escopo do recurso (cliente, visita atribuída, autoria, estado etc.);
9. executar transação sob contexto seguro;
10. registrar auditoria quando relevante.

O backend nunca aceitará do cliente:
- `created_by`/autor como prova de autoria;
- `company_identifier` como prova de acesso;
- `role`, `superuser`, permissões ou status como claims confiáveis sem validação;
- associação entre IDs sem verificar tenant;
- mudança de tenant de registro via UPDATE.

Operações de sistema/agent terão identidade técnica própria, permissões próprias e escopo explícito. A Isa não poderá assumir identidade do humano solicitante nem receber superuser.

## 9. Auditoria append-only

A fundação deverá suportar `audit_events` tenant-scoped para operações relevantes, respeitando ADR-0016:
- `company_identifier`;
- tipo de evento, entidade, identificador do recurso;
- ator tipado (usuário ERP, agente ou sistema);
- origem/canal;
- `occurred_at` e `recorded_at`;
- correlation/idempotency key quando aplicável;
- dados mínimos antes/depois ou referência a snapshot, com minimização de dados pessoais.
Não permitir UPDATE/DELETE por papéis operacionais. Correções geram novos eventos. O desenho detalhado de retenção, particionamento e anonimização deverá ser aprovado no contrato de auditoria.

## 10. Migração do estado atual

A migração deverá ser executada em fases, com backup e reconciliação. Não converter dados ambíguos automaticamente.

### Fase 0 — pré-condições
- confirmar projeto/ambiente e snapshot/backup restaurável;
- obter inventário atual do schema, policies, grants, triggers, funções, views, constraints, índices e storage;
- identificar repositório de código/migrations (não identificado nesta documentação);
- estabelecer baseline versionado de migrations, pois a auditoria não encontrou migrations registradas no projeto;
- testar em ambiente isolado/restaurável.

### Fase 1 — identidade e vínculos
- reconciliar `users.identifier` versus `auth.users.id` e `auth_user_identifier`;
- reconciliar `company_users.user_identifier` com `users.identifier`;
- investigar divergência entre `company_users.role_identifier` legado e `company_user_roles`;
- preservar `company_admin` somente como atributo comercial, caso ainda necessário;
- validar todos os vínculos e usuários, sem promover papéis automaticamente.

### Fase 2 — backfill tenant
- definir tenant correto de cada customer, contact, address e visit usando evidências verificáveis;
- não preencher `company_identifier` com a única empresa atual sem validar a origem e o significado de cada linha;
- registros sem atribuição comprovável ficam em quarentena/revisão, não visíveis pela API;
- clientes: 94/103 sem tenant na fotografia; contatos: 61/61 sem tenant; endereços: 9 sem tenant; visitas: 26 sem tenant;
- reconstruir OS das visitas sem `work_order_identifier` somente após decisão e evidência de negócio;
- não agrupar visitas automaticamente por cliente;
- preservar os IDs existentes.

### Fase 3 — integridade
- adicionar colunas inicialmente nullable quando necessário;
- preencher e validar backfill;
- identificar órfãos e relações cross-tenant;
- criar UNIQUEs compostas e FKs compostas;
- corrigir defaults indevidos e triggers/funções quebradas;
- só então tornar `company_identifier` NOT NULL;
- validar índices e plano de consulta.

### Fase 4 — autorização
- criar funções auxiliares com privilégios mínimos;
- remover policies permissivas após preparar substitutas testadas;
- revisar grants, views, triggers e funções SECURITY DEFINER;
- implantar policies RLS por tabela;
- validar acesso direto PostgREST e via backend, incluindo papéis privilegiados.

### Fase 5 — testes e liberação
- testes automatizados de isolamento e autorização;
- teste de regressão de todos os fluxos do MVP;
- testes concorrentes de escrita e troca de empresa;
- monitoramento de negações e erros;
- plano de rollback testado;
- liberação gradual somente após critérios de aceite.

## 11. Matriz de dados legados e bloqueios conhecidos

| Achado da auditoria 2026-11-08 | Tratamento exigido |
|---|---|
| 38 tabelas com RLS; 26 sem policy; policies permissivas em customers, addresses, contacts, visits etc. | Remediar em lote coordenado por tabela; não corrigir isoladamente sem backend/grants. |
| 94/103 customers sem `company_identifier` | Backfill com evidência; não assumir tenant automaticamente. |
| 61/61 customer_contacts sem `company_identifier` | Derivar apenas após validar cliente e empresa; FK composta. |
| 9 addresses sem tenant; 4 sem cliente | Quarentena/revisão para órfãos; backfill verificado. |
| 26 visits sem tenant; 26 sem OS; 1 sem cliente | Reconstrução de vínculos e mapeamento de estado; sem agrupamento presumido. |
| `set_visit_address_snapshot()` referencia colunas inexistentes | Corrigir e testar trigger antes de liberar inserção de visita com endereço. |
| `work_orders.customer_identifier` com default UUID aleatório | Remover default; exigir FK válida. |
| Divergência `company_users.role_identifier` versus `company_user_roles` | Decisão/migração manual por vínculo; não promover automaticamente. |
| `permissions.company_identifier NOT NULL` não representa catálogo global | Separar catálogo mestre/versionado e adoção/customização tenant. |
| `user_permissions.allow` nullable | Corrigir para NOT NULL após confirmar valores existentes. |
| FKs simples sem garantia de tenant | Migrar para FKs compostas. |
| Grants amplos e funções SECURITY DEFINER | Revisão de privilégios, search_path, proprietário e EXECUTE. |
| `can_view_expense` e estruturas Financeiro | Fora do MVP; não integrar à fundação nem alterar sem deliberação. |

## 12. Testes de aceitação obrigatórios

A implantação só poderá ser considerada apta quando todos os testes abaixo passarem com duas empresas distintas (A e B), dois usuários com vínculos diferentes e pelo menos um usuário multiempresa:

1. Sem JWT: SELECT/INSERT/UPDATE/DELETE de dados protegidos negados.
2. Usuário sem vínculo: acesso negado mesmo conhecendo UUIDs.
3. Usuário A: não lê nem altera registros de B.
4. INSERT com `company_identifier` de B pelo usuário A: negado.
5. UPDATE de registro A para `company_identifier` B: negado.
6. FK cruzada A→B (cliente, OS, visita, tarefa, evidência, papel ou permissão): rejeitada pela constraint.
7. Vínculo suspenso: novas operações negadas.
8. Usuário multiempresa: seleção obrigatória e isolamento entre trocas.
9. Cache/query de A não aparece após troca para B.
10. Permissão ausente: negar; concessão por qualquer role: permitir; `allow=false`: negar; `allow=true`: permitir, conforme exceções vigentes.
11. `superuser` humano: acesso amplo apenas dentro da própria empresa e módulos disponíveis.
12. Agente: nunca recebe `superuser`; não assume identidade humana.
13. Módulo global indisponível ou desativado na empresa: operações novas bloqueadas; execuções existentes seguem drenagem da ADR-0015.
14. Evento append-only: UPDATE/DELETE negados, correção cria evento/revisão.
15. Funções SECURITY DEFINER não permitem bypass de tenant; anon não executa funções privilegiadas.
16. Storage: usuário A não baixa binário de B nem reutiliza URL assinada fora do escopo.
17. APIs e acesso direto ao PostgREST produzem decisões equivalentes.
18. Concorrência: troca/suspensão de vínculo não permite operação iniciada com autorização obsoleta após o ponto de serialização definido.
19. Restauração de backup e rollback são testados em ambiente não produtivo.
20. Regressão funcional dos fluxos MVP: clientes, contatos, endereços, equipamentos, OS, visitas, tarefas, evidências, aceite e execução offline.

## 13. Ordem recomendada de implementação para reduzir prazo sem sacrificar segurança

A proposta evita implementar tabelas operacionais sobre uma fundação insegura. Ordem:
1. baseline de schema/migrations e reconciliação de identidade/vínculos;
2. backfill e constraints tenant das tabelas já existentes;
3. contexto autenticado da empresa e serviço de autorização no backend;
4. grants, funções e RLS coordenados;
5. testes multiempresa e regressão;
6. criação das entidades operacionais ausentes (`visit_tasks`, `visit_events`, evidências, avaliações, relatórios e demais tabelas MVP), já nas convenções tenant;
7. liberação gradual.

É possível paralelizar especificação de contratos e testes, mas não liberar tabelas de negócio sem isolamento efetivo. O caminho mais rápido seguro é corrigir a fundação uma vez, evitando retrabalho e migrações incompatíveis em cada módulo.

## 14. Pontos que exigem confirmação explícita do responsável

A proposta abaixo contém escolhas técnicas que precisam de aprovação ou ajuste:
1. `company_identifier NOT NULL` em todas as tabelas tenant-scoped, inclusive tabelas filhas e associações, conforme preferência manifestada.
2. `users` global sem tenant; `companies` como raiz sem tenant.
3. catálogo mestre de permissões global e versões imutáveis, com adoção/customizações por empresa.
4. papéis atribuíveis sempre tenant-scoped; templates globais separados se necessários.
5. FKs compostas e UNIQUE `(company_identifier, identifier)` em todas as tabelas tenant-scoped.
6. RLS negar por padrão e sem policies permissivas em dados de empresa.
7. backend como autoridade para contexto da empresa, com contexto SQL transacional não falsificável pelo cliente.
8. `customers.cpf_cnpj` único por empresa, não global — proposta sujeita a confirmação.
9. backfill de registros sem tenant somente com evidência; sem atribuição automática.
10. tratar tabelas `documents` e `contact_channels` como pendentes de inclusão no MVP, conforme inventário.
11. manter `phonenumbers` suspensa para uso até resolver sobreposição com `customer_contacts`.
12. não usar nem remover `work_order_equipment`, `expenses`, `can_view_expense`, integrações ou catálogos genéricos fora do escopo.
13. política de retenção e anonimização de eventos/GPS/evidências ainda precisa de especificação.
14. modelo físico de autenticação de agentes e contexto de sessões por aplicação permanece dependente de especificação própria.

## 15. Critério de aprovação desta proposta

Aprovar este documento significa aprovar a direção técnica e os contratos físicos aqui descritos, sujeitos às ressalvas explicitamente indicadas. Itens marcados como pendentes não se tornam automaticamente aprovados. A aprovação não autoriza execução no Supabase.

Após aprovação:
- preparar plano de migração detalhado, tabela por tabela, com SQL completo e ordem de execução;
- revisar a classificação final de cada coluna/constraint com o schema real revalidado;
- preparar testes automatizados e plano de rollback;
- apresentar o pacote de DDL para autorização explícita antes de qualquer execução;
- registrar decisões que alterem arquitetura em ADR quando necessário;
- atualizar modelo lógico, status e continuidade após a aprovação.

## 16. Referências

- `documentacao/decisoes/ADR-0017-tenancy-compartilhada-autorizacao-multiempresa.md`
- `documentacao/decisoes/ADR-0011-catalogo-permissoes-global-versionado.md`
- `documentacao/decisoes/ADR-0016-historico-append-only.md`
- `documentacao/MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md`
- `documentacao/MODELO_LOGICO_DADOS_OPERACAO.md`
- `documentacao/CONTROLE_ESCOPO_MVP_IGNORADOS_E_OBSOLETOS.md`
- `documentacao/auditorias/AUDITORIA_DADOS_AUTORIZACAO_2026-11-08.md`
- `documentacao/PROTOCOLO_DE_ATUACAO_IA_GITHUB.md`

## Histórico

- 2026-11-08 — v0.1.0: primeira consolidação da proposta física multiempresa, com classificação tenant por tabela, regras de integridade, identidade, autorização, RLS, backend, migração e critérios de aceite. Criada para revisão; não aprovada e não implementada.
