# Modelo de autorização e isolamento multiempresa — ERP Winservice

**Documento:** Modelo de autorização e isolamento multiempresa  
**Versão:** 0.3.0  
**Data:** 2026-11-08  
**Estado:** PROPOSTA PARA DISCUSSÃO — inclui diretrizes e sugestões recentes ainda em avaliação; não substitui ADRs aprovadas nem autoriza implementação  
**Projeto Supabase consultado:** Winservice (`voaovwphncwbrwtcqnsc`)  
**Schema consultado:** `public`  
**Base técnica:** estrutura e policies observadas em 2026-11-07; consultar novamente antes de implementar.

## 1. Objetivo

Definir o modelo conceitual e os requisitos de autorização que deverão orientar a identidade, o vínculo de usuários com empresas, o catálogo de permissões, a autorização de operações, o isolamento dos dados, as APIs, as políticas RLS e a atuação da Isa.

Este documento é uma proposta de especificação. Não altera ADRs aprovadas nem autoriza alterações no banco.

## 2. Autoridade e decisões relacionadas

Este modelo deve respeitar:

- **ADR-0001:** produto preparado para SaaS multiempresa, inicialmente utilizado pela Winservice.
- **ADR-0002:** Supabase como fonte central de verdade.
- **ADR-0011:** catálogo padrão global versionado; cada empresa mantém catálogo próprio e escolhe quando aderir a novas versões; personalizações devem ser preservadas, rastreadas e restauráveis.
- **ADR-0012:** a Isa consulta somente informações do cliente identificado no atendimento atual; pode criar clientes, equipamentos, ordens de serviço e visitas; não pode excluir; só pode alterar registros criados por ela; enforcement no backend/API e autorização, não apenas em prompt ou interface.

A estratégia de tenancy foi aprovada pela ADR-0017: banco e schema compartilhados, com isolamento lógico por empresa, vínculo multiempresa, seleção da empresa ativa na sessão e RLS/backend como barreiras complementares. A matriz definitiva de papéis e permissões está sendo consolidada neste documento, sem alterar a ADR-0017.

## 3. Evidências do estado atual consultado

A inspeção foi somente leitura. Nenhuma alteração foi feita no Supabase.

### 3.1 Estruturas relevantes encontradas

Foram identificadas, entre outras, as seguintes tabelas e campos:

| Estrutura | Evidência observada | Observação |
|---|---|---|
| `companies` | `identifier`, `name`, `is_active` | Cadastro de empresas |
| `users` | `identifier`, `auth_user_identifier`, `role`, `status` | Há uma distinção estrutural entre identificador interno e identificador de Auth |
| `company_users` | `company_identifier`, `user_identifier`, `role_identifier`, `company_admin` | Associação usuário–empresa |
| `company_user_roles` | `company_user_identifier`, `role_identifier` | Associação de múltiplos papéis ao vínculo |
| `roles` | `identifier`, `company_identifier` | Papel pode estar associado a uma empresa |
| `permissions` | `company_identifier`, `entity`, `field`, `action` | Permissões atuais são associadas a uma empresa |
| `role_permissions` | `company_identifier`, `role_identifier`, `permission_identifier` | Associação entre papel e permissão |
| `user_permissions` | `company_identifier`, `company_user_identifier`, `permission_identifier`, `allow` | Há estrutura para exceções por usuário; semântica completa ainda precisa ser definida |
| `modules`, `company_modules`, `role_modules`, `user_modules` | Catálogo e associações de módulos | Não comprovam, isoladamente, autorização sobre registros |
| `screens`, `role_screens`, `user_screens` | Catálogo e associações de telas | Controle de navegação não substitui autorização de dados |

Também foram observadas policies RLS permissivas em tabelas como `companies`, `customers`, `addresses`, `customer_contacts` e `visits`, com condições `true` para operações de usuários autenticados. A auditoria técnica de 2026-11-07 detalha os achados e suas limitações.

### 3.2 Pontos que exigem reconciliação

- `users` contém `auth_user_identifier`, mas a policy observada em `users` compara `auth.uid()` com `users.identifier`.
- A policy observada em `company_users` compara `user_identifier` com `auth.uid()`, enquanto a FK de `company_users.user_identifier` referencia `users.identifier`.
- A tabela `users` tem FK de `auth_user_identifier` para uma entidade externa ao schema `public`; a relação exata com `auth.users` e o fluxo de provisionamento precisam ser confirmados.
- `permissions.company_identifier` é obrigatório no esquema observado, o que não demonstra, por si só, um catálogo global independente como exige a ADR-0011.
- As FKs individuais não comprovam que todos os relacionamentos entre entidades pertencem à mesma empresa.
- O campo `company_admin` e os campos de papel em `company_users` coexistem com a tabela `company_user_roles`; precedência e semântica não estão documentadas.

Esses pontos são observações estruturais. Não se conclui, sem análise adicional, que sejam exploráveis ou que o fluxo de autenticação esteja necessariamente quebrado.

