# Plano de migração da fundação multiempresa — Parte A (estrutura e integridade)

**Projeto:** ERP Winservice  
**Data da inspeção:** 2026-11-08  
**Versão:** 0.2.0  
**Estado:** PROPOSTA TÉCNICA PARA APROVAÇÃO — NÃO EXECUTADA  
**Ambiente:** Supabase PostgreSQL 17.6, projeto `voaovwphncwbrwtcqnsc`, região `sa-east-1`  
**Branch documental:** `main`  
**Commit-base desta revisão:** `7b51e642b3ac814f209113c10ac994740bea2204`

> Este documento detalha somente a Parte A: estrutura física, backfill, constraints, chaves e integridade referencial. A Parte B — contexto autenticado da empresa, funções de autorização, grants e policies RLS — permanece separada e bloqueada até que o contrato confiável de contexto transacional no backend seja definido. Nenhum SQL deste documento foi executado.

---

## 1. Objetivo e limites

Transformar a especificação física aprovada em um plano revisável para corrigir a fundação estrutural multiempresa do MVP, sem misturar a implementação da autorização.

Direção aprovada:
- PostgreSQL e schema compartilhados, com isolamento lógico por empresa (ADR-0017).
- `company_identifier NOT NULL` em todas as tabelas pertencentes a uma empresa, inclusive filhas e associações.
- Exceções justificadas para identidade global (`users`), raiz do tenant (`companies`), identidade do provedor (`auth.users`) e catálogos/infraestrutura genuinamente globais.
- FKs compostas com `company_identifier` para impedir referências entre empresas.
- CPF/CNPJ único dentro de cada empresa e permitido em empresas diferentes, após canonicalização.
- Preservação de histórico; não apagar registros legados para facilitar a migração.

Este plano não aprova nem executa DDL, DML, migrations, policies, grants, funções ou triggers. A autorização operacional deverá ser específica e posterior à aprovação deste pacote.

### 1.1 Escopo da Parte A

1. Baseline e pré-validação do schema.
2. Colunas `company_identifier` faltantes nas tabelas tenant-scoped existentes que pertencem ao escopo da fundação.
3. Backfill baseado em relações verificáveis.
4. Chaves únicas compostas necessárias como destino de FKs.
5. FKs compostas entre entidades do mesmo tenant.
6. Unicidade por empresa de CPF/CNPJ e identificadores externos aplicáveis.
7. Remoção de defaults que criam referências artificiais.
8. Reconciliação estrutural das associações usuário–empresa–papel, sem escolher papéis pelo responsável.
9. Correção da função de snapshot de endereço, após validação do comportamento do trigger.
10. Testes de integridade, validação pós-migração e plano de rollback.

### 1.2 Fora desta entrega

- Contexto de empresa na sessão/transação, funções auxiliares de autorização, grants e RLS (Parte B).
- Stack, implementação ou repositório da API/backend. A documentação vigente ainda não identifica um repositório de API do ERP.
- Criação das entidades operacionais ausentes (`visit_tasks`, `visit_events`, GPS, evidências, avaliações, relatos, aceites e auditoria). Elas deverão nascer já tenant-scoped, conforme o modelo lógico, em pacote próprio.
- `documents` e `contact_channels`, cuja inclusão no MVP continua pendente.
- `phonenumbers`, suspensa até resolver sobreposição com `customer_contacts`.
- `expenses`, `entity_types`, `entity_status`, integrações e filas de integração, fora do MVP.
- `work_order_equipment`, obsoleta/substituída conceitualmente; não será usada nem removida fisicamente nesta etapa.
- `a_blank_template`, artefato técnico fora do escopo de negócio.
- Migração dos status legados das visitas. Não há mapeamento aprovado para converter `active`, `aberto`, `opened`, `scheduled`, `completed` e `cancelled` para a máquina de estados vigente.

---

## 2. Evidência da inspeção atual

Inspeção somente leitura no projeto Supabase indicado acima.

| Indicador | Resultado |
|---|---:|
| Tabelas no schema `public` | 38 |
| Tabelas com RLS habilitado | 38 |
| Policies RLS existentes | 23 |
| Chaves estrangeiras existentes | 103 |
| FKs compostas envolvendo tenant | 0 |
| Tabelas que já possuem `company_identifier` | 15 |
| Dessas colunas, ainda nullable | 6 |
| Migrations registradas no Supabase | 0 |

A existência de RLS não significa que a autorização esteja correta. A Parte B tratará as policies; este documento não tenta corrigi-las.

### 2.1 Colunas tenant já existentes

