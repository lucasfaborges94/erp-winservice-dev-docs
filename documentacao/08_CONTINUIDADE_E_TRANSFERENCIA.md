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


### Sessão — 2026-11-07 — Desativação de módulos com drenagem de execuções

**Responsável/agente:** ChatGPT, sob orientação do responsável pelo projeto  
**Ferramenta/ambiente:** GitHub — repositório documental, branch main  
**Etapa do roadmap:** definição arquitetural / governança transversal de módulos  
**Módulo/processo:** ciclo de vida de execuções e filas  
**Estado:** CONCLUÍDA para atualização documental; implementação não iniciada

#### 1. Definição fornecida pelo responsável
Quando qualquer módulo for desativado em qualquer um dos níveis de controle, todas as execuções já aceitas deverão terminar normalmente, inclusive as que estiverem em andamento e as que estiverem aguardando processamento na fila. Nenhuma nova execução poderá ser aceita ou entrar na fila até que o módulo volte a estar ativo globalmente e para a empresa.

#### 2. Regra registrada
- Fechar imediatamente a admissão de novas execuções no escopo afetado.
- Não cancelar nem interromper abruptamente execuções já aceitas.
- Continuar processando e concluir as execuções em andamento e as já enfileiradas.
- Drenar a fila existente até sua conclusão.
- Não admitir nem enfileirar novas execuções enquanto o módulo estiver inativo, inclusive após a fila esvaziar.
- Reabrir a admissão somente quando os dois níveis estiverem ativos e as demais condições de autorização forem atendidas.
- Aplicar a regra a todos os módulos, inclusive IA.

#### 3. Artefatos alterados
- `documentacao/MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md`: versão 0.1.5.
- `documentacao/06_STATUS_E_RELATORIOS.md`: versão 0.7.0.
- Este registro de continuidade.

#### 4. Estado e limites
A regra foi registrada na proposta documental. Nenhuma ADR foi criada ou alterada nesta sessão. Nenhuma implementação foi realizada em código, banco, filas, workflows ou infraestrutura. Nenhuma alteração foi realizada no Supabase.

#### 5. Pendências técnicas
- Definir mecanismo atômico de fechamento da admissão para evitar corrida entre desativação e enfileiramento.
- Definir como identificar e preservar o conjunto de execuções aceitas antes do fechamento.
- Definir política de retry para execuções já aceitas sem permitir admissão indevida de novas execuções.
- Definir comportamento de falhas permanentes, timeouts e execuções que não conseguem terminar normalmente, sem contrariar a regra de drenagem.
- Criar testes concorrentes para desativação enquanto há execuções ativas e aguardando na fila.


### Sessão — 2026-11-07 — Tratamento de falhas durante a drenagem de módulos

**Responsável/agente:** ChatGPT, sob orientação e aprovação explícita do responsável pelo projeto  
**Ferramenta/ambiente:** GitHub — repositório documental, branch main  
**Etapa do roadmap:** definição arquitetural / governança transversal de módulos  
**Módulo/processo:** ciclo de vida de execuções, filas, retries e falhas  
**Estado:** CONCLUÍDA para registro da decisão; implementação não iniciada

#### 1. Definição aprovada

O responsável aprovou a aplicação geral, a todos os módulos do ERP, destas regras:
- Falhas temporárias permitem novas tentativas da mesma execução, com limite de tentativas e política de backoff.
- Falhas permanentes encerram a execução em `FAILED`, com registro do motivo.
- Timeouts encerram a execução em `TIMEOUT`, com registro do ocorrido.
- Retries preservam identidade e contexto da execução originalmente aceita e não constituem nova execução.
- Para fins de drenagem, a execução é encerrada ao atingir estado terminal, incluindo `COMPLETED`, `FAILED` ou `TIMEOUT`.
- Não será permitido retry ilimitado que impeça a conclusão da drenagem.

Os valores de limite, backoff e timeout, assim como os critérios de classificação de falhas, permanecem pendentes de especificação técnica.

