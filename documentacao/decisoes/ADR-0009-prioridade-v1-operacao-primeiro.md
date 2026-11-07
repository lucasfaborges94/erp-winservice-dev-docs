# ADR-0009 — Prioridade da primeira versão: Operação primeiro

**Identificador:** ADR-0009  
**Estado:** APROVADA  
**Data da proposta:** Não documentado  
**Data da aprovação/rejeição:** Não documentado  
**Responsável pela decisão:** Responsável pelo projeto; identificação nominal não registrada nesta ficha  
**ADR relacionado/substituído:** Nenhum

## 1. Contexto

O ERP abrange múltiplos domínios, incluindo cadastros, operação, comercial, financeiro, atendimento, automação, gestão, integrações, aplicações móveis e Jurídico. O Charter define Operação como prioridade da primeira versão.

O registro primário da deliberação original, sua data e os participantes não estão documentados. Esta ficha individualiza uma decisão que consta como aprovada no baseline do índice de decisões.

## 2. Problema ou necessidade

Estabelecer a prioridade de produto para orientar sequenciamento, alocação de esforço e definição do primeiro conjunto de entregas.

## 3. Alternativas consideradas

As alternativas efetivamente avaliadas na deliberação original não estão documentadas.

| Alternativa | Benefícios | Custos, riscos e limitações |
|---|---|---|
| Não documentado | Não documentado | Não documentado |

Não há evidência suficiente para reconstruir retrospectivamente alternativas, critérios comparativos ou a opção de não alterar o estado existente.

## 4. Decisão

A primeira versão do ERP priorizará os processos do domínio de Operação.

A prioridade não significa que os demais módulos estejam cancelados ou que todo o domínio de Operação já tenha escopo fechado. Processos, estados, critérios de aceite e fronteiras da primeira entrega precisam ser detalhados.

**Estado de aprovação:** preservado como APROVADA, conforme o baseline legado registrado em documentacao/02_DECISOES_ARQUITETURA.md. A data e o registro primário da aprovação original permanecem não documentados.

## 5. Justificativa

A justificativa histórica detalhada não está documentada. A decisão está registrada como aprovada no baseline e é contextualizada pelos objetivos e princípios do Project Charter e pelos documentos de stack, módulos e roadmap, conforme aplicável. Esses documentos não comprovam quais critérios específicos foram usados na deliberação original.

## 6. Consequências e impactos

### Benefícios esperados

Roadmap, backlog e dependências deverão priorizar os fluxos operacionais necessários à continuidade do negócio. Cadastros e serviços de plataforma necessários à operação poderão ser dependências. Escopo comercial, financeiro e demais domínios serão sequenciados conforme planejamento próprio.

### Custos, riscos e limitações

- Custos e riscos específicos da deliberação original: não documentados.
- Detalhes técnicos não definidos nesta decisão deverão ser especificados antes da implementação correspondente.
- Aprovação arquitetural não comprova implementação ou validação.

### Impactos operacionais e de manutenção

Roadmap, backlog e dependências deverão priorizar os fluxos operacionais necessários à continuidade do negócio. Cadastros e serviços de plataforma necessários à operação poderão ser dependências. Escopo comercial, financeiro e demais domínios serão sequenciados conforme planejamento próprio.

### Dependências e integrações

As dependências específicas deverão ser verificadas nos documentos de stack, módulos e roadmap e nos artefatos de implementação. Não documentado além do escopo descrito nesta ficha.

## 7. Segurança, privacidade e isolamento multiempresa

A prioridade não reduz os requisitos gerais de autenticação, autorização, RLS, isolamento multiempresa e auditoria. Processos operacionais deverão validar permissões no backend e registrar ações relevantes.

A existência de diretrizes nesta ficha não comprova que os controles estejam implementados ou validados. Permanecem aplicáveis os princípios gerais de segurança no backend e no banco, isolamento por empresa, RLS quando aplicável e trilha de auditoria.

## 8. Implementação e validação

### Componentes afetados

Conforme o domínio e os limites descritos nesta ficha. O inventário exato de componentes implementados deve ser verificado no código, banco, workflows e ambientes.

### Etapas de implementação

O status registra Operação como prioridade V1, com processos, estados e escopo detalhado ainda pendentes. Não declarar a V1 concluída sem critérios de aceite e evidências.

### Testes e critérios de aceitação

Os critérios detalhados e resultados de testes não estão integralmente documentados nesta ficha. Devem ser definidos e evidenciados antes de declarar a implementação concluída.

### Estado da implementação

O status registra Operação como prioridade V1, com processos, estados e escopo detalhado ainda pendentes. Não declarar a V1 concluída sem critérios de aceite e evidências.

A aprovação desta decisão não significa que sua implementação esteja concluída.

### Plano de reversão

Não documentado.

## 9. Referências e evidências

- documentacao/02_DECISOES_ARQUITETURA.md — registro da decisão como aprovada no baseline legado.
- documentacao/01_PROJECT_CHARTER.md — visão, objetivos e princípios do projeto.
- documentacao/03_ROADMAP_ETAPAS.md — etapas e dependências, quando aplicável.
- documentacao/04_MODULOS_ESCOPO.md — mapa de módulos e escopo, quando aplicável.
- documentacao/05_STACK_RECURSOS.md — tecnologias e diretrizes técnicas, quando aplicável.
- documentacao/06_STATUS_E_RELATORIOS.md — estado conhecido e pendências, quando aplicável.
- Registro primário da deliberação e data específica de aprovação: não documentados.

## 10. Revisão e substituição

- **Substitui:** Nenhum.
- **Substituído por:** Nenhum.
- **Motivo de revisão:** Não aplicável.

Uma mudança substantiva deverá ser registrada em novo ADR, conforme a governança documental.

## Histórico desta ficha

- 2026-11-07 — criação da ficha individual, preservando o estado APROVADA registrado no baseline legado. A data e o registro primário da aprovação original permanecem não documentados.
