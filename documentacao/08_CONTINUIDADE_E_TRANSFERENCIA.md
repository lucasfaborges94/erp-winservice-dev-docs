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

### 2026-11-07 — Governança dos controles e condição de execução do Módulo de IA

**Estado:** CONCLUÍDA para registro das regras conceituais; especificação técnica e implementação pendentes.

**Definições fornecidas pelo responsável:**
- O controle global de disponibilidade do Módulo de IA pertence aos desenvolvedores responsáveis pelo ERP (inicialmente o responsável pelo projeto ou futura equipe).
- O superadmin de cada empresa poderá ativar ou desativar o módulo dentro da própria empresa.
- Cada empresa deverá possuir no mínimo um superadmin.
- Nenhuma execução de IA poderá ocorrer enquanto o módulo não estiver efetivamente ativo, isto é, disponível globalmente e habilitado para a empresa.

**Regras derivadas registradas na proposta:**
- O sistema deverá impedir que a remoção, suspensão ou rebaixamento do último superadmin deixe a empresa sem superadmin ativo.
- Se o módulo for desativado durante uma execução, a execução não poderá continuar realizando novas ações; o mecanismo técnico de interrupção/cancelamento ainda precisa ser especificado.

**Arquivo alterado:**
- `documentacao/MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md` — versão 0.1.3.

**Banco Supabase:** nenhuma alteração realizada.

**Próxima ação:** especificar provisionamento e recuperação do superadmin e o mecanismo de bloqueio/interrupção das execuções quando qualquer nível do módulo for desativado.


### 2026-11-07 — Definição dos níveis de controle do Módulo de IA

**Estado:** CONCLUÍDA para registro da definição conceitual; especificação operacional e implementação pendentes.  
**Contexto:** o responsável definiu que o Módulo de Inteligência Artificial terá dois níveis de controle: disponibilidade global pela plataforma e ativação individual por empresa.

**Definição registrada:**
- A plataforma controla a disponibilidade global do módulo.
- Cada empresa controla sua ativação individual, condicionada à disponibilidade global e às condições de contratação.
- A utilização efetiva exige os dois controles habilitados.
- A ativação do módulo não ativa automaticamente os agentes; cada agente mantém estado próprio e permissões próprias.
- Desativar o módulo bloqueia novas execuções, sem apagar agentes, configurações, históricos ou registros anteriores.
- Provedores de modelos e infraestrutura de execução não se confundem com o módulo funcional nem com os usuários autônomos.

**Arquivo alterado:**
- `documentacao/MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md` — versão 0.1.2.

**Banco Supabase:** nenhuma alteração realizada.

**Próxima ação:** especificar quem pode alterar cada nível, o tratamento de execuções em andamento e os requisitos de contratação, limites e retomada.


### 2026-11-07 — Esclarecimento do conceito de usuários autônomos de IA

**Estado:** CONCLUÍDA para atualização da proposta documental; decisões de implementação ainda pendentes.  
**Contexto:** o responsável esclareceu que a Isa foi concebida como um usuário comum do ERP, operado autonomamente por IA. Cada empresa poderá cadastrar usuários autônomos para funções específicas e personalizar nome e perfil de cada agente. A Isa é a primeira atendente de IA da Winservice, não uma função fixa da plataforma.

**Atualização realizada:**
- A seção de agentes foi reformulada para definir usuários autônomos como usuários ERP operados por IA.
- Diferenciados usuários autônomos de workflows e integrações técnicas.
- Registrado que agentes têm identidade e permissões próprias, sem herdar automaticamente privilégios de quem os cadastrou.
- Delimitados os limites da ADR-0012 à configuração atual da Isa, sem generalizá-los automaticamente a todos os futuros agentes.
- Incluída rastreabilidade entre empresa, configurador, agente executor e atendimento/processo.
- Registrado como pendente decidir se a implementação reutilizará `users` com tipo/modo de execução ou terá entidade complementar.

**Arquivo alterado:**
- `documentacao/MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md` — versão 0.1.1, commit `078712afdff5fa7d74ea18276de912a6d5151dad`.

