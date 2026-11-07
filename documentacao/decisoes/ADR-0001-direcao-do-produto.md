# ADR-0001 — Direção do produto

**Identificador:** ADR-0001  
**Estado:** APROVADA  
**Data da proposta:** Não documentado  
**Data da aprovação/rejeição:** Não documentado  
**Responsável pela decisão:** Responsável pelo projeto; identificação nominal não registrada nesta ficha  
**ADR relacionado/substituído:** Nenhum

## 1. Contexto

O ERP Winservice está sendo concebido como um produto próprio, modular e preparado para SaaS multiempresa. A implantação inicial será realizada na Winservice, sem restringir a arquitetura à utilização por uma única empresa.

O Project Charter registra como visão construir um ERP que permita evolução gradual, integração com serviços externos e futura comercialização para outras empresas.

**Limite da evidência histórica:** os documentos consultados registram a direção aprovada, mas não preservam o contexto detalhado da deliberação original, sua data ou os participantes.

## 2. Problema ou necessidade

Definir a direção de produto que orientará a concepção do ERP: se a solução será estruturada exclusivamente para atender à operação da Winservice ou se deverá nascer preparada para atender múltiplas empresas como produto SaaS.

## 3. Alternativas consideradas

As alternativas efetivamente avaliadas na deliberação original não estão registradas na documentação disponível.

| Alternativa | Benefícios | Custos, riscos e limitações |
|---|---|---|
| Não documentado | Não documentado | Não documentado |

Não há evidência documental suficiente para reconstruir alternativas, critérios comparativos ou a opção de não alterar o estado existente.

## 4. Decisão

O ERP Winservice será desenvolvido como um produto modular preparado para SaaS multiempresa, tendo a Winservice como primeira empresa usuária.

O escopo desta decisão estabelece a direção do produto. Ela não define, por si só, a estratégia técnica exata de tenancy, o modelo comercial, os planos SaaS ou a topologia de infraestrutura.

**Estado de aprovação:** esta ficha individualiza uma decisão registrada como aprovada no baseline do índice de decisões v0.2.0. A data específica e o registro primário da aprovação não estão documentados.

## 5. Justificativa

A justificativa histórica detalhada não está documentada.

O Project Charter registra como objetivos associados a essa direção a construção de uma arquitetura multiempresa, a substituição gradual das ferramentas atuais, a portabilidade dos dados e componentes e a possibilidade de futura comercialização para outras empresas. Esses objetivos contextualizam a decisão, mas não substituem o registro da justificativa original.

## 6. Consequências e impactos

### Benefícios esperados

- A arquitetura do produto deverá considerar o atendimento a mais de uma empresa.
- A Winservice será a primeira empresa usuária, permitindo implantação e evolução incremental.
- A direção de produto não ficará limitada, por definição, a uma instalação exclusiva para a Winservice.

### Custos, riscos e limitações

- Os custos e riscos específicos da decisão não foram quantificados na documentação disponível.
- O isolamento entre empresas deverá ser tratado como requisito de arquitetura e validado tecnicamente; esta decisão, isoladamente, não comprova que esse isolamento já esteja implementado.
- A estratégia exata de tenancy permanece em aberto no índice de decisões.

### Impactos operacionais e de manutenção

A implantação inicial deverá atender à operação da Winservice, mantendo a direção de evolução para um produto multiempresa. O plano detalhado de operação, suporte e manutenção para múltiplos clientes não está documentado nesta decisão.

### Dependências e integrações

Não foram registradas dependências técnicas específicas decorrentes desta decisão. A seleção de tecnologias e os limites entre módulos são tratados em decisões e documentos próprios.

## 7. Segurança, privacidade e isolamento multiempresa

A direção multiempresa exige que o isolamento de dados e a autorização sejam considerados desde a concepção.

O Project Charter estabelece como princípios que:
- a arquitetura deve contemplar isolamento por empresa;
- a segurança deve ser aplicada no backend e no banco, e não apenas na interface;
- as operações relevantes devem manter trilha de auditoria.

Esta ficha não especifica nem atesta a implementação de mecanismos concretos de segurança.

Permanecem sem definição documental nesta decisão:
- estratégia exata de tenancy;
- desenho completo de autenticação e autorização;
- matriz definitiva de papéis e permissões;
- configuração e cobertura de RLS por tabela;
- evidências de testes de isolamento entre empresas.

## 8. Implementação e validação

### Componentes afetados

A decisão orienta a arquitetura e a evolução dos módulos do ERP. Não há, nesta ficha, inventário completo dos componentes já implementados em função dela.

### Etapas de implementação

O plano detalhado de implementação desta decisão não está documentado.

### Testes e critérios de aceitação

O Project Charter estabelece como critério geral de sucesso que o isolamento multiempresa seja validado por testes de segurança. Os casos de teste, critérios técnicos detalhados e resultados executados não estão registrados nesta ficha.

### Evidências e estado da implementação

A aprovação da direção de produto não significa que a arquitetura SaaS multiempresa esteja integralmente implementada ou validada. O estado real deve ser verificado no código, nas migrações, no banco e nos ambientes.

### Plano de reversão

Não documentado.

## 9. Referências e evidências

- documentacao/01_PROJECT_CHARTER.md — visão, objetivos, público, implantação, princípios e critérios gerais de sucesso.
- documentacao/02_DECISOES_ARQUITETURA.md — registro da decisão ADR-0001 como aprovada no baseline legado v0.2.0.
- documentacao/07_GOVERNANCA_DOCUMENTAL.md — regras de individualização e tratamento de decisões legadas.
- Registro primário da deliberação e data específica de aprovação: não documentados.

## 10. Revisão e substituição

- **Substitui:** Nenhum.
- **Substituído por:** Nenhum.
- **Motivo de revisão:** Não aplicável.

Uma alteração substantiva da direção de produto deverá ser registrada em novo ADR, conforme a governança documental.

## Histórico desta ficha

- 2026-11-07 — criação da ficha individual, preservando o estado **APROVADA** registrado no baseline legado do índice v0.2.0. A data e o registro primário da aprovação original permanecem não documentados.
