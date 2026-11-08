# Índice de decisões arquiteturais — ERP Winservice

**Versão:** 0.6.0
**Data de atualização:** 2026-11-08

Este documento é o índice consolidado das decisões arquiteturais e de produto aprovadas. Cada decisão deverá possuir uma ficha individual em `documentacao/decisoes/`, identificada por um código sequencial `ADR-NNNN`.

A existência de uma decisão neste índice registra seu estado consolidado no baseline. As decisões legadas abaixo ainda precisam ser convertidas em fichas individuais completas; não se deve presumir contexto, alternativas ou justificativas que não estejam documentados.

## 1. Como consultar as decisões

- `documentacao/decisoes/ADR-TEMPLATE.md` — modelo oficial para novas fichas.
- `documentacao/decisoes/ADR-NNNN-*.md` — fichas individuais de decisões.
- Esta página — índice, estado atual e visão consolidada.
- O histórico Git registra as alterações dos arquivos; não substitui o conteúdo das fichas.

### Estados possíveis

| Estado | Significado |
|---|---|
| `PROPOSTA` | Em avaliação; ainda não aprovada. |
| `APROVADA` | Decisão explicitamente aprovada e vigente. |
| `REJEITADA` | Proposta avaliada e rejeitada. |
| `SUPERADA` | Decisão anteriormente aprovada, substituída por outra. |

Uma decisão só pode ser marcada como `APROVADA` após aprovação explícita do responsável pelo projeto. A criação de uma ficha, por si só, não equivale a aprovação.

## 2. Decisões aprovadas — baseline legado

As decisões abaixo constavam como aprovadas na versão anterior deste documento. Os identificadores foram normalizados para quatro dígitos, preservando a ordem e a correspondência temática. As fichas individuais ainda não foram redigidas.

| ID | Tema | Síntese da decisão | Estado documental |
|---|---|---|---|
| ADR-0001 | Direção do produto | ERP preparado para SaaS multiempresa; implantação inicial na Winservice. | Aprovada — [ficha individual](decisoes/ADR-0001-direcao-do-produto.md) |
| ADR-0002 | Fonte de verdade | Supabase como fonte central de verdade dos dados. | Aprovada — [ficha individual](decisoes/ADR-0002-fonte-de-verdade.md) |
| ADR-0003 | Web administrativo | WeWeb para aplicação administrativa web, hospedada na VPS. | Aprovada — [ficha individual](decisoes/ADR-0003-weweb-aplicacao-administrativa-web.md) |
| ADR-0004 | Aplicativo técnico | FlutterFlow para aplicativo móvel técnico offline-first, com GPS persistente em background. | Aprovada — [ficha individual](decisoes/ADR-0004-flutterflow-aplicativo-tecnico-offline-first.md) |
| ADR-0005 | Automação | n8n como plataforma de automação de workflows. | Aprovada — [ficha individual](decisoes/ADR-0005-n8n-automacao-workflows.md) |
| ADR-0006 | Agente IA | Isa como agente de IA integrado aos processos, com autonomia limitada. | Aprovada — [ficha individual](decisoes/ADR-0006-isa-agente-ia-autonomia-limitada.md) |
| ADR-0007 | Atendimento | Chatwoot transitório; direção futura inclui módulo Chat omnichannel próprio. | Aprovada — [ficha individual](decisoes/ADR-0007-chatwoot-transitorio-chat-proprio.md) |
| ADR-0008 | Auvo | Auvo não é dependência operacional; poderá existir integração opcional. | Aprovada — [ficha individual](decisoes/ADR-0008-auvo-integracao-opcional.md) |
| ADR-0009 | Implantação | Prioridade da V1: Operação primeiro. | Aprovada — [ficha individual](decisoes/ADR-0009-prioridade-v1-operacao-primeiro.md) |
| ADR-0010 | Migração | Substituição gradual por módulos. | Aprovada — [ficha individual](decisoes/ADR-0010-substituicao-gradual-por-modulos.md) |
| ADR-0011 | Catálogo de permissões | Catálogo padrão global versionado, catálogos próprios por empresa, adesão voluntária às versões e preservação/restauração de customizações. | Aprovada — [ficha individual](decisoes/ADR-0011-catalogo-permissoes-global-versionado.md) |
| ADR-0012 | Autonomia da Isa | Consulta limitada ao cliente do atendimento; criação de clientes, equipamentos, OS e visitas; sem exclusão; alteração apenas de registros criados pela própria Isa; controles no backend/API e permissões. | Aprovada — [ficha individual](decisoes/ADR-0012-autonomia-isa-operacoes-permitidas.md) |
| ADR-0013 | Módulo Jurídico | Módulo Jurídico amplo; Gestão de Documentos e Instrumentos Jurídicos como funcionalidade inicial. | Aprovada — [ficha individual](decisoes/ADR-0013-modulo-juridico-amplo.md) |
| ADR-0014 | Documentos legais | Documentos e versões armazenados no banco; páginas públicas consultam a versão vigente; contratação registra a versão apresentada/aceita. | Aprovada — [ficha individual](decisoes/ADR-0014-documentos-legais-versoes-e-aceite.md) |
| ADR-0015 | Drenagem e falhas de execuções | Bloqueio de novas admissões durante desativação; retry limitado para falhas temporárias e estados terminais para falhas permanentes/timeouts. | Aprovada — [ficha individual](decisoes/ADR-0015-drenagem-e-falhas-de-execucoes.md) |
| ADR-0016 | Histórico imutável | Histórico append-only de alterações relevantes; correções geram novos eventos/revisões, sem sobrescrita ou exclusão do histórico original. | Aprovada — [ficha individual](decisoes/ADR-0016-historico-append-only.md) |
| ADR-0017 | Tenancy e autorização multiempresa | Banco e schema compartilhados; isolamento lógico por empresa; vínculo multiempresa; seleção da empresa ativa na sessão; RLS e backend como barreiras complementares. | Aprovada — [ficha individual](decisoes/ADR-0017-tenancy-compartilhada-autorizacao-multiempresa.md) |

