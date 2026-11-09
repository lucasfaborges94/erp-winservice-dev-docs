# Continuidade e transferência de trabalho — ERP Winservice

**Documento:** Procedimento de continuidade e transferência  
**Versão:** 1.9.0  
**Data:** 2026-11-08  
**Estado:** procedimento estabelecido; registro inicial de referência

## 1. Objetivo

Permitir que o desenvolvimento do ERP Winservice seja retomado por outra conta do ChatGPT, outra IA, outro profissional ou outra equipe sem depender do histórico privado de conversas.

Este documento define o que deve ser registrado ao encerrar uma sessão, como outra pessoa ou agente deve retomar o trabalho, como manter o status verificável e como transferir tarefas entre ferramentas e responsáveis.

As regras gerais de autoridade e trabalho estão em [00_FONTE_DA_VERDADE.md](../00_FONTE_DA_VERDADE.md).

## 2. Regra de registro incremental e encerramento

Toda sessão que produza avanço relevante deve atualizar este documento ou criar um registro individual no histórico definido abaixo.

O registro é incremental: durante a conversa, adicionar os pontos-chave assim que forem confirmados, sem esperar o encerramento. Aprovações, decisões, requisitos, correções de entendimento, descobertas técnicas, mudanças de escopo, artefatos, resultados de testes, riscos e próximos passos devem ser registrados quando se tornarem relevantes.

Uma sessão pode abranger várias mensagens correlatas. Não criar um arquivo para cada mensagem; atualizar o registro da sessão em andamento. Criar um novo registro quando houver novo marco, novo objetivo ou retomada que justifique rastreabilidade independente.

Antes de cada resposta sobre o ERP, consultar novamente o GitHub, a fonte da verdade, o protocolo, o contexto operacional e os documentos pertinentes. O registro de sessão não substitui essa consulta.

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

## 7. Índice de sessões e registros de continuidade

Manter os registros mais recentes primeiro. Cada entrada deve apontar para o registro detalhado, quando separado. O resumo operacional vigente fica em [CONTEXTO_ATUAL_ERP.md](CONTEXTO_ATUAL_ERP.md). Esse resumo é um índice rápido e não substitui documentos normativos, ADRs ou evidências.

### Sessão — 2026-11-08 — aprovação do protocolo de consulta contínua e registro incremental

**Responsável:** responsável pelo projeto  
**Estado:** protocolo aprovado; atualização documental aplicada nesta publicação.

#### Objetivo
Estabelecer consulta obrigatória ao GitHub antes de cada resposta sobre o ERP e registro incremental de sessões e pontos-chave.

#### Deliberação
O responsável aprovou o protocolo proposto, com a exigência adicional de registrar sessões pertinentes conforme o projeto avança e pontos-chave são definidos.

#### Regras incorporadas
- Consulta à versão atual de `00_FONTE_DA_VERDADE.md` antes de cada resposta sobre o ERP, não apenas no início da conversa.
- Consulta ao protocolo, continuidade, contexto operacional e documentos/ADRs específicos pertinentes.
- Verificação de mudanças no HEAD remoto desde o último registro, quando disponível.
- Registro incremental de aprovações, decisões, requisitos, correções, descobertas, artefatos, testes, riscos, pendências e próximos passos.
- Uma sessão pode abranger várias mensagens; não criar um arquivo por mensagem.
- Atualização do índice de sessões e do resumo operacional quando houver mudança relevante.
- Mensagens sem conteúdo novo continuam exigindo consulta, mas não exigem commits artificiais.

#### Artefatos
- `00_FONTE_DA_VERDADE.md`
- `documentacao/PROTOCOLO_DE_ATUACAO_IA_GITHUB.md`
- `documentacao/08_CONTINUIDADE_E_TRANSFERENCIA.md`
- `documentacao/07_GOVERNANCA_DOCUMENTAL.md`
- `documentacao/00_LEIA-ME.md`
- `documentacao/06_STATUS_E_RELATORIOS.md`
- `documentacao/CONTEXTO_ATUAL_ERP.md` (novo)

#### Limites
Esta sessão atualiza a governança e a memória documental. Não representa alteração de código, schema, banco, migrations, políticas ou infraestrutura do ERP.

#### Próxima atividade
Retomar a próxima atividade técnica pendente indicada no contexto operacional e na continuidade, após consulta integral dos documentos e ADRs pertinentes.

## 8. Estado inicial de continuidade — 2026-11-07

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

### 2026-11-08 — Encerramento da sessão e preparação da retomada do MVP

#### 1. Objetivo

Encerrar a sessão de trabalho, consolidar no repositório todas as decisões e atividades realizadas nesta conversa e deixar instruções suficientes para retomar o desenvolvimento do MVP na próxima sessão, sem depender do histórico desta conversa.

#### 2. Contexto consultado

Foram consultados diretamente na branch `main`:
- `00_FONTE_DA_VERDADE.md`;
- `documentacao/PROTOCOLO_DE_ATUACAO_IA_GITHUB.md`;
- `documentacao/00_LEIA-ME.md`;
- `documentacao/06_STATUS_E_RELATORIOS.md`;
- `documentacao/07_GOVERNANCA_DOCUMENTAL.md`;
- `documentacao/08_CONTINUIDADE_E_TRANSFERENCIA.md`;
- árvore de arquivos e metadados do repositório.

#### 3. Trabalho realizado

- Confirmado que o repositório já possuía fonte da verdade, governança documental, ADRs, status e histórico de continuidade.
- Formalizado o GitHub como memória documental oficial e persistente do projeto.
- Atualizado o protocolo para exigir recuperação do contexto no início de novas sessões e manutenção da memória nos documentos especializados.
- Atualizado o mapa documental para explicar a distinção entre memória oficial e contexto auxiliar de ferramentas de IA.
- Relidos os arquivos publicados após as alterações e confirmados os commits.
- Atualizados o status do desenvolvimento e este registro de continuidade para preparar a retomada.

#### 4. Decisões e instruções do responsável

- O repositório GitHub `lucasfaborges94/erp-winservice-dev-docs` é a memória documental oficial do ERP Winservice.
- Histórico de conversas, memória interna de IA, resumos e cópias anexadas são auxiliares e não substituem a documentação versionada.
- O conhecimento deve permanecer organizado em documentos especializados; não criar um arquivo monolítico de memória.
- O responsável encerrou a sessão por hoje e determinou que o trabalho no desenvolvimento do MVP seja retomado amanhã/próxima sessão.
- A retomada deve partir do último estado documentado da frente Operação, conferindo pendências nos modelos funcional e lógico e verificando o estado remoto atual.
- Não interpretar a intenção de continuar o MVP como autorização automática para executar DDL, modificar Supabase, alterar infraestrutura ou aprovar propostas ainda pendentes.