#### 2. Artefatos alterados
- `documentacao/decisoes/ADR-0015-drenagem-e-falhas-de-execucoes.md`: nova ADR-0015, estado APROVADA.
- `documentacao/02_DECISOES_ARQUITETURA.md`: índice atualizado para versão 0.4.0.
- `documentacao/MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md`: versão 0.1.6.
- `documentacao/06_STATUS_E_RELATORIOS.md`: versão 0.8.0.
- Este registro de continuidade.

#### 3. Estado e limites

A regra de tratamento de falhas durante a drenagem foi aprovada e formalizada na ADR-0015. Ela complementa a política geral de desativação com drenagem e não altera o bloqueio de novas admissões enquanto o módulo estiver inativo.

Nenhuma implementação foi realizada em código, banco, filas, workflows ou infraestrutura. Nenhuma alteração foi realizada no Supabase.

#### 4. Pendências técnicas
- Definir critérios determinísticos para classificar falhas temporárias e permanentes.
- Definir limites de tentativas e intervalos/backoff por classe de execução.
- Definir timeouts e tratamento de execução presa ou sem confirmação de resultado.
- Definir persistência do estado e da identidade da execução entre retries.
- Criar testes de falha temporária, falha permanente, timeout, retry e concorrência com desativação.


### Sessão — 2026-11-08 — Modelo funcional operacional do MVP e confronto com Supabase

**Responsável/agente:** ChatGPT, sob orientação do responsável pelo projeto  
**Ferramentas/ambiente:** GitHub — repositório documental, branch main; Supabase — projeto Winservice, inspeção somente leitura  
**Etapa do roadmap:** auditoria/fundação de dados e especificação da Operação V1  
**Módulo/processo:** clientes, equipamentos, OS, visitas, tarefas, relatos e execução técnica  
**Estado:** baseline funcional inicial documentado; implementação não iniciada nesta sessão

#### 1. Objetivo

Consolidar os requisitos funcionais confirmados para o núcleo operacional do MVP e confrontá-los com o schema Supabase vigente, sem executar alterações no banco.

#### 2. Requisitos consolidados

- A OS preserva o histórico completo do atendimento e pode conter várias visitas.
- Uma nova data de atendimento gera nova visita; a visita normalmente permanece na mesma OS. Na criação, o técnico poderá optar por abrir uma nova OS.
- Cada visita contém tarefas próprias. Tarefa representa atividade técnica naquela visita, não checklist nem unidade de execução transferível.
- Se o atendimento continuar em outra data, cria-se nova visita e nova tarefa, normalmente relacionada ao mesmo equipamento e podendo referenciar a tarefa anterior.
- Tarefas não possuem status de execução/conclusão; relatos e sessões de execução preservam sua história.
- Equipamento associado à tarefa é opcional e distingue equipamento identificado, equipamento ainda não identificado e tarefa sem equipamento.
- Visita pode ter relato geral e cada tarefa pode ter relatos próprios. Cada tipo de tarefa poderá definir campos, relatos e evidências obrigatórios. Uma visita exige pelo menos um relato para encerramento.
- Um técnico só pode executar uma visita por vez.
- Check-in automático configurável: visita em `in_displacement`, técnico dentro de 50 metros por 5 minutos (valores configuráveis); inicia a primeira tarefa sem sessão iniciada.
- Afastamento das coordenadas de check-in pausa a sessão; retorno permite retomada automática. Limiares e tolerâncias de saída/retomada ainda precisam ser especificados.
- Sessão em execução não será finalizada automaticamente. Somente sessões pausadas poderão ser finalizadas pelo limite da jornada; o horário efetivo será o da pausa e as coordenadas serão as registradas na pausa. Checkout manual usa horário e coordenadas do próprio checkout.
- Se a visita for finalizada automaticamente, o técnico deverá preencher posteriormente o relato pendente antes de prosseguir com a abertura do aplicativo ou iniciar outro deslocamento/visita.
- Somente o técnico poderá reabrir visita finalizada, dentro de 200 metros do endereço. Gestor e atendente não podem reabrir.
- Gestor pode editar visita finalizada. Edição por técnico e atendente será configurável pelo gestor, separadamente por perfil.
- Alterações devem preservar os dados anteriores e registrar autoria, data/hora, origem e histórico.
- Autoria deve identificar corretamente usuário humano ou agente autônomo.

