# Status do desenvolvimento e relatórios periódicos — ERP Winservice

**Versão:** 1.9.3  
**Data-base:** 2026-11-08

## 1. Resumo executivo

O projeto está em fase de definição arquitetural, organização de governança e consolidação da fundação de dados. Já existe implementação relevante no Supabase, mas seu estado precisa ser comparado com requisitos e auditado antes de declarar módulos concluídos.

A prioridade da primeira versão é Operação, com substituição gradual por módulos.

A documentação passou a definir explicitamente o repositório GitHub como memória documental oficial e persistente, com procedimento obrigatório de recuperação e manutenção do contexto entre sessões. Históricos de conversa e memórias internas de IA são auxiliares. O estado efetivamente implementado deve ser verificado no código, nas migrações e nos ambientes correspondentes.

## 2. Estado conhecido

| Frente | Estado conhecido | Próxima verificação |
|---|---|---|
| Project Charter | Decisões centrais registradas; pacote precisa de revisão formal | Aprovar baseline e completar lacunas |
| Arquitetura SaaS | Direção multiempresa aprovada | Validar tenancy, isolamento e provisionamento |
| Supabase | Auditoria técnica somente leitura realizada em 2026-11-07; relatório versionado | Priorizar revisão de RLS, grants, função de ingestão, integridade multiempresa e trigger de visitas |
| Permissões | Catálogo global versionado e personalizações por empresa definidos | Auditar implementação e políticas |
| Isa | Limites de autonomia definidos | Testar autorização no backend/API |
| Operação | Modelo funcional v0.6.0 e modelo lógico v0.5.0; Máquina A única aprovada; Máquina B eliminada; histórico unificado em visit_events; tarefas sem estados; Base de Conhecimento conceitualmente aprovada; implementação parcial no Supabase ainda não reavaliada | Implementar especificação aprovada de visit_events somente após corrigir tenancy/RLS; definir janela de idade offline e validar jornada/GPS |
| Atendimento | Chatwoot transitório | Mapear dependências e plano de substituição |
| WeWeb | Direção aprovada | Definir ambiente, autenticação e contratos de API |
| FlutterFlow | Direção aprovada | Especificar sync offline-first e GPS background |
| Jurídico | Módulo amplo aprovado; primeira funcionalidade documental | Elaborar arquitetura funcional e requisitos |
| Módulos do ERP | Regra geral de ativação em dois níveis e drenagem de execuções já aceitas e tratamento de falhas durante a drenagem definidos na proposta v0.1.6; primeiro usuário humano como superadmin; agentes de IA proibidos de ocupar superadmin | Especificar enforcement transversal da admissão, drenagem das filas existentes e recuperação do superadmin |
| ADRs | Índice e fichas individuais em consolidação documental | Verificar integridade de todas as fichas e referências |
| Continuidade | Protocolo universal e modelo de transferência registrados | Aplicar ao final de cada sessão relevante |

## 3. Relatório de auditoria Supabase — 2026-11-07\n\nFoi realizada auditoria técnica somente leitura no projeto Supabase Winservice (`voaovwphncwbrwtcqnsc`), schema `public`. O relatório completo está em [Auditoria técnica do Supabase — 2026-11-07](relatorios/2026-11-07-auditoria-tecnica-supabase.md).\n\nPrincipais achados registrados:\n- policies RLS sem restrição por empresa em entidades relevantes;\n- privilégios de tabela amplos para `anon` e `authenticated`, cujo impacto efetivo depende de RLS e do papel;\n- função `authorize_integration_sync_event` como `SECURITY DEFINER`, executável por papéis públicos e sem validação de autenticidade identificada no corpo inspecionado;\n- lacunas de integridade referencial entre entidades multiempresa;\n- incompatibilidade entre a função `set_visit_address_snapshot` e as colunas existentes em `addresses`;\n- alertas de search_path, índices duplicados e FKs sem índice de cobertura.\n\nNenhuma alteração foi realizada no banco. O relatório separa achados confirmados, riscos e itens não verificados. As recomendações ainda não foram executadas.\n\n## 4. Artefatos técnicos localizados