**Nota de rastreabilidade:** os IDs antigos `ADR-001` a `ADR-014` foram renumerados neste índice para `ADR-0001` a `ADR-0014`. A numeração nova é a referência canônica daqui em diante. O histórico Git preserva a versão anterior do índice.

## 3. Convenções e preferências de modelagem

Estas convenções constavam no baseline. Quando ainda não estiverem formalizadas em ADR individual, devem ser tratadas como diretrizes registradas, sem ampliar seu alcance por inferência.

- Nomes de tabelas e campos em inglês.
- Arquitetura multiempresa com `company_identifier` nas entidades aplicáveis.
- Identificadores públicos/estáveis baseados em UUID (`identifier`) nas entidades relevantes.
- Modelar dados, relacionamentos, constraints, triggers, RLS e funções antes de construir telas.
- Supabase é a fonte de verdade; não manter cache operacional local como fonte alternativa no WeWeb.
- O aplicativo FlutterFlow deverá suportar operação offline-first e sincronização posterior.

Essas convenções devem ser verificadas contra o esquema existente antes de qualquer migração; não presumir que todas as tabelas atuais já seguem integralmente o padrão.

## 4. Política de permissões da Isa — síntese

- Identificação do cliente deve ser validada.
- Consultas devem ficar limitadas ao cliente do atendimento atual.
- Criação permitida: clientes, equipamentos, ordens de serviço e visitas.
- Exclusão: proibida.
- Atualização: somente registros criados pela própria Isa.
- A interface ou prompt do agente não substitui autorização no backend, RLS ou API.

Consultar a ficha [ADR-0012](decisoes/ADR-0012-autonomia-isa-operacoes-permitidas.md), que individualiza esta política.

## 5. Catálogo de permissões versionado — síntese

- A plataforma mantém o catálogo padrão global.
- Cada empresa pode manter catálogo próprio.
- A plataforma publica atualizações por versões.
- Cada empresa escolhe quando aderir a uma versão.
- Personalizações locais precisam ser preservadas.
- Mudanças devem ter histórico e permitir restauração dos padrões.

Consultar a ficha [ADR-0011](decisoes/ADR-0011-catalogo-permissoes-global-versionado.md), que individualiza esta política.

## 6. Questões abertas — não presumir como aprovadas

- Matriz definitiva de papéis e permissões.
- Estratégia completa de sincronização offline e resolução de conflitos.
- Topologia final de infraestrutura, alta disponibilidade e escalabilidade.
- Protocolo/API interna entre módulos.
- Estratégia de observabilidade, filas e processamento assíncrono.
- Modelo de versionamento e promoção de banco entre ambientes.
- Estratégia final de autenticação, MFA e gestão de segredos.
- Política de retenção além dos backups diários.

## 7. Registro de alteração

- **2026-11-08 — v0.6.0:** inclusão da ADR-0017, aprovada, sobre tenancy compartilhada e autorização multiempresa; definição da seleção da empresa ativa na sessão para usuários com múltiplos vínculos.
- **2026-11-08 — v0.5.0:** inclusão da ADR-0016, aprovada, sobre histórico imutável append-only.
- **2026-11-07 — v0.4.0:** inclusão da ADR-0015, aprovada, sobre drenagem de execuções e tratamento de falhas durante a drenagem.
- **2026-11-07 — v0.3.0:** ADR-0003 a ADR-0014 individualizados em fichas próprias; estados aprovados preservados do baseline legado. Lacunas históricas mantidas como não documentadas.

- **2026-11-07 — v0.2.0:** índice preparado para fichas ADR individuais; identificadores legados normalizados para quatro dígitos; template e governança documental associados. Nenhuma nova decisão de produto ou arquitetura foi criada nesta atualização.
- **2026-11-07:** ADR-0001 individualizado em `documentacao/decisoes/ADR-0001-direcao-do-produto.md`; estado aprovado preservado do baseline legado.
- **2026-11-07:** ADR-0002 individualizado em `documentacao/decisoes/ADR-0002-fonte-de-verdade.md`, após aprovação do responsável; estado aprovado preservado do baseline legado.