| Tabela | Estado atual |
|---|---|
| `a_blank_template` | NOT NULL; fora do escopo |
| `addresses` | Nullable; zero nulos após backfill já executado |
| `approval_events` | Nullable; tabela vazia |
| `company_modules` | NOT NULL |
| `company_users` | NOT NULL |
| `customer_contacts` | Nullable; zero nulos após backfill já executado |
| `customers` | Nullable; zero nulos após backfill já executado |
| `entity_status` | NOT NULL; fora do escopo |
| `expenses` | Nullable; fora do MVP |
| `integration_sync_queue` | NOT NULL; fora do MVP |
| `integrations` | NOT NULL; fora do MVP |
| `permissions` | NOT NULL; precisa respeitar ADR-0011 |
| `role_permissions` | NOT NULL |
| `roles` | Nullable; zero nulos após backfill já executado |
| `user_permissions` | NOT NULL |

### 2.2 Backfills já executados e confirmados

Em 2026-11-08, foram atribuídos valores da única empresa existente aos registros nulos:

| Tabela | Linhas atualizadas | Nulos depois |
|---|---:|---:|
| `customers` | 94 | 0 |
| `customer_contacts` | 61 | 0 |
| `addresses` | 9 | 0 |
| `roles` | 3 | 0 |
| **Total** | **167** | **0** |

A empresa existente e ativa é Winservice. Nenhuma linha previamente associada a outra empresa foi alterada. Esses backfills são fatos concluídos e não devem ser repetidos como pendentes.

Também foram desativados, sem exclusão, 11 endereços ativos excedentes em grupos duplicados, e recalculado o endereço principal. A verificação posterior encontrou no máximo um endereço ativo por cliente nos grupos examinados.

### 2.3 Situação das visitas

`visits` possui 26 registros e ainda não possui `company_identifier`. O responsável pelo projeto confirmou expressamente que todas as 26 visitas existentes pertencem à Winservice.

| Verificação | Resultado |
|---|---:|
| Visitas totais | 26 |
| Com cliente associado | 25 |
| Com endereço associado | 25 |
| Com ordem de serviço associada | 0 |
| Com cliente e endereço associados | 25 |
| Sem cliente e sem endereço | 1 |

Nas 25 visitas com cliente, o cliente possui `company_identifier` e não foi encontrado conflito entre a empresa do cliente e a do endereço associado. A visita restante não possui cliente, endereço ou OS, mas seu tenant foi confirmado diretamente pelo responsável do projeto: **todas as 26 visitas pertencem à Winservice**. O backfill de `visits.company_identifier` deverá, portanto, atribuir o identificador da Winservice às 26 linhas. A visita sem cliente/endereço permanece sem esses vínculos; não serão criados dados artificiais para completá-los.

**A OS é opcional para uma visita.** `work_order_identifier` permanecerá nullable. Quando preenchido, deverá apontar para uma OS da mesma empresa por FK composta. Não criar OS automaticamente nem agrupar visitas por cliente.

### 2.4 Associação de papéis

Estado verificado:
- `company_users`: 1 vínculo.
- `roles`: 3 registros, todos associados à empresa.
- `company_user_roles`: 2 associações sem `company_identifier`.
- `company_users.role_identifier` legado está preenchido.
- As duas linhas de `company_user_roles` não coincidem com o `role_identifier` legado.
- Não foi encontrado relacionamento de papel entre empresas diferentes.

A ADR-0017 estabelece que o contexto de autorização é próprio de cada vínculo usuário–empresa e inclui papéis. A tabela `company_user_roles` representa a associação N:N de papéis ao vínculo e é compatível com esse requisito. Entretanto, **não foi localizada decisão aprovada que declare formalmente `company_user_roles` como fonte única e determine a remoção de `company_users.role_identifier`**. O plano, portanto, não removerá essa coluna nem escolherá automaticamente entre os dados divergentes. A definição exige deliberação documental específica, além de reconciliação das duas associações existentes e auditoria de dependências (inclusive funções legadas).

### 2.5 Clientes e CPF/CNPJ

- `customers`: 103 registros.
- 101 registros sem CPF/CNPJ preenchido.
- 2 registros com documento preenchido; ambos possuem 11 dígitos após remoção de pontuação.
- Não foram encontradas duplicidades normalizadas dentro da empresa atual.
- Existe hoje a constraint global `clientes_cpf_cnpj_key`, incompatível com a decisão aprovada de permitir o mesmo documento em empresas diferentes.

A migration deverá substituir a unicidade global pela unicidade tenant-scoped após validação de canonicalização. Zeros à esquerda deverão ser preservados. Nulos e strings vazias não deverão competir no índice.

### 2.6 Outros achados estruturais

