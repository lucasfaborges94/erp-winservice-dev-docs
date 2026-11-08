# ADR-0015 — Drenagem e tratamento de falhas de execuções

**Identificador:** ADR-0015  
**Estado:** APROVADA  
**Data da proposta:** 2026-11-07  
**Data da aprovação/rejeição:** 2026-11-07  
**Responsável pela decisão:** Responsável pelo projeto; identificação nominal não registrada nesta ficha  
**ADR relacionado/substituído:** Nenhum

## 1. Contexto

O ERP adota ativação de módulos em dois níveis: disponibilidade global pela plataforma e ativação individual por empresa. Quando qualquer nível é desativado, novas execuções deixam de ser admitidas, enquanto as execuções previamente aceitas devem poder terminar.

Era necessário definir o tratamento de falhas durante a drenagem para evitar a interrupção indevida de trabalho já aceito e a permanência indefinida de execuções que não conseguem terminar. Esta decisão foi aprovada explicitamente pelo responsável em 2026-11-07.

## 2. Problema ou necessidade

Estabelecer comportamento uniforme para retries, falhas permanentes e timeouts durante a drenagem, aplicável a todos os módulos, sem permitir novas admissões enquanto o módulo estiver inativo.

## 3. Alternativas consideradas

As alternativas efetivamente avaliadas além da política aprovada não foram registradas.

| Alternativa | Benefícios | Custos, riscos e limitações |
|---|---|---|
| Retry limitado para falhas temporárias; falhas permanentes e timeouts encerram em estado terminal | Recupera falhas transitórias sem bloquear indefinidamente a drenagem | Exige classificação, limites, backoff, timeout e persistência de estado |
| Não documentado | Não documentado | Não documentado |

## 4. Decisão

A política aplica-se a todos os módulos do ERP quando houver desativação em qualquer nível e drenagem de execuções previamente aceitas:

1. **Falha temporária:** permitir novas tentativas da mesma execução, respeitando limite de tentativas e política de backoff.
2. **Falha permanente:** encerrar a execução com estado terminal `FAILED`, registrando o motivo.
3. **Timeout:** encerrar a execução com estado terminal `TIMEOUT`, registrando o ocorrido.
4. **Retry da execução aceita:** preservar identidade e contexto da execução original. A tentativa de recuperação não constitui nova execução nem nova admissão independente.
5. **Conclusão da drenagem:** considerar encerrada, para fins de drenagem, cada execução que alcance estado terminal, incluindo `COMPLETED`, `FAILED` ou `TIMEOUT`.
6. **Limite obrigatório:** não permitir retries ilimitados que impeçam a conclusão da drenagem.

Os valores de limite de tentativas, intervalos/backoff e timeout, assim como os critérios determinísticos para classificar falhas temporárias e permanentes, deverão ser definidos na especificação técnica antes da implementação.

Esta ADR complementa a regra de desativação com drenagem. Não autoriza novas admissões enquanto o módulo estiver inativo, não determina cancelamento das execuções aceitas e não define implementação específica de fila ou orquestrador.

**Estado de aprovação:** APROVADA explicitamente pelo responsável pelo projeto em 2026-11-07.

## 5. Justificativa

Retries limitados permitem recuperar falhas transitórias mantendo a identidade do trabalho já aceito. Estados terminais para falhas permanentes e timeouts permitem concluir contabilmente a drenagem, registrar o resultado e evitar bloqueio indefinido. Preservar a identidade impede que uma recuperação seja usada para contornar o bloqueio de novas admissões.

## 6. Consequências e impactos

### Benefícios esperados
- Comportamento consistente entre módulos.
- Recuperação controlada de falhas transitórias.
- Encerramento rastreável de execuções que não podem ser concluídas com sucesso.
- Drenagem finita, sem retries ilimitados.

### Custos, riscos e limitações
- Classificação incorreta pode encerrar prematuramente uma execução ou provocar retries desnecessários.
- Limites e backoff inadequados podem aumentar latência, carga ou custo.
- Um timeout não prova que um efeito externo não ocorreu; operações com efeitos externos poderão exigir idempotência e reconciliação.
- Esta decisão não define valores numéricos nem tratamento detalhado de cada classe de erro.

### Impactos operacionais e de manutenção
Os mecanismos de execução deverão persistir identidade, estado, contagem de tentativas, motivo de falha e timestamps necessários para auditoria e recuperação. A drenagem deverá distinguir execuções aceitas antes do fechamento de novas admissões.

### Dependências e integrações
Aplica-se ao backend/orquestrador, filas e workflows que executem operações dos módulos. Os detalhes dependem da arquitetura de execução adotada, ainda não definida nesta ADR.

## 7. Segurança, privacidade e isolamento multiempresa

O retry deverá preservar contexto de empresa, identidade do ator, autorização original e escopo da execução. A desativação não poderá ser contornada por chamadas internas, retries ou workflows técnicos. A condição de admissão deverá ser aplicada no backend/orquestrador.

Logs e dados de falha deverão evitar exposição de segredos ou dados pessoais desnecessários. Esta ADR não comprova que os controles estejam implementados.

## 8. Implementação e validação

### Componentes afetados
Política transversal de módulos, backend/orquestrador, mecanismos de fila, workflows e trilhas de auditoria.

### Etapas de implementação
1. Definir estados e transições de execução.
2. Definir critérios de classificação de falhas.
3. Definir limites de tentativas, backoff e timeouts.
4. Persistir identidade e contexto da execução entre retries.
5. Implementar coordenação atômica entre fechamento da admissão e enfileiramento.
6. Implementar drenagem e reconciliação de execuções aceitas.
7. Validar por testes automatizados e concorrentes.

### Testes e critérios de aceitação
- Falha temporária gera retry limitado da mesma execução, sem novo identificador.
- Falha permanente termina em `FAILED`, com motivo registrado.
- Timeout termina em `TIMEOUT`, com ocorrência registrada.
- Retry preserva empresa, ator, contexto e identidade originais.
- Nenhum retry ilimitado impede a conclusão da drenagem.
- Nenhuma nova execução é admitida durante a desativação.
- A drenagem termina quando todas as execuções aceitas alcançam estado terminal.
- Reativação só reabre a admissão quando os dois níveis estiverem ativos e as demais autorizações forem satisfeitas.

### Estado da implementação
Não implementada nem validada. Esta ADR registra a decisão arquitetural, não evidência de código, banco, fila ou workflow.

## 9. Referências e evidências
- `documentacao/MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md`
- `documentacao/06_STATUS_E_RELATORIOS.md`
- `documentacao/08_CONTINUIDADE_E_TRANSFERENCIA.md`
- Aprovação explícita do responsável pelo projeto nesta sessão, em 2026-11-07.

## 10. Revisão e substituição
- **Substitui:** Nenhum.
- **Substituído por:** Nenhum.
- **Motivo de revisão:** Não aplicável.

Uma mudança substantiva nesta política deverá ser registrada em nova ADR, preservando o histórico desta decisão.

## Histórico desta ficha
- 2026-11-07 — criação da ficha com estado APROVADA, após aprovação explícita do responsável pelo projeto.
