# Auditoria técnica do Supabase — ERP Winservice

**Data da auditoria:** 2026-11-07  
**Projeto Supabase:** Winservice  
**Project ID:** `voaovwphncwbrwtcqnsc`  
**Versão PostgreSQL:** 17.6  
**Escopo principal:** schema `public`  
**Natureza:** auditoria técnica somente leitura  
**Estado:** relatório de diagnóstico; nenhuma correção aplicada

## 1. Objetivo e escopo

Registrar uma fotografia técnica do estado encontrado no projeto Supabase Winservice em 07/11/2026, com foco em:

- segurança, privilégios e Row Level Security (RLS);
- isolamento multiempresa e integridade referencial;
- funções, triggers e mecanismos de integração;
- estruturas de permissões;
- índices e alertas de desempenho;
- evidências de implementação relacionadas às decisões arquiteturais vigentes.

A auditoria foi realizada por inspeção de metadados, definições SQL e resultados de ferramentas de análise. Não foram consultados registros pessoais de clientes nem executadas funções de negócio.

## 2. Limites e método

Foram utilizadas consultas de leitura e ferramentas de inspeção do Supabase. Não foram executados comandos de escrita, DDL, migrations, chamadas de RPC de negócio ou operações de deploy.

Não foram alterados:
- tabelas, colunas, constraints, índices ou dados;
- policies RLS, grants ou roles;
- funções, triggers ou configurações;
- Edge Functions ou migrations.

A auditoria não incluiu teste de penetração, tentativa de acesso cruzado, teste funcional com usuários reais, inspeção dos workflows n8n, nem inspeção de código externo de API/backend. Portanto, conclusões sobre a camada de banco não devem ser generalizadas para componentes externos não inspecionados.

## 3. Inventário observado

O inventário consultado anteriormente nesta auditoria registrou:

| Indicador | Resultado |
|---|---:|
| Tabelas no schema public | 38 |
| Tabelas com RLS habilitada | 38 |
| Tabelas com FORCE RLS | 0 |
| Tabelas sem policies | 26 |
| Policies públicas | 23 |
| Funções/procedures públicas | 11 |
| Funções SECURITY DEFINER | 2 |
| Triggers não internos | 18 |
| Foreign keys | 103 |

Os números representam o estado consultado na data da auditoria. Não devem ser confundidos com snapshots de 06/11/2026.

## 4. Achados de segurança

### SEC-01 — Policies sem restrição por empresa

**Classificação:** crítico  
**Estado:** confirmado na configuração consultada

Foram identificadas policies com expressões permissivas `true`, sem condição de isolamento por empresa, em tabelas relevantes. Entre os exemplos observados:

- `companies`: policy `ALL` com condição permissiva;
- `customers`: policies de leitura e inserção sem filtro por empresa;
- `addresses`: policies de leitura, inserção e atualização sem filtro por empresa;
- `customer_contacts`: policies de leitura e inserção sem filtro por empresa;
- `visits`: policies de leitura, inserção e atualização sem filtro por empresa.

As policies identificadas são direcionadas a papéis autenticados conforme a configuração consultada. A existência de RLS habilitada não neutraliza uma policy permissiva que autoriza a operação.

**Impacto:** risco de acesso ou alteração de registros pertencentes a outras empresas por usuários autenticados, conforme os privilégios e demais caminhos de acesso efetivos.

**Recomendação:** definir e implementar uma matriz RLS por entidade, empresa, usuário e operação. Testes positivos e negativos devem ser executados em ambiente controlado antes de liberar acesso operacional.

### SEC-02 — Privilégios amplos em tabelas públicas

**Classificação:** alta  
**Estado:** grants observados; impacto efetivo depende de RLS e papel

A consulta a `information_schema.role_table_grants` encontrou privilégios de tabela para `anon` e `authenticated` em todas as 38 tabelas públicas. Os privilégios observados incluem SELECT, INSERT, UPDATE, DELETE, TRUNCATE, REFERENCES e TRIGGER.

Isso caracteriza concessão ampla e contraria o princípio do menor privilégio. Não significa, por si só, que todas essas operações possam ser realizadas sobre linhas: RLS, policies, privilégios efetivos e caminhos privilegiados também determinam o resultado.

A consulta de roles indicou que `anon` e `authenticated` não possuem `BYPASSRLS`; `service_role` e `postgres` possuem.

**Recomendação:** revisar grants por tabela e papel, concedendo somente os privilégios necessários. Relacionar cada grant às policies e aos fluxos que realmente precisam dele.

### SEC-03 — Função SECURITY DEFINER de ingestão de eventos

