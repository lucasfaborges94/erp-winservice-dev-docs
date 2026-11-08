# Modelo funcional operacional do MVP — ERP Winservice

**Versão:** 0.2.0  
**Data da consolidação:** 2026-11-08  
**Estado:** baseline funcional em consolidação  
**Escopo:** clientes, equipamentos, ordens de serviço, visitas, tarefas, relatos, execução técnica por GPS, orçamentos e aprovações.

> Este documento consolida requisitos funcionais confirmados pelo responsável do projeto durante a modelagem. Não é uma ADR, não substitui decisões arquiteturais formais e não declara que os requisitos estejam implementados. A avaliação do Supabase é uma fotografia do schema consultado em 2026-11-08; qualquer mudança posterior exige nova inspeção.

## 1. Objetivo e princípios

O módulo Operação deve preservar a história completa do atendimento ao cliente, permitindo que atendentes humanos, agentes autônomos e técnicos entendam o que foi solicitado, o que ocorreu em cada visita, quais equipamentos foram envolvidos e quais providências ainda precisam ser tomadas.

Princípios:
- A Ordem de Serviço (OS) é o contêiner histórico do atendimento.
- Cada visita representa um comparecimento/atendimento em uma data e possui seu próprio conjunto de tarefas e relatos.
- Uma tarefa representa uma atividade técnica registrada em uma visita; não é checklist de etapas nem uma entidade de execução que migra entre visitas.
- A execução temporal do trabalho é registrada em sessões vinculadas à visita; tarefas não possuem sessões nem estados próprios de execução.
- Relatos e evidências preservam o que aconteceu; não se deve inferir conclusão técnica apenas pelo encerramento de uma sessão.
- Alterações não apagam silenciosamente o passado: versões e eventos anteriores devem permanecer consultáveis.
- O modelo deve permitir navegação tanto do cliente para seus equipamentos e OS quanto do equipamento para suas tarefas, visitas e OS.

## 2. Hierarquia e relacionamentos funcionais

A visão conceitual é:

`Cliente → Equipamentos`

`Cliente → Ordens de Serviço → Visitas → Tarefas → Equipamentos (opcional)`

`Visita → Relatos da visita`

`Tarefa → Relatos da tarefa, evidências e registros relacionados`

`Visita → Sessões de execução (zero ou mais)`

### 2.1 Cliente
- Reúne os dados cadastrais, contatos e endereços.
- Possui seus equipamentos e ordens de serviço.
- O histórico do cliente deve permitir chegar às visitas, tarefas, relatos, orçamentos e demais registros relacionados.

### 2.2 Equipamento
- Representa um bem associado a determinado cliente.
- Pode aparecer em tarefas de diferentes visitas e OS ao longo do tempo.
- Seu histórico deve permitir consultar todas as tarefas, visitas e OS em que foi relacionado.
- O equipamento pode ser vinculado posteriormente, quando os dados forem identificados.

### 2.3 Ordem de Serviço (OS)
- É o registro que preserva o histórico completo de um atendimento.
- Uma OS pode conter uma ou mais visitas.
- Uma OS mantém a relação com o cliente e agrega a história operacional, comercial e documental pertinente.
- Uma nova visita em outra data normalmente continua na mesma OS.
- No momento de criar uma nova visita, o técnico poderá optar por vinculá-la à OS existente ou indicar a criação de uma nova OS.
- Separar ou abrir outra OS não deve apagar nem reescrever o histórico das visitas anteriores.

### 2.4 Visita
- Representa o comparecimento/atendimento em uma data e local.
- Pertence a uma OS e mantém referência ao cliente, endereço, técnico responsável, agendamento e eventos de chegada/saída.
- Cada visita possui suas próprias tarefas e pode possuir relato geral.
- Uma visita precisa ter pelo menos um relato registrado nela própria ou em uma de suas tarefas para poder ser encerrada.
- Ao iniciar uma nova visita para o mesmo técnico, a visita anterior deve ser finalizada mediante registro do relato exigido; a nova execução não deve começar antes dessa condição.
- O encerramento da visita não significa que todas as atividades técnicas tenham sido concluídas; os relatos devem descrever o resultado real.