#### 5. Artefatos alterados nesta sessão

- `00_FONTE_DA_VERDADE.md`
- `documentacao/PROTOCOLO_DE_ATUACAO_IA_GITHUB.md`
- `documentacao/00_LEIA-ME.md`
- `documentacao/06_STATUS_E_RELATORIOS.md`
- `documentacao/08_CONTINUIDADE_E_TRANSFERENCIA.md`

Os três primeiros foram atualizados na etapa de formalização da memória. Os dois últimos foram atualizados no fechamento desta sessão.

#### 6. Verificações e commits

- Os quatro primeiros arquivos da etapa de formalização foram relidos no GitHub após a publicação.
- Commits da etapa de formalização:
  - `63cb4faacf854a32a7516a30caafcb6ca0d5fc00` — `00_FONTE_DA_VERDADE.md`;
  - `4061d55d3fe6bb61f8bec4a4f5e785e65d85758f` — `documentacao/PROTOCOLO_DE_ATUACAO_IA_GITHUB.md`;
  - `fb5b7ebea3ab4b43b8a798a4e5580201ef5e0617` — `documentacao/00_LEIA-ME.md`;
  - `0c7cb5e414c7e66c76b70f068b42adaf23ebe826` — primeira atualização deste registro de continuidade.
- Os commits de fechamento do status e da continuidade serão registrados após a publicação e verificação correspondentes.

#### 7. Estado ao encerrar

A memória documental oficial e seu procedimento de recuperação estão formalizados no repositório. O status e o registro de continuidade passam a indicar explicitamente que a próxima sessão deverá retomar o desenvolvimento do MVP.

Nenhum desenvolvimento funcional do MVP foi executado nesta sessão; o trabalho realizado foi de governança e documentação.

#### 8. Pendências e bloqueios

- Retomar o desenvolvimento do MVP na próxima sessão.
- Antes de começar, conferir branch, HEAD, documentos de continuidade e status.
- Revisar as pendências ainda abertas nos modelos funcional e lógico da Operação e identificar a próxima atividade concreta e seus critérios de aceite.
- Não assumir aprovação do modelo lógico como modelo físico.
- Não executar alterações no Supabase ou infraestrutura sem autorização específica.
- Proteção da branch `main` e estado de Secret Scanning continuam sem alteração; não foram configurados nesta sessão.

#### 9. Riscos e observações

O repositório não é carregado automaticamente em toda conversa. A continuidade depende de executar o protocolo de recuperação pela integração do GitHub. Se o acesso não estiver disponível, a limitação deverá ser informada e o contexto não deverá ser presumido.

#### 10. Próxima atividade recomendada

Retomar o trabalho de desenvolvimento do MVP, começando pela frente Operação e pelo último estado registrado nos documentos funcionais, lógicos e de continuidade. Confirmar o escopo exato da próxima entrega antes de qualquer alteração técnica.

#### 11. Instruções para quem assumir

Começar pelo `00_FONTE_DA_VERDADE.md`, seguir o protocolo de atuação com GitHub, ler esta sessão e comparar a referência documental com o HEAD atual. Consultar `06_STATUS_E_RELATORIOS.md`, `MODELO_FUNCIONAL_OPERACIONAL_MVP.md`, `MODELO_LOGICO_DADOS_OPERACAO.md` e ADRs pertinentes. Distinguir requisitos confirmados de propostas pendentes. Não modificar Supabase, código ou infraestrutura sem a autorização correspondente.


### 2026-11-08 — Formalização do repositório como memória documental oficial

#### 1. Objetivo

Formalizar o uso do repositório `erp-winservice-dev-docs` como memória documental persistente do ERP Winservice e definir o procedimento de recuperação e manutenção desse conhecimento entre conversas, contas, ferramentas e profissionais.

#### 2. Contexto consultado

- `00_FONTE_DA_VERDADE.md`
- `documentacao/PROTOCOLO_DE_ATUACAO_IA_GITHUB.md`
- `documentacao/00_LEIA-ME.md`
- `documentacao/07_GOVERNANCA_DOCUMENTAL.md`
- `documentacao/08_CONTINUIDADE_E_TRANSFERENCIA.md`
- Árvore completa da branch `main` e metadados do repositório.

#### 3. Trabalho realizado

- Confirmada a existência de fonte da verdade, governança documental, ADRs, status e histórico de continuidade.
- Formalizado o repositório como memória documental oficial e persistente.
- Definida a sequência mínima de recuperação de contexto no início de novas sessões.
- Definida a classificação do conhecimento e o local adequado para registrar decisões, requisitos, propostas, implementação, auditorias e continuidade.
- Reforçada a distinção entre documentação aprovada, implementação verificada e contexto auxiliar de ferramentas de IA.

#### 4. Decisões

- O repositório GitHub é a memória documental oficial do projeto.
- Memórias internas de IA, conversas, anexos e resumos são auxiliares e não substituem os documentos versionados.
- Não será criado um arquivo monolítico de memória; o conhecimento continuará organizado por assunto e tipo de artefato.
- Toda sessão que produza avanço relevante deverá registrar o resultado no documento especializado e atualizar a continuidade.

#### 5. Artefatos alterados

- `00_FONTE_DA_VERDADE.md`: inclusão da definição de memória documental oficial e atualização de versão.
- `documentacao/PROTOCOLO_DE_ATUACAO_IA_GITHUB.md`: inclusão do procedimento de inicialização de contexto e manutenção da memória.
- `documentacao/00_LEIA-ME.md`: explicitação do papel do repositório como memória oficial.

#### 6. Verificações e testes

- Leitura dos arquivos e metadados diretamente no GitHub, branch `main`.
- Verificação da estrutura documental e das referências existentes.
- Não foram executados testes de aplicação, alterações de banco ou operações de infraestrutura.

#### 7. Estado ao encerrar

A estrutura documental existente foi mantida e complementada. A memória oficial está formalmente definida; sua eficácia depende de cumprir o procedimento de consulta e atualização em cada sessão relevante.

#### 8. Pendências e bloqueios

- Verificar futuramente se o fluxo de retomada e a atualização dos registros estão sendo cumpridos de forma consistente.
- A proteção da branch `main` e o estado de Secret Scanning continuam sem alteração nesta sessão.

#### 9. Riscos e observações

O repositório não carrega automaticamente todo o seu conteúdo para cada conversa. O agente precisa consultar os arquivos pela integração disponível. Se o acesso falhar, deve declarar a limitação em vez de presumir que possui o contexto completo.