**Classificação:** crítica/alta  
**Estado:** código e permissões confirmados; exploração não testada

A função `public.authorize_integration_sync_event` foi encontrada com:

- proprietário `postgres`;
- `SECURITY DEFINER`;
- execução concedida a `anon`, `authenticated` e `service_role`;
- argumentos que incluem identificador da integração, tipo/operação, identificador externo, payload JSON e direção;
- inserção de eventos em `integration_sync_queue`.

O código valida existência e status da integração, provider, parâmetros e formato do payload, e calcula uma chave de idempotência. Não foi identificada no corpo inspecionado validação de segredo, assinatura ou prova de autenticidade do remetente.

Como a função é executada com privilégios do proprietário, um chamador autorizado a executá-la pode acionar operações além dos privilégios normais de seu papel. A ausência de autenticação do remetente no corpo representa risco de injeção não autorizada de eventos caso o identificador de integração seja obtido.

**Limite da conclusão:** não houve tentativa de chamada nem exploração. Não foi verificada a existência de uma camada externa que autentique o remetente antes da chamada.

**Recomendação:** revisar o fluxo completo de entrada, restringir EXECUTE a um componente confiável e exigir validação criptográfica/autenticação do evento antes da inserção.

**Divergência arquitetural:** a função contém lógica específica do Auvo, embora a decisão vigente para a Máquina de Estados V1 exclua Auvo do escopo atual. A existência da função não comprova que o fluxo esteja ativo. Classificar como implementação residual até verificar dependências e uso.

### SEC-04 — Função SECURITY DEFINER can_view_expense

**Classificação:** média  
**Estado:** definição e grants confirmados

A função `public.can_view_expense` é `SECURITY DEFINER` e executável por `PUBLIC`, incluindo `anon` e `authenticated`.

A lógica observada verifica se o usuário autenticado é criador da despesa ou possui papel `superuser`/`financial` na empresa correspondente. O retorno é booleano.

Não foi identificada, apenas pelo corpo analisado, exposição direta dos dados da despesa. Ainda assim, a execução pública/anônima deve ser revista e limitada ao conjunto mínimo de papéis necessário.

### SEC-05 — Search path de funções

**Classificação:** média  
**Estado:** alerta do Security Advisor

O Security Advisor apontou sete funções com `search_path` mutável. Entre os nomes identificados na análise estão:

- `set_updated_fields`;
- `set_created_by`;
- `ensure_primary_address`;
- `set_created_fields`;
- `set_visit_address_snapshot`;
- `update_updated_at`;
- `prevent_changes`.

**Recomendação:** revisar cada função e fixar explicitamente um `search_path` seguro, com referências qualificadas quando apropriado. A alteração deve ser planejada e testada, não aplicada durante esta auditoria.

### SEC-06 — Proteção contra senhas comprometidas

**Classificação:** média  
**Estado:** alerta do Security Advisor

O Security Advisor indicou que a proteção contra senhas comprometidas está desabilitada.

**Recomendação:** avaliar e habilitar a proteção nas configurações de autenticação, após considerar o impacto sobre o fluxo de cadastro e troca de senhas.

## 5. Integridade e isolamento multiempresa

### TEN-01 — Relações sem garantia composta de empresa

**Classificação:** alta  
**Estado:** estrutura de constraints inspecionada

Foram observadas relações em que a empresa e a entidade relacionada são validadas por foreign keys independentes, sem constraint composta que garanta a mesma empresa nos dois lados.

Exemplo estrutural em `addresses`:
- referência de `company_identifier` para empresa;
- referência de `customer_identifier` para cliente;
- ausência de validação composta que assegure que o cliente pertença à empresa informada no endereço.

Padrão semelhante foi observado em `customer_contacts`.

Também foram identificados relacionamentos operacionais que dependem de cadeia relacional, como equipamentos, ordens de serviço, visitas e orçamentos. As foreign keys individuais não comprovam, por si sós, a coerência de empresa entre todas as entidades associadas.

**Impacto:** o banco pode aceitar referências válidas individualmente, mas incoerentes entre empresas, se não houver validação adicional em triggers, funções ou backend.

**Recomendação:** formalizar primeiro o modelo de tenancy e os caminhos de herança de empresa. Em seguida, avaliar constraints compostas e políticas RLS por entidade. Não adicionar `company_identifier` indiscriminadamente: catálogos globais e entidades dependentes exigem modelagem própria.

### TEN-02 — Tabelas sem company_identifier direto

**Classificação:** requer análise por entidade  
**Estado:** confirmado no inventário

Foram identificadas tabelas sem coluna `company_identifier`, incluindo:

`companies`, `company_user_roles`, `contact_channels`, `documents`, `entity_types`, `equipments`, `integration_webhooks`, `modules`, `phonenumbers`, `quotations`, `role_modules`, `role_screens`, `screens`, `task_types`, `user_modules`, `user_screens`, `users`, `visit_requirements`, `visit_type_requirement`, `visit_types`, `visits`, `work_order_equipment` e `work_orders`.

A ausência pode ser intencional em catálogos globais ou entidades que herdam empresa por relacionamento. Deve ser avaliada individualmente; não é automaticamente um defeito.

## 6. Funções e triggers

### DB-01 — Trigger set_visit_address_snapshot

**Classificação:** alta  
**Estado:** incompatibilidade entre definição da função e estrutura da tabela observada

A função associada ao snapshot de endereço referencia colunas `address` e `address_identifier` na tabela `addresses`. A estrutura inspecionada de `addresses` apresenta, entre outras, as colunas `identifier`, `street`, `street_number` e `display_address`, sem as duas colunas referenciadas pela função.

Quando o caminho do trigger executa essa consulta, há risco de erro por referência a coluna inexistente.

**Impacto provável:** falha na criação de visitas que acionem a consulta de endereço.

**Limite:** não foi feita inserção de visita para reproduzir o comportamento.

**Recomendação:** alinhar a função ao schema vigente e criar teste de regressão antes da publicação.

### DB-02 — Trigger de atualização em companies

**Classificação:** média  
**Estado:** comportamento incompatível com a finalidade aparente

Foi observado trigger `AFTER UPDATE` que chama `set_updated_fields`, função que altera os campos de `NEW`. Em triggers AFTER, a alteração de `NEW` não modifica a linha já gravada.

**Impacto provável:** os campos de auditoria podem não ser atualizados como esperado por esse mecanismo.

**Recomendação:** revisar o momento do trigger e a estratégia de preenchimento de campos de auditoria.

### DB-03 — Geração de segredo de webhook

**Classificação:** ponto positivo com ressalva  
**Estado:** mecanismo encontrado

Foi encontrada função `generate_integration_webhook_secret` associada a trigger de integração, com geração aleatória de 32 bytes em representação hexadecimal quando o segredo está ausente.

A geração do segredo não comprova sua validação em todos os pontos de entrada. A função de ingestão de eventos descrita em SEC-03 não apresentou validação desse segredo no corpo inspecionado.

## 7. Desempenho e índices

O Performance Advisor retornou:

| Achado | Quantidade |
|---|---:|
| Foreign keys sem índice de cobertura | 89 |
| Pares de índices duplicados | 2 |
| Policies com oportunidade de otimização | 1 |
| Funções com alerta de search_path | 7 |

Os pares de índices duplicados apontados estão em `customers` e `visits`, sobre `(external_app, external_id)`.

**Recomendação:** avaliar índices com base em consultas reais, cardinalidade, planos de execução e custo de escrita. Não remover índices somente com base no alerta; verificar dependências e constraints.

## 8. Conformidade com decisões arquiteturais

Esta seção compara evidências do banco com decisões registradas no índice ADR. A presença de tabelas não é tratada como prova de implementação funcional.

| Decisão | Evidência encontrada | Avaliação |
|---|---|---|
| ADR-0002 — Supabase como fonte de verdade | Banco central com estrutura significativa | Estrutura presente; uso exclusivo por todos os módulos não verificado |
| ADR-0008 — Auvo opcional/fora da V1 atual | Função e lógica de integração específicas do Auvo | Resíduo de implementação encontrado; fluxo ativo não verificado |
| ADR-0011 — Catálogo de permissões global versionado | Estruturas de roles, permissions e associações | RBAC parcial; versionamento, publicação global e adesão por empresa não comprovados |
| ADR-0012 — Autonomia limitada da Isa | Policies permissivas; ausência de policies em algumas entidades operacionais; autoria não aplicada uniformemente | Não comprovado no banco; RLS atual não demonstra cumprimento |
| ADR-0014 — Documentos legais e versões | Tabela genérica `documents` | Não comprova versionamento legal, vigência ou aceite associado à versão apresentada |

### ADR-0011 — observação específica

As estruturas encontradas incluem `permissions`, `roles`, `role_permissions`, `user_permissions`, `company_users`, `company_user_roles`, `company_modules`, `modules`, `role_modules`, `role_screens`, `user_modules` e `user_screens`.