- `work_orders` está vazia; `customer_identifier` aceita NULL e possui default `gen_random_uuid()`. Esse default inventa um identificador sem cliente real.
- `equipments`, `quotations` e `approval_events` estão vazias.
- `role_modules`, `role_screens`, `user_modules` e `user_screens` estão vazias e são associações vinculadas a papel ou vínculo de empresa.
- `company_user_roles.identifier` é nullable; validar antes de impor NOT NULL ou usá-lo como chave pública.
- `user_permissions.allow` é nullable; a tabela está vazia. Tornar NOT NULL antes de receber dados.
- `set_visit_address_snapshot()` consulta `addresses.address` e `addresses.address_identifier`, que não existem. O schema possui `street`, `display_address` e `identifier`. A função está estruturalmente incompatível e deve ser corrigida antes de depender do trigger.
- Não há migrations registradas no projeto Supabase. O repositório de código/migrations da aplicação não foi identificado na documentação.

---

### Referência adicional — catálogo de permissões

A definição física proposta para o catálogo global versionado e sua adoção/customização por empresa está em [Proposta de modelo físico do catálogo de permissões — MVP](PROPOSTA_MODELO_FISICO_CATALOGO_PERMISSOES_MVP.md). Esse desenho permanece sujeito à aprovação e não autoriza DDL. A tabela atual `permissions` não deverá ser migrada ou removida até aprovação, mapeamento do registro existente e auditoria de dependências.

## 3. Classificação das tabelas para a Parte A

### 3.1 Entidades tenant-scoped existentes a preparar

| Tabela | Ação | Evidência/backfill |
|---|---|---|
| `customers` | Manter coluna; impor NOT NULL; chave composta | Backfill anterior concluído |
| `customer_contacts` | Manter coluna; impor NOT NULL; FK composta para cliente | Backfill anterior concluído |
| `addresses` | Manter coluna; impor NOT NULL; FK composta para cliente quando associado | Backfill anterior concluído |
| `equipments` | Adicionar `company_identifier NOT NULL`; FK composta para cliente | Tabela vazia |
| `work_orders` | Adicionar tenant; cliente obrigatório; remover default aleatório | Tabela vazia |
| `visits` | Adicionar coluna; backfill de 26 para Winservice; impor NOT NULL; FKs compostas; OS opcional | Empresa confirmada pelo responsável |
| `quotations` | Adicionar tenant; FKs compostas para cliente, OS e visita | Tabela vazia |
| `approval_events` | Manter coluna; impor NOT NULL; FKs compostas para orçamento, OS e cliente | Tabela vazia |
| `company_users` | Manter tenant; chave composta para referência por vínculo | Já possui tenant |
| `company_user_roles` | Adicionar tenant; backfill pelo vínculo pai; FKs compostas para vínculo e papel | 2 linhas deriváveis |
| `roles` | Manter tenant; impor NOT NULL; chave composta | Backfill anterior concluído |
| `company_modules` | Manter tenant; validar chave/índice e FK | Tabela vazia |
| `role_permissions` | Manter tenant; chaves e FKs compostas para papel e permissão | Tabela vazia |
| `user_permissions` | Manter tenant; FK composta para vínculo e permissão; `allow NOT NULL` | Tabela vazia |
| `role_modules` | Adicionar tenant derivado do papel; FK composta para papel | Tabela vazia |
| `role_screens` | Adicionar tenant derivado do papel; FK composta para papel | Tabela vazia |
| `user_modules` | Adicionar tenant derivado do vínculo; FK composta para vínculo | Tabela vazia |
| `user_screens` | Adicionar tenant derivado do vínculo; FK composta para vínculo | Tabela vazia |

`company_identifier` também deverá existir em todas as novas tabelas tenant-scoped do MVP, mas a criação dessas tabelas operacionais não faz parte deste pacote.

### 3.2 Catálogos que exigem separação entre definição global e adoção local

- `task_types`
- `visit_types`
- `visit_requirements`
- `visit_type_requirement`

A especificação física prevê definições padrão globais e adoções/cópias/customizações tenant-scoped. Não é correto atribuir a empresa atual diretamente às definições mestres sem comprovar se os registros existentes são templates globais ou dados locais.

A Parte A não alterará essas quatro tabelas até que o modelo físico de catálogo/adoção esteja individualizado. A tabela `visit_type_requirement` deverá receber tenant quando seus alvos locais estiverem definidos.

### 3.3 Exceções e tabelas adiadas

| Tabela/grupo | Tratamento |
|---|---|
| `users` | Global; não adicionar tenant |
| `companies` | Raiz do tenant; não adicionar tenant |
| `auth.users` | Identidade do provedor; não adicionar tenant |
| `modules`, `screens` | Catálogos globais; não adicionar tenant nesta etapa |
| `permissions` | Não migrar nesta etapa sem aprovação do modelo físico global versionado e adoção local; seguir proposta específica |
| `documents`, `contact_channels` | Inclusão no MVP pendente |
| `phonenumbers` | Suspensa até resolver sobreposição |
| `expenses`, `entity_types`, `entity_status` | Fora do MVP |
| `integrations`, `integration_sync_queue`, `integration_webhooks` | Fora do MVP |
| `work_order_equipment` | Obsoleta/substituída; não alterar nem excluir |
| `a_blank_template` | Artefato técnico fora do escopo |

