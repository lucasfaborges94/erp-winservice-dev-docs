# Especificação técnica proposta — MVP do SaaS de atendimento automatizado

**Estado:** PROPOSTA — pendente de revisão e aprovação explícita do responsável  
**Data:** 2026-11-14  
**Base documental:** ADR-0020 aprovada; ADR-0017 e ADR-0019; especificação de autenticação, contexto transacional e RLS  
**Branch:** `spec/atendimento-saas-mvp`  
**Escopo deste documento:** especificação documental. Não autoriza implementação, migrations, alterações Supabase/n8n/WhatsApp, provisionamento, deploy ou acesso a produção.

**Estado atual informado pelo responsável:** o ERP ainda não possui servidor MCP. Para o MVP proposto, os contratos REST da API NestJS são o caminho inicial. MCP é uma possível camada futura, opcional e sujeita a decisão/implementação próprias; exemplos de perfis MCP nesta especificação são arquiteturais, não descrevem componente existente nem pré-requisito atual.

## 1. Objetivo e resultado esperado

Especificar um produto SaaS de atendimento automatizado, independente na experiência e contratação, com a Winservice como primeiro tenant. A solução deve receber conversas por WhatsApp, identificar ou cadastrar o cliente no contexto autorizado, qualificar a solicitação, responder somente com regras configuradas pela empresa e transferir a conversa para atendimento humano quando necessário.

A direção arquitetural aprovada na ADR-0020 é reutilizar a API NestJS e o Supabase/PostgreSQL compartilhados com o ERP. O n8n orquestra integrações; regras, autorização, persistência e operações de negócio continuam sob autoridade da API. Este documento não altera a prioridade do MVP operacional do ERP nem os limites da ADR-0019.

### 1.1 Premissas documentadas

- Produto de atendimento contratável sem exigir os módulos operacionais do ERP.
- Configurações comerciais, canal, mensagens e regras pertencem ao tenant.
- Base de conhecimento/documentos são opcionais; ausência de dado essencial resulta em pergunta de esclarecimento ou transferência humana, sem invenção.
- O MVP compreende recepção, identificação, qualificação, consulta de regras configuradas, histórico/status e transferência humana.
- Agendamento automático e orçamento completo ficam fora do MVP.
- As restrições da ADR-0012 à Isa se aplicam: acesso apenas ao cliente do atendimento, sem exclusão e com alteração limitada aos registros criados pelo agente, até que uma especificação aprovada refine os limites.

### 1.2 Decisões pendentes e conflitos a resolver

A lista abaixo identifica o que ainda está pendente e o que já foi confirmado pelo responsável durante a revisão. As confirmações não aprovam a especificação inteira.

1. **Prioridade e fronteira de produto:** a decisão de alto nível já está registrada na ADR-0020: o atendimento terá plano de entrega próprio, preservando a prioridade de Operação do ERP e sem tornar WhatsApp/n8n parte automática do MVP operacional. Fica pendente detalhar a fronteira técnica de releases, módulos e operação na API compartilhada, sem reabrir a decisão já aprovada.
2. **Canal/provedor WhatsApp:** o responsável aprovou um contrato/adaptador independente de provedor. A escolha entre Meta WhatsApp Cloud API e provedor intermediário permanece pendente; definir titularidade/número por tenant, templates, janela de atendimento, custos e ambiente de homologação.
3. **Inbox humana:** decisão confirmada: Chatwoot transitório, já disponível segundo informação do responsável, conforme ADR-0007. Permanecem pendentes os detalhes do contrato de integração e a autoridade para status/mensagens durante a transferência.
4. **Identidade de cliente:** decisão confirmada: não associar automaticamente um cliente existente apenas pelo telefone. Pedir confirmação quando houver ambiguidade e encaminhar ao humano quando a identidade não puder ser confirmada; regras para números compartilhados e prevenção de duplicidade devem seguir esse princípio.
5. **Modelo SaaS:** decidir onboarding self-service ou assistido, aprovação/verificação de empresas, planos, limites, cobrança, suspensão, suporte e retenção. Billing não integra o MVP funcional salvo aprovação específica.
6. **Modelo de configuração:** decidir quem publica/aprova regras comerciais, versionamento, validade temporal, auditoria e rollback de uma configuração por tenant.
7. **Autenticação máquina a máquina:** princípio aprovado: n8n terá identidade de serviço própria perante a API do ERP, distinta de identidade humana. Permanecem pendentes o mecanismo/issuer da credencial, escopos por operação/tenant, segregação por ambiente, validade, rotação e revogação; não usar credencial humana compartilhada nem `service_role` como caminho normal.
8. **Retenção e privacidade:** decidir prazos para mensagens, transcrições, eventos e dados de contato; política de exclusão/anonimização e atendimento de solicitações do titular.
9. **Catálogo de equipamentos/regiões:** decidir quais dados são globais de plataforma e quais são privados/configuráveis por tenant.
10. **Disponibilidade e operação:** definir SLOs, limites de envio, janelas de suporte, alertas e política de fallback quando API, n8n ou provedor falharem.

### 1.3. Revisão de consistência e recomendações para deliberação

Esta revisão compara a proposta com as ADRs vigentes. Os itens desta seção são recomendações para o responsável; não alteram o estado de aprovação.

