# Continuidade e transferência de trabalho — ERP Winservice

**Documento:** Procedimento de continuidade e transferência  
**Versão:** 1.0.0  
**Data:** 2026-11-07  
**Estado:** procedimento estabelecido; registro inicial de referência

## 1. Objetivo

Permitir que o desenvolvimento do ERP Winservice seja retomado por outra conta do ChatGPT, outra IA, outro profissional ou outra equipe sem depender do histórico privado de conversas.

Este documento define o que deve ser registrado ao encerrar uma sessão, como outra pessoa ou agente deve retomar o trabalho, como manter o status verificável e como transferir tarefas entre ferramentas e responsáveis.

As regras gerais de autoridade e trabalho estão em [00_FONTE_DA_VERDADE.md](../00_FONTE_DA_VERDADE.md).

## 2. Regra de encerramento

Toda sessão que produza avanço relevante deve atualizar este documento ou criar um registro de sessão no histórico definido abaixo.

Não registrar como concluído algo que foi apenas discutido, proposto, gerado mas não aplicado, ou aplicado sem validação.

Se uma sessão não produzir mudança relevante, não é necessário criar um relatório artificial. Pode-se registrar apenas uma observação se ela evitar perda de contexto importante.

## 3. Modelo obrigatório de registro de sessão

Copiar e preencher este modelo para cada sessão relevante:

---

### Sessão — AAAA-MM-DD — título curto

**Responsável/agente:**  
**Ferramenta/ambiente:**  
**Branch:**  
**Commit inicial:**  
**Commit final:**  
**Etapa do roadmap:**  
**Módulo/processo:**  
**Estado:** EM_ANDAMENTO | CONCLUÍDA | BLOQUEADA

#### 1. Objetivo
Descrever o resultado pretendido.

#### 2. Contexto consultado
Listar documentos, ADRs, issues, arquivos, tabelas, ambientes e versões efetivamente consultados.

#### 3. Trabalho realizado
Descrever apenas atividades efetivamente executadas.

#### 4. Decisões
- Decisões aprovadas e seus IDs ADR:
- Propostas ainda não aprovadas:
- Decisões pendentes do responsável:

#### 5. Artefatos alterados
| Arquivo/objeto | Tipo de alteração | Referência (commit, PR, migration etc.) |
|---|---|---|
| | | |

#### 6. Verificações e testes
| Verificação/teste | Ambiente/versão | Resultado | Evidência |
|---|---|---|---|
| | | | |

Não preencher com testes planejados como se tivessem sido executados.

#### 7. Estado ao encerrar
- O que está concluído:
- O que está parcialmente concluído:
- O que não foi iniciado:
- O que não foi verificado:

#### 8. Pendências e bloqueios
| Item | Impacto/prioridade | Dependência ou responsável | Próxima ação |
|---|---|---|---|
| | | | |

#### 9. Riscos e observações
Registrar riscos técnicos, operacionais, de segurança, dados ou escopo.

#### 10. Próxima atividade recomendada
Descrever uma tarefa concreta, com objetivo, escopo e critério de conclusão.

#### 11. Instruções para quem assumir
Informar arquivos a abrir, comandos seguros, ambiente necessário, cuidados e o que não deve ser repetido.

---

## 4. Procedimento de retomada

Quem assumir o trabalho deve:

1. Ler [00_FONTE_DA_VERDADE.md](../00_FONTE_DA_VERDADE.md).
2. Ler [00_LEIA-ME.md](00_LEIA-ME.md) e [07_GOVERNANCA_DOCUMENTAL.md](07_GOVERNANCA_DOCUMENTAL.md).
3. Consultar este documento e identificar a sessão mais recente.
4. Verificar o estado atual do repositório e a branch, sem presumir que o registro é mais recente que o código.
5. Consultar o commit citado e comparar com o HEAD atual.
6. Ler os documentos e ADRs relacionados à próxima atividade.
7. Verificar o estado real de banco, código e ambiente quando aplicável.
8. Confirmar pendências, bloqueios e critérios de aceite.
9. Informar ao responsável o entendimento do estado atual e o plano de retomada, destacando divergências encontradas.
10. Só então prosseguir com alterações.