### 2.5 Tarefa
- É um registro da atividade técnica solicitada ou realizada durante uma visita.
- Cada tarefa pertence a uma visita específica.
- Exemplos de tipos (lista não exaustiva): orçamento, coleta de equipamento, cobrança, retorno/garantia e vistoria.
- Cada tipo de tarefa poderá definir campos, relatos, evidências e obrigatoriedades próprias.
- Os tipos partem de um catálogo global inicial, mas cada empresa poderá editar sua própria versão sem alterar o catálogo global nem as versões de outras empresas.
- A tarefa não terá status de execução ou de conclusão. Não usar estados como `pending`, `in_progress`, `paused` ou `completed` na entidade tarefa.
- A história da tarefa é composta por seus dados, relatos e evidências; a execução temporal é registrada no nível da visita.
- Uma tarefa registrada em uma visita não é transferida para outra visita. Se o atendimento continuar em outra data, cria-se outra visita e uma nova tarefa, vinculada ao mesmo equipamento quando aplicável e podendo referenciar a tarefa anterior.
- A tarefa poderá existir sem equipamento previamente identificado.

### 2.6 Associação de equipamento à tarefa

São três situações funcionais distintas:
1. **Equipamento identificado:** tarefa vinculada a um equipamento cadastrado do cliente.
2. **Equipamento não identificado:** sabe-se que há um equipamento envolvido, mas ainda não há dados suficientes para identificá-lo; o vínculo poderá ser completado depois.
3. **Sem equipamento associado:** tarefa não depende de um equipamento específico, como uma cobrança.

A estrutura física que representará essas três situações ainda precisa ser definida. Não criar equipamentos fictícios apenas para satisfazer uma referência obrigatória.

## 3. Relatos, evidências e obrigatoriedades

- A visita pode possuir um relato geral.
- Cada tarefa pode possuir um ou mais relatos próprios.
- Cada tipo de tarefa poderá exigir relatos, campos, fotos, documentos ou outras evidências específicas.
- As regras de obrigatoriedade serão configuráveis por tipo de tarefa.
- Para encerrar uma visita, deve existir pelo menos um relato válido no nível da visita ou de alguma tarefa daquela visita.
- Relatos devem manter autoria e horário de registro.
- Se um relato for corrigido, o conteúdo anterior deverá continuar visível no histórico.
- Fotos, documentos e demais evidências devem manter vínculo inequívoco com a visita ou tarefa correspondente e preservar sua autoria e temporalidade.

A definição de campos obrigatórios por tipo, estrutura de formulários e critérios para considerar um relato válido ainda requer detalhamento.

## 4. Execução técnica da visita e GPS

### 4.1 Separação entre tarefa e execução

A tarefa não possui estado nem sessão de execução. O trabalho temporal do técnico será representado por sessões vinculadas à visita. Enquanto a visita estiver em execução, o técnico registra as atividades realizadas por meio das tarefas e dos respectivos relatos.

### 4.2 Exclusividade do técnico e transição entre visitas

- Um técnico só pode executar uma visita por vez.
- O backend deve impedir que o mesmo técnico inicie ou mantenha duas visitas simultaneamente em execução, inclusive diante de requisições concorrentes ou dispositivos diferentes.
- Ao iniciar nova visita, a visita anterior deve ser finalizada mediante relato do técnico.
- A nova visita não pode iniciar enquanto a finalização anterior e o relato exigido não forem confirmados.
- Uma reabertura também deve respeitar a exclusividade.

### 4.3 Check-in automático

A funcionalidade de check-in automático por GPS poderá ser habilitada ou desabilitada pela empresa.

Parâmetros anteriormente definidos:
- Raio de chegada: **50 metros**, configurável.
- Permanência mínima dentro do raio: **5 minutos**, configurável.
- Pré-condição: a visita deve estar em deslocamento (in_displacement).

A permanência de 5 minutos para o primeiro check-in precisa ser confirmada em conjunto com os novos limiares de pausa e retomada; não deve ser confundida com a regra de retomada.

