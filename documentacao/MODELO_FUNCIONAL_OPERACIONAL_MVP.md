# Modelo funcional operacional do MVP — ERP Winservice

**Versão:** 0.8.0  
**Data da consolidação:** 2026-11-08  
**Estado:** baseline funcional em consolidação  
**Escopo:** clientes, equipamentos, ordens de serviço, visitas, tarefas, relatos, requisitos operacionais, evidências e uploads, aceite digital, execução técnica por GPS, orçamentos e aprovações.

> Este documento consolida requisitos funcionais confirmados pelo responsável do projeto durante a modelagem. Não é uma ADR, não substitui decisões arquiteturais formais e não declara que os requisitos estejam implementados. A avaliação do Supabase é uma fotografia do schema consultado em 2026-11-08; qualquer mudança posterior exige nova inspeção.

## 1. Objetivo e princípios

O módulo Operação deve preservar a história completa do atendimento ao cliente, permitindo que atendentes humanos, agentes autônomos e técnicos entendam o que foi solicitado, o que ocorreu em cada visita, quais equipamentos foram envolvidos e quais providências ainda precisam ser tomadas.

Princípios:
- A Ordem de Serviço (OS) é o contêiner histórico do atendimento.
- Cada visita representa um comparecimento/atendimento em uma data e possui seu próprio conjunto de tarefas e relatos.
- Uma tarefa representa uma atividade técnica registrada em uma visita; não é checklist de etapas nem uma entidade de execução que migra entre visitas.
- A visita é a única entidade com ciclo de vida operacional. O tempo de execução é reconstruído a partir de eventos históricos append-only da própria visita; não existe máquina ou entidade independente de sessão no MVP.
- Relatos e evidências preservam o que aconteceu; não se deve inferir conclusão técnica apenas por um evento isolado de execução.
- Alterações não apagam silenciosamente o passado: versões e eventos anteriores devem permanecer consultáveis.
- O modelo deve permitir navegação tanto do cliente para seus equipamentos e OS quanto do equipamento para suas tarefas, visitas e OS.

## 2. Hierarquia e relacionamentos funcionais

A visão conceitual é:

`Cliente → Equipamentos`

`Cliente → Ordens de Serviço → Visitas → Tarefas → Equipamentos (opcional)`

`Visita → Relatos da visita`

`Tarefa → Relatos da tarefa, requisitos, evidências e registros relacionados`

`Visita/Tarefa → Requisitos aplicáveis → Avaliações → Pendências e tratamentos`

`Evidências → Uploads → Resumos versionados e aceites digitais`

`Visita → Eventos operacionais append-only (um ou mais, conforme os acontecimentos)`

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

## 3. Relatos, requisitos, pendências e evidências

### 3.1 Relatos

- A visita pode possuir um relato geral.
- Cada tarefa pode possuir um ou mais relatos próprios.
- Toda visita precisa ter pelo menos um relato geral ou de uma tarefa antes de ser encerrada.
- O relato deve preservar autoria e horário de registro.
- Correções geram novos registros/revisões; o conteúdo anterior permanece consultável.
- O relato da solicitação original do cliente deve permanecer distinto do relato técnico, para não confundir o pedido com a conclusão profissional.

### 3.2 Requisitos da visita e das tarefas

- Os requisitos da visita são definidos por `visit_requirements` e pelo tipo de visita aplicável.
- Os requisitos de cada tarefa são definidos por `task_type_requirements`.
- Cada requisito deve indicar se é obrigatório ou opcional.
- Os requisitos aplicáveis devem ser versionados ou congelados em snapshot quando vinculados à visita/tarefa. Alterações futuras em catálogos não podem modificar retroativamente os requisitos de atendimentos anteriores.
- A tarefa não possui status de execução ou conclusão. O resultado da atividade é documentado pelos relatos, evidências e avaliação dos requisitos no contexto da visita.
- As tarefas vinculadas à visita permanecem como registros históricos daquela visita; não são transferidas nem recebem ciclo individual de início/conclusão.

### 3.3 Avaliação dos requisitos e indicadores de pendência

Toda tentativa de finalização normal da visita deve gerar uma avaliação histórica completa, mesmo quando a conclusão for bloqueada. A avaliação inclui os requisitos da visita e de todas as tarefas vinculadas, com snapshot da definição aplicável e resultado individual.

Resultados conceituais por requisito:
- `fulfilled`: requisito cumprido com os dados/evidência exigidos;
- `not_fulfilled`: requisito não cumprido;
- `not_applicable`: requisito não aplicável, somente quando a definição permitir e a justificativa/regra correspondente for preservada.