- inventario_supabase.json — criado em 2026-11-06.
- auditoria_estrutural_supabase.json — criado em 2026-11-06.

Esses artefatos devem acompanhar a base do Projeto como evidência técnica. Antes de gerar novas migrações, confirmar se representam o estado atual do ambiente.

## 5. Registro periódico — 2026-11-07

### Trabalho consolidado anteriormente
- Definição do ERP como SaaS multiempresa, inicialmente para Winservice.
- Consolidação das decisões de stack e arquitetura.
- Registro de disponibilidade-alvo de 99,5%, RPO 1h, RTO 4h e retenção de backups diários de 30 dias.
- Definição de Operação como prioridade da primeira versão e substituição gradual por módulos.
- Definição do catálogo de permissões global versionado com adesão voluntária e preservação de personalizações por empresa.
- Definição dos limites de autonomia da Isa.
- Definição do módulo Jurídico amplo, com Gestão de Documentos e Instrumentos Jurídicos como primeira funcionalidade.
- Definição de documentação oficial e rascunho separados.

### Entregáveis desta iniciativa de continuidade
- Protocolo de fonte da verdade independente de fornecedor, conta ou profissional.
- Procedimento de encerramento e retomada de sessões.
- Modelo de registro de sessão com commits, artefatos, testes, pendências e próximos passos.
- Integração dos procedimentos ao leia-me e à governança documental.

**Evidência Git:** a atualização documental foi publicada na branch main. O último commit desta iniciativa é cd5ac9d39d6d3600e10977387bcb4e24768fe389. Os commits individuais dos cinco arquivos iniciais estão relacionados em documentacao/08_CONTINUIDADE_E_TRANSFERENCIA.md.

### Pendências prioritárias
1. Conferir links relativos e presença de todos os 14 ADRs individuais.
2. Auditar consistência entre Charter, índice de decisões, fichas ADR, roadmap, escopo e governança.
3. Auditar o inventário e a auditoria Supabase de 2026-11-06.
4. Produzir matriz de conformidade do banco: requisito × objeto existente × situação × ação.
5. Consolidar mapa de processos operacionais e máquina de estados.
6. Fechar escopo da primeira entrega operacional.
7. Executar auditoria de consistência documental e registrar os achados.

### Riscos e observações
- A existência de tabelas ou políticas no banco não comprova, isoladamente, que o requisito esteja correto ou seguro.
- RLS habilitada não significa que as políticas estejam adequadas; testar cenários positivos e negativos.
- A documentação atual contém decisões e propostas; itens não confirmados estão marcados como pendentes.
- Não iniciar mudanças estruturais com base apenas em nomes de tabelas ou snippets: consultar inventário e definições completas.

## 6. Modelo funcional operacional do MVP — 2026-11-08

> **Registro histórico:** esta seção descreve o primeiro baseline funcional (v0.1.0) e as constatações daquele momento. As regras vigentes de estados, execução, jornada e reabertura foram consolidadas e substituem os pontos divergentes no registro mais recente da seção 14 e no modelo funcional v0.6.0.

Foi criado `documentacao/MODELO_FUNCIONAL_OPERACIONAL_MVP.md`, versão 0.1.0, consolidando os requisitos funcionais discutidos para clientes, equipamentos, OS, visitas, tarefas, relatos, sessões de execução, GPS, autoria, auditoria e continuidade do atendimento.