## 4. Vocabulário do modelo

- **Identidade autenticada:** identidade validada pelo provedor de autenticação, representada no contexto PostgreSQL por `auth.uid()`.
- **Usuário ERP:** perfil interno que representa uma pessoa ou identidade que utiliza o ERP.
- **Vínculo usuário–empresa:** associação ativa que autoriza o usuário a atuar em determinada empresa.
- **Empresa de contexto:** empresa em cujo escopo uma operação está sendo solicitada.
- **Papel (role):** conjunto nomeado de permissões atribuídas a um vínculo usuário–empresa.
- **Permissão:** autorização granular para executar uma ação sobre uma entidade e, quando aplicável, campo ou recurso.
- **Recurso tenant-scoped:** dado pertencente a uma empresa e isolado pelo identificador dessa empresa.
- **Recurso global:** dado mantido pela plataforma e compartilhado entre empresas segundo regras explícitas.
- **Autoria:** identidade do usuário ou agente autônomo que criou um registro, distinta do ator que o atualizou posteriormente.
- **Usuário humano (`human`):** usuário ERP operado diretamente por uma pessoa.
- **Usuário agente (`agent`):** usuário ERP cuja execução é realizada por um agente de IA, com identidade, função, configuração e permissões próprias.
- **`company_admin`:** designação de responsabilidade pela contratação do SaaS e pela relação administrativa/comercial da conta da empresa; não é, por si só, um papel de autorização nem concede privilégios no nível da plataforma.
- **`superadmin`:** papel de autorização atribuído a usuário humano de uma empresa, com todas as permissões disponíveis no âmbito daquela empresa, respeitados os limites globais da plataforma.
- **Agente autônomo:** configuração operacional e de IA associada a um usuário autônomo; inclui nome de exibição, perfil, função, comportamento, conhecimento autorizado e limites de atuação.

## 5. Princípios propostos

Os princípios abaixo são propostos para aprovação e detalhamento:

1. **Negação por padrão:** ausência de uma autorização positiva resulta em acesso negado.
2. **Autenticação não é autorização:** estar em `authenticated` não concede acesso a uma empresa ou registro.
3. **Vínculo ativo obrigatório:** operações tenant-scoped exigem vínculo ativo do usuário autenticado com a empresa de contexto.
4. **Contexto validado no servidor:** o identificador da empresa enviado pelo cliente é apenas uma solicitação de contexto; nunca prova autorização.
5. **Isolamento em profundidade:** APIs/backend validam o contexto e o banco aplica RLS e constraints adequadas.
6. **Menor privilégio:** conceder apenas as ações e os dados necessários.
7. **Separação de responsabilidades:** permissões de interface, módulos e telas não substituem autorização no backend/banco.
8. **Integridade entre tenants:** relacionamentos entre registros tenant-scoped devem impedir associação entre empresas diferentes.
9. **Auditoria:** operações relevantes registram ator, empresa de contexto, ação, recurso e resultado, sem expor segredos.
10. **Sem bypass implícito:** funções privilegiadas e serviços internos devem ter finalidade, autenticação, autorização e escopo documentados.
11. **Consistência entre canais:** web, app móvel, n8n, Chatwoot, APIs e Isa devem obedecer ao mesmo modelo de autorização, com identidades técnicas próprias quando necessário.
12. **Catálogo versionado:** a evolução das permissões deve preservar a decisão da ADR-0011 e não sobrescrever customizações silenciosamente.

## 6. Fluxo de autorização proposto

Para uma operação de usuário, a sequência lógica proposta é:

1. Validar a sessão e obter a identidade autenticada confiável.
2. Resolver o usuário ERP associado à identidade autenticada.
3. Receber a empresa solicitada como contexto, sem confiar nela como autorização.
4. Confirmar que a empresa existe e está ativa.
5. Confirmar que o usuário possui vínculo ativo com essa empresa.
6. Resolver os papéis e exceções de permissão aplicáveis àquele vínculo.
7. Verificar a permissão exigida para a ação e o recurso.
8. Aplicar restrições adicionais de escopo, autoria, estado e relacionamento.
9. Executar a operação sob RLS e constraints coerentes com o mesmo contexto.
10. Registrar a operação auditável quando aplicável.

Qualquer falha de identidade, vínculo, contexto, permissão ou validação de escopo deve resultar em negação. A API não deve aceitar do cliente claims de papel, permissões, autoria ou empresa como fatos confiáveis sem validação no servidor.

## 7. Modelo conceitual de identidade e vínculo

### 7.1 Identidade e perfil ERP

A identidade do provedor de autenticação deve ser vinculada de forma inequívoca a um perfil ERP. A chave de autenticação e o identificador interno do usuário não devem ser confundidos.

**Proposta:** usar o identificador imutável do provedor como referência de autenticação e manter `users.identifier` como identificador interno do ERP, com relação explícita e única entre ambos. O campo `users.auth_user_identifier` parece destinado a esse vínculo, mas o fluxo real precisa ser confirmado antes de consolidar esta proposta.

