# ADR-0013 — Módulo Jurídico amplo

**Identificador:** ADR-0013  
**Estado:** APROVADA  
**Data da proposta:** Não documentado  
**Data da aprovação/rejeição:** Não documentado  
**Responsável pela decisão:** Responsável pelo projeto; identificação nominal não registrada nesta ficha  
**ADR relacionado/substituído:** Nenhum

## 1. Contexto

O mapa de módulos contempla um domínio Jurídico empresarial. A decisão legada estabelece que o módulo será amplo e não ficará restrito à gestão documental. A primeira funcionalidade a ser especificada será Gestão de Documentos e Instrumentos Jurídicos.

O registro primário da deliberação original, sua data e os participantes não estão documentados. Esta ficha individualiza uma decisão que consta como aprovada no baseline do índice de decisões.

## 2. Problema ou necessidade

Definir a abrangência de produto do domínio Jurídico e evitar que sua arquitetura seja limitada à primeira funcionalidade entregue.

## 3. Alternativas consideradas

As alternativas efetivamente avaliadas na deliberação original não estão documentadas.

| Alternativa | Benefícios | Custos, riscos e limitações |
|---|---|---|
| Não documentado | Não documentado | Não documentado |

Não há evidência suficiente para reconstruir retrospectivamente alternativas, critérios comparativos ou a opção de não alterar o estado existente.

## 4. Decisão

O ERP terá um módulo Jurídico amplo, abrangendo responsabilidades e funcionalidades jurídicas empresariais. Gestão de Documentos e Instrumentos Jurídicos será a primeira funcionalidade a detalhar e desenvolver.

O mapa cita como áreas candidatas contratos, documentos, ocorrências, notificações, disputas, prazos, obrigações, procurações, autorizações e conformidade. A lista não constitui, por si só, requisitos detalhados nem cronograma aprovado para cada funcionalidade.

**Estado de aprovação:** preservado como APROVADA, conforme o baseline legado registrado em documentacao/02_DECISOES_ARQUITETURA.md. A data e o registro primário da aprovação original permanecem não documentados.

## 5. Justificativa

A justificativa histórica detalhada não está documentada. A decisão está registrada como aprovada no baseline e é contextualizada pelos objetivos e princípios do Project Charter e pelos documentos de stack, módulos e roadmap, conforme aplicável. Esses documentos não comprovam quais critérios específicos foram usados na deliberação original.

## 6. Consequências e impactos

### Benefícios esperados

A modelagem deverá permitir evolução do domínio além da gestão documental inicial, mantendo fronteiras claras com cadastros, atendimento, comercial e demais módulos. A sequência e os critérios de aceite das funcionalidades futuras permanecem pendentes.

### Custos, riscos e limitações

- Custos e riscos específicos da deliberação original: não documentados.
- Detalhes técnicos não definidos nesta decisão deverão ser especificados antes da implementação correspondente.
- Aprovação arquitetural não comprova implementação ou validação.

### Impactos operacionais e de manutenção

A modelagem deverá permitir evolução do domínio além da gestão documental inicial, mantendo fronteiras claras com cadastros, atendimento, comercial e demais módulos. A sequência e os critérios de aceite das funcionalidades futuras permanecem pendentes.

### Dependências e integrações

As dependências específicas deverão ser verificadas nos documentos de stack, módulos e roadmap e nos artefatos de implementação. Não documentado além do escopo descrito nesta ficha.

## 7. Segurança, privacidade e isolamento multiempresa

O domínio poderá tratar documentos e informações jurídicas sensíveis. A arquitetura deverá prever controle de acesso, isolamento por empresa, auditoria de consulta/alteração e proteção de arquivos. Classificação, retenção, assinatura e requisitos regulatórios específicos não estão definidos nesta decisão.

A existência de diretrizes nesta ficha não comprova que os controles estejam implementados ou validados. Permanecem aplicáveis os princípios gerais de segurança no backend e no banco, isolamento por empresa, RLS quando aplicável e trilha de auditoria.

## 8. Implementação e validação

### Componentes afetados

Conforme o domínio e os limites descritos nesta ficha. O inventário exato de componentes implementados deve ser verificado no código, banco, workflows e ambientes.

### Etapas de implementação

O status indica módulo amplo aprovado e planejamento funcional iniciado. A primeira funcionalidade documental ainda precisa de especificação e evidências próprias.

### Testes e critérios de aceitação

Os critérios detalhados e resultados de testes não estão integralmente documentados nesta ficha. Devem ser definidos e evidenciados antes de declarar a implementação concluída.

### Estado da implementação

O status indica módulo amplo aprovado e planejamento funcional iniciado. A primeira funcionalidade documental ainda precisa de especificação e evidências próprias.

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