Quando as condições de check-in forem satisfeitas, registrar a chegada/início da execução da visita. Não iniciar uma tarefa individual: as tarefas são atividades registradas dentro da visita.

O evento automático deve preservar horário, coordenadas, precisão GPS, parâmetros aplicados e identificação do processo que o produziu. Eventos repetidos de localização não podem gerar check-ins duplicados.

### 4.4 Pausa automática por afastamento

- O ponto de referência é o endereço/coordenada da visita, não a coordenada de início de uma tarefa.
- Ao atingir ou ultrapassar **200 metros** do endereço da visita, a execução da visita será pausada automaticamente.
- O limiar de 200 metros é configurável.
- A pausa deve guardar horário, coordenadas, precisão GPS e distância calculada.
- O afastamento não significa que o serviço foi concluído nem finaliza a visita por si só.

### 4.5 Retomada automática

- Quando o técnico retornar para uma distância de **50 metros ou menos** do endereço da visita, a execução pausada poderá ser retomada automaticamente.
- O limiar de 50 metros é configurável.
- A retomada deve gerar evento próprio e preservar a pausa anterior.
- A retomada exige que a visita ainda esteja operacional e deve respeitar a exclusividade de uma visita por técnico.
- Visita finalizada não pode ser reativada silenciosamente por mera chegada GPS.

Os limiares assimétricos de 200 m para pausa e 50 m para retomada estabelecem uma faixa de histerese para reduzir oscilações repetidas próximas a um único limite.

### 4.6 Checkout manual e limite da jornada

- O técnico poderá finalizar manualmente a execução da visita, registrando horário e coordenadas GPS do checkout.
- O sistema não deve finalizar automaticamente uma execução que ainda esteja ativa.
- Somente execuções pausadas poderão ser finalizadas pelo processo automático de limite da jornada.
- O horário efetivo de finalização automática será o horário em que a execução foi pausada; o horário em que o sistema processou o fechamento será armazenado separadamente.
- As coordenadas efetivas serão as registradas no evento de pausa; não fabricar coordenadas no momento posterior do processamento.

## 5. Reabertura e edição de visitas finalizadas

### 5.1 Reabertura
- Somente o técnico poderá reabrir uma visita finalizada.
- A reabertura exige que o técnico esteja dentro de **200 metros** do endereço da visita.
- A validação deve ocorrer no backend com base na localização capturada no momento da solicitação.
- A reabertura deve ser registrada como evento auditável, incluindo técnico, data/hora, coordenadas, precisão GPS e visita.
- O gestor não pode reabrir uma visita.
- O atendente não pode reabrir uma visita.
- A reabertura deve respeitar a regra de uma visita por técnico.

### 5.2 Edição depois da finalização
- O gestor pode editar visitas finalizadas, mas não reabri-las.
- A possibilidade de edição por técnicos e atendentes será habilitada/desabilitada pelo gestor, com configurações independentes por perfil.
- A autorização deve ser aplicada no backend.
- Editar uma visita finalizada não altera seu estado para em andamento.
- Alterações posteriores devem preservar os dados anteriores e permitir consulta ao histórico completo.

## 6. Autoria, auditoria e preservação histórica

Todo registro operacional deverá identificar, no mínimo:
- quem criou o registro e quando;
- quem realizou a última alteração e quando.

Campos conceituais usuais:
- `created_at`;
- `created_by`;
- `updated_at`;
- `updated_by`.

A aplicação dessa convenção deverá ser avaliada entidade por entidade. Usuários humanos e usuários autônomos/agentes de IA devem ser identificados por suas próprias identidades no ERP; o usuário que configurou um agente não deve ser confundido com o agente que executou a operação.

Além dos metadados de criação/última alteração, deve existir histórico append-only de alterações relevantes, contendo:
- entidade e identificador do registro;
- tipo de evento/operação;
- autor;
- data/hora;
- valores anteriores e novos ou snapshot versionado;
- origem/canal da operação;
- motivo, quando exigido.

O histórico da visita deve manter versões anteriores de dados da visita e de seus registros relacionados, especialmente tarefas, relatos e associações de equipamentos. A implementação deverá evitar que uma edição destrua a evidência do conteúdo anteriormente registrado.