**Pontos funcionais consolidados:**
- A OS preserva o histórico completo e pode conter várias visitas.
- Cada visita possui suas próprias tarefas; uma nova data gera nova visita, normalmente na mesma OS.
- Tarefa não possui status de execução/conclusão; períodos de trabalho são registrados em sessões independentes.
- Relatos podem pertencer à visita ou à tarefa; visita exige pelo menos um relato para encerramento.
- Equipamento em tarefa é opcional e distingue equipamento identificado, não identificado e ausência de equipamento.
- Check-in automático inicial: 50 m e 5 minutos, com parâmetros configuráveis; inicia a execução da visita; tarefas são registradas dentro da visita e não possuem início individual.
- Afastamento pausa a sessão; retorno permite retomada automática.
- Somente sessões pausadas podem ser finalizadas pelo limite da jornada; sessão ainda em execução não deve ser finalizada automaticamente.
- Reabertura de visita finalizada é exclusiva do técnico dentro de 200 m; gestor não pode reabrir.
- Edição de visita finalizada por técnico/atendente é configurável pelo gestor; gestor pode editar.
- Autoria e histórico de alterações devem preservar versões anteriores.

**Confronto com schema consultado em tempo real (somente leitura):**
- Estruturas existentes: `customers`, `customer_contacts`, `phonenumbers`, `addresses`, `equipments`, `work_orders`, `visits`, `work_order_equipment`, `task_types`, `visit_types`, `visit_requirements`, `visit_type_requirement`, `quotations`, `approval_events` e `documents`.
- Não foram encontradas tabelas dedicadas a tarefas por visita, relatos, sessões de execução, eventos GPS históricos ou auditoria append-only.
- `task_types` existe como catálogo básico, mas não apresenta configuração de campos/obrigatoriedades por tipo.
- `work_order_equipment` relaciona OS e equipamento; não substitui uma tarefa vinculada a uma visita.
- A autoria está incompleta/inconsistente em algumas entidades, e a auditoria anterior apontou riscos de RLS e integridade multiempresa.

**Estado:** documentação funcional inicial criada; modelo lógico e implementação não aprovados/concluídos. Nenhuma alteração foi realizada no Supabase.

## 7. Modelo lógico de dados e matriz de conformidade — 2026-11-08

Foi criado `documentacao/MODELO_LOGICO_DADOS_OPERACAO.md`, versão 0.1.0. O documento apresenta:
- cardinalidades entre cliente, equipamento, OS, visita, tarefa, relatos, sessões e eventos;
- proposta de entidades existentes a aproveitar e entidades novas a criar;
- regras de integridade multiempresa, autoria, histórico e exclusividade do técnico;
- matriz requisito × estrutura atual × evidência × ação proposta × prioridade;
- riscos e decisões pendentes antes de DDL.

**Principais conclusões:** `customers`, `addresses`, `equipments`, `work_orders` e `visits` fornecem uma base parcial; `task_types` é catálogo, não instância de tarefa; `work_order_equipment` não substitui tarefa; não foram encontradas estruturas dedicadas a tarefas por visita, relatos, sessões de execução, eventos GPS históricos ou auditoria append-only. Orçamentos, documentos e autoria exigem complementação.

**Estado:** proposta técnica para validação, não aprovada como modelo físico. Nenhuma alteração foi realizada no Supabase. A ordem de implementação é recomendação, não autorização para DDL.

## 8. Atualização do modelo lógico operacional — 2026-11-08

Foram consolidadas novas definições funcionais:
- catálogo de tipos de tarefa inicialmente global, com personalização independente por empresa;
- orçamento com vínculo opcional e exclusivo a OS, visita ou tarefa, ou sem vínculo;
- execução e sessões no nível da visita; tarefas não possuem sessões nem estados de execução;
- nova visita finaliza a visita anterior mediante relato do técnico;
- afastamento a partir de 200 m (configurável) pausa a execução; retorno a até 50 m (configurável) permite retomada;
- máquina de estados reutilizável pertence ao módulo Chat e não deve ser confundida com o ciclo operacional da visita.

O modelo lógico foi atualizado para v0.2.0. A estratégia física de personalização do catálogo, os detalhes de GPS/jornada e o desenho de auditoria imutável permanecem pendentes. A arquitetura append-only foi registrada como recomendação, não como decisão aprovada.

**Estado:** especificação em evolução. Nenhuma alteração no Supabase, SQL ou aplicação.