#### 10. Próxima atividade recomendada

Retomar a próxima atividade técnica registrada na sessão anterior, seguindo o procedimento de retomada e verificando o estado remoto atual antes de qualquer alteração.

#### 11. Instruções para quem assumir

Começar por `00_FONTE_DA_VERDADE.md`, seguir o protocolo de atuação com GitHub, ler este registro e comparar o commit de referência com o HEAD atual. Consultar os documentos específicos da próxima tarefa e não inferir aprovação ou implementação a partir de propostas documentais.

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
- Check-in automático configurável: visita em `in_displacement`, técnico dentro de 50 metros por 5 minutos (valores configuráveis); inicia a execução da visita; tarefas são registradas dentro da visita e não possuem início individual.
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
- Finalizar a visita quando iniciar novo deslocamento ou quando terminar a jornada do técnico responsável. Tarefas não são iniciadas individualmente e não constituem gatilho de finalização.
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


### Sessão — 2026-11-08 — requisitos, evidências e aceite digital

**Etapa:** consolidação funcional e atualização dos modelos funcional/lógico  
**Estado:** requisitos funcionais confirmados; proposta lógica ainda não aprovada como modelo físico

#### Definições confirmadas

- Tarefas não são iniciadas individualmente nem possuem status de execução/conclusão. Permanecem vinculadas à visita; relatos e evidências registram o que ocorreu.
- Pendências são indicadores derivados de requisitos não cumpridos: `visit_requirements` na visita e `task_type_requirements` nas tarefas.
- Dois indicadores derivados, na visita e na tarefa: `has_pending_mandatory_requirements` e `has_pending_optional_requirements`. Não são editáveis manualmente.
- Cada tentativa de finalização normal gera avaliação histórica completa, mesmo quando bloqueada, cobrindo requisitos da visita e de todas as tarefas.
- Requisito obrigatório não cumprido bloqueia conclusão normal. Requisito opcional não cumprido não bloqueia, mas gera pendência para revisão.
- A visita pode ser encerrada excepcionalmente por interrupção/abandono com requisitos obrigatórios pendentes; isso não equivale a conclusão normal. Pendências permanecem para análise e eventual reavaliação.
- Gestor/atendente autorizado pode aceitar ausência de requisito opcional com justificativa. Técnico pode cumprir ou solicitar revisão, mas não dispensar. A dispensa não transforma resultado original em cumprido.
- Evidências previstas: fotos, documentos e assinaturas. Foto deve ser visualizada e confirmada como legível pelo técnico antes do envio; documentos podem ser fotografados ou selecionados como arquivos.
- Evidência só satisfaz requisito após confirmação de armazenamento/validação pelo backend. Upload pode ocorrer em segundo plano, com timeout e retries limitados, política ainda a definir, e idempotência.
- Upload obrigatório não confirmado bloqueia conclusão normal; visita pode permanecer em andamento ou ser encerrada excepcionalmente. Upload opcional não confirmado gera pendência opcional.
- Evidências devem guardar `created_at` (`timestamptz`), latitude/longitude do local de captura quando disponíveis e autorizadas, além de distinguir captura, recebimento e confirmação do upload.
- Aceite digital padrão: link individual para o dispositivo pessoal do cliente. Alternativa: assinatura presencial no dispositivo do técnico; coleta offline protegida e sincronizada posteriormente.
- Cliente deve ser identificado antes da assinatura. Não coletar impressão digital. Método inicial de identificação permanece em aberto; data de nascimento foi considerada como alternativa e CPF não deve ser exigido indiscriminadamente.
- Resumo de aceite: data/hora, cliente, equipamento, técnico, solicitação original, relato geral e relatos das tarefas, fotografias incorporadas ao corpo e valores discriminados da visita/tarefas. Documentos somente como anexos.
- Resumo e anexos têm versão/hash imutável. Alterações relevantes exigem novo aceite; alterações administrativas sem impacto no conteúdo aceito não exigem.
- Cópia ao cliente após confirmação; em offline, após sincronização.
- Nenhuma modalidade é automaticamente juridicamente superior apenas pelo dispositivo utilizado.

#### Artefatos atualizados nesta sessão

- `documentacao/MODELO_FUNCIONAL_OPERACIONAL_MVP.md`: v0.5.0.
- `documentacao/MODELO_LOGICO_DADOS_OPERACAO.md`: v0.4.0.
- `documentacao/06_STATUS_E_RELATORIOS.md`: v1.3.0.
- `documentacao/00_LEIA-ME.md`: v0.9.0.
- Este registro de continuidade: v1.1.0.

#### Pendências para próxima sessão

1. Definir parâmetros de timeout, retries, backoff, formatos/tamanho e retenção de evidências.
2. Definir método inicial de identificação do signatário, validade/expiração/revogação de links e critérios de autenticação.
3. Definir matriz de dispensa justificada para requisitos obrigatórios após encerramento excepcional.
4. Detalhar quais alterações são relevantes e exigem novo aceite.
5. Definir armazenamento local protegido e política de limpeza após sincronização.
6. Definir jornada e procedimento para sessão ainda ativa ao fim da jornada.
7. Fechar estados e motivos padronizados de visita e a interpretação de precisão GPS, frequência e idade máxima.
8. Validar a proposta lógica antes de qualquer DDL. Nenhuma alteração foi realizada no Supabase.

#### Segurança e limites

A atualização é documental. Nenhum SQL, DDL, DML, migration, policy, função, trigger, configuração ou dado do Supabase foi alterado. As entidades descritas são propostas lógicas. Não foram feitos testes funcionais de upload, aceite digital, offline, autenticação, GPS ou conformidade jurídica.

### Sessão — 2026-11-08 — aprovação da máquina única da visita e arquitetura da Base de Conhecimento

**Etapa:** consolidação funcional e atualização dos modelos  
**Estado:** decisões aprovadas pelo responsável; documentação atualizada nesta publicação

#### Aprovações recebidas

O responsável aprovou expressamente:
- eliminar a Máquina B;
- não manter estados ou entidades independentes de sessão no MVP;
- registrar o histórico operacional/temporal em um único fluxo append-only de eventos da visita (visit_events);
- manter tarefas sem estados de execução/conclusão;
- adotar conceitualmente a arquitetura de Base de Conhecimento em quatro camadas.

#### Consolidação da visita

A visita é a única entidade com ciclo de vida operacional:
- pending → in_displacement, in_progress, prevented, canceled;
- in_displacement → pending, in_progress, prevented, canceled;
- in_progress → paused, completed, interrupted;
- paused → in_progress, interrupted.