---

## 4. Sequência de migração proposta

### Fase A0 — baseline, backup e preflight

Antes de qualquer DDL:
1. Confirmar que o projeto/ambiente é o projeto de produção pretendido.
2. Gerar e testar backup/snapshot restaurável.
3. Exportar inventário de tabelas, colunas, tipos, defaults, PKs, UNIQUEs, FKs, índices, triggers, funções e dependências.
4. Confirmar estado atual da empresa e dos vínculos.
5. Criar baseline versionado de schema/migrations no repositório de implementação que vier a ser definido. A lista de migrations do Supabase está vazia.
6. Ensaiar preflight em ambiente isolado/restaurável.
7. Interromper a migration se qualquer validação de tenant, órfão ou duplicidade falhar.

### Fase A1 — adicionar colunas tenant faltantes

Adicionar inicialmente nullable:
- `equipments.company_identifier`
- `work_orders.company_identifier`
- `visits.company_identifier`
- `quotations.company_identifier`
- `company_user_roles.company_identifier`
- `role_modules.company_identifier`
- `role_screens.company_identifier`
- `user_modules.company_identifier`
- `user_screens.company_identifier`

`approval_events` já possui a coluna; deverá ser validada e tornada NOT NULL, pois está vazia.

Não adicionar tenant diretamente aos catálogos globais da seção 3.2.

### Fase A2 — backfill baseado em relações verificáveis

1. `visits`: atribuir o `company_identifier` da Winservice às 26 visitas, conforme confirmação expressa do responsável. Validar separadamente que referências de cliente/endereço preenchidas não cruzam empresas; não inventar cliente, endereço ou OS para a visita que não os possui.
2. `company_user_roles`: copiar tenant do vínculo pai em `company_users`; exigir que o papel pertença à mesma empresa.
3. `role_modules` e `role_screens`: copiar tenant do papel.
4. `user_modules` e `user_screens`: copiar tenant do vínculo de empresa.
5. `equipments`, `work_orders` e `quotations`: estão vazias; adicionar coluna sem default.
6. `approval_events`: tabela vazia; impor NOT NULL sem preencher dados artificiais.

Não utilizar `created_by`, e-mail, nome, endereço textual ou UUID aleatório como prova isolada de tenant.

### Fase A3 — normalizar e proteger chaves

Para cada tabela tenant-scoped, criar chave candidata/índice único `(company_identifier, identifier)` quando possuir `identifier`. Para `approval_events`, que utiliza `id` como chave UUID, usar `(company_identifier, id)`. Para tabelas de associação, preservar a PK atual e criar as chaves necessárias às FKs compostas.

Antes de cada UNIQUE:
- verificar duplicidades;
- confirmar que tenant está preenchido;
- validar que o identificador referenciado é estável e não nulo.

### Fase A4 — FKs compostas

Criar FKs compostas depois das chaves únicas compostas e da validação dos dados.

| Origem | Colunas | Destino | Colunas |
|---|---|---|---|
| `customer_contacts` | `(company_identifier, customer_identifier)` | `customers` | `(company_identifier, identifier)` |
| `addresses` | `(company_identifier, customer_identifier)` | `customers` | `(company_identifier, identifier)` |
| `equipments` | `(company_identifier, customer_identifier)` | `customers` | `(company_identifier, identifier)` |
| `work_orders` | `(company_identifier, customer_identifier)` | `customers` | `(company_identifier, identifier)` |
| `visits` | `(company_identifier, customer_identifier)` | `customers` | `(company_identifier, identifier)` |
| `visits` | `(company_identifier, address_identifier)` | `addresses` | `(company_identifier, identifier)` |
| `visits` | `(company_identifier, work_order_identifier)` | `work_orders` | `(company_identifier, identifier)` |
| `quotations` | `(company_identifier, customer_identifier)` | `customers` | `(company_identifier, identifier)` |
| `quotations` | `(company_identifier, visit_identifier)` | `visits` | `(company_identifier, identifier)` |
| `quotations` | `(company_identifier, work_order_identifier)` | `work_orders` | `(company_identifier, identifier)` |
| `approval_events` | `(company_identifier, customer_uuid)` | `customers` | `(company_identifier, identifier)` |
| `approval_events` | `(company_identifier, work_order_uuid)` | `work_orders` | `(company_identifier, identifier)` |
| `approval_events` | `(company_identifier, quotation_uuid)` | `quotations` | `(company_identifier, identifier)` |
| `company_user_roles` | `(company_identifier, company_user_identifier)` | `company_users` | `(company_identifier, identifier)` |
| `company_user_roles` | `(company_identifier, role_identifier)` | `roles` | `(company_identifier, identifier)` |
| `company_users` | `(company_identifier, role_identifier)` | `roles` | `(company_identifier, identifier)` — dependente de decisão formal sobre o campo legado; não executar enquanto pendente |
| `role_permissions` | `(company_identifier, role_identifier)` | `roles` | `(company_identifier, identifier)` |
| `role_permissions` | `(company_identifier, permission_identifier)` | `permissions` | `(company_identifier, identifier)` — sujeito ao desenho final ADR-0011 |
| `user_permissions` | `(company_identifier, company_user_identifier)` | `company_users` | `(company_identifier, identifier)` |
| `user_permissions` | `(company_identifier, permission_identifier)` | `permissions` | `(company_identifier, identifier)` — sujeito ao desenho final ADR-0011 |
| `role_modules` | `(company_identifier, role_identifier)` | `roles` | `(company_identifier, identifier)` |
| `role_screens` | `(company_identifier, role_identifier)` | `roles` | `(company_identifier, identifier)` |
| `user_modules` | `(company_identifier, company_user_identifier)` | `company_users` | `(company_identifier, identifier)` |
| `user_screens` | `(company_identifier, company_user_identifier)` | `company_users` | `(company_identifier, identifier)` |

