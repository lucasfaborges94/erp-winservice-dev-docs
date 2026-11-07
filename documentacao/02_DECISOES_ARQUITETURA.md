# Registro de decisões — Arquitetura e produto

**Versão:** 0.1.0  
**Data de consolidação:** 2026-11-07

Este documento mantém decisões arquiteturais aprovadas separadas de propostas e questões abertas.

## 1. Decisões aprovadas

| ID | Tema | Decisão | Estado |
|---|---|---|---|
| ADR-001 | Direção do produto | ERP preparado para SaaS multiempresa; implantação inicial na Winservice | Aprovada |
| ADR-002 | Fonte de verdade | Supabase será a fonte central de verdade dos dados | Aprovada |
| ADR-003 | Web administrativo | WeWeb para aplicação administrativa web, hospedada na VPS | Aprovada |
| ADR-004 | Aplicativo técnico | FlutterFlow para aplicativo móvel técnico offline-first, com GPS persistente em background | Aprovada |
| ADR-005 | Automação | n8n como plataforma de automação de workflows | Aprovada |
| ADR-006 | Agente IA | Isa como agente de IA integrado aos processos, com autonomia limitada | Aprovada |
| ADR-007 | Atendimento | Chatwoot é transitório; direção futura inclui módulo Chat omnichannel próprio | Aprovada |
| ADR-008 | Auvo | Auvo não é dependência operacional; poderá existir integração opcional | Aprovada |
| ADR-009 | Implantação | Prioridade da V1: Operação primeiro | Aprovada |
| ADR-010 | Migração | Substituição gradual por módulos | Aprovada |
| ADR-011 | Catálogo de permissões | Catálogo padrão global mantido pela plataforma e catálogos próprios por empresa; atualização global por versões, adesão voluntária por empresa, preservando customizações, com histórico e restauração dos padrões | Aprovada |
| ADR-012 | Autonomia da Isa | Pode identificar cliente em atendimento e consultar apenas dados desse cliente; pode cadastrar clientes, equipamentos, OS e visitas; não pode excluir; só pode alterar registros que ela própria criou. Restrições devem ser aplicadas no backend/API e permissões | Aprovada |
| ADR-013 | Módulo Jurídico | Módulo Jurídico será amplo; Gestão de Documentos e Instrumentos Jurídicos será uma de suas funcionalidades iniciais | Aprovada |
| ADR-014 | Documentos legais | Documentos legais e versões serão armazenados no banco; páginas públicas consultam a versão vigente; histórico preservado; contratação deve registrar a versão apresentada/aceita | Aprovada |

## 2. Convenções e preferências de modelagem

- Nomes de tabelas e campos em inglês.
- Arquitetura multiempresa com `company_identifier` nas entidades aplicáveis.
- Identificadores públicos/estáveis baseados em UUID (`identifier`) nas entidades relevantes.
- Modelar dados, relacionamentos, constraints, triggers, RLS e funções antes de construir telas.
- Supabase é a fonte de verdade; não manter cache operacional local como fonte alternativa no WeWeb.
- O aplicativo FlutterFlow deverá suportar operação offline-first e sincronização posterior.

Essas convenções devem ser verificadas contra o esquema existente antes de qualquer migração; não presumir que todas as tabelas atuais já seguem integralmente o padrão.

## 3. Política de permissões da Isa

A política de autonomia da Isa é uma regra de segurança de backend:
- Identificação do cliente deve ser validada.
- Consultas devem ficar limitadas ao cliente do atendimento atual.
- Criação permitida: clientes, equipamentos, ordens de serviço e visitas.
- Exclusão: proibida.
- Atualização: somente registros criados pela própria Isa.
- A interface ou prompt do agente não substitui autorização no backend, RLS ou API.

## 4. Catálogo de permissões versionado

- A plataforma mantém o catálogo padrão global.
- Cada empresa pode manter catálogo próprio.
- A plataforma publica atualizações por versões.
- Cada empresa escolhe quando aderir a uma versão.
- Personalizações locais precisam ser preservadas.
- Mudanças devem ter histórico e permitir restauração dos padrões.

## 5. Questões abertas / não presumir como aprovadas

- Estratégia exata de tenancy: shared schema, schema-per-tenant ou outra.
- Matriz definitiva de papéis e permissões.
- Estratégia completa de sincronização offline e resolução de conflitos.
- Topologia final de infraestrutura, alta disponibilidade e escalabilidade.
- Protocolo/API interna entre módulos.
- Estratégia de observabilidade, filas e processamento assíncrono.
- Modelo de versionamento e promoção de banco entre ambientes.
- Estratégia final de autenticação, MFA e gestão de segredos.
- Política de retenção além dos backups diários.

## 6. Registro de alteração

Toda nova decisão deverá receber ID sequencial `ADR-NNN`, data, contexto, decisão, alternativas consideradas, consequências e estado. Decisões substituídas não devem ser apagadas; devem ser marcadas como superseded e apontar para a decisão sucessora.