Estados terminais: completed, interrupted, prevented, canceled.
Estados terminais não podem ser reabertos. A regra anterior de reabertura por técnico dentro de 200 metros foi substituída; continuidade exige uma nova visita.

Revogação de autenticação:
- pending: permanece;
- in_displacement: retorna para pending;
- in_progress e paused: transicionam para interrupted;
- terminais: permanecem inalterados.

Jornada:
- o fim da jornada não encerra automaticamente visita nem execução;
- início/check-in fora da jornada exige confirmação do técnico no aplicativo.

Eventos:
- a proposta de visit_execution_sessions e visit_execution_events foi removida do modelo lógico;
- visit_events passa a registrar diretamente transições, início, pausas, retomadas, checkout, revogação e demais acontecimentos;
- duração e intervalos são projeções calculadas a partir dos eventos;
- a estrutura física e os controles de concorrência/idempotência ainda precisam de especificação.

Check-in automático inicial permanece futuro, fora do MVP, com parâmetros iniciais de 50 metros e 5 minutos configuráveis. As regras funcionais de pausa/retomada GPS permanecem registradas; automação depende de validação de GPS recente e confiável.

#### Arquitetura aprovada conceitualmente para a Base de Conhecimento

1. Domínio: glossário, entidades, atributos semânticos e relacionamentos.
2. Operação: processos, regras de negócio, estados, exceções e procedimentos.
3. Contratos: APIs, operações, parâmetros, permissões, erros e formatos.
4. Guias por público: materiais para técnicos, atendentes, gestores, administradores e agentes, derivados das três camadas normativas.

A autorização não decorre do conhecimento. Backend/API/RLS continuam sendo responsáveis por impor permissões de humanos e agentes. Os guias não devem duplicar ou substituir fontes normativas. Conteúdo deverá manter versão, origem, vigência e nível de autoridade.

#### Artefatos atualizados

- MODELO_FUNCIONAL_OPERACIONAL_MVP.md: v0.6.0.
- MODELO_LOGICO_DADOS_OPERACAO.md: v0.5.0.
- BASE_CONHECIMENTO_ERP.md: criado como arquitetura/governança inicial.
- 00_LEIA-ME.md: mapa documental atualizado.
- 06_STATUS_E_RELATORIOS.md: v1.5.0.
- Este documento: v1.2.0.

#### Pendências

1. Definir esquema físico de visit_events, transações atômicas e integridade de estado/evento.
2. Definir política de idempotência, ordenação e sincronização offline dos eventos.
3. Especificar jornada por empresa e confirmação de início fora da jornada.
4. Definir equivalência ou distinção entre logout voluntário, expiração de token e revogação administrativa.
5. Definir taxonomia de motivos de impedimento, interrupção e cancelamento.
6. Definir frequência, idade máxima, retenção e acesso ao histórico GPS.
7. Desenvolver o conteúdo normativo das camadas da Base de Conhecimento e os contratos técnicos.
8. Validar o modelo lógico contra o Supabase atual antes de qualquer DDL.

#### Segurança e limites

Atualização exclusivamente documental. Nenhum código, SQL, DDL, DML, migration, policy, função, trigger, dado, configuração ou infraestrutura Supabase foi alterado. O modelo lógico permanece proposta técnica, não aprovada como modelo físico. Não foram executados testes funcionais, de GPS, concorrência, autenticação, RLS ou sincronização offline.


### Sessão — 2026-11-08 — aprovação da especificação técnica de visit_events

**Estado:** especificação técnica aprovada; implementação física não autorizada.

#### Aprovações e definições confirmadas

- A especificação de `visit_events` foi aprovada, condicionada à correção prévia de tenancy, integridade multiempresa e RLS.
- `in_displacement`, `in_progress` e `paused` ocupam a disponibilidade do técnico; a exclusividade deve ser global por identidade e garantida no backend/banco.
- A sincronização offline será validada no servidor, preservando `occurred_at` e `recorded_at`; sequência e idempotência serão controladas pelo servidor.
- Ao receber uma nova visita, antes de disponibilizá-la ao técnico, o dispositivo comparará seu relógio com o horário oficial do ERP e avisará se houver divergência. A divergência isolada não bloqueia a visita.
- `visit_events` conterá apenas eventos aceitos. Comandos rejeitados/conflitantes terão trilha de auditoria separada.
- Revisão de conflitos e eventos antigos será restrita a gestores e atendentes/usuários expressamente autorizados.

#### Recomendação ainda pendente

Foi recomendada janela configurável inicial de 24 horas para reconciliação automática de eventos offline, desde que os demais critérios de consistência sejam satisfeitos. Eventos mais antigos seriam encaminhados à revisão autorizada. O responsável aprovou o prazo configurável inicial de 24 horas para reconciliação automática. Eventos mais antigos seguem para revisão por gestor ou atendente/usuário expressamente autorizado.

#### Pré-condições e limites

A implementação depende de corrigir e validar tenancy, FKs compostas, RLS e autorização multiempresa, além das inconsistências de schema/função de visita já identificadas. Nenhuma alteração de DDL, migration, policy, função, trigger ou dado foi executada nesta sessão.


### Complemento da sessão — aprovação da janela de reconciliação offline

Em 2026-11-08, o responsável aprovou a janela configurável inicial de 24 horas para reconciliação automática de eventos offline. A idade não substitui as validações de sequência, estado atual, identidade/origem, autorização, idempotência e consistência temporal. Eventos com mais de 24 horas não são reconciliados automaticamente e devem ser encaminhados à revisão autorizada. Nenhuma alteração no banco foi autorizada ou executada.


### Sessão — 2026-11-08 — aprovação da fundação física multiempresa do MVP

**Etapa:** fundação multiempresa, integridade e autorização  
**Estado:** especificação física aprovada; preparação de migrations é a próxima atividade; execução no Supabase não autorizada.

#### Aprovação recebida

O responsável aprovou explicitamente a especificação `documentacao/ESPECIFICACAO_FISICA_FUNDACAO_MULTIEMPRESA_MVP.md` v0.1.0. A aprovação valida a direção física e os contratos descritos, incluindo:
- `company_identifier NOT NULL` em todas as tabelas tenant-scoped, inclusive tabelas filhas e associações;
- exceções para identidade global (`users`), raiz do tenant (`companies`), identidade do provedor (`auth.users`) e catálogos/infraestrutura genuinamente globais;
- FKs compostas para impedir relacionamentos entre empresas;
- RLS com negação por padrão, grants mínimos e backend/API como barreiras complementares;
- migração faseada, backfill baseado em evidência e testes negativos entre tenants.