### 7.2 Usuário em múltiplas empresas

O modelo deve permitir que uma identidade tenha vínculos independentes com uma ou mais empresas, cada qual com seus próprios papéis, permissões e estado de acesso.

A autorização em uma empresa não deve se propagar automaticamente a outra. Suspender ou remover um vínculo deve impedir novas operações nesse tenant sem exigir a exclusão do perfil global do usuário.

### 7.3 Empresa de contexto

A seleção da empresa ativa pode ocorrer na aplicação, mas sua validade deve ser confirmada em cada operação sensível no backend/banco. Não utilizar uma variável de sessão mutável pelo cliente como única prova de vínculo ou autorização.

A forma técnica de transportar e validar o contexto — JWT, parâmetro de API, contexto transacional ou outro mecanismo — permanece pendente de decisão.

## 8. Modelo conceitual de permissões

A autorização deve considerar, no mínimo:

- identidade autenticada;
- vínculo ativo com a empresa;
- papel ou conjunto de papéis atribuídos ao vínculo;
- permissões concedidas ao papel;
- exceções explícitas por usuário/vínculo, se aprovadas;
- módulo habilitado para a empresa, quando o recurso depender de módulo;
- restrições específicas do recurso, como autoria, cliente do atendimento, estado ou atribuição.

### 8.1 Catálogo global e customizações

A ADR-0011 exige distinguir:

1. catálogo padrão global mantido pela plataforma;
2. versões publicadas desse catálogo;
3. versão adotada por cada empresa;
4. personalizações locais da empresa;
5. histórico de alterações e possibilidade de restauração dos padrões.

A estrutura atual de `permissions` com `company_identifier` obrigatório não comprova que essa separação esteja implementada. A solução de dados deve ser especificada sem violar a preservação das personalizações.

### 8.2 Semântica de múltiplos papéis e exceções

A regra para usuários comuns é a agregação por união: uma permissão é concedida quando qualquer um dos papéis atribuídos ao vínculo a concede. A avaliação não depende da ordem de consulta dos papéis.

As exceções individuais são avaliadas por vínculo usuário–empresa e seguem estas regras:

- `user_permissions.allow = true`: concessão explícita da permissão ao usuário naquele vínculo.
- `user_permissions.allow = false`: negação explícita da permissão, prevalecendo sobre concessões provenientes de qualquer papel.
- Ausência de exceção individual: aplica-se a união das permissões concedidas pelos papéis.
- Ausência de concessão por papel ou exceção: acesso negado por padrão.

Para usuários com papel `superadmin`, a negação explícita individual (`allow = false`) não se aplica. O `superadmin` possui todas as permissões disponíveis no âmbito da própria empresa, sem depender de concessões individuais por papel. A concessão ampla não ultrapassa os limites de disponibilidade global dos módulos, ativação do módulo pela empresa, vínculo com a empresa, isolamento multiempresa ou restrições globais obrigatórias de segurança.

A regra de `superadmin` não concede acesso a controles exclusivos da plataforma SaaS nem a outra empresa. Agentes autônomos não podem receber ou exercer esse papel.

A forma física de armazenamento e a implementação da avaliação permanecem pendentes de especificação técnica.

## 9. Isolamento e integridade dos dados

Para cada entidade, a especificação deverá classificá-la como:

- global da plataforma;
- pertencente diretamente a uma empresa;
- pertencente indiretamente a uma empresa por relacionamento;
- dado de sistema/infraestrutura com acesso restrito.

Para entidades tenant-scoped, a política deve assegurar que:

- leitura só alcance registros da empresa autorizada;
- criação só permita associar registros à empresa de contexto validada;
- atualização não permita mover um registro para outra empresa;
- exclusão dependa de permissão específica e regras de retenção;
- relacionamentos não permitam conectar registros de empresas diferentes;
- consultas indiretas e funções privilegiadas preservem o mesmo isolamento.

Quando um registro não possuir `company_identifier` próprio, o caminho de herança de tenant deve ser explícito, íntegro e verificável. Se necessário, constraints compostas ou outro mecanismo equivalente deverão impedir inconsistências entre tenant e referências.

A decisão sobre quais tabelas devem carregar `company_identifier` diretamente não está tomada neste documento.

## 10. Usuários autônomos e agentes de IA

### 10.1 Conceito de usuário autônomo

O ERP distinguirá conceitualmente dois tipos de usuário: **humanos (`human`)** e **agentes (`agent`)**. Ambos são usuários do ERP, possuem identidade própria, podem ter vínculos com empresas e recebem papéis e permissões próprios. A distinção de tipo não substitui a autorização por empresa.

A Isa é concebida como um **usuário do tipo agente (`agent`) operado autonomamente por IA**, e não como um papel fixo do sistema, uma permissão global ou uma integração externa privilegiada.