## 9. Atualização do modelo operacional — 2026-11-08 (GPS, histórico e catálogos)

Definições incorporadas: check-in inicial de 5 minutos/50 m; precisão GPS inicial de até 50 m de incerteza reportada; histórico persistente de posições; posição mais recente como última referência, sem inferência a partir de amostra obsoleta; finalização de visita por nova tarefa, deslocamento ou fim de jornada; relato textual, mudança de status e motivo de abandono/interrupção; histórico append-only aprovado na ADR-0016. A proposta de repositório global de catálogos versionados com personalização por empresa permanece pendente de aprovação.

Modelo funcional v0.4.0 e modelo lógico v0.3.0. Nenhuma alteração foi realizada no Supabase.



## 10. Atualização operacional — requisitos, evidências e aceite digital (2026-11-08)

Foram consolidadas as seguintes definições funcionais:
- Tarefas permanecem como registros vinculados à visita, sem início/status de execução/conclusão individual.
- Indicadores derivados de requisitos obrigatórios e opcionais pendentes na visita e em cada tarefa.
- Cada tentativa de finalização normal gera avaliação histórica completa dos requisitos, inclusive quando bloqueada.
- Requisitos obrigatórios não cumpridos bloqueiam conclusão normal; requisitos opcionais pendentes permitem conclusão e seguem para revisão.
- Encerramento excepcional por interrupção/abandono pode ocorrer com requisitos obrigatórios pendentes, sem equivaler à conclusão normal.
- Gestor/atendente autorizado pode aceitar ausência de requisito opcional mediante justificativa; técnico pode cumprir ou solicitar revisão.
- Evidências de foto/documento/assinatura com captura, revisão, upload em segundo plano, timeout/retries e confirmação pelo backend; idempotência obrigatória.
- Metadados de evidência incluem `created_at` (`timestamptz`) e latitude/longitude da captura quando disponíveis e autorizadas.
- Aceite digital com link para dispositivo pessoal como padrão, assinatura presencial no dispositivo do técnico como alternativa, coleta offline protegida e sincronização posterior.
- Resumo versionado: identificação do atendimento, cliente, equipamento, técnico, solicitação original, relatos, fotos incorporadas, valores discriminados e documentos como anexos. Alterações relevantes exigem novo aceite.

**Estado:** modelos funcional v0.5.0 e lógico v0.4.0 publicados como especificações em evolução. O modelo lógico continua proposta técnica, não modelo físico aprovado. Nenhuma alteração foi feita no Supabase.

### Pendências
- Timeout, retries, backoff, limites e retenção de uploads.
- Método inicial de identificação do signatário e ciclo de vida dos links.
- Matriz de autorização para dispensa justificada de requisito obrigatório em encerramento excepcional.
- Classificação detalhada de alterações relevantes para novo aceite.
- Armazenamento local protegido e limpeza após sincronização.
- Jornada, sessão ativa no fim da jornada e estados/motivos de visita.


## 11. Modelo para relatórios futuros

Cada relatório deverá conter:
- Período e data.
- Objetivos planejados.
- Trabalho realizado.
- Decisões aprovadas (IDs ADR).
- Artefatos/código/migrações alterados.
- Testes executados e resultados.
- Progresso por módulo/etapa.
- Riscos, bloqueios e desvios.
- Pendências e responsáveis.
- Próximos passos.
- Documentos oficiais atualizados.
- Commits, PRs e evidências.

## 12. Status por módulo

Usar apenas estes estados:
- NOT_STARTED
- DISCOVERY
- SPECIFICATION
- IN_DEVELOPMENT
- IN_REVIEW
- BLOCKED
- DONE
- DEPRECATED

Não marcar DONE sem critérios de aceite e evidências.

## 13. Fechamento de sessão — memória documental e preparação da retomada do MVP (2026-11-08)