- **Fronteira do MVP:** considerar decidida em alto nível pela ADR-0020 (produto/roadmap próprios do atendimento; prioridade Operação do ERP preservada). Antes de implementar, detalhar como a API compartilhada isola módulos, permissões, releases e dependências externas, respeitando o limite da ADR-0019 para o MVP ERP.
- **WhatsApp:** contrato/adaptador independente de provedor aprovado pelo responsável. Selecionar Meta direto ou BSP somente após confirmar número/titularidade, homologação, operação, custos e exigências de cada tenant.
- **Handoff humano:** Chatwoot transitório aprovado pelo responsável e informado como disponível. Manter contrato de integração isolado para não acoplar o domínio de conversas ao inbox.
- **Vinculação do cliente:** política aprovada pelo responsável: telefone sozinho não confirma identidade; pedir confirmação nos casos ambíguos e encaminhar ao humano se não for possível confirmar.
- **Identidade n8n:** identidade de serviço própria perante a API aprovada como princípio pelo responsável. Credencial de escopo mínimo; tenant derivado/validado pela API e pelo canal cadastrado. Não reutilizar token de usuário humano, chave OpenAI ou credencial de banco. O mecanismo de emissão/validação, expiração, rotação, revogação e propagação segura do ator até RLS permanece pendente.
- **Regras comerciais:** publicar versões imutáveis aprovadas por usuário autorizado, com vigência e trilha; ausência, expiração ou conflito de regra deve resultar em pergunta ou handoff.
- **Configuração inicial:** manter catálogo e regras no escopo do tenant até existir decisão e contrato para dados globais. Isso reduz dependência de catálogo compartilhado no piloto.
- **Retenção e piloto:** definir retenção, acesso, anonimização e rotina de solicitação do titular antes de trafegar conversas reais; não usar ausência dessa decisão para bloquear protótipo com dados sintéticos.
- **Billing, planos e auto-onboarding:** manter fora do escopo inicial; resolver antes de comercialização multi-tenant, sem bloquear especificação do fluxo assistido para o piloto Winservice.

**Pontos ainda pendentes antes de fechar os contratos de integração:** fornecedor/canal específico, detalhes de integração do Chatwoot e detalhes da identidade máquina-a-máquina (emissor/formato da credencial, escopos e vínculo tenant). A política de confirmação de identidade e o princípio de identidade técnica estão decididos. Retenção e privacidade precisam estar definidas antes de piloto com dados reais. Billing e SLO comercial podem ficar para a etapa de comercialização.

### Identidade técnica do agente — princípio aprovado; credencial pendente

Proposta de desenho para deliberação: separar autenticação humana de autenticação máquina-a-máquina.

- Usuários humanos do console usam seus próprios JWTs do Supabase Auth e são autorizados por identidade, vínculo, empresa ativa e permissões.
- n8n é um serviço não humano que chama a API REST NestJS. O responsável aprovou o princípio de identidade de serviço própria para esse acesso. A credencial deve ter audiência da API, escopos mínimos, expiração, rotação, revogação e tenant/canal vinculados no servidor. Ela autentica o serviço/agente; não concede acesso direto ao banco.
- Não usar access token de uma pessoa da equipe como credencial permanente do n8n. O JWT Supabase representa uma identidade de usuário; tratá-lo como máquina pode confundir trilha de auditoria, associação Auth→usuário ERP e autorização humana. Uma conta Auth dedicada ao agente só deve ser considerada se for explicitamente modelada como identidade não humana e houver regras próprias de vínculo, escopo, ciclo de vida e RLS.
- Não confundir token de usuário com a chave secreta do projeto Supabase. A secret key / service_role é chave de aplicação com acesso elevado e bypass de RLS, não uma identidade de agente; não deve ser distribuída ao n8n neste desenho.
- A API precisa aplicar o escopo do serviço e a autorização por tenant antes de abrir a transação de banco; o acesso ao PostgreSQL continua sujeito à role de runtime sem BYPASSRLS e ao contexto transaction-local definidos nos gates existentes.

Portanto, não usar token de usuário humano do Supabase Auth para o n8n. A identidade técnica separada foi aprovada como princípio; a escolha concreta entre token opaco/API key rotacionável, JWT de serviço ou provedor OAuth máquina-a-máquina continua pendente de revisão de segurança. A chave de serviço da OpenAI mostrada pelo responsável autentica o n8n perante a OpenAI, não perante a API do ERP.

