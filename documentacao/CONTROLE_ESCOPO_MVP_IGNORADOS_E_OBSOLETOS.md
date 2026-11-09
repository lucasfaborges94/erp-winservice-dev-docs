# Controle de escopo do MVP — itens ignorados, futuros e obsoletos

**Versão:** 0.3.0  
**Data de criação:** 2026-11-08  
**Estado:** inventário incremental  
**Escopo de referência:** MVP operacional do ERP Winservice

## 1. Objetivo

Manter um inventário único dos elementos encontrados durante a análise do ERP que não fazem parte do MVP atual, foram substituídos, pertencem a etapas futuras ou ainda precisam de classificação.

O inventário será atualizado progressivamente conforme documentos, schema, código e processos forem examinados. A ausência de um item nesta lista não significa que ele esteja aprovado para o MVP; significa apenas que ainda não foi classificado.

## 2. Significado das classificações

| Classificação | Significado |
|---|---|
| **FORA_MVP** | Não será implementado nem considerado requisito do MVP atual. |
| **FUTURO** | Ideia ou capacidade reservada para uma etapa posterior, sem compromisso de implementação. |
| **OBSOLETO_SUBSTITUÍDO** | Solução ou estrutura conceitual descartada e substituída por outra. |
| **PENDENTE_CLASSIFICAÇÃO** | Encontrado, mas ainda não há informação suficiente para decidir sua inclusão ou exclusão. |

A classificação documental não autoriza excluir tabelas, funções, colunas, dados, grants, policies, código ou integrações. Qualquer remoção física exigirá análise de dependências, plano de migração e aprovação específica.

## 3. Itens identificados

| Item | Tipo | Classificação | Destino / tratamento | Evidência e observações |
|---|---|---|---|---|
| `public.documents` | Catálogo de documentos da empresa | **PENDENTE_CLASSIFICAÇÃO** | Catálogo geral de documentos, incluindo evidências e outros documentos da empresa; os arquivos serão armazenados em outro local. A inclusão funcional no MVP ainda precisa ser confirmada conforme os fluxos de evidências/documentos. | Finalidade esclarecida pelo responsável em 2026-11-08. A tabela possui metadados de arquivo e armazenamento (`storage_disk`, `storage_path`, `filename`, `mime_type`, `size`, entre outros). Não presumir que os binários serão armazenados no PostgreSQL. |
| `public.work_order_equipment` | Relação física OS–equipamento | **OBSOLETO_SUBSTITUÍDO** | Não utilizar no desenho atual. O equipamento será relacionado à visita, e a visita à OS. A relação direta OS–equipamento não foi finalizada e foi substituída conceitualmente pela associação no nível da visita. Não remover fisicamente a tabela sem análise e autorização próprias. | Deliberação do responsável em 2026-11-08; a tabela existe no schema, mas a relação direta não representa o modelo operacional vigente. |
| `public.contact_channels` | Catálogo de canais de contato do cliente | **PENDENTE_CLASSIFICAÇÃO** | Finalidade definida: registrar canais como WhatsApp, Instagram, e-mail e telefone. A inclusão no MVP ainda depende da confirmação dos requisitos do cadastro/atendimento. | Esclarecimento do responsável em 2026-11-08. |
| `public.a_blank_template` | Template técnico de criação de tabelas | **FORA_MVP** | Não é entidade funcional do ERP nem requisito de negócio do MVP. Tratar apenas como artefato auxiliar de desenvolvimento, se ainda necessário. | Esclarecimento do responsável em 2026-11-08: template utilizado para criação de novas tabelas. |
| `public.entity_types` e `public.entity_status` | Catálogos genéricos de entidades e status | **FORA_MVP** | Não adotar como dependência do MVP. Poderão ser reutilizados pontualmente se uma necessidade concreta justificar, após avaliação. Não foram utilizados em produção. | Esclarecimento do responsável em 2026-11-08. |
| `public.integrations`, `public.integration_sync_queue` e `public.integration_webhooks` | Infraestrutura de integrações | **FORA_MVP** | Excluir do escopo funcional e técnico do MVP atual. Não implementar nem depender dessas estruturas nesta etapa. Não remover fisicamente sem análise de dependências e autorização. | Deliberação do responsável em 2026-11-08. |
| `public.expenses` | Tabela existente no Supabase | **FORA_MVP** | Reservada ao futuro módulo Financeiro. Ignorar no desenho e desenvolvimento do MVP operacional. Reavaliar quando o Financeiro for especificado. | A listagem do schema `public` confirmou a tabela, RLS habilitado e zero registros na consulta realizada. Possui referências a `companies` (`company_identifier`) e `users` (`created_by`, `updated_by`). Não excluir nem alterar agora. |
| `public.can_view_expense(uuid)` | Função SQL existente | **FORA_MVP** | Ignorar no escopo atual e reavaliar junto ao módulo Financeiro. | Função associada ao domínio de despesas, registrada na auditoria anterior. A classificação não autoriza alteração ou remoção de função ou privilégios. |
| Módulo Financeiro — despesas, contas, recebimentos, pagamentos, conciliação e indicadores | Módulo de negócio | **FUTURO** | Fora do MVP operacional atual; detalhar em etapa própria. | O mapa de módulos registra o escopo Financeiro como “a detalhar”. A tabela `public.expenses` é o elemento físico já identificado; não presumir que todas as demais entidades financeiras existam no banco. |
| Check-in inicial automático da visita por GPS (permanência inicial de 5 minutos dentro de 50 metros, parâmetros configuráveis) | Funcionalidade | **FORA_MVP** | Não implementar no MVP. Manter como possibilidade futura, sem incluí-la nos critérios de aceite atuais. | O modelo lógico de Operação registra expressamente a funcionalidade como futura e fora do MVP. |
| Máquina B / execução temporal independente da visita | Modelo conceitual | **OBSOLETO_SUBSTITUÍDO** | Não implementar. O ciclo operacional e seus eventos ficam concentrados na máquina de estados da visita e em `visit_events`. | Decisões registradas no modelo lógico: a Máquina B foi eliminada do MVP. |
| `visit_execution_sessions` | Entidade/tabela conceitual proposta anteriormente | **OBSOLETO_SUBSTITUÍDO** | Não criar para o MVP. Usar `visit_events` para registrar acontecimentos operacionais e temporais. | O modelo lógico declara que essa entidade foi substituída por `visit_events`. A existência física de tabela homônima no banco não foi verificada nesta classificação. |
| `visit_execution_events` | Entidade/tabela conceitual proposta anteriormente | **OBSOLETO_SUBSTITUÍDO** | Não criar para o MVP. Usar `visit_events`. | O modelo lógico declara que essa entidade foi substituída por `visit_events`. A existência física de tabela homônima no banco não foi verificada nesta classificação. |
| Relação N:M `quotation_tasks` entre orçamento e tarefas | Relação conceitual | **FORA_MVP** | Não incluir no desenho inicial. Reavaliar somente se surgir requisito de um orçamento abranger várias tarefas. | O modelo lógico atual declara expressamente que essa relação não fará parte do desenho inicial. |
| Chat omnichannel próprio | Módulo/capacidade | **FUTURO** | Não incluir como entrega do MVP operacional. Chatwoot permanece solução transitória conforme o mapa de módulos; o Chat próprio será avaliado futuramente. | Mapa de módulos e ADR-0007. Não confundir com a máquina de estados operacional de visitas. |
| SaaS / administração da plataforma (onboarding, planos, limites, faturamento SaaS e suporte multiempresa) | Módulo/capacidade | **FUTURO** | Fora da entrega operacional inicial; planejar em etapa futura. | O mapa de módulos classifica explicitamente esse domínio como futuro. |