Regras:
- Requisito obrigatório não cumprido bloqueia a conclusão normal.
- Requisito opcional não cumprido não bloqueia a conclusão normal; gera pendência para revisão.
- A visita poderá ser concluída normalmente quando todos os requisitos obrigatórios estiverem cumpridos, mesmo que existam requisitos opcionais pendentes.
- A visita também poderá ser encerrada excepcionalmente por interrupção/abandono com requisitos obrigatórios pendentes. Esse encerramento não equivale a conclusão normal; os requisitos permanecem pendentes e sujeitos à análise do gestor/atendente autorizado e a eventual reavaliação.
- Uma dispensa justificada não altera o resultado original: o requisito continua historicamente não cumprido, embora seu tratamento possa ser encerrado por decisão autorizada.
- Gestor e atendente autorizado podem aceitar a ausência de requisito opcional mediante justificativa. O técnico pode cumprir o requisito ou solicitar revisão, mas não dispensá-lo.
- Para requisitos obrigatórios pendentes em encerramento excepcional, a justificativa e a revisão do gestor devem ser registradas. A dispensa, quando autorizada, não deve ser confundida com cumprimento.
- Cada nova tentativa de finalização gera uma nova avaliação; avaliações anteriores nunca são sobrescritas.
- As avaliações e decisões devem respeitar o histórico append-only aprovado na ADR-0016.

Indicadores derivados, não editáveis manualmente:
- `has_pending_mandatory_requirements`;
- `has_pending_optional_requirements`.

Os dois indicadores devem existir conceitualmente no contexto da visita e de cada tarefa. São projeções do conjunto de avaliações e tratamentos, não fonte primária da verdade. A apresentação deve distinguir pendências abertas, cumpridas posteriormente e dispensadas com justificativa.

### 3.4 Evidências e uploads

Evidências podem ser exigidas no nível da visita ou da tarefa. Tipos previstos incluem fotografia, documento e assinatura/aceite digital.

**Fotografias**
1. O técnico aciona a captura pela câmera.
2. Visualiza a imagem e confirma sua legibilidade.
3. Confirma o envio.
4. O upload pode continuar em segundo plano.
5. A evidência só satisfaz o requisito após confirmação do backend de que o arquivo foi recebido, armazenado e validado.

**Documentos**
- O técnico pode fotografar o documento ou selecionar um arquivo do dispositivo.
- Deve poder conferir o conteúdo antes de confirmar o envio.
- O requisito só é satisfeito após confirmação do armazenamento pelo backend.

**Upload**
- O envio deve ter identidade idempotente, estado, tentativas, horários, erros e confirmação de armazenamento.
- São necessários timeout e retries limitados, com política de backoff a definir.
- Uma repetição não pode criar evidências duplicadas nem satisfazer indevidamente um requisito.
- Upload obrigatório pendente, em andamento, com falha ou timeout impede a conclusão normal da visita.
- Upload opcional não confirmado gera pendência opcional e não bloqueia a conclusão normal.
- Em modo offline, o arquivo deve permanecer em armazenamento local protegido até sincronização confirmada; a evidência ainda não pode ser considerada confirmada pelo backend.
- O comportamento após esgotar retries deve permitir manter a visita em andamento ou encerrá-la excepcionalmente por interrupção, sem autorizar conclusão normal com evidência obrigatória não confirmada.

Metadados conceituais de cada evidência:
- identificador e empresa;
- vínculo com visita ou tarefa e com o requisito que pretende satisfazer;
- tipo, nome, tipo MIME, tamanho e referência de armazenamento;
- `created_at` (`timestamptz`) como instante de criação/registro da evidência;
- `captured_at` quando houver instante de captura no dispositivo;
- `received_at`/confirmação de upload no backend;
- latitude e longitude do local de captura, quando disponíveis e autorizadas;
- precisão da localização, quando disponível;
- autor, dispositivo/origem, checksum/hash e estado de validação.

As coordenadas representam o local da captura/coleta, não o local posterior de sincronização. Horário de captura, criação local, recebimento e confirmação no servidor devem ser distinguíveis. Para aceite remoto, a localização só será coletada quando disponível e autorizada.

### 3.5 Tratamento das pendências

O tratamento da pendência é distinto do resultado original da avaliação:
- `pending_review`: aguardando análise;
- `assigned_to_technician`: devolvida ao técnico para providências;
- `fulfilled`: requisito posteriormente cumprido com evidência/registro válido;
- `accepted_with_justification`: ausência aceita por gestor/atendente autorizado, com justificativa.

O conjunto de estados é conceitual e poderá ser refinado, mas não deve apagar o resultado original da avaliação. O técnico pode cumprir ou solicitar revisão; não pode dispensar requisito. A decisão de dispensa deve registrar autor autorizado, data/hora, justificativa e referência à avaliação/requisito.