Nesta sessão foi formalizado o repositório `erp-winservice-dev-docs` como memória documental oficial do ERP Winservice. O protocolo passou a exigir a recuperação do contexto no início de cada nova sessão, incluindo leitura da fonte da verdade, do protocolo, do mapa documental, do registro de continuidade e do status, com consulta proporcional dos documentos específicos da tarefa.

Foram atualizados:
- `00_FONTE_DA_VERDADE.md`: papel do repositório como memória oficial;
- `documentacao/PROTOCOLO_DE_ATUACAO_IA_GITHUB.md`: inicialização de contexto e manutenção da memória;
- `documentacao/00_LEIA-ME.md`: orientação sobre memória oficial e fontes auxiliares;
- `documentacao/08_CONTINUIDADE_E_TRANSFERENCIA.md`: registro detalhado desta sessão.

O status e a continuidade foram atualizados nesta publicação para refletir o fechamento.

**Próxima atividade indicada pelo responsável:** retomar o desenvolvimento do MVP na próxima sessão, a partir do último estado documentado da frente Operação e das pendências registradas nos modelos funcional e lógico. Antes de executar, verificar o HEAD remoto e reler os documentos pertinentes. Não presumir que a especificação lógica está aprovada como modelo físico nem que existe autorização para DDL, alterações no Supabase ou mudanças de infraestrutura.

**Limites desta sessão:** trabalho exclusivamente documental no repositório GitHub. Nenhum código da aplicação, SQL, DDL, DML, migration, policy, função, trigger, configuração, dado do Supabase ou infraestrutura foi alterado. A proteção da branch `main` e o estado de Secret Scanning permanecem sem alteração.

## 17. Aprovação do modelo físico do catálogo de permissões — 2026-11-08

O responsável aprovou o modelo físico descrito em [PROPOSTA_MODELO_FISICO_CATALOGO_PERMISSOES_MVP.md](relatorios/PROPOSTA_MODELO_FISICO_CATALOGO_PERMISSOES_MVP.md), agora v0.2.0. A aprovação valida o desenho de catálogo global versionado, cópia efetiva por empresa, adoção voluntária, preservação de customizações, histórico/restauração e associação de permissões efetivas a papéis e usuários.

**Estado:** modelo físico aprovado; implementação não iniciada/não comprovada. A aprovação não autoriza DDL, DML, migrations, RLS, grants ou alterações no Supabase. Permanecem pendentes a matriz completa de permissões, códigos estáveis, atributos customizáveis, política detalhada para permissões removidas, fluxo de adoção/restauração, implementação no backend/API e contrato confiável de contexto transacional.

**Próxima atividade:** preparar a especificação executável e o pacote de migração do catálogo após inventariar dependências de `permissions`, `role_permissions` e `user_permissions`, validar o registro legado e definir o repositório/baseline de migrations. A execução no Supabase dependerá de autorização específica posterior.

## 18. Auditoria somente leitura das estruturas de permissões — 2026-11-08

Após a aprovação do modelo físico, foi realizada nova inspeção somente leitura de `permissions`, `role_permissions` e `user_permissions` no Supabase PostgreSQL 17.6.

Achados confirmados: 1 registro em `permissions` (`customer/view`, sem código estável); zero registros em `role_permissions` e `user_permissions`; FKs individuais sem garantia composta de tenant; `user_permissions.allow` nullable; RLS habilitado, mas nenhuma policy retornada para as três tabelas; nenhum trigger não interno identificado. O único registro legado pertence à Winservice e não foi alterado. A auditoria completa e suas limitações estão registradas na proposta física v0.3.0.

**Próxima atividade:** transformar o modelo aprovado em especificação executável, resolver os pontos de modelagem ainda abertos e localizar o repositório/baseline oficial de migrations. Nenhuma alteração no Supabase foi executada.

## Histórico de alterações