A aprovação foi registrada na especificação como versão 0.2.0.

#### Limites da aprovação

- Não autoriza DDL, DML, migrations, grants, policies, funções, triggers, alterações de configuração ou dados no Supabase.
- Itens expressamente pendentes na especificação continuam pendentes e não foram implicitamente aprovados.
- Dados legados sem tenant ou relações ambíguas não podem receber backfill presumido.
- A decisão sobre unicidade de CPF/CNPJ por empresa versus global permanece sujeita à confirmação registrada no documento.

#### Próxima atividade

Preparar o pacote de migrations/DDL detalhado, por fases e em ordem executável, com plano de backfill, validações pré/pós, testes automatizados de isolamento e autorização, estratégia de rollback e identificação dos pontos que ainda exigem decisão. Apresentar o pacote para autorização explícita separada antes de qualquer execução no Supabase.

#### Artefatos alterados

| Arquivo | Alteração | Referência |
|---|---|---|
| `documentacao/ESPECIFICACAO_FISICA_FUNDACAO_MULTIEMPRESA_MVP.md` | Estado atualizado para APROVADA; limite de autorização de DDL explicitado | Commit `160cd5a5e1a8edebb71841654e8ba8241b7e4b53` |
| `documentacao/08_CONTINUIDADE_E_TRANSFERENCIA.md` | Registro desta sessão e próxima atividade | Commit desta atualização |

#### Segurança e limites

Nenhuma conexão de escrita ao Supabase foi usada e nenhuma alteração de banco foi executada. A aprovação é documental e arquitetural, não autorização operacional.


### Complemento da sessão — decisão sobre unicidade de CPF/CNPJ

**Data:** 2026-11-08  
**Estado:** decisão confirmada e registrada na especificação física; implementação ainda não autorizada.

O responsável confirmou expressamente que:
- um mesmo CPF/CNPJ poderá existir em empresas diferentes;
- dentro de uma mesma empresa, o CPF/CNPJ não poderá se repetir.

A regra lógica é unicidade por empresa, equivalente a uma constraint/índice único sobre `(company_identifier, cpf_cnpj)`, após definir canonicalização para impedir duplicidade por diferenças de pontuação, espaços ou formatação. A migration deverá tratar explicitamente valores nulos/vazios e registros legados, sem normalização destrutiva ou correção presumida.

#### Artefatos atualizados

- `documentacao/ESPECIFICACAO_FISICA_FUNDACAO_MULTIEMPRESA_MVP.md`: v0.3.0.
- Este documento: v1.6.0.

#### Próxima atividade

Continuar a preparação do pacote de migrations/DDL. Ainda dependem de definição ou validação: canonicalização de CPF/CNPJ; política para nulos/vazios; reconciliação do backfill multiempresa; mecanismo confiável de contexto transacional no backend; identificação do repositório e stack da aplicação.

#### Limites

Esta confirmação não autoriza executar SQL, DDL, migration ou DML no Supabase. Nenhuma alteração de banco foi realizada nesta sessão.


### Sessão — 2026-11-08 — preparação do pacote de migração multiempresa (Parte A)

**Etapa:** fundação multiempresa, estrutura e integridade  
**Estado:** plano técnico v0.1.0 criado para aprovação; nenhuma alteração no Supabase executada.

#### Deliberação recebida

O responsável concordou em separar:
- **Parte A:** estrutura física, backfill, constraints, FKs compostas, unicidade tenant-scoped, correções de defaults e integridade;
- **Parte B:** contexto autenticado da empresa, funções de autorização, grants e policies RLS, bloqueada até definição do contrato confiável de contexto transacional no backend.

#### Artefato criado

- `documentacao/relatorios/PLANO_MIGRACAO_FUNDACAO_MULTIEMPRESA_PARTE_A_MVP.md`
- Versão 0.1.0 — proposta técnica para aprovação.
- Inclui auditoria somente leitura atual, classificação das tabelas, sequência de migração, mapa de FKs compostas, canonicalização CPF/CNPJ, SQL de referência, gates, bloqueios, testes e rollback.

#### Achados novos que condicionam a migration

- 38 tabelas públicas, RLS habilitado em 38, 23 policies, 103 FKs e nenhuma FK composta por tenant.
- 15 tabelas já possuem `company_identifier`; 6 dessas colunas ainda aceitam NULL.
- 25 das 26 visitas têm cliente/endereço coerentes e podem receber tenant por relação verificável; uma visita não possui qualquer evidência de tenant.
- Todas as 26 visitas estão sem ordem de serviço; `work_orders` está vazia. Nenhuma OS será criada por suposição.
- As duas associações de `company_user_roles` divergem do campo legado `company_users.role_identifier`; ambas as estruturas serão preservadas até decisão.
- A constraint global atual de CPF/CNPJ deverá ser substituída por unicidade por empresa com canonicalização, conforme decisão já aprovada.
- `set_visit_address_snapshot()` referencia colunas inexistentes e depende de revisão do trigger antes da correção.

#### Limites e próxima atividade

A proposta não é migration executável aprovada e não autoriza DDL/DML. A execução depende da aprovação do documento e da resolução dos bloqueios listados nele, além de backup restaurável e definição do repositório oficial de migrations. A Parte B permanece separada.

Nenhuma alteração foi realizada no Supabase nesta sessão.


### Sessão — 2026-11-08 — revisão das deliberações da Parte A e desenho do catálogo de permissões

**Etapa:** fundação multiempresa, integridade e autorização  
**Estado:** plano Parte A v0.2.0 atualizado; proposta física do catálogo de permissões v0.1.0 criada para aprovação. Nenhuma alteração no Supabase executada.

#### Deliberações recebidas

1. O responsável confirmou que **todas as 26 visitas existentes pertencem à Winservice**. O backfill de `visits.company_identifier` deverá preencher todas as linhas com o identificador da empresa Winservice (`a4de8fcf-95fc-432e-88ef-96eaa45a91fc`). A visita sem cliente/endereço/OS permanece sem esses vínculos; não criar registros artificiais.
2. **Uma visita não precisa estar vinculada a uma OS.** `work_order_identifier` permanecerá nullable. Se preenchido, a FK composta deverá garantir que a OS pertence à mesma empresa.
3. A ADR-0017 determina que o vínculo usuário–empresa possui contexto de autorização próprio, incluindo papéis. Entretanto, não foi encontrada decisão aprovada que formalize `company_user_roles` como fonte única e determine a remoção de `company_users.role_identifier`. A auditoria identificou duas associações em `company_user_roles` divergentes do campo legado. Nenhum campo ou associação será removido/selecionado automaticamente. A remoção exige deliberação documental, reconciliação dos dados e auditoria de dependências.
4. Foi elaborado o desenho físico proposto para a ADR-0011: versões globais imutáveis, catálogo efetivo materializado por empresa, cópia na adoção, personalizações preservadas, adoção voluntária, histórico e restauração. O desenho está sujeito à aprovação; não altera a ADR-0011 nem autoriza implementação.