Além das FKs compostas, cada nova coluna `company_identifier` deverá possuir FK para `companies(identifier)` quando essa relação não estiver já garantida por uma FK composta para uma entidade tenant. Relações com entidades globais, como `users`, `modules` e `screens`, permanecem FKs simples. A FK simples existente poderá ser mantida se não causar redundância ou conflito; sua remoção não é requisito para obter a barreira composta.

Relações com catálogos de tarefa/requisito permanecem pendentes até a separação entre definição global e adoção local.

### Fase A5 — unicidade tenant-scoped

**CPF/CNPJ.** Remover a constraint global `clientes_cpf_cnpj_key` e substituí-la por índice único por empresa, baseado em:

    NULLIF(regexp_replace(cpf_cnpj, '[^0-9]', '', 'g'), '')

Índice proposto:

    CREATE UNIQUE INDEX customers_company_cpf_cnpj_normalized_uq
    ON public.customers (
        company_identifier,
        (NULLIF(regexp_replace(cpf_cnpj, '[^0-9]', '', 'g'), ''))
    )
    WHERE NULLIF(regexp_replace(cpf_cnpj, '[^0-9]', '', 'g'), '') IS NOT NULL;

A expressão remove pontuação e espaços não numéricos e preserva zeros à esquerda. Valores NULL e strings vazias ficam fora da unicidade.

Antes de remover a constraint global:
- executar consulta de duplicidade pela expressão normalizada;
- confirmar que o índice novo pode ser criado;
- validar que os dois documentos existentes permanecem idênticos semanticamente;
- confirmar que não há valor alfanumérico inválido sendo silenciosamente convertido.

A validação de dígitos verificadores de CPF/CNPJ não será introduzida nesta migration sem requisito próprio aprovado.

**Outras chaves.** Revisar e ajustar:
- endereço principal: unicidade `(company_identifier, customer_identifier)` para linhas com `is_primary = true`;
- associação usuário–papel: `(company_identifier, company_user_identifier, role_identifier)`;
- papel–permissão: `(company_identifier, role_identifier, permission_identifier)`;
- usuário–permissão: `(company_identifier, company_user_identifier, permission_identifier)`;
- módulo da empresa: `(company_identifier, module_identifier)`;
- identificadores externos de clientes e visitas: se a semântica for integração por empresa, unicidade `(company_identifier, external_app, external_id)` após verificar índices e duplicidades. Não manter restrição global que impeça duas empresas de usar o mesmo identificador externo.

Índices duplicados existentes não devem ser removidos sem identificar se são constraints ou índices independentes e sem confirmar dependências.

### Fase A6 — NOT NULL e defaults

Impor `company_identifier NOT NULL` somente depois de backfill e validação sem nulos:
- `customers`, `customer_contacts`, `addresses` e `roles`: dados já preenchidos; validar novamente e impor NOT NULL.
- `equipments`, `work_orders`, `quotations`, `approval_events`, `role_modules`, `role_screens`, `user_modules` e `user_screens`: tabelas vazias; adicionar coluna e impor NOT NULL sem default artificial.
- `company_user_roles`: backfill pelo vínculo pai, validar papéis no mesmo tenant e impor NOT NULL.
- `visits`: NOT NULL bloqueado até resolução documentada da única visita sem evidência de tenant.

Outras correções:
- remover default `gen_random_uuid()` de `work_orders.customer_identifier`;
- tornar `work_orders.customer_identifier NOT NULL` (tabela vazia e cliente obrigatório no modelo de OS);
- tornar `user_permissions.allow NOT NULL` (tabela vazia);
- tornar `company_user_roles.identifier NOT NULL` somente após confirmar ausência de NULL e criar unicidade apropriada;
- manter `company_users.role_identifier` sem alteração até deliberação formal sobre a fonte única de papéis, reconciliação dos dados e auditoria das dependências.