## 4. Ciclo operacional da visita, execução técnica e GPS

### 4.1 Princípio: uma única máquina de estados

A visita é a única entidade que controla o ciclo operacional do atendimento. Não haverá uma Máquina B, estados independentes de sessão (active, paused, closed) nem entidade de sessão de execução no MVP.

A execução técnica acompanha o estado da visita:
- pending: execução ainda não iniciada;
- in_displacement: técnico em deslocamento;
- in_progress: atendimento em execução;
- paused: execução temporariamente pausada;
- completed: execução encerrada normalmente;
- interrupted: execução iniciada e encerrada excepcionalmente;
- prevented: execução não iniciada por impedimento operacional;
- canceled: agendamento cancelado antes do início da execução.

Não existe estado de execução separado que possa divergir do estado da visita. Tarefas não possuem estado nem sessão de execução.

### 4.2 Exclusividade do técnico e transição entre visitas

- Um técnico só pode executar uma visita por vez.
- O backend deve impedir que o mesmo técnico inicie ou mantenha duas visitas simultaneamente em execução, inclusive diante de requisições concorrentes ou dispositivos diferentes.
- Uma visita em paused continua ocupando a disponibilidade operacional do técnico.
- Antes de iniciar outra visita, a anterior precisa alcançar estado terminal e ter o relato exigido registrado.
- A nova visita não pode iniciar enquanto a finalização anterior e o relato exigido não forem confirmados.
- Estados terminais não são reabertos; a continuidade exige nova visita, que deve respeitar a exclusividade do técnico.

### 4.3 Check-in e início da execução

O início manual pode ocorrer diretamente de pending para in_progress ou após deslocamento, de in_displacement para in_progress. O check-in manual não exige permanência mínima.

O check-in automático por GPS é funcionalidade futura, fora do MVP. Quando implementado, poderá ser habilitado/desabilitado pela empresa e terá como parâmetros iniciais configuráveis:
- raio de chegada: 50 metros;
- permanência mínima: 5 minutos;
- pré-condição: visita em in_displacement.

O evento automático deverá preservar horário, coordenadas, precisão GPS, parâmetros aplicados e identificação do processo. Eventos repetidos não poderão gerar transições duplicadas.

### 4.4 Pausa e retomada por GPS

As regras funcionais de pausa e retomada automática permanecem:
- o ponto de referência é o endereço/coordenada da visita, não a coordenada de início de uma tarefa;
- afastamento de 200 metros ou mais pausa a visita (in_progress → paused); o limiar é configurável;
- retorno a 50 metros ou menos pode retomar a visita (paused → in_progress); o limiar é configurável;
- a pausa e a retomada geram eventos próprios, preservando horário, coordenadas, precisão e distância quando confiável;
- a retomada exige que a visita continue operacional e respeita a exclusividade do técnico;
- visita terminal não pode ser reativada por mera chegada GPS.

Os limiares assimétricos formam uma faixa de histerese. Posição obsoleta não provoca transição automática.

### 4.5 Jornada de trabalho

O fim da jornada não encerra automaticamente a visita nem a execução técnica. Uma visita em in_progress ou paused poderá permanecer nesse estado depois do horário previsto de encerramento da jornada, até que o técnico a finalize normalmente ou excepcionalmente.

Ao tentar iniciar uma visita ou fazer check-in fora da jornada, o aplicativo deverá solicitar confirmação explícita ao técnico antes de permitir a operação.

Não haverá encerramento automático da visita no fim da jornada; a jornada não é gatilho de transição de estado.

### 4.6 Checkout e registro temporal

O técnico poderá encerrar manualmente a visita, registrando horário e coordenadas GPS do checkout, quando disponíveis. O sistema não deve finalizar automaticamente uma execução ativa por perda de sinal/GPS.

O tempo de execução será reconstruído a partir da sequência de eventos da visita:
- início (in_progress);
- pausas (paused);
- retomadas (in_progress);
- encerramento terminal (completed ou interrupted).

Os intervalos e durações serão projeções calculadas; não exigem sessão ou estado temporal independente. Eventos devem distinguir o instante efetivo do acontecimento (occurred_at) e o instante de registro no backend (recorded_at).

### 4.7 Impedimento, cancelamento e interrupção

- prevented representa impedimento operacional externo que impediu o início da execução; exige motivo estruturado, relato do técnico, data/hora, autoria e evento histórico.
- canceled representa cancelamento do agendamento antes do início da execução.
- interrupted representa encerramento excepcional de visita cuja execução já começou. Pode ocorrer com requisitos obrigatórios pendentes, que permanecem sujeitos a revisão.
- in_displacement pode retornar a pending quando o deslocamento for cancelado sem cancelar o agendamento.
- Estados terminais não possuem transições de saída. Continuidade exige nova visita, que poderá permanecer vinculada à mesma OS.