**Banco Supabase:** nenhuma alteração realizada.

**Próxima ação:** validar o conceito e, depois, definir a representação de dados, ciclo de vida, vínculo com empresa, configuração e matriz de permissões dos usuários autônomos.


### 2026-11-07 — Proposta do modelo de autorização e isolamento multiempresa

**Responsável/agente:** ChatGPT, com autorização do responsável para elaborar a proposta  
**Ferramenta/ambiente:** GitHub e integração Supabase; projeto Winservice, PostgreSQL 17.6  
**Branch:** main (repositório documental)  
**Commit inicial:** não registrado nesta sessão  
**Commit final:** será registrado após a atualização deste documento  
**Etapa do roadmap:** Etapa 1 — auditoria/fundação de dados; preparação das Etapas 2 e 3  
**Módulo/processo:** núcleo da plataforma, identidade, autorização e isolamento multiempresa  
**Estado:** EM_ANDAMENTO — proposta criada; decisões do responsável pendentes

#### 1. Objetivo
Elaborar uma primeira proposta documentada de autorização e isolamento multiempresa, confrontando as decisões aprovadas com a estrutura real do Supabase, sem alterar o banco.

#### 2. Contexto consultado
- `00_FONTE_DA_VERDADE.md`, Project Charter, índice de ADRs, roadmap, mapa de módulos, stack, governança e continuidade.
- ADR-0001, ADR-0002, ADR-0011 e ADR-0012.
- Relatório `documentacao/relatorios/2026-11-07-auditoria-tecnica-supabase.md`.
- Projeto Supabase `voaovwphncwbrwtcqnsc`, PostgreSQL 17.6, schema `public`.
- Metadados de tabelas/colunas, constraints, FKs e policies RLS relevantes; somente consultas de leitura.

#### 3. Trabalho realizado
- Identificadas as estruturas atuais de empresas, usuários, vínculos usuário–empresa, papéis, permissões, módulos e telas.
- Confrontadas as estruturas com as ADRs-0011 e 0012.
- Registradas divergências e pontos que exigem confirmação, incluindo a relação entre `users.identifier`, `users.auth_user_identifier`, `company_users.user_identifier` e `auth.uid()`.
- Elaborada proposta com princípios de autorização, fluxo lógico, tenancy, identidade técnica, escopo da Isa, testes mínimos e decisões pendentes.

#### 4. Decisões
- Decisões aprovadas: nenhuma nova decisão arquitetural nesta sessão.
- Propostas ainda não aprovadas: modelo de autorização e isolamento multiempresa v0.1.0.
- Decisões pendentes: estratégia de tenancy; usuários em múltiplas empresas; transporte/validação do contexto de empresa; semântica de múltiplos papéis e exceções; papel administrativo; classificação de dados globais; identidades técnicas; vínculo Auth–ERP.

#### 5. Artefatos alterados
| Arquivo/objeto | Tipo de alteração | Referência |
|---|---|---|
| `documentacao/MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md` | Criado documento de proposta v0.1.0 | Commit `0846fe9c1626c2705e954bba01eae445448dd59d` |
| `documentacao/00_LEIA-ME.md` | Atualizado índice documental para v0.4.0 | Commit registrado após gravação |
| `documentacao/08_CONTINUIDADE_E_TRANSFERENCIA.md` | Registro desta sessão | Commit desta atualização |

#### 6. Verificações e testes
| Verificação/teste | Ambiente/versão | Resultado | Evidência |
|---|---|---|---|
| Leitura dos documentos de autoridade e decisões | GitHub / main | Executada | Arquivos consultados nesta sessão |
| Inspeção de metadados e policies | Supabase / PostgreSQL 17.6 | Executada somente leitura | Consultas de catálogo e relatório técnico |
| Alterações no banco | Supabase Winservice | Não realizadas | Nenhum DDL/DML ou execução de função |
| Testes funcionais de autorização | Não executados | Pendentes | Dependem do modelo aprovado e ambiente controlado |