#### Inspeção somente leitura do Supabase

Contagens observadas:
- `permissions`: 1 registro;
- `role_permissions`: 0 registros;
- `user_permissions`: 0 registros;
- `roles`: 3 registros;
- `company_users`: 1 vínculo;
- `company_user_roles`: 2 associações.

A inspeção de colunas confirmou `company_users.role_identifier` nullable e `company_user_roles.role_identifier` obrigatório. Não foram executadas alterações no banco.

#### Artefatos atualizados/criados

- `documentacao/relatorios/PLANO_MIGRACAO_FUNDACAO_MULTIEMPRESA_PARTE_A_MVP.md` — v0.2.0; corrigido backfill de visitas, OS opcional e tratamento da divergência de papéis.
- `documentacao/relatorios/PROPOSTA_MODELO_FISICO_CATALOGO_PERMISSOES_MVP.md` — v0.1.0; proposta física para aprovação.
- `documentacao/06_STATUS_E_RELATORIOS.md` — v1.8.0.

#### Pendências e próxima atividade

1. Responsável revisar/aprovar ou ajustar a proposta física do catálogo de permissões.
2. Deliberar formalmente se `company_user_roles` será a fonte única de papéis e se `company_users.role_identifier` será removida após reconciliação e auditoria de dependências.
3. Prosseguir com a definição física de catálogos globais de tarefa/requisito.
4. Inspecionar trigger/função de snapshot de endereço.
5. Definir repositório oficial de migrations e preparar SQL final numerado com rollback.
6. Manter a Parte B bloqueada até definir contrato confiável de contexto transacional no backend.

**Limites:** nenhum DDL, DML, migration, policy, grant, função, trigger ou dado foi alterado no Supabase. A criação/atualização dos documentos é documental e não autoriza execução.


### Complemento da sessão — aprovação da ADR-0018: fonte única de papéis (2026-11-08)

**Estado:** decisão aprovada e registrada; implementação física pendente.

O responsável confirmou que o ERP deverá ignorar integralmente `company_users.role_identifier` e o valor armazenado nessa coluna. Não haverá reconciliação nem migração do papel legado. A tabela `company_user_roles` será a única fonte oficial de atribuição de papéis por vínculo usuário–empresa.

A ADR-0018 foi criada e aprovada. A decisão complementa a ADR-0017 e estabelece:
- preservar e validar as associações existentes em `company_user_roles`;
- garantir tenant por `company_identifier` e FKs compostas para vínculo e papel;
- não usar o campo legado como fallback, fonte de autorização ou dado de migração;
- remover a coluna do schema físico após tratar dependências.

#### Auditoria somente leitura das dependências

A inspeção do PostgreSQL 17.6 identificou:
- constraint `company_users_role_identifier_fkey` sobre a coluna;
- referência a `company_users.role_identifier` no corpo da função `public.can_view_expense(uuid)`.

A função `can_view_expense` está classificada no modelo de autorização como obsoleta e fora do escopo atual do MVP. Seu destino deverá ser resolvido antes da remoção da coluna; não deixar função inválida nem assumir que sua referência pode permanecer. A consulta de dependências não executou alterações.

#### Artefatos atualizados/criados

- `documentacao/decisoes/ADR-0018-company-user-roles-fonte-unica-de-papeis.md` — criada e aprovada.
- `documentacao/02_DECISOES_ARQUITETURA.md` — v0.7.0.
- `documentacao/MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md` — v0.3.2.
- `documentacao/relatorios/PLANO_MIGRACAO_FUNDACAO_MULTIEMPRESA_PARTE_A_MVP.md` — v0.3.0.
- `documentacao/06_STATUS_E_RELATORIOS.md` — v1.9.0.

#### Próxima atividade

Atualizar o pacote de migrations da Parte A para implementar a ADR-0018: preservar as associações da tabela de relacionamento, adicionar tenant e FKs compostas, remover a constraint e dependências do campo legado e, por fim, remover `company_users.role_identifier`. A execução continuará condicionada à revisão do SQL final e autorização operacional específica. A proposta física do catálogo de permissões permanece aguardando aprovação.

**Limites:** nenhum DDL, DML, migration, policy, grant, função, trigger ou dado foi alterado no Supabase.


### Sessão — 2026-11-08 — aprovação do modelo físico do catálogo de permissões

**Estado:** modelo físico aprovado; implementação física não autorizada.

O responsável aprovou a proposta `documentacao/relatorios/PROPOSTA_MODELO_FISICO_CATALOGO_PERMISSOES_MVP.md`, atualizada para v0.2.0. A aprovação valida a separação entre catálogo global versionado e catálogo efetivo materializado por empresa, adoção voluntária, preservação de customizações, histórico/restauração e referências de permissões efetivas em `role_permissions` e `user_permissions`.

A aprovação não resolve as decisões ainda abertas na proposta e não autoriza DDL, DML, migrations, policies, grants ou alterações no Supabase.

**Próxima atividade:** preparar a especificação executável e o pacote de migração, começando pelo inventário de dependências das tabelas atuais `permissions`, `role_permissions` e `user_permissions`, validação do registro legado, definição dos códigos estáveis e identificação do repositório/baseline de migrations. Apresentar o pacote para revisão e autorização operacional específica antes de qualquer execução.

**Limites:** nenhuma alteração no Supabase foi realizada nesta atualização documental.


### Complemento — auditoria somente leitura das estruturas de permissões (2026-11-08)

Após a aprovação do modelo físico, foi inspecionado o schema atual do Supabase. Foram encontrados 1 registro em `permissions` (`customer/view`, ainda sem código estável), nenhum registro em `role_permissions` ou `user_permissions`, FKs individuais sem garantia composta de tenant, `user_permissions.allow` nullable, RLS habilitado sem policies retornadas para as três tabelas e nenhum trigger não interno. O único registro legado pertence à Winservice e permaneceu intacto. Detalhes e limitações estão na proposta física v0.3.0.

Nenhuma alteração de dados ou schema foi executada. A próxima etapa é fechar os pontos técnicos pendentes e preparar a especificação executável/migration draft, sem executar no Supabase.


### Complemento da sessão — aprovação da convenção de códigos de permissão (2026-11-08)

