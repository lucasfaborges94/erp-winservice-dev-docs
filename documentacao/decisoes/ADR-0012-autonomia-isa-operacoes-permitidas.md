# ADR-0012 — Limites de autonomia e permissões da Isa

**Identificador:** ADR-0012  
**Estado:** APROVADA  
**Data da proposta:** Não documentado  
**Data da aprovação/rejeição:** Não documentado  
**Responsável pela decisão:** Responsável pelo projeto; identificação nominal não registrada nesta ficha  
**ADR relacionado/substituído:** Nenhum

## 1. Contexto

A Isa atua em processos de atendimento e pode executar operações no ERP. O baseline registra limites explícitos: consultas limitadas ao cliente do atendimento; criação de clientes, equipamentos, ordens de serviço e visitas; sem exclusão; alteração apenas de registros criados pela própria Isa; controles no backend/API e permissões.

O registro primário da deliberação original, sua data e os participantes não estão documentados. Esta ficha individualiza uma decisão que consta como aprovada no baseline do índice de decisões.

## 2. Problema ou necessidade

Delimitar quais dados a Isa pode consultar e quais operações pode executar, reduzindo riscos de acesso indevido ou mutação não autorizada.

## 3. Alternativas consideradas

As alternativas efetivamente avaliadas na deliberação original não estão documentadas.

| Alternativa | Benefícios | Custos, riscos e limitações |
|---|---|---|
| Não documentado | Não documentado | Não documentado |

Não há evidência suficiente para reconstruir retrospectivamente alternativas, critérios comparativos ou a opção de não alterar o estado existente.

## 4. Decisão

A Isa poderá consultar somente dados relacionados ao cliente identificado no atendimento atual. Poderá criar clientes, equipamentos, ordens de serviço e visitas. Não poderá excluir registros. Poderá alterar somente registros que ela própria tenha criado. Essas restrições deverão ser impostas no backend/API e pelos mecanismos de autorização, não apenas por prompt ou interface.

A decisão não define todos os campos editáveis, transições permitidas, critérios de identificação do cliente, tratamento de homônimos, revisão humana, limites de volume ou política de exceções. Esses detalhes exigem especificação funcional e técnica.

**Estado de aprovação:** preservado como APROVADA, conforme o baseline legado registrado em documentacao/02_DECISOES_ARQUITETURA.md. A data e o registro primário da aprovação original permanecem não documentados.

## 5. Justificativa

A justificativa histórica detalhada não está documentada. A decisão está registrada como aprovada no baseline e é contextualizada pelos objetivos e princípios do Project Charter e pelos documentos de stack, módulos e roadmap, conforme aplicável. Esses documentos não comprovam quais critérios específicos foram usados na deliberação original.

## 6. Consequências e impactos

### Benefícios esperados

As operações da Isa deverão carregar contexto verificável de atendimento, cliente e autoria. O backend deverá distinguir registros criados pela Isa de registros criados por usuários ou integrações. Falhas de identificação devem impedir consultas ou mutações fora do contexto validado.

### Custos, riscos e limitações

- Custos e riscos específicos da deliberação original: não documentados.
- Detalhes técnicos não definidos nesta decisão deverão ser especificados antes da implementação correspondente.
- Aprovação arquitetural não comprova implementação ou validação.

### Impactos operacionais e de manutenção

As operações da Isa deverão carregar contexto verificável de atendimento, cliente e autoria. O backend deverá distinguir registros criados pela Isa de registros criados por usuários ou integrações. Falhas de identificação devem impedir consultas ou mutações fora do contexto validado.

### Dependências e integrações

As dependências específicas deverão ser verificadas nos documentos de stack, módulos e roadmap e nos artefatos de implementação. Não documentado além do escopo descrito nesta ficha.

## 7. Segurança, privacidade e isolamento multiempresa

Autorização deve ser aplicada no servidor e no banco quando aplicável. Consultas devem ser limitadas ao cliente do atendimento atual. Exclusão deve ser negada. Atualização deve verificar autoria do registro. Devem existir logs auditáveis e testes negativos para tentativas de acesso cruzado ou alteração de registros alheios.

A existência de diretrizes nesta ficha não comprova que os controles estejam implementados ou validados. Permanecem aplicáveis os princípios gerais de segurança no backend e no banco, isolamento por empresa, RLS quando aplicável e trilha de auditoria.

## 8. Implementação e validação

### Componentes afetados

Conforme o domínio e os limites descritos nesta ficha. O inventário exato de componentes implementados deve ser verificado no código, banco, workflows e ambientes.

### Etapas de implementação

O status registra limites definidos e recomenda testar autorização no backend/API. Esta ficha não atesta que todos os endpoints, funções e políticas estejam implementados ou testados.

### Testes e critérios de aceitação

Os critérios detalhados e resultados de testes não estão integralmente documentados nesta ficha. Devem ser definidos e evidenciados antes de declarar a implementação concluída.

### Estado da implementação

O status registra limites definidos e recomenda testar autorização no backend/API. Esta ficha não atesta que todos os endpoints, funções e políticas estejam implementados ou testados.

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