### 4.8 Revogação de autenticação

Quando a autenticação do técnico for revogada:
- em pending, a visita permanece pending;
- em in_displacement, retorna para pending;
- em in_progress, transiciona para interrupted;
- em paused, transiciona para interrupted;
- em estado terminal, permanece inalterada.

A revogação deve gerar evento histórico com visita, técnico, instante, origem/motivo e estados anterior/posterior. Não apaga dados nem histórico. Ainda não está definido se logout voluntário, expiração de token e revogação administrativa terão tratamento idêntico; não presumir equivalência.

### 4.9 GPS e perda de sinal

A leitura GPS será aceita inicialmente quando o raio de incerteza reportado pelo dispositivo for de até 50 metros; esse limite é configurável. Valores menores representam leituras mais precisas. Em perda de sinal, o sistema conserva como referência a posição mais recente recebida, registrando sua data/hora e idade. Uma posição antiga não será tratada como nova leitura nem, isoladamente, provocará pausa ou retomada automática.


### 4.10 Sincronização offline e conferência do relógio

Quando o dispositivo receber uma nova visita, deverá comparar seu relógio com o horário oficial do ERP/servidor antes de disponibilizar a visita ao técnico. Se houver divergência, o aplicativo apresentará um aviso; a divergência isolada não bloqueia a disponibilização. O servidor permanece como referência para o horário de registro (`recorded_at`).


A janela inicial para reconciliação automática de eventos offline foi aprovada em 24 horas, configurável. Dentro dessa janela, o servidor ainda deverá validar sequência, estado atual da visita, identidade/origem, autorização, idempotência e consistência temporal; a idade, isoladamente, não garante aceitação. Eventos com mais de 24 horas não serão reconciliados automaticamente e deverão ser encaminhados à revisão de gestores ou atendentes/usuários expressamente autorizados.

Na sincronização de eventos offline, devem ser preservados separadamente o instante de ocorrência (`occurred_at`) e o instante de recebimento/registro oficial (`recorded_at`). O servidor valida estado atual, sequência, autorização, empresa e idempotência antes de aceitar o evento.

Comandos rejeitados ou conflitantes não integram o histórico canônico de eventos da visita; devem ser auditados separadamente. A revisão desses casos é restrita a gestores e atendentes/usuários expressamente autorizados. O técnico não pode aprovar nem dispensar conflitos.

A política de idade máxima para reconciliação automática permanece pendente de aprovação; não presumir um prazo vigente.

## 5. Reabertura e edição de visitas finalizadas

### 5.1 Reabertura
- Visitas em estado terminal (completed, interrupted, prevented ou canceled) não podem ser reabertas nem retornar ao fluxo operacional.
- A continuidade do atendimento exige a criação de uma nova visita, que poderá permanecer vinculada à mesma OS e referenciar a visita/tarefa anterior conforme o contexto.
- A nova visita deve respeitar a exclusividade de uma visita por técnico.
- A regra anterior de reabertura por técnico dentro de 200 metros fica substituída pela máquina de estados aprovada, que define os estados terminais sem transições de saída.

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

Decisão arquitetural aprovada: além dos metadados de criação/última alteração, manter histórico append-only de alterações relevantes, contendo:
- entidade e identificador do registro;
- tipo de evento/operação;
- autor;
- data/hora;
- valores anteriores e novos ou snapshot versionado;
- origem/canal da operação;
- motivo, quando exigido.

O histórico da visita deverá preservar versões anteriores dos dados da visita e de seus registros relacionados, especialmente tarefas, relatos e associações de equipamentos. A proposta é que correções gerem novos eventos/revisões, sem destruir a evidência do conteúdo anteriormente registrado.

O padrão append-only está aprovado. Permanecem para desenho técnico a estrutura de auditoria, retenção, proteção contra adulteração, granularidade dos snapshots e mecanismo de correção/revisão. A aprovação da diretriz não significa que a implementação esteja concluída.

## 7. Vínculo funcional dos orçamentos

Cada orçamento poderá:
- pertencer a uma Ordem de Serviço; ou
- pertencer a uma visita; ou
- pertencer a uma tarefa; ou
- não estar relacionado a nenhuma dessas entidades.

O vínculo é opcional e exclusivo: um orçamento não poderá estar simultaneamente vinculado a OS, visita e/ou tarefa. Quando vinculado a visita ou tarefa, sua relação com a OS poderá ser obtida pela hierarquia operacional, sem duplicar o vínculo no orçamento. O cliente e a empresa deverão ser coerentes com a entidade de contexto, quando houver vínculo.