**Estado:** convenção aprovada e registrada; migration não autorizada.

O responsável aprovou a convenção semântica `entidade.ação` para os códigos estáveis (`permission_code`), em minúsculas e independente dos nomes físicos das tabelas. Exemplos ilustrativos: `customer.view`, `customer.create`, `customer.update`, `visit.view`, `visit.create` e `visit.cancel`.

O registro legado observado (`entity=customer`, `field=NULL`, `action=view`) deverá ser mapeado para `customer.view`, condicionado à validação no plano de migração. A aprovação da convenção não aprova a matriz completa de permissões nem cria automaticamente todos os códigos exemplificados. Mudanças semânticas incompatíveis exigem novo código; mudanças de descrição ou de nomes físicos não devem alterar códigos estáveis.

#### Artefatos atualizados

- `documentacao/relatorios/PROPOSTA_MODELO_FISICO_CATALOGO_PERMISSOES_MVP.md` — v0.4.0, seção 9.1 e histórico de alterações.
- `documentacao/CONTEXTO_ATUAL_ERP.md` — decisão e referência operacional atualizadas.

#### Próxima atividade

Continuar a preparação da especificação executável e do pacote de migration: identificar o repositório/baseline oficial de migrations, validar o mapeamento `customer.view`, completar o inventário de dependências em código e workflows, resolver as decisões técnicas ainda pendentes e apresentar o pacote para revisão e autorização operacional específica.

#### Limites

Aprovação restrita à convenção de nomenclatura. Nenhum DDL, DML, migration, policy, grant, função, trigger ou dado foi alterado no Supabase.


### Complemento da sessão — bloqueio na identificação do repositório de implementação (2026-11-08)

**Estado:** retomada documental realizada; preparação executável bloqueada por falta de acesso identificado ao repositório de implementação.

#### Consulta realizada
- HEAD documental consultado: `ce96eacb272f8c6b7d323b6175760947ae309f99`.
- Repositório GitHub acessível listado pela integração: `lucasfaborges94/erp-winservice-dev-docs`.
- Buscas por repositórios do proprietário com os termos `erp`, `winservice` e `backend` não localizaram outro repositório de implementação.
- ADR-0011, ADR-0017, ADR-0018, proposta física do catálogo de permissões v0.4.0, plano Parte A, contexto, status e stack foram consultados.

#### Resultado e limitação
Não foi possível auditar dependências em código, identificar o sistema de migrations da aplicação ou confirmar o baseline oficial. A busca realizada cobre somente os repositórios visíveis à integração e os termos utilizados; não prova inexistência de código em outra conta, organização ou ambiente.

#### Próxima atividade
Solicitar ao responsável a URL/nome do repositório de implementação e confirmar que a integração possui acesso. Após isso, inspecionar estrutura, branches, migrations, workflows e referências às tabelas/colunas envolvidas; confrontar com o Supabase em modo somente leitura e então fechar a especificação executável. Não executar DDL/DML nem migrations sem autorização operacional específica.

#### Limites
Nenhum código, arquivo do Supabase, schema, dado, migration, policy, grant, função ou trigger foi alterado nesta retomada. Atualizações feitas somente na documentação do repositório oficial.


### Esclarecimento do responsável — não existe repositório separado de aplicação (2026-11-08)

O responsável esclareceu que não existe outro repositório além de `lucasfaborges94/erp-winservice-dev-docs`. A solicitação anterior por URL de repositório de implementação não se aplica; não há código-fonte separado a fornecer ou auditar.

**Próximo passo revisado:** inspecionar em modo somente leitura o histórico de migrations disponível no Supabase e o schema efetivo, incluindo funções, triggers, policies, constraints e dependências relevantes. Como não há baseline Git de migrations em repositório de aplicação, após a auditoria será necessário propor o local e o fluxo de versionamento das migrations futuras. Nenhuma alteração no Supabase está autorizada por este esclarecimento.



### Sessão — 2026-11-08 — aprovação de repositório separado para código-fonte

**Estado:** nome, proprietário, visibilidade e branch principal aprovados; repositório ainda não criado/verificado.

O responsável aprovou a criação do repositório `lucasfaborges94/erp-winservice-app`, privado, com branch principal `main`. O repositório documental `lucasfaborges94/erp-winservice-dev-docs` permanece separado e continua sendo a fonte oficial de requisitos, decisões, ADRs e continuidade.

A integração GitHub disponível nesta sessão foi inspecionada e não expõe ação de criação de repositório. Por isso, nenhuma criação foi executada e não se declara que o repositório exista. O responsável deverá criá-lo pela interface do GitHub e compartilhar/confirmar a URL; em seguida, verificar metadados, branch, permissões e acesso pela integração.

**Próximos passos após a criação:**
1. Confirmar que o repositório está privado e que a branch padrão é `main`.
2. Definir a estrutura inicial do monorepo/repositório conforme a arquitetura e stack efetivamente aprovadas, sem inventar módulos ou frameworks.
3. Estabelecer convenção e baseline de migrations, reconciliando o estado real do Supabase antes de criar baseline.
4. Configurar testes, validações automatizadas e CI/CD com segredos gerenciados fora do Git.
5. Registrar o endereço do repositório de aplicação no contexto, stack e documentação de governança.

**Limites:** nenhuma alteração foi feita no Supabase; nenhum repositório foi criado nesta sessão; nenhum código, migration ou pipeline foi adicionado.


### Complemento da sessão — tentativa de verificação do repositório de aplicação (2026-11-08)

O responsável compartilhou a URL `https://github.com/lucasfaborges94/erp-winservice-app` para confirmação da criação. A chamada de leitura de metadados do repositório pela integração retornou HTTP 404. A listagem dos repositórios acessíveis da conta `lucasfaborges94` retornou somente `erp-winservice-dev-docs`.

**Conclusão limitada:** `erp-winservice-app` não está visível/acessível à integração GitHub usada nesta sessão. O resultado não prova que o repositório inexista: pode estar privado e sem acesso concedido à integração. Não foi possível confirmar proprietário efetivo, visibilidade, branch padrão, permissões ou existência. Nenhuma alteração foi feita no repositório de aplicação ou no Supabase.

**Próximo passo:** conceder à integração GitHub acesso ao repositório privado (se ele já foi criado) e solicitar nova verificação. Após acesso confirmado, validar que é privado e usa `main`, então definir a estrutura inicial e o fluxo de migrations conforme documentação aprovada.


### Confirmação de acesso ao repositório de aplicação — 2026-11-08

**Estado:** acesso confirmado; repositório privado e vazio.