O modelo deverá permitir que cada empresa cadastre e configure seus próprios usuários autônomos para funções específicas. A empresa poderá personalizar, no mínimo, o nome de exibição e o perfil operacional de cada agente. A configuração poderá contemplar função, comportamento, tom de comunicação, instruções, documentos/fontes de conhecimento autorizados e permissões compatíveis com a atividade.

A Isa é a primeira instância desse conceito na Winservice, configurada para atendimento. Outros agentes poderão ser criados para outras funções, sem que a identidade ou as permissões de um agente sejam herdadas automaticamente por outro.

**Importante:** “usuário autônomo” descreve o conceito funcional e de autorização. Ainda será necessário decidir se a implementação física reutilizará a tabela `users` com um tipo/modo de execução ou se terá uma entidade complementar ligada ao usuário ERP. Esta proposta não presume alteração de schema.

### 10.2 Identidade, vínculo e autorização

Cada usuário autônomo deve possuir identidade própria no contexto da empresa, vínculo explícito com a empresa proprietária e permissões limitadas à sua função. Agentes de IA nunca poderão receber, ocupar ou exercer o papel de `superadmin`, independentemente de sua função ou configuração. Não deve operar com a identidade pessoal do usuário que o cadastrou, nem herdar automaticamente os privilégios desse usuário.

A autorização de um agente deve considerar:

- empresa à qual pertence;
- estado ativo/suspenso do agente;
- função e configuração operacional;
- módulos contratados e habilitados para a empresa;
- permissões atribuídas ao próprio usuário autônomo;
- restrições específicas da atividade, do cliente, do atendimento e da autoria;
- limites globais de segurança definidos pela plataforma.

A empresa pode personalizar o perfil do agente, mas não pode ampliar permissões além dos módulos contratados, das capacidades disponibilizadas pela plataforma ou dos limites de segurança obrigatórios.

O papel `superadmin` permanece no âmbito da empresa: não concede acesso a outras empresas nem a módulos não contratados. Quando a empresa adquire/habilita um módulo, o acesso correspondente segue as regras de atribuição aprovadas para aquele módulo. Um agente autônomo também deve respeitar esse limite.

### 10.3 Limites específicos da Isa e da ADR-0012

A ADR-0012 define os limites atuais da Isa na Winservice:

- consulta somente informações do cliente identificado no atendimento atual;
- pode cadastrar clientes, equipamentos, ordens de serviço e visitas;
- não pode excluir registros;
- só pode alterar registros que ela própria criou;
- não pode executar operações fora do escopo autorizado.

Esses limites devem ser aplicados no backend/API e, quando aplicável, no banco. São limites da configuração/autorização da Isa e não devem ser convertidos automaticamente em regras universais e imutáveis para todos os futuros agentes. Cada agente terá escopo próprio, sempre subordinado aos limites globais da plataforma, aos módulos contratados e às permissões concedidas pela empresa.

O identificador de cliente ou atendimento fornecido em texto pelo modelo não constitui prova de autorização. O backend deve resolver e validar o contexto antes de permitir a consulta ou operação.

### 10.4 Auditoria e rastreabilidade

A trilha de auditoria deve permitir distinguir, no mínimo:

- empresa em cujo contexto a operação ocorreu;
- usuário humano que cadastrou ou configurou o agente, quando aplicável;
- usuário autônomo/agente que executou a operação;
- atendimento ou processo que originou a ação, quando aplicável;
- ação, recurso, resultado e instante da operação.

O usuário que configura um agente não se torna automaticamente o autor das operações realizadas pelo agente. A autoria operacional deve identificar o próprio usuário autônomo, mantendo separadamente a informação de quem o configurou.

### 10.5 Workflows e integrações técnicas

Workflows e integrações de infraestrutura, como n8n e conectores externos, não devem ser confundidos com usuários autônomos de negócio. Quando precisarem atuar em nome de um agente, devem preservar a identidade e o escopo desse agente; quando executarem tarefas técnicas próprias, devem usar identidade técnica distinta e privilégios mínimos.

Não reutilizar indiscriminadamente a identidade de um administrador ou a chave `service_role`. O uso de `service_role` ou de funções `SECURITY DEFINER` deve ser excepcional, restrito e justificado; essas vias não podem contornar silenciosamente as regras de negócio e de isolamento.

### 10.6 Governança de ativação dos módulos do ERP

A regra de ativação em dois níveis aplica-se **a todos os módulos do ERP**, e não somente ao Módulo de Inteligência Artificial. Cada módulo deverá possuir controles independentes de disponibilidade global pela plataforma e de ativação individual por empresa.

1. **Disponibilidade global pela plataforma:** somente os desenvolvedores responsáveis pelo ERP — inicialmente o responsável pelo projeto e, futuramente, membros autorizados de sua equipe — poderão ativar ou desativar globalmente a disponibilidade de cada módulo.
2. **Ativação individual por empresa:** o superadmin da própria empresa poderá ativar ou desativar cada módulo para aquela empresa.

Um módulo somente estará efetivamente disponível para uma empresa quando estiver disponibilizado globalmente pela plataforma **e** habilitado para aquela empresa. A ativação individual não poderá sobrepor uma indisponibilidade global.

