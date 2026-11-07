# ADR-0011 — Catálogo global de permissões versionado e customizável por empresa

**Identificador:** ADR-0011  
**Estado:** APROVADA  
**Data da proposta:** Não documentado  
**Data da aprovação/rejeição:** Não documentado  
**Responsável pela decisão:** Responsável pelo projeto; identificação nominal não registrada nesta ficha  
**ADR relacionado/substituído:** Nenhum

## 1. Contexto

O ERP é multiempresa e necessita de permissões consistentes, permitindo padrões mantidos pela plataforma e adaptações por empresa. O índice legado registra catálogo padrão global versionado, catálogos próprios, adesão voluntária às versões e preservação/restauração de customizações.

O registro primário da deliberação original, sua data e os participantes não estão documentados. Esta ficha individualiza uma decisão que consta como aprovada no baseline do índice de decisões.

## 2. Problema ou necessidade

Definir como evoluir o catálogo de permissões da plataforma sem sobrescrever personalizações feitas pelas empresas.

## 3. Alternativas consideradas

As alternativas efetivamente avaliadas na deliberação original não estão documentadas.

| Alternativa | Benefícios | Custos, riscos e limitações |
|---|---|---|
| Não documentado | Não documentado | Não documentado |

Não há evidência suficiente para reconstruir retrospectivamente alternativas, critérios comparativos ou a opção de não alterar o estado existente.

## 4. Decisão

A plataforma manterá um catálogo padrão global versionado. Cada empresa poderá manter seu próprio catálogo e escolher voluntariamente quando aderir a novas versões do padrão. Personalizações deverão ser preservadas, com histórico e possibilidade de restauração dos padrões.

A decisão não define o esquema de tabelas, algoritmo de merge, semântica de herança, granularidade de permissões, papéis padrão, política de conflitos ou UX de atualização.

**Estado de aprovação:** preservado como APROVADA, conforme o baseline legado registrado em documentacao/02_DECISOES_ARQUITETURA.md. A data e o registro primário da aprovação original permanecem não documentados.

## 5. Justificativa

A justificativa histórica detalhada não está documentada. A decisão está registrada como aprovada no baseline e é contextualizada pelos objetivos e princípios do Project Charter e pelos documentos de stack, módulos e roadmap, conforme aplicável. Esses documentos não comprovam quais critérios específicos foram usados na deliberação original.

## 6. Consequências e impactos

### Benefícios esperados

Atualizações do catálogo global não deverão sobrescrever silenciosamente configurações locais. O sistema deverá distinguir versão padrão publicada, versão adotada pela empresa e customizações locais. Alterações precisam ser rastreáveis e reversíveis nos limites definidos pela implementação.

### Custos, riscos e limitações

- Custos e riscos específicos da deliberação original: não documentados.
- Detalhes técnicos não definidos nesta decisão deverão ser especificados antes da implementação correspondente.
- Aprovação arquitetural não comprova implementação ou validação.

### Impactos operacionais e de manutenção

Atualizações do catálogo global não deverão sobrescrever silenciosamente configurações locais. O sistema deverá distinguir versão padrão publicada, versão adotada pela empresa e customizações locais. Alterações precisam ser rastreáveis e reversíveis nos limites definidos pela implementação.

### Dependências e integrações

As dependências específicas deverão ser verificadas nos documentos de stack, módulos e roadmap e nos artefatos de implementação. Não documentado além do escopo descrito nesta ficha.

## 7. Segurança, privacidade e isolamento multiempresa

Permissões efetivas devem ser aplicadas no backend/API e banco, não apenas na interface. A resolução do catálogo deve respeitar o isolamento por empresa e evitar elevação de privilégio por conflito ou atualização. Matriz definitiva e testes de autorização permanecem pendentes.

A existência de diretrizes nesta ficha não comprova que os controles estejam implementados ou validados. Permanecem aplicáveis os princípios gerais de segurança no backend e no banco, isolamento por empresa, RLS quando aplicável e trilha de auditoria.

## 8. Implementação e validação

### Componentes afetados

Conforme o domínio e os limites descritos nesta ficha. O inventário exato de componentes implementados deve ser verificado no código, banco, workflows e ambientes.

### Etapas de implementação

A decisão está registrada no baseline e no status. A implementação do catálogo e sua aderência devem ser auditadas no banco, funções, RLS e aplicação; não há nesta ficha evidência de validação completa.

### Testes e critérios de aceitação

Os critérios detalhados e resultados de testes não estão integralmente documentados nesta ficha. Devem ser definidos e evidenciados antes de declarar a implementação concluída.

### Estado da implementação

A decisão está registrada no baseline e no status. A implementação do catálogo e sua aderência devem ser auditadas no banco, funções, RLS e aplicação; não há nesta ficha evidência de validação completa.

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