Isso indica uma base de RBAC. Não foi comprovada a estrutura necessária para:
- catálogo padrão global independente das empresas;
- versões publicadas do catálogo;
- adesão voluntária por empresa;
- preservação de personalizações;
- histórico e restauração de padrões.

### ADR-0012 — observação específica

A decisão exige limitar consultas da Isa ao cliente do atendimento, permitir criação de entidades específicas, proibir exclusão e restringir alterações aos registros criados pela própria Isa, com enforcement no backend/API.

As policies inspecionadas não demonstram esses limites. Algumas tabelas operacionais não possuem policies, e as permissões de `customers` e `visits` não aplicam filtro de autoria/empresa.

A existência de workflows n8n ou backend intermediário pode acrescentar controles, mas esses componentes não foram auditados nesta etapa.

## 9. Migrations e rastreabilidade

A listagem de migrations não foi consolidada com evidência suficiente nesta auditoria para afirmar ausência de histórico. A situação deve permanecer **não verificada** até uma nova consulta documentada e comparação com arquivos versionados.

O repositório consultado é documental; não foi identificado nesta etapa um repositório de implementação contendo migrations para comparação com o schema atual.

Os inventários técnicos citados no status do projeto devem ser localizados e comparados com o estado atual antes de servirem como baseline.

## 10. Matriz de situação técnica

| Frente | Situação na data da auditoria |
|---|---|
| Banco central Supabase | Estrutura presente |
| RLS habilitada | Presente nas 38 tabelas |
| Isolamento multiempresa | Não demonstrado; há policies sem filtro de empresa |
| Integridade referencial multiempresa | Parcial; constraints compostas insuficientes em relações examinadas |
| Clientes e endereços | Estrutura presente; policies permissivas |
| Equipamentos e OS | Estrutura presente; autorização RLS não comprovada |
| Visitas | Estrutura presente; trigger de snapshot com incompatibilidade identificada |
| Orçamentos | Estrutura presente; integridade multiempresa requer validação |
| RBAC | Estruturas presentes; catálogo global versionado não comprovado |
| Autonomia da Isa | Não comprovada no banco |
| Integração | Estrutura presente; função de ingestão requer revisão de segurança |
| Documentos legais versionados | Não comprovados |
| Edge Functions | Não verificadas nesta auditoria por ausência de evidência consolidada |
| Migrations | Não verificadas |
| WeWeb / FlutterFlow | Fora do escopo de inspeção de implementação desta auditoria |

## 11. Recomendações priorizadas

1. Revisar autenticação, autorização e privilégios da função `authorize_integration_sync_event`.
2. Redesenhar e validar a matriz RLS, começando por `companies`, `customers`, `addresses`, `customer_contacts` e `visits`.
3. Reduzir grants de `anon` e `authenticated` conforme o princípio do menor privilégio.
4. Formalizar o modelo de tenancy e reforçar a integridade entre entidades relacionadas.
5. Comprovar o enforcement da autonomia da Isa no backend/API e no banco.
6. Corrigir a incompatibilidade da função `set_visit_address_snapshot`.
7. Revisar o trigger de atualização de `companies`.
8. Planejar o catálogo de permissões global versionado conforme ADR-0011.
9. Revisar funções com `search_path` mutável e a proteção contra senhas comprometidas.
10. Validar migrations e estabelecer rastreabilidade entre schema, código e ambiente.
11. Avaliar índices duplicados e FKs sem índice de cobertura com base em uso real.
12. Projetar a gestão de documentos legais e versões conforme ADR-0014.

Nenhuma dessas recomendações foi executada nesta auditoria.

## 12. Limitações e itens não verificados

- Não foram consultados dados pessoais de clientes.
- Não foram realizados testes de exploração ou de acesso cruzado.
- Não foram chamadas funções de negócio.
- Não foram inspecionados workflows n8n ou configurações internas do Chatwoot.
- Não foi auditado código de API/backend externo.
- Não foram realizados testes funcionais com usuários reais.
- Não foram realizados testes de carga nem análise abrangente de planos de execução.
- Não foi consolidada a comparação entre migrations e o schema do ambiente.
- Não foi comprovado se a lógica de Auvo ainda está em uso.
- Não foi comprovada a ausência de controles externos que complementem o banco.

## 13. Integridade e resultado da auditoria

A atividade foi somente leitura. Nenhuma alteração foi realizada no Supabase.

Este documento registra achados, riscos e recomendações observados em 07/11/2026. Não representa aprovação de arquitetura, homologação funcional, certificação de segurança ou autorização para executar correções.

### Histórico de alterações

- 2026-11-07 — v1.0.0: criação do relatório inicial da auditoria técnica somente leitura do projeto Supabase Winservice.