#### 3. Confronto com o schema

A inspeção atual de tabelas, colunas e foreign keys encontrou estruturas cadastrais e operacionais iniciais: `customers`, `customer_contacts`, `phonenumbers`, `addresses`, `equipments`, `work_orders`, `visits`, `work_order_equipment`, `task_types`, `visit_types`, `visit_requirements`, `visit_type_requirement`, `quotations`, `approval_events` e `documents`.

Não foram encontradas tabelas dedicadas a tarefas por visita, relatos, sessões de execução, eventos GPS históricos ou auditoria append-only. `task_types` é um catálogo básico sem configuração observada de campos/obrigatoriedades. `work_order_equipment` associa OS e equipamento, mas não substitui a tarefa. Há lacunas de autoria em algumas entidades e riscos de autorização/integridade multiempresa já descritos na auditoria técnica de 2026-11-07.

#### 4. Artefatos publicados

- Criado `documentacao/MODELO_FUNCIONAL_OPERACIONAL_MVP.md`, versão 0.1.0.
- Atualizado `documentacao/00_LEIA-ME.md`, versão 0.5.0, com o novo documento no mapa documental.
- Atualizado `documentacao/06_STATUS_E_RELATORIOS.md`, versão 0.9.0, com o baseline funcional e a situação do schema.
- Este registro de continuidade.

#### 5. Estado e limites

A especificação registra requisitos funcionais confirmados pelo responsável, mas não constitui ADR nem modelo lógico final. Questões técnicas ainda pendentes estão enumeradas no documento funcional. O schema foi inspecionado por ferramentas de leitura; nenhuma tabela, coluna, policy, função, trigger, dado ou configuração foi alterada. Não foram consultados registros pessoais de clientes nem realizados testes funcionais, de GPS, concorrência ou segurança.

#### 6. Próximas atividades

1. Revisar o modelo funcional e resolver as pendências da seção 8 do documento.
2. Produzir modelo lógico das entidades e relacionamentos, incluindo tarefas, relatos, sessões e eventos de auditoria.
3. Produzir matriz de conformidade requisito × objeto atual × lacuna × ação proposta.
4. Revisar RLS e integridade multiempresa antes de liberar operações.
5. Somente após aprovação do modelo lógico, elaborar migrações revisáveis e testes.


### Sessão — 2026-11-08 — Modelo lógico e matriz de conformidade da Operação

**Responsável/agente:** ChatGPT, sob orientação do responsável pelo projeto  
**Repositório:** `lucasfaborges94/erp-winservice-dev-docs`, branch `main`  
**Supabase:** projeto Winservice `voaovwphncwbrwtcqnsc`, schema `public`  
**Estado:** proposta de modelo lógico v0.1.0 criada; não aprovada como modelo físico

#### 1. Objetivo

Traduzir o modelo funcional operacional aprovado conceitualmente em entidades, cardinalidades, regras de integridade e matriz de conformidade com o schema atual.

#### 2. Trabalho realizado

- Releitura do modelo funcional operacional e dos documentos de status/decisões.
- Nova inspeção somente leitura de tabelas, colunas, PKs e FKs relevantes no Supabase.
- Modelagem conceitual de cliente, equipamento, OS, visita, tarefa, tipos de tarefa, relatos, sessões de execução, eventos GPS, orçamento e auditoria.
- Definição de distinção entre tarefa sem equipamento, equipamento ainda não identificado e equipamento identificado.
- Definição de proposta de eventos append-only para execução/GPS e histórico de visita.
- Matriz de conformidade com classificação aproveitar, ajustar, criar e revisar.
- Registro de pré-condições de tenancy, RLS, integridade, autoria e concorrência.

#### 3. Principais conclusões

- Estruturas atuais de clientes, endereços, equipamentos, OS e visitas podem ser aproveitadas, mas exigem ajustes e garantias multiempresa.
- `task_types` é catálogo, não instância de tarefa; `work_order_equipment` não substitui tarefa por visita.
- Não foram encontradas estruturas dedicadas para tarefas por visita, relatos, sessões de execução, eventos GPS históricos e auditoria append-only.
- Orçamentos e documentos precisam de relação explícita com tarefa/visita conforme as regras finais.
- A exclusividade de uma visita por técnico exige controle transacional e constraint/índice adequado.
- O modelo proposto ainda possui decisões abertas sobre catálogo de tipos, requisitos dinâmicos, cardinalidade de orçamentos, simultaneidade de sessões, GPS, jornada e auditoria.