#### 7. Estado ao encerrar
- Concluído: proposta documental v0.1.0 criada e indexada.
- Parcial: modelo conceitual e evidências estruturais identificados.
- Não iniciado: aprovação das decisões pendentes, especificação normativa final, matriz por entidade, desenho final de RLS/constraints e implementação.
- Não verificado: fluxo de autenticação/provisionamento completo, backend/API, n8n e comportamento efetivo em chamadas reais.

#### 8. Pendências e bloqueios
| Item | Impacto/prioridade | Dependência ou responsável | Próxima ação |
|---|---|---|---|
| Aprovar ou ajustar estratégia de tenancy | Crítica | Responsável pelo projeto | Deliberar sobre banco/schema compartilhado e isolamento por linha |
| Definir semântica de usuário e vínculo multiempresa | Alta | Responsável pelo projeto | Confirmar múltiplos vínculos e seleção da empresa de contexto |
| Definir semântica RBAC | Alta | Responsável pelo projeto | Deliberar agregação de papéis, negações e exceções individuais |
| Confirmar vínculo Auth–ERP | Crítica | Responsável técnico | Inspecionar fluxo de criação e sincronização de usuários |
| Classificar todas as tabelas | Alta | Depende do modelo aprovado | Elaborar matriz global/tenant/herdado |

#### 9. Riscos e observações
A proposta não é decisão aprovada e não autoriza alterações. Os achados estruturais não substituem testes funcionais nem comprovam, isoladamente, exploração de vulnerabilidade. O banco permaneceu inalterado.

#### 10. Próxima atividade recomendada
Revisar as decisões pendentes da seção 13 do modelo e obter deliberação explícita do responsável. Depois atualizar a proposta e, se necessário, preparar ADRs antes de especificar alterações no banco.

#### 11. Instruções para quem assumir
Ler primeiro `documentacao/MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md`, ADR-0001, ADR-0011 e ADR-0012, além do relatório de auditoria. Não aplicar migrations nem alterar policies até aprovação explícita do modelo e da estratégia de implementação.


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


### Sessão — 2026-11-07 — Governança geral dos módulos e superadmin

**Responsável/agente:** ChatGPT, sob orientação do responsável pelo projeto  
**Ferramenta/ambiente:** GitHub — repositório documental, branch main  
**Etapa do roadmap:** definição arquitetural / autorização multiempresa  
**Módulo/processo:** governança transversal de módulos, provisionamento de usuários e agentes autônomos  
**Estado:** CONCLUÍDA para atualização documental; implementação não iniciada

#### 1. Objetivo
Registrar as regras definidas pelo responsável para o papel de superadmin e a ativação dos módulos do ERP.

#### 2. Decisões registradas nesta sessão
- Agentes de IA nunca poderão ser superadmin.
- O primeiro usuário de cada empresa será obrigatoriamente um usuário humano com papel de superadmin.
- Cada empresa deverá manter ao menos um superadmin humano ativo; o sistema deverá impedir remoção, suspensão ou rebaixamento do último superadmin humano ativo.
- A ativação em dois níveis — disponibilidade global pela plataforma e ativação individual por empresa — aplica-se a todos os módulos do ERP, não somente ao Módulo de IA.
- Um módulo somente poderá operar quando estiver ativo nos dois níveis. O backend/API/orquestrador deverá impor essa condição; desativação deverá impedir novas ações de operações em andamento.

#### 3. Artefatos alterados
- `documentacao/MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md`: versão 0.1.4.
- `documentacao/06_STATUS_E_RELATORIOS.md`: versão 0.6.0.
- Este registro de continuidade.

#### 4. Estado e limites
As regras foram registradas na proposta documental e no status. Não foram criadas ou alteradas ADRs nesta sessão; a proposta permanece sujeita à formalização arquitetural. Nenhuma alteração foi realizada no Supabase, código, policies, funções, triggers ou configurações.

#### 5. Pendências
- Especificar o enforcement técnico transversal dos estados dos módulos.
- Definir interrupção/cancelamento seguro de operações em andamento para qualquer módulo.
- Especificar recuperação de acesso administrativo.
- Formalizar as decisões em ADRs, após revisão e aprovação explícita do pacote correspondente.