A verificação da condição efetiva deverá ocorrer no backend/API ou no orquestrador responsável pela operação, e não apenas na interface.

**Desativação com drenagem (graceful drain):** quando qualquer um dos dois níveis for desativado, o sistema deverá fechar imediatamente a entrada de novas execuções para o módulo no escopo afetado. Nenhuma nova execução poderá ser aceita nem adicionada à fila enquanto o módulo permanecer inativo. As execuções que já tiverem sido aceitas — tanto as que estiverem em andamento quanto as que já estiverem aguardando processamento na fila — deverão continuar sendo processadas e terminar normalmente. A fila existente deverá ser drenada até que todas essas execuções sejam concluídas; a desativação, por si só, não deverá cancelar nem interromper abruptamente essas execuções.

Durante a drenagem, etapas internas necessárias para concluir uma execução já aceita poderão prosseguir, mas não poderão originar a admissão ou o enfileiramento de uma nova execução independente. O controle de entrada deverá ser aplicado atomicamente no backend/orquestrador para evitar que uma execução seja aceita em uma corrida com a desativação. O módulo permanecerá fechado para novas entradas mesmo depois de a fila esvaziar, até que esteja novamente ativo nos dois níveis.

A desativação não deverá apagar automaticamente dados, configurações, históricos ou registros anteriormente produzidos. A retomada da admissão dependerá de o módulo voltar a estar disponível globalmente e habilitado para a empresa, além das demais condições de autorização.

**Falhas durante a drenagem:** falhas temporárias permitem novas tentativas da mesma execução, respeitando limite de tentativas e política de backoff. Os valores concretos deverão ser definidos na especificação técnica. Falhas permanentes encerram a execução em estado terminal `FAILED`, com registro do motivo. Timeouts encerram a execução em estado terminal `TIMEOUT`, com registro do ocorrido. Retries preservam a identidade e o contexto da execução original e não constituem nova execução nem nova admissão independente. Para fins de drenagem, consideram-se terminais `COMPLETED`, `FAILED` e `TIMEOUT`. Não será permitido retry ilimitado. Os critérios para classificar falhas temporárias/permanentes, limites, backoff e timeouts deverão ser definidos antes da implementação.

Esta regra é transversal e deverá ser aplicada de forma consistente a todos os módulos, APIs, workflows, aplicações e canais de acesso. Um módulo não poderá contornar o controle por meio de chamadas diretas, funções privilegiadas ou automações técnicas.

### 10.7 Administração empresarial e ciclo de vida do superadmin

A designação `company_admin` e o papel `superadmin` representam conceitos distintos:

- **`company_admin`:** identifica a pessoa responsável pela contratação do SaaS e pela relação administrativa/comercial da conta da empresa. Essa designação não concede, por si só, privilégios de autorização no ERP nem acesso a controles exclusivos da plataforma SaaS.
- **`superadmin`:** papel de autorização de um usuário humano da empresa. Concede todas as permissões disponíveis no âmbito da própria empresa, incluindo as configurações que a plataforma permite que uma empresa individual administre. Não concede acesso administrativo ao SaaS, a outras empresas ou a módulos indisponíveis.

O primeiro usuário humano provisionado para a empresa será o responsável `company_admin` e receberá o papel `superadmin`. A designação de responsável comercial e o papel de autorização devem permanecer semanticamente distintos, ainda que sejam inicialmente atribuídos à mesma pessoa. Uma empresa poderá possuir mais de um `superadmin` humano.

Cada empresa deverá manter pelo menos um **superadmin humano ativo**. Agentes de IA nunca poderão receber, ocupar ou exercer esse papel, independentemente de sua função, perfil ou configuração.

O `superadmin` é imune a negações explícitas individuais de permissões no âmbito da empresa, mas continua sujeito à disponibilidade global dos módulos, à ativação de cada módulo pela empresa, ao isolamento multiempresa e às restrições globais obrigatórias de segurança.

O sistema deverá impedir a remoção, suspensão ou rebaixamento do último superadmin humano ativo, bem como qualquer outra operação que deixe a empresa sem esse responsável administrativo. A garantia sob operações concorrentes e o procedimento de recuperação de acesso administrativo ainda deverão ser especificados tecnicamente.

### 10.8 Regras específicas do Módulo de Inteligência Artificial

O ERP deverá possuir um **Módulo de Inteligência Artificial (IA)** próprio. Sua utilização estará subordinada à regra geral de ativação dos módulos descrita na seção 10.6.

A ativação do Módulo de IA não ativa automaticamente os agentes. Cada usuário autônomo/agente deverá possuir estado próprio e somente poderá executar operações quando, cumulativamente, o módulo estiver efetivamente ativo para a empresa, o agente estiver ativo e as permissões e demais condições de autorização forem satisfeitas.