#### 4. Artefatos publicados

- Criado `documentacao/MODELO_LOGICO_DADOS_OPERACAO.md`, versão 0.1.0.
- Atualizado `documentacao/00_LEIA-ME.md`, versão 0.6.0.
- Atualizado `documentacao/06_STATUS_E_RELATORIOS.md`, versão 1.0.0.
- Modelo funcional relacionado: `documentacao/MODELO_FUNCIONAL_OPERACIONAL_MVP.md`, versão 0.1.0.

#### 5. Estado, limites e segurança

A proposta não é ADR nem modelo físico aprovado. Não foi gerado SQL de migração e nenhuma alteração foi realizada no banco. A inspeção foi de metadados, sem leitura de dados pessoais. Não foram executados testes de RLS, concorrência, GPS, workflows, API ou aplicativo. A matriz representa conformidade estrutural observada em 2026-11-08.

#### 6. Próximas atividades

1. Validar o modelo lógico e responder às decisões abertas da seção 9.
2. Definir a máquina de estados da visita e o ciclo completo de sessão.
3. Decidir catálogo de tipos, requisitos por tipo e cardinalidade de orçamento ↔ tarefa.
4. Fechar arquitetura de auditoria e integridade multiempresa.
5. Após aprovação, elaborar modelo físico, plano de migração e testes — ainda sem aplicar ao Supabase até autorização explícita.


### Sessão — 2026-11-08 — Revisão do modelo lógico: execução no nível da visita

**Responsável/agente:** ChatGPT, sob orientação do responsável pelo projeto  
**Repositório:** `lucasfaborges94/erp-winservice-dev-docs`, branch `main`  
**Etapa:** especificação funcional/lógica da Operação MVP  
**Estado:** atualização documental publicada; proposta ainda não aprovada como modelo físico

#### 1. Definições recebidas

- Tipos de tarefa partem de catálogo global e podem ser editados independentemente por cada empresa.
- Um orçamento pertence a uma OS, a uma visita, a uma tarefa ou a nenhuma dessas entidades; o vínculo é opcional e exclusivo.
- Ao iniciar uma nova visita, a visita anterior será finalizada mediante relato do técnico.
- A execução é da visita. Tarefas não possuem sessões nem estados próprios de execução.
- Afastamento a partir de 200 metros (configurável) pausa a execução da visita.
- Aproximação a até 50 metros (configurável) permite retomar a execução pausada.
- A máquina de estados reutilizável integra o módulo Chat; não deve ser tratada como máquina de estados da visita.

#### 2. Revisões realizadas no modelo

- Substituídas as entidades conceituais `task_execution_sessions` e `task_execution_events` por `visit_execution_sessions` e `visit_execution_events`.
- Atualizado o diagrama ER, cardinalidades, fluxo de histórico, constraints, matriz de conformidade e ordem de implementação.
- Removida a associação N:M proposta `quotation_tasks`; o orçamento passou a ter vínculo opcional exclusivo com OS, visita ou tarefa, ou nenhum.
- Registrada a personalização independente por empresa do catálogo global, mantendo pendente a estratégia física.
- Registrados os limiares GPS 200 m/50 m e a necessidade de definir precisão, frequência, perda de sinal e antifraude.
- Registrada a finalização da visita anterior mediante relato para iniciar nova visita.
- A arquitetura de histórico imutável por eventos append-only + revisões/snapshots foi incluída como recomendação técnica, ainda não aprovada.

#### 3. Artefatos alterados

- `documentacao/MODELO_LOGICO_DADOS_OPERACAO.md`: v0.2.0.
- `documentacao/00_LEIA-ME.md`: v0.7.0.
- `documentacao/06_STATUS_E_RELATORIOS.md`: v1.1.0.
- Este registro de continuidade.

#### 4. Limites e segurança