Se não houver registro de sessão recente, reconstruir o estado a partir do último relatório, commits, issues, arquivos e evidências disponíveis. Marcar inferências como não verificadas.

## 5. Critérios de qualidade de um registro

Um registro é adequado quando uma pessoa que não participou da sessão consegue responder:
- Qual era o objetivo?
- O que foi realmente feito?
- Onde estão as alterações?
- Quais decisões foram aprovadas?
- O que foi testado e qual foi o resultado?
- O que falta?
- Qual é a próxima ação concreta?
- Quais riscos ou cuidados devem ser observados?

Evitar expressões vagas como “continuar desenvolvimento”, “ajustar banco” ou “finalizar módulo” sem apontar escopo, objeto e critério de aceite.

## 6. Organização do histórico

O registro mais recente deve ficar no início da seção Histórico de sessões, abaixo deste procedimento. Sessões antigas não devem ser apagadas; podem ser resumidas ou movidas para arquivos separados quando o documento crescer.

Para projetos ou módulos com atividade intensa, criar arquivos de sessão separados em uma pasta própria, mantendo neste documento o índice e o estado atual. O padrão sugerido é:

documentacao/sessoes/AAAA-MM-DD-titulo-curto.md

Não criar essa pasta ou arquivos sem conteúdo real a registrar.

## 7. Estado inicial de continuidade — 2026-11-07

Este é um ponto de partida documental, não uma afirmação de que todas as etapas anteriores do ERP estejam concluídas.

### Contexto confirmado no repositório

- Repositório de documentação: lucasfaborges94/erp-winservice-dev-docs.
- Branch de referência: main.
- O repositório contém Project Charter, roadmap, mapa de módulos, stack, status, governança e inventários técnicos.
- O índice de decisões registra 14 decisões legadas como aprovadas, identificadas como ADR-0001 a ADR-0014.
- As fichas individuais ADR-0001, ADR-0002, ADR-0003 e ADR-0008 foram verificadas nesta revisão; conferir a presença e integridade de todas as 14 fichas antes de qualquer operação em lote.
- O inventário e a auditoria estrutural do Supabase têm data-base de 2026-11-06; não presumir que representam o estado atual.
- A prioridade de produto registrada é Operação primeiro, com substituição gradual por módulos.
- O desenvolvimento vigente da Máquina de Estados V1 não deve considerar Auvo como sistema, integração, fonte de dados ou dependência enquanto essa exclusão estiver vigente. Consultar a decisão e o contexto mais recente antes de alterar esse limite.

### Trabalho desta iniciativa de continuidade

- Estabelecer um protocolo de fonte da verdade independente de fornecedor de IA e de conta.
- Estabelecer um procedimento de encerramento, retomada e transferência de trabalho.
- Integrar esses procedimentos ao mapa e à governança documental.

### Pendências para a próxima revisão

1. Conferir os links e a presença de todos os 14 ADRs individuais.
2. Revisar a consistência entre índice de ADRs, fichas individuais, Project Charter e governança.
3. Atualizar o status do projeto com o commit efetivo desta iniciativa.
4. Registrar a próxima atividade técnica do ERP com base no estado real do código e do banco, não apenas em conversas.
5. Quando houver desenvolvimento de código em repositório próprio, definir como commits, migrations, testes e releases serão referenciados a partir desta documentação.

### Próxima atividade recomendada

Fazer uma auditoria de consistência documental do repositório: validar links relativos, presença dos arquivos referenciados, estados dos ADRs e coerência entre Charter, decisões, roadmap, status e governança. Registrar achados sem corrigir decisões por inferência.

## 8. Histórico de sessões

### 2026-11-07 — Protocolo de continuidade independente de plataforma

**Estado:** registro inicial preparado; publicação deve ser confirmada pelo histórico Git.  
**Objetivo:** tornar a continuidade do ERP independente de contas do ChatGPT, outros fornecedores de IA e profissionais específicos.

**Escopo:** criação do protocolo geral de fonte da verdade e do procedimento de continuidade/transferência. A aplicação efetiva ao repositório deve ser confirmada pelo histórico Git e pelo commit correspondente.

**Próxima ação:** executar auditoria de consistência documental descrita na seção 7.
