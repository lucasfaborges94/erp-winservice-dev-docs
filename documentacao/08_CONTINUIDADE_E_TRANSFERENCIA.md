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

### 2026-11-07 — Auditoria técnica somente leitura do Supabase

**Responsável/agente:** ChatGPT, com autorização do responsável pelo projeto  
**Ferramenta/ambiente:** integração Supabase conectada; projeto Winservice, PostgreSQL 17.6  
**Branch:** main (repositório documental)  
**Commit inicial:** não registrado nesta sessão  
**Commit final:** `13b3723e3d27fe7b65a487c4a59abf831157ae6c`  
**Etapa do roadmap:** fundação de dados / auditoria técnica  
**Módulo/processo:** segurança, RLS, tenancy, integridade e governança do banco  
**Estado:** CONCLUÍDA para a inspeção e registro documental; correções não iniciadas

#### 1. Objetivo
Registrar o estado observado do projeto Supabase Winservice em 07/11/2026 sem alterar o banco, identificando problemas confirmados, riscos e itens não verificados.

#### 2. Contexto consultado
- Projeto Supabase Winservice: `voaovwphncwbrwtcqnsc`.
- PostgreSQL 17.6; schema `public`.
- Índice arquitetural `documentacao/02_DECISOES_ARQUITETURA.md`.
- Status anterior `documentacao/06_STATUS_E_RELATORIOS.md`.
- Governança documental e procedimento de continuidade.
- Decisões ADR-0002, ADR-0008, ADR-0011, ADR-0012 e ADR-0014 conforme índice e contexto arquitetural.

#### 3. Trabalho realizado
- Inspecionados metadados de tabelas, RLS, policies, grants, funções, triggers, foreign keys e índices.
- Consultados alertas do Security Advisor e Performance Advisor.
- Avaliadas evidências estruturais relacionadas ao isolamento multiempresa e às decisões arquiteturais.
- Criado relatório técnico datado, separando achados confirmados, riscos, recomendações e limitações.
- Atualizado o status do projeto com referência ao relatório.

#### 4. Decisões
- Decisões aprovadas e seus IDs ADR: nenhuma nova decisão arquitetural foi tomada.
- Propostas ainda não aprovadas: nenhuma formalizada nesta sessão.
- Decisões pendentes do responsável: autorizar ou não futuras correções, após análise do relatório.

#### 5. Artefatos alterados

| Arquivo/objeto | Tipo de alteração | Referência |
|---|---|---|
| `documentacao/relatorios/2026-11-07-auditoria-tecnica-supabase.md` | Criado relatório de auditoria | Commit `a7ceb8aaf068457bad7302873734cdc5aabb17af` |
| `documentacao/06_STATUS_E_RELATORIOS.md` | Atualizado estado conhecido e referência ao relatório | Commit `13b3723e3d27fe7b65a487c4a59abf831157ae6c` |
| `documentacao/08_CONTINUIDADE_E_TRANSFERENCIA.md` | Registro desta sessão | Commit desta atualização |

#### 6. Verificações e testes

| Verificação/teste | Ambiente/versão | Resultado | Evidência |
|---|---|---|---|
| Inspeção de metadados do schema | Supabase Winservice / PostgreSQL 17.6 | Executada por consultas de leitura | Relatório técnico |
| Inspeção de policies e grants | Supabase Winservice | Achados registrados | Relatório técnico |
| Inspeção de funções e triggers | Supabase Winservice | Definições inspecionadas; funções de negócio não executadas | Relatório técnico |
| Inspeção de alertas | Security Advisor e Performance Advisor | Alertas registrados | Relatório técnico |
| Testes funcionais/exploração | Não executados | Não verificados | Fora do escopo somente leitura |

#### 7. Estado ao encerrar
- Concluído: inspeção de leitura e documentação dos achados disponíveis.
- Parcial: análise de segurança e integridade limitada ao banco; camadas externas não auditadas.
- Não iniciado: correções, testes funcionais, testes de autorização e migrações.
- Não verificado: uso efetivo da integração Auvo, proteções de backend externo, migrations versionadas e consistência dos dados existentes.

#### 8. Pendências e bloqueios

| Item | Impacto/prioridade | Dependência ou responsável | Próxima ação |
|---|---|---|---|
| Revisar função de ingestão de eventos | Crítica | Responsável pelo projeto | Avaliar fluxo externo e estratégia de autenticação |
| Revisar policies RLS e grants | Crítica | Responsável pelo projeto | Definir matriz de acesso por empresa, usuário e operação |
| Reforçar integridade multiempresa | Alta | Depende da definição formal de tenancy | Modelar constraints e validações |
| Revisar trigger de snapshot de endereço | Alta | Responsável técnico | Corrigir em mudança futura e criar teste |
| Comprovar autonomia da Isa | Alta | Depende da inspeção de backend/n8n | Auditar enforcement externo |
| Comparar migrations com schema | Alta | Repositório de implementação | Localizar histórico e verificar divergências |

#### 9. Riscos e observações
- Há policies permissivas sem filtro por empresa em entidades relevantes.
- Há privilégios amplos concedidos a `anon` e `authenticated`; o impacto efetivo depende de RLS e dos caminhos privilegiados.
- A função `authorize_integration_sync_event` é `SECURITY DEFINER`, executável por papéis públicos e não apresentou validação de autenticidade do remetente no corpo inspecionado.
- Há lacunas de integridade referencial entre entidades relacionadas a empresas.
- Foi identificada incompatibilidade entre `set_visit_address_snapshot` e o schema atual de `addresses`.
- Nenhuma alteração foi realizada no Supabase.

#### 10. Próxima atividade recomendada
Fazer uma revisão técnica do relatório e decidir a ordem de correção. Antes de qualquer alteração no banco, definir o modelo de tenancy e a matriz de autorização esperada; depois elaborar mudanças isoladas, revisáveis e testáveis.

#### 11. Instruções para quem assumir
Ler primeiro `documentacao/relatorios/2026-11-07-auditoria-tecnica-supabase.md` e os ADRs relacionados. Não executar correções automaticamente. Confirmar o estado atual do banco antes de qualquer intervenção, pois este relatório é uma fotografia datada de 07/11/2026.


### 2026-11-07 — Protocolo de continuidade independente de plataforma

**Estado:** CONCLUÍDA para a atualização documental desta iniciativa; arquivos verificados na branch main.  
**Objetivo:** tornar a continuidade do ERP independente de contas do ChatGPT, outros fornecedores de IA e profissionais específicos.

**Escopo:** criação do protocolo geral de fonte da verdade e do procedimento de continuidade/transferência, com atualização do leia-me, da governança e do status.

**Arquivos publicados e verificados na branch main:**
- 00_FONTE_DA_VERDADE.md — commit 64b15eccf148dc514f2451f250d6bab58cb17f42
- documentacao/08_CONTINUIDADE_E_TRANSFERENCIA.md — commit e78dc802c1779c42d6598b901c8bc339537e576f
- documentacao/00_LEIA-ME.md — commit 07340db94838ffea92dbef801a25f0bc01d8de90
- documentacao/07_GOVERNANCA_DOCUMENTAL.md — commit 23707622b5954fda0c531d886f8b679877d7e77d
- documentacao/06_STATUS_E_RELATORIOS.md — commit 12098f5f3abb78bfaeed9baa57d0a4a1017331bc

**Verificação:** os cinco arquivos foram relidos com sucesso no GitHub após as gravações.

**Próxima ação:** executar auditoria de consistência documental descrita na seção 7.