---

## 5. SQL de referência e gates de segurança

Os blocos abaixo são referências para a migration final. Não foram executados. A migration deverá ser revisada contra o schema novamente imediatamente antes da execução.

### 5.1 Preflight obrigatório

    DO $preflight$
    BEGIN
      IF (SELECT count(*) FROM public.companies) <> 1 THEN
        RAISE EXCEPTION 'Preflight: quantidade de empresas mudou; revisar backfill.';
      END IF;

      IF EXISTS (SELECT 1 FROM public.customers WHERE company_identifier IS NULL) THEN
        RAISE EXCEPTION 'Preflight: customers ainda possui tenant nulo.';
      END IF;

      IF EXISTS (SELECT 1 FROM public.customer_contacts WHERE company_identifier IS NULL) THEN
        RAISE EXCEPTION 'Preflight: customer_contacts ainda possui tenant nulo.';
      END IF;

      IF EXISTS (SELECT 1 FROM public.addresses WHERE company_identifier IS NULL) THEN
        RAISE EXCEPTION 'Preflight: addresses ainda possui tenant nulo.';
      END IF;

      IF EXISTS (SELECT 1 FROM public.roles WHERE company_identifier IS NULL) THEN
        RAISE EXCEPTION 'Preflight: roles ainda possui tenant nulo.';
      END IF;

      IF EXISTS (
        SELECT 1
        FROM public.visits v
        LEFT JOIN public.customers c ON c.identifier = v.customer_identifier
        LEFT JOIN public.addresses a ON a.identifier = v.address_identifier
        WHERE v.customer_identifier IS NOT NULL
          AND (c.company_identifier IS NULL
            OR (a.identifier IS NOT NULL AND a.company_identifier <> c.company_identifier))
      ) THEN
        RAISE EXCEPTION 'Preflight: visita com cliente/endereço sem coerência de tenant.';
      END IF;

      IF EXISTS (
        SELECT 1
        FROM public.company_user_roles cur
        LEFT JOIN public.company_users cu ON cu.identifier = cur.company_user_identifier
        LEFT JOIN public.roles r ON r.identifier = cur.role_identifier
        WHERE cu.identifier IS NULL
           OR r.identifier IS NULL
           OR cu.company_identifier <> r.company_identifier
      ) THEN
        RAISE EXCEPTION 'Preflight: associação de papel órfã ou cross-tenant.';
      END IF;

      IF EXISTS (
        SELECT 1
        FROM public.visits v
        WHERE v.customer_identifier IS NULL
          AND v.address_identifier IS NULL
          AND v.company_identifier IS NULL
      ) THEN
        RAISE EXCEPTION 'Preflight: existe visita sem evidência de tenant; resolver antes da fase NOT NULL.';
      END IF;

      IF EXISTS (
        SELECT 1
        FROM public.customers
        WHERE NULLIF(regexp_replace(cpf_cnpj, '[^0-9]', '', 'g'), '') IS NOT NULL
        GROUP BY company_identifier,
                 NULLIF(regexp_replace(cpf_cnpj, '[^0-9]', '', 'g'), '')
        HAVING count(*) > 1
      ) THEN
        RAISE EXCEPTION 'Preflight: CPF/CNPJ duplicado após canonicalização no mesmo tenant.';
      END IF;
    END
    $preflight$;

O preflight deverá ser complementado com validações de existência de colunas, constraints, índices, triggers, funções e duplicidades específicas. A visita sem cliente/endereço é um bloqueio conhecido e deverá permanecer explicitamente tratada; não remover essa condição do preflight sem decisão sobre o registro.

### 5.2 Backfill das associações

    UPDATE public.company_user_roles cur
       SET company_identifier = cu.company_identifier
      FROM public.company_users cu
     WHERE cu.identifier = cur.company_user_identifier
       AND cur.company_identifier IS NULL;

    UPDATE public.visits v
       SET company_identifier = c.company_identifier
      FROM public.customers c
     WHERE c.identifier = v.customer_identifier
       AND v.company_identifier IS NULL;

O backfill de visitas deverá ser precedido pela verificação de que o endereço, quando presente, pertence à mesma empresa do cliente. A linha sem cliente/endereço não será preenchida por esse SQL.

Para associações vazias, o tenant será obtido da entidade proprietária:
- `role_modules` e `role_screens`: `roles.company_identifier`;
- `user_modules` e `user_screens`: `company_users.company_identifier`.

A coluna não deverá receber default global nem aceitar tenant alegado pelo cliente.

### 5.3 Exemplo de chave composta

    CREATE UNIQUE INDEX customers_company_identifier_identifier_uq
        ON public.customers (company_identifier, identifier);

    ALTER TABLE public.customer_contacts
      ADD CONSTRAINT customer_contacts_customer_company_fk
      FOREIGN KEY (company_identifier, customer_identifier)
      REFERENCES public.customers (company_identifier, identifier)
      NOT VALID;

    ALTER TABLE public.customer_contacts
      VALIDATE CONSTRAINT customer_contacts_customer_company_fk;