Referência técnica consultada em 2026-11-14: [Supabase API keys](https://supabase.com/docs/guides/getting-started/api-keys) distingue a chave de aplicação da identidade Auth do usuário e informa que a chave secreta mapeia à role service_role com bypass RLS; [Supabase Auth OAuth 2.1](https://supabase.com/docs/guides/auth/oauth-server) descreve fluxo Authorization Code com PKCE, autenticação e consentimento do usuário, não devendo ser presumido como client credentials para execução unattended.

### 1.4. Deliberações do responsável — 2026-11-14

O responsável confirmou expressamente as seguintes direções para esta proposta:

- O adaptador/contrato de WhatsApp deve ser independente do fornecedor; a escolha do provedor concreto continua em aberto.
- O handoff humano usará Chatwoot de forma transitória. O responsável informou que já está disponível; essa disponibilidade é informação do responsável, não uma verificação de ambiente feita nesta sessão.
- Um número de telefone sozinho não autoriza associação automática a um cadastro existente. Quando a identidade estiver ambígua, solicitar confirmação ou transferir ao humano.

O princípio de identidade de serviço do n8n perante a API REST do ERP foi aprovado pelo responsável. O token/credencial não será um token de usuário humano do Supabase Auth nem uma chave da API OpenAI; seu emissor, formato, escopos, vínculo tenant e ciclo de vida continuam pendentes. O responsável também concordou com workflows separados por perfil para o piloto, cada um usando identidade/credencial própria; um roteador eventual apenas encaminha e não detém permissões amplas. A especificação inteira continua em estado PROPOSTA até aprovação explícita posterior.

## 2. Escopo funcional

### 2.1 Incluído no MVP

- Receber evento/mensagem de WhatsApp e associá-lo ao tenant pelo canal previamente cadastrado, validado e ativo.
- Validar assinatura/autenticidade do webhook do provedor e deduplicar eventos.
- Abrir ou continuar conversa com estado explícito e trilha de auditoria.
- Identificar cliente por telefone como sinal de busca; quando ambíguo ou insuficiente, pedir confirmação ou encaminhar a humano.
- Consultar/criar cadastro mínimo de cliente somente por operação autorizada da API.
- Coletar, de forma progressiva, equipamento/categoria, marca/modelo quando conhecidos, defeito/solicitação, região e informações de contato necessárias.
- Consultar configurações e regras comerciais ativas daquele tenant por ferramenta/API limitada.
- Informar ao cliente quando a configuração não cobre sua pergunta; não inventar preço, cobertura, prazo, garantia ou disponibilidade.
- Permitir solicitação e execução de transferência humana; pausar automação conforme estado de handoff.
- Registrar mensagens e transições, com proveniência (cliente, agente, humano, sistema), horário, correlação e tenant.
- Permitir consulta operacional de fila/status e retomada humana pela integração transitória com Chatwoot; definir sincronização, fonte autoritativa e comportamento durante indisponibilidade.
- Encerrar/reabrir atendimento segundo política documentada e conservar vínculo com o histórico.

### 2.2 Fora do MVP

- Agendamento automático de visita ou técnico.
- Orçamento completo, negociação, cobrança ou pagamento.
- Criação/alteração autônoma de ordem de serviço, visita ou tarefa.
- Escrita direta do agente ou workflow no banco.
- Atendimento por outros canais, voz ou disparos em massa.
- Busca semântica/vetorial de documentos, salvo decisão específica posterior; conhecimento opcional pode começar em campos estruturados.
- Provisionamento self-service, billing e catálogo comercial de planos.
- Alterações ao MVP operacional do ERP, aos workflows existentes ou ao ambiente de produção.

## 3. Requisitos funcionais e critérios de aceite

| ID | Requisito | Critério verificável |
|---|---|---|
| RF-01 | Provisionar tenant e habilitar produto Atendimento de forma independente | Tenant sem módulo ERP Operação consegue configurar o Atendimento; tenant sem produto ativo recebe negação |
| RF-02 | Cadastrar canal de WhatsApp por tenant | Canal inativo, inválido ou não mapeado não abre atendimento; segredo nunca retorna em leitura |
| RF-03 | Ingerir evento autenticado de forma idempotente | Reenvio do mesmo ID do provedor não duplica mensagem, transição nem resposta |
| RF-04 | Criar/continuar conversa com isolamento de tenant | Toda leitura e escrita valida empresa e produto; tentativa de trocar tenant por payload/ID é negada |
| RF-05 | Qualificar solicitação progressivamente | Campos desconhecidos permanecem nulos/desconhecidos; a conversa pede apenas dados necessários |
| RF-06 | Usar configurações comerciais da própria empresa | Resposta cita apenas uma regra ativa do tenant; ausência/expiração resulta em esclarecimento ou handoff |
| RF-07 | Restringir ações do agente | Sem exclusão; consulta limitada ao cliente/conversa; gravação feita por operações de API com escopo e autoria verificáveis |
| RF-08 | Transferir para humano | Handoff registra motivo, fila/destino e estado; automação para ou limita respostas conforme decisão humana |
| RF-09 | Manter histórico auditável | Mensagens e transições possuem origem, correlação, timestamps e referência tenant-scoped; correção não apaga trilha |
| RF-10 | Recuperar de falhas externas | Retry limitado e idempotente; falha persistente gera estado observável e fila de revisão humana |
| RF-11 | Onboarding sem documentação interna | Empresa completa configuração guiada mínima e passa por validação/publicação; documentos são opcionais |
| RF-12 | Atender solicitação fora da cobertura | Sistema informa limite e oferece transferência, sem prometer serviço ou inventar resposta |

## 4. Tenancy, identidade e autorização

1. Estratégia proposta alinhada à ADR-0017: banco/schema compartilhado e isolamento lógico por `company_identifier` nas entidades tenant-scoped.
2. A API é a autoridade para validar JWT Supabase, usuário ERP, vínculo ativo, empresa ativa, papéis/permissões e produto habilitado. O identificador de empresa enviado pelo cliente, modelo de IA, n8n ou webhook nunca prova autorização.
3. Usuários com um vínculo ativo podem usar a seleção automática prevista na ADR-0017; múltiplos vínculos exigem seleção explícita; nenhum vínculo resulta em negação. Toda troca de empresa invalida contexto anterior.
4. O contexto para PostgreSQL/RLS deve seguir a especificação técnica aprovada: conexão/role dedicada sem BYPASSRLS, contexto transacional local, limpo entre requisições, backend e RLS como barreiras complementares, sem `service_role` no fluxo normal.
5. Toda consulta por identificador deve incluir escopo de tenant e, quando aplicável, conversa/cliente autorizado. Constraints relacionais devem impedir referências cruzadas.
6. n8n recebe credencial técnica de escopo mínimo, identificável e revogável; a API deriva e valida o tenant a partir da credencial/canal cadastrado. Não aceitar `company_identifier` arbitrário como contexto suficiente.
7. Webhooks públicos são autenticados pelo mecanismo do provedor e mapeados a um canal previamente registrado. Payload não escolhe tenant, workflow, credencial nem ferramenta.
8. Prompts não são mecanismo de autorização. Cada ferramenta do agente aplica autorização no servidor, valida schema e lista de operações permitidas, registra autoria e rejeita campos não permitidos.
9. Dados globais de plataforma, dados do tenant, dados pessoais, segredos e telemetria devem ser classificados separadamente. Configurações e credenciais são tenant-scoped; segredos são criptografados/geridos fora do Git e nunca expostos em logs ou DTOs.
10. Cache, memória conversacional, índices, logs, fila de retry e métricas devem incluir escopo tenant e controles contra consulta cruzada. Conteúdo pessoal deve ser minimizado e mascarado nos logs.
11. Aplicar rate limit por canal/tenant, limites de concorrência e proteção contra replay, abuso e custo inesperado.
12. Toda mutação deve gerar auditoria com ator (humano/agente/sistema), tenant, operação, entidade, correlação e resultado, sem armazenar segredos ou conteúdo desnecessário.

### 4.1. Múltiplos agentes, identidades de serviço e workflows — proposta (REST inicial; MCP futuro)

Os conceitos abaixo são distintos; o número de workflows não define, por si só, o número de identidades.

- **Agente:** papel lógico com objetivo, limites de autonomia e conjunto de capacidades; deve aparecer como ator identificável na auditoria.
- **Identidade de serviço:** principal técnico apresentado pelo n8n à API REST. Deve mapear a um perfil de autorização explícito, com escopo mínimo e tenant permitido. Para o piloto, a proposta confirmada pelo responsável é usar identidade/credencial própria por perfil de capacidade, separando os perfis com privilégios diferentes. Se um servidor MCP for aprovado futuramente, sua identidade/token também deverá ser delimitada ao recurso MCP.
- **Workflow:** implementação/orquestração no n8n. Um workflow de propósito fixo usa credencial correspondente ao perfil do agente. Vários workflows podem compartilhar uma identidade somente se tiverem a mesma fronteira de confiança e exatamente o mesmo conjunto de permissões; separar credenciais quando os privilégios diferirem.
- **API REST (caminho inicial):** valida credencial, validade e escopos e autoriza cada operação no servidor. Se um servidor MCP for criado no futuro, ele também deverá validar credencial/audiência/escopos e encaminhar ao backend apenas contexto de ator verificado. A disponibilidade de ferramenta no prompt ou workflow nunca concede permissão.

**Matriz inicial de perfis baseada no exemplo do responsável — PROPOSTA, pendente de revisão:**

| Perfil proposto | Leituras autorizadas | Escritas autorizadas | Restrições |
|---|---|---|---|
| `atendimento_qualificador_rw` | Contexto da conversa atual; regras publicadas do próprio tenant; cadastro do cliente somente quando já estiver vinculado/confirmado na conversa | Criar qualificação e handoff da conversa atual; criar cadastro mínimo do cliente quando necessário e permitido pela regra funcional | Sem buscas globais, leitura de outra conversa/tenant, alteração de registros alheios, exclusão ou acesso genérico ao ERP |
| `atendimento_registrador_write` | Nenhuma ferramenta de leitura ampla | Criar qualificação e handoff apenas para a conversa cujo contexto foi validado pelo servidor; não criar cadastro de cliente | Não recebe consultas/listagens; não escolhe tenant/cliente/conversa por ID do modelo; não pode criar cadastro de cliente, alterar registros ou excluir |

- O perfil com leitura/escrita recebe apenas os dados mínimos da conversa e cliente atual; a busca inicial por telefone, quando necessária, deve ser uma operação específica, tenant-scoped e sem listar cadastros.
- O perfil write-only não consulta o banco por ferramentas. O workflow pode encaminhar um contexto mínimo da mensagem recebida; a API valida canal, tenant, conversa e permissões antes de gravar. A criação de cadastro mínimo de cliente fica desabilitada nesse perfil no piloto inicial, conforme deliberação do responsável; qualquer inclusão futura exige nova decisão e revisão da especificação.
- Se a gravação depender de estado atual, a API deve encapsular a validação e a escrita numa operação autorizada; não ampliar o perfil com uma permissão genérica de leitura.
- Exemplos de escopos simbólicos: `conversation.current.read`, `service_profile.published.read`, `customer.current.read`, `qualification.current.create`, `handoff.current.create`, `customer.minimal.create`. Nomes e granularidade são ilustrativos, não contratos implementados.
- Negar por padrão qualquer ação não listada: alteração/remoção de cliente, ordens/visitas, leitura de conversas antigas, busca entre clientes ou troca de tenant.
- Cada chamada recebe tenant/canal da credencial ou de contexto confiável validado pela API; IDs ou `agent_id` enviados pelo modelo/payload nunca concedem autorização.

A matriz representa capacidades de negócio, não uma implementação de MCP. O caminho atual proposto é REST; se MCP for criado futuramente, cada ferramenta deve mapear para as mesmas operações e verificações no servidor.

“Somente escrita” deve ser definido por operações concretas, não por um privilégio amplo `write`. Se uma operação precisar consultar contexto para validar a gravação, conceder apenas a leitura mínima daquele contexto ou fazer a validação dentro da própria operação autorizada. IDs fornecidos pelo modelo não bastam para selecionar tenant/cliente/conversa.

**Estratégia n8n acordada para a proposta do piloto:** separar workflows por perfil de agente e usar uma identidade/credencial própria em cada workflow. Um roteador pode encaminhar a mensagem, mas não terá credencial ampla; o subworkflow do perfil de destino fará a chamada com sua credencial própria. A seleção de perfil deve vir de configuração confiável e ser validada no servidor. Nunca confiar em `agent_id` vindo de mensagem, modelo ou payload não autenticado; não dar ao roteador uma credencial global com a união de todas as permissões.

**Separação de tokens se MCP vier a ser adotado:** o token MCP deve ter audiência daquele servidor; não deve ser encaminhado como credencial para uma API upstream. O MCP usaria credencial/contexto próprio do backend ou mecanismo de delegação aprovado, preservando o ator para auditoria. No MVP REST atual, n8n chama a API diretamente com a identidade de serviço definida para esse caminho.

**Operação n8n:** credenciais pertencem à configuração de segurança do n8n, não são identidade garantida pelo nome do workflow. Rever quem pode editar/compartilhar workflows e usar credenciais, pois editores de workflows compartilhados podem usar credenciais presentes nos nós conforme a documentação do n8n.

Referências normativas/técnicas: [ADR-0017](decisoes/ADR-0017-tenancy-compartilhada-autorizacao-multiempresa.md) exige identidade técnica e menor privilégio; [especificação de autorização MCP](https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization) aplica-se caso um servidor MCP seja futuramente adotado; [documentação n8n sobre compartilhamento](https://docs.n8n.io/workflows/sharing/) descreve acesso de editores às credenciais usadas nos workflows compartilhados.

Esta seção é uma proposta de desenho e não aprova o mecanismo de credenciais, perfis finais, ferramentas MCP nem qualquer alteração de workflow.
## 5. Onboarding proposto

Estado de onboarding por tenant: `draft → validating → ready_for_review → active`; falha retorna a `draft` ou `needs_attention`. A ativação exige revisão conforme política que o responsável ainda deve decidir.

1. Criar empresa/tenant ou associar empresa existente pela rotina autorizada da plataforma.
2. Habilitar o produto Atendimento separadamente dos módulos operacionais; confirmar plano/limites quando decisão comercial estiver definida.
3. Cadastrar nome público, horários, regiões atendidas, categorias de equipamento, marcas e tipos de solicitação.
4. Configurar regras de cobertura e atendimento (o que atende, exceções, horários, informações que podem ser informadas e quando transferir).
5. Configurar mensagens de abertura, consentimento/informação do canal, encerramento e transferência.
6. Conectar canal WhatsApp em modo de homologação; validar propriedade, webhook, assinatura e envio/recebimento de teste.
7. Definir fila/contato humano, horário de atendimento, contingência e procedimento de takeover.
8. Executar checklist de completude, contradição e conteúdo proibido; testar perguntas sem resposta, ambíguas e fora da região.
9. Revisar e publicar versão imutável da configuração, com autor, aprovador, timestamp e vigência. Reversão publica versão anterior; não reescreve histórico.
10. Ativar somente após gates técnicos e operacionais aplicáveis; manter botão/ação de pausa por tenant e canal.

**Configuração mínima para ativar:** canal verificado; horário/área de atendimento definidos; rota humana testada; regras comerciais mínimas publicadas ou política explícita de “perguntar/transferir”; avisos de privacidade revisados; limites e responsável operacional definidos.

## 6. Modelo de dados lógico proposto (não é schema aprovado)

Reutilizar entidades existentes somente após reconciliar os nomes e semântica no modelo vigente/código/banco. Os nomes abaixo são sugestões para discussão; não afirmar que existem nem criar migration nesta etapa.

| Entidade proposta | Campos/relacionamentos relevantes | Observações |
|---|---|---|
| `company_products` | `identifier`, `company_identifier`, `product_code`, `status`, período de vigência | Habilitação independente do ERP; compatibilizar com catálogo/módulos existente |
| `service_channels` | tenant, tipo, provedor, referência externa, status, segredo referenciado, configuração não secreta | Segredo armazenado em cofre/secret manager; unicidade de canal/tenant |
| `service_config_versions` | tenant, número/versão, estado, configuração validada, autor/aprovador, vigência | Publicações imutáveis; versão ativa única por escopo |
| `service_rules` | tenant, tipo, conteúdo estruturado, validade, versão, estado | Preferir regras determinísticas a texto livre para cobertura, região e condições |
| `service_conversations` | tenant, canal, contato/cliente opcional, estado, atribuição, timestamps, chave externa | Índices compostos tenant+identifier; não presumir cliente identificado |
| `service_messages` | tenant, conversa, direção, origem, ID externo, conteúdo minimizado, timestamps, status | Deduplicação por tenant/canal/ID externo; política de retenção pendente |
| `service_qualifications` | tenant, conversa, cliente, equipamento/categoria, marca/modelo, problema, região, completude | Campos parciais e proveniência; sem preencher dado desconhecido por inferência |
| `service_handoffs` | tenant, conversa, motivo, fila/usuário, estado, solicitado/em/assumido/concluído | Registrar takeover; humano pode suspender automação |
| `integration_events` | tenant, canal, ID/idempotency key, tipo, estado, tentativas, próximo retry, correlação | Payload bruto com retenção mínima e proteção; evitar duplicar domínio de eventos existente |
| `audit_events` | tenant, ator, ação, objeto, correlation ID, resultado, timestamp | Preferir mecanismo de auditoria vigente e append-only, conforme ADR-0016 |

### 6.1 Integridade e índices propostos

- Todas as tabelas tenant-scoped carregam `company_identifier` diretamente ou são protegidas por relação composta até uma raiz tenant-scoped.
- Chaves estrangeiras tenant-scoped incluem tenant quando aplicável, impedindo associação de conversa/mensagem/configuração de empresas diferentes.
- Unicidade: `(company_identifier, channel_identifier, provider_event_id)` para deduplicação de eventos; `(company_identifier, conversation_identifier, sequence)` se sequência for adotada.
- Índices de fila por tenant/status/updated_at; busca de contatos por tenant e telefone normalizado com política de unicidade ainda pendente.
- Segredos não são colunas de texto retornáveis; armazenar referência a cofre ou forma criptografada com chave fora do banco conforme decisão de plataforma.
- Definir retenção, particionamento/volume e estratégia de remoção/anonimização antes de persistir grande volume de mensagens.
- Não criar DDL/migration até reconciliar inventário, baseline restaurável, gates e aprovação operacional.

## 7. Máquina de estados sugerida

Conversa: `open → qualifying → awaiting_human → assigned → in_progress → resolved → closed`; permitir `reopened` mediante evento auditado. Erros de integração devem ficar como estado operacional do evento, não substituir silenciosamente o estado da conversa.

- `open`: mensagem recebida; evento autenticado e deduplicado.
- `qualifying`: agente pode coletar campos permitidos e responder com configuração publicada.
- `awaiting_human`: transferência solicitada; agente não inicia novas respostas de negócio.
- `assigned/in_progress`: humano assume; política de coexistência deve ser decidida.
- `resolved/closed`: encerramento explícito, com política de reabertura/retensão ainda pendente.

Transições devem ser validadas no servidor. Mensagens repetidas, fora de ordem ou atrasadas não podem reabrir ou encerrar conversa sem regra explícita.

## 8. Contratos REST propostos

Prefixo `/api/v1`. JSON UTF-8; identificadores opacos; timestamps ISO-8601 UTC; paginação por cursor. Os contratos finais devem seguir convenções do projeto e ser revisados antes de implementação.

### 8.1 Usuário autenticado / console de configuração

- `GET /api/v1/companies/{companyId}/service-profile` — ler perfil/configuração publicada; valida vínculo, empresa ativa e produto.
- `PUT /api/v1/companies/{companyId}/service-profile/draft` — salvar rascunho de configuração; autorização administrativa, validação de campos e versão concorrente.
- `POST /api/v1/companies/{companyId}/service-profile/validate` — retornar erros/avisos estruturados; sem ativar/publicar.
- `POST /api/v1/companies/{companyId}/service-profile/publish` — publicar versão após permissão/aprovação; idempotência e trilha de auditoria.
- `GET /api/v1/companies/{companyId}/service-channels` — metadados sem segredo.
- `POST /api/v1/companies/{companyId}/service-channels` — iniciar conexão/verificação do canal.
- `POST /api/v1/companies/{companyId}/service-channels/{channelId}/pause` e `.../resume` — controle operacional com autorização e auditoria.
- `GET /api/v1/companies/{companyId}/service-conversations?status=&cursor=` — lista paginada tenant-scoped.
- `GET /api/v1/companies/{companyId}/service-conversations/{conversationId}` — resumo e estado; conteúdo limitado por permissão.
- `POST /api/v1/companies/{companyId}/service-conversations/{conversationId}/handoff` — solicitar/registrar transferência.
- `POST /api/v1/companies/{companyId}/service-conversations/{conversationId}/messages` — mensagem humana, se o inbox escolhido delegar envio à API.

O parâmetro `companyId` apenas seleciona contexto solicitado; o servidor revalida vínculo/empresa ativa conforme ADR-0017. A API não deve revelar se um identificador de outro tenant existe; responder 404/negação conforme política de erros uniforme.

### 8.2 Integrações técnicas

- `POST /api/v1/integrations/whatsapp/{provider}/webhook` — validar autenticidade, resolver canal, tenant e evento; limitar tamanho e formato do payload.
- `POST /api/v1/internal/service-events` — entrada autenticada de eventos n8n apenas se a identidade técnica e o caso de uso forem aprovados; tenant derivado da credencial/canal, idempotency key obrigatória.
- `POST /api/v1/internal/service-conversations/{conversationId}/actions` — executar ações enumeradas e autorizadas, nunca SQL ou chamadas arbitrárias; propriedade da conversa revalidada no servidor.
- `GET /api/v1/integrations/whatsapp/{provider}/webhook` — verificação inicial de webhook quando exigida pelo provedor; validação de token sem expor segredo.

Autenticação de serviço, assinatura, replay protection, rotação, rate limiting, allowlist de ações, timeout e auditoria são requisitos bloqueantes. Não colocar token de tenant em URL.

### 8.3 Envelope de resposta e erros

Sucesso retorna DTO explícito e mínimo. Erros seguem envelope estável, por exemplo:
```json
{
  "error": {
    "code": "SERVICE_RULE_NOT_CONFIGURED",
    "message": "Não há uma regra publicada para esta solicitação.",
    "correlationId": "opaque-id",
    "details": []
  }
}
```

Códigos propostos: `VALIDATION_ERROR`, `UNAUTHENTICATED`, `FORBIDDEN`, `NOT_FOUND`, `PRODUCT_NOT_ENABLED`, `CHANNEL_NOT_ACTIVE`, `IDEMPOTENCY_CONFLICT`, `SERVICE_RULE_NOT_CONFIGURED`, `HANDOFF_REQUIRED`, `RATE_LIMITED`, `INTEGRATION_UNAVAILABLE`. Não devolver stack trace, dados de outro tenant, segredos ou prompts internos.

### 8.4 Idempotência, ordenação e concorrência

- Eventos de provedor e mutações de publicação/envio usam chave idempotente com escopo tenant/canal e janela de retenção definida.
- Persistir estado do evento antes de efeitos externos; distinguir recebido, validado, processando, concluído e falho.
- Resposta ao provedor ocorre conforme seu contrato de timeout; processamento longo deve ser assíncrono apenas após escolher infraestrutura aprovada.
- Retry usa backoff limitado, jitter e dead-letter/revisão humana; não duplicar mensagem enviada.
- Controle otimista de versão para configuração e transições; mensagens fora de ordem são armazenadas e ordenadas com base em timestamp/ID do provedor sem confiar no relógio do cliente.

## 9. Integração n8n e WhatsApp proposta

1. O provedor entrega webhook à API ou a uma entrada técnica controlada. A API valida assinatura, canal ativo, tenant, tamanho, timestamp/replay e idempotência antes de aceitar o evento.
2. A API registra evento e conversa com estado consistente. n8n recebe apenas um evento normalizado e tenant-scoped, via credencial de serviço limitada.
3. n8n coordena etapas declarativas: normalizar mensagem, chamar ferramentas permitidas da API, encaminhar texto ao modelo, validar resposta contra regras e enviar mensagem pelo conector autorizado.
4. O modelo nunca acessa banco, Supabase, credenciais, funções arbitrárias ou ferramentas genéricas. Toda operação de leitura/escrita passa pela API e valida contexto no servidor.
5. Configuração publicada e regras comerciais são consultadas por API com versão explícita. Instruções e conteúdo de cliente são dados não confiáveis; prompt injection não amplia escopo.
6. Saída do modelo passa por validação de formato, conteúdo permitido e política de “sem fonte, sem afirmação”. A API/provedor controla envio, limites, idempotência e auditoria.
7. Handoff pausa o ciclo automatizado e abre/atualiza tarefa no inbox humano escolhido. Retomada requer transição autorizada e evento registrado.
8. Indisponibilidade da API, n8n, modelo ou provedor resulta em retry controlado/alerta e alternativa de atendimento humano; não perde nem duplica silenciosamente o evento.
9. Workflows são exportáveis/versionados, mas não armazenam credenciais ou dados pessoais reais. Mudanças de workflow exigem revisão, testes em ambiente não produtivo e autorização operacional antes da ativação.

## 10. Segurança, privacidade e observabilidade

- JWT de usuário validado pelo backend; autorização por vínculo, papel/permissão, empresa ativa e módulo/produto.
- Identidade de máquina separada de usuário humano; credenciais de menor privilégio, secret manager, segregação por ambiente e rotação/revogação.
- RLS e backend complementares; sem acesso direto do agente/workflow ao banco e sem `service_role` como caminho comum.
- Webhook: validação criptográfica conforme provedor, timestamps/replay, limitação de payload, rate limit e idempotência.
- Segredos fora de Git/exports/logs; conteúdo e dados pessoais minimizados, mascarados em telemetria e sujeitos a retenção aprovada.
- Auditoria append-only para publicação de regras, mudanças de canal, handoff, ações de agente e acessos administrativos.
- Observabilidade com correlation ID ponta a ponta; métricas agregadas e tenant-safe: eventos aceitos/duplicados, falha/retry, latência, handoffs, taxa de resposta sem regra e indisponibilidade por dependência. Não registrar conteúdo integral por padrão.
- Limites de autonomia da ADR-0012 aplicados no backend; negação segura em identidade ambígua, configuração ausente, tenant desconhecido ou falha de autorização.
- Threat model antes da implementação: cross-tenant IDOR, prompt injection, replay, spoofing, exfiltração em logs, credencial comprometida, duplicidade/ordenação e negação de serviço/custo.

## 11. Plano de testes proposto

Nenhum teste foi executado como parte desta especificação. Antes de implementação/ativação, preparar evidências automatizadas com dados sintéticos e ambientes isolados.

### 11.1 Funcionais

- Onboarding mínimo completo, incompleto, inválido e publicação/rollback de versão.
- Recepção de mensagem, identificação inequívoca, cliente novo, telefone compartilhado, ambiguidade e dados faltantes.
- Qualificação progressiva, regra atendida, regra ausente/expirada, região fora da cobertura e solicitação não suportada.
- Handoff solicitado pelo cliente, por regra, por falha e por ambiguidade; takeover e retomada conforme política.
- Pausa/retomada de canal, encerramento, reabertura e mensagens atrasadas/fora de ordem.

### 11.2 Tenancy e autorização negativas

- Tenant A não lista/lê/altera conversa, cliente, canal, mensagem, regra, configuração, fila ou evento de B nem por ID conhecido.
- Usuário sem vínculo, vínculo suspenso, produto inativo, papel insuficiente e tenant arbitrário são negados.
- Troca concorrente de empresa no pool não carrega contexto da requisição anterior; rollback/erro limpa contexto transacional.
- Workflow/credencial de A não executa ação em B; agente não consulta outro cliente ou altera registro alheio.
- RLS, constraints e API rejeitam relacionamentos cruzados; acesso com contexto ausente é negado.
- Segredo, token, conteúdo pessoal excessivo e dados de outro tenant não aparecem em resposta/log/erro.

### 11.3 Integração e resiliência

- Assinatura inválida, replay, timestamp expirado, payload excessivo e provider desconhecido.
- Eventos duplicados, concorrentes, fora de ordem e reentrega após timeout; efeito único.
- API/n8n/modelo/provedor indisponível, timeout, resposta inválida, retry esgotado, fila de revisão e recuperação.
- Publicação concorrente de configuração, conflito de versão e idempotência de envio.
- Limites de taxa, burst, custos máximos e backpressure.
- Contrato REST: validação de DTO, erros, paginação, idempotência, autorização e compatibilidade versionada.

### 11.4 Qualidade de IA

- Sem regra/documento: pergunta ou transfere, sem inventar preço, prazo, cobertura ou garantia.
- Tentativas de prompt injection e solicitação de dados de outras empresas não alteram ferramentas nem escopo.
- Resposta verificável contra versão de configuração usada, com comportamento previsível em conteúdo contraditório.
- Testes conversacionais com exemplos sintéticos, avaliação de regressão e revisão humana; registrar versão de modelo/prompt/configuração sem guardar dados pessoais desnecessários.

## 12. Gates de segurança, dados e entrega

Estado documental consultado na continuidade mais recente disponível (2026-11-09): G0 parcial; G1 parcial; G2–G5 não iniciados. Revalidar antes de qualquer execução futura.

| Gate | Evidência exigida | Regra de passagem |
|---|---|---|
| G0 — Baseline e recuperação | backup restaurável, baseline reconciliada, pool documentado e restauração ensaiada fora de produção | Revisão humana do resultado; inventário estrutural sozinho não basta |
| G1 — Identidade | validação JWT ponta a ponta, mapeamento Auth→ERP e provisionamento testados | Tokens inválidos/identidades não mapeadas são negados |
| G2 — Conexão/contexto | role de runtime sem BYPASSRLS e contexto transaction-local isolado em pool | Testes concorrentes/rollback sem resíduo |
| G3 — Tenancy/RLS | classificação por tabela, policies/constraints e testes cross-tenant negativos | Acesso cruzado negado na API e no banco |
| G4 — Autorização/autonomia | papéis/permissões, produto ativo, ações permitidas e limites do agente | Negação testada para ação, tenant e cliente fora do escopo |
| G5 — Implementação da API | contratos versionados, revisão de segurança, pipeline e testes integrados | Só então considerar endpoints de negócio em ambiente isolado |
| G6 — Integrações | provedor, webhook, máquina-a-máquina, idempotência, secrets, retry e monitoramento validados em homologação | Aprovação técnica/operacional e ativação por tenant |
| G7 — Piloto Winservice | canal de teste, regras publicadas, handoff, suporte, pausa e métricas acordadas | Autorização operacional específica antes de produção |
| G8 — Comercialização | critérios de suporte, limites, billing, privacidade e onboarding multi-tenant aprovados | Revisão do responsável antes de abrir a novos tenants |

A conclusão desta especificação não fecha gates nem libera acesso a banco/produção. Aprovação do documento, se concedida, também não autoriza execução operacional sem autorização específica.

## 13. Critério de aceite deste documento

- Todos os requisitos de escopo solicitados estão descritos.
- Decisões pendentes e conflito de roadmap estão destacados.
- Modelo de dados e endpoints estão marcados como propostas, sem afirmar existência física.
- Requisitos de tenancy, segurança, testes e gates são rastreáveis às ADRs vigentes.
- Documento revisado e aprovado explicitamente pelo responsável antes de ser tratado como especificação normativa.
- Nenhum conteúdo desta branch muda estado de ADR, roadmap, banco, workflows, código ou produção.

## 14. Referências

- [ADR-0020 — SaaS de atendimento com núcleo compartilhado](decisoes/ADR-0020-atendimento-saas-backend-compartilhado.md)
- [ADR-0017 — Tenancy compartilhada e autorização multiempresa](decisoes/ADR-0017-tenancy-compartilhada-autorizacao-multiempresa.md)
- [ADR-0019 — Arquitetura da API interna](decisoes/ADR-0019-arquitetura-api-interna.md)
- [ADR-0012 — Limites de autonomia da Isa](decisoes/ADR-0012-autonomia-isa-operacoes-permitidas.md)
- [ADR-0016 — Histórico append-only](decisoes/ADR-0016-historico-append-only.md)
- [ADR-0007 — Chatwoot transitório](decisoes/ADR-0007-chatwoot-transitorio-chat-proprio.md)
- [Especificação técnica de autenticação, contexto e RLS](ESPECIFICACAO_TECNICA_AUTENTICACAO_CONTEXTO_RLS.md)
- [Fonte da verdade e protocolo de trabalho](../00_FONTE_DA_VERDADE.md)
- [Continuidade e transferência](08_CONTINUIDADE_E_TRANSFERENCIA.md)