A atualização é documental. Não houve geração ou execução de SQL, migration, DDL, DML, alteração de policies, funções, triggers, configurações ou dados no Supabase. A proposta não é modelo físico aprovado. Os estados operacionais completos da visita não foram definidos. A máquina de estados do módulo Chat permanece em escopo separado.

#### 5. Pendências para a próxima sessão

1. Confirmar se o check-in inicial ainda exige permanência de 5 minutos dentro de 50 metros.
2. Definir precisão mínima, frequência GPS, tolerância, perda de sinal e proteção contra localização simulada.
3. Especificar jornada e finalização automática de execução pausada.
4. Definir relato mínimo/obrigatório para finalizar visita anterior ao iniciar uma nova.
5. Validar ou revisar a recomendação de histórico imutável append-only + revisões/snapshots.
6. Definir requisitos dinâmicos e versionamento dos tipos de tarefa.
7. Especificar estados e transições operacionais da visita, sem confundir com a máquina de estados do módulo Chat.
8. Detalhar como personalizações de tipos globais serão preservadas ao publicar novas versões globais.


### Sessão — 2026-11-08 — GPS, finalização de visitas e histórico imutável

**Etapa:** consolidação funcional e atualização dos modelos documental/lógico  
**Estado:** diretrizes funcionais atualizadas; ADR-0016 aprovada; catálogo global versionado ainda como proposta

#### Definições confirmadas

- Check-in inicial: permanência de 5 minutos dentro de 50 metros, configuráveis.
- Precisão GPS inicial: aceitar amostras com incerteza reportada de até 50 metros, configurável. Valores menores indicam maior precisão.
- Manter histórico de posições GPS dos técnicos em entidade própria.
- Em perda de sinal, usar a posição mais recente recebida como última referência conhecida, mantendo sua idade; não simular atualização nem executar transição automática baseada em posição obsoleta.
- Finalizar a visita quando outra tarefa que exija transição para fora da visita atual iniciar, quando iniciar novo deslocamento ou quando terminar a jornada do técnico responsável.
- Na transição por tarefa/deslocamento, exigir relato textual com as palavras do técnico, atualização do status e motivo de abandono/interrupção. Valores exatos de status e motivos permanecem pendentes.
- Aprovação do histórico append-only, formalizada na ADR-0016. Correções devem gerar novos eventos/revisões sem sobrescrever ou excluir o registro original.

#### Proposta ainda pendente

Repositório global de catálogos versionados, com versões globais imutáveis e camada de adoção/personalização por empresa. Sugestão de entidades: `global_catalogs`, `global_catalog_versions`, `global_catalog_items`, `company_catalogs`, `company_catalog_items`, com origem/versão e snapshots locais. A política de comparação, merge e resolução de conflitos ainda precisa de aprovação.

#### Artefatos atualizados

- `MODELO_FUNCIONAL_OPERACIONAL_MVP.md`: v0.4.0.
- `MODELO_LOGICO_DADOS_OPERACAO.md`: v0.3.0.
- `02_DECISOES_ARQUITETURA.md`: v0.5.0.
- `00_LEIA-ME.md`: v0.8.0.
- `06_STATUS_E_RELATORIOS.md`: v1.2.0.
- Nova ficha `ADR-0016-historico-append-only.md`.

#### Segurança e limites

Nenhuma alteração foi feita no Supabase. Não houve DDL, DML, migration, alteração de RLS, função, trigger, configuração ou dado. As tabelas e estruturas descritas são conceituais. O histórico GPS é dado operacional sensível; retenção, frequência e controles de acesso precisam ser definidos antes da implementação.

#### Pendências

1. Confirmar a interpretação do limite de precisão GPS de até 50 m de incerteza reportada.
2. Definir frequência de amostragem, idade máxima operacional e retenção das posições GPS.
3. Definir status de destino e motivos padronizados para abandono/interrupção da visita.
4. Definir como o início de uma nova tarefa exige ou não a transição para fora da visita atual.
5. Aprovar ou ajustar o repositório global de catálogos versionados e política de atualização das personalizações.
6. Definir jornada (horário, fuso, dias e exceções) e comportamento detalhado no fim da jornada.