A migração do schema atual deverá tratar registros que hoje tenham simultaneamente vínculo de OS e visita, sem escolher automaticamente qual deles deve prevalecer.

## 8. Máquina de estados da visita e histórico

A visita possui a única máquina de estados do ciclo operacional de atendimento. Ela é distinta de qualquer máquina reutilizável do módulo Chat.

### 8.1 Estados

| Estado | Significado |
|---|---|
| pending | Visita agendada; deslocamento ainda não iniciado |
| in_displacement | Técnico iniciou o deslocamento |
| in_progress | Atendimento técnico iniciado |
| paused | Atendimento temporariamente suspenso |
| completed | Conclusão normal |
| interrupted | Atendimento iniciado e encerrado antes da conclusão normal |
| prevented | Técnico impedido de iniciar por impedimento operacional externo |
| canceled | Agendamento cancelado antes do início da execução |

### 8.2 Transições permitidas

| Origem | Destinos permitidos |
|---|---|
| pending | in_displacement, in_progress, prevented, canceled |
| in_displacement | pending, in_progress, prevented, canceled |
| in_progress | paused, completed, interrupted |
| paused | in_progress, interrupted |

completed, interrupted, prevented e canceled são terminais e não possuem transições de saída.

### 8.3 Regras de transição

- O início pode ocorrer diretamente de pending para in_progress, sem deslocamento.
- in_displacement → pending cancela somente o deslocamento, não o agendamento.
- prevented só se aplica antes do início do atendimento e exige motivo/relato.
- interrupted só se aplica depois do início do atendimento e exige motivo/relato; não equivale a conclusão normal.
- Estados terminais não são reabertos. A continuidade exige nova visita.
- Revogação de autenticação: in_displacement → pending; in_progress ou paused → interrupted; pending permanece; estados terminais permanecem inalterados.
- A jornada não finaliza automaticamente a visita. A tentativa de início/check-in fora da jornada exige confirmação do técnico no aplicativo.

### 8.4 Eventos e imutabilidade

A sequência operacional será registrada em eventos append-only da visita, sem Máquina B e sem entidade de sessão no MVP. O evento deverá preservar, conforme aplicável, visita/empresa, tipo, estado anterior/posterior, instante efetivo, instante de registro, ator, origem, motivo, coordenadas, precisão e metadados.

A diretriz append-only está aprovada pela ADR-0016. Correções devem gerar novos eventos/revisões, sem sobrescrever o conteúdo histórico original. A estrutura física, retenção, integridade, proteção contra alteração e granularidade dos snapshots ainda precisam de desenho técnico.

## 9. Histórico de posições GPS

O sistema manterá um histórico de posições recebidas dos dispositivos dos técnicos, em tabela própria conceitualmente denominada `technician_gps_positions`. Cada amostra deverá preservar, no mínimo, técnico, empresa, latitude, longitude, precisão reportada, instante da captura no dispositivo, instante de recebimento pelo backend, origem/dispositivo e metadados técnicos necessários à validação.

A posição mais recente recebida será a última amostra válida, acompanhada de sua idade e precisão. Em perda de sinal, a última posição fica disponível como referência histórica; não é prova de localização atual. Sem nova amostra, não executar transição automática de pausa/retomada baseada em posição desatualizada. O histórico GPS é dado operacional sensível e exige controle de acesso por empresa, retenção definida, minimização e auditoria de consultas.

## 10. Catálogos globais e personalizações por empresa

Diretriz funcional confirmada: tipos de tarefa partem de um catálogo global e podem ser editados independentemente por cada empresa. A arquitetura física recomendada é um repositório de catálogos globais versionados, com versões imutáveis publicadas pela plataforma, e uma camada de catálogo efetivo por empresa.

Estrutura conceitual sugerida:
- `global_catalogs`: identifica cada catálogo e seu domínio;
- `global_catalog_versions`: versões globais publicadas e imutáveis;
- `global_catalog_items`: itens pertencentes a cada versão;
- `company_catalogs` e `company_catalog_items`: versão adotada e personalizações locais, com referência ao item e à versão de origem;
- snapshots/histórico das personalizações para comparação, restauração e atualização controlada.

A empresa não altera registros globais. Aderir a uma nova versão global deve ser ação explícita e controlada, apresentar diferenças e preservar customizações locais. A estrutura exata e a política de merge permanecem como proposta técnica, ainda não aprovada como ADR.

## 11. Aceite digital e resumo da visita

### 11.1 Finalidade e modalidades