- 2026-11-08 — v1.9.3: registra auditoria somente leitura das tabelas atuais de permissões após aprovação do modelo físico.
- 2026-11-08 — v1.9.2: registra a aprovação do modelo físico do catálogo de permissões; implementação e DDL continuam não autorizados.
- 2026-11-08 — v1.4.0: registra formalização da memória documental oficial, atualização do protocolo de retomada e preparação para continuar o MVP na próxima sessão.
- 2026-11-08 — v1.3.0: registra requisitos/pendências, avaliações por tentativa, evidências e uploads idempotentes, coleta de assinatura e aceite digital versionado.
- 2026-11-08 — v1.2.0: registra GPS/histórico de posições, transições de visita, aprovação do histórico append-only (ADR-0016) e proposta de catálogos globais versionados.
- 2026-11-08 — v1.1.0: registra a revisão do modelo lógico v0.2.0 e as definições funcionais de execução por visita, GPS, catálogo global editável por empresa e vínculo de orçamento.
- 2026-11-08 — v1.0.0: registra o modelo lógico proposto e a matriz de conformidade da Operação.\n- 2026-11-08 — v0.9.0: registra o modelo funcional operacional do MVP e o confronto somente leitura com o schema Supabase atual.
- 2026-11-07 — v0.8.0: registra retries limitados para falhas temporárias, estados terminais para falhas permanentes/timeouts e conclusão da drenagem por estados terminais.
- 2026-11-07 — v0.7.0: registra desativação com drenagem das execuções já aceitas e bloqueio de novas entradas até reativação nos dois níveis.
- 2026-11-07 — v0.6.0: generaliza o controle de ativação em dois níveis para todos os módulos; registra primeiro usuário humano como superadmin e proíbe agentes de IA como superadmin.
- 2026-11-07 — v0.5.0: registra responsáveis pelos controles do Módulo de IA, mínimo de um superadmin por empresa e bloqueio de execuções quando o módulo não estiver ativo.
- 2026-11-07 — v0.4.0: registra a definição conceitual do Módulo de IA com disponibilidade global pela plataforma e ativação individual por empresa.
- 2026-11-07 — v0.3.0: inclusão do relatório de auditoria técnica somente leitura do Supabase e atualização do estado conhecido.\n- 2026-11-07 — v0.2.0: registro do protocolo de fonte da verdade e da continuidade entre ferramentas e profissionais.
- 2026-11-07 — v0.1.0: baseline inicial do status do projeto.

## 14. Consolidação do ciclo operacional da visita e Base de Conhecimento — 2026-11-08

### Decisões funcionais aprovadas pelo responsável

- A visita é a única entidade com máquina de estados do ciclo operacional. A proposta de Máquina B foi eliminada.
- Não haverá estados independentes de sessão (active, paused, closed) nem entidade de sessão de execução no MVP.
- O histórico temporal e operacional será registrado em eventos append-only diretamente vinculados à visita (visit_events); duração e intervalos serão projeções calculadas a partir dos eventos.
- Tarefas permanecem sem estados e sem sessões de execução.
- Máquina A aprovada: pending, in_displacement, in_progress, paused, completed, interrupted, prevented, canceled, com transições documentadas no modelo funcional.
- Revogação de autenticação: pending permanece; in_displacement retorna a pending; in_progress e paused passam a interrupted; estados terminais permanecem.
- Estados terminais não podem ser reabertos; a regra anterior de reabertura por técnico dentro de 200 metros foi substituída. Continuidade exige nova visita.
- Fim da jornada não encerra automaticamente visita nem execução. Início/check-in fora da jornada exige confirmação do técnico.
- A arquitetura conceitual da Base de Conhecimento foi aprovada em quatro camadas: domínio, operação, contratos e guias por público. Guias devem derivar das fontes normativas, sem duplicar regras.
- A autorização de humanos e agentes permanece imposta pelo backend/API/RLS; conhecimento de uma operação não concede autorização.

### Artefatos documentais atualizados