Após o responsável conectar a integração GitHub ao novo repositório, a consulta direta de metadados de `lucasfaborges94/erp-winservice-app` retornou sucesso. Foram confirmados: proprietário `lucasfaborges94`, visibilidade `private`, branch padrão `main`, repositório não arquivado e permissões `admin`, `maintain`, `pull`, `push` e `triage` para a integração. O tamanho informado pelo GitHub é zero.

A consulta de conteúdo em `main` retornou HTTP 404 com a mensagem `This repository is empty`; a listagem de commits retornou HTTP 409 (`Git Repository is empty`). Assim, o repositório existe e está acessível, mas ainda não possui commit, arquivo ou estrutura inicial. Não foi possível inspecionar código, workflows ou migrations porque não existem conteúdos.

**Próximas atividades:**
1. Definir a estrutura inicial do repositório estritamente a partir das decisões, stack e escopo aprovados.
2. Definir e documentar a convenção de migrations e reconciliar o estado real do Supabase antes de criar qualquer baseline.
3. Só depois estabelecer testes, validações automatizadas e CI/CD, sem incluir segredos no Git.

**Limites:** nesta verificação não foi criado arquivo nem commit no repositório de aplicação. Nenhuma alteração foi feita no Supabase, em migrations, policies, funções, triggers ou infraestrutura. A confirmação de acesso não representa autorização para DDL/DML ou implantação.


### Sessão — 2026-11-08 — delegação operacional do Supabase e inventário estrutural inicial

**Deliberação do responsável:** o agente atual será o operador exclusivo para executar alterações no projeto Supabase do ERP. A delegação não revoga os gates de revisão, preflight, backup restaurável e autorização operacional específica definidos nos documentos e ADRs.

**Trabalho realizado em modo somente leitura:**
- Confirmado projeto Supabase `voaovwphncwbrwtcqnsc`, Winservice, região `sa-east-1`, PostgreSQL `17.6.1.141`, estado saudável.
- Capturado inventário estrutural do schema `public`: 38 tabelas, 461 colunas, 206 constraints, 110 índices, 11 funções, 18 triggers, 23 policies, 34 sequences, 8 extensões instaladas e 1.064 concessões de privilégios.
- Confirmadas contagens: 26 visitas, 103 clientes, 99 endereços, 1 empresa, 1 `company_users`, 2 `company_user_roles`, 3 papéis, 1 permissão, 0 `role_permissions` e 0 `user_permissions`.
- Confirmada a permanência física de `company_users.role_identifier`, sua FK e `public.can_view_expense(uuid)`, conforme dependências registradas na ADR-0018.
- Listagem de migrations retornou zero e `supabase_migrations.schema_migrations` não existe.
- Executados Supabase Advisors de segurança e performance; relatório integral salvo no repositório de aplicação.

**Artefatos no repositório de aplicação:**
- `supabase/README.md`;
- `supabase/baseline/2026-11-08-schema-inventory.json` — inventário estrutural sem linhas de dados ou segredos; não é dump restaurável;
- `supabase/baseline/2026-11-08-advisors.json` — achados dos advisors.

**Limitação:** a integração Supabase disponível não fornece Supabase CLI/`pg_dump`; não foi possível produzir nesta etapa um dump SQL completo e restaurável nem baseline executável de migrations. Não foi criado arquivo de migration.

**Advisors — achados a triar:** 26 tabelas RLS sem policy; 7 funções com search_path mutável; 2 funções SECURITY DEFINER executáveis por anon/authenticated; proteção contra senhas vazadas desabilitada; 89 FKs sem índice de cobertura; 2 pares de índices duplicados; 12 índices sem uso observado; 1 policy com initplan Auth.

**Limites:** nenhuma alteração foi feita no Supabase. Nenhum dado de linha, credencial, token ou segredo foi versionado. Nenhuma migration, policy, função, grant, índice ou configuração foi alterada.

**Próximo passo:** estabelecer um dump SQL restaurável/baseline com CLI ou ferramenta equivalente, revisar os achados de segurança e concluir a especificação executável da fundação multiempresa e do catálogo de permissões. Consultar o banco real imediatamente antes de qualquer alteração e verificar o resultado depois.


### Complemento da sessão — triagem de segurança Supabase (2026-11-08)

**Estado:** inspeção somente leitura concluída; correções não iniciadas.

**Fontes consultadas:** HEAD documental `f9567597c26d7c6f4d572d6d08edb92d563b41ce`; ADR-0017; ADR-0018; modelo de autorização; auditoria de autorização de 2026-11-08; plano Parte A; proposta do catálogo de permissões; Supabase Security Advisors e catálogos PostgreSQL do projeto `voaovwphncwbrwtcqnsc`.

**Achados:**
1. `public.authorize_integration_sync_event(...)` é SECURITY DEFINER, proprietária postgres, executável por anon/authenticated. Seu corpo valida integração existente/ativa e provider Auvo, mas não mostra validação de assinatura, segredo ou identidade do chamador antes de inserir o payload em `integration_sync_queue`. Risco alto a validar; não foi chamada nem explorada.
2. Policies permissivas com `USING (true)`/ `WITH CHECK (true)` foram observadas em tabelas operacionais, incluindo customers, addresses, customer_contacts e visits, sem filtro de vínculo/empresa. A policy ALL de companies também usa predicado true. Isso diverge da ADR-0017. Não foram feitas leituras de linhas via Data API nem testes com sessões reais.
3. `can_view_expense` permanece SECURITY DEFINER e executável por anon/authenticated, mas está fora do MVP e reservada ao futuro Financeiro conforme ADR-0018; não reutilizar como mecanismo geral.
4. Advisor listou 26 tabelas RLS sem policy; classificar individualmente, pois ausência de policy normalmente nega acesso a papéis sujeitos a RLS.
5. Seis funções apresentam search_path mutável: set_updated_fields, set_created_by, ensure_primary_address, set_created_fields, set_visit_address_snapshot e update_updated_at, além de prevent_changes (sete no total conforme Advisor). Correção exige revisão individual.
6. Proteção contra senhas vazadas está desabilitada.

**Artefato:** `documentacao/auditorias/AUDITORIA_SEGURANCA_SUPABASE_2026-11-08.md`.

**Limites:** somente SELECTs de catálogo e Advisor; sem DDL/DML, chamadas RPC, exploração, leitura de dados pessoais, payloads ou segredos. Nenhuma alteração no Supabase.

**Próximas ações:** revisar o contrato de autenticação do webhook; classificar tabelas global/tenant/infra e definir matriz tabela × ator × operação; especificar contexto empresarial e policies conforme ADR-0017; manter dump restaurável e ensaio não produtivo como gates para correções.