A estrutura técnica de auditoria, retenção, proteção contra adulteração e granularidade dos snapshots ainda precisa ser projetada.

## 7. Confronto com o schema Supabase consultado em 2026-11-08

**Projeto:** Winservice — `voaovwphncwbrwtcqnsc`  
**Schema:** `public`  
**Método:** inspeção somente leitura de tabelas, colunas e foreign keys. Nenhuma alteração foi executada.

### 7.1 Estruturas existentes relevantes

| Tabela | Evidência observada | Avaliação frente ao modelo |
|---|---|---|
| `customers` | Cadastro, status, empresa, created_by e updated_by | Base cadastral existente; verificar regras e consistência multiempresa |
| `customer_contacts` | Contatos ligados a cliente, empresa e autoria | Estrutura básica presente |
| `phonenumbers` | Telefone ligado a cliente | Existe, mas não possui created_by/updated_by |
| `addresses` | Endereço, coordenadas, cliente, empresa e autoria | Base presente; integridade empresa-cliente precisa de reforço |
| `equipments` | Dados do equipamento e customer_identifier | Cadastro existente; não possui created_by/updated_by |
| `work_orders` | identifier, status e customer_identifier | Base muito enxuta; não possui created_by/updated_by |
| `visits` | OS, cliente, status, endereço, coordenadas, responsável, agendamento, check-in e checkout | Base de visita existente; não possui relatos/tarefas/sessões próprios no schema observado |
| `work_order_equipment` | Associação entre OS e equipamento | Não substitui tarefa: não identifica atividade por visita, tipo, relato ou sessão |
| `task_types` | name, code e metadados de autoria | Catálogo básico de tipos existe, sem configuração de obrigatoriedades observada |
| `visit_types` | name, description, estimated_time e code | Catálogo de tipos de visita, distinto de tipos de tarefa |
| `visit_requirements` e `visit_type_requirement` | Requisitos associados a tipos de visita | Estrutura voltada a requisitos de visita; não comprova requisitos dinâmicos por tipo de tarefa |
| `quotations` | Orçamento relacionado a OS, cliente e opcionalmente visita | Existe; não há relação direta com tarefa observada |
| `approval_events` | Eventos de aprovação com versão, hash, decisão, origem e autoria | Estrutura útil; referencia orçamento, OS e cliente, não tarefa |
| `documents` | Metadados genéricos de arquivo e uploader | Não há vínculo direto com visita/tarefa observado na tabela |

### 7.2 Estruturas não encontradas na listagem atual

Não foram encontradas tabelas dedicadas com nomes `tasks`, `visit_tasks`, `task_reports`, `visit_reports`, `execution_sessions`, `visit_events` ou `audit_events`.

Essa constatação significa apenas que não há tabelas com esses nomes na listagem consultada. Não exclui que algum comportamento esteja parcialmente representado por outra estrutura, JSON, serviço externo ou código não inspecionado.

### 7.3 Lacunas e inconsistências relevantes