- MODELO_FUNCIONAL_OPERACIONAL_MVP.md: v0.6.0.
- MODELO_LOGICO_DADOS_OPERACAO.md: v0.5.0 (proposta lógica; não aprovada como modelo físico).
- BASE_CONHECIMENTO_ERP.md: criado como arquitetura e governança da base compartilhada.
- 00_LEIA-ME.md: mapa documental atualizado.
- 08_CONTINUIDADE_E_TRANSFERENCIA.md: registro desta sessão.

### Estado e limites

Esta atualização é documental. Não foram alterados código da aplicação, banco Supabase, DDL, DML, migrations, policies, funções, triggers, dados ou infraestrutura. A estrutura física de visit_events, transações, idempotência, índices, projeções e retenção permanece pendente de especificação. Nenhuma Máquina B foi implementada ou aprovada anteriormente como ADR; sua eliminação consolida o modelo funcional/lógico aprovado pelo responsável.

### Pendências prioritárias

1. Especificar estrutura física e transações atômicas de visit_events.
2. Definir idempotência e ordenação de eventos, inclusive sincronização offline.
3. Definir jornada por empresa e confirmação para início fora do horário.
4. Definir se logout voluntário, expiração de token e revogação administrativa compartilham a regra de revogação aprovada.
5. Fechar motivos padronizados de prevented, interrupted e cancelamento.
6. Definir frequência, idade máxima, retenção e controles do histórico GPS.
7. Continuar especificação da Base de Conhecimento: glossário, entidades, processos, contratos e guias derivados.
8. Validar o modelo lógico e confrontá-lo novamente com o estado real do Supabase antes de qualquer DDL.


## 14. Atualização — especificação técnica de eventos e sincronização offline (2026-11-08)

O responsável aprovou a especificação técnica de `visit_events`, incluindo:
- exclusividade do técnico nos estados `in_displacement`, `in_progress` e `paused`, com enforcement transacional e restrição de integridade;
- sincronização offline validada pelo servidor, preservando `occurred_at` e `recorded_at`;
- conferência do relógio do dispositivo contra o horário oficial do ERP antes de disponibilizar uma nova visita, com aviso em caso de divergência;
- separação entre eventos canônicos aceitos e comandos rejeitados/conflitantes;
- revisão de conflitos restrita a gestores e atendentes/usuários autorizados.

A correção de tenancy, integridade multiempresa e RLS é pré-condição para implementar `visit_events`. A especificação aprovada não autoriza DDL nem alterações no banco. A janela configurável inicial de 24 horas para reconciliação automática foi aprovada; eventos mais antigos devem ser encaminhados à revisão autorizada.

Nenhuma alteração foi realizada no Supabase nesta etapa.


### Aprovação complementar — idade de eventos offline (2026-11-08)

O responsável aprovou a janela configurável inicial de 24 horas para reconciliação automática. A idade não é critério isolado: o servidor deve validar sequência, estado atual, identidade/origem, autorização, idempotência e consistência temporal. Eventos com mais de 24 horas seguem para revisão por gestor ou atendente/usuário expressamente autorizado. Esta definição não autoriza alterações no Supabase.


## Atualização — 2026-11-08 — plano de migração da fundação multiempresa (Parte A)

**Estado:** proposta técnica criada para aprovação; execução no Supabase não autorizada.

Documento: [PLANO_MIGRACAO_FUNDACAO_MULTIEMPRESA_PARTE_A_MVP.md](relatorios/PLANO_MIGRACAO_FUNDACAO_MULTIEMPRESA_PARTE_A_MVP.md).

A Parte A cobre estrutura física, backfill, constraints, FKs compostas, unicidade por empresa, correções de defaults, integridade, testes e rollback. A Parte B — contexto confiável de empresa, funções auxiliares, grants e RLS — permanece separada até definição do contrato transacional no backend.

**Atualização documental subsequente:** o responsável confirmou que todas as 26 visitas pertencem à Winservice e que a OS é opcional para uma visita. Esses dois pontos deixam de ser bloqueios. A visita sem cliente/endereço continuará sem vínculos artificiais.