O aceite digital registra a manifestação do cliente sobre uma versão específica do resumo da visita e seus anexos. A assinatura não deve ser tratada como simples imagem isolada: precisa estar vinculada ao conteúdo efetivamente apresentado, à identificação do signatário e aos eventos de apresentação e decisão.

Modalidades aprovadas:
1. **Padrão — dispositivo pessoal do cliente:** envio de link individual por canal disponível (por exemplo, WhatsApp ou e-mail); o cliente abre o link no próprio celular, consulta o conteúdo e assina.
2. **Alternativa presencial — dispositivo do técnico:** utilizada quando o fluxo padrão não for viável. O técnico entrega o dispositivo ao cliente, que deve visualizar o mesmo conteúdo e realizar pessoalmente a assinatura. O técnico não pode assinar em nome do cliente.
3. **Presencial offline:** quando não houver conectividade, o aplicativo pode coletar a assinatura no dispositivo do técnico, desde que o resumo tenha sido preparado e armazenado localmente. A coleta fica protegida e aguardando sincronização; o aceite só é confirmado pelo backend após sincronização e validação.

A modalidade deve ser registrada. Nenhuma modalidade será considerada juridicamente superior apenas pelo dispositivo utilizado; a confiabilidade depende do conjunto de evidências e controles.

### 11.2 Identificação do signatário

- O signatário deve ser identificado antes da assinatura.
- A identificação deve ser proporcional à finalidade e evitar coleta indiscriminada de CPF.
- Data de nascimento foi considerada como alternativa de menor exposição em relação ao CPF, mas o método inicial e os critérios de validação ainda precisam ser especificados.
- CPF poderá ser utilizado quando necessário, mas não deve ser exigido indiscriminadamente.
- Não haverá coleta nem armazenamento da impressão digital do cliente.
- A assinatura desenhada na tela é evidência gráfica da manifestação, não biometria de impressão digital.
- Na coleta presencial, o cliente deve preencher/confirmar pessoalmente os dados de identificação e assinar; o técnico não deve preencher ou assinar em seu nome.

### 11.3 Conteúdo do resumo apresentado

O resumo principal deverá conter, inicialmente:
- data e hora relacionadas à visita e ao atendimento;
- identificação do cliente;
- equipamento(s) envolvidos;
- identificação do técnico responsável;
- motivo da visita, refletindo a solicitação original do cliente;
- relato geral do técnico e relatos das tarefas;
- fotografias incorporadas diretamente no resumo;
- valores monetários discriminados, relacionados à visita e às tarefas.

Documentos não serão incorporados visualmente ao resumo principal: serão apresentados como anexos identificados. O cliente deve poder consultar o resumo e os anexos antes de assinar.

A apresentação deverá distinguir a solicitação original do cliente dos relatos e conclusões do técnico. Os valores devem ser discriminados e relacionados à visita ou à tarefa correspondente, evitando apenas um total sem composição.

### 11.4 Versionamento, integridade e novo aceite

- Cada resumo apresentado deve gerar uma versão identificável e imutável, com hash do conteúdo e referências/versões dos anexos.
- A assinatura fica vinculada à versão exata apresentada.
- Alterações relevantes após a assinatura — por exemplo, serviços, valores ou relatos técnicos — exigem nova versão e novo aceite.
- Alterações administrativas sem impacto no conteúdo aceito não exigem nova assinatura.
- Versões anteriores, anexos, hashes, assinaturas e eventos de apresentação/aceite permanecem preservados conforme a ADR-0016.
- O sistema deve registrar separadamente apresentação, ciência, aceite e consentimento, pois são eventos juridicamente distintos. Não presumir que todo aceite equivale a consentimento LGPD.

### 11.5 Registro do aceite, cópia e sincronização

Registrar, quando aplicável:
- cliente e signatário;
- visita e versão do resumo;
- método de identificação;
- modalidade remota/presencial e dispositivo/canal;
- instante de emissão/apresentação e instante da assinatura;
- evidência da assinatura e hash do conteúdo aceito;
- técnico responsável pela coleta presencial;
- latitude e longitude quando disponíveis e autorizadas;
- estado de sincronização e validação do backend;
- envio da cópia ao cliente e respectivo resultado.

Uma cópia deve ser enviada ao cliente após confirmação do aceite; se a coleta ocorrer offline, o envio fica aguardando sincronização/conectividade. O backend confirma o aceite somente depois de validar a integridade e registrar a evidência sincronizada.

## 12. Confronto com o schema Supabase consultado em 2026-11-08

**Projeto:** Winservice — `voaovwphncwbrwtcqnsc`  
**Schema:** `public`  
**Método:** inspeção somente leitura de tabelas, colunas e foreign keys. Nenhuma alteração foi executada.

### 12.1 Estruturas existentes relevantes