| Integrações externas e conectores (incluindo Auvo), webhooks, sincronizações, filas e infraestrutura dedicada | Capacidade transversal | **FORA_MVP** | Não implementar nem tornar dependência dos fluxos operacionais do MVP. Tratar autenticação, autorização, isolamento e demais problemas específicos quando a frente de integrações for desenvolvida. A API interna necessária às aplicações/módulos não é automaticamente excluída e será definida separadamente. | Deliberação explícita do responsável em 2026-11-08. |

## 4. Regras de uso do inventário

1. Antes de especificar uma tabela, funcionalidade ou processo, verificar se já consta neste inventário.
2. Itens `FORA_MVP` não devem entrar no backlog, modelo lógico, critérios de aceite ou implementação do MVP, salvo nova deliberação explícita.
3. Itens `FUTURO` podem ser registrados em roadmap ou brainstorm, mas não devem ser tratados como compromisso aprovado.
4. Itens `OBSOLETO_SUBSTITUÍDO` não devem ser reintroduzidos sem revisão explícita da decisão que os substituiu.
5. Itens `PENDENTE_CLASSIFICAÇÃO` devem permanecer fora de decisões implícitas até serem avaliados.
6. Uma classificação pode ser alterada quando surgirem novos requisitos ou deliberações. Registrar data, motivo e fonte da alteração no histórico.
7. Não excluir estruturas existentes do banco apenas porque foram classificadas como fora do MVP ou obsoletas.

## 5. Limites e pendências do levantamento

- O inventário é incremental, não uma declaração de que todos os elementos do projeto já foram examinados.
- O schema Supabase foi inspecionado em 2026-11-08 para identificar tabelas e policies RLS. Os resultados e limitações estão registrados em `documentacao/auditorias/AUDITORIA_DADOS_AUTORIZACAO_2026-11-08.md`; consultar esse registro antes de repetir a inspeção.
- A presença física de `visit_execution_sessions` e `visit_execution_events` não foi verificada.
- O inventário deverá ser enriquecido conforme avançarmos na análise dos módulos, tabelas, funções, workflows e código das aplicações.
- A classificação de um módulo como futuro não permite inferir automaticamente que toda tabela existente com nome relacionado a esse domínio deva ser removida.

## 6. Referências

- `documentacao/04_MODULOS_ESCOPO.md` — mapa macro de módulos.
- `documentacao/MODELO_LOGICO_DADOS_OPERACAO.md` — modelo lógico do MVP operacional.
- `documentacao/auditorias/AUDITORIA_DADOS_AUTORIZACAO_2026-11-08.md` — auditoria estrutural e funcional, incluindo a função legada de despesas.
- ADR-0007 — Chatwoot transitório e Chat próprio futuro.
- ADR-0009 — prioridade da V1: Operação primeiro.
- ADR-0015 e ADR-0016 — decisões aplicáveis ao fluxo operacional e histórico append-only.

## Histórico

- 2026-11-08 — v0.3.0: confirma integrações externas fora do MVP e distingue a API interna, ainda pendente de definição.
- 2026-11-08 — v0.2.0: incorpora esclarecimentos do responsável sobre documentos, canais de contato, relação equipamento–OS substituída por equipamento–visita, catálogos genéricos, template técnico e integrações; registra classificações e tratamentos sem autorizar exclusões físicas.
- 2026-11-08 — v0.1.0: criação do inventário incremental; inclusão da tabela `public.expenses`, função associada, domínio Financeiro, check-in inicial automático, estruturas conceituais substituídas, relação N:M não incluída no desenho inicial, Chat próprio futuro e administração SaaS futura.
