# ADR-NNNN — Título objetivo da decisão

**Identificador:** ADR-NNNN  
**Estado:** PROPOSTA | APROVADA | REJEITADA | SUPERADA  
**Data da proposta:** AAAA-MM-DD  
**Data da aprovação/rejeição:** AAAA-MM-DD ou `Não aplicável`  
**Responsável pela decisão:** A confirmar  
**ADR relacionado/substituído:** Nenhum ou identificador(es)

## 1. Contexto

Descrever o contexto de negócio, técnico e operacional que originou a necessidade da decisão. Separar fatos verificados de hipóteses.

## 2. Problema ou necessidade

Definir claramente o problema, a restrição ou a oportunidade que exige uma decisão.

## 3. Alternativas consideradas

Registrar as alternativas efetivamente avaliadas, incluindo a opção de não alterar o estado atual. Não inventar alternativas retrospectivamente; quando não houver evidência, declarar `Não documentado`.

| Alternativa | Benefícios | Custos, riscos e limitações |
|---|---|---|
| Alternativa A | A preencher | A preencher |
| Alternativa B | A preencher | A preencher |

## 4. Decisão

Descrever a decisão de forma objetiva, delimitando o que está aprovado e o que permanece fora do escopo.

> Para fichas em estado `PROPOSTA`, esta seção deve ser identificada como proposta, não como decisão vigente.

## 5. Justificativa

Explicar por que a alternativa escolhida atende melhor ao contexto e aos critérios relevantes. Registrar evidências ou indicar quando a justificativa histórica não está disponível.

## 6. Consequências e impactos

### Benefícios esperados
- A preencher.

### Custos, riscos e limitações
- A preencher.

### Impactos operacionais e de manutenção
- A preencher.

### Dependências e integrações
- A preencher.

## 7. Segurança, privacidade e isolamento multiempresa

Avaliar, quando aplicável:
- autenticação e autorização;
- aplicação de controles no backend/API e banco;
- RLS e isolamento por empresa (`company_identifier`);
- exposição e tratamento de dados pessoais;
- gestão de segredos e trilhas de auditoria.

Se algum item não se aplicar, justificar. Não declarar segurança validada sem evidência técnica.

## 8. Implementação e validação

Registrar:
- componentes, tabelas, APIs, workflows ou módulos afetados;
- etapas de implementação;
- testes e critérios de aceitação;
- evidências verificáveis;
- plano de reversão, quando aplicável.

Uma decisão aprovada não significa que sua implementação já foi concluída.

## 9. Referências e evidências

- Documentos relacionados:
- Relatórios de sessão:
- Issues/PRs/commits:
- Testes ou auditorias:
- Outras evidências:

## 10. Revisão e substituição

- **Substitui:** Nenhum ou ADR(s).
- **Substituído por:** Nenhum ou ADR(s).
- **Motivo de revisão:** A preencher, se aplicável.

Decisões aprovadas não devem ser apagadas nem reescritas para alterar silenciosamente seu significado. Uma mudança substantiva deve gerar novo ADR, que referencia o anterior e o marca como `SUPERADA` quando a substituição for aprovada.

## Histórico desta ficha

- AAAA-MM-DD — criação da ficha em estado `PROPOSTA` ou registro da aprovação explicitamente confirmada.
