# ADR-0004 — FlutterFlow para aplicativo técnico offline-first

**Identificador:** ADR-0004  
**Estado:** APROVADA  
**Data da proposta:** Não documentado  
**Data da aprovação/rejeição:** Não documentado  
**Responsável pela decisão:** Responsável pelo projeto; identificação nominal não registrada nesta ficha  
**ADR relacionado/substituído:** Nenhum

## 1. Contexto

O ERP prevê aplicativo móvel destinado à operação técnica em campo. O Charter e a stack registram FlutterFlow como direção para esse aplicativo, com operação offline-first. O índice também registra GPS persistente em background como parte da decisão legada.

O registro primário da deliberação original, sua data e os participantes não estão documentados. Esta ficha individualiza uma decisão que consta como aprovada no baseline do índice de decisões.

## 2. Problema ou necessidade

Definir a tecnologia de referência para o aplicativo técnico móvel e sua capacidade de operação em cenários sem conectividade.

## 3. Alternativas consideradas

As alternativas efetivamente avaliadas na deliberação original não estão documentadas.

| Alternativa | Benefícios | Custos, riscos e limitações |
|---|---|---|
| Não documentado | Não documentado | Não documentado |

Não há evidência suficiente para reconstruir retrospectivamente alternativas, critérios comparativos ou a opção de não alterar o estado existente.

## 4. Decisão

FlutterFlow será utilizado para o aplicativo móvel técnico, com abordagem offline-first e suporte a GPS persistente em background.

A decisão define a plataforma e os requisitos direcionais citados. Não especifica estratégia completa de sincronização, resolução de conflitos, armazenamento local, permissões de localização, consumo de bateria ou compatibilidade por dispositivo.

**Estado de aprovação:** preservado como APROVADA, conforme o baseline legado registrado em documentacao/02_DECISOES_ARQUITETURA.md. A data e o registro primário da aprovação original permanecem não documentados.

## 5. Justificativa

A justificativa histórica detalhada não está documentada. A decisão está registrada como aprovada no baseline e é contextualizada pelos objetivos e princípios do Project Charter e pelos documentos de stack, módulos e roadmap, conforme aplicável. Esses documentos não comprovam quais critérios específicos foram usados na deliberação original.

## 6. Consequências e impactos

### Benefícios esperados

O aplicativo deverá permitir continuidade operacional offline e sincronização posterior com a fonte central de verdade no Supabase. O armazenamento local é mecanismo operacional, não fonte alternativa definitiva. O comportamento de GPS em background deverá ser validado nos sistemas operacionais suportados.

### Custos, riscos e limitações

- Custos e riscos específicos da deliberação original: não documentados.
- Detalhes técnicos não definidos nesta decisão deverão ser especificados antes da implementação correspondente.
- Aprovação arquitetural não comprova implementação ou validação.

### Impactos operacionais e de manutenção

O aplicativo deverá permitir continuidade operacional offline e sincronização posterior com a fonte central de verdade no Supabase. O armazenamento local é mecanismo operacional, não fonte alternativa definitiva. O comportamento de GPS em background deverá ser validado nos sistemas operacionais suportados.

### Dependências e integrações

As dependências específicas deverão ser verificadas nos documentos de stack, módulos e roadmap e nos artefatos de implementação. Não documentado além do escopo descrito nesta ficha.

## 7. Segurança, privacidade e isolamento multiempresa

Dados locais, autenticação, autorização e sincronização deverão respeitar isolamento por empresa e controles do backend. Criptografia local, proteção de sessão, permissões do dispositivo e cenários de perda/roubo não estão detalhados nesta decisão.

A existência de diretrizes nesta ficha não comprova que os controles estejam implementados ou validados. Permanecem aplicáveis os princípios gerais de segurança no backend e no banco, isolamento por empresa, RLS quando aplicável e trilha de auditoria.

## 8. Implementação e validação

### Componentes afetados

Conforme o domínio e os limites descritos nesta ficha. O inventário exato de componentes implementados deve ser verificado no código, banco, workflows e ambientes.

### Etapas de implementação

A direção tecnológica está aprovada. Aplicativo, sincronização e GPS devem ser validados por testes reais em dispositivos; não há evidência nesta ficha de conclusão integral.

### Testes e critérios de aceitação

Os critérios detalhados e resultados de testes não estão integralmente documentados nesta ficha. Devem ser definidos e evidenciados antes de declarar a implementação concluída.

### Estado da implementação

A direção tecnológica está aprovada. Aplicativo, sincronização e GPS devem ser validados por testes reais em dispositivos; não há evidência nesta ficha de conclusão integral.

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