O padrão será aplicado às relações da matriz da seção 4.4, usando nomes únicos e preservando a semântica atual de ON DELETE/ON UPDATE após revisão de cada FK. Não copiar automaticamente ações referenciais de uma FK antiga sem verificar o efeito sobre histórico e dados operacionais.

### 5.4 Função de snapshot de endereço

A função atual não pode permanecer como está. A substituição deverá:
- utilizar `addresses.identifier` como chave de busca;
- utilizar `display_address` ou composição explícita de `street`, número e demais campos disponíveis;
- copiar latitude/longitude;
- validar que o endereço pertence à mesma empresa da visita;
- validar coerência de cliente conforme a regra final de endereços sem cliente;
- falhar com erro claro se o endereço informado não existir ou pertencer a outro tenant;
- preservar o snapshot textual da visita após a criação, sem depender de alterações futuras no endereço.

O corpo final depende da confirmação da regra para endereços sem `customer_identifier` e da inspeção do trigger (evento, colunas e momento de disparo). A função não será substituída por implementação presumida.

---

## 6. Reconciliações que bloqueiam a conclusão integral da Parte A

| Bloqueio | Evidência | Tratamento necessário |
|---|---|---|
| Visita sem cliente/endereço | Uma das 26 visitas não possui cliente, endereço ou OS | Tenant confirmado como Winservice; manter vínculos ausentes sem fabricar dados |
| Visitas sem OS | `work_order_identifier` nulo em todas; `work_orders` vazia | Permitido: OS é opcional; não criar OS fictícia |
| Papéis legados divergentes | Duas associações na tabela de junção diferem de `company_users.role_identifier` | Decidir fonte operacional e reconciliar sem perda |
| Catálogos globais e adoção local | Catálogos têm registros, mas modelo físico de adoção não está aplicado | Individualizar desenho e preservar distinção global/tenant |
| Catálogo de permissões | `permissions` atual é tenant-scoped; ADR-0011 exige catálogo global versionado e customizações | Avaliar e aprovar a proposta física específica antes de fechar FKs de permissão |
| Identidade Auth/ERP | `users` global; `auth_user_identifier` pode exigir unicidade 1:1; `approval_events.created_by` referencia `auth.users` | Validar mapeamento e unicidade sem presumir equivalência entre IDs |
| Trigger de endereço | Função consulta colunas inexistentes | Inspecionar trigger e fechar regra para endereço genérico |
| Baseline de migrations | Nenhuma migration registrada; repositório de implementação não identificado | Definir local versionado oficial antes da execução |

### 6.1 Papéis — tratamento proposto

A migration pode adicionar `company_identifier` a `company_user_roles` a partir do vínculo pai e criar chaves compostas, porque a empresa do vínculo e do papel é verificável. A ADR-0017 exige papéis no contexto do vínculo, mas a documentação ainda não declarou a tabela de associação como fonte única nem aprovou a remoção do campo legado. Portanto, até deliberação formal, isso não autoriza:
- remover `company_users.role_identifier`;
- apagar associações;
- escolher uma das duas fontes;
- transformar `company_admin` em papel;
- promover qualquer usuário a `superuser`.

### 6.2 Visitas — tratamento proposto

As 25 visitas com cliente poderão receber tenant derivado do cliente após validação cliente/endereço. A visita sem evidência deve permanecer bloqueada. A coluna poderá ser adicionada nullable e o backfill parcial preparado, mas a migration não poderá declarar a fundação concluída nem impor NOT NULL até resolução.

Os estados legados permanecem inalterados. A associação obrigatória a OS deverá ser resolvida antes de ativar o fluxo operacional, mas não por criação automática de registros.

---

## 7. Índices e desempenho

Além dos índices únicos necessários às FKs compostas, criar/revisar índices não únicos nas colunas de relacionamento e tenant.

Critérios:
- índice começa por `company_identifier` quando o padrão de consulta filtra por tenant;
- índices de FKs compostas cobrem `(company_identifier, fk_identifier)`;
- não duplicar índice com mesma definição;
- não remover índice legado até verificar dependências, consultas e plano;
- considerar `CONCURRENTLY` quando volume e janela de escrita exigirem; separar operação de transação e verificar índice inválido após falha;
- verificar planos com `EXPLAIN (ANALYZE, BUFFERS)` em ambiente de teste.

Tabelas pequenas/vazias não justificam índices adicionais indiscriminados. Cada índice deverá corresponder a FK, constraint ou consulta documentada.

---

## 8. Plano de rollback e recuperação

O rollback será definido por migration e testado em ambiente restaurável.