Pendências vigentes:
- executar ADR-0018: manter `company_user_roles` como fonte única, ignorar o campo legado e tratar dependências antes da remoção física de `company_users.role_identifier`;
- aprovação/ajuste da proposta física do catálogo de permissões global versionado e adoção por empresa;
- desenho físico de catálogos de tarefa/requisito;
- correção da função de snapshot de endereço, dependente da inspeção do trigger;
- baseline de migrations e repositório de implementação ainda não identificados;
- Parte B: contrato confiável de contexto transacional no backend antes de funções, grants e RLS.

Nenhum DDL, DML, migration, policy ou grant foi executado nesta etapa.


## 15. Complemento — revisão do plano multiempresa e catálogo de permissões (2026-11-08)

O plano da Parte A foi revisado para incorporar as deliberações do responsável:
- todas as 26 visitas existentes pertencem à Winservice; o backfill deverá preencher o tenant em todas;
- visita pode existir sem OS; `work_order_identifier` permanece nullable e a FK composta será aplicada quando houver referência;
- a decisão sobre a fonte única de papéis foi posteriormente formalizada e aprovada na ADR-0018; o registro anterior desta seção está superado. `company_user_roles` é a fonte única e `company_users.role_identifier` será descartado sem reconciliação.

Foi criada a proposta [PROPOSTA_MODELO_FISICO_CATALOGO_PERMISSOES_MVP.md](relatorios/PROPOSTA_MODELO_FISICO_CATALOGO_PERMISSOES_MVP.md), v0.1.0, com catálogo global versionado, cópia efetiva tenant-scoped, preservação de customizações, adoção voluntária, histórico e restauração. A proposta aguarda aprovação e não autoriza DDL.

Artefatos atualizados:
- [PLANO_MIGRACAO_FUNDACAO_MULTIEMPRESA_PARTE_A_MVP.md](relatorios/PLANO_MIGRACAO_FUNDACAO_MULTIEMPRESA_PARTE_A_MVP.md), v0.2.0.
- [PROPOSTA_MODELO_FISICO_CATALOGO_PERMISSOES_MVP.md](relatorios/PROPOSTA_MODELO_FISICO_CATALOGO_PERMISSOES_MVP.md), v0.1.0.

A inspeção somente leitura do schema confirmou, neste momento, 1 registro em `permissions`, 0 em `role_permissions`, 0 em `user_permissions`, 3 em `roles`, 1 em `company_users` e 2 em `company_user_roles`. Nenhuma alteração no Supabase foi executada.


## 16. Decisão aprovada — fonte única de papéis por vínculo (ADR-0018)

Em 2026-11-08, o responsável aprovou formalmente a ADR-0018:
- `company_user_roles` é a única fonte de atribuição de papéis aos vínculos usuário–empresa;
- `company_users.role_identifier` e seu valor legado ficam fora do ERP; não haverá reconciliação nem migração desse valor;
- a coluna deverá ser removida do schema físico após tratamento das dependências.

Inspeção somente leitura do Supabase identificou a constraint `company_users_role_identifier_fkey` e referência a `company_users.role_identifier` no corpo da função `public.can_view_expense(uuid)`. Esta função está classificada como obsoleta e fora do escopo atual do MVP. Seu destino deverá ser resolvido antes da remoção da coluna. Nenhuma função, constraint, coluna ou dado foi alterado.

Documentos atualizados:
- [ADR-0018](decisoes/ADR-0018-company-user-roles-fonte-unica-de-papeis.md) — aprovada.
- [Índice de decisões arquiteturais](02_DECISOES_ARQUITETURA.md) — v0.7.0.
- [Modelo de autorização e isolamento multiempresa](MODELO_DE_AUTORIZACAO_E_ISOLAMENTO_MULTIEMPRESA.md) — v0.3.2.
- [Plano de migração Parte A](relatorios/PLANO_MIGRACAO_FUNDACAO_MULTIEMPRESA_PARTE_A_MVP.md) — v0.3.0.

A decisão documental não autoriza execução de DDL/DML ou migration no Supabase.