1. **Entidade tarefa:** existe `task_types`, mas não foi encontrada uma entidade de tarefa vinculada à visita. O catálogo de tipos não registra atividades individuais.
2. **Requisitos por tipo de tarefa:** `task_types` possui somente nome e código além dos metadados; não foi observada estrutura de campos/obrigatoriedades por tipo.
3. **Relatos:** não foi encontrada estrutura dedicada para relatos gerais de visita e relatos de tarefa.
4. **Sessões de execução:** não foi encontrada estrutura para múltiplos períodos de execução, pausas, retomadas, checkout e coordenadas por sessão.
5. **Eventos GPS:** `visits` possui um conjunto único de colunas de check-in/checkout, insuficiente por si só para preservar vários ciclos de pausa e retomada.
6. **Auditoria histórica:** campos de autoria/atualização não substituem um histórico append-only com valores anteriores e novos.
7. **Autoria inconsistente:** `work_orders`, `equipments`, `phonenumbers` e `quotations` não apresentam `created_by`/`updated_by` no schema consultado. `task_types` utiliza `last_updated_by`; `work_order_equipment` possui defaults `gen_random_uuid()` nos campos de autoria, que precisam de revisão antes de serem considerados confiáveis.
8. **Orçamento por tarefa:** `quotations` está relacionado à OS, cliente e visita; não foi observada referência direta a tarefa.
9. **Evidências:** `documents` guarda metadados de arquivos, mas não apresenta vínculo direto com tarefa ou visita.
10. **Associação de equipamento:** `work_order_equipment` relaciona OS e equipamento, mas não registra em qual tarefa e visita o equipamento foi efetivamente atendido.
11. **Integridade multiempresa:** a auditoria técnica de 07/11/2026 identificou ausência de garantias compostas em relações relevantes e policies RLS permissivas. O modelo operacional não deve ser implementado antes de alinhar autorização e integridade multiempresa.
12. **Trigger de endereço:** a auditoria de 07/11/2026 identificou incompatibilidade entre a função `set_visit_address_snapshot` e as colunas existentes em `addresses`; requer correção e teste antes de depender do fluxo de criação de visitas.

### 7.4 Conclusão técnica

O Supabase possui uma fundação cadastral e relacionamentos iniciais para clientes, equipamentos, OS, visitas, tipos de tarefa, orçamentos e aprovações. Entretanto, o modelo consultado não demonstra a implementação do núcleo funcional agora definido para tarefas por visita, relatos, sessões de execução, múltiplos eventos GPS e histórico imutável de alterações.

A próxima etapa técnica deverá produzir um modelo lógico e uma matriz de conformidade detalhada antes de qualquer DDL ou migração. Não adicionar tabelas/colunas por inferência nem adaptar `work_order_equipment` para representar tarefas.

## 8. Pendências de especificação

1. Definir campos, relatos e evidências obrigatórios para cada tipo de tarefa.
2. Definir o esquema de tarefa e a distinção persistente entre equipamento não identificado e tarefa sem equipamento.
3. Definir se uma visita pode ter várias sessões de tarefas simultâneas ou apenas uma sessão ativa por técnico.
4. Definir raio, precisão mínima e tempo de confirmação para pausa automática.
5. Definir condições de permanência para retomada automática.
6. Definir horário de corte da jornada e fuso horário por empresa.
7. Definir exatamente quando a visita será finalizada automaticamente e como tratar visita com sessão ainda em execução.
8. Definir o critério de relato adequado exigido após finalização automática.
9. Definir se e como a reabertura GPS poderá ser tentada quando o técnico já tiver outra visita ativa.
10. Definir estrutura de auditoria, versionamento e snapshots históricos.
11. Definir relação entre tarefas, orçamentos, documentos, fotos e demais evidências.
12. Definir estados e transições da visita, sem reutilizar status de visita como status da tarefa.
13. Definir como a seleção da OS existente ou criação de nova OS será apresentada no fluxo do técnico.
14. Definir tratamento de falha/indisponibilidade do GPS e ações manuais excepcionais, sem permitir fraude ou perda de rastreabilidade.

## 9. Segurança e limites desta análise

- A inspeção do Supabase foi somente leitura.
- Nenhuma tabela, coluna, policy, função, trigger, dado ou configuração foi alterada.
- Não foram consultados registros pessoais de clientes.
- Não foram executados testes funcionais, de GPS, concorrência, RLS ou acesso cruzado.
- Não foram inspecionados workflows n8n, Chatwoot, aplicativo móvel ou backend externo.
- O inventário e o relatório anterior são fotografias técnicas datadas; esta seção usa a listagem de schema consultada em 2026-11-08.
- Requisitos funcionais confirmados não equivalem a implementação comprovada nem a uma ADR formal.

## Histórico de alterações

- 2026-11-08 — v0.2.0: atualização para execução/sessões no nível da visita, finalização da visita anterior mediante relato, limiares GPS de 200 m/50 m e catálogo global de tipos editável independentemente por empresa.
- 2026-11-08 — v0.1.0: criação do baseline funcional de Operação e confronto inicial com o schema Supabase consultado em tempo real, somente leitura.