1. Backup/snapshot restaurável obrigatório.
2. Preferir alterações aditivas e constraints `NOT VALID` seguidas de `VALIDATE CONSTRAINT`.
3. Não apagar registros, IDs, eventos ou históricos.
4. Não reverter backfills já concluídos.
5. Não remover colunas tenant depois que novas gravações dependerem delas.
6. Se FK composta falhar, interromper e corrigir dados; não desabilitar constraint para prosseguir.
7. Se a substituição da unicidade CPF/CNPJ falhar, manter constraint anterior até que a nova possa ser criada e validada; planejar janela sem intervalo desprotegido.
8. Para mudança de default ou NOT NULL, registrar estado anterior e comando de reversão.
9. Preservar versão anterior da função de snapshot e executar regressão.
10. Preferir forward-fix ou restauração de snapshot, não exclusão manual sem inventário.

A estratégia final deverá indicar etapas transacionais e operações fora de transação, como índices CONCURRENTLY.

---

## 9. Testes de aceitação da Parte A

A Parte A somente será considerada concluída quando:
1. Nenhuma tabela tenant-scoped incluída permanece com `company_identifier` NULL.
2. Todas as tabelas tenant-scoped incluídas possuem FK válida para `companies(identifier)` quando aplicável.
3. Cada tabela com `identifier` tenant-scoped possui chave única `(company_identifier, identifier)`; `approval_events` usa `(company_identifier, id)`.
4. Cliente, contato, endereço, equipamento, OS, visita, orçamento e evento de aprovação não relacionam registros de empresas diferentes.
5. Associação usuário–papel não aponta para vínculo ou papel de outra empresa.
6. Associação usuário/papel–permissão não cruza empresas, respeitando o desenho final ADR-0011.
7. Associações de módulos/telas não apontam para papel ou vínculo de outra empresa.
8. CPF/CNPJ normalizado pode repetir em empresas diferentes e não se repete dentro da mesma empresa.
9. Pontuação e espaços não criam duplicidade aparente; zeros à esquerda são preservados.
10. Nulos e strings vazias não geram conflito no índice CPF/CNPJ.
11. `work_orders.customer_identifier` não possui default aleatório e não aceita OS sem cliente.
12. `user_permissions.allow` não aceita NULL.
13. `company_user_roles.identifier` não aceita NULL se adotado como identificador estável.
14. Função de snapshot usa colunas existentes e rejeita endereço de outro tenant.
15. Nenhuma visita recebe OS, cliente, endereço ou status inventado.
16. Nenhum registro legado é excluído.
17. Todas as FKs novas são validadas; nenhuma permanece NOT VALID ao final.
18. Índices únicos não apresentam duplicidades nem estados inválidos.
19. Inserções/atualizações com combinações tenant A→tenant B são rejeitadas pelo PostgreSQL.
20. Regressão de clientes, contatos, endereços, equipamentos, OS, visitas e orçamentos passa em ambiente de teste.

A Parte A não substitui testes de autorização da Parte B. FKs compostas protegem integridade referencial, mas não substituem RLS nem validação no backend.

---

## 10. Critérios de liberação para execução

Autorizar execução somente depois de:
- aprovação deste plano e dos pontos de decisão;
- resolução documentada da visita sem tenant;
- definição do tratamento das 26 visitas sem OS;
- decisão sobre divergência entre `company_users.role_identifier` e `company_user_roles`;
- definição do desenho físico do catálogo de permissões e catálogos de tarefa/requisito;
- inspeção final do trigger de endereço;
- definição do repositório oficial de migrations e baseline;
- backup restaurável e ensaio em ambiente não produtivo;
- apresentação do SQL final completo, dividido em migrations numeradas e com rollback.

**A aprovação deste documento não autoriza execução no Supabase.** A autorização operacional deverá mencionar expressamente o projeto, as migrations e o escopo aprovado.

---

## 11. Fontes e rastreabilidade

- `documentacao/decisoes/ADR-0017-tenancy-compartilhada-autorizacao-multiempresa.md`
- `documentacao/decisoes/ADR-0011-catalogo-permissoes-global-versionado.md`
- `documentacao/decisoes/ADR-0016-historico-append-only.md`
- `documentacao/ESPECIFICACAO_FISICA_FUNDACAO_MULTIEMPRESA_MVP.md` — v0.3.0
- `documentacao/MODELO_LOGICO_DADOS_OPERACAO.md`
- `documentacao/MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md`
- `documentacao/PROTOCOLO_DE_ATUACAO_IA_GITHUB.md`
- Auditoria somente leitura realizada em 2026-11-08 no projeto Supabase identificado no cabeçalho.

### Histórico

- 2026-11-08 — v0.1.0: proposta da Parte A preparada após concordância do responsável em separar estrutura/integridade da Parte B de autorização. Inclui evidências atuais do banco, classificação, sequência, SQL de referência, bloqueios, testes e rollback. Nenhuma alteração de banco executada.