A admissão de novas execuções de IA deverá ser bloqueada quando o módulo estiver indisponível globalmente ou desabilitado para a empresa. As execuções de IA já aceitas, inclusive as que estiverem aguardando na fila, deverão ser concluídas normalmente conforme a regra de drenagem da seção 10.6; nenhuma nova execução poderá ser admitida até a reativação do módulo nos dois níveis.

Provedores de modelos, workflows de execução, n8n e demais componentes de infraestrutura são serviços de suporte e não se confundem com o Módulo de IA nem com a identidade dos agentes.

## 11. Estratégia de tenancy aprovada

A estratégia de tenancy não é uma questão em aberto. A ADR-0017 está aprovada e determina que o ERP utilizará banco PostgreSQL compartilhado e schema compartilhado, com isolamento lógico por empresa nas entidades aplicáveis.

A autorização deverá respeitar cumulativamente o vínculo ativo do usuário com a empresa, a empresa selecionada como contexto da sessão, as permissões aplicáveis e a disponibilidade/ativação dos módulos envolvidos. RLS e backend/API serão barreiras complementares; constraints e relacionamentos deverão preservar a integridade entre empresas.

A ADR-0017 não significa que essa arquitetura já esteja implementada ou validada no banco atual. A auditoria estrutural identificou pontos que exigem correção e testes antes da liberação. A classificação das entidades globais e tenant-scoped, a revisão de RLS, as constraints e a implementação permanecem atividades técnicas pendentes.

As alternativas de schema por empresa e banco por empresa não são a arquitetura aprovada para a V1.

## 12. Matriz mínima de testes de autorização

Antes de produção, devem existir testes positivos e negativos para pelo menos os seguintes cenários:

| Cenário | Resultado esperado |
|---|---|
| Usuário sem sessão tenta acessar dado protegido | Negado |
| Usuário autenticado sem vínculo com a empresa tenta consultar dados | Negado |
| Usuário vinculado à empresa A consulta registro da empresa B | Negado |
| Usuário da empresa A cria registro informando empresa B | Negado |
| Usuário tenta atualizar registro para mudar seu tenant | Negado |
| Usuário tenta relacionar cliente da empresa A a endereço/OS/visita da empresa B | Negado |
| Usuário com papel sem permissão tenta executar ação | Negado |
| Usuário com permissão válida e vínculo ativo executa ação permitida | Permitido |
| Vínculo do usuário está suspenso | Novas operações negadas |
| Isa consulta cliente diferente do atendimento validado | Negado |
| Isa tenta excluir registro | Negado |
| Isa tenta alterar registro criado por outra identidade | Negado |
| Serviço técnico sem escopo tenta executar operação | Negado |
| Função privilegiada é chamada sem autenticação/autorização válida | Negado |

Os testes devem ser executados com identidades e empresas distintas em ambiente controlado, sem usar dados reais de clientes como fixtures.

## 13. Decisões funcionais consolidadas e questões técnicas pendentes

As deliberações funcionais sobre tenancy, vínculo multiempresa, seleção da empresa na sessão, múltiplos papéis, exceções individuais e escopo empresarial do `superadmin` foram consolidadas nas ADRs aplicáveis e nesta proposta. Permanecem pendentes os detalhes de implementação que não foram decididos:

1. **Contexto da empresa:** definir o mecanismo técnico para transportar e validar o contexto entre sessão, aplicação, backend e banco, conforme a ADR-0017.
2. **Identidade Auth ↔ ERP:** confirmar no fluxo real que `users.auth_user_identifier` implementa a relação canônica com `auth.users.id` e que `users.identifier` é o identificador interno do ERP.
3. **Tipo físico de usuário:** definir como `human` e `agent` serão representados no schema, sem presumir que a estrutura atual já suporte a distinção.
4. **Garantia do último superadmin:** especificar proteção transacional/concorrente para impedir remoção, suspensão ou rebaixamento do último superadmin humano ativo.
5. **Recuperação administrativa:** definir procedimento seguro de recuperação quando houver perda de acesso dos superadmins.
6. **Dados globais e tenant-scoped:** concluir a classificação de todas as entidades e catálogos.
7. **Identidades de infraestrutura:** especificar identidades e escopos próprios para n8n e demais integrações técnicas.
8. **Implementação da autorização:** definir avaliação backend/RLS, estrutura física das exceções e testes automatizados, preservando as regras funcionais consolidadas.

A forma física das tabelas, constraints, funções, policies e APIs não é aprovada por este documento.

## 14. Próximas atividades após aprovação

1. Resolver as decisões pendentes desta proposta.
2. Atualizar este documento para especificação aprovada.
3. Se houver decisão arquitetural nova, registrar ADR próprio antes de alterar o índice.
4. Fazer inventário de classificação tenant/global para todas as tabelas.
5. Desenhar matriz de autorização por entidade e ação.
6. Projetar RLS, constraints, funções e contratos de API.
7. Preparar migrations revisáveis e testes automatizados.
8. Aplicar mudanças somente após aprovação explícita e em ambiente apropriado.
9. Atualizar inventário, auditoria, status e continuidade com evidências reais.

## 15. Limites desta versão

