# ADR-0014 — Armazenamento e versionamento de documentos legais e registro de aceite

**Identificador:** ADR-0014  
**Estado:** APROVADA  
**Data da proposta:** Não documentado  
**Data da aprovação/rejeição:** Não documentado  
**Responsável pela decisão:** Responsável pelo projeto; identificação nominal não registrada nesta ficha  
**ADR relacionado/substituído:** Nenhum

## 1. Contexto

O domínio Jurídico inclui documentos legais apresentados em páginas públicas e utilizados em contratações. O baseline define que documentos e versões sejam armazenados no banco, páginas públicas consultem a versão vigente e a contratação registre a versão apresentada e aceita.

O registro primário da deliberação original, sua data e os participantes não estão documentados. Esta ficha individualiza uma decisão que consta como aprovada no baseline do índice de decisões.

## 2. Problema ou necessidade

Garantir que o conteúdo legal apresentado ao usuário seja identificável posteriormente e que o histórico de versões aceitas seja preservado.

## 3. Alternativas consideradas

As alternativas efetivamente avaliadas na deliberação original não estão documentadas.

| Alternativa | Benefícios | Custos, riscos e limitações |
|---|---|---|
| Não documentado | Não documentado | Não documentado |

Não há evidência suficiente para reconstruir retrospectivamente alternativas, critérios comparativos ou a opção de não alterar o estado existente.

## 4. Decisão

Documentos legais e suas versões serão armazenados no banco. Páginas públicas consultarão a versão vigente. Em cada contratação, o sistema registrará a versão específica apresentada e aceita, preservando o histórico e sem sobrescrever versões anteriormente utilizadas.

A decisão não define formato de conteúdo, modelo de publicação, workflow de aprovação, agendamento, assinatura eletrônica, evidências técnicas de aceite, carimbo temporal, política de retenção ou serviço de armazenamento de anexos.

**Estado de aprovação:** preservado como APROVADA, conforme o baseline legado registrado em documentacao/02_DECISOES_ARQUITETURA.md. A data e o registro primário da aprovação original permanecem não documentados.

## 5. Justificativa

A justificativa histórica detalhada não está documentada. A decisão está registrada como aprovada no baseline e é contextualizada pelos objetivos e princípios do Project Charter e pelos documentos de stack, módulos e roadmap, conforme aplicável. Esses documentos não comprovam quais critérios específicos foram usados na deliberação original.

## 6. Consequências e impactos

### Benefícios esperados

A publicação de nova versão não deverá alterar retroativamente a versão vinculada a uma contratação anterior. O registro deverá permitir identificar o conteúdo/versão efetivamente apresentado e o evento de aceite. Apresentação, ciência, aceite contratual e consentimento não devem ser tratados como eventos juridicamente equivalentes.

### Custos, riscos e limitações

- Custos e riscos específicos da deliberação original: não documentados.
- Detalhes técnicos não definidos nesta decisão deverão ser especificados antes da implementação correspondente.
- Aprovação arquitetural não comprova implementação ou validação.

### Impactos operacionais e de manutenção

A publicação de nova versão não deverá alterar retroativamente a versão vinculada a uma contratação anterior. O registro deverá permitir identificar o conteúdo/versão efetivamente apresentado e o evento de aceite. Apresentação, ciência, aceite contratual e consentimento não devem ser tratados como eventos juridicamente equivalentes.

### Dependências e integrações

As dependências específicas deverão ser verificadas nos documentos de stack, módulos e roadmap e nos artefatos de implementação. Não documentado além do escopo descrito nesta ficha.

## 7. Segurança, privacidade e isolamento multiempresa

Acesso administrativo a documentos e versões deve ser autorizado e auditado. Páginas públicas devem expor somente a versão destinada à publicação. Registros de contratação e aceite devem preservar integridade e vínculo à empresa/contratação. Requisitos legais específicos e mecanismos criptográficos não estão definidos nesta decisão.

A existência de diretrizes nesta ficha não comprova que os controles estejam implementados ou validados. Permanecem aplicáveis os princípios gerais de segurança no backend e no banco, isolamento por empresa, RLS quando aplicável e trilha de auditoria.

## 8. Implementação e validação

### Componentes afetados

Conforme o domínio e os limites descritos nesta ficha. O inventário exato de componentes implementados deve ser verificado no código, banco, workflows e ambientes.

### Etapas de implementação

O mapa de módulos registra as diretrizes de armazenamento, publicação e vínculo de versão. Formato de conteúdo, workflow de aprovação, publicação programada e modelo de eventos permanecem pendentes de decisão/especificação.

### Testes e critérios de aceitação

Os critérios detalhados e resultados de testes não estão integralmente documentados nesta ficha. Devem ser definidos e evidenciados antes de declarar a implementação concluída.

### Estado da implementação

O mapa de módulos registra as diretrizes de armazenamento, publicação e vínculo de versão. Formato de conteúdo, workflow de aprovação, publicação programada e modelo de eventos permanecem pendentes de decisão/especificação.

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