| Tabela | Evidência observada | Avaliação frente ao modelo |
|---|---|---|
| `customers` | Cadastro, status, empresa, created_by e updated_by | Base cadastral existente; verificar regras e consistência multiempresa |
| `customer_contacts` | Contatos ligados a cliente, empresa e autoria | Estrutura básica presente |
| `phonenumbers` | Telefone ligado a cliente | Existe, mas não possui created_by/updated_by |
| `addresses` | Endereço, coordenadas, cliente, empresa e autoria | Base presente; integridade empresa-cliente precisa de reforço |
| `equipments` | Dados do equipamento e customer_identifier | Cadastro existente; não possui created_by/updated_by |
| `work_orders` | identifier, status e customer_identifier | Base muito enxuta; não possui created_by/updated_by |
| `visits` | OS, cliente, status, endereço, coordenadas, responsável, agendamento, check-in e checkout | Base de visita existente; não possui relatos, tarefas ou trilha completa de eventos no schema observado |
| `work_order_equipment` | Associação entre OS e equipamento | Não substitui tarefa: não identifica atividade por visita, tipo, relato ou evento de execução |
| `task_types` | name, code e metadados de autoria | Catálogo básico de tipos existe, sem configuração de obrigatoriedades observada |
| `visit_types` | name, description, estimated_time e code | Catálogo de tipos de visita, distinto de tipos de tarefa |
| `visit_requirements` e `visit_type_requirement` | Requisitos associados a tipos de visita | Estrutura voltada a requisitos de visita; não comprova requisitos dinâmicos por tipo de tarefa |
| `quotations` | Orçamento relacionado a OS, cliente e opcionalmente visita | Existe; não há relação direta com tarefa observada |
| `approval_events` | Eventos de aprovação com versão, hash, decisão, origem e autoria | Estrutura útil; referencia orçamento, OS e cliente, não tarefa |
| `documents` | Metadados genéricos de arquivo e uploader | Não há vínculo direto com visita/tarefa observado na tabela |

### 12.2 Estruturas não encontradas na listagem atual

Não foram encontradas tabelas dedicadas com nomes `tasks`, `visit_tasks`, `task_reports`, `visit_reports`, `visit_events` ou `audit_events`.

Essa constatação significa apenas que não há tabelas com esses nomes na listagem consultada. Não exclui que algum comportamento esteja parcialmente representado por outra estrutura, JSON, serviço externo ou código não inspecionado.

### 12.3 Lacunas e inconsistências relevantes

1. **Entidade tarefa:** existe `task_types`, mas não foi encontrada uma entidade de tarefa vinculada à visita. O catálogo de tipos não registra atividades individuais.
2. **Requisitos por tipo de tarefa:** `task_types` possui somente nome e código além dos metadados; não foi observada estrutura de campos/obrigatoriedades por tipo.
3. **Relatos:** não foi encontrada estrutura dedicada para relatos gerais de visita e relatos de tarefa.
4. **Eventos operacionais:** não foi encontrada estrutura append-only para registrar transições, início, pausas, retomadas, checkout e revogação diretamente na visita.
5. **Eventos GPS:** `visits` possui um conjunto único de colunas de check-in/checkout, insuficiente por si só para preservar vários ciclos de pausa e retomada.
6. **Auditoria histórica:** campos de autoria/atualização não substituem um histórico append-only com valores anteriores e novos.
7. **Autoria inconsistente:** `work_orders`, `equipments`, `phonenumbers` e `quotations` não apresentam `created_by`/`updated_by` no schema consultado. `task_types` utiliza `last_updated_by`; `work_order_equipment` possui defaults `gen_random_uuid()` nos campos de autoria, que precisam de revisão antes de serem considerados confiáveis.
8. **Orçamento por tarefa:** `quotations` está relacionado à OS, cliente e visita; não foi observada referência direta a tarefa.
9. **Evidências:** `documents` guarda metadados de arquivos, mas não apresenta vínculo direto com tarefa ou visita.
10. **Associação de equipamento:** `work_order_equipment` relaciona OS e equipamento, mas não registra em qual tarefa e visita o equipamento foi efetivamente atendido.
11. **Integridade multiempresa:** a auditoria técnica de 07/11/2026 identificou ausência de garantias compostas em relações relevantes e policies RLS permissivas. O modelo operacional não deve ser implementado antes de alinhar autorização e integridade multiempresa.
12. **Trigger de endereço:** a auditoria de 07/11/2026 identificou incompatibilidade entre a função `set_visit_address_snapshot` e as colunas existentes em `addresses`; requer correção e teste antes de depender do fluxo de criação de visitas.

### 12.4 Conclusão técnica