- Documento em estado de proposta.
- Nenhuma decisão pendente foi considerada aprovada.
- Nenhuma alteração foi feita no banco Supabase.
- Nenhuma policy, função, trigger, grant ou constraint foi modificada.
- Não foram consultados dados pessoais de clientes.
- Não foram realizados testes de exploração nem testes de acesso cruzado.
- A análise se baseia em metadados e definições estruturais observadas em 2026-11-07; consultar novamente antes de implementar.


## 15. Novas diretrizes e propostas em avaliação — 2026-11-08

Esta seção registra contexto recente para continuidade da análise. Seu conteúdo não constitui decisão arquitetural aprovada, não altera ADRs vigentes e não autoriza implementação no Supabase.

### 15.1 Política de sessões por aplicação
- O aplicativo móvel técnico deverá permitir somente um login operacional simultâneo por usuário e empresa.
- O web app de atendimento poderá permitir múltiplas sessões simultâneas, sujeitas a limite configurável e tempo de expiração configurável.
- Um futuro aplicativo de chat será avaliado posteriormente; sua política de sessões ainda não foi definida.
- Recomendação em avaliação: separar sessão de autenticação e sessão/contexto operacional do aplicativo; aplicar exclusividade móvel no servidor sob controle transacional.
- Interpretação preliminar: exclusividade por usuário + empresa + aplicação, permitindo sessões em empresas diferentes. Confirmar antes da especificação física.
- Permanecem a definir: novo login invalida sessão anterior ou é rejeitado, identificação de dispositivo, tratamento offline, revogação, duração máxima e exceções administrativas.

### 15.2 Arquitetura extensível de catálogos
A solução deverá considerar outros catálogos personalizáveis além do catálogo de permissões, incluindo tipos de equipamentos, marcas de equipamentos e tipos de visitas com requisitos e configurações.
Recomendação em avaliação: infraestrutura conceitual compartilhada para versionamento, publicação, adoção por empresa, personalizações, histórico e restauração quando aplicáveis, mantendo modelos de domínio específicos. Não presumir uma única tabela genérica para todos os catálogos. A ADR-0011 permanece restrita ao catálogo de permissões; ampliar seu escopo exigirá análise e eventual decisão formal separada.

### 15.3 Titularidade da conta
Nomenclatura solicitada para avaliação: account_owner em substituição a company_owner/company_admin como conceito de titularidade da conta. Grafia recomendada: account_owner (não accont_owner).
Recomendação em avaliação: manter titularidade separada do papel superadmin e avaliar representação própria, sem transformá-la em role. Titular humano, titularidade única, transferência protegida e exigência de superadmin permanecem sujeitos a aprovação específica.

### 15.4 Papéis iniciais sugeridos
Papéis informados para avaliação: superadmin, gerente, supervisor, atendente, técnico, suporte e vendedor. A lista é preliminar e não define permissões, hierarquia, exclusividade, escopo por módulo, atribuição padrão ou se os papéis serão globais ou customizáveis por empresa. Recomenda-se identificadores técnicos em inglês e rótulos de apresentação em português.

### 15.5 Levantamento obrigatório antes da migração
Antes de qualquer migração de autorização, levantar em modo somente leitura:
- papéis existentes, identificadores, nomes, escopo global/empresarial e duplicidades;
- associações entre papéis e permissões;
- vínculos usuário–empresa ativos, inativos ou suspensos;
- valores e significado operacional de company_users.role_identifier e company_users.company_admin;
- titulares/administradores atuais e usuários com superadmin ou equivalentes;
- usuários sem vínculo, vínculos sem usuário correspondente e referências órfãs;
- sessões existentes e mecanismos atuais de autenticação;
- dependências em views, funções, triggers, policies RLS, APIs, workflows e aplicações.
O inventário estrutural do repositório não substitui a consulta dos dados reais. A fotografia atual dos registros ainda não foi levantada. A migração deverá preservar identificadores, vínculos, papéis, permissões e responsabilidades, com relatório de mapeamento, reconciliação, contagens antes/depois e plano de reversão. Nenhuma linha deve ser descartada ou reinterpretada silenciosamente.

### 15.6 Estado desta seção
Todos os itens acima são diretrizes, sugestões ou questões em avaliação. Não foram aprovados como especificação física, não alteram ADRs e não autorizam DDL, alterações de dados ou implantação.


## 16. Resultado da auditoria inicial dos dados atuais — 2026-11-08

Foi concluída auditoria somente leitura no Supabase do projeto documentado, schema public. A consolidação está em `documentacao/auditorias/AUDITORIA_DADOS_AUTORIZACAO_2026-11-08.md`.