O Supabase possui uma fundação cadastral e relacionamentos iniciais para clientes, equipamentos, OS, visitas, tipos de tarefa, orçamentos e aprovações. Entretanto, o modelo consultado não demonstra a implementação do núcleo funcional agora definido para tarefas por visita, relatos, eventos operacionais/GPS e histórico imutável de alterações.

A próxima etapa técnica deverá produzir um modelo lógico e uma matriz de conformidade detalhada antes de qualquer DDL ou migração. Não adicionar tabelas/colunas por inferência nem adaptar `work_order_equipment` para representar tarefas.

## 13. Pendências de especificação

1. Definir campos, relatos e evidências obrigatórios para cada tipo de tarefa.
2. Definir o esquema de tarefa e a distinção persistente entre equipamento não identificado e tarefa sem equipamento.
3. Definir frequência de amostragem, idade máxima operacional, retenção e controles de acesso ao histórico GPS.
4. Definir critérios de localização simulada/antifraude e tratamento de leituras inconsistentes.
5. Definir horário de jornada, fuso por empresa, dias úteis e exceções para confirmação de início fora da jornada; o fim da jornada não encerra a visita.
6. Definir taxonomia detalhada de motivos para prevented, interrupted e cancelamentos, além do tratamento quando o técnico não consegue registrar relato imediatamente.
7. Definir se logout voluntário, expiração de token e revogação administrativa terão o mesmo tratamento da revogação de autenticação.
8. Definir estrutura física de visit_events, índices, idempotência, ordenação, transação com a mudança de estado e projeções de duração.
9. Definir estrutura de auditoria geral, versionamento e snapshots históricos.
10. Definir relação entre tarefas, orçamentos, documentos, fotos e demais evidências.
11. Definir como a seleção da OS existente ou criação de nova OS será apresentada no fluxo do técnico.
12. Definir tratamento de falha/indisponibilidade do GPS e ações manuais excepcionais, sem permitir fraude ou perda de rastreabilidade.
13. Definir os parâmetros técnicos das automações GPS antes de sua implementação. O check-in automático inicial permanece fora do MVP.

## 14. Segurança e limites desta análise

- A inspeção do Supabase foi somente leitura.
- Nenhuma tabela, coluna, policy, função, trigger, dado ou configuração foi alterada.
- Não foram consultados registros pessoais de clientes.
- Não foram executados testes funcionais, de GPS, concorrência, RLS ou acesso cruzado.
- Não foram inspecionados workflows n8n, Chatwoot, aplicativo móvel ou backend externo.
- O inventário e o relatório anterior são fotografias técnicas datadas; esta seção usa a listagem de schema consultada em 2026-11-08.
- Requisitos funcionais confirmados não equivalem a implementação comprovada nem a uma ADR formal.

## Histórico de alterações

- 2026-11-08 — v0.8.0: aprovação da janela configurável inicial de 24 horas para reconciliação automática de eventos offline e revisão autorizada dos eventos mais antigos.

- 2026-11-08 — v0.7.0: inclusão da conferência do relógio do dispositivo ao receber nova visita, distinção temporal na sincronização offline e restrição de revisão de conflitos a gestores/atendentes/usuários autorizados.

- 2026-11-08 — v0.6.0: aprovação da visita como única máquina de estados operacional; remoção da Máquina B e das sessões independentes no MVP; consolidação dos oito estados, transições, revogação de autenticação, regra de jornada sem encerramento automático e histórico append-only unificado de eventos da visita.
- 2026-11-08 — v0.5.0: consolidação de requisitos obrigatórios/opcionais e pendências, avaliações históricas por tentativa, evidências com upload assíncrono/idempotente, coordenadas e timestamps, encerramento excepcional com requisitos obrigatórios pendentes e fluxo de aceite digital versionado com resumo, anexos e assinatura remota/presencial/offline.
- 2026-11-08 — v0.4.0: confirmação do check-in inicial de 5 minutos/50 m, precisão GPS configurável até 50 m, histórico de posições, gatilhos de finalização por nova tarefa/deslocamento/fim de jornada, relato de transição e aprovação do histórico append-only; incluída proposta de repositório de catálogos globais versionados.
- 2026-11-08 — v0.3.0: inclusão do vínculo opcional e exclusivo de orçamento e separação entre máquina de estados do módulo Chat e ciclo operacional da visita.
- 2026-11-08 — v0.2.0: atualização para execução/sessões no nível da visita, finalização da visita anterior mediante relato, limiares GPS de 200 m/50 m e catálogo global de tipos editável independentemente por empresa.
- 2026-11-08 — v0.1.0: criação do baseline funcional de Operação e confronto inicial com o schema Supabase consultado em tempo real, somente leitura.