Resumo: 1 empresa; 5 usuários (1 ativo, 4 inativos); 1 vínculo empresarial; 3 papéis globais (`financial`, `superuser`, `technician`); 2 atribuições via `company_user_roles`; 1 permissão; nenhuma linha em `role_permissions` ou `user_permissions`. O vínculo existente tem `company_admin=true), `company_users.role_identifier` apontando para `superuser`, enquanto `company_user_roles` atribui `financial` e `technician`. Os achados não autorizam conversão automática; o mapeamento precisa de aprovação. Nenhum DDL ou dado foi alterado.


## 17. Complemento da auditoria técnica — 2026-11-08

A auditoria identificou que `public.can_view_expense(uuid)` é `SECURITY DEFINER`, consulta `company_users.role_identifier` e é executável por `anon`, `authenticated` e `service_role`. Portanto, o campo legado tem dependência funcional real e não pode ser removido sem substituição e validação da função. A função e a permissão de execução de `anon` são achados para revisão de segurança, não corrigidos nesta etapa.

Nas sete tabelas de autorização examinadas, RLS está habilitado; policies foram encontradas somente em `users` e `company_users`. Não foram encontradas policies em `company_user_roles`, `roles`, `permissions`, `role_permissions` e `user_permissions`. Os cinco usuários têm `identifier = auth_user_identifier`; não foram encontrados triggers nas tabelas examinadas. Detalhes e limitações constam no relatório de auditoria. Nenhuma alteração foi feita no banco.


## 18. Complemento de segurança da auditoria — 2026-11-08

A auditoria de privilégios encontrou grants amplos para `anon`, `authenticated` e `service_role` nas sete tabelas de autorização examinadas. RLS é camada adicional, mas a combinação de grants amplos, ausência de policies em cinco tabelas e funções `SECURITY DEFINER` acessíveis exige revisão coordenada. Os Security Advisors retornaram 26 tabelas com RLS sem policies, 7 funções com `search_path` mutável, 2 funções `SECURITY DEFINER` executáveis por `anon`, 2 por `authenticated` e proteção contra senhas vazadas desabilitada. Views públicas examinadas não mostraram referências aos campos legados; a função `can_view_expense` permanece dependência confirmada de `company_users.role_identifier`. Nenhuma correção ou alteração de privilégios foi executada. Detalhes em `documentacao/auditorias/AUDITORIA_DADOS_AUTORIZACAO_2026-11-08.md`.

## Histórico de alterações

- 2026-11-08 — v0.3.3: registra grants SQL e achados dos Security Advisors identificados na auditoria.
- 2026-11-08 — v0.3.2: registra achados técnicos complementares da auditoria RLS e dependência da função can_view_expense no campo legado.
- 2026-11-08 — v0.3.1: registra referência à auditoria somente leitura dos papéis, vínculos, administradores e permissões atuais. Nenhuma migração autorizada.
- 2026-11-08 — v0.3.0: registra diretrizes em avaliação sobre sessões por aplicação, arquitetura extensível de catálogos, account_owner, papéis iniciais e levantamento de dados/dependências antes da migração. Não altera ADRs nem autoriza implementação.
- 2026-11-08 — v0.2.0: consolida a união das permissões de múltiplos papéis para usuários comuns; define concessões e negações individuais, com negação prevalecendo para usuários comuns; define a imunidade do `superadmin` a negações individuais dentro do escopo empresarial; distingue `company_admin` de `superadmin`; formaliza os tipos conceituais `human` e `agent`; registra que o primeiro usuário humano provisionado recebe a designação `company_admin` e o papel `superadmin`; corrige a referência à tenancy para refletir a ADR-0017 aprovada; explicita questões técnicas ainda pendentes. Nenhuma alteração no Supabase.
- 2026-11-07 — v0.1.6: define retry limitado para falhas temporárias, preservação da identidade da execução, estados terminais para falhas permanentes/timeouts e conclusão da drenagem por estados terminais.
- 2026-11-07 — v0.1.5: define desativação com drenagem: bloquear novas admissões e enfileiramentos, concluir normalmente todas as execuções já aceitas e reabrir a entrada somente após reativação nos dois níveis.
- 2026-11-07 — v0.1.4: generaliza a ativação em dois níveis para todos os módulos; define que o primeiro usuário da empresa será humano e superadmin; proíbe agentes de IA como superadmin; exige a manutenção de ao menos um superadmin humano ativo.
- 2026-11-07 — v0.1.3: define que desenvolvedores do ERP controlam a disponibilidade global, superadmin controla a ativação por empresa, cada empresa mantém ao menos um superadmin e toda execução de IA depende do módulo efetivamente ativo.
- 2026-11-07 — v0.1.2: registra o Módulo de Inteligência Artificial com disponibilidade global pela plataforma e ativação individual por empresa; define a conjunção dos controles e a independência do estado de cada agente.
- 2026-11-07 — v0.1.1: esclarece usuários autônomos como usuários ERP operados por IA, configuráveis por empresa; diferencia agentes de negócio de workflows técnicos; delimita a ADR-0012 à configuração da Isa e registra questões de implementação pendentes.
- 2026-11-07 — v0.1.0: criação da proposta inicial com base no Project Charter, ADR-0001, ADR-0011, ADR-0012 e inspeção somente leitura do schema e das policies do Supabase.
